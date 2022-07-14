## SpringCloud Stream

**屏蔽底层消息中间件的差异，统一消息的编程模型**

![](https://note.youdao.com/yws/api/personal/file/85A154F0C2654C33BBFD03E05F964E74?method=download&shareKey=1f17e417ccb0315ae034736e420fdb3b)

```
Application Core:生产者/消费者
inputs:消费者
outputs:生产者
binder:绑定器，主要和消息中间件之间进行绑定操作
Middleware:消息中间件服务
```

通过Stream可以很好的屏蔽各个中间件的API差异，它统一了API，生产者通过OUTPUT向消息中间件发送消息，此时并不需要关心消息中间件是Kafka还是RabbitMQ，不需要关注他们的API，只需要用到Stream的API，这样可以降低学习成本。消费方通过INPUT消费指定的消息，也不需要关注消息中间件的API，架构图如上图。

#### 工作流程

![](https://note.youdao.com/yws/api/personal/file/D7603727C7A54BC0AB0FFAE1DDF95F30?method=download&shareKey=6a99491686daab9dfb4c3fbfa611cb43)

```
生产者：
	1:使用Source绑定消息输出通道。
	2:通过MessageChannel输出消息。
	3:通过@EnableBinding开启Binder，将生产者绑定到指定MQ服务。

消费者：
	1:通过@EnableBinding绑定到指定MQ。
	2:通过Sink绑定输出数据通道。
	3:@StreamListener监听指定通道数据。
```

| 组成            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| Middleware      | 中间件                                                       |
| Binder          | Binder是应用与消息中间件之间的封装，通过Binder可以很方便的连接中间件，可以动态的改变消息类型 |
| @Input          | 注解标识输入通道，通过该输入通道接收到的消息进入应用程序     |
| @Output         | 注解标识输出通道，发布的消息将该通道离开应用程序             |
| @StreamListener | 监听队列，用于消费者的队列的消息接收                         |
| @EnableBinding  | 指信道channel和topic绑定在一起                               |

### 实例

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

