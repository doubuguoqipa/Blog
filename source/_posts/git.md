---
title: 服务器配置Git仓库
categories:
  - 服务器配置
tags:
  - git
date: 2018-10-30 14:50:43
permalink: 20181030-1450
---
## 安装 Git

### 第一步： 安装

Ubunt /Debian

```
$ sudo apt-get install git
```

CentOS

```
$ sudo yum install git
```

### 第二步：创建新用户提供 Git 服务

出于安全考虑，创建一个名为 git 的新用户，它将只被允许执行 Git 服务相关命令。

```
$ sudo adduser git
$ sudo passwd git
```

接下来，要对 git 用户的 home 目录进行操作，需要切换到 git 用户或者 root 用户才能继续后续操作。

$ su git

### 第三步：上传用户公钥

在连接 Git 服务器时，需要无密码的 ssh 连接，上传用户的公钥 id_rsa.pub（位于~/.ssh/id_rsa.pub）。如果没有，先生成一个新的公钥。把公钥复制到服务器，这里使用 scp 命令.

```
$ scp ~/.ssh/id_rsa.pub user@serverIP:/home/git/.ssh
```

把公钥导入到/home/git/.ssh/authroied_keys文件中，有多个用户，一行插入一个公钥，

```
$ cat /home/git/.ssh/id_rsa.pub >> /home/git/.ssh/authorized_keys
```

### 第四步：修改权限

设置 git 用户对 authroied_keys 文件为拥有者和读写权限。

```
$ chmod 600 authorized_keys    
$ chmod 700 ~/.ssh
$ ll /home/git/.ssh/authorized_keys
-rw-------. 1 git git 418 Sep 21 06:08 /home/git/.ssh/authorized_keys
```

### 第五步：初始化 git 仓库

选一个目录作为 Git 仓库，将目录所有者改为 git 用户。

```
$ sudo git init --bare sample.git
$ sudo chown -R git:git sample.git
```

### 第六步：使用 git-shell 登录

对于服务器的 git 用户，应该只允许 git 相关操作，所以要把 shell 登录改为 git-shell 登录。编辑/etc/passwd文件：

```
git:x:1001:1001:,,,:/home/git:/bin/bash
```

改为：

```
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
​```
还要复制一个名为git-shell-commands的目录：
​```
$ cp /usr/share/doc/git-1.7.4.4/contrib/git-shell-commands /home/git -R
$ chown git:developers /home/git/git-shell-commands/ -R
$ chmod +x /home/git/git-shell-commands/help
$ chmod +x /home/git/git-shell-commands/list
​```
### 第七步：使用远程仓库
​```
$ git clone git@server:/path/to/sample.git
​```
```

https://leihungjyu.com/post/hexo-blog-git-to-vps.html