---
title: 学习java第一天——markdown基础
body: [article, comments]
meta: 
  header: [title, author, date,category]
  footer: [updated, tags, share]
categories: java笔记
tags: java笔记
---

> {% span yellow, 本人是一个跨考生，本科学习的农学，现在研究生跨入了计算机专业，因为底子比较薄，所以想通过看一些课程，在博客上自己记录一下学习历程，以便于督促自己。 %}

<!--more-->

# markdown基础

## 一.标题

##+space+内容

### 三级标题

###+space+内容

#### 四级标题

####+space+内容

## 二.字体

**hello，java**            ** + 内容 + **

*hello，java*                * + 内容 + *

~~hello，java~~             ~~ + 内容 + ~~

***hello，java***            ** *  +   内容   + ** *

## 三.引用

> 学习java第一天，认认真真记笔记！
>
> '>' + space + 内容

## 四.分割线

---

键盘输入“---”，也就是三个减号就可以了

或者也可以用三个星号，“***”

##　五.图片

![截图](https://ss0.bdstatic.com/94oJfD_bAAcT8t7mm9GUKT-xh_/timg?image&quality=100&size=b4000_4000&sec=1596721861&di=2eb592e5a35f908fb45fd4b25ad82e17&src=http://a1.att.hudong.com/05/00/01300000194285122188000535877.jpg)

先输入“!” + 英文状态下的“[ ]” + 图片的网络地址或者本地地址

## 六.超链接

[超链接](https://www.csdn.net/)

先输入“[ ]”+ "( )"在括号里面放要链接的网址就可以了

## 七.列表

1. 第一个
2. 第二个
3. 

这个就是直接写“1” + “.” + space

### 无序列表

- 第一个
- 第二个

输入“-”减号 + space

## 八.表格

1. 右键插入

2. 代码形式

   | 姓名 | 性别 | 年龄 |
   | ---- | ---- | ---- |
   | 小明 | 男   | 18   |
   |      |      |      |

   mac下：输入        |名字|性别|生日|          即可

   win下：输入         |名字|性别|生日|

​                                      |--|--|--|

​                                       |小明|男|18|                然后回车 

## 九.代码

```java
package hello;

public class world
{
    public static void main(String[] args)
    {
        System.out.println("Hello, world!");
    }
}
```

输入“···” + "语言名称"，就会出现代码框了

- 其中的点 在键盘按键上的 Esc 的下边
- 语言名称指的是“java”，”c“，”python“等