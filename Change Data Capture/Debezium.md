**Related documents**
[[Overview CDC]]
[[ElasticSearch]]
[[Kafka]]
## What is debezium
Debezium is an open source project that provides a low latency data streaming platform for change data capture (CDC). Set up and configure Debezium to monitor our databases, and then our applications consume events for each row-level change made to the database.

## How to setup debezium
### Setup postgres connector for tracking database changes
- Create a base network with this command
```bash
docker create network base-network
```
- Prepare a `Dockerfile` and put it in `connect` folder
```bash
FROM debezium/connect:3.0.0.Final

# Install Elasticsearch sink connector for Kafka Connect.

ARG ELASTICSEARCH_SINK_VERSION=15.1.1

RUN set -eux; \

    curl -fsSL "https://hub-downloads.confluent.io/api/plugins/confluentinc/kafka-connect-elasticsearch/versions/${ELASTICSEARCH_SINK_VERSION}/confluentinc-kafka-connect-elasticsearch-${ELASTICSEARCH_SINK_VERSION}.zip" -o /tmp/es-sink.zip; \

    mkdir -p /kafka/connect/confluentinc-kafka-connect-elasticsearch; \

    unzip -q /tmp/es-sink.zip -d /kafka/connect/confluentinc-kafka-connect-elasticsearch; \

    rm -f /tmp/es-sink.zip
```
- Prepare docker compose file
```yml
version: '3.9'
services:
  kafka-ui:
    container_name: kafka-ui
    image: ghcr.io/kafbat/kafka-ui:latest
    ports:
      - "8070:8080"
    depends_on:
      - kafka
    environment:
      KAFKA_CLUSTERS_0_NAME: local
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:9092  
      DYNAMIC_CONFIG_ENABLED: 'true'
    networks:
      - base-network
  kafka:
    image: apache/kafka:4.0.2
    container_name: kafka
    ports:
      - "9092:9092"
      - "9094:9094"
    environment:

      ############################################

      # KRaft Metadata & Node Identity

      ############################################

      # Unique ID for this Kafka node

      KAFKA_NODE_ID: 1

      # Unique Kafka cluster identifier (required for KRaft mode)

      KAFKA_CLUSTER_ID: 'energy-tracker-cluster-1'

      # Node roles — this instance will act as BOTH a broker and controller

      KAFKA_PROCESS_ROLES: 'broker,controller'

  

      ############################################

      # Controller Quorum Configuration (Raft)

      ############################################

      # List of controller nodes that form the Raft quorum

      KAFKA_CONTROLLER_QUORUM_VOTERS: '1@kafka:9093'

      # Listener name used by controllers for internal Raft communication

      KAFKA_CONTROLLER_LISTENER_NAMES: 'CONTROLLER'

  

      ############################################

      # Network Listeners (Internal, External & Controller)

      ############################################

      # Define all listener endpoints for this Kafka node

      KAFKA_LISTENERS: >
        PLAINTEXT://0.0.0.0:9092,
        EXTERNAL://0.0.0.0:9094,
        CONTROLLER://0.0.0.0:9093

      # What each listener advertises to connecting clients
      # Internal (Docker containers): kafka:9092
      # External (host machine): localhost:9094
      KAFKA_ADVERTISED_LISTENERS: >
        PLAINTEXT://kafka:9092,
        EXTERNAL://localhost:9094
      # Maps listener names to their security protocols

      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: >
        CONTROLLER:PLAINTEXT,
        PLAINTEXT:PLAINTEXT,
        EXTERNAL:PLAINTEXT
      # Which listener brokers should use for internal communication

      KAFKA_INTER_BROKER_LISTENER_NAME: 'PLAINTEXT'
      ############################################

      #  Single-Node Cluster Safety Settings

      #    (because replication >1 would break)

      ############################################
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1

      ############################################

      #  Broker Defaults & Quality-of-Life Settings

      ############################################

      # Speed up consumer group startup

      KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS: 0
      # Auto-create topics if they don't already exist
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: 'true'
      # Default number of partitions per topic
      KAFKA_NUM_PARTITIONS: 1
    networks:
      - base-network

  debezium-connect:
    build:
      context: ./connect
    container_name: debezium-connect
    ports:
      - "8083:8083"
    environment:
      - BOOTSTRAP_SERVERS=kafka:9092
      - GROUP_ID=1
      - CONFIG_STORAGE_TOPIC=my_connect_configs
      - OFFSET_STORAGE_TOPIC=my_connect_offsets
      - STATUS_STORAGE_TOPIC=my_connect_statuses
      - CONFIG_STORAGE_REPLICATION_FACTOR=1
      - OFFSET_STORAGE_REPLICATION_FACTOR=1
      - STATUS_STORAGE_REPLICATION_FACTOR=1
      - REST_ADVERTISED_HOST_NAME=debezium-connect
      - KEY_CONVERTER=org.apache.kafka.connect.storage.StringConverter
      - VALUE_CONVERTER=org.apache.kafka.connect.json.JsonConverter
      - VALUE_CONVERTER_SCHEMAS_ENABLE=false
      - PLUGIN_PATH=/kafka/connect,/usr/share/java,/usr/share/confluent-hub-components
    depends_on:
      - kafka
    networks:
      - base-network

  debezium-ui:
    container_name: debezium-ui
    image: debezium/debezium-ui
    ports:
      - "8080:8080"
    environment:
      - KAFKA_CONNECT_URIS=http://debezium-connect:8083
    depends_on:
      - debezium-connect
    networks:
      - base-network

networks:
  base-network:
    external: true
```
- Running docker compose
```bash
docker compose up -d --build
```
- Summary settings
	- Debezium connect: This is service that will create connector to tracking Write-Ahead Logging of databases like PostgreSQL,...The connector will be created using RESTful APIs
	- Debezium UI: Interact on UI will simplied the process call APIs to Debezium connect
	- Kafka: Setting up Kafka to be a place that will receive streaming message changes from debezium connect
	- Kafka UI: visualize messages, consumer and topics come to Kafka for easy in debugging
	- The usage of Dockerfile that will help us add more connector to debezium connect. Because below I will show you how to sync from PostgreSQL to ElasticSearch then I will need a connector to stream directly data to ElasticSearch. In your case if doesn't need ElasticSearch you  could remove Dockerfile and use image of debezium connect normally
	
## Setup synchronization from PostgreSQL to ElasticSearch
- Keep above  `docker-compose.yml` for setting up debezium and kafka.
- Create another `docker-compose-elk.yml` for setting up ElasticSearch and Kibana
```yml
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.15
    container_name: elasticsearch
    ports:
      - "9200:9200"
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - ES_JAVA_OPTS=-Xms512m -Xmx512m
    networks:
      - base-network

  kibana:
    image: docker.elastic.co/kibana/kibana:7.17.15
    container_name: kibana
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    depends_on:
      - elasticsearch
    networks:
      - base-network

networks:
  base-network:
    external: true
```
- Run above docker compose to start elasticsearch and kibana
```bash
docker compose -f docker-compose-elk.yml up -d --build
```
- Next we will create connector for PostgreSQL and ElasticSearch by using 2 curls below
> Note: You need to create table `users` in your PostgreSQL database before

* **ElasticSearch connector**
```bash
curl --location 'http://localhost:8083/connectors' \
--header 'Content-Type: application/json' \
--data '{
  "name": "elasticsearch-sink-connector",
  "config": {
    "connector.class": "io.confluent.connect.elasticsearch.ElasticsearchSinkConnector",
    "topics": "users",
    "connection.url": "http://elasticsearch:9200",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter.schemas.enable": "false",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
    "schema.ignore": "true",
    "key.ignore": "false",

  
    "write.method": "upsert",
    "behavior.on.null.values": "delete",
    "transforms": "extractKey,removeDeleted",
    "transforms.extractKey.type": "org.apache.kafka.connect.transforms.ExtractField$Key",
    "transforms.extractKey.field": "id",
    "transforms.removeDeleted.type": "org.apache.kafka.connect.transforms.ReplaceField$Value",
    "transforms.removeDeleted.blacklist": "__deleted",
    "errors.tolerance": "all",
    "errors.deadletterqueue.topic.name": "dlq-users",
    "errors.deadletterqueue.topic.replication.factor": "1"
  }
```
* **PostgreSQL connector**
```bash
curl --location 'http://localhost:8083/connectors' \
--header 'Content-Type: application/json' \
--data '{
  "name": "postgres-source-connector",
  "config": {
    "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
    "database.hostname": "postgresdb",
    "database.port": "5432",
    "database.user": "postgres",
    "database.password": "postgres",
    "database.dbname": "template",
    "topic.prefix": "users_table",
    "table.include.list": "public.users",
    "plugin.name": "pgoutput",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter.schemas.enable": "false",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
    "transforms": "route,unwrap",
    "transforms.route.type": "org.apache.kafka.connect.transforms.RegexRouter",
    "transforms.route.regex": "users_table.public.users",
    "transforms.route.replacement": "users",
    "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
    "transforms.unwrap.drop.tombstones": "false",
    "transforms.unwrap.delete.handling.mode": "rewrite"
  }
```
- Now just need to run a transaction for CREATE, UPDATE, DELETE in your postgreSQL database and view message of topics `users` on Kafka UI
- Then check the index `users` of ElasticSearch in devtools of kibana UI.