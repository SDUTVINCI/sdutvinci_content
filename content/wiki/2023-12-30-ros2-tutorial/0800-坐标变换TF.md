---
title: "坐标变换TF"
---

### 坐标变换
#### 引言与应用场景
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1264.webp)

**`里程计ODOM`**

**`惯性计IMU`**

**`激光雷达Laser`**

**`摄像头Camera`**

场景1：现有一移动式机器人底盘，在底盘上安装了一雷达，雷达相对于底盘的偏移量已知，现雷达检测到一障碍物信息，获取到坐标分别为(x,y,z)，该坐标是以雷达为参考系的，如何将这个坐标转换成以小车为参考系的坐标呢？

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1265.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1266.webp)

激光雷达与小车的中心或边缘相差的横纵距离，以及激光雷达与墙的距离及小车与墙的距离。

场景2:现有一带机械臂的机器人(比如:PR2)需要夹取目标物，当前机器人头部摄像头可以探测到目标物的坐标(x,y,z)，不过该坐标是以摄像头为参考系的，而实际操作目标物的是机械臂的夹具，当前我们需要将该坐标转换成相对于机械臂夹具的坐标，这个过程如何实现？

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1267.webp)

以上通过TF即可算

#### 概念与作用
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1268.webp)

TF实行**右手坐标系**

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1269.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1270.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1271.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1272.webp)

重要的就是相对位置和时间，在某个时间某个物体位于某个位置。（时间差太大，数据会被废弃）

#### 案例安装以及运行
安装乌龟案列：

```bash

# Humble版本安装
sudo apt-get install ros-humble-turtle-tf2-py ros-humble-tf2-tools ros-humble-tf-transformations

# Jazzy版本安装
sudo apt-get install ros-jazzy-turtle-tf2-py ros-jazzy-tf2-tools ros-jazzy-tf-transformations
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1273.webp)

此外，还需要安装一个名为 `transforms3d` 的 Python 包，它为 `tf_transformations`包提供四元数和欧拉角变换功能，安装命令如下：

```bash

# 方式一（不推荐）
sudo apt intall python3-pip
pip3 install transforms3d

#方式二（推荐）
sudo apt install python3-transforms3d
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1274.webp)

启动两个终端，终端1输入如下命令：

```bash
ros2 launch turtle_tf2_py turtle_tf2_demo.launch.py
```

该命令会启动 turtlesim_node 节点，turtlesim_node 节点中自带一只小乌龟 turtle1，除此之外还会新生成一只乌龟 turtle2，turtle2 会运行至 turtle1 的位置。

终端2输入如下命令：

```bash
ros2 run turtlesim turtle_teleop_key
```

该终端下可以通过键盘控制 turtle1 运动，并且 turtle2 会跟随 turtle1 运动（参考引言部分的 **案例1** ）。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1275.webp)

龟男🐢🚹会跟随前面的乌龟🐢运动

#### 坐标变换相关消息
坐标变换的实现其本质是基于话题通信的发布订阅模型的，发布方可以发布坐标系之间的相对关系，订阅方则可以监听这些消息，并实现不同坐标系之间的变换。显然的根据之前介绍，在话题通信中，接口消息作为数据载体在整个通信模型中是比较重要的一部分，本节将会介绍坐标变换中常用的两种接口消息：`geometry_msgs/msg/TransformStamped`和`geometry_msgs/msg/PointStamped`。

前者用于描述某一时刻两个坐标系之间相对关系的接口，后者用于描述某一时刻坐标系内某个坐标点的位置的接口。在坐标变换中，会经常性的使用到坐标系相对关系以及坐标点信息。

##### geometry_msgs/msg/TransformStamped
通过如下命令查看接口定义：

```bash
ros2 interface show geometry_msgs/msg/TransformStamped
```

接口定义解释：

```bash
std_msgs/Header header
    builtin_interfaces/Time stamp     # 时间戳
        int32 sec         #秒
        uint32 nanosec    #纳秒
    string frame_id                   # 父级坐标系
string child_frame_id                 # 子级坐标系
Transform transform                   # 子级坐标系相对于父级坐标系的位姿
    Vector3 translation               # 三维偏移量
        float64 x
        float64 y
        float64 z
    Quaternion rotation               # 四元数
        float64 x 0
        float64 y 0
        float64 z 0
        float64 w 1
```

描述一个物体运动一般有6个自由度：X，Y，Z，Yaw，Pitch，Roll。

三个平动，三个旋转：

Vector3 translation代表3个平移

Quaternion rotation四元数可以转化为三个欧拉角(yaw，pitch，roll)

（Q：为何不用欧拉角而用四元数？A：因为用欧拉角计算会出现死锁现象，所以选择用四元数，而不用欧拉角，以便避免欧拉角的缺陷。）

3个平移以米meter为单位

3个旋转以弧度rad为单位

四元数类似于欧拉角用于表示坐标系的相对姿态，

具体转化详见[大疆开发板C型嵌入式软件教程文档.pdf](https://sdutvincirobot.feishu.cn/wiki/PVS8wQzRgiTRqpko9l4cEK33nhw)的18.3节

具体转化算法（Mahony算法）（ROS2的TF2库中也有具体的转化算法）：

https://x-io.co.uk/open-source-imu-and-ahrs-algorithms/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1276.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1277.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1278.webp)

按右手坐标系来看，N2相对于N1沿X轴平移了1m(一格代表1米）。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1279.webp)

旋转

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1280.webp)

##### geometry_msgs/msg/PointStamped
通过如下命令查看接口定义：

```bash
ros2 interface show geometry_msgs/msg/PointStamped
```

接口定义解释：

```bash
std_msgs/Header header
    builtin_interfaces/Time stamp    # 时间戳
        int32 sec   #秒
        uint32 nanosec   #纳秒
    string frame_id                  # 参考系
Point point                          # 三维坐标
    float64 x
    float64 y
    float64 z
```

在三维中的坐标点

### 坐标变换广播
#### 引言与案例及分析
坐标系相对关系主要有两种： **静态坐标系相对关系** 与 **动态坐标系相对关系** 。

所谓静态坐标系相对关系是指两个坐标系之间的相对位置是固定不变的，比如：车辆上的雷达、摄像头等组件一般是固定式的，那么雷达坐标系相对于车辆底盘坐标系或摄像头坐标系相对于车辆底盘坐标系就是一种静态关系。

所谓动态坐标系相对关系是指两个坐标系之间的相对位置关系是动态改变的，比如：车辆上机械臂的关节或夹爪、多车编队中不同车辆等都是可以运动的，那么机械臂的关节或夹爪坐标系相对车辆底盘坐标系或不同车辆坐标系的相对关系就是一种动态关系。

本节会主要介绍如何实现静态坐标变换广播与动态坐标变换广播。另外，本节还会演示如何发布坐标点消息。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1281.webp)

激光雷达Laser和色相头与底盘位置是静态的，而机械臂的末端执行器与机器人的位置是动态的。

##### 1.案例需求
**案例1：** 现有一无人车，在无人车底盘上装有固定式的雷达与摄像头，已知车辆底盘、雷达与摄像头各对应一坐标系，各坐标系的原点取其几何中心。现又已知雷达坐标系相对于底盘坐标系的三维平移量分别为：x方向0.4米，y方向0米，z方向0.2米，无旋转。摄像头坐标系相对于底盘坐标系的三维平移量分别为：x方向-0.5米，y方向0米，z方向0.4米，无旋转。请广播雷达与底盘的坐标系相对关系，摄像头与底盘的坐标系相对关系，并在 rviz2 中查看广播的结果。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1282.webp)

**案例2：** 启动 turtlesim_node，设该节点中窗体有一个世界坐标系(左下角为坐标系原点)，乌龟是另一个坐标系，乌龟可以通过键盘控制运动，请动态发布乌龟坐标系与世界坐标系的相对关系。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1283.webp)

##### 2.案例分析
在上述案例中，案例1需要使用到静态坐标变换，案例2则需要使用动态坐标变换，不论无论何种实现关注的要素都有两个：

1.  如何广播坐标系相对关系；

2.  如何使用 rviz2 显示坐标系相对关系。

##### 3.流程简介
以编码的实现实现静态或动态坐标变换的流程类似，主要步骤如下：

1.  编写广播实现；

2.  编辑配置文件；

3.  编译；

4.  执行；

5.  在 rviz2 中查看坐标系关系。

案例我们会采用 C++ 和 Python 分别实现，二者都遵循上述实现流程。

另外：需要说明的是，静态广播器除了可以以编码的方式实现外，在 tf2 中还内置了相关工具，可以无需编码，直接执行节点并传入表示坐标系相对关系的参数，即可实现静态坐标系关系的发布（即命令行，有命令行可以优先用命令行）。而动态广播器没有提供类似的工具。（即必须敲代码）

##### 4.准备工作
终端下进入工作空间的src目录，调用如下两条命令分别创建C++功能包。

```bash
ros2 pkg create cpp03_tf_broadcaster --build-type ament_cmake --dependencies rclcpp tf2 tf2_ros geometry_msgs turtlesim
```

tf2功能包内包含了四元数与欧拉角的转换算法。

tf2_ros功能包内包含了广播对象。

geometry_msgs功能包消息载体

turtlesim功能包是获取乌龟🐢位姿

#### 静态广播器_命令行实现
##### 1.静态广播器工具
在 `tf2_ros`功能包中提供了一个名为`static_transform_publisher`的可执行文件，通过该文件可以直接广播静态坐标系关系，其使用语法如下。

**格式1：**

使用以米为单位的 x/y/z 偏移量和以弧度为单位的roll/pitch/yaw（可直译为滚动/俯仰/偏航，分别指的是围绕 x/y/z 轴的旋转）向 tf2 发布静态坐标变换。

```bash
ros2 run tf2_ros static_transform_publisher --x x --y y --z z --yaw yaw --pitch pitch --roll roll --frame-id frame_id --child-frame-id child_frame_id
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1284.webp)

父级坐标系和子级坐标系是必须要写的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1285.webp)

如果这些可选参数不选的话，默认父级坐标系和子级坐标系重合，也就是偏移量和旋转度都是0。

偏移量：X,Y,Z

旋转度：QX,QY,QZ,QW **或者** ROLL,PITCH,YAW（单位是弧度）

时间戳：不用设置，会以发布的时间为起点

**格式2：**

使用以米为单位的 x/y/z 偏移量和 qx/qy/qz/qw 四元数向 tf2 发布静态坐标变换。

```bash
ros2 run tf2_ros static_transform_publisher --x x --y y --z z --qx qx --qy qy --qz qz --qw qw --frame-id frame_id --child-frame-id child_frame_id
```

注意：在上述两种格式中除了用于表示父级坐标系的`--frame-id`和用于表示子级坐标系的`--child-frame-id`之外，其他参数都是可选的，如果未指定特定选项，那么将直接使用默认值。

##### 2.静态广播器工具使用
打开两个终端，终端1输入如下命令发布雷达（laser）相对于底盘（base_link）的静态坐标变换（重合）：

```bash
ros2 run tf2_ros static_transform_publisher --frame-id base_link --child-frame-id laser
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1286.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1287.webp)

base_link是父级参考系

laser是子级参考系

一般选父系参考系

##### 3.rviz2 查看坐标系关系
新建终端，通过命令`rviz2`打开 rviz2 并配置相关插件查看坐标变换消息：

1.  将 Global Options 中的 Fixed Frame 设置为 base_link；

2.  点击 add 按钮添加 TF 插件；

3.  勾选 TF 插件中的 show names。

右侧 Grid 中将以图形化的方式显示坐标变换关系。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1288.webp)

如图，两个参考系重合。

打开两个终端，终端1输入如下命令发布雷达（laser）相对于底盘（base_link）的静态坐标变换：

```bash
ros2 run tf2_ros static_transform_publisher --x 0.4 --y 0 --z 0.2 --yaw 0.5 --roll 0 --pitch 0 --frame-id base_link --child-frame-id laser
```

终端2输入如下命令发布摄像头（camera）相对于底盘（base_link）的静态坐标变换：

```bash
ros2 run tf2_ros static_transform_publisher --x -0.5 --y 0 --z 0.4 --yaw 0 --roll 0 --pitch 0 --frame-id base_link --child-frame-id camera
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1289.webp)

#### 静态广播器_C++实现
##### 框架搭建
```cmake
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(turtlesim REQUIRED)

add_executable(demo01_static_tf_broadcaster src/demo01_static_tf_broadcaster.cpp)
ament_target_dependencies(
  demo01_static_tf_broadcaster
  "rclcpp"
  "tf2"
  "tf2_ros"
  "geometry_msgs"
  "turtlesim"
)

install(TARGETS demo01_static_tf_broadcaster
  DESTINATION lib/${PROJECT_NAME})
```

##### 广播实现
```cpp
/*  
  需求：编写静态坐标变换程序，执行时传入两个坐标系的相对位姿关系以及父子级坐标系id，
       程序运行发布静态坐标变换。
  步骤：
    1.包含头文件；
    2.判断终端传入的参数是否合法；
    3.初始化 ROS 客户端；
    4.定义节点类；
      4-1.创建静态坐标变换发布方；
      4-2.组织并发布消息。
    5.调用 spin 函数，并传入对象指针；
    6.释放资源。

*/

// 1.包含头文件；
#include <geometry_msgs/msg/transform_stamped.hpp>

#include <rclcpp/rclcpp.hpp>
#include <tf2/LinearMath/Quaternion.h>
#include <tf2_ros/static_transform_broadcaster.h>

using std::placeholders::_1;

// 4.定义节点类；
class MinimalStaticFrameBroadcaster : public rclcpp::Node
{
public:
  explicit MinimalStaticFrameBroadcaster(char * transformation[]): Node("minimal_static_frame_broadcaster")
  {
    // 4-1.创建静态坐标变换发布方；
    tf_publisher_ = std::make_shared<tf2_ros::StaticTransformBroadcaster>(this);

    this->make_transforms(transformation);
  }

private:
  // 4-2.组织并发布消息。
  void make_transforms(char * transformation[])
  {
    // 组织消息
    geometry_msgs::msg::TransformStamped t;

    rclcpp::Time now = this->get_clock()->now();
    t.header.stamp = now;
    t.header.frame_id = transformation[7];
    t.child_frame_id = transformation[8];

    t.transform.translation.x = atof(transformation[1]);
    t.transform.translation.y = atof(transformation[2]);
    t.transform.translation.z = atof(transformation[3]);
    tf2::Quaternion q;
    q.setRPY(
      atof(transformation[4]),
      atof(transformation[5]),
      atof(transformation[6]));
    t.transform.rotation.x = q.x();
    t.transform.rotation.y = q.y();
    t.transform.rotation.z = q.z();
    t.transform.rotation.w = q.w();

    // 发布消息
    tf_publisher_->sendTransform(t);
  }
  std::shared_ptr<tf2_ros::StaticTransformBroadcaster> tf_publisher_;
};

int main(int argc, char * argv[])
{
  // 2.判断终端传入的参数是否合法；
  auto logger = rclcpp::get_logger("logger");

  if (argc != 9) {
    RCLCPP_INFO(
      logger, "运行程序时请按照：x y z roll pitch yaw frame_id child_frame_id 的格式传入参数");
    return 1;
  }

  // 3.初始化 ROS 客户端；
  rclcpp::init(argc, argv);
  // 5.调用 spin 函数，并传入对象指针；
  rclcpp::spin(std::make_shared<MinimalStaticFrameBroadcaster>(argv));
  // 6.释放资源。
  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1290.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1291.webp)

必须传参正确，否则抛错。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1292.webp)

创建组织并发布数据的函数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1293.webp)

用最简单的重载。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1294.webp)

这样就可以发送了，接下来编辑发送的内容。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1295.webp)

stamp时间戳设置为当前时间，now()函数是设置为当前时间。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1296.webp)

atof()是转化为float浮点类型

x()和getx()都是获取四元数x。（y，z，w以此类推）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1297.webp)

终端中进入当前工作空间，编译功能包：

```bash
colcon build --packages-select cpp03_tf_broadcaster
```

当前工作空间下，启动两个终端，终端1输入如下命令发布雷达（laser）相对于底盘（base_link）的静态坐标变换：

```bash
. install/setup.bash 
ros2 run cpp03_tf_broadcaster demo01_static_tf_broadcaster 0.4 0 0.2 0 0 0 base_link laser
```

参考 **静态广播器（命令）** 内容启动并配置 rviz2，最终执行结果与案例1类似。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1298.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1299.webp)

本质就是话题通信，但这个话题通信的topic是啥呢？

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1300.webp)

通过查看该类源码，就得知，话题为/tf_static

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1301.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1302.webp)

发布方有俩，订阅方有一个。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1303.webp)

这俩是发布方

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1304.webp)

这个是订阅方

#### 动态广播器_C++实现
##### 框架搭建
CMakeLists.txt 文件需要添加如下内容：

```cmake
add_executable(demo02_dynamic_tf_broadcaster src/demo02_dynamic_tf_broadcaster.cpp)
ament_target_dependencies(
  demo02_dynamic_tf_broadcaster
  "rclcpp"
  "tf2"
  "tf2_ros"
  "geometry_msgs"
  "turtlesim"
)
```

文件中 install 修改为如下内容：

```cmake
install(TARGETS demo01_static_tf_broadcaster
  demo02_dynamic_tf_broadcaster
  DESTINATION lib/${PROJECT_NAME})
```

##### 广播实现
```cpp
/*   
  需求：编写动态坐标变换程序，启动 turtlesim_node 以及 turtle_teleop_key 后，该程序可以发布
       乌龟坐标系到窗口坐标系的坐标变换，并且键盘控制乌龟运动时，乌龟坐标系与窗口坐标系的相对关系
       也会实时更新。

  步骤：
    1.包含头文件；
    2.初始化 ROS 客户端；
    3.定义节点类；
      3-1.创建动态坐标变换发布方；
      3-2.创建乌龟位姿订阅方；
      3-3.根据订阅到的乌龟位姿生成坐标帧并广播。
    4.调用 spin 函数，并传入对象指针；
    5.释放资源。

*/
// 1.包含头文件；
#include <geometry_msgs/msg/transform_stamped.hpp>

#include <rclcpp/rclcpp.hpp>
#include <tf2/LinearMath/Quaternion.h>
#include <tf2_ros/transform_broadcaster.h>
#include <turtlesim/msg/pose.hpp>

using std::placeholders::_1;

// 3.定义节点类；
class MinimalDynamicFrameBroadcaster : public rclcpp::Node
{
public:
  MinimalDynamicFrameBroadcaster(): Node("minimal_dynamic_frame_broadcaster")
  {
    // 3-1.创建动态坐标变换发布方；
    tf_broadcaster_ = std::make_unique<tf2_ros::TransformBroadcaster>(*this);

    std::string topic_name = "/turtle1/pose";

    // 3-2.创建乌龟位姿订阅方；
    subscription_ = this->create_subscription<turtlesim::msg::Pose>(
      topic_name, 10,
      std::bind(&MinimalDynamicFrameBroadcaster::handle_turtle_pose, this, _1));
  }

private:
  // 3-3.根据订阅到的乌龟位姿生成坐标帧并广播。   
  void handle_turtle_pose(const turtlesim::msg::Pose & msg)
  {
    // 组织消息
    geometry_msgs::msg::TransformStamped t;
    rclcpp::Time now = this->get_clock()->now();

    t.header.stamp = now;
    t.header.frame_id = "world";   //窗体坐标系
    t.child_frame_id = "turtle1";  //乌龟坐标系

    t.transform.translation.x = msg.x;
    t.transform.translation.y = msg.y;
    t.transform.translation.z = 0.0;      //乌龟在平面内运动

    //从欧拉角转换为四元数
    tf2::Quaternion q;
    q.setRPY(0, 0, msg.theta);        //乌龟只有Yaw
    t.transform.rotation.x = q.x();
    t.transform.rotation.y = q.y();
    t.transform.rotation.z = q.z();
    t.transform.rotation.w = q.w();
    // 发布消息
    tf_broadcaster_->sendTransform(t);
  }
  rclcpp::Subscription<turtlesim::msg::Pose>::SharedPtr subscription_;
  std::unique_ptr<tf2_ros::TransformBroadcaster> tf_broadcaster_;
};

int main(int argc, char * argv[])
{
  // 2.初始化 ROS 客户端；
  rclcpp::init(argc, argv);
  // 4.调用 spin 函数，并传入对象指针；
  rclcpp::spin(std::make_shared<MinimalDynamicFrameBroadcaster>());
  // 5.释放资源。
  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1305.webp)

入口参数：

参数1话题名称

参数2QoS

参数3回调函数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1306.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1307.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1308.webp)

操控乌龟运动，会使乌龟在Rviz2里也运动

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1309.webp)

#### 坐标点发布_C++实现
##### 案例与分析
**案例需求**

**案例：** 无人车上安装有激光雷达，现激光雷达扫描到一点状障碍物并且可以定位障碍物的坐标，请在雷达坐标系下发布障碍物坐标点数据，并在 rviz2 中查看发布结果。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1310.webp)

**案例分析**

上述案例，是一个简单的话题发布程序，在了解坐标点`geometry_msgs/msg/PointStamped`接口消息之后，直接通过话题发布方按照一定逻辑发布消息即可。

**流程简介**

程序实现主要步骤如下：

1.  编写话题发布实现；

2.  编辑配置文件；

3.  编译；

4.  执行；

5.  在 rviz2 中查看运行结果。

案例我们会采用 C++ 和 Python 分别实现，二者都遵循上述实现流程。

CMakeLists.txt 文件需要添加如下内容：

```cmake
add_executable(demo03_point_publisher src/demo03_point_publisher.cpp)
ament_target_dependencies(
  demo03_point_publisher
  "rclcpp"
  "tf2"
  "tf2_ros"
  "geometry_msgs"
  "turtlesim"
)
```

文件中 install 修改为如下内容：

```cmake
install(TARGETS demo01_static_tf_broadcaster
  demo02_dynamic_tf_broadcaster
  demo03_point_publisher
  DESTINATION lib/${PROJECT_NAME})
```

##### 实现
```cpp
/*  
    需求：发布雷达坐标系中某个坐标点相对于雷达（laser）坐标系的位姿。
    步骤：
        1.包含头文件；
        2.初始化 ROS 客户端；
        3.定义节点类；
            3-1.创建坐标点发布方；
            3-2.创建定时器；
            3-3.组织并发布坐标点消息。
        4.调用 spin 函数，并传入对象指针；
        5.释放资源。

*/
// 1.包含头文件；
#include "rclcpp/rclcpp.hpp"
#include "geometry_msgs/msg/point_stamped.hpp"

using namespace std::chrono_literals;

// 3.定义节点类；
class MinimalPointPublisher: public rclcpp::Node {
public:
    MinimalPointPublisher(): Node("minimal_point_publisher"),x(0.1){
        // 3-1.创建坐标点发布方；
        point_pub_ = this->create_publisher<geometry_msgs::msg::PointStamped>("point",10);
        // 3-2.创建定时器；
        timer_ = this->create_wall_timer(0.1s,std::bind(&MinimalPointPublisher::on_timer, this));
    }
private:
    void on_timer(){
        // 3-3.组织并发布坐标点消息。
        geometry_msgs::msg::PointStamped point;
        point.header.frame_id = "laser";
        point.header.stamp = this->now();
        x += 0.004;
        point.point.x = x;
        point.point.y = 0.0;
        point.point.z = 0.1;        
        point_pub_->publish(point);
    }
    rclcpp::Publisher<geometry_msgs::msg::PointStamped>::SharedPtr point_pub_;
    rclcpp::TimerBase::SharedPtr timer_;
    double_t x;
};

int main(int argc, char const *argv[])
{
    // 2.初始化 ROS 客户端；
    rclcpp::init(argc,argv);
    // 4.调用 spin 函数，并传入对象指针；
    rclcpp::spin(std::make_shared<MinimalPointPublisher>());
    // 5.释放资源。
    rclcpp::shutdown();
    return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1311.webp)

创建定时器用create_wall_time()函数，要填时间间隔和对应的回调函数。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1312.webp)

**执行**

当前工作空间下，启动两个终端，终端1输入如下命令发布雷达（laser）相对于底盘（base_link）的静态坐标变换：

```bash
. install/setup.bash 
ros2 run cpp03_tf_broadcaster demo01_static_tf_broadcaster 0.4 0 0.2 0 0 0 base_link laser
```

终端2输入如下命令发布障碍物相对于雷达（laser）的坐标点：

```bash
. install/setup.bash 
ros2 run cpp03_tf_broadcaster demo03_point_publisher
```

**rviz2 查看坐标系关系**

参考 **5.3.2 静态广播器（命令）** 内容启动并配置 rviz2，显示坐标变换后，再添加 PointStamped 插件并将其话题设置为 /point，最终显示结果与案例演示类似。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1313.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1314.webp)

通过这里可以改球的透明度和大小

#### 小结
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1315.webp)

静态广播只发布一次。

而动态广播和坐标点广播都是发布多次。

但实质上就是话题通信。

### 坐标变换监听
#### 案例与分析
**案例1：** 在 **5.3 坐标变换广播** 中发布了laser相对于base_link和camra相对于base_link的坐标系关系，请求解laser相对于camera的坐标系关系。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1316.webp)

**案例2：** 在 **5.3 坐标变换广播** 中发布了laser相对于base_link的坐标系关系且发布了laser坐标系下的障碍物的坐标点数据，请求解base_link坐标系下该障碍物的坐标。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1317.webp)

**案例分析**

在上述案例中，案例1是多坐标系的场景下实现不同坐标系之间的变换，案例2则是要实现同一坐标点在不同坐标系下的变换，虽然需求不同，但是相关算法都被封装好了，我们只需要调用相关 API 即可。

**流程简介**

两个案例的实现流程类似，主要步骤如下：

1.  编写坐标变换程序实现；

2.  编辑配置文件；

3.  编译；

4.  执行。

案例我们会采用 C++ 和 Python 分别实现，二者都遵循上述实现流程。

**准备工作**

终端下进入工作空间的src目录，调用如下两条命令分别创建C++功能包和Python功能包。

```bash
ros2 pkg create cpp04_tf_listener --build-type ament_cmake --dependencies rclcpp tf2 tf2_ros geometry_msgs --node-name demo01_tf_listener
```

#### 坐标 **系** 变换监听_C++
##### 实例分析
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1318.webp)

与之前实现不太一样，要保存到buffer中。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1319.webp)

因为之前的广播是发一条订阅一条。

但是在坐标变换中，是多对一实现的。

多个广播发布的消息组成一个坐标树，要从坐标树中获取不同坐标帧的变换。

把多条广播方的消息组成坐标树，就要使用buffer，把消息全部存到缓存buffer中。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1320.webp)

这里为何要做异常处理呢？

因为进程间的通信开销比较大，是有延迟的，可能程序要开始做变换了，可惜消息还没订阅到。

消息都没有，就会抛异常。直到buffer里有数据，坐标也转化成功，才不会抛异常。

##### 实现
```cpp
/*  
  需求：订阅 laser 到 base_link 以及 camera 到 base_link 的坐标系关系，
       并生成 laser 到 camera 的坐标变换。
  步骤：
    1.包含头文件；
    2.初始化 ROS 客户端；
    3.定义节点类；
      3-1.创建tf缓存对象指针；融合多个坐标系相对关系为一棵坐标树。
      3-2.创建tf监听器；指定缓存对象，会将所有广播器广播的数据写入缓存。
      3-3.编写定时器，循环实现转换；按照条件查找符合条件的坐标系并生成变换后的坐标帧。
    4.调用 spin 函数，并传入对象指针；
    5.释放资源。

*/
#include "rclcpp/rclcpp.hpp"
#include "tf2_ros/transform_listener.h"
#include "tf2_ros/buffer.h"
#include "tf2/LinearMath/Quaternion.h"

using namespace std::chrono_literals;

// 3.定义节点类；
class MinimalFrameListener : public rclcpp::Node {
public:
  MinimalFrameListener():Node("minimal_frame_listener"){
    tf_buffer_ = std::make_unique<tf2_ros::Buffer>(this->get_clock());
    transform_listener_ = std::make_shared<tf2_ros::TransformListener>(*tf_buffer_);
    timer_ = this->create_wall_timer(1s, std::bind(&MinimalFrameListener::on_timer,this));
  }

private:
  void on_timer(){
    try
    {
      auto transformStamped = tf_buffer_->lookupTransform("camera","laser",tf2::TimePointZero);
      RCLCPP_INFO(this->get_logger(),"----------------------转换结果----------------------");
      RCLCPP_INFO(this->get_logger(),"frame_id:%s",transformStamped.header.frame_id.c_str());
      RCLCPP_INFO(this->get_logger(),"child_frame_id:%s",transformStamped.child_frame_id.c_str());
      RCLCPP_INFO(this->get_logger(),"坐标:(%.2f,%.2f,%.2f)",
                transformStamped.transform.translation.x,
                transformStamped.transform.translation.y,
                transformStamped.transform.translation.z);

    }
    catch(const tf2::LookupException& e)
    {
      RCLCPP_INFO(this->get_logger(),"坐标变换异常：%s",e.what());
    }

  }
  rclcpp::TimerBase::SharedPtr timer_;
  std::shared_ptr<tf2_ros::TransformListener> transform_listener_;
  std::unique_ptr<tf2_ros::Buffer> tf_buffer_;
};

int main(int argc, char const *argv[])
{
  // 2.初始化 ROS 客户端；
  rclcpp::init(argc,argv);
  // 4.调用 spin 函数，并传入对象指针；
  rclcpp::spin(std::make_shared<MinimalFrameListener>());
  // 5.释放资源。
  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1321.webp)

要填回调函数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1322.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1323.webp)

实现坐标系转换的核心函数就是lookuptransform()

target_frame就是父级

source_frame就是子级

time是时间，一般都写最新时刻的

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1324.webp)

如果buffer没捕获到，抛异常的函数实现

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1325.webp)

这样就成功转换坐标了，可以打印转换的坐标。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1326.webp)

除了用try catch处理转换异常，还可以用buffer底下的函数转换。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1327.webp)

比如这个cantransform可以判断是否可以正常转换。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1328.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1329.webp)

这种报错要改为C风格字符串

启动三个终端，终端1输入如下命令发布雷达（laser）相对于底盘（base_link）的静态坐标变换：

```bash
ros2 run tf2_ros static_transform_publisher --frame-id base_link --child-frame-id camera --x -0.5 --z -0.4
```

终端2输入如下命令发布摄像头（camera）相对于底盘（base_link）的静态坐标变换：

```bash
ros2 run tf2_ros static_transform_publisher --frame-id base_link --child-frame-id laser--x 0.4 --z 0.2
```

终端3输入如下命令执行坐标系变换：

```bash
. install/setup.bash 
ros2 run cpp04_tf_listener demo01_tf_listener
```

终端3将输出 laser 相对于 camera 的坐标，具体结果请参考案例1。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1330.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1331.webp)

当只运行一个广播，他会抛错。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1332.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1333.webp)

当所有广播都跑起来时，才会正常转换。

#### 坐标 **点** 变换监听_C++
##### 实现框架
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1334.webp)

求laser测得的坐标点到base_link的距离

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1335.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1336.webp)

laser到point的坐标用的是point话题，与监听器用的话题不一样，所以不好订阅，要创建一个新的订阅对象。

##### 框架搭建
package.xml在创建功能包时，所依赖的部分功能包已经自动配置了，不过为了实现坐标点变换，还需要添加依赖包`tf2_geometry_msgs`和

`message_filters`，修改后的配置内容如下：

```xml
<depend>rclcpp</depend>
<depend>tf2</depend>
<depend>tf2_ros</depend>
<depend>geometry_msgs</depend>
<depend>tf2_geometry_msgs</depend>
<depend>message_filters</depend>
```

CMakeLists.txt 文件修改后的内容如下：

```cmake
find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(message_filters REQUIRED)

add_executable(demo01_tf_listener src/demo01_tf_listener.cpp)
ament_target_dependencies(
  demo01_tf_listener
  "rclcpp"
  "tf2"
  "tf2_ros"
  "geometry_msgs"
)
add_executable(demo02_message_filter src/demo02_message_filter.cpp)
ament_target_dependencies(
  demo02_message_filter
  "rclcpp"
  "tf2"
  "tf2_ros"
  "geometry_msgs"
  "tf2_geometry_msgs"
  "message_filters"
)
install(TARGETS demo01_tf_listener
  demo02_message_filter
  DESTINATION lib/${PROJECT_NAME})
```

##### 实现
```cpp
/*  
  需求：将雷达感知到的障碍物的坐标点数据（相对于 laser 坐标系），
       转换成相对于底盘坐标系（base_link）的坐标点。

  步骤：
    1.包含头文件；
    2.初始化 ROS 客户端；
    3.定义节点类；
      3-1.创建tf缓存对象指针；
      3-2.创建tf监听器；
      3-3.创建坐标点订阅方并订阅指定话题；
      3-4.创建消息过滤器过滤被处理的数据；
      3-5.为消息过滤器注册转换坐标点数据的回调函数。
    4.调用 spin 函数，并传入对象指针；
    5.释放资源。

*/// 1.包含头文件；#include <geometry_msgs/msg/point_stamped.hpp>#include <message_filters/subscriber.h>#include <rclcpp/rclcpp.hpp>#include <tf2_ros/buffer.h>#include <tf2_ros/create_timer_ros.h>#include <tf2_ros/message_filter.h>#include <tf2_ros/transform_listener.h>// #ifdef TF2_CPP_HEADERS//   #include <tf2_geometry_msgs/tf2_geometry_msgs.hpp>// #else//   #include <tf2_geometry_msgs/tf2_geometry_msgs.h>// #endif#include <tf2_geometry_msgs/tf2_geometry_msgs.hpp>using namespace std::chrono_literals;

// 3.定义节点类；class MessageFilterPointListener : public rclcpp::Node
{
public:
  MessageFilterPointListener(): Node("message_filter_point_listener")
  {

    target_frame_ = "base_link";

    typedef std::chrono::duration<int> seconds_type;
    seconds_type buffer_timeout(1);
    // 3-1.创建tf缓存对象指针；
    tf2_buffer_ = std::make_shared<tf2_ros::Buffer>(this->get_clock());
    auto timer_interface = std::make_shared<tf2_ros::CreateTimerROS>(
      this->get_node_base_interface(),
      this->get_node_timers_interface());
    tf2_buffer_->setCreateTimerInterface(timer_interface);
    // 3-2.创建tf监听器；
    tf2_listener_ = std::make_shared<tf2_ros::TransformListener>(*tf2_buffer_);

    // 3-3.创建坐标点订阅方并订阅指定话题；
    point_sub_.subscribe(this, "point");
    // 3-4.创建消息过滤器过滤被处理的数据；
    tf2_filter_ = std::make_shared<tf2_ros::MessageFilter<geometry_msgs::msg::PointStamped>>(
      point_sub_, *tf2_buffer_, target_frame_, 100, this->get_node_logging_interface(),
      this->get_node_clock_interface(), buffer_timeout);
    // 3-5.为消息过滤器注册转换坐标点数据的回调函数。
    tf2_filter_->registerCallback(&MessageFilterPointListener::msgCallback, this);
  }

private:
  void msgCallback(const geometry_msgs::msg::PointStamped::SharedPtr point_ptr){
    geometry_msgs::msg::PointStamped point_out;
    try {
      tf2_buffer_->transform(*point_ptr, point_out, target_frame_);
      RCLCPP_INFO(
        this->get_logger(), "坐标点相对于base_link的坐标:(%.2f,%.2f,%.2f)",
        point_out.point.x,
        point_out.point.y,
        point_out.point.z);
    } catch (tf2::TransformException & ex) {
      RCLCPP_WARN(
        // Print exception which was caughtthis->get_logger(), "Failure %s\n", ex.what());
    }
  }
  std::string target_frame_;
  std::shared_ptr<tf2_ros::Buffer> tf2_buffer_;
  std::shared_ptr<tf2_ros::TransformListener> tf2_listener_;
  message_filters::Subscriber<geometry_msgs::msg::PointStamped> point_sub_;
  std::shared_ptr<tf2_ros::MessageFilter<geometry_msgs::msg::PointStamped>> tf2_filter_;
};

int main(int argc, char * argv[]){
  // 2.初始化 ROS 客户端；
  rclcpp::init(argc, argv);
  // 4.调用 spin 函数，并传入对象指针；
  rclcpp::spin(std::make_shared<MessageFilterPointListener>());
  // 5.释放资源。
  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1337.webp)

把上面此参数设置一下

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1338.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1339.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1340.webp)

进行数据解析

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1341.webp)

用第一个重载。

转换过程会抛异常，可以不管他。转化失败会在终端上自动抛异常。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1342.webp)

在当前工作空间下，启动三个终端，终端1输入如下命令发布雷达（laser）相对于底盘（base_link）的静态坐标变换：

```bash
. install/setup.bash 
ros2 run cpp03_tf_broadcaster demo01_static_tf_broadcaster 0.4 0 0.2 0 0 0 base_link laser
```

终端2输入如下命令发布障碍物相对于雷达（laser）的坐标点：

```bash
. install/setup.bash 
ros2 run cpp03_tf_broadcaster demo03_point_publisher
```

终端3输入如下命令执行坐标点变换：

```bash
. install/setup.bash 
ros2 run cpp04_tf_listener demo02_message_filter
```

终端3将输出坐标点相对于 base_link 的坐标，具体结果请参考案例2。

按顺序依次发布

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1343.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1344.webp)

### 坐标变换工具
在 ROS2 的 TF 框架中除了封装了坐标广播与订阅功能外，还提供了一些工具，可以帮助我们提高开发、调试效率。本节主要介绍这些工具的使用，这些工具主要涉及到两个功能包：`tf2_ros`和`tf2_tools`。

`tf2_ros`包中提供的常用节点如下：

*   static_transform_publisher：该节点用于广播静态坐标变换；（用过很多次了）

*   tf2_monitor：该节点用于打印所有或特定坐标系的发布频率与网络延迟；

*   tf2_echo：该节点用于打印特定坐标系的平移旋转关系。

`tf2_tools`包中提供的节点如下：

*   view_frames：该节点可以生成显示坐标系关系的 pdf 文件，该文件包含树形结构的坐标系图谱。

上述诸多工具中，功能包`tf2_ros`中的`static_transform_publisher`节点在 **静态广播器（命令）** 一节中已有详细说明，本节不再介绍。

**准备工作：**

为了更好的演示工具的使用，请先启动若干坐标系广播节点，比如：可以按照 **静态广播器（命令）** 和 **动态广播器（C++）** 广播一些坐标系消息。

第一个终端：

```bash
ros2 run tf2_ros static_transform_publisher --x 0.4 --y 0 --z 0.2 --yaw 0.5 --roll 0 --pitch 0 --frame-id base_link --child-frame-id laser
```

第二个终端：

```bash
ros2 run tf2_ros static_transform_publisher --x -0.5 --y 0 --z 0.4 --yaw 0 --roll 0 --pitch 0 --frame-id base_link --child-frame-id camera
```

第三个终端：

```bash
ros2 run turtlesim turtlesim_node
```

第四个终端：

```bash
ros2 run turtlesim turtle_teleop_key
```

第五个终端：

```bash
source install/setup.bash
ros2 run cpp03_tf_broadcaster demo02_dynamic_tf_broadcaster
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1345.webp)

以上是准备工作

##### 1.tf2_monitor
###### 1.1打印所有坐标系的发布频率与网络延迟
终端执行命令：

```bash
ros2 run tf2_ros tf2_monitor
```

运行结果：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1346.webp)

该命令有10s的阻塞时间，因为要在一个区间内测频率。

静态的没有变化，而动态的发布频率是有一些变化的。

该函数每隔10s会再发布一次。

###### 1.2打印指定坐标系的发布频率与网络延迟
终端执行命令：

```bash
ros2 run tf2_ros tf2_monitor camera laser
```

运行结果：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1347.webp)

刚开始在缓存里拿不到数据抛错很正常。

##### 2.tf2_echo
打印两个坐标系的平移旋转关系。

终端执行命令：

```bash
ros2 run tf2_ros tf2_echo world turtle1
```

运行结果：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1348.webp)

会出平移量和旋转量

会以好几种方式表现，如*平移距离，四元数，弧度欧拉角，角度欧拉角，矩阵*等。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1349.webp)

当龟男🐢🚹动弹的时候，数值也会发生改变。

##### 3.view_frames（常用）
以图形化的方式显示坐标系关系。

终端执行命令：

```bash
ros2 run tf2_tools view_frames
```

运行结果：将会在**当前目录**下生成 frames_xxxx.gv 与 frames_xxxx.pdf 文件，其中 xxxx 为时间戳。打开 pdf 文件显示如下内容：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1350.webp)

此节点会监听我们5秒钟，并生成对应的文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1351.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1352.webp)

上图中有两棵坐标树，但是实际上的项目中，咱们只能设计一棵树，而不能设计两个坐标树。

### 练习
#### 乌龟跟随
#### 乌龟护航
### 小结
