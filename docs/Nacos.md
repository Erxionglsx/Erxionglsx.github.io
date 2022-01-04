## Nacos

> https://www.bilibili.com/video/BV18E411x7eT?p=96

[TOC]

#### Nacos作为注册中心

![](https://note.youdao.com/yws/api/personal/file/2A557901FC8640AFA14D01D150CDA045?method=download&shareKey=5486226e97c723f07f2ca2c77bce189e)

#### Nacos部署

```java
//启动nacos的命令，在bin文件目录下输入命令
.\startup.cmd -m standalone
```

![](https://note.youdao.com/yws/api/personal/file/93F3965B1B6D4E5D870E417A47089C81?method=download&shareKey=796472ddaec6f0f7d3ed0e0928052712)

可使用http://localhost:8848/nacos/index.html来访问，也可使用IP地址来访问

默认账号密码都为nacos

![](https://note.youdao.com/yws/api/personal/file/84452643E82C4106BC8FDE9D755C06DC?method=download&shareKey=1d7fdd68fffd48c73819c8dc9c36d11e)

> [nacos视频笔记](https://blog.csdn.net/u011863024/article/details/114298282)

#### 注册中心

> F1键放大配置文件，ESC退出全屏

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

**Nacos支持AP和CP模式的切换**

C是所有节点在同一时间看到的数据是一致的;而A的定义是所有的请求都会收到响应。

何时选择使用何种模式?

—般来说，如果不需要存储服务级别的信息且服务实例是通过nacos-client注册，并能够保持心跳上报，那么就可以选择AP模式。当前主流的服务如Spring cloud和Dubbo服务，都适用于AP模式，AP模式为了服务的可能性而减弱了一致性，因此AP模式下只支持注册临时实例。

如果需要在服务级别编辑或者存储配置信息，那么CP是必须，K8S服务和DNS服务则适用于CP模式。CP模式下则支持注册持久化实例，此时则是以Raft协议为集群运行模式，该模式下注册实例之前必须先注册服务，如果服务不存在，则会返回错误。

切换命令：

```yml
curl -X PUT '$NACOS_SERVER:8848/nacos/v1/ns/operator/switches?entry=serverMode&value=CP
```

#### 配置中心

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

#### Group分组

新建文件，填写不同分组

![](C:\Users\erxio\AppData\Roaming\Typora\typora-user-images\1638963658245.png)

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

#### NameSpace命名空间

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

#### Nacos集群

默认Nacos使用嵌入式数据库derby实现数据的存储。所以，如果启动多个默认配置下的Nacos节点，数据存储是存在一致性问题的。为了解决这个问题，Nacos采用了集中式存储的方式来支持集群化部署，目前只支持MySQL的存储。

Nacos支持三种部署模式:

* 单机模式-用于测试和单机试用。
* 集群模式-用于生产环境，确保高可用。
* 多集群模式-用于多数据中心场景。
  







