> 八股文整合，该系列文档搜集于各大平台，据理解和需求整合
> 
> 参考资料平台：javaGuide、java3y、cs-notes、廖雪峰的Java教程、javacore

## 概述

### Java 集合框架包含哪些接口和实现类？

Java 集合，也称作 `容器`，主要是由两大接口派生而来：`Collection`接口，主要用于存放单一元素；`Map`接口，主要用于存放键值对。顾名思义，容器就是用来存放数据的。

Java 集合框架主要的继承派生关系：

![](https://resource.lzyan.fun/PigGo/20211224160454.png)

- `Collection`  一个独立元素的序列，这些元素都服从一条或者多条规则
  - `List` 存储的元素是有序的、可重复的
  - `Set` 存储的元素是无序的、不可重复的
  - `Queue` 按特定的排队规则来确定先后顺序，存储的元素是有序的、可重复的
- `Map` 一组成对的"键值对"对象，允许使用键来查询值

### 为什么需要使用集合？

当我们需要保存一组类型相同的数据的时候，我们可以使用数组来进行存储，但数组的缺点是一旦声明之后，长度就不可变了；同时，声明数组时的数据类型也决定了该数组存储的数据的类型；而且，数组存储的数据是有序的、可重复的，特点单一。**但是 Java 集合提高了数据存储的灵活性，Java 集合不仅可以用来存储不同类型不同数量的对象，还可以保存具有映射关系的数据。**

数组与集合，二者有以下区别：
- 存储大小是否固定

    数组的长度 `固定`；

    集合的长度 `可变`。
- 数据类型

    数组可以存储基本数据类型，也可以存储引用数据类型；

    集合只能存储引用数据类型，基本数据类型的变量要转换成对应的包装类才能放入容器类中。


例子：数组 与 ArrayList

在日常开发的时候，往往是不知道 `数组` 的大小的。如果 `数组` 的大小指定多了，内存浪费；如果 `数组` 大小指定少了，装不下。假设给定 `数组` 的大小是10，要往这个 `数组` 里边填充元素，我们只能添加 10 个元素。

但 `ArrayList` 不一样，`ArrayList` 我们在使用的时候可以往里边添加 20 个，30 个，甚至更多的元素。

因为 `ArrayList` 是实现了动态扩容的，大概的意思就是：当我们 `new ArrayList()` 的时候，默认会有一个空的 Object 数组，大小为 0。当我们第一次 add 添加数据的时候，会给这个数组初始化一个大小，这个大小默认值为 10，使用 `ArrayList` 在每一次 add 的时候，它都会先去计算这个数组够不够空间，如果空间是够的，那直接追加上去就好了。如果不够，那就得扩容。

### Collection 和 Collections 的区别？

`Collection` 是一个接口，它是Set、List等容器的父接口；`Collections` 是一个工具类，提供了一系列的静态方法来辅助容器操作，这些方法包括对容器的搜索、排序、线程安全化等

### 无序性和不可重复性的含义是什么?

无序性不等于随机性，无序性是指存储的数据在底层数组中并非按照数组索引的顺序添加，而是根据数据的哈希值决定的。

不可重复性是指添加的元素按照equals()判断时，返回false,需要同时重写equals()方法和HashCode()方法。

### fail-fast 和 fail-safe 是什么？

`fail-fast` 是 Java 容器的一种错误检测机制。`modCount` 用来记录 `ArrayList` 结构发生变化的次数，当多个线程对容器进行结构上的改变的操作时（结构发生改变是指添加或者删除至少一个元素的所有操作，或者是调整内部数组的大小，仅仅只是设置元素的值不算结构发生变化），`modCount`发生改变，比较操作前后 `modCount` 是否改变时就可能触发 `fail-fast` 机制。即使不是多线程环境，如果单线程违反了规则，同样也有可能抛出异常。

与 `fail-fast` 相对应，解决 `fail-fast` 的就是 `fail-safe` 机制，在JUC包集合都是有这种机制实现。表示的是在遍历时不是直接在原集合上进行访问，而是先复制原有集合内容，在拷贝的集合上进行遍历。 由于迭代时是对原集合的拷贝进行遍历，所以在 遍历过程中对原集合所作的修改并不能被迭代器检测到 所以不会触发 `ConcurrentModificationException`。 `java.util.concurrent` 包下的容器都是安全失败的，可以在多线程条件下使用，并发修改。fail-safe迭代缺点是：首先不能保证返回集合更新后的数据，因为其工作在克隆的集合上，而非集合本身，其次创建集合拷贝需要相应的开销，包括时间和内存。

`fail-fast` 产生示例：容器在迭代操作中改变元素个数（添加、删除元素）都可能会导致 fail-fast。

```java
public class FailFastDemo {

    private static int MAX = 100;

    private static List<Integer> list = new ArrayList<>();

    public static void main(String[] args) {
        for (int i = 0; i < MAX; i++) {
            list.add(i);
        }
        new Thread(new MyThreadA()).start();
        new Thread(new MyThreadB()).start();
    }

    /** 迭代遍历容器所有元素 */
    static class MyThreadA implements Runnable {

        @Override
        public void run() {
            Iterator<Integer> iterator = list.iterator();
            while (iterator.hasNext()) {
                int i = iterator.next();
                System.out.println("MyThreadA 访问元素:" + i);
                try {
                    TimeUnit.MILLISECONDS.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }

    }

    /** 遍历删除指定范围内的所有偶数 */
    static class MyThreadB implements Runnable {

        @Override
        public void run() {
            int i = 0;
            while (i < MAX) {
                if (i % 2 == 0) {
                    System.out.println("MyThreadB 删除元素" + i);
                    list.remove(i);
                }
                i++;
            }
        }

    }

}
```

`fail-safe`两种解决方案：
- 在遍历过程中所有涉及到改变容器个数的地方全部加上 `synchronized` 或者直接使用 `Collections.synchronizedXXX` 容器，这样就可以解决。但是不推荐，因为增删造成的同步锁可能会阻塞遍历操作，影响吞吐。
- 使用并发容器，如：`CopyOnWriterArrayList`

### Interator 和 Interable 的区别是什么？

`intertor` 和 `interable` 都是接口。`Collection` 继承了 `Iterable` 接口，它其中的 `iterator()` 方法能够产生一个 `Iterator` 对象，通过这个对象就可以迭代遍历 `Collection` 中的元素。从 JDK 1.5 之后可以使用 foreach 方法来遍历实现了 Iterable 接口的聚合对象。

为什么要这么做而不是让集合类直接实现 `Intertor` 接口？因为 `Intertor` 接口的核心方法 `next()` 或者 `hasNext()` 是依赖于迭代器的当前迭代位置。如果 `Collection` 直接实现 `Interator` 接口，势必导致集合对象中包含当前迭代位置的数据(指针)，当集合在不同方法间传递时由于当前迭代位置不可预置，那么 `next()` 方法的结果回变成不可预知。除非 `Interator` 接口添加一个 `reset()` 方法，用来重置当前的迭代位置。但即使这样，`Collection` 也**只能同时存在一个**当前迭代位置，而 `Interable` 则不然，每次调用都会返回一个从头开始计数的迭代器，多个迭代器是互不干扰的。

## List

### ArrayList 和 LinkedList 的区别？

1. **数据结构：** `ArrayList` 底层使用的是 `Object` 数组，存储空间是连续的；`LinkedList` 底层使用的是双向链表数据结构，存储空间是不连续的。（JDK1.6之前为循环链表，JDK1.7取消了循环。）

2. **线程安全：** `ArrayList` 和 `LinkedList` 都是不同步的，也就是不保证线程安全。

3. **增删数据：** 对于新增和删除操作 `add` 和 `remove` ，`LinedList` 比较占优势，因为 `ArrayList` 要移动数据。当使用 `ArrayList` 执行 `add(E e)` 方法的时候， `ArrayList` 会默认在将指定的元素追加到此列表的末尾，这种情况时间复杂度就是 `O(1)`。但是如果要在指定位置 `i` 插入和删除元素的话 `（add(int index, E element)）` 时间复杂度就为 `O(n-i)`。当使用 `LinkedList` 在头尾插入或者删除元素不受元素位置的影响 `（add(E e)、addFirst(E e) 、addLast(E e) 、removeFirst()  、removeLast()）`，近似 `O(1)` ，如果是要在指定位置 `i` 插入和删除元素 `（add(int index, E element)，remove(Object o)）` 时间复杂度近似为 `O(n)` ，因为需要先移动到指定位置再插入。

4. **随机访问：** 快速随机访问就是通过元素的序号快速获取元素对象，对应于 `get(int index)` 方法。`ArrayList` 优于 `LinkedList`，因为 `LinkedList` 要移动指针。

5. **空间占用：** `ArrayList` 的空间浪费主要体现在在 `list` 列表的结尾会预留一定的容量空间，而 `LinkedList` 的空间花费则体现在它的每一个元素都需要消耗比 `ArrayList` 更多的空间，因为要存放直接后继和直接前驱以及数据。同样的数据量 `LinkedList` 所占用空间可能会更小，因为 `ArrayList` 需要预留空间便于后续数据增加，而 `LinkedList` 增加数据只需要增加一个节点。

### Vector 和 Stack 的区别？

`Vector` 和 `Stack` 的设计目标是作为线程安全的 `List` 实现，替代 `ArrayList`。

1. **Vector：** `Vector` 和 `ArrayList` 类似，也实现了 `List` 接口。但是， `Vector` 中的主要方法都是 `synchronized` 方法，即通过互斥同步方式保证操作的线程安全。
2. **Stack：** `Stack` 也是一个同步容器，它的方法也用 `synchronized` 进行了同步，它实际上是继承于 `Vector` 类。

### ArrayList 和 Array 有什么区别？

- `Array` 可以存储基本数据类型和对象，`ArrayList` 只能存储对象。
- `Array` 是指定固定大小的，而 `ArrayList` 大小是使用动态扩容的。
- `Array` 内置方法没有 `ArrayList` 多，比如 `addAll`、`removeAll`、`iteration` 等方法只有 `ArrayList` 有。

对于基本类型数据，集合使用自动装箱来减少编码工作量。但是，当处理固定大小的基本数据
类型的时候，这种方式相对比较慢。

### ArrayList 的要点？

- `ArrayList` 是一个数组队列，相当于动态数组。`ArrayList` 默认初始容量大小为 `10` ，添加元素时，如果发现容量已满，会自动扩容为原始大小的 1.5 倍左右（源码中的变量 oldCapacity 为偶数就是 1.5 倍，否则是 1.5 倍左右）

- `ArrayList` 实现了 `List` 接口，并继承了 `AbstractList`，它支持所有 `List` 的操作。

- `ArrayList` 实现了 `RandomAccess` 接口，支持随机访问。`RandomAccess` 是一个标志接口，它意味着“只要实现该接口的 `List` 类，都支持快速随机访问”。在 `ArrayList` 中，我们即可以通过元素的序号快速获取元素对象；这就是快速随机访问。

- `ArrayList` 实现了 `Cloneable` 接口，默认为浅拷贝。

- `ArrayList` 实现了 `Serializable` 接口，支持序列化，能通过序列化方式传输。

- `ArrayList` 是非线程安全的。


### LinkedList 的要点？

- `LinkedList` 基于双向链表数据结构实现。所以顺序访问会非常高效，而随机访问效率比较低。

- `LinkedList` 实现了 `List` 接口，并继承了 `AbstractSequentialList`，它支持所有 `List` 的操作。

- `LinkedList` 实现了 `Deque` 接口，也可以被当作队列( `Queue` ) 或双端队列（ `Deque` ）进行操作，此外也可以用来实现栈。

- `LinkedList` 实现了 `Cloneable` 接口，默认为浅拷贝。

- `LinkedList· 实现了 `Serializable` 接口，支持序列化。

- `LinkedList` 是非线程安全的。

### Arrays.asList 获得的 List 应该注意什么？

在业务开发中，我们常常会把原始的数组转换为 `List` 类数据结构，来继续展开各种 `Stream` 操作。通常，我们会使用 `Arrays.asList` 方法可以把数组一键转换为 `List`。

1. **不能直接使用 `Arrays.asList` 来转换基本类型数组**

其原因是：`Arrays.asList` 方法传入的是一个泛型 `T` 类型变长参数。
```java
public static <T> List<T> asList(T... a) {
    return new ArrayList<>(a);
}
```
如果使用 `Java8` 以上版本可以使用 `Arrays.stream` 方法来转换，否则可以把基本数据类型数组声明为包装类型数组：
```java
// Arrays.stream
int[] arr1 = { 1, 2, 3 };
List list1 = Arrays.stream(arr1).boxed().collect(Collectors.toList());
log.info("list:{} size:{} class:{}", list1, list1.size(), list1.get(0).getClass());
//output: list:{[1, 2, 3]} size:{3} class:{class java.lang.Integer}

// 包装类
Integer[] arr2 = { 1, 2, 3 };
List list2 = Arrays.asList(arr2);
log.info("list:{} size:{} class:{}", list2, list2.size(), list2.get(0).getClass());
//output: list:{[1, 2, 3]} size:{3} class:{class java.lang.Integer}
```

2. **`Arrays.asList` 返回的 `List` 不支持增删操作**

```java
public static void main(String[] args) {
 Integer[] integer = new Integer[] { 1, 2, 3, 4 };
 List integetList = Arrays.asList(integer);
 integetList.add(5);
}
// 结果抛出异常
// Exception in thread "main" java.lang.UnsupportedOperationException
```
这是由于 `Arrays.asList` 返回的 `List` 并不是期望的 `java.util.ArrayList`，而是 `Arrays` 的内部类 `ArrayList`。查看源码，可以发现 `Arrays.asList` 返回的 `ArrayList` 继承了 `AbstractList`，但是并没有覆写 `add` 和 `remove` 方法。

3. 使用 `Arrays.asList` 获得的 `List` 后对原始数组的修改会影响到获得的那个 `List`

`ArrayList` 其实是直接使用了原始的数组。解决方法很简单，重新 `new` 一个 `ArrayList` 初始化 `Arrays.asList` 返回的 `List` 即可：
```java
String[] arr = {"1", "2", "3"};
List<String> list1 = new ArrayList(Arrays.asList(arr));// 重新new
List<String> list2 = Arrays.asList(arr); // 直接获取List
arr[1] = "4"; // 改变原始数组
list1.add("5");
System.out.println(Arrays.toString(arr)); // [1, 4, 3]
System.out.println(list1); // [1, 2, 3, 5]
System.out.println(list2); // [1, 4, 3]
```

## Set

### HashSet、LinkedHashSet 和 TreeSet 三者的异同？


