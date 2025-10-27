# Testing - Tutorial 21 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Testing Fundamentals](#testing-fundamentals)
3. [Jest Testing](#jest-testing)
4. [React Testing Library](#react-testing-library)
5. [Testing Hooks](#testing-hooks)
6. [Integration Testing](#integration-testing)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Testing is **essential for building reliable applications**. It helps catch bugs early, ensures code quality, and makes refactoring safer.

### What You'll Learn
- ✅ **Testing Fundamentals** - Types of tests and best practices
- ✅ **Jest Testing** - JavaScript testing framework
- ✅ **React Testing Library** - Testing React components
- ✅ **Testing Hooks** - Testing custom hooks
- ✅ **Integration Testing** - Testing component interactions

---

## Testing Fundamentals

### Types of Tests
```javascript
// Unit Tests - Test individual functions
function add(a, b) {
    return a + b;
}

// Integration Tests - Test how components work together
function UserProfile({ user }) {
    return (
        <div>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
        </div>
    );
}

// End-to-End Tests - Test complete user workflows
// (Usually done with tools like Cypress or Playwright)
```

### Testing Best Practices
```javascript
// 1. Test behavior, not implementation
// Good: Test what the user sees
expect(screen.getByText('Hello, World!')).toBeInTheDocument();

// Bad: Test implementation details
expect(component.state.message).toBe('Hello, World!');

// 2. Use descriptive test names
test('should display error message when login fails', () => {
    // test implementation
});

// 3. Arrange, Act, Assert pattern
test('should add two numbers correctly', () => {
    // Arrange
    const a = 2;
    const b = 3;
    
    // Act
    const result = add(a, b);
    
    // Assert
    expect(result).toBe(5);
});

// 4. Test edge cases
test('should handle empty input', () => {
    expect(validateInput('')).toBe(false);
});

test('should handle null input', () => {
    expect(validateInput(null)).toBe(false);
});
```

---

## Jest Testing

### Basic Jest Setup
```javascript
// package.json
{
    "scripts": {
        "test": "jest",
        "test:watch": "jest --watch",
        "test:coverage": "jest --coverage"
    },
    "devDependencies": {
        "jest": "^29.0.0",
        "@testing-library/jest-dom": "^5.16.0"
    }
}

// jest.config.js
module.exports = {
    testEnvironment: 'jsdom',
    setupFilesAfterEnv: ['<rootDir>/src/setupTests.js'],
    moduleNameMapping: {
        '\\.(css|less|scss)$': 'identity-obj-proxy'
    }
};
```

### Basic Jest Tests
```javascript
// math.js
export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}

export function multiply(a, b) {
    return a * b;
}

export function divide(a, b) {
    if (b === 0) {
        throw new Error('Division by zero');
    }
    return a / b;
}

// math.test.js
import { add, subtract, multiply, divide } from './math';

describe('Math functions', () => {
    describe('add', () => {
        test('should add two positive numbers', () => {
            expect(add(2, 3)).toBe(5);
        });
        
        test('should add negative numbers', () => {
            expect(add(-2, -3)).toBe(-5);
        });
        
        test('should add zero', () => {
            expect(add(5, 0)).toBe(5);
        });
    });
    
    describe('subtract', () => {
        test('should subtract two numbers', () => {
            expect(subtract(5, 3)).toBe(2);
        });
    });
    
    describe('multiply', () => {
        test('should multiply two numbers', () => {
            expect(multiply(2, 3)).toBe(6);
        });
    });
    
    describe('divide', () => {
        test('should divide two numbers', () => {
            expect(divide(6, 2)).toBe(3);
        });
        
        test('should throw error when dividing by zero', () => {
            expect(() => divide(5, 0)).toThrow('Division by zero');
        });
    });
});
```

### Async Testing
```javascript
// api.js
export async function fetchUser(id) {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) {
        throw new Error('User not found');
    }
    return response.json();
}

// api.test.js
import { fetchUser } from './api';

// Mock fetch
global.fetch = jest.fn();

describe('fetchUser', () => {
    beforeEach(() => {
        fetch.mockClear();
    });
    
    test('should fetch user successfully', async () => {
        const mockUser = { id: 1, name: 'John Doe' };
        fetch.mockResolvedValueOnce({
            ok: true,
            json: async () => mockUser
        });
        
        const result = await fetchUser(1);
        
        expect(fetch).toHaveBeenCalledWith('/api/users/1');
        expect(result).toEqual(mockUser);
    });
    
    test('should throw error when user not found', async () => {
        fetch.mockResolvedValueOnce({
            ok: false
        });
        
        await expect(fetchUser(999)).rejects.toThrow('User not found');
    });
});
```

### Mocking
```javascript
// utils.js
export function formatDate(date) {
    return new Date(date).toLocaleDateString();
}

export function generateId() {
    return Math.random().toString(36).substr(2, 9);
}

// utils.test.js
import { formatDate, generateId } from './utils';

describe('formatDate', () => {
    test('should format date correctly', () => {
        const date = '2023-12-25';
        const result = formatDate(date);
        expect(result).toBe('12/25/2023');
    });
});

describe('generateId', () => {
    test('should generate unique IDs', () => {
        const id1 = generateId();
        const id2 = generateId();
        
        expect(id1).toBeDefined();
        expect(id2).toBeDefined();
        expect(id1).not.toBe(id2);
        expect(id1).toHaveLength(9);
    });
    
    test('should generate different IDs on multiple calls', () => {
        const ids = Array.from({ length: 100 }, () => generateId());
        const uniqueIds = new Set(ids);
        expect(uniqueIds.size).toBe(100);
    });
});
```

---

## React Testing Library

### Installation and Setup
```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

```javascript
// setupTests.js
import '@testing-library/jest-dom';
```

### Basic Component Testing
```javascript
// Button.jsx
import React from 'react';

function Button({ children, onClick, disabled = false }) {
    return (
        <button onClick={onClick} disabled={disabled}>
            {children}
        </button>
    );
}

export default Button;

// Button.test.jsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import Button from './Button';

describe('Button', () => {
    test('should render button with text', () => {
        render(<Button>Click me</Button>);
        expect(screen.getByRole('button', { name: 'Click me' })).toBeInTheDocument();
    });
    
    test('should call onClick when clicked', () => {
        const handleClick = jest.fn();
        render(<Button onClick={handleClick}>Click me</Button>);
        
        fireEvent.click(screen.getByRole('button'));
        expect(handleClick).toHaveBeenCalledTimes(1);
    });
    
    test('should be disabled when disabled prop is true', () => {
        render(<Button disabled>Click me</Button>);
        expect(screen.getByRole('button')).toBeDisabled();
    });
    
    test('should not call onClick when disabled', () => {
        const handleClick = jest.fn();
        render(<Button onClick={handleClick} disabled>Click me</Button>);
        
        fireEvent.click(screen.getByRole('button'));
        expect(handleClick).not.toHaveBeenCalled();
    });
});
```

### Form Testing
```javascript
// LoginForm.jsx
import React, { useState } from 'react';

function LoginForm({ onSubmit }) {
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    
    const handleSubmit = (e) => {
        e.preventDefault();
        onSubmit({ email, password });
    };
    
    return (
        <form onSubmit={handleSubmit}>
            <div>
                <label htmlFor="email">Email:</label>
                <input
                    id="email"
                    type="email"
                    value={email}
                    onChange={(e) => setEmail(e.target.value)}
                    required
                />
            </div>
            <div>
                <label htmlFor="password">Password:</label>
                <input
                    id="password"
                    type="password"
                    value={password}
                    onChange={(e) => setPassword(e.target.value)}
                    required
                />
            </div>
            <button type="submit">Login</button>
        </form>
    );
}

export default LoginForm;

// LoginForm.test.jsx
import React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import LoginForm from './LoginForm';

describe('LoginForm', () => {
    test('should render form fields', () => {
        render(<LoginForm onSubmit={jest.fn()} />);
        
        expect(screen.getByLabelText('Email:')).toBeInTheDocument();
        expect(screen.getByLabelText('Password:')).toBeInTheDocument();
        expect(screen.getByRole('button', { name: 'Login' })).toBeInTheDocument();
    });
    
    test('should call onSubmit with form data', async () => {
        const user = userEvent.setup();
        const handleSubmit = jest.fn();
        
        render(<LoginForm onSubmit={handleSubmit} />);
        
        await user.type(screen.getByLabelText('Email:'), 'test@example.com');
        await user.type(screen.getByLabelText('Password:'), 'password123');
        await user.click(screen.getByRole('button', { name: 'Login' }));
        
        expect(handleSubmit).toHaveBeenCalledWith({
            email: 'test@example.com',
            password: 'password123'
        });
    });
    
    test('should not submit with empty fields', async () => {
        const user = userEvent.setup();
        const handleSubmit = jest.fn();
        
        render(<LoginForm onSubmit={handleSubmit} />);
        
        await user.click(screen.getByRole('button', { name: 'Login' }));
        
        expect(handleSubmit).not.toHaveBeenCalled();
    });
});
```

### Testing with Context
```javascript
// ThemeContext.jsx
import React, { createContext, useContext, useState } from 'react';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
    const [theme, setTheme] = useState('light');
    
    const toggleTheme = () => {
        setTheme(prev => prev === 'light' ? 'dark' : 'light');
    };
    
    return (
        <ThemeContext.Provider value={{ theme, toggleTheme }}>
            {children}
        </ThemeContext.Provider>
    );
}

export function useTheme() {
    const context = useContext(ThemeContext);
    if (!context) {
        throw new Error('useTheme must be used within a ThemeProvider');
    }
    return context;
}

// ThemeToggle.jsx
import React from 'react';
import { useTheme } from './ThemeContext';

function ThemeToggle() {
    const { theme, toggleTheme } = useTheme();
    
    return (
        <button onClick={toggleTheme}>
            Switch to {theme === 'light' ? 'dark' : 'light'} theme
        </button>
    );
}

export default ThemeToggle;

// ThemeToggle.test.jsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { ThemeProvider } from './ThemeContext';
import ThemeToggle from './ThemeToggle';

const renderWithTheme = (component) => {
    return render(
        <ThemeProvider>
            {component}
        </ThemeProvider>
    );
};

describe('ThemeToggle', () => {
    test('should render toggle button', () => {
        renderWithTheme(<ThemeToggle />);
        expect(screen.getByRole('button')).toBeInTheDocument();
    });
    
    test('should toggle theme when clicked', () => {
        renderWithTheme(<ThemeToggle />);
        
        const button = screen.getByRole('button');
        expect(button).toHaveTextContent('Switch to dark theme');
        
        fireEvent.click(button);
        expect(button).toHaveTextContent('Switch to light theme');
    });
});
```

---

## Testing Hooks

### Testing Custom Hooks
```javascript
// useCounter.js
import { useState } from 'react';

export function useCounter(initialValue = 0) {
    const [count, setCount] = useState(initialValue);
    
    const increment = () => setCount(prev => prev + 1);
    const decrement = () => setCount(prev => prev - 1);
    const reset = () => setCount(initialValue);
    
    return { count, increment, decrement, reset };
}

// useCounter.test.js
import { renderHook, act } from '@testing-library/react';
import { useCounter } from './useCounter';

describe('useCounter', () => {
    test('should initialize with default value', () => {
        const { result } = renderHook(() => useCounter());
        expect(result.current.count).toBe(0);
    });
    
    test('should initialize with custom value', () => {
        const { result } = renderHook(() => useCounter(10));
        expect(result.current.count).toBe(10);
    });
    
    test('should increment count', () => {
        const { result } = renderHook(() => useCounter());
        
        act(() => {
            result.current.increment();
        });
        
        expect(result.current.count).toBe(1);
    });
    
    test('should decrement count', () => {
        const { result } = renderHook(() => useCounter(5));
        
        act(() => {
            result.current.decrement();
        });
        
        expect(result.current.count).toBe(4);
    });
    
    test('should reset count', () => {
        const { result } = renderHook(() => useCounter(10));
        
        act(() => {
            result.current.increment();
            result.current.increment();
        });
        
        expect(result.current.count).toBe(12);
        
        act(() => {
            result.current.reset();
        });
        
        expect(result.current.count).toBe(10);
    });
});
```

### Testing Async Hooks
```javascript
// useApi.js
import { useState, useEffect } from 'react';

export function useApi(url) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    
    useEffect(() => {
        const fetchData = async () => {
            try {
                setLoading(true);
                const response = await fetch(url);
                if (!response.ok) {
                    throw new Error('Failed to fetch');
                }
                const result = await response.json();
                setData(result);
            } catch (err) {
                setError(err.message);
            } finally {
                setLoading(false);
            }
        };
        
        fetchData();
    }, [url]);
    
    return { data, loading, error };
}

// useApi.test.js
import { renderHook, waitFor } from '@testing-library/react';
import { useApi } from './useApi';

// Mock fetch
global.fetch = jest.fn();

describe('useApi', () => {
    beforeEach(() => {
        fetch.mockClear();
    });
    
    test('should fetch data successfully', async () => {
        const mockData = { id: 1, name: 'Test' };
        fetch.mockResolvedValueOnce({
            ok: true,
            json: async () => mockData
        });
        
        const { result } = renderHook(() => useApi('/api/test'));
        
        expect(result.current.loading).toBe(true);
        expect(result.current.data).toBe(null);
        expect(result.current.error).toBe(null);
        
        await waitFor(() => {
            expect(result.current.loading).toBe(false);
        });
        
        expect(result.current.data).toEqual(mockData);
        expect(result.current.error).toBe(null);
    });
    
    test('should handle fetch error', async () => {
        fetch.mockRejectedValueOnce(new Error('Network error'));
        
        const { result } = renderHook(() => useApi('/api/test'));
        
        await waitFor(() => {
            expect(result.current.loading).toBe(false);
        });
        
        expect(result.current.data).toBe(null);
        expect(result.current.error).toBe('Network error');
    });
});
```

---

## Integration Testing

### Testing Component Interactions
```javascript
// TodoApp.jsx
import React, { useState } from 'react';
import TodoForm from './TodoForm';
import TodoList from './TodoList';

function TodoApp() {
    const [todos, setTodos] = useState([]);
    
    const addTodo = (text) => {
        setTodos(prev => [...prev, {
            id: Date.now(),
            text,
            completed: false
        }]);
    };
    
    const toggleTodo = (id) => {
        setTodos(prev => prev.map(todo =>
            todo.id === id ? { ...todo, completed: !todo.completed } : todo
        ));
    };
    
    const deleteTodo = (id) => {
        setTodos(prev => prev.filter(todo => todo.id !== id));
    };
    
    return (
        <div>
            <h1>Todo App</h1>
            <TodoForm onAdd={addTodo} />
            <TodoList 
                todos={todos}
                onToggle={toggleTodo}
                onDelete={deleteTodo}
            />
        </div>
    );
}

export default TodoApp;

// TodoApp.test.jsx
import React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import TodoApp from './TodoApp';

describe('TodoApp Integration', () => {
    test('should add todo and display it', async () => {
        const user = userEvent.setup();
        render(<TodoApp />);
        
        const input = screen.getByPlaceholderText('Add a todo');
        const addButton = screen.getByRole('button', { name: 'Add' });
        
        await user.type(input, 'Test todo');
        await user.click(addButton);
        
        expect(screen.getByText('Test todo')).toBeInTheDocument();
        expect(input).toHaveValue('');
    });
    
    test('should toggle todo completion', async () => {
        const user = userEvent.setup();
        render(<TodoApp />);
        
        // Add a todo
        await user.type(screen.getByPlaceholderText('Add a todo'), 'Test todo');
        await user.click(screen.getByRole('button', { name: 'Add' }));
        
        // Toggle completion
        const checkbox = screen.getByRole('checkbox');
        await user.click(checkbox);
        
        expect(checkbox).toBeChecked();
    });
    
    test('should delete todo', async () => {
        const user = userEvent.setup();
        render(<TodoApp />);
        
        // Add a todo
        await user.type(screen.getByPlaceholderText('Add a todo'), 'Test todo');
        await user.click(screen.getByRole('button', { name: 'Add' }));
        
        // Delete todo
        const deleteButton = screen.getByRole('button', { name: 'Delete' });
        await user.click(deleteButton);
        
        expect(screen.queryByText('Test todo')).not.toBeInTheDocument();
    });
});
```

### Testing with Router
```javascript
// App.jsx
import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Home from './Home';
import About from './About';
import Contact from './Contact';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/about" element={<About />} />
                <Route path="/contact" element={<Contact />} />
            </Routes>
        </BrowserRouter>
    );
}

export default App;

// App.test.jsx
import React from 'react';
import { render, screen } from '@testing-library/react';
import { BrowserRouter } from 'react-router-dom';
import App from './App';

const renderWithRouter = (component) => {
    return render(
        <BrowserRouter>
            {component}
        </BrowserRouter>
    );
};

describe('App Routing', () => {
    test('should render home page by default', () => {
        renderWithRouter(<App />);
        expect(screen.getByText('Home Page')).toBeInTheDocument();
    });
    
    test('should navigate to about page', () => {
        renderWithRouter(<App />);
        // Test navigation logic
    });
});
```

---

## Practice Exercises

### Exercise 1: Calculator Component
```javascript
// Calculator.jsx
import React, { useState } from 'react';

function Calculator() {
    const [display, setDisplay] = useState('0');
    const [previousValue, setPreviousValue] = useState(null);
    const [operation, setOperation] = useState(null);
    
    const handleNumber = (num) => {
        if (display === '0') {
            setDisplay(num.toString());
        } else {
            setDisplay(display + num);
        }
    };
    
    const handleOperation = (op) => {
        setPreviousValue(parseFloat(display));
        setOperation(op);
        setDisplay('0');
    };
    
    const handleEquals = () => {
        if (previousValue !== null && operation !== null) {
            const currentValue = parseFloat(display);
            let result;
            
            switch (operation) {
                case '+':
                    result = previousValue + currentValue;
                    break;
                case '-':
                    result = previousValue - currentValue;
                    break;
                case '*':
                    result = previousValue * currentValue;
                    break;
                case '/':
                    result = previousValue / currentValue;
                    break;
                default:
                    return;
            }
            
            setDisplay(result.toString());
            setPreviousValue(null);
            setOperation(null);
        }
    };
    
    const handleClear = () => {
        setDisplay('0');
        setPreviousValue(null);
        setOperation(null);
    };
    
    return (
        <div className="calculator">
            <div className="display">{display}</div>
            <div className="buttons">
                <button onClick={handleClear}>C</button>
                <button onClick={() => handleOperation('/')}>/</button>
                <button onClick={() => handleOperation('*')}>*</button>
                <button onClick={() => handleOperation('-')}>-</button>
                
                <button onClick={() => handleNumber(7)}>7</button>
                <button onClick={() => handleNumber(8)}>8</button>
                <button onClick={() => handleNumber(9)}>9</button>
                <button onClick={() => handleOperation('+')}>+</button>
                
                <button onClick={() => handleNumber(4)}>4</button>
                <button onClick={() => handleNumber(5)}>5</button>
                <button onClick={() => handleNumber(6)}>6</button>
                <button onClick={handleEquals}>=</button>
                
                <button onClick={() => handleNumber(1)}>1</button>
                <button onClick={() => handleNumber(2)}>2</button>
                <button onClick={() => handleNumber(3)}>3</button>
                <button onClick={() => handleNumber(0)}>0</button>
            </div>
        </div>
    );
}

export default Calculator;

// Calculator.test.jsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Calculator from './Calculator';

describe('Calculator', () => {
    test('should display initial value', () => {
        render(<Calculator />);
        expect(screen.getByText('0')).toBeInTheDocument();
    });
    
    test('should display numbers when clicked', async () => {
        const user = userEvent.setup();
        render(<Calculator />);
        
        await user.click(screen.getByText('1'));
        await user.click(screen.getByText('2'));
        await user.click(screen.getByText('3'));
        
        expect(screen.getByText('123')).toBeInTheDocument();
    });
    
    test('should perform addition', async () => {
        const user = userEvent.setup();
        render(<Calculator />);
        
        await user.click(screen.getByText('2'));
        await user.click(screen.getByText('+'));
        await user.click(screen.getByText('3'));
        await user.click(screen.getByText('='));
        
        expect(screen.getByText('5')).toBeInTheDocument();
    });
    
    test('should clear display', async () => {
        const user = userEvent.setup();
        render(<Calculator />);
        
        await user.click(screen.getByText('1'));
        await user.click(screen.getByText('2'));
        await user.click(screen.getByText('C'));
        
        expect(screen.getByText('0')).toBeInTheDocument();
    });
});
```

### Exercise 2: User Management Hook
```javascript
// useUsers.js
import { useState, useEffect } from 'react';

export function useUsers() {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);
    
    const fetchUsers = async () => {
        setLoading(true);
        setError(null);
        try {
            const response = await fetch('/api/users');
            if (!response.ok) {
                throw new Error('Failed to fetch users');
            }
            const data = await response.json();
            setUsers(data);
        } catch (err) {
            setError(err.message);
        } finally {
            setLoading(false);
        }
    };
    
    const addUser = async (userData) => {
        setLoading(true);
        setError(null);
        try {
            const response = await fetch('/api/users', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(userData)
            });
            if (!response.ok) {
                throw new Error('Failed to add user');
            }
            const newUser = await response.json();
            setUsers(prev => [...prev, newUser]);
        } catch (err) {
            setError(err.message);
        } finally {
            setLoading(false);
        }
    };
    
    const deleteUser = async (id) => {
        setLoading(true);
        setError(null);
        try {
            const response = await fetch(`/api/users/${id}`, {
                method: 'DELETE'
            });
            if (!response.ok) {
                throw new Error('Failed to delete user');
            }
            setUsers(prev => prev.filter(user => user.id !== id));
        } catch (err) {
            setError(err.message);
        } finally {
            setLoading(false);
        }
    };
    
    useEffect(() => {
        fetchUsers();
    }, []);
    
    return {
        users,
        loading,
        error,
        addUser,
        deleteUser,
        refetch: fetchUsers
    };
}

// useUsers.test.js
import { renderHook, waitFor } from '@testing-library/react';
import { useUsers } from './useUsers';

global.fetch = jest.fn();

describe('useUsers', () => {
    beforeEach(() => {
        fetch.mockClear();
    });
    
    test('should fetch users on mount', async () => {
        const mockUsers = [
            { id: 1, name: 'John Doe', email: 'john@example.com' },
            { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
        ];
        
        fetch.mockResolvedValueOnce({
            ok: true,
            json: async () => mockUsers
        });
        
        const { result } = renderHook(() => useUsers());
        
        expect(result.current.loading).toBe(true);
        
        await waitFor(() => {
            expect(result.current.loading).toBe(false);
        });
        
        expect(result.current.users).toEqual(mockUsers);
        expect(result.current.error).toBe(null);
    });
    
    test('should handle fetch error', async () => {
        fetch.mockRejectedValueOnce(new Error('Network error'));
        
        const { result } = renderHook(() => useUsers());
        
        await waitFor(() => {
            expect(result.current.loading).toBe(false);
        });
        
        expect(result.current.users).toEqual([]);
        expect(result.current.error).toBe('Network error');
    });
    
    test('should add user', async () => {
        const mockUser = { id: 1, name: 'John Doe', email: 'john@example.com' };
        const newUser = { id: 2, name: 'Jane Smith', email: 'jane@example.com' };
        
        fetch.mockResolvedValueOnce({
            ok: true,
            json: async () => [mockUser]
        });
        
        const { result } = renderHook(() => useUsers());
        
        await waitFor(() => {
            expect(result.current.loading).toBe(false);
        });
        
        fetch.mockResolvedValueOnce({
            ok: true,
            json: async () => newUser
        });
        
        await result.current.addUser(newUser);
        
        expect(result.current.users).toHaveLength(2);
        expect(result.current.users[1]).toEqual(newUser);
    });
});
```

---

## Summary

### Key Concepts Learned
- ✅ **Testing Fundamentals** - Types of tests and best practices
- ✅ **Jest Testing** - JavaScript testing framework
- ✅ **React Testing Library** - Testing React components
- ✅ **Testing Hooks** - Testing custom hooks
- ✅ **Integration Testing** - Testing component interactions

### Best Practices
- Test behavior, not implementation
- Use descriptive test names
- Follow Arrange, Act, Assert pattern
- Test edge cases and error conditions
- Keep tests simple and focused
- Use proper mocking and cleanup

### Common Mistakes
- Testing implementation details
- Not testing error cases
- Over-mocking dependencies
- Not cleaning up after tests
- Writing tests that are too complex

---

**Excellent! You've mastered testing. Ready for Deployment? 🚀**

**Next Tutorial:** [Deployment](./22-deployment.md)
