---
title: "工作空间与功能包"
---

### 工作空间简介
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image222.webp)

工作空间里有4个子空间

未来编写的代码和脚本都需要人为的放入src空间里，

编译所形成的中间文件会存放到build空间里，

可执行文件会存放到install空间里，

编译过程以及运行之后各种警告，错误信息等会存放到log空间里。

* * *

用pip工具可以很方便的安装各种python的包

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image223.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image224.webp)

国内开发者@鱼香ROS 开发的一个专门处理ROS2依赖的工具

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image225.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image226.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image227.webp)

实际上就是扫描了各个功能包里的package.xml里的depend，然后查找本机是否含有该依赖，再决定是否安装。

```bash
sudo rosdepc init
rosdepc update
cd ..
rosdepc install -i --from-path src --rosdistro humble -y  #在src文件里看功能包所需依赖并查找安装
```

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image228.webp)

后面还需要创建好几个目录，这些目录大多都是和接口文件相关的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image229.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image230.webp)

### 源文件编译
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image231.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image232.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image233.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image234.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class MyNode: public rclcpp::Node
{
  public:
    MyNode():Node("node_name")
    {
      RCLCPP_INFO(this->get_logger(),"hello world!");
    }
};

int main(int argc,char *argv[])
{
  rclcpp::init(argc,argv);
  auto node = std::make_shared<MyNode>();
  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image235.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image236.webp)

实例化只能一个进程组织一个节点，

而继承可以组织多个节点。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image237.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image238.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image239.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image240.webp)

初始化和资源释放的作用是什么？

可以往context对象里放数据，也可以取数据，类似于FreeRTOS里的消息队列，但也不完全类似，它可以存储之前的数据。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image241.webp)

初始化不是仅仅只创建context对象，这是它的其中一个功能，它还有其他比较重要的功能。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image242.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image243.webp)

先初始化父类构造，并传入一个节点名称。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image244.webp)

智能指针忘记的可以看[Vinci机器人队C/C++资料](/wiki/2023-10-05-cplusplus-jiao-xue)

智能指针是一种自动管理内存的指针，它会在不需要对象时自动释放内存。使用智能指针可以避免内存泄漏和空悬指针等问题。

最安全的分配和使用动态内存的方法是调用一个名为 make_shared 的标准库函数。 此函数在动态内存中分配一个对象并初始化它，返回指向此对象的 shared_ptr。与智能指针一样，make_shared 也定义在头文件 memory 中。

当要用 make_shared 时，必须指定想要创建的对象的类型。定义方式与模板类相同， 在函数名之后跟一个尖括号，在其中给出类型：

```cpp
// 指向一个值为42的int的shared_ptr
shared_ptr<int> p3 = make_shared<int>(42);

// p4 指向一个值为"9999999999"的string
shared_ptr<string> p4 = make_shared<string>(10,'9');

// p5指向一个未初始化的int
shared_ptr<int> p5 = make_shared<int>();
//当然，我们通常用 auto 定义一个对象来保存 make_shared 的结果，这种方式较为简单：

// p6指向一个动态分配的空vector<string>
auto p6 = make_shared<vector>();
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image245.webp)

用make_shared可以分配堆区内存。

### 配置文件
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image246.webp)

C++

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image247.webp)

Python

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image248.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image249.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image250.webp)

name是功能包名称

version是包的版本

description是描述包的信息，也就是包是干嘛的

email是维护者的邮箱地址

license是我们的功能包使用的软件协议

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image251.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image252.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image253.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image254.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image255.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image256.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image257.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image258.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image259.webp)

不建议直接重命名功能包的名字，当修改了文件夹名称，则里面很多文件里的配置内容也需要被修改。建议重新建。

### 常用操作命令
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image260.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image261.webp)

ament_cmake是cmake的增强版

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image262.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image263.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image264.webp)

\-h 是查看帮助信息

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image265.webp)

```bash
ros2 pkg executables #输出当前系统可执行的功能包和节点
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image266.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image267.webp)

```bash
ros2 pkg executables 功能包名         #是输出当前包下可执行的功能包和节点
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image268.webp)

```bash
ros2 pkg list #是输出当前系统可执行的功能包
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image269.webp)

```bash
ros2 pkg prefix + 功能包名   #是输出该功能包的路径(重要，后面经常要用)
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image270.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image271.webp)

```bash
ros2 pkg xml + 功能包名   #是输出该功能包里的packages.xml的内容
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image272.webp)

### 核心模块_通信相关
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image273.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image274.webp)

通信模块被封装进了功能包

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image275.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image276.webp)

比如（例子）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image277.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image278.webp)

会搜到巨多的内容，可以用grep进一步搜索

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image279.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image280.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image281.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image282.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image283.webp)

下载分支用 -b

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image284.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image285.webp)

有个警告，因为有两个功能包都叫humble了

如果允许覆盖需要加参数 --allow-overriding

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image286.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image287.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image288.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image289.webp)

也就是helloworld那样的

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image290.webp)

### 核心模块_工具相关
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image291.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image292.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image293.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image294.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image295.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image296.webp)

命令行在某些地方比图形化工具比较好用，在远程登录时只能用命令行

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image297.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image298.webp)

Lanuch文件现在在ROS2里是一个Python文件了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image299.webp)

Camera是摄像头位置，Base_Link是车体的位置，Laser是激光雷达的位置。

雷达检测的距离信息，会转化成车体的位置信息。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image300.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image301.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image302.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image303.webp)

### 功能包
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image304.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image305.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image306.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image307.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image308.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image309.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image310.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image311.webp)

python是解析型语言，不用编译，但是需要setup.py文件，setup.py主要功能是把可执行文件移动到install的。

### ROS2技术支持
ROS2_Wiki官网:

http://wiki.ros.org/

ROS2_Wiki中文官网(ROS2维基百科现已支持简体中文):

http://wiki.ros.org/cn

ROS2简体中文社区：

http://wiki.ros.org/cn/community

ROS2插件索引网址:

https://index.ros.org/packages/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image312.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image313.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image314.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image315.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image316.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image317.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image318.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image319.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image320.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image321.webp)

### ROS2应用方向
许多ROS团队伴随ROS成长到今日，其规模已经发展到足以被认为是独立组织的程度了。在导航、机械臂、无人驾驶、无人机等诸多领域大放异彩，下面列出了其中的一些团队项目，这些项目对我们以后的进阶发展，也提供了指导。

ROS2社区：

https://www.ros.org/blog/community/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image322.webp)

* * *

#### **NAV2**
Nav2项目继承自ROS Navigation Stack。该项目旨在可以让移动机器人从A点安全的移动到B点。它也可以应用于涉及机器人导航的其他应用，例如跟随动态点。Nav2将用于实现路径规划、运动控制、动态避障和恢复行为等一系列功能。

NAV2官网：

https://navigation.ros.org/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image323.webp)

* * *

#### **OpenCV**
OpenCV（Open Source Computer Vision Library）是一个开源的计算机视觉和机器学习软件库。OpenCV旨在为计算机视觉应用程序提供通用基础架构，并加速机器感知在商业产品中的使用。OpenCV允许企业轻松地使用和修改代码。

OpenCV官网：

https://opencv.org/

教程：[OpenCV教程](/wiki/2026-04-13-opencv-jiao-cheng)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image324.webp)

* * *

#### **MoveIt**
MoveIt是一组ROS软件包， 主要包含运动规划、碰撞检测、运动学、3D感知、操作控制等功能。它可以用于构建机械臂的高级行为。MoveIt现在可以用于市面上的大多数机械臂，并被许多大公司使用。

https://moveit.ros.org/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image325.webp)

* * *

#### **The Autoware Foundation**
Autoware Foundation是ROS下属的非营利组织，支持实现自动驾驶的开源项目。Autoware基金会在企业发展和学术研究之间创造协同效应，为每个人提供自动驾驶技术。

TAF官网:

https://www.autoware.org/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image326.webp)

* * *

#### **F1 Tenth**
F1 Tenth是将模型车改为无人车的竞速赛事，是一个由研究人员、工程师和自主系统爱好者组成的国际社区。它最初于 2016 年在宾夕法尼亚大学成立，但后来扩展到全球许多其他机构。

F1 Tenth官网:

https://f1tenth.org/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image327.webp)

* * *

#### **microROS**
在基于ROS的机器人应用中，micro-ROS正在弥合性能有限的微控制器和一般处理器之间的差距。micro-ROS在各种嵌入式硬件上运行，使ROS能直接应用于机器人硬件。

MicroROS官网:

https://micro.ros.org/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image328.webp)

* * *

#### **Open Robotics**
Open Robotics与全球ROS社区合作，为机器人创建开放的软件和硬件平台，包括 ROS1、ROS2、Gazebo模拟器和Ignition模拟器。Open Robotics使用这些平台解决一些重要问题，并通过为各种客户组织提供软件和硬件开发服务来帮助其他人做同样的事情。

Open Robotics官网:

https://www.openrobotics.org/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image329.webp)

* * *

#### **PX4**
PX4是一款用于无人机和其他无人驾驶车辆的开源飞行控制软件。该项目为无人机开发人员提供了一套灵活的工具，用于共享技术并为无人机应用程序创建量身定制解决方案。

PX4官网:

https://px4.io/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image330.webp)

* * *

#### **ROS-Industrial**
ROS-Industrial是一个开源项目，将 ROS 软件的高级功能扩展到工业相关硬件和应用程序。

https://rosindustrial.org/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image331.webp)
