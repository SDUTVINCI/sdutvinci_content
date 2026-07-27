---
title: "TCP 通信"
---

> TCP 是面向连接、可靠、有序的字节流协议。  
> 在机器人里，TCP 常用于：上位机调试工具、远程控制、日志上传、参数服务、局域网设备通信等。

注意：TCP 是“字节流”，不是“消息包”。所以以后必须面对：

```text
粘包、半包、协议分帧
```

本节依旧先写普通 `main()`，再写类封装版本。异步回调统一用 `std::bind`。

---

## 示例 1：普通 `main()` 里写同步 TCP Echo Server

### 程序目标

写一个最小 TCP 服务端：

1. 监听 `0.0.0.0:9000`；
2. 等待一个客户端连接；
3. 读取客户端发来的一段数据；
4. 原样发回去；
5. 程序结束。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <boost/system/error_code.hpp>
#include <array>
#include <iostream>
#include <string>

using boost::asio::ip::tcp;

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io;
    boost::system::error_code ec;

    // acceptor 负责监听 TCP 端口并接收客户端连接。
    tcp::acceptor acceptor(io, tcp::endpoint(tcp::v4(), 9000));

    std::cout << "server：监听 0.0.0.0:9000，等待客户端连接" << std::endl;

    tcp::socket socket(io);
    // 同步 accept 会阻塞，直到有客户端连接进来。
    acceptor.accept(socket, ec);
    if (ec)
    {
        std::cout << "server：accept 失败：" << ec.message() << std::endl;
        return 1;
    }

    std::cout << "server：客户端已连接" << std::endl;

    std::array<char, 1024> data;
    std::size_t n = socket.read_some(boost::asio::buffer(data), ec);
    if (ec)
    {
        std::cout << "server：读取失败：" << ec.message() << std::endl;
        return 1;
    }

    std::string msg(data.data(), n);
    std::cout << "server：收到 " << n << " 字节：" << msg;

    boost::asio::write(socket, boost::asio::buffer(data, n), ec);
    if (ec)
    {
        std::cout << "server：回写失败：" << ec.message() << std::endl;
        return 1;
    }

    std::cout << "server：已原样回写，程序结束" << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

终端 1：运行服务端。

```bash
g++ demo1_tcp_sync_server.cpp -o demo1_tcp_sync_server -std=c++17 -lboost_system -pthread
./demo1_tcp_sync_server
```

终端 2：用 `nc` 连接服务端。

```bash
echo "hello tcp" | nc 127.0.0.1 9000
```

### 运行输出与时间顺序

服务端启动后立刻输出：

```text
server：监听 0.0.0.0:9000，等待客户端连接
```

此时服务端阻塞在：

```cpp
acceptor.accept(socket, ec);
```

客户端连接并发送数据后，服务端输出：

```text
server：客户端已连接
server：收到 10 字节：hello tcp
server：已原样回写，程序结束
```

客户端终端会收到回显：

```text
hello tcp
```

### 本示例需要注意的点

同步 TCP 服务端会阻塞：

1. `accept()` 阻塞等待连接；
2. `read_some()` 阻塞等待数据；
3. `write()` 阻塞等待写完成。

这适合理解流程，但不适合复杂机器人程序主线程。

### 关键函数说明

#### `tcp::acceptor acceptor(io, tcp::endpoint(tcp::v4(), 9000));`

作用：创建 TCP 监听器，监听本机所有 IPv4 地址的 9000 端口。

#### `acceptor.accept(socket, ec);`

作用：同步等待客户端连接。

返回值：`void`。

连接成功后，`socket` 表示和这个客户端的连接。

#### `socket.read_some(buffer, ec);`

作用：同步读取当前能读到的一些字节。

返回值：读取到的字节数。

注意：它不保证一次读到完整一条消息。

---

## 示例 2：普通 `main()` 里写同步 TCP Client

### 程序目标

写一个 TCP 客户端，连接 `127.0.0.1:9000`，发送一行数据，再读取服务端回显。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <boost/system/error_code.hpp>
#include <array>
#include <iostream>
#include <string>

using boost::asio::ip::tcp;

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io;
    boost::system::error_code ec;

    tcp::socket socket(io);
    tcp::endpoint server_endpoint(boost::asio::ip::make_address("127.0.0.1"), 9000);

    std::cout << "client：准备连接 127.0.0.1:9000" << std::endl;

    // 发起连接，成功后 socket 才能收发数据。
    socket.connect(server_endpoint, ec);
    if (ec)
    {
        std::cout << "client：连接失败：" << ec.message() << std::endl;
        return 1;
    }

    std::cout << "client：连接成功" << std::endl;

    std::string msg = "hello tcp\n";
    boost::asio::write(socket, boost::asio::buffer(msg), ec);
    if (ec)
    {
        std::cout << "client：发送失败：" << ec.message() << std::endl;
        return 1;
    }

    std::cout << "client：发送完成，等待回显" << std::endl;

    std::array<char, 1024> data;
    // 读取收到的数据，返回值表示本次实际读到的字节数。
    std::size_t n = socket.read_some(boost::asio::buffer(data), ec);
    if (ec)
    {
        std::cout << "client：读取失败：" << ec.message() << std::endl;
        return 1;
    }

    std::string reply(data.data(), n);
    std::cout << "client：收到回显：" << reply;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

终端 1：先运行示例 1 的服务端。

```bash
./demo1_tcp_sync_server
```

终端 2：运行客户端。

```bash
g++ demo2_tcp_sync_client.cpp -o demo2_tcp_sync_client -std=c++17 -lboost_system -pthread
./demo2_tcp_sync_client
```

### 运行输出与时间顺序

客户端输出：

```text
client：准备连接 127.0.0.1:9000
client：连接成功
client：发送完成，等待回显
client：收到回显：hello tcp
```

服务端输出：

```text
server：监听 0.0.0.0:9000，等待客户端连接
server：客户端已连接
server：收到 10 字节：hello tcp
server：已原样回写，程序结束
```

### 本示例需要注意的点

客户端必须在服务端已经监听后再运行，否则会连接失败：

```text
Connection refused
```

### `socket.connect()` 说明

```cpp
socket.connect(server_endpoint, ec);
```

作用：同步连接服务器。

连接成功后，后续可以通过同一个 `socket` 进行读写。

---

## 示例 3：类里写异步 TCP Echo Session

### 程序目标

写一个异步 TCP Echo Server：

1. 异步接受客户端连接；
2. 每个连接创建一个 `EchoSession`；
3. 异步读取客户端数据；
4. 异步写回客户端；
5. 继续读取下一段数据。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <boost/system/error_code.hpp>
#include <array>
#include <functional>
#include <iostream>
#include <memory>

using boost::asio::ip::tcp;

class EchoSession : public std::enable_shared_from_this<EchoSession>
{
public:
    explicit EchoSession(boost::asio::io_context& io)
        : socket_(io)
    {
    }

    tcp::socket& socket()
    {
        return socket_;
    }

    void start()
    {
        std::cout << "session：开始读取客户端数据" << std::endl;
        do_read();
    }

private:
    void do_read()
    {
        socket_.async_read_some(boost::asio::buffer(data_),
                                std::bind(&EchoSession::on_read,
                                          shared_from_this(),
                                          std::placeholders::_1,
                                          std::placeholders::_2));
    }

    void on_read(const boost::system::error_code& ec, std::size_t length)
    {
        if (ec)
        {
            std::cout << "session：读取结束或失败：" << ec.message() << std::endl;
            return;
        }

        std::cout << "session：收到 " << length << " 字节，准备回写" << std::endl;

        boost::asio::async_write(socket_,
                                 boost::asio::buffer(data_, length),
                                 std::bind(&EchoSession::on_write,
                                           shared_from_this(),
                                           std::placeholders::_1,
                                           std::placeholders::_2));
    }

    void on_write(const boost::system::error_code& ec, std::size_t length)
    {
        if (ec)
        {
            std::cout << "session：写失败：" << ec.message() << std::endl;
            return;
        }

        std::cout << "session：回写完成，字节数 = " << length << std::endl;

        do_read();
    }

private:
    tcp::socket socket_;
    std::array<char, 1024> data_;
};

class EchoServer
{
public:
    EchoServer(boost::asio::io_context& io, unsigned short port)
        : io_(io),
          acceptor_(io, tcp::endpoint(tcp::v4(), port))
    {
        std::cout << "server：监听端口 " << port << std::endl;
        do_accept();
    }

private:
    void do_accept()
    {
        std::shared_ptr<EchoSession> session = std::make_shared<EchoSession>(io_);

        acceptor_.async_accept(session->socket(),
                               std::bind(&EchoServer::on_accept,
                                         this,
                                         session,
                                         std::placeholders::_1));
    }

    void on_accept(std::shared_ptr<EchoSession> session,
                   const boost::system::error_code& ec)
    {
        if (ec)
        {
            std::cout << "server：accept 失败：" << ec.message() << std::endl;
        }
        else
        {
            std::cout << "server：有新客户端连接" << std::endl;
            session->start();
        }

        do_accept();
    }

private:
    boost::asio::io_context& io_;
    // acceptor 负责监听 TCP 端口并接收客户端连接。
    tcp::acceptor acceptor_;
};

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io;
    EchoServer server(io, 9000);

    std::cout << "main：调用 io.run()，服务端开始运行" << std::endl;

    // 启动事件循环，前面注册的异步任务会在这里被调度执行。
    io.run();

    std::cout << "main：io.run() 返回" << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

终端 1：运行异步服务端。

```bash
g++ demo3_async_tcp_echo_server.cpp -o demo3_async_tcp_echo_server -std=c++17 -lboost_system -pthread
./demo3_async_tcp_echo_server
```

终端 2：连接测试。

```bash
nc 127.0.0.1 9000
```

然后输入：

```text
hello
robot
```

### 运行输出与时间顺序

服务端启动后输出：

```text
server：监听端口 9000
main：调用 io.run()，服务端开始运行
```

客户端连接后：

```text
server：有新客户端连接
session：开始读取客户端数据
```

客户端输入 `hello` 回车后，服务端输出：

```text
session：收到 6 字节，准备回写
session：回写完成，字节数 = 6
```

客户端会看到回显：

```text
hello
```

继续输入 `robot` 回车后，服务端输出：

```text
session：收到 6 字节，准备回写
session：回写完成，字节数 = 6
```

### 本示例需要注意的点

这个程序不会自动退出，因为服务端持续调用：

```cpp
do_accept();
do_read();
```

所以 `io.run()` 会一直运行。

### `shared_from_this()` 说明

异步 session 最容易出问题的是对象生命周期。

如果回调还没执行，`EchoSession` 对象已经被释放，就会崩溃。

所以这里用：

```cpp
std::enable_shared_from_this<EchoSession>
shared_from_this()
```

让异步回调持有 `EchoSession` 的 `shared_ptr`，保证回调执行时对象还活着。

### `async_read_some()` 说明

```cpp
socket_.async_read_some(buffer, handler);
```

作用：异步读取当前到达的一些字节。

回调参数：

```cpp
const boost::system::error_code& ec
std::size_t length
```

注意：它不保证读到完整一条消息。

---

## 示例 4：类里写异步 TCP Client

### 程序目标

写一个异步 TCP 客户端：

1. 异步连接服务器；
2. 连接成功后发送一行数据；
3. 读取服务端回显；
4. 程序结束。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <boost/system/error_code.hpp>
#include <array>
#include <functional>
#include <iostream>
#include <memory>
#include <string>

using boost::asio::ip::tcp;

class TcpClient
{
public:
    TcpClient(boost::asio::io_context& io,
              const std::string& host,
              unsigned short port)
        : socket_(io),
          endpoint_(boost::asio::ip::make_address(host), port),
          msg_("hello async tcp\n")
    {
    }

    void start()
    {
        std::cout << "client：开始异步连接" << std::endl;

        socket_.async_connect(endpoint_,
                              std::bind(&TcpClient::on_connect,
                                        this,
                                        std::placeholders::_1));
    }

private:
    void on_connect(const boost::system::error_code& ec)
    {
        if (ec)
        {
            std::cout << "client：连接失败：" << ec.message() << std::endl;
            return;
        }

        std::cout << "client：连接成功，开始异步发送" << std::endl;

        boost::asio::async_write(socket_,
                                 boost::asio::buffer(msg_),
                                 std::bind(&TcpClient::on_write,
                                           this,
                                           std::placeholders::_1,
                                           std::placeholders::_2));
    }

    void on_write(const boost::system::error_code& ec, std::size_t length)
    {
        if (ec)
        {
            std::cout << "client：发送失败：" << ec.message() << std::endl;
            return;
        }

        std::cout << "client：发送完成，字节数 = " << length << "，等待回显" << std::endl;

        // 读取收到的数据，返回值表示本次实际读到的字节数。
        socket_.async_read_some(boost::asio::buffer(data_),
                                std::bind(&TcpClient::on_read,
                                          this,
                                          std::placeholders::_1,
                                          std::placeholders::_2));
    }

    void on_read(const boost::system::error_code& ec, std::size_t length)
    {
        if (ec)
        {
            std::cout << "client：读取失败：" << ec.message() << std::endl;
            return;
        }

        std::string reply(data_.data(), length);
        std::cout << "client：收到回显：" << reply;
    }

private:
    tcp::socket socket_;
    tcp::endpoint endpoint_;
    std::string msg_;
    std::array<char, 1024> data_;
};

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io;

    TcpClient client(io, "127.0.0.1", 9000);
    client.start();

    std::cout << "main：调用 io.run()" << std::endl;

    // 启动事件循环，前面注册的异步任务会在这里被调度执行。
    io.run();

    std::cout << "main：io.run() 返回" << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

终端 1：先运行示例 3 的异步服务端。

```bash
./demo3_async_tcp_echo_server
```

终端 2：运行客户端。

```bash
g++ demo4_async_tcp_client.cpp -o demo4_async_tcp_client -std=c++17 -lboost_system -pthread
./demo4_async_tcp_client
```

### 运行输出与时间顺序

客户端输出：

```text
client：开始异步连接
main：调用 io.run()
client：连接成功，开始异步发送
client：发送完成，字节数 = 16，等待回显
client：收到回显：hello async tcp
main：io.run() 返回
```

服务端输出类似：

```text
server：有新客户端连接
session：开始读取客户端数据
session：收到 16 字节，准备回写
session：回写完成，字节数 = 16
session：读取结束或失败：End of file
```

### 本示例需要注意的点

`msg_` 和 `data_` 都是成员变量，是为了保证异步写和异步读期间内存一直有效。

不要在 `on_connect()` 里写这种危险代码：

```cpp
std::string msg = "hello\n";
boost::asio::async_write(socket_, boost::asio::buffer(msg), handler);
```

因为 `on_connect()` 返回后，`msg` 就销毁了。

---

## TCP 粘包和半包提醒

TCP 是字节流。你发送三次：

```text
A
B
C
```

接收端可能一次读到：

```text
ABC
```

也可能分两次读到：

```text
A
BC
```

甚至一条消息被拆开：

```text
AB
C
```

所以真实项目必须设计协议，例如：

1. 换行符分隔：`cmd_vel 0.1 0.0\n`；
2. 固定长度包；
3. 包头 + 长度 + payload + CRC；
4. protobuf / flatbuffers 等序列化协议。

---

## 本节总结

1. 同步 TCP 适合理解流程，但会阻塞。
2. 异步 TCP 需要 `io.run()` 驱动。
3. 服务端通常是 `async_accept()` + `Session` 类。
4. 每个 TCP 连接对应一个 socket。
5. `shared_from_this()` 常用于保证 session 生命周期。
6. TCP 没有消息边界，真实项目必须设计分帧协议。
