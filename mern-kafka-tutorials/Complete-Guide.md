# Complete MERN + Kafka Guide

## 🎯 All-in-One Reference Guide

This comprehensive guide covers everything you need to master MERN Stack with Kafka.

---

## 📚 MERN Stack Components

### MongoDB (M)
```javascript
// Connection
const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost:27017/myapp');

// Schema
const userSchema = new mongoose.Schema({
  name: String,
  email: String
});

// Model
const User = mongoose.model('User', userSchema);

// CRUD
await User.create({ name: 'John', email: 'john@example.com' });
const users = await User.find();
await User.findByIdAndUpdate(id, { name: 'Jane' });
await User.findByIdAndDelete(id);
```

### Express (E)
```javascript
const express = require('express');
const app = express();

app.use(express.json());

// Routes
app.get('/api/users', async (req, res) => {
  const users = await User.find();
  res.json(users);
});

app.listen(3000);
```

### React (R)
```javascript
// Component
import { useState, useEffect } from 'react';
import axios from 'axios';

function UserList() {
  const [users, setUsers] = useState([]);
  
  useEffect(() => {
    axios.get('http://localhost:5000/api/users')
      .then(res => setUsers(res.data));
  }, []);
  
  return (
    <div>
      {users.map(user => <div key={user._id}>{user.name}</div>)}
    </div>
  );
}
```

### Node.js (N)
```javascript
// Server setup
const http = require('http');
const server = http.createServer((req, res) => {
  res.end('Hello Node.js');
});
server.listen(3000);
```

---

## ⚡ Kafka Integration

### Producer
```javascript
const { Kafka } = require('kafkajs');

const kafka = new Kafka({ brokers: ['localhost:9092'] });
const producer = kafka.producer();

await producer.connect();

// Send event
await producer.send({
  topic: 'events',
  messages: [{ value: JSON.stringify(event) }]
});
```

### Consumer
```javascript
const consumer = kafka.consumer({ groupId: 'my-group' });

await consumer.connect();
await consumer.subscribe({ topic: 'events' });

await consumer.run({
  eachMessage: async ({ message }) => {
    const event = JSON.parse(message.value.toString());
    // Process event
  }
});
```

---

## 🏗️ Complete Application Structure

```
mern-kafka-app/
├── backend/
│   ├── src/
│   │   ├── models/
│   │   ├── routes/
│   │   ├── controllers/
│   │   ├── kafka/
│   │   │   ├── producers.js
│   │   │   └── consumers.js
│   │   └── server.js
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── services/
│   │   └── App.js
│   └── package.json
├── kafka/
│   └── config/
├── docker-compose.yml
└── README.md
```

---

## 🎯 Key Concepts

**MongoDB**: Document database, flexible schema  
**Express**: Web framework, REST APIs  
**React**: UI library, component-based  
**Node.js**: JavaScript runtime  
**Kafka**: Event streaming platform  

**Integration**: Real-time, scalable, event-driven architecture

---

**You now have complete MERN + Kafka knowledge!** 🎉

Build amazing applications with this powerful stack!
