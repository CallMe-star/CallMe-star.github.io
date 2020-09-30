---
title: tensorflow第一节
body: [article, comments]
meta: 
  header: [title, author, date,category]
  footer: [updated, tags, share]
categories: TensorFlow
tags: TensorFlow
mathjax: true
---

> 先学习一下tensorflow的相关的基本函数

<!--more-->

### 常用函数

#### 1、类型转换

- 强制tensor转换为该数据类型

tf.case(张量名, dtype=数据类型)

- 计算张量维度上元素的最小值

tf.reduce_min(张量名)

- 计算张量维度上元素的最大值

tf.reduce_max(张量名)

```python
x1 = tf.constant([1., 2., 3.], dtype = tf.float64)
print(x1)
x2 = tf.case(x1, tf.int32)
print(x2)
print(tf.reduce_min(x2), tf.reduce_max(x2))
```

运行结果：

```python
tf.Tensor([1.2.3.], shape = (3,), dtype = float64)
tf.Tensor([1 2 3], shape = (3,), dtype = int32)
tf.Tensor(1, shape = (), dtype = int32)
tf.Tensor(3, shape = (), dtype = int32)
```

#### 2、理解axis

在一个二维向量中axis=0代表以列为单位求取最大值，axis代表以行为单位求取最大值，如果不指定，则所有元素都参与运算

- 计算张量沿着指定维度的平均值

tf.reduce_mean(张量名, axis=操作轴)

- 计算张量沿着指定维度的和

tf.reduce_sum(张量名, axis=操作轴)

```python
x = tf.constant([[1, 2, 3], [2, 2, 3]])
print(x)
print(tf.reduce_mean(x))
print(tf.reduce_sum(x,axis=1))
```

#### 3、运算

- 将变量标记为可训练

tf.Variable（初始值）

```python
tf.Variable(tf.random.normal([2,2], mean = 0, stddev = 1))
```

```python
a = tf.ones([1, 3])   # 1*3的矩阵赋值为1
b = tf.fill([1,3], 3.)  # 1*3的矩阵赋值为3
print(a)
print(b)
print(tf.add(a, b))
print(tf.subtract(a, b))
print(tf.multiply(a, b))
print(tf.divide(b, a))
```

```python
a = tf.fill([1,2], 3.)
print(a)
print(tf.pow(a, 3))
print(tf.square(a))
print(tf.sqrt(a))
```

#### 4、对应标签和数据

```python
features = tf.constant([12, 23, 10 ,17])     #获取数据
labels = tf.constant([0, 1, 1, 0])       #获取标签
dataset = tf.data.Dataset.from_tensor_slices((features,labels))
print(dataset)
for element in dataset：
	print(element)
```

#### 5、求导运算

with结构记录计算过程，gradient求出张量的梯度

> with tf.GradientTape() as tape:
>
> 若干计算过程
>
> grad=tape.gradient(函数，对谁求导)

```python
with tf.GradientTape() as tape:
  w = tf.Variable(tf.constant(3.0))
  loss = tf.pow(w, 2)
grad = tape.gradient(loss, w)
print(grad)
```

#### 6、enumerate

enumerate是python的内建函数，他可以遍历元素，元组或字符串

```python
seq = ['one', 'two', 'three']
for i, element in enumerate(seq):
  print(i, element)
```

#### 7、tf.one_hot      独热编码

在分类问题中，常用独热码作为标签，标记类别：0代表非，1表示是

> 标签：1
>
> 独热码： （0， 1， 0）
>
> 表示是标签0的概率为0，是标签1的概率是百分百，是标签2的概率是0

#### 8、tf.one_hot(待转换数据，depth=几分类)

```python
classes = 3
labels = tf.constant([1,0,2])
output = tf.one_hot(labels, depth = classes)
print(output)
```

#### 9、tf.nn.softmax

tf.nn.softmax能把n个分类的n个输出转换成0到1之间的概率值

```python
y = tf.constant([1.01, 2.01, -0.66])     #定义一些数值
y_pro = tf.nn.softmax(y)      #转换成标准概率
print("result, y_pro is ",y_pro)
```

#### 10、tf.argmax

返回张量最大值得索引，tf.argmax(张量名，axis=操作轴)

```python
import numpy as np
test = np.array([[1,2,3],[2,3,4],[5,4,3],[8,7,2]])
print(test)
print(tf.argmax(test,axis=0))
print(tf.argmax(test,axis=0))
```

