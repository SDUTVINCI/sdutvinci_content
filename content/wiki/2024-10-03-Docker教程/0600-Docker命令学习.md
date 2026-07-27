---
title: "Docker命令学习"
---

### 参考文档
https://www.runoob.com/docker/docker-tutorial.html

### 常用命令
常用的标红了，偶尔用的标绿了，其他了解就行。

| 命令 | 描述 | 示例 |
|:---|:---|:---|
| docker run | 创建并启动一个新的容器。 | docker run -it ubuntu bash |
| docker build | 通过指定的 Dockerfile 创建一个新的镜像。 | docker build -t myimage . |
| docker pull | 从 Docker 仓库拉取镜像。 | docker pull ubuntu |
| docker push | 将本地镜像推送到 Docker 仓库。 | docker push myimage |
| docker stop | 停止一个正在运行的容器。 | docker stop container_id |
| docker start | 启动一个已经存在的容器。 | docker start container_id |
| docker restart | 重新启动容器。 | docker restart container_id |
| docker ps | 列出当前正在运行的容器。 | docker ps |
| docker rm | 删除一个或多个停止的容器。 | docker rm container_id |
| docker exec | 在一个正在运行的容器中执行命令。 | docker exec -it container_id bash |
| docker logs | 查看容器的日志输出。 | docker logs container_id |
| docker images | 列出本地所有镜像。 | docker images |
| docker rmi | 删除一个或多个镜像。 | docker rmi myimage |
| docker network | 管理 Docker 网络。 | docker network ls |
| docker volume | 管理 Docker 数据卷。 | docker volume ls |
| docker-compose up | 启动 docker-compose.yml 中定义的所有服务。 | docker-compose up |
| docker-compose down | 停止并移除 docker-compose.yml 中定义的所有服务及其相关资源。 | docker-compose down |
| docker info | 显示 Docker 系统的详细信息。 | docker info |
| docker stats | 查看正在运行的容器的实时资源使用情况（CPU、内存等）。 | docker stats |
| docker inspect | 查看容器或镜像的详细信息（JSON 格式）。 | docker inspect container_id |
| docker save | 将一个镜像保存为 tar 文件。 | docker save -o myimage.tar myimage |
| docker load | 从 tar 文件中加载镜像。 | docker load -i myimage.tar |
| docker tag | 为镜像添加标签（tag）。 | docker tag myimage myimage:v1 |
| docker buildx build | 使用 Buildx 构建多架构镜像。 | docker buildx build -t myimage . |
| docker buildx create | 创建一个新的 Buildx 构建实例。 | docker buildx create --use |
| docker buildx ls | 列出所有可用的 Buildx 构建实例。 | docker buildx ls |
| docker buildx use | 设置当前的 Buildx 构建实例。 | docker buildx use mybuilder |
| docker buildx bake | 使用 Bake 文件批量构建镜像。 | docker buildx bake -f bake.hcl |
| docker buildx build --push | 构建镜像并推送到镜像仓库。 | docker buildx build --push -t myimage . |
| docker buildx build --platform | 构建镜像并为多个平台生成支持。 | docker buildx build --platform linux/amd64,linux/arm64 -t myimage . |

#### **run命令的参数（非常重要）**
| 参数/配置 | 功能说明 | 重要性与参考依据 |
|:---|:---|:---|
| --name=ros_jazzy_opencv411_cuda128_cudnn971_noble | 指定容器名称，便于后续管理 | 替代随机生成的容器名。 |
| --gpus all | 允许容器访问宿主机所有GPU资源，需NVIDIA驱动支持 | 用于CUDA加速等GPU依赖任务。 |
| -e NVIDIA_DRIVER_CAPABILITIES=all | 启用NVIDIA驱动的全部功能（如CUDA、图形渲染） | 确保容器内GPU功能完整67。 |
| -dit | 组合参数：- -d：后台运行容器（Detached模式）- -i：保持标准输入（STDIN）开放- -t：分配伪终端（TTY） | 允许容器在后台运行并支持交互操作。 |
| --privileged | 赋予容器完全主机权限（可访问设备、内核模块等） | 用于需要直接操作硬件的场景（如访问USB设备），但存在安全风险。 |
| --net=host | 共享宿主机网络命名空间（容器使用宿主机IP和端口） | 简化网络配置，无NAT，这样的话，网络效率更高，局域网设备更容易发现。 |
| --group-add audio--group-add video--group-add dialout | 将容器用户加入宿主机用户组：- audio：音频设备访问- video：视频设备访问- dialout：串口设备访问 | 避免权限问题（如避免无法调用摄像头、麦克风）。 |
| -e DISPLAY=$DISPLAY-e XAUTHORITY=/home/tungchiahui/.Xauthority-e WAYLAND_DISPLAY-e XDG_RUNTIME_DIR-e QT_QPA_PLATFORM=xcb | 配置图形显示环境：- 绑定宿主机显示接口（X11或Wayland）- 设置GUI应用渲染后端 | 支持容器内运行图形界面应用（如OpenCV可视化）。 |
| -v /tmp/.X11-unix:/tmp/.X11-unix:rw-v /dev/dri:/dev/dri | 挂载宿主机图形设备：- X11套接字目录- 直接渲染管理器（DRI）设备 | 实现容器内图形显示。 |
| -v $HOME/.Xauthority:/home/tungchiahui/.Xauthority:ro | 挂载X11认证文件（只读） | 确保容器有权连接宿主机显示服务。 |
| -v /run/user/1000/wayland-0-v /run/user/1000 | 挂载Wayland显示协议相关目录 | 支持Wayland协议的图形显示。 |
| --ulimit nofile=1024:524288 | 设置进程**最大可打开文件数（nofile）**的方式，用于控制容器或进程运行时的文件句柄数量限制。--ulimit <限制类型>=<软限制>:<硬限制> | 如果默认限制太小，可能会出现 "too many open files" 的错误。所以在容器运行或系统服务启动时，需要调大这个值。--ulimit nofile=4096:65536 |
| -v /home/tungchiahui:/home/tungchiahui | 挂载宿主机用户目录到容器内同名路径 | 实现宿主机与容器间文件共享（如代码、数据持久化）。 |
| -w /home/tungchiahui | 设置容器启动后的默认工作目录 | 直接进入项目路径，方便执行命令2324。 |
| tungchiahui/ros-opencv:jazzy-411-cuda128-cudnn971-noble | 镜像名称指定镜像及标签，包含：- ROS 2 Jazzy- OpenCV 4.11- CUDA 12.8- cuDNN 9.7.1 | 提供预配置的深度学习与机器人开发环境。 |

下方这条命令一定要在普通用户下运行，不要在root用户下运行，其实加不加`sudo`加不加`sudo -E`都无所谓。

用户已经被加到docker组了，不用`sudo`也行跑，其次，`sudo`运行的话，你的`$HOME`变量也不会变，更何况加上-E的话，这样你的`$HOME`更不可能变了。

```bash
sudo docker run --name=ros_opencv_cuda \
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
tungchiahui/ros-opencv:humble-411-cuda128-cudnn970-jammy
```

注意：

1.  `NVIDIA_DRIVER_CAPABILITIES=all` `--gpus all`没有英伟达显卡请注释。

2.  \--name后面请自己为容器起名。

3.  最后一行仓库名称请你自己找对应的镜像填上。

4.  ROS1在Fedora发行版下会爆内存，需要添加上下面这个参数，如果你不是Fedora和ROS1,***请不要加***。

```bash
--ulimit nofile=1024:524288 \
```

5.  如果想用当前用户登陆容器,可以加上下面这几条,但非常非常***不建议*****.**

```bash
--user $(id -u):$(id -g) \
-v /etc/passwd:/etc/passwd:ro \
-v /etc/group:/etc/group:ro \
```
