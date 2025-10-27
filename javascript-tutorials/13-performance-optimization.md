# Performance Optimization - Tutorial 13 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Performance Metrics](#performance-metrics)
3. [Code Optimization](#code-optimization)
4. [Memory Management](#memory-management)
5. [DOM Optimization](#dom-optimization)
6. [Network Optimization](#network-optimization)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Performance optimization is **crucial for creating fast, responsive applications**. This tutorial covers techniques to improve JavaScript performance and user experience.

### What You'll Learn
- ✅ **Performance Metrics** - Measuring and monitoring performance
- ✅ **Code Optimization** - Writing efficient JavaScript code
- ✅ **Memory Management** - Avoiding memory leaks and optimizing memory usage
- ✅ **DOM Optimization** - Efficient DOM manipulation
- ✅ **Network Optimization** - Reducing load times and improving responsiveness

---

## Performance Metrics

### Core Web Vitals
```javascript
// Largest Contentful Paint (LCP)
function measureLCP() {
    new PerformanceObserver((list) => {
        const entries = list.getEntries();
        const lastEntry = entries[entries.length - 1];
        console.log('LCP:', lastEntry.startTime);
    }).observe({ entryTypes: ['largest-contentful-paint'] });
}

// First Input Delay (FID)
function measureFID() {
    new PerformanceObserver((list) => {
        const entries = list.getEntries();
        entries.forEach(entry => {
            console.log('FID:', entry.processingStart - entry.startTime);
        });
    }).observe({ entryTypes: ['first-input'] });
}

// Cumulative Layout Shift (CLS)
function measureCLS() {
    let clsValue = 0;
    new PerformanceObserver((list) => {
        const entries = list.getEntries();
        entries.forEach(entry => {
            if (!entry.hadRecentInput) {
                clsValue += entry.value;
            }
        });
        console.log('CLS:', clsValue);
    }).observe({ entryTypes: ['layout-shift'] });
}

// Initialize measurements
measureLCP();
measureFID();
measureCLS();
```

### Performance Timing
```javascript
// Navigation timing
function getNavigationTiming() {
    const navigation = performance.getEntriesByType('navigation')[0];
    
    return {
        // DNS lookup time
        dns: navigation.domainLookupEnd - navigation.domainLookupStart,
        
        // TCP connection time
        tcp: navigation.connectEnd - navigation.connectStart,
        
        // Request time
        request: navigation.responseStart - navigation.requestStart,
        
        // Response time
        response: navigation.responseEnd - navigation.responseStart,
        
        // DOM processing time
        domProcessing: navigation.domContentLoadedEventEnd - navigation.responseEnd,
        
        // Total load time
        totalLoad: navigation.loadEventEnd - navigation.navigationStart
    };
}

// Resource timing
function getResourceTiming() {
    const resources = performance.getEntriesByType('resource');
    
    return resources.map(resource => ({
        name: resource.name,
        duration: resource.duration,
        size: resource.transferSize,
        type: resource.initiatorType
    }));
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
console.log('Navigation timing:', getNavigationTiming());
console.log('Resource timing:', getResourceTiming());
console.log('Memory usage:', getMemoryUsage());
```

### Performance Monitoring
```javascript
// Performance monitoring class
class PerformanceMonitor {
    constructor() {
        this.metrics = {};
        this.observers = [];
    }
    
    // Measure function execution time
    measureFunction(name, fn) {
        const start = performance.now();
        const result = fn();
        const end = performance.now();
        
        this.metrics[name] = {
            duration: end - start,
            timestamp: Date.now()
        };
        
        console.log(`${name} took ${end - start} milliseconds`);
        return result;
    }
    
    // Measure async function execution time
    async measureAsyncFunction(name, fn) {
        const start = performance.now();
        const result = await fn();
        const end = performance.now();
        
        this.metrics[name] = {
            duration: end - start,
            timestamp: Date.now()
        };
        
        console.log(`${name} took ${end - start} milliseconds`);
        return result;
    }
    
    // Monitor memory usage
    monitorMemory() {
        setInterval(() => {
            const memory = getMemoryUsage();
            if (memory) {
                console.log('Memory usage:', memory);
                
                // Alert if memory usage is high
                if (memory.used / memory.limit > 0.8) {
                    console.warn('High memory usage detected!');
                }
            }
        }, 5000);
    }
    
    // Get performance report
    getReport() {
        return {
            metrics: this.metrics,
            memory: getMemoryUsage(),
            navigation: getNavigationTiming(),
            resources: getResourceTiming()
        };
    }
}

// Usage
const monitor = new PerformanceMonitor();

// Measure function performance
const result = monitor.measureFunction('Array processing', () => {
    return new Array(1000000)
        .fill(0)
        .map((_, i) => i * 2)
        .filter(x => x % 4 === 0)
        .reduce((sum, x) => sum + x, 0);
});

// Monitor memory
monitor.monitorMemory();
```

---

## Code Optimization

### Algorithm Optimization
```javascript
// Inefficient O(n²) algorithm
function inefficientSearch(arr, target) {
    for (let i = 0; i < arr.length; i++) {
        for (let j = 0; j < arr.length; j++) {
            if (arr[i] + arr[j] === target) {
                return [i, j];
            }
        }
    }
    return -1;
}

// Optimized O(n) algorithm
function efficientSearch(arr, target) {
    const map = new Map();
    
    for (let i = 0; i < arr.length; i++) {
        const complement = target - arr[i];
        
        if (map.has(complement)) {
            return [map.get(complement), i];
        }
        
        map.set(arr[i], i);
    }
    
    return -1;
}

// Performance comparison
const testArray = Array.from({ length: 10000 }, (_, i) => i);
const target = 15000;

console.time('Inefficient');
inefficientSearch(testArray, target);
console.timeEnd('Inefficient');

console.time('Efficient');
efficientSearch(testArray, target);
console.timeEnd('Efficient');
```

### Loop Optimization
```javascript
// Inefficient loops
function inefficientLoops(arr) {
    let result = [];
    
    // Using for...in on arrays
    for (let index in arr) {
        if (arr[index] > 0) {
            result.push(arr[index] * 2);
        }
    }
    
    return result;
}

// Optimized loops
function optimizedLoops(arr) {
    let result = [];
    
    // Using for loop (fastest)
    for (let i = 0; i < arr.length; i++) {
        if (arr[i] > 0) {
            result.push(arr[i] * 2);
        }
    }
    
    return result;
}

// Even more optimized with pre-allocation
function highlyOptimizedLoops(arr) {
    const result = new Array(arr.length);
    let resultIndex = 0;
    
    for (let i = 0; i < arr.length; i++) {
        if (arr[i] > 0) {
            result[resultIndex++] = arr[i] * 2;
        }
    }
    
    return result.slice(0, resultIndex);
}

// Performance comparison
const testArray = Array.from({ length: 1000000 }, (_, i) => i - 500000);

console.time('Inefficient');
inefficientLoops(testArray);
console.timeEnd('Inefficient');

console.time('Optimized');
optimizedLoops(testArray);
console.timeEnd('Optimized');

console.time('Highly Optimized');
highlyOptimizedLoops(testArray);
console.timeEnd('Highly Optimized');
```

### Function Optimization
```javascript
// Inefficient function
function inefficientFunction(data) {
    let result = [];
    
    for (let i = 0; i < data.length; i++) {
        if (data[i].active) {
            result.push({
                id: data[i].id,
                name: data[i].name,
                email: data[i].email,
                fullName: data[i].firstName + ' ' + data[i].lastName
            });
        }
    }
    
    return result;
}

// Optimized function
function optimizedFunction(data) {
    const result = [];
    const dataLength = data.length;
    
    for (let i = 0; i < dataLength; i++) {
        const item = data[i];
        
        if (item.active) {
            result.push({
                id: item.id,
                name: item.name,
                email: item.email,
                fullName: item.firstName + ' ' + item.lastName
            });
        }
    }
    
    return result;
}

// Even more optimized with object destructuring
function highlyOptimizedFunction(data) {
    const result = [];
    const dataLength = data.length;
    
    for (let i = 0; i < dataLength; i++) {
        const { active, id, name, email, firstName, lastName } = data[i];
        
        if (active) {
            result.push({
                id,
                name,
                email,
                fullName: firstName + ' ' + lastName
            });
        }
    }
    
    return result;
}

// Performance comparison
const testData = Array.from({ length: 100000 }, (_, i) => ({
    id: i,
    name: `User ${i}`,
    email: `user${i}@example.com`,
    firstName: `First${i}`,
    lastName: `Last${i}`,
    active: i % 2 === 0
}));

console.time('Inefficient');
inefficientFunction(testData);
console.timeEnd('Inefficient');

console.time('Optimized');
optimizedFunction(testData);
console.timeEnd('Optimized');

console.time('Highly Optimized');
highlyOptimizedFunction(testData);
console.timeEnd('Highly Optimized');
```

---

## Memory Management

### Memory Leak Prevention
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

// Usage
const eventManager = new EventManager();
const button = document.getElementById('my-button');

eventManager.addListener(button, 'click', () => {
    console.log('Button clicked');
});

// Clean up when done
eventManager.removeAllListeners(button);
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

// Practical example: DOM element tracking
const elementData = new WeakMap();

function attachData(element, data) {
    elementData.set(element, data);
}

function getData(element) {
    return elementData.get(element);
}

// When DOM element is removed, its data is automatically cleaned up
const div = document.createElement('div');
attachData(div, { some: 'data' });
console.log(getData(div)); // { some: 'data' }

// Remove element from DOM
div.remove();
// elementData entry is automatically cleaned up
```

### Memory Pool Pattern
```javascript
// Object pool for reusing objects
class ObjectPool {
    constructor(createFn, resetFn, initialSize = 10) {
        this.createFn = createFn;
        this.resetFn = resetFn;
        this.pool = [];
        this.active = new Set();
        
        // Pre-populate pool
        for (let i = 0; i < initialSize; i++) {
            this.pool.push(this.createFn());
        }
    }
    
    acquire() {
        let obj;
        
        if (this.pool.length > 0) {
            obj = this.pool.pop();
        } else {
            obj = this.createFn();
        }
        
        this.active.add(obj);
        return obj;
    }
    
    release(obj) {
        if (this.active.has(obj)) {
            this.active.delete(obj);
            this.resetFn(obj);
            this.pool.push(obj);
        }
    }
    
    getStats() {
        return {
            poolSize: this.pool.length,
            activeCount: this.active.size,
            totalCreated: this.pool.length + this.active.size
        };
    }
}

// Usage example: Particle system
class Particle {
    constructor() {
        this.x = 0;
        this.y = 0;
        this.vx = 0;
        this.vy = 0;
        this.life = 0;
        this.active = false;
    }
    
    reset() {
        this.x = 0;
        this.y = 0;
        this.vx = 0;
        this.vy = 0;
        this.life = 0;
        this.active = false;
    }
    
    update() {
        this.x += this.vx;
        this.y += this.vy;
        this.life--;
        this.active = this.life > 0;
    }
}

// Create particle pool
const particlePool = new ObjectPool(
    () => new Particle(),
    (particle) => particle.reset(),
    100
);

// Use particles
const particles = [];
for (let i = 0; i < 50; i++) {
    const particle = particlePool.acquire();
    particle.x = Math.random() * 800;
    particle.y = Math.random() * 600;
    particle.vx = (Math.random() - 0.5) * 4;
    particle.vy = (Math.random() - 0.5) * 4;
    particle.life = 60;
    particle.active = true;
    particles.push(particle);
}

// Update and release particles
function updateParticles() {
    for (let i = particles.length - 1; i >= 0; i--) {
        const particle = particles[i];
        particle.update();
        
        if (!particle.active) {
            particlePool.release(particle);
            particles.splice(i, 1);
        }
    }
}

console.log('Pool stats:', particlePool.getStats());
```

---

## DOM Optimization

### Efficient DOM Manipulation
```javascript
// Inefficient DOM manipulation
function inefficientDOMUpdate(items) {
    const container = document.getElementById('container');
    
    // Clear container
    container.innerHTML = '';
    
    // Add each item individually
    items.forEach(item => {
        const div = document.createElement('div');
        div.className = 'item';
        div.textContent = item.name;
        container.appendChild(div);
    });
}

// Optimized DOM manipulation
function optimizedDOMUpdate(items) {
    const container = document.getElementById('container');
    
    // Use DocumentFragment for batch operations
    const fragment = document.createDocumentFragment();
    
    items.forEach(item => {
        const div = document.createElement('div');
        div.className = 'item';
        div.textContent = item.name;
        fragment.appendChild(div);
    });
    
    // Single DOM update
    container.innerHTML = '';
    container.appendChild(fragment);
}

// Even more optimized with innerHTML
function highlyOptimizedDOMUpdate(items) {
    const container = document.getElementById('container');
    
    // Build HTML string
    const html = items.map(item => 
        `<div class="item">${item.name}</div>`
    ).join('');
    
    // Single DOM update
    container.innerHTML = html;
}

// Performance comparison
const testItems = Array.from({ length: 10000 }, (_, i) => ({
    name: `Item ${i}`
}));

console.time('Inefficient DOM');
inefficientDOMUpdate(testItems);
console.timeEnd('Inefficient DOM');

console.time('Optimized DOM');
optimizedDOMUpdate(testItems);
console.timeEnd('Optimized DOM');

console.time('Highly Optimized DOM');
highlyOptimizedDOMUpdate(testItems);
console.timeEnd('Highly Optimized DOM');
```

### Virtual Scrolling
```javascript
// Virtual scrolling implementation
class VirtualScroller {
    constructor(container, itemHeight, renderItem) {
        this.container = container;
        this.itemHeight = itemHeight;
        this.renderItem = renderItem;
        this.data = [];
        this.visibleStart = 0;
        this.visibleEnd = 0;
        this.containerHeight = 0;
        this.scrollTop = 0;
        
        this.init();
    }
    
    init() {
        this.container.style.overflow = 'auto';
        this.container.style.position = 'relative';
        
        this.container.addEventListener('scroll', () => {
            this.handleScroll();
        });
        
        this.handleScroll();
    }
    
    setData(data) {
        this.data = data;
        this.updateContainerHeight();
        this.handleScroll();
    }
    
    updateContainerHeight() {
        const totalHeight = this.data.length * this.itemHeight;
        this.container.style.height = '400px';
        this.container.style.overflow = 'auto';
        
        // Create spacer element for total height
        let spacer = this.container.querySelector('.virtual-spacer');
        if (!spacer) {
            spacer = document.createElement('div');
            spacer.className = 'virtual-spacer';
            this.container.appendChild(spacer);
        }
        spacer.style.height = `${totalHeight}px`;
    }
    
    handleScroll() {
        this.scrollTop = this.container.scrollTop;
        this.containerHeight = this.container.clientHeight;
        
        this.visibleStart = Math.floor(this.scrollTop / this.itemHeight);
        this.visibleEnd = Math.min(
            this.visibleStart + Math.ceil(this.containerHeight / this.itemHeight) + 1,
            this.data.length
        );
        
        this.renderVisibleItems();
    }
    
    renderVisibleItems() {
        // Remove existing visible items
        const existingItems = this.container.querySelectorAll('.virtual-item');
        existingItems.forEach(item => item.remove());
        
        // Render visible items
        for (let i = this.visibleStart; i < this.visibleEnd; i++) {
            const item = this.renderItem(this.data[i], i);
            item.style.position = 'absolute';
            item.style.top = `${i * this.itemHeight}px`;
            item.style.height = `${this.itemHeight}px`;
            item.className = 'virtual-item';
            this.container.appendChild(item);
        }
    }
}

// Usage
const container = document.getElementById('virtual-container');
const itemHeight = 50;

const renderItem = (data, index) => {
    const div = document.createElement('div');
    div.textContent = `Item ${index}: ${data.name}`;
    div.style.padding = '10px';
    div.style.borderBottom = '1px solid #ccc';
    return div;
};

const scroller = new VirtualScroller(container, itemHeight, renderItem);

// Set data
const data = Array.from({ length: 100000 }, (_, i) => ({
    name: `Data item ${i}`
}));

scroller.setData(data);
```

---

## Network Optimization

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

// Lazy loading components
class LazyComponent {
    constructor(selector, loadFn) {
        this.selector = selector;
        this.loadFn = loadFn;
        this.loaded = false;
        this.observer = null;
    }
    
    init() {
        this.observer = new IntersectionObserver((entries) => {
            entries.forEach(entry => {
                if (entry.isIntersecting && !this.loaded) {
                    this.loadComponent();
                }
            });
        });
        
        const elements = document.querySelectorAll(this.selector);
        elements.forEach(el => this.observer.observe(el));
    }
    
    async loadComponent() {
        if (this.loaded) return;
        
        this.loaded = true;
        const elements = document.querySelectorAll(this.selector);
        
        for (const element of elements) {
            try {
                const component = await this.loadFn();
                element.innerHTML = component;
            } catch (error) {
                console.error('Failed to load component:', error);
            }
        }
    }
}

// Usage
const lazyComponent = new LazyComponent('.lazy-component', async () => {
    const module = await loadModule('heavy-component');
    return module.render();
});

lazyComponent.init();
```

### Caching Strategies
```javascript
// Simple cache implementation
class Cache {
    constructor(maxSize = 100) {
        this.cache = new Map();
        this.maxSize = maxSize;
    }
    
    get(key) {
        if (this.cache.has(key)) {
            // Move to end (LRU)
            const value = this.cache.get(key);
            this.cache.delete(key);
            this.cache.set(key, value);
            return value;
        }
        return null;
    }
    
    set(key, value) {
        if (this.cache.has(key)) {
            this.cache.delete(key);
        } else if (this.cache.size >= this.maxSize) {
            // Remove least recently used
            const firstKey = this.cache.keys().next().value;
            this.cache.delete(firstKey);
        }
        
        this.cache.set(key, value);
    }
    
    clear() {
        this.cache.clear();
    }
    
    size() {
        return this.cache.size;
    }
}

// HTTP cache with TTL
class HttpCache {
    constructor() {
        this.cache = new Map();
    }
    
    async get(url, options = {}) {
        const key = this.getCacheKey(url, options);
        const cached = this.cache.get(key);
        
        if (cached && Date.now() < cached.expires) {
            return cached.data;
        }
        
        const response = await fetch(url, options);
        const data = await response.json();
        
        this.cache.set(key, {
            data,
            expires: Date.now() + (options.ttl || 300000) // 5 minutes default
        });
        
        return data;
    }
    
    getCacheKey(url, options) {
        return `${url}:${JSON.stringify(options)}`;
    }
    
    clear() {
        this.cache.clear();
    }
}

// Usage
const httpCache = new HttpCache();

async function fetchUserData(userId) {
    return httpCache.get(`/api/users/${userId}`, { ttl: 600000 }); // 10 minutes
}
```

---

## Practice Exercises

### Exercise 1: Performance-Optimized Data Processing
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

### Exercise 2: Optimized Search Component
```javascript
class OptimizedSearch {
    constructor(container, options = {}) {
        this.container = container;
        this.options = {
            debounceDelay: 300,
            minQueryLength: 2,
            maxResults: 10,
            ...options
        };
        
        this.cache = new Map();
        this.debouncedSearch = this.debounce(this.search.bind(this), this.options.debounceDelay);
        this.init();
    }
    
    init() {
        this.container.innerHTML = `
            <input type="text" class="search-input" placeholder="Search...">
            <div class="search-results"></div>
        `;
        
        this.input = this.container.querySelector('.search-input');
        this.results = this.container.querySelector('.search-results');
        
        this.input.addEventListener('input', (e) => {
            this.debouncedSearch(e.target.value);
        });
    }
    
    debounce(func, delay) {
        let timeoutId;
        return function(...args) {
            clearTimeout(timeoutId);
            timeoutId = setTimeout(() => func.apply(this, args), delay);
        };
    }
    
    async search(query) {
        if (query.length < this.options.minQueryLength) {
            this.results.innerHTML = '';
            return;
        }
        
        // Check cache first
        if (this.cache.has(query)) {
            this.displayResults(this.cache.get(query));
            return;
        }
        
        try {
            const results = await this.performSearch(query);
            this.cache.set(query, results);
            this.displayResults(results);
        } catch (error) {
            console.error('Search error:', error);
            this.displayError('Search failed. Please try again.');
        }
    }
    
    async performSearch(query) {
        // Simulate API call
        await new Promise(resolve => setTimeout(resolve, 100));
        
        // Mock search results
        const allResults = [
            { id: 1, title: 'JavaScript Tutorial', category: 'Programming' },
            { id: 2, title: 'Python Guide', category: 'Programming' },
            { id: 3, title: 'Web Development', category: 'Programming' },
            { id: 4, title: 'Data Science', category: 'Programming' },
            { id: 5, title: 'Machine Learning', category: 'Programming' }
        ];
        
        return allResults
            .filter(item => 
                item.title.toLowerCase().includes(query.toLowerCase())
            )
            .slice(0, this.options.maxResults);
    }
    
    displayResults(results) {
        if (results.length === 0) {
            this.results.innerHTML = '<div class="no-results">No results found</div>';
            return;
        }
        
        const html = results.map(result => `
            <div class="search-result" data-id="${result.id}">
                <div class="result-title">${result.title}</div>
                <div class="result-category">${result.category}</div>
            </div>
        `).join('');
        
        this.results.innerHTML = html;
        
        // Add click handlers
        this.results.querySelectorAll('.search-result').forEach(item => {
            item.addEventListener('click', () => {
                this.selectResult(item.dataset.id);
            });
        });
    }
    
    displayError(message) {
        this.results.innerHTML = `<div class="error">${message}</div>`;
    }
    
    selectResult(id) {
        console.log('Selected result:', id);
        // Handle result selection
    }
    
    clearCache() {
        this.cache.clear();
    }
}

// Usage
const searchContainer = document.getElementById('search-container');
const search = new OptimizedSearch(searchContainer, {
    debounceDelay: 500,
    minQueryLength: 3,
    maxResults: 5
});
```

### Exercise 3: Memory-Efficient Image Gallery
```javascript
class ImageGallery {
    constructor(container, options = {}) {
        this.container = container;
        this.options = {
            imagesPerPage: 20,
            imageWidth: 200,
            imageHeight: 200,
            ...options
        };
        
        this.images = [];
        this.currentPage = 0;
        this.visibleImages = new Set();
        this.imageCache = new Map();
        this.observer = null;
        
        this.init();
    }
    
    init() {
        this.container.innerHTML = `
            <div class="gallery-container"></div>
            <div class="gallery-controls">
                <button id="prev-page">Previous</button>
                <span id="page-info">Page 1</span>
                <button id="next-page">Next</button>
            </div>
        `;
        
        this.galleryContainer = this.container.querySelector('.gallery-container');
        this.prevButton = this.container.querySelector('#prev-page');
        this.nextButton = this.container.querySelector('#next-page');
        this.pageInfo = this.container.querySelector('#page-info');
        
        this.prevButton.addEventListener('click', () => this.previousPage());
        this.nextButton.addEventListener('click', () => this.nextPage());
        
        this.setupIntersectionObserver();
    }
    
    setupIntersectionObserver() {
        this.observer = new IntersectionObserver((entries) => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    this.loadImage(entry.target);
                } else {
                    this.unloadImage(entry.target);
                }
            });
        });
    }
    
    setImages(images) {
        this.images = images;
        this.currentPage = 0;
        this.renderPage();
    }
    
    renderPage() {
        const startIndex = this.currentPage * this.options.imagesPerPage;
        const endIndex = Math.min(startIndex + this.options.imagesPerPage, this.images.length);
        const pageImages = this.images.slice(startIndex, endIndex);
        
        this.galleryContainer.innerHTML = '';
        
        pageImages.forEach((image, index) => {
            const imageElement = this.createImageElement(image, startIndex + index);
            this.galleryContainer.appendChild(imageElement);
            this.observer.observe(imageElement);
        });
        
        this.updatePageInfo();
        this.updateButtons();
    }
    
    createImageElement(image, index) {
        const div = document.createElement('div');
        div.className = 'gallery-item';
        div.dataset.index = index;
        div.style.width = `${this.options.imageWidth}px`;
        div.style.height = `${this.options.imageHeight}px`;
        div.style.border = '1px solid #ccc';
        div.style.display = 'inline-block';
        div.style.margin = '5px';
        div.style.position = 'relative';
        
        // Placeholder
        div.innerHTML = '<div class="placeholder">Loading...</div>';
        
        return div;
    }
    
    loadImage(imageElement) {
        const index = parseInt(imageElement.dataset.index);
        const image = this.images[index];
        
        if (!image || this.visibleImages.has(index)) {
            return;
        }
        
        this.visibleImages.add(index);
        
        // Check cache first
        if (this.imageCache.has(image.url)) {
            this.displayImage(imageElement, this.imageCache.get(image.url));
            return;
        }
        
        // Load image
        const img = new Image();
        img.onload = () => {
            this.imageCache.set(image.url, img);
            this.displayImage(imageElement, img);
        };
        img.onerror = () => {
            imageElement.innerHTML = '<div class="error">Failed to load</div>';
        };
        img.src = image.url;
    }
    
    unloadImage(imageElement) {
        const index = parseInt(imageElement.dataset.index);
        this.visibleImages.delete(index);
        
        // Clear image content to free memory
        imageElement.innerHTML = '<div class="placeholder">Loading...</div>';
    }
    
    displayImage(imageElement, img) {
        imageElement.innerHTML = '';
        imageElement.appendChild(img.cloneNode());
    }
    
    previousPage() {
        if (this.currentPage > 0) {
            this.currentPage--;
            this.renderPage();
        }
    }
    
    nextPage() {
        const maxPage = Math.ceil(this.images.length / this.options.imagesPerPage) - 1;
        if (this.currentPage < maxPage) {
            this.currentPage++;
            this.renderPage();
        }
    }
    
    updatePageInfo() {
        const totalPages = Math.ceil(this.images.length / this.options.imagesPerPage);
        this.pageInfo.textContent = `Page ${this.currentPage + 1} of ${totalPages}`;
    }
    
    updateButtons() {
        this.prevButton.disabled = this.currentPage === 0;
        const maxPage = Math.ceil(this.images.length / this.options.imagesPerPage) - 1;
        this.nextButton.disabled = this.currentPage >= maxPage;
    }
    
    clearCache() {
        this.imageCache.clear();
        this.visibleImages.clear();
    }
}

// Usage
const galleryContainer = document.getElementById('gallery-container');
const gallery = new ImageGallery(galleryContainer, {
    imagesPerPage: 12,
    imageWidth: 150,
    imageHeight: 150
});

// Mock images
const images = Array.from({ length: 1000 }, (_, i) => ({
    id: i,
    url: `https://picsum.photos/200/200?random=${i}`,
    title: `Image ${i}`
}));

gallery.setImages(images);
```

---

## Summary

### Key Concepts Learned
- ✅ **Performance Metrics** - Measuring and monitoring performance
- ✅ **Code Optimization** - Writing efficient JavaScript code
- ✅ **Memory Management** - Avoiding memory leaks and optimizing memory usage
- ✅ **DOM Optimization** - Efficient DOM manipulation
- ✅ **Network Optimization** - Reducing load times and improving responsiveness

### Best Practices
- Measure performance before optimizing
- Use appropriate data structures and algorithms
- Implement caching strategies
- Optimize DOM operations
- Use lazy loading for heavy resources

### Common Mistakes
- Optimizing without measuring
- Premature optimization
- Ignoring memory leaks
- Inefficient DOM manipulation
- Not implementing proper caching

---

**Fantastic! You've mastered performance optimization. Ready for Web APIs? 🚀**

**Next Tutorial:** [Web APIs](./14-web-apis.md)
