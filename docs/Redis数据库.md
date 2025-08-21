Redis数据库

***

[TOC]

### Redis简介

非关系型数据库严格上不是一种数据库，应该是一种数据结构化存储方法的集合。

Redis是**基于内存**，常用作于**缓存**的一种技术，并且Redis存储的方式是以`key-value`的形式。默认有16个库，编号从0开始，0-15，默认使用0号库，库与库之间相互隔离

切换库的命令： select dbid(库编号)  eg：select 1

**Redis作为一个高性能的key-value数据库具有以下特征：**

*   多样的数据模型
*   持久化
*   主从同步

**Redis是单进程单线程的**

![](https://note.youdao.com/yws/api/personal/file/556EE7CCE43743EF9D77EADEE3913F2A?method=download\&shareKey=fa356fe7b337b20983518a97bb1a10f7)

#### 数据类型

*   <font color="lighblue">string(字符串)</font>-->简单的`key-value`，他能够存储任何类型的字符串，底层实现有点类似于 Java 中的 **ArrayList**。
*   <font color="lighblue">list(链表)</font>-->有序链表(底层是双向链表)-->可做简单队列，可以支持反向查找和遍历，列表相当于 Java 语言中的 **LinkedList**，注意它是链表而不是数组。list 的插入和删除操作非常快，时间复杂度为 O(1)，但是索引定位很慢，时间复杂度为 O(n)。
*   <font color="lighblue">hash(字典)</font>-->哈希表-->存储结构化数据，字典相当于Java中的 **HashMap**，"数组 + 链表"的链地址法来解决部分 **哈希冲突**。
*   <font color="lighblue">set(集合)</font>-->无序列表(**去重**)-->提供一系列的交集、并集、差集的命令，集合相当于 Java 语言中的 **HashSet**，它内部的键值对是无序、唯一的。
*   <font color="lighblue">zset(有序集合)</font>-->**有序**集合映射，类似于 Java 中 **SortedSet** 和 **HashMap** 的结合体，它是一个 set，保证了内部 value 的唯一性，另一方面它可以为每个 value 赋予一个 score 值，用来代表排序的权重。内部实现用的是一种叫做 **「跳跃表」** 的数据结构。

#### Redis 优点

*   **读写性能优异**， Redis能读的速度是 `110000` 次/s，写的速度是 `81000` 次/s。
*   **支持数据持久化**，支持 AOF 和 RDB 两种持久化方式。
*   **支持事务**，Redis 的所有操作都是原子性的，同时 Redis 还支持对几个操作合并后的原子性执行。
*   **数据结构丰富**，除了支持 string 类型的 value 外还支持 hash、set、zset、list 等数据结构。
*   **支持主从复制**，主机会自动将数据同步到从机，可以进行读写分离。

#### Redis 缺点

*   数据库 **容量受到物理内存的限制**，不能用作海量数据的高性能读写，因此 Redis 适合的场景主要局限在较小数据量的高性能操作和运算上。
*   Redis **不具备自动容错和恢复功能**，主机从机的宕机都会导致前端部分读写请求失败，需要等待机器重启或者手动切换前端的 IP 才能恢复。
*   主机宕机，宕机前有部分数据未能及时同步到从机，切换 IP 后还会引入数据不一致的问题，降低了 **系统的可用性**。
*   **Redis 较难支持在线扩容**，在集群容量达到上限时在线扩容会变得很复杂。为避免这一问题，运维人员在系统上线时必须确保有足够的空间，这对资源造成了很大的浪费。

#### Redis单线程为什么快

*   **纯内存操作**：读取不需要进行磁盘 I/O，所以比传统数据库要快上不少；(但不要有误区说磁盘就一定慢，例如 Kafka 就是使用磁盘顺序读取但仍然较快)
*   **单线程，无锁竞争**：这保证了没有线程的上下文切换，不会因为多线程的一些操作而降低性能；
*   **多路 I/O 复用模型，非阻塞 I/O**：采用多路 I/O 复用技术可以让单个线程高效的处理多个网络连接请求（尽量减少网络 IO 的时间消耗）；
*   **高效的数据结构，加上底层做了大量优化**：Redis 对于底层的数据结构和内存占用做了大量的优化，例如不同长度的字符串使用不同的结构体表示，HyperLogLog 的密集型存储结构等等..

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

1.  sdshdr数据结构中用len属性记录了字符串的长度。那么获取字符串的长度时，时间复杂度只需要O(1)。
2.  SDS不会发生溢出的问题，如果修改SDS时，空间不足。先会扩展空间，再进行修改！(**内部实现了动态扩展机制**)。
3.  SDS可以**减少内存分配的次数**(空间预分配机制)。在扩展空间时，除了分配修改时所必要的空间，还会分配额外的空闲空间(free 属性)。
4.  SDS是**二进制安全的**，所有SDS API都会以处理二进制的方式来处理SDS存放在buf数组里的数据。

#### Redis链表的特性

*   无环双向链表
*   获取表头指针，表尾指针，链表节点长度的时间复杂度均为O(1)
*   链表使用`void *`指针来保存节点值，可以保存各种不同类型的值

#### 跳跃表(shiplist)

跳跃表(shiplist)是实现sortset(**有序**集合)的底层数据结构之一！

Redis的跳跃表实现由zskiplist和zskiplistNode两个结构组成。其中**zskiplist保存跳跃表的信息**(表头，表尾节点，长度)，**zskiplistNode则表示跳跃表的节点**。

### Redis数据库

Redis服务器中也有数据库这么一个概念。如果不指定具体的数量，默认会有**16**个数据库。

Redis是C/S结构，数据库与数据库之间的数据是**隔离**的。

Redis的数据库就是使用字典(哈希表)来作为底层实现的，对**数据库的增删改查都是构建在字典(哈希表)的操作之上的**。

#### 键的过期时间

内存是**有限**的。所以我们**会干掉不常用的数据，保留常用的数据**。我们需要设置一下键的过期(生存)时间。

*   设置键的**生存**时间可以通过`EXPIRE`或者`PEXPIRE`命令。
*   设置键的**过期**时间可以通过`EXPIREAT`或者`PEXPIREAT`命令。

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

![](https://note.youdao.com/yws/api/personal/file/B6A9E249EE324FE987B7D624D0124458?method=download\&shareKey=5ef826e43cba3ca38ff243d2de5665bc)

**SAVE**会**阻塞**Redis服务器进程，服务器不能接收任何请求，直到RDB文件创建完毕为止。

![](https://note.youdao.com/yws/api/personal/file/E59FAE4EE6AB4576A975ACDFE71742CF?method=download\&shareKey=8ecb6d462d2abfe3df13fbda9fe8031c)

#### AOF(文件追加)

AOF是通过保存Redis服务器所执行的**写命令**来记录数据库的数据的。生成的文件名为appendonly.aof。默认不开启，开启持久化命令：appendonly yes

**日志追加频率**

1.  always(谨慎使用)

    每个redis写命令都要同步写入硬盘，严重降低redis速度

2.  everysec(推荐)

    每秒执行一次同步显示的将多个写命令同步到磁盘

3.  no(不推荐)

    由操作系统决定何时同步

配置文件修改频率命令：appendfsync  everysec | always | no

<font color="lighblue">AOF持久化</font>功能的实现可以分为3个步骤：

*   命令追加：命令写入aof\_buf缓冲区
*   文件写入：调用flushAppendOnlyFile函数，考虑是否要将aof\_buf缓冲区写入AOF文件中
*   文件同步：考虑是否将内存缓冲区的数据真正写入到硬盘

<font color="lighblue">AOF重写</font>由Redis自行触发(参数配置)，也可以用`BGREWRITEAOF`命令**手动触发**重写操作。为了一定程度上减小AOF文件的体积。

*   执行BGREWRITEAOF命令，不会阻塞redis的服务。
*   服务器配置方式自动触发。在redis.conf中，如果设置auto-aof-rewrite-percentage值为100和auto-aof-rewrite-min-size 64mb,并且启用的AOF持久化时,那么当AOF文件体积大于64N,并且AOF文件的体积比上一次重写之后体积大了至少一倍(108%)时，会自动触发。

重写原理：重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件,替换原有的文件这点和快照有点类似。

<font color="lighblue">AOF后台重写</font>是不会阻塞主进程接收请求的，新的写命令请求可能会导致**当前数据库和重写后的AOF文件的数据不一致**！

为了解决数据不一致的问题，Redis服务器设置了一个**AOF重写缓冲区**，这个缓存区会在服务器**创建出子进程之后使用**。

![](https://note.youdao.com/yws/api/personal/file/A13525BECD8A4E97AF030F3899184B19?method=download\&shareKey=c2756c1cd942a9cfc389f1fe48a185eb)

#### RDB和AOF对过期键的策略

RDB持久化对过期键的策略：

*   执行`SAVE`或者`BGSAVE`命令创建出的RDB文件，程序会对数据库中的过期键检查，**已过期的键不会保存在RDB文件中**。
*   载入RDB文件时，程序同样会对RDB文件中的键进行检查，**过期的键会被忽略**。

AOF持久化对过期键的策略：

*   如果数据库的键已过期，但还没被惰性/定期删除，AOF文件不会因为这个过期键产生任何影响(也就说会保留)，当过期的键被删除了以后，会追加一条DEL命令来显示记录该键被删除了
*   重写AOF文件时，程序会对RDB文件中的键进行检查，**过期的键会被忽略**。

复制模式：

*   **主服务器来控制**从服务器统一删除过期键(保证主从服务器数据的一致性)

#### RDB和AOF用哪个

RDB和AOF并不互斥，它俩可以**同时使用**。

*   RDB的优点：载入时**恢复数据快**、文件体积小。
*   RDB的缺点：会一定程度上**丢失数据**(因为系统一旦在定时持久化之前出现宕机现象，此前没有来得及写入磁盘的数据都将丢失。)
*   AOF的优点：丢失数据少(默认配置只丢失一秒的数据)。
*   AOF的缺点：恢复数据相对较慢，文件体积大

如果Redis服务器**同时开启**了RDB和AOF持久化，服务器会**优先使用AOF文件**来还原数据(因为AOF更新频率比RDB更新频率要高，还原的数据更完善)

#### RDB配置过程

默认情况下，Redis 已内置 RDB 相关配置（如默认的save规则、文件名等），无需专门配置也能使用 RDB 功能。但在生产环境中，通常建议根据业务需求调整配置，原因如下：

1. 默认配置可能不匹配业务场景：
   * 默认的save 900 1（15 分钟 1 次修改）等规则，可能导致数据丢失风险过高（如高频写入场景下，宕机可能丢失 15 分钟数据）。
   * 默认存储路径（dir ./）可能在 Redis 重启时因工作目录变化导致 RDB 文件找不到，建议指定绝对路径（如dir /var/redis/data）。
   

2. 性能与安全性权衡：
   * 若对 CPU 敏感（如高频写入场景），可关闭压缩（rdbcompression no）；
   * 若对数据完整性要求高，可调整save规则（如save 60 100，1 分钟内 100 次修改即触发快照）。

因此，生产环境建议专门配置 RDB，至少指定存储路径和合理的save规则；测试环境可直接使用默认配置。集群中每个节点都需要单独配置 RDB。

#### AOF配置过程

**一、AOF 基本配置（redis.conf）**

1.开启 AOF（默认关闭）

```ini
appendonly yes  # 默认为no，改为yes开启AOF持久化，开启后，Redis 会将所有写命令（如set、hset等）追加到 AOF 文件中。
```

2.AOF 文件名称

```ini
appendfilename "appendonly.aof"  # AOF文件的名称，默认存储在Redis数据目录
```

3.同步策略（关键参数，影响性能与安全性）

```ini
# appendfsync always    # 每次写命令都立即同步到磁盘（最安全，性能最差）
appendfsync everysec    # 每秒同步一次（默认值，平衡安全性和性能）
# appendfsync no        # 由操作系统决定何时同步（性能最好，安全性最差）
```

4.AOF 重写相关配置

AOF 文件会随时间变大（重复命令堆积），Redis 通过 “重写” 机制压缩文件，保留最小命令集。

```ini
# 自动重写触发条件1：AOF文件当前大小超过上次重写后大小的100%（即翻倍）
auto-aof-rewrite-percentage 100

# 自动重写触发条件2：AOF文件大小至少达到64MB（避免小文件频繁重写）
auto-aof-rewrite-min-size 64mb
```

* 两个条件同时满足时，Redis 会自动触发 AOF 重写。
* 也可手动触发重写：redis-cli bgrewriteaof（后台执行，不阻塞服务）。

5.重写期间是否暂停同步

```ini
no-appendfsync-on-rewrite no  # 默认为no
```

* no（默认）：重写期间继续按appendfsync策略同步，可能影响性能，但不丢失数据。
* yes：重写期间暂停同步，提升重写速度，但可能丢失这段时间的写命令。

6.AOF 文件损坏修复

```ini
aof-load-truncated yes  # 默认为yes
```

* 若 AOF 文件尾部损坏（如 Redis 意外宕机），启动时会忽略损坏部分并继续加载，保证 Redis 能正常启动。
* 若设为no，文件损坏时 Redis 会启动失败，需手动修复（如使用redis-check-aof工具）。

**二、配置步骤**

1. 打开 Redis 配置文件（通常为redis.conf）。
2. 搜索并修改上述参数（主要开启appendonly yes，调整appendfsync策略）。
3. 重启 Redis 使配置生效：

```ini
redis-cli shutdown  # 优雅关闭
redis-server /path/to/redis.conf  # 用新配置启动
```

**三、验证 AOF 配置**

1. 连接 Redis 客户端，执行写命令（如`set test aof`）。
2. 查看 AOF 文件内容（默认在 Redis 数据目录下的`appendonly.aof`），应包含刚执行的命令。
3. 检查配置是否生效：

```bash
redis-cli config get appendonly  # 应返回"yes"
redis-cli config get appendfsync  # 应返回配置的策略
```

### Redis主从复制

#### 主从架构

**主从架构特点：**

主从复制架构仅仅用来解决数据的冗余备份，从节点仅仅用来同步数据。

无法解决: 1.master节点出现故障的自动故障转移

![](https://note.youdao.com/yws/api/personal/file/915454C08E0C4ABDA4CBC2A5FB239494?method=download\&shareKey=3b9f4e39570bd5ea676632392595e9b8)

![](https://note.youdao.com/yws/api/personal/file/3F6AB64607AA432589F91B6791764331?method=download\&shareKey=8661786fec5aa41a3b4935ee1e9b3f50)

主从架构的**好处**：

*   读写分离(主服务器负责写，从服务器负责读)
*   高可用(某一台从服务器挂了，其他从服务器还能继续接收请求，不影响服务)
*   处理更多的并发量(每台从服务器**都可以接收读请求**，读QPS就上去了)

#### 复制功能

主从架构的特点之一：主服务器和从服务器的数据是**一致**的。

复制功能分为两个操作：

*   <font color="lighblue">同步(sync)</font>
    *   将从服务器的数据库状态**更新至**主服务器的数据库状态

*   <font color="lighblue">命令传播(command propagate)</font>
    *   主服务器的数据库状态**被修改**，导致主从服务器的数据库状态**不一致**，让主从服务器的数据库状态**重新回到一致状态**。

![](https://note.youdao.com/yws/api/personal/file/A0C624C3A6F24D85A5593F029C1A7031?method=download\&shareKey=ebd42206a16d3723a7ed07dca25f8b53)

![](https://note.youdao.com/yws/api/personal/file/F12C5A558A8C444F829936F0724A5489?method=download\&shareKey=85fab767d8552f10534b82a622316ce9)

从服务器对主服务器的**同步又可以分为两种情况**：

*   初次同步：从服务器**没有复制过任何**的主服务器，或者从服务器要复制的主服务器跟上次复制的主服务器**不一样**。
*   断线后同步：处于**命令传播阶段**的主从服务器因为**网络原因**中断了复制，从服务器通过**自动重连**重新连接主服务器，并继续复制主服务器

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

![](https://note.youdao.com/yws/api/personal/file/9B31B5C1E2F64ADABD1FEA4AC82301B8?method=download\&shareKey=009febd2a1693f5f68b845e8ecf667f7)

```properties
#2.启动3台机器进行测试
- cd /usr/redis/bin
- ./redis-server /root/master/redis.conf
- ./redis-server /root/slave1/redis.conf
- ./redis-server /root/slave2/ redis.conf
```

### 哨兵(Sentinel)机制

> <https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247484451&idx=1&sn=5495b1165954cd6b84b011489e04a66b&chksm=ebd74522dca0cc3416ab0ccd3a4ddb4ddd28290c9769596a069d81df3b03f4bad72e27d30a6d&token=620000779&lang=zh_CN&scene=21###wechat_redirect>

主服务器挂了，我们可以将从服务器**升级**为主服务器，等到旧的主服务器(挂掉的那个)重连上来，会将它(挂掉的主服务器)变成从服务器。哨兵就是带有自动故障转移功能的主从架构。可设置多个哨兵，防止脑裂。

无法解决：1.单节点并发压力问题  2.单节点内存和磁盘物理上限

![](https://note.youdao.com/yws/api/personal/file/6B0E6BEDA2F146448BFDF90561A7A31D?method=download\&shareKey=153cec115a91a574de66a07f5a851fff)

在正常的情况下，主从加哨兵(Sentinel)机制是这样子的：

![](https://note.youdao.com/yws/api/personal/file/98FD4FD575224CB2BA3CC8E8D8071D93?method=download\&shareKey=f9fa4ae579894f23ba9ea11ba499ad4a)

主服务器挂了，主从复制操作就中止了，并且哨兵系统是可以察觉出主服务挂了：

![](https://note.youdao.com/yws/api/personal/file/6A4522B7E07848D29FA9FBB74386260C?method=download\&shareKey=bde831d5c0fa7efeab4bebcfb99f2d69)

Redis提供哨兵机制可以将**选举**一台从服务器变成主服务器

![](https://note.youdao.com/yws/api/personal/file/38726A28A0424489A6CDBD6F1C270A4A?method=download\&shareKey=608ec7d1abacb3e3fdc4bb32be1b1d0a)

然后旧的主服务器如果重连了，会变成从服务器：

![](https://note.youdao.com/yws/api/personal/file/E66AEDB7DEA7479285C55C11BF543A7B?method=download\&shareKey=efbfc6caf110d88c7c19ea2feb999b77)

哨兵(Sentinel)机制主要用于实现Redis的**高可用性**，主要的功能：

*   Sentinel**不停地监控**Redis主从服务器是否正常工作
*   如果某个Redis实例有故障，那么哨兵负责**发送消息通知**管理员
*   如果主服务器挂掉了，会**自动**将从服务器提升为主服务器(包括配置都会修改)。
*   Sentinel可以作为**配置中心**，能够提供当前主服务器的信息。

Sentinel本质上只是一个**运行在特殊模式下的Redis服务器**。因为Sentinel做的事情和Redis服务器是不一样的，所以它们的初始化是有所区别的(比如，Sentinel在初始化的时候并不会载入AOF/RDB文件，因为Sentinel根本就不用数据库)。

#### 哨兵的选举机制

在 Redis 哨兵（Sentinel）机制中，当主服务器（Master）宕机后，多台从服务器（Slave）的推举过程（即 “故障转移”）由哨兵集群协同完成，核心是通过选举 “领头哨兵” 和筛选最优从节点两个步骤实现，确保最终只有一台从服务器被晋升为新主服务器。

**哨兵集群**

- **哨兵（Sentinel）**：监控主从节点状态的特殊 Redis 实例，通常以集群形式部署（至少 3 个节点，避免单点故障）。
- **通信机制**：哨兵之间通过 “发布 / 订阅” 机制交换信息，共同维护主从节点的健康状态。

#### 推举流程

**1.检测主节点宕机（主观下线 → 客观下线）**

* 主观下线（SDOWN）：单个哨兵发现主节点超过down-after-milliseconds时间未响应（如 30 秒），标记为主节点 “可能宕机”。
* 客观下线（ODOWN）：当超过quorum（配置的法定人数，如 2 个）哨兵都认为主节点 “主观下线”，则确认主节点 “真正宕机”，触发故障转移。

**2.选举 “领头哨兵”（Leader Sentinel）**

故障转移由**单个领头哨兵**负责执行（避免多个哨兵同时操作），选举规则基于 “Raft 协议简化版”：

1. 发起投票：最先发现主节点 “客观下线” 的哨兵向其他哨兵发送 “成为领头” 的请求。
2. 投票机制：
   * 每个哨兵在一轮选举中只能投 1 票，且优先投票给第一个请求的哨兵。
   * 若某哨兵获得超过半数（(哨兵总数/2)+1）的选票，则当选为领头哨兵。
   * 若一轮投票无结果（如平票），则等待随机时间后重新投票，直到选出领头哨兵。

**3.筛选最优从节点（晋升为新主）**

领头哨兵从所有健康的从节点中，按以下规则筛选出 “最优候选者”：

1. **排除不健康节点**：
   - 排除处于 “下线” 或 “断线” 状态的从节点。
   - 排除与原主节点断开连接时间过长（超过`down-after-milliseconds * 10`）的从节点（数据可能过时）。
2. **优先级排序（核心规则）**：
   - **步骤 1：比较slave-priority（从节点优先级）**
     配置文件中`slave-priority`值越高的从节点，优先级越高（默认 100）。若某从节点优先级为 0，则永远不会被选中。
   - **步骤 2：比较复制偏移量（replication offset）**
     若优先级相同，选择复制原主节点数据最完整的从节点（偏移量越大，数据越新）。
   - **步骤 3：比较运行 ID（runid）**
     若前两项相同，选择运行 ID 最小的从节点（随机但可重复的规则）。

**4. 执行故障转移**

1. 领头哨兵向 “最优从节点” 发送`slaveof no one`命令，使其晋升为新主节点。
2. 向其他从节点发送`slaveof 新主节点IP:端口`命令，让它们从新主节点同步数据。
3. 更新所有哨兵的配置，记录新主节点信息，并通知客户端（如通过发布 / 订阅机制）。

**总结**

Redis 哨兵的推举逻辑通过 “客观下线确认→领头哨兵选举→从节点优先级筛选” 三步，确保在主节点宕机后，能快速选出数据最完整、最健康的从节点作为新主，且整个过程由哨兵集群自动完成，无需人工干预。这一机制保证了 Redis 主从架构的高可用性，是中小规模 Redis 集群常用的容灾方案。

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

![](https://note.youdao.com/yws/api/personal/file/0BFF65B195E741A7B85AB53845EE58DE?method=download\&shareKey=f41414438a45785da80d7462b32843c5)

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

![](https://note.youdao.com/yws/api/personal/file/BE8A60F50A094CD194B015BAD84CC565?method=download\&shareKey=ede8c71f84c099792d77497da8890d6f)

主从+哨兵架构可以说Redis是高可用的，但要清楚的是：Redis还是会**丢失数据**的。

**丢失数据有两种情况：**

*   异步复制导致的数据丢失

    *   **有部分数据还没复制到从服务器，主服务器就宕机了**，此时这些部分数据就丢失了。

*   脑裂导致的数据丢失

    *   有时候主服务器脱离了正常网络，跟其他从服务器不能连接。此时哨兵可能就会**认为主服务器下线了**(然后开启选举，将某个从服务器切换成了主服务器)，但是实际上主服务器还运行着。这个时候，集群里就会有两个服务器(也就是所谓的脑裂)。



    *   虽然某个从服务器被切换成了主服务器，但是可能客户端**还没来得及切换到新的主服务器**，客户端还继续写向旧主服务器写数据。旧的服务器重新连接时，会作为从服务器复制新的主服务器(这意味着旧数据丢失)。

#### redis面试题

![](https://note.youdao.com/yws/api/personal/file/9400E8C9419A4454B0C5E8E462D076B0?method=download\&shareKey=c50cda47a5d0f23da4bcfbdbf1528665)

![](https://note.youdao.com/yws/api/personal/file/5A139A125F5D4EB7864147C557561327?method=download\&shareKey=3818b78dff86a6841b7f267ccd0b697d)

![](https://note.youdao.com/yws/api/personal/file/88037224C4C843799B0116B26F98C164?method=download\&shareKey=493c23733b63ca33586655fd7d13df67)

![](https://note.youdao.com/yws/api/personal/file/27191F99158849CFACCF6FFD93BE65EB?method=download\&shareKey=7fdcf2bc1cf8c55ea6719f65f9f2398d)

#### Redis 的淘汰策略有哪些

| 策略              | 描述                                                |
| :-------------- | :------------------------------------------------ |
| volatile-lru    | 从已设置过期时间的 KV 集中优先对最近最少使用(less recently used)的数据淘汰 |
| volitile-ttl    | 从已设置过期时间的 KV 集中优先对剩余时间短(time to live)的数据淘汰        |
| volitile-random | 从已设置过期时间的 KV 集中随机选择数据淘汰                           |
| allkeys-lru     | 从所有 KV 集中优先对最近最少使用(less recently used)的数据淘汰       |
| allKeys-random  | 从所有 KV 集中随机选择数据淘汰                                 |
| noeviction      | 不淘汰策略，若超过最大内存，返回错误信息                              |

### Redis分布式锁

Redis 分布式锁是利用 Redis 的特性在分布式系统中实现的一种互斥锁机制，用于解决多个独立服务（或进程）对共享资源的并发访问问题，确保同一时间只有一个服务能操作资源，从而保证数据一致性。

#### 什么是 Redis 分布式锁？

在单体应用中，我们可以用本地锁（如 Java 的`synchronized`、`ReentrantLock`）解决多线程并发问题。但在分布式系统中，服务部署在多个节点上（如多台服务器、容器），本地锁只能控制单个节点内的线程，无法跨节点生效。

Redis 分布式锁的核心思想是：**通过 Redis 的一个共享 key 作为 “锁标识”**，多个服务竞争这个 key，抢到 key 的服务获得锁，完成操作后释放锁，其他服务则等待或重试。

#### Redis 分布式锁的作用

分布式系统中，多个服务同时操作共享资源（如数据库、缓存、消息队列）时，可能出现数据不一致问题：

*   秒杀场景：多个服务同时扣减库存，导致超卖；
*   订单支付：多个服务同时处理同一订单的支付回调，导致重复扣款；
*   缓存更新：多个服务同时更新缓存，导致旧数据覆盖新数据。

Redis 分布式锁的作用就是**保证这些场景下的 “互斥性”**，确保同一时间只有一个服务能操作资源，避免并发安全问题。

#### Redis 分布式锁的具体实现（核心逻辑）

实现分布式锁需满足 3 个核心条件：

**互斥性**：同一时间只有一个服务能获取锁；
**安全性**：锁只能被持有它的服务释放，不能被其他服务误删；
**避免死锁**：即使持有锁的服务崩溃，锁也能自动释放（防止永久阻塞）。

### Redisson

Redisson 是一个基于 Redis 的 Java 分布式和服务化组件库，它不仅提供了 Redis 的 Java 客户端功能，还封装了分布式环境下的常用工具类（如锁、集合、队列等）和服务（如缓存、调度、事件通知等）。其核心设计理念是 “关注点分离”，让开发者更专注于业务逻辑，而非底层分布式细节。

**核心功能**

Redisson 的功能可分为两大类\*\*：分布式数据结构\*\* 和 **分布式服务**。

1.  分布式数据结构
    *   **基础类型**：`Map`、`List`、`Set`、`Queue`、`Deque` 等，支持线程安全的分布式操作。
    *   高级类型
        *   **分布式锁**：`RLock`（可重入锁）、`RFairLock`（公平锁）、`RedLock`（红锁，多节点强一致性）。
        *   **读写锁**：`RReadWriteLock`，支持多读单写。
        *   **信号量**：`RSemaphore`，控制资源访问数量。
        *   **闭锁**：`RCountDownLatch`，等待多个线程完成操作。
        *   **布隆过滤器**：`RBloomFilter`，高效判断元素是否存在。
2.  分布式服务
    *   **缓存服务**：集成 Spring Cache，支持分布式缓存。
    *   **调度服务**：`RScheduler`，实现分布式定时任务。
    *   **远程服务**：`RRemoteService`，支持 RPC 调用。
    *   **发布/订阅**：`RTopic`，实现分布式事件通知。

#### 应用场景

**分布式锁**

**场景**：秒杀系统、订单防重、分布式事务协调。

```java
RLock lock = redisson.getLock("orderLock");
try {
    lock.lock(10, TimeUnit.SECONDS); // 获取锁，超时时间 10 秒
    // 执行业务逻辑（如查询订单、插入新订单）
} finally {
    lock.unlock(); // 释放锁
}
```

**分布式缓存**

**场景**：缓存热点数据，减轻数据库压力。

```java
@Cacheable(value = "userCache", key = "#id")
public User getUserById(Long id) {
    return userRepository.findById(id).orElse(null);
}
```

**分布式消息队列**

**场景**：异步任务处理、解耦系统。

```java
//创建目标队列
RBlockingQueue<String> queue = redisson.getBlockingQueue("taskQueue");
//创建延迟队列（绑定到目标队列）
RDelayedQueue<Long> delayedQueue = redissonClient.getDelayedQueue(queue);
queue.put("task1"); // 生产者放入任务
String task = queue.take(); // 消费者取出任务
```

**分布式任务调度**

```java
RScheduledExecutorService scheduler = redisson.getExecutorService("myScheduler");
scheduler.scheduleAtFixedRate(() -> {
    System.out.println("定时任务执行: " + LocalDateTime.now());
}, 0, 1, TimeUnit.SECONDS);
```

#### Redisson 与 Redis 分布式锁的对比

| **对比维度**   | **Redis 原生分布式锁**                | **Redisson 分布式锁**                |
| ---------- | ------------------------------- | -------------------------------- |
| **实现方式**   | 手动执行 `SETNX` 和 `EXPIRE` 命令，非原子性 | 通过 `RLock` 接口封装，内部使用 Lua 脚本保证原子性 |
| **可重入性**   | 不支持，同一线程重复加锁会死锁                 | 支持，同一线程可多次加锁                     |
| **自动续租**   | 不支持，需手动处理锁超时                    | 支持，通过看门狗机制自动延长锁有效期               |
| **公平性**    | 不支持，依赖客户端实现顺序                   | 提供公平锁（`RFairLock`），按请求顺序获取锁      |
| **多节点一致性** | 单节点，需自行实现 RedLock 算法            | 直接提供 `RedLock` 实现，支持多节点强一致性      |
| **易用性**    | 需手动编写锁获取/释放逻辑                   | 提供简单 API（如 `lock()`/`unlock()`）  |
| **性能**     | 依赖网络和命令执行效率                     | 优化了网络通信和序列化，性能更高                 |

总结：
Redis 原生锁：适合简单场景，需开发者自行处理锁超时、重试等逻辑。
Redisson 锁：适合复杂分布式场景，提供了可重入、公平锁、自动续租等高级特性，极大简化了开发。

#### **Redisson 分布式锁的完整流程**

```java
// 1. 配置 Redisson 客户端
Config config = new Config();
config.useSingleServer().setAddress("redis://127.0.0.1:6379");
RedissonClient redisson = Redisson.create(config);

// 2. 获取分布式锁
RLock lock = redisson.getLock("myLock");

// 3. 加锁（可重入、自动续租）
try {
    lock.lock(30, TimeUnit.SECONDS); // 锁自动续租，业务执行时间可超过 30 秒
    // 执行业务逻辑
    System.out.println("获取锁成功，执行业务...");
} finally {
    lock.unlock(); // 释放锁
    System.out.println("释放锁");
}

// 4. 关闭 Redisson 客户端
redisson.shutdown();
```

Redisson 是 Redis 的 Java 高级客户端，通过封装分布式对象和服务，简化了分布式系统的开发。在分布式锁场景中，它比 Redis 原生实现更安全、易用，支持可重入、公平锁、自动续租等特性。对于需要处理高并发、数据一致性的 Java 应用，Redisson 是一个值得推荐的解决方案。

### 集群

Redis 集群部署主要用于解决单节点性能瓶颈和高可用问题，官方推荐的集群方案是Redis Cluster，采用分片（Sharding）+ 主从复制（Master-Slave）+ 哨兵（Sentinel）机制，支持自动分片、故障转移和水平扩展。

#### 集群核心特性

1. **分片存储**：将数据分散到 16384 个哈希槽（Hash Slot）中，每个节点负责一部分槽位，实现数据分片。
2. **主从架构**：每个主节点（Master）可配置多个从节点（Slave），从节点复制主节点数据，提供读负载和故障转移能力。
3. **自动故障转移**：当主节点宕机，其从节点会自动晋升为主节点，保证集群可用性。
4. **水平扩展**：支持动态添加 / 删除节点，自动重新分配槽位。

#### 集群部署步骤

**以 6 节点为例：3 主 3 从**

1.环境准备

- 服务器：至少 3 台机器（或同一机器的不同端口，推荐生产环境用独立机器）。
- Redis 版本：需 2.8 以上，推荐 5.0+（支持集群命令简化）。
- 关闭防火墙或开放 Redis 端口（如 6379-6384）及集群总线端口（端口 + 10000，如 16379-16384）。

2.配置节点

在每个节点上创建 Redis 配置文件（以`redis-6379.conf`为例），核心配置如下：

```yml
# 端口号（每个节点不同，如6379、6380、6381、6382、6383、6384）
port 6379
# 开启集群模式
cluster-enabled yes
# 集群配置文件（自动生成，记录槽位和节点信息）
cluster-config-file nodes-6379.conf
# 节点超时时间（毫秒，超过此时间视为节点宕机）
cluster-node-timeout 15000
# 持久化（可选，推荐开启）
appendonly yes
# 绑定IP（生产环境填实际IP，允许远程访问）
bind 0.0.0.0
# 密码（可选，所有节点需统一密码）
requirepass yourpassword
masterauth yourpassword
```

将配置文件复制到 6 个节点（端口依次递增），修改对应`port`和`cluster-config-file`名称。

3.启动所有节点

```bash
redis-server redis-6379.conf
redis-server redis-6380.conf
redis-server redis-6381.conf
redis-server redis-6382.conf
redis-server redis-6383.conf
redis-server redis-6384.conf
```

4.创建集群（Redis 5.0 + 简化命令）

```bash
# 语法：redis-cli --cluster create 主节点1 主节点2 主节点3 从节点1 从节点2 从节点3 --cluster-replicas 1
redis-cli -a yourpassword --cluster create 192.168.1.10:6379 192.168.1.11:6380 192.168.1.12:6381 192.168.1.10:6382 192.168.1.11:6383 192.168.1.12:6384 --cluster-replicas 1
```

- `--cluster-replicas 1`：表示每个主节点对应 1 个从节点。
- 执行后，Redis 会自动分配哈希槽（3 个主节点平均分配 16384 个槽），并建立主从关系。

5.验证集群状态

```bash
# 连接节点
redis-cli -h 192.168.1.10 -p 6379 -a yourpassword
# 查看集群状态（返回ok表示正常）
cluster info
# 查看节点列表（包括主从关系和槽位分配）
cluster nodes
```

#### 核心原理

1. **数据分片机制**
   * 数据存储时，Redis 通过CRC16(key) % 16384计算键对应的哈希槽，将数据存储到负责该槽的主节点。
   * 客户端请求时，会自动路由到对应槽的节点（若连接的节点不负责该槽，会返回MOVED重定向指令）。

2. **故障转移流程**
* 当主节点宕机，其从节点通过心跳检测发现主节点不可达。
* 从节点向集群其他主节点发起选举请求，获得多数票后晋升为主节点。
  新主节点接管原主节点的哈希槽，并通知所有节点更新集群信息。
3. **集群扩容 / 缩容**
* 扩容：新增主从节点 → 加入集群 → 重新分配槽位（redis-cli --cluster reshard）。
* 缩容：将待删除节点的槽位迁移到其他节点 → 移除节点（redis-cli --cluster del-node）

#### 注意

1. 生产环境建议每个主节点至少 1 个从节点，且主从节点分布在不同物理机。
2. 集群节点数量建议为奇数（便于选举投票），最小 3 主 3 从。
3. 避免在集群中使用KEYS、FLUSHALL等阻塞命令，可能导致所有节点卡顿。
4. 若需兼容旧版本客户端，可开启cluster-require-full-coverage no（允许部分槽不可用时集群仍可用）。

### Redis的使用

#### Redis的安装和使用

**配置redis的环境变量，Path路径中添加redis文件夹路径**

#### Redis命令使用参考

> <https://www.bilibili.com/video/BV1jD4y1Q7tU?p=12>

#### 开启Redis

*   启动Redis

    *
        ```java
        redis-server
        ```

*   指定配置文件

    *
              ./redis-server /etc/redis/6379.conf

    *
              redis-cli -p 6380

![](https://note.youdao.com/yws/api/personal/file/4AEC0ED6538A4B6496E92CC584B8274A?method=download\&shareKey=5b802c9857d13525eaee3691af7e5324)

启动Redis客户端

*
    ```cmd
    redis-cli
    ```

![](https://note.youdao.com/yws/api/personal/file/7B3497F4A2034789AAC40E34D4720AE9?method=download\&shareKey=5f565ccaa9dfc3f1820b6dd8143edd05)

连接客户端

![](https://note.youdao.com/yws/api/personal/file/B998DDBB9855486CA7346D570882E57E?method=download\&shareKey=4f29808d4c7516eb3fd1f9956b6b0091)

#### Redis事务

*   启动事务：MULTI

*   执行事务：EXEC

![](https://note.youdao.com/yws/api/personal/file/6F682BDFB7BE4BA697E6C4AC63D386EA?method=download\&shareKey=1fa526f4035634d3a51072621e51de1e)

#### 消息发布

![](https://note.youdao.com/yws/api/personal/file/AD5D18D39AC94E4F99BA4FA687025324?method=download\&shareKey=b8907ab6785186c6753a9dcd473b3722)

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

![](https://note.youdao.com/yws/api/personal/file/A8A3A0B5613844398B778083005CFF79?method=download\&shareKey=0f95310e7366f2b00cf5137e5588f4ea)

#### String类型

![](https://note.youdao.com/yws/api/personal/file/91024A389FA1454BAD154A410E3B86E9?method=download\&shareKey=c0f9afcfeceb41273245b5cc4a87f9ad)

常用操作命令

| 命令                        | 说明                      |
| ------------------------- | ----------------------- |
| set                       | 设置一个key/value           |
| get                       | 根据key获得对应的value         |
| mset                      | 一次设置多个key value         |
| mget                      | —次获得多个key的value         |
| getset                    | 获得原始key的值，同时设置新值        |
| strlen                    | 获得对应key存储value的长度       |
| append                    | 为对应key的value追加内容        |
| getrange索引0开始             | 截取value的内容              |
| setex                     | 设置一个key存活的有效期（秒)        |
| psetex                    | 设置一个key存活的有效期（毫秒)       |
| setnx                     | 存在不做任何操作,不存在添加          |
| msetnx原子操作(只要有一个存在不做任何操作) | 可以同时设置多个key,只有有一个存在都不保存 |
| decr                      | 进行数值类型的-1操作             |
| decrby                    | 根据提供的数据进行减法操作           |
| Incr                      | 进行数值类型的+1操作             |
| incrby                    | 根据提供的数据进行加法操作           |
| lncrbyfloat               | 根据提供的数据加入浮点数            |

![](https://note.youdao.com/yws/api/personal/file/6BC6CDACB74946898D208A70E2FA18A1?method=download\&shareKey=04d99ce5f7a911e5ee433cab7ef7c093)

#### List类型

元素有序且可重复

![](https://note.youdao.com/yws/api/personal/file/2051F5FBE2534AEEAE3FCBC921FC2A36?method=download\&shareKey=407a191c87b53c7abec03b0a875bc047)

常用操作命令

| 命令      | 说明                                                                    |
| ------- | --------------------------------------------------------------------- |
| lpush   | 将某个值加入到一个key列表头部                                                      |
| lpushx  | 同lpush,但是必须要保证这个key存在                                                 |
| rpush   | 将某个值加入到一个key列表末尾                                                      |
| rpushx  | 同rpush,但是必须要保证这个key存在                                                 |
| lpop    | 返回和移除列表左边的第一个元素                                                       |
| rpop    | 返回和移除列表右边的第一个元素                                                       |
| lrange  | 获取某一个下标区间内的元素，lrange key start end                                    |
| llen    | 获取列表元素个数                                                              |
| lset    | 设置某一个指定索引的值(索引必须存在)                                                   |
| lindex  | 获取某一个指定索引位置的元素                                                        |
| lrem    | 删除重复元素                                                                |
| ltrim   | 保留列表中特定区间内的元素                                                         |
| linsert | 在某一个元素value1之前\|之后插入新元素value2，linsert key before\|after value1 value2 |

![](https://note.youdao.com/yws/api/personal/file/886B631CBA614597A5B1D6C1B3F8285E?method=download\&shareKey=4a3d29d0b32cab2eecd795d1cf524265)

#### Set类型

元素无序 不可以重复

**内存存储模型**

![](https://note.youdao.com/yws/api/personal/file/1C9B97948F6542B990D99B1945B6B6F6?method=download\&shareKey=fe1743e74b100490d24da9d4a3893ebd)

常用操作命令

| 命令          | 说明                  |
| ----------- | ------------------- |
| sadd        | 为集合添加元素             |
| smembers    | 显示集合中所有元素无序         |
| scard       | 返回集合中元素的个数          |
| spop        | 随机返回一个元素并将元素在集合中删除  |
| smove       | 从一个集合中向另一个集合移动元素    |
| srem        | 从集合中删除一个元素          |
| sismember   | 判断一个集合中是否含有这个元素     |
| srandmember | 随机返回元素              |
| sdiff       | 去掉第一个集合中其它集合含有的相同元素 |
| sinter      | 求交集                 |
| sunion      | 求和集                 |

![](https://note.youdao.com/yws/api/personal/file/6F83838AD3BD4EE7A311B59DCEB95D35?method=download\&shareKey=864b851ebe09478e790ff08c31a61062)

#### ZSet类型

可排序的set集合，排序 不可重复

![](https://note.youdao.com/yws/api/personal/file/82BC8728EDD147A99644B6BA4CEE1716?method=download\&shareKey=90b58ade6ec3d1856a599f99b2b2c8c5)

常用操作命令

| 命令                  | 说明             |
| ------------------- | -------------- |
| zadd                | 添加一个有序集合元素     |
| zcard               | 返回集合的元素个数      |
| zrange升序zrevrange降序 | 返回一个范围内的元素     |
| zrangebyscore       | 按照分数查找一个范围内的元素 |
| zrank               | 返回排名           |
| zrevrank            | 倒序排名           |
| zscore              | 显示某一个元素的分数     |
| zrem                | 移除某一个元素        |
| zincrby             | 给某个特定元素加分      |

![](https://note.youdao.com/yws/api/personal/file/6D386F3B333A40CD947616C53B387258?method=download\&shareKey=6d13fe0cfa3c30d40a43401b7b985666)

#### Hash类型

value是一个map结构，存在key value key无序的

![](https://note.youdao.com/yws/api/personal/file/1D6A31E661294855AD6FCE466AE16199?method=download\&shareKey=87b9944732f9b8539bfbceea27aa0cb7)

常用操作命令

| 命令           | 说明              |
| ------------ | --------------- |
| hset         | 设置一个key/value对  |
| hget         | 获得一个key对应的value |
| hgetall      | 获得所有的key/value对 |
| hdel         | 删除某一个key/value对 |
| hexists      | 判断一个key是否存在     |
| hkeys        | 获得所有的key        |
| hvals        | 获得所有的value      |
| hmset        | 设置多个key/value   |
| hmget        | 获得多个key的value   |
| hsetnx       | 设置一个不存在的key的值   |
| hincrby      | 为value进行加法运算    |
| hincrbyfloat | 为value加入浮点值     |

![](https://note.youdao.com/yws/api/personal/file/0E5A5BC4CD214164B1D9D2C0071A24BD?method=download\&shareKey=99c87c985fc1ead4ca092207d9b91614)

### 连接Redis数据库

#### Java连接Redis数据库(jedis)

添加jedis-2.9.0.jar到项目中

*   Redis中java使用

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

*   特点
    *   异常快
    *   支持丰富的数据类型
    *   操作具有原子性
    *   多实用工具：缓存、消息队列、短期数据

*   发消息（同步）

    *   订阅：

        *
            ```cmd
            SUBSCRIBE redis123
            ```

    *   发布：

        *
            ```cmd
            PUBLISH redis123 "hello world"
            ```

*   关闭redis服务器命令
    *   打开客户端（redis-cli）
    *   shutdown

*   获取连接

    *
        ```java
         Jedis jedis=new Jedis("localhost",6379);
        ```

*   指定库

    *
        ```java
        jedis.select(1);
        ```

*   String类型

    *   设置：set（key,value）
    *   取值：get（key）

*   实例：

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

*   List类型

    *   设置：lpush（key，value1）； lpush（key，value2）；

    *   取值：lrange（key，开始范围，结束范围）    集合类型接收

    *   实例

        *
            ```java
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

*   Set类型

    *   设置：sadd（key，value1，value2，value3）

    *   取值：smembers（key）

    *   实例

        *
            ```java
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

*   hash类型

    *   设置：hmset()
    *   取值：hgetAll()

#### SpringBoot引入Redis

<https://www.bilibili.com/video/BV1jD4y1Q7tU?p=12>

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
		stringRedisTemplate.opsForHash().put("maps", "name", "张三");//创建一个hash类型并放入key value
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

![](https://note.youdao.com/yws/api/personal/file/56C519E6CC604BF781259684995E001A?method=download\&shareKey=c9bf171be7642b643c5449437b1cbf9e)

**解决Redis插入乱码**

<https://blog.csdn.net/LLLLLLL_LLLLLLL/article/details/124104800>

**解决JSONException报错**

报错信息：com.alibaba.fastjson.JSONException: autoType is not support. com.example.demo.entity.Role

![](https://note.youdao.com/yws/api/personal/file/9CF14CFF8F0E41CFB4E63F9EFDA0D09C?method=download\&shareKey=4eaf2d60fb66a3a9970c2b413b350e9e)

\-Dfastjson.parser.autoTypeAccept=com.taobao.pac.client.sdk.dataobject.,com.example.demo

com.example.demo为项目路径

**注意**：

1.  针对于日后处理key value都是 string使用stringRedisTemplate
2.  针对于日后处理的key value存在对象使用RedisTemplate
3.  针对于同一个key多次操作可以使用boundXXxOps () value List Set set Hash的api简化书写

### Redis中分布式缓存实现

1.  什么是缓存(Cache)

    定义:就是计算机内存中一段数据

2.  内存中数据特点
    *   读写快
    *   断电立即丢失

3.  缓存解决了什么问题?
    *   提高网站吞吐量提高网站运行效率
    *   核心解决问题:缓存的存在是用来减轻数据库访问压力

4.  既然缓存能提高效率，那项目中所有数据加入缓存岂不是更好?
    注意:使用缓存时一定是数据库中数据极少发生修改,更多用于查询这种情况

5.  本地缓存和分布式缓存区别?
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

![](https://note.youdao.com/yws/api/personal/file/106BCF7E46E5411EAD769707FBB9A4DD?method=download\&shareKey=3e864907d7f4682439d797a7e508dd40)

![](https://note.youdao.com/yws/api/personal/file/87344AC7CE0649B4B2AF911AC09EF574?method=download\&shareKey=db565a5e6e1d2edfcea14a6ab64c8d04)

上面的方法只适用于单表查询

在mybatis的缓存中如何要解决关联关系时更新缓存信息的问题?

\<cache-ref/> 用来将多个具有关联关系查询缓存放在一起处理

```xml
<mapper namespace="com.example.demo.mapper.RoleMapper">
<!-- 开启mybatis二级缓存-->
<!--    <cache type="com.example.demo.cache.RedisCache"/>-->

<cache-ref namespace="com.example.demo.mapper.UserMapper"/>
```

无论哪个数据进行了增删改操作，都会清空缓存

#### 缓存优化

对放入redis中key进行优化: key的长度不能太长

算法\:MD5处理加密特点:

1.  一切文件字符串等经过md5处理之后都会生成32位16进制字符串
2.  不同内容文件经过md5进行加密，加密结果一定不一致
3.  相当内容文件多次经过md5生成结果始终─致

推荐:在redis整合mybatis过程中建议将key进行md5优化处理

<https://www.bilibili.com/video/BV1jD4y1Q7tU?p=23&spm_id_from=pageDriver>

### 安装redis

```yaml
# 解压安装包redis-5.0.5到/opt
# 复制redis.conf到/etc下
cp /opt/redis-5.0.5/redis.conf /etc/
# 打开 Redis 配置文件
vi /etc/redis.conf

# 查找并修改 dir 配置（按 /dir 搜索）
# 将 dir ./ 改为明确的目录，如 /var/lib/redis（已创建并设置权限的目录）
dir /var/lib/redis
# 确保目录存在且权限正确
mkdir -p /var/lib/redis
chown -R redis:redis /var/lib/redis  # 所有者为 Redis 运行用户
chmod -R 755 /var/lib/redis          # 读写执行权限
# 重启服务使配置生效
systemctl restart redis

# 检查服务状态
systemctl status redis
```

新建redis.service文件

```service
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
User=redis
Group=redis
ExecStart=/usr/local/bin/redis-server /etc/redis.conf
ExecStop=/usr/local/bin/redis-cli shutdown
Restart=always

[Install]
WantedBy=multi-user.target
```

```yaml
# 确认 Redis 可执行文件路径
which redis-server

# 若输出为空，检查是否安装
ls -l /usr/local/bin/redis-server  # 源码安装默认路径

# 检查用户是否存在
id redis

# 如果不存在，创建 redis 用户（不创建家目录）
useradd -r -s /bin/false redis

# 重载 systemd 配置
systemctl daemon-reload

# 尝试启动 Redis
systemctl start redis

# 检查状态
systemctl status redis
```

