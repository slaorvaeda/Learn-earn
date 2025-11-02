# 14. MERN + Kafka Integration - Real-time Applications

## 🎯 Learning Objectives
- Integrate Kafka with MERN stack
- Build real-time event-driven applications
- Implement producers in Express APIs
- Create consumers for background processing
- Handle event-driven UI updates

## 🏗️ Architecture Overview

```
React Frontend
    ↓ (HTTP)
Express API (Producers)
    ↓ (Kafka Events)
Kafka Cluster
    ↓ (Kafka Events)
Background Consumers
    ↓ (Updates)
MongoDB Database
```

## 📦 Project Setup

### Complete Project Structure
```
mern-kafka-app/
├── backend/
│   ├── src/
│   │   ├── config/
│   │   │   ├── database.js
│   │   │   └── kafka.js
│   │   ├── kafka/
│   │   │   ├── producers/
│   │   │   │   └── orderProducer.js
│   │   │   └── consumers/
│   │   │       └── orderConsumer.js
│   │   ├── models/
│   │   ├── routes/
│   │   ├── controllers/
│   │   └── server.js
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── services/
│   │   └── App.js
│   └── package.json
└── docker-compose.yml
```

## 🔧 Kafka Configuration

### Backend Kafka Setup
```javascript
// backend/src/config/kafka.js
const { Kafka } = require('kafkajs');

const kafka = new Kafka({
  brokers: process.env.KAFKA_BROKERS ? 
    process.env.KAFKA_BROKERS.split(',') : 
    ['localhost:9092'],
  clientId: 'mern-kafka-app'
});

// Producers
const orderProducer = kafka.producer({
  maxInFlightRequests: 1,
  idempotent: true
});

// Consumers
const orderConsumer = kafka.consumer({ 
  groupId: 'order-processing-group' 
});

module.exports = {
  kafka,
  orderProducer,
  orderConsumer
};
```

## 📨 Producer Integration

### Express API with Kafka
```javascript
// backend/src/controllers/orderController.js
const Order = require('../models/Order');
const { publishOrderEvent } = require('../kafka/producers');

exports.createOrder = async (req, res) => {
  try {
    const order = await Order.create(req.body);
    
    // Publish event to Kafka
    await publishOrderEvent({
      eventType: 'order-created',
      orderId: order._id,
      customerId: order.customerId,
      total: order.total,
      timestamp: new Date().toISOString()
    });
    
    res.status(201).json(order);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};

exports.updateOrder = async (req, res) => {
  try {
    const order = await Order.findByIdAndUpdate(
      req.params.id,
      req.body,
      { new: true }
    );
    
    // Publish update event
    await publishOrderEvent({
      eventType: 'order-updated',
      orderId: order._id,
      status: order.status,
      timestamp: new Date().toISOString()
    });
    
    res.json(order);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};
```

## 📥 Consumer Integration

### Background Consumer
```javascript
// backend/src/kafka/consumers/orderConsumer.js
const { orderConsumer, orderProducer } = require('../config/kafka');
const Order = require('../../models/Order');
const Inventory = require('../../models/Inventory');

async function startOrderConsumer() {
  await orderConsumer.connect();
  await orderConsumer.subscribe({ topic: 'order-events' });
  
  await orderConsumer.run({
    eachMessage: async ({ message }) => {
      try {
        const event = JSON.parse(message.value.toString());
        console.log('Processing event:', event.eventType);
        
        switch(event.eventType) {
          case 'order-created':
            await handleOrderCreated(event);
            break;
          case 'order-updated':
            await handleOrderUpdated(event);
            break;
        }
      } catch (error) {
        console.error('Consumer error:', error);
      }
    }
  });
}

async function handleOrderCreated(event) {
  // Process order: update inventory, calculate shipping, etc.
  console.log('Processing order:', event.orderId);
  
  // Example: Update inventory
  // await Inventory.decreaseStock(event.items);
  
  // Send event to shipping service
  await publishShippingEvent(event);
}

async function handleOrderUpdated(event) {
  // Handle order updates
  console.log('Order updated:', event.orderId);
}

module.exports = { startOrderConsumer };
```

## 🔄 Real-time Updates with Socket.io

### WebSocket Integration
```javascript
// backend/src/server.js
const express = require('express');
const http = require('http');
const { Server } = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = new Server(server, {
  cors: { origin: "http://localhost:3000" }
});

// Socket connection
io.on('connection', (socket) => {
  console.log('Client connected:', socket.id);
  
  socket.on('disconnect', () => {
    console.log('Client disconnected:', socket.id);
  });
});

// Forward Kafka events to WebSocket
orderConsumer.on('message', (message) => {
  const event = JSON.parse(message.value.toString());
  io.emit('order-event', event);  // Broadcast to all clients
});

module.exports = { io };
```

## 🧪 Complete Integration Example

### Order Service with Kafka
```javascript
// Complete flow example
// backend/src/controllers/orderController.js
const Order = require('../models/Order');
const { publishOrderEvent } = require('../kafka');

exports.createOrder = async (req, res) => {
  const session = await mongoose.startSession();
  session.startTransaction();
  
  try {
    // 1. Create order in MongoDB
    const order = await Order.create([req.body], { session });
    
    // 2. Publish event to Kafka
    await publishOrderEvent({
      eventType: 'order-created',
      orderId: order[0]._id,
      ...order[0]
    });
    
    // 3. Commit transaction
    await session.commitTransaction();
    
    res.status(201).json(order[0]);
  } catch (error) {
    await session.abortTransaction();
    res.status(400).json({ error: error.message });
  } finally {
    session.endSession();
  }
};
```

## 🔗 Next Steps

MERN+Kafka integrated! Let's build real projects!

---

**Key Takeaways:**
- Kafka connects backend services
- Producers publish events from APIs
- Consumers process events async
- WebSockets provide real-time updates
- Event-driven architecture decouples services

**Next Tutorial:** [15-Project-Real-time-Dashboard.md](./15-Project-Real-time-Dashboard.md)
