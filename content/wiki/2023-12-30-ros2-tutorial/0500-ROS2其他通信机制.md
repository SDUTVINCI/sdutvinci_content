---
title: "ROS2其他通信机制"
---

### 概述
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image846.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image847.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image848.webp)

不同设备之间的通信，是通过分布式来实现的。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image849.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image850.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image851.webp)

镜像王八

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image852.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image853.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image854.webp)

### 分布式搭建
**场景**

在许多机器人相关的应用场景中都涉及到多台ROS2设备协作，比如：无人车编队、无人机编队、远程控制等等，那么不同的ROS2设备之间是如何实现通信的呢？

**概念**

分布式通信是指可以通过网络在不同主机之间实现数据交互的一种通信策略。

ROS2本身是一个分布式通信框架，可以很方便的实现不同设备之间的通信，ROS2所基于的中间件是DDS，当处于同一网络中时，通过DDS的域ID机制(ROS_DOMAIN_ID)可以实现分布式通信，大致流程是：在启动节点之前，可以设置域ID的值，不同节点如果域ID相同，那么可以自由发现并通信，反之，如果域ID值不同，则不能实现。默认情况下，所有节点启动时所使用的域ID为0，换言之，只要保证在同一网络，你不需要做任何配置，不同ROS2设备上的不同节点即可实现分布式通信。

**作用**

分布式通信的应用场景是较为广泛的，如上所述：机器人编队时，机器人可能需要获取周边机器人的速度、位置、运行轨迹的相关信息，远程控制时，则可能需要控制端获取机器人采集的环境信息并下发控制指令...... 这些数据的交互都依赖于分布式通信。

* * *

**实现**

多机通信时，可以通过域ID对节点进行分组，组内的节点之间可以自由通信，不同组之间的节点则不可通信。如果所有节点都属于同一组，那么直接使用默认域ID即可，如果要将不同节点划分为多个组，那么可以在终端中启动节点前设置该节点的域ID(比如设置为6)，具体执行命令为：

```bash
export ROS_DOMAIN_ID=6
```

上述指令执行后，该节点将被划分到ID为6的域内。

如果要为当前设备下的所有节点设置统一的域ID，那么可以执行如下指令：

```bash
echo "export ROS_DOMAIN_ID=6" >> ~/.bashrc
```

执行完毕后再重新启动终端，运行的所有节点将自动被划分到ID为6的域内。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image855.webp)

默认域ID是0，域ID不一样，无法互相通信。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image856.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image857.webp)

ID不一样就无法正常通信，和在ROS1内需要指定ROS Master一样。

**注意**

在设置ROS_DOMAIN_ID的值时并不是随意的，也是有一定约束的：

1.  建议ROS_DOMAIN_ID的取值在\[0,101\] 之间，包含0和101；

2.  每个域ID内的节点总数是有限制的，需要小于等于120个；

3.  如果域ID为101，那么该域的节点总数需要小于等于54个。

**DDS 域 ID 值的计算规则**

域ID值的相关计算规则如下：

1.  DDS是基于TCP/IP或UDP/IP网络通信协议的，网络通信时需要指定端口号，端口号由2个字节的无符号整数表示，其取值范围在\[0,65535\]之间；

2.  端口号的分配也是有其规则的，并非可以任意使用的，根据DDS协议规定以7400作为起始端口，也即可用端口为\[7400,65535\]，又已知按照DDS协议默认情况下，每个域ID占用250个端口，那么域ID的个数为：(65535-7400)/250 = 232(个)，对应的其取值范围为\[0,231\]；

3.  操作系统还会设置一些预留端口，在DDS中使用端口时，还需要避开这些预留端口，以免使用中产生冲突，不同的操作系统预留端口又有所差异，其最终结果是，在Linux下，可用的域ID为\[0,101\]与\[215-231\]，在Windows和Mac中可用的域ID为\[0,166\]，综上，为了兼容多平台，建议域ID在\[0,101\] 范围内取值。

4.  每个域ID默认占用250个端口，且每个ROS2节点需要占用两个端口，另外，按照DDS协议每个域ID的端口段内，第1、2个端口是Discovery Multicast端口与User Multicast端口，从第11、12个端口开始是域内第一个节点的Discovery Unicast端口与User Unicast，后续节点所占用端口依次顺延，那么一个域ID中的最大节点个数为：(250-10)/2 = 120(个)；

5.  特殊情况：域ID值为101时，其后半段端口属于操作系统的预留端口，其节点最大个数为54个。

上述计算规则了解即可。

```bash
域 ID 与节点所占用端口示意

Domain ID:      0
Participant ID: 0

Discovery Multicast Port: 7400
User Multicast Port:      7401
Discovery Unicast Port:   7410
User Unicast Port:        7411

---

Domain ID:      1
Participant ID: 2
Discovery Multicast Port: 7650
User Multicast Port:      7651
Discovery Unicast Port:   7664
User Unicast Port:        7665
```

Domain ID是指域ID

Participant ID是参与组ID，是指该域内的第几个节点

Discovery Multicast Port主发现端口，DDS规定，端口应从7400开始

User Multicast Port用户广播端口，

Discovery Unicast Port单播发现端口，7410是第一个节点开始使用的Discovery Unicast端口，因为DDS规定的，第11个端口才是第一个节点的Discovery Unicast端口。

User Unicast Port单播用户端口，7411才是第一个节点开始使用的User Unicast端口，因为DDS规定的，第12个端口才是第一个节点的User Unicast端口。

Discovery Unicast Port和User Unicast Port是第一个节点所占用的端口，所以一共占用了俩端口。

如果Domain ID不变还为0，Participant ID变成1的话，那么下一个节点的Discovery Unicast Port为7412，User Unicast Port为7413。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image858.webp)

以此类推。

一个Domain ID占用250个端口，所以当Domain ID为1的时候，Discovery Unicast Port应该是7650。

这是第三个节点，所以是7664和7665。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image859.webp)

实践：在树莓派5上跑一个ROS2 Jazzy开启键盘控制节点，然后在电脑实体机Linux(或者在Docker里跑也行，但要设置好网络)里跑一个ROS2 Humble，并打开乌龟显示节点。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image860.webp)

会发现是可以正常通信的。

### 工作空间覆盖
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image861.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image862.webp)

**没什么用，建议不要使用**

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image863.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image864.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image865.webp)

这个是和你的bashrc文件里，加载bash文件的顺序有关，谁最后加载，谁就会运行，也就是最高优先级。除了ROS2本身自带的bash，这个bash是不论在哪加载，都是最低优先级。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image866.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image867.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image868.webp)

### 元功能包
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image869.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image870.webp)

distro就是发行版的意思

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image871.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image872.webp)

\--build-type默认C++，

\--dependent默认无，

\--node-name本身是虚包，所以也无需设置。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image873.webp)

CmakeLists无需修改

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image874.webp)

先把12，13行删除

<exec_depend>xxxxxx</exec_depend>

所要依赖的功能包名

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image875.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image876.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image877.webp)

* * *

以后可能需要的元功能包：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image878.webp)

这个功能包是导航相关的

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image879.webp)

这个就是个元功能包

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image880.webp)

只有配置文件，没有其他实质性实现

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image881.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image882.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image883.webp)

元功能包的作用就是方便安装，把自己的东西打包，可以共享到ROS2社区。也方便安装别人的东西。

### 节点重名
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image884.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image885.webp)

而且节点名称都是一致的，图中有个<2>，这是操作系统给的标号，其他的操作系统是没有这个标号的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image886.webp)

```bash
rqt_graph
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image887.webp)

他只显示一个turtlesim，实际上我们是使用了两个turtlesim的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image888.webp)

这样虽然都显示了，但是是一模一样的名字，容易混淆，而且上面也给重名警告了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image889.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image890.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image891.webp)

要么起别名：王大宝，王小宝

要么加命名空间： 毛驴子家的王宝，李二狗家的王宝

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image892.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image893.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image894.webp)

```bash
ros2 run turtlesim turtlesim_node --ros-args --remap __ns:=/t1
```

ros2 run 功能包名 节点名 --ros-args --remap __ns:=/命名空间

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image895.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image896.webp)

```bash
ros2 run turtlesim turtlesim_node --ros-args --remap __name:=turtlesim2
```

ros2 run 功能包名 节点名 --ros-args --remap __name:=别名

or

ros2 run 功能包名 节点名 --ros-args --remap __node:=别名

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image897.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image898.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image899.webp)

```bash
ros2 pkg create cpp05_names --build-type ament_cmake --node-name demo01_names
```

节点名可以不设置，这里设置主要是为以后学习做铺垫。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image900.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image901.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image902.webp)

```cmake
install(DIRECTORY launch DESTINATION share/${PROJECT_NAME})
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image903.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image904.webp)

先导俩包

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image905.webp)

这个LaunchDescription对象里面呢是个列表，这个列表就是存储要启动的若干个节点。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image906.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image907.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(package="turtlesim",executable="turtlesim_node",name="t1")
    ])
```

package功能包名

executable节点名

name别名

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image908.webp)

```bash
ros2 launch cpp05_names demo01_names_launch.py
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image909.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image910.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image911.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image912.webp)

根标签是Launch

子集标签是node

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image913.webp)

```xml
<launch>
    <node pkg="turtlesim" exec="turtlesim_node" name="t1" />
</launch>
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image914.webp)

```bash
ros2 launch cpp05_names demo02_names_launch.xml 
```

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image915.webp)

yaml的根标签也是launch

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image916.webp)

```yaml
launch:
node:
  pkg: "turtlesim"
  exec: "turtlesim_node"
  name: "t1"
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image917.webp)

```bash
ros2 launch cpp05_names demo03_names_launch.yaml 
```

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image918.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image919.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image920.webp)

name别名

namespace命名空间

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image921.webp)

```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(package="turtlesim",executable="turtlesim_node",name="turtle1"),
        Node(package="turtlesim",executable="turtlesim_node",namespace="t1"),
        Node(package="turtlesim",executable="turtlesim_node",namespace="t1",name="turtle1")
    ])
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image922.webp)

```xml
<launch>
    <node pkg ="turtlesim" exec ="turtlesim_node" name ="turtle1" />
    <node pkg ="turtlesim" exec ="turtlesim_node" namespace ="t1" />
    <node pkg ="turtlesim" exec ="turtlesim_node" namespace ="t1" name= "turtle1" />
</launch>
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image923.webp)

```yaml
launch:
node:
  pkg: "turtlesim"
  exec: "turtlesim_node"
  name: "turtle"
node:
  pkg: "turtlesim"
  exec: "turtlesim_node"
  namespace: "t1"
node:
  pkg: "turtlesim"
  exec: "turtlesim_node"
  namespace: "t1"
  name: "turtle"
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image924.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image925.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image926.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image927.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image928.webp)

可指定命名空间

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image929.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class MyNode: public rclcpp::Node
{
  public:
    MyNode():Node("mynode_node_cpp","t1_ns")
    {
      RCLCPP_INFO(this->get_logger(),"Hello World!");
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  auto node = std::make_shared<MyNode>();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image930.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image931.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image932.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image933.webp)

### 话题重名
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image934.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image935.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image936.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image937.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image938.webp)

```bash
ros2 run turtlesim turtlesim_node --ros-args --remap __ns:=/t1
```

ros2 run 功能包名 节点名 --ros-args --remap __ns:=/命名空间

这种加命名空间的方式，不仅对节点重名生效，当然对话题名称依然生效。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image939.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image940.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image941.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image942.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image943.webp)

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard 
```

这个是打开控制机器人运动的节点

ros2 run teleop_twist_keyboard teleop_twist_keyboard

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image944.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image945.webp)

其所对应的话题名称是这个。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image946.webp)

但此时控制乌龟运动无效，是因为话题命名空间不同。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image947.webp)

乌龟接收的话题是/turtle1/cmd_vel，命名空间是/turtle1

而控制乌龟运动的是/cmd_vel，命名空间不同，

所以我们要把两者命名空间弄成一样的。

随便改即可，只要改成一样的，就可以正常通信了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image948.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image949.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image950.webp)

remappings可以实现话题的重映射，该参数是一个列表，然后里面是元组，每一个元组都可以对一个话题进行重映射，元组里第一个参数是原话题名称，第二个参数是重映射后的话题名称。

namespace可以实现命名空间。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image951.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image952.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image953.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image954.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image955.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image956.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image957.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image958.webp)

remp也是可以设置重映射，from是原话题名称，to是重映射的名称。

```xml
<launch>

    <node pkg ="turtlesim" exec ="turtlesim_node" namespace ="t1" />
    <node pkg ="turtlesim" exec ="turtlesim_node" >
        <remap from= "/turtle1/cmd_vel" to="/cmd_vel" />
    </node>
</launch>
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image959.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image960.webp)

launch:

```yaml
- node:
pkg: "turtlesim"
exec: "turtlesim_node"
name: "turtle"
- node:
pkg: "turtlesim"
exec: "turtlesim_node"
namespace: "t1"
- node:
pkg: "turtlesim"
exec: "turtlesim_node"
namespace: "t1"
name: "turtle"

node:
  pkg: "turtlesim"
  exec: "turtlesim_node"
  namespace: "t1"
node:
  pkg: "turtlesim"
  exec: "turtlesim_node"
  remap:
  -
      from: "/turtle1/cmd_vel"
      to: "/cmd_vel"
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image961.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image962.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image963.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image964.webp)

命名空间可以有好几级。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image965.webp)

全局话题是和节点命名空间平级，也就是挂载在根下的。

相对话题是挂载在命名空间下的。

私有话题是节点名称的子级。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image966.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image967.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image968.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image969.webp)

```cpp
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/string.hpp"

using std_msgs::msg::String;

class MyNode: public rclcpp::Node
{
  public:
    MyNode():Node("mynode_node_name","t1_namespace")
    {
      RCLCPP_INFO(this->get_logger(),"Hello World!");
      publisher_ = this->create_publisher<String>("/global_topics",10);
    }
  private:
    rclcpp::Publisher<String>::SharedPtr publisher_;
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  auto node = std::make_shared<MyNode>();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image970.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image971.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image972.webp)

全局话题

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image973.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image974.webp)

相对话题

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image975.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image976.webp)

私有话题

### 时间相关API
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image977.webp)

发消息可以有消息头，消息头里有时间戳，接收方解析消息头，并把消息时间和当前时间进行比对，看是否延迟过高。

Rate是频率

Time是时刻

Duration是持续时间

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image978.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image979.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image980.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image981.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image982.webp)

这个类的构造函数有两个重载，第一个是周期，第二个是频率。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image983.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image984.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image985.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

using namespace std::chrono_literals;

class MyNode: public rclcpp::Node
{
  public:
    MyNode():Node("time_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"Hello World!");
      demo_rate();
    }
  private:
    void demo_rate()
    {
      rclcpp::Rate rate1(500ms);
      rclcpp::Rate rate2(1.0);
      // while(rclcpp::ok())
      // {
      //   RCLCPP_INFO(this->get_logger(),"休眠500ms");
      //   rate1.sleep();
      // }
      while(rclcpp::ok())
      {
        RCLCPP_INFO(this->get_logger(),"休眠1000ms");
        rate2.sleep();
      }
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  auto node = std::make_shared<MyNode>();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image986.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image987.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image988.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image989.webp)

可以传入一个纳秒

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image990.webp)

也可以传入一个秒和一个纳秒

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image991.webp)

因为是int64_t类型的，所以我们后面加个L，这是5亿纳秒，也就是0.5秒。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image992.webp)

这样time2代表2.5秒。

获取当前时刻有两种方式，

一个是this->get_clock()->now()，

另一个是this->now();

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image993.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image994.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image995.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image996.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

using namespace std::chrono_literals;

class MyNode: public rclcpp::Node
{
  public:
    MyNode():Node("time_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"Hello World!");
      // demo_rate();
      demo_time();
    }
  private:
    void demo_rate()
    {
      rclcpp::Rate rate1(500ms);
      rclcpp::Rate rate2(1.0);
      // while(rclcpp::ok())
      // {
      //   RCLCPP_INFO(this->get_logger(),"休眠500ms");
      //   rate1.sleep();
      // }
      while(rclcpp::ok())
      {
        RCLCPP_INFO(this->get_logger(),"休眠1000ms");
        rate2.sleep();
      }
    }

    void demo_time()
    {
      rclcpp::Time time1(500000000L);
      rclcpp::Time time2(2,500000000L);
      rclcpp::Time right_now_1 = this->get_clock()->now();
      rclcpp::Time right_now_2 = this->now();

      RCLCPP_INFO(this->get_logger(),"s = %.2f , ns = %ld",time1.seconds(),time1.nanoseconds());
      RCLCPP_INFO(this->get_logger(),"s = %.2f , ns = %ld",time2.seconds(),time2.nanoseconds());
      RCLCPP_INFO(this->get_logger(),"s = %.2f , ns = %ld",right_now_1.seconds(),right_now_1.nanoseconds());
      RCLCPP_INFO(this->get_logger(),"s = %.2f , ns = %ld",right_now_2.seconds(),right_now_2.nanoseconds());
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  auto node = std::make_shared<MyNode>();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image997.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image998.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image999.webp)

和Time类似

但是不完全相同，这个duration用到了chrono。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1000.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1001.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1002.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1003.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1004.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1005.webp)

t2和t1可以进行相减，结果是一个duration类型的，但是不能相加。

time也可以和duration相加相减，结果是一个time。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1006.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1007.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1008.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1009.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1010.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1011.webp)

### 通信机制工具
#### 命令行
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1012.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1013.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1014.webp)

ros2 doctor是来检测系统网络状态、版本兼容性等状态的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1015.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1016.webp)

是通过了的，但是有几个警告：版本过低，不影响正常使用。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1017.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1018.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1019.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1020.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1021.webp)

参数服务端的本质还是服务端，所以也会列在Service Servers里。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1022.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1023.webp)

如果查看list发现有一些接口文件没显示，那说明你的这个终端的环境变量没刷新。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1024.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1025.webp)

proto比show更精简一些。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1026.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1027.webp)

pose是发送位姿的，会一直发数据。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1028.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1029.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1030.webp)

**想输出延时，消息必须有消息头。**

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1031.webp)

输出实时位姿

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1032.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1033.webp)

find和type是相反着来的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1034.webp)

消息发布频率是不断变动的，消息频率是可以通过定时器来控制，但是定时器是有误差的，并不是特别特别精准。当然还有网络也是一大影响因素。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1035.webp)

ros2 topic pub -r 发布消息的频率 话题名称 消息 具体的指令（要用json格式）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1036.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1037.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1038.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1039.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1040.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1041.webp)

clear是清除乌龟轨迹

kill是杀乌龟

reset是将乌龟位置重置

spawn是产卵，生成新乌龟

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1042.webp)

可以按Tab补齐

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1043.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1044.webp)

type和find是相反的

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1045.webp)

empty就是空，所以我们后面内容啥都不用写。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1046.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1047.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1048.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1049.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1050.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1051.webp)

可以加上-f或者-feedback打开连续反馈，这个连续反馈是航向角弧度

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1052.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1053.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1054.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1055.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1056.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1057.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1058.webp)

删除不是所有参数都能删除，这里提示不能删除静态类型参数。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1059.webp)

最大值255，最小取值0

步长1

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1060.webp)

可以显示在终端上，

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1061.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1062.webp)

也可以写入磁盘

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1063.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1064.webp)

当然也可以修改

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1065.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1066.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1067.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1068.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1069.webp)

也可以用ROS2 RUN来修改，--ros-args -p 后面跟 键:=值

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1070.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1071.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1072.webp)

ROS2 RUN也可以直接读取磁盘文件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1073.webp)

#### Rqt工具箱
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1074.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1075.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1076.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1077.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1078.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1079.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1080.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1081.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1082.webp)

这个是显示两个节点之间关系的。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1083.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1084.webp)

这个是用来发布消息的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1085.webp)

点击添加按钮

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1086.webp)

可以设置线速度和角速度，频率等

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1087.webp)

设置好参数后勾选

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1088.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1089.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1090.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1091.webp)

call /clear这个是清除轨迹的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1092.webp)

也可以产卵，设置好参数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1093.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1094.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1095.webp)

这个可以直接修改参数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1096.webp)

* * *

我们不能用rqt代替命令行，虽然rqt更方便，但是因为我们在工作中是远程控制机器人的，我们是通过terminal来远程控制机器人，所以，命令行很重要，这样不能使用rqt。
