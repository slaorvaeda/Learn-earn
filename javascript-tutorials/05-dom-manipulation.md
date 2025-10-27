# DOM Manipulation - Tutorial 5 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [What is the DOM?](#what-is-the-dom)
3. [Selecting Elements](#selecting-elements)
4. [Modifying Elements](#modifying-elements)
5. [Creating and Removing Elements](#creating-and-removing-elements)
6. [Event Handling](#event-handling)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

The Document Object Model (DOM) allows JavaScript to **interact with HTML elements** and create dynamic, interactive web pages.

### What You'll Learn
- ✅ **DOM Structure** - Understanding the DOM tree
- ✅ **Element Selection** - Finding HTML elements
- ✅ **Element Modification** - Changing content and attributes
- ✅ **Element Creation** - Adding new elements
- ✅ **Event Handling** - Responding to user interactions

---

## What is the DOM?

### DOM Tree Structure
```html
<!DOCTYPE html>
<html>
<head>
    <title>DOM Example</title>
</head>
<body>
    <h1 id="main-title">Hello World</h1>
    <p class="description">This is a paragraph</p>
    <ul>
        <li>Item 1</li>
        <li>Item 2</li>
    </ul>
</body>
</html>
```

### DOM Nodes
```javascript
// Every element in the DOM is a node
// Types of nodes:
// 1. Element nodes (HTML tags)
// 2. Text nodes (text content)
// 3. Attribute nodes (attributes)

console.log(document); // The root document node
console.log(document.body); // The body element node
```

---

## Selecting Elements

### getElementById
```javascript
// Select element by ID
let title = document.getElementById('main-title');
console.log(title); // <h1 id="main-title">Hello World</h1>

// Change content
title.textContent = "New Title";
title.innerHTML = "<em>New Title</em>";
```

### getElementsByClassName
```javascript
// Select elements by class name
let descriptions = document.getElementsByClassName('description');
console.log(descriptions); // HTMLCollection

// Access individual elements
let firstDesc = descriptions[0];
console.log(firstDesc.textContent);
```

### getElementsByTagName
```javascript
// Select elements by tag name
let paragraphs = document.getElementsByTagName('p');
let listItems = document.getElementsByTagName('li');

console.log(paragraphs.length); // Number of paragraphs
console.log(listItems[0].textContent); // First list item
```

### querySelector and querySelectorAll
```javascript
// Modern way to select elements
let title = document.querySelector('#main-title');
let descriptions = document.querySelectorAll('.description');
let paragraphs = document.querySelectorAll('p');

// CSS selectors work here
let firstParagraph = document.querySelector('p:first-child');
let lastListItem = document.querySelector('li:last-child');

console.log(title);
console.log(descriptions.length);
```

### Selecting Multiple Elements
```javascript
// Get all elements with class 'item'
let items = document.querySelectorAll('.item');

// Loop through selected elements
items.forEach((item, index) => {
    console.log(`Item ${index}: ${item.textContent}`);
});

// Convert NodeList to Array
let itemsArray = Array.from(items);
let itemsArray2 = [...items]; // Spread operator
```

---

## Modifying Elements

### Changing Text Content
```javascript
let element = document.getElementById('my-element');

// textContent - plain text
element.textContent = "New text content";

// innerHTML - HTML content
element.innerHTML = "<strong>Bold text</strong>";

// innerText - visible text (respects CSS)
element.innerText = "Visible text";
```

### Modifying Attributes
```javascript
let image = document.querySelector('img');

// Get attribute
let src = image.getAttribute('src');
console.log(src);

// Set attribute
image.setAttribute('src', 'new-image.jpg');
image.setAttribute('alt', 'New image description');

// Remove attribute
image.removeAttribute('alt');

// Direct property access
image.src = 'another-image.jpg';
image.className = 'new-class';
image.id = 'new-id';
```

### Modifying CSS Classes
```javascript
let element = document.getElementById('my-element');

// Add class
element.classList.add('new-class');
element.classList.add('class1', 'class2');

// Remove class
element.classList.remove('old-class');

// Toggle class
element.classList.toggle('active');

// Check if class exists
if (element.classList.contains('active')) {
    console.log('Element has active class');
}

// Replace class
element.classList.replace('old-class', 'new-class');
```

### Modifying Styles
```javascript
let element = document.getElementById('my-element');

// Set individual styles
element.style.color = 'red';
element.style.backgroundColor = 'blue';
element.style.fontSize = '20px';
element.style.display = 'none';

// Set multiple styles
element.style.cssText = 'color: red; background: blue; font-size: 20px;';

// Get computed styles
let computedStyle = window.getComputedStyle(element);
console.log(computedStyle.color);
```

---

## Creating and Removing Elements

### Creating Elements
```javascript
// Create new element
let newDiv = document.createElement('div');
let newParagraph = document.createElement('p');

// Set content
newDiv.textContent = 'New div content';
newParagraph.innerHTML = '<strong>New paragraph</strong>';

// Set attributes
newDiv.setAttribute('class', 'new-div');
newDiv.id = 'new-div-id';

// Append to parent
document.body.appendChild(newDiv);
document.body.appendChild(newParagraph);
```

### Inserting Elements
```javascript
let parent = document.getElementById('parent');
let newElement = document.createElement('p');
newElement.textContent = 'New paragraph';

// appendChild - adds to end
parent.appendChild(newElement);

// insertBefore - adds before specific element
let referenceElement = document.getElementById('reference');
parent.insertBefore(newElement, referenceElement);

// insertAdjacentHTML - insert HTML string
parent.insertAdjacentHTML('beforeend', '<p>New paragraph</p>');

// insertAdjacentElement - insert element
let anotherElement = document.createElement('span');
parent.insertAdjacentElement('afterbegin', anotherElement);
```

### Removing Elements
```javascript
let element = document.getElementById('to-remove');

// Remove element
element.remove();

// Remove child element
let parent = document.getElementById('parent');
let child = document.getElementById('child');
parent.removeChild(child);

// Replace element
let newElement = document.createElement('div');
let oldElement = document.getElementById('old');
parent.replaceChild(newElement, oldElement);
```

### Cloning Elements
```javascript
let originalElement = document.getElementById('original');

// Clone element
let clonedElement = originalElement.cloneNode(true); // true = deep clone
let shallowClone = originalElement.cloneNode(false); // false = shallow clone

// Modify cloned element
clonedElement.id = 'cloned';
clonedElement.textContent = 'Cloned content';

// Add to page
document.body.appendChild(clonedElement);
```

---

## Event Handling

### Adding Event Listeners
```javascript
let button = document.getElementById('my-button');

// addEventListener method
button.addEventListener('click', function() {
    console.log('Button clicked!');
});

// Arrow function
button.addEventListener('click', () => {
    console.log('Button clicked with arrow function!');
});

// Named function
function handleClick() {
    console.log('Button clicked with named function!');
}
button.addEventListener('click', handleClick);
```

### Event Object
```javascript
let button = document.getElementById('my-button');

button.addEventListener('click', function(event) {
    console.log('Event type:', event.type);
    console.log('Target element:', event.target);
    console.log('Current target:', event.currentTarget);
    console.log('Mouse coordinates:', event.clientX, event.clientY);
    
    // Prevent default behavior
    event.preventDefault();
    
    // Stop event propagation
    event.stopPropagation();
});
```

### Common Event Types
```javascript
let element = document.getElementById('my-element');

// Mouse events
element.addEventListener('click', handleClick);
element.addEventListener('dblclick', handleDoubleClick);
element.addEventListener('mousedown', handleMouseDown);
element.addEventListener('mouseup', handleMouseUp);
element.addEventListener('mouseover', handleMouseOver);
element.addEventListener('mouseout', handleMouseOut);

// Keyboard events
element.addEventListener('keydown', handleKeyDown);
element.addEventListener('keyup', handleKeyUp);
element.addEventListener('keypress', handleKeyPress);

// Form events
let form = document.getElementById('my-form');
form.addEventListener('submit', handleSubmit);
form.addEventListener('reset', handleReset);

// Input events
let input = document.getElementById('my-input');
input.addEventListener('input', handleInput);
input.addEventListener('change', handleChange);
input.addEventListener('focus', handleFocus);
input.addEventListener('blur', handleBlur);
```

### Event Delegation
```javascript
// Instead of adding listeners to each item
let list = document.getElementById('my-list');

// Add one listener to parent
list.addEventListener('click', function(event) {
    // Check if clicked element is a list item
    if (event.target.tagName === 'LI') {
        console.log('List item clicked:', event.target.textContent);
    }
});

// This works even for dynamically added items
function addListItem(text) {
    let li = document.createElement('li');
    li.textContent = text;
    list.appendChild(li);
    // No need to add event listener - delegation handles it
}
```

---

## Practice Exercises

### Exercise 1: Interactive List
```javascript
// HTML: <ul id="todo-list"></ul>
// HTML: <input id="todo-input" placeholder="Add todo">
// HTML: <button id="add-button">Add</button>

let todoList = document.getElementById('todo-list');
let todoInput = document.getElementById('todo-input');
let addButton = document.getElementById('add-button');

function addTodo() {
    let text = todoInput.value.trim();
    if (text) {
        let li = document.createElement('li');
        li.textContent = text;
        li.addEventListener('click', function() {
            li.classList.toggle('completed');
        });
        todoList.appendChild(li);
        todoInput.value = '';
    }
}

addButton.addEventListener('click', addTodo);
todoInput.addEventListener('keypress', function(event) {
    if (event.key === 'Enter') {
        addTodo();
    }
});
```

### Exercise 2: Dynamic Content
```javascript
// HTML: <div id="content"></div>
// HTML: <button id="load-button">Load Content</button>

let content = document.getElementById('content');
let loadButton = document.getElementById('load-button');

loadButton.addEventListener('click', function() {
    // Clear existing content
    content.innerHTML = '';
    
    // Create new content
    let title = document.createElement('h2');
    title.textContent = 'Dynamic Content';
    
    let paragraph = document.createElement('p');
    paragraph.textContent = 'This content was loaded dynamically!';
    
    let list = document.createElement('ul');
    for (let i = 1; i <= 3; i++) {
        let li = document.createElement('li');
        li.textContent = `Item ${i}`;
        list.appendChild(li);
    }
    
    // Add to content
    content.appendChild(title);
    content.appendChild(paragraph);
    content.appendChild(list);
});
```

### Exercise 3: Form Validation
```javascript
// HTML: <form id="contact-form">
// HTML: <input id="email" type="email" required>
// HTML: <input id="name" type="text" required>
// HTML: <button type="submit">Submit</button>
// HTML: </form>

let form = document.getElementById('contact-form');
let emailInput = document.getElementById('email');
let nameInput = document.getElementById('name');

function validateForm(event) {
    event.preventDefault();
    
    let isValid = true;
    let errors = [];
    
    // Validate name
    if (nameInput.value.trim().length < 2) {
        errors.push('Name must be at least 2 characters');
        isValid = false;
    }
    
    // Validate email
    let emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!emailPattern.test(emailInput.value)) {
        errors.push('Please enter a valid email');
        isValid = false;
    }
    
    // Display errors or success
    if (isValid) {
        alert('Form submitted successfully!');
        form.reset();
    } else {
        alert('Please fix the following errors:\n' + errors.join('\n'));
    }
}

form.addEventListener('submit', validateForm);
```

---

## Summary

### Key Concepts Learned
- ✅ **DOM Structure** - Understanding the document tree
- ✅ **Element Selection** - Finding HTML elements
- ✅ **Element Modification** - Changing content and attributes
- ✅ **Element Creation** - Adding new elements dynamically
- ✅ **Event Handling** - Responding to user interactions

### Best Practices
- Use `querySelector` and `querySelectorAll` for modern selection
- Use `textContent` for plain text, `innerHTML` for HTML
- Use event delegation for dynamic content
- Always validate user input
- Use `addEventListener` instead of inline event handlers

### Common Mistakes
- Confusing `textContent` with `innerHTML`
- Not handling events properly
- Creating memory leaks with event listeners
- Not validating user input
- Using outdated selection methods

---

**Excellent! You've mastered DOM manipulation. Ready for Events and Event Handling? 🚀**

**Next Tutorial:** [Events and Event Handling](./06-events-event-handling.md)
