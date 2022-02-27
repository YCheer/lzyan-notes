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

`Collection` 是一个接口，它是 `Set`、`List` 等容器的父接口；`Collections` 是一个工具类，提供了一系列的静态方法来辅助容器操作，这些方法包括对容器的搜索、排序、线程安全化等。

其中线程安全化中需要特别注意，`HashSet`，`TreeSet`，`ArrayList`,`LinkedList`,`HashMap`,`TreeMap` 都是线程不安全的, `Collections` 提供了多个静态方法可以把他们包装成线程同步的集合。方法如下：
```java
synchronizedCollection(Collection<T>  c) //返回指定 collection 支持的同步（线程安全的）collection。
synchronizedList(List<T> list)//返回指定列表支持的同步（线程安全的）List。
synchronizedMap(Map<K,V> m) //返回由指定映射支持的同步（线程安全的）Map。
synchronizedSet(Set<T> s) //返回指定 set 支持的同步（线程安全的）set。
```
但是最好不要这些方法，效率非常低，需要线程安全的集合类型时请考虑使用 `JUC` 包下的并发集合。


### 无序性和不可重复性的含义是什么?

无序性不等于随机性，无序性是指存储的数据在底层数组中并非按照数组索引的顺序添加，而是根据数据的哈希值决定的。

不可重复性是指添加的元素按照 `equals()` 判断时，返回 `false` ,需要同时重写 `equals()` 方法和`HashCode()` 方法。

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

- `LinkedList` 实现了 `Serializable` 接口，支持序列化。

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

- `HashSet` 、`LinkedHashSet` 和 `TreeSet` 都是 `Set` 接口的实现类，都能保证元素唯一，并且都不是线程安全的。

- `HashSet` 的底层数据结构是哈希表（基于 `HashMap` 实现）。`LinkedHashSet` 的底层数据结构是链表和哈希表，元素的插入和取出顺序满足 `FIFO` (先进先出) 。`TreeSet` 底层数据结构是红黑树，元素是有序的，排序的方式有自然排序和定制排序。

- 底层数据结构不同又导致这三者的应用场景不同。`HashSet` 用于不需要保证元素插入和取出顺序的场景，`LinkedHashSet` 用于保证元素的插入和取出顺序满足 `FIFO` 的场景，`TreeSet` 用于支持对元素自定义排序规则的场景。

### HashSet 的要点？

- `HashSet` 实际上是通过 `HashMap` 实现的。`HashSet` 中维护了一个 `HashMap` 对象 `map`，`HashSet` 的重要方法，如 `add`、`remove`、`iterator`、`clear`、`size` 等都是围绕这个 `map` 实现的。

- `HashSet` 中存储的元素是无序的、散列的。

- `HashSet` 通过继承 `AbstractSet` 实现了 `Set` 接口中的骨干方法。

- `HashSet` 实现了 `Cloneable`，所以支持克隆、实现了 `Serializable`，所以支持序列化。

- `HashSet` 允许 `null` 值的元素，但只能拥有一个 `null` 值，即不允许存储相同的元素, 且不是线程安全的。

比较关键的就是 `add()` 方法。它是将存放的对象当作 `HashMap` 的键，`value` 都是相同的 `PRESENT` 虚值，如源码所示
```java
public boolean add(E e) {
    return map.put(e, PRESENT)==null;// 调用HashMap的put方法,PRESENT是一个至始至终都相同的虚值
}
```
由于 `HashMap` 的 `key` 是不能重复的，所以每当重复的值写入到 `HashSet` 时，`value` 会被覆盖，但 `key`不会受到影响，这样就保证了 `HashSet` 中只能存放不重复的元素。

### LinkedHashSet 的要点？

- `LinkedHashSet` 通过继承 `HashSet` 实现 `Set` 接口中的骨干方法。

- `LinkedHashSet` 实现了 `Cloneable` ，所以支持克隆。

- `LinkedHashSet` 实现了 `Serializable` ，所以支持序列化。

- `LinkedHashSet` 不是线程安全的。

- `LinkedHashSet` 维护了一个双链表。由双链表的特性可以知道，它是按照元素的插入顺序保存的。所以，这就是 `LinkedHashSet` 中存储的元素是按照插入顺序保存的原理。

### TreeSet 的要点？

- `TreeSet` 实际上是通过 `TreeMap` 实现的。`TreeSet` 中的元素是有序的，它是按自然排序或者用户指定比较器（Comparator）中提供的顺序规则排序的 `Set`。

- `TreeSet` 通过继承 `AbstractSet` 实现了 `NavigableSet` 接口中的骨干方法。

- `TreeSet` 实现了 `Cloneable`，所以支持克隆。

- `TreeSet` 实现了 `Serializable`，所以支持序列化。

- `TreeSet` 不是线程安全的。

## Map

### HashMap 和 Hashtable 的区别？

`HashMap` 是非线程安全的，`Hashtable` 是线程安全的,因为 `Hashtable` 内部的方法基本都经过`synchronized` 修饰。因为线程安全的问题，`HashMap` 要比 `Hashtable` 效率高一点。另外，`Hashtable` 基本被淘汰，不要在代码中使用它。

`HashMap` 可以存储 `null` 的 `key` 和 `value`，但 `null` 作为键只能有一个，`null` 作为值可以有多个；`Hashtable` 不允许有 `null` 键和 `null` 值，否则会抛出 `NullPointerException`。

`Hashtable` 创建时如果不指定容量初始值， 默认的初始大小为 `11`，之后每次扩充，容量变为原来的 `2n+1`。`HashMap` 默认的初始化大小为 `16`。之后每次扩充，容量变为原来的 `2` 倍。创建时如果给定了容量初始值，那么 `Hashtable` 会直接使用你给定的大小，而 `HashMap` 会将其扩充为 2 的幂次方大小，具体实现在 `tableSizeFor()` 方法中。 `HashMap` 总是使用 2 的幂作为哈希表的大小。

`HashMap` 底层数据结构是数组+链表/红黑树，当数组的大小大于 `64` 且链表的大小大于 `8` 的时候才会将链表改为红黑树，当红黑树大小为 `6` 时，会退化为链表，这里转红黑树退化为链表的操作主要出于查询和插入时对性能的考量。链表查询时间复杂度 `O(N)`，插入时间复杂度 `O(1)` ，红黑树查询和插入时间复杂度 `O(logN)`。
`Hashtable` 底层数据结构和 `HashMap` 一样，但没有这样的机制。

### HashMap 和 TreeMap 的区别？

`TreeMap` 基于红黑树实现。`TreeMap` 是有序的，但不是线程安全的。

`TreeMap` 和 `HashMap` 都继承自 `AbstractMap` ，但是需要注意的是 `TreeMap` 它还实现了 `NavigableMap` 接口和 `SortedMap` 接口。

实现 `NavigableMap` 接口让 `TreeMap` 有了对集合内元素的搜索的能力。实现 `SortedMap` 接口让 `TreeMap` 有了对集合中的元素根据键排序的能力或使用提供的比较器（Comparator）的自定义比较顺序。

相比于 `HashMap` 来说 `TreeMap` 主要多了对集合中的元素根据键排序的能力以及对集合内元素的搜索的能力。

### HashMap 和 HashSet 的区别？

|            HashMap             |                           HashSet                            |
| :----------------------------: | :----------------------------------------------------------: |
|          实现Map接口           |                         实现Set接口                          |
|           存储键值对           |                          仅存储对象                          |
|    调用put()向map中添加元素    |                 调用add()方法向Set中添加元素                 |
| HashMap使用键(Key)计算Hashcode | HashSet使用成员对象来计算hashcode值，对于两个对象来说hashcode可能相同，所以还需要用equals()方法用来判断对象的相等性，如果两个对象不同的话返回的是false |

### HashMap 在JDK 1.7 和 JDK 1.8 中的区别？

`JDK1.8` 主要解决或优化了一下问题：
1. `resize` 扩容优化
2. 引入了红黑树，目的是避免单条链表过长而影响查询效率。
3. 解决了多线程死循环问题，但仍是非线程安全的，多线程时可能会造成数据丢失问题。

|     不同点     |          JDK1.7 HashMap          |          JDK1.8 HashMap          |
| :------------: | :----------------------------: | :----------------------------: |
|    数据结构    |           数组+链表            |        数组+链表+红黑树        |
|  插入数据方式  |             头插法             |             尾插法             |
| hash值计算方式 | 9次扰动处理(4次位运算+5次异或) | 2次扰动处理(1次位运算+1次异或) |
|    扩容策略    |           插入前扩容           |           插入后扩容           |

### HashMap 的实现原理？

 `HashMap` 是基于哈希表的 `Map` 接口的非同步实现。此实现提供所有可选的映射操作，并允许使用 `null` 值和 `null` 键。此类不保证映射的顺序，特别是它不保证该顺序恒久不变。在Java中，保存数据有两种比较简单的数据结构：数组和链表。数组的特点是：**寻址容易，插入和删除困难**；链表的特点是：**寻址困难，但插入和删除容易**；所以我们将**数组和链表结合在一起**，发挥两者各自的优势，使用一种叫做拉链法的方式可以解决哈希冲突。

JDK1.8之前采用的是拉链法。拉链法：将链表和数组相结合。也就是说创建一个链表数组，数组中每一
格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可。

JDK1.8之后相比于之前的版本，jdk1.8在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为8）时，将
链表转化为红黑树，以减少搜索时间。至于大于某一阈值才转化是由于，红黑树需要进行左旋，右旋，变色这些操作来保持平衡，而单链表不需要。当元素小于 `8` 个的时候，此时做查询操作，链表结构已经能保证查询性能。当元素大于 `8` 个的时候， 红黑树搜索时间复杂度是 `O(logn)`，而链表是 `O(n)`，此时需要红黑树来加快查询速度，但是新增节点的效率变慢了。

### HashMap 默认加载因子为什么是 0.75？

默认负载因子 `0.75`,在时间和空间成本上提供了很好的折衷。较高的值会降低空间开销，但提高查找成本（体现在大多数的HashMap类的操作，包括get和put）。一般不推荐修改，除非在时间和空间比较特殊的情况下：

如果内存空间很多而又对时间效率要求很高，可以降低负载因子 `loadfactor` 的值 。

相反，如果内存空间紧张而对时间效率要求不高，可以增加负载因子 `loadFactor` 的值，这个值可以大于1。

### HashMap 的长度为什么是 2 的幂次方？

为了加快哈希计算以及减少哈希冲突，换句话说就是存取高效，尽量较少碰撞，尽量把数据分配均匀。

`Hash` 值的取值范围 `-2147483648` 到 `2147483647`，总共有  `40+` 亿个映射空间，只要哈希函数映射的比较均匀，一般应用很难出现碰撞，但是内存肯定不能一次加载这么长的数组，所以这个散列值是不能拿来直接用的，我们只能创建合理长度的数组作为哈希表，在插入数据之前做取模运算，得到的余数就是将要存放的数据在哈希表中对应的下标。在 `HashMap` 中这个下标的取值算法是：`(n - 1) & hash`; `n` 是哈希表的长度。

取模运算中如果除数是2的幂次方则等价于 其与除数减一的&操作，就是：`hash % length == hash & (length - 1)`
采用二进制位操作 `&` 相对于 `%` 能够提高运算效率，这也就解释了为啥 `HashMap` 的长度需要为 `2` 的幂次方

### 为什么HashMap中String、Integer这样的包装类适合作为Key？

`String` 、`Integer` 等包装类的特性能够保证 `Hash` 值的不可更改性和计算准确性，能够有效的减少
`Hash` 碰撞的几率。

都是 `final` 类型，即不可变性，保证 `key` 的不可更改性，不会存在获取 `hash` 值不同的情况。

内部已重写了 `equals()`、`hashCode()` 等方法，遵守了 `HashMap` 内部的规范，不容易出现 `Hash` 值计算错误的情况。

### HashMap 常用的遍历方式以及性能问题？

[HashMap 的 7 种遍历方式与性能分析！](https://mp.weixin.qq.com/s/zQBN3UvJDhRTKP6SzcZFKw)