 **Related documents**
[[Message Queue]]
## What is Kafka

Apache Kafka allows you to decouple your data streams and systems. So the idea is that the source systems will have the responsibility to send their data into Apache Kafka, and then any target systems that want to get access to this data feed this data stream will have to query and read from Apache Kafka to get the stream of data from these 3 systems and so by having this decoupling we are putting the responsibility of receiving and sending the data all on Apache Kafka. 

![](https://media.geeksforgeeks.org/wp-content/cdn-uploads/20220711233438/Kakfa-1.drawio-2.png "Click to enlarge")

## How Does Apache Kafka Work?

Apache Kafka is a distributed, high-performance platform for real-time data streaming and message processing. But if you're just starting out with it, you may be thinking how they works:

Think of Kafka as a large, really fast post office that receives messages ([data](https://www.geeksforgeeks.org/data-science/what-is-data/)) from various sources and sends them to their respective destinations.

- [Producers](https://www.geeksforgeeks.org/java/apache-kafka-producer/) send the messages (similar to people sending letters).
- [Kafka brokers](https://www.geeksforgeeks.org/cloud-computing/what-is-a-kafka-broker/) behave like postal staff – sorting, storing, and controlling the flow.
- [Consumers](https://www.geeksforgeeks.org/java/apache-kafka-consumer/) get the messages (such as individuals opening their mailbox).
- Topics are similar to labeled bins or folders that categorize where messages go.
- Partitions assist in dividing the load so Kafka can process massive amounts of data in a timely manner.

## Kafka Architecture

Kafka architecture is based on producer-subscriber model and follows distributed architecture, runs as cluster.

### 1. ****Kafka Producers****

- These are the applications or systems that produce data into Kafka.
- For example, a mobile application producing user clicks, or an online store producing order information.
- Producers publish messages to a Kafka topic.

### 2. ****Kafka Topics****

- A topic is a named stream of data.
- It groups messages by category. For example: orders, payments, or user-activity.
- Topics can be divided into partitions to enhance performance.

### 3. ****Kafka Partitions****

- Partitions are similar to lanes in a highway. Every topic is assigned one or more partitions to deal with high-traffic volume.
- Messages within partitions are persisted in the same order they arrived
	![](https://kafka.apache.org/images/streams-and-tables-p1_p4.png)

### 4. Kafka Brokers

- A Kafka broker is a server that continues the data and serves requests.
- Multiple brokers make up a Kafka cluster in big systems to share the load and ensure high availability.

### 5. Kafka Consumers

- Consumers are apps or services reading data from a Kafka topic.
- They subscribe to a topic and pull messages periodically.
- Consumers can operate as consumer groups for load sharing to avoid missing data

## How Kafka Transfers Data

Here how the Apache Kafka transfer the data step by step:

- A producer application sends a message to Kafka.
- Kafka stores the message in a specific partition inside a topic.
- The message is held in Kafka's disk-based log storage — it’s not deleted immediately.
- A consumer application reads the message from that partition.
- After reading, Kafka doesn’t delete the message (unless configured) — this allows multiple consumers to read the same data independently.