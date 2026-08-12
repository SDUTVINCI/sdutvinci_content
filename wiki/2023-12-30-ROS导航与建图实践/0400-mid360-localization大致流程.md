---
title: mid360-localization大致流程
authors:
- liuyehan
publishedAt: '2023-12-30T00:00:00.000Z'
updatedAt: '2025-04-21T13:26:59.000Z'
---

实验室终于搞到一块mid360，怀着激动和忐忑的心情，让我们一起学习怎么使用吧！

# Ciallo～\(∠・ω\< \)⌒☆

注：文章写于本人刚刚完成mid360的定位和导航的实机实现，若有缺漏或错误的理解请联系我修改，我本人也会在进一步的使用中完善此文章。

我已将此项目整合至github，下载后删除devel和build文件夹重新catkin\_make即可（注意环境）

[https://github.com/MoMoxiaohan/lio_localization_ws]()

搭建平台：jetson agx xavier ubuntu20\.04 noetic/Legion Y9000p ubuntu20\.04 noetic

# 简介

Livox Mid\-360是一款性价比高、安全可靠的激光雷达传感器，适用于无人驾驶、机器人、智慧城市等领域。它支持建图、定位、识别、避障等功能。该激光雷达具有360°宽广的探测视场角，能够探测距离仅为0\.1米的物体\[1\]。Livox Mid\-360采用先进的光学机械系统，实现更远的量程、更高的点云密度和覆盖率，精确捕捉视场中的每个细节，适应性更强。用户可以通过Livox Viewer 2软件实时获取三维点云图像，也可以基于Livox SDK进行开发，轻松获取3D点云数据，以满足个性化的应用需求。Livox Mid\-360的最大探测距离可达100米。

# 驱动安装

一代驱动较为老旧，很多算法的包都开始使用二代，目前，二代驱动应该是会向使用一代的包兼容，所以这里统一用2代驱动

## mid360\-SDK2

[https://github.com/Livox-SDK/Livox-SDK2]()

原文中写的很清楚该怎么安装，这里不赘述了，大概就是

```Bash
mkdir build && cd build
cmake..
make -jx
sudo make install
```

## livox\-ros\-driver2

[https://github.com/Livox-SDK/livox_ros_driver2]()

这相当于ros\-SDK，需要git下来之后放在ws下的src内catkin\_make，第一次catkin\_make记得要执行包内的sh文件，以后如果在包内添加新节点或者修改源文件直接工作空间下catkin\_make即可\.

```Bash
cd ws/src/livox_ros_driver2
./build.sh ROS1#如果是ros2就写ROS2
```

安装完毕后要修改config下的ip地址为mid360广播的地址：ws/src/livox\_ros\_driver2/config/MID360\_config\.json

```JSON
{
  "lidar_summary_info" : {
    "lidar_type": 8
  },
  "MID360": {
    "lidar_net_info" : {
      "cmd_data_port": 56100,
      "push_msg_port": 56200,
      "point_data_port": 56300,
      "imu_data_port": 56400,
      "log_data_port": 56500
    },
    "host_net_info" : {
      "cmd_data_ip" : "192.168.1.50",
      "cmd_data_port": 56101,
      "push_msg_ip": "192.168.1.50",
      "push_msg_port": 56201,
      "point_data_ip": "192.168.1.50",
      "point_data_port": 56301,
      "imu_data_ip" : "192.168.1.50",
      "imu_data_port": 56401,
      "log_data_ip" : "",
      "log_data_port": 56501
    }
  },
  "lidar_configs" : [
    {
      "ip" : "192.168.1.1xx",#修改这里的xx为你激光雷达底座上的代码的最后两位
      "pcl_data_type" : 1,
      "pattern_mode" : 0,
      "extrinsic_parameter" : {
        "roll": 0.0,
        "pitch": 0.0,
        "yaw": 0.0,
        "x": 0,
        "y": 0,
        "z": 0
      }
    }
  ]
}
```

## 效果测试

我这里是rviz远程查看信息，如果你是直接在从机查看数据，直接运行launch文件就好

### 从机：

注释rviz\_MID360\.launch下的rviz相关node和param

或者将msg\_MID360下的    `<arg name="xfer_format" default="1"/>`改为0

这个参数的意义为

0：PointXYZRTLT

1：livox\_ros\_driver2::CustomMssg

2:   pcl::PointXYZI

运行：

```Bash
source ./devel/setup.bash
roslaunch livox_ros_driver2 rviz_MID360.launch
或roslaunch livox_ros_driver2 msg_MID360.launch
```

### 主机：

新建rviz\.launch

复制rviz相关节点到新建立的launch文件中

```Bash
source ./devel/setup.bash
roslaunch livox_ros_driver2 rviz.launch
```

观察得到mid360点云信息：



# 获取三维建图

要想由点云信息建立三维点云图就必须依赖建图算法，目前有的建图算法有fast\-lio fast\-lio2，point\-lio，lio\-sam，cartographer等，这里首先选择比较古老且安装较为轻松地fast\-lio，（point\-lio里程计更为好用，fast\-lio点云更密集，两者差不多，在下面的包里都有，会一个就能会另一个）

## 安装fast\-liolocalization

如果你要想单独安装fast\-lio或者其他建图包可以去找它的github然后git下来catkin\_make

[https://github.com/hku-mars/FAST_LIO]()

依赖包名记得改为livox\_ros\_driver2

这里直接下了一个别人改好的包

即上面提到的文章的github地址

文章使用了很多别的库，也是三维点云操作常用的库，这里一起下载：

```Bash
sudo apt install libeigen3-dev
sudo apt install libpcl-dev
# 所需包
sudo apt install ros-$ROS_DISTRO-ros-numpy
#下载python所需包之前注意有没有启动conda环境，切勿将pip用于根python环境，否则conda环境会报错
#如果你conda环境寄了，请备份env文件夹下的conda环境，然后卸载重装conda，再将环境移入env文件夹即可
pip install numpy==1.21
pip install open3d
sudo apt install ros-noetic-map-server
# 打开一个终端.(ctrl+alt+T)输入下面指令安装octomap.
sudo apt-get install ros-noetic-octomap-ros #安装octomap
sudo apt-get install ros-noetic-octomap-msgs
sudo apt-get install ros-noetic-octomap-server

# 安装octomap 在 rviz 中的插件
sudo apt-get install ros-noetic-octomap-rviz-plugins
# install move_base
sudo apt-get install ros-noetic-move-base


```

cmake报错的话要去cmakelists中指定python地址

open3d安装缓慢导致失败可以去换国内源

## 编译

注意编译顺序为livox\_ros\_driver2\-\>fast\_lio/point\_lio\-\>其他

这是因为作者引用了这些库里的文件，所以直接编译的话会报找不到文件的错，这种情况多编译几次就通过了

100%

## 开始建图

上面提到的参数确定为0，不然订阅不到点云信息

从机：

```Bash
source ./devel/setup/.bash
roslaunch livox_ros_driver2 msg_MID360.launch
roslaunch fast_lio mapping_mid360.launch
```

主机：

开启对应rviz

查看建图信息：

![2025\-01\-19 11\-19\-23 的屏幕截图\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/30/001-0c4ae8192f18.webp)

建图完毕后pcd文件会自动保存至fast\-lio下的PCD文件夹

安装pcl\-viewr以查看建好的pcd图

```Bash
sudo apt install pcl-tools
```

在PCD文件夹下：

```Bash
pcl_viewr ./scans.pcd
```

![2025\-01\-19 11\-47\-55 的屏幕截图\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/30/002-93f277471540.webp)

如果想要更改保存的pcd文件路径和名称，可以修改建图的源代码，在laserMpping\.cpp中修改相应路径即可

可以在fast\_lio文件夹下的config文件夹内更改mid360\.yaml选择是否保存pcd文件来只使用里程计而不保存pcd建图信息。

# 三维转二维

因为我们比赛主要用的是二维的定位和导航，所以我们将三维图压至二维图后，再应用相关算法进行导航，在此，有两种方式：

第一种：先建好图用pcd2pgm转为二维图

第二种：变建图边转：用octomap插件转为octomap并用map\-server保存二维地图

我们这里用第一种方式，octomap还可用于实时三维栅格地图生成，用于无人机导航

修改位于pcd2pgm下的run \.launch的pcd文件路径

```Bash
roslaunch pcd2pgm run.launch
```

用rviz查看建图信息并用mapserver保存地图

# 定位

## 统计定位需要的所有信息：

1、建图得到的pcd文件

2、里程计信息

3、实时点云信息

4、转换得到的二维地图信息

5、三维转二维雷达的点云信息

6、完整的tf树

事实上，对于全部的路径规划，我们都要有：

map odom body tf\(map\-\>odom\) scan

只要打通这些数据间的联系，就能运行move\_base

## 按照实际情况修改定位launch文件sentry\_localize\.launch

## 运行launch文件得到定位结果

[定位\.mp4](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/30/%E5%AE%9A%E4%BD%8D.mp4)

# 导航

这里用的dwa局部规划器，如果能找到更好用的规划器就最好了

修改相应的move\_base\.launch的坐标系和话题名称

```Bash
roslaunch sentry_nav sentry_move_base.launch
```

得到代价地图：

略

在rviz上发布坐标点：

这是rviz2\(

![Screenshot\_20250421\_212345\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/30/003-4ba5a72e4f80.webp)



得到路径和速度信息

```Bash
rostopic ehco /cmd_vel
```



# 总结

mid360是集成了imu的三维激光雷达，对我们研究自动驾驶有重要意义。学习全流程需要有较好的ros基础，并不断巩固和学习。

参考资料：

[https://blog.csdn.net/weixin_52612260/article/details/134124028]()

[https://github.com/66Lau/NEXTE_Sentry_Nav]()

难点在于局部规划器的调参和里程计到机器人的坐标变换，其实我一直想实现伪导航，可以在电控忙的时候自己在上位机用pid自己调一调试试。
