

### 创建MVC工程

**步骤**

1. 创建maven工程
1. pom.xml加入相关spring依赖

​	   配置web.xml：配置SpringMVC的 DispatcherServlet

​								 通过init-param标签设置SpringMVC.xml文件的位置和名称

3. 创建一个类，通过注解方式让它成为 请求控制器

​		@Controller 来实例该类，此时在SpringMVC.xml配置文件开启注解扫描。

​		*在SpringMVC.xml配置文件添加：Thymeleaf视图解析器*

​		@RequestMapping(value="/") 来实例该类的方法。value表示处理哪个浏览器url的访问，若需要请求转发，直接return “页面名” 即可，Thymeleaf配置已经帮忙简化了 页面位置前缀和.hmtl/.js等后缀。

**差异**

- SpringMVC.xml包含：开启注解扫描、Thymeleaf视图解析器、bean标签（实例类）等
- web.xml包含： 前端控制器配置（声明了SpringMVC.xml位置、load-on-startup标签）



### SpringMVC访问流程

浏览器发送请求，若请求地址符合web.xml文件中的前端控制器的url-pattern，该请求就会被前端控制器DispatcherServlet处理。

前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器，将请求地址和控制器中@RequestMapping注解的value属性值进行匹配，若匹配成功，该注解所标识的控制器方法就是处理请求的方法。

处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径，通过Thymeleaf对视图进行渲染，最终转发到视图所对应页面



### Javaweb包类图

![image-20220712150234352](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207121502476.png)

### SSM框架总览

![image-20220721170719515](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207211707666.png)

- Spring框架是优化**业务层Service**（使用ioc，aop）
- SpringMVC是优化**控制器Contorller**，即得到浏览器请求、处理请求、响应请求 或 转发视图等。
  - 传统技术：servlet程序
  - 历史上技术有：Webwork、Struts
- Mybatis是优化**持久层Dao**，即对数据库操作。
  - 传统技术：Jdbc
  - 历史上技术有：DbUtils、JdbcTemplate、Hiberante、JPA



## 一、获取浏览器请求⭐

## @RequestMapping注解

### 1.功能概述

修饰类

修饰方法

### 2.属性（常用）

 1. **value属性**

    浏览器访问的url

 2. **method属性**

    get/post等方法....

 3. params属性（参数需要同时满足）

    浏览器请求时发送的参数

 4. headers属性（参数需要同时满足）

​		浏览器请求时发送的请求头

### 3.value属性特殊风格

#### ant风格

**在接收地址value值，可设置如下符号：**

？：表示任意的单个字符

*：表示任意的0个或多个字符

\**：表示任意的一层或多层目录

注意：在使用\**时，只能使用/**/xxx的方式

#### **路径中的占位符 **

**在页面请求url中，可通过“斜杠”来传值，但接收时候控制器注解value要设置：**

原始方式：/deleteUser?id=1

rest方式：/deleteUser/1

1. @RequestMapping注解的value属性中通过占位符{xxx}表示传输的数据。如@RequestMapping("/testRest/{id}")

2. 在controller类的方法中，为方法参数添加注解@PathVariable，该参数即可获取到请求时提供的值。



## javaweb补充知识

请求参数：

```
request.getParameter("xxx")是获得页面传来的数据，如带参超链接/表单数据.只能获取，不能设置。
request.getAttribute("xxx")是获得setAttribute的数据.可以设置和获取，一般由页面来获取显示内容。

Parameter和Attribute，虽然都存储在request中，但就算键名称相同也不会影响获取。
```

请求转发：

从一个servlet转到另一个servlet（或资源hmtl/jsp...）的操作叫请求转发。

```java
RequestDispatcher requestDispatcher = req.getRequestDispatcher("/servlet2");
requestDispatcher.forward(req,resp);
```

form表单：

```text
method属性：可选方法只有get/post，默认get。
```

xml文件中＜url-pattern＞标签值 “ /* ”与“ / ” 的区别：

```text
/*: 拦截所有的HTTP请求,包括.jsp的请求，都做为控制器类的请求路径来处理
/ : 拦截所有的HTTP请求,但不包括.jsp的请求，不会放行静态资源(html/css/js/图片)的请求
```





## 二、处理浏览器请求⭐

### 获取请求参数

*浏览器发送数据给服务器。*

一、通过ServletAPI获取（原生的方法）

​		HttpServletRequest request变量来获取，request.getParameter("xxx")。

**二、通过控制器方法的形参获取请求参数（spring特有）**

​		设置和请求参数同名的形参，即可接收到页面传来的参数（内部实现由DispatcherServlet来完成）。

格外功能：

1. @RequestParam注解：将请求参数和控制器方法的形参创建映射关系

   **不使用该注解，也可以获取到参数。**

​			三个属性：

​					value指定为形参赋值的请求参数的参数名。

​					required设置是否必须传输此请求参数，默认值为true

​					**defaultValue：当请求参数没有传输或传输的值为空""时，则使用默认值为形参赋值。**

​	2. @RequestHeader 注解：将请求头信息和控制器方法的形参创建映射关系。

​	**想要获取请求头信息，要使用该注解。**不同于获取请求参数只需设置同名参数即可。

​			三个属性：value、required、defaultValue

​			用法上同，下图为请求头信息。

![image-20220714142312814](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207141423868.png)

3. @CookieValue 注解：将cookie数据和控制器方法的形参创建映射关系

​		三个属性：value、required、defaultValue

​		用法上同。







### 域对象共享参数

*服务器发送数据给浏览器，通过域对象。*

- pageContext: 一个jsp页面有效 ❌

- request：一次请求内有效  ✔

- session：一个会话范围内有效（打开浏览器访问服务器，直到关闭浏览器）✔

- application:  整个 web 工程范围内都有效（只要 web 工程不停止，数据都在），别名servletContext

#### 向request域共享数据

原生方法: 使用ServletAPI向request域对象共享数据

​		形参HttpServletRequest request，通过调用request对象方法，向request域共享数据。
​		request.setAttribute("name", "xxx");

**spring特有方法：**

1. **使用ModelAndView向request域对象共享数据。**（建议使用，这是底层方法）
2. 使用Model向request域对象共享数据。
3. 使用map向request域对象共享数据。
4. 使用ModelMap向request域对象共享数据。




#### 向session域共享数据

1. **使用ServletAPI向request域对象共享数据（推荐使用）**

​	HttpSession session的对象，调用session.setAttribute("xxx","xxx")方法设置。

2. 使用spring特有方法。

#### 向application域共享数据

方法多种：可通过request、session、servletConfig对象来获取 application对象。

**一般采用session。**

ServletContext application = session.getServletContext();



## 三、转发视图

### Thymeleaf视图

### 转发视图

InternalResourceView

### 重定向视图

RedirectView	当功能已经完成，可以使用重定向。如删除后，重定向显示页面。

### 视图控制器

当控制器方法中，仅仅用来实现页面跳转，没有其他处理，即只需要设置视图名称时，可以将处理器方法使用view-controller标签进行表示



## 四、RESTful⭐

一种风格。

以前对于同一个资源，不同操作，需要访问的url不一样。如：updateUser，deleteUser....

restful可以通过一个url，对同一个资源可以不同的操作，采取了GET、POST、PUT、DELETE四种操作。

**PUT、Delete如何实现**？

SpringMVC 提供了 **HiddenHttpMethodFilter** 帮助我们**将 POST 请求转换为 DELETE 或 PUT 请求**。

在web.xml组测一个过滤器即可。



## 五、请求与响应之HttpMessageConverter

信息转换器。

把获取页面请求参数 转换为 后端java对象。**或者把后端java对象 转换为 页面的响应参数（常用）**

### 获取请求参数（少用）

一般使用第二章的方法获取请求参数。

#### 1.注解 @RequestBody

标识控制器的形参。获取请求体。

#### 2.RequestEntity类型

设置控制器的形参类型。RequestEntity表示整个请求的信息。可获取请求头，请求体。

getHeaders()

getBody()



### 发回响应参数

#### 1.HttpServletResponse

**传统方法：通过HttpServletResponse对象响应**

​	respone.getWriter().print("页面显示内容"); 该语句作用直接把响应体给浏览器。

响应体：浏览器页面看到的内容。



#### 2.@ResponseBody注解

**SpringMVC提供方法：@ResponseBody注解（常用）**	

标识控制器方法，将该方法的返回值直接作为响应报文的响应体响应到浏览器。

```java
@RequestMapping("/testResponseBody")
@ResponseBody
public String testResponseBody(){
    return "页面显示内容！";
}
```

> Ajax的诞生：
>
> ​	浏览器发起请求时候，目的是为了得到来自服务器的响应，从而刷新(更新)当前浏览器页面。那么，服务器为了解决，可以使用请求转发/重定向 来让浏览器跳转到新的页面。
>
> ​	但有时候请求仅仅只是为了对当前页面进行局部更新，Ajax就诞生了，它不会产生新页面，只会返回数据对当前页面进行更新。
>
> ​	所有，在用@ResponseBody注解标识的方法，返回的String字符串不再是 页面资源 or servlet程序，而是变成了响应体。因为此时不再需要返回新页面了，需要的是对当前页面更新的数据。



#### 扩展知识

##### SpringMVC处理json

​	1.导入json依赖。2.开启注解<mvc:annotation-driven />

​	完成后，服务器即可向浏览器传输bean等数据信息，内部由json实现类完成。

##### SpringMVC处理Ajax

​	通过采用@ResponseBody注解。



#### 3.ResponseEntity类型

控制器方法的返回值类型，该控制器方法的返回值就是响应到浏览器的响应报文。



## 六、文件操作

**实现文件下载：**

​	 ResponseEntity<byte[]> 返回类型，通过对responseEntity对象设置：响应体(下载内容)，响应头，状态码

​	其中响应体读取通过流来实现。

**实现文件上传：**

需要导入依赖。SpringMVC.xml添加配置。控制器再实现上传到的路径。

解决文件上传服务器的重名问题，使用的是UUID，即36位随机数（重复的概率几乎为0）。



## 七、拦截器⭐

拦截器是 拦截/处理一些url请求，与过滤器类似功能。

顺序：页面请求 --> 过滤器 -> DispatcherServlet -> [pre拦截器] --> controller控制器 --> [post拦截器]--> 返回页面 --> [after拦截器]

### 使用

拦截器是一个类，该类要自行创建并实现抽象类HandlerInterceptor。该类实现pre post after三个方法。

还需要在SpringMVC的配置文件中进行配置。即，哪个拦截器需要拦截哪些路径。

### 多个拦截器

执行顺序不同于过滤器。



## 八、异常处理器

### 介绍

SpringMVC提供了一个处理控制器方法执行过程中所出现的异常的接口：HandlerExceptionResolver

该接口有两个实现类：DefaultHandlerExceptionResolver和SimpleMappingExceptionResolver

- DefaultHandlerExceptionResolver是默认异常处理，当出现异常时候，SpringMVC自动帮我们处理，如返回404页面
- SimpleMappingExceptionResolver是自定义异常处理类。如：出现错误时，想返回一个自定义的错误页面告诉用户，可以在该类实现。

### 处理方式

1. 基于配置SpringMVC.xml方式创建自定义异常处理器

2. 基于注解方式创建自定义异常处理器

​	两种方式其具体实现，都是创建一个类，让实现接口它成为异常处理器。

​	再告诉这个类，出现什么异常，如何处理（只能选择返回哪个页面，不能其他操作）。



## 九、完全注解⭐

使用注解代替web.xml和SpringMVC配置文件的功能。

### 步骤

1.创建一个类(WebInit) 来替代web.xml配置文件。该类需要实现AbstractAnnotationConfigDispatcherServletInitializer接口，然后系统会自动找到该类来替代web.xml。

WebInit类要实现的三个方法：

- getRootConfigClasses()	需要返回一个或多个 spring的配置类
- getServletConfigClasses() 需要返回一个或多个 SpringMVC的配置类
- getServletMappings() 需要返回DispatcherServlet的映射规则，即url-pattern
- （可选重写方法）添加过滤器类



2.创建SpringConfig配置类，代替spring的配置文件。类上方添加注解@Configuration。

3.创建WebConfig配置类，代替SpringMVC的配置文件。类上方添加注解@Configuration。

​	注解类方式实现WebConfig需要的内容：

- 扫描组件 ：添加类注解@ComponetScan
- 开启mvc注解驱动 ：添加类注解@EnableWebMvc
- thymeleaf 视图解析器 ：创建方法并添加注解@Bean
- 文件上传解析器 ：创建方法并添加注解@Bean
- view-controller ： 实现WebMvcConfigurer接口的方法
- default-servley-handler(处理静态资源) ： 实现WebMvcConfigurer接口的方法
- 拦截器 ：实现WebMvcConfigurer接口的方法
- 异常处理器 ：实现WebMvcConfigurer接口的方法































十、执行过程⭐

