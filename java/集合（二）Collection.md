
前面我们讲到了集合的定义以及集合的 Iterator。我们知道集合分为 Collection和 Map，今天我们的重点是学习 Collection。

# 什么是集合
我们再来回顾一下集合解决了什么问题：*在编译期间不知道要多少个对象，例如数组必须在申明的时候明确指明数组长度，如果使用数组，申请太多的空间就会造成资源浪费，如果申请太少空间，就不够用，所以引出了一个概念叫“集合”。*

从这里我们可以看出，集合的特性有：

- 类似于数组，包含了多个元素的对象
- 可根据需要动态申请内存空间。

因此，我们可以理解集合是一个高级的数组。可能这种说法不太合理，那我换个说法吧，集合是许多个体组织成的一个整体。

既然是整体，那么肯定有增删改查等各种操作，我们前面在面向对象的理解中讲了接口的理解：*接口就是对一个对象的行为进行规范，使对象具有做某些事情的能力*。集合本身只是用来存储数据的，如果需要具有增删改查功能，肯定是用接口来扩展。不多说了，我们来看代码。


![](https://user-gold-cdn.xitu.io/2017/9/25/84c590ccc84e9b5a6e3c90acc9e0031c)

不看代码了，Collection 就是一个接口，定义了集合的行为规范，我们直接看类结构图就好。

- int size(); 获取元素个数
- boolean isEmpty();是否为空
- boolean contains(Object var1);是否包含指定元素
- Iterator<E> iterator();获取迭代器
- Object[] toArray();转换成数组
- <T> T[] toArray(T[] var1);转换成数组，指定类型
- boolean add(E var1);添加一个元素
- boolean remove(Object var1);删除一个元素
- boolean containsAll(Collection<?> var1);是否包含一组元素
- boolean addAll(Collection<? extends E> var1);添加一组元素
- boolean removeAll(Collection<?> var1);删除一组元素
- default boolean removeIf(Predicate<? super E> var1) 模式实现方法，删除指定条件元素
- boolean retainAll(Collection<?> var1);保留本集合中 c 集合中两者共有的，如果集合有改变就返回 true
- void clear();清空集合
- boolean equals(Object var1);比较
- int hashCode();获取哈希值
- default Spliterator<E> spliterator() 获取分割迭代器，并行操作
- default Stream<E> stream()   [Java 8流式操作](http://blog.csdn.net/u010425776/article/details/52344425)
- default Stream<E> parallelStream() 同上


# 集合的抽象实现类 AbstractCollection
AbstractCollection是所有 Collection 系集合的父类。从类结构上来看，List、Set 系列的集合都继承自 AbstractCollection。

AbstractCollection 没有太多的代码，主要实现了 Collection 接口方法。其实这个类设计还是蛮有意思的。仅仅抽象了两个方法iterator()和size()，其余所有的操作都在AbstractCollection 里面实现了，在数据结构都不清楚的情况下，仅仅通过 iterator 就实现了对集合的增删改查。如果是我们自己来设计一个集合，能否在不清楚数据结构的情况下，就写出对集合的增删改查方法的具体实现呢？我想这就是面向对象编程 OOP 的魅力所在吧。

# 预告
接下来我会根据 List、Set、Queue 三个 Collection 接口的继承接口来学习 Collection 集合的各种实现。

- List 基于数组实现的集合类，元素有序可重复
- Set 基于 HashMap 实现的集合类，无序且不能重复
- Queue 基于数组实现的集合类，模拟队列，遵循 FIFO

----
补充：

# Collections 和 Arrays

### Collections

> 此类完全由在 collection 上进行操作或返回 collection 的静态方法组成。它包含在 collection 上操作的多态算法，即“包装器”，包装器返回由指定 collection 支持的新 collection，以及少数其他内容。

好了，就是一个工具类，里面的方法都是静态方法，这里了解一下有哪些方法即可，如果代码中用到，可以简化部分代码。

方法名|方法描述
----|----
addAll(Collection c,T elements)|将所有指定元素添加到指定 collection 中
asLifoQueue(Deque deque)|以后进先出 (Lifo) Queue 的形式返回某个 Deque 的视图
binarySearch(List<? extends Comparable<? super T>>list,T key)|使用二分搜索法搜索指定列表，以获得指定对象。
copy(List<? super T> dest, List<? extends T> src)|将所有元素从一个列表复制到另一个列表
disjoint(Collection<?> c1, Collection<?> c2) |如果两个指定 collection 中没有相同的元素，则返回 true
fill(List<? super T> list, T obj)|使用指定元素替换指定列表中的所有元素
frequency(Collection<?> c, Object o) |返回指定 collection 中等于指定对象的元素数
indexOfSubList(List<?> source, List<?> target)|返回指定源列表中第一次出现指定目标列表的起始位置；如果没有出现这样的列表，则返回 -1
lastIndexOfSubList(List<?> source, List<?> target)|同上
max(Collection<? extends T> coll)|根据元素的自然顺序，返回给定 collection 的最大元素
max(Collection<? extends T> coll, Comparator<? super T> comp)|同上
min(Collection<? extends T> coll)| 同上
newSetFromMap(Map<E,Boolean> map)|返回指定映射支持的 set
replaceAll(List<T> list, T oldVal, T newVal)|使用另一个值替换列表中出现的所有某一指定值
reverse(List<?> list)|反转指定列表中元素的顺序
sort(List<T> list) | 根据元素的自然顺序 对指定列表按升序进行排序
swap(List<?> list, int i, int j) |在指定列表的指定位置处交换元素
synchronized\*\*\*(\*\*\*<T> c)|返回指定集合且支持同步（线程安全的）\*\*\*

### Arrays
> 此类包含用来操作数组（比如排序和搜索）的各种方法。此类还包含一个允许将数组作为列表来查看的静态工厂。

同上，这也是一个对数组操作的工具类

方法名|方法描述
----|----
asList(T... a) | 返回一个受指定数组支持的固定大小的列表。

binarySearch(E[] a, E key) | 使用二分搜索法来搜索指定的 E 型数组，以获得指定的值
copyOf(E[] original, int newLength)复制指定的数组，截取或用 默认值 填充（如有必要），以使副本具有指定的长度
copyOfRange(E[] original, int from, int to) |将指定数组的指定范围复制到一个新数组
deepEquals(Object[] a1, Object[] a2)|如果两个指定数组彼此是深层相等 的，则返回 true
equals(E[] a, E[] a2) |如果两个指定的 E 型数组彼此相等，则返回 true
fill(E[] a, E val)|将指定的 boolean 值分配给指定 boolean 型数组的每个元素
hashCode(E[] a)|基于指定数组的内容返回哈希码
sort(E[] a) | 对指定的 byte 型数组按数字升序进行排序
toString(E[] a)|返回指定数组内容的字符串表示形式          
          
***其中，E 是基本数据类型***         
          
          

