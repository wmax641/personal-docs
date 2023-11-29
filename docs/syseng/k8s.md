# Kubernetes / K8s

## Operations
#### Get pods with label
```bash
kubectl get pods -l KEY=VALUE
```

#### Run command/shell on pod
`-it` is needed if interactive shell is required<br>
`-c <CONTAINER_NAME>` to specify a container in multi-container pod
```bash
kubectl exec -it POD_NAME -- /bin/bash

# Target specific container in multi-container pod with '-c'
kubectl exec -it -c CONTAINER_NAME POD_NAME -- /bin/bash
```

#### Get Logs of container
```bash
kubectl logs POD_NAME

# Target specific container in multi-container pod with '-c'
kubectl logs -c CONTAINER_NAME POD_NAME
```

#### List all containers
In a specific pod...
```bash
kubectl get pods POD_NAME -o json | jq '.spec.containers[] | 
    {name, image} | join (", ")'
```
In all pods with a label...
```bash
kubectl get pods -l KEY=VALUE -o json | jq '.items[] | 
    .metadata.name as $POD_NAME | 
    .spec.containers[] | {"pod":$POD_NAME, name, image}'
```

## Architecture
### Services
**NodePort** - Exposes the service on each Node's IP at a static port ("NodePort")
![NodePort](../img/k8s-nodeport.jpg)
