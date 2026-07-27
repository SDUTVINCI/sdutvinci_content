---
title: "个性化配置"
---

### 修改默认主题进入路径`openwrt/feeds/luci/collections/luci`，修改`Makefile`（适用于openwrt版本19.07，不同的版本，会稍微不同）：
- 将LUCI_DESCRIPTION中，bootstrap 替换为 argon
- 将LUCI_DEPENDS中，+luci-theme-bootstrap 替换为 +luci-theme-argon

原文链接：https://blog.csdn.net/pyt1234567890/article/details/107442792

##### 个性化主题以 "argon"主题为例，可编辑性强要更改OpenWrt源码中的主题，包括Argon主题，可以按照以下步骤操作：

1. 进入`feeds/luci/themes/`目录
2. 找到Argon主题所在的目录（一般为`luci-theme-argon`），并进入该目录
3. 打开`root/usr/share/luci/static/resources/view/themes/argon/header.htm`文件，该文件定义了Argon主题的顶部导航栏、LOGO等内容
4. 在header.htm文件中找到需要修改的内容，并进行相应的编辑和保存。例如，将LOGO图片路径指向自己的图片文件，或者调整导航栏的布局和样式
5. 如果需要修改Argon主题的其他部分，例如底部导航栏、颜色方案等，可以在`root/usr/share/luci/static/resources/view/themes/argon/`目录下找到对应的文件进行编辑

##### 个性化主机名修改`package/base-files/files/bin/config_generate`，将`"OpenWrt"`替换

##### 自定义默认密码修改passwd文件

##### 终端横幅修改文件：
```bash
./package/base-files/files/etc/banner
```
横幅生成地址：http://www.network-science.de/ascii/

##### 无线更改修改文件：
```bash
/package/kernel/mac80211/files/lib/wifi/mac80211.sh    # (ssid)
```

修改自定义版本号：openwrt_release
