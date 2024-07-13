#python/问题解决
# Python常见问题解决

## 解决python脚本之间的相互调用

引入`sys`

```
sys.path.append('被调用文件的上层文件目录')
```

将该位置加入环境变量