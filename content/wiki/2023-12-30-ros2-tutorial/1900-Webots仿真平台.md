---
title: "Webots仿真平台"
---

这个平台用来测运动学很舒服。

可以SolidWorks转URDF（ROS官方提供sw2udfo工具），再URDF转Webots（webots官方提供urdf2webots工具）。

https://github.com/ros/solidworks_urdf_exporter

https://github.com/cyberbotics/urdf2webots

这样的话，连电机甚至都给你选好了，你只需要写运动学即可。

运动控制程序可以用C/C++、Python、Java等语言编写，控制的时候只需要注意一下控制电机角度和速度的方式以及单位即可。

如果你给电机setPosition(INFINITY)的话，那么这个电机将变成角度电机，而原本设置速度的函数将会变成设置角速度。

如果你没给电机设置setPosition(INFINITY)，那么这个电机就是个速度电机。

角度单位是rad（存疑）

速度单位是rad/s（存疑）

```cpp
        for (int i = 0;i < 4;i++)
        {
                //舵电机仿真中默认顺时针为正
                swerve_motor[i] = robot->getMotor("swerve_joint" + std::to_string(i));
                //swerve_motor[i]->setPosition(INFINITY);       // 注释掉：不启用速度控制
                swerve_motor[i]->setPosition(0.0);       // 初始角度为0
                swerve_motor[i]->setVelocity(50.0);       // 设置角速度

                //驱动电机仿真中默认向后滚为正
                drive_motor[i] = robot->getMotor("drive_joint" + std::to_string(i));
                drive_motor[i]->setPosition(INFINITY);   // 启用速度控制
                drive_motor[i]->setVelocity(-0.0);        // 初始速度为0
        }
```
