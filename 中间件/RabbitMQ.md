# RabbitMQ的使用

## 1 基础知识:

### 1.1 RabbitMQ的设计架构:

需要介绍一下RabbitMQ的设计架构，这样我们就知道各个模块管理的是什么内容了：

![image-20220416103043845](https://image.itbaima.net/markdown/2023/03/08/j5kIgD9ZRQiGtd6.jpg)



- **生产者（Publisher）和消费者（Consumer）：**不用多说了吧。
- **Channel：**我们的客户端连接都会使用一个Channel，再通过Channel去访问到RabbitMQ服务器，注意通信协议不是http，而是amqp协议。
- **Exchange：**类似于交换机一样的存在，会根据我们的请求，转发给相应的消息队列，每个队列都可以绑定到Exchange上，这样Exchange就可以将数据转发给队列了，可以存在很多个，不同的Exchange类型可以用于实现不同消息的模式。
- **Queue：**消息队列本体，生产者所有的消息都存放在消息队列中，等待消费者取出。
- **Virtual Host：**有点类似于环境隔离，不同环境都可以单独配置一个Virtual Host，每个Virtual Host可以包含很多个Exchange和Queue，每个Virtual Host相互之间不影响。



### 1.2 交换机(exchange)

现在我们来看看交换机：

![image-20220419143338487](https://image.itbaima.net/markdown/2023/03/08/GDnFoizW86pC5l9.jpg)



交换机列表中自动为我们新增了刚刚创建好的虚拟主机相关的预设交换机，一共7个，这里我们首先介绍一下前面两个`direct`类型的交换机，一个是`（AMQP default）`还有一个是`amq.direct`，它们都是**直连模式的交换机**，我们来看看第一个：

![image-20220419143612318](https://image.itbaima.net/markdown/2023/03/08/lIpfxGjLPrOatE5.jpg)



**1.（AMQP default）交换机:**

第一个交换机（AMQP default）是所有虚拟主机都会自带的一个**默认交换机**，并且此交换机不可删除，此交换机默认绑定到所有的消息队列，如果是通过默认交换机发送消息，那么会根据消息的`routingKey`（之后我们发消息都会指定）决定发送给哪个同名的消息队列，同时也不能显示地将消息队列绑定或解绑到此交换机。

我们可以看到，上图中，当前交换机特性是**持久化**(durable=true)的，也就是说就算机器重启，那么此交换机也会保留，如果不是持久化，那么一旦重启就会消失。实际上我们在列表中看到`D`的字样，就表示此交换机是持久化的，包含一会我们要讲解的消息队列列表也是这样，所有自动生成的交换机都是持久化的。

**2. amq.direct交换机:**

我们接着来看第二个交换机，这个交换机是一个普通的直连交换机：

![image-20220419144200533](https://image.itbaima.net/markdown/2023/03/08/DnpENxIPgOUTSbM.jpg)



这个交换机和我们刚刚介绍的默认交换机类型一致，并且也是持久化的，但是我们可以看到它是具有绑定关系的，如果没有指定的消息队列绑定到此交换机上，那么这个交换机无法正常将信息存放到指定的消息队列中，也是根据`routingKey`寻找消息队列（但是可以自定义）

我们可以在下面直接操作，让某个队列绑定。

### 1.3 获取消息队列中的消息

我们可以直接在消息队列这边**获取消息队列中的消息**，找到下方的Get message选项：

![image-20220419145936160](https://image.itbaima.net/markdown/2023/03/08/emrY3SZ98CJRAOh.jpg)



可以看到有三个选择，首先第一个Ack Mode，这个是应答模式选择，一共有4个选项：

![image-20220419150053926](https://image.itbaima.net/markdown/2023/03/08/nrWPuoGRTp7F36e.jpg)



- Nack message requeue true：拒绝消息，也就是说不会将消息从消息队列取出，并且重新排队，一次可以拒绝多个消息。
- Ack message requeue false：确认应答，确认后消息会从消息队列中移除，一次可以确认多个消息。
- Reject message requeue true/false：也是拒绝此消息，但是可以指定是否重新排队。

这里我们使用默认的就可以了，这样只会查看消息是啥，但是不会取出，消息依然存在于消息队列中，第二个参数是编码格式，使用默认的就可以了，最后就是要生效的操作数量，选择1就行：

![image-20220419150712314](https://image.itbaima.net/markdown/2023/03/08/c6auDXoHFqZT9M2.jpg)



可以看到我们刚刚的消息已经成功读取到。