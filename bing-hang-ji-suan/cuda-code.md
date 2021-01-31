# CUDA编程

CUDA安装

安装到指定目录

如果根目录空间不够，或者没有sudo权限，则可以安装到其他目录，这时需要指定临时文件的目录，如：

```bash
​sh ./cuda_9.0.176_384.81_linux.run --tmpdir=~/cuda9
```

如果系统gcc版本太高，可以加`--override`参数：

```bash
​sh ./cuda_9.0.176_384.81_linux.run --tmpdir=~/cuda9 --override
```



