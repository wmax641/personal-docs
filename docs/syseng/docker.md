# Docker

ℹ️ The examples here use a mock image `wmax641/test-app:latest`

## Operations

### Override / keep running
Useful to keep container running for troubleshooting
```bash
# Simple drop into shell via override
docker run -it --entrypoint=/bin/sh wmax641/test-app:latest

# Override entrypoint into `tail -f /dev/null`
docker run -d --entrypoint tail wmax641/test-app:latest -f /dev/null

```

### Get shell into container
```bsh
docker exec -it <container name/id> /bin/sh
```

### Get Logs
For exited instances - look up history (`docker ps -a`) to find container name
```bash
docker ps             # use '-a' for historical containers
docker logs <container name/id>
```

### Quick multi-kill instances
Change `grep` to match the instances you want to kill
```bash
docker ps | grep "test-app" | awk '{print $1}' | while read line; 
    do docker kill $line; 
done
```

## Build
### Build, Tag and then Push 
```bash
docker build . -t wmax641/test-app:latest
docker push wmax641/test-app:latest
```

### Dockerfile Boilerplate
See [Docker Boilerplate](../patterns/docker.md)
