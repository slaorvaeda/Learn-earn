# Security - Tutorial 29 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Web Security Fundamentals](#web-security-fundamentals)
3. [Authentication & Authorization](#authentication--authorization)
4. [Data Protection](#data-protection)
5. [API Security](#api-security)
6. [Frontend Security](#frontend-security)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Security is **critical for protecting user data and preventing attacks**. This tutorial covers essential security practices for JavaScript applications.

### What You'll Learn
- ✅ **Web Security Fundamentals** - Common vulnerabilities and protections
- ✅ **Authentication & Authorization** - Secure user management
- ✅ **Data Protection** - Encryption and data security
- ✅ **API Security** - Securing backend APIs
- ✅ **Frontend Security** - Client-side security measures

---

## Web Security Fundamentals

### OWASP Top 10
```javascript
// 1. Injection Prevention
// Bad: SQL injection vulnerability
const getUser = (userId) => {
  const query = `SELECT * FROM users WHERE id = ${userId}`;
  return db.query(query);
};

// Good: Parameterized queries
const getUser = (userId) => {
  const query = 'SELECT * FROM users WHERE id = ?';
  return db.query(query, [userId]);
};

// 2. Broken Authentication
// Bad: Weak password requirements
const validatePassword = (password) => {
  return password.length >= 6;
};

// Good: Strong password requirements
const validatePassword = (password) => {
  const minLength = 8;
  const hasUpperCase = /[A-Z]/.test(password);
  const hasLowerCase = /[a-z]/.test(password);
  const hasNumbers = /\d/.test(password);
  const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);
  
  return password.length >= minLength && 
         hasUpperCase && 
         hasLowerCase && 
         hasNumbers && 
         hasSpecialChar;
};

// 3. Sensitive Data Exposure
// Bad: Storing sensitive data in plain text
const user = {
  name: 'John Doe',
  email: 'john@example.com',
  password: 'plaintextpassword',
  ssn: '123-45-6789'
};

// Good: Encrypting sensitive data
const bcrypt = require('bcryptjs');
const crypto = require('crypto');

const encryptSensitiveData = (data) => {
  const algorithm = 'aes-256-gcm';
  const key = crypto.randomBytes(32);
  const iv = crypto.randomBytes(16);
  
  const cipher = crypto.createCipher(algorithm, key);
  let encrypted = cipher.update(data, 'utf8', 'hex');
  encrypted += cipher.final('hex');
  
  return { encrypted, key: key.toString('hex'), iv: iv.toString('hex') };
};
```

### Input Validation
```javascript
// Input validation utility
class InputValidator {
  static validateEmail(email) {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
  }
  
  static validatePassword(password) {
    const minLength = 8;
    const hasUpperCase = /[A-Z]/.test(password);
    const hasLowerCase = /[a-z]/.test(password);
    const hasNumbers = /\d/.test(password);
    const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);
    
    return {
      isValid: password.length >= minLength && 
               hasUpperCase && 
               hasLowerCase && 
               hasNumbers && 
               hasSpecialChar,
      errors: [
        password.length < minLength && 'Password must be at least 8 characters',
        !hasUpperCase && 'Password must contain uppercase letter',
        !hasLowerCase && 'Password must contain lowercase letter',
        !hasNumbers && 'Password must contain number',
        !hasSpecialChar && 'Password must contain special character'
      ].filter(Boolean)
    };
  }
  
  static sanitizeInput(input) {
    if (typeof input !== 'string') return input;
    
    // Remove HTML tags
    const sanitized = input.replace(/<[^>]*>/g, '');
    
    // Escape special characters
    return sanitized
      .replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
      .replace(/"/g, '&quot;')
      .replace(/'/g, '&#x27;')
      .replace(/\//g, '&#x2F;');
  }
  
  static validateInput(input, rules) {
    const errors = [];
    
    for (const [field, rule] of Object.entries(rules)) {
      const value = input[field];
      
      if (rule.required && (!value || value.trim() === '')) {
        errors.push(`${field} is required`);
        continue;
      }
      
      if (value && rule.minLength && value.length < rule.minLength) {
        errors.push(`${field} must be at least ${rule.minLength} characters`);
      }
      
      if (value && rule.maxLength && value.length > rule.maxLength) {
        errors.push(`${field} must be no more than ${rule.maxLength} characters`);
      }
      
      if (value && rule.pattern && !rule.pattern.test(value)) {
        errors.push(`${field} format is invalid`);
      }
      
      if (value && rule.custom && !rule.custom(value)) {
        errors.push(`${field} validation failed`);
      }
    }
    
    return {
      isValid: errors.length === 0,
      errors
    };
  }
}

// Usage
const userInput = {
  name: 'John Doe',
  email: 'john@example.com',
  password: 'Password123!'
};

const rules = {
  name: { required: true, minLength: 2, maxLength: 50 },
  email: { required: true, pattern: /^[^\s@]+@[^\s@]+\.[^\s@]+$/ },
  password: { required: true, custom: (pwd) => InputValidator.validatePassword(pwd).isValid }
};

const validation = InputValidator.validateInput(userInput, rules);
console.log(validation);
```

---

## Authentication & Authorization

### JWT Implementation
```javascript
// JWT utility
const jwt = require('jsonwebtoken');
const crypto = require('crypto');

class JWTManager {
  constructor(secret, expiresIn = '24h') {
    this.secret = secret;
    this.expiresIn = expiresIn;
  }
  
  generateToken(payload) {
    return jwt.sign(payload, this.secret, { expiresIn: this.expiresIn });
  }
  
  verifyToken(token) {
    try {
      return jwt.verify(token, this.secret);
    } catch (error) {
      throw new Error('Invalid token');
    }
  }
  
  generateRefreshToken() {
    return crypto.randomBytes(64).toString('hex');
  }
  
  hashRefreshToken(token) {
    return crypto.createHash('sha256').update(token).digest('hex');
  }
}

// Authentication middleware
const authenticateToken = (req, res, next) => {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ message: 'Access token required' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    return res.status(403).json({ message: 'Invalid token' });
  }
};

// Role-based authorization
const authorize = (...roles) => {
  return (req, res, next) => {
    if (!req.user) {
      return res.status(401).json({ message: 'Authentication required' });
    }
    
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ message: 'Insufficient permissions' });
    }
    
    next();
  };
};

// Usage
app.get('/api/admin/users', authenticateToken, authorize('admin'), (req, res) => {
  // Only admins can access this route
  res.json({ users: [] });
});
```

### Password Security
```javascript
// Password security utility
const bcrypt = require('bcryptjs');
const crypto = require('crypto');

class PasswordManager {
  static async hashPassword(password, saltRounds = 12) {
    return bcrypt.hash(password, saltRounds);
  }
  
  static async comparePassword(password, hashedPassword) {
    return bcrypt.compare(password, hashedPassword);
  }
  
  static generateSalt() {
    return crypto.randomBytes(16).toString('hex');
  }
  
  static async hashWithSalt(password, salt) {
    return crypto.pbkdf2Sync(password, salt, 100000, 64, 'sha512').toString('hex');
  }
  
  static validatePasswordStrength(password) {
    const minLength = 8;
    const hasUpperCase = /[A-Z]/.test(password);
    const hasLowerCase = /[a-z]/.test(password);
    const hasNumbers = /\d/.test(password);
    const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);
    const hasNoCommonPatterns = !this.hasCommonPatterns(password);
    
    const score = [
      password.length >= minLength,
      hasUpperCase,
      hasLowerCase,
      hasNumbers,
      hasSpecialChar,
      hasNoCommonPatterns
    ].filter(Boolean).length;
    
    return {
      score,
      strength: score < 3 ? 'weak' : score < 5 ? 'medium' : 'strong',
      isValid: score >= 4
    };
  }
  
  static hasCommonPatterns(password) {
    const commonPatterns = [
      /123456/,
      /password/i,
      /qwerty/i,
      /abc123/i,
      /admin/i
    ];
    
    return commonPatterns.some(pattern => pattern.test(password));
  }
}

// Usage
const password = 'MySecurePassword123!';
const strength = PasswordManager.validatePasswordStrength(password);
console.log(strength); // { score: 6, strength: 'strong', isValid: true }

const hashedPassword = await PasswordManager.hashPassword(password);
console.log(hashedPassword);
```

---

## Data Protection

### Encryption
```javascript
// Encryption utility
const crypto = require('crypto');

class EncryptionManager {
  constructor(algorithm = 'aes-256-gcm') {
    this.algorithm = algorithm;
  }
  
  generateKey() {
    return crypto.randomBytes(32);
  }
  
  generateIV() {
    return crypto.randomBytes(16);
  }
  
  encrypt(text, key, iv) {
    const cipher = crypto.createCipher(this.algorithm, key);
    let encrypted = cipher.update(text, 'utf8', 'hex');
    encrypted += cipher.final('hex');
    
    const authTag = cipher.getAuthTag();
    
    return {
      encrypted,
      authTag: authTag.toString('hex')
    };
  }
  
  decrypt(encryptedData, key, iv, authTag) {
    const decipher = crypto.createDecipher(this.algorithm, key);
    decipher.setAuthTag(Buffer.from(authTag, 'hex'));
    
    let decrypted = decipher.update(encryptedData, 'hex', 'utf8');
    decrypted += decipher.final('utf8');
    
    return decrypted;
  }
  
  hash(text, algorithm = 'sha256') {
    return crypto.createHash(algorithm).update(text).digest('hex');
  }
  
  hmac(text, secret, algorithm = 'sha256') {
    return crypto.createHmac(algorithm, secret).update(text).digest('hex');
  }
}

// Usage
const encryption = new EncryptionManager();
const key = encryption.generateKey();
const iv = encryption.generateIV();

const data = 'Sensitive information';
const encrypted = encryption.encrypt(data, key, iv);
console.log('Encrypted:', encrypted);

const decrypted = encryption.decrypt(encrypted.encrypted, key, iv, encrypted.authTag);
console.log('Decrypted:', decrypted);
```

### Data Sanitization
```javascript
// Data sanitization utility
class DataSanitizer {
  static sanitizeString(input) {
    if (typeof input !== 'string') return input;
    
    return input
      .replace(/<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi, '')
      .replace(/<[^>]*>/g, '')
      .replace(/javascript:/gi, '')
      .replace(/on\w+\s*=/gi, '')
      .trim();
  }
  
  static sanitizeObject(obj) {
    const sanitized = {};
    
    for (const [key, value] of Object.entries(obj)) {
      if (typeof value === 'string') {
        sanitized[key] = this.sanitizeString(value);
      } else if (typeof value === 'object' && value !== null) {
        sanitized[key] = this.sanitizeObject(value);
      } else {
        sanitized[key] = value;
      }
    }
    
    return sanitized;
  }
  
  static validateEmail(email) {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
  }
  
  static validateURL(url) {
    try {
      const urlObj = new URL(url);
      return ['http:', 'https:'].includes(urlObj.protocol);
    } catch {
      return false;
    }
  }
  
  static escapeHTML(input) {
    if (typeof input !== 'string') return input;
    
    return input
      .replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
      .replace(/"/g, '&quot;')
      .replace(/'/g, '&#x27;')
      .replace(/\//g, '&#x2F;');
  }
}

// Usage
const userInput = {
  name: '<script>alert("XSS")</script>John Doe',
  email: 'john@example.com',
  website: 'https://example.com',
  bio: 'This is a <b>test</b> bio'
};

const sanitized = DataSanitizer.sanitizeObject(userInput);
console.log(sanitized);
```

---

## API Security

### Rate Limiting
```javascript
// Rate limiting middleware
const rateLimit = require('express-rate-limit');
const RedisStore = require('rate-limit-redis');
const Redis = require('ioredis');

const redis = new Redis({
  host: process.env.REDIS_HOST,
  port: process.env.REDIS_PORT,
  password: process.env.REDIS_PASSWORD
});

// General rate limiting
const generalLimiter = rateLimit({
  store: new RedisStore({
    sendCommand: (...args) => redis.call(...args),
  }),
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP, please try again later.',
  standardHeaders: true,
  legacyHeaders: false,
});

// Strict rate limiting for auth endpoints
const authLimiter = rateLimit({
  store: new RedisStore({
    sendCommand: (...args) => redis.call(...args),
  }),
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // limit each IP to 5 requests per windowMs
  message: 'Too many authentication attempts, please try again later.',
  standardHeaders: true,
  legacyHeaders: false,
});

// Apply rate limiting
app.use('/api/', generalLimiter);
app.use('/api/auth/', authLimiter);
```

### CORS Configuration
```javascript
// CORS configuration
const cors = require('cors');

const corsOptions = {
  origin: (origin, callback) => {
    // Allow requests with no origin (mobile apps, etc.)
    if (!origin) return callback(null, true);
    
    const allowedOrigins = [
      'http://localhost:3000',
      'https://yourdomain.com',
      'https://www.yourdomain.com'
    ];
    
    if (allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true,
  optionsSuccessStatus: 200,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Authorization', 'X-Requested-With']
};

app.use(cors(corsOptions));
```

### Security Headers
```javascript
// Security headers middleware
const helmet = require('helmet');

app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      scriptSrc: ["'self'"],
      imgSrc: ["'self'", "data:", "https:"],
      connectSrc: ["'self'"],
      fontSrc: ["'self'"],
      objectSrc: ["'none'"],
      mediaSrc: ["'self'"],
      frameSrc: ["'none'"],
    },
  },
  crossOriginEmbedderPolicy: false,
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  }
}));

// Additional security headers
app.use((req, res, next) => {
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Referrer-Policy', 'strict-origin-when-cross-origin');
  next();
});
```

---

## Frontend Security

### XSS Prevention
```javascript
// XSS prevention utility
class XSSPrevention {
  static sanitizeHTML(input) {
    if (typeof input !== 'string') return input;
    
    const div = document.createElement('div');
    div.textContent = input;
    return div.innerHTML;
  }
  
  static escapeHTML(input) {
    if (typeof input !== 'string') return input;
    
    return input
      .replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
      .replace(/"/g, '&quot;')
      .replace(/'/g, '&#x27;')
      .replace(/\//g, '&#x2F;');
  }
  
  static validateInput(input, type) {
    switch (type) {
      case 'email':
        return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(input);
      case 'url':
        try {
          new URL(input);
          return true;
        } catch {
          return false;
        }
      case 'alphanumeric':
        return /^[a-zA-Z0-9]+$/.test(input);
      default:
        return true;
    }
  }
  
  static sanitizeURL(url) {
    try {
      const urlObj = new URL(url);
      if (!['http:', 'https:'].includes(urlObj.protocol)) {
        return null;
      }
      return urlObj.toString();
    } catch {
      return null;
    }
  }
}

// Usage
const userInput = '<script>alert("XSS")</script>Hello World';
const sanitized = XSSPrevention.sanitizeHTML(userInput);
console.log(sanitized); // &lt;script&gt;alert("XSS")&lt;/script&gt;Hello World
```

### CSRF Protection
```javascript
// CSRF protection utility
class CSRFProtection {
  static generateToken() {
    const array = new Uint8Array(32);
    crypto.getRandomValues(array);
    return Array.from(array, byte => byte.toString(16).padStart(2, '0')).join('');
  }
  
  static validateToken(token, sessionToken) {
    return token === sessionToken;
  }
  
  static addTokenToForm(form) {
    const token = this.generateToken();
    const input = document.createElement('input');
    input.type = 'hidden';
    input.name = '_csrf';
    input.value = token;
    form.appendChild(input);
    return token;
  }
  
  static addTokenToHeaders() {
    const token = this.generateToken();
    return {
      'X-CSRF-Token': token
    };
  }
}

// Usage in form submission
const form = document.getElementById('myForm');
const token = CSRFProtection.addTokenToForm(form);

form.addEventListener('submit', async (e) => {
  e.preventDefault();
  
  const formData = new FormData(form);
  const response = await fetch('/api/submit', {
    method: 'POST',
    body: formData,
    headers: {
      'X-CSRF-Token': token
    }
  });
  
  if (response.ok) {
    console.log('Form submitted successfully');
  }
});
```

---

## Practice Exercises

### Exercise 1: Secure Authentication System
```javascript
// Secure authentication system
const express = require('express');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
const rateLimit = require('express-rate-limit');
const helmet = require('helmet');

const app = express();

// Security middleware
app.use(helmet());
app.use(express.json({ limit: '10mb' }));

// Rate limiting
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // limit each IP to 5 requests per windowMs
  message: 'Too many authentication attempts, please try again later.'
});

// User storage (in production, use a database)
const users = new Map();

// Password validation
const validatePassword = (password) => {
  const minLength = 8;
  const hasUpperCase = /[A-Z]/.test(password);
  const hasLowerCase = /[a-z]/.test(password);
  const hasNumbers = /\d/.test(password);
  const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);
  
  return password.length >= minLength && 
         hasUpperCase && 
         hasLowerCase && 
         hasNumbers && 
         hasSpecialChar;
};

// Input sanitization
const sanitizeInput = (input) => {
  if (typeof input !== 'string') return input;
  return input.replace(/<[^>]*>/g, '').trim();
};

// Register endpoint
app.post('/api/auth/register', authLimiter, async (req, res) => {
  try {
    const { name, email, password } = req.body;
    
    // Validate input
    if (!name || !email || !password) {
      return res.status(400).json({ message: 'All fields are required' });
    }
    
    if (!validatePassword(password)) {
      return res.status(400).json({ 
        message: 'Password must be at least 8 characters with uppercase, lowercase, number, and special character' 
      });
    }
    
    // Sanitize input
    const sanitizedName = sanitizeInput(name);
    const sanitizedEmail = sanitizeInput(email);
    
    // Check if user exists
    if (users.has(sanitizedEmail)) {
      return res.status(400).json({ message: 'User already exists' });
    }
    
    // Hash password
    const hashedPassword = await bcrypt.hash(password, 12);
    
    // Create user
    const user = {
      id: Date.now(),
      name: sanitizedName,
      email: sanitizedEmail,
      password: hashedPassword,
      createdAt: new Date()
    };
    
    users.set(sanitizedEmail, user);
    
    // Generate token
    const token = jwt.sign(
      { userId: user.id, email: user.email },
      process.env.JWT_SECRET,
      { expiresIn: '24h' }
    );
    
    res.status(201).json({
      message: 'User created successfully',
      token,
      user: {
        id: user.id,
        name: user.name,
        email: user.email
      }
    });
  } catch (error) {
    console.error('Registration error:', error);
    res.status(500).json({ message: 'Server error' });
  }
});

// Login endpoint
app.post('/api/auth/login', authLimiter, async (req, res) => {
  try {
    const { email, password } = req.body;
    
    // Validate input
    if (!email || !password) {
      return res.status(400).json({ message: 'Email and password are required' });
    }
    
    // Sanitize input
    const sanitizedEmail = sanitizeInput(email);
    
    // Find user
    const user = users.get(sanitizedEmail);
    if (!user) {
      return res.status(401).json({ message: 'Invalid credentials' });
    }
    
    // Check password
    const isMatch = await bcrypt.compare(password, user.password);
    if (!isMatch) {
      return res.status(401).json({ message: 'Invalid credentials' });
    }
    
    // Generate token
    const token = jwt.sign(
      { userId: user.id, email: user.email },
      process.env.JWT_SECRET,
      { expiresIn: '24h' }
    );
    
    res.json({
      message: 'Login successful',
      token,
      user: {
        id: user.id,
        name: user.name,
        email: user.email
      }
    });
  } catch (error) {
    console.error('Login error:', error);
    res.status(500).json({ message: 'Server error' });
  }
});

// Protected route
app.get('/api/profile', (req, res) => {
  const token = req.headers.authorization?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ message: 'Access token required' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    const user = Array.from(users.values()).find(u => u.id === decoded.userId);
    
    if (!user) {
      return res.status(404).json({ message: 'User not found' });
    }
    
    res.json({
      user: {
        id: user.id,
        name: user.name,
        email: user.email,
        createdAt: user.createdAt
      }
    });
  } catch (error) {
    res.status(403).json({ message: 'Invalid token' });
  }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

### Exercise 2: Input Validation Middleware
```javascript
// Input validation middleware
const { body, validationResult } = require('express-validator');

const validateUser = [
  body('name')
    .trim()
    .isLength({ min: 2, max: 50 })
    .withMessage('Name must be between 2 and 50 characters')
    .matches(/^[a-zA-Z\s]+$/)
    .withMessage('Name can only contain letters and spaces'),
  
  body('email')
    .isEmail()
    .withMessage('Must be a valid email')
    .normalizeEmail(),
  
  body('password')
    .isLength({ min: 8 })
    .withMessage('Password must be at least 8 characters')
    .matches(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]/)
    .withMessage('Password must contain uppercase, lowercase, number, and special character'),
  
  body('age')
    .optional()
    .isInt({ min: 0, max: 120 })
    .withMessage('Age must be between 0 and 120'),
  
  body('website')
    .optional()
    .isURL()
    .withMessage('Must be a valid URL'),
  
  (req, res, next) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({
        message: 'Validation failed',
        errors: errors.array()
      });
    }
    next();
  }
];

// Usage
app.post('/api/users', validateUser, (req, res) => {
  // Handle validated user data
  res.json({ message: 'User created successfully', user: req.body });
});
```

---

## Summary

### Key Concepts Learned
- ✅ **Web Security Fundamentals** - OWASP Top 10, input validation
- ✅ **Authentication & Authorization** - JWT, password security
- ✅ **Data Protection** - Encryption, data sanitization
- ✅ **API Security** - Rate limiting, CORS, security headers
- ✅ **Frontend Security** - XSS prevention, CSRF protection

### Best Practices
- Always validate and sanitize input
- Use strong password requirements
- Implement proper authentication and authorization
- Use HTTPS in production
- Keep dependencies updated
- Implement rate limiting
- Use security headers
- Encrypt sensitive data

### Common Mistakes
- Not validating input
- Using weak passwords
- Storing sensitive data in plain text
- Not implementing rate limiting
- Missing security headers
- Not sanitizing output
- Using outdated dependencies

---

**Excellent! You've mastered security. Ready for Career Development? 🚀**

**Next Tutorial:** [Career Development](./30-career-development.md)
