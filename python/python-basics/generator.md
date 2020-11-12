# 生成器

## ✏ 1、生成器generator <a id="1&#x751F;&#x6210;&#x5668;generator"></a>

   
python中有两种类型的生成器:生成器函数和生成器表达式。  
   
生成器和迭代器的功能非常相似，它也会提供 `__next__()` 方法，这意味着程序同样可调用内置的 `next()` 函数来获取生成器的下一个值，也可使用 for 循环来遍历生成器。  
   
生成器与迭代器的区别在于，迭代器通常是先定义一个迭代器类，然后通过创建实例来创建迭代器，我们在实现一个迭代器时，关于当前迭代到的状态需要我们自己记录，进而才能根据当前状态生成下一个数据；而生成器则是先定义一个包含 `yield` 语句的函数，然后通过调用该函数来创建生成器。生成器是一类特殊的迭代器。

## ✏ 2、生成器表达式 <a id="2&#x751F;&#x6210;&#x5668;&#x8868;&#x8FBE;&#x5F0F;"></a>

1、列表推导式\(list comprehension\)：

```python
>>> numbers = [1, 2, 3, 4, 5, 6]
>>> [x*x for x in numbers]
[1, 4, 9, 16, 25, 36]
```

2、集合推导式\(set comprehension\)

```python
>>> {x*x for x in numbers}
set([1, 36, 9, 16, 25, 4])
```

3、字典推导式\(dict comprehension\)

```python
>>> {x:x*x for x in numbers}
{1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36}
```

4、生成器表达式

```python
>>> gen = (x * x for x in numbers)
>>> gen
<generator object <genexpr> at 0x10a351b90> #这就是生成器generator和一个内存地址
>>> next(gen)
1
>>> list(gen)
[4, 9, 16, 25, 36]
```

（从python 2.5以后，添加了基于生成器构建的并发框架--协程）

## ✏ 3、生成器函数——`yeild` <a id="3&#x751F;&#x6210;&#x5668;&#x51FD;&#x6570;yeild"></a>

字典为动词“to yield”给出了两个释义：产出和让步。对于 Python 生成器中的 yield 来说，这两个含义都成立。yield item 这行代码会产出一个值，提供给 next\(...\) 的调用方；此外，还会作出让步，暂停执行生成器，让调用方继续工作，直到需要使用另一个值时再调用 next\(\)，调用方会从生成器中拉取值。

### 3.1、使用：实例 <a id="31&#x4F7F;&#x7528;&#x5B9E;&#x4F8B;"></a>

斐波那契（Fibonacci）數列是一个非常简单的递归数列，除第一个和第二个数外，任意一个数都可由前两个数相加得到。用计算机程序输出斐波那契數列的前 N 个数是一个非常简单的问题，许多初学者都可以轻易写出如下函数：

**清单 1. 简单输出斐波那契數列前 N 个数**

```python
def fab(max):
    n, a, b = 0, 0, 1    
    while n < max:
            print b        
            a, b = b, a + b        
            n = n + 1
```

执行 fab\(5\)，我们可以得到如下输出：

```python
>>> fab(5) 1 1 2 3 5
```

结果没有问题，但直接在 fab 函数中用 `print` 打印数字会导致该函数可复用性较差，因为 fab 函数返回 None，其他函数无法获得该函数生成的数列。要提高 fab 函数的可复用性，最好不要直接打印出数列，而是返回一个 List。以下是 fab 函数改写后的第二个版本：

**清单 2. 输出斐波那契數列前 N 个数第二版**

```python
def fab(max):
    n, a, b = 0, 0, 1    
    L = []    
    while n < max:
            L.append(b)        
            a, b = b, a + b        
            n = n + 1    
    return L
```

可以使用如下方式打印出 fab 函数返回的 List：

```python
>>> for n in fab(5): 
...     print n 
... 
1 
1 
2 
3 
5
```

改写后的 fab 函数通过返回 `List` 能满足复用性的要求，但是该函数在运行中占用的内存会随着参数 max 的增大而增大，如果要控制内存占用，最好不要用 List来保存中间结果，而是通过 iterable 对象来迭代。例如，在 Python2.x 中，代码：

**清单 3. 通过 iterable 对象来迭代**

```python
for i in range(1000): pass
```

会导致生成一个 1000 个元素的 List，而代码：

```python
for i in xrange(1000): pass
```

则不会生成一个 1000 个元素的 List，而是在每次迭代中返回下一个数值，内存空间占用很小。因为 `xrange` 不返回 List，而是返回一个 `iterable` 对象。

利用 iterable 我们可以把 fab 函数改写为一个支持 iterable 的 class，以下是第三个版本的 Fab：

**清单 4. 第三个版本**

```python
class Fab(object):
    def __init__(self, max):
            self.max = max        
            self.n, self.a, self.b = 0, 0, 1
                
    def __iter__(self):
            return self
            
    def next(self):
            if self.n < self.max:
                r = self.b            
                self.a, self.b = self.b, self.a + self.b            
                self.n = self.n + 1            
                return r        
            raise StopIteration()
```

Fab 类通过 `next()` 不断返回数列的下一个数，内存占用始终为常数：

```python
>>> for n in Fab(5): 
...     print n 
... 
1 
1 
2 
3 
5
```

然而，使用 class 改写的这个版本，代码远远没有第一版的 fab 函数来得简洁。如果我们想要保持第一版 fab 函数的简洁性，同时又要获得 iterable 的效果，yield 就派上用场了：

**清单 5. 使用 yield 的第四版**

```python
def fab(max):
     n, a, b = 0, 0, 1     
     while n < max:
              yield b         
              # print b         
              a, b = b, a + b         
              n = n + 1 
```

第四个版本的 fab 和第一版相比，仅仅把 print b 改为了 yield b，就在保持简洁性的同时获得了 iterable 的效果。  
   
调用第四版的 fab 和第二版的 fab 完全一致：

```python
>>> for n in Fab(5): 
...     print n 
... 
1 
1 
2 
3 
5
```

**简单地讲，yield 的作用就是把一个函数变成一个 generator，带有 yield 的函数不再是一个普通函数，Python 解释器会将其视为一个 generator，调用 fab\(5\) 不会执行 fab 函数，而是返回一个 iterable 对象！在 for 循环执行时，每次循环都会执行 fab 函数内部的代码，执行到 yield b 时，fab 函数就返回一个迭代值，下次迭代时，代码从 yield b 的下一条语句继续执行，而函数的本地变量看起来和上次中断执行前是完全一样的，于是函数继续执行，直到再次遇到 yield。**  
   
也可以手动调用 fab\(5\) 的 next\(\) 方法（因为 fab\(5\) 是一个 generator 对象，该对象具有 next\(\) 方法），这样我们就可以更清楚地看到 fab 的执行流程：

**清单 6. 执行流程**

```python
>>> f = fab(5) 
>>> f.next() 
1 
>>> f.next() 
1 
>>> f.next() 
2 
>>> f.next() 
3 
>>> f.next() 
5 
>>> f.next() 
Traceback (most recent call last): 
 File "<stdin>", line 1, in <module> 
StopIteration
```

当函数执行结束时，generator 自动抛出 StopIteration 异常，表示迭代完成。在 for 循环里，无需处理 StopIteration 异常，循环会正常结束。

### 3.2、特性 <a id="32&#x7279;&#x6027;"></a>

一个带有 yield 的函数就是一个 `generator`，它和普通函数不同，生成一个 `generator` 看起来像函数调用，但不会执行任何函数代码，直到对其调用 next\(\)（在 for 循环中会自动调用 next\(\)）才开始执行。虽然执行流程仍按函数的流程执行，但每执行到一个 yield 语句就会中断，并返回一个迭代值，下次执行时从 yield 的下一个语句继续执行。看起来就好像一个函数在正常执行的过程中被 yield 中断了数次，每次中断都会通过 yield 返回当前的迭代值。

```python
>>> def gen():
...     yield 
... 
>>> g = gen()
>>> g
<generator object gen at 0x10a351b40>
>>> dir(g)
['__class__', '__delattr__', '__doc__', '__format__', '__getattribute__', 
'__hash__', '__init__', '__iter__', '__name__', '__new__', '__reduce__', 
'__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', 
'__subclasshook__', 'close', 'gi_code', 'gi_frame', 'gi_running', 'next', 
'send', 'throw']
```

yield 的好处是显而易见的，把一个函数改写为一个 `generator` 就获得了迭代能力，比起用类的实例保存状态来计算下一个 `next()` 的值，不仅代码简洁，而且执行流程异常清晰。  
   
如何判断一个函数是否是一个特殊的 generator 函数？可以利用 isgeneratorfunction 判断：

**清单 7. 使用 isgeneratorfunction 判断**

```python
>>> from inspect import isgeneratorfunction 
>>> isgeneratorfunction(fab) 
True
```

要注意区分 fab 和 fab\(5\)，fab 是一个 generator function，而 fab\(5\) 是调用 fab 返回的一个 generator，好比类的定义和类的实例的区别：

**清单 8. 类的定义和类的实例**

```python
>>> import types 
>>> isinstance(fab, types.GeneratorType) 
False 
>>> isinstance(fab(5), types.GeneratorType) 
True
```

fab 是无法迭代的，而 fab\(5\) 是可迭代的：

```python
>>> from collections import Iterable 
>>> isinstance(fab, Iterable) 
False 
>>> isinstance(fab(5), Iterable) 
True
```

每次调用 fab 函数都会生成一个新的 generator 实例，各实例互不影响：

```python
>>> f1 = fab(3) 
>>> f2 = fab(5) 
>>> print 'f1:', f1.next() 
f1: 1 
>>> print 'f2:', f2.next() 
f2: 1 
>>> print 'f1:', f1.next() 
f1: 1 
>>> print 'f2:', f2.next() 
f2: 1 
>>> print 'f1:', f1.next() 
f1: 2 
>>> print 'f2:', f2.next() 
f2: 2 
>>> print 'f2:', f2.next() 
f2: 3 
>>> print 'f2:', f2.next() 
f2: 5
```

**return 的作用：**  
    1、在一个 generator function 中，如果没有 return，则默认执行至函数完毕，如果在执行过程中 return，则直接抛出 `StopIteration` 终止迭代，return的值将作为异常的解释。  
    2、外部可以通过generator.close\(\)函数手动关闭生成器，此后调用next或者send方法将抛出异常。  
   
**注意点：**

1. 当函数含有yield，会被系统默认为是一个生成器。
2. 执行到yield p， 返回p值以及整个生成器处于暂停的状态，并跳出当前函数，执行到调用返回值p的语句。
3. 当再次执行到这个含有yield的生成器函数时，会自动立即执行到上次暂停的位置继续执行，也就是从yield p这个语句继续执行。
4. 带有yield的函数不仅仅只用于for循环中，而且可用于某个函数的参数，只要这个函数的参数允许迭代参数。比如array.extend函数，它的原型是array.extend\(iterable\)。

### 3.3、对象生成器 <a id="33&#x5BF9;&#x8C61;&#x751F;&#x6210;&#x5668;"></a>

由于生成器本身就是迭代器，所以只能遍历一次。有一种方法可以定义可重用的生成器，就是定义一个基于对象的生成器。任何在`__iter__`方法中通过yield返回数据的类都可以用作对象生成器，注意是用作生成器，它并不是生成器。

**清单 9. 另一个 yield 的例子**

```python
>>> class counter(object):
...     def __init__(self, a, b):
...         self.a = a
...         self.b = b
...     def __iter__(self):
...         num = self.a
...         while self.b >= num:
...             yield num
...             num += 1
... 
>>> c = counter(1, 3)
>>> for i in c:
...     print i
... 
1
2
3
>>> for i in c:
...     print i
... 
1
2
3
```

### 3.4、生成器的send\(\)方法 <a id="34&#x751F;&#x6210;&#x5668;&#x7684;send&#x65B9;&#x6CD5;"></a>

当生成器运行起来之后，开发者还可以为生成器提供值，通过这种方式让生成器与“外部程序”动态地交换数据。为了实现生成器与“外部程序” 动态地交换数据，需要借助于生成器的 send\(\) 方法，该方法的功能与前面示例中所使用的 next\(\) 函数的功能非常相似，它们都用于获取生成器所生成的下一个值，并将生成器“冻结”在 yield 语句处；但 send\(\) 方法可以接收一个参数，该参数值会被发送给生成器函数。  
   
在生成器函数内部，程序可通过 yield 表达式来获取 send\(\) 方法所发送的值，这意味着此时程序应该使用一个变量来接收 yield 语句的值。如果程序依然使用 next\(\) 函数来获取生成器所生成的下一个值，那么 yield 语句返回 None。  
   
另外，需要说明的是，只有等到生成器被“冻结”之后，外部程序才能使用 send\(\) 方法向生成器发送数据。获取生成器第一次所生成的值，应该使用 next\(\) 函数；如果程序非要使用 send\(\) 方法获取生成器第一次所生成的值，也不能向生成器发送数据，只能为该方法传入 None 参数。

**清单 10. 另一个 yield 的例子**

```python
def square_gen(val):
    i = 0
    out_val = None
    while True:
        # 使用yield语句生成值，使用out_val接收send()方法发送的参数值
        out_val = (yield out_val ** 2) if out_val is not None else (yield i ** 2)
        # 如果程序使用send()方法获取下一个值，out_val会获取send()方法的参数
        if out_val is not None : print("====%d" % out_val)
        i += 1
sg = square_gen(5)
# 第一次调用send()方法获取值，只能传入None作为参数
print(sg.send(None))  # 0
print(next(sg))  # 1
print('--------------')
# 调用send()方法获取生成器的下一个值，参数9会被发送给生成器
print(sg.send(9))  # 81
# 再次调用next()函数获取生成器的下一个值
print(next(sg))  # 9
```

该程序第 6 行代码在 yield 语句（yield 语句被放在 if 表达式中，整个表达式只会运回一个 yield 语句）的左边放了一个变量，该变量就用于接受生成器 send\(\) 方法所发送的值。  
   
第一次使用生成器的 send\(\) 方法来获取生成器的下一个值，因此只能为 send\(\) 方法传入 None 作为参数。程序执行到第 9 行代码，由于此时 out\_val 为 None ，因此程序执行 yield i\*\*2（生成器返回 0），程序被“冻结”。注意，当程序被“冻结” 时，程序还未对 out\_val 变量赋值，因此看到第一次获取生成器的值为 0 。  
   
通过上面的执行过程不难看出，生成器根本不能获取第一次调用 send\(\) 方法发送的参数值，这就是 Python 要求生成器第一次调用 send\(\) 方法时只能发送 None 参数的原因。  
   
接下来程序调用 next\(sg\) 获取生成器的下一个值，程序从“冻结”处（对 out\_val 赋值）向下执行。由于此处调用 next\(\) 函数获取生成器的下一个值，因此 out\_val 被赋值为 None，所以程序执行 yield i\*\*2（生成器返回 1），程序再次被“冻结”。  
   
接下来程序调用 sg.send\(9\) 获取生成器的下一个值，程序从“冻结”处（对 out\_val 赋值）向下执行。由于此处调用 send\(9\) 方法获取生成器的下一个值，因此 out\_val 被赋值为 9，所以程序执行 yield out\_val\*\*2（生成器返回 81），程序再次被“冻结”。因此看到本次获取生成器的值为 81。  
   
程序再次调用 next\(sg\) 获取生成器的下一个值，程序从“冻结”处（对 out\_val 赋值）向下执行。由于此处调用 next\(\) 函数获取生成器的下一个值，因此 out\_val 被赋值为 None，所以程序执行 yield i\*\*2（此时 i 己经递增到 3，因此生成器返回 9），程序再次被“冻结”。因此看到本次获取生成器的值为 9。

### 3.5、其他方法 <a id="35&#x5176;&#x4ED6;&#x65B9;&#x6CD5;"></a>

1. close\(\)：该方法用于停止生成器。
2. throw\(\)：该方法用于在生成器内部（yield 语句内）引发一个异常。

```python
# 让生成器引发异常
sg.throw(ValueError)
```

运行上面代码，将看到如下输出结果：

```python
Traceback (most recent call last):
  File "C:\Users\mengma\Desktop\1.py", line 21, in <module>
    sg.throw(ValueError)
  File "C:\Users\mengma\Desktop\1.py", line 6, in square_gen
    out_val = (yield out_val ** 2) if out_val is not None else (yield i ** 2)
ValueError
```

### 3.6、用途举例 <a id="36&#x7528;&#x9014;&#x4E3E;&#x4F8B;"></a>

一个 yield 的例子来源于文件读取。如果直接对文件对象调用 read\(\) 方法，会导致不可预测的内存占用。好的方法是利用固定长度的缓冲区来不断读取文件内容。通过 yield，我们不再需要编写读文件的迭代类，就可以轻松实现文件读取：

**清单 11. 用 yield 读取文件内容的例子**

```python
def read_file(fpath): 
   BLOCK_SIZE = 1024 
   with open(fpath, 'rb') as f: 
       while True: 
           block = f.read(BLOCK_SIZE) 
           if block: 
               yield block 
           else: 
               return
```

### 3.7、yield from （Python3.3新出现的句法） <a id="37yield-from-python33&#x65B0;&#x51FA;&#x73B0;&#x7684;&#x53E5;&#x6CD5;"></a>

**见下一个笔记（Python生成器补充）**

## ✏ **4、**生成器进化成协程

