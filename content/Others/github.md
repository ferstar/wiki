---
title: "Github"
date: 2016-01-24 16:56
description: "-->全球最大的同性交友网站"
---

[TOC]

## git status --porcelain

[L163](https://github.com/tankywoo/simiki/blob/master/simiki/conf_templates/fabfile.py#L163)这里会漏掉两种情况：
```
# 1. 有文件被删除 git status --porcelain命令结果前两位字符应该是
空格+D
# 2. 新添加的文件没有被追踪 git status --porcelain 输出前两位字符是
??
# 3. 有文件被修改
空格+M
```
所以需要稍微修改下正则，匹配到前两种情况
`res = local('git status --porcelain 2>/dev/null | egrep \'^ [DM]|^\?\?\' | wc -l', capture=True)`

然后检测到有改动执行`git add -A`后
[L169](https://github.com/tankywoo/simiki/blob/master/simiki/conf_templates/fabfile.py#L169)这里会漏掉三种情况
```
# 1. 文件被重命名
R+空格
# 2. 文件被删除
D+空格
# 3. 有新文件
A+空格
# 4. 文件被修改
M+空格
```
所以需要修改下正则，匹配到前三种情况
`res = local('git status --porcelain 2>/dev/null | egrep \'^[ADMR]\' | wc -l', capture=True)`

## PR流程

> 私以为`Gihub`的精髓就是这个`Pull Request`，可以让大家一起愉快的搞基。一般`PR`流程如下：

1. 查看项目主页有无关于`PR`的约束要求，有则按照约束做，无则下一步。

2. `fork`一份源码到自己的`Github`仓库。

3. 在`fork`到自己的仓库项目新建一个`branch`，这个分支的名字虽然可以由你指定，但约定俗成认为最好是以要修复的`bug`或增加的`function`命名，比如你修复了一个安卓内核无限重启的`bug`，那么新分支的名字就可以取`fix-boot-loops`。

4. 以上操作全部可以在`Github`网站上进行，完成后你可以`clone`新分支到本地修改。

5. 修改完成后先`push`到自己先前新建的分支上去。

6. 在`Github`你`fork`的项目页面某处有一个`Pull Request`的按钮，接下来就是写好`commit`和`description`坐等远方的基友接受你的`PR`。

7. `PR`被接受并`merge`后，删掉当前分支或者连项目一起删掉，下次有情况再从步骤一开始循环。

> 参考：

> [Fork A Repo](https://help.github.com/articles/fork-a-repo/)

> [Contributing to Processing with Pull Requests](https://github.com/processing/processing/wiki/Contributing-to-Processing-with-Pull-Requests)