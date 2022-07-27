## OpenFeign

> https://www.bilibili.com/video/BV18E411x7eT?p=43

Feign是一个声明式WebService客户端。使用Feign能让编写Web Service客户端更加简单。它的使用方法是定义一个服务接口然后在上面添加注解。Feign也支持可拔插式的编码器和解码器。Spring Cloud对Feign进行了封装，使其支持了Spring MVC标准注解和HttpMessageConverters。Feign可以与Eureka和Ribbon组合使用以支持负载均衡。

#### Feign能干什么

Feign旨在使编写Java Http客户端变得更容易。

前面在使用Ribbon+RestTemplate时，利用RestTemplate对http请求的封装处理，形成了一套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义。在Feign的实现下，我们只需创建一个接口并使用注解的方式来配置它(以前是Dao接口上面标注Mapper注解,现在是一个微服务接口上面标注一个Feign注解即可)，即可完成对服务提供方的接口绑定，简化了使用Spring cloud Ribbon时，自动封装服务调用客户端的开发量。

**OpenFeign**是一种声明式、模板化的HTTP客户端。在Spring Cloud中使用OpenFeign，可以做到使用HTTP请求访问远程服务，就像调用本地方法一样的，开发者完全感知不到这是在调用远程方法，更感知不到在访问HTTP请求。

```java
@EnableDiscoveryClient
@EnableFeignClients(basePackages = {"com.tata.seller", "com.tata.price","com.tata.item"})
public class OrderApplication extends SpringBootServletInitializer implements WebApplicationInitializer {
    	public static void main(String[] args) {
		SpringApplication.run(OrderApplication.class, args);
	}
}
```

**@EnableFeignClients**申明该项目是Feign客户端，扫描对应的FeignClient。@EnableFeignClients注解告诉框架扫描所有使用注解@FeignClient定义的feign客户端，并把feign客户端注册到IOC容器中。

```java
@RequestMapping(value = "/ordercenter/ordCfmRepair")
@FeignClient(value = "order-lsx", path = "ordercenter/ordCfmRepair",contextId="IOrdCfmRepairServiceFacade")
public interface IOrdCfmRepairServiceFacade extends IBaseServiceFacade {
    @RequestMapping(value = "/queryOrdCfmRepairPageList", method = RequestMethod.POST)
    GridResponseDto<OrdCfmRepairResultDto> queryOrdCfmRepairPageList(@RequestBody QueryOrdCfmRepairParamDto queryOrdCfmRepairParamDto) throws Exception;
    ......
}

访问路径：/ordercenter/ordCfmRepair/方法名
例：http://127.0.0.1:8287/ordercenter/ordCfmRepair/queryOrdCfmRepairPageList
```

声明接口之后，在代码中通过@Resource注入之后即可使用。@FeignClient标签的常用属性如下：

- name：指定FeignClient的名称，如果项目使用了Ribbon，name属性会作为微服务的名称，用于服务发现
- url: url一般用于调试，可以手动指定@FeignClient调用的地址
- decode404:当发生http 404错误时，如果该字段位true，会调用decoder进行解码，否则抛出FeignException
- configuration: Feign配置类，可以自定义Feign的Encoder、Decoder、LogLevel、Contract
- fallback: 定义容错的处理类，当调用远程接口失败或超时时，会调用对应接口的容错逻辑，fallback指定的类必须实现@FeignClient标记的接口
- fallbackFactory: 工厂类，用于生成fallback类示例，通过这个属性我们可以实现每个接口通用的容错逻辑，减少重复的代码
- path: 定义当前FeignClient的统一前缀，当我们项目中配置了server.context-path,server.servlet-path时使用
- value="/xxx/xxx"就是我们服务方暴露的接口地址




