---
layout: post
title: "卷积神经网络（CNN）介绍"
author: "JT"
header-img: "img/post-bg-halting.jpg"
header-mask: 0.5
mathjax: true
tags:
  - 神经网络
  - 深度学习
  - 人工智能
---

> 这篇文章转载自[我的CSDN 博客](https://blog.csdn.net/u010976453/article/details/78500413)

# 卷积神经网络（CNN）

Keywords：

常用网络：LeNet5 、AlexNet VGGNet、 GoogleNet、 ResNet、DenseNet

filter size

stripe

padding

参数共享机制

fine tuning



输入层、卷积层、激励层、池化层、全连接层、Softmax 输出层

一般CNN结构依次为：

![CNN 层次结构](http://oymv8fxwf.bkt.clouddn.com/17-11-10/26033748.jpg)

一个CNN 层级结构示意图：

![CNN demo](http://oymv8fxwf.bkt.clouddn.com/17-11-10/85210239.jpg)

卷积层：一组固定的权重和不同窗口内数据做内积，就是**卷积**。

![卷积层](http://oymv8fxwf.bkt.clouddn.com/17-11-10/53122593.jpg)

激励层：

激励函数：

Sigmoid ： $f(x) = \frac{1}{1+e^{(-x)}}$ 函数很容易饱和，$|x| \geq 10$ 梯度近似于0

![Sigmoid](http://oymv8fxwf.bkt.clouddn.com/17-11-10/5109754.jpg)

Tanh : $f(x)=\frac{e^x-e^{-x}}{e^x+e^{-x}}$ 相比Sigmoid 优点是对称中心在原点，缺陷是跟Sigmoid 相似，容易饱和。

![](http://oymv8fxwf.bkt.clouddn.com/17-11-10/18890822.jpg)

ReLU：$f(x) = \max\{0,x\}$  收敛快（相比Sigmoid 大致提高6倍），求梯度简单，但比较脆弱

![](http://oymv8fxwf.bkt.clouddn.com/17-11-10/57571548.jpg)

Leaky ReLU：$f(x)=\max\{\alpha x,x\}$ 不会饱和，计算速度也很快

![](http://oymv8fxwf.bkt.clouddn.com/17-11-10/56827898.jpg)

ELU：指数线性单元，所有ReLU的优点都有，不会挂，输出均值趋于0，但是有指数存在，计算量略大。

![](http://oymv8fxwf.bkt.clouddn.com/17-11-10/14439110.jpg)

Maxout：两条直线拼接，计算是线性，不会饱和不会挂，缺点是增加了参数。

![maxout](http://oymv8fxwf.bkt.clouddn.com/17-11-10/33406859.jpg)

工业上**主流用的还是ReLU（或类ReLU）激励层**。



池化层（Pooling layer）

![](http://oymv8fxwf.bkt.clouddn.com/17-11-10/75910776.jpg)

pooling：原理是图像具有局部特征不变性，所以可以采用池化操作。

1.  Max pooling
2.  Average pooling

Max pooling 示意图

![Max pool](http://oymv8fxwf.bkt.clouddn.com/17-11-10/9711392.jpg)

downsampling

全连接层 （Full connect layer）：所有神经元之间都有权重连接，试图尽可能将池化下采样中的信息恢复出来。如果一开始都用全连接层，参数太多，训练量太大，无法有效计算。



CNN 训练算法：

![CNN 训练算法](http://oymv8fxwf.bkt.clouddn.com/17-11-10/71616949.jpg)

CNN 优缺点：

优点：

-   共享卷积核，对高维数据处理无压力；
-   无需手动选取特征，训练好权重，即获得特征
-   分类效果好

缺点：

-   需要调参，需要大样本量，训练最好要GPU
-   物理含义不明确



常见的CNN 框架

LeNet



AlexNet



GoogleNet



VGGNet



ResNet



DenseNet



---



Fine tuning：

![fine tuning](http://oymv8fxwf.bkt.clouddn.com/17-11-10/47158484.jpg)

**Mini-batch SGD**：

不断循环：

1.  采样一个batch 数据（如32张图片，可以做镜像对称）；
2.  前向计算得到损失loss
3.  反向传播计算梯度（一个batch上的）
4.  用这部分梯度迭代更新权重参数



CNN训练图像数据预处理： **去均值**，但是**不要做标准化、PCA**和**白化**。

均值是训练集样本数据的均值，训练集和测试集均需要减去训练集样本均值。



权重初始化

**权重初始化**，不能全部初始化为0。可采用正态随机初始化

```python
W = np.random.randn(m,n)*0.01 # W初始化，m是输入层神经元个数，n是输出层神经元个数
```

权重$W$的取值过小，会导致深层的激励为0

Xavier Initialization：

```python
import numpy as np
W = np.random.randn(fan_in,fan_out)/np.sqrt(fan_in)
```

ReLU activation function:

```python
import numpy as np
W = np.random.randn(node_in, node_out) / np.sqrt(node_in / 2)
```

Batch Normalization Layer （Google 提出）可以有效降低深度网络对weight初始化的依赖，**防止“梯度弥散”**:

```python
import tensorflow as tf # tensorflow
# put this before nonlinear transformation
layer = tf.contrib.layers.batch_norm(layer, center=True, scale=True,
                                     is_training=True)
```

通常在全连接层后，激励层前做。因为全连接层对所有的神经元关联，波动性大。其对激励过后的结果进行约束，使激励后的结果呈现高斯分布。 是对SGD 反向传播训练权重W的约束

![batch normalization](http://oymv8fxwf.bkt.clouddn.com/17-11-10/49708024.jpg)

Training时 $\mu_B$和 $\sigma_B$由当前batch计算得出；在Testing时 $\mu_B$和 $\sigma_B$应使用Training时保存的均值或类似的经过处理的值，而不是由当前batch计算。 $\gamma$ 和 $\beta$ 都是有神经网络 训练得到， 作用则是为了让因训练所需而“刻意”加入的BN能够有可能还原最初的输入（（即当$\gamma^{(k)}= \sqrt{Var[x^{(k)}]}$ , $\beta^{k}= E[x^{(k)}]$ 时）

**Batch normalization 的好处**：

1.  梯度传递（计算）更为顺畅；
2.  **学习率设高一点也没关系**；
3.  **对初始值的依赖减少了**！！！
4.  这里也可以看作是一种正则化，减少了对Dropout的需求。
