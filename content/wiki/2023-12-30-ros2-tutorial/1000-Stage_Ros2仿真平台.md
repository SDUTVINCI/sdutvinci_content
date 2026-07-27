---
title: "Stage_Ros2仿真平台"
---

stage_ros2是基于Stage模拟器的ROS2功能包，用于在虚拟环境中模拟和控制机器人的行为。它提供了强大的工具和通信接口，可用于开发、测试和验证机器人系统。通过stage_ros2，用户可以轻松创建虚拟环境，并使用ROS 2发送指令控制机器人的运动，同时接收传感器数据。这个功能包可以帮助用户以更低的成本和风险进行机器人开发，提供了高效且灵活的机器人仿真环境。

*stage使用手册：*

http://rtv.github.io/Stage/modules.html

（ **可以不学** ，连ROS2官方都已经移除掉了Stage， **用更牛X的Gazebo更好** ，但学一学也问题不大）

#### stage_ros2安装与运行
1.安装

因为ROS2官方已经移除掉了Stage，所以我们要通过源码编译手动安装，已经有大佬适配了ROS2版本的Stage：

请先调用如下指令安装依赖：

```bash
sudo apt-get install git cmake g++ libjpeg8-dev libpng-dev libglu1-mesa-dev libltdl-dev libfltk1.1-dev
```

进入ROS2工作空间的src目录，调用如下指令下载相关仓库：

```bash
git clone https://github.com/damuxt/Stage.git
git clone https://github.com/damuxt/stage_ros2.git
```

进入工作空间目录，使用`colcon build`指令进行构建。

（这是第三方作者适配Humble版本的，暂时不支持Jazzy版本，Jazzy版本目前编译会报错，可能作者会更新Jazzy版本的）

2.节点说明

`stage_ros2`节点是stage_ros2功能包的核心节点之一。它通过加载world文件来创建一个仿真场景，包括地图、障碍物和机器人等元素。world文件描述了虚拟环境的属性，节点会根据文件中的描述构建对应的环境。stage_ros2节点利用这个虚拟环境来模拟机器人的运动、感知和控制。通过该节点和world文件的结合，可以进行机器人的仿真和测试。

3.使用

在功能包下，已经内置了使用示例，终端下可以执行如下指令启动示例：

```bash
ros2 launch stage_ros2 my_house.launch.py
```

模拟器以及rviz2将被启动，并且在rviz2中可以显示里程计、激光雷达以及深度相机等相关信息，运行结果如下。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1618.webp)

#### stage_ros2仿真框架搭建
本节开始，我们将介绍如何通过stage_ros2自定义仿真环境。

1.准备工作

首先请创建一个功能包，指令如下：

```bash
ros2 pkg create demo_stage_sim --dependencies stage_ros2
```

然后在功能包下新建launch、world、config目录，并修改功能包下的CMakeLists.txt文件，添加如下代码：

```bash
install(DIRECTORY launch world config DESTINATION share/${PROJECT_NAME})
```

其中，launch目录用于存储launch文件，config用于存储rviz2的配置文件，world用于存储仿真相关文件。world目录中请新建maps目录，并将课程配套资料中的图片素材复制进去。

2.搭建代码框架

在功能包的launch目录下，新建stage_sim.launch.py文件，并输入如下内容：

```bash
import os
from ament_index_python.packages import get_package_share_directory
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    this_directory = get_package_share_directory('demo_stage_sim')
return LaunchDescription([
        Node(
            package='stage_ros2',
            executable='stage_ros2',
            name='stage',
            parameters=[{"world_file": os.path.join(this_directory,'world','sim.world')}],
        )
    ])
```

该launch文件将运行stage_ros2节点，并加载world目录下的sim.world文件。

在world目录下，新建sim.world文件，并输入如下内容：

```bash

# -----------------------------------------------------------------------------

# 设置窗体

# 设置模拟器地图分辨率(以 米/像素 为单位)
resolution 0.02

# 设置模拟器的时间步长(以 毫秒 为单位)
interval_sim 100
```

sim.world文件当前只是声明了仿真环境的一些通用参数。

3.编译

终端中进入当前工作空间，编译功能包：

```bash
colcon build --packages-select demo_stage_sim
```

4.执行

当前工作空间下，启动终端，并输入如下指令：

```bash
. install/setup.bash
ros2 launch demo_stage_sim stage_sim.launch.py
```

执行该launch文件后，将生成一个窗口，该窗口中并无任何内容，下一步我们就可以着手创建具体的仿真内容了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1619.webp)

5.world文件参数

**摘要和默认值**

```bash
name                      <worldfile name>
interval_sim              100
quit_time                 0
resolution                0.02
show_clock                0
show_clock_interval       100
threads                   0
```

**详解**

*   name <string>

*   用于世界的识别名称，例如在图形用户界面的标题栏中使用。

*   interval_sim <float>

*   每次调用 World::Update() 时运行的模拟时间量。每个模型都有其自己可配置的更新间隔，可以大于或小于此值，但比此值更短的间隔在图形用户界面或 World 更新回调中是不可见的。您可能不需要改变默认值100毫秒：此值在客户端内部使用，如Player和WebSim。

*   quit_time <float> 在模拟时间达到指定的秒数后停止模拟。在 libstage 中，World::Update() 返回 true。在带有图形用户界面的 Stage 中，模拟被暂停。在没有图形用户界面的 Stage 中，Stage 会退出。

*   resolution <float> 底层位图模型的分辨率（以米为单位）。较大的值可以加快射线追踪速度，但会以碰撞检测和感知精度为代价。通常情况下，默认值是一个合理的选择。

*   show_clock <int>

*   如果非零，每经过 $show_clock_interval 次更新就在标准输出上打印模拟时间。这对于观察非 GUI 模拟的进展非常有用。

*   show_clock_interval <int>

*   设置在启用 $show_clock 的情况下，在标准输出上打印时间之间的更新次数。默认值是每经过 10 秒模拟时间打印一次。较小的值会稍微降低模拟速度。

*   threads <int>

*   要生成的工作线程数。一些模型可以并行更新（例如激光器、测距仪），在这里运行 2 个或更多线程可能会使模拟运行更快，取决于可用的 CPU 内核数量和世界文件的情况。如果您有启用并行的高分辨率模型（例如带有数百或数千个样本的激光器或大量模型），则每个内核使用一个线程。

#### stage_ros2设置窗体
stage 窗体由菜单栏和模拟世界的视图组成。我们可以放大和缩小视图，并滚动视图以查看模拟世界的更多内容。模拟的机器人设备、障碍物等被渲染为彩色多边形。窗体中还可以实现各种传感器和执行器模型的数据和配置的可视化。菜单则具有用于控制呈现哪些数据和配置的选项。本节我们将介绍如何设置以及操作stage窗体。

1.设置窗体

设置窗体相关参数，可以在stage_sim.launch.py文件中添加如下内容：

```bash

# 配置窗体参数
window
(
  size [ 700.000 700.000 ] # 窗体尺寸(以 像素 为单位)
  scale 35  # 缩放比
  center [ 0  0 ] # 地图相对于窗体的偏移量(以 米 为单位)
  rotate [ 0  0 ] # 地图旋转角度
  show_data 1     # 是否显示传感器数据 1=on 0=off
)
```

上述代码创建了一个窗体对象，该窗体分辨率为700\*700，使用了35倍缩放比，后续加载的地图相对于窗体无偏移无旋转且会以可视化的方式显示传感器数据。

2.编译执行

构建功能包并执行launch文件后，运行结果如下。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1620.webp)

3.窗体参数

**摘要和默认值**

```bash
window
(
  size [ 400 300 ]

  # camera options
  center [ 0 0 ]
  rotate [ 0 0 ]
  scale 1.0

  # perspective camera options
  pcam_loc [ 0 -4 2 ]
  pcam_angle [ 70 0 ]

  # GUI options
  show_data 0
  show_flags 1
  show_blocks 1
  show_clock 1
  show_footprints 0
  show_grid 1
  show_trailarrows 0
  show_trailrise 0
  show_trailfast 0
  show_occupancy 0
  show_tree 0
  pcam_on 0
  screenshots 0
)
```

**详解**

*   speedup <int>

*   Stage 将尝试以实时的这个倍数运行。如果设置为 -1，Stage 将尽可能以最快速度运行，并且不会尝试跟踪实时时间。

*   size \[<width:int><height:int> \]

*   窗口的大小（以像素为单位）。

*   center \[<x:float><y:float> \]

*   窗口中心的位置，使用世界坐标表示（以米为单位）。

*   rotate \[<pitch:float><yaw:float> \]

*   相对于垂直向上的角度，旋转角度（以度为单位）。

*   scale<float>

*   世界坐标到像素坐标的比例（窗口缩放）。

*   pcam_loc \[<x:int><y:int><z:int> \]

*   透视摄像机的位置（以米为单位）。

*   pcam_angle \[<pitch:float><yaw:float> \]

*   透视摄像机的垂直和水平角度。

*   pcam_on<int>

*   是否启用透视摄像机（0/1）。

4.操作窗体

**滚动视图**

在背景上左键单击并拖动以移动你对世界的视图。

**缩放视图**

滚动鼠标滚轮可放大或缩小鼠标光标处的位置。

**保存世界**

你可以保存当前世界中所有事物的位置，使用“文件/保存”菜单项。警告：保存的位置将覆盖当前的世界文件。在保存之前，先复制你的世界文件，以保留旧的位置。另外，可以使用“文件/另存为”菜单项将其保存到一个新的世界文件中。

**暂停和恢复时钟**

按下“p”键可以暂停或恢复模拟。按下“.”（句号）键可以运行一个模拟步骤。按住“.”键可以重复多次步进。步进会让模拟暂停，所以按下“p”键可以恢复运行。可以在世界文件中使用“paused”属性来设置初始的暂停/运行状态。

**选择模型**

可以通过点击左键来选择模型。通过按住Shift键并点击多个模型，也可以选择多个模型。选择的模型可以通过拖动来移动，或者通过按住右键并移动鼠标来旋转。点击世界中的空白位置可以清除选择。在清除选择后，最后选中的单个模型将被保存为影响特定模型的几个视图选项的目标。

**视图选项**

视图菜单提供了许多影响世界渲染方式的功能。在每个选项的右侧，通常有一个按键快捷键，可快速切换相关选项。

“Data”选项（快捷键'd'）可切换传感器数据可视化。滤波器数据选项（快捷键Shift+'d'）打开对话框，可启用或禁用特定传感器的可视化。对话框中的“Visualize All”选项可切换是否为所有模型启用传感器可视化，还是仅为当前选择的模型启用。

“Follow”选项可保持视图始终在最后选择的模型上。

“Perspective camera”选项可以从正交视图切换到透视视图。

**保存截图**

要保存世界的一系列截图，从视图菜单中选择“Save screenshots”选项以开始录制图像，然后再次从菜单中选择该选项以停止。

#### stage_ros2基本模型
基本模型（model）模拟具有基本属性的对象；位置、大小、速度、颜色、对各种传感器的可见性等。基本模型还具有由线性列表组成的主体。在内部，基本模型被用作所有其他模型类型的基类。我们可以使用基本模型来模拟环境对象。在stage中，底盘模型、雷达模型、相机模型等都可以看作是基本模型的子类。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1621.webp)

1.添加基本模型

在stage_sim.launch.py文件中添加如下内容：

```bash

# -----------------------------------------------------------------------------

# 添加障碍物
model( pose [ -2 -4 0 0 ] color "green")

# define a block
define my_block model
(
  size [1.0 1.0 1.0]
  gui_nose 0
  gui_grid 0
  gui_outline 0
)

# throw in a block
my_block( pose [ 0 4 0 90 ] color "red" bitmap "maps/ghost.png")
```

上述代码中，直接使用默认的基础model创建了一个绿色模型对象，并且还自定义了继承自model的my_block模型，然后创建了该模型对象。

2.编译执行

构建功能包并执行launch文件后，运行结果如下。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1622.webp)

3.窗体参数

**摘要和默认值**

```bash
model (
    pose [ 0.0 0.0 0.0 0.0 ]
    size [ 0.1 0.1 0.1 ]
    origin [ 0.0 0.0 0.0 0.0 ]
    velocity [ 0.0 0.0 0.0 0.0 ]
    color "red"
    color_rgba [ 0.0 0.0 0.0 1.0 ]
    bitmap ""
    ctrl ""

    # determine how the model appears in various sensors
    fiducial_return 0
    fiducial_key 0
    obstacle_return 1
    ranger_return 1
    blob_return 1
    laser_return LaserVisible
    gripper_return 0
    gravity_return 0
    sticky_return 0

    # GUI properties
    gui_nose 0
    gui_grid 0
    gui_outline 1
    gui_move 0 (1 if the model has no parents);
    boundary 0
    mass 10.0
    map_resolution 0.1
    say ""
    alwayson 0
)
```

**详解**

*   pose \[ x:<float> y:<float> z:<float> heading:<float> \] 指定模型在其父坐标系中的姿态。

*   size \[ x:<float> y:<float> z:<float> \] 指定模型在各个维度上的大小。

*   origin \[ x:<float> y:<float> z:<float> heading:<float> \] 指定对象中心的位置，相对于其姿态。

*   velocity \[ x:<float> y:<float> z:<float> heading:<float> omega:<float> \] 指定模型的初始速度。请注意，如果模型撞到障碍物，其速度将被设置为零。

*   velocity_enable int (默认为0) 大多数模型忽略其速度状态。这样可以节省处理时间，因为大多数模型的速度永远不会被设置为非零值。一些子类（例如ModelPosition）会改变这个默认值，因为它们期望移动。用户可以在这里指定一个非零值来启用对该模型的速度控制。这与调用Model::VelocityEnable()的效果相同。

*   color <string> 使用X11数据库（rgb.txt）中的颜色名称指定对象的颜色。

*   bitmap filename:<string> 通过解释位图中的线条来绘制模型（支持bmp、jpeg、gif、png）。文件被打开并解析为一组线条。这些线条被缩放以适应模型当前大小所定义的矩形内。

*   ctrl <string> 指定模型的控制器模块及其参数字符串。例如，字符串"foo bar bash"将加载libfoo.so，其Init()函数将以整个字符串作为参数进行调用（包括库名称）。控制器需要解析字符串（如果需要参数）。

*   fiducial_return fiducial_id <int> 如果非零，则通过fiducialfinder传感器检测到此模型。该值用作基准标识符。

*   fiducial_key <int> 仅当模型和fiducialfinder的fiducial_key值匹配时，fiducial_id模型才会被fiducialfinder检测到。这允许您在同一环境中拥有几种独立类型的基准标识，每种类型只显示在为其"调谐"的fiducialfinders中。

*   obstacle_return <int> 如果为1，则此模型可以与具有此属性设置的其他模型发生碰撞。

*   ranger_return <int> 如果为1，则该模型可以被ranger传感器检测到。

*   blob_return <int> 如果为1，则该模型可以在blob_finder中被检测到（取决于其颜色）。

*   laser_return <int> 如果为0，则此模型不会被激光传感器检测到。如果为1，则该模型在激光传感器中显示为正常（0）反射。如果为2，则显示为高（1）反射。

*   gripper_return <int> 如果为1，则该模型可以被夹爪夹取，并且可以通过与具有非零obstacle_return的任何东西的碰撞来推动。

*   gui_nose <int> 如果为1，则在模型上绘制显示其朝向的鼻子（正X轴）。

*   gui_grid <int> 如果为1，则在模型上绘制比例网格。

*   gui_outline <int> 如果为1，则在模型周围绘制边界框，指示其大小。

*   gui_move <int> 如果为1，则模型可以在GUI窗口中通过鼠标移动。

#### stage_ros2添加地图
本节我们将在仿真环境中，添加一张全局地图，请先准备一张作为全局地图的图片。

1.添加地图

在stage_sim.launch.py文件中添加如下内容：

```bash

# -----------------------------------------------------------------------------

# 设置地图(定义模型)
define floorplan model
(
  color "gray30"  # 颜色
  boundary 1  # 为地图设置边框

  gui_nose 0
  gui_grid 0 

  gui_outline 0
  gripper_return 0
  fiducial_return 0
  laser_return 1
)

# 加载地图
floorplan
( 
  name "room"
  size [16.000 16.000 0.800]
  pose [0 0 0 0]
  bitmap "maps/room.jpg"
  gui_move 0
)
```

上述代码自定义了floorplan模型，并根据此模型创建了一个加载了地图数据的floorplan对象。

2.编译执行

构建功能包并执行launch文件后，运行结果如下。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1623.webp)

#### stage_ros2添加机器人
本节将在仿真环境中添加一个由底盘、摄像头以及激光雷达组成的虚拟机器人。

1.代码框架搭建

在world目录下新建robot目录，robot目录中再创建car_base.inc、camera.inc、laser.inc以及mycar.inc等文件，各文件作用如下：

*   car_base.inc：用于设置机器人底盘模块；

*   camera.inc：用于设置机器人相机模块；

*   laser.inc：用于设置机器人雷达模块；

*   mycar.inc：用于组装机器人各模块。

除此之外，还会在sim.world中包含mycar.inc并调用其生成机器人的功能。

2.设置机器人底盘

stage中的position模型可以以差速、全向或阿克曼的方式模拟移动机器人底盘。在car_base.inc中输入如下代码：

```bash

# 机器人底盘配置
define car_base position 
(
  color "red"                   # 车身颜色
  drive "diff"                  # 车辆运动学模型                  
  obstacle_return 1             
  ranger_return 1           
  blob_return 1                  
  fiducial_return 1             

  localization "odom"           # 定位方式
  odom_error [ 0.05 0.05 0.0 0.1 ]  # 里程计误差

  # localization_origin [0 0 0 0]   # 定位原点，默认为机器人的初始位置。

  # [ xmin xmax ymin ymax zmin zmax amin amax ]        
  velocity_bounds [-1 1 0 0 0 0 -45.0 45.0 ]         # 速度最值 
  acceleration_bounds [-0.5 0.5 0 0 0 0 -45 45.0 ]   # 加速度最值

  size [0.44 0.38 0.22]  # 车体尺寸
  origin [0 0 0 0] # 旋转中心与车体中心的偏移量
  mass 23.0 # 车体质量，单位kg

  gui_nose 0 # 是否绘制方向指示标记

  block( 
    points 8
    point[0] [-0.2 0.18]
    point[1] [-0.2 -0.18]
    point[2] [-0.15 -0.27]
    point[3] [0.12 -0.23]
    point[4] [0.2 -0.12]
    point[5] [0.2 0.12]
    point[6] [0.12 0.23]
    point[7] [-0.15 0.27]
    z [0 0.22]
  )
)
```

**position摘要和默认值**

```bash
position(
    drive "diff"
    localization "gps"
    localization_origin [<defaults to model's start pose>]
    odom_error [0.03 0.03 0.00 0.05]
    velocity_enable 1
)
```

**详解**

*   drive "diff", "omni" 或 "car" 选择差速转向模型、全向模式或类似汽车的阿克曼模式。

*   localization "gps" 或 "odom" 如果选择 “gps”，位置模型将以完全准确的精度报告位置。如果选择 “odom”，将使用简单的里程计模型，并且位置数据会随时间与真实位置的差异而漂移。里程计模型由 odom_error 属性参数化。

*   localization_origin \[x y z theta\] 您可以使用 localization_origin 参数来设置定位坐标系的原点。默认情况下，它将复制模型的初始姿态，因此机器人将报告相对于起始位置的位置。提示: 如果将 localization_origin 设置为 \[0 0 0 0\] 并且定位方式为 “gps”，模型将返回其真实的全局位置。这种设置是不现实的，但在希望抽象定位细节的研究中很有用。

*   odom_error \[x y z theta\] 在选择 “odom” 定位方式时用到的里程计误差模型参数，每个值是计算里程计位置估计时积分 x、y 和 theta 速度的误差比例的最大值。对于每个轴，如果在此处指定的值为 E，则实际比例在启动时在 -E/2 到 +E/2 的范围内随机选择。请注意，由于舍入误差，将这些值设置为零并不能让定位完美无误 - 为了实现这一点，您需要选择 “gps” 定位方式。

3.设置摄像头

stage中的camera模型可以模拟深度相机。在camera.inc中输入如下代码：

```bash
define my_camera camera
(
    range [ 0.3 3.0 ] # 相机采样范围
    resolution [ 160 90 ]  #相机分辨率 1280 × 720 / 8
    fov [ 87 58 ] # 相机视场
    pantilt [ 0 0 ] # 相机姿态
    alwayson 1 # 是否一直处于启动状态
    size [ 0.025 0.09 0.025 ] # 相机尺寸
    color "gray" # 相机颜色
)
```

**camera摘要和默认值**

```bash
camera(
  resolution [ 32 32 ]
  range [ 0.2 8.0 ]
  fov [ 70.0 40.0 ]
  pantilt [ 0.0 0.0 ]
  size [ 0.1 0.07 0.05 ]
  color "black"
  watts 100.0 
)
```

**详解**

*   resolution \[ width:<int> height:<int>\] 相机分辨率。

*   range \[ min:<float> max:<float> \] 相机报告的距离范围，以米为单位。距离小于`min`或大于`max`的物体将无法显示。`min`数字越小，深度精度越低 - 不要将此值设置得太接近 0。

*   fov \[ horizontal: <float> vertical: <float> \] 水平和垂直视野的角度，以度为单位。

*   pantilt \[ pan:<float> tilt:<float> \] 相机的朝向角度，以度为单位。左右位置称为 pan，上下位置称为 tilt。

4.设置激光雷达

stage中的ranger模型可以模拟激光雷达。在laser.inc中输入如下代码：

```bash
define my_laser ranger
(
  sensor(

    range [ 0.0  15.0 ]     # 雷达数据采集区间
    fov 360.0               # 视角
    samples 720             # 采样数
    color_rgba [ 0 0 1 0.15 ] # 可视化光束颜色以及透明度
  )
  model # 雷达外观
  (
    pose [ 0 0 0 0 ]        # 雷达位姿
    size [ 0.07 0.07 0.05 ] # 雷达尺寸信息
    color "blue"            # 雷达颜色
  )
)
```

**ranger摘要和默认值**

```bash
 sensor(
   samples 180
   range_max 8.0
   fov 360.0
   resolution 1
   size [ 0.15 0.15 0.2 ]
   color "blue"
 )
```

**详解**

*   samples <int> 每次扫描的激光样本数量。

*   range_max <float> 激光扫描仪所报告的最大距离，以米为单位。扫描仪将不会检测超出此范围的物体。

*   fov <float> 激光扫描仪的角度视野。

*   resolution <int> 仅计算第 n 个激光样本的真实距离。缺失的样本将用线性插值填充。一般来说，使用较少的样本会更好，但某些（实现不好的）程序需要固定数量的样本数。设置此数字大于 1 可以减少所需的计算量，适用于固定大小的激光矢量。

5.组装机器人

在mycar.inc中实现机器人的组装：

```SQL

# 组装机器人各个模块
include "robot/car_base.inc"
include "robot/camera.inc"
include "robot/laser.inc"

define my_car car_base(
    my_camera(pose [0.15 0 0 0])
    my_laser()
)
```

my_car模型是在car_base模型之上，集成了my_camera和my_laser模型。

6.在仿真环境中生成机器人

在sim.world中添加如下代码生成一个机器人：

```SQL

# -----------------------------------------------------------------------------

# 生成机器人

# 文件包含
include "robot/mycar.inc"

my_car(
  name "robot_0"
  color "red"
  pose [ -3 -7 0 90 ] 
)
```

7.构建执行

构建功能包并执行launch文件后，运行结果如下。

启动rviz2可以查看仿真环境下传感器相关数据，启动键盘控制节点后，也可以控制机器人运动。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1624.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1625.webp)

#### stage_ros2添加多机器人
在stage_ros2中也可以很方便的生成多个机器人。

1.生成多个机器人

在sim.world中还可以继续创建my_car对象以生成新的机器人模型，添加代码如下：

```bash
my_car(
  name "robot_1"
  color "yellow"
  pose [ -1 -7 0 90 ] 
)
```

2.构建执行

构建功能包并执行launch文件后，运行结果如下。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1626.webp)

在多机器人运行时，不同的机器人发布的话题会以各自对应的`name`值为命名空间，且不同机器人发布的坐标变换也会以`name`值为前缀。
