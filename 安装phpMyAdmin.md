# 官网
https://www.phpmyadmin.net/downloads/

# 安装phpMyAdmin
```
# mkdir -p /usr/local/phpMyAdmin

# cd /usr/local/phpMyAdmin

# tar -zxvf phpMyAdmin-4.7.5-all-languages.tar.gz

# mv /usr/local/phpMyAdmin/phpMyAdmin-4.7.5-all-languages /usr/local/nginx/html/phpMyAdmin
```

# 修改配置
```
# cd /usr/local/nginx/html/phpMyAdmin

# cp config.sample.inc.php config.inc.php

# vim config.inc.php
```
```
把 $cfg['Servers'][$i]['host'] = 'localhost';
修改为 $cfg['Servers'][$i]['host'] = '127.0.0.1';
```

# 访问phpMyAdmin
http://localhost/phpMyAdmin/index.php

# loaclhost和127.0.0.1是不能等价的
```
loaclhost是一个name,这个name一般情况下代表了127.0.0.1这个IP地址，也就是说，当访问loaclhost的时候，计算机会通过DNS或者NBNS或者hosts这个文件将它翻译成127.0.0.1.

如果使用loaclhost去链接数据库，则Mysql会使用unixsocket去链接。如果指定为127.0.0.1，则会使用TCP/IP来链接。

所以，尽量使用127.0.0.1.
```
