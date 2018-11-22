---
title: 在Windows环境下的Docker配置
categories:
  - Docker
tags:
  - Windows
date: 2018-11-22 10:50:53
---

### mysql5.7安装
```powershell
docker run --name mysql5.7 -v C:\Users\*\Documents\Docker\Mysql:/var/lib/mysql -v C:\Users\*\Documents\Docker\MysqlConf:/etc/mysql/conf.d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
```

### oracle 11g 安装

1. 搜索镜像

```powershell
docker search oracle
```

2. 下载镜像

````
docker pull alexeiled/docker-oracle-xe-11g
````

3. 使用镜像

用[https://dashboard.daocloud.io/packages/explore](https://link.jianshu.com?t=https%3A%2F%2Fdashboard.daocloud.io%2Fpackages%2Fexplore) 对镜像进行搜索，或者直接上官网也可以。

```powershell
docker run -d --shm-size=2g -p 1521:1521 -p 18080:8080 alexeiled/docker-oracle-xe-11g
```

> Connect 
> Connect database with following setting:  
> hostname: localhost 
> port: 1521
> sid: xe 
> username: system 
> password: oracle 
> Password for SYS user 
>
> oracle 
> Connect to Oracle Application Express web management console with following settings:  
> url: http://localhost:18080/apex 
> workspace: internal
> user: admin 
> password: oracle (首次登录需要更改密码：123456)

### 附录： 常用命令

docker image ls (-a)  查看镜像

docker container ls (-a)  查看容器

docker volume ls  查看文件卷

docker attach 容器名称/容器id

docker inspect 容器名/容器ID

docker exec -it 容器名 bash 进入已经运行的容器，并运行bash命令