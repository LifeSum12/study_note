![JDK1.8主要新特性](https://cdn.tobebetterjavaer.com/tobebetterjavaer/images/sidebar/sanfene/javase-37.png)

文档 https://juejin.cn/post/6962035387787116551#heading-0

## Lambda表达式

使用Lambda表达式 依赖于函数接口，下面是函数式接口的要求:

1. 在接口中只能够允许有一个抽象方法

2. 在函数接口中定义object类中方法

3. 使用默认或者静态方法

### 集合遍历

```java
        ArrayList<String> strings = new ArrayList<>();
        strings.add("no1");
        strings.add("no2");
        strings.add("no3");
        strings.add("no4");
		//匿名内部类方式
        strings.forEach(new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        });
		//Lambda方式
        strings.forEach((s -> System.out.println(s)));
```

底层解析：

`forEach` 方法：`action` 是一个函数式接口，用于接收集合中的每个元素并对其执行操作。`Consumer` 接口定义了一个名为 `accept` 的抽象方法，该方法接收一个参数并返回 `void`，正好符合我们的需求。因此，我们可以将 Lambda 表达式作为 `forEach` 方法的参数，用于对集合中的每个元素执行操作。

```java
void forEach(Consumer<? super T> action)
```

```java
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}
```

具体来看看，forEach方法的步骤：

Collection,Map 接口都有 `forEach` 方法：

（Collection继承于Iterable接口，Map自身就有该方法）

```java
public void forEach(Consumer<? super E> action) {
//首先，该方法会对传入的函数式接口进行非空检查。注意：Objects是一个final类工具类且不可继承，与父类object不同。
    Objects.requireNonNull(action);
//为了避免在遍历过程中集合被修改，modCount记录了集合被修改的次数。
//注意：modCount是在AbstractList类中定义的一个变量，用于记录集合被修改的次数。
    final int expectedModCount = modCount;
//注意this关键字，指的是调用forEach方法的集合
    @SuppressWarnings("unchecked")
    //将集合中的元素保存到 elementData 数组中
    final E[] elementData = (E[]) this.elementData;
    //获取集合的大小 size
    final int size = this.size;	    
//用 for 循环遍历数组中的元素，并将每个元素传递给 action.accept 方法，即执行 Lambda 表达式中的操作。
    for (int i = 0; modCount == expectedModCount && i < size; i++) {
        action.accept(elementData[i]);
    }
//如果发现集合在遍历过程中被修改，就抛出 ConcurrentModificationException 异常   
    if (modCount != expectedModCount) {
        throw new ConcurrentModificationException();
    }
}

```

可以看得出，lambda表达式对Consumer接口实现。参数是该集合的每一个元素，对每一个元素的操作是自定义的。



### 集合排序

List接口有sort排序方法。

而HashMap是一种无序集合，没有排序方法。

（1.可以将其转换为其他集合类型，例如List，并使用工具类Collections.sort()方法对其进行排序。）

（2.使用TreeMap来存取。TreeMap是一种有序的Map，它会根据键的自然顺序或指定的Comparator对键进行排序。）

（3. Java 8 还可以使用Stream API对Map进行排序。）

```java
//要实现Comparator接口中的compare方法。compare方法：两个参数，返回值int类型
//实现compare方法的比较规则
public class MyComparator implements Comparator<MyObject> {
    @Override
    public int compare(MyObject o1, MyObject o2) {
        // 比较逻辑
        // 如果 o1 小于 o2，返回负数
        // 如果 o1 等于 o2，返回 0
        // 如果 o1 大于 o2，返回正数
    }
}
```

String集合排序：

```java
        ArrayList<String> strings = new ArrayList<>();
        strings.add("no1");
        strings.add("no2");
        strings.add("no3");
        strings.add("no4");
//匿名内部类方式
        strings.sort(new Comparator<String>() {
            @Override
            public int compare(String s1, String s2) {
                return s1.compareTo(s2);
            }
        });
//Lambda方式        
        strings.sort((s1,s2)-> s1.compareTo(s2));
```

List.sort()方法底层是调用Array.sort()方法。把Comparator实现类传递给Array.sort()方法。



## 方法引入

把一个方法当作一个参数传递。

| **类型**     | **语法**             | **对应lambda表达式**               |
| ------------ | -------------------- | ---------------------------------- |
| 构造器引用   | Class::new           | (args) -> new 类名(args)           |
| 静态方法引用 | Class::static_method | (args) -> 类名.static_method(args) |
| 对象方法引用 | Class::method        | (inst,args) -> 类名.method(args)   |
| 实例方法引用 | instance::method     | (args) -> instance.method(args)    |



## Stream流

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202303131757558.jpg" alt="img" style="zoom:120%;" />

### Stream将list转换为Set

```java
ArrayList<UserEntity> userEntities = new ArrayList<>();
//获取UserEntity类型的流
Stream<UserEntity> stream = userEntities.stream();
//转换
Set<UserEntity> collectSet = stream.collect(Collectors.toSet());
```

`stream.collect()`方法是Java 8引入的Stream API中的一个终端操作，它的作用是将Stream中的元素收集到一个数据结构中，比如List、Set、Map等。该方法接收一个Collector对象作为参数，Collector对象定义了具体的收集逻辑。

`Collectors.toSet()`是Stream API提供的一个Collector对象，它的作用是将Stream中的元素收集到一个Set中。具体来说，`Collectors.toSet()`方法会创建一个新的HashSet对象，并使用HashSet.add()方法将Stream中的元素逐个添加到该对象中，最终返回这个HashSet对象。

### Stream将list转换为Map

```java
       // key 为string类型 value UserEntity  集合中的数据：UserEntity , string类型
        Map<String, UserEntity> collect = stream.collect(Collectors.toMap(new Function<UserEntity, String>() 		{
            @Override
            public String apply(UserEntity userEntity) {
                return userEntity.getUserName();
            }
        }, new Function<UserEntity, UserEntity>() {
            @Override
            public UserEntity apply(UserEntity userEntity) {
                return userEntity;
            }
        }));

//Lambda写法
Map<String, UserEntity> collect = stream.collect(Collectors.toMap(userEntity->userEntity.getUserName, userEntity->userEntity))
```



## Optional

为了解决 java 中的空指针问题而生！

`Optional<T> 类(java.util.Optional)` 是一个容器类，它可以保存类型 `T` 的值，代表这个值存在。或者仅仅保存 `null`，表示这个值不存在。原来用 `null` 表示一个值不存在，现在 `Optional` 可以更好的表达这个概念。并且可以避免空指针异常。





# =========================

# Java 8 新特性

Java 8 (又称为 jdk 1.8) 是由Oracle 公司于 2014 年 3 月 18 日发布 Java 8。

目录：

- **Lambda 表达式** − Lambda 允许把函数作为一个方法的参数。

  **方法引用** − 方法引用提供了非常有用的语法，可以直接引用已有Java类或对象（实例）的方法或构造器。与lambda联合使用，方法引用可以使语言的构造更紧凑简洁，减少冗余代码。

  **函数式接口** − 可以使用Lambda表达式来表示该接口的一个实现。

- **默认方法** − 默认方法就是一个在接口里面有了一个实现的方法。

- **Stream API** −新添加的Stream API（java.util.stream） 把真正的函数式编程风格引入到Java中。

- **Date Time API** − 加强对日期与时间的处理。

- **Optional 类** − Optional 类已经成为 Java 8 类库的一部分，用来解决空指针异常。



### Lambda表达式

  #### 出现缘由

1. 每个方法都要写（但每一个要求，需要写一个方法）

优化：

​	2.1 策略设计模式：用接口。只写一个方法，定义一个接口。把接口作为参数，使用只需把实现类传入。（但每一个要求，需要写一个实现类）

​	2.2 匿名内部类。减少类的书写。

3. 引出Lambda表达式 / Stream API。（匿名内部类的优化，简化了语法）

#### 使用

![image-20211219204357934](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211219204357934.png)

函数式接口：只有一个抽象方法。可用@FunctionalInterface 注解修饰，来检查是否是函数式接口。

格式：(x , y) -> { x = x + y; System.out.println(x)}; 

​	左边为参数，右边是具体操作。一个参数可省略括号(不建议)，一条语句可省略大括号。如果只有一条语句且是返回语句，可以省略return。

如：

```java
public class Main {
    public static void main(String[] args) {
        //在传入接口时，无需传入实现类，直接使用Lambda表达式
        int a = calMethod(10,(x) -> x+100); 
        System.out.println(a);
    }

    public static int calMethod(int a, Cal cal){ //传入接口参数
        return cal.getValue(a);
    }
}
//接口（函数式）
@FunctionalInterface
interface Cal{
    public int getValue(int a);
}
```

### 函数式接口

只包含一个抽象方法的接口，称为**函数式接口**。

你可以通过 Lambda 表达式来创建该接口的对象实例。

#### 内置的四大核心

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209071423416.png" alt="image-20220907142331833" style="zoom:80%;" />

#### 其他接口（子接口）

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209071423576.png" alt="image-20220907142359483" style="zoom: 67%;" />


### 引用

#### 方法引用

可以理解为 Lambda 表达式的另一种表现形式。

##### 语法格式

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209071426053.png" alt="image-20220907142617994" style="zoom: 80%;" />

- 要求1：引用的方法的 **参数和返回值** 要和函数式接口中的抽象方法 一致。因此，可以省略参数书写。系统会一一对应。

- 要求2：(若使用的是第三种) 需满足

  ①参数列表中的第一参数是 实例方法的调用者。

  ②而第二个参数是实例方法的参数。

##### 例子

**例子1：（对象：：实例方法名）**

传统的Lambda表达式：

采用方法引用后：

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209071426271.png" alt="image-20220907142656219" style="zoom:80%;" />

Consumer中的抽象方法 accept 和 PrintSream类的 println方法 **参数和返回值一致**。



**例子2：（类：：静态方法名）**

![image-20211220201054732](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211220201054732.png)

Comparator 中的抽象方法 compare 和 Integer 类的 compare 方法 **参数和返回值一致**。



**例子3：（类：：实例方法名）**

![image-20211220202110572](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211220202110572.png)

第二种形式，系统会认为第一个参数为调用者，第二个参数为实例方法的实参。

  

#### 构造器引用

可看作特殊的方法引用

##### 格式

```java
类名::new
```

##### 例子

**例子1：（无参构造器）**

![image-20211220203611894](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211220203611894.png)



**例子2：（一个或多个参数构造器）**

![image-20211220203639723](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211220203639723.png)

只需换一个接口实现即可。（需要满足 参数/返回值 一致）



**例子3：（两个参数构造器）**

![image-20211220203925659](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211220203925659.png)

BiFunction接口的详细。

```java
public interface BiFunction<T, U, R> {
    R apply(T t, U u);
    //xxxx
 }
```



#### 数组引用

##### 语法

```java
数组类型::new
```



##### 例子

![image-20211220205247230](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211220205247230.png)



### Stream API

#### 概念

流：都是处理数据的。

**源数据** ==传到==> **流** ====经过一系列的操作 ====>**新流**

![image-20211220210814065](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211220210814065.png)



#### 步骤（三步）

![image-20211220211152607](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211220211152607.png)



##### 创建（四种方式）

![image-20211220212210031](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211220212210031.png)

##### 中间操作

###### (一) 筛选与切片

![image-20211221152701067](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221152701067.png)

1. `filter(Predicate<? super T> predicate)`

​	接受一个函数式接口Predicate（实现时，用Lambda表达式表达），从而实现过滤。

![image-20211221153929424](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221153929424.png)

- 只有当终止操作执行时，中间操作才会真正执行。
- filter内部会迭代，由Stream API完成。



2. `limit(long maxSize)`

​	只取前maxSize个元素，后面不要。

![image-20211221154320959](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221154320959.png)



3. `skip(long n)`

​	跳过前n个元素，只保留后面的元素。

![image-20211221154551789](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221154551789.png)

4. `distinct()`

​	去除重复元素。（地址相同 或 值相同(重写过equal)）

![image-20211221155222825](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221155222825.png)



###### (二) 映射

![image-20211221162043212](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221162043212.png)

1. `map(Function<? super T,? extends  R> mapper)`

​	把元素映射成一个新元素。

![image-20211221162122092](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221162122092.png)

- 上图中，下面直接采用 `Employee::getName` 类::方法名，是由于系统会认为传入的参数 Employee对象调用getName方法。



2. `flatMap(Function<? super T,? extends Stream<? extends  R>> mapper)`

​	主函数：

![image-20211221165050738](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221165050738.png)

​	其他函数 ：

![image-20211221165250041](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221165250041.png)



###### (三) 排序

![image-20211221165630167](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221165630167.png)

1. `sorted()`	自然排序

​	安装元素类的 实现接口Comparable 的 compareTo方法。如：String元素，安装它的compareTo方法来排序。

![image-20211221170037163](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221170037163.png)



2. `sorted(Comparator<?  super T> comparator)`	定制排序

![image-20211221170226216](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221170226216.png)



##### 终止操作

###### (一)查找与匹配

 ![image-20211221170458600](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221170458600.png)

1.  `allMatch(Predicate<? super T> predicate)` 

​		`anyMatch(Predicate<? super T> predicate)`

​		`noneMatch(Predicate<? super T> predicate)`

​	**返回值Boolean。**是否 所有元素 /至少一个元素  满足该要求。或者全部元素都不符合要求。

![image-20211221172251702](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221172251702.png) 

2.  `findFirst()`  找第一个

    `findAny()` 随便找一个

​	返回值Optional类(一个装数据容器，当有可能返回数据为空时，就会放到该类中)。

  ![image-20211221173406863](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221173406863.png)

- 第二个方法需求是：随便找一个处于空闲状态的员工

```java
employees.stream() 串行流
employees.parallelStream() 并行流：每个操作，都不同流在操作，不是按顺序执行。
```



3. `count()` 返回值 long

​		`max(Comparator<?  super T> comparator)` 返回值`Optional<T>`

​		`min(Comparator<?  super T> comparator)` 返回值`Optional<T>`

![image-20211221174241933](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221174241933.png)

- 不同的形式(Lambda表达式，方法引用)，实现操作。



###### (二)归约

将流中元素反复结合起来，得到一个新的值。

![image-20211221184050076](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221184050076.png)

1. `reduce(T identity, BinaryOperator<T> accumulator)`

​	返回值为泛型 T 。identity为初始值。

```java
//BinaryOperator实际是一个Function
public interface BinaryOperator<T> extends BiFunction<T,T,T> {}
BiFunction<T, U, R> {
    R apply(T t, U u);
}
```

下面例子(返回数组的总和sum)

![image-20211221183128122](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221183128122.png)

- 初始值为0，意味着第一次 x+y 时x=0，而y是从集合中取出的元素。把得到的结果再作为x，再取出新的y，以此类推。

2. `reduce(BinaryOperator<T> accumulator)` 

​	返回值为Optional。无初始值，可能返回为空。

![image-20211221184235961](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221184235961.png)



###### (二)收集

![image-20211221184743657](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221184743657.png)

Collectors类中，提供了很多静态方法，可以方便地创建常见收集器(Collector)的实例。

如：Collectors.toList()，返回一个装满元素的List。

![image-20211221185929241](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221185929241.png)

其他方法：

![image-20211221190348062](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221190348062.png)

 分组方法：

​	按状态分组，得到一个Map集合。

![image-20211221191038615](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221191038615.png)

多级分组：

​	先按状态分，再按年龄分。

![image-20211221191313609](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221191313609.png)



分区：

满足条件的一个区，不满足的另一个区。

![image-20211221191527575](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211221191527575.png)



### 并行流与顺序流

![image-20211222170944694](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20211222170944694.png)



### 接口中的默认方法与静态方法

 https://www.ssymon.com/archives/interface-static-default-method









