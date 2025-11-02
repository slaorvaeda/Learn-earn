# 07. Authentication and Security

## 🎯 Learning Objectives
- Implement JWT authentication
- Secure user registration and login
- Use bcrypt for password hashing
- Protect routes with middleware
- Handle token refresh

## 🔐 Authentication Flow

```
User Registration
    ↓
Password Hashed (bcrypt)
    ↓
User Saved to DB
    ↓
JWT Token Generated
    ↓
Token Sent to Client
    ↓
Token Stored (localStorage/cookie)
```

## 🔑 Backend Authentication

### Install Dependencies
```bash
npm install jsonwebtoken bcryptjs
npm install --save-dev @types/jsonwebtoken @types/bcryptjs
```

### User Model
```javascript
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');

const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true, minlength: 6 }
}, { timestamps: true });

// Hash password before saving
userSchema.pre('save', async function(next) {
  if (!this.isModified('password')) return next();
  this.password = await bcrypt.hash(this.password, 10);
  next();
});

// Compare password method
userSchema.methods.comparePassword = async function(candidatePassword) {
  return bcrypt.compare(candidatePassword, this.password);
};

module.exports = mongoose.model('User', userSchema);
```

### Auth Controller
```javascript
const User = require('../models/User');
const jwt = require('jsonwebtoken');

// Generate JWT
const generateToken = (userId) => {
  return jwt.sign({ userId }, process.env.JWT_SECRET, { expiresIn: '7d' });
};

// Register
exports.register = async (req, res) => {
  try {
    const { name, email, password } = req.body;
    
    const existingUser = await User.findOne({ email });
    if (existingUser) {
      return res.status(400).json({ error: 'Email already registered' });
    }
    
    const user = await User.create({ name, email, password });
    const token = generateToken(user._id);
    
    res.status(201).json({
      message: 'User registered successfully',
      token,
      user: { id: user._id, name: user.name, email: user.email }
    });
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};

// Login
exports.login = async (req, res) => {
  try {
    const { email, password } = req.body;
    
    const user = await User.findOne({ email });
    if (!user) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }
    
    const isMatch = await user.comparePassword(password);
    if (!isMatch) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }
    
    const token = generateToken(user._id);
    
    res.json({
      message: 'Login successful',
      token,
      user: { id: user._id, name: user.name, email: user.email }
    });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};
```

### Protect Routes
```javascript
const jwt = require('jsonwebtoken');

const authMiddleware = async (req, res, next) => {
  try {
    const token = req.headers.authorization?.replace('Bearer ', '');
    
    if (!token) {
      return res.status(401).json({ error: 'Authentication required' });
    }
    
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.userId = decoded.userId;
    next();
  } catch (error) {
    res.status(401).json({ error: 'Invalid token' });
  }
};

// Use middleware
router.get('/profile', authMiddleware, userController.getProfile);
```

## 🔗 Next Steps

Authentication mastered! Let's add real-time features!

---

**Key Takeaways:**
- JWT for stateless authentication
- bcrypt hashes passwords
- Middleware protects routes
- Secure token generation
- Handle auth errors gracefully

**Next Tutorial:** [08-Real-time-Features.md](./08-Real-time-Features.md)
