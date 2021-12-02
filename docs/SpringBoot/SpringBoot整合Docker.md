### 准备一个Spring Boot项目

在 `pom.xml` 文件中有Spring Boot 2.0相关依赖
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.5.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```
web和测试的依赖
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```
创建一个类 `DockerController` ，编写一个index方法，返回的是hello Docker
```java
@RestController
public class DockerController {

    @RequestMapping("/")
    public String index(){
        return "hello Docker";
    }
}
```

### Spring Boot项目添加Docker的依赖

在 `pom.xml-properties` 中添加Docker镜像名plugins中添加Docker插件

![image-20200312004910503.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832920418670084096.png) 

![image-20200312004933186.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832920485007196160.png) 

```xml
<docker.image.prefix>springboot</docker.image.prefix>
```

```xml
	<!-- Docker maven plugin -->
	<plugin>
		<groupId>com.spotify</groupId>
		<artifactId>docker-maven-plugin</artifactId>
		<version>1.0.0</version>
		<configuration>
			<imageName>${docker.image.prefix}/${project.artifactId}</imageName>
			<dockerDirectory>src/main/docker</dockerDirectory>
			<resources>
				<resource>
					<targetPath>/</targetPath>
					<directory>${project.build.directory}</directory>
					<include>${project.build.finalName}.jar</include>
				</resource>
			</resources>
		</configuration>
	</plugin>
	<!-- Docker maven plugin -->
```

src/main/docker目录下创建 `Dockerfile` 文件，用来配置说明如何构建镜像
```docker
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD spring-boot-docker-1.0.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

文件解析：构建jdk基础环境，添加Spring Boot Jar到镜像
1. FROM ，表示使用 Jdk8 环境 为基础镜像，如果镜像不是本地的会从 DockerHub 进行下载
2. VOLUME ，VOLUME 指向了一个`/tmp`的目录，由于 Spring Boot 使用内置的Tomcat容器，Tomcat 默认使用`/tmp`作为工作目录。这个命令的效果是：在宿主机的`/var/lib/docker`目录下创建一个临时文件并把它链接到容器中的`/tmp`目录
3. ADD ，拷贝文件并且重命名
4. ENTRYPOINT ，为了缩短 Tomcat 的启动时间，添加`java.security.egd`的系统属性指向`/dev/urandom`作为 ENTRYPOINT

启动项目，访问http://127.0.0.1:8080/，返回hello Docker，Spring Boot项目配置以及添加Docker依赖正常

![image-20200312004352236.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832920672341590016.png)

### 安装Docker

用Xshell连接云主机（centOS7），输入命令

```linux
yum install docker
```

安装完成，输入命令启动docker服务，并将其设置为开机启动

```linux
systemctl  start docker.service
systemctl  enable docker.service
```

配置使用加速器，因为安装的Docker默认是去 `Docker Hub` 找想要的镜像，因为国外的肯定没有国内的快，所以一般都会配置一个加速器

```
vi  /etc/docker/daemon.json
#添加：
{
    "registry-mirrors": ["https://registry.docker-cn.com"],
    "live-restore": true
}
```

然后重启docker

```
systemctl restart docker
```

检验是否返回版本信息则安装正常

```
docker version
```

![image-20200311224518049.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832920766377885696.png)

### 安装JDK

此处省略

### 安装MAVEN

本地下载好Maven安装包，连接https://maven.apache.org/download.cgi，然后使用Xftp把文件上传到云服务器

![image-20200311230350667.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832920846065467392.png)

然后解压，移动

```
##解压
tar vxf apache-maven-3.6.3-bin.tar.gz
##移动
mv apache-maven-3.6.3-bin.tar.gz /usr/local/maven
```

配置环境变量，在/etc/profile添加

```
##打开修改profile文件
vim /etc/profile
##添加
MAVEN_HOME=/usr/local/maven
export MAVEN_HOME
export PATH=${PATH}:${MAVEN_HOME}/bin
```

然后执行`source /etc/profile`使环境变量生效

输入`mvn -version` 返回版本信息则安装正常。

- 这样子已经构建好整个环境的配置了。

### 使用Docker部署Spring Boot项目

先打包好Spring Boot项目，打包方式有很多。这里使用maven构建工具进行打包

```mvn
mvn package
```

然后进入项目的target目录启动项目

```
java -jar demo-0.0.1-SNAPSHOT.jar
```

看到启动信息，表示启动成功，环境配置并没有问题

![image-20200312105242488.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832920936347860992.png)

现在开始使用DockerFile构建镜像，这里需要注意的是构建镜像要在有pom.xml文件的目录下，具体为什么没深究，还不清楚

```
mvn package docker:build
```

第一次构建会比较慢，构建成功之后会看到如下信息。

![image-20200312130616968.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832921031709556736.png)

在成功之前我构建过程出现了一个错误，但红框中也说明了是什么问题。

![image-20200312130805668.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832921155760291840.png)

所以在DockerFile文件中修改对应你的jar包的名字

![image-20200312130904315.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832921235657588736.png)

接下来用`docker images`命令查看构建好的镜像

![image-20200312131059098.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832921326334246912.png)

可以看到，`springboot/demo` 就是构建好的镜像，然后运行这个镜像

```
docker run -p 8080:8080 -t springboot/demo
```

可以看到此时输出日志信息

![image-20200312131456862.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832921417770074112.png)

此时你按Ctrl+c退出了，这个镜像也还在运行中，可访问

![image-20200312131711386.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832921507792420864.png)

输入`docker ps`可以查看正在运行得镜像

![image-20200312131816746.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832921614113832960.png)

到此说明使用Docker部署Spring Boot项目已经成功了！

如果你想要停止，此时你已经用docker ps查看了所有的运行的进程，然后通过`docker stop id` 停止运行

![image-20200312132411763.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832921692845113344.png)

进程停止后，可以通过`docker rm id`删除承载该进程的容器

![image-20200312132916472.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832921779709149184.png)

最后通过运行`docker rmi id`删除镜像

![image-20200312133318064.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832921864924823552.png)