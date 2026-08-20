---
vinciId: 68e08c30-a608-457a-9dd1-a2bae116d7ae
title: STM32任务目录
description: 注意事项
authors:
  - dongjiahui
contributors:
  - zhangyihao
  - zhangchangfei
  - fangzihao
  - cuigongyan
  - zhaoyouqi
  - maojingqiu
publishedAt: 2023-12-06T00:00:00.000Z
updatedAt: 2026-08-20T15:07:00.166Z
tags:
  - 嵌入式组
---

## 注意事项

1. 工程里不要包含其他库，不要包含其他不应该有的代码

2. 不要用拼音写代码

3. 时钟函数不要不改

4. 不允许直接用其他同学写的代码，连IO口都一样，概率太小了





## 任务

### 任务名称:跑马灯

### 任务要求:

点亮单片机板载的LED灯，并让他每隔500ms闪烁一次

### 元器件建议:

单片机型号:stm32f103c8t6

### 重点考察:

GPIO输出







## 任务

### 任务名称:红外循迹模块控制板载LED

### 任务要求:

当寻迹模块前方有障碍时，LED亮，前方无障碍时，LED灭

### 元器件建议:

单片机型号:stm32f103c8t6

循迹模块型号:红外寻迹\-循迹\-避障传感器模块\(https://detail\.tmall\.com/item\.htm?abbucket=12\&id=16489814035\&ns=1\&spm=a21n57\.1\.0\.0\.130f523c2ROyKn\)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/001-5aa0310dc700.webp)

### 重点考察:

GPIO输出、GPIO输入

##

## 任务

### 任务名称:红外循迹模块控制板载LED

### 任务要求:

当寻迹模块前方有障碍时，LED亮，前方无障碍时，LED灭

### 元器件建议:

单片机型号:stm32f103c8t6

循迹模块型号:红外寻迹\-循迹\-避障传感器模块

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/001-5aa0310dc700.webp)

### 重点考察:

GPIO输出、外部中断\(中断式\)



## 任务

### 任务名称:蓝牙模块控制板载LED\(阻塞式，在while1里写逻辑代码\)

### 任务要求:

用手机连接上蓝牙模块，然后通过手机操控单片机板载的LED灯亮灭（比如手机发送1，灯亮，手机发送2，灯灭）

### 元器件建议:

单片机型号:stm32f103c8t6

蓝牙模块型号:HC\-05蓝牙模块\(https://blog\.csdn\.net/qq\_51967985/article/details/125863389?spm=1001\.2014\.3001\.5506\)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/002-ca5bde4c1fc0.webp)

### 重点考察:

GPIO输出、串口\(阻塞式\)





## 任务

### 任务名称:蓝牙模块控制板载LED\(中断式，在中断回调里写逻辑代码\)

### 任务要求:

用手机连接上蓝牙模块，然后通过手机操控单片机板载的LED灯亮灭（比如手机发送1，灯亮，手机发送2，灯灭）

### 元器件建议:

单片机型号:stm32f103c8t6

蓝牙模块型号:HC\-05蓝牙模块\(https://blog\.csdn\.net/qq\_51967985/article/details/125863389?spm=1001\.2014\.3001\.5506\)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/002-ca5bde4c1fc0.webp)

### 重点考察:

GPIO输出、串口接收中断\(中断式\)





## 任务

### 任务名称:跑马灯（定时器中断的方式）

### 任务要求:

点亮单片机板载的LED灯，并让他每隔500ms闪烁一次

### 元器件建议:

单片机型号:stm32f103c8t6

### 重点考察:

GPIO输出、定时器中断\(中断式\)



## 任务

### 任务名称:舵机转动（定时器PWM）

### 任务要求:

让舵机默认先转到45度，然后过一秒后转到90度，再过一秒回到45度，再过一秒到90度，循环往复

### 元器件建议:

单片机型号:stm32f103c8t6

舵机型号:建议3\-7V的，实验室里有3\.7\-7V的，但是得外接电源

### 重点考察:

定时器PWM





## 任务

### 任务名称:蓝牙模块控制舵机

### 任务要求:

用手机输入1，舵机转到0度位置处，输入2，舵机转到90度位置处（可远程开关灯）

### 元器件建议:

单片机型号:stm32f103c8t6

舵机型号:建议3\-7V的，实验室里有3\.7\-7V的，但是得外接电源

蓝牙模块型号:HC\-05蓝牙模块\(https://blog\.csdn\.net/qq\_51967985/article/details/125863389?spm=1001\.2014\.3001\.5506\)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/002-ca5bde4c1fc0.webp)

### 重点考察:

定时器PWM、串口（阻塞式和中断式都可以）





## 任务

### 任务名称:超声波测距模块\(定时器输入捕获\)

### 任务要求:

用debug可以读取超声波测距返回的距离数值

### 元器件建议:

单片机型号:stm32f103c8t6

超声波测距型号:HC\-SR04（**老版** 定时器输入捕获版）

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/003-ce8d56eae4c1.webp)

### 重点考察:

GPIO输出、定时器输入捕获



从这里开始，训练使用CubeMX与EMIS\_Vinci机器人队C\+\+工程模板。



## 任务

### 任务名称:大疆电机开环转动\(CAN通信\)

### 任务要求:

通过单片机发送CAN通信报文，让大疆M3508电机开环转动（以大疆800分辨率的电流转动）

### 元器件建议:

单片机型号:stm32f407ih6\(大疆C板\)

电调型号:大疆C620电调或者C610电调

电机型号:大疆M3508电机\(搭配C620电调\)或者大疆M2006电机\(搭配C610电调\)或者大疆GM6020电机

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/004-5bc458036316.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/005-fb91948dab2f.webp)

###

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/006-d40c1569fbd2.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/007-f2d3b24a9a2c.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/008-5702b816e51f.webp)

### 重点考察:

CAN通信发送



## 任务

### 任务名称:读取大疆直流无刷电机编码器的值

### 任务要求:

通过单片机接收大疆电机发送给单片机的CAN通信报文，并将数据处理成 电流值、速度值、角度值、总角度值等

### 元器件建议:

单片机型号:stm32f407ih6\(大疆C板\)

电调型号:大疆C620电调或者C610电调

电机型号:大疆M3508电机\(搭配C620电调\)或者大疆M2006电机\(搭配C610电调\)或者大疆GM6020电机

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/004-5bc458036316.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/005-fb91948dab2f.webp)

###

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/006-d40c1569fbd2.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/007-f2d3b24a9a2c.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/008-5702b816e51f.webp)

### 重点考察:

CAN通信发送、CAN通信接收中断、库的调用



## 任务

### 任务名称:大疆电机负反馈转动（CAN通信\+PID算法）

### 任务要求:

单片机使用PID算法控制大疆FOC电机的转子以500rpm的速度稳定转动。

### 元器件建议:

单片机型号:stm32f407ih6\(大疆C板\)

电调型号:大疆C620电调或者C610电调

电机型号:大疆M3508电机\(搭配C620电调\)或者大疆M2006电机\(搭配C610电调\)或者大疆GM6020电机

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/004-5bc458036316.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/005-fb91948dab2f.webp)

###

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/006-d40c1569fbd2.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/007-f2d3b24a9a2c.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/008-5702b816e51f.webp)

### 重点考察:

CAN通信发送、CAN通信接收中断、库的调用、PID算法



## 任务:

### 任务名称:驱动直流有刷电机旋转\(开环\)

### 任务要求:

使直流有刷电机在pwm驱动下旋转，主要搞懂电机驱动板，电机，单片机之间的连接。

### 元器件建议:

单片机型号:stm32f103c8t6
电机型号:MG513、MG370、ATK\-JGB37\-520E
电机驱动板型号:TB6612、L298N、AT8236

### 重点考察:

GPIO输出，定时器PWM



## 任务:

### 任务名称:读取直流有刷电机编码器值

### 任务要求:

用debug可以读取直流有刷电机编码器的值，并处理为相对速度，位置等值。

### 元器件建议:

单片机型号:stm32f103c8t6
电机型号:MG513、MG370、ATK\-JGB37\-520E

电机驱动板型号:TB6612、L298N、AT8236

### 重点考察:

定时器编码器模、定时器输入捕获





## 任务:

### 任务名称:直流有刷电机PID

### 任务要求:

直流有刷电机实现pid速度环，位置环，速度位置双环等。

### 元器件建议:

单片机型号:stm32f103c8t6
电机型号:MG513、MG370、ATK\-JGB37\-520E
电机驱动板型号:TB6612、L298N、AT8236

### 重点考察:

定时器编码器模式、定时器输入捕获、定时器PWM、PID算法



## 任务:

### 任务名称:蓝牙遥控器

### 任务要求:

用Android端的app弄一个操控小车的遥控器。

要求可以给电机发送1%\-100%任意速度的指令。\(也就是要弄一个虚拟的遥杆\)

### 元器件建议:

单片机型号:stm32f103c8t6
蓝牙模块型号:HC\-05

APP推荐 : 蓝牙调试器\(https://www\.jianshu\.com/p/1a8262492619\)

### 重点考察:

串口、串口数据协议包

![1721125211456\.jpg](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/12/06/009-1cca7d96e28c.webp)

图中为蓝牙调试器APP的数据包说明。



## 任务:

### 任务名称:串口DMA

### 任务要求:

用Android端的app弄一个操控小车的遥控器。

要求可以给电机发送1%\-100%任意速度的指令。\(也就是要弄一个虚拟的遥杆\)

串口必须使用DMA。

### 元器件建议:

单片机型号:stm32f103c8t6
蓝牙模块型号:HC\-05

APP推荐 : 蓝牙调试器\(https://www\.jianshu\.com/p/1a8262492619\)

### 重点考察:

DMA、串口、串口数据协议包



## 任务:

### 任务名称:读取陀螺仪的欧拉角\(yaw，pitch，roll\)

### 任务要求:

用debug可以读取陀螺仪发来的值，并最后处理为欧拉角。

### 元器件建议:

单片机型号:stm32f103c8t6

陀螺仪型号:mpu6050\(难度高，需配置iic并读数\+滤波\+姿态解算\)、HWT605\-TTL\(需要会使用串口数据包协议\)、HWT101CT\-232\(同上\)、HWT605\-485\(同上，且需要懂Modbus\)

电平转换电路型号: TTL转RS232模块、TTL转RS485模块等

### 重点考察：

mpu6050 : I2C、卡尔曼滤波算法\(可选\)、姿态解算算法\(Mahony或者Madgwick等，详见[大疆开发板C型嵌入式软件教程文档\.pdf](https://sdutvincirobot.feishu.cn/wiki/PVS8wQzRgiTRqpko9l4cEK33nhw)\)

维特\(hwt\) : 串口UART、串口数据协议包\(陀螺仪协议说明书请看维特官网\)、各种逻辑电平之间的关系\(TTL、RS232、RS485等等\)、Modbus协议





## 任务:

### 任务名称:平衡小车

### 任务要求:

不要瞎找教程应付\(找那种人家成套教程做的不算做完任务\)，如果前面这些任务都会了，此任务靠自己想就可以完成，包括进阶要求。
①最低要求:

平衡小车要直立不倒，推动后扔不倒，起码轻微推不倒。

②一般要求:

平衡小车可由手机进行控制前进，后退，左转，右转等，且大部分时间不倒。

③标准要求:

平衡小车有俩模式:模式一是平衡小车被手机控制移动。模式二是平衡小车与前方挡板一直保持一个距离不变，前方挡板直线移动后，小车紧跟着向前移动。

④进阶要求:

在第三个要求的基础上，在模式一多加一个功能，功能为在车静止直立时，将小车头绕Z轴旋转一个角度后\(用手或者脚给他踹一下，让他绕z转一定角度\)，在手或脚的外力离开后，车会立马回到原来的yaw角度。\(简单来讲，让小车航向角yaw一直保持不变\)

### 元器件建议:

单片机型号:stm32f103c8t6、stm32f407igh6
电机型号:M2006、MG513、MG370、ATK\-JGB37\-520E
电机驱动板型号:C610、TB6612、L298N、AT8236

蓝牙模块:HC\-05

超声波测距模块:HCSR04\(输入捕获版\)

### 重点考察:

大脑、毅力



## 任务:

### 任务名称: 全向轮底盘\(omni wheels chassis\)

### 任务要求:

进行全向轮底盘运动解算，可以遥控全向轮底盘小车。进阶的话，可以给全向轮底盘小车加航向角环，使其可以纠正航向角，然后尝试使用世界坐标系。\(对于全向轮，加陀螺仪纠正的代码很好写，跟平衡小车差不多，这也不算进阶\)

### 元器件建议:

单片机型号:stm32f103c8t6、stm32f407igh6
电机型号:M3508、M2006、MG513、MG370、ATK\-JGB37\-520E
电机驱动板型号:C620、C610、TB6612、L298N、AT8236
蓝牙模块:HC\-05

### 重点考察:

全向轮运动解算、PID算法



## 任务:

### 任务名称: 麦克纳姆轮底盘\(mecanum wheels chassis\)

### 任务要求:

进行麦克纳姆轮底盘运动解算，可以遥控麦克纳姆轮底盘小车。进阶的话，可以给麦克纳姆轮底盘小车加航向角环，使其可以纠正航向角，然后尝试使用世界坐标系。\(对于麦轮，加陀螺仪纠正的代码很好写，跟平衡小车差不多，这也不算进阶\)

### 元器件建议:

单片机型号:stm32f103c8t6、stm32f407igh6
电机型号:M3508、M2006、MG513、MG370、ATK\-JGB37\-520E
电机驱动板型号:C620、C610、TB6612、L298N、AT8236
蓝牙模块:HC\-05

### 重点考察:

麦克纳姆轮运动解算、PID算法



## 任务:

### 任务名称:控制继电器和电磁换向阀

### 任务要求:

使用单片机控制继电器，使继电器再控制一个电磁换向阀

### 元器件建议:

单片机型号:stm32f103c8t6
继电器型号 :光耦隔离继电器模块等

电磁换向阀型号:二位五通电磁气动换向阀等

### 重点考察:

GPIO输出、继电器原理及使用方法、电磁换向阀原理及使用方法\(要求会认识几位几通换向阀，认识如何接线\)



## 任务:

### 1\. 任务名称:FreeRTOS创建任务\(线程\)

### 2\. 任务要求:

创建3个任务:

要求必须用CubeMX与C\+\+工程

第一个任务是让板载小灯每隔500ms闪烁。

第二个任务是让串口1发送“你好”给电脑或者其他串口调试器。

第三个任务是让串口1发送“Hello”给电脑或者其他串口调试器。

### 3\. 元器件建议:

单片机型号:stm32f103c8t6
串口模块:USB\-TTL调试器、蓝牙模块HC\-05

### 4\. 重点考察:

FreeRTOS的原理、FreeRTOS延时



## 任务:

### 任务名称:FreeRTOS队列、信号量

### 任务要求:

创建2个任务:
要求必须用CubeMX与C\+\+工程

一个任务是接收蓝牙发过来的数据，并保存到一个变量中。

另一个任务是获取任务一中的变量值，并判断若为1则让led亮，如果为0则让led灭。\(采用队列、信号量\)

### 元器件建议:

单片机型号:stm32f103c8t6
蓝牙模块:HC\-05

### 重点考察:

FreeRTOS的队列和信号量原理及使用、FreeRTOS延时



## 任务

### 任务名称:FreeRTOS内存管理



## 任务:

### 任务名称: 舵轮底盘\(AGV，helm wheels chassis\)

### 任务要求:

注意，舵轮不论是运动解算还是航向角纠正的难度与工作量都要高好几个档次。
进行舵轮底盘运动解算，可以遥控舵轮底盘小车。进阶的话，可以给舵轮底盘小车加航向角环，使其可以纠正航向角，然后尝试使用世界坐标系。

### 元器件建议:

单片机型号:stm32f103c8t6、stm32f407igh6
电机型号:GM6020、M3508、M2006、MG513、MG370、ATK\-JGB37\-520E
电机驱动板型号:C620、C610、TB6612、L298N、AT8236
蓝牙模块:HC\-05

### 重点考察:

舵轮运动解算、PID算法
