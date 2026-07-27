---
title: "其他可选配置"
---

根据自己是否有需求再选择对应的功能进行配置

### KDE的Wayland和X11互相切换
```bash

# Ubuntu
sudo apt install plasma-workspace-x11 plasma-workspace-wayland

# Fedora，Rocky
sudo dnf install plasma-workspace-x11 plasma-workspace-wayland
```

然后重启，在kde的登陆界面的左下角可以选择X11还是Wayland！

### 修改DNS
使用 NetworkManager（最推荐，适用于大多数现代发行版）这是最灵活的方法，NetworkManager 是 Ubuntu、Fedora、CentOS 等大多数主流发行版的默认网络管理工具。

1.  修改全局配置文件（对所有连接生效）：创建或编辑 `/etc/NetworkManager/conf.d/dns.conf`文件：

```bash
sudo nano /etc/NetworkManager/conf.d/dns.conf
```

添加以下内容：

```bash
[main]dns=none
systemd-resolved=falserc-manager=unmanaged
```

这告诉 NetworkManager 不要管理 DNS 设置。

2.  创建静态 resolv.conf 文件：编辑 `/etc/resolv.conf`文件（如果文件不存在则创建）：

```bash
sudo nano /etc/resolv.conf
```

添加您想要的 DNS 服务器，例如：

```bash
nameserver 223.5.5.5
nameserver 119.29.29.29
options timeout:1 attempts:2
```

3.  防止文件被覆盖：为防止系统其他服务覆盖此文件，将其设置为不可更改：

```bash
sudo chattr +i /etc/resolv.conf
```

（如需修改此文件，先使用 `sudo chattr -i /etc/resolv.conf`解除锁定）

重启 NetworkManager：

```bash
sudo systemctl restart NetworkManager
```

4.  使用 `resolvectl`或 `systemd-resolve`查看是否成功（最推荐）

这是最直接的方法，可以查看系统默认的 DNS 配置。

```bash
resolvectl status
```

或者（对于旧版系统）

```bash
systemd-resolve --status
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image271.webp)

### 截图快捷键
KDE自带的截图工具贼好用，同时这个截图工具同样可以录屏，但是OBS比截图工具更加专业，所以我们一般只用这个截图工具进行截图。

QQ和微信的截图在Wayland下截至2025年还都有些小问题，所以我们选择KDE自带的截图工具。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image272.webp)

这里在捕获矩形区域的自定义快捷键在这里输入自己想用的快捷键即可。

### 搜索工具
KDE自带的搜索工具也很好用，可以快速打开自己想打开的应用。就像这样，所以我们也设置一个快捷键进行呼出。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image273.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image274.webp)

Meta这个按键就是Win键，上面是一个微软LOGO。

### 关闭SELinux
这玩意在个人电脑没必要开，纯给自己找麻烦，Android倒是可以开。

这个目前Feodra是默认开的（`enforcing`模式），所以需要关一下。

禁用有俩方式，`disabled`和`permissive`模式，下面是这俩模式的区别：

| 特性 | Disabled 模式 | Permissive 模式 |
|:---|:---|:---|
| SELinux 内核模块状态 | 完全禁用 SELinux，内核模块不运行137。 | SELinux 内核模块正常运行，但仅记录违规行为，不阻止访问126。 |
| 访问控制策略 | 不执行任何策略，所有访问均被允许29。 | 检查策略并记录违规行为，但不会阻止操作36。 |
| 日志记录 | 不生成 SELinux 相关的审计日志37。 | 记录所有违反策略的行为到 /var/log/audit/audit.log26。 |
| 安全性 | 最低，完全依赖传统 Linux DAC（自主访问控制）28。 | 高于 Disabled 模式，提供调试环境，同时保留日志分析能力36。 |
| 切换方式 | 必须修改配置文件 /etc/selinux/config 并重启系统34。 | 可通过命令 setenforce 0 临时切换，无需重启34。 |
| 适用场景 | 仅用于彻底规避 SELinux 兼容性问题（如老旧软件），不推荐长期使用19。 | 用于调试策略、排查权限问题，或在开发阶段测试 SELinux 规则367。 |

由于Disabled模式，SELinux 完全关闭，内核模块未加载。所有进程和文件的访问仅受传统 Linux 用户/组权限控制（如 `rwx`），所以我们选择Permissive模式，该模式下SELinux 策略正常加载，但仅记录违规行为（如进程尝试访问未授权文件）。

下面是修改模式的教程：

```bash
sudo vim /etc/selinux/config
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image275.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image276.webp)

```yaml

# This file controls the state of SELinux on the system. 

# SELINUX= can take one of these three values: 

#     enforcing - SELinux security policy is enforced. 

#     permissive - SELinux prints warnings instead of enforcing. 

#     disabled - No SELinux policy is loaded. 

# See also: 

# https://docs.fedoraproject.org/en-US/quick-docs/getting-started-with-selinux/#getting-started-with-selinux-selinux-states-and-modes 
# 

# NOTE: In earlier Fedora kernel builds, SELINUX=disabled would also 

# fully disable SELinux during boot. If you need a system with SELinux 

# fully disabled instead of SELinux running with no policy loaded, you 

# need to pass selinux=0 to the kernel command line. You can use grubby 

# to persistently set the bootloader to boot with selinux=0: 
# 

#    grubby --update-kernel ALL --args selinux=0 
# 

# To revert back to SELinux enabled: 
# 

#    grubby --update-kernel ALL --remove-args selinux 
# 
SELINUX=permissive 

# SELINUXTYPE= can take one of these three values: 

#     targeted - Targeted processes are protected, 

#     minimum - Modification of targeted policy. Only selected processes are protected. 

#     mls - Multi Level Security protection. 
SELINUXTYPE=targeted

```

`SELINUXTYPE`是修改selinux的策略，`targeted` 策略是 SELinux 的 默认策略，主要对 高风险的网络服务（如 Apache、MySQL、SSH）进行强制访问控制，其他非关键进程沿用传统的 Linux 用户/组权限（DAC）。这种设计在安全性和易用性之间取得了平衡,所以无需修改。

重启即可

```bash
sudo reboot
```

### UEFI启动界面rEFInd
https://www.bilibili.com/video/BV1qh411Q7d4

#### 安装rEFInd
```bash

# Ubuntu
sudo apt update
sudo apt install refind
```
```bash

# Fedora
sudo dnf makecache
sudo dnf install rEFInd
```

注：如果安装了三系统，尽量把refind安装到Linux盘的第一个系统上。

#### 确认refind是否安装成功
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image277.webp)

如图在/boot/efi/EFI文件夹里，有个refind.

如果你没有这个refind文件的话，需要手动进行安装。

手动安装方式一：

找到`/usr/share/rEFInd/`文件夹，看看里面是否有refind-install,直接运行下方命令即可。

```bash
cd /usr/share/rEFInd/
sudo ./refind-install
sudo reboot
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image278.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image279.webp)

手动安装方式二：

```bash
cd /usr/share/rEFInd/
sudo cp -r /usr/share/rEFInd/refind /boot/efi/EFI/
sudo reboot
```

安装完毕后，可以看看/boot/efi/EFI文件夹里，是否有refind了.

#### 配置 refind.conf
```bash
cd /boot/efi/EFI/refind
sudo vim ./refind.conf
```

最后一行加上（vim编辑器不会用自己百度）

```bash
dont_scan_dirs ESP:/EFI/boot,EFI/ubuntu,EFI/boot,EFI/deepin_os,EFI/UOS,EFI/fedora
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image280.webp)

```bash
sduo reboot
#如果使用refind-install安装的refind,默认自动将rEFInd启动项作为第一项，如果没有进BIOS手动配置
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image281.webp)

完成！！！

#### 美化
rEFInd-glassy主题：https://pan.baidu.com/s/1HgfXG3m4j57VIk4k6inI-g

提取码：zimo

记得解压出来。

```bash
cd /boot/efi/EFI/refind
mkdir -p ./themes

# 你把美化包解压到哪里了，就cd到哪里
cd ~/Downloads
sudo cp -r ./rEFInd-glassy /boot/efi/EFI/refind/themes
```

然后

```bash
cd /boot/efi/EFI/refind
sudo vim ./refind.conf
```

最后一行加上（vim编辑器不会用自己百度）

```bash
include themes/rEFInd-glassy/theme.conf
```
```bash

# 重新启动电脑
sudo reboot
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image282.webp)

### KDE主题
先从kde store下载一个心仪的主题，然后解压出来。

https://store.kde.org/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image283.webp)

```bash

# cd进主题的文件夹
cd ~/Downloads

# 把主题文件复制到KDE的主题
sudo cp -r ./Apple.BigSur.Dark.P6 /usr/share/plasma/look-and-feel
```

进入设置就可以看到啦，如果看不到，就重启下电脑。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image284.webp)

### 自启应用与脚本
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image285.webp)

在右上角可以添加脚本和应用，添加脚本前先要确认脚本是否拥有可执行权限。也可以选择开机时运行脚本和关机时运行脚本。

例子：拿docker_x11的命令`xhost +local:docker`为例。

先找到一个存放脚本的文件夹，你自己在home分区创一个就可以了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image286.webp)

```bash
touch docker_x11.bash
sudo chmod a+x ./docker_x11.bash
sudo vim ./docker_x11.bash
```
```bash
#!/bin/bash
# 等待 X Server 就绪（最多等 10 秒）
for i in {1..10}; do
    if [ -n "$DISPLAY" ] && xset q >/dev/null 2>&1; then
        /usr/bin/xhost +local:docker
        exit 0
    fi
    sleep 1
done
```

在vim编辑器里用`:wq`保存并退出

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image287.webp)

在kde6设置里找到autostart，然后添加登陆脚本，而不是登出脚本。

🟢 **Login Script（登录脚本）**

*   **什么时候执行？** 当用户登录系统时（例如通过终端、TTY 或 SSH）自动执行。

🔴 **Logout Script（登出脚本）**

*   **什么时候执行？** 当用户退出 shell 或注销登录会话时自动执行。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image288.webp)

找到脚本文件添加进去即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image289.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image290.webp)

重启即可

### 从Bash切换到Zsh
#### 什么是shell？
简单说，shell是你与应用程序交互的媒介。 通常情况下，你将你想要使用的应用程序和参数输入到shell，shell在PATH中查找你希望调用的应用程序，对参数进行解析，并传入参数。最终将结果返回给你。

如果你使用的是bash shell，那么你大概率正在使用某个linux发行版。 目前，大部分`linux`发行版使用的默认shell仍然是`bash`。 `Windows`默认使用`Powershell`。 从`Catalina`开始，`MacOS`已经将默认shell从`bash`切换到了`zsh`。

#### zsh有什么优势？
相比`bash`，`zsh`有庞大的插件社区和成熟的插件管理框架如`oh my zsh`，这使得`zsh`的功能扩展变得极为容易，你可以向管理vim插件一样管理zsh插件。

#### zsh安装与配置
先查看自己现在是啥shell？（一般是bash）

```bash
echo $SHELL  # 应该会显示/usr/bin/bash

# 或
echo $0      # 应该显示 "-bash"
```

现在让我们来安装zsh吧。

```bash

# Debian系
sudo apt install zsh

#红帽系
sudo dnf install zsh
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image291.webp)

确认一下自己是否安装成功

```bash

# 查看shell版本
bash --version
zsh --version
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image292.webp)

如何配置zsh呢？

和`bash`差不多。

`zsh`的全局配置文件位于`/etc/zsh.zshc`， 单用户配置文件位于`~/.zshrc`。

但不推荐你现在修改任何一个文件，因为我们有更加优雅的配置方式：

#### oh my zsh!
项目地址：

https://github.com/ohmyzsh/ohmyzsh

`oh my zsh`是当下最流行的`zsh`插件管理工具，相当于vim中的vim-plug或vundle。

安装oh my zsh

```bash

# 方式一（可能需要科学上网）
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 方式二
sh -c "$(curl -fsSL https://install.ohmyz.sh/)"
```

下图可选是否现在就把zsh设置成默认的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image293.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image294.webp)

如上图即安装成功。

#### 配置oh my zsh
现在查看.zshrc文件，你会发现oh my zsh已经几乎帮你配置好了。

如果你想使用插件，可以找到plugins，在其中添加即可。

这里给出我的.zshrc供你参考。

#### 定制独一无二的zsh
还记得上文中我提到不建议修改.zshrc，这是因为我们在使用oh my zsh框架后，oh my zsh会建立一个~/oh-my-zsh目录用于存放相关文件，其中有一个名为custom的文件夹，我们可以将我们的配置脚本放在此处，oh my zsh会自动加载这一目录下的vim脚本。

基本支持bash的配置命令。以博主为例，bashrc脚本迁移到zsh未作任何改动。

我创建了一个名为myshrc.zsh的脚本以存储配置，文件如下供你参考。

```bash
cd /home/用户名/.oh-my-zsh/custom
touch ./myshrc.zsh
vim ./myshrc.zsh
```

将自己~/.bashrc里自己多添加的设置全部复制到该文件中即可。

例如我的：

```bash

# 配置CUDA
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH

# 配置ROS-DISTRO
export ROSDISTRO_INDEX_URL=https://mirrors.bfsu.edu.cn/rosdistro/index-v4.yaml

# 配置ROS1 Noetic

# source /opt/ros/noetic/setup.zsh

# export ROS_MASTER_URI=http://localhost:11311

# export ROS_HOSTNAME=localhost

# export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:/home/tungchiahui/UserFloder/MySource/ROS_WS/gazebo_models:/home/tungchiahui/UserFloder/MySource/ROS_WS/ign_models

# 配置ROS2 Humble
source /opt/ros/humble/setup.zsh
export ROS_DOMAIN_ID=6
export IGN_GAZEBO_RESOURCE_PATH=$IGN_GAZEBO_RESOURCE_PATH:/home/tungchiahui/UserFloder/MySource/ROS_WS/gazebo_models:/home/tungchiahui/UserFloder/MySource/ROS_WS/ign_models
```

接着可以刷新当前终端环境

```bash

# 刷新环境
source ~/.zshrc

# 检查自己的配置是否生效
echo $LD_LIBRARY_PATH 
```

对应着我上面我的配置，会对应echo打印出下列即为成功。

```bash
/usr/local/cuda/lib64:/usr/local/cuda/lib64: 
```

#### 切换默认shell
如果在下图选择了yes,就不用再重新设置了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image295.webp)

```bash
chsh -s $(which zsh)
```
```bash
echo $SHELL  # 应输出 "/usr/bin/zsh"
echo $0      # 若显示 "-zsh"，表示已生效
```

完毕！

如果这里发现无法设置成默认，如下图：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image296.webp)

那么使用下方命令强制修改：

```bash

# 强制修改
sudo usermod -s /usr/bin/zsh tungchiahui(用户名)

# 验证
grep tungchiahui(用户名) /etc/passwd  # 检查是否显示 "/usr/bin/zsh"

# 重启
sudo reboot
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image297.webp)

重启后验证

```bash
echo $SHELL  # 应输出 "/usr/bin/zsh"
echo $0      # 若显示 "-zsh"，表示已生效
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image298.webp)

再次完毕！

#### 添加插件教程
##### powerlevel10k
这个插件是美化zsh的，优点是不会怎么影响终端性能，速度比较快。

https://github.com/romkatv/powerlevel10k

安装powerlevel10k

```bash

# 国外用户
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc

# 国内用户
git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
```

下载字体

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image299.webp)

下面的任选其一

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image300.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image301.webp)

```bash

# 刷新当前环境变量
source ~/.zshrc
```

这里需要检查图标是否都显示正常，显示不正常就证明字体有问题，关闭终端再重新打开看看字体还有问题吗?

没问题就一直y.

建议参考这个教程的配置。

https://www.bilibili.com/video/BV1dX4y127JL

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image302.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image303.webp)

下面是配置好的样子：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image304.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image305.webp)

如果想重新配置就输入：

```bash
p10k configure
```

VScode如果图标显示不全的话，如下操作即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image306.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image307.webp)

1.  搜索 `Terminal › Integrated: Font Family`

输入支持 Nerd Font 的字体名称，例如：

```bash
MesloLGS NF
```

（注意 `MesloLGS NF` 必须放在首位）

2.  **用基础连字支持**

搜索 `Terminal › Integrated: Font Ligatures`

勾选 `Enabled`（允许字体渲染特殊连字符号）

3.  **确保 GPU 加速开启**

*   搜索 `Terminal › Integrated: Gpu Acceleration`

*   设置为 `on`（提升渲染性能）

**若 GPU 加速导致问题**

*   尝试设置为 `off` 后重启终端

*   检查 Fallback Ligatures 是否生效

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image308.webp)

再重开终端即可（GPU设置必须重启终端）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image309.webp)


### 给终端加颜色
如果你的终端里的文字一点颜色都没有的话再添加这个,某些发行版比如RockyLinux默认是没有颜色的,而Fedora,Ubuntu这种开箱即用的发行版一般默认都是有颜色的.

```bash
vim ~/.bashrc
```
把下面这些内容放到文件最底部并保存
```bash
# =========================================================
# Fedora / Ubuntu style colored bash prompt
# =========================================================

# Enable color support
case "$TERM" in
    xterm-color|*-256color) color_prompt=yes;;
esac

# Git branch function
parse_git_branch() {
    git branch 2>/dev/null | sed -n '/^\*/s/^\* //p'
}

if [ "$color_prompt" = yes ]; then
    if [ "$EUID" -eq 0 ]; then
        # root prompt (red)
        PS1='\[\e[1;31m\]\u@\h\[\e[0m\]:\[\e[1;34m\]\w\[\e[1;33m\]$(parse_git_branch)\[\e[0m\]\$ '
    else
        # normal user prompt (green)
        PS1='\[\e[1;32m\]\u@\h\[\e[0m\]:\[\e[1;34m\]\w\[\e[1;33m\]$(parse_git_branch)\[\e[0m\]\$ '
    fi
else
    PS1='\u@\h:\w\$ '
fi

# Enable colored commands
alias ls='ls --color=auto'
alias ll='ls -lh --color=auto'
alias la='ls -A --color=auto'

alias grep='grep --color=auto'
alias diff='diff --color=auto'
alias ip='ip -color=auto'

export CLICOLOR=1

```

```bash
source ~/.bashrc
```


### Flatpak软件管理工具
flatpak类似于apt和dnf等包管理工具，可以安装软件，但安装的不是最基础的软件，是QQ，QQ音乐这种软件。

当你的apt或者dnf没有某个软件时，可以去flathub上去看看是否有这个软件。

#### flatpak安装
https://flatpak.org/

1.  Ubuntu

```bash

# 安装flatpak
sudo apt update && sudo apt install flatpak

# 先添加官方国外源
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo

# 再修改为中科大镜像源
sudo flatpak remote-modify flathub --url=https://mirrors.ustc.edu.cn/flathub

# 查看仓库详情
flatpak remotes --show-details
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image310.webp)

2.  Fedora

Fedora已经自带。

```bash

# 先添加官方国外源
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo

# 再修改为中科大镜像源
sudo flatpak remote-modify flathub --url=https://mirrors.ustc.edu.cn/flathub

# 查看仓库详情
flatpak remotes --show-details
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image311.webp)

#### 字体问题解决
因为flatpak是沙盒,所以容易缺字体,先安装字体
```bash
#Ubuntu
sudo apt install google-noto-sans-cjk-fonts google-noto-serif-cjk-fonts

# Fedora
sudo dnf install google-noto-sans-cjk-fonts google-noto-serif-cjk-fonts
```
实际上这套字体叫：
Noto Sans CJK / Noto Serif CJK
它同时包含：
中文（Simplified + Traditional）
日文（JP）
韩文（KR）


然后设置 Flatpak 的字体访问权限（override）,让所有 Flatpak 应用能“看到”这些字体：
```bash
sudo flatpak override --filesystem=/usr/share/fonts
sudo flatpak override --filesystem=~/.local/share/fonts
```

如果你之前安装过flatpak软件,那么上面的操作对已安装的软件不生效,
拿QQ音乐举例,我们需要让他生效.

进入 Flatpak 沙盒环境,这条命令让你进入 QQ 音乐的沙盒终端，就像“进入容器”一样。
里面的路径和主系统是隔离的。
```bash
flatpak run --command=bash com.qq.QQmusic
```

删除旧的 fontconfig 缓存,Flatpak 会在自己的沙盒里缓存字体索引。
删掉旧缓存后，新的字体才能重新被识别。

```bash
rm -rvf ~/.var/app/com.qq.QQmusic/cache/fontconfig/
```

重建字体缓存,这一步会强制重新扫描字体路径（包含 /usr/share/fonts 和用户字体路径），
生成新的缓存文件，修复显示问题。

```bash
fc-cache -f -v
```


#### 安装软件
https://flathub.org/

去上面的官网搜索软件+下载软件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image312.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image313.webp)

```bash
flatpak install flathub com.obsproject.Studio
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image314.webp)

#### 运行软件
方法一（官方）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image315.webp)

```bash
flatpak install flathub com.obsproject.Studio
```

方法二（直接当普通软件运行即可）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image316.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image317.webp)

### Appimage
比如说，QQ音乐只提供deb，appimage.并不提供rpm格式的安装包（1.1.7版本是这样的，以后估计rpm会给。纯纯开发者欠C了，连rpm都不给。）所以说我想在Fedora上安装QQ音乐要么用appimage,要么选择用flatpak.(这里使用appimage)

先下载appimage

https://y.qq.com/download/download.html

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image318.webp)

再去google下载个QQ音乐图标（因为google可以下载透明图标）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image319.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image320.webp)

给他们放在`/home/用户名`的某个文件夹中（这个自己定，比如我是`/home/tungchiahui/UserFloder/Applications/qqmusic/`文件夹）

先给QQ音乐执行权限

```bash
cd ~/UserFloder/Applications/qqmusic
sudo chmod +x ./qqmusic-1.1.7.AppImage
```

给QQ音乐配置 **快捷方式** ：

```bash
cd ~/.local/share/applications/
touch ./qqmusic.desktop
vim ./qqmusic.desktop
sudo chmod +x ./qqmusic.desktop
```

内容如下

```bash
[Desktop Entry]
Name=QQ音乐
Exec=/home/tungchiahui/UserFloder/Applications/qqmusic/qqmusic-1.1.7.AppImage
Icon=/home/tungchiahui/UserFloder/Applications/qqmusic/QQ_Music2023.svg
Type=Application
Categories=Audio;Music;Player;
Comment=QQ Music Client for Linux
```

此时找到软件就可以打开了，如果找不到，请重启，部分不先进的发行版刷新图标列表不会很快。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image321.webp)

如果QQ音乐闪退，这个只是QQ音乐自己软件的问题，按下图这样做。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image322.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image323.webp)

如果你用的不是KDE，那么也可以直接修改`qqmuic.desktop`：在exec的末尾加上`--no-sandbox`

```bash
[Desktop Entry]
Name=QQ音乐
Exec=/home/tungchiahui/UserFloder/Applications/qqmusic/qqmusic-1.1.7.AppImage --no-sandbox
Icon=/home/tungchiahui/UserFloder/Applications/qqmusic/QQ_Music2023.svg
Type=Application
Categories=Audio;Music;Player;
Comment=QQ Music Client for Linux
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image324.webp)

如果qq音乐缺字体,那么请安装字体(这个字体是多种语言合一的字体)
```bash
sudo dnf install google-noto-sans-cjk-fonts google-noto-serif-cjk-fonts
```

### 修改HOSTNAME
例如我要修改为Dell-G15-5511

```bash
sudo hostnamectl set-hostname "Dell-G15-5511"

hostnamectl | grep "Static hostname"      # 验证静态主机名

sudo systemctl restart systemd-hostnamed  # 重启主机名服务
sudo systemctl restart NetworkManager      # 重启网络服务
sudo reboot

echo $HOSTNAME           # 验证主机名
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image325.webp)

### 删掉应用配置
有的应用配置有问题，可以删掉他的缓存。

缓存在`~/.config`文件夹下。

比如VScode出问题了：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image326.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image327.webp)

这样删掉他就结束了。

而下面的这个`~/.vscode`是扩展。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image328.webp)
