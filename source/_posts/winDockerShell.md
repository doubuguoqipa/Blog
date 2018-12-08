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

4. PL/SQL Developer

连接Oracle，需下载oracle instance client解压，并配置instance client:
   在instance client目录中新建名为network的文件夹，并在network目录下新建admin文件夹，在admin文件夹下新建tnsnames.ora文件，使用记事本编写：
   xe = 
   (DESCRIPTION = 
       (ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 49161)) 
       (CONNECT_DATA = 
         (SERVICE_NAME = XE) 
       ) 
   ) 
其中第一行中的XE是oracle数据库的实例名。
SERVICE_NAME后填写oracle的服务名，可在容器中用lsnrctl status查看，这里我们填写XE。
打开PL/SQL Developer客户端（此时仍无法登陆，关闭登录窗口以未登录状态打开即可），选择Tools-》Preferences,将Oracle Home和OCI Liabrary两栏中填入instance client所在的路径和OCI文件所在路径（oci.dll在instance client目录下），重启PL/SQL Developer。

5. oracle数据库导入sql时需修改sql脚本中的user用户名，或者先创建一个用户名，分配connect、resource、dba权限，然后运行sql脚本导入。

### 附录： 常用命令

docker image ls (-a)  查看镜像

docker container ls (-a)  查看容器

docker volume ls  查看文件卷

docker attach 容器名称/容器id

docker inspect 容器名/容器ID

docker exec -it 容器名 bash 进入已经运行的容器，并运行bash命令