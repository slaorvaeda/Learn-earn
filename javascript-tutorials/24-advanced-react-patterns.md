# Advanced React Patterns - Tutorial 24 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Higher-Order Components (HOCs)](#higher-order-components-hocs)
3. [Render Props](#render-props)
4. [Compound Components](#compound-components)
5. [Custom Hooks Patterns](#custom-hooks-patterns)
6. [Context Patterns](#context-patterns)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Advanced React patterns help you **write more reusable, maintainable, and flexible code**. These patterns solve common problems and improve code organization.

### What You'll Learn
- ✅ **Higher-Order Components** - Reusable component logic
- ✅ **Render Props** - Sharing code between components
- ✅ **Compound Components** - Building flexible component APIs
- ✅ **Custom Hooks Patterns** - Reusable stateful logic
- ✅ **Context Patterns** - Advanced state management

---

## Higher-Order Components (HOCs)

### Basic HOC Pattern
```javascript
// withLoading.js
import React from 'react';

const withLoading = (WrappedComponent) => {
  return function WithLoadingComponent({ isLoading, ...props }) {
    if (isLoading) {
      return <div>Loading...</div>;
    }
    return <WrappedComponent {...props} />;
  };
};

export default withLoading;

// Usage
const UserProfile = ({ user }) => (
  <div>
    <h2>{user.name}</h2>
    <p>{user.email}</p>
  </div>
);

const UserProfileWithLoading = withLoading(UserProfile);

// In your component
<UserProfileWithLoading isLoading={loading} user={user} />
```

### HOC with State
```javascript
// withCounter.js
import React, { useState } from 'react';

const withCounter = (WrappedComponent) => {
  return function WithCounterComponent(props) {
    const [count, setCount] = useState(0);
    
    const increment = () => setCount(prev => prev + 1);
    const decrement = () => setCount(prev => prev - 1);
    const reset = () => setCount(0);
    
    return (
      <WrappedComponent
        {...props}
        count={count}
        increment={increment}
        decrement={decrement}
        reset={reset}
      />
    );
  };
};

export default withCounter;

// Usage
const Counter = ({ count, increment, decrement, reset }) => (
  <div>
    <p>Count: {count}</p>
    <button onClick={increment}>+</button>
    <button onClick={decrement}>-</button>
    <button onClick={reset}>Reset</button>
  </div>
);

const CounterWithState = withCounter(Counter);
```

### HOC with Data Fetching
```javascript
// withData.js
import React, { useState, useEffect } from 'react';

const withData = (url) => (WrappedComponent) => {
  return function WithDataComponent(props) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    
    useEffect(() => {
      const fetchData = async () => {
        try {
          setLoading(true);
          const response = await fetch(url);
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
    
    return (
      <WrappedComponent
        {...props}
        data={data}
        loading={loading}
        error={error}
      />
    );
  };
};

export default withData;

// Usage
const UserList = ({ data, loading, error }) => {
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <ul>
      {data?.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
};

const UserListWithData = withData('/api/users')(UserList);
```

---

## Render Props

### Basic Render Props Pattern
```javascript
// DataProvider.js
import React, { useState, useEffect } from 'react';

const DataProvider = ({ url, render }) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch(url);
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
  
  return render({ data, loading, error });
};

export default DataProvider;

// Usage
<DataProvider
  url="/api/users"
  render={({ data, loading, error }) => {
    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    
    return (
      <ul>
        {data?.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    );
  }}
/>
```

### Render Props with Children
```javascript
// MouseTracker.js
import React, { useState, useEffect } from 'react';

const MouseTracker = ({ children }) => {
  const [mousePosition, setMousePosition] = useState({ x: 0, y: 0 });
  
  useEffect(() => {
    const handleMouseMove = (event) => {
      setMousePosition({
        x: event.clientX,
        y: event.clientY
      });
    };
    
    window.addEventListener('mousemove', handleMouseMove);
    return () => window.removeEventListener('mousemove', handleMouseMove);
  }, []);
  
  return children(mousePosition);
};

export default MouseTracker;

// Usage
<MouseTracker>
  {({ x, y }) => (
    <div>
      <p>Mouse position: {x}, {y}</p>
    </div>
  )}
</MouseTracker>
```

### Render Props with Multiple Values
```javascript
// FormProvider.js
import React, { useState } from 'react';

const FormProvider = ({ initialValues, children }) => {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  
  const handleChange = (name, value) => {
    setValues(prev => ({ ...prev, [name]: value }));
    setTouched(prev => ({ ...prev, [name]: true }));
  };
  
  const handleBlur = (name) => {
    setTouched(prev => ({ ...prev, [name]: true }));
  };
  
  const handleSubmit = (onSubmit) => (e) => {
    e.preventDefault();
    onSubmit(values);
  };
  
  const reset = () => {
    setValues(initialValues);
    setErrors({});
    setTouched({});
  };
  
  return children({
    values,
    errors,
    touched,
    handleChange,
    handleBlur,
    handleSubmit,
    reset
  });
};

export default FormProvider;

// Usage
<FormProvider initialValues={{ name: '', email: '' }}>
  {({ values, handleChange, handleSubmit }) => (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <input
        name="name"
        value={values.name}
        onChange={(e) => handleChange('name', e.target.value)}
      />
      <input
        name="email"
        value={values.email}
        onChange={(e) => handleChange('email', e.target.value)}
      />
      <button type="submit">Submit</button>
    </form>
  )}
</FormProvider>
```

---

## Compound Components

### Basic Compound Components
```javascript
// Tabs.js
import React, { useState, createContext, useContext } from 'react';

const TabsContext = createContext();

const Tabs = ({ children, defaultTab }) => {
  const [activeTab, setActiveTab] = useState(defaultTab);
  
  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">
        {children}
      </div>
    </TabsContext.Provider>
  );
};

const TabList = ({ children }) => (
  <div className="tab-list">
    {children}
  </div>
);

const Tab = ({ id, children }) => {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  
  return (
    <button
      className={`tab ${activeTab === id ? 'active' : ''}`}
      onClick={() => setActiveTab(id)}
    >
      {children}
    </button>
  );
};

const TabPanels = ({ children }) => (
  <div className="tab-panels">
    {children}
  </div>
);

const TabPanel = ({ id, children }) => {
  const { activeTab } = useContext(TabsContext);
  
  if (activeTab !== id) return null;
  
  return (
    <div className="tab-panel">
      {children}
    </div>
  );
};

// Export compound components
Tabs.TabList = TabList;
Tabs.Tab = Tab;
Tabs.TabPanels = TabPanels;
Tabs.TabPanel = TabPanel;

export default Tabs;

// Usage
<Tabs defaultTab="tab1">
  <Tabs.TabList>
    <Tabs.Tab id="tab1">Tab 1</Tabs.Tab>
    <Tabs.Tab id="tab2">Tab 2</Tabs.Tab>
  </Tabs.TabList>
  
  <Tabs.TabPanels>
    <Tabs.TabPanel id="tab1">
      <h2>Tab 1 Content</h2>
    </Tabs.TabPanel>
    <Tabs.TabPanel id="tab2">
      <h2>Tab 2 Content</h2>
    </Tabs.TabPanel>
  </Tabs.TabPanels>
</Tabs>
```

### Modal Compound Components
```javascript
// Modal.js
import React, { useState, createContext, useContext } from 'react';

const ModalContext = createContext();

const Modal = ({ children, isOpen, onClose }) => {
  if (!isOpen) return null;
  
  return (
    <ModalContext.Provider value={{ onClose }}>
      <div className="modal-overlay" onClick={onClose}>
        <div className="modal-content" onClick={(e) => e.stopPropagation()}>
          {children}
        </div>
      </div>
    </ModalContext.Provider>
  );
};

const ModalHeader = ({ children }) => (
  <div className="modal-header">
    {children}
  </div>
);

const ModalBody = ({ children }) => (
  <div className="modal-body">
    {children}
  </div>
);

const ModalFooter = ({ children }) => (
  <div className="modal-footer">
    {children}
  </div>
);

const ModalCloseButton = () => {
  const { onClose } = useContext(ModalContext);
  
  return (
    <button className="modal-close" onClick={onClose}>
      ×
    </button>
  );
};

// Export compound components
Modal.Header = ModalHeader;
Modal.Body = ModalBody;
Modal.Footer = ModalFooter;
Modal.CloseButton = ModalCloseButton;

export default Modal;

// Usage
<Modal isOpen={isModalOpen} onClose={() => setIsModalOpen(false)}>
  <Modal.Header>
    <h2>Modal Title</h2>
    <Modal.CloseButton />
  </Modal.Header>
  
  <Modal.Body>
    <p>Modal content goes here</p>
  </Modal.Body>
  
  <Modal.Footer>
    <button onClick={() => setIsModalOpen(false)}>Close</button>
  </Modal.Footer>
</Modal>
```

---

## Custom Hooks Patterns

### Data Fetching Hook
```javascript
// useApi.js
import { useState, useEffect, useCallback } from 'react';

const useApi = (url, options = {}) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  const fetchData = useCallback(async () => {
    setLoading(true);
    setError(null);
    
    try {
      const response = await fetch(url, options);
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
  }, [url, options]);
  
  useEffect(() => {
    fetchData();
  }, [fetchData]);
  
  return { data, loading, error, refetch: fetchData };
};

export default useApi;

// Usage
const UserList = () => {
  const { data: users, loading, error, refetch } = useApi('/api/users');
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <div>
      <button onClick={refetch}>Refresh</button>
      <ul>
        {users?.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
};
```

### Form Hook
```javascript
// useForm.js
import { useState, useCallback } from 'react';

const useForm = (initialValues, validationRules = {}) => {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  
  const validate = useCallback((fieldName, value) => {
    const rule = validationRules[fieldName];
    if (!rule) return '';
    
    if (rule.required && !value) {
      return rule.required;
    }
    
    if (rule.minLength && value.length < rule.minLength) {
      return rule.minLength;
    }
    
    if (rule.pattern && !rule.pattern.test(value)) {
      return rule.pattern.message;
    }
    
    return '';
  }, [validationRules]);
  
  const handleChange = useCallback((name, value) => {
    setValues(prev => ({ ...prev, [name]: value }));
    
    const error = validate(name, value);
    setErrors(prev => ({ ...prev, [name]: error }));
  }, [validate]);
  
  const handleBlur = useCallback((name) => {
    setTouched(prev => ({ ...prev, [name]: true }));
  }, []);
  
  const handleSubmit = useCallback((onSubmit) => (e) => {
    e.preventDefault();
    
    const newErrors = {};
    let hasErrors = false;
    
    Object.keys(validationRules).forEach(fieldName => {
      const error = validate(fieldName, values[fieldName]);
      if (error) {
        newErrors[fieldName] = error;
        hasErrors = true;
      }
    });
    
    setErrors(newErrors);
    setTouched(Object.keys(validationRules).reduce((acc, key) => ({ ...acc, [key]: true }), {}));
    
    if (!hasErrors) {
      onSubmit(values);
    }
  }, [values, validate, validationRules]);
  
  const reset = useCallback(() => {
    setValues(initialValues);
    setErrors({});
    setTouched({});
  }, [initialValues]);
  
  return {
    values,
    errors,
    touched,
    handleChange,
    handleBlur,
    handleSubmit,
    reset
  };
};

export default useForm;

// Usage
const ContactForm = () => {
  const validationRules = {
    name: {
      required: 'Name is required',
      minLength: 'Name must be at least 2 characters'
    },
    email: {
      required: 'Email is required',
      pattern: {
        test: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
        message: 'Invalid email format'
      }
    }
  };
  
  const { values, errors, touched, handleChange, handleBlur, handleSubmit } = useForm(
    { name: '', email: '' },
    validationRules
  );
  
  const onSubmit = (data) => {
    console.log('Form submitted:', data);
  };
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <input
          name="name"
          value={values.name}
          onChange={(e) => handleChange('name', e.target.value)}
          onBlur={() => handleBlur('name')}
          placeholder="Name"
        />
        {touched.name && errors.name && <span>{errors.name}</span>}
      </div>
      
      <div>
        <input
          name="email"
          value={values.email}
          onChange={(e) => handleChange('email', e.target.value)}
          onBlur={() => handleBlur('email')}
          placeholder="Email"
        />
        {touched.email && errors.email && <span>{errors.email}</span>}
      </div>
      
      <button type="submit">Submit</button>
    </form>
  );
};
```

---

## Context Patterns

### Theme Context Pattern
```javascript
// ThemeContext.js
import React, { createContext, useContext, useState, useEffect } from 'react';

const ThemeContext = createContext();

export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState(() => {
    const savedTheme = localStorage.getItem('theme');
    return savedTheme || 'light';
  });
  
  useEffect(() => {
    localStorage.setItem('theme', theme);
    document.documentElement.setAttribute('data-theme', theme);
  }, [theme]);
  
  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };
  
  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
};
```

### Auth Context Pattern
```javascript
// AuthContext.js
import React, { createContext, useContext, useState, useEffect } from 'react';

const AuthContext = createContext();

export const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    const checkAuth = async () => {
      try {
        const token = localStorage.getItem('token');
        if (token) {
          const response = await fetch('/api/me', {
            headers: { Authorization: `Bearer ${token}` }
          });
          if (response.ok) {
            const userData = await response.json();
            setUser(userData);
          }
        }
      } catch (error) {
        console.error('Auth check failed:', error);
      } finally {
        setLoading(false);
      }
    };
    
    checkAuth();
  }, []);
  
  const login = async (credentials) => {
    try {
      const response = await fetch('/api/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(credentials)
      });
      
      if (response.ok) {
        const { user, token } = await response.json();
        localStorage.setItem('token', token);
        setUser(user);
        return { success: true };
      } else {
        const error = await response.json();
        return { success: false, error: error.message };
      }
    } catch (error) {
      return { success: false, error: error.message };
    }
  };
  
  const logout = () => {
    localStorage.removeItem('token');
    setUser(null);
  };
  
  return (
    <AuthContext.Provider value={{ user, loading, login, logout }}>
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

## Practice Exercises

### Exercise 1: Create a Data Table HOC
```javascript
// withSorting.js
import React, { useState } from 'react';

const withSorting = (WrappedComponent) => {
  return function WithSortingComponent({ data, ...props }) {
    const [sortConfig, setSortConfig] = useState({ key: null, direction: 'asc' });
    
    const handleSort = (key) => {
      setSortConfig(prev => ({
        key,
        direction: prev.key === key && prev.direction === 'asc' ? 'desc' : 'asc'
      }));
    };
    
    const sortedData = React.useMemo(() => {
      if (!sortConfig.key) return data;
      
      return [...data].sort((a, b) => {
        const aValue = a[sortConfig.key];
        const bValue = b[sortConfig.key];
        
        if (aValue < bValue) {
          return sortConfig.direction === 'asc' ? -1 : 1;
        }
        if (aValue > bValue) {
          return sortConfig.direction === 'asc' ? 1 : -1;
        }
        return 0;
      });
    }, [data, sortConfig]);
    
    return (
      <WrappedComponent
        {...props}
        data={sortedData}
        sortConfig={sortConfig}
        onSort={handleSort}
      />
    );
  };
};

export default withSorting;

// Usage
const UserTable = ({ data, sortConfig, onSort }) => (
  <table>
    <thead>
      <tr>
        <th onClick={() => onSort('name')}>
          Name {sortConfig.key === 'name' && (sortConfig.direction === 'asc' ? '↑' : '↓')}
        </th>
        <th onClick={() => onSort('email')}>
          Email {sortConfig.key === 'email' && (sortConfig.direction === 'asc' ? '↑' : '↓')}
        </th>
      </tr>
    </thead>
    <tbody>
      {data.map(user => (
        <tr key={user.id}>
          <td>{user.name}</td>
          <td>{user.email}</td>
        </tr>
      ))}
    </tbody>
  </table>
);

const SortableUserTable = withSorting(UserTable);
```

### Exercise 2: Create a Modal Compound Component
```javascript
// Modal.js
import React, { useState, createContext, useContext } from 'react';

const ModalContext = createContext();

const Modal = ({ children, isOpen, onClose }) => {
  if (!isOpen) return null;
  
  return (
    <ModalContext.Provider value={{ onClose }}>
      <div className="modal-overlay" onClick={onClose}>
        <div className="modal-content" onClick={(e) => e.stopPropagation()}>
          {children}
        </div>
      </div>
    </ModalContext.Provider>
  );
};

const ModalHeader = ({ children }) => (
  <div className="modal-header">
    {children}
  </div>
);

const ModalBody = ({ children }) => (
  <div className="modal-body">
    {children}
  </div>
);

const ModalFooter = ({ children }) => (
  <div className="modal-footer">
    {children}
  </div>
);

const ModalCloseButton = () => {
  const { onClose } = useContext(ModalContext);
  
  return (
    <button className="modal-close" onClick={onClose}>
      ×
    </button>
  );
};

Modal.Header = ModalHeader;
Modal.Body = ModalBody;
Modal.Footer = ModalFooter;
Modal.CloseButton = ModalCloseButton;

export default Modal;
```

### Exercise 3: Create a Custom Hook for Local Storage
```javascript
// useLocalStorage.js
import { useState, useEffect } from 'react';

const useLocalStorage = (key, initialValue) => {
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
};

export default useLocalStorage;

// Usage
const Settings = () => {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  const [language, setLanguage] = useLocalStorage('language', 'en');
  
  return (
    <div>
      <select value={theme} onChange={(e) => setTheme(e.target.value)}>
        <option value="light">Light</option>
        <option value="dark">Dark</option>
      </select>
      
      <select value={language} onChange={(e) => setLanguage(e.target.value)}>
        <option value="en">English</option>
        <option value="es">Spanish</option>
      </select>
    </div>
  );
};
```

---

## Summary

### Key Concepts Learned
- ✅ **Higher-Order Components** - Reusable component logic
- ✅ **Render Props** - Sharing code between components
- ✅ **Compound Components** - Building flexible component APIs
- ✅ **Custom Hooks Patterns** - Reusable stateful logic
- ✅ **Context Patterns** - Advanced state management

### Best Practices
- Use HOCs for cross-cutting concerns
- Use render props for flexible component composition
- Use compound components for complex UI patterns
- Use custom hooks for reusable stateful logic
- Use context for global state management

### Common Mistakes
- Overusing HOCs
- Not properly forwarding refs in HOCs
- Creating too many contexts
- Not memoizing expensive operations
- Not handling edge cases in custom hooks

---

**Excellent! You've mastered advanced React patterns. Ready for Backend Integration? 🚀**

**Next Tutorial:** [Backend Integration](./25-backend-integration.md)
