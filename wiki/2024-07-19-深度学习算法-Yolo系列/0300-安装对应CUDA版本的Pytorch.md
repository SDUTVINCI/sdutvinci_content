---
vinciId: 0750cb27-d649-4974-955f-12bbf35709f7
title: 如何安装对应cuda版本的Pytorch
description: 这篇文章是如何在Windows上安装CUDA与CUDANN的后继，帮助你安装对应cuda版本的Pytorch
authors:
  - cuitonghui
contributors:
  - wangxiaohan
  - dongjiahui
publishedAt: 2024-07-19T00:00:00.000Z
updatedAt: 2024-12-21T05:54:36.000Z
---

这篇文章是[如何在Windows上安装CUDA与CUDANN](/wiki/2024-07-19-shen-du-xue-xi-suan-fa-yolo-xi-lie/0200-windows-an-zhuang-cuda-yu-cudnn)的后继，帮助你安装对应cuda版本的Pytorch

## **步骤 1：确定你的CUDA版本**



首先，你需要确定你系统中已经安装的CUDA版本。可以通过以下方式检查：



1. 打开命令提示符（Cmd）或PowerShell。

2. 输入以下命令：



    ```Bash
    nvcc --version
    ```



将看到类似如下的输出：

```Bash
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2023 NVIDIA Corporation
Built on Thu_Jan_26_19:01:24_PST_2023
Cuda compilation tools, release 12.0, V12.0.140
```



在这个例子中，CUDA版本是12\.0。



## **步骤 2：选择合适的PyTorch版本**



根据你的CUDA版本，选择与之兼容的PyTorch版本。可以通过访问 [PyTorch官网](https://pytorch.org/get-started/locally/) 并选择合适的选项来获得安装命令。



例如，如果有CUDA 12\.0，并且想要安装PyTorch的稳定版本，可以选择以下选项：

\- **PyTorch Build**: Stable \(默认选项\)

\- **Your OS**: Windows

\- **Package**: Pip \(或者你使用的其他包管理器，如Conda\)

\- **Language**: Python

\- **Compute Platform**: CUDA 12\.0



这将为你生成相应的安装命令。



## **步骤 3：通过pip安装PyTorch**



如果选择使用pip来安装PyTorch，你可以使用以下命令：



3. 打开命令提示符（Cmd）或PowerShell。

4. 输入以下命令来安装PyTorch及其对应的CUDA版本（假设CUDA版本为12\.0）：

    ```Bash
    pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu120
    ```



## **步骤 4：验证安装**



安装完成后，你可以通过以下代码验证PyTorch是否正确安装并与CUDA集成：



5. 打开Python解释器或创建一个新的Python脚本。

6. 输入以下代码：

    ```Python
    import torch
    print(torch.cuda.is_available())
    print(torch.cuda.get_device_name(0))
    ```



如果PyTorch与CUDA正确集成，`torch.cuda.is_available()` 将返回 `True`，并且 `torch.cuda.get_device_name(0)` 将输出你NVIDIA GPU的名称。
