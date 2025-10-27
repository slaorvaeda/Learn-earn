# Performance Optimization - Tutorial 23 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Bundle Optimization](#bundle-optimization)
3. [Code Splitting](#code-splitting)
4. [Lazy Loading](#lazy-loading)
5. [Caching Strategies](#caching-strategies)
6. [Image Optimization](#image-optimization)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Performance optimization is **crucial for creating fast, responsive applications**. It improves user experience, reduces bounce rates, and improves SEO rankings.

### What You'll Learn
- ✅ **Bundle Optimization** - Reducing bundle size
- ✅ **Code Splitting** - Loading code on demand
- ✅ **Lazy Loading** - Loading resources when needed
- ✅ **Caching Strategies** - Storing data for faster access
- ✅ **Image Optimization** - Optimizing images for web

---

## Bundle Optimization

### Webpack Bundle Analyzer
```bash
# Install bundle analyzer
npm install --save-dev webpack-bundle-analyzer

# Add to package.json
"scripts": {
  "analyze": "npm run build && npx webpack-bundle-analyzer build/static/js/*.js"
}

# Run analysis
npm run analyze
```

### Tree Shaking
```javascript
// utils.js - Use ES6 modules for tree shaking
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;
export const multiply = (a, b) => a * b;

// main.js - Import only what you need
import { add, multiply } from './utils.js';

// This will be tree-shaken out
// import { subtract } from './utils.js';
```

### Minification
```javascript
// webpack.config.js
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        terserOptions: {
          compress: {
            drop_console: true, // Remove console.log in production
            drop_debugger: true, // Remove debugger statements
          },
        },
      }),
    ],
  },
};
```

### Dead Code Elimination
```javascript
// Remove unused code
// Bad: Import entire library
import _ from 'lodash';

// Good: Import specific functions
import { debounce, throttle } from 'lodash';

// Use dynamic imports for large libraries
const loadChart = async () => {
  const { Chart } = await import('chart.js');
  return Chart;
};
```

---

## Code Splitting

### Route-based Code Splitting
```javascript
// App.js
import React, { Suspense, lazy } from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

// Lazy load components
const Home = lazy(() => import('./components/Home'));
const About = lazy(() => import('./components/About'));
const Contact = lazy(() => import('./components/Contact'));

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<div>Loading...</div>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
          <Route path="/contact" element={<Contact />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}

export default App;
```

### Component-based Code Splitting
```javascript
// HeavyComponent.js
import React, { useState, useEffect } from 'react';

const HeavyComponent = () => {
  const [data, setData] = useState(null);

  useEffect(() => {
    // Simulate heavy computation
    const result = Array.from({ length: 10000 }, (_, i) => i);
    setData(result);
  }, []);

  return (
    <div>
      <h2>Heavy Component</h2>
      <p>Data loaded: {data?.length} items</p>
    </div>
  );
};

export default HeavyComponent;

// App.js
import React, { Suspense, lazy, useState } from 'react';

const HeavyComponent = lazy(() => import('./HeavyComponent'));

function App() {
  const [showHeavy, setShowHeavy] = useState(false);

  return (
    <div>
      <button onClick={() => setShowHeavy(!showHeavy)}>
        Toggle Heavy Component
      </button>
      
      {showHeavy && (
        <Suspense fallback={<div>Loading heavy component...</div>}>
          <HeavyComponent />
        </Suspense>
      )}
    </div>
  );
}
```

### Dynamic Imports
```javascript
// utils.js
export const loadChart = async () => {
  const { Chart } = await import('chart.js');
  return Chart;
};

export const loadMoment = async () => {
  const moment = await import('moment');
  return moment.default;
};

// Component.js
import React, { useState, useEffect } from 'react';

const ChartComponent = () => {
  const [Chart, setChart] = useState(null);

  useEffect(() => {
    const loadChartLibrary = async () => {
      const ChartLib = await import('./utils').then(utils => utils.loadChart());
      setChart(ChartLib);
    };
    
    loadChartLibrary();
  }, []);

  if (!Chart) return <div>Loading chart...</div>;

  return <div>Chart loaded!</div>;
};
```

---

## Lazy Loading

### Image Lazy Loading
```javascript
// LazyImage.js
import React, { useState, useRef, useEffect } from 'react';

const LazyImage = ({ src, alt, placeholder, ...props }) => {
  const [isLoaded, setIsLoaded] = useState(false);
  const [isInView, setIsInView] = useState(false);
  const imgRef = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsInView(true);
          observer.disconnect();
        }
      },
      { threshold: 0.1 }
    );

    if (imgRef.current) {
      observer.observe(imgRef.current);
    }

    return () => observer.disconnect();
  }, []);

  return (
    <div ref={imgRef} {...props}>
      {isInView && (
        <img
          src={src}
          alt={alt}
          onLoad={() => setIsLoaded(true)}
          style={{ opacity: isLoaded ? 1 : 0 }}
        />
      )}
      {!isLoaded && placeholder && (
        <div style={{ background: '#f0f0f0' }}>
          {placeholder}
        </div>
      )}
    </div>
  );
};

export default LazyImage;

// Usage
<LazyImage
  src="https://example.com/image.jpg"
  alt="Description"
  placeholder={<div>Loading...</div>}
/>
```

### Component Lazy Loading
```javascript
// LazyComponent.js
import React, { useState, useRef, useEffect } from 'react';

const LazyComponent = ({ children, fallback, threshold = 0.1 }) => {
  const [isVisible, setIsVisible] = useState(false);
  const ref = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsVisible(true);
          observer.disconnect();
        }
      },
      { threshold }
    );

    if (ref.current) {
      observer.observe(ref.current);
    }

    return () => observer.disconnect();
  }, [threshold]);

  return (
    <div ref={ref}>
      {isVisible ? children : fallback}
    </div>
  );
};

export default LazyComponent;

// Usage
<LazyComponent fallback={<div>Loading...</div>}>
  <ExpensiveComponent />
</LazyComponent>
```

### Data Lazy Loading
```javascript
// useLazyData.js
import { useState, useEffect, useRef } from 'react';

const useLazyData = (fetchFn, options = {}) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const ref = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(
      async ([entry]) => {
        if (entry.isIntersecting && !data && !loading) {
          setLoading(true);
          try {
            const result = await fetchFn();
            setData(result);
          } catch (err) {
            setError(err);
          } finally {
            setLoading(false);
          }
        }
      },
      { threshold: options.threshold || 0.1 }
    );

    if (ref.current) {
      observer.observe(ref.current);
    }

    return () => observer.disconnect();
  }, [fetchFn, data, loading, options.threshold]);

  return { ref, data, loading, error };
};

export default useLazyData;

// Usage
const DataComponent = () => {
  const { ref, data, loading, error } = useLazyData(
    () => fetch('/api/data').then(res => res.json())
  );

  return (
    <div ref={ref}>
      {loading && <div>Loading...</div>}
      {error && <div>Error: {error.message}</div>}
      {data && <div>Data: {JSON.stringify(data)}</div>}
    </div>
  );
};
```

---

## Caching Strategies

### Browser Caching
```javascript
// Service Worker for caching
const CACHE_NAME = 'my-app-v1';
const urlsToCache = [
  '/',
  '/static/js/bundle.js',
  '/static/css/main.css',
  '/manifest.json'
];

self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then((cache) => cache.addAll(urlsToCache))
  );
});

self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request)
      .then((response) => {
        if (response) {
          return response;
        }
        return fetch(event.request);
      })
  );
});
```

### Memory Caching
```javascript
// Simple memory cache
class MemoryCache {
  constructor(maxSize = 100) {
    this.cache = new Map();
    this.maxSize = maxSize;
  }

  get(key) {
    if (this.cache.has(key)) {
      const item = this.cache.get(key);
      // Move to end (LRU)
      this.cache.delete(key);
      this.cache.set(key, item);
      return item.value;
    }
    return null;
  }

  set(key, value, ttl = 60000) {
    if (this.cache.size >= this.maxSize) {
      const firstKey = this.cache.keys().next().value;
      this.cache.delete(firstKey);
    }

    this.cache.set(key, {
      value,
      expires: Date.now() + ttl
    });
  }

  has(key) {
    if (!this.cache.has(key)) return false;
    
    const item = this.cache.get(key);
    if (Date.now() > item.expires) {
      this.cache.delete(key);
      return false;
    }
    return true;
  }
}

// Usage
const cache = new MemoryCache();

const fetchData = async (url) => {
  if (cache.has(url)) {
    return cache.get(url);
  }

  const response = await fetch(url);
  const data = await response.json();
  cache.set(url, data);
  return data;
};
```

### React Query Caching
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
      <MyComponent />
    </QueryClientProvider>
  );
}

// MyComponent.js
import { useQuery } from '@tanstack/react-query';

const fetchUser = async (id) => {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
};

const MyComponent = ({ userId }) => {
  const { data, isLoading, error } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId),
    enabled: !!userId,
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return <div>User: {data?.name}</div>;
};
```

---

## Image Optimization

### Responsive Images
```javascript
// ResponsiveImage.js
import React from 'react';

const ResponsiveImage = ({ 
  src, 
  alt, 
  sizes = '(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw',
  ...props 
}) => {
  const baseSrc = src.replace(/\.[^/.]+$/, '');
  const extension = src.split('.').pop();

  return (
    <picture>
      <source
        media="(max-width: 768px)"
        srcSet={`${baseSrc}-mobile.${extension} 1x, ${baseSrc}-mobile@2x.${extension} 2x`}
      />
      <source
        media="(max-width: 1200px)"
        srcSet={`${baseSrc}-tablet.${extension} 1x, ${baseSrc}-tablet@2x.${extension} 2x`}
      />
      <img
        src={`${baseSrc}-desktop.${extension}`}
        srcSet={`${baseSrc}-desktop.${extension} 1x, ${baseSrc}-desktop@2x.${extension} 2x`}
        alt={alt}
        sizes={sizes}
        loading="lazy"
        {...props}
      />
    </picture>
  );
};

export default ResponsiveImage;
```

### WebP Support
```javascript
// WebPImage.js
import React from 'react';

const WebPImage = ({ src, alt, fallback, ...props }) => {
  const baseSrc = src.replace(/\.[^/.]+$/, '');
  const extension = src.split('.').pop();

  return (
    <picture>
      <source srcSet={`${baseSrc}.webp`} type="image/webp" />
      <img
        src={fallback || src}
        alt={alt}
        loading="lazy"
        {...props}
      />
    </picture>
  );
};

export default WebPImage;
```

### Image Compression
```javascript
// imageUtils.js
const compressImage = (file, quality = 0.8, maxWidth = 1920) => {
  return new Promise((resolve) => {
    const canvas = document.createElement('canvas');
    const ctx = canvas.getContext('2d');
    const img = new Image();

    img.onload = () => {
      const ratio = Math.min(maxWidth / img.width, maxWidth / img.height);
      canvas.width = img.width * ratio;
      canvas.height = img.height * ratio;

      ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
      
      canvas.toBlob(
        (blob) => resolve(blob),
        'image/jpeg',
        quality
      );
    };

    img.src = URL.createObjectURL(file);
  });
};

// Usage
const handleImageUpload = async (file) => {
  const compressedFile = await compressImage(file, 0.8, 1920);
  // Upload compressed file
};
```

---

## Practice Exercises

### Exercise 1: Optimize Bundle Size
```javascript
// webpack.config.js
const path = require('path');
const TerserPlugin = require('terser-webpack-plugin');
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[contenthash].js',
    chunkFilename: '[name].[contenthash].chunk.js',
  },
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all',
        },
        common: {
          name: 'common',
          minChunks: 2,
          chunks: 'all',
          enforce: true,
        },
      },
    },
    usedExports: true,
    sideEffects: false,
  },
  plugins: [
    new BundleAnalyzerPlugin({
      analyzerMode: 'static',
      openAnalyzer: false,
    }),
  ],
};
```

### Exercise 2: Implement Code Splitting
```javascript
// App.js
import React, { Suspense, lazy } from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

// Lazy load components
const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));
const Contact = lazy(() => import('./pages/Contact'));
const Dashboard = lazy(() => import('./pages/Dashboard'));

// Loading component
const Loading = () => (
  <div className="loading">
    <div className="spinner"></div>
    <p>Loading...</p>
  </div>
);

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<Loading />}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
          <Route path="/contact" element={<Contact />} />
          <Route path="/dashboard" element={<Dashboard />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}

export default App;
```

### Exercise 3: Implement Caching
```javascript
// cache.js
class APICache {
  constructor(ttl = 5 * 60 * 1000) {
    this.cache = new Map();
    this.ttl = ttl;
  }

  get(key) {
    const item = this.cache.get(key);
    if (!item) return null;

    if (Date.now() > item.expires) {
      this.cache.delete(key);
      return null;
    }

    return item.data;
  }

  set(key, data) {
    this.cache.set(key, {
      data,
      expires: Date.now() + this.ttl
    });
  }

  clear() {
    this.cache.clear();
  }
}

// api.js
const cache = new APICache();

export const fetchData = async (url) => {
  const cached = cache.get(url);
  if (cached) return cached;

  const response = await fetch(url);
  const data = await response.json();
  cache.set(url, data);
  return data;
};
```

---

## Summary

### Key Concepts Learned
- ✅ **Bundle Optimization** - Reducing bundle size and tree shaking
- ✅ **Code Splitting** - Loading code on demand
- ✅ **Lazy Loading** - Loading resources when needed
- ✅ **Caching Strategies** - Storing data for faster access
- ✅ **Image Optimization** - Optimizing images for web

### Best Practices
- Use bundle analysis tools
- Implement code splitting for routes and components
- Use lazy loading for images and heavy components
- Implement proper caching strategies
- Optimize images for different screen sizes
- Use modern image formats like WebP

### Common Mistakes
- Not analyzing bundle size
- Loading all code upfront
- Not implementing lazy loading
- Ignoring caching strategies
- Using unoptimized images
- Not using modern image formats

---

**Excellent! You've mastered performance optimization. Ready for Advanced React Patterns? 🚀**

**Next Tutorial:** [Advanced React Patterns](./24-advanced-react-patterns.md)
