**首先，在探索集合之前，我们先来思考一个问题，集合是什么？**

*针对一个特定的问题，如果事先不知道需要多少个对象，或者它们的持续时间有多长，那么也不知道如何保存那些对象。既然如此，怎样才能知道那些对象要求多说空间呢？事先上根本无法提前知道，除非进入运行期。
在面向对象的设计中,大多数问题的解决办法似乎都有些轻率——只是简单地创建另一种类型的对象。用于解决特定问题的新型对象容纳了指向其他对象的引用。当然,也可以用数组来做同样的事情,那是大多数语言都具有的一种功能。 但不能只看到这一点。这种新对象通常叫作“集合”(亦叫作一个“容器”)。在 需要的时候,集合会自动扩充自己,以便适应我们在其中置入的任何东西。所以 我们事先不必知道要在一个集合里容下多少东西。只需创建一个集合,以后的工作让它自己负责好了。*

上文摘抄自《Thinking in Java》，集合解决的问题是，在编译期间不知道要多少个对象，但是数组必须在申明的时候明确指明数组长度，如果食用数组，申请太多的空间就会造成资源浪费，如果申请太少空间，就不够用。所以引出了一个概念叫“容器”，来解决这个问题，这个容器就是我们今天要研究的对象－－“集合”。

我们先来看一下类关系图～


![](https://user-gold-cdn.xitu.io/2017/9/24/89f19f4769cfc2b41706e201cbea6431)

Java 提供的集合都在 Java.utils 包下，集合主要分两类，Collection 和 Map。我们用到的各种类型的集合，都是实现自这两个接口。集合的实现类有很多，开发过程中，我们需要根据不同的需求，选择合适的集合设计，以便高效率的解决我们的实际问题。至于什么场景用哪一种类型的容器，使用这种容器能带来哪些好处，这就是我们要研究的核心点，也是我们用好 Java 集合的精髓。

磨刀不误砍柴工，我们在探索集合的架构设计之前，我们先来研究一下Iterator。

# Iterator
>Iterator ：［计］迭代器，迭代程序

迭代器，这里用到的就是设计模式中的迭代器模式。

>迭代器模式
>定义：提供一种方法访问一个容器对象中各个元素，而又不暴露该对象的内部细节。

这里我们的重点不是迭代器模式，对“迭代器模式”感兴趣的童鞋可以自行去了解一波。

先来看看接口 Iterator 的设计。


    public interface Iterator<E> {
        boolean hasNext();
    
        E next();
    
        default void remove() {
            throw new UnsupportedOperationException("remove");
        }
    
        default void forEachRemaining(Consumer<? super E> var1) {
            Objects.requireNonNull(var1);
    
            while(this.hasNext()) {
                var1.accept(this.next());
            }
        }
    }

一共四个方法，其中hasNext（）和 next（）方法是迭代必须方法。remove（）和forEachRemaining（）方法有默认实现，小伙伴不要纠结接口怎么会有默认实现方法，这是 Java 8 的新特性。

- hasNext()：是否有下一个元素
- next（）：获取下一个元素
- remove（）：删除当前元素，非必须的方法，有需要可重写实现。
- forEachRemaining（）：给剩下来所有元素做了一个自定义的相同操作。非必须的方法，有需要可重写实现。

#fail-fast 与 ConcurrentModificationException

> fail-fast:是java集合(Collection)中的一种错误机制。当多个线程对同一个集合的内容进行操作时，就可能会产生fail-fast事件。
> ConcurrentModificationException：出现 fail-fast 问题的时候就会抛出这个异常。

可能问题描述得有点抽象，我举个例子：假设有个 ArrayList 集合A，A里面包含10个元素，分别是0～9。假设线程a在获取第5个元素的过程中，线程b操作A删除了第一个元素。那么问题来了，此时a线程是获取的到结果是5，但是我的本意应该是取到结果4，此时程序发生了错误，因此产生 fail-fast 问题，遂抛出异常。

###解决方案
1. 在遍历过程中所有涉及到改变modCount值得地方全部加上synchronized。
2. 用 CopyOnWriteArrayList，ConcurrentHashMap 替换 ArrayList， HashMap，它们的功能和名字一样，在写入时会创建一个 copy，然后在这个 copy 版本上进行修改操作，这样就不会影响原来的迭代。不过坏处就是浪费内存。


# Iterator 实现迭代功能
Iterator 的实现类一般以内部类的形式写在集合类里面。功能的实现是根据各种集合实现的特定实现，比如说 ArrayList 和 LinkedArrayList 的数据结构不一样，所以 Iterator 实现也不一样。

这里以 ArrayList 的 Iterator 举例子讲一下 Iterator 的代码实现。
在看源码之前，我们先来回顾一下 Iterator 的使用。

    不使用 Iterator 遍历集合是这样的：
    for(int i=0; i<list.size();i++){  
            // ... 
        } 
    //使用 Iterator 遍历集合是这样的：
    
    Iterator iterator = list.iterator();
    while (iterator.hasNext()){
        Object obj = iterator.next()
    }

一般情况，如果只是遍历获取集合的所有元素，我选择使用第一种方式，因为用 iterator 感觉好麻烦的样子。但是肯定很多童鞋都犯过一个这样的错误，我们在 for 循环里面对集合进行了remove操作，但是最后的结果和我们期望的不一样，这时候老手告诉你，集合不能这样操作，如果你要remove，请用 iterator操作，那样不会出问题，于是，我们默默的记下了这个结论。稍后，我们会在Iterator 的源码里面找到原因。

    private class Itr implements Iterator<E> {
            int cursor;//当前角标位置
            int lastRet;//用来标记当前需要删除的元素角标
            int expectedModCount;//ArrayList元素个数
    
        private Itr() {
            this.lastRet = -1;//角标默认指向－1
            this.expectedModCount = ArrayList.this.modCount;
        }
    
        public E next() {
            this.checkForComodification();//检查 fail-fast
            int var1 = this.cursor;
            if(var1 >= ArrayList.this.size) {
                throw new NoSuchElementException();//角标越界
            } else {
                Object[] var2 = ArrayList.this.elementData;//ArrayList的底层实现实际上就是一个数组
                if(var1 >= var2.length) {//double check
                    throw new ConcurrentModificationException();
                } else {
                    this.cursor = var1 + 1;
                    return var2[this.lastRet = var1];
                }
            }
        }
        
        public boolean hasNext() {
            //检查是否大于数组长度
            return this.cursor != ArrayList.this.size;
        }
            
        public void remove() {
            if(this.lastRet < 0) {
                throw new IllegalStateException();
            } else {
                this.checkForComodification();
                try {
                    ArrayList.this.remove(this.lastRet);//调用ArrayList的remove
                    this.cursor = this.lastRet;//cursor
                    this.lastRet = -1;//避免同时调用两次remove
                    this.expectedModCount = ArrayList.this.modCount;//更新数组长度
                } catch (IndexOutOfBoundsException var2) {
                    throw new ConcurrentModificationException();
                }
            }
        }
    ｝

注视我都写在代码里面了，其实ArrayList.Iterator 就是一个对数组的遍历，较之直接 for（）循环ArrayList，优点是做了 fail-fast 检查，并且增加了在遍历过程中删除的功能。

再来详细讲一下for（）循环里面不能用list.remove（i）的原因。因为在 for(int i=0; i<list.size();i++) 语句中，假设 list 有4个元素，假如果在 i ＝ 0 的时候调用了list.remove(i),此时就出现了取值错位并且漏值的情况。但是在Iterator 里面，我们可以看到有一行这样的代码 this.cursor = this.lastRet 改变了当前数组的角标。

这里分享一个使用for循环然后再在循环里面删除值并且不会出错的办法

    for (int i = list.size()-1; i >=0; i--) {
        String s = list.get(i);
        if (s.equals("remove")) {
            list.remove(i);
        }
    }


好了，很简单的逻辑，Iterator 就分享到这里吧，不同的集合里面的 Iterator 的实现方式不一样，但是逻辑都是一样的，所以就不再赘述了。

古耐～

