---
title: "ROS环境搭建"
---

[ROS机器人操作系统教程](/wiki/2024-07-13-ros1-ji-qi-ren-cao-zuo-xi-tong-jiao-cheng)

[ROS2机器人操作系统教程](/wiki/2023-12-30-ros2-tutorial)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image61.webp)

ROS1已经EOF了，官方鼓励使用ROS2.

### 推荐的搭建环境
#### 方案一(推荐)
***ROS1=Kubuntu20.04*****（Noetic与20.04双双寿终正寝）**

***ROS2=Kubuntu22.04+***

**仍然建议使用22.04+Humble** Noetic，Humble，Jazzy这几个LTS之间主要有以下区别:

1.  Noetic是ROS1的LTS（长支持版），将于2025年5月31日EOF寿终正寝，自带算法比较老，依赖于其他非自带算法，但是教程最多，第三方算法支持也比较多。

2.  Humble是ROS2的第一个LTS，支持到2027年5月31日或海龟节，自带老版Gazebo Classic和新版Igntion Gazebo（独一份支持新旧两个Gazebo版本比较好的ROS版本），选择性比较广，自带算法比较新，教程正在逐渐增多中，第三方算法也在逐渐增多中，很多用ROS1的人都在往ROS2Humble上转。

3.  Jazzy是ROS2第二个LTS，支持到2029年5月31日或海龟节，在Humble的基础上，多了一些功能，但只有新版Gazebo Sim，针对Jazzy做ROS2教程的资料很少很少，而且很多第三方算法也并未针对Jazzy做相应版本，但是Jazzy目前来看兼容很多Humble的算法与教程，目前除了新版Gazebo改名需要改一些标记语言的标签外，没发现其他代码不兼容的地方。

#### 方案二(也是比较推荐，但是要多学个Docker)
1.  系统发行版:Fedora KDE（学长比较推荐，选一个自己喜欢的其他Linux发行版也行）

2.  ROS1Noetic安装方式:Docker

3.  ROS2Humble/Jazzy安装方式:Docker 详看[Docker教程](/wiki/2024-10-03-docker-jiao-cheng)

#### 方案三(可以用)
1.  系统发行版:Kubuntu 22.04 Jammy LTS / Kubuntu 24.04 Jammy LTS

2.  ROS1Noetic安装方式:西南交通大佬的ppa(详见上面ROS文档中，仅amd64(x86_64))

3.  ROS2Humble / ROS2Jazzy安装方式:官方二进制安装方式(详见上面的ROS2文档中)

#### 方案四(可以用)
1.  系统发行版:Windows10及以上

2.  WSL2发行版:Ubuntu24.04 Noble LTS

3.  ROS1Noetic安装方式:西南交通大佬的ppa(详见上面ROS文档中，仅amd64(x86_64))

4.  ROS2Jazzy安装方式:官方二进制安装方式(详见上面的ROS2文档中)
