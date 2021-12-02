
## 当前环境
> 阿里云服务器：centos 7.2


## 相关指令

docker中搜索镜像

```
docker search nginx
```

docker中拉取nginx镜像，不添加版本号默认latest

```
docker pull nginx
```

docker创建一个nginx容器

```
docker run -d --name=test-nginx -p 80:80 nginx
```

进入到docker容器的nginx

```
docker exec -it test-nginx /bin/bash
```

复制docker中nginx里面网站的内容到宿主机

```
docker cp test-nginx:/usr/share/nginx/html/ ./
```

复制宿主机里面的内容到docker的nginx容器里面

```
docker cp ./html/index.html test-nginx:/usr/share/nginx/html/
```



## 上面的操作每次修改都要复制进去就很麻烦，于是

先了解到nginx一些目录结构

nginx的网页内容目录：/usr/share/nginx/html

nginx的配置文件目录：/ect/nginx/nginx.conf

nginx的log文件路径：/var/log/nginx/error.log

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



## 把本地的目录挂载或者说是映射到docke中的nginx容器上

- ### 首先在宿主机上创建一些文件夹

```
mkdir wwwroot

mkdir logs

mkdir conf
```

- ### 在文件夹下面创建一些文件

```
touch nginx.conf（把上面配置文件里面的内容拷进去）

vi index.html(编辑一个简单的html文件)

touch error.log
```

- ### 创建完成后在我本地的宿主机对应的文件是

```
/home/docker/nginx/conf/nginx.conf

/home/docker/nginx/wwwroot/index.html

/home/docker/nginx/logs/error.log
```

- ### 创建完成后去run

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

- ### 如果此时再修改nginx.conf里面的内容不生效

重启一下

  ```
  docker exec -it containerName service nginx reload
  ```
  
但是自己用这个方法并不成功只好先 `stop` 再 `start` 了

