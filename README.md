**Setup**

Run [spotify/kafka](https://github.com/spotify/docker-kafka) docker image. For example: 

```
docker run --rm -d --name kafka -p 9092:9092 --env ADVERTISED_PORT=9092 --env ADVERTISED_HOST=localhost spotify/kafka
```
_Note: Follow the instructions on docker-kafka README to customise the container._

Enter the container bash: 

```
docker run exec -it kafka /bin/bash
```

And then go to the folder:

```
cd /opt/kafka_<scala_version>-<kafka_version>
```

Kafka utilities are now available:

# Topics

Creating a New Topic
```
./bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic my-topic
```
Verify the topic
```
./bin/kafka-topics.sh --list --zookeeper localhost:2181
```
Adding Partitions
```
./bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic my-topic --partitions 16
```
Deleting a Topic
```
./bin/kafka-topics.sh --zookeeper localhost:2181 --delete --topic my-topic
```
Listing All Topics in a Cluster
```
./bin/kafka-topics.sh --zookeeper localhost:2181 --list
```
Describing Topic Details
```
./bin/kafka-topics.sh --zookeeper localhost:2181/kafka-cluster --describe
```
Show Under-replicated Partitions for topics
```
./bin/kafka-topics.sh --zookeeper localhost:2181/kafka-cluster --describe --under-replicated-partitions
```

# Producers
Produce messages standard input
```
./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic my-topic
```
Produce messages file
```
./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test < messages.txt
```
Produce Avro messages
```
./bin/kafka-avro-console-producer.sh --broker-list localhost:9092 --topic my.Topic --property value.schema='{"type":"record","name":"myrecord","fields":[{"name":"f1","type":"string"}]}' --property schema.registry.url=http://localhost:8081
```
And enter a few values from the console:
```
{"f1": "value1"}
```

# Consumers

## Consume messages

Start a consumer from the beginning of the log
```
./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic my-topic --from-beginning
```
Consume 1 message
```
./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic my-topic  --max-messages 1
```

Consume 1 message from `__consumer_offsets`
```
./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic __consumer_offsets --formatter 'kafka.coordinator.GroupMetadataManager$OffsetsMessageFormatter' --max-messages 1
```

Consume, specify consumer group:
```
./bin/kafka-console-consumer.sh --topic my-topic --new-consumer --bootstrap-server localhost:9092 --consumer-property group.id=my-group
```

## Consume Avro messages
```
./bin/kafka-avro-console-consumer.sh --topic position-reports --new-consumer --bootstrap-server localhost:9092 --from-beginning --property schema.registry.url=localhost:8081 --max-messages 10
```

```
./bin/kafka-avro-console-consumer.sh --topic position-reports --new-consumer --bootstrap-server localhost:9092 --from-beginning --property schema.registry.url=localhost:8081
```

## Consumers admin operations

List Groups
```
./bin/kafka-consumer-groups.sh --new-consumer --list --bootstrap-server localhost:9092
```
Describe Groups
```
./bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group testgroup
```

# Config
Set the retention for the topic
```
./bin/kafka-configs.sh --zookeeper localhost:2181 --alter --entity-type topics --entity-name my-topic --add-config retention.ms=3600000
``` 
Show all configuration overrides for a topic
```
./bin/kafka-configs.sh --zookeeper localhost:2181 --describe --entity-type topics --entity-name my-topic
```
Delete a configuration override for retention.ms for a topic 
```
./bin/kafka-configs.sh --zookeeper localhost:2181 --alter --entity-type topics --entity-name my-topic --delete-config retention.ms 
```

# Performance

Producer
```
./bin/kafka-producer-perf-test.sh --topic position-reports --throughput 10000 --record-size 300 --num-records 20000 --producer-props bootstrap.servers="localhost:9092"
```

# ACLs
```
./bin/kafka-acls.sh --authorizer-properties zookeeper.connect=localhost:2181 --add --allow-principal User:Bob --consumer --topic topicA --group groupA
```

```
./bin/kafka-acls.sh --authorizer-properties zookeeper.connect=localhost:2181 --add --allow-principal User:Bob --producer --topic topicA
```
List the ACLs
```
./bin/kafka-acls.sh --authorizer-properties zookeeper.connect=localhost:2181 --list --topic topicA
```

# Zookeeper 
Enter zookeepr shell:
```
zookeeper-shell localhost:2182 ls /
```
