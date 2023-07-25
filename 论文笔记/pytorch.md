## 项目知识

### 导入新项目

一、创建新conda环境（pycharm）

二、导入依赖包（通过作者给予的requirement.txt文件）

**指令：pip install -r requirements.txt**

以下指令是将当前虚拟环境中的依赖包以版本号生成至requirements.txt文件中：

**pip freeze > requirements.txt**



### 安装包

在线安装

1. 用conda install pkgname即可，pkgname为包名

2. 当找不到包的时候，尝试下面的语句

    conda install -c conda-forge pkgname

3. 依旧找不到的话，用下面的搜索包名，进行最后尝试

    anaconda search -t conda pkgname    找到需要的pkgname全名后，输入下面的指令
    anaconda show pkgname（全名），会列出channel_url
    最后根据列出来的url（就是看着像网址的那些）选择相应版本，输入下面的指令进行下载
    conda install --channel channel_url  pkgname

原文链接：https://blog.csdn.net/Shadow_heaven/article/details/89405282



## 基础知识

### Tensor

Tensor是机器学习中模型参数的基本类型。

`torch.Tensor`是一种包含单一数据类型元素的多维矩阵。

`torch.Tensor`是默认的tensor类型（`torch.FlaotTensor`）的简称。

**Tensor与Numpy区别：**

- Tensor和Numpy最大不同是，Tensor可以GPU计算和自动求梯度等，其他什么区别，都是可n维数组计算的工具。
- Tensor和Numpy很容易互相转换类型。

**numpy类**

![image-20221024191728469](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202210241917523.png)

**Tensor类**

<img src="C:/Users/10275/AppData/Roaming/Typora/typora-user-images/image-20221024172434584.png" alt="image-20221024172434584" style="zoom:80%;" />

属性+方法分析 https://www.jianshu.com/p/b6e4bbe5b16c

常见的属性：

```python
T：返回Tensor的转置。
data： 返回张量的数据,类型为Tensor，就是张量本身。（但是data返回的数据状态改变：require s_grad = False）
dtype： Tensoor具体每个元素类型。

导数相关：
grad：导数，默认为None。
grad_fn：计算导数的函数。
requires_grad：grad_fn函数自动跟踪，需要设置requires_grad=True

gpu计算相关：
is_cuda：判断是否是cuda计算（GPU计算），Boolean类型。
device：返回device专门的类构造。

其他：
shape：Tensor的形状。tensor.shape和torch.size(tensor)返回结果一样。
ndim: 与属性dim一样，是tensor维度。（看最左边几个“[”中括号，就是几个维度）
                                  
```

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202210241841817.png" alt="img" style="zoom: 33%;" />

```python
上述图片分别对应：
torch.tensor([2.0, 3.0, 4.0])
torch.tensor([ [1.0, 2.0, 3.0],[4.0, 5.0, 6.0] ])
torch.tensor([
	[ [1,2,3,4,5],[6,7,8,9,10] ] ,
	[ [11,12,13,14,15],[16,17,18,19,20] ]
])
```

常用的方法：

```python
1.加法操作

2.矩阵乘法
    A * B 表示按元素计算
    tensor.mul(A,B)  表示按元素计算
    tensor.matmul(A,B) 表示矩阵乘法
3.数据类型转换
    tensor.to_double(a)
    tensor.to_float(a)
4.形状操作
    tensor.reshape(a, (1,4))
5.数组方面的 切片和合并
6.矩阵相关操作
    tensor.matrix_inverse  方阵的逆矩阵  
    tensor.matrix_determinant  方阵的行列式
    tensor.transpose转置  
    tensor.diag  给定对角线上的值，返回对角tensor
7.Tensor 和 numpy array互转
	tensor转numpy：tensor = tensor.numpy()
    numpy转tensor：tensor = torch.from_numpy(numpy)
    
8.将Tensor在CPU和GPU（需要硬件支持）之间相互移动。需重新赋值
	获取device = torch.device("cuda") ，tensor = tensor.to(device) 
    或者直接 tensor = tensor.to("cuda") 
    
9.其他
扁平化：flatten(start_dim=0, end_dim=-1)，参数start_dim为开始的维度，从0开始计算。
```

操作图

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202210241740383.jpeg" alt="img" style="zoom:5%;" />



**张量、矩阵、向量、标量**

Tesnor也称“张量”，可以看作是一个多维数组。(在深度学习的范畴内，标量、向量和矩阵都统称为张量)

标量可以看作是0维张量，向量可以看作1维张量，矩阵可以看作是二维张量。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202210241822023.png" alt="图片" style="zoom:80%;" />

**Tensor和tensor区别**

在pytorch中，`torch.Tensor`是一个类，所有的张量都是 `torch.Tensor`的实例。

相反，`torch.tensor()`是一个返回Tensor类型的函数.

```python
torch.tensor(data, dtype=None, device=None, requires_grad=False) → Tensor
```

注意：

- torch.Tensor（data）是将输入的data转化torch.FloatTensor
- torch.tensor(data):  将data转化为torch.FloatTensor、torch.LongTensor、torch.DoubleTensor等类型，当你未指定dtype的类型时，转化类型依据于data的类型。

**Tensor、numpy、list互转**

```python
ndarray = np.array(list)  # list 转 numpy数组
list = ndarray.tolist()  # numpy 转 list
tensor = torch.Tensor(list)  # list 转 torch.Tensor
list = tensor.numpy().tolist()  # torch.Tensor 转 list  先转numpy，后转list
ndarray = tensor.cpu().numpy()  # torch.Tensor 转 numpy  *gpu上的tensor不能直接转为numpy
tensor = torch.from_numpy(ndarray)  # numpy 转 torch.Tensor
```





## 机器学习训练全过程

### 1.数据加载dataloader

默认按顺序加载数据集的策略

```python
train_dataset = datasets.CIFAR10(dataset=data_dir, train=True, download=True,
                               transform=apply_transform)

test_dataset = datasets.CIFAR10(dataset=data_dir, train=False, download=True,
                              transform=apply_transform)
```

参数**batch_size** ：每批装载多少个样本，默认为 1。

参数**shuffle：** 每次epoch是否打乱，默认false。



### 2.train 训练过程

#### 创建

1. 模型model
2. Loss函数（不同的函数参数不一样）

```python
loss_fn = nn.CrossEntropyLoss()
```

3. 优化器（不同优化器参数不一样。但至少包含下面两个：当前训练model，学习率）

```python
optimizer = optim.SGD(mymodel.parameters(), lr=learning_rate)
```

#### 训练

```python
mymodel.train()
```

1. 从dataloader取出数据
2. 得到预测值，然后计算损失（ y^ - y） 

```python
outputs = mymodel(imgs)
loss = loss_fn(outputs, targets) # targets是真实的结果，outputs是预测结果
```

（ Loss函数计算时，需要提供参数为：input,target，直接调用loss()时，内部实际调用的是forward()函数来计算误差 ）

3. 利用loss反向传播得到梯度，优化参数

```python
optimizer.zero_grad()  # 把每个参数上一次梯度清零。不然执行backward()会继续累加梯度。
loss.backward()  # 反向传播计算得到每个参数的梯度值。如果不清零，再次调用会累加。
				 # 最后将梯度存到每个参数W(w.grad.data)。(w.data是权重的值)
optimizer.step()  # 模型参数更新（w = w - η*g）
```

- 输出损失时一般喜欢写 loss.item() 而不是 loss。item()函数意思是转换成数据型（标量）。

- 分类问题一般还会计算精度，即预测正确性。(在测试数据集中，预测正确数量 / 预测的总数量)

- 某些模型需要写 mymodel.train() 和 mymodel.eval()。无论与否一般都默认写。 

#### 测试

```python
mymodel.eval()
```

1. 开头使用with关键字(类似try catch)：

```python
with torch.no_grad(): #取消梯度计算，加快运行速度。因为测试精度和计算loss，不需要梯度
```

2. 利用loss函数，计算损失

3. 保存模型

```python
torch.save(mymodel, "model/tudui_{}.pth".format(i)) # i表示当前epoch
#formt格式化字符串，即把字符串中的'{}'用 i 来替代。
#一般使用pth为后缀
```

 加载时：*model = torch.load("model/tudui_{}.pth")*

**官方推荐的保存模型格式（不传整个模型，只传参数，利于节省空间加快速率）：**

采用 **model.state_dict()** 参数字典：

​	存储：torch.save(vgg16.state_dict(), "vgg16_method2.pth")

​	加载：

```python
#创建模型结构，但采取初始值（即预训练为false。）
#预训练是官方会预先用其他数据对该模型进行训练，一会自己训练其他数据时，参数更容易收敛
vgg16 = torchvision.models.vgg16(pretrained=False) 
#加载vgg16_method2.pth的所有参数
vgg16.load_state_dict(torch.load("vgg16_method2.pth")) 
```



**model.parameters() 和 model.state_dict() 区别**

parameters() 返回的是一个generator。state_dict() 返回是一个顺序字典，键值对。

parameters没有对应的key名称，是一个由纯参数组成的generator，而state_dict是一个字典，包含了一个key。



### Gpu训练

**模型、Loss函数、数据加载** 这三类可以使用gpu加速。

方式一：

```python
tudui = Tudui()
if torch.cuda.is_available():   
    tudui = tudui.cuda() 
```

方式二：

```python
# 定义训练的设备
#有gpu时，device取值是“gpu”。则会优先使用gpu资源，不够再用cpu
device = torch.device("cuda" if torch.cuda.is_available() else "cpu") 
#模型采用gpu加速
mymodel = MyModel()
mymodel.to(device)
#Loss函数采用gpu加速
loss_fn = nn.CrossEntropyLoss()
loss_fn.to(device)
#数据加载采用gpu加速。不能直接imgs.to(device)，需要重新赋值
 imgs = imgs.to(device)
 targets = targets.to(device)
```

 从cpu运算转化为gpu运算：to(device) 或 cuda()



### Model参数（图片）

**model定义：**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209271608874.png" alt="image-20220927160837813" style="zoom:80%;" />

**CNN模型：（该模型含有conv1层，conv2层，conv2_drop层，fc1线性层，fc2线性层）**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209271605174.png" alt="image-20220927160523122" style="zoom:150%;" />

**每一层：含有bias和权重w（Tensor类型），stride，输入输出维度等信息。**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209271610128.png" alt="image-20220927161026066"  />

**Model.state_dict( )信息**

![image-20220927161833539](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209271618590.png)

如CNN模型，它的state_dict中的key如下所示：

```text
conv1.weight 
conv1.bias
conv2.weight 
conv2.bias
fc1.weight
fc1.bias
fc2.weight
fc2.bias
...
```







## 参数agrs使用

**特殊参数1：**（Argparse中action的可选参数store_true和store_false）

```python
#store_true 是指带触发action时为真，不触发则为假
parser.add_argument('--all_clients', action='store_true')
```

需要在命令行 terminal执行：

```python
python main_fed.py --all-clients
```

- 输入该参数名称时，args.all_clients为True  [属性action='store_true']
- 输入该参数名称时，args.all_clients为False  [属性action='store_false']



**特殊参数2：**（参数设置为require的内容）

​	运行时，一定要触发它(即命令输入)，否则无法执行。



**创建和使用：**

定义一个方法。

```python
def args_parser():
    # 步骤一，创建解析器
    parser = argparse.ArgumentParser()
    # 添加参数
    parser.add_argument('--split', type=str, default='user', help="")
    # 返回解析器的参数
    args = parser.parse_args()
    return args

if __name__ == '__main__':
    # 直接调用方法args_parser()，即可获取参数解析器
    args = args_parser()
	# 具体使用方法 args.参数名称。（参数名去掉头部--）
    # 如： args.split
```





```python
net = CNNMnist()
net.state_dict() #返回一个参数字典： conv1.weigth：xxx ，conv1.bias:xxx ......
net.parameters() #返回一个参数迭代器： 包含n个w，w为tensor类型。
for w in net.parameters()
	print(w.data) #返回具体数据。Tensor类型包含数据和梯度。
```





## TensorBoard的使用

### 配置

1. 导入包（方式二选一）

```text
from torch.utils.tensorboard import SummaryWriter  
from tensorboardX import SummaryWriter
```

2. 首先我们将SummaryWriter实例化

```text
writer = SummaryWriter('./path/to/log')
```

这里传入的参数就是指向文件夹的路径，之后我们使用这个writer对象“拿出来”的任何数据都保存在这个路径之下。

3. 这个对象包含多个方法，比如针对数值，我们可以调用

①画图Loss、Accuary

```text
writer.add_scalar(tag, scalar_value, global_step=None, walltime=None)
```

这里的tag指定可视化时这个变量的名字，scalar_value是你要.存的值，global_step可以理解为x轴坐标。

②画模型

```python
add_graph(model, input_to_model=None, verbose=False, use_strict_trace = True)
```

③记录实验的超参数

add_hparams

### **可视化**

我们已经将关心的数据拿出来了，接下来我们只需要在**命令行**运行：

```text
tensorboard --logdir=./path/to/the/folder --port 8123
```

不写端口号，系统自动分配。

然后打开浏览器，访问地址[http://localhost:8123/](https://link.zhihu.com/?target=http%3A//localhost%3A8123/)即可。这里的8123只是随便一个例子，用其他的未被占用端口也没有任何问题，注意命令行的端口与浏览器访问的地址同步。



**另一点要注意的是tensorboard并不是实时显示（visdom是完全实时的），而是默认30秒刷新一次。**

tensorboard教程上篇：https://zhuanlan.zhihu.com/p/103630393



## python 基础知识

### python package

`__init__.py`该文件的作用就是相当于把自身整个文件夹当作一个包来管理，每当有外部`import`该包的时候，就会自动执行里面的函数。

### 赋值、浅拷贝和深度拷贝

**直接赋值**：其实就是对象的引用（别名）。

**浅拷贝(copy)**：拷贝父对象，不会拷贝对象的内部的子对象。（部分新建，部分引用）

**深拷贝(deepcopy)**： copy 模块的 deepcopy 方法，完全拷贝了父对象及其子对象。（完全新建了一份数据，没有引用）

1、**b = a:** 赋值引用，a 和 b 都指向同一个对象。

![img](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202210261615231.png)

**2、b = a.copy():** 浅拷贝, a 和 b 是一个独立的对象，但他们的子对象还是指向统一对象（是引用）。

![img](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202210261615250.png)

**b = copy.deepcopy(a):** 深度拷贝, a 和 b 完全拷贝了父对象及其子对象，两者是完全独立的。

![img](https://www.runoob.com/wp-content/uploads/2017/03/1489720930-5882-BO4qO.png)
