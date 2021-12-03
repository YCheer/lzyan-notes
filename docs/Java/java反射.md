
## 定义

Java反射机制是在运行状态中，对任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的属性。简单来说就是动态获取信息以及调用对象方法的功能就是反射

## 介绍

反射其实就是围绕Class对象和java.lang.reflect类库学习，学习它的各种API。

java反射框架提供以下功能：

- 在运行时判定任意一个对象所属的类
- 在运行时构造任意一个类的对象
- 在运行时判定任意一个类所具有的成员变量和方法
- 在运行时调用任意一个对象的方法

## 优点

使用反射机制，代码可以在运行时装配，提高程序的灵活和扩展性，降低耦合度，提高自适应。它允许程序创建和控制任何类的对象，无需硬性编码目标类

## 缺点

性能问题：使用反射基本上是一种解释的操作，JVM无法对这些代码进行优化，因此反射操作的效率要比非反射操作效率低很多。反射机制主要应用在对灵活性和扩展性要求比较高的系统框架上，对性能要求高的程序不太适合。

安全限制：使用反射技术要求程序必须在一个没有安全限制的环境中运行

内部暴露：由于反射允许代码执行在一些正常情况下不被允许的操作（私有的属性和方法），所以使用反射可能会导致意料之外的副作用，如代码有功能上的错误，降低了可移植性。反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化。

## 反射机制相关的类

| 类名          | 用途                                             |
| ------------- | ------------------------------------------------ |
| Class类       | 代表类的实体，在运行的Java应用程序中表示类和接口 |
| Field类       | 代表类的成员变量（成员变量也称为类的属性）       |
| Method类      | 代表类的方法                                     |
| Constructor类 | 代表类的构造方法                                 |

## 为什么需要反射机制

其实看了这么多概念性的东西，到底也是不太明白为什么而需要反射这样的机制，所以还是得通过一些实践的例子来理解。

### 案例一

在学jdbc的时候，我们肯定会见到这样的一段代码

```java
Class.forName("com.mysql.jdbc.Driver");
//获取与数据库连接的对象
connection = DriverManager.getConnection("jdbc:mysql//localhost:3306/myblog","root","password");
//获取执行sql语句的statement对象
statement = connection.createStatement();
//执行sql语句
statement.executeQuery("SELECT * FORM users");
```

后来过渡到以配置的方式

```java
//获取配置文件的读入流
InputStream inputStream = 
UtilsDemo.class.getClassLoader().getResourceAsStream("db.properties");
Properties properties = new Properties();
properties.load(inputStream);
//获取配置文件的信息
driver = properties.getProperty("driver");
url = properties.getProperty("url");
username = properties.getProperty("username");
password = properties.getProperty("password");
//加载这个驱动类
Class.forName(driver);
```

这样子写就是在需要修改的部分，都在配置里面进行修改，而不用修改代码了。

但是有疑问的是，这修改配置也是修改代码啊。好像这也没有错，但是对于在企业工作的时候，一个项目总不能是永远一个人在开发的，当你接到别人的项目的时候想要修改数据库的时候，你如果像前面的那种写法，那就得去找到对应的接口修改其对应的代码，或许你还会找半天还找不到。并且，改代码的风险要比这种配置的方式要大，所以通过这种配置的方式来规避一些不必要的风险。而这种通过可配置的方法，其内部就很可能通过反射的机制来实现的。

### 案例二

Servlet和Spring MVC中，Servlet是通过getParameter()来填充值的，而Spring MVC中却是通过约定好的JavaBean的字段名来把值填充进去的

Servlet:

```java
//通过html的name属性获取值
String username = request.getParameter("username");
String password = request.getParameter("password");
String gender = request.getParameter("gender");
//获取到文本域的值
String description = request.getParameter("textarea");
```

Spring MVC:

```java
@RequestMapping("/save")
@ResponseBody
public String taskSave(PushConfig pushConfig){
	String name = pushConfig.getName();
}
```

Spring MVC这种简单的写法其实就是使用反射来实现的。在编写JavaBean的时候，保持字段名和参数名相同，就能自动装配而得到对应的值。**屏蔽掉实现的细节，让使用者更加方便好用**

## 总结

虽然大部分人在开发的过程中使用反射来编写代码其实不多，因为反射的机制比较多运用在组件/框架，但是在学习框架时，我们很多时候只是直接拿来用不懂里面的实现原理的，那就很有必要学习反射的机制来更深一层地学习这个框架了。

