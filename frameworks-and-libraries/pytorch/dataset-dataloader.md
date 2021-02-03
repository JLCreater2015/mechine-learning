# 数据集加载机制

## 🖌 1、Dataset

```python
# torch.utils.data.Dataset
class Dataset(object):
    """An abstract class representing a Dataset.

    All other datasets should subclass it. All subclasses should override
    ``__len__``, that provides the size of the dataset, and ``__getitem__``,
    supporting integer indexing in range from 0 to len(self) exclusive.
    """

    def __getitem__(self, index):
        raise NotImplementedError

    def __len__(self):
        raise NotImplementedError

    def __add__(self, other):
        return ConcatDataset([self, other])
```

**`torch.utils.data.Dataset`**是一个表示数据集的抽象类。任何自定义的数据集都需要继承这个类并覆写相关方法。

所谓数据集，其实就是一个负责处理索引（index）到样本（sample）映射的一个类（class）。Pytorch提供两种数据集： Map 式数据集 和 Iterable 式数据集。

### ✏ 1.1、map式数据集

一个Map式的数据集必须要重写`__getitem__(self, index)`和`__len__(self)` 两个内建方法，用来表示从索引到样本的映射（Map）。这样一个数据集dataset，当使用`dataset[idx]`命令时，可以在你的硬盘中读取你的数据集中第idx 张图片以及其标签（如果有的话），`len(dataset)`则会返回这个数据集的容量。

自定义类大致是这样的：

```python
class CustomDataset(data.Dataset): # 需要继承data.Dataset
    def __init__(self):
        # TODO
        # 1. Initialize file path or list of file names.
        pass
    def __getitem__(self, index):
        # TODO
        # 1. Read one data from file (e.g. using numpy.fromfile, PIL.Image.open).
        # 2. Preprocess the data (e.g. torchvision.Transform).
        # 3. Return a data pair (e.g. image and label).
        pass
    def __len__(self):
        # You should change 0 to the total size of your dataset.
        return 0
```

### ✏ 1.2、iterable式数据集

一个 Iterable 式数据集是抽象类 **`torch.utils.data.IterableDataset`** 的子类，并且覆写了**`__iter__`**方法成为一个迭代器。这种数据集主要用于数据大小未知，或者以流的形式的输入，本地文件不固定的情况，需要以迭代的方式来获取样本索引。

## 🖌 2、DataLoader

通过继承`torch.utils.data.Dataset`的这个抽象类，我们可以定义好我们需要的数据类。我们可以通过迭代的方式来取得每一个数据，但是这样很难实现取batch，shuffle或者多线程读取数据，所以pytorch还提供了一个简单的方法来做这件事情，通过`torch.utils.data.DataLoader`类来定义一个新的迭代器，用来将自定义的数据读取接口或者 PyTorch 已有的数据读取接口的输入按照 batch size 封装成Tensor，后续只需要再包装成 Variable 即可作为模型的输入。

 `Dataloader`类源码如下：

```python
# torch.utils.data.DataLoader

class DataLoader(object):

    __initialized = False

    def __init__(self, dataset, batch_size=1, shuffle=False, sampler=None, batch_sampler=None,
                 num_workers=0, collate_fn=default_collate, pin_memory=False, drop_last=False,
                 timeout=0, worker_init_fn=None):
        self.dataset = dataset
        self.batch_size = batch_size
        self.num_workers = num_workers
        self.collate_fn = collate_fn
        self.pin_memory = pin_memory
        self.drop_last = drop_last
        self.timeout = timeout
        self.worker_init_fn = worker_init_fn

        if timeout < 0:
            raise ValueError('timeout option should be non-negative')

        if batch_sampler is not None:
            if batch_size > 1 or shuffle or sampler is not None or drop_last:
                raise ValueError('batch_sampler option is mutually exclusive '
                                 'with batch_size, shuffle, sampler, and '
                                 'drop_last')
            self.batch_size = None
            self.drop_last = None

        if sampler is not None and shuffle:
            raise ValueError('sampler option is mutually exclusive with '
                             'shuffle')

        if self.num_workers < 0:
            raise ValueError('num_workers option cannot be negative; '
                             'use num_workers=0 to disable multiprocessing.')

        if batch_sampler is None:
            if sampler is None:
                if shuffle:
                    sampler = RandomSampler(dataset)
                else:
                    sampler = SequentialSampler(dataset)
            batch_sampler = BatchSampler(sampler, batch_size, drop_last)

        self.sampler = sampler
        self.batch_sampler = batch_sampler
        self.__initialized = True

    def __setattr__(self, attr, val):
        if self.__initialized and attr in ('batch_size', 'sampler', 'drop_last'):
            raise ValueError('{} attribute should not be set after {} is '
                             'initialized'.format(attr, self.__class__.__name__))

        super(DataLoader, self).__setattr__(attr, val)

    def __iter__(self):
        return _DataLoaderIter(self)

    def __len__(self):
        return len(self.batch_sampler)

'''
dataset(Dataset): 传入的数据集
batch_size(int, optional): 每个batch有多少个样本
shuffle(bool, optional): 在每个epoch开始的时候，对数据进行重新排序，若这个参数有定义，则shuffle必须为False
sampler(Sampler, optional): 自定义从数据集中取样本的策略，如果指定这个参数，那么shuffle必须为False
batch_sampler(Sampler, optional): 与sampler类似，但是一次只返回一个batch的indices（索引），需要注意的是，
       一旦指定了这个参数，那么batch_size,shuffle,sampler,drop_last就不能再制定了（互斥——Mutually exclusive）
num_workers (int, optional): 这个参数决定了有几个进程来处理data loading。0意味着所有的数据都会被load进主进程。（默认为0）
collate_fn (callable, optional): 将一个list的sample组成一个mini-batch的函数
pin_memory (bool, optional)： 如果设置为True，那么data loader将会在返回它们之前，将tensors拷贝到CUDA中的固定内存（CUDA pinned memory）中
drop_last (bool, optional): 如果设置为True：这个是对最后的未完成的batch来说的，比如你的batch_size设置为64，
       而一个epoch只有100个样本，那么训练的时候后面的36个就被扔掉了，如果为False（默认），那么会继续正常执行，只是最后的batch_size会小一点。
timeout(numeric, optional): 如果是正数，表明等待从worker进程中收集一个batch等待的时间，若超出设定的时间还没有收集到，
       那就不收集这个内容了。这个numeric应总是大于等于0。默认为0
worker_init_fn (callable, optional): 每个worker初始化函数，默认为None，如果不是None，这个函数将被每个子进程以子进程id（[0, num_workers - 1]之间的数）调用
'''
```

### ✏ 2.1、collate\_fn

### ✏ 2.2、**DataLoaderIter**

## 🖌 3、Sampler

