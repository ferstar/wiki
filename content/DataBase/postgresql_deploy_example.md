---
title: "PostgreSQL"
date: 2016-01-26 22:08
description: "--> 简单初始化配置记录"
---

[TOC]

## 关于

PostgreSQL 是自由的对象-关系型数据库服务器（数据库管理系统），在灵活的BSD-风格许可证下发行。它在其他开放源代码数据库系统（比如 MySQL 和 Firebird），和专有系统比如 Oracle、Sybase、IBM 的 DB2 和 Microsoft SQL Server 之外，为用户又提供了一种选择。

PostgreSQL 不寻常的名字导致一些读者停下来尝试拼读它，特别是那些把 SQL 拼读为"sequel"的人。PostgreSQL 开发者把它拼读为"post-gress-Q-L"。它也经常被简略念为"postgres"。

## Create Test DB

```shell
postgres=# create user test encrypted password 'test';
CREATE ROLE
postgres=# create database test owner test;
CREATE DATABASE
postgres=# GRANT ALL PRIVILEGES ON DATABASE test to test;
GRANT
postgres=# alter role test in database test set search_path=test;
ALTER ROLE
```

## Deploy DB

### install

```shell
sudo apt-get install postgresql postgresql-contrib postgresql-server-dev-9.*
```

### change default data directory

```
mkdir -p /home/postgres/9.*/data
chown -R postgres:postgres /home/postgres/9.*/data
chmod -R 0700 /home/postgres/9.*/data
su -i -u postgres
sudo /usr/lib/postgresql/9.*/bin/initdb -D /home/postgres/9.*/data -E UTF8
```

### authentication security

- config file path

`/etc/postgresql/9.*/main/postgresql.conf`

- FILE LOCATIONS & Connection Settings

```
data_directory = '/home/postgres/9.*/data'
hba_file = '/home/postgres/9.*/data/pg_hba.conf'
ident_file = '/home/postgres/9.*/data/pg_ident.conf'
listen_addresses = 'localhost'          # defaults to 'localhost'; use '*' to listen all
max_connections = 1000
ssl = false
password_encryption = on
```
