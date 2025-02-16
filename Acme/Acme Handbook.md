# Acme.sh Cheatsheet

## Install

```bash
# install git and socat
yum install -y git socat

# fetch acme.sh code
git clone <https://gitee.com/neilpang/acme.sh.git>

# install acme.sh
cd acme.sh; ./acme.sh --install -m root@root.com

# reload .bashrc or other .shellrc
cd ~; source .bashrc

```

## Usage

```bash
# switch default ca
# ca: letsencrypt | zerossl
acme.sh --set-default-ca --server letsencrypt

# enable auto upgrade acme.sh
acme.sh  --upgrade --auto-upgrade

# disable auto upgrade acme.sh
acme.sh  --upgrade --auto-upgrade 0

# list domain
acme.sh --list

# just issue a certificate, can not issue wildcard certificates
# path_to_dir is the root folder where you host your website files
# you must have write access to this folder
acme.sh --issue -d {{DOMAIN}} -d {{DMAIN}} -w {{PATH_TO_DIR}}

# stop certificate renewal
acme.sh --remove -d {{DOMAIN}}

# install the certificate to apache
acme.sh --install-cert -d example.com \\
--cert-file      /path/to/certfile/in/apache/cert.pem  \\
--key-file       /path/to/keyfile/in/apache/key.pem  \\
--fullchain-file /path/to/fullchain/certfile/apache/fullchain.pem \\
--reloadcmd     "service apache2 force-reload"

# install the certificate to nginx
acme.sh --install-cert -d example.com \\
--key-file       /path/to/keyfile/in/nginx/key.pem  \\
--fullchain-file /path/to/fullchain/nginx/cert.pem \\
--reloadcmd     "service nginx force-reload"

# issue certificate using nginx mode
acme.sh --issue --nginx -d {{DOMAIN}} -d {{DOMAIN}}

# issue certificate using manual dns mode, can issue wildcard certificates
acme.sh --issue --dns -d {{DOMAIN}} -d {{DOMAIN}} --yes-I-know-dns-manual-mode-enough-go-ahead-please

# issue certificate using dns api mode, can issue wildcard certificates
# dns_api: dns_ali | gnd_gd | dns_cf
# more dns providers see: <https://github.com/acmesh-official/acme.sh/wiki/dnsapi>
acme.sh --issue --dns {{DNS_API}} -d {{DOMAIN}} -d {{DOMAIN}}

# use standalone server to issue certificate, can not issue wildcard certificates
# port 80 must be free to listen on
acme.sh --issue --standalone -d {{DOMAIN}} -d {{DOMAIN}}

# use standalone ssl server to issue certificate, can not issue wildcard certificates
# port 443 must be free to listen on
acme.sh --issue --alpn -d {{DOMAIN}} -d {{DOMAIN}}

# issue specified algorithm certificate
# algorithm: ec-256 | ec-384 | ec-521 | 2048 | 3072 | 4096
acme.sh --issue -d {{DOMAIN}} --keylength {{ALGORITHM}}

# renew certificate using dns mode
acme.sh --renew --dns {{DNS_API}} -d {{DOMAIN}}
```