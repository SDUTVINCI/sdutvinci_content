---
title: "四大通信"
---

### 通信机制简介与代码模板
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image332.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class MyNode: public rclcpp::Node
{
  public:
    MyNode():Node("mynode_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"Hello World!");
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  rclcpp::spin(std::make_shared<MyNode>());

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image333.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image334.webp)

https://snippet-generator.app/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image335.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image336.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image337.webp)

```JSON
{
"ROS2节点模板(C++)": {
    "prefix": "ros2_node_cpp",
    "body": [
      "#include \"rclcpp/rclcpp.hpp\"",
      "",
      "class MyNode: public rclcpp::Node",
      "{",
      "  public:",
      "    MyNode():Node(\"mynode_node_cpp\")",
      "    {",
      "      RCLCPP_INFO(this->get_logger(),\"Hello World!\");",
      "    }",
      "};",
      "",
      "int main(int argc, char ** argv)",
      "{",
      "  rclcpp::init(argc,argv);",
      "",
      "  rclcpp::spin(std::make_shared<MyNode>());",
      "",
      "  rclcpp::shutdown();",
      "  return 0;",
      "}"
    ],
    "description": "ROS2节点模板(C++)"
  }
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image338.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image339.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image340.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image341.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image342.webp)

第一个窗口是服务端，第二个窗口是客户端。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image343.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image344.webp)

通信至少要涉及两方，只是一个人算不上通信。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image345.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image346.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image347.webp)

面向接口，话题是一致的，数据载体也是一致的，就可以无缝对接

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image348.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image349.webp)

话题通信：只能单向传输数据

服务通信：双向通信，可以互为客户端和服务端，客户端给服务端发数据，服务端给客户端响应

动作通信：和服务通信很像，有服务端给客户端发的最终响应，但是中间也会连续发送反馈给客户端。

参数服务：参数服务是基于**服务通信**的，参数客户先发送一个请求，然后从参数服务的数据池里拿走数据。也可以更改数据池里的东西，但是不能删除。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image350.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image351.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image352.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image353.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image354.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image355.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image356.webp)

参数通信不用自己定义接口文件，系统会自己弄接口文件，但是开发者是看不到该文件的，该文件被封装了。

我们操作的数据被封装成参数对象了。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image357.webp)

ros2 pkg create + 功能包名（可以写在前面或者） + --build-type(构建类型) + ament_cmake / ament_python + --dependencies（依赖） + rclcpp(ROS2的CPP客户端) + --node-name（节点名） + 节点名

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image358.webp)

在src里创建功能包

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image359.webp)

### 话题通信_理论
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image360.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image361.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image362.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image363.webp)

在ROS1里，node和node之间通信需要经过master，每个传输数据的node都需要在master里注册相关数据，master再将信息进行匹配。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image364.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image365.webp)

一个publisher发布数据，两个subscriber都会接收到数据。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image366.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image367.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image368.webp)

### 话题通信_实验1(C++)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image369.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image370.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image371.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image372.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image373.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image374.webp)

```JSON
ros2 pkg create cpp01_topic --build-type ament_cmake --dependencies rclcpp std_msgs base_interfaces_demo
```

依赖还需要std_msgs，base_interfaces_demo（这里面存放了我们自己定义的所需的接口）

ros2 pkg create + 功能包名（可以写在前面或者最后面） + --build-type(构建类型) + ament_cmake / ament_python + --dependencies（依赖） + rclcpp(ROS2的CPP客户端) + --node-name（节点名） + 节点名

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image375.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image376.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image377.webp)

* * *

发布方

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image378.webp)

定时器是用来控制发送频率的，定时器里还会执行一个回调函数timer_callback。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image379.webp)

count_是计数器，每执行一次这个回调函数，count_就++；

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image380.webp)

spin函数是，程序一旦执行到这里，就返回到上面，返回到上面是为了调用回调函数，如果没有这个spin函数，那么我们这个回调函数是不会被执行的。

以后只要我们创建完节点类对象指针，就要把该指针传入spin()函数里。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image381.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image382.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image383.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image384.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image385.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image386.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class Talker: public rclcpp::Node
{
  public:
    Talker():Node("talker_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"发布节点创建！");
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  rclcpp::spin(std::make_shared<Talker>());

  rclcpp::shutdown();
  return 0;
}
```

此时程序处于挂起状态，会一直运行，因为spin函数。

想结束得按Ctrl+C。

我们想要的类型在std_msgs里，所以要加头文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image387.webp)

create_publisher()第一个入口参数是话题名称，是一个字符串

create_publisher()第二个入口参数是QOS服务质量有关的，是队列深度是一串数字，暂时可以先填10或者20等。

也就是当网络质量不好的时候，消息发不出去了，我们可以将数据先存到队列里，假设填10，最多就可以存10个数，当网络恢复了，我们就从队列里取数据，将其发出。

其他入口参数有默认值，可以暂时先不管。

返回值是一个publisher的指针。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image388.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image389.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image390.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image391.webp)

创建定时器，这个函数有模板，但是模板有默认值可以不设置，

然后三个入口参数，

第一个入口参数是持续时间，也就是周期；

第二个入口参数是回调函数；

第三个入口参数有默认值，先不管。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image392.webp)

使用该命名空间的优势是，在第一个入口参数，可以直接填时间+单位。

如果是1s就写1s，是100ms就填100ms。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image393.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image394.webp)

该函数还有个返回值，返回值是定时器相关的一个指针。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image395.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image396.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image397.webp)

该函数有多个重载，选择适合自己的一个函数。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image398.webp)

发布对象得先创建对象。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image399.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image400.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image401.webp)

把count转化成字符串并发送。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image402.webp)

因为它是一个std::string类型的，我们要转化成c风格的字符串。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image403.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image404.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image405.webp)

尽量在构造函数的时候，给count赋初值0.

```cpp
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/string.hpp"

using namespace std::chrono_literals;

class Talker: public rclcpp::Node
{
  public:
    Talker():Node("talker_node_cpp")，count(0)
    {
      RCLCPP_INFO(this->get_logger(),"发布节点创建！");
      publisher_ = this->create_publisher<std_msgs::msg::String>("chatter",10);
      timer_ = this->create_wall_timer(1s,std::bind(&Talker::on_timer,this));
    }
  private:
    void on_timer()
    {
      auto message = std_msgs::msg::String();
      message.data = "hello world!" + std::to_string(count++);
      RCLCPP_INFO(this->get_logger(),"发布方发布的消息：%s",message.data.c_str());
      publisher_->publish(message);
    }
    rclcpp::Publisher<std_msgs::msg::String>::SharedPtr publisher_;
    rclcpp::TimerBase::SharedPtr timer_;
    size_t count;
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  rclcpp::spin(std::make_shared<Talker>());

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image406.webp)

但是不一定消息真的发布出去了。

验证方法：

```bash
ros2 topic echo /xxx
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image407.webp)

这样才能确定消息被发到chatter话题上了。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image408.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image409.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image410.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image411.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image412.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image413.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image414.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image415.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image416.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image417.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class Listener: public rclcpp::Node
{
public:
    Listener():Node("listener_node_cpp")
    {
        RCLCPP_INFO(this->get_logger(),"订阅方创建!");
    }
};

int main(int argc, char * argv[])
{
    rclcpp::init(argc,argv);

    rclcpp::spin(std::make_shared<Listener>());

    rclcpp::shutdown();
    return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image418.webp)

编译之前别忘了编辑配置文件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image419.webp)

依赖包已经自动生成了，不用管。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image420.webp)

主要改这三大部分

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image421.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image422.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image423.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image424.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image425.webp)

一共有5个入口参数，后面两个入口参数有默认值。

第一个入口参数是话题名称，要保证和发布方一致；

第二个入口参数是QoS，就是服务质量管理，队列深度，10或者20暂时随便设置，可以看看发布方那边的QoS的解释;

第三个入口参数是回调函数，一旦接收到数据，就触发该回调函数。

返回值是一个订阅对象的指针。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image426.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image427.webp)

std::placeholders::_1这个是占位符，_1是指一个。这个地方本应该填入消息。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image428.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image429.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image430.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image431.webp)

```cpp
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/string.hpp"

class Listener: public rclcpp::Node
{
public:
    Listener():Node("listener_node_cpp")
    {
        RCLCPP_INFO(this->get_logger(),"订阅方创建!");
        subscription_ = this->create_subscription<std_msgs::msg::String>("chatter",10,std::bind(&Listener::do_callback,this,std::placeholders::_1));

    }
private:
    void do_callback(const std_msgs::msg::String &msg)
    {
        RCLCPP_INFO(this->get_logger(),"订阅到的消息是:%s",msg.data.c_str());
    }
    rclcpp::Subscription<std_msgs::msg::String>::SharedPtr subscription_;
};

int main(int argc, char * argv[])
{
    rclcpp::init(argc,argv);

    rclcpp::spin(std::make_shared<Listener>());

    rclcpp::shutdown();
    return 0;
}
```

### 话题通信_自定义接口信息
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image432.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image433.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image434.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image435.webp)

构建依赖

执行依赖

当前功能包所属的功能包组

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image436.webp)

find_package是要把构建依赖传递过来

然后还要指定当前被构建的接口文件的路径（通过这个设置，就可以把.msg转化成对应的C++和Python代码了）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image437.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image438.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image439.webp)

文件名可以自定义，但是首字母必须大写

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image440.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image441.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image442.webp)

写完之后test_depend报错了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image443.webp)

删掉即可

编译依赖是rosidl开头的，我们通过grep查询一下

```bash
ros2 pkg list | grep -i rosidl
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image444.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image445.webp)

我们用的是这一个，直接复制过来

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image446.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image447.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image448.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image449.webp)

在list里这个所属的功能包组就没有了，需要自己写rosidl_interface_packages

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image450.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image451.webp)

这个依赖要和构建依赖一样

然后我们要为接口文件生成源码

需要使用rosidl_generate_interfaces函数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image452.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image453.webp)

```bash
colcon build --packages-select base_interfaces_demo
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image454.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image455.webp)

会在install空间下生成student.hpp代码

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image456.webp)

以上.msg生成C++的

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image457.webp)

然后这个是.msg生成的Python的源码

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image458.webp)

也可以通过这个方式来检测是否编译正常

interface是接口

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image459.webp)

### 话题通信_实验2(C++)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image460.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image461.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image462.webp)

新建完源文件之后，要配置CMakeLists

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image463.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image464.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image465.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image466.webp)

将最基本的框架直接复制过来

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image467.webp)

然后替换类的名称

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image468.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class TalkerStu: public rclcpp::Node
{
  public:
    TalkerStu():Node("talkerstu_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"发布节点创建！");
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  rclcpp::spin(std::make_shared<TalkerStu>());

  rclcpp::shutdown();
  return 0;
}
```

为了编码规范，把talkerstu_node_cpp改成小写

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image469.webp)

同样的，订阅方也是需要这样操作

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image470.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class ListenerStu: public rclcpp::Node
{
public:
    ListenerStu():Node("listenerstu_node_cpp")
    {
        RCLCPP_INFO(this->get_logger(),"订阅方创建!");
    }
};

int main(int argc, char * argv[])
{
    rclcpp::init(argc,argv);

    rclcpp::spin(std::make_shared<ListenerStu>());

    rclcpp::shutdown();
    return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image471.webp)

```JSON
{
  "configurations": [
    {
      "browse": {
        "databaseFilename": "${default}",
        "limitSymbolsToIncludedHeaders": false
      },
      "includePath": [
        "/opt/ros/humble/include/**",
        "/home/tungchiahui/mysource/ros2src/3.ws01_plumbing/src/base_interfaces_demo/include/**",
        "/usr/include/**",
        "${workspaceFolder}/",
**        "${workspaceFolder}/install/base_interfaces_demo/include/**"
      ],
      "name": "ROS",
      "intelliSenseMode": "gcc-x64",
      "compilerPath": "/usr/bin/gcc",
      "cStandard": "gnu11",
      "cppStandard": "c++14"
    }
  ],
  "version": 4
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image472.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image473.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image474.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image475.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image476.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image477.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image478.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image479.webp)

不要忘记字符串转成C风格的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image480.webp)

```cpp
#include "rclcpp/rclcpp.hpp"
#include "base_interfaces_demo/msg/student.hpp"

using namespace std::chrono_literals;

class TalkerStu: public rclcpp::Node
{
public:
    TalkerStu():Node("talkerstu_node_cpp"),age(0)
    {
      RCLCPP_INFO(this->get_logger(),"发布节点创建！");
      publisher_ = this->create_publisher<base_interfaces_demo::msg::Student>("chatter_stu",10);
      timer_ = this->create_wall_timer(500ms,std::bind(&TalkerStu::on_timer_callback,this));
    }
private:
    void on_timer_callback()
    {
        auto stu = base_interfaces_demo::msg::Student();
        stu.name = "葫芦娃";
        stu.age = age;
        stu.height = 2.20f;
        age++;
        publisher_->publish(stu);
        RCLCPP_INFO(this->get_logger(),"发布的消息:(%s,%d,%.2f)",stu.name.c_str(),stu.age,stu.height);
    }
    rclcpp::Publisher<base_interfaces_demo::msg::Student>::SharedPtr publisher_;
    rclcpp::TimerBase::SharedPtr timer_;
    int32_t age;
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  rclcpp::spin(std::make_shared<TalkerStu>());

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image481.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image482.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image483.webp)

虽然发布方可以打印日志，但是不代表信息被正常发出去了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image484.webp)

这样检验才是真能确定数据被发送出去了。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image485.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image486.webp)

```cpp
#include "rclcpp/rclcpp.hpp"
#include "base_interfaces_demo/msg/student.hpp"

using base_interfaces_demo::msg::Student;

class ListenerStu: public rclcpp::Node
{
public:
    ListenerStu():Node("listenerstu_node_cpp")
    {
        RCLCPP_INFO(this->get_logger(),"订阅方创建!");
        Subscription_ = this->create_subscription<Student>("chatter_stu",10,std::bind(&ListenerStu::do_callback,this,std::placeholders::1));
    }
private:
    void do_callback(const Student &stu)
    {
        RCLCPP_INFO(this->get_logger(),"订阅的学生信息:name=%s,age=%d,height=%.2f",stu.name.c_str(),stu.age,stu.height);
    }
_    rclcpp::Subscription<Student>::SharedPtr Subscription_;
};

int main(int argc, char * argv[])
{
    rclcpp::init(argc,argv);

    rclcpp::spin(std::make_shared<ListenerStu>());

    rclcpp::shutdown();
    return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image487.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image488.webp)

### 话题通信_rqt查看计算图
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image489.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image490.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image491.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image492.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image493.webp)

图形化工具RQT

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image494.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image495.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image496.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image497.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image498.webp)

### 服务通信_理论
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image499.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image500.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image501.webp)

只能有一个服务端，可以有多个客户端，每个客户端都可以向服务端发送请求。（当然可以有多个服务端，但是会出很多逻辑问题，这是极其不合理的，禁止使用）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image502.webp)

### 服务通信_实验1_服务端实现(C++)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image503.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image504.webp)

先开服务端，然后从客户端提交两个整数，到服务端之后，服务端会解析数据，然后求和，并返回给客户端。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image505.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image506.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image507.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image508.webp)

```bash
ros2 pkg create cpp02_service --build-type ament_cmake --dependencies rclcpp base_interfaces_demo --node-name demo01_server
```

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image509.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image510.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image511.webp)

如果之前用过demo_interfaces_demo，那么一般是不用再配置package.xml了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image512.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image513.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image514.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image515.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image516.webp)

记得文件名首字母要大写！

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image517.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image518.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image519.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image520.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image521.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image522.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image523.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image524.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image525.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image526.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image527.webp)

这是另一个验证方式

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image528.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image529.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class AddIntsServer: public rclcpp::Node
{
  public:
    AddIntsServer():Node("add_ints_server_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"服务端节点创建！");
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  rclcpp::spin(std::make_shared<AddIntsServer>());

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image530.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image531.webp)

服务端是一直要挂起的

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image532.webp)

客户端是执行完毕就结束返回到终端的

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image533.webp)

所以客户端不用调用spin函数，直接创建对象即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image534.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class AddIntsClient: public rclcpp::Node
{
  public:
    AddIntsClient():Node("add_ints_client_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"客户端节点创建！");
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

//   rclcpp::spin(std::make_shared<AddIntsClient>());
  auto client = std::make_shared<AddIntsClient>();

  rclcpp::shutdown();
  return 0;
}
```

然后还要编辑配置文件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image535.webp)

package.xml现在不用修改

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image536.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image537.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image538.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image539.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image540.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image541.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image542.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image543.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image544.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image545.webp)

有4个入口参数，但是后两个有默认值，所以我们只用管前2个。

第一个入口参数就是一个话题名称，字符串

第二个入口参数是回调函数

返回值是一个service类型的智能指针

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image546.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image547.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image548.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image549.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image550.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image551.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image552.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image553.webp)

```cpp
#include "rclcpp/rclcpp.hpp"
#include "base_interfaces_demo/srv/add_ints.hpp"

using base_interfaces_demo::srv::AddInts;

class AddIntsServer: public rclcpp::Node
{
  public:
    AddIntsServer():Node("add_ints_server_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"服务端节点创建！");
      server_ = this->create_service<base_interfaces_demo::srv::AddInts>("add_ints",std::bind(&AddIntsServer::add_callback,this,std::placeholders::_1,std::placeholders::2));
    }
  private:
    void add_callback(const AddInts::Request::SharedPtr req,AddInts::Response::SharedPtr res)
    {
      res->sum = req->num1 + (*req).num2;
      RCLCPP_INFO(this->get_logger(),"%d + %d = %d",req->num1,req->num2,res->sum);
    }
_    rclcpp::Service<base_interfaces_demo::srv::AddInts>::SharedPtr server_;
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  rclcpp::spin(std::make_shared<AddIntsServer>());

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image554.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image555.webp)

因为我们的客户端还没写，所以先暂时用ros2 service call这个小工具来查看服务端的情况

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image556.webp)

```bash
ros2 service call /add_ints base_interfaces_demo/srv/AddInts "{'num1': 10,'num2': 30}"
```

ros2 service call + 话题名 + 接口数据类型 + json代码(也可以理解成yaml格式的)

此json代码(yaml格式)格式: "{'第一个数的名': **空格** +对应数值,'第二个数的名': **空格** +对应数的数值}"

### 服务通信_实验1_客户端实现(C++)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image557.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image558.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image559.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image560.webp)

运行的时候后面跟了两个整形数据，

所以这个argc应该是等于3的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image561.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image562.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image563.webp)

**argv\[\]:接收编译时的返回的argc的参数**

**argc是命令行总的参数个数**

**argv\[\]是argc个参数，其中第0个参数是程序的全名，以后的参数**

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image564.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image565.webp)

必须得保证服务器开着，并且客户端能够连接服务器，如果服务器没开，那么发送的数据会丢失，但是一般使用服务通信的都是比较重要的信息，一定不要丢失了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image566.webp)

客户端发送完数据后，会产生一个响应，这里直接当函数的返回值给返回了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image567.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image568.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image569.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image570.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image571.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image572.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class AddIntsClient: public rclcpp::Node
{
  public:
    AddIntsClient():Node("add_ints_client_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"客户端节点创建！");
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

//   rclcpp::spin(std::make_shared<AddIntsClient>());
  auto client = std::make_shared<AddIntsClient>();

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image573.webp)

这一段就应该放在节点初始化前面，防止多作一些耗资源的操作再进行判断。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image574.webp)

因为RCLCPP_INFO在节点创建的前面，无法使用类和实例化方式进行get_logger，也就是无法使用this指针和节点智能指针来获取。

所以我们采用以下方式：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image575.webp)

这种方式通过rclcpp里的get_logger，但是需要给日志起个名字，放到入口参数里，我们就叫rclcpp吧。

```cpp
#include "rclcpp/rclcpp.hpp"

class AddIntsClient: public rclcpp::Node
{
  public:
    AddIntsClient():Node("add_ints_client_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"客户端节点创建！");
    }
};

int main(int argc, char ** argv)
{
  if(argc != 3)
  {
    RCLCPP_INFO(rclcpp::get_logger("rclcpp"),"请提交两个整形数字!");
    return 1;
  }
  rclcpp::init(argc,argv);

  auto client = std::make_shared<AddIntsClient>();

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image576.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image577.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image578.webp)

如果我不提交参数，直接回车，然后这是一个异常，主函数返回值不是0

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image579.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image580.webp)

也可以把INFO改成ERROR

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image581.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class AddIntsClient: public rclcpp::Node
{
  public:
    AddIntsClient():Node("add_ints_client_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"客户端节点创建！");
    }
};

int main(int argc, char ** argv)
{
  if(argc != 3)
  {
    RCLCPP_ERROR(rclcpp::get_logger("rclcpp"),"请提交两个整形数字!");
    return 1;
  }
  rclcpp::init(argc,argv);

  auto client = std::make_shared<AddIntsClient>();

  rclcpp::shutdown();
  return 0;
}
```

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image582.webp)

一共3个入口参数，

第一个入口参数是话题名称，是字符串;

第二个入口参数和第三个入口参数有默认值，先不用管;

返回值是客户端的智能指针。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image583.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image584.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image585.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image586.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image587.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image588.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image589.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image590.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image591.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image592.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image593.webp)

一旦Ctrl+C关闭，则会疯狂爆INFO，且程序无法停止。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image594.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image595.webp)

再按Ctrl+Z可以停止程序进行。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image596.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image597.webp)

解决上面Ctrl+C的bug：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image598.webp)

rclcpp::ok()这个是判断当前程序是否正常运行，如果正常运行，则返回true，如果不正常运行则返回false，比如按下Ctrl+C就是不正常运行。

当rclcpp::ok() != true的时候，就是ctrl+c按下了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image599.webp)

这样直接可以让函数结束。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image600.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image601.webp)

这时按下ctrl+c会爆很多错误

这是因为

this->get_logger()

client->get_logger()

rclcpp::get_logger()

的不同

这个异常和context有关，初始化的时候会创建context对象，相当于是一个容器，可以往容器里放数据，也可以在容器里取数据。

当前，如果我们连接失败的话，打印日志。

按下ctrl+c会结束我们的ROS2程序，要释放资源，比如要关闭context，这时已经关掉了context，这样，我们再从client和this来获取日志，就不行了，所以建议用rclcpp::get_logger()。

因为rclcpp::get_logger()的调用和context没有关系。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image602.webp)

这样程序就正常了！

```cpp
#include "rclcpp/rclcpp.hpp"
#include "base_interfaces_demo/srv/add_ints.hpp"

using base_interfaces_demo::srv::AddInts;
using namespace std::chrono_literals;

class AddIntsClient: public rclcpp::Node
{
  public:
    AddIntsClient():Node("add_ints_client_node_cpp")
    {
      RCLCPP_INFO(rclcpp::get_logger("rclcpp"),"客户端节点创建！");
      client_ = this->create_client<AddInts>("add_ints");
    }

    bool connect_server()
    {
      while(client_->wait_for_service(1s) != true)
      {
        RCLCPP_INFO(rclcpp::get_logger("rclcpp"),"服务连接中!");

        if (rclcpp::ok() != true)
        {
          RCLCPP_INFO(rclcpp::get_logger("rclcpp"),"强行终止客户端!");
          return false;
        }
      }
      return true;
    }

  private:
    rclcpp::Client<AddInts>::SharedPtr client_;
};

int main(int argc, char ** argv)
{
  if(argc != 3)
  {
    RCLCPP_ERROR(rclcpp::get_logger("rclcpp"),"请提交两个整形数字!");
    return 1;
  }
  rclcpp::init(argc,argv);

  auto client = std::make_shared<AddIntsClient>();

  bool flag = client->connect_server();

  if (flag != true)
  {
    RCLCPP_INFO(rclcpp::get_logger("rclcpp"),"服务器连接失败，程序退出!");
    return 0;
  }

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image603.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image604.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image605.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image606.webp)

返回值类型有了，我们就粘贴过去，

因为using base_interfaces_demo::srv::AddInts所以可以省略成AddInts

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image607.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image608.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image609.webp)

在主函数里要调用函数。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image610.webp)

atoi()是把数据转化成整形

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image611.webp)

我们还要处理响应，响应有3个

第一个是中断，第二个是成功，第三个是超时;

我们一般只判断成功，其他两种情况都认为是失败。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image612.webp)

第一个入口参数是 节点的智能指针

第二个入口参数是future

第三个入口参数有默认值，先不用管

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image613.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image614.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image615.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image616.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image617.webp)

```cpp
#include "rclcpp/rclcpp.hpp"
#include "base_interfaces_demo/srv/add_ints.hpp"

using base_interfaces_demo::srv::AddInts;
using namespace std::chrono_literals;

class AddIntsClient: public rclcpp::Node
{
  public:
    AddIntsClient():Node("add_ints_client_node_cpp")
    {
      RCLCPP_INFO(rclcpp::get_logger("rclcpp"),"客户端节点创建！");
      client_ = this->create_client<AddInts>("add_ints");
    }

    bool connect_server()
    {
      while(client_->wait_for_service(1s) != true)
      {
        RCLCPP_INFO(rclcpp::get_logger("rclcpp"),"服务连接中!");

        if (rclcpp::ok() != true)
        {
          RCLCPP_INFO(rclcpp::get_logger("rclcpp"),"强行终止客户端!");
          return false;
        }
      }
      return true;
    }

    rclcpp::Client<AddInts>::FutureAndRequestId send_request(int32_t num1,int32_t num2)
    {
      /*
        返回值 rclcpp::Client<base_interfaces_demo::srv::AddInts>::FutureAndRequestId
        入口参数 async_send_request(std::shared_ptr<base_interfaces_demo::srv::AddInts_Request> request)  //其实就相当于AddInts::Request类型
      */
      auto request = std::make_sharedautolinkAddInts::Requestautolink();
      request->num1 = num1;
      request->num2 = num2;
      return client_->async_send_request(request);
    }

  private:
    rclcpp::Client<AddInts>::SharedPtr client_;
};

int main(int argc, char ** argv)
{
  if(argc != 3)
  {
    RCLCPP_ERROR(rclcpp::get_logger("rclcpp"),"请提交两个整形数字!");
    return 1;
  }
  rclcpp::init(argc,argv);

  auto client = std::make_shared<AddIntsClient>();

  bool flag = client->connect_server();

  if (flag != true)
  {
    RCLCPP_INFO(rclcpp::get_logger("rclcpp"),"服务器连接失败，程序退出!");
    return 0;
  }
  auto future = client->send_request(atoi(argv[1]),atoi(argv[2]));

  if (rclcpp::spin_until_future_complete(client,future) == rclcpp::FutureReturnCode::SUCCESS)
  {
    RCLCPP_INFO(client->get_logger(),"响应成功! sum = %d",future.get()->sum);
  }
  else
  {
    RCLCPP_INFO(client->get_logger(),"响应失败!");
  }

  rclcpp::shutdown();
  return 0;
}
```

### 动作通信_理论
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image618.webp)

让B一直给A返回当前机器人的状态信息，这样的行为通信更符合我们操控机器人的导航需求。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image619.webp)

输入10，会累加1-10的所有数，并且会遍历1-10所有的数，并进行累加，累加是需要耗时的，假设每累加一次，耗时一秒，

然后为了好看出来程序运行情况，在每累加的时候，都发一个INFO，代表当前进度。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image620.webp)

可以在进行任务时，把任务取消掉。

第一步，客户端给服务端发目标数据

第二步，服务端评估目标数据，并反馈给客户端这个评估结果(是否能够达到目标)

第三步，客户端再给服务端发最终确定的目标数据

第四步，服务端一直反馈给客户端执行的过程数据

第五步，结束之后，服务端反馈给客户端最终的结果

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image621.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image622.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image623.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image624.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image625.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image626.webp)

ros2 pkg create cpp03_action --build-type ament_cmake --dependencies rclcpp rclcpp_action base_interfaces_demo --node-name demo01_action_server

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image627.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image628.webp)

最顶上是请求数据，

中间是最终响应结果的数据，

最底下是连续反馈的数据。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image629.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image630.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image631.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image632.webp)

depend是build depend,exe depend,export depend三者的集成。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image633.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image634.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image635.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image636.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image637.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image638.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image639.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image640.webp)

ros2 interface show base_interfaces_demo/action/Progress

### 动作通信_实验1_服务端实现(C++)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image641.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class ProgressActionServer: public rclcpp::Node
{
  public:
    ProgressActionServer():Node("progress_action_server_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"action服务端创建!");
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  rclcpp::spin(std::make_shared<ProgressActionServer>());

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image642.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class ProgressActionClient: public rclcpp::Node
{
  public:
    ProgressActionClient():Node("progress_action_server_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"action客户端创建!");
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  rclcpp::spin(std::make_shared<ProgressActionClient>());

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image643.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image644.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image645.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image646.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image647.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image648.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image649.webp)

有俩模板，我们只需要设置action就行了，就是我们的动作接口类型。

第一个参数是node，在class里就用this指针，

第二个参数是话题，字符串，

第三个参数是回调函数用来处理目标值的，

第四个参数是回调函数用来处理取消请求的，

第五个参数是接收目标值之后，该回调函数生成连续反馈，

第六、第七个参数有默认值，先不管，

返回值是action智能指针。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image650.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image651.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image652.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image653.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image654.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image655.webp)

Goal_callback解析：

第一个参数是GoalUUID，

第二个参数是我们动作接口下的Goal，

返回值是goalresponse，用的命名空间是rclcpp_action，底下封装了3个常量，

第一个是接收并马上执行，

第二个是接收并推迟执行，

第三个是拒绝。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image656.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image657.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image658.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image659.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image660.webp)

报错原因是没加占位符

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image661.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image662.webp)

```cpp
#include "rclcpp/rclcpp.hpp"
#include "rclcpp_action/rclcpp_action.hpp"
#include "base_interfaces_demo/action/progress.hpp"

using base_interfaces_demo::action::Progress;
using std::placeholders::_1;
using std::placeholders::_2;

class ProgressActionServer: public rclcpp::Node
{
  public:
    ProgressActionServer():Node("progress_action_server_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"action服务端创建!");
      /*
      rclcpp_action::Server<ActionT>::SharedPtr create_server<ActionT,
      NodeT>(NodeT node,
      const std::string &name,
      rclcpp_action::Server<ActionT>::GoalCallback handle_goal,
      rclcpp_action::Server<ActionT>::CancelCallback handle_cancel,
      rclcpp_action::Server<ActionT>::AcceptedCallback handle_accepted,
      const rcl_action_server_options_t &options = rcl_action_server_get_default_options(),
      rclcpp::CallbackGroup::SharedPtr group = nullptr)
      */
      server_ = rclcpp_action::create_server<Progress>(
        this,
        "get_sum_topic",
        std::bind(&ProgressActionServer::handle_goal_callback,this,_1,_2),
        std::bind(&ProgressActionServer::handle_cancel_callback,this,_1),
        std::bind(&ProgressActionServer::handle_accepted_callback,this,_1)
        );
    }
    //std::function<GoalResponse(const GoalUUID &, std::shared_ptr<const typename ActionT::Goal>)>
    rclcpp_action::GoalResponse handle_goal_callback(const rclcpp_action::GoalUUID &, std::shared_ptr<const Progress::Goal>)
    {

      return rclcpp_action::GoalResponse::ACCEPT_AND_EXECUTE;
    }

    //std::function<CancelResponse(std::shared_ptr<ServerGoalHandle<ActionT>>)>
    rclcpp_action::CancelResponse handle_cancel_callback(std::shared_ptr<rclcpp_action::ServerGoalHandle<Progress>> goal_handle)
    {

      return rclcpp_action::CancelResponse::ACCEPT;
    }

    //std::function<void (std::shared_ptr<ServerGoalHandle<ActionT>>)>
    void handle_accepted_callback(std::shared_ptr<rclcpp_action::ServerGoalHandle<Progress>> goal_handle)
    {
    }

  private:
    rclcpp_action::Server<Progress>::SharedPtr server_;
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  rclcpp::spin(std::make_shared<ProgressActionServer>());

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image663.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image664.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image665.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image666.webp)

```bash
ros2 action send_goal /get_sum_topic base_interfaces_demo/action/Progress -f "{'num': 10}"
```

ros2 action send_goal /话题名称 + 接口类型 + -f + 参数

\-f是连续反馈，就是可以获取连续反馈。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image667.webp)

发送目标值为10

然后为我们客户端设置了一个ID，因为可能有多个客户端都访问这个服务端，所以我们要给每个客户端都设置一个唯一的ID

然后结果是0

因为我们程序暂时啥都还没写。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image668.webp)

uuid就是客户端ID，在此时没有使用，那就用(void)uuid，因为如果不用，编译器可能报警告。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image669.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image670.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image671.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image672.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image673.webp)

* * *

我们的这个任务是可以正常被取消的，所以直接return accept就可以，根据不同任务需求来在函数里做不同的事。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image674.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image675.webp)

* * *

因为我们的连续反馈和最终响应的生成是耗时操作，为了避免主逻辑出现阻塞，建议单独再开一个线程。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image676.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image677.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image678.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image679.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image680.webp)

生成连续反馈的API，需要传参，传入的参数就是Progress里的Feedback对象。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image681.webp)

get_goal()可以获取目标值

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image682.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image683.webp)

因为这是个耗时操作，为了看出效果来，所以咱们每次循环的时候都给设置一下休眠。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image684.webp)

1.0是指1Hz，也就是每隔1秒执行一次。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image685.webp)

因为1Hz，所以我们这个rate.sleep()每次都会休眠1秒钟;

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image686.webp)

生成最终结果的API，需要传参，传入的参数就是Progress里的Result对象。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image687.webp)

ok()函数的返回值：如果正常运行，则返回true，如果不正常运行则返回false

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image688.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image689.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image690.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image691.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image692.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image693.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image694.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image695.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image696.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image697.webp)

* * *

bug：当我们终止客户端之后，服务端没有停止运行。服务端要一直执行到程序结束。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image698.webp)

bug解决思路：

接收到取消请求后，就是中断我的主逻辑，也就是execute_callback被关闭，

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image699.webp)

这个函数返回值是布尔值，如果接收到了取消请求就返回true，否则返回false，

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image700.webp)

我们取消之后，其实仍然可以向客户端反应最终的结果，用canceled函数，

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image701.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image702.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image703.webp)

要把定义result放在前面。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image704.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image705.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image706.webp)

```cpp
#include "rclcpp/rclcpp.hpp"
#include "rclcpp_action/rclcpp_action.hpp"
#include "base_interfaces_demo/action/progress.hpp"

using base_interfaces_demo::action::Progress;
using std::placeholders::_1;
using std::placeholders::_2;

class ProgressActionServer: public rclcpp::Node
{
  public:
    ProgressActionServer():Node("progress_action_server_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"action服务端创建!");
      /*
      rclcpp_action::Server<ActionT>::SharedPtr create_server<ActionT,
      NodeT>(NodeT node,
      const std::string &name,
      rclcpp_action::Server<ActionT>::GoalCallback handle_goal,
      rclcpp_action::Server<ActionT>::CancelCallback handle_cancel,
      rclcpp_action::Server<ActionT>::AcceptedCallback handle_accepted,
      const rcl_action_server_options_t &options = rcl_action_server_get_default_options(),
      rclcpp::CallbackGroup::SharedPtr group = nullptr)
      */
      server_ = rclcpp_action::create_server<Progress>(
        this,
        "get_sum_topic",
        std::bind(&ProgressActionServer::handle_goal_callback,this,_1,_2),
        std::bind(&ProgressActionServer::handle_cancel_callback,this,_1),
        std::bind(&ProgressActionServer::handle_accepted_callback,this,_1)
        );
    }

    //std::function<GoalResponse(const GoalUUID &, std::shared_ptr<const typename ActionT::Goal>)>
    rclcpp_action::GoalResponse handle_goal_callback(const rclcpp_action::GoalUUID &uuid, std::shared_ptr<const Progress::Goal> goal)
    {
      (void)uuid;
      if(goal->num <= 1)
      {
        RCLCPP_INFO(this->get_logger(),"提交的目标值必须大于1!");
        return rclcpp_action::GoalResponse::REJECT;
      }
      RCLCPP_INFO(this->get_logger(),"提交的目标值合法!");
      return rclcpp_action::GoalResponse::ACCEPT_AND_EXECUTE;
    }

    //std::function<CancelResponse(std::shared_ptr<ServerGoalHandle<ActionT>>)>
    rclcpp_action::CancelResponse handle_cancel_callback(std::shared_ptr<rclcpp_action::ServerGoalHandle<Progress>> goal_handle)
    {
      (void)goal_handle;
      RCLCPP_INFO(this->get_logger(),"接收到任务取消请求!");
      return rclcpp_action::CancelResponse::ACCEPT;
    }

    //std::function<void (std::shared_ptr<ServerGoalHandle<ActionT>>)>
    void execute_callback(std::shared_ptr<rclcpp_action::ServerGoalHandle<Progress>> goal_handle)
    {
      //void publish_feedback(std::shared_ptr<base_interfaces_demo::action::Progress_Feedback> feedback_msg)
      //goal_handle->publish_feedback();
      int num = goal_handle->get_goal()->num;
      int sum = 0;
      auto feedback = std::make_sharedautolinkProgress::Feedbackautolink();
      auto result = std::make_sharedautolinkProgress::Resultautolink();
      rclcpp::Rate rate(1.0);
      for (int32_t i = 1; i <= num; i++)
      {
        sum += i;
        double progress = i / (double)num;
        feedback->progress = progress;

        goal_handle->publish_feedback(feedback);
        RCLCPP_INFO(this->get_logger(),"连续反馈中，当前进度为:%.2f",progress);

        if(goal_handle->is_canceling() == true)
        {
          result->sum = sum;
          goal_handle->canceled(result);
          RCLCPP_INFO(this->get_logger(),"任务被取消了!");
          return;
        }

        rate.sleep();
      }

      //void succeed(std::shared_ptr<base_interfaces_demo::action::Progress_Result> result_msg)
      //goal_handle->succeed();

      if(rclcpp::ok() == true)
      {
        result->sum = sum;
        goal_handle->succeed(result);
        RCLCPP_INFO(this->get_logger(),"最终响应结果为:%d",sum);
      }

    }

    void handle_accepted_callback(std::shared_ptr<rclcpp_action::ServerGoalHandle<Progress>> goal_handle)
    {
      std::thread(std::bind(&ProgressActionServer::execute_callback,this,goal_handle)).detach();
    }

  private:
    rclcpp_action::Server<Progress>::SharedPtr server_;
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  rclcpp::spin(std::make_shared<ProgressActionServer>());

  rclcpp::shutdown();
  return 0;
}
```

### 动作通信_实验1_客户端实现(C++)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image707.webp)

这条红色的线是在action_client帮我们封装好的，所以可以不用管。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image708.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class ProgressActionClient: public rclcpp::Node
{
  public:
    ProgressActionClient():Node("progress_action_server_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"action客户端创建!");
    }
};

int main(int argc, char ** argv)
{
  if(argc != 2)
  {
    RCLCPP_INFO(rclcpp::get_logger("rclcppp"),"请输入一个整形数字!");
    return 1;
  }
  rclcpp::init(argc,argv);

  rclcpp::spin(std::make_shared<ProgressActionClient>());

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image709.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image710.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image711.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image712.webp)

第一个入口参数是node

第二个入口参数是话题名称，字符串

第三个入口参数和第四个入口参数都有默认值

返回值是客户端智能指针。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image713.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image714.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image715.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image716.webp)

需要把一步分成两步。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image717.webp)

再把send_goal函数调用一下。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image718.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image719.webp)

async_send_goal是异步发送目标值

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image720.webp)

第一个入口参数是我们接口文件里的目标值

第二个入口参数是发送目标选项对象，我们可以设置这个目标发送过去之后，我们需要处理的一些回调函数

返回值是

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image721.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image722.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image723.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image724.webp)

先不管其他的，先把函数随便定义上，什么返回值，入口参数都是void，先不让程序报错。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image725.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image726.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image727.webp)

得知，

GoalResponseCallback返回值是void，入口参数是goalhandle，goalhandle在本图的上面，是clientgoalhandle，然后这个clientgoalhandle属于rclcpp_action工作空间

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image728.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image729.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image730.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image731.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image732.webp)

返回值是void

第一个入口参数是clientgoalhandle

第二个入口参数是feedback

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image733.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image734.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image735.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image736.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image737.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image738.webp)

发送一个数值给服务端，服务端要先拿到目标值进行判断，判断该目标值是否可以被接收，或者被拒绝，再把处理结果响应给客户端。

如果说这个目标值可处理，那么goal_handle里是有内容的;

如果不可以被处理，那么goal_handle是一个nullptr空指针。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image739.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image740.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image741.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image742.webp)

* * *

接下来处理反馈数据：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image743.webp)

如果我们只是解析反馈的数据，那么goal_handle是用不上的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image744.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image745.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image746.webp)

需要用俩%来转译%，如上图是打印百分比数据的案例。

假设progress_int是50，则会输出50%。

如果只想打印一个%，那就需要%%来转译。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image747.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image748.webp)

回调函数是可能会数据丢失的。这是正常现象。

* * *

最终响应：

这个result最终结果的状态是不一定的，有可能任务被取消了，或被终止了，也有可能任务正常运行了。

所以我们要通过状态码来判断状态。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image749.webp)

第一个是被强行终止

第二个是取消

第三个是成功

第四个是未知

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image750.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image751.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image752.webp)

```cpp
#include "rclcpp/rclcpp.hpp"
#include "rclcpp_action/rclcpp_action.hpp"
#include "base_interfaces_demo/action/progress.hpp"

using base_interfaces_demo::action::Progress;
using namespace std::chrono_literals;
using std::placeholders::_1;
using std::placeholders::_2;

class ProgressActionClient: public rclcpp::Node
{
  public:
    ProgressActionClient():Node("progress_action_server_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"action客户端创建!");
      /*
      rclcpp_action::Client<ActionT>::SharedPtr create_client<ActionT,
      NodeT>(NodeT node, const std::string &name,
      rclcpp::CallbackGroup::SharedPtr group = nullptr,
      const rcl_action_client_options_t &options = rcl_action_client_get_default_options())
      */
      client_ = rclcpp_action::create_client<Progress>(this,"get_sum_topic");
    }

    void send_goal(int32_t num)
    {
      if(client_->wait_for_action_server(1s) != true)
      {
        RCLCPP_ERROR(this->get_logger(),"服务连接失败!");
        return;
      }

      /*
      std::shared_future<rclcpp_action::ClientGoalHandle<base_interfaces_demo::action::Progress>::SharedPtr>
      async_send_goal(const base_interfaces_demo::action::Progress::Goal &goal,
      const rclcpp_action::Client<base_interfaces_demo::action::Progress>::SendGoalOptions &options)
      */
      auto goal = Progress::Goal();
      goal.num = num;

      rclcpp_action::Client<Progress>::SendGoalOptions options;
      options.goal_response_callback = std::bind(&ProgressActionClient::goal_response_callback,this,_1);
      options.feedback_callback = std::bind(&ProgressActionClient::feedback_callback,this,_1,_2);
      options.result_callback = std::bind(&ProgressActionClient::result_callback,this,_1);

      auto future = client_->async_send_goal(goal,options);
    }

  void goal_response_callback(rclcpp_action::ClientGoalHandle<Progress>::SharedPtr goal_handle)
  {
    if(goal_handle == nullptr)
    {
      RCLCPP_INFO(this->get_logger(),"目标请求被服务端拒绝!");
    }
    else
    {
      RCLCPP_INFO(this->get_logger(),"目标处理中!");
    }
  }

  void feedback_callback(rclcpp_action::ClientGoalHandle<Progress>::SharedPtr goal_handle,std::shared_ptr<const Progress::Feedback> feedback)
  {
    (void)goal_handle;
    double progress = feedback->progress;
    int progress_int = (int) (progress * 100);
    RCLCPP_INFO(this->get_logger(),"当前进度为:%d%%",progress_int);
  }

  void result_callback(const rclcpp_action::ClientGoalHandle<Progress>::WrappedResult & result)
  {
    if (result.code == rclcpp_action::ResultCode::SUCCEEDED)
    {
      RCLCPP_INFO(this->get_logger(),"最终结果为:%d",result.result->sum);
    }
    else if(result.code == rclcpp_action::ResultCode::ABORTED)
    {
      RCLCPP_INFO(this->get_logger(),"过程被中断!");
    }
    else if(result.code == rclcpp_action::ResultCode::CANCELED)
    {
      RCLCPP_INFO(this->get_logger(),"任务被取消!");
    }
    else
    {
      RCLCPP_INFO(this->get_logger(),"未知异常!");
    }
  }

  private:
    rclcpp_action::Client<Progress>::SharedPtr client_;
};

int main(int argc, char ** argv)
{
  if(argc != 2)
  {
    RCLCPP_ERROR(rclcpp::get_logger("rclcppp"),"请输入一个整形数字!");
    return 1;
  }
  rclcpp::init(argc,argv);

  auto node = std::make_shared<ProgressActionClient>();

  node->send_goal(atoi(argv[1]));

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image753.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image754.webp)

此时我们取消客户端，反而服务端还在运行，这里的ctrl+c只是结束了我们的客户端，而不是指挥我们的客户端去下发取消请求指令，我们只有去捕获一下我们的键盘才能完成取消请求指令的发送。

* * *

修复bug:

还没修复好

```cpp
// 发送取消请求auto future_cancel = client_->async_cancel_goal(goal_handle);
rclcpp::spin_until_future_complete(this->get_node_base_interface(), future_cancel);
if (future_cancel.wait_for(1s) == std::future_status::ready)
{
  RCLCPP_INFO(this->get_logger(), "终止请求已发送!");
}
else
{
  RCLCPP_ERROR(this->get_logger(), "无法发送终止请求...");
}
```

### 参数服务_理论与API介绍(C++)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image755.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image756.webp)

当然还有特殊情况，比如把参数设置为私有的。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image757.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image758.webp)

其他通信需要自己弄接口文件，但是参数服务不用自己弄接口文件，ROS2已经封装好了API，所以我们只需要调用API即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image759.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image760.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image761.webp)

只是想展示一下API，所以先创建参数功能包展示下API，先不创建客户端和服务端。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image762.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image763.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image764.webp)

rclcpp::parameter 对象(键,值);

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image765.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image766.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image767.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image768.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image769.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image770.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image771.webp)

该函数是给parameter的值赋值的，有18个重载，各种类型。

其中空是说不给赋值，这样只有键，没有值。

### 参数服务_实验1_服务端(C++)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image772.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image773.webp)

这里有差异，在Node里传入了第二个参数，这句是专门用来允许删除参数的。undeclared解除声明。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image774.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image775.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image776.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image777.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image778.webp)

只有查询和修改的接口API，并没有新增和删除的API，这是ROS2根据安全考虑的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image779.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image780.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image781.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image782.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class ParamServer: public rclcpp::Node
{
  public:
    ParamServer():Node("param_server_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"参数服务端搭建!");
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  auto node = std::make_shared<ParamServer>();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
#include "rclcpp/rclcpp.hpp"

class ParamClient: public rclcpp::Node
{
  public:
    ParamClient():Node("param_client_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"参数客户端搭建!");
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  auto node = std::make_shared<ParamClient>();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image783.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image784.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image785.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image786.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image787.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image788.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image789.webp)

链式编程。

一个普通的节点就可以当参数服务端，不需要另行创建参数服务端。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image790.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image791.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class ParamServer: public rclcpp::Node
{
  public:
    ParamServer():Node("param_server_node_cpp",rclcpp::NodeOptions().allow_undeclared_parameters(true))
    {
      RCLCPP_INFO(this->get_logger(),"参数服务端搭建!");
    }

    void create_param()
    {
      RCLCPP_INFO(this->get_logger(),"-------------增操作--------------");
    }

    void get_param()
    {
      RCLCPP_INFO(this->get_logger(),"-------------查操作--------------");
    }

    void update_param()
    {
      RCLCPP_INFO(this->get_logger(),"-------------改操作--------------");
    }

    void delete_param()
    {
      RCLCPP_INFO(this->get_logger(),"-------------删操作--------------");
    }

};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  auto node = std::make_shared<ParamServer>();

  node->create_param();
  node->get_param();
  node->update_param();
  node->delete_param();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image792.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image793.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image794.webp)

ros2 param list

查询所有节点里的所有参数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image795.webp)

ros2 param get /节点名称 参数键名 来查看参数的值

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image796.webp)

可以通过键来查询参数的值

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image797.webp)

带复数形式的函数可以通过由键组成的容器来获取一些参数对象。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image798.webp)

来判断是否有该参数的，入口参数也是键，返回值是布尔值。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image799.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image800.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image801.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image802.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image803.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image804.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image805.webp)

需要传入parameter对象。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image806.webp)

我们覆盖掉旧值即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image807.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image808.webp)

通过set_parameter也可以创建参数,但是必须声明allow_undeclared_parameters(true)。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image809.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image810.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image811.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image812.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image813.webp)

这种声明的参数不可以被删除，只能删除未声明但设置的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image814.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image815.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

class ParamServer: public rclcpp::Node
{
  public:
    ParamServer():Node("param_server_node_cpp",rclcpp::NodeOptions().allow_undeclared_parameters(true))
    {
      RCLCPP_INFO(this->get_logger(),"参数服务端搭建!");
    }

    void create_param()
    {
      RCLCPP_INFO(this->get_logger(),"-------------增操作--------------");

      this->declare_parameter("car_name","ER");
      this->declare_parameter("width",1.55);
      this->declare_parameter("wheels",5);

      this->set_parameter(rclcpp::Parameter("height",2.00));
    }

    void get_param()
    {
      RCLCPP_INFO(this->get_logger(),"-------------查操作--------------");

      auto car = this->get_parameter("car_name");
      RCLCPP_INFO(this->get_logger(),"key = %s,value = %s",car.get_name().c_str(),car.as_string().c_str()); 

      auto params = this->get_parameters({"car_name","width","wheels"});
      for(auto &¶m : params)
      {
        RCLCPP_INFO(this->get_logger(),"key = %s,value = %s",param.get_name().c_str(),param.value_to_string().c_str());
      }
      bool car_name_flag = this->has_parameter("car_name");
      bool height_flag = this->has_parameter("height");
      RCLCPP_INFO(this->get_logger(),"是否包含car_name? 答案:%d",car_name_flag);
      RCLCPP_INFO(this->get_logger(),"是否包含height? 答案:%d",height_flag);
    }

    void update_param()
    {
      RCLCPP_INFO(this->get_logger(),"-------------改操作--------------");
      this->set_parameter(rclcpp::Parameter("width",1.85));
      RCLCPP_INFO(this->get_logger(),"width = %.2f",this->get_parameter("width").as_double());
    }

    void delete_param()
    {
      RCLCPP_INFO(this->get_logger(),"-------------删操作--------------");
    //   this->undeclare_parameter("car_name");
    //   RCLCPP_INFO(this->get_logger(),"是否包含car_name? 答案:%d",this->has_parameter("car_name"));
      this->undeclare_parameter("height");
      RCLCPP_INFO(this->get_logger(),"是否包含height? 答案:%d",this->has_parameter("height"));
    }

};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  auto node = std::make_shared<ParamServer>();

  node->create_param();
  node->get_param();
  node->update_param();
  node->delete_param();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

### 参数服务_实验1_客户端(C++)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image816.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image817.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image818.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image819.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image820.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image821.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image822.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image823.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image824.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image825.webp)

第一个入口参数是客户端节点对象，

第二个入口参数是需要连接的服务端的节点名称。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image826.webp)

如果1秒钟之内连接上了就返回true，如果超时1s没连接上就返回false。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image827.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image828.webp)

```cpp
#include "rclcpp/rclcpp.hpp"

using namespace std::chrono_literals;

class ParamClient: public rclcpp::Node
{
  public:
    ParamClient():Node("param_client_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"参数客户端搭建!");
      param_client_ = std::make_sharedautolinkrclcpp::SyncParametersClientautolink(this,"param_server_node_cpp");
    }

    bool connect_server()
    {
      while(param_client_->wait_for_service(1s) != true)
      {
        if(rclcpp::ok() != true)
        {
          return false;
        }
        RCLCPP_INFO(this->get_logger(),"服务连接中!");
      }
      return true;
    }

    void get_param()
    {
      RCLCPP_INFO(this->get_logger(),"-----------参数查询操作-------------");
    }

    void update_param()
    {
      RCLCPP_INFO(this->get_logger(),"-----------参数更新操作-------------");
    }

  private:
    rclcpp::SyncParametersClient::SharedPtr param_client_;
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  auto client = std::make_shared<ParamClient>();

  bool flag = client->connect_server();

  if(!flag)
  {
    return 0;
  }

  client->get_param();
  client->update_param();
  client->get_param();

  // rclcpp::spin(client);

  rclcpp::shutdown();
  return 0;
}
```

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image829.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image830.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image831.webp)

这些话题都是我们此节点名称下的。

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image832.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image833.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image834.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image835.webp)

要用高级for

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image836.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image837.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image838.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image839.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image840.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image841.webp)

入口参数填参数对象的容器。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image842.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image843.webp)

我们不仅可以修改值，也可以创建新的参数，但是要保证服务端那边调用过undeclared......

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image844.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image845.webp)
