# python使用

## 1基础知识

### 1.1转义字符

**转义字符**`\`可以转义很多字符，比如`\n`表示换行，`\t`表示制表符，字符`\`本身也要转义，所以`\\`表示的字符就是`\`，可以在Python的交互式命令行用`print()`打印字符串看看：

```
>>> print('I\'m ok.')
I'm ok.
>>> print('I\'m learning\nPython.')
I'm learning
Python.
>>> print('\\\n\\')
\
\
```

如果字符串里面有很多字符都需要转义，就需要加很多`\`，为了简化，Python还允许用`r''`表示`''`内部的字符串默认不转义，可以自己试试：

```
>>> print('\\\t\\')
\       \
>>> print(r'\\\t\\')
\\\t\\
```

### 1.2序列（列表、元组...）

list和tuple是Python内置的有序集合，一个可变，一个不可变。根据需要来选择使用它们。

#### 1.2.1列表的使用

- 增：append(),insert()
- 删：pop()
- 改: list[0] = 
- 查: list[0]

```python

list1 = ['physics', 'chemistry', 1997, 2000] 
list2 = [1, 2, 3, 4, 5, 6, 7 ]  
print ("list1[0]: ", list1[0])
print ("list2[1:5]: ", list2[1:5])
```

以上实例输出结果：

```
list1[0]:  physics
list2[1:5]:  [2, 3, 4, 5]
```

**insert() 把元素插入到指定的位置:**

也可以把元素插入到指定的位置，比如索引号为`1`的位置：

```
>>> classmates.insert(1, 'Jack')
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy', 'Adam']
```

**pop() 删除list末尾的元素:**

要**删除list末尾的元素**，用`pop()`方法：

```
>>> classmates.pop()
'Adam'
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy']
```

要**删除指定位置的元素**，用`pop(i)`方法，其中`i`是索引位置：

```
>>> classmates.pop(1)
'Jack'
>>> classmates
['Michael', 'Bob', 'Tracy']
```

**对列表进行排序sort()：**

~~~
>>> a = ['c', 'b', 'a']
>>> a.sort()
>>> a
['a', 'b', 'c']
~~~



#### 1.2.2元组的使用

**另一个有序列表叫元组：tuple。**tuple和list非常类似，但是tuple一旦初始化就不能修改,因为tuple不可变，所以代码更安全。如果可能，能用tuple代替list就尽量用tuple。

~~~python
classmates = ('Michael', 'Bob', 'Tracy')
~~~

**注意**：只有1个元素的tuple定义时必须加一个逗号`,`，来消除歧义：

```
>>> t = (1,)
>>> t
(1,)
```

Python在显示只有1个元素的tuple时，也会加一个逗号`,`，以免你误解成数学计算意义上的括号。

**来看一个“可变的”tuple：**

```
>>> t = ('a', 'b', ['A', 'B'])
>>> t[2][0] = 'X'
>>> t[2][1] = 'Y'
>>> t
('a', 'b', ['X', 'Y'])
```

当我们把list的元素`'A'`和`'B'`修改为`'X'`和`'Y'`后，tuple变为：

![tuple-1](https://www.liaoxuefeng.com/files/attachments/923973647515872/0)

tuple所谓的“不变”是说，tuple的每个元素，指向永远不变。即指向`'a'`，就不能改成指向`'b'`，指向一个list，就不能改成指向其他对象，但指向的这个list本身是可变的！

#### 1.2.3dict字典的使用

1. get(key) 查
2. pop(key) 删
3. update(key=value) 增、改





Python内置了字典：dict的支持，dict全称dictionary，在其他语言中也称为map，使用键-值（key-value）存储，具有极快的查找速度。

用Python写一个dict如下：

```
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
```

除了初始化时指定外，还可以**通过key插入元素：**

```
>>> d['Adam'] = 67  赋值：没有Adam这个key时会自动创建这个key，最终插入一个key-value
>>> d['Adam'] 
67
```

要**避免key不存在的错误**，有两种办法，一是**通过`in`判断key是否存在**：

```
>>> 'Thomas' in d
False
```

二是通过dict提供的**`get()`方法，**如果key不存在，可以返回`None`，或者自己指定的value：

```
>>> d.get('Thomas')
>>> d.get('Thomas', -1)
-1
```

**注意：**返回`None`的时候Python的交互环境不显示结果。

要**删除一个key**，用`pop(key)`方法，对应的value也会从dict中删除：

```
>>> d.pop('Bob')
75
>>> d
{'Michael': 95, 'Tracy': 85}
```

**注意：**要保证hash的正确性，作为key的对象就不能变。在Python中，字符串、整数等都是不可变的，因此，可以放心地作为key。而list是可变的，就不能作为key：

```
>>> key = [1, 2, 3]
>>> d[key] = 'a list'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
```

**对比：**

和list比较，dict有以下几个特点：

1. 查找和插入的速度极快，不会随着key的增加而变慢；
2. 需要占用大量的内存，内存浪费多。

而list相反：

1. 查找和插入的时间随着元素的增加而增加；
2. 占用空间小，浪费内存很少。

所以，dict是用空间来换取时间的一种方法。

#### 1.2.4set的使用

1. add() 增
2. remove() 删





set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。

要**创建一个set**，需要提供一个list作为输入集合：

```
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}
```

或者：

~~~python
basket = {'apple', 'orange', 'apple', 'pear', 'orange', 'banana'}
print('basket type=',type(basket))
# 输出结果：basket type= <class 'set'> 原因：集合的创建使用大括号{}，元素之间用逗号分隔。输出结果为<class 'set'>表示basket是一个set类型的对象。
~~~



**注意，**传入的参数`[1, 2, 3]`是一个list，而显示的`{1, 2, 3}`只是告诉你这个set内部有1，2，3这3个元素，显示的顺序也不表示set是有序的。

通过**`add(key)`方法**可以添加元素到set中，可以重复添加，但不会有效果：

```
>>> s.add(4)
>>> s
{1, 2, 3, 4}
>>> s.add(4)
>>> s
{1, 2, 3, 4}
```

通过**`remove(key)`方法**可以删除元素：

```
>>> s.remove(4)
>>> s
{1, 2, 3}
```

set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以**做数学意义上的交集、并集等操作**：

```
>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2   交集
{2, 3}
>>> s1 | s2   并集
{1, 2, 3, 4}
```

set和dict的唯一区别仅在于没有存储对应的value，但是，set的原理和dict一样，所以，**同样不可以放入可变对象(如list)，因为无法判断两个可变对象是否相等**，也就无法保证set内部“不会有重复元素”。试试把list放入set，会报错。

对于字符串不变对象：

```
>>> a = 'abc'
>>> b = a.replace('a', 'A')
>>> b
'Abc'
>>> a
'abc'
```

对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回，这样，就保证了不可变对象本身永远是不可变的，因为方法都不能对这个对象进行改变。

### 1.3序列的使用（简洁版本）

list是方括号[ ]，如L=[1,2,3]

tuple是圆括号( )，如t=(1,2,3)

dict是花括号{ }，如d={'张三':1, ’王五':2, '赵六':3}

set也是花括号{ }，如s={1,2,3}

**增删改查：**

**1、list**

1）查: L[1] L[0]   L(-1) #索引最后一个元素

2）增: L.append('赵六') #添加到最后一个位置  L.insert(2,'赵六') #添加到指定位置

3）删: L.pop() #删除最后一个位置的元素   L.pop(1) #删除指定位置的元素

4）改: L[1]='王丹'

5）空: L=[ ]

**2、Tuple：**

1）不能删改内容，索引方法和list相同

2）定义只有一个元素的tulpe： t=('张三',)

**3、dict：**

1）查：'李四' in d #查是否存在   d.get('李四') #不存在则无反馈 或d.get('李四',-1) #不存在返回-1

2）增/改：d['李四']=80

3）删：d.pop('李四')

**4、set**（一组key的合集，无value，不重复，无序）

1）增：s.add(4)

2）删：s.remove(4)

3）交集：s1&s2

2）并集：s1 I s2

### 1.4字符串的使用

- 替换: str.replace("/","")  表示把字符串中的"/"替换为""
- 清除前后的空格：str.strip()
- 将字符串分割成一个字符串列表 [] = str.split("/")



str:

**字符与整数的相互转换：**

~~~
>>> ord('A')
65
>>> chr(65)
'A'
~~~

**转换字符的编码：**

b'ABC'表示bytes，`bytes`的每个字符都只占用一个字节，以Unicode表示的`str`通过`encode()`方法可以编码为指定的`bytes`，例如：

```
>>> 'ABC'.encode('ascii')
b'ABC'
>>> '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'
>>> '中文'.encode('ascii')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)
```

**字符串格式化方式：**

1.在Python中，**字符串采用的格式化方式**和C语言是一致的，用`%`实现，举例如下：

```
>>> 'Hello, %s' % 'world'  如果只有一个%?，括号可以省略。
'Hello, world'   
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
```

2.**另一种格式化字符串的方法是使用字符串的`format()`方法**，它会用传入的参数依次替换字符串内的占位符`{0}`、`{1}`……:

```
>>> 'Hello, {0}, 成绩提升了 {1}%'.format('小明', 17.125)
'Hello, 小明, 成绩提升了 17.125%'
```

3.最后一种格式化字符串的方法是**使用以`f`开头的字符串，称之为`f-string`**，它和普通字符串不同之处在于，字符串如果包含`{xxx}`，就会以对应的变量替换：

```
>>> r = 2.5
>>> s = 3.14 * r ** 2
>>> print(f'The area of a circle with radius {r} is {s:.2f}') :.2f表示保留两位小数
The area of a circle with radius 2.5 is 19.62
```



### 1.5 使用utf-8进行编码

当Python解释器读取源代码时，为了让它按UTF-8编码读取，我们通常在文件开头写上这两行：

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```

第一行注释是为了告诉Linux/OS X系统，这是一个Python可执行程序，Windows系统会忽略这个注释；

第二行注释是为了告诉Python解释器，按照UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码。

### 1.6函数

函数名其实就是**指向一个函数对象的引用**，完全可以把函数名赋给一个变量，相当于给这个函数起了一个“别名”：

```
>>> a = abs # 变量a指向abs函数
>>> a(-1) # 所以也可以通过a调用abs函数
1
```



在Python中，**定义一个函数**要使用`def`语句，依次写出函数名、括号、括号中的参数和冒号`:`，然后，在缩进块中编写函数体，函数的返回值用`return`语句返回。

我们以自定义一个求绝对值的`my_abs`函数为例：

~~~python
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
~~~

**从py文件中import函数：**

```ascii
┌────────────────────────────────────────────────────────┐
│Command Prompt - python                           - □ x │
├────────────────────────────────────────────────────────┤
│>>> from abstest import my_abs    
表示从abstest.py文件中导入my_abs函数
│>>> my_abs(-9)                                          │
│9                                                       │
│>>> _                                                   │
│                                                        │
│                                                        │
│                                                        │
│                                                        │
│                                                        │
│                                                        │
│                                                        │
```

#### 1.6.1 数据类型检查 isinstance

可以用内置函数`isinstance()`实现：

```
def my_abs(x):
    if not isinstance(x, (int, float)):
        raise TypeError('bad operand type')
    if x >= 0:
        return x
    else:
        return -x
```

#### 1.6.2 return多个值：

Python的函数返回多值其实就是返回一个tuple，但写起来更方便。函数执行完毕也没有`return`语句时，自动`return None`。

~~~
>>> r = move(100, 100, 60, math.pi / 6)   返回值： return nx, ny
>>> print(r)
(151.96152422706632, 70.0)
~~~

#### 1.6.3 默认参数的使用

 定义默认参数要牢记一点：默认参数必须指向不变对象！

先定义一个函数，传入一个list，添加一个`END`再返回：

反面例子：

```python
def add_end(L=[]):# L作为参数，即使函数运行结束也不会销毁，可以继续存在
    L.append('END')
    return L
```

再次调用`add_end()`时，结果就不对了：

```
>>> add_end()
['END', 'END']
>>> add_end()
['END', 'END', 'END']
```

要修改上面的例子，我们可以用`None`这个不变对象来实现：

```
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
```

原理：`str`、`None`这样的不变对象，不变对象一旦创建，对象内部的数据就不能修改。

#### 1.6.4 可变参数 *nums：

我们把函数的参数改为可变参数，可变参数在函数调用时**自动组装为一个tuple**：

```
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```

调用该函数时，可以传入任意个参数，包括0个参数：

```
>>> calc(1, 2)
5
>>> calc()
0
```

Python允许你在list或tuple前面加一个`*`号，把list或tuple的元素变成可变参数传进去：

```
>>> nums = [1, 2, 3]
>>> calc(*nums)
14
```

#### 1.6.5 关键字参数 **kw

关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。请看示例：

```
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
```

函数`person`除了必选参数`name`和`age`外，还接受关键字参数`kw`。在调用该函数时，可以只传入必选参数：

```
>>> person('Michael', 30)
name: Michael age: 30 other: {}
```

也可以传入任意个数的关键字参数：

```
>>> person('Bob', 35, city='Beijing')
name: Bob age: 35 other: {'city': 'Beijing'}
>>> person('Adam', 45, gender='M', job='Engineer')
name: Adam age: 45 other: {'gender': 'M', 'job': 'Engineer'}
```

可以用简化的写法：

```
>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, **extra)
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
```

`**extra`表示把`extra`这个dict的所有key-value用关键字参数传入到函数的`**kw`参数，`kw`将获得一个dict，注意`kw`获得的dict是`extra`的一份拷贝，对`kw`的改动不会影响到函数外的`extra`。

#### 1.6.6 命名关键字参数 *,xx,xx

如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收`city`和`job`作为关键字参数。这种方式定义的函数如下：

```
def person(name, age, *, city, job):
    print(name, age, city, job)
```

和关键字参数`**kw`不同，命名关键字参数需要一个特殊分隔符`*`，`*`后面的参数被视为命名关键字参数。

调用方式如下：

```
>>> person('Jack', 24, city='Beijing', job='Engineer')
Jack 24 Beijing Engineer
```

通用的调用方式：func(*args, **kw)

可变参数既可以直接传入：`func(1, 2, 3)`，又可以先组装list或tuple，再通过`*args`传入：`func(*(1, 2, 3))`；

关键字参数既可以直接传入：`func(a=1, b=2)`，又可以先组装dict，再通过`**kw`传入：`func(**{'a': 1, 'b': 2})`。

~~~python
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)
    
>>> args = (1, 2, 3, 4)
>>> kw = {'d': 99, 'x': '#'}
>>> f1(*args, **kw)
a = 1 b = 2 c = 3 args = (4,) kw = {'d': 99, 'x': '#'}
~~~

参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。

所以：**对于任意函数，都可以通过类似`func(*args, **kw)`的形式调用它**，无论它的参数是如何定义的。

### 1.7切片

trule,list,str都能切片

#### 1.7.1语法

比如，一个list如下：
>>> L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']
>>> L[0:3]表示，从索引0开始取，直到索引3为止(左闭右开)，但不包括索引3。即索引0，1，2，正好是3个元素。

如果第一个索引是0，还可以省略：

>>> L[:3]
>>> ['Michael', 'Sarah', 'Tracy']

支持倒数切片，试试：

>>> L[-2:]
>>>
>>> ['Bob', 'Jack']
>>> L[-2:-1]
>>> ['Bob']

#### 1.7.2案例:

后10个数：

>>> L[-10:]
>>> [90, 91, 92, 93, 94, 95, 96, 97, 98, 99]
>>> 前11-20个数：

>>> L[10:20]
>>> [10, 11, 12, 13, 14, 15, 16, 17, 18, 19]
>>> 前10个数，每两个取一个：

>>> L[:10:2]
>>> [0, 2, 4, 6, 8]
>>> 所有数，每5个取一个：

>>> L[::5]
>>> [0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95]
>>> 甚至什么都不写，只写[:]就可以原样复制一个list：

>>> L[:]
>>> [0, 1, 2, 3, ..., 99]
>>>

### 1.8列表生成式
>>>
>>> 创建一个0-99的数列：

>>> L = list(range(100))
>>> L = [0,1,...,99]

但如果要生成[1x1, 2x2, 3x3, ..., 10x10]怎么做？方法一是循环：

>>> L = []
>>> for x in range(1, 11):
>>> ...    L.append(x * x)
>>> ...
>>> L
>>> [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
>>> 但是循环太繁琐，而列表生成式则可以用一行语句代替循环生成上面的list：

>>> [x * x for x in range(1, 11)]
>>> [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
>>> 写列表生成式时，把要生成的元素x * x放到前面，后面跟for循环，就可以把list创建出来，十分有用，多写几次，很快就可以熟悉这种语法。

for循环后面还可以加上if判断，这样我们就可以筛选出仅偶数的平方：

>>> [x * x for x in range(1, 11) if x % 2 == 0]
>>> [4, 16, 36, 64, 100]
>>> 还可以使用两层循环，可以生成全排列：

>>> [m + n for m in 'ABC' for n in 'XYZ']
>>> ['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
>>> 三层和三层以上的循环就很少用到了。

[0, 1, 2, 3, ..., 99]

案例:要求:
输入:  L1 = ['Hello', 'World', 18, 'Apple', None]
输出:  L2 == ['hello', 'world', 'apple']
L2=?
解答:  L2=[x.lower() for x in L1 if isinstance(x,str)]

**在一个列表生成式中，for前面的if ... else是表达式，而for后面的if是过滤条件，不能带else。**
**先执行后面的if筛选条件结果为true则保留再执行前面的表达式**

### **1.9 迭代**

dict就可以迭代：

>>> d = {'a': 1, 'b': 2, 'c': 3}
>>> for key in d:
>>> ...     print(key)
>>> ...
>>> a
>>> c
>>> b
>>> 因为dict的存储不是按照list的方式顺序排列，所以，迭代出的结果顺序很可能不一样。

默认情况下，dict迭代的是key。如果要迭代value，可以用for value in d.values()，如果要同时迭代key和value，可以用for k, v in d.items()。

如果要对list实现类似Java那样的下标循环怎么办？Python内置的enumerate函数可以把一个list变成索引-元素对，这样就可以在for循环中同时迭代索引和元素本身：

>>> for i, value in enumerate(['A', 'B', 'C']):
>>> ...     print(i, value)
>>> ...
>>> 0 A
>>> 1 B
>>> 2 C
>>> 4.

### 1.19 高阶函数

可以接收函数作为参数的函数

#### 1.map()

`map()`函数接收两个参数，一个是函数，一个是`Iterable`，`map`将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回。

#### 2.filter()

和`map()`类似，`filter()`也接收一个函数和一个序列。和`map()`不同的是，`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`True`还是`False`决定保留还是丢弃该元素。

例如，在一个list中，删掉偶数，只保留奇数，可以这么写：

```
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
# 结果: [1, 5, 9, 15]
```

#### 3.reduce()

`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收两个参数，`reduce`把结果继续和序列的下一个元素做累积计算，其效果就是：

```
reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
```

比方说对一个序列求和，就可以用`reduce`实现：

```
>>> from functools import reduce
>>> def add(x, y):
...     return x + y
...
>>> reduce(add, [1, 3, 5, 7, 9])
25
```

#### 4.sorted()

Python内置的`sorted()`函数就可以对list进行排序：

```
>>> sorted([36, 5, -12, 9, -21])
[-21, -12, 5, 9, 36]
```

此外，`sorted()`函数也是一个高阶函数，它还**可以接收一个`key`函数**来实现自定义的排序，例如按绝对值大小排序：

```
>>> sorted([36, 5, -12, 9, -21], key=abs)
[5, 9, -12, -21, 36]
```

key指定的函数将作用于list的每一个元素上，并根据key函数返回的结果进行排序。对比原始的list和经过`key=abs`处理过的list：

```
list = [36, 5, -12, 9, -21]

keys = [36, 5,  12, 9,  21]
```

然后`sorted()`函数按照keys进行排序，并按照对应关系返回list相应的元素：

```ascii
keys排序结果 => [5, 9,  12,  21, 36]
                |  |    |    |   |
最终结果     => [5, 9, -12, -21, 36]
```

我们再看一个字符串排序的例子：

```
>>> sorted(['bob', 'about', 'Zoo', 'Credit'])
['Credit', 'Zoo', 'about', 'bob']
```

默认情况下，对字符串排序，是按照ASCII的大小比较的，由于`'Z' < 'a'`，结果，大写字母`Z`会排在小写字母`a`的前面。

现在，我们提出排序应该忽略大小写，按照字母序排序。要实现这个算法，不必对现有代码大加改动，只要我们能用一个key函数把字符串映射为忽略大小写排序即可。忽略大小写来比较两个字符串，实际上就是先把字符串都变成大写（或者都变成小写），再比较。

这样，我们给`sorted`传入key函数，即可实现忽略大小写的排序：

```
>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower)
['about', 'bob', 'Credit', 'Zoo']
```

要进行反向排序，不必改动key函数，可以传入**第三个参数`reverse=True`：**

```
>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)
['Zoo', 'Credit', 'bob', 'about']
```

## 2 各种api

### collections的Counter 计数器

计数：

~~~python
from collections import Counter

def find_most_frequent_strings(strings):
    # 使用Counter来计数,计算字符串列表中每个字符串出现的次数
    counter = Counter(strings)

    # 找到出现次数最多的字符串和次数
    most_common = counter.most_common()

    # most_common返回的是一个列表，列表的元素是元组，第一个元素是字符串，第二个是次数
    most_frequent_string, most_frequent_count = most_common[0]
    # 找到出现次数最少的字符串和次数
    # return counter.most_common()[-1]

    return most_frequent_string, most_frequent_count, dict(counter) # 把字符串与数量存储到字典中
~~~
