# Complete GitHub Actions Tutorial 🚀

## Table of Contents
1. [What is GitHub Actions?](#what-is-github-actions)
2. [Core Concepts](#core-concepts)
3. [Getting Started](#getting-started)
4. [Basic Workflow Examples](#basic-workflow-examples)
5. [Advanced Workflows](#advanced-workflows)
6. [Popular Actions](#popular-actions)
7. [Environment Variables & Secrets](#environment-variables--secrets)
8. [Matrix Strategies](#matrix-strategies)
9. [Conditional Logic](#conditional-logic)
10. [Artifacts & Caching](#artifacts--caching)
11. [Deployment Workflows](#deployment-workflows)
12. [Self-Hosted Runners](#self-hosted-runners)
13. [Best Practices](#best-practices)
14. [Troubleshooting](#troubleshooting)
15. [Real-World Examples](#real-world-examples)

---

## What is GitHub Actions?

**GitHub Actions** is a **CI/CD platform** that allows you to automate your software development workflows directly in your GitHub repository.

### Key Benefits:
- ✅ **Automated Testing** - Run tests on every push/PR
- ✅ **Automated Deployment** - Deploy to production automatically
- ✅ **Code Quality Checks** - Linting, formatting, security scans
- ✅ **Cross-Platform Testing** - Test on Windows, macOS, Linux
- ✅ **Free for Public Repos** - Unlimited minutes for public repositories
- ✅ **Integrated with GitHub** - Native integration with issues, PRs, releases

### How It Works:
```
Code Push → Trigger Workflow → Run Jobs → Deploy/Notify
```

---

## Core Concepts

### 1. **Workflow**
A workflow is an **automated process** defined in a YAML file in `.github/workflows/`

### 2. **Event**
Something that **triggers** a workflow (push, pull request, release, etc.)

### 3. **Job**
A **set of steps** that run on the same runner

### 4. **Step**
An **individual task** within a job

### 5. **Action**
A **reusable unit** of code that performs a specific task

### 6. **Runner**
A **server** that runs your workflows (GitHub-hosted or self-hosted)

---

## Getting Started

### 1. Create Your First Workflow

Create `.github/workflows/hello-world.yml`:

```yaml
name: Hello World

# Trigger on push to main branch
on:
  push:
    branches: [ main ]

# Define jobs
jobs:
  hello:
    # Run on Ubuntu latest
    runs-on: ubuntu-latest
    
    # Steps to execute
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Say hello
      run: echo "Hello, World!"
      
    - name: Show environment info
      run: |
        echo "Runner OS: $RUNNER_OS"
        echo "Node version: $(node --version)"
        echo "Python version: $(python --version)"
```

### 2. Workflow File Structure

```yaml
name: Workflow Name                    # Display name
on: [push, pull_request]               # Triggers
env:                                   # Environment variables
  NODE_VERSION: '18'
jobs:                                  # Jobs to run
  job-name:
    runs-on: ubuntu-latest             # Runner type
    steps:                             # Steps in job
    - name: Step name
      uses: action/name@version        # Action to use
      with:                            # Inputs for action
        input-name: value
    - name: Run command
      run: echo "Hello"                # Shell command
```

---

## Basic Workflow Examples

### Example 1: Node.js Application

```yaml
name: Node.js CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        node-version: [16, 18, 20]
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Setup Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v4
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run tests
      run: npm test
      
    - name: Run linting
      run: npm run lint
      
    - name: Build application
      run: npm run build
```

### Example 2: Python Application

```yaml
name: Python CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        python-version: [3.8, 3.9, '3.10', '3.11']
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Setup Python ${{ matrix.python-version }}
      uses: actions/setup-python@v4
      with:
        python-version: ${{ matrix.python-version }}
        
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        pip install pytest pytest-cov
        
    - name: Run tests
      run: |
        pytest --cov=src tests/
        
    - name: Upload coverage
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage.xml
```

### Example 3: Docker Application

```yaml
name: Docker CI/CD

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v3
      
    - name: Log in to Container Registry
      uses: docker/login-action@v3
      with:
        registry: ${{ env.REGISTRY }}
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}
        
    - name: Extract metadata
      id: meta
      uses: docker/metadata-action@v5
      with:
        images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
        tags: |
          type=ref,event=branch
          type=ref,event=pr
          type=sha,prefix={{branch}}-
          
    - name: Build and push Docker image
      uses: docker/build-push-action@v5
      with:
        context: .
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}
```

---

## Advanced Workflows

### Example 4: Multi-Environment Deployment

```yaml
name: Deploy to Multiple Environments

on:
  push:
    branches: [ main, develop ]
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy to'
        required: true
        default: 'staging'
        type: choice
        options:
        - staging
        - production

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '18'
        cache: 'npm'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run tests
      run: npm test
      
    - name: Run security audit
      run: npm audit --audit-level moderate

  deploy-staging:
    needs: test
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    environment: staging
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Deploy to staging
      run: |
        echo "Deploying to staging environment"
        # Add your deployment commands here
        
    - name: Notify team
      uses: 8398a7/action-slack@v3
      with:
        status: ${{ job.status }}
        channel: '#deployments'
      env:
        SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}

  deploy-production:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Deploy to production
      run: |
        echo "Deploying to production environment"
        # Add your deployment commands here
        
    - name: Create release
      uses: actions/create-release@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        tag_name: v${{ github.run_number }}
        release_name: Release v${{ github.run_number }}
        body: |
          Changes in this Release
          - Automated deployment from main branch
        draft: false
        prerelease: false
```

### Example 5: Scheduled Workflows

```yaml
name: Scheduled Maintenance

on:
  schedule:
    # Run every Monday at 9 AM UTC
    - cron: '0 9 * * 1'
  workflow_dispatch:

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Run security scan
      uses: github/super-linter@v4
      env:
        DEFAULT_BRANCH: main
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        
    - name: Dependency check
      run: |
        npm audit --audit-level high
        npm outdated

  cleanup:
    runs-on: ubuntu-latest
    steps:
    - name: Clean up old artifacts
      uses: actions/github-script@v7
      with:
        script: |
          const artifacts = await github.rest.actions.listArtifactsForRepo({
            owner: context.repo.owner,
            repo: context.repo.repo,
            per_page: 100
          });
          
          const cutoffDate = new Date();
          cutoffDate.setDate(cutoffDate.getDate() - 30);
          
          for (const artifact of artifacts.data.artifacts) {
            if (new Date(artifact.created_at) < cutoffDate) {
              await github.rest.actions.deleteArtifact({
                owner: context.repo.owner,
                repo: context.repo.repo,
                artifact_id: artifact.id
              });
              console.log(`Deleted artifact: ${artifact.name}`);
            }
          }
```

---

## Popular Actions

### Essential Actions

```yaml
# Checkout code
- uses: actions/checkout@v4

# Setup Node.js
- uses: actions/setup-node@v4
  with:
    node-version: '18'
    cache: 'npm'

# Setup Python
- uses: actions/setup-python@v4
  with:
    python-version: '3.11'

# Setup Java
- uses: actions/setup-java@v4
  with:
    distribution: 'temurin'
    java-version: '17'

# Setup Go
- uses: actions/setup-go@v4
  with:
    go-version: '1.21'

# Setup Docker Buildx
- uses: docker/setup-buildx-action@v3

# Cache dependencies
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
```

### Third-Party Actions

```yaml
# Code quality
- uses: github/super-linter@v4
  env:
    DEFAULT_BRANCH: main
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

# Security scanning
- uses: aquasecurity/trivy-action@master
  with:
    scan-type: 'fs'
    scan-ref: '.'
    format: 'sarif'
    output: 'trivy-results.sarif'

# Deploy to AWS
- uses: aws-actions/configure-aws-credentials@v4
  with:
    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    aws-region: us-east-1

# Deploy to Azure
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}

# Deploy to Google Cloud
- uses: google-github-actions/auth@v2
  with:
    credentials_json: ${{ secrets.GCP_SA_KEY }}
```

---

## Environment Variables & Secrets

### Environment Variables

```yaml
name: Environment Variables Example

on: [push]

env:
  NODE_ENV: production
  API_URL: https://api.example.com

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      BUILD_NUMBER: ${{ github.run_number }}
      BRANCH_NAME: ${{ github.ref_name }}
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Show environment variables
      run: |
        echo "NODE_ENV: $NODE_ENV"
        echo "API_URL: $API_URL"
        echo "BUILD_NUMBER: $BUILD_NUMBER"
        echo "BRANCH_NAME: $BRANCH_NAME"
        echo "GITHUB_SHA: $GITHUB_SHA"
        echo "GITHUB_REF: $GITHUB_REF"
```

### Secrets Management

```yaml
name: Secrets Example

on: [push]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Deploy with secrets
      run: |
        echo "Deploying with API key: ${{ secrets.API_KEY }}"
        echo "Database URL: ${{ secrets.DATABASE_URL }}"
        # Use secrets in your deployment script
      env:
        API_KEY: ${{ secrets.API_KEY }}
        DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

### Setting Up Secrets

1. Go to your repository
2. Click **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Add your secret name and value

---

## Matrix Strategies

### Cross-Platform Testing

```yaml
name: Cross-Platform Tests

on: [push, pull_request]

jobs:
  test:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node-version: [16, 18, 20]
        exclude:
          - os: windows-latest
            node-version: 16
          - os: macos-latest
            node-version: 20
    
    runs-on: ${{ matrix.os }}
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Setup Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v4
      with:
        node-version: ${{ matrix.node-version }}
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run tests
      run: npm test
```

### Include/Exclude Matrix

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest, macos-latest]
    node-version: [16, 18, 20]
    include:
      - os: ubuntu-latest
        node-version: 21
        experimental: true
    exclude:
      - os: windows-latest
        node-version: 16
```

---

## Conditional Logic

### Conditional Steps

```yaml
name: Conditional Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Install dependencies
      run: npm ci
      
    - name: Run tests
      if: github.ref == 'refs/heads/main'
      run: npm test
      
    - name: Run integration tests
      if: github.event_name == 'pull_request'
      run: npm run test:integration
      
    - name: Deploy to staging
      if: github.ref == 'refs/heads/develop'
      run: npm run deploy:staging
      
    - name: Deploy to production
      if: github.ref == 'refs/heads/main'
      run: npm run deploy:production
```

### Conditional Jobs

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - name: Run tests
      run: npm test
      
  deploy-staging:
    needs: test
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    steps:
    - name: Deploy to staging
      run: echo "Deploying to staging"
      
  deploy-production:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
    - name: Deploy to production
      run: echo "Deploying to production"
```

---

## Artifacts & Caching

### Uploading Artifacts

```yaml
name: Build and Upload Artifacts

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '18'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Build application
      run: npm run build
      
    - name: Upload build artifacts
      uses: actions/upload-artifact@v4
      with:
        name: build-files
        path: dist/
        retention-days: 30
        
    - name: Upload test results
      uses: actions/upload-artifact@v4
      with:
        name: test-results
        path: test-results/
        if-no-files-found: warn
```

### Downloading Artifacts

```yaml
name: Download and Deploy

on: [push]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Download artifacts
      uses: actions/download-artifact@v4
      with:
        name: build-files
        path: dist/
        
    - name: Deploy
      run: |
        echo "Deploying files from dist/"
        ls -la dist/
```

### Caching Dependencies

```yaml
name: Cache Example

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Cache npm dependencies
      uses: actions/cache@v3
      with:
        path: ~/.npm
        key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
        restore-keys: |
          ${{ runner.os }}-node-
          
    - name: Install dependencies
      run: npm ci
      
    - name: Cache build output
      uses: actions/cache@v3
      with:
        path: dist/
        key: ${{ runner.os }}-build-${{ github.sha }}
        restore-keys: |
          ${{ runner.os }}-build-
```

---

## Deployment Workflows

### Example 1: Deploy to Vercel

```yaml
name: Deploy to Vercel

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Deploy to Vercel
      uses: amondnet/vercel-action@v25
      with:
        vercel-token: ${{ secrets.VERCEL_TOKEN }}
        vercel-org-id: ${{ secrets.ORG_ID }}
        vercel-project-id: ${{ secrets.PROJECT_ID }}
        vercel-args: '--prod'
```

### Example 2: Deploy to AWS S3

```yaml
name: Deploy to AWS S3

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1
        
    - name: Build application
      run: |
        npm ci
        npm run build
        
    - name: Deploy to S3
      run: |
        aws s3 sync dist/ s3://${{ secrets.S3_BUCKET }} --delete
        
    - name: Invalidate CloudFront
      run: |
        aws cloudfront create-invalidation --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }} --paths "/*"
```

### Example 3: Deploy to Kubernetes

```yaml
name: Deploy to Kubernetes

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Configure kubectl
      uses: azure/setup-kubectl@v3
      
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v3
      
    - name: Log in to Container Registry
      uses: docker/login-action@v3
      with:
        registry: ghcr.io
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}
        
    - name: Build and push Docker image
      uses: docker/build-push-action@v5
      with:
        context: .
        push: true
        tags: ghcr.io/${{ github.repository }}:${{ github.sha }}
        
    - name: Deploy to Kubernetes
      run: |
        echo "${{ secrets.KUBE_CONFIG }}" | base64 -d > kubeconfig
        export KUBECONFIG=kubeconfig
        kubectl set image deployment/my-app my-app=ghcr.io/${{ github.repository }}:${{ github.sha }}
        kubectl rollout status deployment/my-app
```

---

## Self-Hosted Runners

### Setting Up Self-Hosted Runner

```yaml
name: Self-Hosted Runner Example

on: [push]

jobs:
  build:
    runs-on: self-hosted
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Run on self-hosted runner
      run: |
        echo "Running on self-hosted runner"
        uname -a
        df -h
```

### Runner Setup Script

```bash
# Download and configure runner
mkdir actions-runner && cd actions-runner
curl -o actions-runner-linux-x64-2.311.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.311.0/actions-runner-linux-x64-2.311.0.tar.gz
tar xzf ./actions-runner-linux-x64-2.311.0.tar.gz

# Configure runner
./config.sh --url https://github.com/yourusername/yourrepo --token YOUR_TOKEN

# Install and start
sudo ./svc.sh install
sudo ./svc.sh start
```

---

## Best Practices

### 1. Workflow Organization

```yaml
# Good: Clear naming and structure
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
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Run tests
      run: npm test
      
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
    - name: Build application
      run: npm run build
      
  deploy:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
    - name: Deploy
      run: npm run deploy
```

### 2. Security Best Practices

```yaml
name: Secure Workflow

on: [push]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      security-events: write
      
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Run Trivy vulnerability scanner
      uses: aquasecurity/trivy-action@master
      with:
        scan-type: 'fs'
        scan-ref: '.'
        format: 'sarif'
        output: 'trivy-results.sarif'
        
    - name: Upload Trivy scan results
      uses: github/codeql-action/upload-sarif@v2
      with:
        sarif_file: 'trivy-results.sarif'
```

### 3. Performance Optimization

```yaml
name: Optimized Workflow

on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Cache dependencies
      uses: actions/cache@v3
      with:
        path: ~/.npm
        key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
        restore-keys: |
          ${{ runner.os }}-node-
          
    - name: Install dependencies
      run: npm ci
      
    - name: Run tests in parallel
      run: npm run test:parallel
```

### 4. Error Handling

```yaml
name: Error Handling Example

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    continue-on-error: true
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Build application
      run: npm run build
      
    - name: Notify on failure
      if: failure()
      uses: 8398a7/action-slack@v3
      with:
        status: failure
        channel: '#alerts'
      env:
        SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

---

## Troubleshooting

### Common Issues

#### 1. Permission Denied
```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    steps:
    - name: Deploy
      run: echo "Deploying..."
```

#### 2. Cache Miss
```yaml
- name: Cache dependencies
  uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
      ${{ runner.os }}-
```

#### 3. Timeout Issues
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    timeout-minutes: 30
    steps:
    - name: Run tests
      run: npm test
```

### Debugging Workflows

```yaml
name: Debug Workflow

on: [push]

jobs:
  debug:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Debug environment
      run: |
        echo "Runner OS: $RUNNER_OS"
        echo "GitHub Event: $GITHUB_EVENT_NAME"
        echo "GitHub Ref: $GITHUB_REF"
        echo "GitHub SHA: $GITHUB_SHA"
        echo "GitHub Actor: $GITHUB_ACTOR"
        
    - name: Debug files
      run: |
        ls -la
        pwd
        whoami
```

---

## Real-World Examples

### Example 1: Full-Stack Application

```yaml
name: Full-Stack CI/CD

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test-frontend:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '18'
        cache: 'npm'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run tests
      run: npm test
      
    - name: Run linting
      run: npm run lint
      
    - name: Build frontend
      run: npm run build
      
    - name: Upload build artifacts
      uses: actions/upload-artifact@v4
      with:
        name: frontend-build
        path: dist/

  test-backend:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Setup Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.11'
        
    - name: Install dependencies
      run: |
        pip install -r requirements.txt
        pip install pytest pytest-cov
        
    - name: Run tests
      run: pytest --cov=src tests/
      
    - name: Upload coverage
      uses: codecov/codecov-action@v3

  build-docker:
    needs: [test-frontend, test-backend]
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v3
      
    - name: Log in to Container Registry
      uses: docker/login-action@v3
      with:
        registry: ghcr.io
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}
        
    - name: Build and push Docker image
      uses: docker/build-push-action@v5
      with:
        context: .
        push: true
        tags: |
          ghcr.io/${{ github.repository }}:latest
          ghcr.io/${{ github.repository }}:${{ github.sha }}

  deploy-staging:
    needs: build-docker
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    environment: staging
    steps:
    - name: Deploy to staging
      run: |
        echo "Deploying to staging environment"
        # Add your deployment commands here

  deploy-production:
    needs: build-docker
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    steps:
    - name: Deploy to production
      run: |
        echo "Deploying to production environment"
        # Add your deployment commands here
```

### Example 2: Monorepo Workflow

```yaml
name: Monorepo CI/CD

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  changes:
    runs-on: ubuntu-latest
    outputs:
      frontend: ${{ steps.changes.outputs.frontend }}
      backend: ${{ steps.changes.outputs.backend }}
      docs: ${{ steps.changes.outputs.docs }}
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      with:
        fetch-depth: 0
        
    - name: Check for changes
      uses: dorny/paths-filter@v2
      id: changes
      with:
        filters: |
          frontend:
            - 'frontend/**'
          backend:
            - 'backend/**'
          docs:
            - 'docs/**'

  test-frontend:
    needs: changes
    if: needs.changes.outputs.frontend == 'true'
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '18'
        cache: 'npm'
        cache-dependency-path: frontend/package-lock.json
        
    - name: Install dependencies
      run: |
        cd frontend
        npm ci
        
    - name: Run tests
      run: |
        cd frontend
        npm test

  test-backend:
    needs: changes
    if: needs.changes.outputs.backend == 'true'
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      
    - name: Setup Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.11'
        
    - name: Install dependencies
      run: |
        cd backend
        pip install -r requirements.txt
        
    - name: Run tests
      run: |
        cd backend
        pytest tests/
```

---

## Summary

### Key Takeaways

1. **GitHub Actions** automates your development workflow
2. **Workflows** are defined in YAML files in `.github/workflows/`
3. **Jobs** run on runners and contain steps
4. **Actions** are reusable units of code
5. **Secrets** store sensitive information securely
6. **Matrix strategies** enable parallel testing
7. **Artifacts** store build outputs
8. **Caching** improves performance
9. **Self-hosted runners** provide more control
10. **Best practices** ensure security and performance

### Essential Commands

```yaml
# Basic workflow structure
name: Workflow Name
on: [push, pull_request]
jobs:
  job-name:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - run: echo "Hello World"
```

### Next Steps

1. **Start simple** - Create basic workflows
2. **Add testing** - Integrate your test suite
3. **Add deployment** - Automate deployments
4. **Optimize** - Use caching and matrix strategies
5. **Secure** - Implement security best practices
6. **Monitor** - Add notifications and alerts

---

## Resources

### Documentation
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Workflow Syntax](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [Actions Marketplace](https://github.com/marketplace?type=actions)

### Learning Resources
- [GitHub Learning Lab](https://lab.github.com/)
- [GitHub Actions Examples](https://github.com/actions/starter-workflows)
- [Awesome Actions](https://github.com/sdras/awesome-actions)

### Tools
- **GitHub CLI** - `gh` command-line tool
- **GitHub Desktop** - GUI for Git and GitHub
- **VS Code** - Built-in GitHub Actions support

---

**Happy Automating! 🚀**

Remember: Start with simple workflows and gradually add complexity. GitHub Actions is a powerful tool that can significantly improve your development workflow!
