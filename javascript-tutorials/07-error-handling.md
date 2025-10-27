# Error Handling - Tutorial 7 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Types of Errors](#types-of-errors)
3. [Try-Catch Blocks](#try-catch-blocks)
4. [Throwing Errors](#throwing-errors)
5. [Error Objects](#error-objects)
6. [Async Error Handling](#async-error-handling)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Error handling is **essential for robust applications**. It allows you to gracefully handle unexpected situations and provide meaningful feedback to users.

### What You'll Learn
- ✅ **Error Types** - Syntax, runtime, and logical errors
- ✅ **Try-Catch Blocks** - Catching and handling errors
- ✅ **Throwing Errors** - Creating custom error conditions
- ✅ **Error Objects** - Built-in and custom error types
- ✅ **Async Error Handling** - Handling errors in asynchronous code

---

## Types of Errors

### Syntax Errors
```javascript
// Syntax errors occur during parsing
// These will prevent the script from running

// Missing closing bracket
// let arr = [1, 2, 3;

// Missing semicolon in object
// let obj = { name: "John" age: 25 }

// Incorrect function syntax
// function myFunction( {
//     return "Hello";
// }
```

### Runtime Errors
```javascript
// Runtime errors occur during execution

// ReferenceError - variable not defined
try {
    console.log(undefinedVariable);
} catch (error) {
    console.log('ReferenceError:', error.message);
}

// TypeError - wrong type of operation
try {
    let num = 42;
    num.toUpperCase(); // TypeError: num.toUpperCase is not a function
} catch (error) {
    console.log('TypeError:', error.message);
}

// RangeError - number out of range
try {
    let arr = new Array(-1); // RangeError: Invalid array length
} catch (error) {
    console.log('RangeError:', error.message);
}
```

### Logical Errors
```javascript
// Logical errors don't throw exceptions but produce wrong results

function divide(a, b) {
    // Logical error: doesn't handle division by zero
    return a / b; // Returns Infinity instead of handling the error
}

// Corrected version
function divideSafe(a, b) {
    if (b === 0) {
        throw new Error('Division by zero is not allowed');
    }
    return a / b;
}
```

---

## Try-Catch Blocks

### Basic Try-Catch
```javascript
try {
    // Code that might throw an error
    let result = riskyOperation();
    console.log('Success:', result);
} catch (error) {
    // Handle the error
    console.log('Error occurred:', error.message);
}
```

### Try-Catch-Finally
```javascript
try {
    // Code that might throw an error
    let data = JSON.parse(invalidJson);
    console.log('Data parsed successfully');
} catch (error) {
    // Handle the error
    console.log('JSON parsing failed:', error.message);
} finally {
    // This always runs, regardless of success or failure
    console.log('Cleanup operations');
}
```

### Nested Try-Catch
```javascript
try {
    try {
        // Inner try block
        let result = divide(10, 0);
        console.log('Division result:', result);
    } catch (innerError) {
        console.log('Inner error:', innerError.message);
        throw new Error('Division failed: ' + innerError.message);
    }
} catch (outerError) {
    console.log('Outer error:', outerError.message);
}
```

### Multiple Catch Blocks
```javascript
try {
    // Code that might throw different types of errors
    let data = JSON.parse(userInput);
    let result = data.value / data.divisor;
    console.log('Result:', result);
} catch (error) {
    if (error instanceof SyntaxError) {
        console.log('Invalid JSON format');
    } else if (error instanceof TypeError) {
        console.log('Invalid data type');
    } else if (error.message.includes('Division by zero')) {
        console.log('Cannot divide by zero');
    } else {
        console.log('Unexpected error:', error.message);
    }
}
```

---

## Throwing Errors

### Throwing Built-in Errors
```javascript
function validateAge(age) {
    if (typeof age !== 'number') {
        throw new TypeError('Age must be a number');
    }
    if (age < 0) {
        throw new RangeError('Age cannot be negative');
    }
    if (age > 150) {
        throw new RangeError('Age cannot be greater than 150');
    }
    return true;
}

// Usage
try {
    validateAge(-5);
} catch (error) {
    console.log('Validation failed:', error.message);
}
```

### Throwing Custom Errors
```javascript
function withdrawMoney(balance, amount) {
    if (amount <= 0) {
        throw new Error('Withdrawal amount must be positive');
    }
    if (amount > balance) {
        throw new Error('Insufficient funds');
    }
    return balance - amount;
}

// Usage
try {
    let newBalance = withdrawMoney(100, 150);
    console.log('New balance:', newBalance);
} catch (error) {
    console.log('Withdrawal failed:', error.message);
}
```

### Throwing with Error Details
```javascript
function processUser(user) {
    if (!user) {
        throw new Error('User object is required');
    }
    if (!user.name) {
        throw new Error('User name is required');
    }
    if (!user.email) {
        throw new Error('User email is required');
    }
    
    // Process user
    return {
        id: Date.now(),
        name: user.name,
        email: user.email,
        processedAt: new Date()
    };
}

// Usage
try {
    let user = { name: 'John' }; // Missing email
    let processedUser = processUser(user);
    console.log('User processed:', processedUser);
} catch (error) {
    console.log('Processing failed:', error.message);
}
```

---

## Error Objects

### Built-in Error Types
```javascript
// Error - base error type
try {
    throw new Error('Something went wrong');
} catch (error) {
    console.log('Error:', error.message);
    console.log('Stack:', error.stack);
}

// ReferenceError
try {
    throw new ReferenceError('Variable not defined');
} catch (error) {
    console.log('ReferenceError:', error.message);
}

// TypeError
try {
    throw new TypeError('Invalid type');
} catch (error) {
    console.log('TypeError:', error.message);
}

// RangeError
try {
    throw new RangeError('Value out of range');
} catch (error) {
    console.log('RangeError:', error.message);
}

// SyntaxError
try {
    throw new SyntaxError('Invalid syntax');
} catch (error) {
    console.log('SyntaxError:', error.message);
}
```

### Custom Error Classes
```javascript
// Custom error class
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

// Usage
function validateEmail(email) {
    if (!email) {
        throw new ValidationError('Email is required', 'email');
    }
    if (!email.includes('@')) {
        throw new ValidationError('Invalid email format', 'email');
    }
    return true;
}

function fetchData(url) {
    // Simulate network error
    throw new NetworkError('Failed to fetch data', 500);
}

// Error handling
try {
    validateEmail('invalid-email');
} catch (error) {
    if (error instanceof ValidationError) {
        console.log(`Validation error in ${error.field}: ${error.message}`);
    } else {
        console.log('Unexpected error:', error.message);
    }
}
```

### Error Properties and Methods
```javascript
try {
    throw new Error('Test error');
} catch (error) {
    console.log('Error name:', error.name);
    console.log('Error message:', error.message);
    console.log('Error stack:', error.stack);
    
    // Check error type
    console.log('Is Error:', error instanceof Error);
    console.log('Is TypeError:', error instanceof TypeError);
    
    // Convert to string
    console.log('String representation:', error.toString());
}
```

---

## Async Error Handling

### Promises and Error Handling
```javascript
// Promise with error handling
function fetchUserData(userId) {
    return new Promise((resolve, reject) => {
        if (!userId) {
            reject(new Error('User ID is required'));
            return;
        }
        
        // Simulate API call
        setTimeout(() => {
            if (userId === 'invalid') {
                reject(new Error('User not found'));
            } else {
                resolve({ id: userId, name: 'John Doe' });
            }
        }, 1000);
    });
}

// Using .then() and .catch()
fetchUserData('123')
    .then(user => {
        console.log('User data:', user);
    })
    .catch(error => {
        console.log('Error:', error.message);
    });

// Using async/await
async function getUserData(userId) {
    try {
        let user = await fetchUserData(userId);
        console.log('User data:', user);
        return user;
    } catch (error) {
        console.log('Error:', error.message);
        throw error; // Re-throw if needed
    }
}
```

### Async Function Error Handling
```javascript
async function processMultipleUsers(userIds) {
    let results = [];
    let errors = [];
    
    for (let userId of userIds) {
        try {
            let user = await fetchUserData(userId);
            results.push(user);
        } catch (error) {
            errors.push({ userId, error: error.message });
        }
    }
    
    return { results, errors };
}

// Usage
processMultipleUsers(['123', 'invalid', '456'])
    .then(({ results, errors }) => {
        console.log('Successful results:', results);
        console.log('Errors:', errors);
    });
```

### Promise.all Error Handling
```javascript
async function fetchAllUsers(userIds) {
    try {
        let promises = userIds.map(id => fetchUserData(id));
        let users = await Promise.all(promises);
        return users;
    } catch (error) {
        console.log('One or more requests failed:', error.message);
        return [];
    }
}

// Alternative: Promise.allSettled
async function fetchAllUsersSettled(userIds) {
    let promises = userIds.map(id => fetchUserData(id));
    let results = await Promise.allSettled(promises);
    
    let successful = results
        .filter(result => result.status === 'fulfilled')
        .map(result => result.value);
    
    let failed = results
        .filter(result => result.status === 'rejected')
        .map(result => result.reason);
    
    return { successful, failed };
}
```

---

## Practice Exercises

### Exercise 1: Calculator with Error Handling
```javascript
class Calculator {
    add(a, b) {
        if (typeof a !== 'number' || typeof b !== 'number') {
            throw new TypeError('Both arguments must be numbers');
        }
        return a + b;
    }
    
    subtract(a, b) {
        if (typeof a !== 'number' || typeof b !== 'number') {
            throw new TypeError('Both arguments must be numbers');
        }
        return a - b;
    }
    
    multiply(a, b) {
        if (typeof a !== 'number' || typeof b !== 'number') {
            throw new TypeError('Both arguments must be numbers');
        }
        return a * b;
    }
    
    divide(a, b) {
        if (typeof a !== 'number' || typeof b !== 'number') {
            throw new TypeError('Both arguments must be numbers');
        }
        if (b === 0) {
            throw new Error('Division by zero is not allowed');
        }
        return a / b;
    }
}

let calc = new Calculator();

try {
    console.log(calc.add(5, 3));        // 8
    console.log(calc.divide(10, 2));     // 5
    console.log(calc.divide(10, 0));     // Error
} catch (error) {
    console.log('Calculator error:', error.message);
}
```

### Exercise 2: Form Validation with Error Handling
```javascript
class FormValidator {
    validateEmail(email) {
        if (!email) {
            throw new ValidationError('Email is required', 'email');
        }
        if (typeof email !== 'string') {
            throw new ValidationError('Email must be a string', 'email');
        }
        if (!email.includes('@')) {
            throw new ValidationError('Invalid email format', 'email');
        }
        return true;
    }
    
    validatePassword(password) {
        if (!password) {
            throw new ValidationError('Password is required', 'password');
        }
        if (password.length < 8) {
            throw new ValidationError('Password must be at least 8 characters', 'password');
        }
        if (!/[A-Z]/.test(password)) {
            throw new ValidationError('Password must contain at least one uppercase letter', 'password');
        }
        if (!/[0-9]/.test(password)) {
            throw new ValidationError('Password must contain at least one number', 'password');
        }
        return true;
    }
    
    validateForm(formData) {
        let errors = [];
        
        try {
            this.validateEmail(formData.email);
        } catch (error) {
            errors.push(error);
        }
        
        try {
            this.validatePassword(formData.password);
        } catch (error) {
            errors.push(error);
        }
        
        if (errors.length > 0) {
            throw new FormValidationError('Form validation failed', errors);
        }
        
        return true;
    }
}

class ValidationError extends Error {
    constructor(message, field) {
        super(message);
        this.name = 'ValidationError';
        this.field = field;
    }
}

class FormValidationError extends Error {
    constructor(message, errors) {
        super(message);
        this.name = 'FormValidationError';
        this.errors = errors;
    }
}

// Usage
let validator = new FormValidator();

try {
    validator.validateForm({
        email: 'invalid-email',
        password: 'weak'
    });
} catch (error) {
    if (error instanceof FormValidationError) {
        console.log('Form validation failed:');
        error.errors.forEach(err => {
            console.log(`- ${err.field}: ${err.message}`);
        });
    }
}
```

### Exercise 3: API Error Handling
```javascript
class ApiClient {
    async fetchData(url) {
        try {
            let response = await fetch(url);
            
            if (!response.ok) {
                throw new NetworkError(`HTTP ${response.status}: ${response.statusText}`, response.status);
            }
            
            let data = await response.json();
            return data;
        } catch (error) {
            if (error instanceof NetworkError) {
                throw error;
            } else if (error.name === 'TypeError' && error.message.includes('fetch')) {
                throw new NetworkError('Network connection failed', 0);
            } else {
                throw new Error(`Unexpected error: ${error.message}`);
            }
        }
    }
}

class NetworkError extends Error {
    constructor(message, statusCode) {
        super(message);
        this.name = 'NetworkError';
        this.statusCode = statusCode;
    }
}

// Usage
let apiClient = new ApiClient();

async function loadUserData(userId) {
    try {
        let userData = await apiClient.fetchData(`/api/users/${userId}`);
        console.log('User data loaded:', userData);
        return userData;
    } catch (error) {
        if (error instanceof NetworkError) {
            console.log('Network error:', error.message);
            if (error.statusCode === 404) {
                console.log('User not found');
            } else if (error.statusCode >= 500) {
                console.log('Server error, please try again later');
            }
        } else {
            console.log('Unexpected error:', error.message);
        }
        throw error;
    }
}
```

---

## Summary

### Key Concepts Learned
- ✅ **Error Types** - Syntax, runtime, and logical errors
- ✅ **Try-Catch Blocks** - Catching and handling errors gracefully
- ✅ **Throwing Errors** - Creating custom error conditions
- ✅ **Error Objects** - Built-in and custom error types
- ✅ **Async Error Handling** - Handling errors in asynchronous code

### Best Practices
- Always handle errors gracefully
- Provide meaningful error messages
- Use specific error types for different situations
- Log errors for debugging purposes
- Don't ignore errors silently

### Common Mistakes
- Not handling errors at all
- Catching errors but not doing anything with them
- Throwing generic Error objects instead of specific types
- Not handling errors in async operations
- Exposing sensitive information in error messages

---

**Excellent! You've mastered error handling. Ready for ES6+ Features? 🚀**

**Next Tutorial:** [ES6+ Features](./08-es6-features.md)
