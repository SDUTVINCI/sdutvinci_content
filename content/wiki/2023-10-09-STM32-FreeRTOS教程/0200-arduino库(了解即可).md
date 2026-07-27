---
title: "arduino库(了解即可)"
---

### arduino库（Qualcomm Arduino、esp32）
Qualcomm Arduino 的语言系统在设计时参考了C、C++、Java，是一种综合性的简洁语言，语法更类似于C++，但是不支持C++的异常处理，没有STL库，你可以把它当作是精简后的C++。

资料:[Arduino常用库函数和速学参考](https://sdutvincirobot.feishu.cn/docx/HxRYd0Ixpoq1s6xyeQYcNBwun1s)

```cpp

int led0 = 13;

// 初始化函数
void setup()    //运行一遍
{
  //将LED灯引脚(引脚值为13，被封装为了LED_BUTLIN)设置为输出模式
  pinMode(led0, OUTPUT);
//OUTPUT输出信号，输出让led灯亮的信号 给引脚写数据
}

// 循环执行函数
void loop()         //while(true)
{
  digitalWrite(led0, HIGH);   // 打开LED灯   HIGH高电平
  delay(1000);                       // 休眠1000毫秒ms
  digitalWrite(led0, LOW);    // 关闭LED灯
  delay(1000);                       // 休眠1000毫秒ms
}
```
```cpp
int main()
{

    while(true)
    {

    }
}
```
```cpp
int main()
{
    setup();

    while(true)
    {
        loop();
    }
}
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image1.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image2.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image3.webp)

I/O口初始化函数pinMode()

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image4.webp)

I/O输出函数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image5.webp)

I/O输入函数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image6.webp)

### 最简单的电机驱动板使用讲解
**L298N电机驱动板介绍**

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image7.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image8.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image9.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image10.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image11.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image12.webp)

***如何给******单片机******和电机驱动板L298N供电呢？***

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image13.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image14.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image15.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image16.webp)
