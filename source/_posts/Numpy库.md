---
title: Numpy库
date: 2019-10-01 20:27:26
categories: Python
tags: Python包
toc: true
---


NumPy库是Python中科学计算的核心库。它提供了一个高性能的多维数组对象，以及处理这些数组的工具。

### 载入包
```
import numpy as np
```

### 创建数组

```
a = np.array([1, 2, 3])
>>> [1 2 3]

b = np.array([(1.5, 2, 3), (4, 5, 6)], dtype=float)
>>> [[1.5 2.  3. ]
>>> [4.  5.  6. ]]

c = np.array([[(1.5, 2, 3), (4, 5, 6)], [(3, 2, 1), (4, 5, 6)]], dtype=float)
>>> [[[1.5 2.  3. ]
>>> [4.  5.  6. ]]
>>> [[3.  2.  1. ]
>>> [4.  5.  6. ]]]
```

#### 最初的占位符 Initial Placeholders

```
np.zeros((3, 4))
# 用法：zeros(shape, dtype=float, order='C')
# 返回：返回来一个给定形状和类型的用0填充的数组

np.ones((2,3,4),dtype=np.int16)
# 依据给定形状和类型(shape[, dtype, order])返回一个新的元素全部为1的数组

d = np.arange(10, 25, 5)
# arange函数用于创建等差数组
# np.arange([start, ]stop, [step, ]dtype=None)
# start:可忽略不写，默认从0开始;起始值
# stop:结束值；生成的元素不包括结束值
# step:可忽略不写，默认步长为1；步长
# dtype:默认为None，设置显示元素的数据类型
nd1 = np.arange(5)#array([0, 1, 2, 3, 4])
nd2 = np.arange(1,5)#array([1, 2, 3, 4])

np.arange(n)
# 类似range()函数，返回ndarray类型，元素从0到n‐1

np.linspace(0, 2, 9)
# numpy.linspace(start, stop[, num=50[, endpoint=True[, retstep=False[, dtype=None]]]]])
# 返回在指定范围内的均匀间隔的数字（组成的数组），也即返回一个等差数列
# start - 起始点
# stop - 结束点
# num - 元素个数，默认为50
# endpoint - 是否包含stop数值，默认为True，包含stop值；若为False，则不包含stop值
# retstep - 返回值形式，默认为False，返回等差数列组，若为True，则返回结果(array([`samples`, `step`])),
# dtype - 返回结果的数据类型，默认无，若无，则参考输入数据类型。

e = np.full((2, 2), 7)
# 创建一个由常数填充的数组,第一个参数是数组的形状，第二个参数是数组中填充的常数。

f = np.eye(2)
# 创建单位矩阵，函数中的参数n，则创建n * n的单位矩阵

np.ones_like(a)
# 根据数组a的形状生成一个全1数组

np.zeros_like(a)
# 根据数组a的形状生成一个全0数组

np.full_like(a,val)
# 根据数组a的形状生成一个数组，每个元素值都是val

np.random.random((2, 2))
# 按照参数中给出的形状来创建数组，创建随机数数组

np.empty((3, 2))
# 按照参数中给出的形状来创建数组，创建一个随机元素
```

### 输入/输出 I/O

#### 数组以二进制格式保存 Saving & Loading On Disk

```
>>> np.save('my_array', a)
>>> np.savez('array.npz', a, b)
>>> np.load('my_array.npy')

# 例
>>> a=np.array([[1, 2, 3], [4, 5, 6]])
>>> b=np.array([1, 2])
>>> np.savez('/tmp/123.npz', a=a, b=b)
>>> data = np.load('/tmp/123.npz')
>>> data['a']
array([[1, 2, 3],
       [4, 5, 6]])
>>> data['b']
array([1, 2])
>>> data.close()
```

#### 存取文本文件 Saving & Loading Text Files

```
>>> np.loadtxt("myfile.txt")
>>> np.genfromtxt("my_file.csv", delimiter=',')
>>> np.savetxt("myarray.txt", a, delimiter=" ")
# 使用 np.savetxt 和 np.loadtxt 只能读写 1 维和 2 维的数组 
# np.savetxt：将数组写入以某种分隔符隔开的文本文件中 
# np.loadtxt：指定某种分隔符，将文本文件读入到数组中
```

### 数据类型
```
np.int64
# 64位长度的整数，取值：[‐2^63 , 2^63‐1]

np.float32
# 32位半精度浮点数：1位符号位，8位指数，23位尾数

np.complex
# 复数类型

np.bool
# 布尔类型，True或False

np.object
np.string_
np.unicode_
```

### 查询数组 Inspecting Your Array

```
a.shape
# 表示每个维度上的数组的大小

len(a)
# 获得narray的长度

b.ndim
# 表示数组对象或矩阵的维度

e.size
# 表示数组中元素的总数，等同于ndarray.shape中两个元素的乘积;

b.dtype
# 表示数组中元素的类型;

b.dtype.name
# 获得数据类型对象名称

b.astype(int)
# 转换数据类型
```

### 数学 Array Mathematics

#### 算术运算 Arithmetic Operations
```
>>> np.subtract(a,b)
>>> g = a - b 
    array([[-0.5, 0. , 0. ], [-3. , -3. , -3. ]])
# 减法

>>> np.add(b,a)
>>> b + a
 array([[ 2.5, 4. , 6. ],
 [ 5. , 7. , 9. ]])
# 加法

>>> np.divide(a,b)
>>> a / b
 array([[ 0.66666667, 1. , 1. ],
 [ 0.25 , 0.4 , 0.5 ]])
# 除法

>>> np.multiply(a,b)
>>> a * b  
  array([[ 1.5, 4. , 9. ],
  [ 4. , 10. , 18. ]])
# 乘法

>>> np.exp(b) 
# 求幂

>>> np.sqrt(b) 
# 计算数组各元素的平方根

>>> np.sin(a) 
# 正弦值

>>> np.cos(b) 
# 计算数组各元素的普通型和双曲型三角函数

>>> np.log(a) 
# 计算数组各元素的自然对数、10底对数和2底对数

>>> np.abs(x)  np.fabs(x)
# 计算数组各元素的绝对值

>>> e.dot(f)  
array([[ 7., 7.],
  [ 7., 7.]])

 
```

#### 比较 Comparison
```
>>> a == b
 array([[False, True, True],
  [False, False, False]], dtype=bool)
  
>>> a < 2
 array([True, False, False], dtype=bool)
 
>>> np.array_equal(a, b)
# 判断两数组是否相等
```


#### 聚合函数 Aggregate Functions
```
>>> a.sum() 
# 向量的加法

>>> a.min() 
# 无参，所有中的最小值
# axis=0; 每列的最小值
# axis=1；每行的最小值

>>> b.max(axis=0)
# 无参，所有中的最大值
# axis=0; 每列的最大值
# axis=1；每行的最大值

>>> b.cumsum(axis=1) 
# axis=0，按照行累加
# axis=1，按照列累加
# axis不给定具体值，就把numpy数组当成一个一维数组

>>> a.mean() 
# axis 不设置值，对 m*n 个数求均值，返回一个实数
# axis = 0：压缩行，对各列求均值，返回 1* n 矩阵
# axis =1 ：压缩列，对各行求均值，返回 m *1 矩阵

>>> b.median() 
# axis 不设置值，求中位数
# axis = 0：压缩行，对各列求中位数
# axis = 1：压缩列，对各行求中位数

>>> a.corrcoef() 
# 计算皮尔逊积矩相关系数

>>> np.std(b)
# axis 不设置值，计算全局标准差
# axis = 0：计算每一列的标准差
# axis = 1：计算每一行的标准差

```

### 复制 Copying Arrays

```
>>> h = a.view() 
# 创建具有相同数据的数组视图

>>> np.copy(a) 
# 创建数组的副本

>>> h = a.copy()
# 创建数组的深度副本
```

### 排序 Sorting Arrays
```
>>> a.sort() 
# 返回的是一个经过复制排序后的对维数组

>>> c.sort(axis=0)
# 0指的是列维度
```

### 构造子集、切片、索引 Subsetting, Slicing, Indexing

#### 构造子集 Subsetting
```
>>> a[2]  
3

>>> b[1,2]
6.0
```

#### 切片 Slicing
```
>>> a[0:2] 
# 选择索引0和1处的项
 array([1, 2])
 
>>> b[0:2,1] 
# 在第1列的第0行和第1行选择项
 array([ 2., 5.]) 
 
>>> b[:1] 
# 选择第0行中的所有项(equivalent to b[0:1, :])
array([[1.5, 2., 3.]]) 

>>> c[1,...] 
# 相同 [1,:,:]
array([[[ 3., 2., 1.],
 [ 4., 5., 6.]]])
 
>>> a[ : :-1] 
# 逆转了数组
array([3, 2, 1])
```

#### 布尔索引 Boolean Indexing
```
>>> a[a<2]
array([1])
# 从小于2的元素中选择元素
```

#### Fancy Indexing
```
>>> b[[1, 0, 1, 0],[0, 1, 2, 0]]  
# 选择元素(1,0)、(0,1)、(1,2)和(0,0)
array([ 4. , 2. , 6. , 1.5])

>>> b[[1, 0, 1, 0]][:,[0,1,2,0]]  
# 选择矩阵的行和列的子集
array([[ 4. ,5. , 6. , 4. ], 
 [ 1.5, 2. , 3. , 1.5],
 [ 4. , 5. , 6. , 4. ],
 [ 1.5, 2. , 3. , 1.5]])
```

### 数组操作 Array Manipulation

#### 置换阵列  Transposing Array
```
>>> i = np.transpose(b) 
>>> i.T
# 交换数组维度
```

#### 改变阵列形状 Changing Array Shape
```
>>> b.ravel() 
# 改成一维数组

>>> g.reshape(3,-2)
# 重塑，但不要改变数据
```

#### 添加/删除元素 Adding/Removing 
```
>>> h.resize((2,6)) 
# 返回一个形状为(2,6)的新数组

>>> np.append(h,g) 
# 向数组追加项

>>> np.insert(a, 1, 5) 
# 在数组中插入项

>>> np.delete(a,[1])
# 从数组中删除项
```

#### 结合数组 Combining Arrays
```
>>> np.concatenate((a,d),axis=0) 
array([ 1, 2, 3, 10, 15, 20])
# 合并数组

>>> np.vstack((a,b))
>>> np.r_[e,f] 
array([[ 1. , 2. , 3. ],
 [ 1.5, 2. , 3. ],
 [ 4. , 5. , 6. ]])
# 垂直堆栈数组(行方向)

>>> np.hstack((e,f))  
array([[ 7., 7., 1., 0.], 
  [ 7., 7., 0., 1.]])
# 水平堆栈阵列(按列排列)
  
>>> np.column_stack((a,d))  
array([[ 1, 10],
 [ 2, 15],
 [ 3, 20]])
>>> np.c_[a,d]
# 创建堆叠的列方向数组
```

#### Splitting Arrays
```
>>> np.hsplit(a,3) 
[array([1]),array([2]),array([3])] 
# 在第3个索引处水平分割数组

>>> np.vsplit(c,2) 
[array([[[ 1.5, 2. , 1. ],
 [ 4. , 5. , 6. ]]]), 
 array([[[ 3., 2., 3.],
 [ 4., 5., 6.]]])]
# 在第二个索引处垂直拆分数组
```