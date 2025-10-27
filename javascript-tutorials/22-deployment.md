# Deployment - Tutorial 22 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Static Site Deployment](#static-site-deployment)
3. [React App Deployment](#react-app-deployment)
4. [Node.js Backend Deployment](#nodejs-backend-deployment)
5. [Database Deployment](#database-deployment)
6. [CI/CD Pipeline](#cicd-pipeline)
7. [Practice Exercises](#practice-exercises)
8. [Summary](#summary)

---

## Introduction

Deployment is the process of **making your application available to users** on the internet. It involves building, configuring, and hosting your application on servers.

### What You'll Learn
- ✅ **Static Site Deployment** - Deploying HTML/CSS/JS sites
- ✅ **React App Deployment** - Deploying React applications
- ✅ **Node.js Backend Deployment** - Deploying server applications
- ✅ **Database Deployment** - Setting up databases
- ✅ **CI/CD Pipeline** - Automated deployment workflows

---

## Static Site Deployment

### GitHub Pages
```bash
# 1. Create a repository on GitHub
# 2. Clone the repository
git clone https://github.com/username/your-repo.git
cd your-repo

# 3. Add your files
echo "<h1>Hello World</h1>" > index.html
git add .
git commit -m "Initial commit"
git push origin main

# 4. Enable GitHub Pages in repository settings
# Go to Settings > Pages > Source: Deploy from a branch > main
```

### Netlify
```bash
# 1. Install Netlify CLI
npm install -g netlify-cli

# 2. Login to Netlify
netlify login

# 3. Deploy from current directory
netlify deploy

# 4. Deploy to production
netlify deploy --prod
```

### Vercel
```bash
# 1. Install Vercel CLI
npm install -g vercel

# 2. Login to Vercel
vercel login

# 3. Deploy from current directory
vercel

# 4. Deploy to production
vercel --prod
```

### Manual Deployment
```bash
# 1. Build your project
npm run build

# 2. Upload files to server
scp -r dist/* user@server:/var/www/html/

# 3. Configure web server (Nginx)
sudo nano /etc/nginx/sites-available/your-site

# 4. Enable site
sudo ln -s /etc/nginx/sites-available/your-site /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## React App Deployment

### Create React App Deployment
```bash
# 1. Build the app
npm run build

# 2. Test the build locally
npx serve -s build

# 3. Deploy to Netlify
# Drag and drop the build folder to Netlify

# 4. Deploy to Vercel
vercel --prod

# 5. Deploy to GitHub Pages
npm install --save-dev gh-pages

# Add to package.json
"homepage": "https://username.github.io/repo-name",
"scripts": {
  "predeploy": "npm run build",
  "deploy": "gh-pages -d build"
}

# Deploy
npm run deploy
```

### Environment Variables
```bash
# .env.local
REACT_APP_API_URL=https://api.example.com
REACT_APP_VERSION=1.0.0

# .env.production
REACT_APP_API_URL=https://api.production.com
REACT_APP_VERSION=1.0.0
```

### Build Optimization
```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'build'),
    filename: 'static/js/[name].[contenthash].js',
    chunkFilename: 'static/js/[name].[contenthash].chunk.js',
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
      },
    },
  },
};
```

---

## Node.js Backend Deployment

### Heroku Deployment
```bash
# 1. Install Heroku CLI
# Download from https://devcenter.heroku.com/articles/heroku-cli

# 2. Login to Heroku
heroku login

# 3. Create Heroku app
heroku create your-app-name

# 4. Add environment variables
heroku config:set NODE_ENV=production
heroku config:set DATABASE_URL=your-database-url

# 5. Deploy
git push heroku main

# 6. Open app
heroku open
```

### Railway Deployment
```bash
# 1. Install Railway CLI
npm install -g @railway/cli

# 2. Login to Railway
railway login

# 3. Initialize project
railway init

# 4. Deploy
railway up

# 5. Open app
railway open
```

### DigitalOcean App Platform
```yaml
# .do/app.yaml
name: my-app
services:
- name: web
  source_dir: /
  github:
    repo: username/repo-name
    branch: main
  run_command: npm start
  environment_slug: node-js
  instance_count: 1
  instance_size_slug: basic-xxs
  envs:
  - key: NODE_ENV
    value: production
  - key: PORT
    value: "8080"
```

### Docker Deployment
```dockerfile
# Dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

```bash
# Build Docker image
docker build -t my-app .

# Run container
docker run -p 3000:3000 my-app

# Deploy to Docker Hub
docker tag my-app username/my-app
docker push username/my-app
```

---

## Database Deployment

### MongoDB Atlas
```javascript
// Connect to MongoDB Atlas
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    const conn = await mongoose.connect(process.env.MONGODB_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    });
    console.log(`MongoDB Connected: ${conn.connection.host}`);
  } catch (error) {
    console.error('Database connection error:', error);
    process.exit(1);
  }
};

module.exports = connectDB;
```

### PostgreSQL with Railway
```javascript
// Connect to PostgreSQL
const { Pool } = require('pg');

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: {
    rejectUnauthorized: false
  }
});

module.exports = pool;
```

### Redis with Upstash
```javascript
// Connect to Redis
const { Redis } = require('@upstash/redis');

const redis = new Redis({
  url: process.env.UPSTASH_REDIS_REST_URL,
  token: process.env.UPSTASH_REDIS_REST_TOKEN,
});

module.exports = redis;
```

---

## CI/CD Pipeline

### GitHub Actions
```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
    - name: Install dependencies
      run: npm ci
    - name: Run tests
      run: npm test
    - name: Build
      run: npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Deploy to Netlify
      uses: nwtgck/actions-netlify@v2.0
      with:
        publish-dir: './build'
        production-branch: main
        github-token: ${{ secrets.GITHUB_TOKEN }}
        deploy-message: "Deploy from GitHub Actions"
      env:
        NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
        NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
```

### Vercel Deployment
```json
// vercel.json
{
  "version": 2,
  "builds": [
    {
      "src": "package.json",
      "use": "@vercel/static-build",
      "config": {
        "distDir": "build"
      }
    }
  ],
  "routes": [
    {
      "src": "/static/(.*)",
      "headers": {
        "cache-control": "s-maxage=31536000,immutable"
      }
    },
    {
      "src": "/(.*)",
      "dest": "/index.html"
    }
  ]
}
```

### Environment Variables
```bash
# Set environment variables in deployment platform
# Netlify
netlify env:set API_URL https://api.example.com

# Vercel
vercel env add API_URL

# Heroku
heroku config:set API_URL=https://api.example.com
```

---

## Practice Exercises

### Exercise 1: Deploy a React App
```bash
# 1. Create a new React app
npx create-react-app my-deployment-app
cd my-deployment-app

# 2. Add some content
echo 'export default function App() {
  return (
    <div className="App">
      <header className="App-header">
        <h1>My Deployment App</h1>
        <p>This app is deployed!</p>
      </header>
    </div>
  );
}' > src/App.js

# 3. Build the app
npm run build

# 4. Deploy to Netlify
# - Go to netlify.com
# - Drag and drop the build folder
# - Configure custom domain if needed

# 5. Deploy to Vercel
npx vercel

# 6. Deploy to GitHub Pages
npm install --save-dev gh-pages
# Add to package.json:
# "homepage": "https://username.github.io/my-deployment-app"
# "scripts": { "deploy": "gh-pages -d build" }
npm run deploy
```

### Exercise 2: Deploy a Node.js API
```javascript
// server.js
const express = require('express');
const cors = require('cors');
const app = express();
const PORT = process.env.PORT || 3000;

app.use(cors());
app.use(express.json());

// Routes
app.get('/api/health', (req, res) => {
  res.json({ status: 'OK', timestamp: new Date().toISOString() });
});

app.get('/api/users', (req, res) => {
  res.json([
    { id: 1, name: 'John Doe', email: 'john@example.com' },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
  ]);
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

```json
// package.json
{
  "name": "my-api",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "cors": "^2.8.5"
  },
  "engines": {
    "node": "18.x"
  }
}
```

```bash
# Deploy to Heroku
heroku create my-api-app
git add .
git commit -m "Initial commit"
git push heroku main

# Deploy to Railway
railway init
railway up

# Deploy to DigitalOcean
# Create app in DigitalOcean App Platform
# Connect GitHub repository
# Configure build and run commands
```

### Exercise 3: Set up CI/CD Pipeline
```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Run linting
      run: npm run lint
    
    - name: Run tests
      run: npm test
    
    - name: Run type checking
      run: npm run type-check
    
    - name: Build application
      run: npm run build

  deploy-staging:
    if: github.ref == 'refs/heads/develop'
    needs: test
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Deploy to staging
      run: echo "Deploying to staging environment"

  deploy-production:
    if: github.ref == 'refs/heads/main'
    needs: test
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Deploy to production
      run: echo "Deploying to production environment"
```

---

## Summary

### Key Concepts Learned
- ✅ **Static Site Deployment** - GitHub Pages, Netlify, Vercel
- ✅ **React App Deployment** - Build optimization, environment variables
- ✅ **Node.js Backend Deployment** - Heroku, Railway, DigitalOcean
- ✅ **Database Deployment** - MongoDB Atlas, PostgreSQL, Redis
- ✅ **CI/CD Pipeline** - GitHub Actions, automated deployment

### Best Practices
- Use environment variables for configuration
- Implement proper error handling
- Set up monitoring and logging
- Use HTTPS in production
- Implement proper security measures
- Test deployments in staging first

### Common Mistakes
- Not setting up environment variables
- Forgetting to build the application
- Not testing the deployment
- Exposing sensitive information
- Not setting up proper monitoring

---

**Excellent! You've mastered deployment. Ready for Performance Optimization? 🚀**

**Next Tutorial:** [Performance Optimization](./23-performance-optimization.md)
