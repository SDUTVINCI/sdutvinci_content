---
title: "docker容器里的程序的图形界面弹不出来"
---

（等你成功创建容器后，再回来搞这个操作）

临时允许X11访问： 每次开机在主机上运行以下命令以允许X11访问：(但每次开机都运行一遍命令很麻烦，可以写成脚本开机自启，详见 [自启应用与脚本](/wiki/2024-03-30-linux-jiao-cheng/0600-qi-ta-ke-xuan-pei-zhi#自启应用与脚本))

命令如下:
```bash
xhost +local:docker
```

自启脚本如下:
```bash
#!/bin/bash
# 等待 X Server 就绪（最多等 10 秒）
for i in {1..10}; do
    if [ -n "$DISPLAY" ] && xset q >/dev/null 2>&1; then
        /usr/bin/xhost +local:docker
        exit 0
    fi
    sleep 1
done
```

教程部分如下：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image11.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image12.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image13.webp)
