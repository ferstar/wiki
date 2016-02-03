---
title: "Ubuntu"
date: 2016-01-06 10:16
description: " --> 使用过程中的一些问题记录"
---

[TOC]

## 服务自启动

`service --status-all`命令可以获取当前系统所有服务自动启动与否状态。`+`表示已设置为自启动。

- 设置某服务为自启动服务的命令：

`update-rc.d SERVICENAME defaults`

- 关闭某服务自启动的命令：

`update-rc.d -f SERVICENAME remove`

> 注：在 CentOS/RHEL 中，类似的命令行工具有`chkconfig`或`systemctl`；另外从 Ubuntu 15.04 开始，系统服务由 systemd 接管，对应的命令行工具是`systemctl`。

## 其它

### remove vs purge ###

`apt-get` 的 `remove` 和 `purge` 区别:

* `remove`: 只删除软件包, 保留配置文件
* `purge`: 删除软件包和配置文件

这里的配置文件是指包添加的系统配置, 不包括用户自定义的配置.

参考:

* man apt-get
* [What is the Difference Between `apt-get purge` and `apt-get remove`?](http://askubuntu.com/questions/231562/what-is-the-difference-between-apt-get-purge-and-apt-get-remove)
* [What is the correct way to completely remove an application?](http://askubuntu.com/questions/187888/what-is-the-correct-way-to-completely-remove-an-application)

## syntax error: unknown group 'ssl-cert' in statoverride file ##

安装某个包时遇到这个报错:

    $ apt-get install libffi-dev
    Reading package lists... Done
    Building dependency tree
    Reading state information... Done
    The following NEW packages will be installed:
      libffi-dev
    0 upgraded, 1 newly installed, 0 to remove and 18 not upgraded.
    Need to get 0 B/96.1 kB of archives.
    After this operation, 356 kB of additional disk space will be used.
    dpkg: unrecoverable fatal error, aborting:
     syntax error: unknown group 'ssl-cert' in statoverride file
    E: Sub-process /usr/bin/dpkg returned an error code (2)

其实提示还是很清楚了, statoverride文件有一个未知的用户组ssl-cert.

这个组其实是Apache建立的. 个人猜测应该是在卸载Apache时, 用的remove而不是purge导致.

文件在/var/lib/dpkg/statoverride:

    $ cat /var/lib/dpkg/statoverride
    root mlocate 2755 /usr/bin/mlocate
    root ssl-cert 710 /etc/ssl/private
    root Debian-exim 640 /etc/exim4/passwd.client
    root stapusr 4750 /usr/bin/staprun
    root crontab 2755 /usr/bin/crontab

这个的作用可以[`man 8 dpkg-statoverride`](http://manpages.ubuntu.com/manpages/trusty/man8/dpkg-statoverride.8.html):

> override ownership and mode of files

> `stat overrides' are a way to tell dpkg(1) to use a different owner or mode for a path when a package is installed
