---
title: "stm32单片机(重点)"
---

### 单片机介绍
#### ①什么是单片机?
单片机又称[单片微控制器](https://baike.baidu.com/item/%E5%8D%95%E7%89%87%E5%BE%AE%E6%8E%A7%E5%88%B6%E5%99%A8/2790468?fromModule=lemma_inlink)，它不是完成某一个逻辑功能的[芯片](https://baike.baidu.com/item/%E8%8A%AF%E7%89%87/32249?fromModule=lemma_inlink)，而是把一个计算机系统集成到一个芯片上。相当于一个微型的计算机，和计算机相比，单片机只缺少了I/O设备。概括的讲：一块芯片就成了一台计算机。它的体积小、质量轻、价格便宜、为学习、应用和开发提供了便利条件。同时，学习使用单片机也是了解[计算机原理](https://baike.baidu.com/item/%E8%AE%A1%E7%AE%97%E6%9C%BA%E5%8E%9F%E7%90%86/1221312?fromModule=lemma_inlink)与结构的最佳选择。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image19.webp)

#### ②单片机的应用?
1.  物联网(※)

2.  医用设备

3.  工业控制

4.  计算机网络通信(※)

5.  ... ...

#### ③stm32单片机组成部分
1.  CPU（中央处理器）

    1.  芯片框图

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image20.webp)

    3.  处理器内核（内核这个东西了解一下，以后要根据内核的架构和操作系统来判断下什么版本的软件)

        1.  介绍与作用 : CPU所有的计算、接收/存储命令、处理数据全部由内核执行。

        2.  指令集分类 : ARM架构、X86架构、LoongArch架构，RISC-V架构

            1.  ARM架构指令集

                1.  应用:广泛应用于移动行业（手机、平板、工控机等）等需要很强的能耗比的场景中

                2.  架构分类: ARM32、AArch64（ARM64）等

                3.  内核分类：ARM Cortex-X(手机) 、ARM Cortex-A（手机） 、 ARM Cortex-R（嵌入式） 、 ARM Cortex-M（嵌入式）

                4.  了解CPU Soc和CPU内核的区别

                        小米玄戒o1，华为麒麟9000，意法半导体STM32F407VET6这三个芯片都是基于ARM架构。他们的CPU的核心的前端设计都是由英国ARM公司设计好的，ARM的内核决定了这个CPU的性能，总线，浮点运算器等等。

                        而小米，华为，意法半导体只对CPU核心进行后端设计，对CPU性能等影响不会太大，**CPU绝大部分特征都是由ARM的前端设计决定的**。

                        **所以，我们在使用F407IG，F407VE的时候，因为他们的CPU内核都是Cortex-M4,所以CPU特征都一样，所以代码也几乎都一样的。**

                ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image21.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image22.webp)

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image23.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image24.webp)

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image25.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image26.webp)

            10.  X86架构指令集

                1.  应用：广泛应用于电脑、软路由、工控机等需要高性能计算的场景中

                2.  分类：X86、AMD64(X86_64)

            11.  LoongArch架构

                1.  应用：政府单位采购、军工采购、个人电脑等

                2.  分类：龙架构32位、龙架构64位

            12.  RISC-V架构

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image27.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image28.webp)

    5.  GPIO(通用输入/输出端口General-Purpose IO ports)

        1.  定义：CPU与外部进行信息交换的端口（Input输入、Output输出）

        2.  理解：CPU上的“金手指”（注意和电路板PCB上的引脚区分）

        3.  所在位置：通常把CPU焊在电路板PCB上，一般地，GPIO端口在电路板上经过某些电路最终被引出来成为电路板引脚

        4.  数量：STM32F407系列具有上百GPIO端口，由于数量过多，将其分为7个组(A,B,C... ...)，每组共16个IO口(0,1,2,3...15)

        5.  命名：P+GPIO组+IO口号（比如PA2,PB6等）

        6.  应用

            1.  普通输出IO口：输出高电平，或低电平

            2.  普通输入IO口：读取外部高低电平

            3.  复用IO口：可变为通信IO口与电脑、电机、蓝牙模块等通信（定时器PWM、串口UART、CAN通信、SWD调试通信、晶振IO口）

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image29.webp)

            ###       ④原理图

            6.  介绍：[顾名思义](https://baike.baidu.com/item/%E9%A1%BE%E5%90%8D%E6%80%9D%E4%B9%89/1481950?fromModule=lemma_inlink)就是表示[电路板](https://baike.baidu.com/item/%E7%94%B5%E8%B7%AF%E6%9D%BF/10106124?fromModule=lemma_inlink)上各[器件](https://baike.baidu.com/item/%E5%99%A8%E4%BB%B6/8755458?fromModule=lemma_inlink)之间[连接](https://baike.baidu.com/item/%E8%BF%9E%E6%8E%A5/70199?fromModule=lemma_inlink)原理的[图表](https://baike.baidu.com/item/%E5%9B%BE%E8%A1%A8/1252386?fromModule=lemma_inlink)。（各元件在原理图中是用整体形式来表示，进行二次接线的图）

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image30.webp)

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image31.webp)

            9.  组成部分：

                1.  元器件（包括元器件端口）

                2.  导线

                3.  网络标号

                4.  电源符号

                5.  等... ...

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image32.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image33.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image34.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image35.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image36.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image37.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image38.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image39.webp)

#### ⑤芯片手册
1.  作用：查询各种芯片信息（比如CPU频率，IO定义，时钟树等等）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image40.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image41.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image42.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image43.webp)

### 软件介绍
1.  STM32库

    1.  各种开发方式：寄存器，标准库，HAL库，LL库

        1.  寄存器功能简单了解：寄存器就是一个离CPU内核更近的存储结构，所以与CPU内核交换数据比内存（RAM）更快，每个寄存器都有不同的功能，在寄存器里存不同的值，CPU读取后都会实现对应的不同功能。

        2.  库：库是源文件+头文件。stm32的库是由汇编语言、C语言混合编译而成（HAL库、LL库兼容C++）。现有标准库、HAL库、LL库（标准库已淘汰，咱们实验室使用HAL库和LL库）

        3.  各种开发方式的优缺点：

            1.  寄存器：这种开发方式硬件执行效率高，但由于STM32寄存器过于多，用寄存器写可读性差，且麻烦繁琐，故不建议全用寄存器写，在某些场合下可以偶尔使用。（比如在流水灯可直接对寄存器进行移位操作、在调PWM占空比时可直接对CCR寄存器进行赋值等）

            2.  标准库：太老了，现在已经淘汰，该库使用汇编+C语言进行开发，代码可读性很高，但是由于初期对标准库设计有些问题以及一些专利上的问题，导致会出一些问题（比如IIC通信），且时钟配置过于麻烦繁琐，所以咱们于2021级开始就不再使用标准库。

            3.  HAL库、LL库（力推）：ARM公司与ST意法半导体力推的库，符合ARM CMSIS标准，该标准是当今嵌入式开发者都需要遵循的一个标准。该库由汇编语言+C语言进行开发，且兼容C++（头文件中有extern "C"条件编译），使用C++的OOP（面向对象）进行开发要方便一万倍。HAL库和LL库仍然被ST公司维护中，其解决掉了标准库的各种确定，比如硬件IIC无法正常使用，时钟配置及其容易。ARM CMSIS标准介绍:https://www.arm.com/technologies/cmsis

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image44.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image45.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image46.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image47.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image48.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image49.webp)

2.  开发软件介绍：

    1.  搭建环境教程:[STM32 Windows开发环境软件安装教程](/wiki/2023-12-10-dian-kong-shi-jue-huan-jing-da-jian)

    2.  ARM Keil MDK

        1.  介绍：可进行开发各种基于ARM Cortex系列内核开发的CPU的单片机（比如stm32），也可以开发其他类型单片机（例如51单片机）

        2.  作用：进行单片机的代码编辑(edit)、编译(compile)、构建(build)以及下载(download)与调试(debug)。

        3.  版本选择：

            1.  MDK 5.3及以上：建议使用，但需要自己装ARMCC编译器。其只能在Windows平台进行开发，且图形界面过于丑陋，且没有黑暗模式，夜晚开发及其辣眼，但由于其使用ARMCC和ARMCLANG编译器，比ARM-GCC编译器生成的量要小很多，且因为其对ARM Cortex内核兼容性极好，所以仍选择用MDK 5.3版本。

            2.  MDK 6及以上：详见下方的VScode

            3.  折中方案(Keil MDK5 + VScode +Keil Assistant): 详见下方的VScode

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image50.webp)

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image51.webp)

    3.  STM32 CubeMX

        1.  介绍：用图形界面生成STM32 HAL库部分驱动层代码的软件，由ST公司开发，仅支持STM32系列单片机。

        2.  作用：后期开发使用，进行STM32单片机的驱动层的基本配置（比如时钟树、GPIO、各种外设通信、中断、嵌入式实时操作系统等的配置），**前期新手禁止使用STM32 CubeMX这款软件****，不然就和没学一样。****前期新手只可以用该软件生成时钟函数****，其他的一切操作概不允许，可以了解一下，但不准作为主力开发工具。** （大概熟练掌握CAN通信，DMA等就可以使用该软件了）

    4.  STM32 CubeIDE（选用，没有需求就不要去使用）

        1.  介绍：跨平台的STM32单片机开发平台，仅支持STM32系列单片机，且只能用ARM-GCC编译器(该编译器远远比不上MDK5和MDK6上的ARM-Clang编译器，甚至部分性能也比不上ARM-CC编译器)

    5.  VScode

        1.  介绍：由微软开发的，开源的，世界第一的万能编辑器

        2.  作用：只是个编辑器（类似记事本），不自带编译器（比如GCC、MSVC，ARMCC(AC5)，ARMCLANG(AC6)，ARM-GCC），需要自己配置环境才能够正常开发C/C++，CMake，Python，ROS2，单片机等。

        3.  优点：①图形界面非常优美，②可跨平台，在Windows，Linux，MacOS上均能使用，③有非常多好用的插件。

        4.  缺点：①VSCode是使用Electron开发的，约等于塞了一个Google Chromium浏览器内核，非常占内存。②且环境难配置，但是这是必须要学的。

        5.  插件：

            1.  Keil Studio Pack(Keil MDK 6，截止2024年1月2日，推荐熟练使用keil5后再使用) MDK6已经基本完善了，可以使用，但是不建议使用。MDK6学习成本比较高，对新手不友好，且MDK5还在更新维护，所以建议使用MDK5.3及以上。但MDK 6基于MS VScode编辑器开发，实现了跨平台，可在Windows，Linux，MacOS上进行开发，且界面非常优美，所以未来可期。[ARM Keil MDK6使用教程](/wiki/2024-01-21-arm-keil-mdk6-jiao-cheng)

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image52.webp)

            3.  Keil Assistant（后期开发建议使用，可以代替Keil MDK 5.3 完成代码编译(edit)，但是编译，构建，下载，调试仍然建议在Keil MDK 5.3 上使用）在Windows上用MDK5软件配合Vscode的keil assistant插件进行开发。【VS Code开发stm32和51单片机的教程，vscode代替Keil-哔哩哔哩】 https://www.bilibili.com/video/BV18e4y1H7xX

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image53.webp)

### 时钟树
#### ①使用CubeMX配置时钟的步骤
1.  时钟配置介绍：这是每一个工程都需要做的事情，给予CPU正常的心跳。

2.  作用：给予CPU正常的心跳，并且给予各个外设的心跳，让CPU和其各个外设正常工作。（比如延时函数的准确度，定时器PWM波形的准确度）

3.  STM32F1系列CPU时钟框图：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image54.webp)

4.  配置需要注意的事项：

    1.  注意电路板上HSE的真实晶振频率，填高了会导致超频，会出现比较严重的问题

    2.  配置时建议用CubeMX配置时钟函数，然后复制到正点原子模板工程中（因为手撸时钟函数太难了）

    3.  CubeMX参考文档：[大疆开发板C型嵌入式软件教程文档.pdf](https://sdutvincirobot.feishu.cn/file/VTXjbEQQTohL3lxITmUcZokbnke)

5.  配置步骤（这里只点出几个要注意的点，详细步骤请看大疆C板开发文档）：

    1.  打开大疆C板开发文档

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image55.webp)

    2.  找到目录，点击0.4.2

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image56.webp)

    4.  按照0.4.2的步骤开始操作（每一步必须都得做，特别是Debug选Serial Wire，不选的话该工程代码会让板子假变砖）

        1.  需要注意板子型号，大疆板子是stm32f407igh6，咱们需要根据咱们实际的板子型号进行选择

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image57.webp)

        3.  配置时钟树时，需要注意HSE的时钟频率，按照实际原理图上的HSE频率来配置

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image58.webp)

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image59.webp)

        6.  代码路径必须全是英文，并且不能有连续两个空格，建议直接不要空格，单词之间用下划线（不可以放在桌面上）

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image60.webp)

        8.  解释

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image61.webp)

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image62.webp)

    5.  打开CubeMX生成的MDK 5工程

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image63.webp)

    7.  再复制一个并打开正点原子的模板工程

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image64.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image65.webp)

    10.  在CubeMX HAL库工程中的main.c中找到时钟函数void SystemClock_Config(void)的定义

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image66.webp)

    12.  复制整个void SystemClock_Config(void)函数的定义

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image67.webp)

    14.  然后打开正点原子的工程，在main函数中找到sys_stm32_clock_init(RCC_PLL_MUL9)函数，右键该函数，并go to definition of "sys_stm32_clock_init"找到这个函数的定义。

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image68.webp)

    15. 如果弹出下方的问题，请按照这个框框中的提示来解决，说的很明白。（如果看不懂英语，就去百度搜，锻炼下搜索能力）

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image69.webp)

    16.  go to definition of "sys_stm32_clock_init"完后找到这个函数的定义，删掉整个函数，并把刚才复制的CubeMX HAL库里的时钟函数复制到这里。并将Error_Handler();直接删掉，或者替换成while(1)；

    17. 找到sys_stm32_clock_init函数定义

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image70.webp)

    18. 框选后删掉

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image71.webp)

    19. 把复制的CubeMX HAL库里的时钟函数复制到这里。

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image72.webp)

    20. 用while(1)；替换掉Error_Handler();或直接删掉。

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image73.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image74.webp)

    21.  找到void SystemClock_Config(void)函数所在的源文件sys.c对应的头文件sys.h

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image75.webp)

    22.  找到sys_stm32_clock_init(uint32_t plln)函数，删掉，替换成void SystemClock_Config(void)的声明。

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image76.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image77.webp)

    23.  回到主函数，找到sys_stm32_clock_init(RCC_PLL_MUL9);函数，删掉，并调用咱们新的时钟函数

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image78.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image79.webp)

    24.  修改HSE_VALUE
    25. 随便找个地方输入HSE_VALUE并go to definition（go to definition完毕后，就可以删掉这个自己写的HSE_VALUE）

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image80.webp)

    26. 修改HSE_VALUE的值(如果是8MHz就写8000000U，如果是12MHz就写12000000U)

    通过看原理图可知，该板子为8MHz。（具体填多少，看你板子HSE的原理图，对应OSCIN和OSCOUT这俩IO口）

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image81.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image82.webp)

    27. 删掉原来用来go to definition才写的HSE_VALUE

    28.  删掉多余的代码

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image83.webp)

    29.  第9行的delay_init的入口参数具体填什么值，先查看一下他的定义

    30. 查看delay_init的定义，得知其入口参数为sysclk（系统时钟）

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image84.webp)

    31. 查看CubeMX的时钟树框图，得知SYSCLK的值为72MHz

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image85.webp)

    32. 把delay_init的值改为时钟树中的SYSCLK的值

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image86.webp)

    33. 然后编译所有文件

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image87.webp)

    34. 零错误零警告即配置成功，有错误有警告请自行百度、谷歌

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image88.webp)

#### ②查询某个外设时钟频率的方法（拿定时器来举例子）
1.  打开tim.c

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image89.webp)

2.  找到Msp初始化弱函数（看TIM的基句柄得知是哪个TIMx）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image90.webp)

3.  查找__HAL_RCC_XXX_CLK_ENABLE()的定义

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image91.webp)

4.  根据函数定义，可以看出TIM1挂载在APB2上

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image92.webp)

5.  查询时钟树，找APB2 Timer Clock可得TIM1的TCLK是168MHz

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image93.webp)

6.  所以得知，TIM1的TCLK频率为168MHz

### stm32程序组成
#### 基本介绍(主函数等)
1.  工程构成：stm32工程是由C语言和汇编语言的库组成的工程，所以有主函数，符合C/C++语言的结构。

2.  程序运行顺序：除了预编译等，程序从主函数开始运行，而且非常符合C/C++运行顺序，从主函数开始会逐行运行代码，然后会进入死循环。

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image94.webp)

3.  主函数内必须的组成部分：死循环\[while(true)或者for(;;)\]，因为单片机要一直运行下去，所以有个死循环。

4.  HAL库 与 用户自定义库

    1.  库：

        1.  .h文件声明函数

        2.  .c/.cpp文件定义函数

        3.  .c/.cpp文件调用函数

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image95.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image96.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image97.webp)

#### 中断服务函数的介绍
5.  特殊函数(中断服务函数)：中断服务函数是由汇编定义的，与芯片硬件更紧密，是由芯片中断事件所触发，并不满足常规C/C++调用顺序。

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image98.webp)

    1.  中断服务函数的调用方式：由中断事件所触发。一旦满足某个中断事件，就立马从正在运行的地方切换到中断服务函数里开始运行，然后运行完中断服务函数后，再返回刚才运行的地方接着运行。

    2.  中断事件：比如说第X条线的外部中断事件、systick滴答定时器中断(普通延时函数的实现方式)、UART接收中断事件、UART发送中断事件、TIM定时器溢出更新中断、TIM定时器输入捕获中断、CAN通信发送中断事件、CAN通信接收中断事件、RTOS的PendSV中断等等。（每个事件对应的中断服务函数一般都不相同，但是也有一些中断事件会共用同一个中断服务函数）

    3.  中断服务函数处理过程：

        1.  CPU检测到有中断事件的发生

        2.  保护现场，将当前位置的PC地址压栈(程序计数器(Program Counter))；

        3.  跳转到中断服务函数，执行中断服务程序；

        4.  恢复现场，将栈顶的值回送给PC;

        5.  跳转到被中断的位置开始执行下一个指令。

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image99.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image100.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image101.webp)

    4.  中断优先级与分组

        1.  优先级：抢占优先级和子优先级

        2.  分组0-5

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image102.webp)

        3.  更改分组(在HAL_Init中更改)

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image103.webp)

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image104.webp)

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image105.webp)

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image106.webp)

    4.  中断服务函数内容：

        1.  先查询中断标志位，确定被触发的中断事件

        2.  清除对应标志位，防止中断一直被触发，好让下次中断正常运行

        3.  接收数据等（可选）

        4.  逻辑业务代码实现（可选，比如数据处理等）

        5.  中断服务函数的特点：

            1.  中断服务函数不能传入参数；

            2.  中断服务函数不能有返回值；

            3.  中断服务函数应该做到短小精悍；

            4.  迫不得已的情况下，不准在中断服务函数中使用延时函数，如果要使用延时，请设置好延时和中断的优先级，否则程序出卡死（除了外部中断为了软件消抖而设立的延时）

            5.  不要在中断服务函数中使用printf函数，会带来重入和性能问题。

        6.  举例：

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image107.webp)

            1.  USART1_IRQHnadler函数

                1.  中文名：串口1_中断服务函数

                2.  声明定义：由汇编声明，需要用户自己去定义(如果使能了，用户还不定义，程序将会卡在汇编代码中）

                3.  调用条件：由CPU中断事件调用

                4.  作用：被CPU调用，并调用紧急的中断程序(中断程序也就是中断服务函数里的内容)

            2.  HAL_UART_IRQHnadler(句柄)

                1.  中文名：串口_中断**公共**服务函数(公共的意思指串口1,2,3,4,5......等所有的串口都共用这一个函数实现功能，由后面的句柄决定究竟是哪个函数被触发)

                2.  声明定义：ST公司编写的HAL库声明和定义

                3.  调用条件：由中断服务函数调用

                4.  作用：

                    1.  先查询中断标志位，确定被触发的中断事件

                    2.  清除对应标志位，防止中断一直被触发，好让下次中断正常运行

                    3.  接收数据等（可选）

                    4.  调用中断事件对应的中断回调函数

                    5.  其他操作（比如特殊的，在串口接收中断里会disable失能中断，也就是关掉中断）

            3.  HAL_UART_RxCpltCallback(句柄)

                1.  中文名：串口_中断回调函数(因为他被中断公共服务函数调用，所以句柄是由调用它的中断公共服务函数所决定)

                2.  声明定义：ST公司编写的HAL库声明为弱函数，需要用户自己去定义

                3.  调用条件：被中断公共服务函数调用

                4.  作用：先确定是哪个句柄调用的，再进行相应的业务逻辑实现（可选，比如数据处理等）

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image108.webp)

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image109.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image110.webp)

#### RTOS与ROS/ROS2简单了解
6.  进阶（非裸机开发，基于RTOS系统开发）

    1.  常见的RTOS(嵌入式实时操作系统)：FreeRTOS、Nuttx、RT-Thread、μC/OS-II、Xiaomi VelaOS

    2.  FreeRTOS官网：https://www.freertos.org/zh-cn-cmn-s/

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image111.webp)

    4.  FreeRTOS简单理解：拥有多线程库特性并兼容POSIX标准的操作系统

    5.  多线程：系统拥有多个任务(线程)，每个任务(线程)独立并同时运行(可以理解成每个任务都是一个主函数，这些任务都是同时在执行的。具体实现方式以后再学，原理就是PendSV中断等)

7.  进阶(非裸机开发，基于RTOS和ROS2_MicroROS)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image112.webp)

    2.  使用方式：ESP32使用arduino库+FreeRTOS+MicroROS并通过串口与STM32进行通信。

    3.  主要作用之一：可通过WIFI远程与上位机（电脑、工控机）的ROS2进行更加安全、稳定的通信，对比直接用串口通信（rosserial），要好很多(DDS分布式)。

    4.  MicroROS Vs ROSserial的详解链接: https://mp.weixin.qq.com/s/1lQXAA3sV-4GpXAzHiGChQ

### 寄存器
1.  理解：是CPU内部用来存放数据的一些小型存储区域，用来暂时存放参与运算的数据和运算结果。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image113.webp)

2.  实现的功能：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image114.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image115.webp)

3.  寄存器如何在基于C语言的HAL库中发挥作用的呢？（应该说是 C语言HAL库实现stm32单片机控制的原理是什么？）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image116.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image117.webp)

### Vinci机器人队标准工程格式
#### 英语
必须用 **英语** ，工程文件名、函数名、变量名必须用英语！(走出中文舒适圈，最起码一些专业英语你要认识)

#### 正点原子HAL库工程标准：
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image118.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image119.webp)

#### Vinci机器人队STM32工程标准(Cube+C语言)：
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image120.webp)

1.  applications应用层

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image121.webp)

2.  bsp驱动层

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image122.webp)

3.  Middlewares中间层

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image123.webp)

4.  Core(主函数所在地，条件编译配置HAL库的头文件所在地)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image124.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image125.webp)

####   Vinci机器人队STM32C/C++工程标准(类正点原子，试运行，***不建议***，`建议用下一节的类Cube_Cpp`):
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image126.webp)

1.  应用层、驱动层等采用模块集成式，不再采用Src和Inc分离式

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image127.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image128.webp)

2.  公共兼容层：

3.  C++子main兼容库

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image129.webp)

2.  作用：在.cpp文件中创建一个普通的函数，该函数调用C++的代码，然后被C语言main.c文件中的main函数所调用。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image130.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image131.webp)

5.  弱函数_回调函数库（该文件的源文件全局要有extern "C",因为弱函数是C语言的东西，C++无法正常识别）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image132.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image133.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image134.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image135.webp)

#### **(建议)Vinci机器人队STM32Cube C/C++工程标准(类Cube，试运行，建议):**
##### 首先打开CubeMX进行工程配置，比如我们这里用裸机开发使一个LED灯闪烁
然后选择OpenFolder打开文件夹

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image136.webp)

##### 打开Github将一些必备文件进行克隆
仓库链接：

https://github.com/tungchiahui/CubeMX_MDK5to6_Template

或者直接打开terminal输入

```bash
git clone https://github.com/tungchiahui/CubeMX_MDK5to6_Template.git
```

##### 打开克隆的模板与刚才CubeMX生成的工程
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image137.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image138.webp)

打开模板中的***`工程文件移植(创建新模板请看这里)`*** 文件夹，然后将里面的文件全部复制到CubeMX工程文件中。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image139.webp)

移动后：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image140.webp)

##### 打开工程设置工程
1.  打开MDK5工程

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image141.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image142.webp)

2.  点击Options for Target

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image143.webp)

3.  修改编译器为`ARMClang[ARM Compiler6 (AC6)]` **替换掉** `ARMCC[ARM Compiler5 (AC5)]`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image144.webp)

4.  添加头文件的路径(Include Path)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image145.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image146.webp)

添加applications中的Inc文件夹

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image147.webp)

添加bsp/boards中的Inc文件夹

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image148.webp)

点击OK即可

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image149.webp)

5.  添加源文件.c/.cpp等

打开Manage Project Items

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image150.webp)

创建两个分组

分组的名字分别叫

applications

bsp/boards

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image151.webp)

将Core/Src目录下的startup_main.cpp加入到Application/User/Core组中。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image152.webp)

将bsp/boards/Src目录下的bsp_delay.cpp加入到bsp/boards组中。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image153.webp)

可以看到工程里的文件都就绪了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image154.webp)

##### 编译并配置一些必要代码
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image155.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image156.webp)

可以右键头文件，然后点Open Document "xxx.h"来打开头文件，用来检查头文件是否导入成功。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image157.webp)

找到main.c文件，准备在主函数main()中调用C++的类主函数startup_main();

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image158.webp)

在USER CODE BEGIN Includes和USER CODE END Includes这两行注释中间 `引用startup_main.h` ***(因为不放在BEGIN和END之间的代码在CubeMX重新配置后，代码都会消失)***

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image159.webp)

在USER CODE BEGIN和USER CODE END这两行注释中间 `调用startup_main();`***(因为不放在BEGIN和END之间的代码在CubeMX重新配置后，代码都会消失)***

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image160.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image161.webp)

打开startup_main.h

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image162.webp)

更改isRTOS宏的值，如果是裸机开发则为0，如果使用了FreeRTOS则改为1。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image163.webp)

至此，你可以在startup_main()函数中随意调用C/C++库中的代码啦。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image164.webp)

##### C++库的头文件格式
拿bsp_delay.h举例

```cpp
#ifndef __BSP_DELAY_H_
#define __BSP_DELAY_H_

#ifdef __cplusplus
extern "C" 
{
#endif

#include "startup_main.h"

class BSP_Delay
{
        public:
                class F1
                {
                        public:
                                void Init(uint16_t sysclk);
                                void us(uint32_t nus);
                                void ms(uint16_t nms);
                }f1;
                class F4
                {
                        public:
                                void Init(uint16_t sysclk);
                                void us(uint32_t nus);
                                void ms(uint16_t nms);
                }f4;
                class FreeRTOS
                {
                        public:
                                void Init(void);
                }freertos;
};

extern BSP_Delay bsp_delay;

#ifdef __cplusplus
}
#endif

#endif

```

条件编译肯定不能少，一个是防止头文件重复引用的条件编译，一个是把C++链接为C语言的条件编译。（如果忘了，请看[Vinci机器人队C/C++资料](/wiki/2023-10-05-cplusplus-jiao-xue)）

然后引用startup_main.h头文件

再创建该模块的类，比如说class BSP_LED等，我这里为延时的类，所以是class BSP_Delay。

然后写类里的声明。

注意：不要在.h文件里写任何代码实现，也就是不能写任何函数的定义。

然后第35行的extern BSP_Delay bsp_delay;是在主函数中进行了创建对象bsp_delay，我们要在这里声明(extern)一下对象(变量)。方便其他的源文件调用。

理论上你是可以看懂上面所说的的，如果你实在看不懂，就照葫芦画瓢，画着画着也就理解了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image165.webp)

##### C++库的源文件格式
拿bsp_delay.cpp举例

```cpp
#include "bsp_delay.h"

#if isRTOS == 1
    #include "cmsis_os.h"
#endif

static uint32_t g_fac_us = 0;       /* us延时倍乘数 */

BSP_Delay bsp_delay;

/**
 * @brief       初始化延迟函数
 * @param       sysclk: 系统时钟频率, 即CPU频率(HCLK)
 * @retval      无
 */
 void BSP_Delay::F1::Init(uint16_t sysclk)
{
    SysTick->CTRL = 0;                                          /* 清Systick状态，以便下一步重设，如果这里开了中断会关闭其中断 */
    HAL_SYSTICK_CLKSourceConfig(SYSTICK_CLKSOURCE_HCLK_DIV8);   /* SYSTICK使用内核时钟源8分频,因systick的计数器最大值只有2^24 */

    g_fac_us = sysclk / 8;                                      /* 不论是否使用OS,g_fac_us都需要使用,作为1us的基础时基 */
}

/**
 * @brief       延时nus
 * @param       nus: 要延时的us数.
 * @note        注意: nus的值,不要大于1864135us(最大值即2^24 / g_fac_us  @g_fac_us = 9)
 * @retval      无
 */
void BSP_Delay::F1::us(uint32_t nus)
{
    uint32_t temp;
    SysTick->LOAD = nus * g_fac_us; /* 时间加载 */
    SysTick->VAL = 0x00;            /* 清空计数器 */
    SysTick->CTRL |= 1 << 0 ;       /* 开始倒数 */

    do
    {
        temp = SysTick->CTRL;
    } while ((temp & 0x01) && !(temp & (1 << 16))); /* CTRL.ENABLE位必须为1, 并等待时间到达 */

    SysTick->CTRL &= ~(1 << 0) ;    /* 关闭SYSTICK */
    SysTick->VAL = 0X00;            /* 清空计数器 */
}

/**
 * @brief       延时nms
 * @param       nms: 要延时的ms数 (0< nms <= 65535)
 * @retval      无
 */
void BSP_Delay::F1::ms(uint16_t nms)
{
    uint32_t repeat = nms / 1000;   /*  这里用1000,是考虑到可能有超频应用,
                                     *  比如128Mhz的时候, delay_us最大只能延时1048576us左右了
                                     */
    uint32_t remain = nms % 1000;

    while (repeat)
    {
        us(1000 * 1000);      /* 利用delay_us 实现 1000ms 延时 */
        repeat--;
    }

    if (remain)
    {
        us(remain * 1000);    /* 利用delay_us, 把尾数延时(remain ms)给做了 */
    }
}

/**
 * @brief     初始化延迟函数
 * @param     sysclk: 系统时钟频率, 即CPU频率(rcc_c_ck), 168MHz
 * @retval    无
 */  
void BSP_Delay::F4::Init(uint16_t sysclk)
{
    HAL_SYSTICK_CLKSourceConfig(SYSTICK_CLKSOURCE_HCLK);/* SYSTICK使用外部时钟源,频率为HCLK */
    g_fac_us = sysclk;                                  /* 不论是否使用OS,g_fac_us都需要使用 */
}

/**
 * @brief       延时nus
 * @param       nus: 要延时的us数.
 * @note        nus取值范围 : 0~190887435(最大值即 2^32 / fac_us @fac_us = 21)
 * @retval      无
 */
void BSP_Delay::F4::us(uint32_t nus)
{
    uint32_t ticks;
    uint32_t told, tnow, tcnt = 0;
    uint32_t reload = SysTick->LOAD;        /* LOAD的值 */
    ticks = nus * g_fac_us;                 /* 需要的节拍数 */
    told = SysTick->VAL;                    /* 刚进入时的计数器值 */
    while (1)
    {
        tnow = SysTick->VAL;
        if (tnow != told)
        {
            if (tnow < told)
            {
                tcnt += told - tnow;        /* 这里注意一下SYSTICK是一个递减的计数器就可以了 */
            }
            else 
            {
                tcnt += reload - tnow + told;
            }
            told = tnow;
            if (tcnt >= ticks)
            {
                break;                      /* 时间超过/等于要延迟的时间,则退出 */
            }
        }
    }
}

/**
 * @brief       延时nms
 * @param       nms: 要延时的ms数 (0< nms <= 65535)
 * @retval      无
 */
void BSP_Delay::F4::ms(uint16_t nms)
{
    uint32_t repeat = nms / 540;    /*  这里用540,是考虑到可能有超频应用, 比如248M的时候,delay_us最大只能延时541ms左右了 */
    uint32_t remain = nms % 540;

    while (repeat)
    {
        us(540 * 1000);        /* 利用delay_us 实现 540ms 延时 */
        repeat--;
    }

    if (remain)
    {
        us(remain * 1000);    /* 利用delay_us, 把尾数延时(remain ms)给做了 */
    }
}

void BSP_Delay::FreeRTOS::Init(void)
{
        //调用FreeRTOS自带的延时即可。
        //osDelay
        //vTaskDelay
        //vTaskDelayUntil
}

/**
  * @brief HAL库内部函数用到的延时
           HAL库的延时默认用Systick，如果我们没有开Systick的中断会导致调用这个延时后无法退出
  * @param Delay 要延时的毫秒数
  * @retval None
  */
void HAL_Delay(uint32_t Delay)
{
#if isRTOS==0   //如果是裸机开发

        #ifdef STM32F1  //如果是裸机开发且为F1
                        bsp_delay.f1.ms(Delay);
        #endif

        #ifdef STM32F4  //如果是裸机开发且为F4
                        bsp_delay.f4.ms(Delay);
        #endif

#elif isRTOS==1          //如果是FreeRTOS开发
                 osDelay(Delay);
#endif
}

```

刚上来肯定要引用自己的头文件。

这个条件编译不用管，因为延时在裸机开发和RTOS开发时有区别，所以我加了一行条件编译。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image166.webp)

上来要先创建一下类对象bsp_delay;

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image167.webp)

然后把类里的函数都进行定义。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image168.webp)

**函数注释格式：**

这一块是该函数的注释，以后尽量都这样写注释。（在以后MDK6中进行调用函数时，会提示该注释，一目了然）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image169.webp)

这样写注释的好处，在调用时，会显示入口参数需要填什么，会显示返回值是什么。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image170.webp)

brief 函数摘要

param 入口参数

retval 返回值

note或attention 注意事项

注意这里，有几个param入口参数，就写几个param

比如

```cpp
/**
 * @brief       CAN1通信发送函数
 * @param       motor1: 第1个电机的相对电流值
 * @param       motor2: 第2个电机的相对电流值
 * @param       motor3: 第3个电机的相对电流值
 * @param       motor4: 第4个电机的相对电流值
 * @retval      bool是否发送成功
 * @note        无特殊注意事项
 */
 bool CAN_BUS::CAN1::CMD1(int16_t motor1,int16_t motor2,int16_t motor3,int16_t motor4)
 {
 // ... ...
 }
```

##### 注意事项
1.  在.cpp源文件中，弱函数的定义前面要加个extern "C" 因为__weak是C语言(汇编向量)特有的，所以必须把代码以C语言的形式链接。

2.  代码要写在Begin和End之间，否则再次用CubeMX配置代码后，代码会消失。

### 驱动
1.  驱动，驱动程序全称设备驱动程序，能够使计算机与相应的设备进行通信。驱动程序是硬件厂商根据操作系统编写的配置文件，可以说没有驱动程序，计算机中的硬件就无法工作。

2.  普通模块的驱动：GPIO初始化程序+通信协议程序 数据协议处理程序

    1.  例子：LED灯，只需要GPIO初始化程序；蓝牙模块，需要写GPIO初始化程序和通信协议程序+数据处理程序

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image171.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image172.webp)

    4.  GPIO程序：

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image173.webp)

    6.  通信协议程序：如图是串口的通信协议程序以及GPIO程序

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image174.webp)

    8.  数据解析程序：如图是PS2手柄的数据处理函数（见C++题库数据解析的题型，主要用二进制，十六进制，位操作符等）

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image175.webp)

        2.  数据单位变换：

            1.  1Mbps（比特率） = 1000 000 bit/s（比特/秒） 1 byte（字节） = 8 bit（比特）= 8位二进制 （非常重要） 1 kbyte（千字节） = 1024 byte（字节） 1Mbyte（兆字节） = 1024 kbyte（千字节） 1Gbyte （千兆字节） = 1024Mbyte（兆字节）

            2.  1个字符 = 1 byte（字节）

                  1个阿拉伯数字 = 1个字符

                  在GBK编码下，1个汉字 = 2个字符

                  在Utf-8编码下，1个汉字 = 3个字符

        3.  数据命名格式（详细请见C++文档）：

            ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image176.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image177.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image178.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image179.webp)

    12.  针对HAL库外设API的填参方法：

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image180.webp)

        2.  查看对应的数据类型

        3.  查数据手册查函数的内容的注释

###   大疆电机控制(CAN)
    ###   ①CAN通信简介

3.  什么是CAN通信？

[CAN总线](https://so.csdn.net/so/search?q=CAN%E6%80%BB%E7%BA%BF&spm=1001.2101.3001.7020)通信系统是**串行通信**的一种，要优于串口[RS485](https://so.csdn.net/so/search?q=RS485&spm=1001.2101.3001.7020)总线。与I2C、SPI等具有时钟信号的同步通讯方式不同，CAN通讯并不是以时钟信号来进行同步的，它是一种**异步****半双工**通讯。（差分信号，半双工）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image181.webp)

2.  串口通信逻辑电平表示方法的分类

    1.  TTL（单片机上引脚常用电平，串口全双工，芯片IO口为TTL电平的RX,TX;信号线为TTL电平的RX,TX）

    2.  RS232（电压范围比TTL高的一种电平，抗干扰较好，串口全双工，芯片IO口为TTL电平的RX,TX;信号线为RS232电平的RX,TX）

    3.  RS485（差分信号，抗干扰极好，Modbus协议，串口半双工，芯片IO口为TTL电平的RX,TX;信号线为A和B）

3.  CAN通信信号线

    1.  差分信号，抗干扰极好，半双工，芯片IO口为TTL电平的CAN_RX,CAN_TX;信号线为CAN_H和CAN_L;（类似RS485)

#### ②电机库代码解析（该库内容要求尽量全部看懂，尽量一行不差）
4.  代码及其初始化

    1.  这部分正点原子都有讲，只需要把参数改为大疆电机的

    代码仓库链接:https://github.com/SDUTEMIS/SDUT_VinciRobot/tree/main/1.Embedded_STM32_Driver%2FC%2F4.Motor_Drivers%2F1.DJI_CAN_PID

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image182.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image183.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image184.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image185.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image186.webp)

5.  大疆电机库开环代码解析：库由往届学长学姐对大疆官方库代码修改后的。

    1.  CAN报文发送函数解析

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image187.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image188.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image189.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image190.webp)

       **此函数是将电流值发送给大疆CAN1通信电机，CAN1通信每次只能发送8比特的数据，电流值是16比特的数据，所以把电流值向右移8位，然后再发送给电机。电机接收到电流值就开始转动（入口参数是电调ID为1-4的电机电流值）**

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image191.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image192.webp)

       **此函数是将电流值发送给大疆CAN1通信电机，CAN1通信每次只能发送8比特的数据，电流值是16比特的数据，所以把电流值向右移8位，然后再发送给电机。电机接收到电流值就开始转动（入口参数是电调ID为5-8的电机电流值）**

    10.  CAN报文发送函数调用

    ```cpp
    int16_t Current_Motor_Target[1];

    void chassis_task(void const * argument)
    {
      //wait a time
      //空闲一段时间
      vTaskDelay(20);  //等待所有设备准备就绪

      while(1)   //可以在定时器中断里实现
      {
        Current_Motor_Target[0] = 1000;    //测试电机闭环是否可用的代码，正式使用时请注释该行代码
        CAN1_CMD_1(Current_Motor_Target[0],0,0,0);  //对电调ID为1的电机发送1000电流使其开环转动。
        //系统延时
        vTaskDelay(2);  //等同于osDelay(2);      
      }

    }
    ```

6.  大疆电机库闭环代码解析：库由往届学长学姐对大疆官方库代码修改后的。（在开环基础上又加了CAN报文接收，以及一系列数据解析程序）

    1.  CAN通信接收中断回调函数(CAN_RX0接收中断回调函数用来处理CAN通信电机发来的数据，也就是 **电机的速度，角度，温度** 等数据。)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image193.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image194.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image195.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image196.webp)

    6.  数据解析函数

        1.  电机编码器分为增量式编码器和绝对值编码器

            1.  增量式编码器：上电时数据会丢失，角度从0开始

            2.  绝对值编码器：掉电后数据不会丢失。

            3.  M3508和M2006编码器都为绝对值编码器，掉电后数据不会丢失，但是因为记录的是转子的角度，转子连接了一个减速器，所以导致数据是错误的，所以我们要用代码将绝对值编码器的数据转化为增量式编码器的数据来使用。(结构体里的total是绝对编码器的角度，而total_angle是我们处理后改为增量式编码器的总角度)

        2.  记录上电角度

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image197.webp)

        4.  计算总角度（经代码处理后，上电时总角度 = 圈数(0) \*8192 + 当前绝对值编码器的角度(假设为A) - 上电时捕获到的上电角度(因为此时为上电时，所以也为A) = 0）

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image198.webp)

        6.  暂时用不着的函数(此函数没有被调用)

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image199.webp)

#### ③PID控制器
1.  PID算法简介：

    1.  MATLAB_PID控制器介绍：https://www.mathworks.com/help/control/pid-controller-design.html?s_tid=CRUX_lftnav

2.  PID算法原理

关于理解PID控制算法最典型的一个例子就是一个漏水的水缸的问题。

有个漏水的水缸，而且漏水的速度还不是恒定的。然后我们还有个水桶，我们可以控制往水缸里面加水或者从水缸里面舀水出来。另外我们可以检测水平面。现在我们的目的就是要控制水平面稳定在我们想要的任何一个平面上。

注意我们使用PID需要在一个闭环系统里面。什么叫闭环系统，就是有输入有反馈，输入就是能输入一个量去影响和控制我们的系统，反馈就是我们要能知道我们最终控制的东西的状态。在这个漏水的水缸系统中，输入就是这个水桶，我们能通过水桶往水缸里面加水或者从水缸里面舀水出来来影响我们水缸的水平面，反馈的话也就是说我们要能测量水平面，知道水平面是多少。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image200.webp)

a, **比例控制理解**

*首先是比例控制。比例控制就好比是通过水桶往水缸加水或者从水缸舀水。假设我们需要把水平面稳定在A平面，而实际水平面在B平面，那么水平面差值Err=A-B，那这个时候我们需要往里面加水的量就是Kp\*Err，Kp就是我们的比例控制系数。*

*如果A>B，Err为正，就往水缸里面加水；如果A<B，Err为负，就从水缸里面舀水出来。那么只要预期水平面和实际水平面有差值，我们都会通过水桶去加减水来调整系统。同时Kp的大小也有对系统的性能有影响。如果Kp的值比较大，优点是从B平面达到A平面的速度快，缺点是在B平面已经接近A平面的时候系统会产生比较大的震荡。如果Kp的值比较小，优点是B平面在接近A平面的时候系统震荡小，缺点是从B平面达到A平面的速度慢。*

*这里也许有人会有疑问，如果这里把比例控制系数Kp直接设置成1，然后加水的量直接为Err=A-B不就可以了。然而实际上很多系统是做不到这点的。比如温度控制系统，实际温度为10度，我要通过加热把温度提升到40度，这里难道我们能一次性准确的给系统加30度？显然这是做不到的。那么比例控制的最终结果是Err的值趋向于0。*

b, **微分控制理解**

*然后我们先看看微分控制。在我们的比例控制的作用下，Err是开始减小的（假设一开始预期水平面A大于实际水平面B，也就是说Err是一个正值），那么也就是说Err随时间是一条斜率小于0的曲线，那么在周期时间内，Err越大，微分的绝对值越大，那么也就对Err的减小速度是起到抑制的作用的，直到最后斜率为0微分才会停止作用。*

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image201.webp)

微分控制能反映输入信号的变化趋势，因此在输入信号的量值在变化太大之前可为系统引入一个有效的早期修正信号以增加系统的阻尼程度，从而提高系统的稳定性，但一阶微分的高通特性使得该控制器易于放大高频噪声

c, **积分控制理解**

*积分控制部分的作用主要是用来消除静差。那么积分是怎样来消除静差的呢？*

*比例控制只能尽量将Err调节到0，而微分的作用是将曲线的斜率控制到0则停止对其作用，但斜率为0的时候Err并不一定为0。*

*这个时候我们就需要积分来起作用了。我们知道曲线的积分相当于曲线与x轴围出来的面积。如下图，积分作用的目的是使红色部分的面积和蓝色部分的面积的和为0，那么即使系统在比例控制和微分控制部分已经趋于稳定，只要Err不为0就会存在静差，只要存在静差那么积分就会对系统产生影响，直到系统的Err值为0。那么这样我们的PID控制在理论上就可以达到一个非常精确的控制效果。*

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image202.webp)

d, **PID算法离散化**

假设采样时间间隔为T，则在k时刻：

偏差为e(k);

积分为e(k)+e(k-1)+e(k-2)+…+e(0);

微分为(e(k)-e(k-1))/T;

从而公式离散化后如下：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image203.webp)

比例系数：Kp，

积分系数：Kp*T/Ti，可以用Ki表示；*

\* 微分系数：Kp\*Td/T，可以用Kd表示；

则公式可以写成如下形式：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image204.webp)

PID算法的离散形式就是这样了，这就是我们平时说的位置式PID。

但是为什么还要增推算量式？

一个累加符号使得微机的内存可能不够用，一个字节八位最多存到255，第二点就是掉电之后产生的产生的影响非常大，之前存储的状态会全部丢失，所以要推算对状态记录要求不高的增量式。

接下来我们继续推算增量式PID，根据上面公式我们可以求得：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image205.webp)

e,pid双环

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image206.webp)

f,pid前馈

3.  PID算法库

    1.  核心计算函数（非常成熟的控制器，数学算法）

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image207.webp)

    3.  初始化代码(将Kp，Ki，Kd三个参数与输出最大值，积分限赋值赋值给PID句柄pid_v_1或者其他的句柄)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image208.webp)

    5.  反馈环代码

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image209.webp)

    7.  闭环代码调用

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image210.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image211.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image212.webp)

#### ④C++库（建议）
##### 简介
代码仓库链接:https://github.com/TungChiahuiMCURepos/CAN_PID_CPP

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image213.webp)

类比着C语言的库，

can.c是CubeMX自动生成的CAN通信初始化驱动文件，

bsp_can.cpp是需要自己写的开启CAN通信的代码文件（CubeMX没自动生成的部分，需要手动调用）

can_receive.cpp里是CANRX0接收中断回调函数的实现，该回调函数里用了一些电机信息数据处理函数，然后还有CAN的4个发送函数。

pid.cpp是pid控制系统核心的数学算法代码

pid_user.cpp里是调用pid核心代码并进行封装为PID控制器的初始化代码和一些闭环实现代码。

##### C++大疆电机库
######   CLASS的结构与简单介绍
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image214.webp)

下方图片中是CAN_BUS类，其中嵌套了3个类。

1.  CAN_BUS::BSP类，该类中包含两个方法：

    1.  CAN_Start是开启CAN通信的函数；

    2.  Filter_Init是CAN通信滤波的函数。

2.  CAN_BUS::DJI_ENCODER类，该类里包含三个方法（该类里的所有函数都由CAN_RX0接收中断回调函数调用）：

    1.  get_motor_measure是处理CAN通信接收到的大疆电机编码器数据，并处理得到 **电机各个信息** 函数；

    2.  get_moto_offset是处理CAN通信接收到的大疆电机编码器数据，并处理得到 **电机刚开始上电的角度初始值** 函数；

    3.  get_total_angle是处理CAN通信接收到的大疆电机编码器数据，并处理得到 **电机角度值** 函数。（暂时没被调用）

3.  CAN_BUS::CMD类，该类里包含四个方法：

    1.  CAN1_Front是给CAN1 **前** 4个电机发送电流的函数；（对应电调ID：1-4）

    2.  CAN1_Behind是给CAN1 **后** 4个电机发送电流的函数;（对应电调ID：5-8）

    3.  CAN2_Front是是给CAN2 **前** 4个电机发送电流的函数;（对应电调ID：1-4）

    4.  CAN2_Behind是是给CAN2 **后** 4个电机发送电流的函数。（对应电调ID：5-8）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image215.webp)

###### CAN_BUS::BSP类的方法(函数) (在bsp_can.cpp中)
#######   CAN_Start 开启CAN通信的函数
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image216.webp)

####### Filter_Init CAN通信滤波的函数
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image217.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image218.webp)

###### CAN_BUS::DJI_ENCODER类的方法(函数) (在can_receive.cpp中)
####### get_motor_measure 处理CAN通信接收到的大疆电机编码器数据，并处理得到 **电机各个信息** 函数
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image219.webp)

####### get_moto_offset 处理CAN通信接收到的大疆电机编码器数据，并处理得到 **电机刚开始上电的角度初始值** 函数
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image220.webp)

####### get_total_angle 处理CAN通信接收到的大疆电机编码器数据，并处理得到 **电机角度值** 函数。（*暂时没被调用*）
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image221.webp)

###### CAN_BUS::CMD类的方法(函数) (在can_receive.cpp中)
####### CAN1_Front CAN1 **前** 4个电机发送电流的函数
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image222.webp)

####### CAN1_BehindCAN1 **后** 4个电机发送电流的函数
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image223.webp)

####### CAN2_FrontCAN2 **前** 4个电机发送电流的函数
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image224.webp)

####### CAN2_Behind CAN2 **后** 4个电机发送电流的函数
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image225.webp)

###### CAN_RX0接收中断回调函数 (在can_receive.cpp中)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image226.webp)

##### C++PID库
###### CLASS的结构与简单介绍
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image227.webp)

下方图片中是PID_Controller类，其中嵌套了3个类和一个方法：

1.  PID_Controller类：

    1.  All_Device_Init 将所有设备的PID控制器进行初始化

2.  PID_Controller::CORE核心类，该类中包含三个方法：

    1.  PID_Init PID核心初始化函数；

    2.  PID_Calc PID核心计算函数；

    3.  PID_Clear PID清0函数。

3.  PID_Controller::CAN_MOTORcan电机类，该类中包含6个方法(因为上面3个方法和下面3个方法只是CAN通信不一样，所以只讲CAN1)：

    1.  CAN1_Velocity_Realize CAN1速度环实现函数；

    2.  CAN1_Position_Realize CAN1位置环实现函数；

    3.  CAN1_VP_Dual_Loop_Realize CAN1速度位置双环实现函数；

    4.  CAN2_Velocity_Realize CAN2速度环实现函数；

    5.  CAN2_Position_Realize CAN2位置环实现函数；

    6.  CAN2_VP_Dual_Loop_Realize CAN2速度位置双环实现函数；

4.  PID_Controller::SENSORS传感器类，该类中包含三个方法：

    1.  Yaw_Realize 陀螺仪IMU的航向角PID实现函数；

    2.  Pos_X_Realize 码盘定位X坐标实现函数；

    3.  Pos_Y_Realize 码盘定位Y坐标实现函数；

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image228.webp)

###### PID_Controller类的方法(函数) (在pid_user.cpp中)
#######   All_Device_Init 将所有设备的PID控制器进行初始化
    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image229.webp)

###### PID_Controller::CORE类的方法(函数) (在pid.cpp中)
#######   PID_Init PID核心初始化函数
    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image230.webp)

#######   PID_Calc PID核心计算函数
    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image231.webp)

#######   PID_Clear PID清0函数
    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image232.webp)

###### PID_Controller::CAN_MOTOR类的方法 (在pid_user.cpp中)（这里只讲CAN1的3个闭环函数）
1.  **注意：**

    1.  *一般电流值变量定义为一个数组形式，比如fp32 motor_current_target\[8\];这样就成功定义了8个电机要发送的电流值。*

      *速度和角度位置同理，fp32 motor_speed_target\[8\];和fp32 motor_position_target\[8\];。*

    3.  *C++电机PID库与C语言的电机PID 库有些区别*

        1.  *因为电调ID的范围是1-8，而数组范围是0-7，*

        2.  *所以为了和数组序号一样，这个地方注意一下区别：*

        3.  *C语言库中，i的值为电调ID的值。*

        4.  *C++库中，****i值为电调ID值-1。***

####### CAN1_Velocity_Realize CAN1速度环实现函数
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image233.webp)

####### CAN1_Position_Realize CAN1位置环实现函数
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image234.webp)

####### CAN1_VP_Dual_Loop_Realize CAN1速度位置双环实现函数
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image235.webp)

###### PID_Controller::SENSORS传感器类的方法(函数) (在pid_user.cpp中)
####### Yaw_Realize 陀螺仪IMU的航向角PID实现函数（等你们完善好 陀螺仪IMU的C++库你们再补充）
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image236.webp)

####### Pos_X_Realize 码盘定位X坐标实现函数（等你们完善好 码盘OPS-9的C++库你们再补充）
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image237.webp)

####### Pos_Y_Realize 码盘定位Y坐标实现函数（等你们完善好 码盘OPS-9的C++库你们再补充）
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image238.webp)

##### 如何调用？
我这里选择每隔1ms使用PID控制器进行一次负反馈回路的控制，并发送一次电流值。

可以选择在while(1)死循环中加个delay(1)进行发送；

也可以使用周期为1ms的定时器中断进行实现，更建议使用定时器中断。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image239.webp)

#### ⑤实物连接，详细的请看说明书
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image240.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image241.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image242.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image243.webp)

### DMA(Direct Memory Access / 直接存储器访问)
### FreeRTOS
#### 理论知识
https://www.bilibili.com/video/BV19g411p7UT

**下方只会讲一些常用的操作和注意事项，更详细的FreeRTOS配置请看：(配合着学习)**

[大疆开发板C型嵌入式软件教程文档.pdf](https://sdutvincirobot.feishu.cn/wiki/PVS8wQzRgiTRqpko9l4cEK33nhw)

[STM32F1 FreeRTOS开发手册_V1.1.pdf](https://sdutvincirobot.feishu.cn/wiki/L40WwB369itdj4kGIsTcOc9cnQf)

[STM32F4 FreeRTOS开发手册_V1.1.pdf](https://sdutvincirobot.feishu.cn/wiki/VLg4w0oIQi8HHNkVE4CcGs65nue)

#### 常用的内容(下方教程着重讲CubeMX如何配置，理论知识请看正点原子)
##### 系统配置
1.  选择系统时基源(Timebase Source)

    1.  原因：因为FreeRTOS会占用systick，所以需要改时基源。

    2.  选择规则：优先选择功能少的定时器。(比如说F407ZGT6的tim6和tim7的功能比较少)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image244.webp)

    4.  如何选择？（如图）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image245.webp)

2.  选择接口(Interface)

    1.  原因：FreeRTOS遵循ARM的CMSIS标准。

    2.  选择原则：优先选CMSIS v1，因为CMSIS v2还有些小问题没解决。

    3.  如何选择？

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image246.webp)

3.  配置Include Parameters

    1.  功能：与hal_conf.h(用来开启HAL库的一些功能)一样，用来开启FreeRTOS的一些功能。

    2.  Include Parameters的配置

        1.  CubeMX配置(推荐)

        2.  需要什么功能就Enabled对应的功能即可。（常用的就是vTaskDelayUntil）

        ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image247.webp)

        4.  手动编辑头文件配置(不推荐)

##### 创建任务
1.  CubeMX创建任务：

    1.  各参数介绍(详看大疆手册)：

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image248.webp)

    3.  一般选择什么参数？

        1.  Task Name(任务名)：英文大写(与Entry Function对应)

        2.  Priority(优先级)：一般选择普通优先级即可(除非有特殊的逻辑)

        3.  Stack Size(栈空间）:128 Words即可

        4.  Entry Function(入口函数名)：英文小写(与Task Name对应)

        5.  Code Generation Option(代码生成选项)：无脑选择As weak(使FreeRTOS线程任务的入口函数以弱函数的形式生成)

        6.  Parameter(参数)：一般NULL即可，如果要用一些特殊功能(比如信号量)，要填一些句柄(比如信号量的句柄)

        7.  Allocation(份额)：无脑选Dynamic，让FreeRTOS动态分配管理即可

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image249.webp)

2.  注意事项：

    1.  任务创建太多会内存爆掉

##### 延时
1.  相对延时

    1.  函数：以下这俩函数作用相同，osDelay()和vTaskDelay()

    2.  时间：是从调用该函数才开始算，直到延时指定时间结束

    3.  调用方法：与HAL_Delay()方法一样

    ```cpp
    extern "C" //若在C++中运行需要加上该行
    void green_led_task(void const * argument)
    { 
        for(;;) //等同于while(true) 
        { 
        HAL_GPIO_WritePin(LED_GPIO_Port, LED_Pin, GPIO_PIN_RESET); 
        osDelay(500); 
        HAL_GPIO_WritePin(LED_GPIO_Port, LED_Pin, GPIO_PIN_SET); 
        osDelay(500); 
        }
    }
    ```

2.  绝对延时

    1.  函数：

        1.  获取当前时间：osKernelSysTick()

        2.  绝对延时函数：osDelayUntil()

    2.  时间：从任务开始就开始算时间了，将整个任务运行周期看成一个整体，适用于按照一定频率运行的任务

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image250.webp)

    4.  调用方法：

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image251.webp)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image252.webp)

##### 任务状态转换
1.  FreeRTOS状态(详看大疆手册)：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image253.webp)

2.  函数介绍：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image254.webp)

3.  如何调用：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image255.webp)

##### 队列
1.  原因：全局变量在多线程里是不安全的，多个任务对该变量进行操作时，数据容易受损。

2.  队列：队列是任务到任务、任务到中断、中断到任务数据交流的一种机制(消息传递)

3.  具体内容：详看正点原子视频学习理论知识

4.  调用

    1.  CubeMX配置：(Queue Size选择你要传的数据的比特数(即二进制位数)，Item Size选择数据的类型)

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image256.webp)

    3.  调用(详解请看正点原子)：

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image257.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image258.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image259.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image260.webp)

##### 信号量(队列的特殊形式)
1.  原因：同队列

2.  信号量：一种特殊的队列，是一种解决同步问题的机制，可以实现对共享资源的有序访问。

3.  分类：二值信号量、计数型信号量(详见正点原子)

4.  同步问题：A做完一个事情，通知B，B才可以做，这叫同步问题。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image261.webp)

5.  信号量简介(详见正点原子)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image262.webp)

6.  队列与信号量的对比

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image263.webp)

7.  二值信号量介绍：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image264.webp)

8.  CubeMX配置

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image265.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image266.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image267.webp)

要把创建的二值信号量的句柄传入任务的parameter参数里。

其实设置为NULL也可以。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image268.webp)

9.  调用(详细API函数作用请看正点原子)

    1.  释放信号量函数：xSemaphoreGive();

    2.  获取信号量函数：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image269.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image270.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image271.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image272.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image273.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image274.webp)

##### 内存管理
###### 简介
**栈区（stack）**：由编译器自动分配和释放，存放函数的参数值、局部变量的值等，其操作方式类似于数据结构中的栈。

**堆区（heap）**：一般由程序员分配和释放，若程序员不释放，程序结束时可能由操作系统回收。分配方式类似于数据结构中的链表。

(详细请看[Vinci机器人队C/C++资料](/wiki/2023-10-05-cplusplus-jiao-xue))

###### 修改stm32的栈区和堆区大小
#######   stm32本身的堆区和栈区大小修改
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image275.webp)

如上图，

stm32一般内存总大小为20Kb。

Heap Size就是堆大小，为512byte = 0.5Kb。

Stack Size就是栈大小，为1024byte = 1Kb。

剩余的其他部分的内存，分配给剩余的区，其中大部分内存都分给了Static静态区。

使用CubeMX生成工程后，可以在启动文件中看到咱们设置的堆区和栈区大小地址。（当然也可以在这里进行修改，不过建议直接在CubeMX上进行修改，一般没啥需求也不用改。)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image276.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image277.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image278.webp)

####### FreeRTOS的堆区大小修改(此堆区非彼堆区，请看下方介绍)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image279.webp)

1.  TOTAL_HEAP_SIZE：如果使用了FreeRTOS，可以在这里修改FreeRTOS的堆区的大小。

2.  memory management scheme：可以修改动态分配内存的算法，一般都使用heap_4算法。

3.  FreeRTOS_HEAP这里的堆区，非彼堆区，而是FreeRTOS从stm32的ZI区中开辟的内存(可以这么理解，其实是FreeRTOS的内核在data,bss,heap,stack等中抢的内存)，而并非从stm32的Heap堆区开辟的内存(在当你选择heap_1,2,4,5算法时)。如果你选择的是heap_3算法，那么将会使用C库的malloc()和free()函数进行开辟堆区内存，这个时候，FreeRTOS就是使用的为stm32的堆区(32的堆区比较小，所以不如heap_4算法)。但是咱们一般都使用heap_4算法进行内存管理，所以这里的***FreeRTOS_HEAP是从stm32_ZI区分配的。(也就是FreeRTOS_HEAP并非直接从heap区申请，而是非常灵活的在RAM中进行申请，可以超过STM32_HEAP的大小)***

4.  因为我们使用的是heap_4算法，所以我们不用对stm32的heap和stack进行修改，只需要对FreeRTOS_HEAP进行修改即可。(也就是对FreeRTOS可操控的stm32的ZI区内存进行分配)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image280.webp)

###### 内存管理API介绍
####### C语言库的内存管理API(不建议)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image281.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image282.webp)

####### 正点原子分块式内存管理API
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image283.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image284.webp)

memx就是指内存块，内部的SRAM和外部的SRAM(外部的不一定有外部的SRAM)。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image285.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image286.webp)

####### FreeRTOS内存管理API(建议)
1.  介绍

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image287.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image288.webp)

2.  FreeRTOS内存管理算法(我们一般选择heap_4)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image289.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image290.webp)

heap_4的first-fit算法是从堆区内存起始地址块开始找出第一个适合的内存大小。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image291.webp)

3.  FreeRTOS内存管理API函数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image292.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image293.webp)

可以通过看上面代码，就可以得知，申请完内存再释放掉内存后，空闲内存数会还原。

但是，可以看到，此时我们分配的是一个4字节的内存，但是他扣掉了16字节的内存，这是因为字节对齐的原因，FreeRTOS选择使用用空间换速度的方式进行字节对齐。

### FPU浮点数计算加速
STM32由于主频比较低，所以运算浮点数运算会非常慢，目前有下列几种办法可以优化sin，cos这种大型浮点数运算。

#### 检查是否支持
| STM32 系列 | CPU 内核 | DSP 指令 | FPU 类型 | arm_cos_f32() 性能 | 适合的计算 | 建议使用的函数 |
|:---|:---|:---|:---|:---|:---|:---|
| STM32H7 | Cortex-M7 | ✅ 支持 | ✅ 双精度 FPU (DP-FPU) | 🚀 最快（硬件加速） | 高精度计算、机器人、滤波、导航 | arm_cos_f32() |
| STM32H5 | Cortex-M33 | ✅ 支持 | ✅ 双精度 FPU (DP-FPU) | 🚀 最快（硬件加速） | 高精度计算、滤波、AI 计算 | arm_cos_f32() |
| STM32F7 | Cortex-M7 | ✅ 支持 | ✅ 单精度 FPU (SP-FPU) | 🔥 很快（硬件加速） | 机器人控制、导航、滤波 | arm_cos_f32() |
| STM32F4 | Cortex-M4 | ✅ 支持 | ✅ 单精度 FPU (SP-FPU) | 🔥 很快（硬件加速） | 机器人控制、数学运算 | arm_cos_f32() |
| STM32G4 | Cortex-M4 | ✅ 支持 | ✅ 单精度 FPU (SP-FPU) | 🔥 很快（硬件加速） | 电机控制、滤波 | arm_cos_f32() |
| STM32L4 | Cortex-M4 | ✅ 支持 | ✅ 单精度 FPU (SP-FPU) | 🔥 很快（硬件加速） | 低功耗计算 | arm_cos_f32() |
| STM32U5 | Cortex-M33 | ✅ 支持 | ✅ 单精度 FPU (SP-FPU) | 🔥 很快（硬件加速） | 低功耗 AI 计算 | arm_cos_f32() |
| STM32F3 | Cortex-M4 | ✅ 支持 | ❌ 无 FPU | ⚠️ 较慢（无 FPU，仅 DSP 加速） | 电机控制、信号处理 | arm_cos_q31() |
| STM32G0 | Cortex-M0+/M4 | ❌ 部分支持 | ❌ 无 FPU（部分 M4 版有 SP-FPU） | ⚠️ 较慢（软件计算） | 基础控制 | arm_cos_q31() |
| STM32F1 | Cortex-M3 | ❌ 不支持 | ❌ 无 FPU | 🚫 最慢（纯软件计算） | 不推荐做浮点计算 | arm_cos_q31() |
| STM32F0 | Cortex-M0 | ❌ 不支持 | ❌ 无 FPU | 🚫 最慢（纯软件计算） | 不推荐做浮点计算 | arm_cos_q31() |
| STM32L0 | Cortex-M0+ | ❌ 不支持 | ❌ 无 FPU | 🚫 最慢（纯软件计算） | 超低功耗应用 | arm_cos_q31() |

#### 开启FPU
浮点运算单元（FPU）是一种用于执行浮点运算的结构，通常由电路实现，应用于计算机芯片中。ARM设计的M4内核及更高级的内核都支持FPU，也就是STM32F4系列及往上。**（也就是STM32F1是不支持的）**

STM32F4/F7一般有单精度FPU，而STM32H5/H7，一般有双精度FPU。

**STM32F4开启FPU和不开启FPU往往会有****数十倍甚至上百倍****的差距。**

使用STM32CubeMX生成工程，会默认开启FPU，如下图。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image294.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image295.webp)

如果你使用F1的话，会压根都没有这个选项，代表M3内核不支持FPU。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image296.webp)

下面这张图可以从源码看到开启了FPU。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image297.webp)

#### DSP加速
DSP加速是指CMSIS-DSP库进行三角函数算法优化，使计算速度加快，但是误差会变大一些，不过对于99%的应用场景误差够用了，大概是1e-6单位的误差。

DSP库只适用于ARM的Cortex-A和Cortex-M的内核，也就是适应手机，ARM单片机，树莓派等等的设备。

**对于STM32单片机来说，基本覆盖了所有STM32系列，所以都可以用。**

假设你没有FPU，比如STM32F1系列的单片机，也可以通过DSP库来加速三角函数运算，这个DSP库的是通过查表+插值的数学运算方式进行优化的，计算也是比较快。

| 平台/库函数 | CMSIS-DSP | C++ std::cos | C math.h |
|:---|:---|:---|:---|
| arm_cos_f32 | std::__math::cos | cosf() / cos() |
| Cortex-M4/M7（带FPU） | ✅最快（查表+插值） | ✅比较快（完整计算） | ✅比较快（和 std::cos 相近） |
| Cortex-M0/M3（无FPU） | ⚠️比较慢（查表+插值） | 🚫最慢（软件浮点） | 🚫最慢（和 std::cos 相近） |
| Cortex-A（如 Raspberry Pi） | ✅可能更快（查表方法） | ✅更快（用 SIMD/FPU） | ✅更快（glibc/libm，SIMD 优化） |
| x86/x86-64（PC 端） | ❌不可用 | ✅最快（硬件加速） | ✅最快（使用 FPU 或 SIMD） |

所以说在STM32上跑还是建议用dsp库的函数。

##### 安装并使能DSP库：
1.  方法一（推荐）：使用CubeMX打开

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image298.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image299.webp)

然后使能DSP库

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image300.webp)

生成工程后，可以通过MDK5或者MDK6看到我们生成的lib。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image301.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image302.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image303.webp)

2.  方法二（不推荐）：使用MDK5打开

这种方式会使编译时间增加至少200%.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image304.webp)

##### 函数介绍
ARM内核的CPU支持 CMSIS-DSP 库的三角函数，这比标准 `math.h`、`cmath` 的函数更快。

1.  普通的C/C++三角函数库：

下面是普通的重载三角函数，当我们开启了FPU后，只要传入的是fp32的类型，其实速度也是相当快的，可以不使用DSP库也可以。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image305.webp)

```cpp
#include <cmath>
// 更新机器人的位置（假设机器人沿着x轴移动）
this->x_position += this->vx * std::__math::cos(this->yaw) * this->dt;  
this->y_position += this->vy * std::__math::sin(this->yaw) * this->dt;
this->y_position = - y_position;
this->yaw += this->vw * this->dt;
```

2.  DSP库函数：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image306.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image307.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image308.webp)

传入fp32的值。

```cpp
    // 更新机器人的位置（假设机器人沿着x轴移动）
    this->x_position += this->vx * arm_cos_f32(this->yaw) * this->dt;  
    this->y_position += this->vy * arm_sin_f32(this->yaw) * this->dt;
    this->y_position = - y_position;
    this->yaw += this->vw * this->dt;
```

##### 性能对比
| ✅ 对于有FPU的单片机 |
|:---|
| 函数 | 数据类型 | 计算方式 | FPU 需求 | 计算周期（STM32F4 @168MHz） | 相对速度 |
| 重载函数std::__math::cos(x) | double | C++ 标准库 (泰勒级数) | ✅ 需要 FPU | ⚠️ 80~100 cycles | ❌ 最慢 |
| float | C++ 标准库 (泰勒级数) | ✅ 需要 FPU | ✅ 20~30 cycles | ✅ 较快 |
| cosf(x) | float | C 标准库 (泰勒级数) | ✅ 需要 FPU | ✅ 20~30 cycles | ✅ 较快 |
| arm_cos_f32(x) | float | CMSIS-DSP 逼近计算 | ✅ 需要 FPU | ✅ 10~15 cycles | 🚀 最快 |
| arm_cos_q31(x) | Q31 | CMSIS-DSP（定点） | ❌ 无需 FPU | ✅ 约 50 cycles | ⚡ 比无FPU快 100 倍 |
| 查表法（LUT） | 预存数据查表 | ❌ 无需 FPU | 🚀 <10 cycles | 🔥 最快（1000 倍加速） | 🔥 比最快还快 |

除了`arm_cos_f32`，还有其他的一些`arm_cos_q31`函数，可能更加适配于F103这种低端芯片，可以进行自由选择。

| ❌ 对于无FPU的单片机 |
|:---|
| 函数 | 类型 | 计算方式 | FPU 需求 | 计算周期（F103@72MHz） | 相对速度 |
| arm_cos_f32(x) | float | CMSIS-DSP 逼近计算 | ❌ 无 FPU 需软件仿真 | ⚠️ 4500+ cycles | ⚠️ 很慢 |
| arm_cos_q31(x) | Q31 | CMSIS-DSP 定点查表 + 插值 | ✅ 无需 FPU | ✅ 约 50 cycles | 🚀 100 倍加速 |
| 重载函数std::__math::cos(x) | double | 标准库软件计算 | ❌ 无 FPU 需软件仿真 | 🚫 6000+ cycles | ❌ 最慢 |
| float | 标准库软件计算 | ❌ 无 FPU 需软件仿真 | 🚫 5000+ cycles | ❌ 非常慢 |
| cosf(x) | float | 标准 C 库软件计算 | ❌ 无 FPU 需软件仿真 | 🚫 5000+ cycles | ❌ 非常慢 |
| 查表法（LUT） | float / Q31 | 预计算余弦表 | ✅ 无需 FPU | 🚀 约 5 cycles | 🔥 1000 倍加速 |

### DMA+多通道adc(遥控器遥杆)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image309.webp)

cubemx配置：

多通道adc大部分要开启扫描模式；

adc连续模式开启或者关闭，影响mian函数的相关代码，不开continuous则需在while中不断对adc进行开启

开连续模式（延时500可以去掉）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image310.webp)

不开：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image311.webp)

相比之下开连续更快，更建议连续

### STM32常见问题
#### STM32 使用ST-link下载问题
1.  原因：在使用CubeMX 配置文件时，忘记设置SYS选项里面的Debug选项

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image312.webp)

2.  现象：下载完一次程序之后程序无法运行，且无法重新下载。

3.  stm32共有三种启动模式：

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image313.webp)

    2.  用户闪存：正常的工作模式。stm32内置的Flash，一般我们使用JTAG或者SWD模式下载程序时就是下载到这个里面，重启之后也是从这里启动程序。

    3.  SRAM：芯片内置的RAM区，就是内存，没有程序储存的能力，这个模式一般用于调试。

    4.  系统储存器：系统储存器是芯片内部的一块特定的区域

    5.  stm32厂商在这个区域内部设置了一段Bootloader。选用这种启动模式，是为了能够从串口下载程序，因为在商家提供的Bootloader中，提供了串口下载的固件，可以通过这个Bootloader将程序下载到系统的Flash中。

4.  解决方法：

    1.  将BOOT0设置为1；BOOT1设置为0

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/09/image314.webp)

    3.  连接电脑后按下复位键，使用keil5下载没有问题的正常程序，发现程序正常下载。

    4.  将BOOT引脚改为原来的状态，再次尝试下载程序发现一切正常。
