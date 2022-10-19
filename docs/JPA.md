# JPA

> https://www.bilibili.com/video/BV1Y4411W7Rx?p=1&spm_id_from=pageDriver&vd_source=afbddbbee93fbe1f3b36256159f5f4e8

[TOC]

#### ORM思想

主要目的：操作实体类就相当于操作数据库表

建立两个映射关系：

* 实体类和表的映射关系
* 实体类中属性和表中字段的映射关系

ORM就是建立实体类和数据库表之间的关系，从而达到操作实体类就相当于操作数据库表的目的。

常见的orm框架：Mybatis（ibatis）、Hibernate、Jpa

#### JPA概述

JPA的全称是Java Persistence API， 即Java 持久化API，是SUN公司推出的一套基于ORM的规范，内部是由一系列的接口和抽象类构成。

JPA规范本质上就是一种ORM规范，如果使用JPA规范进行数据库操作，底层需要hibernate作为其实现类完成数据持久化工作。

![](https://note.youdao.com/yws/api/personal/file/WEB377aef875963f0afbb41393515039407?method=download&shareKey=679538a66761523b4d347357cc069062)

#### 实例

resource下创建文件夹META-INF，在该文件夹下创建persistence.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence xmlns="http://java.sun.com/xml/ns/persistence"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://java.sun.com/xml/ns/persistence
    http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd"
             version="2.0">
    <!--配置持久化单元
        name：持久化单元名称
        transaction-type：事务类型
             RESOURCE_LOCAL：本地事务管理
             JTA：分布式事务管理 -->
    <persistence-unit name="myJpa" transaction-type="RESOURCE_LOCAL">
        <!--配置JPA规范的服务提供商 -->
        <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>
        <properties>
            <!-- 数据库驱动 -->
            <property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver" />
            <!-- 数据库地址 -->
            <property name="javax.persistence.jdbc.url" value="jdbc:mysql:///jpa"/>
            <!-- 数据库用户名 -->
            <property name="javax.persistence.jdbc.user" value="root" />
            <!-- 数据库密码 -->
            <property name="javax.persistence.jdbc.password" value="123456" />

            <!--配置jpa实现方(hibernate)的配置信息
               显示sql           ：   false|true
               自动创建数据库表    ：  hibernate.hbm2ddl.auto
                       create      : 程序运行时创建数据库表（如果有表，先删除表再创建）
                       update      ：程序运行时创建表（如果有表，不会创建表）
                       none        ：不会创建表
           -->
            <property name="hibernate.show_sql" value="true" />
            <property name="hibernate.hbm2ddl.auto" value="update" />
        </properties>
    </persistence-unit>
</persistence>
```

创建实体类Customer

```java
@Entity //声明实体类
@Table(name="cst_customer") //建立实体类和表的映射关系
public class Customer {

    /**
     * @Id：声明主键的配置
     * @GeneratedValue:配置主键的生成策略
     *      strategy
     *          GenerationType.IDENTITY ：自增，mysql
     *                 * 底层数据库必须支持自动增长（底层数据库支持的自动增长方式，对id自增）
     *          GenerationType.SEQUENCE : 序列，oracle
     *                  * 底层数据库必须支持序列
     *          GenerationType.TABLE : jpa提供的一种机制，通过一张数据库表的形式帮助我们完成主键自增
     *          GenerationType.AUTO ： 由程序自动的帮助我们选择主键生成策略
     * @Column:配置属性和字段的映射关系
     *      name：数据库表中字段的名称
     */
    @Id//声明当前私有属性为主键
    @GeneratedValue(strategy=GenerationType.IDENTITY) //配置主键的生成策略
    @Column(name="cust_id") //指定和表中cust_id字段的映射关系
    private Long custId;

    @Column(name="cust_name") //指定和表中cust_name字段的映射关系
    private String custName;

    @Column(name="cust_source")//指定和表中cust_source字段的映射关系
    private String custSource;

    @Column(name="cust_industry")//指定和表中cust_industry字段的映射关系
    private String custIndustry;

    @Column(name="cust_level")//指定和表中cust_level字段的映射关系
    private String custLevel;

    @Column(name="cust_address")//指定和表中cust_address字段的映射关系
    private String custAddress;

    @Column(name="cust_phone")//指定和表中cust_phone字段的映射关系
    private String custPhone;
    
    //set、get方法....
```

**jpa操作的操作步骤**

1.加载配置文件创建实体管理器工厂
	Persisitence:静态方法(根据持久化单元名称创建实体管理器工厂)
		createEntityMnagerFactory (持久化单元名称)
	作用:创建实体管理器工厂
2.根据实体管理器工厂，创建实体管理器
	EntityManagerFactory :获取EntityManager对 象
	方法: createEntityManager

* 内部维护的很多的内容
  		  内部维护了数据库信息，
    	维护了缓存信息
    	维护了所有的实体管理器对象
    	再创建EntityManlagerFactory的过程中会根据配置创建数据库表

* EntityManagerFactory的创建过 程比较浪费资源
  特点:线程安全的对象
  多个线程访问同一个EntityManagerFactory不会有线程安全问题

3.创建事务对象，开启事务
	EntityManager对象:实体类管理器
  		beginTransaction :创建事务对象
  		presist :保存
 		 merge:更新
  		remove :删除
  		find/getRefrence :: 根据id查询]
	Transaction对象: 事务
  		begin:开启事务
 		 commit:提交事务
		  rollback:回滚
4.增删改查操作

**新增**

persist()

```java
    public void testSave() {
//        //1.加载配置文件创建工厂（实体管理器工厂）对象
//        EntityManagerFactory factory = Persistence.createEntityManagerFactory("myJpa");
//        //2.通过实体管理器工厂获取实体管理器
//        EntityManager em = factory.createEntityManager();
        EntityManager em = JpaUtils.getEntityManager();
        //3.获取事务对象，开启事务
        EntityTransaction tx = em.getTransaction(); //获取事务对象
        tx.begin();//开启事务
        //4.完成增删改查操作：保存一个客户到数据库中
        Customer customer = new Customer();
        customer.setCustName("传智");
        customer.setCustIndustry("教育");
        //保存，
        em.persist(customer); //保存操作
        //5.提交事务
        tx.commit();
        //6.释放资源
        em.close();
        //factory.close();
    }
```

**查询**

根据ID查询：find()、getReference()

```java
Customer customer = entityManager.find(Customer.class, 1L); //立即加载
Customer customer = entityManager.getReference(Customer.class, 1L); //延迟加载（懒加载）
```

**删除**

根据ID删除：remove()

```java
 entityManager.remove(customer);
```

**更新**

根据ID更新：merge()

```java
entityManager.merge(customer);
```

#### Jpql查询

查询语句中用的是类和类的属性

查询全部数据

getResultList() ： 直接将查询结果封装为list集合

```java
/**
     * 排序查询： 倒序查询全部客户（根据id倒序）
     *      sql：SELECT * FROM cst_customer ORDER BY cust_id DESC
     *      jpql：from Customer order by custId desc
     *
     * 进行jpql查询
     *      1.创建query查询对象
     *      2.对参数进行赋值
     *      3.查询，并得到返回结果
     */
public void testFindAll() {
        //1.获取entityManager对象
        EntityManager em = JpaUtils.getEntityManager();
        //2.开启事务
        EntityTransaction tx = em.getTransaction();
        tx.begin();
        //3.查询全部
        String jpql ="from Customer order by custId desc";
        Query query = em.createQuery(jpql);

        //发送集合，并封装结果集
        List list = query.getResultList();

        for (Object obj : list){
            System.out.println(obj);
        }

        //4.提交事务
        tx.commit();
        //5.释放资源
        em.close();
    }
```

getSingleResult() : 得到唯一的结果集

```java
String jpql = "select count(custId) from Customer";
Query query = em.createQuery(jpql);
Object result = query.getSingleResult();
System.out.println(result);
```

分页查询

```java
String jpql = "from Customer";
Query query = em.createQuery(jpql);
//ii.对参数赋值 -- 分页参数
//起始索引
query.setFirstResult(0);
//每页查询的条数
query.setMaxResults(2);
//iii.发送查询，并封装结果
List list = query.getResultList();
for(Object obj : list) {
    System.out.println(obj);
}
```

条件查询

```java
String jpql = "from Customer where custName like ? ";
Query query = em.createQuery(jpql);
//ii.对参数赋值 -- 占位符参数
//第一个参数：占位符的索引位置（从1开始），第二个参数：取值
query.setParameter(1,"传智播客%");
//iii.发送查询，并封装结果
List list = query.getResultList();
for(Object obj : list) {
    System.out.println(obj);
}
```

### Spring Data JAP

Spring Data JPA 是 Spring 基于 ORM 框架、JPA 规范的基础上封装的一套JPA应用框架，可使开发者用极简的代码即可实现对数据库的访问和操作。它提供了包括增删改查等在内的常用功能，且易于扩展。

![](https://note.youdao.com/yws/api/personal/file/WEB4f42c6b53a439b2a8a1fc6a33c28fc79?method=download&shareKey=ce2091a91e2c4cea886b3b4182aeb2ec)



```java
/**
 * 符合SpringDataJpa的dao层接口规范
 *      JpaRepository<操作的实体类类型，实体类中主键属性的类型>
 *          * 封装了基本CRUD操作
 *      JpaSpecificationExecutor<操作的实体类类型>
 *          * 封装了复杂查询（分页）
 */
public interface CustomerDao extends JpaRepository<Customer,Long> ,JpaSpecificationExecutor<Customer> {
    ......
}
```

#### 常用方法

	findOne(id)：根据id查询   立即加载
	getOne(id)：根据id查询  延迟加载
	save(customer):保存或者更新（依据：传递的实体类对象中，是否包含id属性）
	delete(id)：根据id删除
	findAll()：查询全部
	count()：统计总条数
	exists()：判断是否存在
```java
//根据id查询
 Customer customer = customerDao.findOne(4l);
/** save : 保存或者更新
     *      根据传递的对象是否存在主键id，
     *      如果没有id主键属性：保存
     *      存在id主键属性，根据id查询数据，更新数据
     */
customerDao.save(customer);
//根据id删除
customerDao.delete(3l);
//查询所有
List<Customer> list = customerDao.findAll();
//统计总条数
long count = customerDao.count();
//判断id为4的客户是否存在
boolean exists = customerDao.exists(4l);
```

**springDataJpa的运行过程和原理剖析**

1. 通过JdkDynamicAopProxy的invoke方法创建了一个动态代理对象
2. SimpleJpaRepository当中封装了JPA的操作（借助JPA的api完成数据库的CRUD）
3. 通过hibernate完成数据库操作（封装了jdbc）

#### JPQL查询

jpql的查询方式
		jpql ： jpa query language  （jpq查询语言）
		特点：语法或关键字和sql语句类似
			查询的是类和类中的属性

需要将JPQL语句配置到接口方法上
	1.特有的查询：需要在dao接口上配置方法
	2.在新添加的方法上，使用注解的形式配置jpql查询语句
	3.注解 ： @Query

```java
public interface CustomerDao extends JpaRepository<Customer,Long> ,JpaSpecificationExecutor<Customer> {
    //配置jpql语句，使用的@Query注解
    @Query(value="from Customer where custName = ?")
	public Customer findJpql(String custName);
    
     /**
     *  对于多个占位符参数
     *      赋值的时候，默认的情况下，占位符的位置需要和方法参数中的位置保持一致
     *  可以指定占位符参数的位置
     *      ? 索引的方式，指定此占位的取值来源
     */
    @Query(value = "from Customer where custName = ?2 and custId = ?1")
    public Customer findCustNameAndId(Long id,String name);
    
      /**
     * 使用jpql完成更新操作
     *  @Query : 代表的是进行查询
     *      * 声明此方法是用来进行更新操作
     *  @Modifying
     *      * 当前执行的是一个更新操作
     */
    @Query(value = " update Customer set custName = ?2 where custId = ?1 ")
    @Modifying
    public void updateCustomer(long custId,String custName);
    
     /**
     *  Query : 配置sql查询, 模糊查询
     *      value ： sql语句
     *      nativeQuery ： 查询方式
     *          true ： sql查询
     *          false：jpql查询
     */
    @Query(value="select * from cst_customer where cust_name like ?1",nativeQuery = true)
    public List<Object [] > findSql(String name);
}
```

```java
 	/**
     * 测试jpql的 更新操作
     *  * springDataJpa中使用jpql完成 更新/删除操作
     *         * 需要手动添加事务的支持
     *         * 默认会执行结束之后，回滚事务
     *   @Rollback : 设置是否自动回滚
     *          false | true
     */
    @Transactional //添加事务的支持
    @Rollback(value = false)
    public void testUpdateCustomer() {
        customerDao.updateCustomer(4l,"黑马程序员");
    }

	/**
	 *  模糊查询,加%
	*/
   public void testFindSql() {
        List<Object[]> list = customerDao.findSql("传智播客%");
        for(Object [] obj : list) {
            System.out.println(Arrays.toString(obj));
        }
    }
```

#### 方法名称规则查询

1. 是对jpql查询，更加深入的一层封装
2. 我们只需要安装SpringDataJpa提供的方法名称规则定义方法，不需要再去配置jpql语句，完成查询

**findBy开头：代表查询**

​	对象中属性的名称(首字母大写)

含义：根据属性名称进行查询

```java
public interface CustomerDao extends JpaRepository<Customer,Long> ,JpaSpecificationExecutor<Customer> {
 /**
     * 方法名的约定：
     *      findBy : 查询
     *  findByCustName   --   根据客户名称查询
     *
     *  再springdataJpa的运行阶段
     *          会根据方法名称进行解析  findBy    from  xxx(实体类)
     *                                      属性名称      where  custName =
     *
     *      1.findBy  + 属性名称 （根据属性名称进行完成匹配的查询=）
     *      2.findBy  + 属性名称 + “查询方式（Like | isnull）”
     *          findByCustNameLike
     *      3.多条件查询
     *          findBy + 属性名 + “查询方式”   + “多条件的连接符（and|or）”  + 属性名 + “查询方式”
     */
    public Customer findByCustName(String custName);
    
    public List<Customer> findByCustNameLike(String custName);

    //使用客户名称模糊匹配和客户所属行业精准匹配的查询
    public Customer findByCustNameLikeAndCustIndustry(String custName,String custIndustry);
}
```

```java
 	//测试方法命名规则的查询
    @Test
    public void testNaming() {
        Customer customer = customerDao.findByCustName("传智播客");
        System.out.println(customer);
    }

    //测试方法命名规则的查询
    @Test
    public void testFindByCustNameLike() {
        List<Customer> list = customerDao.findByCustNameLike("传智播客%");
        for (Customer customer : list) {
            System.out.println(customer);
        }
    }

    //测试方法命名规则的查询
    @Test
    public void testFindByCustNameLikeAndCustIndustry() {
        Customer customer = customerDao.findByCustNameLikeAndCustIndustry("传智播客1%", "it教育");
        System.out.println(customer);
    }
```

#### Specifications动态查询

第一 Specifications动态查询

JpaSpecificationExecutor 方法列表

```java
T findOne(Specification<T> spec);  //查询单个对象

List<T> findAll(Specification<T> spec);  //查询列表

//查询全部，分页
//pageable：分页参数
//返回值：分页pageBean（page：是springdatajpa提供的）
Page<T> findAll(Specification<T> spec, Pageable pageable);

//查询列表
//Sort：排序参数
List<T> findAll(Specification<T> spec, Sort sort);

long count(Specification<T> spec);//统计查询
```

Specification ：查询条件
	自定义我们自己的Specification实现类
		// root：查询的根对象（查询的任何属性都可以从根对象中获取）
		// CriteriaQuery：顶层查询对象，自定义查询方式（了解：一般不用）
		// CriteriaBuilder：查询的构造器，封装了很多的查询条件
		Predicate toPredicate(Root<T> root, CriteriaQuery<?> query, CriteriaBuilder cb); //封装查询条件

```java
	============================单条件查询=========================
    @Test
    public void testSpec() {
        //匿名内部类
        /**
         * 自定义查询条件
         *      1.实现Specification接口（提供泛型：查询的对象类型）
         *      2.实现toPredicate方法（构造查询条件）
         *      3.需要借助方法参数中的两个参数（
         *          root：获取需要查询的对象属性
         *          CriteriaBuilder：构造查询条件的，内部封装了很多的查询条件（模糊匹配，精准匹配）
         *       ）
         *  案例：根据客户名称查询，查询客户名为传智播客的客户
         *          查询条件
         *              1.查询方式
         *                  cb对象
         *              2.比较的属性名称
         *                  root对象
         */
        Specification<Customer> spec = new Specification<Customer>() {
            @Override
            public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
                //1.获取比较的属性
                Path<Object> custName = root.get("custName");
                //2.构造查询条件  ：    select * from cst_customer where cust_name = '传智播客'
                /**
                 * 第一个参数：需要比较的属性（path对象）
                 * 第二个参数：当前需要比较的取值
                 */
                Predicate predicate = cb.equal(custName, "传智播客");//进行精准的匹配  （比较的属性，比较的属性的取值）
                return predicate;
            }
        };
        Customer customer = customerDao.findOne(spec);
        System.out.println(customer);
    }

     ============================多条件查询=========================
	/**
     *      案例：根据客户名（传智播客）和客户所属行业查询（it教育）
     *
     */
    @Test
    public void testSpec1() {
        /**
         *  root:获取属性
         *      客户名
         *      所属行业
         *  cb：构造查询
         *      1.构造客户名的精准匹配查询
         *      2.构造所属行业的精准匹配查询
         *      3.将以上两个查询联系起来
         */
        Specification<Customer> spec = new Specification<Customer>() {
            @Override
            public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
                Path<Object> custName = root.get("custName");//客户名
                Path<Object> custIndustry = root.get("custIndustry");//所属行业

                //构造查询
                //1.构造客户名的精准匹配查询
                Predicate p1 = cb.equal(custName, "传智播客");//第一个参数，path（属性），第二个参数，属性的取值
                //2..构造所属行业的精准匹配查询
                Predicate p2 = cb.equal(custIndustry, "it教育");
                //3.将多个查询条件组合到一起：组合（满足条件一并且满足条件二：与关系，满足条件一或满足条件二即可：或关系）
                Predicate and = cb.and(p1, p2);//以与的形式拼接多个查询条件
                // cb.or();//以或的形式拼接多个查询条件
                return and;
            }
        };
        Customer customer = customerDao.findOne(spec);
        System.out.println(customer);
    }

   ============================模糊查询=========================
   /**
     * 案例：完成根据客户名称的模糊匹配，返回客户列表
     *      客户名称以 ’传智播客‘ 开头
     *
     * equal ：直接的到path对象（属性），然后进行比较即可
     * gt，lt,ge,le,like : 得到path对象，根据path指定比较的参数类型，再去进行比较
     *      指定参数类型：path.as(类型的字节码对象)
     */
    @Test
    public void testSpec3() {
        //构造查询条件
        Specification<Customer> spec = new Specification<Customer>() {
            @Override
            public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
                //查询属性：客户名
                Path<Object> custName = root.get("custName");
                //查询方式：模糊匹配
                Predicate like = cb.like(custName.as(String.class), "传智播客%");
                return like;
            }
        };
		List<Customer> list = customerDao.findAll(spec);
        
        //添加排序
        //创建排序对象,需要调用构造方法实例化sort对象
        //第一个参数：排序的顺序（倒序，正序）
        //   Sort.Direction.DESC:倒序
        //   Sort.Direction.ASC ： 升序
        //第二个参数：排序的属性名称
        Sort sort = new Sort(Sort.Direction.DESC,"custId");
        List<Customer> list = customerDao.findAll(spec, sort);
    }


	============================分页查询=========================
	/**
     *      Specification: 查询条件
     *      Pageable：分页参数
     *          分页参数：查询的页码，每页查询的条数
     *          findAll(Specification,Pageable)：带有条件的分页
     *          findAll(Pageable)：没有条件的分页
     *  返回：Page（springDataJpa为我们封装好的pageBean对象，数据列表，共条数）
     */
    @Test
    public void testSpec4() {

        Specification spec = null;
        //PageRequest对象是Pageable接口的实现类
        /**
         * 创建PageRequest的过程中，需要调用他的构造方法传入两个参数
         *      第一个参数：当前查询的页数（从0开始）
         *      第二个参数：每页查询的数量
         */
        Pageable pageable = new PageRequest(0,2);
        //分页查询
        Page<Customer> page = customerDao.findAll(null, pageable);
        System.out.println(page.getContent()); //得到数据集合列表
        System.out.println(page.getTotalElements());//得到总条数
        System.out.println(page.getTotalPages());//得到总页数
    }
```

#### 多表操作

第二 多表之间的关系和操作多表的操作步骤

表关系
		**一对一**
		**一对多**
			一的一方：主表
			多的一方：从表
			外键：需要再从表上新建一列作为外键，他的取值来源于主表的主键
		**多对多**
			中间表：中间表中最少应该由两个字段组成，这两个字段做为外键指向两张表的主键，又组成了联合主键

分析步骤
		1.明确表关系
		2.确定表关系（描述 外键|中间表）
		3.编写实体类，再实体类中描述表关系（包含关系）
		4.配置映射关系

第三 完成多表操作

i.一对多操作
		案例：客户和联系人的案例（一对多关系）
			客户：一家公司
			联系人：这家公司的员工

​			一个客户可以具有多个联系人
​			一个联系人从属于一家公司

​	分析步骤
​			1.明确表关系
​				一对多关系
​			2.确定表关系（描述 外键|中间表）
​				主表：客户表
​				从表：联系人表

​	再从表上添加外键
​		3.编写实体类，再实体类中描述表关系（包含关系）
​			客户：再客户的实体类中包含一个联系人的集合
​			联系人：在联系人的实体类中包含一个客户的对象
​		4.配置映射关系

**映射的注解说明**

@OneToMany:
作用：建立一对多的关系映射
属性：
targetEntityClass：指定多的多方的类的字节码
mappedBy：指定从表实体类中引用主表对象的名称。
cascade：指定要使用的级联操作
fetch：指定是否采用延迟加载
orphanRemoval：是否使用孤儿删除
@ManyToOne
作用：建立多对一的关系
属性：
targetEntityClass：指定一的一方实体类字节码
cascade：指定要使用的级联操作
fetch：指定是否采用延迟加载
optional：关联是否可选。如果设置为false，则必须始终存在非空关系。
@JoinColumn
作用：用于定义主键字段和外键字段的对应关系。
属性：
name：指定外键字段的名称
referencedColumnName：指定引用主表的主键字段名称
unique：是否唯一。默认值不唯一
nullable：是否允许为空。默认值允许。
insertable：是否允许插入。默认值允许。
updatable：是否允许更新。默认值允许。
columnDefinition：列的定义信息。

**一对多映射**

```java
===========================一对多============================
========================有外键，主表==========================
//配置客户和联系人之间的关系（一对多关系） 一个客户对应多个联系人
    /**
     * 使用注解的形式配置多表关系
     *      1.声明关系
     *          @OneToMany : 配置一对多关系
     *              targetEntity ：对方对象的字节码对象
     *      2.配置外键（中间表）
     *              @JoinColumn : 配置外键
     *                  name：外键字段名称
     *                  referencedColumnName：参照的主表的主键字段名称
     *
     *  * 在客户实体类上（一的一方）添加了外键了配置，所以对于客户而言，也具备了维护外键的作用
     */
    @OneToMany(targetEntity = LinkMan.class)
    @JoinColumn(name = "lkm_cust_id",referencedColumnName = "cust_id")
    private Set<LinkMan> linkMans = new HashSet<>();
	===================有外键，主表，放弃外键维护权,级联操作，同时操作两个表=====================
    /**
     * 放弃外键维护权
     *      mappedBy：对方配置关系的属性名称\
     * cascade : 配置级联（可以配置到设置多表的映射关系的注解上）
     *      CascadeType.all         : 所有
     *                  MERGE       ：更新
     *                  PERSIST     ：保存
     *                  REMOVE      ：删除
     *
     * fetch : 配置关联对象的加载方式
     *          EAGER   ：立即加载
     *          LAZY    ：延迟加载

      */
    @OneToMany(mappedBy = "customer",cascade = CascadeType.ALL)
    private Set<LinkMan> linkMans = new HashSet<>();
    
========================有外键，从表==========================
  /**
     * 配置联系人到客户的多对一关系
     *     使用注解的形式配置多对一关系
     *      1.配置表关系
     *          @ManyToOne : 配置多对一关系
     *              targetEntity：对方的实体类字节码
     *      2.配置外键（中间表）
     *
     * * 配置外键的过程，配置到了多的一方，就会在多的一方维护外键
     */
    @ManyToOne(targetEntity = Customer.class,fetch = FetchType.LAZY)
    @JoinColumn(name = "lkm_cust_id",referencedColumnName = "cust_id")
    private Customer customer;    
```

```java
 	@Test
    @Transactional //配置事务
    @Rollback(false) //不自动回滚
    public void testAdd() {
        //创建一个客户，创建一个联系人
        Customer customer = new Customer();
        customer.setCustName("百度");
        LinkMan linkMan = new LinkMan();
        linkMan.setLkmName("小李");

        /**
         * 配置了客户到联系人的关系
         *      从客户的角度上：发送两条insert语句，发送一条更新语句更新数据库（更新外键）
         * 由于我们配置了客户到联系人的关系：客户可以对外键进行维护
         */
        customer.getLinkMans().add(linkMan);
		/**
         * 配置联系人到客户的关系（多对一）
         *    只发送了两条insert语句
         * 由于配置了联系人到客户的映射关系（多对一）
         */
        //linkMan.setCustomer(customer);

        customerDao.save(customer);
        linkManDao.save(linkMan);
    }

   /**
     * 级联添加：保存一个客户的同时，保存客户的所有联系人
     *      需要在操作主体的实体类上，配置casacde属性
     */
    @Test
    @Transactional //配置事务
    @Rollback(false) //不自动回滚
    public void testCascadeAdd() {
        Customer customer = new Customer();
        customer.setCustName("百度1");
        LinkMan linkMan = new LinkMan();
        linkMan.setLkmName("小李1");
        linkMan.setCustomer(customer);
        customer.getLinkMans().add(linkMan);
        customerDao.save(customer);
    }
```

**多对多映射**

```java
===========================多对多============================
    /**
     * 配置用户到角色的多对多关系
     *      配置多对多的映射关系
     *          1.声明表关系的配置
     *              @ManyToMany(targetEntity = Role.class)  //多对多
     *                  targetEntity：代表对方的实体类字节码
     *          2.配置中间表（包含两个外键）
     *                @JoinTable
     *                  name : 中间表的名称
     *                  joinColumns：配置当前对象在中间表的外键
     *                      @JoinColumn的数组
     *                          name：外键名
     *                          referencedColumnName：参照的主表的主键名
     *                  inverseJoinColumns：配置对方对象在中间表的外键
     */
    @ManyToMany(targetEntity = Role.class)
    @JoinTable(name = "sys_user_role",
            //joinColumns,当前对象在中间表中的外键
            joinColumns = {@JoinColumn(name = "sys_user_id",referencedColumnName = "user_id")},
            //inverseJoinColumns，对方对象在中间表的外键
            inverseJoinColumns = {@JoinColumn(name = "sys_role_id",referencedColumnName = "role_id")}
    )
    private Set<Role> roles = new HashSet<>();
    
    //配置多对多
    @ManyToMany(mappedBy = "roles")  //配置多表关系
    private Set<User> users = new HashSet<>();
    
    ===========================多对多,级联============================
    @ManyToMany(targetEntity = Role.class,cascade = CascadeType.ALL)
```

```java
  /**
     * 保存一个用户，保存一个角色
     *
     *  多对多放弃维护权：被动的一方放弃
     */
    @Test
    @Transactional
    @Rollback(false)
    public void  testAdd() {
        User user = new User();
        user.setUserName("小李");
        Role role = new Role();
        role.setRoleName("java程序员");

        //配置用户到角色关系，可以对中间表中的数据进行维护     1-1
        user.getRoles().add(role);
        //配置角色到用户的关系，可以对中间表的数据进行维护     1-1
        role.getUsers().add(user);

        userDao.save(user);
        roleDao.save(role);
    }
```

**对象导航查询**

查询一个对象的同时，通过此对象查询他的关联对象

从一方查询多方：默认使用延迟加载

从多方查询一方：默认使用立即加载

```java
 /**
     * 对象导航查询：
     *      默认使用的是延迟加载的形式查询的
     *          调用get方法并不会立即发送查询，而是在使用关联对象的时候才会查询
     * 修改配置，将延迟加载改为立即加载
     *      fetch，需要配置到多表映射关系的注解上
     */
    @Test
    @Transactional // 解决在java代码中的no session问题
    public void  testQuery2() {
        //查询id为1的客户
        Customer customer = customerDao.findOne(1l);
        //对象导航查询，此客户下的所有联系人
        Set<LinkMan> linkMans = customer.getLinkMans();
        System.out.println(linkMans.size());
    }

    /**
     * 从联系人对象导航查询他的所属客户
     *      * 默认 ： 立即加载
     */
    @Test
    @Transactional // 解决在java代码中的no session问题
    public void  testQuery3() {
        LinkMan linkMan = linkManDao.findOne(2l);
        //对象导航查询所属的客户
        Customer customer = linkMan.getCustomer();
        System.out.println(customer);
    }

```


