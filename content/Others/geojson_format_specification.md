---
title: "GeoJSON"
date: 2016-01-30 17:32
description: "通用地图格式"
---

[TOC]

## 1. 介绍

GeoJSON 是一种表示多种地图数据结构的格式。一个 GeoJSON 对象可以表示一个几何图形、一个特性或者多个特性的集合。GeoJSON 支持如下几种几何类型：单点、单线、多边形、多点、多线、多个多边形和几何图形集合。GeoJSON 特征是包含一个几何对象和附加的类型还有一个表示一系列特性的特性集合（嗯，这里翻的好烂）。

一个完整的 GeoJSON 数据结构总是一个 JSON 格式的对象。在 GeoJSON 中，一个对象由一系列 key/value 组合而成——通常也成为成员。对于每个成员来说，key 永远是一个字符串，成员 values 既可以是字符串也可以是数字，对象，数组或者布尔值，如 True，False 和 Null。一个数组由上面提到过的各元素值组成。

### 1.1 例子

一个 GeoJSON 特性集合：

```
{ "type": "FeatureCollection",
    "features": [
      { "type": "Feature",
        "geometry": {"type": "Point", "coordinates": [102.0, 0.5]},
        "properties": {"prop0": "value0"}
        },
      { "type": "Feature",
        "geometry": {
          "type": "LineString",
          "coordinates": [
            [102.0, 0.0], [103.0, 1.0], [104.0, 0.0], [105.0, 1.0]
            ]
          },
        "properties": {
          "prop0": "value0",
          "prop1": 0.0
          }
        },
      { "type": "Feature",
         "geometry": {
           "type": "Polygon",
           "coordinates": [
             [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0],
               [100.0, 1.0], [100.0, 0.0] ]
             ]
         },
         "properties": {
           "prop0": "value0",
           "prop1": {"this": "that"}
           }
         }
       ]
     }
```

## 2. GeoJSON 对象

GeoJSON 对象通常由一个对象组成。这个对象（如下所示）表示一个几何图形，特性或者是一个特性集合。

- GeoJSON 对象可以拥有任意数量的成员（key/value）

- GeoJSON 对象必须有一个名为`"type"`的成员。这个成员的值是一个字符串，决定 GeoJSON 对象的类型。

- `"type"`成员的键值必须是以下任意一个："Point", "MultiPoint", "LineString", "MultiLineString", "Polygon", "MultiPolygon", "GeometryCollection", "Feature", or "FeatureCollection"。区分大小写。

- GeoJSON 可以包含一个可选的`"crs"`成员，其每个值都必须是一个相对系统坐标值

- GeoJSON 对象可以包含一个`"bbox"`成员，其值必须是一个受约束的边界数组。

### 2.1 几何对象

当几何对象的成员类型值是如下字符串时："Point", "MultiPoint", "LineString", "MultiLineString", "Polygon", "MultiPolygon", or "GeometryCollection" 该几何对象就是一个 GeoJSON 对象。

一个 GeoJSON 对象中除了几何集合以外都必须要有一个名为 "coordinates" 的成员。其值通常是一个数组。数组结构由几何模型的类型决定。如点、线、多边形等。

#### 2.1.1 位置 Positions

位置信息是一个基本的几何结构。一个几何对象的 coordinates 成员由位置可以由一个位置，如单点，一个位置数组，如线，多点几何模型，一个位置数组的数组，如多边形，多线，或者超长的位置数组，比如多个多边形。艾玛，译吐了。

位置信息由一个包含数字的数组表示。这个数组至少须要包含两个甚至更多的元素。元素顺序必须服从笛卡尔直角坐标系顺序，即 x，y，z。地理信息表示顺序则是 经度，纬度，海拔。这里支持任意数量的元素，但超出本文讨论范围。

#### 2.1.2 点 Point

对于 "point" 类型，坐标必须只能是一个位置

#### 2.1.3 多点 MultiPoint

对于多点，坐标成员必须是一个多位置的数组

#### 2.1.4 线 LineString

对于线型，坐标必须是两个或以上的位置集合数组

闭环线须要四或者更多位置。首尾位置坐标相等，表示这是一条闭合的环线。虽然闭环线并不是一个清晰的 GeoJSON 几何对象类型，它更符合多边形几何模型的定义。

#### 2.1.5 多线 MultiLineString

很好理解，多条线当然须要一个包含多条单线的坐标系列组合。

#### 2.1.6 多边形 Polygon

对于多边形类型，坐标成员必须是一堆闭环坐标数组。 For Polygons with multiple rings, the first must be the exterior ring and any others must be interior rings or holes.

#### 2.1.7 多个多边形 MultiPolygon

顾名思义，这里肯定是要包含一堆多边形的数组集合。

#### 2.1.8 几何集合 Geometry Collection

此类型的 GeoJSON 对象包含多个几何对象的集合

几何集合必须有一个名为 "geometries" 的成员。其值应该是一个数组。这个数组中的任一元素均为一个 GeoJSON 几何对象。

### 2.2 Feature Objects 这咋译？特性对象？囧rz。

A GeoJSON object with the type "Feature" is a feature object.

- A feature object must have a member with the name "geometry". The value of the geometry member is a geometry object as defined above or a JSON null value.

- A feature object must have a member with the name "properties". The value of the properties member is an object (any JSON object or a JSON null value).

- If a feature has a commonly used identifier, that identifier should be included as a member of the feature object with the name "id".

## 举例

以下例子均是一个完整的 GeoJSON 对象。注意结尾的留白在 JSON 中并不会被解析，空白的存在是为了显示更和谐，易读。实际上留白并不是必须的。

### Point 单点

`{ "type": "Point", "coordinates": [100.0, 0.0] }`

### LineString 单线

```
{ "type": "LineString",
    "coordinates": [ [100.0, 0.0], [101.0, 1.0] ]
    }
```

### Polygon 多边形

多边形分无孔洞和有孔洞两种情况，数组第一行元素代表多边形外轮廓，其余行元素均代表内轮廓，即孔洞。

- 无孔洞

```
{ "type": "Polygon",
    "coordinates": [
      [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ]
      ]
   }
```

- 有孔洞

```
{ "type": "Polygon",
    "coordinates": [
      [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ],
      [ [100.2, 0.2], [100.8, 0.2], [100.8, 0.8], [100.2, 0.8], [100.2, 0.2] ]
      ]
   }
```

### MultiPoint 多点

```
{ "type": "MultiPoint",
    "coordinates": [ [100.0, 0.0], [101.0, 1.0] ]
    }
```

### MultiLineString 多线

```
{ "type": "MultiLineString",
    "coordinates": [
        [ [100.0, 0.0], [101.0, 1.0] ],
        [ [102.0, 2.0], [103.0, 3.0] ]
      ]
    }
```

### MultiPolygon 多个多边形

```
{ "type": "MultiPolygon",
    "coordinates": [
      [[[102.0, 2.0], [103.0, 2.0], [103.0, 3.0], [102.0, 3.0], [102.0, 2.0]]],
      [[[100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0]],
       [[100.2, 0.2], [100.8, 0.2], [100.8, 0.8], [100.2, 0.8], [100.2, 0.2]]]
      ]
    }
```

### GeometryCollection 几何集合

```
{ "type": "GeometryCollection",
    "geometries": [
      { "type": "Point",
        "coordinates": [100.0, 0.0]
        },
      { "type": "LineString",
        "coordinates": [ [101.0, 0.0], [102.0, 1.0] ]
        }
    ]
  }
```

## 参考

[http://geojson.org/geojson-spec.html](http://geojson.org/geojson-spec.html) 
[Python GeoJSON Utilities](https://pypi.python.org/pypi/geojson/) 
