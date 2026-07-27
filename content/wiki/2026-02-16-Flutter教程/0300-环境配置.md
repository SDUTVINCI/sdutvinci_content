---
title: "环境配置"
---

### Linux

https://dart.dev/get-dart

#### 安装方式

##### APT

```bash
sudo apt-get update && sudo apt-get install apt-transport-https
```

```bash
wget -qO- https://dl-ssl.google.com/linux/linux_signing_key.pub \
  | sudo gpg  --dearmor -o /usr/share/keyrings/dart.gpg
```

```bash
sudo apt-get update && sudo apt-get install dart
```

##### DNF

```bash
sudo dnf install dnf-plugins-core
```

```bash
sudo dnf copr enable albertop/dart
sudo dnf install dart
```

#### 验证是否安装成功
```bash
dart --version
```
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/02/16/1771223439849.webp)


#### VScode插件下载

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/02/16/1771223560894.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/02/16/1771223587027.webp)

#### 测试

创建第一个项目`demo01_helloworld.dart`

```dart
void main() 
{
  print('Hello, World!');
}
```

点击运行
![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/02/16/1771223942698.webp)
