---
vinciId: 93a9adb8-7ec9-5022-8f4d-8cc4d4b2f9af
title: Linux
chapterDepth: 0
chapterOrder: "0300"
date: 2026-04-17
docKey: 2026-04-17-stm32cubeide-vscode-huan-jing-da-jian
docRoot: /wiki/2026-04-17-stm32cubeide-vscode-huan-jing-da-jian
docTitle: STM32CubeIDE VScode环境搭建
isWikiDoc: true
isWikiIndex: false
wikiDepth: 1
---

### 环境介绍

本教程环境介绍：

1. 系统：Fedora 43 KDE Edition Linux
2. 系统内核：Linux 6.19.12-200.fc43.x86\_64
3. 架构：X86\_64(amd64)

其他Linux环境也可以。

### 安装各种软件与环境

#### 安装CubeMX

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image9.webp)

下载地址：

<https://www.st.com.cn/zh/development-tools/stm32cubemx.html>

**推荐下载6.14.1版本（不要下载6.15.0,这个版本有bug，不知道后续何时会修复）**

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image10.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image11.webp)

解压出来

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image12.webp)

用root权限打开这个软件`SetupSTM32CubeMX-6.15.0`

```cpp
sudo ./SetupSTM32CubeMX-6.15.0
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image13.webp)

在新弹出的界面一直点下一步就行，安装结束后出现如下图就成功了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image14.webp)

`/usr/local/STMicroelectronics/STM32Cube/STM32CubeMX`进入这个文件夹，然后打开终端输入

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

<https://code.visualstudio.com/Download>

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

然后可以配置一个环境单独给CubeIDE插件使用，避免和默认环境冲突。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776420070033.webp)

进行一些设置，按我的来就可以

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776420416232.webp)

选中STM32

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776420490177.webp)

然后安装一些插件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image27.webp)

找到下面这个`STM32CubeIDE for Visual Studio Code`插件安装

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776420601935.webp)

右边弹这个提示要选择安装（要有良好的*科学网络*）

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776420688705.webp)

紧接着会进行一些环境的安装

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776420710467.webp)

也可以再安装一些其他的插件，比如Codex等插件
这些看你自己啦

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776420944068.webp)

### 工程创建与测试

#### 使用CubeMX创建工程

点击进入单片机挑选的按钮

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image60.webp)

搜索对应芯片，并双击对应芯片选项。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image61.webp)

进行一些配置，以下都是很基础的东西，你在看这个视频前肯定都会了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image62.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image63.webp)

随便开一个IO用来测试，比如LED的GPIO

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image64.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image65.webp)

FreeRTOS也要配置一下。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image66.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image67.webp)

这些文件夹也要配置好，最后Toolchain选择CMake,编译器选择GCC(6.14.1及之前没有选择编译器这个选项很正常)

（但是CubeMX6.15.0有bug,这个选择GCC编译器并没有用，还需要后续自己手动选择编译器，以后可能会修复这个bug.）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image68.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image69.webp)

#### 对工程进行配置与编译

在工程文件夹打开终端

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776421629435.webp)

```bash
code .
```

打开VScode后记得切到\`STM32·的配置

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776421689049.webp)

选择这里的Yes进行配置CMake预设

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776421794873.webp)

一般选Debug即可

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776421850499.webp)

找个C语言的代码文件打开，然后右下角会提示安装一个C/C++插件，这个可以安装也可以不安装，他也带代码提示，但是他的代码提示对比自带的clangd简直是弱爆了，如果你是新手，你不会设置代码提示，建议按我下面的操作来，直接别装这个插件。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776422028364.webp)

你可以测试一下代码提示，是不是很强。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776422393367.webp)

编译的话，图中的这俩build都可以

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776423543189.webp)

#### 移植作者tungchiahui的标准C/C++工程模板

用git clone命令克隆仓库:<https://github.com/tungchiahui/STM32HAL_CMake_CPP_Template>

```bash
git clone https://github.com/tungchiahui/STM32HAL_CMake_CPP_Template.git
```

把仓库里的 **所有文件与文件夹（除了**\*\*`.git`\*\***以外）** 复制到我们的STM32工程的目录里。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776427498924.webp)

然后打开applications文件夹，在Src和Inc文件夹分别创建led\_task.cpp和led\_task.h，内容分别如下:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image91.webp)

led\_task.cpp:

```cpp
#include "led_task.h"
#include "cmsis_os.h"
#include "stm32f1xx_hal.h" 

GPIO_PinState pinstate = GPIO_PIN_RESET;

extern "C"
void StartDefaultTask(void *argument)
{
  for(;;)
  {
    HAL_GPIO_WritePin(GPIOC,GPIO_PIN_13,pinstate);
    pinstate = (pinstate == GPIO_PIN_RESET) ? GPIO_PIN_SET : GPIO_PIN_RESET;
    osDelay(500);
  }
}
```

led\_task.h:

```cpp
#ifndef __LED_TASK_H_
#define __LED_TASK_H_

#ifdef __cplusplus
extern "C"
{
#endif

#include "cpp_interface.h"

#ifdef __cplusplus
}
#endif

#endif

```

然后打开`cmake/user`文件夹下的`CMakeLists.txt`，把刚才新建的led\_task.cpp添加上去。

详细介绍（可以不看）：这里的`cmake/stm32cubemx`下的`CMakeLists.txt`是被CubeMX管理的，你重新用CubeMX生成新代码后，这个文件里的东西会被覆盖。而工作区根目录下的`CMakeLists.txt`是不会被重新覆盖的，而且给我们留了一些区域加源文件和头文件，但是这样会让这个文件太过于嘈杂。所以我们选择新建一个user文件夹，然后在这里面弄一个`CMakeLists.txt`，再用顶层`CMakeLists.txt`去加载这个子`CMakeLists.txt`，这个子`CMakeLists.txt`方便咱们修改，文件结构也更加明显。（这些都不需要咱们自己创建，我已经给创建到**模板**里了，你在上面复制的时候已经复制过来了）

像下图这样加上cpp文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image92.webp)

然后要去最顶层的CMakeLists.txt里加上这句话来引用我们自己的CMakeLists.txt。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image93.webp)

```cmake

# Add USER generated sources
add_subdirectory(cmake/user)
```

大功告成，编译一次试试。可以看到下图，那些新加的文件都编译上了。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776429160105.webp)

#### 下载程序到板子

下载之前首先要先配置 :br

ST-Link就不用配置了，直接开始debug就完事了。

而Jlink等是需要配置的。

##### 配置调试器

###### ST-Link

无需任何配置

![alt text](https://cdn.sdutvincirobot.top/images/2026-04-17-STM32CubeIDE-VScode%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/1776435639185.png)

###### JLink

先安装jlink-gdbserver的bundle，如下图所示：

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776431910132.webp)

然后还要配置下launch：

问题解决方案：<https://community.st.com/t5/stm32cubeide-for-visual-studio/stm32h7a3vg-debugging-with-j-link-under-vscode/m-p/826188#M960>

在`.vscode`文件夹下创建一个`launch.json`，然后输入以下内容：

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "jlinkgdbtarget",
            "request": "launch",
            "name": "STM32Cube: STM32 Launch JLink GDB Server",
            "origin": "snippet",
            "cwd": "${workspaceFolder}",
            "preBuild": "${command:st-stm32-ide-debug-launch.build}",
            "runEntry": "main",
            "imagesAndSymbols": [
                {
                    "imageFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}"
                }
            ]
        }
    ]
}
```

完事

##### 进行调试：(这里linux可能会遇到一些usb权限的问题，请自行解决)

如果你是STLink应该是下图所示：

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776431995915.webp)

如果你是JLink应该是下图所示：

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776432039560.webp)

然后会出现这个条，他会下载程序到板子
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776432143180.webp)

然后就成功下载了程序并进入了Debug
