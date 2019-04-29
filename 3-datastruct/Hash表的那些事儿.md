Sunday, April 22, 2018

**小伙伴们写的总结：**

1. [Milo 的总结](https://github.com/iwannabetop/Awesome-Android-Learning-Guide/blob/master/datastruct/hashtable/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95-%E6%95%A3%E5%88%97%E8%A1%A8_Milo.md)
2. [shixinzhang的总结](https://github.com/iwannabetop/Awesome-Android-Learning-Guide/blob/master/datastruct/hashtable/%E5%93%88%E5%B8%8C%E8%A1%A8_%E5%BC%A0%E6%8B%AD%E5%BF%83.md)
3. [金鑫的总结](https://github.com/iwannabetop/Awesome-Android-Learning-Guide/blob/master/datastruct/hashtable/%E6%95%A3%E5%88%97%E8%A1%A8_%E9%87%91%E9%91%AB.txt)
4. [Cloud 的总结](https://github.com/iwannabetop/Awesome-Android-Learning-Guide/blob/master/datastruct/hashtable/%E5%93%88%E5%B8%8C%E8%A1%A8%E7%9A%84%E5%AE%9E%E7%8E%B0%E3%80%81%E6%80%A7%E8%83%BD%E5%92%8C%E4%BD%BF%E7%94%A8%E5%9C%BA%E6%99%AF_Cloud.md)
5. [ZHL 的总结](https://github.com/iwannabetop/Awesome-Android-Learning-Guide/blob/master/datastruct/hashtable/%E6%95%A3%E5%88%97%E8%A1%A8_ZHL.md)
6. [杨哲的总结](https://github.com/iwannabetop/Awesome-Android-Learning-Guide/blob/master/datastruct/hashtable/%E6%95%A3%E5%88%97%E8%A1%A8%E5%AD%A6%E4%B9%A0__%E6%9D%A8%E5%93%B2.md)
7. [Alex 赵的总结](https://github.com/iwannabetop/Awesome-Android-Learning-Guide/blob/master/datastruct/hashtable/%E5%93%88%E5%B8%8C%E8%A1%A8%E7%9A%84%E7%89%B9%E7%82%B9%EF%BC%8C%E6%80%A7%E8%83%BD%E5%92%8C%E4%BD%BF%E7%94%A8%E5%9C%BA%E6%99%AF_Alex%20%E8%B5%B5.md)
8. [麦田哥的总结](https://github.com/iwannabetop/Awesome-Android-Learning-Guide/blob/master/datastruct/hashtable/%E5%93%88%E5%B8%8C%E8%A1%A8_%E9%BA%A6%E7%94%B0%E5%93%A5.md)
9. [Anonymous 的总结](https://github.com/iwannabetop/Awesome-Android-Learning-Guide/blob/master/datastruct/hashtable/Hash%E8%A1%A8_Anonymous.md)
10. [Struggle 的总结](https://github.com/iwannabetop/Awesome-Android-Learning-Guide/blob/master/datastruct/hashtable/%E6%95%A3%E5%88%97%E8%A1%A8_Struggle.md)

题目：Hash 表的那些事儿

[TOC]

> 文章出自：[安卓进阶学习指南](https://github.com/iwannabetop/Awesome-Android-Learning-Guide)

# 背景介绍

![这里写图片描述](http://img.blog.csdn.net/20171031224622133?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTI0MDg3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

大家好，这篇文章是 [《安卓进阶技能树计划》](https://github.com/iwannabetop/Awesome-Android-Learning-Guide) 的第二部分 [《数据结构与算法系列》](https://github.com/iwannabetop/Awesome-Android-Learning-Guide/blob/master/datastruct/README.md) 的第二篇。

# 前言

想象一个场景，如果想在一个学校中找出一个叫王五的学生，这个时候可能出现两种情况：

- 从学生处拿到学生名单，一个个查找直到定位到王五所在的班级，这种查找方式我们一般称之为**顺序查找**
- 刚好学生处有人认识王五，他直接就带我们找到了王五

这两种方式毫无疑问是第二种比较快，而今天我们介绍的 Hash 表就应用了这种思想。

# 基础概念

Hash 表（哈希表）又称之为散列表，散列表实际上是一种散列表示的**字典**，那么**字典**是什么呢？**字典**是由一群具有映射关系的元素组成的集合，其中每种元素都包含关键码（Key）和属性（Value）。
散列表的实质是通过散列函数建立映射关系的数据结构，对于这种映射关系可以用以下公式来进行表示 

![](https://ws2.sinaimg.cn/large/006tNc79gy1fqlt9t49m7j30cu01ujr8.jpg)

其中 ![](http://latex.codecogs.com/gif.latex?h%28key%29) 就是这里所说的**散列函数**，对于每个关键码为 ![](http://latex.codecogs.com/gif.latex?key) 的元素而言，其通过散列函数计算出散列地址 ![](http://latex.codecogs.com/gif.latex?h%28key%29)，然后将 ![](http://latex.codecogs.com/gif.latex?value) 存储在散列地址上。
如果两个不相等的关键码 ![](http://latex.codecogs.com/gif.latex?key1) 和 ![](http://latex.codecogs.com/gif.latex?key2)，通过散列函数计算之后得到了相同的散列地址（即 ![](http://latex.codecogs.com/gif.latex?h%28key1%29%20%3D%20h%28key2%29)），这种现象称之为**碰撞**，而发生碰撞的两个关键码称之为**同义词**

散列函数 ![](http://latex.codecogs.com/gif.latex?h%28key%29) 的定义域是整个关键码的集合，![](http://latex.codecogs.com/gif.latex?h%28key%29) 的值域应该是可以使用的整个地址空间，称之为**基本区域**。为了评价散列函数的好坏以及衡量检索的效率还引入了以下两个概念
**负载因子（![](http://latex.codecogs.com/gif.latex?a) ）**:

![](https://ws1.sinaimg.cn/large/006tNc79gy1fqlta8218lj30aa02qq2t.jpg)

当 ![](http://latex.codecogs.com/gif.latex?a>1) 时必定会出现碰撞

**平均检索长度（![](http://latex.codecogs.com/gif.latex?ASL)）**：

![](https://ws2.sinaimg.cn/large/006tNc79gy1fqltb2he7wj309g03iq2r.jpg)

其中 ![](http://latex.codecogs.com/gif.latex?n) 是 Hash 表中的元素个数，![](http://latex.codecogs.com/gif.latex?p_%7Bi%7D) 是查找第 ![](http://latex.codecogs.com/gif.latex?i) 个元素的概率，![](http://latex.codecogs.com/gif.latex?c_%7Bi%7D) 是找到第 ![](http://latex.codecogs.com/gif.latex?i) 个元素的比较次数。![](http://latex.codecogs.com/gif.latex?ASL) 越小代表性能越好

# **特点**及使用场景

Hash 表的主要特点如下：

- 访问速度很快

  Hash 其实是随机存储的一种优化，先进行分类，然后查找时按照这个对象的分类去找。Hash 通过一次计算大幅度缩小查找范围，自然比从全部数据里查找速度要快。理想情况下 Hash 表检索的时间复杂度为 ![](http://latex.codecogs.com/gif.latex?O%281%29)，而顺序检索的时间复杂度为 ![](http://latex.codecogs.com/gif.latex?O%28n%29)

- 需要额外的空间

  对于 Hash 表而言，为了**减少碰撞**并且**方便后续元素的增加**，其散列函数的基本区域一般是需要大于关键码的集合的，所以相比较普通的链式存储而言，其需要更多的空间。

- 无序

  由于元素是根据散列函数计算出来的散列地址进行存储和访问的，所以对于有序访问无能为力

- 可能产生碰撞

鉴于 Hash 表的各项特点，其使用场景一般如下：

1. 内存缓存
   - 缓存的存在是为了提高效率，使用 Hash 表来实现缓存，不仅可以省去重新生成的时间，还可以加快查询的效率，一举两得
2. 快速查找
   - 有时候需要在一大堆数据里找到某个数据，你有很多种算法比如二分、比如先排序，但那都依赖于数据有序，在数据无序的情况下，适用 Hash 表来保存，查询时的效率可以说是非常快的
3. 建立关联关系
   - 哈希表的核心就是 key-value，这正适合一些需要建立关联的业务中，比如 EventBus 中就使用了 Hash 表保存了事件和调用方法的映射关系

# 设计一个 Hash 表

在设计  Hash 表的时候我们主要关注两点：

- 散列函数的选择
  - 使得散列地址分布尽可能的均匀
  - 使得负载因子 ![](http://latex.codecogs.com/gif.latex?a%3E0.5)
- 碰撞的处理
  - 同义词可以放置在基本区域未被占用的存储单元
  - 也可以放置在基本区域以外另开辟的区域（溢出区）

## 常用的散列函数

1. 数字分析法

   取关键码的某个线性函数值为散列地址

   ![](https://ws1.sinaimg.cn/large/006tNc79gy1fqgw9ikg1xj30ji0aw74f.jpg)

2. 折叠法

   将关键码分割成位数相同的几部分，最后一部分位数可以不同，然后取这几部分的叠加和（去除进位）作为散列地址。数位叠加可以有移位叠加和间界叠加两种方法。移位叠加是将分割后的每一部分的最低位对齐，然后相加；间界叠加是从一端向另一端沿分割界来回折叠，然后对齐相加

   ![](https://ws1.sinaimg.cn/large/006tNc79gy1fqgwada71zj30ru0b6mx5.jpg)

3. 中平方法

   当无法确定关键码里哪几位分布相对比较均匀时，可以先求出关键码的平方值，然后按需要取平方值的中间几位作为散列地址。这是因为计算平方之后的中间几位和关键字中的每一位都相关，所以不同的关键字会以较高的概率产生不同的散列地址

   ![](https://ws3.sinaimg.cn/large/006tNc79gy1fqgwb2v9tmj30h404ot8l.jpg)

4. 基数转换法

   把关键码看作是另外一个进制的表示，然后再转换成原来进制的数，最后用数字分析法取其中几位作为散列地址。

   ![](https://ws4.sinaimg.cn/large/006tNc79gy1fqgwsj2ipaj30p605k748.jpg)

5. 除余法

   选择一个适当的正整数 ![](http://latex.codecogs.com/gif.latex?P)，用 ![](http://latex.codecogs.com/gif.latex?P) 去除关键码，余数作为散列地址，即 ![](http://latex.codecogs.com/gif.latex?h%28key%29%20%3D%20key%5C%25P) 。
   这个方法的关键在于选取适当的 ![](http://latex.codecogs.com/gif.latex?P)。如果 ![](http://latex.codecogs.com/gif.latex?P) 为偶数，则总是把奇数的关键码转换为奇数地址，把偶数的关键码转换为偶数地址；如果 ![](http://latex.codecogs.com/gif.latex?P) 是关键码的基数的幂次，则相当于选择关键码的最后几位数字为地址。这两种情况都不太好。一般 ![](http://latex.codecogs.com/gif.latex?P) 为不大于基本区域长度的最大素数比较好。

> 一般而言，散列函数应该尽可能简单使得关键码到地址的转换速度较快，另外计算出的散列地址应该尽可能均匀分布。

## 碰撞的处理

### 开地址法

在基本区域内形成一个同义词的探查序列，沿着探查序列逐个查找，直到找到查找的元素或碰到一个未被占用的地址为止。发生碰撞时计算公式如下：

![](http://latex.codecogs.com/gif.latex?H_%7Bi%7D%3D%28h%28key%29%20+%20d_%7Bi%7D%29%5C%25m)

![](http://latex.codecogs.com/gif.latex?m) 是散列表长度，![](http://latex.codecogs.com/gif.latex?d_{i}) 是增量序列
形成探查序列的方法有很多种，这里将结合例子介绍线性探查法以及双散列函数法：

1. 线性探查法（ ![](http://latex.codecogs.com/gif.latex?d_%7Bi%7D%3Di)）

   ![](https://ws4.sinaimg.cn/large/006tNc79gy1fqgy4i8immj31iw082jrp.jpg)

   散列函数：![](http://latex.codecogs.com/gif.latex?h%28key%29%3Dkey%5C%2513)，在上述序列中插入19

   - ![](http://latex.codecogs.com/gif.latex?h%2819%29%3D6)，发生碰撞
   - ![](http://latex.codecogs.com/gif.latex?H_%7B1%7D%3D7)，发生碰撞
   - ……..
   - ![](http://latex.codecogs.com/gif.latex?H_%7B5%7D%3D11)，未发生碰撞，存储在11

2. 双散列函数法（![](http://latex.codecogs.com/gif.latex?d_%7Bi%7D%3Di*h_%7B2%7D%28key%29)）

   在上述例子中改用双散列函数法插入19，其中 ![](http://latex.codecogs.com/gif.latex?h_%7B2%7D%28key%29%3Dkey%5C%257+1)

   - ![](http://latex.codecogs.com/gif.latex?h%2819%29%3D6)，发生碰撞
   - ![](http://latex.codecogs.com/gif.latex?H_%7B1%7D%20%3D%20%286%20+%20%2819%20%5C%25%207%20+%201%29%29%20%5C%2513%20%3D%2012)，未发生碰撞，存储在12

### 拉链法

拉链法解决碰撞的结构图如下

![](https://ws2.sinaimg.cn/large/006tNc79gy1fqgyiirdo0j30mi0q0dfw.jpg)

可以看到实际上采用拉链法解决碰撞时，Hash 表中保存的是链表

## 实现 Hash 表

学习了这么多理论知识，终于到了实践的环节了

1. 确定散列函数和解决碰撞的方法

   - 散列函数采用除余法
   - 解决碰撞的方法采用拉链法

2. 定义链表中的元素 Entry

   ```java
   public class Entry {
       int key;
       int value;
       Entry next;

       public Entry(int key, int value, Entry next) {
           this.key = key;
           this.value = value;
           this.next = next;
       }
   }
   ```

3. 定义 HashTable

   ```Java
   public class HashTable {

       /**
        * 默认散列表的初始化长度
        * 设置小一点，这样我们能清楚看到扩容
        * 在实际使用中其实可以在初始化时传参，要知道，扩容也是很损耗性能的
        */
       private static final int DEFAULT_CAPACITY = 4;

       /**
        * 扩容因子
        */
       private static final float LOAD_FACTOR = 0.75f;

       /**
        * 散列表数组
        */
       private Entry[] table = new Entry[DEFAULT_CAPACITY];
       private int size = 0;//散列元素个数
       private int use = 0;//散列表使用地址个数

       /**
        * 添加/修改
        * @param key
        * @param value
        */
       public void put(int key, int value) {
           int index = hash(key);
           //初始化链表头节点
           if (table[index] == null) {
               table[index] = new Entry(-1, -1, null);
           }
           Entry e = table[index];

           if (e.next == null) {
               //不存在值，向链表添加，有可能扩容，要用 table 属性
               table[index].next = new Entry(key, value, null);
               size++;
               use++;
               //不存在值，说明是个未用过的地址，需要判断是否要扩容
               if (use >= table.length * LOAD_FACTOR) {
                   resize();
               }
           } else {
               //本身存在值，修改已有的值
               for (e = e.next; e != null; e = e.next) {
                   int k = e.key;
                   if (k == key) {
                       e.value = value;
                       return;
                   }
               }
               //不存在相同的值，直接向链表中添加元素
               Entry temp = table[index].next;
               Entry newEntry = new Entry(key, value, temp);
               table[index].next = newEntry;
               size++;
           }
       }

       /**
        * 删除
        * @param key
        */
       public void remove(int key) {
           int index = hash(key);
           Entry e = table[index];
           Entry pre = table[index];
           if (e != null && e.next != null) {
               for (e = e.next; e != null; pre = e, e = e.next) {
                   int k = e.key;
                   if (k == key) {
                       pre.next = e.next;
                       size--;
                       return;
                   }
               }
           }
       }

       /**
        * 获取
        * @param key
        * @return
        */
       public int get(int key) {
           int index = hash(key);
           Entry e = table[index];
           if (e != null && e.next != null) {
               for (e = e.next; e != null; e = e.next) {
                   int k = e.key;
                   if (k == key) {
                       return e.value;
                   }
               }
           }
           //若没有找到，则返回-1
           return -1;
       }

       /**
        * 获取散列表中元素个数
        * @return
        */
       public int size() {
           return size;
       }

       public int getLength() {
           return table.length;
       }

       private void resize() {
           int newLength = table.length * 2;
           Entry[] oldTable = table;
           table = new Entry[newLength];
           use = 0;
           for (int i = 0; i < oldTable.length; i++) {
               if (oldTable[i] != null && oldTable[i].next != null) {
                   Entry e = oldTable[i];
                   while (null != e.next) {
                       Entry next = e.next;
                       //重新计算哈希值，放入新的地址当中
                       int index = hash(next.key);
                       if (table[index] == null) {
                           use++;
                           table[index] = new Entry(-1, -1, null);
                       }
                       Entry temp = table[index].next;
                       Entry newEntry = new Entry(next.key, next.value, temp);
                       table[index].next = newEntry;
                       e = next;
                   }
               }
           }
       }

       //散列函数
       private int hash(int key) {
           return key % table.length;
       }
   }
   ```

#  Hash 表的性能分析

Hash 表的整体性能如下

|      | 最优情况 | 最糟情况 |
| ---- | -------- | -------- |
| 查找 | O(1)     | O(n)     |
| 插入 | O(1)     | O(n)     |
| 删除 | O(1)     | O(n)     |

在没有发生碰撞的情况下， Hash 表是在查找中效率最高的，此时它的时间复杂度为![](http://latex.codecogs.com/gif.latex?O(1))。然而在实际应用中，碰撞是不可避免的，所以 Hash 表的效率取决于碰撞发生的次数。影响碰撞的因素主要有以下三点：

1. 散列函数计算出散列地址是否均匀，越均匀发生碰撞的概率越低
2. 处理冲突的方法：相同的关键字，相同的散列函数，处理冲突的方法不同，会使得 ![](http://latex.codecogs.com/gif.latex?ASL) 不同。
3. 负载因子![](http://latex.codecogs.com/gif.latex?a)：当填入表中的记录越多，![](http://latex.codecogs.com/gif.latex?a)越大，产生冲突的可能性就越大，所以这个时候需要用扩容解决负债因子过大的问题

# Java 中的 Hash 表

这里主要结合 1.8 中的 HaspMap 对 Java 中 Hash 表的应用做一下说明

1. 散列函数

   ```java
   static final int hash(Object key) {
       int h;
       return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
   }
   ```

   由于哈希表的容量都是 **2 的 N 次方**，元素的 hashCode() 在很多时候下低位是相同的，这将导致碰撞，所以 HashMap 中的散列函数采用的是将 hashCode() 值右移 16 位取高 16 位之后与 hashCode() 值做异或运算。

2. 碰撞的解决方案

   ```Java
   final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                      boolean evict) {
           Node<K,V>[] tab; Node<K,V> p; int n, i;
           ...
                   for (int binCount = 0; ; ++binCount) {
                       if ((e = p.next) == null) {
                           //普通情况采用链表解决碰撞
                           p.next = newNode(hash, key, value, null);
                           //链表节点个数大于8的时候采用树的方式解决碰撞
                           if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                               treeifyBin(tab, hash);
                           break;
                       }
                       if (e.hash == hash &&
                           ((k = e.key) == key || (key != null && key.equals(k))))
                           break;
                       p = e;
                   }
           ...
           return null;
       }
   ```

   从截取的代码部分可以看到 HashMap 对于碰撞还是采用的是拉链法，只是在链表节点大于 8 个时会将链表修改为树，之所以这样做的原因是为了避免极端情况下查找链表最后一个元素所导致的时间复杂度过高的问题。

3. HashMap 的扩容

   ```Java
   final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                  boolean evict) {
       ...
       if (++size > threshold)
           resize();
       ...
       return null;
   }

   final Node<K,V>[] resize() {
       ...
       if (oldCap > 0) {
           if (oldCap >= MAXIMUM_CAPACITY) {
               ....
           }
           else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                    oldCap >= DEFAULT_INITIAL_CAPACITY)
               newThr = oldThr << 1; // 扩容至两倍
       }
       else if (oldThr > 0) // initial capacity was placed in threshold
           newCap = oldThr;
       else {               // zero initial threshold signifies using defaults
           newCap = DEFAULT_INITIAL_CAPACITY;
           //默认扩容系数 DEFAULT_INITIAL_CAPACITY 0.75
           newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
       }
       if (newThr == 0) {
           float ft = (float)newCap * loadFactor;
           newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                     (int)ft : Integer.MAX_VALUE);
       }
       threshold = newThr;
       @SuppressWarnings({"rawtypes","unchecked"})
           Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
       table = newTab;
       ....
       return newTab;
   }
   ```

   Hash 表的负载越大的时候出现冲突的可能性就会越高，所以 HashMap 设置了扩容系数用以保证性能，系数值默认0.75（太小多次扩容也会影响性能），当 HashMap 里面的元素个数超过现有 Hash 表 3/4 时，则会对 Hash 表进行扩容操作，扩容默认是扩容一倍。

# Thanks

- [数据结构之------什么是哈希表？](http://www.cnblogs.com/feichengwulai/articles/3682302.html)
- [数据结构之哈希表](http://www.cnblogs.com/s-b-b/p/6208565.html)
- [Java 集合深入理解（16）：HashMap 主要特点和关键方法源码解读](https://blog.csdn.net/u011240877/article/details/53351188)