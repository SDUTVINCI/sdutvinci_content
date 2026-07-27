---
title: "里程计Odom"
---

### 功能包创建
不一定非得要码盘(里程计Odom)的数据，只要把机器人6个自由度DOF，可以由电机编码器计算而来（x,y,z,yaw,pitch,roll不懂的看上方Moveit2里的教学）全部发过来就行。

首先先创建功能包

```bash
ros2 pkg create odom_plumb --build-type ament_cmake --dependencies rclcpp nav_msgs geometry_msgs tf2_ros --node-name odom_node
```

先编译一下

```bash
colcon build --cmake-args -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
```

### 消息接口
底盘驱动启动之后，会持续广播一个比较重要的数据——里程计。里程计用于描述当前机器人相对于出发点的位姿以及速度等信息，里程计在机器人定位和导航的局部路径规划中有着重要的作用。在ROS2中里程计消息被封装为了`nav_msgs/msg/Odometry`接口，可通过指令`ros2 interface show nav_msgs/msg/Odometry`查看该接口的具体数据结构，结果如下：

它描述的是机器人位姿与速度的预估信息。 其中位姿信息是以header中的frame_id为参考系的，而速度信息则是以child_frame_id为参考系的。

在 **ROS2** 中， **odom 坐标系** （通常是 `odom` frame）遵循 **右手坐标系** ，其定义如下：

*   **X 轴** ：向前（前进方向）为 **正**

*   **Y 轴** ：向左（左侧方向）为 **正**

*   **Z 轴** ：向上（垂直地面）为 **正**

在 **ROS2 的 odom 坐标系** （右手坐标系）中， **yaw 角的定义** 如下：

*   **yaw = 0** ：机器人面向 **X 轴正方向** （向前）。

*   **逆时针（CCW，Counter Clockwise）旋转为正** 。

*   **顺时针（CW，Clockwise）旋转为负** 。

**yaw 角范围**

*   `yaw ∈ (-π, π]` **（即 `-180°` 到 `180°`）**

*   当 `yaw = π`（180°），如果继续增加，应该跳转到 `-π`（-180°），而不是 `π`。

*   当 `yaw = -π`（-180°），如果继续减少，应该跳转到 `π`（180°）。

```bash
std_msgs/Header header # 标头
    builtin_interfaces/Time stamp # 时间戳
        int32 sec
        uint32 nanosec
    string frame_id # 位姿信息所参考的坐标系
位姿信息所指向的坐标系，也是速度信息所参考的坐标系
string child_frame_id
相对于frame_id的预估位姿信息
geometry_msgs/PoseWithCovariance pose
    Pose pose
        Point position # 坐标
            float64 x
            float64 y
            float64 z
        Quaternion orientation # 四元数
            float64 x 0
            float64 y 0
            float64 z 0
            float64 w 1
    float64[36] covariance
相对于child_frame_id的预估线速度与角速度
geometry_msgs/TwistWithCovariance twist
    Twist twist
        Vector3  linear # 线速度
            float64 x
            float64 y
            float64 z
        Vector3  angular # 角速度
            float64 x
            float64 y
            float64 z
    float64[36] covariance
```

需要注意的是，

1.  坐标和位姿是相对于**frame_id**的，一般是odom自己。

2.  而线速度和角速度是相对于**child_frame_id**的。一般是base_link **。** （这里注意，学长我最初就弄错了）

3.  虽然Twist是相对于child_frame_id **（** base_link）的，但是他表达的是**瞬时值**，就和汽车一样，汽车上面的速度表也是相对于车身的**瞬时值**。

4.  里程计在计算时是存在累计误差的，它所描述的是机器人的预估位姿，并不绝对准确。

### 节点主要逻辑
所以我们需要初始化`nav_msgs::msg::Odometry`并发布出去。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1794.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1795.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1796.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1797.webp)

所以你需要从MCU单片机上去获取这些数据，或者计算这些数据，并以`nav_msgs::msg::Odometry`类型的消息发送出去。

注意，如果你没有正儿八经的里程计，那么里程计的数据可以由底盘电机编码器数据计算而成。

除了发布话题的代码，别忘记发布odom与base_link之间的动态坐标变换。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1798.webp)

下方是运动学正解算出来的odom数据:

```cpp
#include "odom_mg513.h"
#include "mg513_gmr500ppr.h"
//#include <cmath>
#include "arm_math.h"

ODOM_Motor odom_motor_;

// 坐标系满足右手坐标系

//（单位：米）
//轮距
extern fp32 Wheel_Spacing;
//轴距
extern fp32 Alex_Spacing;
//轮子半径
extern fp32 Wheel_Radius;

void ODOM_Motor::Analysis(fp32 dt)
{
  this->dt = dt;
  for(int16_t i = 0;i < 4;i++)
    {
        // 将 RPM 转换为 m/s
      encoder_wheel_velocities_[i] = mg513_gmr500ppr_motor[i].encoder.motor_data.motor_speed  * 2.0f * 3.14159265358979f * Wheel_Radius / 60.0f;
    }
    // 计算机器人前进的线速度和角速度，公式不需要轮半径
    //线速度，四个轮子在机器人的运动学模型中贡献相同，所以要除以4
    this->vx = ( encoder_wheel_velocities_[0] + encoder_wheel_velocities_[1] - encoder_wheel_velocities_[2] - encoder_wheel_velocities_[3]) / 4.0f;
    this->vy = ( encoder_wheel_velocities_[0] - encoder_wheel_velocities_[1] - encoder_wheel_velocities_[2] + encoder_wheel_velocities_[3]) / 4.0f;

    //线速度，轮距（wheel_spacing） 决定了左右轮对旋转的贡献程度，轴距（alex_spacing） 决定了前后轮对旋转的贡献程度，
    //所以要除以底盘尺寸，alex_spacing + wheel_spacing 是底盘尺寸。
    this->vw = (-encoder_wheel_velocities_[0] - encoder_wheel_velocities_[1] - encoder_wheel_velocities_[2] - encoder_wheel_velocities_[3]) / (4.0f * (Wheel_Spacing + Alex_Spacing));

    // 更新机器人的位置（假设机器人沿着y轴移动）
//     this->x_position += this->vx * std::__math::cos(this->yaw) * this->dt;  
//     this->y_position += this->vy * std::__math::sin(this->yaw) * this->dt;
    this->x_position += this->vx * arm_cos_f32(this->yaw) * this->dt;  
    this->y_position += this->vy * arm_sin_f32(this->yaw) * this->dt;
    this->yaw += this->vw * this->dt;

    // 保证 yaw 始终在 -PI 到 PI 之间
    if(this->yaw > 3.14159265358979f) 
    {
      this->yaw -= 2.0 * 3.14159265358979f;
    }
    if(this->yaw < -3.14159265358979f) 
    {
      this->yaw += 2.0 * 3.14159265358979f;
    }

//    this->yaw_deg = this->yaw *  180.0f / 3.14159265358979f;

}

```

ROS2总代码如下：

```cpp
#include "rclcpp/rclcpp.hpp"
#include "serial_driver/serial_driver.hpp"
#include <chrono>
#include <functional>
#include <rclcpp/logging.hpp>
#include <string>
#include "sensor04_odom_kc/serial_pack.h"
#include "geometry_msgs/msg/twist.hpp"
#include "nav_msgs/msg/odometry.hpp"
#include <tf2/LinearMath/Quaternion.h>
#include <tf2_ros/transform_broadcaster.h>

extern std::vector<uint8_t> tx_data_buffer;

class Odom_KC_Node : public rclcpp::Node
{
public:
  Odom_KC_Node() : Node("Odom_KC_Node")
  {
    // 声明参数（带默认值）
    // 串口设备默认名（根据实际设备调整）
    this->declare_parameter("device_name", "/dev/ttyACM1");
    //串口默认波特率
    this->declare_parameter("baud_rate", 115200);

    // 获取参数值
    const std::string device_name = this->get_parameter("device_name").as_string();
    const uint32_t baud_rate = this->get_parameter("baud_rate").as_int();

    RCLCPP_INFO(this->get_logger(), "Serial port Node Open!");
    // 创建串口配置对象
    // 波特率默认115200；不开启流控制；无奇偶效验；停止位1。
    drivers::serial_driver::SerialPortConfig config(
        baud_rate,
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

    //串口发布方
    odom_pub_ = this->create_publisher<nav_msgs::msg::Odometry>("odom",10);

    //初始化tf广播器
    tf_broadcaster_ = std::make_unique<tf2_ros::TransformBroadcaster>(*this);

    //初始化cmd_vel消息订阅
    cmd_vel_sub_ = this->create_subscription<geometry_msgs::msg::Twist>("cmd_vel",10,std::bind(&Odom_KC_Node::cmd_vel_sub_callback,this,std::placeholders::_1));

    async_receive_message();   //进入异步接收
  }

private:
  std::shared_ptr<drivers::serial_driver::SerialDriver> serial_driver_;
  std::shared_ptr<drivers::common::IoContext> io_context_;

  rclcpp::Publisher<nav_msgs::msg::Odometry>::SharedPtr odom_pub_;
  std::unique_ptr<tf2_ros::TransformBroadcaster> tf_broadcaster_;

  rclcpp::Subscription<geometry_msgs::msg::Twist>::SharedPtr cmd_vel_sub_;

  // // 四个轮子的速度(对应单片机的顺序)（单位：rpm）
  // fp32 received_encoder_wheel_velocities_[4] = {0.0, 0.0, 0.0, 0.0};
  // // 四个轮子的速度(对应单片机的顺序)（单位：m/s）
  // fp32 encoder_wheel_velocities_[4] = {0.0, 0.0, 0.0, 0.0};  

  // // 四个轮子的速度(对应单片机的顺序)（单位：2000pc）
  // fp32 received_encoder_wheel_angle_[4] = {0.0, 0.0, 0.0, 0.0};

  //麦克纳姆轮底盘参数
  const fp32 wheel_spacing = 0.093; // 轮间距（单位：米）
  const fp32 alex_spacing = 0.085; // 轮距（单位：米）
  const fp32 wheel_radius_ = 0.0375; // 轮子半径（单位：米）

  fp32 vy=0.0,vx=0.0,vw=0.0;
  fp32 yaw_ = 0.0; // 机器人航向角（单位：弧度）
  fp32 dt_;

  fp32 x_position_ = 0.0; // x位置
  fp32 y_position_ = 0.0; // y位置

  void async_receive_message()  //创建一个函数更方便重新调用
  {
    auto port = serial_driver_->port();

    // 设置接收回调函数
    port->async_receive([this](const std::vector<uint8_t> &data,const size_t &size) 
    {
      //创建odom消息类型
      auto odom_msg = nav_msgs::msg::Odometry();

        if (size > 0)
        {
          for(int16_t i = 0;i < (int16_t)size;++i)
          {
            uint8_t data_buffer = data[i];
            // 处理接收到的数据
            serial_pack_.rx.Data_Analysis(
              &data_buffer,
              0x01,
            0,
            0,
            0,
            0,
            8);
          }
          //由于在ROS2中，node是局部变量，所以发布方只能在node类里，故Data_Apply不写任何东西，直接在接收下面的回调函数里实现功能。
          if(serial_pack_.rx.data.cmd == 0x01)
          {
            RCLCPP_DEBUG(this->get_logger(), "\n");
            RCLCPP_DEBUG(this->get_logger(), "以下是电机编码器的odom数据：");

            // // 存储电机速度数据
            // received_encoder_wheel_velocities_[0] = serial_pack_.rx.data.fp32_buffer[0];  // 电机 0 速度
            // received_encoder_wheel_velocities_[1] = serial_pack_.rx.data.fp32_buffer[1];  // 电机 1 速度
            // received_encoder_wheel_velocities_[2] = serial_pack_.rx.data.fp32_buffer[2];  // 电机 2 速度
            // received_encoder_wheel_velocities_[3] = serial_pack_.rx.data.fp32_buffer[3];  // 电机 3 速度

            // // 存储电机位置数据
            // received_encoder_wheel_angle_[0] = serial_pack_.rx.data.fp32_buffer[4];  // 电机 0 位置
            // received_encoder_wheel_angle_[1] = serial_pack_.rx.data.fp32_buffer[5];  // 电机 1 位置
            // received_encoder_wheel_angle_[2] = serial_pack_.rx.data.fp32_buffer[6];  // 电机 2 位置
            // received_encoder_wheel_angle_[3] = serial_pack_.rx.data.fp32_buffer[7];  // 电机 3 位置

            // vx = serial_pack_.rx.data.fp32_buffer[8];
            // vy = serial_pack_.rx.data.fp32_buffer[9];
            // vw = serial_pack_.rx.data.fp32_buffer[10];
            // yaw_ = serial_pack_.rx.data.fp32_buffer[11];
            // dt_ = serial_pack_.rx.data.fp32_buffer[12];
            // x_position_ = serial_pack_.rx.data.fp32_buffer[13];
            // y_position_ = serial_pack_.rx.data.fp32_buffer[14];

            vx = serial_pack_.rx.data.fp32_buffer[0];
            vy = serial_pack_.rx.data.fp32_buffer[1];
            vw = serial_pack_.rx.data.fp32_buffer[2];
            yaw_ = serial_pack_.rx.data.fp32_buffer[3];
            dt_ = serial_pack_.rx.data.fp32_buffer[4];
            x_position_ = serial_pack_.rx.data.fp32_buffer[5];
            y_position_ = serial_pack_.rx.data.fp32_buffer[6];

            // 打印电机速度和位置（角度）
            for (int i = 0; i < 4; ++i) 
            {
                // RCLCPP_DEBUG(this->get_logger(), "%d号电机的速度: %.6f RPM, 位置: %.6f (2000pc)",
                //             i, received_encoder_wheel_velocities_[i], received_encoder_wheel_angle_[i]);

                RCLCPP_DEBUG(this->get_logger(),"线速度:x:%.6f,y:%.6f,z:%.6f",vx,vy,0.0f);
                RCLCPP_DEBUG(this->get_logger(),"角速度:x:%.6f,y:%.6f,z:%.6f",0.0f,0.0f,vw);
                RCLCPP_DEBUG(this->get_logger(),"欧拉角(逆正顺负):r:%.6f,p:%.6f,y:%.6f",0.0f,0.0f,yaw_);
                RCLCPP_DEBUG(this->get_logger(),"积分间隔:%.6f",dt_);
                RCLCPP_DEBUG(this->get_logger(),"右手坐标系X坐标(前正后负):%.6f",x_position_);
                RCLCPP_DEBUG(this->get_logger(),"右手坐标系Y坐标(左正右负):%.6f",y_position_);
            }

            //时间戳
            odom_msg.header.stamp = this->get_clock()->now();

            //位姿信息所参考的坐标系
            odom_msg.header.frame_id = "odom";

            // 设置child_frame_id（底盘坐标系）
            odom_msg.child_frame_id = "base_link";  // 设置子坐标系为机器人底盘坐标系

            //DOF平动位置
            odom_msg.pose.pose.position.x = x_position_;
            odom_msg.pose.pose.position.y = y_position_;
            odom_msg.pose.pose.position.z = 0.0;

            //从欧拉角转换为四元数
            tf2::Quaternion q;
            q.setRPY(0,0,yaw_);
            //DOF转动（四元数）
            odom_msg.pose.pose.orientation.x = q.x();
            odom_msg.pose.pose.orientation.y = q.y();
            odom_msg.pose.pose.orientation.z = q.z();
            odom_msg.pose.pose.orientation.w = q.w();

            //线速度
            odom_msg.twist.twist.linear.x = vx;
            odom_msg.twist.twist.linear.y = vy;
            odom_msg.twist.twist.linear.z = 0.0;

            //角速度
            odom_msg.twist.twist.angular.x = 0.0;
            odom_msg.twist.twist.angular.y = 0.0;
            odom_msg.twist.twist.angular.z = vw;

            // 发布消息
            odom_pub_->publish(odom_msg);

            // 打印位置（XYZ）
            RCLCPP_DEBUG(
              this->get_logger(),
              "位置Position(XYZ): %.6f, %.6f, %.6f",
              odom_msg.pose.pose.position.x,
              odom_msg.pose.pose.position.y,
              odom_msg.pose.pose.position.z
            );

            // 打印姿态（四元数WXYZ）
            RCLCPP_DEBUG(
              this->get_logger(),
              "姿态Orientation(WXYZ): %.6f, %.6f, %.6f, %.6f",
              odom_msg.pose.pose.orientation.w,  // 注意顺序：WXYZ
              odom_msg.pose.pose.orientation.x,
              odom_msg.pose.pose.orientation.y,
              odom_msg.pose.pose.orientation.z
            );

            // 打印姿态（欧拉角RPY）
            RCLCPP_DEBUG(
              this->get_logger(),
              "欧拉角Euler(RPY): %.6f, %.6f, %.6f",
              0.0,
              0.0,
              yaw_
            );

            // 打印线速度（XYZ）
            RCLCPP_DEBUG(
              this->get_logger(),
              "线速度LinearVel(XYZ): %.6f, %.6f, %.6f",
              odom_msg.twist.twist.linear.x,
              odom_msg.twist.twist.linear.y,
              odom_msg.twist.twist.linear.z
            );

            // 打印角速度（XYZ）
            RCLCPP_DEBUG(
              this->get_logger(),
              "角速度AngularVel(XYZ): %.6f, %.6f, %.6f",
              odom_msg.twist.twist.angular.x,
              odom_msg.twist.twist.angular.y,
              odom_msg.twist.twist.angular.z
            );

            /* 接下来发布tf */
            auto transform = geometry_msgs::msg::TransformStamped();
            transform.header.stamp = odom_msg.header.stamp; // 时间戳与odom同步
            transform.header.frame_id = "odom";
            transform.child_frame_id = "base_link";
            transform.transform.translation.x = x_position_;
            transform.transform.translation.y = y_position_;
            transform.transform.translation.z = 0.0;
            transform.transform.rotation = odom_msg.pose.pose.orientation; // 复用四元数
            tf_broadcaster_->sendTransform(transform);
          }

        }
        // 继续监听新的数据
        async_receive_message();
    }
    );
  }

  void cmd_vel_sub_callback(const geometry_msgs::msg::Twist &cmd_msg)
  {
    // bool bool_buffer[] = {1, 0, 1, 0};
    // int8_t int8_buffer[] = {0x11,0x22};
    // int16_t int16_buffer[] = {2000,6666};
    // int32_t int32_buffer[] = {305419896};
    fp32 fp32_buffer[] = {(fp32)cmd_msg.linear.x,(fp32)cmd_msg.linear.y,(fp32)cmd_msg.linear.z,
                          (fp32)cmd_msg.angular.x,(fp32)cmd_msg.angular.y,(fp32)cmd_msg.angular.z};

    //由于ROS2中node为局部变量，所以只能在node中调用send函数，所以Serial_Transmit只负责处理data_buffer。
    serial_pack_.tx.Data_Pack(0x01, 
                                 nullptr, 0,
                                 nullptr, 0,
                                 nullptr, 0,
                                 nullptr, 0,
                                 fp32_buffer, sizeof(fp32_buffer) / sizeof(fp32));

    auto port = serial_driver_->port();

    try
    {
      size_t bytes_size = port->send(tx_data_buffer);

      RCLCPP_DEBUG(this->get_logger(), "平动XYZ：%.6f,%.6f,%.6f", fp32_buffer[0],fp32_buffer[1],fp32_buffer[2]);
      RCLCPP_DEBUG(this->get_logger(), "转动XYZ：%.6f,%.6f,%.6f", fp32_buffer[3],fp32_buffer[4],fp32_buffer[5]);
      RCLCPP_DEBUG(this->get_logger(), "(%ld bytes)", bytes_size);
    }
    catch(const std::exception &ex)
    {
      RCLCPP_ERROR(this->get_logger(), "Error Transmiting from serial port:%s",ex.what());
    }
  }
};

int main(int argc, char **argv)
{
  rclcpp::init(argc, argv);

  auto node = std::make_shared<Odom_KC_Node>();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1799.webp)

最终odom有效数据应该在250Hz左右,4ms发布一次,对于导航也是完全够用的.
