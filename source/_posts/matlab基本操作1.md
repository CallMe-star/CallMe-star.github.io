---
title: matlab基础操作第一节
body: [article, comments]
meta: 
  header: [title, author, date,category]
  footer: [updated, tags, share]
categories: matlab
tags: matlab
mathjax: true
---

> 记录基本的matlab对图像的操作以及简单的函数调用对图像简单处理

<!--more-->

## 一、图像的读入与显示

```matlab
img = imread('图像在本地的地址.jpg');
figure(1),
imshow(img);
```

以上就完成了最基本的图像读入与显示工作。但是为了操作简便我们经常不会对一个彩色图像进行处理，因为彩色图想有三个通道，会增加计算的复杂程度，所以我们第一步就是要对图像进行处理转换为灰度图像（简单地理解就是黑色和白色两种色彩的图像，只不过图像的黑和白的程度被细分成了256种不同程度的颜色）洗面我们来操作一下：

```matlab
img = imread('图像在本地的地址.jpg');
img1 = rgb2gray(img);
```

如果单单对一个图片进行显示，那么我们就无法比较改变前后两个照片的细节上有什么差别，下面我们借用subplot函数，把两张或者多张图像显示在一个画布上：

```matlab
figure(2),
subplot(1,2,1),imshow(img),title('原图')；
subplot(1,2,2),imshow(img1),title('灰度图')；
```

## 二、相关函数调用

### 1、把图像转换为二值图

```matlab
img = imread('图像在本地的地址.jpg');
img1 = im2bw(img);
figure,
subplot(121),imshow(img);
subplot(122),imshow(img1);
```

### 2、把一个对比度不清晰的图像转换成清晰的图像

#### <1>下面用到两个函数imadjust()和stretchlim()

①stretchlim函数是用来获取一个图像的最佳阈值分割矩阵的函数，将这个函数产生的矩阵直接带入下面的imadjust函数即可解决大部分对比度不清晰地问题

②imadjust（）本来括号里面有四个参数（待处理的图像，待处理图像中的灰度范围，要装换到新图片中灰度被拉伸的区间，gamma）

举个例子imadjust(img, [0,1], [1,0], 1)

就是把原图中[0,1]灰度范围内的灰度以一定的映射规则转换到[1,0]内，也就是实现了一个灰度翻转，这个例子实现的功能跟一个函数类似Jmcomplement();  

```matlab
img = imread('图像在本地的地址.jpg');
img = rgb2gray(img);
dst = imcomplement(img)
figure,
subplot(121),imshow(img);
subplot(122),imshow(dst);
```



gamma用来规定映射的规则，如图所示，gamma以1为界限，大于1的时候把灰度较高的部分拉伸的很长，相当于把图像中亮度较高的部分，灰度分配到了【0，255】范围内，因此我们可以看到更多光亮部分的细节，相反就能看到较暗部分的细节

![](https://cdn.jsdelivr.net/gh/CallMe-star/picbed@master/20200829203428.png)

```matlab
img = imread('图像在本地的地址.jpg');
img = rgb2gray(img);
%下面用到两个函数imadjust（）和strtchlim()
h = stretchlim(img);
dst = imgadjust(img,h,[])
% 其中的[]是对图像对比度进行加深，让现实的图像是我们想象中的样子
figure，
subplot(221),imshow(img);
subplot(222),imshow(dst);
%下面用imhist函数看一下整幅图片的灰度分布图
subplot(223),imhist(img),title('原图灰度');
subplot(224),imhist(dst),title('变换后的灰度');
```

#### <2>histeq()

这个函数也是把图像进行一下智能的直方图均衡化，将对比度差的图像转换成能看到细节的图像

```matlab
img = imread('图像在本地的地址.jpg');
img = rgb2gray(img);
dst = histeq(img);
figure,
subplot(121),imshow(dst);
subplot(122),imhist(dst);
```



### 3、调用函数对图像进行去噪，求边缘等等

#### ①首先我们先认识一个函数medfilt2()

```matlab
img = imread('图像在本地的地址.jpg');
img = rgb2gray(img);
%先用一个函数给一个图像加入椒盐噪声
%或者还可以加高斯噪声imnoise(img,'guassian', 0.02)
img1=imnoise(img,‘salt & pepper’,0.02) 
img2 = medfilt2(img1, [3,3]);
figure,
subplot(121),imshow(img1);
subplot(122),imshow(img2);
```

#### ②fspecial（）和imfilter（）

##### fspecial()是用来做滤波器的

他可以做出各种类型的滤波器

Fspecial('滤波器名字'，hesize，sigma)

均值滤波器：h = fspecial('average',5)

高斯滤波器：h = fspecial('gaussian',5)

拉普拉斯滤波器：h = fspecial('laplacian')

拉普拉斯高斯：h = fspecial('log',3,0.2) 

sobel边缘提取：h = fspecial('sobel')

##### imfilter()是用来滤波的

imfilter（A, h, filtermode,boundary,size）

其中A是待处理图像

h是上边生成的滤波器

filtermode是滤波类型包括‘corr’即相关，还有‘conv’即卷积

boundary是边界补全的方式：有‘X’ 输入图像的边界通过用值X（无引号）来填充扩展 其默认值为0，‘repliacate’图像大小通过复制外边界的值来扩展，‘symmetric’图像大小通过镜像反射其边界来扩展，‘circular’图像大小通过将图像看成是一个二维周期函数的一个周期来扩展

size指的是：输出图像的大小，有‘same’和‘full’

```matlab
img = imread('peppers.png');
h = fspecial('average');%创建一个滤波器
dst = imfilter(img, h, 'conv', 'replicate','same');
figure, 
imshow(img);
imshow(dst);
```

