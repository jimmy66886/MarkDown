# MySql

可能就是关于多表联查和一些数据库高级篇的部分吧,毕竟那块实在是薄弱.

## 1 多表查询

多表查询,也成为关联查询,指两个或更多个表一起完成的查询操作.

**前提条件**:这些一起查询的表之间是有关系的(一对一,一对多),它们之间一定是有关联字段的,这个关联字段可能是建立了外键,也可能没有建立外键,比如员工表和部门表,这两个表靠`部门编号`进行关联

案例:3个表
1. employees
![20230527092351](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230527092351.png)
2. departments
![20230527092404](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230527092404.png)
3. locations
![20230527092415](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230527092415.png)

>三个表的关联关系:
![20230527092544](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230527092544.png)

### 1.1 多表查询如何实现

根据员工id查询员工对应的部门名

1. 先看错误的实现方式(每个员工都与每个部门匹配了一遍):
```sql
# 错误的实现方式：
select employee_id,department_name
FROM employees,departments #查询出2889条数据

SELECT * FROM employees # 107条
SELECT 2889/107 FROM DUAL  # 结果为27
SELECT * FROM departments  # 27个部门
```

为什么出现这种情况呢?现在就是107个员工都在27个部门中工作过,**这种属于是求出了两个表的笛卡尔积,也叫出现笛卡尔积的错误**
**这个错误是怎么导致的呢,是因为缺少了多表的连接条件**

>笛卡尔积(虽然我以前学过)

笛卡尔积是一个数学运算,假设我有两个集合X和Y,那么X和Y的笛卡尔积就是X和Y的所有可能组合,也就是第一个对象来自于X,第二个对象来自于Y的所有可能,组合的个数即为两个集合中元素个数的乘积

>笛卡尔积的错误会在下面条件下产生:
1. 省略多个表的连接条件(或关联条件)
2. 连接条件(或关联条件)无效 
3. 所有表中的所有行互相连接

为了避免笛卡尔积,可以在where中加上连接条件.

*刚配置了一下md的代码片段,现在打个cc就可以出来反引号了,舒服了*

加上连接条件:
```sql
SELECT
	employees.employee_id,
	departments.department_name,
	employees.department_id
FROM
	employees,
	departments
WHERE
	employees.department_id = departments.department_id # 查询出106
```

**从sql优化的角度,建议多表查询时,每个字段前都指明其所在的表**

因为有一个员工是没有部门id的,所以只有106条记录

>给表起别名
由于表名特别长导致sql语句特别长时,可以给表起别名-在select和where中使用表的别名,**注意,如果设置了表的别名,此时在select和where中不能使用表的原名了,必须使用别名**
```sql
SELECT
	emp.employee_id,
	dept.department_name,
	emp.department_id
FROM
	employees emp,
	departments dept
WHERE
	emp.department_id = dept.department_id # 查询出106
```

---

?,我突然明白什么时候会用到外连接了,比如现在这种情况,我要查询所有的员工信息以及对应的部门信息,此时要设置连接条件,那就是两个部门id相等,此时如果没有部门id,那么该员工就不会出现在结果集中,怎么解决呢?就要使用到外连接了,看下面的sql
```sql
SELECT employee_id,department_name
FROM employees LEFT JOIN departments ON employees.department_id = departments.department_id
```

此时是左连接,得到的结果是107条,其中包含一个没有部门id的员工

```sql
SELECT employee_id,department_name
FROM employees RIGHT JOIN departments ON employees.department_id = departments.department_id
```

此时是右连接,会返回除了每个员工以及对应的部门外,还会有没有员工的部门(但是此时并没有*没有部门id的员工的信息*)

**现在理解了吧,如果是左连接,就会返回左边所有的记录,如果是右连接,就会返回右边所有的记录**

没事,后面还会详细的讲

---

>再加一个表

练习:查询员工的employee_id,last_name,department_name,city
```sql
SELECT
	e.employee_id,
	e.last_name,
	d.department_name,
	l.city
FROM
	employees e,
	departments d,
	locations l
WHERE
	e.department_id = d.department_id
AND d.location_id = l.location_id
```

### 1.2 多表查询的分类

#### 1.2.1 等值连接Vs非等值连接

像上面的例子,明显都是等值连接(连接条件,x=x,那就是等值连接),其余的条件是都是非等值连接

举个例子
```sql
# 要查询员工的姓名，工资，和grade_level
SELECT
	e.last_name,
	e.salary,
	j.grade_level
FROM
	employees e,
	job_grades j 
-- WHERE e.salary BETWEEN j.lowest_sal AND j.highest_sal
WHERE
	e.salary >= j.lowest_sal
AND e.salary <= j.highest_sal;
```

>between..and..
和下面的意义是一样的,between后面紧跟下界,and后面紧跟上界

#### 1.2.2 自连接Vs非自连接

**不同的表之间进行连接-非自连接,同一张表进行的连接-自连接**,也叫自我引用:
![20230527112759](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230527112759.png)

*有没有很熟悉?之前的项目里面的菜单结构就是这个表,有id和parent_id*

举例:
```sql
# 练习: 查询员工id,员工姓名,及其管理者的id和姓名
SELECT
	emp.employee_id,
	emp.last_name,
	mgr.employee_id,
	mgr.last_name
FROM
	employees emp,
	employees mgr
WHERE
	emp.manager_id = mgr.employee_id
```

此时也是106条,因为公司老大没有管理者,所以他没有在这个记录中,如果使用left join的话,应该就能出来了,试试

```sql
SELECT
	emp.employee_id,
	emp.last_name,
	mgr.employee_id,
	mgr.last_name
FROM
	employees emp
LEFT JOIN employees mgr ON emp.manager_id = mgr.employee_id
```

此时就可以得到107条数据,里面包含了第一条没有管理者id的数据:
![20230527113713](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230527113713.png)

#### 1.2.3 内连接Vs外连接

>什么叫内连接:

上面写的都是内连接,随便拿一个:
```sql
SELECT
	emp.employee_id,
	dept.department_name,
	emp.department_id
FROM
	employees emp,
	departments dept
WHERE
	emp.department_id = dept.department_id # 查询出106
```

这就是内连接,`emp.department_id = dept.department_id`会拿出左表和右表中满足条件的记录,左表,右表,就是from里面,左边的就是左表,右边的就是右表

>官方解释:
合并具有同一列的两个以上的表的行,结果集中不包含一个表与另一个表不匹配的行

**再看上面的sql,因为有一个员工是没有部门id的,所以跟右边的部门表无法匹配,就没有该条员工的记录,这就是内连接.还有一种情况,就是有些部门是没有对应的员工的,此时就会没有该条部门的记录**

想要把左表中所有的数据,不管有没有匹配条件,就是左连接,反之,右连接

>什么叫外连接

示例:
查询**所有**的员工的last_name和对应的部门信息(出了所有,一定就是一个外连接)
```sql
SELECT
	emp.last_name,
	dept.department_name
FROM
	employees emp
LEFT JOIN departments dept ON emp.department_id = dept.department_id
```

**官方解释:**
合并具有同一列的两个以上的表的行,结果集中除了包含一个表与另一个表匹配的行之外,还查询到了左表/右表中不匹配的行


---

这里又涉及到SQL92和SQL99两种语法

>SQL92的语法:
使用`(+)`
```sql
SELECT
	emp.employee_id,
	dept.department_name,
FROM
	employees emp,
	departments dept
WHERE
	emp.department_id = dept.department_id(+)
```

**当然,MySql不支持SQL92的语法中外连接的写法**-*有没有一种别骗的感觉哈哈哈,虽然mysql不支持,但是oracle是可以的*

>SQL99的语法:
使用`JOIN ... ON`的形式进行多表查询,这种方式也能解决外连接的问题.MySql支持这种语法(SQL99的语法不仅可以实现外连接,也可以实现内连接)

1. SQL99实现内连接:
```sql
SELECT
	emp.last_name,
	dept.department_name,
	l.city
FROM
	employees emp
INNER JOIN departments dept ON emp.department_id = dept.department_id INNER JOIN locations l ON dept.location_id = l.location_id
```
![20230527155634](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230527155634.png)

**注意,这里的`INNER JOIN`和`JOIN`是一样的,可以把`JOIN`当作INNER`JOIN`的简写**

外连接就看下面的吧

---


所以这里外连接又分为三类,一类是左连接,一类是外连接,满外连接(这个之前没了解过)

>这里员工表一共107条记录,有一条是没有部门id的,而部门表中一共是27条记录,有16个部门是没有员工的,此时如果是左外连接,那么就是107条,返回的结果中有一条记录没有部门信息,而如果是右4外连接,此时返回的结果就是106+16=122条,因为结果集中包含106条匹配的结果以及16个没有员工的部门信息

1. 左外连接:

说白了就是比内连接多出了左表中不匹配的行,或者说是**会查询出左表中所有的行**

举个例子
查询所有的员工的last_name,department_name信息
```sql
SELECT
	emp.last_name,
	dept.department_name
FROM
	employees emp
LEFT JOIN departments dept ON emp.department_id = dept.department_id
```

**由于只有外连接才有`LEFT/RIGHT`,所以这里的`OUTER`也可以省略掉**

2. 右外连接:

同理,就是返回的结果中除了匹配的行,还会查出右表中不满足条件的行,或者说是**会查出右表中所有的行**

举个例子
查询**所有**部门的department_name以及对应的员工last_name信息

```sql
SELECT
	emp.last_name,
	dept.department_name
FROM
	employees emp
RIGHT JOIN departments dept ON emp.department_id = dept.department_id
```

此时就能查到122条数据,**一共106条有员工的部门,加上16条没有员工的部门

![20230527160942](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230527160942.png)

---

>那如果想查询出左表和右表中所有的记录呢?就是不管匹配不匹配,都要查出来,就要用到满外连接

满外连接在sql99中的语法就是`FULL JOIN`
```sql
SELECT
	emp.last_name,
	dept.department_name
FROM
	employees emp
FULL JOIN departments dept ON emp.department_id = dept.department_id
```

但是,mysql**不支持**,mysql支持什么呢:

这就需要其他方式了:
![20230527161912](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230527161912.png)

看这个表,是7中join方式,我们想要实现full join,那么就需要借用另外两种join方式了-(图左中和右中)

在那之前,需要再了解一个内容:UNION的使用

>UNION的使用

**合并查询结果**

利用UNION关键字,可以给出多条SELECT语句,并将它们的结果组合成单个结果集,合并时,两个表对应的列数和数据类型必须相同,并且相互对应,各个SELECT语句之间使用UNION或UNION ALL关键字分隔

语法格式:
```sql
SELECT column,...FROM table1
UNION (ALL)
SELECT column,...FROM table2
```

>`UNION`和`UNION ALL`的区别

1. `UNION`操作符返回两个查询的结果集的并集,去除重复记录
2. `UNION ALL`操作符返回两个查询的结果集的并集,对于两个结果集的重复部分,不去重
3. 注意,执行UNION ALL语句时所需要的资源比UNION语句少,如果明确知道合并数据后的结果数据不存在重复数据,或者不需要去除重复的数据,则**尽量使用UNION ALL语句**,以提高数据查询的效率

就是这个意思,UNION就是123,而UNION ALL多了一个交集,为123+106
![20230527163212](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230527163212.png)


这时我们再想实现满外连接:(我们这里使用左上和右中图)
```sql
SELECT
	emp.last_name,
	dept.department_name
FROM
	employees emp
LEFT JOIN departments dept ON emp.department_id = dept.department_id
UNION ALL
	SELECT
		emp.last_name,
		dept.department_name
	FROM
		employees emp
	RIGHT JOIN departments dept ON emp.department_id = dept.department_id
	WHERE
		emp.department_id IS NULL
```

此时就能实现查询到左表和右表中所有的数据了
![20230527164222](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230527164222.png)

##### 7中JOIN的实现
把表再拿下来:
A就当成员工表,而B就是部门表
![20230527161912](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230527161912.png)

>中图-内连接-查询结果应为106条
```sql
SELECT
	emp.employee_id,
	dept.department_id 
FROM
	employees emp
	INNER JOIN departments dept ON emp.department_id = dept.department_id
```

>左上图-左外连接-107条数据,包含一条没有部门的员工信息
```sql
SELECT
	emp.employee_id,
	dept.department_id 
FROM
	employees emp
	LEFT JOIN departments dept ON emp.department_id = dept.department_id
```

>右上图-右外连接-122条数据,包含106个有部门信息的员工信息,以及16条没有员工的部门信息
```sql
SELECT
	emp.employee_id,
	dept.department_id 
FROM
	employees emp
	RIGHT JOIN departments dept ON emp.department_id = dept.department_id
```

>左中图-左外连接-1条数据,只会查到没有部门id的员工信息-且把两者相同的部分也给抹掉了
```sql
SELECT
	emp.employee_id,
	dept.department_id 
FROM
	employees emp
	LEFT JOIN departments dept ON emp.department_id = dept.department_id 
WHERE
	dept.department_id IS NULL
```

>右中图-右外连接-16条数据
```sql
SELECT
	emp.employee_id,
	dept.department_id 
FROM
	employees emp
	RIGHT JOIN departments dept ON emp.department_id = dept.department_id
WHERE emp.department_id IS NULL
```

>左下图-两个表的满外连接-得到全部的123条数据
1. 左上图UNION ALL 右中图
```sql
SELECT
	emp.employee_id,
	dept.department_id 
FROM
	employees emp
	LEFT JOIN departments dept ON emp.department_id = dept.department_id UNION ALL
SELECT
	emp.employee_id,
	dept.department_id 
FROM
	employees emp
	RIGHT JOIN departments dept ON emp.department_id = dept.department_id 
WHERE
	emp.department_id IS NULL
```
2. 右上图UNION ALL 左下图
```sql
SELECT
	emp.employee_id,
	dept.department_id 
FROM
	employees emp
	RIGHT JOIN departments dept ON emp.department_id = dept.department_id UNION ALL
SELECT
	emp.employee_id,
	dept.department_id 
FROM
	employees emp
	LEFT JOIN departments dept ON emp.department_id = dept.department_id 
WHERE
	dept.department_id IS NULL
```

>右下图-左中和右中UNION ALL--得到17条数据
```sql
SELECT
	emp.employee_id,
	dept.department_id 
FROM
	employees emp
	LEFT JOIN departments dept ON emp.department_id = dept.department_id 
WHERE
	dept.department_id IS NULL UNION ALL
SELECT
	emp.employee_id,
	dept.department_id 
FROM
	employees emp
	RIGHT JOIN departments dept ON emp.department_id = dept.department_id 
WHERE
	emp.department_id IS NULL
```

### 1.3 SQL99语法的新特性

#### 1.3.1 自然连接

SQL99在SQL92的基础上提供了一些特殊语法,比如`NATURAL JOIN`用来表示自然连接,我们可以把自然连接理解为SQL92中的等值连接,它会帮你自动查询两张连接表中`所有哦相同的字段`,然后进行等值连接
```sql
# 普通写法:
SELECT
	employee_id,
	last_name,
	department_name 
FROM
	employees e
	JOIN departments d ON e.department_id = d.department_id 
	AND e.manager_id = d.manager_id
# 新写法:
SELECT
	employee_id,
	last_name,
	department_name 
FROM
	employees e
	NATURAL JOIN departments d
```

**就是会选择相同的字段进行等值连接**

#### 1.3.2 USING

原来的写法:
```sql
SELECT
	employees.employee_id,
	employees.last_name,
	department_name 
FROM
	employees
	JOIN departments ON employees.department_id = departments.department_id
```

用USING:
```sql
SELECT
	employees.employee_id,
	employees.last_name,
	department_name 
FROM
	employees
	JOIN departments USING(department_id)
```

效果是一样的.相当于替换掉`employees.department_id = departments.department_id`

嗯...暂时到这吧.
2023年5月27日18点47分

嗯...
仔细想了以下,感觉还是把练习题也过一遍吧

### 1.4 练习题

>1. **显示`所有`员工的姓名,部门号,和部门名称**

```sql
SELECT
	e.last_name,
	e.department_id,
	d.department_name 
FROM
	employees e
	-- 普通写法:
	LEFT JOIN departments d ON e.department_id = d.department_id
	-- 高级写法:
	-- LEFT JOIN departments d USING(department_id)
```

>2. **查询部门号为90的员工的job_id和90号部门的location_id**

```sql
SELECT
	e.job_id,
	d.location_id 
FROM
	employees e
	INNER JOIN departments d ON e.department_id = d.department_id 
WHERE
	e.department_id = 90
```

>3. **选择`所有有`奖金的员工的last_name,department_name,location_id,city**

*e.commission_pct是奖金*

注意,这里是左连接,因为有一个人是有奖金,但是没有部门的:
![20230528001438](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230528001438.png)
```sql
SELECT
	e.last_name,
	d.department_name,
	e.commission_pct,
	l.location_id,
	l.city 
FROM
	employees e
	LEFT JOIN departments d ON e.department_id = d.department_id
	LEFT JOIN locations l ON d.location_id = l.location_id 
WHERE
	e.commission_pct IS NOT NULL
```

>4. **选择city在Toronto工作的员工的last_name,job_id,department_id,department_name**

这里就不涉及到外连接了,因为只有有部门的员工才有city这个对应的属性

```sql
SELECT
	e.last_name,
	e.job_id,
	e.department_id,
	d.department_name 
FROM
	employees e
	INNER JOIN departments d ON e.department_id = d.department_id
	INNER JOIN locations l ON d.location_id = l.location_id 
WHERE
	l.city = 'Toronto'
```

>5. **查询员工所在的部门名称,部门地址,姓名,工作,工资,其中员工所在的部门的部门名称为'Executive'**

```sql
SELECT
	d.department_name,
	l.street_address,
	e.last_name,
	j.job_title,
	e.salary 
FROM
	employees e
	JOIN departments d ON e.department_id = d.department_id
	JOIN locations l ON d.location_id = l.location_id
	INNER JOIN jobs j ON e.job_id = j.job_id 
WHERE
	d.department_name = 'Executive'
```

>6. **选择指定员工的姓名,员工号,以及他的管理者的姓名和员工号,结果类似于下面的格式:**
`employees  Emp#  manager  Mgr#`

```sql
SELECT
	e.last_name "employees",
	e.employee_id "Emp#",
	m.last_name "manager",
	m.employee_id "Mgr#" 
FROM
	employees e
	LEFT JOIN employees m ON e.manager_id = m.employee_id
```

>7. **查询哪些部门没有员工**

相当于员工表中没有的部门id,因为是is null啊,这样就排除了员工表中出现的部门id,也就是派出了有员工的部门,只剩下没有员工的部门

```sql
SELECT
	d.department_id,
	d.department_name
FROM
	employees e
	RIGHT JOIN departments d ON e.department_id = d.department_id 
WHERE
	e.department_id IS NULL
```

>8. **查询哪个城市没有部门**

```sql
SELECT
	city 
FROM
	locations l
	LEFT JOIN departments d ON l.location_id = d.location_id 
WHERE
	d.location_id IS NULL
```

>9. **查询部门名为`Sales`或`IT`的员工信息**

```sql
SELECT
	e.last_name,
	d.department_id,
	d.department_name 
FROM
	employees e
	JOIN departments d ON e.department_id = d.department_id 
WHERE
	d.department_name IN (
		'Sales',
	'IT')
```

ok,课后题也加进来了,告一段落.

---

2023年9月14日 09点19分

又回来了,看看主从复制和redis集群

### Mysql主从复制

一般都是一主一从

#### 主从复制概述

**提升数据库并发能力**

一般应用对数据库而言都是读多写少,也就是说数据库读取数据的压力比较大,有一个思路就是采用数据库集群的方案,做**主从架构**,进行**读写分离**,这样同样可以提升数据库的并发处理能力,但并不是所有应用都需要对数据库进行主从架构的设置,毕竟设置架构本身是有成本的.

>作用
1. **读写分离**,我们可以通过主从复制的方式来**同步数据**,然后通过读写分离提高数据库并发处理能力
   - 其中master代表主库,负责写入数据库,也称之为写库
   - slave从库,负责读取数据,称之为读库
   - 当主库进行更新时,会自动将数据复制到从库中,而我们在客户端读取数据的时候,会从从库中进行读取
![20230914094602](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230914094602.png)
2. **数据备份**,我们通过主从复制将主库上的数据复制到了从库上,相当于是一种**热备份机制**,也就是在主库正常运行时进行的备份,不会影响到服务
3. **具有高可用性**,数据备份实际上是一种冗余机制,通过这种冗余的方式可以换取数据库的高可用性,也就是当服务器出现**故障**或**宕机**的情况下,可以切换到从服务器上,保证服务的正常运行


