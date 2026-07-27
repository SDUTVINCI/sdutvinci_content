---
title: "高级工具"
---

### vcs批量导入仓库
1.  安装

```bash

# debian系
sudo apt install python3-vcstool

# rhel系
sudo dnf install python3-vcstool

# pip3安装
pip3 install vcstool
```

2.  文件格式

文件扩展名为`.repos`或者`.yaml`，必须满足yaml格式，否则会报错。

如下，

repositories:是总标签

ros_ws是克隆完这个仓库，要把仓库里的文件放在哪一个文件夹的文件夹的名字。

type是仓库管理的类型，一般为git.

url是仓库地址

version是分支名

```YAML
repositories:
  ros_ws:
    type: git
    url: https://github.com/tungchiahui/ROS_WS.git
    version: main
  oepncv_projects:
    type: git
    url: https://github.com/tungchiahui/OpenCV_Projects.git
    version: main
```

以下是一个总示例：

```yaml
repositories:
  tungchiahui:
    type: git
    url: https://github.com/tungchiahui/tungchiahui.git
    version: main
  ros_ws:
    type: git
    url: https://github.com/tungchiahui/ROS_WS.git
    version: main
  oepncv_projects:
    type: git
    url: https://github.com/tungchiahui/OpenCV_Projects.git
    version: main
  stm32_projetcts:
    type: git
    url: https://github.com/tungchiahui/STM32_Projects.git
    version: main
  mdk6_template:
    type: git
    url: https://github.com/tungchiahui/CubeMX_MDK5to6_Template.git
    version: master
  serial_pack:
    type: git
    url: https://github.com/tungchiahui/Serial_Pack.git
    version: main
  ros-docker:
    type: git
    url: https://github.com/tungchiahui/ros-docker.git
    version: main
  CyberRobotROS:
    type: git
    url: https://github.com/CyberNaviRobot/CyberRobot_ROS2_Jazzy_WS.git
    version: main
  CyberRobotMCU:
    type: git
    url: https://github.com/CyberNaviRobot/STM32_FreeRTOS_MainController.git
    version: main

```

3.  如何使用？

把yaml文件放在某个你要存放大量仓库的文件夹下，敲入下方命令

```bash
vcs import < myrepos.yaml
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/29/image87.webp)

如下图，成功

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/29/image88.webp)

### Github代理
https://ghproxy.link/

### 搭建博客
使用github搭建自己的博客。

https://www.bilibili.com/video/BV1g68TzPEkh
