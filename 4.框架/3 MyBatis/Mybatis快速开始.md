### 快速开始

1. 创建Maven工程。导入Mybatis核心、Junit测试、MySQL驱动。

2. 创建Mybatis核心配置文件： 
   - 配置连接数据库
   - 引入映射文件
3.  创建mapper接口(替代DAO接口)：只需写抽象方法
4.  创建对应的Mapper配置文件（一个mapper接口一个配置文件）
   - 配置文件中namespace值要对应mapper接口位置
   - 配置文件中 sql 语句标签 id 要对应mapper接口方法
   - 通过在insert/update/select/delete标签，手写sql语句



### 深入详解/优化

1. 增加Junit测试功能 
2. 增加log4j日志功能
3. 核心配置文件详解
   - 替换核心配置文件中，数据库信息固定值，新建properties文件
   - 映射文件引入改进：将包下所有的映射文件引入核心配置文件
4. 增删改实现，以及两种查询实现
5. 设置两种配置文件模板



### Test

①获取输入流（通过mybatis-config.xml全局配置文件）

②新建一个sqlSessionFactoryBuilder对象，称sqlSession工厂创建对象。

**新建一个sqlSessionFactory对象**（通过sqlSessionFactoryBuilder对象① 和 输入流②）

**新建一个sqlSession对象** （通过sqlSessionFactory对象的openSession方法，该方法参数可开启自动提交事务）

**新建一个Mapper对象** （通过sqlSession对象的getMapper方法 和 **自己定义Mapper接口**）

- 得到的Mapper对象，是自己定义Mapper接口的实现类。

**最终，通过Mapper对象来调用编写的方法。**



### Mapper配置文件

“增删改查” 语句编写。

sql语句参数获取【实体类、@param】

各种查询结果接收【List，Map】

特殊sql实现。



自定义映射（数据库字段与java实体类属性）

动态sql

缓存



### 逆向工程配置
