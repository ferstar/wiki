---
title: "MongoDB"
date: 2016-01-25 21:01
description: "-->一个基于分布式文件存储的数据库"
---

[TOC]

## 介绍

MongoDB 是一个基于分布式文件存储的数据库。由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。
MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。

## 初阶

### 安装

Ubuntu默认仓库里的有点问题，还是老老实实按照官网的步骤来比较好

[https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/](https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/) 

官网连接比较慢，最好挂梯子安装比较节省时间

`export http_proxy=http://xxx:port`

可能官网自己也对版本稳定性没有信心，提供了冻结 MongoDB 自动更新的方法

```
echo "mongodb-org hold" | sudo dpkg --set-selections
echo "mongodb-org-server hold" | sudo dpkg --set-selections
echo "mongodb-org-shell hold" | sudo dpkg --set-selections
echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
echo "mongodb-org-tools hold" | sudo dpkg --set-selections
```

然后启动`sudo service mongod start`报错：
```
Failed to start mongod.service: Unit mongod.service failed to load: No such file or directory.
```
这货根本旧没有建立 mongod 这个 service，所以只能自己建一个
[install-mongodb.sh](https://gist.github.com/ferstar/ecef75c46834019627af) 

### 导入数据

https://docs.mongodb.org/getting-started/python/import-data/

`mongoimport --db test --collection restaurants --drop --file primer-dataset.json
`

输出如下

```
2016-01-25T22:17:36.399+0800	connected to: localhost
2016-01-25T22:17:36.400+0800	dropping: test.restaurants
2016-01-25T22:17:37.258+0800	imported 25359 documents
```

导入 25k 的数据居然不到一秒，果然效率

### 安装 pymongo

`suod pip3 install pymongo`

### 建立连接

```
>>> from pymongo import MongoClient
>>> client = MongoClient()
```

不带参数默认连接`localhost`的`27017`端口，带参数

```
>>> client = MongoClient("mongodb://localhost:27017")
```

### 访问数据库对象

两种不同的方式

```
db = client.primer
db = client['primer']
```

结果都是一样的

```
Database(MongoClient(host=['localhost:27017'], document_class=dict, tz_aware=False, connect=True), 'primer')
```

### 访问 Collection Objects

依然有两种方式

```
db.dataset
db['dataset']
```

结果一样

```
Collection(Database(MongoClient(host=['localhost:27017'], document_class=dict, tz_aware=False, connect=True), 'primer'), 'dataset')
```

You may also assign the collection object to a variable for use elsewhere, as in the following examples:

```
coll = db.dataset
coll = db['dataset']
```

### 增

```
from datetime import datetime
from pymongo import MongoClient


client = MongoClient()
db = client.test

result = db.restaurants.insert_one(
    {
        "address": {
            "street": "2 Avenue",
            "zipcode": "10075",
            "building": "1480",
            "coord": [-73.9557413, 40.7720266]
        },
        "borough": "Manhattan",
        "cuisine": "Italian",
        "grades": [
            {
                "date": datetime.strptime("2014-10-01", "%Y-%m-%d"),
                "grade": "A",
                "score": 11
            },
            {
                "date": datetime.strptime("2014-01-16", "%Y-%m-%d"),
                "grade": "B",
                "score": 17
            }
        ],
        "name": "Vella",
        "restaurant_id": "41704620"
    }
)
```

如果 collection 不存在则会新建，插入成功后返回一个 InsertOneResult 的对象，包括一个叫 inserted_id 的属性，可以查看这个属性的值

`result.inserted_id`

这个值可能会略有不同

`ObjectId("56a632357fe9e361aba6102d")`

### 查

```python
from pymongo import MongoClient

client = MongoClient()
db = client.test

cursor = db.restaurants.find()

for document in cursor:
    print(document)

```

结果

```
{'name': 'Vella', 'borough': 'Manhattan', 'grades': [{'date': datetime.datetime(2014, 10, 1, 0, 0), 'grade': 'A', 'score': 11}, {'date': datetime.datetime(2014, 1, 16, 0, 0), 'grade': 'B', 'score': 17}], 'restaurant_id': '41704620', '_id': ObjectId('56a632357fe9e361aba6102d'), 'address': {'street': '2 Avenue', 'coord': [-73.9557413, 40.7720266], 'zipcode': '10075', 'building': '1480'}, 'cuisine': 'Italian'}
balabala一大堆
```

更精细的查找，一堆鸟文，总之支持各种精确匹配

格式如下

```
{ <field1>: <value1>, <field2>: <value2>, ... }
cursor = db.restaurants.find({"borough": "Manhattan"})
```

#### 特殊条件查询（支持操作符）

支持比较和与或逻辑操作，格式如下

```
{ <field1>: { <operator1>: <value1> } }
```

- 大于操作  ($gt)

找出一堆数据里打分超过 30 分的查询命令

```
cursor = db.restaurants.find({"grades.score": {"$gt": 30}})
for document in cursor:
    print(document)
```

- 小于操作 ($lt)

`cursor = db.restaurants.find({"grades.score": {"$lt": 10}})`

- 等于操作 ($eq)

#### 组合条件查询

- 逻辑与

用半角逗号隔开，如找既是意大利邮编又是 10075 的东西

`cursor = db.restaurants.find({"cuisine": "Italian", "address.zipcode": "10075"})`

- 逻辑或

这个操作格式比较奇特，如

```
cursor = db.restaurants.find(
    {"$or": [{"cuisine": "Italian"}, {"address.zipcode": "10075"}]})
```

#### 神技：查询结果排序

将查询结果按照 borough 升序，每个 borough 结果按照 address.zipcode 降序排列

```
import pymongo
cursor = db.restaurants.find().sort([
    ("borough", pymongo.ASCENDING),
    ("address.zipcode", pymongo.DESCENDING)
])
```

### 改

To change a field value, MongoDB provides update operators, such as $set to modify values. Some update operators, such as $set, will create the field if the field does not exist. See the individual update operators reference.

$set 操作符可以更改内容，如果表单不存在，那么会新建

#### 更改 Top-Level Fields 

这玩意咋翻译，顶层数据？

The following operation updates the first document with name equal to "Juni", using the $set operator to update the cuisine field and the $currentDate operator to update the lastModified field with the current date.

大意就是要给一个叫 Juni 的家伙改下风格和时间

```
result = db.restaurants.update_one(
    {"name": "Juni"},
    {
        "$set": {
            "cuisine": "American (New)"
        },
        "$currentDate": {"lastModified": True}
    }
)
```

这个操作会返回更改命中对象的次数，`result.matched_count`，返回 1 说明只匹配并修改了一次

还有个方法可以得到数据修改次数 `result.modified_count` 这里返回应该还是 1 因为目前为止，只有一个数据对象被修改了。

#### 更改内层数据

同上，返回结果记数也可查

```
result = db.restaurants.update_one(
    {"restaurant_id": "41156888"},
    {"$set": {"address.street": "East 31st Street"}}
)
```
#### 批量更改

update_one()  方法更改单个文件，对应的 update_many() 可以改一坨，如

```
result = db.restaurants.update_many(
    {"address.zipcode": "10016", "cuisine": "Other"},
    {
        "$set": {"cuisine": "Category To Be Determined"},
        "$currentDate": {"lastModified": True}
    }
)
```

依然可以如上述查到匹配的次数

#### 替换 replace_one()

> 注意：替换操作后，目标将只包含被替换的内容，原来的内容将会被抹除

```
result = db.restaurants.replace_one(
    {"restaurant_id": "41704620"},
    {
        "name": "Vella 2",
        "address": {
            "coord": [-73.9557413, 40.7720266],
            "building": "1480",
            "street": "2 Avenue",
            "zipcode": "10075"
        }
    }
)
```

#### 额外信息

update_*() 方法如果匹配不到，将不做任何操作，这点跟 set() 方法不同。

### 删

#### 条件删除

删除的操作总是欢快的，如干掉麦哈顿

`result = db.restaurants.delete_many({"borough": "Manhattan"})`

同样，查看 deleted_count 属性可以得到我们删了多少个

`result.deleted_count` 如果你使用的是事例中导入的数据的话，这个次数应该是 10259 

#### 全删

传入一个空字典`{}`，干掉所有数据

`result = db.restaurants.delete_many({})`

同样可以查到删了多少条数据，差不多是 15100 条的样子

#### 重置 / 清空 Collection

删除操作只是删除了 collection 中的内容，但 collection 本身及索引还是会保留。但往往删除整个 collection 更有效的方法就是干掉整个 collection，drop() 方法可以做到这点，清空并重置 collection。

`db.restaurants.drop()`

这个时候我们如果再查询数据库的话，会发现什么都没有了。

### 数据聚合 /数据统计

aggregate() 方法提供了基本的数据统计功能，格式如下

`db.collection.aggregate([<stage1>, <stage2>, ...])`

注意，因为我们之前已经清空了所有数据， 所以需要再次导入之，不然没得玩。

`mongoimport --db test --collection restaurants --drop --file dataset.json`

#### 按照字段分组并记数

Use the $group stage to group by a specified key. In the $group stage, specify the group by key in the _id field. $group accesses fields by the field path, which is the field name prefixed by a dollar sign $. The $group stage can use accumulators to perform calculations for each group. The following example groups the documents in the restaurants collection by the borough field and uses the $sum accumulator to count the documents for each group.

受不了了，粘一段英文，慢慢嚼。按街区分组，并统计每组数目总和

```
cursor = db.restaurants.aggregate(
    [
        {"$group": {"_id": "$borough", "count": {"$sum": 1}}}
    ]
)

for document in cursor:
    print(document)
```

返回分组结果，注意因为 for 循环顺序不定，所以得到的结果序列可能与官方教程略有不同。

```
{'_id': 'Missing', 'count': 51}
{'_id': 'Staten Island', 'count': 969}
{'_id': 'Manhattan', 'count': 10259}
{'_id': 'Brooklyn', 'count': 6086}
{'_id': 'Queens', 'count': 5656}
{'_id': 'Bronx', 'count': 2338}
```

#### 筛选分组数据

先干这碗热翔

Use the $match stage to filter documents. $match uses the MongoDB query syntax. The following pipeline uses $match to query the restaurants collection for documents with borough equal to "Queens" and cuisine equal to Brazilian. Then the $group stage groups the matching documents by the address.zipcode field and uses the $sum accumulator to calculate the count. $group accesses fields by the field path, which is the field name prefixed by a dollar sign $.

大意，先找到皇后街区的巴西风格餐馆，然后把找到的馆子按照邮编分组，并统计每组馆子个数

```
cursor = db.restaurants.aggregate(
    [
        {"$match": {"borough": "Queens", "cuisine": "Brazilian"}},
        {"$group": {"_id": "$address.zipcode", "count": {"$sum": 1}}}
    ]
)

for document in cursor:
    print(document)

```

结果比较诡异，我的结果跟官网教程结果是反的。

```
{'_id': '11377', 'count': 1}
{'_id': '11368', 'count': 1}
{'_id': '11101', 'count': 2}
{'_id': '11106', 'count': 3}
{'_id': '11103', 'count': 1}

```

### 索引

#### 概述

索引支持非常高效的查询。MongoDB 会在新建一个 collection 时会自动在`_id`字段建立索引

creat_index() 方法可以为 collection 建立索引，只会在索引不存在时建立索引。格式

`[ ( <field1>: <type1> ), ... ]`

> For an ascending index, specify pymongo.ASCENDING for <type>.

> For a descending index, specify pymongo.DESCENDING for <type>.

#### 在单一字段建立索引

例：在餐馆这个 collection 的 cuisine 字段创建一个递增的索引

```
import pymongo
db.restaurants.create_index([("cuisine", pymongo.ASCENDING)])
```

返回创建的索引名称 `'cuisine_1'`

#### 创建复杂索引

嗯，这个功能比较不好阐述，照例子描述之。

例：先对 cuisine 创建递增序列，然后对于每个 cuisine 里的 address.zipcode 创建递减序列

```
import pymongo
db.restaurants.create_index([
    ("cuisine", pymongo.ASCENDING),
    ("address.zipcode", pymongo.DESCENDING)
])

```

此方法同样返回创建的索引名

`"u'cuisine_1_address.zipcode_-1'"`

