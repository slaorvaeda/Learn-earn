# Testing and Debugging - Tutorial 12 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Debugging Tools](#debugging-tools)
3. [Console Methods](#console-methods)
4. [Error Handling](#error-handling)
5. [Unit Testing](#unit-testing)
6. [Integration Testing](#integration-testing)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Testing and debugging are **essential skills** for writing reliable, maintainable JavaScript applications. This tutorial covers tools, techniques, and best practices.

### What You'll Learn
- ✅ **Debugging Tools** - Browser dev tools and debugging techniques
- ✅ **Console Methods** - Advanced console logging and debugging
- ✅ **Error Handling** - Comprehensive error management
- ✅ **Unit Testing** - Writing and running tests
- ✅ **Integration Testing** - Testing component interactions

---

## Debugging Tools

### Browser Developer Tools
```javascript
// Using breakpoints
function calculateTotal(items) {
    let total = 0;
    
    for (let item of items) {
        debugger; // Breakpoint here
        total += item.price * item.quantity;
    }
    
    return total;
}

const items = [
    { name: 'Laptop', price: 999, quantity: 1 },
    { name: 'Mouse', price: 29, quantity: 2 }
];

const total = calculateTotal(items);
console.log('Total:', total);
```

### Debugging Techniques
```javascript
// Conditional breakpoints
function processUsers(users) {
    for (let user of users) {
        if (user.age > 18) { // Set breakpoint with condition: user.age > 18
            console.log('Processing adult user:', user.name);
        }
    }
}

// Watch expressions
function complexCalculation(a, b, c) {
    const step1 = a * b;
    const step2 = step1 + c;
    const step3 = step2 / 2;
    
    // Watch: step1, step2, step3
    return step3;
}

// Call stack inspection
function functionA() {
    functionB();
}

function functionB() {
    functionC();
}

function functionC() {
    console.trace('Call stack trace');
}

functionA();
```

### Performance Profiling
```javascript
// Performance timing
function measurePerformance(name, fn) {
    const start = performance.now();
    const result = fn();
    const end = performance.now();
    
    console.log(`${name} took ${end - start} milliseconds`);
    return result;
}

// Memory usage
function getMemoryUsage() {
    if (performance.memory) {
        return {
            used: Math.round(performance.memory.usedJSHeapSize / 1024 / 1024),
            total: Math.round(performance.memory.totalJSHeapSize / 1024 / 1024),
            limit: Math.round(performance.memory.jsHeapSizeLimit / 1024 / 1024)
        };
    }
    return null;
}

// Usage
const result = measurePerformance('Array processing', () => {
    return new Array(1000000)
        .fill(0)
        .map((_, i) => i * 2)
        .filter(x => x % 4 === 0)
        .reduce((sum, x) => sum + x, 0);
});

console.log('Memory usage:', getMemoryUsage());
```

---

## Console Methods

### Basic Console Methods
```javascript
// console.log - Basic logging
console.log('Hello, World!');
console.log('User:', { name: 'John', age: 25 });
console.log('Array:', [1, 2, 3, 4, 5]);

// console.error - Error logging
console.error('Something went wrong!');
console.error('Error details:', new Error('Test error'));

// console.warn - Warning messages
console.warn('This is a warning message');
console.warn('Deprecated function used');

// console.info - Informational messages
console.info('Application started');
console.info('User logged in:', 'john@example.com');
```

### Advanced Console Methods
```javascript
// console.table - Tabular data
const users = [
    { name: 'John', age: 25, city: 'New York' },
    { name: 'Jane', age: 30, city: 'Los Angeles' },
    { name: 'Bob', age: 35, city: 'Chicago' }
];

console.table(users);

// console.group - Grouped logging
console.group('User Authentication');
console.log('Checking credentials...');
console.log('Validating token...');
console.log('User authenticated successfully');
console.groupEnd();

// console.count - Counting occurrences
function processItem(item) {
    console.count('Items processed');
    return item * 2;
}

processItem(1); // Items processed: 1
processItem(2); // Items processed: 2
processItem(3); // Items processed: 3

// console.time - Timing operations
console.time('Data processing');
// Simulate data processing
setTimeout(() => {
    console.timeEnd('Data processing');
}, 1000);
```

### Debugging Console Methods
```javascript
// console.trace - Stack trace
function functionA() {
    functionB();
}

function functionB() {
    functionC();
}

function functionC() {
    console.trace('Call stack trace');
}

functionA();

// console.assert - Assertions
function divide(a, b) {
    console.assert(b !== 0, 'Division by zero is not allowed');
    return a / b;
}

divide(10, 2); // No output
divide(10, 0); // Assertion failed: Division by zero is not allowed

// console.dir - Object inspection
const complexObject = {
    name: 'John',
    address: {
        street: '123 Main St',
        city: 'New York',
        coordinates: {
            lat: 40.7128,
            lng: -74.0060
        }
    },
    hobbies: ['reading', 'gaming', 'cooking']
};

console.dir(complexObject);
```

### Custom Console Methods
```javascript
// Custom logging levels
const Logger = {
    levels: {
        ERROR: 0,
        WARN: 1,
        INFO: 2,
        DEBUG: 3
    },
    
    currentLevel: 2,
    
    log(level, message, ...args) {
        if (level <= this.currentLevel) {
            const timestamp = new Date().toISOString();
            const levelName = Object.keys(this.levels)[level];
            console.log(`[${timestamp}] [${levelName}] ${message}`, ...args);
        }
    },
    
    error(message, ...args) {
        this.log(this.levels.ERROR, message, ...args);
    },
    
    warn(message, ...args) {
        this.log(this.levels.WARN, message, ...args);
    },
    
    info(message, ...args) {
        this.log(this.levels.INFO, message, ...args);
    },
    
    debug(message, ...args) {
        this.log(this.levels.DEBUG, message, ...args);
    }
};

// Usage
Logger.error('Critical error occurred');
Logger.warn('Deprecated function used');
Logger.info('User logged in');
Logger.debug('Processing data...');
```

---

## Error Handling

### Error Types and Handling
```javascript
// Custom error classes
class ValidationError extends Error {
    constructor(message, field) {
        super(message);
        this.name = 'ValidationError';
        this.field = field;
    }
}

class NetworkError extends Error {
    constructor(message, statusCode) {
        super(message);
        this.name = 'NetworkError';
        this.statusCode = statusCode;
    }
}

// Error handling patterns
function validateUser(userData) {
    if (!userData.name) {
        throw new ValidationError('Name is required', 'name');
    }
    
    if (!userData.email) {
        throw new ValidationError('Email is required', 'email');
    }
    
    if (!userData.email.includes('@')) {
        throw new ValidationError('Invalid email format', 'email');
    }
    
    return true;
}

function fetchUserData(userId) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (userId < 0) {
                reject(new NetworkError('User not found', 404));
            } else {
                resolve({ id: userId, name: 'John', email: 'john@example.com' });
            }
        }, 1000);
    });
}

// Error handling in async functions
async function processUser(userId, userData) {
    try {
        // Validate user data
        validateUser(userData);
        
        // Fetch existing user
        const existingUser = await fetchUserData(userId);
        
        // Process user
        const processedUser = {
            ...existingUser,
            ...userData,
            updatedAt: new Date()
        };
        
        return processedUser;
    } catch (error) {
        if (error instanceof ValidationError) {
            console.error(`Validation error in ${error.field}: ${error.message}`);
            throw error;
        } else if (error instanceof NetworkError) {
            console.error(`Network error: ${error.message} (${error.statusCode})`);
            throw error;
        } else {
            console.error('Unexpected error:', error);
            throw new Error('An unexpected error occurred');
        }
    }
}
```

### Global Error Handling
```javascript
// Global error handlers
window.addEventListener('error', (event) => {
    console.error('Global error:', event.error);
    console.error('Error details:', {
        message: event.message,
        filename: event.filename,
        lineno: event.lineno,
        colno: event.colno
    });
});

window.addEventListener('unhandledrejection', (event) => {
    console.error('Unhandled promise rejection:', event.reason);
    event.preventDefault(); // Prevent default behavior
});

// Error boundary for React components
class ErrorBoundary extends React.Component {
    constructor(props) {
        super(props);
        this.state = { hasError: false, error: null };
    }
    
    static getDerivedStateFromError(error) {
        return { hasError: true, error };
    }
    
    componentDidCatch(error, errorInfo) {
        console.error('Error caught by boundary:', error);
        console.error('Error info:', errorInfo);
    }
    
    render() {
        if (this.state.hasError) {
            return <h1>Something went wrong.</h1>;
        }
        
        return this.props.children;
    }
}
```

---

## Unit Testing

### Basic Testing Framework
```javascript
// Simple testing framework
class TestFramework {
    constructor() {
        this.tests = [];
        this.passed = 0;
        this.failed = 0;
    }
    
    test(name, fn) {
        this.tests.push({ name, fn });
    }
    
    assert(condition, message) {
        if (!condition) {
            throw new Error(message || 'Assertion failed');
        }
    }
    
    assertEqual(actual, expected, message) {
        if (actual !== expected) {
            throw new Error(message || `Expected ${expected}, got ${actual}`);
        }
    }
    
    assertThrows(fn, expectedError, message) {
        try {
            fn();
            throw new Error(message || 'Expected function to throw');
        } catch (error) {
            if (expectedError && !(error instanceof expectedError)) {
                throw new Error(message || `Expected ${expectedError.name}, got ${error.constructor.name}`);
            }
        }
    }
    
    async run() {
        console.log('Running tests...\n');
        
        for (const test of this.tests) {
            try {
                await test.fn();
                console.log(`✅ ${test.name}`);
                this.passed++;
            } catch (error) {
                console.log(`❌ ${test.name}: ${error.message}`);
                this.failed++;
            }
        }
        
        console.log(`\nTests completed: ${this.passed} passed, ${this.failed} failed`);
    }
}

// Usage
const test = new TestFramework();

// Test cases
test.test('Addition works correctly', () => {
    test.assertEqual(2 + 2, 4);
    test.assertEqual(5 + 3, 8);
});

test.test('String concatenation', () => {
    test.assertEqual('Hello' + ' ' + 'World', 'Hello World');
});

test.test('Array methods', () => {
    const arr = [1, 2, 3, 4, 5];
    test.assertEqual(arr.length, 5);
    test.assertEqual(arr[0], 1);
    test.assertEqual(arr[arr.length - 1], 5);
});

test.test('Error handling', () => {
    test.assertThrows(() => {
        throw new Error('Test error');
    }, Error);
});

// Run tests
test.run();
```

### Testing Utilities
```javascript
// Testing utilities
class TestUtils {
    static createMockFunction(returnValue) {
        const calls = [];
        const mockFn = (...args) => {
            calls.push(args);
            return returnValue;
        };
        mockFn.calls = calls;
        mockFn.wasCalled = () => calls.length > 0;
        mockFn.wasCalledWith = (...args) => {
            return calls.some(call => 
                call.length === args.length && 
                call.every((arg, i) => arg === args[i])
            );
        };
        return mockFn;
    }
    
    static createSpy(object, methodName) {
        const originalMethod = object[methodName];
        const spy = this.createMockFunction();
        
        object[methodName] = (...args) => {
            spy(...args);
            return originalMethod.apply(object, args);
        };
        
        spy.restore = () => {
            object[methodName] = originalMethod;
        };
        
        return spy;
    }
    
    static async waitFor(condition, timeout = 1000) {
        const start = Date.now();
        
        while (!condition()) {
            if (Date.now() - start > timeout) {
                throw new Error('Timeout waiting for condition');
            }
            await new Promise(resolve => setTimeout(resolve, 10));
        }
    }
}

// Usage
const test = new TestFramework();

test.test('Mock function works', () => {
    const mockFn = TestUtils.createMockFunction('test result');
    
    test.assertEqual(mockFn('arg1', 'arg2'), 'test result');
    test.assert(mockFn.wasCalled());
    test.assert(mockFn.wasCalledWith('arg1', 'arg2'));
});

test.test('Spy works', () => {
    const obj = {
        method: (x) => x * 2
    };
    
    const spy = TestUtils.createSpy(obj, 'method');
    
    test.assertEqual(obj.method(5), 10);
    test.assert(spy.wasCalledWith(5));
    
    spy.restore();
});
```

---

## Integration Testing

### API Testing
```javascript
// API testing utilities
class ApiTester {
    constructor(baseUrl) {
        this.baseUrl = baseUrl;
    }
    
    async request(method, endpoint, data = null) {
        const url = `${this.baseUrl}${endpoint}`;
        const options = {
            method,
            headers: {
                'Content-Type': 'application/json'
            }
        };
        
        if (data) {
            options.body = JSON.stringify(data);
        }
        
        const response = await fetch(url, options);
        const responseData = await response.json();
        
        return {
            status: response.status,
            data: responseData,
            ok: response.ok
        };
    }
    
    async get(endpoint) {
        return this.request('GET', endpoint);
    }
    
    async post(endpoint, data) {
        return this.request('POST', endpoint, data);
    }
    
    async put(endpoint, data) {
        return this.request('PUT', endpoint, data);
    }
    
    async delete(endpoint) {
        return this.request('DELETE', endpoint);
    }
}

// Integration tests
const test = new TestFramework();
const api = new ApiTester('https://jsonplaceholder.typicode.com');

test.test('GET /posts returns posts', async () => {
    const response = await api.get('/posts');
    
    test.assert(response.ok);
    test.assert(Array.isArray(response.data));
    test.assert(response.data.length > 0);
    test.assert(response.data[0].hasOwnProperty('id'));
    test.assert(response.data[0].hasOwnProperty('title'));
});

test.test('POST /posts creates new post', async () => {
    const newPost = {
        title: 'Test Post',
        body: 'This is a test post',
        userId: 1
    };
    
    const response = await api.post('/posts', newPost);
    
    test.assert(response.ok);
    test.assert(response.data.hasOwnProperty('id'));
    test.assertEqual(response.data.title, newPost.title);
    test.assertEqual(response.data.body, newPost.body);
});

test.test('PUT /posts updates post', async () => {
    const updatedPost = {
        id: 1,
        title: 'Updated Post',
        body: 'This post has been updated',
        userId: 1
    };
    
    const response = await api.put('/posts/1', updatedPost);
    
    test.assert(response.ok);
    test.assertEqual(response.data.title, updatedPost.title);
    test.assertEqual(response.data.body, updatedPost.body);
});

test.test('DELETE /posts deletes post', async () => {
    const response = await api.delete('/posts/1');
    
    test.assert(response.ok);
});

// Run tests
test.run();
```

### Component Testing
```javascript
// Component testing utilities
class ComponentTester {
    constructor(container) {
        this.container = container;
    }
    
    render(component) {
        this.container.innerHTML = component;
        return this.container;
    }
    
    querySelector(selector) {
        return this.container.querySelector(selector);
    }
    
    querySelectorAll(selector) {
        return this.container.querySelectorAll(selector);
    }
    
    click(selector) {
        const element = this.querySelector(selector);
        if (element) {
            element.click();
        }
        return this;
    }
    
    type(selector, text) {
        const element = this.querySelector(selector);
        if (element) {
            element.value = text;
            element.dispatchEvent(new Event('input'));
        }
        return this;
    }
    
    getText(selector) {
        const element = this.querySelector(selector);
        return element ? element.textContent : null;
    }
    
    hasClass(selector, className) {
        const element = this.querySelector(selector);
        return element ? element.classList.contains(className) : false;
    }
}

// Component tests
const test = new TestFramework();
const container = document.createElement('div');
const tester = new ComponentTester(container);

test.test('Button component renders correctly', () => {
    const buttonHtml = '<button class="btn btn-primary">Click me</button>';
    tester.render(buttonHtml);
    
    const button = tester.querySelector('.btn');
    test.assert(button !== null);
    test.assertEqual(tester.getText('.btn'), 'Click me');
    test.assert(tester.hasClass('.btn', 'btn-primary'));
});

test.test('Form component handles input', () => {
    const formHtml = `
        <form>
            <input type="text" id="name" placeholder="Enter name">
            <button type="submit">Submit</button>
        </form>
    `;
    
    tester.render(formHtml);
    
    tester.type('#name', 'John Doe');
    test.assertEqual(tester.querySelector('#name').value, 'John Doe');
});

test.test('List component renders items', () => {
    const listHtml = `
        <ul class="item-list">
            <li class="item">Item 1</li>
            <li class="item">Item 2</li>
            <li class="item">Item 3</li>
        </ul>
    `;
    
    tester.render(listHtml);
    
    const items = tester.querySelectorAll('.item');
    test.assertEqual(items.length, 3);
    test.assertEqual(tester.getText('.item'), 'Item 1');
});

// Run tests
test.run();
```

---

## Practice Exercises

### Exercise 1: Calculator Testing
```javascript
class Calculator {
    add(a, b) {
        if (typeof a !== 'number' || typeof b !== 'number') {
            throw new Error('Both arguments must be numbers');
        }
        return a + b;
    }
    
    subtract(a, b) {
        if (typeof a !== 'number' || typeof b !== 'number') {
            throw new Error('Both arguments must be numbers');
        }
        return a - b;
    }
    
    multiply(a, b) {
        if (typeof a !== 'number' || typeof b !== 'number') {
            throw new Error('Both arguments must be numbers');
        }
        return a * b;
    }
    
    divide(a, b) {
        if (typeof a !== 'number' || typeof b !== 'number') {
            throw new Error('Both arguments must be numbers');
        }
        if (b === 0) {
            throw new Error('Division by zero is not allowed');
        }
        return a / b;
    }
}

// Test suite
const test = new TestFramework();
const calc = new Calculator();

test.test('Addition works correctly', () => {
    test.assertEqual(calc.add(2, 3), 5);
    test.assertEqual(calc.add(-1, 1), 0);
    test.assertEqual(calc.add(0, 0), 0);
});

test.test('Subtraction works correctly', () => {
    test.assertEqual(calc.subtract(5, 3), 2);
    test.assertEqual(calc.subtract(1, 1), 0);
    test.assertEqual(calc.subtract(0, 5), -5);
});

test.test('Multiplication works correctly', () => {
    test.assertEqual(calc.multiply(2, 3), 6);
    test.assertEqual(calc.multiply(-2, 3), -6);
    test.assertEqual(calc.multiply(0, 5), 0);
});

test.test('Division works correctly', () => {
    test.assertEqual(calc.divide(6, 2), 3);
    test.assertEqual(calc.divide(5, 2), 2.5);
    test.assertEqual(calc.divide(0, 5), 0);
});

test.test('Error handling for invalid inputs', () => {
    test.assertThrows(() => calc.add('2', 3), Error);
    test.assertThrows(() => calc.subtract(2, '3'), Error);
    test.assertThrows(() => calc.multiply('2', '3'), Error);
    test.assertThrows(() => calc.divide('2', 3), Error);
});

test.test('Error handling for division by zero', () => {
    test.assertThrows(() => calc.divide(5, 0), Error);
    test.assertThrows(() => calc.divide(0, 0), Error);
});

// Run tests
test.run();
```

### Exercise 2: User Management Testing
```javascript
class UserManager {
    constructor() {
        this.users = [];
        this.currentUser = null;
    }
    
    addUser(userData) {
        if (!userData.name || !userData.email) {
            throw new Error('Name and email are required');
        }
        
        if (this.users.find(user => user.email === userData.email)) {
            throw new Error('User with this email already exists');
        }
        
        const user = {
            id: Date.now().toString(),
            ...userData,
            createdAt: new Date()
        };
        
        this.users.push(user);
        return user;
    }
    
    getUserById(id) {
        const user = this.users.find(user => user.id === id);
        if (!user) {
            throw new Error('User not found');
        }
        return user;
    }
    
    updateUser(id, updates) {
        const user = this.getUserById(id);
        Object.assign(user, updates);
        return user;
    }
    
    deleteUser(id) {
        const userIndex = this.users.findIndex(user => user.id === id);
        if (userIndex === -1) {
            throw new Error('User not found');
        }
        return this.users.splice(userIndex, 1)[0];
    }
    
    getAllUsers() {
        return [...this.users];
    }
    
    setCurrentUser(user) {
        this.currentUser = user;
    }
    
    getCurrentUser() {
        return this.currentUser;
    }
}

// Test suite
const test = new TestFramework();
const userManager = new UserManager();

test.test('Add user works correctly', () => {
    const user = userManager.addUser({
        name: 'John Doe',
        email: 'john@example.com'
    });
    
    test.assert(user.id);
    test.assertEqual(user.name, 'John Doe');
    test.assertEqual(user.email, 'john@example.com');
    test.assert(user.createdAt instanceof Date);
});

test.test('Add user validation', () => {
    test.assertThrows(() => {
        userManager.addUser({ name: 'John' });
    }, Error);
    
    test.assertThrows(() => {
        userManager.addUser({ email: 'john@example.com' });
    }, Error);
});

test.test('Get user by ID works', () => {
    const user = userManager.addUser({
        name: 'Jane Doe',
        email: 'jane@example.com'
    });
    
    const retrievedUser = userManager.getUserById(user.id);
    test.assertEqual(retrievedUser.name, 'Jane Doe');
    test.assertEqual(retrievedUser.email, 'jane@example.com');
});

test.test('Get user by ID error handling', () => {
    test.assertThrows(() => {
        userManager.getUserById('nonexistent');
    }, Error);
});

test.test('Update user works', () => {
    const user = userManager.addUser({
        name: 'Bob Smith',
        email: 'bob@example.com'
    });
    
    const updatedUser = userManager.updateUser(user.id, {
        name: 'Bob Johnson',
        age: 30
    });
    
    test.assertEqual(updatedUser.name, 'Bob Johnson');
    test.assertEqual(updatedUser.age, 30);
    test.assertEqual(updatedUser.email, 'bob@example.com');
});

test.test('Delete user works', () => {
    const user = userManager.addUser({
        name: 'Alice Smith',
        email: 'alice@example.com'
    });
    
    const deletedUser = userManager.deleteUser(user.id);
    test.assertEqual(deletedUser.name, 'Alice Smith');
    
    test.assertThrows(() => {
        userManager.getUserById(user.id);
    }, Error);
});

test.test('Get all users works', () => {
    const initialCount = userManager.getAllUsers().length;
    
    userManager.addUser({
        name: 'Test User',
        email: 'test@example.com'
    });
    
    const users = userManager.getAllUsers();
    test.assertEqual(users.length, initialCount + 1);
});

test.test('Current user management', () => {
    const user = userManager.addUser({
        name: 'Current User',
        email: 'current@example.com'
    });
    
    userManager.setCurrentUser(user);
    const currentUser = userManager.getCurrentUser();
    
    test.assertEqual(currentUser.name, 'Current User');
    test.assertEqual(currentUser.email, 'current@example.com');
});

// Run tests
test.run();
```

### Exercise 3: API Integration Testing
```javascript
class ApiClient {
    constructor(baseUrl) {
        this.baseUrl = baseUrl;
    }
    
    async request(method, endpoint, data = null) {
        const url = `${this.baseUrl}${endpoint}`;
        const options = {
            method,
            headers: {
                'Content-Type': 'application/json'
            }
        };
        
        if (data) {
            options.body = JSON.stringify(data);
        }
        
        const response = await fetch(url, options);
        
        if (!response.ok) {
            throw new Error(`HTTP ${response.status}: ${response.statusText}`);
        }
        
        return await response.json();
    }
    
    async get(endpoint) {
        return this.request('GET', endpoint);
    }
    
    async post(endpoint, data) {
        return this.request('POST', endpoint, data);
    }
    
    async put(endpoint, data) {
        return this.request('PUT', endpoint, data);
    }
    
    async delete(endpoint) {
        return this.request('DELETE', endpoint);
    }
}

// Mock API for testing
class MockApiClient extends ApiClient {
    constructor() {
        super('https://api.example.com');
        this.data = {
            users: [
                { id: 1, name: 'John Doe', email: 'john@example.com' },
                { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
            ],
            posts: [
                { id: 1, title: 'Post 1', content: 'Content 1', userId: 1 },
                { id: 2, title: 'Post 2', content: 'Content 2', userId: 2 }
            ]
        };
    }
    
    async request(method, endpoint, data = null) {
        // Simulate network delay
        await new Promise(resolve => setTimeout(resolve, 100));
        
        const [resource, id] = endpoint.split('/').filter(Boolean);
        
        switch (method) {
            case 'GET':
                if (id) {
                    const item = this.data[resource].find(item => item.id == id);
                    if (!item) {
                        throw new Error('HTTP 404: Not Found');
                    }
                    return item;
                }
                return this.data[resource];
                
            case 'POST':
                const newItem = {
                    id: Date.now(),
                    ...data,
                    createdAt: new Date()
                };
                this.data[resource].push(newItem);
                return newItem;
                
            case 'PUT':
                const itemIndex = this.data[resource].findIndex(item => item.id == id);
                if (itemIndex === -1) {
                    throw new Error('HTTP 404: Not Found');
                }
                this.data[resource][itemIndex] = { ...this.data[resource][itemIndex], ...data };
                return this.data[resource][itemIndex];
                
            case 'DELETE':
                const deleteIndex = this.data[resource].findIndex(item => item.id == id);
                if (deleteIndex === -1) {
                    throw new Error('HTTP 404: Not Found');
                }
                return this.data[resource].splice(deleteIndex, 1)[0];
                
            default:
                throw new Error('HTTP 405: Method Not Allowed');
        }
    }
}

// Integration tests
const test = new TestFramework();
const api = new MockApiClient();

test.test('GET /users returns all users', async () => {
    const users = await api.get('/users');
    
    test.assert(Array.isArray(users));
    test.assertEqual(users.length, 2);
    test.assertEqual(users[0].name, 'John Doe');
    test.assertEqual(users[1].name, 'Jane Smith');
});

test.test('GET /users/1 returns specific user', async () => {
    const user = await api.get('/users/1');
    
    test.assertEqual(user.id, 1);
    test.assertEqual(user.name, 'John Doe');
    test.assertEqual(user.email, 'john@example.com');
});

test.test('POST /users creates new user', async () => {
    const newUser = {
        name: 'Bob Johnson',
        email: 'bob@example.com'
    };
    
    const user = await api.post('/users', newUser);
    
    test.assert(user.id);
    test.assertEqual(user.name, 'Bob Johnson');
    test.assertEqual(user.email, 'bob@example.com');
    test.assert(user.createdAt instanceof Date);
});

test.test('PUT /users/1 updates user', async () => {
    const updates = {
        name: 'John Updated',
        age: 30
    };
    
    const user = await api.put('/users/1', updates);
    
    test.assertEqual(user.name, 'John Updated');
    test.assertEqual(user.age, 30);
    test.assertEqual(user.email, 'john@example.com');
});

test.test('DELETE /users/1 deletes user', async () => {
    const user = await api.delete('/users/1');
    
    test.assertEqual(user.name, 'John Updated');
    
    test.assertThrows(async () => {
        await api.get('/users/1');
    }, Error);
});

test.test('Error handling for non-existent user', async () => {
    test.assertThrows(async () => {
        await api.get('/users/999');
    }, Error);
});

// Run tests
test.run();
```

---

## Summary

### Key Concepts Learned
- ✅ **Debugging Tools** - Browser dev tools and debugging techniques
- ✅ **Console Methods** - Advanced console logging and debugging
- ✅ **Error Handling** - Comprehensive error management
- ✅ **Unit Testing** - Writing and running tests
- ✅ **Integration Testing** - Testing component interactions

### Best Practices
- Use appropriate console methods for different log levels
- Implement comprehensive error handling
- Write tests for all critical functionality
- Use mocking for external dependencies
- Test both success and error scenarios

### Common Mistakes
- Not handling errors properly
- Writing tests that are too complex
- Not testing edge cases
- Ignoring error scenarios
- Not cleaning up after tests

---

**Excellent! You've mastered testing and debugging. Ready for Performance Optimization? 🚀**

**Next Tutorial:** [Performance Optimization](./13-performance-optimization.md)
