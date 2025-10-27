# Modern JavaScript Features - Tutorial 15 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Optional Chaining](#optional-chaining)
3. [Nullish Coalescing](#nullish-coalescing)
4. [Dynamic Imports](#dynamic-imports)
5. [BigInt](#bigint)
6. [Private Fields](#private-fields)
7. [Top-level Await](#top-level-await)
8. [Practice Exercises](#practice-exercises)
9. [Summary](#summary)

---

## Introduction

Modern JavaScript features provide **enhanced syntax and capabilities** that make code more readable, maintainable, and powerful. This tutorial covers the latest JavaScript features.

### What You'll Learn
- ✅ **Optional Chaining** - Safe property access
- ✅ **Nullish Coalescing** - Default value handling
- ✅ **Dynamic Imports** - Runtime module loading
- ✅ **BigInt** - Large integer support
- ✅ **Private Fields** - Class encapsulation
- ✅ **Top-level Await** - Async at module level

---

## Optional Chaining

### Basic Optional Chaining
```javascript
// Traditional way
function getUserName(user) {
    if (user && user.profile && user.profile.name) {
        return user.profile.name;
    }
    return 'Unknown';
}

// With optional chaining
function getUserNameModern(user) {
    return user?.profile?.name ?? 'Unknown';
}

// Examples
const user1 = {
    profile: {
        name: 'John Doe'
    }
};

const user2 = {
    profile: null
};

const user3 = null;

console.log(getUserNameModern(user1)); // 'John Doe'
console.log(getUserNameModern(user2)); // 'Unknown'
console.log(getUserNameModern(user3)); // 'Unknown'
```

### Optional Chaining with Arrays
```javascript
// Array access
const users = [
    { name: 'John', hobbies: ['reading', 'gaming'] },
    { name: 'Jane', hobbies: null },
    { name: 'Bob' }
];

// Safe array access
console.log(users[0]?.hobbies?.[0]); // 'reading'
console.log(users[1]?.hobbies?.[0]); // undefined
console.log(users[2]?.hobbies?.[0]); // undefined

// Array methods
const firstUser = users?.[0];
const firstHobby = firstUser?.hobbies?.[0];
const hobbyLength = firstUser?.hobbies?.length;

console.log(firstHobby); // 'reading'
console.log(hobbyLength); // 2
```

### Optional Chaining with Functions
```javascript
// Function calls
const api = {
    getUser: (id) => ({ id, name: 'John' }),
    getPosts: null
};

// Safe function calls
const user = api.getUser?.(123);
const posts = api.getPosts?.(456);

console.log(user); // { id: 123, name: 'John' }
console.log(posts); // undefined

// Method calls
const obj = {
    method: () => 'Hello World',
    anotherMethod: null
};

console.log(obj.method?.()); // 'Hello World'
console.log(obj.anotherMethod?.()); // undefined
console.log(obj.nonExistentMethod?.()); // undefined
```

### Optional Chaining in Real-world Examples
```javascript
// API response handling
async function fetchUserData(userId) {
    try {
        const response = await fetch(`/api/users/${userId}`);
        const data = await response.json();
        
        // Safe property access
        const userName = data?.user?.profile?.name ?? 'Unknown';
        const userEmail = data?.user?.contact?.email ?? 'No email';
        const userPosts = data?.user?.posts?.length ?? 0;
        
        return {
            name: userName,
            email: userEmail,
            postCount: userPosts
        };
    } catch (error) {
        console.error('Error fetching user data:', error);
        return null;
    }
}

// DOM manipulation
function updateUserProfile(userData) {
    const nameElement = document.querySelector('#user-name');
    const emailElement = document.querySelector('#user-email');
    const avatarElement = document.querySelector('#user-avatar');
    
    // Safe property access
    nameElement?.textContent = userData?.profile?.name ?? 'Unknown User';
    emailElement?.textContent = userData?.contact?.email ?? 'No email provided';
    avatarElement?.setAttribute('src', userData?.profile?.avatar ?? '/default-avatar.png');
}

// Configuration handling
function getAppConfig() {
    const config = window.APP_CONFIG;
    
    return {
        apiUrl: config?.api?.baseUrl ?? 'https://api.example.com',
        timeout: config?.api?.timeout ?? 5000,
        retries: config?.api?.retries ?? 3,
        features: {
            darkMode: config?.features?.darkMode ?? false,
            notifications: config?.features?.notifications ?? true
        }
    };
}
```

---

## Nullish Coalescing

### Basic Nullish Coalescing
```javascript
// Traditional way
function getValue(value) {
    if (value !== null && value !== undefined) {
        return value;
    }
    return 'default';
}

// With nullish coalescing
function getValueModern(value) {
    return value ?? 'default';
}

// Examples
console.log(getValueModern('Hello')); // 'Hello'
console.log(getValueModern('')); // ''
console.log(getValueModern(0)); // 0
console.log(getValueModern(false)); // false
console.log(getValueModern(null)); // 'default'
console.log(getValueModern(undefined)); // 'default'
```

### Nullish Coalescing vs Logical OR
```javascript
// Logical OR (||) - falsy values
console.log('' || 'default'); // 'default'
console.log(0 || 'default'); // 'default'
console.log(false || 'default'); // 'default'
console.log(null || 'default'); // 'default'
console.log(undefined || 'default'); // 'default'

// Nullish coalescing (??) - only null/undefined
console.log('' ?? 'default'); // ''
console.log(0 ?? 'default'); // 0
console.log(false ?? 'default'); // false
console.log(null ?? 'default'); // 'default'
console.log(undefined ?? 'default'); // 'default'
```

### Nullish Coalescing with Optional Chaining
```javascript
// Combined usage
function getUserInfo(user) {
    return {
        name: user?.profile?.name ?? 'Unknown',
        email: user?.contact?.email ?? 'No email',
        age: user?.profile?.age ?? 0,
        isActive: user?.status?.active ?? false
    };
}

// Real-world example
function processApiResponse(response) {
    const data = response?.data;
    
    return {
        users: data?.users ?? [],
        total: data?.pagination?.total ?? 0,
        page: data?.pagination?.page ?? 1,
        hasNext: data?.pagination?.hasNext ?? false
    };
}
```

### Nullish Coalescing with Functions
```javascript
// Function parameters
function createUser(userData = {}) {
    return {
        id: userData.id ?? generateId(),
        name: userData.name ?? 'Anonymous',
        email: userData.email ?? 'no-email@example.com',
        createdAt: userData.createdAt ?? new Date().toISOString()
    };
}

function generateId() {
    return Date.now().toString(36) + Math.random().toString(36).substr(2);
}

// Usage
const user1 = createUser({ name: 'John', email: 'john@example.com' });
const user2 = createUser({ name: 'Jane' });
const user3 = createUser();

console.log(user1); // { id: '...', name: 'John', email: 'john@example.com', createdAt: '...' }
console.log(user2); // { id: '...', name: 'Jane', email: 'no-email@example.com', createdAt: '...' }
console.log(user3); // { id: '...', name: 'Anonymous', email: 'no-email@example.com', createdAt: '...' }
```

---

## Dynamic Imports

### Basic Dynamic Imports
```javascript
// Dynamic import returns a promise
async function loadModule() {
    try {
        const module = await import('./math.js');
        console.log(module.PI); // 3.14159
        console.log(module.add(2, 3)); // 5
        return module;
    } catch (error) {
        console.error('Failed to load module:', error);
        throw error;
    }
}

// Usage
loadModule()
    .then(module => {
        console.log('Module loaded successfully');
    })
    .catch(error => {
        console.error('Module loading failed:', error);
    });
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
// Lazy loading components
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

### Dynamic Imports with Error Handling
```javascript
// Robust module loading
class ModuleLoader {
    constructor() {
        this.cache = new Map();
        this.loading = new Map();
    }
    
    async load(modulePath) {
        // Check cache first
        if (this.cache.has(modulePath)) {
            return this.cache.get(modulePath);
        }
        
        // Check if already loading
        if (this.loading.has(modulePath)) {
            return this.loading.get(modulePath);
        }
        
        // Start loading
        const loadPromise = this._loadModule(modulePath);
        this.loading.set(modulePath, loadPromise);
        
        try {
            const module = await loadPromise;
            this.cache.set(modulePath, module);
            this.loading.delete(modulePath);
            return module;
        } catch (error) {
            this.loading.delete(modulePath);
            throw error;
        }
    }
    
    async _loadModule(modulePath) {
        try {
            const module = await import(modulePath);
            return module.default || module;
        } catch (error) {
            console.error(`Failed to load module ${modulePath}:`, error);
            throw new Error(`Module ${modulePath} could not be loaded`);
        }
    }
    
    clearCache() {
        this.cache.clear();
    }
}

// Usage
const moduleLoader = new ModuleLoader();

// Load multiple modules
async function loadAppModules() {
    try {
        const [dashboard, analytics, reports] = await Promise.all([
            moduleLoader.load('./modules/dashboard.js'),
            moduleLoader.load('./modules/analytics.js'),
            moduleLoader.load('./modules/reports.js')
        ]);
        
        console.log('All modules loaded successfully');
        return { dashboard, analytics, reports };
    } catch (error) {
        console.error('Failed to load modules:', error);
        throw error;
    }
}
```

---

## BigInt

### Basic BigInt Usage
```javascript
// Creating BigInt values
const bigInt1 = 123n;
const bigInt2 = BigInt(456);
const bigInt3 = BigInt('789');

console.log(bigInt1); // 123n
console.log(bigInt2); // 456n
console.log(bigInt3); // 789n

// BigInt operations
console.log(bigInt1 + bigInt2); // 579n
console.log(bigInt2 - bigInt1); // 333n
console.log(bigInt1 * bigInt2); // 56088n
console.log(bigInt2 / bigInt1); // 3n (integer division)

// Comparison
console.log(bigInt1 > bigInt2); // false
console.log(bigInt1 === bigInt2); // false
console.log(bigInt1 == 123); // true (loose equality)
console.log(bigInt1 === 123n); // true (strict equality)
```

### BigInt with Large Numbers
```javascript
// Large number calculations
function calculateFactorial(n) {
    let result = 1n;
    for (let i = 2n; i <= n; i++) {
        result *= i;
    }
    return result;
}

// Calculate factorial of 100
const factorial100 = calculateFactorial(100n);
console.log(factorial100.toString()); // Very large number

// Fibonacci with BigInt
function fibonacciBigInt(n) {
    if (n <= 1n) return n;
    
    let a = 0n;
    let b = 1n;
    
    for (let i = 2n; i <= n; i++) {
        const temp = a + b;
        a = b;
        b = temp;
    }
    
    return b;
}

// Calculate large Fibonacci numbers
console.log(fibonacciBigInt(100n).toString()); // Very large number
```

### BigInt Conversion and Utilities
```javascript
// BigInt conversion
function convertToBigInt(value) {
    if (typeof value === 'bigint') {
        return value;
    }
    
    if (typeof value === 'number') {
        if (!Number.isInteger(value)) {
            throw new Error('Cannot convert non-integer number to BigInt');
        }
        return BigInt(value);
    }
    
    if (typeof value === 'string') {
        if (!/^-?\d+$/.test(value)) {
            throw new Error('Cannot convert non-numeric string to BigInt');
        }
        return BigInt(value);
    }
    
    throw new Error('Cannot convert value to BigInt');
}

// BigInt utilities
class BigIntUtils {
    static isBigInt(value) {
        return typeof value === 'bigint';
    }
    
    static toNumber(bigInt) {
        if (bigInt > Number.MAX_SAFE_INTEGER || bigInt < Number.MIN_SAFE_INTEGER) {
            throw new Error('BigInt value is too large to convert to number');
        }
        return Number(bigInt);
    }
    
    static toString(bigInt, radix = 10) {
        return bigInt.toString(radix);
    }
    
    static abs(bigInt) {
        return bigInt < 0n ? -bigInt : bigInt;
    }
    
    static min(...values) {
        return values.reduce((min, current) => current < min ? current : min);
    }
    
    static max(...values) {
        return values.reduce((max, current) => current > max ? current : max);
    }
}

// Usage
const bigInt1 = 123n;
const bigInt2 = -456n;

console.log(BigIntUtils.abs(bigInt2)); // 456n
console.log(BigIntUtils.min(bigInt1, bigInt2)); // -456n
console.log(BigIntUtils.max(bigInt1, bigInt2)); // 123n
```

---

## Private Fields

### Basic Private Fields
```javascript
class BankAccount {
    // Private fields
    #balance = 0;
    #accountNumber;
    #pin;
    
    constructor(accountNumber, pin, initialBalance = 0) {
        this.#accountNumber = accountNumber;
        this.#pin = pin;
        this.#balance = initialBalance;
    }
    
    // Public methods
    deposit(amount) {
        if (amount > 0) {
            this.#balance += amount;
            return true;
        }
        return false;
    }
    
    withdraw(amount, pin) {
        if (this.#validatePin(pin) && amount > 0 && amount <= this.#balance) {
            this.#balance -= amount;
            return true;
        }
        return false;
    }
    
    getBalance(pin) {
        if (this.#validatePin(pin)) {
            return this.#balance;
        }
        return null;
    }
    
    // Private method
    #validatePin(pin) {
        return this.#pin === pin;
    }
    
    // Getter for account number (read-only)
    get accountNumber() {
        return this.#accountNumber;
    }
}

// Usage
const account = new BankAccount('123456789', '1234', 1000);

console.log(account.accountNumber); // '123456789'
console.log(account.getBalance('1234')); // 1000
console.log(account.getBalance('0000')); // null

account.deposit(500);
console.log(account.getBalance('1234')); // 1500

account.withdraw(200, '1234');
console.log(account.getBalance('1234')); // 1300

// Private fields are not accessible from outside
console.log(account.#balance); // SyntaxError: Private field '#balance' must be declared in an enclosing class
```

### Private Fields with Inheritance
```javascript
class Animal {
    #name;
    #age;
    
    constructor(name, age) {
        this.#name = name;
        this.#age = age;
    }
    
    getName() {
        return this.#name;
    }
    
    getAge() {
        return this.#age;
    }
    
    #validateAge(age) {
        return age > 0;
    }
    
    setAge(age) {
        if (this.#validateAge(age)) {
            this.#age = age;
            return true;
        }
        return false;
    }
}

class Dog extends Animal {
    #breed;
    #tricks;
    
    constructor(name, age, breed) {
        super(name, age);
        this.#breed = breed;
        this.#tricks = [];
    }
    
    getBreed() {
        return this.#breed;
    }
    
    addTrick(trick) {
        this.#tricks.push(trick);
    }
    
    getTricks() {
        return [...this.#tricks];
    }
    
    // Private method
    #validateTrick(trick) {
        return typeof trick === 'string' && trick.length > 0;
    }
    
    addValidatedTrick(trick) {
        if (this.#validateTrick(trick)) {
            this.addTrick(trick);
            return true;
        }
        return false;
    }
}

// Usage
const dog = new Dog('Buddy', 3, 'Golden Retriever');

console.log(dog.getName()); // 'Buddy'
console.log(dog.getAge()); // 3
console.log(dog.getBreed()); // 'Golden Retriever'

dog.addTrick('sit');
dog.addTrick('stay');
console.log(dog.getTricks()); // ['sit', 'stay']

dog.addValidatedTrick('roll over');
console.log(dog.getTricks()); // ['sit', 'stay', 'roll over']

// Private fields are not accessible from outside
console.log(dog.#name); // SyntaxError
console.log(dog.#breed); // SyntaxError
```

### Private Fields with Static Methods
```javascript
class UserManager {
    static #users = new Map();
    static #nextId = 1;
    
    static createUser(name, email) {
        const id = this.#nextId++;
        const user = {
            id,
            name,
            email,
            createdAt: new Date()
        };
        
        this.#users.set(id, user);
        return id;
    }
    
    static getUser(id) {
        return this.#users.get(id);
    }
    
    static updateUser(id, updates) {
        const user = this.#users.get(id);
        if (user) {
            Object.assign(user, updates);
            return true;
        }
        return false;
    }
    
    static deleteUser(id) {
        return this.#users.delete(id);
    }
    
    static getAllUsers() {
        return Array.from(this.#users.values());
    }
    
    static getUserCount() {
        return this.#users.size;
    }
    
    // Private static method
    static #validateUserData(userData) {
        return userData && 
               typeof userData.name === 'string' && 
               typeof userData.email === 'string' &&
               userData.email.includes('@');
    }
    
    static createValidatedUser(userData) {
        if (this.#validateUserData(userData)) {
            return this.createUser(userData.name, userData.email);
        }
        throw new Error('Invalid user data');
    }
}

// Usage
const userId1 = UserManager.createUser('John Doe', 'john@example.com');
const userId2 = UserManager.createUser('Jane Smith', 'jane@example.com');

console.log(UserManager.getUser(userId1)); // { id: 1, name: 'John Doe', ... }
console.log(UserManager.getAllUsers()); // Array of users
console.log(UserManager.getUserCount()); // 2

UserManager.updateUser(userId1, { name: 'John Updated' });
console.log(UserManager.getUser(userId1).name); // 'John Updated'

// Private static fields are not accessible
console.log(UserManager.#users); // SyntaxError
console.log(UserManager.#nextId); // SyntaxError
```

---

## Top-level Await

### Basic Top-level Await
```javascript
// In a module file (e.g., main.js)
// Top-level await allows using await at the module level

// Load configuration
const config = await fetch('/api/config').then(res => res.json());

// Initialize application
const app = await import('./app.js');
await app.initialize(config);

// Load user data
const user = await fetch('/api/user').then(res => res.json());

// Render application
app.render(user);

console.log('Application initialized');
```

### Top-level Await with Error Handling
```javascript
// Error handling with top-level await
try {
    const config = await fetch('/api/config').then(res => res.json());
    const app = await import('./app.js');
    await app.initialize(config);
    console.log('Application initialized successfully');
} catch (error) {
    console.error('Failed to initialize application:', error);
    // Fallback initialization
    const fallbackConfig = { theme: 'light', language: 'en' };
    const app = await import('./app.js');
    await app.initialize(fallbackConfig);
    console.log('Application initialized with fallback config');
}
```

### Top-level Await with Conditional Loading
```javascript
// Conditional module loading with top-level await
const isDevelopment = process.env.NODE_ENV === 'development';

let logger;
if (isDevelopment) {
    logger = await import('./dev-logger.js');
} else {
    logger = await import('./prod-logger.js');
}

// Use logger
logger.log('Application started');

// Load features based on configuration
const config = await fetch('/api/config').then(res => res.json());
const features = [];

if (config.features.analytics) {
    features.push(await import('./analytics.js'));
}

if (config.features.reports) {
    features.push(await import('./reports.js'));
}

// Initialize features
for (const feature of features) {
    await feature.initialize();
}
```

### Top-level Await with Parallel Loading
```javascript
// Parallel loading with top-level await
const [config, user, theme] = await Promise.all([
    fetch('/api/config').then(res => res.json()),
    fetch('/api/user').then(res => res.json()),
    fetch('/api/theme').then(res => res.json())
]);

// Load modules in parallel
const [dashboard, analytics, reports] = await Promise.all([
    import('./modules/dashboard.js'),
    import('./modules/analytics.js'),
    import('./modules/reports.js')
]);

// Initialize application with all loaded data
const app = await import('./app.js');
await app.initialize({
    config,
    user,
    theme,
    modules: { dashboard, analytics, reports }
});

console.log('Application fully initialized');
```

---

## Practice Exercises

### Exercise 1: Modern User Management System
```javascript
class ModernUserManager {
    #users = new Map();
    #nextId = 1;
    
    constructor() {
        this.loadUsers();
    }
    
    // Create user with modern syntax
    async createUser(userData) {
        const id = this.#nextId++;
        const user = {
            id,
            name: userData?.name ?? 'Anonymous',
            email: userData?.email ?? 'no-email@example.com',
            age: userData?.age ?? 0,
            preferences: {
                theme: userData?.preferences?.theme ?? 'light',
                language: userData?.preferences?.language ?? 'en',
                notifications: userData?.preferences?.notifications ?? true
            },
            createdAt: new Date().toISOString(),
            updatedAt: new Date().toISOString()
        };
        
        this.#users.set(id, user);
        await this.saveUsers();
        return user;
    }
    
    // Get user with safe property access
    getUser(id) {
        const user = this.#users.get(id);
        if (!user) return null;
        
        return {
            id: user.id,
            name: user.name,
            email: user.email,
            age: user.age,
            preferences: { ...user.preferences },
            createdAt: user.createdAt,
            updatedAt: user.updatedAt
        };
    }
    
    // Update user with modern syntax
    async updateUser(id, updates) {
        const user = this.#users.get(id);
        if (!user) return null;
        
        // Safe property updates
        user.name = updates?.name ?? user.name;
        user.email = updates?.email ?? user.email;
        user.age = updates?.age ?? user.age;
        
        // Update preferences safely
        if (updates?.preferences) {
            user.preferences = {
                ...user.preferences,
                theme: updates.preferences?.theme ?? user.preferences.theme,
                language: updates.preferences?.language ?? user.preferences.language,
                notifications: updates.preferences?.notifications ?? user.preferences.notifications
            };
        }
        
        user.updatedAt = new Date().toISOString();
        await this.saveUsers();
        return user;
    }
    
    // Delete user
    async deleteUser(id) {
        const deleted = this.#users.delete(id);
        if (deleted) {
            await this.saveUsers();
        }
        return deleted;
    }
    
    // Get all users
    getAllUsers() {
        return Array.from(this.#users.values());
    }
    
    // Search users with modern syntax
    searchUsers(query) {
        if (!query) return this.getAllUsers();
        
        const searchTerm = query.toLowerCase();
        return this.getAllUsers().filter(user => 
            user.name?.toLowerCase().includes(searchTerm) ||
            user.email?.toLowerCase().includes(searchTerm)
        );
    }
    
    // Private methods
    async loadUsers() {
        try {
            const users = await this.#loadFromStorage();
            if (users) {
                this.#users = new Map(users.map(user => [user.id, user]));
                this.#nextId = Math.max(...users.map(u => u.id)) + 1;
            }
        } catch (error) {
            console.error('Failed to load users:', error);
        }
    }
    
    async saveUsers() {
        try {
            await this.#saveToStorage(Array.from(this.#users.values()));
        } catch (error) {
            console.error('Failed to save users:', error);
        }
    }
    
    async #loadFromStorage() {
        const data = localStorage.getItem('users');
        return data ? JSON.parse(data) : null;
    }
    
    async #saveToStorage(users) {
        localStorage.setItem('users', JSON.stringify(users));
    }
}

// Usage
const userManager = new ModernUserManager();

// Create users
const user1 = await userManager.createUser({
    name: 'John Doe',
    email: 'john@example.com',
    age: 30,
    preferences: {
        theme: 'dark',
        language: 'en',
        notifications: true
    }
});

const user2 = await userManager.createUser({
    name: 'Jane Smith',
    email: 'jane@example.com',
    age: 25
});

// Search users
const searchResults = userManager.searchUsers('john');
console.log('Search results:', searchResults);

// Update user
await userManager.updateUser(user1.id, {
    name: 'John Updated',
    preferences: {
        theme: 'light'
    }
});

console.log('Updated user:', userManager.getUser(user1.id));
```

### Exercise 2: Modern API Client with BigInt Support
```javascript
class ModernApiClient {
    constructor(baseUrl, options = {}) {
        this.baseUrl = baseUrl;
        this.options = {
            timeout: 5000,
            retries: 3,
            ...options
        };
        this.cache = new Map();
    }
    
    // Make request with modern syntax
    async request(endpoint, options = {}) {
        const url = `${this.baseUrl}${endpoint}`;
        const config = {
            ...this.options,
            ...options,
            headers: {
                'Content-Type': 'application/json',
                ...this.options.headers,
                ...options.headers
            }
        };
        
        try {
            const response = await this.#fetchWithTimeout(url, config);
            
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const contentType = response.headers.get('content-type');
            if (contentType?.includes('application/json')) {
                return await response.json();
            }
            
            return await response.text();
        } catch (error) {
            console.error('API request failed:', error);
            throw error;
        }
    }
    
    // Get with caching
    async get(endpoint, options = {}) {
        const cacheKey = `${endpoint}:${JSON.stringify(options)}`;
        
        if (this.cache.has(cacheKey)) {
            console.log('Cache hit for:', endpoint);
            return this.cache.get(cacheKey);
        }
        
        const data = await this.request(endpoint, { ...options, method: 'GET' });
        this.cache.set(cacheKey, data);
        return data;
    }
    
    // Post with modern syntax
    async post(endpoint, data, options = {}) {
        return this.request(endpoint, {
            ...options,
            method: 'POST',
            body: JSON.stringify(data)
        });
    }
    
    // Handle large numbers with BigInt
    async getLargeNumber(id) {
        const data = await this.get(`/large-numbers/${id}`);
        
        // Convert large numbers to BigInt
        if (data?.value && typeof data.value === 'string' && /^\d+$/.test(data.value)) {
            data.value = BigInt(data.value);
        }
        
        return data;
    }
    
    // Private methods
    async #fetchWithTimeout(url, config) {
        const controller = new AbortController();
        const timeoutId = setTimeout(() => controller.abort(), config.timeout);
        
        try {
            const response = await fetch(url, {
                ...config,
                signal: controller.signal
            });
            clearTimeout(timeoutId);
            return response;
        } catch (error) {
            clearTimeout(timeoutId);
            throw error;
        }
    }
}

// Usage
const api = new ModernApiClient('https://api.example.com');

// Get data with caching
const users = await api.get('/users');
console.log('Users:', users);

// Post data
const newUser = await api.post('/users', {
    name: 'John Doe',
    email: 'john@example.com'
});
console.log('Created user:', newUser);

// Handle large numbers
const largeNumber = await api.getLargeNumber('123');
console.log('Large number:', largeNumber.value); // BigInt
```

### Exercise 3: Modern Component System
```javascript
class ModernComponent {
    #element;
    #props;
    #state;
    #eventListeners = new Map();
    
    constructor(element, props = {}) {
        this.#element = element;
        this.#props = props;
        this.#state = {};
        this.init();
    }
    
    // Initialize component
    init() {
        this.render();
        this.setupEventListeners();
    }
    
    // Render component with modern syntax
    render() {
        const template = this.getTemplate();
        this.#element.innerHTML = template;
    }
    
    // Get template with safe property access
    getTemplate() {
        const { title, content, items = [] } = this.#props;
        const { count = 0 } = this.#state;
        
        return `
            <div class="component">
                <h2>${title ?? 'Default Title'}</h2>
                <p>${content ?? 'No content provided'}</p>
                <div class="items">
                    ${items?.map(item => `
                        <div class="item">
                            <span>${item?.name ?? 'Unnamed'}</span>
                            <span>${item?.value ?? 0}</span>
                        </div>
                    `).join('') ?? ''}
                </div>
                <div class="count">Count: ${count}</div>
                <button class="increment-btn">Increment</button>
            </div>
        `;
    }
    
    // Setup event listeners
    setupEventListeners() {
        const incrementBtn = this.#element.querySelector('.increment-btn');
        if (incrementBtn) {
            const handler = () => this.increment();
            incrementBtn.addEventListener('click', handler);
            this.#eventListeners.set('increment', { element: incrementBtn, handler });
        }
    }
    
    // Update state with modern syntax
    setState(newState) {
        this.#state = { ...this.#state, ...newState };
        this.render();
    }
    
    // Increment count
    increment() {
        this.setState({ count: (this.#state.count ?? 0) + 1 });
    }
    
    // Update props
    updateProps(newProps) {
        this.#props = { ...this.#props, ...newProps };
        this.render();
    }
    
    // Cleanup
    destroy() {
        this.#eventListeners.forEach(({ element, handler }) => {
            element.removeEventListener('click', handler);
        });
        this.#eventListeners.clear();
        this.#element.innerHTML = '';
    }
}

// Usage
const container = document.getElementById('app');
const component = new ModernComponent(container, {
    title: 'Modern Component',
    content: 'This is a modern component with BigInt support',
    items: [
        { name: 'Item 1', value: 100 },
        { name: 'Item 2', value: 200 },
        { name: 'Item 3', value: 300 }
    ]
});

// Update props
component.updateProps({
    title: 'Updated Component',
    content: 'This component has been updated'
});

// Update state
component.setState({ count: 5 });
```

---

## Summary

### Key Concepts Learned
- ✅ **Optional Chaining** - Safe property access with `?.`
- ✅ **Nullish Coalescing** - Default value handling with `??`
- ✅ **Dynamic Imports** - Runtime module loading
- ✅ **BigInt** - Large integer support
- ✅ **Private Fields** - Class encapsulation with `#`
- ✅ **Top-level Await** - Async at module level

### Best Practices
- Use optional chaining for safe property access
- Use nullish coalescing for default values
- Use dynamic imports for code splitting
- Use BigInt for large number calculations
- Use private fields for encapsulation
- Use top-level await for module initialization

### Common Mistakes
- Confusing `??` with `||`
- Not handling BigInt conversion errors
- Not cleaning up dynamic imports
- Not understanding private field scope
- Not handling top-level await errors

---

**Fantastic! You've mastered modern JavaScript features. Ready for Final Project? 🚀**

**Next Tutorial:** [Final Project](./16-final-project.md)
