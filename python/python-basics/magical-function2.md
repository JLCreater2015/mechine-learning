# 魔法函数（二）

## 11、类的构造、删除 <a id="11&#x7C7B;&#x7684;&#x6784;&#x9020;&#x5220;&#x9664;"></a>

```python
object.__new__(self, ...)
object.__init__(self, ...)
object.__del__(self)
```

## 12、二元操作符 <a id="12&#x4E8C;&#x5143;&#x64CD;&#x4F5C;&#x7B26;"></a>

```python
+   object.__add__(self, other)
-   object.__sub__(self, other)
*   object.__mul__(self, other)
//  object.__floordiv__(self, other)
/   object.__div__(self, other)
%   object.__mod__(self, other)
**  object.__pow__(self, other[, modulo])
<<  object.__lshift__(self, other)
>>  object.__rshift__(self, other)
&   object.__and__(self, other)
^   object.__xor__(self, other)
|   object.__or__(self, other)
```

## 13、扩展二元操作符 <a id="13&#x6269;&#x5C55;&#x4E8C;&#x5143;&#x64CD;&#x4F5C;&#x7B26;"></a>

```python
+=  object.__iadd__(self, other)
-=  object.__isub__(self, other)
*=  object.__imul__(self, other)
/=  object.__idiv__(self, other)
//= object.__ifloordiv__(self, other)
%=  object.__imod__(self, other)
**= object.__ipow__(self, other[, modulo])
<<= object.__ilshift__(self, other)
>>= object.__irshift__(self, other)
&=  object.__iand__(self, other)
^=  object.__ixor__(self, other)
|=  object.__ior__(self, other)
```

## 14、一元操作符 <a id="14&#x4E00;&#x5143;&#x64CD;&#x4F5C;&#x7B26;"></a>

```python
-   object.__neg__(self)
+   object.__pos__(self)
abs()   object.__abs__(self)
~  object.__invert__(self)
complex()   object.__complex__(self)
int()   object.__int__(self)
long()  object.__long__(self)
float() object.__float__(self)
oct()   object.__oct__(self)
hex()   object.__hex__(self)
round() object.__round__(self, n)
floor() object__floor__(self)
ceil()  object.__ceil__(self)
trunc() object.__trunc__(self)
```

## 15、比较函数 <a id="15&#x6BD4;&#x8F83;&#x51FD;&#x6570;"></a>

```python
<   object.__lt__(self, other)
<=  object.__le__(self, other)
==  object.__eq__(self, other)
!=  object.__ne__(self, other)
>=  object.__ge__(self, other)
>   object.__gt__(self, other)
```

## 16、类的表示、输出 <a id="16&#x7C7B;&#x7684;&#x8868;&#x793A;&#x8F93;&#x51FA;"></a>

```python
str()   object.__str__(self) 
repr()  object.__repr__(self)
len()   object.__len__(self)
hash()  object.__hash__(self) 
bool()  object.__nonzero__(self) 
dir()   object.__dir__(self)
sys.getsizeof() object.__sizeof__(self)
```

## 17、类容器 <a id="17&#x7C7B;&#x5BB9;&#x5668;"></a>

```python
len()   object.__len__(self)
self[key]   object.__getitem__(self, key)
self[key] = value   object.__setitem__(self, key, value)
del[key] object.__delitem__(self, key)
iter()  object.__iter__(self)
reversed()  object.__reversed__(self)
in操作    object.__contains__(self, item)
字典key不存在时   object.__missing__(self, key)
```

## 18、协程 <a id="18&#x534F;&#x7A0B;"></a>

```python
__await__()
__aiter__()
__anext__()
__aenter__()
__aexit__()
```

