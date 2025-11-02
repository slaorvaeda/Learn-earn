# 10. Apache Kafka - Event Streaming Platform

## 🎯 Learning Objectives
- Understand Apache Kafka and event streaming
- Learn Kafka architecture and concepts
- Install and configure Kafka
- Understand topics, partitions, and brokers
- Set up your first Kafka cluster

## ⚡ What is Apache Kafka?

Apache Kafka is a distributed event streaming platform.

**Key Features:**
- **High Throughput**: Millions of messages per second
- **Scalable**: Distributed architecture
- **Fault Tolerant**: Replication and persistence
- **Real-time**: Low latency processing
- **Durable**: Messages persist on disk

## 🏗️ Kafka Architecture

### Core Components

```
Producer → Kafka Cluster → Consumer
            ├── Broker 1
            │   ├── Topic A (Partition 0, 1)
            │   └── Topic B (Partition 0)
            ├── Broker 2
            │   ├── Topic A (Partition 2)
            │   └── Topic B (Partition 1)
            └── Broker 3
                └── Topic C (Partition 0, 1, 2)
```

### Key Concepts

**Topics**
- Categories for messages
- Similar to tables in databases
- Can have multiple partitions

**Partitions**
- Parallel processing units
- Ordered sequences of messages
- Replicated for fault tolerance

**Brokers**
- Kafka servers
- Store and serve topics
- Form a cluster

**Producers**
- Applications that publish messages
- Send data to topics
- Choose partitions

**Consumers**
- Applications that read messages
- Subscribe to topics
- Process in order

## 🚀 Kafka Installation

### Prerequisites
Java 8 or higher required

```bash
# Check Java version
java -version

# Install Java (macOS)
brew install openjdk

# Install Java (Ubuntu)
sudo apt-get update
sudo apt-get install default-jdk
```

### Download and Setup

```bash
# Download Kafka
wget https://downloads.apache.org/kafka/3.6.0/kafka_2.13-3.6.0.tgz

# Extract
tar -xzf kafka_2.13-3.6.0.tgz
cd kafka_2.13-3.6.0

# Or use Homebrew (macOS)
brew install kafka
```

### Start Kafka

```bash
# Start Zookeeper (required for Kafka)
bin/zookeeper-server-start.sh config/zookeeper.properties

# In new terminal, start Kafka
bin/kafka-server-start.sh config/server.properties

# Or with Homebrew
brew services start zookeeper
brew services start kafka
```

## 🎯 Basic Kafka Operations

### Create Topic
```bash
# Create topic with 3 partitions and replication factor 1
bin/kafka-topics.sh --create \
  --bootstrap-server localhost:9092 \
  --topic my-topic \
  --partitions 3 \
  --replication-factor 1

# List topics
bin/kafka-topics.sh --list --bootstrap-server localhost:9092

# Describe topic
bin/kafka-topics.sh --describe \
  --bootstrap-server localhost:9092 \
  --topic my-topic
```

### Produce Messages
```bash
# Start producer
bin/kafka-console-producer.sh \
  --bootstrap-server localhost:9092 \
  --topic my-topic

# Type messages and press Enter
Hello Kafka!
This is my first message
Event streaming is powerful
```

### Consume Messages
```bash
# Start consumer
bin/kafka-console-consumer.sh \
  --bootstrap-server localhost:9092 \
  --topic my-topic \
  --from-beginning

# Real-time consumer (new messages only)
bin/kafka-console-consumer.sh \
  --bootstrap-server localhost:9092 \
  --topic my-topic
```

## 📊 Kafka Concepts Deep Dive

### Topics and Partitions
```
Topic: "orders"
├── Partition 0: [msg1, msg4, msg7]
├── Partition 1: [msg2, msg5, msg8]
└── Partition 2: [msg3, msg6, msg9]

Benefits of partitions:
- Parallel processing
- Horizontal scaling
- Throughput increase
```

### Consumer Groups
```bash
# Create consumer group
bin/kafka-console-consumer.sh \
  --bootstrap-server localhost:9092 \
  --topic my-topic \
  --group my-consumer-group
```

**Consumer Group Behavior:**
- Multiple consumers in group share partitions
- Each partition consumed by one consumer
- Load balancing across consumers

### Message Keys
```bash
# Produce with key
bin/kafka-console-producer.sh \
  --bootstrap-server localhost:9092 \
  --topic my-topic \
  --property "parse.key=true" \
  --property "key.separator=:"

# Type: userId:message content
1:Hello from user 1
2:Hello from user 2
1:Another message from user 1
```

**Key Benefits:**
- Guaranteed ordering per key
- Partition determined by key
- Related messages to same partition

## 🧪 Hands-On Example

### Complete Workflow
```bash
# Terminal 1: Start Zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

# Terminal 2: Start Kafka
bin/kafka-server-start.sh config/server.properties

# Terminal 3: Create topic
bin/kafka-topics.sh --create \
  --bootstrap-server localhost:9092 \
  --topic test-topic \
  --partitions 3 \
  --replication-factor 1

# Terminal 4: Produce messages
bin/kafka-console-producer.sh \
  --bootstrap-server localhost:9092 \
  --topic test-topic

# Terminal 5: Consume messages
bin/kafka-console-consumer.sh \
  --bootstrap-server localhost:9092 \
  --topic test-topic \
  --from-beginning
```

## 💡 Use Cases

### Real-Time Analytics
```
Website → Kafka → Analytics Engine → Dashboard
```

### Event Sourcing
```
User Actions → Kafka → Event Store → Replay
```

### Log Aggregation
```
Applications → Kafka → Central Log System → Analysis
```

### Microservices Communication
```
Service A → Kafka → Service B
Service A → Kafka → Service C
```

## 🔗 Next Steps

Kafka basics covered! Let's create producers!

---

**Key Takeaways:**
- Kafka is distributed event streaming platform
- Topics organize messages
- Partitions enable parallelism
- Producers publish messages
- Consumers read messages
- Replication ensures durability

**Next Tutorial:** [11-Kafka-Producers.md](./11-Kafka-Producers.md)
