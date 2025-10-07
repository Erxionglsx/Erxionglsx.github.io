# Mybatis

[TOC]

## mybatis的优缺点

### 优点

1.**SQL 控制能力强**
MyBatis 允许开发者直接编写 SQL 语句，能够精确控制查询逻辑，适合复杂查询场景（如多表关联、子查询等），相比 Hibernate 等全自动 ORM 框架更灵活。

2.**学习成本较低**
核心概念简单，只需掌握 XML 配置或注解方式编写 SQL，以及基本的映射规则，容易上手，对 SQL 熟悉的开发者能快速掌握。

3.**性能较好**
避免了全自动 ORM 框架的额外开销（如 Hibernate 的级联查询、缓存机制等可能带来的性能问题），SQL 优化更直接，适合对性能要求高的场景。

4.**灵活的映射关系**
支持多种映射方式（一对一、一对多、多对多），可通过 ResultMap 自定义字段与实体类的映射关系，解决数据库字段与 Java 类属性名不一致的问题。

5.**易于集成**
能与 Spring、Spring Boot 等框架无缝集成，配置简单，且支持多种数据库（MySQL、Oracle、SQL Server 等）。

6.**动态 SQL 功能**
提供 `<if>`、`<choose>`、`<foreach>` 等标签，可根据条件动态生成 SQL，避免手动拼接 SQL 带来的冗余和错误。

### 缺点

1.**SQL 编写工作量大**
与全自动 ORM 框架相比，需要手动编写大量 SQL 语句，尤其在表结构复杂或频繁变更时，维护成本较高。

2.**数据库移植性差**
SQL 语句与具体数据库绑定（如 MySQL 的 LIMIT、Oracle 的 ROWNUM），更换数据库时可能需要修改大量 SQL。

3.**缺少级联查询自动支持**
虽然支持级联映射，但需要手动配置关联查询的 SQL 和 ResultMap，不像 Hibernate 那样自动处理对象间的关联关系。

4.**缓存机制相对简单**
一级缓存（SqlSession 级别）和二级缓存（Mapper 级别）功能有限，复杂场景下需结合 Redis 等第三方缓存框架。

5.**类型转换需手动处理**
对于一些特殊类型（如日期、枚举）的映射，可能需要自定义 TypeHandler，增加额外配置工作。

**总结**：MyBatis 适合需要灵活控制 SQL、重视性能、团队熟悉 SQL 的项目，尤其在互联网行业应用广泛；但对于追求快速开发、低 SQL 侵入性、强数据库移植性的场景，可能不如 JPA/Hibernate 更合适。

## Mybatis整合spring

![](https://note.youdao.com/yws/api/personal/file/EDD4FDBB1F6B414099645BDDF22384BB?method=download&shareKey=44ada6c70a41c656f5dd9bbbc7133be2)

UserMapper.java

```java
public interface UserMapper {
    public User getLoginUser(User user);
    void registerUser(User user);
    void updateUser(User user);
    void deleteUser(User user);
    public List<User> getUserList();
}
```

UserMapper.xml

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >

<mapper namespace="com.huadi.dao.mapper.UserMapper">

    <select id="getLoginUser"  parameterType="User"  resultType="User">
             select * from user2 where username = #{username} and password = #{password}
    </select>

    <insert id="registerUser" parameterType="User">
           insert into user2 (username,password) values (#{username},#{password})
    </insert>
    
    <update id="updateUser" parameterType="User">
        update user2 set username=#{username},password=#{password} where id=#{userId}
    </update>

    <delete id="deleteUser" parameterType="User">
        delete  from user2 where id = #{userId}
    </delete>

    <!--获取用户列表-->
    <select id="getUserList" resultType="User">
        select * from user2
    </select>
</mapper>
```

applicationContext.java

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:beans="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/tx
		http://www.springframework.org/schema/tx/spring-tx-4.3.xsd
        http://www.springframework.org/schema/beans">

    <!-- c3p0数据源 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.cj.jdbc.Driver" />
        <property name="jdbcUrl"
                  value="jdbc:mysql://localhost:3306/test?useUnicode=true
                        &amp;characterEncoding=utf-8&amp;serverTimezone=Asia/Shanghai
                        &amp;useSSL=false&amp;serverTimezone = GMT"/>
        <property name="user" value="erxiong" />
        <property name="password" value="123456" />
    </bean>

    <!-- mybatis的SqlSession的工厂 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="configLocation" value="classpath:mybatis-config.xml" />
    </bean>

    <!-- mapper扫描器 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.huadi.dao.mapper"/>
        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    </bean>

    <!-- 配置Service类 -->
    <bean id="userLoginService" class="com.huadi.service.impl.UserLoginServiceImpl"/>

    <!-- 事务管理 -->
    <bean id="transactionManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <tx:annotation-driven transaction-manager="transactionManager" />

</beans>
```

mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!--实体类，简称 -设置别名-->
    <typeAliases>
        <typeAlias alias="User" type="com.huadi.model.User"/>
    </typeAliases>
    <!--用户映射文件-->
    <mappers>
        <mapper resource="userMapper.xml"/>
    </mappers>
</configuration> 15203510085 70
```

UserTest.java

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(value = "classpath:applicationContext.xml")
public class UserTest {
    @Autowired
    private UserMapper userMapper;

    @Test
    public void testUserLogin(){
        User user = new User();
        user.setUsername("张三");
        user.setPassword("123");
        User user2 = userMapper.getLoginUser(user);
        System.out.println(user2);
    }
    
    @Test
    public void testRegister(){
        User user = new User();
        user.setUsername("bibi");
        user.setPassword("123");
        userMapper.registerUser(user);
    }

    @Test
    public void testUpdate(){
        User user = new User();
        user.setUserId(3);
        user.setUsername("wnagwnag");
        user.setPassword("321");
        userMapper.updateUser(user);
    }

    @Test
    public void testDelete(){
        User user = new User();
        user.setUserId(5);
        userMapper.deleteUser(user);
    }
    
    @Test
    public void testSelectAll(){
        List<User> list= userMapper.getUserList();
        for (User s :list){
            System.out.println(s);
        }
    }
}
```

#### 自己配置的sessionFactory

1.创建配置文件

configration.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!--构建连接池-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver" />
                <property name="url"
                          value="jdbc:mysql://localhost:3306/test?useUnicode=true
                        &amp;characterEncoding=utf-8&amp;serverTimezone=Asia/Shanghai
                        &amp;useSSL=false&amp;serverTimezone = GMT"/>
                <property name="username" value="erxiong" />
                <property name="password" value="123456" />
            </dataSource>
        </environment>
    </environments>

    <!--用户映射文件-->
    <mappers>
        <mapper resource="userMapper.xml"/>
    </mappers>
</configuration>
```

2.根据自己构建的sessionFactory获取session,访问数据库

GetSessionBySessionFactory.java

```java
public class GetSessionBySessionFactory {
    public static void main(String[] args) throws IOException {
        String resource = "configration.xml";
        Reader reader = Resources.getResourceAsReader(resource);
        //根据配置文件获取一个SqlSessionFactory
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader);
        //根据SessionFactory获取一个session
        SqlSession session = factory.openSession();

        User user = new User();
        user.setUsername("aaaa");
        user.setPassword("2222");

        session.insert("registerUser",user);
        session.commit();
        session.close();
    }
}
```

UserTest1.java

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(value = "classpath:applicationContext.xml")
public class UserTest111 {
    @Autowired
    private SqlSessionFactory sqlSessionFactory;

    @Test
    public void testUserLogin(){
        SqlSession session = sqlSessionFactory.openSession();
        //获取Spring中的User
        UserMapper userMapper = session.getMapper(UserMapper.class);
        User user = new User();
        user.setUsername("aaaa");
        user.setPassword("2222");
        User user2 = userMapper.getLoginUser(user);
        System.out.println(user2);
    }
}
```

3.userMapper.xml文件配置

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >

<mapper namespace="com.huadi.dao.mapper.UserMapper">

    <resultMap id="userMap" type="com.huadi.model.User">
        <result property="userId" column="id"/>
        <result property="username" column="username"/>
        <result property="password" column="password"/>
    </resultMap>

    <select id="getLoginUser"  parameterType="com.huadi.model.User"  resultMap="userMap">
             select * from user2 where username = #{username} and password = #{password}
       </select>

    <insert id="registerUser" parameterType="com.huadi.model.User">
           insert into user2 (username,password) values (#{username},#{password})
       </insert>

    <update id="updateUser" parameterType="com.huadi.model.User">
        update user2 set username=#{username},password=#{password} where id=#{userId}
    </update>

    <delete id="deleteUser" parameterType="com.huadi.model.User">
        delete  from user2 where id = #{userId}
    </delete>
    
    <!--获取用户列表-->
    <select id="getUserList" resultType="com.huadi.model.User">
        select * from user2
    </select>
</mapper>
```

**resultType**:表示sql语句返回的类型

**resultMap**:表示sql语句返回的对象，并且对象的属性和数据库的字段一一对应。

#### 动态SQL

就是我们在执行查询的时候根据条件来动态的组合SQL语句。

```xml
<select id="queryUserByUserName" resultMap="userMap"  parameterType="java.lang.String">
        select * from user2
        <if test="username!=null">
            where username = #{username}
        </if>
</select>

<!--逻辑与查询，根据名称和年龄查询-->
<select id="queryAllUserByNameAndAge" parameterType="com.huadi.model.User" resultMap="userMap">
    select * from user2 where 1 = 1
    <if test="username != null">
        and username = #{username}
    </if>
    <if test="age != null">
        and age = #{age}
    </if>
</select>

<!--逻辑或查询，只匹配第一个条件-->
<select id="queryAllUserByOr" parameterType="com.huadi.model.User" resultMap="userMap">
    select * from user2 where 1 = 1
    <choose>
        <when test="age != null">
            and age like #{username}
        </when>
        <when test="username != null">
            and username = #{username}
        </when>
    </choose>
</select>

<!--模糊查询-->
<select id="queryAllUserByUsername" parameterType="java.lang.String" resultMap="userMap">
    select * from user2 where username like '%${value}%' limit 0,3
</select>

<!--添加用户，返回结果
	order="after"表示执行后返回-->
<insert id="addUserBackById" parameterType="com.huadi.model.User">
    <selectKey keyProperty="userId" order="AFTER" resultType="int">
        SELECT LAST_INSERT_ID()
    </selectKey>
    insert into user2(id,username,password) values (#{userId},#{username},#{password})
</insert>

<!--order="before"表示执行前返回-->
<insert id="addUserBeforeById" parameterType="com.huadi.model.User">
    <selectKey keyProperty="userId" order="BEFORE" resultType="int">
        SELECT FLOOR(RAND()*1000)
    </selectKey>
    insert into user2(id,username,password) values (#{userId},#{username},#{password})
</insert>

<!--动态拼接，组合查询-->
    <!--prefixOverrides="AND | OR",如果用到and或者or会进行预加载，提高效率-->
<select id="getAllUserList2" parameterType="com.huadi.model.User" resultMap="userMapper">
    select * from user2
    <trim prefix="where" prefixOverrides="AND | OR">
        <if test="username != null">
            and username = #{username}
        </if>
        <if test="age != null">
            or age = #{age}
        </if>
    </trim>
</select>

<!--动态修改语句-->
<update id="updateUser2" parameterType="com.huadi.model.User">
    update user2
    <set>
        <if test="username != null">
            username = #{username},
        </if>
        <if test="age != null">
            age = #{age},
        </if>
    </set>
    where id = #{userId}
</update>
```

#### MyBatis分页

> 参考项目ERP

**过程：**

1、创建一个用于查询的pojo(QueryVo)，用于封装需要进行查询的参数；

2、**导入myTag.jar包**，在webapp/WEB-INF/lib下面；

3、在Controller(CustomerController)中构造Page对象填充下列属性:
	

* int total     		查询的总记录数
  
* int page   		当前页
  
* int size		每页显示记录条数
  
* List<T> rows		显示的数据集合

4、 customer.jsp页面引入下面的标签:

**<%@ taglib prefix="farsight" uri="http://farsight.cn/common/"%>**

5、 customer.jsp页面中使用分页标签:

**<farsight:page url="${pageContext.request.contextPath }/customer/list.action" />**

QueryVo.java

```java
public class QueryVo {

        private Integer page = 1;  //当前页
        private Integer start;   //开始条数
        private Integer size = 4;  //每页条数

        public Integer getStart() {
            return start;
        }
        public void setStart(Integer start) {
            this.start = start;
        }
        public Integer getPage() {
            return page;
        }
        public void setPage(Integer page) {
            this.page = page;
        }
        public Integer getSize() {
            return size;
        }
        public void setSize(Integer size) {
            this.size = size;
        }
}
```

OrderController.java

```java
@Controller
@RequestMapping(value = "order")
public class OrderController {

    @Autowired
    OrderService orderService;

    @RequestMapping("/list")
    public String list(QueryVo vo, Model model) throws Exception {
        System.out.println("---------CustomerController-------");

	    // 当前页
        if (vo.getPage() == null) {
            vo.setPage(1);
        }

        // 设置查询的起始记录条数
        vo.setStart((vo.getPage() - 1) * vo.getSize());

        // 查询数据列表和数据总数
        List<Order> resutList = orderService.findCustomerByVo(vo);
        int count = orderService.findCustomerByVoCount();

        Page<Order> page = new Page<Order>();
        page.setTotal(count); // 数据总数
        page.setSize(vo.getSize()); // 每页显示条数
        page.setPage(vo.getPage()); // 当前页数
        page.setRows(resutList); // 数据列表
        model.addAttribute("page", page);
        return "purchase";
    }
}
```

OrderService.java

```java
public List<Order> findCustomerByVo(QueryVo vo);
public Integer findCustomerByVoCount();
```

OrderServiceImpl.java

```java
	@Autowired
    OrderMapper orderMapper;

	@Override
    public List<Order> findCustomerByVo(QueryVo vo) {
        List<Order> list = orderMapper.findCustomerByVo(vo);
        return list;
    }

    @Override
    public Integer findCustomerByVoCount() {
        Integer count = orderMapper.findCustomerByVoCount();
        return count;
    }
```

OrderMapper.java

```java
List<Order> findCustomerByVo(QueryVo vo);  //查询订单列表
Integer findCustomerByVoCount();   //查询订单数量
```

orderMapper.xml

```xml
<!-- 条件加分页查询 -->
    <select id="findCustomerByVo" parameterType="com.zb.model.QueryVo" resultMap="orderMap">
        select * from orders limit #{start}, #{size};
    </select>
    <!-- 查询总条数 -->
    <select id="findCustomerByVoCount"  resultType="int">
        select count(*) from orders
    </select>
```

purchase.jsp

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ page isELIgnored="false" %>
<%@ taglib prefix="farsight" uri="http://farsight.cn/common/"%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<div class="right_tb">
        <table class="table table-bordered table-hover " >
            <tr align="center">
                <th ><b>编号</b></th>
                <th ><b>采购单编号</b></th>
                <th ><b>商品名称</b></th>
                <th ><b>采购数量</b></th>
                <th ><b>采购日期</b></th>
                <th ><b>状态</b></th>
                <th ><b></b></th>
            </tr>
            <c:forEach items="${page.rows }" var="pages" >
            <tr align="center" >
                <td><input type="checkbox" name="${pages.id}"/>${pages.id}</td>
                <td>${pages.orderId}</td>
                <td>${pages.productName}</td>
                <td>${pages.orderAmount}</td>
                <td>${pages.orderDate}</td>
                <td>${pages.orderStatus}</td>
                <td>结款</td>
            </tr>
            </c:forEach>
        </table>
        <div class="col-md-12 text-center">
            <farsight:page url="${pageContext.request.contextPath}/order/list.action"/>
        </div>
</div>
```













