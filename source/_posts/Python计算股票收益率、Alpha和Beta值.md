---
title: Python计算股票收益率、Alpha和Beta值
date: 2019-10-08 21:02:40
categories: 量化交易
tags: 代码块
toc: true
---

使用Python计算股票的收益率，重点展示如何利用Python对日收益率数据向月、年收益率转换，然后演示个股Alpha和Beta值的计算。

### 收益率
#### 单期收益率
单期（一个时期）的收益率计算方法如下：

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g7r5o3ph4gj309u02owef.jpg)

其中，r 是收益率，pt 是时间 t 时的资产价格，p0 是初始时间的资产价格。我们利用 Python 程序来实现这个过程：
```
rate_return = 102.0/100 - 1
print(rate_return)
```
假设我们买了 100 美元的股票，半年后它涨到了 102 美元。一年后，价格又涨到了 104 美元。那么我们应该如何计算它的总回报呢？首先，我们可以把它看做是一个时期的投资回报，即股票直接从 100 美元涨到了 104 美元，那么我们的回报率可以这样计算：

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g7r5qpk26hj309602i748.jpg)

或者，我们也可以把这个当做两个时期的回报，即股票先从 100 美元涨到 102 美元，然后再从 102 美元涨到 104 美元，那么我们的回报率可以这样计算：

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g7r5ru8pcqj30n202c3yn.jpg)

在这里，我们对一年的回报进行了两次计算，这种计算被称为半年复合。那么季度复合呢？让我们假设一下，每个季度末的股票价格分别为 p1，p2，p3和p4。

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g7r5tognrlj30ma028q30.jpg)

我们在此计算的收益率称为累积回报或者总回报。它衡量一段时间内该资产的总回报。

现在考虑以下一个问题：我们有两个策略，策略A和策略B。我们将策略A运行了一年，累积回报率是20%。而我们运行策略B三年，累积回报率是65%。那么你觉得哪种策略的回报率更高呢？我们常用的比较方法是将所有收益转换为年复合收益率，无论每种策略的投资期限如何，我们都能通过这种方法来比较两种方法的收益率。我们通过计算获得策略 B 的年复合收益率如下：
![](https://tva1.sinaimg.cn/large/006y8mN6gy1g7r5wswmyhj30ck04qglr.jpg)

所以，策略A有更高的年复合收益率（compounding annual return）。

#### 对数收益率
年复合收益率，这是一种有效的回报率。但是，你也可以将其看做是一种 “假设回报”，因为策略 B 在三年的测试过程中，不可能每年的回报率都是 18.167% 。但是，我们可以假设策略B每年的回报率是 18.167%，所以它能达到三年的累积回报率是 65%。正如我们之前提到的，如果我们假设一个策略是季度复利的，那么季度有效收益率和年回报之间的关系为：

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g7r62m11jpj308a024t8m.jpg)

更一般的说，如果一年内复合的时间是 n，而年回报率是 r，那么我们可以得到如下的关系式：

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g7r63tly11j308802a3yf.jpg)

现在想象一下股市，我们的资产是每秒都在变化，甚至每毫秒都是在变化的。如果符合的时间或 n 接近无限大，则称为连续复合。计算公式如下：

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g7r657kgftj30ac028weg.jpg)

从上面的方程中，我们知道如果我们假设复合收益是连续的：

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g7r677nbabj307y0280sn.jpg)

我们对等式两边都取自然对数 ln ，则：

![](https://tva1.sinaimg.cn/large/006y8mN6gy1g7r67ki419j30au022gll.jpg)

在这里我们获取了对数收益率，或者称为连续复合收益。这在计算回报时经常被使用，因为一旦我们采用资产价格的对数，我们就可以通过简单的减法来计算对数回报。

```
# 先引入后面可能用到的包
import pandas as pd
import numpy as np
from scipy import stats
import tushare as ts
import matplotlib.pyplot as plt

# 正常显示画图时出现的中文和负号
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False
```

#### 收益率转化
对日期进行处理，分别将日对数收益率转化为月和年收益率。主要有三个步骤：

1.估计股票每日对数收益率；  
2.加总对数收益率到每月（年）；  
3.将月（年）收益率转化为百分比收益率  

```
stock = 'sh'
df = ts.get_k_data(stock, start='2010-01-03')
# 使用tushare中的get_k_data()得到的数据框索引是顺序数字，而不是日期序列，因此，为分析方面，需要进行变换，即使用“date”作为索引。
df.set_index(['date'], inplace=True)
```
步骤一：计算对数收益率
```
# 计算的时候第一个数成为缺失值，删掉
lograte = np.log(df.close/df.close.shift(1))
```

步骤二：加总对数收益率到每日
```
month = []
index = lograte.index

for i in range(0, np.size(lograte)):
    month.append(index[i][:7])

y = pd.DataFrame(lograte.values, index=month, columns=["月收益率"])
```

步骤三：将日收益率转化为月收益率
```
ret_monthly = y.groupby(y.index).sum()
print(ret_monthly)

#          月收益率
# 2019-05 -0.060128
# 2019-06  0.027285
# 2019-07 -0.015689
# 2019-08 -0.015904
# 2019-09  0.033933
```

**将日收益率转化为年收益率**
```
stock = '601318'
df = ts.get_k_data(code=stock, ktype='D', autype='qfq', start='2007-03-01')

df.set_index(['date'], inplace=True)

# 计算的时候第一个数成为缺失值，删掉
lograte = np.log(df.close/df.close.shift(1))[1:]

year = []
index = lograte.index

for i in range(0, np.size(lograte)):
    year.append(index[i][:4])

y = pd.DataFrame(lograte.values, index=year, columns=["年收益率"])

ret_annual = np.exp(y.groupby(y.index).sum())-1
print(ret_annual)
```