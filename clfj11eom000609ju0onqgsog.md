---
title: "Apache Kafka fundamentals and practice"
datePublished: Wed Mar 22 2023 01:49:43 GMT+0000 (Coordinated Universal Time)
cuid: clfj11eom000609ju0onqgsog
slug: apache-kafka-fundamentals
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ukzHlkoz1IE/upload/22484029d22229361fd944f3256b6ce9.jpeg
tags: java, kafka, springboot, 2articles1week

---

Initially developed by Linkedin, Kafka is a distributed message system that supports partition and replica based on zookeeper coordination. Its most prominent feature is that it can process large amounts of data in real-time to meet various requirements and scenarios: For example, batch processing system based on Hadoop, the real-time system with low latency, Storm/Spark streaming engine, web/nginx log, access log, message service, etc., written in scala language, Linkedin was donated to the Apache Foundation in 2010 and became a top open source project.

### What is Kafka used for?

* log collection: A company can use Kafka to collect logs of various services. Kafka provides log records to various consumers through unified interface services, such as Hadoop, Hbase, and Solr.
    
* Message systems: decoupling and producer and consumer, caching messages, etc.
    
* User activity tracking: Kafka is often used to record the various activities of web users or app users, such as browsing, searching, clicking, etc Various servers publish the activity information to Kafka topics, which are then subscribed to by subscribers for real-time monitoring and analysis, or loaded into Hadoop, a data warehouse for offline analysis and mining.
    
* Operational Metrics: Kafka is also used to record operational monitoring data. This includes collecting data for various distributed applications and producing centralized feedback for various operations, such as alarms and reports.
    

### Basic ideas of Kafka

Kafka is a distributed, partitioned messaging (officially called commit log) service. It provides functionality that a messaging system should have, but does have a unique design. Kafka, so to speak, borrows ideas from the JMS(Java Message Service) specification, but does not follow it exactly.

Now, let's take look at the basic definition in Kafka:

* Broker: A Kafka node is a Broker. One or more brokers can form a Kafka cluster.
    
* Topic: Kafka categorizes messages by topic, and each message published to a Kafka cluster needs to specify a topic.
    
* Producer: A message producer, a client that sends messages to the Broker
    
* Consumer: Message consumer, the client that reads messages from the Broker
    
* ConsumerGroup: Each Consumer belongs to a specific consumer group, and a purchase can be consumed by multiple different Consumer groups. But only one Consumer in a Consumer Group can consume the message
    
* Partition: A physical concept. A topic can be divided into multiple partitions, and the internal messages of each partition are ordered
    

Therefore, at a high level, the producer sends messages over the network to the Kafka cluster, which is then consumed by consumers. The communication between the server (brokers) and the client (producer, consumer) is completed through the TCP protocol.

### How to use Kafka?

1. Install JDK
    

We need to install JDK first because Kafka is developed by Scala which is running on JVM.

```bash
yum install java -1.8.0 -openjdk* -y
```

1. Install Zookeeper
    

Kafka also relies on Zookeeper, so we need to install zookeeper as well.

```bash
wget https://archive.apache.org/dist/zookeeper/zookeeper ‐3.5.8/apache ‐zookeeper ‐3.5.8 ‐bin.tar.gz
tar ‐zxvf apache ‐zookeeper ‐3.5.8 ‐bin.tar.gz
cd apache ‐zookeeper ‐3.5.8 ‐bin
cp conf/zoo_sample.cfg conf/zoo.cfg

# start zookeeper
bin/zkServer.sh start
```

1. Download and Unzip kafka
    

```bash
wget https://archive.apache.org/dist/kafka/2.4.1/kafka_2.11 ‐2.4.1.tgz # 2.11 is scala version，2.4.1 is kafka version
tar ‐xzf kafka_2.11 ‐2.4.1.tgz
cd kafka_2.11 ‐2.4.1
```

1. Modify the config file (config/server.properties):
    

```bash
#broker.id property must be unique in the kafka cluster
broker.id=0
#kafka部署的机器ip和提供服务的端口号
listeners=PLAINTEXT://192.168.65.60:9092
#kafka的消息存储文件
log.dir=/usr/local/data/kafka ‐logs
#kafka连接zookeeper的地址
zookeeper.connect=192.168.65.60:2181
```

1. Start the Kafka service
    

```bash
kafka­server­start.sh [­daemon] server.properties
```

As you can see, the configuration path of [server.properties](http://server.properties) is a mandatory parameter. -daemon indicates that the back-end process is running, otherwise, the service will stop after the ssh client exits. (Note that the IP address associated with the Linux hostname is used when starting Kafka, so you need to configure the mapping between the hostname and the Linux IP address to the local host, using vim /etc/hosts.)

```bash
# Start kafka and logs in the server.log file in the logs directory
bin/kafka-server-start.sh -daemon config/server.properties   
#startup in daemon mode, does not print logs to the console
# or
bin/kafka-server-start.sh config/server.properties &

# Go to the zookeeper directory and view the zookeeper directory tree on the # zookeeper client
bin/zkCli.sh 
ls /		
# Check the kafka node of the zk root directory
ls /brokers/ids	
# View the kafka node

# stop kafka
bin/kafka-server-stop.sh
```

[**server.properties**](http://server.properties) **Config In Details**

<table><tbody><tr><td colspan="1" rowspan="1"><p><strong>Property</strong></p></td><td colspan="1" rowspan="1"><p><strong>Default</strong></p></td><td colspan="1" rowspan="1"><p><strong>Description</strong></p></td></tr><tr><td colspan="1" rowspan="1"><p><a target="_blank" rel="noopener noreferrer nofollow" href="http://broker.id" style="pointer-events: none">broker.id</a></p></td><td colspan="1" rowspan="1"><p>0</p></td><td colspan="1" rowspan="1"><p>Each broker can be identified by a unique non-negative integer id; This id can be used as the "name" of the broker, and you can choose any number you like as long as the id is unique.</p></td></tr><tr><td colspan="1" rowspan="1"><p>log.dirs</p></td><td colspan="1" rowspan="1"><p>/tmp/kafka-logs</p></td><td colspan="1" rowspan="1"><p>The path where Kafka stores data. This path is not unique; it can be multiple paths separated by commas. Each time a new partition is created, the path with the fewest partitions is selected.</p></td></tr><tr><td colspan="1" rowspan="1"><p>listeners</p></td><td colspan="1" rowspan="1"><p>PLAINTEXT://192.168.65.60:9092</p></td><td colspan="1" rowspan="1"><p>The server accepts the port that the client connects to. The IP address is set to the local IP address of the Kafka</p></td></tr><tr><td colspan="1" rowspan="1"><p>zookeeper.connect</p></td><td colspan="1" rowspan="1"><p><a target="_blank" rel="noopener noreferrer nofollow" href="http://localhost:2181" style="pointer-events: none">localhost:2181</a></p></td><td colspan="1" rowspan="1"><p>The zooKeeper connection string is in the format of hostname: port, where hostname and port are the host and port of a node in the ZooKeeper cluster. If zookeeper is a cluster, the connection modes are hostname1:port1, hostname2:port2, and hostname3:port3</p></td></tr><tr><td colspan="1" rowspan="1"><p>log.retention.hours</p></td><td colspan="1" rowspan="1"><p>168</p></td><td colspan="1" rowspan="1"><p>The amount of time each log file is saved before it is deleted. The default data retention time is the same for all topics.</p></td></tr><tr><td colspan="1" rowspan="1"><p>num.partitions</p></td><td colspan="1" rowspan="1"><p>1</p></td><td colspan="1" rowspan="1"><p>Create the default number of partitions for the topic</p></td></tr><tr><td colspan="1" rowspan="1"><p>default.replication.factor</p></td><td colspan="1" rowspan="1"><p>1</p></td><td colspan="1" rowspan="1"><p>The default number of replicas for the topic is automatically created. You are advised to set it to 2 or greater</p></td></tr><tr><td colspan="1" rowspan="1"><p>min.insync.replicas</p></td><td colspan="1" rowspan="1"><p>1</p></td><td colspan="1" rowspan="1"><p>When the producer sets acks to -1, min.insync.replicas specify the minimum number of replicas (each replica write must be successful). If this number is not reached, the producer sends an exception</p></td></tr><tr><td colspan="1" rowspan="1"><p>delete.topic.enable</p></td><td colspan="1" rowspan="1"><p>false</p></td><td colspan="1" rowspan="1"><p>Whether a topic can be deleted</p></td></tr></tbody></table>

1. Create a topic
    
    Now let's create a topic named "test" with only one partition and a backup factor of 1:
    
    ```bash
    bin/kafka-topics.sh --create --zookeeper 192.168.65.60:2181 --replication-factor 1 --partitions 1 --topic test
    ```
    
    Then we can view the current topic in Kafka with the following command:
    
    ```bash
    bin/kafka-topics.sh --list --zookeeper 192.168.65.60:2181
    ```
    
    Instead of creating a Topic manually, when the producer posts a message to a given topic, the Topic is created automatically if it does not exist.
    
    Delete topic
    
    ```bash
    bin/kafka-topics.sh --delete --topic test --zookeeper 192.168.65.60:2181
    ```
    
2. Send message
    
    Kafka comes with a producer command client that can read content from local files, or input content directly from the command line and send it as a message to the Kafka cluster. By default, each row is treated as a separate message. First, we run the script to publish the message, then enter the contents of the message to be sent into the command:
    
    ```bash
    bin/kafka-console-producer.sh --broker-list 192.168.65.60:9092 --topic test 
    this is a msg
    this is a another msg
    ```
    
3. Consume message
    
    For consumers, Kafka also carries a command line client that outputs the content in a command, consuming the latest message by default:
    
    ```bash
    bin/kafka-console-consumer.sh --bootstrap-server 192.168.65.60:9092 --topic test
    ```
    
    If you want to consume previous messages, you can specify the --from-beginning parameter as follows:
    
    ```bash
    bin/kafka-console-consumer.sh --bootstrap-server 192.168.65.60:9092 --from-beginning --topic test 
    ```
    
    If you run the above command through a different terminal window, you will see that the output from the producer terminal will soon be displayed in the consumer terminal window. All of the above commands have some additional options; When we run the command with no arguments, the detailed usage of the command will be shown.
    
    Consume multiple topics:
    
    ```bash
    bin/kafka-console-consumer.sh --bootstrap-server 192.168.65.60:9092 --whitelist "test|test-2"
    ```
    
    One message can be consumed by multiple Consumers:
    
    Similar to the publish-subscribe fee, the same message in Kafka can only be consumed by a single consumer in the same consumer group. To implement multicast, ensure that these consumers belong to different consumer groups. We add another consumer that belongs to the testGroup-2 consumer group, resulting in both clients receiving messages
    
    ```bash
    bin/kafka-console-consumer.sh --bootstrap-server 192.168.65.60:9092 --consumer-property group.id=testGroup-2 --topic test 
    ```
    
    Check consumer group name:
    
    ```bash
    bin/kafka-consumer-groups.sh --bootstrap-server 192.168.65.60:9092 --list 
    ```
    
    Check consumer group offset:
    
    ```bash
    bin/kafka-consumer-groups.sh --bootstrap-server 192.168.65.60:9092 --describe --group testGroup
    ```
    
    Current-offset: indicates the consumed offset of the current consumption group log-end-offset: end offset (HW) of the partition message corresponding to the topic lag: The number of unconsumed messages in the current consumer group