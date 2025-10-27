# Backend Integration - Tutorial 25 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [REST API Integration](#rest-api-integration)
3. [GraphQL Integration](#graphql-integration)
4. [Authentication & Authorization](#authentication--authorization)
5. [Error Handling](#error-handling)
6. [Real-time Communication](#real-time-communication)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Backend integration is **essential for building full-stack applications**. It involves connecting your frontend to APIs, handling authentication, and managing data flow.

### What You'll Learn
- ✅ **REST API Integration** - Working with RESTful APIs
- ✅ **GraphQL Integration** - Using GraphQL for data fetching
- ✅ **Authentication & Authorization** - User management and security
- ✅ **Error Handling** - Managing API errors gracefully
- ✅ **Real-time Communication** - WebSockets and real-time updates

---

## REST API Integration

### Basic API Calls
```javascript
// api.js
const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:3001/api';

class ApiClient {
  constructor(baseURL = API_BASE_URL) {
    this.baseURL = baseURL;
  }

  async request(endpoint, options = {}) {
    const url = `${this.baseURL}${endpoint}`;
    const config = {
      headers: {
        'Content-Type': 'application/json',
        ...options.headers,
      },
      ...options,
    };

    try {
      const response = await fetch(url, config);
      
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      
      const data = await response.json();
      return data;
    } catch (error) {
      console.error('API request failed:', error);
      throw error;
    }
  }

  // GET request
  async get(endpoint) {
    return this.request(endpoint, { method: 'GET' });
  }

  // POST request
  async post(endpoint, data) {
    return this.request(endpoint, {
      method: 'POST',
      body: JSON.stringify(data),
    });
  }

  // PUT request
  async put(endpoint, data) {
    return this.request(endpoint, {
      method: 'PUT',
      body: JSON.stringify(data),
    });
  }

  // DELETE request
  async delete(endpoint) {
    return this.request(endpoint, { method: 'DELETE' });
  }
}

export const apiClient = new ApiClient();
```

### Using API Client
```javascript
// userService.js
import { apiClient } from './api';

export const userService = {
  // Get all users
  async getUsers() {
    return apiClient.get('/users');
  },

  // Get user by ID
  async getUser(id) {
    return apiClient.get(`/users/${id}`);
  },

  // Create user
  async createUser(userData) {
    return apiClient.post('/users', userData);
  },

  // Update user
  async updateUser(id, userData) {
    return apiClient.put(`/users/${id}`, userData);
  },

  // Delete user
  async deleteUser(id) {
    return apiClient.delete(`/users/${id}`);
  },
};
```

### React Query Integration
```bash
npm install @tanstack/react-query
```

```javascript
// App.js
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000, // 5 minutes
      cacheTime: 10 * 60 * 1000, // 10 minutes
    },
  },
});

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <UserList />
    </QueryClientProvider>
  );
}

// UserList.js
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { userService } from './userService';

const UserList = () => {
  const queryClient = useQueryClient();
  
  // Fetch users
  const { data: users, isLoading, error } = useQuery({
    queryKey: ['users'],
    queryFn: userService.getUsers,
  });

  // Create user mutation
  const createUserMutation = useMutation({
    mutationFn: userService.createUser,
    onSuccess: () => {
      queryClient.invalidateQueries(['users']);
    },
  });

  // Delete user mutation
  const deleteUserMutation = useMutation({
    mutationFn: userService.deleteUser,
    onSuccess: () => {
      queryClient.invalidateQueries(['users']);
    },
  });

  const handleCreateUser = (userData) => {
    createUserMutation.mutate(userData);
  };

  const handleDeleteUser = (id) => {
    deleteUserMutation.mutate(id);
  };

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <div>
      <h2>Users</h2>
      <button onClick={() => handleCreateUser({ name: 'New User', email: 'new@example.com' })}>
        Add User
      </button>
      <ul>
        {users?.map(user => (
          <li key={user.id}>
            {user.name} - {user.email}
            <button onClick={() => handleDeleteUser(user.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
};
```

---

## GraphQL Integration

### Apollo Client Setup
```bash
npm install @apollo/client graphql
```

```javascript
// apolloClient.js
import { ApolloClient, InMemoryCache, createHttpLink } from '@apollo/client';
import { setContext } from '@apollo/client/link/context';

const httpLink = createHttpLink({
  uri: process.env.REACT_APP_GRAPHQL_URL || 'http://localhost:4000/graphql',
});

const authLink = setContext((_, { headers }) => {
  const token = localStorage.getItem('token');
  return {
    headers: {
      ...headers,
      authorization: token ? `Bearer ${token}` : '',
    }
  };
});

export const apolloClient = new ApolloClient({
  link: authLink.concat(httpLink),
  cache: new InMemoryCache(),
});

// App.js
import { ApolloProvider } from '@apollo/client';
import { apolloClient } from './apolloClient';

function App() {
  return (
    <ApolloProvider client={apolloClient}>
      <UserList />
    </ApolloProvider>
  );
}
```

### GraphQL Queries and Mutations
```javascript
// queries.js
import { gql } from '@apollo/client';

export const GET_USERS = gql`
  query GetUsers {
    users {
      id
      name
      email
      posts {
        id
        title
        content
      }
    }
  }
`;

export const GET_USER = gql`
  query GetUser($id: ID!) {
    user(id: $id) {
      id
      name
      email
      posts {
        id
        title
        content
      }
    }
  }
`;

export const CREATE_USER = gql`
  mutation CreateUser($input: UserInput!) {
    createUser(input: $input) {
      id
      name
      email
    }
  }
`;

export const UPDATE_USER = gql`
  mutation UpdateUser($id: ID!, $input: UserInput!) {
    updateUser(id: $id, input: $input) {
      id
      name
      email
    }
  }
`;

export const DELETE_USER = gql`
  mutation DeleteUser($id: ID!) {
    deleteUser(id: $id)
  }
`;
```

### Using GraphQL in Components
```javascript
// UserList.js
import { useQuery, useMutation } from '@apollo/client';
import { GET_USERS, CREATE_USER, DELETE_USER } from './queries';

const UserList = () => {
  const { data, loading, error } = useQuery(GET_USERS);
  
  const [createUser] = useMutation(CREATE_USER, {
    refetchQueries: [{ query: GET_USERS }],
  });
  
  const [deleteUser] = useMutation(DELETE_USER, {
    refetchQueries: [{ query: GET_USERS }],
  });

  const handleCreateUser = async () => {
    try {
      await createUser({
        variables: {
          input: {
            name: 'New User',
            email: 'new@example.com'
          }
        }
      });
    } catch (error) {
      console.error('Error creating user:', error);
    }
  };

  const handleDeleteUser = async (id) => {
    try {
      await deleteUser({
        variables: { id }
      });
    } catch (error) {
      console.error('Error deleting user:', error);
    }
  };

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <div>
      <h2>Users</h2>
      <button onClick={handleCreateUser}>Add User</button>
      <ul>
        {data?.users?.map(user => (
          <li key={user.id}>
            {user.name} - {user.email}
            <button onClick={() => handleDeleteUser(user.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
};
```

---

## Authentication & Authorization

### JWT Authentication
```javascript
// authService.js
class AuthService {
  constructor() {
    this.token = localStorage.getItem('token');
    this.user = JSON.parse(localStorage.getItem('user') || 'null');
  }

  async login(credentials) {
    try {
      const response = await fetch('/api/auth/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(credentials)
      });

      if (!response.ok) {
        throw new Error('Login failed');
      }

      const { token, user } = await response.json();
      
      this.token = token;
      this.user = user;
      
      localStorage.setItem('token', token);
      localStorage.setItem('user', JSON.stringify(user));
      
      return { success: true, user };
    } catch (error) {
      return { success: false, error: error.message };
    }
  }

  async logout() {
    this.token = null;
    this.user = null;
    
    localStorage.removeItem('token');
    localStorage.removeItem('user');
  }

  isAuthenticated() {
    return !!this.token;
  }

  getAuthHeaders() {
    return {
      'Authorization': `Bearer ${this.token}`,
      'Content-Type': 'application/json'
    };
  }
}

export const authService = new AuthService();
```

### Protected Routes
```javascript
// ProtectedRoute.js
import { Navigate } from 'react-router-dom';
import { authService } from './authService';

const ProtectedRoute = ({ children }) => {
  if (!authService.isAuthenticated()) {
    return <Navigate to="/login" replace />;
  }
  
  return children;
};

export default ProtectedRoute;

// Usage
<Routes>
  <Route path="/login" element={<Login />} />
  <Route path="/dashboard" element={
    <ProtectedRoute>
      <Dashboard />
    </ProtectedRoute>
  } />
</Routes>
```

### Role-based Authorization
```javascript
// RoleBasedRoute.js
import { Navigate } from 'react-router-dom';
import { authService } from './authService';

const RoleBasedRoute = ({ children, requiredRole }) => {
  const user = authService.user;
  
  if (!user) {
    return <Navigate to="/login" replace />;
  }
  
  if (user.role !== requiredRole) {
    return <Navigate to="/unauthorized" replace />;
  }
  
  return children;
};

export default RoleBasedRoute;

// Usage
<RoleBasedRoute requiredRole="admin">
  <AdminPanel />
</RoleBasedRoute>
```

---

## Error Handling

### API Error Handling
```javascript
// errorHandler.js
class ApiError extends Error {
  constructor(message, status, data) {
    super(message);
    this.name = 'ApiError';
    this.status = status;
    this.data = data;
  }
}

export const handleApiError = (error) => {
  if (error instanceof ApiError) {
    switch (error.status) {
      case 401:
        return 'Unauthorized. Please login again.';
      case 403:
        return 'Forbidden. You do not have permission.';
      case 404:
        return 'Resource not found.';
      case 500:
        return 'Server error. Please try again later.';
      default:
        return error.message;
    }
  }
  
  return 'An unexpected error occurred.';
};

// api.js
export const apiClient = {
  async request(endpoint, options = {}) {
    try {
      const response = await fetch(endpoint, options);
      
      if (!response.ok) {
        const errorData = await response.json().catch(() => ({}));
        throw new ApiError(
          errorData.message || 'Request failed',
          response.status,
          errorData
        );
      }
      
      return await response.json();
    } catch (error) {
      if (error instanceof ApiError) {
        throw error;
      }
      throw new ApiError('Network error', 0, { originalError: error });
    }
  }
};
```

### React Error Boundary
```javascript
// ErrorBoundary.js
import React from 'react';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-boundary">
          <h2>Something went wrong.</h2>
          <details>
            {this.state.error && this.state.error.toString()}
          </details>
        </div>
      );
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

---

## Real-time Communication

### WebSocket Integration
```javascript
// useWebSocket.js
import { useEffect, useState, useRef } from 'react';

const useWebSocket = (url) => {
  const [socket, setSocket] = useState(null);
  const [lastMessage, setLastMessage] = useState(null);
  const [readyState, setReadyState] = useState('CONNECTING');
  const ws = useRef(null);

  useEffect(() => {
    ws.current = new WebSocket(url);
    
    ws.current.onopen = () => {
      setReadyState('OPEN');
      setSocket(ws.current);
    };
    
    ws.current.onclose = () => {
      setReadyState('CLOSED');
      setSocket(null);
    };
    
    ws.current.onmessage = (event) => {
      setLastMessage(JSON.parse(event.data));
    };
    
    ws.current.onerror = (error) => {
      console.error('WebSocket error:', error);
    };

    return () => {
      ws.current.close();
    };
  }, [url]);

  const sendMessage = (message) => {
    if (ws.current && ws.current.readyState === WebSocket.OPEN) {
      ws.current.send(JSON.stringify(message));
    }
  };

  return { socket, lastMessage, readyState, sendMessage };
};

export default useWebSocket;

// Usage
const ChatComponent = () => {
  const { lastMessage, sendMessage } = useWebSocket('ws://localhost:8080');
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    if (lastMessage) {
      setMessages(prev => [...prev, lastMessage]);
    }
  }, [lastMessage]);

  const handleSendMessage = (text) => {
    sendMessage({ type: 'message', text });
  };

  return (
    <div>
      <div>
        {messages.map((msg, index) => (
          <div key={index}>{msg.text}</div>
        ))}
      </div>
      <input
        onKeyPress={(e) => {
          if (e.key === 'Enter') {
            handleSendMessage(e.target.value);
            e.target.value = '';
          }
        }}
      />
    </div>
  );
};
```

### Socket.io Integration
```bash
npm install socket.io-client
```

```javascript
// socketService.js
import io from 'socket.io-client';

class SocketService {
  constructor() {
    this.socket = null;
  }

  connect(token) {
    this.socket = io(process.env.REACT_APP_SOCKET_URL, {
      auth: { token }
    });

    this.socket.on('connect', () => {
      console.log('Connected to server');
    });

    this.socket.on('disconnect', () => {
      console.log('Disconnected from server');
    });
  }

  disconnect() {
    if (this.socket) {
      this.socket.disconnect();
      this.socket = null;
    }
  }

  emit(event, data) {
    if (this.socket) {
      this.socket.emit(event, data);
    }
  }

  on(event, callback) {
    if (this.socket) {
      this.socket.on(event, callback);
    }
  }

  off(event, callback) {
    if (this.socket) {
      this.socket.off(event, callback);
    }
  }
}

export const socketService = new SocketService();
```

---

## Practice Exercises

### Exercise 1: Complete CRUD API Integration
```javascript
// todoService.js
import { apiClient } from './api';

export const todoService = {
  async getTodos() {
    return apiClient.get('/todos');
  },

  async getTodo(id) {
    return apiClient.get(`/todos/${id}`);
  },

  async createTodo(todoData) {
    return apiClient.post('/todos', todoData);
  },

  async updateTodo(id, todoData) {
    return apiClient.put(`/todos/${id}`, todoData);
  },

  async deleteTodo(id) {
    return apiClient.delete(`/todos/${id}`);
  },

  async toggleTodo(id) {
    return apiClient.patch(`/todos/${id}/toggle`);
  }
};

// TodoApp.js
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { todoService } from './todoService';

const TodoApp = () => {
  const queryClient = useQueryClient();
  
  const { data: todos, isLoading, error } = useQuery({
    queryKey: ['todos'],
    queryFn: todoService.getTodos,
  });

  const createTodoMutation = useMutation({
    mutationFn: todoService.createTodo,
    onSuccess: () => {
      queryClient.invalidateQueries(['todos']);
    },
  });

  const updateTodoMutation = useMutation({
    mutationFn: ({ id, data }) => todoService.updateTodo(id, data),
    onSuccess: () => {
      queryClient.invalidateQueries(['todos']);
    },
  });

  const deleteTodoMutation = useMutation({
    mutationFn: todoService.deleteTodo,
    onSuccess: () => {
      queryClient.invalidateQueries(['todos']);
    },
  });

  const toggleTodoMutation = useMutation({
    mutationFn: todoService.toggleTodo,
    onSuccess: () => {
      queryClient.invalidateQueries(['todos']);
    },
  });

  const handleCreateTodo = (text) => {
    createTodoMutation.mutate({ text, completed: false });
  };

  const handleToggleTodo = (id) => {
    toggleTodoMutation.mutate(id);
  };

  const handleDeleteTodo = (id) => {
    deleteTodoMutation.mutate(id);
  };

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <div>
      <h2>Todos</h2>
      <TodoForm onSubmit={handleCreateTodo} />
      <TodoList
        todos={todos}
        onToggle={handleToggleTodo}
        onDelete={handleDeleteTodo}
      />
    </div>
  );
};
```

### Exercise 2: Authentication Flow
```javascript
// AuthContext.js
import React, { createContext, useContext, useState, useEffect } from 'react';
import { authService } from './authService';

const AuthContext = createContext();

export const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const checkAuth = async () => {
      if (authService.isAuthenticated()) {
        setUser(authService.user);
      }
      setLoading(false);
    };
    
    checkAuth();
  }, []);

  const login = async (credentials) => {
    const result = await authService.login(credentials);
    if (result.success) {
      setUser(result.user);
    }
    return result;
  };

  const logout = async () => {
    await authService.logout();
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, login, logout, loading }}>
      {children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  return context;
};
```

---

## Summary

### Key Concepts Learned
- ✅ **REST API Integration** - Working with RESTful APIs
- ✅ **GraphQL Integration** - Using GraphQL for data fetching
- ✅ **Authentication & Authorization** - User management and security
- ✅ **Error Handling** - Managing API errors gracefully
- ✅ **Real-time Communication** - WebSockets and real-time updates

### Best Practices
- Use proper error handling for all API calls
- Implement authentication and authorization
- Use React Query for data fetching and caching
- Handle loading and error states
- Implement proper security measures

### Common Mistakes
- Not handling API errors properly
- Not implementing proper authentication
- Not using proper loading states
- Not handling network errors
- Not implementing proper security measures

---

**Excellent! You've mastered backend integration. Ready for Mobile Development? 🚀**

**Next Tutorial:** [Mobile Development](./26-mobile-development.md)
