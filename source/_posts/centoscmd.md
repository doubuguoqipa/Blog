---
title: CENTOS下常用命令
categories:
  - 服务器配置
tags:
  - centos
date: 2018-11-26 11:05:14
---
#### 查看防火墙状态

```
service iptables status
```

#### 查看已开放端口

```
firewall-cmd --list-ports
```

#### 列出所有端口

```
netstat -ntlp
```

#### 查看80端口使用情况

```
lsof -i tcp:80
```

#### 查看关联服务使用

```
netstat -lnp|grep -i -E 'sql|nginx|tomcat|oracle|weblogic|mysql'
```

#### 修改root密码

```
passwd 
```

