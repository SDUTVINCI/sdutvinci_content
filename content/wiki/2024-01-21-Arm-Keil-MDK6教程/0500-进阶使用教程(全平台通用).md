---
title: "进阶使用教程(全平台通用)"
---

### Run（运行程序）和Debug（调试程序）？
#### 选择packs
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image65.webp)

出现STM32 STLink后，接着点回车Enter

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image66.webp)

搜索对应的芯片的Packs并选中

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image67.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image68.webp)

#### (RUN)将程序下载到ST-Link中
点击RUN，然后在新弹出的窗口选择对应的型号，比如我选择STM32F103C8

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image69.webp)

可以看到下方的命令已经把程序烧写进STM32了，然后STM32也正常工作了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image70.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image71.webp)

#### (DEBUG)调试程序
打上三个断点

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image72.webp)

```cpp
extern "C"
void led_task(void const * argument)
{

    for(;;)
    {
        static int a = 5;
        bsp_led.LED_Toggle();  //实例化后调用对象翻转电平函数
        osDelay(500);
        a++;
    }
}

```

点击Debug并选中型号

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image73.webp)

然后就可以进入Debug界面

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image74.webp)

点击开始按钮

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image75.webp)

可以看到断点被成功命中，且可以通过左边窗口查看a的值。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image76.webp)

接着点击继续。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image77.webp)

下一个断点也被命中了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image78.webp)

接着点继续，发现a的值变为了6，符合我们程序的运行。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image79.webp)

这样就可以正常debug了。

### VScode头文件配置
**(这只是可以更好的编辑代码，这些头文件并没有被加入到编译环境中)**

#### C/C++插件（不推荐）
如果有这种找不到头文件的情况，配置一下VScode的C/C++插件的Include Path即可。

但是由于该插件需要同时配置编译器，所以可能会出一些各种各样的小问题。

而且该插件对于大型项目会很卡，可以选择直接看下方的clangd插件教程。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image80.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image81.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image82.webp)

在这里多加一行../\*\*

除了以上这种方式，也可以通过修改c_cpp_properties.json文件进行。

输入 `"../**"` (意思是将上一个目录(工程根目录)里的所有文件全部加载到Include Path中)

同时建议也把ARMCLANG的include文件加入到这里面 "`/home/tungchiahui/.vcpkg/artifacts/2139c4c6/compilers.arm.armclang/6.21.0/include/`"

每个人的目录不同，但都是在用户文件夹的.vcpkg隐藏文件夹下，可以自己找找。（下方的图不完整，请根据上访内容进行添加）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image83.webp)

配置好之后，我们发现代码提示也正常了，虽然头文件还是有可能会被VScode误报错说找不到，但是其实已经可以正常编译了，也可以正常提示这些头文件了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image84.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image85.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image86.webp)

#### Clangd插件 (非常推荐)
1.  优势：由于clangd适合大型的cmake项目，在大型项目里表现比C/C++插件优秀太多，所以笔者与MDK6都建议用clangd的语言服务器。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image87.webp)

现在最新版MDK6自带clangd插件。

2.  Windows需要下载安装一下LLVM (Linux一般不用管或者`sudo apt install llvm`)

https://github.com/llvm/llvm-project/releases

我下载的是LLVM 18.1.8，中选择`Assets`中选择`LLVM-18.1.8-win64.exe`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image88.webp)

这里选择这个选项`Add LLVM to the system PATH for all users`，其他无脑下一步即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image89.webp)

可以打开terminal测试一下是否安装成功并配置好环境。

```powershell
clang -v
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image90.webp)

3.  现在来安装clangd：

按住Ctrl shift P打开搜索框

输入clangd 找到下载语言服务器这一项目，点击安装clangd（请保持良好的网络状况）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image91.webp)

4.  接着配置clangd：

禁用C/C++的代码提示功能

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image92.webp)

如果没有上图的弹窗，可以进行手动关闭，依然是ctrl shift P,输入settings然后找到如下图的选项

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image93.webp)

找到下图这个选项，改成disabled即可。

`"C_Cpp.intelliSenseEngine": "disabled"`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image94.webp)

新建一个settings.json文件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image95.webp)

修改里面的内容，该内容是 cmake产生的compile_commands.json 文件所在的路径(路径会随MDK6版本更新而改变，请自己找文件所在路径)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image96.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image97.webp)

接着找到armclang编译器的include目录，也添加进来，一般在用户文件夹下的.vcpkg隐藏文件夹下。

(现在已经无需找了)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image98.webp)

以下是Linux版本的settings.json示例

```
{
    "clangd.arguments": [
        "--compile-commands-dir=${workspaceFolder}/tmp/Template_Linux/TemplateLinux"
    ]
}
```

以下是Windows版本的settings.json示例

需要注意的是，Windows需要把盘符号变为小写，比如`C:/`要改为`c:/`然后`反斜杠\`要改为`斜杠/`。

```json
{
    "clangd.arguments": [
        "--compile-commands-dir=${workspaceFolder}/tmp/Template_Linux/TemplateLinux"
    ]
}
```

然后ctrl shift P搜索clangd找到如下图的选项

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image99.webp)

代码提示就正常啦

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image100.webp)

### **添加源文件(对应Project Items)和头文件(对应Include Path)到编译环境中**
#### 常规方法(修改yaml文件)
##### 相关资料
添加源文件需要使用yaml标记语言修改cproject.yml文件。

官方为此提供了相关的更为详细的资料文档：https://github.com/Open-CMSIS-Pack/cmsis-toolbox/blob/main/docs/YML-Input-Format.md#source-file-management

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image101.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image102.webp)

##### 创建文件(.c和.h)
我们这里先在bsp中创建4个文件分别放入到Src和Inc中。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image103.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image104.webp)

##### 添加头文件路径
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image105.webp)

将头文件所在的目录写入

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image106.webp)

```
      add-path:
        - ../Core/Inc
        - ../Drivers/STM32F1xx_HAL_Driver/Inc
        - ../Drivers/STM32F1xx_HAL_Driver/Inc/Legacy
        - ../Drivers/CMSIS/Device/ST/STM32F1xx/Include
        - ../Drivers/CMSIS/Include
        - ../bsp/boards/Inc
        - ../applications/Inc
```

##### 添加源文件与分组
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image107.webp)

在这里输入group的名字和所需要添加的源文件路径（这里因为applications里无源文件，所以我们注释掉）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image108.webp)

```ymal
    - group: bsp/boards
      files:
        - file: ../bsp/boards/Src/gpio_demo.cpp
        - file: ../bsp/boards/Src/gpio_test.c

    # - group: applications

    #   files:
```

源文件和头文件都已经成功导入了，我们可以对文件内容进行编写，看其是否能通过编译。

##### 编写文件并编译
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image109.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image110.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image111.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image112.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image113.webp)

可以看到日志这几行，显示gpio_demo和gpio_test都成功被编译了

```bash
[14/22] Building C object CMakeFiles/Template_Linux.dir/home/tungchiahui/user/Source/STM32_Projects/N1_F407ZGT6_GPIO_Test/bsp/boards/Src/gpio_test.o
[15/22] Building C object CMakeFiles/Template_Linux.dir/home/tungchiahui/user/Source/STM32_Projects/N1_F407ZGT6_GPIO_Test/Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_flash_ex.o
[16/22] Building CXX object CMakeFiles/Template_Linux.dir/home/tungchiahui/user/Source/STM32_Projects/N1_F407ZGT6_GPIO_Test/bsp/boards/Src/gpio_demo.o
```

#### 图形化
##### 简介
由于ARM团队比较给力，短短2个月就搞出来了图形化操作，截止3月初已经更新。

ARM团队更新了什么图形化功能，下方教程就会推迟几天更新一下对应的内容。

##### 添加源文件
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image114.webp)

等待ARM公司更新功能中... ...
