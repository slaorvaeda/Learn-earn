# 06. Advanced React Hooks

## 🎯 Learning Objectives
- Master useState and useEffect
- Use useContext for global state
- Implement useReducer
- Create custom hooks
- Optimize with useMemo and useCallback

## 🎣 Core Hooks

### useState Deep Dive
```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  
  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);
  const reset = () => setCount(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

### useEffect Patterns
```jsx
import { useState, useEffect } from 'react';

function DataFetcher() {
  const [data, setData] = useState(null);
  
  // Run on mount
  useEffect(() => {
    fetchData();
  }, []);
  
  // Run when id changes
  const [id, setId] = useState(1);
  useEffect(() => {
    fetchDataById(id);
  }, [id]);
  
  // Cleanup function
  useEffect(() => {
    const interval = setInterval(() => {
      console.log('Interval');
    }, 1000);
    
    return () => clearInterval(interval);
  }, []);
}
```

## 🌍 useContext Hook

### Create Context
```jsx
import { createContext, useContext } from 'react';

const UserContext = createContext();

// Provider
function App() {
  const [user, setUser] = useState(null);
  
  return (
    <UserContext.Provider value={{ user, setUser }}>
      <Navbar />
      <Content />
    </UserContext.Provider>
  );
}

// Consumer
function Navbar() {
  const { user } = useContext(UserContext);
  return <div>Welcome, {user?.name}</div>;
}
```

## 🔄 useReducer Hook

### Complex State Management
```jsx
import { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    case 'reset':
      return { count: 0 };
    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  
  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </div>
  );
}
```

## 🔗 Next Steps

Hooks mastered! Let's learn authentication!

---

**Key Takeaways:**
- useState manages component state
- useEffect handles side effects and cleanup
- useContext shares global state
- useReducer manages complex state
- Custom hooks reuse logic

**Next Tutorial:** [07-Authentication.md](./07-Authentication.md)
