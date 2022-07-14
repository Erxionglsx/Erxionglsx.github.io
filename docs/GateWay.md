## GateWay

> https://www.bilibili.com/video/BV1LQ4y127n4?p=35

![](https://note.youdao.com/yws/api/personal/file/319BEA91EBBD4FB3AC9795B65C11FC91?method=download&shareKey=00af76859b9c55d7dbb4920422eee716)

网关功能：

- 身份认证和权限校验
- 服务路由、负载均衡
- 请求限流

网关的作用:

- 对用户请求做身份认证、权限校验
- 将用户请求路由到微服务，并实现负载均衡
- 对用户请求做限流

#### 三大核心概念

* Route(路由) - 路由是构建网关的基本模块,它由ID,目标URI,一系列的断言和过滤器组成,如断言为true则匹配该路由；
* Predicate(断言) - 参考的是Java8的java.util.function.Predicate，开发人员可以匹配HTTP请求中的所有内容(例如请求头或请求参数),如果请求与断言相匹配则进行路由；
* Filter(过滤) - 指的是Spring框架中GatewayFilter的实例,使用过滤器,可以在请求被路由前或者之后对请求进行修改。

![](https://note.youdao.com/yws/api/personal/file/66D2BB1169AC4308BA72A103F92C23B3?method=download&shareKey=a8564c87da9abbaf55e1b6be58f54288)

引入相关依赖

```xml
<dependencies>
        <!--nacos服务注册发现依赖-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--网关gateway依赖-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
</dependencies>
```

gateway-dev.yaml

```yaml
server:
  port: 8190 #网关端口
spring:
  application:
    name: api-web-lsx #服务名称
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能，利用微服务名进行路由
      routes:
        - id: api-web-lsx  #路由id，自定义，只要唯一即可
        # uri: http ://127.0.0.1:8081 #路由的目标地址 http就是固定地址
          uri: lb://api-web-lsx #路由的目标地址lb就是负载均衡，后面跟服务名称
          predicates: #路由断言，也就是判断请求是否符合路由规则的条件
            - Path=/lsx/apicenter/** #这个是按照路径匹配,以lsx开头的请求都负载到api-web-lsx服务
          filters: #过滤
            - StripPrefix=1  
```

![](https://note.youdao.com/yws/api/personal/file/BB92E09C326A451C9F3894DE08961A89?method=download&shareKey=8342df64676351a8fc8b13d9065298dd)

![](https://note.youdao.com/yws/api/personal/file/F6CD0DE7AF9D411597C484FA7897A338?method=download&shareKey=6d1c640f56626d69aaf1d7ac887702bd)

访问示例：

```
// gateway访问
http://192.168.33.7:8001/lsx/apicenter/configDingDing/queryInterfaceConfigPageList
// 本地访问
http://localhost:8190/apicenter/configDingDing/queryInterfaceConfigPageList
```
