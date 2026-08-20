---
vinciId: 92e63770-022b-485c-878e-299f6a0a87c2
title: Matlab相机标定方法
tags:
  - 软件算法组
description: 单目相机标定
authors:
  - shangfanxing
contributors:
  - dongjiahui
publishedAt: 2024-09-11T00:00:00.000Z
updatedAt: 2026-08-20T09:47:47.792Z
---

## 单目相机标定

### 概述

相机标定（Camera Calibration）是计算机视觉中的一个关键步骤，其目的是确定相机的内部参数（如焦距、主点坐标、畸变系数等）和外部参数（相机相对于世界坐标系的位姿），以便准确地将三维世界中的点投影到二维图像平面上。相机标定常见有以下几个重要用途：

1. 消除镜头畸变；

2. 3D重建；

3. 计算机视觉中的测量；

4. 图像拼接；

\.\.\. \.\.\.

使用MATLAB对相机内参进行标定，有上手简单，标定数据精确的优点，建议对内统一使用MATLAB进行相机的标注。⭐本文将讲述如何使用MATLAB进行单目相机的标定。

---

### 相机标定板原图

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/a2296761a8698207.webp)

该相机标定板为方格板，图像来源于OpenCV官网，标定板格式为7x10。

使用者可将图片自主缩放至现实世界（15mm/格、20mm/格、25mm/格）等整数尺寸，方便做板子和设置MATLAB参数。

- 图片可以缩放至合适尺寸后打印后，附在平面上，完成标定板制作；

- 图片可以缩放至合适尺寸后，直接将电脑作为标定板，推荐使用word，一定保证图像在电脑上显示的是完全的。



### 获取待标定图像

#### 拍摄图像脚本

当然，如果你想用工业相机连上PC机，使用PC的相机软件拍摄也是一个不错的方法。

- **脚本功能**：拍摄一定数量的棋盘图照片，用于后续的相机标定。
运行时，键盘按下 “**k**” 或者 “**K**” （注意是英文输入法下），截取一张图片；键盘按下 “**q**” 或者 “**Q**” ，退出程序运行，图像截取结束。截取的图片依次输出为 1\.jpg、2\.jpg、3\.jpg…

- 注意拍摄图像的数据量，大概在50\~70张是合适的。

```C++
#include <opencv2/opencv.hpp>
#include <string>
#include <iostream>

using namespace cv;
using namespace std;

int main()
{
        VideoCapture inputVideo(1);
        if (!inputVideo.isOpened())
        {
                cout << "Could not open the input video " << endl;
                return -1;
        }

        //此处可以加上限制图片尺寸的实现
        inputVideo.set(CAP_PROP_FRAME_WIDTH,640);
        inputVideo.set(CAP_PROP_FRAME_HEIGHT,480);

        Mat frame;
        string imgname;
        int f = 1;
        while (1)
        {
                inputVideo >> frame;
                if (frame.empty())
                        break;
                imshow("Camera", frame);
                char key = waitKey(1);
                if (key == 'q' || key == 'Q') // 退出运行
                        break;
                if (key == 'k' || key == 'K') // 截取图片
                {
                        cout << "frame:" << f << endl;
                        imgname = to_string(f++) + ".jpg";
                        imwrite("想要保存图像的地址"+imgname, frame);//////这个地方可以加上你想保存的地址就不会默认保存到工程目录下了
                }
        }
        cout << "Finished writing" << endl;
        return 0;
}

```

#### 注意事项

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/002-7a1204e71fe1.webp)

具体拍摄图像基本如上图所示，要做到对相机标定板多角度拍摄，但也要注意一些问题：

1. 在拍摄过程中，一般是采用 ”相机位置固定，标定板移动“ or ”标定板位置固定、相机位置移动“ 两种拍摄方式，任何一种都行，看自己觉得怎样方便；

2. 要保证自己拍摄的每张图像上，标定板都是完全的（上图image4、5、12、14、15其实就不太合适）

3. 要保证多角度、多距离变化的拍摄，拍摄的尽量清楚，不要模糊或者存在果冻效应的图像；角度变化不要太大，太大了会影响标定效果，[标定板](https://so.csdn.net/so/search?q=%E6%A0%87%E5%AE%9A%E6%9D%BF&spm=1001.2101.3001.7020)最好在视场中心，且占据较大面积。



### 图像标注

#### 标定过程

4. 打开MATLAB，打开加载时间一般比较长，命令行中输入`stereoCameraCalibrator` 回车：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/003-a2b16dc265b5.webp)

5. 点击 Add Images，导入拍照图片。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/004-70b8a2fe2ed2.webp)

6. 修改棋盘格大小为25mm（你的标定板一个格子大小是多少，就写多少）

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/005-b3c877777377.webp)

7. 对于标准相机，菜单栏的option里选择三阶径向畸变和斜切

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/006-3c188a485ec5.webp)

8. 点击Calibratior，开始标定

- 这个时候，MATLAB会将所有图片进行标定，加载完成后，先看一遍所有图片的标定情况，如果有角点标注不全的情况就右击鼠标，remove删除该图片。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/007-55972e0a64c8.webp)

9. 拖拉红线，删除误差大的图像对，使投影误差小于0\.1最好。然后导出标定参数。

- 如果误差小于0\.1\~0\.15的图像数量在总体图像中占比较小，请重新拍摄图像，重新进行标注（该组图像拍摄的较差，标定完后数据不会准确）

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/008-4222de98a426.webp)

10. 把相机参数导出来，点击 Export Camera Parameters。点击确定，就可以看到matlab工作区出现了相机参数。点开这个参数，就可以得到相机的各个参数：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/009-20db72c14270.webp)

到这里标定的工作就完成了，接下来咱们分析上述数据。

---



#### 数据分析

看到该表，我们来说明一下使用MATLAB进行相机标定的到的这些数据各部分分别代表什么含义：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/010-83de4d8d16d2.webp)

1. ImageSize：图像大小

2. Radial Distortion：径向畸变

3. Tangential Distortion：切向畸变

4. World Points：世界坐标系下的点

5. World Units：世界坐标下的单位

6. Estimate Skew：估计倾斜

7. Num Radial Distortion Coefficient：径向畸变系数个数

8. Estimate Tangential Distortion：估计切向畸变

9. Translation Vectors：平移向量

10. Reprojection Errors： 重投影误差

11. Detected Keypoints：检测到的关键点

12. Rotation Vectors：旋转向量

13. Num Patterns：模态数

14. Intrinsics：内参

15. Intrinsic Matrix：内参矩阵

16. Focal Length：焦距

17. Principal Point：主点偏移

18. Skew：偏斜

19. Mean Reprojection Error：平均重投影误差

20. Reprojected Points：重投影点

21. Rotation Matrices：旋转矩阵



（1）相机矩阵：包括焦距（fx，fy），光学中心（Cx，Cy），完全取决于相机本身，是相机的固有属性，只需要计算一次，可用矩阵表示如下：\[fx, 0, Cx; 0, fy, Cy; 0,0,1\];

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/f1a05f2f93bd05ee.webp)

*s* 是图像的 skew 参数，用于表示水平和垂直方向的像素缩放比例差异。通常情况下，*s* 很小，接近于 0，s所在位置即K矩阵左下角，也就是左下角不一定是1



（2\) 畸变系数：畸变数学模型的5个参数 D = （k1，k2， P1， P2， k3）；

相机内参：“相机矩阵”\+“畸变系数”统称为相机内参，在不考虑畸变的时候，相机矩阵也会被称为相机内参；

---



### 扩充\-鱼眼相机标定

#### 【原理参考】

[https://zhuanlan.zhihu.com/p/551277548?utm_id=0]()

#### 【标定参考】

**拍照同上。区别在这里**

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/012-097c2922f974.webp)

- camera model 选择 fisheye, estimate alignment 选择勾，因为官网解释说

Estimate the axes alignment, specified as the comma\-separated pair consisting of ‘EstimateAlignment’ and false or true\. Set to true if the optical axis of the fisheye lens is not perpendicular to the image plane\.
估计坐标轴的对齐，指定为逗号分隔的一对，由’EstimateAlignment’和false或true组成。如果鱼眼镜头的光轴不垂直于像平面，则设置为true。

- 计算结果cameraParams中查看Intrinsics

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/013-1467b1297443.webp)

Mapping Coefficients: 映射系数, $[ a 0 ，a 2， a 3， a 4 ] ，a1=1$
Image Size: 图像大小
Distortion Center: 畸变中心
Stretch Matrix: 拉伸变换 $ \begin{bmatrix}c&d \\
e&1 \end{bmatrix}
$



---

---

---



## 双目相机标定

### 概述

双目相机标定是计算机视觉中的一个重要步骤，其意义在于通过校准两个相机的内部参数和相对位置（外部参数），实现高精度的三维重建、深度计算和立体匹配等应用。

双目相机的标定相对于单目相机相对麻烦一些，请看下述过程。

---

### 双目相机

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/fb7e188ece046321.webp)

该样式本实验室有一个类似产品，可以用于学习双目视觉，但不建议使用到具体工程，精度和使用效果极差。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/f43a3a56a3f7e520.webp)



### 相机标定板原图

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/a2296761a8698207.webp)

该相机标定板为方格板，图像来源于OpenCV官网，标定板格式为7x10。

使用者可将图片自主缩放至现实世界（15mm/格、20mm/格、25mm/格）等整数尺寸，方便做板子和设置MATLAB参数。

- 图片可以缩放至合适尺寸后打印后，附在平面上，完成标定板制作；

- 图片可以缩放至合适尺寸后，直接将电脑作为标定板，推荐使用word，一定保证图像在电脑上显示的是完全的。



### 获取待标定图像

#### 拍摄图像脚本

**【注意】**相对来说双目相机在拍摄图像的时候图像（如下图），因此不建议直接使用PC设备的相机进行拍摄，后期还需要再将左右图像分开，建议直接使用脚本进行拍摄。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/016-b93fb9657638.webp)

**【脚本】**

- 该代码摁下空格会拍摄一帧图像，并将左右相机图像自动分开，保存到Video\_L和Video\_R文件夹中，这两个文件夹的位置可以指定；

- 拍摄图片数量50\~70对是合适的。

```C++
#include"stdafx.h"
#include<iostream>
#include<string>
#include<sstream>
#include<opencv2/core.hpp>
#include<opencv2/highgui.hpp>
#include<opencv2/videoio.hpp>
#include<opencv2/opencv.hpp>
#include<stdio.h>

using namespace std;
using namespace cv;

const char* keys =

{
        "{help h usage ? | | print this message}"

        "{@video | | Video file, if not defined try to use webcamera}"
};



int main(int argc, const char** argv)            //程序主函数

{
        CommandLineParser parser(argc, argv, keys);
        parser.about("Video Capture");


        if (parser.has("help"))                      //帮助信息
        {
                parser.printMessage();
                return 0;
        }

        String videoFile = parser.get<String>(0);


        if (!parser.check())
        {
                parser.printErrors();
                return 0;
        }



        VideoCapture cap;                      //定义摄像头对象，准备对每一帧进行处理
        if (videoFile != "")
        {
                cap.open(videoFile);          //打开视频流文件
        }

        else
        {

                cap.open(1);                             //打开相机，电脑自带摄像头一般编号为0，外接摄像头编号为1，主要是在设备管理器中查看自己摄像头的编号。

                cap.set(CV_CAP_PROP_FRAME_WIDTH, 2560);  //设置捕获视频的宽度 2560
                cap.set(CV_CAP_PROP_FRAME_HEIGHT, 720);  //设置捕获视频的高度 720

        }

        if (!cap.isOpened())                         //判断是否成功打开相机
        {
                cout << "摄像头打开失败!" << endl;
                return -1;
        }

        Mat frame, frame_L,frame_R;


        cap >> frame;                                //从相机捕获一帧图像

        Mat grayImage;                               //用于存放灰度数据

        double fScale = 0.5;                         //定义缩放系数，对2560*720图像进行缩放显示（2560*720图像过大，液晶屏分辨率较小时，需要缩放才可完整显示在屏幕）

        Size dsize = Size(frame.cols*fScale, frame.rows*fScale);

        Mat imagedst = Mat(dsize, CV_32S);

        resize(frame, imagedst, dsize);

        char key;

        char image_left[200];

        char image_right[200];

        int count1 = 0;

        int count2 = 0;

        namedWindow("图片1",1);

        namedWindow("图片2",1);



        while (1)
        {
                key = waitKey(50);

                cap >> frame;                            //从相机捕获一帧图像

                resize(frame, imagedst, dsize);          //对捕捉的图像进行缩放操作


                frame_L = imagedst(Rect(0, 0, 640, 360));  //获取缩放后左Camera的图像  640*360

                namedWindow("Video_L", 1);

                imshow("Video_L", frame_L);                //显示左摄像头拍摄的图像


                frame_R = imagedst(Rect(640, 0, 640, 360)); //获取缩放后右Camera的图像

                namedWindow("Video_R", 2);

                imshow("Video_R", frame_R);

                if (key == 27) //按下ESC退出

                        break;

                if (key == 32) // 按下空格开始拍照图片保存在工程文件下
                {
                        sprintf_s(image_left, "image_left_%d.jpg", ++count1);

                        imwrite(image_left, frame_L);

                        imshow("图片1", frame_L);

                        sprintf_s(image_right, "image_right_%d.jpg", ++count2);

                        imwrite(image_right, frame_R);

                        imshow("图片2", frame_R);
                }
         }

         return 0;
}

```



**【运行结果】**

**左镜头**拍摄到的图：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/017-cb4a93eaf118.webp)

**右镜头**拍摄到的图：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/018-64e0132eebb6.webp)

两个镜头拍摄到的图在近距离有明显的位置差异。



#### 注意事项

同单目相机标定相同，但是对于双目相机要求两个相机都要满足之前的要求。



### 图像标注

#### 标定过程

1. 打开MATLAB，在命令行输入**stereoCameraCalibrator**，出现如下界面：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/019-31959a444487.webp)

2. 然后将上面的“**Skew**”、“**Tangential Distortion**”以及“**2 Coefficients**”等选项选上，将“3 Coefficients”选项去掉，如下：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/020-7b51d3e18826.webp)

3. 然后点击 **Add images（添加图像）**，出现如下界面：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/021-afa14b8f8d97.webp)

4. **Camera 1 **代表左摄像头，**Camera 2** 代表右摄像头，分别选择存放着左右图像的文件夹，需要特别注意的是棋盘格的边长应该根据打印的实际大小填写** \(例如20mm\)**，单位可以选择

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/022-dc7e0e355b0b.webp)

5. 点击Calibrate按钮，开始标定：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/023-50874a4959b7.webp)

6. 校准

- 从下图可以看到，**平均的标定误差**以及 **标定过程中误差较大**的的图像对。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/024-0376edb8b74c.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/025-de9a7a42a8ca.webp)

7. 点击选择不想接受的误差直方图，可以直接在左边的图像对中找到对应的图像，右键选择“**Remove and Recalibrate**”：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/026-ed6018a6d8d6.webp)

可以重复上述步骤，直到认为误差满足标定需求为止。

8. 点击Export camera parameters, 并点击“OK”。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/027-a67b596ec868.webp)



#### 数据分析

- 标定结束后，会得到如下标定参数：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/028-2a33d2fded5d.webp)

CameraParameters1与CameraParameters2为左右摄像头的内部参数，RotationOfCamera2与TranslationOfCamera2为两个摄像头的旋转、平移参数。

---



##### 摄像机内参矩阵

双击框框这里：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/029-e5ce1f0ea45a.webp)

CameraParameters1与CameraParameters2中包含如下文件：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/030-094c598d067c.webp)

IntrinsicMatrix存放的是摄像头的内参，只与摄像机的内部结构有关，老版本MATLAB需要先**转置**再使用。

例如：左相机的参数，**点击CameraParameters1**

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/031-5bccffe4c5ff.webp)

**IntrinsicMatrix **存放的是摄像头的内参

**RadialDistortion **和** TangentialDistortion **中存放的是畸变参数

先看一下 **IntrinsicMatrix** 参数，双击一下 **IntrinsicMatrix **

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/032-5a5121f0d892.webp)

这个和OpenCV中是**转置的**关系，注意不要搞错。

对应 ：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/033-7eb34e3bee75.webp)

---



##### 畸变参数

**RadialDistortion** 和**TangentialDistortion **中存放的是畸变参数，

**RadialDistortion 为 径向畸变**，摄像头由于光学透镜的特性使得成像存在着径向畸变，可由K1，K2，K3确定。

**TangentialDistortion **为** 切向畸变**，由于装配方面的误差，传感器与光学镜头之间并非完全平行，因此成像存在切向畸变，可由两个参数**P1，P2**确定。

不过在使用时，需要注意参数的排放顺序，即**K1，K2，P1，P2，K3**。切记不可弄错，否则后续的立体匹配会出现很大的偏差。

例如， 左相机为例：

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2024/09/11/034-bd08bbe354ff.webp)

**RadialDistortion ：**0\.016004928431323 , 0\.041062484186359  对应 **K1，K2  ； K3默认为0**

**TangentialDistortion ：**0\.005480700176874 , 0\.003979285309815   对应  **P1，P2**

所以在opencv中使用时，**K1，K2，P1，P2，K3**顺序 ：

0\.016004928431323 , 0\.041062484186359 , 0\.005480700176874 , 0\.003979285309815  ,0
