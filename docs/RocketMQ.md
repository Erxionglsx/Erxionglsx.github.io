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

#### 消息模型（Message Model）

RocketMQ主要由 Producer、Broker、Consumer 三部分组成，其中Producer 负责生产消息，Consumer 负责消费消息，Broker 负责存储消息。**Broker 在实际部署过程中对应一台服务器**，每个 Broker 可以存储多个Topic的消息，每个Topic的消息也可以分片存储于不同的 Broker。Message Queue 用于存储消息的物理地址，每个Topic中的消息地址存储于多个 Message Queue 中。ConsumerGroup 由多个Consumer 实例构成。

#### 消息生产者（Producer）

负责生产消息，一般由业务系统负责生产消息。一个消息生产者会把业务应用系统里产生的消息发送到broker服务器。RocketMQ提供多种发送方式，同步发送、异步发送、顺序发送、单向发送。**同步和异步方式均需要Broker返回确认信息**，单向发送不需要。

#### 消息消费者（Consumer）

负责消费消息，一般是后台系统负责异步消费。一个消息消费者会从Broker服务器拉取消息、并将其提供给应用程序。从用户应用的角度而言提供了两种消费形式：pull拉取式消费、push推送式消费。

- **Pull**：拉取型消费者（Pull Consumer）主动从消息服务器拉取信息，只要批量拉取到消息，用户应用就会启动消费过程，所以 Pull 称为主动消费型。
- **Push**：推送型消费者（Push Consumer）封装了消息的拉取、消费进度和其他的内部维护工作，将消息到达时执行的回调接口留给用户应用程序来实现。所以 Push 称为被动消费类型，但从实现上看还是从消息服务器中拉取消息，不同于 Pull 的是 Push 首先要注册消费监听器，当监听器处触发后才开始消费消息。

#### 主题（Topic）

表示一类消息的集合，每个主题包含若干条消息，每条消息只能属于一个主题，是RocketMQ进行消息订阅的基本单位。

#### 代理服务器（Broker Server）

消息中转角色，负责存储消息、转发消息。代理服务器在RocketMQ系统中负责接收从生产者发送来的消息并存储、同时为消费者的拉取请求作准备。代理服务器也存储消息相关的元数据，包括消费者组、消费进度偏移和主题和队列消息等。

#### 名字服务（Name Server）

名称服务充当路由消息的提供者。生产者或消费者能够通过名字服务查找各主题相应的Broker IP列表。多个Namesrv实例组成集群，但相互独立，没有信息交换。

#### 拉取式消费（Pull Consumer）

Consumer消费的一种类型，应用通常主动调用Consumer的拉消息方法从Broker服务器拉消息、主动权由应用控制。一旦获取了批量消息，应用就会启动消费过程。

#### 推动式消费（Push Consumer）

Consumer消费的一种类型，该模式下Broker收到数据后会主动推送给消费端，该消费模式一般实时性较高。

#### 生产者组（Producer Group）

同一类Producer的集合，这类Producer发送同一类消息且发送逻辑一致。如果发送的是事务消息且原始生产者在发送之后崩溃，则Broker服务器会联系同一生产者组的其他生产者实例以提交或回溯消费。

#### 消费者组（Consumer Group）

同一类Consumer的集合，这类Consumer通常消费同一类消息且消费逻辑一致。消费者组使得在消息消费方面，实现负载均衡和容错的目标变得非常容易。要注意的是，消费者组的消费者实例必须订阅完全相同的Topic。RocketMQ 支持两种消息模式：集群消费（Clustering）和广播消费（Broadcasting）。

#### 集群消费（Clustering）

集群消费模式下,相同Consumer Group的每个Consumer实例平均分摊消息。

#### 广播消费（Broadcasting）

广播消费模式下，相同Consumer Group的每个Consumer实例都接收全量的消息。

#### 普通顺序消息（Normal Ordered Message）

普通顺序消费模式下，消费者通过同一个消费队列收到的消息是有顺序的，不同消息队列收到的消息则可能是无顺序的。

#### 严格顺序消息（Strictly Ordered Message）

严格顺序消息模式下，消费者收到的所有消息均是有顺序的。

#### 消息（Message）

消息系统所传输信息的物理载体，生产和消费数据的最小单位，每条消息必须属于一个主题。RocketMQ中每个消息拥有唯一的Message ID，且可以携带具有业务标识的Key。系统提供了通过Message ID和Key查询消息的功能。

#### 标签（Tag）

为消息设置的标志，用于同一主题下区分不同类型的消息。来自同一业务单元的消息，可以根据不同业务目的在同一主题下设置不同标签。标签能够有效地保持代码的清晰度和连贯性，并优化RocketMQ提供的查询系统。消费者可以根据Tag实现对**不同子主题**的不同消费逻辑，实现更好的扩展性。

#### Offset

在Topic的消费过程中，由于消息需要被不同的组进行多次消费，所以消费完的消息并不会立即被删除，这就需要RocketMQ为每个消费组在每个队列上维护一个消费位置（Consumer Offset），这个位置之前的消息都被消费过，之后的消息都没有被消费过，每成功消费一条消息，消费位置就加一。

也可以这么说，`Queue` 是一个长度无限的数组，**Offset** 就是下标。

### 四大核心组成部分

**NameServer**、**Broker**、**Producer**以及**Consumer**

![](https://note.youdao.com/yws/api/personal/file/E1BF99AF14FF4A6B87123D862C8F02C2?method=download&shareKey=96f002f407d1f1cbaef14750359e7023)

![](https://note.youdao.com/yws/api/personal/file/327AF097EDED422C913DF7E4F0541939?method=download&shareKey=fc0ec68ccd804c2b6f2c35501cabd1af)

#### 生产者（Producer）

负责产生消息，生产者向消息服务器发送由业务应用程序系统生成的消息。

#### 消费者（Consumer）

负责消费消息，消费者从消息服务器拉取信息并将其输入用户应用程序。支持分布式集群方式部署。支持以push推，pull拉两种模式对消息进行消费。同时也支持集群方式和广播方式的消费，它提供实时消息订阅机制。

#### 消息服务器（Broker）

是消息存储中心，负责**存储消息**，转发消息。负责接收从生产者发送来的消息并存储、同时为消费者的拉取请求作准备。单个Broker节点与所有的NameServer节点保持长连接及心跳，并会定时将**Topic**信息注册到NameServer。**一个 Topic 分布在多个 Broker上，一个 Broker 可以配置多个 Topic ，它们是多对多的关系。主题中存在多个队列。**

#### 名称服务器（NameServer）

用来保存 Broker 相关 Topic 等元信息并给 Producer ，提供 Consumer 查找 Broker 信息。NameSever就是一个注册中心 ，主要提供两个功能：**Broker管理** 和 **路由信息管理** 。说白了就是Broker会将自己的信息注册到NameServer中，此时NameServer就存放了很多Broker的信息(Broker的路由表)，消费者和生产者就从NameServer中获取路由表然后照着路由表的信息和对应的Broker进行通信(生产者和消费者定期会向NameServer去查询相关的Broker的信息)。

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

- **同步（sync）：** 同步发送就是指 producer 发送消息后，会同步等待，在接收到 broker 响应结果后才继续发下一条消息。一般用于重要通知消息，例如重要通知邮件、营销短信。
- **异步（async）：** 异步发送是指 producer 发出一条消息后，不需要等待 broker 响应，就接着发送下一条消息的通信方式。异步发送同样可以对消息的响应结果进行处理，需要在发送消息时实现异步发送回调接口。一般用于可能链路耗时较长而对响应时间敏感的业务场景，例如用户视频上传后通知启动转码服务。
- **单方向（oneWay）：** 是一种单方向通信方式，也就是说 producer 只负责发送消息，不等待 broker 发回响应结果，而且也没有回调函数触发，这也就意味着 producer 只发送请求不等待响应结果。适用于某些耗时非常短但对可靠性要求并不高的场景，例如日志收集。

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

### 标签tag

rocketmq中，topic与tag都是业务上用来归类的标识，区分在于topic是一级分类，而tag可以理解为是二级分类。定义上：

- **topic：** 消息主题，通过Topic对不同的业务消息进行分类。
- **tag：** 消息标签，用来进一步区分某个Topic下的消息分类，消息从生产者发出即带上的属性。

### 延迟/定时消息

定时消息是指消息发到broker后，不能立刻被consumer消费，要到特定的时间点或者等待特定的时间后才能被消费。

**原理**

其实定时消息实现原理比较简单，如果一个topic对应的消息在发送端被设置为定时消息，那么会将该消息先存放在topic为 `SCHEDULE_TOPIC_XXXX` 的消息队列中，并将原始消息的信息存放在commitLog文件中，由于topic为SCHEDULE_TOPIC_XXXX，所以该消息不会被立即消息，然后通过定时扫描的方式，将到达延迟时间的消息，转换为正确的消息，发送到相应的队列进行消费。

默认 Broker服务器端有18个定时级别，每一个级别分别对应不同的延迟时间：

| 延迟级别 | 延迟时间 |
| -------- | -------- |
| 1        | 1s       |
| 2        | 5s       |
| 3        | 10s      |
| 4        | 30s      |
| 5        | 1m       |
| 6        | 2m       |
| 7        | 3m       |
| 8        | 4m       |
| 9        | 5m       |
| 10       | 6m       |
| 11       | 7m       |
| 12       | 8m       |
| 13       | 9m       |
| 14       | 10m      |
| 15       | 20m      |
| 16       | 30m      |
| 17       | 1h       |
| 18       | 2h       |

```java
    /**
     * 同步延迟发送
     *
     * @param delayLevel 延时等级：现在RocketMq并不支持任意时间的延时，需要设置几个固定的延时等级，从1s到2h分别对应着等级 1 到 18
     *                   1s 5s 10s 30s 1m 2m 3m 4m 5m 6m 7m 8m 9m 10m 20m 30m 1h 2h
     */
    public SendResult syncSendDelay(String topic, String tag, String content, long timeout, int delayLevel) {
        String destination = this.convertDestination(topic, tag);
        Message message = MessageBuilder.withPayload(content).build();
        return rocketMQTemplate.syncSend(destination, message, timeout, delayLevel);
    }
```

### 顺序消息

顺序消息是一种对消息发送和消费顺序有严格要求的消息，对于一个指定的Topic，消息严格按照先进先出（FIFO）的原则进行消息发布和消费，即先发布的消息先消费，后发布的消息后消费。

RocketMQ目前只能保证同一个分区内的顺序消息，那么队列中的消息必然是同一个分区的，因此实现下列场景的方式有：

- **分区有序：** RocketMQ 支持同一个队列分区内的顺序消息。另外某个 Topic 下，所有消息根据 ShardingKey 进行分区，相同 ShardingKey 的消息必须被发送到同一个分区队列。因此只要保证消息按照同一 ShardingKey 发送即可，然后保证 Consumer 同一个队列单线程消费即可。
- **全局有序：** 当设置 Topic 下只有一个分区时，可以实现全局有序。

代码示例：

**生产者**

```java
    /**
     * 同步顺序发送
     *
     * @param hashKey 根据 hashKey 和 队列size() 取模，保证同一 hashKey 的消息发往同一个队列，以实现 同一hashKey下的消息 顺序发送
     *  因此 hashKey 建议取 业务上唯一标识符，如：订单号，只需保证同一订单号下的消息顺序发送
     */
    public SendResult syncSendOrderly(String topic, String tag, String content, String hashKey) {
        String destination = this.convertDestination(topic, tag);
        Message message = MessageBuilder.withPayload(content).build();
        return rocketMQTemplate.syncSendOrderly(destination, message, hashKey);
    }
```

**消费者**

ConsumeMode 有两种值：

针对顺序消息的消费，代码也很容易，主要是 `@RocketMQMessageListener` 注解，通过设置了`consumeMode = ConsumeMode.ORDERLY`，表示使用顺序消费。

- **CONCURRENTLY**：默认值，并发同时接收异步传递的消息。
- **ORDERLY**：顺序消费时开启，只开启一个线程，同一时间只有序接收一个队列的消息。

```java
@RocketMQMessageListener(topic = "xxx-topic",
        consumerGroup = "xxxGroup",
        consumeMode = ConsumeMode.ORDERLY)
public class OrderConsumer implements RocketMQListener<String> {
    @Override
    public void onMessage(String message) {
        ... ...
    }
}
```

### 事务消息

RocketMQ 的事务机制，只体现在生产者，保障的是生产者本地的事务执行、发消息，这两个事务达成一致性。至于消费者收到消息后的事务处理，并不在当前机制内。

**正常事务的流程**

1. 调用发送事务消息方法，正常发送消息。发送事务的方法名为 `syncSendInTransaction`。
2. mq服务器端成功接收到消息后，消息处于一个半接收的状态，并响应给生产者客户端。
3. 生产者收到服务器端成功接收的响应后，执行本地事务。本地事务写在 `executeLocalTransaction` 方法里面，返回结果为枚举 `RocketMQLocalTransactionState`，有：`COMMIT、ROLLBACK、UNKNOWN` 三种值。
4. 服务器端收到 `COMMIT` 状态后，会把消息下发给消费者
5. 服务器端收到 `ROLLBACK` 状态后，会删除掉当前半接收状态的消息，不再处理。
6. 服务器端收到 `UNKNOWN` 状态，或者服务器端超时未收到消息，或者生产者未响应状态，则将进行消息补偿机制。

**消息补偿机制**

1. 当事务消息出现 `UNKNOWN`、超时、未响应时，服务器会主动调用生产者本地的回查方法 `checkLocalTransaction`，查询本地事务执行情况，返回结果还是枚举值 `RocketMQLocalTransactionState`。
2. 服务器接收到返回结果的处理流程和前面的正常流程一样。
3. 如果依然是 `UNKNOWN`、超时、未响应，将继续重试。如果超过最大重试次数后，依然无果，则视为 `ROLLBACK`，删除当前消息。

事务消息相关的参数，基本在 `org.apache.rocketmq.common.BrokerConfig` 类中定义，例如以下几个常用属性的默认值：

- `transactionTimeOut = 6000L`：服务器未收到事务本地消息的超时时间为1分钟。
- `transactionCheckMax = 15`：消息补偿机制中的最大回查次数为15次。
- `transactionCheckInterval = 6000L`：消息补偿机制中每次回查的时间间隔为1分钟。

生产者发送事务消息代码示例：

```java
    /**
     * 事务发送
     */
    public TransactionSendResult syncSendInTransaction(String topic, String tag, String content) {
        String destination = this.convertDestination(topic, tag);
        String transactionId = UUID.randomUUID().toString();
        Message message = MessageBuilder.withPayload(content)
                .setHeader(RocketMQHeaders.TRANSACTION_ID, transactionId)
                .build();
        return rocketMQTemplate.sendMessageInTransaction(destination, message, content);
    }
```

### 重试队列、死信队列

- **生产者重发：** 在前面介绍三种发送消息方式时，针对同步、异步发送失败时，都会再重发，相应重发次数分别对应 `DefaultMQProducer` 类中属性值 `retryTimesWhenSendFailed`、`retryTimesWhenSendAsyncFailed` ，也可以在 properties 配置文件中自定义设置。
- **消费者重发：** 当消息已经进入 broker 后，消费者接收失败，broker 也会给消费者重发，以下衍生出本次的重试队列、死信队列。

**重试队列**

如果消费者端因为各种类型异常导致本次消费失败，为防止该消息丢失而需要将其重新回发给broker端保存，保存这种因为异常无法正常消费而回发给mq的消息队列称之为重试队列。

RocketMQ 会为每个消费组都设置一个 topic 名称为 `“%RETRY%+consumerGroup” `的重试队列（这里需要注意的是，这个Topic的重试队列是针对消费组，而不是针对每个Topic设置的）。

用于暂时保存因为各种异常而导致消费者端无法消费的消息。考虑到异常恢复起来需要一些时间，会为重试队列设置多个重试级别，每个重试级别都有与之对应的重新投递延时，重试次数越多投递延时就越大。RocketMQ 对于重试消息的处理是先保存至 topic 名称为`“SCHEDULE_TOPIC_XXXX”` 的延迟队列中，后台定时任务按照对应的时间进行Delay后重新保存至“%RETRY%+consumerGroup”的重试队列中。

**死信队列**

消息会被判定为 “死信” 并进入死信队列，通常满足以下条件之一：

* 消费失败次数超限：消息被消费者多次消费（默认 16 次）仍未成功（如消费者抛出异常、未返回消费成功状态）。
* 消息过期：消息在队列中超过预设的存活时间（messageDelayLevel）仍未被消费。
* 队列满额：消息队列达到最大长度，新消息无法入队（较少见，通常队列会动态扩容）。

由于有些原因导致消费者端长时间的无法正常消费从 broker 端 pull过来的业务消息，为了确保消息不会被无故的丢弃，那么超过配置的“最大重试消费次数”后就会移入到这个死信队列中。

在RocketMQ中，SubscriptionGroupConfig 配置常量默认地设置了两个参数，一个是retryQueueNums为1（重试队列数量为1个），另外一个是retryMaxTimes为16（最大重试消费的次数为16次）。Broker端通过校验判断，如果超过了最大重试消费次数则会将消息移至这里所说的死信队列。这里，RocketMQ会为每个消费组都设置一个 topic 命名为 `“%DLQ%+consumerGroup"` 的死信队列。但如果一个消费者组未产生死信消息，消息队列 RocketMQ 不会为其创建相应的死信队列的。

因为死信队列中的消息是无法被消费的，它也证实了一部分消息出现了意料之外的情况。因此一般在实际应用中，移入至死信队列的消息，需要人工干预处理。例如通过 console 查看是否有私信队列，当解决问题后，可在 console 上手动重发消息。

### Stream+RocketMQ实例

**Topic主题**

![](https://note.youdao.com/yws/api/personal/file/81381B5021D74E4EBEA714A7CF2EB24E?method=download&shareKey=8b8c4d10b151d9992f0d93dab6b81170)

![](https://note.youdao.com/yws/api/personal/file/04FE15BFF9C0448B91706ADC17382079?method=download&shareKey=9346c62ca82496d6c1f4db0e7dbcc8fb)

![](https://note.youdao.com/yws/api/personal/file/F4377187AD284274A43E097AE95A0D09?method=download&shareKey=da3f3a047985f2690f1662539674ba45)

**生产者**

生产者nacos配置文件

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
```

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


**消费者**

![](https://note.youdao.com/yws/api/personal/file/48C4F38891C04055A1D8835AA43A404A?method=download&shareKey=303c08c9c6359fc19273fb8bd79e9c4d)

![](https://note.youdao.com/yws/api/personal/file/8CF17E1A613E4A41A88BD5CCB1E8867D?method=download&shareKey=a45043e118d1421cf7ac3faaf4a81317)

![](https://note.youdao.com/yws/api/personal/file/66A001D892C24E0390D29EF47808C60D?method=download&shareKey=3ea8404c524b2612d373292cdbbcd6b9)

消费者nacos配置文件

```yaml
spring:
  cloud:
    stream:
      rocketmq:
        binder:
          # nameServer地址
          name-server: 192.168.33.7:9866
      bindings:
         #消费者input相关配置 合同推送
        pushChaContractInput:
          #消息的目标Topic
          destination: push_chacontract_topic
          #消费者所属消费者组
          group: SELF_PUSH_CHACONTRACT_C_GROUP
          #消息格式：这里采取json
          content-type: application/json
```


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

**消息**

![](https://note.youdao.com/yws/api/personal/file/0552F3C827A94E29B5D37EF2F93A07A9?method=download&shareKey=426857a0d1f09fb9664811190f81774c)

![](https://note.youdao.com/yws/api/personal/file/0481E2444031437C91ED8FAECE68079F?method=download&shareKey=c9553192c4b4aa69f57561cf8b8be992)





https://www.cnblogs.com/weifeng1463/p/12889300.html