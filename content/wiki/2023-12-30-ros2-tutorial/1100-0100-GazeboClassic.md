---
title: "Gezebo Classic"
---

### Gazebo([Gazebo Classic](https://classic.gazebosim.org/))
这个是老版Gazebo，仅在ROS1和ROS2 Humble上可用。（但是ROS2并不推荐使用老版Gazebo，更加建议使用新版Gazebo）

ROS2老版Gazebo仅在Humble版本上可用，在Jazzy及以后的版本已移除。

**（不想学老版Gazebo的，直接跳到下一节的Ignition Gazebo即可）**

这俩对咱们的区别不算太大，也就是一个教程多，一个教程少的区别，你直接学老版Gazebo也是一样用的。

**（初学者也可以只学教程多的Gazebo Classic,可以少走一些弯路）**

Gezebo官网：

https://gazebosim.org/home

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1627.webp)

如果你想从老版的Gazebo迁移到新版的Gazebo，那么请看下方官方教程：

从Gazebo Classic迁移到ROS2 Humble的Ign Gazebo(Gazebo Fortress)：

https://gazebosim.org/docs/fortress/gazebo_classic_migration/

从Gazebo Classic迁移到ROS2 Jazzy的Gazebo Sim(Gazebo Harmonic)：

Jazzy之后的版本应该变化不会太大，也可以暂时参照下面这个教程（或者去官网找对应版本的教程）：

https://gazebosim.org/docs/harmonic/gazebo_classic_migration/

#### Gazebo Classic安装与运行
ROS2只有humble有老版Gazebo

```bash
sudo apt install ros-humble-gazebo-ros ros-humble-gazebo-ros-pkgs
```

安装完成后，我们就可以通过下面的命令行来启动gazebo并加载ros2插件。

```bash
gazebo --verbose -s libgazebo_ros_init.so -s libgazebo_ros_factory.so 
```

看到下面这个日志和gazebo界面，没啥大问题就说明成功了。（一些警告是说Gazebo Classic已经弃用，鼓励使用新版Ingition Gazebo，忽略这些警告即可）

```sql
root@Dell-G15-5511:/home/tungchiahui/UserFolder/MySource/ROS_WS/ROS2_WS/6.ws_simulations$ gazebo --verbose -s libgazebo_ros_init.so -s libgazebo_ros_factory.so 
Gazebo multi-robot simulator, version 11.10.2
Copyright (C) 2012 Open Source Robotics Foundation.
Released under the Apache 2 License.
http://gazebosim.org

[Msg] Waiting for master.
Gazebo multi-robot simulator, version 11.10.2
Copyright (C) 2012 Open Source Robotics Foundation.
Released under the Apache 2 License.
http://gazebosim.org

[Wrn] [gazebo_ros_init.cpp:178] 

#     # ####### ####### ###  #####  #######

##    # #     #    #     #  #     # #

# #   # #     #    #     #  #       #

#  #  # #     #    #     #  #       #####

#   # # #     #    #     #  #       #

#    ## #     #    #     #  #     # #

#     # #######    #    ###  #####  #######

This version of Gazebo, now called Gazebo classic, reaches end-of-life
in January 2025. Users are highly encouraged to migrate to the new Gazebo
using our migration guides (https://gazebosim.org/docs/latest/gazebo_classic_migration?utm_source=gazebo_ros_pkgs&utm_medium=cli)

[Msg] Waiting for master.
[Msg] Connected to gazebo master @ http://127.0.0.1:11345
[Msg] Publicized address: 192.168.31.60
[Msg] Loading world file [/usr/share/gazebo-11/worlds/empty.world]
XDG_RUNTIME_DIR (/run/user/1000) is not owned by us (uid 0), but by uid 1000! (This could e.g. happen if you try to connect to a non-root PulseAudio as a root user, over the native protocol. Don't do that.)
ALSA lib pcm_dmix.c:1032:(snd_pcm_dmix_open) unable to open slave
AL lib: (EE) ALCplaybackAlsa_open: Could not open playback device 'default': No such file or directory
[Err] [OpenAL.cc:84] Unable to open audio device[default]
 Audio will be disabled.
[Msg] Connected to gazebo master @ http://127.0.0.1:11345
[Msg] Publicized address: 192.168.31.60
[Wrn] [GuiIface.cc:298] Couldn't locate specified .ini. Creating file at "/root/.gazebo/gui.ini"
[Wrn] [GuiIface.cc:120] QStandardPaths: runtime directory '/run/user/1000' is not owned by UID 0, but a directory permissions 0700 owned by UID 1000 GID 1000
[Wrn] [Event.cc:61] Warning: Deleting a connection right after creation. Make sure to save the ConnectionPtr from a Connect call

libcurl: (35) error:0A000126:SSL routines::unexpected eof while reading
[Wrn] [ModelDatabase.cc:212] Unable to connect to model database using [http://models.gazebosim.org//database.config]. Only locally installed models will be available.
[Wrn] [Event.cc:61] Warning: Deleting a connection right after creation. Make sure to save the ConnectionPtr from a Connect call
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1628.webp)

#### 插件及节点服务介绍
使用之前的命令启动Gazebo并加载gazebo_ros插件，我们使用下面的指令来看插件的节点，以及改节点为我们提供的服务有哪些？

节点列表

```bash
ros2 node list
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1629.webp)

然后我们看看这个节点对外提供的服务有哪些？

```bash
ros2 service list
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1630.webp)

除去和参数相关的几个服务，我们可以看到另外三个特殊服务：

*   /spawn_entity，用于加载模型到gazebo中

*   /get_model_list，用于获取模型列表

*   /delete_entity，用于删除gazbeo中已经加载的模型

我们想要让gazebo显示出我们配置好的机器人模型使用/spawn_entity来加载即可。

接着我们可以来请求服务来加载模型，先带你看一下服务的接口类型。

```bash
ros2 service type /spawn_entity
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1631.webp)

```bash
ros2 interface show gazebo_msgs/srv/SpawnEntity
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1632.webp)

可以看到服务的请求内容包括：

*   string name ，需要加载的实体的名称 (可选的)。

*   string xml ，实体的XML描述字符串, URDF或者SDF。

*   string robot_namespace ，产生的机器人和所有的ROS接口的命名空间，多机器人仿真的时候很有用。

*   geometry_msgs/Pose initial_pose ，机器人的初始化位置

*   string reference_frame ，初始姿态是相对于该实体的frame定义的。如果保持"empty"或"world"或“map”，则使用 gazebo的world作为frame。如果指定了不存在的实体，则会返回错误

#### 调用服务加载模型
我们这里教程使用鱼香ROS的fishbot模型：https://github.com/fishros/fishbot/blob/navgation2/src/fishbot_description/urdf/fishbot_gazebo.urdf

```xml
<?xml version="1.0"?>
<robot name="fishbot">

  <link name="base_footprint"/>

  <joint name="base_joint" type="fixed">
    <parent link="base_footprint"/>
    <child link="base_link"/>
    <origin xyz="0.0 0.0 0.076" rpy="0 0 0"/>
  </joint>

  <link name="base_link">
          <visual>
      <origin xyz="0 0 0.0" rpy="0 0 0"/>
      <geometry>
                <cylinder length="0.12" radius="0.10"/>
      </geometry>
      <material name="blue">
              <color rgba="0.1 0.1 1.0 0.5" /> 
      </material>
    </visual>
    <collision>
      <origin xyz="0 0 0.0" rpy="0 0 0"/>
      <geometry>
                <cylinder length="0.12" radius="0.10"/>
      </geometry>
      <material name="blue">
              <color rgba="0.1 0.1 1.0 0.5" /> 
      </material>
    </collision>
    <inertial>
      <mass value="0.2"/>
      <inertia ixx="0.0122666" ixy="0" ixz="0" iyy="0.0122666" iyz="0" izz="0.02"/>
    </inertial>
  </link>

  <link name="laser_link">
    <visual>
      <origin xyz="0 0 0" rpy="0 0 0"/>
      <geometry>
        <cylinder length="0.02" radius="0.02"/>
      </geometry>
      <material name="black">
        <color rgba="0.0 0.0 0.0 0.5" /> 
      </material>
    </visual>
    <collision>
      <origin xyz="0 0 0" rpy="0 0 0"/>
      <geometry>
        <cylinder length="0.02" radius="0.02"/>
      </geometry>
      <material name="black">
        <color rgba="0.0 0.0 0.0 0.5" /> 
      </material>
    </collision>
    <inertial>
    <mass value="0.1"/>
      <inertia ixx="0.000190416666667" ixy="0" ixz="0" iyy="0.0001904" iyz="0" izz="0.00036"/>
    </inertial>
  </link>

  <joint name="laser_joint" type="fixed">
      <parent link="base_link" />
      <child link="laser_link" />
      <origin xyz="0 0 0.075" />
  </joint>

  <link name="imu_link">
          <visual>
      <origin xyz="0 0 0.0" rpy="0 0 0"/>
      <geometry>
                    <box size="0.02 0.02 0.02"/>
      </geometry>
    </visual>
    <collision>
      <origin xyz="0 0 0.0" rpy="0 0 0"/>
      <geometry>
                    <box size="0.02 0.02 0.02"/>
      </geometry>
    </collision>
    <inertial>
      <mass value="0.1"/>
        <inertia ixx="0.000190416666667" ixy="0" ixz="0" iyy="0.0001904" iyz="0" izz="0.00036"/>
      </inertial>
  </link>

  <joint name="imu_joint" type="fixed">
      <parent link="base_link" />
      <child link="imu_link" />
      <origin xyz="0 0 0.02" />
  </joint>

  <link name="left_wheel_link">
      <visual>
        <origin xyz="0 0 0" rpy="1.57079 0 0"/>
        <geometry>
          <cylinder length="0.04" radius="0.032"/>
        </geometry>
          <material name="black">
            <color rgba="0.0 0.0 0.0 0.5" /> 
          </material>
      </visual>
      <collision>
        <origin xyz="0 0 0" rpy="1.57079 0 0"/>
        <geometry>
          <cylinder length="0.04" radius="0.032"/>
        </geometry>
          <material name="black">
            <color rgba="0.0 0.0 0.0 0.5" /> 
          </material>
      </collision>
      <inertial>
        <mass value="0.2"/>
          <inertia ixx="0.000190416666667" ixy="0" ixz="0" iyy="0.0001904" iyz="0" izz="0.00036"/>
        </inertial>
  </link>

  <link name="right_wheel_link">
      <visual>
        <origin xyz="0 0 0" rpy="1.57079 0 0"/>
        <geometry>
          <cylinder length="0.04" radius="0.032"/>
        </geometry>
          <material name="black">
            <color rgba="0.0 0.0 0.0 0.5" /> 
          </material>
      </visual>
      <collision>
        <origin xyz="0 0 0" rpy="1.57079 0 0"/>
        <geometry>
          <cylinder length="0.04" radius="0.032"/>
        </geometry>
          <material name="black">
            <color rgba="0.0 0.0 0.0 0.5" /> 
          </material>
      </collision>
      <inertial>
      <mass value="0.2"/>
      <inertia ixx="0.000190416666667" ixy="0" ixz="0" iyy="0.0001904" iyz="0" izz="0.00036"/>
    </inertial>
  </link>

  <joint name="left_wheel_joint" type="continuous">
      <parent link="base_link" />
      <child link="left_wheel_link" />
      <origin xyz="-0.02 0.10 -0.06" />
      <axis xyz="0 1 0" />
  </joint>

  <joint name="right_wheel_joint" type="continuous">
      <parent link="base_link" />
      <child link="right_wheel_link" />
      <origin xyz="-0.02 -0.10 -0.06" />
      <axis xyz="0 1 0" />
  </joint>

  <link name="caster_link">
      <visual>
        <origin xyz="0 0 0" rpy="1.57079 0 0"/>
        <geometry>
            <sphere radius="0.016"/>
        </geometry>
          <material name="black">
            <color rgba="0.0 0.0 0.0 0.5" /> 
          </material>
      </visual>
      <collision>
        <origin xyz="0 0 0" rpy="1.57079 0 0"/>
        <geometry>
            <sphere radius="0.016"/>
        </geometry>
          <material name="black">
            <color rgba="0.0 0.0 0.0 0.5" /> 
          </material>
      </collision>
      <inertial>
      <mass value="0.02"/>
      <inertia ixx="0.000190416666667" ixy="0" ixz="0" iyy="0.0001904" iyz="0" izz="0.00036"/>
    </inertial>
  </link>

  <joint name="caster_joint" type="fixed">
      <parent link="base_link" />
      <child link="caster_link" />
      <origin xyz="0.06 0.0 -0.076" />
      <axis xyz="0 1 0" />
  </joint>

  <gazebo reference="caster_link">
    <material>Gazebo/Black</material>
  </gazebo>

  <gazebo reference="caster_link">
    <mu1 value="0.0"/>
    <mu2 value="0.0"/>
    <kp value="1000000.0" />
    <kd value="10.0" />

  </gazebo>

  <gazebo>
    <plugin name='diff_drive' filename='libgazebo_ros_diff_drive.so'>
          <ros>
            <namespace>/</namespace>
            <remapping>cmd_vel:=cmd_vel</remapping>
            <remapping>odom:=odom</remapping>
          </ros>
          <update_rate>30</update_rate>

          <left_joint>left_wheel_joint</left_joint>
          <right_joint>right_wheel_joint</right_joint>

          <wheel_separation>0.2</wheel_separation>
          <wheel_diameter>0.065</wheel_diameter>

          <max_wheel_torque>20</max_wheel_torque>
          <max_wheel_acceleration>1.0</max_wheel_acceleration>

          <publish_odom>true</publish_odom>
          <publish_odom_tf>true</publish_odom_tf>
          <publish_wheel_tf>false</publish_wheel_tf>
          <odometry_frame>odom</odometry_frame>
          <robot_base_frame>base_footprint</robot_base_frame>
      </plugin>

      <plugin name="fishbot_joint_state" filename="libgazebo_ros_joint_state_publisher.so">
        <ros>
          <remapping>~/out:=joint_states</remapping>
        </ros>
        <update_rate>30</update_rate>
        <joint_name>right_wheel_joint</joint_name>
        <joint_name>left_wheel_joint</joint_name>
      </plugin>    
      </gazebo> 

      <gazebo reference="laser_link">
        <material>Gazebo/Black</material>
      </gazebo>

    <gazebo reference="imu_link">
      <sensor name="imu_sensor" type="imu">
      <plugin filename="libgazebo_ros_imu_sensor.so" name="imu_plugin">
          <ros>
            <namespace>/</namespace>
            <remapping>~/out:=imu</remapping>
          </ros>
          <initial_orientation_as_reference>false</initial_orientation_as_reference>
        </plugin>
        <always_on>true</always_on>
        <update_rate>100</update_rate>
        <visualize>true</visualize>
        <imu>
          <angular_velocity>
            <x>
              <noise type="gaussian">
                <mean>0.0</mean>
                <stddev>2e-4</stddev>
                <bias_mean>0.0000075</bias_mean>
                <bias_stddev>0.0000008</bias_stddev>
              </noise>
            </x>
            <y>
              <noise type="gaussian">
                <mean>0.0</mean>
                <stddev>2e-4</stddev>
                <bias_mean>0.0000075</bias_mean>
                <bias_stddev>0.0000008</bias_stddev>
              </noise>
            </y>
            <z>
              <noise type="gaussian">
                <mean>0.0</mean>
                <stddev>2e-4</stddev>
                <bias_mean>0.0000075</bias_mean>
                <bias_stddev>0.0000008</bias_stddev>
              </noise>
            </z>
          </angular_velocity>
          <linear_acceleration>
            <x>
              <noise type="gaussian">
                <mean>0.0</mean>
                <stddev>1.7e-2</stddev>
                <bias_mean>0.1</bias_mean>
                <bias_stddev>0.001</bias_stddev>
              </noise>
            </x>
            <y>
              <noise type="gaussian">
                <mean>0.0</mean>
                <stddev>1.7e-2</stddev>
                <bias_mean>0.1</bias_mean>
                <bias_stddev>0.001</bias_stddev>
              </noise>
            </y>
            <z>
              <noise type="gaussian">
                <mean>0.0</mean>
                <stddev>1.7e-2</stddev>
                <bias_mean>0.1</bias_mean>
                <bias_stddev>0.001</bias_stddev>
              </noise>
            </z>
          </linear_acceleration>
        </imu>
      </sensor>
    </gazebo>

    <gazebo reference="laser_link">
      <sensor name="laser_sensor" type="ray">
      <always_on>true</always_on>
      <visualize>true</visualize>
      <update_rate>5</update_rate>
      <pose>0 0 0.075 0 0 0</pose>
      <ray>
          <scan>
            <horizontal>
              <samples>360</samples>
              <resolution>1.000000</resolution>
              <min_angle>0.000000</min_angle>
              <max_angle>6.280000</max_angle>
            </horizontal>
          </scan>
          <range>
            <min>0.120000</min>
            <max>3.5</max>
            <resolution>0.015000</resolution>
          </range>
          <noise>
            <type>gaussian</type>
            <mean>0.0</mean>
            <stddev>0.01</stddev>
          </noise>
      </ray>

      <plugin name="laserscan" filename="libgazebo_ros_ray_sensor.so">
        <ros>

          <remapping>~/out:=scan</remapping>
        </ros>
        <output_type>sensor_msgs/LaserScan</output_type>
        <frame_name>laser_link</frame_name>
      </plugin>
      </sensor>
    </gazebo>

</robot>
```

看到这里你是不是迫不及待敲起来命令行来加载我们的机器人到gazebo了，别着急，小鱼再推荐一个可视化服务请求工具，其实在第六章中小鱼介绍过，在rqt工具集里有一个叫服务请求工具。

命令行输入rqt，在插件选项中选择Services->Service Caller,然后再下拉框选择/spawn_entity服务，即可看到下面的界面。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1633.webp)

接着我们把我们的FishBot的URDF模型复制粘贴，放到xml中（注意要把原来的''删掉哦！）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1634.webp)

然后点右上角的call，可以显示下图成功了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1635.webp)

接着就可以看到工厂返回说成功把机器人制作出来送入gazebo了。

此时再看我们的Gazebo,一个小小的，白白的机器人出现了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1636.webp)

shift+鼠标左键，或者直接点滚轮中键，都可以拖动视角。很多玩过第三人称游戏的学弟学妹肯定都不陌生这种操控吧。

#### 在不同位置加载多个机器人
可以再生产一个fishbot（为了后面需要多机器人仿真的小伙伴）。

修改rqt中的参数，增加一个命名空间，然后修改一个位置，让第二个机器人和第一个相距1m的地方生产，然后点击Call。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1637.webp)

返回成功，此时拖送Gazebo观察一下，发现多出了一个机器人，距离刚好是在X轴（红色）1米（一个小格子一米）处。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1638.webp)

#### 查询和删除机器人
利用rqt工具，我们再对另外两个服务接口进行请求。

首先先查询有几个模型在仿真环境中

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1639.webp)

查到了三个模型，一个大地，一个fishbot，一个fishbot_0。

我们接着尝试把fishbot_0删掉，选择删除实体，输入fishbot_0的名字，拿起小电话通知工厂回收我们的0号fishbot。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1640.webp)

调用成功，观察gazebo发现机器人，人没了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1641.webp)

#### 创建工作空间
本节代码参考鱼香ROS：https://github.com/fishros/fishbot/tree/navgation2

```bash

# 创建工作空间
mkdir -p ws_simulations/src
cd ws_simulations/src
```

创建功能包

```bash
ros2 pkg create fishbot_description --build-type ament_cmake
cd fishbot_description
```

然后配置package.xml

```xml
  <exec_depend>rviz2</exec_depend>
  <exec_depend>xacro</exec_depend>
  <exec_depend>robot_state_publisher</exec_depend>
  <exec_depend>joint_state_publisher</exec_depend>
  <exec_depend>ros2launch</exec_depend>
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1642.webp)

然后修改cmakelists.txt

```bash
install(
  DIRECTORY launch urdf rviz meshes
  DESTINATION share/${PROJECT_NAME}  
)
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1643.webp)

克隆下鱼香ROS的仓库，并复制下里面的文件到咱们的目录下。这都是上一节的东西，与本节学习无关，直接复制就行。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1644.webp)

打开工作空间，在`src/fishbot_description/launch`中添加一个`gazebo.launch.py`文件，我们开始编写launch文件来在gazebo中加载机器人模型。

我们主要需要做两件事：

1.  启动gazebo，我们可以将命令行写成一个launch节点

```python
ExecuteProcess(
        cmd=['gazebo', '--verbose','-s', 'libgazebo_ros_init.so', '-s', 'libgazebo_ros_factory.so', gazebo_world_path],
        output='screen')

```

2.  上面我们加载机器人是直接将XML格式的URDF复制过去进行加载的，这样很不方便，我们可以使用gazebo_ros为我们提供好的一个叫做`spawn_entity.py`节点，该节点支持从文件地址直接生产机器人到Gazebo。

该节点需要两个参数，一个机器人的模型名字和urdf的文件地址，这个简单，前面我们曾经使用package_share来拼接过urdf路径。

```python
spawn_entity_cmd = Node(
    package='gazebo_ros', 
    executable='spawn_entity.py',
    arguments=['-entity', robot_name_in_model,  '-file', urdf_model_path ], output='screen')
```

先加载赵虚左老师的模板

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1645.webp)

我们首先需要ExecuteProcess来输入终端命令，所以要先把from launch.actions import ExecuteProcess的注释先打开。

为了方便修改加载的机器人模型和urdf,我们还需要使用share目录，所以也要把from ament_index_python.packages import get_package_share_directory的注释打开，并import os。

写完后的launch文件如下：

```python
from launch import LaunchDescription
from launch_ros.actions import Node

# 封装终端指令相关类
from launch.actions import ExecuteProcess

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
import os

def generate_launch_description():
    robot_name_in_model = 'fishbot'
    package_name = 'fishbot_description'
    urdf_name = "fishbot_gazebo.urdf"

    pkg_share = get_package_share_directory(f"{package_name}")
    urdf_model_path = os.path.join(pkg_share, f'urdf/urdf/{urdf_name}')

    # Start Gazebo server
    start_gazebo_cmd = ExecuteProcess(
        cmd=['gazebo', '--verbose','-s', 'libgazebo_ros_init.so', '-s', 'libgazebo_ros_factory.so'],
        output='screen')

    # Launch the robot
    spawn_entity_cmd = Node(
        package='gazebo_ros', 
        executable='spawn_entity.py',
        arguments=['-entity', robot_name_in_model,  '-file', urdf_model_path ], output='screen')

    return LaunchDescription([start_gazebo_cmd,spawn_entity_cmd])
```

编译运行

```python
colcon build --packages-select fishbot_description
source install/setup.bash
ros2 launch fishbot_description gazebo.launch.py
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1646.webp)

#### 插件
使用下面的指令可以查看所有的动态链接库：

```python
ls /opt/ros/humble/lib/libgazebo_ros*
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1647.webp)

```bash
/opt/ros/humble/lib/libgazebo_ros2_control.so
/opt/ros/humble/lib/libgazebo_ros_ackermann_drive.so
/opt/ros/humble/lib/libgazebo_ros_bumper.so
/opt/ros/humble/lib/libgazebo_ros_camera.so
/opt/ros/humble/lib/libgazebo_ros_diff_drive.so
/opt/ros/humble/lib/libgazebo_ros_elevator.so
/opt/ros/humble/lib/libgazebo_ros_factory.so
/opt/ros/humble/lib/libgazebo_ros_force.so
/opt/ros/humble/lib/libgazebo_ros_force_system.so
/opt/ros/humble/lib/libgazebo_ros_ft_sensor.so
/opt/ros/humble/lib/libgazebo_ros_gps_sensor.so
/opt/ros/humble/lib/libgazebo_ros_hand_of_god.so
/opt/ros/humble/lib/libgazebo_ros_harness.so
/opt/ros/humble/lib/libgazebo_ros_imu_sensor.so
/opt/ros/humble/lib/libgazebo_ros_init.so
/opt/ros/humble/lib/libgazebo_ros_joint_pose_trajectory.so
/opt/ros/humble/lib/libgazebo_ros_joint_state_publisher.so
/opt/ros/humble/lib/libgazebo_ros_node.so
/opt/ros/humble/lib/libgazebo_ros_p3d.so
/opt/ros/humble/lib/libgazebo_ros_planar_move.so
/opt/ros/humble/lib/libgazebo_ros_projector.so
/opt/ros/humble/lib/libgazebo_ros_properties.so
/opt/ros/humble/lib/libgazebo_ros_ray_sensor.so
/opt/ros/humble/lib/libgazebo_ros_state.so
/opt/ros/humble/lib/libgazebo_ros_template.so
/opt/ros/humble/lib/libgazebo_ros_tricycle_drive.so
/opt/ros/humble/lib/libgazebo_ros_utils.so
/opt/ros/humble/lib/libgazebo_ros_vacuum_gripper.so
/opt/ros/humble/lib/libgazebo_ros_video.so
/opt/ros/humble/lib/libgazebo_ros_wheel_slip.so
```

##### 运动控制插件+里程计odom
本节课通过配置两轮差速控制插件，让我们的机器人动起来

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1648.webp)

插件介绍：

Gazebo是一个独立于ROS的软件，对外提供了丰富的API可以使用，gazebo的插件按照用途大致可以分为两种：

1.  用于控制的插件，通过插件可以控制机器人关节运动，可以进行位置、速度、力的控制，比如我们这节课的两轮差速控制器。

2.  用于数据采集的插件，比如IMU传感器用于采集机器人的惯性，激光雷达用于采集机器人周围的点云信息。

当然上面两类插件功能也可以写到一个插件里，两轮差速插件（gazebo_ros_diff_drive）就是一个二合一加强版。（差速控制+odom）

两轮差速插件用于控制机器人轮子关节的位置变化，同时该插件还会获取轮子的位置以及速度的信息的反馈，根据反馈的位置信息结合运动学模型即可计算出当前机器人的位姿（里程计）。

两轮差速控制器可以将轮子的目标转速发送给Gazebo，并从Gazebo获取到实际的速度和位置。（注意：发送给Gazebo是目标速度，反馈回来的是实际速度。目标!=实际，比如轮子卡住了，无论你发什么目标速度，实际速度都是0。）

要想快速了解一个系统的功能，最直接的就是看系统的对外的输入和输出是什么？什么都不要说，看下图：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1649.webp)

上图就是对gazebo_ros_diff_drive的输入和输出信息的总结，可以很直观的看到该插件主要输入控制指令，主要输出里程计信息。接着小鱼带你分别认识一下输入和输出两个部分。

这个插件需要配置一系列参数如下图：

不知道你是否还记得在第七章中，小鱼对两轮差速底盘的运动学正的介绍。如果要完成底盘的正逆解和里程计的推算就必须要知道轮子的直径和间距。

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

控制指令：两轮差速控制器默认通过订阅话题`cmd_vel`来获取目标线速度和角速度。该话题的类型为：`geometry_msgs/msg/Twist`

该接口里主要是一些线速度和角速度都包含在x、y、z，代表坐标系的三个方向上的对应速度。 **（详细的接口内容请看硬件平台章节）**

两轮差速控制器收到这个话题数据后将其中的角速度和线速度转换上两个轮子的转动速度发送给Gazebo。

输出的信息：

里程计信息默认的输出话题为`odom`，其消息类型为：`nav_msgs/msg/Odometry`

其数据主要包含三个部分： **（详细的接口内容请看硬件平台章节）**

*   header，表示该消息发布的时间

*   pose，表示当前机器人位置和朝向

*   twist，表示当前机器人的线速度和角速度

*   数据中还包含一个covariance，其代表协方差矩阵，后面小鱼写篇文章来介绍下，这里只需了解其含义即可。

里程计TF信息也可以输出：设为true，订阅tf话题里你就可以看到像下面的msg，建议后面配置好后，手动修改下，对比区别

```yaml
- header:
    stamp:
      sec: 6157
      nanosec: 907000000
    frame_id: odom
  child_frame_id: base_footprint
  transform:
    translation:
      x: 0.0005557960241049835
      y: -0.0007350446303238693
      z: 0.01599968753145574
    rotation:
      x: 4.691143395208505e-07
      y: 7.115496626557812e-06
      z: -0.018531475772549166
      w: 0.9998282774331005
```

轮子TF信息也可以输出：设为true，订阅tf话题里你就可以看到像下面的msg，建议后面配置好后，手动修改下，对比区别

```yaml
- header:
    stamp:
      sec: 6157
      nanosec: 941000000
    frame_id: base_link
  child_frame_id: left_wheel_link
  transform:
    translation:
      x: -0.02
      y: 0.1
      z: -0.06
    rotation:
      x: 0.0
      y: 0.049519025127821005
      z: 0.0
      w: 0.9987731805321918
- header:
    stamp:
      sec: 6157
      nanosec: 941000000
    frame_id: base_link
  child_frame_id: right_wheel_link
  transform:
    translation:
      x: -0.02
      y: -0.1
      z: -0.06
    rotation:
      x: 0.0
      y: -0.0663387077034509
      z: 0.0
      w: 0.9977971616817898

```

咱们之前下载的那个鱼香ROS的fishbot的urdf已经包含了差速插件的内容，如下：

因为是给Gazebo的插件，所以在`URDF`中，我们需要使用`<gazebo>`进行配置，因为是要给`gazebo`配置插件，所有要在`gazebo`标签下添加`plugin`子插件。

```xml
  <gazebo>
    <plugin name='diff_drive' filename='libgazebo_ros_diff_drive.so'>
          <ros>
            <namespace>/</namespace>
            <remapping>cmd_vel:=cmd_vel</remapping>
            <remapping>odom:=odom</remapping>
          </ros>
          <update_rate>30</update_rate>

          <left_joint>left_wheel_joint</left_joint>
          <right_joint>right_wheel_joint</right_joint>

          <wheel_separation>0.2</wheel_separation>
          <wheel_diameter>0.065</wheel_diameter>

          <max_wheel_torque>20</max_wheel_torque>
          <max_wheel_acceleration>1.0</max_wheel_acceleration>

          <publish_odom>true</publish_odom>
          <publish_odom_tf>true</publish_odom_tf>
          <publish_wheel_tf>true</publish_wheel_tf>
          <odometry_frame>odom</odometry_frame>
          <robot_base_frame>base_footprint</robot_base_frame>
      </plugin>
    </gazebo> 
```

编译一下：

```python
colcon build
source install/setup.bash
ros2 launch fishbot_description gazebo.launch.py
```

然后可以看看下面这俩命令：

```python
ros2 node list
ros2 topic list
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1650.webp)

可以看到了我们插件订阅的的/cmd_vel和发布的/odom了。

然后我们可以通过teleop-twist-keyboard节点发布cmd_vel来控制fishbot。

```python
sudo apt install ros-humble-teleop-twist-keyboard
```

使用下方节点来控制

```python
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

接着尝试使用来控制机器人运动

```python
   U    I    O
   J    K    L
   M    <    >
```

点一下，你就能看到fishbot在Gazebo中飞速的移动。接着打开终端，打印一下odom话题和tf话题，移动机器人观察数据变化。

也可以使用rqt显示速度数据

```python
rqt
```

选择Plugin->Visualization->Plot

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1651.webp)

在上方Topic输入`/cmd_vel/linear/x`，再输入`/cmd_vel/angular/z`，然后用键盘控制机器人移动。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1652.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1653.webp)

也可以在rviz2里看odom

```python
rviz2
```

点键盘控制节点按U，让机器人转圈。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1654.webp)

虽然机器人的轨迹已经在RVIZ中显示出来了，但是并没有机器人的模型，也看不到轮子的转动，咱们来带你一起解决这个问题。

前面咱们介绍过，要发布机器人模型我们所使用的节点是`robot_state_publisher`,所以我们在`gazebo.launch.py`中加入这个节点，同时再加上rviz2的启动节点，最终的`gazebo.launch.py`内容如下：

```python
from launch import LaunchDescription
from launch_ros.actions import Node

# 封装终端指令相关类
from launch.actions import ExecuteProcess

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
import os

def generate_launch_description():
    robot_name_in_model = 'fishbot'
    package_name = 'fishbot_description'
    urdf_name = "fishbot_gazebo.urdf"

    pkg_share = get_package_share_directory(f"{package_name}")
    urdf_model_path = os.path.join(pkg_share, f'urdf/urdf/{urdf_name}')

    # gazebo_world_path = os.path.join(pkg_share, 'world/fishbot.world')

    # Start Gazebo server

    # start_gazebo_cmd = ExecuteProcess(

    #     cmd=['gazebo', '--verbose','-s', 'libgazebo_ros_init.so', '-s', 'libgazebo_ros_factory.so',gazebo_world_path],

    #     output='screen')
    start_gazebo_cmd = ExecuteProcess(
        cmd=['gazebo', '--verbose','-s', 'libgazebo_ros_init.so', '-s', 'libgazebo_ros_factory.so'],
        output='screen')

    # Launch the robot
    spawn_entity_cmd = Node(
        package='gazebo_ros', 
        executable='spawn_entity.py',
        arguments=['-entity', robot_name_in_model,  '-file', urdf_model_path ], output='screen')

    # Start Robot State publisher
    start_robot_state_publisher_cmd = Node(
        package='robot_state_publisher',
        executable='robot_state_publisher',
        arguments=[urdf_model_path]
    )

    # Launch RViz
    start_rviz_cmd = Node(
        package='rviz2',
        executable='rviz2',
        name='rviz2',
        output='screen',

        # arguments=['-d', default_rviz_config_path]
        )

    return LaunchDescription([start_gazebo_cmd,spawn_entity_cmd,start_robot_state_publisher_cmd,start_rviz_cmd])
```

保存编译启动

```python
colcon build
ros2 launch fishbot_description gazebo.launch.py
```

这样rviz2里就有模型了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1655.webp)

可以保存下rviz2的配置

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1656.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1657.webp)

然后在launch里添加上rviz2的路径配置：

```python
    default_rviz_config_path = os.path.join(pkg_share, 'rviz/rviz2.rviz')

    # Launch RViz
    start_rviz_cmd = Node(
        package='rviz2',
        executable='rviz2',
        name='rviz2',
        output='screen',
        arguments=['-d', default_rviz_config_path]
        )
```

如下为全部的launch：

```python
from launch import LaunchDescription
from launch_ros.actions import Node

# 封装终端指令相关类
from launch.actions import ExecuteProcess

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
import os

def generate_launch_description():
    robot_name_in_model = 'fishbot'
    package_name = 'fishbot_description'
    urdf_name = "fishbot_gazebo.urdf"

    pkg_share = get_package_share_directory(f"{package_name}")
    urdf_model_path = os.path.join(pkg_share, f'urdf/urdf/{urdf_name}')

    # gazebo_world_path = os.path.join(pkg_share, 'world/fishbot.world')
    default_rviz_config_path = os.path.join(pkg_share, 'rviz/rviz2.rviz')

    # Start Gazebo server

    # start_gazebo_cmd = ExecuteProcess(

    #     cmd=['gazebo', '--verbose','-s', 'libgazebo_ros_init.so', '-s', 'libgazebo_ros_factory.so',gazebo_world_path],

    #     output='screen')
    start_gazebo_cmd = ExecuteProcess(
        cmd=['gazebo', '--verbose','-s', 'libgazebo_ros_init.so', '-s', 'libgazebo_ros_factory.so'],
        output='screen')

    # Launch the robot
    spawn_entity_cmd = Node(
        package='gazebo_ros', 
        executable='spawn_entity.py',
        arguments=['-entity', robot_name_in_model,  '-file', urdf_model_path ], output='screen')

    # Start Robot State publisher
    start_robot_state_publisher_cmd = Node(
        package='robot_state_publisher',
        executable='robot_state_publisher',
        arguments=[urdf_model_path]
    )

    # Launch RViz
    start_rviz_cmd = Node(
        package='rviz2',
        executable='rviz2',
        name='rviz2',
        output='screen',
        arguments=['-d', default_rviz_config_path]
        )

    return LaunchDescription([start_gazebo_cmd,spawn_entity_cmd,start_robot_state_publisher_cmd,start_rviz_cmd])
```

可以自行编译测试。

##### 惯性计IMU
上节课通过配置两轮差速控制器我们已经成功的让fishbot在gazebo中动了起来，本节课我们通过给fishbot的URDF配置IMU传感器插件，让IMU模块工作起来。

惯性测量单元是测量物体三轴姿态角(或角速率)以及加速度的装置。一般的，一个IMU包含了三个单轴的加速度计和三个单轴的陀螺，加速度计检测物体在载体坐标系统独立三轴的加速度信号，而陀螺检测载体相对于导航坐标系的角速度信号，测量物体在三维空间中的角速度和加速度，并以此解算出物体的姿态。在导航中有着很重要的应用价值。

上面这段话是小鱼从百科中摘抄出来的，你需要知道的一个关键点是IMU可以测量以下三组数据：

*   三维加速度计加速度

*   三维陀螺仪角速度

*   三维磁力计（有的也没有磁力计）

用六轴、九轴算法或其他算法等可以输出三轴欧拉角（Yaw,Pitch,Roll），欧拉角可以转化为四元数。

IMU长啥样？直接线下找控制组要就行，他们经常会玩这个。

便宜的长这样（MPU6050,MPU9050等）：

MPU6050是六轴的，MPU9050是九轴的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1658.webp)

贵的长这样（HWT101CT，HWT605等）：

其中HWT101CT是三轴的，HWT605是六轴的。（各有优缺点）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1659.webp)

不要钱的长什么样？

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1660.webp)

仿真的不要钱哈哈，接着我们来配置一下仿真的IMU。

IMU对应的消息类型为`sensor_msgs/msg/Imu`

ROS的imu信息只有三轴加速度，角速度和四元数。（并没有磁力计和欧拉角，磁力计并非必须要填的，而欧拉角和四元数可以互相转换，四元数更好被算法运算，所以选择用四元数）

具体imu接口请看硬件平台章节。

可以看到除了每个数据对应的三个协方差之外，每一个还都对应一个`3*3`的协方差矩阵。

有了上节课的经验，我们可以很轻松的添加IMU传感器，但是还有一个需要注意的地方，为了更真实的模拟IMU传感器，我们需要给我们的仿真IMU传感器加点料。

加什么？加点高斯噪声，高斯噪声只需要指定平均值和标准差两个参数即可，不过因为IMU传感器的特殊性，我们还需要给模型添加两个偏差参数，分别是 `平均值偏差`和`标准差偏差`。

有关Gazebo仿真和噪声模型更深入的介绍可以参考鱼香ROS发的两篇推文：

*   [Gazebo仿真进阶教程之传感器高斯噪声（一）](https://mp.weixin.qq.com/s/0-OEATkyfMf6wEyrP5csGw)

*   [Gazebo仿真进阶教程之传感器高斯噪声（二）](https://mp.weixin.qq.com/s/5k1SEGdASjUMbwWdSpf1PQ)

下面是IMU传感器的URDF配置代码，大家结合文章对应可以理解一下，IMU对应的插件库`libgazebo_ros_imu_sensor.so`：

```xml
    <gazebo reference="imu_link">
      <sensor name="imu_sensor" type="imu">
      <plugin filename="libgazebo_ros_imu_sensor.so" name="imu_plugin">
          <ros>
            <namespace>/</namespace>
            <remapping>~/out:=imu</remapping>
          </ros>
          <initial_orientation_as_reference>false</initial_orientation_as_reference>
        </plugin>
        <always_on>true</always_on>
        <update_rate>100</update_rate>
        <visualize>true</visualize>
        <imu>
          <angular_velocity>
            <x>
              <noise type="gaussian">
                <mean>0.0</mean>
                <stddev>2e-4</stddev>
                <bias_mean>0.0000075</bias_mean>
                <bias_stddev>0.0000008</bias_stddev>
              </noise>
            </x>
            <y>
              <noise type="gaussian">
                <mean>0.0</mean>
                <stddev>2e-4</stddev>
                <bias_mean>0.0000075</bias_mean>
                <bias_stddev>0.0000008</bias_stddev>
              </noise>
            </y>
            <z>
              <noise type="gaussian">
                <mean>0.0</mean>
                <stddev>2e-4</stddev>
                <bias_mean>0.0000075</bias_mean>
                <bias_stddev>0.0000008</bias_stddev>
              </noise>
            </z>
          </angular_velocity>
          <linear_acceleration>
            <x>
              <noise type="gaussian">
                <mean>0.0</mean>
                <stddev>1.7e-2</stddev>
                <bias_mean>0.1</bias_mean>
                <bias_stddev>0.001</bias_stddev>
              </noise>
            </x>
            <y>
              <noise type="gaussian">
                <mean>0.0</mean>
                <stddev>1.7e-2</stddev>
                <bias_mean>0.1</bias_mean>
                <bias_stddev>0.001</bias_stddev>
              </noise>
            </y>
            <z>
              <noise type="gaussian">
                <mean>0.0</mean>
                <stddev>1.7e-2</stddev>
                <bias_mean>0.1</bias_mean>
                <bias_stddev>0.001</bias_stddev>
              </noise>
            </z>
          </linear_acceleration>
        </imu>
      </sensor>
    </gazebo>
```

我们之前下载的fishbot已经包含该内容了，所以不用再添加了，直接运行即可。

```python
ros2 launch fishbot_description gazebo.launch.py
```
```python
ros2 topic list
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1661.webp)

```python
ros2 topic info /imu
ros2 topic echo /imu
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1662.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1663.webp)

```yaml
header:
  stamp:
    sec: 150
    nanosec: 599000000
  frame_id: base_footprint
orientation:
  x: 3.434713830866392e-07
  y: 7.119913105768616e-06
  z: -0.00028312437320413914
  w: 0.9999999598948884
orientation_covariance:
- 0.0
- 0.0
- 0.0
- 0.0
- 0.0
- 0.0
- 0.0
- 0.0
- 0.0
angular_velocity:
  x: -0.00013597855247901325
  y: 0.0006306135617081868
  z: -0.00015794894627685146
angular_velocity_covariance:
- 4.0e-08
- 0.0
- 0.0
- 0.0
- 4.0e-08
- 0.0
- 0.0
- 0.0
- 4.0e-08
linear_acceleration:
  x: 0.08679200038530369
  y: 0.07753419258567491
  z: 9.687910969061628
linear_acceleration_covariance:
- 0.00028900000000000003
- 0.0
- 0.0
- 0.0
- 0.00028900000000000003
- 0.0
- 0.0
- 0.0
- 0.00028900000000000003

```

用rqt可视化：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1664.webp)

##### 雷达Laser
本节我们来认识一个新的传感器，该传感器在自动驾驶、室内导航等应用非常多，比如扫地机器人上就是用的它作为感知环境的重要工具，该传感器是激光雷达。

激光雷达（Light Detection And Ranging）,缩写`LiDAR`，英文也叫laser,翻译一下叫——激光探测与测距。

激光雷达的原理也很简单，就像蝙蝠的定位方法一样，蝙蝠定位大家都知道吧，像下面这样子的回声定位。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1665.webp)

普通的单线激光雷达一般有一个发射器，一个接收器，发射器发出激光射线到前方的目标上，物品会将激光反射回来，然后激光雷达的接受器可以检测到反射的激光。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1666.webp)

通过计算发送和反馈之间的时间间隔，乘上激光的速度，就可以计算出激光飞行的距离，该计算方法成为TOF（飞行时间法Time of flight，也称时差法）。

除了TOF之外还有其他方法进行测距，比如三角法，这里就不拓展了放一篇文章，大家自行阅读。[激光三角测距原理详述](https://www.slamtec.com/cn/News/Detail/190)

目前市面上的激光雷达，几乎都是采用三角测距，比如思岚的：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1667.webp)

需要注意的是虽然只有一个发射器和一个接受器，激光雷达通过电机可以进行旋转，这样就可以达到对周围环境360度测距的目的。

五位数的长这样：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1668.webp)

四位数的长这样（咱们有一台）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1669.webp)

三位数的长这样（咱们也有）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1670.webp)

两位数的长这样

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1671.webp)

不要钱的长这样

仿真的，不要钱

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1672.webp)

因为激光雷达是属于射线类传感器，该类传感在在Gazebo插件中都被封装成了一个动态库`libgazebo_ros_ray_sensor.so`。

接着我们来看看LiDAR的话题消息接口`sensor_msgs/msg/LaserScan`。

雷达的数据结构有些复杂，但通过注释和名字相信你可以看的七七八八，看不懂也没关系，一般情况下我们不会直接的对雷达的数据做操作。

雷达的模型不需要collision，请删掉，否则会挡激光射出。

有了前面的经验，我们需要在URDF添加以下内容即可，但我们下载的是鱼香ROS添加好的，所以不用改了：

```xml
  <gazebo reference="laser_link">
      <sensor name="laser_sensor" type="ray">
      <always_on>true</always_on>
      <visualize>true</visualize>
      <update_rate>10</update_rate>
      <pose>0 0 0.075 0 0 0</pose>
      <ray>
          <scan>
            <horizontal>
              <samples>360</samples>
              <resolution>1.000000</resolution>
              <min_angle>0.000000</min_angle>
              <max_angle>6.280000</max_angle>
            </horizontal>
          </scan>
          <range>
            <min>0.120000</min>
            <max>3.5</max>
            <resolution>0.015000</resolution>
          </range>
          <noise>
            <type>gaussian</type>
            <mean>0.0</mean>
            <stddev>0.01</stddev>
          </noise>
      </ray>

      <plugin name="laserscan" filename="libgazebo_ros_ray_sensor.so">
        <ros>
          <remapping>~/out:=scan</remapping>
        </ros>
        <output_type>sensor_msgs/LaserScan</output_type>
        <frame_name>laser_link</frame_name>
      </plugin>
      </sensor>
    </gazebo>
```

可以看到:

1.  雷达也可以设置更新频率`update_rate`，这里设置为5

2.  雷达可以设置分辨率，设置为1，采样数量360个，最终生成的点云数量就是360

3.  雷达也有噪声，模型为`gaussian`

4.  雷达有扫描范围`range`，这里配置成0.12-3.5，0.015分辨率

5.  雷达的`pose`就是雷达的joint中位置的设置值

下面这个蓝色的就是激光雷达的光线覆盖范围：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1673.webp)

```python
ros2 topic list
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1674.webp)

```python
ros2 topic info /scan
ros2 topic echo /scan
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1675.webp)

接着我们尝试使用rviz2进行可视化激光雷达数据

添加和修改RVIZ2的如下：（通过LaserScan插件可以看到激光数据）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1676.webp)

相信你改完之后依然是看不到任何激光雷达的数据的，反看topic的echo出来的数据，不是0就是inf(无限大)，再看看gazebo你会发现，激光雷达并没有达到任何一个物体上。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1677.webp)

所以我们可以手动的给激光雷达周围添加一下东西，点击Gazebo工具栏的正方体，圆球或者圆柱，随意放置几个到我们激光雷达的最大扫描半径内。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1678.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1679.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1680.webp)

##### 超声波Ultrasonic
这玩意对于ROS2算法意义不是很大，控制组直接在MCU上实现即可，有需求可以学本节。 **（可以跳过本节）**

在实际的机器人开发过程中，我们可能会利用超声波传感器实现实时避障的功能，毕竟超声波的价格相较于激光雷达要便宜很多（便宜的几块钱）。

所以本节我们来说一下如何使用ROS2+Gazebo来仿真超声波传感器。

百科来一段：

超声波传感器是将超声波信号转换成其它能量信号（通常是电信号）的传感器。超声波是[振动频率](https://baike.baidu.com/item/%E6%8C%AF%E5%8A%A8%E9%A2%91%E7%8E%87/8068137)高于20kHz的机械波。它具有频率高、波长短、绕射现象小，特别是方向性好、能够成为[射线](https://baike.baidu.com/item/%E5%B0%84%E7%BA%BF/327964)而定向传播等特点。超声波对液体、固体的穿透本领很大，尤其是在阳光不透明的固体中。超声波碰到杂质或分界面会产生显著反射形成反射回波，碰到活动物体能产生[多普勒效应](https://baike.baidu.com/item/%E5%A4%9A%E6%99%AE%E5%8B%92%E6%95%88%E5%BA%94/115710)。超声波传感器广泛应用在工业、国防、生物医学等方面。

接着看看长什么样子：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1681.webp)

便宜的就长这样子，一共两个头，一个头用于发送波，一个头接收波。这个还稍微高级一点，带一个光敏电阻，可以为超声波数据做一些补偿。

超声波传感器原理是什么呢？

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1682.webp)

```bash
距离=(发送时间-接收时间)*速度/2Copy to clipboardErrorCopied
```

看了超声波的原理，你有没有发现和前面的激光雷达传感器是一样的，是的，所以超声波传感器插件和激光雷达传感器插件在Gazebo插件中是同一个：

```bash
libgazebo_ros_ray_sensor.so
```

超声波总要装在机器人身上某个位置，所以我们先添加一个关节和Joint，为了省事，link我们就只写个名字，你如果有需要可以按照前面的章节那样添加一下。

```xml
  <link name="ultrasonic_sensor_link" />

  <joint name="ultrasonic_sensor_joint" type="fixed">
    <parent link="base_link"/>
    <child link="ultrasonic_sensor_link"/>
    <origin xyz="0.07 0.0 0.076" rpy="0 0 0"/>
  </joint>
```

添加完了关节，我们就可以配置gazebo的插件了，gazebo插件配置如下

```xml
  <gazebo reference="ultrasonic_sensor_link">
    <sensor type="ray" name="ultrasonic_sensor">
      <pose>0 0 0 0 0 0</pose>

      <visualize>true</visualize>

      <update_rate>5</update_rate>
      <ray>
        <scan>

          <horizontal>
            <samples>5</samples>
            <resolution>1</resolution>
            <min_angle>-0.12</min_angle>
            <max_angle>0.12</max_angle>
          </horizontal>

          <vertical>
            <samples>5</samples>
            <resolution>1</resolution>
            <min_angle>-0.01</min_angle>
            <max_angle>0.01</max_angle>
          </vertical>
        </scan>

        <range>
          <min>0.02</min>
          <max>4</max>
          <resolution>0.01</resolution>
        </range>

        <noise>
          <type>gaussian</type>
          <mean>0.0</mean>
          <stddev>0.01</stddev>
        </noise>
      </ray>
      <plugin name="ultrasonic_sensor_controller" filename="libgazebo_ros_ray_sensor.so">
        <ros>

          <remapping>~/out:=ultrasonic_sensor_1</remapping>
        </ros>

        <output_type>sensor_msgs/Range</output_type>

        <radiation_type>ultrasound</radiation_type>

        <frame_name>ultrasonic_sensor_link</frame_name>
      </plugin>
    </sensor>
  </gazebo>

```

添加完成后就可以编译测试下代码

```python
colcon build --packages-select fishbot_description
source install/setup.bash
ros2 launch fishbot_description gazebo.launch.py
```

没有物体的前面可以放个东西

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1683.webp)

打开终端，输入下面指令

```bash
ros2 topic list 
ros2 topic info /ultrasonic_sensor_1
ros2 topic echo /ultrasonic_sensor_1Copy to clipboardErrorCopied
```

不出意外可以看到下面的数据

```bash
header:
  stamp:
    sec: 4458
    nanosec: 1000000
  frame_id: ultrasonic_sensor_link
radiation_type: 0
field_of_view: 0.23999999463558197
min_range: 0.019999999552965164
max_range: 4.0
range: 2.6798219680786133
```

这里的range就是fishbot到墙之间的距离：2.67982

我们来讲一讲超声波传感器的数据类型`sensor_msgs/msg/Range`

```python

# ros2 topic info /ultrasonic_sensor_1
Type: sensor_msgs/msg/Range
Publisher count: 1
Subscription count: 0

```

你可以使用`ros2 interface show sensor_msgs/msg/Range`看到详细的解释，我们翻译一下

```python

# Single range reading from an active ranger that emits energy and reports

# one range reading that is valid along an arc at the distance measured.

# This message is  not appropriate for laser scanners. See the LaserScan

# message if you are working with a laser scanner.
#

# This message also can represent a fixed-distance (binary) ranger.  This

# sensor will have min_range===max_range===distance of detection.

# These sensors follow REP 117 and will output -Inf if the object is detected

# and +Inf if the object is outside of the detection range.

std_msgs/Header header # timestamp in the header is the time the ranger

                             # returned the distance reading

# Radiation type enums

# If you want a value added to this list, send an email to the ros-users list
uint8 ULTRASOUND=0
uint8 INFRARED=1

uint8 radiation_type    # 传感器射线类型

                        # (sound, IR, etc) [enum]

float32 field_of_view   # 距离数据对应的弧[rad]的大小，测量物体的范围介于         

                        # -field_of_view/2 到 field_of_view/2 之间。

                        # 0 角度对应于传感器的 x 轴。

float32 min_range       # 最小范围值 [m]
float32 max_range       # 最大范围值 [m]

                        #  固定距离需要 min_range==max_range

float32 range           # 范围数据 [m]

                        # (Note: values < range_min or > range_max should be discarded)

                        # Fixed distance rangers only output -Inf or +Inf.

                        # -Inf represents a detection within fixed distance.

                        # (Detection too close to the sensor to quantify)

                        # +Inf represents no detection within the fixed distance.

                        # (Object out of range)

```

结论，主要关注range就可以了。

在rviz2里添加超声波数据

Add ->By topic->Range

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1684.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1685.webp)

#### 搭建世界地图
本节我们要在Gazebo中建立一个测试的环境，其实也很简单，利用Gazebo的画墙工具即可完成。

world即世界，gazebo的world文件就是用于描述世界模型的，也就是环境模型。

Gazebo已经为我们准备了很多常用的物体模型，除了基础的圆球，圆柱，立方体外的，其实还有飞机、汽车、房子等你现实中无法拥有的。

但是一开始安装Gazebo的时候并不会帮你下载好这些模型，需要我们手动下载，找一个你要存模型的文件夹，打开终端，复制粘贴下面这句

```python
git clone https://github.com/osrf/gazebo_models
```

并把存放模型的路径加到~/.bashrc里（第二个冒号后面的可以不写，第二个冒号后面的是多个路径。）

GAZEBO_MODEL_PATH是老版Gazebo Classic的宏。

IGN_GAZEBO_RESOURCE_PATH是新版Igntion Gazebo的宏。

模型都通用，可以一起配置上。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1686.webp)

刷新环境变量

```python
source ~/.bashrc
```

此时再次打开终端，输入`gazebo`，把选项卡切换到Insert

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1687.webp)

在Insert选项卡下可以看到一个目录，以及目录下的模型名称，随着下载脚本的不断下载，这里的模型会越来越多。

随手拖几个，搭建一个漂亮的环境出来~

每个成功的男人都有一辆车，咱们也不例外

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1688.webp)

上面是Gazebo为我们准备好的开源模型，我们也可以通过Gazebo的工具来自己画一个环境。

然后也可以用墙壁工具建墙

Gazebo左上角->Edit->Building Editor

接着可以看到这样一个编辑界面

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1689.webp)

点击左边的Wall,你就可以在上方的白色区域进行建墙了，这个和模拟人生游戏不一样，这个是画二维的墙生成三维的墙，模拟人生是直接在三维里画。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1690.webp)

建完后还可以用选Add Color或者Add Texture，然后点击下方墙，给墙添加颜色或者纹理。

首先你要有一个地图，小鱼为你准备了两个，两个图片都是800\*600像素的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1691.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1692.webp)

打开Gazebo->Gazebo左上角->Edit->Building Editor->左下方选Import

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1693.webp)

将上面两个图片存到本地，在这个界面选图片，记着选Next

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1694.webp)

左边选尺寸对应关系

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1695.webp)

我们选择默认的，100像素/米。点击OK（需要手动将100改变一下才能点击OK哦），之后就可以用图片画墙了。

注意：导入完图片不会直接出来墙，图片只是提供了墙的大概位置，需要你手动用墙再将边描一遍。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1696.webp)

建完后点击File->Exit,在退出的弹框中选Exit。

接着在Gazebo界面中就可以看到墙了，我们再手动添加几个物体，就可以用于下面的导航使用了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1697.webp)

添加完，接着点击File->SaveWorld，将文件保存到我们的fishbot_descrption的world下。

没有world目录的小伙伴可以先手动创建下

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1698.webp)

加载world其实也很简单，可以先启动Gazebo，再手动的加载文件，也可以在Gazebo启动时加载：

比如在前面加载ROS2插件基础上再加载fishbot.world。

```python
gazebo --verbose  -s libgazebo_ros_init.so -s  libgazebo_ros_factory.so 你的world文件目录/fishbot.world
```

修改launch文件，将上面的命令行写到`gazebo.launch.py`中即可。

```python
    gazebo_world_path = os.path.join(pkg_share, 'world/fishbot.world')

    # Start Gazebo server
    start_gazebo_cmd =  ExecuteProcess(
        cmd=['gazebo', '--verbose','-s', 'libgazebo_ros_init.so', '-s', 'libgazebo_ros_factory.so', gazebo_world_path],
        output='screen')

```

下面是整个launch文件：

```python
from launch import LaunchDescription
from launch_ros.actions import Node

# 封装终端指令相关类
from launch.actions import ExecuteProcess

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
import os

def generate_launch_description():
    robot_name_in_model = 'fishbot'
    package_name = 'fishbot_description'
    urdf_name = "fishbot_gazebo.urdf"
    world_name = "fishbot.world"

    pkg_share = get_package_share_directory(f"{package_name}")
    urdf_model_path = os.path.join(pkg_share, f'urdf/urdf/{urdf_name}')
    gazebo_world_path = os.path.join(pkg_share, f'world/{world_name}')
    default_rviz_config_path = os.path.join(pkg_share, 'rviz/rviz2.rviz')

    # Start Gazebo server
    start_gazebo_cmd = ExecuteProcess(
        cmd=['gazebo', '--verbose','-s', 'libgazebo_ros_init.so', '-s', 'libgazebo_ros_factory.so',gazebo_world_path],
        output='screen')

    # Launch the robot
    spawn_entity_cmd = Node(
        package='gazebo_ros', 
        executable='spawn_entity.py',
        arguments=['-entity', robot_name_in_model,  '-file', urdf_model_path ], output='screen')

    # Start Robot State publisher
    start_robot_state_publisher_cmd = Node(
        package='robot_state_publisher',
        executable='robot_state_publisher',
        arguments=[urdf_model_path]
    )

    # Launch RViz
    start_rviz_cmd = Node(
        package='rviz2',
        executable='rviz2',
        name='rviz2',
        output='screen',
        arguments=['-d', default_rviz_config_path]
        )

    return LaunchDescription([start_gazebo_cmd,spawn_entity_cmd,start_robot_state_publisher_cmd,start_rviz_cmd])
```

最后记得修改cmakelists.txt文件，让编译后将world文件拷贝到install目录下

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1699.webp)

```python
colcon build
source install/setup.bash
ros2 launch fishbot_description gazebo.launch.py 
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1700.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1701.webp)

#### 框架优化
本节代码学长也发到仓库了，有需要的学弟学妹请看:https://github.com/tungchiahui/ROS_WS/tree/main/ROS2_WS%2F6.ws_simulations%2Fsrc%2Ffishbot_description

比如说支持xacro等优化。

首先先把原来的fishbot_gazebo.urdf里的内容分成好几个urdf和xacro再用一个总的fishbot.urdf.xacro去引用。（学过xacro的肯定都会）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1702.webp)

优化后的launch如下：

```python
from launch import LaunchDescription
from launch_ros.actions import Node

# 封装终端指令相关类
from launch.actions import ExecuteProcess

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
from launch.substitutions import Command,LaunchConfiguration
import os

def generate_launch_description():
    robot_name_in_model = 'fishbot'
    package_name = 'fishbot_description'

    # urdf_xacro_name = "fishbot_gazebo.urdf"
    world_name = "fishbot.world"

    pkg_share = get_package_share_directory(f"{package_name}")
    urdf_xacro_model_path = os.path.join(pkg_share, "urdf/xacro","fishbot.urdf.xacro")
    gazebo_world_path = os.path.join(pkg_share, f'world/{world_name}')
    default_rviz_config_path = os.path.join(pkg_share, 'rviz/rviz2.rviz')

    model = DeclareLaunchArgument(name="model", default_value=urdf_xacro_model_path)
    robot_description = ParameterValue(Command(["xacro ",LaunchConfiguration("model")]))

    # Start Gazebo server
    start_gazebo_cmd = ExecuteProcess(
        cmd=['gazebo', '--verbose','-s', 'libgazebo_ros_init.so', '-s', 'libgazebo_ros_factory.so',gazebo_world_path],
        output='screen')

    # Launch the robot
    spawn_entity_cmd = Node(
        package='gazebo_ros', 
        executable='spawn_entity.py',
        arguments=['-entity', robot_name_in_model,  '-topic', '/robot_description' ], output='screen')

    # Start Robot State publisher
    start_robot_state_publisher_cmd = Node(
        package='robot_state_publisher',
        executable='robot_state_publisher',
        parameters=[{"robot_description": robot_description}]
    )

    # Launch RViz
    start_rviz_cmd = Node(
        package='rviz2',
        executable='rviz2',
        name='rviz2',
        output='screen',
        arguments=['-d', default_rviz_config_path]
        )

    return LaunchDescription([model,start_gazebo_cmd,spawn_entity_cmd,start_robot_state_publisher_cmd,start_rviz_cmd])
```
```python
colcon build
source install/setup.bash
ros2 launch fishbot_description gazebo.launch.py
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1703.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1704.webp)

然后就可以去搞导航啦！也可以接着学新版Ignition Gazebo，但这东西教程巨少，截止2024年只有赵虚左老师讲了。
** 建议学习新的	Gazebo Harmonic （ROS2 Jazzy默认的版本） **