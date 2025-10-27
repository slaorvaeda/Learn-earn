# Advanced Concepts - Tutorial 28 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Design Patterns](#design-patterns)
3. [Architecture Patterns](#architecture-patterns)
4. [Advanced JavaScript Concepts](#advanced-javascript-concepts)
5. [Microservices](#microservices)
6. [Event-Driven Architecture](#event-driven-architecture)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Advanced concepts in JavaScript development help you **build scalable, maintainable, and robust applications**. These concepts are essential for senior developers and architects.

### What You'll Learn
- ✅ **Design Patterns** - Reusable solutions to common problems
- ✅ **Architecture Patterns** - System design and organization
- ✅ **Advanced JavaScript** - Deep language features
- ✅ **Microservices** - Distributed system architecture
- ✅ **Event-Driven Architecture** - Asynchronous communication

---

## Design Patterns

### Singleton Pattern
```javascript
// Singleton pattern for database connection
class DatabaseConnection {
  constructor() {
    if (DatabaseConnection.instance) {
      return DatabaseConnection.instance;
    }
    
    this.connection = null;
    DatabaseConnection.instance = this;
  }
  
  async connect(uri) {
    if (!this.connection) {
      // Simulate database connection
      this.connection = await this.createConnection(uri);
    }
    return this.connection;
  }
  
  async createConnection(uri) {
    // Simulate connection creation
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve({ uri, connected: true });
      }, 1000);
    });
  }
  
  getConnection() {
    return this.connection;
  }
}

// Usage
const db1 = new DatabaseConnection();
const db2 = new DatabaseConnection();

console.log(db1 === db2); // true - same instance
```

### Observer Pattern
```javascript
// Event emitter implementation
class EventEmitter {
  constructor() {
    this.events = {};
  }
  
  on(event, listener) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(listener);
  }
  
  off(event, listener) {
    if (!this.events[event]) return;
    
    this.events[event] = this.events[event].filter(l => l !== listener);
  }
  
  emit(event, ...args) {
    if (!this.events[event]) return;
    
    this.events[event].forEach(listener => {
      listener(...args);
    });
  }
  
  once(event, listener) {
    const onceWrapper = (...args) => {
      listener(...args);
      this.off(event, onceWrapper);
    };
    
    this.on(event, onceWrapper);
  }
}

// Usage
const emitter = new EventEmitter();

emitter.on('user:login', (user) => {
  console.log(`User ${user.name} logged in`);
});

emitter.on('user:logout', (user) => {
  console.log(`User ${user.name} logged out`);
});

emitter.emit('user:login', { name: 'John' });
emitter.emit('user:logout', { name: 'John' });
```

### Factory Pattern
```javascript
// Factory for creating different types of users
class UserFactory {
  static createUser(type, userData) {
    switch (type) {
      case 'admin':
        return new AdminUser(userData);
      case 'moderator':
        return new ModeratorUser(userData);
      case 'regular':
        return new RegularUser(userData);
      default:
        throw new Error(`Unknown user type: ${type}`);
    }
  }
}

class BaseUser {
  constructor(userData) {
    this.name = userData.name;
    this.email = userData.email;
    this.role = userData.role;
  }
  
  getPermissions() {
    return [];
  }
}

class AdminUser extends BaseUser {
  getPermissions() {
    return ['read', 'write', 'delete', 'admin'];
  }
}

class ModeratorUser extends BaseUser {
  getPermissions() {
    return ['read', 'write', 'moderate'];
  }
}

class RegularUser extends BaseUser {
  getPermissions() {
    return ['read'];
  }
}

// Usage
const admin = UserFactory.createUser('admin', {
  name: 'John',
  email: 'john@example.com',
  role: 'admin'
});

console.log(admin.getPermissions()); // ['read', 'write', 'delete', 'admin']
```

### Strategy Pattern
```javascript
// Payment strategies
class PaymentStrategy {
  pay(amount) {
    throw new Error('Payment method not implemented');
  }
}

class CreditCardPayment extends PaymentStrategy {
  pay(amount) {
    return `Paid $${amount} using Credit Card`;
  }
}

class PayPalPayment extends PaymentStrategy {
  pay(amount) {
    return `Paid $${amount} using PayPal`;
  }
}

class BankTransferPayment extends PaymentStrategy {
  pay(amount) {
    return `Paid $${amount} using Bank Transfer`;
  }
}

class PaymentProcessor {
  constructor() {
    this.strategy = null;
  }
  
  setStrategy(strategy) {
    this.strategy = strategy;
  }
  
  processPayment(amount) {
    if (!this.strategy) {
      throw new Error('Payment strategy not set');
    }
    
    return this.strategy.pay(amount);
  }
}

// Usage
const processor = new PaymentProcessor();

processor.setStrategy(new CreditCardPayment());
console.log(processor.processPayment(100)); // Paid $100 using Credit Card

processor.setStrategy(new PayPalPayment());
console.log(processor.processPayment(50)); // Paid $50 using PayPal
```

---

## Architecture Patterns

### MVC Pattern
```javascript
// Model
class UserModel {
  constructor() {
    this.users = [];
  }
  
  create(userData) {
    const user = {
      id: Date.now(),
      ...userData,
      createdAt: new Date()
    };
    this.users.push(user);
    return user;
  }
  
  findById(id) {
    return this.users.find(user => user.id === id);
  }
  
  findAll() {
    return this.users;
  }
  
  update(id, userData) {
    const user = this.findById(id);
    if (user) {
      Object.assign(user, userData);
    }
    return user;
  }
  
  delete(id) {
    const index = this.users.findIndex(user => user.id === id);
    if (index !== -1) {
      return this.users.splice(index, 1)[0];
    }
    return null;
  }
}

// View
class UserView {
  renderUser(user) {
    return `
      <div class="user">
        <h3>${user.name}</h3>
        <p>Email: ${user.email}</p>
        <p>Role: ${user.role}</p>
        <p>Created: ${user.createdAt.toLocaleDateString()}</p>
      </div>
    `;
  }
  
  renderUserList(users) {
    return users.map(user => this.renderUser(user)).join('');
  }
  
  renderError(message) {
    return `<div class="error">${message}</div>`;
  }
}

// Controller
class UserController {
  constructor(model, view) {
    this.model = model;
    this.view = view;
  }
  
  createUser(userData) {
    try {
      const user = this.model.create(userData);
      return this.view.renderUser(user);
    } catch (error) {
      return this.view.renderError(error.message);
    }
  }
  
  getUser(id) {
    const user = this.model.findById(id);
    if (user) {
      return this.view.renderUser(user);
    }
    return this.view.renderError('User not found');
  }
  
  getAllUsers() {
    const users = this.model.findAll();
    return this.view.renderUserList(users);
  }
  
  updateUser(id, userData) {
    try {
      const user = this.model.update(id, userData);
      if (user) {
        return this.view.renderUser(user);
      }
      return this.view.renderError('User not found');
    } catch (error) {
      return this.view.renderError(error.message);
    }
  }
  
  deleteUser(id) {
    const user = this.model.delete(id);
    if (user) {
      return `User ${user.name} deleted successfully`;
    }
    return this.view.renderError('User not found');
  }
}

// Usage
const userModel = new UserModel();
const userView = new UserView();
const userController = new UserController(userModel, userView);

// Create users
userController.createUser({
  name: 'John Doe',
  email: 'john@example.com',
  role: 'admin'
});

userController.createUser({
  name: 'Jane Smith',
  email: 'jane@example.com',
  role: 'user'
});

// Get all users
console.log(userController.getAllUsers());
```

### Repository Pattern
```javascript
// Base repository
class BaseRepository {
  constructor(model) {
    this.model = model;
  }
  
  async create(data) {
    return this.model.create(data);
  }
  
  async findById(id) {
    return this.model.findById(id);
  }
  
  async findAll(filters = {}) {
    return this.model.find(filters);
  }
  
  async update(id, data) {
    return this.model.findByIdAndUpdate(id, data, { new: true });
  }
  
  async delete(id) {
    return this.model.findByIdAndDelete(id);
  }
  
  async count(filters = {}) {
    return this.model.countDocuments(filters);
  }
}

// User repository
class UserRepository extends BaseRepository {
  async findByEmail(email) {
    return this.model.findOne({ email });
  }
  
  async findByRole(role) {
    return this.model.find({ role });
  }
  
  async findActiveUsers() {
    return this.model.find({ isActive: true });
  }
  
  async updateLastLogin(id) {
    return this.model.findByIdAndUpdate(
      id,
      { lastLogin: new Date() },
      { new: true }
    );
  }
}

// Service layer
class UserService {
  constructor(userRepository) {
    this.userRepository = userRepository;
  }
  
  async createUser(userData) {
    // Business logic
    if (!userData.email || !userData.password) {
      throw new Error('Email and password are required');
    }
    
    // Check if user already exists
    const existingUser = await this.userRepository.findByEmail(userData.email);
    if (existingUser) {
      throw new Error('User already exists');
    }
    
    // Hash password
    const hashedPassword = await this.hashPassword(userData.password);
    
    // Create user
    return this.userRepository.create({
      ...userData,
      password: hashedPassword,
      isActive: true
    });
  }
  
  async getUserById(id) {
    const user = await this.userRepository.findById(id);
    if (!user) {
      throw new Error('User not found');
    }
    return user;
  }
  
  async updateUser(id, userData) {
    const user = await this.userRepository.findById(id);
    if (!user) {
      throw new Error('User not found');
    }
    
    return this.userRepository.update(id, userData);
  }
  
  async deleteUser(id) {
    const user = await this.userRepository.findById(id);
    if (!user) {
      throw new Error('User not found');
    }
    
    return this.userRepository.delete(id);
  }
  
  async hashPassword(password) {
    // Simulate password hashing
    return `hashed_${password}`;
  }
}

// Usage
const userRepository = new UserRepository(UserModel);
const userService = new UserService(userRepository);

// Create user
userService.createUser({
  name: 'John Doe',
  email: 'john@example.com',
  password: 'password123'
}).then(user => {
  console.log('User created:', user);
}).catch(error => {
  console.error('Error:', error.message);
});
```

---

## Advanced JavaScript Concepts

### Closures and Lexical Scoping
```javascript
// Closure example
function createCounter(initialValue = 0) {
  let count = initialValue;
  
  return {
    increment: () => ++count,
    decrement: () => --count,
    getValue: () => count,
    reset: () => count = initialValue
  };
}

const counter = createCounter(10);
console.log(counter.getValue()); // 10
console.log(counter.increment()); // 11
console.log(counter.increment()); // 12
console.log(counter.decrement()); // 11
console.log(counter.reset()); // 10

// Module pattern with closures
const UserModule = (() => {
  let users = [];
  let nextId = 1;
  
  const addUser = (userData) => {
    const user = { id: nextId++, ...userData };
    users.push(user);
    return user;
  };
  
  const getUsers = () => [...users]; // Return copy to prevent mutation
  
  const getUserById = (id) => users.find(user => user.id === id);
  
  const updateUser = (id, userData) => {
    const user = getUserById(id);
    if (user) {
      Object.assign(user, userData);
      return user;
    }
    return null;
  };
  
  const deleteUser = (id) => {
    const index = users.findIndex(user => user.id === id);
    if (index !== -1) {
      return users.splice(index, 1)[0];
    }
    return null;
  };
  
  return {
    addUser,
    getUsers,
    getUserById,
    updateUser,
    deleteUser
  };
})();

// Usage
UserModule.addUser({ name: 'John', email: 'john@example.com' });
UserModule.addUser({ name: 'Jane', email: 'jane@example.com' });
console.log(UserModule.getUsers());
```

### Prototypes and Inheritance
```javascript
// Constructor function
function Animal(name, species) {
  this.name = name;
  this.species = species;
}

// Add methods to prototype
Animal.prototype.speak = function() {
  return `${this.name} makes a sound`;
};

Animal.prototype.getInfo = function() {
  return `${this.name} is a ${this.species}`;
};

// Create child constructor
function Dog(name, breed) {
  Animal.call(this, name, 'dog');
  this.breed = breed;
}

// Set up inheritance
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

// Add dog-specific methods
Dog.prototype.speak = function() {
  return `${this.name} barks`;
};

Dog.prototype.getBreed = function() {
  return `${this.name} is a ${this.breed}`;
};

// Usage
const dog = new Dog('Buddy', 'Golden Retriever');
console.log(dog.speak()); // Buddy barks
console.log(dog.getInfo()); // Buddy is a dog
console.log(dog.getBreed()); // Buddy is a Golden Retriever

// ES6 Classes
class Animal {
  constructor(name, species) {
    this.name = name;
    this.species = species;
  }
  
  speak() {
    return `${this.name} makes a sound`;
  }
  
  getInfo() {
    return `${this.name} is a ${this.species}`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name, 'dog');
    this.breed = breed;
  }
  
  speak() {
    return `${this.name} barks`;
  }
  
  getBreed() {
    return `${this.name} is a ${this.breed}`;
  }
}

// Usage
const dog2 = new Dog('Max', 'Labrador');
console.log(dog2.speak()); // Max barks
console.log(dog2.getInfo()); // Max is a dog
console.log(dog2.getBreed()); // Max is a Labrador
```

### Generators and Iterators
```javascript
// Generator function
function* fibonacci() {
  let a = 0, b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

// Usage
const fib = fibonacci();
console.log(fib.next().value); // 0
console.log(fib.next().value); // 1
console.log(fib.next().value); // 1
console.log(fib.next().value); // 2
console.log(fib.next().value); // 3

// Custom iterator
class NumberRange {
  constructor(start, end, step = 1) {
    this.start = start;
    this.end = end;
    this.step = step;
  }
  
  [Symbol.iterator]() {
    let current = this.start;
    const end = this.end;
    const step = this.step;
    
    return {
      next() {
        if (current < end) {
          const value = current;
          current += step;
          return { value, done: false };
        }
        return { done: true };
      }
    };
  }
}

// Usage
const range = new NumberRange(1, 10, 2);
for (const num of range) {
  console.log(num); // 1, 3, 5, 7, 9
}

// Async generator
async function* fetchData(urls) {
  for (const url of urls) {
    try {
      const response = await fetch(url);
      const data = await response.json();
      yield data;
    } catch (error) {
      yield { error: error.message };
    }
  }
}

// Usage
const urls = [
  'https://api.example.com/users',
  'https://api.example.com/posts',
  'https://api.example.com/comments'
];

(async () => {
  for await (const data of fetchData(urls)) {
    console.log(data);
  }
})();
```

---

## Microservices

### Service Discovery
```javascript
// Service registry
class ServiceRegistry {
  constructor() {
    this.services = new Map();
  }
  
  register(serviceName, serviceInfo) {
    this.services.set(serviceName, {
      ...serviceInfo,
      registeredAt: new Date(),
      lastHeartbeat: new Date()
    });
  }
  
  unregister(serviceName) {
    this.services.delete(serviceName);
  }
  
  getService(serviceName) {
    return this.services.get(serviceName);
  }
  
  getAllServices() {
    return Array.from(this.services.entries());
  }
  
  heartbeat(serviceName) {
    const service = this.services.get(serviceName);
    if (service) {
      service.lastHeartbeat = new Date();
    }
  }
  
  getHealthyServices() {
    const now = new Date();
    const healthyServices = [];
    
    for (const [name, service] of this.services) {
      const timeSinceHeartbeat = now - service.lastHeartbeat;
      if (timeSinceHeartbeat < 30000) { // 30 seconds
        healthyServices.push({ name, ...service });
      }
    }
    
    return healthyServices;
  }
}

// Service client
class ServiceClient {
  constructor(registry) {
    this.registry = registry;
  }
  
  async callService(serviceName, endpoint, data = {}) {
    const service = this.registry.getService(serviceName);
    if (!service) {
      throw new Error(`Service ${serviceName} not found`);
    }
    
    const url = `${service.url}${endpoint}`;
    const response = await fetch(url, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(data)
    });
    
    if (!response.ok) {
      throw new Error(`Service call failed: ${response.statusText}`);
    }
    
    return response.json();
  }
}

// Usage
const registry = new ServiceRegistry();
const client = new ServiceClient(registry);

// Register services
registry.register('user-service', {
  url: 'http://localhost:3001',
  version: '1.0.0',
  health: '/health'
});

registry.register('order-service', {
  url: 'http://localhost:3002',
  version: '1.0.0',
  health: '/health'
});

// Call service
client.callService('user-service', '/api/users', { name: 'John' })
  .then(result => console.log(result))
  .catch(error => console.error(error));
```

### API Gateway
```javascript
// API Gateway
class APIGateway {
  constructor(serviceRegistry) {
    this.registry = serviceRegistry;
    this.routes = new Map();
  }
  
  addRoute(path, serviceName, endpoint) {
    this.routes.set(path, { serviceName, endpoint });
  }
  
  async handleRequest(req) {
    const { pathname, method } = new URL(req.url);
    const route = this.routes.get(pathname);
    
    if (!route) {
      return {
        status: 404,
        body: { error: 'Route not found' }
      };
    }
    
    const service = this.registry.getService(route.serviceName);
    if (!service) {
      return {
        status: 503,
        body: { error: 'Service unavailable' }
      };
    }
    
    try {
      const response = await fetch(`${service.url}${route.endpoint}`, {
        method: req.method,
        headers: req.headers,
        body: req.body
      });
      
      return {
        status: response.status,
        body: await response.json()
      };
    } catch (error) {
      return {
        status: 500,
        body: { error: 'Internal server error' }
      };
    }
  }
}

// Usage
const gateway = new APIGateway(registry);

// Add routes
gateway.addRoute('/api/users', 'user-service', '/users');
gateway.addRoute('/api/orders', 'order-service', '/orders');

// Handle request
gateway.handleRequest({
  url: 'http://localhost:3000/api/users',
  method: 'GET',
  headers: {},
  body: null
}).then(response => {
  console.log(response);
});
```

---

## Event-Driven Architecture

### Event Bus
```javascript
// Event bus implementation
class EventBus {
  constructor() {
    this.events = new Map();
    this.middleware = [];
  }
  
  use(middleware) {
    this.middleware.push(middleware);
  }
  
  async emit(event, data) {
    const eventData = { event, data, timestamp: new Date() };
    
    // Apply middleware
    for (const middleware of this.middleware) {
      await middleware(eventData);
    }
    
    // Emit to all listeners
    const listeners = this.events.get(event) || [];
    for (const listener of listeners) {
      try {
        await listener(eventData);
      } catch (error) {
        console.error(`Error in event listener for ${event}:`, error);
      }
    }
  }
  
  on(event, listener) {
    if (!this.events.has(event)) {
      this.events.set(event, []);
    }
    this.events.get(event).push(listener);
  }
  
  off(event, listener) {
    const listeners = this.events.get(event);
    if (listeners) {
      const index = listeners.indexOf(listener);
      if (index !== -1) {
        listeners.splice(index, 1);
      }
    }
  }
  
  once(event, listener) {
    const onceWrapper = (eventData) => {
      listener(eventData);
      this.off(event, onceWrapper);
    };
    this.on(event, onceWrapper);
  }
}

// Event store
class EventStore {
  constructor() {
    this.events = [];
  }
  
  append(event) {
    this.events.push({
      ...event,
      id: Date.now(),
      version: this.events.length + 1
    });
  }
  
  getEvents(aggregateId) {
    return this.events.filter(event => event.aggregateId === aggregateId);
  }
  
  getEventsFromVersion(aggregateId, version) {
    return this.events.filter(
      event => event.aggregateId === aggregateId && event.version > version
    );
  }
}

// Event sourcing example
class UserAggregate {
  constructor(id) {
    this.id = id;
    this.name = '';
    this.email = '';
    this.isActive = false;
    this.version = 0;
  }
  
  apply(event) {
    switch (event.type) {
      case 'UserCreated':
        this.name = event.data.name;
        this.email = event.data.email;
        this.isActive = true;
        break;
      case 'UserUpdated':
        this.name = event.data.name || this.name;
        this.email = event.data.email || this.email;
        break;
      case 'UserDeactivated':
        this.isActive = false;
        break;
    }
    this.version = event.version;
  }
  
  static fromEvents(events) {
    const aggregate = new UserAggregate(events[0].aggregateId);
    events.forEach(event => aggregate.apply(event));
    return aggregate;
  }
}

// Usage
const eventBus = new EventBus();
const eventStore = new EventStore();

// Add logging middleware
eventBus.use(async (eventData) => {
  console.log(`Event emitted: ${eventData.event}`, eventData.data);
});

// Add event store middleware
eventBus.use(async (eventData) => {
  eventStore.append(eventData);
});

// Event handlers
eventBus.on('UserCreated', async (eventData) => {
  console.log('User created:', eventData.data);
});

eventBus.on('UserUpdated', async (eventData) => {
  console.log('User updated:', eventData.data);
});

// Emit events
eventBus.emit('UserCreated', {
  aggregateId: 'user-1',
  type: 'UserCreated',
  data: { name: 'John Doe', email: 'john@example.com' }
});

eventBus.emit('UserUpdated', {
  aggregateId: 'user-1',
  type: 'UserUpdated',
  data: { name: 'John Smith' }
});

// Rebuild aggregate from events
const userEvents = eventStore.getEvents('user-1');
const user = UserAggregate.fromEvents(userEvents);
console.log('User aggregate:', user);
```

---

## Practice Exercises

### Exercise 1: Implement Command Pattern
```javascript
// Command pattern for undo/redo functionality
class Command {
  execute() {
    throw new Error('Execute method must be implemented');
  }
  
  undo() {
    throw new Error('Undo method must be implemented');
  }
}

class AddTextCommand extends Command {
  constructor(editor, text, position) {
    super();
    this.editor = editor;
    this.text = text;
    this.position = position;
  }
  
  execute() {
    this.editor.insertText(this.text, this.position);
  }
  
  undo() {
    this.editor.deleteText(this.position, this.text.length);
  }
}

class DeleteTextCommand extends Command {
  constructor(editor, text, position) {
    super();
    this.editor = editor;
    this.text = text;
    this.position = position;
  }
  
  execute() {
    this.editor.deleteText(this.position, this.text.length);
  }
  
  undo() {
    this.editor.insertText(this.text, this.position);
  }
}

class TextEditor {
  constructor() {
    this.content = '';
    this.history = [];
    this.currentIndex = -1;
  }
  
  insertText(text, position) {
    this.content = this.content.slice(0, position) + text + this.content.slice(position);
  }
  
  deleteText(position, length) {
    this.content = this.content.slice(0, position) + this.content.slice(position + length);
  }
  
  executeCommand(command) {
    command.execute();
    this.history = this.history.slice(0, this.currentIndex + 1);
    this.history.push(command);
    this.currentIndex++;
  }
  
  undo() {
    if (this.currentIndex >= 0) {
      this.history[this.currentIndex].undo();
      this.currentIndex--;
    }
  }
  
  redo() {
    if (this.currentIndex < this.history.length - 1) {
      this.currentIndex++;
      this.history[this.currentIndex].execute();
    }
  }
  
  getContent() {
    return this.content;
  }
}

// Usage
const editor = new TextEditor();

// Add text
const addCommand = new AddTextCommand(editor, 'Hello ', 0);
editor.executeCommand(addCommand);

const addCommand2 = new AddTextCommand(editor, 'World!', 6);
editor.executeCommand(addCommand2);

console.log(editor.getContent()); // Hello World!

// Undo
editor.undo();
console.log(editor.getContent()); // Hello 

// Redo
editor.redo();
console.log(editor.getContent()); // Hello World!
```

### Exercise 2: Implement CQRS Pattern
```javascript
// Command Query Responsibility Segregation (CQRS)

// Commands
class CreateUserCommand {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }
}

class UpdateUserCommand {
  constructor(id, name, email) {
    this.id = id;
    this.name = name;
    this.email = email;
  }
}

// Queries
class GetUserByIdQuery {
  constructor(id) {
    this.id = id;
  }
}

class GetAllUsersQuery {
  constructor() {}
}

// Command handlers
class CreateUserCommandHandler {
  constructor(userRepository, eventBus) {
    this.userRepository = userRepository;
    this.eventBus = eventBus;
  }
  
  async handle(command) {
    const user = await this.userRepository.create({
      name: command.name,
      email: command.email
    });
    
    await this.eventBus.emit('UserCreated', {
      aggregateId: user.id,
      type: 'UserCreated',
      data: { name: user.name, email: user.email }
    });
    
    return user;
  }
}

class UpdateUserCommandHandler {
  constructor(userRepository, eventBus) {
    this.userRepository = userRepository;
    this.eventBus = eventBus;
  }
  
  async handle(command) {
    const user = await this.userRepository.update(command.id, {
      name: command.name,
      email: command.email
    });
    
    await this.eventBus.emit('UserUpdated', {
      aggregateId: user.id,
      type: 'UserUpdated',
      data: { name: user.name, email: user.email }
    });
    
    return user;
  }
}

// Query handlers
class GetUserByIdQueryHandler {
  constructor(userRepository) {
    this.userRepository = userRepository;
  }
  
  async handle(query) {
    return this.userRepository.findById(query.id);
  }
}

class GetAllUsersQueryHandler {
  constructor(userRepository) {
    this.userRepository = userRepository;
  }
  
  async handle(query) {
    return this.userRepository.findAll();
  }
}

// Command and Query buses
class CommandBus {
  constructor() {
    this.handlers = new Map();
  }
  
  register(commandType, handler) {
    this.handlers.set(commandType, handler);
  }
  
  async execute(command) {
    const handler = this.handlers.get(command.constructor);
    if (!handler) {
      throw new Error(`No handler found for command: ${command.constructor.name}`);
    }
    
    return handler.handle(command);
  }
}

class QueryBus {
  constructor() {
    this.handlers = new Map();
  }
  
  register(queryType, handler) {
    this.handlers.set(queryType, handler);
  }
  
  async execute(query) {
    const handler = this.handlers.get(query.constructor);
    if (!handler) {
      throw new Error(`No handler found for query: ${query.constructor.name}`);
    }
    
    return handler.handle(query);
  }
}

// Usage
const commandBus = new CommandBus();
const queryBus = new QueryBus();
const eventBus = new EventBus();
const userRepository = new UserRepository();

// Register handlers
commandBus.register(CreateUserCommand, new CreateUserCommandHandler(userRepository, eventBus));
commandBus.register(UpdateUserCommand, new UpdateUserCommandHandler(userRepository, eventBus));
queryBus.register(GetUserByIdQuery, new GetUserByIdQueryHandler(userRepository));
queryBus.register(GetAllUsersQuery, new GetAllUsersQueryHandler(userRepository));

// Execute commands
const createCommand = new CreateUserCommand('John Doe', 'john@example.com');
const user = await commandBus.execute(createCommand);

// Execute queries
const getUserQuery = new GetUserByIdQuery(user.id);
const foundUser = await queryBus.execute(getUserQuery);
console.log(foundUser);
```

---

## Summary

### Key Concepts Learned
- ✅ **Design Patterns** - Singleton, Observer, Factory, Strategy
- ✅ **Architecture Patterns** - MVC, Repository, CQRS
- ✅ **Advanced JavaScript** - Closures, Prototypes, Generators
- ✅ **Microservices** - Service discovery, API gateway
- ✅ **Event-Driven Architecture** - Event bus, event sourcing

### Best Practices
- Use design patterns to solve common problems
- Implement proper separation of concerns
- Use event-driven architecture for loose coupling
- Implement proper error handling and logging
- Design for scalability and maintainability

### Common Mistakes
- Over-engineering simple solutions
- Not understanding when to use patterns
- Creating tight coupling between services
- Not handling errors properly
- Not considering performance implications

---

**Excellent! You've mastered advanced concepts. Ready for Security? 🚀**

**Next Tutorial:** [Security](./29-security.md)
