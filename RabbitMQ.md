# RabbitMQ

## 一、基础概念

### 1.1 RabbitMQ是什么？

> 参考链接：[JavaGuide](https://javaguide.cn/high-performance/message-queue/rabbitmq-questions.html#rabbitmq-%E6%98%AF%E4%BB%80%E4%B9%88)

**RabbitMQ是一个在AMQP（Advanced Message Queuing Protocol）基础上实现的、可复用的企业消息系统，它可以用于大型软件系统各个模块之间的高效通信，支持高并发，支持可扩展**。它支持多种客户端如：Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP等，支持AJAX，持久化，用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面表现不俗。

RabbitMQ是使用Erlang编写的一个开源的消息队列，本身支持很多的协议：AMQP、XMPP、SMTP、STOMP，也正是如此，使得它变得非常重量级，更适合于企业级的开发。它同时实现了一个Broker构架，这意味着消息在发送给客户端时先在中心队列排队，对路由（Routing）、负载均衡（Load Balance）或者数据持久化都有很好的支持。

### 1.2 RabbitMQ特点

> 参考链接：[JavaGuide](https://javaguide.cn/high-performance/message-queue/rabbitmq-questions.html#rabbitmq-%E7%89%B9%E7%82%B9)

- **可靠性**：RabbitMQ使用一些机制来保证可靠性， 如持久化、传输确认及发布确认等。
- **灵活的路由**：在消息进入队列之前，通过交换器来路由消息。对于典型的路由功能， RabbitMQ己经提供了一些内置的交换器来实现。针对更复杂的路由功能，可以将多个 交换器绑定在一起， 也可以通过插件机制来实现自己的交换器。
- **扩展性**：多个RabbitMQ节点可以组成一个集群，也可以根据实际业务情况动态地扩展集群中节点。
- **高可用性**：队列可以在集群中的机器上设置镜像，使得在部分节点出现问题的情况下队列仍然可用。
- **多种协议**：RabbitMQ除了原生支持AMQP协议，还支持STOMP、MQTT等多种消息中间件协议。
- **多语言客户端**：RabbitMQ几乎支持所有常用语言。
- **管理界面**：RabbitMQ提供了一个易用的用户界面，使得用户可以监控和管理消息、集群中的节点等。
- **插件机制**：RabbitMQ提供了许多插件。

### 1.3 AMQP协议:airplane:

> 参考链接：[JavaGuide](https://javaguide.cn/high-performance/message-queue/rabbitmq-questions.html#amqp-%E6%98%AF%E4%BB%80%E4%B9%88)

RabbitMQ就是AMQP协议的Erlang实现。AMQP的模型架构和RabbitMQ的模型架构是一样的，生产者将消息发送给交换器，交换器和队列绑定。RabbitMQ中的交换器、交换器类型、队列、绑定、路由键等都遵循AMQP协议中相应的概念。

**AMQP协议的三层**：

- **Module Layer**：协议最高层，主要定义了一些客户端调用的命令，客户端可以用这些命令实现自己的业务逻辑。
- **Session Layer**：中间层，主要负责客户端命令发送给服务器，再将服务端应答返回客户端，提供可靠性同步机制和错误处理。
- **Transport Layer**：最底层，主要传输二进制数据流，提供帧的处理、信道服用、错误检测和数据表示等。

**AMQP模型的三大组件**：

- **交换器（Exchange）**：消息代理服务器中用于把消息路由到队列的组件。
- **队列（Queue）**：用来存储消息的数据结构，位于硬盘或内存中。
- **绑定（Binding）**：一套规则，告知交换器消息应该将消息投递给哪个队列。

### 1.4 生产者（Prudcer）和消费者（Consumer）

> 参考链接：[JavaGuide](https://javaguide.cn/high-performance/message-queue/rabbitmq-questions.html#%E8%AF%B4%E8%AF%B4%E7%94%9F%E4%BA%A7%E8%80%85-producer-%E5%92%8C%E6%B6%88%E8%B4%B9%E8%80%85-consumer)

- **生产者**：消息生产者，就是投递消息的一方。消息一般包含两个部分：消息体（`payload`）和标签（`Label`）。
- **消费者**：消费消息，也就是接收消息的一方。消费者连接到RabbitMQ服务器，并订阅队列。消费消息时只消费消息体，丢弃标签。

### 1.5 服务节点（Broker）、队列（Queue）和交换器（Exchange）:airplane:

> 参考链接：[JavaGuide](https://javaguide.cn/high-performance/message-queue/rabbitmq-questions.html#%E8%AF%B4%E8%AF%B4-broker-%E6%9C%8D%E5%8A%A1%E8%8A%82%E7%82%B9%E3%80%81queue-%E9%98%9F%E5%88%97%E3%80%81exchange-%E4%BA%A4%E6%8D%A2%E5%99%A8)

- **Broker**：可以看做RabbitMQ的服务节点。一般请下一个Broker可以看做一个RabbitMQ服务器。
- **Queue**：RabbitMQ的内部对象，用于存储消息。多个消费者可以订阅同一队列，这时队列中的消息会被平摊（轮询）给多个消费者进行处理。
- **Exchange**：生产者将消息发送到交换器，由交换器将消息路由到一个或者多个队列中。当路由不到时，或返回给生产者或直接丢弃。

### 1.6 死信队列、延迟队列和优先级队列:airplane:

> 参考链接：[JavaGuide](https://javaguide.cn/high-performance/message-queue/rabbitmq-questions.html#%E4%BB%80%E4%B9%88%E6%98%AF%E6%AD%BB%E4%BF%A1%E9%98%9F%E5%88%97-%E5%A6%82%E4%BD%95%E5%AF%BC%E8%87%B4%E7%9A%84)

- **死信队列**：全称为Dead Letter Exchange（DLX）。当消息在一个队列中变成死信（Dead Message）之后，它能被重新被发送到另一个交换器中，这个交换器就是DLX，绑定DLX的队列就称之为死信队列。

  - **导致死信的几种情况**：
    - 消息被拒且`requeue = false`。
    - 消息TTL过期。
    - 队列满了，无法再添加。

- **延迟队列**：延迟队列指的是存储对应的延迟消息，消息被发送以后，并不想让消费者立刻拿到消息，而是等待特定时间后，消费者才能拿到这个消息进行消费。RabbitMQ本身是没有延迟队列的，要实现延迟消息，一般有两种方式：

  - 通过RabbitMQ本身队列的特性来实现，需要使用RabbitMQ的死信交换机和消息的存活时间TTL。
  - 在RabbitMQ 3.5.7及以上的版本提供了一个插件（rabbitmq-delayed-message-exchange）来实现延迟队列功能。

  也就是说，**AMQP协议以及RabbitMQ本身没有直接支持延迟队列的功能，但是可以通过TTL和DLX模拟出延迟队列的功能**。

- **优先级队列**：RabbitMQ 3.5.0有优先级队列实现，优先级高的队列会先被消费。可以通过`x-max-priority`参数来实现优先级队列。**不过，当消费速度大于生产速度且Broker没有堆积的情况下，优先级显得没有意义**。

### 1.7 工作模式:airplane:

> 参考链接：[掘金](https://juejin.cn/post/6844903926408413197#heading-1)

- 简单模式（Simple）：生产者将消息放入队列，**消费者监听消息队列**。如果队列中有消息就消费，消息被拿走后就自动从队列中删除。
- 工作模式（Work）：生产者将消息放入队列，**消费者可以有多个并同时监听同一个队列，共同争抢当前的消息队列内容，谁先拿到谁负责消费消息**。
- 订阅模式（Pub/Sub）：每个消费者监听自己订阅的队列，**生产者将消息发给Broker，由交换机将消息转发到绑定此交换机的每个队列**，每个绑定交换机的队列都将接收到消息。
- 路由模式（Routing）：生产者将消息发送给交换机，按照路由规则将消息任务扔到对应的队列中。
- 主题模式（Topic）：在路由模式上添加了模糊匹配。

### 1.8 运转流程:airplane:

> 参考：/Reference/《RabbitMQ实战指南》

#### 生产者发送消息

1. 连接到RabbitMQ Broker，**建立一个连接Connection，开启一个信道Channel**；
2. **声明一个交换器**，并设置相关属性，比如：交换器类型、是否持久化等；
3. **声明一个队列**，并设置相关属性，比如：是否排他、是否持久化、是否自动删除等；
4. 通过**路由键将交换器和队列绑定**起来；
5. **发送消息到Broker**，其中包含：路由键、交换器等信息；
6. 相应交换器**根据接收到的路由键查找匹配的队列**；
7. 如果找到，将消息存入相应的队列中；
8. 如果没有找到，根据生产者配置的属性，选择将消息丢弃还是回退给生产者；
9. 关闭信道；
10. 关闭连接。

#### 消费者接收消息

1. 连接到RabbitMQ Broker，**建立一个连接Connection，开启一个信道Channel**；
2. **向Broker请求消费相应队列中的消息**，可能会设置相应的回调函数，以及做一些准备工作；
3. **等待Broker回应并投递相应队列中的消息**，消费者接收消息；
4. 消费者**确认（`ack`）接收到的消息**；
5. RabbitMQ从队列中**删除相应已经被确认的消息**；
6. 关闭信道；
7. 关闭连接。

## 二、常见问题:airplane:

### 2.1 为什么不使用HTTP协议？

> 参考链接：[LearnKU](https://learnku.com/articles/57461)、/Reference/加油.pdf/RabbitMQ/常见问题

因为HTTP协议相对而言太过**复杂**，有Cookie、数据加密等一系列信息，传输效率比较低，并且大部分情况下HTTP协议都是**短连接**，无法保证数据的完整，也不具有持久化的功能。所以RabbitMQ就制定了自己的一套协议，即AMQP协议。

### 2.2 为什么需要信道？为什么不是TCP直接通信？

> 参考链接：[墨天轮](https://www.modb.pro/db/397668)、/Reference/加油.pdf/RabbitMQ/常见问题

- TCP建立连接和断开连接的开销较大
- 一条TCP连接可以容纳无限的信道，不容易造成性能瓶颈

### 2.3 重复消费原因及解决

> 参考链接：/Reference/加油.pdf/RabbitMQ/常见问题

重复消费原因：正常情况下，消费者在消费消息完毕后会发送一个确认消息给消息队列，消息队列就知道该消息被消费了，就会将该消息删除。但是因为网络传输等故障，确认信息没有传送到消息队列，导致消息队列不知道该消息已经被消费过了，**再次将消息分发给其他的消费者**，造成重复消费。

针对以上问题，一个解决思路是：保证消息消费的**幂等性**。比如： 在业务逻辑中，给写入消息队列的数据做唯一标识（UUID等），生产者将消息的唯一标识和消息一起发送给消息队列；消费者在接收到消息后将消息的唯一标识作为`key`执行`setnx`命令，如果执行成功就表示没有处理过这条消息，可以进行消费；反之则表示消息已经被消费了。

### 2.4 如何保证消息的可靠传输

> 参考链接：[博客园](https://www.cnblogs.com/jojop/p/14101960.html)、/Reference/加油.pdf/RabbitMQ/常见问题

消息丢失可能是：生产者丢失消息、消息队列丢失消息、消费者丢失消息。

#### 生产者丢失消息

##### 事务机制

使用RabbitMQ的事务功能，在生产者发送数据之前开启事务`channel.txSelect`，然后发送消息，如果消息没有成功被消息队列接收到，那么生产者会收到异常报错，此时就可以回滚事务`channel.txRollback`，然后重试发送消息；如果收到了消息，那么可以提交事务`channel.txCommit`。

事务机制可以基本确保生产者投递消息成功，但是这种方式有比较大的缺点，因为事务机制是同步的，会降低吞吐量。

##### `Confirm`模式

将信道设置成`confirm`模式（`channel.selectConfirm()`），则所有在信道上发送的消息都会被指派一个唯一的`ID`（从1开始递增），如果成功写入则回传ACK消息；如果不成功回传NACK，可以选择重试。

##### 比较

事务机制和`confirm`机制最大的不同在于：事务机制是同步的，会造成阻塞；而`confirm`机制是异步的，发送完一个消息之后就可以发送下一个消息。

#### 消息队列丢失消息

开启RabbitMQ的持久化，就是消息写入之后会持久化到磁盘。设置持久化有两个步骤：

1. **创建队列的时候将其设置为持久化**：`durable`设置为`true`。
2. **发送消息时将消息的`deliveryMode`设置为2**。

同时持久化也可以跟生产者`confirm`机制配合起来，只有消息被持久化到磁盘后，才会回传`ACK`。

#### 消费者丢失消息

消费者成功接收到消息，但是还未将消息处理完毕就宕机了。针对这种情况，可以利用RabbitMQ提供的**消息确认机制**，`autoAck`参数：

- 当`autoAck=true`时，RabbitMQ会自动把发送出去的消息置为确认，然后从内存（或者磁盘）中删除，**而不管消费者是否真正地消费到了这些消息**。

- 当`autoAck=false`时，RabbitMQ会等待消费者显式回复确认信号后才从内存（或者磁盘）中移去消息（实质上是先打上删除标记，之后再删除）。

所以对于消费者可能发生宕机的情况，可以将`autoAck`参数置为`false`，消费者就有足够的时间处理这条消息，不用担心处理消息过程中消费者进程挂掉后消息丢失的问题。
