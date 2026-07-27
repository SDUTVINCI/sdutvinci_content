---
title: "回溯rosbag2"
---

### 概述
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1228.webp)

方式1一边采集数据，一边生成地图信息。

方式2是将采集数据和生成地图信息分割开来了，方式2做到了解耦合，所以更灵活一些。用同一套数据，可能用不同的算法处理，总之，非常灵活。

留存的过程咱们也可以叫做序列化。（转化为磁盘文件）

留存一般叫录制（序列化）。

读取一般叫回放(反序列化)。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1229.webp)

留存时也可以将文件进行分卷，也就是每个文件最大能占多大的大小，如果超过该大小，就新建一个文件继续留存。（类似于压缩文件的分卷）

这样的话，存的数据太大，我们一次性打开太慢，就可以分段打开。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1230.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1231.webp)

需要依赖于rosbag2_cpp或者rosbag2_py

然后还要依赖于geometry_msgs，这个是因为我们要序列化的数据是这个包下的速度指令。

```bash
ros2 pkg create cpp02_rosbag --build-type ament_cmake --dependencies rclcpp rosbag2_cpp geometry_msgs --node-name cpp01_writer
```

### rosbag2的命令工具
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1232.webp)

一般ROSBAG2的使用有命令行工具和编码两种使用方式，**命令行工具功能比较齐全，够用。**

查看帮助文档ros2 bag -h

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1233.webp)

主要有6个指令。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1234.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1235.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1236.webp)

可以看record的详细用法。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1237.webp)

record是用来将消息序列化的。(录制)

play是用来反序列化消息的。(回放)

info是用来输出bag文件的相关信息的。比如有多少条消息，录制起始时间和终止时间以及持续时间。

reindex是重建bag文件，可以修改bag源数据文件。

list是输出rosbag2中可用的插件(高阶应用)。

convert我们可以用这个给bag文件修改扩展名，也可以把多个bag文件合并成一个文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1238.webp)

打开小乌龟节点与键盘控制小乌龟节点。

我们用键盘控制小乌龟，然后把速度指令通过rosbag2给序列化。

然后我们关掉两个节点，再重启小乌龟节点，然后这次不通过键盘控制，而是通过play bag文件让小乌龟运动。

record指令后面要跟一个话题组成的列表，但是在咱们下面的操作中，只用到了一个话题。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1239.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1240.webp)

然后再用output，把序列化后的文件写出到一个磁盘目录中去。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1241.webp)

cd进想保存bag的目录，然输入record指令，后面跟话题名称，然后-o +bag文件名，这里也可以不重新命名bag文件，这样会用默认的名字，默认的名称是年月日命名的。

这样就已经开始录制了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1242.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1243.webp)

按Ctrl + C进行结束，结束有个提示，说正在将消息写入bag，需要一段时间。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1244.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1245.webp)

这个yaml文件是源数据文件。

db3是SQLite数据库，这个是移动端(比如手机)常用的数据库。

这个数据库就存储了录制的数据。

### rosbag2 C++案例分析及框架搭建
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1246.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1247.webp)

```cmake
add_executable(demo01_writer src/demo01_writer.cpp)
ament_target_dependencies(
  demo01_writer
  "rclcpp"
  "rosbag2_cpp"
  "geometry_msgs"
)

add_executable(demo02_reader src/demo02_reader.cpp)
ament_target_dependencies(
  demo02_reader
  "rclcpp"
  "rosbag2_cpp"
  "geometry_msgs"
)

install(TARGETS 
  demo01_writer
  demo02_reader
  DESTINATION lib/${PROJECT_NAME})
```

### rosbag2 C++ 录制数据
```cpp
/* 
  需求：录制 turtle_teleop_key 节点发布的速度指令。
  步骤：
    1.包含头文件；
    2.初始化 ROS 客户端；
    3.定义节点类；
      3-1.创建写出对象指针；
      3-2.设置写出的目标文件；
      3-3.写出消息。
    4.调用 spin 函数，并传入对象指针；
    5.释放资源。

 */
// 1.包含头文件；
#include "rclcpp/rclcpp.hpp"
#include "rosbag2_cpp/writer.hpp"
#include "geometry_msgs/msg/twist.hpp"

using std::placeholders::_1;

// 3.定义节点类；
class SimpleBagRecorder : public rclcpp::Node
{
public:
  SimpleBagRecorder()
  : Node("simple_bag_recorder")
  {
    // 3-1.创建写出对象指针；
    writer_ = std::make_unique<rosbag2_cpp::Writer>();
    // 3-2.设置写出的目标文件；(目录为ws目录)
    writer_->open("src/cpp02_rosbag/my_bag");
    subscription_ = create_subscription<geometry_msgs::msg::Twist>(
      "/turtle1/cmd_vel", 10, std::bind(&SimpleBagRecorder::topic_callback, this, _1));
  }

private:
  void topic_callback(std::shared_ptr<rclcpp::SerializedMessage> msg) const
  {
    rclcpp::Time time_stamp = this->now();
    // 3-3.写出消息。
    RCLCPP_INFO(this->get_logger(),"数据写出... ...");
    writer_->write(msg, "/turtle1/cmd_vel", "geometry_msgs/msg/Twist", time_stamp);
  }

  rclcpp::Subscription<geometry_msgs::msg::Twist>::SharedPtr subscription_;
  std::unique_ptr<rosbag2_cpp::Writer> writer_;
};

int main(int argc, char * argv[])
{
  // 2.初始化 ROS 客户端；
  rclcpp::init(argc, argv);
  // 4.调用 spin 函数，并传入对象指针；
  rclcpp::spin(std::make_shared<SimpleBagRecorder>());
  // 5.释放资源。
  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1248.webp)

这是一个相对目录，目录位置是工作空间目录。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1249.webp)

写数据之前，要先创建一个订阅方，订阅方要建立一个回调函数。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1250.webp)

回调函数入口参数，消息类型用write函数的入口参数。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1251.webp)

这里要用斜杠代替冒号，因为入口是string类型。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1252.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1253.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1254.webp)

创建乌龟节点。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1255.webp)

启动writer节点

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1256.webp)

运行小乌龟

运行一会儿后，关掉所有节点。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1257.webp)

成功生成了文件

验证录制文件是否成功。

在验证前，先创建一个乌龟节点，不用创建控制节点。

```cpp
ros2 bag play src/cpp02_rosbag/my_bag
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1258.webp)

可以看到乌龟正常走了。回放成功！

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1259.webp)

如果已经生成了一遍my_bag，再想生成新的会显示不能覆盖。

解决方案，可以把my_bag设置为动态的，加个时间戳或者直接按功能命名。

### rosbag2 C++ 读取数据
```cpp
/* 
  需求：读取 bag 文件数据。
  步骤：
    1.包含头文件；
    2.初始化 ROS 客户端；
    3.定义节点类；
      3-1.创建读取对象指针；
      3-2.设置读取的目标文件；
      3-3.读消息；
      3-4.关闭文件。
    4.调用 spin 函数，并传入对象指针；
    5.释放资源。

 */
 // 1.包含头文件；
 #include "rclcpp/rclcpp.hpp"
 #include "rosbag2_cpp/reader.hpp"
 #include "geometry_msgs/msg/twist.hpp"
 // 3.定义节点类；
class SimpleBagPlayer: public rclcpp::Node {
public:
    SimpleBagPlayer():Node("simple_bag_player"){
        // 3-1.创建读取对象指针；
        reader_ = std::make_unique<rosbag2_cpp::Reader>();
        // 3-2.设置读取的目标文件；
        reader_->open("src/cpp02_rosbag/my_bag");
        // 3-3.读消息；
        while (reader_->has_next())
        {
            auto twist = reader_->read_next<geometry_msgs::msg::Twist>();
            RCLCPP_INFO(this->get_logger(),"线速度:%.2f, 角速度: %.2f",twist.linear.x, twist.angular.z);
        }
        // 3-4.关闭文件。
        reader_->close();
    }
private:
    std::unique_ptr<rosbag2_cpp::Reader> reader_;

};

int main(int argc, char const *argv[]){
    // 2.初始化 ROS 客户端；
    rclcpp::init(argc,argv);
    // 4.调用 spin 函数，并传入对象指针；
    rclcpp::spin(std::make_shared<SimpleBagPlayer>());
    // 5.释放资源。
    rclcpp::shutdown();
    return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1260.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1261.webp)

编译

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1262.webp)

这显示能读出来几条信息。能读出来8条。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1263.webp)

正好8条
