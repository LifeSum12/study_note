# 一、基础入门

## 快速入门

1. 创建maven工程，引入父工程和依赖。
2. 创建主程序 MainApplication 类
3. 编写业务Controller类

点击运行MainApplication类的mian方法即可启动。

application.properties 文件可配置其他信息。



## 底层注解总结

#### **@Configuration 配置类**

​	告诉SpringBoot这是一个配置类 == 配置文件

​	应用：配置类里面使用@Bean标注在方法上给容器注册组件，即创建bean对象

#### **@Import 导入组件**

```java
//给容器中自动创建出这两个类型的组件、默认组件的名字就是全类名
@Import({User.class, DBHelper.class})
public class MyConfig {
}
```

#### **@Conditional 条件装配**

​	满足Conditional指定的条件，则进行组件注入

```java
//有tom名字的Bean对象时，MyConfig类的方法才能生效。
@ConditionalOnBean(name = "tom")
public class MyConfig {
}
```
#### **@ImportResource 导入Spring配置文件**

​	把以前bean.xml文件配置的bean，直接引入到容器中，减免了手动一个个bean注解来写。

```java
@ImportResource("classpath:beans.xml")
public class MyConfig {
}
```

#### **@ConfigurationProperties 配置绑定**

​	在配置文件application.properties 写好属性值，@Component注册组件时，会自动赋值给实例对象Car。

```properties
mycar.brand=BYD
mycar.price=100000
```

```java
@Component
@ConfigurationProperties(prefix = "mycar")
public class Car {
//该Car类含brand、price属性，略。
}
```

## 底层原理

​	SpringBoot默认加载所有的自动配置类，但会按需加载，即符合@Conditional条件才加载。如果用户自己配置了以用户的优先。

​	每个自动配置类按照条件进行生效，默认都会绑定配置文件指定的值（即通过配置文件初始化属性值），先在xxxxProperties里面读取。 该配置文件还会和 application.properties绑定，即用户可以也在全局配置 application.properties修改自己需要的配置。

## 小技巧

#### Lombok简化开发

导入相关lombok依赖即可。

1.简化pojo实体类代码，以后只需写属性，注解会帮忙写上get/set方法、构造器、toString方法、hashcode方法、equals方法。

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

2.简化日志开发，使用log.info("xxx")会输出到控制台，替代了繁琐的System.Out.println("xxx")。

```java
@Slf4j
@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String handle01(@RequestParam("name") String name){
        log.info("请求进来了....");
        return "Hello, Spring Boot 2!"+"你好："+name;
    }
}
```



#### dev-tools

导入对应依赖。

spring提供的工具，唯一实用功能是：当修改了源文件，按需重新启动项目。快捷键Ctrl+F9

付费工具**Jrebel**：重新加载reload，无需重新启动。

**Ctrl + F5 清理浏览器缓存**



#### Spring Initailizr

[Spring Initailizr](https://start.spring.io/)是创建Spring Boot工程向导。IDEA直接创建Springboot项目，更便捷。



# 二、核心功能

## 配置文件

### Yaml文件

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







