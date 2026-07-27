---
title: "Ignition Gazebo（Gazebo Fortress）"
---


### Ignition Gazebo（Gazebo Fortress，基于ROS2 Humble）

** 建议使用ROS2 Jazzy的，这个ROS2 Humble的Gazebo像过渡版本，代码可能和后续版本又冲突！ **
(该版本教程已经停更，以后会专注于更新ROS2 Jazzy及之后的版本，也就是更新Gazebo Harmonic及之后的版本。)

详见<NuxtLink to="/wiki/2023-12-30-ros2-tutorial/1100-0300-gazebosim#Gz Sim（Gazebo Harmonic 及之后的版本（ROS2 Jazzy及之后的版本））">Gz Sim教程</NuxtLink>

#### Ign Gazebo安装与运行
Gazebo每个版本的变化都很大。

特别是ROS1用的老版Gazebo(黑色界面)和ROS2用的新版Gazebo(白色界面)。

ROS2的不同版本的Gazebo跨度也很大，比如Humble和Jazzy及Jazzy之后的版本之间很多标签区别很大。

本文使用humble版本(即Ignition Gazebo)当做教程。

当然，为了兼容以后的Gazebo，在下方也会有教程教你如何从ign gazebo迁移到gazebo sim（最最最新版gazebo）。

Ignition Gazebo 是 ROS2 中使用的全新机器人仿真工具，**它是 Gazebo 的升级版本**。在Humble他还叫Ignition Gazebo(也叫Gazebo Fortress)，在Jazzy中叫Gazebo Harmonic(去掉了Ignition的名字)(https://community.gazebosim.org/t/a-new-era-for-gazebo/1356)。它具备更好的性能和可用性，并通过紧密集成 ROS2 来提供强大的仿真环境。Ignition Gazebo 支持各种机器人平台和传感器，并提供灵活的配置选项和易于使用的界面。它的物理引擎和传感器模型可以帮助开发人员进行机器人系统的开发、测试和验证。无论是研究还是教育，Ignition Gazebo 都是一个强大的工具。

如果想从Ignition Gazebo(ROS2 Humble)迁移到Gazebo(ROS2 Jazzy)，请往下翻翻，下面有一节是讲如何迁移的。

https://docs.ros.org/en/humble/Tutorials/Advanced/Simulators/Gazebo/Gazebo.html

下面这个网站是官方教程(ROS2 Humble的Ignition Gazebo Fortress)：

https://gazebosim.org/docs/fortress/getstarted/

https://gazebosim.org/docs/fortress/library_reference_nav/

源码：https://github.com/gazebosim/docs/blob/master/fortress/tutorials

**安装**

Ignition Gazebo 是不依赖于ROS2的一个独立的项目，可以独自安装。但是如果安装了ROS2，在ROS2存储库中已经集成了对应版本的 Ignition Gazebo，可以调用如下指令直接安装：

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

Ignition Gazebo 安装完毕之后，可以通过两种方式启动。

方式1，以Ignition Gazebo 的方式启动，指令如下：

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

接下来以Empty仿真环境为例，介绍一下Ignition Gazebo的界面组成。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1707.webp)

注意：如果你的Gazebo不卡，但是Ignition Gazebo巨卡的话，请确认Ignition Gazebo是以独显打开的，而不是核显。

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

在Ignition Gazebo中内置了许多插件，可以点击工具栏的右侧按钮自行添加，比如：可以选择 Grid Config 插件调整世界网格的特征，包括单元格大小、网格位置、单元格计数、或颜色等。

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
ros2 launch ros_gz_sim gz_sim.launch.py gz_args:="-v 4 -r visualize_lidar.sdf" # 启动状态
```

两种方式本质相同，都是启动了Ignition Gazebo并且加载了visualize_lidar.sdf文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1719.webp)

**建立连接**

虽然仿真环境中的机器人已经配置了运动控制插件，可以通过`/model/vehicle_blue/cmd_vel`话题订阅速度指令并运动，但是Ignition Gazebo与ROS2中的消息格式并不一致，所以还需要通过ros_gz_bridge这一桥接功能包，实现二者之间消息的转换，调用指令如下：

```bash
ros2 run ros_gz_bridge parameter_bridge /model/vehicle_blue/cmd_vel@geometry_msgs/msg/Twist]gz.msgs.Twist
```

通过该指令可以将发布在`/model/vehicle_blue/cmd_vel`话题上的`geometry_msgs/msg/Twist`类型的ROS2消息转换成可以被Ignition Gzebo识别的`gz.msgs.Twist`类型的消息。

**启动ROS2节点**

启动ROS2的键盘控制节点，并将话题重映射为`/model/vehicle_blue/cmd_vel`，指令如下：

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=/model/vehicle_blue/cmd_vel
```

接下来就可以使用键盘控制机器人运动了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1720.webp)

#### ros_gz_bridge
ros_gz_bridge是连接ROS2与Ignition Gazebo的桥梁，ROS2与Ignition Gazebo使用的消息并不兼容，必须通过ros_gz_bridge进行转换。

**ros_gz_bridge使用语法**

ROS2与Ignition Gazebo的桥接是通过ros_gz_bridge包中的parameter_bridge节点实现，其使用语法如下：

```bash
parameter_bridge [<topic@ROS2_type@Gz_type> ..]  [<service@ROS2_srv_type[@Gz_req_type@Gz_rep_type]> ..]
```

在话题Topic中， **第一个@** 符号是话题名称和消息类型的 **分隔符** 。

第一个@符号后面是ROS消息类型。

ROS消息类型后面是@、\[或\]符号：

*   **@**  表示双向桥接；

*   **\[**  表示从Ignition Gazebo到ROS的桥接；

*   **\]**  表示从ROS到Ignition Gazebo的桥接。

方向符号后是Gazebo Transport消息类型。

（两个@不是同一个含义）

在服务Service中， **第一个@** 符号是服务名称和类型的 **分隔符** 。

第一个@符号后面是ROS服务类型。可以选择地包括Gazebo请求和响应类型，在它们之间用@符号分隔。

**仅** 支持将Gazebo服务公开为ROS服务，即ROS服务将请求转发到Gazebo服务，然后将响应转发回ROS客户端。

双向桥接示例：

```bash
parameter_bridge /chatter@std_msgs/String@gz.msgs.StringMsg
```

从Gazebo到ROS的桥接示例：

```bash
parameter_bridge /chatter@std_msgs/String[gz.msgs.StringMsg
```

从ROS到Gazebo的桥接示例：

```bash
parameter_bridge /chatter@std_msgs/String]gz.msgs.StringMsg
```

服务桥接示例：

```bash
parameter_bridge /world/default/control@ros_gz_interfaces/srv/ControlWorld
或者：
parameter_bridge /world/default/control@ros_gz_interfaces/srv/ControlWorld@gz.msgs.WorldControl@gz.msgs.Boolean
```

也可以运行`ros2 run ros_gz_bridge parameter_bridge -h`指令查看官方说明文档。

**ros_gz_bridge支持的消息类型**

以下是ROS2与Ignition Gazebo中话题消息类型对应表：

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

以及服务消息类型对应表：

| ROS2消息类型 | Gazebo 请求 | Gazebo 响应 |
|:---|:---|:---|
| ros_gz_interfaces/srv/ControlWorld | gz.msgs.WorldControl | gz.msgs.Boolean |

#### 与ROS2集成优化
在 **Ignition Gazebo与ROS2集成** 实现中需要在终端中使用不同的指令启动不同模块，该流程实现稍显复杂，本节将介绍如何以launch文件的方式进行优化。

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

launch目录用于存储launch文件，rviz目录由于存储rviz2的配置文件，而world目录则用于存储Ignition Gazebo仿真环境的相关文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1722.webp)

**rviz目录中生成rviz2的配置文件**

启动 rviz2，直接将默认配置保存至当前功能包的rviz目录，保存文件命名为sim.rviz。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1723.webp)

**复制world文件**

在ignition安装路径下的worlds目录（`/usr/share/ignition/ignition-gazebo6/worlds`）中复制visualize_lidar.sdf文件至world目录。

如果该路径下没有，那可能在ROS的安装路径下：

`/opt/ros/jazzy/opt/gz_sim_vendor/share/gz/gz-sim8/worlds/`

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

再配置rviz2，将`Fixed Frame`设置为`vehicle_blue/odom`，添加TF插件，添加Odometry插件并将话题设置为`/model/vehicle_blue/odometry`，当通过键盘控制发送速度指令时，仿真环境的机器人开始运动，并且在rviz2中可以回显坐标变换以及里程计等消息。

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

首先请调用指令`ign gazebo`启动Gazebo，选择Empty仿真环境，然后添加立方体，每一个立方体都对应一堵墙。

上下左右立方体box、box_1、box_2、box_3对应的坐标分别为(5.0,0.0,0.5)、(-5.0,0.0,0.5)、(0.0,2.5,0.5)、(0.0,-2.5,0.5)。

（以上坐标是指X，Y，Z坐标，没有旋转度）

保存文件到功能包的world目录下，保存的文件名称需要以.sdf为后缀，此处文件名为house.sdf。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1728.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1729.webp)

```xml
<sdf version='1.9'>
  <world name='empty'>
    <physics name='1ms' type='ignored'>
      <max_step_size>0.001</max_step_size>
      <real_time_factor>1</real_time_factor>
      <real_time_update_rate>1000</real_time_update_rate>
    </physics>
    <plugin name='gz::sim::systems::Physics' filename='ignition-gazebo-physics-system'/>
    <plugin name='gz::sim::systems::UserCommands' filename='ignition-gazebo-user-commands-system'/>
    <plugin name='gz::sim::systems::SceneBroadcaster' filename='ignition-gazebo-scene-broadcaster-system'/>
    <plugin name='gz::sim::systems::Contact' filename='ignition-gazebo-contact-system'/>
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
      <static>false</static>
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
      <static>false</static>
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
      <static>false</static>
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
      <static>false</static>
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
<sdf version='1.9'>
  <world name='empty'>
    <physics name='1ms' type='ignored'>
      <max_step_size>0.001</max_step_size>
      <real_time_factor>1</real_time_factor>
      <real_time_update_rate>1000</real_time_update_rate>
    </physics>
    <plugin name='ign::gazebo::systems::Physics' filename='ignition-gazebo-physics-system'/>
    <plugin name='ign::gazebo::systems::UserCommands' filename='ignition-gazebo-user-commands-system'/>
    <plugin name='ign::gazebo::systems::SceneBroadcaster' filename='ignition-gazebo-scene-broadcaster-system'/>
    <plugin name='ign::gazebo::systems::Contact' filename='ignition-gazebo-contact-system'/>
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
      <static>false</static>
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
      <static>false</static>
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
      <static>false</static>
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
      <static>false</static>
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

#### **IgnG添加模型**
在Ignition Gazebo官网提供了许多仿真模型，可以自行下载并使用以优化仿真环境，使其更多样、美观且真实。

**资源下载**

仿真Ignition Gazebo的官方模型链接：

http://app.ignitionrobotics.org/fuel/models

自行选择仿真模型点击进入详情页面，然后点击下载按钮即可将模型资源保存到本地。

在用户目录下新建ign_models目录，将下载的资源解压缩到该目录以作备用。

**资源配置**

为了可以让Ignition Gazebo识别到模型资源，下一步还需要修改用户目录下的 .bashrc 文件，添加如下代码：

```bash

# Humble版本一般是下面的，但是有可能会更新，如果不生效，请尝试Jazzy的宏
export IGN_GAZEBO_RESOURCE_PATH=~/ign_models

# Jazzy版本的宏改了，如下：
export GZ_SIM_RESOURCE_PATH=~/ign_models
```

https://gazebosim.org/docs/latest/fuel_insert/

**模型添加**

终端下进入功能包demo_gazebo_sim的world目录，使用指令`ign gazebo house.sdf` 或者`gz sim house.sdf`启动仿真环境，点击窗口右上的折叠按钮，搜索`Resource Spawner`并打开，点击`Local resources`并选择模型拖拽至仿真环境中。将修改后的内容保存至house.sdf文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1731.webp)

正常下载资源后，这个local resources这里就会显示了

house.sdf文件示例内容如下：

```xml
<sdf version='1.9'>
  <world name='empty'>
    <physics name='1ms' type='ignored'>
      <max_step_size>0.001</max_step_size>
      <real_time_factor>1</real_time_factor>
      <real_time_update_rate>1000</real_time_update_rate>
    </physics>
    <plugin name='gz::sim::systems::Physics' filename='ignition-gazebo-physics-system'/>
    <plugin name='gz::sim::systems::UserCommands' filename='ignition-gazebo-user-commands-system'/>
    <plugin name='gz::sim::systems::SceneBroadcaster' filename='ignition-gazebo-scene-broadcaster-system'/>
    <plugin name='gz::sim::systems::Contact' filename='ignition-gazebo-contact-system'/>
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
      <pose>5.02632 -2e-06 0.500002 -0 4.4e-05 4.6e-05</pose>
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
      <static>false</static>
      <self_collide>false</self_collide>
    </model>
    <model name='box_0'>
      <pose>-5.01336 -0.00029 0.500002 0 -4.2e-05 -0.005335</pose>
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
      <static>false</static>
      <self_collide>false</self_collide>
    </model>
    <model name='box_1'>
      <pose>-0 -2.5 0.5 1e-06 0 0</pose>
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
      <static>false</static>
      <self_collide>false</self_collide>
    </model>
    <model name='box_2'>
      <pose>-0.000154 2.52488 0.500821 -0.018068 -0 -0.003156</pose>
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
      <static>false</static>
      <self_collide>false</self_collide>
    </model>
    <include>
      <uri>file://Bed</uri>
      <name>Bed</name>
      <pose>2.82155 1.18752 0 0 -0 0</pose>
    </include>
    <include>
      <uri>file://Office Desk</uri>
      <name>Desk</name>
      <pose>2.78306 -1.97796 0 0 -0 1.57</pose>
    </include>
    <include>
      <uri>file://Bathtub</uri>
      <name>Bathtub</name>
      <pose>-3.87509 1.82783 0 0 -0 0</pose>
    </include>
    <include>
      <uri>file://Vanity</uri>
      <name>Vanity</name>
      <pose>-2.5974 1.85613 -0.010992 0.021648 0 -1.57</pose>
    </include>
    <include>
      <uri>file://Vanity</uri>
      <name>Vanity_1</name>
      <pose>-2.5974 0.634325 -0.010992 0.021648 0 -1.57</pose>
    </include>
    <include>
      <uri>file://Dining Table</uri>
      <name>DiningTable</name>
      <pose>-0.374337 1.33602 0 0 0 -1.57</pose>
    </include>
    <include>
      <uri>file://Chair</uri>
      <name>Chair</name>
      <pose>2.79762 -1.26474 -0 -0 0 -2.3062</pose>
    </include>
    <include>
      <uri>file://Sofa</uri>
      <name>Sofa</name>
      <pose>-0.546136 -1.92328 0.000119 -0 0 1.57</pose>
    </include>
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

#### IgnG添加机器人
Ignition Gazebo中可以直接创建机器人模型，或者也可以加载ROS2中URDF格式的机器人模型，此处我们使用后者（也可以选择用自己的urdf小车，但是注意修改launch的路径）。

我没有用赵虚左老师的mycar_description，用的自己的小车模型，后面的一系列源码都在下方这个Github仓库中，需要的可以自行clone.

https://github.com/tungchiahui/ROS2_WS/tree/main/6.ws_simulations

**准备机器人模型功能包**

复制机器人描述功能包mycar_description到工作空间的src目录，ign_models中新建mycar_description目录，并将功能包mycar_description下的mesh目录复制进ign_models中的mycar_description目录。

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

#示例： ros2 launch cpp06_urdf display.launch.py model:=`ros2 pkg prefix --share cpp06_urdf`/urdf/urdf/demo01_helloworld.urdf
def generate_launch_description():

    MYCAR_MODEL = os.environ['MYCAR_MODEL']

    mycar_description = get_package_share_directory("mycar_description")
    default_model_path = os.path.join(mycar_description,"urdf",MYCAR_MODEL + ".urdf")
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

修改gazebo_sim_world.launch.py文件，包含机器人模型的发布文件并在Gazebo中生成机器人模型，修改后的代码如下：

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

# MYCAR_MODEL值可以设置为arduino、stm32_2w 或stm32_4w（这个是具体的urdf文件名，在mycar_description包下的）
export MYCAR_MODEL=stm32_4w
ros2 launch demo_gazebo_sim gazebo_sim_world.launch.py
```

运行结果如下图所示。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1733.webp)

####    IgnG运动控制器(实质上就是<gazebo>标签)
本节将介绍如何让你的机器人动起来。

原理就是给urdf或xacro等添加<gazebo>标签：

http://sdformat.org/tutorials?tut=sdformat_urdf_extensions&cat=specification&

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1734.webp)

https://gazebosim.org/api/plugin/2/index.html

安装库：

https://gazebosim.org/api/plugin/2/installation.html

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

arduino.urdf 和 stm32_2w.urdf 文件中，在`<robot>`根标签下添加如下代码：

```xml

  <gazebo>
      <plugin filename="libignition-gazebo-diff-drive-system.so"
        name="ignition::gazebo::systems::DiffDrive">
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
    <plugin filename="ignition-gazebo-joint-state-publisher-system"
      name="ignition::gazebo::systems::JointStatePublisher">
    </plugin>
  </gazebo>
```

stm32_4w.urdf 文件中，在`<robot>`根标签下添加如下代码：

```xml

<gazebo>
    <plugin
        filename="ignition-gazebo-diff-drive-system"
        name="ignition::gazebo::systems::DiffDrive">
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
    <plugin filename="ignition-gazebo-joint-state-publisher-system"
      name="ignition::gazebo::systems::JointStatePublisher">
    </plugin>
  </gazebo>
```

如果是麦轮（ROS1的，ROS2的待更新）（把轮子关节设置为自己轮子关节名即可）：

```xml
<robot name="mycar" xmlns:xacro="http://wiki.ros.org/xacro">

    <xacro:macro name="joint_trans" params="joint_name">

        <transmission name="${joint_name}_trans">
            <type>transmission_interface/SimpleTransmission</type>
            <joint name="${joint_name}">
                <hardwareInterface>hardware_interface/VelocityJointInterface</hardwareInterface>
            </joint>
            <actuator name="${joint_name}_motor">
                <hardwareInterface>hardware_interface/VelocityJointInterface</hardwareInterface>
                <mechanicalReduction>1</mechanicalReduction>
            </actuator>
        </transmission>
    </xacro:macro>

    <xacro:joint_trans joint_name="LeftFrontwheelToBase" />
    <xacro:joint_trans joint_name="LeftBackwheelToBase" />
    <xacro:joint_trans joint_name="RightFrontwheelToBase" />
    <xacro:joint_trans joint_name="RightBackwheelToBase" />

    <gazebo>
        <plugin name="mecanum_controller" filename="libgazebo_ros_planar_move.so">
            <commandTopic>cmd_vel</commandTopic>
            <odometryTopic>odom</odometryTopic>
            <odometryFrame>odom</odometryFrame>
            <leftFrontJoint>LeftFrontwheelToBase</leftFrontJoint>
            <rightFrontJoint>RightFrontwheelToBase</rightFrontJoint>
            <leftRearJoint>LeftBackwheelToBase</leftRearJoint>
            <rightRearJoint>RightBackwheelToBase</rightRearJoint>
            <odometryRate>100</odometryRate>
            <robotBaseFrame>base_footprint</robotBaseFrame>
            <broadcastTF>1</broadcastTF>
        </plugin>
    </gazebo>

</robot>
```

**修改launch文件**

修改gazebo_sim_world.launch.py文件，修改后的代码如下：

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
    world_file = os.path.join(this_pkg,"world","base.sdf")

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
export MYCAR_MODEL=stm32_4w # MYCAR_MODEL值可以设置为arduino、stm32_2w 或stm32_4w（这个是具体的urdf文件名，在mycar_description包下的）
ros2 launch demo_gazebo_sim gazebo_sim_world.launch.py
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

#### Ignition Gazebo仿真之传感器
本节将介绍如何为仿真机器人添加雷达、相机等传感器。本节代码部分内容对于我们教程中涉及到的arduino、stm32_2w以及stm32_4w等机器人模型而言是完全通用的。

**添加传感器插件**

在进行传感器模拟之前，需要先添加一个名为`ignition-gazebo-sensors-system`的插件，打开urdf文件，在`<robot>`根标签内添加如下代码：

```xml

<gazebo>
    <plugin
      filename="ignition-gazebo-sensors-system"
      name="ignition::gazebo::systems::Sensors">
      <render_engine>ogre2</render_engine>
    </plugin>
  </gazebo>
```

ignition-gazebo-sensors-system是Ignition Gazebo仿真环境的插件，提供传感器模型和相关功能，用于创建、模拟和测试各种传感器设备。它包含常见传感器模型，如摄像头、激光雷达等。

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
      <alwaysOn>1</alwaysOn>
      <visualize>true</visualize>
      <ignition_frame_id>laser</ignition_frame_id>
    </sensor>
  </gazebo>

  <gazebo reference="camera" >
    <sensor name="cam_link" type="camera">
      <update_rate>10.0</update_rate>
      <always_on>true</always_on>
      <ignition_frame_id>camera</ignition_frame_id>
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
          <alwaysOn>1</alwaysOn>
          <ignition_frame_id>camera</ignition_frame_id>
      </sensor>
  </gazebo>
```

从官网找到的imu传感器的

```xml

    <gazebo>
        <plugin filename="libignition-gazebo-imu-system.so"
                name="ignition::gazebo::systems::Imu">
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
```

SolidWorks自动生成的模型可能翻转了laser_joint,请你修改回正，这样可能rivz2就有激光了，然后修改一下可视化的模型，让模型正常，不要给碰撞，不然可能会遮挡激光。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1738.webp)

修改gazebo_sim_world.launch.py文件，修改后的代码如下：

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
export MYCAR_MODEL=stm32_4w # MYCAR_MODEL值可以设置为arduino、stm32_2w 或stm32_4w
ros2 launch demo_gazebo_sim gazebo_sim_world.launch.py
```

再启动键盘控制节点，就可以控制机器人运动了。

还可以启动rviz2，以查看机器人发布的诸多数据。终端中进入当前工作空间，调用如下指令执行launch文件：

```bash
. install/setup.bash
rviz2
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1739.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1740.webp)

我没有用赵虚左老师的mycar_description，用的自己的小车模型，后面的一系列源码都在下方这个Github仓库中，需要的可以自行clone.

https://github.com/tungchiahui/ROS2_WS/tree/main/6.ws_simulations

(把上面的全部复现，才能够进行下一章导航，下一章导航依然基于仿真)
