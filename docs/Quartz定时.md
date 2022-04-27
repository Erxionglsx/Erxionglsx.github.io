# Quartz定时

> https://blog.csdn.net/dxx707099957/article/details/93848653

[TOC]

### 简介

Quartz是一个完全由Java编写的开源作业调度框架，为在Java应用程序中进行作业调度提供了简单却强大的机制。Quartz允许开发人员根据时间间隔来调度作业。它实现了作业和触发器的多对多的关系，还能把多个作业与不同的触发器关联。存在某个或某些任务，需要系统定期，自动地执行。

- 它是由OpenSymphony提供的、开源的、java编写的强大任务调度框架
- 几乎可以集成到任何规模的运用程序中，如简单的控制台程序，复杂的大规模分布式电子商务系统
- 可用于创建简单的或复杂的计划任务
- 包含很多企业级功能，如支持JTA和集群等

![](https://note.youdao.com/yws/api/personal/file/B30239FF7D444DCDB7466C0107FF3F31?method=download&shareKey=9073c02bd9e222699be7a4f956460283)

1. 调度器 负责任务管理，内部有时钟监控
2. 触发器 当调度器需要执行任务时，通过触发器启动新的线程去执行
3. JOB/JobDetail 定义任务，JobDetail表示包装任务

### 核心概念

![](https://note.youdao.com/yws/api/personal/file/0A080AB22CAB4FBF87C4CB534F5E57F4?method=download&shareKey=1d676afc712aa3e99a52553d75cd64ab)

Quartz比较关键的两个核心组件分别为Job和Trigger

- job--表示任务是什么
- trigger--表示何时触发任务

![](https://note.youdao.com/yws/api/personal/file/95AEC2D91EB5414699D2746A444FA2DA?method=download&shareKey=5b33a9b5525da12261e77df52110b593)

- Scheduler：调度容器
- Job：Job接口类，即被调度的任务
- JobDetail ：Job的描述类，job执行时的依据此对象的信息反射实例化出Job的具体执行对象。
- Trigger：触发器，存放Job执行的时间策略。用于定义任务调度时间规则。
- JobStore： 存储作业和调度期间的状态
- Calendar：指定排除的时间点（如排除法定节假日）

#### 代码流程

![](https://note.youdao.com/yws/api/personal/file/0A1154B0550444C8B71E168D475408F3?method=download&shareKey=d883073b8be1d8ea25af8b485d9c0f4d)

#### Job 

Job 是一个接口，只有一个方法 void execute(JobExecutionContext context)，开发者实现接口来定义任务。JobExecutionContext 类提供了调度上下文的各种信息。

```java
public class HelloJob implements BaseJob {
  
    private static Logger _log = LoggerFactory.getLogger(HelloJob.class);  
     
    public HelloJob() {}
     
    public void execute(JobExecutionContext context) throws JobExecutionException {
        _log.error("Hello Job执行时间: " + new Date());
    }
}  
```

#### JobDetailImpl 类 / JobDetail 接口

JobDetailImpl类实现了JobDetail接口，用来描述一个 job，定义了job所有属性及其 get/set 方法。

```java
JobDetail jobDetail = JobBuilder.newJob(JobTest.class) //job实现类
    			.withDescription("this is a ram job") //job的描述
				.withIdentity("jobTest", "jobTestGrip") //job 的name和group
				.build();
```

| 属性名        | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| class         | 必须是job实现类（比如JobImpl），用来绑定一个具体job          |
| name          | job 名称。如果未指定，会自动分配一个唯一名称。所有job都必须拥有一个唯一name，如果两个 job 的name重复，则只有最前面的 job 能被调度 |
| group         | job 所属的组名                                               |
| description   | job描述                                                      |
| durability    | 是否持久化。如果job设置为非持久，当没有活跃的trigger与之关联的时候，job 会自动从scheduler中删除。也就是说，非持久job的生命期是由trigger的存在与否决定的 |
| shouldRecover | 是否可恢复。如果 job 设置为可恢复，一旦 job 执行时scheduler发生hard shutdown（比如进程崩溃或关机），当scheduler重启后，该job会被重新执行 |
| jobDataMap    | 除了上面常规属性外，用户可以把任意kv数据存入jobDataMap，实现 job 属性的无限制扩展，执行 job 时可以使用这些属性数据。此属性的类型是JobDataMap，实现了Serializable接口，可做跨平台的序列化传输 |

#### Trigger

是一个类，描述触发Job执行的时间触发规则。主要有 SimpleTrigger 和 CronTrigger 这两个子类。当仅需触发一次或者以固定时间间隔周期执行，SimpleTrigger是最适合的选择；而CronTrigger则可以通过Cron表达式定义出各种复杂时间规则的调度方案：如每早晨9:00执行，周一、周三、周五下午5:00执行等；

```java
Trigger trigger = TriggerBuilder.newTrigger()
				.withDescription("this is a cronTrigger") //描述
				.withIdentity("jobTrigger", "jobTriggerGroup")//类名和分组
				//.withSchedule(SimpleScheduleBuilder.simpleSchedule()) //SimpleTrigger
				.startAt(statTime)  //默认当前时间启动
				.withSchedule(CronScheduleBuilder.cronSchedule("0/2 * * * * ?")) //两秒执行一次
				.build();
```

| 属性名             | 属性类型          | 说明                                                         |
| ------------------ | ----------------- | ------------------------------------------------------------ |
| name               | 所有trigger通用   | trigger名称                                                  |
| group              | 所有trigger通用   | trigger所属的组名                                            |
| description        | 所有trigger通用   | trigger描述                                                  |
| calendarName       | 所有trigger通用   | 日历名称，指定使用哪个Calendar类，经常用来从trigger的调度计划中排除某些时间段 |
| misfireInstruction | 所有trigger通用   | 错过job（未在指定时间执行的job）的处理策略，默认为MISFIRE_INSTRUCTION_SMART_POLICY。 |
| priority           | 所有trigger通用   | 优先级，默认为5。当多个trigger同时触发job时，线程池可能不够用，此时根据优先级来决定谁先触发 |
| jobDataMap         | 所有trigger通用   | 同job的jobDataMap。假如job和trigger的jobDataMap有同名key，通过getMergedJobDataMap()获取的jobDataMap，将以trigger的为准 |
| startTime          | 所有trigger通用   | 触发开始时间，默认为当前时间。决定什么时间开始触发job        |
| endTime            | 所有trigger通用   | 触发结束时间。决定什么时间停止触发job                        |
| nextFireTime       | SimpleTrigger私有 | 下一次触发job的时间                                          |
| previousFireTime   | SimpleTrigger私有 | 上一次触发job的时间                                          |
| repeatCount        | SimpleTrigger私有 | 需触发的总次数                                               |
| timesTriggered     | SimpleTrigger私有 | 已经触发过的次数                                             |
| repeatInterval     | SimpleTrigger私有 | 触发间隔时间                                                 |

#### Calendar

一个Trigger可以和多个Calendar关联，以便排除或包含某些时间点。假设，我们安排每周星期一早上10:00执行任务，但是如果碰到法定的节日，任务则不执行，这时就需要在Trigger触发机制的基础上使用Calendar进行定点排除。

#### Scheduler

调度器，代表一个Quartz的独立运行容器，好比一个『大管家』，这个大管家应该可以接受 Job， 然后按照各种Trigger去运行，Trigger和JobDetail可以注册到Scheduler中，两者在Scheduler中拥有各自的组及名称，组及名称是Scheduler查找定位容器中某一对象的依据，Trigger的组及名称必须唯一，JobDetail的组和名称也必须唯一（但可以和Trigger的组和名称相同，因为它们是不同类型的）。Scheduler定义了多个接口方法，允许外部通过组及名称访问和控制容器中Trigger和JobDetail。

```java
// 注册任务和定时器
scheduler.scheduleJob(jobDetail, trigger);
// 启动 调度器
scheduler.start();
```

Scheduler 可以将 Trigger 绑定到某一 JobDetail 中，这样当 Trigger 触发时，对应的 Job 就被执行。

#### ThreadPool

Scheduler 使用一个线程池作为任务运行的基础设施，任务通过共享线程池中的线程提高运行效率。

### Quartz 调度器

Quartz框架的核心是调度器。调度器负责管理Quartz应用运行时环境。启动时，框架初始化一套worker线程，这套线程被调度器用来执行预定的作业。这就是 Quartz 怎样能并发运行多个作业的原理。Quartz 依赖一套松耦合的线程池管理部件来管理线程环境。

### 表信息解析

1.1.qrtz_blob_triggers : 以Blob 类型存储的触发器。
1.2.qrtz_calendars：存放日历信息， quartz可配置一个日历来指定一个时间范围。
1.3.qrtz_cron_triggers：存放cron类型的触发器。
1.4.qrtz_fired_triggers：存放已触发的触发器。
1.5.qrtz_job_details：存放一个jobDetail信息。
1.6.qrtz_job_listeners：job监听器。
1.7.qrtz_locks： 存储程序的悲观锁的信息(假如使用了悲观锁)。
1.8.qrtz_paused_trigger_graps：存放暂停掉的触发器。
1.9.qrtz_scheduler_state：调度器状态。
1.10.qrtz_simple_triggers：简单触发器的信息。
1.11.qrtz_trigger_listeners：触发器监听器。
1.12.qrtz_triggers：触发器的基本信息。

**Quartz的触发时间的配置的三种方式：**

- cron 方式：采用cronExpression表达式配置时间。
- simple 方式：和JavaTimer差不多，可以指定一个开始时间和结束时间外加一个循环时间。
- calendars 方式：可以和cron配合使用，用cron表达式指定一个触发时间规律，用calendar指定一个范围。

**cron方式需要用到的4张数据表：**

qrtz_triggers，qrtz_cron_triggers，qrtz_fired_triggers，qrtz_job_details。

qrtz_triggers的TRIGGER_STATE字段状态：暂停 PAUSED  运行 ACQUIRED  等待 WAITING



## 定时任务Spring Task

如果使用的是 Spring 或 Spring Boot 框架，可以直接使用 Spring Framework 自带的定时任务

以 Spring Boot 为例，实现定时任务只需两步：

1. 开启定时任务；
2. 添加定时任务。

#### 开启定时任务

开启定时任务只需要在 Spring Boot 的启动类上声明 `@EnableScheduling` 即可，实现代码如下：

```java
@SpringBootApplication
@EnableScheduling // 开启定时任务
public class DemoApplication {
    // do someing
}
```

#### 添加定时任务

定时任务的添加只需要使用 `@Scheduled` 注解标注即可，如果有多个定时任务可以创建多个 `@Scheduled` 注解标注的方法，示例代码如下：

```java
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;
 
@Component // 把此类托管给 Spring，不能省略
public class TaskUtils {
    // 添加定时任务
    @Scheduled(cron = "59 59 23 0 0 5") // cron 表达式，每周五 23:59:59 执行
    public void doTask(){
        System.out.println("我是定时任务~");
    }
}
```

注意：定时任务是自动触发的无需手动干预，也就是说 Spring Boot 启动后会自动加载并执行定时任务。

#### Cron 表达式

Spring Task 的实现需要使用 cron 表达式来声明执行的频率和规则，cron 表达式是由 6 位或者 7 位组成的（最后一位可以省略），每位之间以空格分隔，每位从左到右代表的含义如下：

![](https://note.youdao.com/yws/api/personal/file/8A418F9FB3F34575A6C4B8EC8165E5DF?method=download&shareKey=8c4d66eee23db7e6398d161198d09f0f)

其中 * 和 ? 号都表示匹配所有的时间。

![](https://note.youdao.com/yws/api/personal/file/B53F0DD75836492B96D4BDCEA7141C81?method=download&shareKey=02113e09a731f4ee55c464ecbe814b87)

cron 表达式在线生成地址：https://cron.qqe2.com/

