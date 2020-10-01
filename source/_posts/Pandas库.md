---
title: Pandas库
date: 2019-10-01 20:32:20
categories: Python
tags: Python包
toc: true
---

### 导入pandas
```
import pandas as pd
```

### Pandas 数据结构

#### Series
##### 能够容纳任何数据类型的一维标记数组

![](http://ww1.sinaimg.cn/mw690/405e87d9gy1g67cawl9jyj203z02yq2r.jpg)
```
>>> s = pd.Series([3, -5, 7, 4], index=['a', 'b', 'c', 'd'])
```

#### DataFrame
##### 一个二维标记的数据结构，包含可能不同类型的列

![](http://ww1.sinaimg.cn/mw690/405e87d9gy1g67c56zcnqj207s046wel.jpg)
```
>>> data = {'Country': ['Belgium', 'India', 'Brazil'], 
'Capital': ['Brussels', 'New Delhi', 'Brasília'],
'Population': [11190846, 1303171035, 207847528]}
>>> df = pd.DataFrame(data, columns=['Country', 'Capital', 'Population'])
```

### 选择 Selection
#### 获得 Getting

```
# 得到一个元素
>>> s['b'] 
-5

# 获取DataFrame的子集
>>> df[1:] 
 Country Capital Population
 1 India New Delhi 1303171035
 2 Brazil Brasília 207847528
```

#### 选择，布尔索引和设置 Selecting, Boolean Indexing & Setting

```
# 按行和列选择单个值
>>> df.iloc[[0],[0]] 
'Belgium' 

# 按行和列选择单个值
>>> df.iat([0],[0])
'Belgium'

# 通过行和列标签选择单个值
>>> df.loc[[0], ['Country']] 
'Belgium' 

# 通过行和列标签选择单个值
>>> df.at([0], ['Country']) 
'Belgium'

# 选择行子集的单行
>>> df.ix[2] 
Country Brazil 
Capital Brasília
Population 207847528

# 选择列的子集的单个列
>>> df.ix[:,'Capital']  
0 Brussels  
1 New Delhi
2 Brasília 

# 选择行和列
>>> df.ix[1,'Capital']  
'New Delhi'

# Series s where value is not >1
>>> s[~(s > 1)] 

# s where value is <-1 or >2
>>> s[(s < -1) | (s > 2)] 

# 使用过滤器调整dataframe
>>> df[df['Population']>1200000000]

# Set index a of Series s to 6
>>> s['a'] = 6
```

### 删除 Dropping
```
# 从行中删除值
>>> s.drop(['a', 'c'])

# 从列中删除值(轴=1)
>>> df.drop('Country', axis=1) 

# 删除行
>>> df.drop([1])
```

### 排序和等级 Sort & Rank
```
# 按标签沿轴排序
>>> df.sort_index()

# 默认按“行标签”升序排序，或df.sort_index(axis=0, ascending=True)
# df.sort_index(axis=1) #按“列标签”升序排序

# df.sort_index(by = ['b','a'],ascending = [False,True]) 
# 先按b列“降序”排列，因为b列中有相同值，相同值再按a列的“升序”排列

# sort_index(axis=0, level=None, ascending=True, inplace=False, kind='quicksort', na_position='last', sort_remaining=True, by=None)-->
# axis：0按照行名排序；1按照列名排序
# level：默认None，否则按照给定的level顺序排列---貌似并不是，文档
# ascending：默认True升序排列；False降序排列
# inplace：默认False，否则排序之后的数据直接替换原来的数据框
# kind：排序方法，{‘quicksort’, ‘mergesort’, ‘heapsort’}, default ‘quicksort’。似乎不用太关心。
# na_position：缺失值默认排在最后{"first","last"}
# by：按照某一列或几列数据进行排序，但是by参数貌似不建议使用


# 既可以根据列数据，也可根据行数据排序
>>> df.sort_values(by='Country') 

# 按b列升序排序
# df.sort_values(by='b') #等同于df.sort_values(by='b',axis=0)

# 先按b列降序，再按a列升序排序
# df.sort_values(by=['b','a'],axis=0,ascending=[False,True])

# 按行3升序排列
df.sort_values(by=3,axis=1) #必须指定axis=1

# 按行3升序，行0降排列
# df.sort_values(by=[3,0],axis=1,ascending=[True,False])

# DataFrame.sort_values(by, axis=0, ascending=True, inplace=False, kind='quicksort', na_position='last')
# axis：{0 or ‘index’, 1 or ‘columns’}, default 0，默认按照列排序，即纵向排序；如果为1，则是横向排序。
# by：str or list of str；如果axis=0，那么by="列名"；如果axis=1，那么by="行名"。
# ascending：布尔型，True则升序，如果by=['列名1','列名2']，则该参数可以是[True, False]，即第一字段升序，第二个降序。
# inplace：布尔型，是否用排序后的数据框替换现有的数据框。
# kind：排序方法，{‘quicksort’, ‘mergesort’, ‘heapsort’}, default ‘quicksort’。似乎不用太关心。
# na_position：{‘first’, ‘last’}, default ‘last’，默认缺失值排在最后面。

# 返回从小到大排序的下标
>>> df.rank()
```

### 检索系列/ DataFrame信息 Retrieving Series/DataFrame Information

#### 基本信息 Basic Information

```
>>> df.shape 
# (rows,columns) 

>>> df.index 
# 所有的行标签

>>> df.columns 
# 得到所有的列标签

>>> df.info() 
# 查看索引、数据类型和内存信息

>>> df.count()
# 统计
```

#### 数学计算操作 Summary

```
>>> df.sum() 
# 求和 返回一个含有列小计的Series 

>>> df.cumsum() 

>>> df.min()/df.max() 
# 最小值和最大值

>>> df.idxmin()/df.idxmax() 
# 最小值和最大值的索引值

>>> df.describe() 
# 针对 Series 或 DF 的列计算汇总统计

>>> df.mean() 
# 均值

>>> df.median()
# 中位数

>>> df.var()
# 方差

>>> df.std()
# 标准差
```

### 应用函数 Applying Functions

```
>>> f = lambda x: x*2

>>> df.apply(f)

>>> df.applymap(f)

```

### 输入/输出 I/O
#### Read and Write to CSV

```
>>> pd.read_csv('file.csv', header=None, nrows=5)
>>> df.to_csv('myDataFrame.csv')
```

#### Read and Write to Excel
```
>>> pd.read_excel('file.xlsx')
>>> pd.to_excel('dir/myDataFrame.xlsx', sheet_name='Sheet1')
# Read multiple sheets from the same file
>>> xlsx = pd.ExcelFile('file.xls')
>>> df = pd.read_excel(xlsx, 'Sheet1')
```

#### Read and Write to SQL Query or Database Table
```
>>> from sqlalchemy import create_engine
>>> engine = create_engine('sqlite:///:memory:')
>>> pd.read_sql("SELECT * FROM my_table;", engine)
>>> pd.read_sql_table('my_table', engine)
>>> pd.read_sql_query("SELECT * FROM my_table;", engine)

# read_sql()is a convenience wrapper around read_sql_table() and 
read_sql_query()

>>> pd.to_sql('myDf', engine)
```