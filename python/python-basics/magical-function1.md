# 魔法函数（一）

## 1、魔法函数 <a id="1&#x9B54;&#x6CD5;&#x51FD;&#x6570;"></a>

所谓魔法函数（Magic Methods），是Python的一种高级语法，允许你在类中自定义函数（函数名格式一般为**`__xx__`**），并绑定到类的特殊方法中。比如在类A中自定义**`__str__()`**函数，则在调用`str(A())`时，会自动调用**`__str__()`**函数，并返回相应的结果。在我们平时的使用中，可能经常使用**`__init__()`**函数和**`__del__()`**函数，其实这也是魔法函数的一种。

1. 魔法函数是python内置的，具有双下划线开头结尾的特性。
2. 自定义的类中使用魔法函数，该类就具有了该魔法函数的功能，比如使用**`__iter__()`**魔法函数，该类就具有迭代功能。
3. 使用魔法函数实现高灵活性，实现自己所需要的独特的数据类型。

![](../../.gitbook/assets/image%20%2859%29.png)

## 2、字符串表示 <a id="2&#x5B57;&#x7B26;&#x4E32;&#x8868;&#x793A;"></a>

```python
object.__repr__()   # 格式化字符串式样，主用开发模式下
object.__str__()    # 常用的字符串，格式化字符串
```

**程序清单 1：**

```python
class magic:
    def __init__(self, num):
        self.num = num
    def __str__(self):
        return (self.num + '\n')*3
a = magic('这是 magic...')
print a
```

输出：

```text
这是 magic...
这是 magic...
这是 magic...
```

这里使用`print(str(a))`和使用 `print(a.__str__())`效果是一样的。同理`repr(a)`和`a.__repr__()`

区别：

```python
__repr__ # 目的是为了表示清楚，是为开发者准备的。
__str__  # 目的是可读性好，是为使用者准备的。
__repr__ # 应该尽可能的表示出一个对象来源的类以及继承关系，方便程序员们了解这个对象。
# 而 __str__ 就简单的表示对象，而不要让不懂编程的以为输出的是 bug。
```

**同时定义 repr 方法和 str 方法时，print\(\) 方法会调用 str 方法。**

## 3、集合序列 <a id="3&#x96C6;&#x5408;&#x5E8F;&#x5217;"></a>

```text
__len__()
__getitem__()
__setitem__()
__delitem__()
__contains__()
```

## 4、迭代 <a id="4&#x8FED;&#x4EE3;"></a>

```text
__iter__()
__next__()
```

## 5、可调用 <a id="5&#x53EF;&#x8C03;&#x7528;"></a>

```text
__call__()
```

## 6、with上下文管理 <a id="6with&#x4E0A;&#x4E0B;&#x6587;&#x7BA1;&#x7406;"></a>

```python
__enter__(self)	
# 1. 定义当使用 with 语句时的初始化行为
# 2. __enter__ 的返回值被 with 语句的目标或者 as 后的名字绑定

__exit__(self, exc_type, exc_value, traceback)	
# 1. 定义当一个代码块被执行或者终止后上下文管理器应该做什么
# 2. 一般被用来处理异常，清除工作或者做一些代码块执行完毕之后的日常工作
```

## 7、数值转换 <a id="7&#x6570;&#x503C;&#x8F6C;&#x6362;"></a>

```python
__complex__(self)	# 定义当被 complex() 调用时的行为（需要返回恰当的值）
__int__(self)	    # 定义当被 int() 调用时的行为（需要返回恰当的值）
__float__(self)	  # 定义当被 float() 调用时的行为（需要返回恰当的值）
__round__(self[, n])	# 定义当被 round() 调用时的行为（需要返回恰当的值）
__index__(self)	
# 1. 当对象是被应用在切片表达式中时，实现整形强制转换
# 2. 如果你定义了一个可能在切片时用到的定制的数值型,你应该定义 __index__
# 3. 如果 __index__ 被定义，则 __int__ 也需要被定义，且返回相同的值
```

## 8、属性相关 <a id="9&#x5C5E;&#x6027;&#x76F8;&#x5173;"></a>

```python
__getattr__(self, name)	      # 定义当用户试图获取一个不存在的属性时的行为
__getattribute__(self, name)	# 定义当该类的属性被访问时的行为
__setattr__(self, name, value)	# 定义当一个属性被设置时的行为
__delattr__(self, name)	        # 定义当一个属性被删除时的行为
__dir__(self)	                  # 定义当 dir() 被调用时的行为
__get__(self, instance, owner)	# 定义当描述符的值被取得时的行为
__set__(self, instance, value)	# 定义当描述符的值被改变时的行为
__delete__(self, instance)	    # 定义当描述符的值被删除时的行为
__bool__(self)	              # 定义当被 bool() 调用时的行为，应该返回 True 或 False
__format__(self, format_spec)	# 定义当被 format() 调用时的行为
```

## 9、属性

```python
__module__()
__class__()
__dict__()
```

