# Dataset & Dataloader

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

通过继承`torch.utils.data.Dataset`的这个抽象类，我们可以定义好我们需要的数据类。我们可以通过迭代的方式来取得每一个数据，但是这样很难实现取batch，shuffle或者多线程读取数据，所以pytorch还提供了一个简单的方法来做这件事情，通过`torch.utils.data.DataLoader`类来定义一个新的迭代器，用来将自定义的数据读取接口或者 PyTorch 已有的数据读取接口的输出按照 batch size 封装成Tensor，后续只需要再包装成 Variable 即可作为模型的输入。

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
                    # dataset.__len__() 在 Sampler 中被使用。
                    # 目的是生成一个 长度为 len(dataset) 的 序列索引（随机的）。
                    sampler = RandomSampler(dataset)
                else:
                    # dataset.__len__() 在 Sampler 中被使用。
                    # 目的是生成一个 长度为 len(dataset) 的 序列索引（顺序的）。
                    sampler = SequentialSampler(dataset)
            # Sampler 是个迭代器，一次之只返回一个 索引
            # BatchSampler 也是个迭代器，但是一次返回 batch_size 个 索引
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

在 `DataLoader` 中，`iter(dataloader)` 返回的是一个 `DataLoaderIter` 对象， 这个才是我们一直 `next`的 对象。

需要注意的是DataLoader的部分初始化参数之间存在互斥关系：

* 如果自定义了`batch_sampler`，那么这些参数都必须使用默认值：`batch_size`，`shuffle`，`sampler`，`drop_last`。
* 如果自定义了`sampler`，那么`shuffle`需要设置为`False`。
* 如果`sampler`和`batch_sampler`都为`None`，那么`batch_sampler`使用Pytorch已经实现好的`BatchSampler`，而`sampler`分两种情况：
  * 若`shuffle=True`，则`sampler=RandomSampler(dataset)`
  * 若`shuffle=False`，则`sampler=SequentialSampler(dataset)`

### ✏ 2.1、**DataLoaderIter**

**源码如下：**

```python
class _DataLoaderIter(object):

    def __init__(self, loader):
        # loader 是 DataLoader 对象
        self.dataset = loader.dataset
        self.collate_fn = loader.collate_fn
        self.batch_sampler = loader.batch_sampler
        self.num_workers = loader.num_workers
        self.pin_memory = loader.pin_memory and torch.cuda.is_available()
        self.timeout = loader.timeout
        # 这样就可以用 next 操作 batch_sampler 了
        self.sample_iter = iter(self.batch_sampler)

        base_seed = torch.LongTensor(1).random_().item()

        if self.num_workers > 0:
            self.worker_init_fn = loader.worker_init_fn
            self.worker_queue_idx = 0
            self.worker_result_queue = multiprocessing.Queue()
            
            # 当前已经准备好的 batch 的数量（可能有些正在 准备中）
            # 当为 0 时， 说明， dataset 中已经没有剩余数据了。
            # 初始值为 0, 在 self._put_indices() 中 +1,在 self.__next__ 中减一
            self.batches_outstanding = 0
            self.worker_pids_set = False
            self.shutdown = False
            
            # 用来记录 这次要放到 index_queue 中 batch 的 idx
            self.send_idx = 0
            
            # 用来记录 这次要从的 data_queue 中取出 的 batch 的 idx
            self.rcvd_idx = 0
            self.reorder_dict = {}
            self.done_event = multiprocessing.Event()

            self.index_queues = []
            self.workers = []
            for i in range(self.num_workers):
                
                index_queue = multiprocessing.Queue()
                index_queue.cancel_join_thread()
                w = multiprocessing.Process(
                    target=_utils.worker._worker_loop,
                    args=(self.dataset, index_queue,
                          self.worker_result_queue, self.done_event,
                          self.collate_fn, base_seed + i,
                          self.worker_init_fn, i))
                w.daemon = True

                w.start()
                self.index_queues.append(index_queue)
                self.workers.append(w)

            if self.pin_memory:
                self.data_queue = queue.Queue()
                pin_memory_thread = threading.Thread(
                    target=_utils.pin_memory._pin_memory_loop,
                    args=(self.worker_result_queue, self.data_queue,
                          torch.cuda.current_device(), self.done_event))
                pin_memory_thread.daemon = True
                pin_memory_thread.start()
                # Similar to workers (see comment above), we only register
                # pin_memory_thread once it is started.
                self.pin_memory_thread = pin_memory_thread
            else:
                self.data_queue = self.worker_result_queue

            _utils.signal_handling._set_worker_pids(id(self), tuple(w.pid for w in self.workers))
            _utils.signal_handling._set_SIGCHLD_handler()
            self.worker_pids_set = True

            # prime the prefetch loop
            for _ in range(2 * self.num_workers):
                self._put_indices()

    def __len__(self):
        return len(self.batch_sampler)

    def _try_get_batch(self, timeout=_utils.MP_STATUS_CHECK_INTERVAL):
        # Tries to fetch data from `data_queue` for a given timeout. This can
        # also be used as inner loop of fetching without timeout, with the
        # sender status as the loop condition.
        #
        # This raises a `RuntimeError` if any worker died expectedly. This error
        # can come from either the SIGCHLD handler in `_utils/signal_handling.py`
        # (only for non-Windows platforms), or the manual check below on errors
        # and timeouts.
        #
        # Returns a 2-tuple:
        #   (bool: whether successfully get data, any: data if successful else None)
        try:
            data = self.data_queue.get(timeout=timeout)
            return (True, data)
        except Exception as e:
            if not all(w.is_alive() for w in self.workers):
                pids_str = ', '.join(str(w.pid) for w in self.workers if not w.is_alive())
                raise RuntimeError('DataLoader worker (pid(s) {}) exited unexpectedly'.format(pids_str))
            if isinstance(e, queue.Empty):
                return (False, None)
            raise
    
    '''
    主要根据是否设置了超时时间来操作，如果超过指定的超时时间后没有从队列中读到数据就报错，
    如果不设置超时时间且一致没有从队列中读到数据，那么就会一直卡着且不报错，这部分是PyTorch后来修的一个bug。
    '''
    def _get_batch(self):
        # Fetches data from `self.data_queue`.
        #
        # We check workers' status every `MP_STATUS_CHECK_INTERVAL` seconds,
        # which we achieve by running `self._try_get_batch(timeout=MP_STATUS_CHECK_INTERVAL)`
        # in a loop. This is the only mechanism to detect worker failures for
        # Windows. For other platforms, a SIGCHLD handler is also used for
        # worker failure detection.
        #
        # If `pin_memory=True`, we also need check if `pin_memory_thread` had
        # died at timeouts.
        if self.timeout > 0:
            success, data = self._try_get_batch(self.timeout)
            if success:
                return data
            else:
                raise RuntimeError('DataLoader timed out after {} seconds'.format(self.timeout))
        elif self.pin_memory:
            while self.pin_memory_thread.is_alive():
                success, data = self._try_get_batch()
                if success:
                    return data
            else:
                # while condition is false, i.e., pin_memory_thread died.
                raise RuntimeError('Pin memory thread exited unexpectedly')
            # In this case, `self.data_queue` is a `queue.Queue`,. But we don't
            # need to call `.task_done()` because we don't use `.join()`.
        else:
            while True:
                success, data = self._try_get_batch()
                if success:
                    return data

    def __next__(self):
        if self.num_workers == 0:  # same-process loading
            indices = next(self.sample_iter)  # may raise StopIteration
            batch = self.collate_fn([self.dataset[i] for i in indices])
            if self.pin_memory:
                batch = _utils.pin_memory.pin_memory_batch(batch)
            return batch

        # check if the next sample has already been generated
        if self.rcvd_idx in self.reorder_dict:
            batch = self.reorder_dict.pop(self.rcvd_idx)
            return self._process_next_batch(batch)

        if self.batches_outstanding == 0:
            self._shutdown_workers()
            raise StopIteration

        while True:
            assert (not self.shutdown and self.batches_outstanding > 0)
            idx, batch = self._get_batch()
            self.batches_outstanding -= 1
            if idx != self.rcvd_idx:
                # store out-of-order samples
                self.reorder_dict[idx] = batch
                continue
            return self._process_next_batch(batch)

    next = __next__  # Python 2 compatibility

    def __iter__(self):
        return self
        
    '''
    从self.sample_iter中读取下一个batch数据中每个数据的index：indices = next(self.sample_iter, None)，
    注意这里的index和前面idx是不一样的，这里的index是一个batch中每个数据的index，idx是一个batch的index；
    然后将读取到的index通过调用queue对象的put方法压到队列self.index_queue中：self.index_queue.put((self.send_idx, indices))
    '''
    def _put_indices(self):
        assert self.batches_outstanding < 2 * self.num_workers
        indices = next(self.sample_iter, None)
        if indices is None:
            return
        self.index_queues[self.worker_queue_idx].put((self.send_idx, indices))
        self.worker_queue_idx = (self.worker_queue_idx + 1) % self.num_workers
        self.batches_outstanding += 1
        self.send_idx += 1

    '''
    首先对self.rcvd_idx进行加一，也就是更新下下一个要读取的batch数据的index。
    然后调用_put_indices()方法获取下一个batch的每个数据的index。
    '''
    def _process_next_batch(self, batch):
        self.rcvd_idx += 1
        self._put_indices()
        if isinstance(batch, _utils.ExceptionWrapper):
            # make multiline KeyError msg readable by working around
            # a python bug https://bugs.python.org/issue2651
            if batch.exc_type == KeyError and "\n" in batch.exc_msg:
                raise Exception("KeyError:" + batch.exc_msg)
            else:
                raise batch.exc_type(batch.exc_msg)
        return batch

    def __getstate__(self):
        # TODO: add limited pickling support for sharing an iterator
        # across multiple threads for HOGWILD.
        # Probably the best way to do this is by moving the sample pushing
        # to a separate thread and then just sharing the data queue
        # but signalling the end is tricky without a non-blocking API
        raise NotImplementedError("_DataLoaderIter cannot be pickled")

    def _shutdown_workers(self):
        # See NOTE [ Data Loader Multiprocessing Shutdown Logic ] for details on
        # the logic of this function.
        python_exit_status = _utils.python_exit_status
        if python_exit_status is True or python_exit_status is None:
            # See (2) of the note. If Python is shutting down, do no-op.
            return
        # Normal exit when last reference is gone / iterator is depleted.
        # See (1) and the second half of the note.
        if not self.shutdown:
            self.shutdown = True
            try:
                self.done_event.set()

                if hasattr(self, 'pin_memory_thread'):
                    # Use hasattr in case error happens before we set the attribute.
                    # First time do `worker_result_queue.put` in this process.

                    # `cancel_join_thread` in case that `pin_memory_thread` exited.
                    self.worker_result_queue.cancel_join_thread()
                    self.worker_result_queue.put(None)
                    self.pin_memory_thread.join()
                    
                    self.worker_result_queue.close()

                # Exit workers now.
                for q in self.index_queues:
                    q.put(None)
                    # Indicate that no more data will be put on this queue by the
                    # current process.
                    q.close()
                for w in self.workers:
                    w.join()
            finally:
                if self.worker_pids_set:
                    _utils.signal_handling._remove_worker_pids(id(self))
                    self.worker_pids_set = False

    def __del__(self):
        if self.num_workers > 0:
            self._shutdown_workers()
```

### ✏ 2.2、DataLoader、DataSet、Sampler之间的关系

 看一下`_DataLoaderIter.__next__()`的源代码：

```python
class DataLoader(object):
    ...
    
    def __next__(self):
        if self.num_workers == 0:  
            indices = next(self.sample_iter)  # Sampler
            batch = self.collate_fn([self.dataset[i] for i in indices]) # Dataset
            if self.pin_memory:
                batch = _utils.pin_memory.pin_memory_batch(batch)
            return batch
```

假设我们的数据是一组图像，每一张图像对应一个index，那么如果我们要读取数据就只需要对应的index即可，即上面代码中的`indices`，而选取index的方式有多种，有按顺序的，也有乱序的，所以这个工作需要`Sampler`完成。拿到了`indices`，下一步只需要根据index对数据进行读取即可。

参数的意义：

* `indices`: 表示每一个iteration，sampler返回的indices，即一个batch size大小的索引列表
* `self.dataset[i]`: 前面已经介绍了，这里就是对第i个数据进行读取操作，一般来说`self.dataset[i]=(img, label)`

前面还有一个`self.collate_fn`方法，其作用就是将一个batch的数据进行合并操作。默认的`collate_fn`是将img和label分别合并成imgs和labels，所以如果你的`__getitem__`方法只是返回 `img, label`，那么你可以使用默认的`collate_fn`方法，但是如果你每次读取的数据有`img, box, label`等等，那么你就需要自定义`collate_fn`来将对应的数据合并成一个batch数据，这样方便后续的训练步骤。

```python
def default_collate(batch):
    "Puts each data field into a tensor with outer dimension batch size"
    if torch.is_tensor(batch[0]):
        out = None
        if _use_shared_memory:
            # If we're in a background process, concatenate directly into a
            # shared memory tensor to avoid an extra copy
            # 计算 batch 中所有 元素的个数 
            numel = sum([x.numel() for x in batch])
            storage = batch[0].storage()._new_shared(numel)
            out = batch[0].new(storage)
        return torch.stack(batch, 0, out=out)
    elif type(batch[0]).__module__ == 'numpy':
        elem = batch[0]
        if type(elem).__name__ == 'ndarray':
            return torch.stack([torch.from_numpy(b) for b in batch], 0)
        if elem.shape == ():  # scalars
            py_type = float if elem.dtype.name.startswith('float') else int
            return numpy_type_map[elem.dtype.name](list(map(py_type, batch)))
    elif isinstance(batch[0], int):
        return torch.LongTensor(batch)
    elif isinstance(batch[0], float):
        return torch.DoubleTensor(batch)
    elif isinstance(batch[0], string_classes):
        return batch
    elif isinstance(batch[0], collections.Mapping):
        return {key: default_collate([d[key] for d in batch]) for key in batch[0]}
    elif isinstance(batch[0], collections.Sequence):
        transposed = zip(*batch)
        return [default_collate(samples) for samples in transposed]

    raise TypeError(("batch must contain tensors, numbers, dicts or lists; found {}"
                     .format(type(batch[0]))))
```

{% hint style="info" %}
如果在 `__getitem__()` 或 `collate_fn()` 中使用了 `numpy` 产生随机数， 则需要注意： [why-does-numpy-random-rand-produce-the-same-values-in-different-cores](https://discuss.pytorch.org/t/why-does-numpy-random-rand-produce-the-same-values-in-different-cores/12005) ，如果使用的是 `torch` 产生随机数， 就不用担心这一点。
{% endhint %}

