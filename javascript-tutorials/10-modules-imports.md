# Modules and Imports - Tutorial 10 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [ES6 Modules](#es6-modules)
3. [Export Types](#export-types)
4. [Import Types](#import-types)
5. [Module Patterns](#module-patterns)
6. [Dynamic Imports](#dynamic-imports)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Modules allow you to **organize code into separate files** and import/export functionality between them, making code more maintainable and reusable.

### What You'll Learn
- ✅ **ES6 Modules** - Modern module system
- ✅ **Export Types** - Named, default, and mixed exports
- ✅ **Import Types** - Different ways to import modules
- ✅ **Module Patterns** - Common module organization patterns
- ✅ **Dynamic Imports** - Loading modules at runtime

---

## ES6 Modules

### Basic Module Structure
```javascript
// math.js - Module file
export const PI = 3.14159;

export function add(a, b) {
    return a + b;
}

export function multiply(a, b) {
    return a * b;
}

// main.js - Main file
import { PI, add, multiply } from './math.js';

console.log(PI); // 3.14159
console.log(add(2, 3)); // 5
console.log(multiply(4, 5)); // 20
```

### HTML Setup
```html
<!DOCTYPE html>
<html>
<head>
    <title>Module Example</title>
</head>
<body>
    <script type="module" src="main.js"></script>
</body>
</html>
```

### Module Scope
```javascript
// utils.js
const privateVariable = "I'm private";

export function publicFunction() {
    return privateVariable;
}

// main.js
import { publicFunction } from './utils.js';

console.log(publicFunction()); // "I'm private"
// console.log(privateVariable); // ReferenceError: privateVariable is not defined
```

---

## Export Types

### Named Exports
```javascript
// math.js
export const PI = 3.14159;
export const E = 2.71828;

export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}

export class Calculator {
    constructor() {
        this.result = 0;
    }
    
    add(value) {
        this.result += value;
        return this;
    }
}

// main.js
import { PI, E, add, subtract, Calculator } from './math.js';

console.log(PI, E); // 3.14159 2.71828
console.log(add(5, 3)); // 8
console.log(subtract(10, 4)); // 6

const calc = new Calculator();
console.log(calc.add(5).add(3).result); // 8
```

### Default Exports
```javascript
// user.js
class User {
    constructor(name, email) {
        this.name = name;
        this.email = email;
    }
    
    getInfo() {
        return `${this.name} (${this.email})`;
    }
}

export default User;

// main.js
import User from './user.js';

const user = new User("John", "john@example.com");
console.log(user.getInfo()); // "John (john@example.com)"
```

### Mixed Exports
```javascript
// api.js
const API_BASE_URL = 'https://api.example.com';

export function fetchUsers() {
    return fetch(`${API_BASE_URL}/users`);
}

export function fetchPosts() {
    return fetch(`${API_BASE_URL}/posts`);
}

export default class ApiClient {
    constructor(baseUrl = API_BASE_URL) {
        this.baseUrl = baseUrl;
    }
    
    async get(endpoint) {
        const response = await fetch(`${this.baseUrl}${endpoint}`);
        return response.json();
    }
}

// main.js
import ApiClient, { fetchUsers, fetchPosts } from './api.js';

const api = new ApiClient();
const users = await fetchUsers();
const posts = await fetchPosts();
```

### Re-exports
```javascript
// math.js
export const PI = 3.14159;
export function add(a, b) { return a + b; }

// geometry.js
export const PI as MATH_PI } from './math.js';
export { add as addNumbers } from './math.js';

// main.js
import { MATH_PI, addNumbers } from './geometry.js';
```

---

## Import Types

### Named Imports
```javascript
// utils.js
export const formatDate = (date) => date.toLocaleDateString();
export const formatCurrency = (amount) => `$${amount.toFixed(2)}`;
export const validateEmail = (email) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);

// main.js
import { formatDate, formatCurrency, validateEmail } from './utils.js';

const today = new Date();
console.log(formatDate(today)); // "12/25/2023"
console.log(formatCurrency(99.99)); // "$99.99"
console.log(validateEmail("test@example.com")); // true
```

### Default Imports
```javascript
// logger.js
class Logger {
    log(message) {
        console.log(`[LOG] ${new Date().toISOString()}: ${message}`);
    }
    
    error(message) {
        console.error(`[ERROR] ${new Date().toISOString()}: ${message}`);
    }
}

export default Logger;

// main.js
import Logger from './logger.js';

const logger = new Logger();
logger.log("Application started");
logger.error("Something went wrong");
```

### Namespace Imports
```javascript
// math.js
export const PI = 3.14159;
export const E = 2.71828;
export function add(a, b) { return a + b; }
export function multiply(a, b) { return a * b; }

// main.js
import * as Math from './math.js';

console.log(Math.PI); // 3.14159
console.log(Math.E); // 2.71828
console.log(Math.add(2, 3)); // 5
console.log(Math.multiply(4, 5)); // 20
```

### Mixed Imports
```javascript
// api.js
const API_BASE_URL = 'https://api.example.com';

export function fetchUsers() {
    return fetch(`${API_BASE_URL}/users`);
}

export function fetchPosts() {
    return fetch(`${API_BASE_URL}/posts`);
}

export default class ApiClient {
    constructor(baseUrl = API_BASE_URL) {
        this.baseUrl = baseUrl;
    }
    
    async get(endpoint) {
        const response = await fetch(`${this.baseUrl}${endpoint}`);
        return response.json();
    }
}

// main.js
import ApiClient, { fetchUsers, fetchPosts } from './api.js';
import * as Utils from './utils.js';

const api = new ApiClient();
const users = await fetchUsers();
const posts = await fetchPosts();
```

---

## Module Patterns

### Utility Module
```javascript
// utils.js
export const formatDate = (date) => {
    return new Intl.DateTimeFormat('en-US', {
        year: 'numeric',
        month: 'long',
        day: 'numeric'
    }).format(date);
};

export const formatCurrency = (amount, currency = 'USD') => {
    return new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: currency
    }).format(amount);
};

export const validateEmail = (email) => {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
};

export const debounce = (func, delay) => {
    let timeoutId;
    return (...args) => {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => func.apply(null, args), delay);
    };
};
```

### Service Module
```javascript
// userService.js
class UserService {
    constructor() {
        this.users = [];
        this.currentUser = null;
    }
    
    async fetchUsers() {
        try {
            const response = await fetch('/api/users');
            this.users = await response.json();
            return this.users;
        } catch (error) {
            console.error('Failed to fetch users:', error);
            throw error;
        }
    }
    
    async createUser(userData) {
        try {
            const response = await fetch('/api/users', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(userData)
            });
            
            const newUser = await response.json();
            this.users.push(newUser);
            return newUser;
        } catch (error) {
            console.error('Failed to create user:', error);
            throw error;
        }
    }
    
    setCurrentUser(user) {
        this.currentUser = user;
    }
    
    getCurrentUser() {
        return this.currentUser;
    }
}

export default new UserService();
```

### Configuration Module
```javascript
// config.js
const config = {
    api: {
        baseUrl: process.env.NODE_ENV === 'production' 
            ? 'https://api.myapp.com' 
            : 'http://localhost:3000',
        timeout: 5000
    },
    ui: {
        theme: 'light',
        language: 'en',
        itemsPerPage: 10
    },
    features: {
        enableNotifications: true,
        enableAnalytics: false
    }
};

export const getConfig = () => ({ ...config });

export const updateConfig = (updates) => {
    Object.assign(config, updates);
};

export const getApiConfig = () => config.api;

export const getUiConfig = () => config.ui;
```

---

## Dynamic Imports

### Basic Dynamic Import
```javascript
// Dynamic import returns a promise
async function loadModule() {
    try {
        const module = await import('./math.js');
        console.log(module.PI); // 3.14159
        console.log(module.add(2, 3)); // 5
    } catch (error) {
        console.error('Failed to load module:', error);
    }
}

loadModule();
```

### Conditional Module Loading
```javascript
// Load different modules based on condition
async function loadFeature(featureName) {
    try {
        let module;
        
        switch (featureName) {
            case 'dashboard':
                module = await import('./features/dashboard.js');
                break;
            case 'analytics':
                module = await import('./features/analytics.js');
                break;
            case 'reports':
                module = await import('./features/reports.js');
                break;
            default:
                throw new Error(`Unknown feature: ${featureName}`);
        }
        
        return module.default;
    } catch (error) {
        console.error(`Failed to load feature ${featureName}:`, error);
        throw error;
    }
}

// Usage
loadFeature('dashboard')
    .then(Dashboard => {
        const dashboard = new Dashboard();
        dashboard.render();
    })
    .catch(error => {
        console.error('Error:', error);
    });
```

### Lazy Loading with Dynamic Imports
```javascript
// Lazy load components when needed
class LazyLoader {
    constructor() {
        this.loadedModules = new Map();
    }
    
    async loadComponent(componentName) {
        if (this.loadedModules.has(componentName)) {
            return this.loadedModules.get(componentName);
        }
        
        try {
            const module = await import(`./components/${componentName}.js`);
            this.loadedModules.set(componentName, module.default);
            return module.default;
        } catch (error) {
            console.error(`Failed to load component ${componentName}:`, error);
            throw error;
        }
    }
    
    async renderComponent(componentName, container) {
        try {
            const Component = await this.loadComponent(componentName);
            const instance = new Component();
            instance.render(container);
        } catch (error) {
            console.error(`Failed to render component ${componentName}:`, error);
        }
    }
}

// Usage
const lazyLoader = new LazyLoader();

// Load component when button is clicked
document.getElementById('load-dashboard').addEventListener('click', () => {
    lazyLoader.renderComponent('Dashboard', document.getElementById('app'));
});
```

---

## Practice Exercises

### Exercise 1: E-commerce Module System
```javascript
// product.js
export class Product {
    constructor(id, name, price, category) {
        this.id = id;
        this.name = name;
        this.price = price;
        this.category = category;
    }
    
    getFormattedPrice() {
        return `$${this.price.toFixed(2)}`;
    }
    
    getInfo() {
        return `${this.name} - ${this.getFormattedPrice()} (${this.category})`;
    }
}

export function createProduct(id, name, price, category) {
    return new Product(id, name, price, category);
}

// cart.js
export class Cart {
    constructor() {
        this.items = [];
        this.total = 0;
    }
    
    addItem(product, quantity = 1) {
        const existingItem = this.items.find(item => item.product.id === product.id);
        
        if (existingItem) {
            existingItem.quantity += quantity;
        } else {
            this.items.push({ product, quantity });
        }
        
        this.calculateTotal();
    }
    
    removeItem(productId) {
        this.items = this.items.filter(item => item.product.id !== productId);
        this.calculateTotal();
    }
    
    calculateTotal() {
        this.total = this.items.reduce((sum, item) => {
            return sum + (item.product.price * item.quantity);
        }, 0);
    }
    
    getTotal() {
        return this.total;
    }
    
    getItemCount() {
        return this.items.reduce((count, item) => count + item.quantity, 0);
    }
}

// order.js
export class Order {
    constructor(cart, customerInfo) {
        this.id = Date.now().toString();
        this.items = [...cart.items];
        this.total = cart.getTotal();
        this.customerInfo = customerInfo;
        this.status = 'pending';
        this.createdAt = new Date();
    }
    
    process() {
        this.status = 'processing';
        // Simulate processing delay
        return new Promise((resolve) => {
            setTimeout(() => {
                this.status = 'completed';
                resolve(this);
            }, 2000);
        });
    }
    
    getOrderSummary() {
        return {
            id: this.id,
            total: this.total,
            itemCount: this.items.length,
            status: this.status,
            createdAt: this.createdAt
        };
    }
}

// main.js
import { Product, createProduct } from './product.js';
import { Cart } from './cart.js';
import { Order } from './order.js';

// Create products
const laptop = createProduct(1, 'Laptop', 999.99, 'Electronics');
const mouse = createProduct(2, 'Mouse', 29.99, 'Electronics');
const keyboard = createProduct(3, 'Keyboard', 79.99, 'Electronics');

// Create cart and add items
const cart = new Cart();
cart.addItem(laptop, 1);
cart.addItem(mouse, 2);
cart.addItem(keyboard, 1);

console.log('Cart total:', cart.getFormattedTotal());
console.log('Item count:', cart.getItemCount());

// Create order
const order = new Order(cart, {
    name: 'John Doe',
    email: 'john@example.com',
    address: '123 Main St'
});

console.log('Order created:', order.getOrderSummary());

// Process order
order.process().then(processedOrder => {
    console.log('Order processed:', processedOrder.getOrderSummary());
});
```

### Exercise 2: Plugin System with Dynamic Imports
```javascript
// pluginManager.js
export class PluginManager {
    constructor() {
        this.plugins = new Map();
        this.loadedPlugins = new Set();
    }
    
    async loadPlugin(pluginName) {
        if (this.loadedPlugins.has(pluginName)) {
            return this.plugins.get(pluginName);
        }
        
        try {
            const module = await import(`./plugins/${pluginName}.js`);
            const plugin = module.default;
            
            this.plugins.set(pluginName, plugin);
            this.loadedPlugins.add(pluginName);
            
            console.log(`Plugin ${pluginName} loaded successfully`);
            return plugin;
        } catch (error) {
            console.error(`Failed to load plugin ${pluginName}:`, error);
            throw error;
        }
    }
    
    async loadAllPlugins(pluginNames) {
        const loadPromises = pluginNames.map(name => this.loadPlugin(name));
        
        try {
            const plugins = await Promise.allSettled(loadPromises);
            
            const successful = plugins
                .filter(result => result.status === 'fulfilled')
                .map(result => result.value);
            
            const failed = plugins
                .filter(result => result.status === 'rejected')
                .map(result => result.reason);
            
            console.log(`Loaded ${successful.length} plugins successfully`);
            if (failed.length > 0) {
                console.log(`Failed to load ${failed.length} plugins`);
            }
            
            return { successful, failed };
        } catch (error) {
            console.error('Error loading plugins:', error);
            throw error;
        }
    }
    
    getPlugin(pluginName) {
        return this.plugins.get(pluginName);
    }
    
    getAllPlugins() {
        return Array.from(this.plugins.values());
    }
}

// plugins/analytics.js
export default class AnalyticsPlugin {
    constructor() {
        this.name = 'Analytics';
        this.version = '1.0.0';
    }
    
    initialize() {
        console.log('Analytics plugin initialized');
    }
    
    track(event, data) {
        console.log(`Analytics: ${event}`, data);
    }
}

// plugins/logger.js
export default class LoggerPlugin {
    constructor() {
        this.name = 'Logger';
        this.version = '1.0.0';
    }
    
    initialize() {
        console.log('Logger plugin initialized');
    }
    
    log(message, level = 'info') {
        const timestamp = new Date().toISOString();
        console.log(`[${timestamp}] [${level.toUpperCase()}] ${message}`);
    }
}

// main.js
import { PluginManager } from './pluginManager.js';

const pluginManager = new PluginManager();

// Load plugins dynamically
async function initializeApp() {
    try {
        const { successful, failed } = await pluginManager.loadAllPlugins([
            'analytics',
            'logger'
        ]);
        
        // Initialize successful plugins
        successful.forEach(plugin => {
            plugin.initialize();
        });
        
        // Use plugins
        const analytics = pluginManager.getPlugin('analytics');
        const logger = pluginManager.getPlugin('logger');
        
        if (analytics) {
            analytics.track('app_loaded', { timestamp: Date.now() });
        }
        
        if (logger) {
            logger.log('Application started successfully');
        }
        
    } catch (error) {
        console.error('Failed to initialize app:', error);
    }
}

initializeApp();
```

---

## Summary

### Key Concepts Learned
- ✅ **ES6 Modules** - Modern module system with import/export
- ✅ **Export Types** - Named, default, and mixed exports
- ✅ **Import Types** - Different ways to import modules
- ✅ **Module Patterns** - Common module organization patterns
- ✅ **Dynamic Imports** - Loading modules at runtime

### Best Practices
- Use named exports for multiple exports
- Use default exports for single main export
- Organize modules by functionality
- Use dynamic imports for code splitting
- Keep modules focused and cohesive

### Common Mistakes
- Not using `type="module"` in HTML
- Mixing import/export with require/module.exports
- Circular dependencies between modules
- Not handling dynamic import errors
- Over-exporting from modules

---

**Excellent! You've mastered modules and imports. Ready for Advanced JavaScript Concepts? 🚀**

**Next Tutorial:** [Advanced JavaScript Concepts](./11-advanced-concepts.md)
