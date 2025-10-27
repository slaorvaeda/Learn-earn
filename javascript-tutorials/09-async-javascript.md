# Async JavaScript - Tutorial 9 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Callbacks](#callbacks)
3. [Promises](#promises)
4. [Async/Await](#asyncawait)
5. [Error Handling in Async Code](#error-handling-in-async-code)
6. [Promise Methods](#promise-methods)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Asynchronous JavaScript allows you to **handle operations that take time** without blocking the main thread, making applications responsive and efficient.

### What You'll Learn
- ✅ **Callbacks** - Traditional async pattern
- ✅ **Promises** - Modern async pattern
- ✅ **Async/Await** - Syntactic sugar for promises
- ✅ **Error Handling** - Managing errors in async code
- ✅ **Promise Methods** - Promise.all, Promise.race, etc.

---

## Callbacks

### Basic Callback Pattern
```javascript
// Synchronous callback
function greet(name, callback) {
    const message = `Hello, ${name}!`;
    callback(message);
}

greet("John", function(message) {
    console.log(message); // "Hello, John!"
});

// Asynchronous callback
function fetchData(callback) {
    setTimeout(() => {
        const data = { id: 1, name: "John", age: 25 };
        callback(data);
    }, 1000);
}

fetchData(function(data) {
    console.log("Data received:", data);
});
```

### Callback Hell Problem
```javascript
// Nested callbacks (callback hell)
function getUserData(userId, callback) {
    setTimeout(() => {
        const user = { id: userId, name: "John" };
        callback(null, user);
    }, 1000);
}

function getUserPosts(userId, callback) {
    setTimeout(() => {
        const posts = [{ id: 1, title: "Post 1" }, { id: 2, title: "Post 2" }];
        callback(null, posts);
    }, 1000);
}

function getPostComments(postId, callback) {
    setTimeout(() => {
        const comments = [{ id: 1, text: "Great post!" }];
        callback(null, comments);
    }, 1000);
}

// Callback hell
getUserData(1, function(err, user) {
    if (err) {
        console.log("Error:", err);
        return;
    }
    
    getUserPosts(user.id, function(err, posts) {
        if (err) {
            console.log("Error:", err);
            return;
        }
        
        getPostComments(posts[0].id, function(err, comments) {
            if (err) {
                console.log("Error:", err);
                return;
            }
            
            console.log("User:", user);
            console.log("Posts:", posts);
            console.log("Comments:", comments);
        });
    });
});
```

---

## Promises

### Creating Promises
```javascript
// Basic promise
const promise = new Promise((resolve, reject) => {
    const success = true;
    
    if (success) {
        resolve("Operation successful");
    } else {
        reject("Operation failed");
    }
});

// Using promises
promise
    .then(result => {
        console.log("Success:", result);
    })
    .catch(error => {
        console.log("Error:", error);
    });
```

### Promise with Async Operations
```javascript
function fetchUserData(userId) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (userId > 0) {
                resolve({ id: userId, name: "John", email: "john@example.com" });
            } else {
                reject(new Error("Invalid user ID"));
            }
        }, 1000);
    });
}

// Using the promise
fetchUserData(1)
    .then(user => {
        console.log("User data:", user);
        return user.name; // Return value for next .then()
    })
    .then(name => {
        console.log("User name:", name);
    })
    .catch(error => {
        console.log("Error:", error.message);
    });
```

### Chaining Promises
```javascript
function fetchUser(userId) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve({ id: userId, name: "John" });
        }, 500);
    });
}

function fetchUserPosts(userId) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve([{ id: 1, title: "Post 1" }, { id: 2, title: "Post 2" }]);
        }, 500);
    });
}

function fetchPostComments(postId) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve([{ id: 1, text: "Great post!" }]);
        }, 500);
    });
}

// Chaining promises
fetchUser(1)
    .then(user => {
        console.log("User:", user);
        return fetchUserPosts(user.id);
    })
    .then(posts => {
        console.log("Posts:", posts);
        return fetchPostComments(posts[0].id);
    })
    .then(comments => {
        console.log("Comments:", comments);
    })
    .catch(error => {
        console.log("Error:", error);
    });
```

---

## Async/Await

### Basic Async/Await
```javascript
// Convert promise to async/await
async function fetchUserData(userId) {
    try {
        const user = await fetchUser(userId);
        console.log("User:", user);
        
        const posts = await fetchUserPosts(user.id);
        console.log("Posts:", posts);
        
        const comments = await fetchPostComments(posts[0].id);
        console.log("Comments:", comments);
        
        return { user, posts, comments };
    } catch (error) {
        console.log("Error:", error);
        throw error;
    }
}

// Using async function
fetchUserData(1)
    .then(result => {
        console.log("All data fetched:", result);
    })
    .catch(error => {
        console.log("Error:", error);
    });
```

### Async Function Examples
```javascript
// Async function that returns a promise
async function calculateSum(a, b) {
    return a + b;
}

// Async function with await
async function processData() {
    const result1 = await calculateSum(5, 3);
    const result2 = await calculateSum(10, 7);
    
    return result1 + result2;
}

// Using the async function
processData()
    .then(result => {
        console.log("Total:", result); // 25
    });

// Async function with error handling
async function riskyOperation() {
    try {
        const result = await new Promise((resolve, reject) => {
            setTimeout(() => {
                const success = Math.random() > 0.5;
                if (success) {
                    resolve("Operation successful");
                } else {
                    reject(new Error("Operation failed"));
                }
            }, 1000);
        });
        
        console.log("Result:", result);
        return result;
    } catch (error) {
        console.log("Error caught:", error.message);
        throw error;
    }
}
```

### Parallel Async Operations
```javascript
// Sequential (slow)
async function sequentialFetch() {
    const user = await fetchUser(1);
    const posts = await fetchUserPosts(1);
    const comments = await fetchPostComments(1);
    
    return { user, posts, comments };
}

// Parallel (fast)
async function parallelFetch() {
    const [user, posts, comments] = await Promise.all([
        fetchUser(1),
        fetchUserPosts(1),
        fetchPostComments(1)
    ]);
    
    return { user, posts, comments };
}

// Measure performance
console.time("Sequential");
sequentialFetch().then(() => console.timeEnd("Sequential"));

console.time("Parallel");
parallelFetch().then(() => console.timeEnd("Parallel"));
```

---

## Error Handling in Async Code

### Try-Catch with Async/Await
```javascript
async function fetchDataWithErrorHandling() {
    try {
        const user = await fetchUser(1);
        console.log("User fetched:", user);
        
        const posts = await fetchUserPosts(user.id);
        console.log("Posts fetched:", posts);
        
        return { user, posts };
    } catch (error) {
        console.log("Error in fetchDataWithErrorHandling:", error.message);
        throw error; // Re-throw if needed
    }
}

// Using the function
fetchDataWithErrorHandling()
    .then(result => {
        console.log("Success:", result);
    })
    .catch(error => {
        console.log("Final error:", error.message);
    });
```

### Promise Error Handling
```javascript
function fetchDataWithPromise() {
    return fetchUser(1)
        .then(user => {
            console.log("User:", user);
            return fetchUserPosts(user.id);
        })
        .then(posts => {
            console.log("Posts:", posts);
            return { user: user, posts: posts };
        })
        .catch(error => {
            console.log("Error in promise chain:", error.message);
            throw error;
        });
}
```

### Error Handling Patterns
```javascript
// Pattern 1: Try-catch in async function
async function safeAsyncOperation() {
    try {
        const result = await riskyOperation();
        return { success: true, data: result };
    } catch (error) {
        return { success: false, error: error.message };
    }
}

// Pattern 2: Promise with error handling
function safePromiseOperation() {
    return riskyOperation()
        .then(result => ({ success: true, data: result }))
        .catch(error => ({ success: false, error: error.message }));
}

// Pattern 3: Multiple error handling
async function complexOperation() {
    try {
        const step1 = await operation1();
        console.log("Step 1 completed");
        
        try {
            const step2 = await operation2(step1);
            console.log("Step 2 completed");
            return step2;
        } catch (step2Error) {
            console.log("Step 2 failed, using fallback");
            return await fallbackOperation(step1);
        }
    } catch (error) {
        console.log("Operation failed:", error.message);
        throw error;
    }
}
```

---

## Promise Methods

### Promise.all
```javascript
// Wait for all promises to resolve
async function fetchAllData() {
    try {
        const [users, posts, comments] = await Promise.all([
            fetchUsers(),
            fetchPosts(),
            fetchComments()
        ]);
        
        return { users, posts, comments };
    } catch (error) {
        console.log("One or more operations failed:", error);
        throw error;
    }
}

// Using Promise.all with .then()
Promise.all([
    fetchUser(1),
    fetchUser(2),
    fetchUser(3)
])
.then(users => {
    console.log("All users:", users);
})
.catch(error => {
    console.log("Error:", error);
});
```

### Promise.allSettled
```javascript
// Wait for all promises to settle (resolve or reject)
async function fetchAllDataSettled() {
    const results = await Promise.allSettled([
        fetchUser(1),
        fetchUser(-1), // This will fail
        fetchUser(3)
    ]);
    
    const successful = results
        .filter(result => result.status === 'fulfilled')
        .map(result => result.value);
    
    const failed = results
        .filter(result => result.status === 'rejected')
        .map(result => result.reason);
    
    return { successful, failed };
}
```

### Promise.race
```javascript
// Return the first promise that settles
function fetchWithTimeout(url, timeout = 5000) {
    const fetchPromise = fetch(url);
    const timeoutPromise = new Promise((_, reject) => {
        setTimeout(() => reject(new Error('Request timeout')), timeout);
    });
    
    return Promise.race([fetchPromise, timeoutPromise]);
}

// Usage
fetchWithTimeout('https://api.example.com/data', 3000)
    .then(response => response.json())
    .then(data => console.log('Data:', data))
    .catch(error => console.log('Error:', error.message));
```

### Promise.any
```javascript
// Return the first promise that resolves
async function fetchFromMultipleSources() {
    const sources = [
        'https://api1.example.com/data',
        'https://api2.example.com/data',
        'https://api3.example.com/data'
    ];
    
    const promises = sources.map(url => fetch(url));
    
    try {
        const response = await Promise.any(promises);
        return await response.json();
    } catch (error) {
        console.log('All sources failed:', error);
        throw error;
    }
}
```

---

## Practice Exercises

### Exercise 1: Async User Management
```javascript
class AsyncUserManager {
    constructor() {
        this.users = [
            { id: 1, name: "John", email: "john@example.com" },
            { id: 2, name: "Jane", email: "jane@example.com" },
            { id: 3, name: "Bob", email: "bob@example.com" }
        ];
    }
    
    async getUserById(id) {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                const user = this.users.find(u => u.id === id);
                if (user) {
                    resolve(user);
                } else {
                    reject(new Error(`User with id ${id} not found`));
                }
            }, 1000);
        });
    }
    
    async getAllUsers() {
        return new Promise((resolve) => {
            setTimeout(() => {
                resolve([...this.users]);
            }, 500);
        });
    }
    
    async createUser(userData) {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                if (!userData.name || !userData.email) {
                    reject(new Error('Name and email are required'));
                    return;
                }
                
                const newUser = {
                    id: this.users.length + 1,
                    ...userData
                };
                
                this.users.push(newUser);
                resolve(newUser);
            }, 800);
        });
    }
}

// Usage
async function demonstrateUserManager() {
    const userManager = new AsyncUserManager();
    
    try {
        // Get all users
        const allUsers = await userManager.getAllUsers();
        console.log('All users:', allUsers);
        
        // Get specific user
        const user = await userManager.getUserById(1);
        console.log('User 1:', user);
        
        // Create new user
        const newUser = await userManager.createUser({
            name: 'Alice',
            email: 'alice@example.com'
        });
        console.log('Created user:', newUser);
        
        // Try to get non-existent user
        try {
            await userManager.getUserById(999);
        } catch (error) {
            console.log('Expected error:', error.message);
        }
        
    } catch (error) {
        console.log('Unexpected error:', error.message);
    }
}

demonstrateUserManager();
```

### Exercise 2: Promise-based File Processing
```javascript
function simulateFileRead(filename) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (filename.endsWith('.txt')) {
                resolve(`Content of ${filename}`);
            } else {
                reject(new Error(`Cannot read ${filename}: unsupported format`));
            }
        }, Math.random() * 1000);
    });
}

function processFile(filename) {
    return simulateFileRead(filename)
        .then(content => {
            console.log(`Processing ${filename}...`);
            return content.toUpperCase();
        })
        .then(processedContent => {
            console.log(`Processed ${filename}:`, processedContent);
            return processedContent;
        });
}

async function processMultipleFiles(filenames) {
    try {
        const results = await Promise.allSettled(
            filenames.map(filename => processFile(filename))
        );
        
        const successful = results
            .filter(result => result.status === 'fulfilled')
            .map(result => result.value);
        
        const failed = results
            .filter(result => result.status === 'rejected')
            .map(result => result.reason.message);
        
        console.log('Successful files:', successful);
        console.log('Failed files:', failed);
        
        return { successful, failed };
    } catch (error) {
        console.log('Unexpected error:', error);
        throw error;
    }
}

// Usage
processMultipleFiles(['file1.txt', 'file2.txt', 'file3.jpg', 'file4.txt']);
```

### Exercise 3: Async Data Pipeline
```javascript
class DataPipeline {
    constructor() {
        this.steps = [];
    }
    
    addStep(stepFunction) {
        this.steps.push(stepFunction);
        return this;
    }
    
    async execute(initialData) {
        let data = initialData;
        
        for (const step of this.steps) {
            try {
                data = await step(data);
                console.log('Step completed, data:', data);
            } catch (error) {
                console.log('Step failed:', error.message);
                throw error;
            }
        }
        
        return data;
    }
}

// Step functions
async function validateData(data) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (data && typeof data === 'object') {
                resolve({ ...data, validated: true });
            } else {
                reject(new Error('Invalid data format'));
            }
        }, 500);
    });
}

async function transformData(data) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve({
                ...data,
                transformed: true,
                processedAt: new Date().toISOString()
            });
        }, 300);
    });
}

async function saveData(data) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve({
                ...data,
                saved: true,
                id: Math.random().toString(36).substr(2, 9)
            });
        }, 800);
    });
}

// Usage
const pipeline = new DataPipeline()
    .addStep(validateData)
    .addStep(transformData)
    .addStep(saveData);

pipeline.execute({ name: 'John', age: 25 })
    .then(result => {
        console.log('Pipeline completed:', result);
    })
    .catch(error => {
        console.log('Pipeline failed:', error.message);
    });
```

---

## Summary

### Key Concepts Learned
- ✅ **Callbacks** - Traditional async pattern with callback hell
- ✅ **Promises** - Modern async pattern with chaining
- ✅ **Async/Await** - Syntactic sugar for promises
- ✅ **Error Handling** - Managing errors in async code
- ✅ **Promise Methods** - all, allSettled, race, any

### Best Practices
- Use async/await for cleaner code
- Handle errors properly with try-catch
- Use Promise.all for parallel operations
- Avoid callback hell by using promises
- Use Promise.allSettled when you need all results

### Common Mistakes
- Not handling errors in async operations
- Mixing callbacks and promises unnecessarily
- Not understanding promise chaining
- Forgetting to await async operations
- Creating memory leaks with unhandled promises

---

**Fantastic! You've mastered async JavaScript. Ready for Modules and Imports? 🚀**

**Next Tutorial:** [Modules and Imports](./10-modules-imports.md)
