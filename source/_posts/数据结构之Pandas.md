---
title: 数据结构之Pandas
date: 2019-12-15 11:39:49
categories: Python
tags: Pandas
toc: true
---

Pandas 是 Python 为解决数据分析而创建的，详情看官网 (https://pandas.pydata.org/)。 在使用 pandas 之前，需要引进它，语法如下：

```
import pandas
```

这样你就可以用 pandas 里面所有的内置方法 (build-in methods) 了，比如创建一维的 Series 和二维的 DataFrame。

```
pandas.Series()
pandas.DataFrame()
```

但是每次写 pandas 字数有点多，通常我们给 pandas 起个别名 pd，用以下语法，这样所有出现 pandas 的地方都可以用 pd 替代。

```
import pandas as pd
```

Pandas 里面的数据结构是「多维数据表」，学习它可以类比这 NumPy 里的「多维数组」。1/2/3 维的「多维数据表」分别叫做 Series (系列), DataFrame (数据帧) 和 Panel (面板)，和1/2/3 维的「多维数组」的类比关系如下。

![skLkuq](http://img.kekepu.com/uPic/skLkuq.jpg)

由于「系列」、「数据帧」和「面板」这些直译过来的中文名词听起来有些奇怪，在本帖还是直接用 Series, DataFrame 和 Panel。

对比 NumPy (np) 和 Pandas (pd) 每个维度下的数据结构，不难看出

    pd 多维数据表 = np 多维数组 + 描述

其中

- Series = 1darray + index
- DataFrame = 2darray + index + columns
- Panel = 3darray + index + columns + item

每个维度上的「索引」使得「多维数据表」比「多维数组」涵盖更多的信息，如下图，左边的 2d array 仅仅储存了一组数值 (具体代表什么意思却不知道)，而右边的 DataFrame 一看就知道这是平安银行和茅台从 2018-1-3 到 2019-1-3 的价格。

![BARYJJ](http://img.kekepu.com/uPic/BARYJJ.jpg)

学习 pandas 遵循的 Python 里「万物皆对象」的原则，既然把数据表当对象，我们就按着数据表的创建、数据表的存载、数据表的获取、数据表的合并和连接、数据表的重塑和透视、和数据表的分组和整合来盘一盘 Pandas。

## 数据表的创建

数据表有三大类型

- Series: 一维数据，类似于 python 中的基本数据的 list 或 NumPy 中的 1D array。Pandas 里最基本的数据结构
- DataFrame: 二维数据，类似于 R 中的 data.frame 或 Matlab 中的 Tables。DataFrame 是 Series 的容器
- Panel：三维数据。Panel 是 DataFrame 的容器

#### 知识点

    最常见的数据类型是二维的 DataFrame，其中
    
    每行代表一个示例 (instance)
    每列代表一个特征 (feature)
    
    DataFrame 可理解成是 Series 的容器，每一列都是一个 Series，或者 Series 是只有一列的 DataFrame。
    
    Panel 可理解成是 DataFrame 的容器。

接下来我们用代码来创建 pandas 数据表，有两种方式：

1. 按步就班的用 pd.Series(), pd.DataFrame() 和 pd.Panel()
1. 一步登天的用万矿里面的 WindPy API 读取

### 2.1 按部就班法

#### 一维Series

创建 Series 只需用下面一行代码
```
pd.Series( x, index=idx )
```
其中 x 可以是

1. 列表 (list) 
1. numpy 数组 (ndarray)
1. 字典 (dict)

x 是位置参数

index 是默认参数，默认值为 idx = range(0, len(x))

##### 用列表

```
s = pd.Series([27.2, 27.65, 27.70, 28])
s
```
```
0 27.20
1 27.65
2 27.70
3 28.00
dtype: float64
```
打印出来并不仅仅是列表里面的浮点数，每个浮点数前面还有一个索引，在本例中是 0, 1, 2, 3。

因此在创建 Series 时，如果不显性设定 index，那么 Python 给定一个默认从 0 到 N-1 的值，其中 N 是 x 的长度。

Series s 也是一个对象，用 dir(s) 可看出关于 Series 所有的属性和内置函数，其中最重要的是

- 用 s.values 打印 s 中的元素
- 用 s.index 打印 s 中的元素对应的索引

    s.values
```
array([27.2 , 27.65, 27.7 , 28. ])
```
    s.index
```
RangeIndex(start=0, stop=4, step=1)
```

不难发现，以上创建的 Series 和 numpy 数组比多了「索引」，但这种 0,1,2,3 的索引是在没有什么描述意义。实际上我们定义的 s 是海底捞在 2019 年 4 月 1 日到 2019 年 4 月 4 日的股价，那么用日期来当索引是不是更好些？

```
dates = pd.date_range('20190401',periods=4)
s2 = pd.Series( [27.2, 27.65, 27.70, 28], index=dates )
s2
```
```
2019-04-01 27.20
2019-04-02 27.65
2019-04-03 27.70
2019-04-04 28.00
Freq: D, dtype: float64
```

显然，s2 比 s 包含的信息更多，这是 s2 的索引是一组日期对象，数据类型是 datetime64，频率是 D (天)。

    s2.index

```
DatetimeIndex(['2019-04-01', '2019-04-02', '2019-04-03', '2019-04-04'],
dtype='datetime64[ns]', freq='D')
```

你甚至还可以给 s2 命名，就叫海底捞股价如何？
```
s2.name = '海底捞股价'
s2
```
```
2019-04-01 27.20
2019-04-02 27.65
2019-04-03 27.70
2019-04-04 28.00
Freq: D, Name: 海底捞股价, dtype: float64
```

##### 用 numpy 数组

除了用列表，我们还可以用 numpy 数组来生成 Series。在下例中，我们加入缺失值 np.nan，并分析一下 Series 中另外 5 个属性或内置函数的用法：

- len: s 里的元素个数
- shape: s 的形状 (用元组表示)
- count: s 里不含 nan 的元素个数
- unique: 返回 s 里不重复的元素
- value_counts: 统计 s 里非 nan 元素的出现次数

对照上面函数的用法，下面的输出一看就懂了吧。

```
s = pd.Series( np.array([27.2, 27.65, 27.70, 28, 28, np.nan]) )
print( 'The length is', len(s) )
print( 'The shape is', s.shape )
print( 'The count is', s.count() )
```
```
The length is 6
The shape is (6,)
The count is 5
```
```
s.unique()
```
```
array([27.2 , 27.65, 27.7 , 28. , nan])
```
```
s.value_counts()
```
```
28.00 2
27.70 1
27.65 1
27.20 1
dtype: int64
```

##### 用字典

创建 Series 还可以用字典。字典的「键值对」的「键」自动变成了 Series 的索引 (index)，而「值」自动变成了Series 的值 (values)。代码如下 (下列用 name 参数来对 s3 命名)
```
data_dict = { 'BABA': 187.07, 'PDD': 21.83, 'JD': 30.79, 'BIDU': 184.77 }
s3 = pd.Series(data_dict, name='中概股')
s3.index.name = '股票代号'
s3
```
```
股票代号
BABA 187.07
PDD 21.83
JD 30.79
BIDU 184.77
Name: 中概股, dtype: float64
```
给 s3 起名中概股是因为阿里巴巴 (BABA)、拼多多 (PDD)、京东 (JD) 和百度 (BIDU) 都是中国公司但在美国上市的。此外还可以给 index 命名为 '股票代号'。

现在假设我们的股票代号为

```
stock = ['FB', 'BABA', 'PDD', 'JD']
s4 = pd.Series( sdata, index=stock )
s4
```
```
FB NaN
BABA 160.0
PDD 28.0
JD 25.0
dtype: float64
```

代号里多加了脸书 (FB)，而 sdata 字典中没有 FB 这个键，因此生成的 s4 在 FB 索引下对应的值为 NaN。再者，代号里没有百度 (BIDU)，因此 s4 里面没有 BIDU 对应的值 (即便 sdata 里面有)。

当两个 Series 进行某种操作时，比如相加，Python 会自动对齐不同 Series 的 index，如下面代码所示：

```
s3 + s4
```
```
BABA 320.0
BIDU NaN
FB NaN
JD 50.0
PDD 56.0
dtype: float64
```

Series 是 Pandas 里面最基本的数据结构，但是对应每个索引只有一个元素 (比如一个日期对应一个股价)，因此 Series 处理不了每个索引对应多个元素 (比如一个日期对应一个开盘价、收盘价、交易量等等)。而 DataFrame 可以解决这个问题。

#### 二维 DataFrame 

创建 DataFrame 只需用下面一行代码

```
pd.DataFrame( x, index=idx, columns=col )
```

其中 x 可以是

1. 二维列表 (list)
1. 二维 numpy 数组 (ndarray)
1. 字典 (dict)，其值是一维列表、numpy 数组或 Series
1. 另外一个 DataFrame

- x 是位置参数
- index 是默认参数，默认值为 idx = range(0, x.shape[0])
- columns 是默认参数，默认值为 col = range(0, x.shape[1])

##### 用列表或 numpy 数组

```
# df1 = pd.DataFrame( [[1, 2, 3], [4, 5, 6]] )
df1 = pd.DataFrame( np.array([[1, 2, 3], [4, 5, 6]]) )
df1
```

![xdQV2m](http://img.kekepu.com/uPic/xdQV2m.jpg)

在创建 DataFrame 时，如果不显性设定 index 和 columns 时，那么Python 给它们默认值，其中

- index = 0 到 r-1，r 是 x 的行数
- colmns = 0 到 c-1，c 是 x 的列数

##### 用对象为列表的字典

```
symbol = ['BABA', 'JD', 'AAPL', 'MS', 'GS', 'WMT']
data = {'行业': ['电商', '电商', '科技', '金融', '金融', '零售'],
        '价格': [176.92, 25.95, 172.97, 41.79, 196.00, 99.55],
        '交易量': [16175610, 27113291, 18913154, 10132145, 2626634, 8086946],
        '雇员': [101550, 175336, 100000, 60348, 36600, 2200000]}
df2 = pd.DataFrame( data, index=symbol )
df2.name='美股'
df2.index.name = '代号'
df2
```
![I6kbBQ](http://img.kekepu.com/uPic/I6kbBQ.jpg)

字典的「键值对」的「键」自动变成了 DataFrame 的栏 (columns)，而「值」自动变成了 DataFrame 的值 (values)，而其索引 (index) 需要另外定义。

分别来看 df2 的 values, columns 和 index。

```
df2.values
```
```
array([['电商', 176.92, 16175610, 101550],
       ['电商', 25.95, 27113291, 175336],
       ['科技', 172.97, 18913154, 100000],
       ['金融', 41.79, 10132145, 60348],
       ['金融', 196.0, 2626634, 36600],
       ['零售', 99.55, 8086946, 2200000]], dtype=object)
```
```
df2.columns
```
```
Index(['行业', '价格', '交易量', '雇员'], dtype='object')
```
```
df2.index
```
```
Index(['BABA', 'JD', 'AAPL', 'MS', 'GS', 'WMT'],
      dtype='object', name='代号')
```

**查看 DataFrame**

我们可以从头或从尾部查看 DataFrame 的 n 行，分别用 df2.head() 和 df2.tail(n)，如果没有设定 n，默认值为 5 行。

```
df2.head()
```

![S7fUxU](http://img.kekepu.com/uPic/S7fUxU.jpg)

```
df2.tail(3)
```
![QgbgGh](http://img.kekepu.com/uPic/QgbgGh.jpg)

**统计 DataFrame**

我们用 df2.describe() 还可以看看 DataFrame 每栏的统计数据。
```
df2.describe()
```
![QDWKxW](http://img.kekepu.com/uPic/QDWKxW.jpg)

函数 describe() 只对「数值型变量」有用 (没有对「字符型变量」行业栏做统计)，统计量分别包括个数、均值、标准差、最小值，25-50-75 百分数值，最大值。一般做数据分析第一步会用这个表大概看看

- 数据是否有缺失值 (每个栏下的 count 是否相等)？
- 数据是否有异常值 (最小值 min 和最大值 max 是否太极端)？

**升维 DataFrame**

我们用 MultiIndex.from_tuples() 还可以赋予 DataFrame 多层索引 (实际上增加了维度，多层索引的 DataFrame 实际上是三维数据)。

```
df2.index = pd.MultiIndex.from_tuples( 
            [('中国公司','BABA'), ('中国公司','JD'), 
             ('美国公司','AAPL'), ('美国公司','MS'), 
             ('美国公司','GS'), ('美国公司','WMT')] )
df2
```

![2e6UxJ](http://img.kekepu.com/uPic/2e6UxJ.jpg)

在 MultiIndex.from_tuples() 中传递一个「元组的列表」，每个元组，比如 ('中国公司', 'BABA')，第一个元素中国公司是第一层 index，第二个元素BABA是第二层 index。

DataFrame 是 Series 的容器，那什么是 DataFrame 的容器？Panel

#### 三维 Panel 

创建 Panel 只需用下面一行代码

```
pd.Panel( x, item=itm, major_axis=n1, minor_axis=n2 )
```

其中 x 可以是

1. 三维列表 (list)
1. 三维 numpy 数组 (ndarray)
1. 字典 (dict)，其值是 DataFrame

- x 是位置参数
- items 是默认参数 (axis 0)，默认值为 itm = range(0, number of DataFrame)
- major_axis 是默认参数 (axis 1)，默认值和 DataFrame 的默认 index 一样
- minor_axis 是默认参数 (axis 2)，默认值和 DataFrame 的默认 columns 一样

##### 用 numpy 数组
```
pn = pd.Panel(np.random.randn(2, 5, 4))
pn
```
```
<class 'pandas.core.panel.Panel'>
Dimensions: 2 (items) x 5 (major_axis) x 4 (minor_axis)
Items axis: 0 to 1
Major_axis axis: 0 to 4
Minor_axis axis: 0 to 3
```

Panel pn 含有 2 个 DataFrame，items 为 0, 1；每个 DataFrame 有 5 行 4 列，因此 major_axis 为 0,1,2,3,4，而 minor_axis 为 0,1,2,3。

##### 用对象为 DataFrame 的字典

```
dates = pd.date_range('20190401',periods=4)

data = {'开盘价': [27.2, 27.65, 27.70, 28],
        '收盘价': [27.1, 27.55, 27.45, 28.1]}
df1 = pd.DataFrame( data, index=dates )

data = {'开盘价': [367, 369.8, 378.2, 380.6],
        '收盘价': [369.5, 370.1, 380, 382.1]}
df2 = pd.DataFrame( data, index=dates )

p_data = {'海底捞' : df1, '腾讯' : df2}
pn = pd.Panel(p_data)
pn
```
```
<class 'pandas.core.panel.Panel'>
Dimensions: 2 (items) x 4 (major_axis) x 2 (minor_axis)
Items axis: 海底捞 to 腾讯
Major_axis axis: 2019-04-01 00:00:00 to 2019-04-04 00:00:00
Minor_axis axis: 开盘价 to 收盘价
```

分析上面的 Panel pn

- 有 2 个 DataFrame，items 为 '海底捞' 和 ‘腾讯’

- 每个 DataFrame 有 4 行 2 列

    1. major_axis 从 2019-04-01 到 2019-04-04  
    1. minor_axis 为 ‘开盘价’ 和 ‘收盘价’


让我们来查看两个 DataFrame 的内容

```
pn['海底捞']
```
![VTLIBT](http://img.kekepu.com/uPic/VTLIBT.jpg)

```
pn['腾讯']
```
![jdsf3e](http://img.kekepu.com/uPic/jdsf3e.jpg)

上面这种 Panel 类型的数据在量化投资中还蛮常见，比如我们需要 10 个股票在 1 年时期的 OHLC 价格 (Open, High, Low, Close)，Panel 的 Items, Major_axis 和 Minor_axis 正好可以存储这样的三维数据。如果 Panel 要废掉，那用什么容器来储存三维数据呢？

用多层索引 (Multi-index) 的 DataFrame

```
df = pd.concat([df1, df2])
code = ['海底捞', '腾讯']
midx = [ (c, d) for c in code for d in dates ] 
df.index =pd.MultiIndex.from_tuples( midx )
df
```
![XfXao5](http://img.kekepu.com/uPic/XfXao5.jpg)

首先用 concat() 函数 (下帖的内容) 将 df1 和 df2 连接起来；再用「列表解析法」生成 midx，它是一个元组的列表，c 是股票代码，d 是日期；最后放入 MultiIndex.from_tuples() 生成有多层索引的 DataFrame。

### 一步登天法

上节都是手敲一些数据来创建「多维数据表」的，现实中做量化分析时，数据量都会很大，一般都是从量化平台中或者下载好的 csv 中直接读取。本节介绍如何从量化平台「万矿」中读取数据来创建「多维数据表」的。

首先在 https://www.windquant.com 注册一个账号，点击「研究」后在点开一个 Notebook 作为你的研究环境 (这是要夸奖一下万矿的 Notebook 体验真的不错，而且数据质量方面还有万德保证)。

![muybHz](http://img.kekepu.com/uPic/muybHz.jpg)

接着必须加载 WindPy，然后执行 w.start() 启动 API 接口：
```
from WindPy import *
w.start()
```
![2JPYOm](http://img.kekepu.com/uPic/2JPYOm.jpg)

WindPy 里面有几个获取数据的核心函数，分别是

- 日期序列函数 wsd
- 多维数据函数 wss
- 行情数据函数 wsq
- 分钟序列数据函数 wsi

#### 日期序列函数 wsd

该函数支持股票、债券、基金、期货、指数等多种证券的基本资料、股东信息、市场行情、证券分析、预测评级、财务数据等各种数据，可以支持取单品种单指标、多品种单指标和单品种多指标的时间序列数据 (注：不支持多品种多指标)。函数定义如下
```
w.wsd(security, fields, startdate, enddate, options)
```

- security = 证券代号，可以是 str 或 list 
- fields = 指标，可以是 str 或 list
- startdate = 起始日，可以是 str 或 datetime
- enddate = 起始日，可以是 str 或 datetime
- options = 一些特定设置

##### 单品种单指标
获取平安银行在 2019-04-01 到 2019-04-04 的收盘价。
```
code = "000001.SZ"
factors = ["close"]
startDate = "2019-04-01"
endDate = "2019-04-04"
data = w.wsd(code, factors, startDate, endDate, usedf=True )
data
```
```
(0, CLOSE
2019-04-01 00:00:00.005 13.18
2019-04-02 00:00:00.005 13.36
2019-04-03 00:00:00.005 13.44
2019-04-04 00:00:00.005 13.86)
```

###### 知识点
    当 usedf=True 时返回元组
    
    - 元组第一个元素为 ErrorCode，其为 0 时表示数据获取正常
    - 元组第二个元素为获取的数据 DataFrame，其中 index 列为时间，columns 为参数 Fields 各指标

上面结果 errorcode = 0，要获取 DataFrame 只需访问 data[1]
```
data[1]
```

![WlBbHU](http://img.kekepu.com/uPic/WlBbHU.jpg)

##### 单品种多指标

获取平安银行在 2019-04-01 到 2019-04-04 的开盘价、最低价、最高价和收盘价。
```
code = "000001.SZ"
factors = "open,low,high,close"
startDate = "2019-04-01"
endDate = "2019-04-04"
data = w.wsd(code, factors, startDate, endDate, usedf=True )
data[1]
```
![FQlT2s](http://img.kekepu.com/uPic/FQlT2s.jpg)

##### 多品种单指标

获取平安银行、万科、茅台在 2019-04-01 到 2019-04-04 的收盘价。
```
code = ["000001.SZ","000002.SZ","600519.SH"]
factors = "close"
startDate = "2019-04-01"
endDate = "2019-04-04"
data = w.wsd(code, factors, startDate, endDate, usedf=True )
data[1]
```
![KhqXy1](http://img.kekepu.com/uPic/KhqXy1.jpg)

#### 多维数据函数 wss

该函数同样支持股票、债券、基金、期货、指数等多种证券的基本资料、股东信息、市场行情、证券分析、预测评级、财务数据等各种数据。但是 wss 支持取**多品种多指标某个时间点**的截面数据。函数定义如下
```
w.wss(security, fields, option)
```

- security = 证券代号，可以是 str 或 list 
- fields = 指标，可以是 str 或 list
- options = 一些特定设置

获取平安银行、万科、茅台在 2018-12-31 的收盘价、交易量、每股盈余和 profit/GR。

```
date = "2018-12-31"
codes = ["000001.SZ","000002.SZ","600519.SH"]
factors = "close, volume, eps_basic, profittogr"
data = w.wss( codes, factors, 
              "rptDate="+date+";currencyType=", usedf=True)
data[1]
```
![FLv1w2](http://img.kekepu.com/uPic/FLv1w2.jpg)

如果要看财务数据，万矿是取每个季度最后一天作为报告期，如取 2018 年的四个定期报告数据，那报告期设置分别为：

- 一季报：2018-03-31
- 半年报：2018-06-30
- 三季报：2018-09-30
- 年报：   2018-12-31

本例 2018-12-31 是年报的数据。

#### 行情数据函数 wsq

该函数支持股票、债券、基金、期货、指数等多种证券品种的实时行情数据。函数定义如下
```
w.wsq(security, fields, func=None)
```

- security = 证券代号，可以是 str 或 list 
- fields = 指标，可以是 str 或 list
- func = 回调函数


获取易方达深证 100ETF 里所有成分中的各种行情指标。

```
ETF = w.wset("allfundhelddetail", "rptdate=20181231;windcode=159901.OF")
codes = ETF.Data[2]
fields = "rt_last,rt_vol,rt_chg,rt_pct_chg,rt_vwap,rt_ask1,rt_bid1"
data = w.wsq( codes, fields )
            
data = pd.DataFrame( data.Data, 
                     index=data.Fields, 
                     columns=data.Codes).T
data.head(3).append(data.tail(3))
```
![LFzLgC](http://img.kekepu.com/uPic/LFzLgC.jpg)

读者肯定好奇第一行代码怎么来的？这里 wset 是专门收集数据集信息的函数，万矿做的好的东西是又一套 GUI 帮你生成第一行代码，展示如下：

![fPxWA4](http://img.kekepu.com/uPic/fPxWA4.jpg)

点击「API 函数」下面的「WSET 数据集」会带给你以下界面。再选择「ETF 申购成分信息」。

![X2j3x0](http://img.kekepu.com/uPic/X2j3x0.jpg)

点击下一步得到

![6NY2dE](http://img.kekepu.com/uPic/6NY2dE.jpg)

看到没有第一行代码就这样生成了，获取数据的门槛迅速降低了好多。

#### 分钟序列数据函数 wsi

该函数获取选定证券品种的分钟线数据，包含基本行情和部分技术指标的分钟数据，分钟周期为 1-60 min，技术指标参数可以自定义设置。函数定义如下

```
w.wsi(security, fields, starttime = None, endtime = None, options = None)
```

- security = 证券代号，可以是 str 或 list 
- fields = 指标，可以是 str 或 list
- startdate = 起始日，可以是 str 或 datetime
- enddate = 起始日，可以是 str 或 datetime
- options = 一些特定设置

获取中金所 IF 股指期货当月连续合约 2019-04-01 09:30:00 开始至 2019-04-01 09:40:00 的 1 分钟数据。

```
codes ='IF00.CFE'
fields ='open, high, low, close'
IF = w.wsi( codes, fields, '2019-04-01 09:30:00', '2019-04-01 09:40:00', "", usedf=True )  
IF[1]
```
![lllizT](http://img.kekepu.com/uPic/lllizT.jpg)

### 数据表的存载

本节讲数据表的「保存」和「加载」，数据的存载没什么技术含量

- 保存只是为了下次再用处理好的 DataFrame
- 加载可以不用重新再定义 DataFrame

DataFrame 可以被保存为 Excel, csv, SQL 和 HDF5 格式，其语句一看就懂，用 to_数据格式，具体如下：

- to_excel()
- to_csv()
- to_sql()
- to_hdf()

如果要加载某种格式的数据到 DataFrame 里，用 read_数据格式，具体如下：

- read_excel()
- read_csv()
- read_sql()
- read_hdf()

我们只用 excel 和 csv 格式举例。

#### Excel 格式

用 pd.to_excel 函数将 DataFrame 保存为 .xlsx 格式，并保存到 ‘Sheet1’ 中，具体写法如下：

    pd.to_excel( '文件名'，'表名' )

```
df = pd.DataFrame(np.array([[1, 2, 3], [4, 5, 6]]))
df.to_excel('pd_excel.xlsx', sheet_name='Sheet1')
```

用 pd.read_excel( '文件名'，'表名' ) 即可加载该文件并存成 DataFrame 形式
```
df1 = pd.read_excel('pd_excel.xlsx', sheet_name='Sheet1')
df1
```
![wGaDsg](http://img.kekepu.com/uPic/wGaDsg.jpg)

#### csv 格式

用 pd.to_csv 函数将 DataFrame 保存为 .csv 格式，注意如果 index 没有特意设定，最后不要把 index 值存到 csv 文件中。具体写法如下：

    pd.to_csv( '文件名'，index=False )

```
data = {'Code': ['BABA', '00700.HK', 'AAPL', '600519.SH'],
        'Name': ['阿里巴巴', '腾讯', '苹果', '茅台'],
        'Market': ['US', 'HK', 'US', 'SH'],
        'Price': [185.35, 380.2, 197, 900.2],
        'Currency': ['USD', 'HKD', 'USD', 'CNY']}
df = pd.DataFrame(data)
df.to_csv('pd_csv.csv', index=False)
```

用 pd.read_csv( '文件名' ) 即可加载该文件并存成 DataFrame 形式
```
df2 = pd.read_csv('pd_csv.csv')
df2
```

![ybEt1C](http://img.kekepu.com/uPic/ybEt1C.jpg)

如果一开始储存 df 的时候用 index=True，你会发现加载完后的 df2 是以下的样子。

![UMxxyX](http://img.kekepu.com/uPic/UMxxyX.jpg)

df2 里面第一栏是 df 的 index，由于没有具体的 columns 名称，系统给它一个 "Unamed: 0"。因此在存储 df 的时候，如果 df.index 没有特意设定，记住要在 to_csv() 中把 index 设置为 **False**。

### 数据表的索引和切片

由于索引/切片 Series 跟 numpy 数组很类似，由于 Panel 在未来会被废掉，因此本节只专注于对 DataFrame 做索引和切片。本节以下面 df 为例做展示。

```

symbol = ['BABA', 'JD', 'AAPL', 'MS', 'GS', 'WMT']
data = {'行业': ['电商', '电商', '科技', '金融', '金融', '零售'],
        '价格': [176.92, 25.95, 172.97, 41.79, 196.00, 99.55],
        '交易量': [16175610, 27113291, 18913154, 10132145, 2626634, 8086946],
        '雇员': [101550, 175336, 100000, 60348, 36600, 2200000]}
df = pd.DataFrame( data, index=symbol )
df.name='美股'
df.index.name = '代号'
df
```
![8Dgvyf](http://img.kekepu.com/uPic/8Dgvyf.jpg)

用不同颜色标注了 df 的 index, columns 和 values，可视图如下：

![jqxXRs](http://img.kekepu.com/uPic/jqxXRs.jpg)

DataFrame 的索引或切片可以基于标签 (label-based) ，也可以基于位置 (position-based)，不像 numpy 数组的索引或切片只基于位置。

DataFrame 的索引或切片有四大类：

- 索引单元素：
    - 基于标签的 at
    - 基于位置的 iat

- 切片 columns：
    - 用 . 来切片单列
    - 用 [] 来切片单列或多列
    - 基于标签的 loc
    - 基于位置的 iloc

- 切片 index：
    - 用 [] 来切片单行或多行
    - 基于标签的 loc
    - 基于位置的 iloc

- 切片 index 和 columns：
    - 基于标签的 loc
    - 基于位置的 iloc

总体规律，基于标签就用 at 和 loc，基于位置就用 iat 和 iloc。下面我们来一类类分析：

#### 3.1 索引单元素

两种方法来索引单元素，情况 1 基于标签 at，情况 2 基于位置 iat。

- 情况 1 - df.at['idx_i', 'attr_j']
- 情况 2 - df.iat[i, j]

Python 里的中括号 [] 会代表很多意思，比如单元素索引，多元素切片，布尔索引等等，因此让 Python 猜你用的 [] 意图会很低效。如果你想索引单元素，明明白白的用 at 和 iat 效率最高。

##### 情况 1
```
df.at['AAPL','价格']
```
```
172.97
```
用 at 获取「行标签」为 'AAPL' 和「列标签」为 ‘价格’ 对应的元素。

##### 情况 2
```
df.iat[2,1]
```
```
172.97
```
用 iat 获取第 3 行第 2 列对应的元素。

索引单元素的总结图：

![BFKin1](http://img.kekepu.com/uPic/BFKin1.jpg)

#### 3.2 切片 columns

##### 切片单个 columns

切片单个 columns 会返回一个 Series，有以下四种情况。情况 1 用点 .；情况 2 用中括号 []；情况 3 基于标签 loc，情况 4 基于位置 iloc。

- 情况 1 - df.attr_i
- 情况 2 - df['attr_i']
- 情况 3 - df.loc[:, 'attr_i']
- 情况 4 - df.iloc[:, i]

情况 1 记住就可以了，没什么可说的。

情况 2 非常像二维 numpy 数组 arr 的切片，用 arr[i] 就能获取 arr 在「轴 0」上的第 i 个元素 (一个 1darray)，同理 df['attr_i'] 也能获取 df 的第 i 个 Series。

情况 3 和 4 的 loc 和 iloc 可类比于上面的 at 和 iat。带 i 的基于位置 (位置用整数表示，i 也泛指整数)，不带 i 的基于标签。里面的冒号 : 代表所有的 index (和 numpy 数组里的冒号意思相同)。

个人建议，如果追求简洁和方便，用 . 和 []；如果追求一致和清晰，用 loc 和 iloc。

###### 情况 1
```
df.价格
```
```
代号
BABA 176.92
JD 25.95
AAPL 172.97
MS 41.79
GS 196.00
WMT 99.55
Name: 价格, dtype: float64
```
用 . 获取「价格」那一栏下的 Series。

###### 情况 2
```
df['价格']
```
```
代号
BABA 176.92
JD 25.95
AAPL 172.97
MS 41.79
GS 196.00
WMT 99.55
Name: 价格, dtype: float64
```
用 [] 获取「价格」属性下的 Series。

###### 情况 3
```
df.loc[:, '交易量']
```
```
代号
BABA 16175610
JD 27113291
AAPL 18913154
MS 10132145
GS 2626634
WMT 8086946
Name: 交易量, dtype: int64
```
用 loc 获取「交易量」属性下的 Series。

###### 情况 4
```
df.iloc[:, 0]
```
```
代号
BABA 电商
JD 电商
AAPL 科技
MS 金融
GS 金融
WMT 零售
Name: 行业, dtype: object
```
用 iloc 获取第 1 列下的 Series。

切片单个 columns 的总结图：

![e6vVmL](http://img.kekepu.com/uPic/e6vVmL.jpg)

#### 切片多个 columns

切片多个 columns 会返回一个 sub-DataFrame (原 DataFrame 的子集)，有以下三种情况。情况 1 用中括号 []；情况 2 基于标签 loc，情况 3 基于位置 iloc。


- 情况 1 - df[['attr_i', 'attr_j']]
- 情况 2 - df.loc[:, 'attr_i':'attr_j']
- 情况 3 - df.iloc[:, i:j]

和切片单个 columns 相比：

- 情况 1 用一个列表来储存一组属性 'attr_i', 'attr_j'，然后在放进中括号 [] 里获取它们
- 情况 2 用 'attr_i':'attr_j' 来获取从属性 i 到属性 j 的 sub-DataFrame
- 情况 3 用 i:j 来获取从列 i+1 到列 j 的 sub-DataFrame

个人建议，如果追求简洁和方便，用 []；如果追求一致和清晰，用 loc 和 iloc。

##### 情况 1

```
df[ ['雇员', '价格'] ]
```

![Qucg5s](http://img.kekepu.com/uPic/Qucg5s.jpg)

用 [] 获取「雇员」和「价格」两个属性下的 sub-DataFrame。

##### 情况 2

```
df.loc[:, '行业':'交易量']
```

![230g6w](http://img.kekepu.com/uPic/230g6w.jpg)

用 loc 获取从属性 ‘行业’ 到 ‘交易量‘ 的 sub-DataFrame。

##### 情况 3

```
df.iloc[:, 0:2]
```
![dW5uC2](http://img.kekepu.com/uPic/dW5uC2.jpg)

用 iloc 获取第 1 和 2 列下的 sub-DataFrame。

切片多个 columns 的总结图：

![hLWFe2](http://img.kekepu.com/uPic/hLWFe2.jpg)

#### 3.3 切片 index

##### 切片单个 index 

切片**单个** index 有时会返回一个 Series，有以下两种情况。情况 1 基于标签 loc，情况 2 基于位置 iloc。

- 情况 1 - df.loc['idx_i', :]
- 情况 2 - df.iloc[i, :]

切片**单个** index 有时会返回一个只有一行的 DataFrame，有以下两种情况。情况 3 用中括号 [] 加「位置」，情况 4 用中括号 [] 加「标签」。

- 情况 3 - df[i:i+1]
- 情况 4 - df['idx_i':'idx_i']

情况 1 和 2 的 loc 和 iloc 可类比于上面的 at 和 iat。带 i 的基于位置 (位置用整数表示，i 也泛指整数)，不带 i 的基于标签。里面的冒号 : 代表所有的 columns (和 numpy 数组里的冒号意思相同)。

情况 3 用中括号 [] 加「位置」，位置 i:i+1 有前闭后开的性质。如果要获取第 i+1 行，需要用 i:i+1。

情况 4 用中括号 [] 加「标签」，标签没有前闭后开的性质。如果要获取标签 i，只需要用 'idx_i':'idx_i'。为什么不能只用 'idx_i' 呢？原因是 Python 会把 df['idx_i'] 当成切片 columns，然后发现属性中没有 'idx_i' 这一个字符，会报错的。

个人建议，只用 loc 和 iloc。情况 3 太麻烦，获取一行还要用 i:i+1。情况 4 的 df['idx_i'] 很容易和切片 columns 中的语句 df['attr_j'] 混淆。

###### 情况 1
```
df.loc[ 'GS', : ]
```
```
行业 金融
价格 196
交易量 2626634
雇员 36600
Name: GS, dtype: object
```

用 loc 获取标签为 ‘GS‘ 的 Series。(GS = Goldman Sachs = 高盛)

###### 情况 2
```
df.iloc[ 3, : ]
```
```
行业 金融
价格 41.79
交易量 10132145
雇员 60348
Name: MS, dtype: object
```

用 iloc 获取第 4 行下的 Series。(MS = Morgan Stanley = 摩根斯坦利)

###### 情况 3
```
df[1:2]
```

![Jesdow](http://img.kekepu.com/uPic/Jesdow.jpg)

用 [1:2] 获取第 2 行的 sub-DataFrame (只有一行)。

###### 情况 4
```
df['JD':'JD']
```

![ZmpunJ](http://img.kekepu.com/uPic/ZmpunJ.jpg)

用 ['JD':'JD'] 获取标签为 'JD' 的 sub-DataFrame (只有一行)。

切片单个 index 的总结图：

![fIiZ9h](http://img.kekepu.com/uPic/fIiZ9h.jpg)

##### 切片多个 index

切片多个 index 会返回一个 sub-DataFrame，有以下四种情况。情况 1 用中括号 [] 加「位置」，情况 2 用中括号 [] 加「标签」，情况 3 基于标签 loc，情况 4 基于位置 iloc。

- 情况 1 - df[i:j]
- 情况 2 - df['idx_i':'idx_j']
- 情况 3 - df.loc['idx_i':'idx_j', :]
- 情况 4 - df.iloc[i:j, :]

和切片单个 index 相比：

- 情况 1 用 [i:j] 来获取行 i+1 到行 j 的 sub-DataFrame
- 情况 2 用 ['idx_i':'idx_j'] 来获取标签 i 到标签 j 的 sub-DataFrame
- 情况 3 用 loc 加 'idx_i':'idx_j' 来获取从标签 i 到标签 j 的 sub-DataFrame
- 情况 4 用 iloc 加 i:j 来获取从行 i+1 到行 j 的 sub-DataFrame

个人建议，只用 loc 和 iloc。情况 1 和 2 的 df[] 很容易混淆中括号 [] 里的到底是切片 index 还是 columns。

###### 情况 1
```
df[ 1:4 ]
```

![WxjryM](http://img.kekepu.com/uPic/WxjryM.jpg)

用 [1:4] 获取第 2 到 4 行的 sub-DataFrame。

###### 情况 2
```
df[ 'GS':'WMT' ]
```

![Nyeeeu](http://img.kekepu.com/uPic/Nyeeeu.jpg)

用 ['GS':'WMT'] 获取标签从'GS' 到 'WMT' 的 sub-DataFrame。(WMT = Walmart = 沃尔玛)

###### 情况 3
```
df.loc[ 'MS':'GS', : ]
```

![yhuWWZ](http://img.kekepu.com/uPic/yhuWWZ.jpg)

用 loc 获取标签从 ‘MS‘ 到 'GS' 的 sub-DataFrame。注意 ‘MS’:’GS’ 要按着 index 里面元素的顺序，要不然会返回一个空的 DataFrame，比如：

```
df.loc[ 'MS':'JD', : ]
```

![5JH9rw](http://img.kekepu.com/uPic/5JH9rw.jpg)

情况 4
```
df.iloc[ 1:3, : ]
```

![m7Kaxg](http://img.kekepu.com/uPic/m7Kaxg.jpg)

用 iloc 获取第 2 到 3 行的 sub-DataFrame。

切片多个 index 的总结图：

![LWmLxI](http://img.kekepu.com/uPic/LWmLxI.jpg)

#### 3.4 切片 index 和 columns

切片多个 index 和 columns 会返回一个 sub-DataFrame，有以下两种情况。情况 1 基于标签 loc，情况 2 基于位置 iloc。

- 情况 1 - df.loc['idx_i':'idx_j', 'attr_k':'attr_l']
- 情况 2 - df.iloc[i:j, k:l]

清清楚楚，明明白白，用 loc 和 iloc。

###### 情况 1
```
df.loc[ 'GS':'WMT', '价格': ]
```

![yoj3Va](http://img.kekepu.com/uPic/yoj3Va.jpg)

用 loc 获取行标签从 ‘GS‘ 到 'WMT'，列标签从'价格'到最后的 sub-DataFrame。

###### 情况 2
```
df.iloc[ :2, 1:3 ]
```

![Bzs3e4](http://img.kekepu.com/uPic/Bzs3e4.jpg)

用 iloc 获取第 1 到 2 行，第 1 到 2 列的 sub-DataFrame。

切片 index 和 columns 的总结图：

![Nytc8P](http://img.kekepu.com/uPic/Nytc8P.jpg)

#### 3.5 高级索引

高级索引 (advanced indexing) 可以用布尔索引 (boolean indexing) 和调用函数 (callable function) 来实现，两种方法都返回一组“正确”的索引，而且可以和 loc , iloc , [] 一起套用，具体形式有以下常见几种：

- df.loc[布尔索引, :] 
- df.iloc[布尔索引, :] 
- df[布尔索引] 
- df.loc[调用函数, :]  
- df.iloc[调用函数, :] 
- df[调用函数] 

还有以下罕见几种：

- df.loc[:, 布尔索引] 
- df.iloc[:, 布尔索引] 
- df.loc[:, 调用函数]  
- df.iloc[:, 调用函数] 

读者可以想一想为什么第一组形式「常见」而第二组形式「罕见」呢？(**Hint: 看看两组里冒号 : 在不同位置，再想想 DataFrame 每一行和每一列中数据的特点**)

##### 布尔索引

布尔索引就是用一个由布尔类型值组成的数组来选择元素的方法。

当我们要过滤掉雇员小于 100,000 人的公司，我们可以用 loc 加上布尔索引。

```
print( df.雇员 >= 100000 )
df.loc[ df.雇员 >= 100000, : ]
```

![p4H6td](http://img.kekepu.com/uPic/p4H6td.jpg)

一种更简便的表达形式是用 df[]，但是我个人不喜欢 []，总觉得会引起「到底在切片 index 还是 columns」的歧义。

```
df[ df.雇员 >= 100000 ]
```
![48Pu5r](http://img.kekepu.com/uPic/48Pu5r.jpg)

现在来看一个「罕见」例子，假如我们想找到所有值为整数型的 columns
```
print( df.dtypes == 'int64' )
df.loc[ :, df.dtypes == 'int64' ]
```

![E4VBEz](http://img.kekepu.com/uPic/E4VBEz.jpg)


##### 调用函数

调用函数是只能有一个参数 (DataFrame, Series) 并返回一组索引的函数。因为调用函数定义在 loc , iloc , [] 里面，因此它就像匿名函数。

当我们要找出交易量大于平均交易量的所有公司，我们可以用 loc 加上匿名函数 (这里 x 代表 df)。

```
df.loc[ lambda x: x.交易量 > x.交易量.mean() , : ]
```

![9Zx3z7](http://img.kekepu.com/uPic/9Zx3z7.jpg)

在上面基础上再加一个条件 -- 价格要在 100 之上 (这里 x 还是代表 df)

```
df.loc[ lambda x: (x.交易量 > x.交易量.mean()) & (x.价格 > 100), : ]
```

![z17Ln5](http://img.kekepu.com/uPic/z17Ln5.jpg)

最后来看看价格大于 100 的股票 (注意这里 x 代表 df.价格)

```
df.价格.loc[ lambda x: x > 100 ]
```
```
代号
BABA 176.92
AAPL 172.97
GS 196.00
Name: 价格, dtype: float64
```

#### 3.6 多层索引

在 Panel 那节已经提到过，多层索引可以将「低维数据」升维到「高维数据」，此外，多层索引还可以。。。

##### 多层索引 Series

首先定义一个 Series，注意它的 index 是一个二维列表，列表第一行 dates 作为第一层索引，第二行 codes 作为第二层索引。

```
price = [190,32,196,192,200,189,31,30,199]
dates = ['2019-04-01']*3 + ['2019-04-02']*2
       +['2019-04-03']*2 + ['2019-04-04']*2
codes = ['BABA','JD','GS','BABA','GS','BABA','JD','JD','GS']

data = pd.Series( price, index=[ dates, codes ])
data
```
```
2019-04-01 BABA 190
           JD 32
           GS 196
2019-04-02 BABA 192
           GS 200
2019-04-03 BABA 189
           JD 31
2019-04-04 JD 30
           GS 199
dtype: int64
```

这个 Series 存储了四天里若干股票的价格，2019-04-01 储存了阿里巴巴、京东和高盛的股价，2019-04-04 只储存了京东和高盛的股价。试想，如果不用多层索引的 Series，我们需要用一个 DataFrame 来存储在这样的数据，把 index 设置成 dates，把 colums 设置成 codes。

让我们看看 Series 的多层 index 是如何表示的

```
data.index
```
```
MultiIndex(levels=[['2019-04-01', '2019-04-02', '2019-04-03', '2019-04-04'],
                   ['BABA', 'GS', 'JD']],
           labels=[[0, 0, 0, 1, 1, 2, 2, 3, 3],
                   [0, 2, 1, 0, 1, 0, 2, 2, 1]])
```

输出是一个 MultiIndex 的对象，里面有 levels 和 labels 二类信息。

##### 知识点

    索引既然分多层，那么肯定分「内层」和「外层」把，levels 就是描述层的先后的。levels 是一个二维列表，每一行只存储着「唯一」的索引信息：
    
    - dates 是第一层索引，有 4 个「唯一」元素
    - codes 是第二层索引，有 3 个「唯一」元素
    
    但是 data 里面有九行啊，4 个 dates 和 3 个 codes 怎么能描述这九行信息呢？这就需要 labels 了。labels 也是一个二维列表：
    
    - 第一行储存 dates 每个元素在 data 里的位置索引
    - 第二行储存 codes 每个元素在 data 里的位置索引

用 [] 加第一层索引可以获取第一层信息。
```
data['2019-04-02']
```
```
BABA 192
GS   200
dtype: int64
```

同理，用 loc 加第一层索引也可以切片获取第一层信息。

```
data.loc['2019-04-02':'2019-04-04']
```

```
2019-04-02 BABA 192
           GS 200
2019-04-03 BABA 189
           JD 31
2019-04-04 JD 30
           GS 199
dtype: int64
```

此外，切片还可以在不同层上进行，下面 loc  中的冒号 : 表示第一层所有元素，‘GS’ 表示第二层标签为 ‘GS’。

```
data.loc[ :, 'GS' ]
```

```
2019-04-01 196
2019-04-02 200
2019-04-04 199
dtype: int64
```

##### 多层索引 DataFrame

Series 只有 index，上面刚介绍完多层 index，DataFrame 有 index 和 columns，它们可以设置成多层吗？下面代码用 MultiIndex 函数创建「多层 index 」midx 和「多层columns」mcol。

midx 和 mcol 都是对象，各种都有 levels, labels, names 等性质。

```
data = [ ['电商', 101550, 176.92, 16175610], 
         ['电商', 175336, 25.95, 27113291], 
         ['金融', 60348, 41.79, 10132145], 
         ['金融', 36600, 196.00, 2626634] ]

midx = pd.MultiIndex( 
          levels=[['中国','美国'],
                  ['BABA', 'JD', 'GS', 'MS']], 
          labels=[[0,0,1,1],[0,1,2,3]],
          names=['地区', '代号'])

mcol = pd.MultiIndex( 
          levels=[['公司数据','交易数据'],
                  ['行业','雇员','价格','交易量']], 
          labels=[[0,0,1,1],[0,1,2,3]],
          names=['概括','细分'])

df = pd.DataFrame(data, index=midx, columns=mcol)
df
```

![BWpKhG](http://img.kekepu.com/uPic/BWpKhG.jpg)

这个 DataFrame 的 index 和 columns 都有两层，严格来说是个四维数据。下面看看如何进行「多层索引」的操作吧。

在第一层 columns 的 ‘公司数据’ 和第二层 columns 的 ‘行业’ 做索引，得到一个含两层 index 的 Series。

```
# 1st level-1 column, 2nd level-2 column
df['公司数据','行业']
```

```
地区 代号
中国 BABA 电商
     JD   电商
美国 GS   金融
     MS   金融
Name: (公司数据, 行业), dtype: object
```

在第一层 index 的 ‘中国’ 做切片，得到一个含两层 columns 的 DataFrame。

```
df.loc['中国'].loc['BABA':'JD']
```

![psuEhC](http://img.kekepu.com/uPic/psuEhC.jpg)

##### 调位 level
如果你不喜欢 index level 的顺序，可用 swaplevel 将它们调位。

```
df.swaplevel('地区', '代号')
```

![InWtze](http://img.kekepu.com/uPic/InWtze.jpg)

如果你不喜欢 columns level 的顺序，也可用 swaplevel 将它们调位。

```
df.columns = df.columns.swaplevel(0,1)
df
```

![ygIAt6](http://img.kekepu.com/uPic/ygIAt6.jpg)

##### 重设 index

有时候，一个 DataFrame 的一个或者多个 columns 适合做 index，这时可用 set_index 将它们设置为 index，如果要将 index 还原成 columns，那么用 reset_index 。

看下面这个例子。

```
data = {'地区': ['中国', '中国', '美国', '美国'],
        '代号': ['BABA', 'JD', 'MS', 'GS'],
        '行业': ['电商', '电商', '金融', '金融'],
        '价格': [176.92, 25.95, 41.79, 196.00],
        '交易量': [16175610, 27113291, 10132145, 2626634],
        '雇员': [101550, 175336, 60348, 36600] }
df = pd.DataFrame( data )
df
```

![Kttq8W](http://img.kekepu.com/uPic/Kttq8W.jpg)

将「地区」和「代号」设置为第一层 index 和第二层 index。

```
df2 = df.set_index( ['地区','代号'] )
df2
```

![XIctGd](http://img.kekepu.com/uPic/XIctGd.jpg)

将所有 index 变成 columns。

```
df2.reset_index()
```

![WOCzY6](http://img.kekepu.com/uPic/WOCzY6.jpg)

### 数据表的合并和连接

数据表可以按「键」合并，用 merge 函数；可以按「轴」来连接，用 concat 函数。

#### 4.1 合并

按键 (key) 合并可以分「单键合并」和「多键合并」。

##### 单键合并

单键合并用 merge 函数，语法如下：

    pd.merge( df1, df2, how=s, on=c )

c 是 df1 和 df2 共有的一栏，合并方式 (how=s) 有四种：

1. 左连接 (left join)：合并之后显示 df1 的所有行
1. 右连接 (right join)：合并之后显示 df2 的所有行
1. 外连接 (outer join)：合并 df1 和 df2 共有的所有行
1. 内连接 (inner join)：合并所有行 (默认情况)

首先创建两个 DataFrame：

- df_price：4 天的价格 (2019-01-01 到 2019-01-04)
- df_volume：5 天的交易量  (2019-01-02 到 2019-01-06)

```
df_price = pd.DataFrame( {'Date': pd.date_range('2019-1-1', periods=4),
                          'Adj Close': [24.42, 25.00, 25.25, 25.64]})
df_price
```

![aMOHla](http://img.kekepu.com/uPic/aMOHla.jpg)

```
df_volume = pd.DataFrame( {'Date': pd.date_range('2019-1-2', periods=5), 'Volume' : [56081400, 99455500, 83028700, 100234000, 73829000]})
df_volume
```

![ZTIGoE](http://img.kekepu.com/uPic/ZTIGoE.jpg)

接下来用 df_price  和 df_volume 展示四种合并。

###### left join

    pd.merge( df_price, df_volume, how='left' )

按 df_price 里 Date 栏里的值来合并数据

- df_volume 里 Date 栏里没有 2019-01-01，因此 Volume 为 NaN
- df_volume 里 Date 栏里的 2019-01-05 和 2019-01-06 不在 df_price 里 Date 栏，因此丢弃

###### right join

    pd.merge( df_price, df_volume, how='right' )

![fqTCgr](http://img.kekepu.com/uPic/fqTCgr.jpg)

按 df_volume 里 Date 栏里的值来合并数据

- df_price 里 Date 栏里没有 2019-01-05 和 2019-01-06，因此 Adj Close 为 NaN
- df_price 里 Date 栏里的 2019-01-01 不在 df_volume 里 Date 栏，因此丢弃

###### outer join

    pd.merge( df_price, df_volume, how='outer' )

![WaxZZp](http://img.kekepu.com/uPic/WaxZZp.jpg)

按 df_price 和 df_volume 里 Date 栏里的**所有值**来合并数据

- df_price 里 Date 栏里没有 2019-01-05 和 2019-01-06，因此 Adj Close 为 NaN
- df_volume 里 Date 栏里没有 2019-01-01，因此 Volume 为 NaN

###### inner join

    pd.merge( df_price, df_volume, how='inner' )

![IYKcK4](http://img.kekepu.com/uPic/IYKcK4.jpg)

按 df_price 和 df_volume 里 Date 栏里的**共有值**来合并数据

- df_price 里 Date 栏里的 2019-01-01 不在 df_volume 里 Date 栏，因此丢弃
- df_volume 里 Date 栏里的 2019-01-05 和 2019-01-06 不在 df_price 里 Date 栏，因此丢弃

##### 多键合并

多键合并用的语法和单键合并一样，只不过 on=c 中的 c 是多栏。

    pd.merge( df1, df2, how=s, on=c )

首先创建两个 DataFrame：

- portfolio1：3 比产品 FX Option, FX Swap 和 IR Option 的数量
- portfolio2：4 比产品 FX Option (重复名称), FX Swap 和 IR Swap 的数量

```
porfolio1 = pd.DataFrame({'Asset': ['FX', 'FX', 'IR'], 
                          'Instrument': ['Option', 'Swap', 'Option'], 
                          'Number': [1, 2, 3]})
porfolio1
```

![TfUZyD](http://img.kekepu.com/uPic/TfUZyD.jpg)

```
porfolio2 = pd.DataFrame({'Asset': ['FX', 'FX', 'FX', 'IR'], 
                          'Instrument': ['Option', 'Option', 'Swap', 'Swap'], 
                          'Number': [4, 5, 6, 7]})
porfolio2
```

![lSPGUJ](http://img.kekepu.com/uPic/lSPGUJ.jpg)

在 'Asset' 和 'Instrument' 两个键上做外合并。

pd.merge( porfolio1, porfolio2, 
          on=['Asset','Instrument'],
          how='outer')
          
![AEp8yG](http://img.kekepu.com/uPic/AEp8yG.jpg)

df1 和 df2 中两个键都有 FX Option 和 FX Swap，因此可以合并它们中 number 那栏。

- df1 中有 IR Option 而 df2 中没有，因此 Number_y 栏下的值为 NaN
- df2 中有 IR Swap 而 df1 中没有，因此 Number_x 栏下的值为 NaN

当 df1 和 df2 有两个相同的列 (Asset 和 Instrument) 时，单单只对一列 (Asset) 做合并产出的 DataFrame 会有另一列 (Instrument) 重复的名称。这时 merge 函数给重复的名称加个后缀 _x, _y 等等。


```
pd.merge( porfolio1, porfolio2,  on='Asset' )
```

![Z9rp6r](http://img.kekepu.com/uPic/Z9rp6r.jpg)

当没设定 merge 函数里参数 how 时，默认为 inner (内合并)。在 Asset 列下，df1 有 2 个 FX 和 1 个 IR，df2 有 3 个 FX 和 1 个 IR，内合并完有 8 行 (2×3+1×1)。

如果觉得后缀 _x, _y 没有什么具体含义时，可以设定 suffixes 来改后缀。比如 df1 和 df2 存储的是 portoflio1 和 portfolio2 的产品信息，那么将后缀该成 ‘1’ 和 ‘2’ 更贴切。

```
pd.merge( porfolio1, porfolio2,
          on='Asset', 
          suffixes=('1','2'))
```

![i6WnhT](http://img.kekepu.com/uPic/i6WnhT.jpg)

#### 4.2 连接

Numpy 数组可相互连接，用 np.concat；同理，Series 也可相互连接，DataFrame 也可相互连接，用 pd.concat。

##### 连接 Series

在 concat 函数也可设定参数 axis，

- axis = 0 (默认)，沿着轴 0 (行) 连接，得到一个更长的 Series
- axis = 1，沿着轴 1 (列) 连接，得到一个 DataFrame

被连接的 Series 它们的 index 可以重复 (overlapping)，也可以不同。

###### overlapping index

先定义三个 Series，它们的 index 各不同。


```
s1 = pd.Series([0, 1], index=['a', 'b'])
s2 = pd.Series([2, 3, 4], index=['c', 'd', 'e'])
s3 = pd.Series([5, 6], index=['f', 'g'])
```

沿着「轴 0」连接得到一个更长的 Series。

```
pd.concat([s1, s2, s3])
a 0
b 1
c 2
d 3
e 4
f 5
g 6
dtype: int64
```

沿着「轴 1」连接得到一个 DataFrame。

```
pd.concat([s1, s2, s3], axis=1)
```

![nQyzJR](http://img.kekepu.com/uPic/nQyzJR.jpg)

###### non-overlapping index

将 s1 和 s3 沿「轴 0」连接来创建 s4，这样 s4 和 s1 的 index 是有重复的。

```
s4 = pd.concat([s1, s3])
s4
a 0
b 1
f 5
g 6
dtype: int64
```

将 s1 和 s4 沿「轴 1」内连接 (即只连接它们共有 index 对应的值)

```
pd.concat([s1, s4], axis=1, join='inner')
```

![RCvI9E](http://img.kekepu.com/uPic/RCvI9E.jpg)

###### hierarchical index
最后还可以将 n 个 Series 沿「轴 0」连接起来，再赋予 3 个 keys 创建多层 Series。

```
pd.concat( [s1, s1, s3], keys=['one','two','three'])
```

```
one a 0
    b 1
two a 0
    b 1
three f 5
      g 6
dtype: int64
```

##### 连接 DataFrame

连接 DataFrame 的逻辑和连接 Series 的一模一样。

###### 沿着行连接 (axis = 0)

先创建两个 DataFrame，df1 和 df2。

```
df1 = pd.DataFrame( np.arange(12).reshape(3,4), 
                    columns=['a','b','c','d'])
df1
```

![zRAv7a](http://img.kekepu.com/uPic/zRAv7a.jpg)


```
df2 = pd.DataFrame( np.arange(6).reshape(2,3),
                    columns=['b','d','a'])
df2
```

![h0DiGw](http://img.kekepu.com/uPic/h0DiGw.jpg)

沿着行连接分两步

- 先把 df1 和 df2 **列标签**补齐
- 再把 df1 和 df2 **纵向连**起来

```
pd.concat( [df1, df2] )
```

![X40WuU](http://img.kekepu.com/uPic/X40WuU.jpg)

得到的 DataFrame 的 index = [0,1,2,0,1]，有重复值。如果 index 不包含重要信息 (如上例)，可以将 ignore_index 设置为 True，这样就得到默认的 index 值了。


```
pd.concat( [df1, df2], ignore_index=True )
```

![FCIuJv](http://img.kekepu.com/uPic/FCIuJv.jpg)

###### 沿着列连接 (axis = 1)

先创建两个 DataFrame，df1 和 df2。


```
df1 = pd.DataFrame( np.arange(6).reshape(3,2), 
                    index=['a','b','c'],
                    columns=['one','two'] )
df1
```

![obPARd](http://img.kekepu.com/uPic/obPARd.jpg)


```
df2 = pd.DataFrame( 5 + np.arange(4).reshape(2,2), 
                    index=['a','c'], 
                    columns=['three','four'])
df2
```

![1zYJ6K](http://img.kekepu.com/uPic/1zYJ6K.jpg)

沿着列连接分两步

- 先把 df1 和 df2 **行标签**补齐
- 再把 df1 和 df2 **横向**连起来

pd.concat( [df1, df2], axis=1 )

![aA8YzM](http://img.kekepu.com/uPic/aA8YzM.jpg)

### 数据表的重塑和透视

重塑 (reshape) 和透视 (pivot) 两个操作只改变数据表的布局 (layout)：

- 重塑用 stack 和 unstack 函数 (互为逆转操作)
- 透视用 pivot 和 melt 函数 (互为逆转操作)

#### 5.1 重塑

DataFrame 和「多层索引的 Series」其实维度是一样，只是展示形式不同。而重塑就是通过改变数据表里面的「行索引」和「列索引」来改变展示形式。

- 列索引 → 行索引，用 stack 函数
- 行索引 → 列索引，用 unstack 函数

##### 单层 DataFrame

创建 DataFrame df (1 层行索引，1 层列索引)
```
symbol = ['JD', 'AAPL']
data = {'行业': ['电商', '科技'],
        '价格': [25.95, 172.97],
        '交易量': [27113291, 18913154]}
df = pd.DataFrame( data, index=symbol )
df.columns.name = '特征'
df.index.name = '代号'
df
```

![i8fBGW](http://img.kekepu.com/uPic/i8fBGW.jpg)

从上表中可知：

- 行索引 = [JD, AAPL]，名称是代号
- 列索引 = [行业, 价格, 交易量]，名称是特征
 
###### stack: 列索引 → 行索引

列索引 (特征) 变成了行索引，原来的 DataFrame df 变成了两层 Series (第一层索引是代号，第二层索引是特征)。

```
c2i_Series = df.stack()
c2i_Series
```

```
代号 特征
JD   行业 电商
     价格 25.95
     交易量 27113291
AAPL 行业 科技
     价格 172.97
     交易量 18913154
dtype: object
```

**思考：变成行索引的特征和原来行索引的代号之间的层次是怎么决定的？好像特征更靠内一点，代号更靠外一点。**

###### unstack: 行索引 → 列索引

行索引 (代号) 变成了列索引，原来的 DataFrame df 也变成了两层 Series (第一层索引是特征，第二层索引是代号)。

```
i2c_Series = df.unstack()
i2c_Series
```

```
特征 代号
行业 JD 电商
     AAPL 科技
价格 JD 25.95
     AAPL 172.97
交易量 JD 27113291
       AAPL 18913154
dtype: object
```

**思考：变成列索引的特征和原来列索引的代号之间的层次是怎么决定的？这时好像代号更靠内一点，特征更靠外一点。**

###### 规律总结

对 df 做 stack 和 unstack 都得到了「两层 Series」，但是索引的层次不同，那么在背后的规律是什么？首先我们先来看看两个「两层 Series」的 index 包含哪些信息 (以及 df 的 index 和 columns)。

df.index, df.columns

![UyHwbm](http://img.kekepu.com/uPic/UyHwbm.jpg)

c2i_Series.index

![HmNCN1](http://img.kekepu.com/uPic/HmNCN1.jpg)

i2c_Series.index

![3urHvj](http://img.kekepu.com/uPic/3urHvj.jpg)

定义

- r = [JD, AAPL]，名称是代号
- c = [行业, 价格, 交易量]，名称是特征

那么

- df 的行索引 = r
- df 的列索引 = c
- c2i_Series 的索引 = [r, c]
- i2c_Series 的索引 = [c, r]

现在可以总结规律：

- 当用 stack 将 df 变成 c2i_Series 时，df 的列索引 c 加在其行索引 r 后面得到 [r, c] 做为 c2i_Series 的多层索引
- 当用 unstack 将 df 变成 i2c_Series 时，df 的行索引 r 加在其列索引 c 后面得到 [c, r] 做为 i2c_Series 的多层索引

###### 基于层和名称来 unstack

对于多层索引的 Series，unstack 哪一层有两种方法来确定：

- 基于层 (level-based)
- 基于名称 (name-based)

拿 c2i_Series 举例 (读者也可以尝试 i2c_Series)：

```
代号 特征
JD   行业 电商
     价格 25.95
     交易量 27113291
AAPL 行业 科技
     价格 172.97
     交易量 18913154
dtype: object
```

1. 基于层来 unstack() 时，没有填层数，默认为最后一层。

```
c2i_Series.unstack()
```

![PQCq4S](http://img.kekepu.com/uPic/PQCq4S.jpg)

c2i_Series 的最后一层 (看上面它的 MultiIndex) 就是 [行业, 价格, 交易量]，从行索引转成列索引得到上面的 DataFrame。

2. 基于层来 unstack() 时，选择第一层 (参数放 0)

```
c2i_Series.unstack(0)
```

![YCIPsG](http://img.kekepu.com/uPic/YCIPsG.jpg)

c2i_Series 的第一层 (看上面它的 MultiIndex) 就是 [JD, AAPL]，从行索引转成列索引得到上面的 DataFrame。

3. 基于名称来 unstack 

```
c2i_Series.unstack('代号')
```

![SxHDST](http://img.kekepu.com/uPic/SxHDST.jpg)

c2i_Series 的代号层 (看上面它的 MultiIndex) 就是 [JD, AAPL]，从行索引转成列索引得到上面的 DataFrame。

##### 多层 DataFrame

创建 DataFrame df (2 层行索引，1 层列索引)

```
data = [ ['电商', 101550, 176.92], 
         ['电商', 175336, 25.95], 
         ['金融', 60348, 41.79], 
         ['金融', 36600, 196.00] ]

midx = pd.MultiIndex( levels=[['中国','美国'],
                              ['BABA', 'JD', 'GS', 'MS']], 
                      labels=[[0,0,1,1],[0,1,2,3]],
                      names = ['地区', '代号'])

mcol = pd.Index(['行业','雇员','价格'], name='特征')

df = pd.DataFrame( data, index=midx, columns=mcol )
df
```

![roWXRN](http://img.kekepu.com/uPic/roWXRN.jpg)

从上表中可知：

- 行索引第一层 = r1 = [中国, 美国]，名称是地区
- 行索引第二层 = r2 = [BABA, JD, GS, MS]，名称是代号
- 列索引 = c = [行业, 雇员, 价格]，名称是特征

查看 df 的 index 和 columns 的信息


```
df.index, df.columns
```

![WN2BuG](http://img.kekepu.com/uPic/WN2BuG.jpg)

那么

- df 的行索引 = [r1, r2]
- df 的列索引 = c

1. 基于层来 unstack() 时，选择第一层 (参数放 0)

```
df.unstack(0)
```

![LlxFB8](http://img.kekepu.com/uPic/LlxFB8.jpg)

df 被 unstack(0) 之后变成 (行 → 列)

- 行索引 = r2
- 列索引 = [c, r1]

重塑后的 DataFrame 这时行索引只有一层 (代号)，而列索引有两层，第一层是特征，第二层是地区。

2. 基于层来 unstack() 时，选择第二层 (参数放 1)

```
df.unstack(1)
```

![fgHf8i](http://img.kekepu.com/uPic/fgHf8i.jpg)

df 被 unstack(1) 之后变成 (行 → 列)

- 行索引 = r1
- 列索引 = [c, r2]

重塑后的 DataFrame 这时行索引只有一层 (地区)，而列索引有两层，第一层是地区，第二层是代号。

3. 基于层先 unstack(0) 再 stack(0)

```
df.unstack(0).stack(0)
```

![1oQx1g](http://img.kekepu.com/uPic/1oQx1g.jpg)

df 被 unstack(0) 之后变成 (行 → 列)

- 行索引 = r2
- 列索引 = [c, r1]

再被 stack(0) 之后变成 (列 → 行)

- 行索引 = [r2, c]
- 列索引 = r1

重塑后的 DataFrame 这时行索引有两层，第一层是代号，第二层是特征，而列索引只有一层 (地区)。

4. 基于层先 unstack(0) 再 stack(1)

```
df.unstack(0).stack(1)
```

![oy37su](http://img.kekepu.com/uPic/oy37su.jpg)

df 被 unstack(0) 之后变成 (行 → 列)

- 行索引 = r2
- 列索引 = [c, r1]

再被 stack(1) 之后变成 (列 → 行)

- 行索引 = [r2, r1]
- 列索引 = c

重塑后的 DataFrame 这时行索引有两层，第一层是代号，第二层是地区，而列索引只有一层 (特征)。

5. 基于层先 unstack(1) 再 stack(0)

```
df.unstack(1).stack(0)
```

![IO6u4p](http://img.kekepu.com/uPic/IO6u4p.jpg)

df 被 unstack(1) 之后变成 (行 → 列)

- 行索引 = r1
- 列索引 = [c, r2]

再被 stack(0) 之后变成 (列 → 行)

- 行索引 = [r1, c]
- 列索引 = r2

重塑后的 DataFrame 这时行索引有两层，第一层是地区，第二层是特征，而列索引只有一层 (代号)。

6. 基于层先 unstack(1) 再 stack(1)


```
df.unstack(1).stack(1)
```

![R0cl6M](http://img.kekepu.com/uPic/R0cl6M.jpg)

df 被 unstack(1) 之后变成 (行 → 列)

- 行索引 = r1
- 列索引 = [c, r2]

再被 stack(1) 之后变成 (列 → 行)

- 行索引 = [r1, r2]
- 列索引 = c

重塑后的 DataFrame 这时行索引有两层，第一层是地区，第二层是特征，而列索引只有一层 (代号)。还原成原来的 df 了。

7. 基于层被 stack()，没有填层数，默认为最后一层。


```
df.stack()
```

```
地区 代号 特征
中国 BABA 行业 电商
          雇员 101550
          价格 176.92
     JD 行业 电商
        雇员 175336
...
美国 GS 雇员 60348
        价格 41.79
     MS 行业 金融
        雇员 36600
        价格 196
Length: 12, dtype: object
```

df 被 stack() 之后变成 (列 → 行)

- 行索引 = [r1, r2, c]
- 列索引 = []

重塑后的 Series 只有行索引，有三层，第一层是地区，第二层是代号，第三层是特征。

8. 基于层被 unstack() 两次，没有填层数，默认为最后一层。

```
df.unstack().unstack()
```

```
特征 代号 地区
行业 BABA 中国 电商
          美国 NaN
     JD 中国 电商
        美国 NaN
     GS 中国 NaN
...
价格 JD 美国 NaN
     GS 中国 NaN
        美国 41.79
     MS 中国 NaN
        美国 196
Length: 24, dtype: object
```

df 被第一次 unstack() 之后变成 (行 → 列)

- 行索引 = r1
- 列索引 = [c, r2]

df 被第二次 unstack() 之后变成 (行 → 列)

- 行索引 = []
- 列索引 = [c, r2, r1]

重塑后的 Series 只有列索引 (实际上是个转置的 Series)，有三层，第一层是特征，第二层是代号，第三层是地区。

#### 5.2 透视

数据源表通常只包含行和列，那么经常有重复值出现在各列下，因而导致源表不能传递有价值的信息。这时可用「透视」方法调整源表的布局用作更清晰的展示。

#####知识点

    本节「透视」得到的数据表和 Excel 里面的透视表 (pivot table) 是一样的。透视表是用来汇总其它表的数据：
    
    1. 首先把源表分组，将不同值当做行 (row)、列 (column) 和值 (value)
    1. 然后对各组内数据做汇总操作如排序、平均、累加、计数等
    
    这种动态将·「源表」得到想要「终表」的旋转 (pivoting) 过程，使透视表得以命名。

在 Pandas 里透视的方法有两种：

- 用 pivot 函数将「一张长表」变「多张宽表」，
- 用 melt 函数将「多张宽表」变「一张长表」，

本节使用的数据描述如下：

- 5 只股票：AAPL, JD, BABA, FB, GS
- 4 个交易日：从 2019-02-21 到 2019-02-26

```
data = pd.read_csv('Stock.csv', parse_dates=[0], dayfirst=True)
data
```

![e6e52i](http://img.kekepu.com/uPic/e6e52i.jpg)

从上表看出有 20 行 (5 × 4) 和 8 列，在 Date 和 Symbol 那两列下就有重复值，4 个日期和 5 个股票在 20 行中分别出现了 5 次和 4 次。

从长到宽 (pivot)

当我们做数据分析时，只关注不同股票在不同日期下的 Adj Close，那么可用 pivot 函数可将原始 data「透视」成一个新的 DataFrame，起名 close_price。在 pivot 函数中

- 将 index 设置成 ‘Date’
- 将 columns 设置成 ‘Symbol’
- 将 values 设置 ‘Adj Close’

close_price 实际上把 data[‘Date’] 和 data[‘Symbol’] 的唯一值当成支点(pivot 就是支点的意思) 创建一个 DataFrame，其中

- 行标签 = 2019-02-21, 2019-02-22, 2019-02-25, 2019-02-26
- 列标签 = AAPL, JD, BABA, FB, GS

在把 data[‘Adj Close’] 的值放在以如上的行标签和列标签创建的 close_price 来展示。

代码如下：


```
close_price = data.pivot( index='Date',
                          columns='Symbol',
                          values='Adj Close' )
close_price
```

![DukrkI](http://img.kekepu.com/uPic/DukrkI.jpg)

如果觉得 Adj Close 不够，还想加个 Volume 看看，那么就把 values 设置成 ['Adj Close', 'Volume']。这时支点还是 data[‘Date’] 和 data[‘Symbol’]，但是要透视的值增加到 data[['Adj Close', 'Volume']] 了。pivot 函数返回的是两个透视表。


```
data.pivot( index='Date',
            columns='Symbol',
            values=['Adj Close','Volume'] )
```

![tIMwmQ](http://img.kekepu.com/uPic/tIMwmQ.jpg)

如果不设置 values 参数，那么 pivot 函数返回的是六个透视表。(源表 data 有八列，两列当了支点，剩下六列用来透视)


```
all_pivot = data.pivot( index='Date', 
                        columns='Symbol' )
all_pivot
```

![FHXTWu](http://img.kekepu.com/uPic/FHXTWu.jpg)

再继续观察下，all_pivot 实际上是个多层 DataFrame (有多层 columns)。假设我们要获取 2019-02-25 和 2019-02-26 两天的 BABA 和 FB 的开盘价，用以下「多层索引和切片」的方法。


```
all_pivot['Open'].iloc[2:,1:3]
```

![Iqzwtb](http://img.kekepu.com/uPic/Iqzwtb.jpg)

**从宽到长 (melt)**


pivot 逆反操作是 melt。

- 前者将「一张长表」变成「多张宽表」
- 后者将「多张宽表」变成「一张长表」

具体来说，函数 melt 实际是将「源表」转化成 id-variable 类型的 DataFrame，下例将

- Date 和 Symbol 列当成 id
- 其他列 Open, High, Low, Close, Adj Close 和 Volume 当成 variable，而它们对应的值当成 value

代码如下：


```
melted_data = pd.melt( data, id_vars=['Date','Symbol'] )
melted_data.head(5).append(melted_data.tail(5))
```

![lc4WCf](http://img.kekepu.com/uPic/lc4WCf.jpg)

新生成的 DataFrame 有 120 行 (4 × 5 × 6)

- 4 = data['Date'] 有 4 个日期
- 5 = data['Symbol'] 有 5 只股票
- 6 = Open, High, Low, Close, Adj Close 和 Volume 这 6 个变量

在新表 melted_data 中

- 在参数 id_vars 设置的 Date 和 Symbol 还保持为 columns
- 此外还多出两个 columns，一个叫 variable，一个叫 value

    - variable 列下的值为 Open, High, Low, Close, Adj Close 和 Volume
    - value 列下的值为前者在「源表 data」中的值

函数 melt 可以生成一张含有多个 id 的长表，然后可在 id 上筛选出我们想要的信息，比如


```
melted_data[ lambda x: (x.Date=='25/02/2019') 
                     & ((x.Symbol=='BABA')|(x.Symbol=='FB')) ]
```

![NIgXqR](http://img.kekepu.com/uPic/NIgXqR.jpg)

在 melted_data 上使用**调用函数** (callable function) 做索引，我们得到了在 2019-02-25 那天 BABA 和 FB 的信息。

### 数据表的分组和整合

DataFrame 中的数据可以根据某些规则分组，然后在每组的数据上计算出不同统计量。这种操作称之为 split-apply-combine

#### 6.1 数据准备

本节使用的数据描述如下：

- 5 只股票：AAPL, JD, BABA, FB, GS
- 1 年时期：从 2018-02-26 到 2019-02-26

```
data = pd.read_csv('1Y Stock Data.csv', parse_dates=[0], dayfirst=True)
data.head(3).append(data.tail(3))
```

![9qq3Oh](http://img.kekepu.com/uPic/9qq3Oh.jpg)

我们目前只对 Adj Close 感兴趣，而且想知道在哪一年份或哪一月份每支股票的 Adj Close 是多少。因此我们需要做两件事：

只保留 'Date', 'Symbol' 和 ‘Adj Close‘
从 ‘Date’ 中获取 ‘Year’ 和 ‘Month’ 的信息并插入表中

将处理过后的数据存在 data1 中。


```
data1 = data[['Date', 'Symbol', 'Adj Close']]
data1.insert( 1, 'Year', pd.DatetimeIndex(data1['Date']).year )
data1.insert( 2, 'Month', pd.DatetimeIndex(data1['Date']).month )
data1.head(3).append(data1.tail(3))
```

![LQeh3U](http://img.kekepu.com/uPic/LQeh3U.jpg)

#### 6.2 分组 (grouping)

用某一特定标签 (label) 将数据 (data) 分组的语法如下：

    data.groupBy( label )

##### 单标签分组

首先我们按 Symbol 来分组：

```
grouped = data1.groupby('Symbol')
grouped
```


```
<pandas.core.groupby.groupby.DataFrameGroupBy
object at 0x7fbbc7248d68>
```

又要提起那句说了无数遍的话「万物皆对象」了。这个 grouped 也不例外，当你对如果使用某个对象感到迷茫时，用 dir() 来查看它的「属性」和「内置方法」。以下几个属性和方法是我们感兴趣的：

- ngroups: 组的个数 (int)
- size(): 每组元素的个数 (Series)
- groups: 每组元素在原 DataFrame 中的索引信息 (dict)
- get_groups(label): 标签 label 对应的数据 (DataFrame)

下面看看这些属性和方法的产出结果。

数据里有 5 只股票，因此有 5 组。


```
grouped.ngroups
```

```
5
```

一年有 252 个交易日，因此每只股票含 252 条信息。


```
grouped.size()
```


```
Symbol
AAPL 252
BABA 252
FB 252
GS 252
JD 252
dtype: int64
```

苹果股票 (AAPL) 的索引从 0 到 251，...，一直到高盛股票 (GS) 的索引从 1008 到 1259。


```
grouped.groups
```


![COuAEk](http://img.kekepu.com/uPic/COuAEk.jpg)

查查 'GS' 组里的数据的前五行。


```
grouped.get_group('GS').head()
```

![l2eL53](http://img.kekepu.com/uPic/l2eL53.jpg)

接下来定义个 print_groups 函数便于打印组的名字和前五行信息。


```
def print_groups( group_obj ):
    for name, group in group_obj:
        print( name )
        print( group.head() )
```



用这个函数来调用 grouped (上面用 groupBy 得到的对象）


```
print_groups( grouped )
```

![QGeNbl](http://img.kekepu.com/uPic/QGeNbl.jpg)

这个 print_groups 函数在下面也多次被用到。

##### 多标签分组

groupBy 函数除了支持单标签分组，也支持多标签分组 (将标签放入一个列表中)。


```
grouped2 = data1.groupby(['Symbol', 'Year', 'Month'])
print_groups( grouped2 )
```

![PYPFat](http://img.kekepu.com/uPic/PYPFat.jpg)

![MBSKV8](http://img.kekepu.com/uPic/MBSKV8.jpg)

不难看出在每组左上方，有一个 (Symbol, Year, Month) 元组型的标识：

- 第一组：(‘AAPL’, 2018, 2)
- 最后一组：(‘JD’, 2019, 2)

还记得的重设索引 (set_index) 的操作么？


```
data2 = data1.set_index(['Symbol', 'Year', 'Month'])
data2.head().append(data2.tail())
```

![c9Kb1k](http://img.kekepu.com/uPic/c9Kb1k.jpg)

对 data1 重设索引之后，产出是一个有 multi-index 的 DataFrame，记做 data2。由于有多层索引，这时我们根据索引的 level 来分组，下面 level = 1 就是对第一层 (Year) 进行分组。


```
grouped3 = data2.groupby(level=1)
print_groups( grouped3 )
```

![Kn1cWM](http://img.kekepu.com/uPic/Kn1cWM.jpg)

注意每组**左上方**的标识是  Year。

多层索引中的任意个数的索引也可以用来分组，下面 level = [0,2] 就是对第零层 (Symbol) 和第二层 (Month) 进行分组。


```
grouped4 = data2.groupby(level=[0, 2])
print_groups( grouped4 )
```

![WCmBXk](http://img.kekepu.com/uPic/WCmBXk.jpg)

![fcQxXH](http://img.kekepu.com/uPic/fcQxXH.jpg)

注意每组左上方的标识是 (Symbol, Month)。

#### 6.3 整合 (aggregating)

做完分组之后 so what？当然是在每组做点数据分析再整合啦。

一个最简单的例子就是上节提到的 size() 函数，用 grouped 对象 (上面根据 Symbol 分组得到的) 来举例。

```
grouped.size()
```

```
Symbol
AAPL 252
BABA 252
FB 252
GS 252
JD 252
dtype: int64
```

一个更实际的例子是用 mean() 函数计算每个 Symbol 下 1 年时期的股价均值。在获取任意信息就用 DataFrame 的索引或切片那一套方法。


```
grouped.mean()
```

![TFJohc](http://img.kekepu.com/uPic/TFJohc.jpg)

除了上述方法，整合还可以用内置函数 aggregate() 或 agg() 作用到「组对象」上。用 grouped4 对象 (上面根据 Symbol, Year, Month 分组得到的) 来举例。


```
result = grouped4.agg( np.mean )
result.head().append(result.tail())
```

![4ynBBL](http://img.kekepu.com/uPic/4ynBBL.jpg)

函数 agg() 其实是一个高阶函数里面的参数可以是另外一个函数，比如上例的 np.mean。上面代码对每只股票在每年每个月上求均值。

那么参数可以是另外一组函数么？可以的！


```
result = grouped4.agg( [np.mean, np.std] )
result.head().append(result.tail())
```

![weahpH](http://img.kekepu.com/uPic/weahpH.jpg)

将 np.mean 和 np.std 放进列表中，当成是高阶函数 agg() 的参数。上面代码对每只股票在每年每个月上求均值和标准差。

既然 agg() 是高阶函数，参数当然也可以是匿名函数 (lambda 函数)，下面我们定义一个对 grouped 里面每个标签下求最大值和最小值，再求差。注意 lambda 函数里面的 x 就是 grouped。


```
result = grouped.agg( lambda x: np.max(x)-np.min(x) )
result.head().append(result.tail())
```

![zd1W8V](http://img.kekepu.com/uPic/zd1W8V.jpg)

上面代码对每只股票在 Date, Year, Month 和 Adj Close 上求「最大值」和「最小值」的差。真正有价值的信息在 Adj Close 那一栏，但我们来验证一下其他几栏。

- Date: 365 days，合理，一年数据
- Year: 1，合理，2019 年和 2018 年
- Month: 11，合理，12 月和 1 月。


#### 6.4 split-apply-combine

前几节做的事情的实质就是一个 split-apply-combine 的过程，如下图所示：

![1teLhF](http://img.kekepu.com/uPic/1teLhF.jpg)

该 split-apply-combine 过程有三步：

1. 根据 key 来 split 成 n 组
1. 将函数 apply 到每个组
1. 把 n 组的结果 combine 起来

在看具体例子之前，我们先定一个 top 函数，返回 DataFrame 某一栏中 n 个最大值。


```
def top( df, n=5, column='Volume' ):
    return df.sort_values(by=column)[-n:]
```

将 top 函数用到最原始的数据 (从 csv 中读取出来的) 上。

```
top( data )
```

![urWYaU](http://img.kekepu.com/uPic/urWYaU.jpg)

从上表可看出，在 Volume 栏取 5 个最大值。

##### Apply 函数
在 split-apply-combine 过程中，apply 是核心。Python 本身有高阶函数 apply() 来实现它，既然是高阶函数，参数可以是另外的函数了，比如刚定义好的 top()。

将 top() 函数 apply 到按 Symbol 分的每个组上，按每个 Symbol 打印出来了 Volume 栏下的 5 个最大值。


```
data.groupby('Symbol').apply(top)
```

![tM0EIW](http://img.kekepu.com/uPic/tM0EIW.jpg)

上面在使用 top() 时，对于 n 和 column 我们都只用的默认值 5 和 'Volumn'。如果用自己设定的值 n = 1, column = 'Adj Close'，写法如下(下面使用在元数据上插入 Year 和 Month 的数据)：


```
data1.groupby(['Symbol','Year']).apply(top, n=1, column='Adj Close')
```

![J6cyF9](http://img.kekepu.com/uPic/J6cyF9.jpg)

按每个 Symbol 和 Year 打印出来了 Adj Close 栏下的最大值。

### 总结

Pandas 里面的数据结构是多维数据表，细化为一维的 Series，二维的 DataFrame，三维的 Panel。

    多维数据表 = 多维数组 + 描述


其中

- Series = 1darray + index
- DataFrame = 2darray + index + columns
- Panel = 3darray + index + columns + item

pd 多维数据表和 np 多维数组之间的类比关系如下图所示。

![Qnzeq3](http://img.kekepu.com/uPic/Qnzeq3.jpg)

**【创建数据表】**创建 Series, DataFrame, Panel 用下面语句

- pd.Series(x, index=idx)
- pd.DataFrame(x, index=idx, columns=col)
- pd.Panel(x, item=itm, major_axis=n1, minor_axis=n2)

DataFrame 由多个 Series 组成，Panel 有多个 DataFrame 组成。Series 非常类似于一维的 DataFrame，Panel 未来会被废掉，因此学 Pandas 把注意力放在 DataFrame 上即可。

**【索引和切片数据表】**在索引或切片 DataFrame，有很多种方法。最好记的而不易出错的是用基于位置的 at 和 loc，和基于标签的 iat 和 iloc，具体来说，索引用 at 和 iat，切片用 loc 和 iloc。带 i 的基于位置，不带 i 的基于标签。

用 MultiIndex 可以创建多层索引的对象，获取 DataFrame df 的信息可用

- df.loc[1st].loc[2nd]
- df.loc[1st].iloc[2nd]
- df.iloc[1st].loc[2nd]
- df.iloc[1st].iloc[2nd]

要调换 level 可用

- df.index.swaplevel(0,1)
- df.columns.swaplevel(0,1)

要设置和重设 index 可用

- df.set_index( columns )
- df.reset_index

**【合并数据表】** 用 merge 函数按数据表的共有列进行左/右/内/外合并。

![vlLEzQ](http://img.kekepu.com/uPic/vlLEzQ.jpg)

【连接数据表】用 concat 函数对 Series 和 DataFrame 沿着不同轴连接。

【重塑数据表】用 stack 函数将「列索引」变成「行索引」，用 unstack 函数将「行索引」变成「列索引」。它们只是改变数据表的布局和展示方式而已。
 
![eDp66D](http://img.kekepu.com/uPic/eDp66D.jpg)

![ojKysH](http://img.kekepu.com/uPic/ojKysH.jpg)

![JlrQJW](http://img.kekepu.com/uPic/JlrQJW.jpg)

【透视数据表】用 pivot 函数将「一张长表」变成「多张宽表」，用 melt 函数将「多张宽表」变成「一张长表」。它们只是改变数据表的布局和展示方式而已。

![k5UEPK](http://img.kekepu.com/uPic/k5UEPK.jpg)

![1MEZmp](http://img.kekepu.com/uPic/1MEZmp.jpg)

【分组数据表】用 groupBy 函数按不同「列索引」下的值分组。一个「列索引」或多个「列索引」就可以。

【整合数据表】用 agg 函数对每个组做整合而计算统计量。

【split-apply-combine】用 apply 函数做数据分析时美滋滋。

![fPakDK](http://img.kekepu.com/uPic/fPakDK.jpg)