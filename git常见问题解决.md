

#工具/Git使用 

* 有代理，但是提交时候报错 超时，或者无法访问

  解决方法如下，引起的原因是，github没走代理

  ```
  git config --global http.proxy http://127.0.0.1:7890
  git config --global https.proxy https://127.0.0.1:7890
  
  
  $ git config --global -l
  user.email=1835886867@qq.com
  user.name=lu13
  credential.helper=store
  http.proxy=http://127.0.0.1:7890
  https.proxy=https://127.0.0.1:7890
  ```

* picgo图床设置

  ![image-20241024190248170](https://raw.githubusercontent.com/Thislu13/image_save/main/notebook/202410241902220.png)