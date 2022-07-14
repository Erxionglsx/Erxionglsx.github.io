## MQ

> https://www.bilibili.com/video/BV1LQ4y127n4?p=62

[TOC]

![](https://note.youdao.com/yws/api/personal/file/72124571CB2D4C50903BF479AAE3D7C2?method=download&shareKey=cf9bd981d5358d96ab868ce556d32d84)

什么是MQ？

MQ (MessageQueue)，中文是消息队列，字面来看就是存放消息的队列。也就是事件驱动架构中的Broker。

![](https://note.youdao.com/yws/api/personal/file/2969A7AF1FE64BA4A4B800C17F3052FC?method=download&shareKey=0ec72128beae2066f72989c27f57d353)

> https://www.jianshu.com/p/b43b00971d95
>
> https://github.com/Snailclimb/JavaGuide/blob/main/docs/high-performance/message-queue/rocketmq-intro.md

### 什么是MQ

MQ消息队列，是基础数据结构中“先进先出”的一种数据结构。指把要传输的数据（消息）放在队列中，用队列机制来实现消息传递 —— 生产者产生消息并把消息放入队列，然后由消费者去处理。消费者可以到指定队列拉取消息，或者订阅相应的队列，由MQ服务端给其推送消息。RocketMQ使用的消息模型是标准的发布-订阅模型。

异步通信的优点:

- 耦合度低
- 吞吐量提升
- 故障隔离
- 流量削峰
- 异步、解耦、削峰

异步通信的缺点:

- 系统可用性降低
- 架构复杂了，业务没有明显的流程线，不好追踪管理
- 一致性问题

### 关键概念

![](https://note.youdao.com/yws/api/personal/file/294226FA26FA465EBA05D0D5E3E4BD48?method=download&shareKey=549cb7f78f823bfe3863a27abd28b19b)

#### 1.1.1 消息模型（Message Model）

RocketMQ主要由 Producer、Broker、Consumer 三部分组成，其中Producer 负责生产消息，Consumer 负责消费消息，Broker 负责存储消息。**Broker 在实际部署过程中对应一台服务器**，每个 Broker 可以存储多个Topic的消息，每个Topic的消息也可以分片存储于不同的 Broker。Message Queue 用于存储消息的物理地址，每个Topic中的消息地址存储于多个 Message Queue 中。ConsumerGroup 由多个Consumer 实例构成。

#### 1.1.2 消息生产者（Producer）

负责生产消息，一般由业务系统负责生产消息。一个消息生产者会把业务应用系统里产生的消息发送到broker服务器。RocketMQ提供多种发送方式，同步发送、异步发送、顺序发送、单向发送。**同步和异步方式均需要Broker返回确认信息**，单向发送不需要。

#### 1.1.3 消息消费者（Consumer）

负责消费消息，一般是后台系统负责异步消费。一个消息消费者会从Broker服务器拉取消息、并将其提供给应用程序。从用户应用的角度而言提供了两种消费形式：pull拉取式消费、push推送式消费。

#### 1.1.4 主题（Topic）

表示一类消息的集合，每个主题包含若干条消息，每条消息只能属于一个主题，是RocketMQ进行消息订阅的基本单位。

#### 1.1.5 代理服务器（Broker Server）

消息中转角色，负责存储消息、转发消息。代理服务器在RocketMQ系统中负责接收从生产者发送来的消息并存储、同时为消费者的拉取请求作准备。代理服务器也存储消息相关的元数据，包括消费者组、消费进度偏移和主题和队列消息等。

#### 1.1.6 名字服务（Name Server）

名称服务充当路由消息的提供者。生产者或消费者能够通过名字服务查找各主题相应的Broker IP列表。多个Namesrv实例组成集群，但相互独立，没有信息交换。

#### 1.1.7 拉取式消费（Pull Consumer）

Consumer消费的一种类型，应用通常主动调用Consumer的拉消息方法从Broker服务器拉消息、主动权由应用控制。一旦获取了批量消息，应用就会启动消费过程。

#### 1.1.8 推动式消费（Push Consumer）

Consumer消费的一种类型，该模式下Broker收到数据后会主动推送给消费端，该消费模式一般实时性较高。

#### 1.1.9 生产者组（Producer Group）

同一类Producer的集合，这类Producer发送同一类消息且发送逻辑一致。如果发送的是事务消息且原始生产者在发送之后崩溃，则Broker服务器会联系同一生产者组的其他生产者实例以提交或回溯消费。

#### 1.1.10 消费者组（Consumer Group）

同一类Consumer的集合，这类Consumer通常消费同一类消息且消费逻辑一致。消费者组使得在消息消费方面，实现负载均衡和容错的目标变得非常容易。要注意的是，消费者组的消费者实例必须订阅完全相同的Topic。RocketMQ 支持两种消息模式：集群消费（Clustering）和广播消费（Broadcasting）。

#### 1.1.11 集群消费（Clustering）

集群消费模式下,相同Consumer Group的每个Consumer实例平均分摊消息。

#### 1.1.12 广播消费（Broadcasting）

广播消费模式下，相同Consumer Group的每个Consumer实例都接收全量的消息。

#### 1.1.13 普通顺序消息（Normal Ordered Message）

普通顺序消费模式下，消费者通过同一个消费队列收到的消息是有顺序的，不同消息队列收到的消息则可能是无顺序的。

#### 1.1.14 严格顺序消息（Strictly Ordered Message）

严格顺序消息模式下，消费者收到的所有消息均是有顺序的。

#### 1.1.15 消息（Message）

消息系统所传输信息的物理载体，生产和消费数据的最小单位，每条消息必须属于一个主题。RocketMQ中每个消息拥有唯一的Message ID，且可以携带具有业务标识的Key。系统提供了通过Message ID和Key查询消息的功能。

#### 1.1.16 标签（Tag）

为消息设置的标志，用于同一主题下区分不同类型的消息。来自同一业务单元的消息，可以根据不同业务目的在同一主题下设置不同标签。标签能够有效地保持代码的清晰度和连贯性，并优化RocketMQ提供的查询系统。消费者可以根据Tag实现对**不同子主题**的不同消费逻辑，实现更好的扩展性。

#### 1.1.17Offset

在Topic的消费过程中，由于消息需要被不同的组进行多次消费，所以消费完的消息并不会立即被删除，这就需要RocketMQ为每个消费组在每个队列上维护一个消费位置（Consumer Offset），这个位置之前的消息都被消费过，之后的消息都没有被消费过，每成功消费一条消息，消费位置就加一。

也可以这么说，`Queue` 是一个长度无限的数组，**Offset** 就是下标。

### 四大核心组成部分

**NameServer**、**Broker**、**Producer**以及**Consumer**

![](https://note.youdao.com/yws/api/personal/file/E1BF99AF14FF4A6B87123D862C8F02C2?method=download&shareKey=96f002f407d1f1cbaef14750359e7023)

![](https://note.youdao.com/yws/api/personal/file/327AF097EDED422C913DF7E4F0541939?method=download&shareKey=fc0ec68ccd804c2b6f2c35501cabd1af)

* 生产者（Producer）：负责产生消息，生产者向消息服务器发送由业务应用程序系统生成的消息。
* 消费者（Consumer）：负责消费消息，消费者从消息服务器拉取信息并将其输入用户应用程序。支持分布式集群方式部署。支持以push推，pull拉两种模式对消息进行消费。同时也支持集群方式和广播方式的消费，它提供实时消息订阅机制。
* 消息服务器（Broker）：是消息存储中心，主要作用是接收来自 Producer 的消息并存储， Consumer 从这里取得消息，负责接收从生产者发送来的消息并存储、同时为消费者的拉取请求作准备。**一个 Topic 分布在多个 Broker上，一个 Broker 可以配置多个 Topic ，它们是多对多的关系。主题中存在多个队列。**
* 名称服务器（NameServer）：用来保存 Broker 相关 Topic 等元信息并给 Producer ，提供 Consumer 查找 Broker 信息。NameSever就是一个注册中心 ，主要提供两个功能：**Broker管理** 和 **路由信息管理** 。说白了就是Broker会将自己的信息注册到NameServer中，此时NameServer就存放了很多Broker的信息(Broker的路由表)，消费者和生产者就从NameServer中获取路由表然后照着路由表的信息和对应的Broker进行通信(生产者和消费者定期会向NameServer去查询相关的Broker的信息)。

### 集群

![](https://note.youdao.com/yws/api/personal/file/C8128354EB3F4988BC7CF94408954E08?method=download&shareKey=621105b39dc8fdba88ba6670f51f199e)

第一、我们的 `Broker` **做了集群并且还进行了主从部署** ，由于消息分布在各个 `Broker` 上，一旦某个 `Broker` 宕机，则该`Broker` 上的消息读写都会受到影响。所以 `Rocketmq` 提供了 `master/slave` 的结构，` salve` 定时从 `master` 同步数据(同步刷盘或者异步刷盘)，如果 `master` 宕机，**则 slave 提供消费服务，但是不能写入消息** 。

第二、为了保证 `HA` ，我们的 `NameServer` 也做了集群部署，但是请注意它是 **去中心化** 的。也就意味着它没有主节点，你可以很明显地看出 `NameServer` 的所有节点是没有进行 `Info Replicate` 的，在 `RocketMQ` 中是通过 **单个Broker和所有NameServer保持长连接** ，并且在每隔30秒 `Broker` 会向所有 `Nameserver` 发送心跳，心跳包含了自身的 `Topic` 配置信息，这个步骤就对应这上面的 `Routing Info` 。

第三、在生产者需要向 `Broker` 发送消息的时候，**需要先从 NameServer 获取关于 Broker 的路由信息**，然后通过 **轮询** 的方法去向每个队列中生产数据以达到 **负载均衡** 的效果。

第四、消费者通过 `NameServer` 获取所有 `Broker` 的路由信息后，向 `Broker` 发送 `Pull` 请求来获取消息数据。`Consumer` 可以以两种模式启动—— **广播（Broadcast）和集群（Cluster）**。广播模式下，一条消息会发送给 **同一个消费组中的所有消费者** ，集群模式下消息只会发送给一个消费者。

> https://segmentfault.com/a/1190000041346676?sort=votes

### 普通消息发送

#### 三种消息发送

这里只讨论普通的消息发送方式，区别于顺序、事务、延迟/定时等消息发送方式，当前分为三种：

- **同步（sync）：** 同步发送就是指 producer 发送消息后，会同步等待，在接收到 broker 响应结果后才继续发下一条消息。
- **异步（async）：** 异步发送是指 producer 发出一条消息后，不需要等待 broker 响应，就接着发送下一条消息的通信方式。异步发送同样可以对消息的响应结果进行处理，需要在发送消息时实现异步发送回调接口。
- **单方向（oneWay）：** 是一种单方向通信方式，也就是说 producer 只负责发送消息，不等待 broker 发回响应结果，而且也没有回调函数触发，这也就意味着 producer 只发送请求不等待响应结果。

**三种发送方式对比**

| 发送方式 | 发送TPS | 发送结果响应 | 可靠性 | 使用场景                      |
| -------- | ------- | ------------ | ------ | ----------------------------- |
| 同步     | 一般    | 有           | 高     | 重要的通知场景                |
| 异步     | 快      | 有           | 高     | 比较注重 RT（响应时间）的场景 |
| 单方向   | 最快    | 无           | 低     | 可靠性要求并不高的场景        |

**三种发送消息代码**

```java
    private String convertDestination(String topic, String tag) {
        return StringUtils.isBlank(tag) ? topic : StringUtils.join(topic, ":", tag);
    }

    /**
     * 同步发送
     */
    public SendResult syncSend(String topic, String tag, String content) {
        String destination = this.convertDestination(topic, tag);
        return rocketMQTemplate.syncSend(destination, content);
    }

    /**
     * 异步发送
     */
    public void asyncSend(String topic, String tag, String content, SendCallback sendCallback) {
        String destination = this.convertDestination(topic, tag);
        rocketMQTemplate.asyncSend(destination, content, sendCallback);
    }

    /**
     * 单向发送
     */
    public void sendOneWay(String topic, String tag, String content) {
        String destination = this.convertDestination(topic, tag);
        rocketMQTemplate.sendOneWay(destination, content);
    }
```

#### 批量发送

批量消息发送是将同一主题的多条消息一起打包发送到消息服务端，减少网络调用次数，提高网络传输效率。

```java
    /**
     * 同步-批量发送
     */
    public SendResult syncBatchSend(String topic, String tag, List<String> contentList) {
        String destination = this.convertDestination(topic, tag);
        List<Message<String>> messageList = contentList.stream()
                .map(content -> MessageBuilder.withPayload(content).build())
                .collect(Collectors.toList());
        return rocketMQTemplate.syncSend(destination, messageList);
    }
```

#### 标签tag

rocketmq中，topic与tag都是业务上用来归类的标识，区分在于topic是一级分类，而tag可以理解为是二级分类。定义上：

- **topic：** 消息主题，通过Topic对不同的业务消息进行分类。
- **tag：** 消息标签，用来进一步区分某个Topic下的消息分类，消息从生产者发出即带上的属性。













### 实例

nacos配置文件

```yaml
spring:
  cloud:
    stream:
      rocketmq:
        binder:
          # nameServer地址
          name-server: 192.168.33.7:9866
      bindings:
      # 生产者output相关配置，这是一个通道名称
        pushChaContractOut:
          # 消息的目标Topic
          destination: push_chacontract_topic
          # 生产者所属生产者组
          group: SELF_PUSH_CHACONTRACT_P_GROUP
          # 消息格式：这里采取json
          content-type: application/json
        messageMgtInPut:
          # 消息的目标Topic
          destination: queue_message_mgt_topic
          # 消费者所属消费者组
          group: QUEUE_MESSAGE_MGT_C_GROUP
          # 消息格式：这里采取json
          content-type: application/json
        toVoidChaContractOut:
          # 消息的目标Topic
          destination: to_void_chacontract_topic
          # 生产者所属生产者组
          group: SELF_TO_VOID_CHACONTRACT_P_GROUP
          # 消息格式：这里采取json
          content-type: application/json
```

生产者

业务类

```java
chacontractSendClient.PushChaContract(toCdcContractSendDto);
```

ChacontractSendClient.java

```java
@EnableBinding(ChacontractStreamOutput.class)
public class ChacontractSendClient {

    private static final Logger logger = LoggerFactory.getLogger(ChacontractSendClient.class);

    @Autowired
    private ChacontractStreamOutput chacontractStreamOutput;

    public void PushChaContract(ToCdcContractSendDto toCdcContractSendDto){
        logger.info("==========结算后发送PushChaContractmq " );
        MessageBuilder builder = MessageBuilder.withPayload(toCdcContractSendDto);
        boolean send = chacontractStreamOutput.pushChaContractOut().send(builder.build());
        if(!send){
            throw new MQSendErrorException();
        }
        logger.info("==========结算后发送PushChaContractmq到SAP结束 ============  " + send);
    }
}
```

ChacontractStreamOutput.java

```java
public interface ChacontractStreamOutput {
    // 合同推送
    String pushChaContractOut = "pushChaContractOut";
    @Output(ChacontractStreamOutput.pushChaContractOut)
    MessageChannel pushChaContractOut();
}
```

消费者：

StreamInput.java

```java
public interface StreamInput {
    // 合同推送
    String pushChaContractInput = "pushChaContractInput";
    @Input(StreamInput.pushChaContractInput)
    SubscribableChannel pushChaContractInput();
}
```

ReceiveClient.java

```java
@Component
@EnableBinding(StreamInput.class)
@Slf4j
public class ReceiveClient {

	@StreamListener(StreamInput.pushChaContractInput)
    public void pushChaContractInput(Message<?> message) throws Exception {
        log.info("pushChaContractInput接收消息："+message);
        ToCdcContractSendDto toCdcContractSendDto = JsonMapper.INSTANCE.fromJson(message.getPayload().toString(), ToCdcContractSendDto.class);
        cpsToCdcAS.sendContractPush(toCdcContractSendDto);
    }
    
}
```

业务类

```java
public void sendContractPush(ToCdcContractSendDto toCdcContractSendDto) throws InterruptedException {
    //业务代码
}
```



