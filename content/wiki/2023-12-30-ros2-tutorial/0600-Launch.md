---
title: "Launch"
---

### 概述
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1097.webp)

rosbag2的作用是来序列化储存数据的，是一个数据库。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1098.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1099.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1100.webp)

ros2 launch 功能包名 launch文件名

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1101.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1102.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1103.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1104.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1105.webp)

ros2 pkg create cpp01_launch --build-type ament_cmake --dependencies rclcpp

```bash
ros2 pkg create cpp01_launch --build-type ament_cmake --dependencies rclcpp
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1106.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node

封装终端指令相关类
from launch.actions import ExecuteProcess
from launch.substitutions import FindExecutable
参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
文件包含相关
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
分组相关
from launch_ros.actions import PushRosNamespace
from launch.actions import GroupAction
事件相关
from launch.event_handlers import OnProcessStart,OnProcessExit
from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo
获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

def generate_launch_description():
    return LaunchDescription([])
```

### launch基本使用流程(C++)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1107.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1108.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1109.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1110.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1111.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1112.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1113.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1114.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1115.webp)

配置完这个，不管我launch目录下有多少launch文件，只需要配置这一次就行了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1116.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1117.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1118.webp)

这样说明我们cmake配置对了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1119.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1120.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node
封装终端指令相关类
from launch.actions import ExecuteProcess
from launch.substitutions import FindExecutable
参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
文件包含相关
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
分组相关
from launch_ros.actions import PushRosNamespace
from launch.actions import GroupAction
事件相关
from launch.event_handlers import OnProcessStart,OnProcessExit
from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo
获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

def generate_launch_description():
    return LaunchDescription([])
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1121.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1122.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1123.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1124.webp)

```xml
<launch>
    <node pkg = "turtlesim" exec = "turtlesim_node" name = "t1" />
    <node pkg = "turtlesim" exec = "turtlesim_node" name = "t2" />
</launch>
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1125.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1126.webp)

```yaml
launch:
node:
  pkg: "turtlesim"
  exec: "turtlesim_node"
  name: "t1"
node:
  pkg: "turtlesim"
  exec: "turtlesim_node"
  name: "t2"
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1127.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1128.webp)

最好添加一个依赖

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1129.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1130.webp)

### Launch_Python_Node
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1131.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1132.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1133.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1134.webp)

这个是标签exec_name

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1135.webp)

这俩参数传参的区别在于--ros-args的区别

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1136.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1137.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1138.webp)

在launch里写更简单

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1139.webp)

这样是等价的

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1140.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1141.webp)

{}里是yaml格式的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1142.webp)

有另一种更常用的方法，就是上来读取yaml文件，把数据都存在yaml里，用到的时候直接读取。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1143.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1144.webp)

把yaml文件放到config里

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1145.webp)

```bash
ros2 param dump haha --output-dir src/cpp01_launch/config/
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1146.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1147.webp)

还需要再配置cmakelists

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1148.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1149.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1150.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1151.webp)

我们要读就读install目录下的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1152.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1153.webp)

直接复制路径

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1154.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1155.webp)

可以进一步优化代码

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1156.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1157.webp)

这个就是来获取某个功能包的share目录路径

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1158.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1159.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1160.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node

封装终端指令相关类
from launch.actions import ExecuteProcess
from launch.substitutions import FindExecutable
参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
文件包含相关
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
分组相关
from launch_ros.actions import PushRosNamespace
from launch.actions import GroupAction
事件相关
from launch.event_handlers import OnProcessStart,OnProcessExit
from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo
获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

import os

def generate_launch_description():

    # turtle1 = Node(

    #     package="turtlesim",

    #     executable="turtlesim_node",

    #     exec_name="my_label",

    #     ros_arguments=["--remap","__ns:=/t2"]

    #     )
    turtle2 = Node(
        package="turtlesim",
        executable="turtlesim_node",
        name="haha",
        parameters=[os.path.join(get_package_share_directory("cpp01_launch"),"config","haha.yaml")]
        )
    return LaunchDescription([turtle2])
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1161.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1162.webp)

建议采用第三种 动态获取路径

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1163.webp)

respawn是自动重启的意思，这样运行后的节点，你用鼠标关闭小乌龟窗口后，也会自动重启节点，再把小乌龟窗口打开一个新的。

按Ctrl+C可以终止。

```python
from launch import LaunchDescription
from launch_ros.actions import Node

封装终端指令相关类
from launch.actions import ExecuteProcess
from launch.substitutions import FindExecutable
参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
文件包含相关
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
分组相关
from launch_ros.actions import PushRosNamespace
from launch.actions import GroupAction
事件相关
from launch.event_handlers import OnProcessStart,OnProcessExit
from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo
获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

import os

def generate_launch_description():

    # turtle1 = Node(

    #     package="turtlesim",

    #     executable="turtlesim_node",

    #     exec_name="my_label",

    #     ros_arguments=["--remap","__ns:=/t2"]

    #     )
    turtle2 = Node(
        package="turtlesim",
        executable="turtlesim_node",
        name="haha",

        # parameters=[{"background_r": 255,"background_g": 0,"background_b": 0}],

        # parameters=["/home/tungchiahui/mysource/ros2src/4.ws02_tools/install/cpp01_launch/share/cpp01_launch/config/haha.yaml"],
        parameters=[os.path.join(get_package_share_directory("cpp01_launch"),"config","haha.yaml")],
        respawn=True
        )
    return LaunchDescription([turtle2])
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1164.webp)

第一个是原话题名称，第二个是新话题名称

### Launch_Python_执行命令
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1165.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1166.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1167.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1168.webp)

这是个列表，列表里面写我们的指令

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1169.webp)

这样是把日志既输出到终端也输出到日志，如果不写则只会输出到日志。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1170.webp)

这样就是把命令当成终端指令来执行

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1171.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1172.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node

封装终端指令相关类
from launch.actions import ExecuteProcess
from launch.substitutions import FindExecutable
参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
文件包含相关
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
分组相关
from launch_ros.actions import PushRosNamespace
from launch.actions import GroupAction
事件相关
from launch.event_handlers import OnProcessStart,OnProcessExit
from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo
获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

def generate_launch_description():
    turtle = Node(
        package="turtlesim",
        executable="turtlesim_node"
    )
    cmd = ExecuteProcess(
        cmd = ["ros2 topic echo /turtle1/pose"],
        output = "both",
        shell = True
    )

    return LaunchDescription([turtle,cmd])
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1173.webp)

如果指令过长，可以分到多个字符串里运行。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1174.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1175.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1176.webp)

这样也是可以运行的。

```python
from launch import LaunchDescription
from launch_ros.actions import Node

封装终端指令相关类
from launch.actions import ExecuteProcess
from launch.substitutions import FindExecutable
参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
文件包含相关
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
分组相关
from launch_ros.actions import PushRosNamespace
from launch.actions import GroupAction
事件相关
from launch.event_handlers import OnProcessStart,OnProcessExit
from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo
获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

def generate_launch_description():
    turtle = Node(
        package="turtlesim",
        executable="turtlesim_node"
    )
    cmd = ExecuteProcess(
        cmd = [FindExecutable(name="ros2"),"topic","echo","/turtle1/pose"],
        output = "both",
        shell = True
    )

    return LaunchDescription([turtle,cmd])
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1177.webp)

### Launch_Python_参数设置
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1178.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1179.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1180.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1181.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1182.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1183.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1184.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1185.webp)

没传值的话，乌龟红色是满的，那就是粉红色背景

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1186.webp)

也可以传值，比如传backg_r:=0

这样背景色就变的更偏蓝绿了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1187.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1188.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1189.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node

封装终端指令相关类
from launch.actions import ExecuteProcess
from launch.substitutions import FindExecutable
参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
文件包含相关
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
分组相关
from launch_ros.actions import PushRosNamespace
from launch.actions import GroupAction
事件相关
from launch.event_handlers import OnProcessStart,OnProcessExit
from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo
获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

def generate_launch_description():

    bg_r = DeclareLaunchArgument(name="backg_r",default_value="255")
    bg_g = DeclareLaunchArgument(name="backg_g",default_value="255")
    bg_b = DeclareLaunchArgument(name="backg_b",default_value="255")
    turtle = Node(
        package="turtlesim",
        executable="turtlesim_node",
        parameters=[{"background_r" : LaunchConfiguration("backg_r"),"background_g" : LaunchConfiguration("backg_g"),"background_b" : LaunchConfiguration("backg_b")}]
    )
    return LaunchDescription([bg_r,bg_g,bg_b,turtle])
```

### Launch_Python_文件包含
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1190.webp)

假设我要编写一个机器人启动相关的launch文件，在这个launch文件中，我可能要启动雷达，启动IMU，启动底盘等等，我们需要把这些launch文件都包含进机器人启动的launch文件中。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1191.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1192.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1193.webp)

这个值是由被包含的launch文件封装而来的对象。

这个对象对应的类就是PythonLaunchDescriptionSource，

类里面还得设置一个参数。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1194.webp)

这个参数是launch_file_path就是文件路径。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1195.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1196.webp)

建议用这个来获取路径。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1197.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1198.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1199.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1200.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1201.webp)

也可以为其传参

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1202.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1203.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1204.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node

封装终端指令相关类
from launch.actions import ExecuteProcess
from launch.substitutions import FindExecutable
参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
文件包含相关
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
分组相关
from launch_ros.actions import PushRosNamespace
from launch.actions import GroupAction
事件相关
from launch.event_handlers import OnProcessStart,OnProcessExit
from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo
获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

import os

def generate_launch_description():
    include = IncludeLaunchDescription(
        launch_description_source=PythonLaunchDescriptionSource(
            launch_file_path=os.path.join(
                get_package_share_directory("cpp01_launch"),
                "launch/py",
                "py04_args_launch.py"
            )
        ),
        launch_arguments=[("backg_r","80"),("backg_g","10"),("backg_b","200")]
    )
    return LaunchDescription([include])
```

### Launch_Python_分组设置
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1205.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1206.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1207.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1208.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1209.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1210.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1211.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node

封装终端指令相关类
from launch.actions import ExecuteProcess
from launch.substitutions import FindExecutable
参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
文件包含相关
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
分组相关
from launch_ros.actions import PushRosNamespace
from launch.actions import GroupAction
事件相关
from launch.event_handlers import OnProcessStart,OnProcessExit
from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo
获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

def generate_launch_description():
    turtle1 = Node(
    package="turtlesim",
    executable="turtlesim_node",
    name="t1"
    )
    turtle2 = Node(
    package="turtlesim",
    executable="turtlesim_node",
    name="t2"
    )
    turtle3 = Node(
    package="turtlesim",
    executable="turtlesim_node",
    name="t3"
    )

    group1 = GroupAction(actions=[PushRosNamespace("g1"),turtle1,turtle2])
    group2 = GroupAction(actions=[PushRosNamespace("g2"),turtle3])

    return LaunchDescription([group1,group2])
```

### Launch_Python_事件设置
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1212.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1213.webp)

第一个主要用于注册事件，第二个是开始事件，第三个是节点退出。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1214.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1215.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1216.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1217.webp)

这个是在终端中生成新小乌龟的命令

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1218.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1219.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1220.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1221.webp)

第一个参数是针对哪个事件进行注册。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1222.webp)

target_action是事件源，你要为哪个节点注册事件。

on_start是等到事件被触发，你要做哪些操作？

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1223.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1224.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1225.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1226.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node

封装终端指令相关类
from launch.actions import ExecuteProcess
from launch.substitutions import FindExecutable
参数声明与获取
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
文件包含相关
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
分组相关
from launch_ros.actions import PushRosNamespace
from launch.actions import GroupAction
事件相关
from launch.event_handlers import OnProcessStart,OnProcessExit
from launch.actions import ExecuteProcess,RegisterEventHandler,LogInfo
获取功能包下share目录或路径
from ament_index_python.packages import get_package_share_directory

def generate_launch_description():
    turtle = Node(
    package="turtlesim",
    executable="turtlesim_node",
    )
    spawn = ExecuteProcess(
        cmd=["ros2 service call /spawn turtlesim/srv/Spawn \"{'x': 8.0,'y': 3.0}\""],
        output="both",
        shell=True
    )

    event_start = RegisterEventHandler(
        event_handler=OnProcessStart(
            target_action=turtle,
            on_start=spawn
        )
    )

    event_exit = RegisterEventHandler(
        event_handler=OnProcessExit(
            target_action=turtle,
            on_exit=[LogInfo(msg="turtlesim_node:退出！")]
        )
    )
    return LaunchDescription([turtle,event_start,event_exit])
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1227.webp)

on_exit可以创建一个对象，也可以放在列表里。

可以只学Python版本的Launch，XML和Yaml版本的Launch可以了解就行，自己写就写Python版本的，当你要用到别人开源的功能包用的Launch是Xml或者yaml版本一般不影响正常使用。

### Launch_XML_YAML_Node
### Launch_XML_YAML_执行命令
### Launch_XML_YAML_参数设置
### Launch_XML_YAML_分组设置
### Launch_XML_YAML_文件包含
