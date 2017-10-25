# String源码分析

## 概述
![String继承体系](http://orbm62bsw.bkt.clouddn.com/String%E7%BB%A7%E6%89%BF%E5%85%B3%E7%B3%BB.png)

String是一个final类，并且实现了CharSequence,Comparable以及Serializable接口
对于Android开发者而言，这无疑是一个经常使用的类，所以需要好好了解一下他的原理，先看一下文档中的注释。
- Strings are constant; their values can not be changed after they are created.
Stringbuffers support mutable strings.Because String objects are immutable they can be shared. Forexample:
- String 字符串是常量，其值在实例创建后就不能被修改，但字符串缓冲区支持可变的字符串，因为缓冲区里面的不可变字符串对象们可以被共享（其实就是使对象的引用发生了改变）。

面试中经常会被问到，为什么String要被设计成final的，很多时候会去刻意地去记一些面试题，去不大愿意去看源码，其实看一下注释，就都明白了。

## 正文

### 成员变量
```
/**
The value isused for character storage.
*/
private final char value[];
这是一个字符数组，并且是 final 类型，用于存储字符串内容。从 fianl 关键字可以看出，String 的内容一旦被初始化后，其不能被修改的。

看到这里也许会有人疑惑，String 初始化以后好像可以被修改啊。比如找一个常见的例子：
String str = “hello”;     str = “hi”
其实这里的赋值并不是对 str 内容的修改，而是将str指向了新的字符串。另外可以明确的一点：String 其实是基于字符数组 char[] 实现的。
/**
Cache the hashcode for the string
*/
private int hash;  //Default to 0
缓存字符串的 hash Code，其默认值为 0
```


### 构造方法
#### 空的构造方法

```
 public String(){
  this.value = "".value;
}
```

该构造方法会创建空的字符序列，注意这个构造方法的使用，因为创造不必要的字符串对象是不可变的。因此不建议采取下面的创建 String 对象:
```
String str = new String()
str = "sample";
```
这样的结果显而易见，会产生了不必要的对象。
#### 使用字符串类型的对象来初始化
```
public String(String original){
  this.value = original.value;
  this.hash = original.hash;
}
```
这里将直接将源 String 中的 value 和 hash 两个属性直接赋值给目标 String。因为 String 一旦定义之后是不可以改变的，所以也就不用担心改变源
String 的值会影响到目标 String 的值。

#### 使用字符数组来构造
```
public String(char value[]){
    this.value = Arrays.copyOf(value, value.length);
}
public String(char value[], int offset, int count){
  if(offset<0){
    throw new StringIndexOutOfBoundsException(offset);
  }
  if(count<=0){
    if(count<0){
     throw new String IndexOutOfBoundsException(count);
    }
    if(offset <= value.length){
      this.value = "".value;
      return;
    }
  }

 //Note:offset or count might be near-1>>>1.
  if(offset > value.length - count){
      throw new StringIndexOutOfBoundsException(offset+count);
  }
 this.value=Arrays.copyOfRange(value,offset,offset+count);
}
```

这里值得注意的是：当我们使用字符数组创建
String 的时候，会用到 Arrays.copyOf 方法或
Arrays.copyOfRange 方法。这两个方法是将原有的字符数组中的内容逐一的复制到String中的字符数组中。会创建一个新的字符串对象，随后修改的字符数组不影响新创建的字符串。
#### 使用字节数组来构建 String 
在 Java 中，String 实例中保存有一个 char[] 字符数组，char[] 字符数组是以 unicode 码来存储的，String 和 char 为内存形式。
byte 是网络传输或存储的序列化形式，所以在很多传输和存储的过程中需要将 byte[] 数组和String进行相互转化。所以，String 提供了一系列重载的构造方法来将一个字符数组转化成 String，提到 byte[] 和 String 之间的相互转换就不得不关注编码问题。
String(byte[] bytes, Charset charset)
该构造方法是指通过 charset 来解码指定的 byte 数组，将其解码成 unicode 的 char[] 数组，够造成新的 String。
这里的 bytes 字节流是使用 charset 进行编码的，想要将他转换成 unicode 的 char[] 数组，而又保证不出现乱码，那就要指定其解码方式**

同样使用字节数组来构造 String 也有很多种形式，按照是否指定解码方式分的话可以分为两种：

```
public String(byte bytes[]){
  this(bytes, 0, bytes.length);
}
public String(byte bytes[], int offset, int length){
  checkBounds(bytes, offset, length);
    this.value = StringCoding.decode(bytes, offset, length);
}
```
如果我们在使用 byte[] 构造 String 的时候，使用的是下面这四种构造方法(带有 charsetName 或者
charset 参数)的一种的话，那么就会使用
StringCoding.decode 方法进行解码，使用的解码的字符集就是我们指定的 charsetName 或者charset 。
```
String(byte bytes[])
String(byte bytes[], int offset, int length)
String(byte bytes[], Charset charset)
String(byte bytes[], String charsetName)
String(byte bytes[], int offset, int length, Charset charset)
String(byte bytes[], int offset, int length, String charsetName)
```

我们在使用 byte[] 构造 String 的时候，如果没有指明解码使用的字符集的话，那么 StringCoding 的 decode 方法首先调用系统的默认编码格式，如果没有指定编码格式则默认使用 ISO-8859-1 编码格式进行编码操作。主要体现代码如下：

```
static char[] decode(byte[] ba, int off, int len){
    String csn = Charset.defaultCharset().name();
  try{ //use char set name decode() variant which provide scaching.
         return decode(csn, ba, off, len);
  } catch(UnsupportedEncodingException x){
   warnUnsupportedCharset(csn);
  }
  try{
    return decode("ISO-8859-1", ba, off, len);  } catch(UnsupportedEncodingException x){
    //If this code is hit during VM initiali zation, MessageUtils is the only way we will be able to get any kind of error message.
    MessageUtils.err("ISO-8859-1 char set not available: " + x.toString());
    // If we can not find ISO-8859-1 (are quired encoding) then things are seriously wrong with the installation.
    System.exit(1);
    return null;
  }
}
```
#### 通过 StringBuffer 和 StringBuider构造
···
public String(StringBuffer buffer) {
    synchronized(buffer) {
    this.value = Arrays.copyOf(buffer.getValue(), buffer.length());
    } 
} 
public String(StringBuilder builder) {
     this.value = Arrays.copyOf(builder.getValue(), builder.length());
}
···
当然，这两个构造方法是很少用到的，因为当我们有了 StringBuffer 或者 StringBuilfer 对象之后可以直接使用他们的 toString 方法来得到 String。
关于效率问题，Java 的官方文档有提到说使用StringBuilder 的 toString 方法会更快一些，原因是
StringBuffer 的 toString 方法是 synchronized 的，在牺牲了效率的情况下保证了线程安全。
StringBuilder 的 toString() 方法：
```
@Override
public String toString(){
  //Create a copy, don't share the array
  return new String(value,0,count);
}
StringBuffer 的 toString() 方法：

@Override
public synchronized String toString(){
  if (toStringCache == null){
    toStringCache = Arrays.copyOfRange(value, 0, count);
  }
  return new String(toStringCache, true);
}
```
一个特殊的保护类型的构造方法
String 除了提供了很多公有的供程序员使用的构造方法以外，还提供了一个保护类型的构造方法（Java 7），我们看一下他是怎么样的：

```
String(char[] value, boolean share) {
 // assert share : "unshared not supported";
 this.value = value;
}
```
从代码中我们可以看出，该方法和 String(char[] value) 有两点区别：

第一个，该方法多了一个参数：boolean share，其实这个参数在方法体中根本没被使用。注释说目前不支持 false，只使用 true。
那可以断定，加入这个 share 的只是为了区分于 String(char[] value) 方法，不加这个参数就没办法定义这个函数，只有参数是不能才能进行重载。
第二个区别就是具体的方法实现不同。我们前面提到过，String(char[] value) 方法在创建 String 的时候会用到 Arrays 的 copyOf 方法将value中的内容逐一复制到 String当中，而这个 String(char[] value, boolean share) 方法则是直接将value的引用赋值给String的value。
那么也就是说，这个方法构造出来的 String 和参数传过来的 char[] value 共享同一个数组。
为什么 Java 会提供这样一个方法呢？

- 性能好：这个很简单，一个是直接给数组赋值（相当于直接将 String 的 value 的指针指向char[]数组），一个是逐一拷贝。当然是直接赋值快了。
- 节约内存：该方法之所以设置为 protected，是因为一旦该方法设置为公有，在外面可以访问的话，如果构造方法没有对 arr 进行拷贝，那么其他人就可以在字符串外部修改该数组，由于它们引用的是同一个数组，因此对 arr 的修改就相当于修改了字符串，那就破坏了字符串的不可变性。
- 安全的：对于调用他的方法来说，由于无论是原字符串还是新字符串，其 value 数组本身都是 String 对象的私有属性，从外部是无法访问的，因此对两个字符串来说都很安全。

#### 其他方法

length() 

```
public int length(){
  return value.length;
}
```

 isEmpty() 

public boolean isEmpty(){
  return value.length == 0;
}

charAt(int index) 

```
public char charAt(int index){
  if((index < 0) || (index >= value.length)){
    throw new StringIndexOutOfBoundsException(index);
  }
  return value[index];
}
```
char[] toCharArray() 

trim() 

toUpperCase() 

toLowerCase() 

···


比较方法


```
boolean equals(Object anObject)；

boolean contentEquals(String Buffersb)；

boolean contentEquals(Char Sequencecs)；

boolean equalsIgnoreCase(String anotherString)；

int compareTo(String anotherString)；

int compareToIgnoreCase(String str)；

boolean regionMatches(int toffset, String other, int ooffset, int len) //局部匹配

boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len) //局部匹配
```
字符串有一系列方法用于比较两个字符串的关系。 前四个返回 boolean 的方法很容易理解，前三个比较就是比较 String 和要比较的目标对象的字符数组的内容，一样就返回true, 不一样就返回false，核心代码如下：

```
int n = value.length; 
while (n-- ! = 0) {
   if (v1[i] != v2[i])
     return false;
     i++;
 }
```
v1 v2 分别代表 String 的字符数组和目标对象的字符数组。 第四个和前三个唯一的区别就是他会将两个字符数组的内容都使用 toUpperCase 方法转换成大写再进行比较，以此来忽略大小写进行比较。相同则返回 true，不想同则返回 false

equals方法：


```
public boolean equals(Object anObject) {
     if (this == anObject) {
         return true;
     } 
    if (anObject instanceof String) {
       String anotherString = (String) anObject;
       int n = value.length;
       if (n == anotherString.value.length) {
           char v1[] = value;
           char v2[] = anotherString.value;
           int i = 0;
           while (n-- != 0) {
             if (v1[i] != v2[i])
             return false;
             i++;
           }
           return true;
       }
   } 
   return false;
}
```

该方法首先判断this == anObject ，也就是说判断要比较的对象和当前对象是不是同一个对象，如果是直接返回 true，如不是再继续比较，然后在判断
anObject 是不是 String
类型的，如果不是，直接返回 false，如果是再继续比较，到了能终于比较字符数组的时候，他还是先比较了两个数组的长度，不一样直接返回 false，一样再逐一比较值。 

contentEquals 有两个重载:

StringBuffer 需要考虑线程安全问题，加锁之后再调用 

contentEquals((CharSequence) sb) 方法。

contentEquals((CharSequence) sb) 则分两种情况，一种是 cs instanceof 
AbstractStringBuilder，另外一种是参数是 String
类型。具体比较方式几乎和 equals 方法类似，先做“宏观”比较，在做“微观”比较。

下面这个是equalsIgnoreCase代码的实现：

```
 public boolean equalsIgnoreCase(String anotherString) {
 return (this == anotherString) ? true : (anotherString != null) && (anotherString.value.length == value.length) && regionMatches(true, 0, anotherString, 0, value.length);
 }
```

看到这段代码，眼前为之一亮。使用一个三目运算符和&&操作代替了多个 if 语句。

hashCode


```
public int hashCode(){
  int h = hash;
  if(h == 0 && value.length > 0){
    char val[] = value;
    for(int i = 0; i < value.length; i++){
      h = 31 * h + val[i];
    }
    hash = h;
  }
  return h;
}
```

hashCode 的实现其实就是使用数学公式：s[0]31^(n-1) + s[1]31^(n-2) + ... + s[n-1]
所谓“冲突”，就是在存储数据计算hash地址的时候，我们希望尽量减少有同样的hash地址。如果使用相同 hash 地址的数据过多，那么这些数据所组成的 hash 链就更长，从而降低了查询效率。
所以在选择系数的时候要选择尽量长的系数并且让乘法尽量不要溢出的系数，因为如果计算出来的
hash 地址越大，所谓的“冲突”就越少，查找起来效率也会提高。



#### String对“+”的重载

我们知道，Java是不支持重载运算符，String的“+”是java中唯一的一个重载运算符，那么java使如何实现这个加号的呢？我们先看一段代码：


```
public static void main(String[] args) {
     String string="hollis";
     String string2 = string + "chuang";
}
```

然后我们将这段代码的实际执行情况：

```
public static void main(String args[]){
     String string = "hollis";
     String string2 = (new         
     StringBuilder(String.valueOf(string))).append("chuang").toString();
}
```

看了反编译之后的代码我们发现，其实String对“+”的支持其实就是使用了StringBuilder以及他的append、toString两个方法。

String.valueOf和Integer.toString的区别
接下来我们看以下这段代码，我们有三种方式将一个int类型的变量变成呢过String类型，那么他们有什么区别？

int i = 5;

String i1 = "" + i;

String i2 = String.valueOf(i);

String i3 = Integer.toString(i);

第三行和第四行没有任何区别，因为String.valueOf(i)也是调用Integer.toString(i)来实现的。
第二行代码其实是String i1 = (new StringBuilder()).append(i).toString();

首先创建了一个StringBuilder对象，然后再调用append方法，再调用toString方法。
switch对字符串支持的实现

还是先上代码：

```
public class switchDemoString {
     public static void main(String[] args) {
         String str = "world";
         switch (str) {
         case "hello": 
              System.out.println("hello");
              break;
         case "world":
             System.out.println("world");
             break;
         default: break;
       }
    }
}
```

对编译后的代码进行反编译：


```
public static void main(String args[]) {
       String str = "world";
       String s;
       switch((s = str).hashCode()) {
          case 99162322:
               if(s.equals("hello"))
                   System.out.println("hello");
               break;
          case 113318802:
               if(s.equals("world"))
                   System.out.println("world");
               break;
          default: break;
       }
  }
```

看到这个代码，你知道原来字符串的switch是通过equals()和hashCode()方法来实现的。记住，switch中只能使用整型，比如byte，short，char(ackii码是整型)以及int。其实swich只支持一种数据类型，那就是整型，其他数据类型都是转换成整型之后在使用switch的。

### StringBuffer
我们在上面说String对象是不可变的，而StringBuffer 对象是可变的，大家都说在能大体了解字符串的长度的情况下创建StringBuffer对象时 指定其容量，在上面的string中我们也知道使用“+”号的时候我们也是调用了append方法。

![StringBuffer继承关系](http://orbm62bsw.bkt.clouddn.com/StringBuffer%E7%BB%A7%E6%89%BF%E5%85%B3%E7%B3%BB.png)


```
public final class StringBuffer  extends AbstractStringBuilder
    implements java.io.Serializable, CharSequence
{

    /** use serialVersionUID from JDK 1.0.2 for interoperability */
    static final long serialVersionUID = 3388685877147921107L;

    /**
     * Constructs a string buffer with no characters in it and an
     * initial capacity of 16 characters.
     */

     //  默认为16个字符
    public StringBuffer() {
        super(16);
    }

    /**
     * Constructs a string buffer with no characters in it and
     * the specified initial capacity.
     *
     * @param      capacity  the initial capacity.
     * @exception  NegativeArraySizeException  if the <code>capacity</code>
     *               argument is less than <code>0</code>.
     */
    public StringBuffer(int capacity) {
        super(capacity);
    }

    /**
     * Constructs a string buffer initialized to the contents of the
     * specified string. The initial capacity of the string buffer is
     * <code>16</code> plus the length of the string argument.
     *
     * @param   str   the initial contents of the buffer.
     * @exception NullPointerException if <code>str</code> is <code>null</code>
     */
    public StringBuffer(String str) {
        super(str.length() + 16);
        append(str);
    }

```
StringBuffer 类继承自AbstractStringBuilder那在看看AbstractStringBuilder的源码

```
abstract class AbstractStringBuilder implements Appendable, CharSequence {
    /**
     * The value is used for character storage.
     */
      // 这里我们看到，这个数组没有被final 修饰，所以引用变量的值可以改变，
       //可以引用到其他数组对象
     char[] value;

    /**
     * The count is the number of characters used.
     */
   //  记录字符的个数
   int count;

    /**
     * This no-arg constructor is necessary for serialization of subclasses.
     */
    AbstractStringBuilder() {
    }

    /**
     * Creates an AbstractStringBuilder of the specified capacity.
     */
    AbstractStringBuilder(int capacity) {
       // 构造函数，创建数组对象
        value = new char[capacity];
    }

    /**
     * Returns the length (character count).
     *
     * @return  the length of the sequence of characters currently
     *          represented by this object
     */
    public int length() {
        return count;
    }

```
从这些源码我们看到 他的数组和String的不一样，因为成员变量value数组没有被final修饰所以可以修改他的引用变量的值，即可以引用到新的数组对象。所以StringBuffer对象是可变的


如果知道字符串的长度则创建对象的时候尽量指定大小

- (1)在上面的源代码中我们看到StringBuffer 的构造函数默认创建的大小为16个字符。
- (2)如果我们在创建对象的时候指定了大小则创建指定容量大小的数组对象
  
```
// 调用父类的构造函数，创建数组对象
  public StringBuffer(int capacity) {
        super(capacity);
    }
 /**
     * Creates an AbstractStringBuilder of the specified capacity.
     */
    AbstractStringBuilder(int capacity) {
     //按照指定容量创建字符数组   
     value = new char[capacity];
    }

```

- (3)如果在创建对象时构造函数的参数为字符串则 创建的数组的长度为字符长度+16字符
这样的长度，然后再将这个字符串添加到字符数组中，添加的时候会判断原来字符数组中的个数加上这个字符串的长度是否大于这个字符数组的大小如果大于则进行扩容如果没有则添加，源码如下：


```
 public StringBuffer(String str) {
        super(str.length() + 16);
        append(str);
    }
```
append 出现在了这里刚好一起来看看 append方法的实现
4、其实append方法就做两件事，如果 count （字符数组中已有字符的个数）加添加的字符串的长度小于 value.length 也就是小于字符数组的容量则直接将要添加的字符拷贝到数组在修改count就可以了。
5、如果cout和添加的字符串的长度的和大于value.length  则会创建一个新字符数组 再将原有的字符拷贝到新字符数组，再将要添加的字符添加到字符数组中，再改变conut(字符数组中字符的个数)

整个添加过程的源码如下：

```
public synchronized StringBuffer append(Object obj) {
        super.append(String.valueOf(obj));
        return this;
    }
```

调用父类的方法

```
 public AbstractStringBuilder append(Object obj) {
        return append(String.valueOf(obj));
    }
```

这个方法中调用了ensureCapacityInternal （）方法判断count(字符数组原有的字符个数)+str.length() 的长度是否大于value容量

```

     * This method has the same contract as ensureCapacity, but is
     * never synchronized.
     */
    private void ensureCapacityInternal(int minimumCapacity) {
        // overflow-conscious code
        if (minimumCapacity - value.length > 0)
            expandCapacity(minimumCapacity);
    }
```

如果count+str.length() 长度大于value的容量 则调用方法进行扩容
  
```
/**
     * This implements the expansion semantics of ensureCapacity with no
     * size check or synchronization.
     */
    void expandCapacity(int minimumCapacity) {
        int newCapacity = value.length * 2 + 2;
        if (newCapacity - minimumCapacity < 0)
            newCapacity = minimumCapacity;
        if (newCapacity < 0) {
            if (minimumCapacity < 0) // overflow
                throw new OutOfMemoryError();
            newCapacity = Integer.MAX_VALUE;
        }
        value = Arrays.copyOf(value, newCapacity);
    }
```


Arrays.copyOf（value,newCapacity） 复制指定的数组，截取或用 null 字符填充（如有必要），以使副本具有指定的长度。
上面的getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin)   将字符从此字符串复制到目标字符数组dst中，第一个参数 第二个参数截取要添加字符串的长度，第三个为目标字符数组第四个为字符串要添加到数组的开始位置
       
到这里数组的赋值都结束了，修改count的值，整个append也就结束了。

#### StringBuilder
![StringBuilder继承关系](http://orbm62bsw.bkt.clouddn.com/StringBuilder%E7%BB%A7%E6%89%BF%E5%85%B3%E7%B3%BB.png)
跟StringBuffer差不多，一个是线程安全，一个是非线程安全而已

## 总结

- 一旦string对象在内存(堆)中被创建出来，就无法被修改。
- String类的所有方法都没有改变字符串本身的值，都是返回了一个新的对象。
- 如果你需要一个可修改的字符串，应该使用StringBuffer 或者 StringBuilder。
- 否则会有大量时间浪费在垃圾回收上，因为每次试图修改都有新的string对象被创建出来。
- 如果你只需要创建一个字符串，你可以使用双引号的方式，如果你需要在堆中创建一个新的对象，你可以选择构造函数的方式。
- StringBuffer 类被final 修饰所以不能继承没有子类
- StringBuffer 对象是可变对象，因为父类的 value [] char 没有被final修饰所以可以进行引用的改变，而且还提供了方法可以修改被引用对象的内容即修改了数组内容。
- 在使用StringBuffer对象的时候尽量指定大小这样会减少扩容的次数，也就是会减少创建字符数组对象的次数和数据复制的次数，当然效率也会提升。
- StringBuilder 和StringBuffer 很像只是不是线程安全的其他的很像所以不罗嗦了。
