#python/问题解决
# Python常见问题解决

## 解决python脚本之间的相互调用

引入`sys`

```
sys.path.append('被调用文件的上层文件目录')
```

将该位置加入环境变量

## 将环境加入jupyter

先进入需要操作的环境

```python
python -m ipykernel install --user --name=内核中的名字
```

出现 类似下文成功

```
Installed kernelspec pytorch_evn in /home/user/.local/share/jupyter/kernels/pytorch_evn
```

# setup的使用

1）创建环境

2）进入新环境，进入该目录下，安装

```
pip install .
```

