# Certificates and OpenSSL Cheatsheet

## s_client and Connections
### 
Show Cert Chain
```
openssl s_client -showcerts -connect <HOST:PORT>
```
<br>
STARTTLS
```
openssl s_client --starttls smtp -connect <HOST:PORT>
```
<br>

## Generate CSR and Certs
Generate CSR and Private Key
```
openssl req -new -newkey rsa:2048 -keyout server.key -out server.csr
```
<br>
Generate Private Key and Cert
```
openssl req -x509 -newkey rsa:4096 -keyout server.key -out cert.crt -sha256 -days 365
```
<br>
