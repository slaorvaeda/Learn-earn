# State Management - Tutorial 20 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Local State vs Global State](#local-state-vs-global-state)
3. [Context API](#context-api)
4. [Redux](#redux)
5. [Zustand](#zustand)
6. [Jotai](#jotai)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

State management is **crucial for building complex React applications**. It determines how data flows through your app and how components communicate with each other.

### What You'll Learn
- ✅ **Local vs Global State** - When to use each
- ✅ **Context API** - Built-in state management
- ✅ **Redux** - Popular state management library
- ✅ **Zustand** - Lightweight state management
- ✅ **Jotai** - Atomic state management

---

## Local State vs Global State

### Local State
Local state is managed within individual components and is not shared with other components.

```javascript
import React, { useState } from 'react';

function Counter() {
    const [count, setCount] = useState(0);
    
    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
        </div>
    );
}

function TodoItem({ todo }) {
    const [isEditing, setIsEditing] = useState(false);
    const [editText, setEditText] = useState(todo.text);
    
    const handleEdit = () => {
        setIsEditing(true);
    };
    
    const handleSave = () => {
        // Update todo
        setIsEditing(false);
    };
    
    return (
        <div>
            {isEditing ? (
                <input 
                    value={editText}
                    onChange={(e) => setEditText(e.target.value)}
                    onBlur={handleSave}
                />
            ) : (
                <span onClick={handleEdit}>{todo.text}</span>
            )}
        </div>
    );
}
```

### Global State
Global state is shared across multiple components and can be accessed from anywhere in the app.

```javascript
// Global state example with Context
import React, { createContext, useContext, useState } from 'react';

const UserContext = createContext();

function UserProvider({ children }) {
    const [user, setUser] = useState(null);
    const [isLoading, setIsLoading] = useState(false);
    
    const login = async (credentials) => {
        setIsLoading(true);
        try {
            const response = await fetch('/api/login', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(credentials)
            });
            const userData = await response.json();
            setUser(userData);
        } catch (error) {
            console.error('Login failed:', error);
        } finally {
            setIsLoading(false);
        }
    };
    
    const logout = () => {
        setUser(null);
    };
    
    return (
        <UserContext.Provider value={{ user, isLoading, login, logout }}>
            {children}
        </UserContext.Provider>
    );
}

function useUser() {
    const context = useContext(UserContext);
    if (!context) {
        throw new Error('useUser must be used within a UserProvider');
    }
    return context;
}

// Using global state
function Header() {
    const { user, logout } = useUser();
    
    return (
        <header>
            <h1>My App</h1>
            {user ? (
                <div>
                    <span>Welcome, {user.name}</span>
                    <button onClick={logout}>Logout</button>
                </div>
            ) : (
                <button>Login</button>
            )}
        </header>
    );
}

function Profile() {
    const { user } = useUser();
    
    if (!user) return <div>Please login</div>;
    
    return (
        <div>
            <h2>Profile</h2>
            <p>Name: {user.name}</p>
            <p>Email: {user.email}</p>
        </div>
    );
}
```

---

## Context API

### Basic Context
```javascript
import React, { createContext, useContext, useState } from 'react';

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
```

### Multiple Contexts
```javascript
// User Context
const UserContext = createContext();

function UserProvider({ children }) {
    const [user, setUser] = useState(null);
    const [isLoading, setIsLoading] = useState(false);
    
    const login = async (credentials) => {
        setIsLoading(true);
        try {
            const response = await fetch('/api/login', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(credentials)
            });
            const userData = await response.json();
            setUser(userData);
        } catch (error) {
            console.error('Login failed:', error);
        } finally {
            setIsLoading(false);
        }
    };
    
    const logout = () => {
        setUser(null);
    };
    
    return (
        <UserContext.Provider value={{ user, isLoading, login, logout }}>
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
```

### Context with useReducer
```javascript
import React, { createContext, useContext, useReducer } from 'react';

// Reducer
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
        default:
            return state;
    }
}

// Context
const TodoContext = createContext();

function TodoProvider({ children }) {
    const [state, dispatch] = useReducer(todoReducer, {
        todos: [],
        filter: 'all'
    });
    
    const addTodo = (text) => {
        dispatch({ type: 'ADD_TODO', text });
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
    
    return (
        <TodoContext.Provider value={{
            ...state,
            addTodo,
            toggleTodo,
            deleteTodo,
            setFilter
        }}>
            {children}
        </TodoContext.Provider>
    );
}

function useTodos() {
    const context = useContext(TodoContext);
    if (!context) {
        throw new Error('useTodos must be used within a TodoProvider');
    }
    return context;
}
```

---

## Redux

### Installation and Setup
```bash
npm install @reduxjs/toolkit react-redux
```

```javascript
// store.js
import { configureStore } from '@reduxjs/toolkit';
import todoReducer from './features/todos/todoSlice';

export const store = configureStore({
    reducer: {
        todos: todoReducer
    }
});

// main.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Provider } from 'react-redux';
import { store } from './store';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
    <Provider store={store}>
        <App />
    </Provider>
);
```

### Redux Slice
```javascript
// features/todos/todoSlice.js
import { createSlice } from '@reduxjs/toolkit';

const initialState = {
    todos: [],
    filter: 'all'
};

const todoSlice = createSlice({
    name: 'todos',
    initialState,
    reducers: {
        addTodo: (state, action) => {
            state.todos.push({
                id: Date.now(),
                text: action.payload,
                completed: false
            });
        },
        toggleTodo: (state, action) => {
            const todo = state.todos.find(todo => todo.id === action.payload);
            if (todo) {
                todo.completed = !todo.completed;
            }
        },
        deleteTodo: (state, action) => {
            state.todos = state.todos.filter(todo => todo.id !== action.payload);
        },
        setFilter: (state, action) => {
            state.filter = action.payload;
        }
    }
});

export const { addTodo, toggleTodo, deleteTodo, setFilter } = todoSlice.actions;
export default todoSlice.reducer;
```

### Using Redux in Components
```javascript
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { addTodo, toggleTodo, deleteTodo, setFilter } from './features/todos/todoSlice';

function TodoApp() {
    const { todos, filter } = useSelector(state => state.todos);
    const dispatch = useDispatch();
    
    const [inputValue, setInputValue] = React.useState('');
    
    const handleAddTodo = () => {
        if (inputValue.trim()) {
            dispatch(addTodo(inputValue));
            setInputValue('');
        }
    };
    
    const handleToggleTodo = (id) => {
        dispatch(toggleTodo(id));
    };
    
    const handleDeleteTodo = (id) => {
        dispatch(deleteTodo(id));
    };
    
    const handleSetFilter = (filter) => {
        dispatch(setFilter(filter));
    };
    
    const filteredTodos = todos.filter(todo => {
        if (filter === 'active') return !todo.completed;
        if (filter === 'completed') return todo.completed;
        return true;
    });
    
    return (
        <div>
            <h1>Todo App</h1>
            
            <div>
                <input 
                    value={inputValue}
                    onChange={(e) => setInputValue(e.target.value)}
                    onKeyPress={(e) => e.key === 'Enter' && handleAddTodo()}
                    placeholder="Add a todo"
                />
                <button onClick={handleAddTodo}>Add</button>
            </div>
            
            <div>
                <button onClick={() => handleSetFilter('all')}>All</button>
                <button onClick={() => handleSetFilter('active')}>Active</button>
                <button onClick={() => handleSetFilter('completed')}>Completed</button>
            </div>
            
            <ul>
                {filteredTodos.map(todo => (
                    <li key={todo.id}>
                        <input 
                            type="checkbox"
                            checked={todo.completed}
                            onChange={() => handleToggleTodo(todo.id)}
                        />
                        <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
                            {todo.text}
                        </span>
                        <button onClick={() => handleDeleteTodo(todo.id)}>Delete</button>
                    </li>
                ))}
            </ul>
        </div>
    );
}
```

### Async Actions with Redux Toolkit
```javascript
// features/users/userSlice.js
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

// Async thunk
export const fetchUsers = createAsyncThunk(
    'users/fetchUsers',
    async (_, { rejectWithValue }) => {
        try {
            const response = await fetch('/api/users');
            if (!response.ok) {
                throw new Error('Failed to fetch users');
            }
            return await response.json();
        } catch (error) {
            return rejectWithValue(error.message);
        }
    }
);

export const createUser = createAsyncThunk(
    'users/createUser',
    async (userData, { rejectWithValue }) => {
        try {
            const response = await fetch('/api/users', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(userData)
            });
            if (!response.ok) {
                throw new Error('Failed to create user');
            }
            return await response.json();
        } catch (error) {
            return rejectWithValue(error.message);
        }
    }
);

const initialState = {
    users: [],
    loading: false,
    error: null
};

const userSlice = createSlice({
    name: 'users',
    initialState,
    reducers: {
        clearError: (state) => {
            state.error = null;
        }
    },
    extraReducers: (builder) => {
        builder
            .addCase(fetchUsers.pending, (state) => {
                state.loading = true;
                state.error = null;
            })
            .addCase(fetchUsers.fulfilled, (state, action) => {
                state.loading = false;
                state.users = action.payload;
            })
            .addCase(fetchUsers.rejected, (state, action) => {
                state.loading = false;
                state.error = action.payload;
            })
            .addCase(createUser.pending, (state) => {
                state.loading = true;
                state.error = null;
            })
            .addCase(createUser.fulfilled, (state, action) => {
                state.loading = false;
                state.users.push(action.payload);
            })
            .addCase(createUser.rejected, (state, action) => {
                state.loading = false;
                state.error = action.payload;
            });
    }
});

export const { clearError } = userSlice.actions;
export default userSlice.reducer;
```

---

## Zustand

### Installation and Setup
```bash
npm install zustand
```

### Basic Store
```javascript
// store.js
import { create } from 'zustand';

const useStore = create((set) => ({
    count: 0,
    increment: () => set((state) => ({ count: state.count + 1 })),
    decrement: () => set((state) => ({ count: state.count - 1 })),
    reset: () => set({ count: 0 })
}));

export default useStore;
```

### Using Zustand in Components
```javascript
import React from 'react';
import useStore from './store';

function Counter() {
    const { count, increment, decrement, reset } = useStore();
    
    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={increment}>Increment</button>
            <button onClick={decrement}>Decrement</button>
            <button onClick={reset}>Reset</button>
        </div>
    );
}

function AnotherComponent() {
    const count = useStore((state) => state.count);
    
    return <div>Current count: {count}</div>;
}
```

### Complex Store with Actions
```javascript
// store.js
import { create } from 'zustand';

const useTodoStore = create((set, get) => ({
    todos: [],
    filter: 'all',
    
    addTodo: (text) => set((state) => ({
        todos: [...state.todos, {
            id: Date.now(),
            text,
            completed: false
        }]
    })),
    
    toggleTodo: (id) => set((state) => ({
        todos: state.todos.map(todo =>
            todo.id === id ? { ...todo, completed: !todo.completed } : todo
        )
    })),
    
    deleteTodo: (id) => set((state) => ({
        todos: state.todos.filter(todo => todo.id !== id)
    })),
    
    setFilter: (filter) => set({ filter }),
    
    getFilteredTodos: () => {
        const { todos, filter } = get();
        if (filter === 'active') return todos.filter(todo => !todo.completed);
        if (filter === 'completed') return todos.filter(todo => todo.completed);
        return todos;
    }
}));

export default useTodoStore;
```

### Async Actions with Zustand
```javascript
import { create } from 'zustand';

const useUserStore = create((set, get) => ({
    users: [],
    loading: false,
    error: null,
    
    fetchUsers: async () => {
        set({ loading: true, error: null });
        try {
            const response = await fetch('/api/users');
            const users = await response.json();
            set({ users, loading: false });
        } catch (error) {
            set({ error: error.message, loading: false });
        }
    },
    
    createUser: async (userData) => {
        set({ loading: true, error: null });
        try {
            const response = await fetch('/api/users', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(userData)
            });
            const newUser = await response.json();
            set((state) => ({
                users: [...state.users, newUser],
                loading: false
            }));
        } catch (error) {
            set({ error: error.message, loading: false });
        }
    }
}));
```

---

## Jotai

### Installation and Setup
```bash
npm install jotai
```

### Basic Atoms
```javascript
import { atom, useAtom } from 'jotai';

// Basic atom
const countAtom = atom(0);

function Counter() {
    const [count, setCount] = useAtom(countAtom);
    
    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
            <button onClick={() => setCount(count - 1)}>Decrement</button>
        </div>
    );
}
```

### Derived Atoms
```javascript
import { atom, useAtom } from 'jotai';

// Base atoms
const firstNameAtom = atom('');
const lastNameAtom = atom('');

// Derived atom
const fullNameAtom = atom((get) => {
    const firstName = get(firstNameAtom);
    const lastName = get(lastNameAtom);
    return `${firstName} ${lastName}`.trim();
});

function NameForm() {
    const [firstName, setFirstName] = useAtom(firstNameAtom);
    const [lastName, setLastName] = useAtom(lastNameAtom);
    const [fullName] = useAtom(fullNameAtom);
    
    return (
        <div>
            <input 
                value={firstName}
                onChange={(e) => setFirstName(e.target.value)}
                placeholder="First Name"
            />
            <input 
                value={lastName}
                onChange={(e) => setLastName(e.target.value)}
                placeholder="Last Name"
            />
            <p>Full Name: {fullName}</p>
        </div>
    );
}
```

### Async Atoms
```javascript
import { atom } from 'jotai';

// Async atom
const userAtom = atom(async () => {
    const response = await fetch('/api/user');
    return response.json();
});

// Derived async atom
const userPostsAtom = atom(async (get) => {
    const user = await get(userAtom);
    const response = await fetch(`/api/users/${user.id}/posts`);
    return response.json();
});

function UserProfile() {
    const [user] = useAtom(userAtom);
    const [posts] = useAtom(userPostsAtom);
    
    if (!user) return <div>Loading...</div>;
    
    return (
        <div>
            <h2>{user.name}</h2>
            <div>
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

## Practice Exercises

### Exercise 1: Shopping Cart with Zustand
```javascript
import { create } from 'zustand';

const useCartStore = create((set, get) => ({
    items: [],
    total: 0,
    
    addItem: (product) => set((state) => {
        const existingItem = state.items.find(item => item.id === product.id);
        if (existingItem) {
            return {
                items: state.items.map(item =>
                    item.id === product.id
                        ? { ...item, quantity: item.quantity + 1 }
                        : item
                )
            };
        }
        return {
            items: [...state.items, { ...product, quantity: 1 }]
        };
    }),
    
    removeItem: (id) => set((state) => ({
        items: state.items.filter(item => item.id !== id)
    })),
    
    updateQuantity: (id, quantity) => set((state) => {
        if (quantity <= 0) {
            return {
                items: state.items.filter(item => item.id !== id)
            };
        }
        return {
            items: state.items.map(item =>
                item.id === id ? { ...item, quantity } : item
            )
        };
    }),
    
    getTotal: () => {
        const { items } = get();
        return items.reduce((total, item) => total + (item.price * item.quantity), 0);
    }
}));

function ShoppingCart() {
    const { items, addItem, removeItem, updateQuantity, getTotal } = useCartStore();
    const total = getTotal();
    
    return (
        <div>
            <h2>Shopping Cart</h2>
            {items.map(item => (
                <div key={item.id}>
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
            <div>
                <h3>Total: ${total.toFixed(2)}</h3>
            </div>
        </div>
    );
}
```

### Exercise 2: Theme Store with Jotai
```javascript
import { atom, useAtom } from 'jotai';

const themeAtom = atom('light');
const fontSizeAtom = atom(16);

const themeConfigAtom = atom((get) => {
    const theme = get(themeAtom);
    const fontSize = get(fontSizeAtom);
    
    return {
        theme,
        fontSize,
        colors: theme === 'light' 
            ? { bg: '#fff', text: '#000' }
            : { bg: '#000', text: '#fff' }
    };
});

function ThemeToggle() {
    const [theme, setTheme] = useAtom(themeAtom);
    
    return (
        <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
            Switch to {theme === 'light' ? 'dark' : 'light'} theme
        </button>
    );
}

function FontSizeControl() {
    const [fontSize, setFontSize] = useAtom(fontSizeAtom);
    
    return (
        <div>
            <label>Font Size: {fontSize}px</label>
            <input 
                type="range"
                min="12"
                max="24"
                value={fontSize}
                onChange={(e) => setFontSize(parseInt(e.target.value))}
            />
        </div>
    );
}

function App() {
    const [themeConfig] = useAtom(themeConfigAtom);
    
    return (
        <div style={{
            backgroundColor: themeConfig.colors.bg,
            color: themeConfig.colors.text,
            fontSize: `${themeConfig.fontSize}px`
        }}>
            <ThemeToggle />
            <FontSizeControl />
            <h1>My App</h1>
            <p>This is some content with the current theme and font size.</p>
        </div>
    );
}
```

### Exercise 3: User Management with Redux
```javascript
// features/users/userSlice.js
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

export const fetchUsers = createAsyncThunk(
    'users/fetchUsers',
    async (_, { rejectWithValue }) => {
        try {
            const response = await fetch('/api/users');
            if (!response.ok) throw new Error('Failed to fetch users');
            return await response.json();
        } catch (error) {
            return rejectWithValue(error.message);
        }
    }
);

export const createUser = createAsyncThunk(
    'users/createUser',
    async (userData, { rejectWithValue }) => {
        try {
            const response = await fetch('/api/users', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(userData)
            });
            if (!response.ok) throw new Error('Failed to create user');
            return await response.json();
        } catch (error) {
            return rejectWithValue(error.message);
        }
    }
);

const userSlice = createSlice({
    name: 'users',
    initialState: {
        users: [],
        loading: false,
        error: null
    },
    reducers: {
        clearError: (state) => {
            state.error = null;
        }
    },
    extraReducers: (builder) => {
        builder
            .addCase(fetchUsers.pending, (state) => {
                state.loading = true;
                state.error = null;
            })
            .addCase(fetchUsers.fulfilled, (state, action) => {
                state.loading = false;
                state.users = action.payload;
            })
            .addCase(fetchUsers.rejected, (state, action) => {
                state.loading = false;
                state.error = action.payload;
            })
            .addCase(createUser.pending, (state) => {
                state.loading = true;
                state.error = null;
            })
            .addCase(createUser.fulfilled, (state, action) => {
                state.loading = false;
                state.users.push(action.payload);
            })
            .addCase(createUser.rejected, (state, action) => {
                state.loading = false;
                state.error = action.payload;
            });
    }
});

export const { clearError } = userSlice.actions;
export default userSlice.reducer;

// UserList.jsx
import React, { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { fetchUsers, createUser, clearError } from './features/users/userSlice';

function UserList() {
    const { users, loading, error } = useSelector(state => state.users);
    const dispatch = useDispatch();
    
    useEffect(() => {
        dispatch(fetchUsers());
    }, [dispatch]);
    
    const handleCreateUser = (userData) => {
        dispatch(createUser(userData));
    };
    
    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    
    return (
        <div>
            <h2>Users</h2>
            {users.map(user => (
                <div key={user.id}>
                    <h3>{user.name}</h3>
                    <p>{user.email}</p>
                </div>
            ))}
        </div>
    );
}
```

---

## Summary

### Key Concepts Learned
- ✅ **Local vs Global State** - When to use each
- ✅ **Context API** - Built-in state management
- ✅ **Redux** - Popular state management library
- ✅ **Zustand** - Lightweight state management
- ✅ **Jotai** - Atomic state management

### Best Practices
- Use local state for component-specific data
- Use global state for shared data
- Choose the right state management solution
- Keep state as minimal as possible
- Use proper error handling

### Common Mistakes
- Overusing global state
- Not handling loading states
- Missing error handling
- Not cleaning up subscriptions
- Using the wrong state management solution

---

**Excellent! You've mastered state management. Ready for Testing? 🚀**

**Next Tutorial:** [Testing](./21-testing.md)
