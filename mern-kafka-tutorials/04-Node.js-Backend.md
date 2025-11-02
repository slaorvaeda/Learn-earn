# 04. Node.js Backend Development

## 🎯 Learning Objectives
- Understand Node.js event loop
- Build RESTful APIs
- Handle async/await patterns
- Implement middleware
- Work with file system
- Handle errors effectively

## 🟢 What is Node.js?

Node.js is a JavaScript runtime built on Chrome's V8 engine.

**Features:**
- **Event-Driven**: Asynchronous I/O
- **Non-Blocking**: Single-threaded event loop
- **NPM**: Package ecosystem
- **Cross-Platform**: Run anywhere
- **Real-time**: Great for streaming

## 📦 Project Setup

```bash
# Initialize project
mkdir nodejs-backend
cd nodejs-backend
npm init -y

# Install dependencies
npm install express mongoose dotenv cors helmet morgan

# Install dev dependencies
npm install --save-dev nodemon
```

### Package.json
```json
{
  "name": "nodejs-backend",
  "version": "1.0.0",
  "scripts": {
    "start": "node src/server.js",
    "dev": "nodemon src/server.js"
  },
  "dependencies": {
    "express": "^4.18.0",
    "mongoose": "^7.0.0"
  }
}
```

## 🔄 Event Loop and Async

### Callbacks
```javascript
const fs = require('fs');

fs.readFile('data.txt', 'utf8', (err, data) => {
  if (err) {
    console.error('Error:', err);
    return;
  }
  console.log(data);
});
```

### Promises
```javascript
const fs = require('fs').promises;

fs.readFile('data.txt', 'utf8')
  .then(data => console.log(data))
  .catch(err => console.error('Error:', err));
```

### Async/Await
```javascript
const fs = require('fs').promises;

async function readFile() {
  try {
    const data = await fs.readFile('data.txt', 'utf8');
    console.log(data);
  } catch (err) {
    console.error('Error:', err);
  }
}

readFile();
```

## 🚀 Express.js Server

### Basic Server
```javascript
const express = require('express');
const app = express();

app.use(express.json());

app.get('/', (req, res) => {
  res.json({ message: 'Hello Node.js!' });
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

### Modular Structure
```javascript
// src/server.js
const express = require('express');
require('dotenv').config();

const app = express();

// Middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Routes
app.use('/api/users', require('./routes/users'));

// Start server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

## 🔗 Next Steps

Node.js backend mastered! Let's integrate everything!

---

**Key Takeaways:**
- Node.js uses event loop for async operations
- Express provides routing and middleware
- Async/await simplifies async code
- Modular structure improves maintainability
- Error handling is crucial

**Next Tutorial:** [05-MERN-Integration.md](./05-MERN-Integration.md)
