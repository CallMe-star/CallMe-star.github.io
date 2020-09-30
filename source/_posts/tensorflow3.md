---
title: tensorflow第三节
body: [article, comments]
meta: 
  header: [title, author, date,category]
  footer: [updated, tags, share]
categories: TensorFlow
tags: TensorFlow
mathjax: true
---

> 理论知识以及相关概念

<!--more-->

### 学习目标

- 预备知识

- 神经复杂度

- 指数衰减学习率

- 激活函数

- 损失减数

- 欠拟合和过拟合

- 正则化减少过拟合

- 优化器更新网络参数

#### 预备知识

- tf.where()

  tf.where(条件语句，真返回A，假返回B)

  ```python
  a = tf.constant([1,2,3,1,1])
  b = tf.constant([0,1,3,4,5])
  c = tf.where(tf.greater(a,b),a,b)    #若a>b,返回a对应位置的元素，否则返回b对应位置的元素
  print(c)
  ```

- tf.random.RandomState.rand()

  tf.random.RandomState.rand(维度)

  ```python
  import numpya as np
  rdm = np.random.RandomState(seed=1)    #seed相同生成的随机数相同
  a = rdm.rand()      #返回一个随机标量
  b = rdm.rand(2,3)      #返回一个两行三列的随机矩阵
  print(a)
  print(b)
  ```

  

- tf.vstack()

  将两个数组按垂直方向叠加

  np.vstack(数组1，数组2)

  ```python
  import numpy as np
  a = np.array([1,2,3])
  b = np.array([4,5,6])
  c = vstack('c为：'，c)
  ```

- np.mgrid[]  , .ravel()  , np.c_[]

  用来制表

  np.mgrid[起始值，结束值，步长]

  x.ravel()

  将x变为一维数组，把变量拉直

  np.c_[数组1， 数组2]

  将两个数组进行配对，补成一个大数组

  ```python
  import numpy as np
  x,y = np.mgrid[1:3:1, 2:4:0.5]
  grid = np.c_[x.ravel(),y.ravelz()]
  print(x)
  print(y)
  print(grid)
  ```

#### 复杂度学习率

在上节课中我们发现
$$
在式子中：w_{t+1} = w_t-lr*\frac{\partial loss}{\partial w_t}
$$

$$
损失函数: loss = (w+1)^2
$$

$$
\frac{\partial loss}{\partial w} = 2w+2
$$

参数w初始为5，学习率为若是太小则整个过程更新太慢，如果太大就会越过极值点，导致最后结果不收敛，在极值点附近跳跃。

在应用中可以通过**指数衰减学习率**来实现：

> 指数衰减学习率：先用较大的学习率，快速得到较优解，然后逐步减小学习率，使模型在训练后期稳定

$$
指数衰减学习率 = 初始学习率*学习率衰减率^{(当前轮次/多少轮衰减一次)}
$$



```python
epoch = 40
lr_base = 0.2      #初始学习率
lr_decay = 0.99    #学习率衰减率
lr_step = 1        #多少轮衰减一次 
for epoch in range(epoch):
  lr = lr_base *lr_decay**(epoch / lr _step)
  with tf.Gradient() as tape:
    loss = tf.square(w+1)
  grads = tape.gradient(loss, w)
  w.assign_sub(lr*grads)
  print("after %s epoch, w is %f, loss is %5f, lr = %f" 
        % (epoch, w.numpy(), loss(), lr)
```

#### 激活函数

 下面介绍几种常用的激活函数

1. sigmoid函数
   $$
   f(x)= \frac{1}{1+e^{-x}}
   $$
   特点:①因为需要反向求导，会造成梯度消失问题，使得参数无法更新

   ​		②输出非0均值，收敛慢

   ​		③幂运算复杂，训练时间长

2. tanh
   $$
   f(x) = \frac{1-e^{-2x}}{1+e^{-2x}}
   $$
   特点：①输出是0均值

   ​			②易造成梯度消失

   ​			③幂运算复杂，训练时间长

3. Relu
   $$
   f(x) = max(x,0)\\
   $$
   
   $$
   =\begin{cases}
   0, x<0\\
   x, x>=0
   \end{cases}
   $$
   优点：

   ①解决了梯度消失问题（在正区间）

   ②只需判断输入是否大于零，计算速度快

   ③收敛速度远快于sigmoid和tanh

   缺点：

   ①输出非零均值，收敛慢

   ②某些神经元可能永远不会被激活，导致相应参数永远不更新

4. Leaky Relu

   是对Relu在负区间上的改进
   $$
   f(x) = max(ax,x)
   $$
   理论上来讲，leaky  relu有relu的所有优点，外加不会产生死神经元的问题，但是实际操作中，并没有完全证明他总是好于relu

> 对初学者的建议：
>
> - 首选relu函数
>
> - 学习率设置较小值
>
> - 输入特征标准化，即让输入特征满足以零为均值，1为标准差的正态分布
>
> - $$
>   初始函数中心化，即让随机生成的参数满足以零为均值，\sqrt{\frac{2}{当前输入特征个数}}为标准差的正态分布
>   $$
>
> 

#### 损失函数

**损失函数**用来评价模型的**预测值**和**真实值**不一样的程度，损失函数越好，通常模型的性能越好。不同的模型用的损失函数一般也不一样。

**损失函数**分为**经验风险损失函数**和**结构风险损失函数**。经验风险损失函数指预测结果和实际结果的差别，结构风险损失函数是指经验风险损失函数加上正则项。

- 均方误差：
  $$
  MSE(y_均-y) = \frac{\sum_{i = 1}^{n}(y-y_均)^2}{n}
  $$
  loss_mean = tf.reduce_mean(tf.sqare(y-y_))

- 自定义损失函数
  $$
  loss(y_均,y) = \begin{cases}
  profit*(y_均 - y), y<y_均\\
  cost*(y-y_均)， y>y_均
  \end{cases}
  $$

  ```python
  Loss = tf.reduce_sum(tf.where:(tf,greater(y,y_均),cost(y,y_均), profit(y,y_均)))
  ```

- 交叉熵损失函数

  交叉熵损失函数CE表征两个概率分布之间的距离，原本的公式
  $$
  H（y_均，y） = -\sum{y_均*lny}
  $$
  tensorflow有直接的公式：

  ```python
  tf.losses.categorical_crossentropy(y_均,y)
  ```

  一般我们要经过softmax函数然后计算y与y_的交叉熵损失函数

  ```python
  tf.nn.softmax_cross_entropy_with_logits(y_,y)
  #下面用两种方式都进行运算
  y_ = np.array([[1,0,0],[0,0,1],[1,0,0],[0,1,0]])
  y = np.array([[12,3,2],[3,10,1],[1,2,5],[4,6.5,1.2],[3,6,1]])
  
  y_pro = tf.nn.softmax(y)
  loss_ce1 = tf.losses.categorical_crossentropy(y_,y_pro)
  
  loss_se2 = tf.nn.softmax_cross_entropy_with_logits(y_,y)
  
  print('分布计算的结果', loss_se1)
  print('结合计算的结果', loss_se2)
  ```

#### 欠拟合和过拟合

欠拟合图像不能有效表示出坐标点，而过拟合因为对一种情况过于细节，在另外的样本来临可能会很不准确，存在一个泛化性差的特点。

- 欠拟合的解决方法：

  ①增加输入特征项

  ②增加网络参数

  ③减少正则化参数

- 过拟合的解决方法：

  ①数据清洗

  ②增大训练集

  ③采用正则化

  ④增大正则化参数

> 正则化缓解过拟合：
>
> 正则化在损失函数中引入模型复杂度指标，利用给w加权值，强化了训练数据的噪声（一般不正则化b，只对权重正则化）
> $$
> loss = loss(y与y_均) +　REGULARIZER*loss(w)
> $$
> 其中的REGULARIZER给出了w在总loss中的比例，即正则化的权重。

#### 优化器

待优化参数w，损失函数loss，学习率lr，每迭代一个batch，t表示当前batch迭代的总次数
$$
计算t时刻的损失函数关于当前参数的梯度g_t=\bigtriangledown loss = \frac{\partial loss}{\partial(w_t)}
$$

$$
计算t时刻一阶动量m_t和二阶动量V_t
$$

$$
计算t时刻下降梯度：\eta = lr * m_t/\sqrt{V_t}
$$

$$
计算t+1时刻参数：w_{t+1} = w_t-\eta_t = w_t - lr * m_t/\sqrt{V_t}
$$

> 一阶动量是跟梯度相关的函数
>
> 二阶动量是跟梯度的平方相关的函数

下面搜索几种优化器使用即可

1. SGD
2. SGDM（含momentum 的SGD）
3. Adagrad（SGD增加二阶动量）
4. RMSProp（SGD增加二阶动量）
5. Adam（结合SGDM和RMSProp）