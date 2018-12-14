---
title: 《跟老齐学 Python 轻松入门》学习笔记--1.基本对象类型
tags:
  - Python
categories: 知识点
permalink: python-easy
updated: '2017-10-05 09:01:42'
date: 2017-10-05 09:01:42
---


本文根据 《跟老齐学python轻松入门》 的知识点整理，主要是代码打了一遍做了总结，非常零基础。


<!--more-->



## 数和数的运算

```python
id()   # 查看每个对象的内存地址
help() # 查看其它函数文档
type() # 查看对象类型
dir()  # 查看模块中函数名称
```
- python 自动解决大整数问题

### 加法
```python
>>> 4.0 + 2
6.0
```

### 乘法
```python
# 9^2
>>> 9 ** 2
81

# 2 * 10^3
>>> 2e3
2000.0
```

### 除法

```python
>>> 5 / 2
2.5

>>> 5 // 2
2
```

### 异常计算

浮点数十进制转化为二进制造成误差
```python
>>> 10.0 / 3
3.3333333333333335
```

解决方法1：使用 decimal 模块(小数)

```python
>>> import decimal
>>> a = decimal.Decimal("10.0")
>>> b = decimal.Decimal("3")
>>> a / b
Decimal('3.333333333333333333333333333')
```

解决方法1：使用 fractions 模块(分数)

```python
>>> from fractions import Fraction  
# fractions 是一个大模块(库)，
# 只想用其中的子模块 Fraction
>>> Fraction(10, 3)
Fraction(10, 3)
>>> Fraction(10, 8)
Fraction(5, 4)
```

### 余数

```python
>>> 5 % 2
1

>>> 5.0 % 2
1.0
>>> 

>>> divmod(5, 2)
(2, 1) # 返回商和余数
```

### 四舍五入

```python
>>> round(1.23456, 2)
1.23
```

浮点数十进制转化为二进制造成误差

```python
>>> round(1.2345, 3)
1.234  # 应该是 1.235

>>> round(2.235, 2)
2.23   # 应该是 2.24
```
### math 模块

```python
>>> import math
>>> math.pi
3.141592653589793

>>> 4 ** 2
16

>>> math.pow(4, 2)
16.0

>>> math.sqrt(9)
3.0

>>> math.floor(3.14)
3

>>> math.floor(3.92)
3

>>> math.fabs(-2)
2.0

>>> abs(-2)
2

>>> math.fmod(5, 3)
2.0

>>> 5 % 3
2
```

## 字符串

### 键盘输入

```python
# input() 函数进行输入赋值
>>> age = input("how old are you?")
how old are you?10  # 提示输入内容，通过键盘输入10
>>> age
'10'
>>> type(age)
<class 'str'>
```

### 原始字符串

```python
>>> dos = "c:\news"
>>> print(dos)
c:
ews
>>> dos = "c:\\news"  # 转义字符解决
>>> print(dos)
c:\news
>>> print(r"c:\news") # r 开头的字符串是原始字符串
c:\news
```

### 字符串切片

```python
>>> lang = 'study python'
>>> b = lang[1:]
>>> b
'tudy python'
>>> c = lang[:]
>>> c
'study python'
>>> d = lang[:10]
>>> d
'study pyth'
>>> e = lang[0:10]
>>> e
'study pyth'
>>> f = lang[1:12]
>>> f
'tudy python'
```

### 连接字符串

```python
>>> "py" + "thon"
'python'

>>> a = 1996
>>> b = "xunge"
>>> a + b
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for +: 'int' and 'str'
>>> str(a) + b
'1996xunge'
>>> repr(a) + b
'1996xunge'

# str() 与 repr() 区别:
# str() 转化后的结果更适合与人进行交互
# repr()转化后的结果则可以被 Python的 解释器阅读
>>> s = 'xunge \n'
>>> str(s)
'xunge \n'
>>> repr(s)
"'xunge \\n'"
>>> print(str(s))
xunge 

>>> print(repr(s))
'xunge \n'
>>> 
```

### 判断元素是否在字符串中

```python
>>> str = "python"
>>> "th" in str
True
```

### 最值和比较

```python
>>> max(str)
'y'
>>> min(str)
'h'
>>> ord('y') # 字符转化为编码
121
>>> chr(121) # 编码转化为字符
'y'
```

### 测量长度

```python
>>> len(str)
6
```

### 字符串格式化输出

```python
>>> "I love %s" % "gong yu xin"
'I love gong yu xin'
>>> "I love {0}.{1}.{2}".format("gong", "yu", "xin")
'I love gong.yu.xin'
>>> "I love {0:8}.{1:>8}.{2:^8}".format("gong", "yu", "xin")
'I love gong    .      yu.  xin   '
>>> "I love {0:.2}.{1:>8.2}.{2:^4.2}".format("gong", "yu", "xin")
'I love go.      yu. xi '

>>> "She is {0:d} years old and {1:f}cm".format(21, 175.1221)
'She is 21 years old and 175.122100cm'
>>> "She is {0:4d} years old and {1:7.2f}cm".format(21, 175.1221)
'She is   21 years old and  175.12cm'
>>> "She is {0:04d} years old and {1:07.2f}cm".format(21, 175.1221)
'She is 0021 years old and 0175.12cm'

>>> "I like {lang} and {name}".format(lang = "python", name = "gongyuxin")
'I like python and gongyuxin'
>>> data = {"name":"gongyuxin", "age":21}
>>> "{name} is {age}".format(**data)
'gongyuxin is 21'
```

### 常用字符串方法

```python
# 判断是否全是字母
>>> "python".isalpha()
True
>>> "python2".isalpha()
False

# 根据分隔符分割字符串
>>> s = "I love gong yu xin"
>>> s.split(" ")
['I', 'love', 'gong', 'yu', 'xin']

# 去掉字符串两头的空格
>>> s.strip()  # 去掉两边空格
'gong'
>>> s.lstrip() # 去掉左边空格
'gong '
>>> s.rstrip() # 去掉右边空格
' gong'

# 字符大小写转换
>>> a = "gong yu xin"
>>> b = a.upper()  # 小写字母转换为大写字母
>>> b
'GONG YU XIN'
>>> a
'gong yu xin'      # 原对象未变
>>> c = b.lower()  # 大写字母转换为小写字母
>>> c
'gong yu xin'
>>> c.capitalize() # 把字符串的第一个字母变成大写
'Gong yu xin'
>>> d = a.title()  # 每个单词首字母大写
>>> d
'Gong Yu Xin'
>>> a.islower()
True
>>> b.isupper()
True
>>> c.islower()
True
>>> d.istitle()
True

# join() 拼接字符串
>>> b = "www.xungejiang.com"
>>> c = b.split(".")
>>> c
['www', 'xungejiang', 'com']
>>> ".".join(c)
'www.xungejiang.com'
```

## 列表

### 列表切片

```python
>>> a = ['gong', 831, 'xungejiang.com']
>>> a[1:]
[831, 'xungejiang.com']
>>> a[2][11:14]
'com'

# -1 是右边第一个
>>> a[-1]
'xungejiang.com'
>>> a[-3:-1]    # a[(3-3):(3-1)] = a[0:2]
['gong', 831]

# 完整写法 seq[start:end:step]
>>> alst = [1, 2, 3, 4, 5, 6]
>>> alst[::2]
[1, 3, 5]
>>> alst[::1]
[1, 2, 3, 4, 5, 6]
>>> alst[::-1]   # 反转
[6, 5, 4, 3, 2, 1]
>>> alst[::-2]
[6, 4, 2]

# 使用 reversed() 函数将原来序列对象反转
# 使用 list() 函数将迭代对象转换为列表显示
>>> list(reversed(alst))
[6, 5, 4, 3, 2, 1]
>>> list(reversed("abcd"))
['d', 'c', 'b', 'a']
```
### 列表基本操作

```python
>>> lst = ['jiang', 'xun', 'zhi']
# len() 列表长度
>>> len(lst)
3
>>> alst = ['gong', 'yu', 'xin']
# "+" 连接两个序列
>>> lst + alst
['jiang', 'xun', 'zhi', 'gong', 'yu', 'xin']
# "*" 重复序列元素
>>> lst * 3
['jiang', 'xun', 'zhi', 'jiang', 'xun', 'zhi', 'jiang', 'xun', 'zhi']
# 序列是否包含该元素
>>> "jiang" in lst
True
# 按照元素字典顺序进行比较
>>> max(lst)
'zhi'
>>> min(lst)
'jiang'

# 修改列表元素
# list.append(x) 向列表中追加元素 x
>>> cities = ['harbin', 'changchun']
>>> cities[1] = 'beijing'
>>> cities
['harbin', 'beijing']
>>> cities.append('shanghai')
>>> cities
['harbin', 'beijing', 'shanghai']

# list.extend([L]) 向列表中追加列表 L 的元素
>>> la = [1, 2, 3]
>>> lb = ['jiang', 'gong']
>>> la.extend(lb)
>>> la
[1, 2, 3, 'jiang', 'gong']
>>> lb
['jiang', 'gong']

# append() 和 extend() 的区别
# append() 是整建制的追加
# extend() 是个体化扩编
>>> alst = [1, 2, 3]
>>> blst = [1, 2, 3]
>>> clst = ["jiang", "gong"]
>>> alst.append(clst)
>>> alst
[1, 2, 3, ['jiang', 'gong']]
>>> len(alst)
4
>>> blst.extend(clst)
>>> blst
[1, 2, 3, 'jiang', 'gong']
>>> len(blst)
5

# list.count(x) x 元素出现次数
>>> la = [1, 2, 2, 1, 3, 1]
>>> la.count(1)
3

# list.insert(i, x) 将 x 插入到索引是 i 的元素前面
>>> lst = ["gong", "xin"]
>>> lst.insert(1, "yu")
>>> lst
['gong', 'yu', 'xin']

# list.remove(x) 删除第一次出现的 x 元素，无返回值
>>> lst = ["python", "c++", "python", "java"]
>>> lst.remove("python")
>>> lst
['c++', 'python', 'java']

# list.pop([i]) 删除索引为 i 的元素，并将删除元素作为返回值。
# i 为空则删除列表最后一个
>>> lst.pop(1)
'python'
>>> lst
['c++', 'java']

# list.reverse(L) 将元素顺序反转，不返回值
>>> a = [8, 3, 1, 4, 3, 0]
>>> a.reverse()
>>> a
[0, 3, 4, 1, 3, 8]
# reversed(L) 实现对列表的反向迭代
>>> b = reversed(a)
>>> list(b)
[8, 3, 1, 4, 3, 0]
>>> b
<list_reverseiterator object at 0x0000015DFC053710>

# list.sort() 对列表进行排序
>>> a.sort()
>>> a
[0, 1, 3, 3, 4, 8]
# 从大到小排序
>>> a.sort(reverse = True)
>>> a
[8, 4, 3, 3, 1, 0]
# 按字符串长度排序
>>> lst = ["java", "python", "c++", "basic", "pascal"]
>>> lst.sort(key = len)
>>> lst
['c++', 'java', 'basic', 'python', 'pascal']
```

