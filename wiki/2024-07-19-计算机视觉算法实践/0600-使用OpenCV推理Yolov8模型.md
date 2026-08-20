---
vinciId: 5da2b819-46e0-4250-a46d-352394225394
title: 使用OpenCV推理Yolov8模型（C++）
description: 概述
authors:
  - shangfanxing
contributors:
  - dongjiahui
  - cuitonghui
publishedAt: 2024-07-19T00:00:00.000Z
updatedAt: 2026-08-20T09:26:04.333Z
---

## 概述

众所周知，Yolo模型的推理和训练往往依托于PyTorch框架，语言为python，那么如果你使用的是opencv\-python的版本，就完全不需要关心该如何配置模型的推理，因为我们完全可以使工程环境中同时包含`opencv-pyhon`和`torch`，使用PyTorch框架负责\.pt模型进行推理，然后使用OpenCV去完成其他的工作，如根据模型的输出去绘制目标框和标签。

那么在C\+\+环境下，我们如何用OpenCV去使用Yolo模型，如何用GPU去做到对模型推理的加速，请见本文。

---

## **yolov8官方文档**

[https://github.com/ultralytics/ultralytics/]()

仅以yolov8为例，事实上官方给予了我们在C\+\+环境下使用opencv对yolov8模型推理的源码，就在工程源码当中：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/001-171e972148ea.webp)

ONNXRuntime也是一种常见的支持cpu或者gpu加速通用模型推理框架，是由微软开发的，可以对模型进行优化加快推理速度，\.onnx格式是通用模型格式，值得注意的是该框架目前还并不支持RAM架构的处理器，支持x64架构等，因此还没办法将该架构应用于工控机。

我们需要使用的就是YOLOv8\-CPP\-Inference中的程序。



## 导出ONNX格式模型

当我们使用Yolov8在PyTorch框架上完成对模型的训练，我们将在run/weights目录下得到了best\.pt和last\.pt，而这个格式是PyTorch框架的模型格式。

在OpenCV的C\+\+版本的dnn模块支持多种格式模型的推理（包括\.onnx格式但不包括\.pt格式），因此我们在使用之前，首先要将\.pt格式模型转换成\.onnx格式模型。



【**方式一**】

我们可以在我们的Yolov8环境中使用该脚本对模型格式进行转换：

```Python
from ultralytics import YOLO

# Load a model
model = YOLO(r"替换为模型位置")   # load a pretrained model (recommended for training)
success = model.export(format="onnx")  # export the model to ONNX format  #转换为onnx模型
print('model.export success')
```

运行此脚本，假如我有`boxs_bsaket.pt`，可得：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/002-96e5100fed33.webp)



【**方式二**】

如果你是使用pip指令安装的Yolov8环境，也可以在终端使用以下指令，导出\.onnx格式模型：

`yolo`` export model=best.pt imgsz=640 format=onnx opset=12`



## YOLOv8\-CPP\-Inference程序

这里只说使用时需要修改的地方`(红色标注)`，还有必要的知识`(蓝色标注)`：

【**main\.cpp**】

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/003-4ab356aa0cf1.webp)



**【Inference\.h**】

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/004-40a2d283b344.webp)



【**Inference\.cpp**】

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/005-b7c8549d0b76.webp)

## 测试

我使用官方模型和图片进行测试，运行程序获得：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/006-6bbb7358795a.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/007-79bc4bebca12.webp)



这里是推理的图片，如果你希望推理视频流，仅需要将main\.cpp稍微修改就好。

---

上述部分是使用C\+\+环境下OpenCV使用CPU进行推理的，如果你希望进行OpenCV使用GPU推理见下部分。



## OpenCV\_CUDA部署

OpenCV不默认支持GPU运行，也就说，如果你是在官网使用releases下载安装包安装，则默认不支持使用英伟达显卡加速。

如果你想进行GPU推理并且你的设备有INVIDA独显可以尝试，但是如果你的设备首次下载并不是在官网下载源码手动编译的话，就需要将您的OpenCV卸载，去到官网下载源码，进行手动编译安装，并在CMake安装过程中勾选CUDA选项。

1. Windows具体教程见：

[https://blog.csdn.net/yangyu0515/article/details/133671114?ops_request_misc=&request_id=&biz_id=102&utm_term=%E3%80%90opencv%E3%80%91%E3%80%90GPU%E3%80%91windows10&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-133671114.142^v100^pc_search_result_base6&spm=1018.2226.3001.4187]()

手动编译非常耗时，并且坑极多，如果要配置，请有耐心并能积极自主解决问题！！！

**再此补充一个BUG，在CUDA Toolkit \>= 12\.2\.0以上，OpenCV4\.8及以下，会出现 error C2666: 'operator \!=': overloaded functions have similar conversions 解决方法请参考**[**这里**](https://github.com/opencv/opencv/pull/23948)



2. Linux上部署OpenCV\_CUDA教程 :

请详看下方文档中的部署显卡驱动、CUDA、cuDNN、OpenCV等内容。

显卡驱动、CUDA、cuDNN部署教程:[Linux教程](/wiki/2024-03-30-linux-jiao-cheng)

OpenCV\_CUDA部署教程：[OpenCV CUDA环境搭建](/wiki/2023-12-10-ji-qi-ren-kai-fa-huan-jing-da-jian/0500-opencv-cuda-huan-jing-da-jian)



## GPU推理注意事项

如果你此时已经部署好了OpenCV\_CUDA，那么当你部署模型的时候，需要注意以下几个地方是和CPU部署不同的：

### \.onnx模型导出bug

如果你想使用OpenCV进行Yolov8模型的gpu推理，将不再能使用第二部分中【方法二】的方式导出模型，你只能Git yolov8的源码到工程中使用【方法一】，并且你需要更改Yolov8的源码，具体更改位置如下：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/19/008-c81746eb77e1.webp)



### main\.cpp中参数

`bool runOnGPU = true； `
