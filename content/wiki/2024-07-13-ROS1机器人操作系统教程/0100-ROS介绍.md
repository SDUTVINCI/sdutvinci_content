---
title: "ROS介绍"
---

### ROS介绍
### ROS与ROS2的关系
**ROS1最后一个****LTS****版本****ROS** **Noetic官方仅在Ubuntu20.04 Focal和Debian10 Buster提供二进制安装文件.且在2025年彻底停更。(有第三方的方式可以将ROS Neotic部署在Ubuntu 22.04 Jammy、Ubuntu 24.04 Noble以及Debian12 Bookworm等发行版上)**

**ROS2改进了ROS1初期设计的一些遗留毛病，以后会成为主流，但ROS2纯纯战未来，目前可以学，但是几乎没法应用，没啥教程。**

**ROS2目前主流的****LTS****版本如下:****ROS** **Humble官方仅在Ubuntu22.04 Jammy提供二进制安装文件，ROS Jazzy官方仅在Ubuntu24.04 Noble提供二进制安装文件。**

### ROS的教学资料
1.  教学视频

https://www.bilibili.com/video/BV1Ci4y1L7ZZ/

https://www.bilibili.com/video/BV1Ub4y1a7PH

2.  教学文档

http://www.autolabor.com.cn/book/ROSTutorials/

### 安装ROS1 Noetic
安装ROS2详见:[ROS2机器人操作系统教程](/wiki/2023-12-30-ros2-tutorial)

ROS1已经EOF了，来ROS2吧。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/07/13/image1.webp)

#### 官方途径
1.  Wiki安装首页：

https://wiki.ros.org/cn/noetic/Installation

##### 二进制包安装(仅支持Ubuntu20.04 Focal和Debian10 Buster，**更推荐该方式**)
1.  Ubuntu 20.04 Focal

https://wiki.ros.org/noetic/Installation/Ubuntu

2.  Debian10 Buster

https://wiki.ros.org/noetic/Installation/Debian

##### 根据官方源码，手动编译(不推荐该方式)
1.  支持的发行版：

https://www.ros.org/reps/rep-2000.html

2.  教程

    1.  Ubuntu 20.04 Focal和Debian10 Buster（多此一举：有二进制包安装，就不要从源码编译了）

    https://wiki.ros.org/noetic/Installation/Source

    3.  Arch Linux

    https://wiki.ros.org/noetic/Installation/ArchLinux

#### 第三方途径
##### 二进制包安装(**较为推荐**）
1.  支持Ubuntu 22.04 Jammy的方式：

https://rcbbs.top/t/topic/559

https://github.com/ganyuanzhen/ROS-on-Jammy

```
sudo add-apt-repository ppa:ros-for-jammy/noetic
sudo apt update
sudo apt install ros-noetic-desktop-full
```

2.  支持Ubuntu 24.04 Noble的方式：

https://rcbbs.top/t/topic/559

https://github.com/ganyuanzhen/ROS-on-Jammy

```bash
sudo add-apt-repository ppa:ros-for-jammy/noble
sudo apt update
sudo apt install ros-noetic-desktop-full
```

3.  支持Debian 12 Bookworm的方式：

https://gist.github.com/vrbadev/ec168a0940d45f523bf050011d7dff75

**从密钥服务器重新获取密钥** ： 你可以直接从密钥服务器上获取并保存这个密钥文件到正确的位置。首先，使用以下命令从密钥服务器获取密钥，并将其保存为 `.asc` 文件：

```bash
sudo wget -O /etc/apt/trusted.gpg.d/ros.asc https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc
```

这个Mavros相关的不用管，用不着飞控

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/07/13/image2.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/07/13/image3.webp)

##### Docker容器(不推荐，可供有需要的使用)
[Docker教程](/wiki/2024-10-03-docker-jiao-cheng)

https://gitee.com/qinyinan/amber-ce-bookworm

##### 根据源码，手动编译（不推荐）
1.  Ubuntu

https://zhuanlan.zhihu.com/p/688413327?utm_psn=1805362924497268736

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/07/13/image4.webp)

2.  Fedora

https://zhuanlan.zhihu.com/p/687755518?utm_psn=1823704798471520256

### 测试ROS1
1.  配置环境（只需配置一次即可）

```bash

# 打开文件并编辑
sudo vim ~/.bashrc
```

2.  在末尾加上下方命令（只需配置一次即可）

```bash
source /opt/ros/noetic/setup.bash
```

3.  在当前终端加载修改的文件（只需配置一次即可）

```bash

# 加载文件
source ~/.bashrc
```

4.  测试

打开一个终端，并敲下列命令打开Master

```bash
roscore
```

再打开一个终端，打开乌龟图形界面节点

```bash
rosrun turtlesim turtlesim_node
```

再打开一个终端，打开控制节点，鼠标选中放在该终端中，按键盘即可让🐢龟男🐢移动。

```bash
rosrun turtlesim turtle_teleop_key
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/07/13/image5.webp)
