# 操作符

## ✏ 1、@ <a id="1"></a>

### 🖋 1.1、做函数修饰符 <a id="11&#x505A;&#x51FD;&#x6570;&#x4FEE;&#x9970;&#x7B26;"></a>

`'@'`符号用作函数修饰符是python2.4新增加的功能，修饰符必须出现在函数定义前一行，不允许和函数定义在同一行。只可以在模块或类定义层内对函数进行修饰，不允许修修饰一个类。一个修饰符就是一个函数，它将被修饰的函数做为参数，并返回修饰后的同名函数或其它可调用的东西。

```python
def makebold(fn):
    def warp():
        return "<a>"+fn()+"<a>"
    return warp
def makeitalic(fn):
    def warp():
        return "<b>"+fn()+"<b>"
    return warp
    
@makebold         #这句话相当于makebold(test1),也就是把当前函数传过去
def test1():
    return "test1"
@makeitalic
def test2():
    return "test2"
@makebold
@makeitalic
def test3():     #函数和装饰器是倒着执行的，从下往上
    return "test3"
print(test1())
print(test2())
print(test3())
```

输出结果：

```python
<a>test1<a>
<b>test2<b>
<a><b>test3<b><a>
```

### 🖋 1.2、表示矩阵乘法 <a id="12&#x8868;&#x793A;&#x77E9;&#x9635;&#x4E58;&#x6CD5;python35&#x65B0;&#x589E;&#x52A0;&#x7684;&#x529F;&#x80FD;"></a>

python3.5新增加的功能

## ✏ 2、-&gt; <a id="2"></a>

**作用：给函数参数增加元信息**

使用函数参数注解能提示程序员应该怎样正确使用这个函数。 例如，下面有一个被注解了的函数：

```python
def add(x:int, y:int) -> int:
    return x + y
```

> python解释器不会对这些注解添加任何的语义。它们不会被类型检查，运行时跟没有加注解之前的效果也没有任何差距。 然而，对于那些阅读源码的人来讲就很有帮助啦。第三方工具和框架可能会对这些注解添加语义。同时它们也会出现在文档中。

```python
>>> help(add)
Help on function add in module __main__:
add(x: int, y: int) -> int
>>>
```

尽管你可以使用任意类型的对象给函数添加注解\(例如数字，字符串，对象实例等等\)，不过通常来讲使用类或者字符串会比较好点。

**注：函数注解只存储在函数的 \_\_annotations\_\_ 属性中。例如：**

```python
>>> add.__annotations__
{'y': <class 'int'>, 'return': <class 'int'>, 'x': <class 'int'>}
```

**优点：**

```python
def f(ham: str, eggs: str = 'eggs') -> str :
    print("Annotations:", f.__annotations__)
    print("Arguments:", ham, eggs)
    return ham + ' and ' + eggs
print(f("test","abc"))
```

运行结果：

```python
Annotations:{'ham':<class 'str'>,'eggs':<class 'str'>,'return':<class 'str'>}
Arguments:test abc
test and abc
>>>
```

如果参数为（"test",123），则运行结果如下：

```python
Annotations:{'ham':<class 'str'>,'eggs':<class 'str'>,'return':<class 'str'>}
Arguments:test abc
Traceback (most recent call last)
    File "D:\MyPythonFile\Test.py",line 6,in <module>
        print(f("test",123))
    File "D:\MyPythonFile\Test.py",line 4,in f
        return ham + 'and' + eggs
TypeError: can only concatenate str (not "int") to str
```

原因在于 int 型不能参与字符串拼接，其实这里并没有指定类型 只是写函数的人提醒用函数的人最好传什么类型的参数，因为最后需要两个参数进行字符串拼接；当然，也可以直接写字符串提醒：

```python
def f(ham: "传一个字符串", eggs: str = 'eggs') -> str :
    print("Annotations:", f.__annotations__)
    print("Arguments:", ham, eggs)
    return ham + ' and ' + eggs
print(f("test","abc"))
```

## ✏ 3、\* 、\*\*

\* 与 \*\* 操作符的用法逐年增加， Python 3 为这些操作符添加了许多新用途。

### 🖋 1、我们将要讨论的东西

当我在这篇文章中讨论 \* 和 \*\* 时，涉及的是 \* 和 \*\* 前缀操作符，而不是中缀操作符。

所以我不是在谈论乘法和取幂：

```python
2 * 5
2 ** 5  
```

我们将要讨论的是 \* 和 \*\* 前缀操作符，也就是在一个变量之前使用 \* 和 \*\* 操作符。例如：

```python
1234numbers = [2, 1, 3, 4numbers = [2, 1, 3, 4, 7]
more_numbers = [*numbers, 11, 18]
print(*more_numbers, sep=', ')
2, 1, 3, 4, 7, 11, 18﻿  
```

#### \* 和 \*\* 的用法包括：

* 使用 \* 和 \*\* 向函数传递参数
* 使用 \* 和 \*\* 捕捉传递至函数的参数
* 使用 \* 接受 keyword-only 参数
* 使用 \* 捕捉元组解包过程中的项
* 使用 \* 将可迭代对象解包至列表/元组
* 使用 \*\* 将字典解包至其他字典

即使你认为你已经熟悉了所有这些使用 \* 和 \*\* 的方法，我仍建议再看后文的每一个代码块，以确保它们是你熟悉的一切内容。Python 核心开发人员在过去几年里陆续向这些操作符添加了新功能，因此很容易忽略 \* 和 \*\* 的一些新用法。

### 🖋 2、在函数调用中使用 \* 解包参数

当调用函数时，\* 操作符可用于将可迭代对象解包至函数调用的参数中：

```python
>>> lst = [1, 2, 3, 4]
>>> print(lst[0], lst[1], lst[2], lst[3])
1 2 3 4
>>> print(*lst)
1 2 3 4﻿​
```

`print(*fruit)` 行将 fruits 列表中的所有项作为独立的参数传递到 print 函数调用中，甚至不需要知道列表中有多少个参数。

这里的 \* 操作符不仅仅是语法糖。除非列表的长度是固定的，否则如果没有 \*，就不可能将特定的可迭代对象中的所有项作为独立的参数传入函数。

\*\* 操作符类似，但是使用关键字参数。\*\* 操作符允许我们使用键-值对字典，并在函数调用中将其解包为关键字参数。

```python
>>> dic = {'a': 1, 'b': 2, 'c': 3}
>>> string = "{a}-{b}-{c}".format(**dic)
>>> string
'1-2-3'﻿
```

根据我的经验，使用 \*\* 将关键字参数解包到函数调用中并不常见。我最常看到的是在实践实施继承时：对 `super()` 的调用通常包含 \* 和 \*\*。在函数调用中，可以多次使用 \* 和 \*\*，多次使用 \* 有时会很方便：

```python
>>> strlist = ['a', 'b', 'c', 'd']
>>> numbers = [2, 1, 3, 4, 7]
>>> print(*numbers, *strlist)
2 1 3 4 7 a b c d﻿
```

多次使用 \*\* 类似：

```python
>>> dic = {'a': 1, 'b': 2, 'c': 3}
>>> dic2 = {'e': "Python", 'f': 'everyday'}
>>> string = "{a}-{b}-{c}-{e}-{f}".format(
...     **dic,
...     **dic2,
... )
>>> string
'1-2-3-Python-everyday'﻿​
```

但是，在多次使用 \*\* 时需要小心。Python 中的函数不能多次指定相同的关键字参数，因此与 \*\* 一起使用的每个字典中的键必须是不同的，否则会引发异常。

### 🖋 3、在函数定义中使用 \* 收集参数

在定义函数时，可以使用 \* 操作符捕捉传递给函数的无限数量的位置参数。这些参数被捕捉到一个元组中。

```python
def add(*num):
   return sum(num)
>>> add(1)
1
>>> add(1, 2)
3
>>> add(1, 2, 3)
6﻿​
```

\* 的这种参数收集用法允许我们创建自己的函数，它可以接受任意数量的参数。

\*\* 操作符还有另一个用法：在定义函数时，我们可以使用 \*\* 将传递给函数的任何关键字参数捕捉到字典中：

```python
def pack(**kargs):
   for key, value in kargs.items():
       print(key, value)﻿​
```

\*\* 将捕捉我们传递给该函数的任何关键字参数，并将参数引用到字典中。

```python
>>> pack(a=1)
a 1
>>> pack(a=1, b=2)
a 1
b 2
>>> pack(a=1, b=2, c=3)
a 1
b 2
c 3﻿​
```

### 🖋 4、keyword-only 参数

在 Python 3 中，有一种特殊的语法来接受 keyword-only 参数。Keyword-only 参数是只能使用关键字语法指定的函数参数，这意味着它们不能使用位置指定参数。为了接受 keyword-only 参数，在定义函数时，我们可以在 `*args` 参数后，`**kargs` 参数前加上命名参数：

```python
def kwonly(*args, key, **kargs):    # key 即为 keyword-only 参数
   print(args, key, kargs)
>>> kwonly(1, 2, key=3, d=4, e=5)
(1, 2) 3 {'d': 4, 'e': 5}﻿​
```

如果试图使用位置参数指定 key，那么会引发一个错误：

```python
>>> kwonly(1, 2, 3, d=4, e=5)
Traceback (most recent call last):
 File "<stdin>", line 1, in <module>
TypeError: kwonly() missing 1 required keyword-only argument: 'key'﻿​
```

keyword-only 参数的特性很酷，但是如果你希望在不捕获无限位置参数的情况下需要 keyword-only 参数呢？Python 允许这样做，但它的语法有点奇怪，仅使用一个 \*，\* 后面的所有参数都将作为关键字传递：

```python
def kwonly(a, *, b, c):
   print(a, b, c)
>>> kwonly(1, b=2, c=3)
1 2 3﻿​ 
```

因此不能不指定关键字：

```python
>>> kwonly(1, 2, 3)
Traceback (most recent call last):
 File "<stdin>", line 1, in <module>
TypeError: kwonly() takes 1 positional argument but 3 were given﻿​
```

我通常在获取任意数量的位置参数时使用 keyword-only 参数，但我有时使用 \* 来强制一个参数只通过位置指定。

Python 的内置 `sorted` 函数实际上使用了这种方法。如果你查看 `sorted` 的帮助信息，你将看到以下信息：

```python
>>> help(sorted)
Help on built-in function sorted in module builtins:
 
sorted(iterable, /, *, key=None, reverse=False)
   Return a new list containing all items from the iterable in ascending order.
 
   A custom key function can be supplied to customize the sort order, and the
   reverse flag can be set to request the result in descending order.﻿​
```

### 🖋 5、在元组解包中使用 \*

Python 3 还添加了一种使用 \* 操作符的新方法，这种方法只与定义函数与调用函数时使用 \* 的特性有关。

\* 操作符现在可以用于元组解包：

```python
>>> lst = [1, 2, 3, 4]
>>> a, b, *c = lst
>>> c
[3, 4]
>>> a, *c = lst
>>> c
[2, 3, 4]
>>> a, *b, c = lst
>>> b
[2, 3]﻿
```

通常当我教授 \* 的时候，我注意到你只能在一个多赋值调用中使用一个 \* 表达式。这在技术上是不正确的，因为可以在嵌套解包中使用两个（这篇文章中讨论了嵌套解包）：

```python
lst = ['Python', 'Java', 'PHP', 'C']
((a, *b), *c) = lst
>>> a
'P'
>>> b
['y', 't', 'h', 'o', 'n']
>>> c
['Java', 'PHP', 'C']﻿​
```

我从来没见过它有什么用处，我不认为我会推荐使用它。

### 🖋 6、在列表常量中使用 \*

Python 3.5 通过 PEP 448 引入了大量与 \* 相关的新特性。最大的新特性之一是能够使用 \* 将可迭代对象转储到新列表中。

假设你有一个函数，它取任意序列并返回一个序列和反转的序列合并的列表：

```python
def revlist(seq):
   return list(seq) + list(reversed(seq))  
```

这个函数需要多次将内容转换为列表，以便连接列表并返回结果。在 Python 3.5 中，我们可以这样输入：

```python
def revlist(seq):
   return [*seq, *reversed(seq)]  
```

这段代码删除了一些不必要的列表调用，因此我们的代码更高效，可读性更好。

这是另一个例子：

```python
def rotate_first_item(seq):
   return [*seq[1:], seq[0]]  
```

这个函数返回一个新列表，其中给定列表（或其他序列）中的第一项移动到新列表的末尾。

使用 \* 操作符是将不同类型的可迭代对象连接在一起的好方法。\* 操作符适用于任何可迭代对象，而 + 操作符只适用于所有类型都相同的特定序列。

这不仅仅局限于创建列表。我们还可以将可迭代对象转储到新的元组或集合中：

```python
>>> lst = [1, 2, 3, 4]
>>> (*lst[1:], lst[0])
(2, 3, 4, 1)
>>> squared_list = (a ** 2 for a in lst)
>>> {*lst, *squared_list}
{1, 2, 3, 4, 9, 16}﻿​
```

### 🖋 7、在字典常量中使用 \*\*

PEP 448 还扩展了 \*\* 的功能，允许该操作符将键/值对从一个字典转储到一个新字典中：

```python
>>> dic = {'a': 1, 'b': 2, 'c': 3}
>>> dic2 = {'e': "Python", 'f': 'everyday'}
>>> all_dic = {**dic, **dic2}
>>> all_dic
{'a': 1, 'b': 2, 'c': 3, 'e': 'Python', 'f': 'everyday'}﻿
```

这不仅可以用于合并两个字典，还可以复制一个字典，同时添加一个新的值：

```python
>>> add_dic = {**dic, 'g': 4}
>>> add_dic
{'a': 1, 'b': 2, 'c': 3, 'g': 4}﻿​
```

或复制/合并字典，同时重写特定的值：

```python
>>> new_dic = {**dic, 'a': 111}
>>> new_dic
{'a': 111, 'b': 2, 'c': 3}﻿​
```

Python 的 \* 和 \*\* 操作符不仅仅是语法糖。它们允许你做的一些事情可以通过其他方式实现，但是替代 \* 和 \*\* 的方法往往更麻烦，且需要消耗更多资源。而且没有它们提供的一些特性，一些操作不可能实现：例如，没有 \* 的函数无法接受任意数量的未知参数。

