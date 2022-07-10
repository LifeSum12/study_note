# 一、IOC 

控制反转（Inversion of Control），把创建对象过程交给 Spring 进行管理。

### IOC概念

**原理**

xml 解析、工厂模式、反射。

有一个工厂类，通过解析xml文件获取类路径，路径通过反射得到类的实例。

![image-20220707174511033](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207071751893.png)

**实现**

Spring提供了IOC容器实现的两种方式：（直接通过抽象类，实现上述过程）

- BeanFactory 抽象类

- ApplicationContext 抽象类（开发使用这个）

它们的区别。

```java
//1.加载xml文件
ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
//2.获取配置的创建对象
User user = context.getBean("user", User.class);
```



### IOC操作

即Bean管理，一是Spring 创建对象，二是Spirng 注入属性。

实现管理的两种方式：xml配置文件、注解

#### ①xml方式

1. xml方式来创建对象
2. xml方式来注入属性
   - 通过set方法： property标签
   - 通过有参构造方法： constructor-arg标签

注入不同的数据类型：

- NULL
- 对象（内部bean/外部bean）
- 集合

​		List默认实现类 ArrayList。Set默认实现类 LinkedHashMap。Map默认实现类 LinkedHashSet。



##### Bean管理

1. Spring中有两种类型Bean，普通Bean和工厂Bean (FactoryBean)。

2. Bean作用域

​		单实例 or 多实例

3. Bean的生命周期（从创建到销毁）
4. 自动装配（xml和注解都可以做到，一般用注解方式）
4. 外部属性文件
4. 命名空间




#### ②注解方式

 **简化了xml配置。**

1. 注解方式来创建对象（四种注解）

2. 注解方式来注入属性

3. 完全注解开发：配置类 （替代xml配置文件）



# 二、AOP

### AOP概念原理*

**概念：**面向切面编程(Aspect Oriented Programming)，利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

**原理：**不大理解操作。

### AOP术语

**连接点：**可以被增强的方法

**切入点：**实际中真正被增强的方法

**通知：**

- 前置通知 @before
- 后置通知 @afterReturning
- 环绕通知 @around
- 异常通知 @afterThrowing 【发生异常才会执行】
- 最终通知 @after 【发生异常也会执行，类似finall】

**切面：**把通知应用到切入点的过程



### AOP操作

#### ①xml方式

​	不推荐使用。

#### ②注解方式

1. 设计一个增强的方法，里面编写通知。
2. 通知类型，通过在方法名上标签更改，value值表示该增强方法作用域。
   - **例如：@Before(value = "execution(\* com.atguigu.spring5.aopanno.User.add(..))")****
3. 在增强类上，添加注解@Aspect，表示生成代理对象。
4. xml文件配置开启Aspect标签。



切入点表达式：execution( [权限修饰符] [返回类型] [类全路径] [方法名称] (参数列表) )

- 权限修饰符一般用*代替，返回类型可以省略。



# 三、JdbcTemplate

### 介绍

Spring 框架对 JDBC 进行封装，使用 JdbcTemplate 方便实现对数据库操作。

### 操作

准备工作：引入相关jar包[mysql实现包，druid连接池技术包，spring的jdbc三个包 ]

1. spring 配置文件配置数据库连接池。

2. **配置 JdbcTemplate 对象**，注入 DataSource。

3. 通过 JdbcTemplate 对象方法来进行数据库操作。

   - JdbcTemplate.update (String sql, Object... args)  **增、删、改功能**

   - JdbcTemplate.queryForObject (String sql, Class<T> requiredType)   **查询返回某个值**

     JdbcTemplate.queryForObject(String sql, RowMapper<T> rowMapper, Object... args)  **查询返回对象**

     JdbcTemplate.query(String sql, RowMapper<T> rowMapper, Object... args)  **查询返回集合**

     - 参数需要输入 返回的值类型

   - JdbcTemplate.batchUpdate(String sql, List<Object[]> batchArgs) **批量操作**

     - 该方法内部会遍历循环sql语句，需要传参数数组的列表。
  - 可进行增删改功能。



# 四、事务操作

### 介绍

事务一般添加到 JavaEE三层结构(Web,Service,dao)里面的Service业务逻辑层。

事务管理有两种方式：1.编程式 	**2.声明式（spring提供的）**

声明式事务管理：

1. **基于注解方式（推荐）**
2. 给予xml配置文件方式

**声明式事务管理的底层原理：AOP**

### 操作（注解方式）

1. **在xml文件中配置  事务管理器**。即创建一个事务管理器实现类（Spring提供了一个接口代表事务管理器，这个接口针对不同的框架提供不同的实现类，这里创建的是关于jdbc的实现类）。

   **并在该实现类中注入数据源属性，即datasource 表示为哪个数据库添加事务。**

2. **在** **spring** **配置文件，开启事务注解。**xml文件中要写一行标签，类似于开启aop注解。

3. **在** **service** **类上面（或者** **service** **类里面方法上面）添加事务注解**。@Transactional

​			如果把这个注解添加类上面，这个类里面所有的方法都添加事务。

​			如果把这个注解添加方法上面，为这个方法添加事务.



### **事务的设置**

**在@Transactional() 括号内设置参数。**

①**propagation **传播级别

- **require （默认）**
- require_new
- support

②**ioslation** 隔离级别设置

- 读未提交 read_uncommitted
- 读未提交 read_committed
- **可重复读 repeatable_read （默认）**
- 串行化 serializable 

③ **timeout** 超时时间

​		事务需要在一定时间内进行提交，如果不提交进行回滚。默认-1，秒为单位。

④ **readOnly** 是否只读

​		默认为false，表示可以修改删除操作。

⑤ **rollbackFor** 回滚 

​		设置出现哪些异常进行事务回滚。

⑥ **borollbackFor** 不回滚

​		设置出现哪些异常不进行事务回滚。



# 五、Spring5 新特性

**Spring 5.0 框架的代码基于Java8**

**1. Spring 5.0 框架自带了通用的日志封装:** log4j2

**2. 支持 @Nullable注解**

​	这个注解通常用于消除空指针异常, 提示给代码编译这里可能会出现空值。

**3. Spring5 核心容器支持函数式风格 GenericApplicationContext**

**4.整合 JUnit5 / Junit4  单元测试框架**



## webflux









# 项目优化

利用spring5 (IOC) 创建的项目:

​	创建service，dao和daoImpl包，完成service和daoImpl包里对象创建和注入关系。

​		1）加注解，创建service和daoImpl包里对象。service 注入 dao，在 dao 注入 JdbcTemplate，在 JdbcTemplate 注入 DataSource。【注解格式】

​		2）DataSource创建需要属性有 数据库url/用户名/密码/驱动。通过创建JdbcTemplate对象，把DataSource对象注入JdbcTemplate里。【xml文件配置】









