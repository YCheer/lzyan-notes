## 1、上传代码

**首先把准备好源码通过ftp上传到服务器目录**

![image-20200518154722655.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832919149826015232.png)

**创建Dockerfile文件：touch Dockerfile**

![image-20200518154843246.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832919333989515264.png)

**Dockerfile文件内容**

```
#基于镜像node，这个版本号最好是与开发坏境的保持一致，防止带来不必要的麻烦
FROM node:10.15.3
#作者
MAINTAINER lzyan.fun
#参数，node的环境为生产环境
ENV NODE_ENV=production
#任意ip
ENV HOST 0.0.0.0
#容器内创建目录
RUN mkdir -p /taobaounion
#复制当前的内容到容器内容部目录blog
COPY . /taobaounion
#切换工作目录到
WORKDIR /taobaounion
#暴露端口3000，默认端口
EXPOSE 3000
#配置npm的远程仓库
RUN npm config set registry https://registry.npm.taobao.org
#安装依赖
RUN npm install
#构建，生成dist文件
RUN npm run build
#start
CMD ["npm","start"]

```


## 2、构建镜像

**这里注意要在Dockerfile文件的目录下面执行**

```
docker build -t lzyan-taobao-union .
```

![image-20200518155206184.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832919476985921536.png)

长途跋涉，共12步构建完成，查看一下当前的镜像


![image-20200518155647412.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832919588562796544.png)

## 3、启动镜像
```
docker run --name=lzyan-taobao-union -p 8088:3000 -dt lzyan-taobao-union
```

![image-20200518160903778.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832919670318170112.png)

到这里就已经成功啦，这里的8088是宿主机的端口号，我是用的是阿里云的服务器，所以这里需要在阿里云的控制台安全组上面进行端口开放。

最后通过域名指定8088端口访问成功

![image-20200518161225363.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832919759363244032.png)
