# Kafka Interview Questions (Senior Backend)

Target: backend developer with 3+ years of experience (Java, Spring Boot, microservices, Kafka, Docker).

Examples use the same setup as the review notes:

- Services: Order-Service (producer) and Inventory-Service (consumer).
- Topic: `order-events`.
- Learning setup: one broker, KRaft mode, Docker container `kafka-broker`, port `9092`.
- Production setup: 3 brokers, replication factor 3.

Version notes are marked as "(version-specific)". Nothing here was run by the author of this file. Treat the commands as examples and check them against your Kafka version.

---

## Index

- [Core Concepts](#core-concepts)
  - Basic
    - [Q1. What is Kafka and why use it? [Basic]](#q1-what-is-kafka-and-why-use-it-basic)
    - [Q2. What are topics, partitions and offsets? [Basic]](#q2-what-are-topics-partitions-and-offsets-basic)
    - [Q3. What does a Kafka record contain? [Basic]](#q3-what-does-a-kafka-record-contain-basic)
    - [Q4. What are a broker and a cluster? [Basic]](#q4-what-are-a-broker-and-a-cluster-basic)
    - [Q5. How long does Kafka keep messages? [Basic]](#q5-how-long-does-kafka-keep-messages-basic)
    - [Q6. What is the difference between KRaft and ZooKeeper? [Basic]](#q6-what-is-the-difference-between-kraft-and-zookeeper-basic)
    - [Q7. How is Kafka different from RabbitMQ? [Basic]](#q7-how-is-kafka-different-from-rabbitmq-basic)
  - Intermediate
    - [Q8. What are the ISR and the controller? [Intermediate]](#q8-what-are-the-isr-and-the-controller-intermediate)
    - [Q9. What is log compaction? [Intermediate]](#q9-what-is-log-compaction-intermediate)
    - [Q10. How do you choose the number of partitions? [Intermediate]](#q10-how-do-you-choose-the-number-of-partitions-intermediate)
    - [Q11. What happens when you add partitions to a topic? [Intermediate]](#q11-what-happens-when-you-add-partitions-to-a-topic-intermediate)
  - Advanced
    - [Q12. What is a hot partition and how do you fix it? [Advanced]](#q12-what-is-a-hot-partition-and-how-do-you-fix-it-advanced)

- [Producers and Consumers](#producers-and-consumers)
  - Basic
    - [Q13. How do producers and consumers work? [Basic]](#q13-how-do-producers-and-consumers-work-basic)
    - [Q14. What is a consumer group? [Basic]](#q14-what-is-a-consumer-group-basic)
    - [Q15. How do partition keys work and what ordering does Kafka guarantee? [Basic]](#q15-how-do-partition-keys-work-and-what-ordering-does-kafka-guarantee-basic)
  - Intermediate
    - [Q16. Auto commit vs manual commit of offsets? [Intermediate]](#q16-auto-commit-vs-manual-commit-of-offsets-intermediate)
    - [Q17. What is a rebalance and why can it be a problem? [Intermediate]](#q17-what-is-a-rebalance-and-why-can-it-be-a-problem-intermediate)
    - [Q18. How do you use Kafka with Spring Boot? [Intermediate]](#q18-how-do-you-use-kafka-with-spring-boot-intermediate)
    - [Q19. How do serialization and schemas work? [Intermediate]](#q19-how-do-serialization-and-schemas-work-intermediate)
    - [Q20. How do you tune producer throughput? [Intermediate]](#q20-how-do-you-tune-producer-throughput-intermediate)
    - [Q21. Which consumer timeout settings matter? [Intermediate]](#q21-which-consumer-timeout-settings-matter-intermediate)
  - Advanced
    - [Q22. What is an idempotent producer? [Advanced]](#q22-what-is-an-idempotent-producer-advanced)
    - [Q23. How do retries affect ordering? [Advanced]](#q23-how-do-retries-affect-ordering-advanced)
    - [Q24. How can you reduce the cost of rebalances? [Advanced]](#q24-how-can-you-reduce-the-cost-of-rebalances-advanced)
    - [Q25. What are Kafka Streams and Kafka Connect? [Advanced]](#q25-what-are-kafka-streams-and-kafka-connect-advanced)

- [Reliability and Delivery](#reliability-and-delivery)
  - Basic
    - [Q26. What is replication, and what are leaders and followers? [Basic]](#q26-what-is-replication-and-what-are-leaders-and-followers-basic)
  - Intermediate
    - [Q27. What do the producer acks settings mean? [Intermediate]](#q27-what-do-the-producer-acks-settings-mean-intermediate)
    - [Q28. What is min.insync.replicas? [Intermediate]](#q28-what-is-mininsyncreplicas-intermediate)
    - [Q29. What are the delivery semantics? [Intermediate]](#q29-what-are-the-delivery-semantics-intermediate)
    - [Q30. How do retries and dead-letter topics work in Spring Kafka? [Intermediate]](#q30-how-do-retries-and-dead-letter-topics-work-in-spring-kafka-intermediate)
    - [Q31. How do you handle duplicate messages? [Intermediate]](#q31-how-do-you-handle-duplicate-messages-intermediate)
  - Advanced
    - [Q32. How do Kafka transactions and exactly-once work? [Advanced]](#q32-how-do-kafka-transactions-and-exactly-once-work-advanced)
    - [Q33. How do you configure Kafka for no data loss? [Advanced]](#q33-how-do-you-configure-kafka-for-no-data-loss-advanced)
    - [Q34. What are the high watermark and unclean leader election? [Advanced]](#q34-what-are-the-high-watermark-and-unclean-leader-election-advanced)
    - [Q35. What is a poison pill message? [Advanced]](#q35-what-is-a-poison-pill-message-advanced)

- [Operations and Patterns](#operations-and-patterns)
  - Basic
    - [Q36. How do you run Kafka in Docker and use the CLI? [Basic]](#q36-how-do-you-run-kafka-in-docker-and-use-the-cli-basic)
  - Intermediate
    - [Q37. What is consumer lag and how do you check it? [Intermediate]](#q37-what-is-consumer-lag-and-how-do-you-check-it-intermediate)
  - Advanced
    - [Q38. What is the outbox pattern and why is it needed? [Advanced]](#q38-what-is-the-outbox-pattern-and-why-is-it-needed-advanced)
    - [Q39. How do you implement a saga with Kafka? [Advanced]](#q39-how-do-you-implement-a-saga-with-kafka-advanced)
    - [Q40. Consumer lag keeps growing in production. How do you debug it? [Advanced]](#q40-consumer-lag-keeps-growing-in-production-how-do-you-debug-it-advanced)
    - [Q41. How do you debug a broker failure or under-replicated partitions? [Advanced]](#q41-how-do-you-debug-a-broker-failure-or-under-replicated-partitions-advanced)
    - [Q42. Design question: Order and Inventory with Kafka [Advanced]](#q42-design-question-order-and-inventory-with-kafka-advanced)

---

# Core Concepts

---

### Q1. What is Kafka and why use it? [Basic]

**Level: Basic**

**Short answer:** Kafka is a distributed event log. Services write events to it and other services read them later, at their own speed. It decouples services and keeps events for replay.

**Explanation:**

- Kafka stores messages on disk, in order, for a set time.
- A service that sends messages is a **producer**. A service that reads them is a **consumer**.
- Direct calls (for example Feign/REST) are **synchronous**. The caller waits, and fails if the other service is down.
- With Kafka the call is **asynchronous**. Order-Service writes an event and moves on. If Inventory-Service is down, the event waits in Kafka.
- Common uses: event-driven microservices, log and metrics pipelines, change data capture, stream processing.

```text
Synchronous (Feign):
Order-Service --HTTP call--> Inventory-Service
(Order waits. If Inventory is down, the order fails.)

Asynchronous (Kafka):
Order-Service --event--> [ Kafka: order-events ] --event--> Inventory-Service
(Order does not wait. Event stays in Kafka until Inventory reads it.)
```

**Follow-up questions:**

- **What do you lose by using Kafka instead of a direct call?**
  You lose the immediate response. The result is eventually consistent. You must also handle duplicates, ordering and failures yourself.
- **When should you NOT use Kafka?**
  When you need an immediate answer (for example a price lookup), when volume is tiny and a simple queue is enough, or when the team cannot operate it.
- **Can several services read the same event?**
  Yes. Each consumer group reads all events independently (see Q14).

---

### Q2. What are topics, partitions and offsets? [Basic]

**Level: Basic**

**Short answer:** A topic is a named stream of events. It is split into partitions. Each partition is an ordered, append-only log, and every message in it has an offset (its position number).

**Explanation:**

- **Topic:** a name such as `order-events`.
- **Partition:** one ordered log. A topic has one or more. Each message goes to exactly ONE partition. Partitions are not copies of each other.
- **Offset:** a number that starts at 0 and grows by 1 for each message in a partition. Offsets are per partition, not per topic.
- Partitions give parallelism. One partition can be read by only one consumer in a group (see Q14).
- Messages are immutable. You cannot edit them. They are removed only by retention or compaction (Q5, Q9).

```text
Topic: order-events (3 partitions)

Partition 0: [off 0][off 1][off 2][off 3] -> new messages are appended here
Partition 1: [off 0][off 1][off 2]
Partition 2: [off 0][off 1][off 2][off 3][off 4]
```

A message is identified by topic + partition + offset.

**Follow-up questions:**

- **Is a message copied to all partitions?**
  No. It goes to one partition. Copies across brokers are a different thing called replication (Q26).
- **Do offsets continue across partitions?**
  No. Each partition has its own offsets starting at 0.
- **Can you decrease the number of partitions?**
  No. You can only increase it (see Q11).

---

### Q3. What does a Kafka record contain? [Basic]

**Level: Basic**

**Short answer:** A record has an optional key, a value (the payload), a timestamp, optional headers, and is stored with a partition and offset.

**Explanation:**

| Field | What it is |
|---|---|
| Key | Optional. Used to pick the partition and for compaction |
| Value | The payload (JSON, Avro, Protobuf, plain text, bytes) |
| Timestamp | Time the record was created (or appended by the broker, depending on config) |
| Headers | Optional key-value metadata such as trace ID or source service |
| Partition | Which partition holds it (set by Kafka) |
| Offset | Position in that partition (set by Kafka) |

Kafka stores only bytes. The producer and consumer use serializers and deserializers to convert objects to and from bytes (see Q19).

**Follow-up questions:**

- **What is a good use for headers?**
  Correlation or trace IDs, event type, schema version. They avoid changing the payload.
- **What is the maximum record size?**
  About 1 MB by default (`message.max.bytes` on the broker, `max.request.size` on the producer). Large payloads should go to object storage with only a reference in the event.

---

### Q4. What are a broker and a cluster? [Basic]

**Level: Basic**

**Short answer:** A broker is one Kafka server. A cluster is several brokers working together. Partitions and their copies are spread across the brokers.

**Explanation:**

- In Docker, one container is usually one broker. Our learning setup has one broker listening on `localhost:9092`.
- Production has 3 or more brokers for fault tolerance.
- Each broker has a unique `node.id`. A cluster has a cluster ID.
- Brokers on one machine need different ports. On different machines they can all use 9092.
- A client only needs one or two broker addresses (`bootstrap.servers`). It then discovers the rest of the cluster.

```text
Cluster (3 brokers, topic order-events with 3 partitions)

Broker 1: P0 (leader)   P1 (follower)  P2 (follower)
Broker 2: P0 (follower) P1 (leader)    P2 (follower)
Broker 3: P0 (follower) P1 (follower)  P2 (leader)
```

**Follow-up questions:**

- **Why does the client list only a few brokers?**
  Those are entry points. The client asks them for cluster metadata, then connects to the right leader for each partition.
- **Can replication factor be higher than the number of brokers?**
  No. With one broker the maximum is 1.

---

### Q5. How long does Kafka keep messages? [Basic]

**Level: Basic**

**Short answer:** Kafka keeps messages for a retention period, not until they are read. The default is 7 days. Reading a message does not delete it.

**Explanation:**

- `retention.ms`: delete data older than this. Default 604800000 ms (7 days).
- `retention.bytes`: delete oldest data when a partition exceeds this size. Default is no limit (-1).
- Both are set per topic or as a broker default.
- Deletion works on whole **segment files**, not single messages. A segment is a chunk of the partition log on disk. The active segment is never deleted. So data can stay a little longer than the exact time.
- Because data stays, you can replay it (reset offsets), and new services can read old events.

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-configs.sh \
    --bootstrap-server localhost:9092 \
    --alter \
    --entity-type topics \
    --entity-name order-events \
    --add-config retention.ms=86400000
# Sets retention for order-events to 1 day
```

**Follow-up questions:**

- **If a consumer is offline longer than retention, what happens?**
  Its offset may point to deleted data. It gets an out-of-range error and `auto.offset.reset` decides where it restarts. Messages are lost to that consumer.
- **How do you replay messages?**
  Stop the group, then reset its offsets with `kafka-consumer-groups.sh --reset-offsets` (see Q37), or use a new group ID with `earliest`.
- **What is the difference from RabbitMQ here?**
  A classic RabbitMQ queue removes a message after it is acknowledged (see Q7).

---

### Q6. What is the difference between KRaft and ZooKeeper? [Basic]

**Level: Basic**

**Short answer:** ZooKeeper was an external service that stored Kafka's cluster metadata and helped elect the controller. KRaft moves this into Kafka itself using the Raft protocol, so you run only Kafka.

**Explanation:**

- Old way: run ZooKeeper plus Kafka brokers. Two systems to operate.
- KRaft: some nodes have the `controller` role. They form a quorum (voting group) and store metadata in an internal log. A node can be `broker`, `controller` or both.
- Our Docker setup uses `KAFKA_PROCESS_ROLES: broker,controller`: one node does both jobs.
- Benefits: simpler operations, faster controller failover, and support for more partitions.
- Version-specific: KRaft became production-ready in Kafka 3.3. ZooKeeper mode is deprecated since 3.5 and removed in Kafka 4.0.

```yaml
KAFKA_NODE_ID: 1
KAFKA_PROCESS_ROLES: broker,controller
KAFKA_CONTROLLER_QUORUM_VOTERS: 1@localhost:9093
# Single node that is both broker and controller (learning setup)
```

**Follow-up questions:**

- **How many controller nodes should production have?**
  Usually 3 (or 5). The quorum needs a majority alive: 3 nodes tolerate 1 failure, 5 tolerate 2.
- **Can you migrate from ZooKeeper to KRaft?**
  Yes. There is a supported migration path in Kafka 3.x. You must complete it before upgrading to 4.0.

---

### Q7. How is Kafka different from RabbitMQ? [Basic]

**Level: Basic**

**Short answer:** Kafka is a durable log. Consumers pull and track their own position, messages stay after being read, and it scales for very high throughput. RabbitMQ is a traditional broker. It pushes messages to consumers and removes them once acknowledged. It has richer routing.

| Topic | Kafka | RabbitMQ |
|---|---|---|
| Model | Append-only log, consumers pull | Queues and exchanges, broker pushes |
| After read | Message stays until retention ends | Message removed after ack |
| Replay | Yes, by resetting offsets | Not normally (streams feature exists in newer versions) |
| Ordering | Per partition | Per queue (single consumer) |
| Scaling readers | Add consumers up to partition count | Add consumers on a queue |
| Routing | Topic + key only | Flexible (direct, topic, fanout, headers) |
| Throughput | Very high (millions/sec on a cluster) | High, usually lower than Kafka |
| Best for | Event streaming, event sourcing, data pipelines | Task queues, complex routing, request/reply, per-message TTL and priority |

**Follow-up questions:**

- **When would you pick RabbitMQ over Kafka?**
  For background job queues, per-message priority or delay, and complex routing rules, when you do not need replay.
- **How do other tools compare?**
  AWS SQS is a managed queue (no replay). Amazon Kinesis and Azure Event Hubs are managed log services similar to Kafka. Apache Pulsar is a log-based system with a different architecture.
- **Can Kafka act as a work queue?**
  Yes, with a consumer group. But it has no per-message ack or delay, and one slow message blocks the rest of its partition.

---

### Q8. What are the ISR and the controller? [Intermediate]

**Level: Intermediate**

**Short answer:** The ISR (in-sync replicas) is the set of replicas, including the leader, that are fully caught up. The controller is the broker (or KRaft node) that manages the cluster and elects partition leaders from the ISR.

**Explanation:**

- A follower stays in the ISR while it keeps fetching from the leader and is not too far behind. The limit is `replica.lag.time.max.ms` (default 30 seconds in recent versions).
- A follower that falls behind or crashes is removed from the ISR. It rejoins after catching up.
- When a leader fails, the controller picks the new leader from the ISR. This avoids losing committed data.
- The controller also detects broker joins and leaves, and updates cluster metadata. In KRaft the active controller is elected by the controller quorum. In our single-node setup the broker is also the controller.

```text
Partition 0, RF=3
Leader: Broker 1
Replicas: 1, 2, 3
ISR: 1, 2      (Broker 3 is lagging, so it was removed)

Broker 1 dies -> new leader chosen from {2} -> Broker 2
```

You can see leader, replicas and ISR in `kafka-topics.sh --describe` (Q36).

**Follow-up questions:**

- **What if the ISR shrinks to just the leader?**
  With `acks=all` and `min.insync.replicas=2`, writes are rejected (Q28). Without that setting, a write is acknowledged with one copy only, which is risky.
- **What is a preferred leader?**
  The first replica in the replica list. Kafka can rebalance leadership back to it after a failure (`auto.leader.rebalance.enable`).

---

### Q9. What is log compaction? [Intermediate]

**Level: Intermediate**

**Short answer:** Compaction is a cleanup mode that keeps only the latest record for each key. Older records with the same key are removed in the background.

**Explanation:**

- Set `cleanup.policy=compact` on the topic. (Default is `delete`, which is time/size retention.)
- Good for "current state" topics, such as latest product price or latest customer profile. A new service can rebuild the full state by reading the topic.
- Delete a key by sending a record with that key and a `null` value (a **tombstone**). Tombstones are removed after `delete.retention.ms` (default 24 hours).
- Compaction is not immediate. It runs in the background on older segments. The latest records can still contain older values for a while.
- Records must have keys.

```text
Before compaction:  A=1  B=1  A=2  C=1  A=3  B=null
After compaction:   C=1  A=3  (B deleted by tombstone)
Offsets of kept records do not change.
```

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-topics.sh \
    --create \
    --topic product-prices \
    --bootstrap-server localhost:9092 \
    --partitions 1 \
    --replication-factor 1 \
    --config cleanup.policy=compact
# Creates a compacted topic
```

**Follow-up questions:**

- **Can you use both delete and compact?**
  Yes: `cleanup.policy=compact,delete`. Old data is removed by time and the rest is compacted.
- **Does Kafka itself use compaction?**
  Yes. `__consumer_offsets` is compacted (latest offset per group and partition).
- **Is order-events a good compaction candidate?**
  No. Each event matters as history. Compaction suits state snapshots.

---

### Q10. How do you choose the number of partitions? [Intermediate]

**Level: Intermediate**

**Short answer:** Divide the target throughput by what one partition can handle, on both the producer and consumer side, and take the larger number. Then add headroom, because the count is hard to change later.

**Explanation:**

Rule of thumb:

```text
partitions >= max( target throughput / producer throughput per partition,
                   target throughput / consumer throughput per partition )

Example:
  Target:                       1000 orders/sec
  One Inventory consumer does:   250 orders/sec
  Needed: 1000 / 250 = 4 partitions (and 4 consumers)
  With headroom for growth: 6 to 8 partitions
```

Also consider:

- **Max consumers in a group** equals the partition count.
- **Key ordering:** the count affects key-to-partition mapping. Pick a number you will not change (Q11).
- **Too many partitions cost something:** more open files, more memory, longer leader election after a broker failure, more replication traffic, more end-to-end latency.
- Many teams choose a number divisible by likely consumer counts, such as 6 or 12.
- Measure with `kafka-producer-perf-test.sh` and your real consumer logic.

Our setup: 1 partition is fine for learning. Production Order-Service: start with several (for example 6) with RF 3.

**Follow-up questions:**

- **Can you have too many partitions?**
  Yes. Thousands per broker can slow failover and use heavy resources. Avoid creating a partition per customer.
- **Is the number of partitions per topic or per broker?**
  Per topic is set by you. Total per broker is a capacity limit to watch.
- **Do more partitions always give more throughput?**
  No. The limit moves to the broker, disk, network or the downstream database.

---

### Q11. What happens when you add partitions to a topic? [Intermediate]

**Level: Intermediate**

**Short answer:** You can increase partitions but never decrease them. Adding partitions changes where new records with a given key go, so per-key ordering across old and new records can break.

**Explanation:**

- Partition = hash(key) mod partition count. When the count changes, the same key can map to a different partition.
- Old records stay where they were. New records for key "customer-A" may go to a new partition while the old ones are in the previous one. Two consumers may then process events for the same key at the same time or out of order.
- Adding partitions also triggers a consumer group rebalance.

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-topics.sh \
    --alter \
    --topic order-events \
    --bootstrap-server localhost:9092 \
    --partitions 6
# Increases from (for example) 3 to 6. Cannot go down.
```

Safer approach for keyed topics: create a new topic with the new count and migrate producers and consumers. Or plan ahead with more partitions than you need now.

**Follow-up questions:**

- **How do you reduce partitions?**
  Create a new topic with fewer partitions and move data to it. You cannot shrink in place.
- **Is this a problem for unkeyed topics?**
  No. There is no key ordering to break.
- **How do you avoid the problem?**
  Over-provision partitions at the start.

---

### Q12. What is a hot partition and how do you fix it? [Advanced]

**Level: Advanced**

**Short answer:** A hot partition gets much more traffic than the others, usually because one key is very frequent. One consumer becomes the bottleneck while the others are idle.

**Explanation:**

- Cause: skewed keys, for example one very large customer, or a null/default key value used for many records.
- Effects: high lag on one partition, one broker with more load, scaling the consumer group does not help.
- Fixes:
  - Use a better key with more variety (orderId instead of customerId, if per-customer ordering is not required).
  - **Salting:** key = `customerId + "-" + (random 0..N)` spreads one customer over N partitions. You lose strict per-customer order.
  - A custom partitioner for known heavy keys.
  - Check that you are not sending everything with an empty-string key.

```java
String key = event.customerId() + "-" + ThreadLocalRandom.current().nextInt(4);
kafkaTemplate.send("order-events", key, event);
// A hot customer is spread over 4 partitions. Per-customer order is no longer guaranteed.
```

**Follow-up questions:**

- **How do you detect it?**
  Per-partition lag and per-partition message rate. `kafka-consumer-groups.sh --describe` shows uneven lag.
- **Does adding partitions help?**
  Not if one key dominates. The same key still maps to a single partition.

# Producers and Consumers

---

### Q13. How do producers and consumers work? [Basic]

**Level: Basic**

**Short answer:** A producer sends records to a topic. Kafka picks the partition (from the key or by its default strategy), stores the record and returns an acknowledgment. A consumer polls for records, processes them and commits its offset.

**Explanation:**

Producer steps:

1. Connect to a broker using `bootstrap.servers`.
2. Send a record to a topic, with an optional key.
3. The producer chooses the partition and groups records into batches.
4. The leader broker of that partition stores the batch.
5. The broker sends an acknowledgment (see Q27).

Consumer steps:

1. Connect and subscribe to a topic with a `group.id`.
2. Kafka assigns partitions to the consumer.
3. The consumer calls `poll()` in a loop. This is a **pull** model: the consumer asks for data.
4. It processes the records.
5. It **commits the offset** (tells Kafka "I finished up to here"). The next poll starts after that offset.

```text
Producer --> Leader broker (partition 1) --> ack --> Producer

Consumer --poll()--> Leader broker --records 5,6,7--> Consumer
Consumer processes 5,6,7 --commit offset 8--> Kafka
```

**Follow-up questions:**

- **Does Kafka push messages to consumers?**
  No. Consumers pull. This lets each consumer control its own speed.
- **Where are committed offsets stored?**
  In an internal topic called `__consumer_offsets`.
- **What if there is no committed offset yet?**
  `auto.offset.reset` decides. `latest` (default) starts from new messages. `earliest` starts from the oldest retained message.

---

### Q14. What is a consumer group? [Basic]

**Level: Basic**

**Short answer:** A consumer group is a set of consumers with the same `group.id`. Kafka divides the topic's partitions among them. Each partition is read by only one consumer in the group.

**Explanation:**

- This lets you scale reading: add more Inventory-Service instances, and each takes some partitions.
- Different groups are independent. Each group has its own offsets and reads every message.
- Max parallelism in a group = number of partitions. Extra consumers sit idle.
- If a consumer dies or joins, partitions are moved between consumers. This is a **rebalance** (Q17).

```text
Topic order-events: P0, P1, P2

Group "inventory-service-group":
  Consumer 1 <- P0
  Consumer 2 <- P1
  Consumer 3 <- P2

Group "notification-service-group":
  Consumer A <- P0, P1, P2   (reads ALL messages, own offsets)
```

If the group has 4 consumers and the topic has 3 partitions, one consumer gets nothing.

**Follow-up questions:**

- **Can two consumers in the same group read the same partition?**
  No. At any moment one partition goes to one consumer in the group.
- **How do you make every instance receive every message?**
  Give each instance a different `group.id`. This is rare and usually means a design smell.
- **What is the difference between a consumer group and a queue?**
  A group splits messages between its members like a queue. Several groups together act like publish/subscribe.

---

### Q15. How do partition keys work and what ordering does Kafka guarantee? [Basic]

**Level: Basic**

**Short answer:** Kafka guarantees order only inside one partition. Records with the same key go to the same partition, so they stay in order. There is no ordering across partitions.

**Explanation:**

- The default partitioner hashes the key (murmur2) and takes it modulo the partition count. Same key gives the same partition, as long as the partition count does not change.
- With no key, the producer spreads records over partitions. The default does not do strict round-robin. It fills a batch for one partition and then switches (sticky behavior, version-specific: Kafka 2.4+).
- To keep all events of one order in order, use `orderId` as the key. To keep all events of a customer in order, use `customerId`.
- Ordering also depends on the producer settings (Q23) and on the consumer processing a partition on one thread.

```text
key = "order-42" -> hash -> Partition 1
  order-42 CREATED  (offset 0)
  order-42 PAID     (offset 1)
  order-42 SHIPPED  (offset 2)
Consumer reads them in this order.

Events for different orders may be on P0, P1, P2.
Their relative order across partitions is NOT guaranteed.
```

```java
kafkaTemplate.send(
        "order-events",
        order.getId().toString(),
        event);
// Key = order id. All events of this order go to the same partition.
```

**Follow-up questions:**

- **What if you need a global order across the whole topic?**
  Use one partition. That limits throughput to one consumer. Usually you redesign so that only per-key order matters.
- **What happens to key ordering if you add partitions?**
  The key to partition mapping changes (see Q11).
- **Is a null key allowed?**
  Yes. Then no key-based ordering exists.

---

### Q16. Auto commit vs manual commit of offsets? [Intermediate]

**Level: Intermediate**

**Short answer:** With auto commit the consumer commits offsets on a timer, which can lose or repeat messages. With manual commit your code commits after processing, which gives at-least-once control.

**Explanation:**

- Plain Kafka client: `enable.auto.commit=true` by default, with `auto.commit.interval.ms=5000`. Offsets are committed during `poll()` for records returned earlier.
- Problem: if a record is polled and the timer commits it, then the app crashes during processing, the record is lost (at-most-once). If it commits late, the record repeats.
- Manual: call `commitSync()` (blocking, retries) or `commitAsync()` (non-blocking, no retry) after processing succeeds.
- Spring Kafka sets `enable.auto.commit=false` and commits itself according to `AckMode`. Default is `BATCH`: commit after all records of a poll are processed. `RECORD` commits after each record. `MANUAL` and `MANUAL_IMMEDIATE` let you call `Acknowledgment.acknowledge()`.

```yaml
spring:
  kafka:
    consumer:
      group-id: inventory-service-group
      enable-auto-commit: false
    listener:
      ack-mode: manual
```

```java
@KafkaListener(topics = "order-events")
public void onOrder(
        OrderEvent event,
        Acknowledgment ack) {
    inventoryService.reserve(event);
    ack.acknowledge();
    // Offset is committed only after the work succeeded
}
```

**Follow-up questions:**

- **Does the committed offset point to the last processed message or the next one?**
  The next one. Committing "offset 8" means messages up to 7 are done.
- **What does `commitSync` vs `commitAsync` trade off?**
  Sync is safer but blocks. Async is faster but a failed commit is not retried, so a later commit usually covers it.
- **How do you reduce duplicates with manual commit?**
  Commit often, keep processing idempotent, and commit on rebalance (Spring does this through the container).

---

### Q17. What is a rebalance and why can it be a problem? [Intermediate]

**Level: Intermediate**

**Short answer:** A rebalance is Kafka reassigning partitions among the consumers of a group. It happens when a consumer joins, leaves, crashes or is too slow. During a classic (eager) rebalance, consumption pauses.

**Explanation:**

Triggers:

- A consumer starts or stops (deploy, scale up/down).
- A consumer misses heartbeats for `session.timeout.ms`.
- A consumer does not call `poll()` within `max.poll.interval.ms`.
- The topic's partition count changes or subscription changes.

How it works (classic protocol): one broker acts as the group **coordinator**. Consumers send heartbeats to it. When membership changes, the coordinator starts a rebalance and one consumer computes the new assignment.

Problems:

- With the eager protocol all consumers give up all partitions and wait ("stop the world").
- Rolling deployments cause repeated rebalances.
- Processing that was in progress can be repeated by the new owner (duplicates).

```text
Before: C1:[P0,P1]  C2:[P2,P3]
C2 crashes -> rebalance (all consumption pauses)
After:  C1:[P0,P1,P2,P3]
```

See Q24 for how to reduce the cost.

**Follow-up questions:**

- **How do you see rebalances?**
  Consumer logs ("Revoke previously assigned partitions", "Adding newly assigned partitions") and the metric `rebalance-total`.
- **Which assignment strategies exist?**
  Range, RoundRobin, Sticky and CooperativeSticky. Default is `RangeAssignor`, `CooperativeStickyAssignor` (version-specific: Kafka 3.0+).
- **What is the difference between session timeout and poll interval?**
  Session timeout detects a dead process through missing heartbeats. Poll interval detects a stuck or slow processing loop (Q21).

---

### Q18. How do you use Kafka with Spring Boot? [Intermediate]

**Level: Intermediate**

**Short answer:** Add `spring-kafka`, configure `spring.kafka.*` properties, send with `KafkaTemplate` and receive with `@KafkaListener`.

**Explanation:**

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer
      acks: all
    consumer:
      group-id: inventory-service-group
      auto-offset-reset: earliest
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer.ErrorHandlingDeserializer
      properties:
        spring.deserializer.value.delegate.class: org.springframework.kafka.support.serializer.JsonDeserializer
        spring.json.trusted.packages: com.example.events
```

Producer (Order-Service):

```java
@Service
public class OrderEventPublisher {

    private final KafkaTemplate<String, OrderEvent> kafkaTemplate;

    public OrderEventPublisher(KafkaTemplate<String, OrderEvent> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    public void publish(OrderEvent event) {
        kafkaTemplate
                .send("order-events", event.orderId(), event)
                .whenComplete((result, ex) -> {
                    if (ex != null) {
                        // log and handle the failure
                    }
                });
        // send() is asynchronous. The result arrives in whenComplete.
    }
}
```

Consumer (Inventory-Service):

```java
@Component
public class OrderEventListener {

    @KafkaListener(
            topics = "order-events",
            groupId = "inventory-service-group")
    public void onOrder(OrderEvent event) {
        // reduce stock here
    }
}
```

Version note: in Spring Kafka 3.x, `send()` returns `CompletableFuture<SendResult>`. In 2.x it returned `ListenableFuture`.

**Follow-up questions:**

- **How do you get more parallelism in a listener?**
  Set `concurrency` on `@KafkaListener` (or `spring.kafka.listener.concurrency`). It creates that many consumer threads in one instance. Useful up to the partition count across all instances.
- **Is `KafkaTemplate.send()` blocking?**
  No. It returns a future. Calling `.get()` makes it blocking, which lowers throughput.
- **Why use `ErrorHandlingDeserializer`?**
  Without it a bad message throws during deserialization and the consumer loops on the same record forever (see Q35).

---

### Q19. How do serialization and schemas work? [Intermediate]

**Level: Intermediate**

**Short answer:** Kafka stores bytes. Producers serialize objects to bytes and consumers deserialize them. For shared contracts between teams, use a schema format such as Avro or Protobuf with a Schema Registry that enforces compatibility.

**Explanation:**

- Formats: JSON (readable, large, no enforced schema), Avro and Protobuf (compact, schema-based), plain strings.
- **Schema Registry** (Confluent and compatible products) stores schemas, gives each an ID, and checks that new versions are compatible. The message carries the schema ID.
- Compatibility modes: **backward** (new consumers read old data), **forward** (old consumers read new data), **full** (both).
- Safe changes: add an optional field with a default. Risky: remove a field, rename a field, change a type.
- For plain JSON with Spring, use a versioned event class and make consumers ignore unknown fields.

```java
@JsonIgnoreProperties(ignoreUnknown = true)
public record OrderEvent(
        String eventId,
        String orderId,
        int quantity) {
}
// Unknown new fields from a newer producer do not break this consumer
```

**Follow-up questions:**

- **Why not just use JSON everywhere?**
  It works for small teams. Without a schema, a producer change can silently break consumers.
- **What happens if a consumer cannot deserialize a record?**
  With a plain deserializer the consumer fails on that record repeatedly. Use `ErrorHandlingDeserializer` and a DLT (Q30, Q35).

---

### Q20. How do you tune producer throughput? [Intermediate]

**Level: Intermediate**

**Short answer:** Send fewer, larger requests. Use batching (`batch.size`, `linger.ms`) and compression. Keep safety settings (`acks=all`, idempotence) unless loss is acceptable.

**Explanation:**

| Setting | What it does | Typical change |
|---|---|---|
| `batch.size` | Max bytes of records collected per partition before sending. Default 16 KB | Raise to 64 KB or more for high volume |
| `linger.ms` | How long the producer waits to fill a batch. Default 0 ms before Kafka 4.0, 5 ms from 4.0 (version-specific) | 5 to 50 ms increases batching |
| `compression.type` | Compresses a whole batch: `none`, `gzip`, `snappy`, `lz4`, `zstd` | `lz4` or `zstd` for a good speed/size balance |
| `buffer.memory` | Memory for records waiting to be sent. Default 32 MB | Raise if the producer blocks |
| `max.in.flight.requests.per.connection` | Unacknowledged requests per connection. Default 5 | Keep at 5 or less with idempotence |

```yaml
spring:
  kafka:
    producer:
      acks: all
      batch-size: 65536
      compression-type: lz4
      properties:
        linger.ms: 20
        enable.idempotence: true
# Trades up to 20 ms latency for larger batches and less network use
```

Consumer side: raise `fetch.min.bytes`, `max.poll.records` and use more partitions or `concurrency`, but keep processing within `max.poll.interval.ms` (Q21).

**Follow-up questions:**

- **Why does compression help even though it uses CPU?**
  Larger batches compress well, so network and disk I/O drop. The broker stores and serves the batch as it is.
- **Throughput vs latency trade-off?**
  Higher `linger.ms` and batch size raise throughput and also add latency.
- **What is the first thing to measure?**
  Where the bottleneck is: producer, network, broker disk, or consumer processing.

---

### Q21. Which consumer timeout settings matter? [Intermediate]

**Level: Intermediate**

**Short answer:** `session.timeout.ms` and `heartbeat.interval.ms` detect a dead consumer. `max.poll.interval.ms` and `max.poll.records` control how long processing may take. Getting these wrong causes unwanted rebalances.

| Setting | What it does | Default |
|---|---|---|
| `heartbeat.interval.ms` | How often the consumer sends a heartbeat | 3000 ms |
| `session.timeout.ms` | Time without heartbeat before the consumer is considered dead | 45000 ms (Kafka 3.0+; 10000 before) |
| `max.poll.interval.ms` | Max time between two `poll()` calls before the consumer is removed from the group | 300000 ms (5 min) |
| `max.poll.records` | Max records returned by one `poll()` | 500 |

- Heartbeats run on a background thread. They continue while your code processes a batch.
- If processing 500 records takes longer than `max.poll.interval.ms`, the consumer is kicked out, a rebalance happens, and the records are processed again by another consumer.
- Fix: lower `max.poll.records`, speed up processing, or raise `max.poll.interval.ms`.

```yaml
spring:
  kafka:
    consumer:
      max-poll-records: 100
      properties:
        max.poll.interval.ms: 600000
        session.timeout.ms: 30000
        heartbeat.interval.ms: 10000
# Rule: heartbeat.interval.ms is about 1/3 of session.timeout.ms
```

**Follow-up questions:**

- **Why does a consumer "keep rejoining" with logs about poll interval exceeded?**
  Its batch takes too long. Reduce batch size or processing time.
- **Should you just raise session timeout very high?**
  A high value slows detection of really dead consumers, so partitions stay unassigned longer.

---

### Q22. What is an idempotent producer? [Advanced]

**Level: Advanced**

**Short answer:** An idempotent producer makes sure retries do not create duplicate records in a partition. The broker detects and drops a repeated batch using a producer ID and sequence numbers.

**Explanation:**

- Problem: the producer sends a batch, the broker stores it, the ack is lost, the producer retries, and the record is now stored twice.
- With `enable.idempotence=true`, the producer gets a **producer ID (PID)**. Each batch per partition carries a **sequence number**. The broker keeps the latest sequence per PID and partition. A batch it has already seen is acknowledged but not written again.
- Requirements: `acks=all`, `retries > 0`, `max.in.flight.requests.per.connection <= 5`.
- Version-specific: enabled by default since Kafka 3.0.
- Scope: one producer session and one partition. If the producer process restarts, it gets a new PID, so duplicates across restarts are not prevented. Transactions with a `transactional.id` solve that (Q32).

```text
Without idempotence:
Producer -> batch A -> Broker (stored)  ack lost
Producer -> batch A (retry) -> Broker (stored again)   DUPLICATE

With idempotence:
Producer -> batch A (PID 7, seq 0) -> stored
Producer -> batch A (PID 7, seq 0) retry -> broker sees seq 0 already stored -> ack only
```

```yaml
spring:
  kafka:
    producer:
      acks: all
      properties:
        enable.idempotence: true
        max.in.flight.requests.per.connection: 5
```

**Follow-up questions:**

- **Does it make the whole pipeline exactly-once?**
  No. It prevents duplicates from producer retries only. Consumer-side duplicates still need handling.
- **Does it preserve ordering with retries?**
  Yes, up to 5 in-flight requests (Q23).
- **Does it work across two different producers?**
  No. Each has its own PID.

---

### Q23. How do retries affect ordering? [Advanced]

**Level: Advanced**

**Short answer:** Without idempotence, a failed batch that is retried can land after a later batch, so order breaks when `max.in.flight.requests.per.connection` is above 1. With idempotence on, order is kept for up to 5 in-flight requests.

**Explanation:**

- In-flight requests are requests sent but not yet acknowledged.
- Without idempotence: batch 1 fails, batch 2 succeeds, batch 1 is retried and is now after batch 2.
- The old fix was `max.in.flight.requests.per.connection=1` (slow).
- With `enable.idempotence=true`, sequence numbers let the broker reject out-of-order batches, so the producer retries in the correct order.
- Consumer side: ordering is kept only if one thread processes a partition in sequence. Do not hand records of the same key to parallel worker threads without care.
- Non-blocking retry topics (Q30) also reorder events for a key.

```text
Without idempotence, max.in.flight=5:
send B1, B2   B1 fails, B2 ok
retry B1  -> log order: B2, B1   (WRONG ORDER)

With idempotence:
broker expects seq 0 first, rejects out-of-order, producer resends in order
log order: B1, B2
```

**Follow-up questions:**

- **How do you guarantee strict order in older Kafka versions?**
  Set `max.in.flight.requests.per.connection=1`.
- **Can multithreading in a listener break order?**
  Yes. If you send records of one partition to a thread pool, they finish in any order.
- **Does `delivery.timeout.ms` matter?**
  It is the total time for a send including retries. After it, the send fails and the app must decide what to do.

---

### Q24. How can you reduce the cost of rebalances? [Advanced]

**Level: Advanced**

**Short answer:** Use cooperative rebalancing so only the moved partitions pause, use static membership so restarts do not trigger a rebalance, and keep processing time within `max.poll.interval.ms`.

**Explanation:**

- **Eager rebalance:** every consumer drops all partitions, then receives new ones.
- **Cooperative (incremental) rebalance:** consumers keep partitions that do not move. Use `CooperativeStickyAssignor` (version-specific: default in the assignor list since Kafka 3.0).
- **Static membership:** set `group.instance.id` for each consumer. If it restarts and comes back within `session.timeout.ms`, it gets the same partitions with no rebalance. Good for rolling deployments and Kubernetes StatefulSets.
- **Sticky assignment:** keeps old assignments as much as possible, helpful for stateful consumers.
- Version-specific: Kafka 4.0 makes the new consumer rebalance protocol (KIP-848) generally available. Assignment moves to the broker and rebalances are lighter. Enable it with `group.protocol=consumer`.

```yaml
spring:
  kafka:
    consumer:
      properties:
        partition.assignment.strategy: org.apache.kafka.clients.consumer.CooperativeStickyAssignor
        group.instance.id: inventory-1
        session.timeout.ms: 60000
# Each instance needs its own unique group.instance.id
```

**Follow-up questions:**

- **Why does a rolling deployment cause many rebalances?**
  Each restarted instance leaves and rejoins, causing two rebalances per instance.
- **Risk of static membership?**
  A dead instance holds its partitions until the session timeout, so recovery is slower.
- **How do you pick `group.instance.id` in Kubernetes?**
  Use the pod name from a StatefulSet.

---

### Q25. What are Kafka Streams and Kafka Connect? [Advanced]

**Level: Advanced**

**Short answer:** Kafka Streams is a Java library for processing topics (filter, join, aggregate) inside your own application. Kafka Connect is a framework to move data between Kafka and other systems using ready-made connectors.

**Explanation:**

| Tool | What it is | Example |
|---|---|---|
| Kafka Streams | A client library, not a separate cluster. Your app reads topics, transforms them, and writes to topics. Supports state stores, windows and joins | Count orders per product per minute |
| Kafka Connect | A runtime that runs **source connectors** (into Kafka) and **sink connectors** (out of Kafka) with configuration, not code | Debezium reads DB changes into Kafka. A JDBC sink writes a topic into a table |

- Streams scales by running more instances of the app. Parallelism is limited by input partitions. It can give exactly-once processing with `processing.guarantee=exactly_once_v2`.
- Connect is useful for CDC and the outbox pattern (Q38) and avoids custom copy code.

```java
StreamsBuilder builder = new StreamsBuilder();
builder.<String, OrderEvent>stream("order-events")
        .filter((key, event) -> event.quantity() > 100)
        .to("large-orders");
// Reads order-events, keeps large orders, writes them to another topic
```

**Follow-up questions:**

- **Streams vs a plain consumer?**
  Streams adds stateful operations, fault-tolerant local state and exactly-once support. A plain consumer is simpler for basic handlers.
- **Alternatives?**
  Apache Flink or Spark Structured Streaming for larger processing jobs.

# Reliability and Delivery

---

### Q26. What is replication, and what are leaders and followers? [Basic]

**Level: Basic**

**Short answer:** Replication keeps copies of each partition on different brokers. One copy is the leader and handles all reads and writes. The others are followers and only copy from the leader. If the leader dies, a follower becomes the new leader.

**Explanation:**

- **Replication factor (RF):** total number of copies, including the leader. RF 3 means 1 leader + 2 followers.
- Copies must be on different brokers. Two copies on one broker would both die together.
- Production standard is RF 3. It survives the loss of 2 brokers for data safety (with the right settings, see Q33).
- Our single-broker learning setup uses RF 1: no backup. This is why internal topics need `KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1` in the compose file.

```text
Topic order-events, Partition 0, RF = 3

Broker 1: P0 LEADER   <- producers write here, consumers read here
Broker 2: P0 follower <- copies from leader
Broker 3: P0 follower <- copies from leader

Broker 1 dies -> Broker 2 becomes LEADER
```

**Follow-up questions:**

- **Can consumers read from followers?**
  By default no. Since Kafka 2.4 (KIP-392) a consumer can fetch from the closest replica if configured with `client.rack` and a rack-aware replica selector. This is mainly used to reduce cross-zone network cost.
- **Does replication improve read throughput?**
  Not by default. Its main purpose is fault tolerance.
- **What happened in our single-broker setup when `__consumer_offsets` was created?**
  It defaulted to RF 3, which is impossible with one broker. The broker could not create the topic (INVALID_REPLICATION_FACTOR in the broker log), and consumers got errors like COORDINATOR_NOT_AVAILABLE until we set RF 1.

---

### Q27. What do the producer acks settings mean? [Intermediate]

**Level: Intermediate**

**Short answer:** `acks` controls when the producer treats a write as successful. `0` means do not wait, `1` means wait for the leader, `all` means wait for all in-sync replicas.

| Setting | What it does | Risk |
|---|---|---|
| `acks=0` | Producer does not wait for any response | Message can be lost with no error |
| `acks=1` | Waits until the leader has written it | Lost if the leader dies before followers copy it |
| `acks=all` (or `-1`) | Waits until all replicas in the ISR have it | Safest. Higher latency |

- With `acks=all`, the number of copies required is also limited by `min.insync.replicas` (Q28).
- Version-specific: since Kafka 3.0 the producer default is `acks=all` (with idempotence on). Before 3.0 the default was `acks=1`.
- In our single-broker setup the ISR is just the leader, so `acks=all` behaves like `acks=1`.

```yaml
spring:
  kafka:
    producer:
      acks: all
      retries: 2147483647
```

**Follow-up questions:**

- **Why can `acks=1` lose data?**
  The leader acknowledges, then crashes before followers fetch the message. A follower becomes leader without it.
- **Does `acks=all` mean every replica?**
  It means every replica currently in the ISR, not every replica assigned. That is why `min.insync.replicas` matters.
- **When is `acks=0` acceptable?**
  Rarely: metrics or logs where loss does not matter and latency does.

---

### Q28. What is min.insync.replicas? [Intermediate]

**Level: Intermediate**

**Short answer:** `min.insync.replicas` is the minimum number of ISR members (leader included) that must exist for an `acks=all` write to succeed. If fewer are in sync, the broker rejects the write.

**Explanation:**

- It is a topic or broker setting. Default is 1.
- It only affects producers using `acks=all`.
- Standard production choice: RF 3, `min.insync.replicas=2`, `acks=all`. This tolerates one broker down while still writing, and keeps at least 2 copies of every acknowledged message.
- Trade-off: safety over availability. If two brokers are down, writes fail with `NotEnoughReplicasException`. Reads still work.

```text
RF=3, min.insync.replicas=2, acks=all

ISR = {1,2,3}  -> write OK (3 copies)
ISR = {1,2}    -> write OK (2 copies)
ISR = {1}      -> write REJECTED (NotEnoughReplicas). No unsafe write.
```

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-configs.sh \
    --bootstrap-server localhost:9092 \
    --alter \
    --entity-type topics \
    --entity-name order-events \
    --add-config min.insync.replicas=2
# Applies min.insync.replicas to one topic
```

**Follow-up questions:**

- **Why not set `min.insync.replicas` equal to RF?**
  Then losing one broker blocks all writes. Use RF - 1.
- **What does the producer do on this error?**
  The error is retriable. The producer retries until `delivery.timeout.ms`, then fails the send.
- **Does this work on our single broker?**
  Only with value 1. A value of 2 would reject every write.

---

### Q29. What are the delivery semantics? [Intermediate]

**Level: Intermediate**

**Short answer:** At-most-once means a message may be lost but never repeated. At-least-once means never lost but may be repeated. Exactly-once means processed once, which Kafka supports only within Kafka itself using transactions.

| Semantic | Message lost? | Duplicates? | How it happens |
|---|---|---|---|
| At-most-once | Possible | No | Commit offset BEFORE processing. Or `acks=0` |
| At-least-once | No | Possible | Commit offset AFTER processing. Producer retries with `acks=all` |
| Exactly-once | No | No | Idempotent producer + transactions + `read_committed` consumers |

```text
At-most-once:   poll -> commit -> process (crash here = message lost)
At-least-once:  poll -> process -> commit (crash here = message processed again)
```

- At-least-once is the common default. You handle duplicates with idempotent processing (Q31).
- Exactly-once covers read from Kafka, process, write to Kafka. A side effect outside Kafka (database, email, REST call) is not covered (Q32).

**Follow-up questions:**

- **Which one would you use for Order to Inventory?**
  At-least-once with an idempotent consumer. This gives effectively-once results at lower cost than transactions.
- **Where can duplicates come from?**
  Producer retry after a lost ack, consumer crash after processing but before commit, and rebalance during processing.
- **Is exactly-once free?**
  No. It adds latency and complexity and only covers Kafka-to-Kafka flows.

---

### Q30. How do retries and dead-letter topics work in Spring Kafka? [Intermediate]

**Level: Intermediate**

**Short answer:** When a listener throws, Spring Kafka's `DefaultErrorHandler` retries with a back-off. If retries run out, a `DeadLetterPublishingRecoverer` sends the record to a dead-letter topic (DLT) so the partition can move on.

**Explanation:**

- A **dead-letter topic** is a separate topic that stores messages that could not be processed. You inspect them, fix the cause, and replay them.
- Default `DefaultErrorHandler` behavior: retry in place with no delay, 9 retries (10 attempts total), then log and skip. Configure it to do better.
- **Blocking retry:** retries happen on the same thread. The partition waits. Good for short transient errors.
- **Non-blocking retry:** `@RetryableTopic` sends the failed record to retry topics with delays, so the main topic keeps flowing. Note that this changes ordering for the retried record.

Blocking retry with DLT:

```java
@Bean
public DefaultErrorHandler errorHandler(KafkaTemplate<Object, Object> template) {
    DeadLetterPublishingRecoverer recoverer =
            new DeadLetterPublishingRecoverer(template);
    // Default DLT name: <original-topic>.DLT

    DefaultErrorHandler handler = new DefaultErrorHandler(
            recoverer,
            new FixedBackOff(1000L, 3L));
    // 1 second between tries, 3 retries

    handler.addNotRetryableExceptions(
            IllegalArgumentException.class);
    // Bad data will never succeed, so do not retry it
    return handler;
}
```

Non-blocking retry topics:

```java
@RetryableTopic(
        attempts = "4",
        backoff = @Backoff(delay = 2000, multiplier = 2.0))
@KafkaListener(topics = "order-events")
public void onOrder(OrderEvent event) {
    inventoryService.reserve(event);
}

@DltHandler
public void onDead(OrderEvent event) {
    // alert, store for manual review
}
// Creates topics such as order-events-retry-2000, order-events-retry-4000 and order-events-dlt
// (by default the retry topic suffix is the delay value in ms)
```

**Follow-up questions:**

- **Which errors should be retried?**
  Transient ones: timeouts, deadlocks, a service briefly down. Do not retry validation or deserialization errors.
- **What is the drawback of blocking retries?**
  All messages behind the failing one in that partition wait. Long back-offs can exceed `max.poll.interval.ms` and trigger a rebalance.
- **Do you lose ordering with `@RetryableTopic`?**
  Yes for the failed record. Later records of the same key may be processed first.
- **What do you do with DLT messages?**
  Alert, find the cause, fix, then replay to the original topic.

---

### Q31. How do you handle duplicate messages? [Intermediate]

**Level: Intermediate**

**Short answer:** Make the consumer idempotent: processing the same message twice must give the same result as once. The usual way is to store processed message IDs, or to use database constraints.

**Explanation:**

Duplicates are normal with at-least-once delivery (Q29). Techniques:

1. **Processed-event table.** Save the event ID in the same database transaction as the business change. A unique constraint rejects the second attempt.
2. **Natural idempotency.** "Set status to SHIPPED" is safe to repeat. "Subtract 5 from stock" is not.
3. **Optimistic locking or version numbers.** Ignore events older than the stored version.
4. **Upsert.** Insert or update by business key.

```java
@Transactional
public void handle(OrderEvent event) {
    if (processedEventRepository.existsById(event.eventId())) {
        return;
        // Already processed. Skip safely.
    }
    inventoryService.reserve(event);
    processedEventRepository.save(new ProcessedEvent(event.eventId()));
    // Same transaction: both happen or neither
}
```

```sql
CREATE TABLE processed_event (
    event_id VARCHAR(64) PRIMARY KEY,
    processed_at TIMESTAMP NOT NULL
);
-- The primary key makes a second insert fail
```

**Follow-up questions:**

- **Where does the event ID come from?**
  The producer creates a UUID or uses a business key such as orderId plus event type. Do not use topic-partition-offset if the producer may send the same event twice.
- **How long do you keep processed IDs?**
  At least as long as duplicates can arrive (retention time plus retry window). Clean up with a scheduled job.
- **Why not rely on exactly-once instead?**
  It does not cover database writes (Q32). Idempotent consumers work for any side effect.

---

### Q32. How do Kafka transactions and exactly-once work? [Advanced]

**Level: Advanced**

**Short answer:** Kafka transactions let a producer write to several partitions and commit consumer offsets as one atomic unit. Consumers with `isolation.level=read_committed` see only committed data. It gives exactly-once for a Kafka-to-Kafka flow, not for external systems.

**Explanation:**

- Set `transactional.id`. This enables idempotence and a stable identity across restarts (zombie producers with the same ID are fenced out).
- Pattern "consume, process, produce": read from topic A, write results to topic B and commit the offsets of A in the same transaction. Either all succeed or none are visible.
- Aborted records stay in the log but `read_committed` consumers skip them. Default is `read_uncommitted`.
- Cost: extra latency, a transaction coordinator and a transaction state log (`__transaction_state`). This is why our single-broker setup sets `KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1` and `..._MIN_ISR: 1`.
- Limit: a database update, email or REST call is not part of the Kafka transaction. A crash after the DB commit but before the Kafka commit repeats the work.

```yaml
spring:
  kafka:
    producer:
      transaction-id-prefix: order-tx-
    consumer:
      isolation-level: read_committed
      enable-auto-commit: false
```

```java
@Transactional
@KafkaListener(topics = "order-events")
public void process(OrderEvent event) {
    kafkaTemplate.send(
            "inventory-events",
            event.orderId(),
            new InventoryReserved(event.orderId()));
    // Offset commit and this send are in one Kafka transaction
}
```

**Follow-up questions:**

- **How do you get "exactly-once" with a database?**
  Use at-least-once plus an idempotent consumer (Q31), or write the offset with the data in the same DB transaction. The outbox pattern handles the producing side (Q38).
- **What are zombie producers?**
  Old instances still running after a failover. The `transactional.id` epoch lets the broker fence them off.
- **When is it worth using?**
  Stream processing pipelines where output goes to Kafka. For simple service-to-service events it is often too heavy.

---

### Q33. How do you configure Kafka for no data loss? [Advanced]

**Level: Advanced**

**Short answer:** Use replication factor 3, `min.insync.replicas=2`, producer `acks=all` with idempotence, disable unclean leader election, and commit consumer offsets only after processing.

**Explanation:**

Data loss can happen at three places:

| Place | Setting | Why |
|---|---|---|
| Topic | `replication.factor=3` | Keeps 3 copies on different brokers |
| Topic/broker | `min.insync.replicas=2` | At least 2 copies exist for each acknowledged write |
| Broker | `unclean.leader.election.enable=false` | Never promote an out-of-sync replica (default false) |
| Producer | `acks=all`, `enable.idempotence=true` | Wait for the ISR, and retry without duplicates |
| Producer | Check the send result (callback) | A failed send must not be ignored |
| Consumer | `enable.auto.commit=false`, commit after processing | A crash does not skip unprocessed records |
| Retention | Long enough for consumers to catch up | Old data is deleted by retention |

```text
Safe producer + RF 3 + minISR 2:
Write acknowledged => at least 2 brokers have it
1 broker dies      => data safe, writes continue
2 brokers die      => writes rejected (no unsafe write), data safe on the last broker
```

Also: do not rely on page cache only. Kafka relies on replication rather than `fsync` on every write, so replication is the real durability.

**Follow-up questions:**

- **What is the cost?**
  Higher write latency and lower availability when brokers are down.
- **Is our single-broker Docker setup safe?**
  No. RF 1 means one disk failure loses everything. It is for learning only.
- **Does this guarantee no duplicates?**
  No. It guarantees no loss. Duplicates need idempotence (Q22, Q31).

---

### Q34. What are the high watermark and unclean leader election? [Advanced]

**Level: Advanced**

**Short answer:** The high watermark (HW) is the highest offset that all ISR replicas have copied. Consumers can read only up to it. Unclean leader election lets an out-of-sync replica become leader when no ISR member is available, which can lose data.

**Explanation:**

- Followers fetch from the leader. The leader tracks how far each follower has copied. The smallest offset in the ISR is the HW.
- Consumers see only records below the HW. This means a consumer never reads a record that could vanish after a leader change.
- Each leader has a **leader epoch** (a counter that increases at each leader change). Followers use it to truncate any records that do not match the new leader's log.
- If all ISR members are gone:
  - `unclean.leader.election.enable=false` (default): the partition stays offline until an ISR member returns. Consistency over availability.
  - `true`: an out-of-sync replica becomes leader. The partition is available but records that it did not have are lost.

```text
Leader log:    0 1 2 3 4 5     (log end offset 6)
Follower A:    0 1 2 3 4       (copied up to 4)
Follower B:    0 1 2 3
High watermark = 4  (smallest among ISR copies)
Consumers can read offsets 0 to 3 (below HW) only.
```

**Follow-up questions:**

- **When would you enable unclean election?**
  For data where availability matters more than loss, such as metrics. Not for orders.
- **Why can a producer with `acks=1` lose data on leader change?**
  The leader acknowledged before followers copied the record. The new leader does not have it and the record is truncated.

---

### Q35. What is a poison pill message? [Advanced]

**Level: Advanced**

**Short answer:** A poison pill is a record that always fails processing, such as one with invalid data or an unreadable format. If you retry forever, the consumer is stuck on it and all later records in that partition are blocked.

**Explanation:**

- Types: deserialization failure (before your listener runs) and processing failure (bad data, bug).
- Handling:
  1. Wrap deserializers with `ErrorHandlingDeserializer` so a bad record becomes a catchable error rather than a loop.
  2. Mark non-retryable exceptions (validation) so they skip retries.
  3. Send the record to a dead-letter topic after limited retries (Q30).
  4. Alert on DLT traffic.
- Never "fix" it by skipping offsets manually without recording the record somewhere.

```java
DefaultErrorHandler handler = new DefaultErrorHandler(
        new DeadLetterPublishingRecoverer(template),
        new FixedBackOff(500L, 2L));
handler.addNotRetryableExceptions(
        DeserializationException.class,
        IllegalArgumentException.class);
// Bad records go straight to the DLT instead of blocking the partition
```

**Follow-up questions:**

- **How do you recover a stuck consumer fast in production?**
  Deploy a fix or an error handler. As a last resort, move the group's offset past the bad record with `--reset-offsets --shift-by 1` (group stopped), and save the record for analysis.
- **How do you prevent poison pills?**
  Schema validation at the producer and compatible schema changes (Q19).

# Operations and Patterns

---

### Q36. How do you run Kafka in Docker and use the CLI? [Basic]

**Level: Basic**

**Short answer:** Run the Kafka image with KRaft settings in docker-compose, then use the scripts in `/opt/kafka/bin` with `docker exec`.

**Explanation:**

Key compose settings for the single-broker learning setup:

```yaml
KAFKA_NODE_ID: 1
KAFKA_PROCESS_ROLES: broker,controller
KAFKA_LISTENERS: PLAINTEXT://localhost:9092,CONTROLLER://localhost:9093
KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
KAFKA_CONTROLLER_QUORUM_VOTERS: 1@localhost:9093
KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
# Last three lines: internal topics default to RF 3, which fails with one broker
```

Create a topic:

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-topics.sh \
    --create \
    --topic order-events \
    --bootstrap-server localhost:9092 \
    --partitions 3 \
    --replication-factor 1
# Creates a topic with 3 partitions and 1 copy of each
```

Describe it:

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-topics.sh \
    --describe \
    --topic order-events \
    --bootstrap-server localhost:9092
# Shows each partition, its leader broker, replicas and ISR
```

Send and read messages:

```bash
docker exec -it kafka-broker \
    /opt/kafka/bin/kafka-console-producer.sh \
    --topic order-events \
    --bootstrap-server localhost:9092 \
    --property parse.key=true \
    --property key.separator=:
# Type lines like  order-1:{"qty":5}  (key:value)

docker exec -it kafka-broker \
    /opt/kafka/bin/kafka-console-consumer.sh \
    --topic order-events \
    --bootstrap-server localhost:9092 \
    --group test-group \
    --from-beginning \
    --property print.key=true
# Reads all retained messages and prints the key
```

**Follow-up questions:**

- **Why does a client outside Docker fail to connect even though the broker runs?**
  The advertised listener is what the client is told to use. If it points to an address the client cannot reach, the connection fails after the first metadata call. Set `KAFKA_ADVERTISED_LISTENERS` to an address reachable by that client.
- **Why does the consumer show nothing while the producer works?**
  In our case `__consumer_offsets` could not be created with RF 3 on one broker. Check broker logs for INVALID_REPLICATION_FACTOR.

---

### Q37. What is consumer lag and how do you check it? [Intermediate]

**Level: Intermediate**

**Short answer:** Lag is the difference between the latest offset in a partition (log end offset) and the group's committed offset. It tells you how far behind a consumer is.

**Explanation:**

- Lag = log end offset - committed offset, per partition.
- A small steady lag is normal. Growing lag means consumers cannot keep up or are stuck.
- Also watch lag in time (how old the oldest unread message is) because 1000 messages may mean seconds or hours.

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-consumer-groups.sh \
    --bootstrap-server localhost:9092 \
    --describe \
    --group inventory-service-group
# Columns: TOPIC PARTITION CURRENT-OFFSET LOG-END-OFFSET LAG CONSUMER-ID
```

Reset offsets to replay (group must be stopped):

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-consumer-groups.sh \
    --bootstrap-server localhost:9092 \
    --group inventory-service-group \
    --topic order-events \
    --reset-offsets \
    --to-earliest \
    --execute
# Moves the group back to the oldest retained message
```

Production usually monitors lag with Prometheus (a Kafka exporter or JMX metrics) or Burrow, with alerts.

**Follow-up questions:**

- **What are the usual causes of growing lag?**
  Slow processing (database, external call), too few consumers or partitions, frequent rebalances, one stuck record, or a traffic spike.
- **Can you fix lag by adding consumers?**
  Only up to the partition count. Beyond that, extra consumers are idle.
- **Is lag zero always good?**
  Not by itself. Check that the consumer is alive: a dead consumer with no new traffic also shows zero growth.

---

### Q38. What is the outbox pattern and why is it needed? [Advanced]

**Level: Advanced**

**Short answer:** It solves the dual-write problem. Order-Service writes the order and an "outbox" event row in one database transaction. A separate process reads the outbox and publishes to Kafka. This means the event is sent if and only if the order was saved.

**Explanation:**

- Dual-write problem: saving to the database and sending to Kafka are two systems. If the DB commit works and Kafka send fails (or the service crashes between them), the order exists but no event is sent. The reverse is also possible.
- Outbox steps:
  1. In one DB transaction: insert the order and insert a row in `outbox_event`.
  2. A relay reads new rows and sends them to Kafka. Then it marks them as sent.
- Relay options: a polling job, or change data capture (CDC) with Debezium through Kafka Connect, which reads the database log.
- The relay can send twice (crash after send, before marking). So the relay gives at-least-once, and consumers must be idempotent (Q31).
- Use the same key (for example orderId) so order is kept.

```text
Order-Service DB transaction:
  INSERT orders ...
  INSERT outbox_event (id, aggregate_id, type, payload)
COMMIT
        |
        v
Relay (poller or Debezium CDC) --> Kafka topic order-events --> Inventory-Service
```

```java
@Transactional
public void placeOrder(Order order) {
    orderRepository.save(order);
    outboxRepository.save(
            new OutboxEvent(
                    UUID.randomUUID(),
                    order.getId(),
                    "OrderCreated",
                    toJson(order)));
    // Both rows commit together or not at all. No direct Kafka call here.
}
```

**Follow-up questions:**

- **Polling relay or CDC?**
  Polling is simple but adds DB load and delay. CDC is near real time and does not load the tables but needs extra infrastructure.
- **Why not use a Kafka transaction here?**
  A Kafka transaction cannot include the database write.
- **How do you clean the outbox table?**
  Delete or archive sent rows on a schedule.

---

### Q39. How do you implement a saga with Kafka? [Advanced]

**Level: Advanced**

**Short answer:** A saga is a series of local transactions across services, each publishing an event. If a step fails, earlier steps are undone with compensating actions. With Kafka it is usually done through events (choreography) or a central coordinator (orchestration).

**Explanation:**

- There is no distributed database transaction across microservices. A saga gives eventual consistency instead.
- **Choreography:** each service listens for events and reacts. No central controller. Simple for few steps, hard to follow when the flow grows.
- **Orchestration:** one orchestrator sends commands and tracks state. Easier to see and change the flow.
- Each step should use the outbox (Q38) and an idempotent consumer (Q31). Use the same key (orderId) so each order's events stay ordered.

```text
Choreography:
Order-Service: OrderCreated
  -> Inventory-Service: reserves stock -> StockReserved
  -> Payment-Service: charges -> PaymentCompleted
  -> Order-Service: marks order CONFIRMED

Failure path:
Payment-Service: PaymentFailed
  -> Inventory-Service: releases stock (compensation) -> StockReleased
  -> Order-Service: marks order CANCELLED
```

**Follow-up questions:**

- **What is a compensating action?**
  A new action that undoes the business effect of an earlier step, such as releasing reserved stock. It does not delete history.
- **What if a compensation fails?**
  Retry it (it must be idempotent). After repeated failure, send to a DLT and alert.
- **Choreography or orchestration?**
  Choreography for 2 to 3 steps. Orchestration for longer or branching flows.

---

### Q40. Consumer lag keeps growing in production. How do you debug it? [Advanced]

**Level: Advanced**

**Short answer:** Find out whether the cause is slow processing, too few consumers, rebalancing, a stuck record or a traffic spike. Check per-partition lag first, then consumer logs and downstream dependencies.

**Explanation:**

Step by step:

1. **Is it all partitions or one?**
   Run the describe command (Q37). One partition means a hot key (Q12) or a stuck record (Q35). All partitions means overall capacity.
2. **Are consumers alive and stable?**
   Check logs for rebalance messages and "max.poll.interval.ms exceeded". Frequent rebalances mean little real work gets done.
3. **Is processing slow?**
   Look at downstream calls: database, REST. Check thread dumps, DB connection pool and timeouts. Often the bottleneck is not Kafka.
4. **Are there enough consumers and partitions?**
   Consumers beyond the partition count do nothing. Add partitions (Q11 caveats) or raise `concurrency`.
5. **Is a retry loop blocking?**
   A failing record with blocking retries holds back its partition (Q30).
6. **Did the input rate jump?**
   Check the producer rate. A new feature or batch job may flood the topic.

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-consumer-groups.sh \
    --bootstrap-server localhost:9092 \
    --describe \
    --group inventory-service-group
# Compare LAG per partition. CONSUMER-ID shows "-" for partitions with no owner.
```

Short-term relief: scale out instances (up to the partition count), temporarily raise `max.poll.records` or skip non-critical work. Long-term: optimize processing, batch DB writes, partition appropriately.

**Follow-up questions:**

- **Data may expire before consumers catch up. What do you check?**
  Compare lag-in-time with `retention.ms`. If lag approaches retention, messages will be lost to the consumer.
- **Which metrics should be alerting?**
  Consumer lag (and its trend), rebalance rate, processing time per record, DLT message count, error rate.

---

### Q41. How do you debug a broker failure or under-replicated partitions? [Advanced]

**Level: Advanced**

**Short answer:** Under-replicated partitions are partitions where some replicas are not in the ISR. Check which broker is down or slow, read its logs, and check disk, network and load. Producers with `acks=all` may be failing at the same time.

**Explanation:**

Symptoms and first checks:

| Symptom | What it usually means | First check |
|---|---|---|
| Under-replicated partitions > 0 | A follower is down or lagging | `kafka-topics.sh --describe --under-replicated-partitions` |
| Offline partitions > 0 | No leader available. Data unavailable | Which brokers are down, ISR state, unclean election setting |
| `NotEnoughReplicasException` in producers | ISR smaller than `min.insync.replicas` | ISR of the topic (Q28) |
| Producer timeouts | Leader election, overloaded broker, network | Broker logs, request latency metrics |
| Client "cannot connect" or timeouts after metadata | Wrong advertised listener | `advertised.listeners` (Q36) |
| Disk full on a broker | Retention too long or too much traffic | Disk usage, `retention.*`, log directory |

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-topics.sh \
    --describe \
    --under-replicated-partitions \
    --bootstrap-server localhost:9092
# Lists partitions whose ISR is smaller than their replica list
```

Typical causes of a shrinking ISR: broker crash or restart, slow disk, GC pause, network problems, followers too slow because of heavy traffic.

Our single-broker Docker lessons: the broker log message INVALID_REPLICATION_FACTOR meant internal topics needed RF 1. Always read the broker log first.

**Follow-up questions:**

- **What happens during a rolling broker restart?**
  Partitions briefly lose a replica. With RF 3 and minISR 2, restart one broker at a time and wait for the ISR to recover before the next.
- **How do you rebalance data across brokers after adding one?**
  Use partition reassignment (`kafka-reassign-partitions.sh`) or a tool like Cruise Control.

---

### Q42. Design question: Order and Inventory with Kafka [Advanced]

**Level: Advanced**

**Short answer:** Order-Service saves the order and an outbox event in one transaction. A relay publishes it to `order-events` keyed by orderId. Inventory-Service consumes with at-least-once and an idempotent handler, retries transient errors, and sends failures to a DLT.

**Explanation:**

Design choices and why:

| Area | Choice | Reason |
|---|---|---|
| Topic | `order-events`, 6 partitions, RF 3, `min.insync.replicas=2` | Parallelism, durability |
| Key | `orderId` | Events of one order stay in order |
| Producer | `acks=all`, idempotence on, `linger.ms` small, `lz4` compression | Safe and efficient |
| Publishing | Outbox table + relay | No dual-write problem |
| Consumer | Group `inventory-service-group`, manual or batch ack, commit after processing | At-least-once |
| Duplicates | Processed-event table by `eventId` | Idempotent handling |
| Errors | Limited blocking retries, then `order-events.DLT`, alert | Do not block the partition |
| Result back | Inventory publishes `inventory-events` (StockReserved / StockRejected) | Saga with compensation |
| Monitoring | Lag, rebalance rate, DLT count, under-replicated partitions | Catch problems early |

```text
[Order-Service]
  DB tx: orders + outbox
        |
     relay (key = orderId)
        v
 [ order-events (6 partitions, RF 3) ]
        |
 [Inventory-Service group] -- idempotent handler --> DB
        |                         |
        | retries exhausted       | publishes
        v                         v
 [ order-events.DLT ]     [ inventory-events ] --> Order-Service updates status
```

**Follow-up questions:**

- **What if the inventory check must be immediate for the customer?**
  Use a synchronous call for the check (Feign/REST), and Kafka for follow-up work. Not everything should be asynchronous.
- **How would you test it?**
  Use Testcontainers with a real Kafka broker or `@EmbeddedKafka` for integration tests. Test duplicates, failure and replay.
- **How do you evolve the event format?**
  Backward-compatible changes only, with a schema registry or tolerant JSON readers (Q19).
- **What is the weakest point of this design?**
  The relay and consumers must be idempotent. Eventual consistency also needs a good user experience (for example order status "pending").

---
