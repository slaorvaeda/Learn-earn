# Events and Event Handling - Tutorial 6 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Event Types](#event-types)
3. [Event Object](#event-object)
4. [Event Propagation](#event-propagation)
5. [Event Delegation](#event-delegation)
6. [Custom Events](#custom-events)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Events are **user interactions** or system occurrences that JavaScript can detect and respond to, making web pages interactive and dynamic.

### What You'll Learn
- ✅ **Event Types** - Mouse, keyboard, form, and other events
- ✅ **Event Object** - Properties and methods of event objects
- ✅ **Event Propagation** - Bubbling and capturing phases
- ✅ **Event Delegation** - Efficient event handling
- ✅ **Custom Events** - Creating and dispatching custom events

---

## Event Types

### Mouse Events
```javascript
let element = document.getElementById('my-element');

// Click events
element.addEventListener('click', function(event) {
    console.log('Element clicked');
});

element.addEventListener('dblclick', function(event) {
    console.log('Element double-clicked');
});

// Mouse movement events
element.addEventListener('mousedown', function(event) {
    console.log('Mouse button pressed down');
});

element.addEventListener('mouseup', function(event) {
    console.log('Mouse button released');
});

element.addEventListener('mouseover', function(event) {
    console.log('Mouse entered element');
});

element.addEventListener('mouseout', function(event) {
    console.log('Mouse left element');
});

element.addEventListener('mousemove', function(event) {
    console.log('Mouse moved over element');
});
```

### Keyboard Events
```javascript
let input = document.getElementById('my-input');

input.addEventListener('keydown', function(event) {
    console.log('Key pressed:', event.key);
    console.log('Key code:', event.code);
    console.log('Ctrl pressed:', event.ctrlKey);
    console.log('Shift pressed:', event.shiftKey);
});

input.addEventListener('keyup', function(event) {
    console.log('Key released:', event.key);
});

input.addEventListener('keypress', function(event) {
    console.log('Key pressed (character):', event.key);
});

// Handle specific keys
input.addEventListener('keydown', function(event) {
    if (event.key === 'Enter') {
        console.log('Enter key pressed');
    }
    if (event.key === 'Escape') {
        console.log('Escape key pressed');
    }
});
```

### Form Events
```javascript
let form = document.getElementById('my-form');

form.addEventListener('submit', function(event) {
    event.preventDefault(); // Prevent form submission
    console.log('Form submitted');
});

form.addEventListener('reset', function(event) {
    console.log('Form reset');
});

let input = document.getElementById('my-input');

input.addEventListener('input', function(event) {
    console.log('Input value changed:', event.target.value);
});

input.addEventListener('change', function(event) {
    console.log('Input value changed and lost focus:', event.target.value);
});

input.addEventListener('focus', function(event) {
    console.log('Input focused');
});

input.addEventListener('blur', function(event) {
    console.log('Input lost focus');
});
```

### Window Events
```javascript
// Page load events
window.addEventListener('load', function(event) {
    console.log('Page fully loaded');
});

window.addEventListener('DOMContentLoaded', function(event) {
    console.log('DOM content loaded');
});

// Window resize
window.addEventListener('resize', function(event) {
    console.log('Window resized');
});

// Window scroll
window.addEventListener('scroll', function(event) {
    console.log('Page scrolled');
});

// Page visibility
document.addEventListener('visibilitychange', function(event) {
    if (document.hidden) {
        console.log('Page hidden');
    } else {
        console.log('Page visible');
    }
});
```

---

## Event Object

### Event Properties
```javascript
let button = document.getElementById('my-button');

button.addEventListener('click', function(event) {
    // Event type
    console.log('Event type:', event.type);
    
    // Target element
    console.log('Target:', event.target);
    console.log('Current target:', event.currentTarget);
    
    // Mouse coordinates (for mouse events)
    console.log('Client X:', event.clientX);
    console.log('Client Y:', event.clientY);
    console.log('Page X:', event.pageX);
    console.log('Page Y:', event.pageY);
    
    // Keyboard properties (for keyboard events)
    console.log('Key:', event.key);
    console.log('Code:', event.code);
    console.log('Ctrl key:', event.ctrlKey);
    console.log('Shift key:', event.shiftKey);
    console.log('Alt key:', event.altKey);
    
    // Time
    console.log('Time stamp:', event.timeStamp);
});
```

### Event Methods
```javascript
let link = document.getElementById('my-link');

link.addEventListener('click', function(event) {
    // Prevent default behavior
    event.preventDefault();
    console.log('Default behavior prevented');
});

let parent = document.getElementById('parent');
let child = document.getElementById('child');

parent.addEventListener('click', function(event) {
    console.log('Parent clicked');
    
    // Stop event propagation
    event.stopPropagation();
});

child.addEventListener('click', function(event) {
    console.log('Child clicked');
    // This won't trigger parent's click handler due to stopPropagation
});
```

### Event Target vs Current Target
```javascript
let parent = document.getElementById('parent');
let child = document.getElementById('child');

parent.addEventListener('click', function(event) {
    console.log('Target (clicked element):', event.target.id);
    console.log('Current target (event listener element):', event.currentTarget.id);
    
    // Check if target is the same as current target
    if (event.target === event.currentTarget) {
        console.log('Direct click on parent');
    } else {
        console.log('Click bubbled from child');
    }
});
```

---

## Event Propagation

### Event Bubbling
```javascript
// HTML structure:
// <div id="grandparent">
//   <div id="parent">
//     <div id="child">Click me</div>
//   </div>
// </div>

let grandparent = document.getElementById('grandparent');
let parent = document.getElementById('parent');
let child = document.getElementById('child');

// Event bubbling (default)
grandparent.addEventListener('click', function(event) {
    console.log('Grandparent clicked');
});

parent.addEventListener('click', function(event) {
    console.log('Parent clicked');
});

child.addEventListener('click', function(event) {
    console.log('Child clicked');
});

// When child is clicked, output will be:
// Child clicked
// Parent clicked
// Grandparent clicked
```

### Event Capturing
```javascript
// Event capturing (useCapture = true)
grandparent.addEventListener('click', function(event) {
    console.log('Grandparent captured');
}, true);

parent.addEventListener('click', function(event) {
    console.log('Parent captured');
}, true);

child.addEventListener('click', function(event) {
    console.log('Child captured');
}, true);

// When child is clicked, output will be:
// Grandparent captured
// Parent captured
// Child captured
// Child clicked
// Parent clicked
// Grandparent clicked
```

### Stopping Propagation
```javascript
child.addEventListener('click', function(event) {
    console.log('Child clicked');
    event.stopPropagation(); // Stop bubbling
});

parent.addEventListener('click', function(event) {
    console.log('Parent clicked');
    event.stopImmediatePropagation(); // Stop all propagation
});

// Only "Child clicked" will be logged
```

---

## Event Delegation

### Basic Event Delegation
```javascript
// Instead of adding listeners to each list item
let list = document.getElementById('my-list');

// Add one listener to the parent
list.addEventListener('click', function(event) {
    // Check if clicked element is a list item
    if (event.target.tagName === 'LI') {
        console.log('List item clicked:', event.target.textContent);
        event.target.classList.toggle('selected');
    }
});

// This works for dynamically added items too
function addListItem(text) {
    let li = document.createElement('li');
    li.textContent = text;
    list.appendChild(li);
    // No need to add event listener - delegation handles it
}
```

### Advanced Event Delegation
```javascript
let container = document.getElementById('container');

container.addEventListener('click', function(event) {
    let target = event.target;
    
    // Handle different types of clicks
    if (target.classList.contains('button')) {
        handleButtonClick(target);
    } else if (target.classList.contains('link')) {
        handleLinkClick(target);
    } else if (target.tagName === 'IMG') {
        handleImageClick(target);
    }
});

function handleButtonClick(button) {
    console.log('Button clicked:', button.textContent);
}

function handleLinkClick(link) {
    console.log('Link clicked:', link.href);
}

function handleImageClick(img) {
    console.log('Image clicked:', img.src);
}
```

### Event Delegation with Data Attributes
```javascript
let taskList = document.getElementById('task-list');

taskList.addEventListener('click', function(event) {
    let target = event.target;
    let taskId = target.dataset.taskId;
    let action = target.dataset.action;
    
    if (taskId && action) {
        switch(action) {
            case 'complete':
                completeTask(taskId);
                break;
            case 'delete':
                deleteTask(taskId);
                break;
            case 'edit':
                editTask(taskId);
                break;
        }
    }
});

function completeTask(taskId) {
    console.log('Completing task:', taskId);
}

function deleteTask(taskId) {
    console.log('Deleting task:', taskId);
}

function editTask(taskId) {
    console.log('Editing task:', taskId);
}
```

---

## Custom Events

### Creating Custom Events
```javascript
// Create custom event
let customEvent = new CustomEvent('myCustomEvent', {
    detail: {
        message: 'Hello from custom event',
        timestamp: Date.now()
    }
});

// Dispatch custom event
let element = document.getElementById('my-element');
element.dispatchEvent(customEvent);

// Listen for custom event
element.addEventListener('myCustomEvent', function(event) {
    console.log('Custom event received:', event.detail.message);
});
```

### Custom Event Classes
```javascript
// Create custom event class
class TaskEvent extends CustomEvent {
    constructor(taskId, action, data = {}) {
        super('taskEvent', {
            detail: {
                taskId: taskId,
                action: action,
                data: data,
                timestamp: Date.now()
            }
        });
    }
}

// Use custom event
let taskElement = document.getElementById('task');

taskElement.addEventListener('taskEvent', function(event) {
    let { taskId, action, data } = event.detail;
    console.log(`Task ${taskId}: ${action}`, data);
});

// Dispatch custom event
let taskEvent = new TaskEvent('123', 'completed', { completedAt: Date.now() });
taskElement.dispatchEvent(taskEvent);
```

### Event Bus Pattern
```javascript
class EventBus {
    constructor() {
        this.events = {};
    }
    
    on(event, callback) {
        if (!this.events[event]) {
            this.events[event] = [];
        }
        this.events[event].push(callback);
    }
    
    off(event, callback) {
        if (this.events[event]) {
            this.events[event] = this.events[event].filter(cb => cb !== callback);
        }
    }
    
    emit(event, data) {
        if (this.events[event]) {
            this.events[event].forEach(callback => callback(data));
        }
    }
}

// Use event bus
let eventBus = new EventBus();

// Subscribe to events
eventBus.on('userLogin', function(user) {
    console.log('User logged in:', user.name);
});

eventBus.on('userLogout', function(user) {
    console.log('User logged out:', user.name);
});

// Emit events
eventBus.emit('userLogin', { name: 'John', id: 123 });
eventBus.emit('userLogout', { name: 'John', id: 123 });
```

---

## Practice Exercises

### Exercise 1: Interactive Counter
```javascript
// HTML: <div id="counter">0</div>
// HTML: <button id="increment">+</button>
// HTML: <button id="decrement">-</button>
// HTML: <button id="reset">Reset</button>

let counter = document.getElementById('counter');
let incrementBtn = document.getElementById('increment');
let decrementBtn = document.getElementById('decrement');
let resetBtn = document.getElementById('reset');

let count = 0;

function updateCounter() {
    counter.textContent = count;
}

incrementBtn.addEventListener('click', function() {
    count++;
    updateCounter();
});

decrementBtn.addEventListener('click', function() {
    count--;
    updateCounter();
});

resetBtn.addEventListener('click', function() {
    count = 0;
    updateCounter();
});

// Keyboard support
document.addEventListener('keydown', function(event) {
    if (event.key === '+') {
        count++;
        updateCounter();
    } else if (event.key === '-') {
        count--;
        updateCounter();
    } else if (event.key === 'r' || event.key === 'R') {
        count = 0;
        updateCounter();
    }
});
```

### Exercise 2: Drag and Drop
```javascript
// HTML: <div id="draggable" draggable="true">Drag me</div>
// HTML: <div id="dropzone">Drop here</div>

let draggable = document.getElementById('draggable');
let dropzone = document.getElementById('dropzone');

draggable.addEventListener('dragstart', function(event) {
    event.dataTransfer.setData('text/plain', event.target.id);
    event.target.style.opacity = '0.5';
});

draggable.addEventListener('dragend', function(event) {
    event.target.style.opacity = '1';
});

dropzone.addEventListener('dragover', function(event) {
    event.preventDefault();
    event.target.style.backgroundColor = 'lightblue';
});

dropzone.addEventListener('dragleave', function(event) {
    event.target.style.backgroundColor = '';
});

dropzone.addEventListener('drop', function(event) {
    event.preventDefault();
    let data = event.dataTransfer.getData('text/plain');
    let draggedElement = document.getElementById(data);
    
    event.target.appendChild(draggedElement);
    event.target.style.backgroundColor = '';
});
```

### Exercise 3: Form Validation with Events
```javascript
// HTML: <form id="signup-form">
// HTML: <input id="username" type="text" placeholder="Username">
// HTML: <input id="email" type="email" placeholder="Email">
// HTML: <input id="password" type="password" placeholder="Password">
// HTML: <button type="submit">Sign Up</button>
// HTML: </form>

let form = document.getElementById('signup-form');
let username = document.getElementById('username');
let email = document.getElementById('email');
let password = document.getElementById('password');

// Real-time validation
username.addEventListener('input', function(event) {
    let value = event.target.value;
    if (value.length < 3) {
        showError(username, 'Username must be at least 3 characters');
    } else {
        clearError(username);
    }
});

email.addEventListener('blur', function(event) {
    let value = event.target.value;
    let emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!emailPattern.test(value)) {
        showError(email, 'Please enter a valid email');
    } else {
        clearError(email);
    }
});

password.addEventListener('input', function(event) {
    let value = event.target.value;
    if (value.length < 8) {
        showError(password, 'Password must be at least 8 characters');
    } else {
        clearError(password);
    }
});

form.addEventListener('submit', function(event) {
    event.preventDefault();
    
    let isValid = true;
    let errors = [];
    
    // Validate all fields
    if (username.value.length < 3) {
        errors.push('Username must be at least 3 characters');
        isValid = false;
    }
    
    let emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!emailPattern.test(email.value)) {
        errors.push('Please enter a valid email');
        isValid = false;
    }
    
    if (password.value.length < 8) {
        errors.push('Password must be at least 8 characters');
        isValid = false;
    }
    
    if (isValid) {
        alert('Form submitted successfully!');
        form.reset();
    } else {
        alert('Please fix the following errors:\n' + errors.join('\n'));
    }
});

function showError(input, message) {
    clearError(input);
    let errorDiv = document.createElement('div');
    errorDiv.className = 'error-message';
    errorDiv.textContent = message;
    errorDiv.style.color = 'red';
    errorDiv.style.fontSize = '12px';
    input.parentNode.appendChild(errorDiv);
}

function clearError(input) {
    let errorDiv = input.parentNode.querySelector('.error-message');
    if (errorDiv) {
        errorDiv.remove();
    }
}
```

---

## Summary

### Key Concepts Learned
- ✅ **Event Types** - Mouse, keyboard, form, and window events
- ✅ **Event Object** - Properties and methods for event handling
- ✅ **Event Propagation** - Bubbling and capturing phases
- ✅ **Event Delegation** - Efficient event handling for dynamic content
- ✅ **Custom Events** - Creating and dispatching custom events

### Best Practices
- Use event delegation for dynamic content
- Always prevent default behavior when needed
- Use `addEventListener` instead of inline event handlers
- Clean up event listeners to prevent memory leaks
- Use custom events for complex application communication

### Common Mistakes
- Not preventing default behavior when needed
- Creating memory leaks with event listeners
- Not understanding event propagation
- Using inline event handlers instead of `addEventListener`
- Not handling edge cases in event validation

---

**Fantastic! You've mastered events and event handling. Ready for Error Handling? 🚀**

**Next Tutorial:** [Error Handling](./07-error-handling.md)
