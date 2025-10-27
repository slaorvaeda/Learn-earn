# Objects and Arrays - Tutorial 3 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Objects](#objects)
3. [Arrays](#arrays)
4. [Array Methods](#array-methods)
5. [Object Methods](#object-methods)
6. [Practice Exercises](#practice-exercises)
7. [Summary](#summary)

---

## Introduction

Objects and arrays are **fundamental data structures** in JavaScript that allow you to store and organize complex data.

### What You'll Learn
- ✅ **Objects** - Key-value pairs, properties, methods
- ✅ **Arrays** - Ordered lists, indexing, manipulation
- ✅ **Array Methods** - map, filter, reduce, forEach
- ✅ **Object Methods** - Object.keys, Object.values, Object.entries

---

## Objects

### Creating Objects
```javascript
// Object literal
let person = {
    name: "John",
    age: 25,
    city: "New York",
    isStudent: true
};

// Accessing properties
console.log(person.name);        // "John"
console.log(person["age"]);      // 25
console.log(person.city);        // "New York"
```

### Modifying Objects
```javascript
let person = {
    name: "John",
    age: 25
};

// Adding properties
person.city = "New York";
person["isStudent"] = true;

// Modifying properties
person.age = 26;

// Deleting properties
delete person.isStudent;

console.log(person); // { name: "John", age: 26, city: "New York" }
```

### Object Methods
```javascript
let person = {
    name: "John",
    age: 25,
    greet: function() {
        return `Hello, I'm ${this.name}`;
    },
    // ES6 method syntax
    introduce() {
        return `I'm ${this.name} and I'm ${this.age} years old`;
    }
};

console.log(person.greet());     // "Hello, I'm John"
console.log(person.introduce()); // "I'm John and I'm 25 years old"
```

### Nested Objects
```javascript
let student = {
    name: "Alice",
    age: 20,
    address: {
        street: "123 Main St",
        city: "Boston",
        zipCode: "02101"
    },
    grades: {
        math: 95,
        science: 87,
        english: 92
    }
};

console.log(student.address.city);     // "Boston"
console.log(student.grades.math);      // 95
```

---

## Arrays

### Creating Arrays
```javascript
// Array literal
let fruits = ["apple", "banana", "orange"];
let numbers = [1, 2, 3, 4, 5];
let mixed = ["hello", 42, true, null];

// Array constructor
let colors = new Array("red", "green", "blue");

console.log(fruits[0]);    // "apple"
console.log(fruits[1]);    // "banana"
console.log(fruits.length); // 3
```

### Array Manipulation
```javascript
let fruits = ["apple", "banana"];

// Adding elements
fruits.push("orange");        // Add to end
fruits.unshift("grape");      // Add to beginning

// Removing elements
let lastFruit = fruits.pop();     // Remove from end
let firstFruit = fruits.shift();  // Remove from beginning

console.log(fruits);         // ["apple", "banana"]
console.log(lastFruit);      // "orange"
console.log(firstFruit);     // "grape"
```

### Array Slicing and Splicing
```javascript
let numbers = [1, 2, 3, 4, 5];

// slice() - creates new array
let slice1 = numbers.slice(1, 3);  // [2, 3]
let slice2 = numbers.slice(2);    // [3, 4, 5]

// splice() - modifies original array
let removed = numbers.splice(1, 2); // Remove 2 elements starting at index 1
console.log(numbers);               // [1, 4, 5]
console.log(removed);               // [2, 3]

// splice() - add elements
numbers.splice(1, 0, "a", "b");     // Add "a", "b" at index 1
console.log(numbers);               // [1, "a", "b", 4, 5]
```

---

## Array Methods

### forEach
```javascript
let numbers = [1, 2, 3, 4, 5];

numbers.forEach(function(number) {
    console.log(number * 2);
});

// Arrow function syntax
numbers.forEach(number => console.log(number * 2));

// With index
numbers.forEach((number, index) => {
    console.log(`Index ${index}: ${number}`);
});
```

### map
```javascript
let numbers = [1, 2, 3, 4, 5];

// Double each number
let doubled = numbers.map(function(number) {
    return number * 2;
});

// Arrow function syntax
let doubled2 = numbers.map(number => number * 2);

console.log(doubled);  // [2, 4, 6, 8, 10]

// Convert to strings
let strings = numbers.map(number => `Number: ${number}`);
console.log(strings); // ["Number: 1", "Number: 2", ...]
```

### filter
```javascript
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

// Get even numbers
let evenNumbers = numbers.filter(function(number) {
    return number % 2 === 0;
});

// Arrow function syntax
let evenNumbers2 = numbers.filter(number => number % 2 === 0);

console.log(evenNumbers); // [2, 4, 6, 8, 10]

// Filter objects
let people = [
    { name: "John", age: 25 },
    { name: "Alice", age: 17 },
    { name: "Bob", age: 30 }
];

let adults = people.filter(person => person.age >= 18);
console.log(adults); // [{ name: "John", age: 25 }, { name: "Bob", age: 30 }]
```

### reduce
```javascript
let numbers = [1, 2, 3, 4, 5];

// Sum all numbers
let sum = numbers.reduce(function(accumulator, currentValue) {
    return accumulator + currentValue;
}, 0);

// Arrow function syntax
let sum2 = numbers.reduce((acc, curr) => acc + curr, 0);

console.log(sum); // 15

// Find maximum
let max = numbers.reduce((acc, curr) => Math.max(acc, curr));
console.log(max); // 5

// Count occurrences
let words = ["apple", "banana", "apple", "orange", "banana", "apple"];
let count = words.reduce((acc, word) => {
    acc[word] = (acc[word] || 0) + 1;
    return acc;
}, {});

console.log(count); // { apple: 3, banana: 2, orange: 1 }
```

### find and findIndex
```javascript
let people = [
    { name: "John", age: 25 },
    { name: "Alice", age: 17 },
    { name: "Bob", age: 30 }
];

// Find first person over 18
let adult = people.find(person => person.age >= 18);
console.log(adult); // { name: "John", age: 25 }

// Find index of first person over 18
let adultIndex = people.findIndex(person => person.age >= 18);
console.log(adultIndex); // 0
```

### some and every
```javascript
let numbers = [1, 2, 3, 4, 5];

// Check if any number is even
let hasEven = numbers.some(number => number % 2 === 0);
console.log(hasEven); // true

// Check if all numbers are positive
let allPositive = numbers.every(number => number > 0);
console.log(allPositive); // true
```

---

## Object Methods

### Object.keys()
```javascript
let person = {
    name: "John",
    age: 25,
    city: "New York"
};

let keys = Object.keys(person);
console.log(keys); // ["name", "age", "city"]

// Iterate over keys
keys.forEach(key => {
    console.log(`${key}: ${person[key]}`);
});
```

### Object.values()
```javascript
let person = {
    name: "John",
    age: 25,
    city: "New York"
};

let values = Object.values(person);
console.log(values); // ["John", 25, "New York"]
```

### Object.entries()
```javascript
let person = {
    name: "John",
    age: 25,
    city: "New York"
};

let entries = Object.entries(person);
console.log(entries); // [["name", "John"], ["age", 25], ["city", "New York"]]

// Iterate over entries
entries.forEach(([key, value]) => {
    console.log(`${key}: ${value}`);
});
```

### Object.assign()
```javascript
let person1 = { name: "John", age: 25 };
let person2 = { city: "New York", age: 26 };

// Merge objects
let merged = Object.assign({}, person1, person2);
console.log(merged); // { name: "John", age: 26, city: "New York" }

// Spread operator (ES6+)
let merged2 = { ...person1, ...person2 };
console.log(merged2); // { name: "John", age: 26, city: "New York" }
```

---

## Practice Exercises

### Exercise 1: Student Grade Calculator
```javascript
let students = [
    { name: "John", grades: [85, 90, 78] },
    { name: "Alice", grades: [92, 88, 95] },
    { name: "Bob", grades: [76, 82, 80] }
];

function calculateAverage(grades) {
    return grades.reduce((sum, grade) => sum + grade, 0) / grades.length;
}

students.forEach(student => {
    let average = calculateAverage(student.grades);
    console.log(`${student.name}: ${average.toFixed(2)}`);
});
```

### Exercise 2: Shopping Cart
```javascript
let cart = [
    { name: "Apple", price: 1.50, quantity: 3 },
    { name: "Banana", price: 0.75, quantity: 5 },
    { name: "Orange", price: 2.00, quantity: 2 }
];

// Calculate total
let total = cart.reduce((sum, item) => {
    return sum + (item.price * item.quantity);
}, 0);

console.log(`Total: $${total.toFixed(2)}`);

// Find most expensive item
let mostExpensive = cart.reduce((max, item) => {
    return item.price > max.price ? item : max;
});

console.log(`Most expensive: ${mostExpensive.name}`);
```

### Exercise 3: Array Manipulation
```javascript
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

// Get squares of even numbers
let evenSquares = numbers
    .filter(num => num % 2 === 0)
    .map(num => num * num);

console.log(evenSquares); // [4, 16, 36, 64, 100]

// Sum of odd numbers
let oddSum = numbers
    .filter(num => num % 2 !== 0)
    .reduce((sum, num) => sum + num, 0);

console.log(oddSum); // 25
```

---

## Summary

### Key Concepts Learned
- ✅ **Objects** - Properties, methods, nested objects
- ✅ **Arrays** - Creation, manipulation, indexing
- ✅ **Array Methods** - forEach, map, filter, reduce
- ✅ **Object Methods** - keys, values, entries, assign
- ✅ **Data Manipulation** - Transforming and filtering data

### Best Practices
- Use descriptive property names
- Prefer `const` for arrays and objects when possible
- Use array methods instead of loops when appropriate
- Keep objects and arrays focused and organized

### Common Mistakes
- Confusing arrays with objects
- Mutating arrays when you should create new ones
- Not handling undefined properties
- Forgetting that arrays are objects

---

**Excellent! You've mastered objects and arrays. Ready for Functions Deep Dive? 🚀**

**Next Tutorial:** [Functions Deep Dive](./04-functions-deep-dive.md)
