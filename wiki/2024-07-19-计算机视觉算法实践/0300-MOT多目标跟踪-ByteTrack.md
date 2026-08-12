---
vinciId: 751d8425-6cc5-4732-a46b-729ad96297eb
title: MOT任务-多目标跟踪算法（ByteTrack算法）
description: 概述
authors:
  - shangfanxing
contributors:
  - dongjiahui
publishedAt: 2024-07-19T00:00:00.000Z
updatedAt: 2024-09-11T14:23:13.000Z
---

## 概述

目标跟踪（Object Tracking）是机器人视觉中非常常见的任务，根据跟踪目标的数量的不同，目标跟踪可分为：

- 单目标跟踪（Single Object Tracking，SOT）

- 多目标跟踪（Multi\-Objects Tracking，MOT）

之前我们构建了结合Yolo detect模型的输出和Kalman Filter算法C\+\+环境下的单目标跟踪算法，本文将讲解如何利用Yolo detect模型的输出去在C\+\+环境下构建多目标跟踪的实现——ByteTrack算法。

---

## 前言

ByteTrack 算法是目前主流的解决MOT任务的算法之一，它的核心思想是：

1. 区分目标检测结果的高置信度检测框与低置信度检测框，对两者做不同的处理方式；

2. 在一定帧数范围内保留低置信度框数据，在后续帧的数据中可能低置信度数据可能会重新被确认，不会像其他算法一样直接删除。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/001-cdab55174164.webp)



![271478769\-93bb4ee2\-77a0\-4e4e\-8eb6\-eb8f527f0527\.gif](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/002-a6b4a417d285.webp)

`放大后可看到每个人的id`



ByteTrack 可以有效解决一些目标间发生遮挡带来的问题，且能够保持较低的 IDSwitch。因为目标会因为被遮挡检测置信度有所降低，当重新出现时，置信度会有所升高。可以想象：

当被跟踪的一个目标与另一个目标发生了遮挡关系，自身目标检测的置信度降低，可能导致被置信度阈值过滤掉，也就会导致可能会出现目标丢失的情况，该目标逐渐重现的时候，置信度增加被认为是一个新目标，实际上还是原来的目标，画面中并没有新目标出现，目标的身份就出现了错误。

而ByteTrack 算法就做到了：

1. 当目标逐渐被遮挡时，跟踪目标与低置信度检测目标匹配。

2. 当目标遮挡逐渐重现时，跟踪目标与高置信度检测目标匹配。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/003-032728fbec0a.webp)



![MOT17\-01\-SDP\.gif](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/004-6b7a613fa5da.webp)



常见的多目标跟踪算法还有如DeepSort、BOT\-Sort等，其中Deepsort算法应该是目前网络上讨论测试最多的多目标跟踪算法，我们简述一下ByteTrack和Deepsort算法的区别，以及为什么我要选择使用ByteTrack算法：

|- DeepSort算法：|该算法对被跟踪目标的外观特征描述要求很高，算法内需要构造特征提取神经网络，并构造画面中被检测目标的数据集，放入神经网络进行训练，构造该数据集麻烦，而且由于要在目标检测模型推理结束后再进行特征提取神经网络的运算\+复杂的匹配算法，所以计算量很大。对于被跟踪目标特征差异明显的效果比较好（比如人这个种类，不同人之间有明显差异化）这种差异的特征容易被特征提取神经网络提取，从而作为区别不同目标的依据。|
|---|---|
|- ByteTrack算法：|跟踪效果非常依赖于目标检测效果，如果目标检测效果好，跟踪能力就会比较好；算法不依赖于目标的特征，更适合ROBOCON这种道具外观几乎完全相同的情况；在目标遮挡跟踪方面有优越性。|





## 提前说明

Yolov8本身自带有多目标跟踪的实现，有两种算法选择：`ByteTrack`和`BOT-Sort`

源码位置如图：



![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/005-afc451bc3844.webp)

如果只是想测试一下ByteTrack 算法的效果，看到这里就可以了，可以直接配置好Yolov8环境，在工程目录下直接测试即可，教程见链接：

[https://docs.ultralytics.com/modes/track/]()



## 算法学习起点

### 学习`卡尔曼算法`和`匈牙利算法`

[SOT任务\-单目标跟踪算法（Kalman算法）](/wiki/2024-07-19-ji-suan-ji-shi-jue-suan-fa-shi-jian/0200-sot-dan-mu-biao-gen-zong-kalman)

[匈牙利算法](/wiki/2024-07-19-ji-suan-ji-shi-jue-suan-fa-shi-jian/0300-0100-xiong-ya-li-suan-fa-shu-ju-guan-lian)
