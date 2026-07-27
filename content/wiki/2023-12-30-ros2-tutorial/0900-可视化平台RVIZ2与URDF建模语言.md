---
title: "可视化平台RVIZ2与URDF建模语言"
---

### 可视化简介
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1353.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1354.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1355.webp)

坐标相关、激光雷达相关、摄像头相关的rviz2插件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1356.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1357.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1358.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1359.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1360.webp)

### rviz2基本使用
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1361.webp)

以`sudo apt install ros-[ROS_DISTRO]-desktop`格式安装ROS2时，RViz已经默认被安装了。

```bash
sudo apt install ros-[ROS_DISTRO]-rviz2
```

**备注：** 命令中的 \[ROS_DISTRO\] 指代ROS2版本。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1362.webp)

方式1：`rviz2`；

方式2：`ros2 run rviz2 rviz2`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1363.webp)

**rviz2 启动之后，默认界面如下：**

1.  上部为工具栏：包括视角控制、预估位姿设置、目标设置等，还可以添加自定义插件；

2.  左侧为插件显示区：包括添加、删除、复制、重命名插件，显示插件，以及设置插件属性等功能；

3.  中间为3D试图显示区：以可视化的方式显示添加的插件信息；

4.  右侧为观测视角设置区：可以设置不同的观测视角；

5.  下侧为时间显示区：包括系统时间和ROS时间。

**左侧插件显示区默认有两个插件：**

*   Global Options：该插件用于设置全局显示相关的参数，一般情况下，需要自行设置的是 Fixed Frame 选项，该选项是其他所有数据在可视化显示时所参考的全局坐标系；

*   Global Status：该插件用于显示在 Global Options 设置完毕 Fixed Frame 之后，所有的坐标变换是否正常。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1364.webp)

最上面是菜单区，左侧是插件显示区，中间是3D调试区，右侧是视角切换区域，最下方是时间区ROS Time是ROS2时间，Wall Time是系统时间，Elapsed是Rviz2运行的时间。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1365.webp)

可以保存rviz2的配置，也可以打开配置

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1366.webp)

设置显示面板

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1367.webp)

可以设置平面网格的个数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1368.webp)

可以设置竖直方向网格的个数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1369.webp)

可以设置网格边长是多大

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1370.webp)

改变sRGB的4个通道

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1371.webp)

可以改变视角

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1372.webp)

设置偏移量，如果Z是-1，那么网格会相对于坐标系下沉1个单位

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1373.webp)

Fixed name一般是根坐标系的名称

background color就是背景色

frame rate是坐标系的发布频率

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1374.webp)

全局状态，当fixed name设置对后，就无警告了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1375.webp)

视角切换(一般默认)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1376.webp)

可以翻转Z轴

**常用插件：**

| 序号 | 名称 | 功能 | 消息类型 |
|:---|:---|:---|:---|
| 1 | Axes | 显示 rviz2 默认的坐标系。 |  |
| 2 | Camera | 显示相机图像，必须需要使用消息：CameraInfo。 | sensor_msgs/msg/Image，sensor_msgs/msg/CameraInfo |
| 3 | Grid | 显示以参考坐标系原点为中心的网格。 |  |
| 4 | Grid Cells | 从网格中绘制单元格，通常是导航堆栈中成本地图中的障碍物。 | nav_msgs/msg/GridCells |
| 5 | Image | 显示相机图像，但是和Camera插件不同，它不需要使用 CameraInfo 消息。 | sensor_msgs/msg/Image |
| 6 | InteractiveMarker | 显示来自一个或多个交互式标记服务器的 3D 对象，并允许与它们进行鼠标交互。 | visualization_msgs/msg/InteractiveMarker |
| 7 | Laser Scan | 显示激光雷达数据。 | sensor_msgs/msg/LaserScan |
| 8 | Map | 显示地图数据。 | nav_msgs/msg/OccupancyGrid |
| 9 | Markers | 允许开发者通过主题显示任意原始形状的几何体。 | visualization_msgs/msg/Marker，visualization_msgs/msg/MarkerArray |
| 10 | Path | 显示机器人导航中的路径相关数据。 | nav_msgs/msg/Path |
| 11 | PointStamped | 以小球的形式绘制一个点。 | geometry_msgs/msg/PointStamped |
| 12 | Pose | 以箭头或坐标轴的方式绘制位姿。 | geometry_msgs/msg/PoseStamped |
| 13 | Pose Array | 绘制一组 Pose。 | geometry_msgs/msg/PoseArray |
| 14 | Point Cloud2 | 绘制点云数据。 | sensor_msgs/msg/PointCloud，sensor_msgs/msg/PointCloud2 |
| 15 | Polygon | 将多边形的轮廓绘制为线。 | geometry_msgs/msg/Polygon |
| 16 | Odometry | 显示随着时间推移累积的里程计消息。 | nav_msgs/msg/Odometry |
| 17 | Range | 显示表示来自声纳或红外距离传感器的距离测量值的圆锥。 | sensor_msgs/msg/Range |
| 18 | RobotModel | 显示机器人模型。 |  |
| 19 | TF | 显示 tf 变换层次结构。 |  |
| 20 | Wrench | 将geometry_msgs /WrenchStamped消息显示为表示力的箭头和表示扭矩的箭头加圆圈。 | geometry_msgs/msg/WrenchStamped |
| 21 | Oculus | 将 RViz 场景渲染到 Oculus 头戴设备。 |  |

上述每一种插件又包含了诸多属性，可以通过设置插件属性来控制插件的最终显示效果。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1377.webp)

Image是摄像头数据插件

LaserScan是激光雷达数据插件

TF是坐标变换插件

RobotModel是机器人模型插件

```TypeScript
ros2 run rviz2 rviz2 -d xxx.rviz
#可以读取自己保存的rviz配置
```

### rviz2集成URDF基本流程
####   案例分析
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1378.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1379.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1380.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1381.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1382.webp)

请调用如下命令，安装案例所需的两个功能包(可以控制机器人关节运动)：

```bash
sudo apt install ros-humble-joint-state-publisher
sudo apt install ros-humble-joint-state-publisher-gui
```

终端下进入工作空间的src目录，调用如下命令创建C++功能包。

```bash
ros2 pkg create cpp06_urdf --build-type ament_cmake
```

功能包下新建 urdf、rviz、launch、meshes目录以备用，其中 urdf 目录下再新建子目录 urdf 与 xacro，分别用于存储 urdf 文件和 xacro 文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1383.webp)

launch存放launch文件

urdf文件里面存放urdf三维模型文件

meshes存放stl模型

xacro可以简化urdf文件，并且增强其灵活性

rviz存放rviz2的配置

####   框架搭建
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1384.webp)

```xml

 <robot> name="hello_world"
   <link> name="base_link"
     <visual>
       <geometry>
         <box size="0.5 0.2 0.1"/>
       </geometry>
     </visual>
   </link>
 </robot>
```

  标准的XML文件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1385.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1386.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node
import os
from ament_index_python.packages import get_package_share_directory
from launch_ros.parameter_descriptions import ParameterValue
from launch.substitutions import Command,LaunchConfiguration
from launch.actions import DeclareLaunchArgument

#示例：ros2 launch cpp06_urdf display.launch.py model:=`ros2 pkg prefix --share cpp06_urdf`/urdf/urdf/demo01_helloworld.urdf
def generate_launch_description():

    cpp06_urdf_dir = get_package_share_directory("cpp06_urdf")
    default_model_path = os.path.join(cpp06_urdf_dir,"urdf/urdf","demo01_helloworld.urdf")
    default_rviz_path = os.path.join(cpp06_urdf_dir,"rviz","display.rviz")
    model = DeclareLaunchArgument(name="model", default_value=default_model_path)

    # 加载机器人模型

    # 1.启动 robot_state_publisher 节点并以参数方式加载 urdf 文件
    robot_description = ParameterValue(Command(["xacro ",LaunchConfiguration("model")]))
    robot_state_publisher = Node(
        package="robot_state_publisher",
        executable="robot_state_publisher",
        parameters=[{"robot_description": robot_description}]
    )

    # 2.启动 joint_state_publisher 节点发布非固定关节状态
    joint_state_publisher = Node(
        package="joint_state_publisher",
        executable="joint_state_publisher"
    )

    # rviz2 节点
    rviz2 = Node(
        package="rviz2",
        executable="rviz2"

        # arguments=["-d", default_rviz_path]
    )
    return LaunchDescription([
        model,
        robot_state_publisher,
        joint_state_publisher,
        rviz2
    ])
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1387.webp)

```xml
<exec_depend>rviz2</exec_depend>
<exec_depend>xacro</exec_depend>
<exec_depend>robot_state_publisher</exec_depend>
<exec_depend>joint_state_publisher</exec_depend>
<exec_depend>ros2launch</exec_depend>
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1388.webp)

```cmake
install(
  DIRECTORY launch urdf rviz meshes
  DESTINATION share/${PROJECT_NAME}  
)
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1389.webp)

```bash
colcon build --packages-select cpp06_urdf
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1390.webp)

```bash
source install/setup.bash
ros2 launch cpp06_urdf display.launch.py
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1391.webp)

   **小提示：**

  在本章的后续案例中，所有实现都遵循上述步骤，在后续案例中我们只需要关注 urdf 实现即可，launch 文件和 配置文件无需修改。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1392.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1393.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1394.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1395.webp)

####   urdf文件
  按ctrl+\\生成注释

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1396.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1397.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1398.webp)

因为安装过urdf插件，所以有提示，需要创建robot根标签

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1399.webp)

第一个属性为机器人名字

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1400.webp)

第二个有个xml namespace，指向xacro

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1401.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1402.webp)

第三个有个xml namespace，指向xacro，然后还有一个机器人名字

(暂时用最简单的，也就是第一个)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1403.webp)

link标签叫连杆，也需要起个名字，连杆一般指刚体部分

link有个子集标签，叫visual

visual标签下要写机器人形状

然后该标签下又有一个子集标签叫geometry(几何形状)

然后又有子集标签叫box(矩形体状)后面的size后面对应长宽高

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1404.webp)

```xml

 <robot name="boxrobot"> 

    <link name="base_link"> 

      <visual>

        <geometry>

          <box size="1.0 0.5 0.1"/>
        </geometry>
      </visual>
    </link>
  </robot>
```

####   xacro工具(将磁盘文件加载到ROS2中的工具)
搜索是否安装过xacro

```bash
ros2 pkg list | grep -i xacro
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1405.webp)

如果打印了xacro说明安装了，如果没打印，则要手动安装

```bash
sudo apt-get update
sudo apt-get install ros-humble-xacro
```

使用xacro读取文件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1406.webp)

文件里的内容被输出到了终端，咱们一般集成到launch文件中。咱们在终端里是只能查看内容，但是用launch就可以把文件弄到节点里，也就是集成到ROS2里。

####   launch核心实现
核心实现就三步，加载机器人模型，节点发布非固定关节的状态，启动rviz2节点

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1407.webp)

创建rviz2节点很简单，就声明下包名，声明下executable。

加载机器人模型比较复杂，加载机器人模型，也要创建一个节点，

然后有参数，参数里有个键叫robot_description，然后这个键对应一个值

值是ParameterValue对象，这个对象里执行了一个指令，叫xacro，然后后面又有一个Launch配置，其实就是urdf文件的路径。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1408.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1409.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1410.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1411.webp)

这个值，其实就是URDF文件里的内容，但是内容太长了，所以我们把它封装成一个对象。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1412.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1413.webp)

命令行，不能直接当对象参数值，所以还要封装

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1414.webp)

Comand是专门封装终端指令执行的

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1415.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1416.webp)

记得xacro后面要有空格，这里填路径

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1417.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1418.webp)

此时已经定位到cpp06_urdf的share路径下的cpp06_urdf路径了，返回的也就是该路径的字符串

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1419.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1420.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1421.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node

# 封装终端指令相关类

# from launch.actions import ExecuteProcess

# from launch.substitutions import FindExecutable

# 参数声明与获取

# from launch.actions import DeclareLaunchArgument

# from launch.substitutions import LaunchConfiguration

# 文件包含相关

# from launch.actions import IncludeLaunchDescription

# from launch.launch_description_sources import PythonLaunchDescriptionSource

# 分组相关

# from launch_ros.actions import PushRosNamespace

# from launch.actions import GroupAction

# 事件相关

# from launch.event_handlers import OnProcessStart,OnProcessExit

# from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo

# 获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

from launch_ros.parameter_descriptions import ParameterValue
from launch.substitutions import Command

p_value = ParameterValue(Command(["xacro ",get_package_share_directory("cpp06_urdf") + "/urdf/urdf/demo01_boxrobot.urdf"]))
robot_state_pub = Node(
    package="robot_state_publisher",
    executable="robot_state_publisher",
    parameters=[{"robot_description":p_value}]
)

rviz2 = Node(
    package="rviz2",
    executable="rviz2"
    )

def generate_launch_description():
    return LaunchDescription([robot_state_pub,rviz2])
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1422.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1423.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1424.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1425.webp)

点击左下角Add添加RobotModel插件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1426.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1427.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1428.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1429.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1430.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1431.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1432.webp)

新建一个坐标系插件，长沿着X，宽沿着Y，高沿着Z

####   launch优化说明与实现
我们还需要优化三个点，第一个是打开关节节点，第二是设置rviz2默认配置文件，第三是Launch文件中我们将读取的urdf文件写死了，所以要优化结构。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1433.webp)

还要启动这个节点，来控制关节运动，可以改成joint_state_publisher_gui，出现图形化界面。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1434.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1435.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1436.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1437.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1438.webp)

保存一下rviz2的配置

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1439.webp)

正常的指令是

```bash
ros2 run rviz2 rviz2 -d rviz2配置的路径
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1440.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1441.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1442.webp)

创建一个参数叫model，值是后面那一长串。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1443.webp)

LaunchConfiguration是解析参数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1444.webp)

记得要把model放在最前面，放在后面是不可以的，现在已经把路径封装完毕了。

现在启动是正常启动默认的urdf路径。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1445.webp)

解析非默认值的urdf，在终端里也有类似于get_package_share_directory，以下就是(这里参数model少写了个L)要把参数值用反引号(ESC与TAB中间的按键)框起来。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1446.webp)

```bash
ros2 pkg prefix --share cpp06_urdf

ros2 run launch cpp06_urdf display.launch.py model:=`ros2 pkg prefix --share cpp06_urdf`/urdf/urdf/hahah.urdf
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1447.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1448.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1449.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node

# 封装终端指令相关类

# from launch.actions import ExecuteProcess

# from launch.substitutions import FindExecutable

# 参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration

# 文件包含相关

# from launch.actions import IncludeLaunchDescription

# from launch.launch_description_sources import PythonLaunchDescriptionSource

# 分组相关

# from launch_ros.actions import PushRosNamespace

# from launch.actions import GroupAction

# 事件相关

# from launch.event_handlers import OnProcessStart,OnProcessExit

# from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo

# 获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

from launch_ros.parameter_descriptions import ParameterValue
from launch.substitutions import Command

model = DeclareLaunchArgument(name="model",default_value=get_package_share_directory("cpp06_urdf") + "/urdf/urdf/demo01_boxrobot.urdf")

p_value = ParameterValue(Command(["xacro ",LaunchConfiguration("model")]))
robot_state_pub = Node(
    package="robot_state_publisher",
    executable="robot_state_publisher",
    parameters=[{"robot_description":p_value}]
)

joint_state_pub = Node(
    package="joint_state_publisher",
    executable="joint_state_publisher"
    )

rviz2 = Node(
    package="rviz2",
    executable="rviz2",
    arguments=["-d",get_package_share_directory("cpp06_urdf") + "/rviz/urdf.rviz"]
    )

def generate_launch_description():
    return LaunchDescription([model,robot_state_pub,joint_state_pub,rviz2])
```

继续优化最终的代码为：

```python
from launch import LaunchDescription
from launch_ros.actions import Node

# 封装终端指令相关类

# from launch.actions import ExecuteProcess

# from launch.substitutions import FindExecutable

# 参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration

# 文件包含相关

# from launch.actions import IncludeLaunchDescription

# from launch.launch_description_sources import PythonLaunchDescriptionSource

# 分组相关

# from launch_ros.actions import PushRosNamespace

# from launch.actions import GroupAction

# 事件相关

# from launch.event_handlers import OnProcessStart,OnProcessExit

# from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo

# 获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

from launch_ros.parameter_descriptions import ParameterValue
from launch.substitutions import Command

import os

cpp06_urdf_dir = get_package_share_directory("cpp06_urdf")

default_model_path = os.path.join(cpp06_urdf_dir,"urdf/urdf","demo01_boxrobot.urdf")
default_rviz_path = os.path.join(cpp06_urdf_dir,"rviz","urdf.rviz")

model = DeclareLaunchArgument(name="model",default_value=default_model_path)

p_value = ParameterValue(Command(["xacro ",LaunchConfiguration("model")]))
robot_state_pub = Node(
    package="robot_state_publisher",
    executable="robot_state_publisher",
    parameters=[{"robot_description":p_value}]
)

# 关节信息节点

# joint_state_pub = Node(

#     package="joint_state_publisher",

#     executable="joint_state_publisher"

# )

# 关节信息节点图形界面(建议)
joint_state_pub = Node(
    package="joint_state_publisher_gui",
    executable="joint_state_publisher_gui"
)

rviz2 = Node(
    package="rviz2",
    executable="rviz2",

#    arguments=["-d",get_package_share_directory("cpp06_urdf") + "/rviz/urdf.rviz"]
    arguments=["-d",default_rviz_path]

    )

def generate_launch_description():
    return LaunchDescription([model,robot_state_pub,joint_state_pub,rviz2])
```

### URDF语法
#### 简介
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1450.webp)

#### robot根标签
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1451.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1452.webp)

对机器人进行分割，分割成几个子集，比如一个子集描述头，一个子集描述身子，最后再合成合集，成机器人

虽然我的子文件和主文件在逻辑上是有包含关系的，但是其实，他们都是单独的urdf文件。主文件中，robot标签的name属性必须写，子文件可以不写，如果写，那么子文件name的值与主文件的必须相同！

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1453.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1454.webp)

#### link标签
##### 简介
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1455.webp)

每一个link都是刚体，都是独立部件

Link是通过joint进行拼接的

Link主要包含三部分，Visual，Collision和Inertial

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1456.webp)

没加尖括号的是属性，加了的是标签

**<visual>** （可选）：用于描述link的可视化属性，可以设置link的形状（立方体、球体、圆柱等）。

*   **name** （可选）：指定link名称，此名称会映射为同名坐标系，还可以通过引用该值定位定位link。

*   **<geometry>** （必填）：用于设置link的形状，比如：立方体、球体或圆柱。

    *   **<box>** ：立方体标签，通过size属性设置立方体的边长，原点为其几何中心。

    *   **<cylinder>** ：圆柱标签，通过radius属性设置圆柱半径，通过length属性设置圆柱高度，原点为其几何中心。

    *   **<sphere>** ：球体标签，通过radius属性设置球体半径，原点为其几何中心。

    *   **<mesh>** ：通过属性filename引用“皮肤”文件，为link设置外观，该文件必须是本地文件。使用 package://<packagename>/<path>为文件名添加前缀。

    *   **<origin>** （可选）：用于设置link的相对偏移量以及旋转角度，如未指定则使用默认值（无偏移且无旋转）。

        *   **xyz** ：表示x、y、z三个维度上的偏移量（以米为单位），不同数值之间使用空格分隔，如未指定则使用默认值（三个维度无偏移）。

        *   **rpy** ：表示翻滚、俯仰与偏航的角度（以弧度为单位），不同数值之间使用空格分隔，如未指定则使用默认值（三个维度无旋转）。

    *   **<material>** （可选）：视觉元素的材质。也可以在根标签robot中定义material标签，然后，可以在link中按名称进行引用。

        *   **name** （可选）：为material指定名称，可以通过该值进行引用。

        *   **<color>** （可选）：rgba 材质的颜色，由代表red/green/blue/alpha 的四个数字组成，每个数字的范围为 \[0,1\]。

        *   **<texture>** （可选）：材质的纹理，可以由属性filename设置。

当有多个Visual的时候，需要给Visual设置name，所以是个可选项。

Collision与仿真有关系，我们可以给我们的机器人的刚体设置一个碰撞区间，只要障碍物进入了区间，那么就发生了碰撞，一般碰撞区间要比实际大小要大。

**<collision>** （可选）：link的碰撞属性。可以与link的视觉属性一致，也可以不同，比如：我们会通常使用更简单的碰撞模型来减少计算时间，或者设置的值大于link的视觉属性，以尽量避免碰撞。另外，同一链接可以存在多个 <collision>标签实例，多个几何图形组合表示link的碰撞属性。

*   **name** （可选）：为collision设置名称。

*   **<geometry>** （必须）：请参考visual标签的geometry使用规则。

*   **<origin>** （可选）：请参考visual标签的origin使用规则。

Inertial是设置惯性矩阵的，也是和仿真有关系的。比如说机器人刹车，会出现前倾的情况，比如说惯性矩阵的重心高一些，那么急刹车就会出现翻车的情况了。

**<inertial>** （可选）：用于设置link的质量、质心位置和中心惯性特性，如果未指定，则默认为质量为0、惯性为0。

*   **<origin>** （可选）：该位姿（平移、旋转）描述了链接的质心框架 C 相对于链接框架 L 的位置和方向。

    *   **xyz** ：表示从 Lo（链接框架原点）到 Co（链接的质心）的位置向量为 x L̂x + y L̂y + z L̂z，其中 L̂x、L̂y、L̂z 是链接框架 L 的正交单位向量。

    *   **rpy** ：将 C 的单位向量 Ĉx、Ĉy、Ĉz 相对于链接框架 L 的方向表示为以弧度为单位的欧拉旋转序列 (r p y)。注意：Ĉx、Ĉy、Ĉz 不需要与连杆的惯性主轴对齐。

*   **<mass>** （必填）：通过其value属性设置link的质量。

*   **<inertia>** （必填）：对于固定在质心坐标系 C 中的单位向量 Ĉx、Ĉy、Ĉz，该连杆的惯性矩 ixx、iyy、izz 以及关于 Co（连杆的质心）的惯性 ixy、ixz、iyz 的乘积。

**注意：** <collision> 和 <inertial> 在仿真环境下才需要使用到，如果只是在 rviz2 中集成 urdf，那么不必须为 link 定义这两个标签。

##### 使用
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1457.webp)

```xml
<robot name="link_demo">

  <material name="yellow">
    <color rgba="0.7 0.7 0 0.8" />
  </material>
  <link name="base_link">
    <visual>

        <geometry>

            <box size="0.5 0.3 0.1" />

        </geometry>

        <origin xyz="0 0 0" rpy="0 0 0" />

        <material name="yellow"/>
    </visual>
  </link>
</robot>
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1458.webp)

```bash
ros2 launch cpp06_urdf display.launch.py model:=`ros2 pkg prefix --share cpp06_urdf`/urdf/urdf/demo02_link.urdf
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1459.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1460.webp)

矩形体，球形，圆柱体

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1461.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1462.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1463.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1464.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1465.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1466.webp)

平移量，X平移1，Y和Z平移0，rpy旋转度设置为0(旋转度分别是欧拉角里的翻滚角Roll(绕X)，俯仰角Pitch(绕Y)，航向角Yaw(绕Z))

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1467.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1468.webp)

发现长方体在X上偏移了1

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1469.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1470.webp)

让航向角Yaw(绕Z运动)为0.5 rad

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1471.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1472.webp)

设置翻滚角Roll(绕X运动)为0.5rad

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1473.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1474.webp)

设置俯仰角Pitch(绕Y运动)为0.5rad

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1475.webp)

sRGB是R,G,B,Alpha(浮点模型，所以范围是0-1.0)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1476.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1477.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1478.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1479.webp)

如果一个颜色要被用好几次，可以封装成一个类似于全局变量的东西，然后在其他link中调用时，直接用<material name="对应属性值" /> （这里是个闭环，注意）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1480.webp)

```xml
<robot name="link_demo">

    <material name="yellow">
      <color rgba="0.7 0.7 0 0.8" />
    </material>
    <link name="base_link">
      <visual>

          <geometry>

              <box size="0.5 0.3 0.1" />

          </geometry>

          <origin xyz="0 0 0" rpy="0 0 0" />

          <material name="yellow"/>
      </visual>
    </link>
  </robot>
```

##### 使用补充
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1481.webp)

mesh标签是引用皮肤文件，一般是stl文件，可以用SolidWorks导出，可以看文档后面的SW2URDF

如果不会使用solidworks，可以学，这东西2天半就能学会，只是一个工具，只会画图没有啥水平，最重要的还是机械设计比较难。想学的可以看兄弟社团机械学会微信公众号的视频进行学习。

https://mp.weixin.qq.com/mp/homepage?__biz=MzI4MjkyMDgyMA==&hid=7&sn=1efc3d3cee0142970227785f767cc7c8&scene=18

当然，没时间学习可以直接用别人画好的机器人，在Github上搜索turtlebot3

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1482.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1483.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1484.webp)

这里有已经导出的模型，bases里是外观的模型，sensors是传感器的模型，wheels是轮子的模型。

我们克隆下仓库，注意要克隆branch是ros2的分支。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1485.webp)

我们引用一个就行了，看看效果即可，真正的应用还是要用SolidWorks通过SW2URDF插件进行导出

把项目里的meshes/bases里的burger_base.stl拷贝到我们WS里的meshes目录

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1486.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1487.webp)

filename是写刚才的皮肤文件，package://就是协议名，后面跟包名，也就是cpp06_urdf。然后跟功能包下的文件路径，也就是meshes/burger_base.stl（其实也就是share下的路径）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1488.webp)

因为是个三维模型，所以在填scale大小缩放时，需要填3个比例，咱们都填1.0即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1489.webp)

为什么显示的模型会这么大呢，因为rviz2以米为单位，而stl是以mm为单位，注意。在机械上，默认不说单位就都是mm，不要乱改单位，一般都要以mm为单位，咱们是做机器人的，要专业一些。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1490.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1491.webp)

#### joint标签
##### 简介
urdf 中的 joint 标签用于描述机器人关节的运动学和动力学属性，还可以指定关节运动的安全极限，机器人的两个部件(分别称之为 parent link 与 child link)以”关节“的形式相连接，不同的关节有不同的运动形式: 旋转、滑动、固定、旋转速度、旋转角度限制....,比如:安装在底座上的轮子可以360度旋转，而摄像头则可能是完全固定在底座上。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1492.webp)

*   **name** （必填）：为关节命名，名称需要唯一。

*   **type** （必填）：设置关节类型，可用类型如下：

    *   continuous：旋转关节，可以绕单轴无限旋转。

    *   revolute：旋转关节，类似于 continues，但是有旋转角度限制。

    *   prismatic：滑动关节，沿某一轴线移动的关节，有位置极限。

    *   planer：平面关节，允许在平面正交方向上平移或旋转。

    *   floating：浮动关节，允许进行平移、旋转运动。

    *   fixed：固定关节，不允许运动的特殊关节。

以下是子级标签

*   **<parent>** （必填）：指定父级link。

    *   **link** （必填）：父级link的名字，是这个link在机器人结构树中的名字。

*   **<child>** （必填）：指定子级link。

    *   **link** （必填）：子级link的名字，是这个link在机器人结构树中的名字。

*   **<origin>** （可选）：这是从父link到子link的转换，关节位于子link的原点。

    *   **xyz** ：各轴线上的偏移量。

    *   **rpy** ：各轴线上的偏移弧度。

*   **<axis\>** （可选）：如不设置，默认值为（1，0，0）。

    *   **xyz** ：用于设置围绕哪个关节轴运动。

*   **<calibration>** （可选）：关节的参考位置，用于校准关节的绝对位置。

    *   **rising** （可选）：当关节向正方向移动时，该参考位置将触发上升沿。

    *   **falling** （可选）：当关节向正方向移动时，该参考位置将触发下降沿。

*   **<dynamics>** （可选）：指定接头物理特性的元素。这些值用于指定关节的建模属性，对仿真较为有用。

    *   **damping** （可选）：关节的物理阻尼值，默认为0。

    *   **friction** （可选）：关节的物理静摩擦值，默认为0。

*   **<limit>** （关节类型是revolute或prismatic时为必须的）：

    *   **lower** （可选）：指定关节下限的属性（旋转关节以弧度为单位，棱柱关节以米为单位）。如果关节是连续的，则省略。

    *   **upper** （可选）：指定关节上限的属性（旋转关节以弧度为单位，棱柱关节以米为单位）。如果关节是连续的，则省略。

    *   **effort** （必填）：指定关节可受力的最大值。

    *   **velocity** （必填）：用于设置最大关节速度（旋转关节以弧度每秒 \[rad/s\] 为单位，棱柱关节以米每秒 \[m/s\] 为单位）。

*   **<mimic>** （可选）：此标签用于指定定义的关节模仿另一个现有关节。该关节的值可以计算为*value = multiplier \* other_joint_value + offset*。

    *   **joint** （必填）：指定要模拟的关节的名称。

    *   **multiplier** （可选）：指定上述公式中的乘法因子。

    *   **offset** （可选）：指定要在上述公式中添加的偏移量，默认为 0（旋转关节的单位是弧度，棱柱关节的单位是米）。

*   **<safety_controller>** （可选）：安全控制器。

    *   **soft_lower_limit** （可选）：指定安全控制器开始限制关节位置的下关节边界，此限制需要大于joint下限。

    *   **soft_upper_limit** （可选）：指定安全控制器开始限制关节位置的关节上边界的属性，此限制需要小于joint上限。

    *   **k_position** （可选）：指定位置和速度限制之间的关系。

    *   **k_velocity** （必填）：指定力和速度限制之间的关系。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1493.webp)

关节名称是必填的，且是唯一的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1494.webp)

咱们最常用的是有限位的revolute类型的关节，continuous可无限旋转的关节，fixed固定关节，这个根据具体的关节类型来填。

revolute一般用于工业机器人机械臂的关节，continuous比如舵轮结构的“关节”，fixed就是一些固定的不能运动的结构关节。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1495.webp)

子集标签很多，咱们用的最常用的就几个，记住常用的即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1496.webp)

parent标签，link属性指定父级link的名字。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1497.webp)

child标签类似。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1498.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1499.webp)

这个轴，默认是1，0，0，以X轴进行旋转，但是咱们一般是需要设置的，可通过SolidWorks设置基准轴进行设置。

剩下的标签，都与关节类型有关，比如limit，如果关节类型是revolute，而且不设置limit，那么在joint_state_publisher_gui里是无法调关节的角度的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1500.webp)

其他标签用到的时候再进行介绍。

##### 练习
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1501.webp)

先把俩关节，单独实现，然后再通过joint关节进行连接。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1502.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1503.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1504.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1505.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1506.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1507.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1508.webp)

黄色的话，红和绿要多一些。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1509.webp)

这是红色。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1510.webp)

底盘Link就创建完毕了，

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1511.webp)

摄像头Link也创建完毕了，

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1512.webp)

关节名字设置为camera2base_link，也就是摄像头连接底座的joint，然后类型是360度都可以转的continuous。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1513.webp)

填入子级Link与父级Link

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1514.webp)

这样两个Link就通过该Joint连接到一起了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1515.webp)

还需要设置这俩选项，咱们先不设置，先看默认是什么状态。

```bash
colcon build --packages-select cpp06_urdf
source install/setup.bash
ros2 launch cpp06_urdf display.launch.py model:=`ros2 pkg prefix --share cpp06_urdf`/urdf/urdf/demo03_joint.urdf
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1516.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1517.webp)

可以打开TF看看坐标系，勾上Show Names，发现是重合的。所以显示效果不满足咱们的逻辑业务。（默认状态下），所以需要设置偏移量。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1518.webp)

如果咱们想把摄像头移动到车头，如图所示，在X上有偏移量，Y没有，但是Z有。然后roll，pitch，yaw上没有。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1519.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1520.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1521.webp)

Z的高度就是1/2的底盘高度+1/2的摄像头高度

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1522.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1523.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1524.webp)

咱们要绕Yaw旋转，所以也就是绕Z轴旋转，也就是001，注意一定得是整形，不能是浮点型。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1525.webp)

想要看是否能旋转，要打开joint_state_publisher_gui，可以从launch中打开，也可以从终端中直接打开。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1526.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1527.webp)

```bash
ros2 run joint_state_publisher_gui joint_state_publisher_gui
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1528.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1529.webp)

拖拽滚动条可改变摄像头的Yaw

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1530.webp)

Randomize是随机数，Center是回中(复位)

```xml

 <robot name="joint_demo">

  <material name="yellow">
    <color rgba="0.7 0.7 0 0.8" />
  </material>
  <material name="red">
    <color rgba="0.8 0.1 0.1 0.8" />
  </material>
  <link name="base_link">
    <visual>

        <geometry>
            <box size="0.5 0.3 0.1" />
        </geometry>
        <origin xyz="0 0 0" rpy="0 0 0" />
        <material name="yellow"/>
    </visual>
  </link>

  <link name="camera">
      <visual>
          <geometry>
              <box size="0.02 0.05 0.05" />
          </geometry>
          <origin xyz="0 0 0" rpy="0 0 0" />
          <material name="red" />
      </visual>
  </link>

  <joint name="camera2baselink" type="continuous">
      <parent link="base_link"/>
      <child link="camera" />

      <origin xyz="0.2 0 0.075" rpy="0 0 0" />
      <axis xyz="0 0 1" />
  </joint>

</robot>
```

##### joint_state_publisher
bug:Yaw不稳定，会一直回中。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1531.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1532.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1533.webp)

其实是因为launch里启动的和终端里启动的冲突了。

解决方案：只启动其中一个。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1534.webp)

解决方案：直接在Launch里启动GUI版本的，这样即可解决。(但是不建议)

建议方案：用非GUI版本的，因为以后，我们控制关节是用程序控制，而不是GUI控制。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1535.webp)

如果只是想展示模型，用GUI，

如果想用程序控制，用普通版。

##### base_footprint
bug:机器人底盘半沉入地下

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1536.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1537.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1538.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1539.webp)

```xml
  <link name="base_footprint">
    <visual>
      <geometry>
          <sphere radius="0.001"/>
      </geometry>
    </visual>
  </link>
```
```xml
    <joint name="baselink2basefootprint" type="fixed">
      <parent link="base_footprint"/>
      <child link="base_link"/>
      <origin xyz="0.0 0.0 0.05"/>
    </joint>
```

Z的偏移量要填下沉底盘的距离，也就是整车底盘的一半。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1540.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1541.webp)

修改参考坐标系

其实这个优化，可以不做，影响不大。但是建议用有basefootprint版本的。

```xml

 <robot name="base_footprint_demo">

  <material name="yellow">
    <color rgba="0.7 0.7 0 0.8" />
  </material>
  <material name="red">
    <color rgba="0.8 0.1 0.1 0.8" />
  </material>

  <link name="base_footprint">
    <visual>
      <geometry>
          <sphere radius="0.001"/>
      </geometry>
    </visual>
  </link>

  <link name="base_link">
    <visual>

        <geometry>
            <box size="0.5 0.3 0.1" />
        </geometry>
        <origin xyz="0 0 0" rpy="0 0 0" />
        <material name="yellow"/>
    </visual>
  </link>

  <joint name="baselink2basefootprint" type="fixed">
    <parent link="base_footprint"/>
    <child link="base_link"/>
    <origin xyz="0.0 0.0 0.05"/>
  </joint>

  <link name="camera">
      <visual>
          <geometry>
              <box size="0.02 0.05 0.05" />
          </geometry>
          <origin xyz="0 0 0" rpy="0 0 0" />
          <material name="red" />
      </visual>
  </link>

  <joint name="camera2baselink" type="fixed">
      <parent link="base_link"/>
      <child link="camera" />

      <origin xyz="0.2 0 0.075" rpy="0 0 0" />
      <axis xyz="0 0 1" />
  </joint>

</robot>
```

#### 练习
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1542.webp)

没必要太深入练习，咱们可以直接用SolidWorks建模，更为友好。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1543.webp)

```xml

 <robot name="exercise_demo">

  <material name="yellow">
    <color rgba="0.7 0.7 0 0.8" />
  </material>
  <material name="red">
    <color rgba="0.8 0.1 0.1 0.8" />
  </material>
  <material name="gray">
    <color rgba="0.2 0.2 0.2 0.8" />
  </material>

  <link name="base_footprint">
    <visual>
      <geometry>
          <sphere radius="0.001"/>
      </geometry>
    </visual>
  </link>

  <link name="base_link">
    <visual>

        <geometry>
            <box size="0.2 0.12 0.07" />
        </geometry>
        <origin xyz="0 0 0" rpy="0 0 0" />
        <material name="yellow"/>
    </visual>
  </link>

  <joint name="baselink2basefootprint" type="fixed">
    <parent link="base_footprint"/>
    <child link="base_link"/>
    <origin xyz="0.0 0.0 0.05"/>
  </joint>

  <link name="front_left_wheel">
      <visual>
          <geometry>
              <cylinder radius="0.025" length="0.02"/>
          </geometry>
          <origin xyz="0 0 0" rpy="1.57 0 0" />
          <material name="gray" />
      </visual>
  </link>

  <joint name="frontleftwheel2baselink" type="continuous">
      <parent link="base_link"/>
      <child link="front_left_wheel" />

      <origin xyz="0.075 0.06 -0.025" rpy="0 0 0" />
      <axis xyz="0 1 0" />
  </joint>

  <link name="front_right_wheel">
    <visual>
        <geometry>
            <cylinder radius="0.025" length="0.02"/>
        </geometry>
        <origin xyz="0 0 0" rpy="1.57 0 0" />
        <material name="gray" />
    </visual>
  </link>

  <joint name="frontrightwheel2baselink" type="continuous">
      <parent link="base_link"/>
      <child link="front_right_wheel" />

      <origin xyz="0.075 -0.06 -0.025" rpy="0 0 0" />
      <axis xyz="0 1 0" />
  </joint>

  <link name="back_left_wheel">
    <visual>
        <geometry>
            <cylinder radius="0.025" length="0.02"/>
        </geometry>
        <origin xyz="0 0 0" rpy="1.57 0 0" />
        <material name="gray" />
    </visual>
  </link>

  <joint name="backleftwheel2baselink" type="continuous">
    <parent link="base_link"/>
    <child link="back_left_wheel" />

    <origin xyz="-0.075 0.06 -0.025" rpy="0 0 0" />
    <axis xyz="0 1 0" />
  </joint>

  <link name="back_right_wheel">
    <visual>
        <geometry>
            <cylinder radius="0.025" length="0.02"/>
        </geometry>
        <origin xyz="0 0 0" rpy="1.57 0 0" />
        <material name="gray" />
    </visual>
  </link>

  <joint name="backrightwheel2baselink" type="continuous">
    <parent link="base_link"/>
    <child link="back_right_wheel" />

    <origin xyz="-0.075 -0.06 -0.025" rpy="0 0 0" />
    <axis xyz="0 1 0" />
  </joint>

</robot>
```

终端运行

```bash
ros2 launch cpp06_urdf display.launch.py model:=`ros2 pkg prefix --share cpp06_urdf`/urdf/urdf/demo05_exercise.urdf
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1544.webp)

### URDF工具
在 ROS2 中，提供了一些URDF文件相关的工具，比如:

*   `check_urdf`命令可以检查复杂的 urdf 文件是否存在语法问题；

*   `urdf_to_graphviz`命令可以查看 urdf 模型结构，显示不同 link 的层级关系。

当然，要使用工具之前，请先安装，安装命令：`sudo apt install liburdfdom-tools`。

#### **check_urdf 语法检查**
进入urdf文件所属目录，调用：`check_urdf urdf文件`，如果不抛出异常，说明文件合法，否则非法。

示例，终端下进入功能包 cpp06_urdf 的 urdf/urdf 目录，执行如下命令：

```bash
check_urdf demo05_exercise.urdf
```

urdf 文件如无异常，将显示urdf中link的层级关系，如下图所示：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1545.webp)

否则将会给出错误提示。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1546.webp)

演示错误，

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1547.webp)

#### **urdf_to_graphviz 结构查看**
进入urdf文件所属目录，调用:`urdf_to_graphviz urdf文件`，当前目录下会生成 pdf 文件。

示例，终端下进入功能包 cpp06_urdf 的 urdf/urdf 目录，执行如下命令：

```bash
urdf_to_graphviz demo05_exercise.urdf
```

当前目录下，将生成以urdf中robot名称命名的.pdf和.gv文件，打开pdf文件会显示如下图内容：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1548.webp)

在上图中会以树形结构显示link与joint的关系。

**注意：** 该工具以前名为`urdf_to_graphiz`现建议使用`urdf_to_graphviz`替代。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1549.webp)

`urdf_to_graphiz`是历史版本，已经被废弃，建议用`urdf_to_graphviz`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1550.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1551.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1552.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1553.webp)

黑色方框代表Link，蓝色代表Joint，也会展示平移量和旋转度等信息。

### SW2URDF
#### solidworks简介
SolidWorks是一种计算机辅助设计（CAD）和计算机辅助制造（CAM）软件，由Dassault Systèmes SolidWorks Corp.开发。它主要用于工程设计和制造，可用于创建3D三维模型、进行装配设计、进行工程分析和绘图等。SolidWorks具有直观的用户界面和强大的功能，使工程师和设计师能够快速而精确地设计复杂的零部件和装配体。该软件广泛应用于机械、航空航天、汽车、医疗设备等行业，是工程设计领域的重要工具之一。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1554.webp)

#### solidworks插件sw2urdf介绍
sw2urdf插件维基百科：

https://wiki.ros.org/sw_urdf_exporter

github下载链接：

https://github.com/ros/solidworks_urdf_exporter/releases

虽然github链接上写着只支持到SW2021，但是目前发现最新版SW(2022、2024经测试)也是可以正常用的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1555.webp)

#### 安装sw2urdf
1.  去github上下载sw2urdf插件（这个SW版本不用管，实测在后续的SW版本依然可用）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1556.webp)

2.  查看SW的安装路径

比如我的路径"C:\\Program Files\\SOLIDWORKS Corp\\SOLIDWORKS\\"

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1557.webp)

3.  打开插件安装器，默认情况下会自动找到你的SW路径进行安装，如果没有自动找到路径安装，那么需要手动选取SW安装路径。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1558.webp)

4.  查看插件是否安装成功

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1559.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1560.webp)

如图这样就是安装成功了。

#### 导出URDF与Meshes
1.  如图是一个标准的SW装配体图(大家也可以尝试自己手撸一个，里面没有传动装置也可以)

这个过程看不太懂的话，可以参考一下古月居老师的视频，结合本教程一起学习。 【SolidWorks模型导出urdf（古月居老师）-哔哩哔哩】https://www.bilibili.com/video/BV1Tx411o7rH

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1561.webp)

2.  对joint关节建立基准轴

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1562.webp)

选择关节的圆柱面或者其他面，对转轴进行标定

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1563.webp)

如果建完基准轴，发现不能够正常展示，那么就打开该选项。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1564.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1565.webp)

建完所有系后

3.  打开export as URDF选项

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1566.webp)

4.  需要先选择base_link基底刚体

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1567.webp)

这里的Global Origin可能需要自己选择，就选择Origin_Global即可，但是一般选择automatically Generate即可。

5.  因为他的base_link通过一个joint连接了一个child_link，所以，这个选项要填1。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1568.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1569.webp)

咱们把base_link连接的关节叫joint1，把joint1连接的更上面的刚体叫做link1,

然后参考基准轴选择刚才咱们在这个关节处建立的基准轴1，然后joint type关节类型选择revolute（有限位的关节，只有这样，机器人关节才可以正常运动）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1570.webp)

这里的参考坐标系系统可以选择automatically Generate，也可以选择Origin_Joint1。

然后再在link1上加一，创建link2。

后面的link3，link4添加步骤是一样的，不再详细展示。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1571.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1572.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1573.webp)

6.  点击preview and export

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1574.webp)

7.  对关节进行限位设置

coordinates是坐标系，axis是轴，如果这里Axis坐标显示错误，那么说明Coordinates或者Axis选择错了，请手动选择正确的坐标系或者基准轴。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1575.webp)

如果转轴生成错误，比如都是000，可以自己填一下。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1576.webp)

比如这里是X轴，可以把左轮全填-100,右轮100（这里，仅供参考，chatgpt提供的解决方案）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1577.webp)

8.  查看矩阵，并点导出urdf与meshes

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1578.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1579.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1580.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1581.webp)

这样就成功生成ROS1的WS了，将WS移动至Linux系统。

#### 将ROS1的WS转化为ROS2的WS
1.  先将ROS1的WS移动到Linux系统上

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1582.webp)

2.  新建一个ROS2的WS

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1583.webp)

```bash
mkdir -p ros2_4axisrobot_ws/src
cd ros2_4axisrobot_ws/
colcon build
cd src
ros2 pkg create cpp01_urdf --build-type ament_cmake
cd ..
code .
```

3.  完善WS的目录

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1584.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1585.webp)

在./src/cpp01_urdf路径下创建launch、meshes、rviz、urdf等等文件夹，在urdf文件夹下再新建urdf和xacro文件夹

4.  复制ROS1的WS里的URDF和Meshes到ROS2的WS中

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1586.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1587.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1588.webp)

5.  对CMake和package进行配置

在CMake中请添加

```cmake
install(
  DIRECTORY launch urdf rviz meshes
  DESTINATION share/${PROJECT_NAME}  
)
```

在Package.xml中请添加

```xml
  <exec_depend>rviz2</exec_depend>
  <exec_depend>xacro</exec_depend>
  <exec_depend>robot_state_publisher</exec_depend>
  <exec_depend>joint_state_publisher</exec_depend>
  <exec_depend>ros2launch</exec_depend>
```

CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.8)
project(cpp01_urdf)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)

# uncomment the following section in order to fill in

# further dependencies manually.

# find_package(<dependency> REQUIRED)

install(
  DIRECTORY launch urdf rviz meshes
  DESTINATION share/${PROJECT_NAME}  
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)

  # the following line skips the linter which checks for copyrights

  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)

  # the following line skips cpplint (only works in a git repo)

  # comment the line when this package is in a git repo and when

  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()

```

package.xml

```xml
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>cpp01_urdf</name>
  <version>0.0.0</version>
  <description>TODO: Package description</description>
  <maintainer email="tungchiahui@gmail.com">tungchiahui</maintainer>
  <license>TODO: License declaration</license>

  <buildtool_depend>ament_cmake</buildtool_depend>

  <exec_depend>rviz2</exec_depend>
  <exec_depend>xacro</exec_depend>
  <exec_depend>robot_state_publisher</exec_depend>
  <exec_depend>joint_state_publisher</exec_depend>
  <exec_depend>ros2launch</exec_depend>

  <test_depend>ament_lint_auto</test_depend>
  <test_depend>ament_lint_common</test_depend>

  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>

```

6.  对launch文件进行编写

详细过程请看URDF有关Launch的核心优化那一节

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1589.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node

# 封装终端指令相关类

# from launch.actions import ExecuteProcess

# from launch.substitutions import FindExecutable

# 参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration

# 文件包含相关

# from launch.actions import IncludeLaunchDescription

# from launch.launch_description_sources import PythonLaunchDescriptionSource

# 分组相关

# from launch_ros.actions import PushRosNamespace

# from launch.actions import GroupAction

# 事件相关

# from launch.event_handlers import OnProcessStart,OnProcessExit

# from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo

# 获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

from launch_ros.parameter_descriptions import ParameterValue
from launch.substitutions import Command

import os

cpp01_urdf_dir = get_package_share_directory("cpp01_urdf")

default_model_path = os.path.join(cpp01_urdf_dir,"urdf/urdf","4axisrobot.urdf")
default_rviz_path = os.path.join(cpp01_urdf_dir,"rviz","urdf.rviz")

model = DeclareLaunchArgument(name="model",default_value=default_model_path)

p_value = ParameterValue(Command(["xacro ",LaunchConfiguration("model")]))
robot_state_pub = Node(
    package="robot_state_publisher",
    executable="robot_state_publisher",
    parameters=[{"robot_description":p_value}]
)

# 关节信息节点(建议)

# joint_state_pub = Node(

#     package="joint_state_publisher",

#     executable="joint_state_publisher"

# )

# 关节信息节点图形界面
joint_state_pub = Node(
    package="joint_state_publisher_gui",
    executable="joint_state_publisher_gui"
)

rviz2 = Node(
    package="rviz2",
    executable="rviz2",

#    arguments=["-d",get_package_share_directory("cpp06_urdf") + "/rviz/urdf.rviz"]
    arguments=["-d",default_rviz_path]

    )

def generate_launch_description():
    return LaunchDescription([model,robot_state_pub,joint_state_pub,rviz2])
```

7.  对URDF文件进行修改

修改的内容主要有两项，一个是meshes的路径，一个是删掉易引起报错的注释。

主要由于ROS1的WS和ROS2的WS路径不同，所以，我们只需要修改有关路径的内容，比如说Meshes的路径

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1590.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1591.webp)

package://一般是share目录，所以我们需要从cpp01_urdf这一级开始写（share目录需要编译后才会显示）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1592.webp)

然后按Ctrl+H

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1593.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1594.webp)

然后还需要删除掉urdf文件刚开始的注释，否则也会报错

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1595.webp)

如下是替换完毕的URDF文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<robot
  name="4axisrobot">
  <link
    name="base_link">
    <inertial>
      <origin
        xyz="-0.0819771145953119 -0.0394328123681598 -0.0605612328464761"
        rpy="0 0 0" />
      <mass
        value="7.43807257011133" />
      <inertia
        ixx="0.33173827700719"
        ixy="-1.91350798633049E-05"
        ixz="-2.38586363043925E-05"
        iyy="0.510937907009661"
        iyz="1.14706407748075E-05"
        izz="0.66107272632796" />
    </inertial>
    <visual>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://cpp01_urdf/meshes/base_link.STL" />
      </geometry>
      <material
        name="">
        <color
          rgba="0.792156862745098 0.819607843137255 0.933333333333333 1" />
      </material>
    </visual>
    <collision>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://cpp01_urdf/meshes/base_link.STL" />
      </geometry>
    </collision>
  </link>
  <link
    name="link1">
    <inertial>
      <origin
        xyz="0.19141 -0.050676 -0.030057"
        rpy="0 0 0" />
      <mass
        value="21.822" />
      <inertia
        ixx="0.50464"
        ixy="0.13295"
        ixz="0.087353"
        iyy="0.59136"
        iyz="-0.066053"
        izz="0.64081" />
    </inertial>
    <visual>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://cpp01_urdf/meshes/link1.STL" />
      </geometry>
      <material
        name="">
        <color
          rgba="0.79216 0.81961 0.93333 1" />
      </material>
    </visual>
    <collision>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://cpp01_urdf/meshes/link1.STL" />
      </geometry>
    </collision>
  </link>
  <joint
    name="joint1"
    type="revolute">
    <origin
      xyz="-0.081947 -0.039447 0.11191"
      rpy="0.40394 -1.5708 0" />
    <parent
      link="base_link" />
    <child
      link="link1" />
    <axis
      xyz="1 0 0" />
    <limit
      lower="-3.14"
      upper="3.14"
      effort="100"
      velocity="1" />
  </joint>
  <link
    name="link2">
    <inertial>
      <origin
        xyz="0.00711598521454873 0.430460941882332 -0.181074256126024"
        rpy="0 0 0" />
      <mass
        value="30.6115349647897" />
      <inertia
        ixx="2.84056101478768"
        ixy="0.00176700425270318"
        ixz="-0.000605507690039326"
        iyy="0.803420593303961"
        iyz="0.881921487314161"
        izz="2.25142021571439" />
    </inertial>
    <visual>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://cpp01_urdf/meshes/link2.STL" />
      </geometry>
      <material
        name="">
        <color
          rgba="0.792156862745098 0.819607843137255 0.933333333333333 1" />
      </material>
    </visual>
    <collision>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://cpp01_urdf/meshes/link2.STL" />
      </geometry>
    </collision>
  </link>
  <joint
    name="joint2"
    type="revolute">
    <origin
      xyz="0.35141 -0.28119 0.13918"
      rpy="-2.0566 1.1982 1.5708" />
    <parent
      link="link1" />
    <child
      link="link2" />
    <axis
      xyz="-1 0 0" />
    <limit
      lower="-3.14"
      upper="3.14"
      effort="100"
      velocity="1" />
  </joint>
  <link
    name="link3">
    <inertial>
      <origin
        xyz="0.00112887115421612 0.0366346915274407 0.31720330934553"
        rpy="0 0 0" />
      <mass
        value="32.192726667578" />
      <inertia
        ixx="4.01515983126431"
        ixy="0.0013193752174692"
        ixz="0.0113529938102155"
        iyy="3.86196311673823"
        iyz="-0.336440022491883"
        izz="0.399977905540088" />
    </inertial>
    <visual>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://cpp01_urdf/meshes/link3.STL" />
      </geometry>
      <material
        name="">
        <color
          rgba="0.792156862745098 0.819607843137255 0.933333333333333 1" />
      </material>
    </visual>
    <collision>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://cpp01_urdf/meshes/link3.STL" />
      </geometry>
    </collision>
  </link>
  <joint
    name="joint3"
    type="revolute">
    <origin
      xyz="0.3765 0.82481 -0.49591"
      rpy="2.5802 0 0" />
    <parent
      link="link2" />
    <child
      link="link3" />
    <axis
      xyz="1 0 0" />
    <limit
      lower="-3.14"
      upper="3.14"
      effort="100"
      velocity="1" />
  </joint>
  <link
    name="link4">
    <inertial>
      <origin
        xyz="0.162516620557178 -0.213844847423599 3.38133504529381E-07"
        rpy="0 0 0" />
      <mass
        value="12.1646007734167" />
      <inertia
        ixx="0.288208127188405"
        ixy="0.00080326659836716"
        ixz="8.72489392803044E-07"
        iyy="0.107000706575729"
        iyz="-1.48935044230573E-07"
        izz="0.295147863994059" />
    </inertial>
    <visual>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://cpp01_urdf/meshes/link4.STL" />
      </geometry>
      <material
        name="">
        <color
          rgba="0.792156862745098 0.819607843137255 0.933333333333333 1" />
      </material>
    </visual>
    <collision>
      <origin
        xyz="0 0 0"
        rpy="0 0 0" />
      <geometry>
        <mesh
          filename="package://cpp01_urdf/meshes/link4.STL" />
      </geometry>
    </collision>
  </link>
  <joint
    name="joint4"
    type="revolute">
    <origin
      xyz="0 0.070837 0.99728"
      rpy="0.82315 -1.5708 0" />
    <parent
      link="link3" />
    <child
      link="link4" />
    <axis
      xyz="1 0 0" />
    <limit
      lower="-3.14"
      upper="3.14"
      effort="100"
      velocity="1" />
  </joint>
</robot>
```

8.  编译

```bash
colcon build --packages-select cpp01_urdf
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1596.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1597.webp)

9.  更新终端环境

```bash
source install/setup.bash
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1598.webp)

10.  运行Launch

```bash
ros2 launch cpp01_urdf display.launch.py
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1599.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1600.webp)

11.  在Rviz2中添加插件以及基本配置

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1601.webp)

12.  使用joint_state_publisher_gui可以调关节的角度

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1602.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1603.webp)

13.  保存rviz2的配置到rviz文件夹中

首先点击rviz2的菜单栏上的File，然后选择Save Config as

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1604.webp)

可以去本节简介看效果视频

#### 注意事项（仅供参考，ChatGPT的解决方案）
##### 坐标系1
轮子要用continuous的关节，并且尽量自己选坐标系，要求从后面看车的话，左是Y，前是X，上是Z。所有的坐标系都要求。

可以先让他帮你自动生成一个，你再去修改坐标系，要简单一些。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1605.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1606.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1607.webp)

##### 坐标系让车躺着
如果你的车在rviz2里是侧着睡觉的，那么：

哈哈，没错，这种“整车侧着躺尸”的情况 **90%就是 STL 的坐标轴方向搞错了** 。很多 CAD 工具导出的 STL 模型默认是：

*   Z 轴朝前（例如 SolidWorks：Z朝前，Y朝上，用右手坐标系推X坐标系位置，从车屁股后面看是朝左。）

*   或者 Y 轴朝上（例如 Blender）

而 ROS/URDF 的坐标系统是：

*   X 向前（前进方向）

*   Y 向左（平移方向）

*   Z 向上（重力方向）

##### 车直走变旋转，旋转变直走
那说明你的转轴有问题。

如果向前走成了左转，那么说明左轮的转轴错了，少了个符号，加上就行了。

比如这个010,你应该改成0-10

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1608.webp)

### xacro
#### 场景、作用与概念
**场景**

前面 URDF 文件构建机器人模型的过程中，存在若干问题。

> 问题1：在设计关节的位置时，需要按照一定的规则计算，规则是固定的，但是在 URDF 中依赖于人工计算，存在不便，容易计算失误，且当某些参数发生改变时，还需要重新计算。
> 
> 问题2：URDF中的部分内容是高度重复的，比如车轮的设计实现，不同轮子只是部分参数不同，形状、颜色、翻转量都是一致的，在实际应用中，构建复杂的机器人模型时，更是易于出现高度重复的设计，按照一般的编程思想涉及到重复代码应该考虑封装、复用，但是在之前的URDF文件中并没有相关操作。
> 
> ......

如果在一般编程语言中遇到类似问题，我们可以通过变量结合函数解决。对应的，在 ROS 中也给出了类似编程的优化方案，该方案称之为： **Xacro（可以理解为urdf2.0）** 。

**概念**

Xacro 是 XML Macros 的缩写，Xacro 是一种 XML 宏语言，是可编程的 XML。

Xacro 可以声明变量，可以通过数学运算求解；可以使用流程控制控制执行顺序；还可以通过宏封装(可以想成函数)、复用功能，从而提高代码复用率以及程序的安全性。

**作用**

较之于纯粹的 URDF 实现，可以编写更安全、精简、易读性更强的机器人模型文件，且可以提高编写效率。

#### 快速体验
先安装xacro

```bash
#humble版本
sudo apt install ros-humble-xacro
#jazzy版本
sudo apt install ros-jazzy-xacro
```

1.需求

使用xacro优化 **6.4.4 URDF练习** 中的小车底盘实现，需要使用变量封装车辆参数，并使用 xacro 宏封装轮子重复的代码并调用宏创建四个轮子(注意: 在此，演示 xacro 的基本使用，不必要生成合法的 URDF )。

2.实现

功能包cpp06_urdf的urdf/xacro目录下，新建xacro文件demo01_helloworld.urdf.xacro，并编辑文件，输入如下内容：

```xml
<robot name="mycar" xmlns:xacro="http://wiki.ros.org/xacro">

    <xacro:property name="wheel_radius" value="0.025" />
    <xacro:property name="wheel_length" value="0.02" />
    <xacro:property name="PI" value="3.1415927" />

    <xacro:macro name="wheel_func" params="wheel_name" >
        <link name="${wheel_name}_wheel">
            <visual>
                <geometry>
                    <cylinder radius="${wheel_radius}" length="${wheel_length}" />
                </geometry>

                <origin xyz="0 0 0" rpy="${PI / 2} 0 0" />

                <material name="wheel_color">
                    <color rgba="0 0 0 0.3" />
                </material>
            </visual>
        </link>
    </xacro:macro>
    <xacro:wheel_func wheel_name="left_front"/>
    <xacro:wheel_func wheel_name="left_back"/>
    <xacro:wheel_func wheel_name="right_front"/>
    <xacro:wheel_func wheel_name="right_back"/>
</robot>
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1609.webp)

宏类似函数

params类似入口参数

子标签类似函数体

终端下进入当前文件所述目录，输入如下指令：

```bash
cd src/cpp06_urdf/urdf/xacro/
xacro demo01_helloworld.urdf.xacro
#或者
ros2 run xacro xacro demo01_helloworld.urdf.xacro
```

终端将会输出如下内容（以下内容是纯urdf）：

```xml
<?xml version="1.0" ?>

<robot name="mycar">
  <link name="left_front_wheel">
    <visual>
      <geometry>
        <cylinder length="0.02" radius="0.025"/>
      </geometry>
      <origin rpy="1.57079635 0 0" xyz="0 0 0"/>
      <material name="wheel_color">
        <color rgba="0 0 0 0.3"/>
      </material>
    </visual>
  </link>
  <link name="left_back_wheel">
    <visual>
      <geometry>
        <cylinder length="0.02" radius="0.025"/>
      </geometry>
      <origin rpy="1.57079635 0 0" xyz="0 0 0"/>
      <material name="wheel_color">
        <color rgba="0 0 0 0.3"/>
      </material>
    </visual>
  </link>
  <link name="right_front_wheel">
    <visual>
      <geometry>
        <cylinder length="0.02" radius="0.025"/>
      </geometry>
      <origin rpy="1.57079635 0 0" xyz="0 0 0"/>
      <material name="wheel_color">
        <color rgba="0 0 0 0.3"/>
      </material>
    </visual>
  </link>
  <link name="right_back_wheel">
    <visual>
      <geometry>
        <cylinder length="0.02" radius="0.025"/>
      </geometry>
      <origin rpy="1.57079635 0 0" xyz="0 0 0"/>
      <material name="wheel_color">
        <color rgba="0 0 0 0.3"/>
      </material>
    </visual>
  </link>
</robot>
```

显然的，通过xacro我们方便的实现了代码复用。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1610.webp)

##### 语法
    1.  简介

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1611.webp)

      xacro 提供了可编程接口，类似于计算机语言，包括变量声明调用、函数声明与调用等语法实现。在使用 xacro 生成 urdf 时，根标签`robot`中**必须**包含命名空间声明:`xmlns:xacro="``http://wiki.ros.org/xacro``"`。

       **变量**

      变量用于封装 URDF 中的一些字段，比如: PAI 值，小车的尺寸，轮子半径 ....，变量的基本使用语法包括变量定义、变量调用、变量运算等。

      1.1变量定义

      语法格式：

    ```xml
    <xacro:property name="变量名" value="变量值" />
    ```

      示例：

    ```xml
    <xacro:property name="PI" value="3.1416"/>
    <xacro:property name="wheel_radius" value="0.025"/>
    <xacro:property name="wheel_length" value="0.02"/>
    ```

      1.2变量调用

      语法格式：

    ```xml
    ${变量名}
    ```

      示例：

    ```xml
    <geometry>
        <cylinder radius="${wheel_radius}" length="${wheel_length}" />
    </geometry>
    ```

      1.3变量运算

      语法格式：

    ```xml
    ${数学表达式}
    ```

      示例：

    ```xml
    <origin xyz="0 0 0" rpy="${PI / 2} 0 0" />
    ```

    ```xml
    <robot xmlns:xacro="http://www.ros.org/wiki/xacro" name="demo2_pro">

    <xacro:property name="num1" value="10"/>
    <xacro:property name="num2" value="20"/>

    <car length="${num1}" width="${num2}"/>

    <sum value="${num1 + num2}"/>
    </robot>
    ```

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1612.webp)

       **宏**

      类似于函数实现，提高代码复用率，优化代码结构，提高安全性。宏的基本使用语法包括宏的定义与调用。

      2.1宏定义

      语法格式：

    ```xml
    <xacro:macro name="宏名称" params="参数列表(多参数之间使用空格分隔)">
        .....
        参数调用格式: ${参数名}
    </xacro:macro>
    ```

      示例：

    ```xml
    <xacro:macro name="wheel_func" params="wheel_name" >
        <link name="${wheel_name}_wheel">
            <visual>
                <geometry>
                    <cylinder radius="${wheel_radius}" length="${wheel_length}" />
                </geometry>

                <origin xyz="0 0 0" rpy="${PI / 2} 0 0" />

                <material name="wheel_color">
                    <color rgba="0 0 0 0.3" />
                </material>
            </visual>
        </link>
    </xacro:macro>
    ```

      2.2宏调用

      语法格式：

    ```xml
    <xacro:宏名称 参数1=xxx 参数2=xxx/>
    ```

      示例：

    ```xml
    <xacro:wheel_func wheel_name="left_front"/>
    <xacro:wheel_func wheel_name="left_back"/>
    <xacro:wheel_func wheel_name="right_front"/>
    <xacro:wheel_func wheel_name="right_back"/>
    ```

    ```xml
    <robot xmlns:xacro="http://www.ros.org/wiki/xacro" name="demo3_func">

        <xacro:macro name="get_sum" params="num1 num2">
            <sum value="${num1 + num2}"/>
        </xacro:macro>

        <xacro:get_sum num1="20" num2="30"/>
        <xacro:get_sum num1="70" num2="30"/>

    </robot>
    ```

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1613.webp)

       **文件**

机器人由多部件组成，不同部件可能封装为单独的 xacro 文件，最后再将不同的文件集成，组合为完整机器人，可以使用文件包含实现。

语法格式：

```xml
<xacro:include filename="其他xacro文件" />
```

示例：

```xml
<robot name="car" xmlns:xacro="http://wiki.ros.org/xacro">
      <xacro:include filename="car_base.xacro" />
      <xacro:include filename="car_camera.xacro" />
      <xacro:include filename="car_laser.xacro" />
</robot>
```

```xml
<robot xmlns:xacro="http://www.ros.org/wiki/xacro" name="demo4_include">
    <xacro:include filename="demo02_base_pro.urdf.xacro"/>
    <xacro:include filename="demo03_base_func.urdf.xacro"/>
</robot>
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1614.webp)

*但不建议这样，建议父级xacro和子级xacro使用同样的name。*

#### 练习
##### 框架
1.需求

使用xacro创建一个四轮机器人模型，该模型底盘可以参考 **6.4.4 URDF练习** 中的实现，并且在底盘之上添加了相机与激光雷达。相机与激光雷达的尺寸参数、安装位置可自定义。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1615.webp)

2.实现分析

需求中的机器人模型是由底盘、摄像头和雷达三部分组成的，那么可以将每一部分都封装进一个xacro文件，最后再通过xacro文件包含组织成一个完整的机器人模型。

3.实现

功能包cpp06_urdf的urdf/xacro目录下，新建多个xacro文件，分别为：

*   car.urdf.xacro：用于包含不同机器人部件对应的xacro文件；

*   car_base.urdf.xacro：描述机器人底盘的xacro文件；

*   car_camera.urdf.xacro：描述摄像头的xacro文件；

*   car_laser.urdf.xacro：描述雷达的xacro文件。

编辑car.urdf.xacro文件，输入如下内容：

```xml
<robot name="car" xmlns:xacro="http://wiki.ros.org/xacro">
    <xacro:include filename="car_base.urdf.xacro"/>
    <xacro:include filename="car_camera.urdf.xacro"/>
    <xacro:include filename="car_laser.urdf.xacro"/>
</robot>
```

##### 车体
编辑car_base.urdf.xacro文件，输入如下内容：

```xml
<robot xmlns:xacro="http://wiki.ros.org/xacro">

    <xacro:property name="PI" value="3.1416"/>

    <xacro:property name="base_link_x" value="0.2"/>
    <xacro:property name="base_link_y" value="0.12"/>
    <xacro:property name="base_link_z" value="0.07"/>

    <xacro:property name="distance" value="0.015"/>

    <xacro:property name="wheel_radius" value="0.025"/>
    <xacro:property name="wheel_length" value="0.02"/>

    <material name="yellow">
        <color rgba="0.7 0.7 0 0.8" />
    </material>
    <material name="red">
        <color rgba="0.8 0.1 0.1 0.8" />
    </material>
    <material name="gray">
        <color rgba="0.2 0.2 0.2 0.95" />
      </material>

    <link name="base_footprint">
        <visual>
            <geometry>
                <sphere radius="0.001"/>
            </geometry>
        </visual>
    </link>

    <link name="base_link">
        <visual>

            <geometry>
                <box size="${base_link_x} ${base_link_y} ${base_link_z}" />
            </geometry>
            <origin xyz="0 0 0" rpy="0 0 0" />
            <material name="yellow"/>
        </visual>
    </link>
    <joint name="baselink2basefootprint" type="fixed">
        <parent link="base_footprint"/>
        <child link="base_link"/>
        <origin xyz="0.0 0.0 ${distance + base_link_z / 2}"/>
    </joint>

    <xacro:macro name="wheel_func" params="wheel_name is_front is_left" >
        <link name="${wheel_name}_wheel">
            <visual>
                <geometry>
                    <cylinder radius="${wheel_radius}" length="${wheel_length}" />
                </geometry>
                <origin xyz="0 0 0" rpy="${PI / 2} 0 0" />
                <material name="gray"/>
            </visual>
        </link>
        <joint name="${wheel_name}2baselink" type="continuous">
            <parent link="base_link"  />
            <child link="${wheel_name}_wheel" />
            <origin xyz="${(base_link_x / 2 - wheel_radius) * is_front} ${base_link_y / 2 * is_left} ${(base_link_z / 2 + distance - wheel_radius) * -1}" rpy="0 0 0" />
            <axis xyz="0 1 0" />
        </joint>
    </xacro:macro>

    <xacro:wheel_func wheel_name="left_front" is_front="1" is_left="1" />
    <xacro:wheel_func wheel_name="left_back" is_front="-1" is_left="1" />
    <xacro:wheel_func wheel_name="right_front" is_front="1" is_left="-1" />
    <xacro:wheel_func wheel_name="right_back" is_front="-1" is_left="-1" />
</robot>
```

##### 添加摄像头
编辑car_camera.urdf.xacro文件，输入如下内容：

```xml

<robot xmlns:xacro="http://wiki.ros.org/xacro">

    <xacro:property name="camera_x" value="0.012" /> 
    <xacro:property name="camera_y" value="0.05" /> 
    <xacro:property name="camera_z" value="0.01" /> 
    <xacro:property name="camera_joint_x" value="${base_link_x / 2 - camera_x / 2}" /> 
    <xacro:property name="camera_joint_y" value="0.0" /> 
    <xacro:property name="camera_joint_z" value="${base_link_z / 2 + camera_z / 2}" /> 

    <link name="camera">
        <visual>
            <geometry>
                <box size="${camera_x} ${camera_y} ${camera_z}" />
            </geometry>
            <origin xyz="0.0 0.0 0.0" rpy="0.0 0.0 0.0" />
            <material name="red" />
        </visual>
    </link>

    <joint name="camera2baselink" type="fixed">
        <parent link="base_link" />
        <child link="camera" />
        <origin xyz="${camera_joint_x} ${camera_joint_y} ${camera_joint_z}" />
    </joint>
</robot>
```

##### 添加雷达
编辑car_laser.urdf.xacro文件，输入如下内容：

```xml

<robot xmlns:xacro="http://wiki.ros.org/xacro">

    <material name="blue">
        <color rgba="0.0 0.0 0.4 0.95" />
    </material>

    <xacro:property name="laser_length" value="0.03" /> 
    <xacro:property name="laser_radius" value="0.03" /> 
    <xacro:property name="laser_joint_x" value="0.0" /> 
    <xacro:property name="laser_joint_y" value="0.0" /> 
    <xacro:property name="laser_joint_z" value="${base_link_z / 2 + laser_length / 2}" /> 

    <link name="laser">
        <visual>
            <geometry>
                <cylinder radius="${laser_radius}" length="${laser_length}" />
            </geometry>
            <origin xyz="0.0 0.0 0.0" rpy="0.0 0.0 0.0" />
            <material name="blue" />
        </visual>
    </link>

    <joint name="laser2baselink" type="fixed">
        <parent link="base_link" />
        <child link="laser" />
        <origin xyz="${laser_joint_x} ${laser_joint_y} ${laser_joint_z}" />
    </joint>
</robot>
```

##### 执行
编译后，工作空间终端下调用如下命令执行：

```bash

# ROS Humble
ros2 launch cpp06_urdf display.launch.py model:=ros2 pkg prefix --share cpp06_urdf/urdf/xacro/car.urdf.xacro
#ROS Jazzy
colcon build
source install/setup.bash
ros2 run xacro xacro $(ros2 pkg prefix cpp06_urdf)/share/cpp06_urdf/urdf/xacro/car.urdf.xacro -o ./src/cpp06_urdf/urdf/urdf/car.urdf
ros2 launch cpp06_urdf display.launch.py model:=./src/cpp06_urdf/urdf/urdf/car.urdf
```

命令执行后，rviz2 中可以显示与需求类似的机器人模型。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1616.webp)

### 小结
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1617.webp)

目前只是空壳，激光雷达和摄像头以及轮子还都是空壳，到进阶联系中，才可以实现作用。
