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

## 	创建分支

```
git branch (分支)
```

>  如果没加括号内内容 显示的是已有环境
>
> 否则创造新的分支

创造出新的分支根据开发逻辑，是从主分支上`clone`得到的

## 	删除远程分支

```
git checkout 其他分支
git push origin --delete 分支名

```

#工具/Git使用

# Git 建立 ssh 连接

## 	一、单git用户

- 设置用户信息

```
git config --global user.email ''
git config --global user.name ''
```

> 这里的账户名和邮箱与实际 git 托管平台无关，仅仅用来标记提交对象

- 读全局设置

```
git config --global --list
```

- 进入个人 ssh目录

```
cd ~/.ssh
```

- 生成**SSH密钥**

```
ssh-keygen -t rsa -C 'email'
```

>   `ssh-keygen` 生成密钥 
>
>   `-t`加密方式    `rsa` 指定的加密方式
>
>   `-C` 为密钥指定备注 通常为邮箱

* 密钥的 命名 与 密钥密码

  ```
  Enter file in which to save the key (/Users/username/.ssh/id_rsa):
  ```

  > 询问密钥的命名以及生成的路径 默认是 id_ rsa(加密方式)

  ```
  Enter passphrase (empty for no passphrase):
  ```

 > 生成密码 嫌麻烦跳过

> 无特殊需求交互页面无脑回车 

* 获得密钥

  ```
  cat ~/.ssh/id_rsa.pub
  ```

 > 生成字符末尾为实际密钥

* 测试
```
ssh -T git@github.com
```
## 	二、多git用户

### PlanA

*将生成的 **ssh key** 加入多个平台*

### PlanB  

_生成多个**ssh key**_

* 生成 **ssh key**

```
ssh-keygen -t rsa -C 'email_1'
```

> ```
> Enter file in which to save the key (/c/Users/wangqiushuo/.ssh/id_rsa): id_rsa_github
> ```
>
> 这里可以加以区分 个人github

```
ssh-keygen -t rsa -C 'email_2'
```

> ```
> Enter file in which to save the key (/c/Users/wangqiushuo/.ssh/id_rsa): id_rsa_company
> ```
>
> 这里可以加以区分  公司git平台

* 将以上ssh加入 ssh-agent

```
ssh-add id_rsa_github
ssh-add id_rsa_genomics
```

> 正常的返回为 `Identity added: id_rsa_*(email)`
>
> 如果出现 `Could not open a connection to your authentication agent.`
>
> 无法打开到您的身份验证代理的链接
>
> 输入 `ssh-agent bash`

> 当使用默认密钥名 id_rsa 时不用添加，`id_rsa`被默认添加到 ssh agent

设置config

`~/.ssh` 目录下存在一个 `config` 文件 

> 不存在 `touch` 一个就好了

进入编辑`config`文件

```
Host github
    User git
    Hostname github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_github

Host company-git
    User git
    # 替换为你公司的 Git 代码托管平台的服务器
    Hostname company-git-repo.com
    # 你公司 SSH 服务的端口号
    Port 22
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_company
```

>Host 链接的主机 随意就好 相当于别名
>
>User 使用的用户名 一般为git
>
>Hostname Git平台 实际IP地址
>
>Port SSH 端口号默认22
>
>PreferredAuthentications：指定优先使用的身份验证方法，指定为publickey，即使用公钥进行身份认证
>
>IdentityFile：指定要使用的私钥文件路径 不同 Git 代码托管平台指定不同私钥

* 设置上传时的用户名及邮箱

可以将全局部分设置为自己的邮箱与用户名（参考单用户部分 ）

将从公司git拉下来仓库local设置为实际要求

```
git config --local user.name zhangsan[改为你的真实姓名]
git config --local user.email zhangsan@company.com[改为你公司的邮箱]
```

