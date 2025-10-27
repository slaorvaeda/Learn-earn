# Advanced JavaScript Concepts - Tutorial 11 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Prototypes and Inheritance](#prototypes-and-inheritance)
3. [Closures Deep Dive](#closures-deep-dive)
4. [This Keyword](#this-keyword)
5. [Memory Management](#memory-management)
6. [Performance Optimization](#performance-optimization)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Advanced JavaScript concepts are **essential for writing efficient, maintainable code** and understanding how JavaScript works under the hood.

### What You'll Learn
- ✅ **Prototypes and Inheritance** - JavaScript's object system
- ✅ **Closures Deep Dive** - Advanced closure patterns
- ✅ **This Keyword** - Understanding context binding
- ✅ **Memory Management** - Avoiding memory leaks
- ✅ **Performance Optimization** - Writing efficient code

---

## Prototypes and Inheritance

### Prototype Chain
```javascript
// Every object has a prototype
const obj = {};
console.log(obj.__proto__); // Object.prototype
console.log(obj.__proto__.__proto__); // null

// Function prototypes
function Person(name) {
    this.name = name;
}

Person.prototype.greet = function() {
    return `Hello, I'm ${this.name}`;
};

const person = new Person("John");
console.log(person.greet()); // "Hello, I'm John"

// Prototype chain
console.log(person.__proto__ === Person.prototype); // true
console.log(Person.prototype.__proto__ === Object.prototype); // true
```

### Prototypal Inheritance
```javascript
// Parent constructor
function Animal(name) {
    this.name = name;
}

Animal.prototype.speak = function() {
    return `${this.name} makes a sound`;
};

Animal.prototype.eat = function() {
    return `${this.name} is eating`;
};

// Child constructor
function Dog(name, breed) {
    Animal.call(this, name); // Call parent constructor
    this.breed = breed;
}

// Set up inheritance
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

// Add child-specific methods
Dog.prototype.speak = function() {
    return `${this.name} barks`;
};

Dog.prototype.fetch = function() {
    return `${this.name} is fetching`;
};

// Usage
const dog = new Dog("Buddy", "Golden Retriever");
console.log(dog.speak()); // "Buddy barks"
console.log(dog.eat()); // "Buddy is eating"
console.log(dog.fetch()); // "Buddy is fetching"
```

### Modern Class Inheritance
```javascript
class Animal {
    constructor(name) {
        this.name = name;
    }
    
    speak() {
        return `${this.name} makes a sound`;
    }
    
    eat() {
        return `${this.name} is eating`;
    }
}

class Dog extends Animal {
    constructor(name, breed) {
        super(name); // Call parent constructor
        this.breed = breed;
    }
    
    speak() {
        return `${this.name} barks`;
    }
    
    fetch() {
        return `${this.name} is fetching`;
    }
}

const dog = new Dog("Buddy", "Golden Retriever");
console.log(dog.speak()); // "Buddy barks"
console.log(dog.eat()); // "Buddy is eating"
console.log(dog.fetch()); // "Buddy is fetching"
```

### Object.create and Mixins
```javascript
// Base object
const animal = {
    speak() {
        return `${this.name} makes a sound`;
    },
    
    eat() {
        return `${this.name} is eating`;
    }
};

// Create object with specific prototype
const dog = Object.create(animal);
dog.name = "Buddy";
dog.breed = "Golden Retriever";
dog.speak = function() {
    return `${this.name} barks`;
};

// Mixin pattern
const canFly = {
    fly() {
        return `${this.name} is flying`;
    }
};

const canSwim = {
    swim() {
        return `${this.name} is swimming`;
    }
};

// Mix in capabilities
Object.assign(dog, canSwim);
console.log(dog.swim()); // "Buddy is swimming"
```

---

## Closures Deep Dive

### Closure with Private Variables
```javascript
function createCounter(initialValue = 0) {
    let count = initialValue;
    
    return {
        increment() {
            count++;
            return count;
        },
        
        decrement() {
            count--;
            return count;
        },
        
        getValue() {
            return count;
        },
        
        reset() {
            count = initialValue;
            return count;
        }
    };
}

const counter = createCounter(10);
console.log(counter.getValue()); // 10
console.log(counter.increment()); // 11
console.log(counter.increment()); // 12
console.log(counter.decrement()); // 11
console.log(counter.reset()); // 10
```

### Module Pattern
```javascript
const UserModule = (function() {
    let users = [];
    let currentUser = null;
    
    function validateUser(user) {
        return user && user.name && user.email;
    }
    
    function generateId() {
        return Date.now().toString(36) + Math.random().toString(36).substr(2);
    }
    
    return {
        addUser(userData) {
            if (!validateUser(userData)) {
                throw new Error('Invalid user data');
            }
            
            const user = {
                id: generateId(),
                ...userData,
                createdAt: new Date()
            };
            
            users.push(user);
            return user;
        },
        
        getUsers() {
            return [...users];
        },
        
        getUserById(id) {
            return users.find(user => user.id === id);
        },
        
        setCurrentUser(user) {
            currentUser = user;
        },
        
        getCurrentUser() {
            return currentUser;
        },
        
        removeUser(id) {
            users = users.filter(user => user.id !== id);
        }
    };
})();

// Usage
UserModule.addUser({ name: 'John', email: 'john@example.com' });
UserModule.addUser({ name: 'Jane', email: 'jane@example.com' });

console.log(UserModule.getUsers());
```

### Closure with Event Handlers
```javascript
function createButtonHandler(buttonName) {
    let clickCount = 0;
    
    return function(event) {
        clickCount++;
        console.log(`Button ${buttonName} clicked ${clickCount} times`);
        
        if (clickCount === 5) {
            console.log(`Button ${buttonName} has been clicked 5 times!`);
        }
    };
}

// Create different button handlers
const submitHandler = createButtonHandler('Submit');
const cancelHandler = createButtonHandler('Cancel');

// In real HTML:
// document.getElementById('submit-btn').addEventListener('click', submitHandler);
// document.getElementById('cancel-btn').addEventListener('click', cancelHandler);
```

### Closure with Memoization
```javascript
function memoize(fn) {
    const cache = new Map();
    
    return function(...args) {
        const key = JSON.stringify(args);
        
        if (cache.has(key)) {
            console.log('Cache hit!');
            return cache.get(key);
        }
        
        console.log('Computing...');
        const result = fn.apply(this, args);
        cache.set(key, result);
        return result;
    };
}

// Example: Fibonacci with memoization
const fibonacci = memoize(function(n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
});

console.log(fibonacci(10)); // Computing... 55
console.log(fibonacci(10)); // Cache hit! 55
```

---

## This Keyword

### This in Different Contexts
```javascript
// Global context
console.log(this); // Window object (in browser) or global object (in Node.js)

// Function context
function regularFunction() {
    console.log(this); // Window object (in browser) or global object (in Node.js)
}

// Method context
const obj = {
    name: 'John',
    greet() {
        console.log(this.name); // 'John'
    }
};

obj.greet();

// Arrow function context
const obj2 = {
    name: 'Jane',
    greet: () => {
        console.log(this.name); // undefined (inherits from outer scope)
    }
};

obj2.greet();
```

### This Binding Methods
```javascript
const person = {
    name: 'John',
    greet() {
        return `Hello, I'm ${this.name}`;
    }
};

// call() - calls function with specific this and arguments
const message1 = person.greet.call({ name: 'Alice' });
console.log(message1); // "Hello, I'm Alice"

// apply() - calls function with specific this and array of arguments
const message2 = person.greet.apply({ name: 'Bob' });
console.log(message2); // "Hello, I'm Bob"

// bind() - returns new function with bound this
const boundGreet = person.greet.bind({ name: 'Charlie' });
console.log(boundGreet()); // "Hello, I'm Charlie"
```

### This in Classes
```javascript
class Person {
    constructor(name) {
        this.name = name;
    }
    
    greet() {
        return `Hello, I'm ${this.name}`;
    }
    
    greetDelayed() {
        setTimeout(() => {
            console.log(this.greet()); // Arrow function preserves this
        }, 1000);
    }
    
    greetDelayedRegular() {
        setTimeout(function() {
            console.log(this.greet()); // Error: this is undefined
        }, 1000);
    }
}

const person = new Person('John');
person.greetDelayed(); // "Hello, I'm John" (after 1 second)
// person.greetDelayedRegular(); // Error
```

### This in Event Handlers
```javascript
class Button {
    constructor(element) {
        this.element = element;
        this.clickCount = 0;
        this.element.addEventListener('click', this.handleClick.bind(this));
    }
    
    handleClick() {
        this.clickCount++;
        console.log(`Button clicked ${this.clickCount} times`);
    }
}

// Alternative with arrow function
class ButtonArrow {
    constructor(element) {
        this.element = element;
        this.clickCount = 0;
        this.element.addEventListener('click', this.handleClick);
    }
    
    handleClick = () => {
        this.clickCount++;
        console.log(`Button clicked ${this.clickCount} times`);
    }
}
```

---

## Memory Management

### Memory Leaks Prevention
```javascript
// Memory leak example
function createLeak() {
    const largeArray = new Array(1000000).fill('data');
    
    return function() {
        console.log('This function holds reference to largeArray');
    };
}

// Better approach - avoid holding references
function createNoLeak() {
    return function() {
        console.log('This function doesn't hold unnecessary references');
    };
}

// Event listener cleanup
class EventManager {
    constructor() {
        this.listeners = new Map();
    }
    
    addListener(element, event, handler) {
        element.addEventListener(event, handler);
        
        if (!this.listeners.has(element)) {
            this.listeners.set(element, []);
        }
        this.listeners.get(element).push({ event, handler });
    }
    
    removeAllListeners(element) {
        if (this.listeners.has(element)) {
            const listeners = this.listeners.get(element);
            listeners.forEach(({ event, handler }) => {
                element.removeEventListener(event, handler);
            });
            this.listeners.delete(element);
        }
    }
    
    cleanup() {
        for (const [element, listeners] of this.listeners) {
            listeners.forEach(({ event, handler }) => {
                element.removeEventListener(event, handler);
            });
        }
        this.listeners.clear();
    }
}
```

### WeakMap and WeakSet
```javascript
// WeakMap - keys are weakly referenced
const weakMap = new WeakMap();
const obj1 = { id: 1 };
const obj2 = { id: 2 };

weakMap.set(obj1, 'value1');
weakMap.set(obj2, 'value2');

console.log(weakMap.get(obj1)); // 'value1'

// When obj1 is garbage collected, its entry in WeakMap is also removed
obj1 = null;

// WeakSet - values are weakly referenced
const weakSet = new WeakSet();
const obj3 = { id: 3 };
const obj4 = { id: 4 };

weakSet.add(obj3);
weakSet.add(obj4);

console.log(weakSet.has(obj3)); // true

// When obj3 is garbage collected, it's removed from WeakSet
obj3 = null;
```

### Memory Profiling
```javascript
// Memory usage monitoring
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

// Monitor memory usage
setInterval(() => {
    const memory = getMemoryUsage();
    if (memory) {
        console.log(`Memory: ${memory.used}MB / ${memory.total}MB (limit: ${memory.limit}MB)`);
    }
}, 5000);
```

---

## Performance Optimization

### Debouncing and Throttling
```javascript
// Debouncing - delay execution until after events stop
function debounce(func, delay) {
    let timeoutId;
    return function(...args) {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => func.apply(this, args), delay);
    };
}

// Throttling - limit execution frequency
function throttle(func, limit) {
    let inThrottle;
    return function(...args) {
        if (!inThrottle) {
            func.apply(this, args);
            inThrottle = true;
            setTimeout(() => inThrottle = false, limit);
        }
    };
}

// Usage
const debouncedSearch = debounce(function(query) {
    console.log('Searching for:', query);
}, 300);

const throttledScroll = throttle(function() {
    console.log('Scroll event');
}, 100);
```

### Lazy Loading
```javascript
// Lazy loading images
function lazyLoadImages() {
    const images = document.querySelectorAll('img[data-src]');
    const imageObserver = new IntersectionObserver((entries, observer) => {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                const img = entry.target;
                img.src = img.dataset.src;
                img.removeAttribute('data-src');
                observer.unobserve(img);
            }
        });
    });
    
    images.forEach(img => imageObserver.observe(img));
}

// Lazy loading modules
const lazyModules = new Map();

async function loadModule(moduleName) {
    if (lazyModules.has(moduleName)) {
        return lazyModules.get(moduleName);
    }
    
    const module = await import(`./modules/${moduleName}.js`);
    lazyModules.set(moduleName, module);
    return module;
}
```

### Performance Monitoring
```javascript
// Performance timing
function measurePerformance(name, fn) {
    const start = performance.now();
    const result = fn();
    const end = performance.now();
    
    console.log(`${name} took ${end - start} milliseconds`);
    return result;
}

// Usage
const result = measurePerformance('Array processing', () => {
    return new Array(1000000)
        .fill(0)
        .map((_, i) => i * 2)
        .filter(x => x % 4 === 0)
        .reduce((sum, x) => sum + x, 0);
});

// Web Vitals monitoring
function measureWebVitals() {
    // Largest Contentful Paint
    new PerformanceObserver((list) => {
        const entries = list.getEntries();
        const lastEntry = entries[entries.length - 1];
        console.log('LCP:', lastEntry.startTime);
    }).observe({ entryTypes: ['largest-contentful-paint'] });
    
    // First Input Delay
    new PerformanceObserver((list) => {
        const entries = list.getEntries();
        entries.forEach(entry => {
            console.log('FID:', entry.processingStart - entry.startTime);
        });
    }).observe({ entryTypes: ['first-input'] });
}
```

---

## Practice Exercises

### Exercise 1: Advanced Counter with Prototypes
```javascript
function Counter(initialValue = 0) {
    this.value = initialValue;
    this.history = [initialValue];
}

Counter.prototype.increment = function(amount = 1) {
    this.value += amount;
    this.history.push(this.value);
    return this;
};

Counter.prototype.decrement = function(amount = 1) {
    this.value -= amount;
    this.history.push(this.value);
    return this;
};

Counter.prototype.reset = function() {
    this.value = 0;
    this.history.push(this.value);
    return this;
};

Counter.prototype.getValue = function() {
    return this.value;
};

Counter.prototype.getHistory = function() {
    return [...this.history];
};

Counter.prototype.undo = function() {
    if (this.history.length > 1) {
        this.history.pop();
        this.value = this.history[this.history.length - 1];
    }
    return this;
};

// Usage
const counter = new Counter(10);
counter.increment(5).increment(3).decrement(2);
console.log(counter.getValue()); // 16
console.log(counter.getHistory()); // [10, 15, 18, 16]
counter.undo();
console.log(counter.getValue()); // 18
```

### Exercise 2: Module System with Closures
```javascript
const AppModule = (function() {
    let modules = new Map();
    let dependencies = new Map();
    
    function registerModule(name, moduleFactory, deps = []) {
        dependencies.set(name, deps);
        modules.set(name, moduleFactory);
    }
    
    function getModule(name) {
        if (!modules.has(name)) {
            throw new Error(`Module ${name} not found`);
        }
        
        const deps = dependencies.get(name) || [];
        const resolvedDeps = deps.map(dep => getModule(dep));
        
        return modules.get(name)(...resolvedDeps);
    }
    
    function createModule(moduleFactory, deps = []) {
        return function(name) {
            registerModule(name, moduleFactory, deps);
            return getModule(name);
        };
    }
    
    return {
        registerModule,
        getModule,
        createModule
    };
})();

// Define modules
const loggerModule = AppModule.createModule(() => {
    return {
        log: (message) => console.log(`[LOG] ${message}`),
        error: (message) => console.error(`[ERROR] ${message}`)
    };
});

const storageModule = AppModule.createModule(() => {
    const storage = new Map();
    
    return {
        set: (key, value) => storage.set(key, value),
        get: (key) => storage.get(key),
        remove: (key) => storage.delete(key),
        clear: () => storage.clear()
    };
});

const userModule = AppModule.createModule((logger, storage) => {
    return {
        createUser: (userData) => {
            const user = { id: Date.now(), ...userData };
            storage.set(user.id, user);
            logger.log(`User created: ${user.name}`);
            return user;
        },
        
        getUser: (id) => {
            const user = storage.get(id);
            if (user) {
                logger.log(`User retrieved: ${user.name}`);
            }
            return user;
        }
    };
}, ['logger', 'storage']);

// Usage
const logger = loggerModule('logger');
const storage = storageModule('storage');
const user = userModule('user');

const newUser = user.createUser({ name: 'John', email: 'john@example.com' });
const retrievedUser = user.getUser(newUser.id);
```

### Exercise 3: Performance-Optimized Data Processing
```javascript
class DataProcessor {
    constructor() {
        this.cache = new Map();
        this.workers = [];
        this.maxWorkers = navigator.hardwareConcurrency || 4;
    }
    
    // Memoized processing
    processData(data, processor) {
        const key = JSON.stringify(data);
        
        if (this.cache.has(key)) {
            console.log('Cache hit!');
            return this.cache.get(key);
        }
        
        console.log('Processing data...');
        const result = processor(data);
        this.cache.set(key, result);
        return result;
    }
    
    // Web Worker processing
    async processDataParallel(data, processor) {
        const chunkSize = Math.ceil(data.length / this.maxWorkers);
        const chunks = [];
        
        for (let i = 0; i < data.length; i += chunkSize) {
            chunks.push(data.slice(i, i + chunkSize));
        }
        
        const workerPromises = chunks.map(chunk => {
            return new Promise((resolve) => {
                const worker = new Worker('data-worker.js');
                worker.postMessage({ data: chunk, processor: processor.toString() });
                
                worker.onmessage = (event) => {
                    resolve(event.data);
                    worker.terminate();
                };
            });
        });
        
        const results = await Promise.all(workerPromises);
        return results.flat();
    }
    
    // Debounced processing
    createDebouncedProcessor(processor, delay = 300) {
        let timeoutId;
        
        return function(data) {
            clearTimeout(timeoutId);
            timeoutId = setTimeout(() => {
                processor(data);
            }, delay);
        };
    }
    
    // Batch processing
    createBatchProcessor(processor, batchSize = 100) {
        let batch = [];
        let timeoutId;
        
        return function(item) {
            batch.push(item);
            
            if (batch.length >= batchSize) {
                clearTimeout(timeoutId);
                processor(batch);
                batch = [];
            } else {
                clearTimeout(timeoutId);
                timeoutId = setTimeout(() => {
                    if (batch.length > 0) {
                        processor(batch);
                        batch = [];
                    }
                }, 100);
            }
        };
    }
    
    // Memory management
    clearCache() {
        this.cache.clear();
    }
    
    // Performance monitoring
    measurePerformance(name, fn) {
        const start = performance.now();
        const result = fn();
        const end = performance.now();
        
        console.log(`${name} took ${end - start} milliseconds`);
        return result;
    }
}

// Usage
const processor = new DataProcessor();

// Memoized processing
const data = [1, 2, 3, 4, 5];
const result = processor.processData(data, (arr) => arr.map(x => x * 2));
console.log(result); // [2, 4, 6, 8, 10]

// Debounced processing
const debouncedProcessor = processor.createDebouncedProcessor((data) => {
    console.log('Processing batch:', data);
});

// Batch processing
const batchProcessor = processor.createBatchProcessor((batch) => {
    console.log('Processing batch of', batch.length, 'items');
});

// Add items to batch
for (let i = 0; i < 250; i++) {
    batchProcessor(i);
}
```

---

## Summary

### Key Concepts Learned
- ✅ **Prototypes and Inheritance** - JavaScript's object system
- ✅ **Closures Deep Dive** - Advanced closure patterns and module system
- ✅ **This Keyword** - Understanding context binding and methods
- ✅ **Memory Management** - Avoiding memory leaks and optimization
- ✅ **Performance Optimization** - Writing efficient, scalable code

### Best Practices
- Use modern class syntax for inheritance
- Leverage closures for encapsulation
- Understand `this` binding in different contexts
- Monitor memory usage and prevent leaks
- Optimize performance with debouncing, throttling, and caching

### Common Mistakes
- Not understanding prototype chain
- Creating memory leaks with closures
- Confusing `this` context in different scenarios
- Not cleaning up event listeners
- Ignoring performance implications

---

**Fantastic! You've mastered advanced JavaScript concepts. Ready for Testing and Debugging? 🚀**

**Next Tutorial:** [Testing and Debugging](./12-testing-debugging.md)
