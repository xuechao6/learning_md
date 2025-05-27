# LINUX学习

## 1.cat命令

1. 显示文件内容：`cat file.txt`，将显示文件`file.txt`的内容。
2. 连接多个文件：`cat file1.txt file2.txt`，将连接文件`file1.txt`和`file2.txt`的内容，并按顺序显示。
3. 创建文件：`cat > file.txt`，在命令行中输入内容，并按Ctrl+D结束输入，将内容保存到`file.txt`文件中。
4. 追加内容到文件：`cat >> file.txt`，在命令行中输入内容，并按Ctrl+D结束输入，将内容追加到`file.txt`文件的末尾。
5. 复制文件：`cat source.txt > destination.txt`，将`source.txt`文件的内容复制到`destination.txt`文件中。
6. 显示行号：`cat -n file.txt`，显示文件`file.txt`的内容，并在每一行前面加上行号。
7. 显示非空行：`cat -s file.txt`，显示文件`file.txt`的内容，将连续的空行压缩成一行显示。



## 2.shell脚本

学习网站：[Shell 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/linux/linux-shell.html)

shell脚本对空格的要求非常严格

**1.变量**

**解释器**：第一行是解释器，类似规定这个脚本以什么方式运行`#!/bin/bash`

**引用**：变量引用需要加上`$变量`，或者是`${变量}`；还可以使用`$(hostname或ls catkin_ws等)`使用linux系统的命令引用参数

**赋值**：变量一般用大写子母表示；变量赋值一般等号左边和右边都不留空格，和编程不太一样

**删除变量**：`unset + 变量名称`注意变量名称前面不加`$`，只有具体数值传递时采用，删除后数据无法再使用

```bash
#!/bin/bash
echo "hello shell"
MY_NAME="XC" 
echo my name is ${MY_NAME}

cd catkin_ws
list=$(ls build)
echo $list

read -p "enter your name:" value_save
echo read test name is $value_save
unset value_save
echo "删除后的结果为：${value_save}"

declare -i my_int=42
# declare -A my_array
# my_array["name"]="xc"
# my_array["age"]=$my_int
echo "int类型的数据为：${my_int}"

echo ${PATH}
```



**2.传递参数**

获取参数的形式为`${n}`,**n** 代表一个数字，**1** 为执行脚本的第一个参数，**2** 为执行脚本的第二个参数

```bash
shell脚本命令
echo "shell传递参数"
echo "文件名称为${0}"
echo "第1个参数为${1}"
echo "第2个参数为${2}"
echo "第3个参数为${3}"

终端命令：./test_shell.sh 6 5 4
输出结果
shell传递参数
文件名称为./test_shell.sh
第1个参数为6
第2个参数为5
第3个参数为4
```



**3.流程**

**if函数**：

- 语法

- ```bash
  if condition          if condition
  then                  then
      command1               command1
      command2          elif 
      ...                    command1
  else                  else  
      command                command
  fi                    fi
  ```

- 判断语句格式：`[ ... ]`和`(( ... ))`，第一种的大于`-gt`，小于用`-lt`，第二种可以直接用运算符。需要注意的是无论哪一种，括号内部前和后，都需要用空格隔开，运算符也需要空格隔开

- ```bash
  a=10
  b=20
  if [ ${a} -lt ${b} ] #需要用空格隔开
  then
      echo "a小于b"
  else
      echo "a大于b"
  fi
  
  if (( ${a} < ${b} ))
  then
      echo "a小于b"
  else
      echo "a大于b"
  fi
  ```

  **for语句**

  - **语法：**in 列表可以包含替换、字符串和文件名。

  - ```bash
    for var in item1 item2 ... itemN
    do
        command1
        command2
        ...
        commandN
    done
    ```

  - 举例：`this is a string`例如这句话，如果不加双引号，就会按照单词依次输出，如果加上双引号，就是一个字符串，整句输出一次

  - ```bash
    for var in $(ls) #in后面的位置也可以换成1,2,3等，或者是字符串一句话，分别打印每个单词
    do
        echo $var
    done   
    ```

  **case语句**

  - **语法：**

  - 取值后面必须为单词 **in**，每一模式必须以右括号结束。取值可以为变量或常数，匹配发现取值符合某一模式后，其间所有命令开始执行直至 **;;**。

    取值将检测匹配的每一个模式。一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号 ***** 捕获该值，再执行后面的命令。

    最后的结束语是case反过来

  - ```bash
    case 值 in
    模式1)
        command1
        command2
        ...
        commandN
        ;;
    模式2)
        command1
        command2
        ...
        commandN
        ;;
     *)
     	command
     	;;
    esac
    ```

    举例

  - ```bash
    echo '输入 1 到 4 之间的数字:'
    echo '你输入的数字为:'
    read aNum
    case $aNum in
        1)  echo '你选择了 1'
        ;;
        2)  echo '你选择了 2'
        ;;
        3)  echo '你选择了 3'
        ;;
        4)  echo '你选择了 4'
        ;;
        *)  echo '你没有输入 1 到 4 之间的数字'
        ;;
    esac
    ```

  

**4.函数**

  **返回值**：参数返回，可以显示加**return** 返回，如果不加，将以最后一条命令运行结果，作为返回值。 **return** 后跟数值 **n(0-255)**，用`$?`				用来接收函数的返回值，return只能返回数值，其他的不行

  **举例**：先写定义，和c++编程一样，但是在调用时不写括号

```bash
#!/bin/bash
funWithReturn(){
    echo "这个函数会对输入的两个数字进行相加运算..."
    echo "输入第一个数字: "
    read aNum
    echo "输入第二个数字: "
    read anotherNum
    echo "两个数字分别为 $aNum 和 $anotherNum !"
    return $(($aNum+$anotherNum))
}
funWithReturn
echo "输入的两个数字之和为 $? !"
```

**传参**：函数后面紧跟着参数，并不是编程的形参传递，传参可以是任何类型，但是返回值只能是0~255的int

```bash
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
```



**5.启动其他脚本**

采用`.`的方式引入其他脚本，例如`. .test_shell02.sh`，注意两个点之间需要有空格



**6.启动新的终端**

`gnome-terminal -t " title-name " -x bash -c " sh ./test_shell02.sh; exec bash;"`

- `-t` 为打开终端的标题，便于区分
- `-x` 后面的为要在打开的终端中执行的脚本，根据需要自己修改就行了
- `exec bash;` 是让打开的终端在执行完脚本后不关闭
- ` bash -c` 执行shell命令



## 3.查找/结束进程

`ps aux | grep 软件`：查看当前ubuntu系统该软件的进程，例如`ps aux | grep gz`，举例输出为：

`kill 端口号 `杀死进程，如果实在没办法杀死，使用`kill -9 端口号`强制杀死

```
bit         3658  0.8  0.9 1177605916 157832 ?   SLl  11:33   0:02 /usr/share/code/code --unity-launch /home/bit/work/sim/gz_model/models/missile/model.sdf
bit         7967  0.0  0.0  12328  2496 pts/0    S+   11:37   0:00 grep --color=auto gz
```

这个情况就是有两个进程运行，一个是软件没有结束的，另一个是grep调起来的

进程一：`missile/model.sdf`一直在运行，3658是第一个的进程标识符（PID），使用`kill 3658`结束该进程；

进程二：第二个不算，是grep调用的gz进程，使用grep查找会自动调起来这个相关的进程









## 4.纯CMake编写

### 1.cmakelists示例：

vscode和cmake联合配置（这个是纯用cmake构建的，没有涉及到g++的东西，vscode里的task.json就可以按照他的配置cmake make build）：https://blog.csdn.net/TU_Dresden/article/details/122414454

标准的一套流程：

```cmake
#最低版本要求：必须
cmake_minimum_required(VERSION 3.0)
#项目名称，版本：必须
project(My_test_project)

############非必须##############
#导入第三方库：非必须
find_package(库的名称 REQUIRED)  # REQUIRED 参数表示如果找不到会报错
#将第三方库的头文件添加到项目路径下
include_directories(${库名称_INCLUDE_DIRS})
############非必须##############

#导入自己的include文件夹下的头文件
include_directories(include)  #将指定的目录添加到项目的包含路径
#添加可执行文件：必须
add_executable(想生成的可执行文件 源文件1.cpp 源文件2.cpp)
#不同的源文件是多文件调用的情况。只有一个文件中有main函数，其他文件中是函数的实现，

############非必须##############
#链接可执行文件和库文件：如果有第三方库，就需要链接
target_link_libraries(想生成的可执行文件 ${库名称1_LIBRARIES} ${库名称1_LIBRARIES})
############非必须##############
```

**文件流程：**

```css
project_Mytest/
│
├── CMakeLists.txt
│
├── include/
│   ├── header1.h
│   ├── header2.h
│   └── header3.h
└── src/
    ├── source1.cpp
    ├── source2.cpp
    ├── source3.cpp
    └── source4.cpp
```

**编写流程：**

```
####创建
mkdir -p not_ros_ws/src
cd not_ros_ws
mkdir build
mkdir include

####编译
cd build
cmake ..
make
```



### **2.find_package：**

**（1）搜索路径：**

如果提供了 `CMAKE_PREFIX_PATH` 或 `PackageName_DIR`，优先在这些路径中查找。

1.用户指定路径

- `CMAKE_PREFIX_PATH`：指定搜索的前缀路径。
- `CMAKE_MODULE_PATH`：指定自定义模块文件的路径。

2.系统默认路径：

- `/usr/lib/`
- `/usr/local/lib/`
- `/usr/include/`
- `/usr/local/include/`

3.环境变量：

- 如 PATH 或特定的包路径环境变量

**（2）相关操作：**

`find_package` 找到包后，会**自动生成并设置**一系列变量，供后续的 CMake 脚本使用。这些变量包含包的路径、版本等信息。常见的变量包括：

| 变量名                           | 描述                                                         | 示例                                                         |
| -------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **`<PackageName>_FOUND`**        | 布尔值，设置为 `TRUE`，表示包已找到。                        | `Boost_FOUND` 表示 Boost 库被成功找到。                      |
| **`<PackageName>_INCLUDE_DIRS`** | 包含包的头文件路径，供 `include_directories` 或 `target_include_directories` 使用。 | `Boost_INCLUDE_DIRS` 可能包含 `/usr/include/boost`。         |
| **`<PackageName>_LIBRARIES`**    | 包含包的库文件路径，供 `target_link_libraries` 使用。        | `Boost_LIBRARIES` 可能包含 Boost 的 `filesystem` 和 `regex` 库的路径。 |

示例：

```cmake
find_package(Boost 1.70 REQUIRED)
if(Boost_FOUND)
  message("Boost found: ${Boost_VERSION}")
  include_directories(${Boost_INCLUDE_DIRS})
  target_link_libraries(my_target PRIVATE ${Boost_LIBRARIES})
endif()
```



### 3.include_directories

用于指定头文件搜索路径的命令

include_directories：全局地添加头文件搜索路径，影响之后定义的所有目标（targets）。

target_include_directories：为特定的目标（target）添加头文件搜索路径，仅影响指定的目标

| 特性                   | `include_directories`              | `target_include_directories`                   |
| ---------------------- | ---------------------------------- | ---------------------------------------------- |
| **作用范围**           | 全局，影响后续所有目标             | 局部，仅影响指定的目标                         |
| **现代性**             | 较老，CMake早期风格                | 现代，CMake 3.x推荐方式                        |
| **访问控制**           | 无访问控制，所有路径对所有目标可见 | 支持 `PUBLIC`、`PRIVATE`、`INTERFACE` 访问控制 |
| **模块化**             | 不模块化，可能导致头文件冲突       | 模块化，路径仅与特定目标关联                   |
| **与导入目标的兼容性** | 需要手动指定路径，兼容性一般       | 与导入目标（如 `Qt5::Widgets`）无缝集成        |
| **推荐程度**           | 不推荐（除非兼容旧项目）           | 推荐，CMake官方鼓励使用                        |

举例：

`include_directories`

```cmake
include_directories(/usr/include/boost)
add_executable(my_app main.cpp)   #第一个需要boost库，会使用/usr/include/boost这个路径
add_executable(my_app2 main2.cpp) #第二个即使不需要boost库，也会使用/usr/include/boost这个路径
add_library(my_lib SHARED lib.cpp) #生成动态库时，会使用
```

`target_include_directories`

```cmake
target_include_directories(my_app PRIVATE /usr/include/boost) #只规定了my_app才会使用这个路径
add_executable(my_app main.cpp)
add_executable(my_app2 main2.cpp)  #不会使用
add_library(my_lib SHARED lib.cpp) #生成动态库时，也不会使用
```



## 5.ros版cmake编写

ros2包必须包含package.xml和cmakelists文件

### 1.cmakelists示例：

**cmakelist的工程名称`project(...)`必须和package中包的名称保持一致**

**必须命令：**

| 命令                                  | 功能                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| `cmake_minimum_required(VERSION ...)` | 指定CMake的最低版本要求。                                    |
| `project(...)`                        | 定义项目名称及语言等信息。                                   |
| `find_package(ament_cmake REQUIRED)`  | 引入ROS 2构建工具。必须。                                    |
| `find_package(...)`                   | 查找依赖的包，如 `rclcpp`、`std_msgs`、`sensor_msgs`等。每个依赖都需明确声明。 |
| `add_executable(...)`                 | 添加可执行文件（如节点）。                                   |
| `ament_target_dependencies(...)`      | 为可执行文件声明依赖库。                                     |
| `install(TARGETS ...)`                | 安装可执行文件，使得可以通过 `ros2 run` 调用。               |
| `ament_package()`                     | 必须，标识这是一个 ROS 2 包。必须放在文件底部。              |

**非必须命令：**

| 命令                                         | 说明                                   |
| -------------------------------------------- | -------------------------------------- |
| `add_library(...)`                           | 如果你写的是库而不是节点，用来构建库。 |
| `install(DIRECTORY include/...)`             | 安装头文件（对于库项目有用）。         |
| `rosidl_generate_interfaces(...)`            | 如果定义了自定义消息/服务时必须。      |
| `enable_testing()` 和 `ament_add_gtest(...)` | 如果需要写测试用例时使用。             |
| `target_include_directories(...)`            | 手动添加头文件路径时使用。             |
| `ament_export_dependencies(...)`             | 如果你的库被别的包引用，需导出依赖。   |

模板示例：

```cmake
cmake_minimum_required(VERSION 3.8)
project(my_ros2_node)

# 指定使用 C++17
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
endif()

# 查找必须的依赖包
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(rosidl_default_generators REQUIRED)

# 添加可执行节点
add_executable(talker src/talker.cpp)
add_executable(listener src/listener.cpp)

# 添加include路径和库文件的路径
ament_target_dependencies(talker rclcpp std_msgs)
ament_target_dependencies(listener rclcpp std_msgs)

# 自定义消息
rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/MyMsg.msg"
  "srv/MySrv.srv"
)

# 安装可执行文件
install(TARGETS
  talker
  listener
  DESTINATION lib/${PROJECT_NAME}
)

# 必须的宏
ament_package()

```

### 2.ament_target_dependencies

#### （1）基础知识

根据`find_package`后cmake自动生成的变量，将这些find得到的包的头文件目录和库文件目录添加到可执行程序中

`ament_target_dependencies`实际上是对` target_include_directories `和 `target_link_libraries `的封装，是ros2自己封装好的，就不需要自己手动在配置了

| 特性           | target_link_libraries                      | ament_target_dependencies                   |
| -------------- | ------------------------------------------ | ------------------------------------------- |
| 来源           | CMake 标准命令                             | ROS 2 ament_cmake 专用宏                    |
| 主要功能       | 链接库文件，设置头文件路径（需手动配置）   | 自动处理 ROS 2 依赖的头文件、库和消息配置   |
| 适用场景       | 通用 C++ 项目，需精确控制链接行为          | ROS 2 项目，简化依赖管理                    |
| 头文件路径     | 需通过 target_include_directories 手动指定 | 自动添加依赖的头文件路径                    |
| 依赖管理       | 需手动确保所有依赖的库和头文件正确配置     | 自动解析 find_package 和 package.xml 的依赖 |
| ROS 2 消息支持 | 不直接支持，需手动处理消息生成的头文件和库 | 自动支持 ROS 2 消息和服务生成的头文件和库   |
| 复杂性         | 配置较复杂，适合低级控制                   | 配置简单，适合 ROS 2 生态                   |
| 依赖传递       | 可通过 PUBLIC/PRIVATE/INTERFACE 控制       | 自动处理依赖传递，符合 ROS 2 规范           |

**举例**：

```cmake
ament_target_dependencies(my_node PUBLIC rclcpp std_msgs)
等价于
target_include_directories(my_node PUBLIC ${rclcpp_INCLUDE_DIRS} ${std_msgs_INCLUDE_DIRS})
target_link_libraries(my_node PUBLIC ${rclcpp_LIBRARIES} ${std_msgs_LIBRARIES})
```

**第一种方式**：不使用ros2，因此需要手动指定头文件路径（target_include_directories）和库，比较麻烦

```cmake
find_package(rclcpp REQUIRED)
find_package(geometry_msgs REQUIRED)
#需要手动指定include的文件夹
target_include_directories(my_node PUBLIC ${rclcpp_INCLUDE_DIRS}  ${geometry_msgs_INCLUDE_DIRS})
target_link_libraries(my_node  rclcpp::rclcpp  geometry_msgs::geometry_msgs)
add_executable(my_node src/my_node.cpp)
```

**第二种方式**：使用ros2

```cmake
find_package(rclcpp REQUIRED)
find_package(geometry_msgs REQUIRED)
#一行命令直接高定对库的链接和依赖
ament_target_dependencies(my_node rclcpp geometry_msgs)
add_executable(my_node src/my_node.cpp)
```

#### （2）注意事项

`ament_target_dependencies(my_node rclcpp geometry_msgs)`后面括号中的`geometry_msgs`等名称,必须和`find_package(geometry_msgs)`中括号里的名称保持完全一致，ros2封装的是直接根据名称匹配`find_package`后找到的`库_INCLUDE`等参数的



### 3.rosidl_generate_interfaces

#### （1）作用

用于生成 ROS 2 消息（.msg）、服务（.srv）或动作（.action）文件的接口代码，将 .msg、.srv 和 .action 文件编译为目标语言（如 C++、Python）的头文件、源文件或模块，以便在 ROS 2 节点中发布、订阅或调用这些接口

生成的接口会被安装到标准位置（如 install//share/<package_name>），然后`source install setup.bash`就能找到这个自定义的消息了

#### （2）举例

```cmake
rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/MyMessage.msg"
  "srv/MyService.srv"
  "action/MyAction.action"
  DEPENDENCIES std_msgs
)
```



### 4.install

#### （1）作用

用于指定在构建项目后如何将生成的目标文件（如可执行文件、库文件）或其他文件（配置文件、文档等）安装到指定的目标目录中

在ros2中，一般都需要有这个install，作用都是将可执行文件安装到install文件夹下，然后`source install/setup.bash`就能找到这个可执行程序，然后执行`ros2 run <包名> 可执行文件名称`

#### （2）语法

```cmake
install(TARGETS target_name
        [RUNTIME DESTINATION <dir>]  # 可执行文件
        [LIBRARY DESTINATION <dir>]  # 动态库
        [ARCHIVE DESTINATION <dir>]  # 静态库
)


add_executable(offboard_attitude_control src/examples/offboard/offboard_attitude_control.cpp)
ament_target_dependencies(offboard_attitude_control rclcpp px4_msgs)
install(TARGETS offboard_attitude_control DESTINATION lib/${PROJECT_NAME}) #就是将offboard_attitude_control安装到install/lib/包名/，路径下
```





### 5.ament_export_dependencies

#### （1）作用

将当前包mypackage的依赖（通过 find_package 查找的包）导出到下游包nextpackage，下游包只需要find_package当前包，就能找到mypackage中所有的依赖关系，而无需在在nextpackage中find其他的包

ament_export_dependencies 的核心功能是将当前包的依赖关系导出到其 **CMake 配置文件**（通常是 Config.cmake），以便下游包可以自动获取这些依赖的配置信息。

#### （2）**举例**：

上游包package1

```cmake
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
ament_export_dependencies(rclcpp std_msgs)
```

下游包package2

```cmake
find_package(package1 REQUIRED)
ament_target_dependencies(my_node2 package1)
ament_target_dependencies(my_node2 rclcpp std_msgs)
```

## 6.package.xml编写

**cmakelist的工程名称`project(...)`必须和package中包的名称保持一致，由package来定义**

`package.xml` 是 **ROS 2 功能包的元数据文件**，它的主要作用是为构建工具（如 `colcon`）、依赖管理工具（如 `rosdep`）以及其他 ROS 工具链提供关于这个功能包的**基本信息和依赖信息**。

以一个包含 C++ 节点的 ROS 2 功能包为例，它的 `package.xml` 作用包括：

* 告诉系统这个包叫 `my_robot_node`，版本是 1.0；
* 告诉 `colcon` 需要用 `ament_cmake` 构建；
* 告诉 `colcon` 编译时需要 `rclcpp` 和 `std_msgs`；
* 告诉 `ros2 run` 怎么找到这个包编译生成的可执行文件；
* 告诉 `rosdep` 在缺依赖时如何自动安装。

基本参数解释：

| 标签                 | 说明                                                         |
| -------------------- | ------------------------------------------------------------ |
| `<buildtool_depend>` | 构建工具依赖，ROS 2 使用 `ament_cmake` 或 `ament_python`。   |
| `<build_depend>`     | 编译时所需依赖（头文件、链接库等）。一般要和cmakelist的find_package对应起来 |
| `<exec_depend>`      | 运行时所需依赖（例如运行节点时需要的库）。                   |
| `<depend>`           | 同时作为 `build` 和 `exec` 的快捷写法（不推荐）。            |
| `<export>`           | 用于声明构建类型、插件导出等                                 |

以下是一个模板：

```xml
<?xml version="1.0"?>
<package format="3">
  <name>my_custom_msgs</name>
  <version>0.1.0</version>
  <description>Custom message definitions for my project</description>

  <maintainer email="yourname@example.com">Your Name</maintainer>
  <license>Apache-2.0</license>

  <!-- 基本构建工具 -->
  <buildtool_depend>ament_cmake</buildtool_depend>
    <buildtool_depend>ament_cmake_python</buildtool_depend>

  <!-- 生成和运行消息接口的依赖 -->
  <build_depend>rosidl_default_generators</build_depend>
  <exec_depend>rosidl_default_runtime</exec_depend>

  <!-- 使用标准内建类型 -->
  <build_depend>builtin_interfaces</build_depend>
  <exec_depend>builtin_interfaces</exec_depend>

  <depend>rclpy</depend>
  <depend>rclcpp</depend>
  <depend>sensor_msgs</depend>
  <depend>geometry_msgs</depend>
  <depend>cv_bridge</depend>
  <depend>eigen3</depend>

  <depend>std_msgs</depend> <!-- 这行话等同于下面两行话 -->
  <build_depend>std_msgs</build_depend>
  <exec_depend>std_msgs</exec_depend>

  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>

```



## 7.自定义消息

步骤一：新建msgs文件夹，在文件夹中新建消息文件

步骤二：在package.xml中定义

```xml
<!-- 包名定义 -->
<name>my_package</name>
<!-- 生成和运行消息接口的依赖 -->
<build_depend>rosidl_default_generators</build_depend>
```

步骤三：在camelist中编写：

```cmake
project(my_package) #必须和package.xml中的包名保持一致
rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/MyMessage.msg" #示例，自定义的消息
  "srv/MyService.srv"
  "action/MyAction.action"
  DEPENDENCIES std_msgs
)
```

步骤四：编译自定义消息

```sh
colcon build --packages-select my_msgs
```

步骤五：导入c++或python

c++中会自动将大写转为小写，并在前面加一个下划线（第一个大写字母除外），驼峰转下划线。

python中依旧保持驼峰，需要import对应的消息包

```c++
include "my_package/msgs/my_message"
```

```python
from my_package.msgs import MyMessage
```



## 8.查找安装的软件包

```
 dpkg -l | grep <name>
```

组合命令：

`dpkg -l`：显示系统安装的软件包信息

`|`：管道操作符，将前一个命令的输出（`dpkg -l`）作为后一个命令的输入（`grep`）

`grep <name>`：在文件中搜索包含名称为name的所有行

举例：

```sh
dpkg -l | grep webots #输入
```

```sh
#输出
ii  ros-foxy-webots-ros2                            2023.0.2-1focal.20230606.053955       amd64        Interface between Webots and ROS2
ii  ros-foxy-webots-ros2-control                    2023.0.2-1focal.20230606.040139       amd64        ros2_control plugin for Webots
ii  ros-foxy-webots-ros2-driver                     2023.0.2-1focal.20230606.035454       amd64        Implementation of the Webots - ROS 2 interface
ii  webots     
```



## 9.卸载软件包

apt安装的包：

```sh
#卸载指令
sudo apt remove <name>
#如果想连同配置文件一起删除，可以使用 purge 命令：
sudo apt purge <name>
```

deb安装的包：

```sh
#查找安装的name包
dpkg -l | grep <name>
#卸载指令
sudo dpkg -r <name>
#如果希望连同配置文件一起删除，可以使用 purge 选项：
sudo dpkg --purge <name>
```



## 下载安装问题

### 1.rosdep init问题

·运行`sudo rosdep init`报错：

`ERROR: cannot download default sources list from:
https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/sources.list.d/20-default.list Website may be down.
<urlopen error <urlopen error [Errno 111] Connection refused> (https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/sources.list.d/20-default.list)>`

```
sudo -E rosdep init 可以解决
```

如果提示：

`ERROR: default sources list file already exists: /etc/ros/rosdep/sources.list.d/20-default.list
Please delete if you wish to re-initialize`那就删掉这个文件，重新运行sudo rosdep init

参考：https://github.com/ros-infrastructure/rosdep/issues/791



