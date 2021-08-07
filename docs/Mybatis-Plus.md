## Mybatis-Plus

> [B站狂神](https://www.bilibili.com/video/BV17E411N7KN?from=search&seid=1406035208605393646)

------

[TOC]

**MyBatis-Plus （简称 MP）是一个 MyBatis的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。**

### 特性

- **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响
- **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
- **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
- **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
- **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
- **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
- **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
- **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎(自动帮你生成代码)
- **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
- **分页插件支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
- **内置性能分析插件**：可输出 Sql 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作

### 配置

```xml
<!-- 数据库驱动 --> 
<dependency> 
    <groupId>mysql</groupId> 
    <artifactId>mysql-connector-java</artifactId> 
</dependency> 
<!-- lombok --> 
<dependency> 
    <groupId>org.projectlombok</groupId> 
    <artifactId>lombok</artifactId> 
</dependency> 
<!-- mybatis-plus --> 
<!-- mybatis-plus 是自己开发，并非官方的！ --> 
<dependency> 
    <groupId>com.baomidou</groupId> 
    <artifactId>mybatis-plus-boot-starter</artifactId> 
    <version>3.0.5</version> 
</dependency>
```

说明：我们使用 mybatis-plus 可以节省我们大量的代码，尽量不要同时导入 mybatis 和 mybatis-plus！版本的差异！可以只导入mybatis-plus。

### 区别

**传统方式pojo-dao（连接mybatis，配置mapper.xml文件）-service-controller**

使用了mybatis-plus 之后

pojo类 User.java

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

mapper接口 UserMapper.java

```java
//在对应mapper上面继承基础类BaseMapper
@Repository
public interface UserMapper extends BaseMapper<User> {
    //所有的CRUD操作都已经编写完成了
    //不需要像以前的配置一大堆文件了
}
```

#### 查询测试类

```java
@SpringBootTest
class MybatisPlusApplicationTests {

    //继承了BaseMapper，所有的方法都来自自己的父类
    //我们也可以编写自己的扩展方法
    @Autowired
    private UserMapper userMapper;
    
    @Test
    void contextLoads() {
        //参数是一个Wrapper,条件构造器,先不用 null
        //查询全部用户
        List<User> users = userMapper.selectList(null);
        users.forEach(System.out::println);
    }
}
```

结果：

![](https://note.youdao.com/yws/api/personal/file/C599DC706DB348DB9440248E5EF972BD?method=download&shareKey=5959c636d35c5325f88f4166aabe4b9d)

#### 配置日志

application.properties

```properties
#配置日志
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

![img](https://note.youdao.com/yws/api/personal/file/8D549B4FFA964759A3E45F9069D6DD87?method=download&shareKey=d37b2420b79c0c09b1de217d5890864c)

#### 插入测试

```java
//测试插入
    @Test
    public void testInsert(){
        User user = new User();
        user.setName("数据结构");
        user.setAge(3);
        user.setEmail("3445@qq.com");

        int result =  userMapper.insert(user);//自动生成id
        System.out.println(result);//受影响行数
        System.out.println(user);//id自动回填
    }
```

![img](https://note.youdao.com/yws/api/personal/file/1D2243C0D08041708C9B455FC2D9E622?method=download&shareKey=333bbbb2b89e1c4acfc184895c29aece)

数据库插入的id的默认值为：全局的唯一id

### 主键生成策略

默认 ID_WORKER 全局唯一id 

分布式系统唯一id生成：https://www.cnblogs.com/haoxinyue/p/5208136.html 

**雪花算法：** 

snowflflake是Twitter开源的分布式ID生成算法，结果是一个long型的ID。其核心思想是：使用41bit作为毫秒数，10bit作为机器的ID（5个bit是数据中心，5个bit的机器ID），12bit作为毫秒内的流水号（意味着每个节点在每毫秒可以产生 4096 个 ID），最后还有一个符号位，永远是0。可以保证几乎全球唯 一！

#### 配置主键自增

实体类字段上 @TableId(type = IdType.AUTO) 

```java
public class User {
	//对应数据库中的主键(uuid、自增id、雪花算法、redis、zookeeper)
    @TableId(type = IdType.INPUT)//一旦手动输入id之后，就需要自己配置id了
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

```java
public enum IdType { 
    AUTO(0), // 数据库id自增 
    NONE(1),// 未设置主键 
    INPUT(2),// 手动输入 
    ID_WORKER(3),// 默认的全局唯一id 
    UUID(4),// 全局唯一id 
    uuid ID_WORKER_STR(5); //ID_WORKER 字符串表示法 
}
```

#### 更新测试

```java
//测试更新
    @Test
    public void testUpdate(){
        User user = new User();
        user.setId(5L);
        user.setName("高等数学");
    //  user.setAge(21);
        // 注意：updateById 但是参数是一个 对象！
        int i = userMapper.updateById(user);
        System.out.println(i);
    }
```

![img](https://note.youdao.com/yws/api/personal/file/1A1C3D2D931749278DEBA82A19252F4C?method=download&shareKey=cd8afe00171fd2a5f6bc5a4d3f7fc1c3)

所有的sql都是自动帮你动态配置的！

### 自动填充(时间)

创建时间、修改时间！这些个操作一遍都是自动化完成的，我们不希望手动更新！ 

阿里巴巴开发手册：所有的数据库表：gmt_create、gmt_modifified几乎所有的表都要配置上！而且需 

要自动化

#### 方式一：数据库级别（工作中不允许你修改数据库）

1、在表中新增字段 create_time, update_time

![img](https://note.youdao.com/yws/api/personal/file/CA855E4A015E4ED7BC54267642C9CA9C?method=download&shareKey=a0d427847f4efe083fbda53fc1206883)

进行更新操作后

![img](https://note.youdao.com/yws/api/personal/file/1969D6F5C0694A76A9540FA918DE417F?method=download&shareKey=0c10ad27618fdc34e0d278f986b7be9c)

#### 方式二：代码级别

1.删除数据库的默认值、更新操作！

2.实体类字段属性上需要增加注解

```java
// 字段添加填充内容 
@TableField(fill = FieldFill.INSERT) 
private Date createTime; 

@TableField(fill = FieldFill.INSERT_UPDATE) 
private Date updateTime;
```

3.编写处理器来处理这个注解

```java
@Slf4j
@Component// 一定不要忘记把处理器加到IOC容器中！
public class MyMetaObjectHandler implements MetaObjectHandler {
    //插入时的填充策略
    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("start insert fill...");
        // setFieldValByName(String fieldName, Object fieldVal, MetaObject metaObject
        this.setFieldValByName("createTime",new Date(),metaObject);
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }

    //更新时的填充策略
    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("start update fill.....");
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }
}
```

4.测试插入、更新操作

![img](https://note.youdao.com/yws/api/personal/file/7BE47A109A284CABA069C0166B5BD36C?method=download&shareKey=961de0f8cec7276ede5ab3a4a82054c7)

### 乐观锁

乐观锁:故名思意十分乐观，它总是认为不会出现问题，无论干什么不去上锁！如果出现了问题，再次更新值测试

悲观锁:故名思意十分悲观，它总是认为总是出现问题，无论千什么都会上锁！再去操作！

乐观锁实现方式:

* 取出记录时，获取当前version
* 更新时，带上这个version
* 执行更新时，set version = newVersion where version = oldVersion
* 如果version不对，就更新失败

```sql
乐观锁：1、先查询，获得版本号 version = 1 
-- A 
update user set name = "kuangshen", version = version + 1 where id = 2 and version = 1 
-- B 
线程抢先完成，这个时候 version = 2，会导致 A 修改失败！ update user set name = "kuangshen", version = version + 1 where id = 2 and version = 1
```

**测试一下MP的乐观锁插件**

1、给数据库中增加version字段！

2、我们实体类加对应的字段

```java
@Version //乐观锁Version注解 
private Integer version;
```

3、注册组件

```java
// 扫描我们的 mapper 文件夹 
@MapperScan("com.kuang.mapper") 
@EnableTransactionManagement 
@Configuration 
// 配置类 
public class MyBatisPlusConfig { 
    // 注册乐观锁插件 
    @Bean 
    public OptimisticLockerInterceptor optimisticLockerInterceptor() { 
        return new OptimisticLockerInterceptor(); 
    } 
}
```

4、测试

```java
// 测试乐观锁成功！ 
@Test 
public void testOptimisticLocker(){ 
    // 1、查询用户信息 
    User user = userMapper.selectById(1L); 
    // 2、修改用户信息 
    user.setName("kuangshen"); 
    user.setEmail("24736743@qq.com"); 
    // 3、执行更新操作 
    userMapper.updateById(user); 
}

// 测试乐观锁失败！多线程下 
@Test 
public void testOptimisticLocker2(){ 
    // 线程 1 
    User user = userMapper.selectById(1L); 
    user.setName("kuangshen111"); 
    user.setEmail("24736743@qq.com"); 
    // 模拟另外一个线程执行了插队操作 
    User user2 = userMapper.selectById(1L); 
    user2.setName("kuangshen222"); user2.setEmail("24736743@qq.com"); 
    userMapper.updateById(user2); 
    // 自旋锁来多次尝试提交！ 
    userMapper.updateById(user); 
    // 如果没有乐观锁就会覆盖插队线程的值！ 
}
```

### 查询操作 

```java
	//测试根据id查询
    @Test
    public void testSelectById(){
        User user = userMapper.selectById(1L);
        System.out.println(user);
    }

    //测试根据id批量查询
    @Test
    public void testSelectByBatchId(){
        List<User> users = userMapper.selectBatchIds(Arrays.asList(1,2,3));
        users.forEach(System.out::println);
    }

    //条件查询 map
    @Test
    public void testSelectByBatchIds(){
        HashMap<String,Object> map = new HashMap<>();
        //自定义要查询
        map.put("name","数据结构");
        map.put("age",3);

        List<User> users = userMapper.selectByMap(map);
        users.forEach(System.out::println);
    }
```

### 分页查询

1. 原始的 limit 进行分页 
2. pageHelper 第三方插件 
3. MP 其实也内置了分页插件！

1、配置拦截器组件即可

```java
//分页插件
@Bean
public PaginationInterceptor paginationInterceptor(){
    return new PaginationInterceptor();
}
```

2、直接使用Page对象

```java
// 测试分页查询 
@Test 
public void testPage(){ 
    // 参数一：当前页 
    // 参数二：页面大小 
    Page<User> page = new Page<>(2,5); 
    userMapper.selectPage(page,null); 
    page.getRecords().forEach(System.out::println); 
    System.out.println(page.getTotal()); 
}
```

### 删除操作

1、根据 id 删除记录

```java
//测试删除
@Test
public void testDeleteById(){
    userMapper.deleteById(1390564342914650114L);
}

//通过id批量删除
@Test
public void testDeleteBatchId(){
    userMapper.deleteBatchIds(Arrays.asList(1,2));
}

//通过map删除
@Test
public void testDeleteMap(){
    HashMap<String,Object> map = new HashMap<>();
    map.put("name","数据结构");
    userMapper.deleteByMap(map);
}
```

### 逻辑删除

物理删除 ：从数据库中直接移除  

逻辑删除 ：再数据库中没有被移除，而是通过一个变量来让他失效！ deleted = 0 => deleted = 1 

管理员可以查看被删除的记录！防止数据的丢失，类似于回收站

1、在数据表中增加一个 deleted 字段

![](https://note.youdao.com/yws/api/personal/file/0B66BBE9318B41D29935783AF7104393?method=download&shareKey=87cfddfbab1281136f8e88587b9bd343)

deleted默认为0

2、实体类中增加属性

```java
@TableLogic //逻辑删除 
private Integer deleted;
```

3、配置

```java
// 逻辑删除组件！ 
@Bean 
public ISqlInjector sqlInjector() 
{ 
    return new LogicSqlInjector(); 
}
```

```properties
# 配置逻辑删除 
mybatis-plus.global-config.db-config.logic-delete-value=1 
mybatis-plus.global-config.db-config.logic-not-delete-value=0
```

4、测试一下删除

```java
//测试删除
@Test
public void testDeleteById(){
    userMapper.deleteById(1390563847936491521L);
}
```

![](https://note.youdao.com/yws/api/personal/file/B472476FDB37409892D58859F48829B6?method=download&shareKey=61a592cf55009e819441cba2e61cf2ea)

记录依旧在数据库，但是值确已经变化了！

![](https://note.youdao.com/yws/api/personal/file/349579AE03D7418CBE856353F14B7463?method=download&shareKey=98ce72fc694db906c902113f2f9a8323)

### 性能分析插件

我们在平时的开发中，会遇到一些慢sql。测试！ druid,,,,, 

作用：性能分析拦截器，用于输出每条 SQL 语句及其执行时间 

MP也提供性能分析插件，如果超过这个时间就停止运行！ 

1、导入插件

```java
/**
     * SQL执行效率插件
     */
    @Bean
    @Profile({"dev","test"})// 设置 dev test 环境开启，保证我们的效率
    public PerformanceInterceptor performanceInterceptor() {
        PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
        performanceInterceptor.setMaxTime(10);// ms设置sql执行的最大时间，如果超过了则不执行并抛出异常,正常为100ms
        performanceInterceptor.setFormat(true);// 是否格式化代码
        return performanceInterceptor;
    }
```

要在SpringBoot中配置环境为dev或者 test 环境

2、测试使用

```java
@Test 
void contextLoads() { 
    // 参数是一个 Wrapper ，条件构造器，这里我们先不用 null 
    // 查询全部用户 
    List<User> users = userMapper.selectList(null); 
    users.forEach(System.out::println); 
}
```

![](https://note.youdao.com/yws/api/personal/file/B05D18D1B6E34413AED347E414656FC6?method=download&shareKey=99bb06c0038e5c4623e72bea60f967e7)

使用性能分析插件，可以帮助我们提高效率

### 条件构造器

[构造器语法](https://mp.baomidou.com/guide/wrapper.html#ne)

十分重要：Wrapper

![](https://note.youdao.com/yws/api/personal/file/678E8994012D40F2A2BF03E344E647B1?method=download&shareKey=4962598db4ba84c427b9be77945fd455)

```java
// 模糊查询
    @Test
    void test5(){
        QueryWrapper<User> wrapper = new QueryWrapper<>();
        // id 在子查询中查出来
        wrapper.inSql("id","select id from user where id<3");
        List<Object> objects = userMapper.selectObjs(wrapper);
        objects.forEach(System.out::println);
}
```

等同于

```sql
SELECT
        id,
        name,
        age,
        email,
        version,
        deleted,
        create_time,
        update_time 
    FROM
        user 
    WHERE
        deleted=0 
        AND id IN (
            select
                id 
            from
                user 
            where
                id<3
        )
```
#### AbstractWrapper

* **allEq：全部等于**  

  `allEq({id:1,name:"老王",age:null})`--->`id = 1 and name = '老王' and age is null`

* **eq：等于**  

  `eq("name", "老王")`--->`name = '老王'`

* **ne：不等于**  

  `ne("name", "老王")`--->`name <> '老王'`

* **gt：大于**

  `gt("age", 18)`--->`age > 18`

* **ge：大于等于**

  `ge("age", 18)`--->`age >= 18`

* **lt：小于**

  `lt("age", 18)`--->`age < 18`

* **le：小于等于**

  `le("age", 18)`--->`age <= 18`

* **between：取一个范围**

  `between("age", 18, 30)`--->`age between 18 and 30`

* **notBetween：不在该范围内**

  `notBetween("age", 18, 30)`--->`age not between 18 and 30`

* **like：模糊搜索**

  `like("name", "王")`--->`name like '%王%'`

* **notLike：不取该模糊值**

  `notLike("name", "王")`--->`name not like '%王%'`

* **likeLeft**

  ``likeLeft("name", "王")`--->`name like '%王'`

* **likeRight**

  `likeRight("name", "王")`--->`name like '王%'`

* **isNull：是否为空**

  `isNull("name")`--->`name is null`

* **isNotNull：是否不为空**

  `isNotNull("name")`--->`name is not null`

* **in**

  `in("age",{1,2,3})`--->`age in (1,2,3)`

  `in("age", 1, 2, 3)`--->`age in (1,2,3)`

* **notIn**

  `notIn("age",{1,2,3})`--->`age not in (1,2,3)`

  `notIn("age", 1, 2, 3)`--->`age not in (1,2,3)`

* **inSql：插入sql语句**

  `inSql("age", "1,2,3,4,5,6")`--->`age in (1,2,3,4,5,6)`

  `inSql("id", "select id from table where id < 3")`--->`id in (select id from table where id < 3)`

* **notInSql**

  `notInSql("age", "1,2,3,4,5,6")`--->`age not in (1,2,3,4,5,6)`

  `notInSql("id", "select id from table where id < 3")`--->`id not in (select id from table where id < 3)`

* **groupBy：分组**

  `groupBy("id", "name")`--->`group by id,name`

* **orderByAsc：升序**

  `orderByAsc("id", "name")`--->`order by id ASC,name ASC`

* **orderByDesc：降序**

  `orderByDesc("id", "name")`--->`order by id DESC,name DESC`

* **orderBy：排序**

  `orderBy(true, true, "id", "name")`--->`order by id ASC,name ASC`

* **having：用于聚合函数**

  `having("sum(age) > 10")`--->`having sum(age) > 10`

* **func**
  
  func(i -> if(true) {i.eq("id", 1)} else {i.ne("id", 1)})
  
* **or：拼接OR**

  `eq("id",1).or().eq("name","老王")`--->`id = 1 or name = '老王'`

  `or(i -> i.eq("name", "李白").ne("status", "活着"))`--->`or (name = '李白' and status <> '活着')`

* **and：and嵌套**

  `and(i -> i.eq("name", "李白").ne("status", "活着"))`--->`and (name = '李白' and status <> '活着')`

* **nested：正常嵌套**

  `nested(i -> i.eq("name", "李白").ne("status", "活着"))`--->`(name = '李白' and status <> '活着')`

* **apply：拼接sql**

  `apply("id = 1")`--->`id = 1`

  `apply("date_format(dateColumn,'%Y-%m-%d') = {0}", "2008-08-08")`--->`date_format(dateColumn,'%Y-%m-%d') = '2008-08-08'")`

* **last：无视优化规则直接拼接到 sql 的最后**

  last("limit 1")

* **exists：拼接exists**

  `exists("select id from table where age = 1")`--->`exists (select id from table where age = 1)`

* **notExists：拼接NOT EXISTS**

  `notExists("select id from table where age = 1")`--->`not exists (select id from table where age = 1)`

### 代码自动生成器

AutoGenerator 是 MyBatis-Plus 的代码生成器，通过 AutoGenerator 可以快速生成 Entity、 Mapper、Mapper XML、Service、Controller 等各个模块的代码，极大的提升了开发效率。 

```java
// 代码自动生成器
public class Code {
    public static void main(String[] args) { // 需要构建一个 代码自动生成器 对象
         AutoGenerator mpg = new AutoGenerator();
        // 配置策略
        // 1、全局配置
         GlobalConfig gc = new GlobalConfig();
         String projectPath = System.getProperty("user.dir");
         gc.setOutputDir(projectPath+"/src/main/java");
         gc.setAuthor("二雄");
         gc.setOpen(false);
         gc.setFileOverride(false);// 是否覆盖
         gc.setServiceName("%sService");//去Service的I前缀
         gc.setIdType(IdType.ID_WORKER);
         gc.setDateType(DateType.ONLY_DATE);
         gc.setSwagger2(true);
         mpg.setGlobalConfig(gc);

        // 2、设置数据源
         DataSourceConfig dsc = new DataSourceConfig();
         dsc.setUrl("jdbc:mysql://localhost:3306/mybatis_plus?useSSL=false&useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2B8");
         dsc.setDriverName("com.mysql.cj.jdbc.Driver");
         dsc.setUsername("root");
         dsc.setPassword("123456");
         dsc.setDbType(DbType.MYSQL);
         mpg.setDataSource(dsc);

        // 3、包的配置
         PackageConfig pc = new PackageConfig();
         pc.setModuleName("blog");
         pc.setParent("com.example");
         pc.setEntity("pojo");
         pc.setMapper("mapper");
         pc.setService("service");
         pc.setController("controller");
         mpg.setPackageInfo(pc);
        // 4、策略配置
         StrategyConfig strategy = new StrategyConfig();
         strategy.setInclude("user");// 设置要映射的表名
         strategy.setNaming(NamingStrategy.underline_to_camel);
         strategy.setColumnNaming(NamingStrategy.underline_to_camel);
         strategy.setEntityLombokModel(true);// 自动lombok；
         strategy.setLogicDeleteFieldName("deleted");// 自动填充配置
//         TableFill gmtCreate = new TableFill("gmt_create", FieldFill.INSERT);
//         TableFill gmtModified = new TableFill("gmt_modified", FieldFill.INSERT_UPDATE);
//         ArrayList<TableFill> tableFills = new ArrayList<>(); tableFills.add(gmtCreate);
//         tableFills.add(gmtModified); strategy.setTableFillList(tableFills);
        // 乐观锁
        strategy.setVersionFieldName("version");
        strategy.setRestControllerStyle(true);
        strategy.setControllerMappingHyphenStyle(true);//localhost:8080/hello_id_2
        mpg.setStrategy(strategy); mpg.execute();//执行
    }
}
```