# `jq` cheatsheet
ℹ️ The examples here use a JSON output from kubectl:
```bash
kubectl get po -o json | jq -r

{
    "apiVersion": "v1",
    "items": [
        {
            "apiVersion": "v1",
            "kind": "Pod",
            "metadata": {
                "name": "bar-app",
                "namespace": "default",
            },
            "spec": {
                "containers": [
                    {
                        "image": "kicbase/echo-server:1.0",
                        "name": "bar-app",
                        ...
                    },
                ],
                ...
            }
        },
        ...
    ]
```
## Data Extraction 
Extract top level attribute value
```bash
jq '.apiVersion'
"v1"
```

For each item in array, Extract specific attribute value
```bash
jq '.items[].metadata.name'
jq '.items[] | .metadata.name'
"bar-app"
"foo-app"
"kubes-bootcamp-7c5b8c477c-6bkvc"
```

Greedy and careless grab all attribute values for all items
```bash
jq '.items[].spec.containers[].image'
jq '.items[] | .spec.containers[].image'
"kicbase/echo-server:1.0"
"wmax641/test-app:latest"
"kicbase/echo-server:1.0"
"gcr.io/google-samples/kubernetes-bootcamp:v1"
```

Slice an array (similar to python indexing). Outputs into a separate list, so have to re-reference the new list
```bash
jq '.items[0:2] | .[].metadata.name'
"bar-app"
"foo-app"
```
## Functions
