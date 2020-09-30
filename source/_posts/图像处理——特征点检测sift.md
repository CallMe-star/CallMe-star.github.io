---
title: 理解边缘检测sift
body: [article, comments]
meta: 
  header: [title, author, date,category]
  footer: [updated, tags, share]
categories: 图像处理笔记
tags: 图像处理笔记
---

> 图像处理——sift理解

<!--more-->

### 通俗的大致理解

1. 首先，理解同一幅**图像在不同的空间中有不同的表达方式**。比如在RGB空间中，图像中的像素点可以用颜色值(r,g,b)来表示；在灰度空间中，图像中的像素点可以用灰度值来表示。类似的，你可以把梯度图像认为是图像在**梯度空间**中的一种表达，图像中的像素点可以**用梯度方向**来表示，即，梯度图像中的像素点表达的是该点的梯度方向（而不是灰度值/颜色值）。

1. 其次，理解图像的**灰度直方图是什么**。图像的灰度直方图就是把**灰度图像中灰度值分别为0,1,...,255的像素的个数统计起来**，得到的一个一维向量。类似的，图像的梯度直方图就是把**梯度图像中梯度方向分别为0°到360°的像素的个数统计起来**，得到一个一维向量。



SIFT是什么？简单来说就是图像中某个局部区域（如16*16像素的一个区域）对应的梯度直方图，这就是最简单直观的理解。当然，SIFT还包括各种细节，如量化，尺度金字塔、旋转不变性、局部归一化等，这些东西很多博客上都有“教科书般”的介绍。

SURF是什么？SURF本身不是什么新的descriptor，而是对SIFT实现上的加速，核心点在于采用**积分图**对计算加速。

作者：大道至简知不语
链接：https://www.zhihu.com/question/40736560/answer/358547318
来源：知乎

**知识点：积分图**

### sift深度剖析

#### 1、明确学习目的

不管是Harris还是Shi-Tomas，角点检测检测即便做得再优化，也总是有不可克服的缺点：

- **对尺度很敏感，不具有尺度不变性**
- **需要设计角点匹配算法**

而SIFT算法是一种基于局部兴趣点的算法，因此

- **不仅对图片大小和旋转不敏感**
- **而且对光照、噪声等影响的抗击能力也非常优秀**

因此，该算法在性能和适用范围方面较于之前的算法有着质的改变。



在学习sift算法之前，我们先得搞明白这个算法目的是为了干什么，无非就是找到图像中的特征点，找到优质的特征点。

优质的特征点有什么特征呢？

- **尺度不变性：**人类在识别一个物体时，不管这个物体或远或近，都能对它进行正确的辨认，这就是所谓的尺度不变性。

![](https://cdn.jsdelivr.net/gh/CallMe-star/picbed@master/cat_scale.jpg)

- **旋转不变性：**当这个物体发生旋转时，我们照样可以正确地辨认它，这就是所谓的旋转不变性。

![](https://cdn.jsdelivr.net/gh/CallMe-star/picbed@master/cat_direct.jpg)

#### 2、sift过程

##### 构建多尺度的高斯金字塔

> ①构建单尺度的空间，单尺度空间有6张图，六张图分别是经过方差大小不同的高斯滤波处理的图

> ②然后对同一尺度的一组照片中的相邻滤波处理的照片做差得到的就是图像的轮廓，轮廓就是我们要得到的最基本的特征。做差得出的图像就叫做DoG。

> ③然后再分成多个尺度，小尺度是通过上一层大尺度的第三张照片作为原图再次进行高斯滤波得到的。这一步就是要解决尺度的问题：构建金字塔，将特征值拓展到多分辨率上。
>
> ***我的疑惑点：这么多尺度的图像他是怎样提取不同尺度的图像，然后融合在一起的***

##### 检测尺度空间的极值点

> 直接遍历找出极值点，极值点不但要根这个点周围的八个点比较，还要跟同一层相邻的另外两张图像作比较。一共比较26个点。注意做完这一步之后，应该就可以在原图上画出来极值点坐标了示意图了。

##### 精确定位极值点

> 上一步我们已经找到了相关的特征点的大致坐标，接下来就要确定他们的准确位置，这里牵涉到很多数学运算。

![](https://cdn.jsdelivr.net/gh/CallMe-star/picbed@master/jingqueweizhi.jpg)

##### 选取特征点的方向

> 对我们已经用金字塔解决了尺度不变性问题，下面就要通过确定特征点的方向来解决旋转不变性问题。完成关键点的梯度计算后，使用直方图统计邻域内像素的梯度和方向。梯度直方图将0~360度的方向范围分为36个柱(bins)，其中每柱10度。如图所示，直方图的峰值方向代表了关键点的主方向，(为简化，图中只画了八个方向的直方图)。

![](https://cdn.jsdelivr.net/gh/CallMe-star/picbed@master/direction.jpg)

> **直方图的峰值则代表了该关键点处邻域梯度的主方向，即作为该关键点的方向；其他的达到最大值80%的方向可作为辅助方向。**到这里我们已经检测出的含有位置、尺度和方向的关键点即是该图像的SIFT特征点。

##### 生成关键描述子

到这里还没有结束

金字塔保证特征点的空间不变性， 严格删选保证了特征点的准确性， 方向信息保证了特征点的旋转不变性。我们如何把所有信息作为属性附加给关键点呢？

①为什么要添加描述子：通过上面的步骤，对于每一个关键点，拥有三个信息：**位置、尺度以及方向**。接下来就是为每个关键点建立一个描述符，用一组向量将这个关键点描述出来，使其不随各种变化而改变，比如光照变化、视角变化等等。这个描述子不但包括关键点，也包含关键点周围对其有贡献的像素点，并且描述符应该有较高的独特性，以便于提高特征点正确匹配的概率。

②添加过程：**1) 确定计算描述子所需的图像区域**特征描述子与特征点所在的尺度有关，因此，对梯度的求取应在特征点对应的高斯图像上进行。将关键点附近的邻域划分为d*d(Lowe建议d=4)个子区域，每个子区域做为一个种子点，每个种子点有8个方向。每个子区域的大小与关键点方向分配时相同。

![](https://cdn.jsdelivr.net/gh/CallMe-star/picbed@master/miaoshuziimg.jpg)

以关键点为中心，4*4格为一个种子点，每个种子点8个方向。

每一个小格都代表了特征点邻域所在的尺度空间的一个像素 ，箭头方向代表了像素梯度方向，箭头长度代表该像素的幅值。然后在4×4的窗口内计算8个方向的梯度方向直方图。绘制每个梯度方向的累加可形成一个种子点。

- **2) 将坐标轴旋转为关键点的方向，以确保旋转不变性**

![](https://cdn.jsdelivr.net/gh/CallMe-star/picbed@master/xuanzhuanxy.jpg)

- **3) 将邻域内的采样点分配到对应的子区域内，将子区域内的梯度值分配到8个方向上，计算其权值**

![[公式]](https://www.zhihu.com/equation?tex=w%3Dm%28a%2Bx%2C+b%2By%29+%2A+e%5E%7B-%5Cfrac%7B%5Cleft%28x%5E%7B%5Cprime%7D%5Cright%29%5E%7B2%7D%2B%5Cleft%28y%5E%7B%5Cprime%7D%5Cright%29%5E%7B2%7D%7D%7B2+%5Ctimes%280.5+d%29%5E%7B2%7D%7D%7D)

其中a，b为关键点在高斯金字塔图像中的位置坐标。

- **4) 插值计算每个种子点八个方向的梯度**

![](https://cdn.jsdelivr.net/gh/CallMe-star/picbed@master/zhongzitidu.jpg)

- **5) 归一化**

如上统计的4*4*8=128个梯度信息即为该关键点的特征向量。特征向量形成后，为了去除光照变化的影响，需要对它们进行归一化处理，对于图像灰度值整体漂移，图像各点的梯度是邻域像素相减得到，所以也能去除。

- **6） 向量门限**

描述子向量门限。非线性光照，相机饱和度变化对造成某些方向的梯度值过大，而对方向的影响微弱。因此设置门限值(向量归一化后，一般取0.2)截断较大的梯度值。然后，再进行一次归一化处理，提高特征的鉴别性。

- **7） 排序**

按特征点的尺度对特征描述向量进行排序。

![](https://cdn.jsdelivr.net/gh/CallMe-star/picbed@master/paixu.jpg)

在每个4*4的1/16象限中，通过加权梯度值加到直方图8个方向区间中的一个，计算出一个梯度方向直方图。这样就可以对每个feature形成一个4*4*8=128维的描述子，每一维都可以表示4*4个格子中一个的scale/orientation. 将这个向量归一化之后，就进一步去除了光照的影响。



#### 3.sift怎么匹配

**1、 首先还是要对图片生成特征点**

一张图经过SIFT算法后，会得到多个特征点，每个特征点有128维的描述子属性。那么，匹配特征点都简单多啦！

生成了A、B两幅图的描述子，（分别是k1*128维和k2*128维），就将两图中各个scale（所有scale）的描述子进行匹配，匹配上128维即可表示两个特征点match上了。

**2、 然后考虑怎么匹配**

当两幅图像的SIFT特征向量生成后，下一步我们采用关键点特征向量的欧式距离来作为两幅图像中关键点的相似性判定度量。取图像1中的某个关键点，并找出其与图像2中欧式距离最近的前两个关键点，在这两个关键点中，如果最近的距离除以次近的距离少于某个比例阈值，则接受这一对匹配点。降低这个比例阈值，SIFT匹配点数目会减少，但更加稳定。

```c++
#include <opencv2/opencv.hpp>
#include <opencv2/xfeatures2d.hpp>
#include <iostream>

using namespace cv;
using namespace cv::xfeatures2d;
using namespace std;

int main(int argc, char** argv) {

	Mat cat = imread("cat.png");
	Mat smallCat = imread("smallCat.png");
	imshow("cat image", cat);
	imshow("smallCat image", smallCat);

	auto detector = SIFT::create();
	vector<KeyPoint> keypoints_cat, keypoints_smallCat;
	Mat descriptor_cat, descriptor_smallCat;
	detector->detectAndCompute(cat, Mat(), keypoints_cat, descriptor_cat);
	detector->detectAndCompute(smallCat, Mat(), keypoints_smallCat, descriptor_smallCat);

	Ptr<FlannBasedMatcher> matcher = FlannBasedMatcher::create();
	vector<DMatch> matches;
	matcher->match(descriptor_cat, descriptor_smallCat, matches);
	Mat dst;
	drawMatches(cat, keypoints_cat, smallCat, keypoints_smallCat, matches, dst);
	imshow("match-demo", dst);


	waitKey(0);
	return 0;
}
```


![img](https://picb.zhimg.com/80/v2-295c77fc626560a0421be89e19516d6d_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-aac36afb03c30320918b8d3d9f7f986a_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-799a944fce5797047a6e8a18fb624d2e_720w.jpg)

作者: lowkeyway

转载自: https://zhuanlan.zhihu.com/p/90122194