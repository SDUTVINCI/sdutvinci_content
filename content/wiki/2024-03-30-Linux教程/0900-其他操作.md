---
title: "其他操作"
---

### 工控机如何连wifi
当我们工控机从一个环境移动到另一个环境，且新的环境的wifi我们没有连接过，我们没有屏幕如何让工控机连上网呢？

进入工控机的硬盘目录，进入`/etc/netplan/`，底下有很多yaml,找到一个最符合下方格式的yaml:

```YAML
network: 
  version: 2 
  wifis: 
    NM-6f414fe0-2658-48ff-89ee-c7981b87bc96: 
      renderer: NetworkManager 
      match: 
        name: "wlan0" 
      dhcp4: true 
      dhcp6: true 
      access-points: 
        "EMIS_Vinci_Robocon_5G": 
          auth: 
            key-management: "psk" 
            password: "vinci666" 
          networkmanager: 
            uuid: "6f414fe0-2658-48ff-89ee-c7981b87bc96" 
            name: "EMIS_Vinci_Robocon_5G" 
            passthrough: 
              wifi-security.auth-alg: "open" 
              ipv6.addr-gen-mode: "default" 
              ipv6.ip6-privacy: "-1" 
              proxy._: "" 
      networkmanager: 
        uuid: "6f414fe0-2658-48ff-89ee-c7981b87bc96" 
        name: "EMIS_Vinci_Robocon_5G"

```

将其改为新wifi的名和密码：

```YAML
network:
  version: 2
  wifis:
    wlan0:
      dhcp4: true
      dhcp6: true
      access-points:
        "EMIS_Vinci_Robocon_5G":
          password: "vinci666"

```

工控机开机即可连上网。

### SSH远程开发
#### 环境准备
**1.硬件准备**

首先要有一台工控机（X86小型电脑，专业工控机，树莓派等等）。

控制系统的硬件载体是具有多样性的，常用的多是基于ARM、x86等架构的处理器，比如：PC、工控机、树莓派、NVIDIA Jetson...。不同的处理器都存在一定的优缺点，PC和工控机，处理器性能强大，但是功耗高、体积大、灵活性差，嵌入式系统则反之。对于我们而言，可以根据机器人平台的电气、载重、空间以及用途等诸多要素自主选择合适的控制系统。

无论选用何种处理器，只要是要进行机器人的开发，安装了ROS或者ROS2，那么对于开发人员而言，在使用上，没有任何本质的区别，或者换言之，作为软件工程师，部分场景下无需关注于硬件的选型。

**2.设置固定IP**

远程连接时，不管使用何种工具，需要根据IP地址定位到被连接的主机，再通过账号和密码登录该主机，因此，我们需要先获取该IP地址。并且每次连接时，都需要使用到IP，为了保证连接的便利性和稳定性，最好将被连接主机的IP地址设置为固定IP，具体操作如下。

1.进入设置界面

启动被连接的主机（启动时需要连接显示器或使用HDMI采集卡，并且配置完SSH远程访问之后，可以不再使用显示器或HDMI采集卡），并进入设置界面。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image332.webp)

2.配置所连接的网络

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image333.webp)

3.设置固定IP

查看当前IP地址。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image334.webp)

设置固定IP。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image335.webp)

至此，IP配置完毕。

#### 环境搭建
**概念**

SSH（Secure Shell）是一种通用的、功能强大的、基于软件的网络安全解决方案。计算机每次向网络发送数据时，SSH都会自动对其进行加密。数据到达目的地时，SSH自动对加密数据进行解密。整个过程都是透明的，使用OpenSSH工具将会增进你的系统安全性。SSH安装容易、使用简单。

**实现**

SSH实现架构上分为客户端和服务器端两大部分，客户端是数据的发送方，服务端是数据的接收方，当前场景下，我们需要从本地主机发送数据到远程主机，那么本地主机需要安装并启动SSH客户端，而远程主机则需要安装并启动SSH服务端，整个实现具体流程如下：

1.  本地主机安装SSH客户端，远程主机安装SSH服务端；

2.  远程主机启动SSH服务；

3.  本地主机登陆远程主机；

4.  实现数据传输。

1.安装SSH客户端与服务端

默认情况下，Ubuntu系统已经安装了SSH客户端，因此只需要在远程主机安装SSH服务端即可，安装命令如下：

```Plain Text
 sudo apt install openssh-server
```

如果SSH客户端需要自行安装，那么调用如下命令:

```Plain Text
sudo apt install openssh-client
```

2.远程主机启动SSH服务

远程主机启动 ssh 服务命令如下：

```Plain Text
sudo /etc/init.d/ssh start
```

启动后可使用如下命令查看服务是否正常运行：

```Plain Text
ps -e | grep ssh
```

如果启动成功，会包含 sshd 程序。

以后需要频繁的使用ssh远程登录，为了简化实现，可以将远程主机的ssh服务设置为开机自启动，命令如下：

```Plain Text
sudo systemctl enable ssh
```

3.本地远程登录

登录远程主机可以调用如下命令:

```bash
ssh -X 用户名@ip地址
```

然后根据提示，录入登陆密码，即可成功登录。

如果退出，可以调用exit命令：

```Plain Text
exit
```

**4.实现数据传输**

通过SSH在本地主机只需调用相关指令，便可方便的实现与远程主机的数据上传或下载，指令格式如下所示：

上传文件指令格式如下：

```Plain Text
scp 本地文件路径 账号@ip:路径
```

上传文件夹指令格式如下：

```Plain Text
scp -r 本地文件夹路径 账号@ip:路径
```

下载文件指令格式如下：

```Plain Text
scp 账号@ip:路径 本地文件夹路径
```

下载文件夹指令格式如下：

```Plain Text
scp -r 账号@ip:路径 本地文件夹路径
```

**优化**

每次远程登录时，都需要输入密码，使用不方便，可以借助密钥简化登录过程，实现免密登录，提高操作效率。实现思想是：生成一对公钥私钥，私钥存储在本地，公钥上传至服务器，每次登录时，本地直接上传私钥到服务器，服务器有匹配的公钥就认为是合法用户，直接创建SSH连接即可。具体实现步骤只有两步：

1.  本地生成密钥对；

2.  将公钥上传至远程主机。

**1.生成密钥对**

本地客户端生成公私钥：（一路回车默认即可）

```Plain Text
ssh-keygen
```

上面这个命令会在用户目录.ssh文件夹下创建公私钥：

1.  id_rsa （私钥）；

2.  id_rsa.pub (公钥)。

**2.公钥上传**

上传指令如下：

```Plain Text
ssh-copy-id -i ~/.ssh/id_rsa.pub 账号@ip
```

上面这条指令是将本地公钥上传到远程主机的ssh目录下，该目录下有文件authorized_keys保存了公钥内容。

以后再远程登录就无需录入密码了。

#### 使用VScode优化
上一节，我们介绍了ssh远程连接的使用，但是如果只是纯粹使用ssh也存在一些不足，比如：编辑文件内容时，需要使用vi编辑器，且在一个终端内，无法同时编辑多个文件。本节将介绍一更为实用的功能——VSCode结合插件实现远程开发，这使我们可以以图形化的方式实现远程开发，比ssh的使用更方便快捷，可以大大的提高程序开发效率。

**1.准备工作**

VScode远程开发依赖于ssh，请首先按照上一节内容配置ssh远程连接。

**2.为VScode安装远程开发插件**

启动VScode，首先点击侧边栏的扩展按钮，然后在`扩展：商店`的搜索栏输入`Remote Development`并点击同名插件，最后在右侧显示区中点击`安装`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image336.webp)

**3.配置远程连接**

步骤1：使用快捷键`ctrl + shift + p`打开命令输入窗口，并输入`Remote-SSH:Connect to Host...`，弹出列表中选择与之同名的选项。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image337.webp)

步骤2：步骤1完成将弹出一个新的命令窗口如下，选择下拉列表中的 `Add New SSH Host`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image338.webp)

步骤3：步骤2完成又将弹出一个新的命令窗口，在其中输入：`ssh -X ubuntu@192.168.43.164`，其中，`ubuntu`需要替换为你的登录用户名，`192.18.43.164` 则替换为被远程连接主机的ip地址。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image339.webp)

步骤4：选择步骤3完成后的弹窗列表中的第一个选项(或直接回车)，即可完成配置，配置成功后会有提示信息。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image340.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image341.webp)

**4.使用**

步骤1：继续使用快捷键`ctrl + shift + p`打开命令输入窗口，并输入`Remote-SSH:Connect to Host...`，此时列表中将显示步骤3中配置的ip地址，直接选择，选择后，VScode将打开一个新的窗口。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image342.webp)

或者，也可以点击侧边栏的`远程资源管理器`，在弹出的服务器列表中选择要连接的服务器，并右击，选择在本窗口或新窗口中实现远程连接。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image343.webp)

步骤2：选择菜单栏的`文件`下的`打开文件夹`，在弹窗列表中选择需要打开的文件夹并点击确定即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image344.webp)

最终，我们就可以像操作本地文件一样实现远程开发了。

### 远程桌面
因为用X11转发效率太低太低，所以还是要选用远程访问桌面的形式来看Rviz2和Gazebo等等（如果有需求的话）

我们选择使用VNC来看。

1.  安装VNC服务器：

这里以TigerVNC为例进行安装：

```bash
sudo apt install tigervnc-standalone-server tigervnc-common
```

2.  设置VNC密码：

为VNC用户设置密码，运行以下命令：

```bash
vncpasswd
```

3.  配置VNC启动脚本:

创建VNC配置文件，在用户家目录内的.vnc文件夹中创建启动文件：

```bash
mkdir -p ~/.vnc
nano ~/.vnc/xstartup
```

在打开的编辑器里面输入以下内容（以GNOME为例）：

```bash
#!/bin/sh
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
gnome-session
```

保存并退出（在nano中按Ctrl + O保存，然后按Ctrl + X退出）。

4.  给予执行权限：

为xstartup文件设置执行权限：

```bash
chmod +x ~/.vnc/xstartup
```

5.  启动VNC服务器：

```bash
vncserver -geometry 1920x1080 -localhost=0
```

geometry 选项指定窗口大小，localhost 选项设为 0 以开放连接。(设为1是只允许本地连接)

你将看到类似于:1的输出，这表示VNC会话的显示编号。例如，如果输出为:1，则VNC监听的端口为5901（5900 + 显示编号）。

6.  查看已开启的VNC服务器：

```bash
vncserver -list
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image345.webp)

7.  连接到VNC服务器：

使用VNC客户端（如vncviewer）连接到VNC服务器，输入你的服务器IP和端口。例如，如果服务器IP为192.168.31.10，且显示编号为1，你应该连接到192.168.31.10:1，或者直接输入192.168.31.10:5901。

8.  停止VNC服务器：

如果需要停止VNC服务器，可以使用以下命令：

```bash
vncserver -kill :1
```

将:1替换为你实际使用的显示编号。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image346.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image347.webp)

### USB端口设置
**首先先配置好权限**

```bash

# 将用户权限提高
sudo usermod -aG dialout $USER
newgrp dialout

# 查看下面命令是否输出dialout（若输出才正常）
groups
```

因为我们插拔USB设备，他的端口号可能会一直变，所以我们要给他的tty起一个固定的别名。

#### 根据USB设备绑定端口(多个不同设备)
**需求：** Ubuntu系统中现接入雷达和智能小车，请为二者绑定端口。

**实现原理：** 可以通过USB设备本身的“标识”实现端口绑定。

**流程如下：**

（1）.查找设备idVendor和idProduct

接入两个USB设备，在终端调用指令`lsusb`查看显示系统中以及连接到系统的USB设备信息。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image348.webp)

如上图所示，红色方框内数据为USB设备，ID后面的`1a86:7523`分别为USB的idVendor和idProduct（两个参数之间使用”：“分隔）。

另外：可以通过重新插拔比较的方式确定哪些数据对应接入的USB设备。

（2）.编写映射规则

在`/etc/udev/rules.d`目录下新建文件xxx.rules（文件名自定义）

```bash
sudo vim /etc/udev/rules.d/xxx.rules
```

输入如下内容：

```bash
KERNEL=="ttyUSB*", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", MODE:="0777", SYMLINK+="mylidar"
KERNEL=="ttyUSB*", ATTRS{idVendor}=="1a86", ATTRS{idProduct}=="7523", MODE:="0777", SYMLINK+="mycar"
```

代码解释：

*   KERNEL是内核固定名称，这里统一是“ttyUSB\*”；

*   MODE是节点权限，通常改为“0777”，表示可读写可运行；

*   SYMLINK是符号连接，即绑定的别名；

*   ATTRS是设备厂商的唯一标识，idVendor和idProduct正好组成上面通过lsusb查找到的设备ID。

***小提示：****一般的USB设备供应商都会提供类似的脚本文件，对于调用者而言，直接复制该文件到*`/etc/udev/rule.d`*目录即可。*

| 设备类型 | 内核名称示例 | 说明 |
|:---|:---|:---|
| USB 串口设备 | ttyUSB* | USB 转串口设备，如 /dev/ttyUSB0 |
| 串口设备 | ttyS* | 物理串口设备，如 /dev/ttyS0 |
| 存储设备 | sd* | SCSI 磁盘设备，如 /dev/sda |
| 网络设备 | eth* | 以太网设备，如 /dev/eth0 |
| 输入设备 | event* | 输入事件设备，如 /dev/input/event0 |
| 蓝牙设备 | rfcomm* | 蓝牙串口设备，如 /dev/rfcomm0 |

（3）.使规则生效

在终端下输入如下指令：

```bash

# Ubuntu用下方命令
sudo service udev reload
sudo service udev restart

#Fedora用下方命令
sudo udevadm control --reload
sudo udevadm trigger
```

再重新插拔设备即可。

（4）.测试

终端下输入如下指令

```bash
ll /dev | grep ttyUSB
```

运行结果如下：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image349.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image350.webp)

也可以多次插拔USB设备，会发现设备端口`ttyUSBn`中的n编号会变动，但是别名是始终可以指向对应的USB设备的。至此，就可以使用别名来关联所需使用的USB设备了。

**缺点：**

上述实现也存在一定的局限性，当Ubuntu接入两台或多台相同型号的USB设备时，由于设备ID是一样的，该种实现方式只会对其中的一台设备生效，这种情况下，就需要通过第二种策略来实现端口的绑定了。

#### 根据主机硬件绑定端口（多个相同设备)
**需求：** 无人车中现接入一前一后两台相同型号的雷达，请为二者绑定端口。

**实现原理：** USB设备所连接主机的USB接口也是有其“标识”的，可以通过这个标识实现端口绑定。

**流程如下：**

（1）.查看所连接的主机USB接口的KERNELS

调用如下指令查看第一台雷达的USB信息：

```bash
udevadm info --attribute-walk --name=/dev/ttyUSB0 | grep KERNELS
udevadm info --attribute-walk --name=/dev/ttyACM0 | grep KERNELS
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image351.webp)

调用如下指令查看第二台雷达的USB信息：

```bash
udevadm info --attribute-walk --name=/dev/ttyUSB1 | grep KERNELS
```

运行结果相比较不同的KERNELS，第一台雷达端口地址为`KERNELS==1-1.3:1.0`，第二个雷达端口地址为`KERNELS==1-1.4:1.0`。可以使用此数据作为不同端口的“**唯一性标识**”。

（2）.编写映射规则

在`/etc/udev/rules.d`目录下新建文件xxx.rules（文件名自定义），输入如下内容：

```bash
sudo vim /etc/udev/rules.d/xxx.rules
```
```bash
KERNEL=="ttyUSB*", KERNELS=="1-1.3:1.0", MODE:="0777", SYMLINK+="rplidar_front"
KERNEL=="ttyUSB*", KERNELS=="1-1.4:1.0", MODE:="0777", SYMLINK+="rplidar_back"
```

（3）.使规则生效

在终端下输入如下指令：

```bash

# Ubuntu用下方命令
sudo service udev reload
sudo service udev restart

#Fedora用下方命令
sudo udevadm control --reload
sudo udevadm trigger
```

再重新插拔设备即可。

（4）.测试

终端下输入如下指令，运行结果如下：

```bash
ll /dev | grep ttyUSB
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image352.webp)

至此，就可以使用别名来关联所需使用的USB设备了。

**缺点：** USB设备必须连接在主机的指定端口上，否则，会导致端口绑定失败，或产生逻辑错误。(而且增加新设备可能会导致端口改变)

#### 根据其他属性绑定端口
（1）.查看所连接的主机USB接口的信息

```bash
udevadm info -a -p $(udevadm info -q path -n /dev/ttyACM0)
udevadm info -a -p $(udevadm info -q path -n /dev/ttyUSB0)
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image353.webp)

这些数据都是不会变的，所以可以拿这些数据来做标识

（2）.编写映射规则

在`/etc/udev/rules.d`目录下新建文件xxx.rules（文件名自定义），输入如下内容：

```bash
sudo vim /etc/udev/rules.d/xxx.rules
```
```bash
KERNEL=="ttyACM*", ATTRS{idVendor}=="1a86", ATTRS{idProduct}=="55d4", ATTRS{serial}=="0001", MODE:="0777", SYMLINK+="ttyACM_Lidar"
KERNEL=="ttyACM*", ATTRS{idVendor}=="1a86", ATTRS{idProduct}=="55d4", ATTRS{serial}=="0002", MODE:="0777", SYMLINK+="ttyACM_MCU"
KERNEL=="ttyUSB*", ATTRS{idVendor}=="1a86", ATTRS{idProduct}=="7523", MODE:="0777", SYMLINK+="ttyUSB_IMU"
```

（3）.使规则生效

在终端下输入如下指令：

```bash

# Ubuntu用下方命令
sudo service udev reload
sudo service udev restart

#Fedora用下方命令
sudo udevadm control --reload
sudo udevadm trigger
```

再重新插拔设备即可，或者 **直接重启系统。**

（4）.测试

终端下输入如下指令`ll /dev | grep ttyUSB`，运行结果如下：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image354.webp)

至此，就可以使用别名来关联所需使用的USB设备了。

**缺点：** 一些设备可能没有串口号等。

#### 其他注意事项
并非所有的USB设备端口号都是`ttyUSBn`的格式，比如Arduino的端口号可能是`ttyACMn`，而对于USB摄像头而言，一台设备则对应两个端口号，分别是`videon`和`video(n+1)`，并且启用摄像头设备一般使用的是`videon`端口，绑定时需要关联的也是该接口。

但是无论是外接何种USB设备，也不管采用上述两种方案的哪一种进行端口绑定，其原理都是类似的，只是实现细节不同而已。如果是外接的Arduino设备，那么需要在rules文件中的将`KERNEL=="ttyUSB*"`修改为`KERNEL=="ttyACM*"`，如果外接的是USB摄像头，那么则需要在rules文件中的将`KERNEL=="ttyUSB*"`修改为类似于`KERNEL=="video[0,2,4,6]"`的格式，其中`video[0,2,4,6]`表示可以绑定的端口为`video0`或`video2`或`video4`或`video6`。

### Linux分区Gui工具
```Plain Text
sudo apt install gparted
sudo dnf install gparted
```

### 挂载内网网页
可以把本地的网页挂载到路由器端，或者同内网段其他设备上，甚至可以挂载在本地。

下面以安卓设备为例子，由于安卓设备暂时不能直接打开html,所以我们可以把html挂载到本地服务器，再通过浏览器查看。

由于安卓系统基于Linux内核，所以我们可以下载一个叫termux的app来敲一些Linux命令：

https://github.com/termux/termux-app

首先打开app后，先更新软件缓存：

```bash
pkg update
```

然后换源

```bash
nano $PREFIX/etc/apt/sources.list
```

将该文件内容替换为下面：

```bash
deb https://mirrors.bfsu.edu.cn/termux/termux-packages-24 stable main
```

*   如果你使用的是`nano`编辑器：

    *   按 `Ctrl+O` 保存文件。

    *   按 `Enter` 确认文件名。

    *   按 `Ctrl+X` 退出编辑器。

*   如果你使用的是`vim`编辑器：

    *   按 `Esc` 退出编辑模式。

    *   输入 `:wq` 保存并退出。

接下来重新更新软件源：

```bash
pkg update && pkg upgrade
```

安装python：

```bash
pkg install python3
```

**通过Termux访问设备存储** ：

如果文件在安卓设备上，可以使用Termux的文件管理器访问设备的存储：

```bash
termux-setup-storage
```

1.  **进入HTML文件所在的目录** ：

    1.  使用`cd`命令进入HTML文件所在的目录：

```bash
cd ~/your_html_folder
```

2.  **启动Python HTTP服务器** ：

    1.  运行以下命令启动一个简单的HTTP服务器：

    ```bash
    python3 -m http.server 8000
    ```
    3.  这会在端口`8000`上启动一个Web服务器。

*   启动服务器后，Termux会显示日志信息，例如：

```bash
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

*   这表示服务器已成功启动。

**在浏览器中访问HTML文件**

1.  **在同一设备上访问** ：

    1.  打开安卓设备上的浏览器（如Chrome），输入以下地址：

    ```bash
    http://localhost:8000
    ```
    3.  如果HTML文件中有`index.html`，它会自动加载；否则，你需要手动点击文件链接。


### RustDesk

#### RustDesk无人值守远程控制
目前rustdesk还没办法进行无人值守在wayland下,所以我们需要把登陆密码,锁屏和休眠待机全部关掉.

##### 设置 KDE6 自动登录
1. 可以先查自己的sddm类型
```bash
ls /usr/share/xsessions/
```

```bash
ls /usr/share/wayland-sessions/
```
看哪个会输出东西,输出东西的文件夹就是你拥有的sddm,比如xsessions输出内容的话,那就是x11的sddm,如果wayland-sessions输出内容的话你就是wayland的sddm.


2. 编辑 SDDM 配置：
```bash
sudo nano /etc/sddm.conf
```

在文件最上面有个[Autologin],在这个底下加入下面的内容：
```bash
[Autologin]
User=你的用户名
Session=plasma.desktop   # 或 plasma.desktop（X11），方案一可用 Wayland
```

3. 保存退出后重启 SDDM：
```bash
sudo systemctl restart sddm
```

✅ 开机后自动进入桌面，无需输入密码

##### 关闭锁屏在 KDE6 系统设置里：
打开 系统设置 → 屏幕锁定(Screen Locking)
关闭自动锁定
关闭屏幕关闭 / 屏幕保护锁定
这样 RustDesk 可以开机后直接控制桌面，不会被锁屏阻挡

##### 关闭休眠系统设置 → 电源管理(Power Managerment)
禁用“休眠”或“自动挂起”
保证电脑开机一直在线，RustDesk 可随时远控
如果必须休眠，可在 BIOS 设置里禁用休眠，保证开机即联网


#### RustDesk自建服务器

##### 在服务器端安装

###### 命令行方式

1. 打开官网:https://rustdesk.com/docs/zh-cn/self-host/

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768144998052.webp)


2. 用ssh访问你需要安装RustDesk的服务器
```bash
ssh tungchiahui@10.0.0.4
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768145500558.webp)

3. 使用方法二的命令

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768145605588.webp)

```bash
wget https://raw.githubusercontent.com/techahold/rustdeskinstall/master/install.sh
chmod +x install.sh
./install.sh
```

./install.sh的设置看下一节


4. 配置与查看KEY

这里如果你是想直接用公网ip则选择`1)IP`,如果你是DDNS等用域名绑定了IP地址,则选择`2)`.

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768148704646.webp)

这个选`2)No`

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768148806049.webp)

这个是KEY,要复制下来,等后面软件配置的时候要用.

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768148866392.webp)

5. 配置防火墙
    1. 如果你是Debian系
    安装防火墙
    ```bash
    sudo apt install ufw
    ```

    配置防火墙
    ```bash
    sudo ufw allow 22

    ufw allow 21114:21119/tcp
    ufw allow 21116/udp
    sudo ufw enable
    ```
    2. 如果你是RHEL系
    安装防火墙
    ```bash
    # 如果你是RHEL系
    sudo dnf install firewalld
    sudo systemctl enable --now firewalld
    ```

    配置防火墙
    ```bash
    sudo firewall-cmd --add-port=22/tcp --permanent
    sudo firewall-cmd --reload

    sudo firewall-cmd --add-port=21114-21119/tcp --permanent
    sudo firewall-cmd --reload

    sudo firewall-cmd --add-port=21116/udp --permanent
    sudo firewall-cmd --reload

    sudo systemctl enable --now firewalld
    ```



###### 1Panel方式

1. 在面板里搜RustDesk并安装

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768146100467.webp)

2. 配置防火墙

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768146828373.webp)

添加几个端口
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768146848849.webp)
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768146858053.webp)
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768146876612.webp)

3. 查看KEY
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768146996637.webp)

复制KEY

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768147034654.webp)



##### 自建服务器后配置RustDesk软件

1. PC端

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768147115389.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768147204480.webp)


出现就绪就是配置成功了
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768147547538.webp)

2. 移动端

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768149138253.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768149199158.webp)

###### 注意的问题
如果你是家庭的内网服务器,则需要让路由器也放行对应端口

找到openwrt防火墙设置,点击添加
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768148357743.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768148378543.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/1768148398594.webp)

这样才可以让你的服务器端口被真正成功放行.
