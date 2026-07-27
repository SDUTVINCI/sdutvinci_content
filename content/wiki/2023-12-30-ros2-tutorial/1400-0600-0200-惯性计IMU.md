---
title: "惯性计IMU"
---

### 简介
**概念**

IMU是惯性测量单元（Inertial Measurement Unit）的缩写，它是一种集成了多个惯性传感器如加速度计、陀螺仪、磁力计等的导航传感器。IMU可以实时地测量和记录其所在物体的加速度、角速度和方向等信息，并通过信号处理和数学模型来计算出物体的姿态、位置和运动状态等。

IMU（惯性测量单元）主要由以下几种类型组成：

1.  加速度计（Accelerometer）：用于测量物体的加速度，通常以三个轴向（X、Y、Z）提供加速度数据。加速度计可以帮助判断物体的线性运动和动作姿态变化。

2.  陀螺仪（Gyroscope）：用于测量物体的角速度或角度变化率。陀螺仪可以提供关于物体旋转的信息，判断物体的角度、方向和转动动作。

3.  磁力计（Magnetometer）：用于测量物体周围的磁场强度和方向（即测量场强）。磁力计可以帮助判断物体的方向和导航，尤其在地磁定位和航向控制中起重要作用。

这些传感器通常集成在一起形成IMU，并通过数据融合算法将它们的输出结合起来，提供综合的姿态、位置和运动信息。

**作用**

IMU是一种重要的传感器组件，它在姿态感知、行驶状态监测、非GPS定位中发挥着重要的作用。

**特点**

惯性测量单元（IMU）作为一种常见的传感器，在机器人领域中也有许多应用，下面是IMU在机器人领域的一些优点和缺点：

（1）优点：

*   **实时性高：** IMU能够以很高的采样频率获取姿态和加速度数据，提供实时的运动信息。

*   **精度较高：** IMU可以测量物体的加速度和角速度，经过合成和滤波处理后，可提供较高精度的姿态估计和运动跟踪。

*   **不受光照条件限制：** IMU不依赖于光照条件，适用于各种环境，如室内、室外和低光条件。

*   **尺寸小巧：** IMU通常具有小型、轻量级和紧凑的设计，适合嵌入式系统和对空间要求较高的机器人平台。

*   **低功耗：** IMU的功耗相对较低，适合资源受限或需要长时间运行的应用。

（2）缺点：

*   **累积误差：** 由于积分计算的误差会随时间累积，IMU的姿态估计随着时间推移可能会出现漂移，需要与其他传感器进行融合或采用校准方法进行补偿。

*   **受外部干扰影响：** IMU的测量结果容易受到振动、震动和温度变化等外界干扰的影响，可能导致姿态估计不稳定或不准确。

*   **不适用于绝对定位：** 单独使用IMU无法提供物体的绝对位置信息，需要结合其他传感器（如GNSS）来实现绝对定位。

*   **无法感知环境信息：** IMU只能提供自身的加速度和角速度数据，无法直接感知环境或检测周围物体。

综上所述，IMU在机器人领域具有实时性高、精度较高、不受光照条件限制、尺寸小巧和低功耗等优点。然而，它也存在累积误差、受外部干扰影响、不适用于绝对定位和无法感知环境信息等缺点。因此，在设计机器人系统时需综合考虑应用需求，并与其他传感器（如相机、激光雷达等）进行融合，以提升机器人的感知、定位和控制能力。

### 消息接口
在ROS2中imu消息被封装为了`sensor_msgs/msg/Imu`接口，可通过指令`ros2 interface show sensor_msgs/msg/Imu`查看该接口的具体数据结构，结果如下：

```bash

# 这是一个从惯性测量单元（Inertial Measurement Unit，简称IMU）获取数据的消息。
#

# 加速度应该以米/秒^2为单位（不是以g为单位），旋转速度应该以弧度/秒为单位。
#

# 如果测量的协方差已知，则应该填写相应值。全零的协方差矩阵将被解释为“协方差未知”，必须从其他源获取协方差才能使用协方差数据。
#

# 如果没有其中一个数据元素的估计值（例如，你的IMU不会产生方向估计），请将相关协方差矩阵的第 0 个元素设置为 -1。

# 如果你正在解析此消息，请检查每个协方差矩阵的第一个元素中是否有 -1 的值，并忽略相关的估计值。

std_msgs/Header header # 标头
    builtin_interfaces/Time stamp # 时间戳
        int32 sec
        uint32 nanosec
    string frame_id # imu坐标系

geometry_msgs/Quaternion orientation # 四元数姿态
    float64 x 0
    float64 y 0
    float64 z 0
    float64 w 1
float64[9] orientation_covariance # 姿态协方差矩阵

geometry_msgs/Vector3 angular_velocity # 三轴角速度
    float64 x
    float64 y
    float64 z
float64[9] angular_velocity_covariance # 角速度协方差矩阵

geometry_msgs/Vector3 linear_acceleration # 三轴线性加速度
    float64 x
    float64 y
    float64 z
float64[9] linear_acceleration_covariance # 线性加速度协方差矩阵
```

### 功能包创建
首先先创建功能包

```bash
ros2 pkg create imu_plumb --build-type ament_cmake --dependencies rclcpp sensor_msgs --node-name imu_node
```

先编译一下

```bash
colcon build --cmake-args -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
```

### 节点主要逻辑
```cpp
#include "rclcpp/rclcpp.hpp"
#include "serial_driver/serial_driver.hpp"
#include "cyber_imu/WitStandardProtocol.h"
#include "sensor_msgs/msg/imu.hpp"
#include <cstdint>
#include <rclcpp/logging.hpp>
#include <sensor_msgs/msg/detail/imu__struct.hpp>
#include <tf2/LinearMath/Quaternion.h>

class ImuNode: public rclcpp::Node
{
  public:
    ImuNode():Node("ImuNode_node_cpp")
    {
      RCLCPP_INFO(this->get_logger(),"惯性计imu节点启动!");

      //创建odom话题通信发布方
      imu_pub_ = this->create_publisher<sensor_msgs::msg::Imu>("/imu",10);

    // 声明参数（带默认值）
    // 串口默认设备名（根据实际设备调整）
    this->declare_parameter("device_name", "/dev/ttyUSB0");
    //串口默认波特率
    this->declare_parameter("baud_rate", 115200);

    // 获取参数值
    const std::string device_name = this->get_parameter("device_name").as_string();
    const uint32_t baud_rate = this->get_parameter("baud_rate").as_int();

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

      async_receive_message();   //进入异步接收
    }

  private:
    rclcpp::Publisher<sensor_msgs::msg::Imu>::SharedPtr imu_pub_;

    std::shared_ptr<drivers::serial_driver::SerialDriver> serial_driver_;
    std::shared_ptr<drivers::common::IoContext> io_context_;

    void async_receive_message()  //创建一个函数更方便重新调用
    {
      auto port = serial_driver_->port();

      // 设置接收回调函数
      port->async_receive([this](const std::vector<uint8_t> &data,const size_t &size) 
      {
          if (size > 0)
          {
            RCLCPP_DEBUG(this->get_logger(),"\n");
            RCLCPP_DEBUG(this->get_logger(),"以下是接收到的IMU惯性计数据:");
            //创建imu消息类型
            auto imu_msg = sensor_msgs::msg::Imu();
            bool valid_frame = false;  // 添加有效帧标志
            bool valid_data = false;  // 添加有效帧标志

            for(int16_t i = 0;i < (int16_t)size;++i)
            {
              uint8_t data_buffer = data[i];
              // RCLCPP_INFO(this->get_logger(),"接收到的字节：%x",data_buffer);
              // 处理接收到的数据
              if(wit_imu_.rx.Data_Analysis(&data_buffer) == true)
              {
                valid_frame = true;  // 标记有有效帧需要处理
              }

              if(valid_frame == true)
              {
                if(wit_imu_.rx.data.cmd == 0x51)
                {
                  // 存储加速度计数据
                  wit_imu_.rx.data.imu.Accel.X = wit_imu_.rx.data.int16_buffer[0];
                  wit_imu_.rx.data.imu.Accel.Y = wit_imu_.rx.data.int16_buffer[1];
                  wit_imu_.rx.data.imu.Accel.Z = wit_imu_.rx.data.int16_buffer[2];
                  wit_imu_.rx.data.imu.Temp    = wit_imu_.rx.data.int16_buffer[3];

                  //加速度单位m/s2，温度单位℃摄氏度
                  wit_imu_.rx.data.imu.Accel.X = wit_imu_.rx.data.imu.Accel.X / 32768.0f * 16.0f * 9.8f;
                  wit_imu_.rx.data.imu.Accel.Y = wit_imu_.rx.data.imu.Accel.Y / 32768.0f * 16.0f * 9.8f;
                  wit_imu_.rx.data.imu.Accel.Z = wit_imu_.rx.data.imu.Accel.Z / 32768.0f * 16.0f * 9.8f;
                  wit_imu_.rx.data.imu.Temp    =  wit_imu_.rx.data.imu.Temp / 100.0f;

                  // 打印加速度计数据
                  RCLCPP_DEBUG(this->get_logger(), "加速度(XYZ): %.6f, %.6f, %.6f",
                              wit_imu_.rx.data.imu.Accel.X, wit_imu_.rx.data.imu.Accel.Y, wit_imu_.rx.data.imu.Accel.Z);
                  //减少时间戳带来的延迟
                  imu_msg.header.stamp = this->get_clock()->now();
                }

                else if(wit_imu_.rx.data.cmd == 0x52)
                {
                  // 存储陀螺仪数据
                  wit_imu_.rx.data.imu.Gyro.X = wit_imu_.rx.data.int16_buffer[0];
                  wit_imu_.rx.data.imu.Gyro.Y = wit_imu_.rx.data.int16_buffer[1];
                  wit_imu_.rx.data.imu.Gyro.Z = wit_imu_.rx.data.int16_buffer[2];

                  //角速度单位rad/s
                  wit_imu_.rx.data.imu.Gyro.X = wit_imu_.rx.data.imu.Gyro.X / 32768.0f * 2000.0f * (M_PI / 180.0f);
                  wit_imu_.rx.data.imu.Gyro.Y = wit_imu_.rx.data.imu.Gyro.Y / 32768.0f * 2000.0f * (M_PI / 180.0f);
                  wit_imu_.rx.data.imu.Gyro.Z = wit_imu_.rx.data.imu.Gyro.Z / 32768.0f * 2000.0f * (M_PI / 180.0f);

                  // 打印陀螺仪数据
                  RCLCPP_DEBUG(this->get_logger(), "角速度(XYZ): %.6f, %.6f, %.6f",
                              wit_imu_.rx.data.imu.Gyro.X, wit_imu_.rx.data.imu.Gyro.Y, wit_imu_.rx.data.imu.Gyro.Z);
                }

                else if(wit_imu_.rx.data.cmd == 0x54)
                {
                  // 存储磁力计数据(单位lsb)
                  wit_imu_.rx.data.imu.Magnet.X = wit_imu_.rx.data.int16_buffer[0];
                  wit_imu_.rx.data.imu.Magnet.Y = wit_imu_.rx.data.int16_buffer[1];
                  wit_imu_.rx.data.imu.Magnet.Z = wit_imu_.rx.data.int16_buffer[2];

                  //磁场单位uT
                  // wit_imu_.rx.data.imu.Magnet.X = wit_imu_.rx.data.imu.Magnet.X / 1.0f;
                  // wit_imu_.rx.data.imu.Magnet.Y = wit_imu_.rx.data.imu.Magnet.Y / 1.0f;
                  // wit_imu_.rx.data.imu.Magnet.Z = wit_imu_.rx.data.imu.Magnet.Z / 1.0f;

                  // 打印磁力计数据
                  RCLCPP_DEBUG(this->get_logger(), "磁场(XYZ): %.6f, %.6f, %.6f",
                              wit_imu_.rx.data.imu.Magnet.X, wit_imu_.rx.data.imu.Magnet.Y, wit_imu_.rx.data.imu.Magnet.Z);
                }

                else if(wit_imu_.rx.data.cmd == 0x53)
                {
                  // 存储欧拉角数据
                  wit_imu_.rx.data.imu.Euler.roll = wit_imu_.rx.data.int16_buffer[0];
                  wit_imu_.rx.data.imu.Euler.pitch = wit_imu_.rx.data.int16_buffer[1];
                  wit_imu_.rx.data.imu.Euler.yaw = wit_imu_.rx.data.int16_buffer[2];

                  //欧拉角单位dec
                  wit_imu_.rx.data.imu.Euler.roll = wit_imu_.rx.data.imu.Euler.roll / 32768.0f * 180.0f;
                  wit_imu_.rx.data.imu.Euler.pitch = wit_imu_.rx.data.imu.Euler.pitch / 32768.0f * 180.0f;
                  wit_imu_.rx.data.imu.Euler.yaw = wit_imu_.rx.data.imu.Euler.yaw / 32768.0f * 180.0f;

                  // 打印欧拉角数据
                  RCLCPP_DEBUG(this->get_logger(), "欧拉角(XYZ_RPY): %.6f, %.6f, %.6f",
                              wit_imu_.rx.data.imu.Euler.roll, wit_imu_.rx.data.imu.Euler.pitch, wit_imu_.rx.data.imu.Euler.yaw);

                  //欧拉角单位rad
                  wit_imu_.rx.data.imu.Euler.roll = wit_imu_.rx.data.imu.Euler.roll * (M_PI / 180.0f);
                  wit_imu_.rx.data.imu.Euler.pitch = wit_imu_.rx.data.imu.Euler.pitch  * (M_PI / 180.0f);
                  wit_imu_.rx.data.imu.Euler.yaw = wit_imu_.rx.data.imu.Euler.yaw  * (M_PI / 180.0f);
                }

                else if(wit_imu_.rx.data.cmd == 0x59)
                {
                  for(int16_t i = 0 ; i < 4 ; i++)
                  {
                    // 存储四元数数据
                    wit_imu_.rx.data.imu.Quat.q[i] = wit_imu_.rx.data.int16_buffer[i];

                    //四元数是归一化的四元数
                    wit_imu_.rx.data.imu.Quat.q[i] = wit_imu_.rx.data.imu.Quat.q[i] / 32768.0f;
                  }

                  // 打印四元数数据
                  //注意，0对应y，1对应x，-2才对应z。
                  RCLCPP_DEBUG(this->get_logger(), "四元数(xyzw): %.6f, %.6f, %.6f, %.6f",
                              wit_imu_.rx.data.imu.Quat.q[1],wit_imu_.rx.data.imu.Quat.q[0],-wit_imu_.rx.data.imu.Quat.q[2],wit_imu_.rx.data.imu.Quat.q[3]);
                  valid_data = true;
                }
                wit_imu_.rx.data.cmd = 0x00;   //跑过一次就进行清0
                valid_frame = false;
              }
            }

            if(valid_data == true)
            {
              //时间戳
              // imu_msg.header.stamp = this->get_clock()->now();
              //位姿信息所参考的坐标系
              imu_msg.header.frame_id = "imu";

              // tf2::Quaternion q;
              // //DOF欧拉角单位rad
              // q.setRPY(roll_, pitch_, yaw_);
              // //DOF欧拉角（四元数）
              // imu_msg.orientation.x = q.x();
              // imu_msg.orientation.y = q.y();
              // imu_msg.orientation.z = q.z();
              // imu_msg.orientation.w = q.w();

              //DOF欧拉角（四元数）
              //注意对应关系
              // 传感器坐标系 -> ROS坐标系：
              // X -> Y
              // Y -> X
              // Z -> -Z
              imu_msg.orientation.x =   wit_imu_.rx.data.imu.Quat.q[1];
              imu_msg.orientation.y =   wit_imu_.rx.data.imu.Quat.q[0];
              imu_msg.orientation.z = - wit_imu_.rx.data.imu.Quat.q[2];
              imu_msg.orientation.w =   wit_imu_.rx.data.imu.Quat.q[3];

              //加速度
              imu_msg.linear_acceleration.x = wit_imu_.rx.data.imu.Accel.X;
              imu_msg.linear_acceleration.y = wit_imu_.rx.data.imu.Accel.Y;
              imu_msg.linear_acceleration.z = wit_imu_.rx.data.imu.Accel.Z;

              //角速度
              imu_msg.angular_velocity.x = wit_imu_.rx.data.imu.Gyro.X;
              imu_msg.angular_velocity.y = wit_imu_.rx.data.imu.Gyro.Y;
              imu_msg.angular_velocity.z = wit_imu_.rx.data.imu.Gyro.Z;

              imu_pub_->publish(imu_msg);
              valid_data = false;
            }

          }
          // 继续监听新的数据
          async_receive_message();
      }
      );
    }
};

int main(int argc, char ** argv)
{
  rclcpp::init(argc,argv);

  auto node = std::make_shared<ImuNode>();

  rclcpp::spin(node);

  rclcpp::shutdown();
  return 0;
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1800.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1801.webp)

这种框架对于一个250Hz（每4ms输出一次）的陀螺仪来说,最后/imu的发布频率是100Hz（每10ms发布一次）,效率也是还可以接受的.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1802.webp)
