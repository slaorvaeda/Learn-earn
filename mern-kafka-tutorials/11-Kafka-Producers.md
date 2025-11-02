# 11. Kafka Producers - Publishing Events

## 🎯 Learning Objectives
- Create Kafka producers in Node.js
- Understand producer configuration
- Handle acknowledgments and retries
- Implement message serialization
- Best practices for producers

## 🚀 Setting Up KafkaJS

### Install KafkaJS
```bash
npm install kafkajs
```

### Basic Producer Setup
```javascript
// kafka/producer.js
const { Kafka } = require('kafkajs');

const kafka = new Kafka({
  brokers: ['localhost:9092'],
  clientId: 'my-producer-app'
});

const producer = kafka.producer();

// Connect producer
async function connect() {
  await producer.connect();
  console.log('Producer connected');
}

// Disconnect producer
async function disconnect() {
  await producer.disconnect();
  console.log('Producer disconnected');
}

module.exports = { producer, connect, disconnect };
```

## 📤 Sending Messages

### Send Single Message
```javascript
const { producer, connect, disconnect } = require('./kafka/producer');

async function sendMessage() {
  await connect();
  
  await producer.send({
    topic: 'test-topic',
    messages: [
      {
        key: 'user-1',
        value: JSON.stringify({
          userId: '1',
          action: 'login',
          timestamp: new Date().toISOString()
        })
      }
    ]
  });
  
  console.log('Message sent');
  await disconnect();
}

sendMessage();
```

### Send Multiple Messages
```javascript
async function sendMultipleMessages() {
  await connect();
  
  const messages = [
    { value: JSON.stringify({ type: 'order', amount: 100 }) },
    { value: JSON.stringify({ type: 'order', amount: 200 }) },
    { value: JSON.stringify({ type: 'order', amount: 300 }) }
  ];
  
  await producer.send({
    topic: 'orders',
    messages: messages
  });
  
  await disconnect();
}
```

### Send with Partition Key
```javascript
async function sendWithKey() {
  await connect();
  
  await producer.send({
    topic: 'user-events',
    messages: [
      {
        key: 'user-123',  // All messages with same key go to same partition
        value: JSON.stringify({ userId: '123', event: 'page_view' })
      }
    ]
  });
}
```

## ⚙️ Producer Configuration

### Important Settings
```javascript
const producer = kafka.producer({
  maxInFlightRequests: 1,        // Wait for ack before next send
  idempotent: true,              // Exactly-once semantics
  transactionTimeout: 30000,
  retry: {
    initialRetryTime: 100,
    retries: 8
  }
});
```

### Acknowledgment Modes
```javascript
// Producer config
const producer = kafka.producer({
  idempotent: true
});

// Send with acks
await producer.send({
  topic: 'orders',
  messages: [{ value: 'order data' }],
  acks: 1  // 0, 1, or -1 (all)
});
```

## 🔄 Retry and Error Handling

### Error Handling
```javascript
async function sendWithErrorHandling() {
  try {
    await producer.send({
      topic: 'orders',
      messages: [{ value: 'order data' }]
    });
    console.log('Message sent successfully');
  } catch (error) {
    console.error('Error sending message:', error);
    // Implement retry logic
  }
}
```

### Retry Configuration
```javascript
const producer = kafka.producer({
  retry: {
    initialRetryTime: 100,      // Initial delay
    retries: 8,                  // Max retries
    maxRetryTime: 30000,         // Max delay
    multiplier: 2,               // Exponential backoff
    restartOnFailure: async () => true
  }
});
```

## 🧪 Complete Producer Example

### Order Event Producer
```javascript
// kafka/producers/orderProducer.js
const { producer, connect, disconnect } = require('../config');

class OrderProducer {
  async publishOrderCreated(order) {
    await producer.send({
      topic: 'order-events',
      messages: [{
        key: order.id,
        value: JSON.stringify({
          eventType: 'order-created',
          orderId: order.id,
          customerId: order.customerId,
          total: order.total,
          items: order.items,
          timestamp: new Date().toISOString()
        })
      }]
    });
  }

  async publishOrderShipped(orderId, trackingNumber) {
    await producer.send({
      topic: 'order-events',
      messages: [{
        key: orderId,
        value: JSON.stringify({
          eventType: 'order-shipped',
          orderId,
          trackingNumber,
          timestamp: new Date().toISOString()
        })
      }]
    });
  }

  async publishOrderCancelled(orderId, reason) {
    await producer.send({
      topic: 'order-events',
      messages: [{
        key: orderId,
        value: JSON.stringify({
          eventType: 'order-cancelled',
          orderId,
          reason,
          timestamp: new Date().toISOString()
        })
      }]
    });
  }
}

module.exports = new OrderProducer();
```

### Usage in Express API
```javascript
// controllers/orderController.js
const orderProducer = require('../kafka/producers/orderProducer');

exports.createOrder = async (req, res) => {
  try {
    const order = await Order.create(req.body);
    
    // Publish event to Kafka
    await orderProducer.publishOrderCreated(order);
    
    res.status(201).json(order);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};
```

## 🔗 Next Steps

Producer setup complete! Let's create consumers!

---

**Key Takeaways:**
- KafkaJS for Node.js integration
- Producers publish messages to topics
- Configure retries and acknowledgments
- Use message keys for partitioning
- Handle errors properly
- Separate producer logic into classes

**Next Tutorial:** [12-Kafka-Consumers.md](./12-Kafka-Consumers.md)
