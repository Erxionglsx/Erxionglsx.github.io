# Redis数据库

------

[TOC]

### Redis简介

非关系型数据库严格上不是一种数据库，应该是一种数据结构化存储方法的集合。

Redis是**基于内存**，常用作于**缓存**的一种技术，并且Redis存储的方式是以`key-value`的形式。默认有16个库，编号从0开始，0-15，默认使用0号库，库与库之间相互隔离

切换库的命令： select dbid(库编号)  eg：select 1

**Redis作为一个高性能的key-value数据库具有以下特征：**

- 多样的数据模型
- 持久化
- 主从同步

**Redis是单进程单线程的**

![](https://note.youdao.com/yws/api/personal/file/556EE7CCE43743EF9D77EADEE3913F2A?method=download&shareKey=fa356fe7b337b20983518a97bb1a10f7)

#### 数据类型

- <font color="lighblue">string(字符串)</font>-->简单的`key-value`，他能够存储任何类型的字符串，底层实现有点类似于 Java 中的 **ArrayList**。
- <font color="lighblue">list(链表)</font>-->有序链表(底层是双向链表)-->可做简单队列，可以支持反向查找和遍历，列表相当于 Java 语言中的 **LinkedList**，注意它是链表而不是数组。list 的插入和删除操作非常快，时间复杂度为 O(1)，但是索引定位很慢，时间复杂度为 O(n)。
- <font color="lighblue">hash(字典)</font>-->哈希表-->存储结构化数据，字典相当于Java中的 **HashMap**，"数组 + 链表"的链地址法来解决部分 **哈希冲突**。
- <font color="lighblue">set(集合)</font>-->无序列表(**去重**)-->提供一系列的交集、并集、差集的命令，集合相当于 Java 语言中的 **HashSet**，它内部的键值对是无序、唯一的。
- <font color="lighblue">zset(有序集合)</font>-->**有序**集合映射，类似于 Java 中 **SortedSet** 和 **HashMap** 的结合体，它是一个 set，保证了内部 value 的唯一性，另一方面它可以为每个 value 赋予一个 score 值，用来代表排序的权重。内部实现用的是一种叫做 **「跳跃表」** 的数据结构。

#### Redis 优点

- **读写性能优异**， Redis能读的速度是 `110000` 次/s，写的速度是 `81000` 次/s。
- **支持数据持久化**，支持 AOF 和 RDB 两种持久化方式。
- **支持事务**，Redis 的所有操作都是原子性的，同时 Redis 还支持对几个操作合并后的原子性执行。
- **数据结构丰富**，除了支持 string 类型的 value 外还支持 hash、set、zset、list 等数据结构。
- **支持主从复制**，主机会自动将数据同步到从机，可以进行读写分离。

#### Redis 缺点

- 数据库 **容量受到物理内存的限制**，不能用作海量数据的高性能读写，因此 Redis 适合的场景主要局限在较小数据量的高性能操作和运算上。
- Redis **不具备自动容错和恢复功能**，主机从机的宕机都会导致前端部分读写请求失败，需要等待机器重启或者手动切换前端的 IP 才能恢复。
- 主机宕机，宕机前有部分数据未能及时同步到从机，切换 IP 后还会引入数据不一致的问题，降低了 **系统的可用性**。
- **Redis 较难支持在线扩容**，在集群容量达到上限时在线扩容会变得很复杂。为避免这一问题，运维人员在系统上线时必须确保有足够的空间，这对资源造成了很大的浪费。

#### Redis单线程为什么快

- **纯内存操作**：读取不需要进行磁盘 I/O，所以比传统数据库要快上不少；*(但不要有误区说磁盘就一定慢，例如 Kafka 就是使用磁盘顺序读取但仍然较快)*
- **单线程，无锁竞争**：这保证了没有线程的上下文切换，不会因为多线程的一些操作而降低性能；
- **多路 I/O 复用模型，非阻塞 I/O**：采用多路 I/O 复用技术可以让单个线程高效的处理多个网络连接请求（尽量减少网络 IO 的时间消耗）；
- **高效的数据结构，加上底层做了大量优化**：Redis 对于底层的数据结构和内存占用做了大量的优化，例如不同长度的字符串使用不同的结构体表示，HyperLogLog 的密集型存储结构等等..

redis利用队列技术将并发访问变为串行访问，消除了传统数据库串行控制的开销

Redis的存储是以`key-value`的形式的。Redis中的<font color="lighblue">key一定是字符串</font>，value可以是string、list、hash、set、sortset这几种常用的。

#### SDS简单动态字符串

Redis使用sdshdr结构来表示一个SDS值：

```java
struct sdshdr{

    // 字节数组，用于保存字符串
    char buf[];

    // 记录buf数组中已使用的字节数量，也是字符串的长度
    int len;

    // 记录buf数组未使用的字节数量
    int free;
}
```

#### SDS字符串的好处

1. sdshdr数据结构中用len属性记录了字符串的长度。那么**获取字符串的长度时，时间复杂度只需要O(1)**。
2. SDS不会发生溢出的问题，如果修改SDS时，空间不足。先会扩展空间，再进行修改！(**内部实现了动态扩展机制**)。
3. SDS可以**减少内存分配的次数**(空间预分配机制)。在扩展空间时，除了分配修改时所必要的空间，还会分配额外的空闲空间(free 属性)。
4. SDS是**二进制安全的**，所有SDS API都会以处理二进制的方式来处理SDS存放在buf数组里的数据。

#### Redis链表的特性

- 无环双向链表
- 获取表头指针，表尾指针，链表节点长度的时间复杂度均为O(1)
- 链表使用`void *`指针来保存节点值，可以保存各种不同类型的值

#### 跳跃表(shiplist)

跳跃表(shiplist)是实现sortset(**有序**集合)的底层数据结构之一！

Redis的跳跃表实现由zskiplist和zskiplistNode两个结构组成。其中**zskiplist保存跳跃表的信息**(表头，表尾节点，长度)，**zskiplistNode则表示跳跃表的节点**。

### Redis数据库

Redis服务器中也有数据库这么一个概念。如果不指定具体的数量，默认会有**16**个数据库。

Redis是C/S结构，数据库与数据库之间的数据是**隔离**的。

Redis的数据库就是使用字典(哈希表)来作为底层实现的，对**数据库的增删改查都是构建在字典(哈希表)的操作之上的**。

#### 键的过期时间

内存是**有限**的。所以我们**会干掉不常用的数据，保留常用的数据**。我们需要设置一下键的过期(生存)时间。

- 设置键的**生存**时间可以通过`EXPIRE`或者`PEXPIRE`命令。
- 设置键的**过期**时间可以通过`EXPIREAT`或者`PEXPIREAT`命令。

Redis采用的是**惰性删除+定期删除**两种策略，所以说，在Redis里边如果过期键到了过期的时间了，未必被立马删除的。

使用 Redis 缓存数据时，为了提高缓存命中率，需要保证缓存数据都是**热点数据**。可以将内存最大使用量设置为热点数据占用的内存量，然后启用<font color="lighblue">allkeys-lru淘汰策略</font>，将<font color="lighblue">最近最少使用的数据</font>淘汰。

### Redis持久化

Redis是基于内存的，如果不想办法将数据保存在硬盘上，一旦Redis重启(退出/故障)，内存的数据将会全部丢失。

**RDB**：保存这一时刻的数据状态，默认开启

**AOF**：将所有redis写命令记录到日志文件中

#### RDB(快照持久化)

<font color="lighblue">RDB持久化</font>可以**手动**执行，也可以根据服务器配置**定期**执行。RDB持久化所生成的RDB文件是一个经过**压缩**的二进制文件，Redis可以通过这个文件**还原**数据库的数据。生成的文件名为dump.rdb

通过手动调用**SAVE或者BGSAVE**命令或者配置条件触发，将数据库**某一时刻**的数据快照，生成RDB文件实现持久化。

**两个命令可以手动执行生成RDB文件：**

**BGSAVE**创建出一个**子进程**，由子进程来负责创建RDB文件，服务器进程可以继续接收请求。

![](https://note.youdao.com/yws/api/personal/file/B6A9E249EE324FE987B7D624D0124458?method=download&shareKey=5ef826e43cba3ca38ff243d2de5665bc)

**SAVE**会**阻塞**Redis服务器进程，服务器不能接收任何请求，直到RDB文件创建完毕为止。

![](https://note.youdao.com/yws/api/personal/file/E59FAE4EE6AB4576A975ACDFE71742CF?method=download&shareKey=8ecb6d462d2abfe3df13fbda9fe8031c)

#### AOF(文件追加)

AOF是通过保存Redis服务器所执行的**写命令**来记录数据库的数据的。生成的文件名为appendonly.aof。默认不开启，开启持久化命令：appendonly yes

**日志追加频率**

1. always(谨慎使用)

   每个redis写命令都要同步写入硬盘，严重降低redis速度

2. everysec(推荐)

   每秒执行一次同步显示的将多个写命令同步到磁盘

3. no(不推荐)

   由操作系统决定何时同步

配置文件修改频率命令：appendfsync  everysec | always | no

<font color="lighblue">AOF持久化</font>功能的实现可以分为3个步骤：

- 命令追加：命令写入aof_buf缓冲区
- 文件写入：调用flushAppendOnlyFile函数，考虑是否要将aof_buf缓冲区写入AOF文件中
- 文件同步：考虑是否将内存缓冲区的数据真正写入到硬盘

<font color="lighblue">AOF重写</font>由Redis自行触发(参数配置)，也可以用`BGREWRITEAOF`命令**手动触发**重写操作。为了一定程度上减小AOF文件的体积。

* 执行BGREWRITEAOF命令，不会阻塞redis的服务。
* 服务器配置方式自动触发。在redis.conf中，如果设置auto-aof-rewrite-percentage值为100和auto-aof-rewrite-min-size 64mb,并且启用的AOF持久化时,那么当AOF文件体积大于64N,并且AOF文件的体积比上一次重写之后体积大了至少一倍(108%)时，会自动触发。

重写原理：重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件,替换原有的文件这点和快照有点类似。

<font color="lighblue">AOF后台重写</font>是不会阻塞主进程接收请求的，新的写命令请求可能会导致**当前数据库和重写后的AOF文件的数据不一致**！

为了解决数据不一致的问题，Redis服务器设置了一个**AOF重写缓冲区**，这个缓存区会在服务器**创建出子进程之后使用**。

![](https://note.youdao.com/yws/api/personal/file/A13525BECD8A4E97AF030F3899184B19?method=download&shareKey=c2756c1cd942a9cfc389f1fe48a185eb)

#### RDB和AOF对过期键的策略

RDB持久化对过期键的策略：

- 执行`SAVE`或者`BGSAVE`命令创建出的RDB文件，程序会对数据库中的过期键检查，**已过期的键不会保存在RDB文件中**。
- 载入RDB文件时，程序同样会对RDB文件中的键进行检查，**过期的键会被忽略**。

AOF持久化对过期键的策略：

- 如果数据库的键已过期，但还没被惰性/定期删除，AOF文件不会因为这个过期键产生任何影响(也就说会保留)，当过期的键被删除了以后，会追加一条DEL命令来显示记录该键被删除了
- 重写AOF文件时，程序会对RDB文件中的键进行检查，**过期的键会被忽略**。

复制模式：

- **主服务器来控制**从服务器统一删除过期键(保证主从服务器数据的一致性)

#### RDB和AOF用哪个

RDB和AOF并不互斥，它俩可以**同时使用**。

- RDB的优点：载入时**恢复数据快**、文件体积小。
- RDB的缺点：会一定程度上**丢失数据**(因为系统一旦在定时持久化之前出现宕机现象，此前没有来得及写入磁盘的数据都将丢失。)
- AOF的优点：丢失数据少(默认配置只丢失一秒的数据)。
- AOF的缺点：恢复数据相对较慢，文件体积大

如果Redis服务器**同时开启**了RDB和AOF持久化，服务器会**优先使用AOF文件**来还原数据(因为AOF更新频率比RDB更新频率要高，还原的数据更完善)

### Redis主从复制

#### 主从架构

**主从架构特点：**

主从复制架构仅仅用来解决数据的冗余备份，从节点仅仅用来同步数据。

无法解决: 1.master节点出现故障的自动故障转移

![](https://note.youdao.com/yws/api/personal/file/915454C08E0C4ABDA4CBC2A5FB239494?method=download&shareKey=3b9f4e39570bd5ea676632392595e9b8)

![](https://note.youdao.com/yws/api/personal/file/3F6AB64607AA432589F91B6791764331?method=download&shareKey=8661786fec5aa41a3b4935ee1e9b3f50)

主从架构的**好处**：

- 读写分离(主服务器负责写，从服务器负责读)
- 高可用(某一台从服务器挂了，其他从服务器还能继续接收请求，不影响服务)
- 处理更多的并发量(每台从服务器**都可以接收读请求**，读QPS就上去了)

#### 复制功能

主从架构的特点之一：主服务器和从服务器的数据是**一致**的。

复制功能分为两个操作：

- <font color="lighblue">同步(sync)</font>
  * 将从服务器的数据库状态**更新至**主服务器的数据库状态

- <font color="lighblue">命令传播(command propagate)</font>
  * 主服务器的数据库状态**被修改**，导致主从服务器的数据库状态**不一致**，让主从服务器的数据库状态**重新回到一致状态**。

![](https://note.youdao.com/yws/api/personal/file/A0C624C3A6F24D85A5593F029C1A7031?method=download&shareKey=ebd42206a16d3723a7ed07dca25f8b53)

![](https://note.youdao.com/yws/api/personal/file/F12C5A558A8C444F829936F0724A5489?method=download&shareKey=85fab767d8552f10534b82a622316ce9)

从服务器对主服务器的**同步又可以分为两种情况**：

- 初次同步：从服务器**没有复制过任何**的主服务器，或者从服务器要复制的主服务器跟上次复制的主服务器**不一样**。
- 断线后同步：处于**命令传播阶段**的主从服务器因为**网络原因**中断了复制，从服务器通过**自动重连**重新连接主服务器，并继续复制主服务器

#### 搭建主从复制

```properties
# 1.准备3台机器并修改配置,master为主服务器,redis.conf文件
- master
  port 6379
  bind 0.0.0.0
  
- slave1
  port 6380
  bind 0.0.0.0
  slaveof masterip masterport
  # slaveof指定主服务器ip和端口port，bind开启远程连接权限
  
- slave2
  port 6381
  bind 0.0.0.0
  slaveof masterip masterport
```

![](https://note.youdao.com/yws/api/personal/file/9B31B5C1E2F64ADABD1FEA4AC82301B8?method=download&shareKey=009febd2a1693f5f68b845e8ecf667f7)

```properties
#2.启动3台机器进行测试
- cd /usr/redis/bin
- ./redis-server /root/master/redis.conf
- ./redis-server /root/slave1/redis.conf
- ./redis-server /root/slave2/ redis.conf
```

### 哨兵(Sentinel)机制

> https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484451&idx=1&sn=5495b1165954cd6b84b011489e04a66b&chksm=ebd74522dca0cc3416ab0ccd3a4ddb4ddd28290c9769596a069d81df3b03f4bad72e27d30a6d&token=620000779&lang=zh_CN&scene=21###wechat_redirect

主服务器挂了，我们可以将从服务器**升级**为主服务器，等到旧的主服务器(挂掉的那个)重连上来，会将它(挂掉的主服务器)变成从服务器。哨兵就是带有自动故障转移功能的主从架构。可设置多个哨兵，防止脑裂。

无法解决：1.单节点并发压力问题  2.单节点内存和磁盘物理上限

![](https://note.youdao.com/yws/api/personal/file/6B0E6BEDA2F146448BFDF90561A7A31D?method=download&shareKey=153cec115a91a574de66a07f5a851fff)

在正常的情况下，主从加哨兵(Sentinel)机制是这样子的：

![](https://note.youdao.com/yws/api/personal/file/98FD4FD575224CB2BA3CC8E8D8071D93?method=download&shareKey=f9fa4ae579894f23ba9ea11ba499ad4a)

主服务器挂了，主从复制操作就中止了，并且哨兵系统是可以察觉出主服务挂了：

![](https://note.youdao.com/yws/api/personal/file/6A4522B7E07848D29FA9FBB74386260C?method=download&shareKey=bde831d5c0fa7efeab4bebcfb99f2d69)

Redis提供哨兵机制可以将**选举**一台从服务器变成主服务器

![](https://note.youdao.com/yws/api/personal/file/38726A28A0424489A6CDBD6F1C270A4A?method=download&shareKey=608ec7d1abacb3e3fdc4bb32be1b1d0a)

然后旧的主服务器如果重连了，会变成从服务器：

![](https://note.youdao.com/yws/api/personal/file/E66AEDB7DEA7479285C55C11BF543A7B?method=download&shareKey=efbfc6caf110d88c7c19ea2feb999b77)

哨兵(Sentinel)机制主要用于实现Redis的**高可用性**，主要的功能：

* Sentinel**不停地监控**Redis主从服务器是否正常工作
* 如果某个Redis实例有故障，那么哨兵负责**发送消息通知**管理员
* 如果主服务器挂掉了，会**自动**将从服务器提升为主服务器(包括配置都会修改)。
* Sentinel可以作为**配置中心**，能够提供当前主服务器的信息。

Sentinel本质上只是一个**运行在特殊模式下的Redis服务器**。因为Sentinel做的事情和Redis服务器是不一样的，所以它们的初始化是有所区别的(比如，Sentinel在初始化的时候并不会载入AOF/RDB文件，因为Sentinel根本就不用数据库)。

#### 搭建哨兵机制

```properties
#1.在主节点上创建哨兵配置
-在Master对应redis.conf同目录下新建sentinel.conf文件，名字绝对不能错;

#2.配置哨兵,在sentine1.conf文件中填入内容;
- sentinel monitor 哨兵监听的名称（自己起名字) ip port 1
eg:sentinel monitor mymaster 192.168.121.102 6379 1

#3.启动哨兵模式进行测试
- redis-sentinel /myredis/sentinel.conf
  说明:这个后面的数字2,是指当有两个及以上的sentinel服务检测到master宕机，才会去执行主从切换的功能。
```

![](https://note.youdao.com/yws/api/personal/file/0BFF65B195E741A7B85AB53845EE58DE?method=download&shareKey=f41414438a45785da80d7462b32843c5)

application.properties

```properties
#redis单节点
#spring.redis.host=192.168.202.206
#spring.redis.port=7000
#spring.redis.database=0

#redis sentinel 配置
#master书写是使用哨兵监听的那个名称
spring .redis .sentinel.master=mymaster
#连接的不再是一个具体redis主机,书写的是多个哨兵节点
spring.redis.sentine1.nodes=192.168.202.206:26379
```

![](https://note.youdao.com/yws/api/personal/file/BE8A60F50A094CD194B015BAD84CC565?method=download&shareKey=ede8c71f84c099792d77497da8890d6f)

主从+哨兵架构可以说Redis是高可用的，但要清楚的是：Redis还是会**丢失数据**的。

**丢失数据有两种情况：**

- 异步复制导致的数据丢失

  * **有部分数据还没复制到从服务器，主服务器就宕机了**，此时这些部分数据就丢失了。

- 脑裂导致的数据丢失

  * 有时候主服务器脱离了正常网络，跟其他从服务器不能连接。此时哨兵可能就会**认为主服务器下线了**(然后开启选举，将某个从服务器切换成了主服务器)，但是实际上主服务器还运行着。这个时候，集群里就会有两个服务器(也就是所谓的脑裂)。

  - 虽然某个从服务器被切换成了主服务器，但是可能客户端**还没来得及切换到新的主服务器**，客户端还继续写向旧主服务器写数据。旧的服务器重新连接时，会作为从服务器复制新的主服务器(这意味着旧数据丢失)。


#### redis面试题

![](https://note.youdao.com/yws/api/personal/file/9400E8C9419A4454B0C5E8E462D076B0?method=download&shareKey=c50cda47a5d0f23da4bcfbdbf1528665)



![](https://note.youdao.com/yws/api/personal/file/5A139A125F5D4EB7864147C557561327?method=download&shareKey=3818b78dff86a6841b7f267ccd0b697d)



![](https://note.youdao.com/yws/api/personal/file/88037224C4C843799B0116B26F98C164?method=download&shareKey=493c23733b63ca33586655fd7d13df67)



![](https://note.youdao.com/yws/api/personal/file/27191F99158849CFACCF6FFD93BE65EB?method=download&shareKey=7fdcf2bc1cf8c55ea6719f65f9f2398d)

#### Redis 的淘汰策略有哪些

| 策略            | 描述                                                         |
| :-------------- | :----------------------------------------------------------- |
| volatile-lru    | 从已设置过期时间的 KV 集中优先对最近最少使用(less recently used)的数据淘汰 |
| volitile-ttl    | 从已设置过期时间的 KV 集中优先对剩余时间短(time to live)的数据淘汰 |
| volitile-random | 从已设置过期时间的 KV 集中随机选择数据淘汰                   |
| allkeys-lru     | 从所有 KV 集中优先对最近最少使用(less recently used)的数据淘汰 |
| allKeys-random  | 从所有 KV 集中随机选择数据淘汰                               |
| noeviction      | 不淘汰策略，若超过最大内存，返回错误信息                     |

### 集群

Redis在3.0后开始支持Cluster(模式)模式,目前redis的集群支持节点的自动发现,支持slave-master选举和容错,支持在线分片等特性。

- 所有的redis节点彼此互联(PING-PONG机制),内部使用二进制协议优化传输速度和带宽。
- 节点的fail是通过集群中超过半数的节点检测失效时才生效。
- 客户端与redis节点直连,不需要中间proxy层.客户端不需要连接集群所有节点,连接集群中任何一个可用节点即可
- redis-cluster把所有的物理节点映射到[0-16383]slot上,cluster 负责维护node<->slot<->value



### Redis的使用

#### Redis的安装和使用

**配置redis的环境变量，Path路径中添加redis文件夹路径**

#### Redis命令使用参考

> https://www.bilibili.com/video/BV1jD4y1Q7tU?p=12

#### 开启Redis

* 启动Redis

  * ```java
    redis-server
    ```

* 指定配置文件

  * ```
    ./redis-server /etc/redis/6379.conf
    ```

  * ```
    redis-cli -p 6380
    ```

![](https://note.youdao.com/yws/api/personal/file/4AEC0ED6538A4B6496E92CC584B8274A?method=download&shareKey=5b802c9857d13525eaee3691af7e5324)

启动Redis客户端

- ```cmd
  redis-cli
  ```

![](https://note.youdao.com/yws/api/personal/file/7B3497F4A2034789AAC40E34D4720AE9?method=download&shareKey=5f565ccaa9dfc3f1820b6dd8143edd05)

连接客户端

![](https://note.youdao.com/yws/api/personal/file/B998DDBB9855486CA7346D570882E57E?method=download&shareKey=4f29808d4c7516eb3fd1f9956b6b0091)

#### Redis事务

- 启动事务：MULTI

- 执行事务：EXEC

![](https://note.youdao.com/yws/api/personal/file/6F682BDFB7BE4BA697E6C4AC63D386EA?method=download&shareKey=1fa526f4035634d3a51072621e51de1e)

#### 消息发布

![](https://note.youdao.com/yws/api/personal/file/AD5D18D39AC94E4F99BA4FA687025324?method=download&shareKey=b8907ab6785186c6753a9dcd473b3722)

#### 常用命令

```java
数据: <key value>  eg:<name zhangsan>
1.查看当前库的数据: keys *
2.清空当前库数据: FLUSHDB
  清空所有库数据: FLUSHALL
3.添加: set name zhangsan
4.查询: get name
5.删除: del name
6.判断数据是否存在：exists name //存在则返回1
7.设置有效期: expire name 10  //10秒后过期被清除
  设置有效期: pxpire name 1000 //毫秒为单位，1000毫秒后过期
8.返回剩余有效时间: TTL name  //key不存在，返回-2，没有设置有效期，返回-1，存在返回剩余有效时间
  返回剩余有效时间: pttl name  //毫秒为单位
9.移动数据至其他数据库: move name 1   //移动key为name的数据至1号数据库
10.修改key值: rename name names  //改名
11.查询key存储数据的类型: type name  //若key不存在，返回none，否则返回value对应类型
```

![](https://note.youdao.com/yws/api/personal/file/A8A3A0B5613844398B778083005CFF79?method=download&shareKey=0f95310e7366f2b00cf5137e5588f4ea)

#### String类型

![](https://note.youdao.com/yws/api/personal/file/91024A389FA1454BAD154A410E3B86E9?method=download&shareKey=c0f9afcfeceb41273245b5cc4a87f9ad)

常用操作命令

| 命令                                       | 说明                                       |
| ------------------------------------------ | ------------------------------------------ |
| set                                        | 设置一个key/value                          |
| get                                        | 根据key获得对应的value                     |
| mset                                       | 一次设置多个key value                      |
| mget                                       | —次获得多个key的value                      |
| getset                                     | 获得原始key的值，同时设置新值              |
| strlen                                     | 获得对应key存储value的长度                 |
| append                                     | 为对应key的value追加内容                   |
| getrange索引0开始                          | 截取value的内容                            |
| setex                                      | 设置一个key存活的有效期（秒)               |
| psetex                                     | 设置一个key存活的有效期（毫秒)             |
| setnx                                      | 存在不做任何操作,不存在添加                |
| msetnx原子操作(只要有一个存在不做任何操作) | 可以同时设置多个key,只有有一个存在都不保存 |
| decr                                       | 进行数值类型的-1操作                       |
| decrby                                     | 根据提供的数据进行减法操作                 |
| Incr                                       | 进行数值类型的+1操作                       |
| incrby                                     | 根据提供的数据进行加法操作                 |
| lncrbyfloat                                | 根据提供的数据加入浮点数                   |

![](https://note.youdao.com/yws/api/personal/file/6BC6CDACB74946898D208A70E2FA18A1?method=download&shareKey=04d99ce5f7a911e5ee433cab7ef7c093)

#### List类型

元素有序且可重复

![](https://note.youdao.com/yws/api/personal/file/2051F5FBE2534AEEAE3FCBC921FC2A36?method=download&shareKey=407a191c87b53c7abec03b0a875bc047)

常用操作命令

| 命令    | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| lpush   | 将某个值加入到一个key列表头部                                |
| lpushx  | 同lpush,但是必须要保证这个key存在                            |
| rpush   | 将某个值加入到一个key列表末尾                                |
| rpushx  | 同rpush,但是必须要保证这个key存在                            |
| lpop    | 返回和移除列表左边的第一个元素                               |
| rpop    | 返回和移除列表右边的第一个元素                               |
| lrange  | 获取某一个下标区间内的元素，lrange key start end             |
| llen    | 获取列表元素个数                                             |
| lset    | 设置某一个指定索引的值(索引必须存在)                         |
| lindex  | 获取某一个指定索引位置的元素                                 |
| lrem    | 删除重复元素                                                 |
| ltrim   | 保留列表中特定区间内的元素                                   |
| linsert | 在某一个元素value1之前\|之后插入新元素value2，linsert key before\|after value1 value2 |

![](https://note.youdao.com/yws/api/personal/file/886B631CBA614597A5B1D6C1B3F8285E?method=download&shareKey=4a3d29d0b32cab2eecd795d1cf524265)

#### Set类型

元素无序 不可以重复

**内存存储模型**

![](https://note.youdao.com/yws/api/personal/file/1C9B97948F6542B990D99B1945B6B6F6?method=download&shareKey=fe1743e74b100490d24da9d4a3893ebd)

常用操作命令

| 命令        | 说明                                   |
| ----------- | -------------------------------------- |
| sadd        | 为集合添加元素                         |
| smembers    | 显示集合中所有元素无序                 |
| scard       | 返回集合中元素的个数                   |
| spop        | 随机返回一个元素并将元素在集合中删除   |
| smove       | 从一个集合中向另一个集合移动元素       |
| srem        | 从集合中删除一个元素                   |
| sismember   | 判断一个集合中是否含有这个元素         |
| srandmember | 随机返回元素                           |
| sdiff       | 去掉第一个集合中其它集合含有的相同元素 |
| sinter      | 求交集                                 |
| sunion      | 求和集                                 |

![](https://note.youdao.com/yws/api/personal/file/6F83838AD3BD4EE7A311B59DCEB95D35?method=download&shareKey=864b851ebe09478e790ff08c31a61062)

#### ZSet类型

可排序的set集合，排序 不可重复

![](https://note.youdao.com/yws/api/personal/file/82BC8728EDD147A99644B6BA4CEE1716?method=download&shareKey=90b58ade6ec3d1856a599f99b2b2c8c5)

常用操作命令

| 命令                    | 说明                         |
| ----------------------- | ---------------------------- |
| zadd                    | 添加一个有序集合元素         |
| zcard                   | 返回集合的元素个数           |
| zrange升序zrevrange降序 | 返回一个范围内的元素         |
| zrangebyscore           | 按照分数查找一个范围内的元素 |
| zrank                   | 返回排名                     |
| zrevrank                | 倒序排名                     |
| zscore                  | 显示某一个元素的分数         |
| zrem                    | 移除某一个元素               |
| zincrby                 | 给某个特定元素加分           |

![](https://note.youdao.com/yws/api/personal/file/6D386F3B333A40CD947616C53B387258?method=download&shareKey=6d13fe0cfa3c30d40a43401b7b985666)

#### Hash类型

value是一个map结构，存在key value key无序的

![](https://note.youdao.com/yws/api/personal/file/1D6A31E661294855AD6FCE466AE16199?method=download&shareKey=87b9944732f9b8539bfbceea27aa0cb7)

常用操作命令

| 命令         | 说明                    |
| ------------ | ----------------------- |
| hset         | 设置一个key/value对     |
| hget         | 获得一个key对应的value  |
| hgetall      | 获得所有的key/value对   |
| hdel         | 删除某一个key/value对   |
| hexists      | 判断一个key是否存在     |
| hkeys        | 获得所有的key           |
| hvals        | 获得所有的value         |
| hmset        | 设置多个key/value       |
| hmget        | 获得多个key的value      |
| hsetnx       | 设置一个不存在的key的值 |
| hincrby      | 为value进行加法运算     |
| hincrbyfloat | 为value加入浮点值       |

![](https://note.youdao.com/yws/api/personal/file/0E5A5BC4CD214164B1D9D2C0071A24BD?method=download&shareKey=99c87c985fc1ead4ca092207d9b91614)

### 连接Redis数据库

#### Java连接Redis数据库(jedis)

添加jedis-2.9.0.jar到项目中


- Redis中java使用

   ```xml
    首先用maven构建一个java项目，引入redis的客户端——jedis依赖：
    
    <!--引入redis依赖-->
    <dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.9.0</version>
    </dependency>
   ```

   ```java
  PONG说明操作成功，
    接下来就可以通过jedis对象来操作Redis了；
    
    1.String类型的数据 : 
    添加：set()
    查询：get()
    
    2.hash类型的数据：
    添加：hmset()
    查询：hgetAll()
    
    3.list类型的数据：
    添加：lpush()
    查询：lrange()
    
    4.set类型的数据：
    添加：sadd()
    查询：smembers()
  ```
  
- 特点
  - 异常快
  - 支持丰富的数据类型
  - 操作具有原子性
  - 多实用工具：缓存、消息队列、短期数据

- 发消息（同步）

  - 订阅：

    - ```cmd
      SUBSCRIBE redis123
      ```

  - 发布：

    - ```cmd
      PUBLISH redis123 "hello world"
      ```

- 关闭redis服务器命令
  - 打开客户端（redis-cli）
  - shutdown

- 获取连接

  - ```java
     Jedis jedis=new Jedis("localhost",6379);
    ```

- 指定库

  - ```java
    jedis.select(1);
    ```

- String类型

  - 设置：set（key,value）
  - 取值：get（key）

- 实例：

   ```java
    public class RedisTest {
        static Jedis jedis=null;
        public static Jedis getRedis(){
            //连接的IP和端口
            jedis=new Jedis("localhost",6379);
            return jedis;
        }
        /**
         * 字符串设置和获取
         */
        public static void testString(Jedis jedis){
            //选择1号数据库
            jedis.select(1);
            jedis.set("city","TaiYuan");
            jedis.set("country","china");
            System.out.println(jedis.get("city"));
            System.out.println(jedis.get("country"));
        }
        public static void main(String[] args) {
            jedis=getRedis();
            testString(jedis);
            jedis.close();
        }
    }
   ```

- List类型

  - 设置：lpush（key，value1）； lpush（key，value2）；

  - 取值：lrange（key，开始范围，结束范围）    集合类型接收

  - 实例

    - ```java
      /**
           * 列表操作
           * @param jedis
           */
      public static void testList(Jedis jedis){
              jedis.select(1);
              jedis.lpush("student","liujie");
              jedis.lpush("student","jack");
              jedis.lpush("student","wjj");
              List<String> list= jedis.lrange("student",0,2);
              for(String s:list){
                  System.out.println(s);
              }
          }
      ```

- Set类型

  - 设置：sadd（key，value1，value2，value3）
  - 取值：smembers（key）

  - 实例

    - ```java
      /**
           * 可变集合
           * @param
           */
      public static void testSet(Jedis jedis){
              jedis.select(1);
              long count=jedis.sadd("citys","成都","北京","太原");
              System.out.println(count);
              System.out.println(jedis.smembers("citys"));
          }
      ```

- hash类型

  - 设置：hmset()
  - 取值：hgetAll()

#### SpringBoot引入Redis

https://www.bilibili.com/video/BV1jD4y1Q7tU?p=12

​		Spring Boot Data(数据) Redis 中提供了<font color="lighblue">RedisTemplate和StringRedisTemplate</font>，其中StringRedisTemplate是RedisTemplate的子类，两个方法基本一致，不同之处主要体现在操作的数据类型不同，RedisTemplate中的两个泛型都是object，意味着存储的key和value都可以是一个对象，而StringRedisTemplate的两个泛型都是String，意味着stringRedisTemplate的key和value都只能是字符串。

**注意**：使用RedisTemplate默认是将对象序列化到Redis中,所以放入的对象必须实现对象序列化接口。

引入依赖

```xml
<!--Redis-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

application.properties

```properties
# redis的使用
spring.session.store-type=redis
spring.redis.host=127.0.0.1
spring.redis.port=6379
```

##### StringRedisTemplate

###### key相关操作

```java
//操作redis中key相关
	@Test
	public void testRedis1() {
		stringRedisTemplate.delete("name");//删除一个key
		Boolean hasKey = stringRedisTemplate.hasKey("name");//删除一个key
		DataType name = stringRedisTemplate.type("name");//判断key所对应值的类型
		Set<String> keys = stringRedisTemplate.keys("*");//获取redis中所有值
		keys.forEach(key -> System.out.println("key = " +key ));
		stringRedisTemplate.opsForValue().set("age","18");
		Long expire = stringRedisTemplate.getExpire("age");//获取key超时时间 -1 永不超时 -2 key不存在 》=0 过期时间
		stringRedisTemplate.randomKey();//在redis中随机获取一个key
		stringRedisTemplate.rename("age","age1");//给key改名
		stringRedisTemplate.move("age1",1);//移动key到指定库
	}
```

###### String相关操作

```java
//操作redis中字符串 opsForValue 实际操作就是redis中string类型
	@Test
	public void testRedis() {
		//1.将对象保存到redis中
		stringRedisTemplate.opsForValue().set("name","小陈");//set 用来设置一个key value
		String value = stringRedisTemplate.opsForValue().get("name");//用来获取一个key对应的value
		stringRedisTemplate.opsForValue().set("code","1886555",120, TimeUnit.SECONDS);//设置一个key超时时间
		stringRedisTemplate.opsForValue().append("name","张三");//追加内容
	}
```

###### List相关操作

```java
//操作redis中list类型，opsForList实际操作就是redis中list类型
	@Test
	public void testList() {
		stringRedisTemplate.opsForList().leftPush("names","张三");//创建一个列表，并放入一个元素
		stringRedisTemplate.opsForList().leftPushAll("names","小三","小四","小五");//创建一个列表，并放入多个元素
		List<String> names = new ArrayList<>();
		names.add("小红");
		names.add("小兰");
		stringRedisTemplate.opsForList().leftPushAll("names",names);//创建一个列表，并放入多个元素
		List<String> stringList = stringRedisTemplate.opsForList ().range("names",0,-1);//遍历list
		stringList.forEach(value-> System.out.println("value"+value));
		stringRedisTemplate.opsForList().trim("name",1,3);//截取指定区间的list
	}
```

###### Set相关操作

```java
//操作redis中set类型，opsForSet实际操作就是redis中set类型
	@Test
	public void testSet() {
		stringRedisTemplate.opsForSet().add("sets", "张三", "张三", "小陈", "xiaoming"); //创建set 并放入多个元素
		Set<String> sets = stringRedisTemplate.opsForSet().members("sets");//查看set中成员
		sets.forEach(value -> System.out.println("value = " + value));
		Long size = stringRedisTemplate.opsForSet().size("sets"); //获取set集合元素个数;
	}
```

###### ZSet相关操作

```java
//操作redis中Zset类型opsForSet 实际操作就是redis中set类型Test
	@Test
	public void testZset () {
		stringRedisTemplate.opsForZSet().add("zsets", "小黑", 20); //创建并放入元素
		Set<String> zsets = stringRedisTemplate.opsForZSet().range("zsets", 0, -1);//指定范围查询
		zsets.forEach(value -> System.out.println(value));
		Set<ZSetOperations.TypedTuple<String>> zsets1 = stringRedisTemplate.opsForZSet().rangeByScoreWithScores("zsets",0,1000);//获取指定元素以及分数
		zsets1.forEach(typedTuple -> {
			System.out.println(typedTuple.getValue());
			System.out.println(typedTuple.getScore());
		});
	}
```

###### Hash相关操作

```java
//操作redis中Hash类型opsForHash 实际操作就是redis中Hash类型
	@Test
	public void testHash () {
		stringRedisTemplate.opsForHash().put("maps", "name", "张三");//创建一个nash类型并放入key value
		Map<String, String> map = new HashMap<String, String>();
		map.put("age", "12");
		map.put("bir", "2012-12-12");
		stringRedisTemplate.opsForHash().putAll("maps", map);//放入多个key value
		List<Object> values = stringRedisTemplate.opsForHash().multiGet("maps", Arrays.asList("name", "age"));//获取多个key的值
		values.forEach(value -> System.out.println(value));
		String value = (String) stringRedisTemplate.opsForHash().get("maps", "name");//获取hash中某个key的值
		List<Object> vals = stringRedisTemplate.opsForHash().values("maps");//获取所有values
		Set<Object> keys = stringRedisTemplate.opsForHash().keys("maps");//获取所有keys
	}
```

##### RedisTemplate

使用RedisTemplate默认是将对象序列化到Redis中，所以放入的对象必须实现对象序列化接口

测试示例

```java
@TableName("user")
@Data
public class User implements Serializable {
    @TableId(type = IdType.AUTO)
    private Integer id;
    private String username;
    private String password;
    private String nickName;
    private Integer age;
    private String sex;
    private String address;
    private String phoneNumber;
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
    @TableLogic
    private Integer deleted;

    @TableField(exist = false)
    private List<Book> bookList;

    public User(String username,String nickName,int age,String sex){
        this.username = username;
        this.nickName = nickName;
        this.age = age;
        this.sex = sex;
    }
}

```

```java
	@Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void testRedis(){

        //修改key序列化方案string类型序列
        redisTemplate.setKeySerializer(new StringRedisSerializer( ) ) ;
        //修改hash key序列化方案
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());

        User user = new User("张三","长久",18,"男");
        redisTemplate.opsForValue().set("user",user);//redis进行设置，对象需要经过序列化
        User user1 = (User)redisTemplate.opsForValue().get("user");
        redisTemplate.opsForList().leftPush ( "list",user);
        redisTemplate.opsForSet().add ("set",user);
        redisTemplate.opsForZSet().add ("zset",user, 10);
        redisTemplate.opsForHash().put( "map","name",user);
    }
```

![](https://note.youdao.com/yws/api/personal/file/56C519E6CC604BF781259684995E001A?method=download&shareKey=c9bf171be7642b643c5449437b1cbf9e)

**解决Redis插入乱码**

https://blog.csdn.net/LLLLLLL_LLLLLLL/article/details/124104800

**解决JSONException报错**

报错信息：com.alibaba.fastjson.JSONException: autoType is not support. com.example.demo.entity.Role

![](https://note.youdao.com/yws/api/personal/file/9CF14CFF8F0E41CFB4E63F9EFDA0D09C?method=download&shareKey=4eaf2d60fb66a3a9970c2b413b350e9e)

-Dfastjson.parser.autoTypeAccept=com.taobao.pac.client.sdk.dataobject.,com.example.demo

com.example.demo为项目路径

**注意**：

1. 针对于日后处理key value都是 string使用stringRedisTemplate
2. 针对于日后处理的key value存在对象使用RedisTemplate
3. 针对于同一个key多次操作可以使用boundXXxOps () value List Set set Hash的api简化书写

### Redis中分布式缓存实现

1. 什么是缓存(Cache)

   定义:就是计算机内存中一段数据

2. 内存中数据特点
   * 读写快
   * 断电立即丢失

3. 缓存解决了什么问题?
   * 提高网站吞吐量提高网站运行效率
   * 核心解决问题:缓存的存在是用来减轻数据库访问压力

4. 既然缓存能提高效率，那项目中所有数据加入缓存岂不是更好?
   注意:使用缓存时一定是数据库中数据极少发生修改,更多用于查询这种情况

5. 本地缓存和分布式缓存区别?
   **本地缓存**:存在应用服务器内存中数据称之为本地缓存(local cache)
   **分布式缓存**:存储在当前应用服务器内存之外数据称之为分布式缓存(distribute cache)集群:将同一种服务的多个节点放在一起共同对系统提供服务过程称之为集群
   **分布式**:有多个不同服务集群共同对系统提供服务这个系统称之为分布式系统(distribute system)

#### 自定义Redis缓存实现

```java
//用来获取springboot创建好的工厂
@Component
public class ApplicationContextUtil implements ApplicationContextAware {

    //保留下来工厂
    private static ApplicationContext applicationContext;

    //将创建好工厂以参数形式传递给这个类
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        ApplicationContextUtil.applicationContext = applicationContext;
    }

    //提供在工厂中过去对象的方法 //RedisTemplate redisTemplate
    public static Object getBean(String beanName){
        return applicationContext.getBean(beanName);
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.demo.mapper.BookMapper">

    <!-- 开启mybatis二级缓存-->
    <cache type="com.example.demo.cache.RedisCache"/>

    <select id="selectBookByUserId" resultType="com.example.demo.entity.Book">
        select `book`.*, `user`.nick_name
        from `book`
        left join `user` on `book`.user_id = `user`.id
        where `book`.user_id = #{userId} and book.DELETED = 0
    </select>
</mapper>
```

```java
//自定义Redis缓存实现
public class RedisCache implements Cache {

    //当前放入缓存的mapper的namespace
    private final String id;

    //必须存在构造方法
    public RedisCache(String id) {
        System.out.println("id：=========" + id);
        this.id = id;
    }

    //返回cache唯一标识
    @Override
    public String getId() {
        return this.id;
    }

    //缓存放入值
    @Override
    public void putObject(Object key, Object value) {
        //使用redishash类型作为缓存存储模型keyhashkey value
        getRedisTemplate().opsForHash().put(id.toString(), getKeyToMD5(key.toString()), value);
        if(id.equals("com.example.demo.mapper.UserMapper")){
            //缓存超时失效
            getRedisTemplate().expire(id.toString(),1, TimeUnit.HOURS);
        }
        if(id.equals("com.example.demo.mapper.RoleMapper")){
            //缓存超时失效
            getRedisTemplate().expire(id.toString(),30, TimeUnit.MINUTES);
        }
        //...指定不同业务模块设置不同缓存超时时间
    }

    //获取数据
    @Override
    public Object getObject(Object key) {
        //根据key从redis的hash类型中获取数据
        return getRedisTemplate().opsForHash().get(id.toString(), getKeyToMD5(key.toString()));
    }

    @Override
    public Object removeObject(Object o) {
        return null;
    }

    @Override
    public void clear() {
        //清空namespace
        getRedisTemplate().delete(id.toString());//清空缓存,新增、修改、删除会触发
    }

    @Override
    public int getSize() {
        //获取hash中的key value数量
        return getRedisTemplate().opsForValue().size(id.toString()).intValue();
    }

    //封装RedisTemplate
    public RedisTemplate getRedisTemplate() {
        // 通过application工具类获取redisTemplate
        RedisTemplate redisTemplate = (RedisTemplate) ApplicationContextUtil.getBean("redisTemplate");
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        return redisTemplate;
    }

    //封装一个对key进行rmd5处理方法
    private String getKeyToMD5(String key) {
        return DigestUtils.md5DigestAsHex(key.getBytes());
    }
}
```

![](https://note.youdao.com/yws/api/personal/file/106BCF7E46E5411EAD769707FBB9A4DD?method=download&shareKey=3e864907d7f4682439d797a7e508dd40)

![](https://note.youdao.com/yws/api/personal/file/87344AC7CE0649B4B2AF911AC09EF574?method=download&shareKey=db565a5e6e1d2edfcea14a6ab64c8d04)

上面的方法只适用于单表查询

在mybatis的缓存中如何要解决关联关系时更新缓存信息的问题?

<cache-ref/> 用来将多个具有关联关系查询缓存放在一起处理

```xml
<mapper namespace="com.example.demo.mapper.RoleMapper">
<!-- 开启mybatis二级缓存-->
<!--    <cache type="com.example.demo.cache.RedisCache"/>-->

<cache-ref namespace="com.example.demo.mapper.UserMapper"/>
```

无论哪个数据进行了增删改操作，都会清空缓存

#### 缓存优化

对放入redis中key进行优化: key的长度不能太长

算法:MD5处理加密特点:

1. 一切文件字符串等经过md5处理之后都会生成32位16进制字符串
2. 不同内容文件经过md5进行加密，加密结果一定不一致
3. 相当内容文件多次经过md5生成结果始终─致

推荐:在redis整合mybatis过程中建议将key进行md5优化处理



https://www.bilibili.com/video/BV1jD4y1Q7tU?p=23&spm_id_from=pageDriver





