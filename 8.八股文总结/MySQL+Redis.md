# 数据库知识

## 基础

数据库范式有 3 种：

- 1NF(第一范式)：要求任何一张表必须有主键，每一个字段原子性不可再分。

- 2NF(第二范式)：建立在第一范式的基础之上，要求所有非主键字段完全依赖主键，不要产生部分依赖。

  【总结：多对多，三张表，关系表两个外键！】

- 3NF(第三范式)：建立在第二范式的基础之上，要求所有非主键字段直接依赖主键，不要产生传递依赖。

​		【总结：一对多，两张表，多的表加外键！】

例子链接 https://blog.51cto.com/u_15178976/3689833

MySQL执行流程：

![img](https://oss.javaguide.cn/javaguide/13526879-3037b144ed09eb88.png)

从上图可以看出， MySQL 主要由下面几部分构成：

- **连接器：** 身份认证和权限相关(登录 MySQL 的时候)。
- **查询缓存：** 执行查询语句的时候，会先查询缓存（MySQL 8.0 版本后移除，因为这个功能不太实用）。
- **分析器：** 没有命中缓存的话，SQL 语句就会经过分析器，分析器说白了就是要先看你的 SQL 语句要干嘛，再检查你的 SQL 语句语法是否正确。
- **优化器：** 按照 MySQL 认为最优的方案去执行。
- **执行器：** 执行语句，然后从存储引擎返回数据。 执行语句之前会先判断是否有权限，如果没有权限的话，就会报错。
- **插件式存储引擎** ： 主要负责数据的存储和读取，采用的是插件式架构，支持 InnoDB、MyISAM、Memory 等多种存储引擎。



## 事务

ACID特性：

- A-原子性-不可分割的单位

- C-一致性-事务必须使数据库从一个状态便回到另一个状态

- I-隔离性-多个并发事务之间要相互隔离

- D-持久性-事务一旦提交对数据库的改变是永久性的

保存点（savepoint）和 回滚（rollback）：启动了事务一组操作会先生效。但只要还未执行提交commit，就可以进行回滚操作，会到任意的保存点。

**MySQL 默认采用自动提交模式。**也就是说，如果不显式使用`START TRANSACTION`语句来开始一个事务，那么每个查询操作都会被当做一个事务并自动提交。

## 隔离级别

事务并发会出现的问题

### 出现的问题

- 脏读 （读了另一个事务尚未提交数据）
- 不可重复读 （单条数据会变化，有事务修改这条数据，每次读结果不一样）
- 幻读（结果集数量会变化，有线程插入新的数据，每次读取的数量不一样）



解决问题的隔离级别类型

### 隔离级别类型

- 读未提交
- 读已提交
- 可重复读(默认)：把读取到的数据加锁锁住，不允许其他控制台修改或删除。但无法避免其他控制台insert数据，因为仅仅锁住了刚刚读到的数据。
- 串行执行：加锁。当其他事务在操作某一张表时，若本事务想要操作这张表会被锁住（即会等待，等到其他表commit后才可以操作）。

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/image-20191207223400787.png" alt="img" style="zoom:80%;" />



隔离级别实现机制是加锁，锁的分类。

### 锁

![image-20220711203519162](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202304191634438.png)

#### 态度划分

- 悲观锁（数据库自带）
  - 共享锁（S锁）：数据加了共享锁，其他事务只能读到该数据，不能修改数据。
  - 排他锁（X锁）：数据加了排他锁，其他事务既不能读该数据，也不能修改数据。
- 乐观锁（通过程序实现）

#### 粒度划分

MySQL 中提供了两种封锁粒度：行级锁以及表级锁。

应该尽量只锁定需要修改的那部分数据，而不是所有的资源。锁定的数据量越少，发生锁争用的可能就越小，系统的并发程度就越高。在选择封锁粒度时，需要在锁开销和并发程度之间做一个权衡。

#### 封锁类型

**读写锁**

- 互斥锁（Mute Exclusive），简写为 X 锁，又称写锁。
- 共享锁（Shared），简写为 S 锁，又称读锁。

排他锁（Exclusive Lock）和 互斥锁（Mutual Exclusion Lock）区别：

一个事务持有了一个数据行的排他锁时，其他事务不能对该数据行进行读或写操作，直到该事务释放了该锁。

一个事务对数据对象 A 加了互斥锁，就可以对 A 进行读取和更新。加锁期间其它事务不能对 A 加任何锁。

**意向锁**

检测一个行级/表级数据是否加了X/S锁。

原理：一个事务在获得某个数据行对象的 S/X锁之前，必须先获得表的 IS/IX 锁或者更强的锁；通过引入意向锁，事务想要对表 A 加 X 锁，只需要先检测是否有 IX/IS 锁，如果加了就表示有其它事务正在使用这个表，因此事务 T 加 X 锁失败。

**其他**

- 全局锁（Global Lock）：是指对整个数据库实例进行加锁，以保证整个数据库实例的原子性和一致性。
- 死锁（Deadlock）：是指两个或多个事务相互等待对方释放锁而陷入无限等待的状态。

- 自增锁：主键或关键字`AUTO_INCREMENT`修饰的列都会涉及到自增锁，一个事务正在插入数据到有自增列的表时，会先获取自增锁，可以理解为自增锁就是一个接口，其具体的实现有多种。具体实现则是根据表数据自动添加一个id号。

  

### 传播机制

多个事务的方法在相互调用时，事务是如何在这些方法间传播的。



# MySQL

## 索引

### 入门知识

为数据建立索引，可以提高数据库的查询速度。数据存储空间会增加。

#### **索引的原理**

1. 当没有索引时，查找数据需要进行全表扫描，速度慢。（不知道在哪/不知有几个数据）

2. 建立索引，会形成一个索引的数据结构，比如**排序二叉树** [大的放右边，小的放左边]。

#### **索引的代价**

1. 磁盘占用
2. 会对dml(修改，删除，添加) 速度产生影响（每次修改数据的同时，也要修改索引的数据结构）

- 因为在项目中 select 操作多，所以可以忽略dml造成的影响。

#### 种类

1.主键索引、唯一索引 (Unique)：

​	当字段被设置为主键/唯一时，系统就会为其设置索引，无需再添加。

2.普通索引 (Index)：

​	即该字段不唯一也不是主键，手动添加。

3.全文索引：

​	一般不使用MySQL自带的全文索引。用ES引擎。

#### 语法

**https://www.runoob.com/mysql/mysql-index.html**

**1.添加索引**

```mysql
#1 添加普通索引
create index 索引名 on 表名 (列名);
alter table 表名 add index 索引名 (列名);

#2 添加唯一索引
create unique index 索引名 on 表名 (列名);
alter table 表名 add unique (列名);

#3 添加唯一索引
alter table** 表名 add primary key (列名);
```

**2.删除索引**

```mysql
drop index 索引名 on 表名;
```

**3.查看索引**

```mysql
show index from 表名;

show key from 表名;

desc 表名;
```

desc结果中 key可取的值：

> (1).如果Key是空的, 没有索引
>
> (2).如果Key是PRI,  主键索引
>
> (3).如果Key是UNI,  唯一索引
>
> (4).如果Key是MUL, 普通索引

#### 索引规则

哪些字段**适合**使用索引？

- 较频繁的作为查询条件字段。如 id号

哪些字段**不适合**使用索引？

- 唯一性太差的不合适创建索引。如 性别sex
- 更新非常频繁的字段不适合创建索引，影响增删改的效率。
- 不会出现在where字句中的字段，即不会用于查询的字段，浪费空间。

### MySQL索引原理

索引底层数据结构存在很多种类型，常见的索引结构有: B 树， B+树 和 Hash、红黑树。在 MySQL 中，无论是 Innodb 还是 MyIsam，都使用了 B+树作为索引结构。

#### Hash表、红黑树、B树、B+树

**Hash表**

当数据过更多时，有Hash 冲突问题，一般采用链地址法。如JDK1.8为了减少链表过长的时候搜索时间过长引入了红黑树。
哈希表搜索时间复杂度O(1)，为什么 MySQL 没有使用其作为索引的数据结构呢？
	主要是因为 Hash 索引不支持顺序和范围查询。假如我们要对表中的数据进行排序或者进行范围查询，那 Hash 索引可就不行了。并且，每次 IO 只能取一个。如 SELECT * FROM tb1 WHERE id < 500; 就要找500次。

![img](https://oss.javaguide.cn/github/javaguide/database/mysql20210513092224836.png)

**红黑树**

自平衡二叉排序树，通过在插入和删除时调整二叉树结构，保持二叉树深度平衡，提高查找效率。
	在插入节点时，红黑树会按照二叉排序树的规则，找到插入位置并将新节点插入其中。为了保持红黑树的平衡性，需要进行调整操作。删除结点时也需要调整。
	红黑树会尽可能保持平衡，并且保证从根节点到叶节点的最长路径不大于最短路径的两倍。

下图是二叉排序树：

![二叉排序树_百度百科](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAOAAAADgCAMAAAAt85rTAAAAilBMVEX///8AAAD7+/v39/fz8/Pq6urx8fHk5OTn5+fu7u7Z2dnV1dWqqqp0dHRgYGDe3t6dnZ2UlJSCgoLDw8OWlpajo6PMzMy5ublra2tERER7e3uJiYlhYWG9vb3IyMhZWVlLS0twcHA4ODhSUlKxsbFJSUkuLi4lJSUcHBwODg48PDwhISErKysSEhLLE43nAAAPPklEQVR4nO1daXuiMBAmAeS+T7kF7OG2///vrahAkCCgxNY+vB92FW1gksncGSlqxYoVK1asWLFixYoVK1asWLFixYoVK1aseAh04oRhaHvCTz8IGbDKNjXixJKVrWr99MMsD0bey3T9JsnMv7aKWhpw6Ht7m/zUoxCBmfUuhT77/OcgBc2H/YuO/vwHIYTNHrfjoO0+/UnIgA5L7HXR5LDXXw5eiLzhEWaVbQznvh42QcuKrrpXy4YqPvoTykJUmPolByInBa2WN70feaKFYbXSMvg47kjfbN7H6k880NJw4uZl+A0pIUK25NsPPM/icMTmJTxao9m/9j11+Aub0NGal0yQq29F+57KNMwfvBoQAs1PTRBVhEX/BoGNlmDz3fHfElEO+V9QhHKjC+htevzXiRqqNvmPPNHCiFs1YQBZsoHRvLf+hJoQlU3z2v73+RW0b/+GokdNNYpz+fYN+zdMNUryGex1aMTY668Hw8FedhHefW1stliHV/8rDm8VssBc1O2nPwcZxNKRG4treen6Mk3/BTVPB2YlYrhC6QTRjNyloI3fmy8F980+h3uhvC+Fy0vaLYLqJYt6Tq8Js2gFCRekvhFbsXz8/8KwtKL80IMtg/Lr2lmgGZbp7Dz38Lr7UFTUCbFrZ/+qscMklSctjpNKpB+FCAx/6sp4hTj+pd8GXYmnm2HcQOT79wLuQn78Wy206LWMmuRgjH+pAyF9IYUohNkdUkN9GddXM427nKDsNfYhp0azdh8Cu3iBEGJZlPcbJnL069WFHTyks2Pzd2fttQKXiZ+DsvjFQSgunaf7sNCKX6sQ3cxdwi3Y7Oeq0OeACbKlBIT6y0KJmyoeoRXGYl4d3P4qhbhRraPuSxcdU0+PCtEc/95TIAG5TJcOHZUpx33+EkffB3tzefVshQH4HbYpDwAgEfpzAMCFiokDeqESOrJsm0pY2Sz8AXzmRbDwEkJXOc7b9ylhw8lBYMiybASBTt4IsFLf0qr7QkGL9wFPxbYkcjy3dAqFl3QVgCreKGeGxFURVJoXna288H2u4RWopUnLb0TluRtSQmCic7eJUpL+Bh0WV4KNI1x5LarXtXrellwAjgnNXhYT+iRFnYZx8iVyBZgplh8jclxKb2nMVYkUl4r4oBBDrpDHwWdHy4DI3WCIm06q8lGJ3O+4gIeBD1QiaWEOUbu8hfh9jEIoDR3W9oNwlitMctl9Wkbidn7jw2jgn3L4ard6QsZ/E7f1q7fT3PrA/yrOFwIS6vDQcKiTsZD5aDcCR4ZA/TysphxAlUHU3hKKA2fKJAK70G0rrpzqzum+/YyMeXEx/yTVPxFoVAnh7dkrEwiUfzmtDOVZCvL/kGWTSWgmxm/2+VdFoFKVu/lnxbGJlr9fh+3jDxAiFkZJ4gwSFzUBxPeKQL8SOeHbaS5pAlnvDoEbt/xElAMRAjW/S+CuWkEzJ0Zg2BZECtXiBaD9TCahJ/iWRd8RFj0ZiwwBT1FuVkw4FUCGoNX7MoloNH1FYGge53WfnS5sCAgZutFKlJNbvPvRTuKGTKS2zfmeCGTeHNoFZ17hSPj6bdST/wT5JxJhl8hkLuNGVH6e6AnBoS4UdkhoXhkZVOqIFZmQm324Mn65snYFDyTse3aILQSfUMpLHrLi4x2R+zkDdOjETnGq+CphViXk8h6wFDpkvLMKAlbdsT6pItoYN3NeOuAmLgETJ58Ncud+5aJHoZCSLDODftgL38v9S0shoLz3K/HlvbMlybgabardlOrGLIhVsRtHIQ3NzGlYcqMUMk3RX0TrynnFb2dQTH2JjGFIVcGs03+skkelxLOunubnvg1JhBd2CwFaaR5YHMvF4f5QKUKLTBafzpuJtIIoTf2wnkhoko6nC/JOTVNFv+wPm0iUKxy2jrQnZyqZjMC2l5Ub2sAjvYRX0NTFw/cwvbXPNuqT24pIxdIjGreZwhs4ekUM+sIz6v67/TkkEqi8AeZtUfvJLcZcdlg8ubTFzR6vqWqwicZTZNqzj/vrC1ps9hSP3XiynNlEi92Q306JKdHpk6ta3KUqq+iJZxjDZ3cvchYSbJMdWnwCmByE6GHBtoFVLm6qwydW3yTo/fbgpo96TpJDUb1jm8PQDYq2n0mhsTtqxEcCbNIHH87IArABpV+H+siikKnoEcM7+fqYladKPkH+VAIpP32oYs/7AABMzvjb78dvvz+RQMhIPgCPyFIHgH043TNxgyOFTyRQC6oFeEQ7hUCftYWhnIEFTcTx+7k6AMHMKWVEq3QcuUxEivZmW0O8I1WVgrF8HMJzNfLGjWgHHAU113Mcp7Sk0WjNpgx8Xfbi0jH88D5jT3P8QC89r3RM33jCUSQGWqYfOp7nybayc27PqVUoUs1jrKfu7gjqJpkuXvQv5MqMfGM/2tjF9XNuJPtmNMNKJfR5aPk6zjuOMO9Y5nxwIHwWSdyGnWhC8jYodTZ+zz6Hu3kySurbPSzZA9am39t2g6efdphMh5DO0aPMAbNtmZxgobWIM0Ri/Kq4WK+B3s7IbsrY2Yiz6SPMBIMfOsA9B23gA2OuOlnPDAWAyeW7BkYWVYyb4aEMitKkT7WFYIj4ZxDZzoxCaBtK9WHF5nkvty37QRZaRQRmZyn4qQ6v1CbxmTJEYzSJSUZZNLXNdr2PjAtz9g/CuWhe3wDoRyO6s0HQevQOiDLQ6gduR0Thu3V7vaR2KUpwWZp+/ULYns4J0+8OgVMNtrxhfLGIaZghbHKnUTQC+byrNOUdnDeH9AUuN2V6sX1EWBZR3iHQnVbnw7WlUHKVShQQUSYTCddcEnhJtD8TKBRBTSDVa/7RidvaHQK5aU6v1zA5oyhy2lGgMYnTW0Iju5LPE4GBQjUEKlcBKdhpEdndg8K0s49OW60XgbeoAIijbJFQFOyuXiXrRKDl8y2B5jXP3CJwWmmf3EhbQa1CxSYy5DMI5IsjTQ2BwTWBHRbtEjixtC9uvrbxq0gVjwzikWDRTcOiJwJjsDNDsL0konfXMdPOqbIuge600j62FTJ6tWtdZBCHyAGnJml5IlDTdVsH2cUs6VX02Kgh0yXQm2iHbJsxpazkpLydF9okUvnR2CsWqNerZlG+Jzc0VFR2CIRTy12R5IQO8u/v1rbV7vGcx6HV+R6EwIuks3tcR/tDulicaqqxamvyiDIaj/UINW/YDdoPh77nEA/ttOnGdk80X0CnhApKxWzgAwdnmzh4Niqnx+7ZAXcpJZbejvECPsa2ROFSnOctZTPCjhY2rRwSrBNSsFa8gucYfden0C3muHJQ0fsUSjuCwW4BczqTNgaUEizNK2roJJwn3wX9OtLPymRb/FhmebUsrqEPzqi2s9GN6Ab9kNVN8CJlFSUyPCz9oXOjS0EwfXROmfBm1RVnFM0SiH5azsugQuPI+5yiOvWaxYriTtejdwImflDrJN5OnRGGEfRs64eGGeVba+7ceyeNCbXgkCnHIYpcjastuSXdaJrx0jwNjdA/HMIpFgXnObYzm7qjNMmbDc+Vuu3UrECmvLOFV2khybH1mGynC/drqCAvJ9srzX9Ss7nh4zYlmYMqF0g3Kz2Xgzzs8tFEG/qVy/WSugXp7cZtbIJFULT5lB9qdG8e0CIUFz1BVJ7RSY9Lbwc+bXJVXk/5gT/aH4m5SOSU/cjULoPxdh3zqjVmgF+8PB13E3PUVilJMZLxhD6I3Hbc5eBINUb9IjNsjVM6Tp0S09CPu3RphSyKFE+g5wMKKamaaEz6ZkrDpQ9rW6okEVaCyTZxzGlmueEo2cJ3F7I88hKRpCFjfb5PankLLSf4Bosf9vUBAMUdfs90xADkvj1Bfrj58VkWF3gKACFZO6Y8PvbWmzKFdPhY4SMWAen2jrQJprd895Yn0CTiSkMxzA55noYSJehzqkPFkmK8aJvnW//R9iVMmR7HyaKZQaMp4PQs0sUN3Lh6VFizNBsU/cy0OBpynlkE0t3Cj0l2WZDwNK15ZhYuXJPqRkYrUrTAn6GGYOB7zYRDz58UH8JACJU2Mgkd1Vgy7upsO0YztNTJu4D35Q4/sc7+LstGS+XO33FGsVgVNRfI10KZnhgwgGU/B8rNWf8aTr+Vvjgz/D4M7OEkZZJ2k3ChL3FOJeMZHu6opnv3zz9cjYO1JeEEX+Joa2O1iTS30Qd8w7J1uUiT781A8I+ZcJpnqMpibuuieGAyF2lV0HXbkd/2Go+ZMc0vgNLsCfWUsAOZvCHUHXjheSFhLRJo7K83zkTnVBKL1DExow1s2hy9Dk5o5mqg+ngATZlDVqWduQx81lUV1uM2TYymsmkfIJvKGYmZwV0jLrk4ji3zswkUcbMIPP8s+MY9V03ab7IkXwop+ceN0h2aNnVylEBrJGYGr8rgM2TnOTPEjHYuNUq+zyxw+iWqj/O8M4+7YuhP6MmZ9IGw5UDz3wZSd3o91IWc0xzmUu5Hc8KJQJejIFdb8A/XijFIMRv3UVIogfyILCy7G+QTVYlzKknDRlZeNrEb7OujWeWjpUYsUjuoH9niA2FRYYRFu1Ur+gf6rpwh4Ns6nAuBkpIfLs/hPRq1E1oC3TdR47+RKWNHVrBTQM0cOtulfGQFj/i66Pjy4chyy6Lux0nUfzYXxnr8WqgALkGHmeQZRUJtwWBFIO1VA/nbs5lWPhyZuTqChLLoWJdmGgmvM363ebYzw4xMGovstIJpZef/Owsw+vGQ5FX5F1qwXI6IMIgceZM+OrPBzJJ+zTY5BehkENnq+/k5BhsvToew77wtW6t+PAWJNGXguw2Pk1mKvinjPSkXpgzU2gaUFuiJO3hgnRttByQN/mS5PasUWLzyGmih3nm9Eu07oA1N0oQOUkPGnNX/fYebsAe0nbdITHngtwNu9CNrINhYQui5hycHthqXL5LXoXPc/JV7zMUePGyhpTrbwNrhZlN4qN0Dgljta1Mhm+SJMYHRV1T+HT5Oijn1bC52aKHsnUZwp1YrC8F12SGDrTseHUcJrwQWqw+XT86HGUjtaLRVBNPDrlyOxrN5+3DnWQpXLRHjQAu3y1YhyJHpnWgUpDJUZo0thpFunWjkElkx7o6EiWbkWNXGgFzipPrS9basZfj+TlEi35idmZPKIPIVxY+UoYbIkwBFuR5HJlG9ATcsr/Hs5p6kAM3ynMYLD0v1ahyOfXycFStWrFixYsWKFStWrFixYsWKFStWrFjxavgPy9fNT1+YIZcAAAAASUVORK5CYII=)

下图是红黑树（自平衡二叉排序树）：

<img src="https://img-blog.csdnimg.cn/20200708092215796.png" alt="img" style="zoom:80%;" />

红黑树和平衡二叉树区别：
	红黑树放弃了追求完全平衡，追求大致平衡，在与平衡二叉树的时间复杂度相差不大的情况下，保证每次插入最多只需要三次旋转就能达到平衡，实现起来也更为简单。而平衡二叉树追求绝对平衡，会无限旋转找到最平衡的情况。

**B树**

又称多路平衡查找树。诞生的原因也是解决排序二叉树的平衡问题。

- 就是每个节点不止存储一个数据值，也不止有两个子节点。因此比起平衡二叉树，它能很大程度减低树的高度。
- 当一个结点插入值太多，也会进行调整，会向上传递元素。

![img](https://img-blog.csdnimg.cn/20200708231226215.png)

**B+树**

B树的延申。

- 比起B-树，B+树所有的节点数值都会出现在叶子节点中。
- 所有叶子节点组成了一个增序的链表。



![img](https://oss-emcsprod-public.modb.pro/wechatSpider/modb_20210419_00dc398e-a0c3-11eb-887f-00163e068ecd.png)

图解：什么是B-树、B+树、B*树 https://www.modb.pro/db/53521

#### MySQL的索引的原理

MySQL的两种搜索引擎：MyISAM搜索引擎和InnoDB搜索引擎

##### MyISAM

MyISAM引擎使用B+Tree作为索引结构。叶节点的data域存放的是数据记录的地址，且辅助索引跟主键索引的结构没什么区别。



![图片](https://mmbiz.qpic.cn/mmbiz_png/rguJGkWuUib0JKy44qgCs9xIcias6gIEurgMPEeIw3HIHicDcB5UY7WA0OhkYNpSh2ck8HoMwfoDkpsAFND5po2Sw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

##### InnoDB

InnoDB也使用B+Tree作为索引结构。他的关键字和数据在叶节点上，在一起存储。key为索引值，value为该行记录其他的字段值。

![图片](https://mmbiz.qpic.cn/mmbiz_png/rguJGkWuUib0JKy44qgCs9xIcias6gIEurE6Dlo2AURrCZiaRHIJS7XoicsibJVBAeMxiaQse2JT7vibhlYb6jJBAp0Gw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

InnoDB的辅助索引data域存储相应记录主键的值而不是全部的数据。

![图片](https://mmbiz.qpic.cn/mmbiz_png/rguJGkWuUib0JKy44qgCs9xIcias6gIEurib5MlY172vTbNekj2uBz3zAwkeH43BTiauRMyUKDtAqSvicUWVU1IDcfA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

如果我们要查询名称叫Alice的数据，会先通过辅助索引查询到，这条数据的主键是18。
然后再通过主键索引进行搜索，找到主键是18的叶子节点，并将数据返回。

所以，对于InnoDB搜索引擎，主键索引是非常关键和重要的。

因此，MySQL中InnoDB引擎是要求表必须有一个主键的，没有手动建立主键，MySQL会将选一个不包含null的字段将它当做主键，并建立索引。

**为什么MySQL不使用B-树而选择B+实现索引？**

- B+树的所有节点会在叶子节点中，并组成了一个增序的链表，这对于区间查询来说是非常高效的。比如需要查询年龄在18-20岁的小姑娘。
- I/O磁盘操作是影响计算机性能的一个瓶颈。B+树的所有节点都会出现在叶子节点，非叶子节点仅仅充作索引目录的作用，所以每次I/O操作可以读取更多的节点数量。
- B+树结构的检索性能更具有稳定性。B-最快的时候1次I/O操作就能拿到数据，而B+树每次都需要遍历到叶子节点才能拿到数据。

**为什么MongoDB数据库使用B-树实现索引？**

MongoDB是目前最为流行的NoSQL数据库，是一种文档型数据库，以JSON格式存储数据。

```Json
[{
        "year": 2015,
        "title": "The Big New Movie",
        "info": xxx
 }]
```

首先B-树相比B+树最大的优势是数据查询的最快时间复杂度是O(1)。文档型模式设计一般会将你所需要的数据都相对集中在一起（内存或硬盘），B树查找速度快，减少了磁盘I/O的次数。因此，B树的存储结构更适合于MongoDB文档型数据库的设计理念。

### 全文索引

​	全文索引的原理是将文本数据进行分词，生成单词或短语的列表，并将其存储在索引结构中。在查询时，输入的搜索条件也会进行分词，然后在索引中查找包含这些单词或短语的记录，最后返回匹配的结果。

​	虽然全文索引可以提高查询效率和搜索精度，但是它需要占用较多的磁盘空间，并且在每次更新文本内容时需要进行重建索引，因此需要根据实际情况选择是否使用全文索引。

不使用MySQL作为全文索引理由：

- 分布式架构。ES是分布式的搜索引擎，可以通过将数据存储在不同的节点上来提高查询的性能。而MySQL只能在单个实例上运行。
- 多语言支持。ES支持中文插件，MySQL仅英文从，处理中文会出现问题。
- 高级查询。ES支持丰富的高级查询功能。



## SQL性能优化

**1.只返回必要的字段：不使用 “ SELECT * ”；**

- 增加查询分析器解析成本。
- 增减字段容易与 resultMap 配置不一致。
- 无用字段增加网络消耗，尤其是 text 类型的字段。

**2.只返回必要的行：使用 LIMIT 语句来限制返回的数据；**

**3.缓存重复查询的数据；**

**4.减少服务器端扫描的行数：必要的字段建立索引；**

**5.分解大连接查询：将一个大连接查询分解成对每一个表进行一次单表查询；**

- 让缓存更高效。
- 减少冗余记录的查询。解成多个单表查询，这些单表查询的缓存结果更可能被其它查询使用到。
- 减少锁竞争。

**6.用union all代替union**

**7.使用in关键字，小表驱动大表**

假如有order和user两张表，其中order表有10000条数据，而user表有100条数据。

```mysql
select * from order
where user_id in (select id from user where status=1)
```

如果sql语句中包含了in关键字，则它会优先执行in里面的`子查询语句`，然后再执行in外面的语句。如果in里面的数据量很少，作为条件查询速度更快。而如果exists则会很慢。

- `in` 适用于左边大表，右边小表。
- `exists` 适用于左边小表，右边大表。

**8.批量操作**

如果你有一批数据经过业务处理之后，需要插入数据，该怎么办？

反例：

```js
//在循环中逐条插入数据。
for(Order order: list){
   orderMapper.insert(order):
}
```

```mysql
insert into order(id,code,user_id) 
values(123,'001',100);
```

我们在代码中，每次远程请求数据库，是会消耗一定性能的。而如果我们的代码需要请求多次数据库，才能完成本次业务功能，势必会消耗更多的性能。

正例：

```js
//提供一个批量插入数据的方法。
orderMapper.insertBatch(list):
```

```js
insert into order(id,code,user_id) 
values(123,'001',100),(124,'002',100),(125,'003',101);
```

这样只需要远程请求一次数据库，sql性能会得到提升，数据量越多，提升越大。

**9.高效的分页**

比如现在分页参数变成了：

```js
select id,name,age 
from user limit 1000000,20;
```

mysql会查到1000020条数据，然后丢弃前面的1000000条，只查后面的20条数据，这个是非常浪费资源的。

优化sql：

```js
select id,name,age 
from user where id > 1000000 limit 20;
```

先找到上次分页最大的id，然后利用id上的索引查询。不过该方案，要求id是连续的，并且有序的。

**10.用连接查询代替子查询**

mysql中如果需要从两张以上的表中查询出数据的话，一般有两种实现方式：`子查询` 和 `连接查询`。

子查询的例子如下：

```js
select * from order
where user_id in (select id from user where status=1)
```

子查询语句可以通过`in`关键字实现，一个查询语句的条件落在另一个select语句的查询结果中。程序先运行在嵌套在最内层的语句，再运行外层的语句。

但缺点是mysql执行子查询时，需要创建临时表，查询完毕后，需要再删除这些临时表，有一些额外的性能消耗。

这时可以改成连接查询。 具体例子如下：

```js
select o.* from order o
inner join user u on o.user_id = u.id
where u.status=1
```

**11.缩小范围，再操作**

我们有很多业务场景需要使用`group by`等关键字，它主要的功能是去重和分组。

通常它会跟`having`一起配合使用，表示分组后再根据一定的条件过滤数据。

**反例：**

```js
select user_id,user_name from order
group by user_id
having user_id <= 200;
```

这种写法性能不好，它先把所有的订单根据用户id分组之后，再去过滤用户id大于等于200的用户。

分组是一个相对耗时的操作，为什么我们不先缩小数据的范围之后，再分组呢？

**正例：**

```js
select user_id,user_name from order
where user_id <= 200
group by user_id
```

使用where条件在分组前，就把多余的数据过滤掉了，这样分组时效率就会更高一些。

> 其实这是一种思路，不仅限于group by的优化。我们的sql语句在做一些耗时的操作之前，应尽可能缩小数据范围，这样能提升sql整体的性能。

**12.explain关键字**

MySQL 中的 EXPLAIN 关键字主要用来分析查询语句并生成查询计划。通过 EXPLAIN 关键字，可以查看 MySQL 数据库是如何执行查询语句的，包括哪些表被查询、表之间的连接方式、使用了哪些索引等。

使用 EXPLAIN 关键字可以帮助我们优化查询语句，提高查询性能。

EXPLAIN 关键字只是模拟了查询的执行计划，并不会真正地执行查询语句。

例如：

```js
explain select * from `order` where code='002';
```

结果：

![img](https://ask.qcloudimg.com/http-save/yehe-5604447/061ad534190fcc9a5c5bc83a5413d6d5.png)

## 存储引擎

### InnoDB

是 MySQL 默认的事务型存储引擎，只有在需要它不支持的特性时，才考虑使用其它存储引擎。

默认隔离级别是可重复读（REPEATABLE READ）

### MyISAM

设计简单，数据以紧密格式存储。紧密格式是指数据定长方式存储，即每个记录的长度是相同的，每个字段占用的存储空间是固定的。可以提高查询效率，但有时会浪费存储空间。

不支持事务。

支持压缩表。



## 切分

当一个表的数据不断增多时，Sharding 是必然的选择，它可以将数据分布到集群的不同节点上，从而缓存单个数据库的压力。

###  水平切分

水平切分又称为 Sharding，它是将同一个表中的记录拆分到多个结构相同的表中。

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/63c2909f-0c5f-496f-9fe5-ee9176b31aba.jpg)

### 垂直切分

垂直切分是将一张表按列切分成多个表，通常是按照列的关系密集程度进行切分，也可以利用垂直切分将经常被使用的列和不经常被使用的列切分到不同的表中。

在数据库的层面使用垂直切分将按数据库中表的密集程度部署到不同的库中，例如将原来的电商数据库垂直切分成商品数据库、用户数据库等。

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/e130e5b8-b19a-4f1e-b860-223040525cf6.jpg)



## 复制

### 主从复制

主要涉及三个线程：binlog 线程、I/O 线程和 SQL 线程。

- **binlog 线程** ：负责将主服务器上的数据更改写入二进制日志（Binary log）中。
- **I/O 线程** ：负责从主服务器上读取二进制日志，并写入从服务器的中继日志（Relay log）。
- **SQL 线程** ：负责读取中继日志，解析出主服务器已经执行的数据更改并在从服务器中重放（Replay）。

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/master-slave.png)

### 读写分离

主服务器处理写操作以及实时性要求比较高的读操作，而从服务器处理读操作。

读写分离能提高性能的原因在于：

- 主从服务器负责各自的读和写，极大程度缓解了锁的争用；
- 从服务器可以使用 MyISAM，提升查询性能以及节约系统开销；
- 增加冗余，提高可用性。

读写分离常用代理方式来实现，代理服务器（如MySQL Proxy）接收应用层传来的读写请求，然后决定转发到哪个服务器。

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/master-slave-proxy.png)

主从延迟解决方案：

- sleep 方案：主库更新后，读从库之前先 sleep 一下；
- 强制走主库方案；
- 并行复制。（写数据同时复制到从库）



# Redis