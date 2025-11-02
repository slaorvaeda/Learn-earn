# 05. MERN Stack Integration - Full-Stack Application

## 🎯 Learning Objectives
- Connect React frontend to Express backend
- Handle CORS and API calls
- Implement state management
- Deploy full-stack application
- Best practices for MERN apps

## 🔗 Connecting Frontend and Backend

### Backend API Setup
```javascript
// backend/src/server.js
const express = require('express');
const cors = require('cors');
const mongoose = require('mongoose');

const app = express();

// CORS configuration
app.use(cors({
  origin: 'http://localhost:3000',
  credentials: true
}));

app.use(express.json());

// Connect to MongoDB
mongoose.connect('mongodb://localhost:27017/mern-app');

// Routes
app.use('/api/users', require('./routes/users'));

app.listen(5000, () => {
  console.log('Backend running on port 5000');
});
```

### Frontend API Service
```javascript
// frontend/src/services/api.js
import axios from 'axios';

const api = axios.create({
  baseURL: 'http://localhost:5000/api',
  headers: {
    'Content-Type': 'application/json'
  }
});

export default api;

// Usage
// api.get('/users')
// api.post('/users', data)
```

### React Component with API
```javascript
// frontend/src/components/UserList.js
import { useState, useEffect } from 'react';
import api from '../services/api';

function UserList() {
  const [users, setUsers] = useState([]);
  
  useEffect(() => {
    fetchUsers();
  }, []);
  
  const fetchUsers = async () => {
    const response = await api.get('/users');
    setUsers(response.data);
  };
  
  return (
    <div>
      {users.map(user => (
        <div key={user._id}>{user.name}</div>
      ))}
    </div>
  );
}
```

## 📊 Complete MERN Application

### Backend Structure
```
backend/
├── src/
│   ├── models/
│   │   └── User.js
│   ├── controllers/
│   │   └── userController.js
│   ├── routes/
│   │   └── users.js
│   └── server.js
└── package.json
```

### Frontend Structure
```
frontend/
├── src/
│   ├── components/
│   │   └── UserList.js
│   ├── pages/
│   │   └── Home.js
│   ├── services/
│   │   └── api.js
│   └── App.js
└── package.json
```

### Full Integration
```javascript
// Complete data flow
// 1. Frontend sends request
const response = await api.post('/users', { name, email });

// 2. Backend receives request
app.post('/users', async (req, res) => {
  const user = await User.create(req.body);
  res.json(user);
});

// 3. Frontend updates UI
setUsers([...users, response.data]);
```

## 🚀 Running Full Stack

### Terminal Setup
```bash
# Terminal 1: Backend
cd backend
npm run dev

# Terminal 2: Frontend
cd frontend
npm start

# Terminal 3: MongoDB
mongod

# All running!
# Backend: http://localhost:5000
# Frontend: http://localhost:3000
```

## 🔗 Next Steps

MERN integrated! Let's add Kafka for real-time!

---

**Key Takeaways:**
- CORS enables frontend-backend communication
- Axios handles HTTP requests
- State management updates UI
- Separate concerns (frontend/backend)
- Full-stack apps need coordination

**Next Tutorial:** [06-React-Hooks.md](./06-React-Hooks.md)
