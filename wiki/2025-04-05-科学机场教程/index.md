---
vinciId: e00a8b45-a5f5-48c4-b990-bf42b03cdc2f
title: 科学机场教程
description: 说明
authors:
  - dongjiahui
publishedAt: 2025-04-05T00:00:00.000Z
updatedAt: 2026-08-20T15:06:59.941Z
tags:
  - 通用资料
---

## 说明

**仅供学习使用，请勿用于非法用途。**

**為中華之復興而讀書！**

本文旨在分享 自建内网管理和远程运维工具的使用教程，适用于个人服务器、NAS、家用实验环境等场景。教程将以 Clash 为例，讲解如何配置多协议网络代理，实现：
安全访问自己在海外或本地的VPS服务器与 NAS
远程调试、维护个人服务与应用
灵活管理网络流量，提高工作与学习效率

注意事项：
1\. 本文教程仅面向 个人自用环境，不涉及任何向公众提供服务或翻墙访问禁止网站的内容。
2\. 教程内容均属于 网络运维与安全管理 范畴，使用者需遵守中国大陆🇨🇳法律法规。
3\. 本文提供的配置方法仅供学习、测试和自我运维使用，禁止将本教程用于非法用途。


## 教程

### 带GUI

#### Clash Verge Rev

##### 下载与安装

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/001-49e97bd6114b.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/002-18f0631c7638.webp)

[https://github.com/clash-verge-rev/clash-verge-rev]()

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/003-e32b33ceb83b.webp)

```Bash
# Windows 双击安装即可

# Debian系
sudo apt install ./clash包名.deb

# Fedora系
sudo dnf install ./clash包名.rpm
```

##### 添加订阅

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/004-3810fc43c9d5.webp)

把你的订阅链接\(自己整去\)输入上

***\(建议请不要使用非法的订阅链接，自己想搭建家庭内网vpn访问nas还可以，别用vpn搞其他事情，请遵守中国大陆🇨🇳法律\)***



![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/005-4017691283ad.webp)

这个代理里有内容就说明添加订阅成功了。



##### 开启

红色的地方必开，蓝色的部分根据需求开

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/006-5b5ed886cffd.webp)

##### 管理节点

下面界面可以切换节点\(这里的节点指自有服务器的端点\)以及右上角可以切换代理\(这里的代理指多协议网络管理，内网访问，运维工具\)模式

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/007-96fb8e3d0800.webp)

##### 测试

点击下方网站看是否可以进入

[https://www.google.com.hk/?hl=zh-cn]()

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/008-fa702c3dce15.webp)





### 无GUI

#### Shell Crash

##### 环境介绍

树莓派5 \+ Ubuntu Server 24\.04 LTS无图形界面版。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/009-ca9b52390d7d.webp)

##### 安装shell crash

[https://github.com/juewuy/ShellCrash]()

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/010-ee3a4087b615.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/011-3f6784cbdeb9.webp)

上面这俩任意一个

```bash
sudo -i #切换到root用户，如果需要密码，请输入密码
bash #如已处于bash环境可跳过
export url='https://fastly.jsdelivr.net/gh/juewuy/ShellCrash@master' && wget -q --no-check-certificate -O /tmp/install.sh $url/install.sh  && bash /tmp/install.sh && source /etc/profile &> /dev/null
```

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/012-9cf4690634d2.webp)

选公测版

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/013-ceca53d23c76.webp)

选在/etc下安装

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/014-58a3c22093ec.webp)

##### 添加订阅

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/015-cf0145b97b39.webp)

```Bash
crash
```

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/016-cd49fd69484a.webp)

由于我们是普通的linux且只想代理本机，所以选2\.（如果你是openwrt等想用于软路由设备的发行版，选择1）

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/017-01f9d7f9284e.webp)

一般咱们家用的ipv4都是NAT分出来的，所以本机设备都没有公网ipv4,所以选择0即可。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/018-fb89eaa130ec.webp)

选择导入。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/019-d2bee4765579.webp)

选择在线生成。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/020-cae2261520eb.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/021-f49b6fd2df70.webp)

将你购买的机场链接输入上

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/022-f66f32b35ae4.webp)

选择1,只导入一个，直接开始生成配置文件。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/023-197c49ad6c15.webp)

生成完毕后，再输入1,在线生成配置文件。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/024-f671e502040c.webp)

立即启动服务即可。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/025-adc02d7e8e95.webp)

##### 设置开机自启

一般默认开机自启，如果没有开机自启的话，可以看下面。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/026-88e72ba9e8ac.webp)

先输入4内核启动设置，再输入1开启shellcrash开机启动（由于我已经开机自启了，所以我这里的1成了禁止开机自启）

##### 管理节点

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/027-58f70609083f.webp)

先9更新，再4安装本地面板

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/028-f41003e7130e.webp)

根据自己喜好安装面板，我这里选择2\.

在一个同局域网同网段的设备的浏览器输入`树莓派5的ip:9999/ui`即可。\(比如，你可以用你的电脑去调工控机的节点配置\)

例如：

```Bash
192.168.31.10:9999/ui
```

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/029-222aa0d58fa7.webp)

##### 测试

首先先获取shellcrash监听的端口\(没测试\):

方法一: 进入浏览器管理ui界面，点击设置，可以看到端口，我这里端口是7890。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/030-45d57e7a555d.webp)



方法二:查看配置文件，即可知道端口，比如我的是7890。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/031-cedcdbc5a647.webp)

左键点击上面这个链接

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/032-9617dda1d751.webp)





用curl获取下Google网页看看是否成功，可以判别是否代理成功。

下方的`7890`需要修改为你的shellcrash监听的对应的端口，一般默认为 （我也不知道，用上面的方法获取吧）。

```Bash
curl -x socks5h://127.0.0.1:7890 https://www.google.com.hk
```

如果出现下图这种情况，就是代理成功了，也正常连接Google谷歌了。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/033-b4e321515f37.webp)

##### 常见问题

1. 订阅配置文件格式错误

如果添加订阅显示格式错误，请按下列步骤:

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/034-6ee90fc8aef7.webp)

选择5，选择肥羊服务器，再选择1重新生成配置文件即可。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/035-b434e6e15813.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2025/04/05/025-adc02d7e8e95.webp)
