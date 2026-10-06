---
vinciId: beaa3992-40cb-4f11-b9fe-d64e257e321a
title: Linux环境搭建（新）
description: "***`（本教程为2026年9月创建的，可能与以后的版本有些出入）`***"
authors:
  - dongjiahui
publishedAt: 2026-10-06T09:22:17.225Z
updatedAt: 2026-10-06T09:22:17.225Z
---

***`（本教程为2026年9月创建的，可能与以后的版本有些出入）`***

### 环境介绍
本教程环境介绍：

1.  系统：Fedora 44 KDE Edition Linux

2.  系统内核：Linux 7.2.6-200.fc44.x86_64

3.  架构：X86_64(amd64)

其他Linux环境也可以。


### 需要准备的东西
1. 一台Linux电脑

2. 一块STM32F4及以上的板子(目前STM32F1使用MDK6比较麻烦)

3.  CubeMX最新版

4.  VScode最新版

5.  pyOcd（如何安装下方有教程）

6.  ST-Link驱动（如何安装下方有教程）


### 安装各种软件与环境

#### 安装CubeMX
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image9.webp)

下载地址：

https://www.st.com.cn/zh/development-tools/stm32cubemx.html

**推荐下载最新版本（此时最新版本是6.15.0）**

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image10.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image11.webp)

解压出来

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image12.webp)

用root权限打开这个软件`SetupSTM32CubeMX-6.18_1`（不建议）

```bash
sudo ./SetupSTM32CubeMX-6.18_1
```

（更建议：也可以不用root权限打开，但在选择安装路径的时候注意以下，自己改成`/home/xxx`里的某个文件夹里）

```bash
./SetupSTM32CubeMX-6.18_1
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image13.webp)

在新弹出的界面一直点下一步就行，安装结束后出现如下图就成功了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image14.webp)

`/usr/local/STMicroelectronics/STM32Cube/STM32CubeMX`（如果你不是root权限安装的，你的路径不是这个，需要选择对应的路径）进入这个文件夹，然后打开终端输入

```cpp
./STM32CubeMX
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image15.webp)

点击Help

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image16.webp)

选`Manage embedded software packages`，把STM32F1，F4，H7的第一个最新的固件勾上。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image17.webp)

点install

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image18.webp)

登陆上账号

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image19.webp)

然后等下载和安装完

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image20.webp)

下载好就行了。

接下来可以把CubeMX应用配置一个桌面快捷方式等可以快速打开，教程详见[Vinci机器人队Linux入门教程](/wiki/2024-03-30-linux-jiao-cheng/0600-qi-ta-ke-xuan-pei-zhi#appimage)的Appimage章节，可以用ctrl+F快速定位该章节。

桌面快捷方式如下：

（如果你不是root权限安装的，你的路径不是这个`/usr/local/STMicroelectronics/STM32Cube/STM32CubeMX/`，需要选择对应的路径）

```bash
[Desktop Entry]
Name=STM32CubeMX
Exec=/usr/local/STMicroelectronics/STM32Cube/STM32CubeMX/STM32CubeMX
Icon=/usr/local/STMicroelectronics/STM32Cube/STM32CubeMX/help/STM32CubeMX.png
Type=Application
Categories=Development;Electronics;Embedded;
Comment=STM32CubeMX configuration and code generation tool
Terminal=false
```

根据教程做，就可以实现这种效果啦。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image21.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image22.webp)


#### 安装VScode
https://code.visualstudio.com/Download

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image23.webp)

如果是debian系下载deb,如果是rhel系下载rpm.

下载完之后，点击浏览器，找到这个安装包的文件夹，并在该路径打开终端。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image24.webp)

Debian系：输入`sudo apt install ./code`然后按`tab`按键补齐文件名，回车。

RHEL系：输入`sudo dnf install ./code`然后按`tab`按键补齐文件名，回车。

例如补齐后的：

```bash
sudo dnf install ./code-1.102.1-1752598767.el8.x86_64.rpm
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image25.webp)

然后打开VScode，在终端输入下面的命令

```bash
code
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image26.webp)


然后可以配置一个环境单独给Keil Studio(MDK v6)插件使用，避免和默认环境冲突。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776420070033.webp)

进行一些设置，按我的来就可以。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790746466196-6a4b4127.webp)

创建后选配置：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790746491598-a2ba8e1c.webp)

选中他

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790746516609-c1e03243.webp)


#### 安装插件

搜索`keil studio pack`安装第一个即可

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790746693994-45ad4e57.webp)


### 环境配置

官方教程:https://mdk-packs.github.io/vscode-cmsis-solution-docs/installation.html

#### CubeMX路径配置:

打开一个终端:

```bash
find ~/ /usr/local /opt -type f -name STM32CubeMX 2>/dev/null
```

一般会输出你的CubeMX可执行文件的目录,比如:

```text
/usr/local/STMicroelectronics/STM32Cube/STM32CubeMX/STM32CubeMX
```

那么你复制`/usr/local/STMicroelectronics/STM32Cube/STM32CubeMX`即可,不要复制最后一个`STM32CubeMX`.

打开VScode设置:

搜索`CMSIS Solution Environment Variables`:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790754740299-66ae6315.webp)

添加完毕后关掉这个界面即可

### 创建一个简单的例程

#### 创建一个工程

点击侧边栏的`CMSIS`,如下图，
- 第一个是 创建`keil mdk6`工程
- 第二个是 把`keil mdk5`的工程转化为`keil mdk6`工程
- 第三个是浏览各种arm例程

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790746869807-2ea43778.webp)

选择`Create New Solution`后,选择 Device，然后搜索芯片，例如`stm32f407ig`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790748945865-5af58a97.webp)

`Templates`选择`CubeMX Basic solution`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790750394664-40418157.webp)

- `Solution Base Folder`：workspace 所在目录，可以包含多个 project/solution.(工程文件夹所在的目录)
- `Solution Sub Folder`：通常是 workspace 下的一个子目录。(工程文件夹名字)

我这里选择了`/home/tungchiahui/UserFolder/MySource/MDK6Projects/`存放工程。（你任选一个空的你自己创建的纯英文路径的文件夹存放即可）
然后工程名叫做`mdk6_test`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790750996238-86c08a1b.webp)

点击创建后VScode工作区会自动跳转到工程文件夹.

#### 添加许可

可能右下角会提示让你加许可,虽然咱没用`armclang(ac6)`,他也会要,但是实际上你不给许可也可以,但可以给个社区许可,照下图这样点击即可.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790751164769-f8068ca7.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790751207320-19e7b7b3.webp)

许可成功

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790751271889-9a94dca9.webp)

#### Pack安装

##### 安装CMSIS

先打开下面这个网站:

https://www.keil.arm.com/packs

搜索`CMSIS`,

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790751475734-9ecdf88c.webp)

复制`cpackget add`命令

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790751527200-87c2cccd.webp)

按`ctrl + ~`来开关VScode自带的终端,在终端里敲自己复制的那条命令,比如:

```bash
cpackget add ARM::CMSIS@6.3.0
```

他会开始猛猛下载:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790751645053-b88d44f9.webp)


这里键盘上按`A`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790751654385-fd2d4aac.webp)

安装完毕:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790751666419-14a1a6f7.webp)


##### 安装设备DFP

再打开下面这个网站:

https://www.keil.arm.com/devices

搜索选中你想要的设备,这里比如`STM32F407IGHx`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790749595498-4a2cb712.webp)

点击那个`CMSIS Pack`:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790749629848-9ebec443.webp)

复制`cpackget add`命令

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790749765582-ca1c4117.webp)

按`ctrl + ~`来开关VScode自带的终端,在终端里敲自己复制的那条命令,比如:

```bash
cpackget add Keil::STM32F4xx_DFP@3.1.1
```

他会开始猛猛下载:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790749862696-773b03ea.webp)


这里键盘上按`A`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790749714198-99faa178.webp)

安装完毕:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790749738489-ac8e3704.webp)

然后`Ctrl + Shift + P`搜索：

```text
developer:reload window
```

#### 打开CubeMX生成器

##### 图形化

直接点击:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790757181232-ffb96987.webp)

如果界面弹不出,实测`Arm CMSIS Solution 1.70.0`可以弹出来,而`Arm CMSIS Solution 1.72.0`弹不出来,这可能是个bug([Issue #550](https://github.com/Open-CMSIS-Pack/vscode-cmsis-solution/issues/550)),可以先降级或者直接用终端打开CubeMX.


##### 终端

出现下面这种问题,你只能在终端里打开CubeMX了:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790754816902-d21711c3.webp)

```bash
csolution CubeMX.csolution.yml run \
  -g CubeMX \
  -a STM32F407IGHx
```

#### 用CubeMX配置工程(这玩意不用教吧,自己配置吧)

顺利弹出CubeMX:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790754949023-e0a8e34e.webp)

接下来进行配置,

我这里主要配置了如下设置:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790755331564-b4fbfcf0.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790755345215-e5e4287e.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790755380157-7c80a3ee.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790755393977-6b3d2c0c.webp)

并开启了一个LED做测试

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790755464472-dc1bb359.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790755413257-d5e5bbf4.webp)

配置完毕后,注意这里的设置也要根据下图进行修改:

| 你在 MDK6/csolution 里选的编译器 | CubeMX 侧对应工具链 |
|---|---|
| `AC6` | `MDK-ARM` |
| `IAR` | `EWARM` |
| `GCC` | `STM32CubeIDE / Makefile / CMake` |

我这里选择`STM32CubeIDE`(因为官方例程就是拿`STM32CubeIDE`举的例子),我也只测试过`STM32CubeIDE`没问题,其他俩选项你自己自测,但其实没必要,选`STM32CubeIDE`就行.

而 ST 官方对 Generate Under Root 的定义是：
- ✅ 勾上：把 STM32CubeIDE 的工程文件直接扔到 CubeMX 项目根目录
- ❌ 不勾：生成独立的 STM32CubeIDE/ 工具链目录

所以我们不勾选.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790765660141-2c2d7237.webp)

这里会有个问题,在GCC编译器(只要你使用GCC/newlib + FreeRTOS)下,需要开启`configUSE_NEWLIB_REENTRANT`,所以咱们需要先去FreeRTOS设置里先设置一下:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790765952226-40d23e60.webp)


#### 下载固件与生成配置

点击`generate code`后,如果弹出来要你下载FW,你就选yes.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790755537530-84a80eb6.webp)

下面这个选`don't ask me again`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790755596677-660a4557.webp)

他会自动开始下载固件(看你网络环境如何了,一般裸连是可以的,必要时可以采取一些那什么手段)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790755624439-7506dea8.webp)

同意下许可

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790755949031-0c50f676.webp)

会自动开始解压

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790755962433-f7ceb5f1.webp)

这样代码就生成完毕了,点击`close`,并关闭CubeMX

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790756349512-b239e26f.webp)

#### 修改配置:

##### Arm Tools 环境配置

然后`Ctrl + Shift + P`搜索：

```text
Arm Tools: Configure Arm Tools Environment
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790747443747-5d11b38b.webp)

进入 **Arm Tools Environment Manager**。

至少安装：

```text
Arm CMSIS-Toolbox
MDK-Toolbox
GCC compiler for ARM CPUs
Kitware's CMake tool
Ninja Build
```

1. Arm CMSIS-Toolbox

找到`Arm CMSIS-Toolbox`，选择`2.15.0`，这个版本尽量可以先选这个，后续你明白这玩意是干啥的了，再选择其他版本。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790747620876-4986789f.webp)

弹出来的界面一直选`总是允许`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790747535225-b294f999.webp)

弹出来的这个`vcpkg-configuration.json`你用`Ctrl + S`帮他保存一下

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790747933728-ef57b00d.webp)

左下角这里会开始下载,当下载完毕会出现`Arm Tools:x`如下图:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790747971746-92526292.webp)

2. MDK-Toolbox

选最新版即可

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790763054589-20bdad6b.webp)

3. GCC compiler for ARM CPUs

> 不用`Arm Compiler for Embedded`的原因是`Arm Clang(AC6)`目前只支持C++17标准,而有很多C++20很爱好用的特性无法用,即便`Arm Clang(AC6)`很强,咱们还是放弃使用了,咱们用`Arm GCC`编译器.

选择最新版本即可，越高的版本，支持的C/C++标准越高.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790747820196-e42e9c22.webp)

然后同时可以关闭`Arm Compiler for Embedded`,咱们并不用`Arm Clang(AC6)`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790764675735-c30d1456.webp)

4. Kitware's CMake tool

选最新版即可

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790748384839-bb57b37d.webp)

5. Ninja Build

选最新版即可

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790748393076-952822d7.webp)


弹出来的这个`vcpkg-configuration.json`你用`Ctrl + S`帮他保存一下

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790764712706-80cef55a.webp)

##### solution的编译器选择

打开`CubeMX.csolution.yml`

把下面这个从`AC6`改成`GCC`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790765339737-8bab6e47.webp)

##### 

CubeMX 生成的 `startup_stm32f407ighx.s` 需要 CubeMX 自己那套 GCC linker script 里的这些符号，但 CMSIS-Toolbox 现在却用了它自动生成的默认 `gcc_linker_script.ld`。

官方 CMSIS-Toolbox 的 CubeMX 文档其实专门强调了这一点：

CubeMX 生成的 linker script 和 startup/system 初始化代码是强绑定的，应该使用 CubeMX 生成的 linker script。

而且官方对 GCC 的路径就是,`./STM32CubeMX/<target_name>/STM32CubeMX/<device_name>.ld`

打开`CubeMX.cproject.yml`,在最底下加上:

```yml
  linker:
    - script: ./STM32CubeMX/STM32F407IGHx/STM32CubeMX/STM32F407xx_FLASH.ld
      for-compiler: GCC
```

- 这个`linker`是和`components`以及`output`属于同一级别，也就是属于`project`的下一级。

- 这里的`./STM32CubeMX/STM32F407IGHx/STM32CubeMX/STM32F407xx_FLASH.ld`是CubeMX生成的`.ld`,你去看看具体是不是这个路径,文件名是不是这个,可能需要修改下,不能直接复制。

### 初次编译

接下来编译

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790766094330-692df709.webp)

成功编译

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790766720803-70bd76a3.webp)

### 配置成标准工程

#### 配置C/C++标准

打开`CubeMX.cproject.yml`,在最底下加上:

这俩是属于`project`的下一级：

我这里拿`C23`和`C++23`举例子，实际上你可能用不到那么高的标准，自行决定标准，我们也只用到`C++20`。

```yml
  # List the programming languages used in the project.
  language-C: c23
  language-CPP: c++23
```

以下是这俩选项具体可以填什么：

```text
language-C:
Documentation: https://open-cmsis-pack.github.io/cmsis-toolbox/YML-Input-Format/#language-c

Language standard to apply for compiling C source files.

For example c17, or gnu11.

Allowed Values:

c90
gnu90
c99
gnu99
c11
gnu11
c17
gnu17
c23
gnu23

Source: cproject.schema.json
```

```text
language-CPP:
Documentation: https://open-cmsis-pack.github.io/cmsis-toolbox/YML-Input-Format/#language-cpp

Language standard to apply for compiling C++ source files.

For example c++17 or gnu++17.

Allowed Values:

c++98
gnu++98
c++03
gnu++03
c++11
gnu++11
c++14
gnu++14
c++17
gnu++17
c++20
gnu++20
c++23
gnu++23

Source: cproject.schema.json
```

#### 配置STM32 C++标准工程

```bash
git clone 
```

把文件夹里的东西全部复制到工程目录`mdk6_test`：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790767627803-4feba6bf.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790767652163-13fd9ac9.webp)


打开`CubeMX.cproject.yml`:

我们要添加：

```text
add-path:
groups:
files:
```

`add-path`和`groups`是属于`project`的下一级

官方定义就是：
- add-path：添加 C/C++ include path
- groups：添加源码分组
- files：添加具体源码文件

比如咱们这里的工程：

```yml
 # List the include paths to be used by the compiler.
  add-path:
    - ./applications/Inc
    - ./bsp/boards/Inc

  # List the groups of source files to be compiled.
  groups:
    - group: applications
      files:
        - file: ./applications/Src/cpp_interface.cpp
    - group: bsp/boards
      files:
        - file: ./bsp/boards/Src/bsp_delay.cpp
```

写完保存下。

然后打开`main.c`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790768648331-6242382c.webp)

在这个地方加上：

```cpp
/* USER CODE BEGIN Includes */
#include "cpp_interface.h"
/* USER CODE END Includes */
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790768891625-5ff9be11.webp)

```cpp
  /* USER CODE BEGIN 2 */
  cpp_main();
  /* USER CODE END 2 */
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790768880281-0dc5e79d.webp)

然后打开`cpp_interface.cpp`，找到`isRTOS`：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790769064512-049bf4ca.webp)

给`isRTOS`改成1

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790769093513-f95643ca.webp)

编译一次试试：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790768938754-fbabb47e.webp)

#### 点亮一个灯

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790768963018-23e91802.webp)

复制这个`void StartDefaultTask(void *argument)`。

去`cpp_interface.cpp`做一个简单测试：

```cpp
#include "cpp_interface.h"
#include "cmsis_os.h"
#include "main.h"

void cpp_main(void)
{
#if isRTOS == 0  // 裸机开发
    for (;;)
    {
    }
#endif
}

extern "C"
void StartDefaultTask(void *argument)
{
    for (;;)
    {
        HAL_GPIO_TogglePin(LED_B_GPIO_Port , LED_B_Pin);
        osDelay(500);
    }
}
```

编译一次试试：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790769254166-1b85e963.webp)

### 配置Debugger

点击设置，找到`Debug Adapter for Target STM32F407IGHx`：

可以选择特别多的Debugger,比如`ST-Link`，`J-Link`，`CMSIS-DAP`等等。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/1790769581682-66b5e2d2.webp)
