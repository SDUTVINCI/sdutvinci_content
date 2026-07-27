---
title: "常见问题"
---

### FreeRTOS使用ARMCLANG(AC6)编译报错的问题
1.  如果你是使用的模板，那么将模板中的“其他注意事项”文件夹中的Middlewares文件夹复制到根目录即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image115.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image116.webp)

2.  如果你是自己从Windows上从0开始创立的工程(没有使用模板)，那么需要你去寻找CubeMX下载的固件源码

比如Linux中固件源码在`/home/tungchiahui(你自己的用户名)/STM32Cube/Repository/`中。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image117.webp)

假如你是F103，那么打开`STM32Cube_FW_F1_V1.8.5`文件夹。

如果你是F407，那么打开`STM32Cube_FW_F4_V1.28.0`文件夹。

找到路径`/home/tungchiahui/STM32Cube/Repository/STM32Cube_FW_F1_V1.8.5/Middlewares/Third_Party/FreeRTOS/Source/portable/`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image118.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image119.webp)

将这个GCC文件夹里的ARM_CM3文件夹复制到 **工程文件夹** 对应的RVDS文件夹下。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image120.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image121.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image122.webp)

### 错误执行cmake配置
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image123.webp)

如果遇到`error cbuild: error executing 'cmake' configuration`这种错误。则删掉MDK-ARM文件夹下的tmp文件夹。再重新编译即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image124.webp)

```bash
#删除tmp文件夹
rm -rf ./tmp
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image125.webp)

### 修改汇编语言的编译器为ARMClang集成的汇编编译器
这是个警告，不影响正常使用，但是咱们尽量可以修改一下。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image126.webp)

```Plain Text
Warning: A1950W: The legacy armasm assembler is deprecated. 
Consider using the armclang integrated assembler instead.
0 Errors, 1 Warning
```

暂时没找到解决方案

### 出现某些工具没被下载的情况
按下面的arm tools然后进入下面的界面选择对应版本,再点击update tool registry即可.(最常见的就是编译器和调试器的库没自动下载.)

如果不知道需要哪些工具,建议可以全部都选上最新版本.(亲测全选最新版本是可以正常使用的)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image127.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/01/21/image128.webp)
