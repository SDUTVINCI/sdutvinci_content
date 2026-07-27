---
title: "QEMU-KVM虚拟机"
---

QEMU-KVM是一个高性能的虚拟机,以下是以安装RockyLinux9为例.

### 安装必备软件
确保主机已经安装了 QEMU、KVM 和相关工具。以基于 Debian/Ubuntu 的系统为例，执行以下命令：

```bash
sudo apt update
sudo apt install qemu-kvm qemu-img virt-manager virt-install virt-viewer libvirt libvirt-daemon libvirt-daemon-qemu bridge-utils virglrenderer
```

对于基于 Fedora/CentOS/Rocky Linux 的系统，命令可能是：

```bash
sudo dnf install qemu-kvm qemu-img virt-manager virt-install virt-viewer libvirt libvirt-daemon libvirt-daemon-qemu bridge-utils virglrenderer
```

安装完成后，确保当前用户在 `kvm` 组中（否则可能没有权限使用 KVM 加速）：

```bash
sudo usermod -aG kvm $USER
```

**注意：** 修改组后可能需要注销重新登录才能生效。

### 打开KVM硬件加速
运行以下命令检查 KVM 是否启用：

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```

如果输出 `0`，说明 CPU 不支持 KVM 或未开启 VT-x（Intel）/ SVM（AMD），需要在 BIOS 里开启 **Intel VT-x** 或 **AMD SVM** 。

然后检查 KVM 模块是否加载：

```bash

# 验证 KVM 加速可用
lsmod | grep kvm

# 验证当前用户有权限访问 /dev/kvm
ls -l /dev/kvm
```

如果没有输出，加载 KVM 模块：

```bash
sudo modprobe kvm_intel  # Intel 处理器
sudo modprobe kvm_amd    # AMD 处理器
```

### 创建虚拟硬盘文件
你需要为虚拟机创建一个硬盘镜像文件。这里以 qcow2 格式、100GB 为例（你可以根据需要调整大小）。在终端中运行：

```bash
qemu-img create -f qcow2 disk.qcow2 100G
```

这将在当前目录下生成一个名为 `disk.qcow2` 的虚拟硬盘文件。

### 准备 OVMF（UEFI 固件）文件
安装 `OVMF` 包。例如，在 Ubuntu 上可通过以下命令安装：

```bash

# 一般电脑都默认安装过了
sudo apt install ovmf # Debian/Ubuntu
sudo dnf install edk2-ovmf # RHEL/CentOS/Fedora
```

安装后，查找文件路径，通常位于 `/usr/share/OVMF/OVMF_CODE.fd` 或 `/usr/share/ovmf/OVMF.fd`。直接在命令中使用正确路径。(下方脚本中有例子,你只需要确保你的`/usr/share/OVMF/`下有这俩文件即可)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image329.webp)

也可以下载下面这个文件:

暂时无法在飞书文档外展示此内容

1.  编写或调整 run.sh 脚本

将下面的内容写入你的 `run.sh` 文件（你可以使用文本编辑器）。

```bash
sudo vim ./run.sh
```

下面的脚本示例使用 QEMU 启动虚拟机并加载 ISO 镜像：

```bash
#!/bin/bash

qemu-system-x86_64 \
    -machine q35,vmport=off,kernel_irqchip=on \
    -accel kvm \
    -cpu host,kvm=on,vmx=on,migratable=on,+invtsc \
    -smp 8,sockets=1,cores=4,threads=2 \
    -m 8G,slots=4,maxmem=32G \
    -device virtio-gpu-gl-pci,max_outputs=1 \
    -vga none \
    -display sdl,gl=on \
    -audiodev pa,id=pa1,server=unix:/run/user/1000/pulse/native \
    -device ich9-intel-hda \
    -device hda-micro,audiodev=pa1 \
    -device qemu-xhci,id=xhci \
    -device virtio-tablet-pci \
    -device usb-kbd,bus=xhci.0 \
    -bios OVMF-pure-efi64.fd \
    -boot d \
    -blockdev driver=qcow2,node-name=disk1,file.driver=file,file.cache.direct=on,file.aio=io_uring,file.filename=disk.qcow2 \
    -device virtio-blk-pci,drive=disk1 \
    -drive file=kubuntu-20.04.6-desktop-amd64.iso,media=cdrom,if=none,id=cdrom \
    -device usb-storage,drive=cdrom,removable=on \
    -nic user,model=virtio-net-pci,hostfwd=tcp::8022-:22 \
    -monitor stdio \
    -parallel none \
    -serial none \
    -msg timestamp=on

```

保存后给脚本执行权限：

```bash
sudo chmod +x run.sh
```

### 启动虚拟机
确保当前目录下有以下文件：

*   `run.sh`

*   `disk.qcow2`（刚刚创建的虚拟硬盘）

*   `Rocky-9.4-x86_64-dvd.iso`（Rocky Linux 9 安装 ISO）

*   `OVMF-pure-efi64.fd`文件

然后在终端中运行脚本：

```bash
./run.sh
```

此时，QEMU 应该会启动一个窗口，并加载 ISO 镜像进入安装界面。

### 安装系统
在虚拟机窗口中，你会看到 Linux 的安装界面。按照安装向导的步骤进行安装。安装完成后，你可能需要调整启动项，将硬盘作为启动介质（如果默认还是从 CD 启动）。

**建议：** 安装好系统后，关闭虚拟机，再修改 `run.sh` 将 ISO 镜像移除或改为可选启动设备，这样下次启动时就会直接从硬盘启动。

例如，将 `-drive file=Rocky-9.5-x86_64-dvd.iso,media=cdrom` 移除，或者更换为启动顺序参数。

### 其他操作
#### 显卡直通(谨慎,不懂不要乱搞)
此操作的意思是把显卡完全给虚拟机用,实体宿主机就没法用显卡了.

1.  主机和 IOMMU 配置

**(1) 启用 IOMMU（以 Intel 为例）**

编辑 `/etc/default/grub`，在 `GRUB_CMDLINE_LINUX_DEFAULT` 中加入：

```bash
intel_iommu=on iommu=pt
```

对于 AMD 主机则设置 `amd_iommu=on`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image330.webp)

更新 grub 后重启：

```bash
sudo grub2-mkconfig -o /boot/grub2/grub.cfg   # CentOS/Fedora/Rocky 系列

# 或者
sudo update-grub   # Ubuntu/Debian 系统
```

可以在重启后用下面命令确认 IOMMU 是否启用：

```bash
dmesg | grep -e DMAR -e IOMMU
```

如果看到类似“DMAR: IOMMU enabled”之类的信息，就说明生效了。

**(2) 查找你的 NVIDIA 显卡设备 ID** 使用 `lspci -nn | grep NVIDIA` 查找显卡，例如：

```bash
tungchiahui@Dell-G15-5511:~/Downloads$ lspci -nn | grep NVIDIA 
01:00.0 VGA compatible controller [0300]: NVIDIA Corporation GA106M [GeForce RTX 3060 Mobile / M
ax-Q] [10de:2560] (rev a1) 
01:00.1 Audio device [0403]: NVIDIA Corporation GA106 High Definition Audio Controller [10de:228
e] (rev a1)
```

记下上面的 PCI 地址和设备 ID。

**显卡** PCI 地址：`01:00.0` 设备 ID：`10de:2560`

**显卡音频** PCI 地址：`01:00.1` 设备 ID：`10de:228e`

**(3) 绑定设备到 vfio 驱动** 

另一种方式是创建一个 modprobe 配置文件，让 vfio-pci 在加载时绑定这些设备。

1.  创建文件（例如 `/etc/modprobe.d/vfio.conf`）：

```bash
sudo vim /etc/modprobe.d/vfio.conf
```

2.  在文件中写入：

```bash
options vfio-pci ids=10de:2560,10de:228e
```

3.  保存文件后，更新 initramfs：

```bash

# debian系选这个
sudo update-initramfs -u

# 红帽系选这个
sudo dracut --force
```

4.  重启系统。

5.  检查设备绑定情况

重启后，你可以用下面命令检查设备是否已经被 vfio-pci 驱动接管：

```bash
lspci -nnk | grep -A3 "10de:2560"
```

你应该能看到类似：

```YAML
01:00.0 VGA compatible controller [0300]: NVIDIA Corporation GA106M [GeForce RTX 3060 Mobile / Max-Q] [10de:2560]
        Subsystem: ...
        Kernel driver in use: vfio-pci
```

同样用相似的命令检查音频设备（10de:228e）。

```bash
lspci -nnk | grep -A3 "10de:228e"
```

如果看到 `Kernel driver in use: vfio-pci`，说明绑定成功。

**(4) 启动选项**

在run.sh里添加上下面两行

```bash

    # 直通 NVIDIA 显卡
    -device vfio-pci,host=01:00.0,multifunction=on,x-vga=on \
    -device vfio-pci,host=01:00.1 \
```
