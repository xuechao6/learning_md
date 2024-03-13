# px4固件学习：

## 1.硬件

**硬件**：原版固件一般分为两个助力器，分为stm32：px4FMU主处理器以及stm32：px4io协处理器

如何判断自己的pixhawk硬件是什么版本的呢？

打开QGC后，点击左上角图标，选择第一个齿轮后，左侧第二列的第二个固件选项，在这个界面中重新插拔飞控，可以显示出飞控的硬件型号，例如：

<img src="picture\image-20240229210151620.png" alt="image-20240229210151620" style="zoom:50%;" />

**处理流程**：主处理器上电后运行BootLoader程序，判断是否有新的烧录程序，如果有，通过地面站烧录程序到主处理器，再进入nuttx操作系统，进一步完成姿态控制之类的活动，其中有一个进程是px4io.cpp，会通过这个进程向协处理器烧录程序，不需要外部人员烧录

**传感器：**imu有两个，一个单独的imu传感器，一个imu和gps合成的传感器



## 2.固件文件夹作用

<img src="picture\image-20240228195057224.png"  >

在src文件夹中，以下是各个部分的作用：

<img src="picture\image-20240228195342962.png"  >



## 3.修改启动脚本

通过选择启动脚本，判断应该执行哪些代码，脚本所在位置为：PX4-Autopilot\ROMFS\px4fmu_common\init.d\rcS，这个总脚本会启动同目录下的其他脚本。应该是在qgc里选择机型，就会向rcs脚本里输入参数，进而选择第二级的启动脚本，启动不同机型的控制程序，例如多旋翼的启动脚本就是`rc.mc_apps和rc.mc_defaults`。**二次开发中可能需要修改**

<img src="picture\image-20240229210913704.png"  >

## 4.总的编译脚本（cmake）

作用：修改后的固件编译命令：`make px4_fmu-v5_default`之类命令，编译的依据，个人理解像是`cmakelists.txt`类型的文件

- 在v.8之前（cmake）：编译脚本的位置都在cmake下的config文件夹下：`cmake/configs/nuttx_px4fmu-v5_default.cmake`，后缀是default的cmake文件是主编译脚本，我感觉和cmakelists文件差不多，添加新的路径及可执行文件

<img src="picture\image-20240301172716628.png"  >

- 在v.9~v.12（cmake）：统一修改到了新的位置：`boards/px4/fmu-v5/default.cmake`，书写方式有稍微的变化，先写文件夹，再写相应的文件名称

  <img src="picture\image-20240301172603714.png"  >

  在v.12~现在（后缀px4board）：修改文件类型为：`boards/px4/fmu-v5/default.px4board`，书写方式有很大的变化，首先都需要大写。必须有`CONFIG_`前缀，后面跟着文件夹名字`EXAMPLE`，后面跟着`TEST`等文件名称，最后加上`=y`，新加的文件需要在上述路径的文件内，最后加上类似的一行

  <img src="picture\image-20240301173144087.png"  >



## 5.自定义消息类型与文件

1. **修改msg和总cmake：**，

   - 在msg文件夹下创建自定义的msg文件，必须在自定义消息中首先添加`uint64 timestamp`
   - 并且在msg文件夹下找到Cmakelists.txt文件，把自定义的文件注册到里面去

2. **编译生成自定义消息的头文件**：

   * `make px4_fmu-v5_default`编译以后，产生`/PX4_Firmware/build/px4_fmu-v5_default/uORB/topics/vehicle_acceleration.h`头文件

3. **源代码内容和cmakelists**：在src目录下的随便一个文件夹内（一般在modules或者example），创建一个发布者（订阅者）文件夹，内部存放自定义的发布者（订阅者）代码，并创建cmakelists.txt文件。

   **主文件编写**：

   - **头文件**：需要在发布者文件中包含头文件，make编译以后，产生在`/PX4_Firmware/build/px4_fmu-v5_default/uORB/topics/vehicle_acceleration.h`，包含的头文件形式例如`#include <uORB/topics/vehicle_acceleration.h>`
   - **函数名**：
     1. 主函数第一行要写`__EXPORT int first_test_main(int argc, char *argv[]);`, `__EXPORT`是导出让NUTTX找到这个函数，函数名后面一定要加`_main`，这样NUTTX才能找到，前面的函数名`first_test`是自己起的名，需要和Cmakelists文件中的MAIN相对应。
     2. 在mavlink控制台的终端中输入的`xxx start`也是和函数名对应的，上例中就是`first_test start`
   - **自定义消息的结构体**：在发布（订阅）者文件中`struct test_urod_s 实例化对象 `，就是自定义消息对应的结构体类型，一定要多加一个`_s`后缀（举例消息名为test_urod.msg，主文件中的结构体类型就是`struct test_urod_s`）`test_urod_s`实际上式系统自己生成的，只要在msg文件中存入`test_urod`的消息，就会自动生成这个结构体，

   **camkelist文件编写**：

   * `MODULE`是模块的固件唯一名称，填写的是文件的名称，需要和文件夹的路径相对应，参考其他的例子
   * `MAIN`是NUTTX模块的入口点，填写的是.c文件中主函数名称，不写后缀main，例如`MAIN first_test`

   **Kconfig文件编写**：

   * bool后面填写的是文件夹的名字，不是.c文件的名字，Enable support for后面也是一样

4. **修改总的编译脚本**：在总的编译脚本`board->px4->default.px4board`里，添加`CONFIG_文件路径_文件名称=y`的一行话，告诉编译器要编译这个文件

5. **（可选）上电自启动**，在ROMFS/rc.mc_default里添加 “`模块 start`” 这句话，就可以飞控上电自启动了

6. **编译烧录**：make px4_fmu-v5_default编译，make px4_fmu-v5_default upload固件烧录



## 6.px4固件烧录

1. 创建目录，创建c文件，改写cmakelists.txt文件，改写Kconfig文件，这几步就跟着指导手册走:[第一个应用教程（Hello Sky）| PX4 用户指南 (v1.13) --- First Application Tutorial (Hello Sky) | PX4 User Guide (v1.13)](https://docs.px4.io/v1.13/en/modules/hello_sky.html)

2. 编译构建的程序：先要在px4board文件中添加对应的Kconfig键，方法有两种：参考上面链接中**Build the Application/Firmware 构建应用程序/固件** 这部分

   - **方法一：**找到/PX4_Firmware/boards/px4/fmu-v5/default.px4board文件，这个fmu-v5是根据主板型号进行选择的，型号选择的地址可以在下图查看。**（详情见上一节）**在最后一行手动添加CONFIG_EXAMPLES_PX4_SIMPLE_APP=y，`CONFIG_`是一定要的，`EXAMPLES_`是文件夹的名称，`PX4_SIMPLE_APP`是文件的名称。新版改成了

   <img src="picture\image-20240229212041493.png"  >

   - **方法二**：或者进入px4终端，在终端输入`make px4_fmu-v5_default boardguiconfig`，中间的`-v5`应该改成自己的硬件版本，然后在gui界面中勾选上新加的文件

   <img src="picture\image-20240229212528249.png"  >

3. 进行编译，进入px4文件夹后，终端输入make px4_fmu-v5_default，fmu-v5需要改成自己对应的硬件

4. 上传到主板，有两种方式，QGC烧录和命令行烧录

   - 1. QGC烧录：

        打开QGC后，点击左上角图标，选择第一个齿轮后，左侧第二列的第二个固件选项，在这个界面中重新插拔飞控，弹出右侧的选项。把高级设置打钩，选择最底下的自定义文件，然后点确定
        
        <img src="picture\image-20240229203646493.png"  >

   - 在弹出的选项中选择固件，一般在px4目录下的build文件夹里，会生成刚才编译过的文件，进入文件夹后，选择底下后缀为`.px4`的文件，一般名称就是`px4_fmu_v5_default.px4`，耐心等待就可以完成烧录

   - <img src="picture\image-20240229203759794.png"  >

   - 2. 命令行烧录：我没试成功

   -  进入px4文件夹，终端输入`make px4_fmu-v5_default upload`，然后会出现`Loaded firmware for X,X, waiting for the bootloader...`的提示，之后重新插拔飞控，期间不用关闭终端，重新插上后，等待擦出和重写，会有进度条显示



## 5.QGC终端：

在左上角，点击第二个选项后，Mavlink控制台，输入help之类的命令即可

<img src="picture\image-20240229213741897.png"  >



# Mavlink：

任务一：研究modules文件夹下创建新的模块，如何启用自定义的文件，附带命令行烧录

任务二：mavlink自定义消息类型修改，查询资料

PX4-Autopilot\src\modules\mavlink\mavlink_main.cpp文件中的1400行开始，configure_stream_local函数是配置发送的数据频率，括号里面的数字表示一秒钟发送几次，越小表示发送越慢，越大表示发送越快，发送越快，对数据传输压力和载荷量较大，会造成发送距离较近的结果，平常使用时，可以删除一些不需要的消息包，增大发送距离。

心跳包heartbeat发送位置在2183行，不断发送更新的代码在2443行stream->update(t)

发送的数据类型存放在mavlink_messages.cpp中，248行相当于是一个链表，循环发送这个链表，具体在哪发送的我还没找到

所有mavlink发送的消息都是通过2421行mavlink_msg_command_long_send_struct函数发送的

自定义mavlink流程，第21节课程



写xml文件，借助第三方工具，生成对应的h头文件，在common.h中调用该头文件

写一个自己发送的消息类，模仿其他mavlink自带的数据类别，模仿的数据消息的类放在mavlink文件夹下的stream文件夹下

将类添加到待发送的链表中，链表的位置在mavlink文件夹下的mavlink_messages.cpp文件中250行

需要在mavlink.cpp中tset_main函数中用configure_stream将待发送的消息及其频率加入到`case MAVLINK_MODE_NORMAL`里面，在switch case括号外面写





地面端发送到天空端飞控后，飞控进行数据接收和解析 23节后半段

在编译过的mavlink文件夹下，看看有没有include、mavlink 版本 common

/PX4_Firmware/src/modules/mavlink/mavlink/message_definitions/v1.0

/PX4_Firmware/build/px4_fmu-v5_default/uORB/topics



