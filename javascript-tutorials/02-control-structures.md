# Control Structures - Tutorial 2 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Conditional Statements](#conditional-statements)
3. [Loops](#loops)
4. [Functions](#functions)
5. [Switch Statements](#switch-statements)
6. [Practice Exercises](#practice-exercises)
7. [Summary](#summary)

---

## Introduction

Control structures allow you to **control the flow of your program** by making decisions and repeating actions. They are fundamental to programming logic.

### What You'll Learn
- ✅ **Conditional Statements** - if, else if, else
- ✅ **Loops** - for, while, do-while
- ✅ **Functions** - Declaration, parameters, return values
- ✅ **Switch Statements** - Multiple condition handling

---

## Conditional Statements

### if Statement
```javascript
let age = 18;

if (age >= 18) {
    console.log("You are an adult");
}
```

### if-else Statement
```javascript
let temperature = 25;

if (temperature > 30) {
    console.log("It's hot outside");
} else {
    console.log("It's not hot outside");
}
```

### if-else if-else Statement
```javascript
let score = 85;

if (score >= 90) {
    console.log("Grade: A");
} else if (score >= 80) {
    console.log("Grade: B");
} else if (score >= 70) {
    console.log("Grade: C");
} else if (score >= 60) {
    console.log("Grade: D");
} else {
    console.log("Grade: F");
}
```

### Ternary Operator
```javascript
let age = 20;
let status = age >= 18 ? "adult" : "minor";
console.log(status); // "adult"

// Equivalent to:
let status2;
if (age >= 18) {
    status2 = "adult";
} else {
    status2 = "minor";
}
```

### Logical Operators in Conditions
```javascript
let isStudent = true;
let hasDiscount = false;
let age = 22;

// AND operator
if (isStudent && age < 25) {
    console.log("Student discount available");
}

// OR operator
if (isStudent || hasDiscount) {
    console.log("Discount available");
}

// NOT operator
if (!isStudent) {
    console.log("Not a student");
}
```

---

## Loops

### for Loop
```javascript
// Basic for loop
for (let i = 0; i < 5; i++) {
    console.log("Iteration:", i);
}

// Loop through array
let fruits = ["apple", "banana", "orange"];
for (let i = 0; i < fruits.length; i++) {
    console.log(fruits[i]);
}

// for...of loop (ES6+)
for (let fruit of fruits) {
    console.log(fruit);
}
```

### while Loop
```javascript
let count = 0;
while (count < 5) {
    console.log("Count:", count);
    count++;
}

// Infinite loop (be careful!)
// while (true) {
//     console.log("This will run forever");
// }
```

### do-while Loop
```javascript
let count = 0;
do {
    console.log("Count:", count);
    count++;
} while (count < 5);

// do-while runs at least once
let x = 10;
do {
    console.log("This runs once");
} while (x < 5);
```

### Loop Control Statements
```javascript
// break - exit the loop
for (let i = 0; i < 10; i++) {
    if (i === 5) {
        break; // Exit the loop
    }
    console.log(i);
}

// continue - skip current iteration
for (let i = 0; i < 10; i++) {
    if (i === 5) {
        continue; // Skip this iteration
    }
    console.log(i);
}

// Nested loops
for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
        console.log(`i: ${i}, j: ${j}`);
    }
}
```

---

## Functions

### Function Declaration
```javascript
// Function declaration
function greet(name) {
    return `Hello, ${name}!`;
}

let message = greet("John");
console.log(message); // "Hello, John!"
```

### Function Expression
```javascript
// Function expression
let greet = function(name) {
    return `Hello, ${name}!`;
};

console.log(greet("Alice")); // "Hello, Alice!"
```

### Arrow Functions (ES6+)
```javascript
// Arrow function
let greet = (name) => {
    return `Hello, ${name}!`;
};

// Shorter syntax
let greet2 = name => `Hello, ${name}!`;

// Multiple parameters
let add = (a, b) => a + b;

console.log(add(5, 3)); // 8
```

### Function Parameters
```javascript
// Default parameters
function greet(name = "Guest") {
    return `Hello, ${name}!`;
}

console.log(greet()); // "Hello, Guest!"
console.log(greet("John")); // "Hello, John!"

// Rest parameters
function sum(...numbers) {
    let total = 0;
    for (let num of numbers) {
        total += num;
    }
    return total;
}

console.log(sum(1, 2, 3, 4, 5)); // 15
```

### Return Values
```javascript
// Function with return value
function calculateArea(length, width) {
    return length * width;
}

let area = calculateArea(10, 5);
console.log("Area:", area); // 50

// Function without return value
function displayMessage(message) {
    console.log(message);
    // No return statement
}

displayMessage("Hello World!");
```

### Function Scope
```javascript
let globalVar = "I'm global";

function myFunction() {
    let localVar = "I'm local";
    console.log(globalVar); // Can access global
    console.log(localVar);  // Can access local
}

myFunction();
console.log(globalVar); // Can access global
// console.log(localVar); // Error: localVar is not defined
```

---

## Switch Statements

### Basic Switch
```javascript
let day = 3;
let dayName;

switch (day) {
    case 1:
        dayName = "Monday";
        break;
    case 2:
        dayName = "Tuesday";
        break;
    case 3:
        dayName = "Wednesday";
        break;
    case 4:
        dayName = "Thursday";
        break;
    case 5:
        dayName = "Friday";
        break;
    case 6:
        dayName = "Saturday";
        break;
    case 7:
        dayName = "Sunday";
        break;
    default:
        dayName = "Invalid day";
}

console.log(dayName); // "Wednesday"
```

### Switch with Multiple Cases
```javascript
let grade = "B";

switch (grade) {
    case "A":
    case "B":
        console.log("Good job!");
        break;
    case "C":
        console.log("Not bad");
        break;
    case "D":
    case "F":
        console.log("Need improvement");
        break;
    default:
        console.log("Invalid grade");
}
```

### Switch vs if-else
```javascript
// Using if-else
let month = 3;
if (month === 1) {
    console.log("January");
} else if (month === 2) {
    console.log("February");
} else if (month === 3) {
    console.log("March");
} // ... more conditions

// Using switch (cleaner for many conditions)
switch (month) {
    case 1:
        console.log("January");
        break;
    case 2:
        console.log("February");
        break;
    case 3:
        console.log("March");
        break;
    // ... more cases
}
```

---

## Practice Exercises

### Exercise 1: Grade Calculator
```javascript
function calculateGrade(score) {
    if (score >= 90) {
        return "A";
    } else if (score >= 80) {
        return "B";
    } else if (score >= 70) {
        return "C";
    } else if (score >= 60) {
        return "D";
    } else {
        return "F";
    }
}

console.log(calculateGrade(85)); // "B"
```

### Exercise 2: Number Guessing Game
```javascript
function checkGuess(guess, target) {
    if (guess === target) {
        return "Correct!";
    } else if (guess < target) {
        return "Too low!";
    } else {
        return "Too high!";
    }
}

console.log(checkGuess(5, 7)); // "Too low!"
```

### Exercise 3: Factorial Calculator
```javascript
function factorial(n) {
    let result = 1;
    for (let i = 1; i <= n; i++) {
        result *= i;
    }
    return result;
}

console.log(factorial(5)); // 120
```

### Exercise 4: Array Sum
```javascript
function sumArray(numbers) {
    let total = 0;
    for (let num of numbers) {
        total += num;
    }
    return total;
}

console.log(sumArray([1, 2, 3, 4, 5])); // 15
```

### Exercise 5: Temperature Converter
```javascript
function celsiusToFahrenheit(celsius) {
    return (celsius * 9/5) + 32;
}

function fahrenheitToCelsius(fahrenheit) {
    return (fahrenheit - 32) * 5/9;
}

console.log(celsiusToFahrenheit(25)); // 77
console.log(fahrenheitToCelsius(77)); // 25
```

---

## Summary

### Key Concepts Learned
- ✅ **Conditional Statements** - if, else if, else, ternary operator
- ✅ **Loops** - for, while, do-while, break, continue
- ✅ **Functions** - Declaration, expression, arrow functions
- ✅ **Parameters** - Default, rest parameters
- ✅ **Switch Statements** - Multiple condition handling

### Best Practices
- Use meaningful variable names
- Keep functions small and focused
- Use `const` and `let` instead of `var`
- Always use `===` for comparison
- Add comments for complex logic

### Common Mistakes
- Forgetting `break` in switch statements
- Infinite loops
- Not handling edge cases
- Confusing `=` with `===`

---

**Great job! You've mastered control structures. Ready for Objects and Arrays? 🚀**

**Next Tutorial:** [Objects and Arrays](./03-objects-arrays.md)
