---
vinciId: 1bed1d96-3d44-4339-bb64-705c8275e114
title: slam建图(slamtoolbox)
description: 本文主要讲述如何利用slamtoolbox建图
authors:
  - liuyehan
contributors:
  - dongjiahui
publishedAt: 2023-12-30T00:00:00.000Z
updatedAt: 2025-08-26T12:00:11.000Z
---

本文主要讲述如何利用slamtoolbox建图

slamtoolbox是slam的一种算法，因为性能比较好\(但是点云需求貌似更高，有待验证\),所以本文以slamtoolbox作为slam算法进行建图，导航部分位于[Nav2导航](/wiki/2023-12-30-ros-dao-hang-yu-jian-tu-shi-jian/0200-nav2-dao-hang)

一、原理

SLAM是Simultaneous localization and mapping缩写，意为“同步定位与建图”，主要用于解决机器人在未知环境运动时的定位与地图构建问题。slam依赖激光雷达信息或类激光雷达信息，算法将雷达激光无法穿过的区域绘制为障碍物，而可穿过区域绘制为可通行区域，又依赖给出的机器人尺寸参数和预先设定的激进参数，绘制出试探性区域，从而完成slam绘图。

详可见

[https://blog.csdn.net/u010632165/article/details/119426739]()



二、slamtoolbox

这里主要介绍slamtoolbox的参数含义

![slamtoolbox所有launch文件\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/30/001-b77b007146d1.webp)

如图所示，slamtoolbox的官方launch文件共有五个，我们主要应用的是localization\_launch\.py和online\_async\_launch\.py,一个负责定位，一个是同步建图,最后我们可以自己写最适合项目的launch文件,包含行为树和状态机等。
