---
title: "各种Docker容器部署"
---

### 部署容器步骤
先从dockerhub拉取（docker pull）镜像，**然后再通过docker run命令创建容器即可。**（直接运行docker run命令也行，这会自己寻找本地镜像并创建，如果本地没有则会自动去dockerhub上寻找镜像并拉取创建容器一条龙服务。）

下面是各大容器拉取的命令（均支持amd64和arm64架构）：

### 各大容器拉取
#### Vinci机器人队暂时主使用的docker版本
**（该版本暂未构建上传到dockerhub，但是tungchiahui/ros-opencv:humble-411-cuda128-cudnn970-jammy已经实现了下列说的全部了）**

https://hub.docker.com/repositories/sdutvincirobot

https://github.com/SDUTVINCI/docker

使用以下带有CUDA和CuDNN的Docker必须满足的条件:

1.  有英伟达NVIDIA独立显卡

2.  显卡驱动必须满足≥570.86.10

3.  设备的架构必须为amd64(x86_64)架构或者aarch64(arm64)架构。(绝大多数设备均满足)

4.  支持的显卡型号如下:

    1.  GTX10系列桌面端、移动端显卡均已支持

    2.  RTX20-RTX50系列桌面端、移动端显卡均已支持

    3.  NVIDIA Jetson AGX Orin、NVIDIA Jetson Orin NX、NVIDIA Jetson Orin Nano工控机已支持

    4.  NVIDIA Jetson AGX Xavier、NVIDIA Jetson Xavier NX工控机已支持

    5.  其他显卡均未适配，强行使用其他显卡肯定会有不兼容的问题，如果想要适配你的显卡型号，请单独联系学长

该镜像包含的内容：

1.  Ubuntu22.04

2.  ROS2 Humble

3.  OpenCV4.11

4.  CUDA12.8

5.  CuDNN9.7.0

6.  cv_bridge(amd64支持，但arm64暂时没构建，请自行构建)

7.  Livox-SDK2

8.  (但无Livox-ROS-Driver2，自己在ws下编译吧)

请电控组成员在组长的允许下，变更该docker镜像内容，dockerfile和镜像均上传到github及dockerhub上了。

1.  从dockerhub上拉取镜像

```bash
docker pull sdutvincirobot/ros-opencv:humble-411
```

#### ROS+OpenCV纯CPU版本
https://hub.docker.com/repository/docker/tungchiahui/ros

https://github.com/tungchiahui/ros-docker/blob/main/README-zh_CN.md

1.  从dockerhub上拉取镜像

暂时主要维护ROS Humble的版本，其他版本随缘更新，但也基本都是非常够用的状态（随着战队主要使用的版本而变化）

```bash
docker pull tungchiahui/ros:noetic-focal

docker pull tungchiahui/ros:humble-jammy

docker pull tungchiahui/ros:jazzy-noble
```

#### （无ROS）OpenCV4.11+CUDA12.8+CuDNN9.7.0
https://hub.docker.com/repository/docker/tungchiahui/opencv

https://github.com/tungchiahui/ros-docker/blob/main/README-zh_CN.md

OpenCV4.11+CUDA12.8+CuDNN9.7.0：

（因为50系显卡最低要跑CUDA12.8,所以拉高门槛）

[https://pcnveplwrxf8.feishu.cn/sync/HtRPdZxPHsfwnwbXDsjcBfVcnah](https://pcnveplwrxf8.feishu.cn/sync/HtRPdZxPHsfwnwbXDsjcBfVcnah)

暂时主要维护Ubuntu Jammy的版本，其他版本随缘更新，但也基本都是非常够用的状态（随着战队主要使用的版本而变化）

```bash
docker pull tungchiahui/opencv:411-cuda128-cudnn970-focal

docker pull tungchiahui/opencv:411-cuda128-cudnn971-jammy

docker pull tungchiahui/opencv:411-cuda128-cudnn971-noble
```

#### ROS+OpenCV4.11+CUDA12.8+CuDNN9.7.0
https://hub.docker.com/repository/docker/tungchiahui/ros-opencv/general

https://github.com/tungchiahui/ros-docker/blob/main/README-zh_CN.md

1.  拉取镜像：

      ROS+OpenCV4.11+CUDA12.8+CuDNN9.7.0：

      （因为50系显卡最低要跑CUDA12.8,所以拉高门槛）

    使用以下带有CUDA和CuDNN的Docker必须满足的条件:

    1.  有英伟达NVIDIA独立显卡

    2.  显卡驱动必须满足≥570.86.10

    3.  设备的架构必须为amd64(x86_64)架构或者aarch64(arm64)架构。(绝大多数设备均满足)

    4.  cv_bridge(amd64支持，但arm64暂时没构建，请自行构建)

    5.  支持的显卡型号如下:

        1.  GTX10系列桌面端、移动端显卡均已支持

        2.  RTX20-RTX50系列桌面端、移动端显卡均已支持

        3.  NVIDIA Jetson AGX Orin、NVIDIA Jetson Orin NX、NVIDIA Jetson Orin Nano工控机已支持

        4.  NVIDIA Jetson AGX Xavier、NVIDIA Jetson Xavier NX工控机已支持

        5.  其他显卡均未适配，强行使用其他显卡肯定会有不兼容的问题，如果想要适配你的显卡型号，请单独联系学长

      暂时主要维护ROS Humble的版本，其他版本随缘更新，但也基本都是非常够用的状态随着战队主要使用的版本而变化）

```bash
docker pull tungchiahui/ros-opencv:noetic-411-cuda128-cudnn970-focal

docker pull tungchiahui/ros-opencv:humble-411-cuda128-cudnn970-jammy

docker pull tungchiahui/ros-opencv:jazzy-411-cuda128-cudnn971-noble
```
