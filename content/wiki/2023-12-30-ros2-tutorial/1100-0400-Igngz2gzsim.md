---
title: "将Ign Gazebo迁移至Gz Sim"
---

### 将Ign Gazebo迁移至Gz Sim

**（即 ROS2 Humble 迁移至 ROS2 Jazzy 及之后版本。只用 Humble 的工程也建议看一下，因为很多写法会影响以后升级。）**

ROS2 Humble 时代，新版 Gazebo 仍经常使用 Ignition / Ign Gazebo 的命名，例如命令是 `ign gazebo`，ROS 包名常见 `ros_ign_*`，插件文件名常见 `ignition-gazebo-xxx-system`。

到了 ROS2 Jazzy，官方搭配的是 Gazebo Harmonic。此时命名体系基本切换为 Gazebo / Gz Sim，例如命令变成 `gz sim`，ROS 包名改成 `ros_gz_*`，插件文件名改成 `gz-sim-xxx-system`。

所以从 Humble 迁移到 Jazzy 时，兼容性问题最大的地方通常就是 Gazebo。界面和功能看起来差不多，但代码里大量名字不互通，需要把 Ignition 时代的命名迁移到 Gz Sim / Gazebo Harmonic 写法。

可以简单理解为：大部分迁移不是功能重写，而是命名体系、插件名、launch 包名、SDF 标签和环境变量的迁移。

官方参考：

- Gazebo Harmonic 迁移说明：https://gazebosim.org/docs/harmonic/migration_from_ignition/
- Gazebo Fuel 模型引用示例：https://gazebosim.org/docs/harmonic/fuel_insert/
- Gazebo Sensors 示例：https://gazebosim.org/docs/harmonic/sensors/
- ROS2 Gazebo Classic 迁移到新 Gazebo 的说明：https://gazebosim.org/docs/harmonic/migrating_gazebo_classic_ros2_packages/
- SDFormat Sensor 规范：https://sdformat.org/spec

#### 迁移前先搜索旧写法

在工程根目录执行：

```bash
rg -n -i "ignition|ignition-gazebo|libignition|ros_ign|ign_args|ign_version|fuel.ignitionrobotics|ignition_frame_id|<ignition-gui>|ign gazebo|ign topic|alwaysOn" src
```

重点检查这些文件：

- `*.sdf`：world、model、GUI、插件、Fuel URL 通常在这里。
- `*.urdf.xacro` / `*.urdf`：机器人插件、传感器标签通常在这里。
- `*.launch.py`：Gazebo 启动包、启动参数、桥接 topic 通常在这里。
- `package.xml`：运行依赖通常在这里。
- `CMakeLists.txt`：安装目录和编译依赖通常在这里。

建议修改时加成对中文注释，方便以后知道从哪里开始、到哪里结束：

```xml
<!-- Jazzy 迁移开始：说明这里为什么要改。 -->
<plugin filename="gz-sim-physics-system" name="gz::sim::systems::Physics"/>
<!-- Jazzy 迁移结束：说明这里已经改成什么写法。 -->
```

#### SDF文件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1741.webp)

根据官方迁移说明，普通命名通常把 `ignition` / `ign` 换成 `gz`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1742.webp)

插件要按新命名修改，例如：

- `ignition::gazebo` 命名空间改为 `gz::sim`
- `ignition::` 改为 `gz::`
- `ignition-gazebo-XXX-system` 改为 `gz-sim-XXX-system`
- `libignition-gazebo-XXX-system.so` 改为 `gz-sim-XXX-system`

常见替换表：

| Humble / Ignition 写法 | Jazzy / Gazebo Harmonic 写法 | 示例场景 |
|:---|:---|:---|
| `ignition-gazebo-*` | `gz-sim-*` | 插件文件名 |
| `libignition-gazebo-*.so` | `gz-sim-*` | 插件文件名 |
| `ignition::gazebo::*` | `gz::sim::*` | 插件命名空间 |
| `ros_ign_*` | `ros_gz_*` | ROS-Gazebo 包名 |
| `ign_args` | `gz_args` | launch 参数 |
| `ign_version` | `gz_version` | launch 参数 |
| `IGN_GAZEBO_RESOURCE_PATH` | `GZ_SIM_RESOURCE_PATH` | 模型/资源路径 |
| `fuel.ignitionrobotics.org` | `fuel.gazebosim.org` | Gazebo Fuel URL |

##### 系统插件

旧写法：

```xml
<plugin filename="libignition-gazebo-physics-system.so"
        name="ignition::gazebo::systems::Physics"/>
```

Jazzy / Gazebo Harmonic 写法：

```xml
<plugin filename="gz-sim-physics-system"
        name="gz::sim::systems::Physics"/>
```

常见系统插件：

| 旧插件 | 新插件 |
|:---|:---|
| `ignition-gazebo-physics-system` | `gz-sim-physics-system` |
| `ignition-gazebo-sensors-system` | `gz-sim-sensors-system` |
| `ignition-gazebo-scene-broadcaster-system` | `gz-sim-scene-broadcaster-system` |
| `ignition-gazebo-user-commands-system` | `gz-sim-user-commands-system` |
| `ignition-gazebo-contact-system` | `gz-sim-contact-system` |
| `ignition-gazebo-diff-drive-system` | `gz-sim-diff-drive-system` |
| `ignition-gazebo-joint-state-publisher-system` | `gz-sim-joint-state-publisher-system` |

##### GUI 标签

旧版 Gazebo GUI 配置可能写：

```xml
<ignition-gui>
  <property type="string" key="state">docked</property>
</ignition-gui>
```

Jazzy / Gazebo Harmonic 改成：

```xml
<gz-gui>
  <property type="string" key="state">docked</property>
</gz-gui>
```

可以用下面命令检查：

```bash
rg -n "<ignition-gui>|</ignition-gui>" src
```

#### URDF / Xacro 里的 Gazebo 传感器

传感器部分是 Jazzy 迁移时最容易留下 warning 的地方。

##### alwaysOn 改为 always_on

旧写法：

```xml
<alwaysOn>true</alwaysOn>
```

Jazzy / SDF 标准写法：

```xml
<always_on>true</always_on>
```

如果不改，Gazebo 可能会提示：

```text
XML Element[alwaysOn], child of element[sensor], not defined in SDF.
```

##### ignition_frame_id / gz_frame_id 与 pose relative_to

旧写法可能是：

```xml
<ignition_frame_id>laser</ignition_frame_id>
```

有些 Gazebo ROS 迁移文档里会看到：

```xml
<gz_frame_id>laser</gz_frame_id>
```

但是在当前 ROS2 Jazzy / Gazebo Harmonic 工具链里，URDF 转 SDF 通常会输出 SDF 1.11，而 SDF 1.11 没有标准 `<gz_frame_id>` 元素。因此启动时可能会出现：

```text
XML Element[gz_frame_id], child of element[sensor], not defined in SDF. Copying[gz_frame_id] as children of [sensor].
```

但是这个警告说实话，是警告，但实际上这个标签是生效的，必须要使用这个标签，请忽略那些警告。

然后你也可以再加上一个下面这个`pose relative_to`标签，这是官方示例里给的。

```xml
<pose relative_to="laser">0 0 0 0 0 0</pose>
```

相机示例：

```xml
<sensor name="cam_link" type="camera">
  <update_rate>10.0</update_rate>
  <always_on>true</always_on>
  <pose relative_to="camera">0 0 0 0 0 0</pose>
  <gz_frame_id>camera</gz_frame_id>
  <topic>/image_raw</topic>
  <camera name="my_camera">
    ...
  </camera>
</sensor>
```

雷达示例：

```xml
<sensor name="gpu_lidar" type="gpu_lidar">
  <topic>scan</topic>
  <update_rate>30</update_rate>
  <always_on>true</always_on>
  <visualize>true</visualize>
  <pose relative_to="laser">0 0 0 0 0 0</pose>
  <gz_frame_id>laser</gz_frame_id>
  <lidar>
    ...
  </lidar>
</sensor>
```

注意：`pose relative_to` 和 `gz_frame_id` 不是同一个概念。

- `pose relative_to`：表示传感器的位姿相对于哪个 link/frame，解决“传感器放在哪里”的问题。
- `gz_frame_id`：尝试指定传感器消息里的 frame id，解决“消息 header.frame_id 写什么”的问题。

建议同时采用 `pose relative_to` 和 `<gz_frame_id>`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1743.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1744.webp)


#### Launch 文件

Jazzy 使用 `ros_gz_sim` 启动 Gazebo，不再使用旧的 `ros_ign_gazebo` / `ros_ign`。

旧思路常见是：

```python
pkg_ros_ign_gazebo = get_package_share_directory("ros_ign_gazebo")
```

Jazzy 推荐：

```python
pkg_ros_gz_sim = get_package_share_directory("ros_gz_sim")
```

启动 Gazebo 的 launch 写法：

```python
gz_sim = IncludeLaunchDescription(
    PythonLaunchDescriptionSource(
        os.path.join(pkg_ros_gz_sim, "launch", "gz_sim.launch.py")),
    launch_arguments={
        "gz_args": "-r " + world_file
    }.items(),
)
```

注意参数名也要改：

| Humble / Ignition | Jazzy / Gazebo Harmonic |
|:---|:---|
| `ign_args` | `gz_args` |
| `ign_version` | `gz_version` |

如果要 spawn URDF / robot_description，Jazzy 使用：

```python
spawn = Node(
    package="ros_gz_sim",
    executable="create",
    arguments=[
        "-name", "mycar",
        "-topic", "/robot_description",
    ],
    output="screen",
)
```

#### ROS 和 Gazebo 消息桥接

Jazzy 使用 `ros_gz_bridge`：

```python
bridge = Node(
    package="ros_gz_bridge",
    executable="parameter_bridge",
    arguments=[
        "/cmd_vel@geometry_msgs/msg/Twist@gz.msgs.Twist",
        "/scan@sensor_msgs/msg/LaserScan@gz.msgs.LaserScan",
        "/image_raw@sensor_msgs/msg/Image@gz.msgs.Image",
    ],
)
```

检查点：

- ROS 包名用 `ros_gz_bridge`
- Gazebo 消息命名空间用 `gz.msgs.*`
- 如果 topic 没有桥出来，先执行 `gz topic -l` 看 Gazebo 侧真实 topic 名称，再改 bridge 参数

#### package.xml 依赖

如果 launch 文件里用了 `ros_gz_sim` 和 `ros_gz_bridge`，`package.xml` 也要声明运行依赖：

```xml
<exec_depend>ros_gz_sim</exec_depend>
<exec_depend>ros_gz_bridge</exec_depend>
<exec_depend>geometry_msgs</exec_depend>
<exec_depend>nav_msgs</exec_depend>
<exec_depend>tf2_msgs</exec_depend>
<exec_depend>sensor_msgs</exec_depend>
<exec_depend>rosgraph_msgs</exec_depend>
```

如果还启动 RViz，也补：

```xml
<exec_depend>rviz2</exec_depend>
```

#### Fuel URL 和模型路径

旧 Fuel URL（实际上旧的链接也生效，被重定向到新链接了）：

```xml
<uri>https://fuel.ignitionrobotics.org/1.0/openrobotics/models/Playground</uri>
```

新 Fuel URL：

```xml
<uri>https://fuel.gazebosim.org/1.0/openrobotics/models/Playground</uri>
```

本地模型路径不要盲目改。例如：

```xml
<uri>file:///home/xxx/ROS_WS/ign_models/bed</uri>
```

这里的 `ign_models` 可能只是自己建的文件夹名，不是 Gazebo API 名称。只要这个目录真实存在，就可以保留。

如果想改成更通用的模型路径，可以设置下面这个宏，宏从`IGN_GAZEBO_RESOURCE_PATH`修改为`GZ_SIM_RESOURCE_PATH`：

```bash
export GZ_SIM_RESOURCE_PATH=/path/to/model_parent
```

然后 SDF 中写：

```xml
<uri>model://bed</uri>
```

#### 命令行迁移

| Humble / Ignition 命令 | Jazzy / Gazebo Harmonic 命令 |
|:---|:---|
| `ign gazebo world.sdf` | `gz sim world.sdf` |
| `ign gazebo -r world.sdf` | `gz sim -r world.sdf` |
| `ign topic -l` | `gz topic -l` |
| `ign topic -e -t /scan` | `gz topic -e -t /scan` |

ROS launch 仍然用：

```bash
ros2 launch 包名 launch文件.py
```

#### 迁移后的验证顺序

##### 1. 检查 XML / SDF 是否合法

```bash
xmllint --noout src/demo_gazebo_sim/world/house.sdf
xmllint --noout src/demo_gazebo_sim/world/visualize_lidar.sdf
xmllint --noout src/mycar_description/urdf/xacro/gazebo_sensor.urdf.xacro
```

##### 2. 检查 xacro 是否能生成 URDF

```bash
source /opt/ros/jazzy/setup.bash
xacro src/mycar_description/urdf/xacro/car.urdf.xacro -o /tmp/car_check.urdf
```

##### 3. 检查 Gazebo 转 SDF 是否还有 warning

```bash
gz sdf -p /tmp/car_check.urdf
```

如果还有 `gz_frame_id` 或 `alwaysOn` warning，就回到传感器部分继续改。

##### 4. 构建工程

```bash
colcon build --symlink-install
source install/setup.bash
```

##### 5. 启动 Gazebo

```bash
ros2 launch demo_gazebo_sim gazebo_sim_world.launch.py
ros2 launch demo_gazebo_sim gazebo_sim_robot_world.launch.py
```

也可以只跑 Gazebo server 做快速检查：

```bash
gz sim -s -r -v 2 src/demo_gazebo_sim/world/house.sdf --iterations 1
```

#### 常见错误排查

##### 插件找不到

检查是否还写着：

```text
ignition-gazebo-*
libignition-gazebo-*.so
ignition::gazebo::systems::*
```

Jazzy 应该改成：

```text
gz-sim-*-system
gz::sim::systems::*
```

##### GUI 标签 warning

检查是否还有：

```xml
<ignition-gui>
```

Jazzy 应该改成：

```xml
<gz-gui>
```

##### 传感器 warning

检查是否还有：

```xml
<alwaysOn>
<ignition_frame_id>
```

建议改成：

```xml
<always_on>true</always_on>
<pose relative_to="laser">0 0 0 0 0 0</pose>
<gz_frame_id>laser</gz_frame_id>
```

或：

```xml
<pose relative_to="camera">0 0 0 0 0 0</pose>
<gz_frame_id>camera</gz_frame_id>
```

##### robot_state_publisher 的 KDL 根 link 惯性警告

如果日志出现：

```text
The root link base_footprint has an inertia specified in the URDF, but KDL does not support a root link with an inertia.
```

说明 URDF 根 link 带了 `<inertial>`，KDL 不支持。


##### 改了 xacro 但日志仍显示旧内容

先确认旧 launch 已经停止：

```bash
pgrep -af "gazebo|gz sim|robot_state_publisher|parameter_bridge"
```

如果旧的 `robot_state_publisher` 还在，同一个 ROS graph 里 `ros_gz_sim create` 可能会从旧的 `/robot_description` 拿模型。

停止旧进程后重新构建、source、启动：

```bash
colcon build --symlink-install
source install/setup.bash
ros2 launch demo_gazebo_sim gazebo_sim_robot_world.launch.py
```

##### QStandardPaths / libEGL warning

如果日志里只剩：

```text
QStandardPaths: runtime directory '/run/user/1000' is not owned by UID 0
libEGL warning: egl: failed to create dri2 screen
```

这通常不是 Gazebo 迁移错误，而是用 root 启动 GUI 或显卡渲染环境导致的 warning。建议用普通用户运行 ROS/Gazebo，或者检查显卡驱动、Mesa、EGL 环境。

#### 最后再扫一遍旧引用

功能性旧引用检查：

```bash
rg -n "filename=['\"](lib)?ignition|name=['\"]ignition::gazebo|<ignition-gui>|</ignition-gui>|<ignition_frame_id>|</ignition_frame_id>|<gz_frame_id>|</gz_frame_id>|fuel\\.ignitionrobotics\\.org|ros_ign|ign_args|ign_version|alwaysOn" src
```

官方文档提醒的误替换检查：

```bash
rg -n -i "gz-gazebo|gzition|an gz" src
```

如果只在中文注释或教程里搜到旧写法，而功能代码里没有，一般就迁移干净了。


