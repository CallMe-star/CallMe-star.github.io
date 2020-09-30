---
title: tensorflow第四节
body: [article, comments]
meta: 
  header: [title, author, date,category]
  footer: [updated, tags, share]
categories: TensorFlow
tags: TensorFlow
mathjax: true
---

> 八股六步法搭建网络结构

<!--more-->

## 用tf.keras搭建网络八股

### 六步法：

1. import       导入相关模块

2. train,test     告知喂入网络的训练集和测试集
3. model = tf.keras.models.Sequential      在Sequential中搭建网络结构，逐层描述网络，相当于走了一遍前向传播
4. model.compile    配置训练方法（告知训练时使用的优化器、损失函数、评测指标）
5. model.fit      执行训练过程（告知训练集和测试集的输入特征和标签  告知每个batch是多少要迭代多少次数据集）
6. model.summary   打印出网络的结构和参数统计

   代码展示

```python
import tensorflow as tf
from sklearn import datasets
import numpy as np

x_train = datasets.load_iris().data
y_train = datasets.load_iris().target

np.random.seed(116)
np.random.shuffle(x_train)
np.random.seed(116)
np.random.shuffle(y_train)
tf.random.set_seed(116)

model = tf.keras.models.Sequential([
    tf.keras.layers.Dense(3,activation='softmax', kernel_regularizer=tf.keras.regularizers.12())
])

model.compile(optimizer=tf.keras.optimizers.SGD(lr=0.1),
              loss=tf.keras.losses.SparseCategoricalCrossentropy(from_logits=False),
              metrics=['sparse_categorical_accuracy']
             )
model.fit(x_train, y_train, batch_size=32,epochs=500,validation_split=0.2,validation_freq=20)
model.summary()
```

代码解释：

①model = tf.keras.models.Sequential([网络结构])      我们需要在sequential中搭建网络结构

> tf.keras.layers.Dense(3, activation='softmax', kernel_regularizer=tf.keras.l2())
>
> 3为神经元个数
>
> activation为激活函数
>
> kernels_regularizer为正则化方法

②model.compile(opeimizer=tf.keras.optimizers.GSD(lr=0.1), loss=tf.keras.losses.SparseCategoricalCrossentropy(from_logits=False),metrics=['sparse_categorical_accuracy'])

> models.compile中配置训练方法
>
> optimizer是优化器
>
> loss为损失函数（sparsecategoricalcrossentropy中的False是因为神经网络末端经过了softmax函数，使得输出是概率分布而不是原始输出）
>
> metrics为评测指标：sparse_categorical_accuracy与上方的logits=false对应，数据集标签是0,1,2为独热码，神经网络前向传播的输出是概率分布。

③在fit中执行训练过程：model.fit(x_train, y_train, batch_size, epochs=500, validtion_split=0.2, validation_freq=20)

> validation_split告知从训练集中选择20%数据作为测试集
>
> validation_freq每迭代20次数据集验证一次准确率

④summary打印出网络结构和参数统计

### 显示数据集

```python
for i in range(100):
  plt.imshow(x_train[i])
  plt.show()
print("x_train[0]:\n", x_train[0])   #输出的是数据集第一个元素的矩阵（是一个灰度图像或者三通道彩色图想，色彩范围在0—255之间）
print("x_test.shape\n", x_test.shape)  #输出测试集的形状（三维的矩阵）
```

