---
title: "Windows配置MDK6环境教程"
---

### 需要准备的软件
1.  Keil MDK5.3x及以上

2.  VScode最新版

3.  CubeMX最新版

### vcpkg安装与环境配置
1.  克隆vcpkg仓库

https://github.com/microsoft/vcpkg/tree/master

```bash
git clone https://github.com/microsoft/vcpkg.git
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image29.webp)

2.  生成vcpkg程序

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image30.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image31.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image32.webp)

3.  配置环境

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image33.webp)

点击高级系统设置

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image34.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image35.webp)

将用户环境变量和系统环境变量都配置一下

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image36.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image37.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image38.webp)

4.  测试

```bash
vcpkg --version
```

显示如下图所示，则安装成功

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image39.webp)

### 生成工程文件
1.  打开CubeMX并登录ST账号

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image40.webp)

2.  安装好Pack

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image41.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image42.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image43.webp)

3.  配置工程

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image44.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image45.webp)

### 打开工程并配置默认编译器
1.  配置默认编译器为ARMCLANG(AC6)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image46.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image47.webp)

2.  编译验证

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image48.webp)

### 下载并激活Keil MDK6插件
1.  打开VScode

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image49.webp)

2.  安装Keil Studio Pack插件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image50.webp)

3.  安装完毕后，重启VScode

4.  然后右下角会跳出来两个窗口，点击激活MDK6Community.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image51.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image52.webp)

显示这个通知即激活成功。

### MDK5工程转化为MDK6工程
点击Convert进行转化

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image53.webp)

右下角把这些要安装的pack都安装一下，有什么提示要允许的都允许一下

在安装Packs的时候，需要保证一个良好的网络环境(需要一个有魔法的网络环境)，

这个阶段会持续5-20分钟，请慢慢等待。(看你的机场速度而决定)

(只有第一次运行需要这些操作)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image54.webp)

可以打开任务管理器看cmsis.exe是否在正常下载，如果后面有网速，则说明在正常下载，等待即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image55.webp)

这个调查可以不查

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image56.webp)

如图即是安装成功

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image57.webp)

这里如果是没激活环境，则需要active environment。(图中是取消激活环境)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image58.webp)

点击转化MDK5工程

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image59.webp)

这样则显示为转化MDK6工程成功。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image60.webp)

### 编译
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image61.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image62.webp)

可以看到，通过KEIL MDK6编译后的大小和KEIL MDK5编译后的大小完全相同。

### Windows如何配置ST-Link等调试器？
Windows就更简单了，根本不用多下其他东西，只要你在MDK5上能用，基本在MDK6上也能用。

#### 添加设备选择ST-Link
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image63.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image64.webp)
