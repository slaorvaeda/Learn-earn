# 15. Project: Real-time Dashboard with MERN + Kafka

## 🎯 Project Overview
Build a real-time analytics dashboard that:
- Displays live metrics and statistics
- Streams events through Kafka
- Updates UI in real-time
- Shows data visualizations
- Handles multiple concurrent users

## 🏗️ Architecture

```
Data Sources
    ↓
Kafka Producer (Express)
    ↓
Kafka Cluster
    ↓
Kafka Consumer
    ↓
MongoDB (Aggregations)
    ↓
React Dashboard (Live Updates)
```

## 🚀 Complete Project

### 1. Backend Setup
```javascript
// backend/src/server.js
const express = require('express');
const { Kafka } = require('kafkajs');
const mongoose = require('mongoose');

const app = express();
const kafka = new Kafka({ brokers: ['localhost:9092'] });
const producer = kafka.producer();

app.use(express.json());

// Emit events
app.post('/api/events', async (req, res) => {
  const event = req.body;
  
  await producer.send({
    topic: 'dashboard-events',
    messages: [{ value: JSON.stringify(event) }]
  });
  
  res.json({ success: true });
});

app.listen(5000);
```

### 2. Kafka Consumer
```javascript
const consumer = kafka.consumer({ groupId: 'dashboard-consumer' });

await consumer.subscribe({ topic: 'dashboard-events' });

await consumer.run({
  eachMessage: async ({ message }) => {
    const event = JSON.parse(message.value.toString());
    
    // Process event
    await processEvent(event);
    
    // Update database
    await Event.create(event);
  }
});
```

### 3. React Dashboard
```javascript
// frontend/src/components/Dashboard.js
import { useState, useEffect } from 'react';
import io from 'socket.io-client';

const socket = io('http://localhost:5000');

function Dashboard() {
  const [metrics, setMetrics] = useState({ users: 0, events: 0 });
  
  useEffect(() => {
    socket.on('update-metrics', (data) => {
      setMetrics(data);
    });
  }, []);
  
  return (
    <div>
      <h1>Real-time Dashboard</h1>
      <div>Users: {metrics.users}</div>
      <div>Events: {metrics.events}</div>
    </div>
  );
}
```

## 🔗 Next Steps

Dashboard complete! Ready for deployment!

---

**Key Takeaways:**
- Real-time data streaming
- Kafka for event processing
- WebSockets for live updates
- React for dynamic UI
- MongoDB for persistence

**Next Tutorial:** [16-Project-E-commerce.md](./16-Project-E-commerce.md)
