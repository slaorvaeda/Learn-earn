# Kubernetes Fundamentals 🚀

## What is Kubernetes?
Kubernetes (K8s) is like a smart manager for your Docker containers. While Docker helps you package applications, Kubernetes helps you:
- **Deploy** containers across multiple machines
- **Scale** applications up and down automatically
- **Manage** container health and recovery
- **Load balance** traffic between containers
- **Roll out** updates without downtime

Think of it as:
- **Docker** = Shipping containers
- **Kubernetes** = Smart port management system

## Key Concepts

### 1. Cluster
A Kubernetes cluster is a set of machines (nodes) that run your containerized applications.

### 2. Node
- **Master Node**: Controls the cluster
- **Worker Node**: Runs your applications

### 3. Pod
The smallest deployable unit in Kubernetes. A pod can contain one or more containers.

### 4. Service
A way to expose your application to the network.

### 5. Deployment
Manages your application's lifecycle (scaling, updates, rollbacks).

## Kubernetes Architecture

```
┌─────────────────────────────────────────────────┐
│                 Kubernetes Cluster              │
├─────────────────────────────────────────────────┤
│  Master Node (Control Plane)                    │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ │
│  │   API       │ │ Scheduler   │ │ Controller  │ │
│  │   Server    │ │             │ │   Manager   │ │
│  └─────────────┘ └─────────────┘ └─────────────┘ │
├─────────────────────────────────────────────────┤
│  Worker Nodes                                   │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ │
│  │    Pod      │ │    Pod      │ │    Pod      │ │
│  │  (App 1)    │ │  (App 2)    │ │  (App 3)    │ │
│  └─────────────┘ └─────────────┘ └─────────────┘ │
└─────────────────────────────────────────────────┘
```

## Essential Kubernetes Objects

### 1. Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

### 2. Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: my-app:latest
        ports:
        - containerPort: 3000
```

### 3. Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 3000
  type: LoadBalancer
```

## Kubernetes Installation Options

### Option 1: Minikube (Local Development)
```bash
# Install Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube

# Start Minikube
minikube start

# Check status
minikube status
```

### Option 2: Docker Desktop (Easiest for Beginners)
1. Install Docker Desktop
2. Enable Kubernetes in Docker Desktop settings
3. You're ready to go!

### Option 3: Cloud Providers
- **Google Kubernetes Engine (GKE)**
- **Amazon Elastic Kubernetes Service (EKS)**
- **Azure Kubernetes Service (AKS)**

## Essential kubectl Commands

```bash
# Check cluster info
kubectl cluster-info

# Get nodes
kubectl get nodes

# Get all resources
kubectl get all

# Create a deployment
kubectl create deployment my-app --image=nginx

# Scale deployment
kubectl scale deployment my-app --replicas=3

# Expose deployment as service
kubectl expose deployment my-app --port=80 --type=LoadBalancer

# Get pods
kubectl get pods

# Get services
kubectl get services

# Describe a pod
kubectl describe pod <pod-name>

# View pod logs
kubectl logs <pod-name>

# Execute command in pod
kubectl exec -it <pod-name> -- bash

# Delete resources
kubectl delete deployment my-app
kubectl delete service my-app
```

## YAML Configuration Files

### Basic Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: my-app
spec:
  containers:
  - name: nginx
    image: nginx:1.20
    ports:
    - containerPort: 80
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

### Deployment with Environment Variables
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: web-app
        image: my-web-app:latest
        ports:
        - containerPort: 3000
        env:
        - name: NODE_ENV
          value: "production"
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: url
```

### Service Types
```yaml
# ClusterIP (default) - internal only
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 3000

# NodePort - accessible from outside
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 3000
    nodePort: 30080

# LoadBalancer - cloud load balancer
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 3000
```

## Common Kubernetes Patterns

### 1. Rolling Updates
```bash
# Update image
kubectl set image deployment/my-app my-app=my-app:v2

# Check rollout status
kubectl rollout status deployment/my-app

# Rollback if needed
kubectl rollout undo deployment/my-app
```

### 2. Health Checks
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  template:
    spec:
      containers:
      - name: web-app
        image: my-app:latest
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5
```

### 3. ConfigMaps and Secrets
```yaml
# ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database.host: "localhost"
  database.port: "5432"

# Secret
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=  # base64 encoded
  password: cGFzc3dvcmQ=  # base64 encoded
```

## Why Use Kubernetes?

### Without Kubernetes:
- Manual container management
- No automatic scaling
- Manual health monitoring
- Complex load balancing setup
- Difficult updates and rollbacks

### With Kubernetes:
- ✅ Automatic container orchestration
- ✅ Auto-scaling based on demand
- ✅ Self-healing (restarts failed containers)
- ✅ Built-in load balancing
- ✅ Rolling updates and rollbacks
- ✅ Resource management
- ✅ Service discovery

## Next Steps
1. Set up a local Kubernetes cluster (Minikube or Docker Desktop)
2. Deploy your first application
3. Learn about persistent storage (PersistentVolumes)
4. Explore advanced features (Ingress, Helm, Operators)
5. Practice with real-world scenarios
