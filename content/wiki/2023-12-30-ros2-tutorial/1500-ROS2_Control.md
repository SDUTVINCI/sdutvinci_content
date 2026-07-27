---
title: "ROS2_Control"
---

### ROS2 Control 概述

#### ROS2 Control 简述

**概念**

`ros2_control` 是 ROS 2 中用于机器人控制的标准框架。它的核心思想是把机器人控制拆成三层：

*   **控制器（Controller）** ：负责控制算法。例如差速底盘控制器把 `cmd_vel` 转成左右轮速度，轨迹控制器把关节轨迹转成关节命令。
*   **控制器管理器（Controller Manager）** ：负责加载、配置、激活、停止控制器，并在固定频率下执行 `read -> update -> write` 控制循环。
*   **硬件接口（Hardware Interface）** ：负责和真实硬件、仿真硬件或模拟硬件通信，让控制器不用关心底层串口、CAN、EtherCAT、Gazebo 或自定义驱动。

简单说，`ros2_control` 是 ROS 2 中“控制器”和“硬件”之间的标准插座。控制器只面对标准接口，硬件只暴露标准接口，中间由 `controller_manager` 管理。

在 Ubuntu 24.04 + ROS 2 Jazzy 中，ROS 2 的默认安装路径是：

```bash
/opt/ros/jazzy
```

常见 ros2_control 包路径如下：

| 软件包 | 路径 | 说明 |
|:---|:---|:---|
| `ros2_control` | `/opt/ros/jazzy/share/ros2_control` | ros2_control 元包 |
| `controller_manager` | `/opt/ros/jazzy/share/controller_manager` | 控制器管理器，提供 `ros2_control_node`、`spawner` 等工具 |
| `controller_interface` | `/opt/ros/jazzy/share/controller_interface` | 自定义控制器要继承的基础接口 |
| `hardware_interface` | `/opt/ros/jazzy/share/hardware_interface` | 自定义硬件接口要继承的基础接口 |
| `ros2_controllers` | `/opt/ros/jazzy/share/ros2_controllers` | 官方常用控制器元包 |
| `ros2controlcli` | `/opt/ros/jazzy/share/ros2controlcli` | 提供 `ros2 control ...` 命令 |

#### ROS2 Control 解决了什么问题

如果不用 `ros2_control`，机器人控制程序很容易写成这样：

*   节点直接订阅 `/cmd_vel`；
*   节点自己计算左右轮速度；
*   节点自己读编码器；
*   节点自己写串口或 CAN；
*   换硬件时整套控制节点都要改；
*   从仿真迁移到真机时还要再改一遍。

使用 `ros2_control` 后，推荐的结构是：

*   控制器负责算法，例如差速运动学、机械臂轨迹插值、PID、夹爪控制。
*   硬件接口负责通信，例如串口协议、CAN 报文、驱动板寄存器、Gazebo 插件。
*   URDF 负责声明机器人有哪些 joint、command interface 和 state interface。
*   YAML 负责声明加载哪些控制器以及控制器参数。
*   `controller_manager` 负责把它们组合起来。

这样做的好处是：控制器可以复用，硬件可以替换，仿真和真机可以共用大部分上层配置。

### ROS2 Control 安装

#### 基础安装

Ubuntu 24.04 使用 ROS 2 Jazzy 时，安装命令如下：

```bash
sudo apt update
sudo apt install ros-jazzy-ros2-control ros-jazzy-ros2-controllers
```

这两个包已经能满足大多数真机控制、控制器开发和基础学习。如果想把本章的调试工具、Gazebo Sim 示例、测试辅助包也装齐，建议继续安装：

```bash
sudo apt install \
  ros-jazzy-gz-ros2-control \
  ros-jazzy-gz-ros2-control-demos \
  ros-jazzy-rqt-controller-manager \
  ros-jazzy-rqt-joint-trajectory-controller \
  ros-jazzy-ros2-controllers-test-nodes \
  ros-jazzy-hardware-interface-testing \
  ros-jazzy-joint-state-topic-hardware-interface \
  ros-jazzy-battery-state-broadcaster
```

其中 `ros-jazzy-gz-ros2-control` 是 Gazebo Sim 接入 ros2_control 的核心包，`ros-jazzy-gz-ros2-control-demos` 提供官方可运行示例，两个 `rqt` 包提供图形化控制器管理和轨迹发送工具。后面几个包不是写普通机器人必需的，但对阅读示例、测试硬件接口、学习 broadcaster 很有帮助。

每次打开新终端后，都需要 source ROS 2 环境：

```bash
source /opt/ros/jazzy/setup.bash
```

#### 验证安装

可以查看基础包：

```bash
ros2 pkg list | grep -E '^(ros2_control|controller_manager|hardware_interface|controller_interface|ros2controlcli)$'
```

正常情况下可以看到：

```bash
controller_interface
controller_manager
hardware_interface
ros2_control
ros2controlcli
```

可以查看常用控制器：

```bash
ros2 pkg list | grep -E '^(joint_state_broadcaster|diff_drive_controller|joint_trajectory_controller|forward_command_controller|position_controllers|velocity_controllers|effort_controllers|pid_controller|mecanum_drive_controller|ackermann_steering_controller|tricycle_controller|omni_wheel_drive_controller)$'
```

常用输出包括：

```bash
ackermann_steering_controller
diff_drive_controller
effort_controllers
forward_command_controller
joint_state_broadcaster
joint_trajectory_controller
mecanum_drive_controller
omni_wheel_drive_controller
pid_controller
position_controllers
tricycle_controller
velocity_controllers
```

这些包名第一次看会比较抽象，可以先按下面这样理解：

| 包名 | 中文理解 | 主要用途 | 典型输入 | 典型输出 |
|:---|:---|:---|:---|:---|
| `joint_state_broadcaster` | 关节状态发布器 | 读取硬件 state interfaces，发布 `/joint_states` 和 `/dynamic_joint_states` | 硬件中的 `position`、`velocity`、`effort` 等状态接口 | ROS 话题，不写硬件命令 |
| `diff_drive_controller` | 差速底盘控制器 | 控制两侧轮子差速运动的小车 | `geometry_msgs/msg/TwistStamped` 速度命令 | 左右轮 `velocity` command interface，另发布 odom |
| `joint_trajectory_controller` | 关节轨迹控制器 | 执行机械臂、云台、多关节机构的关节空间轨迹 | `trajectory_msgs/msg/JointTrajectory` 或 `FollowJointTrajectory` action | 一组关节的 `position`、`velocity` 或 `effort` 命令 |
| `forward_command_controller` | 命令转发控制器 | 把收到的数组命令直接转发到硬件接口，适合测试硬件 | `std_msgs/msg/Float64MultiArray` | 指定 joint 的指定 command interface |
| `position_controllers` | 位置命令组控制器 | 对多个关节直接下发位置目标 | 位置数组 | 多个 joint 的 `position` command interface |
| `velocity_controllers` | 速度命令组控制器 | 对多个关节直接下发速度目标 | 速度数组 | 多个 joint 的 `velocity` command interface |
| `effort_controllers` | 力/力矩命令组控制器 | 对多个关节直接下发 effort 目标 | effort 数组 | 多个 joint 的 `effort` command interface |
| `pid_controller` | PID 控制器 | 在 ros2_control 链路中做 PID 闭环控制 | reference interface 或控制器链输入 | PID 计算后的 command interface |
| `mecanum_drive_controller` | 麦克纳姆轮底盘控制器 | 控制四轮麦克纳姆底盘，实现前后、横移、旋转 | 通常是底盘速度命令 | 四个麦克纳姆轮的 `velocity` command interface |
| `ackermann_steering_controller` | 阿克曼转向控制器 | 控制汽车式前轮转向、后轮驱动结构 | 底盘速度/转向相关命令 | 转向关节位置命令和驱动轮速度命令 |
| `tricycle_controller` | 三轮车控制器 | 控制一个转向轮加驱动轮的三轮底盘 | 底盘速度命令 | 转向关节和驱动轮命令 |
| `omni_wheel_drive_controller` | 全向轮底盘控制器 | 控制三个或更多全向轮组成的全向底盘 | 底盘速度命令 | 多个全向轮的 `velocity` command interface |

这里有一个很重要的区别：`joint_state_broadcaster` 是 broadcaster，它只发布状态，不负责让机器人动；`diff_drive_controller`、`joint_trajectory_controller`、`mecanum_drive_controller` 这类才是 controller，会占用 command interface 并向硬件写命令。`position_controllers`、`velocity_controllers`、`effort_controllers` 和 `forward_command_controller` 更像“直接转发命令”的工具，适合调试硬件接口，但不会替你计算复杂运动学。

如果安装了 Gazebo Sim 和调试辅助包，还可以检查：

```bash
ros2 pkg list | grep -E '^(gz_ros2_control|gz_ros2_control_demos|rqt_controller_manager|rqt_joint_trajectory_controller|hardware_interface_testing|joint_state_topic_hardware_interface|battery_state_broadcaster)$'
```

正常会看到：

```bash
battery_state_broadcaster
gz_ros2_control
gz_ros2_control_demos
hardware_interface_testing
joint_state_topic_hardware_interface
rqt_controller_manager
rqt_joint_trajectory_controller
```

`controller_manager` 包提供了四个常用可执行文件：

```bash
ros2 pkg executables controller_manager
```

输出应包含：

```bash
controller_manager hardware_spawner
controller_manager ros2_control_node
controller_manager spawner
controller_manager unspawner
```

图形化调试工具可以这样启动：

```bash
ros2 run rqt_controller_manager rqt_controller_manager
ros2 run rqt_joint_trajectory_controller rqt_joint_trajectory_controller
```

`rqt_controller_manager` 适合查看、加载、配置、激活和停止控制器；`rqt_joint_trajectory_controller` 适合给 `joint_trajectory_controller` 手动发送简单关节轨迹。

### ROS2 Control 核心概念

#### command interface 和 state interface

学习 ros2_control 最重要的是理解接口。

*   **command interface** ：控制器写入的目标值。例如电机目标速度、关节目标位置、关节目标力矩。
*   **state interface** ：硬件读回来的状态值。例如编码器位置、当前速度、力矩、电流、温度。

Jazzy 中标准接口名定义在：

```bash
/opt/ros/jazzy/include/hardware_interface/hardware_interface/types/hardware_interface_type_values.hpp
```

常用接口如下：

| 接口名 | 含义 |
|:---|:---|
| `position` | 位置，常用于关节角度或直线位移 |
| `velocity` | 速度，常用于轮速或关节速度 |
| `effort` | 力或力矩，常用于力矩控制 |
| `acceleration` | 加速度 |
| `current` | 电流 |
| `temperature` | 温度 |

例如一个轮子关节可以这样声明：

```xml
<joint name="left_wheel_joint">
  <command_interface name="velocity"/>
  <state_interface name="position"/>
  <state_interface name="velocity"/>
</joint>
```

含义是：控制器可以给 `left_wheel_joint/velocity` 写速度命令；硬件接口要能读回 `left_wheel_joint/position` 和 `left_wheel_joint/velocity`。

#### 控制循环

`controller_manager` 的核心循环是：

```text
read() -> update() -> write()
```

具体含义如下：

| 阶段 | 执行者 | 作用 |
|:---|:---|:---|
| `read()` | 硬件接口 | 从电机、编码器、传感器或仿真器读取状态 |
| `update()` | 控制器 | 根据状态和目标计算新的命令 |
| `write()` | 硬件接口 | 把命令写给电机驱动器、仿真器或底层硬件 |

例如差速底盘：

*   `read()` 读取左右轮编码器，更新左右轮 position/velocity。
*   `diff_drive_controller.update()` 根据 `/diff_drive_controller/cmd_vel` 和轮子反馈计算左右轮目标速度，并发布里程计。
*   `write()` 把左右轮目标速度写到电机驱动板。

#### 生命周期状态

ros2_control 的控制器和硬件组件都有生命周期。常见状态如下：

| 状态 | 含义 |
|:---|:---|
| `unconfigured` | 已加载，但还没有配置 |
| `inactive` | 已配置，但不参与控制循环输出 |
| `active` | 已激活，参与控制循环 |
| `finalized` | 已结束 |

常用流程是：

```text
load -> configure -> activate
```

`spawner` 默认会帮控制器完成加载、配置和激活。如果只想加载不激活，可以使用 `--load-only` 或 `--inactive`。

### URDF 中的 ros2_control 标签

#### 基本结构

ros2_control 的硬件描述写在 URDF 或 xacro 的 `<ros2_control>` 标签中。

最小结构如下：

```xml
<ros2_control name="MyRobotSystem" type="system">
  <hardware>
    <plugin>硬件插件名</plugin>
  </hardware>

  <joint name="关节名">
    <command_interface name="命令接口名"/>
    <state_interface name="状态接口名"/>
  </joint>
</ros2_control>
```

`type` 有三种常用取值：

| type | 对应 C++ 基类 | 适用对象 |
|:---|:---|:---|
| `system` | `hardware_interface::SystemInterface` | 多关节机器人、底盘、机械臂 |
| `sensor` | `hardware_interface::SensorInterface` | 只读传感器 |
| `actuator` | `hardware_interface::ActuatorInterface` | 单个执行器 |

#### 使用 mock_components 测试

Jazzy 的 `hardware_interface` 包提供了一个测试用 mock 硬件插件：

```bash
/opt/ros/jazzy/share/hardware_interface/mock_components_plugin_description.xml
```

插件名是：

```bash
mock_components/GenericSystem
```

可以用它先验证控制器配置，而不用立即接真实硬件。

差速底盘示例：

```xml
<ros2_control name="DiffBotSystem" type="system">
  <hardware>
    <plugin>mock_components/GenericSystem</plugin>
  </hardware>

  <joint name="left_wheel_joint">
    <command_interface name="velocity"/>
    <state_interface name="position"/>
    <state_interface name="velocity"/>
  </joint>

  <joint name="right_wheel_joint">
    <command_interface name="velocity"/>
    <state_interface name="position"/>
    <state_interface name="velocity"/>
  </joint>
</ros2_control>
```

注意三点：

*   `left_wheel_joint` 和 `right_wheel_joint` 必须是 URDF 中真实存在的关节名。
*   控制器 YAML 里的关节名必须和 URDF 完全一致。
*   `diff_drive_controller` 输出轮子 `velocity` 命令，所以轮关节必须有 `velocity` command interface。

#### `<param>` 传硬件参数

真实硬件通常需要串口名、波特率、CAN 设备名、减速比等参数，可以写在 `<hardware>` 中：

```xml
<hardware>
  <plugin>my_robot_hardware/MyRobotSystem</plugin>
  <param name="serial_port">/dev/ttyUSB0</param>
  <param name="baud_rate">115200</param>
  <param name="gear_ratio">30.0</param>
</hardware>
```

在自定义硬件接口的 `on_init(const hardware_interface::HardwareInfo & info)` 中，可以通过 `info.hardware_parameters` 读取这些参数。

### Controller Manager

#### controller_manager 配置

`controller_manager` 是 ros2_control 的核心节点。它的默认可执行文件是：

```bash
ros2 run controller_manager ros2_control_node
```

在实际工程中通常通过 launch 启动。控制器配置一般写在 YAML 文件中，例如 `config/controllers.yaml`：

```yaml
controller_manager:
  ros__parameters:
    update_rate: 100

    joint_state_broadcaster:
      type: joint_state_broadcaster/JointStateBroadcaster

    diff_drive_controller:
      type: diff_drive_controller/DiffDriveController
```

`controller_manager` 常用参数：

| 参数 | 默认值 | 说明 |
|:---|:---|:---|
| `update_rate` | `100` | 控制循环频率，单位 Hz |
| `enforce_command_limits` | `false` | 是否根据机器人描述中的关节限制约束命令 |
| `handle_exceptions` | `true` | 是否捕获控制器和硬件组件操作中的异常 |
| `hardware_components_initial_state.unconfigured` | `[]` | 指定启动后停留在 unconfigured 的硬件组件 |
| `hardware_components_initial_state.inactive` | `[]` | 指定启动后停留在 inactive 的硬件组件 |

#### launch 启动方式

典型 launch 写法如下：

```python
from launch import LaunchDescription
from launch_ros.actions import Node


def generate_launch_description():
    control_node = Node(
        package="controller_manager",
        executable="ros2_control_node",
        parameters=[
            {"robot_description": "<这里传入完整 URDF 字符串>"},
            "config/controllers.yaml",
        ],
        output="both",
    )

    return LaunchDescription([control_node])
```

实际项目中，`robot_description` 通常由 xacro 生成。一般流程是：

```text
xacro 文件 -> robot_description 参数 -> robot_state_publisher
                                  -> ros2_control_node
```

### 控制器加载与调试命令

#### spawner

`spawner` 用于加载控制器。默认会完成加载、配置、激活三步：

```bash
ros2 run controller_manager spawner joint_state_broadcaster
ros2 run controller_manager spawner diff_drive_controller
```

如果 controller manager 名字不是默认的 `/controller_manager`，可以用 `-c` 指定：

```bash
ros2 run controller_manager spawner joint_state_broadcaster -c /controller_manager
```

常用选项如下：

| 选项 | 作用 |
|:---|:---|
| `-c` / `--controller-manager` | 指定 controller manager 节点名 |
| `-p` / `--param-file` | 给控制器加载参数文件 |
| `--load-only` | 只加载，不配置、不激活 |
| `--inactive` | 加载并配置，但不激活 |
| `--activate-as-group` | 一组控制器一起激活，适合链式控制器 |
| `--controller-manager-timeout` | 等待 controller manager 服务的超时时间 |
| `--switch-timeout` | 等待控制器切换完成的超时时间 |
| `--unload-on-kill` | spawner 退出时卸载控制器 |

#### hardware_spawner

`hardware_spawner` 用于配置或激活硬件组件：

```bash
ros2 run controller_manager hardware_spawner DiffBotSystem --configure
ros2 run controller_manager hardware_spawner DiffBotSystem --activate
```

常用选项如下：

| 选项 | 作用 |
|:---|:---|
| `--configure` | 把硬件组件配置到 inactive |
| `--activate` | 配置并激活硬件组件 |
| `-c` / `--controller-manager` | 指定 controller manager 节点名 |

#### ros2 control 命令

`ros2controlcli` 提供了 `ros2 control` 命令。

查看控制器：

```bash
ros2 control list_controllers
```

查看控制器详细接口：

```bash
ros2 control list_controllers --verbose
```

查看硬件组件：

```bash
ros2 control list_hardware_components
```

查看硬件接口：

```bash
ros2 control list_hardware_interfaces
ros2 control list_hardware_interfaces --verbose
```

查看可用控制器类型：

```bash
ros2 control list_controller_types
```

切换控制器：

```bash
ros2 control switch_controllers --activate diff_drive_controller --deactivate old_controller
```

卸载控制器：

```bash
ros2 control unload_controller diff_drive_controller
```

生成链式控制器图：

```bash
ros2 control view_controller_chains
```

### 常用控制器

#### Joint State Broadcaster

`joint_state_broadcaster` 用于发布机器人关节状态。插件描述文件是：

```bash
/opt/ros/jazzy/share/joint_state_broadcaster/joint_state_plugin.xml
```

插件名是：

```bash
joint_state_broadcaster/JointStateBroadcaster
```

它会读取 state interfaces，并发布：

| 话题 | 类型 | 说明 |
|:---|:---|:---|
| `/joint_states` | `sensor_msgs/msg/JointState` | 标准关节状态，常被 `robot_state_publisher` 和 RViz 使用 |
| `/dynamic_joint_states` | `control_msgs/msg/DynamicJointState` | 发布所有可用 state interfaces，包括自定义接口 |

常用配置：

```yaml
joint_state_broadcaster:
  ros__parameters:
    use_local_topics: false
    use_urdf_to_filter: true
    publish_dynamic_joint_states: true
```

建议几乎所有机器人都启动 `joint_state_broadcaster`，否则 RViz 中机器人模型通常不会动。

#### Diff Drive Controller

`diff_drive_controller` 用于差速移动机器人。插件描述文件是：

```bash
/opt/ros/jazzy/share/diff_drive_controller/diff_drive_plugin.xml
```

插件名是：

```bash
diff_drive_controller/DiffDriveController
```

它的作用是：

*   订阅速度命令；
*   根据差速运动学计算左右轮目标速度；
*   向左右轮关节的 `velocity` command interface 写命令；
*   根据左右轮反馈计算里程计；
*   可选发布 `odom -> base_link` TF。

订阅话题：

| 话题 | 类型 | 说明 |
|:---|:---|:---|
| `~/cmd_vel` | `geometry_msgs/msg/TwistStamped` | 速度命令，使用 `linear.x` 和 `angular.z` |

如果控制器名是 `diff_drive_controller`，默认命令话题一般是：

```bash
/diff_drive_controller/cmd_vel
```

发布话题：

| 话题 | 类型 | 说明 |
|:---|:---|:---|
| `~/odom` | `nav_msgs/msg/Odometry` | 里程计 |
| `/tf` | `tf2_msgs/msg/TFMessage` | 当 `enable_odom_tf=true` 时发布 |
| `~/cmd_vel_out` | `geometry_msgs/msg/TwistStamped` | 当 `publish_limited_velocity=true` 时发布限幅后的速度 |

最小配置：

```yaml
diff_drive_controller:
  ros__parameters:
    left_wheel_names: ["left_wheel_joint"]
    right_wheel_names: ["right_wheel_joint"]
    wheel_separation: 0.40
    wheel_radius: 0.05
    odom_frame_id: odom
    base_frame_id: base_link
    position_feedback: true
    open_loop: false
    enable_odom_tf: true
    publish_rate: 50.0
```

如果只是用 mock 硬件学习，可以先用开环：

```yaml
diff_drive_controller:
  ros__parameters:
    left_wheel_names: ["left_wheel_joint"]
    right_wheel_names: ["right_wheel_joint"]
    wheel_separation: 0.40
    wheel_radius: 0.05
    position_feedback: false
    open_loop: true
    enable_odom_tf: true
```

发送速度命令：

```bash
ros2 topic pub /diff_drive_controller/cmd_vel geometry_msgs/msg/TwistStamped "{
  header: {frame_id: base_link},
  twist: {
    linear: {x: 0.2, y: 0.0, z: 0.0},
    angular: {x: 0.0, y: 0.0, z: 0.5}
  }
}"
```

注意：Jazzy 的 `diff_drive_controller` 使用 `TwistStamped`。如果旧教程使用 `geometry_msgs/msg/Twist`，在 Jazzy 中可能无法直接工作。

#### Joint Trajectory Controller

`joint_trajectory_controller` 常用于机械臂、云台、多关节机器人。插件描述文件是：

```bash
/opt/ros/jazzy/share/joint_trajectory_controller/joint_trajectory_plugin.xml
```

插件名是：

```bash
joint_trajectory_controller/JointTrajectoryController
```

它执行的是关节空间轨迹。常见输入是：

| 输入 | 类型 | 说明 |
|:---|:---|:---|
| `~/joint_trajectory` | `trajectory_msgs/msg/JointTrajectory` | 轨迹话题 |
| `~/follow_joint_trajectory` | `control_msgs/action/FollowJointTrajectory` | 标准轨迹 action，MoveIt2 常用 |

常见配置：

```yaml
arm_controller:
  ros__parameters:
    joints:
      - joint1
      - joint2
      - joint3
    command_interfaces:
      - position
    state_interfaces:
      - position
      - velocity
```

URDF 中对应 joint 至少要有：

```xml
<joint name="joint1">
  <command_interface name="position"/>
  <state_interface name="position"/>
  <state_interface name="velocity"/>
</joint>
```

如果机器人底层只接收速度命令，可以把 `command_interfaces` 改成 `velocity`。如果底层是力矩控制，可以使用 `effort`，但这要求硬件接口和控制器参数都匹配。

#### Forward Command Controller

`forward_command_controller` 是一个很适合初学者调试硬件接口的控制器。插件描述文件是：

```bash
/opt/ros/jazzy/share/forward_command_controller/forward_command_plugin.xml
```

插件名包括：

```bash
forward_command_controller/ForwardCommandController
forward_command_controller/MultiInterfaceForwardCommandController
```

它不会做复杂控制，只是把收到的数组命令直接写到指定 joint 的指定 command interface。

示例配置：

```yaml
forward_velocity_controller:
  ros__parameters:
    joints:
      - left_wheel_joint
      - right_wheel_joint
    interface_name: velocity
```

对于刚写完硬件接口的人来说，推荐先用 forward controller 测试：

*   `ros2 control list_hardware_interfaces` 确认接口存在；
*   启动 `forward_velocity_controller`；
*   发送简单速度数组；
*   看硬件接口的 `write()` 是否收到命令。

#### ros2_controllers 中的其他控制器

安装 `ros-jazzy-ros2-controllers` 后，还会带很多控制器。常见类型如下：

| 控制器 | 插件名 | 用途 | 常见硬件接口 |
|:---|:---|:---|:---|
| 差速底盘 | `diff_drive_controller/DiffDriveController` | 双轮或两侧多轮差速机器人，负责从底盘速度计算左右轮速度，并发布里程计 | 轮关节 `velocity` 命令，轮关节 `position/velocity` 状态 |
| 机械臂轨迹 | `joint_trajectory_controller/JointTrajectoryController` | 多关节轨迹执行，MoveIt2 常用它执行 `FollowJointTrajectory` | 关节 `position`、`velocity` 或 `effort` 命令，通常读取 `position/velocity` 状态 |
| 位置组控制 | `position_controllers/JointGroupPositionController` | 直接把位置数组写给多个关节，不做轨迹规划和运动学 | 多个 joint 的 `position` 命令 |
| 速度组控制 | `velocity_controllers/JointGroupVelocityController` | 直接把速度数组写给多个关节，常用于轮子或速度型执行器测试 | 多个 joint 的 `velocity` 命令 |
| 力矩组控制 | `effort_controllers/JointGroupEffortController` | 直接把 effort 数组写给多个关节，适合力矩控制或简单测试 | 多个 joint 的 `effort` 命令 |
| 命令转发 | `forward_command_controller/ForwardCommandController` | 通用命令转发器，可以选择某一种 command interface | 由 `interface_name` 参数决定 |
| 多接口命令转发 | `forward_command_controller/MultiInterfaceForwardCommandController` | 同时向多个接口转发命令，适合复杂硬件调试 | 多个 joint 的多个 command interfaces |
| 夹爪 | `parallel_gripper_action_controller/GripperActionController` | 平行夹爪 action 控制，适合两个手指联动的夹爪 | 夹爪关节 `position` 或相关接口 |
| Ackermann | `ackermann_steering_controller/AckermannSteeringController` | 汽车式阿克曼底盘，通常前轮转向、后轮驱动 | 转向关节 `position` 命令，驱动轮 `velocity` 命令 |
| Mecanum | `mecanum_drive_controller/MecanumDriveController` | 四轮麦克纳姆底盘，可前后、横移、旋转 | 四个轮关节 `velocity` 命令 |
| 三轮车 | `tricycle_controller/TricycleController` | 三轮底盘，通常一个转向轮加驱动轮 | 转向关节和驱动轮命令 |
| 全向轮 | `omni_wheel_drive_controller/OmniWheelDriveController` | 三个或更多全向轮底盘，支持平面全向运动 | 多个轮关节 `velocity` 命令 |
| PID | `pid_controller/PidController` | 基于 `control_toolbox` 的 PID 控制器，可用于控制器链 | 读取状态/参考，输出 PID 后的命令 |
| IMU 发布 | `imu_sensor_broadcaster/IMUSensorBroadcaster` | 把硬件状态接口发布成 IMU 消息 | 读取 IMU 相关 state interfaces |
| 力/力矩发布 | `force_torque_sensor_broadcaster/ForceTorqueSensorBroadcaster` | 发布六维力传感器数据 | 读取 force/torque state interfaces |
| 距离传感器发布 | `range_sensor_broadcaster/RangeSensorBroadcaster` | 发布距离传感器数据 | 读取 range state interface |
| 电池状态发布 | `battery_state_broadcaster/BatteryStateBroadcaster` | 发布电池电压、电流、电量等状态 | 读取电池相关 state interfaces |
| 通用状态发布 | `state_interfaces_broadcaster/StateInterfacesBroadcaster` | 发布指定 state interfaces，适合调试自定义状态 | 读取用户指定 state interfaces |

如果只是想确认硬件接口能不能收到命令，优先用 `forward_command_controller`、`position_controllers`、`velocity_controllers` 或 `effort_controllers`。如果已经有明确运动学模型，再选择 `diff_drive_controller`、`mecanum_drive_controller`、`ackermann_steering_controller`、`tricycle_controller` 或 `omni_wheel_drive_controller`。机械臂和多关节机构通常先从 `joint_trajectory_controller` 学起。

选控制器时先问自己三个问题：

*   我的机器人运动学是不是官方已经支持？
*   我的硬件接收的是 position、velocity 还是 effort？
*   我需要控制器自己算运动学，还是只需要把命令转发给硬件？

如果官方控制器能满足需求，就优先使用官方控制器。只有在运动学特殊、控制律特殊、需要融合特殊传感器时，才建议写自定义控制器。

### 一个差速底盘最小 Bringup

#### controllers.yaml

```yaml
controller_manager:
  ros__parameters:
    update_rate: 100

    joint_state_broadcaster:
      type: joint_state_broadcaster/JointStateBroadcaster

    diff_drive_controller:
      type: diff_drive_controller/DiffDriveController

joint_state_broadcaster:
  ros__parameters:
    use_local_topics: false
    publish_dynamic_joint_states: true

diff_drive_controller:
  ros__parameters:
    left_wheel_names: ["left_wheel_joint"]
    right_wheel_names: ["right_wheel_joint"]
    wheel_separation: 0.40
    wheel_radius: 0.05
    odom_frame_id: odom
    base_frame_id: base_link
    position_feedback: false
    open_loop: true
    enable_odom_tf: true
    publish_limited_velocity: true
```

#### URDF 关键片段

```xml
<ros2_control name="DiffBotSystem" type="system">
  <hardware>
    <plugin>mock_components/GenericSystem</plugin>
  </hardware>

  <joint name="left_wheel_joint">
    <command_interface name="velocity"/>
    <state_interface name="position"/>
    <state_interface name="velocity"/>
  </joint>

  <joint name="right_wheel_joint">
    <command_interface name="velocity"/>
    <state_interface name="position"/>
    <state_interface name="velocity"/>
  </joint>
</ros2_control>
```

#### 启动顺序

推荐顺序如下：

1.  启动 `robot_state_publisher`。
2.  启动 `ros2_control_node`。
3.  启动 `joint_state_broadcaster`。
4.  启动 `diff_drive_controller`。
5.  发布 `/diff_drive_controller/cmd_vel`。
6.  查看 `/joint_states`、`/diff_drive_controller/odom` 和 `/tf`。

调试命令如下：

```bash
ros2 control list_hardware_components
ros2 control list_hardware_interfaces
ros2 control list_controllers --verbose
ros2 topic echo /joint_states
ros2 topic echo /diff_drive_controller/odom
```

### 自定义硬件接口

#### 什么时候需要写硬件接口

如果你的机器人使用真实电机驱动板，通常需要写硬件接口。典型场景包括：

*   串口电机驱动板；
*   CAN 总线电机；
*   EtherCAT 驱动器；
*   自研 STM32 下位机；
*   自定义传感器数据；
*   非 Gazebo 的自定义仿真器。

如果你只是想验证控制器配置，可以先用 `mock_components/GenericSystem`。如果你要接真机，就需要继承 `hardware_interface::SystemInterface`。

#### SystemInterface 生命周期

Jazzy 中系统硬件接口的常用函数如下：

| 函数 | 调用时机 | 作用 |
|:---|:---|:---|
| `on_init(const HardwareInfo & info)` | 加载硬件时 | 读取 URDF 中的 joint、interface、param |
| `on_configure(...)` | 配置硬件时 | 打开串口、初始化驱动板、分配资源 |
| `on_activate(...)` | 激活硬件时 | 使能电机，清零命令 |
| `on_deactivate(...)` | 停用硬件时 | 失能电机，停止输出 |
| `read(const rclcpp::Time &, const rclcpp::Duration &)` | 每个控制周期 | 读取硬件状态并写入 state interfaces |
| `write(const rclcpp::Time &, const rclcpp::Duration &)` | 每个控制周期 | 读取 command interfaces 并写给硬件 |

在 Jazzy 中，如果接口已经在 URDF 的 `<ros2_control>` 中声明，`SystemInterface` 默认会根据 URDF 创建接口。自定义硬件里可以使用：

```cpp
set_state("left_wheel_joint/position", value);
set_state("left_wheel_joint/velocity", value);
double cmd = get_command("left_wheel_joint/velocity");
```

这些函数来自 Jazzy 的 `hardware_interface::SystemInterface`。

#### 最小硬件接口框架

下面是一个用于差速底盘的硬件接口骨架。它展示结构，不包含具体串口协议：

```cpp
#include <string>

#include "hardware_interface/system_interface.hpp"
#include "hardware_interface/types/hardware_interface_return_values.hpp"
#include "rclcpp/rclcpp.hpp"

namespace my_robot_hardware
{

class MyDiffBotSystem : public hardware_interface::SystemInterface
{
public:
  hardware_interface::CallbackReturn on_init(const hardware_interface::HardwareInfo & info) override
  {
    if (hardware_interface::SystemInterface::on_init(info) !=
      hardware_interface::CallbackReturn::SUCCESS)
    {
      return hardware_interface::CallbackReturn::ERROR;
    }

    serial_port_ = info_.hardware_parameters.at("serial_port");
    return hardware_interface::CallbackReturn::SUCCESS;
  }

  hardware_interface::CallbackReturn on_configure(const rclcpp_lifecycle::State &) override
  {
    // 在这里打开串口、CAN 或网络连接。
    return hardware_interface::CallbackReturn::SUCCESS;
  }

  hardware_interface::CallbackReturn on_activate(const rclcpp_lifecycle::State &) override
  {
    set_command("left_wheel_joint/velocity", 0.0);
    set_command("right_wheel_joint/velocity", 0.0);
    return hardware_interface::CallbackReturn::SUCCESS;
  }

  hardware_interface::return_type read(
    const rclcpp::Time &, const rclcpp::Duration &) override
  {
    // 从编码器读取真实位置和速度。
    set_state("left_wheel_joint/position", left_position_);
    set_state("left_wheel_joint/velocity", left_velocity_);
    set_state("right_wheel_joint/position", right_position_);
    set_state("right_wheel_joint/velocity", right_velocity_);
    return hardware_interface::return_type::OK;
  }

  hardware_interface::return_type write(
    const rclcpp::Time &, const rclcpp::Duration &) override
  {
    const double left_cmd = get_command("left_wheel_joint/velocity");
    const double right_cmd = get_command("right_wheel_joint/velocity");

    // 把 left_cmd 和 right_cmd 写给电机驱动板。
    return hardware_interface::return_type::OK;
  }

private:
  std::string serial_port_;
  double left_position_ = 0.0;
  double left_velocity_ = 0.0;
  double right_position_ = 0.0;
  double right_velocity_ = 0.0;
};

}  // namespace my_robot_hardware
```

插件导出：

```cpp
#include "pluginlib/class_list_macros.hpp"

PLUGINLIB_EXPORT_CLASS(
  my_robot_hardware::MyDiffBotSystem,
  hardware_interface::SystemInterface)
```

对应插件 XML：

```xml
<library path="my_robot_hardware">
  <class
    name="my_robot_hardware/MyDiffBotSystem"
    type="my_robot_hardware::MyDiffBotSystem"
    base_class_type="hardware_interface::SystemInterface">
    <description>My differential drive robot hardware interface.</description>
  </class>
</library>
```

URDF 中使用：

```xml
<hardware>
  <plugin>my_robot_hardware/MyDiffBotSystem</plugin>
  <param name="serial_port">/dev/ttyUSB0</param>
</hardware>
```

#### 硬件接口调试顺序

写硬件接口时建议按这个顺序调试：

1.  先不用真实电机，确认 `pluginlib` 能加载硬件插件。
2.  启动 `ros2_control_node`，运行 `ros2 control list_hardware_components`。
3.  确认硬件组件能进入 `inactive`。
4.  确认 `list_hardware_interfaces` 中有期望的 command/state interfaces。
5.  使用 `forward_command_controller` 直接给 command interface 发命令。
6.  在 `write()` 中打印一次命令，确认控制器确实写入。
7.  接真实电机，但先架空轮子或关闭高功率输出。
8.  确认 `read()` 能正确更新 `/joint_states`。
9.  最后再接 `diff_drive_controller` 或 `joint_trajectory_controller`。

### 自定义运动学控制器

#### 什么时候需要写控制器

不建议一开始就写自定义控制器。优先判断官方控制器是否够用：

| 需求 | 推荐方案 |
|:---|:---|
| 差速底盘 | `diff_drive_controller` |
| 机械臂轨迹 | `joint_trajectory_controller` |
| 只想直接写关节位置 | `position_controllers/JointGroupPositionController` |
| 只想直接写关节速度 | `velocity_controllers/JointGroupVelocityController` |
| 只想测试硬件命令 | `forward_command_controller` |
| 特殊底盘运动学 | 自定义 controller |
| 自定义阻抗、导纳、力控策略 | 自定义 controller 或基于 `admittance_controller` 扩展 |

如果你的机器人是四轮差速、麦克纳姆轮、舵轮、特殊并联机构、腿式机器人，官方控制器不一定完全匹配，这时就需要写自定义控制器。

#### ControllerInterface 关键函数

Jazzy 中普通控制器继承：

```cpp
controller_interface::ControllerInterface
```

必须关注这些函数：

| 函数 | 作用 |
|:---|:---|
| `on_init()` | 声明参数，初始化非实时对象 |
| `command_interface_configuration()` | 声明要占用哪些 command interfaces |
| `state_interface_configuration()` | 声明要读取哪些 state interfaces |
| `on_configure()` | 读取参数、创建订阅者、初始化缓存 |
| `on_activate()` | 激活前检查接口、清零命令 |
| `on_deactivate()` | 停止控制器、释放状态 |
| `update(time, period)` | 实时控制循环，计算并写入命令 |

其中 `update()` 在实时控制循环中执行，不应该做这些事：

*   不要动态分配大量内存；
*   不要访问阻塞 I/O；
*   不要等待锁；
*   不要频繁打印日志；
*   不要在里面创建 publisher/subscriber；
*   不要做可能阻塞的参数读取。

订阅 ROS 话题时，常用做法是普通回调写入 `realtime_tools::RealtimeBuffer`，`update()` 中只读这个 buffer。

#### 一个自定义底盘控制器的思路

假设要写一个自定义底盘控制器，输入是 `cmd_vel`，输出是四个轮子的速度命令。核心步骤是：

1.  参数中声明四个轮子的 joint 名。
2.  `command_interface_configuration()` 返回四个 `<joint>/velocity`。
3.  `state_interface_configuration()` 返回需要读取的 `<joint>/position` 或 `<joint>/velocity`。
4.  `on_configure()` 创建 `cmd_vel` 订阅者。
5.  订阅者把最新速度命令写入实时 buffer。
6.  `update()` 读取最新速度命令。
7.  根据运动学模型计算各轮速度。
8.  把结果写入 `command_interfaces_`。

伪代码如下：

```cpp
controller_interface::InterfaceConfiguration
MyKinematicsController::command_interface_configuration() const
{
  controller_interface::InterfaceConfiguration config;
  config.type = controller_interface::interface_configuration_type::INDIVIDUAL;
  config.names = {
    "front_left_wheel_joint/velocity",
    "front_right_wheel_joint/velocity",
    "rear_left_wheel_joint/velocity",
    "rear_right_wheel_joint/velocity"
  };
  return config;
}

controller_interface::return_type MyKinematicsController::update(
  const rclcpp::Time &, const rclcpp::Duration &)
{
  const auto cmd = *command_buffer_.readFromRT();

  const double vx = cmd.linear.x;
  const double wz = cmd.angular.z;

  const double left = vx - wz * wheel_separation_ * 0.5;
  const double right = vx + wz * wheel_separation_ * 0.5;

  command_interfaces_[0].set_value(left / wheel_radius_);
  command_interfaces_[1].set_value(right / wheel_radius_);
  command_interfaces_[2].set_value(left / wheel_radius_);
  command_interfaces_[3].set_value(right / wheel_radius_);

  return controller_interface::return_type::OK;
}
```

这段代码只是运动学控制器核心逻辑。完整控制器还需要参数声明、订阅者、接口排序检查、超时保护、插件导出和 CMake 配置。

#### 自定义控制器 package.xml

常见依赖如下：

```xml
<depend>controller_interface</depend>
<depend>hardware_interface</depend>
<depend>pluginlib</depend>
<depend>rclcpp</depend>
<depend>rclcpp_lifecycle</depend>
<depend>realtime_tools</depend>
<depend>geometry_msgs</depend>
```

如果要发布里程计，还需要：

```xml
<depend>nav_msgs</depend>
<depend>tf2</depend>
<depend>tf2_msgs</depend>
<depend>tf2_ros</depend>
```

#### 自定义控制器 CMakeLists

核心写法如下：

```cmake
add_library(my_kinematics_controller SHARED
  src/my_kinematics_controller.cpp
)

ament_target_dependencies(my_kinematics_controller
  controller_interface
  hardware_interface
  pluginlib
  rclcpp
  rclcpp_lifecycle
  realtime_tools
  geometry_msgs
)

pluginlib_export_plugin_description_file(
  controller_interface
  my_kinematics_controller.xml
)

install(
  TARGETS my_kinematics_controller
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

install(
  FILES my_kinematics_controller.xml
  DESTINATION share/${PROJECT_NAME}
)
```

插件 XML：

```xml
<library path="my_kinematics_controller">
  <class
    name="my_kinematics_controller/MyKinematicsController"
    type="my_kinematics_controller::MyKinematicsController"
    base_class_type="controller_interface::ControllerInterface">
    <description>My custom mobile robot kinematics controller.</description>
  </class>
</library>
```

YAML 中加载：

```yaml
controller_manager:
  ros__parameters:
    my_kinematics_controller:
      type: my_kinematics_controller/MyKinematicsController

my_kinematics_controller:
  ros__parameters:
    wheel_radius: 0.05
    wheel_separation: 0.40
```

#### 普通控制器还是链式控制器

Jazzy 中控制器分两类：

| 类型 | 基类 | 适用场景 |
|:---|:---|:---|
| 普通控制器 | `controller_interface::ControllerInterface` | 从 ROS 话题/action 接收命令，直接写硬件 command interface |
| 链式控制器 | `controller_interface::ChainableControllerInterface` | 上游控制器输出 reference interface，下游控制器继续处理 |

初学者建议先写普通控制器。只有当你需要多个控制器串联时，再研究链式控制器。例如：

```text
导航速度命令 -> 运动学控制器 -> PID 控制器 -> 硬件接口
```

链式控制器可以用：

```bash
ros2 control view_controller_chains
```

查看控制器链路。

### Gazebo 与 ROS2 Control

ros2_control 本身不是仿真器。它只是控制框架。要在 Gazebo Sim 中使用，需要 `gz_ros2_control` 插件。

安装：

```bash
sudo apt install ros-jazzy-gz-ros2-control ros-jazzy-gz-ros2-control-demos
```

Gazebo 集成时仍然需要：

*   URDF 中的 `<ros2_control>`；
*   控制器 YAML；
*   `controller_manager`；
*   `joint_state_broadcaster` 和具体控制器。

区别在于 `<hardware><plugin>...</plugin></hardware>` 通常换成 Gazebo 对应硬件插件，而不是 `mock_components/GenericSystem` 或你的真实硬件插件。

Jazzy 中 `gz_ros2_control` 的硬件插件描述文件是：

```bash
/opt/ros/jazzy/share/gz_ros2_control/gz_hardware_plugins.xml
```

Gazebo Sim 系统硬件插件名是：

```bash
gz_ros2_control/GazeboSimSystem
```

URDF 或 xacro 中的 `<ros2_control>` 关键片段通常写成：

```xml
<ros2_control name="GazeboSimSystem" type="system">
  <hardware>
    <plugin>gz_ros2_control/GazeboSimSystem</plugin>
  </hardware>

  <joint name="left_wheel_joint">
    <command_interface name="velocity"/>
    <state_interface name="position"/>
    <state_interface name="velocity"/>
  </joint>

  <joint name="right_wheel_joint">
    <command_interface name="velocity"/>
    <state_interface name="position"/>
    <state_interface name="velocity"/>
  </joint>
</ros2_control>
```

同时还要在机器人描述中加入 Gazebo 系统插件，让 Gazebo Sim 创建 ros2_control 资源管理器并加载控制器参数：

```xml
<gazebo>
  <plugin filename="gz_ros2_control-system" name="gz_ros2_control::GazeboSimROS2ControlPlugin">
    <parameters>$(find my_robot_bringup)/config/controllers.yaml</parameters>
  </plugin>
</gazebo>
```

安装 `gz_ros2_control_demos` 后，可以直接参考官方示例：

```bash
ros2 launch gz_ros2_control_demos diff_drive_example.launch.py
ros2 launch gz_ros2_control_demos cart_example_position.launch.py
ros2 launch gz_ros2_control_demos cart_example_velocity.launch.py
ros2 launch gz_ros2_control_demos cart_example_effort.launch.py
ros2 launch gz_ros2_control_demos mecanum_drive_example.launch.py
ros2 launch gz_ros2_control_demos ackermann_drive_example.launch.py
ros2 launch gz_ros2_control_demos tricycle_drive_example.launch.py
```

这些示例文件安装在：

```bash
/opt/ros/jazzy/share/gz_ros2_control_demos
```

比较重要的文件包括：

| 文件 | 作用 |
|:---|:---|
| `urdf/test_diff_drive.xacro.urdf` | Gazebo 差速底盘 URDF/xacro 示例 |
| `config/diff_drive_controller.yaml` | 差速控制器参数示例 |
| `launch/diff_drive_example.launch.py` | 差速底盘完整启动示例 |
| `urdf/test_mecanum_drive.xacro.urdf` | 麦克纳姆底盘示例 |
| `config/mecanum_drive_controller.yaml` | 麦克纳姆控制器参数示例 |

学习 Gazebo 集成时，不要只看 launch 文件。更推荐按这个顺序读：

1.  先看 `urdf/test_diff_drive.xacro.urdf` 中 `<ros2_control>` 和 `<gazebo>` 插件。
2.  再看 `config/diff_drive_controller.yaml` 中控制器参数。
3.  最后看 `launch/diff_drive_example.launch.py` 如何把机器人描述、Gazebo 和 spawner 连起来。

建议学习顺序是：

1.  先用 `mock_components/GenericSystem` 跑通控制器。
2.  再接 Gazebo。
3.  最后接真实硬件。

这样出错时更容易定位问题。

### 常见问题

#### 控制器加载失败

优先检查：

```bash
ros2 control list_controller_types
```

如果你的控制器类型不在列表里，通常是：

*   插件 XML 没有安装；
*   `pluginlib_export_plugin_description_file()` 写错；
*   YAML 中 `type` 字段和插件 XML 中的 `name` 不一致；
*   没有 source 工作空间：

```bash
source install/setup.bash
```

#### 控制器激活失败

查看接口：

```bash
ros2 control list_hardware_interfaces --verbose
ros2 control list_controllers --verbose
```

常见原因：

*   控制器要的 command interface 不存在；
*   控制器要的 state interface 不存在；
*   另一个控制器已经占用了同一个 command interface；
*   URDF 里的 joint 名和 YAML 里的 joint 名不一致；
*   硬件组件还没有激活。

#### `/joint_states` 没有数据

检查：

```bash
ros2 control list_controllers
ros2 topic echo /dynamic_joint_states
ros2 topic echo /joint_states
```

常见原因：

*   没有启动 `joint_state_broadcaster`；
*   硬件接口没有 state interfaces；
*   `use_urdf_to_filter=true` 时，URDF 中没有对应 joint；
*   硬件 `read()` 没有更新 state。

#### `cmd_vel` 发了但底盘不动

Jazzy 的 `diff_drive_controller` 默认订阅 `TwistStamped`：

```bash
ros2 topic info /diff_drive_controller/cmd_vel
```

确认消息类型是：

```bash
geometry_msgs/msg/TwistStamped
```

还要检查：

*   话题名是不是 `/diff_drive_controller/cmd_vel`；
*   控制器是否 active；
*   轮关节 `velocity` command interface 是否存在；
*   `left_wheel_names`、`right_wheel_names` 是否写对；
*   `cmd_vel_timeout` 是否太短；
*   真机硬件接口 `write()` 是否真的把命令发给电机。

#### 里程计方向不对

优先检查：

*   左右轮 joint 是否写反；
*   轮子正方向是否与 URDF 轴方向一致；
*   `wheel_radius` 是否正确；
*   `wheel_separation` 是否正确；
*   编码器位置单位是否是弧度；
*   硬件接口中的速度单位是否是 rad/s。

#### TF 冲突

如果系统中已有其他节点发布 `odom -> base_link`，不要再让 `diff_drive_controller` 发布同一段 TF：

```yaml
diff_drive_controller:
  ros__parameters:
    enable_odom_tf: false
```

### 学习路线建议

建议按下面顺序学习：

1.  看懂 command interface 和 state interface。
2.  用 `mock_components/GenericSystem` 跑通 `joint_state_broadcaster`。
3.  用 `forward_command_controller` 直接写关节命令。
4.  用 `diff_drive_controller` 跑通差速底盘。
5.  用 `joint_trajectory_controller` 跑通机械臂或多关节模型。
6.  写一个最小 `SystemInterface`，先只打印命令。
7.  接真实串口/CAN，让 `read()` 更新 `/joint_states`。
8.  再接官方控制器。
9.  最后再写自定义运动学控制器。

不要一开始就同时写硬件接口、运动学控制器、仿真插件和导航。这样一旦出错，很难判断是 URDF、YAML、控制器、硬件接口还是底层通信的问题。

### 参考资料

*   ROS2 Control Jazzy 官方文档：https://control.ros.org/jazzy/index.html
*   Getting Started：https://control.ros.org/jazzy/doc/getting_started/getting_started.html
*   Controller Manager：https://control.ros.org/jazzy/doc/ros2_control/controller_manager/doc/userdoc.html
*   Hardware Interface：https://control.ros.org/jazzy/doc/ros2_control/hardware_interface/doc/hardware_interface_types_userdoc.html
*   Joint State Broadcaster：https://control.ros.org/jazzy/doc/ros2_controllers/joint_state_broadcaster/doc/userdoc.html
*   Diff Drive Controller：https://control.ros.org/jazzy/doc/ros2_controllers/diff_drive_controller/doc/userdoc.html
*   Joint Trajectory Controller：https://control.ros.org/jazzy/doc/ros2_controllers/joint_trajectory_controller/doc/userdoc.html
*   ros2_control GitHub：https://github.com/ros-controls/ros2_control
*   ros2_controllers GitHub：https://github.com/ros-controls/ros2_controllers
