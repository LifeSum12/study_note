# Thymeleaf

## 简介

thymeleaf是帮助视图渲染的一个技术。

在html页面上加载java内存数据信息（如数据库查询到的数据），这个过程称为渲染render。

- Jsp（java sever pages）是一种动态网页开发技术。它使用标签在HTML网页中插入Java代码。标签通常以<%开头以%>结束。可以很方便的编写和修改HTML网页而不用在servlet用大量的println语句输出到页面。

- JavaScript 虽然Js可以在客户端动态生成HTML，但是很难与服务器交互，因此不能提供复杂的服务，比如访问数据库和图像处理等等。



### 语法准备

xmlns = XML Namespaces  

```html
<html lang="en" xmlns:th = "http://www.thymeleaf.org">
```

头部声明命名空间即可使用该库中的标签，使用时需要指定命名空间名。

如：href属性是th名称空间中定义的，有特殊的功能，与普通的href属性不一样。

```html
<a th:href="@{/hello}">HelloWorld</a><br/>
```



### 上下文路径

普通超链接**href="/"**,斜线表示http://localhost:8080/

thymeleaf的超链接属性**th:href="@{/}"**,斜线表示 http://localhost:8080/springMVC_demo1，通过thymeleaf语法自动带上下文路径，即项目名/springMVC_demo1

```html
 <a href="/springMVC_demo1/hello">HelloWorld1</a>

<a th:href="@{/hello}">HelloWorld2</a>
```

### 带参数访问

通过括号代替 “?"号

```html
以前方式：
<a th:href="@{/hello?username=admin&password=123456}">HelloWorld2</a>
thymeleaf方式：
<a th:href="@{/hello(username='admin',password=123456)}">HelloWorld2</a>
```

### 页面获取域信息

jsp获取方式：通过在标签中，添加表达式获取。

**thymeleaf获取方式，在属性中设置key名。**

```html
获取requst信息，直接写变量名：
	<p th:text="${变量名}"></p>
获取session/applicaton信息，写session.变量名/applicaton.变量名：
	<p th:text="${session.变量名}"></p>
	<p th:text="${applicaton.变量名}"></p>
```



### 遍历获取信息

th:each属性。

如下：employeeList是request域中的一个集合（集合每个元素是一个对象），每次通过自定义变量employee获取。

```html
<tr th:each="employee : ${employeeList}">
    <td th:text="${employee.id}"></td>
    <td th:text="${employee.lastName}"></td>
    <td th:text="${employee.email}"></td>
    <td th:text="${employee.gender}"></td>
</tr>
```

