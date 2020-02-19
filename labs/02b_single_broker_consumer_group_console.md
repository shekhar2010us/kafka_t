# Console - Consumer groups


### Test Kafka Installation by Producing and Consuming Data

#### bash profile
	source ~/.bashrc


### Check Zookeeper and Kafka

    ## check zookeeper
    echo stat | nc localhost 2181
    
    ## check kafka
    echo dump | nc localhost 2181 | grep brokers


### Start if not started, Zookeeper and Kafka

    ## start zookeeper
    nohup zookeeper-server-start.sh -daemon config/zookeeper.properties > /dev/null 2>&1 &
    
    ## start kafka
    nohup kafka-server-start.sh -daemon config/server.properties > /dev/null 2>&1 &



### Create a consumer with group
	
	# we need three terminals for this section.
	
    # Terminal 1 :- create a topic and run the producer
    kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic KafkaEssentials-4
    
    kafka-console-producer.sh --broker-list localhost:9092 --topic KafkaEssentials-4
    
    # Terminal 2 :-  start consumer 1 but now with group
    kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic KafkaEssentials-4 --group my-app
    
    # Terminal 3 :-  start consumer 2 with the same group (in a different terminal)
    kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic KafkaEssentials-4 --group my-app 
    
    ## check the result:-  messages are divided in all consumers

### TASK:- Create a consumer with group

- Create a consumer group with 2 consumers. Start first consumer with `--from-beginning` parameter. And then start the second consumer
- Check the result

*One consumer will first get all message and second won't, because in a group messages are committed just once - to make sure that collectively all consumers in a group will receive all message just once*

<br>



# Trouble Shooting

    "If you get an error that kafka is running currently you can kill the process."

    ps -fA | grep ./bin/kafka-server-start.sh

    sudo kill -9 <process-id>

    "Faile to acquire lock on file .lock in /kafka/logs. A Kafka instance in another process or thread is using this directory"

    sudo rm /kafka/logs/.lock