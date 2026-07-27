---
title: "Gz Sim（Gazebo Harmonic）"
---

### Gz Sim（Gazebo Harmonic 及之后的版本（ROS2 Jazzy及之后的版本））

新版Gazebo 是 ROS2 中使用的全新机器人仿真工具，**它是 Gazebo 的升级版本**。在Humble他叫Ignition Gazebo(也叫Gazebo Fortress)，在Jazzy中叫Gazebo Harmonic(去掉了Ignition的名字)(https://community.gazebosim.org/t/a-new-era-for-gazebo/1356)。它具备更好的性能和可用性，并通过紧密集成 ROS2 来提供强大的仿真环境。

#### Gazebo安装与运行

官方教程：
https://docs.ros.org/en/jazzy/Tutorials/Advanced/Simulators/Gazebo/Gazebo.html

下面这个网站是官方教程(ROS2 Jazzy 的 Gazebo Harmonic)：

https://gazebosim.org/docs/harmonic/getstarted/

https://gazebosim.org/docs/harmonic/library_reference_nav/

源码：https://github.com/gazebosim/docs/blob/master/harmonic/tutorials

**安装**

Gazebo 是不依赖于ROS2的一个独立的项目，可以独自安装。但是如果安装了ROS2，在ROS2存储库中已经集成了对应版本的 Gazebo，可以调用如下指令直接安装：

```bash

# 通用命令
sudo apt install ros-${ROS_DISTRO}-ros-gz

# Humble版本
sudo apt install ros-humble-ros-gz

# Jazzy版本
sudo apt install ros-jazzy-ros-gz
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1705.webp)

**运行**

Gazebo 安装完毕之后，可以通过两种方式启动。

方式1，以Gazebo 的方式启动，指令如下：

```bash

# Humble版本
ign gazebo

# Jazzy版本
gz sim
```

方式2，以ROS2的方式 启动，指令如下 ：

```bash
ros2 launch ros_gz_sim gz_sim.launch.py
```

二者运行结果一致，如下图所示：在弹出窗口中，选择仿真环境然后点击`run`按钮即可运行。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1706.webp)

**界面介绍**

接下来以Empty仿真环境为例，介绍一下Gazebo的界面组成。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1707.webp)

注意：如果你的Gazebo不卡，但是Gazebo巨卡的话，请确认Gazebo是以独显打开的，而不是核显。

如果不会切换应用显卡，可以直接把核显关闭掉，从混合输出切换为独立显卡输出。

**工具栏**

*   顶部的工具栏包含两个按钮，左侧的文件菜单按钮（水平条纹）和右侧的插件按钮（垂直省略号）。

1.  文件菜单按钮（水平条纹）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1708.webp)

*   文件菜单按钮包含将仿真环境保存到文件、保存和加载界面配置以及自定义界面样式等设置。

2.  右侧的插件按钮（垂直省略号）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1709.webp)

*   插件按钮列出了所有可用的插件。点击后会弹出插件列表，向下滚动此列表以查看所有插件。 当选择一个时，其界面将出现在右侧面板中。

**3D视窗**

*   左上方工具栏包含多种几何体（球体、框体、圆柱体）按钮和变换控件。通过集合体按钮可以直接将盒子、球体或圆柱体模型插入仿真环境。只需单击要插入的形状，然后将其放入环境中。该形状将自动捕捉到地平面上。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1710.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1711.webp)

*   主视图会显示仿真环境，我们可以通过鼠标以不同方式来导航场景，相关操作如下：

```bash
左键单击：选择实体
右键单击：打开带有选项的菜单：
   Move to：移动到以实体为中心的场景
   Follow：选择一个实体让视图保持居中，无论是移动还是平移
   Remove：从模拟中删除实体
   Copy：复制实体
   Past: 粘贴实体
   View：显示实体的重心（Center of Mass）、碰撞边界（Collisions）、惯性（Inertia）、
         关节（Joints）、坐标系（Frames）、透明度（Transparent）、线框（Wireframe）等属性
左键单击并拖动：在场景中平移
右键单击并拖动：放大和缩小
滚轮向前/向后：放大和缩小
滚轮单击并拖动：旋转场景
```

*   想移动这个球，需要点左上角的移动模式，再左键单击选中物体

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1712.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1713.webp)

*   在视窗的底部，从左到右分别是是播放、步长按钮和实时因子（Real-Time Factor，RTF）。点击播放按钮将开始运行仿真环境， 再次点击可以暂停运行。步长按钮用于设置仿真时间的离散单位，可以通过将鼠标悬停在按钮上来自定义步长。实时因子表示仿真运行速度相对于真实时间的比例。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1714.webp)

**右侧面板**

右侧面板用于显示插件，当前仿真环境默认包含两个插件：Model和Entity Tree。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1715.webp)

*   Entity Tree 中会显示仿真环境中的实体列表；

*   点击Entity Tree中的实体后，可以在Model中显示该实体的相关信息。

*   也可以按住 Ctrl 并单击以选择多个实体；

*   还可以右键单击任何插件以打开基本设置或关闭。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1716.webp)

在Gazebo中内置了许多插件，可以点击工具栏的右侧按钮自行添加，比如：可以选择 Grid Config 插件调整世界网格的特征，包括单元格大小、网格位置、单元格计数、或颜色等。

后期随着应用的深入，也会陆续介绍其他一些插件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1717.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1718.webp)

#### 与ROS2集成
本节将介绍如何实现Ignition Gazebo与ROS2的集成，以实现二者之间的交互，比如，可以通过ROS2的键盘控制节点控制机器人运动，并且在rviz2中显示机器人的里程计(odom)数据。其流程大致如下：

1.  启动 Ignition Gazebo 仿真环境；

2.  通过 ros_gz_bridge 建立 ROS2 与 Ignition Gazebo 的连接；

3.  启动 ROS2 相关节点实现与 Ignition Gazebo 的数据收发。

Ignition Gazebo与ROS2的的所有集成实现，基本都遵循上述流程。

**启动仿真环境**

在 Ignition Gazebo 安装时，已经内置了一些仿真环境，直接启动即可。在此我们可以使用名为`visualize_lidar.sdf`的仿真文件，该文件对应的仿真环境中包括了差速机器人以及激光雷达的仿真。启动指令如下：

```bash
ign gazebo -v 4 -r visualize_lidar.sdf
#或者
gz sim -v 4 -r visualize_lidar.sdf
```

或者也可以以ROS2 launch的方式启动，指令如下：

```bash
ros2 launch ros_gz_sim gz_sim.launch.py gz_args:="-v 4 -r visualize_lidar.sdf"
```

两种方式本质相同，都是启动了Ignition Gazebo并且加载了visualize_lidar.sdf文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1719.webp)

**建立连接**

虽然仿真环境中的机器人已经配置了运动控制插件，可以通过`/model/vehicle_blue/cmd_vel`话题订阅速度指令并运动，但是Gazebo与ROS2中的消息格式并不一致，所以还需要通过ros_gz_bridge这一桥接功能包，实现二者之间消息的转换，调用指令如下：

```bash
ros2 run ros_gz_bridge parameter_bridge /model/vehicle_blue/cmd_vel@geometry_msgs/msg/Twist]gz.msgs.Twist
```

通过该指令可以将发布在`/model/vehicle_blue/cmd_vel`话题上的`geometry_msgs/msg/Twist`类型的ROS2消息转换成可以被Gzebo识别的`gz.msgs.Twist`类型的消息。

**启动ROS2节点**

启动ROS2的键盘控制节点，并将话题重映射为`/model/vehicle_blue/cmd_vel`，指令如下：

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=/model/vehicle_blue/cmd_vel
```

接下来就可以使用键盘控制机器人运动了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1720.webp)

#### ros_gz_bridge
ros_gz_bridge是连接ROS2与Gazebo的桥梁，ROS2与Gazebo使用的消息并不兼容，必须通过ros_gz_bridge进行转换。

**ros_gz_bridge使用语法**

ROS2与Gazebo的桥接是通过ros_gz_bridge包中的parameter_bridge节点实现，其使用语法如下：

```bash
parameter_bridge [<topic@ROS2_type@Gz_type> ..]  [<service@ROS2_srv_type[@Gz_req_type@Gz_rep_type]> ..]
```

在话题Topic中， **第一个@** 符号是话题名称和消息类型的 **分隔符** 。

第一个@符号后面是ROS消息类型。

ROS消息类型后面是@、\[或\]符号：

*   **@**  表示双向桥接；

*   **\[**  表示从Gazebo到ROS的桥接；

*   **\]**  表示从ROS到Gazebo的桥接。

方向符号后是Gazebo Transport消息类型。

（两个@不是同一个含义）

在服务Service中， **第一个@** 符号是服务名称和类型的 **分隔符** 。

第一个@符号后面是ROS服务类型。可以选择地包括Gazebo请求和响应类型，在它们之间用@符号分隔。

**仅** 支持将Gazebo服务公开为ROS服务，即ROS服务将请求转发到Gazebo服务，然后将响应转发回ROS客户端。

双向桥接示例：

```bash
parameter_bridge /chatter@std_msgs/msg/String@gz.msgs.StringMsg
```

从Gazebo到ROS的桥接示例：

```bash
parameter_bridge /chatter@std_msgs/msg/String[gz.msgs.StringMsg
```

从ROS到Gazebo的桥接示例：

```bash
parameter_bridge /chatter@std_msgs/msg/String]gz.msgs.StringMsg
```

服务桥接示例：

```bash
parameter_bridge /world/default/control@ros_gz_interfaces/srv/ControlWorld
或者：
parameter_bridge /world/default/control@ros_gz_interfaces/srv/ControlWorld@gz.msgs.WorldControl@gz.msgs.Boolean
```

**ros_gz_bridge支持的消息类型**

以下列出 Jazzy 当前常用和官方文档中列出的部分映射；完整映射以 `ros2 run ros_gz_bridge parameter_bridge -h` 和 ROS2 对应版本的 ros_gz_bridge 文档为准。

以下是ROS2与Gazebo中话题消息类型对应表：

| ROS2消息类型 | Gazebo Transport 类型 |
|:---|:---|
| builtin_interfaces/msg/Time | gz.msgs.Time |
| geometry_msgs/msg/Point | gz.msgs.Vector3d |
| geometry_msgs/msg/Pose | gz.msgs.Pose |
| geometry_msgs/msg/PoseArray | gz.msgs.Pose_V |
| geometry_msgs/msg/PoseStamped | gz.msgs.Pose |
| geometry_msgs/msg/PoseWithCovariance | gz.msgs.PoseWithCovariance |
| geometry_msgs/msg/Quaternion | gz.msgs.Quaternion |
| geometry_msgs/msg/Transform | gz.msgs.Pose |
| geometry_msgs/msg/TransformStamped | gz.msgs.Pose |
| geometry_msgs/msg/Twist | gz.msgs.Twist |
| geometry_msgs/msg/TwistWithCovariance | gz.msgs.TwistWithCovariance |
| geometry_msgs/msg/TwistWithCovarianceStamped | gz.msgs.TwistWithCovariance |
| geometry_msgs/msg/Vector3 | gz.msgs.Vector3d |
| geometry_msgs/msg/Wrench | gz.msgs.Wrench |
| geometry_msgs/msg/WrenchStamped | gz.msgs.Wrench |
| nav_msgs/msg/Odometry | gz.msgs.Odometry |
| nav_msgs/msg/Odometry | gz.msgs.OdometryWithCovariance |
| rcl_interfaces/msg/ParameterValue | gz.msgs.Any |
| ros_gz_interfaces/msg/Altimeter | gz.msgs.Altimeter |
| ros_gz_interfaces/msg/Contact | gz.msgs.Contact |
| ros_gz_interfaces/msg/Contacts | gz.msgs.Contacts |
| ros_gz_interfaces/msg/Dataframe | gz.msgs.Dataframe |
| ros_gz_interfaces/msg/Entity | gz.msgs.Entity |
| ros_gz_interfaces/msg/Float32Array | gz.msgs.Float_V |
| ros_gz_interfaces/msg/GuiCamera | gz.msgs.GUICamera |
| ros_gz_interfaces/msg/JointWrench | gz.msgs.JointWrench |
| ros_gz_interfaces/msg/Light | gz.msgs.Light |
| ros_gz_interfaces/msg/SensorNoise | gz.msgs.SensorNoise |
| ros_gz_interfaces/msg/StringVec | gz.msgs.StringMsg_V |
| ros_gz_interfaces/msg/TrackVisual | gz.msgs.TrackVisual |
| ros_gz_interfaces/msg/VideoRecord | gz.msgs.VideoRecord |
| ros_gz_interfaces/msg/WorldControl | gz.msgs.WorldControl |
| rosgraph_msgs/msg/Clock* | gz.msgs.Clock* |
| sensor_msgs/msg/BatteryState | gz.msgs.BatteryState |
| sensor_msgs/msg/CameraInfo | gz.msgs.CameraInfo |
| sensor_msgs/msg/FluidPressure | gz.msgs.FluidPressure |
| sensor_msgs/msg/Image | gz.msgs.Image |
| sensor_msgs/msg/Imu | gz.msgs.IMU |
| sensor_msgs/msg/JointState | gz.msgs.Model |
| sensor_msgs/msg/Joy | gz.msgs.Joy |
| sensor_msgs/msg/LaserScan | gz.msgs.LaserScan |
| sensor_msgs/msg/MagneticField | gz.msgs.Magnetometer |
| sensor_msgs/msg/NavSatFix | gz.msgs.NavSat |
| sensor_msgs/msg/PointCloud2 | gz.msgs.PointCloudPacked |
| std_msgs/msg/Bool | gz.msgs.Boolean |
| std_msgs/msg/ColorRGBA | gz.msgs.Color |
| std_msgs/msg/Empty | gz.msgs.Empty |
| std_msgs/msg/Float32 | gz.msgs.Float |
| std_msgs/msg/Float64 | gz.msgs.Double |
| std_msgs/msg/Header | gz.msgs.Header |
| std_msgs/msg/Int32 | gz.msgs.Int32 |
| std_msgs/msg/String | gz.msgs.StringMsg |
| std_msgs/msg/UInt32 | gz.msgs.UInt32 |
| tf2_msgs/msg/TFMessage | gz.msgs.Pose_V |
| trajectory_msgs/msg/JointTrajectory | gz.msgs.JointTrajectory |

还有一些增补的类型
| ROS 2 消息类型                                  | Gazebo Transport 类型                 |
| ------------------------------------------- | ----------------------------------- |
| actuator_msgs/msg/Actuators                 | gz.msgs.Actuators                   |
| geometry_msgs/msg/PoseWithCovarianceStamped | gz.msgs.PoseWithCovariance          |
| geometry_msgs/msg/TwistStamped              | gz.msgs.Twist                       |
| gps_msgs/msg/GPSFix                         | gz.msgs.NavSat                      |
| marine_acoustic_msgs/msg/Dvl                | gz.msgs.DVLVelocityTracking         |
| ros_gz_interfaces/msg/EntityWrench          | gz.msgs.EntityWrench                |
| ros_gz_interfaces/msg/LogicalCameraImage    | gz.msgs.LogicalCameraImage          |
| ros_gz_interfaces/msg/LogPlaybackStatistics | gz.msgs.LogPlaybackStatistics       |
| ros_gz_interfaces/msg/ParamVec              | gz.msgs.Param                       |
| ros_gz_interfaces/msg/ParamVec              | gz.msgs.Param_V                     |
| ros_gz_interfaces/msg/WorldStatistics       | gz.msgs.WorldStatistics             |
| sensor_msgs/msg/Range                       | gz.msgs.LaserScan                   |
| vision_msgs/msg/Detection2D                 | gz.msgs.AnnotatedAxisAligned2DBox   |
| vision_msgs/msg/Detection2DArray            | gz.msgs.AnnotatedAxisAligned2DBox_V |
| vision_msgs/msg/Detection3D                 | gz.msgs.AnnotatedOriented3DBox      |
| vision_msgs/msg/Detection3DArray            | gz.msgs.AnnotatedOriented3DBox_V    |


以及服务消息类型对应表：

| ROS2消息类型 | Gazebo 请求 | Gazebo 响应 |
|:---|:---|:---|
| ros_gz_interfaces/srv/ControlWorld | gz.msgs.WorldControl | gz.msgs.Boolean |

#### 与ROS2集成优化
在 **Gazebo与ROS2集成** 实现中需要在终端中使用不同的指令启动不同模块，该流程实现稍显复杂，本节将介绍如何以launch文件的方式进行优化。

**新建功能包**

请首先调用如下指令创建一个功能包：

```bash
ros2 pkg create demo_gazebo_sim
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1721.webp)

**添加目录**

在新建的功能包下添加目录： launch、rviz、world。并在CmakeLists.txt中添加如下代码：

```cmake
install(DIRECTORY rviz world launch DESTINATION share/${PROJECT_NAME})
```

launch目录用于存储launch文件，rviz目录由于存储rviz2的配置文件，而world目录则用于存储Gazebo仿真环境的相关文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1722.webp)

**rviz目录中生成rviz2的配置文件**

启动 rviz2，直接将默认配置保存至当前功能包的rviz目录，保存文件命名为sim.rviz。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1723.webp)

**复制world文件**

在ros安装路径下的worlds目录（`/opt/ros/jazzy/opt/gz_sim_vendor/share/gz/gz-sim8/worlds/`）中复制visualize_lidar.sdf文件至world目录。

如果以上路径下没有，那可能在ign(humble的)的安装路径下：

`/usr/share/ignition/ignition-gazebo6/worlds`

如果还没有的话，手动查找一下：

```bash
sudo find / -name "visualize_lidar.sdf"
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1724.webp)

**编写launch文件**

launch目录下新建launch文件gazebo_sim_demo.launch.py，并输入如下内容：

```python
import os

from ament_index_python.packages import get_package_share_directory

from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument
from launch.actions import IncludeLaunchDescription
from launch.conditions import IfCondition
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch.substitutions import LaunchConfiguration

from launch_ros.actions import Node

def generate_launch_description():

    this_pkg = get_package_share_directory('demo_gazebo_sim')
    pkg_ros_gz_sim = get_package_share_directory('ros_gz_sim')
    world_file = os.path.join(this_pkg,'world','visualize_lidar.sdf')

    gz_sim = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(pkg_ros_gz_sim, 'launch', 'gz_sim.launch.py')),
        launch_arguments={
            'gz_args': '-r ' + world_file
        }.items(),
    )

    # RViz
    rviz = Node(
       package='rviz2',
       executable='rviz2',
       arguments=['-d', os.path.join(this_pkg, 'rviz', 'sim.rviz')],
       condition=IfCondition(LaunchConfiguration('rviz'))
    )

    # Bridge
    bridge = Node(
        package='ros_gz_bridge',
        executable='parameter_bridge',
        arguments=['/model/vehicle_blue/cmd_vel@geometry_msgs/msg/Twist@gz.msgs.Twist',
                   '/model/vehicle_blue/odometry@nav_msgs/msg/Odometry@gz.msgs.Odometry',
                   '/model/vehicle_blue/tf@tf2_msgs/msg/TFMessage[gz.msgs.Pose_V',
                   ],
        parameters=[{'qos_overrides./model/vehicle_blue.subscriber.reliability': 'reliable'}],
        remappings=[
                ('/model/vehicle_blue/tf', '/tf'),
                ('/model/vehicle_blue/cmd_vel','cmd_vel')
            ],
        output='screen'
    )

    return LaunchDescription([
        gz_sim,
        DeclareLaunchArgument('rviz', default_value='true',
                              description='Open RViz.'),
        bridge,
        rviz
    ])
```

该launch文件中，启动了Ignition Gazebo仿真环境、通过ros_gz_bridge建立了仿真与ROS2的连接，并且启动了rviz2节点。其中建立连接时，实现了速度指令、里程计以及坐标变换等消息的转换。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1725.webp)

**构建**

终端中进入当前工作空间，编译功能包：

```bash
colcon build  --packages-select demo_gazebo_sim
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1726.webp)

**执行**

终端中进入当前工作空间，调用如下指令执行launch文件：

```bash
. install/setup.bash
ros2 launch demo_gazebo_sim gazebo_sim_demo.launch.py
```

新开终端，启动键盘控制节点：

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

再配置rviz2，
1. 将`Fixed Frame`设置为`vehicle_blue/odom`，
2. 添加TF插件，
3. 添加Odometry插件并将话题设置为`/model/vehicle_blue/odometry`，
当通过键盘控制发送速度指令时，仿真环境的机器人开始运动，并且在rviz2中可以回显坐标变换以及里程计等消息。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1727.webp)

#### 仿真环境创建 **SDF文件**
前面几节内容我们使用的是Ignition Gazebo内置的仿真环境，本节开始将介绍如何自行搭建仿真环境。本节案例将仿真一个长10m宽5m的矩形房间。该案例可以先启动Ignition Gazebo以拖拽的方式搭建仿真环境，然后再修改仿真环境对应的文件以调整细节。

**SDF、URDF 和 Xacro 的关系：**

*   **URDF 和 SDF 的区别：**

    *   **复杂性：** SDF 支持的功能更强大，能够描述完整的仿真环境；URDF 更适合定义机器人模型。

    *   **用途：** URDF 是 ROS 的标准；SDF 是 Gazebo 的标准。

    *   **物理引擎支持：** URDF 通过插件支持 Gazebo；SDF 原生支持 Gazebo。

    *   **格式转换：** URDF 可以转换为 SDF（通过 ROS 提供的工具`gz sdf -p`）。

*   **Xacro 的作用：**

    *   Xacro 是 URDF 的生成工具，帮助用户高效编写 URDF 文件，但它与 SDF 无直接关系。

* * *

**实践建议**

*   **在 Gazebo 仿真中：** 如果你用的是 ROS 2 和 Gazebo，可以直接使用 SDF 文件，功能更强大。

*   **在 ROS 中：** 如果主要用于机器人控制和规划，推荐使用 URDF 或由 Xacro 生成的 URDF。

*   **两者结合：** 使用 URDF 进行控制，使用 SDF 进行仿真。例如，使用 URDF 定义机器人结构后，借助 Gazebo 插件将其转换为 SDF。

**示例对比**

URDF 示例：

```xml
<robot name="example_robot">
  <link name="base_link">
    <inertial>
      <mass value="1.0" />
      <inertia ixx="1.0" ixy="0.0" ixz="0.0" iyy="1.0" iyz="0.0" izz="1.0" />
    </inertial>
  </link>
</robot>

```

Xacro 示例（生成 URDF）：

```xml
<xacro:robot xmlns:xacro="http://www.ros.org/wiki/xacro" name="example_robot">
  <xacro:macro name="base_link" params="mass">
    <link name="base_link">
      <inertial>
        <mass value="${mass}" />
        <inertia ixx="1.0" ixy="0.0" ixz="0.0" iyy="1.0" iyz="0.0" izz="1.0" />
      </inertial>
    </link>
  </xacro:macro>

  <xacro:base_link mass="1.0" />
</xacro:robot>
```

SDF 示例：

```xml
<sdf version="1.6">
  <model name="example_robot">
    <link name="base_link">
      <inertial>
        <mass>1.0</mass>
        <inertia>
          <ixx>1.0</ixx>
          <iyy>1.0</iyy>
          <izz>1.0</izz>
        </inertia>
      </inertial>
    </link>
  </model>
</sdf>

```

**1.创建sdf文件**

首先请调用指令`gz sim`启动Gazebo，选择Empty仿真环境，然后添加立方体，每一个立方体都对应一堵墙。

上下左右立方体box、box_1、box_2、box_3对应的坐标分别为(5.0,0.0,0.5)、(-5.0,0.0,0.5)、(0.0,2.5,0.5)、(0.0,-2.5,0.5)。

（以上坐标是指X，Y，Z坐标，没有旋转度）

保存文件到功能包的world目录下，保存的文件名称需要以.sdf为后缀，此处文件名为house.sdf。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1728.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1729.webp)

```xml
<sdf version='1.10'>
  <world name='empty'>
    <physics name='1ms' type='ignored'>
      <max_step_size>0.001</max_step_size>
      <real_time_factor>1</real_time_factor>
      <real_time_update_rate>1000</real_time_update_rate>
    </physics>
    <plugin name='gz::sim::systems::Physics' filename='gz-sim-physics-system'/>
    <plugin name='gz::sim::systems::UserCommands' filename='gz-sim-user-commands-system'/>
    <plugin name='gz::sim::systems::SceneBroadcaster' filename='gz-sim-scene-broadcaster-system'/>
    <plugin name='gz::sim::systems::Contact' filename='gz-sim-contact-system'/>
    <gravity>0 0 -9.8</gravity>
    <magnetic_field>6e-06 2.3e-05 -4.2e-05</magnetic_field>
    <atmosphere type='adiabatic'/>
    <scene>
      <ambient>0.4 0.4 0.4 1</ambient>
      <background>0.7 0.7 0.7 1</background>
      <shadows>true</shadows>
    </scene>
    <model name='ground_plane'>
      <static>true</static>
      <link name='link'>
        <collision name='collision'>
          <geometry>
            <plane>
              <normal>0 0 1</normal>
              <size>100 100</size>
            </plane>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='visual'>
          <geometry>
            <plane>
              <normal>0 0 1</normal>
              <size>100 100</size>
            </plane>
          </geometry>
          <material>
            <ambient>0.8 0.8 0.8 1</ambient>
            <diffuse>0.8 0.8 0.8 1</diffuse>
            <specular>0.8 0.8 0.8 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <inertial>
          <pose>0 0 0 0 -0 0</pose>
          <mass>100</mass>
          <inertia>
            <ixx>1</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>1</iyy>
            <iyz>0</iyz>
            <izz>1</izz>
          </inertia>
        </inertial>
        <enable_wind>false</enable_wind>
      </link>
      <pose>0 0 0 0 -0 0</pose>
      <self_collide>false</self_collide>
    </model>
    <model name='box'>
      <pose>5.0 0 0.5 -0 0 0</pose>
      <link name='box_link'>
        <inertial>
          <inertia>
            <ixx>16.666</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>16.666</iyy>
            <iyz>0</iyz>
            <izz>16.666</izz>
          </inertia>
          <mass>100</mass>
          <pose>0 0 0 0 -0 0</pose>
        </inertial>
        <collision name='box_collision'>
          <geometry>
            <box>
              <size>0.1 5 1</size>
            </box>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='box_visual'>
          <geometry>
            <box>
              <size>0.1 5 1</size>
            </box>
          </geometry>
          <material>
            <ambient>0.3 0.3 0.3 1</ambient>
            <diffuse>0.7 0.7 0.7 1</diffuse>
            <specular>1 1 1 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <enable_wind>false</enable_wind>
      </link>
      <static>true</static>
      <self_collide>false</self_collide>
    </model>
    <model name='box_0'>
      <pose>-5.0 -0 0.50000 -0 0 0</pose>
      <link name='box_link'>
        <inertial>
          <inertia>
            <ixx>16.666</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>16.666</iyy>
            <iyz>0</iyz>
            <izz>16.666</izz>
          </inertia>
          <mass>100</mass>
          <pose>0 0 0 0 -0 0</pose>
        </inertial>
        <collision name='box_collision'>
          <geometry>
            <box>
              <size>0.1 5 1</size>
            </box>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='box_visual'>
          <geometry>
            <box>
              <size>0.1 5 1</size>
            </box>
          </geometry>
          <material>
            <ambient>0.3 0.3 0.3 1</ambient>
            <diffuse>0.7 0.7 0.7 1</diffuse>
            <specular>1 1 1 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <enable_wind>false</enable_wind>
      </link>
      <static>true</static>
      <self_collide>false</self_collide>
    </model>
    <model name='box_1'>
      <pose>-0 -2.5 0.5 -0 -0 -0</pose>
      <link name='box_link'>
        <inertial>
          <inertia>
            <ixx>16.666</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>16.666</iyy>
            <iyz>0</iyz>
            <izz>16.666</izz>
          </inertia>
          <mass>100</mass>
          <pose>0 0 0 0 -0 0</pose>
        </inertial>
        <collision name='box_collision'>
          <geometry>
            <box>
              <size>10 0.1 1</size>
            </box>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='box_visual'>
          <geometry>
            <box>
              <size>10 0.1 1</size>
            </box>
          </geometry>
          <material>
            <ambient>0.3 0.3 0.3 1</ambient>
            <diffuse>0.7 0.7 0.7 1</diffuse>
            <specular>1 1 1 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <enable_wind>false</enable_wind>
      </link>
      <static>true</static>
      <self_collide>false</self_collide>
    </model>
    <model name='box_2'>
      <pose>-0 2.5 0.5 0 -0 -0</pose>
      <link name='box_link'>
        <inertial>
          <inertia>
            <ixx>16.666</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>16.666</iyy>
            <iyz>0</iyz>
            <izz>16.666</izz>
          </inertia>
          <mass>100</mass>
          <pose>0 0 0 0 -0 0</pose>
        </inertial>
        <collision name='box_collision'>
          <geometry>
            <box>
              <size>10 0.1 1</size>
            </box>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='box_visual'>
          <geometry>
            <box>
              <size>10 0.1 1</size>
            </box>
          </geometry>
          <material>
            <ambient>0.3 0.3 0.3 1</ambient>
            <diffuse>0.7 0.7 0.7 1</diffuse>
            <specular>1 1 1 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <enable_wind>false</enable_wind>
      </link>
      <static>true</static>
      <self_collide>false</self_collide>
    </model>
    <light name='sun' type='directional'>
      <pose>0 0 10 0 -0 0</pose>
      <cast_shadows>true</cast_shadows>
      <intensity>1</intensity>
      <direction>-0.5 0.1 -0.9</direction>
      <diffuse>0.8 0.8 0.8 1</diffuse>
      <specular>0.2 0.2 0.2 1</specular>
      <attenuation>
        <range>1000</range>
        <linear>0.01</linear>
        <constant>0.90000000000000002</constant>
        <quadratic>0.001</quadratic>
      </attenuation>
      <spot>
        <inner_angle>0</inner_angle>
        <outer_angle>0</outer_angle>
        <falloff>0</falloff>
      </spot>
    </light>
  </world>
</sdf>
```

**2.修改sdf文件**

修改sdf文件，调整立方体的尺寸，实现墙体的合围。在sdf文件中，四个立方体分别对应了四个`<model>`标签，其`name`属性分别为`box`、`box_1`、`box_2`、`box_3`，将`box`和`box_1`中的`<size>1 1 1</size>`修改为`<size>0.1 5 1</size>`，将`box_2`和`box_3`中的`<size>1 1 1</size>`修改为`<size>10 0.1 1</size>`（*注意：每个*`<model>`*标签下，都包含两个*`<size>`*标签，分别位于*`<collision>`*标签和*`<visual>`*标签下，两个*`<size>`*标签内容都需要修改*）。

修改后与的house.sdf文件内容如下：

```xml
<sdf version='1.10'>
  <world name='empty'>
    <physics name='1ms' type='ignored'>
      <max_step_size>0.001</max_step_size>
      <real_time_factor>1</real_time_factor>
      <real_time_update_rate>1000</real_time_update_rate>
    </physics>
    <plugin name='gz::sim::systems::Physics' filename='gz-sim-physics-system'/>
    <plugin name='gz::sim::systems::UserCommands' filename='gz-sim-user-commands-system'/>
    <plugin name='gz::sim::systems::SceneBroadcaster' filename='gz-sim-scene-broadcaster-system'/>
    <plugin name='gz::sim::systems::Contact' filename='gz-sim-contact-system'/>
    <gravity>0 0 -9.8</gravity>
    <magnetic_field>6e-06 2.3e-05 -4.2e-05</magnetic_field>
    <atmosphere type='adiabatic'/>
    <scene>
      <ambient>0.4 0.4 0.4 1</ambient>
      <background>0.7 0.7 0.7 1</background>
      <shadows>true</shadows>
    </scene>
    <model name='ground_plane'>
      <static>true</static>
      <link name='link'>
        <collision name='collision'>
          <geometry>
            <plane>
              <normal>0 0 1</normal>
              <size>100 100</size>
            </plane>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='visual'>
          <geometry>
            <plane>
              <normal>0 0 1</normal>
              <size>100 100</size>
            </plane>
          </geometry>
          <material>
            <ambient>0.8 0.8 0.8 1</ambient>
            <diffuse>0.8 0.8 0.8 1</diffuse>
            <specular>0.8 0.8 0.8 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <inertial>
          <pose>0 0 0 0 -0 0</pose>
          <mass>100</mass>
          <inertia>
            <ixx>1</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>1</iyy>
            <iyz>0</iyz>
            <izz>1</izz>
          </inertia>
        </inertial>
        <enable_wind>false</enable_wind>
      </link>
      <pose>0 0 0 0 -0 0</pose>
      <self_collide>false</self_collide>
    </model>
    <model name='box'>
      <pose>5.0 0 0.5 -0 0 0</pose>
      <link name='box_link'>
        <inertial>
          <inertia>
            <ixx>16.666</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>16.666</iyy>
            <iyz>0</iyz>
            <izz>16.666</izz>
          </inertia>
          <mass>100</mass>
          <pose>0 0 0 0 -0 0</pose>
        </inertial>
        <collision name='box_collision'>
          <geometry>
            <box>
              <size>0.1 5 1</size>
            </box>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='box_visual'>
          <geometry>
            <box>
              <size>0.1 5 1</size>
            </box>
          </geometry>
          <material>
            <ambient>0.3 0.3 0.3 1</ambient>
            <diffuse>0.7 0.7 0.7 1</diffuse>
            <specular>1 1 1 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <enable_wind>false</enable_wind>
      </link>
      <static>true</static>
      <self_collide>false</self_collide>
    </model>
    <model name='box_0'>
      <pose>-5.0 -0 0.50000 -0 0 0</pose>
      <link name='box_link'>
        <inertial>
          <inertia>
            <ixx>16.666</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>16.666</iyy>
            <iyz>0</iyz>
            <izz>16.666</izz>
          </inertia>
          <mass>100</mass>
          <pose>0 0 0 0 -0 0</pose>
        </inertial>
        <collision name='box_collision'>
          <geometry>
            <box>
              <size>0.1 5 1</size>
            </box>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='box_visual'>
          <geometry>
            <box>
              <size>0.1 5 1</size>
            </box>
          </geometry>
          <material>
            <ambient>0.3 0.3 0.3 1</ambient>
            <diffuse>0.7 0.7 0.7 1</diffuse>
            <specular>1 1 1 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <enable_wind>false</enable_wind>
      </link>
      <static>true</static>
      <self_collide>false</self_collide>
    </model>
    <model name='box_1'>
      <pose>-0 -2.5 0.5 -0 -0 -0</pose>
      <link name='box_link'>
        <inertial>
          <inertia>
            <ixx>16.666</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>16.666</iyy>
            <iyz>0</iyz>
            <izz>16.666</izz>
          </inertia>
          <mass>100</mass>
          <pose>0 0 0 0 -0 0</pose>
        </inertial>
        <collision name='box_collision'>
          <geometry>
            <box>
              <size>10 0.1 1</size>
            </box>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='box_visual'>
          <geometry>
            <box>
              <size>10 0.1 1</size>
            </box>
          </geometry>
          <material>
            <ambient>0.3 0.3 0.3 1</ambient>
            <diffuse>0.7 0.7 0.7 1</diffuse>
            <specular>1 1 1 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <enable_wind>false</enable_wind>
      </link>
      <static>true</static>
      <self_collide>false</self_collide>
    </model>
    <model name='box_2'>
      <pose>-0 2.5 0.5 0 -0 -0</pose>
      <link name='box_link'>
        <inertial>
          <inertia>
            <ixx>16.666</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>16.666</iyy>
            <iyz>0</iyz>
            <izz>16.666</izz>
          </inertia>
          <mass>100</mass>
          <pose>0 0 0 0 -0 0</pose>
        </inertial>
        <collision name='box_collision'>
          <geometry>
            <box>
              <size>10 0.1 1</size>
            </box>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='box_visual'>
          <geometry>
            <box>
              <size>10 0.1 1</size>
            </box>
          </geometry>
          <material>
            <ambient>0.3 0.3 0.3 1</ambient>
            <diffuse>0.7 0.7 0.7 1</diffuse>
            <specular>1 1 1 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <enable_wind>false</enable_wind>
      </link>
      <static>true</static>
      <self_collide>false</self_collide>
    </model>
    <light name='sun' type='directional'>
      <pose>0 0 10 0 -0 0</pose>
      <cast_shadows>true</cast_shadows>
      <intensity>1</intensity>
      <direction>-0.5 0.1 -0.9</direction>
      <diffuse>0.8 0.8 0.8 1</diffuse>
      <specular>0.2 0.2 0.2 1</specular>
      <attenuation>
        <range>1000</range>
        <linear>0.01</linear>
        <constant>0.90000000000000002</constant>
        <quadratic>0.001</quadratic>
      </attenuation>
      <spot>
        <inner_angle>0</inner_angle>
        <outer_angle>0</outer_angle>
        <falloff>0</falloff>
      </spot>
    </light>
  </world>
</sdf>
```

**3.编写launch文件**

在launch目录下新建launch文件gazebo_sim_world.launch.py，并输入如下内容：

```python
import os

from ament_index_python.packages import get_package_share_directory

from launch import LaunchDescription
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource

def generate_launch_description():

    this_pkg = get_package_share_directory('demo_gazebo_sim')
    pkg_ros_gz_sim = get_package_share_directory('ros_gz_sim')
    world_file = os.path.join(this_pkg,"world","house.sdf")

    gz_sim = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(pkg_ros_gz_sim, 'launch', 'gz_sim.launch.py')),
        launch_arguments={
            'gz_args': '-r ' + world_file
        }.items(),
    )
    return LaunchDescription([
        gz_sim
    ])
```

**4.构建**

终端中进入当前工作空间，编译功能包：

```bash
colcon build  --packages-select demo_gazebo_sim
```

**5.执行**

终端中进入当前工作空间，调用如下指令执行launch文件：

```bash
. install/setup.bash
ros2 launch demo_gazebo_sim gazebo_sim_world.launch.py
```

运行结果如下图所示。

也可以根据个人喜好，继续设计房间模型。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1730.webp)

#### **Gz Sim添加模型**
在Gazebo官网提供了许多仿真模型，可以自行下载并使用以优化仿真环境，使其更多样、美观且真实。

**资源下载**

仿真Gazebo的官方模型链接：

https://app.gazebosim.org/fuel/models

自行选择仿真模型点击进入详情页面，然后点击下载按钮即可将模型资源保存到本地。

在用户目录下新建`ign_models`目录，将下载的资源解压缩到该目录以作备用。
这个目录的名字随便起，你想起什么起什么，但是前提是纯英文，符合Linux命名规则，别有非法。

**资源配置**

为了可以让Gazebo识别到模型资源，下一步还需要修改用户目录下的 .bashrc 文件，添加如下代码：

```bash
# Jazzy版本的宏如下：
export GZ_SIM_RESOURCE_PATH=~/ign_models

# Humble版本一般是下面的
export IGN_GAZEBO_RESOURCE_PATH=~/ign_models
```

https://gazebosim.org/docs/latest/fuel_insert/

**模型添加**

终端下进入功能包demo_gazebo_sim的world目录，使用指令`gz sim house.sdf`启动仿真环境，点击窗口右上的折叠按钮，搜索`Resource Spawner`并打开，点击`Local resources`并选择模型拖拽至仿真环境中。将修改后的内容保存至house.sdf文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1731.webp)

正常下载资源后，这个local resources这里就会显示了

house.sdf文件示例内容如下：

```xml
<sdf version='1.10'>
  <world name='empty'>
    <physics name='1ms' type='ignored'>
      <max_step_size>0.001</max_step_size>
      <real_time_factor>1</real_time_factor>
      <real_time_update_rate>1000</real_time_update_rate>
    </physics>
    <plugin name='gz::sim::systems::Physics' filename='gz-sim-physics-system'/>
    <plugin name='gz::sim::systems::UserCommands' filename='gz-sim-user-commands-system'/>
    <plugin name='gz::sim::systems::SceneBroadcaster' filename='gz-sim-scene-broadcaster-system'/>
    <plugin name='gz::sim::systems::Contact' filename='gz-sim-contact-system'/>
    <gravity>0 0 -9.8</gravity>
    <magnetic_field>6e-06 2.3e-05 -4.2e-05</magnetic_field>
    <atmosphere type='adiabatic'/>
    <scene>
      <ambient>0.4 0.4 0.4 1</ambient>
      <background>0.7 0.7 0.7 1</background>
      <shadows>true</shadows>
    </scene>
    <model name='ground_plane'>
      <static>true</static>
      <link name='link'>
        <collision name='collision'>
          <geometry>
            <plane>
              <normal>0 0 1</normal>
              <size>100 100</size>
            </plane>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='visual'>
          <geometry>
            <plane>
              <normal>0 0 1</normal>
              <size>100 100</size>
            </plane>
          </geometry>
          <material>
            <ambient>0.8 0.8 0.8 1</ambient>
            <diffuse>0.8 0.8 0.8 1</diffuse>
            <specular>0.8 0.8 0.8 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <inertial>
          <pose>0 0 0 0 -0 0</pose>
          <mass>100</mass>
          <inertia>
            <ixx>1</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>1</iyy>
            <iyz>0</iyz>
            <izz>1</izz>
          </inertia>
        </inertial>
        <enable_wind>false</enable_wind>
      </link>
      <pose>0 0 0 0 -0 0</pose>
      <self_collide>false</self_collide>
    </model>
    <model name='box'>
      <pose>5.05017 0 0.5 -0 0 3e-05</pose>
      <link name='box_link'>
        <inertial>
          <inertia>
            <ixx>16.666</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>16.666</iyy>
            <iyz>0</iyz>
            <izz>16.666</izz>
          </inertia>
          <mass>100</mass>
          <pose>0 0 0 0 -0 0</pose>
        </inertial>
        <collision name='box_collision'>
          <geometry>
            <box>
              <size>0.1 5 1</size>
            </box>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='box_visual'>
          <geometry>
            <box>
              <size>0.1 5 1</size>
            </box>
          </geometry>
          <material>
            <ambient>0.3 0.3 0.3 1</ambient>
            <diffuse>0.7 0.7 0.7 1</diffuse>
            <specular>1 1 1 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <enable_wind>false</enable_wind>
      </link>
      <static>true</static>
      <self_collide>false</self_collide>
    </model>
    <model name='box_0'>
      <pose>-5.05003 -7.8e-05 0.5 -0 -0 1.8e-05</pose>
      <link name='box_link'>
        <inertial>
          <inertia>
            <ixx>16.666</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>16.666</iyy>
            <iyz>0</iyz>
            <izz>16.666</izz>
          </inertia>
          <mass>100</mass>
          <pose>0 0 0 0 -0 0</pose>
        </inertial>
        <collision name='box_collision'>
          <geometry>
            <box>
              <size>0.1 5 1</size>
            </box>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='box_visual'>
          <geometry>
            <box>
              <size>0.1 5 1</size>
            </box>
          </geometry>
          <material>
            <ambient>0.3 0.3 0.3 1</ambient>
            <diffuse>0.7 0.7 0.7 1</diffuse>
            <specular>1 1 1 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <enable_wind>false</enable_wind>
      </link>
      <static>true</static>
      <self_collide>false</self_collide>
    </model>
    <model name='box_1'>
      <pose>0.000162 -2.53144 0.499999 0 -0 -0.001876</pose>
      <link name='box_link'>
        <inertial>
          <inertia>
            <ixx>16.666</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>16.666</iyy>
            <iyz>0</iyz>
            <izz>16.666</izz>
          </inertia>
          <mass>100</mass>
          <pose>0 0 0 0 -0 0</pose>
        </inertial>
        <collision name='box_collision'>
          <geometry>
            <box>
              <size>10 0.1 1</size>
            </box>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='box_visual'>
          <geometry>
            <box>
              <size>10 0.1 1</size>
            </box>
          </geometry>
          <material>
            <ambient>0.3 0.3 0.3 1</ambient>
            <diffuse>0.7 0.7 0.7 1</diffuse>
            <specular>1 1 1 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <enable_wind>false</enable_wind>
      </link>
      <static>true</static>
      <self_collide>false</self_collide>
    </model>
    <model name='box_2'>
      <pose>0.00012 2.51517 0.499999 0 -0 -0.00303</pose>
      <link name='box_link'>
        <inertial>
          <inertia>
            <ixx>16.666</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>16.666</iyy>
            <iyz>0</iyz>
            <izz>16.666</izz>
          </inertia>
          <mass>100</mass>
          <pose>0 0 0 0 -0 0</pose>
        </inertial>
        <collision name='box_collision'>
          <geometry>
            <box>
              <size>10 0.1 1</size>
            </box>
          </geometry>
          <surface>
            <friction>
              <ode/>
            </friction>
            <bounce/>
            <contact/>
          </surface>
        </collision>
        <visual name='box_visual'>
          <geometry>
            <box>
              <size>10 0.1 1</size>
            </box>
          </geometry>
          <material>
            <ambient>0.3 0.3 0.3 1</ambient>
            <diffuse>0.7 0.7 0.7 1</diffuse>
            <specular>1 1 1 1</specular>
          </material>
        </visual>
        <pose>0 0 0 0 -0 0</pose>
        <enable_wind>false</enable_wind>
      </link>
      <static>true</static>
      <self_collide>false</self_collide>
    </model>
    <!-- Jazzy 迁移说明开始：下面是本机 file:// 模型目录，ign_models 只是文件夹名，不属于 Gazebo API，因此保留不改。 -->
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/bed</uri>
      <name>Bed</name>
      <pose>2.11564 -0.080355 0 0 -0 0</pose>
    </include>
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/WhiteCabinet</uri>
      <name>WhiteCabinet</name>
      <pose>-1.69505 1.99357 0 0 0 -1.59625</pose>
    </include>
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/WhiteCabinet</uri>
      <name>WhiteCabinet_1</name>
      <pose>-1.71706 1.10132 0 0 0 -1.59625</pose>
    </include>
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/WhiteCabinet</uri>
      <name>WhiteCabinet_2</name>
      <pose>-1.74096 0.206747 0 0 0 -1.59625</pose>
    </include>
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/WhiteCabinet</uri>
      <name>WhiteCabinet_3</name>
      <pose>-1.76908 -0.654114 0 0 0 -1.59625</pose>
    </include>
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/WhiteCabinet</uri>
      <name>WhiteCabinet_4</name>
      <pose>-3.54096 -1.97391 0 0 0 -1.59625</pose>
    </include>
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/WhiteCabinet</uri>
      <name>WhiteCabinet_4_1</name>
      <pose>-3.51784 -1.10026 0 0 0 -1.59625</pose>
    </include>
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/WhiteCabinet</uri>
      <name>WhiteCabinet_4_1_1</name>
      <pose>-3.44853 2.02068 0 0 0 -1.59625</pose>
    </include>
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/WhiteCabinet</uri>
      <name>WhiteCabinet_4_1_1_1</name>
      <pose>-3.499 -0.197449 0 0 0 -1.59625</pose>
    </include>
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/WhiteCabinet</uri>
      <name>WhiteCabinet_3_1</name>
      <pose>-0.465147 -0.678914 0 0 0 -1.59625</pose>
    </include>
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/WhiteCabinet</uri>
      <name>WhiteCabinet_3_1_1</name>
      <pose>-1.12325 -0.88551 0 0 0 -0.016899</pose>
    </include>
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/WhiteCabinet</uri>
      <name>WhiteCabinet_3_1_2</name>
      <pose>-0.445223 0.182127 0 0 0 -1.59625</pose>
    </include>
    <include>
      <uri>file:///home/tungchiahui/UserFolder/MySource/ROS_WS/ign_models/WhiteCabinet</uri>
      <name>WhiteCabinet_3_1_2_1</name>
      <pose>-0.422109 1.02215 0 0 0 -1.59625</pose>
    </include>
    <!-- Jazzy 迁移说明结束：如果以后重命名本机模型目录，需要同步修改这些 file:// 路径。 -->
    <light name='sun' type='directional'>
      <pose>0 0 10 0 -0 0</pose>
      <cast_shadows>true</cast_shadows>
      <intensity>1</intensity>
      <direction>-0.5 0.1 -0.9</direction>
      <diffuse>0.8 0.8 0.8 1</diffuse>
      <specular>0.2 0.2 0.2 1</specular>
      <attenuation>
        <range>1000</range>
        <linear>0.01</linear>
        <constant>0.90000000000000002</constant>
        <quadratic>0.001</quadratic>
      </attenuation>
      <spot>
        <inner_angle>0</inner_angle>
        <outer_angle>0</outer_angle>
        <falloff>0</falloff>
      </spot>
    </light>
  </world>
</sdf>

```

**构建**

终端中进入当前工作空间，编译功能包：

```bash
colcon build  --packages-select demo_gazebo_sim
```

**执行**

终端中进入当前工作空间，调用如下指令执行launch文件：

```bash
. install/setup.bash
ros2 launch demo_gazebo_sim gazebo_sim_world.launch.py
```

运行结果如下图所示。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1732.webp)


#### Gazebo添加机器人
Gazebo中可以直接创建机器人模型，或者也可以加载ROS2中URDF格式的机器人模型，此处我们使用后者（也可以选择用自己的urdf小车，但是注意修改launch的路径）。

咱们可以用之前创建的`cpp06_urdf`里的模型。

**准备机器人模型功能包**

在工作空间中输入以下命令从而去创建功能包`mycar_description`

```bash
cd ./src
ros2 pkg create mycar_description --build-type ament_cmake
cd ..
```

在功能包下创建以下文件夹`launch，urdf，rviz，meshes`

**修改以下配置文件：**

**1. package.xml：**
在 package.xml 中需要手动添加一些执行时依赖，核心内容如下：

```xml
<exec_depend>rviz2</exec_depend>
<exec_depend>xacro</exec_depend>
<exec_depend>robot_state_publisher</exec_depend>
<exec_depend>joint_state_publisher</exec_depend>
<exec_depend>ros2launch</exec_depend>
```

**2.CMakeLists.txt**
在功能包下，新建了若干目录，需要为这些目录配置安装路径，核心内容如下：

```cmake
install(
  DIRECTORY launch urdf rviz meshes
  DESTINATION share/${PROJECT_NAME}  
)
```

把之前`cpp06_urdf`功能包中的`urdf`文件夹里的内容覆盖到`mycar_description`功能包中的`urdf`文件夹中。

ign_models中新建mycar_description目录，并将功能包mycar_description下的mesh目录复制进ign_models中的mycar_description目录。（此时meshes里是空的，很正常，你后续用到其他模型的时候可能才会有`.STL`文件）


**添加Gazebo需要的sdf特有的标签**

先修改`car_base.urdf.xacro`，添加上各种惯性，碰撞，摩擦等等：

```xml
<robot xmlns:xacro="http://wiki.ros.org/xacro">
    <!-- PI 值 -->
    <xacro:property name="PI" value="3.1416"/>
    <!-- 定义车辆参数 -->
    <!-- 车体长宽高 -->
    <xacro:property name="base_link_x" value="0.2"/>
    <xacro:property name="base_link_y" value="0.12"/>
    <xacro:property name="base_link_z" value="0.07"/>
    <!-- 离地间距 -->
    <xacro:property name="distance" value="0.015"/>
    <!-- 车轮半径 宽度 -->
    <xacro:property name="wheel_radius" value="0.025"/>
    <xacro:property name="wheel_length" value="0.02"/>

    <!-- Gazebo 新增开始：Gazebo 物理参数，用于给 link 生成 inertial -->
    <xacro:property name="base_footprint_radius" value="0.001"/>
    <xacro:property name="base_footprint_mass" value="0.001"/>
    <xacro:property name="base_link_mass" value="1.0"/>
    <xacro:property name="wheel_mass" value="0.05"/>
    <xacro:property name="wheel_mu1" value="1.0"/>
    <xacro:property name="wheel_mu2" value="0.05"/>
    <!-- Gazebo 新增结束：Gazebo 物理参数 -->

    <!-- Gazebo 新增开始：球体惯性宏，给 base_footprint 使用 -->
    <xacro:macro name="sphere_inertial" params="mass radius">
        <inertial>
            <mass value="${mass}"/>
            <inertia
                ixx="${2.0 / 5.0 * mass * radius * radius}" ixy="0.0" ixz="0.0"
                iyy="${2.0 / 5.0 * mass * radius * radius}" iyz="0.0"
                izz="${2.0 / 5.0 * mass * radius * radius}"/>
        </inertial>
    </xacro:macro>
    <!-- Gazebo 新增结束：球体惯性宏 -->

    <!-- Gazebo 新增开始：长方体惯性宏，给 base_link 使用 -->
    <xacro:macro name="box_inertial" params="mass x y z">
        <inertial>
            <mass value="${mass}"/>
            <inertia
                ixx="${mass / 12.0 * (y * y + z * z)}" ixy="0.0" ixz="0.0"
                iyy="${mass / 12.0 * (x * x + z * z)}" iyz="0.0"
                izz="${mass / 12.0 * (x * x + y * y)}"/>
        </inertial>
    </xacro:macro>
    <!-- Gazebo 新增结束：长方体惯性宏 -->

    <!-- Gazebo 新增开始：圆柱车轮惯性宏，给四个轮子使用 -->
    <xacro:macro name="wheel_inertial" params="mass radius length">
        <inertial>
            <mass value="${mass}"/>
            <inertia
                ixx="${mass / 12.0 * (3.0 * radius * radius + length * length)}" ixy="0.0" ixz="0.0"
                iyy="${mass / 2.0 * radius * radius}" iyz="0.0"
                izz="${mass / 12.0 * (3.0 * radius * radius + length * length)}"/>
        </inertial>
    </xacro:macro>
    <!-- Gazebo 新增结束：圆柱车轮惯性宏 -->

    <!-- 定义颜色 -->
    <material name="yellow">
        <color rgba="0.7 0.7 0 0.8" />
    </material>
    <material name="red">
        <color rgba="0.8 0.1 0.1 0.8" />
    </material>
    <material name="gray">
        <color rgba="0.2 0.2 0.2 0.95" />
      </material>
    <!-- 定义 base_footprint -->
    <link name="base_footprint">
        <visual>
            <geometry>
                <!-- Codex 修改：把原来的固定 0.001 改成上面新增的参数 -->
                <sphere radius="${base_footprint_radius}"/>
            </geometry>
        </visual>
        <!-- Gazebo 新增：base_footprint 的惯性，避免 Gazebo 转 SDF 时丢弃根 link -->
        <xacro:sphere_inertial mass="${base_footprint_mass}" radius="${base_footprint_radius}"/>
    </link>

    <!-- 定义 base_link -->
    <link name="base_link">
        <visual>
            <!-- 形状 -->
            <geometry>
                <box size="${base_link_x} ${base_link_y} ${base_link_z}" />
            </geometry>
            <origin xyz="0 0 0" rpy="0 0 0" />
            <material name="yellow"/>
        </visual>
        <!-- Gazebo 新增开始：base_link 碰撞体，Gazebo 物理仿真需要 collision -->
        <collision>
            <geometry>
                <box size="${base_link_x} ${base_link_y} ${base_link_z}" />
            </geometry>
            <origin xyz="0 0 0" rpy="0 0 0" />
        </collision>
        <!-- Gazebo 新增结束：base_link 碰撞体 -->
        <!-- Gazebo 新增：base_link 惯性，Gazebo 物理仿真需要 inertial -->
        <xacro:box_inertial mass="${base_link_mass}" x="${base_link_x}" y="${base_link_y}" z="${base_link_z}"/>
    </link>
    <joint name="baselink2basefootprint" type="fixed">
        <parent link="base_footprint"/>
        <child link="base_link"/>
        <origin xyz="0.0 0.0 ${distance + base_link_z / 2}"/>
    </joint>
    <!-- 车轮宏定义 -->
    <xacro:macro name="wheel_func" params="wheel_name is_front is_left" >
        <link name="${wheel_name}_wheel">
            <visual>
                <geometry>
                    <cylinder radius="${wheel_radius}" length="${wheel_length}" />
                </geometry>
                <origin xyz="0 0 0" rpy="${PI / 2} 0 0" />
                <material name="gray"/>
            </visual>
            <!-- Gazebo 新增开始：车轮碰撞体，四个轮子都会通过这个宏生成 -->
            <collision>
                <geometry>
                    <cylinder radius="${wheel_radius}" length="${wheel_length}" />
                </geometry>
                <origin xyz="0 0 0" rpy="${PI / 2} 0 0" />
            </collision>
            <!-- Gazebo 新增结束：车轮碰撞体 -->
            <!-- Gazebo 新增：车轮惯性，四个轮子都会通过这个宏生成 -->
            <xacro:wheel_inertial mass="${wheel_mass}" radius="${wheel_radius}" length="${wheel_length}"/>
        </link>
        <joint name="${wheel_name}2baselink" type="continuous">
            <parent link="base_link"  />
            <child link="${wheel_name}_wheel" />
            <origin xyz="${(base_link_x / 2 - wheel_radius) * is_front} ${base_link_y / 2 * is_left} ${(base_link_z / 2 + distance - wheel_radius) * -1}" rpy="0 0 0" />
            <axis xyz="0 1 0" />
        </joint>
        <!-- Gazebo 新增：四轮差速原地转弯需要轮胎横向滑移；降低 mu2 可以减少 Gazebo 中的卡顿和抖动 -->
        <gazebo reference="${wheel_name}_wheel">
            <mu1>${wheel_mu1}</mu1>
            <mu2>${wheel_mu2}</mu2>
            <fdir1>1 0 0</fdir1>
        </gazebo>
    </xacro:macro>
    <!-- 车轮宏调用 -->
    <xacro:wheel_func wheel_name="left_front" is_front="1" is_left="1" />
    <xacro:wheel_func wheel_name="left_back" is_front="-1" is_left="1" />
    <xacro:wheel_func wheel_name="right_front" is_front="1" is_left="-1" />
    <xacro:wheel_func wheel_name="right_back" is_front="-1" is_left="-1" />
</robot>
```

修改`car.urdf.xacro`：

```xml
<robot name="car" xmlns:xacro="http://wiki.ros.org/xacro">
    <xacro:include filename="car_base.urdf.xacro"/>
    <xacro:include filename="car_camera.urdf.xacro"/>
    <xacro:include filename="car_laser.urdf.xacro"/>
</robot>
```


**机器人模型功能包下新建launch文件**

新建launch文件mycar_desc_sim.launch.py，并输入如下内容：

```python
from launch import LaunchDescription
from launch_ros.actions import Node
import os
from ament_index_python.packages import get_package_share_directory
from launch_ros.parameter_descriptions import ParameterValue
from launch.substitutions import Command,LaunchConfiguration
from launch.actions import DeclareLaunchArgument

def generate_launch_description():

    mycar_description = get_package_share_directory("mycar_description")
    default_model_path = os.path.join(mycar_description,"urdf/xacro","car.urdf.xacro")
    model = DeclareLaunchArgument(name="model", default_value=default_model_path)

    # 加载机器人模型
    # 启动 robot_state_publisher 节点并以参数方式加载 urdf 文件
    robot_description = ParameterValue(Command(["xacro ",LaunchConfiguration("model")]))
    robot_state_publisher = Node(
        package="robot_state_publisher",
        executable="robot_state_publisher",
        parameters=[{"robot_description": robot_description}]
    )

    return LaunchDescription([
        model,
        robot_state_publisher,
    ])
```

较之于以往该文件缺少了joint_state_publisher节点，该节点作用是发布活动关节状态，这一功能后续由ignition实现。

**添加机器人模型**

创建gazebo_sim_robot_world.launch.py文件，包含机器人模型的发布文件并在Gazebo中生成机器人模型，修改后的代码如下：

```python
import os

from ament_index_python.packages import get_package_share_directory

from launch import LaunchDescription
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch_ros.actions import Node

def generate_launch_description():

    this_pkg = get_package_share_directory("demo_gazebo_sim")
    mycar_desc_pkg = get_package_share_directory("mycar_description")
    pkg_ros_gz_sim = get_package_share_directory("ros_gz_sim")
    world_file = os.path.join(this_pkg,"world","house.sdf")

    gz_sim = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(pkg_ros_gz_sim, "launch", "gz_sim.launch.py")),
        launch_arguments={
            "gz_args": "-r " + world_file
        }.items(),
    )
    mycar_desc = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(mycar_desc_pkg,"launch","mycar_desc_sim.launch.py")
        )
    )
    spawn = Node(package="ros_gz_sim", executable="create",
                arguments=[
                "-name", "mycar",
                "-x", "0",
                "-z", "0.01", #设置为0,可能会陷进地里
                "-y", "0",
                "-R", "0",
                "-P", "0",
                "-Y", "1.57", # Yaw航向角逆时针旋转90度
                "-topic", "/robot_description"],
            output="screen")

    return LaunchDescription([
        gz_sim,
        spawn,
        mycar_desc,
    ])
```

**构建**

终端中进入当前工作空间，编译功能包：

```bash
colcon build --packages-select mycar_description demo_gazebo_sim
```

**执行**

终端中进入当前工作空间，调用如下指令执行launch文件：（执行起来有问题的话，你只要学过urdf怎么跑，应该拥有自我寻找错误的能力了，自己找吧）

```bash
. install/setup.bash

ros2 launch demo_gazebo_sim gazebo_sim_robot_world.launch.py
```

运行结果如下图所示。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1733.webp)


#### Gz Sim运动控制器
本节将介绍如何让你的机器人动起来。

原理就是给urdf或xacro等添加<gazebo>标签：

http://sdformat.org/tutorials?tut=sdformat_urdf_extensions&cat=specification&

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1734.webp)

https://gazebosim.org/api/plugin/2/index.html

安装库：
官方教程：
进入https://gazebosim.org/docs/harmonic/library_reference_nav/
点击插件进入https://gazebosim.org/api/plugin/2/installation.html

```bash
sudo apt-get update
sudo apt-get install lsb-release
sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list'
wget http://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -
sudo apt-get update
sudo apt install libgz-plugin2-dev
```

利用插件去让小车动，比如有两轮差速插件，四轮麦轮插件等等

同时该插件还提供了一些可以控制输出的选项，因为是仿真，所以还要告诉插件轮子对应的joint名称等信息，这样就有了下面这个参数表格：

| 配置项 | 含义 |
|:---|:---|
| ros | ros相关配置，包含命名空间和话题重映射等 |
| update_rate | 数据更新速率 |
| left_joint | 左轮关节名称 |
| right_joint | 右轮关节名称 |
| wheel_separation | 左右轮子的间距 |
| wheel_diameter | 轮子的直径 |
| max_wheel_torque | 轮子最大的力矩 |
| max_wheel_acceleration | 轮子最大的加速度 |
| publish_odom | 是否发布里程计 |
| publish_odom_tf | 是否发布里程计的tf开关 |
| publish_wheel_tf | 是否发布轮子的tf数据开关 |
| odometry_frame | 里程计的framed ID，最终体现在话题和TF上 |
| robot_base_frame | 机器人的基础frame的ID |

**修改URDF文件**

2轮差速车的话，在`<robot>`根标签下添加如下代码：

```xml

  <gazebo>
      <plugin filename="gz-sim-diff-drive-system"
        name="gz::sim::systems::DiffDrive">
        <left_joint>left_joint</left_joint>
        <right_joint>right_joint</right_joint>
        <wheel_separation>0.2097</wheel_separation>
        <wheel_radius>0.03415</wheel_radius>
        <odom_publish_frequency>10</odom_publish_frequency>
        <frame_id>odom</frame_id>
        <child_frame_id>base_footprint</child_frame_id>
        <topic>/cmd_vel</topic>
        <max_linear_acceleration>10</max_linear_acceleration>
        <min_linear_acceleration>-10</min_linear_acceleration>
        <max_angular_acceleration>10</max_angular_acceleration>
        <min_angular_acceleration>-10</min_angular_acceleration>
        <max_linear_velocity>0.5</max_linear_velocity>
        <min_linear_velocity>-0.5</min_linear_velocity>
        <max_angular_velocity>1</max_angular_velocity>
        <min_angular_velocity>-1</min_angular_velocity>
      </plugin>

  </gazebo>

  <gazebo>
    <plugin filename="gz-sim-joint-state-publisher-system"
      name="gz::sim::systems::JointStatePublisher">
    </plugin>
  </gazebo>
```

4轮差速车的话，在`<robot>`根标签下添加如下代码：

```xml

<gazebo>
    <plugin
        filename="gz-sim-diff-drive-system"
        name="gz::sim::systems::DiffDrive">
        <left_joint>left_former_joint</left_joint>
        <left_joint>left_rear_joint</left_joint>
        <right_joint>right_former_joint</right_joint>
        <right_joint>right_rear_joint</right_joint>
        <wheel_separation>0.4</wheel_separation>
        <wheel_radius>0.0415</wheel_radius>
        <odom_publish_frequency>50</odom_publish_frequency>
        <frame_id>odom</frame_id>
        <child_frame_id>base_footprint</child_frame_id>
        <topic>/cmd_vel</topic>
        <max_linear_acceleration>10</max_linear_acceleration>
        <min_linear_acceleration>-10</min_linear_acceleration>
        <max_angular_acceleration>10</max_angular_acceleration>
        <min_angular_acceleration>-10</min_angular_acceleration>
        <max_linear_velocity>0.5</max_linear_velocity>
        <min_linear_velocity>-0.5</min_linear_velocity>
        <max_angular_velocity>1</max_angular_velocity>
        <min_angular_velocity>-1</min_angular_velocity>
      </plugin>
  </gazebo>

  <gazebo>
    <plugin filename="gz-sim-joint-state-publisher-system"
      name="gz::sim::systems::JointStatePublisher">
    </plugin>
  </gazebo>
```

我们这里肯定是4轮车，我创建了一个`move_control.urdf.xacro`专门存放运动控制类代码：

```xml

<robot name="car" xmlns:xacro="http://wiki.ros.org/xacro">
    <!-- 运动控制插件 -->
    <gazebo>
        <plugin
            filename="gz-sim-diff-drive-system"
            name="gz::sim::systems::DiffDrive">
            <left_joint>left_front2baselink</left_joint>
            <left_joint>left_back2baselink</left_joint>
            <right_joint>right_front2baselink</right_joint>
            <right_joint>right_back2baselink</right_joint>
            <!-- wheel_separation轮距 是左右轮中心线之间的距离：0.06 - (-0.06) = 0.12m -->
            <wheel_separation>0.12</wheel_separation>
            <wheel_radius>0.025</wheel_radius>
            <odom_publish_frequency>50</odom_publish_frequency>
            <frame_id>odom</frame_id>
            <child_frame_id>base_footprint</child_frame_id>
            <topic>/cmd_vel</topic>
            <max_linear_acceleration>10</max_linear_acceleration>
            <min_linear_acceleration>-10</min_linear_acceleration>
            <max_angular_acceleration>10</max_angular_acceleration>
            <min_angular_acceleration>-10</min_angular_acceleration>
            <max_linear_velocity>0.5</max_linear_velocity>
            <min_linear_velocity>-0.5</min_linear_velocity>
            <max_angular_velocity>1</max_angular_velocity>
            <min_angular_velocity>-1</min_angular_velocity>
        </plugin>
    </gazebo>
    <!-- 关节状态发布 -->
    <gazebo>
        <plugin filename="gz-sim-joint-state-publisher-system"
        name="gz::sim::systems::JointStatePublisher">
        </plugin>
    </gazebo>
</robot>


```

**修改launch文件**

修改gazebo_sim_robot_world.launch.py文件，修改后的代码如下：

```python
import os

from ament_index_python.packages import get_package_share_directory

from launch import LaunchDescription
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch_ros.actions import Node

def generate_launch_description():

    this_pkg = get_package_share_directory("demo_gazebo_sim")
    mycar_desc_pkg = get_package_share_directory("mycar_description")
    pkg_ros_gz_sim = get_package_share_directory("ros_gz_sim")
    world_file = os.path.join(this_pkg,"world","house.sdf")

    gz_sim = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(pkg_ros_gz_sim, "launch", "gz_sim.launch.py")),
        launch_arguments={
            "gz_args": "-r " + world_file
        }.items(),
    )
    mycar_desc = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(mycar_desc_pkg,"launch","mycar_desc_sim.launch.py")
        )
    )
    spawn = Node(package="ros_gz_sim", executable="create",
                arguments=[
                "-name", "mycar",
                "-x", "-4",
                "-z", "0.01", #设置为0,可能会陷进地里
                "-y", "0",
                "-R", "0",
                "-P", "0",
                "-Y", "1.57",   #逆时针旋转90度
                "-topic", "/robot_description"],
            output="screen")

    # Bridge
    bridge = Node(
        package="ros_gz_bridge",
        executable="parameter_bridge",
        arguments=["/cmd_vel@geometry_msgs/msg/Twist@gz.msgs.Twist",
                   "/model/mycar/odometry@nav_msgs/msg/Odometry@gz.msgs.Odometry",
                   "/model/mycar/tf@tf2_msgs/msg/TFMessage[gz.msgs.Pose_V",
                   "/clock@rosgraph_msgs/msg/Clock[gz.msgs.Clock",
                   "/world/empty/model/mycar/joint_state@sensor_msgs/msg/JointState[gz.msgs.Model",
                   ],
        parameters=[{"qos_overrides./model/mycar.subscriber.reliability": "reliable"}],
        remappings=[
                ("/model/mycar/tf", "/tf"),
                ("/world/empty/model/mycar/joint_state","joint_states"),
                ("/model/mycar/odometry","/odom")
            ],
        output="screen"
    )

    return LaunchDescription([
        gz_sim,
        spawn,
        mycar_desc,
        bridge
    ])
```

**构建**

终端中进入当前工作空间，编译功能包：

```bash
colcon build --packages-select mycar_description demo_gazebo_sim
```

**执行**

终端中进入当前工作空间，调用如下指令执行launch文件：

```bash
. install/setup.bash
ros2 launch demo_gazebo_sim gazebo_sim_robot_world.launch.py
```

再启动键盘控制节点，就可以控制机器人运动了。

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

还可以启动rviz2，以查看里程计消息以及坐标变换。终端中进入当前工作空间，调用如下指令执行launch文件：

启动rviz2

```bash
. install/setup.bash
rviz2
```

RVIZ2软件配置如下图所示：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1735.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1736.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1737.webp)


#### Gz Sim仿真之传感器
本节将介绍如何为仿真机器人添加雷达、相机等传感器。
**添加传感器插件**

在进行传感器模拟之前，需要先添加一个名为`gz-sim-sensors-system`的插件，打开urdf文件，在`<robot>`根标签内添加如下代码：
（建议创建一个`gazebo_sensor.urdf.xacro`专门存放）

```xml

<gazebo>
    <plugin
      filename="gz-sim-sensors-system"
      name="gz::sim::systems::Sensors">
      <render_engine>ogre2</render_engine>
    </plugin>
  </gazebo>
```

gz-sim-sensors-system是Gazebo仿真环境的插件，提供传感器模型和相关功能，用于创建、模拟和测试各种传感器设备。它包含常见传感器模型，如摄像头、激光雷达等。

**添加各种传感器**

**(注意，你的模型一定要有以下几个传感器的模型)**

雷达的模型不需要collision，请删掉，否则会挡激光射出。

```xml

  <gazebo reference="laser">
    <sensor name='gpu_lidar' type='gpu_lidar'>
      <topic>scan</topic>
      <update_rate>30</update_rate>
      <lidar>
        <scan>
          <horizontal>
            <samples>640</samples>
            <resolution>1</resolution>
            <min_angle>-3.1415926</min_angle>
            <max_angle>3.1415926</max_angle>
          </horizontal>
          <vertical>
            <samples>16</samples>
            <resolution>1</resolution>
            <min_angle>-0.261799</min_angle>
            <max_angle>0.261799</max_angle>
          </vertical>
        </scan>
        <range>
          <min>0.08</min>
          <max>10.0</max>
          <resolution>0.01</resolution>
        </range>
      </lidar>
      <visualize>true</visualize>
      <gz_frame_id>laser<gz_frame_id>
      <pose relative_to="laser">0 0 0 0 0 0</pose>
    </sensor>
  </gazebo>

  <gazebo reference="camera" >
    <sensor name="cam_link" type="camera">
      <update_rate>10.0</update_rate>
      <always_on>true</always_on>
      <gz_frame_id>camera</gz_frame_id>
      <pose relative_to="camera">0 0 0 0 0 0</pose>
      <pose>0 0 0 0 0 0</pose>
      <topic>/image_raw</topic>
      <camera name="my_camera">
        <horizontal_fov>1.3962634</horizontal_fov>
        <image>
           <width>600</width>
           <height>600</height>
           <format>R8G8B8</format>
        </image>
        <clip>
          <near>0.02</near>
          <far>300</far>
        </clip>
      </camera>
    </sensor>
  </gazebo>

  <gazebo reference="camera">
    <sensor name="depth_camera" type="depth_camera">
          <update_rate>10</update_rate>
          <topic>depth_camera</topic>
          <camera>
            <horizontal_fov>1.05</horizontal_fov>
            <image>
              <width>256</width>
              <height>256</height>
              <format>R_FLOAT32</format>
            </image>
            <clip>
              <near>0.1</near>
              <far>10.0</far>
            </clip>
          </camera>
          <always_on>1</always_on>
          <gz_frame_id>camera</gz_frame_id>
          <pose relative_to="camera">0 0 0 0 0 0</pose>
      </sensor>
  </gazebo>
```

从官网找到的imu传感器的

```xml

    <gazebo>
        <plugin filename="gz-sim-imu-system"
                name="gz::sim::systems::Imu">
        </plugin>
    </gazebo>

    <gazebo reference="base_link">
        <sensor name="imu_sensor" type="imu">
            <always_on>1</always_on>
            <update_rate>30</update_rate>
            <visualize>true</visualize>
            <topic>imu</topic>
        </sensor>
    </gazebo>
```

可以用`ign topic -e -t /imu`测试gazebo是否发布了话题，后面再用gazebo_bridge把话题给ROS2就行了。

默认情况下，rviz2没有显示imu消息的插件，需要自行安装相关插件，具体安装指令如下：

```cpp
sudo apt install ros-${ROS_DISTRO}-imu-tools

sudo apt install ros-jazzy-imu-tools
```

SolidWorks自动生成的模型可能翻转了laser_joint,请你修改回正，这样可能rivz2就有激光了，然后修改一下可视化的模型，让模型正常，不要给碰撞，不然可能会遮挡激光。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1738.webp)

修改gazebo_sim_robot_world.launch.py文件，修改后的代码如下：

```python
import os

from ament_index_python.packages import get_package_share_directory

from launch import LaunchDescription
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch_ros.actions import Node

def generate_launch_description():

    this_pkg = get_package_share_directory("demo_gazebo_sim")
    mycar_desc_pkg = get_package_share_directory("mycar_description")
    pkg_ros_gz_sim = get_package_share_directory("ros_gz_sim")
    world_file = os.path.join(this_pkg,"world","house.sdf")

    gz_sim = IncludeLaunchDescription(
    PythonLaunchDescriptionSource(
        os.path.join(pkg_ros_gz_sim, "launch", "gz_sim.launch.py")),
        launch_arguments={
        "gz_args": "-r " + world_file
        }.items(),
    )
    mycar_desc = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(mycar_desc_pkg,"launch","mycar_desc_sim.launch.py")
        )
    )
    spawn = Node(package="ros_gz_sim", executable="create",
        arguments=[
            "-name", "mycar",
            "-x", "-4",
            "-z", "0.01", #设置为0,可能会陷进地里
            "-y", "0",
            "-R", "0",
            "-P", "0",
            "-Y", "1.57",   #逆时针旋转90度
            "-topic", "/robot_description"],
        output="screen")

    # Bridge
    bridge = Node(
        package="ros_gz_bridge",
        executable="parameter_bridge",
        arguments=["/cmd_vel@geometry_msgs/msg/Twist@gz.msgs.Twist",
            "/model/mycar/odometry@nav_msgs/msg/Odometry@gz.msgs.Odometry",
            "/model/mycar/tf@tf2_msgs/msg/TFMessage[gz.msgs.Pose_V",
            "/clock@rosgraph_msgs/msg/Clock[gz.msgs.Clock",
            "/world/empty/model/mycar/joint_state@sensor_msgs/msg/JointState[gz.msgs.Model",
            "/scan@sensor_msgs/msg/LaserScan@gz.msgs.LaserScan",
            "/scan/points@sensor_msgs/msg/PointCloud2@gz.msgs.PointCloudPacked",
            "/image_raw@sensor_msgs/msg/Image@gz.msgs.Image",
            "/camera_info@sensor_msgs/msg/CameraInfo@gz.msgs.CameraInfo",
            "/depth_camera@sensor_msgs/msg/Image@gz.msgs.Image",
            "/imu@sensor_msgs/msg/Imu[gz.msgs.IMU",
            "/imu/angular_velocity@geometry_msgs/msg/Vector3[gz.msgs.Vector3d"
        ],
        parameters=[{"qos_overrides./model/mycar.subscriber.reliability": "reliable"}],
        remappings=[
            ("/model/mycar/tf", "/tf"),
            ("/world/empty/model/mycar/joint_state","joint_states"),
            ("/model/mycar/odometry","/odom")
        ],
        output="screen"
    )

    return LaunchDescription([
        gz_sim,
        spawn,
        mycar_desc,
        bridge
    ])
```

**构建**

终端中进入当前工作空间，编译功能包：

```bash
colcon build --packages-select mycar_description demo_gazebo_sim
```

**执行**

终端中进入当前工作空间，调用如下指令执行launch文件：

```bash
. install/setup.bash
ros2 launch demo_gazebo_sim gazebo_sim_robot_world.launch.py
```

这里`gz_frame_id`可能会被sdf1.10规范误报警告，不用管这个警告。实际上，这个版本的Gazebo会读取这个自定义标签`gz_frame_id`。
在sdf1.12上貌似被改成了`frame_id`。

你可以通过`ros2 topic echo /scan --once | grep frame_id`来查看scan的frame_id是否被修改成功了，修改成功会变成`laser`，否则会变成gazebo默认的`mycar/base_footprint/gpu_lidar`。

问题解决参考：https://github.com/gazebosim/gz-sensors/issues/306

再启动键盘控制节点，就可以控制机器人运动了。

还可以启动rviz2，以查看机器人发布的诸多数据。终端中进入当前工作空间，调用如下指令执行launch文件：

```bash
. install/setup.bash
rviz2
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1739.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1740.webp)


(把上面的全部复现，才能够进行下一章导航，下一章导航依然基于仿真)

