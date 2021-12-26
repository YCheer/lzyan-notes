
## 当前环境
> 阿里云服务器：centos 7.2


## 相关指令

`docker` 中搜索镜像

```
docker search nginx
```

`docker` 中拉取 `nginx` 镜像，不添加版本号默认 `latest`

```
docker pull nginx
```

`docker` 创建一个 `nginx` 容器

```
docker run -d --name=test-nginx -p 80:80 nginx
```

进入到 `docker` 容器的 `nginx`

```
docker exec -it test-nginx /bin/bash
```

复制 `docker` 中 `nginx` 里面网站的内容到宿主机

```
docker cp test-nginx:/usr/share/nginx/html/ ./
```

复制宿主机里面的内容到 `docker` 的 `nginx` 容器里面

```
docker cp ./html/index.html test-nginx:/usr/share/nginx/html/
```

## 简化操作

先了解到 `nginx` 一些目录结构

`nginx` 的网页内容目录：`/usr/share/nginx/html`

`nginx` 的配置文件目录：`/ect/nginx/nginx.conf`

`nginx` 的 log 文件路径：`/var/log/nginx/error.log`

这是配置文件 `nginx.conf` 里面的内容

```
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
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
        listen       80;
        server_name  localhost;
    
        #charset koi8-r;
        #access_log  /var/log/nginx/host.access.log  main;
        
        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }
        
        #error_page  404              /404.html;
        
        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
        
        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}
        
        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}
        
        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    
    }

}
```



## 本地的目录挂载

- ### 宿主机上创建一些文件夹

```
mkdir wwwroot

mkdir logs

mkdir conf
```

- ### 创建文件

```
touch nginx.conf（把上面配置文件里面的内容拷进去，此文件在conf文件夹下）

vi index.html（编辑一个简单的html文件，此文件在wwwroot文件夹下）

touch error.log（此文件在logs文件夹下）
```

- ### 本地宿主机对应文件

```
/home/docker/nginx/conf/nginx.conf

/home/docker/nginx/wwwroot/index.html

/home/docker/nginx/logs/error.log
```

- ### 挂在执行 run

```
docker run -d --name=test-nginx -p 80:80 -v/home/docker/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v/home/docker/nginx/wwwroot:/usr/share/nginx/html -v /home/docker/nginx/logs/error.log:/var/log/nginx/error.log nginx:latest
```

> `-d`表明是后台运行
>
>`--name` 是容器的名称
>
>`-v ` 是挂载目录
>
>`\` 翻斜杠是shell里面的连接，如果写成一行会很长，用反斜杠来把上下行连接，形成一行命令
>
>`nginx` 其实就是镜像的名称，有版本可以加版本上去没有就是默认最新版本

- ### 如果再修改nginx.conf里面的内容不生效

重启

  ```
  docker exec -it containerName service nginx reload
  ```
  
但是自己用这个方法并不成功只好先 `stop` 再 `start` 了

