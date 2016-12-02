---
title: 'composer update报ssl错误的解决办法 '
tags:
  - composer
categories:
  - Web development
description: 
toc: true
date: 2015-10-08 16:51:06
feature:
---

#### 检查证书文件的默认位置
```
$ php -r "print_r(openssl_get_cert_locations());"
Array
(
    [default_cert_file] => /opt/lampp/share/openssl/cert.pem
    [default_cert_file_env] => SSL_CERT_FILE
    [default_cert_dir] => /opt/lampp/share/openssl/certs
    [default_cert_dir_env] => SSL_CERT_DIR
    [default_private_dir] => /opt/lampp/share/openssl/private
    [default_default_cert_area] => /opt/lampp/share/openssl
    [ini_cafile] => 
    [ini_capath] => 
)
```
<!-- more  -->
#### 下载证书
```
$ wget http://curl.haxx.se/ca/cacert.pem
```

#### 将证书复制到默认位置
```
$ sudo mv cacert.pem /opt/lampp/share/openssl/cert.pem
```

参考：http://stackoverflow.com/questions/27206719/composer-update-fails-while-updating-from-packagist
