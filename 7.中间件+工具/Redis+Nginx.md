# Redis

## 简介

![image-20220913145605222](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209131501701.png)

数据库分类：

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209131501735.png" alt="image-20220913145645223" style="zoom: 67%;" />

Redis应用场景：

- **缓存（常用）**
- 任务队列
- 消息队列，如：RabbitMQ
- 分布式锁



## 准备工作

### 下载与安装

官网下载，官方只推荐Linux版本。

安装：

<img src="C:/Users/10275/AppData/Roaming/Typora/typora-user-images/image-20220914180831180.png" alt="image-20220914180831180" style="zoom:67%;" />

- make命令：把解压的redis文件(C语言文件)，进行编译。
- make install命令：安装



### 服务启动和关闭

端口号：6379

打开 redis目录下src文件夹下的 redis-serve 文件，即启动了服务端，启动了redis服务。

打开 redis目录下src文件夹下的 redis-cli文件，即可在客户端对服务端进行操作。

### 登录

- 与mysql不同，redis可以默认无密码登录，但不安全，需要在配置文件require属性 = 密码。

​		设置后，需要输入密码才能操作，使用该命令： `auth 密码`

- redis默认本地不能被远程连接，需要在配置文件注释 bind属性 = 本地地址。

  ​	还需要关闭防火墙对6379端口保护。

​			连接命令： `./redis-cli -h 连接地址 -p 端口号 -a 密码`



## 数据类型&常用命令

redis是通过键值key-value对存储数据的。

key是用字符串来表示，而value有以下5种类型。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209142025396.JPG" alt="捕获" style="zoom: 50%;" />

常用命令可参考Redis中文网站 https://www.redis.net.cn

### 字符串

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209142028814.png" alt="image-20220914202848726" style="zoom:150%;" />

- SETEX 应用场景：手机验证码，过了时间后失效。
- SETNX 应用场景：分布式锁

（nil）代表null。

### 哈希

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209142036200.png" alt="image-20220914203615102" style="zoom: 80%;" />

### 列表

<img src="C:/Users/10275/AppData/Roaming/Typora/typora-user-images/image-20220914204202776.png" alt="image-20220914204202776" style="zoom:67%;" />

应用场景：任务队列

### 无序集合set

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209151403275.png" alt="image-20220915140315160" style="zoom: 80%;" />

### 有序集合 sorted set

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209151404240.png" alt="image-20220915140406105" style="zoom:67%;" />

### 通用命令

![image-20220915140535317](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209151405395.png)



## Java中操作redis

如在java中操作数据库，有jdbc技术。Java中操作redis技术（即redis客户端）官方推荐有三种：

- Jedis
- Lettuce
- Redisson

进阶：**Spring对Redis客户端进行了整合，提供了Spring Data Redis，进一步封装。**



### Jedis

导入依赖

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209151409999.png" alt="image-20220915140937930" style="zoom:80%;" />

具体操作：

jedis方法和redis操作命令几乎相同。

```java
    @Test
    public void test(){
        //1.获取连接
        Jedis jedis = new Jedis();
        //2.执行具体操作
        jedis.set("a","10");
        //3.关闭连接
        jedis.close();
    }
```



### Spring Data Redis

导入依赖

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209151439153.png" alt="image-20220915143952084" style="zoom:67%;" />

操作：

![](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209151443334.png)

yml配置：

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209151447181.png" alt="image-20220915144736080" style="zoom: 67%;" />

- database表示：redis默认初始化15个数据库，默认使用第0个。（配置文件可以改数量）

配置类：

![image-20220915145932854](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209151459999.png)



### Spring Cache

使用注解方式，操作缓存，进一步简化操作。

并且该框架，适用多种缓存的技术，如redis、EhCache等。

![image-20220915155320931](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209151557254.png)

常用注解

![image-20220915155726767](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209151557885.png)



## 实战

使用redis进行改进，对频繁查询的数据。

### 登录验证码

1.把验证码存入redis，设置5分钟后自动销毁。

2.登陆成功后，手动销毁。

### 查询菜品/套餐

1.先从Redis中获取数据，如果有直接返回，无需查询数据库

2.如果没有，则查询数据库，并将查询结果放入Redis。

3.当执行对该数据save、update、delete时，需要加入清理redis缓存操作。





## 持久化

RDB 快照，存数据到硬盘上。

AOF 日志，存sql命令到硬盘上













# _______________________________________________________________________________________________________

# Nginx

## 简介

![image-20220915195433583](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209151954714.png)





主动找中介买东西

![image-20220915200847853](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209152008977.png)

被动，中介直接售卖

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209152010172.png" alt="image-20220915201040077" style="zoom:80%;" />



**负载均衡**

![image-20220915202648738](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209152026858.png)









# 前后端分离

## 简介

![image-20220915204344597](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209152043748.png)



**开发流程：**

![image-20220915203743523](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209152037653.png)



**前端技术栈：**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209152039897.png" alt="image-20220915203953796" style="zoom:50%;" />

- nodejs是基石，相当于java项目要基于jdk。
- mock模拟数据，测试用
- webpack打包用



## YApi

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209152045066.png" alt="image-20220915204535939" style="zoom: 50%;" />



## Swagger

![image-20220915204709787](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209152047877.png)

Swagger 有以下 3 个重要的作用：

1. **将项目中所有的接口展现在页面上**，这样后端程序员就不需要专门为前端使用者编写专门的接口文档；
2. 当接口更新之后，只需要修改代码中的 Swagger 描述就可以实时生成新的接口文档了，从而**规避了接口文档老旧不能使用的问题**；
3. 通过 Swagger 页面，我们可以**直接进行接口调用，降低了项目开发阶段的调试成本**。









## 部署

前端资源部署到Nginx

后端资源部署到tomcat

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209152053457.png" alt="image-20220915205302366" style="zoom:67%;" />
