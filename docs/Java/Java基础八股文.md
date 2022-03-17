# 参考
> Java八股文整合系列文档搜集于各大平台，据理解和需求整合的笔记。
> 
> 平台：[javaGuide](https://javaguide.cn/)、[java3y](http://javainterview.gitee.io/)、[cs-notes](http://www.cyc2018.xyz/)、廖雪峰的Java教程、javacore


## 基础概念

### Java语言的特点有哪些？
1. **面向对象：** 封装，继承，多态
2. **跨平台：** Java虚拟机实现跨平台
3. **容易实现多线程：** Java是一门高级语言，高级语言会对用户屏蔽很多底层的实现细节
4. **健壮性：** Java的强类型机制、异常处理、垃圾的回收等都是健壮性的保证
5. **容易进行网络编程和分布式项目开发：** Java语言诞生本身就是为了简化网络编程设计的，Java中有net api，它提供了网络应用编程的类库
6. **编译与解释并存：** JIT编译器完成第一次编译后，其会将字节码对应的机器码保存下来，机器码的运行效率高于Java解释器的

### Java与其他语言相比，有什么优点？
与C/C++相对比，Java语言是一种 **完全的面向对象** 语言，虽然它的底层（运行时库）是用C语言开发的，但不依赖于C。

因为Java的运行是在运行时库的支持下进行的，所以运行效率比起可以更接近底层的C/C++来说会有所影响，不过Java的类库采用了很好的设计理念，比如：**Java不提供指针来直接访问内存，程序内存更加安全、Java有自动内存管理垃圾回收机制(GC)，不需要程序员手动释放无用内存、C++同时支持方法重载和操作符重载，但是Java 只支持方法重载（操作符重载增加了复杂性，这与 Java 最初的设计思想不符）**。Java一度成为了业界的一种标准开发语言，它的跨平台特性更是受到了很多开发者的青睐，只需要开发一次就能在所有安装了Java运行时库的环境运行。

与C#相比，C#是微软开发的一种编程语言，语法与Java类似，运行原理与Java也很类似，也是通过运行时库的支持运行。不过支持的平台有限， **Java几乎被所有平台支持** ，而C#目前只被Windows和Linux支持，Windows下的支持是由微软自己开发的，而Linux下的支持则由mono支持。可实际上，mono也是把C#应用转化为Java应用，所以本质上C#仍只是被微软自己操作系统支持。应用平台的限制是它最大的缺点。

### JVM、JDK、JRE、JavaSE、JavaEE、JavaME、GC这些名词分别是什么意思？
**JVM：** Java Virtual Machine的缩写，Java虚拟机，是运行Java字节码的虚拟机，是跨平台的关键。JVM有针对不同系统的特定实现（Windows，Linux，macOS），目的是使用相同的字节码，他们都会有相同的结果。JVM并不是只有一种，只要满足JVM规范，每个公司、组织或者个人都可以开发自己的专属JVM。也就是说平时接触到的HotSpot VM仅仅只是JVM规范的一种实现。Java虚拟机有自己完善的硬件架构，如处理器、堆栈、寄存器等，还具有相应的指令系统。JVM屏蔽了与具体操作系统平台相关的信息，使得Java程序只需生成在Java虚拟机上运行字节码，就可以在多种平台上不加修改就运行。

**JDK：** Java Development Kit的缩写，Java开发工具包，是功能齐全的Java SDK。JDK是整个Java的核心，包括了Java运行环境（JRE）所拥有的一切，还有编译器（javac）和工具（javadoc和jdb），它能够用来创建和编译程序。

**JRE：** Java Runtime Environment的缩写，它是运行已经编译Java程序所需的所有内容的集合，包含JVM标准实现和JAVA核心类库，它能用来编译程序，不能用于创建新程序。所以如果只是运行Java程序的话，那么只需要安装JRE就可以了。如果是要进行一些Java编程，就需要安装JDK。但，这并不绝对。例如：如果要使用JSP部署Web程序，从技术上讲只需要应用程序程序服务器（Tomcat等）中运行Java程序，但是因为应用程序服务器会将JSP转换为Java Servlet，而Servlet需要JDK来编译，此时便是需要下载JDK的。

**JavaSE：** Java Standard Edition的缩写，标准版，一开始叫J2SE，是常用的一个版本，从JDK5.0开始改名为Java SE，主要应用于桌面应用软件的编程，是Java EE和Java ME的基础。

**JavaEE：** Java Enterprise Edition的缩写，企业版，JavaEE是J2EE的一个新的名称，是企业开发使用的一个版本，主要用于分布式的网络程序的开发。

**JavaME：** Java Micro Edition的缩写，微型版，一般用于移动设备和嵌入式设备，如：机顶盒、移动电话和PDA等，为它们所提供Java语言平台，包括虚拟机和一系列标准化的Java API。

**GC：** Garbage Collection的缩写，垃圾回收，当Java虚拟机发觉内存资源紧张时，则会自动地去清理无用对象（没有被引用的对象）所占用的内存空间。

### 什么是字节码？采用字节码的好处是什么？

字节码是程序的一种低级表示，Java源代码经过 `编译器编译` 后产生的文件（即扩展为.class的文件），它不面向任何特定的处理器 `只面向虚拟机` 。不论该字节码文件来自何方，由哪种编译器编译，甚至是手写字节码文件，只要符合Java虚拟机的规范，那么Java虚拟机就能够执行该字节码文件。

字节码文件中，一般包含以下部分：
- 版本号信息
- 静态常量池（符号常量）
- 类相关的信息
- 字段相关的信息
- 方法相关的信息
- 调试相关的信息

可以说，大部分信息都是通过常量池中的符号常量来表述的

采用字节码的好处是，Java 语言通过字节码的方式在一定程度上解决了传统解释型语言执行效率低的问题，同时又保留了解释型语言可移植的特点。所以，Java 程序运行时相对来说还是高效的（不过，和 C++，Rust，Go 等语言还是有一定差距的），而且由于字节码并不针对一种特定的机器，因此 Java 程序无须重新编译便可在多种不同操作系统的计算机上运行。

### JVM能有几个实例，以及怎么利用JVM实现跨平台的？
每个 Java 程序对应一个 JVM 实例，当一个 Java 程序运行时就会创建一个 JVM 实例，因此 JVM 实例的个数取决于同时执行的程序个数。

Java 跨平台因为有 JVM 屏蔽了底层操作系统， `Java 源代码（*.java）` 经过 ` Java 编译器` 编译成 `Java 字节码（*.class）`，执行 Java 字节码，Java 字节码经过 JVM 解释为具体平台的具体指令并执行。不同平台有不同的 JVM ，主流平台都提供了 JVM ，所以 Java 字节码可以在主流平台上能够解释执行。在这个意义上 Jav a是跨平台的，也就是说： Java 的字节码是跨平台的。

### 从JVM的角度，总结一下Java源码到执行的过程？

这个过程可以总结为四个步骤：编译->加载->解释->执行

- **编译：** 经过 语法分析、语义分析、注解处理 最后才生成会 class 文件
- **加载：** 可以细分步骤为：装载->连接->初始化。装载则把 class 文件装载至 JVM ，连接则校验 class 信息、分配内存空间及赋默认值，初始化则为变量赋值为正确的初始值。连接里又可以细化为：验证、准备、解析
- **解释：** 则是把字节码转换成操作系统可识别的执行指令，在 JVM 中会有字节码解释器和即时编译器。在解释时会对代码进行分析，查看是否为「热点代码」，如果为「热点代码」则触发JIT编译，下次执行时就无需重复进行解释，提高解释速度
- **执行：** 调用系统的硬件执行最终的程序指令

### 简单描述一下Java源码到执行的过程？
  
Java 源代码 ----> 编译器 ----> jvm 可执行的 Java 字节码(即虚拟指令, .class 文件) ----> jvm ----> jvm 中 解释器 -----> 机器可执行的二进制机器码 ----> 程序运行。

### 简单描述一下JIT编译器？

当` .class 文件 --> 机器码`，这个过程中 JVM 类加载器首先加载字节码文件，然后通过解释器逐行解释执行，这种方式的执行速度会相对较慢。而且，有些方法和代码块是经常需要被调用的(也就是所谓的热点代码)，所以后来引进了 `JIT（just-in-time compilation）` 编译器，而 JIT 属于运行时编译。当 JIT 编译器完成第一次编译后，其会将字节码对应的机器码保存下来，下次可以直接使用。而我们知道，机器码的运行效率肯定是高于 Java 解释器的。这也解释了我们为什么经常会说 Java 是编译与解释共存的语言。

### 为什么说Java语言是“编译与解析并存”？

首先需要了解`编译型`和`解析型`语言分别是什么

- 编译型 ：编译型语言会通过编译器将源代码一次性翻译成可被该平台执行的机器码。一般情况下，编译语言的执行速度比较快，开发效率比较低。常见的编译性语言有 C、C++、Go、Rust 等等。

- 解释型 ：解释型语言会通过解释器一句一句的将代码解释为机器代码后再执行。解释型语言开发效率比较快，执行速度比较慢。常见的解释性语言有 Python、JavaScript、PHP 等等。

因为 Java 程序要经过先编译，后解释两个步骤，由 Java 编写的程序需要先经过编译步骤，生成字节码（ .class 文件），这种字节码必须由 Java 解释器来解释执行。
所以 Java 语言既具有编译型语言的特征，也具有解释型语言的特征。


## 基本语法

### Java有哪些数据类型？

定义：

Java语言是 `强类型语言` ，对于每一种数据都定义了明确的具体的数据类型，在内存中分配了不同大小的内存空间。

分类：

**基本数据类型**
- 数值型
  - 整数类型(byte,short,int,long)
  - 浮点类型(float,double)
- 字符型(char)
- 布尔型(boolean)

**引用数据类型**
- 类(class)
- 接口(interface)
- 数组([])

基本数据类型图

![](https://resource.lzyan.fun/PigGo/data_type.png)

### Java有哪些运算符？

运算符不只 Java 中有，其他语言也有运算符，运算符是一些特殊的符号，主要用于数学函数、一些类型的赋值语句和逻辑比较方面。以 Java 为例，有以下运算符

- **赋值运算符：** 例如int a = 10，把 = 号右边的值复制给左边，右边的值可以是任何常数、变量或者表达式，但左边的值必须是一个明确的，已经定义的变量
- **算数运算符：** + 加、- 减、* 乘、/ 除、% 取余
- **自增自减运算符：** ++、--，要注意放在前还是后
- **比较运算符：** > 大于、< 小于、== 等于、>= 大于等于、<= 小于等于、!= 不等于
- **逻辑运算符：** && 短路与、|| 短路或、! 逻辑非、& 逻辑与、| 逻辑或、^ 逻辑异或，其中短路与判断到false就停止往下的判断，短路或直到判断到结果为true
- **按位运算符：** & 按位与、| 按位或、~ 按位非、^ 按位异或
- **移位运算符：** >> 右移、<< 左移
- **三元运算符：** 条件表达式?表达式1:表达式2

### Java有哪些常见的关键字？

![](https://resource.lzyan.fun/PigGo/20211202094253.png)

### switch是否能作用在byte、long、String上？

在 Java 5 以前，switch(expr)中，expr 只能是 byte、short、char、int。从 Java5 开始，Java 中引入了枚举类型，expr 也可以是 enum 类型，从 Java 7 开始，expr 还可以是字符串（String），但是长整型（long）在目前所有的版本中都是不可以的，如果值过于复杂，那么还是用 if 比较合适。

### &和&&的区别

 & 运算符有两种用法：按位与和逻辑与。

 && 运算符是短路与运算。

逻辑与跟短路与的差别是非常巨大的，虽然二者都要 求运算符左右两端的布尔值都是 true 整个表达式的值才是 true 。 && 之所以称为短路运算，是因为如果 && 左边的表达式的值是 false，右边的表达式会被直 接短路掉，不会进行运算。

注意：逻辑或运算符 （|） 和短路或运算符 （||） 的差别也是如此。

### 什么是自动类型转化？

`自动类型转换` 也称为`隐式类型转换`，是指不需要书写代码，由系统自动完成的类型转换。由于实际开发中这样的类型转换有很多，所以Java语言在设计时，并没有为该操作设计语法，而是由JVM完成。

转换规则为：从存储范围小的类型到存储范围大的类型。
```java
byte->short(char)->int->long->float->double
```
例如:因为字面量 1 是 int 类型，它比 short 类型精度要高，因此不能隐式地将 int 类型向下转型为 short 类型。
```java
short s1 = 1; // 编译器在编译时帮我们自动转换成对应的short类型
// s1 = s1 + 1; // 编译错误
```
但是使用 += 或者 ++ 运算符会执行隐式类型转换。
```java
s1 += 1;
s1++;
```
上面的语句相当于将 s1 + 1 的计算结果进行了向下转型：
```java
s1 = (short) (s1 + 1); //强转
```

### 怎样进行强制类型转换？

在Java中强制类型转换分为基本数据类型和引用数据类型两种。

转换的格式：**所要转换的类型 变量名 = （所要转换的类型）原类型**。

- **基本数据类型强制类型转换**

```java
double x = 3.14;
int y = (int) x; // 强转，损失精度 y的值为3
```

- **引用数据类型强制类型转换**

这种强制类型转换方式经常在多态中使用，当子类的实例被父类所引用时，只可以调用父类中的方法，如果想调用子类的所有方法，必须经过强制类型转换
```java
//SuperClass
public class SuperClass {
    public void superTest(){
        System.out.println("super class");
    }
}
//SubClass
public class SubClass extends SuperClass{
    @Override
    public void superTest() {
        System.out.println("override superTest");
    }

    public void subTest(){
        System.out.println("sub class");
    }
}
//main函数
public static void main(String[] args) {
    SuperClass superClass = new SubClass(); //子类的实例指向父类的引用
    superClass.superTest(); // override superTest 只能调用父类自己的方法，当子类重写了这个方法时调用的是子类重写的方法
    SubClass subClass = (SubClass) superClass;// 强转
    subClass.subTest(); // sub class
}
```

### continue、break 和 return 的区别是什么？

在循环结构中，当循环条件不满足或者循环次数达到要求时，循环会正常结束。但是，有时候可能需要在循环的过程中，当发生了某种条件之后 ，提前终止循环，这就需要用到下面几个关键词： 
 - **continue ：** 指跳出当前的这一次循环，继续下一次循环
 - **break ：** 指跳出整个循环体，继续执行循环下面的语句
return 用于跳出所在方法，结束该方法的运行。return 一般有两种用法：
 - **return; ：** 直接使用 return 结束方法执行，用于没有返回值函数的方法 
 - **return value; ：** return 一个特定值，用于有返回值函数的方法 

### 怎么用最有效率的方法计算 2 乘以 8？

2 << 3（左移 3 位相当于乘以 2 的 3 次方，右移 3 位相当于除以 2 的 3 次 方）。


## 面向对象

### 面向对象和面向过程的区别？

- **面向过程：**

  面向过程是一种以事件为中心的编程思想，编程的时候把解决问题的步骤分析出来，然后用函数把这些步骤实现，在一步一步的具体步骤中再按顺序调用函数

    优点：面向过程性能比面向对象高，因为面向对象类调用时需要实例化，开销比较大，比较消耗资源;当性能是最重要的考量因素的时候，比如单片机、嵌入式开发、Linux/Unix等一般采用面向过程开发

    缺点：面向过程没有面向对象易维护、易复用、易扩展

- **面向对象：**

  面向对象是一种以“对象”为中心的编程思想，把要解决的问题分解成各个对象，建立对象的目的不是为了完成一个步骤，而是为了描叙某个对象在整个解决问题的步骤中的属性和行为

    优点：面向对象易维护、易复用、易扩展。 因为面向对象有封装、继承、多态性的特性，所以可以设计出低耦合的系统，使系统更加灵活、更加易于维护

    缺点：面向对象性能比面向过程低

### 面向对象的特征有哪些？

- **抽象：**

抽象是将一类对象的共同特征总结出来从而构造类的过程，包括数据抽象和行为抽象两方面，抽象只关注对象有哪些属性和行为，并不关注这些行为的细节是什么。

- **封装：**

封装是把一个对象的属性私有化，同时提供一些可以被外界访问的属性的方法，如果属性不想被外界访问，我们大可不必提供方法给外界访问。但是如果一个类没 有提供给外界访问的方法，那么这个类也没有什么意义了。面向对象设计始于这个基本概念。

- **继承：**

继承是使用已存在的类的定义作为基础建立新类的技术，新类的定义可以增加新的数据或新的功能，也可以用父类的功能，但不能选择性地继承父类。通过使用继承，可以快速地创建新的类，可以提高代码的重用，程序的可维护性，节省大量创建新类的时间 ，提高我们的开发效率。

关于继承如下 3 点请记住：
1. 子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和方法子类是无法访问，只是拥有。
2. 子类可以拥有自己属性和方法，即子类可以对父类进行扩展。
3. 子类可以用自己的方式实现父类的方法，即重写父类方法。

- **多态：**

所谓多态就是指程序中父类或接口定义的引用变量所指向的具体类型和通过该引用变量发出的方法调用在编程时并不确定，而是在程序运行期间才确定。即一个引用变量到底会指向哪个类的实例对象，该引用变量发出的方法调用到底是哪个类中实现的方法，必须在由程序运行期间才能决定。运行时的多态是面向对象的精髓。

在Java中有两种形式可以实现多态：继承（多个子类对同一方法的重写）和接口 （实现接口并覆盖接口中同一方法）。

多态的特点：
1. 方法重载（overload）实现的是编译时的多态性（也称为前绑定），而方法重写（override）实现的是运行时的多态性（也称为后绑定）；
2. 引用类型变量发出的方法调用的到底是哪个类中的方法，必须在程序运行期间才能确定；
3. 多态不能调用“只在子类存在但在父类不存在”的方法；
4. 如果子类重写了父类的方法，真正执行的是子类覆盖的方法，如果子类没有覆盖父类的方法，执行的是父类的方法；

记住：其中封装、继承、多态是面向对象编程的三大特征

### 面向对象的五大基本原则是什么？

- **单一职责原则SRP(Single Responsibility Principle)**

是指一个类的功能要单一，不能包罗万象。如同一个人一样，分配的工作不能太多，否则一天到晚虽然忙忙碌碌的，但效率却高不起来。

- **开放封闭原则OCP(Open－Close Principle)**

一个模块在扩展性方面应该是开放的而在更改性方面应该是封闭的。比如：一个网络模块，原来只服务端功能，而现在要加入客户端功能，那么应当在不用修改服务端功能代码的前提下，就能够增加客户端功能的实现代码，这要求在设计之初，就应当将服务端和客户端分开，公共部分抽象出来。

- **里式替换原则LSP(the Liskov Substitution Principle LSP)**

子类应当可以替换父类并出现在父类能够出现的任何地方。比如你能代表你爸去你姥姥家干活~

- **依赖倒置原则DIP(the Dependency Inversion Principle DIP)**

高层次的模块不应该依赖低层次的模块，他们都应该依赖于抽象。抽象不应该依赖于具体实现，具体实现应该依赖于抽象。我们知道，依赖一定会存在于类与类、 模块与模块之间。当两个模块之间存在紧密的耦合关系时，最好的方法就是分离接口和实现：在依赖之间定义一个抽象的接口使得高层模块调用接口，而底层模块实现接口的定义，以此来有效控制耦合关系，达到依赖于抽象的设计目标。 

- **接口分离原则(the Interface Segregation Principle ISP)**

接口端不应该依赖它不需要的接口，一个类对另一个类的依赖应该建立在最小的接口上。如果一个提供接口的类中对于它的子类来说不是最小的接口，那么它的子类在实现该类的时候就必须实现一些自己不需要的功能，整个系统就会慢慢变得臃肿难以维护。

[详解](https://zhuanlan.zhihu.com/p/63215764)

### 抽象类和接口的区别？

**抽象类是用来捕捉子类的通用特性的。接口是抽象方法的集合。**

从设计层面来说，抽象类是对类的抽象，是一种模板设计，接口是行为的抽象，是一种行为的规范。

相同点：

1. 接口和抽象类都不能实例化
2. 都位于继承的顶端，用于被其他实现或继承
3. 都包含抽象方法，其子类都必须覆写这些抽象方法

不同点：

|    参数    |                                               抽象类                                               |                                                                               接口                                                                                |
| :--------: | :------------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|    声明    |                                    抽象类使用abstract关键字声明                                    |                                                                    接口使用interface关键字声明                                                                    |
|    实现    | 子类使用extends关键字来继承抽象类。如果子类 不是抽象类的话，它需要提供抽象类中所有声明的方法的实现 |                                            子类使用implements关键字来实现 接口。它需要提供接口中所有声明的 方法的实现                                             |
|   构造器   |                                         抽象类可以有构造器                                         |                                                                         接口不能有构造器                                                                          |
| 访问修饰符 |                                 抽象类中的方法可以是任意访问修饰符                                 | 接口方法默认修饰符是public。并且 不允许定义为 private 或者 protected , 从 Java 9 开始，允许将方法定义为 private，这样就能定义某些复用的代码又不会把方法暴露出去。 |
|   多继承   |                                    一个类最多只能继承一个抽象类                                    |                                                                      一个类可以实现多个接口                                                                       |
|  字段声明  |                                    抽象类的字段声明可以是任意的                                    |                                                               接口的字段默认都是 static 和 final 的                                                               |

备注：在 JDK1.8 以前，接口( interface )没有提供任何具体的实现，在 《JAVA编程思想》 中是这样描述的：“ interface 这个关键字产生了一个完全抽象的类，它根本就没有提供任何具体的实现。它允许创建者确定方法名、参数列表和返回类型，但是没有任何方法体。接口只提供了形式，而未提供任何具体实现”。但是这一限制在 JDK1.8 中被打破了， JDK1.8 开始，接口允许定义默认方法和静态方法，这是因为不支持默认方法的接口的维护成本太高了。在 JDK1.8 之前，如果一个接口想要添加新的方法，那么要修改所有实现了该接口的类，让它们都实现新增的方法。

```java
默认方法：
default 返回值类型 方法名(参数列表) { // 实现体 }
实现类中可以选择性重写接口的默认方法
静态方法：
静态方法的使用比较简单，和类中的静态方法没有什么区别
static 返回值类型 方法名(参数列表) { // 实现体 }

注意： static 和 default 关键字不能同时使用，即一个接口方法不可能既是静态的又是默认方法。
```

所以，我们可以为接口提供默认实现的方法了，并且不用强制子类来实现它。接口和抽象类各有优缺点，在接口和抽象类的使用选择上，必须遵守这样一个原则：

1. 行为模型应该总是通过接口而不是抽象类定义，所以通常是优先选用接口，尽量少用抽象类。并且从 JDK1.8 开始，接口也可以有默认的方法实现，使得修改接口的成本也变的很低。

2. 选择抽象类的时候通常是如下情况：需要定义子类的行为，又要为子类提供通用的功能。

### 普通类和抽象类的区别？

普通类不能包含抽象方法，抽象类可以包含抽象方法。

普通类可以直接实例化，抽象类不能直接实例化。

普通类使用final修饰后不可被继承，抽象类不能用final修饰。

### 创建一个对象用什么运算符?对象实例与对象引用有何不同?

创建一个对象用 `new` 运算符

- 对象实例：如 new Person() ，new 创建对象实例（对象实例在堆内存）。
- 对象引用：如 Person p = new Person() ，= 左边的对象引用指向右边的对象实例（对象引用存放在栈内存中）。

一个对象引用可以指向0个或1个对象，一个对象可以有n个引用指向它。

### 对象的相等与指向他们的引用相等,两者有什么不同？

对象的相等，比的是内存中存放的内容是否相等。而引用相等，比较的是他们指向的内存地址是否相等。

### super和this的区别？使用时需要注意什么？

**this：**

代表对象本身，可以理解为指向对象本身的一个指针。使用方式：
1. 普通的直接引用
2. 方法形参与成员名字重名，用 this 来区分
3. 引用构造函数：调用本类中另一种形式的构造函数

**spuer：**

 super 可以理解为是指向自己父类对象的一个指针，而这个父类指的是离自己最近的一个父类。使用方式：
1. 普通的直接引用
2. 子类中的成员变量或方法与父类中的成员变量或方法同名
3. 引用构造函数：子类调用父类中的某一个构造函数

**注意：**

1. 调用 super() 必须写在子类构造方法的第一行，否则编译不通过。子类一定会调用父类的构造函数来完成初始化工作，每个子类构造方法的第一条语句，都是隐含地调用 super() ，如果子类需要调用父类其它构造函数，那么就可以使用 super() 函数。
2. super() 和 this() 类似,区别是，super() 从子类中调用父类的构造方法，this() 在同一类内调用其它方法。
3. super() 和 this() 均需放在构造方法内第一行。
4. 尽管可以用 this 调用一个构造器，但却不能调用两个。
5. this 和super 不能同时出现在一个构造函数里面，因为 this 必然会调用其它的构造函数，其它的构造函数必然也会有 super 语句的存在，所以在同一个构造函数里面有相同的语句，就失去了语句的意义，编译器也不会通过。
6. this() 和 super() 都指的是对象，所以，均不可以在 static 环境中使用。包括：static 变量,static 方法，static 语句块。
7. 从本质上讲，this 是一个指向本对象的指针, 然而 super 是一个 Java 关键字。


### 重载和重写什么区别？

**重载：**
 - 必须具有不同的参数列表
 - 可以有不同的返回类型，只要参数列表不同就可以了
 - 可以有不同的访问修饰符
 - 可以抛出不同的异常

**重写：**
 - 参数列表必须完全与被重写的方法相同，否则不能称其为重写而是重载
 - 返回的类型必须一直与被重写的方法的返回类型相同，否则不能称其为重写而是重载
 - 访问修饰符的限制一定要大于被重写方法的访问修饰符
 - 重写方法一定不能抛出新的检查异常或者比被重写方法申明更加宽泛的检查型异常

**总结：**

| **区别点** | **重载方法** |                         **重写方法**                         |
| :--------: | :----------: | :----------------------------------------------------------: |
|  发生范围  |   同一个类   |                             子类                             |
|  参数列表  |   必须修改   |                         一定不能修改                         |
|  返回类型  |    可修改    |      子类方法返回值类型应比父类方法返回值类型更小或相等      |
|    异常    |    可修改    | 子类方法声明抛出的异常类应比父类方法声明抛出的异常更小或相等 |
| 访问修饰符 |    可修改    |            一定不能做更严格的限制（可以降低限制）            |
|  发生阶段  |    编译器    |                            运行期                            |

**综上：** `重载`就是同一个类中多个同名方法根据不同的传参来执行不同的逻辑处理，`重写`就是子类对父类方法的重新改造，外部样子不能改变，内部逻辑可以改变

**注意：** 关于重写的返回值类型,如果方法的返回类型是 void 和基本数据类型，则返回值重写时不可修改。但是如果方法的返回值是引用类型，重写时是可以返回该引用类型的子类的

### 类的构造方法的作用是什么？

构造方法主要作用是完成类的初始化工作。如果一个类没有声明构造方法，也可以执行，因为一个类即使没有声明构造方法也会有默认的不带参数的构造方法。

但是如果自己添加了类的构造方法（无论是否有参数），Java 就不会再添加默认的无参构造方法这时候。如果我们重载了有参的构造方法，记得都要把无参的构造方法也写出来（无论是否用到），因为这可以帮助我们在创建对象的时候少踩坑。

### 什么是内部类？

在Java程序中，通常情况下，我们把不同的类组织在不同的包下面，对于一个包下面的类来说，它们是在同一层次，没有父子关系：
```java
java.lang
├── Math
├── Runnable
├── String
└── ...
```
还有一种类，它被定义在另一个类的内部，所以称为内部类（Nested Class）。内部类本身就是类的一个属性，与其他属性定义方式一致。 Java 的内部类分为好几种，通常情况用得不多，但也需要了解它们是如何使用的。

**内部类分四种：成员内部类、局部内部类、匿名内部类和静态内部类。**

- **成员内部类**

定义在类内部，成员位置上的非静态，就是成员内部类。
```java
public class Outer {

    private static int radius = 1;
    private int count = 2;

    class Inner {
        public void visit() {
            System.out.println("visit outer static variable:" + radius);
            System.out.println("visit outer variable:" + count);

        }
    }
}
```
成员内部类可以访问外部类所有的变量和方法，包括静态和非静态，私有和公有。成员内部类依赖于外部类的实例，它的创建方式：外部类实例.new 内部类()，如下：
```java
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
inner.visit();
```
观察Java编译器编译后的.class文件可以发现，Outer类被编译为Outer.class，而Inner类被编译为Outer$Inner.class。

- **局部内部类**

定义在方法中的内部类，就是局部内部类
```java
public class Outer {

    private int out_a = 1;
    private static int STATIC_b = 2;

    public void testFunctionClass() {
        int inner_c = 3;
        class Inner {
            private void fun() {
                System.out.println(out_a);
                System.out.println(STATIC_b);
                System.out.println(inner_c);
            }
        }
        Inner inner = new Inner();
        inner.fun();
    }

    public static void testStaticFunctionClass() {
        int d = 3;
        class Inner {
            private void fun() {
                // System.out.println(out_a); 编译错误，定义在静态方法中的局部类不可以访问外 部类的实例变量
                System.out.println(STATIC_b);
                System.out.println(d);
            }
        }
        Inner inner = new Inner();
        inner.fun();
    }
}
```
定义在实例方法中的局部类可以访问外部类的所有变量和方法，定义在静态方法 中的局部类只能访问外部类的静态变量和方法。局部内部类的创建方式，在对应方法内，new 内部类()，如下：
```java
public static void testStaticFunctionClass(){
  class Inner {
  }
  Inner inner = new Inner();
}

```

- **静态内部类**

和成员内部类类似，但是使用static修饰
```java
public class Outer {

    private static int radius = 1;

    static class StaticInner {
        public void visit() {
            System.out.println("visit outer static variable:" + radius);
        }
    }
}
```
静态内部类可以访问外部类所有的静态变量，而不可访问外部类的非静态变量； 静态内部类的创建方式，new 外部类.静态内部类()，如下：
```java
Outer.StaticInner inner = new Outer.StaticInner();
inner.visit
```
- **匿名内部类**

匿名内部类就是没有名字的内部类,它不需要在外部类中明确地定义这个类，而是在方法内部，通过匿名类（Anonymous Class）来定义
```java
public class Outer {
    private String name;

    Outer(String name) {
        this.name = name;
    }

    void asyncHello() {
        Runnable r = new Runnable() {
            @Override
            public void run() {
                System.out.println("Hello, " + Outer.this.name);
            }
        };
        new Thread(r).start();
    }
}

public class Main {
    public static void main(String[] args) {
        Outer outer = new Outer("Nested");
        outer.asyncHello();
    }
}
```
观察asyncHello()方法，我们在方法内部实例化了一个Runnable。Runnable本身是接口，接口是不能实例化的，所以这里实际上是定义了一个实现了Runnable接口的匿名类，并且通过new实例化该匿名类，然后转型为Runnable。在定义匿名类的时候就必须实例化它，定义匿名类的写法如下：
```java
Runnable r = new Runnable() {
    // 实现必要的抽象方法...
};
```
匿名内部类不能定义任何静态成员和静态方法。匿名类和成员内部类一样，可以访问外部类的所有字段和方法。之所以我们要定义匿名类，是因为在这里我们通常不关心类名，比直接定义内部类可以少写很多代码。

观察Java编译器编译后的.class文件可以发现，Outer类被编译为Outer.class，而匿名类被编译为Outer$1.class。如果有多个匿名类，Java编译器会将每个匿名类依次命名为Outer$1、Outer$2、Outer$3……

### 内部类的优点？有哪些应用场景？

**优点：**
1. 一个内部类对象可以访问创建它的外部类对象的内容，包括私有数据。
2. 内部类不为同一包的其他类所见，具有很好的封装性。
3. 内部类有效实现了“多重继承”，优化 java 单继承的缺陷。
4. 匿名内部类可以很方便的定义回调。

**应用场景：**
1. 一些多算法场合
2. 解决一些非面向对象的语句块。
3. 适当使用内部类，使得代码更加灵活和富有扩展性。
4. 当某个类除了它的外部类，不再被其他的类使用时。

### Java中类的初始化顺序？

**一个类中包含如下的东西：**
1. 静态变量：staic开头定义的属性
2. 静态语句块：static{}圈起来的方法块
3. 实例变量：未带有static定义的属性
4. 普通语句块：{}圈起来的方法块
5. 构造函数：跟类名相同的方法
6. 方法：普通的方法

**普通类的情况下，初始化顺序为：**
- 静态变量
- 静态语句块
- 实例变量
- 普通语句块
- 构造函数

**存在继承的情况下，初始化顺序为：**
- 父类（静态变量、静态语句块）
- 子类（静态变量、静态语句块）
- 父类（实例变量、普通语句块）
- 父类（构造函数）
- 子类（实例变量、普通语句块）
- 子类（构造函数）

**包含接口、抽象类情况下（普通类继承抽象类并实现接口），初始化顺序为：**
- 接口静态变量
- 抽象类静态变量
- 抽象类静态语句块
- 实现类（普通类）静态属性
- 实现类静态语句块
- 抽象类实例变量
- 抽象类普通语句块
- 抽象类构造方法
- 实现类实例变量
- 实现类普通语句块
- 实现类构造函数

### 什么是 classpath 和 jar ？

- **classpath：**

    Java 程序编译后生成 .class文件 ，时 JVM 可直接执行的字节码文件。`classpath` 就是告诉 JVM 去哪里查找要执行的 class 文件，时一组路径的集合，windows 上用` ; `分隔，linux上用 `:` 分隔，如：
    ```bash
    win:
    C:\work\project1\bin;C:\shared;"D:\My Documents\project1\bin"
    linux:
    /usr/shared:/usr/local/bin:/home/liaoxuefeng/bin
    ```
    classpath环境变量不是必须设置的，有两种方法可以告诉JVM class文件的地址：
    1. 可以通过设置环境变量的方法告诉JVM那里查找class文件，但是这种方式是不推荐的，这样会污染系统环境
    2. 在启动JVM时，通过`-classpath`和`-cp`参数启动。例如：java -cp D:\Java\test Test (在D:\Java\test目录下查找Test.class并运行)。如果既没有设置classpath，也没有使用`-classpath`和`-p`参数，则JVM只会在当前目录查找.class文件

- **jar**

    它可以把 `package` 组织的目录层级，以及各个目录下的所有文件（包括.class文件和其他文件）都打成一个 `jar` 文件。`jar` 包实际上就是一个 zip 格式的压缩文件，而 `jar` 包相当于目录。

    因为 `jar` 包的本质是一个目录结构，如果我们要执行一个 `jar` 包的 class，就可以把 `jar` 包放到 `classpath` 中：
    ```bash
    java -cp ./test.jar test.Test
    ```
    `jar` 包还可以包含一个特殊的 `/META-INF/MANIFEST.MF` 文件，`MANIFEST.MF` 是纯文本，可以指定 `Main-Class` 和其它信息。 JVM 会自动读取这个 `MANIFEST.MF` 文件，如果存在 `Main-Class`，我们就不必在命令行指定启动的类名，而是用更方便的命令：
    ```bash
    java -jar test.jar
    ```
    `jar` 包还可以包含其它 `jar` 包，这个时候，就需要在 `MANIFEST.MF` 文件里配置 `classpath` 了。

    创建 `jar` 包的方式：

    1. 直接在资源管理器中，找到正确的目录，点击右键，在弹出的快捷菜单中选择“发送到”，“压缩(zipped)文件夹”，就制作了一个zip文件。然后，把后缀从.zip改为.jar，一个jar包就创建成功。
    2. 使用工具创建jar包。例如Maven，可以非常方便地创建jar包。

### 成员变量与局部变量的区别？

变量：在程序执行的过程中，在某个范围内其值可以发生改变的量。从本质上讲，变量其实是内存中的一小块区域 

成员变量：方法外部，类内部定义的变量。

局部变量：类的方法中的变量。 

成员变量和局部变量的区别：

- **作用域**

    成员变量：针对整个类有效。

    局部变量：只在某个范围内有效。(一般指的就是方法,语句体内)

- **存储位置**

    成员变量：随着对象的创建而存在，随着对象的消失而消失，存储在堆内存中。

    局部变量：在方法被调用，或者语句被执行的时候存在，存储在栈内存中。当方法调用完，或者语句结束后，就自动释放。

- **生命周期**

    成员变量：随着对象的创建而存在，随着对象的消失而消失。

    局部变量：当方法调用完，或者语句结束后，就自动释放。

- **初始值**

    成员变量：有默认初始值。

    局部变量：没有默认初始值，使用前必须赋值。

- **使用原则**

    在使用变量时需要遵循的原则为：就近原则首先在局部范围找，有就使用；接着在成员位置找。


### 实例变量和静态变量是什么？他们有什么区别？

**实例变量和静态变量都属于成员变量**

**实例变量：** 定义在类中但在任何方法之外，当一个对象被实例化之后，每个实例变量的值就跟着确定，它与该实例同生共死

**静态变量：** 又称为类变量，也就是说这个变量属于类的，类所有的实例都共享静态变量，可以直接通过类名进行访问。静态变量在内存中只存在一份，在类加载的时候分配

**区别：**
1. 存放位置：静态变量随着类的加载存在于方法区中，实例变量随着对象的建立存在于堆内存
2. 生命周期：静态变量的生命周期最长随着类的加载而加载消失而消失，实例变量与实例同生共死

### 静态方法为什么不能调用非静态成员？

这个需要结合 JVM 的相关知识，主要原因如下：

1. 静态方法属于类的，在类加载的时候就会分配内存，可以通过类名直接访问。而非静态成员属于实例对象，只有在对象实例化之后才会存在，需要通过类的实例对象去访问
2. 在类的非静态成员不存在的时候静态成员就已经存在了，此时调用在内存中还不存在的非静态成员，属于非法操作 

### static关键字的使用方式有哪些？

1. **静态变量：** static 修饰的变量
2. **静态方法：** 静态方法在类加载的时候就存在了，它不依赖于任何实例。所以静态方法必须有实现，不能是抽象方法。方法中不能有 this 和 super 关键字，因为这两个关键字与具体对象关联
3. **静态语句块：** 在类初始化的时候运行一次
4. **静态内部类：** 非静态内部类依赖于外部类的实例，也就是说需要先创建外部类实例，才能用这个实例去创建非静态内部类，而静态内部类是不需要的。静态内部类不能访问外部类的非静态的变量和方法
5. **静态导包：** 使用 import static 用来导入某个类或者包中的静态方法或者静态变量

### final的作用？

**1. 修饰类**

声明类不可以被继承

**2. 修饰方法**

声明方法不能被子类重写

用private修饰的方法隐式地被指定final，如果子类中定义的方法和父类中一个用private修饰的方法的方法名相同，此时子类的方法不是重写父类的方法，而是在子类中重新定义了一个方法

**3. 修饰数据**

声明数据为常量，可以是编译时常量，也可以是运行时被初始化后不能被改变的常量

```java
public class TestFinal{
  final int f1 = 1; // 编译时常量
  final int f2 ; // 运行时初始化常量
  public TestFinal(){
      this.f2 = 1;
  }
}
```
- 对于基本类型，final使数值不变

- 对于引用类型，final使引用不变，也就不能引用其他对象，但是被引用的对象本身是可以修改的
```java
final int x = 1;
// x = 2;  // cannot assign value to final variable 'x'
final A y = new A();
y.a = 1; // 修改被引用对象本身的属性
```
注意：final修饰的方法参数也同样时遵循以上两点

### 什么是枚举类？

枚举类型它是一种特殊的数据类型，之所以特殊是因为它既是一种 类(class) 类型却又比 类（class） 类型多了些特殊的约束，但是这些约束的存在也造就了枚举类型的简洁性、安全性以及便捷性。

枚举`enum`的全称为 enumeration， 是 JDK5 中引入的特性。

枚举的特性，归结起来就是一句话：除了不能继承，基本上可以将 enum 看做一个常规的类。

在 Java 中，被 enum 关键字修饰的类型就是枚举类型。形式如下：

```java
enum ColorEn { RED, GREEN, BLUE }
```
**枚举的好处：** 可以将常量组织起来，统一进行管理。

**枚举的典型应用场景：** 错误码、状态等。

本质：所有Java语言枚举类型的公共基类`java.lang.Enum`
```java
public abstract class Enum<E extends Enum<E>>
        implements Comparable<E>, Serializable { ... }
```
新建一个`TestEnum.java`文件，内容如下：
```java
public enum TestEnum{
	RED,YELLOW,BLUE
}
```
执行`javac TestEnum.java`命令，生成`TestEnum.class`文件。

![](https://resource.lzyan.fun/PigGo/20211213135115.png)

用`javap TestEnum.class`命令，反解析出当前类对应的汇编指令
```java
Compiled from "TestEnum.java"
public final class TestEnum extends java.lang.Enum<TestEnum> {
  public static final TestEnum RED;
  public static final TestEnum YELLOW;
  public static final TestEnum BLUE;
  public static TestEnum[] values();
  public static TestEnum valueOf(java.lang.String);
  static {};
}
```
由此可以看出枚举的本质就是`java.lang.Enum`的子类。

尽管 `enum` 看起来像是一种新的数据类型，事实上，`enum` 是一种受限制的类，并且具有自己的方法。枚举这种特殊的类因为被修饰为 `final`，所以不能继承其他类。

定义的枚举值，会被默认修饰为 `public static final` ，从修饰关键字，即可看出枚举值本质上是静态常量。

枚举的更多应用：[深入理解Java枚举](https://dunwu.github.io/javacore/basics/java-enum.html#_1-%E7%AE%80%E4%BB%8B)

### Object类是什么？

`Object` 类是一个特殊的类，是所有类的父类。java 中如果一个类没有用 extends 关键字 明确指出继承于某个类，那么它默认继承 `Object` 类。

`Object` 类的通用方法：
```java
public native int hashCode()

public boolean equals(Object obj)

protected native Object clone() throws CloneNotSupportedException

public String toString()

public final native Class<?> getClass()

protected void finalize() throws Throwable {}

public final native void notify()

public final native void notifyAll()

public final native void wait(long timeout) throws InterruptedException

public final void wait(long timeout, int nanos) throws InterruptedException

public final void wait() throws InterruptedException
```

### == 和 equals() 的区别是什么？

**== 对于基本类型和引用类型的作用效果是不同的：**

- 对于基本数据类型来说，==比较的是值
- 对于引用数据类型来说，==比较的是对象的内存地址

**equals() 作用不能用于判断基本数据类型的变量，只能用来判断两个对象是否相等。它存在于Object类中，
equals() 方法存在两种使用情况：**

- 没有覆盖 equals() 方法：通过 equals() 比较该类的对象时，等价于通过“==”比较这两个对象，使用的默认是 Object 类 equals() 方法
- 类覆盖了 equals() 方法：一般我们都覆盖 equals() 方法来比较两个对象中的属性是否相等；若它们的属性相等，则返回 true（即认为这两个对象相等）

**关于String类中的equals()方法：**

- String 类中的 equals() 方法是被重写过的，因为 Object 的 equals() 方法比较的是对象的内存地址，而 String 的 equals() 方法比较的是对象的值
- 当创建 String 类型对象时，虚拟机会在常量池中查找有没有已经存在的值和要创建的值相同的对象，如果有就把它赋给当前引用。如果没有就在常量池中重新创建一个 String 对象

```java
String a = new String("ab"); //a为一个引用
String b = new String("ab"); //b为另一个引用，对象内容一样
String aa = "ab"; //放在常量池
String bb = "ab"; //从常量池中查找
System.out.println(aa==bb); //true
System.out.println(a==b); //false
System.out.println(a.equals(b)); //true 
System.out.println(42 == 42.0);// true
```

### hashCode() 方法的作用？

作用：

`hashCode()` 方法的作用是 `获取哈希码` ，也称散列码；它实际上是返回一个 int 整数。**这个哈希码是用来确定该对象在哈希表中的索引位置。**

`hashCode()` 方法定义在 JDK 的 Object 类中，这就意味着 Java 中的任何类都包含 `hashCode()` 函数。另外需要注意的是： Object 的 `hashCode()` 方法是**本地方法**，也就是用 C 语言或 C++ 实现的，该方法通常用来将对象的内存地址转换为整数之后返回。

虽然，每个 Java 类都包含 `hashCode()` 函数，但是仅仅当创建某个“类的散列表”时，该类的 hashCode() 才有用（作用是：确定该类的每一个对象在散列表中的位置），其他情况下，例如创建类的单个对象或者创建类的对象数组，类的 hashCode() 没有作用

上面的散列表指的是：Java 集合中本质是散列表的类，如 HashMap ，Hashtable ，HashSet。

**哈希码的作用：**

- 散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码。
- 散列表的本质是通过数组实现的。当我们要获取散列表中的某个“值”时，实际上是要获取数组中的某个位置的元素。而数组的位置，就是通过“键”来获取的；更进一步说，数组的位置，是通过“键”对应的散列码计算得到的。

**以 HashSet 为例，深入理解 hashCode() 的作用：**

  假设，HashSet 中已经有 1000 个元素。当插入第 1001 个元素时，需要怎么处理？因为 HashSet 是 Set 集合，它允许有重复元素。

  将第 1001 个元素逐个的和前面 1000 个元素进行比较？显然，这个效率是相等低下的。散列表很好的解决了这个问题，它根据元素的散列码计算出元素在散列表中的位置，然后将元素插入该位置即可。对于相同的元素，自然是只保存了一个。

  由此可知，若两个元素相等，它们的散列码一定相等；但反过来却不一定。

  也就是说，在散列表中：

  1. 如果两个对象相等，那么它们的 hashCode() 值一定要相同；
  2. 如果两个对象 hashCode() 相等，它们并不一定相等。这是在散列表中的情况，在非散列表中比一定如此。

### hashCode() 和 equals() 的关系?

重写 `equals()` 方法时一般需要重写 `hashCode()` 方法，这两个方法是用来协同判断两个对象是否相等的，采用这种方式的原因是可以提高程序插入和查询的速度，`equals()` 方法默认比较是采用 `hashCode()`值比较。

当重写 `equals()` 方法时，说明我们想要更改判断对象是否相等的方式，因此我们需要同时重写 `hashCode()`，才能保证对象判断方式是我们想要的效果。

如果在重写 `equals()` 方法时，不重写 `hashCode()` 方法，就会导致在某些场景下，例如将两个相等的自定义对象存储在 Set 集合时，就会出现程序执行的异常，为了保证程序的正常执行，所以我们就需要在重写 `equals()` 时，也一并重写 `hashCode()` 才行。

### 关于Object类中的toString()方法？

Object 类的 toString() 方法默认返回该对象实现类的 `类名+@+hashcode` 值，而这个返回值不能实现自我描述功能，所以可以重写 toString() 方法来实现描述。至于要不要进行重写需要根据需要来定。

### 深拷贝、浅拷贝是什么？

- **浅拷贝**

浅拷贝并不是真的拷贝，只是 **复制指向某个对象的指针** ，而不复制对象本身，新旧对象还是共享同一块内存。

- **深拷贝**

会另外 **创造一个一模一样的对象** ，新对象跟原对象不共享内存，修改新对象不会改到原对象。


### 什么是包装类？为什么需要包装类？

Java 中的 8 个基本数据类型，分别对应了 8 个包装类

为什么使用：
- 基本数据类型方便、简单、高效、但泛型不支持、集合元素不支持
- 不符合面向对象思维
- 包装类提供很多方法，方便使用如 Integer 类 toHexString(int i) 、 parsenInt(String s) 方法等

### Java基本类型的包装类常量池技术是什么？

Java 基本类型的包装类大部分都实现了**常量池技术**，**常量池是为了避免频繁的创建和销毁对象而影响系统性能，其实现了对象的共享。**

```java
Byte,Short,Integer,Long,Character,Boolean；
```

前面 4 种包装类默认创建了数值 `[-128，127]` 的相应类型的缓存数据， `Character` 创建了数值在 `[0,127]` 范围的缓存数据，`Boolean` 直接返回 `True Or False` 。

如果超出对应范围仍然会去创建新的对象，缓存的范围区间的大小只是在性能和资源之间的权衡。至于为什么是在 `-128~127` 之间，可查阅源码 `Integer` 类中有一个静态内部类 `IntegerCache`，在 `IntegerCache` 类中有一个 `Integer` 数组，用以缓存当前数值范围 `-128~127` 时的 `Integer` 对象。

其中两种浮点数类型的包装类 `Float` , `Double` 并没有实现常量池技术。

```java
Integer i1 = 66;
Integer i2 = 66;
System.out.println(i1 == i2);//true 在范围内比较的是直接缓存的数据

Integer i3 = 128;
Integer i4 = 128;
System.out.println(i3 == i4);//false 在-128~127范围外，比较的是对象

Float f1 = 222f;
Float f2 = 222f;
System.out.println(f1 == f2);//false

Double d1 = 2.2;
Double d2 = 2.2;
System.out.println(d1 == d2);//false
```
需要注意一点：这是阿里Java开发手册中的一条规定

![](https://resource.lzyan.fun/PigGo/20211206223304.png)

### 什么是自动装箱拆箱？

例如：
```java
Integer x = 2;     // 装箱 调用了 Integer.valueOf(2)
int y = x;         // 拆箱 调用了 X.intValue()
```
简单来说：**装箱就是自动将基本数据类型转换为包装类类型，拆箱就是自动将包装器类型转换为基本数据类型。**

装箱拆箱这个过程是自动执行的，所以我们需要通过反编译上面代码编译后的 class 文件得到对应的字节码来看它的执行过程：

![](https://resource.lzyan.fun/PigGo/2021-12-06_230145.png)

从字节码中可以发现，装箱其实就是调用了包装类的 `ValueOf()` 方法，拆箱就是调用了 `intValue()` 方法。

因此:
- `Integer i = 10` 等价于 `Integer i = Integer.valueOf(10)`
-` int n = i` 等价于 `int n = i.intValue()`;

注意：装箱和拆箱过程需要进行大量的计算，装箱和拆箱操作会从速度和内存两个方面损伤应用程序的性能，因此应该清楚编译器会在何时自动产生执行这些操作的指令并尽量避免不必要的装拆箱操作。

### new Integer(123) 与 Integer.valueOf(123) 的区别？

- `new Integer(123)`  每次都会新建一个对象；
- `Integer.valueOf(123) ` 会使用缓存池中的对象，多次调用会取得同一个对象的引用。

```java
Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y);    // false
Integer z = Integer.valueOf(123);
Integer k = Integer.valueOf(123);
System.out.println(z == k);   // true
```

### String类是用什么数据结构来存储字符串的？String类可以被继承吗？

`String` 类是用数组的数据结构来存储字符串的，`String` 类是被 final 修饰符修饰的，而被 final 修饰的类是不能被继承的

### String为什么是不可变的？不可变的好处？

**不可变的原因：**

- 保存字符串的数组被 final 修饰且私有的，并且 String 类没有 提供/暴露 修改这个字符串的方法
- String类被final修饰导致其不能被继承，进而避免了子类破坏 String 不可变
- String不可变的关键在于底层的实现，而不是一个 final

**不可变的好处:**

**1. 可以缓存 hash 值**

因为 String 的 hash 值经常被使用，例如 String 用作 HashMap 的 key，不可变的特性可以使得 hash 值也不可变，因此只需要进行一次计算

**2. String pool 的需要**

如果一个 String 对象已经被创建过了，那么就会从 String pool 中取得引用。只有 String 是不可变的，才可能使用 String pool

**3. 安全性**

String 经常作为参数， String 的不可变性可以保证参数不可变。例如在作为网络连接参数的情况下如果 String 是可变的，那么在网络连接的过程中 String 被改变，改变的一方以为现在连接的是其他的主机而实际情况却不一定是

**4. 线程安全**

String 中的对象是不可变的，也就可以理解为常量，线程安全

### String、StringBuffer和StringBuilder的区别，以及它们的使用场景？

**1. 可变性**

String 不可变

StringBuffer 和 StringBuilder 可变

**2. 线程安全**

String 不可变，因此是 `线程安全` 的

StringBuilder `不是线程安全`的

StringBuffer 是 `线程安全` ，内部使用 synchronized 进行同步

**3. 性能**

每次对 String 类型进行改变的时候，都会生成一个新的 String 对象，然后将指针指向新的 String 对象，**StringBuffer和StringBuilder类的对象能够被多次修改，并且不产生新的未使用的对象。**

**使用场景：**

- 如果操作少量的数据用 String
- 多线程操作字符串时缓冲区下操作大量数据，使用 StringBuffer
- 单线程操作字符串时缓冲区下操作大量数据，使用 StringBuilder （相比较于 StringBuffer 有速度优势）

### instanceof关键字的作用？

`instanceof` 是Java中的一个**双目运算符**，用作判断一个引用类型变量所指向的对象是否是某个类（接口、抽象类、父类）的实例，返回 boolean 类型的数据。
```java
boolean result = obj instanceof Class
```
`obj` 为一个对象的引用，`Class` 则表示一个类或者接口，当 `obj` 为这个 `Class` 的对象实例，或者是其直接或间接子类，再或者是其接口的实现类， result 的结果都是 true ，否则 false。
```java
// obj 必须为引用类型，不能是基本类型
int i = 1;
i instanceof Object //编译不通过

// 在JavaSE规范中对instanceof运算符的规定是：如果obj为null，那将返回false
null instanceof Object // false

// obj为Class类的实例对象
Integer i = new Integer(1);
i instanceof Integer // true

// obj为Class接口的实现类
ArrayList arrayList = new ArrayList();
arrayList instanceof List // true
//or
List list = new ArrayList();
list instanceof ArrayList //true

// obj为Class类的直接或间接子类
public class Animal{
}
public class Dog extends Animal{
}
//测试
Animal a1 = new Animal();
Animal a2 = new Dog();
Dog dog = new Dog();
a1 instanceof Dog // false
a2 instanceof Dog // true
dog instanceof Dog // true
```
实现原理理解：
```java
// 伪代码
boolean result;
if (obj ==null){
    return false;
} else {
    try{
        T temp = (T) obj;
        result = true;
    } catch (ClassCastException e){
        result = false;
    }
}
```
1. `obj==null`，返回false。
2. 如果`obj`强制转换为T时发生编译错误，返回false。
3. 运行时，如果 `T != null`，并且`(T)obj` 不引发`ClassCastException`，返回true。
4. 如果 `obj != null` 并且` (T) obj `不引发` ClassCastException `，返回 true ，否则值为 false 。

总结：编译器会检查 `obj` 是否能转换成`T`类型(右边Class类型)，如果不能转换则直接报错，如果不能确定类型，则通过编译，具体看运行时定。

### Java语法糖有哪些?

**语法糖**（Syntactic Sugar），也称糖衣语法，是英国科学家发明的一个术语，指在计算机语言中添加的某种语法，这种语法对语言本身的功能来说没有什么影响，只是为了方便程序员进行开发，提高开发效率，使用这种语法写出来的程序可读性也更高。

**说白了，语法糖就是对现有语法的一个再封装。**

Java虚拟机是并不支持语法糖的，语法糖在程序编译阶段就会被还原成简单的基础语法结构，这个过程就是解语法糖。**所以在Java中真正支持语法糖的是Java编译器。**

**而Java中的语法糖有很多，主要有以下常用的语法糖：**
- switch-case对String和枚举类的支持
- 泛型
- 包装类自动装箱与拆箱
- 方法变长参数
- 枚举
- 内部类
- 条件编译
- 断言
- 数值字面量
- 增强for循环
- try-with-resource语法
- Lambda表达式
- 字符串+号语法

### 什么是变长参数?
从 `Java5` 开始，Java 支持定义可变长参数，所谓可变长参数就是允许在调用方法时传入不定长度的参数。变长参数是 Java 的一个 `语法糖` ，一个方法只能有一个可变长参数，且这个可变长参数必须是该方法的最后一个参数，java 不允许存在一个方法具备多个变长参数或者变长参数不是方法的最后位置的情况。

**变长参数本质上还是基于数组实现的：**
```java
public class VariableLengthArgument {

    public static void printVariable(String... args) {
        String[] var1 = args;
        int var2 = args.length;

        for(int var3 = 0; var3 < var2; ++var3) {
            String s = var1[var3];
            System.out.println(s);
        }

    }
    // ......
}
```
**当方法重载的情况会优先匹配固定参数的方法：**
```java
public class VariableLengthArgument {

    public static void printVariable(String... args) {
        for (String s : args) {
            System.out.println(s);
        }
    }

    public static void printVariable(String arg1, String arg2) {
        System.out.println(arg1 + arg2);
    }

    public static void main(String[] args) {
        printVariable("a", "b");
        printVariable("a", "b", "c", "d");
    }
}
//输出
ab
a
b
c
d
```

### 下面变长参数的实现函数有什么问题？
```java
class Base {
    void print(String... args) {
        System.out.println("Base print.");
    }
}
class Sub extends Base {
    @Override
    void print(String[] args) {
        System.out.println("SUb print.");
    }
}
Base base = new Sub();
base.print("hello");//1
Sub sub = new Sub();
sub.print("hello");//2
```
`注释 1`  能编译通过且打印为 Sub print.，因为 base 对象把子类对象 sub 做了向上转型，形参列表是由父类决定的，当然能通过。

`注释 2`  编译报错为传递的参数 String 类型与方法需要的 String[] 类型不匹配，因为这时编译器看到子类覆写了父类的 print 方法，所以会使用子类重新定义的 print 方法，尽管参数列表不匹配也不会再去父类匹配（因为找到了就不再找了），故有了类型不匹配的编译错误。


## 异常处理

### Java异常类的层次结构？

Java 标准库内置了一些通用的异常类，所有的异常都有一个共同的祖先 `java.lang` 包中的 `Throwable` 类。`Throwable` 类有两个重要的子类 `Exception（异常）` 和 `Error（错误）`。`Exception` 能被程序本身处理(try-catch)， Error 是无法处理的(只能尽量避免)。

![](https://resource.lzyan.fun/PigGo/20211211114318.png)

**Throwable 可以用来表示任何可以作为异常抛出的类，Throwable又派生出Error类和Exception类。**

**Exception：** 程序本身可以处理的异常，可以通过 catch 来进行捕获。Exception 又可以分为 受检查异常(必须处理) 和 不受检查异常(可以不处理)。

**Error：** Error 类以及它的子类实例，用来表示JVM无法处理的错误，我们没办法通过 catch 来进行捕获，例如，Java 虚拟机运行错误（Virtual MachineError）、虚拟机内存不够错误(OutOfMemoryError)、类定义错误（NoClassDefFoundError）等 。这些异常发生时，Java 虚拟机（JVM）一般会选择线程终止。因此，程序员应该关注Exception为父类的分支下的各种异常

- **受检查异常**

在正确的程序运行过程中，很容易出现的、情理可容的异常状况，在一定程度上这种异常的发生是可以预测的，并且一旦发生该种异常，就必须采取某种方式进行处理。除了RuntimeException及其子类以外，其他的Exception类及其子类都属于检查异常，当程序中可能出现这类异常，要么使用try-catch语句进行捕获，要么用throws子句抛出，否则编译无法通过。

- **不受检查异常**

Java 代码在编译过程中 ，我们即使不处理不受检查异常也可以正常通过编译。RuntimeException 及其子类都统称为非受检查异常，例如：NullPointerException、NumberFormatException（字符串转换为数字）、ArrayIndexOutOfBoundsException（数组越界）、ClassCastException（类型转换错误）、ArithmeticException（算术错误）等。

**区别：不受检查异常为编译器不要求强制处理的异常，受检查异常则是编译器要求必须处置的异常。**

### Java中异常处理的关键字？

Java异常处理涉及到五个关键字，分别是：try、catch、finally、throw、throws。

- `try`  用于监听。将要被监听的代码(可能抛出异常的代码)放在try语句块之内，当try语句块内发生异常时，异常就被抛出。
- `catch`  用于捕获异常。catch 用来捕获 try 语句块中发生的异常。
- `finally`   无论是否捕获或处理异常，finally 块里的语句都会被执行。它主要用于回收在try块里打开的物力资源(如数据库连接、网络连接和磁盘文件)。只有finally块，执行完成之后，才会回来执行try块或catch块遇到return或者throw语句。当 try 语句和 finally 语句中都有 return 语句时，在方法返回之前，finally 语句的内容将被执行，并且 finally 语句的返回值将会覆盖原始的返回值。
- `throw`  用于抛出异常。
- `throws` 用在方法签名中，用于声明该方法可能抛出的异常。如果一个方法可以导致一个异常但不处理它，它必须指定这种行为以使方法的调用者可以保护它们自己而不发生异常。

### Java中常见的异常有哪些？

- `NullPointerException` 空指针异常
- `ArrayIndexOutOfBoundsException` 索引越界异常
- `InputFormatException` 输入类型不匹配
- `SQLException` SQL异常
- `IllegalArgumentException` 非法参数异常
- `NumberFormatException` 类型转换异常
- ···

### 什么是自定义异常、自定义异常怎么使用？

使用Java内置的异常类可以描述在编程时出现的大部分异常情况。除此之外，用户还可以自定义异常，用户自定义异常需要从一个适合的`Exception`派生

**自定义异常的使用大体分为以下步骤：**

1. 创建自定义异常类，继承合适的 Exception
2. 在方法中通过 throw 关键字抛出异常对象。
3. 如果在当前抛出异常的方法中处理异常，可以使用 try-catch 语句捕获并处理；否则在方法的声明处通过 throws 关键字指明要抛出给方法调用者的异常，继续进行下一步操作。
4. 在出现异常方法的调用者中捕获并处理异常

**栗子**

```java
public class MyException extends Exception {
    private int detail;
    MyException(int a){
        detail = a;
    }
    public String toString(){
        return "MyException ["+ detail + "]";
    }
}
public class TestMyException{
    public static void compute(int a) throws MyException{
        System.out.println("Called compute(" + a + ")");
        if(a > 10){
            throw new MyException(a);
        }
        System.out.println("Normal exit!");
    }
    public static void main(String [] args){
        try{
            compute(1);
            compute(20);
        }catch(MyException me){
            System.out.println("Caught " + me);
        }
    }
}

// 输出
Called compute(1)

Normal exit!

Called compute(20)

Caught MyException [20]
```

### 为什么推荐使用try-with-resources代替try-finally?

《Effecitve Java》 一书中明确指出:

> 面对必须要关闭的资源，我们总是应该优先使用 try-with-resources 而不是try-finally。随之产生的代码更简短，更清晰，产生的异常对我们也更有用。try-with-resources语句让我们更容易编写必须要关闭的资源的代码，若采用try-finally则几乎做不到这点。

`finally`关闭资源不会立即关闭，取决与网络和系统，所以，最好不要使用它作为业务流程的控制。

`try-with-resources`的使用是Java 7增加的新特性，只要实现了`java.lang.AutoCloseable` 或 `java.io.Closeable`的对象，在try代码块结束之前，会自动关闭资源。其中字节输入流（InputStream），字节输出流（OutputStream），字符输入流（Reader），字符输出流（Writer）,Scanner 等均实现了这接口。

用`try-catch-finally`语句来实现的方式：
```java
//读取文本文件的内容
Scanner scanner = null;
try {
    scanner = new Scanner(new File("D://read.txt"));
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
} finally {
    if (scanner != null) {
        scanner.close();
    }
}
```
使用 Java 7 之后的 `try-with-resources` 语句改造上面的代码:
```java
try (Scanner scanner = new Scanner(new File("test.txt"))) {
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException fnfe) {
    fnfe.printStackTrace();
}
```
当多个资源需要关闭的时候，通过使用分号 `;` 来进行分隔:
```java
try (
    BufferedInputStream bin = new BufferedInputStream(new FileInputStream(new File("test.txt")));
    BufferedOutputStream bout = new BufferedOutputStream(new FileOutputStream(new File("out.txt")))
    ) 
    {
        int b;
        while ((b = bin.read()) != -1) {
            bout.write(b);
        }
    }
    catch (IOException e) {
    e.printStackTrace();
}
```

## 反射

### 什么是反射？

**通过反射，我们可以在运行时获得程序或程序集中每一个类型的成员和成员的信息。**

程序中一般的对象的类型都是在编译期就确定下来的，而 Java 反射机制可以动态地创建对象并调用其属性，这样的对象的类型在编译期是未知的。所以我们可以通过反射机制直接创建对象，即使这个对象的类型在编译期是未知的。

**反射的核心是 JVM 在运行时才动态加载类或调用方法/访问属性，它不需要事先（写代码的时候或编译期）知道运行对象是谁。**

**Java 反射主要提供以下功能：**
- 在运行时判断任意一个对象所属的类。
- 在运行时构造任意一个类的对象。
- 在运行时判断任意一个类所具有的成员变量和方法（通过反射甚至可以调用 private 方法）。
- 在运行时调用任意一个对象的方法。

**反射机制优缺点：**

- 优点：可以让我们的代码更加灵活、为各种框架提供开箱即用的功能提供了便利。
- 缺点：让我们在运行时有了分析操作类的能力，这同样也增加了安全问题。比如可以无视泛型参数的安全检查（泛型参数的安全检查发生在编译时）。另外，反射的性能也要稍差点，不过，对于框架来说实际是影响不大的。

理解反射重点就在于理解什么是运行时，为什么我们要在 **运行时获取** 类的信息。

**通过一个简单的例子说明：**

正常情况下，如果我们需要调用一个对象的方法，或者访问一个对象的字段，通常会传入对象的实例：
```java
// Main.java
import com.lzyan.Person;

public class Main{
    String getFullName(Person p){
        return p.getFirstName()+" "+p.getLastName();
    }
}
```
但是，如果不能获得`Person`类，只有一个 Object 实例，比如这样:
```java
String getFullName(Object obj){
    return ???
}
```
怎么办？有童鞋会说：强制转型啊！
```java
String getFullName(Object obj){
    Person p = (Person) obj;
    return p.getFirstName()+" "+p.getLastName();
}
```
强制转换类型的时候，你会发现一个问题：编译上面代码，仍然需要引用 `Person` 类。**所以，反射是为了解决在运行期，对某个实例一无所知的情况下，如何调用其方法。**

### 反射机制相关的类有哪些？有什么作用？

反射其实就是围绕`Class`对象和`java.lang.reflect`类库学习，学习它的各种API：

| 类名          | 用途                                             |
| ------------- | ------------------------------------------------ |
| Class类       | 代表类的实体，在运行的Java应用程序中表示类和接口 |
| Field类       | 代表类的成员变量（成员变量也称为类的属性）       |
| Method类      | 代表类的方法                                     |
| Constructor类 | 代表类的构造方法                                 |

- **Class类**

    `class`（包括`interface`）的本质是数据类型（`Type`）。无继承关系的数据类型无法赋值：
    ```java
    Number n = new Double(123.456); // OK
    String s = new Double(123.456); // compile error!
    ```
    而`class`是由JVM在执行过程中动态加载的。JVM在第一次读取到一种`class`类型时，将其加载进内存。每种`class`，JVM就为其创建一个`Class`类型的实例，并且关联起来。

    以`String`类为例，当JVM加载`String`类的时，它首先读取`String.class`文件到内存，然后为`String`类创建一个`Class`实例并关联起来：
    ```java
    Class cls = new Class(String);
    ```
    这个`Class`实例是`JVM`内部创建的，如果我们查看JDK源码，可以发现`Class`类的构造方法是`private`，只有`JVM`能创建`Class`实例，我们自己的Java程序是无法创建`Class`实例的。

    ![](https://resource.lzyan.fun/PigGo/20211212110553.png)

    所以JVM持有的每个`class`实例都指向一个数据类型（`class`或者`interface`）

    ![](https://resource.lzyan.fun/PigGo/20211212110849.png)

    一个`Class`实例包含了该`class`的所有完整信息：

    ![](https://resource.lzyan.fun/PigGo/20211212110924.png)

    由于JVM为每个加载的`class`创建了对应的`Class`实例，并在实例中保存了该`class`的所有信息，包括类名、包名、父类、实现的接口、所有方法、字段等，因此，如果获取了某个`Class`实例，我们就可以通过这个`Class`实例获取到该实例对应的`class`的所有信息。

    如何获取一个`class`的`Class`实例？有三个方法：
    ```java
    1. 直接通过一个class的静态变量class获取：
    Class cls = String.class;

    2. 如果我们有一个实例变量，可以通过该实例变量提供的getClass()方法获取：
    String s = "Hello";
    Class cls = s.getClass();

    3. 如果知道一个class的完整类名，可以通过静态方法Class.forName()获取：
    Class cls = Class.forName("java.lang.String");
    ```

    因为`Class`实例在`JVM`中是唯一的，所以，上述方法获取的`Class`实例是同一个实例。可以用`==`比较两个`Class`实例：
    ```java
    Class cls1 = String.class;

    String s = "Hello";
    Class cls2 = s.getClass();

    boolean sameClass = cls1 == cls2; // true
    ```

    要从`Class`实例获取获取的基本信息，参考下面的代码：
    ```java
    public class Main {
        public static void main(String[] args) {
            printClassInfo("".getClass());
            printClassInfo(Runnable.class);
            printClassInfo(java.time.Month.class);
            printClassInfo(String[].class);
            printClassInfo(int.class);
        }

        static void printClassInfo(Class cls) {
            System.out.println("Class name: " + cls.getName());
            System.out.println("Simple name: " + cls.getSimpleName());
            if (cls.getPackage() != null) {
                System.out.println("Package name: " + cls.getPackage().getName());
            }
            System.out.println("is interface: " + cls.isInterface());
            System.out.println("is enum: " + cls.isEnum());
            System.out.println("is array: " + cls.isArray());
            System.out.println("is primitive: " + cls.isPrimitive());
        }
    }
    // 注意到数组（例如String[]）也是一种Class，而且不同于String.class，它的类名是[Ljava.lang.String。
    // 此外，JVM为每一种基本类型如int也创建了Class，通过int.class访问。
    ```
    如果获取到了一个`Class`实例，我们就可以通过该`Class`实例来创建对应类型的实例：
    ```java
    // 获取String的Class实例:
    Class cls = String.class;
    // 创建一个String实例:
    String s = (String) cls.newInstance();
    // 上述代码相当于new String()。
    // 通过Class.newInstance()可以创建类实例，它的局限是：只能调用public的无参数构造方法。
    // 带参数的构造方法，或者非public的构造方法都无法通过Class.newInstance()被调用。
    ```

    JVM的动态加载：

    JVM在执行Java程序的时候，**并不是一次性把所有用到的class全部加载到内存，而是第一次需要用到class时才加载**。例如：
    ```java
    // Main.java
    public class Main {
        public static void main(String[] args) {
            if (args.length > 0) {
                create(args[0]);
            }
        }

        static void create(String name) {
            Person p = new Person(name);
        }
    }
    ```
    当执行`Main.java`时，由于用到了`Main`，因此，`JVM`首先会把`Main.class`加载到内存。然而，并不会加载`Person.class`，除非程序执行到`create()`方法，`JVM`发现需要加载`Person`类时，才会首次加载`Person.class`。如果没有执行`create()`方法，那么`Person.class`根本就不会被加载。

    `JVM`动态加载`class`的特性对于Java程序非常重要。利用`JVM动态加载class`的特性，我们才能在运行期根据条件加载不同的实现类。

- **Field类**

    `Field`类提供类或接口中单独字段的信息，以及对`单独字段`的`动态访问`。

    对任意的一个`Object`实例，只要我们获取了它的`Class`，就可以获取它的一切信息。

    获取`Field`类对象，一共有四种方法，全部都在`Class`类中：
    ```java
    1. Field getField(name)：根据字段名获取某个public的field（包括父类）
    2. Field getDeclaredField(name)：根据字段名获取当前类的某个field（不包括父类）
    3. Field[] getFields()：获取所有public的field（包括父类）
    4. Field[] getDeclaredFields()：获取当前类的所有field（不包括父类）
    ```
    一个`Field`对象包含一个字段的所有信息：
    ```java
    1. getName()：返回字段名称，例如，"name"；
    2. getType()：返回字段类型，也是一个Class实例，例如，String.class；
    3. getModifiers()：返回字段的修饰符，它是一个int，不同的bit表示不同的含义。
    ```

    `Field`类中常用的方法:

    获取类中字段的值、修改字段的值

    ```java
    public class Main {

        public static void main(String[] args) throws Exception {
            //获取字段值
            Object p1 = new Person("Xiao Ming");
            Class c1 = p1.getClass();
            Field f1 = c1.getDeclaredField("name");
            f1.setAccessible(true);// 因为这个字段的值是private的所以需要通过这个方法修改权限，别管这个字段是不是public，一律允许访问。
            Object value = f1.get(p1);
            System.out.println(value); // "Xiao Ming"

            //修改字段值
            Person p2 = new Person("Xiao Ming");
            System.out.println(p2.getName()); // "Xiao Ming"
            Class c2 = p2.getClass();
            Field f2 = c2.getDeclaredField("name");
            f2.setAccessible(true);// 因为这个字段的值是private的所以需要通过这个方法修改权限，别管这个字段是不是public，一律允许访问。
            f2.set(p2, "Xiao Hong");
            System.out.println(p2.getName()); // "Xiao Hong"
        }
    }

    class Person {
        private String name;

        public Person(String name) {
            this.name = name;
        }

        public String getName() {
            return this.name;
        }
    }
    ```
    如果使用反射可以获取`private`字段的值，那么类的封装还有什么意义？

    答案是正常情况下，我们总是通过`p.name`来访问`Person`的`name`字段，编译器会根据`public`、`protected`和`private`决定是否允许访问字段，这样就达到了数据封装的目的。

    而反射是一种非常规的用法，使用反射，首先代码非常繁琐，其次，它更多地是给工具或者底层框架来使用，目的是在不知道目标实例任何信息的情况下，获取特定字段的值。

    此外，`setAccessible(true)`可能会失败。如果`JVM`运行期存在`SecurityManager`，那么它会根据规则进行检查，有可能阻止`setAccessible(true)`。例如，某个`SecurityManager`可能不允许对`java`和`javax`开头的`package`的类调用`setAccessible(true)`，这样可以保证`JVM`核心库的安全。

- **Method类**

    类提供有关类或接口上单个方法的信息和访问权限。反映的方法可以是类方法或实例方法(包括抽象方法)。

    跟`Field`一样，获取`Method`类对象，一共有四种方法，全部都在`Class`类中：
    ```java
    1. Method getMethod(name, Class...)：获取某个public的Method（包括父类）
    2. Method getDeclaredMethod(name, Class...)：获取当前类的某个Method（不包括父类）
    3. Method[] getMethods()：获取所有public的Method（包括父类）
    4. Method[] getDeclaredMethods()：获取当前类的所有Method（不包括父类）
    ```

    一个Method对象包含一个方法的所有信息：
    ```java
    1. getName()：返回方法名称，例如："getScore"；
    2. getReturnType()：返回方法返回值类型，也是一个Class实例，例如：String.class；
    3. getParameterTypes()：返回方法的参数类型，是一个Class数组，例如：{String.class, int.class}；
    4. getModifiers()：返回方法的修饰符，它是一个int，不同的bit表示不同的含义
    ```

    `Method`类中常用的方法:

    1. 调用普通方法:

    当我们获取到一个`Method`对象时，就可以对它进行调用。我们以下面的代码为例：
    ```java
    tring s = "Hello world";
    String r = s.substring(6); // "world"
    ```
    用反射来调用substring方法：
    ```java
    public class Main {
        public static void main(String[] args) throws Exception {
            // String对象:
            String s = "Hello world";
            // 获取String substring(int)方法，参数为int:
            Method m = String.class.getMethod("substring", int.class);
            // 在s对象上调用该方法并获取结果:
            String r = (String) m.invoke(s, 6);
            // 打印调用结果: World
            System.out.println(r); 
        }
    }
    ```
    注意到`substring()`有两个重载方法，我们获取的是S`tring substring(int)`这个方法。思考一下如何获取`String substring(int, int)`方法。

    对`Method`实例调用`invoke`就相当于调用该方法，`invoke`的第一个参数是对象实例，即在哪个实例上调用该方法，后面的可变参数要与方法参数一致，否则将报错。

    1. 调用静态方法:

    如果获取到的`Method`表示一个静态方法，调用静态方法时，由于无需指定实例对象，所以`invoke`方法传入的第一个参数永远为`null`。我们以`Integer.parseInt(String)`为例：
    ```java
    public class Main {
        public static void main(String[] args) throws Exception {
            // 获取Integer.parseInt(String)方法，参数为String:
            Method m = Integer.class.getMethod("parseInt", String.class);
            // 调用该静态方法并获取结果:
            Integer n = (Integer) m.invoke(null, "12345");
            // 打印调用结果: 12345
            System.out.println(n);
        }
    }
    ```
    1. 调用非public方法:

    和`Field`类似，对于`非public`方法，我们虽然可以通过`Class.getDeclaredMethod()`获取该方法实例，但直接对其调用将得到一个`IllegalAccessException`。为了调用`非public`方法，我们通过`Method.setAccessible(true)`允许其调用：
    ```java
    public class Main {
        public static void main(String[] args) throws Exception {
            Person p = new Person();
            Method m = p.getClass().getDeclaredMethod("setName", String.class);
            m.setAccessible(true);
            m.invoke(p, "Bob");
            System.out.println(p.name);
        }
    }
    class Person {
        String name;
        private void setName(String name) {
            this.name = name;
        }
    }
    ```
    此外，`setAccessible(true)`可能会失败。如果`JVM`运行期存在`SecurityManager`，那么它会根据规则进行检查，有可能阻止`setAccessible(true)`。例如，某个`SecurityManager`可能不允许对java和javax开头的`package`的类调用`setAccessible(true)`，这样可以保证JVM核心库的安全

    1. 多态（继承父类重写方法）

    一个`Person`类定义了`hello()`方法，并且它的子类`Student`也覆写了`hello(`)方法，那么，从`Person.class`获取的`Method`，作用于`Student`实例时，使用反射调用方法时，仍然遵循多态原则：即总是调用实际类型的覆写方法（如果存在）。
    ```java
    public class Main {
        public static void main(String[] args) throws Exception {
            // 获取Person的hello方法:
            Method h = Person.class.getMethod("hello");
            // 对Student实例调用hello方法 打印出: Student:hello 
            h.invoke(new Student());

            // 上面两行代码实际相当于以下的调用方式
            // Person p = new Student();
            // p.hello();
        }
    }
    class Person {
        public void hello() {
            System.out.println("Person:hello");
        }
    }
    class Student extends Person {
        public void hello() {
            System.out.println("Student:hello");
        }
    }
    ```

- **Constructor类**

    Constructor 类提供了访问构造器的信息，以及利用构造器创建对象的一系列方法。当使用 Constructor 创建对象时，参数列表支持自动宽化（比如需要 int 而传入 short ），不支持自动窄化（比如需要 int 而传入 long）。

    我们通常使用`new`操作符创建新的实例：`Person p = new Person();`。

    如果通过反射来创建新的实例，可以调用`Class`提供的`newInstance()`方法：`Person p = Person.class.newInstance();`。而调用`Class.newInstance()`的局限是，它只能调用该类的`public`无参数构造方法。如果构造方法带有参数，或者不是`public`，就无法直接通过`Class.newInstance()`来调用。

    为了调用任意的构造方法，Java的反射API提供了`Constructor`对象，它包含一个构造方法的所有信息，可以创建一个实例。`Constructor`对象和`Method`非常类似，不同之处仅在于它是一个构造方法，并且，调用结果总是返回实例：
    ```java
    public class Main {
        public static void main(String[] args) throws Exception {
            // 获取构造方法Integer(int): 
            Constructor cons1 = Integer.class.getConstructor(int.class);
            // 调用构造方法:
            Integer n1 = (Integer) cons1.newInstance(123);
            System.out.println(n1); // 123

            // 获取构造方法Integer(String)
            Constructor cons2 = Integer.class.getConstructor(String.class);
            Integer n2 = (Integer) cons2.newInstance("456");
            System.out.println(n2);// 456
        }
    }
    ```

    通过`Class`实例获取`Constructor`的方法如下：
    ```java
    1. getConstructor(Class...)：获取某个public的Constructor；
    2. getDeclaredConstructor(Class...)：获取某个Constructor；
    3. getConstructors()：获取所有public的Constructor；
    4. getDeclaredConstructors()：获取所有Constructor。
    ```
    注意`Constructor`总是当前类定义的构造方法，和父类无关，因此不存在多态的问题。

    调用`非public`的`Constructor`时，必须首先通过`setAccessible(true)`设置允许访问。`setAccessible(true)`可能会失败。

### 怎么理解Java ClassLoader？

`ClassLoader`（类装载器）是Java反射中一个非常重要的类，普通Java开发者对其应用不多，对于某些框架开发者来说那就是必须要会的。理解`ClassLoader`的加载机制，可以有利于我们编码写出更高效的代码。

**ClassLoader 的具体作用是将 class 文件加载到 JVM 去，但是 JVM 启动的时候，并不会一次性加载所有的 class 文件，而是根据需要来动态加载。加载完成后就会将 Class 对象存在 ClassLoader 里面，下次就不需要重新加载了。**

`ClassLoader`使用的是双亲委托模型来搜索和加载类，`ClassLoader`的类继承图如下：

![](https://resource.lzyan.fun/PigGo/20211212152642.png)

JVM 运行实例中会存在多个`ClassLoader`，不同的`ClassLoader`会从不同的地方加载字节码文件。它可以从不同的文件目录加载，也可以从不同的 jar 文件中加载，也可以从网络上不同的服务地址来加载(以下AppClassLoader就是图中的ApplicationClassLoader)：

`BootstrapClassLoader` 负责加载 JVM 运行时核心类，这些类位于 JAVA_HOME/lib/rt.jar 文件中，我们常用内置库 java.xxx.* 都在里面，比如 java.util.*、java.io.*、java.nio.*、java.lang.* 等等。这个 ClassLoader 比较特殊，它是由 C 代码实现的，我们将它称之为「根加载器」。

`ExtensionClassLoader` 负责加载 JVM 扩展类，比如 swing 系列、内置的 js 引擎、xml 解析器 等等，这些库名通常以 javax 开头，它们的 jar 包位于 JAVA_HOME/lib/ext/*.jar 中，有很多 jar 包。

`AppClassLoader` 才是直接面向我们用户的加载器，它会加载 Classpath 环境变量里定义的路径中的 jar 包和目录。我们自己编写的代码以及使用的第三方 jar 包通常都是由它来加载的

那些位于网络上静态文件服务器提供的`jar`包和`class`文件，`jdk`内置了一个 `URLClassLoader`，用户只需要传递规范的网络路径给构造器，就可以使用`URLClassLoader`来加载远程类库了。

`URLClassLoader`不但可以加载远程类库，还可以加载本地路径的类库，取决于构造器中不同的地址形式。`ExtensionClassLoader`和`AppClassLoader`都是`URLClassLoader`的子类，它们都是从本地文件系统里加载类库。

[老大难的 Java ClassLoader 再不理解就老了](https://zhuanlan.zhihu.com/p/51374915)

[深入理解Java类加载器(ClassLoader) ](https://www.cnblogs.com/cl-rr/p/9081817.html)

### 什么是动态代理？

**动态代理其实就是代理模式的一种，代理模式是设计模式之一。代理模型有静态代理和动态代理。**

静态代理需要自己写代理类，实现对应的接口，比较麻烦。在Java中，动态代理常见的又有两种实现方式：`JDK动态代理`和`CGLIB代理`。

`JDK动态代理`其实就是运用了反射的机制，而`CGLIB代理`则用的是利用ASM框架（Java字节码操控框架），通过修改其`字节码`生成子类来处理。JDK动态代理会帮我们实现接口的方法，通过invokeHandler对所需要的方法进行增强。

简单地说：**如果程序运行前就在Java代码中定义好代理类(Proxy)，那么这种代理方式就叫做静态代理；若代理类在程序运行时创建就叫做动态代理。**

- 如果为特定类的特定方法生成固定的代理，当然使用静态代理就能很好满足需求。
- 如果要为大量不同类的不同方法生成代理，使用静态代理的话就需要编写大量的代理类，且大量代码冗余，此时动态代理就应该闪亮登场了。

[一文读懂Java中的动态代理](https://www.cnblogs.com/bytesfly/p/dynamic-proxy-in-java.html)

### 说一说反射有哪些应用场景？

1. 我们在使用JDBC连接数据库时使用 `Class.forName()` 通过反射加载数据库的驱动程序

    ```java
    Class.forName("com.mysql.jdbc.Driver");
    //获取与数据库连接的对象
    connection = DriverManager.getConnection("jdbc:mysql//localhost:3306/myblog","root","password");
    //获取执行sql语句的statement对象
    statement = connection.createStatement();
    //执行sql语句
    statement.executeQuery("SELECT * FORM users");
    ```

    后来过渡到以配置的方式，也都是使用了反射机制来实现
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

2. `Servlet` 和 `Spring MVC` 中，`Servlet` 是通过 `getParameter()` 来填充值的，而 `Spring MVC` 中却是通过约定好的 `JavaBean` 的字段名来把值填充进去的，

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
    `Spring MVC` 这种简单的写法其实就是使用反射来实现的。在编写 `JavaBean` 的时候，保持字段名和参数名相同，就能自动装配而得到对应的值。**屏蔽掉实现的细节，让使用者更加方便好用**

3. `Mybatis` 可以让我们只写接口，不写实现类，就可以执行SQL。类上加上@Component注解，Spring就帮你创建对象，等等。这些统统都有反射的身影：约定大于配置，配置大于硬编码。

## 注解

### 什么是Java注解，有什么作用？

`注解（Annontation）`是 Java5 开始引入的新特性，Java 注解是附加在代码中的一些元信息，可以在`编译`、`类加载`、`运行时`被读取，并执行相对应的处理，起到`说明`、`配置`的功能。注解不会也不能影响代码的实际逻辑，仅仅起到辅助性的作用。

注解的作用：

- **提供信息给编译器：** 编译器可利用注解来探测错误和警告信息。
- **编译阶段：** 软件工具可以利用注解信息来生成代码、html 文档或做其它相应处理。
- **运行阶段：** 程序运行时可利用注解提取代码

注解是通过反射获取的，可以通过`Class`对象的`isAnnotationPresent()`方法判断它是否应用了某个注解，再通过`getAnnotation()`方法获取`Annotation`对象

### 说说你对Java注解的理解？

注解的本质是一个继承了Annoation的特殊接口，其具体实现类是Java运行时生成的动态代理类。而我们通过反射获取注解时，返回的是Java运行时生成的动态代理对象。如果没有用来读取注解的方法和工作，那么注解也就不会比注释更有用处了。

解析一个注解往往有两种形式：
- **编译期直接的扫描**
    编译器的扫描指的是编译器在对 java 代码编译字节码的过程中会检测到某个类或者方法被一些注解修饰，这时它就会对于这些注解进行某些处理。这种情况只适用于JDK内置的注解类。
- **运行期的反射**
    如果要自定义注解，Java 编译器无法识别并处理这个注解，它只能根据该注解的作用范围来选择是否编译进字节码文件。如果要处理注解，必须利用反射技术，识别该注解以及它所携带的信息，然后做相应的处理。

Java原生注解有：`@Override`、`@Deprecated`、`@SuppressWarnnings`、`@SafeVarargs（JDK7 引入）`、`@FunctionalInterface（JDK8 引入）`。

原生Java虽然内置了几个注解，但这远远不能满足开发过程中千变万化的需求，除了这些提供基本注解之外，还有一种叫做`元注解`，**元注解的作用就是用于定义其它的注解**。

Java中提供了的元注解类型：`@Retention`、`@Target`、`@Documented`、`@Inherited（JDK8 引入）`、`@Repeatable（JDK8 引入）`

元注解的作用：
  1. `@Retention` 说明注解的存活时间，取值有 RetentionPolicy.SOURCE 注解只在源码阶段保留，在编译器进行编译时被丢弃；RetentionPolicy.CLASS 注解只保留到编译进行的时候（class文件中有效），并不会被加载到 JVM 中（JVM忽略）。RetentionPolicy.RUNTIME 标记的注解在运行时有效。
  2. `@Documented` 注解中的元素包含到 javadoc 中去。
  3. `@Target` 限定注解的应用场景，ElementType.FIELD 给属性进行注解；ElementType.LOCAL_VARIABLE 可以给局部变量进行注解；ElementType.METHOD 可以给方法进行注解；ElementType.PACKAGE 可以给一个包进行注解 ElementType.TYPE 可以给一个类型进行注解，如类、接口、枚举。
  4. `@Inherited` 表示自动继承注解类型。 如果注解类型声明中存在 @Inherited 元注解，则注解所修饰类的所有子类都将会继承此注解；
  5. `@Repeatable` 表示注解可以重复使用。

其中理解`@Retention`这块得了解从.java文件到class文件再到class被jvm加载的过程，如下图：

![](https://resource.lzyan.fun/PigGo/20211218131323.png)

从上面的图可以发现有个`注解抽象语法树`，这里其实就会去解析注解，然后做处理的逻辑。

如果你想要在编译期间处理注解相关的逻辑，你需要继承`AbstractProcessor`并实现`process`方法。比如可以看到`lombok`就用`AnnotationProcessor`继承了`AbstractProcessor`。

一般来说，只要自定义的注解中`@Retention`注解设置为`SOURCE`和`CLASS`这俩个级别，那么就需要继承并实现（因为SOURCE和CLASS这俩个级别等加载到jvm的时候，注解就被抹除了），lombok的实现原理就是在这（为什么使用了个@Data这样的注解就能有set/get等方法了，就是在这里加上去的）

### 如何自定义注解？

使用`@interface`自定义注解，自动继承了`java.lang.Annotation`接口，由编译程序自动完成其他细节。自定义注解类编写的一些规则：
1. 在定义注解时，不能继承其他的注解或接口。
2. `@interface`用来声明一个注解，其中每一个方法实际上是声明了一个配置参数。
3. 方法的名称，返回值类型就是参数的类型（返回值类型只能是基本类型、Clas、String、enum）。
4. 可以通过`default`来声明参数的默认值
5. 要获取类方法和字段的注解信息，必须通过Java的反射技术来获取 Annotation 对象，因为你除此之外没有别的获取注解对象的方法
6. 注解也可以没有定义成员，不过这样注解就没啥用了
7. 自定义注解需要使用元注解

自定义注解示例：[自定义正则校验注解](https://dunwu.github.io/javacore/basics/java-annotation.html#_4-%E8%87%AA%E5%AE%9A%E4%B9%89%E6%B3%A8%E8%A7%A3)


### 自定义注解的场景及实现？

利用自定义注解，结合 SpringAOP 可以完成
1. 权限控制。
2. 日志记录。
3. 统一异常处理。
4. 数字签名。
5. 数据加解密等功能。

实现场景（API接口数据加解密）

1. 自定义一个注解，在需要加解密的方法上添加该注解
2. 配置SringAOP环绕通知
3. 截获方法入参并进行解密
4. 截获方法返回值并进行加密

## 泛型

### 泛型是什么、为什么需要泛型？

`Java泛型（generics）`是JDK5中引入的一个新特性，泛型提供了`编译时`类型安全检测机制，该机制允许程序员在编译时检测到非法的类型。简单来说就是，把`明确类型`的工作推迟到`创建对象`或`调用方法`的时候才去明确的特殊的类型。

为什么需要泛型？可以看这个示例:
```java
public class NoGenericsDemo {
    public static void main(String[] args) {
        List list = new ArrayList<>();
        list.add("abc");
        list.add(18);
        list.add(new double[] {1.0, 2.0});
        Object obj1 = list.get(0);
        Object obj2 = list.get(1);
        Object obj3 = list.get(2);
        System.out.println("obj1 = [" + obj1 + "]");
        System.out.println("obj2 = [" + obj2 + "]");
        System.out.println("obj3 = [" + obj3 + "]");

        int num1 = (int)list.get(0);
        int num2 = (int)list.get(1);
        int num3 = (int)list.get(2);
        System.out.println("num1 = [" + num1 + "]");
        System.out.println("num2 = [" + num2 + "]");
        System.out.println("num3 = [" + num3 + "]");
    }
}
// Output:
// obj1 = [abc]
// obj2 = [18]
// obj3 = [[D@47089e5f]
// Exception in thread "main" java.lang.ClassCastException: java.lang.String cannot be cast to java.lang.Integer
// at io.github.dunwu.javacore.generics.NoGenericsDemo.main(NoGenericsDemo.java:23)
```
> 示例说明：
> 
> `List` 容器没有指定存储数据类型，这种情况下，可以向 `List` 添加任意类型数据，编译器不会做类型检查，而是默默的将所有数据都转为 `Object`。
假设，最初我们希望向 `List` 存储的是整形数据，假设，某个家伙不小心存入了其他数据类型。当你试图从容器中取整形数据时，由于 `List` 当成 `Object` 类型来存储，你不得不使用类型强制转换。在运行时，才会发现 `List` 中数据不存储一致的问题，这就为程序运行带来了很大的风险（无形伤害最为致命）。

**优点**：

- **编译时的强类型检查**

泛型要求在声明时指定实际数据类型，Java 编译器在编译时会对泛型代码做强类型检查，并在代码违反类型安全时发出告警。早发现，早治理，把隐患扼杀于摇篮，在编译时发现并修复错误所付出的代价远比在运行时小。

- **泛型编程可以实现通用算法**

通过使用泛型，程序员可以实现通用算法，这些算法可以处理不同类型的集合，可以自定义，并且类型安全且易于阅读。

- **避免了类型转换**

如上述示例

### 泛型的实现原理是什么？

Java 泛型是在 Java1.5 以后出现的，实现方式不太优雅，是为保持对以前版本的兼容，使用了类型擦除的方法实现泛型。**所谓擦除法是指，虚拟机对泛型其实一无所知，所有的工作都是编译器做的**。

类型擦除做了什么工作？

- 把泛型中的所有类型参数替换为 `Object`，如果指定类型边界，则使用类型边界来替换。因此，生成的字节码仅包含普通的类，接口和方法。
- 擦除出现的类型声明，即去掉` <> `的内容。比如 `T get() `方法声明就变成了 `Object get()` ；`List<String>` 就变成了 `List`。如有必要，插入类型转换以保持类型安全。
- 生成桥接方法以保留扩展泛型类型中的多态性。类型擦除确保不为参数化类型创建新类；因此，泛型不会产生运行时开销。

示例，编写一个泛型类`Pair<T>`，这是编译器看到的代码：
```java
public class Pair<T> {
    private T first;
    private T last;
    public Pair(T first, T last) {
        this.first = first;
        this.last = last;
    }
    public T getFirst() {
        return first;
    }
    public T getLast() {
        return last;
    }
}
```
而虚拟机根本不知道泛型。这是虚拟机执行的代码：
```java
public class Pair {
    private Object first;
    private Object last;
    public Pair(Object first, Object last) {
        this.first = first;
        this.last = last;
    }
    public Object getFirst() {
        return first;
    }
    public Object getLast() {
        return last;
    }
}
```
因此，Java 使用擦除法实现泛型导致了：编译器把类型 `<T>` 视为 `Object` ；编译器根据 `<T>` 实现安全的类型强转

而在使用泛型的时候，编写的代码也是编译器看到的代码，而虚拟机执行的代码并没有泛型：
```java
// 我们编写的代码
Pair<String> p = new Pair<>("Hello", "world");
String first = p.getFirst();
String last = p.getLast();

// 虚拟机执行的代码
Pair p = new Pair("Hello", "world");
String first = (String) p.getFirst();
String last = (String) p.getLast();
```
所以，Java 的泛型是由编译器在编译时实行的，编译器内部永远把所有类型`T`视为`Object`处理，但是，在需要转型的时候，编译器会根据`T`的类型自动为我们实行安全地强制转型。

擦除法决定了泛型`<T>`：
- **不能是基本类型，例如：int；**
- **不能获取带泛型类型的 Class，例如：Pair<String>.class；**
- **不能判断带泛型类型的类型，例如：x instanceof Pair<String>；**
- **不能实例化T类型，例如：new T()。**

### 泛型的使用方式有哪些？

泛型一般有三种使用方式: `泛型类`、`泛型接口`、`泛型方法`。

- **泛型类**

```java
//此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
//在实例化泛型类时，必须指定T的具体类型
public class Generic<T> {
    private T key;
    public Generic(T key) {
        this.key = key;
    }
    public T getKey() {
        return key;
    }
}
```
实例化泛型类：
```java
Generic<Integer> genericInteger = new Generic<Integer>(123456);
```

- **泛型接口**

```java
public interface Generator<T> {
    public T method();
}
```
实现泛型接口，不指定类型：
```java
class GeneratorImpl<T> implements Generator<T>{
    @Override
    public T method() {
        return null;
    }
}
```
实现泛型接口，指定类型：
```java
class GeneratorImpl implements Generator<String>{
    @Override
    public String method() {
        return "hello";
    }
}
```

- **泛型方法**

```java
public static <E> void printArray(E[] inputArray) {
    for (E element : inputArray) {
        System.out.printf("%s ", element);
    }
    System.out.println();
}
```
使用：
```java
// 创建不同类型数组： Integer, Double 和 Character
Integer[] intArray = { 1, 2, 3 };
String[] stringArray = { "Hello", "World" };
printArray(intArray);
printArray(stringArray);
```

### List<? extends T>、List <? super T>、List<?\> 之间有什么区别 ?

`List<? extends T>`这种泛型形式表示具有`上界通配符`，可以接受任何继承自T的类型的List。

`List<? super T>`这种泛型形式表示具有`下界通配符`，可以接受任何T的父类构成的List。

`List<?>`这种泛型形式表示具有`无界通配符`，可以理解为想要使用泛型列表，但是不确定具体的类型，用`?`标识任意类型都可以。由于是泛型，所以列表中的元素的类型理应一致。而跟直接定义`List`对比，List可以理解为持有任意Object类型的原始列表。

### 可以把 List<String\> 传递给一个接受 List<Object\> 参数的方法吗？

对任何一个不太熟悉泛型的人来说，这个 Java 泛型题目看起来令人疑惑，因为乍看起来 String 是一种 Object ，所以`List<String>`应当可以用在需要 `List<Object>` 的地方，但是事实并非如此。

真这样做的话会导致编译错误。如果你再深一步考虑，你会发现 Java 这样做是有意义的，因为`List<Object>`可以存储任何类型的对象包括 String ,  Integer 等等，而 `List<String>` 却只能用来存储 Strings。

### 泛型在实践中的有哪些技巧？

**泛型一些约定俗成的命名：**

- E - Element
- K - Key
- N - Number
- T - Type
- V - Value
- S,U,V etc. - 2nd, 3rd, 4th types

**使用泛型的建议：**

- 消除类型检查告警
- List 优先于数组：因为List可以提供编译期的类型安全保证，而Array却不能
- 优先考虑使用泛型来提高代码通用性
- 优先考虑泛型方法来限定泛型的范围
- 利用有限制通配符来提升 API 的灵活性
- 优先考虑类型安全的异构容器