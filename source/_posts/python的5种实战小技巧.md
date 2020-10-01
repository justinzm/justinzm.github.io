---
title: python的5种实战小技巧
date: 2019-10-06 20:34:10
categories: Python
tags: 代码块
toc: true
---

### 字符串运算

字符串本质上也是一种元组，但是字符串有很多“运算”方式。最直观的是字符串的 + 和 * 运算，它们分别表示重复和连接。

```
>>> my_string = "Hi Medium..!"
>>> print(my_string * 2)
Hi Medium..!Hi Medium..!
>>> print(my_string + " I love Python" * 2)
Hi Medium..! I love Python I love Python
```

此外，如果希望获得反向字符串，也可以直接使用 [::-1] 进行索引：

```
>>> print(my_string[::-1])
!..muideM iH
>>> my_list = [1,2,3,4,5]
>>> print(my_list[::-1])
[5, 4, 3, 2, 1]
```

如果列表元素都是字符串，那么我们可以快速地使用 join() 方法将所有元素拼接在一起：

```
>>> word_list = ["awesome", "is", "this"]
>>> print(' '.join(word_list[::-1]) + '!')
this is awesome!
```

如上我们使用 .join() 方法拼接列表元素，其中 『 』 表示连接方式为空格。其实在自然语言处理中，这个方法会经常使用，例如我们将句子拆分为了字符，那么处理后的合并就需要使用 join() 了。

### 列表推导式

我们先定义一个简单的函数，它会算变量的平方并加 5：

```
>>> def stupid_func(x):
>>> return x**2 + 5
```

如果我们希望将该函数应用到列表中的奇数项，那么不采用列表推导式的情况下，我们一般会写成以下形式：

```
>>> my_list = [1, 2, 3, 4, 5]
>>> new_list = []
>>> for x in my_list:
>>> if x % 2 != 0:
>>> new_list.append(stupid_func(x))
>>> print(new_list)
[6, 14, 30]
```

但是现在我们有了列表推导式，那么上面代码可以等价修改为：

```
>>> my_list = [1, 2, 3, 4, 5]
>>> print([stupid_func(x) for x in my_list if x % 2 != 0])
[6, 14, 30]
```

列表推导式的一般语法可以表示为 [ expression for item in list ]，如果你希望加上一些布尔型条件语句，那么上面的语法可以修改为 [ expression for item in list if conditional ]，它的结构其实和下面是等价的。

```
>>> for item in list:
>>> if conditional:
>>> expression
```

上面的列表推导式可以进一步简化，即不需要定义一个新函数。

```
>>> print([x ** 2 + 5 for x in my_list if x % 2 != 0])
[6, 14, 30]
```

### Lambda 和 Map

Lambda 是一种匿名函数，它看起来可能有一点奇怪，但是一旦你理解了它，那么它就会变得非常直观与强大。

一般而言，Lambda 函数都比较小，它也不需要定义函数名。那么为什么需要匿名函数？简单而言，Lambda 最常执行一些直观的运算，它并不需要标准的函数定义，而且也不需要新的函数名再次调用。

还是拿上面的先平方再加 5 为例，前面我们是定义了一个标准的函数，def stupid_func(x)，现在我们可以试试 Lambda 匿名函数：

```
>>> stupid_func = (lambda x : x ** 2 + 5)
>>> print([stupid_func(1), stupid_func(3), stupid_func(5)])
[6, 14, 30]
```

那么我们为什么要用上面的表达式？很大一部分原因在于，当我们想执行一些简单运算时，可以不需要定义真实函数就能完成。例如排序列表元素，一种直观的方法可以使用 sorted() 方法：

```
>>> my_list = [2, 1, 0, -1, -2]
>>> print(sorted(my_list))
[-2, -1, 0, 1, 2]
```

这只能默认从大到小或从小到大排序，但是借助 Lambda 表达式，我们可以实现更自由的排序标准。如下所示我们希望根据最小的平方数对列表进行排序，其可以使用 Lambda 函数定义键，从而告诉 sorted() 方法该怎样排序。

```
>>> print(sorted(my_list, key = lambda x : x ** 2))
[0, -1, 1, -2, 2]
```

Map 是一个简单的函数，它可以将某个函数应用到其它一些序列元素，例如列表。如果我们有两个列表，我们希望将这两个列表对应的元素相乘，那么使用 lambda 函数和 map 可以快速实现这一功能：

```
>>> print(list(map(lambda x, y : x * y, [1, 2, 3], [4, 5, 6])))
[4, 10, 18]
```

上面的代码非常优雅，如果不用它们两者，那么一般的表达需要写成这样：

```
>>> x, y = [1, 2, 3], [4, 5, 6]
>>> z = []
>>> for i in range(len(x)):
>>> z.append(x[i] * y[i])
>>> print(z)
[4, 10, 18]
```

### 单行条件语句

如果我们使用条件语句，那么最可能写成下面这个样子：

```
>>> x = int(input())
>>> if x >= 10:
>>> print("Horse")
>>> elif 1 < x < 10:
>>> print("Duck")
>>> else:
>>> print("Baguette")
```

但其实我们也可以把所有条件都写在 print 函数内，即上面 7 行代码可以等价写成如下一行：

```
print("Horse" if x >= 10 else "Duck" if 1 < x < 10 else "Baguette")
```

这样看起来真的非常简洁，如果你查看以前写的代码，真的有很多都可以改成这种表达式。

### zip()

前面在介绍 map() 函数时，我们举了个例子将某个函数应用到平行的两个列表，而 zip() 函数可以更简单地做到这一点。

如果我们有两个列表，第一个列表包含了名，第二个列表包含了姓，使用 zip() 函数，我们可以将它们拼接在一起。

```
>>> first_names = ["Peter", "Christian", "Klaus"]
>>> last_names = ["Jensen", "Smith", "Nistrup"]
>>> print([' '.join(x) for x in zip(first_names, last_names)])
['Peter Jensen', 'Christian Smith', 'Klaus Nistrup']
```

也就是说，zip 将两个等长的列表变为了一对一对的，即 (("Peter", "Jensen"), ("Christian", "Smith"), ("Klaus", "Nistrup"))。