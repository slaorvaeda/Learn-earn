# Kubernetes Hands-On Practice 🚀

## Prerequisites
Make sure you have Kubernetes running locally:
```bash
# Check if kubectl is installed
kubectl version --client

# Check cluster status
kubectl cluster-info

# If using Minikube
minikube status
```

## Exercise 1: Your First Pod
Let's start with the simplest Kubernetes object - a Pod.

### Step 1: Create a simple Pod
```yaml
# pod-example.yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-first-pod
  labels:
    app: hello-world
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

### Step 2: Deploy and manage the Pod
```bash
# Create the pod
kubectl apply -f pod-example.yaml

# Check pod status
kubectl get pods

# Get detailed information
kubectl describe pod my-first-pod

# View pod logs
kubectl logs my-first-pod

# Delete the pod
kubectl delete pod my-first-pod
```

## Exercise 2: Deployment with Multiple Replicas
Learn how to manage multiple instances of your application.

### Step 1: Create a Deployment
```yaml
# deployment-example.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labels:
    app: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: nginx
        image: nginx:latest
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

### Step 2: Deploy and Scale
```bash
# Create deployment
kubectl apply -f deployment-example.yaml

# Check deployment status
kubectl get deployments
kubectl get pods

# Scale up to 5 replicas
kubectl scale deployment web-app --replicas=5

# Scale down to 2 replicas
kubectl scale deployment web-app --replicas=2

# Check rollout history
kubectl rollout history deployment/web-app

# Delete deployment
kubectl delete deployment web-app
```

## Exercise 3: Service and Load Balancing
Learn how to expose your application to the network.

### Step 1: Create a Web Application
First, let's create a simple web app that shows which pod is serving the request.

```yaml
# web-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 3
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
        image: nginx:alpine
        ports:
        - containerPort: 80
        command: ["/bin/sh"]
        args:
        - -c
        - |
          echo '<html><body><h1>Hello from Pod: $HOSTNAME</h1><p>Time: $(date)</p></body></html>' > /usr/share/nginx/html/index.html
          nginx -g "daemon off;"
```

### Step 2: Create a Service
```yaml
# web-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
spec:
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
```

### Step 3: Deploy and Test
```bash
# Deploy the application
kubectl apply -f web-app-deployment.yaml
kubectl apply -f web-app-service.yaml

# Check services
kubectl get services

# Get the external IP (if using cloud provider)
kubectl get service web-app-service

# For local testing (Minikube)
minikube service web-app-service

# Test load balancing by accessing the service multiple times
curl http://<external-ip>
```

## Exercise 4: Environment Variables and ConfigMaps
Learn how to configure your applications.

### Step 1: Create a ConfigMap
```yaml
# configmap-example.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database.host: "localhost"
  database.port: "5432"
  app.environment: "development"
  app.debug: "true"
```

### Step 2: Create a Deployment using ConfigMap
```yaml
# app-with-config.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: config-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: config-app
  template:
    metadata:
      labels:
        app: config-app
    spec:
      containers:
      - name: config-app
        image: busybox
        command: ["/bin/sh"]
        args:
        - -c
        - |
          echo "Database Host: $DATABASE_HOST"
          echo "Database Port: $DATABASE_PORT"
          echo "Environment: $APP_ENVIRONMENT"
          echo "Debug: $APP_DEBUG"
          sleep 3600
        env:
        - name: DATABASE_HOST
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: database.host
        - name: DATABASE_PORT
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: database.port
        - name: APP_ENVIRONMENT
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: app.environment
        - name: APP_DEBUG
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: app.debug
```

### Step 3: Deploy and Check
```bash
# Create ConfigMap
kubectl apply -f configmap-example.yaml

# Deploy the app
kubectl apply -f app-with-config.yaml

# Check logs to see environment variables
kubectl logs -l app=config-app

# Clean up
kubectl delete deployment config-app
kubectl delete configmap app-config
```

## Exercise 5: Secrets Management
Learn how to handle sensitive data securely.

### Step 1: Create a Secret
```bash
# Create secret from command line
kubectl create secret generic db-secret \
  --from-literal=username=admin \
  --from-literal=password=secretpassword

# Or create from YAML
echo -n 'admin' | base64  # YWRtaW4=
echo -n 'secretpassword' | base64  # c2VjcmV0cGFzc3dvcmQ=
```

```yaml
# secret-example.yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=
  password: c2VjcmV0cGFzc3dvcmQ=
```

### Step 2: Use Secret in Deployment
```yaml
# app-with-secret.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: secret-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: secret-app
  template:
    metadata:
      labels:
        app: secret-app
    spec:
      containers:
      - name: secret-app
        image: busybox
        command: ["/bin/sh"]
        args:
        - -c
        - |
          echo "Username: $DB_USERNAME"
          echo "Password: $DB_PASSWORD"
          sleep 3600
        env:
        - name: DB_USERNAME
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: username
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
```

## Exercise 6: Health Checks and Self-Healing
Learn how Kubernetes keeps your applications healthy.

### Step 1: Create an App with Health Checks
```yaml
# healthy-app.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: healthy-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: healthy-app
  template:
    metadata:
      labels:
        app: healthy-app
    spec:
      containers:
      - name: healthy-app
        image: nginx:alpine
        ports:
        - containerPort: 80
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
```

### Step 2: Test Self-Healing
```bash
# Deploy the app
kubectl apply -f healthy-app.yaml

# Check pod status
kubectl get pods

# Simulate a pod failure
kubectl delete pod <pod-name>

# Watch Kubernetes recreate the pod
kubectl get pods -w

# Check events
kubectl get events
```

## Exercise 7: Rolling Updates
Learn how to update applications without downtime.

### Step 1: Deploy Initial Version
```yaml
# app-v1.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolling-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: rolling-app
  template:
    metadata:
      labels:
        app: rolling-app
    spec:
      containers:
      - name: rolling-app
        image: nginx:1.20
        ports:
        - containerPort: 80
```

### Step 2: Perform Rolling Update
```bash
# Deploy v1
kubectl apply -f app-v1.yaml

# Update to v2
kubectl set image deployment/rolling-app rolling-app=nginx:1.21

# Watch the rollout
kubectl rollout status deployment/rolling-app

# Check rollout history
kubectl rollout history deployment/rolling-app

# Rollback if needed
kubectl rollout undo deployment/rolling-app
```

## Exercise 8: Complete Application Stack
Deploy a full-stack application with database.

### Step 1: Create Database Deployment
```yaml
# database.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: database
spec:
  replicas: 1
  selector:
    matchLabels:
      app: database
  template:
    metadata:
      labels:
        app: database
    spec:
      containers:
      - name: postgres
        image: postgres:13
        env:
        - name: POSTGRES_DB
          value: "myapp"
        - name: POSTGRES_USER
          value: "user"
        - name: POSTGRES_PASSWORD
          value: "password"
        ports:
        - containerPort: 5432
---
apiVersion: v1
kind: Service
metadata:
  name: database-service
spec:
  selector:
    app: database
  ports:
  - port: 5432
    targetPort: 5432
```

### Step 2: Create Web Application
```yaml
# web-app.yaml
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
        image: nginx:alpine
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_URL
          value: "postgresql://user:password@database-service:5432/myapp"
---
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
spec:
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
```

### Step 3: Deploy the Stack
```bash
# Deploy database
kubectl apply -f database.yaml

# Wait for database to be ready
kubectl wait --for=condition=available deployment/database

# Deploy web app
kubectl apply -f web-app.yaml

# Check all resources
kubectl get all

# Get service URL
kubectl get service web-app-service
```

## Debugging and Troubleshooting

### Common Commands
```bash
# Get detailed pod information
kubectl describe pod <pod-name>

# View pod logs
kubectl logs <pod-name>

# Follow logs in real-time
kubectl logs -f <pod-name>

# Execute commands in pod
kubectl exec -it <pod-name> -- /bin/bash

# Check events
kubectl get events --sort-by=.metadata.creationTimestamp

# Check resource usage
kubectl top pods
kubectl top nodes

# Debug service connectivity
kubectl run debug --image=busybox -it --rm -- /bin/sh
# Inside the debug pod:
# nslookup database-service
# wget -qO- http://web-app-service
```

### Common Issues and Solutions

1. **Pod stuck in Pending**: Check resource constraints
2. **Pod keeps restarting**: Check logs and health checks
3. **Service not accessible**: Check selector labels
4. **Image pull errors**: Check image name and registry access

## Practice Challenges

### Challenge 1: Auto-scaling
Set up Horizontal Pod Autoscaler (HPA) for your application.

### Challenge 2: Persistent Storage
Create a database with persistent storage using PersistentVolumes.

### Challenge 3: Ingress
Set up an Ingress controller to route traffic to multiple services.

### Challenge 4: Namespaces
Organize your applications using namespaces for different environments.

## Next Steps
1. Learn about PersistentVolumes and PersistentVolumeClaims
2. Explore Ingress controllers (NGINX, Traefik)
3. Study Helm for package management
4. Practice with real-world scenarios
5. Learn about monitoring and logging (Prometheus, Grafana)
