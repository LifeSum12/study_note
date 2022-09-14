# SSM整合

## 文件配置

### **①创建Maven Module**

### **②pom.xml导入依赖**

### **③数据库中创建表**

### ⑤**创建并配置web.xml**

包含：

​	**过滤器**：编码过滤器，处理请求方式PUT和DELETE的过滤器

​	**前端控制器**DispatcherServlet，并**指定SpringMVC的配置文件**的位置和名称。

​	**设置Spring的配置文件**的位置和名称。

​	配置Spring的**监听器**(当tomcat启动时，自动装配spring的配置信息)

### ⑥**创建并配置SpringMVC.xml**

 包含：

​		**扫描组件context:component-scan**（识别@controller创建对象）

​		**配置视图解析器thymeleaf**

​		**配置访问的视图控制** （如访问''/'，自动跳转index.html）

​		**开启MVC的注解驱动** （访问的视图控制开启了，其他@RequestMapping会失效，需手动开启该配置）

​		配置默认的servlet处理静态资源default-servlet-handler

​		（可选）拦截器，异常处理器

### ⑦创建并配置Spring.xml



注意：

- 除了控制层组件(bean对象)交给springmvc来管理，其他的都给spring管理。



## 代码开发

### 项目目录图

java目录下包含：

​		controller包：控制器类

​		mapper包：mapper接口

​		pojo包：实体类

​		service包：XxxService接口，serviceImpl包(包含每个service接口实现类)

resource目录下包含：

​		jdbc.properties 数据库信息

​		mybatis-config.xml

​		spring.xml

​		springmvc.xml

### 代码

**实体类**

```java
public class Employee{
    //属性
    //构造方法
    //get和set方法
}
```

**控制器类**

```java
@Controller 
public class EmployeeController { 
    
    @Autowired 
    private EmployeeService employeeService; 
    
    @RequestMapping(value = "/employee", method = RequestMethod.GET) 
    public String getAllEmployeeList(Model model){
        //查询所有员工信息在
        List<Employee> list = employeeService.getAllEmployee(); 
        //将员工信息在请求域中共享
        model.addAttribute("list",list);
        //跳转页面
        return "employee_list";
    } 
}
```

**service接口**

```java
public interface EmployeeService { 
    List<Employee> getAllEmployee(); 
}
```

**serviceImpl实现类**

```java
@Service 
public class EmployeeServiceImpl implements EmployeeService {
    
    @Autowired 
    private EmployeeMapper employeeMapper; 
    
    @Override 
    public List<Employee> getAllEmployee() { 
        List<Employee> list = employeeMapper.getAllEmployee(); 
    } 
}
```

**mapper接口**

```java
public interface EmployeeMapper { 
    List<Employee> getAllEmployee(); 
}
```

**mapper.xml配置文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?> 
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.atguigu.ssm.mapper.EmployeeMapper"> 
    
    <select id="getAllEmployee" resultType="Employee"> 
        select * from t_emp 
    </select> 
    
</mapper>
```

**前端**

**index.html首页**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th = "http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>index</title>
</head>
<body>
    <!--通过超链接发送get请求，传给控制器处理-->
	<a th:href="@{/employee}">查询所有员工信息</a><br/>
</body>
</html>
```

**employee_list.html显示结果页面**

```html
<!--theymeleaf中获取requst信息，直接写${变量名}。-->
<!--把控制器类中传入model对象的参数获取即可，然后 th:each属性实现循环输出-->

<tr th:each="employee : ${list}">
    <td th:text="${employee.empName}"></td>
    <td th:text="${employee.age}"></td>
    <td th:text="${employee.gender}"></td>
    <td th:text="${employee.email}"></td>
</tr>

```



### 流程

**①index.html首页**

​	请求访问，控制器通过匹配url来接收请求。

**②Controller类**

​	接收到请求

​	处理请求：调用Service实现类、设置共享域信息返回信息、转发视图

**③serviceImpl类**

​	通过Mapper接口得到其实现类，调用类中的方法操作数据库，最终返回数据。

**④employee_list.html显示结果页面**

​	通过共享域获取信息，显示在页面中。



**mapper接口**

​	声明方法，让mybatis实现。

**mapper.xml配置文件**

​	编写sql语句。



**注意：一个pojo类 对应一个Controller类、Service接口、Service实现类、Mapper接口、Mapper配置文件**