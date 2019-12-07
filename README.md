# spark-streaming-flume-kafka-integration

This is a simple demo that integrating Spark streaming、Flume and Kafka.    

we use **LoggerGenerator** to create logs and sent to Flume for collect. Then Flume use the *kafka sink* that can publish data to 
kafka topic **streamingtopic**.Finally,spark stream receive data from Kafka and do some actions.

## Version
apache-flume-1.7.0  
jdk-8u144-linux-x64  
kafka_2.11-0.11.0.2  
zookeeper-3.4.10  
spark-2.1.1

