## CRUD增删改查

### Insert语句

![image-20220728163759508](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207281637561.png)

```mysql
INSERT INTO emp (id,`name`,sex)
	VALUES (1,'lec',0);
```

使用细节：

- 数据输入时，字符/字符串使用单引号引起来。

- 可以插入空值NULL。（前提是该字段允许为空）

- 可以一次性添加多条语句。如下图：

```mysql
INSERT INTO `goods` (id, goods_name, price)
	VALUES(50, '三星手机', 2300),(60, '海尔手机', 1800);
```

- 如果是给表中**所有字段添加数据**，**可以不写**前面的**字段名称**。
- 默认值使用：当不给某个字段值时，如果有默认值回自动添加。



### Update语句

![image-20220728163926315](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207281639376.png)

set：修改哪些列 和 赋予哪些值

where：指定应更新哪些记录(行)。没有where字句，则更新所有记录(行)。

expr是表达式。

```mysql
#全部员工薪资设置为3000
UPDATE emp 
	SET salary = 3000;
#某个员工薪资设置为5000
UPDATE emp 
	SET salary = 8000
	WHERE sex = '1';
#某个员工薪资设置增加1000
UPDATE emp 
	SET salary = salary+1000
	WHERE sex = '1';
```

- 如果要修改多字段： set 字段1=值1，字段2=值2...



### Delete语句

![image-20220728164032371](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207281640423.png)

```mysql
DELETE FROM emp 
	WHERE sex = 'llll';
```

- Delete语句不能删除某一列的值（可使用update设为 null 或者' '）

- 没有where字句，则删除所有行（记录）。
- delete语句只删除记录，不删除表。（DROP TABLE table_name ;）



### Select语句

#### 一、单表查询

##### 1.普通查询

![image-20220728164124474](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207281641533.png)

distinct 独特的。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207281642889.png" alt="image-20220728164213841" style="zoom: 80%;" />



##### 2.运算查询

![image-20220728164356022](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207281643087.png)

可以是列名column，亦可以是表达式。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207281644461.png" alt="image-20220728164411407" style="zoom:80%;" />

AS 后面的字符串（含中文）可以不加单引号。

- 指定**列** 别名的关键字AS，可省略
- 同样指定**表** 别名的关键字AS，也可省略。

如： select * from emp (as) worker; 设置emp表别名为worker。

例子：

```mysql
-- 统计每个学生的总分
SELECT `name`, (chinese+english+math) FROM student; 
-- 在所有学生总分加 10 分的情况
SELECT `name`, (chinese + english + math + 10) FROM student; 
-- 使用别名表示学生分数。
SELECT `name` AS '名字', (chinese + english + math + 10) AS total_score
	FROM student;
```



##### 3.where常用运算符（过滤搜索）

放在where关键字后：

![image-20220728165016407](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207281650518.png)

like 使用：

```mysql
-- 查询所有姓李的学生信息。
SELECT * FROM student
	WHERE `name` LIKE '李%
--%表示一个或多个字符
```

between and是闭区间：

```mysql
-- 查询英语分数在 80－90 之间的同学。
SELECT * FROM student
	WHERE english BETWEEN 80 AND 90;
```

in的使用：

```mysql
-- 查询数学分数为 89,90,91 的同学。
SELECT * FROM student
	WHERE math = 89 OR math = 90 OR math = 91;
SELECT * FROM student
	WHERE math IN (89, 90, 91);
```



##### 4.排序查询

![image-20220728165252994](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207281652043.png)

asc升序（默认）、desc降序

![image-20220728165308254](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207281653317.png)

```mysql
-- 对总分按从高到低的顺序输出 [降序] -- 使用别名排序
SELECT `name` , (chinese + english + math) AS total_score FROM student
ORDER BY total_score DESC;
```



#### 二、查询加强

##### 1. 日期可以使用比较字符（大于小于等于）

​		注意日期格式即可

```mysql
-- 如何查找 1992.1.1 后入职的员工
-- 老师说明： 在 mysql 中,日期类型可以直接比较, 需要注意格式
SELECT * FROM emp
	WHERE hiredate > '1992-01-01'
```



##### 2. like操作符（模糊）

​		%表示0到多个任意字符

​		_表示单个人任意字符

```mysql
-- ?如何显示首字符为 S 的员工姓名和工资
SELECT ename, sal FROM emp
	WHERE ename LIKE 'S%'

-- ?如何显示第三个字符为大写 O 的所有员工的姓名和工资
SELECT ename, sal FROM emp
	WHERE ename LIKE '__O%'
```



##### 3. 查询空值，使用 xx is null。

 ![image-20220301191601055](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301191601055.png)

##### 4. 多次排序。

​	后面继续写即可。

```mysql
-- ?按照部门号升序而雇员的工资降序排列 , 显示雇员信息
SELECT * FROM emp
ORDER BY deptno ASC , sal DESC;
```

##### 5.  分页查询

需求：查找结果显示时，当数据量非常大，不可能全部显示，需要一页一页显示。

![image-20220728181528989](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207281815096.png)

使用：

```mysql
-- 分页查询
-- 按雇员的 id 号升序取出， 每页显示 3 条记录，请分别显示 第 1 页，第 2 页，第 3 页
-- 第 1 页
SELECT * FROM emp
	ORDER BY empno
	LIMIT 0, 3; 
-- 第 2 页
SELECT * FROM emp
	ORDER BY empno
	LIMIT 3, 3; 
-- 第 3 页
SELECT * FROM emp
	ORDER BY empno
	LIMIT 6, 3;
```

##### 6. 分组加强

语法顺序

 ![image-20220728181733713](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202207281817813.png)

案例：

略。



#### 三、多表查询（重点、难点）

##### 1. 概念和使用：

select * from 表1, 表2;

多表查询默认处理返回的结果是：**笛卡尔积**

（第一张表的每一行和第二张表每一行结合成一条记录，共 表1行数*表2行数 条记录）

 **所以多表查询重点在于，写出过滤条件。**

```mysql
#1 显示雇员名，工资，及其所在的部门名字（雇员表中只有部门号，没有部门名）
SELECT ename,salary,dname FROM emp,dept
	WHERE emp.deptno = dept.deptno
--表名.列名

#2 显示在部门号为3的雇员名，工资，及其所在的部门名字。
SELECT dname,ename,salary
	FROM emp,dept
	WHERE emp.deptno = dept.deptno AND emp.deptno = 3

#3 显示各个员工的名字，工资，工资等级
SELECT ename,salary,grade
	FROM emp,salgrade
	WHERE salary BETWEEN losal AND hisal

```

- 提醒： 多表查询条件不能少于 **表的个数-1**，否则出现笛卡尔积。

​		如：3张表要有2个条件。



**多表查询的过程：第一张表的每条数据，拿出来与第二张表的各个记录进行对应拼接。**

**如：问题#1，把emp每个员工记录，与dept的每个部门记录对照，看哪条可以对应（给出条件是deptno相同的对应），拼接。**

​		**问题#3，把emp每个员工记录，与salgrade的每个工资等级对照，看哪条可以对应（给出的条件是emp表的salary数据要在工资表的losal和hisal中，看哪条记录符合），拼接。**



##### 2. 自连接

是指在同一张表的连接查询。（将同一张表看作两张表）

思考题目： 显示员工名称和他上级名称

```mysql
-- 老韩分析： 员工名字 在 emp, 上级的名字的名字 emp
-- 员工和上级是通过 emp 表的 mgr 列关联
-- 这里老师小结：
-- 自连接的特点 1. 把同一张表当做两张表使用
-- 2. 需要给表取别名 表名 表别名
-- 3. 列名不明确，可以指定列的别名 列名 as 列的别名
SELECT worker.ename AS '职员名' , boss.ename AS '上级名' FROM emp worker, emp boss
WHERE worker.mgr = boss.empno;
SELECT * FROM emp;
```

即：mgr上级编号也在这张表中。



##### 3.子查询

是指嵌入在其他sql语句中的select语句，也称嵌套查询。

单行子查询：只返回一行数据的子查询语句

多行子查询：返回多行数据的子查询（in关键字）

```mysql
#显示与smith同一部门的所有员工
/*
	1.先查找名为smith员工的部门号
	2.查找出所有该部门号的员工
	采用嵌套
*/
SELECT * FROM emp
	WHERE deptno = (
		SELECT deptno 
		FROM emp 
		WHERE ename='李'
	)
```



- 子查询当作临时表使用

```mysql
#各个部门中，工资最高的员工
/*
	1.先用分组和max函数把各个部门最高工资算出来
	2.把上列得出结果（部门名，最高工资），作为一张表，查询出来是那个人。
*/
SELECT * FROM ( 
	SELECT deptno,MAX(salary) AS Max_salary
	FROM emp
	GROUP BY deptno
) temp ,emp
	WHERE emp.deptno = temp.deptno 
	AND emp.salary = temp.Max_salary 
#temp是临时表名字。给出两个where条件即可确定员工
```



子查询得到结果，可用操作符

- 操作符

  - All

  ```mysql
  #查询工资比三号部门所有人都高的员工
  -- 使用all关键字写法
  SELECT * FROM emp
  	WHERE salary > ALL(
  	SELECT salary
  		FROM emp
  		WHERE deptno = 3
  		)
  -- 常规Max写法		
  SELECT * FROM emp
  	WHERE salary >(
  	SELECT MAX(salary) 
  		FROM emp
  		WHERE deptno = 3
  		)
  ```

  - any

  ```mysql
  #查询工资比三号部门其中一个人高的员工
  -- 使用any关键字写法
  SELECT * FROM emp
  	WHERE salary > any(
  	SELECT salary
  		FROM emp
  		WHERE deptno = 3
  		)
  -- 常规Min写法		
  SELECT * FROM emp
  	WHERE salary >(
  	SELECT min(salary) 
  		FROM emp
  		WHERE deptno = 3
  		)
  ```

  

- 多列子查询

此前除了当作表的子查询，其他都是只返回单个字段(单列)的子查询。 

```mysql
#得到和名为‘刘’员工的部门和岗位一致的其他员工（不包括’刘‘自己）
#全新写法：多字段比较
SELECT * FROM emp
	WHERE (deptno, job) = (
		SELECT deptno, job
		FROM emp
		WHERE ename = '刘'
	) AND emp.ename != '刘' 
	
#其他写法：作为新表
SELECT * FROM emp ,
	(SELECT * FROM emp WHERE ename = '刘') temp -- 新表temp
		WHERE emp.deptno = temp.deptno
		AND emp.job = temp.job 
		AND emp.ename != '刘' 
```



> select	表1.*	from  表1，表2
>
> 该形式表示把表1的所有字段输出
