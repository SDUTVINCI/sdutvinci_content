---
title: "Docker直通"
---

### USB直通
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image5.webp)

1.  方法一(不是很推荐新手)：

创建容器的时候把需要的设备加在红色部分这里即可。可以通过下面的命令查看想要的设备的名字。

```bash
ls /dev
#例如
--device=/dev/tty_USB0
```

2.  方法二(个人更加推荐，不用重新再挂载了，虽然安全性会降低，但是别人利用安全权限能够攻击你的概率很低很低，企业服务器才需要提防)：

```bash
--privileged
```

直接添加一行绿色部分，然后所有设备都会被挂载到docker了。(红色部分就不用写了）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image6.webp)

### NVIDIA显卡直通
NVIDIA Container Toolkit使用户 **能够构建和运行GPU加速的容器** 。该工具包包括一个容器运行库和实用程序，用于自动配置容器以利用NVIDIA GPU。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image7.webp)

https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html

#### 安装
（尽量能看官方就看官方的，安装方式可能会更新）

##### Ubuntu
1.  配置存储库 并更新

```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list \
  && \
    sudo apt-get update
```

2.  安装nvidia-docker2

```bash
sudo apt-get install -y nvidia-docker2
```

3.  使用nvidia-ctk命令配置container runtime

```bash
sudo nvidia-ctk runtime configure --runtime=docker
```

4.  重启docker服务:

```bash
sudo systemctl restart docker
```

##### Fedora
1.  Configure the production repository:

```bash
curl -s -L https://nvidia.github.io/libnvidia-container/stable/rpm/nvidia-container-toolkit.repo | \
sudo tee /etc/yum.repos.d/nvidia-container-toolkit.repo
```

2.  Optionally, configure the repository to use experimental packages:

```bash

# 如果是RHEL或者Rocky（DNF4）
sudo dnf config-manager --add-repo https://nvidia.github.io/libnvidia-container/stable/rpm/libnvidia-container.repo

# 如果是Feodra41+（DNF5）
sudo dnf config-manager addrepo --from-repofile=https://nvidia.github.io/libnvidia-container/stable/rpm/libnvidia-container.repo
```

3.  Install the NVIDIA Container Toolkit packages:

```bash

# 如果是RHEL或者Rocky
sudo dnf install -y nvidia-container-toolkit

# 如果是Fedora
sudo dnf install -y nvidia-container-toolkit
```

4.  **安装nvidia-docker2** ： 在Fedora上使用`dnf`进行安装。

```bash
sudo dnf install -y nvidia-docker2
```

5.  **使用nvidia-ctk命令配置容器运行时** ： 这个命令用于配置NVIDIA Container Toolkit与Docker集成。命令如下：

```bash
sudo nvidia-ctk runtime configure --runtime=docker
```

6.  **重启Docker服务** ： 完成配置后，必须重启Docker服务以使更改生效：

```bash
sudo systemctl restart docker
```

这些步骤执行后，Docker将使用NVIDIA Container Runtime，并支持GPU加速的容器运行。你可以使用`nvidia-smi`命令来验证容器中的NVIDIA GPU是否可用。

#### 测试是否可用
1.  **运行nvidia cuda 容器进行测试** ：

可以使用下面指令进行测试，docker会自动从nvidia/cuda拉取11.0.3-base-ubuntu20.04镜像，并创建一个运行一次即删除的容器。

```bash
sudo docker run --rm --gpus all nvidia/cuda:11.0.3-base-ubuntu20.04 nvidia-smi
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image8.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image9.webp)

### Docker配置CUDA和CuDNN
如果你不需要自己创建Docker镜像，直接使用学长或者其他人创建好的镜像，则不用看该章节，容器的CUDA和CuDNN是和本地完全隔离的环境，你本地有没有CUDA都无所谓，但英伟达驱动版本必须满足CUDA的最低版本要求。

下面是如果你想自己创建镜像，则可以在学会如何创建容器的镜像后再回来看本节：

https://hub.docker.com/r/nvidia/cuda

在上方这个网站中，英伟达都帮我们配置好了CUDA和CuDNN了，我们根本不需要自己去配置了，比传统方式要简单太多太多了。

我们只需要找到对应的Docker镜像当底包即可。

CUDA镜像有三个类型，如下，如果我们需要编译OpenCV4,那么需要使用devel版的CUDA。

| 镜像类型 | 适用场景 | 示例标签 | 大小 |
|:---|:---|:---|:---|
| base | 仅需 CUDA 运行时库 | 12.4.0-base-ubuntu22.04 | ~240MB |
| runtime | 部署编译后的应用（含数学库） | 12.4.0-runtime-ubuntu22.04 | ~2GB |
| devel | 开发环境（含编译工具） | 12.4.0-devel-ubuntu22.04 | ~3GB |

我示例一个，比如我想用在Ubuntu24.04上用CUDA12.6和CuDNN，那么就选择`nvidia/cuda:12.6.0-cudnn-devel-ubuntu24.04`。这个镜像既有CUDA-devel也有CuDNN，而且还是基于Ubuntu24.04的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image10.webp)

在dockerfile里就可以开头这么写。

```dockerfile

# 基于NVIDIA官方CUDA 12.6和CuDNN基础镜像
FROM nvidia/cuda:12.6.0-cudnn-devel-ubuntu24.04
```

### 配置以太网
```bash
--net=host \
```

启动参数里加上这行即可。具体在下方。
