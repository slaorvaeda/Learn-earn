# ES6+ Features - Tutorial 8 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Let and Const](#let-and-const)
3. [Arrow Functions](#arrow-functions)
4. [Template Literals](#template-literals)
5. [Destructuring](#destructuring)
6. [Spread and Rest Operators](#spread-and-rest-operators)
7. [Classes](#classes)
8. [Modules](#modules)
9. [Practice Exercises](#practice-exercises)
10. [Summary](#summary)

---

## Introduction

ES6+ (ECMAScript 2015 and later) introduced **modern JavaScript features** that make code more readable, maintainable, and powerful.

### What You'll Learn
- ✅ **Let and Const** - Block-scoped variable declarations
- ✅ **Arrow Functions** - Concise function syntax
- ✅ **Template Literals** - String interpolation and multiline strings
- ✅ **Destructuring** - Extracting values from objects and arrays
- ✅ **Spread and Rest** - Flexible parameter and array handling
- ✅ **Classes** - Object-oriented programming syntax
- ✅ **Modules** - Code organization and reusability

---

## Let and Const

### Let Declaration
```javascript
// let has block scope
if (true) {
    let blockScoped = "I'm in a block";
    console.log(blockScoped); // "I'm in a block"
}
// console.log(blockScoped); // ReferenceError: blockScoped is not defined

// let can be reassigned
let count = 0;
count = 1;
count = 2;
console.log(count); // 2

// let is not hoisted
// console.log(hoisted); // ReferenceError: Cannot access 'hoisted' before initialization
let hoisted = "I'm hoisted but not initialized";
```

### Const Declaration
```javascript
// const has block scope and cannot be reassigned
const PI = 3.14159;
// PI = 3.14; // TypeError: Assignment to constant variable

// const objects can have properties modified
const person = {
    name: "John",
    age: 25
};
person.age = 26; // This is allowed
person.city = "New York"; // This is allowed
// person = {}; // This is not allowed

// const arrays can have elements modified
const numbers = [1, 2, 3];
numbers.push(4); // This is allowed
numbers[0] = 10; // This is allowed
// numbers = []; // This is not allowed
```

### Block Scope Examples
```javascript
// var vs let/const
function demonstrateScope() {
    if (true) {
        var varVariable = "I'm function scoped";
        let letVariable = "I'm block scoped";
        const constVariable = "I'm block scoped";
    }
    
    console.log(varVariable); // "I'm function scoped"
    // console.log(letVariable); // ReferenceError
    // console.log(constVariable); // ReferenceError
}

// Loop with let
for (let i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i); // 0, 1, 2 (each iteration has its own i)
    }, 100);
}

// Loop with var (old way)
for (var i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i); // 3, 3, 3 (all closures share the same i)
    }, 100);
}
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
const addArrow = (a, b) => {
    return a + b;
};

// Arrow function with implicit return
const addImplicit = (a, b) => a + b;

// Single parameter (parentheses optional)
const square = x => x * x;

// No parameters
const greet = () => "Hello World!";

console.log(add(2, 3));        // 5
console.log(addArrow(2, 3));   // 5
console.log(addImplicit(2, 3)); // 5
console.log(square(4));        // 16
console.log(greet());          // "Hello World!"
```

### Arrow Functions vs Regular Functions
```javascript
const obj = {
    name: "John",
    
    // Regular function (has its own 'this')
    regularMethod: function() {
        console.log("Regular function this:", this.name);
        
        // Nested function loses 'this'
        function nested() {
            console.log("Nested function this:", this.name); // undefined
        }
        nested();
    },
    
    // Arrow function (inherits 'this' from outer scope)
    arrowMethod: function() {
        console.log("Arrow method this:", this.name);
        
        // Arrow function preserves 'this'
        const nested = () => {
            console.log("Arrow nested this:", this.name); // "John"
        };
        nested();
    }
};

obj.regularMethod();
obj.arrowMethod();
```

### Arrow Functions in Array Methods
```javascript
const numbers = [1, 2, 3, 4, 5];

// map with arrow function
const doubled = numbers.map(num => num * 2);

// filter with arrow function
const evens = numbers.filter(num => num % 2 === 0);

// reduce with arrow function
const sum = numbers.reduce((acc, num) => acc + num, 0);

// forEach with arrow function
numbers.forEach(num => console.log(num));

console.log(doubled); // [2, 4, 6, 8, 10]
console.log(evens);   // [2, 4]
console.log(sum);     // 15
```

---

## Template Literals

### Basic Template Literals
```javascript
const name = "John";
const age = 25;

// Traditional string concatenation
const message1 = "Hello, " + name + "! You are " + age + " years old.";

// Template literal
const message2 = `Hello, ${name}! You are ${age} years old.`;

console.log(message1); // "Hello, John! You are 25 years old."
console.log(message2); // "Hello, John! You are 25 years old."
```

### Multiline Strings
```javascript
// Traditional multiline string
const multiline1 = "Line 1\n" +
                  "Line 2\n" +
                  "Line 3";

// Template literal multiline
const multiline2 = `Line 1
Line 2
Line 3`;

console.log(multiline1);
console.log(multiline2);
```

### Expression Evaluation
```javascript
const a = 10;
const b = 20;

// Expressions in template literals
const result = `${a} + ${b} = ${a + b}`;
console.log(result); // "10 + 20 = 30"

// Function calls in template literals
const greet = (name) => `Hello, ${name}!`;
const message = `${greet("Alice")} Welcome to our site.`;
console.log(message); // "Hello, Alice! Welcome to our site."

// Conditional expressions
const isLoggedIn = true;
const userMessage = `Welcome ${isLoggedIn ? "back" : "to our site"}!`;
console.log(userMessage); // "Welcome back!"
```

### Tagged Template Literals
```javascript
// Tag function
function highlight(strings, ...values) {
    return strings.reduce((result, string, i) => {
        const value = values[i] ? `<mark>${values[i]}</mark>` : '';
        return result + string + value;
    }, '');
}

const name = "John";
const age = 25;
const html = highlight`Hello ${name}, you are ${age} years old.`;
console.log(html); // "Hello <mark>John</mark>, you are <mark>25</mark> years old."
```

---

## Destructuring

### Array Destructuring
```javascript
const numbers = [1, 2, 3, 4, 5];

// Basic destructuring
const [first, second, third] = numbers;
console.log(first, second, third); // 1 2 3

// Skip elements
const [a, , c, , e] = numbers;
console.log(a, c, e); // 1 3 5

// Rest operator
const [head, ...tail] = numbers;
console.log(head); // 1
console.log(tail); // [2, 3, 4, 5]

// Default values
const [x, y, z = 10] = [1, 2];
console.log(x, y, z); // 1 2 10

// Swap variables
let a = 1, b = 2;
[a, b] = [b, a];
console.log(a, b); // 2 1
```

### Object Destructuring
```javascript
const person = {
    name: "John",
    age: 25,
    city: "New York",
    country: "USA"
};

// Basic destructuring
const { name, age, city } = person;
console.log(name, age, city); // "John" 25 "New York"

// Rename variables
const { name: personName, age: personAge } = person;
console.log(personName, personAge); // "John" 25

// Default values
const { name, age, salary = 50000 } = person;
console.log(name, age, salary); // "John" 25 50000

// Rest operator
const { name, ...rest } = person;
console.log(name); // "John"
console.log(rest); // { age: 25, city: "New York", country: "USA" }
```

### Nested Destructuring
```javascript
const user = {
    id: 1,
    name: "John",
    address: {
        street: "123 Main St",
        city: "New York",
        coordinates: {
            lat: 40.7128,
            lng: -74.0060
        }
    }
};

// Nested destructuring
const {
    name,
    address: {
        city,
        coordinates: { lat, lng }
    }
} = user;

console.log(name, city, lat, lng); // "John" "New York" 40.7128 -74.0060
```

### Function Parameter Destructuring
```javascript
// Object parameter destructuring
function greet({ name, age, city = "Unknown" }) {
    return `Hello ${name}, you are ${age} years old from ${city}.`;
}

const person = { name: "Alice", age: 30 };
console.log(greet(person)); // "Hello Alice, you are 30 years old from Unknown."

// Array parameter destructuring
function sum([a, b, c = 0]) {
    return a + b + c;
}

console.log(sum([1, 2])); // 3
console.log(sum([1, 2, 3])); // 6
```

---

## Spread and Rest Operators

### Spread Operator
```javascript
// Array spreading
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4, 5, 6]

// Object spreading
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };
const merged = { ...obj1, ...obj2 };
console.log(merged); // { a: 1, b: 2, c: 3, d: 4 }

// Function calls
const numbers = [1, 2, 3, 4, 5];
const max = Math.max(...numbers);
console.log(max); // 5

// Copying arrays and objects
const originalArray = [1, 2, 3];
const copiedArray = [...originalArray];
const originalObject = { x: 1, y: 2 };
const copiedObject = { ...originalObject };
```

### Rest Operator
```javascript
// Rest in function parameters
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3, 4, 5)); // 15

// Rest in destructuring
const [first, ...rest] = [1, 2, 3, 4, 5];
console.log(first); // 1
console.log(rest); // [2, 3, 4, 5]

const { name, ...otherProps } = { name: "John", age: 25, city: "NYC" };
console.log(name); // "John"
console.log(otherProps); // { age: 25, city: "NYC" }
```

---

## Classes

### Basic Class Syntax
```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    greet() {
        return `Hello, I'm ${this.name}`;
    }
    
    getAge() {
        return this.age;
    }
}

const person = new Person("John", 25);
console.log(person.greet()); // "Hello, I'm John"
console.log(person.getAge()); // 25
```

### Class Inheritance
```javascript
class Animal {
    constructor(name) {
        this.name = name;
    }
    
    speak() {
        return `${this.name} makes a sound`;
    }
}

class Dog extends Animal {
    constructor(name, breed) {
        super(name);
        this.breed = breed;
    }
    
    speak() {
        return `${this.name} barks`;
    }
    
    getBreed() {
        return this.breed;
    }
}

const dog = new Dog("Buddy", "Golden Retriever");
console.log(dog.speak()); // "Buddy barks"
console.log(dog.getBreed()); // "Golden Retriever"
```

### Static Methods and Properties
```javascript
class MathUtils {
    static PI = 3.14159;
    
    static add(a, b) {
        return a + b;
    }
    
    static multiply(a, b) {
        return a * b;
    }
}

console.log(MathUtils.PI); // 3.14159
console.log(MathUtils.add(2, 3)); // 5
console.log(MathUtils.multiply(4, 5)); // 20
```

### Getters and Setters
```javascript
class Circle {
    constructor(radius) {
        this._radius = radius;
    }
    
    get radius() {
        return this._radius;
    }
    
    set radius(value) {
        if (value < 0) {
            throw new Error("Radius cannot be negative");
        }
        this._radius = value;
    }
    
    get area() {
        return Math.PI * this._radius * this._radius;
    }
}

const circle = new Circle(5);
console.log(circle.radius); // 5
console.log(circle.area); // 78.54
circle.radius = 10;
console.log(circle.area); // 314.16
```

---

## Modules

### Export and Import
```javascript
// math.js - Exporting
export const PI = 3.14159;

export function add(a, b) {
    return a + b;
}

export function multiply(a, b) {
    return a * b;
}

// Default export
export default class Calculator {
    constructor() {
        this.result = 0;
    }
    
    add(value) {
        this.result += value;
        return this;
    }
    
    getResult() {
        return this.result;
    }
}

// main.js - Importing
import Calculator, { PI, add, multiply } from './math.js';

console.log(PI); // 3.14159
console.log(add(2, 3)); // 5
console.log(multiply(4, 5)); // 20

const calc = new Calculator();
console.log(calc.add(5).add(3).getResult()); // 8
```

### Named Exports and Imports
```javascript
// utils.js
export const formatDate = (date) => {
    return date.toLocaleDateString();
};

export const formatCurrency = (amount) => {
    return `$${amount.toFixed(2)}`;
};

export const validateEmail = (email) => {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
};

// app.js
import { formatDate, formatCurrency, validateEmail } from './utils.js';

const today = new Date();
console.log(formatDate(today)); // "12/25/2023"

console.log(formatCurrency(99.99)); // "$99.99"

console.log(validateEmail("test@example.com")); // true
```

---

## Practice Exercises

### Exercise 1: Modern Array Operations
```javascript
const products = [
    { id: 1, name: "Laptop", price: 999, category: "Electronics" },
    { id: 2, name: "Book", price: 19.99, category: "Education" },
    { id: 3, name: "Phone", price: 699, category: "Electronics" },
    { id: 4, name: "Pen", price: 2.99, category: "Office" }
];

// Use modern array methods and ES6+ features
const electronics = products
    .filter(product => product.category === "Electronics")
    .map(product => ({
        ...product,
        priceWithTax: product.price * 1.08
    }))
    .sort((a, b) => a.price - b.price);

console.log(electronics);
```

### Exercise 2: Class-based Calculator
```javascript
class Calculator {
    constructor() {
        this.history = [];
    }
    
    add(a, b) {
        const result = a + b;
        this.history.push(`${a} + ${b} = ${result}`);
        return result;
    }
    
    subtract(a, b) {
        const result = a - b;
        this.history.push(`${a} - ${b} = ${result}`);
        return result;
    }
    
    multiply(a, b) {
        const result = a * b;
        this.history.push(`${a} * ${b} = ${result}`);
        return result;
    }
    
    divide(a, b) {
        if (b === 0) {
            throw new Error("Division by zero is not allowed");
        }
        const result = a / b;
        this.history.push(`${a} / ${b} = ${result}`);
        return result;
    }
    
    getHistory() {
        return [...this.history];
    }
    
    clearHistory() {
        this.history = [];
    }
}

const calc = new Calculator();
console.log(calc.add(5, 3)); // 8
console.log(calc.multiply(4, 6)); // 24
console.log(calc.getHistory());
```

### Exercise 3: Module-based User Management
```javascript
// user.js
export class User {
    constructor(name, email, age) {
        this.name = name;
        this.email = email;
        this.age = age;
        this.createdAt = new Date();
    }
    
    getInfo() {
        return `${this.name} (${this.email}) - ${this.age} years old`;
    }
    
    isAdult() {
        return this.age >= 18;
    }
}

export class UserManager {
    constructor() {
        this.users = [];
    }
    
    addUser(user) {
        this.users.push(user);
    }
    
    findUserByEmail(email) {
        return this.users.find(user => user.email === email);
    }
    
    getAdultUsers() {
        return this.users.filter(user => user.isAdult());
    }
    
    getUsersCount() {
        return this.users.length;
    }
}

// app.js
import { User, UserManager } from './user.js';

const userManager = new UserManager();

const user1 = new User("John Doe", "john@example.com", 25);
const user2 = new User("Jane Smith", "jane@example.com", 17);

userManager.addUser(user1);
userManager.addUser(user2);

console.log(userManager.getUsersCount()); // 2
console.log(userManager.getAdultUsers().length); // 1
```

---

## Summary

### Key Concepts Learned
- ✅ **Let and Const** - Block-scoped variable declarations
- ✅ **Arrow Functions** - Concise function syntax with lexical this
- ✅ **Template Literals** - String interpolation and multiline strings
- ✅ **Destructuring** - Extracting values from objects and arrays
- ✅ **Spread and Rest** - Flexible parameter and array handling
- ✅ **Classes** - Object-oriented programming syntax
- ✅ **Modules** - Code organization and reusability

### Best Practices
- Use `const` by default, `let` when reassignment is needed
- Use arrow functions for short, simple functions
- Use template literals for string interpolation
- Use destructuring for cleaner parameter handling
- Use classes for object-oriented programming
- Use modules for code organization

### Common Mistakes
- Confusing arrow function `this` with regular function `this`
- Not understanding block scope vs function scope
- Overusing spread operator when not needed
- Not handling module imports/exports correctly
- Mixing old and new syntax inconsistently

---

**Excellent! You've mastered ES6+ features. Ready for Modules and Imports? 🚀**

**Next Tutorial:** [Modules and Imports](./09-modules-imports.md)
