# <center>MySQL笔记</center>

[TOC]

![学习图](C:\Users\Administrator\Desktop\MySQL基础.png)



## sql基本介绍

数据库名：wuchaomysql

密码：483256

----

**连接数据库：**`mysql -h localhost -u root -p`

---

**phpadmin:**一个mysql的dashboard，网页的图形界面。

**SQL:**结构化查询语言。

**what**类型：只需要写出你需要什么。(sql...)

**how**类型 : 怎么去写，怎么去实现。（C++/java/python...）

------

**sql的学习模块：**

DML:数据操作语言，也就是数据库的增删改查。（80%）

DDL:数据定义语言，也就是建表建库建试图。（15%）

DCL:数据控制语言， 对数据库进行监控，权限访问等。（5%）

---

## sql基本操作

**表（table）：**多行多列的数据。

**库(database)：**多张表。

```mysql
show databases; 查看数据库
use dbname; 选择并进入数据库
show tables; 查看表
create database dbname [charset 字符集]; 创建一个数据库
例如： create database wuchao charset utf8;

drop database dbname; 删除一个数据库
修改数据库的名字?这是不可以的，但是可以修改表和列的名字。

create table tablename(列名 字符类型， 列名 字符类型， ....); 创建表
例如：create table user(sum int, name varchar(10));

select * from tablename; 从表中读数据。*表示所有数据
drop table tablename; 删除表
rename table oldname to newname; 修改表名

truncate tablename; 清空表数据
Truncate和Delete是有区别的:
在于 truncate相当于删表再重建一张同样结构的表,操作后得到一张全新表.而Delete是从删除所有的层面来操作的.Trucate相当于把旧的表扔了重画一张,Delete相当于用橡皮把学籍表的数据库擦掉.如果决定全清空的下,truncate速度更快一些.

desc tablename; 查看表结构

解决字符集问题:
默认建表一般用utf8, 而我们在windows下窗口是GBK的,
因此,需要声明字符集.
Set names gbk;


一定要记住 where就表示查行。
where expr; expr为表达式，为真则执行。所以 where 1;就表示所有的都要； where 0;啥都不要。

show create table/view tablename/viewname; 			//查看创建表/视图的过程
show table status [\G]; 							//查看所有表的详细信息，\G表示竖着显示
show table status where name = 'XXX' \G; 			//查看表XXX的详细信息
```

---

# DML操作

---

## insert操作

首先建立一个user表：

```mysql
create table user (
uid int primary key auto_increment,
name varchar(20) not null default '',
age smallint unsigned not null default 0
) engine myisam charset utf8;
```

使用`desc user`查看表结构可以发现表包含`uid` ,`name`,`age`三个属性。

现在开始往表中`insert`数据，语法为`insert into tablename (列名1，列名2，...) values (数据1，数据2，.,,)`添加数据的时候如果是字符或者字符串需要加单引号。

**注意：**

列名可以不用全部列出来，要插入哪些列就写哪些列名，对应的数据也要与列一一对应。比如下面的第二行`age`没有写，默认就是`0`。如果要插入全部列的数据，可以只写`tablename`不写`（列名1，列名2，...）`。

| uid  | name  | age  |
| :--: | :---: | :--: |
|  1   | Lucy  |  20  |
|  2   | Jack  |  0   |
|  3   | Lilei |  18  |

---

## update操作

> 更新谁的什么值？也就是更新第几列的第几行数据。
>
> 改哪张表？你需要改哪几列的值，在哪些行生效？

基本语法：`update tablename set 列名1=数值1，列名2=数值2,... where 行名=数值(或者是表达式);`

如上表要把`Jack`的年龄改为`22`。`update user set age=22 where name='Jack';`

使用`select * from user;`查看修改后结果如下：

| uid  | name  | age  |
| :--: | :---: | :--: |
|  1   | Lucy  |  20  |
|  2   | Jack  |  22  |
|  3   | Lilei |  18  |

---

## delete操作

> 要删除哪张表，删掉哪些行？只能删除行，列是不行的。

基本语法为`delete from tablename where expr；`

例如上面的表格删除：`delete from user where uid=3;`就 删除了第三行的数据。

---

## select操作与select查询模型（重要）

> 查那个表的数据？你要选择哪些列来查询？要选择哪些列？

基本语法：`select 列1，列2，.... from 表名 where expr;`

例如上面的表格：`select uid,name where uid>=2;`就查到了2，3两行的前两列。

> 列名就是变量，变量就可以参与运算。where expr; expr为表达式，为真则执行。所以 where 1;就表示所有的都要； where 0;啥都不要。

既然是变量那么就可以计算。例如要取出他们明年age的值。`select age+1 from user where 1;`

计算`goods`表中，市场价格和商店价格的差。

```mysql
select goods_id,
goods_name,
shop_price,
market_price,
marlet_price - shop_price 
from goods;
```



例子：

- [ ] 更改`mian`表中数字在（20，29）的为20，（30，39）的为30。

| num  |
| :--: |
|  3   |
|  12  |
|  15  |
|  25  |
|  23  |
|  29  |
|  34  |
|  37  |
|  32  |

```mysql
update mian set num=floor(num/10)*10  where num between 20 and 39;
```

----

## 逻辑运算符

`in`和`not in `表示在那个范围，`where age in (20,30);`表示年龄在20到30之间。

`between`表示介于两者之间，`where age between 10 and 20;`

---

## 模糊查询

> 取出诺基亚系列手机？ like 模糊匹配，% 通配任意字符，_ 通配单一字符

`select * from goods where goods_name like "诺基亚%"；`

`select * from goods where goods_name like "诺基亚___"；`手机后面跟三个字符的。

----

## 字符串操作

- [ ] 从`goods`表中把以诺基亚改为HTC。

```mysql
select concat('HTC', substring(goods_names, 4)) 
from goods 
where goods_name like '诺基亚%';
```

---

## NULL

> 一般都要避免用NULL，如果要查询为NULL的值，必须使用 is 来查询。

`where name is null;`或者`where name is nt null;`

---

## group 分组与统计函数

> 统计函数主要有：max(), count(),avg(),min(),sum()

使用方法直接在变量名上使用函数。例如：`select avg(shop_price) from goods;`

`select count(*) from goods;`

> 分组统计。一般先是做排序，然后分组去计算

- [ ] 计算`goods`表中不同`cat_id`的平均价格。

```mysql
select cat_id, avg(shop_price)
from goods
group by cat_id;
```

---

## having 筛选

> 掌握having 筛选和where 的不同：
>
> where是针对表，也就是存储在磁盘的数据，原来就有的数据发挥作用的。但是我们操作表之后生成的新的列，是存储在内存的，临时的。用where是不行的。having就是针对这种情况的，筛选处理产生的新的数据的。
>
> 注意：当一个变量很长的时候可以用 as 来另起一个名字。

- [ ] 查询`goods`表中市场价格比商店价格多200以上的商品。

```mysql
select goods_name, shop_price, market_price, (market_price - shop_price) as sheng 
from goods
having sheng > 200;
```

- [ ] 例子：

找出`score`表中2门及2门以上不及格者的平均成绩。

| name | subject | score |
| :--: | :-----: | :---: |
| 张三 |  数学   |  90   |
| 张三 |  语文   |  50   |
| 张三 |  地理   |  40   |
| 李四 |  语文   |  55   |
| 李四 |  政治   |  45   |
| 王五 |  政治   |  30   |

```mysql
select name, sum(score < 60) as gk, avg(score) as pj
from score
group by name
having gk >= 2;
```

---

## order by 排序

> 降序： desc
>
> 升序：asc（默认）
>
> 多列排序：直接在order by 后面加字段。

如果还要在按照一个列排序后的结果再按照另一个列排序。直接在后面加逗号写就行。

```mysql
select goods_name, cat_id, shop_price
from goods
order by cat_id asc,
shop_price desc;
```

---

## limit查询（限制取出条目）

> 一般与order by配合使用。
>
> 有俩个参数。第一个是要跳过的行数(如果取第一个的话，跳过的行数就是0，可以不写)，第二个是要取多少出来。

- 取出价格前三高的商品。

```mysql
select goods_name, shop_price, goods_id 
from goods
order by shop_price desc
limit 0,3;
```

- 取出最新的商品。good_id最大的那个。

    ```mysql
    select goods_id, goods_name 
    from goods
    order by goods_id desc
    limit 0,1;
    ```

---

## 子句的查询陷阱

> 之前学的 where ,group by , having, order by, limit 这五种查询，是必须按照这个顺序执行的，不能更换顺序。

查询最新的产品。

```mysql
select goods_id, goods_name from goods
where goods_id = (select max(goods_id) from goods);
```



---

### where型子查询

> 第一次查询的结果作为后一次的条件。
>
> select  *  from goods where  goods_id = ( select .... from goods);

 

---

### from型子查询

> 第一次查询的结果作为一个新表供别人查询。
>
> select   *   from ( select  goods_id ,goods_name from goods; ) 



---

### exists型子查询

查出所有有商品的栏目(cat_id)。

```mysql
select * from category 
where exists(select * from goods where goods.cat_id = category.cat_id);
```



---

## 连接

###  内连接查询（inner join）

> 连表查询。  table1 inner join table2  on expr; 结果形成一个新表。

两张表，`boy`	`girl`按照主键配对。

`boy`**表**：

| hid  | bname  |
| :--: | :----: |
|  A   |  屌丝  |
|  B   |  杨过  |
|  C   | 陈冠希 |

`girl`**表**：

| hid  | gname  |
| :--: | :----: |
|  B   | 小龙女 |
|  C   | 张柏芝 |
|  D   | 死宅女 |

```mysql
select boy.hid, boy.bname, girl.hid, girl.gname
from boy inner join girl
on 
boy.hie = girl.hie;
```

**结果：**

| hid  | bname  | hid  | gname  |
| :--: | :----: | :--: | :----: |
|  B   |  杨过  |  B   | 小龙女 |
|  C   | 陈冠希 |  C   | 张柏芝 |

---

### 左右连接查询（left join， right join）

> 左查询就是以左边的表为基准，所有的行都要存在，如果右边的表没有与之配对的就用`null`填充。右边的表有多余的就不要了。
>
> 右连接与左连接相反。

还是上面的表通过左右连接查看结果:

**左连接：**

```mysql
select boy.hid, boy.bname, girl.hid, girl.gname
from boy left join girl
on 
boy.hid = girl.hid;
```

**左连接结果：**


| hid  | bname  | hid  | gname  |
| :--: | :----: | :--: | :----: |
|  B   |  杨过  |  B   | 小龙女 |
|  C   | 陈冠希 |  C   | 张柏芝 |
|A|屌丝|NULL|NULL|

**右连接：**

```mysql
select boy.hid, boy.bname, girl.hid, girl.gname
from boy right join girl
on 
boy.hid = girl.hid;
```

**右连接结果：**


| hid  | bname  | hid  | gname  |
| :--: | :----: | :--: | :----: |
|  B   |  杨过  |  B   | 小龙女 |
|  C   | 陈冠希 |  C   | 张柏芝 |
|NULL|NULL|D|死宅女|

**例子：**

`m`和`t`两张表：

`m`**表**：

| mid  | hid  | gid  | mres |   matime   |
| :--: | :--: | :--: | :--: | :--------: |
|  1   |  1   |  2   | 2:0  | 2006-05-21 |
|  2   |  2   |  3   | 1:2  | 2006-06-21 |
|  3   |  3   |  1   | 2:5  | 2006-06-25 |
|  4   |  2   |  1   | 3:2  | 2006-07-21 |

`t`**表**：

| tid  |  tname   |
| :--: | :------: |
|  1   |   国安   |
|  2   |   申花   |
|  3   | 布尔联队 |

要求把比赛查询结果显示为如下形式：

| mid  | hteam | mres | gteam |   matime   |
| :--: | :---: | :--: | :---: | :--------: |
|  1   | 国安  | 2：0 | 申花  | 2006-05-21 |

`sql`代码如下：

```mysql
select mid, t1.tname as hteam, m.mres, t2.tname as gteam, m.matime
from m inner join t  as t1 on m.hid = t1.tid
inner join t as t2 on m.gid = t2.tid
order by mid;
```

**结果：**

| mid  |  hteam   | mres |  gteam   |   matime   |
| :--: | :------: | :--: | :------: | :--------: |
|  1   |   国安   | 2:0  |   申花   | 2006-05-21 |
|  2   |   申花   | 1:2  | 布尔联队 | 2006-06-21 |
|  3   | 布尔联队 | 2:5  |   国安   | 2006-06-25 |
|  4   |   申花   | 3:2  |   国安   | 2006-07-21 |

---

### union查询

> union查询就是把2条或者多条sql的查询结果，合并成一个结果集合。
>
> sql1:返回 N行
>
> sql2:返回M行
>
> union: 返回 N + M行。
>
> 也可以是从两张表返回再合并。
>
> 注意：唯一满足的条件是，两个返回的列数必须相同，名称可以不一致，使用第一条sql的名称为准。
>
> ​			sql1和sql2返回的结果完全相等的行将会被合并（具有去重的功能）比较耗时。
>
> ​			使用union all 可以避免合并。
>
> ​			union的前后子句里不用order by 因为没有意义，合并和的集合可以order by。

```mysql
select uid, name from user
union all
select id, name from tmp;
```

例子：

有两张表，求出`id`相同的`num`的和。

|  id  | num  |
| :--: | :--: |
|  a   |  5   |
|  b   |  10  |
|  c   |  15  |
|  d   |  10  |

|  id  | num  |
| :--: | :--: |
|  b   |  5   |
|  c   |  15  |
|  d   |  20  |
|  e   |  99  |

`sql`代码 如下（使用的是union, 也可以使用连接的方式）：

---

```mysql
select id,sum(num) from
(select * from a
union all
select * from b) as tmp
group by id;
```

---

# DDL操作

## 创建表 table

> 理解表与列的关系，掌握基本的建表语法
>
> 其实，建表的过程就是申明表头也就是列的过程。
>
> 什么类型的列？

## 列属性分类：

- 数值型：----> 整型，浮点型，定点型

- 字符串：----> char，varchar，text

- 时间和日期：-----> 2016-12-12，14:26:23

    

**基本语法：**

```mysql
create table tablename(
	列1 [列类型 列属性 默认值]，
    列2 [列类型 列属性 默认值]，
    ...........
)engine = 存储引擎
charset = 字符集;
```

**例如：**

```mysql
create table t1 
(
	sn int,
    name varchar(10)
);
```

---

### 整型列

> bigint（8字节）	int(4字节，1字节=8位)	mediumint（3字节）	smallint（2字节）	tinyint（1字节）

**可选参数**

- unsigned : 无符号，全为正。
- zerofill：用0填充,，对齐。适合用于固定宽度的，例如学号，编码等。
- M :  指定大小。设定之后就决定了为unsigned的了。只能配合zerofill使用。

```mysql
alter table tablename add 列名 tinyint(5) zerofill;
给表添加新的一列，并且为tinyint类型，用0填充，宽度为5。
```

---

### 浮点列与定点列

> float	double	decimal

**可选参数：**

- float(M，D)：M表示精度，总位数。D表示标度，小数点后面的位数。例如：float(5,2)  ----> 999.99
- decimal ：和float差不多，主要在于decimal是定点的，更精确，float/double都会出现精度损失。

---

### 字符型列

> char（定长，不管存的是多少，大小都是给定的大小）	
>
> varchar（可变长度，不能超过给定的大小，但是在存储小的时候，为了方便寻址，开头会给一个说明的字节，说明这个存储的长度有多长）	
>
> text （文本）	/ blob (存储大段二进制)
>
> enum (枚举型,，就是定义值只能在这个几个里面出现)  enum(‘男’， ‘女’)；insert into 时候只能是男和女。

---

### 日期时间类型

> year : 年。例如：1993
>
> date ： 日期。例如：1998-12-31 , insert into时候要加单引号。
>
> time ： 时间。 例如：13：56：23，insert into时候要加单引号。
>
> datetime ： 时期时间 。 例如：1998-12-31 13：56：23，insert into时候要加单引号。
>
> 时间戳 timestamp： 一般存注册时间，商品发布时间。

---

## 列的默认值

- null 查询不便利。
- null 的索引效率不高。要用`is null` 或者 `is not null`。

所以，使用中，避免列的值为null。声明方法为：

```mysql
not null default 默认值

create table A (id int not null default 0, name char(10) not null default '');
```

---

## 主键与自增

**主键：**`primary key`。此列不能重复，能够区分每一行。但是不重复的不一定就是主键。

主键一般与`auto_increment`配合使用。一个表中只能有一个自增列。

```mysql
两种创建方法：
create table B (id int primary key auto_increment, name char(2));

create table B (id int, name char(2), primary key(id));
```

---

## 列的删除与增加

**列的改动：**

```mysql
增加列：
alter table tablename add 列名 [属性 默认值];   //默认加在最后一列
alter table tablename add 列名 [属性 默认值] after 列名A;  //指定增加的列在列A的后面

删除列：
alter table tablename drop column 列名;

修改列的属性：
alter table tablename change 列名 [新的列名] 新的属性； //可以顺带修改列名
alter table tablename modify 列名 新属性；   //不能修改列名
```

---

## 视图（view）

> view又被成为虚拟表，view是sql的查询结果。

**view的作用：**

- 权限控制作用：相当于新建一个表，不包括原始表的某些列。
- 简化复杂查询 ： 把上次查询的结果保存为view，也就是一个新的表，下次可以直接用这表。

**视图能不能更新，删除，添加？**

如果视图的每一行是与物理表一一对应的就可以进行这些操作。而且是相互影响的，不管改哪一个，对应的view和物理表都会变化。

如果view的某一行是由物理表经过多行一起计算的结果（例如: 平均值）。view就不能更新。

**语法：**

```mysql
create view viewtablename as (select ............); //就把（）中操作的sql结果保存到了叫做														  viewtablename的视图里了
```

---

##  视图algorithm

> 试图放在哪 ? 内存？磁盘？ 

**两种算法：**

1. 对于简单的查询形成的view，再对view查询时候，如where，order等等，可以把`建视图语句``+查视图语句`--->合并成---->查物理的语句。这种视图的算法就是叫做merge 。简单理解就是，因为操作不复杂，每次存储的都是view的sql语句，用的时候在结合后面的sql语句。
2. 视图语句本身比较复杂的话，很难再和查询语句合并，mysql可先执行视图的创建语句，再把结果集形成内存中的临时表，然后再去查临时表。

```mysql
create algorithm=merge view viewtablename as (select ..........); //使用Merge
create algorithm=temptable view viewtablename as (select ......); //使用临时表
create view viewtablename as (select ........); //mysql自己选择
```

----

## 存储引擎的概念

|      特点      | myisam | innoDB | memory | BDB  | Archive |
| :------------: | :----: | :----: | :----: | :--: | :-----: |
| 批量插入的速度 |   高   |   低   |   高   |  高  | 非常高  |
|    事物安全    |        |  支持  |        | 支持 |         |
|    全文索引    |  支持  |        |        |      |         |
|     锁机制     |  表锁  |  行锁  |  表锁  | 页锁 |  行锁   |

一般默认都是innoDB，插入速度慢，但是有很好高的安全性。数据操作都有日志，而且支持行锁。

---

## 字符集，校对集与乱码问题

`set names gbk；`在`windows`窗口的时候。表明客户端，服务器端都是`gbk`编码。

网页和`linux`都是`utf8`编码。

校对集就是排序规则。按照`ascii`编码还是按字母顺序？

---

# DCL操作

## 索引的概念

索引是数据的目录，能快速定位行数据的位置，索引提高了查询速度，但是降低了增删改的速度。所以，索引并不是加的越多越好。一般在查询频率高的列上加，而且在重复度低的列上加效果好。

**索引类别：**

- key : 普通索引
- unique key :  唯一索引 （不允许重复）
- primary key : 主键索引
- fulltext : 全文索引 （中文环境下全文索引无效，要分词+索引，一般用第三方解决方案。）
- 多列索引：就是把2列或者多列的看作一个整体建立索引
- 冗余索引：

```mysql
create table C (
    id int primary key,			//创建一个id为主键的列
	name char(10),
    email char(20),
    key name(name), 			//创建了一个名字叫做name的以name列为主键的Key型索引
    unique key email(email（10）)     //创建了一个名字叫做email的以email列为主键的unique Key										  型索引，特别的：这email的前面都是一样的，所以取前10											个作为索引
);
```

**操作：**

```mysql
查看索引：
show index from tablename;

查看表创建过程:
show create table tablename;

删除索引:
alter table tablename drop index indexname;
drop index indexname on tablename;

添加索引：
alter table tablename add index/unique indexname(列名);

添加/删除主键索引：
alter table tablename add primary key(列名);
alter table tablename drop primary key；
```

---

## 常用函数

### 数学函数

```mysql
abs(x)   返回x的绝对值
bin(x)   返回x的二进制（oct返回八进制，hex返回十六进制）
ceiling(x)   返回大于x的最小整数值
exp(x)   返回值e（自然对数的底）的x次方
floor(x)   返回小于x的最大整数值
greatest(x1,x2,...,xn)返回集合中最大的值
least(x1,x2,...,xn)      返回集合中最小的值
ln(x)                    返回x的自然对数
log(x,y)返回x的以y为底的对数
mod(x,y)                 返回x/y的模（余数）
pi()返回pi的值（圆周率）
rand()返回０到１内的随机值,可以通过提供一个参数(种子)使rand()随机数生成器生成一个指定的值。
round(x,y)返回参数x的四舍五入的有y位小数的值
sign(x) 返回代表数字x的符号的值
sqrt(x) 返回一个数的平方根
truncate(x,y)            返回数字x截短为y位小数的结果
```



### 聚合函数

```mysql
avg(col)返回指定列的平均值
count(col)返回指定列中非null值的个数
min(col)返回指定列的最小值
max(col)返回指定列的最大值
sum(col)返回指定列的所有值之和
group_concat(col) 返回由属于一组的列值连接组合而成的结果
```



### 字符串函数

```mysql
ascii(char)返回字符的ascii码值
bit_length(str)返回字符串的比特长度
concat(s1,s2...,sn)将s1,s2...,sn连接成字符串
concat_ws(sep,s1,s2...,sn)将s1,s2...,sn连接成字符串，并用sep字符间隔
insert(str,x,y,instr) 将字符串str从第x位置开始，y个字符长的子串替换为字符串instr，返回结果
find_in_set(str,list)分析逗号分隔的list列表，如果发现str，返回str在list中的位置
lcase(str)或lower(str) 返回将字符串str中所有字符改变为小写后的结果
left(str,x)返回字符串str中最左边的x个字符
length(s)返回字符串str中的字符数
ltrim(str) 从字符串str中切掉开头的空格
position(substr,str) 返回子串substr在字符串str中第一次出现的位置
quote(str) 用反斜杠转义str中的单引号
repeat(str,srchstr,rplcstr)返回字符串str重复x次的结果
reverse(str) 返回颠倒字符串str的结果
right(str,x) 返回字符串str中最右边的x个字符
rtrim(str) 返回字符串str尾部的空格
strcmp(s1,s2)比较字符串s1和s2
trim(str)去除字符串首部和尾部的所有空格
ucase(str)或upper(str) 返回将字符串str中所有字符转变为大写后的结果
```



### 日期和时间函数

```mysql
curdate()或current_date() 返回当前的日期
curtime()或current_time() 返回当前的时间
date_add(date,interval int keyword)返回日期date加上间隔时间int的结果(int必须按照关键字进行格式化),如：selectdate_add(current_date,interval 6 month);
date_format(date,fmt)  依照指定的fmt格式格式化日期date值
date_sub(date,interval int keyword)返回日期date加上间隔时间int的结果(int必须按照关键字进行格式化),如：selectdate_sub(current_date,interval 6 month);
dayofweek(date)   返回date所代表的一星期中的第几天(1~7)
dayofmonth(date)  返回date是一个月的第几天(1~31)
dayofyear(date)   返回date是一年的第几天(1~366)
dayname(date)   返回date的星期名，如：select dayname(current_date);
from_unixtime(ts,fmt)  根据指定的fmt格式，格式化unix时间戳ts
hour(time)   返回time的小时值(0~23)
minute(time)   返回time的分钟值(0~59)
month(date)   返回date的月份值(1~12)
monthname(date)   返回date的月份名，如：select monthname(current_date);
now()    返回当前的日期和时间
quarter(date)   返回date在一年中的季度(1~4)，如select quarter(current_date);
week(date)   返回日期date为一年中第几周(0~53)
year(date)   返回日期date的年份(1000~9999)
一些示例：
获取当前系统时间：select from_unixtime(unix_timestamp());
select extract(year_month from current_date);
select extract(day_second from current_date);
select extract(hour_minute from current_date);
返回两个日期值之间的差值(月数)：select period_diff(200302,199802);
在mysql中计算年龄：
select date_format(from_days(to_days(now())-to_days(birthday)),'%y')+0 as age from employee;
这样，如果brithday是未来的年月日的话，计算结果为0。
下面的sql语句计算员工的绝对年龄，即当birthday是未来的日期时，将得到负值。
select date_format(now(), '%y') - date_format(birthday, '%y') -(date_format(now(), '00-%m-%d') <date_format(birthday, '00-%m-%d')) as age from employee
```



### 加密函数

```mysql
aes_encrypt(str,key)  返回用密钥key对字符串str利用高级加密标准算法加密后的结果，调用aes_encrypt的结果是一个二进制字符串，以blob类型存储
aes_decrypt(str,key)  返回用密钥key对字符串str利用高级加密标准算法解密后的结果
decode(str,key)   使用key作为密钥解密加密字符串str
encrypt(str,salt)   使用unixcrypt()函数，用关键词salt(一个可以惟一确定口令的字符串，就像钥匙一样)加密字符串str
encode(str,key)   使用key作为密钥加密字符串str，调用encode()的结果是一个二进制字符串，它以blob类型存储
md5()    计算字符串str的md5校验和
password(str)   返回字符串str的加密版本，这个加密过程是不可逆转的，和unix密码加密过程使用不同的算法。
sha()    计算字符串str的安全散列算法(sha)校验和
示例：
select encrypt('root','salt');
select encode('xufeng','key');
select decode(encode('xufeng','key'),'key');#加解密放在一起
select aes_encrypt('root','key');
select aes_decrypt(aes_encrypt('root','key'),'key');
select md5('123456');
select sha('123456');
```



### 控制流函数

```mysql
mysql有4个函数是用来进行条件操作的，这些函数可以实现sql的条件逻辑，允许开发者将一些应用程序业务逻辑转换到数据库后台。
mysql控制流函数：
case when[test1] then [result1]...else [default] end如果testn是真，则返回resultn，否则返回default
case [test] when[val1] then [result]...else [default]end  如果test和valn相等，则返回resultn，否则返回default
if(test,t,f)   如果test是真，返回t；否则返回f
ifnull(arg1,arg2) 如果arg1不是空，返回arg1，否则返回arg2
nullif(arg1,arg2) 如果arg1=arg2返回null；否则返回arg1
这些函数的第一个是ifnull()，它有两个参数，并且对第一个参数进行判断。如果第一个参数不是null，函数就会向调用者返回第一个参数；如果是null,将返回第二个参数。
如：select ifnull(1,2), ifnull(null,10),ifnull(4*null,'false');
nullif()函数将会检验提供的两个参数是否相等，如果相等，则返回null，如果不相等，就返回第一个参数。
如：select nullif(1,1),nullif('a','b'),nullif(2+3,4+1);
和许多脚本语言提供的if()函数一样，mysql的if()函数也可以建立一个简单的条件测试，这个函数有三个参数，第一个是要被判断的表达式，如果表达式为真，if()将会返回第二个参数，如果为假，if()将会返回第三个参数。
如：selectif(1<10,2,3),if(56>100,'true','false');
if()函数在只有两种可能结果时才适合使用。然而，在现实世界中，我们可能发现在条件测试中会需要多个分支。在这种情况下，mysql提供了case函数，它和php及perl语言的switch-case条件例程一样。
case函数的格式有些复杂，通常如下所示：
case [expression to be evaluated]
when [val 1] then [result 1]
when [val 2] then [result 2]
when [val 3] then [result 3]
......
when [val n] then [result n]
else [default result]
end
这里，第一个参数是要被判断的值或表达式，接下来的是一系列的when-then块，每一块的第一个参数指定要比较的值，如果为真，就返回结果。所有的when-then块将以else块结束，当end结束了所有外部的case块时，如果前面的每一个块都不匹配就会返回else块指定的默认结果。如果没有指定else块，而且所有的when-then比较都不是真，mysql将会返回null。
case函数还有另外一种句法，有时使用起来非常方便，如下：
case
when [conditional test 1] then [result 1]
when [conditional test 2] then [result 2]
else [default result]
end
这种条件下，返回的结果取决于相应的条件测试是否为真。
示例：
mysql>select case 'green'
     when 'red' then 'stop'
     when 'green' then 'go' end;
select case 9 when 1 then 'a' when 2 then 'b' else 'n/a' end;
select case when (2+2)=4 then 'ok' when(2+2)<>4 then 'not ok' end asstatus;
select name,if((isactive = 1),'已激活','未激活') as result fromuserlogininfo;
select fname,lname,(math+sci+lit) as total,
case when (math+sci+lit) < 50 then 'd'
when (math+sci+lit) between 50 and 150 then 'c'
when (math+sci+lit) between 151 and 250 then 'b'
else 'a' end
as grade from marks;
select if(encrypt('sue','ts')=upass,'allow','deny') as loginresultfrom users where uname = 'sue';#一个登陆验证

```



### 格式化函数

```mysql
date_format(date,fmt)  依照字符串fmt格式化日期date值
format(x,y)   把x格式化为以逗号隔开的数字序列，y是结果的小数位数
inet_aton(ip)   返回ip地址的数字表示
inet_ntoa(num)   返回数字所代表的ip地址
time_format(time,fmt)  依照字符串fmt格式化时间time值
其中最简单的是format()函数，它可以把大的数值格式化为以逗号间隔的易读的序列。
示例：
select format(34234.34323432,3);
select date_format(now(),'%w,%d %m %y %r');
select date_format(now(),'%y-%m-%d');
select date_format(19990330,'%y-%m-%d');
select date_format(now(),'%h:%i %p');
select inet_aton('10.122.89.47');
select inet_ntoa(175790383);
```



### 类型转换函数

```mysql
为了进行数据类型转化，mysql提供了cast()函数，它可以把一个值转化为指定的数据类型。类型有：binary,char,date,time,datetime,signed,unsigned
示例：
select cast(now() as signed integer),curdate()+0;
select 'f'=binary 'f','f'=cast('f' as binary);
```



### 系统信息函数

```mysql
database()   返回当前数据库名
benchmark(count,expr)  将表达式expr重复运行count次
connection_id()   返回当前客户的连接id
found_rows()   返回最后一个select查询进行检索的总行数
user()或system_user()  返回当前登陆用户名
version()   返回mysql服务器的版本
示例：
select database(),version(),user();
selectbenchmark(9999999,log(rand()*pi()));#该例中,mysql计算log(rand()*pi())表达式9999999次。
```





## 事务的概念

要在enginer为innoDB的模式下才行。

所谓的事务就是一整个事件的从开始到结束。例如：银行A汇款给B。A的金额先减少，然后B的金额在增加。这算一个事务。但是如果A减少以后出现了问题，B没增加，这就不安全。解决的方法就是在操作的时候启动一个事务。

**事务的特性：**

- 原子性(Atomicity)：原子意为最小的粒子，或者说不能再分的事物。数据库事务的不可再分的原则即为原子性。 

组成事务的所有查询必须：要么全部执行，要么全部取消（就像上面的银行例子）。

- 一致性(Consistency)：指数据的规则,在事务前/后应保持一致

- 隔离性(Isolation)：简单点说，某个事务的操作对其他事务不可见的.

- 持久性(Durability)：当事务完成后，其影响应该保留下来，不能撤消

```mysql
开启事务：
start transaction;
提交事务：
commit;
回滚：
rollback;

```

## 数据备份和恢复

```
就是输出重定向啊？？？

数据备份：

备份单独库下面的所有表的方法
mysqldump -uuname -ppasswd dbname > /dir/filename 

备份某一库下面的几个表的方法
mysqldump -uuname -ppasswd dbname table1 table2.. tableN  > 
/dir/filename 

备份多个库的方法
mysqldump -uname -ppasswd -B db1 db2 > /dir/filename 

备份所有库的方法 
mysqldump -uname -ppasswd -A > /dir/filename 


数据恢复：

1:在命令行操作
mysql -uuname -ppasswd [databaseName]< /dir/filename

2:登陆mysql后source操作
mysql> use dbname;
mysql> source /dir/filename;
```

---

## python连接mysql

```python
import pymysql
import pandas as pd
#--------------使用pymysql读取mysql数据-----------------------------
#连数据库
db = pymysql.connect(host = 'localhost', user = 'root', passwd = '483256', db='test')
#获取游标
cursor = db.cursor()
#用游标执行sql语句
cursor.execute('select * from goods;')
#获取执行完的结果
data_sql = cursor.fetchall()
data_sql


#------------使用pandas读取mysql数据-----------
sqlcmd = 'select * from goods;'
df = pd.read_sql(sqlcmd, db)
df



#------------通过pandas写入mysql数据--------------------
from sqlalchemy import create_engine
engine = create_engine('mysql+pymysql://root:483256@localhost:3306/test')
data2 = pd.DataFrame({'name':['wuchao','yami','heiehi'],'age':[20,21,23]})
data2.to_sql('pytest', engine)
```

---

## 练习笔记

使用distinct去重在数据量特别大的时候会效率很低。可以使用group by 分组去重。

WHERE语句在GROUP BY语句之前，SQL会在分组之前计算WHERE语句。 HAVING语句在GROUP BY语句之后，SQL会在分组之后计算HAVING语句。

















