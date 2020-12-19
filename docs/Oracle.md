# Oracle

------

[TOC]

## 1 .基础使用

**版本选择：**

9i  10i (Internet   网络)

**11g (grid  网格)**

12c（cloud  云计算）.....  19c

各个版本之间的使用是有细微的区别的，使用的时候要注意。

Oracle数据库是一种以后台服务进程的形式存在，不像桌面应用那样，双击打开使用。

后台服务能否正常启动使用：

计算机右键---》管理---》服务---》所有的Oracle服务

![](https://note.youdao.com/yws/api/personal/file/24DB37658C054771A3AF2D59DC3F3D1A?method=download&shareKey=e7f97a37dca7f3832ca5ed3a7d4822fd)

**主服务：**

OracleServiceORCL （orcl为数据库名）

使用时开启

真正处理我们的sql语句的进程。

**监听服务：**

Oralce TNSListerner

支持远程连接的进程，就相当于一个 “ 接线员 ”

数据库如果想要支持远程连接，必须开启这个服务。

远程：不同主机之间；不同的软件之间（jdbc、Navicat、plsql/developer等连接数据库，也是一种远程连接）；

## 2. 数据库的连接使用

监听服务一直在等待连接；主服务一直在等待执行指令。

通过数据库客户端软件，连接数据库服务，并向数据库服务发送指令，接收执行结果。

**安装客户端。**

SQLPlus，自带，没有图形界面，在dos界面操作。

Navicat，需要安装、破解、带图形界面

Developer，需要安装、破解、带图形界面

**连接数据库：**

安装数据库软件时（11g），自动的给我们创建一个数据库，并且数据库有默认的两个管理员账号：

sys 和 system；管理账号的密码，是在安装时设置的密码。

sys的用户名为：sys as sysdab

注：

- sys用数据库的超级用户，数据库内很多重要的东西（数据字典表、内置包、静态数据字典视图等）都属于这个用户，sys用户必须以sysdba身份登录。
- system是数据库内置的一个普通管理员，你手工创建的任何用户在被授予dba角色后都跟这个用户差不多。

![](https://note.youdao.com/yws/api/personal/file/56F575EAB92C414CA8A3DBD0B4A9EC02?method=download&shareKey=effaa60bf1dc7e08ef2a15b0e9d9cc29)

方式1：sqlplus sys/orcl123 as sysdba

如果账号是管理员账号，那么必须写上as sysdba ；否则不用写。

这是一种本地连接的方式，连接的就是本机数据库，**不依赖远程连接**。所以不需要启动TNS监听服务。

但是得启动主服务，否则 “主机都没了要连接哪里呢？！”

方式2：

sqlplus 用户名/密码@//ip地址:端口号/数据库名   （as sysdba）

```sql
sqlplus sys/orcl123@//localhost:1521/orcl as sysdba 
```

远程连接方式：

哪个主机的哪个端口的哪个数据库，用户名和密码是什么，这5个信息。

依赖监听服务的，如果我们的数据库支持这种方式连接，那么**必须开启监听服务**。

我们的Navicat客户端、JDBC都是使用的这种方式连接的数据库。

**监听的相关指令和文件：**

1、在控制台输入 lsnrctl status，可以查看当前监听服务的相关信息：

![](https://note.youdao.com/yws/api/personal/file/DC0971C540AF430D9C692FE681612D75?method=download&shareKey=11b6ad0f938aa8dbbe7cc67b05214c29)

2、 监听的配置文件

tnsnames.ora文件：

![](https://note.youdao.com/yws/api/personal/file/9EFA1B6C150242EABBF5D6205217CA7E?method=download&shareKey=b79bd80f0f021947af1f7477fea597b3)

![](https://note.youdao.com/yws/api/personal/file/2BF13BF5987C441D9C3ABB66DDCDB4F5?method=download&shareKey=4a433778ee25128b8eace26fc94148ee)

## 3.基础的管理操作

### 3.1 创建新用户并授权

不要使用sys系统管理员账号，不安全。而是需要额外的创建新用户，并授权。

```sql
create user 用户名 identified by 密码;
```

create user t514 identified by t123;

```sql
grant 权限 to 用户名
```

grant connect,resource to t514;

**注意：**

1. 创建用户和授权，需要管理员账号来操作，普通用户不行。
2. 新创建的用户不能直接使用，没有任何权限的
3. 通过grant语句进行授权，一般授予普通用户connect、resource两个权限即可。
4. connect：可以连接数据库；resource可以进行普通的表操作。

如果是12c版本的数据，创建的普通的用户，用户名需要以c##开头，例如c##test123

并且在用户授权时，需要额外授予一个unlimited tablespace权限。

新用户创建好之后，我们就可以使用该用户连接操作数据库。

### 3.2 创建数据库表

能够看懂并基本写出建表语句即可，在真正的项目中，可以选择图形界面方式。

```sql
create table 表名(
字段1 数据类型,
字段2 数据类型,
....
);
```

![](https://note.youdao.com/yws/api/personal/file/4B541B21C1BE48C1ADF573E0A76046C9?method=download&shareKey=869c15c2a83608ba8cf5d9413859fd2a)

数据类型有哪些：

**1 数字**

* int/integer：整数。非常大的整数。
* float:小数。
* number(p,s): 小数/整数
* number(5,3)： 5 最大5位数； 3 小数点后最多3位， 10000、100.2、12.356、999、1234.5；

**2 字符串**

* varchar2(最大长度)
* char(固定长度)

varchar2(50)：最大存储50个字符，例如存储的名字是 jack，那么不超过50个字符，可以存储，内容为“jack”

char(50)：固定存储50个字符串，例如存储的名字是 jack，内容为“jack(46个空格)”

一般表示字符串时使用varchar2，省空间。

**3 日期**

* date 日期    年月日 时分秒
* timestamp 时间戳  更精确，可以到毫秒级

date是比较推荐使用的。

**4 大文件**

直接将文本文件或二进制文件存储到数据库字段中。

使用记事本能正常打开阅读的，都是文本文件，.css .js .txt .html .java等，都是文本文件

使用记事本不能正常打开查看，这些文件通常不是让你直接阅读的，例如 .class .mp3 .mp4 .zip .doc .xls等

clob：批量的**字符**数据（文本）   例如，将红楼梦一本书存储到数据库的某个字段中

blob：批量的**字节**数据（非文本）   例如，将图片直接存储到数据库中

### 3.3 修改表结构

向stu学生表添加一个字段：手机号。

```sql
alter table stu add phone char(11);
```

类似的还有：

添加字段、修改字段类型、删除字段、字段重命名、修改表名等操作

*熟悉sql语句即可，如果有用到的场景，可以自行百度！*

### 3.4 一些问题的说明 

**问1：**如果使用账号1登录数据库，创建表1；那么接着使用账号2登录同一个数据库，能否看到刚才创建的表1？

（两个账号的权限都没问题）

答案是：Oracle数据库**不可以**，这也是Oracle与mysql在基础架构上的不同！！

Oracle：

- **用户是属于某个数据库**（不是数据库软件）的，在数据库中我们创建新用户。
- 一个数据库中可以创建多个用户，不同数据库之间用户是不通用的
- 每个用户都可以创建属于自己的表，用户之间的表不通用。

![](https://note.youdao.com/yws/api/personal/file/C6A4D2142A19469592EF30A343ED5C99?method=download&shareKey=e397153a6a5cdbf0631a98a556bc7390)

MySQL：

- **用户是独立于数据库的**，同一个用户可以关联操作多个数据库
- 用户1在某个数据库中创建表，其他用户也可以看到该表，因为他们访问的数据库是同一个。

![](https://note.youdao.com/yws/api/personal/file/6B3E156579DC472E949CD04A39F9DD3A?method=download&shareKey=6198c46d0c4a4a5639e43e62deee92a2)

**问2：**Oracle有如下易混淆的概念，明晰区分这些概念，否则再以后的项目中查阅资料时，你会非常迷茫。

**数据库软件：**

下载安装的那个数据软件包，11g，12c，oraclexe等 

**数据库服务：**

软件安装启动后，在后台运行的那些服务进程，如上面讲到的两个比较重要的服务，主服务和监听服务。 

**数据库：**

存放数据的那些静态文件。

数据库实例：

**问3：Oracle数据库是否区分大小写？**

区分大小写，前提是得有双引号包括；否则，sql语句的内容会自动 转换为大写来执行

Oracle数据库中的单双引号的问题？

双引号包括的是表名、字段名等这些信息，用来区分大小写。

单引号包括的是值，字符串值。

**Mysql数据库是否区分大小写?**

大小写不敏感；单双引号通用，均表示字符串值。注意：

windows就是上面描述的那样；但是有些linux环境下，大小写默认是敏感的；

其实在mysql中，我们可以通过配置设置是否大小写敏感。