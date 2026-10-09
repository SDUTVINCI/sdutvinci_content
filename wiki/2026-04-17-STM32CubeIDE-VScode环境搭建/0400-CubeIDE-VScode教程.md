---
vinciId: 93a9adb8-7ec9-5022-8f4d-8cc4d4b2f9af
title: "插件使用教程"
---

### CubeMX安装FW

点击Help

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image16.webp)

选`Manage embedded software packages`，把STM32F1，F4，H7的第一个最新的固件勾上。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image17.webp)

点install

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image18.webp)

可以不用登录账号选`do not ask me again`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791521942252-f05003e7.webp)

然后等下载和安装完

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image20.webp)

下载好就行了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791522194817-41247684.webp)


### VScode安装插件

然后打开VScode，在终端输入下面的命令

```bash
code
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image26.webp)

然后可以配置一个环境单独给CubeIDE插件使用，避免和默认环境冲突。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776420070033.webp)

进行一些设置，自己按习惯来，不懂的暂时按我的来也行

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


### OpenOCD使用事项

<a id="openocd-parameters"></a>

#### 选择debugger和mcu型号

OpenOCD 一般通过 .cfg 配置文件确定：使用什么调试器 + 连接什么 MCU

```bash
openocd -f interface/调试器.cfg -f target/芯片系列.cfg
```

第一个参数：看你用什么调试器

| 调试器 | OpenOCD 配置 | 说明 |
|---|---|---|
| **ST-Link V2 / V2-1 / V3** | `"interface/stlink.cfg"` | STM32 最常见 |
| **DAPLink** | `"interface/cmsis-dap.cfg"` | DAPLink 实现 CMSIS-DAP |
| **CMSIS-DAP** | `"interface/cmsis-dap.cfg"` | 很多第三方调试器使用 |
| **J-Link** | `"interface/jlink.cfg"` | SEGGER J-Link |
| FTDI 类 JTAG/SWD | `"interface/ftdi/xxx.cfg"` | 根据具体硬件选择 |

第二个参数：看 STM32 哪个系列

| STM32 系列 | 常见型号示例 | OpenOCD Target |
|---|---|---|
| **STM32C0** | C011、C031、C071 | `"target/stm32c0x.cfg"` |
| **STM32F0** | F030、F072 | `"target/stm32f0x.cfg"` |
| **STM32F1** | **F103C8、F103RC** | **`"target/stm32f1x.cfg"`** |
| **STM32F2** | F205、F207 | `"target/stm32f2x.cfg"` |
| **STM32F3** | F303、F334 | `"target/stm32f3x.cfg"` |
| **STM32F4** | **F405、F407、F429、F446** | **`"target/stm32f4x.cfg"`** |
| **STM32F7** | F746、F767 | `"target/stm32f7x.cfg"` |
| **STM32G0** | G030、G070、G0B1 | `"target/stm32g0x.cfg"` |
| **STM32G4** | G431、G474 | `"target/stm32g4x.cfg"` |
| **STM32H5** | H503、H563、H573 | `"target/stm32h5x.cfg"` |
| **STM32H7** | **H743、H750、H745、H747** | **`"target/stm32h7x.cfg"`** |
| STM32H7RS | H7R3、H7S3 | `"target/stm32h7rsx.cfg"` |
| STM32L0 | L031、L073 | `"target/stm32l0.cfg"` |
| STM32L1 | L151、L152 | `"target/stm32l1.cfg"` |
| STM32L4 / L4+ | L432、L476、L496、L4R5 | `"target/stm32l4x.cfg"` |
| STM32L5 | L552、L562 | `"target/stm32l5x.cfg"` |
| STM32N6 | N657 等 | `"target/stm32n6x.cfg"` |
| STM32U0 | U031、U073 | `"target/stm32u0x.cfg"` |
| STM32U3 | U385 等 | `"target/stm32u3x.cfg"` |
| STM32U5 | U575、U585、U5A5 | `"target/stm32u5x.cfg"` |
| STM32WB | WB55 等 | `"target/stm32wbx.cfg"` |
| STM32WBA2 | WBA2xx | `"target/stm32wba2x.cfg"` |
| STM32WBA5 | WBA5xx | `"target/stm32wba5x.cfg"` |
| STM32WBA6 | WBA6xx | `"target/stm32wba6x.cfg"` |
| STM32WL | WL55、WLE5 | `"target/stm32wlx.cfg"` |
| **STM32C5** | C53、C54、C55、C56、C59、C5A | **见下方说明** |

STM32C5 要特别注意：

现在不要把 C5 写死成`target/stm32c5x.cfg`，
原因是 STM32C5 的 OpenOCD 支持非常新。2026 年 7 月才有 stm32c5x.cfg 支持补丁提交到 OpenOCD Gerrit；补丁里的文件名确实就是`target/stm32c5x.cfg`。
但是现在还没完全发行到OpenOCD，需要等一段时间。

#### 测试设备连接

我们用的`stm32f103c8`和`st-link`：

所以用

```bash
openocd -f interface/stlink.cfg -f target/stm32f1x.cfg
```

可以插上之后测试一波：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790822431371-9446f7ae.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791514972175-9fe9ca6a.webp)

照上图所示，已经连接成功了。

几个关键日志分别说明：

```text
Info : STLINK V2J48S7 ...
→ OpenOCD 正常识别 ST-Link。

Info : Target voltage: 3.150483
→ 目标板供电正常，约 3.15 V。

Info : SWD DPIDR 0x1ba01477
→ SWD 通信已经建立。

Info : [stm32f1x.cpu] Cortex-M3 r1p1 processor detected
→ 已经真正识别并连接 MCU 核心。

Info : target has 6 breakpoints, 4 watchpoints
→ Cortex-M3 的硬件断点/数据观察点正常。

Info : Examination succeed
→ Target 初始化成功。

最后：
Info : [stm32f1x.cpu] starting gdb server on 3333
Info : Listening on port 3333 for gdb connections
→ OpenOCD 的 GDB Server 已经正式启动，可以给 VS Code / STM32Cube 调试器连接了。
```

## 工程创建与测试

### 使用CubeMX创建工程
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

这里也打开。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791523019424-a5e98758.webp)

这些文件夹也要配置好，最后Toolchain选择CMake,编译器选择GCC(6.14.1及之前没有选择编译器这个选项很正常)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image68.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image69.webp)


### 对工程进行配置与编译

在工程文件夹打开终端（或者直接在此处打开VScode也行）

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776421629435.webp)

```bash
code .
```

打开VScode后记得切到`STM32·的配置

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776421689049.webp)

选择这里的Yes进行配置CMake预设

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776421794873.webp)

一般选Debug即可

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776421850499.webp)

可能谈一个很长的对话框，是这个`STM32Cube Core`提示的，你直接点yes开始下载（这是自动部署各种环境的）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791523340028-dcacecb6.webp)

反正右下角是`STM32Cube Core`提示让你安装的，你安就完了，每个版本提示可能不一样，总之安装就完了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791523505964-52b2aa1f.webp)

等上面的环境部署完毕后，

找个C语言的代码文件打开，然后右下角会提示安装一个C/C++插件，这个可以安装也可以不安装，他也带代码提示，但是他的代码提示对比自带的clangd简直是弱爆了，如果你是新手，你不会设置代码提示，建议按我下面的操作来，直接别装这个C/C++插件。

默认用的是clangd代码提示

你可以测试一下代码提示，是不是很强。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776422393367.webp)

编译的话，图中的这俩build都可以

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776423543189.webp)

### 移植作者tungchiahui的标准C/C++工程模板

用git clone命令克隆仓库:https://github.com/tungchiahui/STM32HAL_CMake_CPP_Template

```bash
git clone https://github.com/tungchiahui/STM32HAL_CMake_CPP_Template.git
```

把仓库里的 **所有文件与文件夹（除了`.git`以外）** 复制到我们的STM32工程的目录里。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776427498924.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791523805618-93a96d4e.webp)

main.c:

这里面加上如下：

```c
#include "cpp_interface.h"
```

在main函数里找合适位置加上如下函数：
一般裸机的话放在`while(1)`**上面**。
RTOS放在main()里仅靠`while(1)`的那几个RTOS相关的开启函数的**上面**。

```c
cpp_main();
```

然后要去最顶层的CMakeLists.txt里加上这句话来引用我们自己的CMakeLists.txt。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image93.webp)

```cmake
# Add USER generated sources
add_subdirectory(cmake/user)
```

在最顶上，也要把编程语言的标准改一下（这一步在`README.md`里写了）

```cmake
# Setup compiler settings
set(CMAKE_C_STANDARD 11 CACHE STRING "C language standard")
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS ON)

set(CMAKE_CXX_STANDARD 20 CACHE STRING "C++ language standard")
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS ON)
```

然后打开applications文件夹，在Src和Inc文件夹分别创建led_task.cpp和led_task.h，内容分别如下:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image91.webp)

led_task.cpp:

```cpp
#include "led_task.h"
#include "cmsis_os.h"
#include "stm32f1xx_hal.h" 

int32_t t;

extern "C"
void StartDefaultTask(void *argument)
{
  for(;;)
  {
    HAL_GPIO_TogglePin(GPIOC,GPIO_PIN_13);
    ++t;
    osDelay(500);
  }
}
```

led_task.h:

```cpp
#ifndef LED_TASK_H
#define LED_TASK_H

#include "cpp_interface.h"


#endif

```

然后打开`cmake/user`文件夹下的`CMakeLists.txt`，把刚才新建的led_task.cpp添加上去。

详细介绍（可以不看）：这里的`cmake/stm32cubemx`下的`CMakeLists.txt`是被CubeMX管理的，你重新用CubeMX生成新代码后，这个文件里的东西会被覆盖。而工作区根目录下的`CMakeLists.txt`是不会被重新覆盖的，而且给我们留了一些区域加源文件和头文件，但是这样会让这个文件太过于嘈杂。所以我们选择新建一个user文件夹，然后在这里面弄一个`CMakeLists.txt`，再用顶层`CMakeLists.txt`去加载这个子`CMakeLists.txt`，这个子`CMakeLists.txt`方便咱们修改，文件结构也更加明显。（这些都不需要咱们自己创建，我已经给创建到**模板**里了，你在上面复制的时候已经复制过来了）

像下图这样加上cpp文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image92.webp)

大功告成，编译一次试试。可以看到下图，那些新加的文件都编译上了。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776429160105.webp)

### 下载程序到板子

下载之前首先要先配置：

#### 配置原生gdb调试器（仅限St-link和J-link）

> 如果你不是stlink和jlink,接着往下看

按下图的来点击，你看看你是什么debugger,你就选哪个。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790606954240-b80cbe26.webp)

如果这里看不到对应设备，遇到权限问题，请查看[Linux 下 USB 权限问题](#usb-permissions)。

##### 原生ST-Link

可以更新下STlink的驱动：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790607581587-4d65b468.webp)

##### 原生JLink

先安装jlink-gdbserver的bundle，如下图所示：

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776431910132.webp)

#### 使用openocd服务驱动原生gdb调试器

当你要使用除了`ST-Link`和`J-Link`以外的调试器时：

先点调试，让他生成`launch.json`：

`openOCD接管`选`STM32Cube: STM32 Launch GDB`：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790823172983-802516ef.webp)

然后选`openocd`：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790823717750-d08ba3d3.webp)

过一会儿，肯定会调试失败，然后提示你`open launch.json`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790823682049-c5522e4c.webp)

然后再点`STM32Cube: STM32 Launch GDB`：


`launch.json`内容如下：

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "stgdbtarget",
            "request": "launch",
            "name": "STM32Cube: Launch Generic GDB Server",
            "origin": "snippet",
            "cwd": "${workspaceFolder}",
            "preBuild": "${command:st-stm32-ide-debug-launch.build}",
            "program": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
            "gdb": "${command:st-stm32-ide-debug-launch.get-gdb-executable}",
            "deviceName": "${command:st-stm32-ide-debug-launch.get-device-name}",
            "deviceCore": "${command:st-stm32-ide-debug-launch.get-core-name}",
            "deviceTrustzone": "${command:st-stm32-ide-debug-launch.get-trustzone-status}",
            "serverExe": "",
            "serverParameters": [],
            "serverHost": "localhost",
            "serverPort": "",
            "serverCwd": "",
            "runEntry": "main",
            "imagesAndSymbols": [
                {
                    "imageFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
                    "imageOffset": "",
                    "symbolFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
                    "symbolOffset": ""
                }
            ]
        },
        {
            "type": "stgdbtarget",
            "request": "attach",
            "name": "STM32Cube: Launch GDB Client",
            "origin": "snippet",
            "cwd": "${workspaceFolder}",
            "preBuild": "${command:st-stm32-ide-debug-launch.build}",
            "program": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
            "gdb": "${command:st-stm32-ide-debug-launch.get-gdb-executable}",
            "serverHost": "localhost",
            "serverPort": "${command:st-stm32-ide-debug-launch.get-server-port}",
            "runEntry": "main",
            "imagesAndSymbols": [
                {
                    "imageFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
                    "imageOffset": "",
                    "symbolFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
                    "symbolOffset": ""
                }
            ]
        }
    ]
}
```

我们需要改哪里呢？

主要是：
- `serverExe` ： 就填二进制程序名 `openocd`或者`openocd.exe`
- `serverParameters` ：填那两个openOCD参数，参考 [openocd的两个-f参数](#openocd-parameters)
- `serverHost` ：OpenOCD GDB Server 默认监听 localhost，一般不用改
- `serverPort` ：OpenOCD 默认 GDB 端口 为 `3333`
- `serverCwd` ： 填`serverExe`这个二进制程序在哪个目录底下
- 添加`liveWatch`参数（重要）

1. `serverExe`:

Linux填openocd
Windows填openocd.exe

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790826993170-f6f4832e.webp)

2. `serverParameters`按下面这个格式来：

那俩`-f`的参数由[openocd的两个-f参数](#openocd-parameters)可知：
- `interface/stlink.cfg`
- `target/stm32f1x.cfg`

但是除了这俩，还需要一些参数：

```json
            "serverParameters": [                
                "-f",
                "interface/stlink.cfg",

                "-c",
                "transport select swd",

                "-f",
                "target/stm32f1x.cfg",

                "-c",
                "$_TARGETNAME configure -gdb-max-connections 2"],
```

这里建议按上面这个顺序排布参数：先探针 → 再通信协议 → 再 MCU → 最后修改这个 MCU target 的参数

这里的`-c transport select xxx`是选协议：

| 实际调试方式 | 配置 |
|---|---|
| STM32 常用 SWD | `transport select swd` |
| JTAG | `transport select jtag` |

咱们一般都是`swd`,所以选`transport select swd`。

而这个`-c $_TARGETNAME configure -gdb-max-connections 2`是允许这个 OpenOCD target 同时接受最多 `2` 个 GDB 客户端连接。

正常调试已经占了一个：

```text
OpenOCD :3333
    ↑
    └── GDB #1
         普通 Debug
```

咱们还需要用到`Live Watch`，所以把参数设置为`2`之后：

```text
                  ┌─ GDB #1：普通 Debug
STM32 ← OpenOCD ──┤
                  └─ GDB #2：Live Watch
```


3. `serverCwd`：

用终端命令直接查找下：

```bash
# Linux
which openocd

# Windows
where.exe openocd
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790779741632-f3fe4580.webp)

Linux如上图是`/usr/bin/openocd`，所以`serverCwd`填`/usr/bin`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791515580660-37f3dfa4.webp)

Windows如上图是`C:\xpack-openocd-0.12.0-7\bin\openocd.exe`，所以`serverCwd`填`C:\xpack-openocd-0.12.0-7\bin`。
但是不能直接填`C:\xpack-openocd-0.12.0-7\bin`，因为在`json`中`\`是转义字符，所以傻逼Windows应该填`C:/xpack-openocd-0.12.0-7/bin`或者双反斜杠`C:\\xpack-openocd-0.12.0-7\\bin`。

4. 添加`liveWatch`参数

在`serverCwd`后面，`runEntry`前面加上：

```json
            "liveWatch": {
                "enabled": true,
                "samplesPerSecond": "4"
            },
```

最后：

**Linux：**

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "stgdbtarget",
            "request": "launch",
            "name": "STM32Cube: Launch Generic GDB Server",
            "origin": "snippet",
            "cwd": "${workspaceFolder}",
            "preBuild": "${command:st-stm32-ide-debug-launch.build}",
            "program": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
            "gdb": "${command:st-stm32-ide-debug-launch.get-gdb-executable}",
            "deviceName": "${command:st-stm32-ide-debug-launch.get-device-name}",
            "deviceCore": "${command:st-stm32-ide-debug-launch.get-core-name}",
            "deviceTrustzone": "${command:st-stm32-ide-debug-launch.get-trustzone-status}",
            "serverExe": "openocd",
            "serverParameters": [                
                "-f",
                "interface/stlink.cfg",

                "-c",
                "transport select swd",

                "-f",
                "target/stm32f1x.cfg",

                "-c",
                "$_TARGETNAME configure -gdb-max-connections 2"],
            "serverHost": "localhost",
            "serverPort": "3333",
            "serverCwd": "/usr/bin",
            "liveWatch": {
                "enabled": true,
                "samplesPerSecond": "4"
            },
            "runEntry": "main",
            "imagesAndSymbols": [
                {
                    "imageFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
                    "imageOffset": "",
                    "symbolFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
                    "symbolOffset": ""
                }
            ]
        },
        {
            "type": "stgdbtarget",
            "request": "attach",
            "name": "STM32Cube: Launch GDB Client",
            "origin": "snippet",
            "cwd": "${workspaceFolder}",
            "preBuild": "${command:st-stm32-ide-debug-launch.build}",
            "program": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
            "gdb": "${command:st-stm32-ide-debug-launch.get-gdb-executable}",
            "serverHost": "localhost",
            "serverPort": "${command:st-stm32-ide-debug-launch.get-server-port}",
            "runEntry": "main",
            "imagesAndSymbols": [
                {
                    "imageFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
                    "imageOffset": "",
                    "symbolFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
                    "symbolOffset": ""
                }
            ]
        }
    ]
}
```

**Windows：**

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "stgdbtarget",
            "request": "launch",
            "name": "STM32Cube: Launch Generic GDB Server",
            "origin": "snippet",
            "cwd": "${workspaceFolder}",
            "preBuild": "${command:st-stm32-ide-debug-launch.build}",
            "program": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
            "gdb": "${command:st-stm32-ide-debug-launch.get-gdb-executable}",
            "deviceName": "${command:st-stm32-ide-debug-launch.get-device-name}",
            "deviceCore": "${command:st-stm32-ide-debug-launch.get-core-name}",
            "deviceTrustzone": "${command:st-stm32-ide-debug-launch.get-trustzone-status}",
            "serverExe": "openocd.exe",
            "serverParameters": [                
                "-f",
                "interface/stlink.cfg",

                "-c",
                "transport select swd",

                "-f",
                "target/stm32f1x.cfg",

                "-c",
                "$_TARGETNAME configure -gdb-max-connections 2"],
            "serverHost": "localhost",
            "serverPort": "3333",
            "serverCwd": "C:/xpack-openocd-0.12.0-7/bin",
            "liveWatch": {
                "enabled": true,
                "samplesPerSecond": "4"
            },
            "runEntry": "main",
            "imagesAndSymbols": [
                {
                    "imageFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
                    "imageOffset": "",
                    "symbolFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
                    "symbolOffset": ""
                }
            ]
        },
        {
            "type": "stgdbtarget",
            "request": "attach",
            "name": "STM32Cube: Launch GDB Client",
            "origin": "snippet",
            "cwd": "${workspaceFolder}",
            "preBuild": "${command:st-stm32-ide-debug-launch.build}",
            "program": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
            "gdb": "${command:st-stm32-ide-debug-launch.get-gdb-executable}",
            "serverHost": "localhost",
            "serverPort": "${command:st-stm32-ide-debug-launch.get-server-port}",
            "runEntry": "main",
            "imagesAndSymbols": [
                {
                    "imageFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
                    "imageOffset": "",
                    "symbolFileName": "${command:st-stm32-ide-debug-launch.get-projects-binary-from-context1}",
                    "symbolOffset": ""
                }
            ]
        }
    ]
}
```

#### 如何进入调试

##### 显示1

如果你是下图这样，请按下面这样做：

如果不是，请往下滑看下面的显示2。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776431995915.webp)

点完`Run and Debug`后，如下图：
- `ST-Link`选`STM32cube: STM32 Launch STLink GDB Server`
- `J-Link`选`STM32Cube: STM32 LaunchJLink GDB Server`
- `openOCD接管`选`STM32Cube: Launch Generic GDB Server`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790823172983-802516ef.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776431995915.webp)

##### 显示2

如果你是下图这样，请按下面这样做：

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776432039560.webp)

你显示上面这样，可能是因为你已经有生成的`launch.json`了。（这样直接点击就行）

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776432039560.webp)

#### 进入调试

然后会出现这个条，他会下载程序到板子（仅J-link）
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776432143180.webp)

然后就成功下载了程序并进入了Debug

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1776432211211.webp)

按照下图所示，把你要监视的变量输入到最顶上的框里，就可以加入到实时监视框里了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791525244481-514bc00f.webp)

结构体也可以：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790608511630-b448c1d8.webp)


#### 更换调试器软件为`cortex debug`（可选，很没啥必要）：

##### 安装`cortex debug`插件

在VScode里搜索`cortex debug`并安装

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790773273575-136b328f.webp)


##### 进行`launch.json`的配置

新建`.vscode/launch.json`:

```json
{
    "version": "0.2.0",

    "configurations": [
        {
            // 调试配置名称
            "name": "STM32 - OpenOCD - Cortex Debug",

            // Cortex-Debug 固定写法
            "type": "cortex-debug",

            // 下载程序并开始调试
            "request": "launch",

            // 工程工作目录
            "cwd": "${workspaceFolder}",

            // ★ 必改：编译生成的 ELF
            "executable": "${workspaceFolder}/build/Debug/TEST.elf",

            // 使用 OpenOCD
            "servertype": "openocd",

            // ★ OpenOCD 可执行文件
            // Linux 下用 which openocd 查询
            "serverpath": "/usr/bin/openocd",

            // ★ 根据调试器和 MCU 修改
            //
            // ST-Link：
            // interface/stlink.cfg
            //
            // STM32F1：
            // target/stm32f1x.cfg
            //
            // STM32F4 则是：
            // target/stm32f4x.cfg
            "configFiles": [
                "interface/stlink.cfg",
                "target/stm32f1x.cfg"
            ],

            // ★ ARM GNU Toolchain 的 bin 目录
            // 建议不要直接写 "~"
            // 用 ${env:HOME} 更稳
            "armToolchainPath": "${env:HOME}/.local/share/stm32cube/bundles/gnu-tools-for-stm32/14.3.1+st.2/bin",

            // 启动后运行到 main()
            "runToEntryPoint": "main",

            // ★ Cortex Live Watch
            // OpenOCD 下可用
            // samplesPerSecond 最大 20
            // 先用 4Hz 足够
            "liveWatch": {
                "enabled": true,
                "samplesPerSecond": 4
            }


            // ============================================================
            // 以下为可选配置
            // ============================================================


            // 如果 Cortex-Debug 找不到 OpenOCD，
            // Linux：
            // which openocd
            //
            // 一般 Fedora 是：
            // /usr/bin/openocd
            //
            // "serverpath": "/usr/bin/openocd",


            // 如果 OpenOCD 找不到 interface/*.cfg 或 target/*.cfg，
            // Fedora 一般脚本目录在：
            // /usr/share/openocd/scripts
            //
            // 可以显式增加：
            //
            // "searchDir": [
            //     "/usr/share/openocd/scripts"
            // ],


            // 调试 Cortex-Debug / OpenOCD 问题：
            //
            // "showDevDebugOutput": "raw"
        }
    ]
}
```

目前我们要知道三件事，`configFiles`，`executable`，`armToolchainPath`和`serverpath`：

1. `configFiles`:

参考 [openocd的两个-f参数](#openocd-parameters)

我们用的`stm32f103c8`和`st-link`：
所以`"configFiles": ["interface/stlink.cfg","target/stm32f1x.cfg"]`。

2. `executable`:

用vscode的终端命令直接查找下，或者直接在VScode的文件管理器里找：

```bash
# Linux
find build -name "*.elf"
```

```powershell
# Windows
gci build -Recurse -File -Filter "*.elf"
```

> 注意，Windows要用`powershell`,不可以用`cmd`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790776029379-8fe367ee.webp)

如上图所示，在`build/Debug/TEST.elf`。

所以`executable` = `${workspaceFolder}/build/Debug/TEST.elf`。

3. `armToolchainPath`:

用vscode的终端命令直接查找下：

```bash
cube bundle show --project
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790776233140-6b8addf2.webp)

这个输出说明，我在用`gnu-tools-for-stm32@14.3.1+st.2`

用命令找他在哪：

```bash
# Linux
find ~/.local/share/stm32cube/bundles/gnu-tools-for-stm32 \
  -type f -name "arm-none-eabi-gdb"
```

```powershell
# Windows
gci "$env:LOCALAPPDATA\stm32cube\bundles\gnu-tools-for-stm32" `
  -Recurse -File -Filter "arm-none-eabi-gdb.exe"
```

> 注意，Windows要用`powershell`,不可以用`cmd`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790776572642-f50c57f3.webp)

复制输出结果：

```text
/home/tungchiahui/.local/share/stm32cube/bundles/gnu-tools-for-stm32/14.3.1+st.2/bin/arm-none-eabi-gdb
```

但是去掉最后的执行程序`arm-none-eabi-gdb`，只需要到`bin`即可：

```text
/home/tungchiahui/.local/share/stm32cube/bundles/gnu-tools-for-stm32/14.3.1+st.2/bin
```

所以`armToolchainPath` = `/home/tungchiahui/.local/share/stm32cube/bundles/gnu-tools-for-stm32/14.3.1+st.2/bin`。

4. `serverpath`:

用终端命令直接查找下：

```bash
# Linux
which openocd
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790779741632-f3fe4580.webp)

而Windows,你自己装哪的你应该知道吧。。。

最终的`launch.json`:

```json
{
    "version": "0.2.0",

    "configurations": [
        {
            // 调试配置名称
            "name": "STM32 - OpenOCD - Cortex Debug",

            // Cortex-Debug 固定写法
            "type": "cortex-debug",

            // 下载程序并开始调试
            "request": "launch",

            // 工程工作目录
            "cwd": "${workspaceFolder}",

            // ★ 必改：编译生成的 ELF
            "executable": "${workspaceFolder}/build/Debug/TEST.elf",

            // 使用 OpenOCD
            "servertype": "openocd",

            // ★ OpenOCD 可执行文件
            // Linux 下用 which openocd 查询
            "serverpath": "/usr/bin/openocd",

            // ★ 根据调试器和 MCU 修改
            //
            // ST-Link：
            // interface/stlink.cfg
            //
            // STM32F1：
            // target/stm32f1x.cfg
            //
            // STM32F4 则是：
            // target/stm32f4x.cfg
            "configFiles": [
                "interface/stlink.cfg",
                "target/stm32f1x.cfg"
            ],

            // ★ ARM GNU Toolchain 的 bin 目录
            // 建议不要直接写 "~"
            // 用 ${env:HOME} 更稳
            "armToolchainPath": "/home/tungchiahui/.local/share/stm32cube/bundles/gnu-tools-for-stm32/14.3.1+st.2/bin",

            // 启动后运行到 main()
            "runToEntryPoint": "main",

            // ★ Cortex Live Watch
            // OpenOCD 下可用
            // samplesPerSecond 最大 20
            // 先用 4Hz 足够
            "liveWatch": {
                "enabled": true,
                "samplesPerSecond": 4
            }
        }
    ]
}
```




##### Debug与LiveWatch

先编译

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790779823836-11e63de7.webp)

点击进入debug：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790779844026-cbb6d48e.webp)

成功进入：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790779922152-6ec02de5.webp)

复制一个你程序里的某个全局变量，我这里是`cmd_vel2`:

填到`cortex live watch`，注意不是`stm32cube live watch`：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790779948053-cd3fcb69.webp)

然后点运行看一下变量`cmd_vel2`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790779999914-cf9b099a.webp)

是不断在变化的：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790780025415-338a7c30.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1790780038391-54b2f46b.webp)


## 常见问题

<a id="usb-permissions"></a>

### Linux下USB权限问题

> **如果你都正常识别出设备了，则没必要做这一步了**

> 如果你遇到的话，你解决不了就让AI来解决，AI几秒钟就给你把事办了

如果调试器插上后能在 `lsusb` 中看到，但 VS Code 找不到设备，或者调试输出中出现下面的报错，通常是当前用户没有访问 USB 设备节点的权限：

```text
libusb couldn't open USB device /dev/bus/usb/001/008, errno=13
libusb requires write access to USB device nodes
ST-Link enumeration failed
```

先在终端运行 `lsusb`，确认调试器已被系统识别。报错中的 `/dev/bus/usb/001/008` 只是示例，实际路径以你自己的输出为准。可以用 `ls -l` 查看该节点的权限：

```bash
lsusb
ls -l /dev/bus/usb/001/008
```

**使用 ST-Link：**先按上文点击插件中的 `install ST-Link udev rules`。如果仍然报权限错误，检查规则是否装到了系统中：

```bash
find /etc/udev/rules.d /usr/lib/udev/rules.d -maxdepth 1 -iname '*stlink*.rules' -print 2>/dev/null
```

如果没有找到规则，可以从 ST 官方的 [STSW-LINK007 下载页](https://www.st.com/en/development-tools/stsw-link007.html)获取安装包。解压后进入 `AllPlatforms/StlinkRulesFilesForLinux`，按其中的 `readme.txt` 安装与你的发行版对应的 udev 规则包。在这个目录中，Fedora 使用 `.rpm` 包，Ubuntu/Debian 使用 `.deb` 包：

```bash
# Fedora：只运行这一行
sudo dnf install ./st-stlink-udev-rules-*.rpm

# Ubuntu/Debian：只运行这一行
sudo apt install ./st-stlink-udev-rules-*.deb
```

如果下载的包名不同，以实际文件名和随包说明为准。ST 的[发行说明](https://www.st.com/resource/en/release_note/dm00107009-firmware-upgrade-for-st-link-st-link-v2-st-link-v2-1-and-stlink-v3-boards-stmicroelectronics.pdf)也说明了 Linux 下需要安装相应的 ST-Link USB 访问规则。

**使用 J-Link：**上文安装的 `jlink-gdbserver` bundle 用于启动调试服务；USB 权限还需要 SEGGER 的 udev 规则。安装 [SEGGER J-Link Software and Documentation Pack](https://www.segger.com/downloads/jlink/) 中对应发行版的 `.rpm` 或 `.deb` 包时，会一并安装规则。如果使用解压版，则按照包内 `README.txt`，将 `99-jlink.rules` 复制到 `/etc/udev/rules.d/`。具体方法见 [SEGGER 的 Linux 排查说明](https://kb.segger.com/J-Link_Troubleshooting)。

安装规则后，运行：

```bash
sudo udevadm control --reload-rules
```

然后**拔下调试器再插回去**，重新运行 `lsusb`，用新的总线号和设备号查看 `/dev/bus/usb/...` 节点。可以将实际路径代入下方命令，检查当前用户能否读写：

```bash
test -r /dev/bus/usb/001/008 && test -w /dev/bus/usb/001/008 && echo 'USB 设备节点可读写'
```

拔插后设备号可能变化，不要照抄示例中的 `001/008`。确认权限后，回到 VS Code 重新选择调试器。如果 `lsusb` 根本看不到调试器，应先检查 USB 线、接口和供电；这不是 udev 权限问题。

这里检查的是 `/dev/bus/usb/...`。如果另外还要打开板载虚拟串口 `/dev/ttyACM*`，那是串口设备的权限，需要分别排查。

