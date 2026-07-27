---
title: "ARM Keil MDK(ARM单片机环境搭建)"
---

### 介绍以及环境推荐
1.  Keil MDK是ARM公司旗下官方软件。最主流的单片机开发环境IDE，没有之一。

2.  **推荐版本：**

    1.  **新手：无脑** **ARM** **Keil** **MDK** **5 + CubeMX(新手前期只准先安装ARM Keil MDK 5，不准用CubeMX)**

    2.  **老鸟：无脑 VScode + CMake + CubeMX**

### Arm Keil MDK 5 + CubeMX【推荐小鸟(新手）】
#### 简介
1.  最主流的开发环境(即便有很多致命缺点)，没有之一。新手无脑用，新手必须用这个方案开发STM32。

2.  缺点很明显：上古的界面，上古的代码补齐，仅支持Windows，且只有X86的32位版本。

#### Windows
https://www.bilibili.com/video/BV18T411r7Yu

所需文件获取方式：

1.  社团的U盘(直接拷贝)

2.  官网：

    1.  MDK5官网：https://www.keil.com/demo/eval/arm.htm

    2.  MDK5的FW固件下载链接：https://www.keil.arm.com/devices/

    3.  ARM编译器下载链接：

        1.  ARMCC(AC5)必下：https://developer.arm.com/downloads/view/ACOMP5?revision=r5p6-07rel1

        2.  ARMCLANG(AC6)一般MDK5最新版都自带，*不用下*：https://developer.arm.com/downloads/view/ACOMPE

        3.  AC5与AC6对比(一般不用看)：[https://developer.arm.com/Tools and Software/Arm Compiler for Embedded FuSa#Editions](https://developer.arm.com/Tools%20and%20Software/Arm%20Compiler%20for%20Embedded%20FuSa#Editions)

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image1.webp)

    4.  Keil MDK 5 破解注册机(请关闭杀毒软件再下载)： [keygen.iso](https://sdutvincirobot.feishu.cn/file/SBbTbvSQ5o3dvExZ49UcGvYen8d) or https://www.duote.com/soft/907739.html

    5.  Java_JRE下载(一般下载[Windows Offline (64-bit)](https://javadl.oracle.com/webapps/download/AutoDL?BundleId=250129_d8aa705069af427f9b83e66b34f5e380),需要安装JRE才可以打开CubeMX)：https://www.java.com/en/download/manual.jsp

    6.  CubeMX官网：https://www.st.com.cn/zh/development-tools/stm32cubemx.html

    7.  正点原子配套资料1(A盘资料F1):http://www.openedv.com/docs/boards/stm32/zdyz_stm32f103_warshipV4.html

    8.  正点原子配套资料2(A盘资料F4):http://www.openedv.com/docs/boards/stm32/zdyz_stm32f407_explorerV3.html

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image2.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image3.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image4.webp)

### Arm Keil MDK 5 + VScode(Keil Assistant)【不推荐】
#### 简介
1.  该方式是折中方案，介于MDK5和MDK6之间的方案，可以规避掉一部分MDK5的缺点。(但是由于C/C++插件过于垃圾，所以**不太推荐使用**)

#### Windows
##### 配置教程：
快速配置的视频：

https://www.bilibili.com/video/BV18e4y1H7xX

如果遇到一些问题，请看下方这个视频：

https://www.bilibili.com/video/BV19V411g7gD/

1.  请安装好MDK5，CubeMX，VScode等软件

2.  安装VScode插件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image5.webp)

3.  配置Keil Assistant

    1.  点击扩展设置

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image6.webp)

    3.  找到MDK5的路径

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image7.webp)

    5.  复制一下路径

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image8.webp)

    7.  填入刚才复制的路径

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image9.webp)

4.  用VScode打开MDK5工程

    1.  打开工程

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image10.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image11.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image12.webp)

5.  然后就可以用VScode编辑源码了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image13.webp)

##### 注意事项（必看）：
###### 须知
1.  该方式仅仅为**折中方案**，比单用MDK5好用一点，但远没有MDK6好用。

2.  VScode只负责代码编辑(建议别用Keil Assistant的编译等功能)。

3.  MDK5负责编译，下载，debug，添加源文件，头文件等其他一切操作。

4.  优点：解决了AC6编译器的go to definition失效的问题(AC6全是优点，这是唯一缺点)；解决了MDK5上古界面的不护眼；解决了MDK5代码提示拉跨；可以使用各种VScode的插件提高编辑效率。

5.  缺点：需要同时开着VScode和MDK5，各司其职，不统一。C/C++插件的代码提示虽比mdk5好，但也是过于拉胯。(远不如MDK6+Clangd)

###### 文字🦟编码有问题
1.  问题如下：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image14.webp)

2.  手动改编码(点右下角的UTF-8，推荐用下方的插件解决，比手改便捷)：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image15.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image16.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image17.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image18.webp)

3.  插件(VScode的特点就是插件多，不用插件的vscode失去了灵魂)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image19.webp)

该插件会自动提示你当前的编码有问题，自动猜测正确编码，点击yes就可以自动修改。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image20.webp)

###### 误报错
不用管，只要MDK5编译不报错就行。

###### 无法go to definition
(如果有问题才需要做，没问题就不要管)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image21.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/10/image22.webp)

在下面框中加上：(加完后可能还会误报错，但是已经可以正常go to definition了)

```json
..\**
```

或者 将Include Path这一栏里的绝对路径全改为相对路径(下方只列举一部分)：

```json
..\applications\Inc
..\bsp\boards\Inc
```

### Arm Keil MDK 6 (基于VScode)【暂不推荐】
#### 简介
1.  优点：改善了MDK5的基本所有已知缺点，简直是开发者福音。

2.  缺点：学习成本较高，没有一定的电脑常识驾驭不了。

3.  目前暂时还**不是很推荐单独使用**，因为即便你会用，队友也不一定会用，这样工程无法互通。但如果你有能力，就是想用MDK6，那用也无妨，自己用着顺手为主。

4.  但是**非常推荐配合使用**，MDK6配合MDK5一起使用。使用MDK5添加源文件，添加头文件等操作(不要忘记添加完源文件和头文件后，用mdk5编译一下)；使用MDK6转化刚才的mdk5工程，进行源码编辑，编译，debug等等操作。(这样无论是用mdk5还是mdk6的队友，都可以打开你的工程直接食用啦)（看不懂这段话问学长）

#### Windows && Linux
[ARM Keil MDK6使用教程](/wiki/2024-01-21-arm-keil-mdk6-jiao-cheng)

### 告别Keil MDK : VScode+CMake环境部署【非常推荐老鸟】
**(开发起来目前感觉还是挺舒服的，日后电控组组长如果觉得好用，可以统一一下IDE)**

主要现在有三个使用比较广的编译器.

armclang(AC6):编译巨快,开销巨小.(不论是编译,还是开销都非常优秀)

armcc(AC5):编译巨慢,开销很小.(仅仅只是开销小)

armgcc:编译较快,开销略大.(仅仅只是编译快)

所以说armgcc并没有那么大的优势，但是也可以接受。

主要是ARMCC和ARMCLANG是商用编译器，而ARMGCC是开源编译器，所以可以搭配CMake，Makefile等使用，可以更好管理项目，也可以支持全平台(Windows，Linux，MacOS等）

详细教程[STM32+CMake工程部署](/wiki/2025-07-18-linux-stm32-cmake-vscode-huan-jing-da-jian)
