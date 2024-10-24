#工具/Git使用 
# 常用基础命令
* 删除远程仓库中的文件
  ```
  git rm -r --cached  file/dir
  git commit -n 'remove '
  git push 
  ```

> 也可以直接
> `git rm -r`  会把本地文件也同步删掉
> `-r` 逻辑应该和 `linux` 中一致

* 转移到指定版本
  `git checkout [版本号]`
# git分支管理

## 创建分支

```
git branch (分支)
```

>  如果没加括号内内容 显示的是已有环境
>
> 否则创造新的分支

创造出新的分支根据开发逻辑，是从主分支上`clone`得到的

## 删除远程分支

```
git checkout 其他分支
git push origin --delete 分支名
