# Domain Certificate Self-issuance Handbook

# SELF-ISSUANCE / 自签发

## PAN-DOMAIN NAME CERTIFICATE SELF-ISSUANCE

```
# install openssl
yum install -y openssl | apt install openssl

# CA
# generate self-issuance ca root cerificate private key
openssl genpkey -algorithm RSA -out cakey.pem -outform PEM -pkeyopt rsa_keygen_bits:2048 -aes-256-cbc
# generate ca root certificate application request
openssl req -new -key cakey.pem -out ca.csr
# generate ca root certificate and sign it
openssl x509 -req -days 3650 -in ca.csr -signkey cakey.pem -out ca.crt -extensions v3_ca

# CLIENT
# generate client ssl certificate private key
openssl genpkey -algorithm RSA -out clientkey.pem -outform PEM -pkeyopt rsa_keygen_bits:2048
# generate client ssl certificate application request
openssl req -new -key clientkey.pem -out client.csr
# prepare the client ssl certificate signing environment

touch /etc/pki/CA/index.txt
openssl rand -hex 16 > /etc/pki/CA/serial

cat >> client.ext <<EOF
subjectAltName = DNS:*.example.com, DNS:example.com
EOF

# generate client ssl certificate and sign it
openssl ca -days 365 -in client.csr -out client.crt -cert ca.crt -keyfile cakey.pem -extfile client.ext

# when using it, deploy clientkey.pem and client.crt to the server
# and then import ca.crt in the browser as the trusted root domain name certificate
```

## DOMAIN NAME CERTIFICATE SELF-ISSUANCE WITH DOCKER

```
docker run --rm -it -e CERT_DNS="domain.com;*.domain.com;*.a.domain.com" -v `pwd`/certs:/ssl soulteary/certs-maker
```

# APPLY FROM CA / 从 CA 申请

## USE [**ACME.SH](http://acme.sh/)** GENERATE PAN-DOMAIN CERTIFICATE

1. 如果是在一台未申请过证书的服务器，则应该从安装依赖开始进行；
2. 如果是已经申请过，但需要申请其他域名，则从申请域名开始进行即可；
3. 如果需要续期已经申请过的域名，则可以尝试申请续期。如果失败，则按照申请其他域名步骤。
4. 最后，执行安装证书的命令，指定 pem 和 key 的生成路径

```bash
# install git and socat
yum install -y git socat
# fetch acme.sh code
git clone https://gitee.com/neilpang/acme.sh.git
# install acme.sh
cd acme.sh; ./acme.sh --install -m root@haiwell.com
# reload .bashrc or other .shellrc
cd ~; source .bashrc

acme.sh --upgrade --auto-upgrade

# set ca
# acme.sh --set-default-ca --server letsencrypt
# acme.sh --set-default-ca --server zerossl

# apply for a pan-domain name certificate
acme.sh --issue --dns -d test1.tunnel.iotbus.net -d *.test1.tunnel.iotbus.net --yes-I-know-dns-manual-mode-enough-go-ahead-please

# renew after addings dns vertification
acme.sh --renew -d test1.tunnel.iotbus.net -d *.test1.tunnel.iotbus.net --yes-I-know-dns-manual-mode-enough-go-ahead-please

# install cert
acme.sh --installcert -d test1.tunnel.iotbus.net -d *.test1.tunnel.iotbus.net --fullchain-file /root/tunnel.pem --key-file /root/tunnel.key
```