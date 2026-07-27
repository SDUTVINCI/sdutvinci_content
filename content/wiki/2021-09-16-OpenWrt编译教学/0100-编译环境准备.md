---
title: "编译环境准备"
---

### 环境说明本教程以乌班图22.04作为环境
请确保编译过程全局魔法

### 基础编译过程
设置环境变量（不知道有没有用）：
```bash
注意使用root编译会报错，可以在/etc/profile 最后添加export FORCE_UNSAFE_CONFIGURE=1,然后执行
export FORCE_UNSAFE_CONFIGURE=1
```

1. 系统软件包更新 
```bash
sudo apt-get -y update && sudo apt-get -y upgrade
```

2. 安装依赖关系与编译工具链 
```bash
sudo apt install -y ack antlr3 aria2 asciidoc autoconf automake autopoint binutils bison build-essential \
bzip2 ccache cmake cpio curl device-tree-compiler fastjar flex gawk gettext gcc-multilib g++-multilib \
git gperf haveged help2man intltool libc6-dev-i386 libelf-dev libglib2.0-dev libgmp3-dev libltdl-dev \
libmpc-dev libmpfr-dev libncurses5-dev libncursesw5-dev libreadline-dev libssl-dev libtool lrzsz \
mkisofs msmtp nano ninja-build p7zip p7zip-full patch pkgconf python2.7 python3 python3-pip libpython3-dev qemu-utils \
rsync scons squashfs-tools subversion swig texinfo uglifyjs upx-ucl unzip vim wget xmlto xxd zlib1g-dev
```

3. 下载 OpenWrt 源码 
```bash
git clone https://github.com/openwrt/openwrt.git && cd openwrt 
git clone https://github.com/coolsnowwolf/lede && cd lede

# 还有个
git clone https://github.com/istoreos/istoreos
```

（源码有很多，本教程以官方源码和LEDE为例） 

4. 切换适合分支 

openwrt-22.03 已将 iptables 移除，为避免兼容性问题，暂时切换到 openwrt-21.02分支: 
```bash
git checkout openwrt-21.02
```

5. 更新并安装 feeds 软件源
```bash
./scripts/feeds update -a && ./scripts/feeds install -a
```

（以下为校园网防检测部署）

6. 加入模块
```bash
git clone https://github.com/Zxilly/UA2F.git package/UA2F
git clone https://github.com/CHN-beta/rkp-ipid.git package/rkp-ipid 
```

7. 配置编辑
```bash
make menuconfig
```

配置编译 rdp-ipid:
- Kernel modules → Other modules → kmod-rkp-ipid

配置编译 ua2f:
- Network → Routing and Redirection → UA2F

配置编译 iptables 模块（Firewall中）

选中：
- iptables-mod-filter
- iptables-mod-ipopt
- iptables-mod-u32

基本界面配置：
- LuCI → Collections → luci （lede 默认已选）
- LuCI → Modules → Translations → Chinese Simplified (zh_Hans) （lede 默认已选）
- LuCI → Modules → luci-compat
- LuCI → Applications → luci-app-ttyd（网页终端）
- LuCI → Themes → xxxxx (根据需要自己选管理界面的主题，如果路由器储存太小，建议就维持默认的)

其他配置：
- 添加EXT4硬盘格式支持：Kernel modules > Filesystem > kmod-fs-ext4
- 添加USB支持（如不添加可能会不响应键盘）：Kernel modules > USB Support

网卡驱动配置：
- 经查找网卡使用的是Intel IG211-AT，走的是PCIE通道，这里要选用e1000e
- Kernel modules > Network Devices将kmod-e1000e，kmod-igb驱动选中

无线网卡驱动：
- 内核中的无线驱动找到RTL8821AE的驱动
- Kernel modules > Wireless Drivers

> 作者：KANSUNG
> 链接：https://www.jianshu.com/p/5190ab903820
> 来源：简书

添加其他工具（可选）：
- LuCI > Applications -> luci-app-openclash
- LuCI > Applications -> luci-app-adblock
- LuCI > Applications -> luci-app-ddns-go

添加必要组件：
- Kernel modules -> Network Support -> kmod-tun （openclash TUN模式必须）

排除冲突组件：
- Base system -> dnsmasq (取消勾选，因为默认会安装dnsmasq-full，需要排除dnsmasq避免冲突报错）

编辑配置文件：
- 使用 vim .config，在开头添加一行（UA2F 插件需要）：
```bash
CONFIG_NETFILTER_NETLINK_GLUE_CT=y
```

8. 编译和配置内核
```bash
make kernel_menuconfig
make -j1 V=sc kernel_menuconfig  # 日志更详细
```

配置路径：
Networking support →
  Networking options →
    Network packet filtering framework (Netfilter) （要先选中再进去）→
      Core Netfilter Configuration → 

选中以下选项：
- Netfilter NFNETLINK interface
- Netfilter LOG over NFNETLINK interface
- Netfilter connection tracking support
- Connection tracking netlink interface
- NFQUEUE and NFLOG integration with Connection Tracking

9. 编译
下载编译工具：
```bash
make download -j$(nproc) V=s
```

开始编译：
```bash
make -j$(nproc) V=s
make -j1 V=sc 2> build.log  # 错误输出到日志
```

二次编译需要更新源码：
```bash
git pull
```

#### 重新配置如果需要重新配置：
```bash
rm -rf ./tmp && rm -rf .config
make menuconfig
make -j$(($(nproc) + 1)) V=s   # 多线程编译失败后自动进入单线程编译，失败则输出详细日志
```

#### 编译更换其它CPU架构的固件（建议操作）
清除旧的编译产物：
```bash
make clean
```
> 在源码有大规模更新或者内核更新后执行，以保证编译质量。此操作会删除/bin和/build_dir目录中的文件。

清除旧的编译产物、交叉编译工具及工具链等目录：
```bash
make dirclean
```
> 更换架构编译前必须执行。此操作会删除/bin和/build_dir目录的中的文件(make clean)以及/staging_dir、/toolchain、/tmp和/logs中的文件。

清除 OpenWrt 源码以外的文件（可选）：
```bash
make distclean
```
> 除非是做开发，并打算 push 到 GitHub 这样的远程仓库，否则几乎用不到。此操作相当于make dirclean外加删除/dl、/feeds目录和.config文件。

还原 OpenWrt 源码到初始状态（可选）：
```bash
git clean -xdf
```
> 如果把源码改坏了，或者长时间没有进行编译时使用。

清除临时文件：
```bash
rm -rf tmp
```
> 删除执行make menuconfig后产生的一些临时文件，包括一些软件包的检索信息，删除后会重新加载package目录下的软件包。若不删除会导致一些新加入的软件包不显示。

删除编译配置文件：
```bash
rm -f .config
```
> 在不删除的情况下如果取消选择某些组件它的依赖组件不会自动取消，所以对于需要调整组件的情况下建议删除。

注意：所有操作不要使用root用户

> 版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。
> 
> 原文链接：https://blog.csdn.net/u010674953/article/details/129280724
