# References
 - [PodSpec reference](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.22/#podspec-v1-core)

# Configuration
### Environment Variables
Env variables are a list inside containers definition:
```yaml
env:
- name: APP_COLOR
    value: pink
- name: APP_COLOR_2
	valueFrom:
		configMapKeyRef:
- name: APP_COLOR_3
	valueFrom:
		secretRefKey: 
```

### ConfigMaps

Creating configmaps imperative way:
```shell
kubectl create cm <configname> --from-literal=<key>=<value>

kubectl create configmap app-config --from-literal=APP_COLOR=blue \
                                      --from-literal=APP_MOD=prod
kubectl create configmap \
    app-config --from-file=app_config.properties
```

Creating configmaps declarative way:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: app-config
data:
    APP_COLOR: blue
    APP_MOD: prod
```

Inject configMap data into pods:
```yaml
# Env
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
    envFrom:
    - configMapRef:
        name: app-config
---
# Single env from configmap
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
    envFrom:
    - name: APP_COLOR
      valueFrom:
        configMapKeyRef:
            name: app-config
            key: APP_COLOR
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: simple-webapp-color
  name: simple-webapp-color
spec:
  containers:
  - image: kodekloud/webapp-color
    name: simple-webapp-color
    ports:
    - containerPort: 8080
    envFrom:
        volumes:
        - name: app-config-volume
        configMap:
            name: app-config
```

### Secrets

```yaml
image: kodekloud/simple-webapp-mysql
```

### SecurityContexts
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: simple-webapp-color
  name: simple-webapp-color
spec:
  securityContext: # PodSecurityContext
    runAsUser: 1000
  containers:
  - image: simple-webapp-color
    name: simple-webapp-color
    ports:
    - containerPort: 8080
    securityContext: # SecurityContext
      capabilities:
        add: ["MAC_ADMIN"]
```

### ServiceAccounts and RBAC
RBAC:
```yaml
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups:
  - ''
  resources:
  - pods
  verbs:
  - get
  - watch
  - list
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: ServiceAccount
  name: dashboard-sa # Name is case sensitive
  namespace: default
roleRef:
  kind: Role #this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io
```

Create ServiceAccount:
```shell
kubectl create sa <service-account-name>
kubectl create serviceaccount my-sa
```

### Resources
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
    resources:
      requests:
        cpu: 100m
        memory: 256Mi
      limits:
        cpu: 300m
        memory: 500Mi
```
Default namespace limits using LimitRange:
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:
      memory: 512Mi
    defaultRequest:
      memory: 256Mi
    type: Container
---
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-limit-range
spec:
  limits:
  - default:
      cpu: 1
    defaultRequest:
      cpu: 0.5
    type: Container
```
[LimitRange docs.](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)

### Taints and tolerations
Taint nodes:
```shell
# Taint effect will tell whats happens to pods that doesn't tolerate the taint
# NoSchedule | PreferNoSchedule | NoExecute
kubectl taint nodes <node-name> <key>=<value>:<taint-effect>
kubectl taint nodes node1 app=blue:NoSchedule
```
Toleration pods:
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
  tolerations:
  - key: app
    operator: Equal
    value: blue
    effect: NoSchedule
```
[Toleration docs](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.22/#toleration-v1-core)