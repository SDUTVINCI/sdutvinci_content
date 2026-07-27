---
title: "Linux装机教程"
---

### 实体机（双系统，最推荐）
#### 环境准备
1.  至少留出160GB的硬盘空间（可以和Windows是同一块硬盘，也可以是单独的一块硬盘）

2.  一个里面没存东西的大于8GB的U盘（也可以没有）

3.  一个聪慧的大脑

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image1.webp)

#### 关闭安全启动Secure Boot
如果下面没有，可以自行B站搜自己品牌的电脑如何关闭安全启动

##### 联想拯救者
##### 戴尔游匣Intel版
关机，然后在点开机的一瞬间，一直点摁F12（反复狂摁），直到进入一个白色的BIOS界面。

进入`BIOS SETUP`，找到`Boot Configuration`，然后往下滑，找到`Enable Secure Boot`这个选项，给他关闭就行了。

然后再点下面的`Apply Changes`按钮，再点右下角`EXIT`按钮重启即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image2.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image3.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image4.webp)

##### 惠普暗影精灵
##### 华硕天选
开机狂按F2进入BIOS模式,右下角找到高级模式打开

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image5.webp)

关闭安全模式

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image6.webp)

#### 开启核显
安装Linux的时候建议开着核显防止Linux图形显示出现问题。

可以通过打开任务管理器来看自己是否开着核显。（轻薄本不用管，轻薄本肯定开着核显的）

①像你如果是Intel的CPU，那么这里GPU0或者GPU1会显示intel xxxxxx。这样是正确的。

②如果你是AMD的CPU，那么这里GPU0或者GPU1会显示amd xxxxxx。这样是正确的。

③如果你发现，你的GPU0是NVIDIA开头的，而且没有GPU1，那么说明你关闭了核显，请按照下面的操作来关闭独显直连开启核显。（如果你是AMD独显，无所谓，开不开独显直连都一样，就不用管了，但是大部分人都不可能是AMD的独显，所以基本排除这一项了）

对于很多游戏本，支持独显直连，所以可能已经默认关闭了核显，只允许了独显运行，所以此时你需要关闭独显直连，开启核显（独显直连对于打游戏提升很大）

如果你确定你确实已经开启了独显直连，请看下方不同电脑品牌的解决方案。

如果下方没有，可以自行去B站搜自己品牌电脑如何切换到混合输出模式。

##### 联想拯救者
进入联想电脑管家或者Lenovo Ventage，把图形输出模式切换为混合输出即可，

然后重启查看任务管理器确认是否核显已经被打开了。

##### 戴尔游匣Intel版
关机，然后在点开机的一瞬间，一直点摁F12（反复狂摁），直到进入一个白色的BIOS界面。

进入BIOS设置，找到`Display`，然后往下滑，找到`Enable Hybird Graphic`这个选项这个选项，给他打开就行了。然后点`Apply Changes`，再退出`EXIT`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image7.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image8.webp)

再重启后，打开任务管理器确认核显是否已经被打开了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image9.webp)

##### 戴尔游匣AMD版
##### 惠普暗影精灵
##### 华硕天选
进入BIOS的高级设置模式

高级-显示模式-Dynamic

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image10.webp)

保存退出

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image11.webp)

#### 安装ADK
https://learn.microsoft.com/zh-cn/windows-hardware/get-started/adk-install

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image12.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image13.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image14.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image15.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image16.webp)

下载之前可以关掉梯子，这样可能走的是国内的网，可能会加速下载。（如果你的节点够快，可能走外网下载更快）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image17.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image18.webp)

同样下载下面这个

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image19.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image20.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image21.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image22.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image23.webp)

这样就安装结束了，可以重新打开傲梅。

#### 下载DiskGenius
在Windows系统上打开百度搜索DiskGenius并下载。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image24.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image25.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image26.webp)

找一个纯英文目录创建一个DiskGenius文件夹，建议在任意一个磁盘分区里的Program Files文件夹下新建DiskGenius文件夹。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image27.webp)

将刚才压缩包里的所有文件解压到这个DiskGenius文件夹中。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image28.webp)

右键拖住它，拖到桌面再放手。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image29.webp)

点在这里创建快捷方式。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image30.webp)

打开这个快捷方式

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image31.webp)

这样就安装成功了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image32.webp)

#### 下载傲梅分区助手
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image33.webp)

https://www.disktool.cn/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image34.webp)

下载完解压到一个地方（可以是桌面），双击打开。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image35.webp)

如果想安装到C盘，直接点立即安装即可。

如果想安装到D盘，则将默认路径前面的C改成D就行了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image36.webp)

随后安装完毕就可以打开软件了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image37.webp)

把那个安装包.exe删掉就行了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image38.webp)

#### 下载Ventoy
https://www.ventoy.net/cn/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image39.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image40.webp)

如果进不去下面的界面，请你确保你的网络环境（使用能够绕过GFW的东西，懂得都懂）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image41.webp)

找一个纯英文目录创建一个Ventoy文件夹，建议在任意一个磁盘分区里的Program Files文件夹下新建Ventoy文件夹。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image42.webp)

把刚才下载的压缩包里的东西解压到这里。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image43.webp)

右键拖住它，拖到桌面再放手。再点击创建快捷方式。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image44.webp)

双击快捷方式打开。这样就是安装完毕了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image45.webp)

#### 下载镜像
百度搜索 bfsu mirror

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image46.webp)

##### X86_64(amd64)
1.  Ubuntu KDE 22.04（Kubuntu）更推荐

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image47.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image48.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image49.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image50.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image51.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image52.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image53.webp)

2.  Ubuntu Gnome 22.04

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image54.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image55.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image56.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image57.webp)

3.  Fedora KDE

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image58.webp)

依次点击下方的文件夹，那个42是版本号，选最新版即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image59.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image60.webp)

#### 硬盘分区安装方案
重点看红字。其他内容你不需要管。

| 场景分类 | 场景 | 硬盘分配情况 | EFI 分区来源 | Linux 分区方案 | 注意事项 |
|:---|:---|:---|:---|:---|:---|
| 同硬盘分区 | ① 同一块硬盘，Win 已安装在硬盘前面 | 硬盘1：前半部分是Win的C盘，D盘等，在末尾新建分区给 Linux | 复用 Windows 已有 EFI 分区（通常 100–300MB，FAT32，/boot/efi 挂载） | 新建 /（ext4）+ swap（可选） | 安装 Linux 引导时要选现有 EFI 分区，避免覆盖 Windows 引导文件。 |
| 异硬盘混合分区 | ② 两块硬盘，第二块硬盘末尾装 Linux，前面存放 Windows 数据，比如E盘 | 硬盘1：Windows 系统+EFI；硬盘2：前面是Win的数据区，末尾分给 Linux | 复用 硬盘1 的 EFI 分区 | 硬盘2 新建 / + swap | 引导还是写入硬盘1 的 EFI；安装时要特别小心不要把 EFI 装到硬盘2。 |
| 异硬盘独立分区 | ③ 两块硬盘，第二块硬盘全空 | 硬盘1：Windows 系统+EFI；硬盘2：完全给 Linux | 复用 硬盘1 的 EFI 分区（推荐）或者在硬盘2 新建一个 EFI 分区（双 EFI 并存适合老鸟） | 硬盘2 新建 / + swap | 如果新建 EFI，需在 BIOS/UEFI 中手动调整启动顺序；更灵活，但稍复杂。 |

你需要确认你属于哪种情况，下方教程我将以场景一为例子，其他情况也类似。

```bash
───────────────────────────────────────────────────
场景1：同硬盘分区（单硬盘）  
磁盘布局：  
[EFI(共享)] [Win C:] [空闲区域] [Linux Swap] [Linux /]  
        │      │               │             │  
        └──────┴───────────────┴─────────────┘  
        ↑ 所有系统共用此EFI分区（挂载至/boot/efi）
───────────────────────────────────────────────────
场景2：异硬盘混合分区（双硬盘）  
硬盘1: [EFI(共享)] [Win C:] [D: Win存储区]  
        │  
硬盘2: [E: Win存储区 [Linux Swap] [Linux /]  
        ↑  
        └─ Linux安装时挂载硬盘1的EFI至/boot/efi
───────────────────────────────────────────────────
场景3：异硬盘独立分区（双硬盘）  
硬盘1: [EFI_Win] [Win C:]  
        │  
硬盘2: [Linux Swap] [Linux /]  
        │          │  
        └──────────┘  
        ↑ 非独立EFI分区（Linux安装时挂载硬盘1的EFI至/boot/efi）
───────────────────────────────────────────────────
场景3：异硬盘独立分区（双硬盘）  
硬盘1: [EFI_Win] [Win C:]  
        │  
硬盘2: [EFI_Linux] [Linux Swap] [Linux /]  
        │          │  
        └──────────┘  
        ↑ 独立EFI分区（需手动配置UEFI启动顺序，适合老鸟）
───────────────────────────────────────────────────
```

#### 创建安装介质
##### 方法一：U盘法
**（如果没有U盘请看本地法）**

待肝，几乎和本地法差不太多。

###### 创建Ventoy U盘
把一个大于8GB的U盘插入电脑，然后打开ventoy，选择好U盘，点击安装即可。（注意，此过程可能会格式化U盘，请把数据先备份，等安装完Ventoy后再把数据移动回来即可。）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image61.webp)

###### 安装微PE并下载WinPE的镜像
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image62.webp)

https://www.wepe.com.cn/

点击下载2.3版本

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image63.webp)

有钱可以捐赠，没钱可以先不捐赠，假装自己捐赠了也行点击已捐赠。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image64.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image65.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image66.webp)

下载完把下载的exe复制到你想存放软件的盘（比如C盘）里的Program Files下，新建一个WePE文件夹存放。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image67.webp)

双击打开exe文件

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image68.webp)

选择输出位置

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image69.webp)

选择桌面并保存

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image70.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image71.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image72.webp)

然后去桌面，把刚才生成的镜像复制到U盘里，不要放在U盘的某个文件夹，直接放在U盘里就行。

比如我的U盘是H盘。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image73.webp)

###### 复制Linux镜像iso
再把你上面下载好的Linux安装镜像也复制到U盘里。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image74.webp)

###### 设置启动项Ventoy
把U盘插在电脑上，重启电脑，然后打开DiskGenius.

点击导航栏上的`工具`选项，然后再点`设置UEFI BIOS启动项`。

找到U盘，比如我的U盘是爱国者aigo的，所以应该选aigo。

然后点下次从该启动项启动，重启就可以了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image75.webp)

进入这个界面说明成功了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image76.webp)

###### 在Ventoy里启动Linux安装介质镜像
找到你想装的linux的镜像，比如kubuntu22.04,回车进入

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image77.webp)

再点回车即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image78.webp)

进入下面这个界面就说明成功了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image79.webp)

##### 方法二：本地法
###### 本地法创建
本方法是在本地开创一个存放安装介质（安装文件）的分区，为该分区设置上引导，从而安装Linux系统。

**这里分的区不是放置Linux的分区。放置的是安装Linux的文件。（类似安装包）**

1.  查看自己想要放安装介质（也就是安装文件，大概8GB，建议在C盘）还剩多少空间。比如我是还有231GB.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image80.webp)

2.  然后打开傲梅分区助手，选中你想要放置8GB安装介质的磁盘分区（比如我选的是C盘），然后点击右边的调整/移动分区。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image81.webp)

3.  点击分区大小右边的按钮

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image82.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image83.webp)

4.  计算`分区后的未分配空间`

①如果你不打算在这个磁盘分区（比如我这里是C盘）里安装Linux（也就是`硬盘分区安装方案的场景`②和3️⃣），

那么只需要在下图中的`分区大小`中减掉8GB，让`分区后的未分配空间`变为8GB即可。

（如果你打算在这个磁盘的分区安装Linux，也就是`硬盘分区安装方案的场景①`，那么往下看，用②的法）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image84.webp)

②如果你打算在这个磁盘分区中安装Linux，那么需要计算一下到底要压缩多少空间。

`分区后的未分配的空间=8GB+打算给Linux的空间大小。`

比如我剩余的磁盘分区空间为231GB，打算给Linux分配100GB（这里我只是举例子，实际上给Linux的空间不要这么小，至少160GB吧），那么我需要的`分区后的未分配的空间`\=8GB+100GB=108GB。那么就需要在`分区大小`上减去108GB，让`分区后的未分配的空间`\=108GB.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image85.webp)

5.  然后点击确定进行分区，接着在主界面左上角点提交。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image86.webp)

6.  接着点执行。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image87.webp)

7.  因为我修改的是C盘，所以需要进入WinPE里进行操作， **如果你不是在C盘操作的，可能不用做这一步** 。（如果你不能选择Windows PE这个按钮，请去上方教程看看ADK是否安装成功了？）

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image88.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image89.webp)

等待创建PE结束

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image90.webp)

他会自动进入PE进行分区操作。

勾上自动修复分区中的错误和勾上完成操作后自动关机，等待进度条结束即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image91.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image92.webp)

1.  然后开机就可以看到我压缩的磁盘分区变小了，我的是C盘，如下图。可以看到C盘后面多了一块未分配的空间。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image93.webp)

2.  点击这块未分配的空间，然后点击分区

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image94.webp)

3.  给他分一个FAT32格式的8GB大小的磁盘分区。（记住盘符是多少，比如我的是D盘）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image95.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image96.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image97.webp)

4.  然后找到你下载的Linux安装介质.iso，双击打开。（或者右键点`装载mount`）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image98.webp)

5.  然后去此电脑里找到这个被加载的镜像，点进去。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image99.webp)

6.  再打开FAT32格式的这个磁盘分区。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image100.webp)

7.  将镜像里的所有内容全部复制到FAT32的磁盘分区中。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image101.webp)

8.  右键镜像`弹出Eject`虚拟镜像。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image102.webp)

9.  然后打开DiskGenius，点击导航栏上的`工具`选项，然后再点`设置UEFI BIOS启动项`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image103.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image104.webp)

10.  点击添加启动项，文件路径选择刚才Fat32分区（比如我的是D盘）。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image105.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image106.webp)

11.  依次找到/EFI/boot/bootx64.efi

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image107.webp)

12.  可以命名为Linux Install，点击保存，并选择下一次重启以这个引导当首选项。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image108.webp)

13.  重启，可以看到下方已经进入了Linux安装界面。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image109.webp)

重启后是这个样子，说明进入安装界面了。（对于硬盘分区安装方案的场景①的此时已经可以开始安装了，对于还没给Linux预留安装空间的请按电源按键关机并重启回Windows）

如果你安装失败了，你还想进这个安装界面，则在DiskGenius里像上面那样在BIOS UEFI设置里选中引导，勾上`下一次从该项启动`，然后重启即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image110.webp)

###### 删除本地安装介质(安装完后再删）
当你使用本地法安装Linux的话，你完成Linux的安装需要删除掉开辟的这8GB的FAT32的空间。

打开傲梅分区助手。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image111.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image112.webp)

然后再找到C盘，点调整分区。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image113.webp)

然后把剩下的空闲空间全部还给C盘。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image114.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image115.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image116.webp)

#### Linux安装空间的预留
##### 硬盘分区安装方案的场景①
```bash
───────────────────────────────────────────────────
场景1：同硬盘分区（单硬盘）  
磁盘布局：  
[EFI(共享)] [Win C:] [空闲区域] [Linux Swap] [Linux /]  
        │      │               │             │  
        └──────┴───────────────┴─────────────┘  
        ↑ 所有系统共用此EFI分区（挂载至/boot/efi）
───────────────────────────────────────────────────
```

如果你是本地法+场景一，则已经创建过这个地方了，（如下图）跳过本节。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image117.webp)

但如果你是U盘法，则接着往下看。

场景一是和Windows装在一个硬盘里。

1.  打开傲梅分区助手。找到你想安装Linux的Windows的硬盘。点移动分区。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image118.webp)

2.  `分区后的未分配空间`就是你要安装Linux的空间。设置分区大小，或者拖动下面的图形按钮都可以更改分区大小。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image119.webp)

3.  然后点击确定进行分区，接着在主界面左上角点提交。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image120.webp)

4.  接着点执行。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image121.webp)

5.  因为我修改的是C盘，所以需要进入WinPE里进行操作， **如果你不是在C盘操作的，可能不用做这一步** 。（如果你不能选择Windows PE这个按钮，请去上方教程看看ADK是否安装成功了？）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image122.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image123.webp)

等待创建PE结束

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image124.webp)

他会自动进入PE进行分区操作。

勾上自动修复分区中的错误和勾上完成操作后自动关机，等待进度条结束即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image125.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image126.webp)

1.  然后开机就可以看到我压缩的磁盘分区变小了，我的是C盘，如下图。可以看到C盘后面多了一块未分配的空间。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image127.webp)

##### 硬盘分区安装方案的场景②
```bash
───────────────────────────────────────────────────
场景2：异硬盘混合分区（双硬盘）  
硬盘1: [EFI(共享)] [Win C:] [D: Win存储区]  
        │  
硬盘2: [E: Win存储区 [Linux Swap] [Linux /]  
        ↑  
        └─ Linux安装时挂载硬盘1的EFI至/boot/efi
───────────────────────────────────────────────────
```

像场景一一样，把你第二个硬盘里的后半部分像场景一一样给压缩取消掉挂载就行。

##### 硬盘分区安装方案的场景3️⃣
```bash
───────────────────────────────────────────────────
场景3：异硬盘独立分区（双硬盘）  
硬盘1: [EFI_Win] [Win C:]  
        │  
硬盘2: [Linux Swap] [Linux /]  
        │          │  
        └──────────┘  
        ↑ 非独立EFI分区（Linux安装时挂载硬盘1的EFI至/boot/efi）
───────────────────────────────────────────────────
场景3：异硬盘独立分区（双硬盘）  
硬盘1: [EFI_Win] [Win C:]  
        │  
硬盘2: [EFI_Linux] [Linux Swap] [Linux /]  
        │          │  
        └──────────┘  
        ↑ 独立EFI分区（需手动配置UEFI启动顺序，适合老鸟）
───────────────────────────────────────────────────
```

直接把整个第二个硬盘所有分区全部删除，格式化即可。

#### 安装Linux
以Kubuntu 22.04（Ubuntu KDE 22.04）为例子，其他发行版差不多其实也。

##### grub设置
在下面这个界面按E。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image128.webp)

在这一行后面加上 `noauto toram`

这个`noauto`是必须要写的。那个`toram`如果你电脑内存大于等于16GB（小于8GB谨慎，可能会崩溃），则可以填，这样安装速度会提升200+%，甚至300+%，甚至无上限。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image129.webp)

然后按crtl+X启动系统。

##### 基础安装选项
这里语言一定选择English比较好。（这样/home分区底下所有文件夹都是英文，不会出现中文。）

然后选Install Kubuntu.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image130.webp)

点continue

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image131.webp)

连接网络，先点connect to this network,然后找到wifi名，输入上密码，点右边的connect,出现connected后，点continue.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image132.webp)

这里和我选的一样即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image133.webp)

##### 分区设置（重要！！！）
###### 与Windows使用同一个EFI分区
####### 初步设置
这里选Manual,咱们手动分区。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image134.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image135.webp)

找到咱们之前预留给Linux的那块磁盘区域，比如我的是预留了100GB的那块区域，大概Size是107452MB，一定别找错磁盘区域了。如果你这里不是`free space`，那么左键选中，点`delete`.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image136.webp)

####### swap分区建立
选中这块区域，双击或者点击Add按钮。

use as选`swap area.`

给swap区域分区，这个Size请填写你的`内存大小（运存大小）+2GB`*`（单位MB）`*，比如我电脑内存为32GB，那么32+2=34GB，再用`34*1024=34816MB，`所以填`34816`.

tips（可以不看）：为何要这么做是为了开启Linux的休眠功能，如果你的swap分区大小设置有误，Linux休眠功能可能会出问题。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image137.webp)

####### 根目录/分区建立
再选中剩余的这块区域，双击或者点击Add按钮。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image138.webp)

use as选 ext4

然后挂载点选`/`（也就是根目录）

Size使用默认的就行（默认就是把剩下的最大容量全给`根目录`）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image139.webp)

####### 取消挂载其他无关分区
如下图，swap和/已经被创建成功了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image140.webp)

把上图除了**efi和刚才创建的那俩分区** 的其他分区全部点change更改为do not use the partition.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image141.webp)

####### 设置UEFI引导位置
把最下方那个`bootloader`设置为`efi`分区的磁盘号。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image142.webp)

点击右下角Install Now，看看是不是下面只显示修改了swap和ext4俩分区，如果有其他的，说明有无关分区没被取消挂载。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image143.webp)

###### 独立的EFI分区（不推荐）
这种适用于你有一个空的硬盘（不是空的磁盘分区，是一整个硬盘全是空的），然后整个硬盘全分给Linux。（当然这种情况也适用于上面那个和Windows共用一个EFI分区的方法）

适合老鸟。

优点是崩了直接格掉整个硬盘，很干净。

####### 初步设置
这里选Manual,咱们手动分区。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image144.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image145.webp)

找到咱们预留的空硬盘。（整个硬盘全空，我单独找了个空的128GB的硬盘来写教程，如下图的nvme0n1）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image146.webp)

####### EFI分区建立（比前面的那种方法多一个EFI分区）
双击你的硬盘底下的free space,

然后size设置为1024,use as设置为EFI分区。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image147.webp)

####### swap分区建立
紧接着再点free space。

选中这块区域，双击或者点击Add按钮。

use as选`swap area.`

给swap区域分区，这个Size请填写你的`内存大小（运存大小）+2GB`*`（单位MB）`*，比如我电脑内存为32GB，那么32+2=34GB，再用`34*1024=34816MB，`所以填`34816`.

tips（可以不看）：为何要这么做是为了开启Linux的休眠功能，如果你的swap分区大小设置有误，Linux休眠功能可能会出问题。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image148.webp)

####### 根目录/分区建立
再选中剩余的这块区域，双击或者点击Add按钮。

use as选 ext4

然后挂载点选`/`（也就是根目录）

Size使用默认的就行（默认就是把剩下的最大容量全给`根目录`）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image149.webp)

####### 取消挂载其他无关分区
把上图除了刚才创建的**efi，swap,根目录/** 的其他分区全部点change更改为do not use the partition.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image150.webp)

####### 设置UEFI引导位置
把最下方那个`bootloader`设置为`efi`分区的磁盘号。比如我这里是nvme0n1p1。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image151.webp)

点击右下角Install Now，看看是不是下面只显示修改了efi,swap和ext4仨分区，如果有其他的，说明有无关分区没被取消挂载。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image152.webp)

##### 最后设置
时区选个东八区的就行。默认应该是中国上海🇨🇳时间，也可以选中国香港🇭🇰，中国台湾🇨🇳时间都可以。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image153.webp)

用户名设置为英文开头的就行，我这里是tungchiahui

密码选个简单点的，纯数字的，因为以后要经常输入，所以尽可能简单点。

电脑名字随便起，英文开头就行。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image154.webp)

等着就行了，与网速，电脑性能都有关。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image155.webp)

重启。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image156.webp)

如果你是U盘法，请拔掉U盘（大部分情况不拔也行，只要你的开机首选项不是U盘），然后点回车重启

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image157.webp)

开机啦！

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image158.webp)

#### 双系统切换
在点了开机键进入了grub（黑色的界面）

上面显示Ubuntu选项就是进Linux。

上面显示Windows选项就是进Windows。

#### 彻底删除Linux
##### 分区格式化
打开diskgenius，找到给Linux分的盘。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image159.webp)

挨个选中，点删除分区。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image160.webp)

点保存更改。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image161.webp)

##### 引导删除
###### 和Windows共用EFI
打开DiskGenius双击Windows的ESP分区。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image162.webp)

进入EFI文件夹，找到ubuntu文件夹，右键`强制删除`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image163.webp)

然后后面不管给你啥警告，一直点删除，和确定删除等字眼。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image164.webp)

这样引导就删除完毕了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image165.webp)

###### 独立的EFI
则直接格式化掉整个硬盘即可。

找到自己第二个盘。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image166.webp)

右键选中整个硬盘，点`删除所有分区`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image167.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image168.webp)

### 普通虚拟机（不推荐）
(不用教，自己百度解决)

### Windows Subsystem For Linux 2(Windows的Linux子系统2)（勉强的法子）
#### 简介与优缺点
1.  什么情况下推荐？

无多余硬盘，没法实体装Linux。此时最优选择就是WSL2。而非VMware等虚拟机。

2.  优缺点：

    1.  优点：

        1.  WSL2是最好的Linux发行版;(bushi)

        2.  性能损耗极低。且比在Windows上跑深度性能 **强非常非常多;** （如下图，时间的数值越低证明性能越高）

        3.  非常方便调用USB设备，可以USB直通;

        4.  非常方便使用英伟达显卡的CUDA与cuDNN;

        5.  非常方便Win与WSL2互相传文件等协同操作;

        6.  WSL2中甚至可以在Linux的终端中使用Windows软件;

        7.  非常方便使用Windows的VScode远程开发;

    2.  缺点：

        1.  无法调用本机摄像头，或说调用本机摄像头会巨麻烦。（虽无法调用本机摄像头，但是可以使用USB的摄像头）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image169.webp)

#### 安装教程
##### 启动WSL2子系统和虚拟平台
1.  方法一(图形界面)

用Win+R输入`appwiz.cpl`：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image170.webp)

点击OK进入 **程序和功能/Programs and Features** 界面，点击 **启用或关闭Windows功能/Turn Windows features on or off** ：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image171.webp)

选择 **适用于Linux的Windows子系统/Windows Subsystem for Linux** 和 **虚拟机平台/Virtual Machine Platform** 功能：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image172.webp)

2.  方法二(命令行)

```PowerShell

# 开启 Linux 子系统
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

# 开启虚拟机平台
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

##### 将WSL2设置为默认版本
用管理员权限打开powershell

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image173.webp)

升级WSL并设置默认版本为WSL2

```PowerShell

# 更新 wsl
wsl --update

# 将 wsl 版本设置为 wsl2
wsl --set-default-version 2
```

##### 安装Linux发行版
1.  方式一(商店下载,推荐，网速快，不用翻)

直接打开Microsoft Store搜索对应的发行版下载即可(比如Ubuntu 22.04 LTS)

要是跑ROS1建议20.04LTS.

ROS2的话更建议用22.04LTS，对于初学者来说，教程多才是最好的，ROS2 Humble和ROS2 Jazzy在gazebo上区别还是很大的，先学教程多的，以后可以慢慢再转Jazzy。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image174.webp)

2.  方式二(命令行安装)

```PowerShell

# 列出可安装的 Linux 版本
#（需要科学手段）
wsl --list --online
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image175.webp)

选择对应发行版并安装

```PowerShell

# Ubuntu安装完毕后(可以重启)
wsl --install -d Ubuntu-22.04
```

##### 查看系统状态
```PowerShell

# 查看 wsl
wsl -l

# 查看 wsl 运行版本或模式
wsl -l -v

# 设置发行版为使用 wsl 2 （如果版本为 2 则不需要）
wsl --set-version <distro> 2

# 选择要注销的版本 （如果想卸载再用此命令）
wsl --unregister <distro>
```

输出Version为2即正常

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image176.webp)

##### 设置用户名与密码
直接在Powershell里输入wsl命令进入子系统

```PowerShell

# 进入子系统
wsl
```

输入用户名(随便设置，建议字母+数字，或者纯字母)

输入密码(建议低于6位的数字，并且在输入密码时，终端上不会显示输入的字符，但是已经正常输入了)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image177.webp)

##### 卸载发行版
如果出现问题，就卸载wsl2出问题的发行版

第一步在powershell里输入下列命令查询发行版信息。

```Plain Text
wsl -l -v
```

第二步在微软应用商店将Ubuntu卸载。(该步一般可以省略)

第三步要敲下方命令进行注销:

```Plain Text
wsl --unregister 发行版名称
```

第四步可以从安装发行版这步重新装。

#### 配置各种环境
##### 换源
(同实体机LINUX，往下找教程)

##### 英伟达显卡
###### 显卡驱动
不用安装，只要你的Windows安装了驱动，你的WSL2就已经安装好驱动了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image178.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image179.webp)

###### CUDA
https://developer.nvidia.com/cuda/wsl

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image180.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image181.webp)

在下方的网站，选择合适的版本。

https://developer.nvidia.com/cuda-toolkit-archive

**(同实体机LINUX，往下找教程，唯一与实体机不同的地方如下图)**

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image182.webp)

挨行敲一遍

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image183.webp)

敲完后，输入`nvcc -V`检测是否安装成功，如果出现下图，证明没配置环境。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image184.webp)

（请详看下方教程的**安装****CUDA****的配置环境来看**如何配置环境）

###### cuDNN
(同实体机LINUX，往下找教程)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image185.webp)

##### 配置网络
WSL2默认是NAT模式，也就是拿Windows当网关，WSL2是Windows的下级设备，这样的话，WSL2和Windows并不处于同一网段，WSL2只能和Windows以及Windows的上游设备进行通信，无法与和Windows同局域网的同网段设备进行通信。想要和同网段的ROS、ROS2设备进行通信，只能要么端口转发，要么设置DDS，都是非常麻烦的。

WSL2还提供了好几种模式，有一个模式叫Mirrored模式 **（Windows 10 22H2及以上才能开启）** ，相当于Docker的`--network host`，这种方式是让WSL2和Windows使用相同的IP，这样的话，不论是WSL2还是Windows都可以访问同网段的局域网设备了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image186.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image187.webp)

重启WSL2

```PowerShell
wsl --shutdown
wsl
```

##### USB直通
https://learn.microsoft.com/zh-cn/windows/wsl/connect-usb

直通的USB设备只供WSL2使用，应该Windows是没法使用的，如果想使用，请解绑（关闭直通）。

首先现在电脑上安装usbipd，下面是github链接，进去点releases。

https://github.com/dorssel/usbipd-win

点击x64.msi下载并安装。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image188.webp)

安装完后，然后开启你的WSL2

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image189.webp)

右键开始菜单，选择终端（以管理员身份运行）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image190.webp)

列出设备

```cpp
usbipd list
```

下图这个busid为2-1的就是我的目标。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image191.webp)

绑定并链接设备，这个2-1要换成你对应的busid

```cpp
usbipd bind --busid 2-1 --force
usbipd attach --wsl --busid 2-1
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image192.webp)

然后在wsl中的终端里查看设备。

```cpp
lsusb

ls /dev
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image193.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image194.webp)

如果你中途插拔了USB，WSL2找不到了的话，请解绑设备。

```cpp
usbipd unbind --busid 2-1
```

然后再像上方那样重新绑定即可。

##### VScode远程开发
(一定要把工程放在Linux中开发，别放在Win磁盘中，否则I/O效率会很低)

安装该插件(WSL2有自己的远程开发插件，无需使用SSH)。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image195.webp)

连接WSL2

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image196.webp)

远程开发成功。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image197.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image198.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image199.webp)

##### Linux终端使用Windows软件
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image200.webp)

##### OpenGL有问题
```bash
sudo apt update
sudo apt upgrade
sudo apt install mesa-utils

#然后重启wsl2即可
```
