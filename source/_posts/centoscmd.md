---
title: CENTOS下常用命令
categories:
  - 服务器配置
tags:
  - centos
  - Windows
date: 2018-11-26 11:05:14
---
### Centos



#### 查看防火墙状态 iptables

需自行安装

```
service iptables status
```

#### 查看防火墙状态 firewall-cmd

```
systemctl status firewalld

firewall-cmd --state

firewall-cmd --list-ports

```

#### 列出所有端口

```
netstat -ntlp
```

#### 修改ssh默认端口

```
wget https://blog.echainnet.com/2018/11/26/centoscmd/sshport.sh

bash sshport.sh
```

#### 查看80端口使用情况

```
lsof -i tcp:80
```

#### 查看关联服务使用

```
netstat -lnp|grep -i -E 'sql|nginx|tomcat|oracle|weblogic|mysql'

ps aux |  egrep  "sql|nginx|tomcat|oracle|weblogic|mysql" 
```

#### 修改root密码

```
passwd 
```

### Windows

#### 修改windows远程桌面端口号

```
https://blog.echainnet.com/2018/11/26/centoscmd/winport.bat
```

