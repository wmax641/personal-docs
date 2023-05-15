# Certificates and OpenSSL Cheatsheet

## s_client and Connections
### Show Cert Chain
```
openssl s_client -showcerts -connect <HOST:PORT>
```
### STARTTLS
```
openssl s_client --starttls smtp -connect <HOST:PORT>
```

## Shortcut Cert/CSR commands
### Key and CSR
```
openssl req -new -newkey rsa:2048 -keyout server.key -out server.csr
```
### Key and Self Signed Cert
```
openssl req -x509 -newkey rsa:4096 -keyout server.key -out cert.crt -sha256 -days 365
```

## Cert/CSR commands
### RSA Private Key
```
openssl genrsa -out server.key 4096
```
### EC Private Key
```
# List supported curves
openssl ecparam -list_curves

#  generate key
openssl ecparam -name prime256v1 -genkey -out server.key
```

### Generate CSR
```
openssl req -new -key server.key -out server.csr
```

### Generate SAN CSR
First create an openssl config file; __openssl.cnf__:
``` ini
[ req ]
distinguished_name  = dn
req_extensions      = req_ext
prompt              = no
default_md          = sha256
  
[ dn ]
C     = AU
ST    = New South Wales
L     = Sydney
O     = Organisation
CN    = wmax641.website
  
[ req_ext ]
subjectAltName = @alt_names
  
[ alt_names ]
DNS.1 = wmax641.website
DNS.2 = *.wmax641.website
```
Then generate CSR using the config file
```
openssl req -new -key server.key -out server.csr -config openssl.cnf

```

## Let's Encrypt
### Sign CSR
Install `certbot` package, prepare a CSR, then run the `certbot` utility
```
certbot certonly --csr server.csr --manual --preferred-challenge dns
```

