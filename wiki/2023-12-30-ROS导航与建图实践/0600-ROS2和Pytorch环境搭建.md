---
title: ROS2+pytorch环境搭建和初步实验(conda版)
authors:
- liuyehan
publishedAt: '2023-12-30T00:00:00.000Z'
updatedAt: '2025-07-19T11:28:46.000Z'
---

本文将讲述如何在ros2中使用pytorch和scikit\-learn库进行机器学习和深度学习，所用机器最好含有独立显卡

cuda版本:12\.6

orin上有已经搭建好的环境，可以试着在自己电脑上再配置

# 一、前置需求

安装有anaconda/miniconda roshumble cuda12\.6 cudnn

# 二、环境搭建

使用python版本为3\.12,最高支持yolov11,后续再更新可以修改此文档

```Bash
conda create -n ros2_env python=3.12
conda activate ros2_env
#在自己电脑上，如果cuda环境无误的情况下
conda install torch
#在工控机上conda库中没有torch for aarchlinux，所以要用pip，附带换源操作,可以挂梯子使用官方源
pip3 install torch torchvision torchaudio --index-url https://mirrors.nju.edu.cn/pytorch/whl/cu12
#安装ros2所需所有库
pip install catkin_pkg empy lark setuptools
```

# 三、测试

```Bash
conda activate ros2_env
python3
import rclpy
import torch
print(torch.cuda.is_available())
```

以上操作不报错并且cuda可用就算环境配置成功

# 四、训练和预测

可以利用pytorch进行以下算法的实验

## 1、回归

## 2、分类

## 3、聚类

可以顺便配置yolo的环境

```Bash
pip install ultralytics
```
