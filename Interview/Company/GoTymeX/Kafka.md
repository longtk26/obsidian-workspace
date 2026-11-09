|#|Interview question|Key concepts to cover|
|---|---|---|
|1|What is Apache Kafka, and why do we use it?|Distributed event streaming, decoupling, scalability|
|2|What is the difference between a message queue and an event streaming platform?|Message retention, replay, consumers|
|3|What are Kafka brokers, topics, partitions, and offsets?|Core architecture|
|4|Why does Kafka use partitions?|Parallelism, scalability, ordering|
|5|What is a consumer group?|Consumer coordination, load balancing|
|6|What happens when a consumer joins or leaves a consumer group?|Rebalancing|
|7|How does Kafka guarantee message ordering?|Ordering within a partition|
|8|What is a replication factor?|High availability, fault tolerance|
|9|What is the difference between a leader and follower replica?|Partition replication|
|10|What happens when a Kafka broker goes down?|Leader election, ISR|
|11|What are ISR and `min.insync.replicas`?|Replication guarantees|
|12|What is the difference between Kafka and Redis Pub/Sub?|Persistence, replay, delivery model|
|13|What is the difference between Kafka and RabbitMQ?|Streaming vs traditional messaging|
|14|What is log compaction?|Retention by key, latest state|
|15|What is the difference between retention and log compaction?|Event history vs compacted state|