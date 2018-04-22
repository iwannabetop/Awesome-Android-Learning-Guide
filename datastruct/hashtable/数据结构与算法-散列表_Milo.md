# 数据结构与算法-散列表（哈希表）

## 基本概念
```
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {}
```
  **哈希表**（Hash table，也叫散列表），是根据关键码值(Key value)而直接进行访问的数据结构。也就是说，它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做散列函数，存放记录的数组叫做散列表。


  
  
##  成员变量 
Hashtable是通过"拉链法"实现的哈希表。它包括几个重要的成员变量：**table**, **count**, **threshold**, **loadFactor**, **modCount**。

* **table**是一个 Entry[] 数组类型，而 Entry实际上就是一个单向链表。哈希表的"key-value键值对"都是存储在Entry数组中的。
* **count** 是 Hashtable 的大小，它是 Hashtable 保存的键值对的数量。
* **threshold** 是 Hashtable 的阈值，用于判断是否需要调整 Hashtable 的容量。threshold 的值="容量*加载因子"。
* **loadFactor** 就是加载因子。
* **modCount** 是用来实现 fail-fast 机制的。

## put方法：
结合源码分析：

```
public synchronized V put(K key, V value) {
        // Make sure the value is not null确保value不为null
        if (value == null) {
            throw new NullPointerException();
        }

        // Makes sure the key is not already in the hashtable.
        //确保key不在hashtable中
        //首先，通过hash方法计算key的哈希值，并计算得出index值，确定其在table[]中的位置
        //其次，迭代index索引位置的链表，如果该位置处的链表存在相同的key，则替换value，返回旧的value
        Entry tab[] = table;
        int hash = hash(key);
        int index = (hash & 0x7FFFFFFF) % tab.length;
        for (Entry<K,V> e = tab[index] ; e != null ; e = e.next) {
            if ((e.hash == hash) && e.key.equals(key)) {
                V old = e.value;
                e.value = value;
                return old;
            }
        }

        modCount++;
        if (count >= threshold) {
            // Rehash the table if the threshold is exceeded
            //如果超过阀值，就进行rehash操作
            rehash();

            tab = table;
            hash = hash(key);
            index = (hash & 0x7FFFFFFF) % tab.length;
        }

        // Creates the new entry.
        //将值插入，返回的为null
        Entry<K,V> e = tab[index];
        // 创建新的Entry节点，并将新的Entry插入Hashtable的index位置，并设置e为新的Entry的下一个元素
        tab[index] = new Entry<>(hash, key, value, e);
        count++;
        return null;
    }
```

## get方法：

```
    public synchronized V get(Object key) {
        HashtableEntry<?,?> tab[] = table;
        int hash = key.hashCode();
        int index = (hash & 0x7FFFFFFF) % tab.length;
        for (HashtableEntry<?,?> e = tab[index] ; e != null ; e = e.next) {
            if ((e.hash == hash) && e.key.equals(key)) {
                return (V)e.value;
            }
        }
        return null;
    }
```


## remove方法

```
public synchronized V remove(Object key) {
        HashtableEntry<?,?> tab[] = table;
        int hash = key.hashCode();
        int index = (hash & 0x7FFFFFFF) % tab.length;
        @SuppressWarnings("unchecked")
        HashtableEntry<K,V> e = (HashtableEntry<K,V>)tab[index];
        for(HashtableEntry<K,V> prev = null ; e != null ; prev = e, e = e.next) {
            if ((e.hash == hash) && e.key.equals(key)) {
                modCount++;
                if (prev != null) {
                    prev.next = e.next;
                } else {
                    tab[index] = e.next;
                }
                count--;
                V oldValue = e.value;
                e.value = null;
                return oldValue;
            }
        }
        return null;
    }
```


**Hashtable 遍历方式**
1）Enumeration（枚举）

```
Enumeration<String> en1 = table.keys();
    while(en1.hasMoreElements()) {
    en1.nextElement();
}
```
2）Iterator（迭代）

```
Iterator<String> it1 = table.keySet().iterator();
    while(it1.hasNext()) {
    it1.next();
}
```

**HashTable 与 HashMap区别：**

* HashTable基于Dictionary类，而HashMap基于AbstractMap。Dictionary是任何可以将键映射到相应值得类的抽象父类；AbstractMap是基于Map接口的实现，它以最大限度的减少实现此接口所需要的工作。
* HashMap的Key 和value都允许为null，而HashTable的key和value都不允许为null，所以在使用时注意为空判断。
* HashTable方法是同步的，而HashMap不是的。建议涉及到多线程同步使用HashTable没有涉及就采用HashMap，但是推荐并发情况使用ConcurrentHashMap(锁粒度更低、效率更高)。
* HashMap初始容量为16，HashTable初始容量为11，两者的填充因子都为0.75。HashMap扩容时是当前容量的翻倍即：capacity * 2，HashTable扩容时是容量翻倍+1即：capacity * 2 + 1



# Thanks
http://wiki.jikexueyuan.com/project/java-collection/hashtable.html

https://blog.csdn.net/romantic_PK/article/details/54321701

