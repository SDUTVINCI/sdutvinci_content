---
title: "ROS2 Serial Driver库"
---


### ROS1串口库1.1
这是ROS1的库，但是也可以从第三方源码编译这个库，用于ROS2.

http://wjwwood.io/serial/doc/1.1.0/index.html

### ROS2串口库1.2
顾名思义，是一个串口包，

该库适用于ROS Humble和ROS Jazzy版本。

官方详解:

https://docs.ros.org/en/humble/p/serial_driver/generated/index.html#

https://index.ros.org/p/serial_driver/

下面是根据官方文档手搓的教程（截至教程出之前，还没有任何一个像样的教程）

#### SerialDriver库特点
该库主要基于Boost.Asio库，所以说原理就是Boost.Asio库的特点：

**事件驱动** Boost.Asio 使用操作系统提供的底层 I/O 复用机制（如 Linux 的 `epoll`、Windows 的 I/O Completion Ports）来监控文件描述符（如串口、套接字等）是否有可用的数据。当数据到达时，触发相应的事件。

**回调机制** 用户调用异步方法（如 `async_read` 或 `async_receive`）时，传入一个回调函数。当指定的事件（如数据接收完成）发生后，Boost.Asio 会自动调用用户提供的回调函数。

**I/O 服务对象** Boost.Asio 的核心是一个 `io_service` 或 `io_context` 对象，它管理所有的异步操作和回调的调度。异步接收时：

*   用户将操作（如 `async_receive`）注册到 `io_context`。

*   `io_context` 将异步操作挂起，并等待 I/O 事件的触发。

*   当 I/O 事件触发时，`io_context` 调用对应的回调函数。

**不阻塞主线程** 异步操作不会阻塞调用线程。主线程可以继续执行其他任务，而异步操作在后台完成。

#### 基于Boost.Asio库的优点
Boost.Asio库是靠系统IO进行事件触发的，并且单独开了一个线程去干这件事，所以这就使boost库拥有单片机的串口中断的特点，又拥有真多线程的特点。

而单片机的串口中断，虽然也能通过IO进行事件触发，但是由于单片机只有一个真线程，所以在进入中断时，一定会阻塞主线程，造成效率损失。

如果在主线程里直接接收并加上死循环和延时，这种效率往往是最低的，并且有很大的安全风险，非常不建议使用。

效率：Boost.Asio接收 >> 单片机串口中断 >> 多线程阻塞式接收 >> 单线程阻塞式接收（在传输速度非常快的情况下，全都是远远大于>>）

#### 安装ROS2串口包
**serial_driver** 是一个封装了串口通信功能的库，它分为两个部分：

*   **ROS2 依赖版本** ：用于 ROS2 环境中，依赖于 ROS2 的通信机制（如 `rclcpp`）。

*   **独立库版本** ：不依赖 ROS2，可以直接在普通的 C++ 项目中使用。(未经测试)

##### **方式一：在ROS2中安装**
```bash
sudo apt install ros-<ros2-distro>-asio-cmake-module
sudo apt install ros-<ros2-distro>-serial-driver

#humble
sudo apt install ros-humble-asio-cmake-module
sudo apt install ros-humble-serial-driver
#jazzy
sudo apt install ros-jazzy-asio-cmake-module
sudo apt install ros-jazzy-serial-driver
```

##### **方式二：通用（该方法未经测试，如果你测试了，请帮忙删掉本句话）**
如果你在ROS1或者说其他地方使用，而且没有安装ROS2，请选择该方法。

**克隆仓库**

首先，克隆 **transport_drivers** 仓库（包含 `serial_driver`）：

https://github.com/ros-drivers/transport_drivers

```bash
git clone https://github.com/ros-drivers/transport_drivers.git
```

**安装依赖**

`serial_driver` 依赖于 **ASIO** 库（一个跨平台的 C++ 网络编程库）。确保系统中已安装 ASIO：

```bash
sudo apt install libasio-dev
```

**编译独立库**

进入 `serial_driver` 目录并编译：

```bash
cd transport_drivers/serial_driver
mkdir build
cd build
cmake ..
make
sudo make install
```

安装完毕后，在CMake里正常添加该包就可以。

##### 其他准备工作
**串口调试助手安装(Linux版) (如果需要Windows版，问控制组要去)**

```bash
sudo apt-get install cutecom
```

**权限**

```bash

# 将用户权限提高
sudo usermod -aG dialout $USER
newgrp dialout
```

有关Linux的USB设备的更多操作请看[Vinci机器人队Linux入门教程](/wiki/2024-03-30-linux-jiao-cheng/0900-qi-ta-cao-zuo#usb端口设置)

#### 初始化详解
1.  创建功能包

```bash
ros2 pkg create cpp01_serial --build-type ament_cmake --dependencies rclcpp serial_driver --node-name demo01_uart_send
```

2.  引用头文件：`#include "serial_driver/serial_driver.hpp"`

3.  首先要初始化串口：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1772.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1773.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1774.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1775.webp)

串口初始化函数`init_port()`有俩参数，

第一个参数device_name 是端口名称

第二个参数config 是串口参数设置

device_name是个字符串，是填`/dev`下的设备名

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1776.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1777.webp)

```bash
ls /dev | grep USB
```

有关Linux的USB设备的更多操作请看[Vinci机器人队Linux入门教程](/wiki/2024-03-30-linux-jiao-cheng/0900-qi-ta-cao-zuo#usb端口设置)

config是个类对象，所以要创建一下这个类对象如下：

这玩意是个轻量化的类，没必要放在堆区，直接在栈区里创建即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1778.webp)

SerialPortConfig(uint32_t baud_rate, **drivers::serial_driver::FlowControl flow_control** , drivers::serial_driver::Parity parity, drivers::serial_driver::StopBits stop_bits)

从这个重载函数可以看到(参数不懂的问控制组)

第一个参数baud_rate 波特率

第二个参数flow_control 流控制

第三个参数parity 奇偶效验

第四个参数stop_bits 停止位

具体的参数在`serial_port.hpp`中有

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1779.webp)

这样串口就初始化成功了。

#### 发送代码详解
1.  发送主逻辑

我们接下来写发送主逻辑，首先要创建一个延时，不要使用while(true)这种死循环延迟，ROS2提供了定时器。

先创建定时器（创建定时器不用详解了，不懂的回顾上面赵老师的视频）

就需要注意一个点，我们用的是 **毫秒milliseconds** ，不是 **微秒microseconds** 。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1780.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1781.webp)

发送核心函数是这个send函数，可以看到，入口参数是vector类型的向量，

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1782.webp)

```cpp
#include "rclcpp/rclcpp.hpp"
#include "serial_driver/serial_driver.hpp"

class Serial_Node : public rclcpp::Node
{
public:
  Serial_Node() : Node("serial_node_cpp")
  {
    // 等设备准备好再初始化
    // std::this_thread::sleep_for(std::chrono::milliseconds(500));

    // 串口设备名（根据实际设备调整）
    const std::string device_name = "/dev/ttyUSB0";

    RCLCPP_INFO(this->get_logger(), "Serial port Node Open!");
    // 创建串口配置对象
    // 波特率115200；不开启流控制；无奇偶效验；停止位1。
    drivers::serial_driver::SerialPortConfig config(
        9600,
        drivers::serial_driver::FlowControl::NONE,
        drivers::serial_driver::Parity::NONE,
        drivers::serial_driver::StopBits::ONE);

    // 初始化串口
    try
    {
      io_context_ = std::make_shared<drivers::common::IoContext>(1);
      // 初始化 serial_driver_
      serial_driver_ = std::make_shared<drivers::serial_driver::SerialDriver>(*io_context_);
      serial_driver_->init_port(device_name, config);
      serial_driver_->port()->open();

      RCLCPP_INFO(this->get_logger(), "Serial port initialized successfully");
      RCLCPP_INFO(this->get_logger(), "Using device: %s", serial_driver_->port().get()->device_name().c_str());
      RCLCPP_INFO(this->get_logger(), "Baud_rate: %d", config.get_baud_rate());
    }
    catch (const std::exception &ex)
    {
      RCLCPP_ERROR(this->get_logger(), "Failed to initialize serial port: %s", ex.what());
      return;
    }

    // 设置发送定时器
    send_timer_ = this->create_wall_timer(
        std::chrono::milliseconds(500),
        std::bind(&Serial_Node::send_message_timer_callback, this));
  }

private:
  void send_message_timer_callback()
  {
    // 发送一串字符串
    const std::string message = "Hello from ROS 2!\n";
    std::vector<uint8_t> data(message.begin(), message.end());
    // std::vector<uint8_t> hex_data = {0x48, 0x65, 0x6C, 0x6C, 0x6F}; // "Hello" in ASCII
    auto port = serial_driver_->port();

    try
    {
      size_t bytes_sent_size = port->send(data);
      RCLCPP_INFO(this->get_logger(), "Sent: %s (%ld bytes)", message.c_str(), bytes_sent_size);
    }
    catch(const std::exception &ex)
    {
      RCLCPP_ERROR(this->get_logger(), "Error Transmiting from serial port:%s",ex.what());
    }
  }

  std::shared_ptr<drivers::serial_driver::SerialDriver> serial_driver_;
  std::shared_ptr<drivers::common::IoContext> io_context_;
  rclcpp::TimerBase::SharedPtr send_timer_;
};

int main(int argc, char **argv)
{
  rclcpp::init(argc, argv);

  auto node = std::make_shared<Serial_Node>();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1783.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1784.webp)

#### 接收代码详解
##### 定时器方式(低效，不建议用)
```cpp
#include "rclcpp/rclcpp.hpp"
#include "serial_driver/serial_driver.hpp"
#include <string>

class Serial_Node : public rclcpp::Node
{
public:
  Serial_Node() : Node("serial_node_cpp")
  {
    // 等设备准备好再初始化
    // std::this_thread::sleep_for(std::chrono::milliseconds(500));

    // 串口设备名（根据实际设备调整）
    const std::string device_name = "/dev/ttyUSB0";

    RCLCPP_INFO(this->get_logger(), "Serial port Node Open!");
    // 创建串口配置对象
    // 波特率115200；不开启流控制；无奇偶效验；停止位1。
    drivers::serial_driver::SerialPortConfig config(
        9600,
        drivers::serial_driver::FlowControl::NONE,
        drivers::serial_driver::Parity::NONE,
        drivers::serial_driver::StopBits::ONE);

    // 初始化串口
    try
    {
      io_context_ = std::make_shared<drivers::common::IoContext>(1);
      // 初始化 serial_driver_
      serial_driver_ = std::make_shared<drivers::serial_driver::SerialDriver>(*io_context_);
      serial_driver_->init_port(device_name, config);
      serial_driver_->port()->open();

      RCLCPP_INFO(this->get_logger(), "Serial port initialized successfully");
      RCLCPP_INFO(this->get_logger(), "Using device: %s", serial_driver_->port().get()->device_name().c_str());
      RCLCPP_INFO(this->get_logger(), "Baud_rate: %d", config.get_baud_rate());
    }
    catch (const std::exception &ex)
    {
      RCLCPP_ERROR(this->get_logger(), "Failed to initialize serial port: %s", ex.what());
      return;
    }

    // 设置发送定时器
    receive_timer_ = this->create_wall_timer(
        std::chrono::milliseconds(3),
        std::bind(&Serial_Node::receive_message_timer_callback, this));
  }

private:
  void receive_message_timer_callback()
  {
    std::vector<uint8_t> data(100); // "Hello" in ASCII
    auto port = serial_driver_->port();

    try
    {
      size_t bytes_receive_size = port->receive(data);
      if(bytes_receive_size > 0)
      {
        std::string message(data.begin(),data.begin() + bytes_receive_size);
        RCLCPP_INFO(this->get_logger(), "Receive: %s (%ld bytes)", message.c_str(), bytes_receive_size);
      }
    }
    catch(const std::exception &ex)
    {
      RCLCPP_ERROR(this->get_logger(), "Error Receiving from serial port:%s",ex.what());
    }
  }

  std::shared_ptr<drivers::serial_driver::SerialDriver> serial_driver_;
  std::shared_ptr<drivers::common::IoContext> io_context_;
  rclcpp::TimerBase::SharedPtr receive_timer_;
};

int main(int argc, char **argv)
{
  rclcpp::init(argc, argv);

  auto node = std::make_shared<Serial_Node>();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1785.webp)

##### 异步方式(高效)
```cpp
#include "rclcpp/rclcpp.hpp"
#include "serial_driver/serial_driver.hpp"
#include <string>

class Serial_Node : public rclcpp::Node
{
public:
  Serial_Node() : Node("serial_node_cpp")
  {
    // 等设备准备好再初始化
    // std::this_thread::sleep_for(std::chrono::milliseconds(500));

    // 串口设备名（根据实际设备调整）
    const std::string device_name = "/dev/ttyUSB0";

    RCLCPP_INFO(this->get_logger(), "Serial port Node Open!");
    // 创建串口配置对象
    // 波特率115200；不开启流控制；无奇偶效验；停止位1。
    drivers::serial_driver::SerialPortConfig config(
        9600,
        drivers::serial_driver::FlowControl::NONE,
        drivers::serial_driver::Parity::NONE,
        drivers::serial_driver::StopBits::ONE);

    // 初始化串口
    try
    {
      io_context_ = std::make_shared<drivers::common::IoContext>(1);
      // 初始化 serial_driver_
      serial_driver_ = std::make_shared<drivers::serial_driver::SerialDriver>(*io_context_);
      serial_driver_->init_port(device_name, config);
      serial_driver_->port()->open();

      RCLCPP_INFO(this->get_logger(), "Serial port initialized successfully");
      RCLCPP_INFO(this->get_logger(), "Using device: %s", serial_driver_->port().get()->device_name().c_str());
      RCLCPP_INFO(this->get_logger(), "Baud_rate: %d", config.get_baud_rate());
    }
    catch (const std::exception &ex)
    {
      RCLCPP_ERROR(this->get_logger(), "Failed to initialize serial port: %s", ex.what());
      return;
    }

    async_receive_message();
  }

private:
  void async_receive_message()  //创建一个函数更方便重新调用
  {
    auto port = serial_driver_->port();

    // 设置接收回调函数
    port->async_receive([this](const std::vector<uint8_t> &data,const size_t &size) 
    {
        if (size > 0)
        {
            // 处理接收到的数据
            std::string received_message(data.begin(), data.begin() + size);
            RCLCPP_INFO(this->get_logger(), "Received: %s (%ld bytes)", received_message.c_str(),size);
        }
        // 继续监听新的数据
        async_receive_message();
    }
    );
  }

  std::shared_ptr<drivers::serial_driver::SerialDriver> serial_driver_;
  std::shared_ptr<drivers::common::IoContext> io_context_;
  std::vector<uint8_t> receive_data_buffer = std::vector<uint8_t>(1024); // 接收缓冲区
};

int main(int argc, char **argv)
{
  rclcpp::init(argc, argv);

  auto node = std::make_shared<Serial_Node>();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1786.webp)

#### 总代码(又发又收)
```cpp
#include "rclcpp/rclcpp.hpp"
#include "serial_driver/serial_driver.hpp"
#include <string>

class Serial_Node : public rclcpp::Node
{
public:
  Serial_Node() : Node("serial_node_cpp")
  {
    // 等设备准备好再初始化
    // std::this_thread::sleep_for(std::chrono::milliseconds(500));

    // 串口设备名（根据实际设备调整）
    const std::string device_name = "/dev/ttyUSB0";

    RCLCPP_INFO(this->get_logger(), "Serial port Node Open!");
    // 创建串口配置对象
    // 波特率115200；不开启流控制；无奇偶效验；停止位1。
    drivers::serial_driver::SerialPortConfig config(
        9600,
        drivers::serial_driver::FlowControl::NONE,
        drivers::serial_driver::Parity::NONE,
        drivers::serial_driver::StopBits::ONE);

    // 初始化串口
    try
    {
      io_context_ = std::make_shared<drivers::common::IoContext>(1);
      // 初始化 serial_driver_
      serial_driver_ = std::make_shared<drivers::serial_driver::SerialDriver>(*io_context_);
      serial_driver_->init_port(device_name, config);
      serial_driver_->port()->open();

      RCLCPP_INFO(this->get_logger(), "Serial port initialized successfully");
      RCLCPP_INFO(this->get_logger(), "Using device: %s", serial_driver_->port().get()->device_name().c_str());
      RCLCPP_INFO(this->get_logger(), "Baud_rate: %d", config.get_baud_rate());
    }
    catch (const std::exception &ex)
    {
      RCLCPP_ERROR(this->get_logger(), "Failed to initialize serial port: %s", ex.what());
      return;
    }

        // 设置发送定时器
    transmit_timer_ = this->create_wall_timer(
        std::chrono::milliseconds(5000),
        std::bind(&Serial_Node::send_message_timer_callback, this));

    async_receive_message();   //进入异步接收
  }

private:

  void send_message_timer_callback()
  {
    // 发送一串字符串
    const std::string transmitted_message = "Hello from ROS 2!\n";
    transmit_data_buffer = std::vector<uint8_t>(transmitted_message.begin(), transmitted_message.end());
    // std::vector<uint8_t> hex_data = {0x48, 0x65, 0x6C, 0x6C, 0x6F}; // "Hello" in ASCII
    auto port = serial_driver_->port();

    try
    {
      size_t bytes_transmit_size = port->send(transmit_data_buffer);
      RCLCPP_INFO(this->get_logger(), "Transmitted: %s (%ld bytes)", transmitted_message.c_str(), bytes_transmit_size);
    }
    catch(const std::exception &ex)
    {
      RCLCPP_ERROR(this->get_logger(), "Error Transmiting from serial port:%s",ex.what());
    }
  }

  void async_receive_message()  //创建一个函数更方便重新调用
  {
    auto port = serial_driver_->port();

    // 设置接收回调函数
    port->async_receive([this](const std::vector<uint8_t> &data,const size_t &size) 
    {
        if (size > 0)
        {
            // 处理接收到的数据
            std::string received_message(data.begin(), data.begin() + size);
            RCLCPP_INFO(this->get_logger(), "Received: %s (%ld bytes)", received_message.c_str(),size);
        }
        // 继续监听新的数据
        async_receive_message();
    }
    );
  }

  std::shared_ptr<drivers::serial_driver::SerialDriver> serial_driver_;
  std::shared_ptr<drivers::common::IoContext> io_context_;
  rclcpp::TimerBase::SharedPtr transmit_timer_;
  std::vector<uint8_t> transmit_data_buffer = std::vector<uint8_t>(1024); // 发送缓冲区
  std::vector<uint8_t> receive_data_buffer = std::vector<uint8_t>(1024);  // 接收缓冲区
};

int main(int argc, char **argv)
{
  rclcpp::init(argc, argv);

  auto node = std::make_shared<Serial_Node>();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1787.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1788.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1789.webp)

#### 注意事项
1.  禁止引用`boost/asio.hpp`：由于该库的原理就是boost库，而且是ROS2自带的boost库，所以你用通用版的`boost/asio.hpp`，会导致报错出现很多标志重复的问题。
