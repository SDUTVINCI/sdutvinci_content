---
vinciId: fcea015f-b688-43d3-89e3-48f0c686a245
title: 一、Boost库串口通信：Boost介绍+基础实现
description: boost异步串口通信（C\+\+）
authors:
  - shangfanxing
contributors:
  - dongjiahui
publishedAt: 2024-07-18T00:00:00.000Z
updatedAt: 2026-08-20T09:47:47.820Z
---

## boost异步串口通信（C\+\+）

---



### 首先：

对于C\+\+上下位机的串口通信可以使用Qt，Qt在5\.1版本之后引入了一个串口通信类QSerialPort使用前只需加入QT \+= serialport。QSerialPort可以很方便的通过信号槽实现串口同步及异步通信，类似地，如果不在Qt库的环境下，可以使用“准”C\+\+标准库boost。



### boost库介绍

boost 库是一个优秀的，可移植的，开源的 C\+\+ 库，它是由 C\+\+ 标准委员会发起的，在 C\+\+ 社区中影响甚大，是一个不折不扣的准标准库，它的功能十分强大，弥补了 C\+\+ 很多功能函数处理上的不足。

#### 特点

**可移植性**：Windows，Linux，Unix 等

**开源免费**：使用 Boost License 来授权使用，商业和非商业都是可以使用的

**高效**：具有工业强度，设计结构良好，广泛使用



为什么要选择使用该库，详见[ROS2机器人操作系统教程](/wiki/2023-12-30-ros2-tutorial)的ROS2 serial章节。



#### 用途

**广泛应用于****网络编程、编程接口，**拥有字符串和文本处理库，容器库，迭代器库，算法库，函数对象和高阶编程库，泛型编程，模板元编程，预处理元编程，并发编程，数字和数学，排错和测试，数据结构，图像处理，输入输出，内存管理，跨语言混合编程，解析，编程接口等

#### 安装

windows：

[https://blog.csdn.net/m0_67357141/article/details/125318505?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522170891694416800215074742%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=170891694416800215074742&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-125318505-null-null.142^v99^pc_search_result_base5&utm_term=vs%E9%85%8D%E7%BD%AEboost%E5%BA%93&spm=1018.2226.3001.4187]()

Linux：

[https://blog.csdn.net/challenglistic/article/details/129097988?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522172131236916800226529442%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=172131236916800226529442&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-5-129097988-null-null.142^v100^pc_search_result_base3&utm_term=ubuntu%E5%AE%89%E8%A3%85boost%E5%BA%93&spm=1018.2226.3001.4187]()

ROS2 Serial Driver的优点：

**通用性：**这个Boost库在ROS2中被封装成了Serial\_Driver1\.2库，并且ROS2提供了通用的Serial\_Driver1\.2，不一定非要在ROS2中才可以使用，你可以在任何地方使用Serial\_Driver1\.2。

**易学性：**由于Serial\_Driver1\.2封装的更加简洁，所以更好学一点，也可以尝试使用Serial\_Driver1\.2库。详见[ROS2机器人操作系统教程](/wiki/2023-12-30-ros2-tutorial)的串口章节。

### boost::asio::serial\_port

boost的asio提供了boost::asio::serial\_port类————此类专门进行串口通信

**官方说明**：[https://www\.boost\.org/doc/libs/1\_75\_0/doc/html/boost\_asio/overview/serial\_ports\.html](https://www.boost.org/doc/libs/1_75_0/doc/html/boost_asio/overview/serial_ports.html)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/001-fe7491ff3454.webp)



### 示例源码

CMakeLists\.txt（如需要可用）

```CMake
# CMake 最低版本号要求
cmake_minimum_required(VERSION 3.5)

# 项目信息
project(serialporttest LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include_directories(/usr/local/lib/)

# 查找当前目录下的所有源文件
# 并将名称保存到 DIR_SRCS 变量
aux_source_directory(. DIR_SRCS)

# 指定生成目标
add_executable(serialporttest ${DIR_SRCS})

target_link_libraries(serialporttest -lpthread)
```

serialport\.h

```C++
#ifndef SERIALPORT_H
#define SERIALPORT_H

#include <boost/asio.hpp>
#include <boost/bind.hpp>
#include <boost/array.hpp>
#include <memory>
#include <thread>

using namespace std;
using namespace boost::asio;

class SerialPort
{
public:
    SerialPort();
    ~SerialPort();

    bool init(string port_name, uint16_t baud_rate);
    void runService();
    bool open();
    void close();
    void write(string &buf, boost::system::error_code &ec);

    static void startAsyncRead();
    static void handleRead(const boost::system::error_code &ec,size_t byte_read);
private:
    boost::system::error_code errorCode;
    io_service io;
    static shared_ptr<serial_port> serialPort;
    string portName;
    uint16_t baudRate;

    static char receiveData[1024];
};

#endif // SERIALPORT_H
```

serialport\.cpp

```C++
#include "serialport.h"
#include <iostream>

shared_ptr<serial_port> SerialPort::serialPort = nullptr;
char SerialPort::receiveData[] = {0};
SerialPort::SerialPort() :
    portName("COM1"),//如果是Windows系统，端口应该是COM1 ...(这个位置写入的是默认端口)
                     //如果是Linux框架的系统是/dev/ttyUSB0 ...
    baudRate(115200)
{
    memset(receiveData,0,sizeof (receiveData));
}

SerialPort::~SerialPort()
{
    if (serialPort != nullptr)
        serialPort->close();
    serialPort = nullptr;
}

bool SerialPort::init(string port_name, uint baud_rate)
{
    portName = port_name;
    baudRate = baud_rate;

    return open();
}

void SerialPort::runService()
{
    startAsyncRead();
    io.run();
}

bool SerialPort::open()
{
    try
    {
        if(serialPort == nullptr)
            serialPort = shared_ptr<serial_port>(new serial_port(io));

        serialPort->open(portName,errorCode);

        //设置串口参数
        serialPort->set_option(serial_port::baud_rate(baudRate));
        serialPort->set_option(serial_port::flow_control());
        serialPort->set_option(serial_port::parity());
        serialPort->set_option(serial_port::stop_bits());
        serialPort->set_option(serial_port::character_size(8));

        return true;

    }
    catch (exception& err)
    {
        cout << "Exception Error: " << err.what() << endl;
    }

    return false;
}

void SerialPort::startAsyncRead()
{
    memset(receiveData,0,sizeof (receiveData));
    serialPort->async_read_some(boost::asio::buffer(receiveData),
            boost::bind(handleRead,
            boost::asio::placeholders::error,
                        boost::asio::placeholders::bytes_transferred));
}

void SerialPort::close()
{
    serialPort->close();
}

void SerialPort::write(string &buf, boost::system::error_code &ec)
{
    serialPort->write_some(boost::asio::buffer(buf),ec);
    //boost::asio::write(*serialPort,boost::asio::buffer(&buf, 1));
    //如果你每次只需要发送一个字节信息你可以使用该函数，运行消耗会比write_some小
}

void SerialPort::handleRead(const boost::system::error_code &ec,size_t byte_read)
{
    cout.write(receiveData,byte_read);

    startAsyncRead();
}
```

用起来倒也简单————因为已经进行了二次封装

封装后只保留了这几个函数接口：

bool init\(string port\_name, uint16\_t baud\_rate\);
    void runService\(\);
    void close\(\);\-\-\-\-\-\-\-\-此函数非必要，析构中会自动调用
    void write\(string \&buf, boost::system::error\_code \&ec\);



### 使用方法

```C++
bool init(string port_name, uint16_t baud_rate); //初始化函数
--port_name:串口名称
            如果是Windows系统，端口应该是COM1 ...(这个位置写入的是默认端口)
            如果是Linux框架的系统是/dev/ttyUSB0 ...
--baud_rate:波特率，默认为115200
```

```C++
void runService(); //异步串口通信接收函数
调用此函数后，程序会自动挂起，等待接收下位机的信息
```

```C++
bool open();//打开通信串
```

```C++
void close();//关闭通信串口，并对串口有关的量释放
```

```C++
void write(string &buf, boost::system::error_code &ec);//异步串口楼通信发送函数
 --buf：需要发送的内容
 --ec：获取错误码，如果没有错误，信息正常发送该值为0
```



### 测试

#### 5\.1 测试工具

关于串口的测试就稍微有点麻烦，因为一般的机器现在很少有串口了，为了测试：

**步骤一：**

1. **硬件直接测试**：可以向嵌入式组借用或自行购买USB转TTL模块。USB端连接电脑，另一端通过排线连接单片机或树莓派，用于创建真实串口测试环境。Linux、Boost.Asio和上位机串口程序由软件算法组负责，模块电平、接线和MCU UART由嵌入式组负责。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/002-504e778dd999.webp)



2. **虚拟测试**:我们可以使用串口模拟辅助工具创建虚拟串口在上位机上进行测试

**Virtual Serial Port Driver（VSPD）：上位机虚拟串口**

下载方式：

[https://www.bilibili.com/video/BV1u54y1s7B3?vd_source=2a70e8cbccfb71570f2b97be1853e0df]()

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/003-c84d4d123761.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/004-60e927f7d929.webp)

---

两种方法二选一即可，建议采用第二种

**步骤二：**

下载串口调试助手**VOFA\+，**有其他串口助手的使用其他的也都一样

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/005-2a8711ee6163.webp)

用于发送串口信息或者接收验证串口通信数据

下载地址：

[https://www.vofa.plus/docs/learning/start/quick_start]()

#### **5\.2创建虚拟端口**

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/006-480679c3f742.webp)

打开VSPD

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/007-651d73b6b5bd.webp)

创建虚拟端口（使用完后直接充值端口就可以删除虚拟端口）

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/008-5b63d8dfb979.webp)





打开设备管理器可以看到两个虚拟端口被创建

COM1\-\>COM2

COM2\-\>COM1

#### 5\.3测试代码

##### **写出测试**

```C++
#include"serialport.h"
#include<iostream>

//发送字符A到串口
int main()
{
    SerialPort port;
    if (port.init("COM1", 115200))
    {
        std::string message = "A";
        boost::system::error_code ec;
        port.write(message, ec);

        if (!ec)
        {
            std::cout << "Write successful!" << std::endl;
        }
        else
        {
            std::cout << "Write error: " << ec.message() << std::endl;
        }
    }
    else
    {
        std::cout << "Serial port initialization failed." << std::endl;
    }

    return 0;
}
```

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/009-51919c97a4a4.webp)

设置好端口号，波特率，数据位数，左上角蓝色按钮“连接”



![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/010-72815463c283.webp)

运行程序，串口调试工具自动接收到数据（这里我设置的是16进制所以是41，可以调成文本模式，收到的就是A）

##### **读取测试**

```C++
#include"serialport.h"
#include<iostream>

//程序读取下位机发送的信息
int main()
{
    SerialPort port;
    if (port.init("COM1", 115200))
    {
        port.runService();
    }
    else
    {
        std::cout << "Serial port initialization failed." << std::endl;
    }

    return 0;
}
```

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/011-075bce6b3af2.webp)

使用串口工具向上位机发送一个A

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/012-3616734d9b6d.webp)

程序运行时会挂起，等下位机发送程序，此时程序接收到下位机发送的A，并将其打印输出到终端
