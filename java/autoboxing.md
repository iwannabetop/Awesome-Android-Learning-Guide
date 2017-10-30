
# 深入理解Java中的包装类与自动拆装箱

> 文章出处：[安卓进阶学习指南](https://github.com/iwannabetop/Awesome-Android-Learning-Guide)             
> 作者:[麦田哥(Wheat7)](https://github.com/wheat7)      
> 审核者:[shixinzhang](https://github.com/shixinzhang)  [Struggle](https://github.com/LiPingStruggle)                   
完稿日期：2017.10.30

今儿来和大家聊一聊Java中的自动拆装箱问题，也是我们[安卓进阶学习指南](https://github.com/iwannabetop/Awesome-Android-Learning-Guide)的一部分，欢迎大家多多关注，其中的一些问题也是我重新学习得到的，欢迎大家多多讨论

## 什么是自动拆装箱
自动拆装箱在Java5(就是Java1.5,后边改了命名)时被引入，自动装箱就是Java自动将基础类型值转换成对应的包装类对象，比如将int的变量转换成Integer对象，这个过程叫做装箱，反之将Integer对象转换成int类型值，这个过程叫做拆箱。说白了，就是个语法糖

## 基本类型与引用类型
稍有常识的人都看得出。。。哦，不对，稍有Java基础的同学都应该知道Java的数据类型，大的分类就分为基础类型与引用类类型  
     
基础类型又能分为我们俗称的四类八种，分别为四种整型，byte，short，int，long，他们的区别是所能存储的数据的长度不同，也就是说他们在内存中分配的内存长度不同，两种浮点类型，32位的单精度浮点float，64位双精度浮点数double，1种Unicode编码的字符单元 
char，最后就是boolean，真值布尔类型。 接下来是与我们今天主题相关的重点，就是基础类型是存储在栈内存中的，在程序启动的时候就会被初始化，就是你用或不用，他都在那里，在你声明一个基本类型的时候，他就被赋予了默认的初始值，比如int类型，就是0

并且我们再扩展讨论一下这个情况

```
int a = 1
int b = 1
System.out.printf(a == b) ---- true
```
因为 == 判断的是内存地址，也就是判断两者是否为同一个对象，基本类型相同的值指向的是同一块内存区域，所以返回的是ture，这也就解释了我们为什么可以用==来判断基本类型，而不能用 == 来判断引用类型，而是要用equals()方法

基本类型并不具对象的性质

2.引用类型又有类，接口，数组三种，为什么叫引用类型，因为我们的引用类型的对象，是存在于堆内存中的，我们所持有的是栈内存中指向相应堆内存的一个引用

这和自动拆装箱有什么关系？请看下边

## 持有对象&包装类
在有些情况下，我们需要持有一系列的对象，也就是使用我们常用的集合类，在这里不展开说，然而集合类在设计的时候持有的是我们所有类型的单根超类，Object，在将对象装入集合的时候，对象都会被向上转型为Object类，然后取出的时候，又通过参数化类型，也就是我们常用的泛型菱形<>语法，转型为我们装入的原始类型，但是如果我们呢要持有的是基本类型呢？基础类型的并没有父类，所以集合类并不能持有他，那怎么办呢？于是Java为每一个基础类型封装了相应的包装类

基本类型 | 包装类 
-|-|
byte | Byte  |
short | Short  | 
int | Integer  | 
long | Long  | 
float | Float  | 
double | Double  | 
char | Character  | 
boolean | Boolean  | 

于是我们可以这样操作了

```
List<Interger> intList = new ArrayList<>();

intList.add(1);

```

>注意后边一句，这就是我们后边要说的自动装箱，因为add方法需要传入的是List中所持有的参数化类型，也就是int的包装类型Integer，而我们传入的是一个int类型的值,这个int值被编译器自动包装成了Integer值,这就是本文的主题，自动拆装箱，后边我们会展开细说

你应该会想到，数组可以来持有基本类型啊，但是你也知道的是，有些时候我们要持有的数量是不确定的，数组在初始化的时候就必须确定长度，这使我们使用数组来持有基本类型，或是对象都有很大的局限性

集合持有对象是包装类最常见的应用点，当然也有其他地方，我们需要的是Object参数而又需要的是数值，或者是其他基本类型的时候，也会应用到包装类，包装类使基本类型有了对象的性质，并且为其添加了属性和方法，丰富了基本类型的操作

## 自动拆装箱

何为自动拆装箱，请看代码

Java5以前

```
//装箱
Integer integer = new Integer(10);
//拆箱
int i = integer.intValue();

```

Java5以后

```
//自动装箱
Integer integer = 10;
//自动拆箱
int i = integer;

```
在Java5之前，你需要一个Integer类型的对象，你需要像其他对象一样，把他new出来(调用静态方法Integer.valueOf(3)来创建对象内部也是new，别抬杠)，拆箱需要调用intValue()方法来取出int值，而在Java5之后，你创建Integer类型的对象，可以直接用int类型赋值，Integer类型的也能赋值给int类型的变量

通俗点来说，就是基本类型和他的包装类可以互相赋值了，在赋值的时候，编译器自动的进行了包装/拆箱工作，但是不仅仅是赋值的时候会发生自动拆装箱，请看下一个问题

## 什么时候会发生自动拆装箱

1. 赋值       
上边大家已经看到了，不说啦     
2. 方法调用传入参数的时候

```
public void argAutoBoxing(Integer i) {
}

argAutoBoxing(1);

```


```
public void argAutoUnBoxing(int i) {
}

argAutoUnBoxing(new Integer(1));

```

3.被操作符操作的时候

```
Integer integer = new Integer(1);

int i = interger + 1

```

## 自动拆装箱是怎么实现的
一句话，就是编译器帮我们自动调用了拆装箱的方法，以Integer/int为例子，自动装箱就是编译器自动调用了valueOf(int i)方法,自动拆箱自动调用了intValue()方法,其他基本类型类推

## 有哪些问题得注意？

1. 性能问题

首先在堆内存中创建对象的消耗肯定是要比使用栈内存要多的，同时在自动拆装箱的时候，也有一定的性能消耗，如果在数据量比较大，或者是循环的情况下，频繁的拆装箱并且生成包装类的时候，对性能的影响就是一星半点了，所以不是特殊的需求，例如上述被集合持有的情况，还是使用基本类型而不是包装类

在循环的时候

```
Integer sum = 0;
 for(int i=1000; i<5000; i++){
   sum+=i;
}
```

上面的代码sum+=i可以看成sum = sum + i，在sum被+操作符操作的时候，会对sum进行自动拆箱操作，进行数值相加操作，最后发生自动装箱操作转换成Integer对象。其内部变化如下
 
```
sum = sum.intValue() + i;
Integer sum = new Integer(result);
```

sum为Integer类型，在上面的循环中会创建4000个无用的Integer对象，在这样庞大的循环中，会降低程序的性能并且加重了垃圾回收的工作量。因此在我们编程时，需要注意到这一点，正确地声明变量类型，避免因为自动装箱引起的性能问题

再举一个例子，在Java中的HashMap的性能也受到自动拆装箱的影响
因为HashMap接收的参数类型是HashMap <Object, Object>，所以在增删改查的时候，都会对Key值进行大量的自动拆装箱，为了解决这个问题，Java提供了SparseArray，包括SparseBoolMap, SparseIntMap, SparseLongMap, LongSparseMap,所接受的Key值都是基本类型的值，例如SparseIntMap就是SparseIntMap<int, Object>,在避免了大量自动拆装箱的同时，还降低的内存消耗。这里就点到为止，具体的数据结构的问题我们就不深入了

2. 重载与自动装箱
 
在Java5之前，value(int i)和value(Integer o)是完全不相同的方法，开发者不会因为传入是int还是Integer调用哪个方法困惑，但是由于自动装箱和拆箱的引入，处理重载方法时稍微有点复杂，例如在ArrayList中，有remove(int index)和remove(Object o)两个重载方法，如果集合持有三个Integer类型值为3,1,2的对象，我们调用remove(3), 是调用了remove的哪个重载方法？remove掉的是值为3的对象，还是remove了index为3，值为2的那个对象呢？其实问题就是，参数3是否会被自动打包呢？答案是：不会，在这种情况下，编译器不会进行自动拆装箱，所以调用的是remove(int index),index为3值为2的这个Integer对象会被remove

通过以下例子我们可以验证

```
public void testAutoBoxing(int i){
    System.out.println("primitive argument");
 
}
 
public void testAutoBoxing(Integer integer){
    System.out.println("wrapper argument");
 
}
 
//calling overloaded method
int value = 1;
test(value); //no autoboxing 
Integer iValue = value;
test(iValue); //no autoboxing
 
Output:
primitive argument
wrapper argument
```

3. 缓存值问题

这个问题是面试的常客了

```
public class Main {
    public static void main(String[] args) {
 
        Integer i1 = 100;
        Integer i2 = 100;
        Integer i3 = 200;
        Integer i4 = 200;
 
        System.out.println(i1==i2);
        System.out.println(i3==i4);
    }
}

Output:
true
false
```

这是为什呢，让我们来翻一翻源码

```
public static Integer valueOf(int i) {
        if(i >= -128 && i <= IntegerCache.high)
            return IntegerCache.cache[i + 128];
        else
            return new Integer(i);
    }

```
欸，看来问题就出在IntegerCache类中了,我们再来翻一下IntegerCache的实现类

```
private static class IntegerCache {
        static final int high;
        static final Integer cache[];
 
        static {
            final int low = -128;
 
            // high value may be configured by property
            int h = 127;
            if (integerCacheHighPropValue != null) {
                // Use Long.decode here to avoid invoking methods that
                // require Integer's autoboxing cache to be initialized
                int i = Long.decode(integerCacheHighPropValue).intValue();
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - -low);
            }
            high = h;
 
            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);
        }
 
        private IntegerCache() {}
    }
```

在通过valueOf方法创建Integer对象的时候，如果数值在[-128,127]之间，便返回指向IntegerCache.cache中已经存在的对象的引用；否则创建一个新的Integer对象。

上面的代码中i1和i2的数值为100，因此会直接从cache中取已经存在的对象，所以i1和i2指向的是同一个对象，而i3和i4则是分别指向不同的对象


我们再来看一题

```
public class Main {
    public static void main(String[] args) {
 
        Double i1 = 100.0;
        Double i2 = 100.0;
        Double i3 = 200.0;
        Double i4 = 200.0;
 
        System.out.println(i1==i2);
        System.out.println(i3==i4);
    }
}

Output:
flase
flase
```

至于为什么，我们就不深挖下去了，小伙伴可以自己去看源码，这里要说的是，包装类都有相应的缓存机制，来降低一般情况下的资源消耗，但是每个包装类的机制肯定是不一样的，大家自己去探索

4. == 和 equlas()

大家都应该清楚明了的了解两者的区别,一句话说就是 == 比较的是内存中地址，equlas()对比的为数值，因为基本类型相同的数值指向的同一块内存，所以可以用==来比较，而引用类型则不可以

下边我们也是直观的使用代码来说明在包装类和自动拆装箱时使用==和equlas()的情况

```
public class Main {
    public static void main(String[] args) {
 
        Integer a = 1;
        Integer b = 2;
        Integer c = 3;
        Integer d = 3;
        Integer e = 321;
        Integer f = 321;
        Long g = 3L;
        Long h = 2L;
 
        System.out.println(c==d);
        System.out.println(e==f);
        System.out.println(c==(a+b));
        System.out.println(c.equals(a+b));
        System.out.println(g==(a+b));
        System.out.println(g.equals(a+b));
        System.out.println(g.equals(a+h));
    }
}

Output:
true
false
true
true
true
false
true
```

在包装类的使用和自动拆装箱中，使用==运算符的时候，如果两个操作数都是包装器类型的引用，则是比较指向的是否是同一个对象，而如果其中有一个操作数是表达式（即包含算术运算）则比较的是数值（上边说到的的使用运算符触发了自动拆箱）。另外，对于包装器类型，equals()方法并不会进行类型转换，和我们常见的对String类型使用一样，比较的是对象的值

理解了这个，大家应该就对结果清晰明了了，第一句和第二句是因为上边说过的缓存机制，重点解释一下第三句，a+b包含了算术运算，因此会触发自动拆箱过程，因此它们比较的是数值是否相等。而对于c.equals(a+b)会先触发自动拆箱过程，再触发自动装箱过程，也就是说a+b，会先各自调用intValue方法，得到了加法运算后的数值之后，便调用Integer.valueOf方法，再进行equals比较

5. 警惕NullPointerException

我们在使用基本类型的时候，在声明的时候即使我们没有对变量进行赋值，编译器也会自动的为其赋予初始值，比如int值就是0，boolean就是flase，所以我们在使用基本类型的时候，是不会出现NullPointerException的，但在使用包装类的时候，我们就要注意这个问题了，不能因为有自动拆装箱这个语法糖，就忘记了包装类和基本类型的区别，将其同等对待了，如果你没有在使用包装类的时候通过显式、或是通过自动装箱机制为其赋值，在你取出值、或是通过自动拆箱使用该值的时候，就会发生NullPointerException，这个是大家要注意的

## 总结
在Java中，使用基本类型还是最节省资源的选择，虽然基础类型影响了Java的"面向对象性"，，但是牺牲换来了性能，所以在非必要的时候，所以我们应该尽量避免使用包装类，并且在使用的时候，要清楚自动拆装箱机制，规避使用的误区和风险

> 某些内容和栗子参考于网络，没有找到原po,感谢！









