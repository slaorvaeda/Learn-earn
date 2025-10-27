# React Hooks - Tutorial 18 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [useState Hook](#usestate-hook)
3. [useEffect Hook](#useeffect-hook)
4. [useContext Hook](#usecontext-hook)
5. [useReducer Hook](#usereducer-hook)
6. [Custom Hooks](#custom-hooks)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

React Hooks are **functions that let you use state and other React features** in functional components. They were introduced in React 16.8 and revolutionized how we write React components.

### What You'll Learn
- ✅ **useState** - Managing component state
- ✅ **useEffect** - Side effects and lifecycle
- ✅ **useContext** - Sharing data across components
- ✅ **useReducer** - Complex state management
- ✅ **Custom Hooks** - Reusable logic

---

## useState Hook

### Basic useState
```javascript
import React, { useState } from 'react';

function Counter() {
    const [count, setCount] = useState(0);
    
    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
            <button onClick={() => setCount(count - 1)}>Decrement</button>
        </div>
    );
}
```

### Multiple State Variables
```javascript
function UserProfile() {
    const [name, setName] = useState('');
    const [email, setEmail] = useState('');
    const [age, setAge] = useState(0);
    
    return (
        <div>
            <input 
                value={name}
                onChange={(e) => setName(e.target.value)}
                placeholder="Name"
            />
            <input 
                value={email}
                onChange={(e) => setEmail(e.target.value)}
                placeholder="Email"
            />
            <input 
                type="number"
                value={age}
                onChange={(e) => setAge(parseInt(e.target.value) || 0)}
                placeholder="Age"
            />
        </div>
    );
}
```

### Object State
```javascript
function UserForm() {
    const [user, setUser] = useState({
        name: '',
        email: '',
        age: 0
    });
    
    const updateUser = (field, value) => {
        setUser(prevUser => ({
            ...prevUser,
            [field]: value
        }));
    };
    
    return (
        <div>
            <input 
                value={user.name}
                onChange={(e) => updateUser('name', e.target.value)}
                placeholder="Name"
            />
            <input 
                value={user.email}
                onChange={(e) => updateUser('email', e.target.value)}
                placeholder="Email"
            />
            <input 
                type="number"
                value={user.age}
                onChange={(e) => updateUser('age', parseInt(e.target.value) || 0)}
                placeholder="Age"
            />
        </div>
    );
}
```

### Array State
```javascript
function TodoList() {
    const [todos, setTodos] = useState([]);
    const [inputValue, setInputValue] = useState('');
    
    const addTodo = () => {
        if (inputValue.trim()) {
            setTodos(prevTodos => [...prevTodos, {
                id: Date.now(),
                text: inputValue,
                completed: false
            }]);
            setInputValue('');
        }
    };
    
    const toggleTodo = (id) => {
        setTodos(prevTodos => 
            prevTodos.map(todo => 
                todo.id === id ? { ...todo, completed: !todo.completed } : todo
            )
        );
    };
    
    const deleteTodo = (id) => {
        setTodos(prevTodos => prevTodos.filter(todo => todo.id !== id));
    };
    
    return (
        <div>
            <input 
                value={inputValue}
                onChange={(e) => setInputValue(e.target.value)}
                onKeyPress={(e) => e.key === 'Enter' && addTodo()}
                placeholder="Add a todo"
            />
            <button onClick={addTodo}>Add</button>
            
            <ul>
                {todos.map(todo => (
                    <li key={todo.id}>
                        <input 
                            type="checkbox"
                            checked={todo.completed}
                            onChange={() => toggleTodo(todo.id)}
                        />
                        <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
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

## useEffect Hook

### Basic useEffect
```javascript
import React, { useState, useEffect } from 'react';

function DataFetcher() {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    
    useEffect(() => {
        const fetchData = async () => {
            try {
                setLoading(true);
                const response = await fetch('https://api.example.com/data');
                const result = await response.json();
                setData(result);
            } catch (err) {
                setError(err.message);
            } finally {
                setLoading(false);
            }
        };
        
        fetchData();
    }, []); // Empty dependency array means this runs once on mount
    
    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    if (!data) return <div>No data found</div>;
    
    return <div>Data: {JSON.stringify(data)}</div>;
}
```

### useEffect with Dependencies
```javascript
function UserProfile({ userId }) {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);
    
    useEffect(() => {
        const fetchUser = async () => {
            setLoading(true);
            try {
                const response = await fetch(`/api/users/${userId}`);
                const userData = await response.json();
                setUser(userData);
            } catch (error) {
                console.error('Failed to fetch user:', error);
            } finally {
                setLoading(false);
            }
        };
        
        fetchUser();
    }, [userId]); // Runs when userId changes
    
    if (loading) return <div>Loading user...</div>;
    if (!user) return <div>User not found</div>;
    
    return (
        <div>
            <h2>{user.name}</h2>
            <p>Email: {user.email}</p>
        </div>
    );
}
```

### Cleanup in useEffect
```javascript
function Timer() {
    const [count, setCount] = useState(0);
    
    useEffect(() => {
        const interval = setInterval(() => {
            setCount(prevCount => prevCount + 1);
        }, 1000);
        
        // Cleanup function
        return () => {
            clearInterval(interval);
        };
    }, []); // Empty dependency array means this runs once on mount
    
    return <div>Count: {count}</div>;
}
```

### Multiple useEffect Hooks
```javascript
function UserDashboard({ userId }) {
    const [user, setUser] = useState(null);
    const [posts, setPosts] = useState([]);
    const [loading, setLoading] = useState(true);
    
    // Fetch user data
    useEffect(() => {
        const fetchUser = async () => {
            try {
                const response = await fetch(`/api/users/${userId}`);
                const userData = await response.json();
                setUser(userData);
            } catch (error) {
                console.error('Failed to fetch user:', error);
            }
        };
        
        fetchUser();
    }, [userId]);
    
    // Fetch user posts
    useEffect(() => {
        const fetchPosts = async () => {
            try {
                const response = await fetch(`/api/users/${userId}/posts`);
                const postsData = await response.json();
                setPosts(postsData);
            } catch (error) {
                console.error('Failed to fetch posts:', error);
            } finally {
                setLoading(false);
            }
        };
        
        if (userId) {
            fetchPosts();
        }
    }, [userId]);
    
    // Update document title
    useEffect(() => {
        if (user) {
            document.title = `${user.name} - Dashboard`;
        }
        
        return () => {
            document.title = 'Dashboard';
        };
    }, [user]);
    
    if (loading) return <div>Loading...</div>;
    
    return (
        <div>
            <h1>Welcome, {user?.name}</h1>
            <div>
                <h2>Posts</h2>
                {posts.map(post => (
                    <div key={post.id}>
                        <h3>{post.title}</h3>
                        <p>{post.content}</p>
                    </div>
                ))}
            </div>
        </div>
    );
}
```

---

## useContext Hook

### Creating Context
```javascript
import React, { createContext, useContext, useState } from 'react';

// Create context
const ThemeContext = createContext();

// Provider component
function ThemeProvider({ children }) {
    const [theme, setTheme] = useState('light');
    
    const toggleTheme = () => {
        setTheme(prevTheme => prevTheme === 'light' ? 'dark' : 'light');
    };
    
    return (
        <ThemeContext.Provider value={{ theme, toggleTheme }}>
            {children}
        </ThemeContext.Provider>
    );
}

// Custom hook to use context
function useTheme() {
    const context = useContext(ThemeContext);
    if (!context) {
        throw new Error('useTheme must be used within a ThemeProvider');
    }
    return context;
}

// Components using context
function App() {
    return (
        <ThemeProvider>
            <Header />
            <Main />
        </ThemeProvider>
    );
}

function Header() {
    const { theme, toggleTheme } = useTheme();
    
    return (
        <header className={`header ${theme}`}>
            <h1>My App</h1>
            <button onClick={toggleTheme}>
                Switch to {theme === 'light' ? 'dark' : 'light'} theme
            </button>
        </header>
    );
}

function Main() {
    const { theme } = useTheme();
    
    return (
        <main className={`main ${theme}`}>
            <p>Current theme: {theme}</p>
        </main>
    );
}
```

### Multiple Contexts
```javascript
// User Context
const UserContext = createContext();

function UserProvider({ children }) {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);
    
    useEffect(() => {
        // Simulate fetching user data
        setTimeout(() => {
            setUser({ id: 1, name: 'John Doe', email: 'john@example.com' });
            setLoading(false);
        }, 1000);
    }, []);
    
    const login = (userData) => {
        setUser(userData);
    };
    
    const logout = () => {
        setUser(null);
    };
    
    return (
        <UserContext.Provider value={{ user, loading, login, logout }}>
            {children}
        </UserContext.Provider>
    );
}

// Settings Context
const SettingsContext = createContext();

function SettingsProvider({ children }) {
    const [settings, setSettings] = useState({
        notifications: true,
        language: 'en',
        timezone: 'UTC'
    });
    
    const updateSetting = (key, value) => {
        setSettings(prev => ({ ...prev, [key]: value }));
    };
    
    return (
        <SettingsContext.Provider value={{ settings, updateSetting }}>
            {children}
        </SettingsContext.Provider>
    );
}

// App with multiple providers
function App() {
    return (
        <UserProvider>
            <SettingsProvider>
                <ThemeProvider>
                    <Dashboard />
                </ThemeProvider>
            </SettingsProvider>
        </UserProvider>
    );
}

// Component using multiple contexts
function Dashboard() {
    const { user, loading } = useContext(UserContext);
    const { settings } = useContext(SettingsContext);
    const { theme } = useContext(ThemeContext);
    
    if (loading) return <div>Loading...</div>;
    
    return (
        <div className={`dashboard ${theme}`}>
            <h1>Welcome, {user?.name}</h1>
            <p>Notifications: {settings.notifications ? 'On' : 'Off'}</p>
            <p>Language: {settings.language}</p>
        </div>
    );
}
```

---

## useReducer Hook

### Basic useReducer
```javascript
import React, { useReducer } from 'react';

// Reducer function
function counterReducer(state, action) {
    switch (action.type) {
        case 'increment':
            return { count: state.count + 1 };
        case 'decrement':
            return { count: state.count - 1 };
        case 'reset':
            return { count: 0 };
        case 'set':
            return { count: action.value };
        default:
            throw new Error(`Unhandled action type: ${action.type}`);
    }
}

function Counter() {
    const [state, dispatch] = useReducer(counterReducer, { count: 0 });
    
    return (
        <div>
            <p>Count: {state.count}</p>
            <button onClick={() => dispatch({ type: 'increment' })}>+</button>
            <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
            <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
            <button onClick={() => dispatch({ type: 'set', value: 10 })}>Set to 10</button>
        </div>
    );
}
```

### Complex State with useReducer
```javascript
// Todo reducer
function todoReducer(state, action) {
    switch (action.type) {
        case 'ADD_TODO':
            return {
                ...state,
                todos: [...state.todos, {
                    id: Date.now(),
                    text: action.text,
                    completed: false
                }]
            };
        case 'TOGGLE_TODO':
            return {
                ...state,
                todos: state.todos.map(todo =>
                    todo.id === action.id ? { ...todo, completed: !todo.completed } : todo
                )
            };
        case 'DELETE_TODO':
            return {
                ...state,
                todos: state.todos.filter(todo => todo.id !== action.id)
            };
        case 'SET_FILTER':
            return {
                ...state,
                filter: action.filter
            };
        case 'CLEAR_COMPLETED':
            return {
                ...state,
                todos: state.todos.filter(todo => !todo.completed)
            };
        default:
            return state;
    }
}

function TodoApp() {
    const [state, dispatch] = useReducer(todoReducer, {
        todos: [],
        filter: 'all'
    });
    
    const [inputValue, setInputValue] = useState('');
    
    const addTodo = () => {
        if (inputValue.trim()) {
            dispatch({ type: 'ADD_TODO', text: inputValue });
            setInputValue('');
        }
    };
    
    const toggleTodo = (id) => {
        dispatch({ type: 'TOGGLE_TODO', id });
    };
    
    const deleteTodo = (id) => {
        dispatch({ type: 'DELETE_TODO', id });
    };
    
    const setFilter = (filter) => {
        dispatch({ type: 'SET_FILTER', filter });
    };
    
    const clearCompleted = () => {
        dispatch({ type: 'CLEAR_COMPLETED' });
    };
    
    const filteredTodos = state.todos.filter(todo => {
        if (state.filter === 'active') return !todo.completed;
        if (state.filter === 'completed') return todo.completed;
        return true;
    });
    
    return (
        <div>
            <h1>Todo App</h1>
            
            <div>
                <input 
                    value={inputValue}
                    onChange={(e) => setInputValue(e.target.value)}
                    onKeyPress={(e) => e.key === 'Enter' && addTodo()}
                    placeholder="Add a todo"
                />
                <button onClick={addTodo}>Add</button>
            </div>
            
            <div>
                <button onClick={() => setFilter('all')}>All</button>
                <button onClick={() => setFilter('active')}>Active</button>
                <button onClick={() => setFilter('completed')}>Completed</button>
                <button onClick={clearCompleted}>Clear Completed</button>
            </div>
            
            <ul>
                {filteredTodos.map(todo => (
                    <li key={todo.id}>
                        <input 
                            type="checkbox"
                            checked={todo.completed}
                            onChange={() => toggleTodo(todo.id)}
                        />
                        <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
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

## Custom Hooks

### Basic Custom Hook
```javascript
// Custom hook for form handling
function useForm(initialValues) {
    const [values, setValues] = useState(initialValues);
    const [errors, setErrors] = useState({});
    
    const handleChange = (e) => {
        const { name, value } = e.target;
        setValues(prev => ({ ...prev, [name]: value }));
        
        // Clear error when user starts typing
        if (errors[name]) {
            setErrors(prev => ({ ...prev, [name]: '' }));
        }
    };
    
    const handleSubmit = (onSubmit) => (e) => {
        e.preventDefault();
        onSubmit(values);
    };
    
    const reset = () => {
        setValues(initialValues);
        setErrors({});
    };
    
    const setError = (field, message) => {
        setErrors(prev => ({ ...prev, [field]: message }));
    };
    
    return {
        values,
        errors,
        handleChange,
        handleSubmit,
        reset,
        setError
    };
}

// Using the custom hook
function ContactForm() {
    const { values, errors, handleChange, handleSubmit, setError } = useForm({
        name: '',
        email: '',
        message: ''
    });
    
    const onSubmit = (formData) => {
        // Validate form
        if (!formData.name) {
            setError('name', 'Name is required');
            return;
        }
        if (!formData.email) {
            setError('email', 'Email is required');
            return;
        }
        if (!formData.email.includes('@')) {
            setError('email', 'Invalid email format');
            return;
        }
        
        console.log('Form submitted:', formData);
        // Handle form submission
    };
    
    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <input 
                    name="name"
                    value={values.name}
                    onChange={handleChange}
                    placeholder="Name"
                />
                {errors.name && <span className="error">{errors.name}</span>}
            </div>
            
            <div>
                <input 
                    name="email"
                    type="email"
                    value={values.email}
                    onChange={handleChange}
                    placeholder="Email"
                />
                {errors.email && <span className="error">{errors.email}</span>}
            </div>
            
            <div>
                <textarea 
                    name="message"
                    value={values.message}
                    onChange={handleChange}
                    placeholder="Message"
                />
            </div>
            
            <button type="submit">Submit</button>
        </form>
    );
}
```

### Custom Hook for API Calls
```javascript
// Custom hook for API calls
function useApi(url, options = {}) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);
    
    const fetchData = async (customUrl = url, customOptions = options) => {
        setLoading(true);
        setError(null);
        
        try {
            const response = await fetch(customUrl, customOptions);
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            const result = await response.json();
            setData(result);
        } catch (err) {
            setError(err.message);
        } finally {
            setLoading(false);
        }
    };
    
    useEffect(() => {
        if (url) {
            fetchData();
        }
    }, [url]);
    
    return { data, loading, error, refetch: fetchData };
}

// Using the custom hook
function UserProfile({ userId }) {
    const { data: user, loading, error, refetch } = useApi(`/api/users/${userId}`);
    
    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    if (!user) return <div>User not found</div>;
    
    return (
        <div>
            <h2>{user.name}</h2>
            <p>Email: {user.email}</p>
            <button onClick={() => refetch()}>Refresh</button>
        </div>
    );
}
```

### Custom Hook for Local Storage
```javascript
// Custom hook for local storage
function useLocalStorage(key, initialValue) {
    const [storedValue, setStoredValue] = useState(() => {
        try {
            const item = window.localStorage.getItem(key);
            return item ? JSON.parse(item) : initialValue;
        } catch (error) {
            console.error(`Error reading localStorage key "${key}":`, error);
            return initialValue;
        }
    });
    
    const setValue = (value) => {
        try {
            const valueToStore = value instanceof Function ? value(storedValue) : value;
            setStoredValue(valueToStore);
            window.localStorage.setItem(key, JSON.stringify(valueToStore));
        } catch (error) {
            console.error(`Error setting localStorage key "${key}":`, error);
        }
    };
    
    return [storedValue, setValue];
}

// Using the custom hook
function Settings() {
    const [theme, setTheme] = useLocalStorage('theme', 'light');
    const [language, setLanguage] = useLocalStorage('language', 'en');
    
    return (
        <div>
            <h2>Settings</h2>
            <div>
                <label>Theme:</label>
                <select value={theme} onChange={(e) => setTheme(e.target.value)}>
                    <option value="light">Light</option>
                    <option value="dark">Dark</option>
                </select>
            </div>
            <div>
                <label>Language:</label>
                <select value={language} onChange={(e) => setLanguage(e.target.value)}>
                    <option value="en">English</option>
                    <option value="es">Spanish</option>
                    <option value="fr">French</option>
                </select>
            </div>
        </div>
    );
}
```

---

## Practice Exercises

### Exercise 1: Shopping Cart with Hooks
```javascript
function ShoppingCart() {
    const [items, setItems] = useState([]);
    const [total, setTotal] = useState(0);
    
    const addItem = (product) => {
        setItems(prevItems => {
            const existingItem = prevItems.find(item => item.id === product.id);
            if (existingItem) {
                return prevItems.map(item =>
                    item.id === product.id
                        ? { ...item, quantity: item.quantity + 1 }
                        : item
                );
            }
            return [...prevItems, { ...product, quantity: 1 }];
        });
    };
    
    const removeItem = (id) => {
        setItems(prevItems => prevItems.filter(item => item.id !== id));
    };
    
    const updateQuantity = (id, quantity) => {
        if (quantity <= 0) {
            removeItem(id);
            return;
        }
        
        setItems(prevItems =>
            prevItems.map(item =>
                item.id === id ? { ...item, quantity } : item
            )
        );
    };
    
    useEffect(() => {
        const newTotal = items.reduce((sum, item) => sum + (item.price * item.quantity), 0);
        setTotal(newTotal);
    }, [items]);
    
    return (
        <div>
            <h2>Shopping Cart</h2>
            <div>
                {items.map(item => (
                    <div key={item.id} className="cart-item">
                        <h3>{item.name}</h3>
                        <p>Price: ${item.price}</p>
                        <div>
                            <button onClick={() => updateQuantity(item.id, item.quantity - 1)}>-</button>
                            <span>{item.quantity}</span>
                            <button onClick={() => updateQuantity(item.id, item.quantity + 1)}>+</button>
                        </div>
                        <button onClick={() => removeItem(item.id)}>Remove</button>
                    </div>
                ))}
            </div>
            <div className="total">
                <h3>Total: ${total.toFixed(2)}</h3>
            </div>
        </div>
    );
}
```

### Exercise 2: Timer with useReducer
```javascript
function timerReducer(state, action) {
    switch (action.type) {
        case 'START':
            return { ...state, isRunning: true };
        case 'PAUSE':
            return { ...state, isRunning: false };
        case 'RESET':
            return { ...state, isRunning: false, time: 0 };
        case 'TICK':
            return { ...state, time: state.time + 1 };
        default:
            return state;
    }
}

function Timer() {
    const [state, dispatch] = useReducer(timerReducer, {
        time: 0,
        isRunning: false
    });
    
    useEffect(() => {
        let interval;
        if (state.isRunning) {
            interval = setInterval(() => {
                dispatch({ type: 'TICK' });
            }, 1000);
        }
        return () => clearInterval(interval);
    }, [state.isRunning]);
    
    const formatTime = (seconds) => {
        const mins = Math.floor(seconds / 60);
        const secs = seconds % 60;
        return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
    };
    
    return (
        <div>
            <h2>Timer: {formatTime(state.time)}</h2>
            <div>
                <button onClick={() => dispatch({ type: 'START' })} disabled={state.isRunning}>
                    Start
                </button>
                <button onClick={() => dispatch({ type: 'PAUSE' })} disabled={!state.isRunning}>
                    Pause
                </button>
                <button onClick={() => dispatch({ type: 'RESET' })}>
                    Reset
                </button>
            </div>
        </div>
    );
}
```

### Exercise 3: Theme Context with Custom Hook
```javascript
const ThemeContext = createContext();

function ThemeProvider({ children }) {
    const [theme, setTheme] = useState('light');
    
    const toggleTheme = () => {
        setTheme(prevTheme => prevTheme === 'light' ? 'dark' : 'light');
    };
    
    return (
        <ThemeContext.Provider value={{ theme, toggleTheme }}>
            {children}
        </ThemeContext.Provider>
    );
}

function useTheme() {
    const context = useContext(ThemeContext);
    if (!context) {
        throw new Error('useTheme must be used within a ThemeProvider');
    }
    return context;
}

function App() {
    return (
        <ThemeProvider>
            <Header />
            <Main />
        </ThemeProvider>
    );
}

function Header() {
    const { theme, toggleTheme } = useTheme();
    
    return (
        <header className={`header ${theme}`}>
            <h1>My App</h1>
            <button onClick={toggleTheme}>
                Switch to {theme === 'light' ? 'dark' : 'light'} theme
            </button>
        </header>
    );
}

function Main() {
    const { theme } = useTheme();
    
    return (
        <main className={`main ${theme}`}>
            <p>Current theme: {theme}</p>
        </main>
    );
}
```

---

## Summary

### Key Concepts Learned
- ✅ **useState** - Managing component state
- ✅ **useEffect** - Side effects and lifecycle
- ✅ **useContext** - Sharing data across components
- ✅ **useReducer** - Complex state management
- ✅ **Custom Hooks** - Reusable logic

### Best Practices
- Use useState for simple state
- Use useReducer for complex state logic
- Use useEffect for side effects
- Use useContext for global state
- Create custom hooks for reusable logic
- Always clean up side effects

### Common Mistakes
- Mutating state directly
- Missing dependencies in useEffect
- Not cleaning up side effects
- Overusing useContext
- Not using custom hooks for reusable logic

---

**Excellent! You've mastered React Hooks. Ready for React Router? 🚀**

**Next Tutorial:** [React Router](./19-react-router.md)
