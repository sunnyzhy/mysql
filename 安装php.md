# 安装php
```
# yum -y update

# yum -y install php

# php -v
PHP 7.1.11 (cli) (built: Oct 25 2017 10:24:28) ( NTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.1.0, Copyright (c) 1998-2017 Zend Technologies
```

# 安装php-fpm
        解析 PHP 脚本
```
# yum -y install php-fpm

# systemctl start php-fpm
```

# 安装php-mysql
```
# yum -y install php-mysql

# vim /etc/php.ini
mysqli.max_links = 1

# systemctl restart php-fpm
```

# 配置nginx的php项
```
# cd /usr/local/nginx

# vim conf/nginx.conf
        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        location ~ \.php$ {
            root           html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }

# ./sbin/nginx -s reload

# echo -e "<?php\nphpinfo();\n?>" > html/info.php
```

# 访问info.php
http://localhost/info.php
