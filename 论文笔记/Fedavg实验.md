## FedAVG

论文实验结论：

​	在minist、no-iid，cnn模型条件下，E=20，B=10，C=0.1 准确率达到99%，只需要173轮。

​	在minist、no-iid，cnn模型条件下，E=5，B=10，C=0.1 准确率达到99%，只需要206轮。（在此条件下，增大C比率，准确率提升非常小）

​	在minist、no-iid，2nn模型条件下，E=1，B=10，C=0.1 准确率达到97%，需要664轮。（在此条件下，增大C比率，准确率提升非常小）

越多训练次数越好。

### mnist数据集--mlp模型

⭐**global_epoch = 100 , local_epoch = 10 , batch_size = 10**   

> |---- Avg Train Accuracy: 83.90%
> |---- Test Accuracy: 84.90%

⭐**global_epoch = 200 , local_epoch = 10 , batch_size = 10**   

> ---- Avg Train Accuracy: 90.52%
> ---- Test Accuracy: 91.22%

⭐**global_epoch = 300 , local_epoch = 10 , batch_size = 10**   

> |---- Avg Train Accuracy: 92.08%
> |---- Test Accuracy: 93.25%

**global_epoch = 100 , local_epoch = 20 , batch_size = 10**   (增大local_epoch)

> |---- Avg Train Accuracy: 91.52%
> |---- Test Accuracy: 90.90%

**global_epoch = 100 , local_epoch = 5 , batch_size = 10**   (减少local_epoch)

> |---- Avg Train Accuracy: 89.43%
> |---- Test Accuracy: 89.67%

**global_epoch = 500 , local_epoch = 10 , batch_size = 10**   (增大global_epoch)

> |---- Avg Train Accuracy: 93.13%
> |---- Test Accuracy: 93.71%

**global_epoch = 500 , local_epoch = 20 , batch_size = 10**   (增大global_epoch，增大local_epoch)

> |---- Avg Train Accuracy: 89.70%
> |---- Test Accuracy: 90.09%

⭐**global_epoch = 20 , local_epoch = 50 , batch_size = 10**   (小幅增大global_epoch，大幅增大local_epoch)

> |---- Avg Train Accuracy: 82.47%
> |---- Test Accuracy: 84.62%

⭐**global_epoch = 10 , local_epoch = 100 , batch_size = 10**   (小幅增大global_epoch，大幅增大local_epoch)

> |---- Avg Train Accuracy: 65.98%
> |---- Test Accuracy: 67.45%

**global_epoch = 500 , local_epoch = 5 , batch_size = 10**   (增大global_epoch，减少local_epoch)

> |---- Avg Train Accuracy: 93.47%
> |---- Test Accuracy: 93.53%



### mnist数据集--cnn模型

**global_epoch = 100 , local_epoch = 10 , batch_size = 10**   

> |---- Avg Train Accuracy: 93.83%
> |---- Test Accuracy: 94.48%

⭐**global_epoch = 200 , local_epoch = 10 , batch_size = 10**   

> |---- Avg Train Accuracy: 98.38%
> |---- Test Accuracy: 98.20%





按fedavg论文实验2NN达到97%，实际MLP同样条件下只能到 84.5%左右（模型差距）

按fedavg论文实验CNN达到99%，实际CNN同样条件下只能到 **97.50%**左右。✔（使用该模型，目标是达到97.3%）





### 更改欧氏距离计算新方法（聚合后的模型）

### mnist_cnn（98%精度）

**non-iid四类，frac=0.7，epoch=10**   

|                   | 环境1    |      |      |      |
| ----------------- | -------- | ---- | ---- | ---- |
| fedavg            | 87 Round |      |      |      |
| α=0，β=1 ：       | 77 Round |      |      |      |
| α=0.25，β=0.75 ： | 69 Round |      |      |      |
| α=0.5，β=0.5 ：   | 81 Round |      |      |      |
| α=0.75，β=0.25 ： | 74 Round |      |      |      |
| α=1，β=0 ：       | 82 Round |      |      |      |

**non-iid四类，frac=0.5，epoch=10**   

|                   | 环境1 | 环境2 |      |      |
| :---------------- | ----- | ---- | ---- | ---- |
| fedavg            | 44 Round | 45 Round |      |      |
| α=0，β=1 ：       | 48 Round | 53 Round |      |      |
| α=0.25，β=0.75 ： | 42 Round | 46 Round |      |      |
| α=0.5，β=0.5 ：   | ❌ | 57 Round |      |      |
| α=0.75，β=0.25 ： | ❌ | 42 Round |      |      |
| α=1，β=0 ：       | 57 Round | 30 Round |      |      |

**non-iid四类，frac=0.3，epoch=10**   

|                   | 环境1    | 环境2    | 环境3    | 环境4（公平的用户选择） | 环境5（公平用户选择+一轮后在分权） | 环境6（一轮后在分权） |
| ----------------- | -------- | -------- | -------- | ----------------------- | ---------------------------------- | --------------------- |
| fedavg            | 32 Round | 31 Round | 32 Round | 33 Round                | 25 Round                           | 27 Round              |
| α=0，β=1 ：       | 32 Round | 33 Round | 37 Round | 29 Round                | 27 Round                           | 29 Round              |
| α=0.25，β=0.75 ： | 36 Round | 34 Round | 26 Round | 35 Round                | 30 Round                           | 37 Round              |
| α=0.5，β=0.5 ：   | 31 Round | 30 Round | 37 Round | 33 Round                | 30 Round                           | 30 Round              |
| α=0.75，β=0.25 ： | 32 Round | 26 Round | 37 Round | 29Round                 | 28 Round                           | 28 Round              |
| α=1，β=0 ：       | 36 Round | ❌        | 32 Round | 33 Round                | 30 Round                           | 27 Round              |

**non-iid四类，frac=0.2，epoch=10** 

|                   | 环境1 |      |      |      |
| ----------------- | ----- | ---- | ---- | ---- |
| fedavg            |       |      |      |      |
| α=0，β=1 ：       |       |      |      |      |
| α=0.25，β=0.75 ： |       |      |      |      |
| α=0.5，β=0.5 ：   |       |      |      |      |





### mnist_cnn（98.5%精度）

**non-iid四类，frac=0.7，epoch=10**  

|                   | 环境1     |      |      |      |
| ----------------- | --------- | ---- | ---- | ---- |
| fedavg            | 149 Round |      |      |      |
| α=0，β=1 ：       | 136 Round |      |      |      |
| α=0.25，β=0.75 ： | 162 Round |      |      |      |
| α=0.5，β=0.5 ：   | 180 Round |      |      |      |
| α=0.75，β=0.25 ： | 158 Round |      |      |      |
| α=1，β=0 ：       | 191 Round |      |      |      |

**non-iid四类，frac=0.5，epoch=10** 

|                   | 环境1     | 环境2 |      |      |
| ----------------- | --------- | ----- | ---- | ---- |
| fedavg            | 85 Round  |       |      |      |
| α=0，β=1 ：       | 85 Round  |       |      |      |
| α=0.25，β=0.75 ： | 85 Round  |       |      |      |
| α=0.5，β=0.5 ：   | 112 Round |       |      |      |
| α=0.75，β=0.25 ： | 120 Round |       |      |      |
| α=1，β=0 ：       | 70 Round  |       |      |      |

**non-iid四类，frac=0.3，epoch=10**  

|                   | 环境1    | 环境2    | 环境3（公平的用户选择） | 环境4（公平用户选择+一轮后在分权） | 环境5（一轮后在分权） |
| ----------------- | -------- | -------- | ---- | ---- | ----------------- |
| fedavg            | 50 Round | 54 Round | 57 Round | 57 Round | 61 Round |
| α=0，β=1 ：       | 73 Round | 71 Round | 62 Round | 53 Round | 54 Round |
| α=0.25，β=0.75 ： | 62 Round | 52 Round | 64 Round | 59 Round | 65 Round |
| α=0.5，β=0.5 ：   | 64 Round | 67 Round | 50 Round | 66 Round | 56 Round |
| α=0.75，β=0.25 ： | 43 Round | 53 Round | 60 Round | 61 Round | 61 Round |
| α=1，β=0 ： | ❌ | 44 Round | 57 Round | 48 Round | 59 Round |



### mnist_cnn（99%精度）

**non-iid四类，frac=0.7，epoch=10**  

|                   | 环境1   | 环境2 |      |      |
| ----------------- | ------- | ----- | ---- | ---- |
| fedavg            | ❌Round  |       |      |      |
| α=0，β=1 ：       | ❌ Round |       |      |      |
| α=0.25，β=0.75 ： | ❌Round  |       |      |      |
| α=0.5，β=0.5 ：   | ❌Round  |       |      |      |
| α=0.75，β=0.25 ： | ❌Round  |       |      |      |
| α=1，β=0 ：       | ❌Round  |       |      |      |

**non-iid四类，frac=0.5，epoch=10** 

|                   | 环境1       | 环境2 |      |      |
| ----------------- | ----------- | ----- | ---- | ---- |
| fedavg            | 325 Round   |       |      |      |
| α=0，β=1 ：       | 286 Round   |       |      |      |
| α=0.25，β=0.75 ： | 366 Round   |       |      |      |
| α=0.5，β=0.5 ：   | > 600 Round |       |      |      |
| α=0.75，β=0.25 ： | 398 Round   |       |      |      |
| α=1，β=0 ：       | 411 Round   |       |      |      |

**non-iid四类，frac=0.3，epoch=10**   

|                   | 环境1     | 环境2（公平的用户选择） | 环境3（公平用户选择+一轮后在分权） | 环境4（一轮后在分权） |
| ----------------- | --------- | ----------------------- | ---------------------------------- | --------------------- |
| fedavg            | 188 Round | 308 Round               | >350 Round                         | 224 Round             |
| α=0，β=1 ：       | 259 Round | >500 Round              | 160 Round                          | 213 Round             |
| α=0.25，β=0.75 ： | 169 Round | >500 Round              | 350 Round                          | 288 Round             |
| α=0.5，β=0.5 ：   | 223 Round | 218 Round               | 331 Round                          | 202 Round             |
| α=0.75，β=0.25 ： | 240 Round | 363 Round               | 281 Round                          | >350 Round            |
| α=1，β=0 ：       | 191 Round | 237 Round               | 156 Round                          | 212 Round             |



公平的用户选择： 每过10 round，100个用户都被选一次。

究极公平的用户选择： 除了每个用户都会被选择外，每次选择的iid为7个，non-iid为3个。





论文《Fast-Convergent Federated Learning With Adaptive Weighting》

条件：cnn，mnist，10个用户 (7个 non-iid)，E=1，B=32。精度达到90%需要的通信轮次。 **U=20**

​	Fedavg：100

​	FedAdp: 72

​	α=0.5，β=0.5 ：76

​	α=0.75，β=0.25 ：100

​	α=1，β=0 ：55、56





### 降低Epoch=5，增加batchSize=50 , u=60

cnn，mnist，四类，frac=0.7

cnn，mnist，四类，frac=0.3

**总结：没提升**



### 降低Epoch=5，增加batchSize=50，增大用户比例C=0.4 , u=60

cnn，mnist，三类，frac=0.7 ⏳

|                   | 97% |  98%    |      |      |
| ----------------- | -------- | ---- | ---- | ---- |
| fedavg            | 102 Round |      |      |      |
| α=0，β=1 ：       | 111 Round |      |      |      |
| α=0.25，β=0.75 ： |          |      |      |      |
| α=0.5，β=0.5 ：   |          |      |      |      |
| α=0.75，β=0.25 ： |          |      |      |      |
| α=1，β=0 ：       | 103 Round |      |      |      |

### 降低Epoch=1，增加batchSize=32，增大用户比例C=1, u=20

cnn，mnist，三类，frac=0.7 ⏳

|                   | 95%       | 98%  |      |      |
| ----------------- | --------- | ---- | ---- | ---- |
| fedavg            | 141 Round |      |      |      |
| α=0，β=1 ：       | 140 Round |      |      |      |
| α=0.25，β=0.75 ： |           |      |      |      |
| α=0.5，β=0.5 ：   |           |      |      |      |
| α=0.75，β=0.25 ： |           |      |      |      |
| α=1，β=0 ：       | 112 Round |      |      |      |





## Fedavg FedADP FedSA

10个用户(0.7异构)，E=1

![image-20221215204018826](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202212152040963.png)

100个用户(0.9异构)，E=1

![image-20221215204310426](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202212152043593.png)





论文：https://ieeexplore.ieee.org/abstract/document/9446648 欧几里得和余弦距离，选择了余弦

​			https://ieeexplore.ieee.org/abstract/document/9439129 能够通过识别客户端中特定神经元之间的差异来修改其模型架构，可能用到余弦定理。

​		

https://www.sciencedirect.com/science/article/abs/pii/S0167739X22001686?via%3Dihub non-iid最新综述

1. The non-IID data quagmire of decentralized machine learning **指出解决非iid数据问题的难度取决于其偏离程度。**

2. ♥ Dynamic Sample Selection for Federated Learning with Heterogeneous Data in Fog Computing

​	选择用户方法。



