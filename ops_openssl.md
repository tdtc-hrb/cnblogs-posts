---
title: "install and usage with OpenSSL"
description: "update openssl"
date: 2024-12-29T08:38:08+08:00
---
- RHEL Community Version    
Alma Linux / Rocky Linux

# Prepare
Install development tool:
```
# dnf group install 'Development Tools'
```
Install dependencies:
```
# dnf install perl-core zlib-devel -y
```

## down latest OpenSSL
```bash
$mkdir openssl-1.1.1
$curl --progress https://www.openssl.org/source/openssl-1.1.1w.tar.gz | tar xz -C ./openssl-1.1.1 --strip-components 1
```

# setup
```bash
$cd openssl-1.1.1
```
- RHEL8
```bash
./config --prefix=/usr/local/ssl --openssldir=/usr/local/ssl shared zlib
```
- RHEL7
```bash
./config --prefix=/usr --shared
```

## make && install
```bash
$make
```
Root permission Required:
```bash
$sudo make install
```

## RHEL8
```
# mv /bin/openssl /bin/openssl.backup
# cp /usr/local/ssl/bin/openssl /bin/openssl
```

test:
```
# which openssl
# openssl version -a
```

# Usage
- Apache Httpd

## Generate Self-Signed Certificate
- Verifying and testing your self-signed certificate:
```
openssl x509 -in dev.crt -text -noout
```
- at non-interactive:
```
In the certificate, created with the single-line command, 
you see a section "X509v3 extensions:" with "X509v3 Basic Constraints: critical CA:TRUE". 

This is exactly the Apache warning message.
Instead, if you create the certificate with the three steps, 
the "X509v3 extensions:" section is not included into the certificate.
```
### interactive
CSR Information:
- Country Name: [CN](https://www.digicert.com/kb/ssl-certificate-country-codes.htm)
- State or Province Name: Neimenggu
- Locality Name: HM
- Organization Name: TDTC
- Organizational Unit Name: soft
- Common Name: localhost
- A challenge password:(Enter)
- An optional company name:(Enter)

subject:
```
subject=C = CN, ST = Neimenggu, L = HM, O = TDTC,
OU = soft, CN = localhost, emailAddress = veic_2005@163.com
```
#### Generating a Private Key
RSA Private Key:
```
openssl genrsa -out dev.key 4096
```
#### Creating a certificate signing request (CSR)
```
openssl req -new -sha256 -key dev.key -out dev.csr
```
#### Generating a self-signed certificate (CRT)
```
openssl x509 -req -days 1095 -in dev.csr -signkey dev.key -out dev.crt -outform PEM
```

### non-interactive
```
# 3 years expiration(365 * 3 + 3 = 1098)
openssl req -x509 -newkey rsa:4096 -keyout dev.key -out dev.crt -sha256 -days 1098 -nodes -subj "/CN=localhost" \ 
-addext "subjectAltName=DNS:localhost,DNS:*.localhost,IP:127.0.0.1"
```

# Ref
- [How To Install OpenSSL 1.1.1i in CentOS 8](https://www.hostnextra.com/kb/how-to-install-openssl-1-1-1i-in-centos-8/)
- [How to generate a self-signed SSL certificate using OpenSSL?](https://stackoverflow.com/questions/10175812/how-to-generate-a-self-signed-ssl-certificate-using-openssl)
- [Apache warns that my self-signed certificate is a CA certificate](https://stackoverflow.com/a/53601588)
