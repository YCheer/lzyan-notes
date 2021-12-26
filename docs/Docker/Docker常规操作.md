## Centos安装docker

注意： `Docker` 运行在 `CentOS-6.5` 或更高的版本的 `CentOS` 上，要求系统为 `64位` 、系统内核版本为 `2.6.32-431` 或者更高版本。

- 查看当前内核的版本

```
uname -r
```

- 下载 `docker`

```
yum install docker
```

- 安装完成，启动 `docker` 服务，并且设置开机启动

```
systemctl start docker.service
systemctl enable docker.service
```

- 配置使用加速器，因为安装的 `Docker` 默认是取 `Docker Hub` 找想要的镜像的，国外的肯定没有国内快，所以一般会配置一个加速器

```
vi /etc/docker/daemon.json
#添加:
{
	"registry-mirrors":["https://registry.docker-cn.com"],
	"live-restore":true
}
```

- 然后重启 `docker`

```
systemctl restart docker
```

- 检验是否返回版本信息则安装正常

```
docker version
```

## 基础操作

- 在 `docker hub` 上查看 `nginx` 镜像

```linux
docker search nginx
```

- 在 `doncker hub` 上面拉取 `nginx` 镜像，默认为 `latest` 版本可以不写

```
docker pull nginx:latest
```

- 查看镜像

```
docker images
```

- 查看正在启动的容器

```
docker ps
```

- 查看所有的容器

```
docker ps -a
```

- 根据镜像创建容器并且启动容器，容器内部一个端口，可以映射到宿主机多个端口

```
docker run --name=customName -p 宿主机端口:容器端口 -dt containerName:version
```

- 容器已存在，启动容器

```
docker start containerName
```

- 容易已经启动，停止启动

```
docker stop containerName
```

- 删除容器

```
docker rm containerName
```

- 删除镜像

```
docker rmi imagesName
```

- 导出容器

```
docker export containerName>路径以及tar包名，直接写自定义的包名就保存在当前目录
```

- 导入容器，导入的容器要run的话，后面需要加 `command` ，可用 `docker ps -a` 查看所导出的容器的 `command`

```
docker import tar包路径 customImagesName:version(default latest)
```

- 导出镜像

```
docker save >自定义tar包名以及路径 imagesName:version(default latest)
```

- 导入镜像

```
docker load >路径tar包名
```

- 查看容器启动后的日志

```
docker logs containerName
```

- 用 `Dockerfile` 构建镜像, `.`就是 `Dockerfile` 在当前目录下面

```
docker build -t customImageName .
```

- 登录某容器

```
docker exec -it contanierName /bin/bash
```