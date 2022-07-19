## 创建工程

### 步骤

1. 创建maven工程
1. pom.xml加入相关spring依赖

​	   配置web.xml：配置SpringMVC的 DispatcherServlet

​								 通过init-param标签设置SpringMVC.xml文件的位置和名称

3. 创建一个类，通过注解方式让它成为 请求控制器

​		@Controller 来实例该类，此时在SpringMVC.xml配置文件开启注解扫描。

​		*在SpringMVC.xml配置文件添加：Thymeleaf视图解析器*

​		@RequestMapping(value="/") 来实例该类的方法。value表示处理哪个浏览器url的访问，若需要请求转发，直接return “页面名” 即可，Thymeleaf配置已经帮忙简化了 页面位置前缀和.hmtl/.js等后缀。

### 差异

- SpringMVC.xml包含：开启注解扫描、Thymeleaf视图解析器、bean标签（实例类）等
- web.xml包含： 前端控制器配置（声明了SpringMVC.xml位置、load-on-startup标签）



### 访问流程

浏览器发送请求，若请求地址符合web.xml文件中的前端控制器的url-pattern，该请求就会被前端控制器DispatcherServlet处理。

前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器，将请求地址和控制器中@RequestMapping注解的value属性值进行匹配，若匹配成功，该注解所标识的控制器方法就是处理请求的方法。

处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径，通过Thymeleaf对视图进行渲染，最终转发到视图所对应页面

### 包类图

![image-20220712150234352](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207121502476.png)



## 一、获取浏览器请求

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

#### **路径中的占位符 ***

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





## 二、处理浏览器请求

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

		2. @RequestHeader 注解：将请求头信息和控制器方法的形参创建映射关系。

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

RedirectView

### 视图控制器

当控制器方法中，仅仅用来实现页面跳转，没有其他处理，即只需要设置视图名称时，可以将处理器方法使用view-controller标签进行表示



## 四、RESTful

一种风格。

以前对于同一个资源，不同操作，需要访问的url不一样。如：updateUser，deleteUser....

restful可以通过一个url，对同一个资源可以不同的操作，采取了GET、POST、PUT、DELETE四种操作。

**PUT、Delete如何实现**？

SpringMVC 提供了 **HiddenHttpMethodFilter** 帮助我们**将 POST 请求转换为 DELETE 或 PUT 请求**。

在web.xml组测一个过滤器即可。
