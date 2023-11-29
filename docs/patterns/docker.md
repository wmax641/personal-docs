# Dockerfile Templates

## Generic Dockerfile
```docker
FROM alpine:latest

#LABEL tag_key="tag_value"

#ENV SOME_ENV_VAR=SOME_VALUE

ARG TZ=Australia/Sydney

RUN apk update --no-cache --purge \
    && apk upgrade --no-cache \
    && apk add --no-cache python3 py3-pip tzdata \
    && cp /usr/share/zoneinfo/${TZ} /etc/localtime

WORKDIR /app

COPY app.py ./
COPY config.yml ./

RUN addgroup -g 1337 user && adduser -D -u 1337 -G user user

USER user

ENTRYPOINT ["./app.py", "--config", "config.yml"]
```
