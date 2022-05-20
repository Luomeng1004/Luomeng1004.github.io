---
title: Kafka扫盲
date: 2022-05-20 16:42:56
tags: 后端技术
---

## Kafka



### 一、架构示意图

![img](https://cbbing-1253804295.cos.ap-shanghai.myqcloud.com/kekefund/kafka_docker01.png)



	一个典型的Kafka集群中包含若干Producer（可以是web前端FET，或者是服务器日志等），若干broker（Kafka支持水平扩展，一般broker数量越多，集群吞吐率越高），若干ConsumerGroup，以及一个Zookeeper集群。
	
	Kafka通过Zookeeper管理Kafka集群配置：选举Kafka broker的leader，以及在Consumer Group发生变化时进行rebalance，因为consumer消费kafka topic的partition的offsite信息是存在Zookeeper的。
	
	Producer使用push模式将消息发布到broker，Consumer使用pull模式从broker订阅并消费消息。



![img](https://cbbing-1253804295.cos.ap-shanghai.myqcloud.com/kekefund/kafka_docker02.png)

	一个典型的Cloud Kafka集群如上所示。其中的生产者Producer可能是网页活动产生的消息、或是服务日志等信息。生产者通过push模式将消息发布到Cloud Kafka的Broker集群，消费者通过pull模式从broker中消费消息。消费者Consumer被划分为若干个Consumer Group，此外，集群通过Zookeeper管理集群配置，进行leader选举，故障容错等。





### 二、kafka特点：

- 它是一个处理流式数据的”发布-订阅“消息系统。
- 实时高效处理流式数据：kafka每秒可以处理几十万条消息，它的延迟最低只有几毫秒，每个topic可以分多个partition, consumer group 对partition进行consume操作。
- 将数据安全存储在分布式集群。
- 它是运行在集群上的。
- 它将流式记录存储在topics中。
- 每个record由key, value和timestamp组成。



### 三、Docker搭建

参考：[https://github.com/wurstmeister/kafka-docker](https://github.com/wurstmeister/kafka-docker?spm=a2c4e.11153940.blogcont657849.14.52db6f5c8a3J7i)

docker-compose.yml如下：

```
version: '2'
services:
  zookeeper:
    image: wurstmeister/zookeeper
    volumes:
      - ./data:/data
    ports:
      - "2181:2181"
       
  kafka:
    image: wurstmeister/kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: 10.154.38.115
      KAFKA_MESSAGE_MAX_BYTES: 2000000
      KAFKA_CREATE_TOPICS: "Topic1:1:3,Topic2:1:1:compact"
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
    volumes:
      - ./kafka-logs:/kafka
      - /var/run/docker.sock:/var/run/docker.sock
 
  kafka-manager:
    image: sheepkiller/kafka-manager
    ports:
      - 9020:9000
    environment:
      ZK_HOSTS: zookeeper:2181
 
```

参数说明：

- KAFKA_ADVERTISED_HOST_NAME：Docker宿主机IP（如果你要配置多个brokers，就不能设置为 localhost 或 127.0.0.1）
- KAFKA_MESSAGE_MAX_BYTES：kafka(message.max.bytes) 会接收单个消息size的最大限制，默认值为1000000 , ≈1M
- KAFKA_CREATE_TOPICS：初始创建的topics，可以不设置
- 环境变量./kafka-logs为防止容器销毁时消息数据丢失。
- 容器kafka-manager为yahoo出可视化kafka WEB管理平台。

操作命令：

```
# 启动：
$ docker-compose up -d
 
# 增加更多Broker：
$ docker-compose scale kafka=3
 
# 合并：
$ docker-compose up --scale kafka=3
 
```



### 四、Kakfa使用

#### 1.Kafka管理节点

![img](https://cbbing-1253804295.cos.ap-shanghai.myqcloud.com/kekefund/kafka_docker03.png)



#### 2.主题

```
environment:
      KAFKA_CREATE_TOPICS: "Topic1:1:3,Topic2:1:1:compact"
```

Topic1有1个Partition和3个replicas， Topic2有2个Partition，1个replica和cleanup.policy为compact。

> Topic 1 will have 1 partition and 3 replicas, Topic 2 will have 1 partition, 1 replica and a cleanup.policy set to compact.



#### 3.读写验证

读写验证的方法有很多，这里我们用kafka容器自带的工具来验证，首先进入到kafka容器的交互模式：

```
docker exec -it kafka_kafka_1 /bin/bash
```

创建一个主题：

```
/opt/kafka/bin/kafka-topics.sh --create --zookeeper 192.168.31.84:2181 --replication-factor 1 --partitions 1 --topic my-test
```

查看刚创建的主题：

```
/opt/kafka/bin/kafka-topics.sh --list --zookeeper 192.168.31.84:2181
```

![img](https://cbbing-1253804295.cos.ap-shanghai.myqcloud.com/kekefund/kafka_docker04.png)

发送消息：

```
/opt/kafka/bin/kafka-console-producer.sh --broker-list 192.168.31.84:9092 --topic my-test
This is a message
This is another message
```

读取消息：

```
/opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server 192.168.31.84:9092 --topic my-test --from-beginning
```

### 五、使用场景

- 日志收集：一个公司可以用Kafka可以收集各种服务的log，通过kafka以统一接口服务的方式开放给各种consumer，例如hadoop、Hbase、Solr等。

![img](https://cbbing-1253804295.cos.ap-shanghai.myqcloud.com/kekefund/kafka_docker05.png)

- 消息系统：解耦和生产者和消费者、缓存消息等。

- 用户活动跟踪：Kafka经常被用来记录web用户或者app用户的各种活动，如浏览网页、搜索、点击等活动，这些活动信息被各个服务器发布到kafka的topic中，然后订阅者通过订阅这些topic来做实时的监控分析，或者装载到hadoop、数据仓库中做离线分析和挖掘。

- 运营指标：Kafka也经常用来记录运营监控数据。包括收集各种分布式应用的数据，生产各种操作的集中反馈，比如报警和报告。
- 流式处理：比如spark streaming和storm



### 六、Go语言示例

#### 1.生产者：

```go
func producerTest() {
	fmt.Printf("producer_test\n")
	config := sarama.NewConfig()
	config.Producer.RequiredAcks = sarama.WaitForAll
	config.Producer.Partitioner = sarama.NewRandomPartitioner
	config.Producer.Return.Successes = true
	config.Producer.Return.Errors = true
	config.Version = sarama.V0_11_0_2

	producer, err := sarama.NewAsyncProducer([]string{"localhost:9092"}, config)
	if err != nil {
		fmt.Printf("producer_test create producer error :%s\n", err.Error())
		return
	}

	defer producer.AsyncClose()

	// send message
	msg := &sarama.ProducerMessage{
		Topic: "kafka_go_test",
		Key:   sarama.StringEncoder("go_test"),
	}

	value := "this is message"
	for {
		fmt.Scanln(&value)
		msg.Value = sarama.ByteEncoder(value)
		fmt.Printf("input [%s]\n", value)

		// send to chain
		producer.Input() <- msg

		select {
		case suc := <-producer.Successes():
			fmt.Printf("offset: %d,  timestamp: %s", suc.Offset, suc.Timestamp.String() + "\n")
		case fail := <-producer.Errors():
			fmt.Printf("err: %s\n", fail.Err.Error())
		}
	}
}
```

#### 2.消费者

```go
func consumerTest() {
	fmt.Printf("consumer_test")

	config := sarama.NewConfig()
	config.Consumer.Return.Errors = true
	config.Version = sarama.V0_11_0_2

	// consumer
	consumer, err := sarama.NewConsumer([]string{"localhost:9092"}, config)
	if err != nil {
		fmt.Printf("consumer_test create consumer error %s\n", err.Error())
		return
	}

	defer consumer.Close()

	partitionConsumer, err := consumer.ConsumePartition("kafka_go_test", 0, sarama.OffsetOldest)
	if err != nil {
		fmt.Printf("try create partition_consumer error %s\n", err.Error())
		return
	}
	defer partitionConsumer.Close()

	for {
		select {
		case msg := <-partitionConsumer.Messages():
			fmt.Printf("msg offset: %d, partition: %d, timestamp: %s, value: %s\n",
				msg.Offset, msg.Partition, msg.Timestamp.String(), string(msg.Value))
		case err := <-partitionConsumer.Errors():
			fmt.Printf("err :%s\n", err.Error())
		}
	}

}
```

#### 3.元数据

```go
func metadata_test() {
    fmt.Printf("metadata test\n")

    config := sarama.NewConfig()
    config.Version = sarama.V0_11_0_2

    client, err := sarama.NewClient([]string{"localhost:9092"}, config)
    if err != nil {
        fmt.Printf("metadata_test try create client err :%s\n", err.Error())
        return
    }

    defer client.Close()

    // get topic set
    topics, err := client.Topics()
    if err != nil {
        fmt.Printf("try get topics err %s\n", err.Error())
        return
    }

    fmt.Printf("topics(%d):\n", len(topics))

    for _, topic := range topics {
        fmt.Println(topic)
    }

    // get broker set
    brokers := client.Brokers()
    fmt.Printf("broker set(%d):\n", len(brokers))
    for _, broker := range brokers {
        fmt.Printf("%s\n", broker.Addr())
    }
}
```
