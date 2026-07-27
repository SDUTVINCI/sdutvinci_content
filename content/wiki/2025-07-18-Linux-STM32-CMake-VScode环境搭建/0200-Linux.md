---
title: "Linux"
---

### 环境介绍
本教程环境介绍：

1.  系统：Fedora 42 KDE Edition Linux

2.  系统内核：Linux 6.15.6-200.fc42.x86_64

3.  架构：X86_64

其他Linux环境也可以。

### 安装各种环境
#### 安装C/C++环境
```bash

# debian系
sudo apt-get install gcc g++ gdb cmake-gui make

# rhel系
sudo dnf install gcc g++ gdb cmake-gui make
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image1.webp)

查看是否环境安装成功

```
gcc -v
g++ -v
gdb -v
cmake --version
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image2.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image3.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image4.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image5.webp)

接下来测试是否能够对C/C++正常编译，请找一个存放C++代码的文件夹，然后在终端中cd进去。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image6.webp)

然后创建一个.cpp文件并用vim编辑

```bash
vim hello.cpp
```

复制以下代码到该文件里

```cpp
#include <iostream> 
int main(int argc,char **argv) 
{ 
    std::cout << "你好，机电创新学会！" << std::endl; 
    return 0; 
}
```

然后编译

```bash
g++ -o hello hello.cpp
ls
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image7.webp)

运行

```bash
./hello
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image8.webp)

说明环境已经配置好了

#### 安装CubeMX
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image9.webp)

下载地址：

https://www.st.com.cn/zh/development-tools/stm32cubemx.html

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

然后安装一些插件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image27.webp)

下面这些都要装

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image28.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image29.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image30.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image31.webp)

#### 安装ARM GNU工具链
编译工具比较：

| 特性 | ARM GCC (GNU 工具链) | Keil AC5 (ARM Compiler 5) | Keil AC6 (ARM Compiler 6) |
|:---|:---|:---|:---|
| 核心身份 | 基于GNU GPL的开源编译器 | ARM自家的传统编译器 | 基于LLVM/Clang的现代编译器 |
| 许可模式 | 免费、开源 | 商业收费（包含在MDK中） | 商业收费（包含在MDK中） |
| 代码生成/优化 | 良好，持续改进 | 优秀（尤其在小代码尺寸上） | 极佳，在性能与尺寸间有最佳平衡 |
| 标准兼容性 | 紧跟最新C/C++标准（如C17，C++17/20） | 主要支持C++98，较陈旧 | 支持现代C++（C++11/14/17），兼容性更好 |
| 错误/警告信息 | 比较清晰易懂 | 相对晦涩 | 非常清晰和友好，类似GCC/Clang |
| 与Keil MDK集成 | 需要手动配置或通过CubeIDE | 原生、无缝集成 | 原生、无缝集成，是ARM推荐选择 |
| 链接脚本 | 使用自有的链接脚本语法（.ld文件） | 使用ARM自家的分散加载文件语法（.sct） | 使用ARM自家的分散加载文件语法（.sct） |
| 汇编语法 | 使用GNU汇编语法（.S文件） | 使用ARM汇编语法（.s） | 使用ARM汇编语法（.s），但AC6更严格 |
| 生态与未来 | 生态强大，是很多开源项目（如Zephyr，ESP-IDF）和IDE（CubeIDE，VS Code）的首选 | 处于维护模式，ARM不再增加新功能，不推荐新项目使用 | 是ARM的未来和主力，持续更新和优化 |

* * *

##### 安装
**建议都用****官方法****进行安装。**

###### 方法一（官网法）
https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image32.webp)

进入下载目录并打开终端

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image33.webp)

在终端里输入下列命令，将编译器文件tar压缩包复制到你存放程序的文件夹（这个文件夹你自己定，建议在home分区，别以后删了就行）。

具体命令为`cp ./arm-gnu`然后按`tab`补齐，然后空格，再跟上你要复制到的文件夹的路径。

比如下面的命令：

```bash
cp ./arm-gnu-toolchain-14.3.rel1-x86_64-arm-none-eabi.tar.xz ~/UserFolder/Applications/
```

然后进入复制到的文件夹：

```bash
cd ~/UserFolder/Applications/
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image34.webp)

在终端里输入`tar -xvf ./arm-gnu`并按`tab`补齐。

例如我补齐后的：

```bash
 tar -xvf ./arm-gnu-toolchain-14.3.rel1-x86_64-arm-none-eabi.tar.xz
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image35.webp)

再进入这个解压后的文件夹

`cd ./arm-gnu`按`tab`补齐。

```bash
cd ./arm-gnu-toolchain-14.3.rel1-x86_64-arm-none-eabi/
cd ./bin
```

查看文件夹路径

```bash
pwd
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image36.webp)

复制一下`/home/tungchiahui/UserFolder/Applications/arm-gnu-toolchain-14.3.rel1-x86_64-arm-none-eabi/bin`

然后需要配置环境

```bash
vim ~/.bashrc
```

在末尾输入下面的命令，把下面`~/UserFolder/Applications/arm-gnu-toolchain-14.3.rel1-x86_64-arm-none-eabi/bin`替换成你刚才复制的路径，`/home/用户名`可以用`~`来代替。

```bash
export PATH=/home/tungchiahui/UserFolder/Applications/arm-gnu-toolchain-14.3.rel1-x86_64-arm-none-eabi/bin:$PATH
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image37.webp)

加载环境

```bash
source ~/.bashrc
```

###### 方法二（系统仓库法）
**不建议本法**

```bash

# Debian系
sudo apt install arm-none-eabi-gcc

# Rhel系
sudo dnf install arm-none-eabi-gcc
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image38.webp)

##### 测试
检查版本

```bash
arm-none-eabi-gcc -v
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image39.webp)

#### 安装JLink驱动
##### 安装libreadline库
我们烧录会用到JLinkExe的命令，而JLinkExe会用到libreadline库，所以要安装libreadline库，执行如下命令安装：

```bash

# debian系
sudo apt-get install libreadline-dev

# rhel系
sudo dnf install readline-devel
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image40.webp)

##### 安装JLink驱动
https://www.segger.com/downloads/jlink/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image41.webp)

是Debian系下载64位DEB

是RHEL系下载64位RPM

（这里的64位指的是amd64和X86_64,如果你是ARM64请下载下方那个Linux ARM里的64位）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image42.webp)

打开下载到的文件夹，并打开终端

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image43.webp)

然后`sudo apt install ./JLink`然后tab补齐。

然后`sudo dnf install ./JLink`然后tab补齐。

```bash
sudo dnf install ./JLink_Linux_V852_x86_64.rpm
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image44.webp)

检查是否安装成功

```bash
JLinkExe
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image45.webp)

我们点击No，然后会进入Commander交互模式，在这种模式下，我们可以执行各种 J-Link Commander 提供的命令来连接、配置调试器，下载程序或文件到目标设备等操作，感兴趣的同学可自行学习。

执行“q”指令退出该模式。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image46.webp)

##### 下载并安装Ozone
https://www.segger.com/products/development-tools/ozone-j-link-debugger/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image47.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image48.webp)

是Debian系下载64位DEB

是RHEL系下载64位RPM

（这里的64位指的是amd64和X86_64,如果你是ARM64请下载下方那个Linux ARM里的64位）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image49.webp)

然后`sudo apt install ./Ozone`然后tab补齐。

然后`sudo dnf install ./Ozone`然后tab补齐。

```bash
sudo dnf install ./Ozone_Linux_V338g_x86_64.rpm
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image50.webp)

##### 测试
打开终端输入

```bash
ozone
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image51.webp)

#### 下载SVD
https://www.st.com.cn/content/st_com/zh.html

在搜索里搜索芯片型号，如stm32f103c8t6

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image52.webp)

点CAD资源

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image53.webp)

下载SVD，鼠标点红色框的区域就可以下载了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image54.webp)

解压后就可以获得F1系列的SVD文件了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image55.webp)

依次把F4和H7的也下载解压了就可以了。（可以解压到一个文件夹里）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image56.webp)

然后在上面的文件夹打开终端

将这些文件夹全部复制到Ozone的`Config/Peripherals/`目录下。（你需要提前确定一下ozone的配置是否是这个路径再复制）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image57.webp)

```bash
sudo cp ./*.svd /opt/SEGGER/Ozone_V338g/Config/Peripherals/
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image58.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image59.webp)

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

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image70.webp)

然后打开vscode

```bash
code .
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image71.webp)

进入vscode后，点击目录下的CMakeLists.txt

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image72.webp)

检查第25行左右是否有下面这行，如果没有，你需要手动给他加上这两行。(6.14.1版本没有这个bug)

*tips1：这就是上面说的CubeMX6.15.0版本的bug,因为这个版本增加了对clang编译器的支持，在CubeMX里也支持了选择编译器的操作，但是这个选项估计是工程师没写好，选择编译器不管选哪个，他都不会选择咱们选择的编译器，所以咱们需要手动选择。*

*tips2:这CubeMX6.15.0有第二个bug,这个工作区根CMakeLists.txt他说了只会生成一次，后续不会再重新覆盖生成，但是发现每次在CubeMX修改配置后，然后重新生成代码，其他命令都被保留了，就这个命令不会被保留。不知道后续会不会被修复，或者直接修复上面tips1的bug.所以每次重新配置CubeMX后，需要再把这句加上。*

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image73.webp)

```bash

# Include toolchain file
include("cmake/gcc-arm-none-eabi.cmake")
```

按ctrl+～打开内置终端。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image74.webp)

使用下方命令创建并进入build文件夹

```bash
mkdir build
cd build
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image75.webp)

接下来使用cmake命令生成makefile文件

```bash
cmake ..
```

检查一下是否ARM的C/C++以及汇编编译器都被找到了（如果没有，请检查上面的教程是否有做错的地方）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image76.webp)

然后使用make命令进行编译，命令为`make`或者`make -jxx`,这里的xx是你想使用CPU的几个线程来进行编译，比如我电脑是8核16线程，我就可以让xx是比16低的数字。而`make`是默认用一个线程。如果你并不知道你CPU有几个线程，那你就老老实实用`make`命令，别用`make -jxx`命令了。

```bash
make -j16
```

这样就是编译成功了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image77.webp)

#### 对代码提示进行配置
在VScode中按Ctrl+Shift+P,搜索clangd,并选择下载语言服务

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image78.webp)

在右下角选择安装即可，安装完就会出现下图提示。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image79.webp)

接着禁用C/C++插件的代码提示功能(如果没这个界面，请往下看)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image80.webp)

如果没有上图的弹窗，可以进行手动关闭，依然是ctrl shift P,输入settings然后找到如下图的选项

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image81.webp)

找到下图这个选项，改成disabled即可。

`"C_Cpp.intelliSenseEngine": "disabled"`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image82.webp)

在VScode中再按Ctrl+Shift+P,搜索clangd,并选择重启clangd语言服务(重启clangd语言服务之前必须编译过一遍代码了)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image83.webp)

此时，可以看代码里头文件都正常识别了,代码提示也正常了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image84.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image85.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image86.webp)

⚠️注意：Clangd 默认找的是 **本机系统的 libc/include 路径（比如 x86_64 的 `/usr/include`）** ，而我们工程里面实际使用的是 **ARM 工具链的头文件路径** ，这就有概率导致包含C/C++库函数的头文件报错

例如：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image87.webp)

这里的 #include <math.h>显示找不到头文件，但是我们进行编译的时候却没有报错，说明是clangd的配置有问题 。以下介绍一种解决方法：

1.  运行以下命令，获取 ARM GCC 使用的标准 include 路径：

```bash
arm-none-eabi-gcc -x c -E -v - </dev/null
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image88.webp)

2.  在工程根目录下面创建 .clangd 文件 将自己的头文件路径包含进去（引号里面替换成你自己的arm gcc头文件路径）

```
CompileFlags:
  Add: [
    "-isystem", "/home/xiaofang/Applications/arm-gnu-toolchain-14.3.rel1-x86_64-arm-none-eabi/lib/gcc/arm-none-eabi/14.3.1/include",
    "-isystem", "/home/xiaofang/Applications/arm-gnu-toolchain-14.3.rel1-x86_64-arm-none-eabi/lib/gcc/arm-none-eabi/14.3.1/include-fixed",
    "-isystem", "/home/xiaofang/Applications/arm-gnu-toolchain-14.3.rel1-x86_64-arm-none-eabi/arm-none-eabi/include"
  ]
```

保存，此时刷新一下clangd,头文件提示正常

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image89.webp)

#### 移植Vinci机器人队标准C/C++工程模板
用git clone命令克隆仓库:https://github.com/tungchiahui/CubeMX_MDK5to6_Template

```bash
git clone https://github.com/tungchiahui/CubeMX_MDK5to6_Template.git
```

把仓库里的“工程文件移植”文件夹里的 **所有内容** 复制到我们CMake工程的目录里。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image90.webp)

然后打开applications文件夹，在Src和Inc文件夹分别创建led_task.cpp和led_task.h，内容分别如下:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image91.webp)

led_task.cpp:

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

led_task.h:

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

然后打开`cmake/user`文件夹下的`CMakeLists.txt`，把刚才新建的led_task.cpp添加上去。

详细介绍（可以不看）：这里的`cmake/stm32cubemx`下的`CMakeLists.txt`是被CubeMX管理的，你重新用CubeMX生成新代码后，这个文件里的东西会被覆盖。而工作区根目录下的`CMakeLists.txt`是不会被重新覆盖的，而且给我们留了一些区域加源文件和头文件，但是这样会让这个文件太过于嘈杂。所以我们选择新建一个user文件夹，然后在这里面弄一个`CMakeLists.txt`，再用顶层`CMakeLists.txt`去加载这个子`CMakeLists.txt`，这个子`CMakeLists.txt`方便咱们修改，文件结构也更加明显。（这些都不需要咱们自己创建，我已经给创建到 **工程文件移植** 里了，你在上面复制的时候已经复制过来了）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image92.webp)

然后要去最顶层的CMakeLists.txt里加上这句话来引用我们自己的CMakeLists.txt。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image93.webp)

```cmake

# Add USER generated sources
add_subdirectory(cmake/user)
```

大功告成，编译一次试试。可以看到下图，那些新加的文件都编译上了。

```bash
cmake ..
make
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image94.webp)

然后去main.c中引用cpp_interface.h头文件，并将cpp_main()函数在main函数的这个地方调用。(我这里是开RTOS了，所以需要在启动RTOS之前调用cpp_main函数，如果你是没有用RTOS的裸机程序，则在while (1)的上方调用cpp_main即可)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image95.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image96.webp)

然后在cpp_interface.h里修改isRTOS这个宏来让程序知道你是否开启了RTOS，如果开启了，宏就为1，裸机就填0。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image97.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image98.webp)

其他更加详细的关于STM32的C/C++工程介绍请看[Vinci机器人队单片机教程](/wiki/2023-10-09-stm32-freertos-jiao-cheng)。

此时在build文件夹下进行编译程序，发现成功!

```bash
cmake ..
make
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image99.webp)

#### 下载程序到板子
##### 配置CMake生成.bin和.hex文件
在下载程序到板子之前，我们需要去看看咱们之前编译的到底生成了啥文件。

通过下图可知，他只生成了.elf文件，并没有咱们常见的.bin和.hex文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image100.webp)

咱们需要再更改一下工作区下的CMakeLists.txt从而来让编译的时候生成.hex和.bin（没办法，就得这么麻烦，我也不知道为啥CubeMX不给全干好）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image101.webp)

```cmake

# 生成 .bin 和 .hex 文件
find_program(OBJCOPY arm-none-eabi-objcopy REQUIRED)

add_custom_command(TARGET ${CMAKE_PROJECT_NAME} POST_BUILD
    COMMAND ${OBJCOPY} -O binary ${CMAKE_PROJECT_NAME}.elf ${CMAKE_PROJECT_NAME}.bin
    COMMAND ${OBJCOPY} -O ihex   ${CMAKE_PROJECT_NAME}.elf ${CMAKE_PROJECT_NAME}.hex
    COMMENT "Generating ${CMAKE_PROJECT_NAME}.bin and ${CMAKE_PROJECT_NAME}.hex from ${CMAKE_PROJECT_NAME}.elf"
)
```

这些需要在工作区主CMakeLists.txt里添加的命令我全都写在这个记事本里了，每次生成新工程直接复制即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image102.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image103.webp)

然后再次编译

```bash
cmake ..
make
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image104.webp)

此时再看build目录：咱们需要的.hex或者.bin就出来了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image105.webp)

##### 将设备连接到JLink并烧录程序
###### 图形界面烧录
```bash
#打开终端输入
JFlashLite
```

选择对应的芯片型号和速度

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image106.webp)

添加hex文件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image107.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image108.webp)

点击烧录并完成：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image109.webp)

成功点亮led：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image110.webp)

###### 终端烧录
算鸟算鸟，太麻烦了。

#### 配置VScode任务
咱们在上面编译，一直需要输入以下命令

```cmake
cd build
cmake ..
make
```

这样每次编译过于麻烦了，所以我们使用强大的VScode来一键编译。

首先创建`.vscode`文件夹和`tasks.json`文件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image111.webp)

以下是`tasks.json`的内容：

```json
{
    "version": "2.0.0",
    "options": {
        "cwd": "${workspaceFolder}/build"    //需要进入到我们执行tasks任务的文件夹中
    },
    "tasks": [    //tasks包含4个任务
        {
            "type": "shell",
            "label": "stm32-cmake",    //第一个任务的名字叫cmake
            "command": "bash",
            "args": [
                "-c",
                "mkdir -p ../log && echo \"===== CMake started at: $(date) =====\" | tee -a ../log/cmake.log && cmake .. 2>&1 | tee -a ../log/cmake.log"
            ],
            "problemMatcher": []    //这里需要添加一个空的问题匹配器，否则会报错
        },
        {
            "label": "stm32-make",    //第二个任务的名字叫make
            "command": "bash",
            "args": [
                "-c",
                "mkdir -p ../log && echo \"===== Make started at: $(date) =====\" | tee -a ../log/make.log && make -j$(grep -c ^processor /proc/cpuinfo) 2>&1 | tee -a ../log/make.log"
            ],
            "problemMatcher": []    //这里也需要添加一个空的问题匹配器，否则会报错
        },
        {
            "label": "stm32-Build",    //第3个任务的名字叫Build
            "group": {               //默认是build任务
                "kind": "build",
                "isDefault": true
            },
            "dependsOrder": "sequence",    //顺序执行依赖项
            "dependsOn":[    //依赖的2个项为cmake、make
                "stm32-cmake",    //即第一个任务的label
                "stm32-make"      //即第二个任务的label
            ]
        },
        {
            "type": "shell",
            "label": "stm32-clean",    //第四个任务：清理 build 文件夹
            "command": "bash",
            "args": [
                "-c",
                "echo \"===== Clean started at: $(date) =====\" && rm -rf * && echo \"Build folder cleaned.\""
            ],
            "options": {
                "cwd": "${workspaceFolder}/build"    //只清理build目录下的文件
            },
            "problemMatcher": []    //不需要问题匹配器
        }

    ]
}
```

**方法一：**

在VScode标题栏上，找到`终端`，然后再选择`运行构建任务`，快捷键是`Ctrl+Shift+B`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image112.webp)

可见任务已经被运行了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image113.webp)

**方法二：**

在VScode标题栏上，找到`终端`，然后再选择`运行任务`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image114.webp)

下面有4个stm32的任务，第一个是`stm32-Build`任务，运行后的效果和刚才方法一是一样的，方法一的那个`运行构建任务`的按钮，就是运行的这个`stm32-Build`任务。

这个`stm32-Build`任务包含stm32-cmake和stm32-make任务。

然后`stm32-clean`任务就是清除build文件夹下的所有文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image115.webp)

可以试一下`stm32-clean`任务。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image116.webp)

可以发现都删完了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image117.webp)

**你不用担心每次新建工程都需要配置那么多东西。**

以上大多数配置文件全部都已经包含在https://github.com/tungchiahui/CubeMX_MDK5to6_Template仓库下的***`工程文件移植(创建新模板请看这里)`***文件夹了，到时候新建一个工程后，直接把这个文件夹下的所有文件全部复制过来即可。

#### 使用ozone进行Flash烧录和Debug调试
##### 基础配置
打开终端输入ozone打开软件或者直接找到应用图标打开ozone

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image118.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image119.webp)

先选择device，比如我是STM32F407VET6

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image120.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image121.webp)

选择Peripherals:

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image122.webp)

点击下一步

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image123.webp)

你用的SWD就填SWD，是JTAG就填JTAG

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image124.webp)

选择ELF

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image125.webp)

elf,hex,bin都可以选，一般选elf就行。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image126.webp)

这一步保持默认即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image127.webp)

如果你开启了RTOS可能会遇到这个问题。

```bash
warning (138): The target application seems to be using FreeRTOS, but FreeRTOS-awareness is not enabled.
```

意思是你的目标应用似乎使用了 FreeRTOS，但当前没有启用对 FreeRTOS 的调试支持（RTOS-awareness）。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image128.webp)

直接按照他底下的提示应用修复即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image129.webp)

点继续就行。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image130.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image131.webp)

##### 烧录与调试
可以看下面这个视频，讲的挺好的。**(从30:10开始看）**

https://www.bilibili.com/video/BV1yrLHzZEoE

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image132.webp)

点击File让他按文件名排序。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image133.webp)

找到led_task.cpp点击就可以打开这个源文件啦。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2025/07/18/image134.webp)
