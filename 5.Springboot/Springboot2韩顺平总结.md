# 序章：手写SpringBoot框架

## Tomcat

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202308152249143.png" alt="image-20230326195425104" style="zoom:80%;" />
Tomcat要实现的功能：

**一、初始化servlet实例，存入容器（也称servlet容器）**

​	通过读取xml配置文件信息。

​	通过注解方式。

**二、开启服务器8080端口监听。**

**三、接收到HTTP请求。**

​	**0.网络接收到一个Socket对象。**

​	**1.为每个请求开启一个线程。**

​	**2.创建HttpRequest对象**
​	  通过Socket对象，获取输入流InputStream对象。通过输入流InputStream对象，获取到Http请求信息，并把http请求信息封装并解析成HttpRequest对象。
​	  HttpRequest对象：分割出请求方法，请求uri，请求参数【get是uri后面，post是请求体中】。

​	**3.创建HttpResponse对象**
​	 通过Socket对象，获取输出流OutputStream对象。 通过输出流OutputStream对象，并把OutputStream对象和http响应行信息封装到HttpResponse对象。
​	  HttpResponse对象：响应行，输出流对象。

​	**4.判断uri访问的是静态资源还是servlet**

​	用字符串后缀方法判断如果是xxx.html，则找到对应的资源文件，用File方法读取到String中。把String传给输出流OutputStream对象，从而返回给浏览器。

​	**5.分发给对应servlet实例处理**
​	通过HttpRequest对象，获取请求uri，在Tomcat里的Servlet容器找到对应的Servlet对象，进行处理（需要传入HttpRequest，HttpResponse对象）。servlet对象会自己返回响应的页面or数据等。

​	若是xml配置servlet方式：在配置文件找到uri对应的servletName，通过servletName找到全类名。在tomcat容器中获取到该servlet对象实例。

**用到的设计模式**

**模板模式**：定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

## Spring

### IOC

#### IOC容器类实现

ConcurrentHashMap类型成员变量singletonObjects，用来存放单例对象。

**1.解析beans.xml文件（Dom4j技术）**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
<bean id="person" class="com.example.Person">
    <property name="name" value="Alice" />
    <property name="age" value="25" />
</bean>

</beans>
```

**2.得到bean的信息id，class，属性和属性值。**

**3.使用反射生成实例对象，并赋值。**

> Spring的IOC容器默认使用的是"无参构造函数"和"set方法"进行依赖注入的方式.

**4.将创建好的bean对象，放入singletonObjects 集合中。**

**5.容器类提供getBean(id)，可以返回对应bean对象。**

#### IOC容器类信息

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202304051728055.png" alt="image-20230405172825167" style="zoom:80%;" />

beanDefinitionMap属性保存每个bean的定义信息。

singletonObjects属性保存所有已经创建的单例bean对象。

**IOC容器两个比较重要的属性图解：**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202304051735850.png" alt="image-20230405173548735" style="zoom:88%;" />



#### IOC容器类实现（注解方式）

![image-20230405221833010](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20230405221833010.png)

**1.书写ComponentScan注解类**

​	内含一个value值，用来表示扫描哪些包。

**2.书写Config配置类，用@ComponentScan注解修饰**

**3.书写IOC容器类（完成包的扫描 + 对象创建）**

​	获取Config配置类的Class对象，进而通过Class对象获取注解的value信息。

​	得到value信息，需要扫描的包。`com.myspring.component`

​		通过类加载器获取绝对路径+字符转换，最终得到value的绝对路径。`E:/102758/porject/target/com/myspring/component`

​		根据绝对路径，通过IO流操作，遍历路径下的子包/子文件。

​		通过字符转换，得到包/子包下的所有类的全类名。`com.myspring.component.User`

​		通过全类名，用反射来创建Class对象，再通过Class对象的isAnnotationPresent方法判断是否包含@Component注解。如果有则会通过反射创建该对象，并放入容器中。

​		解析@Component注解对象的id值，该值作为key，实例对象作为value存入容器中。

**用到的设计模式有：工厂设计模式，单例设计模式****

​		

### AOP

#### 动态代理

="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202304061818176.png" alt="image-20230406181810051" style="zoom:67%;" />

="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202304061818767.png" alt="image-20230406181833676" style="zoom:60%;" /> 

![JDK动态代理之实现与原理(一)【JDK篇】 - 掘金](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/10/17338852f71b27bc~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.image)

定义接口：

```java
public interface UserService {
    //增加用户
    void addUser();
}
```

实现类：

```java
public class UserServiceImpl implements UserService {
    @Override
    public void addUser() {
        System.out.println("------增加一个用户------");
    }
}
```

拦截处理器：

​	需传入实现类（被代理对象）。

```java
public class UserInvocationHandler implements InvocationHandler {

    /**
     *  真实对象 即被代理对象
     */
    private Object realSubject;


    public UserInvocationHandler(Object realSubject){
        this.realSubject = realSubject;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("------插入前置通知代码------");
        // 执行被代理对象的目标方法
        Object result = method.invoke(realSubject, args);
        System.out.println("------插入后置处理代码------");
        return result;
    }
}
```

Main方法：

```java
public class JDKProxyTest {

    public static void main(String[] args) {
        // 创建真实对象 即被代理对象
        UserService userService = new UserServiceImpl();
        // 创建拦截处理器
        UserInvocationHandler handler = new UserInvocationHandler(userService);
        // 创建jdk动态代理
        UserService jdkProxy = (UserService) Proxy.newProxyInstance(UserService.class.getClassLoader(),
                new Class[]{UserService.class}, handler);
        // 使用调用动态代理 调用方法
        jdkProxy.addUser();
    }
}
```

Proxy.newProxyInstance是创建一个代理类，这个代理类也会实现被代理对象实现的所有的接口，需要传入handler是实际调用接口方法时真正调用的方法。

jdkProxy.addUser() 实际会把Proxy代理类、addUser方法和参数传入handler类的invoke方法中，调用invoke方法。

### Spring整体

#### 1.容器创建和初始化

单例/多例

**属性：Spring容器包含 beanDefinition Map集合和 singletonObject Map集合。**

> beanDefinition<String, BeanDefinition>, String为bean的ID，BeanDefinition类包含bean的信息，即Class对象，scope值。
>
> singletonObject<String, Object>, String为bean的ID，存储所有单例对象、

**初始化：Spring会扫描所有的需要实例化的Bean类，将其放入 beanDefinition 集合中。**

> 再判断如果是scope=单例，则利用Class对象反射创建对象`CreateBean方法`放入单例池singletonObject集合中。
>
> 【如果是懒加载(默认)，会在第一次getBean时才创建对象加入单例池。优点是可以减少系统的启动时间】

**执行时获取：getBean()，先到beanDefinition 集合获取相关信息，没有则抛出异常。**

> 再在beanDefinition 集合中得到scope值。
>
> 如果scope值=单例，直接从单例池获取对象。
>
> 如果scope值=多例，则每次都会利用Class对象反射`CreateBean方法`创建一个新的bean对象，并返回。



#### 2.依赖注入

@Autowired

**利用Class对象反射创建对象时：通过Class对象拿到所有Field字段。**

>  判断该字段是否有Autowired.class注解，若有则得到该字段名称/类型。
>
> 利用字段名称/类型，通过getBean得到对象，并用反射set赋值。



#### 3.Bean后置处理器

**后置处理器也会放入单例池。**

>  实现了接口*BeanPostProcessor* 会被底层认为是后置处理器。

> 当创建对象时候就会从单例池取出调用它的before和after方法

**在创建Bean实例对象`CreateBean方法` 分别执行创建bean对象、before、init初始化方法，after方法。**

> **postProcessBeforeInitialization** 方法需传入bean实例，可能是原来的 bean, 也可能被改变了。
>
> **afterPropertiesSet()** 这个是初始化方法，不同于后置器。如果bean实现了*InitializingBean*接口，则会在这调用该方法。
>
> **postProcessBeforeInitialization** 方法需传入bean实例，可能是原来的 bean, 也可能被改变了。

若存在多个处理器，则按序执行。

**思想：**Bean后置处理器默认对所有的bean都进行拦截处理。但还可以对特定类型的bean进行额外的处理，只需要在befor/after方法内加入类型判断后进行额外操作。



#### 4.AOP

**与后置处理器关联：**若一个类有被增强（即被代理），则在执行完后置处理器的after方法后，返回的bean对象会变成Proxy代理对象。

**创建Proxy代理对象：**与jdk动态代理一致。

**如何判断一个类有被增强？**

> 在初始化时，若类存在@Aspect注解则为代理类，则除了实例化还会在Map保存好代理关系。
>
> getBean方法执行时，即当一个类要被实例化时，在后置处理器的after方法时会查看代理关系Map，检测是否要被代理。
>
> 如果是则在后置处理器的after方法时，创建代理对象。在Handler中，会放入相关的@Before方法, @After方法等。	



## MVC模块

**Tomcat与SpringMVC是如何协同工作的？**

在Tomcat中，当有一个HTTP请求到达时，Tomcat会根据请求URL找到对应的Servlet。在没有SpringMVC的情况下，Servlet会直接处理这个请求，由servlet返回相应的结果。但是，在使用SpringMVC框架时，Tomcat会将请求转发到DispatcherServlet这个Servlet上。

DispatcherServlet是SpringMVC框架的核心控制器，它负责将请求分发给相应的Controller进行处理，并将处理结果返回给客户端。在Tomcat中，配置DispatcherServlet需要在web.xml文件中进行。在web.xml中，需要为DispatcherServlet配置一个URL映射，用于匹配客户端请求的URL。

**SpringMVC相对于Tomcat处理servlet有什么优势？**

简洁开发代码、同时提供了拦截器（Interceptor）和过滤器（Filter）等机制。





# 一、基础入门

## 快速入门

需求：

​	构建一个 SpringBoot 项目，浏览器发送/hello 请求 http://localhost:8080/hello，页面响应  Hello,Springboot



1. 创建maven工程，在pom.xml引入父工程和依赖。（ 在idea右侧maven按键，点击刷新一下下面的依赖才会导入进来。）

```xml
<!-- 导入 springboot 父工程，规定的写法 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.5.3</version>
</parent>
<!-- 导入 web 项目场景启动器,会自动导入和 web 开发相关依赖,非常方便 -->
<dependencies>
    <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

2. 创建主程序 MainApp 类，注解@SpringBootApplication标识是整个Springboot项目的启动程序。

```java
@SpringBootApplication
public class MainApp {
    public static void main(String[] args) {
        //启动 SpringBoot 应用程序
        SpringApplication.run(MainApp.class, args);
	}
}
```

3. 编写业务Controller类

```java
@Controller
public class HelloController {
    @RequestMapping("/hello")
    @ResponseBody
    public String hello(){
        return "hello, spring boot";
    }
}
```

点击运行MainApplication类的mian方法即可启动。

application.properties 文件可配置其他信息。



## 依赖管理

**在pom.xml文件中：**

**一、spring-boot-starter-parent  父项目，声明了开发中常用的依赖的版本号。**

> 即如果程序员没有指定某个依赖 jar 的版本，则以父项目指定的版本为准。

若需要修改指定的版本：在pom.xml文件中添加<dependency>标签来指定。

下面是修改mysql版本的代码：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>quickStart</artifactId>
    <version>1.0-SNAPSHOT</version>
    
    <!-- 方法1：直接指定mysql.version即可 -->
    <properties>
        <mysql.version>5.1.49</mysql.version>
    </properties>
    
    <!-- 导入 springboot 父工程，规定的写法 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.3</version>
    </parent>
    <!-- 导入 web 项目场景启动器,会自动导入和 web 开发相关依赖,非常方便 -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- 方法2：根据依赖就近优先原则，以自己指定的为准 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
             <version>5.1.49<version>
        </dependency>
    </dependencies>

</project>
```



**二、spring-boot-starter-web 依赖，导入指定版本的所有jar包，并且导入这些包的依赖包。**

> 在Springboot中，有自动配置机制。无需手动配置 Tomcat 、SpringMVC、配置如何扫描包、字符过滤器、视图解析器、文件上传等。
>
> spring-boot-starter-web的pom工程都有列出来。



## **配置文件**application.properties

**整个springboot项目结构如下。**

![image-20230325141940345](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202303251419977.png)

**SpringBoot 在哪配置读取 application.properites ？**

SpringBoot 所 有 的 自 动 配 置 功 能 都 在 spring-boot-autoconfigure 包 里 面，一 般 是 XxxAutoConfiguration.java, 对 应XxxxProperties.java

![image-20230325142554392](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202303251425481.png)

**例子，如配置文件为：**

```properties
#修改上传文件大小，默认1MB
spring.servlet.multipart.max-file-size=10MB
```

​	修改了上传文件大小，springboot项目读取到配置文件后，会调用MultipartProperties类实例的Set方法，把maxFileSize属性改为10Mb，然后再通过MultipartProperties类实例去创建MultipartAutoConfiguration配置类。

部分源码：

```java
public class MultipartProperties {
	//属性
    private DataSize maxFileSize = DataSize.ofMegabytes(1L);
    //set方法
    public void setMaxFileSize(DataSize maxFileSize) {
        this.maxFileSize = maxFileSize;
    }
}


public class MultipartAutoConfiguration {
    //配置类其中一个属性是properties类
    private final MultipartProperties multipartProperties;
	//通过properties类初始化。
    public MultipartAutoConfiguration(MultipartProperties multipartProperties) {
        this.multipartProperties = multipartProperties;
    }
}
```





## Spring IOC知识回顾（配置类诞生原因）

通过bean.xml创建实例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="monster03" class="com.hspedu.springboot.bean.Monster">
        <property name="name" value="牛魔王~"></property>
        <property name="age" value="5000"></property>
        <property name="skill" value="芭蕉扇~"></property>
        <property name="id" value="1000"></property>
    </bean>
</beans>
```



**@Component、@Controller、 @Service、@Repository这些注解有何不同？**

答：@Component 是 Spring 中最普遍的注解，它表示一个普通的 Spring 管理的组件类。当一个类不属于以下三个类型（@Controller、@Service、@Repository）时，可以使用 @Component 注解进行标注，以表明该类将被 Spring 所管理。

1. @Controller 用于标注控制器类。@Controller 类可以处理 HTTP 请求并返回 HTTP 响应。
2. @Service 注解用于标注业务逻辑层的类，主要用于注解服务层。它通常是一些复杂的逻辑，需要多个 DAO 协同工作的情况。
3. @Repository 注解用于标注数据访问层（DAO）的类，表示该类用于数据库访问。通常情况下，这些类中的方法与数据库操作相关，例如插入、更新、删除、查询等。

**其他注解：**

@Autowired：根据属性类型自动装配，在IOC容器里找所需的类型并赋予。

@Qualifier：根据属性名称自动注入

```java
@Service
public class UserService {
 //定义 dao 类型属性
 //不需要添加 set 方法
 //添加注入属性注解
 @Autowired 
 private UserDao userDao;
 public void add() {
     System.out.println("service add.......");
     userDao.add();
 }
}
```

@Resource：可以用来指定名称或 ID 来装配 Bean。

@Value：将属性值注入到 Spring 管理的 Bean 中。【底层采用的SetXxx()方法注入】

```java
public class Xxx{
    @Value("hello world")
	private String message;
}
```



- 用注解创建对象，默认的实例名是 类名称，首字母小写。不同于bean.xml文件配置，需要指定实例名。

- 在spring中使用注解创建实例，还需要在配置文件中 开启扫描组件 和 标注扫描的路径。





## 配置类的有关注解

### **@Configuration 配置类**

@Configuration 标识这是一个配置类 等价 配置文件(bean.xml)

​	应用：配置类里面使用@Bean标注在方法上给容器注册组件，即创建bean对象

**既然可以用@Component来创建实例类，为什么还需@Configuration类中使用@Bean注解来定义Bean对象？**

答：在一般情况下，如果我们只是需要定义一些简单的Bean对象，那么@Component注解已经足够了。但是，当我们需要定义一些复杂的Bean对象，或者需要对Bean对象进行配置和管理时，我们需要使用@Configuration类来配置Bean对象，并使用@Bean注解定义每个Bean对象。

下面例子：

```java
@Configuration
public class BeanConfig {
    @Bean
    public User user01(){
        return new User("林立",18,"踢球");
    }
}


public class User {

    private String name;
    private int age;
    private String hobby;
}
```

BeanConfig实例、User的实例(user01)都会被注入到IOC容器中。

#### 使用细节

一、SpringBoot2 新增特性： proxyBeanMethods 指定 Full 模式 和 Lite 模式

```java
/**
* 1. proxyBeanMethods：代理 bean 的方法
* (1) Full(proxyBeanMethods = true)、【保证每个@Bean 方法被调用多少次返回的组件都是
单实例的, 是代理方式】
* (2) Lite(proxyBeanMethods = false)【每个@Bean 方法被调用多少次返回的组件都是新创
建的, 是非代理方式】
* (3) 特别说明: proxyBeanMethods 是在 调用@Bean 方法 才生效，因此，需要先获取
BeanConfig 组件，再调用方法
* 而不是直接通过 SpringBoot 主程序得到的容器来获取 bean, 注意观察直接通过
ioc.getBean() 获取 Bean, proxyBeanMethods 值并没有生效
* (4) 如何选择: 组件依赖必须使用 Full 模式默认。如果不需要组件依赖使用 Lite 模
* (5) Lite 模 也称为轻量级模式，因为不检测依赖关系，运行速度快
*/
//@Configuration 标识这是一个配置类: 等价 配置文件
@Configuration(proxyBeanMethods = false)
public class BeanConfig {
    @Bean
    public Monster monster01() {
   		return new Monster(100, "牛魔王", 500, "芭蕉扇");
    }
}
```



### **@Import 导入组件**

把类实例直接导入到@Configuration 注解类上。

```java
//给容器中自动创建出这两个类型的实例到IOC、默认实例的名字就是全类名
@Import({Dog.class, Cat.class})
@Configuration
public class BeanConfig {
}

public class Dog {
}
public class Cat {
}
```



### **@Conditional 条件装配**

​	满足Conditional指定的条件，则进行实例化注入IOC。

@Conditional 是元注解，类似一个接口。它的实现注解有以下这些：

![image-20230325175427844](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202303251754998.png)

举例：

```java
@Configuration
public class BeanConfig {
    //有tom实例名的Bean对象时，Monster类的才能被实例化。
    @ConditionalOnBean(name = "tom")
    @Bean
    public Monster monster01() {
   		return new Monster(100, "牛魔王", 500, "芭蕉扇");
    }
}
```
注意，@ConditionalOnXxx不具有@Bean的功能，需要另外加。



### **@ImportResource **

​	把以前bean.xml文件配置的bean，直接引入到配置类中， 兼容spring。

```java
@ImportResource({"classpath:beans.xml","classpath:beans2.xml"})
@Configuration
public class MyConfig {
}
```

成功导入后，运行时在IOC容器中即可找到bean.xml编写的实例。



### **@ConfigurationProperties 配置绑定  **

 **SpringBoot 读取到核心配置文件 application.properties 的内容后，是如何把它封装到 JavaBean 中 ？**

答：靠注解@ConfigurationProperties 



例子：

```properties
##这是配置文件application.properties 
mycar.brand=BYD
mycar.price=100000
```

```java
@Component
@ConfigurationProperties(prefix = "mycar")
public class Car {
 private String brand;
 private double price;
 //...
}
```

流程：先通过@Component把Car实例放入IOC容器，再通过@ConfigurationProperties注解去配置文件中找到相应前缀的属性值，并注入到该Car实例中。



自定义配置端口server.port 或 上传文件大小 spring.servlet.multipart.max-file-size，都是通过这种方式。如：在MultipartProperties类中，添加了注解@ConfigurationProperties 

```java
@ConfigurationProperties(
    prefix = "spring.servlet.multipart",
    ignoreUnknownFields = false
)
public class MultipartProperties {
    
    private DataSize maxFileSize = DataSize.ofMegabytes(1L);
    //...
}
```



了解即可

@EnableConfigurationProperties 注解

```java
@EnableConfigurationProperties(Xxx.calss)
@Configuration
public class BeanConfig {
    
}
```

@EnableConfigurationProperties(Furn.class) 作用

1、开启Furn 配置绑定功能

2、把Furn 组件自动注册到容器中

简单来讲，就是在Furn类上不需要加@Component，@EnableConfigurationProperties有自动注册到容器中的功能。



## 底层原理

### @Configuration + @Bean 机制

与Spring的IOC容器底层类似。

**（IOC）加载配置文件：**在 Spring 容器初始化的过程中，利用 I/O 操作读取并解析配置文件（ XML 配置文件）。将这些Bean定义存储在一个集合中。

**（@Configuration+@Bean）扫描文件：**在规定的路径中，扫描被@Configuration注解标识的类。

**创建 Bean 实例：** 把读取到的bean信息，利用反射机制，实例化bean对象。

**依赖注入：**继续利用反射机制，把bean配置的属性值，注入bean对象。

**注册 Bean 实例：**在 Bean 的创 建、属性赋值、初始化和依赖注入等步骤完成后，Spring 会将这些 Bean 实例注册到 Bean 容器中，供其他 Bean 或应用程序使用。该容器是一个Map集合，它的键是Bean的名称，值是对应的Bean实例。

技术：IO/文件扫描 + 注解 + 反射 + 集合 + 映射(Map)



### 程序启动SpringApplication

**SpringBoot主程序启动，底层代码做了什么？ 分析在哪里创建Tomcat、IOC容器。**

主程序

```java
@SpringBootApplication
public class MainApp {
    public static void main(String[] args) {
       SpringApplication.run(MainApp.class, args);
    }
}
```

【 SpringApplication类】

进入run方法：

```java
public ConfigurableApplicationContext run(String... args) {
   //...代码省略 
   
   //IOC容器的创建 
   context = this.createApplicationContext();
   //刷新容器：初始化     
   this.refreshContext(context);       
}
```

【 SpringApplication类】

一、容器创建createApplicationContext() 

```java
protected ConfigurableApplicationContext createApplicationContext() {
    return this.applicationContextFactory.create(this.webApplicationType);
}
```

【applicationContextFactory类】

工厂类的create方法，最终返回一个SERVLET类型的容器。

```java
public interface ApplicationContextFactory {
    //根据webApplicationType的类型，选择创建哪种的IOC容器。Web开发默认是SERVLET，可以在配置文件修改类型。
	switch(webApplicationType) {
            case SERVLET:
                return new AnnotationConfigServletWebServerApplicationContext();
            case REACTIVE:
                return new AnnotationConfigReactiveWebServerApplicationContext();
            default:
                return new AnnotationConfigApplicationContext();
            }                       
}
```

容器创建完成后，定义的bean（实例对象）几乎都没创建，在刷新容器时才会创建。



二、刷新容器（初始化默认设置，注入相关Bean到容器，启动tomcat）

【 SpringApplication类】

```java
protected void refresh(ConfigurableApplicationContext applicationContext) {
    applicationContext.refresh();
}
```

进入applicationContext.refresh();方法

【ServletWebServerApplicationContext类】

```java
public final void refresh() throws BeansException, IllegalStateException {
    try {
        //重点方法
        super.refresh();
    } catch (RuntimeException var3) {
        WebServer webServer = this.webServer;
        if (webServer != null) {
            webServer.stop();
        }

        throw var3;
    }
}
```

进入super.refresh();后

【AbstractApplicationContext类】 

```java
public void refresh() throws BeansException, IllegalStateException {
   synchronized (this.startupShutdownMonitor) {
       
         // Initialize other special beans in specific context subclasses.
       	 // 重点方法
         onRefresh();

         // Last step: publish corresponding event.
         finishRefresh();
   }  
}
```

进入 onRefresh();后

【ServletWebServerApplicationContext类】

```java
protected void onRefresh() {
    super.onRefresh();
    try {
        //创建Web容器（Tomcat）
        this.createWebServer();
    } catch (Throwable var2) {
        throw new ApplicationContextException("Unable to start web server", var2);
    }
}
```

进入createWebServer()后

【ServletWebServerApplicationContext类】

```java
private void createWebServer() {
    //创建工厂
    ServletWebServerFactory factory = this.getWebServerFactory();       
    //重点：用工厂创建Web容器
    this.webServer = factory.getWebServer(new ServletContextInitializer[]{this.getSelfInitializer()});
       
}
```

进入factory.getWebServer()方法后

【TomcatServletWebServerFactory类】

```java
public WebServer getWebServer(ServletContextInitializer... initializers) {
	//重点：真正创建了Tomcat容器
    Tomcat tomcat = new Tomcat();
    //对Tomcat设置一些属性
    tomcat.setBaseDir(baseDir.getAbsolutePath());
    Connector connector = new Connector(this.protocol);
    connector.setThrowOnFailure(true);
    tomcat.getService().addConnector(connector);
    tomcat.setConnector(connector);
    tomcat.getHost().setAutoDeploy(false);
  	//返回Tomcat容器
    return this.getTomcatWebServer(tomcat);
}
```

End

**总结：SpringBoot主程序启动后，Springboot会创建IOC容器，并注入了相关的Bean。创建Tomcat，并启动了tomcat。**

- 通常情况下，在Spring Boot应用程序中只会有一个IoC容器。但在特定情况下，可能会使用多个IoC容器来管理bean对象。

- IOC容器与tomcat容器，它们是两个独立的容器，各自负责不同的任务。

### Tomcat底层实现

主要功能：在8080监听用户http请求，并按规定响应http请求（返回静态资源 or 调用相关servlet业务）

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202308152259365.png" alt="image-20230326195425104" style="zoom:80%;" />

**【Socket网络编程】**

1.创建SocketServer监听HTTP请求 

​	Tomcat通过在服务器上创建SocketServer来监听HTTP请求。SocketServer在服务器上绑定一个端口，并等待客户端请求。

**【Http协议】**

2.接收HTTP请求并解析

​	当有客户端连接到服务器时，SocketServer会接收到一个HTTP请求。Tomcat需要解析这个请求，获取请求的方法、URL、请求头、请求参数等信息。

**【内嵌一个Servlet容器】**

3.根据URL映射到对应的Servlet

​	Tomcat需要根据URL的路径来匹配到对应的Servlet，以便处理请求。Tomcat通过一个叫做Servlet容器的组件来管理Servlet。Servlet容器会根据URL的路径匹配到对应的Servlet，然后创建一个HttpServletRequest对象和HttpServletResponse对象，并将它们传递给Servlet的service()方法处理请求。

4.Servlet处理请求并生成响应

​	Servlet会根据请求的信息和业务逻辑，生成相应的响应数据。通常情况下，Servlet会生成一个HTML页面或者一个JSON数据。

**【IO流】**

5.将响应发送给客户端

​	Tomcat需要将响应数据发送回客户端。它会将响应数据写入HttpServletResponse对象的输出流中，并通过SocketServer将响应数据发送给客户端。

**【多线程】**

每个客户端访问都创建一个线程。

**【xml解析】**

解析web.xml文件：配置Servlet、Filter、Listener和其他的Web应用程序配置信息



### 回顾SpringMVC流程

![image-20230326211920771](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202303262119909.png)





在 SpringMVC 中，所有的请求都会被前端控制器 DispatcherServlet 接收并处理。

**DispatcherServlet 作用** 调用处理请求和返回响应，总指导。它持有Spring IOC容器，可以进行调用分发。

**HandlerMapping作用** 找到对应的 Controller 类，并返回一个 HandlerExecutionChain 对象，其中包含了将要调用的 Controller 对象以及该 Controller 对象对应的拦截器（如果有的话）。然后，SpringMVC 会根据请求的类型（GET、POST、PUT 等）选择合适的 HandlerAdapter 来处理请求。

**HandlerAdapter 作用** 是将请求转换成一个 ModelAndView 对象，其中包含了请求的处理结果以及该结果对应的视图名称。

**Handler作用** 最后将该对象交给对应的 Handler 处理业务逻辑，最后将处理结果返回给客户端。



### SpringBoot框架手动实现*

实现Tomcat容器创建并启动、Spring IOC容器的创建注入、两个容器的关联。

**主程序**

```java
public class HspMainApp {
    public static void main(String[] args) {
        //启动Springboot程序
        HspSpringApplication.run();
    }
}
```

**模拟实现HspSpringApplication，即Tomcat创建启动**

```java
public class HspSpringApplication {
    //创建和启动Tomcat，并关联Spring容器
    public static void run(){
        try {
            //1.创建Tomcat实例
            Tomcat tomcat = new Tomcat();

            //2.tomcat关联Webapp容器。
            //  第一个参数contextPath表示访问路径必须为Localhost:9090/hspboot/...
            //  第二个参数docBase是把项目路径地址给到tomcat。
            tomcat.addWebapp("/hspboot","E:\\10275\\file\\maven-workspace\\springboot\\quickStart");

            //3.设置端口号
            tomcat.setPort(9090);
            //4.启动Tomcat
            tomcat.start();
            //5.用于阻塞当前线程，以便让 Tomcat 服务器持续监听客户端请求。
            tomcat.getServer().await();

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

**模拟实现spring容器**

```java
//Spring容器
public class HspWebApplicationInitializer implements WebApplicationInitializer {
    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {
        //1.加载spring web application configuration == 容器
        AnnotationConfigWebApplicationContext ac = new AnnotationConfigWebApplicationContext();
        //  在容器中注册Xxx配置类
        ac.register(HspConfig.class);
        //  完成bean的创建和配置
        ac.refresh();

        //2.创建前端控制器DispatcherServlet
        //  并让DispatcherServlet持有容器(这样前端控制器才可以调度相应的servlet)
        DispatcherServlet dispatcherServlet = new DispatcherServlet(ac);

        //3. 【关联】向web应用上下文加入前端控制器。（这样tomcat可以调用前端控制器了，session域、application域都是由tomcat管理）
        //   返回注册器
        ServletRegistration.Dynamic registration = servletContext.addServlet("app", dispatcherServlet);
        //  当tomcat启动时，自动加载dispatcherServlet
        registration.setLoadOnStartup(1);
        //  dispatcherServlet拦截请求（只有哪些路径才会进行处理，“/”表示任何路径都可以）
        registration.addMapping("/");

    }
}
```

- 虽然在run方法中没有调用onStartup方法来初始化spring容器。但`Tomcat` 作为 Servlet 容器会在启动时加载 `WebApplicationInitializer` 接口的实现类，并调用其中的 `onStartup` 方法。

**总结：Servlet容器（Tomcat）与Spring容器的关联，是通过向Servlet容器中application域（别名servletContext）添加了dispatcherServlet。**





## 小技巧 

#### Lombok简化开发

导入相关lombok依赖即可。

1.简化pojo实体类代码，以后只需写属性，注解会帮忙写上、构造器、toString方法、hashcode方法、equals方法。

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@ToString
@EqualsAndHashCode
public class User {
    private String userName;
    private String password;
    private Integer age;
}
```

- equals方法是判断各个属性值是否相同。
- hascode方法是根据各个属性值返回哈希码。

Lombok常用注解：

![image-20230327170908573](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202303271709227.png)



lombok扩展内容，需要按照IDEA的lombok插件。

2.简化日志开发，使用log.info("xxx")会输出到控制台，替代了繁琐的System.Out.println("xxx")，有更多种类型规整的输出。

```java
@Slf4j
@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String handle01(@RequestParam("name") String name){
        log.info("请求进来了....");
        return "Hello, Spring Boot 2!"+"你好：" + name;
    }
}
```



#### Spring Initailizr

用Maven来生成项目，相对简陋。

Spring官方提供了[Spring Initailizr](https://start.spring.io/)是创建Spring Boot工程向导。IDEA直接创建Springboot项目，更便捷。

- 让程序员选择需要开发的场景（starter）
- 自动生成启动类 和 单元测试代码







# 二、核心功能

## Yaml配置文件

### 使用

yaml格式配置文件（简称后缀.yml），替代properties格式。

```java
//通过该注解与application.yml文件绑定，prefix表示与配置文件哪个前缀绑定。
@ConfigurationProperties(prefix = "myuser") 
//让该类先成为组件
@Component
public class User {
	xxx
}
```

配置文件

```yml
myuser:
  age: 18
  password: 123
  user-name: li 
```

 

### 定义类绑定的配置提示

自定义的类与yml配置文件绑定后，在yml输入时一般没有提示。若要提示，需要添加依赖。



## web开发⭐

### 1.简单功能分析

#### ①静态资源目录

只要静态资源放在类路径(resource文件夹)下的这四类文件：

​	`/static`  `/public`  `/resources` `/META-INF/resources`

那么浏览器可以直接访问：/ + 静态资源名

#### ②修改默认的静态资源设置

改变默认的静态资源访问路径：

​	通过配置文件 spring.mvc.static-path-pattern 修改。默认值：/**

改变默认的静态资源存放路径：

​	通过配置文件 spring.resource.static-location 修改。

​	默认值是一个字符串数组：["classpath:/META-INF/resources/", "classpath:/resources/", "classpath:/static/", "classpath:/public/"]

```yml
spring:
  mvc:
    #访问静态资源，需要加前缀/res。
    static-path-pattern: /res/**
  resources:
    #静态资源需要放到类路径的haha文件夹下，才能被访问。
    static-locations: [classpath:/haha/]
```

#### ③欢迎页

静态资源路径下 index.html，默认为访问首页。

静态资源目录下 favicon.ico ，默认为页面小图标，下图。

![image-20220801180315034](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202208011803128.png)

#### 访问原理

​	客户端发送请求进来，先看有无对应的Controller处理，不能处理则交给静态资源器，如果静态资源器也处理不了则响应404页面。

​	Controller返回字符串，先看有无对应的/hello的Controller处理，没有则通过视图解析器处理后，再定位页面。

```java
@RequestMapping("go")
public String go(){
    return "hello";
}
```



#### ④视图解析器

```yml
spring:
  mvc:
    view:
      suffix: .html
      prefix: /
```

- InternalResourceViewResolver：默认前缀是“/WEB-INF/”，默认后缀是“.jsp”。

如果是prefix="/"，会默认到静态资源路径去找页面。



### 2.请求参数处理

#### ①请求映射

**rest**使用：

![image-20220801201329184](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202208012013287.png)

Springboot已经有HiddenHttpMethodFilter类，在配置文件开启即可：

​		spring.mvc.hiddenmethod.filter.enabled = true （默认为false）

在前端form表单中，添加一条隐藏input，设置_method为自定义方法。

```html
<form action="/user" method="post">
    <input name="_method" type="hidden" value="Delete">
    xxx
</form>
```

#### ②普通参数与基本注解

若在控制器方法的形参和请求参数同名，即可直接获取到，无需注解。

**@PathVariable 路径变量** 

将Rest风格占位符所表示的数据赋值给控制器方法的形参

```java
@GetMapping("/testRest/{id}")
public String testRest(@PathVariable("id") String id){
}
```

- 可通过一个Map<String,String> map参数获取所有的路径变量。

**@RequestHeader 获取请求头**

 是将请求头信息和控制器方法的形参创建映射关系

​	属性及用法同@RequestParam

- 可通过一个Map<String,String> map参数获取所有的请求头信息。

**@RequestParam 获取请求参数** 

是将请求参数和控制器方法的形参创建映射关系，即请求url中的参数。

value：指定为形参赋值的请求参数的参数名

required：设置是否必须传输此请求参数，默认值为true

defaultValue：当value所指定的请求参数没有传输或传输的值为空""时，则使用默认值为形参赋值

- 可通过一个Map<String,String> map参数获取所有的请求参数。

**@CookieValue 获取cookie值**

​	是将cookie数据和控制器方法的形参创建映射关系

​	属性及用法同@RequestParam

- 也可用Cookie类型获取，内含key-value和其他属性。

**@RequestBody** 当前请求的请求体就会为当前注解所标识的形参赋值

什么是请求体：只要post请求才有请求体，如获取post请求的表单信息。

该注解没有属性，并只允许使用 String类型接收。接收字符串包含全部post信息，用&连接。

**@RequestAttribute  获取request域信息**



**@MatrixVariable 矩阵变量**







#### POJO发展过程

#### 参数处理原理

















## 数据服务

## 单元测试

## 指标监控

## 原理解析







