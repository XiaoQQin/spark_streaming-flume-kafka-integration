# spark-streaming-flume-kafka-integration

This is a simple demo that integrating Spark streaming、Flume and Kafka.    

we use **LoggerGenerator** to create logs and sent to Flume for collect. Then Flume use the *kafka sink* that can publish data to 
kafka topic **streamingtopic**.Finally,spark streaming receive data from Kafka and do some actions.

## version
apache-flume-1.7.0  
jdk-8u144-linux-x64  
kafka_2.11-0.11.0.2  
zookeeper-3.4.10  
spark-2.1.1

## process
1. create Kafka topic name streamingtopic
   ```
   bin/kafka-topics.sh --create --zookeeper hadoop132:2181 --replication-factor 1 --partitions 1 --topic streamingtopic
   ```
   if your Kakfa version is >2.2.x , you should use --bootstrap-server replace the --zookeeper.you can see [kafka documenation](http://kafka.apache.org/documentation/) for detail.


2. create Flume agent file name streaming.conf
    the streaming.conf as folowed
    ```
    agent1.sources=avro-source
    agent1.channels=logger-channel
    agent1.sinks=kafka-sink

    #define source
    agent1.sources.avro-source.type=avro
    agent1.sources.avro-source.bind=0.0.0.0
    agent1.sources.avro-source.port=41414

    #define channels
    agent1.channels.logger-channel.type=memory

    #define sink
    agent1.sinks.kafka-sink.type=org.apache.flume.sink.kafka.KafkaSink
    agent1.sinks.kafka-sink.kafka.topic = streamingtopic
    agent1.sinks.kafka-sink.kafka.bootstrap.servers = hadoop132:9092
    agent1.sinks.kafka-sink.kafka.batchSize = 20
    agent1.sinks.k1.kafka.requireAcks = 1

    #merge
    agent1.sources.avro-source.channels=logger-channel
    agent1.sinks.kafka-sink.channel=logger-channel
    ```
    then start the agent1
    ```
    bin/flume-ng agent \
    --conf conf \
    --conf-file streaming.conf \
    --name agent1 \
    -Dflume.root.logger=INFO,console
    ```
3. start LoggerGenerator to create log
4. start KafkaWordCount

## note
you must change the hostname to your owm.  
if you want to test locally,you should change the KafkaWordCount
```
bootstrap.servers" -> "hadoop132:9092",
val topics = Array("streamingtopic")
```
you can use Maven->Lifecycle->package to package the project to jars and run in cluster.
1. start LoggerGenerator(under the premise of flume startup)
   ```
   java -classpath ./SparkStream-jar-with-dependencies.jar LoggerGenerator
   ```
2. submit KafkaWordCount (cd the SPARK_HOME)
   ```
    bin/spark-submit \
    --class KafkaWordCount \
    ./SparkStream-jar-with-dependencies.jar \
    hadoop132:9092 \
    streamingtopic
   ```
   the 'hadoop132:9092' is first  parameter and 'streamingtopic' is second streamingtopic.
