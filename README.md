# spark-streaming-flume-kafka-integration

This is a simple demo that integrating Spark streaming、Flume and Kafka.    

we use **LoggerGenerator** to create logs and sent to Flume for collect. Then Flume use the *kafka sink* that can publish data to 
kafka topic **streamingtopic**.Finally,spark stream receive data from Kafka and do some actions.

