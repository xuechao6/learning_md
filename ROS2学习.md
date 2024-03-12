# ROS2学习

## Qos问题：

**背景**：

* ros2的发布和订阅依靠消息质量Qos，在网络不好/数据不重要时采用UDP传输，在数据非常重要时候采用TCP传输保证可以接收到数据，而ros1只能采用TCP的方式传输数据，因此在网络不好的时候，依旧采用TCP来回确定的方式会造成数据无法接收的问题。
* ros2发布者和订阅者的可靠性都有`BEST_EFFECT`和`RELIABLE`两种模式，分别对应尽力而为和可靠，下面是两种可靠性的兼容关系，第二种情况不兼容

<img src="picture/Qos1.png">

第二种情况的报错举例：`[WARN] [1710250781.712647750] [mytest]: New publisher discovered on topic '/fmu/out/vehicle_odometry', offering incompatible QoS. No messages will be sent to it. Last incompatible policy: RELIABILITY_QOS_POLICY`

**解决方法：**

将订阅者的可靠性改为尽力而为`BEST_EFFECT`，参考https://blog.csdn.net/weixin_42454034/article/details/106905418

* 方法一：在话题名称后面加上`rclcpp::SystemDefaultsQoS()`，是例如下

  ```c++
  odo_self_subscriber = this->create_subscription<VehicleOdometry>("/fmu/out/vehicle_odometry", rclcpp::SystemDefaultsQoS() ,[this](const VehicleOdometry::SharedPtr msg) {topic_callback(msg);});
  ```

* 方法二：在话题名称后面加上`rclcpp::QoS(rclcpp::KeepLast(1)).best_effort().transient_local()`，`best_effort()`是可选项，也可以选择为`reliable()`，是例如下：

  ```c++
  odo_self_subscriber = this->create_subscription<VehicleOdometry>("/fmu/out/vehicle_odometry", rclcpp::QoS(rclcpp::KeepLast(1)).best_effort().transient_local(), [this](const VehicleOdometry::SharedPtr msg) {topic_callback(msg);});
  ```

