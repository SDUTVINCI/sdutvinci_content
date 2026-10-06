---
vinciId: 433b8088-08dd-57d6-9490-71caf2077c1f
title: VScode远程开发
description: 插件1：微软Docker工具
authors:
  - dongjiahui
publishedAt: 2024-10-03T00:00:00.000Z
updatedAt: 2026-08-18T16:48:11.506Z
---

如何SSH那样在VScode中远程开发容器里的系统呢?

使用VScode的插件即可,上述教程已经教你安装了扩展,现在直接使用扩展即可.

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782047780157.webp)

执行上图操作后,我们的VScode就已经控制Ubuntu24容器了.

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782047838513.webp)

之前的run命令已经挂载了本地磁盘的`/home/用户名`路径了，所以在Docker容器中可以轻松访问本地的工程。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/1782047951841.webp)

输入`/home/`可以看到你宿主机的用户目录也被加载到docker的ubuntu24里了,这样代码可以全部存到这里,但开发环境却是docker里的ubuntu24.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/10/03/image25.webp)
