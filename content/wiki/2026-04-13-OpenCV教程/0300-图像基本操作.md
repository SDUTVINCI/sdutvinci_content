---
title: "图像基本操作"
---

在获取图像后，首先需要了解处理图像的基本操作，例如对图像颜色的分离，像素的改变，图像的拉伸与旋转，甚至需要在图像中添加一些基础的形状，并进行简单处理。因此，本章重点介绍OpenCV 4中提供的图像基本操作，包括彩色空间的介绍、像素操作、图像形状的改变、绘制几何图形以及生成图像金字塔等。

### 图像颜色空间

通过红、绿、蓝3种颜色不同比例的混合能够让图像展现出五彩斑斓的颜色，这种模型称为RGB颜色模型。RGB颜色模型是最常见的颜色模型之一，常用于表示和显示图像。为了能够表示3种颜色的混合，图像以多通道的形式分别存储某一种颜色的红色分量、绿色分量和蓝色分量。除RGB颜色模型外，图像的颜色模型还有YUV、HSV等模型，分别表示图像的亮度、色度、饱和度等分量。了解图像颜色空间对分割拥有颜色区分特征的图像具有重要的帮助，例如提取图像中的红色物体可以通过比较图像红色通道的像素值来实现。

####  颜色模型与转换

本小节将介绍几种OpenCV 4中能够互相转换的常见颜色模型，例如RGB模型、HSV模型、Lab模型、YUV模型及GRAY模型，并介绍这几种模型之间的数学转换关系、OpenCV 4中提供的这几种模型之间的变换函数。

*1. RGB颜色模型*

前面对于RGB颜色模型已经有所介绍，该模型的命名方式是采用3种颜色的英文首字母，分别是红色(Red)、绿色(Green)和蓝色(Blue)。虽然该颜色模型的命名方式是红色在前，但是在OpenCV中却是相反的顺序，第一个通道是蓝色(B)分量，第二个通道是绿色(G)分量，第三个通道是红色(R)分量。根据存储顺序的不同，OpenCV 4中提供了这种顺序的反序格式，用于存储第一个通道是红色分量的图像，但是这两种格式图像的颜色空间是相同的，颜色空间模型如图3-1所示。3个通道对于颜色描述的范围是相同的，因此RGB颜色模型的空间构成是一个立方体。在RGB颜色模型中，所有的颜色都是由这3种颜色通过不同比例的混合得到，如果3种颜色分量都为0，则表示为黑色，如果3种颜色的分量相同且都为最大值，则表示为白色。每个通道都表示某一种颜色0～1的过程，不同位数的图像表示将这个颜色变化过程细分成不同的层级，例如8UC3格式的图像每个通道将这个过程量化成256个等级，分别由0～255表示。在这个模型的基础上增加第四个通道即为RGBA模型，第四个通道表示颜色的透明度，当没有透明度需求的时候，RGBA模型就会退化成RGB模型。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/13/1776174668740.webp)

*2. YUV颜色模型*

YUV模型是电视信号系统所采用的颜色编码方式。这3个变量分别表示像素的亮度(Y)、红色分量与亮度的信号差值(U)、蓝色与亮度的差值(V)。这种颜色模型主要用于视频和图像的传输，该模型的产生与电视机的发展历程密切相关。由于彩色电视机在黑白电视机发明之后才产生，因此用于彩色电视机的视频信号需要能够兼容黑白电视机。彩色电视机需要3个通道的数据才能显示彩色，而黑白电视机只需要一个通道的数据，因此，为了使视频信号能够兼容彩色电视机与黑白电视机，将RGB编码方式转变成YUV的编码方式，其Y通道是图像的亮度，黑白电视只需要使用该通道就可以显示黑白视频图像，而彩色电视机通过将YUV编码转成RGB编码方式，便可以在彩色电视机中显示彩色图像，较好地解决了同一个视频信号兼容不同类型电视机的问题。RGB模型与YUV模型之间的转换关系如式(3-1)所示，其中RGB取值范围均为0～255。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/13/1776174696503.webp)

*3. HSV颜色模型*

HSV是色度(Hue)、饱和度(Saturation)和亮度(Value)的简写，通过名字也可以看出该模型通过这3个特性对颜色进行描述。色度是色彩的基本属性，就是平时常说的颜色，例如红色、蓝色等；饱和度是指颜色的纯度，饱和度越高色彩越纯和越艳，饱和度越低，色彩则逐渐地变灰和变暗，饱和度的取值范围是0～100%；亮度是颜色的明亮程度，其取值范围由0到计算机中允许的最大值。由于色度、饱和度和亮度的取值范围不同，因此其颜色空间模型用锥形表示，如图3-2所示。相比于RGB模型3个颜色分量与最终颜色联系不直观的缺点，HSV模型更加符合人类感知颜色的方式：颜色、深浅及亮暗。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/13/1776174751632.webp)

*4. Lab颜色模型*

Lab颜色模型弥补了RGB模型的不足，是一种设备无关和基于生理特征的颜色模型。在模型中，L表示亮度(Luminosity)，a和b是两个颜色通道，两者的取值区间都是-128～127，其中a通道数值由小到大对应的颜色是从绿色变成红色，b通道数值由小到大对应的颜色是由蓝色变成黄色。Lab颜色模型构成的颜色空间是一个球形，如图3-3所示。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/13/1776174786095.webp)

*5. GRAY颜色模型*

GRAY模型并不是一个彩色模型，而是一个灰度图像的模型，其命名使用的是英文单词gray的全字母大写。灰度图像只有单通道，灰度值根据图像位数不同由0到最大依次表示由黑到白，例如8UC1格式中，由黑到白被量化为256个等级，通过0～255表示，其中255表示白色。彩色图像具有颜色丰富、信息含量大的特性，但是灰度图在图像处理中依然具有一定的优势。例如，灰度图像具有相同尺寸相同压缩格式所占容量小、易于采集、便于传输等优点。常用的RGB模型转成灰度图的方式如式(3-2)所示。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/13/1776174812647.webp)

*6. 不同颜色模型间的互相转换*

针对图像不同颜色模型之间的相互转换，OpenCV 4提供了cvtColor()函数用于实现转换功能，该函数的原型在代码清单3-1中给出。

代码清单3-1 cvtColor()函数原型
```cpp
void cv::cvtColor(InputArray src,
                  OutputArray dst,
                  int code,
                  int dstCn = 0);
```

- src: 待转换颜色模型的原始图像。
- dst: 转换颜色模型后的目标图像。
- code: 颜色空间转换的标志，如由RGB空间到HSV空间。常用标志及含义在表3-1中给出。
- dstCn: 目标图像中的通道数。如果参数为0，则从src和code中自动导出通道数。

函数用于将图像从一个颜色模型转换为另一个颜色模型，前两个参数用于输入待转换图像和转换颜色空间后的目标图像，第三个参数用于声明该函数具体的转换模型空间，常用的标志在表3-1中给出，读者可以自行查阅OpenCV 4的教程以了解详细的标志。第四个参数在一般情况下不需要特殊设置，使用默认参数即可。
需要注意的是该函数变换前后的图像取值范围，由于8位无符号图像的像素为0～255，16位无符号图像的像素为0～65535，而32位浮点图像的像素为0～1，因此一定要注意目标图像的像素范围。在线性变换的情况下，范围问题不需要考虑，目标图像的像素不会超出范围。如果在非线性变换的情况下，那么应将输入RGB图像归一化到适当的范围以内来获得正确的结果，例如将8位无符号图像转成32位浮点图像，需要先将图像像素通过除以255缩放到0～1范围内，以防止产生错误结果。

注意：如果转换过程中添加了alpha通道（RGB模型中第四个通道，表示透明度），则其值将设置为相应通道范围的最大值：CV_8U为255，CV_16U为65535，CV_32F为1。

表3-1 cvtColor()函数颜色模型转换常用标志参数

| 标志参数             | 简记 | 作用               |
| :--------------- | :- | :--------------- |
| `COLOR_BGR2BGRA` | 0  | 对RGB图像添加alpha通道  |
| `COLOR_BGR2RGB`  | 4  | 彩色图像通道颜色顺序的更改    |
| `COLOR_BGR2GRAY` | 10 | 彩色图像转成灰度图像       |
| `COLOR_GRAY2BGR` | 8  | 灰度图像转成彩色图像（伪彩色）  |
| `COLOR_BGR2YUV`  | 82 | RGB颜色模型转成YUV颜色模型 |
| `COLOR_YUV2BGR`  | 84 | YUV颜色模型转成RGB颜色模型 |
| `COLOR_BGR2HSV`  | 40 | RGB颜色模型转成HSV颜色模型 |
| `COLOR_HSV2BGR`  | 54 | HSV颜色模型转成RGB颜色模型 |
| `COLOR_BGR2Lab`  | 44 | RGB颜色模型转成Lab颜色模型 |
| `COLOR_Lab2BGR`  | 56 | Lab颜色模型转成RGB颜色模型 |

为了直观地感受同一张图像在不同颜色空间中的样子，在代码清单3-2中给出了前面几种颜色模型互相转换的程序，运行结果如图3-4所示。需要说明的是，Lab颜色模型具有负数，而通过imshow()函数显示的图像无法显示负数，因此在结果中给出了利用Image Watch插件显示图像在Lab模型中的样子。在程序中，为了防止转换后出现数值越界的情况，我们先将CV_8U类型转成CV_32F类型后再进行颜色模型的转换。

代码清单3-2 myCvColor.cpp 图像颜色模型互相转换

```cpp
#include "chapter3_1_cvclolor/inc/CvColor.hpp"
#include <cstdio>
#include <opencv2/opencv.hpp>


int opencv_function9(void)
{
  const std::string & file_name = std::string(MEDIA_PATH) + "林星阑L.jpg";

  cv::Mat img = cv::imread(file_name);
  if(img.empty() == true)
  {
      std::cout << "请确认图像文件是否正确，请检查输入格式" << std::endl;
      return 1;
  }
  else
  {
      std::cout << "图像成功读取!" << std::endl;
  }
  cv::Mat img32;
  cv::Mat gray,HSV,YUV,Lab;
  img.convertTo(img32,CV_32F,1.0/255);   //缩放因子:1.0/255指将现在的图像的范围转换为目标图像的范围需要乘以的因数
  cv::cvtColor(img32,HSV,cv::COLOR_BGR2HSV);
  cv::cvtColor(img32,YUV,cv::COLOR_BGR2YUV);
  cv::cvtColor(img32,Lab,cv::COLOR_BGR2Lab);
  cv::cvtColor(img32,gray,cv::COLOR_BGR2GRAY);

  cv::namedWindow("原图BGR",cv::WINDOW_FREERATIO);
  cv::namedWindow("HSV",cv::WINDOW_FREERATIO);
  cv::namedWindow("YUV",cv::WINDOW_FREERATIO);
  cv::namedWindow("Lab",cv::WINDOW_FREERATIO);
  cv::namedWindow("gray",cv::WINDOW_FREERATIO);
  cv::imshow("原图BGR",img32);
  cv::imshow("HSV",HSV);
  cv::imshow("YUV",YUV);
  cv::imshow("Lab",Lab);
  cv::imshow("gray",gray);
  cv::waitKey(0);

  return 0;
}

```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/13/1776175023373.webp)

程序中我们利用了OpenCV 4中Mat类自带的数据类型转换函数convertTo()，在平时使用图像数据时也会经常遇到不同数据类型转换的问题，因此下面详细介绍该转换函数的使用方式，在代码清单3-3中给出了该函数的原型。

代码清单3-3 convertTo()函数原型

```cpp
void cv::Mat::convertTo(OutputArray m,
                        int rtype,
                        double alpha = 1,
                        double beta = 0);
```

- m: 转换类型后输出的图像。
- rtype: 转换图像的数据类型。
- alpha: 转换过程中的缩放因子。
- beta: 转换过程中的偏置因子。

该函数用来实现将已有图像转换成指定数据类型的图像，第一个参数用于输出转换数据类型后的图像，第二个参数用于声明转换后图像的数据类型。第三个参数与第四个参数用于声明两个数据类型间的转换关系，具体转换形式如式(3-3)所示。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/13/1776175089069.webp)

通过转换公式可以知道，该转换方式就是将原有数据进行线性转换，并按照指定的数据类型输出。根据其转换规则可以知道，该函数不但能够实现不同数据类型之间的转换，而且能够实现在同一种数据类型中的线性变换。我们在代码清单3-2中给出了CV_8U类型和CV_32F类型之间互相转换的示例，其他类型之间的互相转换与此类似，这里不再赘述，读者可以自行探索，通过实践体会该函数的使用方法。

#### 多通道分离与合并

在图像颜色模型中，不同的分量存放在不同的通道中，如果我们只需要颜色模型的某一个分量，例如只需要处理RGB图像中的红色通道，那么可以将红色通道从3个通道的数据中分离出来再进行处理，这种方式可以减少数据所占据的内存，加快程序的运行速度。同时，当我们分别处理完多个通道后，需要将所有通道合并在一起重新生成RGB图像。针对图像多通道的分离与混合，OpenCV 4中提供了split()函数和merge()函数用于满足这些需求。

*1. 多通道分离函数split()*

OpenCV 4中针对多通道分离函数split()有两种重载原型，在代码清单3-4中给出了这两种函数原型。

代码清单3-4 split()函数原型

```cpp
void cv::split(const Mat &src,
               Mat *mvbegin);

void cv::split(InputArray m,
               OutputArrayOfArrays mv);
```

- mvbegin: 分离后的单通道图像，为数组形式，数组大小需要与图像的通道数相同。
- m: 待分离的多通道图像。
- mv: 分离后的单通道图像，为向量(vector)形式。

该函数主要是用于将多通道的图像分离成若干单通道的图像，两个函数原型中不同之处在于，前者第二个参数输出的是Mat类型的数组，其数组的长度需要与多通道图像的通道数相等并且提前定义；第二种函数原型的第二个参数输出的是一个`vector<Mat>`容器，不需要知道多通道图像的通道数。虽然两个函数原型输入参数的类型不同，但通道分离的原理是相同的。

*2. 多通道合并函数merge()*

OpenCV 4中针对多通道合并函数merge()也有两种重载原型，在代码清单3-5中给出了这两种原型。
代码清单3-5 merge()函数原型

```cpp
void cv::merge(const Mat *mv,
               size_t count,
               OutputArray dst
                )

void cv::merge(InputArrayOfArrays mv,
               OutputArray dst);
```

- mv（第一种重载原型参数）：需要合并的图像数组，其中每个图像必须拥有相同的尺寸和数据类型。
- count：输入的图像数组的长度，其数值必须大于0。
- mv（第二种重载原型参数）：需要合并的图像向量(vector)，其中每个图像必须拥有相同的尺寸和数据类型。
- dst：合并后输出的图像，与mv[0]具有相同的尺寸和数据类型，通道数等于所有输入图像的通道数总和。

该函数主要用于将多个图像合并成一个多通道图像，该函数也具有两种不同的函数原型，每一种函数原型都与split()函数相对应，两种原型分别输入数组形式的图像数据和向量(vector)形式的图像数据，在输入数组形式数据的原型中，还需要输入数组的长度。合并函数的输出结果是一个多通道的图像，其通道数目是所有输入图像通道数目的总和。这里需要说明的是，用于合并的图像并非都是单通道的，也可以是多个通道数目不相同的图像合并成一个通道更多的图像。虽然这些图像的通道数目可以不相同，但是需要所有图像具有相同的尺寸和数据类型。

*3. 图像多通道分离与合并例程*

为了使读者更加熟悉图像多通道分离与合并的操作，同时加深对图像不同通道作用的理解，在代码清单3-6中实现了图像的多通道分离与合并的功能。程序中用两种函数原型分别分离了RGB图像和HSV图像，为了验证merge()函数可以合并多个通道不相同的图像，程序中分别用两种函数原型合并了多个不同通道的图像，合并后图像的通道数为5，不能通过imshow()函数显示，我们用Image Watch插件在看合并的结果。由于RGB的3个通道分离结果显示时都是灰色且相差不大，因此图3-5没有给出其分离后的结果，只是给出合并后显示为绿色的合并图像，同时给出HSV分离结果，其他结果读者可以通过自行运行程序查看。
代码清单3-6 mySplitAndMerge.cpp 实现图像分离与合并

```cpp
#include "chapter3_1_cvclolor/inc/SplitAndMerge.hpp"
#include <cstdio>
#include <opencv2/opencv.hpp>

bool Mat_Arr_Split_merge(const std::string &file_name);
bool Vector_Split_merge(const std::string &file_name);


int opencv_function10(void)
{
  Mat_Arr_Split_merge(std::string(MEDIA_PATH) + "林星阑L.jpg");
//   Vector_Split_merge(std::string(MEDIA_PATH) + "林星阑L.jpg");

  return 0;
}

bool Mat_Arr_Split_merge(const std::string &file_name)
{
  cv::Mat img = cv::imread(file_name);
  if(img.empty() == false)
  {
    printf("成功读取图片!");
  }
  else
  {
    printf("无法读取图片，请确定图片文件是否存在，输入格式是否正确!");
    return false;
  }
  cv::Mat imgs[3];
  cv::Mat result[2];
  cv::split(img,imgs);
  cv::namedWindow("RGB-B通道",cv::WINDOW_FREERATIO);
  cv::namedWindow("RGB-G通道",cv::WINDOW_FREERATIO);
  cv::namedWindow("RGB-R通道",cv::WINDOW_FREERATIO);
  cv::imshow("RGB-B通道",imgs[0]);
  cv::imshow("RGB-G通道",imgs[1]);
  cv::imshow("RGB-R通道",imgs[2]);
  imgs[2] = img;  //改变图像通道数量
  cv::merge(imgs,3,result[0]);
  // cv::namedWindow("合并图像结果0",cv::WINDOW_FREERATIO);
  // cv::imshow("合并图像结果0",result[0]);   //imshow最多显示4个通道，需要Image Watch插件
  cv::Mat zero = cv::Mat::zeros(img.rows,img.cols,CV_8UC1);  //一个通道的0矩阵
  imgs[0] = zero;
  imgs[2] = zero;
  cv::merge(imgs,3,result[1]);
  cv::namedWindow("合并图像结果1",cv::WINDOW_FREERATIO);
  cv::imshow("合并图像结果1",result[1]);   //显示合并结果
  cv::waitKey(0);
  cv::destroyAllWindows();
  return true;
}

bool Vector_Split_merge(const std::string &file_name)
{
  cv::Mat img = cv::imread(file_name);
  if(img.empty() == false)
  {
    printf("成功读取图片!");
  }
  else
  {
    printf("无法读取图片，请确定图片文件是否存在，输入格式是否正确!");
    return false;
  }
  cv::Mat HSV;
  cv::Mat result;
  cv::cvtColor(img,HSV,cv::COLOR_RGB2HSV);
  std::vector<cv::Mat> imgv;
  cv::split(HSV,imgv);
  cv::namedWindow("HSV-H通道",cv::WINDOW_FREERATIO);
  cv::namedWindow("HSV-S通道",cv::WINDOW_FREERATIO);
  cv::namedWindow("HSV-V通道",cv::WINDOW_FREERATIO);
  cv::imshow("HSV-H通道",imgv.at(0));
  cv::imshow("HSV-S通道",imgv.at(1));
  cv::imshow("HSV-V通道",imgv.at(2));
  imgv.push_back(HSV);  //将imgv中的图像通道变成不一致
  cv::merge(imgv,result);
  // cv::namedWindow("合并图像结果2",cv::WINDOW_FREERATIO);
  // cv::imshow("合并图像结果2",result);   //imshow最多显示4个通道，需要Image Watch插件
  cv::waitKey(0);
  cv::destroyAllWindows();
  return true;
}
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2026/04/13/1776175712099.webp)

### 图像像素操作处理

在对图像的不同通道有所了解之后，接下来将对每个通道内图像像素的相关操作进行介绍。关于像素的相关概念，在前面已经有所了解，例如在CV_8U的图像中，像素取值范围由黑到白被分成了256份，灰度值为0～255来表示这个变化的过程。因此，像素灰度值的大小表示的是某个位置像素的亮暗程度，同时灰度值的变化程度也表示了图像纹理的变化程度，因此，了解像素的相关操作是了解图像内容的第一步。

#### 图像像素统计

我们可以将数字图像理解成一定尺寸的矩阵，矩阵中每个元素的大小表示了图像中每个像素的亮暗程度，因此，统计矩阵中的最大值就是寻找图像中灰度值最大的像素，计算平均值就是计算图像像素平均灰度，可以用来表示图像整体的亮暗程度。因此，针对矩阵数据的统计工作在图像像素中同样具有一定的意义和作用。在OpenCV 4中集成了求取图像像素最大值、最小值、平均值、均方差等众多用于统计的函数，下面详细介绍这些功能的相关函数。
