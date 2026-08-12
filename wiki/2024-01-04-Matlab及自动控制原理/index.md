---
title: Matlab及自动控制原理
authors:
- dongjiahui
contributors:
- zhangchangfei
- shangfanxing
publishedAt: '2024-01-04T00:00:00.000Z'
updatedAt: '2025-10-21T08:52:31.000Z'
---

# 安装Matlab

## 注意事项

### 挂载iso的命令：

1. 创建挂载点

```Bash
sudo mkdir -p /mnt/iso
```

2. 以只读方式挂载 ISO 镜像

```Bash
sudo mount -o loop ./filename.iso /mnt/iso
```

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/01/04/001-b1b26bda0cfd.webp)

3. 卸载挂载点

```Bash
sudo umount /mnt/iso
```

4. 删除挂载点

```Bash
sudo rmdir /mnt/iso
```

### 建议用root方式安装：

这样才能安装在`/usr/local/MATLAB/R2024a`

```Bash
cd /mnt/iso
sudo ./install
```

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/01/04/002-9a2b87914298.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/01/04/003-bdaf763b8a99.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/01/04/004-1cc0656e565e.webp)



# Matlab基础命令





# 自动控制原理

## 经典控制理论

### PID算法





## 现代控制理论

### 卡尔曼滤波算法\(最优滤波算法\)



### LQR\(线性二次型最优控制算法\)





# Simulink

### Simulink基础使用

### Simulink系统控制模型

#### 自动控制原理

#### Simulink建立系统控制模型



# Robotics System Toolbox

官网：

[https://ww2.mathworks.cn/products/robotics.html]()

[https://petercorke.com/toolboxes/robotics-toolbox/]()

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/01/04/005-578b45f19e04.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/01/04/006-0549474f7e05.webp)

如图，就是安装成功。
