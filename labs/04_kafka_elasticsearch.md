## Kafka and Elasticsearch


### Create a movie rating producer

#### create a topic "movie_topic"

We will use this topic for this and the next lab on kafka streams

```
kafka-topics.sh --zookeeper localhost:2181 --create --topic movie_topic --partitions 2 --replication-factor 1
```

#### start two consumers in a group for the above topic

```
## open 2 terminals and run command
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic movie_topic --group movie-app
```


#### (java) - read movie sentiment file, and create a producer

```
cd ~/kafka_java/

## check the java class
vi src/main/java/com/shekhar/kafka/file/FileProducer.java
## exit


## run the main class
mvn exec:java -Dexec.mainClass="com.shekhar.kafka.file.FileProducer"

## result: when you check the two consumers - you can see json message containing movie review and sentiment in both consumers

### stop all three terminals
```


### Setup Elasticsearch

The goal is to push all messages from the movie producer to elasticsearch.

`https://bonsai.io/` provides a three node elasticsearch cluster free of cost with limited data and compute resource.

- go to `https://bonsai.io/`
- create an account and login
- confirm your email
- create a elastic cluster
- Go to bonsai elastic `console`, run a `PUT` Rest and pass `/movie` to create the movie index
- Run `Get` Rest `/_cat/indices?v` to verify indices
- Insert a document to elastic: `PUT` -> `/movie/rating/1` and insert a json document `{"class" : "positive", "text" : "awesome movie"}`
- verify insertion - run a `Get` and pass `/movie/rating/1`

<b>*Credentials:-*</b> Go to credentials tab and copy `elasticsearch access url`, `access key` and `access secret` 
Note :- We just need the hostname from the elasticsearch access url. The URl will be in format `https://<USERNAME>:<PASSWORD>@<HOSTNAME>:<PORT>` <br>
We only need HOSTNAME.


<b>*Replace with your credentials in the code:-*</b>
In the code, go to the java class `com.shekhar.kafka.elasticsearch.ElasticsearchConsumer`, and change the `esAccessUrl`, `accessKey` and `accessSecret` with yours.


```

## check the java class
vi src/main/java/com/shekhar/kafka/elasticsearch/ElasticsearchTest.java

## run the main class ElasticsearchTest - just to test if we can connect to elasticsearch from our java code
mvn exec:java -Dexec.mainClass="com.shekhar.kafka.elasticsearch.ElasticsearchTest"

#### result -- you will see an "_id" in the log. Go to bonsai and check the "movie" index by running GET on "/movie/rating/_id"
```


### Kafka to Elasticsearch

Open 3 terminals

```

## check the java class
vi src/main/java/com/shekhar/kafka/elasticsearch/ElasticsearchConsumer.java
---> here the consumer of topic "movie_topic" is consuming movie ratings and indexing in elasticsearch


cd ~/kafka_java/

## terminal 1: Run the movie producer - this will start putting messages to the topic "movie_topic"
mvn exec:java -Dexec.mainClass="com.shekhar.kafka.file.FileProducer"

## terminal 2: start a consumer
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic movie_topic --from-beginning

----> this will show the messages coming in


## terminal 3: run the Elastic consumer - this will consume messages from topic "movie_topic" and index to elasticsearch
mvn exec:java -Dexec.mainClass="com.shekhar.kafka.elasticsearch.ElasticsearchConsumer"

----> this will log "id" for documents inserted to elasticsearch. check elasticsearch for the documents
-----> Get -- /movie/rating/<id>

----> you can also go to a browser and check
	"http://<es_host>:9200/movie/_search"
	-- for all documents in movie index
	"http://<es_host>:9200/_cat/indices?v"
	-- to check how many documents has been indexed
	
	note:- replace <es_host> with yours
```

### cleanup

Stop all terminals


### Kibana

<b>Assignment</b>

- Go to Kibana
- Go to "Create index" on the left, search for "movie", click next
- create a data table or a graph based on class:positive|negative
