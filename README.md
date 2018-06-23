# SSL client certificate example with Nginx 

Follow these steps to get this example up and running

## Create certificate

```
#switch to the ssl directory
cd ssl

#set environment variables
export CLIENT_CN=app123
export DOMAIN=andrejmaya.com
export ORGANIZATION=Andrej Maya
export ORGANIZATION_UNIT=Dev
export STATE=Hamburg
export LOCALITY=Hamburg

#create private key and root cert of CA 
openssl req -new -x509 -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 365 -out rootCA.pem -subj "/C=DE/ST=$STATE/L=$LOCALITY/O=$ORGANIZATION/OU=$ORGANIZATION_UNIT/CN=$DOMAIN" 

#server key, certificate and sign request
openssl req -new -newkey rsa:4096 -nodes -keyout server.key -out server.csr -subj "/C=DE/ST=$STATE/L=$LOCALITY/O=$ORGANIZATION/OU=$ORGANIZATION_UNIT/CN=$DOMAIN"

# sign the request
openssl x509 -req -in server.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out server.crt -days 365 -sha256


# client side
openssl req -new -newkey rsa:4096 -nodes -keyout client.key -out client.csr -subj "/C=DE/ST=$STATE/L=$LOCALITY/O=$ORGANIZATION/OU=$ORGANIZATION_UNIT/CN=$DOMAIN"

#generate client certificate
openssl x509 -req -in client.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out client.crt -days 365 -sha256

# create a p12 which you can import for example into the keychain on OSX
openssl pkcs12 -export -in server.crt -inkey server.key -out client-cert.p12
```

## Start Nginx

`docker-compose up -d`