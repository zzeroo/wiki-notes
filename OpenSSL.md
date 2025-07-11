## 1.1 generate the private key for your CA

```bash
openssl genrsa -aes256 -out ca-key.pem 4096
```

## 1.2 generate the CA certificate (root certificate)

```bash
openssl req -new -x509 -sha256 -days 3650 -key ca-key.pem -out ca-root.pem
```

### 1.2.1 (optional)

View the root certificate.

```bash
openssl x509 -in ca-root.pem -text
```

# 2. create your domain/server certificate request

http://wiki.cacert.org/FAQ/subjectAltName


## 2.1 First create a key

```bash
openssl genrsa -out pfsense-zzeroo-lan.key 4096
```

## 2.1 create the CSR

```bash
openssl req -new -sha256 -subj "/CN=pfsense" -key pfsense-zzeroo-lan.key -out pfsense-zzeroo-lan.csr
```

## 2.2 Sign a certificate from CSR

### 2.2.1 Create a config file for DNS and IP addresses

```bash
echo "subjectAltName=DNS.1:pfsense.zzeroo.lan,DNS.2:pfsense,DNS.3:gateway01.zzeroo.lan,DNS.4:gateway01,IP.1:192.168.10.17" >> pfsense-zzeroo-lan.cnf
```

### 2.2.2 Create the certificate

```bash
openssl x509 -req -sha256 -days 365 -in pfsense-zzeroo-lan.csr -CA ca-root.pem -CAkey ca-key.pem -out pfsense-zzeroo-lan.pem -extfile pfsense-zzeroo-lan.cnf -CAcreateserial
```

# 3. build a chain file

you might want to have an additional intermediate certificate in your organizations process

```bash
cat pfsense-zzeroo-lan.pem > pfsense-zzeroo-lan-chain.pem
cat ca-root.pem >> pfsense-zzeroo-lan-chain.pem
```

You would import this file into your Service, if this was not just an example.

https://stackoverflow.com/questions/61552079/how-to-use-openssl-for-self-signed-certificates-with-custom-ca-and-proper-san-se

https://www.youtube.com/watch?v=VH4gXcvkmOY
