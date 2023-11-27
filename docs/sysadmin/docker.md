# Docker

ℹ️ The examples here use a mock image `wmax641/test-app:latest`

## Operations

### Override / keep running
Useful to keep container running for troubleshooting
```
# Simple drop into shell via override
docker run -it --entrypoint=/bin/sh wmax641/test-app:latest

# Override entrypoint into `tail -f /dev/null`
docker run -d --entrypoint tail wmax641/test-app:latest -f /dev/null

```

### Get shell into container
```
docker exec -it <container name/id> /bin/sh
```

### Get Logs
For exited instances - look up history (`docker ps -a`) to find container name
```
docker ps             # use '-a' for historical containers
docker logs <container name/id>
```

### Quick multi-kill instances
Change `grep` to match the instances you want to kill
```
docker ps | grep "test-app" | awk '{print $1}' | while read line; 
    do docker kill $line; 
done
```

## Build
### Build, Tag and then Push 
```
docker build . -t wmax641/test-app:latest
docker push wmax641/test-app:latest
```

### Dockerfile Boilerplate
```
FROM alpine:latest

ARG TZ=Australia/Sydney

RUN apk update --no-cache --purge \
    && apk upgrade --no-cache \
    && cp /usr/share/zoneinfo/${TZ} /etc/localtime \
    #&& apk add --no-cache python3 py3-pip tzdata

WORKDIR /app

COPY app.py ./
COPY config.yml ./

RUN adduser -D user -u 1337
USER user

ENTRYPOINT ["./app.py", "--config", "config.yml"]

```
