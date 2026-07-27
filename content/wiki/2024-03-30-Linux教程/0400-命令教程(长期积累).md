---
title: "命令教程(长期积累)"
---

### 大多数命令
https://www.runoob.com/linux/linux-tutorial.html

### 包管理工具命令
常见的有apt,dnf,yum,pacman,pkcon等等。

### KDE Dolphin 以root打开
kde5

```bash
pkexec env DISPLAY=$DISPLAY XAUTHORITY=$XAUTHORITY KDE_SESSION_VERSION=5 KDE_FULL_SESSION=true dolphin
```

kde6

```bash
pkexec env DISPLAY=$DISPLAY XAUTHORITY=$XAUTHORITY KDE_SESSION_VERSION=6 KDE_FULL_SESSION=true dolphin
```

kde6.3.3及以上

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image270.webp)

直接图形化就可以了。
