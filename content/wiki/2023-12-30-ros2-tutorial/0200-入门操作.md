---
title: "入门操作"
---

### 简介
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image83.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image84.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image85.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image86.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image87.webp)

### 终端环境搭建
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image88.webp)

如果在上方安装ROS2的时候，已经将该语句添加到`~/.bashrc`了，则不必再跟着这步操作了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image89.webp)

```bash
source /opt/ros/humble/setup.bash  #将ROS2环境变量配置到当前位置
echo " source /opt/ros/humble/setup.bash" >> ~/.bashrc    #每次启动终端都会运行该句
```

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image90.webp)

```bash
ros2 run demo_nodes_cpp talker
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image91.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image92.webp)

用ctrl+c来进行取消程序运行

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image93.webp)

```bash
ros2 run demo_nodes_py listener
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image94.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image95.webp)

```bash
ros2 run turtlesim turtlesim_node
ros2 run turtlesim turtle_teleop_key
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image96.webp)

### 命令行操作
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image97.webp)

mkdir -p 新建文件夹

rm -R 递归删除（删掉文件夹及里面包含的文件夹及文件）

touch 新建文件

rm 删除文件

cd ..退回上级目录（cd 点点）

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image98.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image99.webp)

会弹提示信息，告诉我们后面要跟的参数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image100.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image101.webp)

ros2 node list会把当前ROS2正在运行的节点列出来

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image102.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image103.webp)

ros2 node info + /节点名 可以查看目标节点的详细情况

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image104.webp)

会弹提示信息，告诉我们后面要跟的参数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image105.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image106.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image107.webp)

可以通过话题来显示机器人运动的状态

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image108.webp)

```bash
ros2 topic pub --rate 1 /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image109.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image110.webp)

```bash
ros2 service call /spawn turtlesim/srv/Spawn "{x: 2, y: 2, theta: 0.2, name: ''}"
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image111.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image112.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image113.webp)

```bash
ros2 bag record /turtle1/cmd_vel
ros2 bag play rosbag2_2022_04_11-17_35_40/rosbag2_2022_04_11-17_35_40_0.db3
```

ros2 bag record + 话题

按Ctrl+C结束，然后录制的数据在当前终端的目录下

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image114.webp)

如何去复现呢？

ros2 bag play + 文件夹名称

### ROS2 HelloWorld(C++)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image115.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image116.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image117.webp)

1.创建功能包

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image118.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image119.webp)

指令就是创建ros2的功能包

ros2 pkg create + 功能包名 + --build-type(构建类型) + ament_cmake / ament_python + --dependencies（依赖） + rclcpp(ROS2的CPP客户端) + --node-name（节点名） + 节点名

```bash
ros2 pkg create pkg01_helloworld_cpp --build-type ament_cmake --dependencies rclcpp --node-name helloworld
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image120.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image121.webp)

源文件自动生成了，文件名和我们指定的node name是一致的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image122.webp)

这是自动生成的内容，但是和ROS2没有任何关系。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image123.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image124.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image125.webp)

如果依赖的库不止这一个，则再回车，

xxx

再添加下一个

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image126.webp)

10行是查找包

12行是添加可执行的

add_executable 的第一个参数是 可执行文件的名字（默认和节点名一致，默认和源文件名一致） 第二个参数是源文件的名字

17行是为我们的可执行程序添加依赖 我们的可执行程序依赖于RCLCPP这个库

22行是要为我们的可执行程序设立一个安装目录，创建在了当前功能包下的lib目录，也就是 工作空间名/install/功能包名/lib

编辑配置文件之后编译，用cd..返回ws目录

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image127.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image128.webp)

图标为绿色，是没有警告也没有错误

是黄色的则有警告

是红色的则有致命错误

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image129.webp)

可执行二进制文件的路径

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image130.webp)

```bash
source install/setup.bash #刷新环境变量
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image131.webp)

```bash
ros2 run pkg01_helloworld_cpp helloworld
```

ros2 run 功能包名称 可执行文件名(默认和节点名一致)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image132.webp)

编辑ROS2 C++源文件：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image133.webp)

```
#include "rclcpp/rclcpp.hpp"

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  auto node = rclcpp::Node::make_shared("helloworld_node");

  RCLCPP_INFO(node->get_logger(),"hello world!");

  rclcpp::shutdown();

  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image134.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image135.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image136.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image137.webp)

### ROS2 HelloWorld(Python)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image138.webp)

```bash
ros2 pkg create pkg02_helloworld_py --build-type ament_python --dependencies rclpy --node-name helloworld
```

ros2 pkg create + 功能包名 + --build-type(构建类型) + ament_cmake / ament_python + --dependencies（依赖） + rclpy(ROS2的Python客户端) + --node-name（节点名） + 节点名

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image139.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image140.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image141.webp)

与node name和可执行二进制文件同名

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image142.webp)

默认这里面已经有代码，但是和ROS2无关，这是标准的python代码

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image143.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image144.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image145.webp)

二进制可执行文件 映射到 源文件的main函数

如何编译呢？

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image146.webp)

先返回上一级，来到ws目录

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image147.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image148.webp)

有个黄色警告，但是不影响我们使用。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image149.webp)

```bash
source ./install/setup.bash
```

刷新环境变量

```bash
ros2 run pkg02_helloworld_py helloworld
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image150.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image151.webp)

```python
import rclpy

def main():
    rclpy.init()
    node = rclpy.create_node("helloworld_py_node")
    node.get_logger().info("hello world by python!")
    rclpy.shutdown()

if name == '__main__':
    main()
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image152.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image153.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image154.webp)

### 运行优化(bash终端环境)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image155.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image156.webp)

要使用绝对路径

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image157.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image158.webp)

尽量不要这么干，ROS2有一个bug，就是不同工作空间的功能包可能会调用混乱，所以先不要搞全局的运行优化。

### VScode环境搭建
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image159.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image160.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image161.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image162.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image163.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image164.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image165.webp)

看C/C++，Python，CMake，XML,YAML文件就可以代码高亮显示

在写一些ROS2消息的代码可以提供代码补齐等操作

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image166.webp)

编写机器人模型所要用的插件，也可以进行代码补齐

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image167.webp)

ROS2经常生成PDF文件，可以通过这个插件来查看

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image168.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image169.webp)

ROS2插件建议等成熟之后再进行安装

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image170.webp)

这个官方插件可以尝试安装

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image171.webp)

人工智能代码补全

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image172.webp)

MarkDown高亮

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image173.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image174.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image175.webp)

虽然报错，但是程序是可以正常运行的。（主要是vscode找不到头文件）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image176.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image177.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image178.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image179.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image180.webp)

```JSON
            "includePath": [
                "${default}",
                "${workspaceFolder}/**",
                "/opt/ros/humble/include/**"
            ],
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image181.webp)

/\*\*代表要包含该文件夹下的所有的子集

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image182.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image183.webp)

* * *

按Ctrl + \`（ESC底下的按键）把VSCODE终端打开

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image184.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image185.webp)

\--node-name也是可选参数，如果不配置，则不会有源文件，也不会有可执行文件到源文件的映射

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image186.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image187.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image188.webp)

不需要修改，已经默认生成好了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image189.webp)

再返回WS目录进行编译（但是此时编译是编译整个WS目录下的所有功能包）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image190.webp)

刷新环境变量并运行

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image191.webp)

* * *

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image192.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image193.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image194.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image195.webp)

* * *

如何在一个功能包里添加多个源文件呢？

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image196.webp)

新建一个新文件，比如hellovscode2.cpp

但是此时该文件是一个孤零零的文件，他没有做任何的配置，对应的，编译完之后也不会被执行。

我们想编译执行该文件，必须配置相关的配置文件。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image197.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image198.webp)

选中的这些用不着，可以删掉

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image199.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image200.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image201.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image202.webp)

### 运行优化(colcon build)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image203.webp)

平常会全编译WS目录下的文件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image204.webp)

```bash
colcon build --packages-select xxx xxx xxx #可以指向多个包
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image205.webp)

### VScode环境进阶
#### clangd插件代码提示(可选,但是建议)
https://colcon.readthedocs.io/en/released/index.html

由于C/C++插件在大项目里的表现简直拉胯的一批，所以我们选择使用llvm里的clangd插件来进行代码提示。

但clangd依赖于cmake生成一个编译信息文件，我们需要一些步骤来生成该文件。

由于ROS2没有像ROS1那样的一个总的规范的CMakeLists，所以配置起来没有ROS1那么方便。

1.  配置colcon build参数

    1.  方法一：（不建议） **每次** 编译要用该命令：

    ```bash
    colcon build --cmake-args -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
    ```

      等同于在cmake文件里写上（一般不建议改cmakelists）

    ```bash
    set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
    ```
    5.  方法二：全局参数(更加推荐)

```bash
mkdir ~/.colcon

vim ~/.colcon/defaults.yaml
```

按下`insert（插入）`按键

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image206.webp)

输入下方内容

```bash
build:
  cmake-args:
    - -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
```

按下`ESC`，并按下`:wq`,然后按下`Enter(回车)`即可成功保存。

在编译的时候正常用`colcon build`就可以自动启用CMAKE_EXPORT_COMPILE_COMMANDS=ON参数了。

2.  然后再来配置clangd插件

    1.  先下载clangd插件

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image207.webp)

    2.  下载clangd文件

      按住Ctrl shift P打开搜索框

      输入clangd 找到下载语言服务器这一项目，点击安装clangd（请保持良好的网络状况）

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image208.webp)

    6.  接着配置clangd：

禁用C/C++的代码提示功能

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image209.webp)

如果没有上图的弹窗，可以进行手动关闭，依然是ctrl shift P,输入settings然后找到如下图的选项

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image210.webp)

找到下图这个选项，改成disabled即可。

`"C_Cpp.intelliSenseEngine": "disabled"`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image211.webp)

4.  重启clangd

然后ctrl shift P搜索clangd找到如下图的选项（重启clangd语言服务器前，要先colcon build）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image212.webp)

代码提示就正常啦

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image213.webp)

### 安装其他工具

#### terminal
##### 安装terminator(选装,因人而异,有更好用的Warp,请往下看)

```bash
sudo apt install terminator
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image214.webp)


| 快捷键                | 功能              | 记忆方法                                |
| ------------------ | --------------- | ----------------------------------- |
| `Ctrl + Shift + E` | 垂直分屏，左右分        | **E = East**，往东边开一个窗口，也就是左右分        |
| `Ctrl + Shift + O` | 水平分屏，上下分        | **O = Over / Under**，上下叠放           |
| `Ctrl + Shift + S` | 隐藏/显示滚动条        | **S = Scrollbar**，滚动条               |
| `F11`              | 全屏/退出全屏         | **F = Fullscreen**，F11 本来就是很多软件的全屏键 |
| `Ctrl + Tab`       | 在不同 pane 之间切换   | 跟浏览器切 Tab 类似，循环切换                   |
| `Ctrl + L`         | 清屏              | **L = clear 里的 L**，也可以理解成“拉到新页面”    |
| `Ctrl + Shift + W` | 关闭当前 pane       | **W = Window**，关当前小窗口               |
| `Ctrl + Shift + Q` | 退出整个 Terminator | **Q = Quit**，退出程序                   |

因为他本身的字体非常暗,所以要设置一下.

```bash
cp ~/.config/terminator/config ~/.config/terminator/config.bak
vim ~/.config/terminator/config
```

按一下`ggdG`,注意大小写.
然后按下`insert`按键,
然后让文件内容为:

```bash
[global_config]
  title_transmit_bg_color = "#31363b"
  title_transmit_fg_color = "#fcfcfc"
  title_receive_bg_color = "#232629"
  title_receive_fg_color = "#fcfcfc"
  title_inactive_bg_color = "#232629"
  title_inactive_fg_color = "#bdc3c7"

[keybindings]

[profiles]
  [[default]]
    use_theme_colors = False
    use_system_font = False
    font = Noto Sans Mono 11

    background_type = solid
    background_color = "#232629"
    foreground_color = "#fcfcfc"
    cursor_color = "#fcfcfc"

    palette = "#232629:#ed1515:#11d116:#f67400:#1d99f3:#9b59b6:#1abc9c:#fcfcfc:#7f8c8d:#ff5555:#50fa7b:#fdbc4b:#3daee9:#ff79c6:#8be9fd:#ffffff"

    scrollback_infinite = True

[layouts]
  [[default]]
    [[[window0]]]
      type = Window
      parent = ""
      size = 920, 660
    [[[child1]]]
      type = Terminal
      parent = window0

[plugins]
```

最后,按下`ESC`,输入`:wq`保存并退出.

然后完全关闭 Terminator，再重新打开。
这个配置会做几件事：

1. 背景：接近 Konsole Breeze 的深灰，不是死黑
2. 字体：接近纯白，比以前亮很多
3. 蓝色：换成更亮的 KDE 蓝
4. 绿色/黄色/青色：也整体提亮
5. 顶部红色标题条：改成灰色，不再那么刺眼

##### Warp Terminal（推荐，更好看，也支持分屏）

相比 Terminator，Warp Terminal 的默认外观更现代，字体更清楚，界面也更亮堂。

最重要的是，它也支持类似 Terminator 的多 pane 分屏，所以可以作为 Terminator 的现代平替。

不过需要注意，Warp 的快捷键和 Terminator 不完全一样。

1. 安装

   1. Ubuntu / Debian 安装

      ```bash
      wget -O warp-terminal.deb "https://app.warp.dev/download?package=deb"
      sudo apt install ./warp-terminal.deb
      ```

      安装完成后，可以在应用菜单里打开 Warp，也可以在终端里输入：

      ```bash
      warp-terminal
      ```

   2. Fedora / RHEL 安装

      如果你用的是 Fedora、RHEL、CentOS 这类发行版，可以安装 rpm 包：

      ```bash
      wget -O warp-terminal.rpm "https://app.warp.dev/download?package=rpm"
      sudo dnf install ./warp-terminal.rpm
      ```

      启动方式同样是：

      ```bash
      warp-terminal
      ```



2. Warp 常用快捷键

| 快捷键 | 功能 | 记忆方法 |
|---|---|---|
| `Ctrl + Shift + D` | 向右分屏 | **D = Divide**，分割出一个新的 pane，默认往右边开 |
| `Ctrl + Shift + E` | 向下分屏 | **E = Extend Down**，向下面扩展一个新的 pane |
| `Ctrl + Alt + 方向键` | 按方向切换 pane | 想去哪边就按哪个方向键 |
| `Ctrl + Shift + [` | 切换到上一个 pane | `[` 可以理解成往前切 |
| `Ctrl + Shift + ]` | 切换到下一个 pane | `]` 可以理解成往后切 |
| `Ctrl + Shift + Enter` | 最大化/还原当前 pane | Enter 进入当前 pane 的专注模式 |
| `Ctrl + Shift + W` | 关闭当前 pane | **W = Window**，关闭当前小窗口 |
| `Ctrl + Shift + T` | 新建标签页 | **T = Tab**，新开一个标签页 |
| `Ctrl + PageUp` | 切换到上一个标签页 | 类似浏览器切换标签 |
| `Ctrl + PageDown` | 切换到下一个标签页 | 类似浏览器切换标签 |
| `Ctrl + L` | 清屏 | **L = clear 里的 L**，相当于翻到一个新页面 |
| `Ctrl + =` | 放大字体 | 等号旁边通常也是加号，表示放大 |
| `Ctrl + -` | 缩小字体 | 减号，表示缩小 |
| `Ctrl + 0` | 恢复默认字体大小 | 归零，恢复默认 |



3. Warp 的特点

   1. 默认颜值比 Terminator 高，不需要额外折腾配色
   2. 支持一直分屏，适合大屏幕开发
   3. 字体渲染比较清楚，观感比 Terminator 亮堂
   4. 支持命令块，长命令输出看起来更清晰
   5. 支持 AI 功能，但不使用 AI 也可以当普通终端使用

首次打开 Warp 时可能需要联网初始化。之后即使离线也可以作为普通终端使用，只是 AI、协作等云端功能不能用。

如果只是想要一个传统、稳定、轻量的终端，可以继续用 Terminator。

如果想要更好看的界面、更现代的交互体验，并且也需要分屏，那么 Warp 是一个很不错的选择。


#### git
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image215.webp)

咱们的Git入门教程：[Vinci机器人队Git入门教程](/wiki/2023-12-29-git-jiao-xue)

### ROS2体系框架
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image216.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image217.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image218.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image219.webp)

Client Library就是ROS2的客户端，比如rclcpp，rclpy。

Abstract DDS Layer是DDS抽象层，这样DDS可以实现可插拔，可以随便替换DDS模块。

Intra-process API是进程内通讯API，可以提高通信效率的一类API。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image220.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image221.webp)
