# 08. Real-time Features with WebSockets

## 🎯 Learning Objectives
- Implement WebSocket connections
- Use Socket.io for real-time communication
- Broadcast messages to multiple clients
- Handle online/offline status
- Integrate with MERN stack

## 🔌 WebSocket Basics

WebSockets provide full-duplex communication.

**Benefits:**
- **Low Latency**: Real-time updates
- **Persistent**: Keep connection open
- **Bi-directional**: Client ↔ Server
- **Efficient**: Less overhead than HTTP

## 📦 Socket.io Setup

### Install
```bash
npm install socket.io
```

### Backend Setup
```javascript
const express = require('express');
const http = require('http');
const { Server } = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = new Server(server, {
  cors: { origin: 'http://localhost:3000' }
});

io.on('connection', (socket) => {
  console.log('User connected:', socket.id);
  
  socket.on('disconnect', () => {
    console.log('User disconnected:', socket.id);
  });
});

server.listen(5000, () => {
  console.log('Server running on port 5000');
});
```

### Frontend Setup
```javascript
import { io } from 'socket.io-client';

const socket = io('http://localhost:5000');

socket.on('connect', () => {
  console.log('Connected to server');
});

socket.on('message', (data) => {
  console.log('Received:', data);
});

socket.emit('message', 'Hello Server');
```

## 💬 Chat Application Example

### Backend
```javascript
const io = new Server(server, {
  cors: { origin: '*' }
});

io.on('connection', (socket) => {
  console.log('User connected:', socket.id);
  
  socket.on('join-room', (roomId) => {
    socket.join(roomId);
    console.log(`User ${socket.id} joined room ${roomId}`);
  });
  
  socket.on('send-message', (data) => {
    io.to(data.roomId).emit('receive-message', data);
  });
  
  socket.on('disconnect', () => {
    console.log('User disconnected');
  });
});
```

### Frontend
```javascript
const [messages, setMessages] = useState([]);
const socket = io('http://localhost:5000');

useEffect(() => {
  socket.on('receive-message', (data) => {
    setMessages(prev => [...prev, data]);
  });
  
  return () => socket.disconnect();
}, []);

const sendMessage = (text, roomId) => {
  socket.emit('send-message', { text, roomId });
};
```

## 🔗 Next Steps

Real-time features mastered! Let's add testing!

---

**Key Takeaways:**
- WebSockets enable real-time communication
- Socket.io simplifies WebSocket management
- Rooms for group communication
- Emit events for messaging
- Listen for incoming events

**Next Tutorial:** [09-Testing.md](./09-Testing.md)
