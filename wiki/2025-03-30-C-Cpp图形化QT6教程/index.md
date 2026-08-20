---
vinciId: f3a1a309-0e06-42b2-b54e-038cfadcca07
title: C/C++图形化之QT6教程
description: 简介
authors:
  - dongjiahui
publishedAt: 2025-03-30T00:00:00.000Z
updatedAt: 2025-03-31T10:50:16.000Z
---

# 简介

C\+\+的GUI设计，GUI 中文译为 **"图形用户界面"**（英文全称是 *Graphical User Interface*），读作GUI或者guyi\.

除了C\+\+以外，还有Python等语言的版本，但是只有C\+\+是最原生的版本。

常见的QT软件有微信等软件都是用QT做的图形化。

**学QT6之前需要会CMake和C/C\+\+！**

[Vinci机器人队培养路线](https://sdutvincirobot.feishu.cn/wiki/AT59wOz4NiJ9b9kEUyZcCY92n7f)

[Vinci机器人队C/C\+\+教程](/wiki/2023-10-05-cplusplus-jiao-xue)

[CMake C/C\+\+编译环境配置](/wiki/2023-11-23-cmake-c-cpp-bian-yi-huan-jing-pei-zhi)



# 安装

[机器人开发环境搭建](/wiki/2023-12-10-ji-qi-ren-kai-fa-huan-jing-da-jian)

到底是学QT5还是QT6呢？当然是QT6。

qt6是个大趋势，只是一些老公司不愿意换罢了。而且qt5和qt6区别本身也没有那么那么大，学起来基本一样的，你学懂了qt6,自然也就会qt5了，学懂了qt5,自然也就会qt6了。



# 资料参考

更细致的版本（但使用了qmake，无所谓，反正代码都一样）

[https://www.bilibili.com/video/BV1km4y1k7CW]()

更精简的版本（使用cmake）

[https://www.bilibili.com/video/BV1G94y1Q7h6]()



# 教程

## HelloWorld

参考搭建环境中运行的历程：

[机器人开发环境搭建](/wiki/2023-12-10-ji-qi-ren-kai-fa-huan-jing-da-jian)

```c++
#include "QT6TEST/inc/qt6_test.hpp"
#include <QApplication>
#include <QWidget>
#include "ui_mywidget.h"

int qt6_test(int argc,char **argv)
{
    QApplication app(argc, argv);

    // 创建主窗口和 UI 对象
    QWidget mainWindow;
    Ui::MyWidget ui;        // Ui 命名空间中的类名与 .ui 文件中的 class 属性一致
    ui.setupUi(&mainWindow);

    // 设置窗口标题
    mainWindow.setWindowTitle("Hello Qt6!");

    // 显示窗口
    mainWindow.show();

    return app.exec();
}
```



## GUI基础设计

### GUI程序结构与运行机制

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/001-5e2458832719.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/002-1ccbbed106f8.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/003-5b08a5491894.webp)

我们使用的是CMake,所以这里可以不用看。

CMake也是同样的配置，只不过写的不一样罢了。下面是CMake同样的配置：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/004-e559ade41413.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/005-1ed5564f8c0a.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/006-7b984d1dd3f3.webp)

下面是qt designer的界面介绍：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/007-349da1c61801.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/008-d935435f6a0a.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/009-ff5bd784da44.webp)



![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/010-4de144cbbed5.webp)

要用信号槽，必须加上Q\_OBJECT，那我们现在就加上就行，万一以后要用呢。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/011-0e5206998f5f.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/012-2627b9daaf5c.webp)

上面的看不懂没关系，以后就懂了。

创建一个新的ui文件，也是选用widget\.

下面只有这俩属性，最上面的是基类。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/013-076825aed8c8.webp)

然后想改窗口的标题，可以搜索属性windowsTitle

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/014-ae30e8b9cae2.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/015-6bc2bb97abcf.webp)

也可以用代码给窗口命名：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/016-e745120cb6f3.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/017-10fca07b823e.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/018-190ef4fabdf7.webp)

也可以拖拽窗口来改变窗口大小。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/019-6a79929a1ad3.webp)

给按钮改个名字叫close

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/020-25441e4b4ca3.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/021-c41e570b9549.webp)

可以改个名

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/022-03f5fdc508ff.webp)

如图设置是，当按钮被点击，那么我的widget就关闭。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/023-e58991a2ab14.webp)

可以查看下setupUi这个函数，里面能看到很多在qt designer上的设置。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/024-b6b71df21f4a.webp)

这个界面的内存管理是当根被摧毁，叶子也会跟着摧毁。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/025-958b7adae0cb.webp)

这个就是显示刚才设置那个按钮的代码。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/026-bda3dd4b5dac.webp)

```c++
#include "QT6TEST/inc/qt6_test.hpp"
#include <QApplication>
#include <QWidget>
#include "ui_mywidget.h"

int qt6_test(int argc,char **argv)
{
    QApplication app(argc, argv);

    // 创建主窗口和 UI 对象
    QWidget mainWindow;
    Ui::MyWidget ui;        // Ui 命名空间中的类名与 .ui 文件中的 class 属性一致
    ui.setupUi(&mainWindow);
    ui.label->setText("你好");
    ui.pushButton->setText("关闭");


    // 显示窗口
    mainWindow.show();

    return app.exec();
}
```



### 可视化UI设计原理

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/027-fb3706768209.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/028-ff075d91f724.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/029-dba15a3a48db.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/030-2258ea2b2227.webp)

如果想要窗口有自动排版功能，窗口变大也会自动排版，那么我们常用的就是右上角那俩，spacers是占位符。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/031-4e2c48485420.webp)

横竖按钮的布局就是靠图中的这个。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/032-a49f4060fc8e.webp)

connect本身就没有限制，可以不同类型或者相同类型的东西互相连接。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/033-b9dae70a9e94.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/034-150b73e4417c.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/035-093ec588302e.webp)

在cmake要打开这三个东西。



### 代码实现

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/03/30/036-2c894381e970.webp)
