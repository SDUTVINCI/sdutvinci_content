---
title: "UDP 通信"
---

> UDP 是无连接的数据报协议。  
> 在机器人里，UDP 常用于：局域网低延迟状态广播、传感器数据广播、自定义轻量通信、上位机发现设备等。

UDP 和 TCP 最大区别：

```text
TCP 是字节流，没有消息边界。
UDP 是数据报，一次 send_to 对应对端一次 receive_from 的一个数据报。
```

但 UDP 不保证可靠、不保证顺序、不保证一定送达。

---

## 示例 1：普通 `main()` 里写同步 UDP Receiver

### 程序目标

写一个 UDP 接收端：

1. 绑定本地 9001 端口；
2. 阻塞等待一个 UDP 数据报；
3. 打印发送方地址和内容；
4. 程序结束。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <boost/system/error_code.hpp>
#include <array>
#include <iostream>
#include <string>

using boost::asio::ip::udp;

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io;
    boost::system::error_code ec;

    // UDP socket 用来发送或接收无连接的数据报。
    udp::socket socket(io, udp::endpoint(udp::v4(), 9001));

    std::array<char, 1024> data;
    udp::endpoint sender_endpoint;

    std::cout << "receiver：监听 UDP 0.0.0.0:9001，等待数据" << std::endl;

    // receive_from 会等待并接收一个 UDP 数据报。
    std::size_t n = socket.receive_from(boost::asio::buffer(data), sender_endpoint, 0, ec);
    if (ec)
    {
        std::cout << "receiver：接收失败：" << ec.message() << std::endl;
        return 1;
    }

    std::string msg(data.data(), n);

    std::cout << "receiver：收到来自 " << sender_endpoint.address().to_string()
              << ":" << sender_endpoint.port()
              << " 的 " << n << " 字节：" << msg << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

终端 1：运行接收端。

```bash
g++ demo1_udp_receiver.cpp -o demo1_udp_receiver -std=c++17 -lboost_system -pthread
./demo1_udp_receiver
```

终端 2：用 `nc` 发送 UDP 数据。

```bash
echo "hello udp" | nc -u 127.0.0.1 9001
```

### 运行输出与时间顺序

接收端启动后立刻输出：

```text
receiver：监听 UDP 0.0.0.0:9001，等待数据
```

此时程序阻塞在：

```cpp
socket.receive_from(...)
```

发送数据后，接收端输出类似：

```text
receiver：收到来自 127.0.0.1:xxxxx 的 10 字节：hello udp
```

其中 `xxxxx` 是发送端临时端口，每次可能不同。

### 本示例需要注意的点

UDP 接收端不需要 `accept()`，因为 UDP 没有连接。

只要绑定端口，就可以接收别人发来的数据报。

### 关键函数说明

#### `udp::socket socket(io, udp::endpoint(udp::v4(), 9001));`

作用：创建 UDP socket，并绑定本地 9001 端口。

#### `socket.receive_from(buffer, sender_endpoint, 0, ec);`

作用：同步接收一个 UDP 数据报。

返回值：接收到的字节数。

`sender_endpoint` 会被填充成发送方的 IP 和端口。

---

## 示例 2：普通 `main()` 里写同步 UDP Sender

### 程序目标

写一个 UDP 发送端，向 `127.0.0.1:9001` 发送一条消息。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <boost/system/error_code.hpp>
#include <iostream>
#include <string>

using boost::asio::ip::udp;

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io;
    boost::system::error_code ec;

    // UDP socket 用来发送或接收无连接的数据报。
    udp::socket socket(io);
    socket.open(udp::v4(), ec);
    if (ec)
    {
        std::cout << "sender：打开 socket 失败：" << ec.message() << std::endl;
        return 1;
    }

    udp::endpoint receiver_endpoint(boost::asio::ip::make_address("127.0.0.1"), 9001);

    std::string msg = "hello udp";

    std::cout << "sender：准备发送到 127.0.0.1:9001" << std::endl;

    // send_to 指定目标地址发送 UDP 数据报。
    std::size_t n = socket.send_to(boost::asio::buffer(msg), receiver_endpoint, 0, ec);
    if (ec)
    {
        std::cout << "sender：发送失败：" << ec.message() << std::endl;
        return 1;
    }

    std::cout << "sender：发送完成，字节数 = " << n << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

终端 1：先运行示例 1 接收端。

```bash
./demo1_udp_receiver
```

终端 2：运行发送端。

```bash
g++ demo2_udp_sender.cpp -o demo2_udp_sender -std=c++17 -lboost_system -pthread
./demo2_udp_sender
```

### 运行输出与时间顺序

发送端输出：

```text
sender：准备发送到 127.0.0.1:9001
sender：发送完成，字节数 = 9
```

接收端输出类似：

```text
receiver：监听 UDP 0.0.0.0:9001，等待数据
receiver：收到来自 127.0.0.1:xxxxx 的 9 字节：hello udp
```

### 本示例需要注意的点

UDP 发送不需要先连接服务器。

只要知道对方 IP 和端口，就可以：

```cpp
send_to(...)
```

但这不代表对方一定收到了。

---

## 示例 3：普通 `main()` 里写异步 UDP Receiver

### 程序目标

把示例 1 改成异步版本：

1. 注册 `async_receive_from()`；
2. `io.run()` 等待数据；
3. 收到一个 UDP 数据报后执行回调；
4. 程序结束。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <boost/system/error_code.hpp>
#include <array>
#include <functional>
#include <iostream>
#include <string>

using boost::asio::ip::udp;

void on_receive(const boost::system::error_code& ec,
                std::size_t bytes_transferred,
                std::array<char, 1024>* data,
                udp::endpoint* sender_endpoint)
{
    if (ec)
    {
        std::cout << "on_receive：接收失败：" << ec.message() << std::endl;
        return;
    }

    std::string msg(data->data(), bytes_transferred);

    std::cout << "on_receive：收到来自 " << sender_endpoint->address().to_string()
              << ":" << sender_endpoint->port()
              << " 的 " << bytes_transferred << " 字节：" << msg << std::endl;
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io;
    // UDP socket 用来发送或接收无连接的数据报。
    udp::socket socket(io, udp::endpoint(udp::v4(), 9001));

    std::array<char, 1024> data;
    udp::endpoint sender_endpoint;

    std::cout << "main：注册 async_receive_from" << std::endl;

    socket.async_receive_from(boost::asio::buffer(data),
                              sender_endpoint,
                              std::bind(on_receive,
                                        std::placeholders::_1,
                                        std::placeholders::_2,
                                        &data,
                                        &sender_endpoint));

    std::cout << "main：async_receive_from 已返回，准备 io.run()" << std::endl;

    // 启动事件循环，前面注册的异步任务会在这里被调度执行。
    io.run();

    std::cout << "main：io.run() 返回" << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

终端 1：运行异步接收端。

```bash
g++ demo3_async_udp_receiver.cpp -o demo3_async_udp_receiver -std=c++17 -lboost_system -pthread
./demo3_async_udp_receiver
```

终端 2：发送 UDP 数据。

```bash
echo "imu udp" | nc -u 127.0.0.1 9001
```

### 运行输出与时间顺序

接收端启动后立刻输出：

```text
main：注册 async_receive_from
main：async_receive_from 已返回，准备 io.run()
```

发送数据后输出：

```text
on_receive：收到来自 127.0.0.1:xxxxx 的 8 字节：imu udp
main：io.run() 返回
```

完整输出类似：

```text
main：注册 async_receive_from
main：async_receive_from 已返回，准备 io.run()
on_receive：收到来自 127.0.0.1:xxxxx 的 8 字节：imu udp
main：io.run() 返回
```

### 本示例需要注意的点

`data` 和 `sender_endpoint` 都是局部变量，但是本例安全，因为：

```text
main() 卡在 io.run()
回调执行完之前，data 和 sender_endpoint 不会析构
```

工程里更推荐写成类成员变量。

### `async_receive_from()` 说明

```cpp
socket.async_receive_from(buffer, sender_endpoint, handler);
```

作用：异步接收一个 UDP 数据报。

回调参数：

```cpp
const boost::system::error_code& ec
std::size_t bytes_transferred
```

---

## 示例 4：类里写异步 UDP Echo Server

### 程序目标

写一个异步 UDP Echo Server：

1. 持续接收 UDP 数据报；
2. 打印发送方和内容；
3. 原样发回发送方；
4. 继续接收下一条。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <boost/system/error_code.hpp>
#include <array>
#include <functional>
#include <iostream>
#include <string>

using boost::asio::ip::udp;

class UdpEchoServer
{
public:
    UdpEchoServer(boost::asio::io_context& io, unsigned short port)
        : socket_(io, udp::endpoint(udp::v4(), port))
    {
        std::cout << "UdpEchoServer：监听 UDP 端口 " << port << std::endl;
        start_receive();
    }

private:
    void start_receive()
    {
        socket_.async_receive_from(boost::asio::buffer(data_),
                                   remote_endpoint_,
                                   std::bind(&UdpEchoServer::on_receive,
                                             this,
                                             std::placeholders::_1,
                                             std::placeholders::_2));
    }

    void on_receive(const boost::system::error_code& ec, std::size_t bytes_transferred)
    {
        if (ec)
        {
            std::cout << "UdpEchoServer：接收失败：" << ec.message() << std::endl;
            start_receive();
            return;
        }

        std::string msg(data_.data(), bytes_transferred);

        std::cout << "收到 " << remote_endpoint_.address().to_string()
                  << ":" << remote_endpoint_.port()
                  << " 的数据：" << msg << std::endl;

        socket_.async_send_to(boost::asio::buffer(data_, bytes_transferred),
                              remote_endpoint_,
                              std::bind(&UdpEchoServer::on_send,
                                        this,
                                        std::placeholders::_1,
                                        std::placeholders::_2));
    }

    void on_send(const boost::system::error_code& ec, std::size_t bytes_transferred)
    {
        if (ec)
        {
            std::cout << "UdpEchoServer：发送失败：" << ec.message() << std::endl;
        }
        else
        {
            std::cout << "回显完成，字节数 = " << bytes_transferred << std::endl;
        }

        start_receive();
    }

private:
    // UDP socket 用来发送或接收无连接的数据报。
    udp::socket socket_;
    udp::endpoint remote_endpoint_;
    std::array<char, 1024> data_;
};

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io;
    UdpEchoServer server(io, 9001);

    std::cout << "main：调用 io.run()" << std::endl;

    // 启动事件循环，前面注册的异步任务会在这里被调度执行。
    io.run();

    std::cout << "main：io.run() 返回" << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

终端 1：运行 UDP Echo Server。

```bash
g++ demo4_udp_echo_server.cpp -o demo4_udp_echo_server -std=c++17 -lboost_system -pthread
./demo4_udp_echo_server
```

终端 2：用 `nc` 发送并接收回显。

```bash
nc -u 127.0.0.1 9001
```

然后输入：

```text
hello
robot
```

### 运行输出与时间顺序

服务端启动输出：

```text
UdpEchoServer：监听 UDP 端口 9001
main：调用 io.run()
```

客户端输入 `hello` 回车后，服务端输出类似：

```text
收到 127.0.0.1:xxxxx 的数据：hello
回显完成，字节数 = 6
```

客户端会看到回显：

```text
hello
```

继续输入 `robot` 后，服务端输出：

```text
收到 127.0.0.1:xxxxx 的数据：robot
回显完成，字节数 = 6
```

### 本示例需要注意的点

UDP Echo Server 中这两个成员变量很重要：

```cpp
udp::endpoint remote_endpoint_;
std::array<char, 1024> data_;
```

它们必须活到异步回调完成。

### `async_send_to()` 说明

```cpp
socket_.async_send_to(buffer, remote_endpoint_, handler);
```

作用：异步发送一个 UDP 数据报到指定 endpoint。

回调参数：

```cpp
const boost::system::error_code& ec
std::size_t bytes_transferred
```

---

## 示例 5：类里写周期性 UDP Sender

### 程序目标

机器人里经常需要周期性广播状态，例如：

```text
robot alive
robot alive
robot alive
```

本例使用 timer 每 1 秒通过 UDP 发一次消息，共发送 5 次。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <boost/system/error_code.hpp>
#include <chrono>
#include <functional>
#include <iostream>
#include <string>

using boost::asio::ip::udp;

class UdpHeartbeatSender
{
public:
    UdpHeartbeatSender(boost::asio::io_context& io,
                       const std::string& host,
                       unsigned short port)
        : socket_(io),
          endpoint_(boost::asio::ip::make_address(host), port),
          timer_(io),
          count_(0)
    {
        socket_.open(udp::v4());
    }

    void start()
    {
        schedule_send();
    }

private:
    void schedule_send()
    {
        timer_.expires_after(std::chrono::seconds(1));
        // 注册异步等待：这一行不会阻塞，回调会在定时器到期后执行。
        timer_.async_wait(std::bind(&UdpHeartbeatSender::on_timer,
                                    this,
                                    std::placeholders::_1));
    }

    void on_timer(const boost::system::error_code& ec)
    {
        if (ec)
        {
            std::cout << "timer 取消：" << ec.message() << std::endl;
            return;
        }

        ++count_;
        msg_ = "heartbeat " + std::to_string(count_);

        socket_.async_send_to(boost::asio::buffer(msg_),
                              endpoint_,
                              std::bind(&UdpHeartbeatSender::on_send,
                                        this,
                                        std::placeholders::_1,
                                        std::placeholders::_2));
    }

    void on_send(const boost::system::error_code& ec, std::size_t bytes_transferred)
    {
        if (ec)
        {
            std::cout << "发送失败：" << ec.message() << std::endl;
            return;
        }

        std::cout << "发送 " << msg_ << "，字节数 = " << bytes_transferred << std::endl;

        if (count_ < 5)
        {
            schedule_send();
        }
        else
        {
            std::cout << "发送 5 次完成" << std::endl;
        }
    }

private:
    udp::socket socket_;
    udp::endpoint endpoint_;
    // 创建定时器，并设置到期时间。
    boost::asio::steady_timer timer_;
    std::string msg_;
    int count_;
};

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io;
    UdpHeartbeatSender sender(io, "127.0.0.1", 9001);

    sender.start();

    std::cout << "main：调用 io.run()" << std::endl;

    io.run();

    std::cout << "main：io.run() 返回" << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

终端 1：监听 UDP。

```bash
nc -u -l 9001
```

终端 2：运行程序。

```bash
g++ demo5_udp_heartbeat.cpp -o demo5_udp_heartbeat -std=c++17 -lboost_system -pthread
./demo5_udp_heartbeat
```

### 运行输出与时间顺序

程序立刻输出：

```text
main：调用 io.run()
```

约 1 秒后输出：

```text
发送 heartbeat 1，字节数 = 11
```

之后每隔约 1 秒输出一次：

```text
发送 heartbeat 2，字节数 = 11
发送 heartbeat 3，字节数 = 11
发送 heartbeat 4，字节数 = 11
发送 heartbeat 5，字节数 = 11
发送 5 次完成
main：io.run() 返回
```

监听终端会依次收到：

```text
heartbeat 1heartbeat 2heartbeat 3heartbeat 4heartbeat 5
```

有些 `nc` 版本不会自动按行显示，因为我们没有在消息末尾加 `\n`。你可以把：

```cpp
msg_ = "heartbeat " + std::to_string(count_);
```

改成：

```cpp
msg_ = "heartbeat " + std::to_string(count_) + "\n";
```

### 本示例需要注意的点

这个例子把 timer 和 UDP 结合起来了：

```text
timer 到期 -> 发送 UDP -> 发送完成 -> 再注册下一次 timer
```

这就是很多机器人工程里“周期上报状态”的基本结构。

---

## 本节总结

1. UDP 不需要连接，也没有 `accept()`。
2. UDP 一次发送对应一个数据报，但不保证送达。
3. 接收端要保存发送方 `endpoint`，回包时用它。
4. 异步 UDP 的 buffer 和 endpoint 必须活到回调完成。
5. 周期 UDP 发送可以用 `steady_timer` 实现。
