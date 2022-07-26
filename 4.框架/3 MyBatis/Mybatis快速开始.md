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



