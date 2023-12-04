# Kubernetes / K8s
## Deployments
### ReplicaSet, RollingUpdate
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
spec:
  replicas: 2
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
        image: jocatalin/kubernetes-bootcamp:v1
      - name: my-app-sidecar
        image: alpine:latest
        command: ['sh', '-c', 'env && sleep 3600']
        env:
        - name: SIMPLE_ENV
          value: "1337"
  minReadySeconds: 5
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
```
## Env variables and ConfigMap
### Key Value pairs
Includes declaration:

* direct Environment variable declaration
* ConfigMap reference

```yaml
...
spec:
  containers:
  - name: my-app
    image: jocatalin/kubernetes-bootcamp:v1
  - name: my-app-sidecar
    image: alpine:latest
    command: ['sh', '-c', 'env && sleep 3600']
    env:
    - name: DIRECT_ENV
      value: "1337"
    - name: CONFIG_MAP_ENV
      valueFrom: 
        configMapKeyRef:
          name: my-app-config-map
          key: config_map_var
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-app-config-map
data:
  # simple keys/value;
  config_map_var: "13337"
  # file-like keys
  file.properties: |
    enemy.types=aliens,monsters
    player.maximum-lives=5
```
### Hardcoded Files
Hardcode files into ConfigMap, then mount them onto volume at specific file location

This example mounts a hard coded file at `/config/config.yml`
```yaml
...
spec:
  containers:
  - name: my-app-sidecar
    image: alpine:latest
    command: ['sh', '-c', 'env && cat /config/config.yml  && sleep 3600']
    volumeMounts:
    - mountPath: /config
      name: config-volume

  volumes:
    - name: config-volume
      configMap:
        name: my-app-config-map
        items:
        - key: config_file_data
          path: config.yml
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-app-config-map
data:
  # file-like keys
  config_file_data: |
    [default]
    some_var:1234
    #some_text asdf qwerty
```
## Services
### NodePort
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - port: 8080
      ## By default and for convenience, the `targetPort` is set to the same value as 
      ## the `port` field.
      #targetPort: 80
      ## By default and for convenience, the Kubernetes control plane will allocate a 
      ## port from a range (default: 30000-32767)
      nodePort: 30003
---
```
