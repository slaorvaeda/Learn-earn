# Functions Deep Dive - Tutorial 4 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Function Scope](#function-scope)
3. [Closures](#closures)
4. [Arrow Functions](#arrow-functions)
5. [Higher-Order Functions](#higher-order-functions)
6. [Callback Functions](#callback-functions)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Functions are the **building blocks of JavaScript**. This tutorial explores advanced function concepts that make JavaScript powerful and flexible.

### What You'll Learn
- ✅ **Function Scope** - Global, local, block scope
- ✅ **Closures** - Function with access to outer scope
- ✅ **Arrow Functions** - Modern function syntax
- ✅ **Higher-Order Functions** - Functions that work with other functions
- ✅ **Callback Functions** - Functions passed as arguments

---

## Function Scope

### Global Scope
```javascript
// Global variable
let globalVar = "I'm global";

function myFunction() {
    console.log(globalVar); // Can access global variable
}

myFunction(); // "I'm global"
```

### Local Scope
```javascript
function myFunction() {
    // Local variable
    let localVar = "I'm local";
    console.log(localVar);
}

myFunction(); // "I'm local"
// console.log(localVar); // Error: localVar is not defined
```

### Block Scope
```javascript
if (true) {
    let blockVar = "I'm in a block";
    const blockConst = "I'm also in a block";
    var functionVar = "I'm function scoped";
}

// console.log(blockVar);   // Error: blockVar is not defined
// console.log(blockConst); // Error: blockConst is not defined
console.log(functionVar);   // "I'm function scoped" (var is function scoped)
```

### Scope Chain
```javascript
let globalVar = "global";

function outerFunction() {
    let outerVar = "outer";
    
    function innerFunction() {
        let innerVar = "inner";
        console.log(globalVar); // Can access global
        console.log(outerVar);  // Can access outer
        console.log(innerVar);  // Can access inner
    }
    
    innerFunction();
}

outerFunction();
```

---

## Closures

### What are Closures?
A closure is a function that has access to variables in its outer (enclosing) scope even after the outer function has returned.

### Basic Closure Example
```javascript
function outerFunction(x) {
    // Outer function's variable
    let outerVariable = x;
    
    // Inner function (closure)
    function innerFunction(y) {
        return outerVariable + y;
    }
    
    return innerFunction;
}

let closure = outerFunction(10);
console.log(closure(5)); // 15
```

### Practical Closure Examples
```javascript
// Counter with closure
function createCounter() {
    let count = 0;
    
    return function() {
        count++;
        return count;
    };
}

let counter1 = createCounter();
let counter2 = createCounter();

console.log(counter1()); // 1
console.log(counter1()); // 2
console.log(counter2()); // 1
console.log(counter1()); // 3
```

### Closure with Parameters
```javascript
function createMultiplier(multiplier) {
    return function(number) {
        return number * multiplier;
    };
}

let double = createMultiplier(2);
let triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```

### Closure in Event Handlers
```javascript
function createButtonHandler(buttonName) {
    return function() {
        console.log(`Button ${buttonName} was clicked!`);
    };
}

let button1Handler = createButtonHandler("Submit");
let button2Handler = createButtonHandler("Cancel");

// In real HTML:
// button1.addEventListener('click', button1Handler);
// button2.addEventListener('click', button2Handler);
```

---

## Arrow Functions

### Basic Arrow Function Syntax
```javascript
// Traditional function
function add(a, b) {
    return a + b;
}

// Arrow function
let addArrow = (a, b) => {
    return a + b;
};

// Shorter syntax (single expression)
let addShort = (a, b) => a + b;

console.log(add(2, 3));      // 5
console.log(addArrow(2, 3)); // 5
console.log(addShort(2, 3)); // 5
```

### Arrow Function Variations
```javascript
// Single parameter (parentheses optional)
let square = x => x * x;

// No parameters
let greet = () => "Hello World!";

// Multiple parameters
let multiply = (a, b, c) => a * b * c;

// Multiple statements
let processData = (data) => {
    let processed = data.toUpperCase();
    return processed + " processed";
};

console.log(square(5));           // 25
console.log(greet());             // "Hello World!"
console.log(multiply(2, 3, 4));   // 24
console.log(processData("hello")); // "HELLO processed"
```

### Arrow Functions vs Regular Functions
```javascript
let obj = {
    name: "John",
    
    // Regular function (has its own 'this')
    regularFunction: function() {
        console.log(this.name);
    },
    
    // Arrow function (inherits 'this' from outer scope)
    arrowFunction: () => {
        console.log(this.name); // undefined (or global object)
    }
};

obj.regularFunction(); // "John"
obj.arrowFunction();   // undefined
```

### Arrow Functions in Array Methods
```javascript
let numbers = [1, 2, 3, 4, 5];

// Traditional function
let doubled = numbers.map(function(num) {
    return num * 2;
});

// Arrow function
let doubledArrow = numbers.map(num => num * 2);

// Filter with arrow function
let evens = numbers.filter(num => num % 2 === 0);

// Reduce with arrow function
let sum = numbers.reduce((acc, num) => acc + num, 0);

console.log(doubled);    // [2, 4, 6, 8, 10]
console.log(doubledArrow); // [2, 4, 6, 8, 10]
console.log(evens);      // [2, 4]
console.log(sum);        // 15
```

---

## Higher-Order Functions

### Functions as Arguments
```javascript
// Higher-order function that takes a function as argument
function processArray(array, processor) {
    let result = [];
    for (let item of array) {
        result.push(processor(item));
    }
    return result;
}

// Functions to pass as arguments
let double = x => x * 2;
let square = x => x * x;
let addOne = x => x + 1;

let numbers = [1, 2, 3, 4, 5];

console.log(processArray(numbers, double));  // [2, 4, 6, 8, 10]
console.log(processArray(numbers, square));  // [1, 4, 9, 16, 25]
console.log(processArray(numbers, addOne));  // [2, 3, 4, 5, 6]
```

### Functions as Return Values
```javascript
// Function that returns a function
function createValidator(minLength) {
    return function(text) {
        return text.length >= minLength;
    };
}

let validateShort = createValidator(3);
let validateLong = createValidator(10);

console.log(validateShort("hi"));   // false
console.log(validateShort("hello")); // true
console.log(validateLong("hello"));  // false
console.log(validateLong("hello world")); // true
```

### Built-in Higher-Order Functions
```javascript
let numbers = [1, 2, 3, 4, 5];

// map - transforms each element
let squares = numbers.map(x => x * x);

// filter - selects elements based on condition
let evens = numbers.filter(x => x % 2 === 0);

// reduce - reduces array to single value
let sum = numbers.reduce((acc, x) => acc + x, 0);

// forEach - executes function for each element
numbers.forEach(x => console.log(x));

console.log(squares); // [1, 4, 9, 16, 25]
console.log(evens);   // [2, 4]
console.log(sum);     // 15
```

---

## Callback Functions

### Basic Callback Example
```javascript
function greet(name, callback) {
    console.log(`Hello, ${name}!`);
    callback();
}

function sayGoodbye() {
    console.log("Goodbye!");
}

greet("John", sayGoodbye);
// Output:
// Hello, John!
// Goodbye!
```

### Callback with Parameters
```javascript
function processUser(user, callback) {
    console.log(`Processing user: ${user.name}`);
    let processedUser = {
        ...user,
        processed: true,
        processedAt: new Date()
    };
    callback(processedUser);
}

function displayUser(user) {
    console.log(`User ${user.name} processed at ${user.processedAt}`);
}

let user = { name: "Alice", age: 25 };
processUser(user, displayUser);
```

### Error-First Callbacks
```javascript
function divide(a, b, callback) {
    if (b === 0) {
        callback(new Error("Division by zero"), null);
    } else {
        callback(null, a / b);
    }
}

divide(10, 2, (error, result) => {
    if (error) {
        console.log("Error:", error.message);
    } else {
        console.log("Result:", result);
    }
});

divide(10, 0, (error, result) => {
    if (error) {
        console.log("Error:", error.message);
    } else {
        console.log("Result:", result);
    }
});
```

### Callbacks in Array Methods
```javascript
let students = [
    { name: "John", grade: 85 },
    { name: "Alice", grade: 92 },
    { name: "Bob", grade: 78 }
];

// Using forEach with callback
students.forEach(function(student) {
    console.log(`${student.name}: ${student.grade}`);
});

// Using map with callback
let gradeLetters = students.map(function(student) {
    if (student.grade >= 90) return "A";
    if (student.grade >= 80) return "B";
    if (student.grade >= 70) return "C";
    return "F";
});

console.log(gradeLetters); // ["B", "A", "C"]
```

---

## Practice Exercises

### Exercise 1: Closure Counter
```javascript
function createCounter(initialValue = 0) {
    let count = initialValue;
    
    return {
        increment: () => ++count,
        decrement: () => --count,
        getValue: () => count,
        reset: () => count = initialValue
    };
}

let counter = createCounter(10);
console.log(counter.getValue()); // 10
console.log(counter.increment()); // 11
console.log(counter.increment()); // 12
console.log(counter.decrement()); // 11
console.log(counter.reset());     // 10
```

### Exercise 2: Function Factory
```javascript
function createCalculator(operation) {
    return function(a, b) {
        switch(operation) {
            case 'add':
                return a + b;
            case 'subtract':
                return a - b;
            case 'multiply':
                return a * b;
            case 'divide':
                return b !== 0 ? a / b : 'Cannot divide by zero';
            default:
                return 'Invalid operation';
        }
    };
}

let add = createCalculator('add');
let multiply = createCalculator('multiply');

console.log(add(5, 3));      // 8
console.log(multiply(4, 6)); // 24
```

### Exercise 3: Higher-Order Function
```javascript
function createFilter(predicate) {
    return function(array) {
        return array.filter(predicate);
    };
}

let filterEvens = createFilter(x => x % 2 === 0);
let filterPositives = createFilter(x => x > 0);

let numbers = [-2, -1, 0, 1, 2, 3, 4, 5];
console.log(filterEvens(numbers));    // [-2, 0, 2, 4]
console.log(filterPositives(numbers)); // [1, 2, 3, 4, 5]
```

---

## Summary

### Key Concepts Learned
- ✅ **Function Scope** - Global, local, block scope
- ✅ **Closures** - Functions with access to outer scope
- ✅ **Arrow Functions** - Modern, concise function syntax
- ✅ **Higher-Order Functions** - Functions that work with other functions
- ✅ **Callback Functions** - Functions passed as arguments

### Best Practices
- Use arrow functions for short, simple functions
- Use regular functions for methods that need `this`
- Understand scope to avoid variable conflicts
- Use closures to create private variables
- Prefer higher-order functions for data transformation

### Common Mistakes
- Confusing arrow function `this` with regular function `this`
- Creating memory leaks with closures
- Not understanding scope chain
- Overusing arrow functions when regular functions are clearer

---

**Fantastic! You've mastered advanced function concepts. Ready for DOM Manipulation? 🚀**

**Next Tutorial:** [DOM Manipulation](./05-dom-manipulation.md)
