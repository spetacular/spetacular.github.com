---
layout: post
title: 教程：配置 Let's Encrypt 免费HTTPS证书
category : 服务器
tagline: "Supporting tagline"
tags : [服务器,证书,HTTPS]
---

## 目录定义
1. 存放验证域名的文件，即acme-dir，默认存储在/home/xxx/www/challenges/
2. 存放最终结果证书的目录，如本文中的/data/ssl

## 存放验证域名文件的目录

```
     mkdir ~/www/challenges/
```    

然后配置一个 HTTP 服务，以 Nginx 为例：

```
    NGINX
    server {
        server_name www.yoursite.com yoursite.com;

        location ^~ /.well-known/acme-challenge/ {
            alias /home/xxx/www/challenges/;
            try_files $uri =404;
        }

        location / {
            rewrite ^/(.*)$ https://yoursite.com/$1 permanent;
        }
    }
``` 
 
然后重启nginx。
```  
 sudo service nginx reload
```
 
## 创建存放证书的目录

```
    mkdir /data/ssl
    cd /data/ssl
```

## 创建一个 RSA 私钥用于 Let's Encrypt 识别你的身份

```    
    openssl genrsa 4096 > account.key
```

## 创建 RSA 私钥（兼容性好）

```
    openssl genrsa 4096 > domain.key
```

## Common Name 必须为你的域名，其它的随便填

```
    openssl req -new -sha256 -key domain.key -out domain.csr
```

## 获取脚本

```
    wget https://raw.githubusercontent.com/diafygi/acme-tiny/master/acme_tiny.py
```

## 指定账户私钥、CSR 以及验证目录，执行脚本：

```
    python acme_tiny.py --account-key ./account.key --csr ./domain.csr --acme-dir /home/xxx/www/challenges/ > ./signed.crt
```

如果一切正常，当前目录下就会生成一个 signed.crt，这就是申请好的证书文件。
## 证书融合

```
    wget -O - https://letsencrypt.org/certs/lets-encrypt-x3-cross-signed.pem > intermediate.pem
cat signed.crt intermediate.pem > chained.pem
    wget -O - https://letsencrypt.org/certs/isrgrootx1.pem > root.pem
cat intermediate.pem root.pem > full_chained.pem
```

## ssl配置

```
    server {
        listen 443;
        server_name www.text.wiki;
        root   /data/htdocs/textwiki;
        autoindex on;
        index index.php index.html;
        ssl on;
        ssl_certificate /data/ssl/chained.pem;
        ssl_certificate_key /data/ssl/domain.key;
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;
        ssl_trusted_certificate    /data/ssl/full_chained.pem;

        location / {
                    try_files $uri $uri/ /index.php;

                    location ~ \.php$ {
                        fastcgi_pass unix:/var/run/php5-fpm.sock;
                        fastcgi_param  SCRIPT_FILENAME /data/htdocs/textwiki$fastcgi_script_name;
                        include        fastcgi_params;
                    }
                }
        }
```

## 自动更新脚本
1.新建文件`vi renew_cert.sh`

2.填写如下内容

```
    #!/bin/bash
    cd /data/ssl/
    python acme_tiny.py --account-key account.key --csr domain.csr --acme-dir /home/xxx/www/challenges/ > signed.crt || exit
    wget -O - https://letsencrypt.org/certs/lets-encrypt-x3-cross-signed.pem > intermediate.pem
    cat signed.crt intermediate.pem > chained.pem
    wget -O - https://letsencrypt.org/certs/isrgrootx1.pem > root.pem
cat intermediate.pem root.pem > full_chained.pem
    service nginx reload
```
3.`chmod +x renew_cert.sh`

## 加入crontab

crontab 中加入自动更新脚本，每月自动更新，crontab -e 加入以下内容：

```
    0 0 1 * * /data/ssl/renew_cert.sh >/dev/null 2>&1
```

## 最关键的是，如果服务器有防火墙，请放开服务器的443入口

## 说明
本文注重实战，跟着教程一步一步配置即可，想了解原理的同学，可以参考如下网址：
[Let's Encrypt官网](https://letsencrypt.org/)
[Let's Encrypt，免费好用的 HTTPS 证书](https://imququ.com/post/letsencrypt-certificate.html)
[Let's Encrypt 给网站加 HTTPS 完全指南](https://ksmx.me/letsencrypt-ssl-https/)