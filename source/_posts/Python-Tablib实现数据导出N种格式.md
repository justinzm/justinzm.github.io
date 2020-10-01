---
title: Python Tablib实现数据导出N种格式
date: 2019-10-05 20:28:31
categories: Python
tags: Python包
toc: true
---

### 遇见 Tablib
我们在 Python 实际开发过程中，经常涉及将数据导出为 Excel、Csv、Yaml、Json 等各种格式的文件的需求，一些粗鲁的实现方式是通过安装各种第三方模块以支持不同格式文件的导出操作。

接下来要我们要介绍的这个模块，再也不需要担心因导出不同格式数据时而调用各种第三方模块了，当然使用 Pandas 也是一个不错的选择，只是过于重量级了。

Python Tablib 是麻省理工学院授权的与格式无关的表格数据集库。支持导入、导出和操作表格数据集。优雅的将数据导出为各种不同的格式，包括Excel，Json，Html，Yaml，Csv，Tsv等格式。

### Tablib 安装
我们在 Windows10 Python3 环境下，使用 pip 进行安装 ，安装命令如下：
```
pip install tablib
```
接下来，让我们一步步了解如何使用 Tablib ，简洁而又优雅的实现将数据导出为各种格式。

### 创建数据集
我们使用 tablib.Dataset 方法，采用如下方式创建数据集：
```
import tablib

# 表头 字段名或者列名
headers = ('id', 'name', 'age', 'grade')
# 数据
data = [
    [1, 'Troy', 20, 78],
    [2, 'Orla', 21, 89],
    [3, 'Cody', 20, 96],
    [4, 'Yana', 19, 77],
    [5, 'Maya', 21, 59],
]

# 创建数据集
dataset_o = tablib.Dataset(*data, headers=headers)
print(dataset_o)

# id|name|age|grade
# --|----|---|-----
# 1 |Troy|20 |78   
# 2 |Orla|21 |89   
# 3 |Cody|20 |96   
# 4 |Yana|19 |77   
# 5 |Maya|21 |59 
```
我们还可以另一种方式创建数据集，如下：
```
import tablib

dataset_t = tablib.Dataset()

# 添加表头 字段名或者列名
dataset_t.headers = ('id', 'name', 'age', 'grade')
# 数据
data = [
    [1, 'Troy', 20, 78],
    [2, 'Orla', 21, 89],
    [3, 'Cody', 20, 96],
    [4, 'Yana', 19, 77],
    [5, 'Maya', 21, 59],
]

for t in data:
    # 添加数据
    dataset_t.append(t)
print(dataset_t)

# id|name|age|grade
# --|----|---|-----
# 1 |Troy|20 |78   
# 2 |Orla|21 |89   
# 3 |Cody|20 |96   
# 4 |Yana|19 |77   
# 5 |Maya|21 |59 
```

我们还可以通过导入现有文件数据的方式创建数据集，如下，我们为 dump.json 文件。
```
[["Jack", "20"], ["Tom", "30"]]
```

我们使用 tablib.Dataset.load 方法导入 dump.json 文件数据，实现如下：
```
import tablib

dataset_json = tablib.Dataset().load(open('dump.json').read())
print(dataset_json)
print(dataset_json.json)

# Jack|20
# Tom |30
# [["Jack", "20"], ["Tom", "30"]]
```

创建一个 tablib.Dataset 通过 load 方法，导入现已存在的文件。强大之处是 tablib 能够自动检测传入的文件类型，并使用适当的格式化方法进行导入，实现从各种不同的文件类型中导入。

比如，我们再导入 dataset.xlsx 文件，代码实现如下：
```
import tablib
dataset_xlsx = tablib.Dataset().load(open('dump.xlsx').read())
```

### 添加行

我们可以使用 tablib.Dataset.append 方法，实现向数据集尾部添加行数据，需要注意的是每行的元素数量应与数据集的列数相同

我们也可以使用 tablib.Dataset.insert 方法，实现向数据集指定位置添加行数据，同样需要注意每行的元素数量应与数据集的列数相同

代码实现如下：

```
import tablib

# 表头 字段名或者列名
headers = ('id', 'name', 'age', 'grade')
# 数据
data = [
    [1, 'Troy', 20, 78],
    [2, 'Orla', 21, 89],
    [3, 'Cody', 20, 96],
    [4, 'Yana', 19, 77],
    [5, 'Maya', 21, 59],
]

# 创建数据集
dataset_o = tablib.Dataset(*data, headers=headers)

# 添加到最后一行的下面
dataset_o.append([7, 'Lucy', 21, 68])

# 向数据集的第6行，添加数据
dataset_o.insert(5, [6, 'Zoya', 20, 99])
print(dataset_o)

# id|name|age|grade
# --|----|---|-----
# 1 |Troy|20 |78   
# 2 |Orla|21 |89   
# 3 |Cody|20 |96   
# 4 |Yana|19 |77   
# 5 |Maya|21 |59   
# 6 |Zoya|20 |99   
# 6 |Lucy|21 |68 
```

### 添加列
我们可以使用 tablib.Dataset.append_col 方法，实现向数据集**尾部添加列数据**，需要注意的是每列的元素数量应与数据集的行数相同。

我们也可以使用 tablib.Dataset.insert_col 方法，实现向数据集指定位置添加列数据，需要注意的是每列的元素数量应于数据集的行数相同，代码实现如下
```
import tablib

# 表头 字段名或者列名
headers = ('id', 'name', 'age', 'grade')
# 数据
data = [
    [1, 'Troy', 20, 78],
    [2, 'Orla', 21, 89],
    [3, 'Cody', 20, 96],
    [4, 'Yana', 19, 77],
    [5, 'Maya', 21, 59],
]

# 创建数据集
dataset_o = tablib.Dataset(*data, headers=headers)

# 添加列到最后一列，数据集中有5行，因此该列数据应为5个元素
dataset_o.append_col(['302', '303', '304', '305', '306'], header='class')

# 在第3列添加新列，数据集中有5行，因此该列数据应为5个元素。
dataset_o.insert_col(2, ('male', 'female', 'abc', 'efg', 'zxc'), header='sex')

print(dataset_o)

# id|name|sex   |age|grade|class
# --|----|------|---|-----|-----
# 1 |Troy|male  |20 |78   |302  
# 2 |Orla|female|21 |89   |303  
# 3 |Cody|abc   |20 |96   |304  
# 4 |Yana|efg   |19 |77   |305  
# 5 |Maya|zxc   |21 |59   |306 
```

### 选择行和列
我们可以像Python列表一样通过切片和索引获取行数据。像Python字典通过列名和索引获取列数据，代码实现如下：
```
import tablib

# 表头 字段名或者列名
headers = ('id', 'name', 'age', 'grade')
# 数据
data = [
    [1, 'Troy', 20, 78],
    [2, 'Orla', 21, 89],
    [3, 'Cody', 20, 96],
    [4, 'Yana', 19, 77],
    [5, 'Maya', 21, 59],
]

# 创建数据集
dataset_o = tablib.Dataset(*data, headers=headers)

# 获得表头
print(dataset_o.headers)

# 获取第一行
print(dataset_o[0])

# 获取grade列
print(dataset_o['grade'])

# 获取最后一行
print(dataset_o[-1])

# ['id', 'name', 'age', 'grade']
# (1, 'Troy', 20, 78)
# [78, 89, 96, 77, 59]
# (5, 'Maya', 21, 59)
```

### 删除行
我们可以使用以下方式，实现行删除：

- tablib.Dataset.pop ：删除最后一行
- tablib.Dataset.lpop ：删除第一行
- del dataset[n: m] ：删除指定范围的行

```
import tablib

# 表头 字段名或者列名
headers = ('id', 'name', 'age', 'grade')
# 数据
data = [
    [1, 'Troy', 20, 78],
    [2, 'Orla', 21, 89],
    [3, 'Cody', 20, 96],
    [4, 'Yana', 19, 77],
    [5, 'Maya', 21, 59],
]

# 创建数据集
dataset_o = tablib.Dataset(*data, headers=headers)

# 删除最后一行
dataset_o.pop()

# 删除第一行
dataset_o.lpop()

# 删除第[2,)行数据，只保留前两行
del dataset_o[2:]

print(dataset_o)

# id|name|age|grade
# --|----|---|-----
# 2 |Orla|21 |89   
# 3 |Cody|20 |96   
```

### 删除列
我们可以使用 del dataset['column_name'] ，实现删除指定列，代码实现如下：
```
import tablib

# 表头 字段名或者列名
headers = ('id', 'name', 'age', 'grade')
# 数据
data = [
    [1, 'Troy', 20, 78],
    [2, 'Orla', 21, 89],
    [3, 'Cody', 20, 96],
    [4, 'Yana', 19, 77],
    [5, 'Maya', 21, 59],
]

# 创建数据集
dataset_o = tablib.Dataset(*data, headers=headers)

# 删除age列
del dataset_o['age']

print(dataset_o)

# id|name|grade
# --|----|-----
# 1 |Troy|78   
# 2 |Orla|89   
# 3 |Cody|96   
# 4 |Yana|77   
# 5 |Maya|59   
```

### 数据格式转换
我们可以将数据集转换为如下任一格式，强无敌。

- Excel ( 支持Books)
- Json ( 支持Books)
- Yaml (支持 Books)
- Pandas DataFrames
- Csv
- Html
- Jira
- Tsv
- Ods
- Dbf
- Dict

```
import tablib

# 表头 字段名或者列名
headers = ('id', 'name', 'age', 'grade')
# 数据
data = [
    [1, 'Troy', 20, 20],
    [2, 'Orla', 21, 89],
    [3, 'Cody', 20, 96],
    [4, 'Yana', 19, 77],
    [5, 'Maya', 21, 59],
]

# 创建数据集
dataset_o = tablib.Dataset(*data, headers=headers)

print(dataset_o.yaml)
print(dataset_o.csv)
print(dataset_o.json)
print(dataset_o.df)

# - age: 20
#   grade: 20
#   id: 1
#   name: Troy
# - age: 21
#   grade: 89
#   id: 2
#   name: Orla
# - age: 20
#   grade: 96
#   id: 3
#   name: Cody
# - age: 19
#   grade: 77
#   id: 4
#   name: Yana
# - age: 21
#   grade: 59
#   id: 5
#   name: Maya
# 
# id,name,age,grade
# 1,Troy,20,20
# 2,Orla,21,89
# 3,Cody,20,96
# 4,Yana,19,77
# 5,Maya,21,59
# 
# [{"id": 1, "name": "Troy", "age": 20, "grade": 20}, {"id": 2, "name": "Orla", "age": 21, "grade": 89}, {"id": 3, "name": "Cody", "age": 20, "grade": 96}, {"id": 4, "name": "Yana", "age": 19, "grade": 77}, {"id": 5, "name": "Maya", "age": 21, "grade": 59}]
#    id  name  age  grade
# 0   1  Troy   20     20
# 1   2  Orla   21     89
# 2   3  Cody   20     96
# 3   4  Yana   19     77
# 4   5  Maya   21     59
```

### 数据导出
```
import tablib

# 表头 字段名或者列名
headers = ('id', 'name', 'age', 'grade')
# 数据
data = [
    [1, 'Troy', 20, 20],
    [2, 'Orla', 21, 89],
    [3, 'Cody', 20, 96],
    [4, 'Yana', 19, 77],
    [5, 'Maya', 21, 59],
]
                          
# 创建数据集
dataset_o = tablib.Dataset(*data, headers=headers)

# 设置标签（sheet）
dataset_o.title = 'dataset_o'
with open('dataset_o.xlsx', 'wb') as xlsx:
    xlsx.write(dataset_o.xlsx)
```
使用DataBook 导出多页数据