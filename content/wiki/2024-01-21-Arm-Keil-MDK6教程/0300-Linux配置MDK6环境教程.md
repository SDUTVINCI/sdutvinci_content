---
title: "Linux配置MDK6环境教程"
---

***`（本教程为2024年1月创建的，可能与以后的版本有些出入）`***

### 需要准备的软件
1.  CubeMX最新版

2.  VScode最新版

3.  vcpkg包管理工具

4.  pyOcd（如何安装下方有教程）

5.  ST-Link驱动（如何安装下方有教程）

### vcpkg安装与环境配置
1.  下载依赖包

```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install build-essential tar curl zip unzip
sudo apt-get install default-jre
```

2.  克隆vcpkg仓库

https://github.com/microsoft/vcpkg/tree/master

```bash
git clone https://github.com/microsoft/vcpkg.git
```

3.  生成vcpkg程序

```bash
cd vcpkg
sudo chmod a+x ./bootstrap-vcpkg.sh
sudo ./bootstrap-vcpkg.sh
```

4.  配置环境

```bash
vim ~/.bashrc
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image1.webp)

这个 **VCPKG_HOME是vcpkg的目录**

```bash
#配置vcpkg环境 
export VCPKG_HOME=/home/tungchiahui/user/applications/vcpkg  #目录需要改为你的vcpkg的目录
export PATH=$VCPKG_HOME:$PATH
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image2.webp)

```bash
source ~/.bashrc
vcpkg --version
```

出现如图提示则安装成功！

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image3.webp)

### MDK5工程生成与ARMCLANG(AC6)编译器配置
#### 工程生成与编译器配置
1.  **方式一** ：配置编译器教程需要在Windows进行，在Linux上目前很难修改编译器选项，可以参考下方Windows教程里的生成工程并配置默认编译器。(实质就是把编译器从默认的AC5改成AC6)

2.  **方式二** ：克隆已经生成好的模板（模板目前只有几个常用型号的)

仓库链接：

https://github.com/TungChiahuiMCURepos/CubeMX_CMake_Template

```bash
git clone https://github.com/TungChiahuiMCURepos/CubeMX_CMake_Template.git
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image4.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image5.webp)

#### 工程配置(比如初始化一个GPIO口并创建任务使其电平翻转)
先复制一份工程模板

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image6.webp)

重命名工程

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image7.webp)

打开CubeMX(并点击最上方File->Load Project 或者 直接点击下方图中的图标)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image8.webp)

找到工程并Load，并配置好工程

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image9.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image10.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image11.webp)

在文件夹MDK-ARM下打开终端

```bash
cd MDK-ARM
code .
```

### 安装并激活MDK6插件
下载好ARM Keil Studio Pack

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image12.webp)

激活MDK6插件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image13.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image14.webp)

### 初次转化MDK5工程并下载依赖包
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image15.webp)

右下角把这些要安装的pack都安装一下，有什么提示要允许的都允许一下

在安装Packs的时候，需要保证一个良好的网络环境(需要一个有魔法的网络环境)，

这个阶段会持续5-20分钟，请慢慢等待。(看你的机场速度而决定)

(只有第一次运行需要这些操作)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image16.webp)

这个调查可以不查

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image17.webp)

如图即是安装成功

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image18.webp)

如果下方环境已经配置好了，请右键点击uvprojx选择Convert

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image19.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image20.webp)

如果环境没配置好，右键这个文件，选择active environment(图中因为我的环境配置好了，所以是deactive失能)

然后再执行上一步的Convert

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image21.webp)

如图已经初始化成功了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image22.webp)

### 编译
点击build按钮发现文件大小一样就是编译成功了。

若编译失败，则看一下是否是工程文件列表被多配置了一个点。（看下方进阶教程里的添加源文件解决）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image23.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image24.webp)

### Linux如何配置ST-Link等调试器？
#### 安装pyOCD(Linux)
https://github.com/pyocd/pyOCD

先打开终端输入（如果你是debian系的系统，如Ubuntu，请看下方的教程）

```bash
sudo apt install python3-pip
python3 -mpip install -U pyocd

# 如果上面的不行，则输入下方的
pip3 install -U pyocd
```

如果还不行，且提示

```bash
error: externally-managed-environment

× This environment is externally managed
╰─> To install Python packages system-wide, try apt install
    python3-xyz, where xyz is the package you are trying to
    install.
```

则使用（debian系的系统）

```bash
sudo apt install python3-pyocd
```

**或者** 说直接克隆仓库

```Python
git clone https://github.com/pyocd/pyOCD.git
cd pyOCD
pip3 install .
```

这样也可以安装pyOCD

接下来，我们需要安装ST-Link等调试器的驱动。

pyOCD安装调试器驱动官方教程：

https://github.com/pyocd/pyOCD/tree/main/udev

还是需要用到pyOCD仓库里的文件。

如果你没clone仓库请尽快克隆。

在仓库目录下，输入以下命令

```Python
cd udev
sudo cp *.rules /etc/udev/rules.d
#重启udev
sudo udevadm control --reload
sudo udevadm trigger
```

这样ST-Link就可以正常被检测出来了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image25.webp)

如果没被检测出来，请插拔一下ST-Link，然后点击Add Device添加一下设备。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image26.webp)

#### 更新ST-Link最新驱动(Linux)
https://www.st.com/en/development-tools/stsw-link007.html#get-software

暂时无法在飞书文档外展示此内容

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image27.webp)

下载后的文件解压出来。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image28.webp)

```Python
sudo apt install ./st-stlink-udev-rules-1.0.3-2-linux-all.deb
```

重启VScode即可

(下方还有其他有关的教程操作，请往下滑)
