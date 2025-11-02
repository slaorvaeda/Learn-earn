# 02. Express.js - RESTful API Development

## 🎯 Learning Objectives
- Build RESTful APIs with Express
- Create routes and middleware
- Handle requests and responses
- Implement error handling
- Connect Express with MongoDB

## 🚀 What is Express.js?

Express is a fast, unopinionated web framework for Node.js.

**Features:**
- **Minimal**: Simple HTTP server
- **Flexible**: Middleware support
- **Fast**: Lightweight and fast
- **Unopinionated**: No rigid structure

## 📦 Setup Express Project

### Initialize Project
```bash
# Create project
mkdir my-express-api
cd my-express-api

# Initialize Node.js
npm init -y

# Install dependencies
npm install express mongoose dotenv cors

# Install dev dependencies
npm install --save-dev nodemon
```

### Project Structure
```
my-express-api/
├── src/
│   ├── routes/
│   │   ├── users.js
│   │   └── products.js
│   ├── models/
│   │   └── User.js
│   ├── controllers/
│   │   └── userController.js
│   ├── middleware/
│   │   └── errorHandler.js
│   ├── config/
│   │   └── database.js
│   └── server.js
├── .env
├── package.json
└── README.md
```

### Basic Express Server
```javascript
// src/server.js
const express = require('express');
const mongoose = require('mongoose');
require('dotenv').config();

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Connect to MongoDB
mongoose.connect(process.env.MONGODB_URI)
  .then(() => console.log('MongoDB connected'))
  .catch(err => console.error('MongoDB connection error:', err));

// Basic route
app.get('/', (req, res) => {
  res.json({ message: 'Welcome to Express API' });
});

// Start server
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

### .env File
```
PORT=3000
MONGODB_URI=mongodb://localhost:27017/myapp
NODE_ENV=development
```

### Package.json Scripts
```json
{
  "scripts": {
    "start": "node src/server.js",
    "dev": "nodemon src/server.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  }
}
```

## 🛣️ Routes and Endpoints

### Basic Routes
```javascript
// GET request
app.get('/api/users', (req, res) => {
  res.json({ message: 'Get all users' });
});

// POST request
app.post('/api/users', (req, res) => {
  res.json({ message: 'Create user', data: req.body });
});

// GET by ID
app.get('/api/users/:id', (req, res) => {
  res.json({ id: req.params.id });
});

// PUT request
app.put('/api/users/:id', (req, res) => {
  res.json({ message: 'Update user', id: req.params.id, data: req.body });
});

// DELETE request
app.delete('/api/users/:id', (req, res) => {
  res.json({ message: 'Delete user', id: req.params.id });
});
```

### Route Parameters
```javascript
// Single parameter
app.get('/users/:id', (req, res) => {
  const userId = req.params.id;
  res.json({ userId });
});

// Multiple parameters
app.get('/users/:userId/posts/:postId', (req, res) => {
  const { userId, postId } = req.params;
  res.json({ userId, postId });
});

// Query parameters
app.get('/search', (req, res) => {
  const { q, page, limit } = req.query;
  res.json({ q, page, limit });
});
```

### Modular Routes
```javascript
// src/routes/users.js
const express = require('express');
const router = express.Router();

// Routes
router.get('/', (req, res) => {
  res.json({ message: 'Get all users' });
});

router.post('/', (req, res) => {
  res.json({ message: 'Create user' });
});

router.get('/:id', (req, res) => {
  res.json({ id: req.params.id });
});

router.put('/:id', (req, res) => {
  res.json({ message: 'Update user' });
});

router.delete('/:id', (req, res) => {
  res.json({ message: 'Delete user' });
});

module.exports = router;
```

### Use Routes in Main Server
```javascript
// src/server.js
const userRoutes = require('./routes/users');
const productRoutes = require('./routes/products');

// Use routes
app.use('/api/users', userRoutes);
app.use('/api/products', productRoutes);
```

## 🔧 Middleware

### What is Middleware?
Functions that execute during request-response cycle.

### Built-in Middleware
```javascript
// Parse JSON
app.use(express.json());

// Parse URL-encoded data
app.use(express.urlencoded({ extended: true }));

// Serve static files
app.use(express.static('public'));

// CORS
const cors = require('cors');
app.use(cors());
```

### Custom Middleware
```javascript
// Logger middleware
const logger = (req, res, next) => {
  console.log(`${new Date().toISOString()} - ${req.method} ${req.path}`);
  next();
};
app.use(logger);

// Auth middleware
const auth = (req, res, next) => {
  const token = req.headers.authorization;
  if (!token) {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  // Verify token
  next();
};

// Use on specific routes
app.get('/api/protected', auth, (req, res) => {
  res.json({ message: 'Protected route' });
});
```

### Middleware Order Matters
```javascript
// Order 1: CORS
app.use(cors());

// Order 2: Body parsing
app.use(express.json());

// Order 3: Routes
app.use('/api', userRoutes);

// Order 4: Error handler (last)
app.use(errorHandler);
```

## 📊 Controllers Pattern

### Create Controller
```javascript
// src/controllers/userController.js
const User = require('../models/User');

// Get all users
exports.getAllUsers = async (req, res) => {
  try {
    const users = await User.find();
    res.json(users);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};

// Get user by ID
exports.getUserById = async (req, res) => {
  try {
    const user = await User.findById(req.params.id);
    if (!user) {
      return res.status(404).json({ error: 'User not found' });
    }
    res.json(user);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};

// Create user
exports.createUser = async (req, res) => {
  try {
    const user = new User(req.body);
    const savedUser = await user.save();
    res.status(201).json(savedUser);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};

// Update user
exports.updateUser = async (req, res) => {
  try {
    const user = await User.findByIdAndUpdate(
      req.params.id,
      req.body,
      { new: true, runValidators: true }
    );
    if (!user) {
      return res.status(404).json({ error: 'User not found' });
    }
    res.json(user);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};

// Delete user
exports.deleteUser = async (req, res) => {
  try {
    const user = await User.findByIdAndDelete(req.params.id);
    if (!user) {
      return res.status(404).json({ error: 'User not found' });
    }
    res.json({ message: 'User deleted successfully' });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};
```

### Use Controllers in Routes
```javascript
// src/routes/users.js
const express = require('express');
const router = express.Router();
const userController = require('../controllers/userController');

router.get('/', userController.getAllUsers);
router.get('/:id', userController.getUserById);
router.post('/', userController.createUser);
router.put('/:id', userController.updateUser);
router.delete('/:id', userController.deleteUser);

module.exports = router;
```

## 🔍 Advanced Request Handling

### Request Validation
```javascript
const { body, validationResult } = require('express-validator');

// Validation middleware
exports.validateUser = [
  body('name').trim().notEmpty().withMessage('Name is required'),
  body('email').isEmail().withMessage('Valid email required'),
  body('age').isInt({ min: 18, max: 100 }).withMessage('Age must be 18-100'),
  
  (req, res, next) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    next();
  }
];

// Use in route
router.post('/', exports.validateUser, userController.createUser);
```

### Pagination
```javascript
exports.getAllUsers = async (req, res) => {
  try {
    const page = parseInt(req.query.page) || 1;
    const limit = parseInt(req.query.limit) || 10;
    const skip = (page - 1) * limit;

    const users = await User.find()
      .skip(skip)
      .limit(limit);

    const total = await User.countDocuments();

    res.json({
      users,
      pagination: {
        page,
        limit,
        total,
        pages: Math.ceil(total / limit)
      }
    });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};
```

### Filtering and Sorting
```javascript
exports.getAllUsers = async (req, res) => {
  try {
    const { department, sort, order } = req.query;
    const query = {};

    if (department) {
      query.department = department;
    }

    const sortOrder = order === 'desc' ? -1 : 1;
    const sortBy = sort || 'name';

    const users = await User.find(query)
      .sort({ [sortBy]: sortOrder })
      .limit(100);

    res.json(users);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};
```

## ⚠️ Error Handling

### Error Handler Middleware
```javascript
// src/middleware/errorHandler.js
const errorHandler = (err, req, res, next) => {
  console.error(err.stack);

  if (err.name === 'ValidationError') {
    return res.status(400).json({
      error: 'Validation Error',
      details: Object.values(err.errors).map(e => e.message)
    });
  }

  if (err.name === 'CastError') {
    return res.status(400).json({ error: 'Invalid ID format' });
  }

  res.status(err.status || 500).json({
    error: err.message || 'Internal Server Error'
  });
};

module.exports = errorHandler;

// Use in server.js
app.use(errorHandler);
```

### Async Error Handler
```javascript
// Wrap async functions
const asyncHandler = (fn) => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};

// Use
exports.getAllUsers = asyncHandler(async (req, res) => {
  const users = await User.find();
  res.json(users);
});
```

## 🔐 Security Middleware

### Helmet
```javascript
const helmet = require('helmet');
app.use(helmet());
```

### Rate Limiting
```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limit each IP to 100 requests
});

app.use('/api/', limiter);
```

## 🧪 Complete Example

### File Structure
```
my-express-api/
├── src/
│   ├── models/
│   │   └── User.js
│   ├── routes/
│   │   └── users.js
│   ├── controllers/
│   │   └── userController.js
│   ├── middleware/
│   │   └── errorHandler.js
│   └── server.js
├── .env
└── package.json
```

### User Model
```javascript
// src/models/User.js
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    trim: true
  },
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true
  },
  age: {
    type: Number,
    min: 18
  },
  department: {
    type: String,
    enum: ['IT', 'Sales', 'HR']
  }
}, {
  timestamps: true
});

module.exports = mongoose.model('User', userSchema);
```

### Server Setup
```javascript
// src/server.js
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
require('dotenv').config();

const userRoutes = require('./routes/users');
const errorHandler = require('./middleware/errorHandler');

const app = express();

// Middleware
app.use(cors());
app.use(express.json());

// Routes
app.use('/api/users', userRoutes);

// Error handling
app.use(errorHandler);

// Connect to MongoDB
mongoose.connect(process.env.MONGODB_URI)
  .then(() => {
    console.log('MongoDB connected');
    app.listen(process.env.PORT, () => {
      console.log(`Server running on port ${process.env.PORT}`);
    });
  })
  .catch(err => console.error('MongoDB connection error:', err));
```

## 🔗 Next Steps

Express API mastered? Let's build React frontend!

---

**Key Takeaways:**
- Express provides routing and middleware
- Controllers handle business logic
- Middleware processes requests
- Error handling is crucial
- Security middleware is essential
- Separation of concerns improves code

**Next Tutorial:** [03-React-Fundamentals.md](./03-React-Fundamentals.md)
