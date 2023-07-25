### 数学知识

#### 1.范数

​	L0范数是指向量中非0的元素的个数。

​	L1范数是指向量中各个元素绝对值之和。

​	L2范数是指向量各元素的平方和然后求平方根。

​    p 范数: 为 x 向量各个元素绝对值 p 次方和的 1/p 次方

**范数是具有“长度”概念的函数。**



#### 2.归一化 **(Normalization)**

归一化一般是将数据映射到指定的范围，用于去除不同维度数据的量纲以及量纲单位。

常见的映射范围有 [0, 1] 和 [-1, 1] ，最常见的归一化方法就是 **Min-Max 归一化**：
$$
X_{new} = \frac{X-X_{min}}{X_{max}-X_{min}}
$$
​	还有极差变换法，0均值标准化。

#### 3.标准化

#### 4.正则化 (**Regularization**)



## 机器学习

### 聚类算法

无监督学习一种。`聚类(Clustering)`是按照某个特定标准(如距离)把一个数据集分割成不同的类或簇，使得**同一个簇内的数据对象的相似性尽可能大，同时不在同一个簇中的数据对象的差异性也尽可能地大**。









#### Tensor

##### 创建方式

1. **torch.empty(5, 3)** ：创建一个5x3的未初始化的`Tensor`
2. **torch.randn(5, 3)** ：创建一个5x3的随机初始化的`Tensor`
3. **torch.zeros(5, 3, dtype=torch.long)** ：创建一个5x3的long型全0的`Tensor`
4. **torch.tensor([5.5, 3])**：直接根据数据创建



##### item()

另外一个常用的函数就是`item()`, 它可以将一个标量`Tensor`转换成一个Python number：

```python
x = torch.randn(1)
print(x)
print(x.item())
'''
输出：
	tensor([2.3466])
	2.3466382026672363
'''
```

`torch.abs`(*input*, ***, *out=None*) 





##### np.random.normal

高斯正态分布取值



##### dim

dim的不同值表示不同维度。

![image-20220517171805698](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220517171805698.png)

##### FlattenLayer

扁平层。如把 输入1\*28\*28的64张图片 转换为 64*784 矩阵的输入。把每一张图片扁平化为784个元素。

#### Module

函数内部init方法，会自动初始化每个参数。

#### torchvision包

1. `torchvision.datasets`: 一些加载数据的函数及常用的数据集接口；
2. `torchvision.models`: 包含常用的模型结构（含预训练模型），例如AlexNet、VGG、ResNet等；
3. `torchvision.transforms`: 常用的图片变换，例如裁剪、旋转等；
4. `torchvision.utils`: 其他的一些有用的方法。

#### 图像类型Tensor

```python
feature, label = mnist_train[0]
print(feature.shape, label)
#输出
#torch.Size([1, 28, 28]) tensor(9)
```

 Channel x Height x Width （通道C * 高H * 宽W）



#### 概念

**1.凸 与 非凸函数**

凸函数：

- 指的是顺着梯度方向走到底就 **一定是 最优解** 。
- **大部分 传统机器学习 问题** 都是凸的。

非凸函数：

- 指的是顺着梯度方向走到底只能保证是局部最优，**不能保证** 是全局最优，会在局部最优一直徘徊。
- **深度学习**以及小部分传统机器学习问题都是非凸的。

光滑函数：(smooth function)

- 在数学中特指无穷可导的函数，不存在尖点，也就是说所有的有限阶导数都存在



**2.鞍点**（使用梯度下降时，会无法到达全局最优点。但是该点不是函数局部最优点）

出现概率比非凸函数高。

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220512160258165.png" alt="image-20220512160258165" style="zoom:33%;" />



<img src="https://img1.baidu.com/it/u=3654249603,3383892611&fm=253&fmt=auto&app=138&f=JPEG?w=850&h=413" alt="img" style="zoom:25%;" />

在一个方向上是最优点，另一个平面是最差点。所以引进了**惯性动量momentum **。

**3.随机梯度下降(SGD)、批量梯度下降(BGD)、mini-batch梯度下降(MBGD)**

随机梯度下降loss是单个随机数据的损失，梯度计算时只需要一个数据。

梯度下降cost是所有数据的损失，梯度计算时需要全部数据。

mini-batch梯度下降是一种折中的方法，梯度计算时需要一个batch数据。

**随机梯度下降有利于解决鞍点问题。**

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220512163000011.png" alt="image-20220512163000011" style="zoom:80%;" />



- 梯度下降算法使用的是随机梯度下降，还是批量梯度下降，还是mini-batch梯度下降，用的API都是 torch.optim.SGD。要看传进来的数据是单个的还是批量的。



**4.Tensor**

Tensor类型包含 值 和 梯度。（其中梯度也是一个Tensor）

如：权重W定义为Tensor类型，里面含有W值 和 Loss对W的导数。



**5. bias和variance**

偏差和方差

Error = Bias + Variance

Error反映的是整个模型的准确度，Bias反映的是模型在样本上的输出与真实值之间的误差，即模型本身的[精准度](https://www.zhihu.com/search?q=精准度&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A35151681})，Variance反映的是模型每一次输出结果与模型输出期望之间的误差，即模型的稳定性。



![img](https://pic2.zhimg.com/80/v2-ba86376ec8d7c14c0d19c5785a7b2c39_720w.jpg)

https://zhuanlan.zhihu.com/p/45213397

![image-20220522183731531](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220522183731531.png)

Variance：则代表“相同size的**不同的训练数据集**训练出的模型”与“**这些模型的期望输出值**”之间的差异。**训练集变化导致性能变化， Variance 偏高表示模型很不稳定。**



**6.学习衰退率（learning rate decay） **

就是在模型训练初期，会使用较大的学习率进行模型优化。

随着迭代次数增加，学习率会逐渐进行减小，保证模型在训练后期不会有太大的波动。

从而更加接近最优解。



**7.正则化（regularizer）**

规则化就是向你的模型加入某些规则，加入先验，缩小解空间，减小求出错误解的可能性。你要把你的知识数学化告诉这个模型，对代价函数来说，就是加入对模型“长相”的惩罚。

如：fedprox增加了惩罚项λ(w-wt)^2，进行修正。



#### 全连接层

![img](https://tangshusen.me/Dive-into-DL-PyTorch/img/chapter03/3.4_softmaxreg.svg)



#### 隐藏层

<img src="https://tangshusen.me/Dive-into-DL-PyTorch/img/chapter03/3.8_mlp.svg" alt="img" style="zoom:80%;" />

#### 激活函数

​	多个层连接仍然可以被看成一个层。解决问题的一个方法是**引入非线性变换**，例如对隐藏变量使用按元素运算的非线性函数进行变换，然后再作为下一个全连接层的输入。这个非线性函数被称为激活函数（activation function）。

**ReLU：**

​	y = ReLU(x) 当x>=0时，y=x.  当x<0时，y=0.  下图是[-8,8]区间的ReLU函数。

#### <img src="https://tangshusen.me/Dive-into-DL-PyTorch/img/chapter03/3.8_relu.png" alt="img" style="zoom:33%;" />

**sigmoid：**

sigmoid函数可以将元素的值变换到0和1之间。sigmoid(*x*)=1/[1+exp(−x)]. 

但它目前逐渐被更简单的ReLU函数取代。

下图是[-8,8]区间的sigmoid函数。

<img src="https://tangshusen.me/Dive-into-DL-PyTorch/img/chapter03/3.8_sigmoid.png" alt="img" style="zoom:33%;" align="left" />

#### 多层感知机（multilayer perceptron，MLP）

也称**多层神经网络。**

多层感知机就是含有至少一个隐藏层的由全连接层组成的神经网络，且每个隐藏层的输出通过激活函数进行变换。



#### softmax

**解决分类问题。**

通过下式将输出值变换成值为正且和为1的概率分布



#### 欠拟合

答：模型无法得到较低的训练误差，我们将这一现象称作欠拟合（underfitting）。

#### 过拟合

答：模型的训练误差很小，但它在测试数据集上的误差确很大，我们称该现象为过拟合。

应对过拟合问题的常用方法：

​	增大训练数据集  

##### 	权重衰减（weight decay）：

​			目的是**防止过拟合**。

​			作用是**调节模型复杂度对损失函数的影响**，若weight decay很大，则复杂的模型损失函数的值也就大。

##### 	丢弃法（**dropout**）：在训练过程中隐藏的某些特定神经元会被忽略掉（drop）



#### 卷积神经网络 CNN

（Convolutional Neural Network）



### Module介绍

#### MLP 多层神经网络

#### CNN 卷积神经网络

卷积神经网络就是含卷积层的网络。

通俗卷积层：一张图片，通过一堆Filter(卷积核) 提取图片特征，每一个Flilter提取一种特征 [如64个filter提出一张图片会产生高度为64的特征图]，则通过一个卷积层，里面一堆filter，一张图片转换成一个Feature Map。卷积神经网络无非是 卷积层+pool层

![阿三大苏打实打实](https://d2l.ai/_images/lenet.svg)

​						上图是： **LeNet神经网络**(最早的神经网络)，交替使用卷积层和最大池化层后接全连接层来进行图像分类。

代码：

```python
 #convolution layer 卷积层 
 #pooling layer 池化层
 #dense layer 全连接层
 nn.Conv2d(1, 6, 5), # in_channels, out_channels, kernel_size
 nn.Sigmoid()
 nn.MaxPool2d(2, 2) # kernel_size, stride
 nn.Conv2d(6, 16, 5)
 nn.Sigmoid()
 nn.MaxPool2d(2, 2)
 nn.Linear(16*5*5, 120)
 nn.Sigmoid()
 nn.Linear(120, 84)
 nn.Sigmoid()
 nn.Linear(84, 10)
```



##### 0.二维卷积层

二维卷积层将输入和**卷积核**做互相关运算，并加上一个**标量偏差**来得到输出。

若卷积层为1*1，则是全连接层。

##### 1.卷积核 kernel

​	也称过滤器 filter。

​	通过大量数据学习出来的。是一种权重

![img](https://tangshusen.me/Dive-into-DL-PyTorch/img/chapter05/5.1_correlation.svg)

##### 2.特征图和感受野

​	上图的输出，称为特征图。

​	上图中，输入中阴影部分的四个元素是输出中阴影部分元素的感受野。即：输入中[0 1 3 4]是 输出中 19 的感受野。

##### 3.填充和步幅

​	填充（padding）是指在输入高和宽的两侧填充元素（通常是0元素）。即：使得输入高和宽从3变成了5，并导致输出高和宽由2增加到4。

![img](https://tangshusen.me/Dive-into-DL-PyTorch/img/chapter05/5.2_conv_pad.svg)



步幅(stride) 是指每次 卷积核 滑动的行数和列数，步幅可以减小输出的高和宽。即stride=（2，3），下移2右移变成3。

##### 4.多通道

**输入：**

![img](https://tangshusen.me/Dive-into-DL-PyTorch/img/chapter05/5.3_conv_multi_in.svg)

该方法是累加，所以不论输入通道数是多少，输出通道数总是为1

如果希望得到含多个通道的输出，改变卷积核size。

**输出：**

![image-20220517210025692](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220517210025692.png)

```python
 nn.Conv2d(1, 6, 5), # in_channels, out_channels, kernel_size
 #输入通道1，输出为6，核卷积为5*5矩阵。
#给定参数后，系统会自动计算卷积完全size为：Co*Ci*5*5，即6*1*5*5
#通过该卷积，可以使得输入的1通道，输出为6
```



##### 5.池化层

它的提出是**为了缓解卷积层对位置的过度敏感性**。把图片变小，减少运算量。

最大池化层和平均池化层

![img](https://tangshusen.me/Dive-into-DL-PyTorch/img/chapter05/5.4_pooling.svg)

同卷积层一样，2*2的池化层，也可调整**步幅**和**填充**输入。

在处理多通道输入数据时，**池化层对每个输入通道分别池化，而不是像卷积层那样将各通道的输入按通道相加**。即：池化层的输出通道数跟输入通道数相同。



#### self-attention 注意力机制

如：在词性识别 I saw a saw. 需要考虑上下文关系，可先通过该技术（输出的向量就会包含上下文信息），再输入到神经网络。

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220608162725755.png" alt="image-20220608162725755" style="zoom: 33%;" />

attention里面，是计算两个不同的向量关联程度α。（这里计算方法有多种，如Dot-product）

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220608164053804.png" alt="image-20220608164053804" style="zoom:33%;" />

最终累加所有有关a1的α，得到b1，b1的值主要由谁与a1关联度最大主导。

与CNN的关系：

![image-20220608170854572](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220608170854572.png)



与RNN的关系：（attention替代了RNN）

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220608170939131.png" alt="image-20220608170939131" style="zoom:67%;" />



#### Transformer

是一种 sequence to sequence的模型。

#### Bert

与transformer很有关系。



#### 	AlexNet 深度卷积神经网络



#### 	VGG  使用重复元素的网络







#### 层

##### 全连接层

28\*28像素 --> 1\*784 扁平化。全连接

##### 卷积层

用卷积层代替全连接层的另一个好处是：模型更简洁、所需的参数更少

##### 池化层

##### 循环层



### 优化器

https://zhuanlan.zhihu.com/p/261695487

#### SGD 

随机梯度下降

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220531151351691.png" alt="image-20220531151351691" style="zoom:80%;" />

#### SDG-M

含一阶动量的随机梯度下降。

m表示动量（由此前累计下降梯度决定）

mt = β*此前累计梯度 + (1-β)\*当前梯度，β越大意味着下降方向主要是此前累积的下降方向。

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220531150923516.png" alt="image-20220531150923516" style="zoom:80%;" />



#### NAG

Nesterov Accelerated Gradient，是在SGD、SGD-M的基础上的进一步改进。

用**下一个点的梯度方向**，与历史累积动量相结合。<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220531151513468.png" alt="image-20220531151513468" style="zoom:80%;" />



#### **AdaGrad**

自适应学习率算法，不再统一学习率。

对不同的参数学习率不同（经常更新，即梯度大的参数，调小学习率。反之很少更新的参数，调大学习率）

使用二阶动量V：

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220531151601483.png" alt="image-20220531151601483" style="zoom:80%;" />

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220531151625161.png" alt="image-20220531151625161" style="zoom:80%;" />

动量V是标量。

因为![[公式]](https://www.zhihu.com/equation?tex=%5Csqrt%7BV_t%7D) 是单调递增的，会使得学习率单调递减至0，无法继续学习。



#### AdaDelta/RMSProp

增强的AdaGrad。

由于AdaGrad单调递减的学习率变化过于激进，考虑一个改变二阶动量计算方法的策略：不累积全部历史梯度，而只关注过去一段时间窗口的下降梯度。这也就是AdaDelta名称中Delta的来历。

只要过去一段时间的梯度，因此我们用这一方法来计算二阶累积动量：

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220531152029535.png" alt="image-20220531152029535" style="zoom:80%;" />



#### Adam

**集齐上述方法（一阶动量+二阶动量）**

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220531152442327.png" alt="image-20220531152442327" style="zoom:80%;" />

优化算法里最常见的两个超参数 ![[公式]](https://www.zhihu.com/equation?tex=+%5Cbeta_1%2C+%5Cbeta_2) 就都在这里了，前者控制一阶动量，后者控制二阶动量。

**新增修改：**

二阶动量是固定时间窗口内的累积，随着时间窗口的变化，遇到的数据可能发生巨变，使得![[公式]](https://www.zhihu.com/equation?tex=V_t)可能会时大时小，不是单调变化。这就可能在训练后期引起学习率的震荡，导致模型无法收敛。

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220531153123902.png" alt="image-20220531153123902" style="zoom:80%;" />

缺点：自适应学习率算法可能会对前期出现的特征过拟合，后期才出现的特征很难纠正前期的拟合效果。后期Adam的学习率太低，影响了有效的收敛。



####  Nadam

 Adam + NAG



### 损失函数

https://zhuanlan.zhihu.com/p/58883095

​	**损失函数**分为**经验风险损失函数**和**结构风险损失函数**。

​		经验风险损失函数：指预测结果和实际结果的差别

​		结构风险损失函数：指经验风险损失函数加上正则项。

#### 0-1损失函数(zero-one loss)

​	0-1损失是指预测值和目标值不相等为1， 否则为0。

特点：

 - 直接对应分类判断错误的个数，但是它是一个非凸函数，不太适用.

#### **绝对值损失函数**

​	绝对值损失函数是计算预测值与目标值的差的绝对值。

#### **log对数损失函数**

X为特征，Y为标签。P(Y|X)表示X条件下发生Y的概率（0<P<1）, 因此L的取值范围为(+∞, 0)。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202212081544436.png" alt="image-20221208154422658" style="zoom: 80%;" />

p概率很小时，L接近无穷大。p概率很大时，L接近0。

#### **平方损失函数**

X为特征，Y为标签。f(X)表示预测值。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202212081600314.png" alt="image-20221208160048256" style="zoom:80%;" />

#### **指数损失函数**

exp(x)表示e的x次方。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202212081611627.png" alt="image-20221208161106585" style="zoom:80%;" />

对离群点、噪声非常敏感。

#### **Hinge 损失函数**

#### **感知损失函数(perceptron)**

Hinge损失函数的一个变种。

#### **交叉熵损失函数 (Cross-entropy)**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202212081625509.png" alt="image-20221208162552436" style="zoom:80%;" />
