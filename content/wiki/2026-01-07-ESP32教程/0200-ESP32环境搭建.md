---
title: "ESP32环境搭建"
---

### Linux
打开下面的网站,
https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/get-started/linux-macos-setup.html

#### 安装依赖
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767934049542.webp)

第一步按照这个网站所示,如果你是Ubuntu

```bash
sudo apt-get install git wget flex bison gperf python3 python3-pip python3-venv cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0
```

如果你是Fedora

```bash
sudo dnf -y makecache && sudo dnf install git wget flex bison gperf python3 python3-setuptools cmake ninja-build ccache dfu-util libusbx
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767934192251.webp)

#### 获取 ESP-IDF

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767934330673.webp)

看看目前最新稳定版是哪个版本

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767934480759.webp)

如上图所示,为5.5.2版本.
接下来,你选择一个你要存放固件的文件夹,
比如我要放在/home/tungchiahui/UserFolder/Applications/文件夹下(其中`/home/tungchiahui`可用`~/`代替)
那么

```bash
cd ~/UserFolder/Applications
mkdir -p ./esp
cd ./esp
# 记得版本号要改成最新稳定版(网络环境一定要好)
git clone -b v5.5.2 --recursive https://github.com/espressif/esp-idf.git
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767934869161.webp)

如图才是成功,不是下面这样的都是没下载成功的
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767935331654.webp)


#### 设置工具
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767934989357.webp)

```bash
cd ./esp-idf
# 选择国内服务器
export IDF_GITHUB_ASSETS="dl.espressif.cn/github_assets"
./install.sh all
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767935447878.webp)


![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767935597546.webp)

上图就是成功的样子,这里要复制一下我红色圈起来的东西(`export.sh`的路径),下一个环境配置中要使用,比如我这里复制下来的路径是
`~/UserFolder/Applications/esp/esp-idf/export.sh`


#### 设置环境变量
此时，刚刚安装的工具尚未添加至 PATH 环境变量，无法通过“命令窗口”使用这些工具。因此，必须设置一些环境变量。这可以通过 ESP-IDF 提供的另一个脚本进行设置。

```bash
vim ~/.bashrc
```

添加下面这句(这里要填你具体存放的路径,也就是刚才复制的那串路径,记得把`~`改成`$HOME`,增加健壮性)
```bash
alias get_idf='. $HOME/UserFolder/Applications/esp/esp-idf/export.sh'
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767935820486.webp)

```bash
source ~/.bashrc
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767935970620.webp)


#### 配置VScode
https://docs.espressif.com/projects/vscode-esp-idf-extension/zh_CN/latest/index.html

1. 安装ESP-IDF插件

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767936248029.webp)

2. 点击 Express 并选择下载服务器：

按下图的选,因为我们刚才配置过环境了,这里不要再选择版本了,直接去选择从我的电脑里找到ESP-IDF

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767936512526.webp)

他会自动帮你补全所有工具链

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767936567868.webp)

右下角这么显示则为成功

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767936602550.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767936647403.webp)


紧接着要配置 openOCD :
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767936736342.webp)
复制上面这行,并开终端(任意终端都行,不用管路径)运行

```bash
sudo cp --update=none /home/tungchiahui/.espressif/tools/openocd-esp32/v0.12.0-esp32-20250707/openocd-esp32/share/openocd/contrib/60-openocd.rules /etc/udev/rules.d
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1767936805629.webp)

结束!

### Windows
打开下面的网站,
https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/get-started/windows-setup.html

#### 下载安装程序
查看当前最新版，比如我这里最新版是v5.5.2
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768097572869.webp)

点击工具下载
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768097420298.webp)

并点击最新版的安装，比如我这里最新版是v5.5.2

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768097535450.webp)

#### 安装ESP-IDF
点击下载好的esp-idf
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768097744578.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768097780350.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768097800892.webp)

选择你想安装的硬盘分区，比如我要安装在`C:\Espressif`，那就是如下图这样。
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768097823822.webp)
如果你想安装在D盘，就把第一个C改成D。
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768097881504.webp)

把这些没勾的全部勾上，点安装
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768097941290.webp)

然后点完成
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768099670485.webp)

弹出来的框都这么显示，则是成功安装了
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768099702456.webp)

#### 配置环境
随便打开一个文件夹，右键`This PC（此电脑）`，点`属性`
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768099767864.webp)

这里有个高级系统设置
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768099838542.webp)

点环境变量
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768099861401.webp)


点击新建
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768099886239.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768099909017.webp)

找到刚才安装idf的目录里的这个目录点确定

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768099932086.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768099957136.webp)

#### 配置VScode

安装下面这个插件

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100027759.webp)

安装完毕后，点击打开开始向导

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100049539.webp)

点击第一个
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100080553.webp)


按我这么来，然后点安装
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100238920.webp)

这个界面就是安装成功
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100445841.webp)

#### 下载串口驱动
打开下面这个网站
https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/get-started/establish-serial-connection.html

1. CP210x驱动

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100660387.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100674154.webp)

解压刚才下载的压缩包
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100712206.webp)

右键`silabser.inf`点击`安装`
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100816094.webp)
安装完毕
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100839744.webp)

2. FTDI驱动

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768101118029.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768101142315.webp)

同样解压下载好的压缩包

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768101174406.webp)

分别右键下面这俩，点安装

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768101274422.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100839744.webp)

3. CH340驱动
打开下面的网站
https://www.wch.cn/products/ch340.html

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100958652.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768100976886.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768101016790.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/01/07/1768101038694.webp)
