# 1.遍历使用`enumerate`内置枚举函数

```python
for idx, num in enumerate(data):
```
# 2.列表推导式

```python
out = [x 条件 for x in date if 条件]
```

# 3.排序匿名函数

```python
sorted(data, key=lambda x: x[""])
```

# 4.利用生成器求列表的和提高效率

```python
data = (i for i in range(1000))
sun(data)
```

优于

```python
data = [i for i in range(1000)]
sun(data)
```

# 5.获取字典的值

```python
get("key", "1234")
```

如果没有 key 返回 后面的值 避免程序奔溃

```python
setdefault('key', '123')
```

如果key不存在 将后面的值 设置为他的值

 # 6.join拼接字符串

```python
" ".join(data)
```

将遍历拼接data，并将 " "中的内容作为间隔

# 7.合并字典

```python
out = {**data_1, **data_2}
```

自动去重

# 8.
