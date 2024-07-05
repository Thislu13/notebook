#工具/Git使用 
# Git使用

* 删除远程仓库中的文件

  ```
  git rm -r --cached  file/dir
  git commit -n 'remove '
  git push 
  ```

> 也可以直接
> 
> `git rm -r`  会把本地文件也同步删掉
> 
> `-r` 逻辑应该和 `linux` 中一致