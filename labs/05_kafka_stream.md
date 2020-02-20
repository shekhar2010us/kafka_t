## Kafka Streams to process messages

Overall idea:

Movie ratings from file -> push to Kafka Producer to topic "movie_topics" -> Consume the topic in kafka streams -> process each message in the stream; output if the sentiment prediction is correct -> push the correctness of sentiment into a new topic "new_movie_topic" -> create consumers for both topic and test


### Step 1:

Create a topic from kafka shell `new_movie_topic` with `partition=1` and `replication_factor=1`

```
kafka-topics.sh --zookeeper localhost:2181 --create --topic new_movie_topic --partitions 1 --replication-factor 1
```


### Step 2:

Create consumers - one for `movie_topic` and the other for `new_movie_topic`

- IMPORTANT - keep the two consumers running in two terminals


```
## consumer for topic "movie_topic"
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic movie_topic

## consumer for topic "new_movie_topic"
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic new_movie_topic
```


### Step 3:

Run the java code for the producer that push movie ratings from the file.

```
## run FileProducer class in background
cd ~/kafka_java
mvn clean install -U

nohup mvn exec:java -Dexec.mainClass="com.shekhar.kafka.file.FileProducer" &

---> result: you will see messages will start coming to the first consumer but not in the second consumer
```


### Step 4:

Use Kafka Stream to analyze mesaages in the topic "movie_topic" and push predictions to the new topic

```
## run StreamFilterMovieRatings class in background
nohup mvn exec:java -Dexec.mainClass="com.shekhar.kafka.stream.StreamFilterMovieRatings" &

---> result: you will see messages will start coming to the second consumer about "Predicted Correctly" or "I am not intelligent enough to predict this correctly"
```

<br>
<p>!! <b>Congratulations.</b> You have build an application that reads movie ratings from logs, process and predict the sentiment using kafkastreams</p>

