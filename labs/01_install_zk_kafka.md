## Install Kafka

### 1. Download kafka
Go to kafka downloads `https://kafka.apache.org/downloads` and choose `Kafka 2.0.0` and `scala 2.12` and download the binary

```shell
## create necessary directories
cd ~
sudo apt-get update -y
sudo apt-get install -y tree
mkdir kafka
cd kafka
```

```shell
## download kafka binary
wget https://archive.apache.org/dist/kafka/2.0.0/kafka_2.12-2.0.0.tgz

## untar
tar -xvf kafka_2.12-2.0.0.tgz

## remove tar file
rm kafka_2.12-2.0.0.tgz

## change directory
cd /home/ubuntu/kafka/kafka_2.12-2.0.0

## verify
bin/kafka-topics.sh

### probably this will give java error => exec: java: not found
```

### 2. Download Java and set the classpath

```shell
## install java
sudo apt install -y openjdk-8-jdk

## verify java
which java
java -version

## change directory
cd /home/ubuntu/kafka/kafka_2.12-2.0.0

## verify
bin/kafka-topics.sh
### should show couple of kafka topic commands
```

### 3. Add kafka bin to path

```shell
## add "kafka bin" to "bash profile"
vi ~/.bashrc
## add this line to the end
export PATH=$PATH:/home/ubuntu/kafka/kafka_2.12-2.0.0/bin
## save and quit

vi ~/.bash_profile
## add this line to the end
export PATH=$PATH:/home/ubuntu/kafka/kafka_2.12-2.0.0/bin
## save and quit

## source the two files
source ~/.bashrc
source ~/.bash_profile

## echo $PATH to check
echo $PATH

## verify kafka from anywhere
cd ~
kafka-topics.sh
```


### 3. Start Zookeeper
Configure and start zookeeper

```shell
cd /home/ubuntu/kafka/kafka_2.12-2.0.0

## create a directory to hold zookeeper data
mkdir -p data/zookeeper

## change the zookeeper configuration to use the above directory
vi config/zookeeper.properties
### Replace "dataDir=/tmp/zookeeper" with "dataDir=/home/ubuntu/kafka/kafka_2.12-2.0.0/data/zookeeper"
## save and quit

## Start zookeeper in background mode, otherwise you will have to keep the terminal always open

nohup zookeeper-server-start.sh -daemon config/zookeeper.properties > /dev/null 2>&1 &

### This should run zookeeper service at 0.0.0.0:2181. If something is already using this port, it will give error.
### this should be running all time

## verify :- multiple ways
echo stat | nc localhost 2181
ls -al /home/ubuntu/kafka/kafka_2.12-2.0.0/data/zookeeper
jps | grep Quorum
sudo netstat -nlpt | grep ':2181'
```

### 4. Start Kafka
configure and start kafka

```shell
cd /home/ubuntu/kafka/kafka_2.12-2.0.0

## create a directory to hold kafka data
mkdir -p data/kafka

## change the kafka configuration to use the above directory
vi config/server.properties
### Replace "log.dirs=/tmp/kafka-logs" with "log.dirs=/home/ubuntu/kafka/kafka_2.12-2.0.0/data/kafka"
### Add to the end of file "delete.topic.enable=true"
### verify "zookeeper.connect=localhost:2181"
### 
## save and quit

## Start kafka in background mode, otherwise you will have to keep the terminal always open

nohup kafka-server-start.sh -daemon config/server.properties > /dev/null 2>&1 &

### This should run kafka service. KafkaServer id=0; on port 9092
### this should be running all time

## verify :- multiple ways
echo dump | nc localhost 2181 | grep brokers
ls -al /home/ubuntu/kafka/kafka_2.12-2.0.0/data/kafka
jps | grep -i kafka
sudo netstat -nlpt | grep ':9092'
```

### 5. Basic Test
Let's check command list and create a simple topic

```shell
## will give the list of commands
kafka-topics.sh

## create a topic - error
kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic first_topic --create --partitions 2 --replication-factor 2
 ----> this will give error: Error while executing topic command : Replication factor: 2 larger than available brokers: 1.
 ----> since we have one broker, we can only have 1 replica of each partition.

## create a topic - executes
kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic first_topic --create --partitions 2 --replication-factor 1

## list and describe topics
kafka-topics.sh --zookeeper 127.0.0.1:2181 --list
kafka-topics.sh --zookeeper 127.0.0.1:2181 --describe --topic first_topic
```

### Restarting Kafka (Troubleshooting)
<b>DO THIS ONLY IF YOU WANT TO RESTART KAFKA</b>

Shutdown Kafka and restart

```
"If you get an error that kafka is running currently you can kill the process."

ps -fA | grep ./bin/kafka-server-start.sh

sudo kill -9 <process-id>

"Failed to acquire lock on file .lock in kafka directory. A Kafka instance in another process or thread is using this directory"

sudo rm /home/ubuntu/kafka/kafka_2.12-2.0.0/data/kafka/.lock
```
