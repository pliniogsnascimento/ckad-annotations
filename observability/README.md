# Observability

### Readiness and Liveness probes

Pod states:
- Pending
- ContainerCreating
- Running 

Pod Conditions:
- PodScheduled
- Initialized
- ContainersReady
- Ready(Ready to receive requests)

### ReadinessProbe
http
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: simple-webapp-color
  name: simple-webapp-color
spec:
  containers:
  - image: simple-webapp-color
    name: simple-webapp-color
    ports:
    - containerPort: 8080
  readinessProbe:
    httpGet:
      path: /api/ready
      port: 8080
    initialDelaySeconds: 10s
    periodSeconds: 5 # How often
    failureThreshold: 8 # Number of attempts, defaults to 3
```

tcp
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: simple-webapp-color
  name: simple-webapp-color
spec:
  containers:
  - image: simple-webapp-color
    name: simple-webapp-color
    ports:
    - containerPort: 8080
  readinessProbe:
    tcpSocket:
      port: 3306
```

exec
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: simple-webapp-color
  name: simple-webapp-color
spec:
  containers:
  - image: simple-webapp-color
    name: simple-webapp-color
    ports:
    - containerPort: 8080
  readinessProbe:
    exec:
      command:
      - cat
      - /app/is_ready
```
