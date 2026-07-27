---
title: "Jazzy导航仿真"
---

### 导航概述
#### 导航简述
**概念**

机器人导航是指在没有人为干预的情况下，机器人可以自主地从一个位置移动到另一个位置。在ROS2中，导航实现最为常用的框架是Nav2。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1745.webp)

Nav2也即Navigation2，它继承自ROS的导航堆栈。它采用了与自动驾驶车辆相同的前沿技术，并经过优化和改造，专门适用于移动机器人和地面机器人的导航需求。这个项目赋予移动机器人穿越复杂环境的能力，使其能够完成用户定义的各种应用任务，几乎适用于任何类型的机器人动力学。Nav2不仅支持机器人从A点到B点的移动，还能设置中间姿态，并执行多种任务，如物体跟踪、全面覆盖导航等。作为全球100多家公司信赖的生产级高质量导航框架，Nav2以其卓越的可靠性和稳定性赢得了广泛赞誉。

**作用**

Nav2在机器人导航领域作用显著，主要表现在以下几个方面。

*   Nav2具备多样的导航功能，支持激光雷达、摄像头等多种传感器输入，实时获取环境信息，实现智能路径规划和精确运动控制。它支持多种导航模式，满足不同应用需求，并具备强大的扩展性，可与ROS 2组件无缝集成。

*   Nav2性能卓越，采用高效算法和数据处理技术，确保机器人在导航过程中响应迅速且稳定。

*   安全方面，Nav2采用多种避障算法和参数调节功能，设定安全区域，提供诊断监控，确保机器人安全导航。

总之，Nav2为机器人的导航和应用提供了强大的支持。无论是工业领域的自动化生产，还是服务领域的智能机器人，Nav2都能够发挥重要作用，提升机器人的自主性和智能化水平。

#### 导航安装
借助于Ubuntu的包资源管理器，可以使用二进制的方式安装Nav2，安装指令如下：

```bash
sudo apt install ros-<ros2-distro>-navigation2
sudo apt install ros-<ros2-distro>-nav2-bringup

# Jazzy
sudo apt install ros-jazzy-navigation2
sudo apt install ros-jazzy-nav2-bringup

# Jazzy
sudo apt install ros-jazzy-navigation2
sudo apt install ros-jazzy-nav2-bringup
```

指令中的`<ros2-distro>`请替换成当前所使用的ROS2版本。

#### 导航条件
深入学习Nav2，需了解ROS2机器人基础知识，并配备仿真或实体机器人作为实践环境。

**ROS2基础**

*   **ROS2通信** ：了解ROS2中的节点（Nodes）、话题（Topics）、服务（Services）、动作（Actions）等基本概念，以及如何通过这些机制实现节点间的通信。

*   **生命周期管理** ：熟悉ROS2中的生命周期节点（Lifecycle Nodes），理解节点的启动、配置、激活、去激活、清理等状态转换过程，这对于管理复杂的ROS2系统至关重要。

*   **rviz2：** 熟练使用rviz2进行可视化调试，包括如何设置不同的显示类型（如点云、地图、路径等），以及如何通过rviz2与ROS2节点进行交互（如设置导航目标点、观察机器人状态等）。

*   **URDF：** 了解URDF（Unified Robot Description Format）文件，这是ROS中用于描述机器人模型的一种XML格式。熟悉如何编写和修改URDF文件，以便在仿真或实际环境中准确地表示机器人结构。

*   **TF坐标变换：** 掌握TF（Transform）库的使用，理解坐标变换在机器人导航中的重要性。学会如何设置和查询不同坐标系之间的变换关系，以确保机器人能够准确地定位自身和周围环境。

实践环境

*   **仿真环境：** 搭建一个ROS2兼容的仿真环境，并配置好相应的机器人模型、传感器（如激光雷达、相机等）和仿真世界。确保仿真环境中的机器人能够接收速度指令、反馈里程计消息、发布传感器数据和TF坐标变换等。

*   **实体机器人：** 如果条件允许，准备一台实体机器人。这台机器人应该具备与仿真环境中相似的功能，即能够接收速度指令、反馈里程计消息、发布激光雷达等传感器数据以及发布TF坐标变换。同时，确保实体机器人已经安装了ROS2系统，并且已经配置好相应的驱动程序和算法库。

*   **Nav2软件包：** 安装并配置好Nav2软件包及其依赖项。Nav2是ROS2中用于机器人导航的综合性软件包，它包含了路径规划、避障、局部和全局地图维护等多个功能模块。确保Nav2软件包与您的ROS2版本兼容，并且已经根据需要进行了适当的配置。

通过掌握上述基础知识并准备好实践环境，您将能够更好地学习和应用Nav2进行机器人导航任务的开发与调试。

#### 导航参数参考
https://docs.nav2.org/configuration/index.html

### SLAM 定位与建图
**概念**

SLAM（Simultaneous Localization and Mapping，即时定位与建图）是机器人学和自动导航领域的一种关键技术，允许机器人在未知环境中绘制环境地图。

在ROS 2下进行SLAM，通常涉及使用专门为ROS 2开发的SLAM相关软件包。这些软件包利用ROS 2的通讯框架（如话题、服务和action）来处理接收到的传感器数据，包括激光雷达、相机、惯性测量单元等。

以下列举了一些常见的在ROS2中使用的SLAM系统：

1.  **SLAM Toolbox** ：SLAM Toolbox 是一种在 ROS 2 中普遍使用的 SLAM 解决方案，由 Steve Macenski 维护。它是为了替代原有的ROS中著名的gmapping包和Cartographer SLAM包而开发的，提供了2D激光SLAM领域中的多种功能。

2.  **Cartographer for ROS 2** ：Cartographer是由Google开发的一个2D和3D环境SLAM库。尽管它最初是为ROS开发的，但已经有为ROS 2创建的适配版本，可以在ROS 2生态系统内使用。

要在ROS 2中开始一个SLAM项目，还需要具体的传感器（例如激光雷达或摄像头），计算机要有足够的算力来运行SLAM算法，以及熟悉ROS 2节点、话题、服务、action、参数等知识，以实现有效的数据处理和通信。随着项目的发展，可能还需要考虑动态重配置、3D建图和路径规划等高级功能。

**作用**

必须先说明的是：SLAM与Nav2并没有直接的依赖关系，它们是两种相对独立的技术框架。然而，在实际应用中，这两者却展现出紧密的联系与互补性。

> **独立性：** SLAM能够独立完成环境地图的构建，无需Nav2的介入。它依靠传感器数据来感知环境，并通过算法估计机器人的位置与姿态，从而构建出环境的地图。与此同时，Nav2也具备自主导航的能力。即使没有SLAM地图作为输入，它也能依赖其他来源的环境信息进行导航。
> 
> **互补性：** 尽管两者在技术上各自独立，但它们的结合却带来了显著的优势。Nav2能够利用SLAM创建的精确地图进行高效的路径规划，确保机器人能够顺利导航至目标位置。而SLAM则能借助Nav2的导航控制功能，在机器人移动过程中实时收集环境信息，从而进一步提高地图构建的效率和准确性。

因此，尽管SLAM与Nav2在技术上各自独立，但在构建完整的机器人导航系统中，它们的互补性使得机器人能够在复杂环境中实现更加自主、精准的建图或导航。

#### slam_toolbox概述
概念

SLAM Toolbox是一个基于开源软件的用来构建 **2D地图** 的工具集，旨在为研究人员和开发者提供一个快速构建和实现SLAM算法的平台。它集成了多种常用的SLAM算法，并提供了丰富的数据处理和滤波函数，以及地图构建和环境建模的工具。

功能

*   **算法集成** 包含基于卡尔曼滤波器和粒子滤波器的SLAM算法，以及基于图优化的SLAM算法等。

*   **数据处理** 提供数据融合、数据预处理和异常检测等功能，支持激光雷达、摄像头、IMU等多种传感器数据的处理。

*   **地图构建** 利用传感器数据和机器人运动信息构建准确的地图，并进行更新和优化。

*   **模块化设计** 支持用户插入自己的算法或替换现有模块，实现高度定制化的SLAM解决方案。

优点

*   **开源性和灵活性** 作为开源软件，SLAM Toolbox提供了源代码和文档，用户可以自由修改和扩展算法。

*   **多种传感器支持** 支持激光雷达、摄像头、IMU等多种传感器数据，适应不同应用场景的需求。

*   **高效性** 利用现代C++的特性优化性能，确保在资源受限的硬件上也能高效运行。

*   **广泛的应用前景** 适用于学术研究、无人机导航、自动驾驶汽车、室内机器人和工业自动化等多个领域。

#### slam_toolbox安装
借助于Advanced Packaging Tools([APT](https://c6.y.qq.com/base/fcgi-bin/u?__=bqEbdGxIB7mM))包资源管理器，可以使用二进制的方式安装slam_toolbox，安装指令如下：

```bash
sudo apt install ros-<ros2-distro>-slam-toolbox

#humble
sudo apt install ros-jazzy-slam-toolbox
#jazzy
sudo apt install ros-jazzy-slam-toolbox
```

指令中的`<ros2-distro>`请替换成当前所使用的ROS2版本。

#### slam_toolbox节点说明
在slam_toolbox中常用的节点有两个：sync_slam_toolbox_node和async_slam_toolbox_node。

> **sync_slam_toolbox_node：**
> 
> 这是一个同步节点，会等待所有传感器数据到达后再处理，确保数据完整性和一致性，提高定位和建图准确性。但同步处理可能导致延迟，更适合对数据一致性和准确性要求高、实时性要求不高的场景。
> 
> **async_slam_toolbox_node：**
> 
> 与同步节点不同，这是一个异步节点，可以立即处理已接收的数据，减小延迟，提高响应速度。但异步处理可能导致数据不完全同步，定位和建图结果可能不如同步方式准确。因此，它更适合对实时性要求高、对数据一致性和准确性要求相对较低的场景。

在选择使用sync_slam_toolbox_node还是async_slam_toolbox_node时，需根据应用需求和环境权衡。若实时性关键且能接受一定定位或建图误差，选择async_slam_toolbox_node。若对数据一致性和准确性要求较高，且实时性非首要考虑，则选择sync_slam_toolbox_node。另外二者的主要区别在于数据处理的方式，而两个节点发布的话题、订阅的话题、发布的服务以及使用的参数等都是一样的。

1.  订阅的话题

| 话题 | 类型 | 描述 |
|:---|:---|:---|
| /scan | sensor_msgs/msg/LaserScan | 来自激光雷达输入的扫描数据 |
| /tf | tf2_msgs/msg/TFMessage | 配置的odom_frame到base_frame的转换 |

虽然不订阅/odom,但是需要发布/odom,以改变坐标。

| 特性 | slam_toolbox | hector_slam |
|:---|:---|:---|
| 地图精度 | 高 | 中 |
| 实时性 | 较好，但依赖优化（回环检测） | 极高 |
| 依赖数据 | 激光雷达、TF 树、里程计 | 激光雷达（可选 IMU） |
| 回环检测 | 支持 | 不支持 |
| 长期运行 | 支持 | 不支持 |
| 适用场景 | 动态导航、复杂环境 | 简单环境，或无里程计时 |

2.  发布的话题

| 话题 | 类型 | 描述 |
|:---|:---|:---|
| /map | nav_msgs/msg/OccupancyGrid | pose-graph（姿态图）在特定的更新频率（map_update_interval）下的占用栅格表示。 |
| /pose | geometry_msgs/msg/PoseWithCovarianceStamped | 配置的map_frame中base_frame的位姿以及根据扫描匹配计算的协方差 |

3.  发布的服务

| 话题 | 类型 | 描述 |
|:---|:---|:---|
| /slam_toolbox/clear_changes | slam_toolbox/srv/Clear | 清除所有待处理的手动位姿图操作的更改 |
| /slam_toolbox/deserialize_map | slam_toolbox/srv/DeserializePoseGraph | 从磁盘加载保存的序列化位姿图文件 |
| /slam_toolbox/dynamic_map | nav_msgs/OccupancyGrid | 请求位姿图的当前状态作为占用网格 |
| /slam_toolbox/manual_loop_closure | slam_toolbox/srv/LoopClosure | 请求对位姿图进行手动更改 |
| /slam_toolbox/pause_new_measurements | slam_toolbox/srv/Pause | 暂停处理新传入的激光扫描 |
| /slam_toolbox/save_map | slam_toolbox/srv/SaveMap | 保存可用于显示 AMCL 定位的地图图像文件。 |
| /slam_toolbox/serialize_map | slam_toolbox/srv/SerializePoseGraph | 保存地图位姿图和数据，可用于继续建图、slam_toolbox 定位、离线操作等 |
| /slam_toolbox/toggle_interactive_mode | slam_toolbox/srv/ToggleInteractive | 在交互模式与非交互模式之间切换，发布节点的交互式标记及其位置，以便在应用程序中进行更新 |
| /slam_toolbox/reset | slam_toolbox/srv/Reset | 将当前地图重置回初始状态 |

4.  参数

    1.  求解器参数

*   **solver_plugin**  用于 karto 扫描解算器的非线性解算器类型。选项：solver_plugins::CeresSolver, - solver_plugins::SpaSolver, solver_plugins::G2oSolver. Default: solver_plugins::CeresSolver.

*   **ceres_linear_solver**  Ceres 使用的线性求解器。选项：SPARSE_NORMAL_CHOLESKY、SPARSE_SCHUR、ITERATIVE_SCHUR、CGNR。默认为 SPARSE_NORMAL_CHOLESKY。

*   **ceres_preconditioner**  与该求解器一起使用的预处理器。选项：JACOBI、IDENTITY（none）、SCHUR_JACOBI。默认为JACOBI。

*   **ceres_trust_strategy**  信任区域策略。行搜索策略没有公开，因为它们对于这种用途表现不佳。选项：LEVENBERG_MARQUARDT、DOGLEG。默认值：LEVENBERG_MARQUARDT。

*   **ceres_dogleg_type**  如果信任策略是 DOGLEG，则使用dogleg策略。选项：TRADITIONAL_DOGLEG、SUBSPACE_DOGLEG。默认值：TRADITIONAL_DOGLEG

*   **ceres_loss_function**  拒绝异常值的损失函数类型。没有一个等于损失平方。选项：None、HuberLoss、CauchyLoss。默认值：None。

*   **mode**  “建图”或“定位”模式，用于 Ceres 问题创建中的性能优化

    *   Toolbox参数

*   **odom_frame**  里程计坐标系

*   **map_frame**  地图坐标系

*   **base_frame**  基坐标系

*   **scan_topic**  扫描主题名， 注意是/scan 不是scan

*   **scan_queue_size**  扫描消息对队列长度。在异步模式下应始终设置为 1

*   **use_map_saver**  实例化地图服务程序并自行订阅map主题

*   **map_file_name**  启动时加载的位姿图文件的名称（如果可用）

*   **map_start_pose**  启动建图/定位时的位姿（如果可用）

*   **map_start_at_dock**  在dock（第一个节点）处启动姿势图加载（如果可用）。如果同时设置了pose和dock，优先使用pose

*   **debug_logging**  更改日志以进行调试

*   **throttle_scans**  在同步模式下限制的扫描次数

*   **transform_publish_period**  里程计odom变换发布周期。 0 不会发布变换。

*   **map_update_interval**  更新 2D 占用地图的时间间隔

*   **enable_interactive_mode**  是否允许启用交互模式。交互模式将保留映射到其 ID 的激光扫描缓存，以便在交互模式下进行可视化。结果，该进程的内存将会增加。在定位和长期建图模式下可以手动禁用此功能，因为它们会随着时间的推移增加内存利用率。对于建图或连续建图模式均有效。

*   **position_covariance_scale**  从扫描匹配发布姿势时缩放位置协方差的量。这可用于调整下游定位滤波器中位姿的影响。协方差表示测量的不确定性，因此扩大协方差将减小位姿对下游滤波器的影响。默认值：1.0

*   **yaw_covariance_scale**  从扫描匹配发布位姿时缩放偏航协方差的量。请参阅position_covariance_scale 的描述。默认值：1.0

*   **resolution**  生成的 2D 占用图的分辨率

*   **max_laser_range**  用于 2D 占用地图光栅化的最大激光范围

*   **minimum_time_interval**  在同步模式下处理的扫描之间的最短持续时间

*   **transform_timeout**  查找转换 TF 超时时间限制

*   **tf_buffer_duration**  存储 TF 消息以供查询的时间。如果在同步模式下以倍速脱机运行，则设置高一些。

*   **stack_size_to_use**  将堆栈大小重置为的字节数，以启用文件的序列化/反序列化。自由默认值为 40000000，但越少越好。

*   **minimum_travel_distance**  处理新扫描之前的最小行进距离

    *   匹配器参数

*   **use_scan_matching**  是否使用扫描匹配来优化里程位姿

*   **use_scan_barycenter**  是否使用重心或扫描位姿

*   **minimum_travel_heading**  合理更新的最小航向变化

*   **scan_buffer_size**  缓冲到链中的扫描次数，也用作定位模式循环缓冲区中的扫描次数

*   **scan_buffer_maximum_scan_distance**  从缓冲区中删除之前扫描，距离之前位姿的最大距离

*   **link_match_minimum_response_fine**  精细分辨率通过的阈值链接匹配算法响应

*   **link_scan_maximum_distance**  有效链接扫描之间的最大距离

*   **Loop_search_maximum_distance**  循环闭合时考虑的扫描距离的最大阈值

*   **do_loop_close**  是否进行循环闭合（如果不确定，答案是“true”）

*   **Loop_match_minimum_chain_size**  寻找循环闭合的扫描的最小链长度

*   **Loop_match_maximum_variance_coarse**  粗略搜索中传递给细化的阈值方差

*   **Loop_match_minimum_response_coarse**  粗略搜索中环路闭合算法的阈值响应要传递给细化

*   **Loop_match_minimum_response_fine**  精细搜索中循环闭合算法的阈值响应传递给细化

*   **correlation_search_space_dimension** 搜索网格大小以进行扫描相关性

*   **correlation_search_space_resolution**  搜索网格分辨率以进行扫描相关性

*   **correlation_search_space_smear_deviation**  用于平滑响应的多模态涂抹量

*   **loop_search_space_dimension**  循环闭合算法的搜索网格的大小

*   **loop_search_space_resolution**  搜索网格分辨率以进行循环闭合

*   **loop_search_space_smear_deviation**  用于平滑响应的多模态涂抹量

*   **distance_variance_penalty**  应用于匹配扫描的惩罚，因为它与里程姿势不同

*   **angle_variance_penalty**  应用于匹配扫描的惩罚，因为它与里程姿势不同

*   **fine_search_angle_offset**  用于测试精细扫描匹配的角度范围

*   **rough_search_angle_offset**  用于测试粗略扫描匹配的角度范围

*   **coarse_angle_resolution**  在扫描匹配中测试的偏移范围内的角度分辨率

*   **minimum_angle_penalty**  确保尺寸不会膨胀的最小惩罚角度

*   **minimum_distance_penalty**  扫描可以确保大小不会爆炸的最小惩罚

*   **use_response_expansion**  如果没有找到可行的匹配，是否自动增加搜索网格大小

#### slam_toolbox基本使用
1.  准备工作

在src目录下，请先调用如下指令在工作空间的src目录下创建一个功能包：

```bash
ros2 pkg create mycar_slam_slam_toolbox --dependencies slam_toolbox
```

2.  编写launch文件与参数文件

在功能包下，新建launch目录和params目录，launch目录下新建`online_sync_launch.py`文件并输入如下内容：

```python
import os

from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
from launch_ros.actions import Node
from ament_index_python.packages import get_package_share_directory

def generate_launch_description():
    use_sim_time = LaunchConfiguration('use_sim_time')
    slam_params_file = LaunchConfiguration('slam_params_file')

    declare_use_sim_time_argument = DeclareLaunchArgument(
        'use_sim_time',
        default_value='false',
        description='Use simulation/Gazebo clock')
    declare_slam_params_file_cmd = DeclareLaunchArgument(
        'slam_params_file',
        default_value=os.path.join(get_package_share_directory("mycar_slam_slam_toolbox"),
                                   'params', 'mapper_params_online_sync.yaml'),
        description='Full path to the ROS2 parameters file to use for the slam_toolbox node')

    start_sync_slam_toolbox_node = Node(
        parameters=[
          slam_params_file,
          {'use_sim_time': use_sim_time}
        ],
        package='slam_toolbox',
        executable='sync_slam_toolbox_node',
        name='slam_toolbox',
        output='screen')

    ld = LaunchDescription()

    ld.add_action(declare_use_sim_time_argument)
    ld.add_action(declare_slam_params_file_cmd)
    ld.add_action(start_sync_slam_toolbox_node)

    return ld
```

该launch文件主要是加载了slam_toolbox下的sync_slam_toolbox_node节点，并且会从当前功能包的params下读取一个名为`mapper_params_online_sync.yaml`的配置文件。这个配置文件还不存在，接下来需要在params目录下新建`mapper_params_online_sync.yaml`文件，并输入如下内容：

```yaml
slam_toolbox:
  ros__parameters:
    solver_plugin: solver_plugins::CeresSolver
    ceres_linear_solver: SPARSE_NORMAL_CHOLESKY
    ceres_preconditioner: SCHUR_JACOBI
    ceres_trust_strategy: LEVENBERG_MARQUARDT
    ceres_dogleg_type: TRADITIONAL_DOGLEG
    ceres_loss_function: None

    odom_frame: odom
    map_frame: map
    base_frame: base_link
    scan_topic: /scan
    mode: mapping #localization

    #map_file_name: test_steve
    #map_start_pose: [0.0, 0.0, 0.0]
    #map_start_at_dock: true

    debug_logging: false
    throttle_scans: 1
    transform_publish_period: 0.02 
    map_update_interval: 2.0
    resolution: 0.05
    max_laser_range: 20.0 
    minimum_time_interval: 0.5
    transform_timeout: 0.2
    tf_buffer_duration: 30.0
    stack_size_to_use: 40000000 
    enable_interactive_mode: true

    use_scan_matching: true
    use_scan_barycenter: true
    minimum_travel_distance: 0.1
    minimum_travel_heading: 0.1
    scan_buffer_size: 100
    scan_buffer_maximum_scan_distance: 10.0
    link_match_minimum_response_fine: 0.1  
    link_scan_maximum_distance: 1.5
    loop_search_maximum_distance: 3.0
    do_loop_closing: true 
    loop_match_minimum_chain_size: 10           
    loop_match_maximum_variance_coarse: 3.0  
    loop_match_minimum_response_coarse: 0.35    
    loop_match_minimum_response_fine: 0.45

    correlation_search_space_dimension: 0.5
    correlation_search_space_resolution: 0.01
    correlation_search_space_smear_deviation: 0.1 

    loop_search_space_dimension: 8.0
    loop_search_space_resolution: 0.05
    loop_search_space_smear_deviation: 0.03

    distance_variance_penalty: 0.5      
    angle_variance_penalty: 1.0    

    fine_search_angle_offset: 0.00349     
    coarse_search_angle_offset: 0.349   
    coarse_angle_resolution: 0.0349        
    minimum_angle_penalty: 0.9
    minimum_distance_penalty: 0.5
    use_response_expansion: true
```

配置文件的内容需要根据实际情况进行动态调整。

3.  编辑配置文件

打开`CMakeLists.txt` 并输入如下内容：

```cmake
install(DIRECTORY launch params
  DESTINATION share/${PROJECT_NAME}
)
```

4.  编译

终端中进入当前工作空间，编译功能包：

```bash
colcon build --packages-select mycar_slam_slam_toolbox
```

5.  执行

    1.  请先调用如下指令启动仿真环境：

    ```bash
    . install/setup.bash
    ros2 launch demo_gazebo_sim gazebo_sim_robot_world.launch.py
    ```
    3.  然后在终端下进入当前工作空间，输入如下指令：

    ```bash
    . install/setup.bash
    ros2 launch mycar_slam_slam_toolbox online_sync_launch.py use_sim_time:=True
    ```
    5.  启动rviz2，将Fixed Frame设置为map，添加map插件并将话题设置为/map，即可显示slam_toolbox创建的地图了，当机器人运动时，地图也会随之更新。

    6.  use_sim_time:=True参数表示使用仿真的时间。

      最后需要说明的是，本节内容使用的是`sync_slam_toolbox_node` 节点，即以同步方式建图，而异步建图节点`async_slam_toolbox_node` 的使用与同步类似。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1746.webp)

我们用键盘控制节点去控制机器人跑满整张地图，

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1747.webp)

黑色：障碍物

白色：无障碍物区

灰色：未知区

SLAM是建图与定位，以上就是建图，那么定位是啥呢？

定位就是Slam会发布一个/tf，这里面会包含机器人到map之间的坐标变换。

这个/tf发布的具体是map到odom的坐标变换，所以需要你自己去处理odom和base_link之间的坐标关系。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1748.webp)

这样的设计，可以让整条坐标树是一个链式结构，避免base_link或者base_foot_print出现两个父类，这样计算量会变大。

**注意事项：**
1. 突然烂图
   1. 如果你的环境很简单，那么跑图的时候可能会突然烂图，这个时候，大概率是do_loop_closing的问题，你把do_loop_closing设置成false再试试。
   这个东西是SLAM用激光检测形状来匹配之前走过的路，如果他匹配到的形状差不多他会把当前这段轨迹和以前的轨迹连起来，然后重新优化整张 pose graph，让地图整体更一致。
   主要是为了防止odom会漂，因为 odom 会漂，机器人最后回到起点时，SLAM 可能觉得位置差了几十厘米，但是可以通过激光雷达矫正。
   2. 在窄通道尽量直线过去，不要原地旋转等等，这种窄通道炸了大概率是scan matching相关参数的问题。

#### cartographer概述
**概念**

Cartographer是Google推出的一套基于图优化的激光SLAM算法库，支持二维和三维地图的构建。它结合了激光雷达和惯性测量单元（IMU）的数据，通过高效的算法实现实时、准确的定位和建图。

**功能**

*   **并行扫描匹配** 利用并行计算技术加快扫描匹配速度，提高建图效率。

*   **位姿图优化** 通过图优化技术估计机器人的姿态和地图的拓扑结构，减少累积误差。

*   **实时地图更新** 在机器人移动过程中实时更新地图，确保地图的准确性和时效性。

*   **回环检测** 通过回环检测识别机器人曾经访问过的区域，进一步减少累积误差，提高地图的全局一致性。

*   **多传感器融合** 支持激光雷达、IMU、里程计等多种传感器数据的融合，提高定位和建图的精度。

优点

*   **高效稳定** Cartographer的算法经过精心设计和优化，能够在复杂环境中高效稳定地运行。

*   **高精度** 通过图优化和回环检测技术提供高精度的定位和建图结果。

*   **灵活性** 支持二维和三维地图构建，适应不同应用场景的需求。

*   **开源免费** Cartographer是开源项目，用户可以免费获取和使用其源代码和文档。

*   **社区支持** 拥有活跃的社区支持体系，用户可以获取来自全球开发者的帮助和支持。

#### cartographer安装
借助于Ubuntu的包资源管理器，可以使用二进制的方式安装cartographer，安装指令如下：

```bash
sudo apt install ros-<ros2-distro>-cartographer
sudo apt install ros-<ros2-distro>-cartographer-ros

# humble
sudo apt install ros-jazzy-cartographer
sudo apt install ros-jazzy-cartographer-ros
#jazzy
sudo apt install ros-jazzy-cartographer
sudo apt install ros-jazzy-cartographer-ros
```

上述两条安装指令中，前者用于安装cartographer的核心库，这个包不直接与ROS2集成，而是作为一个独立的算法库存在，为地图构建和定位提供底层的计算支持。后者则是cartographer在ROS2环境下的封装，它提供了与ROS2系统的接口，使得Cartographer算法能够在ROS2环境中运行。另外指令中的`<ros2-distro>`请替换成当前所使用的ROS2版本。

#### cartographer节点说明
在Cartographer框架中，`cartographer_node`和`cartographer_occupancy_grid_node`是两个关键的节点，它们各自承担着不同的角色和功能。详细介绍如下。

> **cartographer_node：**
> 
> 主要负责订阅来自各种传感器的数据（如激光雷达、IMU、里程计等），并基于这些数据实时构建地图。它采用子图（submap）的方法来逐步构建和更新地图，确保定位的准确性和建图的实时性。
> 
> **cartographer_occupancy_grid_node：**
> 
> 该节点负责接收`cartographer_node`发布的子图列表（`/submap_list`），并将其拼接成完整的栅格地图（occupancy grid map），然后发布这个地图。这个节点是地图生成的最终环节，它使得Cartographer能够输出人类可读且易于可视化的地图。

这两个节点的协同工作，前者负责实时构建和更新地图，后者则负责将子图拼接成完整的栅格地图并发布，使得Cartographer能够高效地实现SLAM功能。

1.  cartographer_node订阅的话题

| 话题 | 类型 | 描述 |
|:---|:---|:---|
| /scan | sensor_msgs/msg/LaserScan | 来自激光雷达输入的扫描数据 |
| /odom | nav_msgs/msg/Odometry | 里程计消息 |

2.  cartographer_node发布的话题

| 话题 | 类型 | 描述 |
|:---|:---|:---|
| /scan_matched_points2 | sensors_msgs/msg/PointCloud2 | 匹配好的点云数据，用于scan-to-submap matching |
| /submap_list | cartographer_ros_msgs/SubmapList | 发布构建好的子图列表 |

3.  cartographer_node发布的服务

| 话题 | 类型 | 描述 |
|:---|:---|:---|
| /submap_query | cartographer_ros_msgs/srv/SubmapQuery | 提供查询子图的服务，获取到查询的子图 |
| /start_trajectory | cartographer_ros_msgs/srv/StartTrajectory | 开始一条轨迹 |
| /finish_trajectory | cartographer_ros_msgs/srv/FinishTrajectory | 结束一条给定ID的轨迹 |
| /write_state | cartographer_ros_msgs/srv/WriteState | 将当前状态写入磁盘文件中 |
| /get_trajectory_states | cartographer_ros_msgs/srv/GetTrajectoryStates | 获取指定轨迹的状态 |
| /read_metrics | cartographer_ros_msgs/srv/ReadMetrics | 读取性能指标 |

4.  cartographer_node参数

`cartographer_node`节点需要接收一个参数配置文件，该配置文件包含了地图构建、轨迹跟踪等所需的各项参数。

5.  cartographer_occupancy_grid_node订阅的话题

| 话题 | 类型 | 描述 |
|:---|:---|:---|
| /submap_list | cartographer_ros_msgs/SubmapList | 子图列表 |

6.  cartographer_occupancy_grid_node发布的话题

| 话题 | 类型 | 描述 |
|:---|:---|:---|
| /map | nav_msgs/msg/OccupancyGrid | 发布的栅格地图 |

7.  cartographer_occupancy_grid_node请求的服务

| 话题 | 类型 | 描述 |
|:---|:---|:---|
| /submap_query | cartographer_ros_msgs/srv/SubmapQuery | 获取子图 |

8.  cartographer_occupancy_grid_node参数

`cartographer_occupancy_grid_node`节点需要配置地图的分辨率和更新周期等参数，以确保生成的栅格地图满足特定的精度和实时性要求。

#### cartogarpher基本使用
1.  准备工作

在src目录下，请先调用如下指令在工作空间的src目录下创建一个功能包：

```bash
ros2 pkg create mycar_slam_cartographer --dependencies cartographer
```

2.  编写launch文件与参数文件

在功能包下，新建launch目录和params目录，launch目录下新建`cartographer.launch.py`文件并输入如下内容：

```python
from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
from launch_ros.actions import Node
import os
from ament_index_python.packages import get_package_share_directory

def generate_launch_description():

    use_sim_time_arg = DeclareLaunchArgument('use_sim_time', default_value = 'false')
    resolution_arg = DeclareLaunchArgument('resolution', default_value='0.05')

    cartographer_node = Node(
        package = 'cartographer_ros',
        executable = 'cartographer_node',
        parameters = [{'use_sim_time': LaunchConfiguration('use_sim_time')}],
        arguments = [
            '-configuration_directory', os.path.join(get_package_share_directory("mycar_slam_cartographer"),"params"),
            '-configuration_basename', 'mycar.lua'],
        output = 'screen'
    )

    cartographer_occupancy_grid_node = Node(
        package = 'cartographer_ros',
        executable = 'cartographer_occupancy_grid_node',
        parameters = [
            {'use_sim_time': LaunchConfiguration('use_sim_time')},
            {'resolution': LaunchConfiguration('resolution')}],
    )

    return LaunchDescription([
        use_sim_time_arg,
        resolution_arg,
        cartographer_node,
        cartographer_occupancy_grid_node,
    ])
```

该launch文件主要是加载了cartographer_ros下的cartographer_node与cartographer_occupancy_grid_node节点，并且会从当前功能包的params下读取一个名为mycar.lua的配置文件。这个配置文件还不存在，接下来需要在params目录下新建mycar.lua文件，并输入如下内容：

```lua
include "map_builder.lua" -- 地图构建器
include "trajectory_builder.lua" -- 轨迹构建器

options = {
  map_builder = MAP_BUILDER,
  trajectory_builder = TRAJECTORY_BUILDER,
  map_frame = "map",  -- 地图坐标系
  tracking_frame = "base_link", -- 跟踪的坐标系，可以是基坐标系、雷达或imu的坐标系
  published_frame = "odom", -- cartographer发布的位姿（pose）的坐标系
  odom_frame = "carto_odom",  -- cartographer 计算后优化的里程计，并非机器人本身里程计
  provide_odom_frame = false, -- 是否发布cartographer的里程计
  publish_frame_projected_to_2d = true, -- 是否转换成2d(无俯仰、滚动的情况下为 true)
  use_odometry = true, -- 是否订阅里程计数据
  use_nav_sat = false, -- 是否订阅GPS
  use_landmarks = false, -- 是否订阅路标
  num_laser_scans = 1, -- 订阅的雷达的数量
  num_multi_echo_laser_scans = 0, -- 订阅的多层回波激光雷达数量
  num_subdivisions_per_laser_scan = 1, -- 将激光雷达的数据拆分成多少部分发布
  num_point_clouds = 0, -- 订阅多线激光雷达的数量
  lookup_transform_timeout_sec = 1.5, -- 坐标变换超时时间
  submap_publish_period_sec = 0.5, -- 发布子图的时间间隔
  pose_publish_period_sec = 5e-3, -- 发布pose的时间间隔
  trajectory_publish_period_sec = 30e-3, -- 发布轨迹的时间间隔
  rangefinder_sampling_ratio = 1., -- 雷达采样比例
  odometry_sampling_ratio = 0.8, -- 里程计采样比例(如果里程计精度低，可以减小该设置值)
  fixed_frame_pose_sampling_ratio = 1., -- 参考坐标系采样比例
  imu_sampling_ratio = 1.,-- imu采样比例
  landmarks_sampling_ratio = 1., -- 路标采样比例
}

MAP_BUILDER.use_trajectory_builder_2d = true -- 启用2D轨迹构建器

TRAJECTORY_BUILDER_2D.min_range = 0.15 -- 最小雷达有效距离
TRAJECTORY_BUILDER_2D.max_range = 6.0 -- 最大雷达有效距离
TRAJECTORY_BUILDER_2D.missing_data_ray_length = 3. -- 缺失数据的射线长度
TRAJECTORY_BUILDER_2D.use_imu_data = false -- 是否使用 imu 数据
TRAJECTORY_BUILDER_2D.use_online_correlative_scan_matching = true -- 是否使用在线相关扫描匹配
TRAJECTORY_BUILDER_2D.motion_filter.max_angle_radians = math.rad(0.1) -- 运动滤波器的最大角度限制（以弧度为单位）

POSE_GRAPH.constraint_builder.min_score = 0.65 -- 建约束时的最小分数
POSE_GRAPH.constraint_builder.global_localization_min_score = 0.7 -- 全局定位时的最小分数

-- POSE_GRAPH.optimize_every_n_nodes = 0

return options
```

配置文件的内容需要根据实际情况进行动态调整。

3.  编辑配置文件

打开`CMakeLists.txt` 并输入如下内容：

```cmake
install(DIRECTORY launch params
  DESTINATION share/${PROJECT_NAME}
)
```

4.  编译

终端中进入当前工作空间，编译功能包：

```bash
colcon build --packages-select mycar_slam_cartographer
```

5.  执行

（1）请先调用如下指令启动仿真环境：

```bash
. install/setup.bash
ros2 launch demo_gazebo_sim gazebo_sim_robot_world.launch.py
```

（2）然后在终端下进入当前工作空间，输入如下指令：

```bash
. install/setup.bash
ros2 launch mycar_slam_cartographer cartographer.launch.py use_sim_time:=True
```

（3）启动rviz2，将Fixed Frame设置为map，添加map插件并将话题设置为/map，即可显示创建的地图了，当机器人运动时，地图也会随之更新。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1749.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1750.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1751.webp)

### 地图服务
SLAM建图时，地图数据是保存在内存中的，这也意味着，一旦节点关闭，数据也会一并被释放，而更合理的实现应该是将构建的地图序列化到的磁盘以持久化存储，并且后期还要通过反序列化读取磁盘的地图数据以做其他操作。在Nav2中已经已经封装好了地图序列化和反序列化的相关功能包，该包是：`nav2_map_server`。

在`nav2_map_server`中，可以通过话题和服务接口与Nav2系统的其余部分进行交互。`nav2_map_server`包下有两个重要的节点，分别是`map_saver_cli`和`map_server`，通过`map_saver_cli`节点则可以保存地图，而`map_server`节点则可以在启动时显示地图。

#### 保存地图(序列化)
##### 地图保存节点说明
在`nav2_map_server`中的地图保存节点是`map_saver_server`，该节点相关信息如下。

1.  订阅的话题

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /map | nav_msgs/msg/OccupancyGrid | SLAM节点发布的地图数据 |

2.  参数

*   **save_map_timeout**  保存地图操作的最大等待时间。

*   **free_thresh_default**  栅格单元被认为未被占用的概率阈值。

*   **occupied_thresh_default**  栅格单元被认为占用的概率阈值。

*   **map_subscribe_transient_local**  节点重启后消息不保留，默认为 true。

3.  map_saver_cli

另外，而为了便于使用，在`map_saver_server`的基础之上还封装了一个名为`map_saver_cli`的可执行程序，它可以以实参的方式更方便的设置地图保存相关数据，并且后续执行时也是调用`map_saver_cli`，其实参列表如下：

*   **\-t** 订阅的地图话题。

*   **\-f** 地图存储路径。

*   **\--occ** 栅格单元被认为占用的概率阈值。

*   **\--free** 栅格单元被认为未被占用的概率阈值。

*   **\--fmt** 图片格式。

*   **\--mode** 地图模式，trinary(默认)或scale或raw。

##### 地图保存基本操作
**准备工作**

请先启动仿真或实体机器人，然后启动SLAM相关节点，实现基本的建图功能。

**保存地图**

SLAM建图完毕，在终端下进入工作空间，调用如下指令保存地图：

```bash
ros2 run nav2_map_server map_saver_cli -f map/my_map
```

上述指令将订阅/map话题，并把/map话题里的数据保存为文件，在工作空间下的map目录(*需要自行创建该目录，否则将会抛出异常*)中，生成两个文件，分别名为：`my_map.yaml`和`my_map.pgm`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1752.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1753.webp)

##### 地图接口
在Nav2中地图相关的接口主要有两个：

*   **nav_msgs/msg/MapMetaData** 地图元数据，包括地图的宽度、高度、分辨率等。

*   **nav_msgs/msg/OccupancyGrid** 地图栅格数据，一般会在rviz中以图形化的方式显示。

**nav_msgs/msg/MapMetaData**

调用指令`ros2 interface show nav_msgs/msg/MapMetaData`查看接口格式，显示如下内容（注释已汉化）：

```bash

# 它包含了关于OccupancyGrid特性的基本信息

# 地图加载时间
builtin_interfaces/Time map_load_time
        int32 sec
        uint32 nanosec

# 地图分辨率 [米/像素]
float32 resolution

# 地图宽度 [像素]
uint32 width

# 地图高度 [像素]
uint32 height

#地图的原点坐标[米，米，弧度]。这是地图中单元格(0,0)左下角在现实世界中的位置和方向。
geometry_msgs/Pose origin
        Point position
                float64 x
                float64 y
                float64 z
        Quaternion orientation
                float64 x 0
                float64 y 0
                float64 z 0
                float64 w 1
```

**nav_msgs/msg/OccupancyGrid**

调用指令`ros2 interface show nav_msgs/msg/OccupancyGrid`查看接口格式，显示如下内容（注释已汉化）：

```bash

# 它代表一个二维网格地图。
std_msgs/Header header
        builtin_interfaces/Time stamp
                int32 sec
                uint32 nanosec
        string frame_id

# 地图元数据
MapMetaData info
        builtin_interfaces/Time map_load_time
                int32 sec
                uint32 nanosec
        float32 resolution
        uint32 width
        uint32 height
        geometry_msgs/Pose origin
                Point position
                        float64 x
                        float64 y
                        float64 z
                Quaternion orientation
                        float64 x 0
                        float64 y 0
                        float64 z 0
                        float64 w 1

# 地图数据按照行优先的顺序进行排列，

# 这意味着首先填充第一行的所有单元格，

# 然后填充第二行，依此类推。

# 起始单元格是(0,0)，也就是地图的左上角。

# 单元格(1, 0)紧接着(0,0)，是x方向上紧邻的下一个单元格。

# 而单元格(0, 1)则位于第一行的第二个位置，其索引等于地图的宽度（info.width），

# 然后才是(1, 1)单元格，即第二行的第二个单元格。

# 关于地图数据的值，它们根据具体的应用需求来定义。但在很多情况下，

# 会使用0表示该单元格是未占用的，即机器人可以安全通过；

# 1表示该单元格是确定占用的，即存在障碍物；

# 而-1表示该单元格的状态是未知的，即机器人尚未探测到该区域的状态。
int8[] data
```

##### 地图存储格式
在 **地图保存基本操作**  一节中，地图保存后后生成两个文件，这两个文件就是用来存储序列化后的地图数据的。其中，my_map.pgm是一张图片资源，使用图片查看程序打开即可，而my_map.yaml保存的是地图的元数据信息，用于描述图片，内容格式如下：

```yaml
image: my_map.pgm
mode: trinary
resolution: 0.05
origin: [-0.955, -10.9, 0]
negate: 0
occupied_thresh: 0.65
free_thresh: 0.25
```

**参数解释：**

*   **image**  被描述的图片资源路径，可以是绝对路径也可以是相对路径。

*   **resolution** 图片分片率(单位: m/像素)。

*   **origin** 地图中左下像素的二维姿态，为（x，y，z），偏航为逆时针旋转（偏航=0 表示无旋转）。

*   **occupied_thresh** 占用概率大于此阈值的像素被视为完全占用。

*   **free_thresh** 占用率小于此阈值的像素被视为完全空闲。

*   **negate** 是否应该颠倒白色/黑色 自由/占用的语义。

*   **mode**  地图模式，trinary(默认)或scale或raw。

#### 读取地图(反序列化)
##### 地图读取节点说明
在`nav2_map_server`中的地图读取节点是`map_server`，该节点相关信息如下。

**发布的话题**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /map | nav_msgs/msg/OccupancyGrid | 地图数据 |

**参数**

*   **frame_id**  地图坐标系名称。

*   **topic_name**  话题名称。

*   **yaml_filename**  地图数据源。

##### 地图读取基本操作
**准备工作**

请先调用如下指令在工作空间的src目录下创建一个功能包：

```bash
ros2 pkg create mycar_map_server --dependencies nav2_map_server
```

在功能包下，新建launch文件夹，并在CMakeLists.txt中添加如下配置：

```cmake
install(DIRECTORY launch DESTINATION share/${PROJECT_NAME})
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1754.webp)

**读取地图**

使用`map_server`读取地图时，常用的方式有两种，分别是使用终端指令与launch文件集成。两种方式效果一致，都可以以话题的方式发布地图消息。

```bash
colcon build
source ./install/setup.bash
```

**方式1：终端指令**

请在终端下进入工作空间，输入如下指令：

```bash
ros2 run nav2_map_server map_server --ros-args -p yaml_filename:=map/my_map.yaml
```

由于`map_server`是具有生命周期的节点，所以接下来还需要对节点进行配置和激活，请新开终端执行如下指令：

```bash
ros2 lifecycle set /map_server configure
ros2 lifecycle set /map_server activate
```

执行完毕若无异常，再调用`ros2 topic list`即可查看到`/map`话题了，说明地图消息已经被发布了。

**方式2：launch集成**

方式1需要手动设置`map_server`生命周期，步骤稍显繁琐，因此，我们还可以将该节点集成进launch文件，以简化启动步骤。在launch目录下新建名为`map_server.launch.py`的文件，并输入如下内容：

```python
import os
from launch import LaunchDescription
from launch_ros.actions import Node
def generate_launch_description():
  map_file = os.path.join('map', 'my_map.yaml')
  map_server_node = Node(
      package='nav2_map_server',
      executable='map_server',
      name='map_server',
      output='screen',
      parameters=[{'use_sim_time': True},
                  {'yaml_filename':map_file}]
  )
  manager_mapper_node = Node(
    package='nav2_lifecycle_manager',
    executable='lifecycle_manager',
    name='lifecycle_manager_mapper',
    output='screen',
    parameters=[{'use_sim_time': True},
      {'autostart': True},
      {'node_names': ['map_server']}]
  )
  return LaunchDescription([map_server_node,manager_mapper_node])
```

在该文件中，使用了功能包中`nav2_lifecycle_manager`中的`lifecycle_manager`组件，该组件可以自动的配置、激活其所管理的具有生命周期的节点。构建功能包后并执行该launch文件，其最终效果与方式一类似。

```bash
ros2 launch mycar_map_server map_server.launch.py
```

**显示地图**

打开rviz2，然后添加Map插件，并将话题设置为/map，并将该话题的`Durability Policy`

选项设置为`Transient Local`，就可以正常显示地图数据了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1755.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1756.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1757.webp)

### AMCL自适应蒙特卡洛定位
定位是机器人在已知地图上确定自身位置的过程，为机器人的导航提供了基础信息。

Nav2中的定位技术技术称之为AMCL，全称Adaptive Monte Carlo Localization，即自适应蒙特卡洛定位，是一种基于粒子滤波器的定位算法。它通过蒙特卡洛方法进行自适应定位，利用对机器人周围环境的感知和观测数据的分析，来确定机器人在环境中的位置和姿态。在Nav2中对应的功能包为`nav2_amcl`。

在AMCL中，粒子滤波器的核心思想是使用一组粒子（样本）来代表机器人在地图上的可能位置。每个粒子都有一个权重，表示该粒子所代表的位置的置信度。算法会根据机器人的运动模型和传感器数据来更新这些粒子的位置和权重。随着时间的推移，粒子会逐渐收敛到机器人实际位置附近，从而实现对机器人位置的准确估计。

#### 定位节点说明
功能包`nav2_amcl`中的核心节点为amcl。该节点相关信息如下。

**1.订阅的话题**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /map | /nav_msgs/msg/OccupancyGrid | 地图数据 |
| /scan | /sensor_msgs/msg/LaserScan | 激光雷达数据 |
| /initialpose | /geometry_msgs/msg/PoseWithCovarianceStamped | 用来初始化粒子滤波器的均值和协方差 |
| /tf | /tf2_msgs/msg/TFMessage | 坐标变换消息 |

**2.发布的话题**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /amcl_pose | /geometry_msgs/msg/PoseWithCovarianceStamped | 机器人在地图中的位姿估计 |
| /particle_cloud | /nav2_msgs/msg/ParticleCloud | 位姿估计集合，rviz中可以被 PoseArray 订阅然后图形化显示机器人的位姿估计集合 |
| /tf | /tf2_msgs/msg/TFMessage | 发布从 odom 与 map 的转换 |

**3.发布的服务**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /reinitialize_global_localization | std_srvs/srv/Empty | 在全局范围内初始化粒子位姿 |
| /request_nomotion_update | std_srvs/srv/Empty | 在没有运动模型更新的情况下手动触发粒子群的更新 |

**4.参数**

**通用参数**

*   `bond_disable_heartbeat_timeout`: 设置为`true`时，禁用amcl节点与其他节点之间基于心跳的超时检测。这通常用于当节点之间的连接非常稳定，不需要频繁的心跳检测来确认连接状态时。

*   `base_frame_id`: 定义机器人基坐标系的ID，通常是`base_link`或类似的名称。

*   `global_frame_id`: 定义全局地图坐标系的ID，通常是`map`。

*   `odom_frame_id`: 定义里程计坐标系的ID，通常是`odom`。

*   `tf_broadcast`: 设置为`true`时，amcl节点会发布从里程计坐标系到全局地图坐标系的变换。

*   `transform_tolerance`: 设置TF变换的容忍度，用于处理TF树中的时间不一致性。

*   `use_sim_time`: 设置为`true`时，amcl将使用ROS 2的模拟时间（如果可用）。这在仿真环境中很有用。

**激光模型参数**

*   `laser_model_type`: 设置激光模型类型，`likelihood_field`是一种常用的模型，它考虑了激光束击中障碍物的概率。

*   `laser_max_range`和`laser_min_range`: 分别设置激光雷达的最大和最小探测范围。

*   `laser_likelihood_max_dist`: 设置激光模型考虑的最大距离，超过这个距离的数据将被忽略。

*   `do_beamskip`和相关参数（`beam_skip_distance`、`beam_skip_threshold`、`beam_skip_error_threshold`）: 这些参数用于控制是否跳过某些激光束的处理，以减少计算量。然而，`do_beamskip`被设置为`false`，意味着不跳过任何激光束。

**粒子滤波器参数**

*   `alpha1`**到**`alpha5`: 这些参数用于控制粒子滤波器中的权重更新过程，但它们的具体作用可能因amcl的实现而异。在标准的amcl实现中，这些参数可能不是直接使用的。

*   `max_particles`和`min_particles`: 分别设置粒子滤波器的最大和最小粒子数。

*   `resample_interval`: 设置在重采样前需要的滤波更新次数。

*   `pf_err`和`pf_z`: 这些参数用于控制粒子滤波器的性能，但它们的具体作用可能依赖于amcl的实现细节。

**初始位姿参数**

*   `initial_pose`: 定义了机器人的初始位姿（x, y, yaw, z），但在实际使用中，如果`set_initial_pose`被设置为`true`，则这个初始位姿可能会被通过服务请求设置的初始位姿所覆盖。

*   `set_initial_pose`: 设置为`true`时，允许通过服务请求来设置机器人的初始位姿。

*   `always_reset_initial_pose`: 设置为`false`时，表示不会在每个定位会话开始时自动重置初始位姿。

**其他参数**

*   `first_map_only`: 当设置为`false`时，表示amcl将订阅并处理不断更新的地图话题。

*   `map_topic`: 定义地图话题的名称，amcl将订阅这个话题以获取地图信息。

*   `scan_topic`: 定义激光雷达扫描数据话题的名称，amcl将订阅这个话题以获取用于定位的数据。

*   `save_pose_rate`: 设置保存机器人位姿的速率（以Hz为单位）。

*   `recovery_alpha_fast`和`recovery_alpha_slow`: 这些参数在标准的amcl实现中可能不是直接使用的，它们可能属于某个特定版本的amcl或扩展。

*   `z_hit`、`z_rand`、`z_short`、`z_max`和`sigma_hit`: 这些参数定义了激光模型中的概率分布，用于计算激光束击中障碍物或随机位置的概率。

#### 定位节点基本操作
**1.准备工作**

请先调用如下指令在工作空间的src目录下创建一个功能包：

```bash
ros2 pkg create mycar_localization --dependencies nav2_amcl mycar_map_server
```

**2.编写launch文件与参数文件**

在功能包下，新建launch和params文件夹，在launch目录下新建名为`mycar_loca.launch.py`的文件，并输入如下内容：

```python
import os
from ament_index_python.packages import get_package_share_directory

from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument, IncludeLaunchDescription
from launch.substitutions import LaunchConfiguration
from launch.launch_description_sources import PythonLaunchDescriptionSource

from launch_ros.actions import Node


def generate_launch_description():

    use_sim_time_arg = DeclareLaunchArgument(
        'use_sim_time',
        default_value='false'
    )

    amcl_yaml = os.path.join(
        get_package_share_directory('mycar_localization'),
        'params',
        'amcl.yaml'
    )

    amcl_node = Node(
        package='nav2_amcl',
        executable='amcl',
        name='amcl',
        output='screen',
        parameters=[
            amcl_yaml,
            {'use_sim_time': LaunchConfiguration('use_sim_time')} # 覆盖掉yaml里的use_sim_time参数,以launch启动的设置为准.
        ]
    )

    manager_localization_node = Node(
        package='nav2_lifecycle_manager',
        executable='lifecycle_manager',
        name='lifecycle_manager_localization',
        output='screen',
        parameters=[
            {'use_sim_time': LaunchConfiguration('use_sim_time')},
            {'autostart': True},
            {'node_names': ['amcl']}
        ]
    )

    map_server_launch = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(
                get_package_share_directory('mycar_map_server'),
                'launch',
                'map_server.launch.py'
            )
        ),
        launch_arguments={
            'use_sim_time': LaunchConfiguration('use_sim_time')
        }.items()
    )

    return LaunchDescription([
        use_sim_time_arg,
        map_server_launch,
        amcl_node,
        manager_localization_node,
    ])
```

在上述代码中，创建了`amcl`节点，并从`params`目录加载了名为`amcl.yaml`的配置文件，且由于`amcl`也是拥有生命周期的节点，所以将其添加进了生命周期管理器。最后，定位必须依赖于地图信息，因此又包含了 **地图读取基本操作**  中的launch文件，以加载地图。

在params目录下新建名为`amcl.yaml`的文件，并输入如下内容：

```yaml
/**:
  ros__parameters:
    use_sim_time: True  # 由launch的设置覆盖
    alpha1: 0.2
    alpha2: 0.2
    alpha3: 0.2
    alpha4: 0.2
    alpha5: 0.2
    base_frame_id: "base_link"
    beam_skip_distance: 0.5
    beam_skip_error_threshold: 0.9
    beam_skip_threshold: 0.3
    do_beamskip: false
    global_frame_id: "map"
    lambda_short: 0.1
    laser_likelihood_max_dist: 2.0
    laser_max_range: 100.0
    laser_min_range: -1.0
    laser_model_type: "likelihood_field"
    max_beams: 60
    max_particles: 2000
    min_particles: 500
    odom_frame_id: "odom"
    pf_err: 0.05
    pf_z: 0.99
    recovery_alpha_fast: 0.0
    recovery_alpha_slow: 0.0
    resample_interval: 1
    robot_model_type: "nav2_amcl::DifferentialMotionModel"
    save_pose_rate: 0.5
    sigma_hit: 0.2
    tf_broadcast: true
    transform_tolerance: 2.0
    update_min_a: 0.2
    update_min_d: 0.25
    z_hit: 0.5
    z_max: 0.05
    z_rand: 0.5
    z_short: 0.05
    scan_topic: scan
    set_initial_pose: false
```

关于参数的具体含义，可以参考 **定位节点说明** 中参数相关内容。

**3.编辑配置文件**

打开`CMakeLists.txt` 并输入如下内容：

```cmake
install(DIRECTORY launch params
  DESTINATION share/${PROJECT_NAME}
)
```

**4.编译**

终端中进入当前工作空间，编译功能包：

```bash
colcon build --packages-select mycar_localization
```

**5.执行**

（1）请先调用如下指令启动仿真环境：

```bash
. install/setup.bash
ros2 launch demo_gazebo_sim gazebo_sim_robot_world.launch.py
```

（2）然后在终端下进入当前工作空间，输入如下指令：

```bash
. install/setup.bash
ros2 launch mycar_localization mycar_loca.launch.py use_sim_time:=True
```

（3）启动键盘控制节点以作备用：

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

（4）在rviz2中，将Fixed Frme设置为map，添加TF插件，按照 **地图读取基本操作**  添加并显示地图。

接下来，点击rviz2菜单栏的`2D Pose Estimate`在地图中为机器人设置一个初始位姿。

这里需要给一个大概的机器人位置和机器人的朝向，不是很准确也可以，机器人会在运动中逐渐通过AMCL校准。

先点击`2D Pose Estimate`，左键在地图上点击机器人所在位置，长摁别松手，鼠标往机器人朝向的位置划，出现下方这种绿色箭头，再松手即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1758.webp)

再添加`ParticleCloud`插件，将话题设置为`/particle_cloud`，并将话题下`Reliability Policy`设置为`Best Effort`，最后使用键盘控制机器人运动时，会发现，机器人周边会出现点云，并且随着机器人的运动，点云会出现不同程度的收敛或发散。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1759.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1760.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1761.webp)

即便你的机器人撞墙了，rviz2和Gazebo的机器人位置完全偏移了，只要再让机器人运动一会儿，机器人位置会被重新预估出来，AMCL非常强。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1762.webp)

如上图位置已经完全偏移了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1763.webp)

经过一段时间行驶，姿态位置再次被预估成功。

### 导航服务器
本节将介绍导航服务器中核心节点，实现基本导航功能，并将导航与SLAM集成，以实现自主探索建图。

下方网站是官方NAV2的各个节点的参数说明(**调参的时候非常常用的网站**):

https://docs.nav2.org/configuration/index.html

看不懂的英文可以用沉浸式翻译这个chrome插件来翻译,可以进行中英文对照翻译,非常好用.

https://chromewebstore.google.com/detail/immersive-translate-trans/bpoadfkcbjbfhfodiogcnhhhpibjhbnh?utm%5C_source=official&pli=1

https://microsoftedge.microsoft.com/addons/detail/immersive-translate-tra/amkbmndfnliijdhojkpoglbnaaahippg

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1764.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1765.webp)

不会用这个插件的话,也可以看鱼香ROS翻译好的版本(但是网站不稳定):

http://fishros.org/doc/nav2/configuration/index.html

#### 生命周期管理节点说明
Nav2中通过的`nav2_lifecycle_manager`功能包下的`lifecycle_manager`节点管理其他节点的生命周期状态转换。`lifecycle_manager`节点通过ROS 2的生命周期节点（Lifecycle Node）机制，提供了一种标准化的方法来控制Nav2中各个节点的状态转换。这些状态包括未配置（Unconfigured）、非活动（Inactive）、活动（Active）和结束（Finalized）等。通过精细控制这些状态转换，`lifecycle_manager`节点能够确保Nav2系统的各个部分在正确的时机启动、运行和停止，从而提高系统的可靠性和稳定性。

**参数**

*   `/bond_disable_heartbeat_timeout`: 该参数与节点之间的通信绑定（bonding）有关。节点之间的通信可以通过绑定来增强可靠性，而心跳超时是检测节点是否仍然活跃的一种方式。设置为`false`意味着启用心跳超时检测。

*   `attempt_respawn_reconnection`: 取值为bool值，设置为`true`时，表示如果管理的节点意外终止，生命周期管理器将尝试重新启动它。

*   `autostart`: 取值为bool值，设置为`true`时，表示在生命周期管理器启动时，它将自动尝试启动其管理的节点。

*   `bond_respawn_max_duration`: 重新连接或重新启动节点时的最大持续时间。

*   `bond_timeout`: 与节点之间的通信绑定超时有关。如果在这个时间内没有收到来自另一个节点的消息，则认为该节点已经断开连接。

*   `diagnostic_updater`:

    *   `period`: 诊断更新器的更新周期。诊断更新器用于收集和发布有关节点状态的诊断信息，定义了这些信息更新的频率。

*   `node_names`: 由该生命周期管理器管理的节点名称列表。

*   `use_sim_time`:是否使用仿真实践。

#### 行为树节点说明
**行为树（BT）** 是一种在智能体（如机器人或电脑游戏中的虚拟实体）中构建不同任务之间切换结构的方式。它是一种形式化的图形建模语言，以层次化的节点组织为特征，用于描述和规划复杂系统中各种实体的交互和决策。`nav2_bt_navigator`功能包下的`/bt_navigator`是Nav2中的行为树导航器节点，它实现了基于行为树的导航策略。行为树是一种用于描述复杂行为的树状结构，通过组合不同的行为节点，可以灵活地定义机器人的导航行为，包括路径规划、避障、恢复等。以下是官网。

https://www.behaviortree.dev/

https://arxiv.org/abs/1709.00084

github链接如下：

https://github.com/BehaviorTree/BehaviorTree.CPP

BT可视化工具Groot2下载：

https://www.behaviortree.dev/groot/

1.  订阅的话题

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /goal_pose | geometry_msgs/msg/PoseStamped | 导航目标点，用于触发导航任务 |
| /tf | tf2_msgs/msg/TFMessage | 坐标变换消息，用于不同坐标系之间的转换 |
| /odom | nav_msgs/msg/Odometry | 里程计数据，提供机器人位置和运动信息 |

2.  请求的Action

| Action | 接口 | 描述 |
|:---|:---|:---|
| /navigate_to_pose | nav2_msgs/action/NavigateToPose | 请求导航到指定位姿的Action，包括目标位姿和容忍度等参数 |

3.  参数

*   `use_sim_time`: 指定是否使用模拟时间而非实际时间。

*   `global_frame`: 定义全局坐标系的名称，通常为地图坐标系。

*   `robot_base_frame`: 指定机器人基座的坐标系名称。

*   `odom_topic`: 里程计数据的ROS话题名称。

*   `bt_loop_duration`: 行为树执行循环的持续时间（单位可能根据实现而异）。

*   `default_server_timeout`: 导航服务器操作的默认超时时间。

*   `enable_groot_monitoring`: 启用或禁用Groot监控功能。

*   `groot_zmq_publisher_port`: Groot监控中ZeroMQ发布者的端口号。

*   `groot_zmq_server_port`: Groot监控中ZeroMQ服务器的端口号。

*   `plugin_lib_names`: Nav2 Jazzy 官方示例说明该参数用于添加自定义 BT 插件；Nav2 内置 BT 插件会自动加载，所以没有自定义 BT 插件时不要配置空列表。

#### 规划器节点说明
在Nav2 导航框架中`nav2_planner`功能包下的`planner_server`节点，负责处理路径规划请求，生成从当前位置到目标位置的路径。该节点在执行时需要依赖于`/global_costmap/global_costmap`节点提供的地图消息。

1.  planner_server发布的话题

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /plan | nav_msgs/msg/Path | 当前位置到目标点的全局路径 |

2.  planner_server 参数

*   `/bond_disable_heartbeat_timeout`: 这个参数控制是否禁用心跳超时检测，在ROS 2中，节点之间的通信绑定（bonding）机制用于增强通信的可靠性。心跳超时是检测节点是否仍然活跃的一种机制。如果该参数设置为`true`，则表示禁用了心跳超时检测，这可能在某些特定的网络环境下或当确信节点间通信非常稳定时使用。

*   `GridBased`: 这是一个配置块，专门用于设置基于网格的规划器的参数。基于网格的规划器通常使用地图的网格化表示来规划路径。

    *   `allow_unknown`: 控制规划器是否允许在地图的未知（即未探索）区域中规划路径。

    *   `plugin`: 指定使用的规划器插件。

    *   `tolerance`: 设置规划路径时的容忍度，通常用于考虑机器人尺寸和定位的不确定性。

    *   `use_astar`: 控制是否使用A\* 算法进行路径规划*。A\** 算法是一种启发式搜索算法，能够找到从起点到终点的最短路径。

    *   `use_final_approach_orientation`: 控制规划器是否在路径的终点附近考虑机器人的最终朝向。

*   `expected_planner_frequency`: 这个参数表示对规划器生成新路径的频率的预期值。它帮助系统监控规划器的性能，并可能用于调试或性能优化。

*   `planner_plugins`: 这是一个列表，指定了可用的规划器插件。在这个例子中，它只包含了一个`GridBased`规划器，但理论上可以包含多个不同类型的规划器，以适应不同的导航需求。

*   `use_sim_time`: 这个参数控制是否使用模拟时间。在仿真环境中，时间是由仿真软件控制的，而不是由实际的物理时钟控制的。将此参数设置为`true`允许节点在仿真环境中正常运行，而无需依赖实际的系统时间。这对于开发和测试导航算法非常有用。

3.  /global_costmap/global_costmap订阅的话题

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /global_costmap/footprint | geometry_msgs/msg/Polygon | 机器人（或任何移动平台）的足迹（footprint）信息。足迹是机器人在地图上占据的空间形状，通常用多边形表示。 |
| /map | nav_msgs/msg/OccupancyGrid | 发布环境地图，特别是用于导航的占用网格图（Occupancy Grid Map）。 |
| /scan | sensor_msgs/msg/LaserScan | 激光扫描数据。 |

4.  /global_costmap/global_costmap发布的话题

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /global_costmap/costmap | nav_msgs/msg/OccupancyGrid | 发布全局代价地图的当前状态。 |
| /global_costmap/costmap_raw | nav2_msgs/msg/Costmap | 未经进一步处理的原始代价地图数据。 |
| /global_costmap/costmap_updates | map_msgs/msg/OccupancyGridUpdate | 全局代价地图的更新，该消息可以高效更新地图。 |
| /global_costmap/published_footprint | geometry_msgs/msg/PolygonStamped | 发布机器人的足迹（footprint），即机器人在地图上占据的空间形状。 |

5.  /global_costmap/global_costmap参数

*   `/bond_disable_heartbeat_timeout`: 控制是否禁用心跳超时检测，这在节点间通信绑定时用于监控对方节点的活跃度。

*   `always_send_full_costmap`: 控制是否总是发送完整的代价地图信息，而不是仅发送变化的部分。

*   `clearable_layers`: 列出可以被清除的代价图层，这些图层中的障碍物信息可以通过某种方式（如传感器数据）被更新或清除。

*   `filters`: 定义应用于代价地图的过滤器列表，用于预处理或修改地图数据。

*   `footprint`: 指定机器人在地图上的足迹形状，即机器人占据的空间范围。

*   `footprint_padding`: 为机器人的足迹添加额外的填充空间，以考虑机器人运动时的额外空间需求。

*   `global_frame`: 定义全局代价地图所使用的参考坐标系。

*   `height`和`width`: 定义全局代价地图的高度和宽度（以单元格数计）。

*   `inflation_layer`: 配置膨胀层的参数，膨胀层用于在障碍物周围增加一定宽度的“缓冲区”，以避免机器人与障碍物过近。

    *   `cost_scaling_factor`: 膨胀成本的缩放因子。

    *   `enabled`: 控制膨胀层是否启用。

    *   `inflate_around_unknown`: 控制是否在未知空间周围进行膨胀。

    *   `inflate_unknown`: 控制是否将未知空间视为障碍物并进行膨胀。

    *   `inflation_radius`: 膨胀的半径。

    *   `plugin`: 指定使用的膨胀层插件。

*   `lethal_cost_threshold`: 定义代价地图中视为“致命”障碍物的成本阈值。

*   `map_topic`: 指定订阅以获取地图信息的ROS话题。

*   `observation_sources`: 定义代价地图的观测源，即哪些传感器或数据源用于更新地图。

*   `obstacle_layer`: 配置障碍物层的参数，障碍物层负责处理传感器观测到的障碍物信息。

    *   `combination_method`: 定义如何组合多个观测源的信息。

    *   `enabled`: 控制障碍物层是否启用。

    *   `footprint_clearing_enabled`: 控制是否清除机器人足迹内的障碍物信息。

    *   `max_obstacle_height`和`min_obstacle_height`: 定义障碍物的高度范围。

    *   `observation_sources`: 指定障碍物信息的来源。

    *   `plugin`: 指定使用的障碍物层插件。

    *   `scan`: 包含与激光扫描相关的配置，如扫描数据的处理方式。

*   `origin_x`和`origin_y`: 定义全局代价地图原点的坐标。

*   `plugins`: 列出启用的代价地图插件，这些插件定义了如何构建和更新代价地图。

*   `publish_frequency`: 定义发布代价地图的频率。

*   `resolution`: 定义代价地图的分辨率，即每个单元格代表的实际物理尺寸。

*   `robot_base_frame`: 定义机器人基座的参考坐标系。

*   `robot_radius`: 定义机器人的半径，用于计算机器人在地图上的占用空间。

*   `rolling_window`: 控制是否使用滚动窗口（即动态变化的地图区域）而非固定大小的地图。

*   `static_layer`: 配置静态层的参数，静态层负责处理地图中的静态障碍物信息。

    *   `enabled`: 控制静态层是否启用。

    *   `map_subscribe_transient_local`: 控制是否订阅瞬态本地地图更新。

    *   `map_topic`: 指定静态地图的ROS话题（如果不同于全局地图）。

    *   `plugin`: 指定使用的静态层插件。

    *   `subscribe_to_updates`: 控制是否订阅地图更新。

    *   `transform_tolerance`: 定义坐标变换的容忍度。

*   `track_unknown_space`: 控制是否跟踪地图中的未知空间。

*   `transform_tolerance`: 定义在坐标变换过程中允许的误差范围。

*   `trinary_costmap`: 控制是否使用三态代价地图（空闲、占用、未知），而不是仅使用二态（空闲、占用）。

*   `unknown_cost_value`: 定义在代价地图中表示未知空间的值。

*   `update_frequency`: 定义更新代价地图的频率。

*   `use_maximum`: 控制是否使用多个观测源中的最大值来更新代价地图。

*   `use_sim_time`: 控制是否使用仿真时间（在仿真环境中很有用）。

*   `voxel_layer`: 配置体素层的参数，体素层使用体素网格来表示三维空间中的障碍物信息。

    *   `enabled`: 控制体素层是否启用。

    *   `footprint_clearing_enabled`: 控制是否清除机器人足迹内的体素信息。

    *   `mark_threshold`和`unknown_threshold`: 定义将体素视为障碍物或未知的阈值。

    *   `max_obstacle_height`和`min_obstacle_height`: 定义体素表示的障碍物的高度范围。

    *   `observation_sources`: 指定体素信息的来源。

    *   `origin_z`: 定义体素网格在Z轴上的原点。

    *   `plugin`: 指定使用的体素层插件。

    *   `publish_voxel_map`: 控制是否发布体素地图。

    *   `scan`: 包含与激光扫描相关的配置，如扫描数据的处理方式。

    *   `z_resolution`和`z_voxels`: 定义体素网格在Z轴上的分辨率和体素数。

#### 控制器节点说明
在Nav2导航系统中`nav2_controller`功能包的`controller_server`负责处理导航任务中的控制请求，确保机器人能够按照规划的路径进行移动。其主要功能是根据`nav2_planner`模块计算出的全局或局部路径，生成速度、方向控制的命令，即控制机器人沿着规划好的路径行走。该节点在执行时还需要依赖于`/local_costmap/local_costmap`节点提供的地图消息。

**1.controller_server订阅的话题**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /odom | nav_msgs/msg/Odometry | 机器人的里程计信息，包含位置、速度和姿态 |
| /speed_limit | nav2_msgs/msg/SpeedLimit | 导航过程中的速度限制信息，用于动态调整机器人的移动速度 |

**2.controller_server发布的话题**

| 话题名称 | 消息类型 | 描述 |
|:---|:---|:---|
| /cmd_vel_nav | geometry_msgs/msg/Twist | 发布控制命令，包括线性和角速度，用于控制机器人按照规划路径移动。 |
| /cost_cloud | sensor_msgs/msg/PointCloud2 | 发布成本地图中的点云数据，用于避障和路径规划。 |
| /local_plan | nav_msgs/msg/Path | 发布局部路径规划结果，即机器人应如何到达当前目标点附近的一个点。 |
| /marker | visualization_msgs/msg/MarkerArray | 发布可视化标记，用于在RViz等可视化工具中显示路径、障碍物等信息。 |
| /received_global_plan | nav_msgs/msg/Path | 发布从全局规划器接收到的全局路径，即当前位置到目标点的路径。 |
| /transformed_global_plan | nav_msgs/msg/Path | 发布经过坐标变换的全局路径，确保路径与机器人的当前坐标系一致。 |

**3.controller_server参数**

*   FollowPath: 这个部分定义了一个名为FollowPath的插件或配置集，它可能是一个路径跟随行为或算法的配置。它包含了多个子参数和子配置，用于定义如何跟随路径。

    *   BaseObstacle: 定义了基本的障碍物评估参数，用于在路径跟随过程中避免障碍物。

        *   class: 指定了类的名称，这里是BaseObstacle，表示这是一个基本障碍物评估组件。

        *   scale: 定义了该障碍物评估在整体评估中的权重或影响程度。

        *   sum_scores: 指示是否累加多个障碍物的分数，false可能表示使用最大值或其他逻辑。

    *   GoalAlign, GoalDist, PathAlign, PathDist, RotateToGoal, Oscillation: 这些都是路径跟随过程中的不同评估或行为组件，每个都有其特定的参数和用途，如对齐目标、保持与目标或路径的距离、减少振荡等。

    *   acc_lim_theta, acc_lim_x, acc_lim_y: 这些参数定义了机器人在不同方向上的加速度限制。

    *   critics: 指定了哪些评估组件（或“批评家”）将被用于路径跟随决策。

    *   debug_trajectory_details: 指示是否发布轨迹的详细调试信息。

    *   其他与速度、加速度、时间粒度、轨迹生成等相关的参数，共同定义了路径跟随算法的行为和性能。

*   controller_frequency: 指定了控制器（可能是FollowPath或其他控制器）的运行频率，以赫兹为单位。

*   controller_plugins: 指定了将要使用的控制器插件列表，这里只包含了FollowPath。

*   failure_tolerance: 定义了容忍失败的时间或距离，用于在评估控制器是否失败时提供一定的缓冲。

*   general_goal_checker: 定义了一个通用的目标检查器，用于确定机器人是否已达到其目标位置和方向。

*   goal_checker_plugins: 指定了将要使用的目标检查器插件列表。

*   min_theta_velocity_threshold, min_x_velocity_threshold, min_y_velocity_threshold: 这些定义了机器人在不同方向上的最小速度阈值，低于这些阈值可能被视为停止或静止。

*   odom_topic: 指定了里程计信息的ROS主题。

*   progress_checker: 定义了一个进度检查器，用于评估机器人是否在向目标移动。

*   qos_overrides: 定义了ROS服务或主题的QoS（服务质量）覆盖设置，用于调整消息传递的可靠性和性能。

*   speed_limit_topic: 指定了速度限制信息的ROS主题。

*   use_sim_time: 指示是否使用模拟时间，这在ROS仿真环境中非常有用。

**4./local_costmap/local_costmap订阅的话题**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /local_costmap/footprint | geometry_msgs/msg/Polygon | 机器人或移动平台的足迹多边形，用于本地代价地图的计算 |
| /scan | sensor_msgs/msg/LaserScan | 激光扫描仪的扫描数据，用于环境感知和避障 |

**5./local_costmap/local_costmap发布的话题**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /local_costmap/clearing_endpoints | sensor_msgs/msg/PointCloud2 | 清除成本图上的障碍物点云数据，通常用于动态障碍物处理 |
| /local_costmap/costmap | nav_msgs/msg/OccupancyGrid | 本地成本图，表示机器人周围环境的可通行性 |
| /local_costmap/costmap_raw | nav2_msgs/msg/Costmap | 未经处理的本地成本图，可能包含更详细的信息 |
| /local_costmap/costmap_updates | map_msgs/msg/OccupancyGridUpdate | 本地成本图的更新信息，包括哪些区域发生了变化 |
| /local_costmap/published_footprint | geometry_msgs/msg/PolygonStamped | 发布的机器人足迹多边形，时间戳表示发布时间 |
| /local_costmap/voxel_grid | nav2_msgs/msg/VoxelGrid | 体素网格数据，用于成本图生成中的空间划分和优化 |

**6./local_costmap/local_costmap参数**

*   `/bond_disable_heartbeat_timeout`: 是否禁用节点间的心跳超时检查。当设置为`true`时，表示禁用该功能，可能用于减少网络通信量或适应特定网络环境。

*   `always_send_full_costmap`: 是否总是发送完整的成本图。当设置为`true`时，节点将不依赖于增量更新，而是始终发送完整的成本图数据。

*   `clearable_layers`: 指定可以被清除的层列表。在这个例子中，包括`obstacle_layer`、`voxel_layer`和`range_layer`，这意味着这些层中的障碍物数据可以被清除。

*   `filters`: 用于指定应用于成本图的过滤器列表。此处为空，表示没有应用任何过滤器。

*   `footprint`: 机器人的足迹多边形，定义了机器人在二维空间中的物理占用区域。

*   `footprint_padding`: 足迹的填充量，用于在计算成本图时给机器人足迹添加额外的空间。

*   `global_frame`: 全局参考坐标系的名称，通常用于定位和导航任务。

*   `height`: 成本图的高度（以单元格数量计）。

*   `inflation_layer`: 膨胀层的配置，用于在障碍物周围添加一定范围的膨胀区域，使机器人与障碍物保持安全距离。

*   `lethal_cost_threshold`: 致命成本阈值，超过此阈值的成本值表示不可通行的区域。

*   `map_topic`: 订阅的地图主题名称，用于获取全局地图信息。

*   `observation_sources`: 观察源的配置，用于指定哪些传感器数据将被用于更新成本图。此处为空字符串，可能是默认值或配置方式的不同。

*   `obstacle_layer`: 障碍物层的配置，用于处理来自传感器（如激光雷达）的障碍物数据。

*   `origin_x`,`origin_y`: 成本图原点的X和Y坐标，定义了成本图在全局坐标系中的位置。

*   `plugins`: 启用的插件列表，定义了成本图使用的不同层（如障碍物层、膨胀层等）。

*   `publish_frequency`: 成本图的发布频率（以Hz为单位）。

*   `resolution`: 成本图的分辨率（以米/单元格计）。

*   `robot_base_frame`: 机器人基座的参考坐标系名称，用于定位机器人。

*   `robot_radius`: 机器人的半径，用于在成本图中表示机器人的物理尺寸。

*   `rolling_window`: 是否使用滚动窗口。当设置为`true`时，成本图将随着机器人的移动而更新其位置和范围。

*   `track_unknown_space`: 是否跟踪未知空间。在某些情况下，这可能用于处理未探索或未知的区域。

*   `transform_tolerance`: 变换容差，定义了接受变换的时间差和角度差的阈值。

*   `trinary_costmap`: 是否使用三态成本图（通常是自由、占用、未知）。

*   `unknown_cost_value`: 未知区域在成本图中的成本值。

*   `update_frequency`: 成本图的更新频率（以Hz为单位），不同于发布频率。

*   `use_maximum`: 是否在多个源提供相同位置的成本信息时使用最大值。

*   `use_sim_time`: 是否使用模拟时间而非系统时间。这在仿真环境中很有用。

*   `voxel_layer`: 体素层的配置，用于将三维空间划分为体素（体积像素），以提高成本图的处理效率。

#### 恢复器节点说明
恢复行为是机器人导航过程中一个至关重要的部分，它允许机器人在遇到障碍、卡住或其他导航问题时采取一系列预定义的动作来尝试恢复。在Nav2中由`nav2_behaviors`功能包的`behavior_server`实现这一功能。

**1.订阅的话题**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /clock | rosgraph_msgs/msg/Clock | ROS系统时间 |
| /cmd_vel_teleop | geometry_msgs/msg/Twist | 遥操作命令，用于控制机器人的线性和角速度 |
| /local_costmap/costmap_raw | nav2_msgs/msg/Costmap | 局部代价地图的原始数据 |
| /local_costmap/published_footprint | geometry_msgs/msg/PolygonStamped | 机器人在局部代价地图中的已发布足迹 |
| /preempt_teleop | std_msgs/msg/Empty | 遥操作抢占信号，用于中断当前遥操作 |

**2.发布的话题**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /cmd_vel | geometry_msgs/msg/Twist | 发送给底层控制器的速度命令 |

**3.提供的Action服务器**

| 话题 | Action接口 | 描述 |
|:---|:---|:---|
| /assisted_teleop | nav2_msgs/action/AssistedTeleop | 遥控辅助操作服务，允许用户在导航时提供方向性输入 |
| /backup | nav2_msgs/action/BackUp | 后退动作服务，用于在特定情况下使机器人后退 |
| /drive_on_heading | nav2_msgs/action/DriveOnHeading | 按指定航向行驶的动作服务 |
| /spin | nav2_msgs/action/Spin | 旋转动作服务，允许机器人在原地旋转 |
| /wait | nav2_msgs/action/Wait | 等待动作服务，使机器人在当前位置等待一定时间 |

**4.参数**

*   `use_sim_time`: 该参数指定是否使用模拟时间而非实际时间。这在仿真环境中非常有用，因为仿真环境可以加速或减速时间流逝，而不需要等待实际时间的流逝。

*   `global_frame`: 定义全局坐标系的名称，该坐标系通常用于导航任务中的定位和路径规划。在这里，它被设置为`odom`，意味着使用里程计数据来作为全局坐标系的参考。

*   `robot_base_frame`: 指定机器人基座的坐标系名称。这是机器人上用于定位和运动控制的参考点，通常与机器人的物理中心或驱动轮的中心相对应。

*   `odom_topic`: 这是一个ROS话题名称，用于发布里程计数据。里程计数据包含了机器人随时间推移的位置和姿态变化信息，是导航和定位系统的关键输入之一。

*   `/bond_disable_heartbeat_timeout`：这个参数可能用于配置ROS节点之间的心跳检测机制。将其设置为`true`可能意味着禁用或调整心跳超时的行为，以便在特定情况下（如仿真环境）避免不必要的超时错误。

*   `assisted_teleop`,`backup`,`drive_on_heading`,`spin`,`wait`: 这些是行为树中可能使用的行为插件的配置项。每个插件都定义了机器人可以执行的一种特定行为，如辅助遥操作、后退、按指定方向行驶、原地旋转和等待。

*   `behavior_plugins`: 列出了在行为树中可用的行为插件名称。

*   `cmd_vel_teleop`: 指定了用于遥操作的速度控制命令的ROS话题名称。

*   `costmap_topic`: 定义了局部代价地图的ROS话题名称，代价地图用于表示环境中的障碍物和可通行区域。

*   `cycle_frequency`: 定义了导航系统更新其状态和规划新路径的频率（以赫兹为单位）。

*   `max_rotational_vel`,`min_rotational_vel`,`rotational_acc_lim`: 这些参数定义了机器人旋转时的最大速度、最小速度和加速度限制。

*   `projection_time`: 与代价地图的更新或预测未来障碍物位置有关的时间参数。

*   `footprint_topic`: 定义了发布机器人足迹（即机器人占据的空间）的ROS话题名称。

*   `simulate_ahead_time`,`simulation_time_step`: 这些参数与仿真环境相关，可能用于控制仿真过程中的时间流逝和步长。

*   `transform_tolerance`: 定义了坐标变换时的容差范围，用于处理不同坐标系之间的微小差异。

#### 航点跟随节点说明
在Nav2 导航堆栈中，`nav2_waypoint_follower`包下的`/waypoint_follower`节点负责跟踪由路径规划器生成的一系列航点（waypoints），以确保机器人能够沿着预定的路径安全、准确地移动。该节点的主要功能是根据当前机器人位置和速度信息，以及由路径规划器（如`nav2_global_planner`和`nav2_local_planner`）提供的航点列表，计算出控制指令来控制机器人的运动。这些控制指令可能包括线性和角速度命令，或者更具体的运动学或动力学命令，具体取决于机器人的类型和配置。

**1.提供的Action服务器**

| 话题 | Action接口 | 描述 |
|:---|:---|:---|
| /follow_waypoints | nav2_msgs/action/FollowWaypoints | 允许客户端请求planner_server按照一系列路点进行导航 |

**2.请求的Action服务**

| 话题 | Action接口 | 描述 |
|:---|:---|:---|
| /navigate_to_pose | nav2_msgs/action/NavigateToPose | 允许planner_server（或调用它的节点）请求导航到指定的位姿 |

**3.参数**

*   `use_sim_time`: 指定是否使用模拟时间而非实际时间进行节点的计时和同步。这在仿真环境中特别有用，因为仿真环境可能无法提供与真实时间完全同步的时钟。

*   `loop_rate`: 定义了节点的主循环速率，即节点每秒执行其主要任务（如处理数据、发布信息等）的次数。这个参数对于控制节点的响应性和资源使用非常重要。

*   `stop_on_failure`: 指明当导航任务遇到无法克服的障碍或达到其他失败条件时，节点是否应该停止执行。这对于确保在失败情况下系统能够安全地停止并等待进一步指令很重要。

*   `bond_disable_heartbeat_timeout`: 涉及节点间通信的可靠性机制。Bond是ROS 2中用于节点间稳定通信的一种机制，其中心跳信号用于检测节点是否仍然活跃。将此参数设置为true会禁用心跳超时检测，这可能在某些特定的网络配置或应用场景中是有用的。

*   `waypoint_task_executor_plugin`: 指定了在执行路径点导航任务时要使用的插件。路径点导航通常涉及一系列预先定义的点，机器人需要按顺序访问这些点。这个参数允许用户指定用于执行这种类型任务的特定插件或算法。

*   `wait_at_waypoint`: 这是一个复合参数，用于配置在路径点等待的特定行为。

    *   `enabled`: 启用或禁用在到达每个路径点时等待的功能。

    *   `plugin`: 指定实现等待功能的插件类型。这允许用户根据需要选择不同的等待策略或行为。

    *   `waypoint_pause_duration`: 定义了在每个路径点处等待的持续时间（以毫秒为单位）。这可以用于确保机器人在移动到下一个路径点之前已经稳定或已经完成了某些操作。

#### 路径平滑节点说明
在Nav2框架中`nav2_smoother`功能包下的`smoother_server`节点通过加载和运行各种平滑器插件，对规划出的路径进行平滑处理，使得机器人能够更流畅、连续且安全地移动。这一功能对于提高机器人的导航性能和减少硬件磨损具有重要意义。

**1.订阅的话题**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /global_costmap/costmap_raw | nav2_msgs/msg/Costmap | 全局代价地图的原始数据，用于路径规划 |
| /global_costmap/published_footprint | geometry_msgs/msg/PolygonStamped | 机器人在全局代价地图中的足迹表示 |

**2.发布的话题**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /plan_smoothed | nav_msgs/msg/Path | 经过平滑处理后的全局路径 |

**3.提供的Action服务器**

| 话题 | 动作类型 | 描述 |
|:---|:---|:---|
| /smooth_path | nav2_msgs/action/SmoothPath | 提供平滑路径的服务，接受路径平滑的请求，并返回平滑后的路径。这允许客户端（如行为树）异步地请求路径平滑，并在平滑完成后接收结果。 |

**4.参数**

*   `/bond_disable_heartbeat_timeout`: 指示是否禁用Bond的心跳超时功能。在分布式系统中，Bond用于管理节点间的连接和心跳，此参数用于调整心跳相关的行为。

*   `costmap_topic`: 代价地图数据的ROS话题名称，通常是全局代价地图的原始数据。

*   `footprint_topic`: 机器人足迹（即机器人在地图上的占用区域）的ROS话题名称，用于在全局代价地图中表示机器人的物理尺寸。

*   `robot_base_frame`: 指定机器人基座的坐标系名称，这是机器人导航中用于定位和移动的参考点。

*   `simple_smoother`:

    *   `do_refinement`: 指示是否启用路径的细化（或进一步优化）过程。

    *   `max_its`: 平滑过程中允许的最大迭代次数，用于控制平滑算法的收敛时间。

    *   `plugin`: 平滑插件的类型，这里是`nav2_smoother::SimpleSmoother`，表示使用简单的平滑算法。

    *   `tolerance`: 平滑算法的收敛容差，当路径变化小于此值时，认为平滑过程已完成。

    *   `w_data`: 平滑过程中数据项（如障碍物距离）的权重。

    *   `w_smooth`: 平滑过程中平滑项（如路径曲率）的权重。

*   `smoother_plugins`: 定义的平滑插件列表，这里列出了`simple_smoother`，表示将使用此插件进行路径平滑。

*   `transform_tolerance`: 坐标变换的容差，用于处理不同坐标系之间的转换时的不确定性。

*   `use_sim_time`: 指定是否使用模拟时间而非实际时间。在仿真环境中，这通常设置为`true`，以匹配仿真器的虚拟时间；在真实环境中，应设置为`false`以使用ROS系统的实际时间。

#### 速度平滑节点说明
Nav2框架中的`nav2_velocity_smoother`包下的`velocity_smoother`节点主要负责平滑由Nav2框架发送给机器人控制器的速度指令。其核心功能是实现速度和加速度平滑。这一功能对于确保机器人在导航过程中的稳定性和安全性至关重要。

**1.订阅的话题**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /cmd_vel_nav | geometry_msgs/msg/Twist | 接收来自其他节点的速度控制指令的话题 |

**2.发布的话题**

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /cmd_vel | geometry_msgs/msg/Twist | 发布经过处理或平滑后的速度控制指令的话题 |

**3.参数**

*   `bond_disable_heartbeat_timeout`: 指示是否禁用节点间的心跳超时机制。如果为`true`，则节点间的心跳检测不会因超时而断开连接。

*   `deadband_velocity`: 定义在哪些速度分量上应用死区平滑（即忽略小于此阈值的微小速度变化）。这里分别为X轴、Y轴和偏航角速度（theta）设置了死区值。

*   `feedback`: 指定速度平滑器的反馈类型。`OPEN_LOOP`表示开环控制，即不考虑机器人的实际速度反馈进行速度调整。

*   `max_accel`: 定义机器人在各个方向上的最大加速度限制，包括X轴、Y轴和偏航角速度（theta）。

*   `max_decel`: 定义机器人在各个方向上的最大减速度限制，包括X轴、Y轴和偏航角速度（theta）。注意，减速度值以负数表示。

*   `max_velocity`: 定义机器人在各个方向上的最大速度限制，包括X轴、Y轴和偏航角速度（theta）。

*   `min_velocity`: 定义机器人在各个方向上的最小速度限制，包括X轴、Y轴和偏航角速度（theta）。这通常用于避免发送过小的速度指令给底层控制器。

*   `odom_duration`: 与里程计数据相关的参数，但在此上下文中可能不直接用于`velocity_smoother`节点，可能是遗留或与其他功能相关联。

*   `odom_topic`: 指定里程计数据的ROS话题名称，`velocity_smoother`节点将订阅此话题以获取机器人的运动信息。

*   `scale_velocities`: 指示是否根据加速度限制同比例调整速度的其他分量。如果为`false`，则不会进行速度缩放。

*   `smoothing_frequency`: 定义速度平滑操作的执行频率（Hz），即每秒进行多少次平滑计算。

*   `use_sim_time`: 指定是否使用模拟时间而非实际系统时间。这对于仿真环境特别有用，可以确保时间的一致性和可预测性。

*   `velocity_timeout`: 如果在指定时间内未接收到新的速度指令，则`velocity_smoother`节点将停止发布速度指令，并可能发送零速度指令以停止机器人运动。这是为了防止在失去速度控制时机器人继续移动。

#### 导航功能集成

> 本节 Jazzy 版本的配置依据来自 Jazzy 官方安装路径里的示例文件和 Nav2 官方配置文档：`/opt/ros/jazzy/share/nav2_bringup/params/nav2_params.yaml`、`/opt/ros/jazzy/share/nav2_bt_navigator/behavior_trees/navigate_to_pose_w_replanning_and_recovery.xml`、`/opt/ros/jazzy/share/nav2_bt_navigator/behavior_trees/navigate_through_poses_w_replanning_and_recovery.xml`，以及 Nav2 的配置说明页面。下面的工程参数使用当前工作空间中已经跑通的 `mycar_navigation2_jazzy` 配置。

**1.准备工作**

请先调用如下指令在工作空间的src目录下创建一个功能包：

```bash
ros2 pkg create mycar_navigation2_jazzy --dependencies navigation2 nav2_common
```

**2.编写launch文件与参数文件**

在功能包下，新建launch目录、params目录和bts目录。

launch目录下新建`nav2.launch.py`文件并输入如下内容：

```python
import os

from ament_index_python.packages import get_package_share_directory

from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration

from launch_ros.actions import Node
from launch_ros.descriptions import ParameterFile

from nav2_common.launch import RewrittenYaml


def generate_launch_description():

    use_sim_time_arg = DeclareLaunchArgument(
        'use_sim_time',
        default_value='false'
    )

    use_sim_time = LaunchConfiguration('use_sim_time')
    current_pkg = get_package_share_directory("mycar_navigation2_jazzy")

    bt_params_file = os.path.join(current_pkg, "params", "bt.yaml")
    planner_params_file = os.path.join(current_pkg, "params", "planner.yaml")
    controller_params_file = os.path.join(current_pkg, "params", "controller.yaml")
    behavior_params_file = os.path.join(current_pkg, "params", "behavior.yaml")
    waypoint_params_file = os.path.join(current_pkg, "params", "waypoint.yaml")
    velocity_params_file = os.path.join(current_pkg, "params", "velocity.yaml")
    smoother_params_file = os.path.join(current_pkg, "params", "smoother.yaml")

    def rewrite_params(params_file):
        return ParameterFile(
            RewrittenYaml(
                source_file=params_file,
                root_key='',
                param_rewrites={
                    'use_sim_time': use_sim_time
                },
                convert_types=True
            ),
            allow_substs=True
        )

    bt_params = rewrite_params(bt_params_file)
    planner_params = rewrite_params(planner_params_file)
    controller_params = rewrite_params(controller_params_file)
    behavior_params = rewrite_params(behavior_params_file)
    waypoint_params = rewrite_params(waypoint_params_file)
    velocity_params = rewrite_params(velocity_params_file)
    smoother_params = rewrite_params(smoother_params_file)

    planner_server_node = Node(
        package='nav2_planner',
        executable='planner_server',
        name='planner_server',
        output='screen',
        parameters=[
            planner_params,
            {'use_sim_time': use_sim_time}
        ],
    )

    controller_server_node = Node(
        package='nav2_controller',
        executable='controller_server',
        name='controller_server',
        output='screen',
        parameters=[
            controller_params,
            {'use_sim_time': use_sim_time}
        ],
        remappings=[
            ('cmd_vel', 'cmd_vel_nav')
        ]
    )

    behavior_server_node = Node(
        package='nav2_behaviors',
        executable='behavior_server',
        name='behavior_server',
        output='screen',
        parameters=[
            behavior_params,
            {'use_sim_time': use_sim_time}
        ]
    )

    waypoint_node = Node(
        package='nav2_waypoint_follower',
        executable='waypoint_follower',
        name='waypoint_follower',
        output='screen',
        parameters=[
            waypoint_params,
            {'use_sim_time': use_sim_time}
        ]
    )

    velocity_smoother_node = Node(
        package='nav2_velocity_smoother',
        executable='velocity_smoother',
        name='velocity_smoother',
        output='screen',
        respawn_delay=2.0,
        parameters=[
            velocity_params,
            {'use_sim_time': use_sim_time}
        ],
        remappings=[
            ('cmd_vel', 'cmd_vel_nav'),
            ('cmd_vel_smoothed', 'cmd_vel')
        ]
    )

    smoother_server_node = Node(
        package='nav2_smoother',
        executable='smoother_server',
        name='smoother_server',
        output='screen',
        parameters=[
            smoother_params,
            {'use_sim_time': use_sim_time}
        ],
    )

    bt_navigator_node = Node(
        package='nav2_bt_navigator',
        executable='bt_navigator',
        name='bt_navigator',
        output='screen',
        parameters=[
            bt_params,
            {'use_sim_time': use_sim_time},
            {
                "default_nav_to_pose_bt_xml": os.path.join(
                    current_pkg,
                    "bts",
                    "bt_planner_controller_behavior.xml"
                )
            },
            {
                "default_nav_through_poses_bt_xml": os.path.join(
                    current_pkg,
                    "bts",
                    "bt_planner_controller_behavior_poses.xml"
                )
            }
        ],
    )

    lifecycle_manager_node = Node(
        package='nav2_lifecycle_manager',
        executable='lifecycle_manager',
        name='lifecycle_manager_navigation',
        output='screen',
        parameters=[
            {'use_sim_time': use_sim_time},
            {'autostart': True},
            {
                'node_names': [
                    'bt_navigator',
                    'planner_server',
                    'controller_server',
                    'behavior_server',
                    'waypoint_follower',
                    'velocity_smoother',
                    'smoother_server'
                ]
            }
        ]
    )

    return LaunchDescription([
        use_sim_time_arg,

        lifecycle_manager_node,
        bt_navigator_node,
        planner_server_node,
        controller_server_node,
        behavior_server_node,
        waypoint_node,
        velocity_smoother_node,
        smoother_server_node
    ])
```

该launch文件主要是加载了生命周期管理器、行为树、规划器、控制器、恢复器、航点跟踪、路径平滑以及速度平滑等节点。并且除了生命周期管理器节点外，每个节点都还会加载一个配置文件，接下来需要编辑这些配置文。

**（1）bt_navigator相关配置文件**

bt_navigator相关配置文件有两个，分别是描述行为树的xml文件，以及yaml格式的参数文件，前者存储在bts目录下，后者存储在params目录下。

请在bts目录下，新建一个名为`bt_planner_controller_behavior.xml`的文件并输入如下内容：

```xml
<!--
  This Behavior Tree replans the global path periodically at 1 Hz and it also has
  recovery actions specific to planning / control as well as general system issues.
  This will be continuous if a kinematically valid planner is selected.
-->
<root BTCPP_format="4" main_tree_to_execute="MainTree">
  <BehaviorTree ID="MainTree">
    <RecoveryNode number_of_retries="6" name="NavigateRecovery">
      <PipelineSequence name="NavigateWithReplanning">
        <ControllerSelector selected_controller="{selected_controller}" default_controller="FollowPath" topic_name="controller_selector"/>
        <PlannerSelector selected_planner="{selected_planner}" default_planner="GridBased" topic_name="planner_selector"/>
        <RateController hz="1.0">
          <RecoveryNode number_of_retries="1" name="ComputePathToPose">
            <ComputePathToPose goal="{goal}" path="{path}" planner_id="{selected_planner}" error_code_id="{compute_path_error_code}"/>
            <Sequence>
              <WouldAPlannerRecoveryHelp error_code="{compute_path_error_code}"/>
              <ClearEntireCostmap name="ClearGlobalCostmap-Context" service_name="global_costmap/clear_entirely_global_costmap"/>
            </Sequence>
          </RecoveryNode>
        </RateController>
        <RecoveryNode number_of_retries="1" name="FollowPath">
          <FollowPath path="{path}" controller_id="{selected_controller}" error_code_id="{follow_path_error_code}"/>
          <Sequence>
            <WouldAControllerRecoveryHelp error_code="{follow_path_error_code}"/>
            <ClearEntireCostmap name="ClearLocalCostmap-Context" service_name="local_costmap/clear_entirely_local_costmap"/>
          </Sequence>
        </RecoveryNode>
      </PipelineSequence>
      <Sequence>
        <Fallback>
          <WouldAControllerRecoveryHelp error_code="{follow_path_error_code}"/>
          <WouldAPlannerRecoveryHelp error_code="{compute_path_error_code}"/>
        </Fallback>
        <ReactiveFallback name="RecoveryFallback">
          <GoalUpdated/>
          <RoundRobin name="RecoveryActions">
            <Sequence name="ClearingActions">
              <ClearEntireCostmap name="ClearLocalCostmap-Subtree" service_name="local_costmap/clear_entirely_local_costmap"/>
              <ClearEntireCostmap name="ClearGlobalCostmap-Subtree" service_name="global_costmap/clear_entirely_global_costmap"/>
            </Sequence>
            <Spin spin_dist="1.57" error_code_id="{spin_error_code}"/>
            <Wait wait_duration="5.0"/>
            <BackUp backup_dist="0.30" backup_speed="0.15" error_code_id="{backup_code_id}"/>
          </RoundRobin>
        </ReactiveFallback>
      </Sequence>
    </RecoveryNode>
  </BehaviorTree>
</root>
```

继续在bts目录下新建一个名为`bt_planner_controller_behavior_poses.xml`的文件，并输入如下内容：

```xml
<!--
  This Behavior Tree replans the global path periodically at 1 Hz through an array of poses continuously
   and it also has recovery actions specific to planning / control as well as general system issues.
-->
<root BTCPP_format="4" main_tree_to_execute="MainTree">
  <BehaviorTree ID="MainTree">
    <RecoveryNode number_of_retries="6" name="NavigateRecovery">
      <PipelineSequence name="NavigateWithReplanning">
        <ControllerSelector selected_controller="{selected_controller}" default_controller="FollowPath" topic_name="controller_selector"/>
        <PlannerSelector selected_planner="{selected_planner}" default_planner="GridBased" topic_name="planner_selector"/>
        <RateController hz="0.333">
          <RecoveryNode number_of_retries="1" name="ComputePathThroughPoses">
            <ReactiveSequence>
              <RemovePassedGoals input_goals="{goals}" output_goals="{goals}" radius="0.7"/>
              <ComputePathThroughPoses goals="{goals}" path="{path}" planner_id="{selected_planner}" error_code_id="{compute_path_error_code}"/>
            </ReactiveSequence>
            <Sequence>
              <WouldAPlannerRecoveryHelp error_code="{compute_path_error_code}"/>
              <ClearEntireCostmap name="ClearGlobalCostmap-Context" service_name="global_costmap/clear_entirely_global_costmap"/>
            </Sequence>
          </RecoveryNode>
        </RateController>
        <RecoveryNode number_of_retries="1" name="FollowPath">
          <FollowPath path="{path}" controller_id="{selected_controller}" error_code_id="{follow_path_error_code}"/>
          <Sequence>
            <WouldAControllerRecoveryHelp error_code="{follow_path_error_code}"/>
            <ClearEntireCostmap name="ClearLocalCostmap-Context" service_name="local_costmap/clear_entirely_local_costmap"/>
          </Sequence>
        </RecoveryNode>
      </PipelineSequence>
      <Sequence>
        <Fallback>
          <WouldAControllerRecoveryHelp error_code="{follow_path_error_code}"/>
          <WouldAPlannerRecoveryHelp error_code="{compute_path_error_code}"/>
        </Fallback>
        <ReactiveFallback name="RecoveryFallback">
          <GoalUpdated/>
          <RoundRobin name="RecoveryActions">
            <Sequence name="ClearingActions">
              <ClearEntireCostmap name="ClearLocalCostmap-Subtree" service_name="local_costmap/clear_entirely_local_costmap"/>
              <ClearEntireCostmap name="ClearGlobalCostmap-Subtree" service_name="global_costmap/clear_entirely_global_costmap"/>
            </Sequence>
            <Spin spin_dist="1.57" error_code_id="{spin_error_code}"/>
            <Wait wait_duration="5.0"/>
            <BackUp backup_dist="0.30" backup_speed="0.15" error_code_id="{backup_error_code}"/>
          </RoundRobin>
        </ReactiveFallback>
      </Sequence>
    </RecoveryNode>
  </BehaviorTree>
</root>
```

在params目录下新建一个名为`bt.yaml`的文件，并输入如下内容：

```yaml

/**:
  ros__parameters:
    use_sim_time: True
    global_frame: map
    robot_base_frame: base_link
    odom_topic: /odom
    bt_loop_duration: 10
    default_server_timeout: 20
    wait_for_service_timeout: 1000
    action_server_result_timeout: 900.0
    navigators: ["navigate_to_pose", "navigate_through_poses"]
    navigate_to_pose:
      plugin: "nav2_bt_navigator::NavigateToPoseNavigator"
    navigate_through_poses:
      plugin: "nav2_bt_navigator::NavigateThroughPosesNavigator"
    error_code_names:
      - compute_path_error_code
      - follow_path_error_code
```

**（2）planner_server相关配置文件**

在params目录下新建一个名为`planner.yaml`的文件，并输入如下内容：

```yaml
/**:
  ros__parameters:
    expected_planner_frequency: 20.0
    use_sim_time: True
    planner_plugins: ["GridBased"]
    GridBased:
      plugin: "nav2_navfn_planner::NavfnPlanner"
      tolerance: 0.5
      use_astar: false
      allow_unknown: true
  global_costmap:
    ros__parameters:
      update_frequency: 1.0
      publish_frequency: 1.0
      global_frame: map
      robot_base_frame: base_link
      use_sim_time: True
      footprint: "[[0.19, 0.13], [0.19, -0.13], [-0.19, -0.13], [-0.19, 0.13]]"
      resolution: 0.05
      track_unknown_space: true
      plugins: ["static_layer", "obstacle_layer", "voxel_layer", "inflation_layer"]
      obstacle_layer:
        plugin: "nav2_costmap_2d::ObstacleLayer"
        enabled: True
        observation_sources: scan
        scan:
          topic: /scan
          max_obstacle_height: 2.0
          clearing: True
          marking: True
          data_type: "LaserScan"
          raytrace_max_range: 3.0
          raytrace_min_range: 0.0
          obstacle_max_range: 2.5
          obstacle_min_range: 0.0
      voxel_layer:
        plugin: "nav2_costmap_2d::VoxelLayer"
        enabled: True
        publish_voxel_map: True
        origin_z: 0.0
        z_resolution: 0.05
        z_voxels: 16
        max_obstacle_height: 2.0
        mark_threshold: 0
        observation_sources: scan
        scan:
          topic: /scan
          max_obstacle_height: 2.0
          clearing: True
          marking: True
          data_type: "LaserScan"
          raytrace_max_range: 3.0
          raytrace_min_range: 0.0
          obstacle_max_range: 2.5
          obstacle_min_range: 0.0
      static_layer:
        plugin: "nav2_costmap_2d::StaticLayer"
        map_subscribe_transient_local: True
      inflation_layer:
        plugin: "nav2_costmap_2d::InflationLayer"
        cost_scaling_factor: 4.0
        inflation_radius: 0.55
      always_send_full_costmap: False
```

**（3）controller_server相关配置文件**

在params目录下新建一个名为`controller.yaml`的文件，并输入如下内容：

```yaml
/**:
  ros__parameters:
    use_sim_time: True
    controller_frequency: 10.0
    min_x_velocity_threshold: 0.001
    min_y_velocity_threshold: 0.5
    min_theta_velocity_threshold: 0.001
    failure_tolerance: 1.0
    progress_checker_plugins: ["progress_checker"]
    goal_checker_plugins: ["general_goal_checker"] 
    controller_plugins: ["FollowPath"]

    # Progress checker parameters
    progress_checker:
      plugin: "nav2_controller::SimpleProgressChecker"
      required_movement_radius: 0.5
      movement_time_allowance: 10.0

    general_goal_checker:
      stateful: True
      plugin: "nav2_controller::SimpleGoalChecker"
      xy_goal_tolerance: 0.25
      yaw_goal_tolerance: 0.25

    # DWB parameters
    FollowPath:
      plugin: "dwb_core::DWBLocalPlanner"
      debug_trajectory_details: True
      min_vel_x: 0.0
      min_vel_y: 0.0
      max_vel_x: 0.6
      max_vel_y: 0.0
      max_vel_theta: 1.0
      min_speed_xy: 0.0
      max_speed_xy: 0.6
      min_speed_theta: 0.0
      acc_lim_x: 1.6
      acc_lim_y: 0.0
      acc_lim_theta: 3.2
      decel_lim_x: -1.6
      decel_lim_y: 0.0
      decel_lim_theta: -3.2
      vx_samples: 20
      vy_samples: 5
      vtheta_samples: 20
      sim_time: 1.7
      linear_granularity: 0.05
      angular_granularity: 0.025
      transform_tolerance: 1.0
      xy_goal_tolerance: 0.15
      trans_stopped_velocity: 0.25
      short_circuit_trajectory_evaluation: True
      stateful: True
      critics: ["RotateToGoal", "Oscillation", "BaseObstacle", "GoalAlign", "PathAlign", "PathDist", "GoalDist"]
      BaseObstacle.scale: 0.02
      PathAlign.scale: 32.0
      PathAlign.forward_point_distance: 0.1
      GoalAlign.scale: 24.0
      GoalAlign.forward_point_distance: 0.1
      PathDist.scale: 32.0
      GoalDist.scale: 24.0
      RotateToGoal.scale: 32.0
      RotateToGoal.slowing_factor: 5.0
      RotateToGoal.lookahead_time: -1.0
  local_costmap:
    ros__parameters:
      update_frequency: 5.0
      publish_frequency: 2.0
      global_frame: odom
      robot_base_frame: base_link
      use_sim_time: True
      rolling_window: True
      width: 2
      height: 2
      resolution: 0.05
      footprint: "[[0.19, 0.13], [0.19, -0.13], [-0.19, -0.13], [-0.19, 0.13]]"
      plugins: ["obstacle_layer", "voxel_layer", "inflation_layer"]
      inflation_layer:
        plugin: "nav2_costmap_2d::InflationLayer"
        inflation_radius: 0.5
        cost_scaling_factor: 4.0
      obstacle_layer:
        plugin: "nav2_costmap_2d::ObstacleLayer"
        enabled: True
        observation_sources: scan
        scan:
          topic: /scan
          max_obstacle_height: 2.0
          clearing: True
          marking: True
          data_type: "LaserScan"
      voxel_layer:
        plugin: "nav2_costmap_2d::VoxelLayer"
        enabled: True
        publish_voxel_map: True
        origin_z: 0.0
        z_resolution: 0.05
        z_voxels: 16
        max_obstacle_height: 2.0
        mark_threshold: 0
        observation_sources: scan
        scan:
          topic: /scan
          max_obstacle_height: 2.0
          clearing: True
          marking: True
          data_type: "LaserScan"
          raytrace_max_range: 3.0
          raytrace_min_range: 0.0
          obstacle_max_range: 2.5
          obstacle_min_range: 0.0
      static_layer:
        map_subscribe_transient_local: True
      always_send_full_costmap: True
```

Footprint

*   将毫米转换为米：长0.96m，宽0.45m

*   以机器人中心（`base_link`坐标系原点）为基准，计算矩形顶点坐标：

*说明*：顶点按顺时针或逆时针顺序排列，覆盖机器人长宽边界。（右手坐标系）

例如长960宽450长400

```cpp
footprint: [[0.48, 0.225], [0.48, -0.225], [-0.48, -0.225], [-0.48, 0.225]]
```

**（4）behavior_server相关配置文件**

在params目录下新建一个名为`behavior.yaml`的文件，并输入如下内容：

```yaml
/**:
  ros__parameters:
    local_costmap_topic: local_costmap/costmap_raw
    global_costmap_topic: global_costmap/costmap_raw
    local_footprint_topic: local_costmap/published_footprint
    global_footprint_topic: global_costmap/published_footprint
    cycle_frequency: 5.0
    behavior_plugins: ["spin", "backup", "drive_on_heading", "assisted_teleop", "wait"]
    spin:
      plugin: "nav2_behaviors::Spin"
    backup:
      plugin: "nav2_behaviors::BackUp"
    drive_on_heading:
      plugin: "nav2_behaviors::DriveOnHeading"
    wait:
      plugin: "nav2_behaviors::Wait"
    assisted_teleop:
      plugin: "nav2_behaviors::AssistedTeleop"
    local_frame: odom
    global_frame: map
    robot_base_frame: base_link
    transform_tolerance: 0.1
    use_sim_time: True
    simulate_ahead_time: 2.0
    max_rotational_vel: 1.0
    min_rotational_vel: 0.4
    rotational_acc_lim: 3.2
```

**（5）waypoint_follower相关配置文件**

在params目录下新建一个名为`waypoint.yaml`的文件，并输入如下内容：

```yaml
/**:
  ros__parameters:
    use_sim_time: True
    loop_rate: 20
    stop_on_failure: false
    waypoint_task_executor_plugin: "wait_at_waypoint"
    wait_at_waypoint:
      plugin: "nav2_waypoint_follower::WaitAtWaypoint"
      enabled: True
      waypoint_pause_duration: 5000
```

**（6）velocity_smoother相关配置文件**

在params目录下新建一个名为`velocity.yaml`的文件，并输入如下内容：

```yaml
/**:
  ros__parameters:
    use_sim_time: True
    smoothing_frequency: 20.0
    scale_velocities: False
    feedback: "OPEN_LOOP"
    max_velocity: [0.6, 0.0, 1.0]
    min_velocity: [-0.6, 0.0, -1.0]
    max_accel: [2.5, 0.0, 3.2]
    max_decel: [-2.5, 0.0, -3.2]
    odom_topic: "odom"
    odom_duration: 0.1
    deadband_velocity: [0.0, 0.0, 0.0]
    velocity_timeout: 1.0
```

**（7）smoother_server相关配置文件**

在params目录下新建一个名为`smootherr.yaml`的文件，并输入如下内容：

```yaml
/**:
  ros__parameters:
    costmap_topic: global_costmap/costmap_raw
    footprint_topic: global_costmap/published_footprint
    robot_base_frame: base_link
    transform_timeout: 0.1
    smoother_plugins: ["simple_smoother"]
    simple_smoother:
      plugin: "nav2_smoother::SimpleSmoother"
      tolerance: 1.0e-10
      do_refinement: True
```

**3.launch集成**

导航实现时，需要依赖于地图与定位功能，我们可以在一个launch文件中集成之前的定位launch以及当前编写的导航核心模块的launch，以简化导航功能的启动，在launch目录下新建一个名为`bringup.launch.py`的launch文件，并输入如下内容：

```python
import os

from ament_index_python.packages import get_package_share_directory

from launch import LaunchDescription
from launch.actions import IncludeLaunchDescription, DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
from launch.launch_description_sources import PythonLaunchDescriptionSource


def generate_launch_description():

    use_sim_time_arg = DeclareLaunchArgument(
        'use_sim_time',
        default_value='false'
    )

    amcl_pkg = get_package_share_directory("mycar_localization")
    nav2_pkg = get_package_share_directory("mycar_navigation2_jazzy")

    amcl_launch = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(
                amcl_pkg,
                'launch',
                'mycar_loca.launch.py'
            )
        ),
        launch_arguments={
            'use_sim_time': LaunchConfiguration('use_sim_time')
        }.items()
    )

    nav2_launch = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(
                nav2_pkg,
                'launch',
                'nav2.launch.py'
            )
        ),
        launch_arguments={
            'use_sim_time': LaunchConfiguration('use_sim_time')
        }.items()
    )

    return LaunchDescription([
        use_sim_time_arg,
        amcl_launch,
        nav2_launch
    ])
```

**4.编辑配置文件**

打开`CMakeLists.txt` 并输入如下内容：

```cmake
install(DIRECTORY launch params bts
  DESTINATION share/${PROJECT_NAME}
)
```

**5.编译**

终端中进入当前工作空间，编译功能包：

```bash
colcon build --packages-select mycar_navigation2_jazzy
```

**6.执行**

（1）请先调用如下指令启动仿真环境：

```bash
. install/setup.bash
ros2 launch demo_gazebo_sim gazebo_sim_robot_world.launch.py
```

（2）然后在终端下进入当前工作空间，输入如下指令启动导航功能：

```bash
. install/setup.bash
ros2 launch mycar_navigation2_jazzy bringup.launch.py use_sim_time:=True
```

（3）启动rviz2，加载`/opt/ros/jazzy/share/nav2_bringup/rviz`下的`nav2_default_view.rviz`文件，为机器人设置初始位姿后，再通过菜单栏的`Nav2 Goal`设置目标点，机器人就可以自动导航至目标点了。

```bash
rviz2 -d /opt/ros/jazzy/share/nav2_bringup/rviz/nav2_default_view.rviz
```

*   `rviz2`：启动 `rviz2` 工具。

*   `-d`：指定要加载的 `.rviz` 配置文件。

*   `/opt/ros/jazzy/share/nav2_bringup/rviz/nav2_default_view.rviz`：`.rviz` 配置文件的路径。

运行该命令后，`rviz2` 将启动并加载 `nav2_default_view.rviz` 配置文件。

先使用

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1766.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1767.webp)

如上图，左图是选择初始位置，右图是选择目标位置。

上图中，每个障碍物和墙附近的光圈是危险程度，颜色越红表示机器人经过此地越危险。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1768.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1769.webp)




#### 自主探索SLAM
导航需要依赖于地图与定位，例如在上一节 **导航功能集成** 中，导航实现时就是以launch文件的方式集成了 **定位AMCL** 一节中的定位功能，而机器人SLAM时，也是发布地图数据与定位信息的，所以导航时也可以不借助于amcl而是直接与SLAM结合，达到自主探索的SLAM效果。

**1.编写launch文件**

在功能包mycar_navigation2_jazzy的launch目录下新建名为`auto_slam.launch.py`的launch文件，并输入如下内容：

```python
import os

from ament_index_python.packages import get_package_share_directory

from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument, IncludeLaunchDescription
from launch.conditions import IfCondition
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch.substitutions import LaunchConfiguration, PythonExpression


def generate_launch_description():

    use_sim_time_arg = DeclareLaunchArgument(
        'use_sim_time',
        default_value='false'
    )

    slam_backend_arg = DeclareLaunchArgument(
        'slam_backend',
        default_value='cartographer',
        description='SLAM backend: slam_toolbox or cartographer'
    )

    use_sim_time = LaunchConfiguration('use_sim_time')
    slam_backend = LaunchConfiguration('slam_backend')

    slam_toolbox_pkg = get_package_share_directory("mycar_slam_slam_toolbox")
    cartographer_pkg = get_package_share_directory("mycar_slam_cartographer")
    nav2_pkg = get_package_share_directory("mycar_navigation2_jazzy")

    slam_toolbox_launch = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(
                slam_toolbox_pkg,
                'launch',
                'online_sync_launch.py'
            )
        ),
        launch_arguments={
            'use_sim_time': use_sim_time
        }.items(),
        condition=IfCondition(
            PythonExpression(["'", slam_backend, "' == 'slam_toolbox'"])
        )
    )

    cartographer_launch = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(
                cartographer_pkg,
                'launch',
                'cartographer.launch.py'
            )
        ),
        launch_arguments={
            'use_sim_time': use_sim_time
        }.items(),
        condition=IfCondition(
            PythonExpression(["'", slam_backend, "' == 'cartographer'"])
        )
    )

    nav2_launch = IncludeLaunchDescription(
        PythonLaunchDescriptionSource(
            os.path.join(
                nav2_pkg,
                'launch',
                'nav2.launch.py'
            )
        ),
        launch_arguments={
            'use_sim_time': use_sim_time
        }.items()
    )

    ld = LaunchDescription()
    ld.add_action(use_sim_time_arg)
    ld.add_action(slam_backend_arg)
    ld.add_action(slam_toolbox_launch)
    ld.add_action(cartographer_launch)
    ld.add_action(nav2_launch)
    return ld
```

**2.编译**

终端中进入当前工作空间，编译功能包：

```bash
colcon build --packages-select mycar_navigation2_jazzy
```

**3.执行**

（1）请先调用如下指令启动仿真环境：

```bash
. install/setup.bash
ros2 launch demo_gazebo_sim gazebo_sim_robot_world.launch.py
```

（2）然后在终端下进入当前工作空间，输入如下指令启动自主SLAM功能：

```bash
. install/setup.bash
ros2 launch mycar_navigation2_jazzy auto_slam.launch.py use_sim_time:=True slam_backend:=cartographer
```

（3）启动rviz2，加载`/opt/ros/jazzy/share/nav2_bringup/rviz`下的`nav2_default_view.rviz`文件，再通过菜单栏的`Nav2 Goal`设置目标点，机器人就可以自动导航至目标点，并且导航中还会实现建图的功能。

```bash
rviz2 -d /opt/ros/jazzy/share/nav2_bringup/rviz/nav2_default_view.rviz
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1770.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1771.webp)

### 多车编队
多车编队技术通过先进的传感器、实时数据分析和高度智能化的控制系统，使得一组车辆能够在没有人工干预的情况下实现协同运行，作为一种先进的智能交通系统应用，已经在多个领域展现出其巨大的潜力和广泛的应用场景。比如：

> 时间优化：多车编队技术消除了人为操作中的误差和延误，降低了人力资源成本和运营风险。
> 
> 空间优化：多车编队技术可以减少车辆间的空隙，最大化道路利用率。
> 
> 性能优化：密集编队行驶减少了空气动力学阻力，降低了能耗和燃料消耗。

总之，多车编队技术在机器人领域有着独特的作用和价值。本节将介绍如何在ROS2中实现多车编队。
