---
title: matlab基础操作第二节
body: [article, comments]
meta: 
  header: [title, author, date,category]
  footer: [updated, tags, share]
categories: matlab
tags: matlab
mathjax: true
---

> 记录matlab形态学操作及绘图操作

<!--more-->

## 一、形态学操作

### <1>形态学基础

​																																																														

上节记录了怎么用fspecial做滤波器（也就是掩膜），这次我们要生成的叫结构元素SE

主要用来构建形态学运算中的结构元素，使用的语法为strel(shape,parameters)。shape为形状参数，即设置什么样的结构元素；parameters为控制形状参数大小方向的参数。

就像上一节的滤波器我们也可以手动绘制，这一节的结构元素我们一样可以绘制，但是太过麻烦,下面举例子做一个正45°，长度为6的结构元素

```matlab
SE = [0,0,0,0,0,1;0,0,0,0,1,0;0,0,0,1,0,0;0,0,1,0,0,0;0,1,0,0,0,0;1,0,0,0,0,0;]
SE =

     0     0     0     0     0     1
     0     0     0     0     1     0
     0     0     0     1     0     0
     0     0     1     0     0     0
     0     1     0     0     0     0
     1     0     0     0     0     0

```

这样自己写太麻烦了，matlab为我们提供了一个函数，采用strel()函数则能够快速地构建如上所示的结构元素。

使用方法：

SE = strel('arbitrary',NHOOD)

SE = strel('arbitrary',NHOOD,HEIGHT)

SE = strel('ball',R,H,N)

SE = strel('diamond',R)

SE = strel('disk',R,N)

SE = strel('line',LEN,DEG)

SE = strel('octagon',R)

SE = strel('pair',OFFSET)

SE = strel('periodicline',P,V)

SE = strel('rectangle',MN)

SE = strel('square',W)

可以构建出各种形状的结构元素。

### <2>下面我们开始用结构元素对图像进行膨胀腐蚀操作。

```matlab
img = imread('.......jpg');
SE = strel('ball',20);
dst1 = imdilate(img, SE);
dst2 = imerode(img, SE);
figure,imshow(dst1);
figure,imshow(dst2);
```

### <3>顶帽变换，底帽变换

