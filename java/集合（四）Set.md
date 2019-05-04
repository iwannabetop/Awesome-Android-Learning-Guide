
# Set 接口

前面我们学了 List 集合。我们知道 List 是一个有序的集合，可以根据元素的整数索引访问元素，并且允许重复。

今天我们一起来学习集合的第二大类型---Set 集合。Set 也是一个接口，继承自 Collection 接口。

>定义：一个不包含重复元素的 Collection。什么叫重复元素？set 的定义是不包含满足 e1.equals(e2)的元素对 e1 和 e2，并且最多只包含一个 null 元素。

![](https://user-gold-cdn.xitu.io/2017/9/26/fdb74253020a3029b6b3fdca1ef042dd)

从图上我们可以看到，Set 接口并没有针对 Collection 做任何扩展，这里的接口方法我们就不再赘述了。

# Set 的抽象实现类 AbstractSet
AbstractSet 和 AbstractList 一样，都继承自 AbstractCollection 类，并且实现了各自的行为接口，AbstractSet 实现的是 Set。
AbstractSet 就如下几个方法，我们来看一下。

- AbstractSet()构造方法
- equals 方法。Set 集合的 equals(e)只有当 e 是自身，或者是一个Collection 并且满足containsAll(e)才会返回 true
- hashCode Set 集合的 hashCode 值是集合中所有非空元素的hashCode 累加和。
- removeAll 操作 Iterator，实现了清空集合的操作。
	
可见 AbstractSet 类并没有什么特别的属性和能力，因此我们直接看具体实现类吧。

# Set 的具体实现类

## HashSet

我们来看看 Java API 中对 HashSet 的定义。
>此类实现 Set 接口，由哈希表（实际上是一个 HashMap 实例）支持。它不保证 Set 的迭代顺序；特别是它不保证该顺序恒久不变。此类允许使用 null 元素。
>此实现不是同步的。

特么的，API 中两句话将 HashSet 的特性讲完了。

为了增加点篇幅，我再总结一下 HashSet 的特性吧

- 无序：为什么是无序的？这个问题我不会答，因为并没有对 Set 里面的元素进行排序啊啊啊啊啊啊啊。List 里面的数据之所以有序是因为用了 数组\链表 这两种有序的数据结构。那么 HashSet 用的是什么数据结构呢？ 上面说了 HashSet 是基于哈希表（实际上是一个 HashMap 实例）支持。可能有些同学又会问了，HashMap 是什么数据结构，为什么无序？这个，我们下次分享的时候再说，同学们可以提前了解一下散列表（Java 中叫哈希表）。
- 不能包含重复的元素：为什么不能？刚刚我们说了，由哈希表（实际上是一个 HashMap 实例）支持的元素存储。我们都知道 HashMap 是键值对的形式存在的，并且一个键只能对应一个值。然后。。。我们还是来看看 HashSet 源码吧~

	  public boolean add(E var1) {
	    return this.map.put(var1, PRESENT) == null;
	  }
	
上面就是 HashSet 存储对象的方法，我们可以看到，HashSet 存储的对象都被作为 HashMap 的 K 值保存到了 HashMap 中。我们都知道 HashMap 不允许有重复的 K 值，所以，就保证了 HashSet 存储的唯一性。至于上面的 PRESENT ，我们完全不用 care，我就不告诉你 PRESENT 是什么东西，强迫症可以去源码里面看看 PRESENT 是什么。略略略~~

好了，HashSet 讲完了，基于 HashMap 的实现。HashMap 我们下次再一起学习~

对了，学 HashMap 一定要记得先去了解“散列表”。

## TreeSet
来吧，看 API 定义。

>基于 TreeMap 的 NavigableSet 实现。使用元素的自然顺序对元素进行排序，或者根据创建set 时提供的 Comparator 进行排序，具体取决于使用的构造方法。

好吧，我知道了，TreeSet 相比 HashSet 的区别就是有序。

怎样实现的有序？刚刚 API 文档中说了呀，基于 TreeMap 的实现呀。

回过头来思考一下，HashSet 的存储实现，HashSet 的存储是把对象作为 K 值保存在 HashMap 中，且无序。则可得知 HashMap 的 K 值是无序的。

我们来看看 TreeMap 的 API 文档。

>基于红黑树（Red-Black tree）的 NavigableMap 实现。该映射根据其键的自然顺序进行排序，或者根据创建映射时提供的 Comparator 进行排序，具体取决于使用的构造方法。

好像讲了一大堆看不懂的东西，什么红黑树什么 NavigableMap，都没听说过，有木有。不过没关系，我们的重点是这句话“***该映射根据其键的自然顺序进行排序***”，可以知道 TreeMap 的 K 值是有序的呀。

好了，找到根源了，其实 TreeMap 也没什么特点，红黑树是二叉树的一种优化，TreeMap
后面在 Map 集合中也会单独讲。

好了，TreeSet 就介绍到这里吧。TreeSet 是基于 TreeMap 实现的有序 Set 集合，要实现 TreeSet 有序有两种方式：1.Comparable 接口使元素具有自然顺序。2.使用Comparator 比较器排序。

## LinkedHashSet

我们来看名字推测一下 LinkedHashSet 的特征。

- Linked：基于链表实现。链表有什么特点？有先后顺序呀~
- HashSet：不允许元素重复的 Set 集合。

总结：对于存储有先后顺序的 HashSet。

好了，经过思考后，我们去看 API 文档验证一下我们的猜测。

>具有可预知迭代顺序的 Set 接口的哈希表和链接列表实现。此实现与 HashSet 的不同之外在于，后者维护着一个运行于所有条目的双重链接列表。此链接列表定义了迭代顺序，即按照将元素插入到 set 中的顺序（插入顺序）进行迭代。

mmp，这个API 竟然说维护着运行于所有条目的双重链接列表，为什么不和前面一样，基于“LinkedHashMap 的双重链接表实现”~~~

#### LinkedHashMap
>Map 接口的哈希表和链接列表实现，具有可预知的迭代顺序。此实现与 HashMap 的不同之处在于，后者维护着一个运行于所有条目的双重链接列表。此链接列表定义了迭代顺序，该迭代顺序通常就是将键插入到映射中的顺序（插入顺序）。

没什么意思，都是基于 Map 做的实现。

对了，提一句，这里 LinkedHashSet 的有序和 TreeSet 的有序不是一个概念，LinkedHashSet 指插入的元素按照先后顺序，TreeSet 指插入的元素按照一定的比较顺序插入某个固定的位置。


# 有话说
其实我一直都在纠结是先学 Set还是先学 Map，毕竟 Set 中几个大类都是基于 Map 的实现，可能会有很多原理看不懂。后面想了想，我们的学习过程中，没必要（其实是很难）一开始就把所有的东西都弄懂，先把整个流程捋顺，再去一步一步研究每一个细节。带着问题去学习~~




