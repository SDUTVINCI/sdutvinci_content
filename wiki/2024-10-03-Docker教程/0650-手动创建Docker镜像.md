---
vinciId: 8b5f3aed-3bf2-5d43-91d3-b0b1a46ee231
title: 手动创建Docker镜像
description: （嫌麻烦的话，直接去看各种docker容器部署的章节）（有别人给你创建好的，就别自己折腾啦）
authors:
  - dongjiahui
publishedAt: 2024-10-03T00:00:00.000Z
updatedAt: 2026-08-18T16:48:11.514Z
---

Docker 镜像是通过 `Dockerfile` 文件来构建的。该文件没有任何扩展名，文件名固定为 `Dockerfile`。

从本质上来说，`Dockerfile` 可以看作是一份用于自动化构建镜像的脚本文件。我们在日常使用 Linux 系统时，在终端中执行的各种安装、配置命令（例如安装软件、修改环境变量等），都可以按照一定的语法规则写入到 `Dockerfile` 中。

不过，这些普通的 Shell 命令需要配合 Docker 提供的特定指令（如 `RUN`、`COPY`、`ENV` 等）一起使用，Docker 才能够正确解析并执行这些步骤。构建镜像的过程，本质上就是按照 `Dockerfile` 中定义的指令，从上到下一步步执行，最终生成一个可用的镜像。

通过这种方式，我们可以将环境配置过程标准化、自动化，从而实现环境的一致性与可复现性。

下面列出了一些常用的 Dockerfile 指令及其作用：

### DockerFile常用命令
| 指令 | 说明 | 示例 |
|:---|:---|:---|
| FROM | 指定基础镜像，是 Dockerfile 的起点 | FROM ubuntu:22.04 |
| LABEL | 添加元数据（如作者、版本等） | LABEL maintainer="you@example.com" |
| ENV | 设置环境变量 | ENV PORT=8080 |
| ARG | 构建参数，只在构建期间可用 | ARG VERSION=1.0 |
| RUN | 构建镜像时运行命令 | RUN apt-get update && apt-get install -y curl |
| COPY | 复制文件到镜像中 | COPY . /app |
| ADD | 类似 COPY，额外支持解压 .tar 文件或远程 URL（不推荐用于 URL） | ADD archive.tar.gz /data/ |
| WORKDIR | 设置工作目录 | WORKDIR /opt |
| CMD | 设置容器启动时默认命令（可被 docker run 覆盖） | CMD ["node", "index.js"] |
| ENTRYPOINT | 设置容器启动时固定命令（通常用于 CLI 工具等） | ENTRYPOINT ["python3"] |
| EXPOSE | 声明镜像内服务监听的端口（不会自动映射） | EXPOSE 80 |
| VOLUME | 声明数据卷挂载点 | VOLUME ["/data"] |
| USER | 设置后续命令执行的用户 | USER appuser |
| ONBUILD | 当镜像作为其他镜像基础镜像时触发的构建指令 | ONBUILD COPY . /src |
| SHELL | 更改默认 shell，比如将 sh -c 改为 bash -c | SHELL ["/bin/bash", "-c"] |
| HEALTHCHECK | 定义容器运行时的健康检查命令 | `HEALTHCHECK CMD curl --fail http://localhost:8080` |
| STOPSIGNAL | 容器停止时发送的信号 | STOPSIGNAL SIGKILL |

### 自己创建容器
#### 手动创建

首先创建一个文件夹来存放你的`Dockerfile`
比如我在`~/UserFolder/MySource/`下创建了一个`MyDocker`文件夹.

创建好后,在个文件夹下打开vscode.

```bash
cd ~/UserFolder/MySource/MyDocker
code .
```

在VScode插件中安装`Dev Containers`,`Container Tools`这俩插件.

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782044511801.webp)

1.  插件1：微软Docker工具

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image21.webp)

*docker扩展插件已经进化为container tools了，请安装container tools。*

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image22.webp)

2.  插件2：微软Docker远程开发工具

下面这是远程开发的插件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image23.webp)


接下来创建`Dockerfile`文件:

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782044668776.webp)


我们创建一个最简单的X86架构的镜像,这个镜像的内容是:
1. 基于Ubuntu24.04系统的镜像
2. 自带CUDA和CuDNN
3. 默认Shell环境为bash
4. 换源为国内镜像源
5. Shell添加上丰富的颜色

```dockerfile
# 基于NVIDIA官方CUDA 12.6和CuDNN基础镜像
FROM nvidia/cuda:12.6.0-cudnn-devel-ubuntu24.04

# 设置环境变量以防止交互安装
ENV DEBIAN_FRONTEND=noninteractive

# 强制使用 Bash 作为默认 Shell
SHELL ["/bin/bash", "-c"]

# 先更新包列表并安装CA证书
RUN apt-get update && apt-get install -y ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# 替换为清华大学的DEB822格式镜像源
RUN sed -i 's|http://archive.ubuntu.com/ubuntu/|https://mirrors.tuna.tsinghua.edu.cn/ubuntu/|g' /etc/apt/sources.list.d/ubuntu.sources

# 配置终端颜色和提示符
RUN sed -i 's/#force_color_prompt=yes/force_color_prompt=yes/g' /root/.bashrc && \
    # 自定义PS1（带颜色的提示符）
    echo 'export PS1="\[\033[1;36m\]\u@\h\[\033[00m\]:\[\033[1;34m\]\w\[\033[00m\]\$ "' >> /root/.bashrc && \
    # 给ls、grep等命令添加别名
    echo "alias ls='ls --color=auto'" >> /root/.bashrc && \
    echo "alias grep='grep --color=auto'" >> /root/.bashrc && \
    echo "alias ll='ls -alFh --color=auto'" >> /root/.bashrc && \
    # 清理临时文件
    rm -rf /tmp/LS_COLORS
```
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782045300397.webp)

在x86电脑上编译x86的镜像(一定要有科学的网络环境,不然拉取不了英伟达的镜像)：

命令格式为`docker build -t 镜像名称 .`
比如:

```bash
docker build -t ubuntu24_cuda126_cudnn:latest .
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782045360444.webp)

然后使用`docker images`命令查看编译好的镜像.

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782045993616.webp)

#### 运行此镜像

使用[run命令的参数非常重要](/wiki/2024-10-03-docker-jiao-cheng/0600-docker-ming-ling-xue-xi#run命令的参数非常重要)里的run命令来运行创建好的镜像.

应该把`--name`修改为你要创建的容器的名字,比如`ubuntu24cuda126`,然后最底下那行应该写你刚才创建的镜像的名字,比如`ubuntu24_cuda126_cudnn:latest`.

对应本教程的命令应该为:
```bash
sudo docker run --name=ubuntu24cuda126 \
--gpus all \
-e NVIDIA_DRIVER_CAPABILITIES=all \
-e DISPLAY=$DISPLAY \
-dit \
--privileged \
--net=host \
--group-add audio \
--group-add video \
--group-add dialout \
-e XAUTHORITY=$HOME/.Xauthority \
-e WAYLAND_DISPLAY=$WAYLAND_DISPLAY \
-e XDG_RUNTIME_DIR=$XDG_RUNTIME_DIR \
-e QT_QPA_PLATFORM=xcb \
-v /tmp/.X11-unix:/tmp/.X11-unix:rw \
-v /dev/dri:/dev/dri \
-v $HOME/.Xauthority:$HOME/.Xauthority:ro \
-v /run/user/$(id -u)/wayland-0:/run/user/$(id -u)/wayland-0 \
-v /run/user/$(id -u):/run/user/$(id -u) \
-v $HOME:$HOME \
-w $HOME \
ubuntu24_cuda126_cudnn:latest
```

出现下列这一行,代表成功了.

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782046035662.webp)


可以在VScode的插件里看到容器已经成功运行.

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782046118380.webp)

#### 打开容器里的Ubuntu24系统的终端

命令格式为`docker exec -it 容器名称 bash`
比如:

```bash
docker exec -it ubuntu24cuda126 bash
```

如下图你已经进入了容器里的Ubuntu24系统的终端了.

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782046265023.webp)

接下来可以安装一个`neofetch`测试一下.

注意,你这里已经是在`root`的身份下运行Ubuntu24系统的终端了,所以不需要每条命令前加`sudo`了,比如`sudo apt update`可以直接用`apt update`代替.


```bash
apt update
apt install neofetch
neofetch
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782046451343.webp)


然后可以顺便测试下英伟达驱动和CUDA以及CuDNN的安装情况:

```bash
nvidia-smi
nvcc -V
cat /usr/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
```

如下图可知,容器中的Ubuntu24共享了宿主的英伟达驱动,版本为595.80.
自带CUDA12.6与CuDNN9.3.0

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782047529844.webp)

#### 将镜像推送至Docker Hub


将 Docker 镜像推送到 Docker Hub 的步骤如下：

1.  创建 Docker Hub 账户

如果你还没有 Docker Hub 账户，请前往 Docker Hub 注册一个免费账户。

2.  登录 Docker Hub

在终端中使用以下命令登录到你的 Docker Hub 账户：

```bash
docker login
```

输入你的 Docker Hub 用户名和密码进行验证。

3.  为你的镜像打标签

Docker Hub 使用 `<用户名>/<镜像名>:<标签>` 的格式来标识镜像。你需要为你的镜像打上标签，以便能够推送到 Docker Hub。使用以下命令：

```bash
docker tag 镜像名:标签 <你的用户名>/新镜像名:标签
```

例如，如果你的 Docker Hub 用户名是 `tungchiahui`，你应该执行：

```bash
docker tag ubuntu24_cuda126_cudnn:latest tungchiahui/ubuntu24_cuda126_cudnn:latest
```

4.  推送镜像到 Docker Hub

使用以下命令将镜像推送到 Docker Hub：

```bash
docker push <你的用户名>/镜像名:标签
```

例如：

```bash
docker push tungchiahui/ubuntu24_cuda126_cudnn:latest
```

5.  验证推送成功

你可以通过访问 Docker Hub 的个人页面来验证你的镜像是否已成功推送。

**注意事项**

*   确保你的镜像大小在 Docker Hub 的限制范围内（一般为 10GB）。

*   如果你打算将镜像公开，可以设置为公共仓库；如果希望只有你自己可以访问，可以设置为私有仓库。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image16.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image17.webp)

#### 手动创建(跨平台多架构构建)(高阶的,暂时不用学)

如果您想在 **x86/x64 电脑上即为本机x86设备构建镜像，又想为树莓派、Jetson等ARM64 设备构建 Docker 镜像** ，需要使用 **Docker 的跨平台构建功能** 。以下是完整解决方案：

* * *

**启用 Docker 跨平台构建**

在 x86 主机上模拟 ARM64 环境需要以下工具：

1. 启用 buildx（只需执行一次）

```bash
docker buildx create --name multiarch_builder --use
```

这会创建并启用一个支持多架构构建的 builder，电脑重启后也依然存在，所以只用运行一次。

2. 安装 QEMU 支持（一般新版 Docker Desktop 已自带，但是Linux必须要安装） 如果你用的是服务器或Linux发行版，确保有 qemu 模拟器：

```bash
docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
```

电脑重启后，就会消失，所以需要你每次电脑重启后，在buildx命令前，运行一次该命令即可。

3. 修改dockerfile为多架构版本

```dockerfile
# 支持多架构，使用变量指定架构
ARG TARGETARCH

# 基于NVIDIA官方CUDA 12.6和CuDNN基础镜像
FROM --platform=linux/${TARGETARCH} nvidia/cuda:12.6.0-cudnn-devel-ubuntu24.04

# 设置环境变量以防止交互安装
ENV DEBIAN_FRONTEND=noninteractive

# 强制使用 Bash 作为默认 Shell
SHELL ["/bin/bash", "-c"]

# 先更新包列表并安装CA证书
RUN apt-get update && apt-get install -y ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# 替换为清华大学的DEB822格式镜像源
RUN if [ "$(dpkg --print-architecture)" = "amd64" ]; then \
        sed -i 's|http://archive.ubuntu.com/ubuntu/|https://mirrors.tuna.tsinghua.edu.cn/ubuntu/|g' /etc/apt/sources.list.d/ubuntu.sources; \
    elif [ "$(dpkg --print-architecture)" = "arm64" ]; then \
        sed -i 's|http://ports.ubuntu.com/ubuntu-ports/|https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/|g' /etc/apt/sources.list.d/ubuntu.sources; \
    fi

# 配置终端颜色和提示符
RUN sed -i 's/#force_color_prompt=yes/force_color_prompt=yes/g' /root/.bashrc && \
    # 自定义PS1（带颜色的提示符）
    echo 'export PS1="\[\033[1;36m\]\u@\h\[\033[00m\]:\[\033[1;34m\]\w\[\033[00m\]\$ "' >> /root/.bashrc && \
    # 给ls、grep等命令添加别名
    echo "alias ls='ls --color=auto'" >> /root/.bashrc && \
    echo "alias grep='grep --color=auto'" >> /root/.bashrc && \
    echo "alias ll='ls -alFh --color=auto'" >> /root/.bashrc && \
    # 清理临时文件
    rm -rf /tmp/LS_COLORS
```


4. 构建多架构镜像 用下面的命令构建 amd64 和 arm64：

```bash
docker buildx build --platform linux/amd64,linux/arm64 -t <你的镜像名>:<标签> --push .
```


例子：


```bash
docker buildx build \
--platform linux/amd64,linux/arm64 \
 -t tungchiahui/ubuntu24_cuda126_cudnn:latest \
 --push \
 .
```

说明： --platform 指定多架构。 --push 是必须的，因为 buildx 的多平台构建默认是不能本地加载的（除非加 --load，但那只能支持单一架构）。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image18.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image19.webp)

#### 清除构建缓存

```bash
# 清理BuildKit构建缓存
docker builder prune -f  
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image20.webp)
