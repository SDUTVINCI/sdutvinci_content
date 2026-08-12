---
title: BM、SGBM算法（双目相机测距）
authors:
- shangfanxing
publishedAt: '2024-07-19T00:00:00.000Z'
updatedAt: '2024-07-28T08:07:26.000Z'
---

## 概述

双目测距（Stereo Vision）是一种利用两个摄像头获取场景的深度信息的方法。它模仿人类的双眼，通过计算两个摄像头拍摄的图像之间的视差（disparity），来估算物体的距离。以下是双目测距的基本流程：

---

## 设备\-双目相机



![image\.png](http://10.0.0.4:5244/COS/tungwebsite/assets/images/wiki/2024/07/19/fb7e188ece046321.webp)

该样式实验室有一个类似产品，可以用于学习双目视觉，但不建议使用到具体工程，精度和使用效果极差。\-\-\-\-\-\-\-双目BGR相机

![image\.png](http://10.0.0.4:5244/COS/tungwebsite/assets/images/wiki/2024/07/19/f43a3a56a3f7e520.webp)



## 理论知识

[https://www.bilibili.com/video/BV1Ft4y1u7fZ?vd_source=2a70e8cbccfb71570f2b97be1853e0df]()

### **理想模型**

![image\.png](http://10.0.0.4:5244/COS/tungwebsite/assets/images/wiki/2024/07/19/003-06dd0b150be1.webp)

由公式我们得知，除了相机参数外，在图像上我们需要获取的为参数 d ，也就是**视差。**



### **视差**

视差是同一个空间点在两个相机成像中对应的x坐标的差值，它可以通过编码成灰度图来反映出距离的远近，离镜头越近的灰度越亮。

![image\.png](http://10.0.0.4:5244/COS/tungwebsite/assets/images/wiki/2024/07/19/004-ff006b2ca749.webp)

---



双目相机的理想模型做到了两个镜头的焦距 f 相同，两镜头的光轴平行。

事实上，咱们买到任何设备都有误差，不可能做到理想的状态，因此，双目相机的现实模型应该是这样的：

### **现实模型**

![image\.png](http://10.0.0.4:5244/COS/tungwebsite/assets/images/wiki/2024/07/19/005-b11082f91d2c.webp)

并且，对于双目相机的每个镜头拍摄出来的BGR图像也都存在一定的畸变，因此在进行距离解算之前，要完成图像**去畸变**和**立体矫正**等操作，下面分别讲一下。



### **去畸变操作**

其实对应的，我们在单目测距的实现中，就使用过图像的去畸变操作，对应使用的函数为`undistort()`， 函数用于校正图像的径向和切向畸变。它需要相机内参矩阵和畸变系数。

```C++
void undistort(InputArray src, OutputArray dst, InputArray cameraMatrix, InputArray distCoeffs, InputArray newCameraMatrix = noArray());
```

- `src`: 输入的畸变图像。

- `dst`: 输出的校正后的图像。

- `cameraMatrix`: 相机的内参矩阵。

- `distCoeffs`: 相机的畸变系数。

- `newCameraMatrix`: （可选）新的相机内参矩阵。如果未提供，使用 `cameraMatrix`。



对应的在双目图像去畸变的过程中我们使用`initUndistortRectifyMap()` 函数用于计算图像校正和重投影的映射表，这些映射表可以用于 `remap() `函数来校正图像。

```C++
void initUndistortRectifyMap(InputArray cameraMatrix, InputArray distCoeffs, InputArray R, InputArray newCameraMatrix, Size size, int m1type, OutputArray map1, OutputArray map2);
```

- `cameraMatrix`: 相机的内参矩阵。

- `distCoeffs`: 相机的畸变系数。

- `R`: 旋转矩阵，用于将图像坐标系旋转到标准位置。对于去畸变，通常传入单位矩阵（`cv::Mat::eye(3, 3, CV_64F)`），表示没有旋转。

- `newCameraMatrix`: 新的相机内参矩阵，通常使用与 `cameraMatrix` 相同的矩阵。

- `size`: 图像尺寸。

- `m1type`: 输出映射表的类型，通常为 `CV_32FC1` 或 `CV_16SC2`。

- `map1`, `map2`: 输出的映射表，用于 `remap` 函数。

```C++
#include <opencv2/opencv.hpp>

using namespace cv;

int main() {
    // 假设这些参数已经从相机标定中获得
    Mat cameraMatrix = (Mat_<double>(3, 3) << 418.523, 0, 343.909,
                                               0, 421.223, 235.466,
                                               0, 0, 1);
    Mat distCoeffs = (Mat_<double>(5, 1) << 0.006637, 0.050240, 0.006681, 0.003130, 0);
    Mat R = Mat::eye(3, 3, CV_64F); // 单位矩阵
    Mat newCameraMatrix = cameraMatrix; // 新的相机矩阵与原矩阵相同

    Size imageSize = Size(640, 360); // 图像的宽度和高度
    Mat map1, map2;

    // 计算映射表
    initUndistortRectifyMap(cameraMatrix, distCoeffs, R, newCameraMatrix, imageSize, CV_32FC1, map1, map2);

    // 使用映射表进行去畸变
    Mat undistortedImage;
    Mat inputImage = imread("image.jpg"); // 输入的畸变图像
    remap(inputImage, undistortedImage, map1, map2, INTER_LINEAR);

    imshow("Undistorted Image", undistortedImage);
    waitKey(0);

    return 0;
}
```



### **立体矫正⭐**

**双目立体视觉在进行距离计算的时候，要在左右相机的图像中精确的匹配到同一像素点，从而计算出视差，再根据视差与距离的关系估计像素点的距离**。然而上述的过程是在双目理想状态下建立的，即双目的成像平面处于同一平面，大多数情况下仅仅依靠双目位置的摆放很难达到这个目的，所以就不得不先对双目相机的图像进行立体校正。**立体校正是把双目相机获取的没有共面平行的两幅图像校正为共面平行\(做到焦距相同、光轴平行\)**，即将双目相机的成像平面校准到同一平面。已校正的双目系统和未校正的双目系统如下图所示。立体校正之后，由于双目图像成像处于同一平面，立体匹配搜索算法由二维变为一维，这降低了搜索复杂度并提高了立体匹配的搜索效率。

![image\.png](http://10.0.0.4:5244/COS/tungwebsite/assets/images/wiki/2024/07/19/006-4fb641a63481.webp)



`stereoRectify() `函数用于进行双目图像的立体校正，它的主要任务是将两个相机的图像对齐，使得图像中的对应点在水平线上对齐，以便进行后续的立体匹配。这个过程涉及对图像进行矫正，使得图像中的**极线与图像的行对齐（极线矫正）**，从而简化立体匹配的计算。

**【极线矫正】**

![image\.png](http://10.0.0.4:5244/COS/tungwebsite/assets/images/wiki/2024/07/19/007-88aaffcc04b7.webp)

```C++
void stereoRectify(
    InputArray cameraMatrix1, InputArray distCoeffs1,
    InputArray cameraMatrix2, InputArray distCoeffs2,
    Size imageSize, InputArray R, InputArray T,
    OutputArray R1, OutputArray R2, OutputArray P1, OutputArray P2,
    OutputArray Q, int flags = CALIB_ZERO_DISPARITY,
    double alpha = -1, Size newImageSize = Size(),
    OutputArray validPixROI1 = noArray(), OutputArray validPixROI2 = noArray()
);
```

- `cameraMatrix1` 和 `cameraMatrix2`: 第一个和第二个相机的内参矩阵（相机矩阵）。这些矩阵包含焦距、主点坐标等相机的内在参数。

- `distCoeffs1` 和 `distCoeffs2`: 第一个和第二个相机的畸变系数。用于描述每个相机的畸变情况，包括径向畸变和切向畸变。

- `imageSize`:  输入图像的大小，通常为图像的宽度和高度。

- `R`: 相机之间的旋转矩阵。这个矩阵描述了两个相机坐标系之间的旋转关系。

- `T`: 相机之间的平移向量。这个向量描述了两个相机坐标系之间的平移关系。

- `R1` 和 `R2`: 校正后的第一个和第二个相机的旋转矩阵。它们将原始的相机坐标系旋转到一个新的坐标系，使得两个图像的行对齐。

- `P1` 和 `P2`: 校正后的第一个和第二个相机的投影矩阵。这些矩阵包含了新的相机内参矩阵以及图像的变换信息。

- `Q`: 立体重投影矩阵。用于将视差图转换为三维点云。这个矩阵将视差值转换为三维坐标。

- `flags`: `CALIB_ZERO_DISPARITY` 表示校正后的图像应该具有零视差。其他标志会影响校正的方式和输出图像的大小。

- `alpha`: 图像的缩放因子。值范围在 `[0, 1]` 之间。`alpha = 0` 表示完全裁剪图像以去除黑边；`alpha = 1` 表示保留所有图像数据。如果 `alpha` 为 \-1，则自动选择。

- `newImageSize`: 校正后的图像大小。如果指定此参数，则输出图像将被调整为指定的大小。默认值为 `(0, 0)` 表示使用原始图像大小。

- `validPixROI1` 和 `validPixROI2`: 校正后有效图像区域的矩形区域。这些区域描述了校正后图像中有效的部分。

---

**`stereoRectify()`**` `函数在双目立体视觉中主要用于两项任务：

1. **计算立体校正的变换矩阵**：

    - **旋转矩阵（Rl, Rr）**：将左右相机的图像旋转到相同的图像平面。

    - **投影矩阵（Pl, Pr）**：定义如何从三维空间投影到二维图像平面。

    - **重投影矩阵（Q）**：用于将视差图转换为三维点云。

2. **确定有效的 ROI（Region of Interest）**：

    - **validROIL, validROIR**：指定图像在校正后的有效区域。这是由于校正过程中图像边缘可能会被裁剪。

```C++
// 立体校正
Rodrigues(rec, R);  // Rodrigues变换
stereoRectify(cameraMatrixL, distCoeffL, cameraMatrixR, distCoeffR, imageSize, R, T, Rl, Rr, Pl, Pr, Q, CALIB_ZERO_DISPARITY, 0, imageSize, &validROIL, &validROIR);
initUndistortRectifyMap(cameraMatrixL, distCoeffL, Rl, Pl, imageSize, CV_32FC1, mapLx, mapLy);
initUndistortRectifyMap(cameraMatrixR, distCoeffR, Rr, Pr, imageSize, CV_32FC1, mapRx, mapRy);

// 经过remap之后，左右相机的图像已经共面并且行对准了
remap(grayImageL, rectifyImageL, mapLx, mapLy, INTER_LINEAR);
remap(grayImageR, rectifyImageR, mapRx, mapRy, INTER_LINEAR);
```

---



### 立体匹配⭐

立体匹配算法中最常用的两种匹配算法分别为局部匹配算法BM和半全局匹配算法SGBM。

**局部匹配算法BM**

BM 块匹配算法是基于窗口灰度值的一种局部匹配算法。该匹配算法的主要思想为：首先人为设定一个小的窗口，提取窗口中图像的特征向量，然后使用提取到的特征向量在尚未匹配的图像上进行遍历搜索，在遍历搜索的过程中计算该窗口的特征向量与遍历窗口提取到的特征向量的相似程度，匹配到的最终结果为特征向量相似程度最大的窗口。

窗口的大小会对匹配的准确性产生影响，如果窗口寸过小，则窗口不能包含足够多的图像在灰度上的变化，块匹配的过程中会很容易产生误差，如果窗口尺寸过大，包含的图像复杂信息越多，就会影响匹配时的计算效率，导致匹配精度降低，匹配结果也会更粗糙。

**半全局匹配算法SGBM**

SGBM 算法是一种半全局立体匹配算法，其匹配流程为：首先为每个像素点选择视差值以形成初始的视差图，然后设置一个全局能量函数，该函数与视差图相关，最后求解能量函数的最小化问题，从而得到每个像素的最佳视差值。相对来说算法精度SGBM\>BM，计算效率BM\>SGBM。

[https://blog.csdn.net/zhubaohua_bupt/article/details/51866567]()



**For OpenCV：回到代码层面**

OpenCV可以用于双目立体匹配的类有如下几种：**StereoBM、StereoSGBM、cv::StereoVar、cv::StereoBinaryBM、cv::StereoBinarySGBM**（后两种位于opencv\_contrb模块），值得注意的是，在OpenCV3以后，用于双目立体匹配的这些类就都变成了抽象类，含有大量的纯虚函数，无法直接创建类的实例化对象，**只能依靠智能指针去创建类对象**。

![image\.png](http://10.0.0.4:5244/COS/tungwebsite/assets/images/wiki/2024/07/19/008-ed8e6f428770.webp)

![image\.png](http://10.0.0.4:5244/COS/tungwebsite/assets/images/wiki/2024/07/19/009-542f121c7b5e.webp)

**`compute()`**** 方法来计算特定立体图像对的视差。**

[https://docs.opencv.org/4.x/d2/d6e/classcv_1_1StereoMatcher.html]()



#### **cv::StereoBM  即BM算法类**

[https://docs.opencv.org/4.x/d9/dba/classcv_1_1StereoBM.html]()

**【类构成】**

**枚举类型 **

- `PREFILTER_NORMALIZED_RESPONSE = 0`

- `PREFILTER_XSOBEL = 1`

这些枚举类型用于指定预滤波类型。

**静态成员函数 **

```C++
//该函数创建一个 StereoBM 对象。你可以调用 StereoBM::compute() 来计算特定立体图像对的视差。
static Ptr<StereoBM> create(int numDisparities=0, int blockSize=21)
```

- `numDisparities`：视差搜索范围。对于每个像素，算法将找到从 0（默认最小视差）到 `numDisparities` 的最佳视差。然后可以通过改变最小视差来调整搜索范围。

- `blockSize`：算法比较的块的线性大小。该大小应为奇数（因为块是以当前像素为中心的）。较大的块大小意味着更平滑但不太精确的视差图。较小的块大小提供了更详细的视差图，但算法更有可能找到错误的对应关系。

**公共成员函数**

- **`getPreFilterCap()`**

返回预滤波器的截断值。该值用于在预处理步骤中对图像进行滤波，目的是减少噪声的影响。

- **`getPreFilterSize()`**

返回预滤波器窗口的大小。预滤波器窗口的大小决定了滤波器在每个像素上应用的区域大小。

- **`getPreFilterType()`**

返回预滤波器的类型。预滤波器有不同的类型，可以选择适合的类型来提高匹配的准确性。

- **`getROI1()`**

返回第一个图像的感兴趣区域（ROI）。在某些情况下，只对图像的一部分进行匹配是有益的，这个函数返回第一个图像的那个部分。

- **`getROI2()`**

返回第二个图像的感兴趣区域（ROI）。同上，只不过这个函数返回的是第二个图像的感兴趣区域。

- **`getSmallerBlockSize()`**

返回用于匹配的小块大小。这是一个较小的块大小，用于在处理图像边缘或细节时使用。

- **`getTextureThreshold()`**

返回纹理阈值。纹理阈值用于过滤掉那些纹理太少的区域，因为这些区域的匹配结果往往不可靠。

- **`getUniquenessRatio()`**

返回唯一性比率。唯一性比率用于验证匹配结果的唯一性，防止错误匹配。这个值越大，算法越严格。

- **`setPreFilterCap(int preFilterCap)`**

设置预滤波器的截断值。通过调整这个值，可以控制预滤波器的效果。

- **`setPreFilterSize(int preFilterSize)`**

设置预滤波器窗口的大小。可以根据具体应用调整这个值，以提高匹配的准确性。

- **`setPreFilterType(int preFilterType)`**

设置预滤波器的类型。可以选择不同的预滤波器类型，以优化匹配效果。

- **`setROI1(Rect roi1)`**

设置第一个图像的感兴趣区域。通过这个函数，可以指定要处理的图像部分。

- **`setROI2(Rect roi2)`**

设置第二个图像的感兴趣区域。同上，只不过是对第二个图像进行设置。

- **`setSmallerBlockSize(int blockSize)`**

设置用于匹配的小块大小。可以调整这个值以在处理图像边缘或细节时提高匹配效果。

- **`setTextureThreshold(int textureThreshold)`**

设置纹理阈值。通过调整这个值，可以过滤掉那些纹理太少的区域，从而提高匹配结果的可靠性。

- **`setUniquenessRatio(int uniquenessRatio)`**

设置唯一性比率。通过调整这个值，可以控制匹配结果的唯一性验证，防止错误匹配。

---

#### **cv::StereoSGBM   即SGBM算法类**

[https://docs.opencv.org/4.x/d2/d85/classcv_1_1StereoSGBM.html]()

【**类构成】**

**枚举类型**

`cv::StereoSGBM` 包含了几个枚举值，用于定义不同的算法模式：

- `MODE_SGBM`：标准的 SGBM 模式。

- `MODE_HH`：完全的两个方向动态规划算法。

- `MODE_SGBM_3WAY`：三向动态规划算法。

- `MODE_HH4`：另一种变体的 HH 算法。

**静态成员函数**

```C++
//创建一个 StereoSGBM 对象，你可以调用 StereoSGBM::compute() 来计算特定立体图像对的视差。
static Ptr<StereoSGBM> create(  int minDisparity=0,
                                int numDisparities=16,
                                int blockSize=3,
                                int P1=0, int P2=0,
                                int disp12MaxDiff=0,
                                int preFilterCap=0,
                                int uniquenessRatio=0,
                                int speckleWindowSize=0,
                                int speckleRange=0,
                                int mode=StereoSGBM::MODE_SGBM)
```

- `minDisparity`：最小视差值，通常为 0，但在图像处理时可能需要调整。

- `numDisparities`：最大视差减去最小视差的值，必须大于 0，并且在实现中必须是 16 的倍数。

- `blockSize`：匹配块的大小，必须是大于或等于 1 的奇数。

- `P1` 和 `P2`：控制视差平滑的两个参数。`P1` 是相邻像素之间视差变化的惩罚，`P2` 是相邻像素之间大于 1 的视差变化的惩罚。

- `disp12MaxDiff`：左\-右视差检查的最大允许差异。如果设置为非正值，则禁用检查。

- `preFilterCap`：预处理图像像素的截断值。

- `uniquenessRatio`：最佳计算成本函数值应该"赢得"第二好值的百分比边际。

- `speckleWindowSize`：考虑噪声斑点并使其无效的平滑区域的最大大小。

- `speckleRange`：每个连通组件内的最大视差变化。

- `mode`：选择算法模式，如 `StereoSGBM::MODE_SGBM`。

**公共成员函数**

- **`virtual int getMode() const`**
返回当前使用的算法模式。

- **`virtual int getP1() const`**
获取第一个平滑参数 P1 的值。

- **`virtual int getP2() const`**
获取第二个平滑参数 P2 的值。

- **`virtual int getPreFilterCap() const`**
获取预处理图像像素的截断值。

- **`virtual int getUniquenessRatio() const`**
获取唯一性比率参数的值。

- **`virtual void setMode(int mode)`**
设置算法模式（如 `MODE_SGBM`、`MODE_HH` 等）。

- **`virtual void setP1(int P1)`**
设置第一个平滑参数 P1 的值。

- **`virtual void setP2(int P2)`**
设置第二个平滑参数 P2 的值。

- **`virtual void setPreFilterCap(int preFilterCap)`**
设置预处理图像像素的截断值。

- **`virtual void setUniquenessRatio(int uniquenessRatio)`**
设置唯一性比率参数的值。

【**注意**】

默认情况下，算法是单遍的，仅考虑 5 个方向，而不是 8 个。可以通过设置 `mode=StereoSGBM::MODE_HH`来运行完整的算法版本，但可能会消耗大量内存。

算法匹配的是块而不是单个像素。设置 `blockSize=1` 会将块减少到单个像素。

---



#### cv::StereoBinaryBM and cv::StereoBinarySGBM

`cv::StereoBinaryBM` 和 `cv::StereoBinarySGBM`在实质上还是BM和SGBM算法，但是他们使用二进制特征来加速计算，旨在提高计算速度并且适用于实时处理，但是程序依旧是位于CPU上进行计算。

其对应类的定义及使用方式，见官方文档，这里不再解释：

[https://docs.opencv.org/4.x/d7/d8e/classcv_1_1stereo_1_1StereoBinaryBM.html]()

[https://docs.opencv.org/4.x/d1/d9f/classcv_1_1stereo_1_1StereoBinarySGBM.html]()

- 但是，这两个类的使用可能需要借助opencv的**contrib模块**，如果需要还需要单独下载源码并编译。

- 对于BM 和 SGBM算法，如果受限于CPU计算性能问题，还可以使用GPU进行加速，则需要编译GPU版本的**opencv\_cuda**，并且使用opencv2/cudastereo模块的**cv::cuda::StereoBM类**、**cv::cuda::StereoSGBM类**



## 代码实现

### BM to Video

```C++
#include <opencv2/opencv.hpp>
#include <iostream>
#include <math.h>

using namespace std;
using namespace cv;

const int imageWidth = 640; // 摄像头的分辨率
const int imageHeight = 360;
Vec3f point3;
float d;
Size imageSize = Size(imageWidth, imageHeight);

Mat rgbImageL, grayImageL;
Mat rgbImageR, grayImageR;
Mat rectifyImageL, rectifyImageR;

Rect validROIL; // 图像校正之后，会对图像进行裁剪，这里的validROI就是指裁剪之后的区域
Rect validROIR;

Mat mapLx, mapLy, mapRx, mapRy; // 映射表
Mat Rl, Rr, Pl, Pr, Q; // 校正旋转矩阵R，投影矩阵P 重投影矩阵Q
Mat xyz; // 三维坐标

Point origin; // 鼠标按下的起始点
Rect selection; // 定义矩形选框
bool selectObject = false; // 是否选择对象

int blockSize = 0, uniquenessRatio = 0, numDisparities = 0;
Ptr<StereoBM> bm = StereoBM::create(16, 9);

// 事先标定好的左相机的内参矩阵
Mat cameraMatrixL = (Mat_<double>(3, 3) << 418.523322187048, -1.26842201390676, 343.908870120890,
    0, 421.222568242056, 235.466208987968,
    0, 0, 1);

// 获得的畸变参数
Mat distCoeffL = (Mat_<double>(5, 1) << 0.006636837611004, 0.050240447649195, 0.006681263320267, 0.003130367429418, 0);

// 事先标定好的右相机的内参矩阵
Mat cameraMatrixR = (Mat_<double>(3, 3) << 417.417985082506, 0.498638151824367, 309.903372309072,
    0, 419.795432389420, 230.6,
    0, 0, 1);

Mat distCoeffR = (Mat_<double>(5, 1) << -0.038407383078874, 0.236392800301615, 0.004121779274885, 0.002296129959664, 0);

// T平移向量
Mat T = (Mat_<double>(3, 1) << -1.210187345641146e+02, 0.519235426836325, -0.425535566316217);

// rec旋转向量，对应matlab om参数
Mat rec = (Mat_<double>(3, 3) << 0.999341122700880, -0.00206388651740061, 0.0362361815232777,
    0.000660748031451783, 0.999250989651683, 0.0386913826603732,
    -0.0362888948713456, -0.0386419468010579, 0.998593969567432);

Mat R; // R 旋转矩阵

/*****立体匹配*****/
void stereo_match(int, void*)
{
    bm->setBlockSize(2 * blockSize + 5); // SAD窗口大小，5~21之间为宜
    bm->setROI1(validROIL);
    bm->setROI2(validROIR);
    bm->setPreFilterCap(31);
    bm->setMinDisparity(0); // 最小视差，默认值为0, 可以是负值，int型
    bm->setNumDisparities(numDisparities * 16 + 16); // 视差窗口，即最大视差值与最小视差值之差, 窗口大小必须是16的整数倍，int型
    bm->setTextureThreshold(10);
    bm->setUniquenessRatio(uniquenessRatio); // uniquenessRatio主要可以防止误匹配
    bm->setSpeckleWindowSize(100);
    bm->setSpeckleRange(32);
    bm->setDisp12MaxDiff(-1);

    Mat disp, disp8;
    bm->compute(rectifyImageL, rectifyImageR, disp); // 输入图像必须为灰度图
    disp.convertTo(disp8, CV_8U, 255 / ((numDisparities * 16 + 16) * 16.)); // 计算出的视差是CV_16S格式
    reprojectImageTo3D(disp, xyz, Q, true); // 在实际求距离时，ReprojectTo3D出来的X/W, Y/W, Z/W都要乘以16(也就是W除以16)，才能得到正确的三维坐标信息。
    xyz = xyz * 16;
    imshow("disparity", disp8);
}

/*****描述：鼠标操作回调*****/
static void onMouse(int event, int x, int y, int, void*)
{
    if (selectObject)
    {
        selection.x = MIN(x, origin.x);
        selection.y = MIN(y, origin.y);
        selection.width = std::abs(x - origin.x);
        selection.height = std::abs(y - origin.y);
    }

    switch (event)
    {
    case EVENT_LBUTTONDOWN: // 鼠标左按钮按下的事件
        origin = Point(x, y);
        selection = Rect(x, y, 0, 0);
        selectObject = true;
        point3 = xyz.at<Vec3f>(origin);
        cout << "世界坐标：" << endl;
        cout << "x: " << point3[0] << "  y: " << point3[1] << "  z: " << point3[2] << endl;
        d = point3[0] * point3[0] + point3[1] * point3[1] + point3[2] * point3[2];
        d = sqrt(d); // mm
        d = d / 10.0; // cm
        cout << "距离是:" << d << "cm" << endl;
        break;
    case EVENT_LBUTTONUP: // 鼠标左按钮释放的事件
        selectObject = false;
        if (selection.width > 0 && selection.height > 0)
            break;
    }
}

/*****主函数*****/
int main()
{
    // 立体校正
    Rodrigues(rec, R); // Rodrigues变换
    stereoRectify(cameraMatrixL, distCoeffL, cameraMatrixR, distCoeffR, imageSize, R, T, Rl, Rr, Pl, Pr, Q, CALIB_ZERO_DISPARITY, 0, imageSize, &validROIL, &validROIR);
    initUndistortRectifyMap(cameraMatrixL, distCoeffL, Rl, Pl, imageSize, CV_32FC1, mapLx, mapLy);
    initUndistortRectifyMap(cameraMatrixR, distCoeffR, Rr, Pr, imageSize, CV_32FC1, mapRx, mapRy);

    // 打开摄像头
    VideoCapture cap;
    cap.open(1); // 打开相机，电脑自带摄像头一般编号为0，外接摄像头编号为1，主要是在设备管理器中查看自己摄像头的编号。
    cap.set(CAP_PROP_FRAME_WIDTH, 2560); // 设置捕获视频的宽度
    cap.set(CAP_PROP_FRAME_HEIGHT, 720); // 设置捕获视频的高度

    if (!cap.isOpened()) // 判断是否成功打开相机
    {
        cout << "摄像头打开失败!" << endl;
        return -1;
    }

    Mat frame, frame_L, frame_R;
    cap >> frame; // 从相机捕获一帧图像

    while (true) {
        double fScale = 0.5; // 定义缩放系数，对2560*720图像进行缩放显示（2560*720图像过大，液晶屏分辨率较小时，需要缩放才可完整显示在屏幕）
        Size dsize = Size(frame.cols * fScale, frame.rows * fScale);
        Mat imagedst = Mat(dsize, CV_32S);

        resize(frame, imagedst, dsize);
        frame_L = imagedst(Rect(0, 0, 640, 360)); // 获取缩放后左Camera的图像
        frame_R = imagedst(Rect(640, 0, 640, 360)); // 获取缩放后右Camera的图像
        cap >> frame;

        // 读取图片
        cvtColor(frame_L, grayImageL, COLOR_BGR2GRAY);
        cvtColor(frame_R, grayImageR, COLOR_BGR2GRAY);

        // 经过remap之后，左右相机的图像已经共面并且行对准了
        remap(grayImageL, rectifyImageL, mapLx, mapLy, INTER_LINEAR);
        remap(grayImageR, rectifyImageR, mapRx, mapRy, INTER_LINEAR);

        namedWindow("disparity", WINDOW_AUTOSIZE);
        createTrackbar("blockSize:\n", "disparity", &blockSize, 8, stereo_match);
        createTrackbar("numDisparities:\n", "disparity", &numDisparities, 16, stereo_match);
        createTrackbar("uniquenessRatio:\n", "disparity", &uniquenessRatio, 50, stereo_match);

        setMouseCallback("disparity", onMouse, 0);
        stereo_match(0, 0);

        imshow("imageL", frame_L);
        imshow("imageR", frame_R);
        waitKey(1);
    }

    return 0;
}

```

### SGBM to Image

```C++
#include <opencv2/opencv.hpp>
#include <iostream>
#include <cmath>

using namespace std;
using namespace cv;

const int imageWidth = 640;  // 摄像头的分辨率宽度
const int imageHeight = 360;  // 摄像头的分辨率高度
Vec3f point3;  // 用于存储三维点坐标
float d;  // 用于存储计算的距离
Size imageSize = Size(imageWidth, imageHeight);  // 图像尺寸

Mat rgbImageL, grayImageL;  // 左图像的彩色和灰度图
Mat rgbImageR, grayImageR;  // 右图像的彩色和灰度图
Mat rectifyImageL, rectifyImageR;  // 校正后的左图像和右图像

Rect validROIL;  // 校正后左图像的有效区域
Rect validROIR;  // 校正后右图像的有效区域

Mat mapLx, mapLy, mapRx, mapRy;  // 映射表
Mat Rl, Rr, Pl, Pr, Q;  // 校正旋转矩阵R，投影矩阵P，重投影矩阵Q
Mat xyz;  // 三维坐标

Point origin;  // 鼠标按下的起始点
Rect selection;  // 定义矩形选框
bool selectObject = false;  // 是否选择对象

int blockSize = 5, uniquenessRatio = 15, numDisparities = 1;  // 立体匹配参数
Ptr<StereoBM> bm = StereoBM::create(16, 9);  // 创建立体匹配对象

// 左相机的内参矩阵
Mat cameraMatrixL = (Mat_<double>(3, 3) << 418.523322187048, -1.26842201390676, 343.908870120890,
        0, 421.222568242056, 235.466208987968,
        0, 0, 1);

// 左相机的畸变参数
Mat distCoeffL = (Mat_<double>(5, 1) << 0.006636837611004, 0.050240447649195, 0.006681263320267, 0.003130367429418, 0);

// 右相机的内参矩阵
Mat cameraMatrixR = (Mat_<double>(3, 3) << 417.417985082506, 0.498638151824367, 309.903372309072,
        0, 419.795432389420, 230.6,
        0, 0, 1);

// 右相机的畸变参数
Mat distCoeffR = (Mat_<double>(5, 1) << -0.038407383078874, 0.236392800301615, 0.004121779274885, 0.002296129959664, 0);

// 平移向量
Mat T = (Mat_<double>(3, 1) << -121.0187345641146, 0.519235426836325, -0.425535566316217);

// 旋转矩阵
Mat rec = (Mat_<double>(3, 3) << 0.999341122700880, -0.00206388651740061, 0.0362361815232777,
        0.000660748031451783, 0.999250989651683, 0.0386913826603732,
        -0.0362888948713456, -0.0386419468010579, 0.998593969567432);

Mat R;  // 旋转矩阵

// 立体匹配函数
void stereo_match(int, void*)
{
        bm->setBlockSize(2 * blockSize + 5);  // SAD窗口大小，5~21之间为宜
        bm->setROI1(validROIL);
        bm->setROI2(validROIR);
        bm->setPreFilterCap(31);
        bm->setMinDisparity(0);  // 最小视差
        bm->setNumDisparities(numDisparities * 16 + 16);  // 视差窗口大小
        bm->setTextureThreshold(10);
        bm->setUniquenessRatio(uniquenessRatio);  // 防止误匹配
        bm->setSpeckleWindowSize(100);
        bm->setSpeckleRange(32);
        bm->setDisp12MaxDiff(-1);
        Mat disp, disp8;
        bm->compute(rectifyImageL, rectifyImageR, disp);  // 计算视差图
        disp.convertTo(disp8, CV_8U, 255 / ((numDisparities * 16 + 16) * 16.));  // 转换视差图格式
        reprojectImageTo3D(disp, xyz, Q, true);  // 重投影到三维坐标
        xyz = xyz * 16;
        imshow("disparity", disp8);  // 显示视差图
}

// 鼠标回调函数
static void onMouse(int event, int x, int y, int, void*)
{
        if (selectObject)
        {
                selection.x = MIN(x, origin.x);
                selection.y = MIN(y, origin.y);
                selection.width = abs(x - origin.x);
                selection.height = abs(y - origin.y);
        }

        switch (event)
        {
        case EVENT_LBUTTONDOWN:  // 鼠标左键按下事件
                origin = Point(x, y);
                selection = Rect(x, y, 0, 0);
                selectObject = true;
                point3 = xyz.at<Vec3f>(origin);
                cout << "世界坐标：" << endl;
                cout << "x: " << point3[0] << "  y: " << point3[1] << "  z: " << point3[2] << endl;
                d = point3[0] * point3[0] + point3[1] * point3[1] + point3[2] * point3[2];
                d = sqrt(d);  // mm
                d = d / 10.0;  // cm
                cout << "距离是:" << d << "cm" << endl;
                break;
        case EVENT_LBUTTONUP:  // 鼠标左键释放事件
                selectObject = false;
                if (selection.width > 0 && selection.height > 0)
                        break;
        }
}

// 主函数
int main()
{
        // 立体校正
        Rodrigues(rec, R);  // Rodrigues变换
        stereoRectify(cameraMatrixL, distCoeffL, cameraMatrixR, distCoeffR, imageSize, R, T, Rl, Rr, Pl, Pr, Q, CALIB_ZERO_DISPARITY, 0, imageSize, &validROIL, &validROIR);
        initUndistortRectifyMap(cameraMatrixL, distCoeffL, Rl, Pl, imageSize, CV_32FC1, mapLx, mapLy);
        initUndistortRectifyMap(cameraMatrixR, distCoeffR, Rr, Pr, imageSize, CV_32FC1, mapRx, mapRy);

        // 读取图片
        rgbImageL = imread("image_left_1.jpg", IMREAD_COLOR);
        if (rgbImageL.empty()) {
            cout << "无法读取左图像!" << endl;
            return -1;
        }
        cvtColor(rgbImageL, grayImageL, COLOR_BGR2GRAY);

        rgbImageR = imread("image_right_1.jpg", IMREAD_COLOR);
        if (rgbImageR.empty()) {
            cout << "无法读取右图像!" << endl;
            return -1;
        }
        cvtColor(rgbImageR, grayImageR, COLOR_BGR2GRAY);

        imshow("ImageL Before Rectify", grayImageL);
        imshow("ImageR Before Rectify", grayImageR);

        // 经过remap之后，左右相机的图像已经共面并且行对准了
        remap(grayImageL, rectifyImageL, mapLx, mapLy, INTER_LINEAR);
        remap(grayImageR, rectifyImageR, mapRx, mapRy, INTER_LINEAR);

        // 把校正结果显示出来
        Mat rgbRectifyImageL, rgbRectifyImageR;
        cvtColor(rectifyImageL, rgbRectifyImageL, COLOR_GRAY2BGR);  // 伪彩色图
        cvtColor(rectifyImageR, rgbRectifyImageR, COLOR_GRAY2BGR);

        // 单独显示
        imshow("ImageL After Rectify", rgbRectifyImageL);
        imshow("ImageR After Rectify", rgbRectifyImageR);

        // 显示在同一张图上
        Mat canvas;
        double sf = 600.0 / max(imageSize.width, imageSize.height);  // 缩放因子
        int w = cvRound(imageSize.width * sf);
        int h = cvRound(imageSize.height * sf);
        canvas.create(h, w * 2, CV_8UC3);  // 创建画布

        // 左图像画到画布上
        Mat canvasPart = canvas(Rect(w * 0, 0, w, h));
        resize(rgbRectifyImageL, canvasPart, canvasPart.size(), 0, 0, INTER_AREA);  // 缩放图像
        Rect vroiL(cvRound(validROIL.x * sf), cvRound(validROIL.y * sf), cvRound(validROIL.width * sf), cvRound(validROIL.height * sf));
        cout << "Painted ImageL" << endl;

        // 右图像画到画布上
        canvasPart = canvas(Rect(w, 0, w, h));
        resize(rgbRectifyImageR, canvasPart, canvasPart.size(), 0, 0, INTER_LINEAR);
        Rect vroiR(cvRound(validROIR.x * sf), cvRound(validROIR.y * sf), cvRound(validROIR.width * sf));
        cout << "Painted ImageR" << endl;

        // 画上对应的线条
        for (int i = 0; i < canvas.rows; i += 16)
                line(canvas, Point(0, i), Point(canvas.cols, i), Scalar(0, 255, 0), 1, 8);
        imshow("rectified", canvas);

        // 立体匹配
        namedWindow("disparity", WINDOW_AUTOSIZE);
        // 创建SAD窗口 Trackbar
        createTrackbar("BlockSize:\n", "disparity", &blockSize, 8, stereo_match);
        // 创建视差唯一性百分比窗口 Trackbar
        createTrackbar("UniquenessRatio:\n", "disparity", &uniquenessRatio, 50, stereo_match);
        // 创建视差窗口 Trackbar
        createTrackbar("NumDisparities:\n", "disparity", &numDisparities, 16, stereo_match);
        // 鼠标响应函数
        setMouseCallback("disparity", onMouse, 0);
        stereo_match(0, 0);

        waitKey(0);  // 等待按键

        return 0;
}
```

## 效果

![image\.png](http://10.0.0.4:5244/COS/tungwebsite/assets/images/wiki/2024/07/19/010-b6538b359e76.webp)
