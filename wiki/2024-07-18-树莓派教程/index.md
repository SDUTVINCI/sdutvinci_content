---
title: 树莓派教程
authors:
- shangfanxing
contributors:
- fangzihao
- dongjiahui
publishedAt: '2024-07-18T00:00:00.000Z'
updatedAt: '2025-05-03T13:47:35.000Z'
---

# 一、烧录树莓派官方系统到sd卡

（准备好：树莓派、sd卡、读卡器）\-\-\-\-\-\-\-本队目前有树莓派4b一枚

1. 把准备好的sd卡（注意sd卡要格式化过，大小最好大于16G）使用读卡器插入PC机

2. 根据下列视频和文档Raspberry Pi系统的烧录

[https://www.bilibili.com/video/BV1rL4y1w71w?vd_source=2a70e8cbccfb71570f2b97be1853e0df]()

[https://blog.csdn.net/weixin_53050357/article/details/125023590?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522170494441616800215097466%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=170494441616800215097466&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-6-125023590-null-null.142^v99^pc_search_result_base6&utm_term=%E6%A0%91%E8%8E%93%E6%B4%BE%E7%83%A7%E5%BD%95%E7%B3%BB%E7%BB%9F%E5%88%B0sd%E5%8D%A1&spm=1018.2226.3001.4187]()

[https://mirrors.bfsu.edu.cn/ubuntu-cdimage/ubuntu/releases/24.04.1/release/]()

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/001-11cf133ad68e.webp)

树莓派最建议的发行版还是Ubuntu\-Server,没有图形界面，所以占资源贼小，非常适合树莓派这种小内存设备。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/002-ee7c8a157072.webp)

如上图，开机只占300MB内存。





# 二、树莓派换源

[https://blog.csdn.net/KnightJoker0/article/details/130530041]()

# **三、树莓派串口通信**

## 树莓派常用接口

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/003-90b9128cebbc.webp)

## 树莓派引脚图\(3B\)

![1280X1280\.PNG](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/004-f14c279056cb.webp)

【注意】
树莓派包含两个串口（树莓派3及以前版本）

1. **硬件串口（/dev/ttyAMA0）**,硬件串口由硬件实现，有单独的波特率时钟源，性能高，可靠。一般优先选择这个使用。

2. **mini串口（/dev/ttyS0）**，mini串口时钟源是由CPU内核时钟提供，波特率受到内核时钟的影响，不稳定。



## 树莓派设备连接

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/005-3d78cb6ded1d.webp)

树莓派一般自带网卡，可连接有线宽带或连接无线局域网







## 树莓派配置基础环境

### 更换wifi连接

如果无图形界面，而且想连wifi,需要把TF卡插到电脑上访问system\-boot

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/006-576be2358fbf.webp)

找到network\-config

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/007-f3c671c58ca1.webp)

将wifi名和密码填上即可，拔出TF卡，然后往树莓派上插，重启树莓派就可以发现树莓派连上啦。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/008-7959888c31dd.webp)





### 配置SSH

树莓派烧录软件现已支持选择是否开启ssh的选项。

可以直接通过该软件进行配置。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/009-f6c8e266dd1a.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/010-1d933f45759f.webp)

### 配置VNC









---



## 串口通信环境配置（设备第一次使用需设置）

如果设备之前使用过串口通信，这部分直接略过，直接看第5部分

### **开启ttyS0设备**

【**注意】不要再普通用户组上进行操作，一定要在root用户组上配置，否则将会出错。**

- 打开树莓派终端输入：

```Plain Text
ls -la /dev/
```

如果树莓派再此之前并没有做任何的串口配置，默认红色框内的串口`ttyAMA0`，这是给蓝牙模块使用的。我们还要启用串口`ttyS0`。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/011-e820967a02e6.webp)

- 下面打开串口`/dev/ttyS0`，在命令框输入：

```Bash
sudo raspi-confi
```

- 然后选择 `Interfacing Options`，回车

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/012-74a1f9bd7921.webp)

- 选择`Serial` ，然后回车

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/013-be5506af90e0.webp)

- 选择 `Yes`

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/014-0367be6c0f1b.webp)

- 选择 `Yes`

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/015-085637b426ec.webp)

- 然后退出即可，此时提示树莓派需要`重启`。

- 再输入命令

```Bash
ls -la /dev/
```

此时我们能看到，除了之前的 `ttyAMA0`以外，`ttyS0`也显示在设备当中，说明前面的配置没问题。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/016-d78450efe840.webp)

### 修改映射关系

- 终端输入

```Bash
sudo vim /boot/config.txt
```

- 在该文件最后一行添加：

```Bash
dtoverlay=pi3-miniuart-bt
```

- `重启`树莓派，然后重新在输入`ls -la /dev/`，查看两者是否对调成功。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/017-d972563c92f5.webp)

### 关闭Console

在终端中输入：

```Plain Text
sudo systemctl stop serial-getty@ttyS0.service
sudo systemctl disable serial-getty@ttyS0.service
```

### 配置串口参数：配置`cmdline.txt`

终端输入：

```Bash
sudo vim /boot/cmdline.txt
```

如果打开后，存在 `console=serial1,115200`和`kgdboc=serial1,115200`，则删除。如果没有，这步骤忽略。

```Plain Text
console=tty1 root=PARTUUID=ea7d04d6-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait quiet splash plymouth.ignore-serial-consoles
```

到此，第一次使用配置完成。

---



## 串口通信测试（Python 基于Serial库）



### 环境配置



- 安装python、python3、python\-serial

```Plain Text
sudo apt install python
sudo apt install python3
sudo apt install python-serial
```

- 启动python IDE

输入 python 打开 python 命令行模式，创建serial实例ser，端口为`/dev/ttyAMA0`，波特率设置为`115200`。

```Python
>>> import serial
>>> ser = serial.Serial('/dev/ttyAMA0',115200)
```

检验串口是否打开，若未打开则输入 ser\.open\(\)打开

```Python
>>> ser.isOpen()
True
```

- 打开PC串口调试助手\-\-\-\-\-\-\-\-\-\-\-\-\-这里是使用usb\-ttl利用PC串口助手进行的测试

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/018-0bb6a0caca31.webp)

```Python
>>> ser.write(b'Raspberry pi')
12
```

树莓派往 `PC` 端发送了 `Raspberry` 字符，也就是上图蓝框部分，说明树莓派前面的一系列配置可以实现串口透传通讯了。



### python代码

python脚本代码，实现串口的发送和接收：

```Python
import serial
import time
ser = serial.Serial('/dev/ttyAMA0', 115200)
if ser.isOpen == False:
    ser.open()          # 打开串口
ser.write(b"hello stm32!!\n")
try:
    while True:
        size = ser.inWaiting()          # 获得缓冲区字符
        if size != 0:
            res = ser.read(size)           # 读取内容并显示
            print(res)
            ser.flushInput()                # 情况接收缓存区
            time.sleep(0.5)                        # 软件延时
except KeyboardInterrupt:
    ser.close()

```

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/019-9e8c3bdd1dd0.webp)



## 串口通信测试（C 基于wiringPi库）

### 配置环境

```Bash
#更新软件包列表
sudo apt-get update
#安装wiringPi
sudo apt-get install wiringpi
#验证安装
gpio -v
```

查看 `wiringPi` 是否安装成功：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/020-4b06d1cbecc9.webp)

### C代码

创建C文件写一个串口实现例程：

```C
#include<stdio.h>          // 包含标准输入输出头文件
#include<wiringPi.h>       // 包含wiringPi库头文件，用于树莓派GPIO操作
#include<wiringSerial.h>   // 包含wiringPi串口库头文件，用于串口通信
#include<unistd.h>         // 包含unistd.h头文件，用于sleep等函数

int main()                 // 主函数
{
    int fd;                // 文件描述符，用于串口设备
    int res;               // 存储从串口读取的数据
    int i;                 // 循环计数器

    i = 0;                 // 初始化计数器

    if(wiringPiSetup() < 0)  // 初始化wiringPi，如果返回值小于0，则初始化失败
    {
        return 1;          // 初始化失败，返回1退出程序
    }

    fd = serialOpen("/dev/ttyAMA0", 115200);  // 打开串口设备，波特率设置为115200

    if(fd < 0)             // 判断串口是否打开成功，如果返回值小于0，则打开失败
    {
        return 1;          // 打开串口失败，返回1退出程序
    }

    printf("serail test start ...\n");  // 输出提示信息，串口测试开始
    serialPrintf(fd, "hello stm32!!\n"); // 通过串口发送字符串"hello stm32!!\n"

    while(1)               // 无限循环
    {
        if(serialDataAvail(fd) != 0)  // 检查串口是否有数据可读，如果不为0，则有数据
        {
            res = serialGetchar(fd);  // 从串口读取一个字符
            putchar(res);             // 输出读取到的字符

            fflush(stdout);           // 刷新标准输出缓冲区
        }
    }

    serialClose(fd);       // 关闭串口

    return 0;              // 程序正常退出，返回0
}

```

【注意】该代码可以使用gcc编译，如果你是使用上述安装方式安装的wiringPi库，那么意味着你的环境变量已经自动配置完成，那么编译指令为：

`gcc ``/源文件名/``.c -o ``/``工程名/ -lwiringPi`

编译完成后运行指令为：

`./工程名/`



下面是 PC 端串口调试助手显示：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/07/18/021-94fe24d839d1.webp)



## 串口通信库学习及使用（C\+\+）

【注意】上述串口通信的Python、C的实现是基于`pySerial库`、`wiringPi库`完成的。

1. 其中WiringPi库本身是一个专为树莓派设计的用于C/C\+\+的GPIO库，其中包含了用于串口通信的`wiringSerial`库。这个库仅可以用于树莓派的Raspberry Pi系统，该库提供了一组简单的函数，用于打开、关闭、读取和写入串口数据。

2. 而pySerial库则是一个用于Python通用串口通信库，以在各种操作系统上使用，包括Windows、Linux、和MacOS。树莓派作为运行Linux的设备，自然也可以使用pySerial进行串口通信。



下面将详细讲述基于`WiringPi库`的串口通信，以及基于C\+\+通用串口通信库`Boost库`的串口通信



### 基于WiringPi库的串口通信

#### WiringPi库串口通信常用函数

- 这是树莓派WiringPi库官方文档⬇

[https://www.rubydoc.info/gems/wiringpi2/2.0.1/WiringPi]()

|常见函数定义|参数解释|功能|
|---|---|---|
|int wiringPiSetup\(\) |无传入参数；返回：如果成功初始化 wiringPi 库和 GPIO 系统，函数将返回 0，表示初始化成功。；如果初始化失败，函数将返回 \-1，表示初始化失败或出现错误。|用于初始化 wiringPi 库和 GPIO 系统，准备后续对 GPIO 引脚进行控制。；它会设置合适的权限、初始化必要的数据结构，并确保 GPIO 控制能够正常运行。；初始化成功后，允许使用其他 wiringPi 函数来操控树莓派的 GPIO 引脚。|
|int serialOpen \(char \*device, int baud\)|device:串口的地址，在Linux中就是设备所在的目录，默认一般是"/dev/ttyAMA0"。；baud：波特率；返回：正常返回文件描述符，否则返回\-1失       败。|打开并初始串口|
|void serialClose \(int fd\)|fd:串口的文件描述符|关闭串口|
|void  serialPutchar \(int fd, unsigned char c\)|fd:文件描述符；c:要发送的数据|发送一个字节的数据到串口|
|void  serialPuts \(int fd, char \*s\)|fd：文件描述符；s：发送的字符串，字符串要以'\\0'结尾|发送一个字符串到串口|
|int   serialDataAvail \(int fd\)|fd：文件描述符；返回：串口缓存中已经接收的，可读取的字节数，\-1代表错误|获取串口缓存中可用的字节数。|
|int serialGetchar \(int fd\)|fd：文件描述符；返回：读取到的字符，失败时返回\-1|从串口读取一个字节数据返回。；如果串口缓存中没有可用的数据，则会等待10秒，如果10后还有没，返回\-1；所以，在读取前，做好通过serialDataAvail判断下。|
|void serialFlush \(int fd\)|fd：文件描述符|刷新，清空串口缓冲中的所有可用的数据。|
|\*size\_t write \(int fd,const void \* buf,size\_t count\)|fd：文件描述符；buf：需要发送的数据缓存数组；count:发送buf中的前count个字节数据；返回：实际写入的字符数，错误返回\-1|这个是Linux下的标准IO库函数，需要包含头文件`#include <unistd.h>`；当要发送到的数据量过大时，wiringPi建议使用这个函数。|
|\*size\_t read\(int fd,void \* buf ,size\_t count\);|fd：文件描述符；buf：接受的数据缓存的数组；count:接收的字节数\.；返回：实际读取的字符数，失败时返回\-1|这个是Linux下的标准IO库函数，需要包含头文件`#include <unistd.h>`；当要接收的数据量过大时，wiringPi建议使用这个函数。|

#### WiringPi库串口通信实现

1. **案例1：**

```C
#include <iostream>
#include <wiringPi.h>
#include <wiringSerial.h> // 包含串口函数 ------ wiringpi的串口通信的实现在该部分

int main() {
    const char* device = "/dev/ttyAMA0"; // Raspberry Pi上的串口设备
    int baud = 9600; // 波特率

    // 初始化 wiringPi
    if (wiringPiSetup() == -1) {
        std::cerr << "初始化 wiringPi 失败。\n";
        return 1;
    }

    // 打开串口连接
    int fd = serialOpen(device, baud);
    if (fd == -1) {
        std::cerr << "打开串口设备失败。\n";
        return 1;
    }

    // 向串口写入数据
    serialPuts(fd, "Hello, world!\n");

    // 从串口读取数据并打印
    while (true) {
        while (serialDataAvail(fd) > 0) {
            char c = serialGetchar(fd);
            std::cout << c;
        }
        delay(100); // 等待一段时间再次检查，避免过于频繁的检查
    }

    // 关闭串口连接
    serialClose(fd);

    return 0;
}

```

该实现使用WiringPi库进行了基础的串口通信的发送和接收，具体流程如下⬇

---

2. **案例2：**

事实上，案例1算不上一个完整的串口通信实现，它发送过一个字符串之后，便在死循环中已知等待接收信息，无法做到在兼顾”程序运行的任意时间内去向另一个设备发送信息“和”在其他设备向本机的发送信息的时候立刻接收“，一般情况下如果要实现该基础功能，对于PC来说要使用到多线程的相关实现，以下就是基于多线程利用WiringPi库去实现上述基础功能的实现：

```C
#include <iostream>     // 标准输入输出流
#include <string>       // 字符串库
#include <thread>       // 线程库
#include <cstring>      // 字符串操作库
#include <wiringSerial.h>   // wiringPi 库提供的串口库
#include <wiringPi.h>   // wiringPi 库

int fd; // 全局变量，用于存储串口文件描述符

// 串口发送线程函数
void write_thread()
{
    std::string send_buf = "hello world"; // 要发送的字符串
    char cmd; // 用户输入的命令字符
    while (true)
    {
        std::cout << "pthread1:按任意键发送数据" << std::endl; // 提示用户输入
        std::cin >> cmd; // 等待用户输入任意字符
        write(fd, send_buf.c_str(), send_buf.length()); // 向串口写入数据
    }
}

// 串口接收线程函数
void read_thread()
{
    char msg[128] = {'\0'}; // 接收缓冲区
    std::cout << "pthread2:listening..." << std::endl; // 提示正在监听
    while (true)
    {
        while (serialDataAvail(fd) != -1) // 检查串口是否有数据可读
        {
            int nread = read(fd, msg, 128); // 从串口读取数据
            if (nread > 0)
            {
                std::cout << "get data: " << nread << " Byte context: " << msg << std::endl; // 打印接收到的数据
            }
            memset(msg, 0, sizeof(msg)); // 清空消息缓冲区
        }
    }
}

int main()
{
    if (-1 == wiringPiSetup()) // 初始化 wiringPi 库
    {
        std::cerr << "setup error" << std::endl; // 输出设置错误信息到标准错误流
        return -1; // 返回错误码 -1
    }

    fd = serialOpen("/dev/ttyAMA4", 9600); // 打开串口设备 ttyAMA4，波特率 9600

    std::thread writeThread(write_thread); // 创建串口发送线程
    std::thread readThread(read_thread);   // 创建串口接收线程

    writeThread.join(); // 等待发送线程结束
    readThread.join();  // 等待接收线程结束

    return 0; // 正常结束程序
}

```

- `writeThread` 线程调用 `write_thread()` 函数，不断等待用户输入命令后，将固定的字符串 "hello world" 发送到串口中。

- `readThread` 线程调用 `read_thread()` 函数，不断检查串口是否有数据可读，如果有则读取数据并输出到控制台。

### Boost\.Asio库的串口通信

Boost库是一个C\+\+的网络通信库，其中Asio部分是该库用于串口通信的一小部分，同时Boost库也是C\+\+的标准库，因此，使用该库完成的代码实现可以迁移到任何平台直接使用，本人使用该库初步封装了一个异步串口通信实现，并封装了一部分基于串口通信的功能：

具体实现及代码讲解见该链接⬇：

[Boost库串口通信](/wiki/2024-07-18-shang-wei-ji-chuan-kou-tong-xin-boost-ku)
