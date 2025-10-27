# React Fundamentals - Tutorial 17 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [What is React?](#what-is-react)
3. [JSX and Components](#jsx-and-components)
4. [Props and State](#props-and-state)
5. [Event Handling](#event-handling)
6. [Lifecycle Methods](#lifecycle-methods)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

React is a **powerful JavaScript library** for building user interfaces, especially single-page applications. It's developed by Facebook and is one of the most popular frontend frameworks.

### What You'll Learn
- ✅ **React Basics** - Understanding React concepts
- ✅ **JSX** - JavaScript XML syntax
- ✅ **Components** - Building reusable UI components
- ✅ **Props and State** - Data flow in React
- ✅ **Event Handling** - User interactions
- ✅ **Lifecycle Methods** - Component lifecycle

---

## What is React?

### Why React?
- **Component-Based** - Reusable UI components
- **Virtual DOM** - Efficient updates and rendering
- **Unidirectional Data Flow** - Predictable state management
- **Rich Ecosystem** - Large community and tools
- **Declarative** - Describe what UI should look like

### React vs Vanilla JavaScript
```javascript
// Vanilla JavaScript
function createTaskElement(task) {
    const div = document.createElement('div');
    div.className = 'task';
    
    const title = document.createElement('h3');
    title.textContent = task.title;
    
    const description = document.createElement('p');
    description.textContent = task.description;
    
    div.appendChild(title);
    div.appendChild(description);
    
    return div;
}

// React
function Task({ task }) {
    return (
        <div className="task">
            <h3>{task.title}</h3>
            <p>{task.description}</p>
        </div>
    );
}
```

### Setting Up React
```html
<!-- HTML -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>React App</title>
    <script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
</head>
<body>
    <div id="root"></div>
    
    <script type="text/babel">
        // React code goes here
    </script>
</body>
</html>
```

---

## JSX and Components

### What is JSX?
JSX is a syntax extension that allows you to write HTML-like code in JavaScript. It makes React components more readable and easier to write.

```javascript
// JSX
const element = <h1>Hello, World!</h1>;

// This JSX is compiled to:
const element = React.createElement('h1', null, 'Hello, World!');
```

### Basic JSX
```javascript
// Variables in JSX
const name = 'John';
const element = <h1>Hello, {name}!</h1>;

// Expressions in JSX
const element = <h1>2 + 2 = {2 + 2}</h1>;

// Attributes in JSX
const element = <div className="container" id="main">Content</div>;

// Self-closing tags
const element = <img src="image.jpg" alt="Description" />;
```

### Creating Components
```javascript
// Function Component
function Welcome(props) {
    return <h1>Hello, {props.name}!</h1>;
}

// Arrow Function Component
const Welcome = (props) => {
    return <h1>Hello, {props.name}!</h1>;
};

// Class Component
class Welcome extends React.Component {
    render() {
        return <h1>Hello, {this.props.name}!</h1>;
    }
}

// Using components
const element = <Welcome name="Sara" />;
```

### Component Composition
```javascript
// Parent Component
function App() {
    return (
        <div>
            <Header />
            <Main />
            <Footer />
        </div>
    );
}

// Child Components
function Header() {
    return (
        <header>
            <h1>My App</h1>
            <nav>
                <a href="#home">Home</a>
                <a href="#about">About</a>
                <a href="#contact">Contact</a>
            </nav>
        </header>
    );
}

function Main() {
    return (
        <main>
            <h2>Welcome to My App</h2>
            <p>This is the main content area.</p>
        </main>
    );
}

function Footer() {
    return (
        <footer>
            <p>&copy; 2023 My App. All rights reserved.</p>
        </footer>
    );
}
```

---

## Props and State

### Props
Props are read-only data that are passed from parent to child components.

```javascript
// Parent Component
function App() {
    const user = {
        name: 'John Doe',
        email: 'john@example.com',
        age: 30
    };
    
    return (
        <div>
            <UserProfile user={user} />
            <UserSettings theme="dark" notifications={true} />
        </div>
    );
}

// Child Component
function UserProfile({ user }) {
    return (
        <div className="user-profile">
            <h2>{user.name}</h2>
            <p>Email: {user.email}</p>
            <p>Age: {user.age}</p>
        </div>
    );
}

// Multiple Props
function UserSettings({ theme, notifications }) {
    return (
        <div className="user-settings">
            <p>Theme: {theme}</p>
            <p>Notifications: {notifications ? 'Enabled' : 'Disabled'}</p>
        </div>
    );
}
```

### State
State is data that can change over time and is managed within a component.

```javascript
// Function Component with State (Hooks)
function Counter() {
    const [count, setCount] = React.useState(0);
    
    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
            <button onClick={() => setCount(count - 1)}>Decrement</button>
        </div>
    );
}

// Class Component with State
class Counter extends React.Component {
    constructor(props) {
        super(props);
        this.state = { count: 0 };
    }
    
    increment = () => {
        this.setState({ count: this.state.count + 1 });
    }
    
    decrement = () => {
        this.setState({ count: this.state.count - 1 });
    }
    
    render() {
        return (
            <div>
                <p>Count: {this.state.count}</p>
                <button onClick={this.increment}>Increment</button>
                <button onClick={this.decrement}>Decrement</button>
            </div>
        );
    }
}
```

### State Updates
```javascript
// Function Component
function TodoList() {
    const [todos, setTodos] = React.useState([]);
    const [inputValue, setInputValue] = React.useState('');
    
    const addTodo = () => {
        if (inputValue.trim()) {
            setTodos([...todos, { id: Date.now(), text: inputValue, completed: false }]);
            setInputValue('');
        }
    };
    
    const toggleTodo = (id) => {
        setTodos(todos.map(todo => 
            todo.id === id ? { ...todo, completed: !todo.completed } : todo
        ));
    };
    
    const deleteTodo = (id) => {
        setTodos(todos.filter(todo => todo.id !== id));
    };
    
    return (
        <div>
            <input 
                value={inputValue}
                onChange={(e) => setInputValue(e.target.value)}
                placeholder="Add a todo"
            />
            <button onClick={addTodo}>Add</button>
            <ul>
                {todos.map(todo => (
                    <li key={todo.id}>
                        <span 
                            style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}
                            onClick={() => toggleTodo(todo.id)}
                        >
                            {todo.text}
                        </span>
                        <button onClick={() => deleteTodo(todo.id)}>Delete</button>
                    </li>
                ))}
            </ul>
        </div>
    );
}
```

---

## Event Handling

### Basic Event Handling
```javascript
// Function Component
function Button() {
    const handleClick = () => {
        alert('Button clicked!');
    };
    
    return <button onClick={handleClick}>Click me</button>;
}

// Inline Event Handler
function Button() {
    return <button onClick={() => alert('Button clicked!')}>Click me</button>;
}

// Class Component
class Button extends React.Component {
    handleClick = () => {
        alert('Button clicked!');
    }
    
    render() {
        return <button onClick={this.handleClick}>Click me</button>;
    }
}
```

### Event Object
```javascript
function Input() {
    const handleChange = (event) => {
        console.log('Input value:', event.target.value);
    };
    
    const handleKeyPress = (event) => {
        if (event.key === 'Enter') {
            console.log('Enter pressed');
        }
    };
    
    return (
        <input 
            type="text"
            onChange={handleChange}
            onKeyPress={handleKeyPress}
            placeholder="Type something..."
        />
    );
}
```

### Form Handling
```javascript
function ContactForm() {
    const [formData, setFormData] = React.useState({
        name: '',
        email: '',
        message: ''
    });
    
    const handleChange = (event) => {
        const { name, value } = event.target;
        setFormData(prev => ({
            ...prev,
            [name]: value
        }));
    };
    
    const handleSubmit = (event) => {
        event.preventDefault();
        console.log('Form submitted:', formData);
        // Handle form submission
    };
    
    return (
        <form onSubmit={handleSubmit}>
            <div>
                <label>Name:</label>
                <input 
                    type="text"
                    name="name"
                    value={formData.name}
                    onChange={handleChange}
                />
            </div>
            <div>
                <label>Email:</label>
                <input 
                    type="email"
                    name="email"
                    value={formData.email}
                    onChange={handleChange}
                />
            </div>
            <div>
                <label>Message:</label>
                <textarea 
                    name="message"
                    value={formData.message}
                    onChange={handleChange}
                />
            </div>
            <button type="submit">Submit</button>
        </form>
    );
}
```

---

## Lifecycle Methods

### Class Component Lifecycle
```javascript
class LifecycleDemo extends React.Component {
    constructor(props) {
        super(props);
        this.state = { count: 0 };
        console.log('Constructor called');
    }
    
    componentDidMount() {
        console.log('Component mounted');
        // API calls, timers, etc.
    }
    
    componentDidUpdate(prevProps, prevState) {
        console.log('Component updated');
        console.log('Previous state:', prevState);
        console.log('Current state:', this.state);
    }
    
    componentWillUnmount() {
        console.log('Component will unmount');
        // Cleanup timers, event listeners, etc.
    }
    
    render() {
        console.log('Render called');
        return (
            <div>
                <p>Count: {this.state.count}</p>
                <button onClick={() => this.setState({ count: this.state.count + 1 })}>
                    Increment
                </button>
            </div>
        );
    }
}
```

### useEffect Hook
```javascript
function LifecycleDemo() {
    const [count, setCount] = React.useState(0);
    
    // componentDidMount
    React.useEffect(() => {
        console.log('Component mounted');
        return () => {
            console.log('Component will unmount');
        };
    }, []);
    
    // componentDidUpdate
    React.useEffect(() => {
        console.log('Component updated');
        console.log('Count:', count);
    }, [count]);
    
    // componentDidMount + componentDidUpdate
    React.useEffect(() => {
        console.log('Component mounted or updated');
    });
    
    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
        </div>
    );
}
```

### Data Fetching
```javascript
function UserProfile({ userId }) {
    const [user, setUser] = React.useState(null);
    const [loading, setLoading] = React.useState(true);
    const [error, setError] = React.useState(null);
    
    React.useEffect(() => {
        const fetchUser = async () => {
            try {
                setLoading(true);
                const response = await fetch(`/api/users/${userId}`);
                const userData = await response.json();
                setUser(userData);
            } catch (err) {
                setError(err.message);
            } finally {
                setLoading(false);
            }
        };
        
        fetchUser();
    }, [userId]);
    
    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    if (!user) return <div>User not found</div>;
    
    return (
        <div>
            <h2>{user.name}</h2>
            <p>Email: {user.email}</p>
            <p>Age: {user.age}</p>
        </div>
    );
}
```

---

## Practice Exercises

### Exercise 1: Todo App
```javascript
function TodoApp() {
    const [todos, setTodos] = React.useState([]);
    const [inputValue, setInputValue] = React.useState('');
    const [filter, setFilter] = React.useState('all');
    
    const addTodo = () => {
        if (inputValue.trim()) {
            const newTodo = {
                id: Date.now(),
                text: inputValue,
                completed: false,
                createdAt: new Date()
            };
            setTodos([...todos, newTodo]);
            setInputValue('');
        }
    };
    
    const toggleTodo = (id) => {
        setTodos(todos.map(todo => 
            todo.id === id ? { ...todo, completed: !todo.completed } : todo
        ));
    };
    
    const deleteTodo = (id) => {
        setTodos(todos.filter(todo => todo.id !== id));
    };
    
    const filteredTodos = todos.filter(todo => {
        if (filter === 'active') return !todo.completed;
        if (filter === 'completed') return todo.completed;
        return true;
    });
    
    return (
        <div className="todo-app">
            <h1>Todo App</h1>
            
            <div className="add-todo">
                <input 
                    value={inputValue}
                    onChange={(e) => setInputValue(e.target.value)}
                    onKeyPress={(e) => e.key === 'Enter' && addTodo()}
                    placeholder="Add a todo..."
                />
                <button onClick={addTodo}>Add</button>
            </div>
            
            <div className="filters">
                <button 
                    className={filter === 'all' ? 'active' : ''}
                    onClick={() => setFilter('all')}
                >
                    All
                </button>
                <button 
                    className={filter === 'active' ? 'active' : ''}
                    onClick={() => setFilter('active')}
                >
                    Active
                </button>
                <button 
                    className={filter === 'completed' ? 'active' : ''}
                    onClick={() => setFilter('completed')}
                >
                    Completed
                </button>
            </div>
            
            <ul className="todo-list">
                {filteredTodos.map(todo => (
                    <li key={todo.id} className={todo.completed ? 'completed' : ''}>
                        <input 
                            type="checkbox"
                            checked={todo.completed}
                            onChange={() => toggleTodo(todo.id)}
                        />
                        <span onClick={() => toggleTodo(todo.id)}>
                            {todo.text}
                        </span>
                        <button onClick={() => deleteTodo(todo.id)}>Delete</button>
                    </li>
                ))}
            </ul>
            
            <div className="stats">
                <p>Total: {todos.length}</p>
                <p>Active: {todos.filter(t => !t.completed).length}</p>
                <p>Completed: {todos.filter(t => t.completed).length}</p>
            </div>
        </div>
    );
}
```

### Exercise 2: Weather App
```javascript
function WeatherApp() {
    const [weather, setWeather] = React.useState(null);
    const [loading, setLoading] = React.useState(false);
    const [error, setError] = React.useState(null);
    const [city, setCity] = React.useState('');
    
    const fetchWeather = async (cityName) => {
        if (!cityName) return;
        
        setLoading(true);
        setError(null);
        
        try {
            const response = await fetch(
                `https://api.openweathermap.org/data/2.5/weather?q=${cityName}&appid=YOUR_API_KEY&units=metric`
            );
            
            if (!response.ok) {
                throw new Error('City not found');
            }
            
            const data = await response.json();
            setWeather(data);
        } catch (err) {
            setError(err.message);
        } finally {
            setLoading(false);
        }
    };
    
    const handleSubmit = (e) => {
        e.preventDefault();
        fetchWeather(city);
    };
    
    return (
        <div className="weather-app">
            <h1>Weather App</h1>
            
            <form onSubmit={handleSubmit}>
                <input 
                    type="text"
                    value={city}
                    onChange={(e) => setCity(e.target.value)}
                    placeholder="Enter city name"
                />
                <button type="submit" disabled={loading}>
                    {loading ? 'Loading...' : 'Get Weather'}
                </button>
            </form>
            
            {error && <div className="error">Error: {error}</div>}
            
            {weather && (
                <div className="weather-info">
                    <h2>{weather.name}, {weather.sys.country}</h2>
                    <div className="weather-main">
                        <img 
                            src={`https://openweathermap.org/img/wn/${weather.weather[0].icon}@2x.png`}
                            alt={weather.weather[0].description}
                        />
                        <div>
                            <p className="temperature">{Math.round(weather.main.temp)}°C</p>
                            <p className="description">{weather.weather[0].description}</p>
                        </div>
                    </div>
                    <div className="weather-details">
                        <p>Feels like: {Math.round(weather.main.feels_like)}°C</p>
                        <p>Humidity: {weather.main.humidity}%</p>
                        <p>Wind: {weather.wind.speed} m/s</p>
                    </div>
                </div>
            )}
        </div>
    );
}
```

### Exercise 3: Counter with History
```javascript
function CounterWithHistory() {
    const [count, setCount] = React.useState(0);
    const [history, setHistory] = React.useState([0]);
    
    const increment = () => {
        const newCount = count + 1;
        setCount(newCount);
        setHistory([...history, newCount]);
    };
    
    const decrement = () => {
        const newCount = count - 1;
        setCount(newCount);
        setHistory([...history, newCount]);
    };
    
    const reset = () => {
        setCount(0);
        setHistory([...history, 0]);
    };
    
    const undo = () => {
        if (history.length > 1) {
            const newHistory = history.slice(0, -1);
            setHistory(newHistory);
            setCount(newHistory[newHistory.length - 1]);
        }
    };
    
    return (
        <div className="counter">
            <h2>Counter: {count}</h2>
            
            <div className="controls">
                <button onClick={decrement}>-</button>
                <button onClick={increment}>+</button>
                <button onClick={reset}>Reset</button>
                <button onClick={undo} disabled={history.length <= 1}>
                    Undo
                </button>
            </div>
            
            <div className="history">
                <h3>History:</h3>
                <ul>
                    {history.map((value, index) => (
                        <li key={index} className={index === history.length - 1 ? 'current' : ''}>
                            {value}
                        </li>
                    ))}
                </ul>
            </div>
        </div>
    );
}
```

---

## Summary

### Key Concepts Learned
- ✅ **React Basics** - Understanding React concepts and benefits
- ✅ **JSX** - JavaScript XML syntax for components
- ✅ **Components** - Building reusable UI components
- ✅ **Props and State** - Data flow and state management
- ✅ **Event Handling** - User interactions and form handling
- ✅ **Lifecycle Methods** - Component lifecycle and useEffect

### Best Practices
- Use functional components with hooks
- Keep components small and focused
- Use meaningful prop and state names
- Handle events properly
- Use useEffect for side effects
- Keep state as local as possible

### Common Mistakes
- Mutating state directly
- Not using keys in lists
- Forgetting to handle loading states
- Not cleaning up side effects
- Overusing useEffect

---

**Excellent! You've mastered React fundamentals. Ready for React Hooks? 🚀**

**Next Tutorial:** [React Hooks](./18-react-hooks.md)
