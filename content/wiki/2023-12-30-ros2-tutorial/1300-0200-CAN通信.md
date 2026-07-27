---
title: "CAN通信"
---

## Linux CAN 通信

CAN 通信是机器人、车辆、工业控制和电机驱动中非常常见的总线通信方式。

相比串口通信，CAN 更适合多个设备挂在同一条总线上进行通信。例如一个机器人底盘中，可能有多个电机驱动器、底盘控制板、传感器节点共同连接在 CAN 总线上。

在实际项目中，CAN 通信常见于：

- 控制电机驱动器
- 读取电机编码器反馈
- 连接底盘控制板
- 多个控制节点之间通信
- 车辆总线通信
- 工业设备和运动控制系统

对于移动机器人底盘、电机控制、自动驾驶线控系统等方向来说，CAN 是非常重要的一类通信方式。

### CAN 通信是什么

CAN，全称 Controller Area Network，是一种多主机总线通信协议。

和串口点对点通信不同，CAN 总线上可以挂多个节点。每个节点都可以发送消息，其他节点根据 CAN ID 判断是否需要处理这条消息。

CAN 通信中常见概念包括：

| 概念 | 说明 |
|---|---|
| CAN ID | 用来标识一帧 CAN 消息的含义或来源 |
| 标准帧 | 11 位 CAN ID |
| 扩展帧 | 29 位 CAN ID |
| 数据区 | 经典 CAN 一帧最多 8 字节数据 |
| CAN FD | CAN 的扩展版本，支持更长数据区和更高数据速率 |
| 波特率 | 常见有 250K、500K、1M |
| 终端电阻 | CAN 总线两端通常需要 120Ω 终端电阻 |

在 Linux 中，CAN 设备通常不是 `/dev/ttyUSB0` 这种设备文件，而是被抽象成网络接口，例如：

```bash
can0
```

可以使用下面的命令查看：

```bash
ip link
```

### Linux 下常见 CAN 工具和库

Linux 下 CAN 通信的核心是 SocketCAN。

SocketCAN 是 Linux 内核提供的 CAN 通信框架，它把 CAN 设备抽象成类似网络接口的形式，例如 `can0`、`can1`。

常见工具和库包括：

| 方案 | 类型 | 特点 | 适合场景 |
|---|---|---|---|
| SocketCAN | Linux 原生 CAN 接口 | Linux 标准方案，通用、稳定、工程常用 | 推荐作为主线学习 |
| can-utils | SocketCAN 命令行工具集 | 提供 `candump`、`cansend`、`cangen` 等工具 | 调试 CAN 总线 |
| libsocketcan | SocketCAN 辅助库 | 便于配置和管理 CAN 接口 | 需要程序内配置 CAN 参数时使用 |
| ros2_socketcan | ROS 2 封装库 | 将 SocketCAN 封装进 ROS 2 生态 | 纯 ROS 2 项目或参考实现 |
| ros2_canopen | ROS 2 CANopen 协议栈 | 适合 CANopen 设备 | 电机驱动器使用 CANopen 协议时 |
| 厂商 SDK | 厂商提供的驱动库 | 和具体硬件绑定 | 使用特定 USB-CAN、PCIe-CAN 设备时 |

### 各方案对比

| 方案 | 优点 | 缺点 |
|---|---|---|
| SocketCAN | Linux 原生、通用、稳定、适合长期学习 | 需要理解 socket 编程和 CAN 帧结构 |
| can-utils | 调试方便，命令简单 | 主要用于命令行测试，不是完整工程封装 |
| libsocketcan | 配置 CAN 接口方便 | 不是主要的数据收发方案 |
| ros2_socketcan | 和 ROS 2 生态结合方便 | 离开 ROS 2 后复用性较差 |
| ros2_canopen | 适合 CANopen 设备，协议栈完整 | 只适合 CANopen 场景，学习成本较高 |
| 厂商 SDK | 对特定硬件支持好 | 通用性较差，容易绑定厂商 |

### 本教程建议选择

本教程建议优先选择：

```text
SocketCAN + can-utils
```

原因是：

- SocketCAN 是 Linux 下 CAN 通信的标准方案
- 不依赖 ROS 2，适合普通 C++、OpenCV、Qt、嵌入式 Linux 项目
- `can-utils` 非常适合调试和验证 CAN 设备
- 后续可以封装成普通 C++ driver
- 可以自然接入 `ros2_control hardware_interface`

推荐的学习顺序是：

```text
1. 使用 ip link 配置 can0
2. 使用 candump 接收 CAN 帧
3. 使用 cansend 发送 CAN 帧
4. 理解 struct can_frame
5. 使用 C++ SocketCAN 进行收发
6. 封装自己的 CanDriver 类
7. 接入 ROS 2 或 ros2_control
```

推荐的工程结构是：

```text
上层项目：ROS 2 / OpenCV / Qt / 普通 C++ 程序
        ↓
自己封装的 CanDriver 类
        ↓
SocketCAN
        ↓
can0
        ↓
电机驱动器 / STM32 / 底盘控制板
```

### 与 ROS 2 的关系

学习 SocketCAN，并不代表不用 ROS 2。

在 ROS 2 项目中，可以将 CAN 通信封装成一个普通 C++ 类，然后在 ROS 2 节点或 `ros2_control hardware_interface` 中调用。

例如：

```text
ros2_control controller
        ↓
hardware_interface
        ↓
自己封装的 CanDriver
        ↓
SocketCAN
        ↓
can0
        ↓
电机驱动器
```

这样做的好处是，CAN 通信代码不会和 ROS 2 强绑定。以后即使写普通 C++ 项目、Qt 上位机或者 OpenCV 控制程序，也可以继续复用同一套 CAN driver。

### 什么时候使用 ros2_socketcan

`ros2_socketcan` 并不是不能用。

如果项目本身就是纯 ROS 2 架构，并且希望快速把 CAN 帧转换成 ROS 2 topic，那么 `ros2_socketcan` 是一个可以考虑的方案。

但是对于底层驱动学习和长期工程复用来说，仍然建议先掌握 SocketCAN。

可以这样理解：

```text
SocketCAN：底层能力
ros2_socketcan：ROS 2 封装
```

先学 SocketCAN，再看 `ros2_socketcan` 会更容易理解。

### 什么时候使用 ros2_canopen

如果你的电机驱动器或工业设备使用的是 CANopen 协议，那么就不能只把它当作普通 CAN 帧通信来看待。

CANopen 是建立在 CAN 之上的高层协议，涉及对象字典、PDO、SDO、NMT 等概念。

这种情况下，可以考虑学习：

```text
ros2_canopen
```

但如果你的设备只是自定义 CAN 协议，例如自己规定 CAN ID 和数据格式，那么优先学习 SocketCAN 就足够了。

### 本章学习目标

学习完本章后，应该能够掌握：

- CAN 通信的基本概念
- Linux 下 `can0` 设备的使用方式
- 使用 `ip link` 配置 CAN 波特率
- 使用 `candump`、`cansend` 调试 CAN 总线
- 理解 CAN ID、标准帧、扩展帧、数据区等概念
- 使用 C++ SocketCAN 进行 CAN 帧收发
- 将 CAN 通信封装成可复用的 C++ 类
- 为后续接入 ROS 2、ros2_control、电机驱动器控制打基础
