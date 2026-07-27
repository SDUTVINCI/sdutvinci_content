---
title: "OpenCV_CUDA环境搭建"
---

### Linux
更推荐在Linux上部署，一些深度学习的东西，在Linux上的运行速度要明显**远远高于**Windows。

如果你没有空闲硬盘装Linux了，可以考虑WSL2(在Windows上运行的Linux子系统2)，虽有一点点性能损失，但速度也远远高于Windows。

WSL2安装教程[Vinci机器人队Linux入门教程](/wiki/2024-03-30-linux-jiao-cheng)

**实体机Linux****＞****WSL2****＞＞****Windows**

关于cv_bridge:最好在安装ros之前编译opencv，这样安装ros时,cv_bridge就会自己指向已经安装过的opencv，并且ros不会另外安装opencv，如此就可以通过find_package指令找到电脑上仅有的cv_bridge和opencv，保证系统环境不被污染。关于补救办法，请看常见问题

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image23.webp)

#### 保证显卡正常
**请确保 英伟达驱动、CUDA、cuDNN 全部安装成功并且版本正确。**

**(安装驱动、CUDA、cuDNN教程:**[Vinci机器人队Linux入门教程](/wiki/2024-03-30-linux-jiao-cheng)**)**

```bash

# 检查显卡驱动
nvidia-smi

# 验证CUDA是否安装成功
nvcc -V

# 检查cuDNN版本命令(仅仅只是查了头文件)
cat /usr/local/cuda/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
```

出现下图这样的，则你是有英伟达驱动，CUDA以及cuDNN的

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image24.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image25.webp)

#### 安装依赖项
```bash

# Debian系系统
sudo apt install -y libcurl4 build-essential pkg-config cmake-gui \
    libopenblas-dev libeigen3-dev libtbb-dev \
    libavcodec-dev libavformat-dev \
    libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev \
    libswscale-dev libgtk-3-dev libpng-dev libjpeg-dev \
    libcanberra-gtk-module libcanberra-gtk3-module libv4l-dev python3-dev python3-numpy

# RHEL红帽系系统
bash -c 'sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm'
sudo dnf install -y curl gcc gcc-c++ make cmake cmake-gui \
    openblas-devel eigen3-devel tbb-devel \
    ffmpeg-libs ffmpeg-devel \
    gstreamer1-plugins-base-devel gstreamer1-devel \
    gtk3-devel libpng-devel libjpeg-devel \
    libc=aanberra-gtk3 libcanberra-devel v4l-utils v4l2loopback openexr-devel python3-dev python3-numpy

```

下方表格是这些依赖的说明

| 生成 OpenCV 的主要依赖项 |
|:---|
| 名称 | apt package 名称 | 功能 |
| 编译系统 | build-essential cmake cmake-gui pkg-config | 生成 OpenCV |
| 图像库 | libpng-dev libjpeg-dev | 提供各类图像格式的编解码 |
| OpenBLAS | libopenblas-dev | 利用 CPU 向量运算指令为大量算法提供加速。 |
| Eigen3 | libeigen3-dev | 提供线性代数相关算法支持 |
| Intel TBB | libtbb-dev | 在 Intel CPU 上提供高性能并发计算支持 |
| FFMPEG | libavcodec-dev libavformat-dev libswscale-dev | 提供视频编解码能力 |
| GStreamer | libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev | 提供流媒体处理能力 |
| GTK | libgtk-3-dev libcanberra-gtk-module libcanberra-gtk3-module | 图形化用户界面 |
| Video4Linux | libv4l-dev | Linux摄像头库 |

#### 下载OpenCV源码
https://github.com/opencv/opencv

https://github.com/opencv/opencv_contrib

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image26.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image27.webp)

```bash

# 创建文件夹存放源码
mkdir -p ooppccvv
cd ooppccvv
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image28.webp)

```bash

# 解压源码
unzip ./opencv-4.11.0.zip
unzip ./opencv_contrib-4.11.0.zip
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image29.webp)

确认一下 opencv 目录和 opencv-contrib 目录位于相同的父目录内，并确认这两个目录下都存在 modules 子目录：**(一般不用确认，只要你照着敲我上方的命令，一定没问题)**

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image30.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image31.webp)

#### CMake编译
##### 准备工作
```bash

# 创建build文件夹用于装CMake生成的内容:
cd opencv-4.11.0
mkdir -p build && cd build
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image32.webp)

OpenCV使用CMake与Makefile进行编译，编译选项较多，详见（也可以不看，不过不同版本有些CMake编译选项是不同的）：

https://docs.opencv.org/4.10.0/db/d05/tutorial_config_reference.html

下方被划掉的是在OpenCV4.11.0中已经不复存在的参数，但是可能在其他版本的OpenCV中仍有效，请自行用`CMake-LAH`(不推荐)命令或者`CMake-gui`(推荐)查看。

| OpenCV4.11.0 CMake常用编译选项表一览 |
|:---|
| 序号 | CMake参数名 | 参数值 | 作用 |
| 1 | CMAKE_BUILD_TYPE | Release | 不在行成的库文件中包含调试信息，并进行速度优化。如果指定为 Debug ，就可以在 Debug 过程中进入 OpenCV 内部的代码，但运行速度会略微下降。 |
| 2 | CMAKE_INSTALL_PREFIX | /usr/local | 指定 OpenCV 生成的库文件在系统中的安装路径。 |
| 3 | CMAKE_VERBOSE_MAKEFILE | ON | 务必开启，以便于发现编译中出现的问题。 |
| 4 | BUILD_SHARED_LIBS | ON | 成共享库（.so），如果置为 OFF 则只会生成静态库（.a） |
| 5 | OPENCV_EXTRA_MODULES_PATH | <Modules路径> | 按之前的描述，应为 「../../opencv_contrib-4.10.0/modules」。如果使用CMake-GUI则应为「../opencv_contrib-4.10.0/modules」。可以用 ls 命令确认相对路径是否存在。 |
| 6 | OPENCV_ENABLE_NONFREE | ON | 如果置为OFF，一些包含专利保护算法的函数将不会生成。 |
| 7 | ENABLE_CXX11 | ON | 支持 C++11 以上的语法和 STL 库。 |
| 8 | BUILD_TESTS | OFF | 可以关闭生成后的自我 TEST ，大多数情况没有必要开启，可大辐缩短生成时间。但如果怀疑生成的 OpenCV 库有问题，则尽量开启一下，可以进行自测。 |
| 9 | BUILD_PERF_TESTS | OFF |
| 10 | OPENCV_GENERATE_PKGCONFIG | ON | 建议开启，便于 C++ 程序通过 pkg-config 来引用 OpenCV 库。 |
| 11 | WITH_GTK | ON | 编译图形界面，cv2.show会用到 |
| 12 | CUDA_ARCH_BIN | 7.2;8.6;8.7 | 这俩空需要填显卡算力，下面会有教程教你怎么查你的显卡算力。BIN指定为哪些 真实 GPU 架构 生成二进制代码（SASS），通常你要编译几张不同显卡，就要用分号分隔开这些显卡对应的算力（如 "7.2;8.6;8.7" ）。PTX指定为哪些 虚拟架构 生成 PTX 中间代码（用于未来 GPU 的 JIT 编译） 。通常只需设置BIN的最高算力（如 "8.7" ）。 |
| 13 | CUDA_ARCH_PTX | 8.7 |
| 14 | WITH_CUDA | ON | 如果系统正确安装了 CUDA 并希望 OpenCV 启用 CUDA 支持，这四个选项都要打开。 |
| 15 | ENABLE_FAST_MATH | ON | 支持math快速计算 |
| 16 | CUDA_FAST_MATH | ON | cuda的math快速计算 |
| 17 | WITH_CUBLAS | ON | 开启CUBLAS |
| 18 | WITH_CUDNN | ON | 希望OpenCV启用cuDNN支持，这三项要打开 |
| 19 | WITH_DNN | ON | 开启DNN |
| 20 | OPENCV_DNN_CUDA | ON | 启用CUDA，必须安装CUDA、CUBLAS和CUDNN。 |
| 21 | CUDA_HOST_COMPILER | /usr/bin/gcc-13 | (可选)如果你是gcc14及以上，请安装一个gcc13及以下，改用gcc13及以下编译CUDA |
| 22 | WITH_IPP | ON | 这四个选项控制 OpenCV 如何进行并发运算，默认都是 ON，但如果有需要生成一个绝对单线程运行的 OpenCV ，请将这几个选项均置为 OFF 。 |
| 23 | WITH_TBB | ON |  |
| 24 | WITH_OPENMP | ON |  |
| 25 | WITH_PTHREADS_PF | ON |  |
| 26 | BUILD_opencv_python3 | ON | OpenCV 提供 Python3 支持。在OpenCV4的某个版本已经默认打开。 |
| 27 | OPENCV_PYTHON3_VERSION | Python3版本，如3.12 | 填你的python3版本，比如Fedora42是Python3.13，就填3.13 |
| 28 | PYTHON3_EXECUTABLE | Python3路径 | Python3 C++接口库的路径/usr/bin/python3 |
| 29 | PYTHON3_LIBRARY | Lib路径 | Python3 C++接口库的路径 |
| 30 | PYTHON3_NUMPY_INCLUDE_DIRS | 矩阵库Head路径 | Python3 矩阵库头文件的路径 |
| 31 | PYTHON3_INCLUDE_DIR | Head路径 | Python3 C++头文件的路径 |
| 32 | PYTHON3_PACKAGES_PATH | Path路径 | OpenCV 的 Python3 包安装的路径 |
| 33 | WITH_OPENGL | ON | 启用OPENGL |
|  |  |  |  |

1.  查询GPU Compute Capability(CUDA_ARCH_BIN参数):

https://developer.nvidia.com/cuda-gpus#collapseOne

进入网站后，

GeForce代表英伟达游戏系列显卡，常见的有GTX1080，RTX3080，RTX 4080等。

Jetson代表工控机序列显卡。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image33.webp)

我是3060Laptop(笔记本移动端显卡，所以找右列的Notebook下方的3060)

如果你是3060(台式桌面端，则要找左列的3060)

通过图得知，我的显卡算力(GPU Compute Capability)为8.6，所以我的CMake的CUDA_ARCH_BIN参数为8.6。

CUDA_ARCH_PTX为BIN的最高值，我只设置了一个BIN，所以最高值就是这个8.6。(只有你要给电脑更换显卡的情况下，才要给BIN设置多个值，就需要把你要用的显卡的值全包含在BIN中，而PTX只需要BIN的最高值即可)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image34.webp)

2.  Python3的路径查询，请在终端中使用，检查是否都有结果生成，确保打印出的结果符合预期后再进行下面的CMake生成操作。(不需要记住路径)

当然你也可以用命令反馈出来的路径复制出来，这个路径就是参数的值。

```bash

# Python3 C++接口库的路径
python3 -c "import sysconfig; from os.path import join; print(join(sysconfig.get_config_var('LIBDIR'), sysconfig.get_config_var('LDLIBRARY')))"

# Python3 矩阵库头文件的路径
python3 -c "import numpy; print(numpy.get_include())"

# OpenCV 的 Python3 包安装的路径。
python3 -c "import sysconfig; print(sysconfig.get_path('purelib'))"

# Python3 头文件的路径
python3 -c "import sysconfig; print(sysconfig.get_path('include'))"
```

##### CMake编译**(两种方式选其一)**
因为电脑配置的不同，每个电脑的硬件，软件(依赖包)等都不同，所以我能跑起来的你不一定一下就能跑成功。

一般很难风调雨顺，如果有问题及时去百度，谷歌，OpenCV论坛上找答案。

论坛:https://forum.opencv.org/

###### CMake终端命令方式(不建议，更建议用GUI的方式，这种终端的方式容易出奇奇怪怪的问题)
```bash
cmake .. -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX=/usr/local \
        -DBUILD_SHARED_LIBS=ON \
        -DOPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-4.11.0/modules \
        -DOPENCV_ENABLE_NONFREE=ON \
        -DBUILD_TESTS=ON \
        -DBUILD_PERF_TESTS=ON \
        -DOPENCV_GENERATE_PKGCONFIG=ON \
        -DWITH_GTK=ON \
        -DWITH_CUDA=ON \
        -DENABLE_FAST_MATH=ON \
        -DCUDA_FAST_MATH=ON \
        -DWITH_CUBLAS=ON \
        -DCUDA_ARCH_BIN="8.6" \
        -DCUDA_ARCH_PTX="8.6" \
        -DCUDA_HOST_COMPILER=/usr/bin/gcc-13 \
        -DWITH_CUDNN=ON \
        -DOPENCV_DNN_CUDA=ON \
        -DWITH_IPP=ON \
        -DWITH_TBB=ON \
        -DWITH_OPENMP=ON \
        -DWITH_PTHREADS_PF=ON \
        -DOPENCV_PYTHON3_VERSION=3.12 \
        -DPYTHON3_EXECUTABLE=/usr/bin/python3 \
        -DPYTHON3_LIBRARY=$(python3 -c "import sysconfig; from os.path import join; print(join(sysconfig.get_config_var('LIBDIR'), sysconfig.get_config_var('LDLIBRARY')))") \
        -DPYTHON3_NUMPY_INCLUDE_DIRS=$(python3 -c "import numpy; print(numpy.get_include())") \
        -DPYTHON3_PACKAGES_PATH=$(python3 -c "import sysconfig; print(sysconfig.get_path('purelib'))") \
        -DPYTHON3_INCLUDE_DIR=$(python3 -c "import sysconfig; print(sysconfig.get_path('include'))") \
        -DWITH_OPENGL=ON

```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image35.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image36.webp)

你可以根据你的需要，按照之前的说明来自行增减或修改这些选项。如果 cmake 命令出错，往往是缺少依赖项或配置的生成选项不正确所致，可根据提示信息来检查。如果执行成功，就可以进行编译（请看Makefiles编译部分）了：

###### CMake-GUI方式(推荐，不过太麻烦，但是问题少，且更好找问题)
1.  打开CMake-GUI：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image37.webp)

配置一下这俩路径

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image38.webp)

点击左下角配置，选择Makefiles

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image39.webp)

2.  配置参数

根据上方表格去挨个参数进行配置。填完所有选项后，配置完毕点Configure.

在配置参数遇到问题，请看下面的常见问题章节。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image40.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image41.webp)

你可以根据你的需要，按照之前的说明来自行增减或修改这些选项。如果 cmake 命令出错，往往是缺少依赖项或配置的生成选项不正确所致，可根据提示信息来检查。如果执行成功，就可以进行编译(进行Makefiles编译)了：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image42.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image43.webp)

###### 常见问题
####### 无CUDA选项
有时候CMake-GUI只会在编译后才显示某些参数(比如只有编译过WITH_CUDA才会显示CUDA相关的选项)。

所以你需要先把WITH_CUDA打上勾，再点左下角的config,这样才会出现和CUDA相关的选项，再把那些选项配置一下。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image44.webp)

####### 模组的路径与命令行方式不同
需要注意的是，这个相对路径与直接敲CMake命令配置的参数不同，这里是`../opencv_contrib-4.11.0/modules`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image45.webp)

####### OPENCV_PYTHON3_VERSION参数的类型错了
在cmake-gui中不知道为何OPENCV_PYTHON3_VERSION参数的类型成了布尔型。

需要手动改为字符串型数据。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image46.webp)

上图就是问题所在，这里竟然是个布尔值。

先删掉该选项。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image47.webp)

再重新添加一个该选项。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image48.webp)

```Dockerfile

# 查看python3版本
python3 --version
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image49.webp)

在下面填上3.12，后面的小版本号不用填。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image50.webp)

#### Makefiles编译
后面 -j 参数的意义是使用所有 CPU 参与编译。如果启用了 CUDA ，生成过程会比较慢，请耐心等待。编译期间如果出错往往是编译器、系统库、三方库的版本兼容性问题，找问题的难度偏大。

**请在build目录下进行下方命令。**

```bash

# 内存小于16GB
make all

# 内存等于16GB
make all -j$(( $(grep -c ^processor /proc/cpuinfo) / 2 ))

# 内存大于32GB
make all -j$(grep -c ^processor /proc/cpuinfo)

# 或者自行规定线程数量（比如16线程）
make all -j16
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image51.webp)

全核跑编译

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image52.webp)

如图才是真编译成功，没有错误。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image53.webp)

生成结束后，执行以下命令进行安装：

`$(grep -c ^processor /proc/cpuinfo)`变量为CPU线程的数量。(这样可以使CPU全力多线程进行编译)

```bash

# 内存小于16GB
sudo make install

# 内存等于16GB
sudo make install -j$(( $(grep -c ^processor /proc/cpuinfo) / 2 ))

# 内存大于32GB
sudo make install -j$(grep -c ^processor /proc/cpuinfo)

# 或者自行规定线程数量（比如16线程）
sudo make install -j16
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image54.webp)

无报错则安装成功

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image55.webp)

#### 配置OpenCV环境变量ENV
1.  可能需要配置一下lib的路径：

```bash
vim ~/.bashrc
```

在最底下加上下方的内容

```bash

# 设置 LD_LIBRARY_PATH
export LD_LIBRARY_PATH="/usr/local/lib:$LD_LIBRARY_PATH"
```

2.  其他的不用配置，开箱即用(可以配置一下pkg-config)，

检查是否安装成功：

```bash
opencv_version
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image56.webp)

#### 测试OpenCV_CUDA(CMake程序示例)
1.  CMake是一定要掌握的，请看下方文档学习:[CMake C/C++编译环境配置](/wiki/2023-10-05-cplusplus-jiao-xue/2200-cmake-gong-cheng-mu-ban)

2.  如图测试完毕：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image57.webp)

3.  下方是实例工程我已上传至GitHub供大家下载：(下方示例工程里的CMake模板不是最新的，可能不如新版功能齐全，不如新版各方面设计的更周到，如果想找最新版模板请看[CMake C/C++编译环境配置](/wiki/2023-10-05-cplusplus-jiao-xue/2200-cmake-gong-cheng-mu-ban))

https://github.com/tungchiahui/opencv_cuda_test

```bash

# 克隆源码
git clone https://github.com/tungchiahui/opencv_cuda_test.git

# cd进工程
cd opencv_cuda_test

# cd进build目录
cd build

# 进行cmake编译
cmake ..

# 进行make编译+进行make install安装(最大线程)
make install -j$(grep -c ^processor /proc/cpuinfo)

# 给予脚本执行权限
sudo chmod a+x ../script/setup_vinci_emis.bash
sudo chmod a+x ../script/vinci_emis
sudo chmod a+x ../install/.setup.bash

# 执行环境脚本
source ../script/setup_vinci_emis.bash

# 执行demo1二进制程序
../script/vinci_emis run demo1
```

**或者**直接点击`Run`，`Start Debugging`或者`Run Without Debugging`都可以。(已经将launch.json及task.json全部配置好了)(发现bug及时call我，call我之前，请看最新CMake模板是否已经修复了该bug，若未修复，再call我)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image58.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image59.webp)

测试完毕

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image60.webp)

#### 常见问题
##### cmake -jx编译遇到operator!=或权重weight相关问题
      这个问题大多发生在ubuntu20.04或者cuda12.2上，编译时添加contrib库，对应ros版本为noetic，opencv版本为4.8.0。

      github上的讨论：

https://github.com/ros-perception/vision_opencv/tree/kinetic

根据GitHub中的解决方案，将对应报错.hpp文件中的相应代码修改（若只是有一些WARNING那大可不必修改）：

`114 opencv/modules/dnn/src/cuda4dnn/primitives/normalize_bbox.hpp 中 if (weight != 1.0)`改为 `if (weight != static_cast<T>(1.0))`

`124 opencv/modules/dnn/src/cuda4dnn/primitives/region.hpp 中if (nms_iou_threshold > 0)`

改为 `if (nms_iou_threshold > static_cast<T>(0))`

再次编译

```bash
make -j16
```

##### ros原装opencv与自己搭建的opencv_cuda冲突的问题
原因：ros自带的cv_bridge自动链接ros的opencv，而ros自带的opencv没有cuda加速，故报错。

Ubuntu允许多版本 opencv共存，不建议直接卸载opencv，可能导致相关环境异常。

解决方案：额外配置一个版本的cv_bridge进行opencv的链接

###### ROS1
解决方案：

在https://github.com/ros-perception/vision_opencv/tree/kinetic中下载对应版本的cv_bridge

先对cv_bridge中的CmakeList.txt进行修改，OpencvDIR对应自己的opencv安装路径，并将修改包名：

```cmake
project(cv_bridge_480)#修改为你的包名，加个版本号就可以
set(OpenCV_DIR "/home/liu/opencv/opencv-4.8.0")
find_package(OpenCV 4.8.0 REQUIRED
  COMPONENTS
    opencv_core
    opencv_imgproc
    opencv_imgcodecs
  CONFIG
)

```

修改package.xml中的包名

```xml
  <name>cv_bridge_480</name>
```

此时将cv_bridge作为一个ros功能包进行编译，把包整体复制进你工作空间的src中进行编译

```xml
cp -rf ./cv_bridge ~/Yolo_Tensorrt_Demo/demo01_test/src
catkin_make
```

然后就可以将cv_bridge作为功能包使用了，在你原本的包CmakeLists中添加

```cmake
find_package(cv_bridge_480)
```

package.xml:

```xml
<build_depend>cv_bridge_480</build_depend>
<build_export_depend>cv_bridge_480</build_export_depend>
<exec_depend>cv_bridge_480</exec_depend>
```

到这里自定义的cv_bridge包就配好了

如果你还想在vscode中使用代码补全，添加路径一直到cv_bridge包的include就可以,我这里是另一个工作空间，都一样。

```json
"includePath": [
    "/home/liu/cv_bridge_ws/src/cv_bridge/include",
    "/home/liu/cv_bridge_ws/src/cv_bridge"

]
```

###### ROS2
详见[ROS2机器人操作系统教程](/wiki/2023-12-30-ros2-tutorial)中CV_Bridge章节.

##### opencv编译爆内存的问题
解决方案：采用多核编译，一般采用make -j16 （这里16是CPU线程数，可以根据实际情况进行调整）即可解决，cpu线程越多，编译速度就越快

###### windows中：
Mingw：

```PowerShell
mingw64-make -j16
```

值得注意的是，mingw并不支持windows上的opencv_contrib编译，这在configure一开始就会提示

Vs：

https://blog.csdn.net/hollyholly5/article/details/68062513

###### linux中：
```bash
make -j$(grep -c ^processor /proc/cpuinfo)
```

### Windows
详见[使用OpenCV推理Yolov8模型（C++）](https://sdutvincirobot.feishu.cn/wiki/Lm6XwbEJyi099ykqyV4cvirPn2f)
