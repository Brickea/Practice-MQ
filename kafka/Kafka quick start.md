# Quick Start for Kafka <!-- omit in toc -->

<img src="https://media.giphy.com/media/L1KpkdbH8aEkXow8eV/giphy.gif" width="100%">

- [Download the Kafka](#download-the-kafka)
- [Prepare Kafka environment](#prepare-kafka-environment)
- [Create a topic for Kafka](#create-a-topic-for-kafka)
- [Write some events into topic](#write-some-events-into-topic)
- [Read events from topic](#read-events-from-topic)
- [Import/Export your data as streams of events with **Kafka connect**](#importexport-your-data-as-streams-of-events-with-kafka-connect)
- [Process events with Kafka streams](#process-events-with-kafka-streams)
- [Delete all local data](#delete-all-local-data)

https://kafka.apache.org/documentation/#connects](#importexport-your-data-as-streams-of-events-with-kafka-connect)

## Download the Kafka

Here is the [link](https://www.apache.org/dyn/closer.cgi?path=/kafka/2.6.0/kafka_2.13-2.6.0.tgz)

And then extract it

```
tar -xzf kafka-x-x.tgz
```

## Prepare Kafka environment

*NOTE: Your local environment must have Java 8+ installed.*

Run the following commands in order to start all services in the correct order:

```
# Start the ZooKeeper service
# Note: Soon, ZooKeeper will no longer be required by Apache Kafka.

# In summary, ZK assists in the ‘under the hood’ coordination functions, so that the brokers are able to ‘agree’ on a consistent configuration and effectively serve the clients.

$ bin/zookeeper-server-start.sh config/zookeeper.properties
```

Open another terminal session and run:

```
# Start the Kafka broker service
$ bin/kafka-server-start.sh config/server.properties
```

Once all services have successfully launched, you will have a basic Kafka environment running and ready to use.

## Create a topic for Kafka

a topic is similar to a folder in a filesystem, and the events are the files in that folder.

```
$ bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092
```

All of Kafka's command line tools have additional options: run the ```kafka-topics.sh``` command without any arguments to display the usage information.

it can also show you details such as the partition count of the new topic:


```
$ bin/kafka-topics.sh --describe --topic quickstart-events --bootstrap-server localhost:9092
```

## Write some events into topic

Run the **console producer client** to write a few events into your topic. By default, each line you enter will result in a separate event being written to the topic.

```
$ bin/kafka-console-producer.sh --topic quickstart-events --bootstrap-server localhost:9092
```

You can stop the producer client with ```Ctrl-C``` at any time.

## Read events from topic

Run the **console consumer** to read the events that you just created:

```
$ bin/kafka-console-consumer.sh --topic quickstart-events --from-beginning --bootstrap-server localhost:9092
```

During this time, if you open one producer console and write events, your consumer console will receive the events right away.

## Import/Export your data as streams of events with **Kafka connect**

You probably have lots of data in existing systems like relational databases or traditional messaging systems, along with many applications that already use these systems. [Kafka Connect](https://kafka.apache.org/documentation/#connect) allows you to continuously ingest data from external systems into Kafka, and vice versa. It is thus very easy to integrate existing systems with Kafka. To make this process even easier, there are hundreds of such connectors readily available.

Take a look at the [Kafka Connect section](https://kafka.apache.org/documentation/#connect) learn more about how to continuously import/export your data into and out of Kafka.

## Process events with Kafka streams

Once your data is stored in Kafka as events, you can process the data with the **Kafka Streams** client library for Java/Scala. It allows you to implement mission-critical real-time applications and microservices, where the input and/or output data is stored in Kafka topics. Kafka Streams combines the simplicity of writing and deploying standard Java and Scala applications on the client side with the benefits of Kafka's server-side cluster technology to make these applications highly scalable, elastic, fault-tolerant, and distributed. The library supports exactly-once processing, stateful operations and aggregations, windowing, joins, processing based on event-time, and much more.

To give you a first taste, here's how one would implement the popular WordCount algorithm:

```java
KStream<String, String> textLines = builder.stream("quickstart-events");

KTable<String, Long> wordCounts = textLines
            .flatMapValues(line -> Arrays.asList(line.toLowerCase().split(" ")))
            .groupBy((keyIgnored, word) -> word)
            .count();

wordCounts.toStream().to("output-topic"), Produced.with(Serdes.String(), Serdes.Long()));
```

The [Kafka Streams demo](https://kafka.apache.org/25/documentation/streams/quickstart) and the [app development tutorial](https://kafka.apache.org/25/documentation/streams/tutorial) demonstrate how to code and run such a streaming application from start to finish.


## Delete all local data

If you also want to delete any data of your local Kafka environment including any events you have created along the way, run the command:

```
rm -rf /tmp/kafka-logs /tmp/zookeeper
```
