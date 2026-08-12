---
vinciId: 40ca4c88-efdd-415b-a910-b978ce3d6ace
title: 如何在Windows上安装CUDA与CUDANN
description: 前提条件
authors:
  - cuitonghui
contributors:
  - shangfanxing
  - dongjiahui
publishedAt: 2024-07-19T00:00:00.000Z
updatedAt: 2024-12-15T06:20:53.000Z
---

## **前提条件**

在开始之前，请确保你满足以下前提条件：

1. 运行的是支持CUDA的NVIDIA GPU。

2. 安装了最新的NVIDIA驱动程序。



## **步骤 1：安装CUDA Toolkit**



3. 访问 [NVIDIA CUDA Toolkit 下载页面](https://developer.nvidia.com/cuda-downloads)。

4. 选择你的操作系统、架构、版本和安装类型（通常选择`exe (local)`）。

5. 下载并运行安装程序。

6. 在安装过程中，选择“Express”安装（推荐）。这将自动安装CUDA Toolkit及其所需的依赖项。



## **步骤 2：验证CUDA安装**



完成CUDA Toolkit安装后，验证CUDA是否正确安装。



7. 打开命令提示符（Cmd）或PowerShell。

8. 输入以下命令以检查CUDA版本：

```Bash
nvcc --version
```



输出应显示CUDA的版本信息，例如：

```Bash
nvcc: NVIDIA (R) Cuda compiler driver
   Copyright (c) 2005-2023 NVIDIA Corporation
   Built on Thu_Jan_26_19:01:24_PST_2023
   Cuda compilation tools, release 12.0, V12.0.140
```

## **步骤 3：下载并安装cuDNN**



9. 访问 [NVIDIA cuDNN 下载页面](https://developer.nvidia.com/cudnn)。

10. 登录你的NVIDIA账户。如果没有账户，需要注册一个。

11. 下载与你安装的CUDA版本对应的cuDNN版本。

12. 解压下载的压缩文件，将解压后的`bin`、`include`和`lib`目录下的所有文件复制到CUDA Toolkit安装目录中的相应目录中。通常，CUDA Toolkit的安装目录是：`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v<cuda_version>`

例如：

- 将`bin`文件夹中的文件复制到`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.0\bin`。

- 将`include`文件夹中的文件复制到`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.0\include`。

- 将`lib`文件夹中的文件复制到`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.0\lib\x64`。







## **步骤 4：验证cuDNN安装**



13. 打开命令提示符（Cmd）或PowerShell。

14. 导航到CUDA的bin目录：

```Bash
cd C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v<cuda_version>\extras\demo_suite
```

15. 输入以下命令以验证cuDNN是否正确安装：

```Bash
./bandwidthTest.exe
```



## **步骤 5：设置环境变量**



为了确保所有开发工具都能找到CUDA和cuDNN库，建议将CUDA的`bin`目录添加到系统的环境变量中。



16. 右键单击“此电脑”，选择“属性”。

17. 选择“高级系统设置”，然后点击“环境变量”。

18. 在“系统变量”部分，找到`Path`，然后点击“编辑”。

19. 点击“新建”，并添加以下路径：

    - `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v<cuda_version>\bin`

    - `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v<cuda_version>\libnvvp`
