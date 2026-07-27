---
title: "OpenCV"
---

### OpenCV
[基础视觉算法-OpenCV实现](/wiki/2026-04-13-opencv-jiao-cheng)

### CV_Bridge
cv_bridge维基百科介绍:

https://wiki.ros.org/cv_bridge

https://index.ros.org/p/cv_bridge/

ROS2Humble的cv_bridge仓库链接(注意选择对应版本的分支branches):

https://github.com/ros-perception/vision_opencv/tree/humble

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1953.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1954.webp)

#### 安装
提前自己编译好带CUDA的OpenCV4,详见[电控组环境搭建大全](/wiki/2023-12-10-dian-kong-shi-jue-huan-jing-da-jian)

1.  apt安装(不建议)

由于ros自带的cv_bridge自动链接ros自带的oepncv版本,所以我们一般不会用ros2自带的cv_bridge,一般都需要自己手动编译一个cv_bridge.

```cmake

# 通用命令
sudo apt install ros-<ros2-distro>-vision-opencv

# ROS2 Humble
sudo apt install ros-humble-vision-opencv

# ROS2 Jazzy
sudo apt install ros-jazzy-vision-opencv
```

2.  源码编译安装(建议)

本教程以jazzy为例子.

首先克隆仓库,克隆jazzy,humble,rolling都可以,只要是ROS2的基本都没啥大变化.但是官方暂时没出jazzy,我就直接克隆默认的rolling了.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1955.webp)

新建一个文件夹

```cmake
mkdir ~/ros2_ws/src
cd ~/ros2_ws/src

# 克隆源码
git clone https://github.com/ros-perception/vision_opencv.git
cd vision_opencv

# 如果是humble建议:
git checkout humble
```

安装依赖:

```cmake
sudo apt install python3-numpy
sudo apt install libboost-python-dev
```

修改cv_bridge的CMakeLists

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1956.webp)

将原本的`find_package(OpenCV 4 QUIET)`改为精确匹配版本，并添加`EXACT`参数：

EXACT是未找到精确版本时，CMake会报错并终止构建.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1957.webp)

```cmake
find_package(OpenCV 4.11 EXACT QUIET
  COMPONENTS
    opencv_core
    opencv_imgproc
    opencv_imgcodecs
  CONFIG
)
```
```cmake
cd ~/ros2_ws

# 下面这三个根据情况三选一,一般是第一个colcon build --symlink-install

# 如果你曾经没编译过
colcon build --symlink-install

# 如果你只想编译cv_bridge
colcon build --symlink-install --packages-select cv_bridge

# 如果你曾经编译过一遍,则需要下列命令
colcon build --symlink-install --packages-select cv_bridge --allow-overriding cv_bridge
```

验证:

```cmake

# 列出cv_bridge链接的opencv版本
ldd ./install/cv_bridge/lib/libcv_bridge.so | grep opencv 
```

如下图,我的成功链接到411了,也就是opencv4.11版本.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1958.webp)

接下来配置环境:

```cmake
vim ~/.bashrc
```

在`source /opt/ros/jazzy/setup.bash`的下一行加入下面这句

```cmake
source ~/ros2_ws/install/setup.bash
```

输入`:wq`保存

完成安装与环境配置结束.
