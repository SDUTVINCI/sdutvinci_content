---
title: "插件开发"
---

### 插件源码的文件组成Openwrt插件一般使用"MVC"结构：

1. 配置文件
位于 `/etc/config/*`：
```
config server
option username ''
option password ''
```
例示文件中生成了两个待输入的配置。现在需要一个表单页面来管理它。

2. CBI文件
一般位于目录 `/usr/lib/lua/luci/model/cbi/*.lua`：

```lua
require("luci.sys")

-- 页面标题和描述
m = Map("bargo", translate("Bargo Client"), translate("Configure Bargo client, Powered By Sinchie."))

-- 读取配置文件
s = m:section(TypedSection, "server", "")
s.addremove = false
s.anonymous = true

-- 是否启用的选择框
enable = s:option(Flag, "enable", translate("Enable"))
-- 映射我们的配置到输入框
username = s:option(Value, "username", translate("Username"))
pass = s:option(Value, "password", translate("Password"))
pass.password = true

-- 如果点击了保存按钮
local apply = luci.http.formvalue("cbi.apply")
if apply then
    -- 这里是调用我们自己的程序脚本，后面会讲怎么来写这个脚本
    io.popen("/etc/init.d/bargo restart > /dev/null &")
end

return m
```

3. 控制器文件
一般位于系统的`/usr/lib/lua/luci/controller/*.lua`，创建控制器文件后可以在web界面创建一个界面入口（菜单）。脚本结构如下：

```lua
-- module 名称
module("luci.controller.bargo", package.seeall)

function index()
    -- 4 个参数介绍
    -- 1.后台访问路径 admin/services/bargo 
    -- 2.target 动作（call, template, cbi）call 是调用自定义函数，template 调用 html 模板，cbi 调用 openwrt 的公共表单页面
    -- 3.菜单名称 
    -- 4.排序
    entry({"admin", "services", "bargo"}, cbi("bargo"), _("Bargo Client"), 1)
end
```

##### 创建构建文件Makefile
制作 OpenWRT 编译 Makefile 文件需要以下步骤：

1. 安装 OpenWRT SDK，并设置环境变量
2. 在任意目录下，创建一个新的文件夹，用于存放你的应用程序代码
3. 在该目录下，创建 `Makefile` 文件，并按照以下结构填写相应内容：

```makefile

# 这是注释，可以忽略或者修改

include $(TOPDIR)/rules.mk # 引入OpenWRT编译规则
PKG_NAME:=myapp # 应用程序的名称，建议使用小写字母和数字组合
PKG_VERSION:=1.0 # 应用程序的版本号
PKG_RELEASE:=1 # 应用程序的发行版本号

include $(INCLUDE_DIR)/package.mk # 引入OpenWRT中提供的软件包模板
define Package/myapp # 描述应用程序的信息
  SECTION:=net
  CATEGORY:=Network
  TITLE:=My Application
  DEPENDS:=+libopenssl +libcurl +libjson-c 
endef

define Package/myapp/description # 描述应用程序功能和用途
 My Application is a simple program that does something useful.
endef

define Build/Prepare # 准备构建应用程序所需的源码和资源

# 此处可以添加自定义命令，用于准备应用程序的源码和资源
endef

define Build/Configure # 配置应用程序的编译选项

# 此处可以添加自定义命令，用于配置应用程序的编译选项
endef

define Build/Compile # 编译应用程序的源码

# 此处可以添加自定义命令，用于编译应用程序的源码
endef

define Package/myapp/install # 安装应用程序所需的文件和目录
$(INSTALL_DIR) $(1)/usr/bin # 创建安装目录
$(INSTALL_BIN) $(PKG_BUILD_DIR)/myapp $(1)/usr/bin/ # 安装二进制文件
endef

$(eval $(call BuildPackage,myapp)) # 构建软件包并注册到OpenWRT中
```
