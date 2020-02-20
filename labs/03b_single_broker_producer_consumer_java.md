# Kafka Java Demo



### 1. Download and build a maven project from github

```shell
## install git and maven
sudo apt-get update -y
sudo apt-get install -y git maven
cd ~

## clone the repo
git clone https://github.com/shekhar2010us/kafka_java.git
cd /home/ubuntu/kafka_java

## build the project
mvn clean install -U
```

### 2. Start a consumer in console and run ProducerDemo in maven

```shell
## check if you have a topic called "first_topic"
kafka-topics.sh --zookeeper localhost:2181 --list

#### run only if "first_topic" do not exist
kafka-topics.sh --zookeeper localhost:2181 --create --topic first_topic --partitions 2 --replication-factor 1

## run a console consumer
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic

## run the main class ProducerDemo
mvn exec:java -Dexec.mainClass="com.shekhar.kafka.basic.ProducerDemo"

#### result -- you will see the message "hello world basic" in the consumer
```


### 3. ProducerDemo with Callback
This has a callback function, with overriden method `onCompletion` that takes care of callback

```shell
## assuming the consumer is still running

## run the main class ProducerDemoWithCallback
mvn exec:java -Dexec.mainClass="com.shekhar.kafka.basic.ProducerDemoWithCallback"

#### result -- you will see 10 messages "hello world callback +i" in the consumer
#### result -- you will see logs something like
	Topic: first_topic
	Offset: xx
	Partition: xx
	Timestamp: xx
#### result -- you will see that messages are not in order -- ONLY IF your topic has multiple partitions
```


### 3. ProducerDemo with Keys
This has a (Key,value) in the message. The data is read from a file, and there are multiple messages with the same key - to test partitions.

```shell
## Kill your console consumer for this section. We will create two consumers under a consumer group, since we have 2 partitions for this topic.

## build the project
cd /home/ubuntu/kafka_java
mvn clean install -U

## start two consumers in a group
## open "two tabs" and run this in both
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --group my-app-1

## run the main class ProducerDemoWithCallback in a third tab
mvn exec:java -Dexec.mainClass="com.shekhar.kafka.basic.ProducerDemoKeys"

#### result -- data is distributed in two consumers and same key goes to same consumer in the group
```


### 4. ConsumerDemo
Implement "Consumer" in a java code.

```shell
## Kill all your console producers and consumers for this section. we do not need it.

## build the project
cd /home/ubuntu/kafka_java
mvn clean install -U

## run the main class ProducerDemoWithCallback in a third tab
mvn exec:java -Dexec.mainClass="com.shekhar.kafka.basic.ConsumerDemo"

#### result -- all data will be shown - since we reset the offset.. something like.

#### result -- all data from partition 0 is read first and then from partition 1

#### Note - if you run the code again, it will not show any message, since the consumer-group has already read all messages

#### Test - keep the main class running, open a new terminal and run the main class in the second terminal. Watch the logs in both consumers. You will see something like --

	[com.shekhar.kafka.basic.ConsumerDemo.main()] INFO org.apache.kafka.clients.consumer.internals.AbstractCoordinator - [Consumer clientId=consumer-1, groupId=my-app-2] Attempt to heartbeat failed since group is rebalancing
	[com.shekhar.kafka.basic.ConsumerDemo.main()] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-1, groupId=my-app-2] Revoking previously assigned partitions [first_topic-0, first_topic-1]
	[com.shekhar.kafka.basic.ConsumerDemo.main()] INFO org.apache.kafka.clients.consumer.internals.AbstractCoordinator - [Consumer clientId=consumer-1, groupId=my-app-2] (Re-)joining group
	[com.shekhar.kafka.basic.ConsumerDemo.main()] INFO org.apache.kafka.clients.consumer.internals.AbstractCoordinator - [Consumer clientId=consumer-1, groupId=my-app-2] Successfully joined group with generation 10
	[com.shekhar.kafka.basic.ConsumerDemo.main()] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-1, groupId=my-app-2] Setting newly assigned partitions [first_topic-1]

## Reason - This is because another consumer has joined the group, so partitions has been re-assigned to 2 consumers. 

## If you open a third terminal and run the main class of ProducerKeys, you will see that now messages are received in two consumers
```

