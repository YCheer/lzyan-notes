### Maven 打包错误：Failed to execute goal on project...

在一个多模块的项目中，各个模块必定是有多种依赖的关系，打包的时候在 `root` 模块中按顺序执行 `mvn clear` 和 `mvn install`，需要打包成可执行 `jar` 包的模块在 `pom` 文件中记得添加 `spring-boot-maven-plugin` 插件

### Maven 命令 install 和 package 的区别

Maven 的 install 命令，做了两件事情：

1. 将项目打包（jar/war），将打包结果放在项目的 `target` 目录下
2. 同时将打包结果放到 **本地仓库** 的相应目录中，共其他项目或者模块使用

Maven 的 package 命令：

1. 将项目打包（jar/war），将打包结果放在项目的 `target` 目录下

### spring-boot-maven-plugin 的作用

通常在 `pom` 文件中的 `build` 节点下有

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <version>2.0.3.RELEASE</version>
    <executions>
        <execution>
            <goals>
                <goal>repackage</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

他的作用是能够将 `SpringBoot` 应用打包成可执行的 `jar` 或 `war` 文件。可见，当去掉这个依赖之后打包出来的jar包会非常小，这是由于没有这个插件导致没把依赖的包打进去，所以也就是无法执行的。


