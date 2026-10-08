---
vinciId: 8f8ab1e2-3078-465c-8281-cce8c14881aa
title: vofa
description: 所有文件下载连接：https://dl.sdutvinci.cn/vdl/%E8%BD%AF%E4%BB%B6/%E5%B5%8C%E5%85%A5%E5%BC%8F%E7%BB%84/vofa
authors:
  - duanquanyu
publishedAt: 2026-10-08T12:41:29.170Z
updatedAt: 2026-10-08T12:41:29.170Z
tags:
  - 嵌入式组
---
[所有文件下载连接：https://dl.sdutvinci.cn/vdl/%E8%BD%AF%E4%BB%B6/%E5%B5%8C%E5%85%A5%E5%BC%8F%E7%BB%84/vofa](https://dl.sdutvinci.cn/vdl/%E8%BD%AF%E4%BB%B6/%E5%B5%8C%E5%85%A5%E5%BC%8F%E7%BB%84/vofa)

建议先全部看完再实操，此软件是免费的，不要付钱！！！！！！！

&#x20;   打开是如下图，直接叉掉，每次打开都会出现，无任何影响，如果学弟学妹们非常有钱，建议赞助一下学长和战队。（学长和战队需要你的支持）

![image](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/08/1791462429580-71219ff5.webp)

<br />

初始设置：

&#x20;先修改此按键，改为串口

![image](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/08/1791461515756-928a5009.webp)

<br />

&#x20;   如果在连接之后没有串口，是未下载串口接口，在上面的连接，串口连接中下载固件，在下载之后，有些电脑需要重启之后才可以正常使用。

![image](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/08/1791461687044-5c19c6a1.webp)

<br />

<br />

如果是只需要收发信号，则选择RawData在此可以显示使用。

如果需要在页面上显示连续数据，则选择JustFloat，需要特定的传输格式和文件，详细见连接Vofa.c 和 Vofa.h。

![image](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/08/1791461657390-d6cf4b2c.webp)

1.是切换16进制和字符串

2.是发送结尾格式

要发送按发送键即可

![image](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/08/1791462951588-8146516c.webp)

<br />

<br />

I0和I1是不可见状态 

I2，I3，I4是可见状态，点开I1可以修改配置

增益和偏置是放大x和y和其本身值

![image](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/08/1791461963658-2b169394.webp)

<br />

这个是控制可观测边界，可以放大缩小xyz，建议可以自己试试改变红色按键，紫色按键和绿色按键，可以按auto自动定位，也可以用鼠标滚轮放大缩小页面

![image](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/08/1791462198241-00f0f75a.webp)

<br />

这个按键是控制串口开关的

<br />

![image](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/08/1791462346953-313837bc.webp)

<br />

如果你只需要基本学习看到此处已足够，但是学习是永无止境的，所以进阶版也要学！！

<br />

1和2是打开的内容，3是如左边，生成数据流，4是导入数据流形成折线图，对以后观测各自数据非常有帮助！

![image](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/08/1791463073366-d07fb445.webp)

<br />
