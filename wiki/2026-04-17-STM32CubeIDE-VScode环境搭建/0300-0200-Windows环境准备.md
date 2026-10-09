---
title: "Windows环境准备"
---

## 环境介绍
本教程环境介绍：

1.  系统：Windows 10 LSTC 2021

2.  系统内核：Windows NT

3.  架构：X86_64(amd64)

其他Windows 10 及以上环境也可以。

## 安装各种软件与环境

### 安装CubeMX

官网下载：https://www.st.com.cn/zh/development-tools/stm32cubemx.html
机器人队网站下载（需要登陆，但速度很快）：https://vinci.sdut.edu.cn/downloads

下载完后解压出来`SetupSTM32CubeMX-6.18.1.exe`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791511828314-a637f627.webp)

这个下面的路径建议默认，你也可以选择其他地方，但需要保证纯英文路径和有权限的路径（如果你不懂，建议默认）。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791511851361-d2547914.webp)

等待进度条滚完就结束了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791511882760-6afeb701.webp)

### 安装VScode

先百度官网：https://code.visualstudio.com/

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791511471149-6a59b1d1.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791511495297-3992ae6d.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791511502777-59799060.webp)

这个下面的路径建议默认，你也可以选择其他地方，但需要保证纯英文路径和有权限的路径（如果你不懂，建议默认）。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791511510472-d41e491e.webp)

必须像这样勾选

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791511567177-dd046980.webp)

等待进度条滚完就结束了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791511580556-99475f90.webp)

### 安装openOCD

#### 安装

官方链接：https://openocd.org/pages/getting-openocd.html

下载链接：https://github.com/xpack-dev-tools/openocd-xpack/releases

然后下载`xpack-openocd-版本号-win32-x64.zip`：

下载完后解压到C盘，比如：`C:\xpack-openocd-0.12.0-7`。

#### 添加环境变量

```text
设置
→ 系统
→ 系统信息
→ 高级系统设置
→ 环境变量
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791448937614-a1881ef9.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791448939610-fdf7b7cb.webp)

编辑 → 新建，把`C:\xpack-openocd-0.12.0-7\bin`加上

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791448940693-8c46184c.webp)

保存后，打开`powershell`：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791513263463-d612b91c.webp)

```powershell
openocd --version
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791448941884-bef3a46b.webp)

#### 安装驱动

Linux 下我们需要关注 /dev/bus/usb 和 udev rules。

Windows 下没有 udev，因此不需要配置 Linux 那一套权限规则。

但是 Windows 需要给不同的调试器安装合适的 USB 驱动。

```text
Linux → udev 权限
Windows → USB Driver
```

| 调试器 | Windows 是否需要额外装驱动 | 推荐做法 |
|---|---|---|
| **ST-Link V2 / V2-1** | **需要** | 安装 ST 官方 `STSW-LINK009` |
| **STLINK-V3** | 通常系统能识别，但仍推荐装 ST 官方组件 | 安装官方驱动最省心 |
| **J-Link** | **需要官方 J-Link 软件包** | 安装 `J-Link Software and Documentation Pack`，里面自带 USB Driver |
| **DAPLink / CMSIS-DAP v1** | **一般不需要** | 使用 Windows 自带 HID 驱动 |
| **DAPLink / CMSIS-DAP v2** | **一般不需要** | 正规 DAPLink 使用 WinUSB，可自动绑定 |
| 某些国产 CMSIS-DAP 魔改探针 | 不一定 | 真识别不了再考虑 WinUSB/Zadig |

##### ST-Link

官方链接：https://www.st.com.cn/zh/development-tools/stsw-link009.html
机器人队网站下载（需要登陆，但速度很快）：https://vinci.sdut.edu.cn/downloads

解压出来

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791514117240-1b5989b3.webp)

右键`stlink_winusb_install.bat`以管理员身份运行

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791514160704-7a571b7a.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791514164923-eab246c4.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791514169049-603627e5.webp)

##### SEGGER J-Link

官方链接：https://www.st.com.cn/zh/development-tools/stsw-link009.html
机器人队网站下载（需要登陆，但速度很快）：https://vinci.sdut.edu.cn/downloads

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791514339329-cc07b0cb.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791514360974-71133882.webp)

右键下载好的exe以管理员身份运行，比如我的叫`JLink_Windows_V984_x86_64.exe`

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791514502584-63060429.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791514511458-cc1182c5.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791514608278-7739e12b.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791514636597-868e4658.webp)

等待进度条滚完就结束了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/17/1791514643624-eb60b6a7.webp)

## 重启

做完上述操作后，傻逼Windows大概率需要重启一下