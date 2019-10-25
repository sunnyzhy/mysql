# 下载地址
官网： https://galeracluster.com/downloads/

![](images/galera-cluster-1.png)

必须下载的项：
- galera-3-25.3.28-1.el7.x86_64.rpm
- mysql-wsrep-5.7-5.7.27-25.19.el7.x86_64.rpm
- mysql-wsrep-client-5.7-5.7.27-25.19.el7.x86_64.rpm
- mysql-wsrep-common-5.7-5.7.27-25.19.el7.x86_64.rpm
- mysql-wsrep-libs-5.7-5.7.27-25.19.el7.x86_64.rpm
- mysql-wsrep-libs-compat-5.7-5.7.27-25.19.el7.x86_64.rpm
- mysql-wsrep-server-5.7-5.7.27-25.19.el7.x86_64.rpm

# 安装mysql
[安装mysql](https://github.com/sunnyzhy/mysql/blob/master/%E5%AE%89%E8%A3%85mysql5.7.md)

# 安装依赖包socat、boost、rsync、lsof
```
# yum -y install socat boost rsync lsof
```

# 依次执行以下命令
```
# rpm -ivh mysql-wsrep-common-5.7-5.7.27-25.19.el7.x86_64.rpm

# rpm -ivh mysql-wsrep-libs-5.7-5.7.27-25.19.el7.x86_64.rpm

# rpm -ivh mysql-wsrep-libs-compat-5.7-5.7.27-25.19.el7.x86_64.rpm

# rpm -ivh mysql-wsrep-client-5.7-5.7.27-25.19.el7.x86_64.rpm

# rpm -ivh mysql-wsrep-server-5.7-5.7.27-25.19.el7.x86_64.rpm

# rpm -ivh mysql-wsrep-5.7-5.7.27-25.19.el7.x86_64.rpm

# rpm -ivh galera-3-25.3.28-1.el7.x86_64.rpm
```

如果安装包有冲突，就先卸载冲突的安装包，例如：
```
# rpm -ivh galera-3-25.3.28-1.el7.x86_64.rpm
warning: galera-3-25.3.28-1.el7.x86_64.rpm: Header V4 RSA/SHA512 Signature, key ID bc19ddba: NOKEY
Preparing...                         ################################# [100%])
	file /usr/bin/garbd from install of galera-3-25.3.28-1.el7.x86_64 conflicts with file from package galera-25.3.24-1.rhel7.el7.centos.x86_64
	
# yum -y remove galera-25.3.24-1.rhel7.el7.centos.x86_64

# rpm -ivh galera-3-25.3.28-1.el7.x86_64.rpm
```

# 查看rpm包的安装路径
```
#  rpm -qpl galera-3-25.3.28-1.el7.x86_64.rpm
warning: galera-3-25.3.28-1.el7.x86_64.rpm: Header V4 RSA/SHA512 Signature, key ID bc19ddba: NOKEY
/etc/sysconfig/garb
/usr/bin/garb-systemd
/usr/bin/garbd
/usr/lib/systemd/system/garb.service
/usr/lib64/galera-3
/usr/lib64/galera-3/libgalera_smm.so
/usr/share/doc/galera-3
/usr/share/doc/galera-3/COPYING
/usr/share/doc/galera-3/LICENSE.asio
/usr/share/doc/galera-3/LICENSE.chromium
/usr/share/doc/galera-3/LICENSE.crc32c
/usr/share/doc/galera-3/README
/usr/share/doc/galera-3/README-MySQL
/usr/share/man/man8/garbd.8.gz
```

# 编辑my.cnf
```
# vim /etc/my.cnf
[mysqld]

[galera]
wsrep_provider= /usr/lib64/galera/libgalera_smm.so #查看rpm包的安装路径
wsrep_cluster_address="gcomm://192.168.197.107,192.168.197.108,192.168.197.109" #集群中所有的节点地址
wsrep_cluster_name = 'mycluster' #集群名称，所有节点的集群名称都相同
wsrep_node_name = 'node1' #节点名称，每个节点名称唯一
wsrep_node-address = '192.168.197.107' #本节点地址
binlog_format=row
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0
```

# 启动集群
## 启动第一个集群节点
```
# /usr/bin/mysqld_bootstrap
```

或者

```
# mysqld_bootstrap
```

