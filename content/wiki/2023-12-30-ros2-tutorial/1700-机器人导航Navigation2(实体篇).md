---
title: "机器人导航Navigation2(实体篇)"
---

### 准备工作
1.  实物

2.  已经跑过一遍机器人导航Navigation2(仿真篇)

3.  本章只讲大体思路实现，一般只要你仿真篇搞明白了，实体篇看看大体思路就懂怎么实现了。

4.  本章非赵虚左教程，是自己的实现思路，仅供参考，可能赵老师有更好的办法，不过他还没出实体篇教程。

5.  本章用的是4轮麦克纳姆轮实现的，仅供参考。

以下代码都在下方这个github仓库里：

https://github.com/CyberNaviRobot/CyberRobot_ROS2_WS

*下方的教程只有实现思路，不会放源码，所以建议克隆一下这个仓库，看看源码。*

### 导航参数参考
https://docs.nav2.org/configuration/index.html

### SLAM 定位与建图
#### slam_toolbox
根据上方的节点说明，我们要订阅/scan和/tf。

一般激光雷达的说明书都会提供源码去发布/scan,所以这个请看你硬件的说明书。

/tf则需要我们发布odom_frame到base_frame的转换，我们必须使用C++代码去动态发布odom的坐标变换。

但是这里你需要发布/odom,以便于知道机器人的位置和姿态，这样才能够够推算出机器人在map中的位置。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1806.webp)

确保slam toolbox各项参数没有设置错，特别是坐标系等等，其他参数可以看着说明微调。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1807.webp)

确保激光雷达发布的话题是/scan。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1808.webp)

先启动激光雷达

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1809.webp)

再启动urdf模型，同时发布tf。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1810.webp)

最后开启slam

```bash
ros2 launch mycar_slam_slam_toolbox online_sync_launch.py use_sim_time:=false
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1811.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1812.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1813.webp)

#### cartographer
根据cartographer说明，我们需要/scan和/odom即可。

先打开串口接收节点，接收stm32传过来的数据。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1814.webp)

然后再打开里程计节点，发布odom话题

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1815.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1816.webp)

再发布一下TF，可以直接用launch开启robot_state_publisher和joint_state_publisher,并打开urdf模型来发布TF。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1817.webp)

打开激光雷达的节点，发布scan话题

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1818.webp)

等/odom,/scan和TF全发了之后，再打开cartographer建图，然后可以检查map的TF是否发布。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1819.webp)

检查TF树如下：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1820.webp)

建图如下

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1821.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1822.webp)

### 地图服务
#### 保存地图(序列化)
```bash
mkdir ./map
ros2 run nav2_map_server map_saver_cli -f map/my_map
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1823.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1824.webp)

#### 读取地图(反序列化)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1825.webp)

```bash
ros2 launch mycar_map_server map_server.launch.py
```

### AMCL自适应蒙特卡洛定位
首先需要地图的数据，发布/map话题。

其次需要激光雷达数据，即/scan话题。

然后需要坐标变换消息，即/tf话题。

然后那个/initial_pose话题，是2D地图上的初始位置，可以用rviz2发布，也可以用C++代码发布。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1826.webp)

然后需要修改一下参数：

这个是参数的官方网站：

https://docs.nav2.org/configuration/packages/configuring-amcl.html#

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1827.webp)

修改完Launch后，再修改params参数。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1828.webp)

这里的OmniChassis不止指全向轮底盘，而是广义的全向轮底盘，像全向轮底盘，麦轮底盘都是全向轮底盘。当然也可以自定义底盘类型。

这个配置文件最顶上的那个use_sim_time设置为False。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1829.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1830.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1831.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1832.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1833.webp)

### 导航服务器
涉及的话题太多了，所以我们列出来了一个表：

1.  订阅的话题

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /goal_pose | geometry_msgs/msg/PoseStamped | 导航目标点，用于触发导航任务 |
| /tf | tf2_msgs/msg/TFMessage | 坐标变换消息，用于不同坐标系之间的转换 |
| /odom | nav_msgs/msg/Odometry | 里程计数据，提供机器人位置和运动信息 |
| 话题 | 接口 | 描述 |
| /global_costmap/footprint | geometry_msgs/msg/Polygon | 机器人（或任何移动平台）的足迹（footprint）信息。足迹是机器人在地图上占据的空间形状，通常用多边形表示。 |
| /map | nav_msgs/msg/OccupancyGrid | 发布环境地图，特别是用于导航的占用网格图（Occupancy Grid Map）。 |
| /scan | sensor_msgs/msg/LaserScan | 激光扫描数据。 |
| 话题 | 接口 | 描述 |
| /odom | nav_msgs/msg/Odometry | 机器人的里程计信息，包含位置、速度和姿态 |
| /speed_limit | nav2_msgs/msg/SpeedLimit | 导航过程中的速度限制信息，用于动态调整机器人的移动速度 |
| 话题 | 接口 | 描述 |
| /local_costmap/footprint | geometry_msgs/msg/Polygon | 机器人或移动平台的足迹多边形，用于本地代价地图的计算 |
| /scan | sensor_msgs/msg/LaserScan | 激光扫描仪的扫描数据，用于环境感知和避障 |
| 话题 | 接口 | 描述 |
| /clock | rosgraph_msgs/msg/Clock | ROS系统时间 |
| /cmd_vel_teleop | geometry_msgs/msg/Twist | 遥操作命令，用于控制机器人的线性和角速度 |
| /local_costmap/costmap_raw | nav2_msgs/msg/Costmap | 局部代价地图的原始数据 |
| /local_costmap/published_footprint | geometry_msgs/msg/PolygonStamped | 机器人在局部代价地图中的已发布足迹 |
| /preempt_teleop | std_msgs/msg/Empty | 遥操作抢占信号，用于中断当前遥操作 |
| 话题 | 接口 | 描述 |
| /global_costmap/costmap_raw | nav2_msgs/msg/Costmap | 全局代价地图的原始数据，用于路径规划 |
| /global_costmap/published_footprint | geometry_msgs/msg/PolygonStamped | 机器人在全局代价地图中的足迹表示 |
| 话题 | 接口 | 描述 |
| /cmd_vel_nav | geometry_msgs/msg/Twist | 接收来自其他节点的速度控制指令的话题 |

2.  发布的话题

| 话题 | 接口 | 描述 |
|:---|:---|:---|
| /plan | nav_msgs/msg/Path | 当前位置到目标点的全局路径 |
| 话题 | 接口 | 描述 |
| /global_costmap/costmap | nav_msgs/msg/OccupancyGrid | 发布全局代价地图的当前状态。 |
| /global_costmap/costmap_raw | nav2_msgs/msg/Costmap | 未经进一步处理的原始代价地图数据。 |
| /global_costmap/costmap_updates | map_msgs/msg/OccupancyGridUpdate | 全局代价地图的更新，该消息可以高效更新地图。 |
| /global_costmap/published_footprint | geometry_msgs/msg/PolygonStamped | 发布机器人的足迹（footprint），即机器人在地图上占据的空间形状。 |
| 话题名称 | 消息类型 | 描述 |
| /cmd_vel_nav | geometry_msgs/msg/Twist | 发布控制命令，包括线性和角速度，用于控制机器人按照规划路径移动。 |
| /cost_cloud | sensor_msgs/msg/PointCloud2 | 发布成本地图中的点云数据，用于避障和路径规划。 |
| /local_plan | nav_msgs/msg/Path | 发布局部路径规划结果，即机器人应如何到达当前目标点附近的一个点。 |
| /marker | visualization_msgs/msg/MarkerArray | 发布可视化标记，用于在RViz等可视化工具中显示路径、障碍物等信息。 |
| /received_global_plan | nav_msgs/msg/Path | 发布从全局规划器接收到的全局路径，即当前位置到目标点的路径。 |
| /transformed_global_plan | nav_msgs/msg/Path | 发布经过坐标变换的全局路径，确保路径与机器人的当前坐标系一致。 |
| 话题 | 接口 | 描述 |
| /local_costmap/clearing_endpoints | sensor_msgs/msg/PointCloud2 | 清除成本图上的障碍物点云数据，通常用于动态障碍物处理 |
| /local_costmap/costmap | nav_msgs/msg/OccupancyGrid | 本地成本图，表示机器人周围环境的可通行性 |
| /local_costmap/costmap_raw | nav2_msgs/msg/Costmap | 未经处理的本地成本图，可能包含更详细的信息 |
| /local_costmap/costmap_updates | map_msgs/msg/OccupancyGridUpdate | 本地成本图的更新信息，包括哪些区域发生了变化 |
| /local_costmap/published_footprint | geometry_msgs/msg/PolygonStamped | 发布的机器人足迹多边形，时间戳表示发布时间 |
| /local_costmap/voxel_grid | nav2_msgs/msg/VoxelGrid | 体素网格数据，用于成本图生成中的空间划分和优化 |
| 话题 | 接口 | 描述 |
| /cmd_vel | geometry_msgs/msg/Twist | 发送给底层控制器的速度命令 |
| 话题 | 接口 | 描述 |
| /plan_smoothed | nav_msgs/msg/Path | 经过平滑处理后的全局路径 |
| 话题 | 接口 | 描述 |
| /cmd_vel | geometry_msgs/msg/Twist | 发布经过处理或平滑后的速度控制指令的话题 |

由于赵虚左是把官方的源码重新写在了WS里，这样对于初学者来说会比较麻烦，对于初学者来说建议使用官方写好的bringup节点，以下是我根据官方Wiki总结出来的使用方法：（这里选择使用官方的bringup节点，而不是赵虚左老师的节点。）

以下是配置的`nav2.launch.py`文件：

```python
import os

from ament_index_python.packages import get_package_share_directory
from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch.substitutions import LaunchConfiguration

# from launch_ros.actions import Node

def generate_launch_description():
    navigation2_dir = get_package_share_directory('nav05_navigation2')
    nav2_bringup_dir = get_package_share_directory('nav2_bringup')

    # launch的参数的优先级比yaml的参数优先级高
    use_sim_time = LaunchConfiguration('use_sim_time', default='flase')
    map_yaml_path = LaunchConfiguration('map',default=os.path.join(navigation2_dir,'map','house.yaml'))
    nav2_param_path = LaunchConfiguration('params_file',default=os.path.join(navigation2_dir,'params','nav2.yaml'))

    return LaunchDescription([
        DeclareLaunchArgument('use_sim_time',default_value=use_sim_time,description='Use simulation (Gazebo) clock if true'),
        DeclareLaunchArgument('map',default_value=map_yaml_path,description='Full path to map file to load'),
        DeclareLaunchArgument('params_file',default_value=nav2_param_path,description='Full path to param file to load'),

        IncludeLaunchDescription(
            PythonLaunchDescriptionSource([nav2_bringup_dir,'/launch','/bringup_launch.py']),
            launch_arguments={
                'map': map_yaml_path,
                'use_sim_time': use_sim_time,
                'params_file': nav2_param_path}.items(),
        ),
    ])
```

以下是`rviz2.launch.py`：

```python
import os

from ament_index_python.packages import get_package_share_directory
from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
from launch_ros.actions import Node

def generate_launch_description():
    navigation2_dir = get_package_share_directory('nav05_navigation2')

    use_sim_time = LaunchConfiguration('use_sim_time', default='false')

    rviz_config_dir = os.path.join(navigation2_dir,'rviz','nav2.rviz')

    return LaunchDescription([
        DeclareLaunchArgument('use_sim_time',default_value=use_sim_time,description='Use simulation (Gazebo) clock if true'),

        Node(
            package='rviz2',
            executable='rviz2',
            name='rviz2',
            arguments=['-d', rviz_config_dir],
            parameters=[{'use_sim_time': use_sim_time}],
            output='screen'),
    ])
```

以下是`nav2.yaml`配置文件（差速模型+DWE局部规划器）：

```python
amcl:
  ros__parameters:
    use_sim_time: false
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
    transform_tolerance: 1.0
    update_min_a: 0.2
    update_min_d: 0.25
    z_hit: 0.5
    z_max: 0.05
    z_rand: 0.5
    z_short: 0.05
    scan_topic: scan

amcl_map_client:
  ros__parameters:
    use_sim_time: false

amcl_rclcpp_node:
  ros__parameters:
    use_sim_time: false

bt_navigator:
  ros__parameters:
    use_sim_time: false
    global_frame: map
    robot_base_frame: base_link
    odom_topic: /odom
    bt_loop_duration: 10
    default_server_timeout: 20

    # 'default_nav_through_poses_bt_xml' and 'default_nav_to_pose_bt_xml' are use defaults:

    # nav2_bt_navigator/navigate_to_pose_w_replanning_and_recovery.xml

    # nav2_bt_navigator/navigate_through_poses_w_replanning_and_recovery.xml

    # They can be set here or via a RewrittenYaml remap from a parent launch file to Nav2.
    plugin_lib_names:
    - nav2_compute_path_to_pose_action_bt_node
    - nav2_compute_path_through_poses_action_bt_node
    - nav2_smooth_path_action_bt_node
    - nav2_follow_path_action_bt_node
    - nav2_spin_action_bt_node
    - nav2_wait_action_bt_node
    - nav2_back_up_action_bt_node
    - nav2_drive_on_heading_bt_node
    - nav2_clear_costmap_service_bt_node
    - nav2_is_stuck_condition_bt_node
    - nav2_goal_reached_condition_bt_node
    - nav2_goal_updated_condition_bt_node
    - nav2_globally_updated_goal_condition_bt_node
    - nav2_is_path_valid_condition_bt_node
    - nav2_initial_pose_received_condition_bt_node
    - nav2_reinitialize_global_localization_service_bt_node
    - nav2_rate_controller_bt_node
    - nav2_distance_controller_bt_node
    - nav2_speed_controller_bt_node
    - nav2_truncate_path_action_bt_node
    - nav2_truncate_path_local_action_bt_node
    - nav2_goal_updater_node_bt_node
    - nav2_recovery_node_bt_node
    - nav2_pipeline_sequence_bt_node
    - nav2_round_robin_node_bt_node
    - nav2_transform_available_condition_bt_node
    - nav2_time_expired_condition_bt_node
    - nav2_path_expiring_timer_condition
    - nav2_distance_traveled_condition_bt_node
    - nav2_single_trigger_bt_node
    - nav2_is_battery_low_condition_bt_node
    - nav2_navigate_through_poses_action_bt_node
    - nav2_navigate_to_pose_action_bt_node
    - nav2_remove_passed_goals_action_bt_node
    - nav2_planner_selector_bt_node
    - nav2_controller_selector_bt_node
    - nav2_goal_checker_selector_bt_node
    - nav2_controller_cancel_bt_node
    - nav2_path_longer_on_approach_bt_node
    - nav2_wait_cancel_bt_node
    - nav2_spin_cancel_bt_node
    - nav2_back_up_cancel_bt_node
    - nav2_drive_on_heading_cancel_bt_node

bt_navigator_rclcpp_node:
  ros__parameters:
    use_sim_time: false

controller_server:
  ros__parameters:
    use_sim_time: false
    controller_frequency: 20.0
    min_x_velocity_threshold: 0.001
    min_y_velocity_threshold: 0.5
    min_theta_velocity_threshold: 0.001
    failure_tolerance: 0.3
    progress_checker_plugin: "progress_checker"
    goal_checker_plugins: ["general_goal_checker"] # "precise_goal_checker"
    controller_plugins: ["FollowPath"]

    # Progress checker parameters
    progress_checker:
      plugin: "nav2_controller::SimpleProgressChecker"
      required_movement_radius: 0.5
      movement_time_allowance: 10.0

    # Goal checker parameters
    #precise_goal_checker:

    #  plugin: "nav2_controller::SimpleGoalChecker"

    #  xy_goal_tolerance: 0.25

    #  yaw_goal_tolerance: 0.25

    #  stateful: True
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
      max_vel_x: 0.26
      max_vel_y: 0.0
      max_vel_theta: 1.0
      min_speed_xy: 0.0
      max_speed_xy: 0.26
      min_speed_theta: 0.0

      # Add high threshold velocity for turtlebot 3 issue.

      # https://github.com/ROBOTIS-GIT/turtlebot3_simulations/issues/75
      acc_lim_x: 2.5
      acc_lim_y: 0.0
      acc_lim_theta: 3.2
      decel_lim_x: -2.5
      decel_lim_y: 0.0
      decel_lim_theta: -3.2
      vx_samples: 20
      vy_samples: 5
      vtheta_samples: 20
      sim_time: 1.7
      linear_granularity: 0.05
      angular_granularity: 0.025
      transform_tolerance: 0.2
      xy_goal_tolerance: 0.25
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

controller_server_rclcpp_node:
  ros__parameters:
    use_sim_time: false

local_costmap:
  local_costmap:
    ros__parameters:
      update_frequency: 5.0
      publish_frequency: 2.0
      global_frame: odom
      robot_base_frame: base_link
      use_sim_time: false
      rolling_window: true
      width: 3
      height: 3
      resolution: 0.05
      robot_radius: 0.22
      plugins: ["static_layer", "obstacle_layer", "voxel_layer", "inflation_layer"]
      inflation_layer:
        plugin: "nav2_costmap_2d::InflationLayer"
        cost_scaling_factor: 3.0
        inflation_radius: 0.55
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
      always_send_full_costmap: True

  local_costmap_client:
    ros__parameters:
      use_sim_time: false
  local_costmap_rclcpp_node:
    ros__parameters:
      use_sim_time: false

global_costmap:
  global_costmap:
    ros__parameters:
      update_frequency: 1.0
      publish_frequency: 1.0
      global_frame: map
      robot_base_frame: base_link
      use_sim_time: false
      robot_radius: 0.22
      resolution: 0.05
      track_unknown_space: true
      plugins: ["static_layer", "obstacle_layer", "inflation_layer"]
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
      static_layer:
        plugin: "nav2_costmap_2d::StaticLayer"
        map_subscribe_transient_local: True
      inflation_layer:
        plugin: "nav2_costmap_2d::InflationLayer"
        cost_scaling_factor: 3.0
        inflation_radius: 0.55
      always_send_full_costmap: True

  global_costmap_client:
    ros__parameters:
      use_sim_time: false
  global_costmap_rclcpp_node:
    ros__parameters:
      use_sim_time: false

map_server:
  ros__parameters:
    use_sim_time: false
    yaml_filename: "house.yaml"

map_saver:
  ros__parameters:
    use_sim_time: false
    save_map_timeout: 5.0
    free_thresh_default: 0.25
    occupied_thresh_default: 0.65
    map_subscribe_transient_local: True

planner_server:
  ros__parameters:
    expected_planner_frequency: 20.0
    use_sim_time: false
    planner_plugins: ["GridBased"]
    GridBased:
      plugin: "nav2_navfn_planner/NavfnPlanner"
      tolerance: 0.5
      use_astar: false
      allow_unknown: true

planner_server_rclcpp_node:
  ros__parameters:
    use_sim_time: false

smoother_server:
  ros__parameters:
    use_sim_time: false
    smoother_plugins: ["simple_smoother"]
    simple_smoother:
      plugin: "nav2_smoother::SimpleSmoother"
      tolerance: 1.0e-10
      max_its: 1000
      do_refinement: True

behavior_server:
  ros__parameters:
    costmap_topic: local_costmap/costmap_raw
    footprint_topic: local_costmap/published_footprint
    cycle_frequency: 10.0
    behavior_plugins: ["spin", "backup", "drive_on_heading", "wait"]
    spin:
      plugin: "nav2_behaviors/Spin"
    backup:
      plugin: "nav2_behaviors/BackUp"
    drive_on_heading:
      plugin: "nav2_behaviors/DriveOnHeading"
    wait:
      plugin: "nav2_behaviors/Wait"
    global_frame: odom
    robot_base_frame: base_link
    transform_tolerance: 0.1
    use_sim_time: false
    simulate_ahead_time: 2.0
    max_rotational_vel: 1.0
    min_rotational_vel: 0.4
    rotational_acc_lim: 3.2

robot_state_publisher:
  ros__parameters:
    use_sim_time: false

waypoint_follower:
  ros__parameters:
    loop_rate: 20
    stop_on_failure: false
    waypoint_task_executor_plugin: "wait_at_waypoint"
    wait_at_waypoint:
      plugin: "nav2_waypoint_follower::WaitAtWaypoint"
      enabled: True
      waypoint_pause_duration: 200

velocity_smoother:
  ros__parameters:
    use_sim_time: false
    smoothing_frequency: 20.0
    scale_velocities: False
    feedback: "OPEN_LOOP"
    max_velocity: [0.26, 0.0, 1.0]
    min_velocity: [-0.26, 0.0, -1.0]
    max_accel: [2.5, 0.0, 3.2]
    max_decel: [-2.5, 0.0, -3.2]
    odom_topic: "odom"
    odom_duration: 0.1
    deadband_velocity: [0.0, 0.0, 0.0]
    velocity_timeout: 1.0
```

### 多车编队
