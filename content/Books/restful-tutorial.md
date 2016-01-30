---
title: "RESTful Tutorial"
date: 2016-01-28 10:45
description: " --> RESTful API 设计指南"
---

[TOC]

## 定义

表征性状态传输（英文：Representational State Transfer，简称REST）是Roy Fielding博士于2000年在他的博士论文中提出来的一种软件架构风格。


### 应用于 Web 服务

符合 REST 设计风格的 Web API 称为 RESTful API。它从以下三个方面资源进行定义：

- 直观简短的资源地址：URI，比如：http://example.com/resources/。

- 传输的资源：Web服务接受与返回的互联网媒体类型，比如：JSON，XML，YAML等。

- 对资源的操作：Web服务在该资源上所支持的一系列请求方法（比如：POST，GET，PUT或DELETE）。

下表列出了在实现RESTful API时HTTP请求方法的典型用途。

| 资源 | GET | PUT | POST | DELETE |
| :-| :-| :-| :-| :-|
|一组资源的URI，比如http://example.com/resources/ |列出URI，以及该资源组中每个资源的详细信息（后者可选）。	| 使用给定的一组资源替换当前整组资源。| 在本组资源中创建/追加一个新的资源。该操作往往返回新资源的URL。| 删除整组资源。|
|单个资源的URI，比如http://example.com/resources/142	|获取指定的资源的详细信息，格式可以自选一个合适的网络媒体类型（比如：XML、JSON等）	|替换/创建指定的资源。并将其追加到相应的资源组中。	|把指定的资源当做一个资源组，并在其下创建/追加一个新的元素，使其隶属于当前资源。	|删除指定的元素。|

> PUT和DELETE方法是幂等方法。GET方法是安全方法（不会对服务器端有修改，因此当然也是幂等的）。

> 不像基于SOAP的Web服务，RESTful Web服务并没有“正式”的标准。这是因为REST是一种架构，而SOAP只是一个协议。虽然REST不是一个标准，但在实现RESTful Web服务时可以使用其他各种标准（比如HTTP，URL，XML，PNG等）。

## 要点

> REST 是一种设计风格而不是**标准**。通常使用现有协议规定的通用方法完成资源的操作。

- 资源由 URI 来指定。

- 资源操作包括增、删、改和查，分别对应 HTTP 提供的 POST、DELETE、PUT、GET 方法。

- 通过操作资源的表现形式来操作资源。

- 资源的表现形式，通常是 json 了吧，具体信息获取可以通过 curl 命令取得。

```
# curl https://api.github.com -I
# 协议 / 返回码
HTTP/1.1 200 OK
Server: GitHub.com
Date: Thu, 28 Jan 2016 03:37:37 GMT
# 资源表现形式 / 编码 == json / utf8
Content-Type: application/json; charset=utf-8
Content-Length: 2064
# 状态码
Status: 200 OK
# API 限制
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 58
X-RateLimit-Reset: 1453953247
Cache-Control: public, max-age=60, s-maxage=60
ETag: "d251d84fc3f78921c16c7f9c99d74eae"
Vary: Accept
# API 版本
X-GitHub-Media-Type: github.v3
Access-Control-Allow-Credentials: true
Access-Control-Expose-Headers: ETag, Link, X-GitHub-OTP, X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Reset, X-OAuth-Scopes, X-Accepted-OAuth-Scopes, X-Poll-Interval
Access-Control-Allow-Origin: *
Content-Security-Policy: default-src 'none'
Strict-Transport-Security: max-age=31536000; includeSubdomains; preload
X-Content-Type-Options: nosniff
X-Frame-Options: deny
X-XSS-Protection: 1; mode=block
Vary: Accept-Encoding
X-Served-By: e183f7c661b1bbc2c987b3c4dc7b04e0
X-GitHub-Request-Id: 01549811:1C1EB:A707303:56A98D00
```

### 1. 协议

API与用户的通信协议，总是使用HTTPs协议。

### 2. 域名

应将 API 部署在专用域名或事先规划好的主域名 URL 之下

- `https://api.helloworl.com`

- `https://helloworld.com/api/`

### 3. 版本

直观的做法是将 API 的版本号放入 URL 中，另一种做法是将版本号写入 HTTP 头信息中。

`https://api.helloworld.com/v1/`

### 4. 路径

在 RESTful 架构中，每个网址代表一种资源（resource），所以网址中**不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应**。一般来说，数据库中的表都是同种记录的"集合"（collection），所以**API中的名词也应该使用复数**。

举例来说，有一个API提供动物园（zoo）的信息，还包括各种动物和雇员的信息，则它的路径应该设计成下面这样。

```
https://api.example.com/v1/zoos
https://api.example.com/v1/animals
https://api.example.com/v1/employees
```

### 5. HTTP 动词

常用的 HTTP 动词有四个

```
GET（SELECT）：从服务器取出资源（一项或多项）。
POST（CREATE）：在服务器新建一个资源。
PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
DELETE（DELETE）：从服务器删除资源。
```

### 6. 过滤信息

如果记录数量很多，服务器不可能都将他们返回给用户。所以 API 应该提供参数，过滤返回结果。

以下是一些常见的参数。

```
?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置。
?page=2&per_page=100：指定第几页，以及每页的记录数。
?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?animal_type_id=1：指定筛选条件
```

参数的设计允许存在冗余，即允许 API 路径和 URL 参数偶尔有重复。比如，`GET /zoo/ID/animals` 与 `GET /animals?zoo_id=ID` 的含义是相同的。

### 7. 状态码

服务器向用户返回的状态码和提示信息，常见的有以下一些（方括号中是该状态码对应的HTTP动词）。

见参考链接

### 8. 错误处理

如果状态码是 4xx，就应该向用户返回出错信息。一般来说，返回的信息中将 error 作为键名，出错信息作为键值即可
```
{
    error: "Invalid API key!"
}
```

### 9. 返回结果

针对不同操作，服务器向用户返回的结果应该符合以下规范。

```
GET /collection：返回资源对象的列表（数组）
GET /collection/resource：返回单个资源对象
POST /collection：返回新生成的资源对象
PUT /collection/resource：返回完整的资源对象
PATCH /collection/resource：返回完整的资源对象
DELETE /collection/resource：返回一个空文档
```

### 10. Hypermedia API

RESTful API最好做到Hypermedia，即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么。
比如，当用户向api.example.com的根目录发出请求，会得到这样一个文档。

```
{"link": {
  "rel":   "collection https://www.example.com/zoos",
  "href":  "https://api.example.com/zoos",
  "title": "List of zoos",
  "type":  "application/vnd.yourformat+json"
}}
```

上面代码表示，文档中有一个link属性，用户读取这个属性就知道下一步该调用什么API了。rel表示这个API与当前网址的关系（collection关系，并给出该collection的网址），href表示API的路径，title表示API的标题，type表示返回类型。
Hypermedia API的设计被称为HATEOAS。Github的API就是这种设计，访问api.github.com会得到一个所有可用API的网址列表。

```
{
  "current_user_url": "https://api.github.com/user",
  "authorizations_url": "https://api.github.com/authorizations",
  // ...
}
```

从上面可以看到，如果想要获取用户的信息，就应该去访问api.github.com/user，然后就得到了下面结果。

```
{
  "message": "Requires authentication",
  "documentation_url": "https://developer.github.com/v3"
}
```

上面代码表示，服务器给出了提示信息，以及文档的网址。

### 11. 其他

1. 身份验证

2. 服务器返回格式

3. HTTPs协议加密

## 优点

- 可更高效利用缓存来提高响应速度

- 通讯本身的无状态性可以让不同的服务器的处理一系列请求中的不同请求，提高服务器的扩展性

- 浏览器即可作为客户端，简化软件需求

- 相对于其他叠加在HTTP协议之上的机制，REST的软件依赖性更小

- 不需要额外的资源发现机制

- 在软件技术演进中的长期的兼容性更好

## 参考

[HTTP 状态消息](http://www.w3school.com.cn/tags/html_ref_httpmessages.asp) 


[RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html) 

[Principles of good RESTful API Design](http://codeplanet.io/principles-good-restful-api-design/) 

[Learn REST: A RESTful Tutorial](http://www.restapitutorial.com/) 

[Some REST best practices](https://bourgeois.me/rest/) 

[https://zh.wikipedia.org/wiki/REST](https://zh.wikipedia.org/wiki/REST) 