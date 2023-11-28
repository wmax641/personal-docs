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

Greedy and careless grab specified attribute value for all items
```bash
jq '.items[].spec.containers[].image'
jq '.items[] | .spec.containers[].image'
"kicbase/echo-server:1.0"
"wmax641/test-app:latest"
"kicbase/echo-server:1.0"
"gcr.io/google-samples/kubernetes-bootcamp:v1"
```
Extract multiple attribute values
```bash
jq '.items[].spec.containers[] | {name, image}'
{
  "name": "bar-app",
  "image": "kicbase/echo-server:1.0"
}
{
  "name": "wma-test-app",
  "image": "wmax641/test-app:latest"
}
{
  "name": "foo-app",
  "image": "kicbase/echo-server:1.0"
}
{
  "name": "kubes-bootcamp",
  "image": "gcr.io/google-samples/kubernetes-bootcamp:v1"
}
```
```bash
jq '.items[].spec.containers[] | {name, image} | join(",")'
"bar-app,kicbase/echo-server:1.0"
"wma-test-app,wmax641/test-app:latest"
"foo-app,kicbase/echo-server:1.0"
"kubes-bootcamp,gcr.io/google-samples/kubernetes-bootcamp:v1"
```

Slice an array (similar to python indexing). Outputs into a separate list, so have to re-reference the new list
```bash
jq '.items[0:2] | .[].metadata.name'
"bar-app"
"foo-app"
```
## Functions
Get length of an array
```bash
jq '.items[].spec.containers | length'
2
1
1
```
Get keys for attribute
```bash
jq '.items[1].spec.containers[0] | keys '
[
  "image",
  "imagePullPolicy",
  "name",
  "resources",
  "terminationMessagePath",
  "terminationMessagePolicy",
  "volumeMounts"
]
```
