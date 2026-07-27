---
title: "Boost.Asio 基础"
---

> 本节不急着讲串口、TCP、UDP，而是把 `io_context`、`run()`、`post()`、`work_guard`、`buffer`、`std::bind`、对象生命周期这些基础讲清楚。  
> 这些东西如果没搞懂，后面串口和网络程序会出现“为什么回调不执行”“为什么程序直接退出”“为什么段错误”等问题。

---

## 示例 1：`io_context` 没有任务时，`run()` 立刻返回

### 程序目标

验证一个非常重要的现象：如果 `io_context` 里没有任何未完成任务，`io.run()` 会立刻返回。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <iostream>

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io;

    std::cout << "main：准备调用 io.run()" << std::endl;

    // 启动事件循环，前面注册的异步任务会在这里被调度执行。
    std::size_t count = io.run();

    std::cout << "main：io.run() 返回，执行了 " << count << " 个回调" << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

```bash
g++ demo1_empty_run.cpp -o demo1_empty_run -std=c++17 -lboost_system -pthread
./demo1_empty_run
```

### 运行输出与时间顺序

这个程序不会等待，立刻输出：

```text
main：准备调用 io.run()
main：io.run() 返回，执行了 0 个回调
```

### 本示例需要注意的点

`io.run()` 不是永远阻塞。它阻塞的前提是：

```text
io_context 里还有未完成的异步任务，或者还有 work_guard 保持它不退出。
```

如果什么任务都没有，它会马上返回。

### `io.run()` 返回值

`io.run()` 返回值表示执行了多少个 handler。

本例中没有任何任务，所以返回值是：

```text
0
```

---

## 示例 2：普通 `main()` 里用 `post()` 投递任务

### 程序目标

`boost::asio::post()` 可以把一个普通函数投递到 `io_context` 里，让它以后由 `io.run()` 执行。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <functional>
#include <iostream>
#include <string>

void print_msg(const std::string& msg)
{
    std::cout << "执行任务：" << msg << std::endl;
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io;

    std::cout << "main：投递任务 A" << std::endl;
    // post 只是把任务放进队列，真正执行要等 io.run()。
    boost::asio::post(io, std::bind(print_msg, std::string("A")));

    std::cout << "main：投递任务 B" << std::endl;
    boost::asio::post(io, std::bind(print_msg, std::string("B")));

    std::cout << "main：准备调用 io.run()" << std::endl;

    // 启动事件循环，前面注册的异步任务会在这里被调度执行。
    std::size_t count = io.run();

    std::cout << "main：io.run() 返回，执行了 " << count << " 个任务" << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

```bash
g++ demo2_post.cpp -o demo2_post -std=c++17 -lboost_system -pthread
./demo2_post
```

### 运行输出与时间顺序

程序立刻输出：

```text
main：投递任务 A
main：投递任务 B
main：准备调用 io.run()
执行任务：A
执行任务：B
main：io.run() 返回，执行了 2 个任务
```

### 本示例需要注意的点

`post()` 不是立刻执行函数，而是把函数放进 `io_context` 的任务队列。

真正执行发生在：

```cpp
io.run();
```

### `post()` 函数说明

```cpp
boost::asio::post(io, handler);
```

参数：

1. `io`：任务投递到哪个 `io_context`；
2. `handler`：将来要执行的函数对象。

返回值：通常不用关心。

### `std::bind` 说明

```cpp
std::bind(print_msg, std::string("A"))
```

表示生成一个“无参数函数对象”。以后执行它时，相当于执行：

```cpp
print_msg("A");
```

---

## 示例 3：`work_guard` 让 `io.run()` 不会因为没任务而立刻退出

### 程序目标

很多机器人程序里，Asio 通信线程需要长期运行。  
如果暂时没有任务，`io.run()` 可能直接返回，通信线程就结束了。

`executor_work_guard` 可以告诉 `io_context`：

```text
先别退出，我后面可能还会投递任务。
```

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <boost/system/error_code.hpp>
#include <chrono>
#include <functional>
#include <iostream>
#include <memory>

using WorkGuard = boost::asio::executor_work_guard<boost::asio::io_context::executor_type>;

void release_guard(const boost::system::error_code& ec,
                   std::shared_ptr<WorkGuard> guard)
{
    if (ec)
    {
        std::cout << "release_guard 定时器取消：" << ec.message() << std::endl;
        return;
    }

    std::cout << "2 秒到了：释放 work_guard" << std::endl;
    guard->reset();
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io;

    std::shared_ptr<WorkGuard> guard =
        std::make_shared<WorkGuard>(boost::asio::make_work_guard(io));

    // 创建定时器，并设置到期时间。
    boost::asio::steady_timer timer(io, std::chrono::seconds(2));

    // 注册异步等待：这一行不会阻塞，回调会在定时器到期后执行。
    timer.async_wait(std::bind(release_guard,
                               std::placeholders::_1,
                               guard));

    std::cout << "main：有 work_guard，io.run() 不会空转退出" << std::endl;

    std::size_t count = io.run();

    std::cout << "main：io.run() 返回，执行了 " << count << " 个回调" << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

```bash
g++ demo3_work_guard.cpp -o demo3_work_guard -std=c++17 -lboost_system -pthread
./demo3_work_guard
```

### 运行输出与时间顺序

程序立刻输出：

```text
main：有 work_guard，io.run() 不会空转退出
```

约 2 秒后输出：

```text
2 秒到了：释放 work_guard
main：io.run() 返回，执行了 1 个回调
```

完整输出类似：

```text
main：有 work_guard，io.run() 不会空转退出
2 秒到了：释放 work_guard
main：io.run() 返回，执行了 1 个回调
```

### 本示例需要注意的点

如果只有 `work_guard`，但没有 timer 释放它，那么 `io.run()` 会一直不返回。

机器人通信线程里经常会这么做：

```text
创建 io_context
创建 work_guard
开一个线程 run()
程序退出时 reset guard + stop io_context
```

### `make_work_guard()` 说明

```cpp
boost::asio::make_work_guard(io)
```

作用：创建一个 guard，让 `io_context` 认为“还有工作没完成”。

`guard.reset()` 后，如果此时没有其他任务，`io.run()` 就可以返回。

---

## 示例 4：`std::bind` 的普通函数参数绑定

### 程序目标

专门练习 `std::bind`，看清楚 `_1`、固定参数、参数顺序。

### 完整代码

```cpp
#include <functional>
#include <iostream>
#include <string>

void print_robot_state(const std::string& name, double x, double y)
{
    std::cout << "机器人：" << name << "，x = " << x << "，y = " << y << std::endl;
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // std::function 可以保存普通函数、lambda 或函数对象。
    std::function<void(double, double)> f =
        // bind 会把函数和部分参数提前绑定成一个可调用对象。
        std::bind(print_robot_state,
                  std::string("mycar"),
                  std::placeholders::_1,
                  std::placeholders::_2);

    std::cout << "main：准备调用绑定后的函数" << std::endl;

    f(1.2, 3.4);
    f(5.6, 7.8);

    std::cout << "main：结束" << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

```bash
g++ demo4_bind_basic.cpp -o demo4_bind_basic -std=c++17
./demo4_bind_basic
```

### 运行输出

```text
main：准备调用绑定后的函数
机器人：mycar，x = 1.2，y = 3.4
机器人：mycar，x = 5.6，y = 7.8
main：结束
```

### 本示例需要注意的点

这句：

```cpp
std::bind(print_robot_state,
          std::string("mycar"),
          std::placeholders::_1,
          std::placeholders::_2)
```

表示：

```text
第 1 个参数固定成 "mycar"
未来传入的第 1 个参数放到 x
未来传入的第 2 个参数放到 y
```

所以：

```cpp
f(1.2, 3.4);
```

等价于：

```cpp
print_robot_state("mycar", 1.2, 3.4);
```

---

## 示例 5：类成员函数使用 `std::bind`

### 程序目标

看清楚成员函数为什么要写：

```cpp
&ClassName::function_name
this
```

### 完整代码

```cpp
#include <functional>
#include <iostream>
#include <string>

class Robot
{
public:
    explicit Robot(const std::string& name)
        : name_(name)
    {
    }

    void print_pose(double x, double y)
    {
        std::cout << "机器人：" << name_ << "，x = " << x << "，y = " << y << std::endl;
    }

private:
    std::string name_;
};

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    Robot robot("mycar");

    // std::function 可以保存普通函数、lambda 或函数对象。
    std::function<void(double, double)> f =
        // bind 会把函数和部分参数提前绑定成一个可调用对象。
        std::bind(&Robot::print_pose,
                  &robot,
                  std::placeholders::_1,
                  std::placeholders::_2);

    std::cout << "main：准备调用成员函数绑定对象" << std::endl;

    f(1.0, 2.0);
    f(3.0, 4.0);

    std::cout << "main：结束" << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

```bash
g++ demo5_bind_member.cpp -o demo5_bind_member -std=c++17
./demo5_bind_member
```

### 运行输出

```text
main：准备调用成员函数绑定对象
机器人：mycar，x = 1，y = 2
机器人：mycar，x = 3，y = 4
main：结束
```

### 本示例需要注意的点

普通函数绑定：

```cpp
std::bind(print_robot_state, ...)
```

成员函数绑定：

```cpp
std::bind(&Robot::print_pose, &robot, ...)
```

原因：成员函数必须依赖某个对象才能调用。

### `&Robot::print_pose` 是什么

它是成员函数指针，表示“Robot 类里的 print_pose 函数”。

但它还没有指定具体对象。

### `&robot` 是什么

它表示具体调用哪个对象的成员函数。

所以完整含义是：

```text
未来调用 robot.print_pose(x, y)。
```

---

## 示例 6：`buffer` 的基本使用

### 程序目标

Boost.Asio 读写数据时经常写：

```cpp
boost::asio::buffer(data)
```

本例先看它的基本含义。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <array>
#include <iostream>
#include <string>

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    std::string msg = "hello asio";
    auto buf1 = boost::asio::buffer(msg);

    // std::array 是固定长度数组，长度在编译期就确定。
    std::array<char, 128> data;
    auto buf2 = boost::asio::buffer(data);

    std::cout << "msg.size() = " << msg.size() << std::endl;
    std::cout << "buffer(msg).size() = " << buf1.size() << std::endl;
    std::cout << "array size = " << data.size() << std::endl;
    std::cout << "buffer(data).size() = " << buf2.size() << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

```bash
g++ demo6_buffer.cpp -o demo6_buffer -std=c++17 -lboost_system -pthread
./demo6_buffer
```

### 运行输出

```text
msg.size() = 10
buffer(msg).size() = 10
array size = 128
buffer(data).size() = 128
```

### 本示例需要注意的点

`boost::asio::buffer()` 通常不会复制数据，它只是生成一个“指向已有内存的 buffer 视图”。

所以异步读写时特别注意：

```text
buffer 指向的原始数据必须活到异步回调完成。
```

错误写法示意：

```cpp
void send()
{
    std::string msg = "hello";
    boost::asio::async_write(socket_, boost::asio::buffer(msg), handler);
}
```

这个 `msg` 在 `send()` 返回后就销毁了，但异步写可能还没完成。

正确思路：

```text
把 msg 做成类成员变量
或者用 shared_ptr 管理
或者使用写队列保存待发送数据
```

---

## 示例 7：类里封装 `io_context` 工作线程

### 程序目标

工程里经常希望 Asio 在单独线程运行。  
本例写一个最小的 `IoThread` 类：

1. 构造时创建 `work_guard`；
2. `start()` 开线程执行 `io.run()`；
3. `stop()` 停止线程；
4. 用 `post()` 投递任务验证效果。

### 完整代码

```cpp
#include <boost/asio.hpp>
#include <functional>
#include <iostream>
#include <memory>
#include <string>
#include <thread>

class IoThread
{
public:
    IoThread()
        : guard_(boost::asio::make_work_guard(io_))
    {
    }

    ~IoThread()
    {
        stop();
    }

    void start()
    {
        thread_ = std::thread(std::bind(&IoThread::run, this));
    }

    void stop()
    {
        if (!stopped_)
        {
            stopped_ = true;
            guard_.reset();
            io_.stop();

            if (thread_.joinable())
            {
                thread_.join();
            }
        }
    }

    boost::asio::io_context& io()
    {
        return io_;
    }

private:
    void run()
    {
        std::cout << "IoThread：io.run() 开始" << std::endl;
        // 启动事件循环，前面注册的异步任务会在这里被调度执行。
        io_.run();
        std::cout << "IoThread：io.run() 返回" << std::endl;
    }

private:
    // io_context 是 Asio 的事件循环对象，异步任务需要靠它调度。
    boost::asio::io_context io_;
    boost::asio::executor_work_guard<boost::asio::io_context::executor_type> guard_;
    std::thread thread_;
    bool stopped_ = false;
};

void print_task(const std::string& msg)
{
    std::cout << "执行任务：" << msg << std::endl;
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    IoThread io_thread;
    io_thread.start();

    // post 只是把任务放进队列，真正执行要等 io.run()。
    boost::asio::post(io_thread.io(), std::bind(print_task, std::string("A")));
    boost::asio::post(io_thread.io(), std::bind(print_task, std::string("B")));

    std::this_thread::sleep_for(std::chrono::seconds(1));

    std::cout << "main：准备停止 IoThread" << std::endl;
    io_thread.stop();

    std::cout << "main：结束" << std::endl;

    return 0;
}
```

**运行结果**：见下方“运行输出与时间顺序”；如果示例涉及定时器、线程、网络或外部设备，具体时间和顺序可能会随环境略有变化。

### 编译运行

```bash
g++ demo7_iothread.cpp -o demo7_iothread -std=c++17 -lboost_system -pthread
./demo7_iothread
```

### 运行输出与时间顺序

程序启动后，工作线程开始运行：

```text
IoThread：io.run() 开始
执行任务：A
执行任务：B
```

约 1 秒后主线程停止它：

```text
main：准备停止 IoThread
IoThread：io.run() 返回
main：结束
```

完整输出类似：

```text
IoThread：io.run() 开始
执行任务：A
执行任务：B
main：准备停止 IoThread
IoThread：io.run() 返回
main：结束
```

### 本示例需要注意的点

这个类就是以后封装串口、TCP、UDP 通信线程的雏形。

但注意：

```cpp
io_.stop();
```

会让 `io_context` 尽快停止，未完成的异步任务可能不会正常完成。

工程里更细致的做法是：

```text
先 cancel socket / timer / serial_port
再 reset work_guard
最后等待 run() 返回
```

---

## 本节总结

1. 没有任务时，`io.run()` 立刻返回。
2. `post()` 可以把普通函数投递给 `io_context` 执行。
3. `work_guard` 可以防止 `io.run()` 因为没任务而退出。
4. `std::bind` 可以绑定普通函数，也可以绑定成员函数。
5. `boost::asio::buffer()` 通常只是内存视图，不负责延长数据生命周期。
6. 异步工程里最重要的是对象生命周期和 buffer 生命周期。
