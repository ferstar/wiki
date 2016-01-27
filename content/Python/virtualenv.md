---
title: "virtualenv"
date: 2013-08-22 23:48
description: " --> 虚拟安装环境，可解决依赖问题"
---

[TOC]

## 关于

virtualenv 创建一个拥有自己安装目录的环境, 这个环境不与其他虚拟环境共享库, 能够方便的管理 python 版本和管理 python 库

## 初阶

### 安装

`sudo pip3 install https://github.com/pypa/virtualenv/tarball/develop`

### 使用

```
➜  ~  mkdir flask
➜  ~  virtualenv flask 
Using base prefix '/usr'
New python executable in /home/ferstar/flask/bin/python3
Also creating executable in /home/ferstar/flask/bin/python
Installing setuptools, pip, wheel...done.
➜  ~  cd flask 
➜  flask  ls
bin  include  lib
➜  flask  source bin/activate
(flask) ➜  flask  deactivate
➜  flask
```

### 指定 python 版本

可以使用 -p PYTHONPATH 选项在创建虚拟环境的时候指定 python 版本。

`virtualenv -p /usr/local/bin/python3.4 ENV`

### 部署

某些特殊需求下,可能没有网络，我们可能想直接打包一个ENV, 可以解压后直接使用, 这时候可以使用 virtualenv -relocatable 指令将 ENV 修改为可更改位置的 ENV

```
(flask) ➜  flask  virtualenv --relocatable ./
Making script /home/ferstar/flask/bin/easy_install relative
Making script /home/ferstar/flask/bin/pip3 relative
Making script /home/ferstar/flask/bin/python-config relative
Making script /home/ferstar/flask/bin/easy_install-3.4 relative
Making script /home/ferstar/flask/bin/pip3.4 relative
Making script /home/ferstar/flask/bin/pip relative
Making script /home/ferstar/flask/bin/wheel relative

```

## Read More ##

* [Virtualenv和pip小探](http://mengzhuo.org/blog/virtualenv%E5%92%8Cpip%E5%B0%8F%E6%8E%A2.html)
* [virtualenv doc](https://virtualenv-chinese-docs.readthedocs.org/en/latest/)
* [Virtualenv Tutorial](http://simononsoftware.com/virtualenv-tutorial/)
* [Virtualenv Docs](https://virtualenv.pypa.io/en/latest/installation.html)
