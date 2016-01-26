---
title: "MySQL"
date: 2016-01-26 22:24
description: "--> WordPress 数据库迁移"
---

[TOC]

## About

有两个小站的数据库是用阿里云的数据库，因为折扣期过去后觉得略贵，所以就有了想把数据库迁出去的想法，至于备份也是用很简单的`mysqldump`工具，数据很少，没必要出动主从热备什么高大上的方案。这里记录下过程。

## 数据库迁移

```
# 备份远程数据库数据
mysqldump -u username -p -h sql_server_host database_name > database_name.sql
# 新建数据库
create database database_name;
show databases;
# 添加用户/密码
create user 'username' IDENTIFIED BY 'password';
# 设定数据库访问权限
grant all on database_name.* to username IDENTIFIED BY 'password';
flush privileges;
# 还原数据库备份
mysql -uusername -ppassword database_name < database_name.sql
或
gzip < database_name.sql.gz | mysql -u root -p dataname
或：
zcat database_name.sql.gz  | mysql -u root -p dataname
```

### WordPress配置部分"wp-config.php"

```
/** MySQL主机 */
define('DB_HOST', 'localhost');
```

## 更改默认监听地址

> [MySQL/MariaDB Server: Bind To Multiple IP Address](http://www.cyberciti.biz/faq/unix-linux-mysqld-server-bind-to-more-than-one-ip-address/) 

默认配置只允许`localhost`访问, 远程备份不方便, 所以需要更改下, 上面的文章说的很清楚, 关键的地方就在这个配置文件里`vi /etc/mysql/my.cnf`找到并如下更改:

```
bind-address            = 0.0.0.0
```

改完后重启下`mysql`
`service mysql restart`

检查效果`netstat -anp | grep 3306`如下

```
tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN      5173/mysqld
```

这样就对外完全放开了3306的访问权限, 不安全, 可以通过`iptables`或者`ufw`之类防火墙限制下, 或者`mysql`本身就有限制公网ip账户登录的设定

可以新建一个只具有`select,lock`权限的数据库用户, 比如`backup`, 然后在数据库开限制此用户的登录ip

```
create user 'backup' identified by  'back_up';
grant select,lock tables on database_name.* to backup@'备份服务器ip地址' identified by 'back_up';
flush privileges;
```

检查下数据库内容`select host, user from mysql.user;`

```
+----------------+------------------+
| host           | user             |
+----------------+------------------+
| 114.114.114.114| backup           |
| 127.0.0.1      | root             |
| ::1            | root             |
| iz25mfbe902z   | root             |
| localhost      | debian-sys-maint |
| localhost      | root             |
+----------------+------------------+
9 rows in set (0.00 sec)
```

### 数据库自动备份脚本

```shell
#!/bin/sh
# Name:bakmysql.sh
# This is a ShellScript For Auto DB Backup and Delete old Backup
#
backupdir=/home/sqlbak
mysql_bin_dir=/usr/bin
time=`date +%Y%m%d`
dbname1=
dbname2=
username=
passwd=
host=

$mysql_bin_dir/mysqldump -u $username -p$passwd -h $host $dbname1 | gzip > $backupdir/bak-$dbname1-$time.sql.gz
$mysql_bin_dir/mysqldump -u $username -p$passwd -h $host $dbname2 | gzip > $backupdir/bak-$dbname2-$time.sql.gz

find $backupdir -name "*.gz" -type f -mtime +7 -exec rm {} \; > /dev/null 2>&1
```
[https://gist.github.com/ferstar/387b20c9468979ddd5f6](https://gist.github.com/ferstar/387b20c9468979ddd5f6)