# Json & h5py

## ✏ 1、`json`文件操作 <a id="1json&#x6587;&#x4EF6;&#x64CD;&#x4F5C;"></a>

从 `python2.6` 开始，python 标准库中添加了对 `json` 的支持，操作 `json` 时，只需要 `import json` 即可。

### 🖋 1.1、python对象转换成`json`字符串 <a id="11python&#x5BF9;&#x8C61;&#x8F6C;&#x6362;&#x6210;json&#x5B57;&#x7B26;&#x4E32;"></a>

#### 1.1.1、python对象到 `json` 字符串的转换规则： <a id="111python&#x5BF9;&#x8C61;&#x5230;json&#x5B57;&#x7B26;&#x4E32;&#x7684;&#x8F6C;&#x6362;&#x89C4;&#x5219;"></a>

| Python | Json |
| :---: | :---: |
| dict | object |
| list, tuple | array |
| str, unicode | string |
| int, long, float | number |
| True | true |
| False | false |
| None | null |

#### 1.1.2、主要使用如下函数： <a id="112&#x4E3B;&#x8981;&#x4F7F;&#x7528;&#x5982;&#x4E0B;&#x51FD;&#x6570;"></a>

```python
json.dumps()
```

> dump的功能就是把Python对象encode为 `json` 对象，一个编码过程。该函数的具体参数（具体用法参见附录中链接）：

```python
json.dumps(obj, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, encoding=”utf-8”, default=None, sort_keys=False, **kw)
```

> json 模块提供了`json.dumps`和`json.dump`方法，区别是dump直接到文件，而dumps到一个字符串，这里的s可以理解为string。`json.dump` 不仅可以把Python对象编码为string，还可以写入文件。因为我们不能把Python对象直接写入文件，这样会报错`TypeError: expected a string or other character buffer object`，我们需要将其序列化之后才可以。

#### 1.1.3、`json.dumps` 常用参数 <a id="113jsondumps-&#x5E38;&#x7528;&#x53C2;&#x6570;"></a>

**1、sort\_keys：** 输出时字典的是按键值排序的，而不是随机的。

```python
data = [ { 'a':'A', 'b':(2, 4), 'c':3.0 } ]
print 'DATA:', repr(data)
unsorted = json.dumps(data)
print 'JSON:', json.dumps(data)
print 'SORT:', json.dumps(data, sort_keys=True)
```

```python
DATA: [{'a': 'A', 'c': 3.0, 'b': (2, 4)}]
JSON: [{"a": "A", "c": 3.0, "b": [2, 4]}]
SORT: [{"a": "A", "b": [2, 4], "c": 3.0}]
```

**2、indent：**就是更个缩进，让我们更好地看清结构。

```python
data = [ { 'a':'A', 'b':(2, 4), 'c':3.0 } ]
print 'DATA:', repr(data)
print 'NORMAL:', json.dumps(data, sort_keys=True)
print 'INDENT:', json.dumps(data, sort_keys=True, indent=2)
```

```python
DATA: [{'a': 'A', 'c': 3.0, 'b': (2, 4)}]
NORMAL: [{"a": "A", "b": [2, 4], "c": 3.0}]
INDENT: [
  {
    "a": "A", 
    "b": [
      2, 
      4
    ], 
    "c": 3.0
  }
]
```

**3、separators：**是提供分隔符，可以出去白空格，输出更紧凑，数据更小。默认的分隔符是\(', ', ': '\)，有白空格的。不同的dumps参数，对应文件大小一目了然。

```python
data = [ { 'a':'A', 'b':(2, 4), 'c':3.0 } ]
print 'DATA:', repr(data)
print 'repr(data)             :', len(repr(data))
print 'dumps(data)            :', len(json.dumps(data))
print 'dumps(data, indent=2)  :', len(json.dumps(data, indent=2))
print 'dumps(data, separators):', len(json.dumps(data, separators=(',',':')))
```

```python
DATA: [{'a': 'A', 'c': 3.0, 'b': (2, 4)}]
repr(data)             : 35
dumps(data)            : 35
dumps(data, indent=2)  : 76
dumps(data, separators): 29
```

#### 1.1.4、注意 <a id="114&#x6CE8;&#x610F;"></a>

**`json` 需要字典的的键是字符串，否则会抛出 `ValueError`。**

```python
data = [ { 'a':'A', 'b':(2, 4), 'c':3.0, ('d',):'D tuple' } ]
print 'First attempt'
try:
    print json.dumps(data)
except (TypeError, ValueError) as err:
    print 'ERROR:', err
print
print 'Second attempt'
print json.dumps(data, skipkeys=True)
```

```python
First attempt
ERROR: keys must be a string
Second attempt
[{"a": "A", "c": 3.0, "b": [2, 4]}]
```

### 🖋 1.2、`json`字符串转换成python对象 <a id="12json&#x5B57;&#x7B26;&#x4E32;&#x8F6C;&#x6362;&#x6210;python&#x5BF9;&#x8C61;"></a>

#### 1.2.1、`json`字符串到python对象的转换规则： <a id="121json&#x5B57;&#x7B26;&#x4E32;&#x5230;python&#x5BF9;&#x8C61;&#x7684;&#x8F6C;&#x6362;&#x89C4;&#x5219;"></a>

| Json | Python |
| :---: | :---: |
| object | dict |
| array | list |
| string | unicode |
| number \(int\) | int, long |
| number \(real\) | float |
| true | True |
| false | False |
| null | None |

#### 1.2.2、主要使用如下函数： <a id="122&#x4E3B;&#x8981;&#x4F7F;&#x7528;&#x5982;&#x4E0B;&#x51FD;&#x6570;"></a>

```python
json.loads()
```

> 该函数的具体参数（具体用法参见附录中链接）：

```python
json.loads(s[, encoding[, cls[, object_hook[, parse_float[, parse_int[, parse_constant[, object_pairs_hook[, **kw]]]]]]]])
```

> `json.loads`方法是基于string的，如果是文件，我们可以用`json.load`方法。

## ✏ 2、`h5py`文件文件操作 <a id="2h5py&#x6587;&#x4EF6;&#x6587;&#x4EF6;&#x64CD;&#x4F5C;"></a>

`h5py`文件是存放两类对象的容器，数据集\(`dataset`\)和组\(group\)，`dataset`类似数组类的数据集合，和`numpy`的数组差不多。group是像文件夹一样的容器，它好比python中的字典，有键\(key\)和值\(value\)。group中可以存放`dataset`或者其他的group。”键”就是组成员的名称，”值”就是组成员对象本身\(组或者数据集\)，下面来看下如何创建组和数据集。

### 🖋 2.1、创建文件和数据集 <a id="21&#x521B;&#x5EFA;&#x6587;&#x4EF6;&#x548C;&#x6570;&#x636E;&#x96C6;"></a>

使用 `h5py.File()`方法创建`hdf5`文件

```python
h5file = h5py.File(filename,'w')
```

然后在此基础上创建数据集

```python
X = h5file.create_dataset(shape=(0,args.patch_size,args.patch_size),　            #数据集的维度
                          maxshape = (None,args.patch_size,args.patch_size),                #数据集的允许最大维度　
                          dtype=float,compression='gzip',name='train',                      #数据类型、是否压缩，以及数据集的名字
                          chunks=(args.chunk_size,args.patch_size,args.patch_size))         #分块存储，每一分块的大小
```

最为关系的两个参数为shape和`maxshape`，很显然我们希望数据集的某一个维度是可以扩展的，所以在`maxshape`中，将希望扩展的维度标记为None，其他维度和shape参数里面的一样。还有一点值得注意的是，使用`compression='gzip'`以后，整个数据集能够被极大的压缩，对比较大的数据集非常又用，并且在数据读写的时候，不用用户显式的解码。

### 🖋 2.2、写数据集 <a id="22&#x5199;&#x6570;&#x636E;&#x96C6;"></a>

在使用上面的`creat_dataset`创建了`dataset`以后，读写数据集就如同读写`numpy`数组一样方便，比如上面的函数定义了数据集'train'，也就是变量X以后，可以下面的方法来读写：

```python
data = np.zeros((100,args.patch_size,arg))
X[0:100,:,:] = data
```

在前面创建数据集的时候，我们定义`shape = (args.chunk_size,args.patch_size,args.patch_size)`，如果有更多的数据，怎么办呢？可以使用resize方法来扩展在`maxshape`中定义为None的那个维度:

```python
X.resize(X.shape[0]+args.chunk_size,axis=0)
```

因为我们在`maxshape=(None,args.patch_size,args.patch_size)`中将第零个维度定义为可扩展，所以，首先我们用X.shape\[0\]来找到该维度的长度，并将其扩展。该维度扩展以后，就可以继续向里面写入数据了。

### 🖋 2.3、读数据集 <a id="23&#x8BFB;&#x6570;&#x636E;&#x96C6;"></a>

读取`h5`文件的方法也非常简单，首先利用`h5py.File`方法打开对应的`h5`文件，然后将里面的某个数据集取出至变量，对这个变量的读取就如同`numpy`一样了。

```python
h = h5py.File(hd5file,'r')
train = h['train']
train[1]
train[2]
...
```

但是上面的读取方法存在一个问题就是每一次使用的时候\(`train[1],train[2]`\)都需要从硬盘读取数据，这将会导致读取的速度比较慢。一个比较好的方法是，每次从硬盘读取一个chunk\_size的数据，然后将这些数据存储到内存中，在需要的时候从内存中读取，比如使用下面的方法：

```python
h = h5py.File(hd5file,'r')
train = h['train']
X = train[0:100]         #一次从硬盘中读取比较多的数据，X将存储在内存中
X[1]                     #从内存中读取
X[2]                     #从内存中读取
```

这样的方法就会快很多。

