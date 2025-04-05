## 1.1 generate the private key for your CA

```
openssl genrsa -aes256 -out ca-key.pem 4096
```

## 1.2 generate the CA certificate (root certificate)

```
openssl req -new -x509 -sha256 -days 3650 -key ca-key.pem -out ca-root.pem
```

### 1.2.1
View the root certificate.

```
openssl x509 -in ca-root.pem -text
```

# 2. create your domain/server certificate request
http://wiki.cacert.org/FAQ/subjectAltName

## 2.1 First create a key

```
openssl genrsa -out v-elk-01-zzeroo-lan-2048.key 4096
```

## 2.1 create the CSR

```
openssl req -new -sha256 -subj "/CN=v-elk-01" -key v-elk-01-zzeroo-lan-2048.key -out v-elk-01-zzeroo-lan-2048.csr
```

## 2.2 Create the certificate from the CSR
### 2.2.1 Create a config file for DNS and IP addresses

```
echo "subjectAltName=DNS.1:v-elk-01.zzeroo.lan,DNS.2:v-elk-01,DNS.3:elk.zzeroo.lan,DNS.4:elk,IP.1:192.168.10.13" >> v-elk-01-zzeroo-lan-2048.cnf
```

### 2.2.2 Create the certificate

```
openssl x509 -req -sha256 -days 365 -in v-elk-01-zzeroo-lan-2048.csr -CA ca-root.pem -CAkey ca-key.pem -out elk-01-zzeroo-lan-2048.pem -extfile v-elk-01-zzeroo-lan-2048.cnf -CAcreateserial
```

# 3. build a chain file

you might want to have an additional intermediate certificate in your organizations process

```
cat elk-01-zzeroo-lan-2048.pem > elk-01-zzeroo-lan-chain.pem
cat ca-root.pem >> elk-01-zzeroo-lan-chain.pem
```

You would import this file into your Service, if this was not just an example.



https://stackoverflow.com/questions/61552079/how-to-use-openssl-for-self-signed-certificates-with-custom-ca-and-proper-san-se

https://www.youtube.com/watch?v=VH4gXcvkmOY
