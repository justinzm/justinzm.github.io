---
title: kNN k近邻算法
date: 2019-10-01 15:02:18
categories: 机器学习
tags: 机器学习
toc: true
---

kNN k近邻算法是思想极度简单；效果好；应用数学知识少

## 练习数据
```
import numpy as np
import matplotlib.pyplot as plt

raw_data_X = [
    [3.393533211, 2.331273381],
    [3.110073483, 1.781539638],
    [1.343808831, 3.368360954],
    [3.582294042, 4.679179110],
    [2.280362439, 2.866990263],
    [7.423436942, 4.696522875],
    [5.745051997, 3.533989803],
    [9.172168622, 2.511101045],
    [7.792783481, 3.424088941],
    [7.939820817, 0.791637231]
]

raw_data_y = [0, 0, 0, 0, 0, 1, 1, 1, 1, 1]

X_train = np.array(raw_data_X)
y_train = np.array(raw_data_y)

# 需要预测的点
x = np.array([8.093607318, 3.365731514])

plt.scatter(X_train[y_train == 0, 0], X_train[y_train == 0, 1], color='g')
plt.scatter(X_train[y_train == 1, 0], X_train[y_train == 1, 1], color='r')
plt.scatter(x[0], x[1], color='b')
plt.show()
```

## kNN的过程
#### 欧拉距离
![](http://ww1.sinaimg.cn/large/405e87d9gy1g5nrlfcp9lj218q0pi41d.jpg)

![](http://ww1.sinaimg.cn/large/405e87d9gy1g5nrn9fc9ej21820pgtat.jpg)

实现欧拉距离；获得测试点与所有点的距离
```
from math import sqrt
distances = []
for x_train in X_train:
    d = sqrt(np.sum((x_train - x)**2))
    distances.append(d)
```

```
distances = [sqrt(np.sum((x_train - x)**2)) for x_train in X_train]
```

对数据进行排序，返回数组的索引

```
nearest = np.argsort(distances)
```

设置最近的k个点 获取最近点的y的数据值

```
k = 6
topK_y = [y_train[i] for i in nearest[:k]]
```

把数组中的值进行归纳 Counter；获取最多的值

```
from collections import Counter
votes = Counter(topK_y)
predict_y = votes.most_common(1)[0][0]
```

## kNN 算法封装
```
import numpy as np
from math import sqrt
from collections import Counter

def kNN_classify(k, X_train, y_train, x):
    """
    kNN算法函数
    :param k: 
    :param X_train: 训练数据集
    :param y_train: 训练数据集
    :param x:   预测集
    :return: 输出预测结果
    """
    distances = [sqrt(np.sum((x_train - x)**2)) for x_train in X_train]
    nearest = np.argsort(distances)

    topK_y = [y_train[i] for i in nearest[:k]]
    votes = Counter(topK_y)

    return votes.most_common(1)[0][0]
    

raw_data_X = [
    [3.393533211, 2.331273381],
    [3.110073483, 1.781539638],
    [1.343808831, 3.368360954],
    [3.582294042, 4.679179110],
    [2.280362439, 2.866990263],
    [7.423436942, 4.696522875],
    [5.745051997, 3.533989803],
    [9.172168622, 2.511101045],
    [7.792783481, 3.424088941],
    [7.939820817, 0.791637231]
]

raw_data_y = [0, 0, 0, 0, 0, 1, 1, 1, 1, 1]

# 二维矩阵\向量
X_train = np.array(raw_data_X)
y_train = np.array(raw_data_y)

# 需要预测的点
x = np.array([8.093607318, 3.365731514])

k = 6

predict_y = kNN_classify(k, X_train, y_train, x)
```

![](http://ww1.sinaimg.cn/mw690/405e87d9gy1g5osg2si1lj20t80bl41g.jpg)


## 使用scikit-learn中的kNN
https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KNeighborsClassifier.html

```
from sklearn.neighbors import KNeighborsClassifier

# 1、生成对象，参数n_neighbors 对比参数数量
kNN_classifier = KNeighborsClassifier(n_neighbors=k)

# 2、fit进行拟合
kNN_classifier.fit(X_train, y_train)

# 一位数组转换为矩阵
X_predict = x.reshape(1, -1)

# 3、获得预测值
predict_y = kNN_classifier.predict(X_predict)
```

## 重复构造自定义kNN类
```
import numpy as np
from math import sqrt
from collections import Counter

class KNNClassifier:
    def __init__(self, k):
        """
        初始化kNN分类器
        """
        self.k = k
        self._X_train = None
        self._y_train = None

    def fit(self, X_train, y_train):
        """
        根据训练集X_train和y_train训练kNN分类器
        """
        self._X_train = X_train
        self._y_train = y_train
        return self

    def predict(self, X_predict):
        """
        给定待预测数据集X_predict，返回表示X_predict的结果向量
        """
        y_predict = [self._predict(x) for x in X_predict]
        return np.array(y_predict)

    def _predict(self, x):
        """
        给定单个待预测数据x，返回x的预测结果值
        """
        distances = [sqrt(np.sum((x_train - x) ** 2)) for x_train in self._X_train]
        nearest = np.argsort(distances)

        topK_y = [self._y_train[i] for i in nearest[:self.k]]
        votes = Counter(topK_y)

        return votes.most_common(1)[0][0]

    def score(self, X_test, y_test):
        """
        计算X_test和y_test之间的准确率
        """
        y_predict = self.predict(X_test)
        return sum(y_test == y_predict) / len(y_predict)

    def __repr__(self):
        return "kNN(k=%d)" % self.k
```
