# Dockerfile Templates

## Generic Dockerfile
```docker
FROM alpine:latest

LABEL tag_key="tag_value"

ENV SOME_ENV_VAR=SOME_VALUE

ARG TZ=Australia/Sydney

RUN apk update --no-cache --purge \
    && apk upgrade --no-cache \
    && apk add --no-cache --update python3 tzdata \
    && cp /usr/share/zoneinfo/${TZ} /etc/localtime

# Install Python3 pip and modules
RUN wget https://bootstrap.pypa.io/get-pip.py \
    && python3 get-pip.py \
    && pip3 install --upgrade \
        boto3 \
        requests \
    && python3 -m pip uninstall -y pip setuptools \
    && rm get-pip.py \
    && rm -rf /usr/lib/python*/ensurepip

# Install Application
WORKDIR /app
COPY app.py ./
COPY config.yml ./

# Drop to unprivileged user
RUN addgroup -g 1337 user && adduser -D -u 1337 -G user user
USER user

ENTRYPOINT ["./app.py", "--config", "config.yml"]
```
