# Web APIs - Tutorial 14 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Fetch API](#fetch-api)
3. [Local Storage](#local-storage)
4. [Geolocation API](#geolocation-api)
5. [Web Workers](#web-workers)
6. [Service Workers](#service-workers)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Web APIs provide **powerful functionality** for building modern web applications. This tutorial covers essential browser APIs that enhance user experience and application capabilities.

### What You'll Learn
- ✅ **Fetch API** - Making HTTP requests
- ✅ **Local Storage** - Storing data locally
- ✅ **Geolocation API** - Accessing user location
- ✅ **Web Workers** - Background processing
- ✅ **Service Workers** - Offline functionality and caching

---

## Fetch API

### Basic Fetch Usage
```javascript
// Basic GET request
fetch('https://jsonplaceholder.typicode.com/posts')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error('Error:', error));

// GET request with async/await
async function fetchPosts() {
    try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts');
        const data = await response.json();
        console.log(data);
        return data;
    } catch (error) {
        console.error('Error:', error);
        throw error;
    }
}

// POST request with data
async function createPost(postData) {
    try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(postData)
        });
        
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        const data = await response.json();
        console.log('Post created:', data);
        return data;
    } catch (error) {
        console.error('Error creating post:', error);
        throw error;
    }
}

// Usage
const newPost = {
    title: 'My New Post',
    body: 'This is the content of my new post',
    userId: 1
};

createPost(newPost);
```

### Advanced Fetch Features
```javascript
// Fetch with custom headers
async function fetchWithAuth(url, token) {
    const response = await fetch(url, {
        headers: {
            'Authorization': `Bearer ${token}`,
            'Content-Type': 'application/json',
            'X-Custom-Header': 'custom-value'
        }
    });
    
    if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    return response.json();
}

// Fetch with timeout
function fetchWithTimeout(url, options = {}, timeout = 5000) {
    return Promise.race([
        fetch(url, options),
        new Promise((_, reject) => 
            setTimeout(() => reject(new Error('Request timeout')), timeout)
        )
    ]);
}

// Fetch with retry logic
async function fetchWithRetry(url, options = {}, maxRetries = 3) {
    for (let i = 0; i < maxRetries; i++) {
        try {
            const response = await fetch(url, options);
            if (response.ok) {
                return response;
            }
            throw new Error(`HTTP error! status: ${response.status}`);
        } catch (error) {
            if (i === maxRetries - 1) {
                throw error;
            }
            console.log(`Retry ${i + 1} failed, retrying...`);
            await new Promise(resolve => setTimeout(resolve, 1000 * (i + 1)));
        }
    }
}

// Usage
fetchWithRetry('https://api.example.com/data')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error('All retries failed:', error));
```

### Fetch Utility Class
```javascript
class ApiClient {
    constructor(baseUrl, defaultOptions = {}) {
        this.baseUrl = baseUrl;
        this.defaultOptions = {
            headers: {
                'Content-Type': 'application/json',
                ...defaultOptions.headers
            },
            ...defaultOptions
        };
    }
    
    async request(endpoint, options = {}) {
        const url = `${this.baseUrl}${endpoint}`;
        const config = {
            ...this.defaultOptions,
            ...options,
            headers: {
                ...this.defaultOptions.headers,
                ...options.headers
            }
        };
        
        try {
            const response = await fetch(url, config);
            
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const contentType = response.headers.get('content-type');
            if (contentType && contentType.includes('application/json')) {
                return await response.json();
            }
            
            return await response.text();
        } catch (error) {
            console.error('API request failed:', error);
            throw error;
        }
    }
    
    async get(endpoint, options = {}) {
        return this.request(endpoint, { ...options, method: 'GET' });
    }
    
    async post(endpoint, data, options = {}) {
        return this.request(endpoint, {
            ...options,
            method: 'POST',
            body: JSON.stringify(data)
        });
    }
    
    async put(endpoint, data, options = {}) {
        return this.request(endpoint, {
            ...options,
            method: 'PUT',
            body: JSON.stringify(data)
        });
    }
    
    async delete(endpoint, options = {}) {
        return this.request(endpoint, { ...options, method: 'DELETE' });
    }
}

// Usage
const api = new ApiClient('https://jsonplaceholder.typicode.com');

// Get all posts
api.get('/posts')
    .then(posts => console.log('Posts:', posts))
    .catch(error => console.error('Error:', error));

// Create new post
api.post('/posts', {
    title: 'New Post',
    body: 'Post content',
    userId: 1
})
.then(post => console.log('Created post:', post))
.catch(error => console.error('Error:', error));
```

---

## Local Storage

### Basic Local Storage Operations
```javascript
// Storing data
localStorage.setItem('username', 'john_doe');
localStorage.setItem('userPreferences', JSON.stringify({
    theme: 'dark',
    language: 'en',
    notifications: true
}));

// Retrieving data
const username = localStorage.getItem('username');
const preferences = JSON.parse(localStorage.getItem('userPreferences') || '{}');

console.log('Username:', username);
console.log('Preferences:', preferences);

// Updating data
localStorage.setItem('username', 'jane_doe');
localStorage.setItem('userPreferences', JSON.stringify({
    ...preferences,
    theme: 'light'
}));

// Removing data
localStorage.removeItem('username');

// Clearing all data
localStorage.clear();

// Checking if key exists
if (localStorage.getItem('username')) {
    console.log('Username exists');
} else {
    console.log('Username does not exist');
}
```

### Local Storage Utility Class
```javascript
class StorageManager {
    constructor(storage = localStorage) {
        this.storage = storage;
    }
    
    set(key, value) {
        try {
            const serializedValue = JSON.stringify(value);
            this.storage.setItem(key, serializedValue);
            return true;
        } catch (error) {
            console.error('Error storing data:', error);
            return false;
        }
    }
    
    get(key, defaultValue = null) {
        try {
            const item = this.storage.getItem(key);
            return item ? JSON.parse(item) : defaultValue;
        } catch (error) {
            console.error('Error retrieving data:', error);
            return defaultValue;
        }
    }
    
    remove(key) {
        this.storage.removeItem(key);
    }
    
    clear() {
        this.storage.clear();
    }
    
    exists(key) {
        return this.storage.getItem(key) !== null;
    }
    
    keys() {
        return Object.keys(this.storage);
    }
    
    size() {
        return this.storage.length;
    }
    
    // Get storage usage in bytes
    getStorageSize() {
        let total = 0;
        for (let key in this.storage) {
            if (this.storage.hasOwnProperty(key)) {
                total += this.storage[key].length + key.length;
            }
        }
        return total;
    }
}

// Usage
const storage = new StorageManager();

// Store user data
storage.set('user', {
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    preferences: {
        theme: 'dark',
        language: 'en'
    }
});

// Retrieve user data
const user = storage.get('user');
console.log('User:', user);

// Update user preferences
if (user) {
    user.preferences.theme = 'light';
    storage.set('user', user);
}

// Check storage usage
console.log('Storage size:', storage.getStorageSize(), 'bytes');
```

### Local Storage with Expiration
```javascript
class ExpiringStorage {
    constructor(storage = localStorage) {
        this.storage = storage;
    }
    
    set(key, value, expirationMinutes = 60) {
        const expirationTime = Date.now() + (expirationMinutes * 60 * 1000);
        const data = {
            value,
            expiration: expirationTime
        };
        
        try {
            this.storage.setItem(key, JSON.stringify(data));
            return true;
        } catch (error) {
            console.error('Error storing data:', error);
            return false;
        }
    }
    
    get(key) {
        try {
            const item = this.storage.getItem(key);
            if (!item) return null;
            
            const data = JSON.parse(item);
            
            if (Date.now() > data.expiration) {
                this.storage.removeItem(key);
                return null;
            }
            
            return data.value;
        } catch (error) {
            console.error('Error retrieving data:', error);
            return null;
        }
    }
    
    remove(key) {
        this.storage.removeItem(key);
    }
    
    clear() {
        this.storage.clear();
    }
    
    // Clean up expired items
    cleanup() {
        const keys = Object.keys(this.storage);
        let cleanedCount = 0;
        
        keys.forEach(key => {
            const item = this.storage.getItem(key);
            if (item) {
                try {
                    const data = JSON.parse(item);
                    if (data.expiration && Date.now() > data.expiration) {
                        this.storage.removeItem(key);
                        cleanedCount++;
                    }
                } catch (error) {
                    // Remove invalid items
                    this.storage.removeItem(key);
                    cleanedCount++;
                }
            }
        });
        
        console.log(`Cleaned up ${cleanedCount} expired items`);
        return cleanedCount;
    }
}

// Usage
const expiringStorage = new ExpiringStorage();

// Store data with 5-minute expiration
expiringStorage.set('tempData', { message: 'This will expire in 5 minutes' }, 5);

// Retrieve data
const tempData = expiringStorage.get('tempData');
console.log('Temp data:', tempData);

// Clean up expired items
expiringStorage.cleanup();
```

---

## Geolocation API

### Basic Geolocation Usage
```javascript
// Get current position
function getCurrentPosition() {
    if (!navigator.geolocation) {
        console.error('Geolocation is not supported by this browser');
        return;
    }
    
    navigator.geolocation.getCurrentPosition(
        (position) => {
            console.log('Latitude:', position.coords.latitude);
            console.log('Longitude:', position.coords.longitude);
            console.log('Accuracy:', position.coords.accuracy, 'meters');
            console.log('Altitude:', position.coords.altitude);
            console.log('Speed:', position.coords.speed);
            console.log('Heading:', position.coords.heading);
        },
        (error) => {
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    console.error('User denied the request for Geolocation');
                    break;
                case error.POSITION_UNAVAILABLE:
                    console.error('Location information is unavailable');
                    break;
                case error.TIMEOUT:
                    console.error('The request to get user location timed out');
                    break;
                default:
                    console.error('An unknown error occurred');
                    break;
            }
        },
        {
            enableHighAccuracy: true,
            timeout: 10000,
            maximumAge: 60000
        }
    );
}

// Watch position changes
function watchPosition() {
    if (!navigator.geolocation) {
        console.error('Geolocation is not supported by this browser');
        return;
    }
    
    const watchId = navigator.geolocation.watchPosition(
        (position) => {
            console.log('Position updated:', {
                latitude: position.coords.latitude,
                longitude: position.coords.longitude,
                accuracy: position.coords.accuracy
            });
        },
        (error) => {
            console.error('Error watching position:', error);
        },
        {
            enableHighAccuracy: true,
            timeout: 10000,
            maximumAge: 60000
        }
    );
    
    // Stop watching after 5 minutes
    setTimeout(() => {
        navigator.geolocation.clearWatch(watchId);
        console.log('Stopped watching position');
    }, 300000);
}

// Usage
getCurrentPosition();
watchPosition();
```

### Geolocation Utility Class
```javascript
class GeolocationManager {
    constructor() {
        this.watchId = null;
        this.callbacks = {
            success: null,
            error: null
        };
    }
    
    async getCurrentPosition(options = {}) {
        if (!navigator.geolocation) {
            throw new Error('Geolocation is not supported by this browser');
        }
        
        const defaultOptions = {
            enableHighAccuracy: true,
            timeout: 10000,
            maximumAge: 60000
        };
        
        const config = { ...defaultOptions, ...options };
        
        return new Promise((resolve, reject) => {
            navigator.geolocation.getCurrentPosition(
                (position) => {
                    const location = {
                        latitude: position.coords.latitude,
                        longitude: position.coords.longitude,
                        accuracy: position.coords.accuracy,
                        altitude: position.coords.altitude,
                        speed: position.coords.speed,
                        heading: position.coords.heading,
                        timestamp: position.timestamp
                    };
                    resolve(location);
                },
                (error) => {
                    reject(this.handleGeolocationError(error));
                },
                config
            );
        });
    }
    
    watchPosition(callback, errorCallback, options = {}) {
        if (!navigator.geolocation) {
            throw new Error('Geolocation is not supported by this browser');
        }
        
        const defaultOptions = {
            enableHighAccuracy: true,
            timeout: 10000,
            maximumAge: 60000
        };
        
        const config = { ...defaultOptions, ...options };
        
        this.callbacks.success = callback;
        this.callbacks.error = errorCallback;
        
        this.watchId = navigator.geolocation.watchPosition(
            (position) => {
                const location = {
                    latitude: position.coords.latitude,
                    longitude: position.coords.longitude,
                    accuracy: position.coords.accuracy,
                    altitude: position.coords.altitude,
                    speed: position.coords.speed,
                    heading: position.coords.heading,
                    timestamp: position.timestamp
                };
                callback(location);
            },
            (error) => {
                errorCallback(this.handleGeolocationError(error));
            },
            config
        );
        
        return this.watchId;
    }
    
    stopWatching() {
        if (this.watchId) {
            navigator.geolocation.clearWatch(this.watchId);
            this.watchId = null;
        }
    }
    
    handleGeolocationError(error) {
        switch (error.code) {
            case error.PERMISSION_DENIED:
                return new Error('User denied the request for Geolocation');
            case error.POSITION_UNAVAILABLE:
                return new Error('Location information is unavailable');
            case error.TIMEOUT:
                return new Error('The request to get user location timed out');
            default:
                return new Error('An unknown error occurred');
        }
    }
    
    // Calculate distance between two coordinates
    calculateDistance(lat1, lon1, lat2, lon2) {
        const R = 6371; // Earth's radius in kilometers
        const dLat = this.toRadians(lat2 - lat1);
        const dLon = this.toRadians(lon2 - lon1);
        
        const a = Math.sin(dLat / 2) * Math.sin(dLat / 2) +
                  Math.cos(this.toRadians(lat1)) * Math.cos(this.toRadians(lat2)) *
                  Math.sin(dLon / 2) * Math.sin(dLon / 2);
        
        const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
        const distance = R * c;
        
        return distance;
    }
    
    toRadians(degrees) {
        return degrees * (Math.PI / 180);
    }
}

// Usage
const geoManager = new GeolocationManager();

// Get current position
geoManager.getCurrentPosition()
    .then(location => {
        console.log('Current location:', location);
    })
    .catch(error => {
        console.error('Error getting location:', error);
    });

// Watch position changes
geoManager.watchPosition(
    (location) => {
        console.log('Position updated:', location);
    },
    (error) => {
        console.error('Error watching position:', error);
    }
);

// Stop watching after 5 minutes
setTimeout(() => {
    geoManager.stopWatching();
}, 300000);
```

---

## Web Workers

### Basic Web Worker
```javascript
// worker.js
self.onmessage = function(e) {
    const { data, type } = e.data;
    
    switch (type) {
        case 'CALCULATE':
            const result = performCalculation(data);
            self.postMessage({ type: 'RESULT', data: result });
            break;
        case 'PROCESS_ARRAY':
            const processedArray = processArray(data);
            self.postMessage({ type: 'ARRAY_PROCESSED', data: processedArray });
            break;
        default:
            self.postMessage({ type: 'ERROR', data: 'Unknown message type' });
    }
};

function performCalculation(data) {
    // Simulate heavy calculation
    let result = 0;
    for (let i = 0; i < data.iterations; i++) {
        result += Math.sqrt(i) * Math.sin(i);
    }
    return result;
}

function processArray(data) {
    return data.map(item => item * 2).filter(item => item > 10);
}

// main.js
class WorkerManager {
    constructor() {
        this.workers = new Map();
    }
    
    createWorker(workerScript) {
        const worker = new Worker(workerScript);
        this.workers.set(workerScript, worker);
        return worker;
    }
    
    sendMessage(workerScript, message) {
        const worker = this.workers.get(workerScript);
        if (worker) {
            worker.postMessage(message);
        }
    }
    
    terminateWorker(workerScript) {
        const worker = this.workers.get(workerScript);
        if (worker) {
            worker.terminate();
            this.workers.delete(workerScript);
        }
    }
    
    terminateAllWorkers() {
        for (const [script, worker] of this.workers) {
            worker.terminate();
        }
        this.workers.clear();
    }
}

// Usage
const workerManager = new WorkerManager();
const worker = workerManager.createWorker('worker.js');

// Send message to worker
worker.postMessage({
    type: 'CALCULATE',
    data: { iterations: 1000000 }
});

// Listen for messages from worker
worker.onmessage = function(e) {
    const { type, data } = e.data;
    
    switch (type) {
        case 'RESULT':
            console.log('Calculation result:', data);
            break;
        case 'ARRAY_PROCESSED':
            console.log('Processed array:', data);
            break;
        case 'ERROR':
            console.error('Worker error:', data);
            break;
    }
};

// Handle worker errors
worker.onerror = function(error) {
    console.error('Worker error:', error);
};

// Terminate worker when done
setTimeout(() => {
    workerManager.terminateWorker('worker.js');
}, 10000);
```

### Web Worker Pool
```javascript
class WorkerPool {
    constructor(workerScript, poolSize = navigator.hardwareConcurrency || 4) {
        this.workerScript = workerScript;
        this.poolSize = poolSize;
        this.workers = [];
        this.queue = [];
        this.busyWorkers = new Set();
        
        this.initializeWorkers();
    }
    
    initializeWorkers() {
        for (let i = 0; i < this.poolSize; i++) {
            const worker = new Worker(this.workerScript);
            worker.id = i;
            worker.onmessage = (e) => this.handleWorkerMessage(worker, e);
            worker.onerror = (e) => this.handleWorkerError(worker, e);
            this.workers.push(worker);
        }
    }
    
    handleWorkerMessage(worker, e) {
        const { type, data, taskId } = e.data;
        
        if (type === 'TASK_COMPLETE') {
            this.busyWorkers.delete(worker);
            this.processQueue();
            
            // Resolve the promise for this task
            const task = this.queue.find(t => t.id === taskId);
            if (task) {
                task.resolve(data);
                this.queue = this.queue.filter(t => t.id !== taskId);
            }
        }
    }
    
    handleWorkerError(worker, error) {
        console.error('Worker error:', error);
        this.busyWorkers.delete(worker);
        this.processQueue();
    }
    
    processQueue() {
        if (this.queue.length === 0) return;
        
        const availableWorker = this.workers.find(w => !this.busyWorkers.has(w));
        if (!availableWorker) return;
        
        const task = this.queue.shift();
        this.busyWorkers.add(availableWorker);
        
        availableWorker.postMessage({
            ...task.data,
            taskId: task.id
        });
    }
    
    async executeTask(data) {
        return new Promise((resolve, reject) => {
            const task = {
                id: Date.now() + Math.random(),
                data,
                resolve,
                reject
            };
            
            this.queue.push(task);
            this.processQueue();
        });
    }
    
    terminate() {
        this.workers.forEach(worker => worker.terminate());
        this.workers = [];
        this.queue = [];
        this.busyWorkers.clear();
    }
}

// Usage
const workerPool = new WorkerPool('worker.js', 4);

// Execute multiple tasks
const tasks = [
    { type: 'CALCULATE', data: { iterations: 1000000 } },
    { type: 'CALCULATE', data: { iterations: 2000000 } },
    { type: 'CALCULATE', data: { iterations: 1500000 } },
    { type: 'CALCULATE', data: { iterations: 3000000 } }
];

Promise.all(tasks.map(task => workerPool.executeTask(task)))
    .then(results => {
        console.log('All tasks completed:', results);
    })
    .catch(error => {
        console.error('Task execution failed:', error);
    });
```

---

## Service Workers

### Basic Service Worker
```javascript
// sw.js
const CACHE_NAME = 'my-app-cache-v1';
const urlsToCache = [
    '/',
    '/index.html',
    '/styles.css',
    '/script.js',
    '/manifest.json'
];

// Install event
self.addEventListener('install', (event) => {
    event.waitUntil(
        caches.open(CACHE_NAME)
            .then((cache) => {
                console.log('Opened cache');
                return cache.addAll(urlsToCache);
            })
    );
});

// Fetch event
self.addEventListener('fetch', (event) => {
    event.respondWith(
        caches.match(event.request)
            .then((response) => {
                // Return cached version or fetch from network
                return response || fetch(event.request);
            })
    );
});

// Activate event
self.addEventListener('activate', (event) => {
    event.waitUntil(
        caches.keys().then((cacheNames) => {
            return Promise.all(
                cacheNames.map((cacheName) => {
                    if (cacheName !== CACHE_NAME) {
                        console.log('Deleting old cache:', cacheName);
                        return caches.delete(cacheName);
                    }
                })
            );
        })
    );
});

// main.js
class ServiceWorkerManager {
    constructor() {
        this.registration = null;
    }
    
    async register(swPath = '/sw.js') {
        if (!('serviceWorker' in navigator)) {
            console.log('Service Worker not supported');
            return false;
        }
        
        try {
            this.registration = await navigator.serviceWorker.register(swPath);
            console.log('Service Worker registered:', this.registration);
            return true;
        } catch (error) {
            console.error('Service Worker registration failed:', error);
            return false;
        }
    }
    
    async unregister() {
        if (this.registration) {
            const success = await this.registration.unregister();
            if (success) {
                console.log('Service Worker unregistered');
                this.registration = null;
            }
            return success;
        }
        return false;
    }
    
    async update() {
        if (this.registration) {
            await this.registration.update();
            console.log('Service Worker updated');
        }
    }
    
    // Send message to service worker
    async sendMessage(message) {
        if (this.registration && this.registration.active) {
            this.registration.active.postMessage(message);
        }
    }
    
    // Listen for messages from service worker
    onMessage(callback) {
        navigator.serviceWorker.addEventListener('message', callback);
    }
}

// Usage
const swManager = new ServiceWorkerManager();

// Register service worker
swManager.register('/sw.js')
    .then(success => {
        if (success) {
            console.log('Service Worker registered successfully');
        }
    });

// Listen for messages from service worker
swManager.onMessage((event) => {
    console.log('Message from Service Worker:', event.data);
});

// Send message to service worker
swManager.sendMessage({ type: 'CACHE_UPDATE', data: 'new-data' });
```

### Advanced Service Worker with Caching Strategies
```javascript
// sw.js
const CACHE_NAME = 'my-app-cache-v2';
const STATIC_CACHE = 'static-cache-v1';
const DYNAMIC_CACHE = 'dynamic-cache-v1';

// Install event
self.addEventListener('install', (event) => {
    console.log('Service Worker installing');
    event.waitUntil(
        caches.open(STATIC_CACHE)
            .then((cache) => {
                return cache.addAll([
                    '/',
                    '/index.html',
                    '/styles.css',
                    '/script.js',
                    '/manifest.json'
                ]);
            })
    );
});

// Activate event
self.addEventListener('activate', (event) => {
    console.log('Service Worker activating');
    event.waitUntil(
        caches.keys().then((cacheNames) => {
            return Promise.all(
                cacheNames.map((cacheName) => {
                    if (cacheName !== STATIC_CACHE && cacheName !== DYNAMIC_CACHE) {
                        console.log('Deleting old cache:', cacheName);
                        return caches.delete(cacheName);
                    }
                })
            );
        })
    );
});

// Fetch event with different caching strategies
self.addEventListener('fetch', (event) => {
    const { request } = event;
    const url = new URL(request.url);
    
    // Handle different types of requests
    if (request.method === 'GET') {
        if (url.pathname.startsWith('/api/')) {
            // API requests - Network First
            event.respondWith(networkFirst(request));
        } else if (url.pathname.endsWith('.html')) {
            // HTML pages - Cache First
            event.respondWith(cacheFirst(request, STATIC_CACHE));
        } else if (url.pathname.match(/\.(css|js|png|jpg|jpeg|gif|svg)$/)) {
            // Static assets - Cache First
            event.respondWith(cacheFirst(request, STATIC_CACHE));
        } else {
            // Other requests - Stale While Revalidate
            event.respondWith(staleWhileRevalidate(request));
        }
    }
});

// Cache First strategy
async function cacheFirst(request, cacheName) {
    try {
        const cachedResponse = await caches.match(request);
        if (cachedResponse) {
            return cachedResponse;
        }
        
        const networkResponse = await fetch(request);
        if (networkResponse.ok) {
            const cache = await caches.open(cacheName);
            cache.put(request, networkResponse.clone());
        }
        
        return networkResponse;
    } catch (error) {
        console.error('Cache First strategy failed:', error);
        return new Response('Offline content not available', { status: 503 });
    }
}

// Network First strategy
async function networkFirst(request) {
    try {
        const networkResponse = await fetch(request);
        if (networkResponse.ok) {
            const cache = await caches.open(DYNAMIC_CACHE);
            cache.put(request, networkResponse.clone());
        }
        return networkResponse;
    } catch (error) {
        console.log('Network failed, trying cache:', error);
        const cachedResponse = await caches.match(request);
        if (cachedResponse) {
            return cachedResponse;
        }
        return new Response('Offline content not available', { status: 503 });
    }
}

// Stale While Revalidate strategy
async function staleWhileRevalidate(request) {
    const cache = await caches.open(DYNAMIC_CACHE);
    const cachedResponse = await cache.match(request);
    
    const fetchPromise = fetch(request).then((networkResponse) => {
        if (networkResponse.ok) {
            cache.put(request, networkResponse.clone());
        }
        return networkResponse;
    });
    
    return cachedResponse || fetchPromise;
}

// Background sync
self.addEventListener('sync', (event) => {
    if (event.tag === 'background-sync') {
        event.waitUntil(doBackgroundSync());
    }
});

async function doBackgroundSync() {
    console.log('Performing background sync');
    // Perform background tasks here
}

// Push notifications
self.addEventListener('push', (event) => {
    if (event.data) {
        const data = event.data.json();
        const options = {
            body: data.body,
            icon: '/icon-192x192.png',
            badge: '/badge-72x72.png',
            vibrate: [100, 50, 100],
            data: data.data,
            actions: [
                {
                    action: 'explore',
                    title: 'Go to the site',
                    icon: '/checkmark.png'
                },
                {
                    action: 'close',
                    title: 'Close notification',
                    icon: '/xmark.png'
                }
            ]
        };
        
        event.waitUntil(
            self.registration.showNotification(data.title, options)
        );
    }
});

// Notification click
self.addEventListener('notificationclick', (event) => {
    event.notification.close();
    
    if (event.action === 'explore') {
        event.waitUntil(
            clients.openWindow('/')
        );
    }
});
```

---

## Practice Exercises

### Exercise 1: Weather App with APIs
```javascript
class WeatherApp {
    constructor() {
        this.apiKey = 'your-api-key'; // Replace with actual API key
        this.baseUrl = 'https://api.openweathermap.org/data/2.5';
        this.storage = new StorageManager();
        this.geoManager = new GeolocationManager();
    }
    
    async getWeatherByCity(city) {
        try {
            const response = await fetch(
                `${this.baseUrl}/weather?q=${city}&appid=${this.apiKey}&units=metric`
            );
            
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const data = await response.json();
            this.storage.set('lastWeather', data);
            return data;
        } catch (error) {
            console.error('Error fetching weather:', error);
            throw error;
        }
    }
    
    async getWeatherByLocation() {
        try {
            const location = await this.geoManager.getCurrentPosition();
            const response = await fetch(
                `${this.baseUrl}/weather?lat=${location.latitude}&lon=${location.longitude}&appid=${this.apiKey}&units=metric`
            );
            
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const data = await response.json();
            this.storage.set('lastWeather', data);
            return data;
        } catch (error) {
            console.error('Error fetching weather by location:', error);
            throw error;
        }
    }
    
    async getForecast(city) {
        try {
            const response = await fetch(
                `${this.baseUrl}/forecast?q=${city}&appid=${this.apiKey}&units=metric`
            );
            
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const data = await response.json();
            return data;
        } catch (error) {
            console.error('Error fetching forecast:', error);
            throw error;
        }
    }
    
    displayWeather(weatherData) {
        const container = document.getElementById('weather-container');
        container.innerHTML = `
            <div class="weather-card">
                <h2>${weatherData.name}</h2>
                <div class="weather-main">
                    <img src="https://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png" alt="${weatherData.weather[0].description}">
                    <div class="weather-info">
                        <div class="temperature">${Math.round(weatherData.main.temp)}°C</div>
                        <div class="description">${weatherData.weather[0].description}</div>
                    </div>
                </div>
                <div class="weather-details">
                    <div class="detail">
                        <span>Feels like:</span>
                        <span>${Math.round(weatherData.main.feels_like)}°C</span>
                    </div>
                    <div class="detail">
                        <span>Humidity:</span>
                        <span>${weatherData.main.humidity}%</span>
                    </div>
                    <div class="detail">
                        <span>Wind:</span>
                        <span>${weatherData.wind.speed} m/s</span>
                    </div>
                </div>
            </div>
        `;
    }
    
    async init() {
        // Try to get weather by location first
        try {
            const weather = await this.getWeatherByLocation();
            this.displayWeather(weather);
        } catch (error) {
            console.log('Location-based weather failed, trying default city');
            try {
                const weather = await this.getWeatherByCity('London');
                this.displayWeather(weather);
            } catch (error) {
                console.error('Failed to load weather data');
            }
        }
    }
}

// Usage
const weatherApp = new WeatherApp();
weatherApp.init();
```

### Exercise 2: Offline-First Note App
```javascript
class NoteApp {
    constructor() {
        this.storage = new StorageManager();
        this.notes = this.storage.get('notes', []);
        this.isOnline = navigator.onLine;
        this.syncQueue = this.storage.get('syncQueue', []);
        
        this.init();
    }
    
    init() {
        this.setupEventListeners();
        this.renderNotes();
        this.setupOfflineHandling();
    }
    
    setupEventListeners() {
        document.getElementById('add-note-btn').addEventListener('click', () => {
            this.addNote();
        });
        
        document.getElementById('sync-btn').addEventListener('click', () => {
            this.syncNotes();
        });
    }
    
    setupOfflineHandling() {
        window.addEventListener('online', () => {
            this.isOnline = true;
            this.syncNotes();
        });
        
        window.addEventListener('offline', () => {
            this.isOnline = false;
        });
    }
    
    addNote() {
        const title = prompt('Enter note title:');
        const content = prompt('Enter note content:');
        
        if (title && content) {
            const note = {
                id: Date.now().toString(),
                title,
                content,
                createdAt: new Date().toISOString(),
                updatedAt: new Date().toISOString(),
                synced: false
            };
            
            this.notes.push(note);
            this.saveNotes();
            this.renderNotes();
            
            if (this.isOnline) {
                this.syncNote(note);
            } else {
                this.addToSyncQueue(note);
            }
        }
    }
    
    editNote(noteId) {
        const note = this.notes.find(n => n.id === noteId);
        if (note) {
            const newTitle = prompt('Enter new title:', note.title);
            const newContent = prompt('Enter new content:', note.content);
            
            if (newTitle && newContent) {
                note.title = newTitle;
                note.content = newContent;
                note.updatedAt = new Date().toISOString();
                note.synced = false;
                
                this.saveNotes();
                this.renderNotes();
                
                if (this.isOnline) {
                    this.syncNote(note);
                } else {
                    this.addToSyncQueue(note);
                }
            }
        }
    }
    
    deleteNote(noteId) {
        if (confirm('Are you sure you want to delete this note?')) {
            const note = this.notes.find(n => n.id === noteId);
            if (note) {
                this.notes = this.notes.filter(n => n.id !== noteId);
                this.saveNotes();
                this.renderNotes();
                
                if (this.isOnline) {
                    this.deleteNoteFromServer(noteId);
                } else {
                    this.addToSyncQueue({ id: noteId, action: 'delete' });
                }
            }
        }
    }
    
    renderNotes() {
        const container = document.getElementById('notes-container');
        container.innerHTML = '';
        
        this.notes.forEach(note => {
            const noteElement = document.createElement('div');
            noteElement.className = 'note';
            noteElement.innerHTML = `
                <h3>${note.title}</h3>
                <p>${note.content}</p>
                <div class="note-actions">
                    <button onclick="noteApp.editNote('${note.id}')">Edit</button>
                    <button onclick="noteApp.deleteNote('${note.id}')">Delete</button>
                    ${!note.synced ? '<span class="sync-status">Not synced</span>' : ''}
                </div>
            `;
            container.appendChild(noteElement);
        });
    }
    
    saveNotes() {
        this.storage.set('notes', this.notes);
    }
    
    addToSyncQueue(note) {
        this.syncQueue.push(note);
        this.storage.set('syncQueue', this.syncQueue);
    }
    
    async syncNotes() {
        if (!this.isOnline) {
            console.log('Cannot sync: offline');
            return;
        }
        
        for (const note of this.syncQueue) {
            try {
                if (note.action === 'delete') {
                    await this.deleteNoteFromServer(note.id);
                } else {
                    await this.syncNote(note);
                }
                
                // Remove from sync queue
                this.syncQueue = this.syncQueue.filter(n => n.id !== note.id);
            } catch (error) {
                console.error('Sync failed for note:', note.id, error);
            }
        }
        
        this.storage.set('syncQueue', this.syncQueue);
    }
    
    async syncNote(note) {
        // Simulate API call
        await new Promise(resolve => setTimeout(resolve, 1000));
        
        // Mark as synced
        note.synced = true;
        this.saveNotes();
        this.renderNotes();
    }
    
    async deleteNoteFromServer(noteId) {
        // Simulate API call
        await new Promise(resolve => setTimeout(resolve, 500));
        console.log('Note deleted from server:', noteId);
    }
}

// Usage
const noteApp = new NoteApp();
```

---

## Summary

### Key Concepts Learned
- ✅ **Fetch API** - Making HTTP requests and handling responses
- ✅ **Local Storage** - Storing data locally with expiration
- ✅ **Geolocation API** - Accessing user location and position tracking
- ✅ **Web Workers** - Background processing and worker pools
- ✅ **Service Workers** - Offline functionality and caching strategies

### Best Practices
- Use appropriate caching strategies for different content types
- Handle offline scenarios gracefully
- Implement proper error handling for API calls
- Use Web Workers for CPU-intensive tasks
- Implement proper cleanup for workers and event listeners

### Common Mistakes
- Not handling offline scenarios
- Not implementing proper error handling
- Memory leaks from not cleaning up workers
- Not using appropriate caching strategies
- Ignoring security considerations for stored data

---

**Excellent! You've mastered Web APIs. Ready for Modern JavaScript Features? 🚀**

**Next Tutorial:** [Modern JavaScript Features](./15-modern-features.md)
