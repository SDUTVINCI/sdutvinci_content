---
title: "拓展功能"
---

### Wine
### Android
#### I卡、A卡
1.  Wayland-Waydroid(体验超级好)

https://waydro.id/

#### N卡(So Nvidia Fxxk U!)
1.  QEMU-KVM虚拟机 + BlissOS-X86

    https://www.bilibili.com/video/BV1aS411P7Wh

    ![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2024/03/30/image331.webp)

    3.  比较推荐的配置

    ```bash
    qemu-system-x86_64 \
            -M q35,vmport=off \
            -accel kvm \
            -cpu qemu64,+sse3,+sse4.2 \
            -smp 8,cores=4 \
            -m 6G \
            -usb \
            -device virtio-tablet \
            -device virtio-keyboard \
            -device usb-tablet \
            -bios OVMF-pure-efi64.fd \
            -net user,hostfwd=tcp::5555-:5555,hostfwd=tcp::8022-:8022 \
            -net nic,model=virtio \
            -audio sdl,model=hda \
            -display sdl,gl=on,show-cursor=on \
            -device virtio-vga-gl,xres=1920,yres=1080 \
            -drive file=disk.qcow2,media=disk,if=virtio,cache=writeback \
            -drive file=Bliss-v16.9.7-x86_64-OFFICIAL-gapps-20240911.iso,media=cdrom \
            -monitor stdio \
            -serial none
    ```
    5.  常用操作：

        1.  全屏：Ctrl+Alt+F

        2.  adb调试

            1.  使Linux与Android用ADB连接。

            ```bash

            # 连接安卓
            adb connect 127.0.0.1

            # 打开shell
            adb -s 127.0.0.1:5555 shell

            # 使用root权限
            su
            ```

        3.  中文输入法

            1.  可以下载google输入法：按Shift+space进行中英文切换（这样有效防止与linux本机的中英文切换冲突）
