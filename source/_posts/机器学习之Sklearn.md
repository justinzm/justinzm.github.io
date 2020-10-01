---
title: 机器学习之Sklearn
date: 2019-12-14 21:10:17
categories: 机器学习
tags: Sklearn
toc: true
---

Sklearn (全称 Scikit-Learn) 是基于 Python 语言的机器学习工具。它建立在 NumPy, SciPy, Pandas 和 Matplotlib 之上，里面的 API 的设计非常好，所有对象的接口简单，很适合新手上路。

在 Sklearn 里面有六大任务模块：分别是分类、回归、聚类、降维、模型选择和预处理，如下图从其官网的截屏。

![9NRoXM](http://img.kekepu.com/uPic/9NRoXM.jpg)

要使用上述六大模块的方法，可以用以下的伪代码，注意 import 后面我用的都是一些通用名称，如 SomeClassifier, SomeRegressor, SomeModel，具体化的名称由具体问题而定，比如

- SomeClassifier = RandomForestClassifier
- SomeRegressor = LinearRegression
- SomeModel = KMeans, PCA
- SomeModel = GridSearchCV, OneHotEncoder

上面具体化的例子分别是随机森林分类器、线性回归器、K 均值聚类、主成分分析、网格追踪法、独热编码。

##### 分类 (Classification)


```
from sklearn import SomeClassifier
from sklearn.linear_model import SomeClassifier
from sklearn.ensemble import SomeClassifier
```


##### 回归 (Regression)


```
from sklearn import SomeRegressor
from sklearn.linear_model import SomeRegressor
from sklearn.ensemble import SomeRegressor
```


##### 聚类 (Clustering)

```
from sklearn.cluster import SomeModel
```

##### 降维 (Dimensionality Reduction)

```
from sklearn.decomposition import SomeModel
```

##### 模型选择 (Model Selection)

```
from sklearn.model_selection import SomeModel
```

##### 预处理 (Preprocessing)

```
from sklearn.preprocessing import SomeModel
```

SomeClassifier, SomeRegressor, SomeModel 其实都叫做估计器 (estimator)，就像 Python 里「万物皆对象」那样，Sklearn 里「**万物皆估计器**」。

此外，Sklearn 里面还有很多自带数据集供，引入它们的伪代码如下。

##### 数据集 (Dataset)

```
from sklearn.datasets import SomeData
```

本贴我们用以下思路来讲解：

- 第一章介绍机器学习，从定义出发引出机器学习四要素：**数据、任务、性能度量和模型**。加这一章的原因是不把机器学习相关概念弄清楚之后很难完全弄明白 Sklearn。
- 第二章介绍 Sklearn，从其 API 设计原理出发分析其五大特点：**一致性、可检验、标准类、可组合和默认值**。最后再分析 Sklearn 里面自带数据以及储存格式。
- 第三章介绍 Sklearn 里面的三大核心 API，包括**估计器、预测器和转换器**。这一章的内容最重要，几乎所有模型都会用到这三大 API。
- 第四章介绍 Sklearn 里面的高级 API，即**元估计器**，有可以大大简化代码量的流水线 (Pipeline 估计器)，有集成模型 (Ensemble 估计器)、有多类别-多标签-多输出分类模型 (Multiclass 和 Multioutput 估计器) 和模型选择工具 (Model Selection 估计器)。

### 机器学习简介

#### 定义和组成元素

什么是机器学习？字面上来讲就是 (人用) 计算机来学习。谈起机器学习就一定要提起汤姆米切尔 (Tom M.Mitchell)，就像谈起音乐就会提起贝多芬，谈起篮球就会提起迈克尔乔丹，谈起电影就会提起莱昂纳多迪卡普里奥。米切尔对机器学习定义的原话是：

    A computer program is said to learn from experience E with respect to some class of tasks  T and performance measure P if its performance at  tasks in T, as measured by P, improves with experience E.

整段英文有点抽象难懂对吗？首先注意到两个词 computer program 和 learn，翻译成中文就是机器 (计算机程序) 和学习，再把上面英译中：

    假设用性能度量 P 来评估机器在某类任务 T 的性能，若该机器通利用经验 E 在任务 T 中改善其性能 P，那么可以说机器对经验 E 进行了学习。

在该定义中，除了核心词机器和学习，还有关键词经验 E，性能度量 P 和任务 T。在计算机系统中，通常经验 E 是以数据 D 的形式存在，而机器学习就是给定不同的任务 T 从数据中产生模型 M，模型 M 的好坏就用性能度量 P 来评估。

由上述机器学习的定义可知机器学习包含四个元素

- 数据 (Data)
- 任务 (Task)
- 性能度量 (Quality Metric)
- 模型 (Model)

![lxUBeP](http://img.kekepu.com/uPic/lxUBeP.jpg)

下面四小节分别介绍数据、任务、性能度量和模型。

#### 数据

数据 (data) 是经验的另一种说法，也是信息的载体。数据可分为

- 结构化数据和非结构化数据 (按数据具体类型划分)
- 原始数据和加工数据 (按数据表达形式划分)
- 样本内数据和样本外数据 (按数据统计性质划分)

##### 结构化和非结构化

结构化数据 (structured data) 是由二维表结构来逻辑表达和实现的数据。非结构化数据是没有预定义的数据，不便用数据库二维表来表现的数据。

###### 非结构化数据

非结构化数据包括图片，文字，语音和视屏等如下图。

![9BXoLs](http://img.kekepu.com/uPic/9BXoLs.jpg)

对于以上的非结构数据，相关应用实例有

- 深度学习的卷积神经网络 (convolutional neural network, CNN) 对图像数据做人脸识别或物体分类
- 深度学习的循环神经网络 (recurrent neural network, RNN) 对语音数据做语音识别或机器对话，对文字数据做文本生成或阅读理解
- 增强学习的阿尔法狗 (AlphaGo) 对棋谱数据学习无数遍最终打败了围棋世界冠军李世石和柯洁

计算机追根到底还是只能最有效率的处理数值型的结构化数据，如何从原始数据加工成计算机可应用的数据会在后面讲明。

###### 结构化数据

机器学习模型主要使用的是结构化数据，即二维的数据表。非结构化数据可以转换成结构化数据，比如把

- 图像类数据里像素张量重塑成一维数组
- 文本类数据用独热编码转成二维数组

对于结构化数据，我们用勒布朗詹姆斯 (Lebron James) 四场比赛的数据举例。

![Ztm9nF](http://img.kekepu.com/uPic/Ztm9nF.jpg)


下面术语大家在深入了解机器学习前一定要弄清楚：

- 每行的记录 (这是一场比赛詹姆斯的个人统计) ，称为一个**示例 (instance)**
- 反映对象在某方面的性质，例如得分，篮板，助攻，称为**特征 (feature) 或输入 (input)**
- 特征上的取值，例如「示例 1」对应的 27, 10, 12 称为**特征值 (feature value)**
- 关于示例结果的信息，例如赢，称为**标签 (label) 或输出 (output)**
- 包含标签信息的示例，则称为**样例 (example)**，即样例 = (特征, 标签)
- 从数据中学得模型的过程称为**学习 (learning) 或训练 (training)**
- 在训练数据中，每个样例称为**训练样例 (training example)**，整个集合称为**训练集 (training set)**

##### 原始和加工
计算机处理数值型的结构型数据最有效率，但是现实世界到处出是原始数据，分为两类

- 非结构数据比如图片和文字型数据 (情况一)
- 结构型数据的分类型变量 (情况二)

###### 图像性数据

拿情况一的图片为例，通过特定函数 imread 将彩色图片用 RGB 像素表示出来，再按红绿蓝的顺序，将所有像素排成一个数值列向量 (column vector)，而计算机可以接受这样的输入。具体转换过程见下图。

![cHnZTW](http://img.kekepu.com/uPic/cHnZTW.jpg)

###### 文本型数据


推特 (twitter) 的每条推文 (tweet) 规定只能发 280 个字符。在编码推文时，将 280 个字符的序列用独热编码 (one-hot encoding) 到包含 128 个字符的 ASCII 表，如下所示。

![PkKSiH](http://img.kekepu.com/uPic/PkKSiH.jpg)

这样，每条推文都可以编码为 2 维张量形状 (280, 128)，比如一条 tweet 是 “I love python :)”，这句话映射到 ASCII 表变成：

![N0yZnd](http://img.kekepu.com/uPic/N0yZnd.jpg)

如果收集到 1 百万条推文，那么整个数据集的形状为 (1000000, 280, 128)。传统机器学习的对率回归可以来做情感分析。

###### 分类型变量

篮球比赛结果非输即赢，是一个二类 (binary class) 变量

![ggTLBd](http://img.kekepu.com/uPic/ggTLBd.jpg)

二类变量用「0-1编码」，比如比赛结果= {赢, 输} 表示成 y= [1 0 0 1]，1 代表赢，0 代表输。

而足球比赛结果是有赢、平、输三种，是一个多类 (multi-class) 变量。

![60V2LY](http://img.kekepu.com/uPic/60V2LY.jpg)

多类变量分别用 0, 1, 2 来表示，那么 y = [0 1 0 2]。但更常见的是用独热编码 (one-hot encoding)，即

![Knzhdo](http://img.kekepu.com/uPic/Knzhdo.jpg)

##### 样本内和样本外

在统计中，把研究对象的全体称为总体 (population)，而把组成总体的各个元素称为个体，把从总体中抽取的若干个体称为样本 (sample)。

![jeRRFB](http://img.kekepu.com/uPic/jeRRFB.jpg)

举个调查中国男性平均身高的例子：

- 全国的男性就是总体
- 每个男性是个体

普查所有男性金钱花费和时间成本太高，通常会抽取若干男性作为样本，计算样本里的男性平均身高作为总体里的所有男性平均身高的推理 (inference)。

统计学中做的事情就是用样本数据的统计 (statistics) 来推出总体数据的参数 (parameter)。样本数据也叫做样本内数据，除样本内数据之外的总体数据叫做样本外数据。

在机器学习中，样本内和样本外数据的定义稍微有些不同，如下图：

![ThUnLs](http://img.kekepu.com/uPic/ThUnLs.jpg)

**样本内数据**是用来训练模型的数据，也叫训练数据。它们是已知的，可计算统计的。

**样本外数据**是未来的没见过的新数据。它们是未知的，不可计算统计的。

机器学习在样本内数据训练模型用来预测：

- 样本内预测：根据训练模型对样本内数据进行预测，可与已知标签比较来评估模型表现
- 样本外预测：根据训练模型对样本外数据进行预测，不能与未知的标签比较

机器学习的难点就是如何用好的样本内预测来保证好的样本外预测，幸运的是我们有〖计算学习理论〗来保证它。

#### 任务

根据学习的任务模式 (训练数据是否有标签)，机器学习可分为四大类：

- 有监督学习 (有标签)
- 无监督学习 (无标签)
- 半监督学习 (有部分标签)
- 增强学习 (有评级标签)

**深度学习**只是一种方法，而不是任务模式，因此与上面四类不属于同一个维度，但是深度学习与它们可以叠加成：深度有监督学习、深度非监督学习、深度半监督学习和深度增强学习。迁移学习也是一种方法，也可以分类为有监督迁移学习、非监督迁移学习、半监督迁移学习和增强迁移学习。

下图画出机器学习各类之间的关系。

![Rx6KAH](http://img.kekepu.com/uPic/Rx6KAH.jpg)

由于 Sklearn 里面模型主要处理「有监督学习」和「无监督学习」两类，我们接下来也只讨论这两类。

##### 有监督学习

有监督学习 (supervised learning) 利用输入数据及其对应标签来训练模型。这种学习方法类似学生通过研究问题和参考答案来学习，在掌握问题和答案之间的对应关系后，学生可自己给出相似新问题的答案了。

在有监督学习中，数据 = (**特征，标签**)，而其主要任务是分类和回归。以上述詹姆斯的个人统计为例。

###### 分类

如果预测的是离散值 (discrete value)，例如比赛结果赢或输，此类学习任务称为分类 (classification)。

![Pd3R9c](http://img.kekepu.com/uPic/Pd3R9c.jpg)

###### 回归

如果预测的是连续值 (continuous value)，例如詹姆斯效率 65.1, 70.3 等等，此类学习任务称为回归 (regression)。

![L5SyyT](http://img.kekepu.com/uPic/L5SyyT.jpg)

##### 无监督学习

无监督学习 (unsupervised learning) 是找出输入数据的模式。比如，它可以根据电影的各种特征做聚类，用这种方法收集数据为电影推荐系统提供标签。此外无监督学习还可以降低数据的维度，它可以帮助我们更好的理解数据。

在无监督学习中，数据 = (**特征，**)。

###### 聚类

除了根据詹姆斯个人统计来预测骑士队输赢或者个人效率值外，我们还可以对该数据做聚类 (clustering)，即将训练集中的数据分成若干组，每组成为一个簇 (cluster)。

![4xkf68](http://img.kekepu.com/uPic/4xkf68.jpg)

假设聚类方法将数据聚成二个簇 A 和 B，如下图

![JyJEKN](http://img.kekepu.com/uPic/JyJEKN.jpg)

后来发现簇 A 代表赢，簇 B 代表输。聚类的用处就是可以找到一个潜在的原因来解释为什么样例 1 和 3 可以赢球。难道真的是只要詹姆斯三双就可以赢球？

###### 降维

注：下面对降维的理解不是那么严谨，只为了让小白对降维大概有个概念。

詹姆斯完整统计数据还有抢断、盖帽和犯规，但这些对预测比赛输赢、效率值都没什么用，因此可以通过降维的方法将其去除。

![ucO1IA](http://img.kekepu.com/uPic/ucO1IA.jpg)

#### 性能度量

回归和分类任务中最常见的误差函数以及一些有用的性能度量如下。

![K6gYLk](http://img.kekepu.com/uPic/K6gYLk.jpg)

回归任务的误差函数估量在数据集 D 上模型的连续型预测值 h(x) 与连续型真实值 y 的距离，h(x) 和 y 可以取任意实数。误差函数是一个非负实值函数，通常使用 ED[h] 来表示。图表展示如下。

![Wk40iH](http://img.kekepu.com/uPic/Wk40iH.jpg)

![I3W58A](http://img.kekepu.com/uPic/I3W58A.jpg)

分类任务的误差函数估量在数据集 D 上模型的离散型预测值 h(x) 与离散型真实值 y 的不一致程度，惯例是 y 和 h(x) 取±1，比如正类取 1 负类取 -1。图表展示如下。

![WQkgZT](http://img.kekepu.com/uPic/WQkgZT.jpg)

![baMnJA](http://img.kekepu.com/uPic/baMnJA.jpg)

除上述损失函数之外，分类任务还有很多其他有用的性能度量。

**错误率：**分类错误的样本数占样本总数的比例称为错误率 (error rate)，相应的分类正确的样本数占样本总数的比例称为精度 (accuracy)。在 10 个样本中有 2 个样本分类错误，则错误率为 20%，而精度为 80%。

**查准率和查全率：**错误率和精度虽然常用，但是不能满足所有任务需求。假定用训练好的模型预测骑士赢球，显然，错误率衡量了多少比赛实际是赢球但预测成输球。但是若我们关心的是“预测出的比赛中有多少是赢球”，或“赢球的比赛中有多少被预测出了”，那么错误率这个单一指标显然就不够用了，这时需要引进更为细分的性能度量，即查准率 (precision) 和查全率 (recall)。

其他概念比如混淆矩阵、ROC、AUC 我们再下帖的实例用到时再细讲。

#### 模型

有监督模型如下图所示：

![UMLsNX](http://img.kekepu.com/uPic/UMLsNX.jpg)

无监督模型包括各种聚类分析 (KMeans, DBSCAN)、主成分分析 (PCA)、独立成分分析 (ICA)、隐含狄利克雷分配 (LDA) 等等。


### Sklearn 数据

Sklearn 和之前讨论的 NumPy, SciPy, Pandas, Matplotlib 相似，就是一个处理特殊任务的包，Sklearn 就是处理机器学习 (有监督学习和无监督学习) 的包，更精确的说，它里面有六个任务模块和一个数据引入模块：

- 有监督学习的**分类任务**
- 有监督学习的**回归任务**
- 无监督学习的**聚类任务**
- 无监督学习的**降维任务**
- **数据预处理任务**
- **模型选择任务**
- 数据引入

本节就来看看 Sklearn 里数据格式和自带数据集。

#### 数据格式

在 Sklean 里，模型能即用的数据有两种形式：

1. Numpy 二维数组 (ndarray) 的稠密数据 (dense data)，通常都是这种格式。
1. SciPy 矩阵 (scipy.sparse.matrix) 的稀疏数据 (sparse data)，比如文本分析每个单词 (字典有 100000 个词) 做独热编码得到矩阵有很多 0，这时用 ndarray 就不合适了，太耗内存。

上述数据在机器学习中通常用符号 X 表示，是模型自变量。它的大小 = [样本数, 特征数]，图下图所示。该房屋数据有 21000 条包括平方英尺，卧室数，楼层，日期，翻新年份等等 21 栏。该数据形状为 [21000, 21]

![z8o4Qx](http://img.kekepu.com/uPic/z8o4Qx.jpg)

有监督学习除了需要特征 X 还需要标签 y，而 y 通常就是 Numpy 一维数组，无监督学习没有 y。

#### 自带数据集

Sklearn 里面有很多自带数据集供用户使用。

##### 特例描述

![tYvPFv](http://img.kekepu.com/uPic/tYvPFv.jpg)

数据集包括 150 条鸢尾花的四个特征 (萼片长/宽和花瓣长/宽) 和三个类别。从 Sklearn 里面的 datasets 模块中引入，代码如下：


```
from sklearn.datasets import load_iris
iris = load_iris()
```

数据是以「字典」格式存储的，看看 iris 的键有哪些。


```
iris.keys()

dict_keys(['data', 'target',
           'target_names', 'DESCR',
           'feature_names', 'filename'])
```

键里面的名称解释如下：

- data：特征值 (数组)
- target：标签值 (数组)
- target_names：标签 (列表)
- DESCR：数据集描述
- feature_names：特征 (列表)
- filename：iris.csv 文件路径

具体感受一下 iris 数据中特征的大小、名称和前五个示例。


```
n_samples, n_features = iris.data.shape
print((n_samples, n_features))
print(iris.feature_names)
iris.data[0:5]

(150, 4)

['sepal length (cm)', 'sepal width (cm)',
 'petal length (cm)', 'petal width (cm)']

array([[5.1, 3.5, 1.4, 0.2],
       [4.9, 3. , 1.4, 0.2],
       [4.7, 3.2, 1.3, 0.2],
       [4.6, 3.1, 1.5, 0.2],
       [5. , 3.6, 1.4, 0.2]])
```



150 个样本，4 个特征，没毛病！再感受一下标签的大小、名称和全部示例。


```
print(iris.target.shape)
print(iris.target_names)
iris.target

(150,)

['setosa' 'versicolor' 'virginica']

array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
       0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
       0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
       1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
       1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2,
       2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2,
       2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2])
```

150 个标签，3 类别 (分别用 0, 1, 2 数值来代表 setosa, versicolor, virginica)。

用 Pandas 的 DataFrame (将 X 和 y 合并) 和 Seaborn 的 pairplot (看每个特征之间的关系) 来用表格和图来展示一下数据集的内容。

###### Pandas DataFrame

```
iris_data = pd.DataFrame( iris.data, 
                          columns=iris.feature_names )
iris_data['species'] = iris.target_names[iris.target]
iris_data.head(3).append(iris_data.tail(3))
```

###### Seaborn Pairplot


```
sns.pairplot( iris_data, hue='species', palette='husl' );
```

![biWRkH](http://img.kekepu.com/uPic/biWRkH.jpg)

##### 正规引入

看完鸢尾花的 iris 数据展示后，现在来看看 Sklearn 三种引入数据形式。

- 打包好的数据：对于小数据集，用 sklearn.datasets.load_*
- 分流下载数据：对于大数据集，用 sklearn.datasets.fetch_*
- 随机创建数据：为了快速展示，用 sklearn.datasets.make_*

上面这个星号 * 是什么意思，指的是具体文件名，敲完

- datasets.load_<TAB>
- datasets.fetch_<TAB>
- datasets.make_<TAB>

点击键盘上的 <TAB> 键就可以看到很多完整的文件名，看下面动图就明白了。

![uub7kl](http://img.kekepu.com/uPic/uub7kl.jpg)

Load 一个数字小数据集 digits?


```
digits = datasets.load_digits()
digits.keys()
dict_keys(['data', 'target', 'target_names',
           'images', 'DESCR'])
```



Fetch 一个加州房屋大数据集 california_housing?


```
california_housing = datasets.fetch_california_housing()
california_housing.keys()
dict_keys(['data', 'target',
           'feature_names', 'DESCR'])
```

Make 一个高斯分位数数据集 gaussian_quantile？


```
gaussian_quantiles = datasets.make_gaussian_quantiles()
type(gaussian_quantiles), len(gaussian_quantiles)
(tuple, 2)
```

好了，本节讲的就是通过 sklearn 获得数据三种方式。在自己做数据分析时，最常见的还是从 csv 和 txt 文件中通过 Pandas 读取并存储成 DataFrame 的形式。

### 核心 API

Sklearn 里万物皆估计器。估计器是个非常抽象的叫法，可把它不严谨的当成一个模型 (用来回归、分类、聚类、降维)，或当成一套流程 (预处理、网格最终)。

本节三大 API 其实都是估计器：

1. 估计器 (estimator) 当然是**估计器**
1. 预测器 (predictor) 是**具有预测功能的估计器**
1. 转换器 (transformer) 是**具有转换功能的估计器**

这三句看似废话，其实蕴藏了很多内容。其实我对第 1 点这个估计器的起名不太满意，我觉得应该叫拟合器 (fitter) - 具有拟合功能的估计器。看完这一节你就会明白「拟合器」这种叫法更合理。

#### 估计器

**定义：任何可以基于数据集对一些参数进行估计的对象都被称为估计器。**

两个核心点：**1. 需要输入数据，2. 可以估计参数**。估计器首先被创建，然后被拟合。

创建估计器

    创建估计器：需要设置一组超参数，比如
    
    - 线性回归里超参数 normalize=True
    - K 均值里超参数 n_clusters=3
    
    在创建好的估计器 model 可以直接访问这些超参数，用 . 符号。
    
    - model.normalize
    - model.n_clusters
    
    但 model 中有很多超参数，你不可能一开始都知道要设置什么值，没设置的用 Sklearn 会给个合理的默认值，因此新手不用担心。

拟合估计器

    拟合估计器：需要训练集。在有监督学习中的代码范式为
    
        model.fit( X_train, y_train )
    
    在无监督学习中的代码范式为
    
        model.fit( X_train )
    
    拟合之后可以访问 model 里学到的参数，比如线性回归里的特征前的系数 coef_，或 K 均值里聚类标签 labels_。
    
    - model.coef_
    - model.labels_

说了这么多抽象的东西，现在展示有监督学习的「线性回归」和无监督学习的「K 均值」的具体例子。


##### 线性回归

首先从 sklearn 下的 linear_model 中引入 LinearRegression，再创建估计器起名 model，设置超参数 normalize 为 True，指的在每个特征值上做标准化，这样会加速数值运算。


```
from sklearn.linear_model import LinearRegression

model = LinearRegression(normalize=True)
model
```

![Po4pCQ](http://img.kekepu.com/uPic/Po4pCQ.jpg)

创建完后的估计器会显示所有的超参数，比如我们设置好的 normalize=True，其他没设置的都是去默认值，比如 n_jobs=None 是只用一个核，你可以将其设为 2 就是两核并行，甚至设为 -1 就是电脑里所有核并行。

自己创建一个简单数据集 (没有噪声完全线性) 只为了讲解估计器里面的特征。


```
x = np.arange(10)
y = 2 * x + 1
plt.plot( x, y, 'o' );
```

![divLLr](http://img.kekepu.com/uPic/divLLr.jpg)



还记得 Sklearn 里模型要求特征 X 是个两维变量么 (样本数×特征数)？但在本例中 X 是一维，因为我们用 np.newaxis 加一个维度，它做的事情就是把 [1, 2, 3] 转成 [[1],[2],[3]]。再把 X 和 y 丢进 fit() 函数来拟合线性模型的参数。

X = x[:, np.newaxis]
model.fit( X, y )




拟合完后的估计器和创建完的样子看起来一样，但是已经用「model.param_」可以访问到学好的参数了，展示如下。

print( model.coef_ )
print( model.intercept_ )
[2.]
1.0


斜率为 2，截距为 1，没毛病。和访问超参数时不一样，注意访问参数要加一个下划线 _。



##### K 均值

首先从 sklearn 下的 cluster 中引入 KMeans，再创建估计器起名 model，设置超参数 n_cluster 为 3 (为了展示方便而我们知道用的 iris 数据集有 3 类，实际上应该选不同数量的 n_cluster，根据 elbow 图来决定，下帖细讲)。


再者，iris 数据里是有标签 y 的，我们假装没有 y 才能无监督的聚类啊，要不然应该做有监督的分类的。

```
from sklearn.cluster import KMeans

model = KMeans( n_clusters=3 )
model
```

![dfCAgS](http://img.kekepu.com/uPic/dfCAgS.jpg)


创建完后的估计器会显示所有的超参数，比如我们设置好的 n_cluster=3，其他没设置的都是去默认值，比如 max_iter=300 是最多迭代次数为 300，算法不收敛也停了。


还记得 iris 里的特征有四个吗 (萼片长、萼片宽、花瓣长、花瓣宽)？四维特征很难可视化，因此我们只取两个特征 (萼片长、萼片宽) 来做聚类并且可视化结果。注意下面代码 X = iris.data[:,0:2]。
```
X = iris.data[:,0:2]
model.fit(X)
```

![uFV0mx](http://img.kekepu.com/uPic/uFV0mx.jpg)

拟合完后的估计器和创建完的样子看起来一样，但是已经用「model.param_」可以访问到学好的参数了，展示如下。

```
print( model.cluster_centers_, '\n')
print( model.labels_, '\n' )
print( model.inertia_, '\n')
print( iris.target )
```

![mGgmL5](http://img.kekepu.com/uPic/mGgmL5.jpg)

有点乱，解释一下 KMeans 模型这几个参数：


- model.cluster_centers_：簇中心。三个簇那么有三个坐标。
- model.labels_：聚类后的标签
- model.inertia_：所有点到对应的簇中心的距离平方和 (越小越好)

需要强调的是真实标签 iris.label 和聚类标签 model.labels_ 看起来差的很远。类别 0 都一致，但是类别 1 和 2 弄反了，这是因为在 KMeans 算法里标注的类别索引和真实类别索引不一样 (我现在也没找到什么方法能够让它们一致)。


最后画出两幅图，左图是根据聚类得到的标签画出散点图，而右图是根据真实标签画出散点图，对比两幅图看很像，聚类的效果还不错是把。

![J3TQYX](http://img.kekepu.com/uPic/J3TQYX.jpg)

![o9FjM1](http://img.kekepu.com/uPic/o9FjM1.jpg)


##### 小结

虽然上面以有监督学习的 LinearRegression 和无监督学习的 KMeans 举例，但实际上你可以将它们替换成其他别的模型，比如有监督学习的 LogisticRegression 和无监督学习的 DBSCAN。它们都是「估计器」，因此都有 fit() 方法。使用它们的通用伪代码如下：


```
# 有监督学习
from sklearn.xxx import SomeModel
# xxx 可以是 linear_model 或 ensemble 等

model = SomeModel( hyperparameter )
model.fit( X, y )
```
```
# 无监督学习
from sklearn.xxx import SomeModel
# xxx 可以是 cluster 或 decomposition 等

model = SomeModel( hyperparameter )
model.fit( X )
```

#### 预测器

**定义：预测器在估计器上做了一个延展，延展出预测的功能。**

两个核心点：**1. 基于学到的参数预测，2. 预测有很多指标**。最常见的就是 predict() 函数：

model.predict(X_test)：**评估**模型在新数据上的表现

model.predict(X_train)：**确认**模型在老数据上的表现

因为要做预测，首先将数据分成 80:20 的训练集 (X_train, y_train) 和测试集 (X_test, y_test)，在用从训练集上拟合 fit() 的模型在测试集上预测 predict()。


```
from sklearn.datasets import load_iris
iris = load_iris()
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test 
= train_test_split( iris['data'], 
                    iris['target'], 
                    test_size=0.2 )

print( 'The size of X_train is ', X_train.shape )
print( 'The size of y_train is ', y_train.shape )
print( 'The size of X_test is ', X_test.shape )
print( 'The size of y_test is ', y_test.shape )
```
```
The size of X_train is (120, 4)
The size of y_train is (120,)
The size of X_test is (30, 4)
The size of y_test is (30,)

```


让我们来看个有监督学习的「对率回归」和继续上节无监督学习的「K 均值」的例子。

##### 对率回归

首先从 sklearn 下的 linear_model 中引入 LogisticRegression，再创建估计器起名 model，设置超参数 mutli_class 为 multinomial 因为有三种鸢尾花，是个多分类问题。

接着再训练集上拟合参数，这时估计器 model 里面已经可以访问这些参数了。

![pPhE65](http://img.kekepu.com/uPic/pPhE65.jpg)

![Pp9ZCS](http://img.kekepu.com/uPic/Pp9ZCS.jpg)


**predict & predict_proba**

对于分类问题，我们不仅想知道预测的类别是什么，有时还想知道预测该类别的信心如何。前者用 predict()，后者用 predict_proba()。



代码如下，在测试集上比较预测标签 y_pred 和真实标签 y_test 发现它们完全吻合，准确率 100% (iris 数据太简单 )。

```
y_pred = model.predict( X_test )
p_pred = model.predict_proba( X_test )
print( y_test, '\n' )
print( y_pred, '\n' )
print( p_pred )
```

![LA2olY](http://img.kekepu.com/uPic/LA2olY.jpg)


解释一下 p_pred - 测试集里有 30 个数据，鸢尾花有 3 类，因此 predict_proba() 生成一个 30×3 的数组，每行的概率加起来为 1。



为了验证我们的理解，我们看 Sklearn 是不是把「每行中最大概率值对应的那一类」作为预测结果。

```
s = ['Class 1 Prob', 'Class 2 Prob', 'Class 3 Prob']
prob_DF = pd.DataFrame( p_pred, columns=s )
prob_DF['Predicted Class'] = y_pred
prob_DF.head()
```

![VMzrvG](http://img.kekepu.com/uPic/VMzrvG.jpg)

是的！前三行 Class 1 Prob 最大，预测是第一类；第四行 Class 2 Prob 最大，预测是第二类；第四行 Class 3 Prob 最大，预测是第三类。


**score & decision_function**

预测器里还有额外的两个函数可以使用。在分类问题中

- score() 返回的是分类准确率
- decision_function() 返回的是每个样例在每个类下的分数值

```
print( model.score( X_test, y_test ) )
print( np.sum(y_pred==y_test)/len(y_test) )
```
```
1.0
1.0
```
```
decision_score = model.decision_function( X_test )
print( decision_score )
```

![qbDoeq](http://img.kekepu.com/uPic/qbDoeq.jpg)

为了验证我们的理解，我们看 Sklearn 是不是把「每行中最高得分值对应的那一类」作为预测结果。

```
s = ['Class 1 Score', 'Class 2 Score', 'Class 3 Score']
decision_DF = pd.DataFrame( decision_score, columns=s )
decision_DF['Predicted Class'] = y_pred
decision_DF.tail()
```

![7gM0cr](http://img.kekepu.com/uPic/7gM0cr.jpg)

是的！前两行 Class 3 Score 最大，预测是第三类；后三行 Class 1 Score 最大，预测是第一类。


##### K 均值

继续上一节的 KMeans 模型，首先用 fit() 训练。

![xnVNGW](http://img.kekepu.com/uPic/xnVNGW.jpg)

再用 predict() 在测试集上预测出类别 inx_pred，和真实标签 y_test 比对。再次强调，inx_pred 和 y_test 给三个类别的索引定义是不同的。

```
idx_pred = model.predict( X_test[:,0:2] )
print( index_pred )
print( y_test )
```

最后画出两幅图 (都是在测试集上)，左图是根据聚类预测的标签画出散点图，而右图是根据真实标签画出散点图，对比两幅图看很像，聚类的效果也不错。

![qdEMHh](http://img.kekepu.com/uPic/qdEMHh.jpg)

![TDE8uM](http://img.kekepu.com/uPic/TDE8uM.jpg)


KMeans 模型里也有 score() 函数，输出是值是它要优化的目标函数的对数。

```
model.score( X_test[:,0:2] )
```
```
-9.662259042197803
```

##### 小节

估计器都有 fit() 方法，预测器都有 predict() 和 score() 方法，言外之意不是每个预测器都有 predict_proba() 和 decision_function() 方法，这个在用的时候查查官方文档就清楚了 (比如 RandomForestClassifier 就没有 decision_function() 方法)。

使用它们的通用伪代码如下：

```
# 有监督学习
from sklearn.xxx import SomeModel
# xxx 可以是 linear_model 或 ensemble 等

model = SomeModel( hyperparameter )
model.fit( X, y )
y_pred = model.predict( X_new )
s = model.score( X_new )
```
```
# 无监督学习
from sklearn.xxx import SomeModel
# xxx 可以是 cluster 或 decomposition 等

model = SomeModel( hyperparameter )
model.fit( X )
idx_pred = model.predict( X_new )
s = model.score( X_new )
```


