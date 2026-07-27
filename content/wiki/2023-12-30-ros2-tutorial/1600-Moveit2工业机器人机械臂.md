---
title: "Moveit2工业机器人机械臂"
---

https://moveit.ros.org/

### 机器人学
https://www.bilibili.com/video/BV1v4411H7ez

https://www.bilibili.com/video/av59243185

#### 理论基础
##### DOF(自由度)
简单来说，自由度(以下统称为dof)指的是 **物体在空间里面的基本运动方式** ，总共有6种。任何运动都可以拆分成这6种基本运动方式，而这6种基本运动方式又可以分为两类： **位移** 和 **旋转** 。

位移：X轴、Y轴、Z轴的平动

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1834.webp)

旋转：Roll横滚角(绕X转动)、Pitch俯仰角(绕Y转动)、Yaw航向角(绕Z转动)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1835.webp)

#### 数理基础
##### 位姿（位置与姿态）的表示
倘若在一个空间里有一个刚体（frame），我们如何确定刚体在这个空间里的位姿呢？

首先要建立一个世界坐标系（world frame），然后要在刚体上建立刚体坐标系（body frame）.

1.  **位置的描述**

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1836.webp)

描述刚体的质心(一个点)在世界中的位置，就可以用一个3X1向量来表示.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1837.webp)

这样就知道了平动的三个DOF。

2.  **方位的描述**

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1838.webp)

设世界坐标系为A，刚体坐标系为B。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1839.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1840.webp)

上面这个矩阵就叫旋转矩阵（Rotation Matrix），是一个3\*3的正交矩阵，ABR描述的是A为参考坐标系，B相对于A的方向。

每一个列向量，都代表B的对应坐标轴各自指向的方向。

每一列向量都是B的对应的坐标轴相对于A的方向余弦（Direct Cosines）。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1841.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1842.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1843.webp)

旋转矩阵的每个元素 r ij代表 B 的第 j 轴与 A 的第 i 轴的方向余弦.

实在看不懂，先来看下面来看例子：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1844.webp)

B的X轴在A中怎么表示？可以看出来，B的X正好是A的Z轴的负半轴，那就是0，0，-1.

B的Y轴在A中怎么表示？可以看出来，B的Y正好是A的Y轴的正半轴，那就是0，1，0.

B的Z轴在A中怎么表示？可以看出来，B的Z正好是A的X轴的正半轴，那就是1，0，0.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1845.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1846.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1847.webp)

这个例子里，AB的Z重合了，所以我们只看上视图就可以了。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1848.webp)

就是把XB这个单位向量，投影到A的X和Y上看分量即可。

同理YB也一样。

ZB和ZA重合，比较简单。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1849.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1850.webp)

答案是B。

3.  **位姿的描述**

通过BF在WF的状态，就可以知道刚体在世界中的位姿。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1851.webp)

4.  运动的描述

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1852.webp)

红色是刚体运动的轨迹线，

轨迹（平动DOF）对时间的微分(导数)，就是刚体线速度。

刚体速度再对时间的微分(导数)，就是刚体线加速度。

同理,转动DOF对时间的微分(导数)，就是刚体的角速度。

角速度再对时间的微分(导数)，就是刚体角加速度。

##### 旋转矩阵
特性：

由于旋转矩阵R里每个元素都是两个向量内积，内积是可以交换位置且最后结果**数值不变**的。

所以我们选择交换位置。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1853.webp)

原始的R是每一 **列** 都是B的某一轴在A系的分量。

交换位置后的R是每一 **行** 都是A的某一轴在B系的分量。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1854.webp)

结论：所以说Rab = Rba的T。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1855.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1856.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1857.webp)

他俩明显是转置关系。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1858.webp)

RT\*R=I3（3\*3单位阵）(正交阵orthogonal matrix的性质)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1859.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1860.webp)

还有个性质就是|R|=1

虽然有9个数字，但是啊，因为正交阵的性质，所以是有约束条件的，这9个数字里有6个数字是随着其他数字变化而变化的，所以这9个数字实际上只有3个参数可以任意选择，也就是旋转矩阵实际上只有3个自由度。(转动DOF)

旋转矩阵的一个功能如下：

比如说另一个坐标系B相对于A坐标系绕X,Y,Z轴各自**逆时针**转动theta度的旋转矩阵。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1861.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1862.webp)

在中国大陆教材中，常用R(X,theta)来代替图中的RXA(theta)。图中这个A指的是原坐标系，得出来的AP\`也是原坐标下的坐标。

这样的话，AP左乘一个R就得出来了P转动后在A系的坐标。（一定要与下面讲的旋转坐标变换分清楚，很容易混淆）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1863.webp)

P逆时针转动30度后，在**原坐标系**中的坐标为002.

总结：旋转矩阵主要是三种用法，如下图：

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1864.webp)

##### 坐标变换
1.  平移坐标变换

2.  旋转坐标变换

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1865.webp)

这里的APX是个数值，XA等是矢量，加法是矢量加法，最后得出来的是AP向量。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1866.webp)

重要结论就是AP = ABR\*BP.(注意是矩阵的左乘)

AP就是P在A系的坐标。

BP就是P在B系的坐标。

ABR就是A为参考坐标系，B相对于A的旋转矩阵。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1867.webp)

##### 物体的变换及逆变换
物体平动的顺序可以互相颠倒,但是物体转动的顺序不能互相颠倒,否则姿态会不一样.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1868.webp)

主要是两种拆解方式,一个是设一个固定的坐标系,一直按这个坐标系转动,

另一个方式是假设物体的坐标系.

#### 机械臂描述方式
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1869.webp)

Link 0一般也叫base_Link

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1870.webp)

先看好相对关系，Axis i-1的后面才是Link i - 1(当然其他描述也成立)

#### 描述各关节之间的关系
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1871.webp)

也就是公垂线(唯一解)，其长度为Link Length连杆长度

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1872.webp)

现在只是限制住了两个轴的距离，两个轴还是可以转动的，所以需要下一个参数，Link Twist连杆扭角。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1873.webp)

这个角就是沿中垂线，把后一个轴线沿中垂线往当前轴移动，然后形成的夹角叫Link Twist连杆扭角。

也就是说，针对空间中任意两个转轴，我们需要两个参数来进行描述，也就是Link Length和Link Twist。

如果是多个串起来的转轴，我们就无法找到对应关系了，比如说

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1874.webp)

上面这个图，我没法表示ai-1与ai在轴线Axis i上的相对关系以及相对姿态是什么样子的。

所以还需要其他参数。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1875.webp)

首先肯定需要一个长度，两个公垂线在Axis i上的距离，叫做Link Offset，连杆偏距。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1876.webp)

然后还需要一个角，Joint Angle连杆夹角，也叫关节角。

其实发现，这四个参数，只有一个参数是变化的，其他都是固定的。

如果ioint type是revolute joint，那么thetai变化，其他不变。

如果joint type是prismatic joint，那么di变化，其他不变。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1877.webp)

描述两个轴需要2个参数，连杆长度与连杆扭角。

描述多个轴串在一起需要4个参数(每两两杆件都需要4个参数)，连杆长度Link Length，连杆扭角Link Twist，连杆偏距Link Offset，连杆夹角Joint Angle。

#### 在joint上建立frame
咱们一般把Z方向定义成和转轴的方向一样，Z朝上或朝下是看怎么朝向，这两个轴的夹角最小，这样就能够确定Z的方向了。

Xi的方向是沿着ai的方向。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1878.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1879.webp)

Xi与Zi+1和Zi都垂直。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1880.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1881.webp)

右手定则，判断Y方向。

原点是Z和X的交点。

若是建立base_link(link0)与link1的话，则是特殊情况。（base_link是immobile不动的）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1882.webp)

frame0和frame1重叠(重合)。通常，比如说是个旋转关节，虽然规定theta是arbitrary任意的，但是我把theta固定成0，然后让frame0和frame1(theta

\= 0)重合。如果是平动关节，那么同理也取d=0的时候的frame1。注意，这里是重叠重合，并不是形状相同，而是完全重叠的坐标系。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1883.webp)

最后一个杆件，也差不多，因为Xn和Xn-1都要垂直于Axis n(Zn)，所以最简单的方法就是让Xn与Xn-1方向一致。

Xn取Xn-1的方向。也就是framen和framen-1是延长的。

**下面是重点中的重点：(有好几种方法判断，如有错误请讨论后修改)**

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1884.webp)

需要知道的常识：

1.  一般连杆扭角按逆时针是正值。

2.  我们常说的转向，是从逆着的方向去看，也就是让箭头指向眼睛的方向去看的。

3.  从轴的逆方向去看和顺着方向去看转向，是完全相反的方向，但是在平面上容易产生视觉错觉，难以理解。可以拿支笔或者电机，转动一下试试。

①alphai-1是正值还是负值，要看Zi-1到Zi的角是顺时针还是逆时针，伸出右手，让拇指沿Xi-1的方向，如果alphai-1顺着四指方向则为逆时针，正值，反之为顺时针，负值。

所以如图，alphai-1是逆时针，所以是正值。

②ai-1的长度因为是长度，所以永远是正值，然后值为Z轴间的相对距离。

③thetai的角度也基本同理，将右手大拇指沿着Zi的方向，若thetai顺着四指方向则为逆时针，逆着则为顺时针。

④di的大小方向要看从ai-1沿着zi的方向到ai，则是正值，反之为负值，大小即为距离。

#### Link Transformations
##### 理论
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1885.webp)

我们两个关节都有俩轴，一个Axisi-1一个是Axisi，我们也有俩frame，一个是framei-1一个是framei。

我们需要找到两个frame之间的关系式是什么，也就是找到变换矩阵Transformation Matrix。

然后将Trans Matrix量化即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1886.webp)

假设说有一个点P，他在frame i下的表达是Pi，如果我们找到了Ti-1 i的矩阵，那么就有办法，获得P在frame i-1下的表达了。

所以我们现在需要，用刚才找到的四个参数，转化成我们的Trans Matrix。

这四个参数，也就是ai-1，alphai-1，di和thetai，足以可以表达framei-1到framei了。

①首先在Axis i-1上，

先描述alpha，就把framei-1的Zi-1旋转到差不多Zi的方向，生成FrameR（只旋转Z，X不动，然后右手定则判断Y）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1887.webp)

②然后描述a，

就把FrameR沿着ai-1的方向移动到Zi上，

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1888.webp)

③再描述theta

我们转动frameR中的ZR，使其与ai方向相同，生成frameP（X动，Z不动，右手定则判断Y）

这样搞完之后，Xp的方向与Xi是相同的，Zp的方向也与Zi相同。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1889.webp)

④再描述d，也就是把FrameP往上拉，最后会与Framei重合。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1890.webp)

也就是从Framei - 1到Frame R，然后再到Frame Q，然后再到Frame P，最后到Frame i。一共四次转化。

刚才我们演示的是从Pi-1到Pi，现在我们要求的是从我们的Pi要到Pi-1，那么就是倒着左乘，先乘Tp i接着往下以此类推。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1891.webp)

从连杆i到连杆i-1的坐标系间的齐次变换矩阵T i-1 i=Rot(X，aplhai-1)Trans(ai-1,0,0)Rot(Z,thetai)Trans(0,0,di)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1892.webp)

左上角是3X3的旋转矩阵，所以只有角度theta和alpha参数，

右上角3X1的矩阵，也就是frame i的原点相对于frame i - 1的原点的向量。然后是从frame i - 1去看。所以他是长度与角度的复合。

最后一行的1X4的矩阵，是0001是固定数不动的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1893.webp)

对于连续的杆件，我们可以从base_link一直算到linkx，x想是几就是几。

比如说，现在有三个杆件，我们找到T23（3对2的），T12（1对2的）T01（地对1的），就可以找到T03（地对3的）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1894.webp)

##### Example
###### 平面RRR类型
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1895.webp)

①先找到关节转轴Joint Axes

三个转轴是三个红点，是点的话，也就是出纸面的方向。（因为，他是个平面的，所以说，每个Z轴之间的角度都是0，所以说Link Twist Alpha是0，所以Z的方向都随便取，但是咱们这里，假设关节都是逆时针旋转，按右手螺旋定则来看，Z就都朝上）

②再找到公垂线Common Perpendiculars

但是由于Axis都是互相平行的，所以说，这个公垂线有无数多条，所以我们就在一个平面内表达即可。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1896.webp)

③下一步定义Zi向量（Z方向与转轴方向相同所以也是向上。）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1897.webp)

④然后判断中间的Xi向量

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1898.webp)

⑤然后判断中间的Yi向量

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1899.webp)

⑥然后判断头和尾，也就是frame 0 和frame n

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1900.webp)

其实，我们可以把frame0和frame1建的完全重合，就是让alpha和theta都为0，如图并没有重合，所以有theta大小。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1901.webp)

最后一个杆件也一样，建议让X3的方向和X2方向重合，这样的话，theta和alpha都是0。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1902.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1903.webp)

因为每个轴都是平行的，所以alpha是0，由于ai都是同平面的，所以d也为0.

因为frame0和frame1重合，所以a0 = 0，然后a1 = L1，a2 = L2

由于全是RRR，所以，theta都是变化的角。

P点末端执行器，也可以算出，沿X3方向走，获得P在frame n的表达，然后就可以推出P在frame 0中的表达了。

如图的，P点在Frame3里的坐标是（L3，0，0）

###### RPR类型
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1904.webp)

①先找到转轴

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1905.webp)

按右手螺旋来。

②找公垂线

因为frame1的Z1和frame2的Z2相交，所以，没有公垂线。

frame2的Z2和frame3的Z3重合，所以也没有公垂线。

也就是说a全是0.

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1906.webp)

③建立Zi向量

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1907.webp)

Zi方向与转轴方向相同。

④Xi的向量

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1908.webp)

当Z1和Z2相交时，我们挑X的方向就挑和Z1和Z2都垂直的，有两种方案，要么X往前，要么往后，如图是往后的。

X1和X2必须平行，因为是个P类型的关节

⑤Y轴

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1909.webp)

⑥Frame 0和frame n

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1910.webp)

frame 0 和frame1重合

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1911.webp)

让X3方向与X2方向相同

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1912.webp)

如图，驱动的关节参数分别是theta1，d2，theta3

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1913.webp)

由于frame0和frame1重合，那么alpha0 = 0，

从Z1到Z2的角，伸右手大拇指指向X1，然后四指与Z1到Z2方向相同，所以是逆时针，为正值，所以是90度。

然后fame2到frame3，Z共线，所以alpha2=0

然后由于Zi有的相交，有的共线，所以a全是0

然后d1是0，因为frame0和frame1重合，

d2是d2，d3是L2（d是X在Axis上的距离）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1914.webp)

P点在Frame3上的坐标为（0，0，L3）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1915.webp)

其实Z方向有俩选择，X也有俩选择，一共四种选择，选择自己好理解，好计算的方案即可。

###### 中国台积电晶圆机器人(PRRR类型4个自由度)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1916.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1917.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1918.webp)

###### SCARA机器人(RRRP类型4个自由度)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1919.webp)

该机器人最后一个关节是既可以R又可以P的，所以是个RP关节，既可以先算R也可以先算P。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1920.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1921.webp)

###### RP类型
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1922.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1923.webp)

选D，因为俩自由度，所以有俩驱动参数。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1924.webp)

#### 执行器关节与笛卡尔空间(Actuator Joint and Cartesian Spaces)
![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1925.webp)

我们这里的驱动是theta1-3，

当我们知道theta1-3的值之后，我们就会知道P点在世界坐标系上的表达。

这被叫做正向运动学（Forward Kinematics）。

由P点世界坐标系反算关节角度，那么叫逆向运动学（Inverse Kinematics）。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1926.webp)

Actuator Space就是驱动器空间，比如一个电机怎么操控能转joint space下的固定角（经过一系列转换）。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1927.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1928.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1929.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1930.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1931.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1932.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1933.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1934.webp)

有转动有移动的部分（所以需要两个电机来达到这两个自由度）

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1935.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1936.webp)

同步带机构使其旋转。（第一个电机）

齿轮齿条机构达到上下移动。（第二个电机）

但是，这两个并不是独立的，因为是同轴驱动的，所以有些负荷。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1937.webp)

两个转动变成一个转动一个移动。

#### 正运动学
**定义** ：已知机器人各个关节（或轮子等驱动单元）的运动参数（如角度、位移、速度等），计算末端执行器的位置和姿态。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1938.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1939.webp)

ads=dv/dt \* ds = ds/dt \*dv = vdv

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1940.webp)

牛顿第二定律，能量守恒，冲量与动量

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1941.webp)

Wp就是P点在世界坐标系下的坐标

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1942.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1943.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1944.webp)

可以根据该公式，得知末端执行器在世界坐标系中的坐标。

#### 逆运动学
**定义** ：已知末端执行器的目标位置和姿态，计算需要让各个关节（或轮子等驱动单元）运动到什么角度或速度才能达到该目标。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1945.webp)![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1946.webp)

先知道末端执行器的某个点P在世界坐标系中的表达，也就是给出Pw或者末端执行器某个点上的frameH，

通过Pw求出theta。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1947.webp)

这样手臂就有6个未知数

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1948.webp)

16个数字，转动的部分占了9个数字，也就是左上角的3X3的旋转矩阵，然后右上角3X1的向量表示相对于原点的位移量是什么。（也就是frame6的原点相对于frame0的原点位移量是什么）

下面的0001是整数，固定的，不变的。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1949.webp)

这个旋转矩阵里，有3个长度条件，3个互相垂直条件，所以9个数字里，就剩3个自由度。（也就是向量长度为1限制3个，向量两两垂直限制3个，所以是平移矩阵，3个自由度）

然后右上角3X1的向量中，相对原点的坐标X,Y,Z,那么就是3个自由度。

所以总共有6个自由度。

这12个方程式就是除了低下的0001，上面的参数都可以列一个式子。我们要做的，就是从12个式子中求出6个未知数。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1950.webp)

灵活工作空间Dexterous workspace是可达工作空间Reachable workspace的子集。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1951.webp)

它的可达工作空间是个圆环。

对于某个点，在这个例子中，只有1种或2种姿态可以达到。这个机器人只有RWS，没有DWS。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/30/image1952.webp)

手臂一样长的话，那样工作空间就是个圆了，

有一个点就是DWS,就是原点，当手臂内折，那么可以以360度任意一个角度来达到这个点，所以该点就是DWS。
