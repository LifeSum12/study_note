### **一、JavaWeb = Servlet + JSP +JDBC**

- Servlet = 服务端小程序（Service Applet）

- JSP = Java服务端页面（HTML + Java ）

- JDBC = 用Java语言去访问数据库的技术



### **二、MVC思想**

Model，这一层主要负责处理业务，包括数据库的交互。

- 如：JDBC

Controller，这一层主要负责接收请求，然后根据不同情况把请求转发给不同的Model层业务组件处理。

- 如：Servlet

View，这一层就专门负责处理视图在后端服务器的渲染。

- 如：JSP



### **三、Spring框架出现**

Spring是一个容器，用来管理对象。

框架基石。



### **四、百花齐放——框架开发时代的来临**

**Struts出现，取代原生的Servlet开发。**

- 产生原因：每一个需求有一个Servlet，Servlet越来越多，管理太混乱。所以参照Spring的方式管理对象方式，搞一个管理Servlet的。在Struts每一个Servlet叫Action。

**Mybatis/Hibernate出现，取代以前原生JDBC开发。**

- Mybatis半自动
- Hibernate全自动

**框架整合SSH**

Spring公司把每个厂家开发框架整合了。

SSH：Structs + Spring + Hibernate



### 五、Spring生态圈的扩展

Spring公司开始提出新的框架。

**SpringMVC出现，取代Struts。**

- 产生原因①：一个大型系统，至少也是百来计数请求。如果用Servlet的一个类映射一个请求这样来开发，未必也太慢，太复杂了。之前Servlet开发的请求处理，是在【类】的维度。现在把对于请求的处理改变到【方法】的维度。

- 产生原因②：每个Servlet需要在web.xml文件配置，SpringMVC觉得太复杂了，他提供了一个注解@RequestMapping。

**新的架构SSM**

SSM架构开始流行了：SpringMVC + Spring + MyBatis



### 六、前后端新时代

前端(浏览器)开始干活。

之前页面都是在后端通过数据进行动态渲染以后发给前端浏览器显示的模式，逐渐演化为，希望后端只是负责处理数据，然后把数据返回给前端，由前端浏览器动态渲染的模式了。

AJax到来。

JSON到来。

ThymeLeaf到来，它写出来的页面，就是xxx.html，不经过后端，就可以直接被浏览器解析。替代JSP。



### 七、Spring生态圈统一时代

**SpringBoot到来。**



### 八、前后端彻底分离的时代

Vue，React，Angular三大JavaScript框架在前端三分天下。



