# Nacos

> https://www.bilibili.com/video/BV18E411x7eT?p=96

[TOC]

## 注册中心

> [nacos视频笔记](https://blog.csdn.net/u011863024/article/details/114298282)

![](https://note.youdao.com/yws/api/personal/file/2A557901FC8640AFA14D01D150CDA045?method=download&shareKey=5486226e97c723f07f2ca2c77bce189e)

Nacos 的服务发现功能通过 “服务注册 - 服务发现 - 健康检查” 三个核心步骤，实现服务实例的动态管理。

**类似服务注册与发现服务：Eureka、Consul、Zookeeper**

#### 1. 服务注册（Service Registration）

服务提供者启动时，主动将自身信息注册到 Nacos 服务器，让服务消费者可感知其存在。

- **注册主体**：服务提供者（如 Spring Cloud 应用、Dubbo 应用等）。
- **注册信息**：包括服务名（`serviceName`，全局唯一标识服务）、实例 IP、端口、权重、元数据（如版本号、环境标签）等。
- 注册方式：
  - 基于 SDK：服务通过 Nacos 客户端 SDK（如`nacos-client`）调用`registerInstance`接口主动注册。
  - 基于配置：在服务配置文件中声明 Nacos 服务器地址，框架（如 Spring Cloud Alibaba）自动触发注册（无需手动调用 SDK）。
- **服务端处理**：Nacos 服务器接收注册请求后，将服务实例信息存储在内存（默认）或持久化到数据库（如 MySQL，支持高可用），并维护服务与实例的映射关系（`服务名 -> 实例列表`）。

#### 2. 服务发现（Service Discovery）

服务消费者需要调用服务时，从 Nacos 服务器获取目标服务的可用实例列表，实现动态寻址。

- **发现触发**：服务消费者启动时或调用目标服务前，主动发起查询。
- 获取方式：
  - 拉取（Pull）：消费者通过 SDK 调用`getAllInstances`接口，主动从 Nacos 服务器拉取服务实例列表（支持按服务名、分组、命名空间过滤）。
  - 推送（Push）：Nacos 服务器通过 “长连接” 维护与消费者的通信，当服务实例信息（如新增、下线、权重变化）发生变更时，主动向消费者推送最新列表（减少无效查询，提升实时性）。
- **负载均衡**：消费者获取实例列表后，结合负载均衡策略（如轮询、随机、权重等）选择一个实例发起调用（Nacos 可集成 Ribbon、Spring Cloud LoadBalancer 等组件实现）。

#### 3. 健康检查（Health Check）

Nacos 通过健康检查确保服务实例的可用性，避免消费者调用无效服务。

- **检查目标**：识别 “宕机”“异常” 的服务实例，将其从可用列表中移除。
- 检查方式：
  - 客户端主动上报（默认）：服务提供者定期向 Nacos 服务器发送 “心跳包”（默认 5 秒一次），若超过心跳超时时间（默认 15 秒）未收到心跳，Nacos 标记实例为 “不健康”。
  - 服务端主动探测：针对无法主动上报心跳的服务（如数据库、缓存），Nacos 支持 TCP、HTTP、MySQL 等协议的主动探测（如 TCP 探测端口是否存活，HTTP 探测接口返回码是否为 200）。
- **实例恢复**：当不健康实例恢复正常后（如重新发送心跳、探测成功），Nacos 会将其重新加入可用列表，并通知消费者。

**配置注册**

application.yml(生产者)

```yml
server:
  port: 9001

spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

application.yml(消费者)

```yml
server:
  port: 83

spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848

#消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
service-url:
  nacos-user-service: http://nacos-payment-provider
```

![](https://note.youdao.com/yws/api/personal/file/A4E130F27E0740D7996DC6E7DF82C785?method=download&shareKey=3cff6e5c7a3c8218dd836f997037545a)

**Nacos支持AP和CP两种服务发现模式的切换**

* **AP 模式**（可用性优先）：当网络分区发生时，优先保证服务可用（允许短暂的数据不一致），适用于服务实例频繁变更的场景（如电商秒杀）。基于分布式哈希算法实现，类似 Eureka。
* **CP 模式**（一致性优先）：当网络分区发生时，优先保证数据一致性（可能暂时不可用），适用于需要强一致性的场景（如金融交易）。基于 Raft 协议实现，类似 Consul。

**何时选择使用何种模式?**

—般来说，如果不需要存储服务级别的信息且服务实例是通过nacos-client注册，并能够保持心跳上报，那么就可以选择AP模式。当前主流的服务如Spring cloud和Dubbo服务，都适用于AP模式，AP模式为了服务的可靠性而减弱了一致性，因此AP模式下只支持注册临时实例。

如果需要在服务级别编辑或者存储配置信息，那么CP是必须，K8S服务和DNS服务则适用于CP模式。CP模式下则支持注册持久化实例，此时则是以Raft协议为集群运行模式，该模式下注册实例之前必须先注册服务，如果服务不存在，则会返回错误。

用户可通过配置nacos.core.protocol指定模式，或按服务粒度动态切换。

切换命令：

```yml
curl -X PUT '$NACOS_SERVER:8848/nacos/v1/ns/operator/switches?entry=serverMode&value=CP
```

## 配置中心

springboot.yml

```yml
# nacos配置
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置

# ${spring.application.name}-${spring.profile.active}.${spring.cloud.nacos.config.file-extension}
# nacos-config-client-dev.yaml
```

application.yml

```yml
spring:
  profiles:
    active: dev # 表示开发环境
    #active: test # 表示测试环境
    #active: info
```

```
${prefix}-${spring-profile.active}.${file-extension}
```

- prefix默认为spring.application.name的值，也可以通过配置项spring.cloud.nacos.config.prefix来配置。
- spring.profile.active即为当前环境对应的 profile，详情可以参考 Spring Boot文档。注意：当spring.profile.active为空时，对应的连接符 - 也将不存在，datald 的拼接格式变成${prefix}.${file-extension}
- file-exetension为配置内容的数据格式，可以通过配置项spring .cloud.nacos.config.file-extension来配置。目前只支持properties和yaml类型。
- 通过Spring Cloud 原生注解@RefreshScope实现配置自动更新。

![](https://note.youdao.com/yws/api/personal/file/02A898B34912423E89A8F28D355628F2?method=download&shareKey=76695053d106b80423092654bf3d4140)

![](https://note.youdao.com/yws/api/personal/file/03E7B856C1D846098A49566FE61E19FD?method=download&shareKey=bdd959bbe3018df30f5fc49f585fa8a8)

![](https://note.youdao.com/yws/api/personal/file/28E33CDFAE3847CAA2E0889232F45591?method=download&shareKey=e7daa51ea5b997ae09097e71e938effb)

## 服务的多层级管理

Nacos 通过 “命名空间（Namespace）- 服务组（Group）- 服务（Service）- 实例（Instance）” 的四级模型管理服务，适配复杂场景。

* 命名空间：隔离不同环境（如开发、测试、生产），避免服务名冲突（默认命名空间为public）。
* 服务组：同一命名空间下，按业务场景分组服务（如order-service的payment组和refund组）。
* 服务：抽象的服务标识（如user-service），对应一组提供相同功能的实例。
* 实例：服务的具体部署节点（如192.168.1.100:8080）。

### NameSpace命名空间

隔离不同环境（如开发、测试、生产），避免服务名冲突（默认命名空间为`public`）。

![](https://note.youdao.com/yws/api/personal/file/506D475C883D4E61A703214C42138A64?method=download&shareKey=09b87343e4d3bdf4c3165666381737dc)

![](https://note.youdao.com/yws/api/personal/file/542F0A4BD4E14B7D9DDA8294F2ECE16F?method=download&shareKey=7d1027d0b2f00ee386ee8f10537ed091)

```yml
# nacos配置
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置
        group: DEFAULT_GROUP #指定分组
        namespace: e36bb5db-6b6c-4238-a50f-257f2bda2e9c #指定命名空间
        
spring:
 profiles:
   #active: dev # 表示开发环境
   #active: test # 表示测试环境
   active: info
```

### Group分组

新建文件，填写不同分组

```yml
# nacos配置
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置
        group: DEV_GROUP #指定分组

spring:
 profiles:
   #active: dev # 表示开发环境
   #active: test # 表示测试环境
   active: info
```

## Nacos集群

默认Nacos使用嵌入式数据库derby实现数据的存储。所以，如果启动多个默认配置下的Nacos节点，数据存储是存在一致性问题的。为了解决这个问题，Nacos采用了集中式存储的方式来支持集群化部署，目前只支持MySQL的存储。

* 数据同步：集群节点间通过 Raft 协议同步服务元数据，避免单点故障。
* 负载均衡：客户端通过 Nacos 服务器列表（如127.0.0.1:8848,127.0.0.1:8849）随机选择节点通信，分散压力。
* 自动故障转移：当某节点宕机，客户端会自动切换到其他健康节点，不影响服务注册与发现。

Nacos支持三种部署模式:

* 单机模式-用于测试和单机试用。
* 集群模式-用于生产环境，确保高可用。
* 多集群模式-用于多数据中心场景。
  

#### Nacos持久化

Nacos\nacos\conf\application.properties

```properties
#*************** Config Module Related Configurations ***************#
### If use MySQL as datasource:
spring.datasource.platform=mysql

### Count of DB:
db.num=1

### Connect URL of DB:
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user.0=root
db.password.0=123456

### Connection pool configuration: hikariCP
db.pool.config.connectionTimeout=30000
db.pool.config.validationTimeout=10000
db.pool.config.maximumPoolSize=20
db.pool.config.minimumIdle=2
```

在本地新建nacos数据库，运行nacos-mysql.sql文件

![](https://note.youdao.com/yws/api/personal/file/506B924020FB4CD48E20EC11F9181B90?method=download&shareKey=d4054f0515e76ebb1319dc3311155481)

## Nacos部署

```java
//启动nacos的命令，在bin文件目录下输入命令
.\startup.cmd -m standalone
```

![](https://note.youdao.com/yws/api/personal/file/93F3965B1B6D4E5D870E417A47089C81?method=download&shareKey=796472ddaec6f0f7d3ed0e0928052712)

可使用http://localhost:8848/nacos/index.html来访问，也可使用IP地址来访问

默认账号密码都为nacos

![](https://note.youdao.com/yws/api/personal/file/84452643E82C4106BC8FDE9D755C06DC?method=download&shareKey=1d7fdd68fffd48c73819c8dc9c36d11e)