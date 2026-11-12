# Kafka Review Notes

## 1. What is Kafka?

A **message delivery system** for microservices.

**Without Kafka (Synchronous - Feign):**

```
Order-Service → [Direct Call (Feign)] → Inventory-Service
```

- Like a **phone call** - both sides must be online
- If inventory-service is down → order fails
- Order-service waits until response comes back (blocking)

**With Kafka (Asynchronous):**

```
Order-Service → [Message] → Kafka → [Delivers] → Inventory-Service
```

- Like a **voicemail/WhatsApp** - leave message, receiver checks when ready
- If inventory-service is down → message waits in Kafka
- Order-service doesn't wait (non-blocking)

---

## 2. Core Concepts

### Producer

- The service that **sends** messages to Kafka
- "I have something to say!"
- Example: Order-service sends "New order placed"

#### How Producer Sends Messages:

1. Producer connects to Kafka broker (bootstrap-server)
2. Specifies which **topic** to send to
3. Optionally provides a **key** (determines which partition)
4. Sends the **message** (value)
5. Kafka stores the message in the correct partition

#### Producer Flow:

```
Producer → connects to Kafka
        → "Send this message to topic 'order-events'"
        → Kafka picks a partition (based on key or round-robin)
        → Message stored with an offset number
        → Kafka confirms: "Got it!" (acknowledgment)
```

### Consumer

- The service that **reads/receives** messages from Kafka
- "I'm listening for new messages"
- Example: Inventory-service listens for order events

#### How Consumer Reads Messages:

1. Consumer connects to Kafka broker
2. **Subscribes** to a topic (or specific partitions)
3. Kafka assigns partitions to this consumer
4. Consumer **polls** (asks) for new messages continuously
5. Processes each message
6. **Commits offset** (tells Kafka: "I've read up to here")

#### Consumer Flow:

```
Consumer → connects to Kafka
         → "I want to subscribe to topic 'order-events'"
         → Kafka assigns Partition 0 to this consumer
         → Consumer polls: "Any new messages?"
         → Kafka responds: "Here's offset 5, 6, 7"
         → Consumer processes them
         → Consumer commits: "I've read up to offset 7"
         → Next poll starts from offset 8
```

#### Consumer Group

- Multiple consumers with the **same group ID** form a group
- Kafka distributes partitions among consumers in the same group
- Each partition is assigned to ONLY one consumer in the group
- If a consumer crashes → its partitions are reassigned to others (**rebalancing**)

```
Consumer Group: "inventory-service-group"
  ├── Consumer 1 → reads Partition 0
  ├── Consumer 2 → reads Partition 1
  └── Consumer 3 → reads Partition 2
```

- Different consumer groups can read the SAME messages independently
- Each group maintains its own offsets (position tracking)

```
Topic: order-events

Consumer Group A: "inventory-service-group" → reads ALL messages (own pace)
Consumer Group B: "notification-service-group" → reads ALL messages (own pace)
(Both groups get every message independently!)
```

### Topic

- A **channel/category** for messages
- Like a WhatsApp group - messages go here, subscribers read from here
- Example: `order-events` topic
- You create them explicitly or Kafka auto-creates (if enabled)

#### Topic Properties:

- **Name:** Unique identifier (e.g., `order-events`, `inventory-check`)
- **Partitions:** How many segments the topic is split into (see Section 5)
- **Replication Factor:** How many copies of each partition exist (see Section 4)
- **Retention:** How long messages are kept (default: 7 days, then auto-deleted)

#### Topic Retention:

- Messages are NOT deleted after being read (unlike traditional queues like RabbitMQ!)
- They stay for a configured time (e.g., 7 days)
- Multiple consumer groups can re-read the same messages independently
- After retention period → Kafka auto-deletes old messages

##### Real Example (Order & Inventory):

```
Topic: order-events (retention = 7 days)

Monday:      Order-service sends {quantity: 5}
Tuesday:     Inventory-service reads it → processes it → message STILL in Kafka!
Wednesday:   Notification-service reads SAME message → sends email → still there!
Thursday:    Analytics-service reads SAME message → records stats → still there!
Next Monday: 7 days passed → Kafka auto-deletes the message
```

##### Why Is Retention Useful?

1. **Multiple services can read the same message:**
   - Inventory-service checks stock
   - Notification-service sends email to customer
   - Analytics-service logs order data
   - ALL read from the SAME topic, at their own pace!

2. **Replay/Reprocess:**
   - Bug in inventory-service? Fix it, then re-read old messages from beginning!
   - `--from-beginning` flag lets you re-consume all retained messages

3. **New services:**
   - You add a new "shipping-service" next week
   - It can read ALL orders from the past 7 days (still in Kafka)
   - No need to re-send old messages!

##### Retention Settings:

- `retention.ms` = Time-based (default: 7 days = 604800000 ms)
- `retention.bytes` = Size-based (delete when partition reaches X bytes)
- Can be configured per-topic

---

#### ⚠️ PARTITIONS - Explained with Our Services:

**The BIG question:** "Is a message copied into all 4 partitions?"

**NO!** Each message goes to **ONE partition only.** Partitions are NOT copies!

**Think of it this way:**

- A Topic is like a **restaurant**
- Partitions are **different tables** in the restaurant
- Each order (message) is assigned to **one table only**
- A customer doesn't sit at all tables at once!

##### Real Example with Order-Service:

**Scenario:** Topic `order-events` with 4 partitions. Order-service sends 8 orders:

```
Order-service sends 8 messages to topic: "order-events" (4 partitions)

Message 1: {quantity: 2}  → goes to Partition 0 (offset 0)
Message 2: {quantity: 5}  → goes to Partition 1 (offset 0)
Message 3: {quantity: 1}  → goes to Partition 2 (offset 0)
Message 4: {quantity: 3}  → goes to Partition 3 (offset 0)
Message 5: {quantity: 7}  → goes to Partition 0 (offset 1)
Message 6: {quantity: 4}  → goes to Partition 1 (offset 1)
Message 7: {quantity: 6}  → goes to Partition 2 (offset 1)
Message 8: {quantity: 2}  → goes to Partition 3 (offset 1)
```

**Result:**

```
Partition 0: [Message 1, Message 5]         ← 2 messages
Partition 1: [Message 2, Message 6]         ← 2 messages
Partition 2: [Message 3, Message 7]         ← 2 messages
Partition 3: [Message 4, Message 8]         ← 2 messages
```

**Key point:** Each message exists in ONLY ONE partition! Messages are **distributed**, not copied!

##### Why Distribute? What's the Point?

**1 partition = 1 consumer can read it:**

```
Topic: order-events (1 partition)
  └── Partition 0: [All 8 messages]
      └── Inventory-Service Instance 1 (reads ALL messages alone, slow!)
```

**4 partitions = 4 consumers can read in PARALLEL:**

```
Topic: order-events (4 partitions)
  ├── Partition 0: [Message 1, 5] → Inventory-Service Instance 1 (reads these)
  ├── Partition 1: [Message 2, 6] → Inventory-Service Instance 2 (reads these)
  ├── Partition 2: [Message 3, 7] → Inventory-Service Instance 3 (reads these)
  └── Partition 3: [Message 4, 8] → Inventory-Service Instance 4 (reads these)
```

**4x faster!** Each instance handles only 2 messages instead of 8!

##### When Do You Need Multiple Partitions?

- Your order-service receives **1000 orders/second**
- 1 inventory-service instance can process **250 orders/second**
- Solution: 4 partitions → 4 inventory-service instances → handles 1000/sec
- This is **horizontal scaling** (add more instances + partitions)

##### Our Setup (Learning):

- 1 partition = enough (we have 1 consumer, low traffic)
- Production with high traffic → more partitions

---

#### ⚠️ REPLICATION - Explained with Our Services:

**The BIG question:** "Where do copies stay? Same broker or different? Why?"

**Copies (replicas) ALWAYS stay on DIFFERENT brokers.** Never on the same broker!

##### Why?

Think about it: If you keep the original AND the backup copy on the same laptop...
and that laptop catches fire... BOTH copies are gone! Useless backup!

**Same logic for Kafka:** If a broker crashes, copies on OTHER brokers survive.

##### Real Example with Order-Service (3 Brokers):

**Setup:** Topic `order-events`, 1 partition, replication-factor = 3

```
Broker #1 (server1): Partition 0 → LEADER (original)
Broker #2 (server2): Partition 0 → FOLLOWER (copy #1)
Broker #3 (server3): Partition 0 → FOLLOWER (copy #2)
```

**Order-service sends:** `{quantity: 5}`

```
Step 1: Order-service → sends to Broker #1 (Leader)
Step 2: Broker #1 stores message at offset 0
Step 3: Broker #2 copies message from Broker #1 (stays in sync)
Step 4: Broker #3 copies message from Broker #1 (stays in sync)
Result: Message exists on ALL 3 brokers (3 copies)
```

**Now Broker #1 crashes:**

```
Broker #1: DEAD ☠️ (Leader is gone!)
Broker #2: Partition 0 → NEW LEADER (promoted automatically!)
Broker #3: Partition 0 → FOLLOWER (still a backup)

Order-service: Automatically connects to Broker #2 now
Inventory-service: Automatically reads from Broker #2 now
```

**No data loss! No downtime! Automatic failover!**

##### When Is Replication Needed? Real Scenario:

**Scenario WITHOUT replication (our learning setup):**

```
Order-service sends: {quantity: 5} → Broker #1 (only copy)
Broker #1 crashes...
Message is GONE! ☠️
Inventory-service never processes this order.
Customer's order is LOST.
```

**Scenario WITH replication (production):**

```
Order-service sends: {quantity: 5} → Broker #1 (Leader)
Message copied to Broker #2 and #3 automatically.
Broker #1 crashes...
Broker #2 becomes new Leader.
Inventory-service reads from Broker #2.
Customer's order is SAFE! ✅
```

##### Why Copies Can't Be on Same Broker:

```
❌ WRONG (same broker):
Broker #1: Partition 0 (Leader) + Partition 0 (Copy)
→ Broker #1 crashes → BOTH gone! Useless!

✅ CORRECT (different brokers):
Broker #1: Partition 0 (Leader)
Broker #2: Partition 0 (Copy)
→ Broker #1 crashes → Broker #2 still has it! Safe!
```

##### Replication Factor Cheat Sheet:

- `replication-factor: 1` = No backup (1 broker crash = data loss). Fine for learning.
- `replication-factor: 2` = Survives 1 broker failure. Minimum for production.
- `replication-factor: 3` = Survives 2 broker failures. **Standard for production.**

##### Our Setup:

- 1 broker → can only have replication-factor 1 (no backup possible)
- If this broker crashes → all messages lost (acceptable for learning, NOT for production)

**Production Setup Example:**

```
Topic: "order-events"
- Partitions: 3
- Replication Factor: 3
- Retention: 7 days

3-Broker Cluster:

Broker #1:
  ├── Partition 0 (LEADER)     ← handles orders going to partition 0
  ├── Partition 1 (FOLLOWER)   ← backup of partition 1
  └── Partition 2 (FOLLOWER)   ← backup of partition 2

Broker #2:
  ├── Partition 0 (FOLLOWER)   ← backup of partition 0
  ├── Partition 1 (LEADER)     ← handles orders going to partition 1
  └── Partition 2 (FOLLOWER)   ← backup of partition 2

Broker #3:
  ├── Partition 0 (FOLLOWER)   ← backup of partition 0
  ├── Partition 1 (FOLLOWER)   ← backup of partition 1
  └── Partition 2 (LEADER)     ← handles orders going to partition 2
```

**Flow:**

```
1. Order-service sends: {quantity: 5}
2. Message goes to ONE partition (e.g., Partition 1 on Broker #2 = Leader)
3. Broker #2 stores it → copies to Broker #1 and #3 (replication)
4. Inventory-service (subscribed to partition 1) reads it
5. Checks inventory → processes order
6. Message stays in Kafka for 7 more days (retention)
7. Other services can read it too during those 7 days
```

**Our Learning Setup (simplified):**

```
Topic: "order-events"
- Partitions: 1
- Replication Factor: 1
- Retention: 7 days (default)

1-Broker Cluster:

Broker #1:
  └── Partition 0 (LEADER, no copies)

Flow:
1. Order-service sends: {quantity: 5}
2. Message stored in Partition 0, offset 0
3. Inventory-service reads it from Partition 0
4. Done! Simple!
```

### Message (Record)

- The actual data being sent
- Also called a **Record** in Kafka terminology
- Can be plain text, JSON, Avro, Protobuf - anything

#### Message Structure:

```
Message = {
  Key:       "customer-123"              (optional, used for partitioning)
  Value:     {"orderId": 1, "qty": 5}    (the actual data/payload)
  Timestamp: 2026-05-20T10:30:00         (when it was produced)
  Headers:   {source: "order-service"}   (optional metadata)
  Offset:    42                          (assigned by Kafka, position in partition)
  Partition: 0                           (which partition it's stored in)
}
```

### Broker

- A **Kafka server instance**
- The "post office" that stores and delivers messages
- 1 container = 1 broker
- Listens on a port (our setup: `localhost:9092`)
- Each broker stores some partitions of topics
- Brokers in a cluster work together to distribute load

### Cluster

- **Group of brokers** working together as one system
- Identified by a **Cluster ID** (a unique string)
- All brokers with same Cluster ID = same cluster
- We have: 1-broker cluster (minimal, for learning)
- Production: 3+ broker cluster (for reliability)

#### Why Multiple Brokers in a Cluster?

1. **Fault Tolerance** - If 1 broker crashes, others keep working. No downtime.
2. **Load Distribution** - Messages are spread across brokers. No single server gets overwhelmed.
3. **Data Safety** - Copies of data exist on multiple brokers. If one dies, data survives.

#### Key Cluster Terms:

##### Leader

- For each **partition** of a topic, ONE broker is the **Leader**
- The Leader **handles ALL reads and writes** for that partition
- Producers send messages to the Leader
- Consumers read from the Leader
- Only one Leader per partition at any time

##### Follower (Replica)

- Other brokers that **copy data** from the Leader
- They don't serve clients directly - they just keep a backup
- If the Leader dies → one Follower gets **promoted to new Leader** (automatic!)
- Think of it like: Leader is the main cashier, Followers are backup cashiers ready to take over

##### ISR (In-Sync Replicas)

- The list of Followers that are **up-to-date** with the Leader
- "In-Sync" means they have copied ALL recent messages from the Leader
- If a Follower falls behind (slow/crashed) → removed from ISR
- When Leader dies, new Leader is chosen ONLY from ISR (because they have all the data)

##### Controller

- ONE broker in the cluster acts as the **Controller**
- The "manager" of the cluster
- Responsibilities:
  - Decides who is Leader for each partition
  - Detects when brokers join/leave the cluster
  - Reassigns Leaders when a broker crashes
- In KRaft mode: the controller role is built into Kafka itself
- In our setup: our single broker is BOTH the controller AND the broker

#### Example: 3-Broker Cluster with Topic "order-events" (3 partitions, replication=3)

```
Cluster ID: ABC123

Broker #1 (ID=1):
  ├── Partition 0: LEADER    ← Handles reads/writes for partition 0
  ├── Partition 1: FOLLOWER  ← Backup copy of partition 1
  └── Partition 2: FOLLOWER  ← Backup copy of partition 2

Broker #2 (ID=2):
  ├── Partition 0: FOLLOWER  ← Backup copy of partition 0
  ├── Partition 1: LEADER    ← Handles reads/writes for partition 1
  └── Partition 2: FOLLOWER  ← Backup copy of partition 2

Broker #3 (ID=3):
  ├── Partition 0: FOLLOWER  ← Backup copy of partition 0
  ├── Partition 1: FOLLOWER  ← Backup copy of partition 1
  └── Partition 2: LEADER    ← Handles reads/writes for partition 2
```

**What happens if Broker #2 crashes?**

- Partition 1 lost its Leader!
- Controller detects the crash
- Picks a Follower from ISR (Broker #1 or #3) as new Leader for Partition 1
- Clients automatically redirect to new Leader
- No data loss (Followers had copies)!
- No manual intervention needed!

#### Our Setup (1-Broker Cluster):

```
Cluster ID: MkU3OEVBNTcwNTJENDM2Qk

Broker #1 (ID=1):
  └── Partition 0: LEADER (no Followers, only 1 broker exists)
```

- Our single broker is Leader for everything
- No Followers (can't replicate to yourself)
- No fault tolerance (if it crashes, data is gone)
- Fine for learning! Production would use 3+ brokers

---

## 3. KRaft vs ZooKeeper

### ZooKeeper (Old Way - Deprecated)

- External service that managed Kafka
- Extra complexity (run 2 services: ZooKeeper + Kafka)
- Being removed in Kafka 4.0

### KRaft (New Way - What We Use)

- **K**afka **Raft** - Kafka manages itself
- No external dependency
- Simpler setup (just Kafka)
- Our broker has **two roles**:
  - `broker` - handles messages
  - `controller` - manages cluster (replaced ZooKeeper's job)

---

## 4. Replication

### What:

- Making **copies** of data across multiple brokers
- The ONLY purpose: **fault tolerance** (if one broker dies, data survives)
- Without replication: broker crashes = data lost forever

### How It Works:

- `replication-factor: 3` = keep 3 copies of each partition across 3 different brokers
- One copy is the **Leader** (serves clients)
- Other copies are **Followers** (backups, stay in sync)
- If Leader dies → a Follower becomes the new Leader automatically

### Example:

```
Topic: order-events, Partition 0, Replication Factor: 3

Broker #1 → Partition 0 (LEADER)    ← Producer writes here, Consumer reads here
Broker #2 → Partition 0 (FOLLOWER)  ← Copies from Leader (backup #1)
Broker #3 → Partition 0 (FOLLOWER)  ← Copies from Leader (backup #2)
```

**If Broker #1 dies:**

```
Broker #1 → DEAD ☠️
Broker #2 → Partition 0 (NEW LEADER) ← Now serves clients!
Broker #3 → Partition 0 (FOLLOWER)   ← Still a backup
```

- Automatic failover, no data loss, no human intervention!

### Key Terms:

#### Replication Factor

- Number of TOTAL copies (Leader + Followers)
- `replication-factor: 1` = 1 copy (just the Leader, no backup)
- `replication-factor: 3` = 3 copies (1 Leader + 2 Followers)

#### Acknowledgments (acks) - Producer Setting

- Controls when the Producer considers a message "successfully sent"
- `acks=0`: Producer doesn't wait for any confirmation (fastest, but message could be lost)
- `acks=1`: Producer waits for Leader to confirm (message saved on Leader, but Followers might not have it yet)
- `acks=all`: Producer waits for Leader AND all ISR Followers to confirm (slowest, but safest - no data loss)

#### Min In-Sync Replicas (min.insync.replicas)

- Minimum number of replicas that must confirm a write (when acks=all)
- Example: `min.insync.replicas=2` with `acks=all`
  - Producer sends message → Leader AND at least 1 Follower must confirm
  - If only Leader is alive (Followers all dead) → Producer gets an error (won't risk losing data)

### Rules:

- Replication factor **cannot exceed** number of brokers
- 1 broker → max replication factor = 1 (no backup possible)
- 3 brokers → max replication factor = 3 (every broker has a copy)
- 5 brokers → can still use replication factor = 3 (only 3 out of 5 brokers get copies)

### Our Setup:

- 1 broker → replication factor = 1 (no backup, fine for learning)
- Production standard: replication factor = 3 (handles up to 2 broker failures)

### Real-World Use Case (Order & Inventory):

**The BIG question:** "Where do copies stay? Same broker or different? Why?"

**Copies (replicas) ALWAYS stay on DIFFERENT brokers.** Never on the same broker!

#### Why?

Think about it: If you keep the original AND the backup copy on the same laptop...
and that laptop catches fire... BOTH copies are gone! Useless backup!

**Same logic for Kafka:** If a broker crashes, copies on OTHER brokers survive.

#### Real Example with Order-Service (3 Brokers):

**Setup:** Topic `order-events`, 1 partition, replication-factor = 3

```
Broker #1 (server1): Partition 0 → LEADER (original)
Broker #2 (server2): Partition 0 → FOLLOWER (copy #1)
Broker #3 (server3): Partition 0 → FOLLOWER (copy #2)
```

**Order-service sends:** `{quantity: 5}`

```
Step 1: Order-service → sends to Broker #1 (Leader)
Step 2: Broker #1 stores message at offset 0
Step 3: Broker #2 copies message from Broker #1 (stays in sync)
Step 4: Broker #3 copies message from Broker #1 (stays in sync)
Result: Message exists on ALL 3 brokers (3 copies)
```

**Now Broker #1 crashes:**

```
Broker #1: DEAD ☠️ (Leader is gone!)
Broker #2: Partition 0 → NEW LEADER (promoted automatically!)
Broker #3: Partition 0 → FOLLOWER (still a backup)

Order-service: Automatically connects to Broker #2 now
Inventory-service: Automatically reads from Broker #2 now
```

**No data loss! No downtime! Automatic failover!**

#### Scenario WITHOUT replication (our learning setup):

```
Order-service sends: {quantity: 5} → Broker #1 (only copy)
Broker #1 crashes...
Message is GONE! ☠️
Inventory-service never processes this order.
Customer's order is LOST.
```

#### Scenario WITH replication (production):

```
Order-service sends: {quantity: 5} → Broker #1 (Leader)
Message copied to Broker #2 and #3 automatically.
Broker #1 crashes...
Broker #2 becomes new Leader.
Inventory-service reads from Broker #2.
Customer's order is SAFE! ✅
```

#### Why Copies Can't Be on Same Broker:

```
❌ WRONG (same broker):
Broker #1: Partition 0 (Leader) + Partition 0 (Copy)
→ Broker #1 crashes → BOTH gone! Useless!

✅ CORRECT (different brokers):
Broker #1: Partition 0 (Leader)
Broker #2: Partition 0 (Copy)
→ Broker #1 crashes → Broker #2 still has it! Safe!
```

---

## 5. Partitions

### What:

- A topic is split into **segments** called partitions
- Each partition is an **ordered, immutable sequence of messages** (like a log file)
- Messages within a partition have a number called **offset** (0, 1, 2, 3...)

### Why Use Partitions:

- **Parallelism** - multiple consumers can read different partitions at the same time
- **Performance** - distribute the load across multiple brokers
- **Scalability** - more partitions = more throughput

### Analogy:

- Topic = A highway
- Partitions = Lanes on that highway
- 1 partition = 1 lane (all cars in single file, slow)
- 4 partitions = 4 lanes (cars move in parallel, faster)

### Key Terms:

#### Offset

- A **sequential number** assigned to each message within a partition
- Starts at 0, increments by 1 for each new message
- Like a line number in a file
- Consumers track their position using offsets ("I've read up to offset 42")

```
Partition 0:
  Offset 0: "Order #1 placed"
  Offset 1: "Order #2 placed"
  Offset 2: "Order #3 placed"  ← Consumer is here (has read 0, 1, 2)
  Offset 3: "Order #4 placed"  ← Not yet read
  Offset 4: "Order #5 placed"  ← Not yet read
```

#### Partition Key

- When producing messages, you can attach a **key**
- Messages with the **same key** always go to the **same partition**
- Messages with **no key** are distributed round-robin (evenly spread)

```
Example: Key = customer ID

Customer "A" orders → always goes to Partition 0
Customer "B" orders → always goes to Partition 1
Customer "A" orders → always goes to Partition 0 (same key = same partition!)
```

**Why this matters:**

- Messages in the SAME partition are **ordered** (guaranteed!)
- Messages across DIFFERENT partitions have **no ordering guarantee**
- So if you need all orders from Customer "A" processed in order → use customer ID as key

#### Ordering Guarantee

- **Within 1 partition:** Messages are ALWAYS in order (offset 0 before 1 before 2...)
- **Across partitions:** NO ordering guarantee (Partition 0 offset 5 might be processed before Partition 1 offset 3)
- **Rule of thumb:** If order matters for a group of messages, make sure they go to the same partition (use same key)

### How Partitions Are Distributed Across Brokers:

```
Topic: order-events (4 partitions) on 3 brokers:

Broker #1: Partition 0, Partition 3
Broker #2: Partition 1
Broker #3: Partition 2
```

- Kafka spreads partitions across brokers automatically
- This distributes the load (no single broker holds everything)

### Partitions + Consumers:

- 1 partition can be read by **only 1 consumer** in a consumer group
- If you have 4 partitions → max 4 consumers can work in parallel
- If you have 4 partitions but 6 consumers → 2 consumers sit idle (nothing to read)
- **Rule:** Number of consumers in a group should be ≤ number of partitions

```
Topic: order-events (3 partitions)
Consumer Group: inventory-service-group (3 consumers)

Consumer 1 → reads Partition 0
Consumer 2 → reads Partition 1
Consumer 3 → reads Partition 2
(Perfect! Each consumer has work to do)
```

### How Many Partitions Should I Use?

- **Learning/Dev:** 1 partition (simple, easy to understand)
- **Small production:** 3-6 partitions
- **High throughput:** 10-50+ partitions
- **Rule of thumb:** Start small, increase when you need more parallelism

### Our Setup:

- 1 partition per topic (simplest, all messages in one sequence)

### Real-World Use Case (Order & Inventory):

**The BIG question:** "Is a message copied into all partitions?"

**NO!** Each message goes to **ONE partition only.** Partitions are NOT copies!

**Think of it this way:**

- A Topic is like a **restaurant**
- Partitions are **different tables** in the restaurant
- Each order (message) is assigned to **one table only**
- A customer doesn't sit at all tables at once!

#### Real Example:

**Scenario:** Topic `order-events` with 4 partitions. Order-service sends 8 orders:

```
Message 1: {quantity: 2}  → goes to Partition 0 (offset 0)
Message 2: {quantity: 5}  → goes to Partition 1 (offset 0)
Message 3: {quantity: 1}  → goes to Partition 2 (offset 0)
Message 4: {quantity: 3}  → goes to Partition 3 (offset 0)
Message 5: {quantity: 7}  → goes to Partition 0 (offset 1)
Message 6: {quantity: 4}  → goes to Partition 1 (offset 1)
Message 7: {quantity: 6}  → goes to Partition 2 (offset 1)
Message 8: {quantity: 2}  → goes to Partition 3 (offset 1)
```

**Result:** Each message is in ONLY ONE partition - messages are **distributed**, not copied!

#### Why Distribute? Parallel Processing:

```
1 partition (slow):
Partition 0: [All 8 messages]
→ Only 1 inventory-service instance can read (sequential, slow)

4 partitions (fast):
Partition 0 → Inventory Instance 1 (reads messages 1, 5)
Partition 1 → Inventory Instance 2 (reads messages 2, 6)
Partition 2 → Inventory Instance 3 (reads messages 3, 7)
Partition 3 → Inventory Instance 4 (reads messages 4, 8)
→ 4x faster! All instances work in parallel!
```

#### When Would You Need This?

- Order-service receives 1000 orders/second
- 1 inventory-service handles 250 orders/second
- Solution: 4 partitions + 4 inventory-service instances = handles 1000/sec!

---

## 6. Broker, Machine, Container

### Key Understanding:

- **1 Kafka instance = 1 Broker**
- **1 Docker container = 1 Kafka instance = 1 Broker**
- Brokers on **same machine** → need different ports (9092, 9093, 9094)
- Brokers on **different machines** → can use same port (9092)

### For Learning:

- Multiple Docker containers on your laptop = simulates multiple brokers
- No need for multiple physical computers!

### Production (Different Machines):

```
✅ server1:9092 → Broker #1
✅ server2:9092 → Broker #2
✅ server3:9092 → Broker #3
(Same port, different machines = OK!)
```

### If on Same Machine:

```
✅ localhost:9092 → Broker #1
✅ localhost:9093 → Broker #2
✅ localhost:9094 → Broker #3
(Same machine, different ports = OK!)
```

---

## 7. Docker Setup Explained (docker-compose.yml)

```yaml
KAFKA_NODE_ID: 1 # Broker ID
KAFKA_PROCESS_ROLES: broker,controller # Does both jobs (KRaft)
KAFKA_LISTENERS: PLAINTEXT://localhost:9092, CONTROLLER://localhost:9093
#                 ↑ clients connect here    ↑ internal management
KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092 # Address told to clients
KAFKA_CONTROLLER_QUORUM_VOTERS: 1@localhost:9093 # Controller node list
CLUSTER_ID: MkU3OEVBNTcwNTJENDM2Qk # Identifies our cluster

# Single-broker fixes (prevents replication errors):
KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
```

### Why those last 3 lines?

- Kafka's internal topics default to replication-factor 3
- We only have 1 broker → auto-creation fails
- These settings tell Kafka: "Use replication 1 for internal topics"

### Single-Broker Config Notes:

```yaml
# When Running Single-Broker Kafka (Development/Learning):
KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1

# When Running Multi-Broker Kafka (Production):
KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 3
KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 3
KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 2
```

---

## 8. Kafka CLI Commands

### Start Kafka (Docker):

```bash
docker-compose up -d
```

### Stop Kafka:

```bash
docker-compose down
```

### Check if Running:

```bash
docker ps
```

### Check Logs:

```bash
docker logs kafka-broker
```

### Parts of a Command (read this first):

Every command below is built from the same pieces.

| Part | Meaning |
|------|---------|
| `docker exec kafka-broker` | Run a command inside the running container named `kafka-broker` |
| `-it` | Interactive mode: keeps the terminal open so you can type (needed for producer and consumer) |
| `/opt/kafka/bin/kafka-topics.sh` | The Kafka script to run, located inside the container |
| `--bootstrap-server localhost:9092` | Which Kafka broker to connect to (address and port) |
| `--topic test-topic` | The topic to work with |
| `--partitions 1` | How many partitions the topic has |
| `--replication-factor 1` | How many copies of each partition are kept (1 = no extra copies) |

- The scripts are `kafka-topics.sh` (manage topics), `kafka-console-producer.sh` (send messages) and `kafka-console-consumer.sh` (read messages).
- `localhost` here means inside the container, so it reaches the broker running in that same container.
- The commands below are split across lines with `\` (bash). In PowerShell, use a backtick `` ` `` at the end of each line instead of `\`.

### Create a Topic:

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-topics.sh \
    --create \
    --topic test-topic \
    --bootstrap-server localhost:9092 \
    --partitions 1 \
    --replication-factor 1
```

- Creates a topic named `test-topic`.
- No `-it` needed: it runs once and exits.
- `--partitions 1`: one partition, so all messages stay in a single ordered log.
- `--replication-factor 1`: one copy only. This must be 1 or less than the number of brokers, and we have one broker.

### List All Topics:

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-topics.sh \
    --list \
    --bootstrap-server localhost:9092
```

- Prints the names of all topics on the broker.

### Describe a Topic:

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-topics.sh \
    --describe \
    --topic test-topic \
    --bootstrap-server localhost:9092
```

- Shows the partition count, replication factor, which broker is the leader and the in-sync replicas.

### Send Messages (Producer):

```bash
docker exec -it kafka-broker \
    /opt/kafka/bin/kafka-console-producer.sh \
    --topic test-topic \
    --bootstrap-server kafka-broker:9092
```

- `-it` is needed because you type messages into it.
- Type a message and press Enter to send it. Each line is one message.
- Press Ctrl+C to exit.
- `kafka-broker:9092` uses the container name as the address. From inside the container it reaches the same broker as `localhost:9092`.

### Read Messages (Consumer):

```bash
docker exec -it kafka-broker \
    /opt/kafka/bin/kafka-console-consumer.sh \
    --topic test-topic \
    --bootstrap-server localhost:9092 \
    --from-beginning
```

- `-it` keeps the consumer running, so new messages appear as they arrive.
- `--from-beginning` = read ALL messages from the start.
- Without it = only shows NEW messages.
- Press Ctrl+C to exit.

### Create the `__consumer_offsets` Topic:

```bash
docker exec kafka-broker \
    /opt/kafka/bin/kafka-topics.sh \
    --create \
    --topic __consumer_offsets \
    --bootstrap-server localhost:9092 \
    --partitions 50 \
    --replication-factor 1
```

- `__consumer_offsets` is a special internal topic. Kafka uses it to remember how far each consumer group has read (the offsets).
- Kafka normally creates it automatically with replication-factor 3. With only one broker that fails, so consumers cannot work (see section 9).
- Create it by hand with `--replication-factor 1`.
- `--partitions 50` is Kafka's default for this topic.
- You only need to do this once, and only if Kafka could not create it.

### Note for Windows Git Bash:

- Use PowerShell instead (Git Bash has path conversion issues with Docker)
- Or prefix with `winpty` or use `//opt` instead of `/opt`

---

## 9. Issue We Fixed

### Problem:

`__consumer_offsets` topic failed to auto-create because Kafka defaulted to replication-factor 3, but we only had 1 broker.

### Symptoms:

- Producer sends messages (no error)
- Consumer shows 0 messages
- Logs spamming: "INVALID_REPLICATION_FACTOR"

### Fix:

1. Created `__consumer_offsets` manually with replication-factor 1
2. Added single-broker config to docker-compose.yml to prevent in future

---

## 10. Architecture Overview

```
Service Registry (Eureka)  → Port 8761
Gateway Service            → Port 8080
Order Service             → Port 8282
Inventory Service         → Port 8383
Kafka Broker (Docker)     → Port 9092
```

---

## 11. Full Picture - Our Order & Inventory System

**Production Setup Example:**

```
Topic: "order-events"
- Partitions: 3
- Replication Factor: 3
- Retention: 7 days

3-Broker Cluster:

Broker #1:
  ├── Partition 0 (LEADER)     ← handles orders going to partition 0
  ├── Partition 1 (FOLLOWER)   ← backup of partition 1
  └── Partition 2 (FOLLOWER)   ← backup of partition 2

Broker #2:
  ├── Partition 0 (FOLLOWER)   ← backup of partition 0
  ├── Partition 1 (LEADER)     ← handles orders going to partition 1
  └── Partition 2 (FOLLOWER)   ← backup of partition 2

Broker #3:
  ├── Partition 0 (FOLLOWER)   ← backup of partition 0
  ├── Partition 1 (FOLLOWER)   ← backup of partition 1
  └── Partition 2 (LEADER)     ← handles orders going to partition 2
```

**Flow:**

```
1. Order-service sends: {quantity: 5}
2. Message goes to ONE partition (e.g., Partition 1 on Broker #2 = Leader)
3. Broker #2 stores it → copies to Broker #1 and #3 (replication)
4. Inventory-service (subscribed to partition 1) reads it
5. Checks inventory → processes order
6. Message stays in Kafka for 7 more days (retention)
7. Other services (notification, analytics) can read it too during those 7 days
```

**Our Learning Setup (simplified):**

```
Topic: "order-events"
- Partitions: 1
- Replication Factor: 1
- Retention: 7 days (default)

1-Broker Cluster:

Broker #1:
  └── Partition 0 (LEADER, no copies)

Flow:
1. Order-service sends: {quantity: 5}
2. Message stored in Partition 0, offset 0
3. Inventory-service reads it from Partition 0
4. Done! Simple!
```

---

