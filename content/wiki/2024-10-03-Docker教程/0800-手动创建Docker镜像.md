---
title: "手动创建Docker镜像"
---

**（嫌麻烦的话，直接去看各种docker容器部署的章节）（有别人给你创建好的，就别自己折腾啦）**

### DockerFile
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
https://github.com/tungchiahui/ros-docker

```bash

# DockerFile内容请看Github仓库中的DockerFile
```

在x86电脑上编译x86的：

```bash
docker build -t ros-melodic-cuda118-cudnn8-bionic:latest .

docker build -t ros-noetic-focal:latest .

docker build -t ros-humble-jammy:latest .

docker build -t ros-jazzy-noble:latest .

docker build -t ros-humble-opencv411-cuda128-cudnn970-jammy:latest .

docker build -t ros-jazzy-opencv411-cuda128-cudnn970-noble:latest .
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image14.webp)

镜像大小5GB(压缩后的大小详见DockerHub)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image15.webp)

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
docker tag ros-jazzy-noble:latest <你的用户名>/ros-jazzy-noble:latest
```

例如，如果你的 Docker Hub 用户名是 `tungchiahui`，你应该执行：

```bash
docker tag ros-noetic-focal:latest tungchiahui/ros-noetic-focal:latest

docker tag ros-humble-jammy:latest tungchiahui/ros-humble-jammy:latest

docker tag ros-jazzy-noble:latest tungchiahui/ros-jazzy-noble:latest

docker tag ros-humble-opencv411-cuda128-cudnn970-jammy:latest tungchiahui/ros-humble-opencv411-cuda128-cudnn970-jammy:latest

docker tag ros-jazzy-opencv411-cuda128-cudnn970-noble:latest tungchiahui/ros-jazzy-opencv411-cuda128-cudnn970-noble:latest
```

4.  推送镜像到 Docker Hub

使用以下命令将镜像推送到 Docker Hub：

```bash
docker push <你的用户名>/ros-noetic-jazzy-noble:latest
```

例如：

```bash
docker push tungchiahui/ros-noetic-focal:latest

docker push tungchiahui/ros-humble-jammy:latest

docker push tungchiahui/ros-jazzy-noble:latest

docker push tungchiahui/ros-humble-opencv411-cuda128-cudnn970-jammy:latest

docker push tungchiahui/ros-jazzy-opencv411-cuda128-cudnn970-noble:latest

docker push tungchiahui/ros-noetic-focal-arm64:latest
```

5.  验证推送成功

你可以通过访问 Docker Hub 的个人页面来验证你的镜像是否已成功推送。

**注意事项**

*   确保你的镜像大小在 Docker Hub 的限制范围内（一般为 10GB）。

*   如果你打算将镜像公开，可以设置为公共仓库；如果希望只有你自己可以访问，可以设置为私有仓库。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image16.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image17.webp)

#### 手动创建(跨平台多架构构建)
如果您想在 **x86/x64 电脑上即为本机x86设备构建镜像，又想为树莓派、Jetson等ARM64 设备构建 Docker 镜像** ，需要使用 **Docker 的跨平台构建功能** 。以下是完整解决方案：

* * *

1\. **启用 Docker 跨平台构建**

在 x86 主机上模拟 ARM64 环境需要以下工具：

第一步：启用 buildx（只需执行一次）

```Shell
docker buildx create --name multiarch_builder --use
```

这会创建并启用一个支持多架构构建的 builder，电脑重启后也依然存在，所以只用运行一次。

第二步：安装 QEMU 支持（一般新版 Docker Desktop 已自带，但是Linux必须要安装） 如果你用的是服务器或Linux发行版，确保有 qemu 模拟器：

```bash
docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
```

电脑重启后，就会消失，所以需要你每次电脑重启后，在buildx命令前，运行一次该命令即可。

第三步：构建多架构镜像 用下面的命令构建 amd64 和 arm64：

```bash
docker buildx build --platform linux/amd64,linux/arm64 -t <你的镜像名>:<标签> --push .

# 例子：
docker buildx build \
--platform linux/amd64,linux/arm64 \
 -t tungchiahui/ros:noetic-focal \
 --push \
 .

docker buildx build \
--platform linux/amd64,linux/arm64 \
 -t tungchiahui/ros:humble-jammy \
 --push \
 .

docker buildx build \
--platform linux/amd64,linux/arm64 \
 -t tungchiahui/ros:jazzy-noble \
 --push \
 .

 docker buildx build \
--platform linux/amd64,linux/arm64 \
 -t tungchiahui/opencv:411-cuda128-cudnn970-focal \
 --push \
 .

docker buildx build \
--platform linux/amd64,linux/arm64 \
 -t tungchiahui/opencv:411-cuda128-cudnn971-jammy \
 --push \
 .

 docker buildx build \
--platform linux/amd64,linux/arm64 \
 -t tungchiahui/opencv:411-cuda128-cudnn971-noble \
 --push \
 .

docker buildx build \
--platform linux/amd64,linux/arm64 \
 -t tungchiahui/ros-opencv:noetic-411-cuda128-cudnn970-focal \
 --push \
 .

docker buildx build \
--platform linux/amd64,linux/arm64 \
 -t tungchiahui/ros-opencv:humble-411-cuda128-cudnn970-jammy \
 --push \
 .

 docker buildx build \
--platform linux/amd64,linux/arm64 \
 -t tungchiahui/ros-opencv:jazzy-411-cuda128-cudnn970-noble \
 --push \
 .

  docker buildx build \
--platform linux/amd64,linux/arm64 \
 -t sdutvincirobot/ros-opencv:humble-411-cuda128-cudnn970-jammy \
 --push \
 .
```

说明： --platform 指定多架构。 --push 是必须的，因为 buildx 的多平台构建默认是不能本地加载的（除非加 --load，但那只能支持单一架构）。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image18.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image19.webp)

#### 清除构建缓存
```C++

# 清理BuildKit构建缓存
docker builder prune -f  
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image20.webp)
