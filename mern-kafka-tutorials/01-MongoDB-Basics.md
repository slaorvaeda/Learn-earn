# 01. MongoDB Basics - Document Database

## 🎯 Learning Objectives
- Understand NoSQL and document databases
- Install and set up MongoDB
- Learn CRUD operations
- Work with collections and documents
- Use Mongoose ODM

## 📊 What is MongoDB?

MongoDB is a **NoSQL document database** that stores data in JSON-like documents.

**Key Features:**
- **Flexible Schema**: No fixed structure
- **Document-Based**: JSON-like documents
- **Scalable**: Horizontal scaling
- **Fast**: High-performance queries
- **Rich Queries**: Powerful query language

## 🔑 Key Concepts

### Database → Collection → Document

```
Database: company
└── Collection: employees
    ├── Document: { name: "Alice", department: "IT", salary: 80000 }
    ├── Document: { name: "Bob", department: "Sales", salary: 55000 }
    └── Document: { name: "Charlie", title: "Manager", team: ["John", "Jane"] }
```

### Document Structure
```json
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "name": "Alice Johnson",
  "email": "alice@example.com",
  "age": 30,
  "department": "IT",
  "skills": ["JavaScript", "Node.js", "MongoDB"],
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "zip": "10001"
  },
  "createdAt": ISODate("2024-01-15T10:30:00Z")
}
```

## 🚀 Installation

### macOS
```bash
# Install with Homebrew
brew tap mongodb/brew
brew install mongodb-community

# Start MongoDB
brew services start mongodb-community

# Stop MongoDB
brew services stop mongodb-community
```

### Linux
```bash
# Add MongoDB repository
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -

# Install
sudo apt-get update
sudo apt-get install -y mongodb-org

# Start
sudo systemctl start mongod
```

### Windows
```
Download MongoDB Community Server from:
https://www.mongodb.com/try/download/community

Install and follow setup wizard.
```

### Verify Installation
```bash
# Start MongoDB shell
mongosh

# Or use legacy
mongo

# Check version
db.version()
```

## 📝 Basic Operations

### Create Database
```javascript
// Switch to or create database
use mydatabase

// Show current database
db

// List all databases
show dbs
```

### Collections
```javascript
// Create collection (automatic)
db.users.insertOne({ name: "Test User" })

// Show collections
show collections

// Count documents
db.users.countDocuments()

// Drop collection
db.users.drop()
```

## ➕ CRUD Operations

### Create (Insert)

#### insertOne
```javascript
// Insert single document
db.users.insertOne({
  name: "Alice",
  email: "alice@example.com",
  age: 25,
  department: "IT"
})
```

#### insertMany
```javascript
// Insert multiple documents
db.users.insertMany([
  { name: "Bob", email: "bob@example.com", age: 30, department: "Sales" },
  { name: "Charlie", email: "charlie@example.com", age: 28, department: "IT" },
  { name: "Diana", email: "diana@example.com", age: 32, department: "HR" }
])
```

### Read (Find)

#### Find All
```javascript
// Get all documents
db.users.find()

// Pretty print
db.users.find().pretty()

// Count
db.users.find().count()
```

#### Find with Filter
```javascript
// Find specific user
db.users.find({ name: "Alice" })

// Find by age
db.users.find({ age: { $gt: 25 } })

// Multiple conditions (AND)
db.users.find({ department: "IT", age: { $gt: 25 } })

// OR condition
db.users.find({ $or: [
  { department: "IT" },
  { department: "Sales" }
]})
```

#### Find One
```javascript
// Get first matching document
db.users.findOne({ department: "IT" })
```

#### Sorting and Limits
```javascript
// Sort ascending
db.users.find().sort({ age: 1 })

// Sort descending
db.users.find().sort({ age: -1 })

// Limit results
db.users.find().limit(5)

// Skip and limit (pagination)
db.users.find().skip(10).limit(5)
```

### Update

#### updateOne
```javascript
// Update single document
db.users.updateOne(
  { name: "Alice" },
  { $set: { age: 26, email: "alice.new@example.com" } }
)
```

#### updateMany
```javascript
// Update multiple documents
db.users.updateMany(
  { department: "IT" },
  { $set: { manager: "John" } }
)
```

#### Update Operators
```javascript
// $set: Set field value
db.users.updateOne({ name: "Alice" }, { $set: { age: 26 } })

// $inc: Increment value
db.users.updateOne({ name: "Alice" }, { $inc: { age: 1 } })

// $push: Add to array
db.users.updateOne({ name: "Alice" }, { $push: { skills: "React" } })

// $pull: Remove from array
db.users.updateOne({ name: "Alice" }, { $pull: { skills: "React" } })
```

### Delete

#### deleteOne
```javascript
// Delete first matching document
db.users.deleteOne({ name: "Charlie" })
```

#### deleteMany
```javascript
// Delete all matching documents
db.users.deleteMany({ department: "HR" })

// Delete all documents in collection
db.users.deleteMany({})
```

## 🔍 Query Operators

### Comparison Operators
```javascript
// Greater than
db.users.find({ age: { $gt: 25 } })

// Less than
db.users.find({ age: { $lt: 30 } })

// Greater than or equal
db.users.find({ age: { $gte: 25 } })

// Less than or equal
db.users.find({ age: { $lte: 30 } })

// Not equal
db.users.find({ age: { $ne: 25 } })

// In array
db.users.find({ department: { $in: ["IT", "Sales"] } })

// Not in array
db.users.find({ department: { $nin: ["HR", "Finance"] } })
```

### Logical Operators
```javascript
// AND
db.users.find({ $and: [
  { department: "IT" },
  { age: { $gt: 25 } }
]})

// OR
db.users.find({ $or: [
  { department: "IT" },
  { age: { $gt: 30 } }
]})

// NOT
db.users.find({ department: { $not: { $eq: "HR" } } })
```

### Array Operators
```javascript
// Contains element
db.users.find({ skills: "JavaScript" })

// Contains all
db.users.find({ skills: { $all: ["JavaScript", "MongoDB"] } })

// Array size
db.users.find({ skills: { $size: 3 } })

// Element at index
db.users.find({ "skills.0": "JavaScript" })
```

## 🔧 Using Mongoose (Node.js)

### Install Mongoose
```bash
npm install mongoose
```

### Basic Setup
```javascript
// server.js
const mongoose = require('mongoose');

// Connect to MongoDB
mongoose.connect('mongodb://localhost:27017/myapp')
  .then(() => console.log('MongoDB connected'))
  .catch(err => console.error('Connection error:', err));
```

### Define Schema
```javascript
const mongoose = require('mongoose');

// Define schema
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
    min: 18,
    max: 100
  },
  department: {
    type: String,
    enum: ['IT', 'Sales', 'HR', 'Marketing']
  },
  skills: [String],
  address: {
    street: String,
    city: String,
    zip: String
  },
  createdAt: {
    type: Date,
    default: Date.now
  }
});

// Create model
const User = mongoose.model('User', userSchema);
```

### CRUD with Mongoose
```javascript
// Create
const user = new User({
  name: 'Alice Johnson',
  email: 'alice@example.com',
  age: 30,
  department: 'IT'
});
await user.save();

// Or use create
const user = await User.create({
  name: 'Bob Smith',
  email: 'bob@example.com'
});

// Read
const users = await User.find();
const user = await User.findById(userId);
const itUsers = await User.find({ department: 'IT' });

// Update
await User.updateOne(
  { _id: userId },
  { $set: { age: 31 } }
);

// Delete
await User.deleteOne({ _id: userId });
```

## 🧪 Hands-On Practice

### Practice Database
```javascript
// Create sample data
db.employees.insertMany([
  {
    name: "Alice Johnson",
    email: "alice@company.com",
    age: 28,
    department: "IT",
    position: "Senior Developer",
    salary: 95000,
    skills: ["JavaScript", "Node.js", "MongoDB", "React"],
    joiningDate: ISODate("2022-01-15"),
    isActive: true
  },
  {
    name: "Bob Smith",
    email: "bob@company.com",
    age: 32,
    department: "Sales",
    position: "Sales Manager",
    salary: 85000,
    skills: ["CRM", "Negotiation"],
    joiningDate: ISODate("2020-06-01"),
    isActive: true
  },
  {
    name: "Charlie Brown",
    email: "charlie@company.com",
    age: 26,
    department: "IT",
    position: "Junior Developer",
    salary: 70000,
    skills: ["JavaScript", "React"],
    joiningDate: ISODate("2023-03-20"),
    isActive: true
  },
  {
    name: "Diana Prince",
    email: "diana@company.com",
    age: 35,
    department: "HR",
    position: "HR Manager",
    salary: 90000,
    skills: ["Recruitment", "Employee Relations"],
    joiningDate: ISODate("2019-11-10"),
    isActive: true
  }
])
```

### Practice Queries

**1. Find IT employees**
```javascript
db.employees.find({ department: "IT" })
```

**2. Find high earners**
```javascript
db.employees.find({ salary: { $gt: 80000 } })
```

**3. Find by skill**
```javascript
db.employees.find({ skills: "React" })
```

**4. Complex query**
```javascript
db.employees.find({
  $and: [
    { department: "IT" },
    { salary: { $gte: 75000 } },
    { isActive: true }
  ]
}).sort({ salary: -1 })
```

**5. Update salary**
```javascript
db.employees.updateMany(
  { department: "IT", position: "Junior Developer" },
  { $inc: { salary: 5000 } }
)
```

## 💡 Best Practices

### 1. Use Indexes
```javascript
// Create index for faster queries
db.employees.createIndex({ email: 1 })
db.employees.createIndex({ department: 1, salary: -1 })

// Compound index
db.employees.createIndex({ department: 1, position: 1 })
```

### 2. Validate Data
```javascript
// Use Mongoose validators
const schema = new mongoose.Schema({
  email: {
    type: String,
    required: true,
    validate: {
      validator: function(v) {
        return /^\S+@\S+\.\S+$/.test(v);
      }
    }
  }
});
```

### 3. Use Projection
```javascript
// Return only needed fields
db.employees.find({}, { name: 1, department: 1, _id: 0 })
```

### 4. Handle Errors
```javascript
try {
  const user = await User.create(data);
} catch (error) {
  if (error.code === 11000) {
    // Duplicate key error
    console.log('Email already exists');
  }
}
```

## 🔗 Next Steps

Mastered MongoDB basics? Let's build Express.js APIs!

---

**Key Takeaways:**
- MongoDB stores JSON-like documents
- Collections contain documents
- CRUD operations are straightforward
- Query operators provide powerful filtering
- Mongoose simplifies Node.js integration
- Indexes improve query performance

**Next Tutorial:** [02-Express-API.md](./02-Express-API.md)
