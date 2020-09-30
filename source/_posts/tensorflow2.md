---
title: tensorflow第二节
body: [article, comments]
meta: 
  header: [title, author, date,category]
  footer: [updated, tags, share]
categories: TensorFlow
tags: TensorFlow
mathjax: true
---

> tensorflow实战，实现鸢尾花的分类

<!--more-->

### 鸢尾花分类

三种鸢尾花，有四种特征，四个数据同时输入，权重共有3*4=12组, [1,4]✖️[4,3]

#### 鸢尾花数据读入

从sklearn包dataset读入数据集，语法为：

> from sklearn.datasets import load_iris
>
> x_data = datasets.load_isis().data
>
> y_data = datasets.load_isis().target

```python
from sklearn import datasets
from pandas import DataFrame
import pandas as pd

x_data = datasets.load_isis().data    #.data返回数据集的输入特征
y_data = datasets.load_isis().target      # 。target返回数据集的所有标签
print(x_data)
print(y_data)

x_data = DataFrame(x_data,columes=["花萼长"，"花萼宽"，"花瓣长"，"花瓣宽"])   # 将数据转换成表格形式
pd.set_option('display.unicode.east_asian_width',True)   #设置列名对齐
print("x_data add index: \n",x_data)
x_data['类别'] = y_data    #新增加一列，标签为类别，数据为y_data
print("x_data add a colum: \n",x_data)
```

在运行过程中会提示我们缺少数据包，然后我们用python自带的pip下载安装即可（比如：pip install sklearn）

#### 神经网络实现鸢尾花分类

> 1、准备数据

- 数据集读入
- 数据集乱序

```python
np.random.seed(116)    #使用相同的seed使输入特征标签一一对应
np.random.shuffle（x_data）
np.random.seed(116)
np.random.shuffle(y_data)
tf.random.set_seed(116)
```

- 数据集分出永不相见的训练集和测试集

  训练集为前120行，测试集为最后30行

```python
x_train = x_data[:-30]
y_train = y_data[:-30]
x_test = x_data[-30:]
y_test = y_data[-30:]
```

- 配成【输入特征， 标签】对，每次喂入一小撮（batch）

  每32组数据打包成一个batch

```python
train_db = tf.data.Dataset.from_tensor_slice((x_train,y_train)).batch(32)
test_db = tf.data.Dataset.from_tensor_slice((x_train,y_train)).batch(32)
```

> 搭建网络

定义神经网络中所有可训练参数

```python
w1 = tf.Variable(tf.random.truncated_normal([4,3],stddev = 0, seed =1))
b1 = tf.Variable(tf.random.truncated_normal([3],stddev=0.1,seed=1))
```

> 参数优化

嵌套循环迭代，with结构更新参数，显示当前loss

```python
for epoch in range(epoch):   #数据集级别迭代
  for step,(x_train,y_train) in enumerate(train_db):
    with tf.GradientTape() as tape:     #记录梯度信息
      #向前传播过程计算y
      #计算总的loss
      y = tf.matmul(x_train,w1)+b1     #神经网络乘加运算
      y = tf.nn.softmax(y)    #使输出y符和概率分布，此操作后和独热码同量级
      y_ = tf.one_hot(y_train，depth = 3)  #将标签转换为独热码形式
      loss = tf.reduce_mean(tf.quare(y_-y))
      loss_all += loss.numpy()
    grads = tap.gradient(loss.[w1,b1])    #计算loss对各个参数的梯度
    #实现梯度的更新：w1=w1-lr*w1_grad, b1= b1-lr*b_grad
    w1.assign_sub(lr*grads[0])     #参数自更新
    b1.assign_sub(lr*grads[1])     #其中的lr是学习率
  print("Epoch {},loss:{}".format(epoch,loss_all/4))
```

> 测试效果

计算当前参数向前传播后的准确率，显示当前acc

```python
for x_test,y_test in test_db:
  y = tf.matmul(h,w)+b      #y为预测结果
  y = tf.nn.softmax(y)       #是结果符合概率分布
  pred= tf.argmax(y,axis=1)    #返回y中最大值的索引，即预测的分类
  pred= tf.case(pred,dtype=y_type.dtype)    #调整数据类型，与标签一致 
  correct = tf.cast(tf.equal(pred,y_test),dtype = tf.int32)
  correct = tf.reduce_sum(correct)     #将每个batch的correct数加起来
  total_correct += int(correct)    #将所有batch中的correct数加起来
  total_number +=x_test.shape[0]
acc = total_correct/total_number
print("test_acc:", acc)
```

> acc/loss 可视化

```  python
plt.title("acc curve")  #图片标题
plt.xlabel("epoch")   # x轴名字
plt,ylabel("acc")      #y轴名字
plt.plot(test_acc,label="$Accuracy$")    #逐点画出test_acc值并连线
plt.legend()
plt.show()
```

本文先对代码的整个大体流程有一个感性的认识，详细过程，下面会有讲解！