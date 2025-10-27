# React Router - Tutorial 19 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Basic Routing](#basic-routing)
3. [Route Parameters](#route-parameters)
4. [Nested Routes](#nested-routes)
5. [Navigation](#navigation)
6. [Route Guards](#route-guards)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

React Router is a **powerful routing library** for React applications that enables navigation between different components based on the URL. It's essential for building single-page applications (SPAs).

### What You'll Learn
- ✅ **Basic Routing** - Setting up routes and components
- ✅ **Route Parameters** - Dynamic routing with parameters
- ✅ **Nested Routes** - Hierarchical routing structure
- ✅ **Navigation** - Programmatic and declarative navigation
- ✅ **Route Guards** - Protecting routes and authentication

---

## Basic Routing

### Installation and Setup
```bash
npm install react-router-dom
```

```javascript
import React from 'react';
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';

// Components
function Home() {
    return <h1>Home Page</h1>;
}

function About() {
    return <h1>About Page</h1>;
}

function Contact() {
    return <h1>Contact Page</h1>;
}

function App() {
    return (
        <BrowserRouter>
            <nav>
                <Link to="/">Home</Link>
                <Link to="/about">About</Link>
                <Link to="/contact">Contact</Link>
            </nav>
            
            <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/about" element={<About />} />
                <Route path="/contact" element={<Contact />} />
            </Routes>
        </BrowserRouter>
    );
}

export default App;
```

### Basic Route Structure
```javascript
import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/about" element={<About />} />
                <Route path="/contact" element={<Contact />} />
                <Route path="/products" element={<Products />} />
                <Route path="/services" element={<Services />} />
            </Routes>
        </BrowserRouter>
    );
}
```

### Navigation Components
```javascript
import React from 'react';
import { Link, NavLink } from 'react-router-dom';

function Navigation() {
    return (
        <nav>
            <ul>
                <li>
                    <Link to="/">Home</Link>
                </li>
                <li>
                    <NavLink 
                        to="/about" 
                        className={({ isActive }) => isActive ? 'active' : ''}
                    >
                        About
                    </NavLink>
                </li>
                <li>
                    <NavLink 
                        to="/contact"
                        style={({ isActive }) => ({
                            color: isActive ? 'red' : 'blue'
                        })}
                    >
                        Contact
                    </NavLink>
                </li>
            </ul>
        </nav>
    );
}
```

---

## Route Parameters

### Dynamic Routes
```javascript
import React from 'react';
import { Routes, Route, useParams } from 'react-router-dom';

function UserProfile() {
    const { userId } = useParams();
    
    return (
        <div>
            <h1>User Profile</h1>
            <p>User ID: {userId}</p>
        </div>
    );
}

function ProductDetails() {
    const { productId } = useParams();
    
    return (
        <div>
            <h1>Product Details</h1>
            <p>Product ID: {productId}</p>
        </div>
    );
}

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/user/:userId" element={<UserProfile />} />
                <Route path="/product/:productId" element={<ProductDetails />} />
            </Routes>
        </BrowserRouter>
    );
}
```

### Multiple Parameters
```javascript
function BlogPost() {
    const { year, month, slug } = useParams();
    
    return (
        <div>
            <h1>Blog Post</h1>
            <p>Year: {year}</p>
            <p>Month: {month}</p>
            <p>Slug: {slug}</p>
        </div>
    );
}

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/blog/:year/:month/:slug" element={<BlogPost />} />
            </Routes>
        </BrowserRouter>
    );
}
```

### Optional Parameters
```javascript
function SearchResults() {
    const { query, category } = useParams();
    
    return (
        <div>
            <h1>Search Results</h1>
            <p>Query: {query}</p>
            {category && <p>Category: {category}</p>}
        </div>
    );
}

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/search/:query" element={<SearchResults />} />
                <Route path="/search/:query/:category" element={<SearchResults />} />
            </Routes>
        </BrowserRouter>
    );
}
```

---

## Nested Routes

### Basic Nested Routes
```javascript
import React from 'react';
import { Routes, Route, Outlet } from 'react-router-dom';

function Layout() {
    return (
        <div>
            <header>
                <h1>My App</h1>
                <nav>
                    <Link to="/">Home</Link>
                    <Link to="/dashboard">Dashboard</Link>
                    <Link to="/profile">Profile</Link>
                </nav>
            </header>
            <main>
                <Outlet />
            </main>
            <footer>
                <p>&copy; 2023 My App</p>
            </footer>
        </div>
    );
}

function Dashboard() {
    return <h2>Dashboard</h2>;
}

function Profile() {
    return <h2>Profile</h2>;
}

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/" element={<Layout />}>
                    <Route index element={<Home />} />
                    <Route path="dashboard" element={<Dashboard />} />
                    <Route path="profile" element={<Profile />} />
                </Route>
            </Routes>
        </BrowserRouter>
    );
}
```

### Complex Nested Routes
```javascript
function Dashboard() {
    return (
        <div>
            <h2>Dashboard</h2>
            <nav>
                <Link to="/dashboard/overview">Overview</Link>
                <Link to="/dashboard/analytics">Analytics</Link>
                <Link to="/dashboard/settings">Settings</Link>
            </nav>
            <Outlet />
        </div>
    );
}

function Overview() {
    return <h3>Dashboard Overview</h3>;
}

function Analytics() {
    return <h3>Analytics</h3>;
}

function Settings() {
    return <h3>Settings</h3>;
}

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/" element={<Layout />}>
                    <Route index element={<Home />} />
                    <Route path="dashboard" element={<Dashboard />}>
                        <Route index element={<Overview />} />
                        <Route path="overview" element={<Overview />} />
                        <Route path="analytics" element={<Analytics />} />
                        <Route path="settings" element={<Settings />} />
                    </Route>
                </Route>
            </Routes>
        </BrowserRouter>
    );
}
```

---

## Navigation

### Programmatic Navigation
```javascript
import React from 'react';
import { useNavigate } from 'react-router-dom';

function LoginForm() {
    const navigate = useNavigate();
    const [username, setUsername] = useState('');
    const [password, setPassword] = useState('');
    
    const handleSubmit = async (e) => {
        e.preventDefault();
        
        try {
            // Simulate login
            const response = await fetch('/api/login', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ username, password })
            });
            
            if (response.ok) {
                navigate('/dashboard');
            } else {
                alert('Login failed');
            }
        } catch (error) {
            console.error('Login error:', error);
        }
    };
    
    return (
        <form onSubmit={handleSubmit}>
            <input 
                type="text"
                value={username}
                onChange={(e) => setUsername(e.target.value)}
                placeholder="Username"
            />
            <input 
                type="password"
                value={password}
                onChange={(e) => setPassword(e.target.value)}
                placeholder="Password"
            />
            <button type="submit">Login</button>
        </form>
    );
}
```

### Navigation with State
```javascript
function ProductList() {
    const navigate = useNavigate();
    
    const handleProductClick = (product) => {
        navigate('/product', { 
            state: { product },
            replace: false
        });
    };
    
    return (
        <div>
            {products.map(product => (
                <div 
                    key={product.id}
                    onClick={() => handleProductClick(product)}
                >
                    <h3>{product.name}</h3>
                    <p>{product.description}</p>
                </div>
            ))}
        </div>
    );
}

function ProductDetails() {
    const location = useLocation();
    const product = location.state?.product;
    
    if (!product) {
        return <div>Product not found</div>;
    }
    
    return (
        <div>
            <h1>{product.name}</h1>
            <p>{product.description}</p>
            <p>Price: ${product.price}</p>
        </div>
    );
}
```

### Navigation Hooks
```javascript
import { useNavigate, useLocation, useParams } from 'react-router-dom';

function UserProfile() {
    const navigate = useNavigate();
    const location = useLocation();
    const { userId } = useParams();
    
    const goBack = () => {
        navigate(-1);
    };
    
    const goForward = () => {
        navigate(1);
    };
    
    const goToHome = () => {
        navigate('/');
    };
    
    return (
        <div>
            <h1>User Profile</h1>
            <p>User ID: {userId}</p>
            <p>Current path: {location.pathname}</p>
            <p>Search params: {location.search}</p>
            
            <div>
                <button onClick={goBack}>Go Back</button>
                <button onClick={goForward}>Go Forward</button>
                <button onClick={goToHome}>Go Home</button>
            </div>
        </div>
    );
}
```

---

## Route Guards

### Authentication Guard
```javascript
import React from 'react';
import { Navigate, useLocation } from 'react-router-dom';

function ProtectedRoute({ children, isAuthenticated }) {
    const location = useLocation();
    
    if (!isAuthenticated) {
        return <Navigate to="/login" state={{ from: location }} replace />;
    }
    
    return children;
}

function LoginPage() {
    const navigate = useNavigate();
    const location = useLocation();
    
    const from = location.state?.from?.pathname || '/';
    
    const handleLogin = () => {
        // Simulate login
        localStorage.setItem('isAuthenticated', 'true');
        navigate(from, { replace: true });
    };
    
    return (
        <div>
            <h1>Login</h1>
            <button onClick={handleLogin}>Login</button>
        </div>
    );
}

function App() {
    const isAuthenticated = localStorage.getItem('isAuthenticated') === 'true';
    
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/login" element={<LoginPage />} />
                <Route 
                    path="/dashboard" 
                    element={
                        <ProtectedRoute isAuthenticated={isAuthenticated}>
                            <Dashboard />
                        </ProtectedRoute>
                    } 
                />
            </Routes>
        </BrowserRouter>
    );
}
```

### Role-Based Access
```javascript
function RoleBasedRoute({ children, requiredRole, userRole }) {
    if (userRole !== requiredRole) {
        return <Navigate to="/unauthorized" replace />;
    }
    
    return children;
}

function AdminPanel() {
    return <h1>Admin Panel</h1>;
}

function UserDashboard() {
    return <h1>User Dashboard</h1>;
}

function App() {
    const userRole = localStorage.getItem('userRole') || 'user';
    
    return (
        <BrowserRouter>
            <Routes>
                <Route 
                    path="/admin" 
                    element={
                        <RoleBasedRoute requiredRole="admin" userRole={userRole}>
                            <AdminPanel />
                        </RoleBasedRoute>
                    } 
                />
                <Route 
                    path="/dashboard" 
                    element={
                        <RoleBasedRoute requiredRole="user" userRole={userRole}>
                            <UserDashboard />
                        </RoleBasedRoute>
                    } 
                />
            </Routes>
        </BrowserRouter>
    );
}
```

### Loading States
```javascript
function LoadingRoute({ children, isLoading }) {
    if (isLoading) {
        return <div>Loading...</div>;
    }
    
    return children;
}

function App() {
    const [isLoading, setIsLoading] = useState(true);
    
    useEffect(() => {
        // Simulate loading
        setTimeout(() => {
            setIsLoading(false);
        }, 2000);
    }, []);
    
    return (
        <BrowserRouter>
            <Routes>
                <Route 
                    path="/" 
                    element={
                        <LoadingRoute isLoading={isLoading}>
                            <Home />
                        </LoadingRoute>
                    } 
                />
            </Routes>
        </BrowserRouter>
    );
}
```

---

## Practice Exercises

### Exercise 1: E-commerce App
```javascript
function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/" element={<Layout />}>
                    <Route index element={<Home />} />
                    <Route path="products" element={<Products />} />
                    <Route path="product/:id" element={<ProductDetails />} />
                    <Route path="cart" element={<Cart />} />
                    <Route path="checkout" element={<Checkout />} />
                    <Route path="profile" element={<Profile />} />
                </Route>
            </Routes>
        </BrowserRouter>
    );
}

function Layout() {
    return (
        <div>
            <header>
                <h1>E-commerce Store</h1>
                <nav>
                    <Link to="/">Home</Link>
                    <Link to="/products">Products</Link>
                    <Link to="/cart">Cart</Link>
                    <Link to="/profile">Profile</Link>
                </nav>
            </header>
            <main>
                <Outlet />
            </main>
        </div>
    );
}

function Products() {
    const [products, setProducts] = useState([]);
    
    useEffect(() => {
        fetch('/api/products')
            .then(res => res.json())
            .then(data => setProducts(data));
    }, []);
    
    return (
        <div>
            <h2>Products</h2>
            <div className="product-grid">
                {products.map(product => (
                    <div key={product.id} className="product-card">
                        <h3>{product.name}</h3>
                        <p>${product.price}</p>
                        <Link to={`/product/${product.id}`}>View Details</Link>
                    </div>
                ))}
            </div>
        </div>
    );
}

function ProductDetails() {
    const { id } = useParams();
    const [product, setProduct] = useState(null);
    
    useEffect(() => {
        fetch(`/api/products/${id}`)
            .then(res => res.json())
            .then(data => setProduct(data));
    }, [id]);
    
    if (!product) return <div>Loading...</div>;
    
    return (
        <div>
            <h2>{product.name}</h2>
            <p>${product.price}</p>
            <p>{product.description}</p>
            <button>Add to Cart</button>
        </div>
    );
}
```

### Exercise 2: Blog App
```javascript
function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/" element={<Layout />}>
                    <Route index element={<Home />} />
                    <Route path="blog" element={<Blog />}>
                        <Route index element={<BlogList />} />
                        <Route path=":slug" element={<BlogPost />} />
                    </Route>
                    <Route path="about" element={<About />} />
                    <Route path="contact" element={<Contact />} />
                </Route>
            </Routes>
        </BrowserRouter>
    );
}

function Blog() {
    return (
        <div>
            <h2>Blog</h2>
            <nav>
                <Link to="/blog">All Posts</Link>
            </nav>
            <Outlet />
        </div>
    );
}

function BlogList() {
    const [posts, setPosts] = useState([]);
    
    useEffect(() => {
        fetch('/api/posts')
            .then(res => res.json())
            .then(data => setPosts(data));
    }, []);
    
    return (
        <div>
            <h3>All Posts</h3>
            {posts.map(post => (
                <div key={post.id}>
                    <h4>{post.title}</h4>
                    <p>{post.excerpt}</p>
                    <Link to={`/blog/${post.slug}`}>Read More</Link>
                </div>
            ))}
        </div>
    );
}

function BlogPost() {
    const { slug } = useParams();
    const [post, setPost] = useState(null);
    
    useEffect(() => {
        fetch(`/api/posts/${slug}`)
            .then(res => res.json())
            .then(data => setPost(data));
    }, [slug]);
    
    if (!post) return <div>Loading...</div>;
    
    return (
        <div>
            <h3>{post.title}</h3>
            <p>{post.content}</p>
            <p>Published: {post.publishedAt}</p>
        </div>
    );
}
```

### Exercise 3: Admin Dashboard
```javascript
function App() {
    const [user, setUser] = useState(null);
    
    useEffect(() => {
        const userData = localStorage.getItem('user');
        if (userData) {
            setUser(JSON.parse(userData));
        }
    }, []);
    
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/login" element={<Login />} />
                <Route path="/" element={<Layout user={user} />}>
                    <Route index element={<Home />} />
                    <Route path="dashboard" element={<Dashboard />} />
                    <Route path="users" element={<Users />} />
                    <Route path="settings" element={<Settings />} />
                </Route>
            </Routes>
        </BrowserRouter>
    );
}

function Layout({ user }) {
    if (!user) {
        return <Navigate to="/login" replace />;
    }
    
    return (
        <div>
            <header>
                <h1>Admin Dashboard</h1>
                <nav>
                    <Link to="/">Home</Link>
                    <Link to="/dashboard">Dashboard</Link>
                    <Link to="/users">Users</Link>
                    <Link to="/settings">Settings</Link>
                </nav>
                <div>
                    Welcome, {user.name}
                    <button onClick={() => {
                        localStorage.removeItem('user');
                        window.location.reload();
                    }}>Logout</button>
                </div>
            </header>
            <main>
                <Outlet />
            </main>
        </div>
    );
}

function Login() {
    const navigate = useNavigate();
    
    const handleLogin = (e) => {
        e.preventDefault();
        const userData = {
            id: 1,
            name: 'Admin User',
            role: 'admin'
        };
        localStorage.setItem('user', JSON.stringify(userData));
        navigate('/');
    };
    
    return (
        <form onSubmit={handleLogin}>
            <h2>Login</h2>
            <input type="text" placeholder="Username" />
            <input type="password" placeholder="Password" />
            <button type="submit">Login</button>
        </form>
    );
}
```

---

## Summary

### Key Concepts Learned
- ✅ **Basic Routing** - Setting up routes and components
- ✅ **Route Parameters** - Dynamic routing with parameters
- ✅ **Nested Routes** - Hierarchical routing structure
- ✅ **Navigation** - Programmatic and declarative navigation
- ✅ **Route Guards** - Protecting routes and authentication

### Best Practices
- Use nested routes for complex layouts
- Implement route guards for security
- Use programmatic navigation for dynamic behavior
- Keep route structure organized
- Handle loading and error states

### Common Mistakes
- Not wrapping routes in BrowserRouter
- Forgetting to use Outlet for nested routes
- Not handling route parameters properly
- Missing authentication checks
- Not handling navigation state

---

**Excellent! You've mastered React Router. Ready for State Management? 🚀**

**Next Tutorial:** [State Management](./20-state-management.md)
