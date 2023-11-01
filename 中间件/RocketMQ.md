# Rocket的使用

## 1架构图

![https://image.itbaima.net/images/173/image-20230928156874955.png](https://image.itbaima.net/images/173/image-20230928156874955.png)

Broker:代理;消息代理;   MQ服务器，有两个，主和从

dashboard:仪表板

slave:奴隶;

![https://image.itbaima.net/images/173/image-20230928154347857.png](https://image.itbaima.net/images/173/image-20230928154347857.png)

## 2 安装与启动RocketMQ

1.安装JDK 1.8+,配置%JAVA_HOME%环境变量，注意：%JAVA_HOME%中不要出现空格

2.访问https://rocketmq.apache.org/zh/download，下载RocketMQ 5.x最新版Binary压缩包

3.解压缩，配置环境变量%ROCKETMQ_HOME%，指向解压缩目录

4.使用cmd命令窗口执行"%ROCKETMQ_HOME%/bin/mqnamesrv.cmd",启动NameServer进程，默认使用9876端口

5.在%ROCKETMQ_HOME%/conf目录下新建master.properties，增加Broker运行配置文件

代码如下：

~~~properties
#集群名称，同一个集群下的broker要求统一
brokerClusterName=DefaultCluster

#broker名称,唯一标识
brokerName=broker-a

#brokerId=0代表主节点，大于零代表从节点
brokerId=0

#删除日志文件时间点，默认凌晨 4 点
deleteWhen=04

#日志文件保留时间，默认 48 小时
fileReservedTime=48

#Broker 的角色
#- ASYNC_MASTER 异步复制Master
#- SYNC_MASTER 同步双写Master
brokerRole=SYNC_MASTER

#刷盘方式(数据写入磁盘)
#- ASYNC_FLUSH 异步刷盘，性能好宕机会丢数据(Broker还没有完成把消息写入磁盘的工作就给生产者返回ack确认的消息)
#- SYNC_FLUSH 同步刷盘，性能较差不会丢数据(Broker完成了把消息写入磁盘的工作后才给生产者返回ack确认的消息)
flushDiskType=SYNC_FLUSH

#末尾追加，NameServer节点列表，使用分号分割  配置NameServer运行的ip与端口，节点启动时向这个位置进行注册
namesrvAddr=localhost:9876

autoCreateTopicEnable=true
~~~



6.

在%ROCKETMQ_HOME%/bin目录下面执行：表示以master.properties作为配置文件执行mqbroker.cmd脚本

~~~ssh
mqbroker.cmd -c ../conf/master.properties
~~~

或者

- 执行命令：‘start mqbroker.cmd -n 127.0.0.1:9876 autoCreateTopicEnable=true’

> 注意：假如弹出提示框提示‘错误: 找不到或无法加载主类 xxxxxx’。打开runbroker.cmd，然后将‘%CLASSPATH%’加上英文双引号。



![失败](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/9/16a9d12b765ad735~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)

打开 `runbroker.cmd` 进行修改 原：



```
set "JAVA_OPT=%JAVA_OPT% -cp %CLASSPATH%"
```

修改后：

```
set "JAVA_OPT=%JAVA_OPT% -cp "%CLASSPATH%""
```



![修改后](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/9/16a9d12b9ba9203c~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)



7.运行测试工具tools.cmd验证接入过程

首先执行：建立一个会话级别的环境变量

```
SET NAMESRV_ADDR=localhost:9876
```

然后执行：模拟一个生产者向MQ发送数据

```
tools.cmd org.apache.rocketmq.example.quickstart.Producer
```

最后执行：模拟一个消费者消费MQ的数据

```
tools.cmd org.apache.rocketmq.example.quickstart.Consumer
```

## 3 SpringBoot整合RocketMQ

流程：

![https://image.itbaima.net/images/173/image-20230930106295723.png](https://image.itbaima.net/images/173/image-20230930106295723.png)

配置配置文件：设置com.imooc包下的日志输出级别为debug

![https://image.itbaima.net/images/173/image-2023093011545954.jpeg](https://image.itbaima.net/images/173/image-2023093011545954.jpeg)

~~~yaml
spring:
	application:
		name: cpp
server:
	port: 8000
logging:
	level:
		root: info
		com.imooc: debug
~~~



依赖：版本要与本地下载的RocketMQ保持一致

~~~xml
<dependency>
<groupId>org.apache.rocketmq</groupId>
<artifactId>rocketmq-client</artifactId>
<version>5.0.0</version>
</dependency>
~~~

 ### 3.1 实例化MQProducer对象

写在CPPApplication.class中(最好新建一个配置类)，在程序启动时运行下面的代码把bean注入到ioc容器中:

~~~java
@Bean
public DefaultMQProducer producer (){
    //创建一个生产者并且放在pg1生产者组中
    DefaultMQProducer producer = new DefaultMQProducer("pg1");
    producer.setNamesrvAddr ("localhost:9876");
    try{
        //
        producer.start();
        log.debug("Producer己启动成功")；
    } catch (MQClientException e){
    	throw new RuntimeException(e);
    }
    return producer;
}
~~~

### 3.2 构建Message对象并且发送

~~~java
@Resource
private DefaultMQProducer producer;

public void publish(Chapter chapter){
    log.debug("Chapter已处理完毕，准备推送至Broker");
    //序列化对象，序列化为容易传输的JSON对象
    ObjectMapper mapper = new ObjectMapper();
    //设置对对象中为null的属性不进行序列化
    mapper.setSerializationInclusion (JsonInclude.Include.NON NULL);
    try{
        ChapterWrapper chapterWrapper = new ChapterWrapper(chapter);
        //把chapterWrapper包装类处理后的chapter对象序列化为JSON字符串并且接收
        String portalData = mapper.writeValueAsString(chapterWrapper.getObject("PORTAL"));
        //构建Message对象,参数分别为：主题、标签、要传输JSON字符串的字节数组(二进制数据)
        Message portalMessage = new Message ("PORTAL","",portalData.getBytes());
        //发送信息并接收发送结果
        SendResult portalResult  = producer.send(portalMessage);
        log.debug("PORTAL主题消息己发送：MsgId:",+portalResult.getMsgId()+",发送状态："+portalResult.getSentResult());
        }catch (Exception e){
            1og.error("MQ消息异常"，e);
            throw new RuntimeException(e);
        }
}
~~~

消费消息:

流程：

![https://image.itbaima.net/images/173/image-20230930111003134.jpeg](https://image.itbaima.net/images/173/image-20230930111003134.jpeg)



### 3.3 实例化MQConsumer对象并进行消费

~~~java
@Bean
public MQConsumer mqComsumer(){
    //推送模式：push表示以推送的方式通知消费者消费对象(实时性好，Borker一旦收到消息就会进行推送)  "cg-portal"表示分组名称
    DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("cg-portal")
    //拉取模式：pull表示消费者根据定时任务主动获取消息来进行消费
    //DefaultMQPullConsumer consumer = new DefaultMQPullConsumer("cg-portal")
    try{
        //序列化对象，把对象序列化为容易传输的JSON字符串或者把JSON字符串还原为对象
    	ObjectMapper mapper = new ObjectMapper();
        consumer.setNamesrvAddr("localhost:9876");
        //设置订阅PORTAL主题的消息，*表示接收所有的消息不进行过滤
        consumer.subscribe("PORTAL","*")
        //默认为集群模式（同一个组下的消费者实例负载均衡的消费消息）
        consumer.setMessageModel(MessageModel..CLUSTERING);
        
        //设置广播模式:（同一个组下的每个消费者实例都接收所有的消费消息）
		//consumer.setMessageModel(MessageModel..BROADCASTING);
            
        //设置消费者如何消费 有消息到来时自动触发consumeMessage方法
        consumer.registerMessageListener(new MessageListenerConcurrently(){
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,ConsumeConcurrentlyContext context){
                    try{
                        //进行消费
                        for (MessageExt msg:list){
                            //msg.getBody()即消息的二进制数据,格式为JSON字符串
                            log.debug("接收到新章节数据："+msg.getMsgId()+"==>"+new String(msg.getBody()));
                            //把JSON字符串的二进制数据还原为Chapter对象
                            Chapter chapter = mapper.readvalue(msg.getBody(),Chapter.class);
                            log.debug(chapter);
                        }
                        //告诉Broker消息己被接收确认,对应的消息可以删除了
                        return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
                    }catch(Exception e){
                        1og.error("接收章节异常"，e);
                        //告诉Broker延后一段时间再发送一次信息，再次尝试进行消费
                        return ConsumeConcurrentlyStatus.RECONSUME_LATER;
                    }
            }
        });
        //启动消费者，与Broker建立长连接
        consumer.start();
        log.debug("Comsumer启动成功");
    }catch((Exception e){
        throw new Runtime(Exception(e);
    }
    return comsumer;
}
~~~



## 4 RocketMQ原理

![https://image.itbaima.net/images/173/image-2023093015115380.png](https://image.itbaima.net/images/173/image-2023093015115380.png)
