---
title: MongoDB索引类型
date: 2019-10-05 14:16:00
tags: 
    - Mongodb
    - 索引
---

### MongoDB索引的类型

#### 索引的概念
数据库索引是对数据库中一列或多列的值进行排序的一种数据结构，使用索引可以快速访问数据库表中的特定信息

数据库索引的功能类似于书籍的索引，书籍有了索引就不需要翻查整本书。与此类似，在进行查询时，数据库会首先在索引中查找，找到相应的条目后，就可以直接挑转到目标文档的位置

所有的索引信息被保存在system.indexes集合中，且默认总是为_id创建索引。db.system.indexes.find()可以查看当前数据库中创建的所有索引。

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g7nb9qbmbnj31a60n2niu.jpg)

#### 单字段索引

MongoDB可以在单个字段上建立索引，字段可以是普通字段、整个子文档以及子文档的某个字段。
```
db.student.createIndex({'address':1})
```

_id索引是系统默认创建的**单字段升序且具有唯一属性**的索引，每个集合文档都会包含该字段，不能被删除，默认值是ObjectId类型。

#### 复合索引

复合索引是建立在多个字段上的索引，功能比单字段索引强大，但使用较复杂。
```
db.student.createIndex({name:1, age:-1})
```
**索引字段的排序方向** 上面例子中，索引数据会首先按照name升序排序，对于name相同的文档会按照age进行降序排序。
**索引字段排序顺序**MongoDB在使用索引时，会自动进行优化，利用上面的索引可以支持如下的两个排序操作：
```
db.student.find().sort({name:1, age:-1})
db.student.find().sort({name:-1, age:1})
```

对于复合索引，Mongodb支持前缀匹配

#### 多键索引

多键索引是对数组类型建立的索引，**对数组建立索引，实际上是对数组的每个元素建立索引，而不是对数组本身建立索引**
```
obj1 = {name:'bob', scores:[95, 98]}
obj2 = {name:'joe', scores:[95, 99]}
db.student.createIndex({scores:1})
```
#### 哈希索引

哈希索引项中存储的是索引键的哈希值，哈希索引只支持等值查询，不支持范围查找
```
 db.student.createIndex({name:"hashed"})
```

