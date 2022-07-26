## HTML

## CSS

## JavaScript

与jsp在页面插入java代码不同，js和java没有关系，只是为了吸引起的名字。

js在head标签里加入script脚本代码。

​	1. 可定义变量、数组、函数等。

```html
<!DOCTYPE html> 
<html lang="en">
<html> 
<head> 
	<meta charset="utf-8"> 
	<title>标题</title>
    <script type="text/javascript">
        //js代码
    	var i = 12;
        alert(i)
    </script>
</head> 
<body>    
	内容
</body> 
</html>
```

​	2. js包含事件。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207191158564.png" alt="image-20220719115846477" style="zoom:80%;" />

3. DOM模型

把文档中的标签，属性等 转换成为对象来管理。

### **jQuery** 

针对 js 常用功能封装了大量的 API，屏蔽了浏览器之间的兼容性问题，让你写 JS 的效率很高。

### Json

​	JSON 属于 JavaScript 的一个子集。

​	需要 Ajax 发送的数据越来越多，大家用 xml 来描述，发现解析还是挺麻烦的，于是就基于 JavaScript 的数据类型创造了 **JSON** 这种数据描述格式，很简单的就可以描述很复杂的数据。



## AJAX

局部更新技术，交互式网页应用开发技术。

Ajax 技术提供了一种新的前后端数据交互方式，不需要刷新页面，而且不阻塞页面执行流程，异步的去请求去获取、交互数据。



## Jsp

​	jsp 的主要作用是代替 Servlet 程序回传 html 页面的数据。页面中嵌套java代码。在不经过后端解析的情况下，是没法直接在浏览器中显示的。

​	js的全称是JavaScript，是一种前端语言，是可以直接运行在浏览器上的。

### EL表达式

EL (Expression Language) 表达式主要是代替 jsp 页面中的表达式脚本在 jsp 页面中进行数据的输出。

比 jsp 的表达式脚本要简洁很多。

### JSTL 标签库

为了替换代码脚本。这样使得整个 jsp 页面变得更佳简洁。 



# Vue

https://cn.vuejs.org/

好处：

1. 使用JavaScript框架
2. 简化Dom操作
3. 响应式数据驱动

---

## 基础

使用vue，要导包，使用一行代码即可。

```vue
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
```

**Vue实例作用范围：**

管理el选项命中的元素及其内部元素。

el的属性：id选择器：el: "#app"，类选择器：el: ".app"，标签选择器：el: "div"

```vue
<body>
    <div id="app">
        {{ message }}
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el: "#app",
            //el: ".app",
            //el: 'div',
            data: {
                message: 'Hello Vue!'
            }
        })
    </script>
</body>
</html>
```



**Vue实例的数据：**

三种数据类型。字符串，字符数组，对象。

定义方式不同，访问的方式和js一致。

```vue
<body>
    <div id="app">
        {{ type_String }}
        <h2>{{ type_object.attribute1}} {{ type_object.attribute2}}</h2>
        <ul>
            <li>{{type_array[0]}}</li>
            <li>{{type_array[1]}}</li>
        </ul>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el: "#app",
            data: {
                type_String: 'Hello Vue!',
                type_object: {
                    attribute1 : "值1",
                    attribute2 : "值2"
                },
                type_array:["数组1","数组2","数组3"]
            }
        })
    </script>
</body>
```



## 本地应用

### 内容绑定，事件绑定

**1.v-text属性：**设置标签的内容（会替换）

**2.v-html属性：**设置标签的内容，但数据解析为标签

```vue
<body>
    <div id="app" >
        <h2 v-text="message1"></h2>
        <h2 v-html="message2"></h2>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el: "#app",
            data: {
                message1: 'Hello Vue!',
                message2: '<a href="www.baiducom">百度</a>'
            }
        })
    </script>
</body>
```

**3.v-on属性：**为元素进行事件绑定，属性值为响应的函数。

**可简化为@事件名。方法内部可使用 this.属性名 访问vue实例data元素。**

```vue
<body>
<div id="app">
    <input type="button" value="单击" v-on:click="function1">
    <input type="button" value="双击" v-on:dblclick="function2">
    <input type="button" value="单击2" @click="function1">
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
    var app = new Vue({
        el: "#app",
        data: {
            message: 'Hello Vue!'
        },
        methods: {
            function1: function () {
                alert("单击!")
            },
            function2: function () {
                alert("双击!")
            }
        }
    })
</script>
</body>
```

### 显示切换，属性绑定

**1.v-show：**根据值真假，切换元素的显示和隐藏

​	值有三种：固定的true和false，data值，表达式

​	原理：v-show值最终都会解析为布尔值，底层是修改元素的display属性。

2.v-if：根据值真假，切换元素的显示和隐藏（操纵dom元素）

原理：底层是直接添加或删除元素。

```vue
<body>
<div id="app">
    <p v-show="false">固定值显示1</p>
    <p v-show="isShow">data数据显示2</p>
    <p v-show="num>10">表达式显示3</p>
    <p v-if="isShow">v-if显示4</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
    var app = new Vue({
        el: "#app",
        data: {
            num:18,
            isShow:true
        }
    })
</script>
</body>
```

**3.v-bind：**设置元素的属性

使用v-bind: 属性= ”属性值“ ，可以识别是否为vue实例的属性。

省略写法，直接 : 属性= ”属性值“ 

```vue
<input type="button" v-bind:value="url">
<input type="button" :value="url">
```



### 列表循环、表单元素绑定

**v- for：**根据数据生成列表结构

在数组中使用：遍历值为 值，索引

在对象中使用：遍历值为 值，键，索引

```vue
<body>
<div id="app">
    <ul>
        <li v-for="item in arr">{{item}}</li>
    </ul>
    <ul>
        <li v-for="(item,index) in arr">{{index+1}}:{{item}}</li>
    </ul>
    <p v-for="(value,key,index) in object">{{index+":"+key+"="+value}}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
    var app = new Vue({
        el: "#app",
        data: {
            arr:["上海","北京","深圳","广州"],
            object:{
                name:"leechr",
                age:18
            }
        }
    })
</script>
</body>
```

**v-on补充：**传递自定义参数，事件修饰符(对事件进一步限制)

如：@keyup表示任一键盘按钮按下即触发，添加 .enter可以限制触发的按钮为回车键。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207191752778.png" alt="image-20220719175200593" style="zoom:67%;" />



**v-model：** 获取和设置表单元素的值（双向数据绑定）

更改任意一边数据，会同步更新对方值。

![image-20220719175512370](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207191755503.png)

## 网路应用

结合网络数据开发应用。axios（网络请求库）+ vue

 使用axios，要导包，使用一行代码即可。

![image-20220719184106686](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207191841827.png)

第一个function为访问成功函数，response对象为从服务器获取到的数据。

第二个function为访问失败需要执行的函数。



