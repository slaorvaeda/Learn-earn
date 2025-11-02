# 03. React Fundamentals - Frontend Development

## 🎯 Learning Objectives
- Understand React components and JSX
- Learn useState and useEffect hooks
- Handle events and forms
- Pass data with props
- Work with conditional rendering

## ⚛️ What is React?

React is a JavaScript library for building user interfaces.

**Key Features:**
- **Component-Based**: Reusable UI pieces
- **Declarative**: Describe UI, React renders
- **Virtual DOM**: Efficient updates
- **Unidirectional Data Flow**: Props down, events up

## 🚀 Setting Up React

### Create React App
```bash
# Create new React app
npx create-react-app my-react-app

# Navigate to app
cd my-react-app

# Start development server
npm start

# Or with Vite (faster)
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm run dev
```

### Project Structure
```
my-react-app/
├── public/
│   └── index.html
├── src/
│   ├── components/
│   │   ├── Header.js
│   │   ├── UserCard.js
│   │   └── TodoList.js
│   ├── pages/
│   ├── App.js
│   ├── index.js
│   └── styles/
└── package.json
```

## 🧩 Components and JSX

### Basic Component
```jsx
// App.js
import React from 'react';

function App() {
  return (
    <div className="App">
      <h1>Hello React!</h1>
      <p>Welcome to React development</p>
    </div>
  );
}

export default App;
```

### Component with Props
```jsx
// UserCard.js
function UserCard(props) {
  return (
    <div className="user-card">
      <h2>{props.name}</h2>
      <p>Email: {props.email}</p>
      <p>Age: {props.age}</p>
    </div>
  );
}

// Usage
<UserCard name="Alice" email="alice@example.com" age={25} />
```

### Arrow Function Components
```jsx
const UserCard = ({ name, email, age }) => {
  return (
    <div className="user-card">
      <h2>{name}</h2>
      <p>{email}</p>
      <p>{age} years old</p>
    </div>
  );
};

export default UserCard;
```

## 🎣 React Hooks

### useState Hook
```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(count - 1)}>-</button>
    </div>
  );
}
```

### Multiple useState
```jsx
function Form() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [age, setAge] = useState(0);
  
  return (
    <form>
      <input 
        value={name} 
        onChange={e => setName(e.target.value)} 
        placeholder="Name"
      />
      <input 
        value={email} 
        onChange={e => setEmail(e.target.value)} 
        placeholder="Email"
      />
      <input 
        type="number"
        value={age} 
        onChange={e => setAge(e.target.value)} 
      />
    </form>
  );
}
```

### useEffect Hook
```jsx
import { useState, useEffect } from 'react';

function UserList() {
  const [users, setUsers] = useState([]);
  
  useEffect(() => {
    // Fetch users when component mounts
    fetch('http://localhost:5000/api/users')
      .then(res => res.json())
      .then(data => setUsers(data));
  }, []); // Empty deps = run once
  
  return (
    <div>
      {users.map(user => (
        <div key={user.id}>{user.name}</div>
      ))}
    </div>
  );
}
```

## 📝 Forms and Events

### Controlled Components
```jsx
function LoginForm() {
  const [formData, setFormData] = useState({
    email: '',
    password: ''
  });
  
  const handleChange = (e) => {
    setFormData({
      ...formData,
      [e.target.name]: e.target.value
    });
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form data:', formData);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        name="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <input
        type="password"
        name="password"
        value={formData.password}
        onChange={handleChange}
        placeholder="Password"
      />
      <button type="submit">Login</button>
    </form>
  );
}
```

### Event Handlers
```jsx
function Button() {
  const handleClick = () => {
    alert('Button clicked!');
  };
  
  return <button onClick={handleClick}>Click Me</button>;
}
```

## 🔄 Conditional Rendering

### If/Else
```jsx
function Greeting({ isLoggedIn }) {
  if (isLoggedIn) {
    return <h1>Welcome back!</h1>;
  } else {
    return <h1>Please log in</h1>;
  }
}
```

### Ternary Operator
```jsx
function Status({ isOnline }) {
  return (
    <div>
      <p>Status: {isOnline ? 'Online' : 'Offline'}</p>
    </div>
  );
}
```

### Logical AND
```jsx
function Notification({ message }) {
  return (
    <div>
      {message && <div className="alert">{message}</div>}
    </div>
  );
}
```

## 📊 Lists and Keys

### Rendering Lists
```jsx
function UserList({ users }) {
  return (
    <div>
      {users.map(user => (
        <div key={user.id}>
          <h3>{user.name}</h3>
          <p>{user.email}</p>
        </div>
      ))}
    </div>
  );
}

// Usage
const users = [
  { id: 1, name: 'Alice', email: 'alice@example.com' },
  { id: 2, name: 'Bob', email: 'bob@example.com' }
];

<UserList users={users} />
```

### Filter and Map
```jsx
function ActiveUsers({ users }) {
  const active = users.filter(user => user.isActive);
  
  return (
    <div>
      {active.map(user => (
        <UserCard key={user.id} user={user} />
      ))}
    </div>
  );
}
```

## 🔗 API Integration

### Fetch Data
```jsx
import { useState, useEffect } from 'react';

function ProductList() {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    fetch('http://localhost:5000/api/products')
      .then(res => {
        if (!res.ok) throw new Error('Failed to fetch');
        return res.json();
      })
      .then(data => {
        setProducts(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err.message);
        setLoading(false);
      });
  }, []);
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <div>
      {products.map(product => (
        <ProductCard key={product._id} product={product} />
      ))}
    </div>
  );
}
```

### Axios Integration
```jsx
import axios from 'axios';
import { useState, useEffect } from 'react';

function UserList() {
  const [users, setUsers] = useState([]);
  
  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await axios.get('http://localhost:5000/api/users');
        setUsers(response.data);
      } catch (error) {
        console.error('Error fetching users:', error);
      }
    };
    
    fetchUsers();
  }, []);
  
  return (
    <div>
      {users.map(user => (
        <div key={user._id}>{user.name}</div>
      ))}
    </div>
  );
}
```

### POST Request
```jsx
function CreateUser() {
  const [formData, setFormData] = useState({ name: '', email: '' });
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    
    try {
      const response = await axios.post(
        'http://localhost:5000/api/users',
        formData
      );
      console.log('User created:', response.data);
      setFormData({ name: '', email: '' });
    } catch (error) {
      console.error('Error creating user:', error);
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        value={formData.name}
        onChange={e => setFormData({...formData, name: e.target.value})}
      />
      <input
        value={formData.email}
        onChange={e => setFormData({...formData, email: e.target.value})}
      />
      <button type="submit">Create User</button>
    </form>
  );
}
```

## 🧪 Complete Component Example

### Todo App Component
```jsx
import { useState, useEffect } from 'react';
import axios from 'axios';

function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState('');
  
  useEffect(() => {
    fetchTodos();
  }, []);
  
  const fetchTodos = async () => {
    const response = await axios.get('http://localhost:5000/api/todos');
    setTodos(response.data);
  };
  
  const addTodo = async (e) => {
    e.preventDefault();
    const response = await axios.post('http://localhost:5000/api/todos', {
      text: input,
      completed: false
    });
    setTodos([...todos, response.data]);
    setInput('');
  };
  
  const toggleTodo = async (id) => {
    await axios.put(`http://localhost:5000/api/todos/${id}/toggle`);
    setTodos(todos.map(todo =>
      todo._id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };
  
  const deleteTodo = async (id) => {
    await axios.delete(`http://localhost:5000/api/todos/${id}`);
    setTodos(todos.filter(todo => todo._id !== id));
  };
  
  return (
    <div>
      <h1>Todo App</h1>
      <form onSubmit={addTodo}>
        <input
          value={input}
          onChange={e => setInput(e.target.value)}
          placeholder="Add todo"
        />
        <button type="submit">Add</button>
      </form>
      
      <ul>
        {todos.map(todo => (
          <li key={todo._id}>
            <span
              onClick={() => toggleTodo(todo._id)}
              style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}
            >
              {todo.text}
            </span>
            <button onClick={() => deleteTodo(todo._id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default TodoApp;
```

## 🔗 Next Steps

React fundamentals mastered! Let's build Node.js backend!

---

**Key Takeaways:**
- Components are reusable UI building blocks
- useState manages component state
- useEffect handles side effects
- Controlled forms for user input
- Conditional rendering is powerful
- Map over arrays to render lists
- Integrate APIs with axios/fetch

**Next Tutorial:** [04-Node.js-Backend.md](./04-Node.js-Backend.md)
