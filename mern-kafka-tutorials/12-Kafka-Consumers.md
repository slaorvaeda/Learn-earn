# 12. Kafka Consumers - Consuming Events

## 🎯 Learning Objectives
- Create Kafka consumers in Node.js
- Understand consumer groups
- Process messages efficiently
- Handle offsets and commits
- Implement error handling

## 📥 Consumer Setup

### Basic Consumer
```javascript
// kafka/consumer.js
const { Kafka } = require('kafkajs');

const kafka = new Kafka({
  brokers: ['localhost:9092'],
  clientId: 'my-consumer-app'
});

const consumer = kafka.consumer({ 
  groupId: 'my-consumer-group' 
});

// Connect consumer
async function connect() {
  await consumer.connect();
  console.log('Consumer connected');
}

// Subscribe to topic
async function subscribe(topic) {
  await consumer.subscribe({ topic, fromBeginning: true });
}

module.exports = { consumer, connect, subscribe };
```

## 📨 Consuming Messages

### Basic Consumer
```javascript
const { consumer, connect, subscribe } = require('./kafka/consumer');

async function consumeMessages() {
  await connect();
  await subscribe('test-topic');
  
  await consumer.run({
    eachMessage: async ({ topic, partition, message }) => {
      console.log({
        topic,
        partition,
        offset: message.offset,
        key: message.key.toString(),
        value: message.value.toString()
      });
    }
  });
}

consumeMessages();
```

### Process JSON Messages
```javascript
await consumer.run({
  eachMessage: async ({ topic, partition, message }) => {
    try {
      const data = JSON.parse(message.value.toString());
      console.log('Received:', data);
      
      // Process the message
      await processOrderEvent(data);
    } catch (error) {
      console.error('Error processing message:', error);
    }
  }
});
```

## 🔄 Consumer Groups

### Consumer Group Benefits
```
Topic: orders (3 partitions)

Consumer Group: order-processors
├── Consumer 1 → processes partition 0
├── Consumer 2 → processes partition 1
└── Consumer 3 → processes partition 2

Load balanced across consumers!
```

### Create Consumer Group
```javascript
const consumer = kafka.consumer({ 
  groupId: 'order-processing-group',
  sessionTimeout: 30000,
  rebalanceTimeout: 60000
});
```

## 📊 Offset Management

### Offsets
```javascript
// Commit offsets
await consumer.run({
  eachMessage: async ({ message }) => {
    // Process message
    await processMessage(message);
    
    // Offsets auto-committed after processing
  }
});

// Manual commit
await consumer.commitOffsets([
  { 
    topic: 'orders',
    partition: 0,
    offset: '100'
  }
]);
```

### From Beginning vs Latest
```javascript
// Read all messages from beginning
await consumer.subscribe({ 
  topic: 'orders', 
  fromBeginning: true 
});

// Read only new messages
await consumer.subscribe({ 
  topic: 'orders', 
  fromBeginning: false 
});
```

## 💼 Complete Consumer Example

### Order Event Consumer
```javascript
// kafka/consumers/orderConsumer.js
const { consumer, connect, subscribe } = require('../config');

class OrderConsumer {
  async start() {
    await connect();
    await subscribe('order-events');
    
    await consumer.run({
      eachMessage: async ({ message }) => {
        try {
          const event = JSON.parse(message.value.toString());
          
          switch(event.eventType) {
            case 'order-created':
              await this.handleOrderCreated(event);
              break;
            case 'order-shipped':
              await this.handleOrderShipped(event);
              break;
            case 'order-cancelled':
              await this.handleOrderCancelled(event);
              break;
          }
        } catch (error) {
          console.error('Consumer error:', error);
          // Send to dead letter queue
        }
      }
    });
  }

  async handleOrderCreated(event) {
    console.log('Processing order created:', event.orderId);
    // Update inventory, send email, etc.
  }

  async handleOrderShipped(event) {
    console.log('Processing order shipped:', event.orderId);
    // Send tracking email
  }

  async handleOrderCancelled(event) {
    console.log('Processing order cancelled:', event.orderId);
    // Refund, restore inventory
  }
}

module.exports = new OrderConsumer();
```

### Start Consumer
```javascript
// server.js
const orderConsumer = require('./kafka/consumers/orderConsumer');

async function start() {
  // Start Express server
  app.listen(3000, () => {
    console.log('Server running on port 3000');
  });
  
  // Start Kafka consumer
  await orderConsumer.start();
}

start();
```

## 🔗 Next Steps

Consumers mastered! Let's integrate with MERN!

---

**Key Takeaways:**
- Consumers read messages from topics
- Consumer groups enable parallel processing
- Offsets track read position
- Process messages in eachMessage handler
- Handle errors and implement retries
- Organize by consumer classes

**Next Tutorial:** [14-MERN-Kafka-Integration.md](./14-MERN-Kafka-Integration.md)
