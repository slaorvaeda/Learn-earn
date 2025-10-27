# JavaScript Basics - Tutorial 1 🚀

## Table of Contents
1. [Introduction to JavaScript](#introduction-to-javascript)
2. [Setting Up Your Environment](#setting-up-your-environment)
3. [Variables and Data Types](#variables-and-data-types)
4. [Operators](#operators)
5. [Type Conversion](#type-conversion)
6. [Comments](#comments)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction to JavaScript

### What is JavaScript?
JavaScript is a **high-level, interpreted programming language** that makes web pages interactive. It's one of the core technologies of the web alongside HTML and CSS.

### Why Learn JavaScript?
- ✅ **Universal Language** - Runs everywhere (browser, server, mobile)
- ✅ **High Demand** - Most popular programming language
- ✅ **Easy to Start** - No complex setup required
- ✅ **Versatile** - Frontend, backend, mobile development
- ✅ **Rich Ecosystem** - Thousands of libraries and frameworks

### JavaScript vs Other Languages
| Feature | JavaScript | Python | Java |
|---------|------------|--------|------|
| **Type** | Dynamic | Dynamic | Static |
| **Compilation** | Interpreted | Interpreted | Compiled |
| **Syntax** | C-like | Indentation-based | C-like |
| **Use Case** | Web, Mobile, Server | Data Science, AI | Enterprise |

---

## Setting Up Your Environment

### Method 1: Browser Console
```javascript
// Open browser console (F12)
// Type JavaScript code directly
console.log("Hello, World!");
```

### Method 2: HTML File
```html
<!DOCTYPE html>
<html>
<head>
    <title>JavaScript Tutorial</title>
</head>
<body>
    <h1>Learning JavaScript</h1>
    
    <script>
        // JavaScript code goes here
        console.log("Hello from HTML!");
    </script>
</body>
</html>
```

### Method 3: External JavaScript File
```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
    <title>JavaScript Tutorial</title>
</head>
<body>
    <h1>Learning JavaScript</h1>
    <script src="script.js"></script>
</body>
</html>
```

```javascript
// script.js
console.log("Hello from external file!");
```

### Method 4: Node.js (Command Line)
```bash
# Install Node.js from nodejs.org
# Create a file: hello.js
echo 'console.log("Hello, World!");' > hello.js
node hello.js
```

---

## Variables and Data Types

### Variables Declaration
```javascript
// Three ways to declare variables
var name = "John";           // Old way (avoid)
let age = 25;                // Modern way (mutable)
const city = "New York";     // Modern way (immutable)

console.log(name);  // "John"
console.log(age);   // 25
console.log(city);  // "New York"
```

### Variable Naming Rules
```javascript
// Valid variable names
let userName = "john_doe";
let userAge = 25;
let $price = 99.99;
let _private = "secret";

// Invalid variable names (will cause errors)
// let 2name = "invalid";     // Cannot start with number
// let user-name = "invalid"; // Cannot use hyphens
// let let = "invalid";       // Cannot use reserved words
```

### Data Types
```javascript
// 1. String
let firstName = "John";
let lastName = 'Doe';
let fullName = `John Doe`;  // Template literal

// 2. Number
let age = 25;
let price = 99.99;
let temperature = -10;

// 3. Boolean
let isStudent = true;
let isWorking = false;

// 4. Undefined
let undefinedVariable;
console.log(undefinedVariable); // undefined

// 5. Null
let emptyValue = null;

// 6. Object
let person = {
    name: "John",
    age: 25,
    isStudent: true
};

// 7. Array
let fruits = ["apple", "banana", "orange"];

// 8. Symbol (ES6+)
let id = Symbol("id");
```

### Type Checking
```javascript
let name = "John";
let age = 25;
let isStudent = true;

console.log(typeof name);      // "string"
console.log(typeof age);       // "number"
console.log(typeof isStudent); // "boolean"
console.log(typeof undefined); // "undefined"
console.log(typeof null);      // "object" (this is a bug in JavaScript)
```

---

## Operators

### Arithmetic Operators
```javascript
let a = 10;
let b = 3;

console.log(a + b);  // 13 (addition)
console.log(a - b);  // 7 (subtraction)
console.log(a * b);  // 30 (multiplication)
console.log(a / b);  // 3.333... (division)
console.log(a % b);  // 1 (modulus/remainder)
console.log(a ** b); // 1000 (exponentiation)

// Increment and Decrement
let count = 5;
count++;  // count is now 6
count--;  // count is now 5
++count;  // count is now 6
--count;  // count is now 5
```

### Assignment Operators
```javascript
let x = 10;

x += 5;   // x = x + 5 (15)
x -= 3;   // x = x - 3 (12)
x *= 2;   // x = x * 2 (24)
x /= 4;   // x = x / 4 (6)
x %= 5;   // x = x % 5 (1)
x **= 3;  // x = x ** 3 (1)
```

### Comparison Operators
```javascript
let a = 5;
let b = "5";

console.log(a == b);   // true (loose equality)
console.log(a === b);  // false (strict equality)
console.log(a != b);   // false (loose inequality)
console.log(a !== b);  // true (strict inequality)
console.log(a > b);    // false
console.log(a < b);    // false
console.log(a >= b);   // true
console.log(a <= b);   // true
```

### Logical Operators
```javascript
let isStudent = true;
let hasJob = false;

console.log(isStudent && hasJob);  // false (AND)
console.log(isStudent || hasJob);  // true (OR)
console.log(!isStudent);          // false (NOT)

// Short-circuit evaluation
let name = "John";
let greeting = name && "Hello " + name;  // "Hello John"
let defaultName = name || "Guest";       // "John"
```

### String Operators
```javascript
let firstName = "John";
let lastName = "Doe";

// Concatenation
let fullName = firstName + " " + lastName;  // "John Doe"
let greeting = "Hello, " + fullName + "!"; // "Hello, John Doe!"

// Template literals (ES6+)
let message = `Hello, ${firstName} ${lastName}!`;  // "Hello, John Doe!"
let multiline = `
    Hello,
    ${firstName}!
    Welcome to JavaScript.
`;
```

---

## Type Conversion

### Implicit Conversion (Type Coercion)
```javascript
// String concatenation
let result = "5" + 3;        // "53" (string)
let result2 = "5" - 3;       // 2 (number)
let result3 = "5" * 3;       // 15 (number)
let result4 = "5" / 3;       // 1.666... (number)

// Boolean conversion
console.log(Boolean(1));     // true
console.log(Boolean(0));     // false
console.log(Boolean(""));    // false
console.log(Boolean("hello")); // true
```

### Explicit Conversion
```javascript
// String to Number
let str = "123";
let num = Number(str);       // 123
let num2 = parseInt(str);    // 123
let num3 = parseFloat("123.45"); // 123.45

// Number to String
let number = 456;
let string = String(number);    // "456"
let string2 = number.toString(); // "456"

// Boolean conversion
let truthy = Boolean(1);        // true
let falsy = Boolean(0);         // false
```

### Common Conversion Examples
```javascript
// Converting user input
let userInput = prompt("Enter a number:");
let userNumber = Number(userInput);

if (isNaN(userNumber)) {
    console.log("Invalid number!");
} else {
    console.log("You entered:", userNumber);
}

// Converting for display
let price = 99.99;
let displayPrice = "$" + price.toFixed(2); // "$99.99"
```

---

## Comments

### Single-line Comments
```javascript
// This is a single-line comment
let name = "John"; // This is also a single-line comment
```

### Multi-line Comments
```javascript
/*
This is a multi-line comment
It can span multiple lines
Useful for documentation
*/

let age = 25; /* This is also a multi-line comment */
```

### Comment Best Practices
```javascript
// Good: Explain why, not what
let result = calculateTax(income); // Calculate tax based on current rates

// Bad: Obvious comments
let name = "John"; // Assign "John" to name variable

// Good: Document complex logic
// Check if user is eligible for discount
// Must be over 18 and have valid membership
if (age > 18 && hasMembership) {
    applyDiscount();
}
```

---

## Practice Exercises

### Exercise 1: Variable Declaration
```javascript
// Declare variables for a person
let firstName = "John";
let lastName = "Doe";
let age = 25;
let isStudent = true;

console.log(firstName, lastName, age, isStudent);
```

### Exercise 2: Basic Calculations
```javascript
// Calculate area of a rectangle
let length = 10;
let width = 5;
let area = length * width;

console.log("Area:", area);
```

### Exercise 3: String Manipulation
```javascript
// Create a greeting message
let name = "Alice";
let greeting = `Hello, ${name}! Welcome to JavaScript.`;

console.log(greeting);
```

### Exercise 4: Type Conversion
```javascript
// Convert and calculate
let num1 = "10";
let num2 = "20";
let sum = Number(num1) + Number(num2);

console.log("Sum:", sum);
```

### Exercise 5: Comparison
```javascript
// Compare two numbers
let a = 15;
let b = 20;

console.log("a > b:", a > b);
console.log("a < b:", a < b);
console.log("a === b:", a === b);
```

---

## Summary

### Key Concepts Learned
- ✅ **Variables** - `let`, `const`, `var`
- ✅ **Data Types** - String, Number, Boolean, Object, Array
- ✅ **Operators** - Arithmetic, Assignment, Comparison, Logical
- ✅ **Type Conversion** - Implicit and explicit conversion
- ✅ **Comments** - Single-line and multi-line comments

### Next Steps
1. **Practice** - Write code every day
2. **Experiment** - Try different values and operators
3. **Read** - Study other people's code
4. **Build** - Create simple projects
5. **Move to Tutorial 2** - Control Structures

### Common Mistakes to Avoid
- Using `var` instead of `let`/`const`
- Confusing `==` with `===`
- Not understanding type coercion
- Forgetting semicolons (optional but recommended)

---

**Congratulations! You've completed JavaScript Basics. Ready for Control Structures? 🚀**

**Next Tutorial:** [Control Structures](./02-control-structures.md)
