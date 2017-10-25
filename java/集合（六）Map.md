

# Map

我们都知道 Map 是键值对关系的集合，并且键唯一，键一对一对应值。

关于 Map 的定义，大概就这些吧，API 文档的定义也是酱紫。

照惯例，我们来看类结构图吧~~

![](https://user-gold-cdn.xitu.io/2017/10/18/08defcae996cbe2351534ae33d7fc989)

都是一些行为控制的方法，用过 Map 集合的我们都熟悉这些方法，我就不做过多的赘述了，这里我们重点来看看嵌套类 Map.Entry

### Map.Entry<K,V>

>定义：映射项（键-值对）。Map.entrySet 方法返回映射的 collection 视图，其中的元素属于此类。获得映射项引用的唯一 方法是通过此 collection 视图的迭代器来实现。这些 Map.Entry 对象仅 在迭代期间有效；更确切地讲，如果在迭代器返回项之后修改了底层映射，则某些映射项的行为是不确定的，除了通过 setValue 在映射项上执行操作之外。

这里我们可以看到 Map 的泛型K,V也给 Map.Entry用了，然后根据定义，我们可以大胆的猜测这个 Entry 就是用来存放 K,V 等关键信息的实体类。

我们来看看 Map.Entry 定义的方法


方法名|用途
----|----
equals(Object o)|比较指定对象与此项的相等性
getKey()|返回与此项对应的键
getValue()|返回与此项对应的值
hashCode()|返回此映射的哈希码值
setValue()|用指定的值替换与此项对应的值

这里的5个方法，除了 hashCode 之外，都能顾名思义。那么问题来了，我们这里的实体类为什么要引入 hashCode 的概念呢~~这里我们要先学习一种数据结构---散列表。

# Map 的抽象实现类 AbstractMap
此类提供 Map 接口的骨干实现，以最大限度地减少实现此接口所需的工作。

要实现不可修改的映射，编程人员只需扩展此类并提供 entrySet 方法的实现即可，该方法将返回映射的映射关系 set 视图。通常，返回的 set 将依次在 AbstractSet 上实现。此 set 不支持 add 或 remove 方法，其迭代器也不支持 remove 方法。

要实现可修改的映射，编程人员必须另外重写此类的 put 方法（否则将抛出 UnsupportedOperationException），entrySet().iterator() 返回的迭代器也必须另外实现其 remove 方法。

按照 Map 接口规范中的建议，编程人员通常应该提供一个 void（无参数）构造方法和 map 构造方法。

此类中每个非抽象方法的文档详细描述了其实现。如果要实现的映射允许更有效的实现，则可以重写所有这些方法。

# HashMap

这个是最正宗的基于哈希表的 Map 接口实现。此实现提供所有可选的映射操作，并允许使用 null 值和 null 键。

HashMap 的实例有两个参数影响其性能：初始容量 和加载因子。容量 是哈希表中bucketIndex（桶）的数量，初始容量只是哈希表在创建时的容量。加载因子 是哈希表在其容量自动增加之前可以达到多满的一种尺度。当哈希表中的条目数超出了加载因子与当前容量的乘积时，则要对该哈希表进行 rehash 操作（即重建内部数据结构），从而哈希表将具有大约两倍的桶数。

通常，默认加载因子 (0.75) 在时间和空间成本上寻求一种折衷。加载因子过高虽然减少了空间开销，但同时也增加了查询成本（在大多数 HashMap 类的操作中，包括 get 和 put 操作，都反映了这一点）。在设置初始容量时应该考虑到映射中所需的条目数及其加载因子，以便最大限度地减少 rehash 操作次数。如果初始容量大于最大条目数除以加载因子，则不会发生 rehash 操作。

**注意，此实现不是同步的**。如果多个线程同时访问一个哈希映射，而其中至少一个线程从结构上修改了该映射，则它必须 保持外部同步。（结构上的修改是指添加或删除一个或多个映射关系的任何操作；仅改变与实例已经包含的键关联的值不是结构上的修改。）这一般通过对自然封装该映射的对象进行同步操作来完成。如果不存在这样的对象，则应该使用 Collections.synchronizedMap 方法来“包装”该映射。最好在创建时完成这一操作，以防止对映射进行意外的非同步访问，如下所示：

   	Map m = Collections.synchronizedMap(new HashMap(...));
   	
我们先了解以下三个名词，如果还不懂的话也没事，我们一起手撸一个 HashMap就是了。

### 散列表

散列表，又名哈希表（Hash table）,是根据关键码值（Key value）而进行访问的数据结构，也就是说，它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做散列函数，存放记录的数组叫做散列表

给定表M，存在函数f(key)，对任意给定的关键字值key，代入函数后若能得到包含该关键字的记录在表中的地址，则称表M为哈希(Hash）表，函数f(key)为哈希(Hash) 函数。

说起来可能有点抽象，我给大家举个栗子吧~~
对于一个具有 n 个元素的数组，我们需要找到其中某一个值的时间复杂度是 O(n)。现在我们使用散列表来实现，要获取一个元素“vaule”,我们可以通过该元素的 Key 值“key”来获取“value”在数组中存放的位置，然后直接获取到我们需要的元素，则获取的时间复杂度是 O(1)。那么问题是，怎么通过“key”来获取呢，上面的定义有说到。把关键词“key”代入到哈希函数里面计算，计算的结果就是“value”存放的位置。key 是个泛型，要使不同类型的数据都能带入到哈希函数里面进行计算，这里我们用的是对象的 hashCode 值，hashCode 值的定义这里不过多赘述，大家记得 hashCode 是 Object 的方法即可。

看到这里如果还不明白的话，那我就只能讲自己的理解了：就是通过**哈希函数**计算一个 **Key** 的 **hash** 值，得到一个**bucketIndex(可以理解为数组角标)**，把 **Value** 存放到这个**bucketIndex**对应的位置。下次再取这个 Vaule 的时候只需把 key 的 hash代入到哈希函数里面进行计算得到 Value 的位置即可。

### 键唯一
上一篇我们分析 HashSet 源码怎么实现集合元素不重复的时候，我挖了一个坑，现在来把这个坑给填上吧。

要比较两个元素是否相等，这个在 java 里面似乎是一个比较简单的问题，但是要把==，equals 和 hashcode 牵扯进来，好像又有点讲不清楚。

- 首先我们来区分“==”和 equals 的区别

"=="在比较基本数据类型的时候比较的是值是否相等，在比较对象变量的时候比较的是两个变量是否指向同一个对象。equals Object 的方法，用于比较两个对象内容是否相等。默认是用==做比较，如果重写则单独处理。

- hashCode 的约定
	- 在 Java 应用程序执行期间，在对同一对象多次调用 hashCode 方法时，必须一致地返回相同的整数，前提是将对象进行 equals 比较时所用的信息没有被修改。从某一应用程序的一次执行到同一应用程序的另一次执行，该整数无需保持一致。
	- 如果根据 equals(Object) 方法，两个对象是相等的，那么对这两个对象中的每个对象调用 hashCode 方法都必须生成相同的整数结果。
	- 如果根据 equals(java.lang.Object) 方法，两个对象不相等，那么对这两个对象中的任一对象上调用 hashCode 方法不 要求一定生成不同的整数结果。但是，程序员应该意识到，为不相等的对象生成不同整数结果可以提高哈希表的性能。 

好，看完这些，我们可以知道，如果两个对象相等，那么hashCode 的值必然相等；如果两个对象不相等，hashCode 的值也有可能相等，只是两个不相等的对象 hashCode 值相等的话，哈希表的性能会下降。

好了，看到这里我们可以根据上面的结论，先获取是否有 hash 相同的 key，如果有，再执行==和 equals 操作比较，如果没有。。。那就算键唯一。

### 键冲突
刚刚我们在保证键唯一的时候有一个这样的问题不知道大家注意到了没，就是不同的对象，其 hashCode的值是有可能相等的，那么当两个不同的 key 存入 map，而正好其key 的 hash 值相等，那该怎么办？

这个其实是属于散列表的问题，但是散列表牵扯到的东西太多，所以刚刚我没有讲。但是我们刚刚在保持**键唯一**的时候又碰到了这个问题，那么我们来简单讲一下吧。

刚刚我们在讲散列表数据结构的时候已经说了，其实散列表的数据存储就是一个数组，哈希函数根据 key计算出来的 hash 值就是 value 的存放位置，而 value 则是一个Map.Entry 的具体实现类，Java 的 HashMap 在这里用了“拉链法”来键冲突。什么是拉链法呢？就是让 value 变成一个链表，添加一个指向下一个元素的引用。

也就是说，map 的数据存储其实就是一个数组，当我们插入一组 K,V 的时候，用 K 的 hashcode 经过 hash 函数计算得出 V 存放的位置 bucketIndex，然后如果 数组[bucketIndex]没有元素，则创建 Entry 赋值给 数组[bucketIndex]。如果有1或多个元素（多个元素以链表的形式），则遍历比较各组元素的key 是否和插入 key 相等，如果相等则覆盖 value，否则new 一个 Entry 插入到表头。



# HashTable
继承自 Dictionary 类，实现了 Map 接口。

>Dictionary 类是任何可将键映射到相应值的类（如 Hashtable）的抽象父类。每个键和每个值都是一个对象。在任何一个 Dictionary 对象中，每个键至多与一个值相关联。给定一个 Dictionary 和一个键，就可以查找所关联的元素。任何非 null 对象都可以用作键或值。

基本上不会再使用的类，K、V 都不能为 null，支持同步算是唯一的优点了吧。但是 Java 推荐我们使用 Collections.synchronized*** 对各种集合进行了同步的封装，所以基本废弃。


# TreeMap

我们先来看看类注释说明~

>A Red-Black tree based NavigableMap implementation. The map is sorted according to the Comparable natural ordering of its keys, or by a Comparator provided at map creation time, depending on which constructor is used.

TreeMap 是 Map 接口基于红黑树的实现，键值对是有序的。TreeMap 的排序方式基于Key的Comparable 接口的比较，或者在构造方法中传入一个Comparator.

>This implementation provides guaranteed log(n) time cost for the {@code containsKey}, {@code get}, {@code put} and {@code remove} operations.  Algorithms are adaptations of those in Cormen, Leiserson, and Rivest's <em>Introduction to Algorithms</em>.

TreeMap提供时间复杂度为log(n)的containsKey，get，put ，remove操作。

和 HashMap 的区别？

- TreeMap的元素是有序的，HashMap的元素是无序的。
- TreeMap的键值不能为 null。

大致的特性就这些吧，关键就是掌握红黑树。

说回来，TreeMap 就是Java 的红黑树实现啊~~

### 红黑树

红黑树是一种自平衡二叉查找树，是一种比较特别的数据结构。

红黑树和 AVL 树类似，都是在进行插入和删除操作保持二叉查找树的平衡，从而活得较高的查找性能。

红黑树虽然负责，但是它最坏的运行时间也是非常良好的，并且在实践中是高效的，它可以在 O(lon n)时间内做查找、插入和删除，这里的 n 指树种元素的数目。

额、如果对树以及二叉树不了解的同学可以跳过这一段。

**五大特性**

- 节点是红色或者黑色
- 根是黑色
- 所有的叶子都是黑色的（叶子是 NIL 节点）
- 每个红色节点的两个子节点都是黑色（从每个叶子到根的所有路径上不能有两个连续的红色节点）
- 从任一节点到其每个叶子的所有简单路径 都包含相同数目的黑色节点。

我们来对着下面这张图来理解一下这五大特征。

![](https://user-gold-cdn.xitu.io/2017/10/18/55f60f91d3b49456dfb65662ed8541d5)

1.节点都是红色或者黑色——没毛病
2.根节点是黑色——没毛病
3.所有的叶子节点都是黑色——可以理解成最外层的节点都会有一个 NIL 的黑色节点，实际数据结构中就是 null
4.每个红色节点的两个子节点都是黑色——注意不能反过来，这是为了保证不会有两个连续的红色节点。
5.保证黑色节点数相同，也就是保证了从根节点到叶子节点最短的路径*2>=最长的路径

**树的旋转**
当我们在堆红黑树进行插入和删除等操作时，对树做了修改，那么可能会违背红黑树的性质。

为了保持红黑树的性质，我们可以通过对树进行旋转操作，即修改树种某些节点的颜色及指针结构，已达到对红黑树进行插入、删除节点等操作时，红黑树依然能保持它特有的性质。

说白了，就是增删节点的时候会破坏树的性质，所以通过旋转来保持。

怎样旋转？为什么旋转可以保持红黑树性质？
这个~~旋转是一门玄学，一般看运气才能正确的做出旋转操作。
至于为什么旋转可以保持树的性质，这个……你可以暂且把这个旋转理解成是一个“定理”吧。
> 定理：是经过受逻辑限制的证明为真的陈述

好了，别纠结了，你记住旋转可以保持树的性质就行了。

**树的插入**
树的插入很简单，只能插入到叶子节点上（如果插入的 key 在树上已存在，则覆盖 Value），根据左节点小又节点大的性质，找到对应的叶节点插入即可，注意插入的节点默认只能是红色。如果插入的叶子节点的父节点是红色，则违背了特性4，这时候就需要通过旋转来稳定树的性质。

**怎样旋转**

旋转分了左右旋转，左旋操作有如下几步
1.把跟节点 a 的右孩子 c 作为根节点
2.把旧的根节点 a 作为新的根节点 c 的左孩子
3.把新的跟节点 c 的左孩子作为 a 节点的右孩子

右旋就简单了，把上面步骤的左右对调就行了。

好了，旋转就这样，很简单，但是一定要用纸和笔在纸上画一画才能掌握哦~~

**什么时候旋转？做什么旋转？**

mmp，这个问题我操蛋了好久好久，我怎么知道怎样旋转。去搜别人写的 blog 。。。。。

然后搜到了各种图解，看了几篇还是半懂不懂的，索性自己去分析源码。。。。

在 Treemap 里面 put 插入了一个节点之后有个fixAfterInsertion()操作。看名字我们就知道是插入后修复。

源码就不带着大家一起读了，后面我手撸 RBTree的时候会一步一步讲解。

我用文字表述一下fixAfterInsertion(x)里面的逻辑。

首先把 x 节点设为红色，这里的 x 节点就是新插入的节点。

当 x 节点不为空，x 节点不为跟节点，x 的父节点是红色的时候，while 以下操作。

敲黑板，这里逻辑比较复杂，里面各种 if else 逻辑，注意了！！！

为了避免有人说我吓唬小朋友，我还是贴一下代码吧~~

	while (x != null && x != root && x.parent.color == RED) {
            if (parentOf(x) == leftOf(parentOf(parentOf(x)))) {
                TreeMapEntry<K,V> y = rightOf(parentOf(parentOf(x)));
                if (colorOf(y) == RED) {
                    setColor(parentOf(x), BLACK);
                    setColor(y, BLACK);
                    setColor(parentOf(parentOf(x)), RED);
                    x = parentOf(parentOf(x));
                } else {
                    if (x == rightOf(parentOf(x))) {
                        x = parentOf(x);
                        rotateLeft(x);
                    }
                    setColor(parentOf(x), BLACK);
                    setColor(parentOf(parentOf(x)), RED);
                    rotateRight(parentOf(parentOf(x)));
                }
            } else {
                TreeMapEntry<K,V> y = leftOf(parentOf(parentOf(x)));
                if (colorOf(y) == RED) {
                    setColor(parentOf(x), BLACK);
                    setColor(y, BLACK);
                    setColor(parentOf(parentOf(x)), RED);
                    x = parentOf(parentOf(x));
                } else {
                    if (x == leftOf(parentOf(x))) {
                        x = parentOf(x);
                        rotateRight(x);
                    }
                    setColor(parentOf(x), BLACK);
                    setColor(parentOf(parentOf(x)), RED);
                    rotateLeft(parentOf(parentOf(x)));
                }
            }
        }

逻辑还是蛮复杂的，其实总结清楚了之后，就只有以下三种情况。

- case 1：当前节点的父节点是红色，叔叔节点（这个名词能理解吧，不能理解我也没办法了）也是红色。
	1. 将父节点设为黑色
	2. 将叔叔节点设为黑色
	3. 将祖父节点设为红色
	4. 将祖父节点设为当前节点，重新判断 case
- case 2：当前节点的父节点是红色，叔叔节点是黑色，且当前节点是父节点的右孩子
	1. 如果当前节点是右孩子，将当前节点指向父节点，并自身左旋 
	2. 设置父节点黑色，爷爷节点红色
	3. 以爷爷节点为支点右旋，重新判断 case
- case 3：当前节点的父节点是红色，叔叔节点是黑色，且当前节点是父节点的左孩子
	1. 如果当前节点是左孩子，将当前节点指向父节点，并自身右旋 
	2. 设置父节点黑色，爷爷节点红色
	3. 以爷爷节点为支点左旋，重新判断 case
- case 4：啥？不是只有3种情况么？当前节点的父节点是黑色
	1. 不用修复，树稳定。

**树的删除操作**
这他喵的也是一段很闹腾的代码。

删除就是根据 key 找到对应的节点，并执行删除操作，删除之后为了保证树的稳定性，需要根据节点的一些属性进行调整。

这里主要处理两个问题：

- 如何删除
- 删除后如何调整

删除节点分三种情况
1.没有儿子的节点
直接删除即可
2.有一个儿子的节点，把父节点的儿子指针指向自己的儿子，再删除即可（就像链表中删除一个元素）
3.有两个儿子的节点，首先找到右儿子最左边的叶节点或者左儿子最右边的叶子节点a，再把 a 赋值给自己，然后删除 a 节点。

这个比较容易，应该能理解吧。。。。。。理解不了自己去画画图~

删除后如何修复，我们再来根据刚刚删除节点的三种情况分析

1.1 没有儿子的节点，且当前节点为红色。直接删除即可，不影响树的性质
1.2 没有儿子的节点，且当前节点为黑色。执行删除后修复操作，传参是被删除的节点
2.1 有一个儿子节点，且当前节点为红色。直接删除即可，不影响树的性质
2.2 有一个儿子节点，且当前节点为黑色。执行删除后修复操作，传参是被删除节点的子节点
3.1 有两个儿子节点，且找到的后备叶子节点是红色。直接删除即可，不影响树的性质
3.2 有两个儿子节点，且找到的后备叶子节点是黑色。执行删除后修复操作，传参是被删除的叶子节点

好了，删除的逻辑我们看完了，反正就是传一个指定的节点 x 到fixAfterDeletion(x)到这个方法里面执行修复操作。

接下来，我们就来看看怎样修复吧~
已知修复是根据传参的节点来判断的，然后里面也有很多 if else 等语句，逻辑和插入修复差不多，也很复杂。这里我先给大家总结一下方法里面的逻辑 case

- case 1：x 的兄弟节点是红色
	1. 将 x 的兄弟节点设为黑色
	2. 将 x 的父节点设为红色
	3. 以 x 的父节点为支点进行左旋
	4. 后续逻辑为 case 2、3、4随机一种
- case 2：x 的兄弟节点是黑色，且兄弟节点的两个孩子都是黑色
	1. 将 x 的兄弟节点设为红色
	2. x 指向 x 的父节点，继续 while 循环
- case 3：x 的兄弟节点是黑色，且兄弟的左孩子是红色、右孩子是黑色
	1. 将 x 的兄弟节点设为红色
	2. 将 x 的兄弟节点左孩子设为黑色
	3. 以 x 的兄弟节点为支点进行右旋
	4. 执行 case 4
- case 4：x 的兄弟节点是黑色，且兄弟的左孩子是黑色、右孩子是红色
	1. 将 x 的父节点颜色赋值给 x 的兄弟节点颜色
	2. 将 x 的父节点设为黑色
	3. 将 x 的右孩子设为黑色
	4. 以 x 的父节点为支点进行左旋
- case 5：如果 x 是左孩子，以上4个 case 的操作均没毛病，如果 x 的右孩子，以上左右取反。


	
		private void fixAfterDeletion(Entry<K,V> x) {  
        // 删除节点需要一直迭代，知道 直到 x 不是根节点，且 x 的颜色是黑色  
        while (x != root && colorOf(x) == BLACK) {  
            if (x == leftOf(parentOf(x))) {      //若X节点为左节点  
                //获取其兄弟节点  
                Entry<K,V> sib = rightOf(parentOf(x));  
  
                /* 
                 * 如果兄弟节点为红色----（case 1） 
                 */  
                if (colorOf(sib) == RED) {       
                    setColor(sib, BLACK);       
                    setColor(parentOf(x), RED);    
                    rotateLeft(parentOf(x));  
                    sib = rightOf(parentOf(x));  
                }  
  
                /* 
                 * 若兄弟节点的两个子节点都为黑色----（case 2） 
                 */  
                if (colorOf(leftOf(sib))  == BLACK &&  
                    colorOf(rightOf(sib)) == BLACK) {  
                    setColor(sib, RED);  
                    x = parentOf(x);  
                }   
                else {  
                    /* 
                     * 如果兄弟节点只有右子树为黑色----（case 3）  
                     * 这时情况会转变为case 4 
                     */  
                    if (colorOf(rightOf(sib)) == BLACK) {  
                        setColor(leftOf(sib), BLACK);  
                        setColor(sib, RED);  
                        rotateRight(sib);  
                        sib = rightOf(parentOf(x));  
                    }  
                    /* 
                     *case 4 
                     */  
                    setColor(sib, colorOf(parentOf(x)));  
                    setColor(parentOf(x), BLACK);  
                    setColor(rightOf(sib), BLACK);  
                    rotateLeft(parentOf(x));  
                    x = root;  
                }  
            }   
              
            /** 
             * case 5
             */  
            else {  
                Entry<K,V> sib = leftOf(parentOf(x));  
  
                if (colorOf(sib) == RED) {  
                    setColor(sib, BLACK);  
                    setColor(parentOf(x), RED);  
                    rotateRight(parentOf(x));  
                    sib = leftOf(parentOf(x));  
                }  
  
                if (colorOf(rightOf(sib)) == BLACK &&  
                    colorOf(leftOf(sib)) == BLACK) {  
                    setColor(sib, RED);  
                    x = parentOf(x);  
                } else {  
                    if (colorOf(leftOf(sib)) == BLACK) {  
                        setColor(rightOf(sib), BLACK);  
                        setColor(sib, RED);  
                        rotateLeft(sib);  
                        sib = leftOf(parentOf(x));  
                    }  
                    setColor(sib, colorOf(parentOf(x)));  
                    setColor(parentOf(x), BLACK);  
                    setColor(leftOf(sib), BLACK);  
                    rotateRight(parentOf(x));  
                    x = root;  
                }  
            }  
        }  
  
        setColor(x, BLACK);  
    	}  



### WeakHashMap
可能很多同学没用过这个类，没吃过猪肉，应该见过猪跑吧，根据名字猜测，大致能知道这是一个跟弱引用有关系的 HashMap。
我们来看看官方文档的定义

>以弱键 实现的基于哈希表的 Map。在 WeakHashMap 中，当某个键不再正常使用时，将自动移除其条目。更精确地说，对于一个给定的键，其映射的存在并不阻止垃圾回收器对该键的丢弃，这就使该键成为可终止的，被终止，然后被回收。丢弃某个键时，其条目从映射中有效地移除，因此，该类的行为与其他的 Map 实现有所不同。

嗯~~大致就是告诉我们，key 除了被 HashMap 引用之外没有任何引用，就会自动删掉这个 key 以及 value。

**弱引用的概念：**弱引用是用来描述非必需对象的，被弱引用关联的对象只能生存到下一次垃圾收集发生之前，当垃圾收集器工作时，无论当前内存是否足够，都会回收掉只被弱引用关联的对象。

大致我们也知道了这是怎么回事，就是控制 key 的外部引用，可以控制 HashMap 里面保存数据的存留，在大量数据的读取删除的时候，我们可以考虑使用 HashMap。

接下来我们通过一段代码来学习怎么控制弱引用。

	Map<String, String> weak = new WeakHashMap<String, String>();
	weak.put(new String("1"), "1");
	weak.put(new String("2"), "2");
	weak.put(new String("3"), "3");
	weak.put(new String("4"), "4");
	weak.put(new String("5"), "5");
	weak.put(new String("6"), "6");
	Log.e("weak1:",weak.size()+"");//6
	Runtime.getRuntime().gc();  //手动触发 Full GC
	try {
	     Thread.sleep(50); //我的测试中发现必须sleep一下才能看到不一样的结果
	    } catch (InterruptedException e) {
	     e.printStackTrace();
	    }
	Log.e("weak2:",weak.size()+"");//0

	Map<String, String> weak2 = new WeakHashMap<String, String>();
	weak2.put("1", "1");
	weak2.put("2", "2");
	weak2.put("3", "3");
	weak2.put("4", "4");
	weak2.put("5", "5");
	weak2.put("6", "6");
	Log.e("weak3:",weak2.size()+"");//6
	Runtime.getRuntime().gc();
	try {
	    Thread.sleep(50);
	    } catch (InterruptedException e) {
	    e.printStackTrace();
	}
	Log.e("weak4:",weak2.size()+"");//6

打印结果在代码后面的注释里面，从这里我们可以看到。weak里面的 key 值只有weak 对其持有引用，所以在调用 gc 之后，weak的 size 就变成了0.这里有两点需要注意，一是调用 gc 不能用 System.gc()，而要用Runtime.getRuntime().gc()。二是要分得清new String("1")和“1”的区别。

**接下来，我们就来看看key 弱引用是如何关联的。**

查看源码我们能看到，几乎所有的方法都直接或者间接的调用了expungeStaleEntries()方法，我们来看看这个方法。

	/**
     * Expunges stale entries from the table.
     */
    private void expungeStaleEntries() {
        for (Object x; (x = queue.poll()) != null; ) {
            synchronized (queue) {
                @SuppressWarnings("unchecked")
                    Entry<K,V> e = (Entry<K,V>) x;
                int i = indexFor(e.hash, table.length);

                Entry<K,V> prev = table[i];
                Entry<K,V> p = prev;
                while (p != null) {
                    Entry<K,V> next = p.next;
                    if (p == e) {
                        if (prev == e)
                            table[i] = next;
                        else
                            prev.next = next;
                        // Must not null out e.next;
                        // stale entries may be in use by a HashIterator
                        e.value = null; // Help GC
                        size--;
                        break;
                    }
                    prev = p;
                    p = next;
                }
            }
        }
    }
    
方法名已经方法注释都告诉了我们，这个方法是在清除 tab 里面过期的元素。但是我找遍了整个 WeakHashMap 的源码，都没有找到任何 queue.add()的操作，mmp，这特么几个意思。最后，细心的我在 WeakHashMap 的 put 方法里面找到了这样以后代码  *tab[i] = new Entry<>(k, value, queue, h, e);* 
不多说了，直接去看Entry的构造方法。

	private static class Entry<K,V> extends WeakReference<Object> implements Map.Entry<K,V> {
    V value;
    int hash;
    Entry<K,V> next;

    /**
     * Creates new entry.
     */
    Entry(Object key, V value,
          ReferenceQueue<Object> queue,
          int hash, Entry<K,V> next) {
        super(key, queue);
        this.value = value;
        this.hash  = hash;
        this.next  = next;
    }
    ...

我们可以看到Entry 继承自WeakReference，然后把key 和queue 传到了WeakReference 的构造方法中,然后调用了父类Reference 的方法。

好了，到这里就不用太纠结了，就是在Reference 里面做的操作。大致的流程是这样的：JVM计算对象key 的可达性后，发现没有该key 对象的引用，那么就会把该对象关联的Entry<K,V>添加到pending中，所以每次垃圾回收时发现弱引用对象没有被引用时，就会将该对象放入待清除队列中，最后由应用程序来完成清除，WeakHashMap中就负责由方法expungeStaleEntries()来完成清除。

其实这里关于Reference 我自己也没有弄得很清楚，下次找个时间单独学Reference 机制。


### ConCurrentMap
并发集合类，以后在并发的时候再看吧。
挺重要的一个冷门知识点，Android 几乎用不上高并发，刚刚问了 Java 后端的同学，他们也说没用过。。。。是因为我没去过大厂的原因么~~~
据说大厂面试经常会问这个知识点。
同样遗漏的还有 BlockingQueue.

### IdentityHashMap

一个 Key 值可以重复的 map.

> 此类利用哈希表实现 Map 接口，比较键（和值）时使用引用相等性代替对象相等性。换句话说，在 IdentityHashMap 中，当且仅当 (k1= =k2) 时，才认为两个键 k1 和 k2 相等（在正常 Map 实现（如 HashMap）中，当且仅当满足下列条件时才认为两个键 k1 和 k2 相等：(k1= =null ? k2= =null : e1.equals(e2))

也就是说，只有当 两个 key 指向同一引用的时候，才会执行覆盖操作。

用途？举个例子，jvm 中所有的对象都是独一无二的，哪怕两个对象是同一个 class 的对象，而且两个对象的数据完全相同，对于 jvm 来说，他们也是完全不相同的，如果要用一个 map 来记录这样jvm 中的对象，就需要用到 IdentityHashMap。

具体我也没用过~~😀


# 结束语
集合篇到这里就差不多结束了，总的来说，只分析了框架，但并不是知道了框架设计，捋清了实现思路，就一定能手撸出来，要想深入掌握，还得自己跟着思路去手撸一遍。接下来再花一天的时间手撸 ArrayList、HashMap、TreeMap，就正式开始 I/O流的学习吧。


