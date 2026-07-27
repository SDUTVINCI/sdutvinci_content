---
title: "安装必备配置"
---

###   软件包换源（以Debian系的发行版为例）
打开下方网站（注意，该Ubuntu仓库版本是X86、amd64的仓库，ARM的仓库并非该网站（99.9%的人的电脑是X86的，很多工控机是ARM的，但工控机也有X86的，请看[Vinci机器人队单片机教程](/wiki/2023-10-09-stm32-freertos-jiao-cheng)中讲的了解一下，或者自行百度。）

下方这几个镜像源选一个即可。

https://mirrors.bfsu.edu.cn/help/ubuntu/

https://mirrors.cloud.tencent.com/help/ubuntu.html

https://developer.aliyun.com/mirror/ubuntu

https://mirrors.sustech.edu.cn/help/ubuntu.html#introduction

https://mirrors.ustc.edu.cn/help/ubuntu.html

https://help.mirrors.cernet.edu.cn/ubuntu/

可以测速，选择出速度最快的源。

```bash
curl https://mirrors.cernet.edu.cn/oh-my-mirrorz.py | python3
```

在北方（山东）测速如下：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image201.webp)

在南方（广东）测速如下：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image202.webp)

在南北都比较快的是`北京外国语大学BFSU`和`南方科技大学SUSTech`，不想测速可以无脑选，也可以测速选择最好的源。

####   Ubuntu22.04(及Debian10)及以下版本
输入以下命令

```bash
sudo apt update
sudo apt install vim
sudo vim /etc/apt/sources.list
```

在以下界面，用键盘按`ggdG`（注意区分大小写）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image203.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image204.webp)

选择好Ubuntu版本后，

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image205.webp)

粘贴进去

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image206.webp)

按一下ESC，然后英文冒号，输入 `:wq!`然后回车

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image207.webp)

输入以下命令

```bash
sudo apt update
```

下方出现对应学校源网站则成功

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image208.webp)

至此换源结束

####   Ubuntu24.04(及Debian12)及以上版本
```bash
sudo apt update
sudo apt install vim
sudo rm -rf /etc/apt/sources.list
sudo rm -rf /etc/apt/sources.list.d/**

sudo vim /etc/apt/sources.list.d/ubuntu.sources
```

选择好Ubuntu版本后，复制下方的内容。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image209.webp)

粘贴进去

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image210.webp)

按一下ESC，然后英文冒号，输入 `:wq!`然后回车

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image211.webp)

输入以下命令

```bash
sudo apt update
```

这里被替换成对应学校则成功

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image212.webp)

#### Fedora41(DNF5)及之后
https://mirrors.bfsu.edu.cn/help/fedora/

https://help.mirrors.cernet.edu.cn/fedora/

```bash
sed -e 's|^metalink=|#metalink=|g' \
    -e 's|^#baseurl=http://download.example/pub/fedora/linux|baseurl=https://mirrors.bfsu.edu.cn/fedora|g' \
    -i.bak \
    /etc/yum.repos.d/fedora.repo \
    /etc/yum.repos.d/fedora-updates.repo
```

### 卸载恶心的snap(Ubuntu的衍生版)
**⚠️官方默认原版Ubuntu** **Gnome****不要卸载snap,会g.原版Ubuntu只能继续让snap恶心你，没办法。(现在貌似非原版ubuntu也不允许卸载了，恶心坏了）**

所以我一直都推荐Ubuntu KDE（也就是Kubuntu）。

#### 安装非firefox浏览器
卸载snap前，请先安装一个浏览器（以Google Chrome为例）(这里是因为Ubuntu默认安装的是snap版本的FireFox)

以下是Google上海服务器的Chrome官网（无需挂梯）

https://www.google.cn/chrome/index.html

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image213.webp)

咱们这里是Ubuntu所以是Debian系的系统，所以选deb扩展名的程序包。（也就是红色框框）

红帽系则需要安装的是rpm扩展名程序包。（也就是蓝色框框）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image214.webp)

```bash
#某些发行版中，可能Downloads是中文下载，所以就需要  cd ~/下载
cd ~/Downloads
#下方chrome安装包名不一定是这个，根据名字来看
sudo apt install ./google-chrome-stable_current_amd64.deb
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image215.webp)

#### 查看snap包应用数量
```bash

# 老版
sudo apt update
sudo apt install neofetch
neofetch

# 新版
sudo add-apt-repository ppa:zhangsongcui3371/fastfetch
sudo apt update
sudo apt install fastfetch
fastfetch

# windows 想看系统信息的话
winget install fastfetch
#重启 powershell
fastfetch
```

这里显示，一共有3162个debian的程序，7个snap程序

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image216.webp)

#### 移除snap应用
系统在启动时，会自动启动Snap相关服务，我们先禁用掉这些服务

```bash
sudo systemctl disable snapd.service
sudo systemctl disable snapd.socket
sudo systemctl disable snapd.seeded.service
```

然后查看snap应用列表

根据你安装的选择不同（最小安装或普通安装），有[不同的](https://so.csdn.net/so/search?q=%E4%B8%8D%E5%90%8C%E7%9A%84&spm=1001.2101.3001.7020)Snap软件会被预装到系统，在删除Snap服务之前，我们需要移除Snap安装的这些

```bash

# 查询当前系统上snap安装了哪些app
snap list
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image217.webp)

卸载掉图中所有应用，按照**先删除应用软件，再删除非应用软件**的顺序进行删除

```bash
#移除snap-store，如果是Kubuntu，则没有这个东西，测不需要卸载
sudo snap remove snap-store
#移除firefox浏览器 gnome-42-2204 gtk-common-themes(可以一次性移除多个)
sudo snap remove firefox gnome-42-2204 gtk-common-themes
#移除其它...

#移除core22,bare以及snapd（下面这些需要最后再移除，否则会报错）
sudo snap remove core22
sudo snap remove bare
sudo snap remove snapd
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image218.webp)

输入以下命令查看是否还有snap包

```bash
snap list

# 老版
neofetch

# 新版
fastfetch
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image219.webp)

#### 移除snap
在删除掉[Snap安装](https://so.csdn.net/so/search?q=Snap%E5%AE%89%E8%A3%85&spm=1001.2101.3001.7020)的软件后，下一步就是把Snap本身也删除掉，这里需要使用Apt来实现（谨慎卸载，可视化页面容易崩）

```bash
 #使用apt移除掉snap
sudo apt autoremove --purge snapd
#移除snapd的一些目录
sudo rm -rf /var/cache/snapd
sudo rm -rf ~/snap
```

至此，其实snap已经被删除掉了。但是这个并不足够，如果你使用apt安装一些软件 `sudo apt install firefox`时，会自动下载并重新安装snap服务。因为Ubuntu源中的一些软件已经是snap版本，而非deb版本，下载snap版本时，会自动检查并在必要时重新安装snap服务。这也是Canonical为了推广自己的Snap Store而做的一些额外的努力吧。(这一点最被很多人不喜欢)

#### 禁止系统自动安装snap
我们可以利用APT可配置禁用安装哪些依赖的特性，来实现禁止重新自动安装Snap服务

```bash
sudo vim /etc/apt/preferences.d/nosnap.pref
```

按insert进入编辑模式，然后复制以下内容，最后按ESC，切换为英文输入法模式，然后敲 `:wq` 回车

```bash
Package: snapd
Pin: release a=*
Pin-Priority: -10
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image220.webp)

#### 测试是否成功
```bash
sudo apt update
sudo apt install firefox
```

这里提示snapd无法被安装，就证明成功了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image221.webp)

#### 重新安装FireFox浏览器（没大有必要）
我个人其实更偏好Google Chrome浏览器，浏览器是从Chrome官网下载deb进行安装的。所以我有浏览器了，就没必要安装火狐浏览器了，

但如果你确实喜欢Firefox，在删除掉Snap后，其实没法再通过Snap或Apt来安装Firefox了，而Firefox官网提供的下载，又没有deb包，没有桌面快捷方式，不是非常方便。

所以，你可以考虑使用Mozilla提供的源来安装debian版本的Firefox

```bash

# 添加Mozilla提供的源
sudo add-apt-repository ppa:mozillateam/ppa

# 安装Firefox
sudo apt update
sudo apt install firefox
```

#### 跨Ubuntu大版本更新
跨Ubuntu大版本更新，是指从Ubuntu22.04LTS更新到Ubuntu24.04LTS这种大版本更新。

跨Ubuntu大版本更新时，建议恢复snap，等更新完毕后再删掉snap，以防止Canonical公司从中作妖。

```bash
#删掉禁止安装snap的配置文件
sudo rm -rf /etc/apt/preferences.d/nosnap.pref
sudo apt update

sudo apt dist-update
```

### 切换系统语言到中文
（实在不喜欢英文的可以切换，以KDE6为例子，其他类似）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image222.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image223.webp)

### 中文输入法
#### 搜狗(不建议)(Ubuntu22.04 **及** 以下)
官网下载搜狗输入法

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image224.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image225.webp)

打开终端输入如下

```bash
cd ~/Downloads/
sudo apt install ./sogoupinyin_4.2.1.145_amd64.deb
```

打开Fcitx

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image226.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image227.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image228.webp)

添加搜狗输入法

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image229.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image230.webp)

将搜狗输入法设置为唯一输入法

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image231.webp)

#### Fcitx5(Ubuntu22.04 **及** 以上、Debian12及以上、Fedora)
1.  卸载旧输入法

```bash

# Ubuntu或者Debian
sudo apt purge fcitx* ibus*

# Fedora

# 应该不用干任何事，fedora41基本都移除了
```

2.  安装Fcitx5

```bash

# Ubuntu22.04及以上、Debian12及以上
sudo apt install fcitx5 fcitx5-chinese-addons

# Ubuntu20.04
sudo add-apt-repository ppa:zhsj/fcitx5
sudo apt update
sudo apt install fcitx5 fcitx5-chinese-addons

# Fedora
sudo dnf install fcitx5 fcitx5-chinese-addons fcitx5-autostart
```

3.  Reboot System重启系统

4.  启动Fcitx5

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image232.webp)

5.  修改一些设置

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image227.webp)

将Pinyin和Keyboard - English US加入到左边。(如果找不到，不要勾选右边的仅显示当前语言)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image233.webp)

添加快捷键以便于更好切换中英文

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image234.webp)


6. 配置环境变量
    1. 如果你是KDE+Wayland,则还需要下面这个步骤（Kubuntu26.04及以上，Debian13及以上，Rocky10及以上，Fedora等）

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image235.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image236.webp)

    2. 如果你是KDE+X11,则还需要下面这个步骤(大多数发行版的低版本,可以用`fastfetch`看具体的用的`x11`还是`wayland`)

    ```bash
    vim ~/.config/plasma-workspace/env/fcitx5.sh
    ```

    ```bash
    export GTK_IM_MODULE=fcitx
    export QT_IM_MODULE=fcitx
    export XMODIFIERS="@im=fcitx"
    export SDL_IM_MODULE=fcitx
    export INPUT_METHOD=fcitx
    ```

    再重启

7.  重启

```bash
sudo shutdown -r now
```

8.  使用Fcitx5工具箱优化Fcitx5(进入Github自己看说明书安装)

https://github.com/debuggerx01/fcitx5_customizer

```Plain Text

# Ubuntu

# 直接在线执行工具箱优化
curl -sSL https://fcitx5.debuggerx.com/fcitx5_customizer.sh | bash -s -- recommend

# Fedora

# 直接在线执行工具箱优化
curl -sSL https://fcitx5.debuggerx.com/fcitx5_customizer.sh | bash -s -- recommend
```

9.  再重启

```bash
sudo shutdown -r now
```


### pip3源替换
非Debian系发行版需要做。

像Ubuntu、Debian这类使用apt工具的发行版不需要此操作。

使用dnf的系统（如Fedora，RHEL，RockyLinux）也可能可以不用做。

https://mirrors.tuna.tsinghua.edu.cn/help/pypi/

按图中顺序敲，但是不出意外的话，会报错，因为大部分发行版不自带pip，所以请接着往下看。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image237.webp)

```bash
#安装pip3
sudo apt update
sudo apt-get install python3-pip

#更新pip3
python3 -m pip install -i https://mirrors.bfsu.edu.cn/pypi/web/simple --upgrade pip

#设置pip源为北京外国语大学镜像源
pip3 config set global.index-url https://mirrors.bfsu.edu.cn/pypi/web/simple

#测试
python3 -m pip install --upgrade pip
```

成功的话，会看到Looking后面是bfsu字样。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image238.webp)

### RPMFusion安装并换源
**（仅红帽系，即Ubuntu等Debian系的不用弄）**

https://mirrors.bfsu.edu.cn/help/rpmfusion/

1.  先用下方命令查询你是否开启了rpmfusion.

```bash
rpm -qa | grep rpmfusion
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image239.webp)

2.  如果开启了的话，就先卸载rpmfusion

```bash
sudo dnf remove rpmfusion-free-release rpmfusion-nonfree-release
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image240.webp)

3.  安装北京外国语大学的rpmfusion

    1.  Fedora

    ```bash

    # 安装免费仓库非免费仓库
    sudo dnf install --nogpgcheck https://mirrors.bfsu.edu.cn/rpmfusion/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.bfsu.edu.cn/rpmfusion/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
    ```
    3.  RHEL、RockyLinux

    ```bash

    # 安装免费仓库非免费仓库
    sudo dnf install --nogpgcheck https://mirrors.bfsu.edu.cn/rpmfusion/free/fedora/rpmfusion-free-release-$(rpm -E %rhel).noarch.rpm https://mirrors.bfsu.edu.cn/rpmfusion/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %rhel).noarch.rpm
    ```

4.  把其他rpmfusion仓库也改为bfsu源

```bash
sudo sed -e 's!^metalink=!#metalink=!g' \
         -e 's!^mirrorlist=!#mirrorlist=!g' \
         -e 's!^#baseurl=!baseurl=!g' \
         -e 's!https\?://download1\.rpmfusion\.org/!https://mirrors.bfsu.edu.cn/rpmfusion/!g' \
         -i /etc/yum.repos.d/rpmfusion*.repo
```

5.  检查修改好的仓库

```bash
grep "mirrors.bfsu" /etc/yum.repos.d/rpmfusion*.repo
```

可以看到不止free和nonfree的源换了，英伟达和steam也换了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image241.webp)

6.  刷新仓库缓存

```bash
sudo dnf clean all
sudo dnf makecache
```

### 时间不同步
双系统会导致Ubuntu比Windows的时间快8小时，而Windows的时间比Ubuntu慢8小时，所以需要解决系统时间同步的问题。还是第一次分享的那个视频，后面有一节讲到了时间同步的问题。

（ **建议：打开字幕观看，因为有些莫名出现的问题，会在弹幕里有解答** ）

https://www.bilibili.com/video/BV1554y1n7zv/?vd_source=ceb9c29ca8792358f229b53eef0c1448

### 显卡驱动安装
#### 卸载显卡驱动(卸载干净)
```bash

# 如果使用了.run文件进行安装的，再使用下面命令卸载干净
sudo /usr/bin/nvidia-uninstall

# 如果是使用apt安装的驱动
sudo apt-get remove --purge nvidia*

# 如果是使用dnf安装的驱动
sudo dnf remove nvidia*
```

#### 常见问题（遇到问题再翻上来看这里）
1.  禁用开源驱动nouveau，nouveau经常会引起卡顿黑屏花屏，所以要禁用

```bash
sudo vim /etc/modprobe.d/blacklist-nouveau.conf
```

blacklist-nouveau.conf文件内容如下：

```bash
blacklist nouveau
blacklist lbm-nouveau
options nouveau modeset=0
alias nouveau off
alias lbm-nouveau off
```

接着运行下方命令

```bash

# debian系选这个（Ubuntu等）
sudo update-initramfs -u

# 红帽系选这个（Fedora，Rocky等）
sudo dracut --force
```
```Plain Text

# 重启
sudo reboot
```

重启后，查询nouveau是否还开着，如果什么都不出现，说明禁用成功。

```bash
lsmod | grep nouveau
```

2.  无法调节屏幕亮度，无法使用HDMI插口(这是因为xorg文件和grub没配置)( **仅X11** ，例如Ubuntu24.04及以下，像Fedora等使用wayland的不能用下方的配置。)

先配置xorg

```bash
sudo vim /usr/share/X11/xorg.conf.d/10-nvidia.conf
```

并把10-nvidia.conf里的内容修改为下方内容并保存，然后重启即可。

```bash
Section "OutputClass"
   Identifier "nvidia"
   MatchDriver "nvidia-drm"
   Driver "nvidia"
   Option "AllowEmptyInitialConfiguration"
   Option "PrimaryGPU" "yes"
   ModulePath "/usr/lib/x86_64-linux-gnu/nvidia/xorg"
EndSection

# 或者

Section "OutputClass" 
    Identifier     "nvidia" 
    MatchDriver    "nvidia-drm" 
    Driver         "nvidia" 
    Option         "RegistryDwords" "EnableBrightnessControl=1;PrimaryGPU=1" 
    Option         "Backlight" "nvidia_0" 
    ModulePath     "/usr/lib/x86_64-linux-gnu/nvidia/xorg" 
EndSection
```

再配置grub

```bash
sudo vim /etc/default/grub
```

grub参数添加上下面这些参数，注意是添加啊，可不是改为这样，原来的参数要保留。

```bash
GRUB_CMDLINE_LINUX_DEFAULT="nvidia.NVreg_EnableBacklightControl=1 acpi_backlight=native video.use_native_backlight=0"
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image242.webp)

```bash

# debian系选这个（Ubuntu等）
sudo update-initramfs -u
#或
sudo update-grub

# 红帽系选这个（Fedora，Rocky等）
sudo dracut --force

sudo reboot
```

重启即可

3.  安装N卡驱动提示错误

```Plain Text
Unable to find the kernel source tree for the currently running kernel.  Please make sure you have installed the kernel source files for your kernel and that they are properly configured; on Red Hat Linux systems, for example, be sure you have the 'kernel-source' or 'kernel-devel' RPM installed.If you know the correct kernel source files are installed, you may specify the kernel source path with the '--kernel-source-path' command line option.
```

1.  检查当前运行的内核版本：

运行以下命令以查看你当前正在使用的内核版本：

```bash
uname -r
```

2.  安装内核开发文件：

确保你已经安装了与当前内核版本相匹配的 `kernel-devel` 包。运行以下命令来安装：

```bash
sudo dnf install kernel-devel-$(uname -r)
```

这将自动安装与你当前内核版本匹配的内核开发包。

3.  安装内核源代码（可选）：

如果需要访问完整的内核源代码，你可以运行以下命令来安装它：

```bash
sudo dnf install kernel-headers kernel-source
```

4.  检查安装：

安装完成后，确认路径是否正确。内核源文件通常位于 `/usr/src/kernels/` 目录下，你可以使用以下命令来检查该目录是否存在：

```bash
ls /usr/src/kernels/
```

如果仍然遇到问题，可以通过 `--kernel-source-path` 选项手动指定内核源文件路径。

#### 安装方式推荐
下面有三个方式安装驱动，教程基本都写了，分别是通用方式，APT方式，DNF方式。

新手建议使用APT方式（Ubuntu或者Debian）或者DNF方式（Fedora或者RockyLinux）安装 **NVIDIA驱动** ，然后CUDA用通用方式安装，CUDNN依然用APT或者DNF方式安装。

**（你是新手的话，老老实实按照下方的表格推荐的方式进行安装，别瞎整活）**

| 各发行版推荐使用的方式 |
|:---|
|  | Ubuntu | Debian | Fedora | RockyLinux |
| NVIDIA驱动 | APT | APT | DNF | DNF |
| CUDA | 通用或APT | 通用或APT | 通用或DNF | 通用或DNF |
| cuDNN | APT | APT | DNF | DNF |

#### APT安装（方式一：适合Debian,Ubuntu等）
##### 安装显卡驱动
这种方式安装的显卡驱动，以后更新内核后都不用再重新安装显卡驱动了。

###### Ubuntu
```bash

# 安装英伟达驱动
sudo apt install nvidia-driver-版本号

# 重启 (必须重启)
sudo shutdown -r now

# 检查驱动是否安装成功
nvidia-smi
```

###### Debian
```bash

# 安装内核头文件和编译工具
sudo apt install -y linux-headers-amd64 build-essential

# 安装英伟达驱动
sudo apt install nvidia-driver

# 重启
sudo shutdown -r now

# 检查驱动是否安装成功
nvidia-smi
```

##### 安装CUDA
（请使用方式一通用法）

##### 安装cuDNN
选择CuDNN v9版本（该版本安装和CUDA差不多，可以看着官网教程来安装）

https://developer.nvidia.com/cudnn-archive

#### DNF安装（方式二：适合Fedora,Rocky等）
##### 安装显卡驱动
如果我们使用通用方式安装显卡驱动，会发现每次Feodra更新内核后，显卡驱动都会掉，如何解决这个办法呢？请采用DNF安装显卡驱动。

这种方式安装的显卡驱动，以后更新内核后都不用再重新安装显卡驱动了。（安装新内核后，重启时会自动触发 `akmod` 的构建流程。此时，系统会生成与新内核匹配的 NVIDIA 驱动模块。）

注意，安装驱动之前，要确定自己没用.run等方式安装了驱动。可以用nvidia-smi命令查看，如果提示没安装驱动就可以。如果提示当前已经安装了驱动，请先卸载驱动(卸载教程上面应该有）或者等下一次内核更新(内核更新会掉.run安装的驱动）

###### Fedora
```bash

# 启用 RPM Fusion 仓库（北京外国语源）
sudo dnf install --nogpgcheck https://mirrors.bfsu.edu.cn/rpmfusion/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.bfsu.edu.cn/rpmfusion/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

# 安装驱动和内核工具
sudo dnf install akmod-nvidia  # 自动适配内核的驱动（重点）
sudo dnf install xorg-x11-drv-nvidia-cuda  # 包含 CUDA 支持

# 重启并验证(这种方式必须重启)
sudo reboot
nvidia-smi  # 检查驱动版本（如 570.86.16）
```

##### 安装CUDA
通过dnf安装完显卡驱动后，也可以使用通用法安装CUDA，这种方法也很方便（CUDA在更新内核后不会掉的）。

也可以使用下面这种dnf的方式进行CUDA安装。

（此教程以Fedora41和RTX3060Laptop为例）(Rocky,RHEL也类似)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image243.webp)

1.  先查看显卡驱动所支持的最高CUDA版本

```bash
nvidia-smi
```

左边是英伟达驱动版本，右边是所支持CUDA的最高版本，注意是最高版本，比这个版本低的CUDA都是可以安装的。(不用追求最高，稳定即可，虽然都很稳定)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image244.webp)

2.  下载CUDA并安装CUDA

https://developer.nvidia.com/cuda-toolkit-archive

选择一个版本，这里以CUDA12.4为例（绿色框住的都是我这台电脑对应可安装的版本，而红色框则不能）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image245.webp)

我这里是Intel CPU(X86_64架构或者叫amd64架构)，所以选择X86_64。（AMD和Intel全是X86的，如果分不清，请看[Vinci机器人队单片机教程](/wiki/2023-10-09-stm32-freertos-jiao-cheng)）

我是Fedora41，所以选择了Fedora41 ,(你是RockyLinux要选择RockyLinux)如下图所示:

https://developer.nvidia.com/cuda-toolkit-archive

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image246.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image247.webp)

按照网站对应的部分敲，如我上图里是这样的：

```bash
wget https://developer.download.nvidia.com/compute/cuda/12.8.0/local_installers/cuda-repo-fedora41-12-8-local-12.8.0_570.86.10-1.x86_64.rpm
sudo dnf install ./cuda-repo-fedora41-12-8-local-12.8.0_570.86.10-1.x86_64.rpm
sudo dnf clean all
sudo dnf -y install cuda-toolkit-12-8
```

进行环境配置：

```bash
sudo vim ~/.bashrc
```

复制下面这一串到~/.bashrc文件中，并保存，不会用vim编辑器的请自行百度。

```bash
export PATH=/usr/local/cuda-12.4/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-12.4/lib64:$LD_LIBRARY_PATH
```

或者（更建议下方这个）

```bash
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image248.webp)

```bash

# 刷新当前终端的环境变量
source ~/.bashrc

# 验证CUDA是否安装成功
nvcc -V
```

出现下图这种则安装成功。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image249.webp)

##### 安装cuDNN
选择CuDNN v9版本（该版本安装和CUDA差不多，可以看着官网教程来安装）

https://developer.nvidia.com/cudnn-archive

#### 通用方式（方式三：脚本或压缩包方式）
遇到问题请看 **常见问题** 那节，看看有没有对应解决方案。（对于Ubuntu，Debian，Fedora，Rocky的常见问题应该是全的）

##### 安装显卡驱动
使用这种方式安装，需要先禁用掉nouveau，请先往下翻，找到**常见问题（在上面一两小节）**那节内容里的禁用nouveau，将nouveau禁用。

先下载N卡驱动，下载.run扩展名的

https://www.nvidia.cn/drivers/lookup/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image250.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image251.webp)

用以下命令停止可视化桌面环境（用ctrl alt f\*也可以）***（也可以不禁用桌面环境，直接进行下一步。）***

```bash

# 通用的命令（sudo telinit 5是打开图形界面）
sudo telinit 3

# ubuntu 使用下方命令
sudo service lightdm stop

# Fedora 使用下方命令
sudo systemctl isolate multi-user.target
```

之后会进入一个新的命令行会话，使用当前的用户名密码登录。(不用使用root用户)

```bash

# 编译环境+准备工作
sudo apt install build-essential

# 进入到.run目录中
cd /home/tungchiahui/Downloads/

# 给驱动文件增加可执行权限：
sudo chmod a+x NVIDIA-Linux-x86_64-550.107.02.run

# 然后执行安装：
sudo ./NVIDIA-Linux-x86_64-550.107.02.run

# 如果有异常则：(一般不用下方这条，会导致OPENGL没法被安装，这样ROS有些功能无法使用)
sudo ./NVIDIA-Linux-x86_64-550.107.02.run  --no-opengl-files
```

安装完毕重启即可

```bash

# 重启 
sudo reboot

# 检查驱动是否安装成功
nvidia-smi
```

##### 安装CUDA
（此教程以Debian 12 Bookworm和RTX3060Laptop为例）(Ubuntu，Fedora也类似)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image252.webp)

1.  先查看显卡驱动所支持的最高CUDA版本

```bash
nvidia-smi
```

左边是英伟达驱动版本，右边是所支持CUDA的最高版本，注意是最高版本，比这个版本低的CUDA都是可以安装的。(不用追求最高，稳定即可，虽然都很稳定)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image253.webp)

2.  下载CUDA并安装CUDA

https://developer.nvidia.com/cuda-toolkit-archive

选择一个版本，这里以CUDA12.4为例（绿色框住的都是我这台电脑对应可安装的版本，而红色框则不能）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image254.webp)

我这里是Intel CPU(X86_64架构或者叫amd64架构)，所以选择X86_64。（AMD和Intel全是X86的，如果分不清，请看[Vinci机器人队单片机教程](/wiki/2023-10-09-stm32-freertos-jiao-cheng)）

我是Debian12，所以选择了Debian12 ,(你是Ubuntu要选择Ubuntu)如下图所示，建议选择runfile文件进行安装。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image255.webp)

将最下方框框里的命令敲入终端

```bash

# 在当前文件夹下创建一个ttt的子文件夹
mkdir -p ./ttt

# 进入ttt文件夹
cd ttt

# 输入复制的第一条命令（用来下载cuda的runfile文件）
wget https://developer.download.nvidia.com/compute/cuda/12.4.0/local_installers/cuda_12.4.0_550.54.14_linux.run

# 给予权限
sudo chmod a+x ./cuda_12.4.0_550.54.14_linux.run

# 运行脚本
sudo ./cuda_12.4.0_550.54.14_linux.run
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image256.webp)

正在加载中：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image257.webp)

输入accept接受即可

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image258.webp)

因为我们已经安装了驱动，所以删掉该项，Install即可。（ **一定不要勾选安装驱动** ，新手避免非必要的麻烦）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image259.webp)

等待片刻后，一般不出红色字体就是安装成功了，可以读读英语确认一下。(顺便确定一下位置)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image260.webp)

3.  配置环境

根据上方确认一下路径，比如我上面红色框起来的为

`Please make sure that` `- PATH includes /usr/local/cuda-12.4/bin` `- LD_LIBRARY_PATH includes /usr/local/cuda-12.4/lib64, or, add /usr/local/cuda-12.4/lib64 to /etc/ld.so.conf and run ldconfig as root`

如果刚才忘记查看了，也可以使用下方命令查看路径：

```bash
which nvcc
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image261.webp)

所以我进行以下操作：

```bash
sudo vim ~/.bashrc
```

复制下面这一串到~/.bashrc文件中，并保存，不会用vim编辑器的请自行百度。

```bash
export PATH=/usr/local/cuda-12.4/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-12.4/lib64:$LD_LIBRARY_PATH
```

或者

```bash
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image262.webp)

```bash

# 刷新当前终端的环境变量
source ~/.bashrc

# 验证CUDA是否安装成功
nvcc -V
```

出现下图这种则安装成功。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image263.webp)

##### 安装cuDNN**（基本废弃，请往下看apt和dnf安装CuDNN的方式）**
1.  据安装好的CUDA版本选择合适的cuDNN版本进行下载，注意这里是需要注册登录的：

https://developer.nvidia.cn/rdp/cudnn-archive#a-collapse805-111

因为我是CUDA12.4，所以下方红圈版本都是可以安装的，越新越好。（上方网站最高版本也就v8.9.7,还有更新的版本，可以直接往下看apt和dnf方式安装）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image264.webp)

红色是X86架构的所有Linux通用的，所以我选择红色的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image265.webp)

绿色的是X86架构的Ubuntu，是Ubuntu且CPU为X86架构的可以选择下载。

蓝色的是arm64(aarch64)架构的Ubuntu，是Ubuntu且CPU为arm64(aarch64)架构的可以选择下载。

**我这里选择所有Linux X86_64最通用的办法：**

下载好的：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image266.webp)

2.  安装cuDNN

找到该文件所在目录，并打开终端，cd到该目录。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image267.webp)

解压文件，并复制到对应位置完成安装

```bash

# 解压cuDNN文件
tar -xvf ./cudnn-linux-x86_64-8.9.7.29_cuda12-archive.tar.xz

# cd进文件夹
cd ./cudnn-linux-x86_64-8.9.7.29_cuda12-archive

# 复制cuDNN文件到CUDA目录
sudo cp include/cudnn*    /usr/local/cuda/include
sudo cp lib/libcudnn*    /usr/local/cuda/lib64

# 对比一下有没有缺文件
ls /usr/local/cuda/include/cudnn*
ls /usr/local/cuda/lib64/libcudnn*

# 给予权限
sudo chmod a+r /usr/local/cuda/include/cudnn*
sudo chmod a+r /usr/local/cuda/lib64/libcudnn*
```

3.  检查是否安装成功

```bash

# 检查cuDNN版本命令
cat /usr/local/cuda/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
```

出现下图这样的就是安装成功了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image268.webp)

4.  软链接

```bash

# 运行 ldconfig 以更新库缓存：（如果运行 ldconfig 后没有出现任何错误，说明配置已经完成。）
sudo ldconfig
```

如果出现下列提示，则需要软链接**(如果没有下列提示，则什么都不需要做)**

```Plain Text
ldconfig: /usr/local/cuda-12.4/targets/x86_64-linux/lib/libcudnn_cnn_train.so.8 is not a symbolic link  
ldconfig: /usr/local/cuda-12.4/targets/x86_64-linux/lib/libcudnn_ops_train.so.8 is not a symbolic link  
ldconfig: /usr/local/cuda-12.4/targets/x86_64-linux/lib/libcudnn_ops_infer.so.8 is not a symbolic link  
ldconfig: /usr/local/cuda-12.4/targets/x86_64-linux/lib/libcudnn_adv_infer.so.8 is not a symbolic link  
ldconfig: /usr/local/cuda-12.4/targets/x86_64-linux/lib/libcudnn_cnn_infer.so.8 is not a symbolic link  
ldconfig: /usr/local/cuda-12.4/targets/x86_64-linux/lib/libcudnn.so.8 is not a symbolic link  
ldconfig: /usr/local/cuda-12.4/targets/x86_64-linux/lib/libcudnn_adv_train.so.8 is not a symbolic link
```

运行下方的命令

```bash

# cd进入CUDA路径
cd /usr/local/cuda/targets/x86_64-linux/lib

 # 查看本路径下的需要软链接的文件名
 ls
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image269.webp)

如上图，我需要将`libcudnn_xxx_xxxxx.so.8.9.7`和`libcudnn_xxx_xxxxx.so.8`和`libcudnn_xxx_xxxxx.so`软链接

```bash

# 创建符号链接。对于每个 .so.8 文件，需要创建一个指向该文件的符号链接，通常链接的名称是不包含 .8 的文件名。
sudo ln -sf libcudnn_cnn_train.so.8.9.7 libcudnn_cnn_train.so.8
sudo ln -sf libcudnn_adv_infer.so.8.9.7 libcudnn_adv_infer.so.8
sudo ln -sf libcudnn_adv_train.so.8.9.7 libcudnn_adv_train.so.8
sudo ln -sf libcudnn_cnn_infer.so.8.9.7 libcudnn_cnn_infer.so.8
sudo ln -sf libcudnn.so.8.9.7 libcudnn.so.8
sudo ln -sf libcudnn_ops_infer.so.8.9.7 libcudnn_ops_infer.so.8
sudo ln -sf libcudnn_ops_train.so.8.9.7 libcudnn_ops_train.so.8

# 运行 ldconfig 以更新库缓存：（如果运行 ldconfig 后没有出现任何提示，说明配置已经完成。）
sudo ldconfig

# 使用 ls -l 来确认符号链接是否创建成功：(再次确认链接）
ls -l /usr/local/cuda/targets/x86_64-linux/lib/libcudnn_*.so*
```
