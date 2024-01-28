# openssl

## root

```bash
# create root directory
mkdir certs crl csr newcerts private
touch index.txt
echo 1000 > serial

# create root key(keep in absolute private, used to issue trusted cert)
openssl genrsa -aes256 -out private/ca.key.pem 4096

touch openssl.cnf

# create root cert
openssl req -config openssl.cnf -key private/ca.key.pem -new -x509 -days 7300 -sha256 -extensions v3_ca -out certs/ca.cert.pem -subj "/C=<country>/ST=<city>/L=<locality>/O=<organization>/OU=<organization_unit>/CN=<common_name>"

# verify root cert
openssl x509 -noout -text -in certs/ca.cert.pem
```

## intermediate

```bash
# create intermediate directory
mkdir intermediate
cd intermediate
mkdir certs crl csr newcerts private
touch index.txt
echo 1000 > serial
echo 1000 > crlnumber
cd ..

# create intermediate key
openssl genrsa -aes256 -out intermediate/private/intermediate.key.pem 4096

# create intermediate certificate signing request(CSR)
# pass phrase: secretpassword
openssl req -config intermediate/openssl.cnf -new -sha256 -key intermediate/private/intermediate.key.pem -out intermediate/csr/intermediate.csr.pem -subj "/C=<country>/ST=<city>/L=<locality>/O=<organization>/OU=<organization_unit>/CN=<intermediate_common_name>"

# create an intermediate certificate signed by root
# ca.key.pem pass phrase: secretpassword
openssl ca -config openssl.cnf -extensions v3_intermediate_ca -days 7300 -notext -md sha256 -in intermediate/csr/intermediate.csr.pem -out intermediate/certs/intermediate.cert.pem

# verify the intermediate certificate
openssl x509 -noout -text -in intermediate/certs/intermediate.cert.pem

# verify the intermediate certificate against the root certificate
openssl verify -CAfile certs/ca.cert.pem intermediate/certs/intermediate.cert.pem
# intermediate.cert.pem: OK
```

## client & server certificate

```bash
# create client certificate
openssl genrsa -out intermediate/private/client.key.pem 2048

# create client csr
openssl req -config intermediate/openssl.cnf -key intermediate/private/client.key.pem -new -sha256 -out intermediate/csr/client.csr.pem -subj "/C=<country>/ST=<city>/L=<locality>/O=<organization>/OU=<organization_unit>/CN=<client_name>"

# create client server certificate
openssl ca -config intermediate/openssl.cnf -extensions server_cert -days 375 -notext -md sha256 -in intermediate/csr/client.csr.pem -out intermediate/certs/client.cert.pem

# verify client certificate
openssl x509 -noout -text -in intermediate/certs/client.cert.pem
```

## client & server key

```bash
# Generate CA key:
openssl genrsa -des3 -out ca.key 4096

# Generate CA certificate:
openssl req -new -x509 -days 365 -key ca.key -out ca.crt -subj "/C=<country>/ST=<city>/L=<locality>/O=<organization>/OU=<organization_unit>/CN=<server_name>"

# Generate server key:
openssl genrsa -des3 -out server.key 4096

# Generate server signing request:
openssl req -new -key server.key -out server.csr -subj "/C=<country>/ST=<city>/L=<locality>/O=<organization>/OU=<organization_unit>/CN=<server_name>"

# Self-sign server certificate:
openssl x509 -req -days 365 -in server.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out server.crt

# Remove passphrase from the server key:
openssl rsa -in server.key -out server.key

# Generate client key:
openssl genrsa -des3 -out client.key 4096

# Generate client signing request:
openssl req -new -key client.key -out client.csr -subj "/C=<country>/ST=<city>/L=<locality>/O=<organization>/OU=<organization_unit>/CN=<client_name>"

# Self-sign client certificate:
openssl x509 -req -days 365 -in client.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out client.crt

# Remove passphrase from the client key:
openssl rsa -in client.key -out client.key
```
