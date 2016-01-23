---
title: "效率工具"
date: 2013-08-17 07:32
updated: 2016-01-23 21:33:05
---


## 云笔记本 ##

### 为知笔记 ###

最初弃用印象笔记的原因主要是不支持`Markdown`语法而为知笔记支持，习惯后发现已经离不开他了。

官网链接：[http://www.wiz.cn/](http://www.wiz.cn/) 


## 思维导图 ##

### FreeMind ###

类似有`XMind`, `MindManager`等等.  
用过`XMind`, 在三台电脑(win7 64bit)上试了, 都有点问题. 虽然看起来很强大, 最终还是放弃了.  
FreeMind相对而言比较简洁和轻量级, 而且功能也都非常不错.  
有时候帮助自己规划一些事情, 非常合适.  


## 书签 ##

### Delicious ###

1~2年前一度打开非常慢, 难以容忍. 后来换了国内的`美味书签`. 结果有次发现我添加的一些书签都弄丢了. 彻底放弃!  

### Xmarks ###

这个是为了多浏览器之间同步书签用的.  
不过把我Chrome的书签搞的非常乱, 可能是我自己的原因. 不过我也只用Chrome, 不需要跨浏览器, 所以还是卸了.  


## 网盘 ##

### Sina微盘 ###
试过`Dropbox`和一些其他网盘, `微云`是我见过最差劲了, Dropbox还行, 命令行下也可以同步, 这点超赞! 可惜国内经常性的墙.  
最终找到了Sina微盘, 感觉非常不错. 现在我有10多G的空间了, 放一些书籍和资料足够了.  


## RSS ##

### Feedly ###
自从`Google Reader`宣布2013-07-01关闭, 于是找到了这个, 不过也偶尔需要自备梯子.  
<strike>整体还行吧, 对RSS阅读器要求不高</strike>  
现在感觉feedly做的还是不错的. 当然, 也许还有更多优秀的rss阅读器, 还没去尝试.


## 梯子 ##

note: 以下链接用base64转码

* [vpnso](aHR0cDovL3ZwbnNvLmNvbS8K) 用了两年, 可选列表很多, 买的 v?n 还带有chrome插件, 走socks代理.
* [鱼摆摆](aHR0cHM6Ly95YmIxMDI0LmNvbS8K) 只能mac下用, 走的socks代理.
* [红杏](aHR0cDovL2hvbnguaW4vaS9WVEpHLVlrV0doakMydURtCg==) 只能邀请注册, 所以url带尾巴. 只支持Chrome浏览器, 以插件形式使用. (未使用)
* [Shadowsocks](aHR0cHM6Ly9zaGFkb3dzb2Nrcy5jb20vCg==) 用的shadowsocks. (未使用)
* [熊猫翻滚](aHR0cHM6Ly93d3cucGFuZGFmYW4ub3JnLz9yPTIyNjAxCg==) 带尾巴. http(s)代理. (未使用)

## 眼睛福利 f.lux

这软件会自动在夜间将显示器色温调至偏黄色，似乎对眼睛有点好处。

主页地址：[https://justgetflux.com/](https://justgetflux.com/) 

`Ubuntu`安装方法：

```shell
sudo apt-get install python-glade2 python-appindicator
git clone https://github.com/Kilian/f.lux-indicator-applet.git
cd f.lux-indicator-applet
chmod +x setup.py
sudo ./setup.py install
sudo chmod +x /usr/local/bin/xflux
fluxgui
```
