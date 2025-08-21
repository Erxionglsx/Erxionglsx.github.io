Spring

> 代码参考：SpringTest
>
> [Spring入门](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247483942&idx=1&sn=f71e1adeeaea3430dd989ef47cf9a0b3&chksm=ebd74327dca0ca3141c8636e95d41629843d2623d82be799cf72701fb02a665763140b480aec&scene=21###wechat_redirect)

------

[TOC]

## 1.IOC

IOC：反转控制 -》 反射

容器创建对象，将创建对象的权利交给Spring容器，我们只需要从容器中取出对象来使用；

> [理解IOC和AOP](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247487013&idx=1&sn=f0d8c292738eb49bcd09cb2f6458dc69&chksm=ebd74f24dca0c632fa3ef8f205a2dd5c96531f78a68eae805e15b84de0b59774196a188aed14&token=306734573&lang=zh_CN#rd)

**ioc的思想最核心的地方**在于，资源不由使用资源的双方管理，而由不使用资源的第三方管理，这可以带来很多好处。

* 第一，将对象集中统一管理，便于修改
* 第二，降低了耦合度

优点：

* 对象和对象实现解耦
* Spring提供对很多组件的支持：Dao,Web,AOP,JEE

那么<font color="lighblue">控制反转</font>是什么意思呢？？？

**对象的创建交给外部容器完成，这个就做控制反转。**

- Spring使用控制反转来实现对象不用在程序中写死
- 控制反转解决对象处理问题【把对象交给别人创建】

那么对象的对象之间的<font color="lighblue">依赖关系</font>Spring是怎么做的呢？？

**依赖注入，dependency injection.**

- Spring使用依赖注入来实现对象之间的依赖关系
- 在创建完对象之后，对象的关系处理就是依赖注入

**控制反转是一种思想，依赖注入是其最典型的实现方法：对象无需自行创建或管理它们的依赖关系，依赖关系将被「自动注入」到需要它们的对象当中去。**

## 2.生命周期

Spring 生命周期全过程大致分为五个阶段：创建前准备阶段、创建实例阶段、依赖注入阶段、容器缓存阶段和销毁实例阶段。

BeanLifeCycle.java

```java
//bean的生命周期
public class BeanLifeCycle implements InitializingBean, DisposableBean {
    @Override
    public void destroy() throws Exception {
        System.out.println("bean已经被初始化");
    }
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("bean已经被销毁");
    }
    
    //注解方式
    //初始化
    @PostConstruct
    public void initBean(){
        System.out.println("--------bean被初始化------");
    }
    //销毁
    @PreDestroy
    public void destroyBean(){
        System.out.println("------------bean被销毁---------");
    }
}
```

spring-lifecycle.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <!--对象的实体bean-->
    <bean id="beanLifeCycle" class="com.huadi.lifecycle.BeanLifeCycle"/>
    <!--开启注解-->
    <context:annotation-config/>
</beans>
```

BeanlifeTest.java

```java
//测试生命周期
public class BeanlifeTest extends UnitTestBase {
    public BeanlifeTest() {
        super("spring-lifecycle.xml");
    }
    @Test
    public void testInterface(){
        BeanLifeCycle beanLifeCycle = super.getBean("beanLifeCycle");
    }
    //注解方式
    @Test
    public void testAnnotation(){
        BeanLifeCycle beanLifeCycle = super.getBean("beanLifeCycle");
    }
}
```

## 3.作用域

**在默认的情况下，Spring IoC 容器只会对一个 Bean 创建一个实例**，但有时候，我们希望能够通过 Spring IoC 容器获取多个实例，我们可以通过 `@Scope` 注解或者 `<bean>` 元素中的 `scope` 属性来设置。

| 作用域类别              | 描述                                                         |
| ----------------------- | ------------------------------------------------------------ |
| singleton(单例)         | 在Spring IoC容器中仅存在一个Bean实例 （默认的scope）         |
| prototype(多例)         | 每次从容器中调用Bean时，都返回一个新的实例，即每次调用getBean()时 ，相当于执行new XxxBean()：不会在容器启动时创建对象 |
| request(请求)           | 用于web开发，将Bean放入request范围 ，request.setAttribute("xxx") ， 在同一个request 获得同一个Bean |
| session(会话)           | 用于web开发，将Bean 放入Session范围，在同一个Session 获得同一个Bean |
| globalSession(全局会话) | 一般用于 Porlet 应用环境 , 分布式系统存在全局 session 概念（单点登录），如果不是 porlet 环境，globalSession 等同于 Session |

**指定scope属性，IOC容器就知道创建对象的时候是单例还是多例的了。**

MyScope.java

```java
public class MyScope {
    public void testScope(){
        System.out.println("hashcode:"+this.hashCode());
    }
}
```

TestScope.java

```java
//注解方式设置Scope为多例模式
@Scope(value = "prototype")
public class TestScope {
    
//    MyScope scope = new MyScope();
    //自动装配
    @Autowired
    private MyScope myScope;
    public void scopeSay(){
        myScope.testScope();
    }
}
```

spring-scope.xml

```xml
<bean id="myScope" class="com.huadi.scope.MyScope" scope="singleton" lazy-init="true" init-method="" destroy-method=""/>
<bean id="testScope" class="com.huadi.scope.TestScope"/>
<context:annotation-config/>
```

**1、singleton作用域** 

**当我们使用singleton【单例】的时候，从IOC容器获取的对象都是同一个。**

**2、prototype** 

**当我们使用prototype【多例】的时候，从IOC容器获取的对象都是不同的。**

 1) 对象创建： 单例/多例

* scope="singleton", 默认值， 即 默认是单例 【service/dao/工具类】
* scope="prototype", 多例；【Action对象】

2) 什么时候创建?

* scope="prototype"  在用到对象的时候，才创建对象。
* scope="singleton"  在启动(容器初始化之前)， 就已经创建了bean，且整个应用只有一个。

3)是否延迟创建

* lazy-init="false"  默认为false,  不延迟创建，即在启动时候就创建对象
* lazy-init="true"   延迟初始化， 在用到对象的时候才创建对象（只对单例有效）

4) 创建对象之后，初始化/销毁

* init-method="init_user"       【对应对象的init_user方法，在对象创建之后执行 】
* destroy-method="destroy_user"  【在调用容器对象的destroy方法时候执行，(容器用实现类)】

5)注解方式

* 设置Scope为单例模式
  @Scope(value = "singleton")
* 设置Scope为多例模式
  @Scope(value = "prototype")

ScopeText.java

```java
public class ScopeText extends UnitTestBase {
    public ScopeText(){
        super("spring-scope.xml");
    }
    
    @Test
    public void testScope(){
        MyScope myScope1 = super.getBean("myScope");
        MyScope myScope2 = super.getBean("myScope");
        myScope1.testScope();
        myScope2.testScope();
    }

    @Test
    public void testScope2(){
        TestScope testScope1 = super.getBean("testScope");
        TestScope testScope2 = super.getBean("testScope");
        testScope1.scopeSay();
        testScope2.scopeSay();
    }
}
```

## 4.注解

创建对象以及处理对象依赖关系，相关的注解：

- **@ComponentScan扫描器**

- **@Configuration表明该类是配置类**

  @ComponentScan会扫描对应路径下的@Respository,@Service,@Controller,@Component

  ```java
  @Configuration//配置文件注解
  @ComponentScan("com.huadi.aware2")
  public class AwareConfig {
  }
  //相当于xml中
  <beans>
     <context:component-scan base-package="com.huadi.aware2"/>
  <beans>
  ```

  ```java
  AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AwareConfig.class);
  AwareService service = context.getBean(AwareService.class);
  ```

- **@Repository   作用同@Component； 在持久层Dao使用**

- **@Service      作用同@Component； 在业务逻辑层使用**

- **@Controller    作用同@Component； 在控制层使用**

- **@Component   指定把一个对象加入IOC容器，泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注**

  ```java
  @Respository("userDao")
  @Service("userService")
  @Controller("userController")
  @Component("showUser")
  public class ShowUser(){}
  //等同于xml中
  <bean id = "userDao" class="com.huadi.injection.UserDao"/>
  <bean id = "userService" class="com.huadi.injection.UserService"/>
  <bean id = "userController" class="com.huadi.injection.UserController"/>
  <bean id = "showUser" class="com.huadi.injection.ShowUser"/>
  ```

- **@Autowried 自动注入**

  可以加注在成员变量、set方法、构造器上，完成自动装配

  ```java
  @Autowired
  private static List<String> list;
  ```

- **@Qualifier限定哪个bean应该被自动注入**

  ```java
<bean id="user1" class="com.test.User">
  <property name="name" value="zhangsan"/></bean>
  
  <bean id="user2" class="com.test.User">
  <property name="name" value="lisi"/></bean>
  ```
  
  ```java
  @Autowired
  @Qualifier("user1")
  private User user;
  ```

- **@Resource  依赖关系**

  可以加注在成员变量、set方法，但是不能加注在构造器上

  - **如果@Resource不指定值，那么就根据类型来找，相同的类型在IOC容器中不能有两个**
  - **如果@Resource指定了值，那么就根据名字来找**
  
  ```java
  @Autowried 
  private UserDao userDao;
  //等同于
  @Resource
  private UserDao userDao;
  ```
  
- **@Scope 作用域**

  默认为singleton单例模式

  ```java
  @scope("singleton")//单例模式
  public class MyScope(){}
  @scope("prototype")//多例模式
  public class MyScope(){}
  等同于
  <bean id="myScope" class="com.huadi.scope.MyScope" scope="singleton"/>
  <bean id="myScope" class="com.huadi.scope.MyScope" scope="prototype"/>
  ```

## 5.依赖注入DI

**Spring中完成注入的几种方式：**

UnitTestBase.java

```java
public class UnitTestBase {

    private ClassPathXmlApplicationContext context =null;
    private String springXmlPath;

    public UnitTestBase(String springXmlPath){
        this.springXmlPath = springXmlPath;
    }

    @Before  //运行前加载
    public void before(){
        if(StringUtils.isEmpty(springXmlPath)){
            springXmlPath ="classpath*:spring-*.xml";
        }
        String[] name = springXmlPath.split("[,\\s]+");
        context = new ClassPathXmlApplicationContext(name);
        System.out.println("启动");
        context.start();  //启动Spring容器
    }
    @After //运行后加载
    public void after(){
        context.destroy();  //销毁容器
        System.out.println("销毁");
    }

    public <T extends  Object> T getBean(String beanId){
           return (T)context.getBean(beanId);
    }
}
```

* **set注入(属性注入)**

  property：通过setter对应的方法注入。

  要有无参的构造方法

  > 代码参考：SpringTest->injection

  UserServiceImpl.java

  ```java
  public class UserServiceImpl implements UserService {
  
      private UserDao userDao;
  
      //采用set方式注入到本类
      public void setUserDao(UserDao userDao) {
          this.userDao = userDao;
      }
  
      @Override
      public void addUser(String user) {
          userDao.saveUser(user);
      }
  }
  ```

  spring-injection.xml

  ```xml
  <!--创建UserDao的bean-->
      <bean id="userDao" class="com.huadi.injection.dao.UserDaoImpl"/>
      <!--创建UserService的bean-->
      <bean id="userService" class="com.huadi.injection.service.UserServiceImpl">
          <!--property：通过setter对应的方法注入。-->
          <property name="userDao" ref="userDao"/>
      </bean>
  ```

  ```java
  public class InjectionTest extends UnitTestBase {
      public InjectionTest() {
          super("spring-injection.xml");
      }
  
      @Test
      public void testInjection(){
          UserService userService = super.getBean("userService");
          userService.addUser("bobo");
      }
  }
  ```

* **构造器注入**

  ShowUser2.java

  ```java
  public class ShowUser2 {
      private  User2 user2;
      public  ShowUser2(User2 user2){
          this.user2 = user2;
      }
      
      public void testUser(){
          System.out.println(user2.toString());
      }
  }
  ```

  constructor-arg：通过构造函数注入。 

  ```xml
  <bean id="showUser2" class="com.huadi.injection.execise.ShowUser2">
          <constructor-arg name="user2" ref="user2"/>
      </bean>
  
      <bean id="user2" class="com.huadi.injection.execise.User2">
          <constructor-arg name="username" value="liai"/>
          <constructor-arg name="age" value="22"/>
          <constructor-arg name="address" value="太原"/>
          <constructor-arg name="score" value="80"/>
      </bean>
  ```

  ```java
  public class UserTest extends UnitTestBase {
      public UserTest( ) {
          super("spring-user.xml");
      }
  
      //构造器注入
      @Test
      public void testUser2(){
          ShowUser2 showUser2 = super.getBean("showUser2");
          showUser2.testUser();
      }
  }
  ```

* **注解注入(Autowried,Resource)**

  可以实现将Spring中的bean对象取出来并装配到指定的对象中

  在xml文件中：

  ```xml
  <!--开启注解-->
  <context:annotation-config/>
  ```

  UserServiceImpl.java

  ```java
  public class UserServiceImpl implements UserService {
      @Autowired
      private UserDao userDao;
  
      @Override
      public void addUser(String user) {
          userDao.saveUser(user);
      }
  }
  ```

  spring-injection.xml

  ```xml
  <!--创建UserService的bean-->
  <bean id="userService" class="com.huadi.injection.service.UserServiceImpl">
  </bean>
  <!--开启注解-->
  <context:annotation-config/>
  ```

  InjectionTest.java

  ```java
  public InjectionTest() {
       super("spring-injection.xml");
  }
  @Test
  public void testAnnotation(){
      UserService userService = super.getBean("userService");
      userService.addUser("张三");
  }
  ```

## 6.Aware接口

通常使用 Spring Aware 的目的是为了让 Bean 获得 Spring 容器的服务。实现这些接口会将代码耦合到Spring框架，应该避免使用任何 Aware 接口。

|          Aware子接口           |                      描述                      |
| :----------------------------: | :--------------------------------------------: |
|         BeanNameAware          |             获取容器中 Bean 的名称             |
|        BeanFactoryAware        | 获取容器的BeanFactory ，这样可以调用容器的服务 |
|    ApplicationContextAware     |           通过此接口获取容器的上下文           |
|       MessageSourceAware       |        获取 Message Source 相关文本信息        |
| ApplicationEventPublisherAware |                    发布事件                    |
|      ResourceLoaderAware       |      获取资源加载器，这样获取外部资源文件      |

beanName.java

```java
public class BeanName implements BeanNameAware {
    public String beanName;
    @Override
    public void setBeanName(String s) {
        this.beanName = s;
        System.out.println("-----beanName被设置-----");
        System.out.println(this.hashCode());
    }
}
```

BeanFactory.java

```java
public class BeanFactory implements BeanFactoryAware {
    
    private ConfigurableListableBeanFactory factory;
    @Override
    public void setBeanFactory(org.springframework.beans.factory.BeanFactory beanFactory) throws BeansException {
        this.factory = (ConfigurableListableBeanFactory) beanFactory;
        System.out.println("-----beanFactory被创建------");
    }
}
```

ApplicationContext.java

```java
public class ApplicationContext implements ApplicationContextAware {

    private org.springframework.context.ApplicationContext applicationContext;
    @Override
    public void setApplicationContext(org.springframework.context.ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
        System.out.println("-------Spring容器被创建-----");
        System.out.println("application:"+this.hashCode());
    }
}
```

spring-aware.xml

```java
<!--创建beanName对象-->
<bean id="beanName" class="com.huadi.aware.BeanName"/>
<!--创建beanFactory-->
<bean id="beanFactory" class="com.huadi.aware.BeanFactory"/>
<!--创建applicationContext-->
<bean id="applicationContext" class="com.huadi.aware.ApplicationContext"/>
```

TestAware.java

```java
public class TestAware extends UnitTestBase {
    public TestAware() {
        super("spring-aware.xml");
    }

    @Test
    public void testBeanname(){
        BeanName beanName = super.getBean("beanName");
        System.out.println("beanName:"+beanName.hashCode());
    }
    
    @Test
    public void testBeanFactory(){
        BeanFactory beanFactory = super.getBean("beanFactory");
        System.out.println("beanFactory:"+beanFactory.hashCode());
    }
    
     @Test
    public void testApplication(){
        ApplicationContext context = super.getBean("applicationContext");
        System.out.println("applicationContext:"+context.hashCode());
    }
}
```

**资源加载器的使用**

1.创建一个配置文件类AwareConfig.java

```java
//Spring配置文件类
@Configuration//配置文件注解
@ComponentScan("com.huadi.aware2")
public class AwareConfig {
}
```

2.创建一个测试类AwareService类，实现资源加载器接口

```java
@Component
public class AwareService implements BeanNameAware, ResourceLoaderAware {

    private ResourceLoader resourceLoader;
    private String name;
    @Override
    public void setBeanName(String beanName) {
        this.name = beanName;
    }

    @Override
    public void setResourceLoader(ResourceLoader resourceLoader) {
        this.resourceLoader = resourceLoader;
    }

    public void readFile() throws IOException {
        String line = "";
        Resource resource = resourceLoader.getResource("classpath:com/huadi/aware2/text.txt");
        InputStream in;
        InputStreamReader inputStreamReader = new InputStreamReader(resource.getInputStream());
        //将文件输入流包装成缓冲流
        BufferedReader reader = new BufferedReader(inputStreamReader);
        while ((line = reader.readLine())!=null){
            System.out.println(line);
        }
        reader.close();
    }
}
```

3.创建测试类，创建Spring容器，获取AwareService类

```java
public class TestAware {
    @Test
    public void test() throws IOException {
        //创建spring容器
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AwareConfig.class);
        AwareService service = context.getBean(AwareService.class);
        service.readFile();
        context.close();
    }
}
```

4.将资源文件text.txt放置在类路径target/class下

## 7.Dao、Service

UserDao.java

```java
public interface UserDao {
    void saveUser(String user);
}
```

UsreDaoImpl.java

```java
@Repository//声明此类为Dao层的bean
public class UserDaoImpl implements UserDao{
    @Override
    public void saveUser(String user) {
        System.out.println("保存了用户"+user);
    }
}
```

UserService.java

```java
public interface UserService {
    void addUser(String user);
}
```

UserServiceImpl.java

```java
@Service("userService")//声明Service层的bean
public class UserServiceImpl implements UserService{

    @Autowired
    private UserDao userDao;
    @Override
    public void addUser(String user) {
        userDao.saveUser(user);
    }
}
```

spring-annotation.xml

```xml
<!--扫描指定包下的注解-->
<context:component-scan base-package="com.huadi.annotation"/>
<!--开启注解-->
<context:annotation-config/>
```

TestAnnotation.java

```java
public class TestAnnotation extends UnitTestBase {
    public TestAnnotation() {
        super("spring-annotation.xml");
    }

    @Test
    public void testAnnotation(){
        UserService userService = super.getBean("userService");
        userService.addUser("张三");
    }
}
```

## 8.事务的特性

1.**原子性**：原子性是指事务包含的所有操作要么全部成功，要么全部失败回滚，因此事务的操作如果成功就必须要完全应用到数据库，如果操作失败则不能对数据库有任何影响。

2.**一致性**：一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态，也就是说一个事务执行之前和执行之后都必须处于一致性状态。

3.**隔离性**：隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。

4.**持久性**：持久性是指一个事务一旦被提交了，那么对数据库中的数据的改变就是永久性的，即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作。

**spring支持编程式事务管理和声明式事务管理两种方式。**

- **编程式事务**使用TransactionTemplate或者直接使用底层的PlatformTransactionManager。对于编程式事务管理，spring推荐使用TransactionTemplate。
- **声明式事务**是建立在AOP之上的。其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。声明式事务最大的优点就是不需要通过编程的方式管理事务，这样就不需要在业务逻辑代码中掺杂事务管理的代码，只需在配置文件中做相关的事务规则声明(或通过基于@Transactional注解的方式)，便可以将事务规则应用到业务逻辑中。

#### 编程式事务管理

Spring提供有两种方式的编程式事务管理：

* 使用TransactionTemplate
* 直接使用一个PlatformTransactionManager实现

AccountDaoImpl.java

```java
public class AccountDaoImpl extends JdbcDaoSupport implements AccountDao{
    @Override
    public void inMoney(String inAccount, double money) {
        String sql = "update account set money = money + ? where name2 = ?";
        System.out.println(sql);
        this.getJdbcTemplate().update(sql,money,inAccount);
    }

    @Override
    public void outMoney(String outAccount, double money) {
        String sql = "update account set money = money - ? where name2 = ?";
        System.out.println(sql);
        this.getJdbcTemplate().update(sql,money,outAccount);
    }
}
```

applicationContext.xml

```xml
 <!--引入数据-->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="driverClass" value="${jdbc.driverClass}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    <!--Dao层-->
    <bean id="accountDao" class="com.huadi.dao.AccountDaoImpl">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--Service层-->
    <bean id="accountService" class="com.huadi.service.AccountServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="transactionTemplate" ref="transactionTemplate"/>
    </bean>
    <!--事务模板-->
    <bean id="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
        <property name="transactionManager" ref="transactionManager"/>
    </bean>
    <!--事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```

使用TransactionTemplate

```java
public class AccountServiceImpl implements AccountService{

    private AccountDao accountDao;

    private TransactionTemplate transactionTemplate;

    public void setTransactionTemplate(TransactionTemplate transactionTemplate) {
        this.transactionTemplate = transactionTemplate;
    }

    public void setAccountDao(AccountDao accountDao) {
        this.accountDao = accountDao;
    }

//    @Override
//    public void transfer(String outAccount, String inAccount, double money) {
//        //支出
//        accountDao.outMoney(outAccount, money);
//        int i = 100/0;//模拟一个异常产生
//        //收入
//        accountDao.inMoney(inAccount, money);
//    }

    //使用编程式事务管理TransactionTemplate后，出现异常，会回滚
    //转账执行，通过匿名内部类实现回调方法，实现对事务的管理
    @Override
    public void transfer(String inAccount, String outAccount, double money){
        transactionTemplate.execute(new TransactionCallbackWithoutResult() {
            @Override
            protected void doInTransactionWithoutResult(TransactionStatus transactionStatus){
                accountDao.outMoney(outAccount,money);
                int i =100/0;//模拟一个异常产生
                accountDao.inMoney(inAccount,money);
            }
        });
    }
}
```

#### 声明式事务管理

* 注解方式：  @Transactional
* xml文件：  aop配置事务增强

**注解方式：**

applicationContext2.xml

```xml
    <!--引入数据-->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <!--c3p0数据源-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="driverClass" value="${jdbc.driverClass}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    <!--Dao层-->
    <bean id="accountDao" class="com.huadi.dao.AccountDaoImpl">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--Service层-->
    <bean id="accountService" class="com.huadi.service.AccountServiceImpl2">
        <property name="accountDao" ref="accountDao"/>
    </bean>
    <!--事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--开启Spring的事务管理器-->
    <tx:annotation-driven transaction-manager="transactionManager"/>
</beans>
```

AccountServiceImpl2.java

```java
@Transactional(propagation = Propagation.REQUIRED,  //传播行为
isolation = Isolation.DEFAULT)  //隔离级别
public class AccountServiceImpl2 implements AccountService{
    private AccountDao accountDao;
    public void setAccountDao(AccountDao accountDao) {
        this.accountDao = accountDao;
    }

    @Override
    public void transfer(String outAccount, String inAccount, double money) {
        accountDao.outMoney(outAccount,money);
        int i = 100/0;
        accountDao.inMoney(inAccount,money);
    }
}
```

**采用aop的声明式事务管理**

pom.xml

```xml
<!--aop相关的jar包-->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.6.6</version>
            <scope>runtime</scope>
        </dependency>
```

applicationContext3.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/cache
       http://www.springframework.org/schema/cache/spring-cache.xsd
       http://www.springframework.org/schema/task
       http://www.springframework.org/schema/task/spring-task.xsd
       http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop-3.0.xsd">

    <!--引入数据-->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <!--c3p0数据源-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="driverClass" value="${jdbc.driverClass}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <!--转账Service-->
    <bean id="accountService" class="com.huadi.service.AccountServiceImpl3">
        <property name="accountDao" ref="accountDao"/>
    </bean>

    <!--转账Dao-->
    <bean id="accountDao" class="com.huadi.dao.AccountDaoImpl">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--事务的增强-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <!--拦截的方法-->
            <tx:method name="transfer" propagation="REQUIRED" isolation="DEFAULT"/>
            <!--指定回滚异常：rollback-for="java.lang.ArithmeticException"-->
            <!--指定不需要回滚的异常：no-rollback-for="java.lang.ArrayIndexOutOfBoundsException"-->
        </tx:attributes>
    </tx:advice>

    <!--配置aop的切面-->
    <aop:config>
        <!--配置连接点,选定AccountService下的所有方法-->
        <aop:pointcut id="pointcut" expression="execution(* com.huadi.service.AccountService+.*(..))"/>
        <!--配置切面-->
        <aop:advisor advice-ref="txAdvice" pointcut-ref="pointcut"/>
    </aop:config>
</beans>
```

AccountServiceImpl.java

```java
public class AccountServiceImpl3 implements AccountService{
    private AccountDao accountDao;

    public void setAccountDao(AccountDao accountDao) {
        this.accountDao = accountDao;
    }

    @Override
    public void transfer(String outAccount, String inAccount, double money) {
        accountDao.outMoney(outAccount,money);
        int i = 100/0;
        accountDao.inMoney(inAccount,money);
    }
}
```

## 9.AOP

Spring AOP主要做的事情就是：「把重复的代码抽取，在运行的时候往业务方法上**动态植入**“切面类代码”」

**这种在运行时，动态地将代码切入到类的指定方法、指定位置上的编程思想就是面向切面的编程。**

一般而言，我们管切入到指定类指定方法的代码片段称为切面，而切入到哪些类、哪些方法则叫切入点。有了AOP，我们就可以把几个类共有的代码，抽取到一个切片中，等到需要时再切入对象中去，从而改变其原有的行为。

**xml配置**

StudentDaoImpl.java

```java
public class StudentDaoImpl implements StudentDao{
    @Override
    public void insert(User user) {
        System.out.println("保存用户："+user.hashCode());
    }
}
```

StudentServiceImpl.java

```java
public class StudentServiceImpl implements StudentService{
    private StudentDao studentDao;
    public void setStudentDao(StudentDao studentDao) {
        this.studentDao = studentDao;
    }

    @Override
    public void addUser(User user) {
        studentDao.insert(user);
    }
}
```

LogImpl.java

```java
public class LogImpl {
    public void beforeInsert(){
        System.out.println("--------before insert------");
    }
    
    public void afterInsert(){
        System.out.println("---------after insert---------");
    }
    
    public void aroundInsert(){
        System.out.println("----------around insert----------");
    }
    
    public void returnInsert(){
        System.out.println("----------return insert----------");
    }
}
```

applicationContext4.xml

```xml
    <!--切面类-->
    <bean id="log" class="com.huadi.aop.LogImpl"/>
    <!--dao数据库类-->
    <bean id="studentDao" class="com.huadi.aop.StudentDaoImpl"/>
    <!--service业务类-->
    <bean id="studentService" class="com.huadi.aop.StudentServiceImpl">
        <property name="studentDao" ref="studentDao"/>
    </bean>
    <!---->
    <aop:config>
        <!--切入点-->
        <aop:pointcut id="studentPoint" expression="execution(* com.huadi.aop.StudentServiceImpl.*(..))"/>
        <!--配置切面-->
        <aop:aspect id="logAspect" ref="log">
            <!--前置通知-->
            <aop:before method="beforeInsert" pointcut-ref="studentPoint"/>
            <!--最终通知-->
            <aop:after method="afterInsert" pointcut-ref="studentPoint"/>
            <!--环绕通知-->
            <aop:around method="aroundInsert" pointcut-ref="studentPoint"/>
            <!--返回后通知-->
            <aop:after-returning method="returnInsert" pointcut-ref="studentPoint"/>
        </aop:aspect>
    </aop:config>
```

AopTest.java

```java
public class AopTest {
    @Test
    public void testAdvice(){
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext4.xml");
        StudentService studentService = (StudentService) context.getBean("studentService");
        User user = new User();
        studentService.addUser(user);
    }
}
```

**注解实现**

LogServiceImpl.java

```java
public class LogServiceImpl implements LogService{

    @Override
    public void addUser() {
        System.out.println("-------add User-----");
    }

    @Override
    public void deleteUser() {
        System.out.println("-------delete User-----");
    }

    @Override
    public void updateUser() {
        System.out.println("-------update User-----");
    }

    @Override
    public void selectUser() {
        System.out.println("-------select User-----");
    }
}
```

ServiceAspect.java

```java
@Component
@Aspect
public class ServiceAspect {
    private final Logger logger = Logger.getLogger(String.valueOf(this.getClass()));

    /**
     *  切入点
     */
    private final String Point_cut = "execution(* com.huadi.aop.aspect.LoginServiceImpl.*(..))";

    //建立一个空方法作为切入点
    @Pointcut(Point_cut)
    private void pointcut(){

    }
    
    //前置通知
    @Before(value = Point_cut)
    //通过连接点参数可以捕获到目标对象的名称和方法名
    public void before(JoinPoint joinPoint){
        String Classname = joinPoint.getTarget().getClass().getName();
        String methodName = joinPoint.getSignature().getName();
        StringBuilder sb = new StringBuilder();
        System.out.println("------前置通知------");
        System.out.println("className:"+Classname);
        System.out.println("methodName:"+methodName);
        //将方法中的参数拼接到StringBuider中
        Object[] objects = joinPoint.getArgs();
        for(Object object : objects){
            sb.append(JSON.toJSONString(object));
            logger.info(sb.toString());
        }
    }

    //最终通知
    @After(value = Point_cut)
    public void After(JoinPoint joinPoint){
        String Classname = joinPoint.getTarget().getClass().getName();
        String methodName = joinPoint.getSignature().getName();
        StringBuilder sb = new StringBuilder();
        System.out.println("------最终通知------");
        System.out.println("className:"+Classname);
        System.out.println("methodName:"+methodName);
        Object[] objects = joinPoint.getArgs();
        for(Object object : objects){
            sb.append(JSON.toJSONString(object));
            logger.info(sb.toString());
        }
    }

    //环绕通知
    @Around(value = Point_cut)
    public Object arround(ProceedingJoinPoint proceedingJoinPoint) {
        System.out.println("---------环绕通知---------");
        Long begin = System.currentTimeMillis();
        StringBuilder log = new StringBuilder();
        Object result = null;
        try {
            result = proceedingJoinPoint.proceed();
        } catch (Throwable throwable) {
            System.out.println(log+throwable.getMessage());
        }
        Long end = System.currentTimeMillis();
        System.out.println("开始时间"+new Date(begin));
        System.out.println("结束时间"+new Date(end));
        return result;
    }
    
    //后置通知
    @AfterReturning(value = Point_cut,returning = "returnObj")
    public void afterReturning(Object returnObj){
        String result = JSON.toJSONString(returnObj);
        System.out.println("--------返回通知--------"+result);
    }

    //异常通知
    @AfterThrowing(value = Point_cut,throwing = "e")
    public void afterThrowing(Throwable e){
        System.out.println("异常信息："+e);
    }
}
```

aspect.xml

```xml
    <bean id="logService" class="com.huadi.aop.aspect.LogServiceImpl"/>
    <!--扫描器-->
    <context:component-scan base-package="com.huadi.aop.aspect"/>
    <!--开启aop代理-->
    <aop:aspectj-autoproxy/>
    <!--切面类-->
    <bean class="com.huadi.aop.aspect.ServiceAspect"/>
```

AspectTest.java

```java
public class AspectTest {
    @Test
        public void testAspect(){
            ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("aspect.xml");
            LogService logService = (LogService) context.getBean("logService");
            logService.addUser();
        }
    }
```

## 10.Bean

> https://github.com/wmyskxz/MoreThanJava/blob/master/java-web/spring/Spring%E5%AD%A6%E4%B9%A0-3-%E2%80%94%E2%80%94%E8%A3%85%E9%85%8DSpring-Bean%E8%AF%A6%E8%A7%A3.md

 Spring 中提供了 3 种方法进行配置：

- 在 XML 文件中显式配置
- 在 Java 的接口和类中实现配置
- 隐式 Bean 的发现机制和自动装配原则

**方式选择的原则**

在现实的工作中，这 3 种方式都会被用到，并且在学习和工作之中常常混合使用，所以这里给出一些关于这 3 种优先级的建议：

1.**最优先：通过隐式 Bean 的发现机制和自动装配的原则。** 基于约定优于配置的原则，这种方式应该是最优先的

- **好处：** 减少程序开发者的决定权，简单又不失灵活。

2.**其次：Java 接口和类中配置实现配置** 在没有办法使用自动装配原则的情况下应该优先考虑此类方法

- **好处：** 避免 XML 配置的泛滥，也更为容易。
- **典型场景：** 一个父类有多个子类，比如学生类有两个子类，一个男学生类和女学生类，通过 IoC 容器初始化一个学生类，容器将无法知道使用哪个子类去初始化，这个时候可以使用 Java 的注解配置去指定。

3.**最后：XML 方式配置** 在上述方法都无法使用的情况下，那么也只能选择 XML 配置的方式。

- **好处：** 简单易懂（当然，特别是对于初学者）
- **典型场景：** 当使用第三方类的时候，有些类并不是我们开发的，我们无法修改里面的代码，这个时候就通过 XML 的方式配置使用了。

> 引用：我没有三颗心脏
>
> 代码参见IDEA中Spring01项目

#### XML装配 Bean

**控制反转是一种通过描述（在 Java 中可以是 XML 或者注解）并通过第三方（Spring）去产生或获取特定对象的方式。**

1.<font color="lighblue">id属性</font>是 Spring 能找到当前 Bean 的一个依赖的编号，**不过 id 属性不是一个必需的属性**，<font color="lighblue">name 属性</font>也可以定义 bean 元素的名称。

2.<font color="lighblue">class 属性</font>显然就是一个类的全限定名。

3.<font color="lighblue">property 元素</font>是定义类的属性，其中的 name 属性定义的是属性的名称，而 value是它的值。

4.注入对象，使用 <font color="lighblue">ref 属性</font>,通过 ref 属性去引用对应的 Bean。

```xml
<!-- 配置 srouce 原料 -->
<bean name="source" class="pojo.Source">
    <property name="fruit" value="橙子"/>
    <property name="sugar" value="多糖"/>
    <property name="size" value="超大杯"/>
</bean>

<bean name="juickMaker" class="pojo.JuiceMaker">
    <!-- 注入上面配置的id为srouce的Srouce对象 -->
    <property name="source" ref="source"/>
</bean>
```

5.("productService",ProductService.class) 按照名字和类型拿bean，最安全和方便

```java
//创建Spring上下文（加载bean.xml）
//读取classpath中的资源
ApplicationContext context= new ClassPathXmlApplicationContext("bean.xml");
ProductService productService = (ProductService)context.getBean("productService",ProductService.class);
productService.doSomeService();
```

6.**BeanFactory 和 ApplicationContext 的区别**：

**BeanFactory**：是Spring中最底层的接口，只提供了最简单的IoC功能,负责配置，创建和管理bean。

在应用中，一般不使用 BeanFactory，而推荐使用ApplicationContext（应用上下文），原因如下。

**ApplicationContext**：

1.继承了 BeanFactory，拥有了基本的 IoC 功能；

2.除此之外，ApplicationContext 还提供了以下功能：

① 支持国际化；

② 支持消息机制；

③ 支持统一的资源加载；

④ 支持AOP功能；

7.**注入对象：**使用 ref 属性

```xml
<!-- 配置 srouce 原料 -->
<bean name="source" class="pojo.Source">
    <property name="fruit" value="橙子"/>
    <property name="sugar" value="多糖"/>
    <property name="size" value="超大杯"/>
</bean>

<bean name="juickMaker" class="pojo.JuiceMaker">
    <!-- 注入上面配置的id为srouce的Srouce对象 -->
    <property name="source" ref="source"/>
</bean>
```

8.装配集合

- <font color="lighblue">List 属性</font>为对应的 <list> 元素进行装配，然后通过多个 <value> 元素设值
- <font color="lighblue">Map 属性</font>为对应的 <map> 元素进行装配，然后通过多个 <entry> 元素设值，只是entry 包含一个键值对(key-value)的设置
- <font color="lighblue">Properties 属性</font>为对应的 <properties> 元素进行装配，通过多个 <property> 元素设值，只是 properties 元素有一个必填属性 key ，然后可以设置值
- <font color="lighblue">Set 属性</font>为对应的 <set> 元素进行装配，然后通过多个 <value> 元素设值
- 对于<font color="lighblue">数组</font>而言，可以使用 <array> 设置值，然后通过多个 <value> 元素设值。

```xml
<bean id="complexAssembly" class="pojo.ComplexAssembly">
    <!-- 装配Long类型的id -->
    <property name="id" value="1"/>
    
    <!-- 装配List类型的list -->
    <property name="list">
        <list>
            <value>value-list-1</value>
            <value>value-list-2</value>
            <value>value-list-3</value>
        </list>
    </property>
    
    <!-- 装配Map类型的map -->
    <property name="map">
        <map>
            <entry key="key1" value="value-key-1"/>
            <entry key="key2" value="value-key-2"/>
            <entry key="key3" value="value-key-2"/>
        </map>
    </property>
    
    <!-- 装配Properties类型的properties -->
    <property name="properties">
        <props>
            <prop key="prop1">value-prop-1</prop>
            <prop key="prop2">value-prop-2</prop>
            <prop key="prop3">value-prop-3</prop>
        </props>
    </property>
    
    <!-- 装配Set类型的set -->
    <property name="set">
        <set>
            <value>value-set-1</value>
            <value>value-set-2</value>
            <value>value-set-3</value>
        </set>
    </property>
    
    <!-- 装配String[]类型的array -->
    <property name="array">
        <array>
            <value>value-array-1</value>
            <value>value-array-2</value>
            <value>value-array-3</value>
        </array>
    </property>
</bean>
```

上面看到了对简单 String 类型的各个集合的装载，但是有些时候可能需要更为复杂的装载，比如一个 List 可以是一个系列类的对象，为此需要定义注入的相关信息，其实跟上面的配置没什么两样，只不过加入了 `ref` 这一个属性而已。

**集合注入总结：**

- List 属性使用 `<list>` 元素定义注入，使用多个 `<ref>` 元素的 Bean 属性去引用之前定义好的 Bean

```xml
<property name="list">
    <list>
        <ref bean="bean1"/>
        <ref bean="bean2"/>
    </list>
</property>
```

- Map 属性使用 `<map>` 元素定义注入，使用多个 `<entry>` 元素的 `key-ref` 属性去引用之前定义好的 Bean 作为键，而用 `value-ref` 属性引用之前定义好的 Bean 作为值

```xml
<property name="map">
    <map>
        <entry key-ref="keyBean" value-ref="valueBean"/>
    </map>
</property>
```

- Set 属性使用 `<set>` 元素定义注入，使用多个 `<ref>` 元素的 `bean` 去引用之前定义好的 Bean

```xml
<property name="set">
    <set>
        <ref bean="bean"/>
    </set>
</property>
```

#### 注解装配 Bean

1.@Component表示 Spring IoC 会把这个类扫描成一个 bean 实例。

<bean id="student1" class="pojo.Student" />，也可以简写成 @Component("student1")。

@Value注解：表示值的注入，跟在 XML 中写 value 属性是一样的。

```java
@Component(value = "student1")
public class Student {
    @Value("1")
    int id;
    @Value("student_name_1")
    String name;
    // getter and setter
}
```

2.自动装配——@Autowired

所谓自动装配技术是一种由 Spring 自己发现对应的 Bean，自动完成装配工作的方式，它会应用到一个十分常用的注解 @Autowired 上，这个时候 Spring 会根据类型去寻找定义的 Bean 然后将其注入。

```java
 @Autowired
 private Student student = null;
```

3.@Qualifier 注解

为了消除歧义性，按照名字来查找和注入消除歧义性：

```java
public class JuiceMaker {
    ......
    @Autowired
    @Qualifier("source1")
    public void setSource(Source source) {
        this.source = source;
    }
}
```

4.@Bean 注解

使用 @Bean 注解，注解到方法之上，使其成为 Spring 中返回对象为 Spring 的 Bean 资源

```java
@Configuration
public class BeanTester {

    @Bean(name = "testBean")
    public String test() {
        String str = "测试@Bean注解";
        return str;
    }
}
```

@Configuration 注解相当于 XML 文件的根元素，必须有，有了才能解析其中的 @Bean 注解

5.在 applicationContext.xml 中配置自动注入，并告诉 Spring IoC 容器去哪里扫描这两个 Bean：

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <context:component-scan base-package="aspect" />
    <context:component-scan base-package="pojo" />
    <aop:aspectj-autoproxy/>
</beans>
```

注解方式扫描：

（1）basePackages可读性会更好一些，所以在项目中会优先选择使用它。

```java
@ComponentScan(basePackages = {"pojo","aspect"})
```

（2）basePackageClasses会有错误提示,很多时候重构修改包名需要反复地配置,所有用它。

```java
@ComponentScan(basePackageClasses = pojo.Student.class)
```

#### 注解配置AOP

1.用 @Aspect 注解一个类，那么 Spring IoC 容器就会认为这是一个切面。

2.被定义为切面的类仍然是一个 Bean ，需要 @Component 注解标注。

```java
@Component
@Aspect
public class Broker {

    @Before("execution(* aop.pojo.Landlord.service())")
    public void before(){
        System.out.println("带租客看房");
        System.out.println("谈价格");
    }
    @After("execution(* aop.pojo.Landlord.service())")
    public void after(){
        System.out.println("交钥匙");
    } 
}
```

3.Spring 中的 AspectJ 注解：

| **注解**        | **说明**                                                     |
| --------------- | ------------------------------------------------------------ |
| @Before         | 前置通知，在连接点方法前调用                                 |
| @Around         | 环绕通知，它将覆盖原有方法，但是允许你通过反射调用原有方法，后面会讲 |
| @After          | 后置通知，在连接点方法后调用                                 |
| @AfterReturning | 返回通知，在连接点方法执行并正常返回后调用，要求连接点方法在执行过程中没有发生异常 |
| @AfterThrowing  | 异常通知，当连接点方法异常时调用                             |

```java
@Component
@Aspect
public class Broker {
/**环绕通知*/
    @Around("execution(* aop.pojo.Landlord.service())")
    public void around(ProceedingJoinPoint joinPoint) {
        System.out.println("带租客看房");
        System.out.println("谈价格");
        try {
            joinPoint.proceed();
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
        System.out.println("交钥匙");
     }
    }
```

4.execution 的正则表达式:

```java
execution(* pojo.Landlord.service())
```

- execution：代表执行方法的时候会触发
- \* ：代表任意返回类型的方法
- pojo.Landlord：代表类的全限定名
- service()：被拦截的方法名称

5.通知

- 环绕通知

  ```java
  @Around("execution(* pojo.Landlord.service())") 
  ```

- 前置通知

  ```java
  @Before("execution(* pojo.Landlord.service())") 
  ```

- 后置通知

  ```java
  @After("execution(* pojo.Landlord.service())")
  ```

| AOP 配置元素        | 用途                             | 备注                       |
| ------------------- | -------------------------------- | -------------------------- |
| aop:advisor         | 定义 AOP 的通知                  | 一种很古老的方式，很少使用 |
| aop:aspect          | 定义一个切面                     | ——                         |
| aop:before          | 定义前置通知                     | ——                         |
| aop:after           | 定义后置通知                     | ——                         |
| aop:around          | 定义环绕通知                     | ——                         |
| aop:after-returning | 定义返回通知                     | ——                         |
| aop:after-throwing  | 定义异常通知                     | ——                         |
| aop:config          | 顶层的 AOP 配置元素              | AOP 的配置是以它为开始的   |
| aop:declare-parents | 给通知引入新的额外接口，增强功能 | ——                         |
| aop:pointcut        | 定义切点                         | ——                         |

6.在 applicationContext.xml 中配置自动注入，并告诉 Spring IoC 容器去哪里扫描这两个 Bean：

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <context:component-scan base-package="aspect" />
    <context:component-scan base-package="pojo" />
    <aop:aspectj-autoproxy/>
</beans>
```

#### XML配置AOP

```java
<!-- 装配 Bean-->
<bean name="landlord" class="pojo.Landlord"/>
<bean id="broker" class="aspect.Broker"/>
<!-- 配置AOP -->
<aop:config>
    <!-- where：在哪些地方（包.类.方法）做增加 -->
    <aop:pointcut id="landlordPoint"
                  expression="execution(* pojo.Landlord.service())"/>
    <!-- what:做什么增强 -->
    <aop:aspect id="logAspect" ref="broker">
        <!-- when:在什么时机（方法前/后/前后） -->
        <aop:around pointcut-ref="landlordPoint" method="around"/>
    </aop:aspect>
</aop:config>
```

## 11.Lombok标签

```xml
<!--lombok-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```

在代码中我们可以只加上标签<font color="lighblue">@Data</font> 而不用get,set方法；@Data : 注解在类上, 为类提供读写属性, 此外还提供了 equals()、hashCode()、toString() 方法

<font color="lighblue">@NonNull</font> : 注解在参数上, 如果该类参数为 null , 就会报出异常,  throw new NullPointException(参数名)

<font color="lighblue">@Cleanup</font> : 注释在引用变量前, 自动回收资源 默认调用 close() 方法

<font color="lighblue">@Getter/@Setter</font> : 注解在类上, 为类提供读写属性

<font color="lighblue">@ToString</font> : 注解在类上, 为类提供 toString() 方法

<font color="lighblue">@EqualsAndHashCode</font> : 注解在类上, 为类提供 equals() 和 hashCode() 方法

@NoArgsConstructor, @RequiredArgsConstructor, @AllArgsConstructor : 注解在类上, 为类提供无参,有指定必须参数, 全参构造函数

#### 构造函数

<font color="lighblue">@AllArgsConstructor</font>：生成全参数构造函数，同时如果变量使用了NotNull annotation ， 会进行是否为空的校验， 
全部参数的构造函数的自动生成，该注解的作用域也是只有在实体类上，参数的顺序与属性定义的顺序一致。

<font color="lighblue">@NoArgsConstructor</font>：生成无参构造函数

<font color="lighblue">@RequiredArgsConstructor</font>：会生成一个包含常量（final），和标识了@NotNull的变量 的构造方法。

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

## 12.循环依赖

**Spring 是如何解决循环依赖问题的？**

如果在代码中，将两个或多个 Bean 互相之间持有对方的引用就会发生循环依赖。循环的依赖将会导致陷入死循环。这是 Spring 发生循环依赖的原因。

**循环依赖有三种形态：** 

第一种互相依赖：A 依赖 B，B 又依赖 A，它们之间形成了循环依赖。

第二种三者间依赖：A 依赖 B，B 依赖 C，C 又依赖 A，形成了循环依赖。

第三种是自我依赖：A 依赖 A 形成了循环依赖。

Spring 中设计了**三级缓存**来解决循环依赖问题，当我们去调用 getBean(）方法的时候，Spring 会先从一级缓存中去找到目标 Bean，如果发现一级缓存中没有便会去二级缓存中去找，而如果一、二级缓存中都没有找到，意味着该目标 Bean 还没有实例化。于是，Spring 容器会实例化目标 Bean（PS：刚初始化的 Bean 称为早期 Bean） 。然后，将目标 Bean 放入二级缓存中，同时，加上标记是否存在循环依赖。如果不存在循环依赖便会将目标 Bean 存入到二级缓存，否则，便会标记该 Bean 存在循环依赖，然后将等待下一次轮询赋值，也就是解析@Autowired 注解。等@Autowired 注解赋值完成后（PS：完成赋值的 Bean 称为成熟 Bean） ，会将目标 Bean 存入到一级缓存。

**如何理解三级缓存？**

* 一级缓存：放 “成品对象”（完全初始化好的、可以直接用的对象）。
* 二级缓存：放 “半成品对象”（已经 new 出来了，但还没完成属性注入的对象）。
* 三级缓存：放 “对象工厂”（用来生成半成品对象的工具，万一需要代理对象时用）。

**三级缓存怎么工作？**

假设 A 和 B 互相依赖（A 需要 B，B 需要 A），步骤如下：
1.创建 A：

* Spring 先 new 一个 A 的空对象（此时 A 是半成品，还没注入属性）。

* 把 A 的 “对象工厂” 放进三级缓存，再把 A 从工厂里取出来，放进二级缓存（此时二级缓存里是 A 的半成品）。

* 开始给 A 注入属性，发现需要 B，于是暂停 A 的创建，去创建 B。

2.创建 B：

* Spring 同样 new 一 个 B 的空对象（半成品）。

* 把 B 的 “对象工厂” 放进三级缓存，再取出 B 的半成品放进二级缓存。

* 开始给 B 注入属性，发现需要 A，于是去缓存里找 A。

* 此时二级缓存里已经有 A 的半成品了，直接把 A 的半成品拿过来注入给 B。

*  B 的属性注入完成，变成 “成品”，放进一级缓存，B 创建结束。

3.继续创建 A：

* 回到 A 的创建流程，现在 B 已经是成品了，直接把 B 的成品注入给 A。
* A 的属性注入完成，变成 “成品”，放进一级缓存，A 创建结束。

**总结**

三级缓存的核心逻辑是：**在对象还没完全创建好时，先把它的 “半成品” 或 “生成工具” 存起来，一旦循环依赖的另一方需要它，就先把半成品丢过去救急，避免死循环。**
最后，所有对象完成初始化后，都会放进一级缓存，供程序正常使用。

不过要注意：这种方式只对 “单例对象”+“setter 注入 / 字段注入” 有效，要是用构造器注入或者原型对象（每次创建都是新的），三级缓存也救不了，这时候就得从代码设计上避免循环依赖了。

* 构造器注入：因为 “必须先有依赖才能创建对象”，导致无法生成半成品，三级缓存没用。
* 原型对象(多例模式)：因为 “每次都是新对象，不缓存”，导致循环依赖时无限创建，三级缓存也没用。

**如何避免循环依赖？**

最佳实践是从设计上避免循环依赖，通过重构降低 Bean 之间的耦合；若无法避免，可结合@Lazy注解、Setter 注入等 Spring 特性规避问题。需注意：构造器注入和原型 Bean 的循环依赖是 Spring 无法解决的，必须通过设计调整规避。

