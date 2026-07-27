---
title: "安装Docker"
---

### Linux安装Docker Engine(推荐)
Linux只需要安装Docker Engine就可以，不要安装docker desktop，那玩意是专门给Mac和Windows用的。

Linux跑docker性能损失很低，而Windows和MacOS跑docker损失相对于大一些。

https://docs.docker.com/engine/install/

https://mirrors.bfsu.edu.cn/help/docker-ce/

#### Ubuntu（APT）
以下内容根据 [官方文档](https://docs.docker.com/engine/install/ubuntu/) 修改而来。

如果你过去安装过 docker，先删掉：

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

首先安装依赖和GPG：

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.asc

# 如果上面这行报错就弄下面这行
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

信任 Docker 的 GPG 公钥并添加仓库：

```bash
# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://mirrors.bfsu.edu.cn/docker-ce/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

最后安装

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

#### Fedora（DNF5）
以下内容根据 [官方文档](https://docs.docker.com/engine/install/fedora/) 修改而来。(官方教程还是DNF4,太老了，请看下方的教程)

如果你之前安装过 docker，请先删掉

```bash
sudo dnf remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```

安装依赖，下载 repo 文件，并把软件仓库地址替换为镜像站：

```bash
sudo dnf -y install dnf-plugins-core
sudo dnf config-manager addrepo --from-repofile=https://download.docker.com/linux/fedora/docker-ce.repo
sudo sed -i 's+https://download.docker.com+https://mirrors.bfsu.edu.cn/docker-ce+' /etc/yum.repos.d/docker-ce.repo
```

最后安装：

```bash
sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 配置环境
#### **检查 Docker 服务状态** ：
在 Linux 上，你可以通过以下命令检查 Docker 服务的状态：

```bash
systemctl status docker
```

#### **启动 Docker 服务** ：
如果服务没有运行，可以使用以下命令启动 Docker 服务：

```bash
sudo systemctl start docker
```

#### **设置 Docker 开机自启** ：
如果你希望 Docker 在每次启动时自动运行，可以启用开机自启：

```bash
sudo systemctl enable docker
```

#### **将用户添加到 `docker` 组** ：
使用以下命令将当前用户添加到 `docker` 组：

```bash
sudo usermod -aG docker $USER
```

#### **退出并重新登录** ：
执行完上述命令后，你需要退出当前会话并重新登录，或者运行以下命令使更改生效：

```bash
newgrp docker
```

#### **重新启动 Docker 服务（如果需要）** ：
确保 Docker 服务正在运行，可以使用以下命令：

```bash
sudo systemctl start docker
```

#### **重启电脑后检查 Docker 服务状态** ：
先重启电脑，接着你可以通过以下命令检查 Docker 服务的状态，看看是否正常：

```bash
sudo reboot

systemctl status docker
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image1.webp)

### 安装Docker Desktop（Win，Mac）
(Docker Desktop在Windows和MacOS使用的是虚拟机，性能有损失，在这俩系统上可以用，但是你需要接受这些性能损失。在Windows上性能损失和WSL2的损失几乎一样，因为Windows的docker desktop基于wsl2)

(Docker Desktop在Linux上只是Docker Engine的一个GUI管理工具，依然默认使用Docker Engine开启容器，所以依然几乎没有损耗，讨厌用命令行的可以考虑使用)

官方下载安装:https://www.docker.com/

Windows的Docker显卡直通与USB直通:

在Windows上想Nvidia显卡直通的话，需要先去DockerDesktop设置里开启WSL2支持并勾选一个wsl2的发行版，比如Ubuntu22.04，紧接着，需要进入wsl2的Ubuntu22.04中安装NVIDIA Container Toolkit，教程在下方。

在Windows的Docker上想要USB直通需要先让wsl2直通该usb，再在docker run命令将该设备添加到docker。(如果把wsl2所有设备全挂载到docker了，那么只需要让usb直通wsl2)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image2.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image3.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image4.webp)
