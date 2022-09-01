---
title: kafka生产者与消费者
description: kafka生产者与消费者
slug: kafka生产者与消费者
date: 2022-08-31 11:22:00+0000
categories:
    - 消息队列
tags:
    - Kafka
---

# kafka生产者与消费者

## 1 实用的脚本工具

Kafka提供了许多实用的脚本工具，存放在$KAFKA_HOME的bin目录下

- kafka-topics.sh

  ```shell
  kafka-topics.sh --zookeeper localhost:2181/kafka --create --topic topic-demo --replication-factor 3 --partitions 4
  ```

  创建一个分区数为4，副本因子为3的主题 topic-demo

- kafka-console-consumer.sh

  ```shell
  kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topic-demo
  ```

  创建一个订阅主题topic-demo的消费者

- kafka-console-producer.sh

  ```shell
  kafka-console-producer.sh --broker-list localhost:9092 --topic topic-demo
  ```

  创建一个发布主题为topic-demo的生产者

## 2 Java客户端

```xml
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>2.0.0</version>
</dependency>
```

## 3 生产客户端

一个正常的生产逻辑需要具备以下几个步骤：

- 配置生产者客户端参数及创建相应的生产者实例。

- 构建待发送的消息。

- 发送消息。

- 关闭生产者实例

要往Kafka中写入消息，首先要创建一个生产者客户端实例并设置一些配置参数，然后构建消息的ProducerRecord对象，其中必须包含所要发往的主题及消息的消息体，进而再通过生产者客户端实例将消息发出，最后可以通过 close（）方法来关闭生产者客户端实例并回收相应的资源。

这里有必要单独说明的是构建的消息对象ProducerRecord，它并不是单纯意义上的消息，它包含了多个属性，原本需要发送的与业务相关的消息体只是其中的一个 value 属性，比如“Hello，Kafka！”只是ProducerRecord对象中的一个属性。ProducerRecord类的定义如下：

- topic 主题

- partition 分区号

- headers 消息头部

- key 键

  同一个key的消息会被划分到同一个分区中

- value 值

  value是指消息体，一般不为空，如果为空则表示特定的消息—墓碑消息

- timestamp 消息的时间戳

### 3.1  生产者实例的必要参数

- bootstrap.servers：该参数用来指定生产者客户端连接Kafka集群所需的broker地址清单，具体的内容格式为host1：port1，host2：port2，可以设置一个或多个地址，中间以逗号隔开，此参数的默认值为“”。注意这里并非需要所有的broker地址，因为生产者会从给定的broker里查找到其他broker的信息。不过建议至少要设置两个以上的broker 地址信息，当其中任意一个宕机时，生产者仍然可以连接到 Kafka集群上。
- key.serializer 和 value.serializer：broker 端接收的消息必须以字节数组（byte[]）的形式存在。代码清单2-1中生产者使用的KafkaProducer＜String，String＞和ProducerRecord＜String，String＞中的泛型＜String，String＞对应的就是消息中key和value的类型，生产者客户端使用这种方式可以让代码具有良好的可读性，不过在发往broker之前需要将消息中对应的key和value做相应的序列化操作来转换成字节数组。key.serializer和value.serializer这两个参数分别用来指定key和value序列化操作的序列化器，这两个参数无默认值。注意这里必须填写序列化器的全限定名
- client.id：这个参数用来设定KafkaProducer对应的客户端id，默认值为“”。如果客户端不设置，则KafkaProducer会自动生成一个非空字符串，内容形式如“producer-1”“producer-2”，即字符串“producer-”与数字的拼接。

一般情况下，普通开发人员无法记住所有的参数名称，只能有个大致的印象。在实际使用过程中，诸如“key.serializer”“max.request.size”“interceptor.classes”之类的字符串经常由于人为因素而书写错误。为此，我们可以直接使用客户端中的org.apache.kafka.clients.producer.ProducerConfig类来做一定程度上的预防措施，每个参数在ProducerConfig 类中都有对应的名称

### 3.2 消息的发送

在创建完生产者实例之后，接下来的工作就是构建消息，即创建ProducerRecord对象。

针对不同的消息，需要构建不同的ProducerRecord对象，在实际应用中创建ProducerRecord对象是一个非常频繁的动作。

发送消息主要有三种模式：

- 发后即忘（fire-and-forget）

  ```
  producer.send(record)
  ```

- 同步（sync）

  ```
  producer.send(record).get()
  ```

- 异步（async）

  ```
  producer.send(record, new Callback(){})
  ```

KafkaProducer 的 send（）方法并非是 void 类型，而是 Future＜RecordMetadata＞类型。

消息发送完成后需要调用close，关闭生产者。

### 3.3 序列化

生产者需要用序列化器（Serializer）把对象转换成字节数组才能通过网络发送给Kafka。而在对侧，消费者需要用反序列化器（Deserializer）把从 Kafka 中收到的字节数组转换成相应的对象。

org.apache.kafka.common.serialization.Serializer接口，此接口有3个方法：

- public void configure(Map<String, ?> configs, boolean isKey)
- public byte[] serialize(String topic, T data)
- public void close()

如果 Kafka 客户端提供的几种序列化器都无法满足应用需求，则可以选择使用如 Avro、JSON、Thrift、ProtoBuf和Protostuff等通用的序列化工具来实现，或者使用自定义类型的序列化器来实现。

### 3.4 分区器

消息在通过send（）方法发往broker的过程中，有可能需要经过拦截器（Interceptor）、序列化器（Serializer）和分区器（Partitioner）的一系列作用之后才能被真正地发往 broker。拦截器一般不是必需的，而序列化器是必需的。消息经过序列化之后就需要确定它发往的分区，如果消息ProducerRecord中指定了partition字段，那么就不需要分区器的作用，因为partition代表的就是所要发往的分区号。

如果消息ProducerRecord中没有指定partition字段，那么就需要依赖分区器，根据key这个字段来计算partition的值。分区器的作用就是为消息分配分区。

Kafka中提供的默认分区器是org.apache.kafka.clients.producer.internals.DefaultPartitioner，它实现了org.apache.kafka.clients.producer.Partitioner接口，这个接口中定义了2个方法，具体如下所示。

- public int partition(String topic, Object key, byte[] keyBytes, Object value, byte[] valueBytes, Cluster cluster)
- public void close()

其中partition（）方法用来计算分区号，返回值为int类型。partition（）方法中的参数分别表示主题、键、序列化后的键、值、序列化后的值，以及集群的元数据信息，通过这些信息可以实现功能丰富的分区器。close（）方法在关闭分区器的时候用来回收一些资源。

Partitioner 接口还有一个父接口org.apache.kafka.common.Configurable，这个接口中只有一个方法,Configurable接口中的configure（）方法主要用来获取配置信息及初始化数据。

### 3.5 拦截器

## 4 重要的生产者参数

### 4.1 acks

这个参数用来指定分区中必须要有多少个副本收到这条消息，之后生产者才会认为这条消息是成功写入的。acks 是生产者客户端中一个非常重要的参数，它涉及消息的可靠性和吞吐量之间的权衡。acks参数有3种类型的值（都是字符串类型）。

acks=1。默认值即为1。生产者发送消息之后，只要分区的leader副本成功写入消息，那么它就会收到来自服务端的成功响应。

acks=0。生产者发送消息之后不需要等待任何服务端的响应。如果在消息从发送到写入Kafka的过程中出现某些异常，导致Kafka并没有收到这条消息，那么生产者也无从得知，消息也就丢失了。在其他配置环境相同的情况下，acks 设置为 0 可以达到最大的吞吐量。

acks=-1或acks=all。生产者在消息发送之后，需要等待ISR中的所有副本都成功写入消息之后才能够收到来自服务端的成功响应。在其他配置环境相同的情况下，acks 设置为-1（all）可以达到最强的可靠性。但这并不意味着消息就一定可靠，因为ISR中可能只有leader副本，这样就退化成了acks=1的情况。要获得更高的消息可靠性需要配合 min.insync.replicas 等参数的联动，

### 4.2 max.request.size

这个参数用来限制生产者客户端能发送的消息的最大值，默认值为 1048576B，即 1MB。一般情况下，这个默认值就可以满足大多数的应用场景了。笔者并不建议读者盲目地增大这个参数的配置值，尤其是在对Kafka整体脉络没有足够把控的时候。因为这个参数还涉及一些其他参数的联动，比如broker端的message.max.bytes参数，如果配置错误可能会引起一些不必要的异常。

### 4.3 retries和retry.backoff.ms

retries参数用来配置生产者重试的次数，默认值为0，即在发生异常的时候不进行任何重试动作。消息在从生产者发出到成功写入服务器之前可能发生一些临时性的异常，比如网络抖动、leader副本的选举等，这种异常往往是可以自行恢复的，生产者可以通过配置retries大于0的值，以此通过内部重试来恢复而不是一味地将异常抛给生产者的应用程序。

retry.backoff.ms，这个参数的默认值为100，它用来设定两次重试之间的时间间隔，避免无效的频繁重试。

### 4.4 compression.type

这个参数用来指定消息的压缩方式，默认值为“none”，即默认情况下，消息不会被压缩。该参数还可以配置为“gzip”“snappy”和“lz4”。对消息进行压缩可以极大地减少网络传输量、降低网络I/O，从而提高整体的性能。消息压缩是一种使用时间换空间的优化方式，如果对时延有一定的要求，则不推荐对消息进行压缩。

### 4.5 connections.max.idle.ms

这个参数用来指定在多久之后关闭限制的连接，默认值是540000（ms），即9分钟。

### 4.6 linger.ms

这个参数用来指定生产者发送 ProducerBatch 之前等待更多消息（ProducerRecord）加入ProducerBatch 的时间，默认值为 0。生产者客户端会在 ProducerBatch 被填满或等待时间超过linger.ms 值时发送出去。增大这个参数的值会增加消息的延迟，但是同时能提升一定的吞吐量。这个linger.ms参数与TCP协议中的Nagle算法有异曲同工之妙。

### 4.7 receive.buffer.bytes

这个参数用来设置Socket接收消息缓冲区（SO_RECBUF）的大小，默认值为32768（B），即32KB。如果设置为-1，则使用操作系统的默认值。如果Producer与Kafka处于不同的机房，则可以适地调大这个参数值。

### 4.8 send.buffer.bytes

这个参数用来设置Socket发送消息缓冲区（SO_SNDBUF）的大小，默认值为131072（B），即128KB。与receive.buffer.bytes参数一样，如果设置为-1，则使用操作系统的默认值。

### 4.9 request.timeout.ms

这个参数用来配置Producer等待请求响应的最长时间，默认值为30000（ms）。请求超时之后可以选择进行重试。注意这个参数需要比broker端参数replica.lag.time.max.ms的值要大，这样可以减少因客户端重试而引起的消息重复的概率。

## 5 消费者与消费组

消费者（Consumer）负责订阅Kafka中的主题（Topic），并且从订阅的主题上拉取消息。与其他一些消息中间件不同的是：在Kafka的消费理念中还有一层消费组（Consumer Group）的概念，每个消费者都有一个对应的消费组。当消息发布到主题后，只会被投递给订阅它的每个消费组中的一个消费者。

某个主题中共有4个分区（Partition）：P0、P1、P2、P3。有两个消费组A和B都订阅了这个主题，消费组A中有4个消费者（C0、C1、C2和C3），消费组B中有2个消费者（C4和C5）。按照Kafka默认的规则，最后的分配结果是消费组A中的每一个消费者分配到1个分区，消费组B中的每一个消费者分配到2个分区，两个消费组之间互不影响。每个消费者只能消费所分配到的分区中的消息。换言之，每一个分区只能被一个消费组中的一个消费者所消费。

可以通过消费者客户端参数partition.assignment.strategy 来设置消费者与订阅主题之间的分区分配策略。

消费者与消费组这种模型可以让整体的消费能力具备横向伸缩性，我们可以增加（或减少）消费者的个数来提高（或降低）整体的消费能力。对于分区数固定的情况，一味地增加消费者并不会让消费能力一直得到提升，如果消费者过多，出现了消费者的个数大于分区个数的情况，就会有消费者分配不到任何分区。

## 6 消费者客户端

一个正常的消费逻辑需要具备以下几个步骤：

- 配置消费者客户端参数以及创建相应的消费者实例。
- 订阅主题。
- 拉取消息并消费。
- 提交消费位移。
- 关闭消费者实例。

必要的参数配置：

- bootstrap.servers：该参数的释义和生产者客户端 KafkaProducer 中的相同，用来 指 定 连 接Kafka 集 群 所 需 的 broker 地 址 清 单，具 体内 容 形 式 为host1：port1，host2：post，可以设置一个或多个地址，中间用逗号隔开，此参数的默认值为“”。
- group.id：消费者隶属的消费组的名称，默认值为“”。如果设置为空，则会报出异常
- key.deserializer 和 value.deserializer：与生产者客户端 KafkaProducer中的key.serializer和value.serializer参数对应。消费者从broker端获取的消息格式都是字节数组（byte[]）类型，所以需要执行相应的反序列化操作才能还原成原有的对象格式。这两个参数分别用来指定消息中key和value所需反序列化操作的反序列化器，这两个参数无默认值。注意这里必须填写反序列化器类的全限定名

### 6.1 订阅主题与分区

使用subscribe（）方法订阅主题

在 Kafka 中默认的消费位移的提交方式是自动提交，这个由消费者客户端参数enable.auto.commit 配置，默认值为 true。当然这个默认的自动提交不是每消费一条消息就提交一次，而是定期提交，这个定期的周期时间由客户端参数auto.commit.interval.ms配置，默认值为5秒，此参数生效的前提是enable.auto.commit参数为true

在默认的方式下，消费者每隔5秒会将拉取到的每个分区中最大的消息位移进行提交。自动位移提交的动作是在poll（）方法的逻辑里完成的，在每次真正向服务端发起拉取请求之前会检查是否可以进行位移提交，如果可以，那么就会提交上一次轮询的位移。

在Kafka消费的编程逻辑中位移提交是一大难点，自动提交消费位移的方式非常简便，它免去了复杂的位移提交逻辑，让编码更简洁。但随之而来的是重复消费和消息丢失的问题。假设刚刚提交完一次消费位移，然后拉取一批消息进行消费，在下一次自动提交消费位移之前，消费者崩溃了，那么又得从上一次位移提交的地方重新开始消费，这样便发生了重复消费的现象（对于再均衡的情况同样适用）。我们可以通过减小位移提交的时间间隔来减小重复消息的窗口大小，但这样并不能避免重复消费的发送，而且也会使位移提交更加频繁。
