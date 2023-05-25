# 数据库

------

[TOC]

### 关系型数据库和非关系型数据库

**DML：数据操作语言**

insert,delete,update,select,add

**DDL：数据定义语言**

alert,create,table,drop,create,database,drop,database

**关系型数据库：**

mysql,sql-server,oracle,db2,sybase,informix,access 

**非关系型数据库：**

mongoDB,redis,hbase,memcache

**区别：**

* 存储的数据类型不同
* 数据表的结构不同
* 数据容量不同
* 数据访问效率不同

|              数据库类型              |                             特性                             |                             优点                             |                             缺点                             |
| :----------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|  关系型数据库 SQLite、Oracle、mysql  | 1、关系型数据库，是指采用了关系模型来组织 数据的数据库； 2、关系型数据库的最大特点就是事务的一致性； 3、简单来说，关系模型指的就是二维表格模型， 而一个关系型数据库就是由二维表及其之间的联系所组成的一个数据组织。 | 1、容易理解：二维表结构是非常贴近逻辑世界一个概念，关系模型相对网状、层次等其他模型来说更容易理解； 2、使用方便：通用的SQL语言使得操作关系型数据库非常方便； 3、易于维护：丰富的完整性(实体完整性、参照完整性和用户定义的完整性)大大减低了数据冗余和数据不一致的概率； 4、支持SQL，可用于复杂的查询。 | 1、为了维护一致性所付出的巨大代价就是其读写性能比较差； 2、固定的表结构； 3、高并发读写需求； 4、海量数据的高效率读写； |
| 非关系型数据库 MongoDb、redis、HBase | 1、使用键值对存储数据； 2、分布式； 3、一般不支持ACID特性； 4、非关系型数据库严格上不是一种数据库，应该是一种数据结构化存储方法的集合。 | 1、无需经过sql层的解析，读写性能很高； 2、基于键值对，数据没有耦合性，容易扩展； 3、存储数据的格式：nosql的存储格式是key,value形式、文档形式、图片形式等等，文档形式、图片形式等等，而关系型数据库则只支持基础类型。 | 1、不提供sql支持，学习和使用成本较高； 2、无事务处理，附加功能bi和报表等支持也不好； |

### 数据库连接池

如果一个项目中如果需要多个连接，如果一直获取连接，断开连接，这样比较浪费资源；如果创建一个池，用池来管理Connection，这样就可以重复使用Connection。有了池我们就不用自己来创建Connection，而是通过池来获取Connection对象。当使用完Connection后，调用Connection的close（）方法也不会真的关闭Connection，而是把Connection“归还”给池。池就可以再利用这个Connection对象了。这里我们常用的连接池有三种，分别是：DBCP连接池、C3P0连接池和Druid连接池；

DBCP：Tomcat的数据源使用的就是DBCP，单线程，并发量低，性能不好，适用于小型系统。

C3P0：开放的源代码的JDBC连接池，单线程，性能较差，适用于小型系统

Druid(德鲁伊)：Druid能够提供强大的监控和扩展功能，是一个可用于大数据实时查询和分析的高容错、高性能的开源分布式系统，尤其是当发生代码部署、机器故障以及其他产品系统遇到宕机等情况时，Druid仍能够保持100%正常运行

Hikari：HiKariCP是数据库连接池的一个后起之秀，号称性能最好，可以完美地PK掉其他连接池，是一个高性能的JDBC连接池，基于BoneCP做了不少的改进和优化。并且在**springboot2.0之后，采用的默认数据库连接池就是Hikari**

### SQL语句

<font color="lighblue">创建表</font>

* PRIMARY KEY 约束唯一标识数据库表中的每条记录。
* 主键必须包含唯一的值。
* 主键列不能包含 NULL 值。
* 每个表都应该有一个主键，并且每个表只能有一个主键。

```sql
create table student
(
         s_id int(4) not null PRIMARY KEY auto_increment,
		s_name varchar(12),
		s_age int(4),
		s_sex varchar(2),
		s_date date,
         FOREIGN KEY (Id_P) REFERENCES Persons(Id_P)
);
```

<font color="lighblue">INSERT INTO</font> 语句用于向表格中<font color="lighblue">插入新的行</font>。

```sql
insert into Persons values ('Wilson', 'Champs-Elysees');
INSERT INTO Persons (LastName, Address) VALUES ('Wilson', 'Champs-Elysees');
```

<font color="lighblue">Update</font> 语句用于<font color="lighblue">修改</font>表中的数据。

```sql
UPDATE Person SET Address = 'Zhongshan 23', City = 'Nanjing' WHERE LastName = 'Wilson';
```

<font color="lighblue">DELETE</font> 语句用于<font color="lighblue">删除</font>表中的行。

```sql
DELETE FROM Person WHERE LastName = 'Wilson' ;
```

<font color="lighblue">查询</font>语句

```sql
select * from student s,score c;
select s2.s_name,count(*) as num from score s1 INNER JOIN student s2 on s1.s_no = s2.s_id GROUP BY s2.s_name;--分组查询
```

<font color="lighblue">ORDER BY</font> 语句用于对结果集进行<font color="lighblue">排序</font>。

```sql
SELECT Company, OrderNumber FROM Orders ORDER BY OrderNumber --顺序显示，小->大
SELECT Company, OrderNumber FROM Orders ORDER BY OrderNumber DESC --逆序显示，大->小
-- MySQL中按照IN语句中的顺序排序,ORDER BY 后面使用关键字 FIELD ，紧接着的是需要排序的字段名称goods_id
SELECT * FROM goods WHERE goods_id IN ( '603', '64', '566', '37', '421', '201', '1002', '339', '283', '494', '222', '617' ) ORDER BY FIELD( goods_id, '603', '64', '566', '37', '421', '201', '1002', '339', '283', '494', '222', '617' )
```

<font color="lighblue">LIKE 操作符</font>用于在 WHERE 子句中搜索列中的指定模式。

SQL通配符必须与LIKE运算符一起使用：

|           通配符           |            描述            |
| :------------------------: | :------------------------: |
|             %              |     替代一个或多个字符     |
|             _              |       仅替代一个字符       |
|         [charlist]         |   字符列中的任何单一字符   |
| [^charlist]或者[!charlist] | 不在字符列中的任何单一字符 |

```sql
-- "Persons" 表中选取居住在以 "N" 开始的城市里的人
SELECT * FROM Persons WHERE City LIKE 'N%';
-- 从 "Persons" 表中选取居住在不包含 "lon" 的城市里的人
SELECT * FROM Persons WHERE City NOT LIKE '%lon%';
-- "Persons" 表中选取名字的第一个字符之后是 "eorge" 的人
SELECT * FROM Persons WHERE FirstName LIKE '_eorge';
--"Persons" 表中选取居住的城市以 "A" 或 "L" 或 "N" 开头的人
SELECT * FROM Persons WHERE City LIKE '[ALN]%';
--"Persons" 表中选取居住的城市不以 "A" 或 "L" 或 "N" 开头的人
SELECT * FROM Persons WHERE City LIKE '[!ALN]%';

--在Mybatis中使用模糊查询
SELECT * FROM Persons WHERE goodsName like concat('%',#{goodsName},'%');
```

<font color="lighblue">IN 操作符</font>允许我们在 WHERE 子句中<font color="lighblue">规定多个值</font>。

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1,value2,...)
```

<font color="lighblue">SQL UNIQUE 约束</font>

* UNIQUE 约束唯一标识数据库表中的每条记录。
* UNIQUE 和 PRIMARY KEY 约束均为列或列集合提供了唯一性的保证。
* PRIMARY KEY 拥有自动定义的 UNIQUE 约束。

每个表可以有多个 UNIQUE 约束，但是每个表只能有一个 PRIMARY KEY 约束。

操作符 <font color="lighblue">BETWEEN ... AND</font> 会选取介于两个值之间的<font color="lighblue">数据范围</font>。这些值可以是数值、文本或者日期。(不同的数据库对 BETWEEN...AND 操作符的处理方式是有差异的。

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name
BETWEEN value1 AND value2
```

通过使用 SQL，可以为列名称和表名称<font color="lighblue">指定别名</font>（AS）。

```sql
SELECT column_name AS name FROM table_name
SELECT LastName AS Family, FirstName AS Name FROM Persons
```

<font color="lighblue">SQL join</font> 用于根据<font color="lighblue">两个或多个表</font>中的列之间的关系，从这些表中查询数据。

- <font color="lighblue">JOIN</font>: 如果表中有至少一个匹配，则返回行，INNER JOIN 与 JOIN 是相同的。
- <font color="lighblue">INNER JOIN</font>:只返回两个表中联结字段相等的行
- <font color="lighblue">LEFT JOIN</font>: 即使右表中没有匹配，也从左表返回所有的行
- <font color="lighblue">RIGHT JOIN</font>: 即使左表中没有匹配，也从右表返回所有的行

```sql
select * from student s,score c where s.s_id = c.c_id;
select * from student s INNER JOIN score c on s.s_id = c.s_no; -- 内连接
select * from student s LEFT JOIN score c on s.s_id = c.s_no; -- 左连接
select * from student s RIGHT JOIN score c on s.s_id = c.s_no; -- 右连接
```

<font color="lighblue">CREATE INDEX</font> 语句用于在表中<font color="lighblue">创建索引</font>。

在不读取整个表的情况下，索引使数据库应用程序可以更快地查找数据。

```sql
CREATE INDEX PersonIndex ON Person (LastName);
CREATE INDEX PersonIndex ON Person (LastName, FirstName);--列出两列索引
```

可以使用 <font color="lighblue">DROP INDEX </font>命令<font color="lighblue">删除表格中的索引</font>。

```sql
ALTER TABLE table_name DROP INDEX index_name; --MySQL
```

<font color="lighblue">DROP DATABASE</font> 语句用于<font color="lighblue">删除数据库</font>：

```sql
DROP DATABASE 数据库名称; --删除数据库：
DROP TABLE 表名称; --删除表（表的结构、属性以及索引也会被删除）
TRUNCATE TABLE 表名称; --除去表内的数据，但并不删除表本身
```

<font color="lighblue">ALTER TABLE</font> 语句用于在已有的表中<font color="lighblue">添加、修改或删除列</font>。

```sql
ALTER TABLE table_name ADD column_name datatype;--添加列
ALTER TABLE table_name DROP COLUMN column_name;--删除列
```

在 SQL 中，视图是基于 SQL 语句的结果集的可视化的表。

```sql
CREATE VIEW view_name AS
SELECT column_name(s)
FROM table_name
WHERE condition
```

MySQL 使用下列数据类型在数据库中存储日期或日期/时间值：

- DATE - 格式 YYYY-MM-DD
- DATETIME - 格式: YYYY-MM-DD HH:MM:SS
- TIMESTAMP - 格式: YYYY-MM-DD HH:MM:SS
- YEAR - 格式 YYYY 或 YY

### SQL函数

<font color="lighblue">AVG()</font> 函数返回数值列的<font color="lighblue">平均值</font>。NULL 值不包括在计算中。

```sql
SELECT AVG(column_name) FROM table_name;
SELECT AVG(OrderPrice) AS OrderAverage FROM Orders;--计算 "OrderPrice" 字段的平均值。
SELECT Customer FROM Orders WHERE OrderPrice > (SELECT AVG(OrderPrice) FROM Orders);--找到 OrderPrice 值高于 OrderPrice 平均值的客户。
```

<font color="lighblue">COUNT()</font> 函数返回匹配指定条件的<font color="lighblue">行数</font>。

```sql
SELECT COUNT(column_name) FROM table_name;--COUNT(column_name) 函数返回指定列的值的数目。
SELECT COUNT(Customer) AS CustomerNilsen FROM Orders WHERE Customer='Carter';--计算客户 "Carter" 的订单数。
```

<font color="lighblue">FIRST() </font>函数返回指定的字段中<font color="lighblue">第一个记录的值</font>。

```sql
SELECT FIRST(column_name) FROM table_name;
SELECT FIRST(OrderPrice) AS FirstOrderPrice FROM Orders;--查找 "OrderPrice" 列的第一个值。
```

<font color="lighblue">LAST() </font>函数返回指定的字段中<font color="lighblue">最后一个记录的值</font>。

```sql
SELECT LAST(column_name) FROM table_name;
SELECT LAST(OrderPrice) AS LastOrderPrice FROM Orders;--查找 "OrderPrice" 列的最后一个值
```

<font color="lighblue">MAX() </font>函数返回指定列的<font color="lighblue">最大值</font>。

```sql
SELECT MAX(column_name) FROM table_name;
SELECT MAX(alexa) AS max_alexa FROM Websites;--从 "Websites" 表的 "alexa" 列获取最大值
```

<font color="lighblue">MIN() </font>函数返回指定列的<font color="lighblue">最小值</font>。

```sql
SELECT MIN(column_name) FROM table_name;
SELECT MIN(alexa) AS min_alexa FROM Websites;--从 "Websites" 表的 "alexa" 列获取最小值
```

<font color="lighblue">SUM() </font>函数返回数值<font color="lighblue">列的总数</font>。

```sql
SELECT SUM(column_name) FROM table_name;
SELECT SUM(count) AS nums FROM access_log;--查找 "access_log" 表的 "count" 字段的总数
```

<font color="lighblue">GROUP BY</font> 语句可结合一些聚合函数来使用,根据一个或多个列对结果集进行<font color="lighblue">分组</font>:

```sql
SELECT site_id, SUM(access_log.count) AS nums FROM access_log GROUP BY site_id;--统计 access_log 各个 site_id 的访问量
```

WHERE 关键字无法与聚合函数一起使用,<font color="lighblue">HAVING </font>子句可以让我们筛选分组后的各组数据。

```sql
SELECT Websites.name, Websites.url, SUM(access_log.count) AS nums FROM (access_log
INNER JOIN Websites
ON access_log.site_id=Websites.id)
GROUP BY Websites.name
HAVING SUM(access_log.count) > 200;
--查找总访问量大于 200 的网站
```

<font color="lighblue">LEN</font> 函数返回文本<font color="lighblue">字段中值的长度</font>。

```sql
SELECT LEN(column_name) FROM table_name;
SELECT LEN(City) as LengthOfCity FROM Persons;--取得 "City" 列中值的长度
```

<font color="lighblue">Replace()</font>函数替换某个字段中某段字符串。

```sql
--在字符串 store_id 中所有出现的字符串 179e58f58d9543 均被 155666666替换，然后返回这个字符串：
UPDATE `tata_users` set store_id = REPLACE(store_id,'179e58f58d9543','155666666');
```

<font color="lighblue">find_in_set()</font>函数查询以逗号分割的字符串中的某一字符串。

```sql
--CITY_CODE:2145,233,6888
--CITY_CODE:2345,23343,684233
select CITY_CODE from user where FIND_IN_SET('233',CITY_CODE); 
--查询结果:CITY_CODE:2145,233,6888
```

<font color="lighblue">case when</font>函数，改变查询结果

```sql
CASE WHEN sex = '1' THEN '男'
     WHEN sex = '2' THEN '女'
ELSE '其他' END   
```

在when后面可以跟多个表达式

```sql
CASE WHEN sex = '1' and name ='Java3y' THEN '男'
         WHEN sex = '2' and name ='Java4y' THEN '女'
ELSE '其他' END 
```

复习SQL语句：

```sql
CREATE TABLE Student 
(
     id INT(4) NOT NULL PRIMARY KEY auto_increment,
	 name VARCHAR(50) NOT NULL,
	 sex VARCHAR(50) NOT NULL,
	 age INT(4) NOT NULL,
	 grade INT(5) NOT NULL,
	 birthday DATE
);

INSERT INTO student(name,sex,age,grade,birthday) VALUES ("王五","男",22,84,"1997-01-01");
INSERT INTO student(name,sex,age,grade,birthday) VALUES ("张三","男",24,54,"1997-05-21");

UPDATE student SET name="李四",sex="女" where id=2;

SELECT * from student where age=22;

DELETE from student where id=2;

ALTER TABLE student ADD phone VARCHAR(10);

ALTER TABLE student ALTER sex VARCHAR(55);

-- 添加索引
CREATE UNIQUE INDEX Sid ON student(id);
CREATE INDEX Sgrade ON student(grade DESC,age); 

-- 删除索引
ALTER TABLE student DROP INDEX id;

-- 用as起别名
select Sno,Sname,2020-Sage as Date,Sdept from student;
-- 消除取消重复的行
select DISTINCT Ssex FROM student;
-- 使用where子句
select * from student where Sdept = '软件学院';
-- 使用between子句
select * from student where Sage between 20 and 22;
select * from student where Sage not between 20 and 22;
-- 使用in字句
select Sno,Sname,Sage from student where Sdept in ('软件学院','马克思学院');
select Sno,Sname,Sage from student where Sdept not in ('软件学院','马克思学院');
-- 模糊查询
select * from student where Sname like '张%';
select * from student where Sname like '李_';
select * from student where Sname like '%三%';
-- 排序
select * from sc order by Grade;
select * from sc order by Grade DESC;
select * from sc order by Cno,Grade DESC;
-- 聚集函数
select Sno,avg(grade) as 平均分 from sc where Sno="171303";
select count(distinct Sno) as 选修学生总数 from sc; 
select max(Grade) as 最高分,min(Grade)as 最低分 from sc;
select sum(Grade) as 总分 from sc;
-- 分组查询
select Sdept,count(*) as 人数 from student group by Sdept;
select Sno,avg(Grade) as 均分 from sc group by Sno having avg(Grade)>60;
-- 等值与非等值查询
select student.*,sc.Grade,sc.Cno from student,sc where student.Sno=sc.Sno;
-- 自身连接查询
select a.Sno,a.Cno,b.Grade from sc a, sc b where a.Grade=b.Grade and a.Cno!=b.Cno;
-- 外连接查询
select student.*,sc.Cno,sc.Grade from student left join sc on student.Sno=sc.Sno;
select student.*,sc.Cno,sc.Grade from student right join sc on student.Sno=sc.Sno;
-- 嵌套查询
select * from student where Sno in (select Sno from sc where Cno="001");
-- 视图
create view J_Student as select student.*,sc.Grade,sc.Cno from student,sc where student.Sno=sc.Sno;
select * from J_Student where Grade>70;
update J_Student set Sage=24 where Grade=78;
drop view J_Student;
```

#### 按12个月统计,没有的月份补齐

```sql
select a.date,ifnull(b.num,0) as num from 
(SELECT CONCAT(#{year},'-01') AS date UNION
SELECT CONCAT(#{year},'-02') AS date UNION
SELECT CONCAT(#{year},'-03') AS date UNION
SELECT CONCAT(#{year},'-04') AS date UNION
SELECT CONCAT(#{year},'-05') AS date UNION
SELECT CONCAT(#{year},'-06') AS date UNION
SELECT CONCAT(#{year},'-07') AS date UNION
SELECT CONCAT(#{year},'-08') AS date UNION
SELECT CONCAT(#{year},'-09') AS date UNION
SELECT CONCAT(#{year},'-10') AS date UNION
SELECT CONCAT(#{year},'-11') AS date UNION
SELECT CONCAT(#{year},'-12') AS date
)a
left join
(select count(store_name) num,DATE_FORMAT(create_dt,'%Y-%m') as date from t_ord_cfm_repair where repair_state >=1200 and del_fg=0 GROUP BY date) b 
on a.date = b.date order by date;
-- DATE_FORMAT() 函数用于以不同的格式显示日期/时间数据。
-- CONCAT() 返回结果为连接参数产生的字符串，如果有任何一个参数为null，则返回值为null。
-- IFNULL() 函数用于判断第一个表达式是否为 NULL，如果为 NULL 则返回第二个参数的值，如果不为 NULL 则返回第一个参数的值。
```

![](https://note.youdao.com/yws/api/personal/file/53A206E219B74DECB554A9D8588FF6DC?method=download&shareKey=ca7da52e9589a4994bb5ef9bfc850ca3)

#### concat函数

> https://blog.csdn.net/weixin_45583894/article/details/123379603
>

**concat()函数**

1、功能：将多个字符串连接成一个字符串。

2、语法：concat(str1, str2,...)

说明：返回结果为连接参数产生的字符串，如果有任何一个参数为null，则返回值为null。

3、举例：select concat (id, name, score) as 别名 from 表名；

**concat_ws()函数**

1、功能：和concat()一样，但是可以指定分隔符（concat_ws就是concat with separator）

2、语法：concat_ws(separator, str1, str2, ...)

说明：第一个参数指定分隔符。需要注意的是分隔符不能为null，如果为null，则返回结果为null。

3、举例：select concat ('#',id, name, score) as 别名 from 表名；

**group_concat()函数**

1、功能：将group by产生的同一个分组中的值连接起来，返回一个字符串结果。

2、语法：group_concat( [distinct] 要连接的字段 [order by 排序字段 asc/desc ] [separator] )

说明：通过使用distinct可以**排除重复值**；如果希望对结果中的值进行**排序**，可以使用order by子句；separator**分隔符**是一个字符串值，缺省为一个逗号。

```sql
SELECT 
	name,
	group_concat( score ORDER BY score DESC separator '#' ) AS score 
FROM
	tk_student 
GROUP BY
	name;
```

**substring_index函数**

substring_index(字符串,分隔符,序号)，截取目标字符串

* string：用于截取目标字符串的字符串。可为字段，表达式等。
* sep：分隔符，string存在且用于分割的字符，比如“，”、“.”等。
* num：序号，为非0整数。若为整数则表示从左到右数，若为负数则从右到左数。

```sql
-- 分组后取每组的最大两条值，若求这两条值的和，可导出后按","分列求和后粘贴为数值
SELECT
	student_id,
	substring_index( GROUP_CONCAT( total_score ORDER BY total_score DESC ), ',', 2 ) as score
FROM
	score 
GROUP BY
	teacher_id;
```

**concat_ws()和group_concat()联合使用**

题目：查询以name分组的所有组的id和score

举例：select name,group_concat(concat_ws('-',id,score) order by id) as 别名 from 表名 group by name；

####  TO_DAYS(date)函数

> https://blog.csdn.net/sinat_37239798/article/details/115161583

```SQL
-- 利用to_days函数查询今天的数据
select * from 表名 where to_days(时间字段名) = to_days(now());
select * from seller where to_days(create_time) = to_days(now());
```

#### 更新查询结果

```sql
UPDATE lesson_claim_copy1 lcl inner join (
	SELECT
	lcl.lesson_id as lesson_id,
	GROUP_CONCAT(experience.description separator ';') AS description
FROM
	lesson_claim_copy1 lcl
	JOIN lesson_check checks ON lcl.lesson_id = checks.lesson_id
	JOIN com_learning_experience experience ON checks.id = experience.object_id
	JOIN learning_experience_teacher teacher ON experience.id = teacher.learing_experience_id 
WHERE
	lcl.teacher_id = teacher.teacher_id and experience.description is not null group by checks.lesson_id) as table1
	on lcl.lesson_id = table1.lesson_id
	SET lcl.description = table1.description;
```

#### SQL语句中1=1的作用

**1=1的用处** 

where 1=1 是为了避免where 关键字后面的第一个词直接就是 “and”而导致语法错误。

动态SQL中连接AND条件

where 1=1 是为了避免where 关键字后面的第一个词直接就是 “and”而导致语法错误。

因为table中根本就没有名称为1的字段，所以该SQL等效于select * from table，

这个SQL语句很明显是全表扫描，需要大量的IO操作，数据量越大越慢，建议查询时增加必输项，即where 1=1后面追加一些常用的必选条件，并且将这些必选条件建立适当的索引，效率会大大提高。

where后面总要有语句，加上了1=1后就可以保证语法不会出错!

可以避免如果不需要where条件时，你还要判断需不需要写where关键字

#### UNION和UNION ALL的区别

UNION和UNION ALL的区别是,UNION会自动压缩多个结果集合中的重复结果，而UNION ALL则将所有的结果全部显示出来，不管是不是重复。
**Union**：对两个结果集进行并集操作，不包括重复行，同时进行默认规则的排序；
UNION在进行表链接后会筛选掉重复的记录，所以在表链接后会对所产生的结果集进行排序运算，删除重复的记录再返回结果。

**Union All**：对两个结果集进行并集操作，包括重复行，不进行排序；
如果返回的两个结果集中有重复的数据，那么返回的结果集就会包含重复的数据了。

注意：字段名称可以不同，字段数量、数据类型、顺序必须相同。UNION 操作符用于合并两个或多个 SELECT 语句的结果集。

```sql
select *  from test  where id<4  
union  
select *  from student  where id>2 and id<6 
```

### (@i:=@i+1)的使用

1.查询sql时，有时需要一个伪列rownum。在mysql中便可以使用(@i:=@i+1)生成自增的序列

```SQL
-- 从0开始，每次都增加1
SELECT (@i:=@i+1) rownum,first_name,last_name FROM employee,(SELECT (@i:=0)) t
```

```SQL
-- 如果想要加5，便改为(@i:=@i+5).如果想要从100开始，便将(@i:=0)改为(@i:=100)
SELECT (@i:=@i+5) rownum,first_name,last_name FROM employee,(SELECT (@i:=100)) t
```

### mysql有哪些数据类型

1、整数类型，包括TINYINT、SMALLINT、MEDIUMINT、INT、 BIGINT，分别表示1字节、2字节、3字节、4字节、8字节整数。任何整数类型都可以加上UNSIGNED属性，表示数据是无符号的，即非负整数。 

2、实数类型，包括FLOAT、DOUBLE、DECIMAL。 

DECIMAL可以用于存储比BIGINT还大的整型，能存储精确的小数。 

而FLOAT和DOUBLE是有取值范围的，并支持使用标准的浮点进行近似计算。 计算时FLOAT和DOUBLE相比DECIMAL效率更高一些，DECIMAL可以理解成是用字符串进行处理。 

3、字符串类型，包括VARCHAR、CHAR、TEXT、BLOB 

VARCHAR用于存储可变长字符串，它比定长类型更节省空间。 

VARCHAR使用额外1或2个字节存储字符串长度。列长度小于255字节时，使用1字节表示，否则使用2字节表示。 

VARCHAR存储的内容超出设置的长度时，内容会被截断。 

CHAR是定长的，根据定义的字符串长度分配足够的空间。 

CHAR会根据需要使用空格进行填充方便比较。 

CHAR适合存储很短的字符串，或者所有值都接近同一个长度。 

CHAR存储的内容超出设置的长度时，内容同样会被截断。 

**使用策略：** 

对于经常变更的数据来说，CHAR比VARCHAR更好，因为CHAR不容易产生碎片。

对于非常短的列，CHAR比VARCHAR在存储空间上更有效率。 

使用时要注意只分配需要的空间，更长的列排序时会消耗更多内存。尽量避免使用TEXT/BLOB类型，查询时会使用临时表，导致严重的性能开销。 

4、枚举类型（ENUM），把不重复的数据存储为一个预定义的集合。 

ENUM存储非常紧凑，会把列表值压缩到一个或两个字节。 

ENUM在内部存储时，其实存的是整数。 

尽量避免使用数字作为ENUM枚举的常量，因为容易混乱。 

5、日期和时间类型，尽量使用timestamp，空间效率高于datetime用整数保存时间戳通常不方便处理。

**注意**：

- 避免使用select *
- count(1)或count(列) 代替 count(*)
- 创建表时尽量用 char 代替 varchar
- 表的字段顺序固定长度的字段优先
- 组合索引代替多个单列索引（经常使用多个条件查询时）
- 尽量使用短索引
- 使用连接（JOIN）来代替子查询(Sub-Queries)
- 连表时注意条件类型需一致
- 索引散列值（重复少）不适合建索引，例：性别不适合

### 三范式

> https://www.cnblogs.com/xiaozengzeng/p/10720226.html

1NF:字段不可分;

2NF:有主键，非主键字段依赖主键;

3NF:非主键字段不能相互依赖;

**解释:**

1NF:原子性 字段不可再分,否则就不是关系数据库;

2NF:唯一性 一个表只说明一个事物;

3NF:每列都与主键有直接关系，不存在传递依赖;

#### 查询死锁

```sql
-- 查看当前事务
SELECT * FROM INFORMATION_SCHEMA.INNODB_TRX;
-- 查看当前锁定的事务
SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCKS;
-- 查看当前等锁的事务
SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCK_WAITS;
```

[mysql innodb_locks](https://blog.csdn.net/sugarCYF/article/details/108433259)

