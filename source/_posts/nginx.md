---
title: Nginx配置SLL通配符证书
categories:
  - 服务器配置
  - Nginx
tags:
  - Nginx
  - Letsencrypt
date: 2018-10-30 10:35:24
---

## 安装nginx
1. 添加源
```
rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```
2. 安装
```
yum install -y nginx

whereis nginx

以下是Nginx的默认路径：
(1) Nginx配置路径：/etc/nginx/
(2) PID目录：/var/run/nginx.pid
(3) 错误日志：/var/log/nginx/error.log
(4) 访问日志：/var/log/nginx/access.log
(5) 默认站点目录：/usr/share/nginx/html

```
事实上，只需知道Nginx配置路径，其他路径均可在/etc/nginx/nginx.conf 以及/etc/nginx/conf.d/default.conf 中查询到。

3. 启动nginx: 
```
systemctl start nginx.service
```
4. 开机启动：
```
sudo systemctl enable nginx.service 
```

## 配置SSL通配符证书

1. 安装Cerbot
```
wget https://dl.eff.org/certbot-auto
```
2. 修改权限
```
chmod a+x ./certbot-auto
```
3. 初始化
```
./certbot-auto
```
4. 添加域名解析
```
./certbot-auto certonly --manual -d *.echainnet.com -d echainnet.com --agree-tos --no-bootstrap --manual-public-ip-logging-ok --preferred-challenges dns-01 --server https://acme-v02.api.letsencrypt.org/directory
```
	tips:在DNS记录中添加一个 _acme-challenge 前缀的域名 TXT记录，记录的内容为中间显示的随机码
5. 创建一个 2048 位的 Diffie-Hellman 文件存放证书
```
openssl dhparam -out /etc/letsencrypt/live/dhparams.pem 2048
```

## 配置Ngin转发规则
```
#设置http全部跳转https
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        server_name _;
        return 301 https://$host$request_uri;
}
#设置证书及跳转
    server {
    listen 443 ssl;
    server_name blog.echainnet.com;
    # 配置站点证书文件地址
    ssl_certificate      /etc/letsencrypt/live/echainnet.com/fullchain.pem;
    # 配置证书私钥
    ssl_certificate_key  /etc/letsencrypt/live/echainnet.com/privkey.pem;
    
    # 配置 Diffie-Hellman 交换算法文件地址
    ssl_dhparam          /etc/letsencrypt/live/dhparams.pem;
    
    # 配置服务器可使用的加密算法
    ssl_ciphers 'ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS';
    
    # 指定服务器密码算法在优先于客户端密码算法时，使用 SSLv3 和 TLS 协议
    ssl_prefer_server_ciphers  on;
    
    # ssl 版本 可用 SSLv2,SSLv3,TLSv1,TLSv1.1,TLSv1.2 
    # ie6 只支持 SSLv2,SSLv3 但是存在安全问题, 故不支持
    ssl_protocols        TLSv1 TLSv1.1 TLSv1.2;
    
    # 配置 TLS 握手后生成的 session 缓存空间大小 1m 大约能存储 4000 个 session
    ssl_session_cache          shared:SSL:50m;
    # session 超时时间
    ssl_session_timeout        1d;
    
    # 负载均衡时使用 此处暂时关闭 详情见 https://imququ.com/post/optimize-tls-handshake.html 
    # 1.5.9 及以上支持
    ssl_session_tickets off;
    
    # 浏览器可能会在建立 TLS 连接时在线验证证书有效性，从而阻塞 TLS 握手，拖慢整体速度。OCSP stapling 是一种优化措施，服务端通过它可以在证书链中封装证书颁发机构的 OCSP（Online Certificate Status Protocol）响应，从而让浏览器跳过在线查询。服务端获取 OCSP 一方面更快（因为服务端一般有更好的网络环境），另一方面可以更好地缓存 以上内容来自 https://imququ.com/post/my-nginx-conf-for-wpo.html
    # 1.3.7 及以上支持
    ssl_stapling               on;
    ssl_stapling_verify        on;
    # 根证书 + 中间证书
    ssl_trusted_certificate    /etc/letsencrypt/live/echainnet.com/fullchain.pem;
    
    # HSTS 可以告诉浏览器，在指定的 max-age 内，始终通过 HTTPS 访问该域名。即使用户自己输入 HTTP 的地址，或者点击了 HTTP 链接，浏览器也会在本地替换为 HTTPS 再发送请求,/imququ.com/post/sth-about-switch-to-https.html
    add_header Strict-Transport-Security max-age=60;
    
    # 在此填写原本 http 协议中的配置
    location / {
        root   /usr/share/nginx/blog/html;
        index  index.html index.htm;
    }
    
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```