## 当前环境
当前环境是基于 `Docker` 部署 `Nginx` 下的 `ssl` 证书配置

## 在nginx上面配置二级域名

`Docker`上创建 `Nginx` 请参考 `Docker部署Nginx` 这篇文章，首先在 `DNS` 解析上添加一条记录

![image-20200527151221885.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832922811960918016.png)

在配置文件中添加多一个 `server` 节点就行

![image-20200527151604933.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832922887378698240.png)

还有多一个 `upstream`

![image-20200527151627170.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832922976889339904.png)

重启一下，ok。

## 配置https证书

首先先把证书下载下来，我是在阿里云开通的免费证书。 

![image-20200527155420328.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832923042756689920.png)

然后用 `ftp` 传到服务器上面

![image-20200527155547327.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832923116593217536.png)

上面我已经创建与运行了这个容器，现在需要把证书重新挂载到 `nginx` 中，所以需要先停止运行以及删除了这个 `nginx` 容器，先 `docker stop containerName` 再 `docker rm containerName`

![image-20200527155903996.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832923193806159872.png)

然后就是构建容器并且挂载目录了，这里注意要多开一个端口 `443` ，还有就是我的服务器是阿里云的所以得在后台添加安全组开启 `443` 的端口

```shell
docker run -d --name=my-nginx -p 80:80 -p 443:443 -v/home/docker/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v/home/docker/nginx/ssl:/etc/nginx/ssl -v/home/docker/nginx/wwwroot:/usr/share/nginx/html -v/home/docker/nginx/logs/error.log:/var/log/nginx/error.log nginx:latest
```

然后就是修改配置文件了，我修改的内容如下，也就是在原来的基础上再添加一个 `server` 节点

```
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}



http {

    upstream taobao {
	server 172.16.68.58:8088 weight=5;
    }
    upstream blog {
	server 172.16.68.58:8080 weight=2;
    }


    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    
    access_log  /var/log/nginx/access.log  main;
    
    sendfile        on;
    #tcp_nopush     on;
    
    keepalive_timeout  65;
    
    #gzip  on;
    
    server {
	listen      80;
	server_name shop.lzyan.fun;
	location / {
	    proxy_pass http://taobao;
	}
    }
    
    server {
    listen    80;       #侦听80端口，如果强制所有的访问都必须是HTTPs的，这行需要注销掉
    listen    443 ssl;
    server_name  www.lzyan.fun;             #域名

    # 增加ssl
    #ssl on;        #如果强制HTTPs访问，这行要打开
    ssl_certificate /etc/nginx/ssl/3262897_lzyan.fun.pem;
    ssl_certificate_key /etc/nginx/ssl/3262897_lzyan.fun.key;

    ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  5m;

     # 指定密码为openssl支持的格式
     ssl_protocols  SSLv2 SSLv3 TLSv1.2;

     ssl_ciphers  HIGH:!aNULL:!MD5;  # 密码加密方式
     ssl_prefer_server_ciphers  on;   # 依赖SSLv3和TLSv1协议的服务器密码将优先于客户端密码

     # 定义首页索引目录和名称
     location / {
       proxy_pass http://blog;
     }

    #重定向错误页面到 /50x.html
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
    
    }
}
```

然后重启一下就OK了。