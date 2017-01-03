---
title: 支持http、https兼容访问website的实现方法
tags:
  - apache
categories:
  - Web server
toc: true
date: 2014-12-14 16:58:19
description: 
  - https
  - apache
keywords:
  - https
  - apache
feature:
---

#### 一、CA服务器的搭建
``` shell
[root@VM_198_220_centos ~]# yum install openssl*
[root@VM_198_220_centos ~]# cd /etc/pki/
[root@VM_198_220_centos pki]# vim tls/openssl.cnf
dir             = /etc/pki/CA                 --第45行
countryName                 = optional          --第88行
stateOrProvinceName     = optional            --第89行
organizationName          = optional            --第90行
countryName_default             = CN            --第136行
stateOrProvinceName_default     = Shanghai          --第141行
localityName_default            = Shanghai          --第144行
注：邮箱、公司名称等根据实际情况进行填写。
[root@VM_198_220_centos pki]# cd CA 
[root@VM_198_220_centos CA]# mkdir certs newcerts crl     --创建3个目录和两个文件
[root@VM_198_220_centos CA]# touch index.txt serial
[root@VM_198_220_centos CA]# echo "01">serial         --根索引文件
[root@VM_198_220_centos CA]#openssl genrsa 1024 >private/cakey.pem --创建ca的私钥文件
[root@VM_198_220_centos CA]# chmod 600 private/cakey.pem  --改变私钥的权限
[root@VM_198_220_centos CA]#openssl req -new -key private/cakey.pem -days 3650 -x509 -out cacert.pem                      
```
<!-- more -->
#### 二、为www服务器颁发证书
``` shell
[root@VM_198_220_centos ~]# cd /etc/httpd/
[[root@VM_198_220_centos httpd]# mkdir certs
[root@VM_198_220_centos httpd]# cd certs/ 
[root@VM_198_220_centos certs]#openssl genrsa 1024 > httpd.key  --产生服务器的私钥
[root@VM_198_220_centos certs]# openssl req -new -key httpd.key -out httpd.csr --产生服务器的请求文件
[root@VM_198_220_centos certs]# openssl ca -in httpd.csr -out httpd.cert --产生服务器的证书文件
[root@VM_198_220_centos certs]#cp /etc/pki/CA/cacert.pem ./    --拷贝ca的证书文件
[root@VM_198_220_centos certs]#chmod 600 *
[root@VM_198_220_centos certs]#yum install mod_ssl*        --改变文件的权限增加安全性
[root@VM_198_220_centos certs]#vim /etc/httpd/conf.d/ssl.conf  --捆绑证书文件和钥匙文件
LoadModule ssl_module modules/mod_ssl.so
Listen 443
SSLPassPhraseDialog  builtin
SSLSessionCache         shmcb:/var/cache/mod_ssl/scache(512000)
SSLSessionCacheTimeout  300
SSLMutex default
SSLRandomSeed startup file:/dev/urandom  256
SSLRandomSeed connect builtin
SSLCryptoDevice builtin
nameVirtualHost *:443
<VirtualHost *:443>
ServerAdmin xxx@ogresugar.com
DocumentRoot /data/www/rx
ServerName rx.ogresugar.com:443
ErrorLog logs/rx_ssl_error_log
TransferLog logs/rx_ssl_access_log
LogLevel warn
SSLEngine on
SSLProtocol all -SSLv2
SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:RC4+RSA:+HIGH:+MEDIUM:+LOW
SSLCertificateFile /etc/httpd/certs/httpd.cert
SSLCertificateKeyFile /etc/httpd/certs/httpd.key
SSLCertificateChainFile /etc/httpd/certs/cacert.pem
<Files ~ "\.(cgi|shtml|phtml|php3?)$">
    SSLOptions +StdEnvVars
</Files>
<Directory "/var/www/cgi-bin">
    SSLOptions +StdEnvVars
</Directory>
SetEnvIf User-Agent ".*MSIE.*" \
         nokeepalive ssl-unclean-shutdown \
         downgrade-1.0 force-response-1.0
CustomLog logs/ssl_request_log \
          "%t %h %{SSL_PROTOCOL}x %{SSL_CIPHER}x \"%r\" %b"
</VirtualHost>
[root@VM_198_220_centos certs]# netstat -tupln |grep httpd 
tcp        0      0 :::80                       :::*                        LISTEN      5544/httpd          
tcp        0      0 :::443                      :::*                        LISTEN      5544/httpd
```

