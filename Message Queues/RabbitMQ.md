[[Message Queue]]

## What is RabbitMQ
RabbitMQ, a popular open-source message broker, uses the AMQP (Advanced Message Queuing Protocol) to send and receive messages between applications

## Core components of RabbitMQ

To understand ****RabbitMQ**** in depth we need to understand the core components of RabitMQ first, so next we will explore RabbirMQ’s core components.
### 1. Producer
A producer in RabbitMQ is an application that is responsible for sending messages to the ****RabbitMQ server****. These messages can carry various kinds of data such as [****JSON****](https://www.geeksforgeeks.org/javascript/javascript-json/), [****XML****](https://www.geeksforgeeks.org/html/xml-basics/), or simple plain text.

### 2. Queue
In RabbitMQ, a Queue can be referred to as temporary or buffered storage of messages, ****RabbitMQ manages a queue, where messages are stored before they are consumed by the receiver****. Queues can have the nature of durable or transient which means persisting across restarts and deleted when the server restarts respectively.

### 3. Consumer
As the producer is responsible for sending, the Consumer in RabbitMQ is responsible for reading and processing messages from the queue. On receiving the message consumer can acknowledge which means it tells RabbitMQ that the message has been processed successfully.

### 4. Exchange
An ****exchange**** is responsible for routing messages among queues. ****RabbitMQ provides support for various kinds of exchanges such as direct, fanout, topic, and headers, each queue with its logic.****

It supports different types of exchanges listed below
- ****Direct Exchange:**** In this exchange, messages are being routed based on routing keys. This means that when the routing key matches the binding key, the message gets delivered to its corresponding queue.
- ****Fanout Exchange:**** With this exchange, RabbitMQ does the broadcasts to all messages that are bound to it regardless of the routing key.
- ****Topic Exchange:**** With topic exchange message routing happens based on pattern matching to the routing and binding key.
- ****Headers Exchange:**** In this route messages are based on headers instead of the routing key. The headers exchange is more flexible but less efficient compared to other exchange types.

### 5. Binding
In ****RabbitMQ**** a connection between queue and exchange is called ****binding****. The message route from the exchange to the queue is defined by binding based on specific criteria such as routing keys.

### 6. Routing Key
The routing key plays a very important role in ****RabbitMQ;**** the exchange in RabbitMQ uses the routing key attribute to determine how to route the message to the appropriate queue(s). It ensures the correct message between exchanges.

## How RabbitMQ Works

In the first step, the producer sends a message to RabbitMQ and the exchange receives the message. Then the exchange routes the message to one or more queues based on the routing key and exchange type. ****Once the message is in the queue, it waits for the consumer to receive and process it.**** Once the process starts, the consumer sends an acknowledgment to RabbitMQ which indicates that the message has been handled successfully. In case of no acknowledgment, RabbitMQ may resend the message to ensure the successful full process of the message.

****RabbitMQ**** provides support for multiple messaging patterns such as ****Point-to-Point****, ****Publish/Subscribe****, and ****Request/Reply.****

- ****Point-to-Point:**** In this pattern, one producer sends the message to only one queue, which will be consumed by one consumer only.
- ****Publish/Subscribe:**** In this pattern, the producer sends the message to an exchange, which routes to multiple queues. Here each queue can have one or more consumers.
- ****Request/Reply:**** Here, the consumer processes the message that it has received and sends a response back to the producer, this can be referred to as two-way communication also.

