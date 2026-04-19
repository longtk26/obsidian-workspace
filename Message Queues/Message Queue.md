**Implementation**
[[Kafka]]
[[RabbitMQ]]

## What is message queue?
> Message queues enable asynchronous communication between system components by acting as a buffer between producers and consumers. They decouple services, allowing each component to operate independently and reliably even during delays or failures.

**Benefit**
- Improves scalability and load handling by distributing work across consumers.
- Enhances fault tolerance by storing messages until they are processed.
**Message format**
A typical message structure consists of two main parts:
- ****Headers:**** These contain metadata about the message, such as unique identifier, timestamp, message type, and routing information.
- ****Body:**** The body contains the actual message payload or content. It can be in any format, including text, binary data, or structured data like JSON.

## Components

A message queue system consists of different components that work together to send, store, and process messages asynchronously.

- ****Message Producer:**** Messages are created and sent to the message queue by the message producer. Any program or part of a system that produces data for sharing can be considered this.
- ****Message Queue:**** Until the message consumers consume them, the messages are stored and managed by a data structure or service called the message queue. It serves as a mediator or buffer between consumers and producers.
- ****Message Consumer:**** Messages in the message queue must be retrieved and processed by the message consumer. Messages from the queue can be read concurrently by several users.
- ****Message Broker (Optional):**** In some message queue systems, a message broker acts as an intermediary between producers and consumers, providing additional functionality like message routing, filtering, and message transformation.

## Types of message queue
There are two main types of message queues in system design:

### 1. Point-to-Point Message Queues

Point-to-point message queues are the simplest type of message queue. When a producer sends a message to a point-to-point queue, the message is stored in the queue until a consumer retrieves it. Once the message is retrieved by a consumer, it is removed from the queue and cannot be processed by any other consumer.

![queue](https://media.geeksforgeeks.org/wp-content/uploads/20260330182849095324/queue.webp "Click to enlarge")

Point-to-point Message Queues

Point-to-point message queues can be used to implement a variety of patterns such as:

- ****Request-Response:**** A producer sends a request message to a queue, and a consumer retrieves the message and sends back a response messages.
- ****Work Queue:**** Producers send work items to a queue, and consumers retrieve the work items and process them.
- ****Guaranteed Delivery:**** Producers send messages to a queue, and consumers can be configured retry retrieving messages until they are successfully processed.
### 2. Publish-Subscribe Message Queues

Publish-Subscribe Message Queues are more complex than point-to-point message queues. When a producer publishes a message to publish/subscribe queue, the message is routed to all consumers that are subscribed to the queue. Consumers can subscribe to multiple queues, and they can also unsubscribe from queues at any time.

- Publish-Subscribe Message Queues are often used to implement real-time streaming applications, such as social media and stock market tickers.
- They can also be used to implement event-driven architecture, where components of a system communicate with each other by publishing and subscribing to events.
## Message Routing

Message Routing involves determining how messages are directed to their intended recipients. The following methods can be employed:

- ****Topic-Based Routing:**** Messages are sent to topics or channels, and subscribers express interest in specific topics. Messages are delivered to all subscribers of a particular topic.
- ****Direct Routing:**** Messages are sent directly to specific queues or consumers based on their addresses or routing keys.
- ****Content-Based Routing:**** The routing decision is based on the content of the message. Filters or rules are defined to route messages that meet specific criteria.

## Dead Letter Queues and Message Prioritization

These concepts help manage failed messages and control the order in which messages are processed in a system.

### 1. Dead Letter Queues

Dead Letter Queues (DLQs) are a mechanism for handling messages that cannot be processed successfully. This includes:

- Messages with errors in their content or format.
- Messages that exceed their time-to-live (TTL) or delivery attempts.
- Messages that cannot be delivered to any consumer.

DLQs provide way to investigate and potentially reprocess failed messages while preventing them from blocking the system.

### 2. Message Prioritization

Message Prioritization is the process of assigning priority levels to messages to control their processing order. Prioritization criteria can include:

- ****Urgency:**** Messages with higher priority may need to processed before lower-priority messages.
- ****Message Content:**** Messages containing critical information or commands may receive higher priority.
- ****Business Rules:**** Custom business rules or algorithms may be used to determine message priority.