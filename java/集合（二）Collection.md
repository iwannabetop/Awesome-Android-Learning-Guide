
前面我们讲到了集合的定义以及集合的 Iterator。我们知道集合分为 Collection和 Map，今天我们的重点是学习 Collection。

# 什么是集合
我们再来回顾一下集合解决了什么问题：*在编译期间不知道要多少个对象，但是数组必须在申明的时候明确指明数组长度，如果使用数组，申请太多的空间就会造成资源浪费，如果申请太少空间，就不够用，所以引出了一个概念叫“集合”。*

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


#集合的抽象实现类 AbstractCollection
AbstractCollection是所有 Collection 系集合的父类。从类结构上来看，List、Set 系列的集合都继承自 AbstractCollection。

AbstractCollection 没有太多的代码，主要实现了 Collection 接口方法。其实这个类设计还是蛮有意思的。仅仅抽象了两个方法iterator()和size()，其余所有的操作都在AbstractCollection 里面实现了，在数据结构都不清楚的情况下，仅仅通过 iterator 就实现了对集合的增删改查。如果是我们自己来设计一个集合，能否在不清楚数据结构的情况下，就写出对集合的增删改查方法的具体实现呢？我想这就是面向对象编程 OOP 的魅力所在吧。

# 预告
接下来我会根据 List、Set、Queue 三个 Collection 接口的继承接口来学习 Collection 集合的各种实现。

- List 基于数组实现的集合类，元素有序可重复
- Set 基于 HashMap 实现的集合类，无序且不能重复
- Queue 基于数组实现的集合类，模拟队列，遵循 FIFO


