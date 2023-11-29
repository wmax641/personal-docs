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
#### Extract specific attribute
```bash
jq '.apiVersion'
"v1"
```

#### Iterative extract attributes
```bash
jq '.items[].metadata.name'
jq '.items[] | .metadata.name'
"bar-app"
"foo-app"
"kubes-bootcamp-7c5b8c477c-6bkvc"
```

#### Recursive extract attributes
```bash
jq '.items[].spec.containers[].image'
jq '.items[] | .spec.containers[].image'
"kicbase/echo-server:1.0"
"wmax641/test-app:latest"
"kicbase/echo-server:1.0"
"gcr.io/google-samples/kubernetes-bootcamp:v1"
```
#### Extract multiple attributes
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

However, to extract multiple attributes at different nest levels, employ this strategy:<br>
While traversing down the nested structure, save certain attributes as a variable before continuing traversal.
Then reconstruct the output referencing the variables
```bash
jq '.items[] | .metadata.name as $POD_NAME | 
    .spec.containers[] | {"pod":$POD_NAME, name, image}'

{
  "pod": "kubes-bootcamp-7b95d6fbcc-dll6x",
  "name": "kubes-bootcamp",
  "image": "gcr.io/google-samples/kubernetes-bootcamp:v1"
}
{
  "pod": "kubes-bootcamp-7b95d6fbcc-dll6x",
  "name": "kubes-bootcamp-thirdwheel",
  "image": "alpine:latest"
}
{
  "pod": "kubes-bootcamp-7b95d6fbcc-sgcwt",
  "name": "kubes-bootcamp",
  "image": "gcr.io/google-samples/kubernetes-bootcamp:v1"
}
{
  "pod": "kubes-bootcamp-7b95d6fbcc-sgcwt",
  "name": "kubes-bootcamp-thirdwheel",
  "image": "alpine:latest"
}
```


#### Slice an array 
Similar to python indexing. Outputs into a separate list, so have to re-reference the new list
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
