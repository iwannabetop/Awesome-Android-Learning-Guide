学了这么久的集合终于要结束了，在最后，我们手撸几个集合聊表对自己刻苦学习的尊敬吧~

本次一共选择了三个具有代表性的集合作为模仿对象，分别是 ArrayList、HashMap、TreeMap。至于为什么选择这三个集合，也是有原因的。因为ArrayList、HashMap 是我们最常用到的集合，然后 TreeMap 是因为比较难以理解且底层数据结构是链表（树应该也算是链表吧~）。

至于集合的三大体系之一的 Set 为什么没选择，我们在学习 Set 的时候已经分析过了，这货就是基于 map 做的实现。

好了，不多扯了，撸代码吧~~

# ArrayList

首先我们先来回顾一下List 接口

> List 定义了一组元素是有序的、可重复的集合。

ArrayList 的特征

- 容量不固定，可以动态扩容
- 有序
- 基于数组的实现

好，那么我们现在就来定义 CustomArrayList

在写代码之前，我们先来明确一下要实现哪些功能，需要哪些属性。

我们这里就只实现核心功能，需要实现的功能有~
1.实现增删改查
2.动态扩容

需要的属性有
1.用来保存实际元素的数组 elementData
2.用来记录当前保存的元素个数 size

	public class CustomArrayList<T> {

	    private Object[] elementData;//存储元素的数组
	    private int size;//当前存储的元素个数
	
	    public CustomArrayList() {
	        elementData = new Object[10];//默认10个长度吧
	        size = 0;//默认0个元素
	    }
	
	    /**
	     * 添加一个元素 t
	     *
	     * @param t 需要添加到 elementData 里面的元素
	     */
	    public void add(T t) {
	        checkArrayLength(size + 1);
	        elementData[size++] = t;
	    }
	
	    /**
	     * 删除角标 index 上的元素
	     *
	     * @param index 想要删除的元素角标
	     * @return element 里面被删除的元素
	     */
	    public T remove(int index) {
	        checkIndex(index);
	
	        T oldValue = (T) elementData[index];
	
	        //删除一个元素之后，需要把这个元素后面所有的元素前移一个角标
	        int numMoved = size - index - 1;
	        if (numMoved > 0) {
	            System.arraycopy(elementData, index + 1, elementData, index, numMoved);
	        }
	
	        elementData[--size] = null; // 清除多余的引用
	
	        return oldValue;
	    }
	
	    /**
	     * 获取角标 index 上的元素
	     *
	     * @param index 想要获取的元素角标
	     * @return element 对应角标的元素
	     */
	    public T get(int index) {
	        checkIndex(index);
	        return (T) elementData[index];
	    }
	
	    /**
	     * 将元素 element 设置到 index 角标上，并返回原来的元素
	     *
	     * @param index   想要修改的元素角标
	     * @param element 想要修改的的元素
	     * @return 修改之前的元素
	     */
	    public T set(int index, T element) {
	        checkIndex(index);
	        T oldValue = (T) elementData[index];
	        elementData[index] = element;
	        return oldValue;
	    }
	
	    /**
	     * 检查是否包含元素 o
	     *
	     * @param o 想要查找的元素
	     * @return true 表示包含元素o
	     */
	    public boolean contains(Object o) {
	        for (Object t : elementData) {
	            //o 为 null 则寻找 null 对象，o 不为 null 则 equals
	            if (t == null && o == null || (t != null && t.equals(o)))
	                return true;
	        }
	
	        return false;
	    }
	
	    /**
	     * 检查操作的角标是否越界
	     *
	     * @param index 想要操作的元素角标
	     * @throws IndexOutOfBoundsException
	     */
	    private void checkIndex(int index) {
	        if (index >= size) {
	            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
	        }
	    }
	
	    /**
	     * 检查数组长度是否够，如果不够则执行扩容操作
	     *
	     * @param length 为当前需要的数组长度
	     */
	    private void checkArrayLength(int length) {
	        if (length > elementData.length) {
	            //需要扩容
	            grow(length);
	        }
	    }
	
	    /**
	     * 执行扩容操作
	     *
	     * @param minCapacity 当前数组长度
	     */
	    private void grow(int minCapacity) {
	        int oldCapacity = elementData.length;
	        int newCapacity = oldCapacity + (oldCapacity >> 1);//扩容1.5倍
	        //这里不考虑扩容超过 Integer.MAX_VALUE 的情况
	        elementData = Arrays.copyOf(elementData, newCapacity);
	    }

	}

代码量很少，都有注释。一个精简版的 ArrayList 完成。

# HashMap

照惯例先回顾一下 Map 接口。

> Map: 是键值对关系的集合，并且键唯一，键一对一对应值。

然后再回想一下 HashMap 的数据结构。
首先是一个HashMapEntry<K,V>的数组 table，根据key.hashCode()&table.length 确定value 存放的 bucketIndex（桶）。然后每个bucketIndex 里面保存的是 key 的 hashCode 值相同的HashMapEntry 集，数据结构是单向链表。

注意~这里有点意思，为什么是 key.hashCode()&(table.length-1)，hashCode 我就不赘述了，介绍 HashMap 的时候我讲过，table.length 又是什么鬼呢，为什么要用这个数？

length 是数组的长度，这点大家不会质疑，所以数组的角标只能是0~length-1，这里注意了，我们的 length 的扩容机制决定了 length 的长度只会是2的倍数，所以这里的 length-1保证了值转换成二进制是 n 个1.然后能保证逻辑与上任何数都能小于 length。

再然后就是 key 冲突的问题。hashcode 毕竟是随机数，很大概率会造成计算出来的bucketIndex 相等。上文也说了，同一个 bucketIndex 的 HashMapEntry 就是单向链表。链表的操作比较简单，我就不再多解释了。

然后说一下加载因子，由于链表比较长，入伙 HashMap 的所有元素全部保持在同一个bucketIndex 里面，即一条单向链表，那么 HashMap 就和 LinkedArrayList 没什么区别了。所以需要引进一个加载因子来加快查找效率，就是通过消耗更多的空间来降低查找时间。加载因子只能是0~1之间，默认0.75 。加载因子是什么意思呢，就是 *HashMap 里面元素的总个数 > table.length 乘以 加载因子*（符号冲突，乘号用汉字代替）的时候，就需要执行table 的扩容操作。

resize 时注意
最后需要注意的就是扩容的时候，table.length-1 的值变了，之前 key.hashCode()&(table.length-1)的值有可能发生改变，所以需要重新计算 HashMap 中已经存在的数据。因此，扩容对于 HashMap 来说是一项比较消耗性能的操作。

最后，在说一下在 Java8中关于 HashMap 的优化。理想情况下，HashMap 的查找时间复杂度是 O(1)，但是在最差的情况（所有的 key 都保存在同一个bucketIndex里面），HashMap 的查找就是在一个单链表上查找内容，时间复杂度是 O(n)。因此，针对这样的情况，Java 对 超过8个元素的 bucketIndex链表自动转换成红黑树。所以在最糟糕的情况下，时间复杂度降低到了O(Logn)。

哦对了，还有 HashMapEntry ，这就是一个 bean，存放了 hash、key、value、next.

好，理论分析完了，在开始撸代码之前，再次强调一下，HashMap 的数据结构就是一个存放着链表表头的数组。

	public class CustomHashMap<K, V> {
	
	    private MyEntry<K, V>[] table;//存放数据的 tab 表
	    private int size;//当前元素个数
	    private int threshold;//当前table 长度最大存放的元素个数，超过则需要扩容
	
	    public CustomHashMap() {
	        table = new MyEntry[16];//默认16个元素
	        size = 0;
	    }
	
	    /**
	     * 插入一组键值对
	     *
	     * @param key   插入的键值对 key，可以为 null
	     * @param value 插入的键值对 value，可以为 null
	     * @return 如果插入的 key 已存在，则返回被覆盖之前的 oldValue，否则返回 null
	     */
	    public V put(K key, V value) {
	        if (key == null) {//如果 key 为 null，则执行插入空 kye 的方法
	            return putForNullKey(value);
	        }
	
	        int hash = secondaryHash(key);
	        int bucketIndex = indexFor(hash, table.length);
	        for (MyEntry<K, V> e = table[bucketIndex]; e != null; e = e.next) {
	            Object k;
	            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
	                V oldValue = e.value;
	                e.value = value;
	                return oldValue;
	            }
	        }
	        addEntry(hash, key, value, bucketIndex);
	        return null;
	    }
	
	    /**
	     * 删除 key 所对应的元素
	     *
	     * @param key 需要删除元素的 key
	     * @return 如果找到 key 对应的元素value，则返回 value，否则返回 null
	     */
	    public V remove(Object key) {
	        MyEntry<K, V> e = removeEntryForKey(key);
	        return (e == null ? null : e.value);
	    }
	
	    /**
	     * 对 key 为 null 的插入执行特殊的处理。因为 key 为 null 无法进行 equals 操作
	     *
	     * @param value 插入空 key 的 value
	     * @return 如果插入的空 key 已存在，则返回被覆盖之前的 oldValue，否则返回 null
	     */
	    private V putForNullKey(V value) {
	        for (MyEntry<K, V> e = table[0]; e != null; e = e.next) {
	            if (e.key == null) {
	                V oldValue = e.value;
	                e.value = value;
	                return oldValue;
	            }
	        }
	        addEntry(0, null, value, 0);
	        return null;
	    }
	
	    /**
	     * 先检测是否需要调整表的大小，然后再执行创建 Entry 操作
	     *
	     * @param hash        插入 key 的 hash 值
	     * @param key         需要插入元素 key
	     * @param value       需要插入元素 value
	     * @param bucketIndex 元素插入在 table 的索引
	     */
	    private void addEntry(int hash, K key, V value, int bucketIndex) {
	        if ((size >= threshold) && (null != table[bucketIndex])) {
	            resize(2 * table.length);
	            hash = (null != key) ? secondaryHash(key) : 0;
	            bucketIndex = indexFor(hash, table.length);
	        }
	
	        createEntry(hash, key, value, bucketIndex);
	    }
	
	    /**
	     * 创建一个新的 MyEntry，并将其插入到表头
	     */
	    private void createEntry(int hash, K key, V value, int bucketIndex) {
	        MyEntry<K, V> e = table[bucketIndex];
	        table[bucketIndex] = new MyEntry<>(hash, key, value, e);
	        size++;
	    }
	
	    /**
	     * 对当前的 table 执行扩容操作
	     * 这里默认了加载因子为0.75
	     * 注意：这里扩容没有做最大值限制，正常应该做的
	     *
	     * @param newCapacity 需要扩容的表长
	     */
	    private void resize(int newCapacity) {
	        MyEntry<K, V>[] newTable = new MyEntry[newCapacity];
	        transfer(newTable);
	        table = newTable;
	        threshold = newCapacity >> 1 + newCapacity >> 2;
	    }
	
	    /**
	     * 将扩容前旧表的所有元素转移到新表
	     * 这里消耗比较多，因为表长度变了，需要重新计算每个元素的 bucketIndex
	     */
	    private void transfer(MyEntry<K, V>[] newTable) {
	        int newCapacity = newTable.length;
	        for (MyEntry<K, V> e : table) {
	            while (null != e) {
	                MyEntry<K, V> next = e.next;
	                int bucketIndex = indexFor(e.hash, newCapacity);
	                e.next = newTable[bucketIndex];
	                newTable[bucketIndex] = e;
	                e = next;
	            }
	        }
	    }
	
	    /**
	     * 查表删除 key 对应的元素
	     * @return 如果有 key 的映射则返回映射的元素，如果没有则返回 null
	     *
	     * */
	    private MyEntry<K, V> removeEntryForKey(Object key) {
	        if (size == 0) {
	            return null;
	        }
	        int hash = (key == null) ? 0 : secondaryHash(key);
	        int bucketIndex = indexFor(hash, table.length);
	        MyEntry<K, V> prev = table[bucketIndex];
	        MyEntry<K, V> e = prev;
	
	        while (e != null) {
	            MyEntry<K, V> next = e.next;
	            Object k;
	            if (e.hash == hash &&
	                    ((k = e.key) == key || (key != null && key.equals(k)))) {
	                size--;
	                if (prev == e)
	                    table[bucketIndex] = next;
	                else
	                    prev.next = next;
	                return e;
	            }
	            prev = e;
	            e = next;
	        }
	
	        return null;
	    }
	
	    /**
	     * 从 api23 里面的 Collections 里面拷贝出来的计算 hash 方法
	     * 我没看懂，既然没有用 Object.hashCode()肯定是这种算法比较优吧
	     *
	     * @param obj 需要计算 hashCode 的对象
	     */
	    private int secondaryHash(Object obj) {
	        int h = obj.hashCode();
	        h += (h << 15) ^ 0xffffcd7d;
	        h ^= (h >>> 10);
	        h += (h << 3);
	        h ^= (h >>> 6);
	        h += (h << 2) + (h << 14);
	        return h ^ (h >>> 16);
	    }
	
	    /**
	     * 通过 hashCode 计算 bucketIndex
	     * @param h hashCode 值
	     * @param length 表长
	     * @return 计算得出的 bucketIndex              
	     * 
	     * */
	    private int indexFor(int h, int length) {
	        return h & (length - 1);
	    }
	
	    /**
	     * 保存 Key Value 的实体 bean
	     * */
	    private static class MyEntry<K, V> {
	        private final K key;
	        private V value;
	        private MyEntry<K, V> next;
	        private int hash;
	
	        MyEntry(int h, K key, V value, MyEntry<K, V> next) {
	            this.hash = h;
	            this.key = key;
	            this.value = value;
	            this.next = next;
	        }
	
	    }
	
	}

代码到这里差不多就写完了，没有对元素超过8个的 bucketIndex 进行链表转红黑树的操作，感兴趣的小伙伴可自行实践。
ps：检查的时候发现 get 方法忘记加了，大概和 remove 方法差不多，有强迫症的小伙伴自己添加测试吧。性能上的话，基本和原生的 HashMap 差不多。

# TreeMap
TreeMap就是红黑树，各种分析我在上一章已经详细讲过了，这里怕大家忘了操作方法，我再复述一遍~

### 插入后修复逻辑
> private void fixAfterInsertion(Node<K, V> e) 
这个方法里面是个 while 循环，循环内容就是以下3个 case，case4是不用修复处理的。
循环条件是*(e != null && e != root && isRedNode(e.parent))*

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

### 删除节点逻辑
> private void deleteEntry(Node<K, V> p) 

- case 1：要删除的节点没有孩子。如果要删除的节点是黑色，则执行删除后修复操作。具体操作参见方法 deleteCase1(Node<K, V> p)
- case 2：要删除的节点有一个孩子。让孩子指向父亲即可，如果删除的节点是黑色，执行删除后修复操作。具体操作参见方法 deleteCase2(Node<K, V> p)
- case 3：要删除的节点有两个孩子。找到删除节点右孩子的最左叶子节点或者左孩子的最右叶子节点。然后把叶子节点的 K、V赋值给需要删除的节点，再根据叶子节点是否有孩子执行 case1或者 case2删除即可。具体操作参见方法 deleteCase3(Node<K, V> p)

### 删除后修复逻辑
> private void fixAfterDeletion(Node<K, V> e)

这个方法里面是个 while 循环，循环内容就是根据条件走4个 step，循环条件是*(e != root && !isRedNode(e))*

这里真的没看出什么名堂出来，感觉就是不听的情况走不同的改变父节点和左右字节点的颜色以及旋转。至于为什么要酱紫做，还是之前那句话，你就当这是数学家总结出来的规律！

源码里面很多的 if else 嵌套操作，我这里拆分了成了四个step。就不用文字总结 每个 step 该怎么操作了，反正代码也很容易阅读了。

### 撸代码
这次的代码没有写什么注释了，注释都在上面分析了。代码相对来说也还是比较清晰，亲测没有毛病，理论上和 TreeMap 性能一样（毕竟算法是一毛一样的）。

	public class CustomTreeMap<K extends Comparable, V> {
	
	    enum Color {RED, BLACK}
	
	    private Node<K, V> root = null;
	    private int size = 0;
	
	
	    public V put(K key, V value) {
	        Node<K, V> t = root;
	        if (t == null) {
	            if (key == null) {
	                throw new NullPointerException("key == null");
	            }
	            root = new Node<>(key, value, Color.BLACK);
	            size = 1;
	            return null;
	        }
	
	        int compare;
	        Node<K, V> parent;
	        do {
	            parent = t;
	            compare = key.compareTo(t.key);
	            if (compare < 0)
	                t = t.leftChild;
	            else if (compare > 0)
	                t = t.rightChild;
	            else
	                return t.setValue(value);
	        } while (t != null);
	
	        Node<K, V> e = new Node<>(key, value, Color.RED, parent);
	        if (compare < 0)
	            parent.leftChild = e;
	        else
	            parent.rightChild = e;
	        fixAfterInsertion(e);
	        size++;
	        return null;
	    }
	
	    public V remove(K key) {
	        Node<K, V> p = getEntry(key);
	        if (p == null)
	            return null;
	
	        V oldValue = p.value;
	        deleteEntry(p);
	        return oldValue;
	    }
	
	    public V get(K key) {
	        Node<K, V> p = getEntry(key);
	        return (p == null ? null : p.value);
	    }
	
	    private Node<K, V> getEntry(K key) {
	        if (key == null)
	            throw new NullPointerException();
	        Node<K, V> p = root;
	        while (p != null) {
	            int cmp = key.compareTo(p.key);
	            if (cmp < 0)
	                p = p.leftChild;
	            else if (cmp > 0)
	                p = p.rightChild;
	            else
	                return p;
	        }
	        return null;
	    }
	
	
	    private void deleteEntry(Node<K, V> p) {
	        size--;
	        if (p.leftChild != null && p.rightChild != null) {
	            deleteCase3(p);
	        } else if (p.leftChild != null || p.rightChild != null) {
	            deleteCase2(p);
	        } else {
	            deleteCase1(p);
	        }
	    }
	
	    //两个孩子
	    private void deleteCase3(Node<K, V> p) {
	        Node<K, V> replaceNode;
	        if (p.rightChild != null) {
	            replaceNode = p.rightChild;
	            while (replaceNode.leftChild != null)
	                replaceNode = replaceNode.leftChild;
	        } else {
	            replaceNode = p.leftChild;
	            while (replaceNode.rightChild != null)
	                replaceNode = replaceNode.rightChild;
	        }
	        p.key = replaceNode.key;
	        p.value = replaceNode.value;
	        if (replaceNode.hasChild()) {
	            deleteCase2(replaceNode);
	        } else {
	            deleteCase1(replaceNode);
	        }
	    }
	
	    //一个孩子
	    private void deleteCase2(Node<K, V> p) {
	        Node<K, V> replacement = (p.leftChild != null ? p.leftChild : p.rightChild);
	        replacement.parent = p.parent;
	        if (p.parent == null)
	            root = replacement;
	        else if (p == p.parent.leftChild)
	            p.parent.leftChild = replacement;
	        else
	            p.parent.rightChild = replacement;
	
	        p.leftChild = p.rightChild = p.parent = null;
	
	        if (p.color == Color.BLACK)
	            fixAfterDeletion(replacement);
	    }
	
	    //没有孩子
	    private void deleteCase1(Node<K, V> p) {
	        if (p.color == Color.BLACK)
	            fixAfterDeletion(p);
	
	        if (p.parent != null) {
	            if (p == p.parent.leftChild)
	                p.parent.leftChild = null;
	            else if (p == p.parent.rightChild)
	                p.parent.rightChild = null;
	            p.parent = null;
	        }
	    }
	
	
	    private void fixAfterDeletion(Node<K, V> e) {
	        while (e != root && !isRedNode(e)) {
	            Node<K, V> brother = brotherOf(e);
	            if (brother != null && isRedNode(brother)) {
	                fixDelStep1(brother);
	            }
	            fixDelStep2(e, brother);
	        }
	        setColor(e, Color.BLACK);
	    }
	
	    private void fixDelStep4(Node<K, V> brother, Node<K, V> e) {
	        setColor(brother, colorOf(e.parent));
	        setColor(e.parent, Color.BLACK);
	        if (!isLeftChild(brother)) {
	            setColor(brother.rightChild, Color.BLACK);
	            rotateLeft(e.parent);
	        } else {
	            setColor(brother.leftChild, Color.BLACK);
	            rotateRight(e.parent);
	        }
	        e = root;
	    }
	
	    private void fixDelStep3(Node<K, V> brother, Node<K, V> e) {
	        setColor(brother, Color.RED);
	        if (!isLeftChild(brother)) {
	            setColor(brother.leftChild, Color.BLACK);
	            rotateRight(brother);
	            brother = e.parent.rightChild;
	        } else {
	            setColor(brother.rightChild, Color.BLACK);
	            rotateLeft(brother);
	            brother = e.parent.leftChild;
	        }
	    }
	
	    private void fixDelStep2(Node<K, V> e, Node<K, V> brother) {
	        if (!isRedNode(getLeftChild(brother)) &&
	                !isRedNode(brother.rightChild)) {
	            setColor(brother, Color.RED);
	            e = e.parent;
	        } else {
	            if (!isLeftChild(brother) && !isRedNode(getRightChild(brother))
	                    || isLeftChild(brother) && !isRedNode(getLeftChild(brother))) {
	                fixDelStep3(brother, e);
	            }
	            fixDelStep4(brother, e);
	        }
	    }
	
	    private void fixDelStep1(Node<K, V> brother) {
	        setColor(brother, Color.BLACK);
	        setColor(brother.parent, Color.RED);
	
	        if (!isLeftChild(brother)) {
	            rotateLeft(brother.parent);
	            brother = brother.parent.rightChild;
	        } else {
	            rotateRight(brother.parent);
	            brother = brother.parent.leftChild;
	        }
	
	    }
	
	    private void fixAfterInsertion(Node<K, V> e) {
	        while (e != null && e != root && isRedNode(e.parent)) {
	            if (isRedNode(brotherOf(e.parent))) {
	                insertCase1(e);
	            } else if (!isRedNode(brotherOf(e.parent)) && !isLeftChild(e)) {
	                insertCase2(e);
	            } else if (isLeftChild(e)) {
	                insertCase3(e);
	            }
	        }
	        root.color = Color.BLACK;
	    }
	
	    private void insertCase3(Node<K, V> e) {
	        if (isLeftChild(e)) {
	            e = e.parent;
	            rotateRight(e);
	        }
	        setColor(e.parent, Color.BLACK);
	        setColor(e.parent.parent, Color.RED);
	        rotateLeft(parentOf(parentOf(e)));
	    }
	
	    private void insertCase2(Node<K, V> e) {
	        if (!isLeftChild(e)) {
	            e = e.parent;
	            rotateLeft(e);
	        }
	        setColor(e.parent, Color.BLACK);
	        setColor(e.parent.parent, Color.RED);
	        rotateRight(parentOf(parentOf(e)));
	    }
	
	    private void insertCase1(Node<K, V> e) {
	        setColor(e.parent, Color.BLACK);
	        setColor(brotherOf(e.parent), Color.BLACK);
	        setColor(parentOf(parentOf(e)), Color.RED);
	        e = e.parent.parent;
	    }
	
	    /**
	     * From CLR
	     */
	    private void rotateLeft(Node<K, V> p) {
	        if (p != null) {
	            Node<K, V> r = p.rightChild;
	            p.rightChild = r.leftChild;
	            if (r.leftChild != null)
	                r.leftChild.parent = p;
	            r.parent = p.parent;
	            if (p.parent == null)
	                root = r;
	            else if (p.parent.leftChild == p)
	                p.parent.leftChild = r;
	            else
	                p.parent.rightChild = r;
	            r.leftChild = p;
	            p.parent = r;
	        }
	    }
	
	    /**
	     * From CLR
	     */
	    private void rotateRight(Node<K, V> p) {
	        if (p != null) {
	            Node<K, V> l = p.leftChild;
	            p.leftChild = l.rightChild;
	            if (l.rightChild != null) l.rightChild.parent = p;
	            l.parent = p.parent;
	            if (p.parent == null)
	                root = l;
	            else if (p.parent.rightChild == p)
	                p.parent.rightChild = l;
	            else p.parent.leftChild = l;
	            l.rightChild = p;
	            p.parent = l;
	        }
	    }
	
	    private Color colorOf(Node<K, V> p) {
	        return (p == null ? Color.BLACK : p.color);
	    }
	
	    private Node<K, V> brotherOf(Node<K, V> p) {
	        return (p == null || p.parent == null ? null :
	                isLeftChild(p) ? p.parent.rightChild : p.parent.leftChild);
	    }
	
	    private Node<K, V> parentOf(Node<K, V> p) {
	        return p != null ? p.parent : null;
	    }
	
	    private boolean isLeftChild(Node<K, V> p) {
	        return !(p == null || p.parent == null) && p.parent.leftChild == p;
	    }
	
	    private Node<K, V> getLeftChild(Node<K, V> p) {
	        return p != null ? p.leftChild : null;
	    }
	
	    private Node<K, V> getRightChild(Node<K, V> p) {
	        return p != null ? p.rightChild : null;
	    }
	
	    private boolean isRedNode(Node<K, V> p) {
	        return p != null && p.color == Color.RED;
	    }
	
	    private void setColor(Node<K, V> p, Color c) {
	        if (p != null)
	            p.color = c;
	    }
	
	    private static class Node<K extends Comparable, V> {
	        Color color = Color.BLACK;
	        V value;
	        K key;
	        Node<K, V> parent, leftChild, rightChild;
	
	        public Node(K key, V value, Color colorMe) {
	            this.color = colorMe;
	            this.value = value;
	            this.key = key;
	        }
	
	        public Node(K key, V value, Color color, Node<K, V> parent) {
	            this.value = value;
	            this.key = key;
	            this.color = color;
	            this.parent = parent;
	        }
	
	        public V setValue(V value) {
	            V oldValue = this.value;
	            this.value = value;
	            return oldValue;
	        }
	
	        public boolean hasChild() {
	            return leftChild != null || rightChild != null;
	        }
	    }
	}

# 结束语
好了，集合的学习就此告一段落，强烈建议自己撸一遍这三个集合。ArrayList 的代码量200不到，HashMap 和 TreeMap 的代码量也都是在300行左右，这些都是集合的核心代码，并且性能和官方源码基本上是一样的。掌握了在集合上就基本不会踩坑了，再次强调，想要扎实掌握集合类，一定要自己手撸一般。


