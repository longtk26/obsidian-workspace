## What is change data capture?
> Change Data Capture (CDC) is a method used in databases to track and record changes made to data

**Behavior**
CDC captures modifications like inserts, updates, and deletes, and stores them for analysis or replication. CDC helps maintain data consistency across different systems by keeping track of alterations in real-time. It's like having a digital detective that monitors changes in a database and keeps a log of what happened and when.

## Which use cases are used?
### Cache invalidation
> Automatically invalidate entries in a cache as soon as the record(s) for entries change or are removed. If the cache is running in a separate process (e.g., Redis, Memcache, Infinispan, and others), then the simple cache invalidation logic can be placed into a separate process or service, simplifying the main application. In some situations, the logic can be made a little more sophisticated and can use the updated data in the change events to update the affected cache entries.

### Data integration
Data is often stored in multiple places, especially when it is used for different purposes and has slightly different forms. Keeping the multiple systems synchronized can be challenging. Example:
> When a system uses a separate search service such as Elasticsearch, it is important to keep the search index synchronized with the main database whenever new records are created or existing data is updated.
> One effective approach to achieve this is by using Change Data Capture (CDC). With CDC in place, the system can automatically capture changes from the database and propagate them to the search service. This helps reduce the amount of business logic that needs to be implemented within the application layer for manually sending updates to Elasticsearch. 
> Instead, a lightweight event processing mechanism can be introduced to handle these changes asynchronously. This not only simplifies the application design but also improves scalability and maintainability of the overall system.

### Real time analysis
CDC powers real-time analytics platforms by continuously feeding data changes into analytical systems, enabling organizations to derive insights from fresh data and respond swiftly to changing conditions.

## Pros and cons
### **Pros**
### 1. Decouples application logic

- The main application no longer needs to explicitly publish events or sync data.
- Reduces duplicated logic across services.
### 2. Near real-time data synchronization

- Changes from the database are streamed almost instantly.
- Ideal for search indexing, analytics, caching layers, etc.
### 3. Non-intrusive to existing systems

- Works at the database level (via WAL (Write Ahead Logging)/binlog), so no need to modify application code heavily.
- Useful for legacy systems.
### 4. Event-driven architecture enablement

- Naturally integrates with systems like Kafka.
- Makes it easier to build microservices and reactive pipelines.
### 5. Data consistency (eventually consistent)

- Ensures downstream systems reflect the source of truth over time.
### 6. Supports multiple consumers

- One stream of changes can feed multiple services (search, analytics, auditing, etc.)
### **Cons**
### 1. Increased system complexity

- Requires infrastructure like Kafka, connectors, monitoring.
- Debugging issues across multiple components can be harder.
### 2. Schema evolution challenges

- Changes in database schema can break consumers if not handled carefully.
- Requires schema management strategy (e.g., schema registry).
### 3. Operational overhead

- Need to manage connectors, offsets, failures, retries.
- Requires DevOps maturity.
### 4. Learning curve

- Teams need to understand event-driven design, CDC concepts, and tooling.
### 6. Data filtering/transformation is limited at source

- Raw DB changes may not match business needs → requires additional processing layer.

## When should use CDC

### ✔ Use CDC when:

- We need to sync data to systems like Elasticsearch, data warehouse, or cache
- We are building event-driven or microservices architecture
- We want to reduce coupling between services
- We need audit logs or history tracking
- We have multiple downstream consumers of the same data
- We want near real-time processing without modifying core application logic

👉 Example:
- Sync user data from PostgreSQL → Elasticsearch for search
- Stream order events → analytics pipeline

---

## When you SHOULD NOT use CDC

### ✖ Avoid CDC when:
- Your system requires **strong consistency** (no delay allowed)
- The system is **simple/monolithic** and doesn’t need event streaming
- We only have **one consumer** and simple integration (a direct API call is enough)
- Our team lacks experience in operating distributed systems
- Infrastructure cost/complexity is a concern
- Our database load is already high (CDC adds overhead)

👉 Example:
- Simple CRUD app with basic search → just update Elasticsearch directly
- Low-scale system without real-time needs

