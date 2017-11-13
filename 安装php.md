
```
# mkdir -p /usr/local/php

# cd /usr/local/php

# wget -P /usr/local/php https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

# wget -P /usr/local/php wget http://rpms.remirepo.net/enterprise/remi-release-7.rpm

# rpm -Uvh remi-release-7.rpm epel-release-latest-7.noarch.rpm

# yum -y install yum-utils

# yum-config-manager --enable remi-php71

# yum -y update

# yum -y install php71

# yum -y install php
```
