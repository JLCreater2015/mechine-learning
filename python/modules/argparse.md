# argparse

argsparse 是 python 的命令行解析的标准模块，内置于python，不需要安装。这个库可以让我们直接在命令行中就可以向程序中传入参数并让程序运行。

主要有三个步骤： 

1. 创建 ArgumentParser\(\) 对象；
2. 调用 add\_argument\(\) 方法添加参数； 
3. 使用 parse\_args\(\) 解析添加的参数。

## 🖌 1、创建解析器

```python
parser = argparse.ArgumentParser(description='Process some integers.')
```

ArgumentParser 对象包含将命令行解析成 Python 数据类型所需的全部信息。

```python
class argparse.ArgumentParser(prog=None, usage=None, description=None, 
    epilog=None, parents=[], formatter_class=argparse.HelpFormatter, 
    prefix_chars='-', fromfile_prefix_chars=None, argument_default=None, 
    conflict_handler='error', add_help=True, allow_abbrev=True)
```

> * prog - 程序的名称（默认：`sys.argv[0]`） 
> * usage - 描述程序用途的字符串（默认值：从添加到解析器的参数生成） 
> * description - 在参数帮助文档之前显示的文本（默认值：无） 
> * epilog - 在参数帮助文档之后显示的文本（默认值：无） 
> * parents - 一个 ArgumentParser 对象的列表，它们的参数也应包含在内 
> * formatter\_class - 用于自定义帮助文档输出格式的类 
> * prefix\_chars - 可选参数的前缀字符集合（默认值：’-’） 
> * fromfile\_prefix\_chars - 当需要从文件中读取其他参数时，用于标识文件名的前缀字符集合（默认值：None） 
> * argument\_default - 参数的全局默认值（默认值： None） 
> * conflict\_handler - 解决冲突选项的策略（通常是不必要的） 
> * add\_help - 为解析器添加一个 `-h/--help` 选项（默认值： True） 
> * allow\_abbrev - 如果缩写是无歧义的，则允许缩写长选项 （默认值：True）

## 🖌 2、添加参数

```python
parser.add_argument('integers', metavar='N', type=int, nargs='+', 
    help='an integer for the accumulator')
    
ArgumentParser.add_argument(name or flags...[, action][, nargs][, const]
    [, default][, type][, choices][, required][, help][, metavar][, dest])
```

> * name or flags - 一个命名或者一个选项字符串的列表，例如 foo 或 `-f, --foo`。 
> * action - 当参数在命令行中出现时使用的动作基本类型。其选项包含：
>   * store - 存储参数的值，为默认选项
>   * store\_const - 存储被 const 命名参数指定的值， 通常用在选项中来指定一些标志 `parser.add_argument('--foo', action='store_const', const=42)`
>   * store\_true / store\_false - store\_const 分别用作存储 True 和 False 值的特殊用例，其默认值为 False / True `parser.add_argument('--foo', action='store_true')`
>   * append - 存储一个列表，并且将每个参数值追加到列表中。在允许多次使用选项时很有用
>   * append\_const - 将 const 命名参数指定的值追加到列表中
>   * count - 计算一个关键字参数出现的数目或次数
>   * help - 打印所有当前解析器中的选项和参数的完整帮助信息
>   * version - 打印版本信息`parser.add_argument('--version', action='version', version='%(prog)s 2.0')`
>   * extend - 存储一个列表，将每个参数值扩展至该列表
> * nargs - 命令行参数应当消耗的数目。 
>   * N - 具体数值
>   * ‘ ? ’ - 若无命令行参数，使用 default；若选项字符串出现但没有跟随命令行参数产生 const 值
>   * ‘ \* ’ - 表示 0 或多个参数
>   * ‘ + ’ - 表示 1 或多个参数
> * const - 被一些 action 和 nargs 选择所需求的常数。 
> * default - 当参数未在命令行中出现时使用的值。 
> * type - 命令行参数应当被转换成的类型。该关键词可以传入`list, str, tuple, set, dict`等。 
> * choices - 可用的参数的容器。 
> * required - 此命令行选项是否可省略 （**仅选项可用**）。 
> * help - 一个此选项作用的简单描述。 
> * metavar - 在使用方法消息中使用的参数值示例。 
> * dest - 被添加到 parse\_args\(\) 所返回对象上的属性名。

### ✏ 2.1、`type`

假设我们写一个文件，在命令行将其编译时，同时输入一个数字，返回一个平方，因为`parse.add_argument()`对于接受的值默认其为`str`，如果要将之视为`int`类型，额外加一句`“type=int”`。

```python
import argparse
parser = argparse.ArgumentParser()
 
parser.add_argument("square", help = "To sqaure the number given", type = int)
# 在命令行中，该py文件希望用户能给他一个参数，最终将之转化为：args.square
 
args = parser.parse_arg()
print(args.square**2)　
```

有了type，程序也可以自动过滤掉相关的不符合类型的参数输入。

### ✏ 2.2、nargs

nargs: 当选项后接受多个或者0个参数时需要这个来指定

1、`-u`选项接受2个参数：

```python
>>> parser.add_argument('-u',nargs=2)  
>>> parser.parse_args('-u a b'.split())  
Namespace(u=['a', 'b'])  
```

2、当选项接受1个或者不需要参数时指定n`args=’?'`，当没有参数时，会从default中取值。对于选项参数有一个额外的情况，就是出现选项而后面没有跟具体参数，那么会从const中取值：

```python
>>> parser.add_argument('-u',nargs='?')  
>>> parser.parse_args(''.split())  
Namespace(u=None)  
>>> parser.parse_args('-u a'.split())  
Namespace(u='a')  
  
>>> parser.add_argument('-u',nargs='?',default='d')  
>>> parser.add_argument('A',nargs='?',default='e')  
>>> parser.parse_args(''.split())  
Namespace(A='e', u='d')  
>>> parser.parse_args('-u'.split())  
Namespace(A='e', u=None)  
  
>>> parser.add_argument('-u',nargs='?',default='d',const='s')  
>>> parser.add_argument('A',nargs='?',default='T',const='P')  
>>> parser.parse_args(''.split())  
Namespace(A='T', u='d')  
>>> parser.parse_args('-u'.split())  
Namespace(A='T', u='s')  
>>> parser.parse_args('A'.split())  
Namespace(A='A', u='d')  
```

3、后面需要跟多个参数的情况，则需要设置`nargs=’*’`：

```python
>>> parser.add_argument('-u',nargs='*')  
>>> parser.parse_args('-u a b c d e'.split())  
Namespace(u=['a', 'b', 'c', 'd', 'e'])  
```

`nargs=’+'`也和`nargs=’*'`一样，但是有一个区别，`’+'`少于1个参数（没有参数）时会报错误，而`‘*’`会使用默认值：

```python
>>> parser.add_argument('u',nargs='+')  
>>> parser.parse_args(''.split())  
usage: [-h] u [u ...]  
: error: too few arguments  

>>> parser.add_argument('u',nargs='*',default='e')  
>>> parser.parse_args(''.split())  
Namespace(u='e')  
```

### ✏ 2.2、可选参数

可选参数：为了避免位置参数的顺序混乱问题，可以使用可选参数，需要在关键词前面加`--`。

```python
import argparse
parser = argparse.ArgumentParser()
 
parser.add_argument("--bool", help = "Whether to pirnt sth.")
# 这里的bool是一个可选参数，返回给args的是 args.bool
args = parser.parse_args()
 
if args.bool:
    print('bool = 1')
```

既然是可选的，如果不指定（就是不使用它）的话，对应的变量会被设置为None。对于简单程序，我们可能只需要两种值 `True`or`False`：**我们可以在原有的程序的基础上，在`add_argument`中多加一个参数：  
`action = "store_true"`，这个时候，只要在命令行中加个`bool`，`args.bool`就是`True`了，无需再加上之后的赋值内容，加了反而会报错**。但是，如果加入了**`required=True`**，如：`parser.add_argument(..., required=True, type=int)`，那么这个参数就是必须的。

```python
parser.add_argument("--bool", action = "store_true", help = "Whether to pirnt sth.")
# python .py --bool
# python .py

parser.add_argument("--bool", required = True, help = "Whether to pirnt sth.")
# python .py --bool 0/1

parser.add_argument("--bool", action = "store_true", required = True, help = "Whether to pirnt sth.")
# python .py --bool
```

### ✏ 2.3、参数简写

```python
parser.add_argument("-b", "--bool", action="store_true")
```

 这样，在程序内部我们依旧使用`args.bool`，但是在命令行当中我们只需要多写一个`“-b”`就好了。

在参数简写的时候不要定义“-h”，这个是给help用的。

### ✏ 2.4、混合定位参数和可选参数

```python
import argparse
parser = argparse.ArgumentParser()
 
parser.add_argument("-b", "--bool", help="to show the ans in a sentencen form", action = "store_true")
parser.add_argument("square", type=int)
 
args = parser.parse_args()
 
ans = args.square**2
 
if args.bool:
    print("the square of {} = {}".format(args.square,ans))
else:
    print(ans)
# 得到的效果为：一旦输入“-b”然后再输入相关的参数，就可以得到我们需要的结果了，
# 不输入“-b”我们还是可以得到结果，只不过是另外一种方式而已。

# python .py --bool 2
the square of 2 = 4
# python .py 2
4
```

* `parser.add_argument("-b", "--bool", choices=[0,1], help="you can only input 0 or 1.")` 其中，`choice`可以限定我们能够输入的数。
* 参数值和顺序： 一般我们要把可选参数最后add，不可选参数放前面add。在命令行里面输入代码时，程序会先赋值“先add的参数选项”。
* count操作计数赋值：如`parser.add_argument("-b", "--bool", action="count", type=int)` 这个时候，在命令后加上 `-b —> bool=1 ; -bb —> bool=2 ;`以此类推。但是这样的话，如果在程序里拿bool作为比较对象的时候，比如`if bool > 0:`，不写`“-b”`的时候会报错，原因是此时的bool是`None`，无法比较，要解决这个问题我们只需要加上一个 **default** 选项：`parser.add_argument("-b", "--bool", action="count", type=int, default=0)就好了。`

### ✏ 2.5、高级用法

参数冲突：我们可以通过`group`方法，来让group组的参数不可以被同时指定：

```python
import argparse
 
parser = argparse.ArgumentParser()
 
group = parser.add_mutually_exclusive_group()
group.add_argument("-f", "--form", action="store_true")
group.add_argument("-F", "--Form", action="store_true")
parser.add_argument("x", type = float)
parser.add_argument("y", type = float)
 
args = parser.parse_args()
ans = args.x**args.y
 
if args.form:
    print("{} to the power {} equals {}".format(args.x, args.y, ans))
elif args.Form:
    print("{}^{} = {}".format(args.x, args.y, ans))
else:
    print(ans)
    
# usage: .py [-h] [-f | -F] x y
```

{% hint style="info" %}
帮助信息中若有“\[a \| b\]”就意味着，ab不可以同时使用。
{% endhint %}

## 🖌 3、解析参数

### ✏ 3.1、`parse_args()`

`parse_args()`函数返回的是一个命名空间（`NameSpace`），这个`NameSpace`中有一些变量，就是我们`add_argument()`的那些参数。该函数可以从命令行获取参数的值，也可以通过传参获得，传参的方式时列表。

```python
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-x')
>>> parser.add_argument('--foo')
>>> parser.parse_args(['-x', 'X'])
Namespace(foo=None, x='X')
>>> parser.parse_args(['--foo', 'FOO'])
Namespace(foo='FOO', x=None)
>>> parser.parse_args(['--foo', 'FOO',-x', 'X'])
Namespace(foo='FOO', x=None)
```

### ✏ 3.2、`parse_known_args()`

`parse_known_args()`返回的是一个有两个元素的元组，第一个元素是`NameSpace`，和`parge_args()`返回的`NameSpace`完全相同，第二个是一个空列表，命令行中输入的多余的东西，以空格为分节符，以字符串的形式存放在列表中。

