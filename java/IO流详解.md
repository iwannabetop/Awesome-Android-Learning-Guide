## 概述
流是一组有顺序的，有起点和终点的字节集合，是对数据传输的总称或抽象。即数据在两设备间的传输称为流，流的本质是数据传输，根据数据传输特性将流抽象为各种类，方便更直观的进行数据操作。IO其实有两类，一类是BIO(BlockingIO)，一类是NIO(Non-BlockingIO)，不过我们通常说的是IO默认指的是BIO;


## 正文

### 基础知识
#### 字符
**字节**是计算机中存储数据的单元，一个8位的二进制数，是一个很具体的存储空间。
####字符编码

**字符**是指人们使用的记号，抽象意义上的一个符号，比如1、2、3、·#￥%。
####字节

**字符编码**（Character encoding）是一套法则，使用该法则能够对自然语言的字符的一个集合（如字母表或音节表），与其他东西的一个集合进行配对。各个国家和地区所制定的不同 ANSI 编码标准中，都只规定了各自语言所需的字符。
#### 常见的编码方式
**ASCII编码**：美国制定了一套字符编码，对英语字符与二进制位之间的关系，做了统一规定。这被称为 ASCII 码，一直沿用至今。
**非 ASCII 编码**：英语用128个符号编码就够了，但是用来表示其他语言，128个符号是不够的，所以在别的国家编码符号会比128要多，所以问题就出现了，不同的国家有不同的字母，因此，哪怕它们都使用256个符号的编码方式，代表的字母却不一样。
**UTF-8编码**：UTF-8 最大的一个特点，就是它是一种变长的编码方式。它可以使用1~4个字节表示一个符号，根据不同的符号而变化字节长度。。UTF-8 就是在互联网上使用最广的一种 Unicode 的实现方式。其他实现方式还包括 UTF-16（字符用两个字节或四个字节表示）和 UTF-32（字符用四个字节表示），不过在互联网上基本不用。重复一遍，这里的关系是，UTF-8 是 Unicode 的实现方式之一。

### 联系与区别
很多时候我们经常提及到字符跟字节之间的关系，这个问题的前提是基于某一种编程语言比如说Java或者C来说的，因为字符跟字节之间的关系跟字符编码是有着紧密联系的，所以单独讨论字符跟字节之间的关系没有意义，下面简单来看一下他们在不同编码上的的对应关系：
语言 | 中文字符| 英文字符
---|---|---
GBK | 2个字节| 1个字节
UTF-8| 2个字节| 2个字节
java语言默认是采用Utf-8来进行编码的，下面用Java来测试一下：

测试GBK编码

       public static void main(String[] args) {
        String str = "Hello_安卓";
        int byte_len = 0;
        try {
            byte_len = str.getBytes("gbk").length;
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        System.out.println("字节长度：" + byte_len);

    }

```
字节长度：10
```

测试UTF-8编码

    public static void main(String[] args) {
        String str = "Hello_安卓";
        int byte_len =str.getBytes().length;
        System.out.println("字节长度：" + byte_len);
   
    }
```
字节长度：12
```

输出的结果，跟之前的规则是一致的，到这里，字节，编码方式，字符，以及他们之间的联系基本上介绍完了，理解了他们之间的关系，下面的File类跟IO之间的关系也就比较好理解了。

#### File类


**File**类翻译过来是一个文件，实际上它并不是一个文件，定义为Path更为合适，这个Path可以是文件的路径也可以是文件夹的路径，因为当我们new File的时候，只是创建了一个路径，这个路径如果创建成功，没有后缀名就是文件夹，有后缀名则创建了一个空文件。下面看一下File类的继承关系：

![File的继承关系](http://orbm62bsw.bkt.clouddn.com/File%E7%9A%84%E7%BB%A7%E6%89%BF%E5%85%B3%E7%B3%BB.png)

##### 构造函数
列举几个常见的构造函数
```
File(String pathname)
File(String parent,String child)
File(File parent,String child)
```
>因为Java命名比较规范，所以很好理解，有一点需要注意的是，这个方法不能保证文件一定会创建成功，但是即使失败也不会报异常，所以一般我们在文件创建之后需要判断一下当前文件是否创建成功，调用一下  exists()方法来判断文件是否创建成功，不成功则调用createNewFile(),此方法失败会抛异常，归纳起来就是：

        File file=new File("E:\\demo","a.txt");
        if (file.exists()){
            //继续文件的操作
        }else {
            try {
                boolean result = file.createNewFile();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }


**路径**：
- 相对路径：./表示当前路径../表示上一级路径
- 绝对路径：绝对路径名是完整的路径名，不需要任何其他信息就可以定位自身表示的文件

##### 路径分隔符：
- windows： "/" "\" 都可以
- linux/unix： "/"
> 注意:如果windows选择用"\"做分割符的话,那么请记得替换成"\\",因为Java中"\"代表转义字符所以推荐都使用"/"，也可以直接使用代码File.separator，表示跨平台分隔符。

##### 创建与删除
```
boolean createNewFile();//创建具体的文件
boolean mkdir();//创建单个目录
boolean mkdirs();//创建多个目录
boolean delete();//删除File
```
##### 判断方法
```
boolean canRead();//判断文件是否可读
boolean canWrite();//判断文件是否可写
boolean exists();//判断文件是否存在
boolean isDirectory();//判断是否是目录
boolean isFile();//判断是否是文件
boolean isAbsolute();//判断是否是绝对路径
```
###### 获取方法
```
String getName();//返回文件或者是目录的名称
String getPath();//返回路径
String getAbsolutePath();//返回绝对路径
String getParent();//返回父目录，如果没有父目录则返回null
long lastModified();//返回最后一次修改的时间
long length();//返回文件的长度
File[] listRoots();// 列出所有的根目录（Window中就是所有系统的盘符）
String[] list() ;//返回一个字符串数组，给定路径下的文件或目录名称字符串
String[] list(FilenameFilter filter);//返回满足过滤器要求的一个字符串数组
File[]  listFiles();//返回一个文件对象数组，给定路径下文件或目录

```
##### 文件过滤
File[] listFiles(FilenameFilter filter);//返回满足过滤器要求的一个文件对象数组
其中包含了一个重要的接口FileNameFilter，该接口是个文件过滤器，包含了一个accept(File dir,String name)方法，该方法依次对指定File的所有子目录或者文件进行迭代，按照指定条件，进行过滤，过滤出满足条件的所有文件。
```

 // 文件过滤
 File[] files = file.listFiles(new FilenameFilter() {
    @Override
  public boolean accept(File file, String filename) {
           return filename.endsWith(".apk");
            }
        });
```
file目录下的所有子文件如果满足后缀是.apk的条件的文件都会被过滤出来。


### 分类

**按数据类型分**：
![IO流分类](http://orbm62bsw.bkt.clouddn.com/IO.png)
-
- 字节流：字节流主要是操作byte类型数据
- 字符流： 因为数据编码的不同，而有了对字符进行高效操作的流对象。本质其实就是基于字节流读取时，去查了指定的码表。

**区别**：
- 读写单位不同：字节流以字节（8bit）为单位，字符流以字符为单位，根据码表映射字符，一次可能读16位字节。
- 处理对象不同：字节流能处理所有类型的数据（如image、avi等），而字符流只能处理字符类型的数据。


> 设备上的数据无论是图片或者视频，文字，它们都以二进制存储的。二进制的最终都是以一个8位为数据单元进行体现，所以计算机中的最小数据单元就是字节。意味着，字节流可以处理设备上的所有数据，所以字节流一样可以处理字符数据。

**结论**：只要是处理纯文本数据，就优先考虑使用字符流。换句话说，能使用字符流的一定也可以使用字节流。

**按照数据流向分**：
![IO流按照流向分](http://orbm62bsw.bkt.clouddn.com/IO%E6%B5%81%E7%9A%84%E6%B5%81%E5%90%91.png)
- 输入流：InputStream或者Reader：从文件中读到程序中；
- 输出流：OutputStream或者Writer：从程序中输出到文件中；
> 这里的输入和输出都是以程序为参照物

**按照流的角色分**
![IO流按照角色分](http://orbm62bsw.bkt.clouddn.com/IO%E6%B5%81%E6%8C%89%E7%85%A7%E6%B5%81%E7%9A%84%E8%A7%92%E8%89%B2%E5%88%86.png)

- 节点流：直接与数据源相连，读入或读出，可以从/向一个特定的IO设备（如磁盘、网络）读/写数据的流，称为节点流，节点流也被成为低级流。
- 处理流：处理流是对一个已存在的流进行连接或封装，通过封装后的流来实现数据读/写功能，处理流也被称为高级流。

>当使用处理流进行输入/输出时，程序并不会直接连接到实际的数据源，没有和实际的输入/输出节点连接。使用处理流的一个明显好处是，只要使用相同的处理流，程序就可以采用完全相同的输入/输出代码来访问不同的数据源，随着处理流所包装节点流的变化，程序实际所访问的数据源也相应地发生变化。

**常用的节点流**
父　类 ：InputStream 、OutputStream、 Reader、 Writer
文　件 ：FileInputStream 、 FileOutputStrean 、FileReader 、FileWriter 文件进行处理的节点流
数　组 ：ByteArrayInputStream、 ByteArrayOutputStream、 CharArrayReader 、CharArrayWriter 对数组进行处理的节点流（对应的不再是文件，而是内存中的一个数组）
字符串 ：StringReader、 StringWriter 对字符串进行处理的节点流
管　道 ：PipedInputStream 、PipedOutputStream 、PipedReader 、PipedWriter 对管道进行处理的节点流

ByteArrayOutputStream、FileOutputStream 是两种基本的介质流，它们分别向Byte 数组、和本地文件中写入数据。
PipedOutputStream 是向与其它线程共用的管道中写入数据。
ObjectOutputStream 和所有FilterOutputStream 的子类都是装饰流。

**常用的处理流**
缓冲流：BufferedInputStrean 、BufferedOutputStream、 BufferedReader、 BufferedWriter 增加缓冲功能，避免频繁读写硬盘。
转换流：InputStreamReader 、OutputStreamReader实现字节流和字符流之间的转换。
数据流： DataInputStream 、DataOutputStream 等-提供将基础数据类型写入到文件中，或者读取出来。
转换流

InputStreamReader 、OutputStreamWriter 要InputStream或OutputStream作为参数，实现从字节流到字符流的转换。

### 父类介绍

#### InputStream
![InputStream继承关系](http://orbm62bsw.bkt.clouddn.com/InputStream.png)

InputStream 是所有的输入字节流的父类，它是一个抽象类，主要包含三个方法：

```
//读取一个字节并以整数的形式返回(0~255),如果返回-1已到输入流的末尾。 
int read() ； 
//读取一系列字节并存储到一个数组buffer，返回实际读取的字节数，如果读取前已到输入流的末尾返回-1。 
int read(byte[] buffer) ； 
//读取length个字节并存储到一个字节数组buffer，从off位置开始存,最多len， 返回实际读取的字节数，如果读取前以到输入流的末尾返回-1。 
int read(byte[] buffer, int off, int len) ；
```
ByteArrayInputStream、StringBufferInputStream、FileInputStream 是三种基本的介质流，它们分别从Byte 数组、StringBuffer、和本地文件中读取数据。
PipedInputStream 是从与其它线程共用的管道中读取数据，与Piped 相关的知识后续单独介绍。
ObjectInputStream 和所有FilterInputStream 的子类都是装饰流（装饰器模式的主角）

#### Reader
![Reader继承关系](http://orbm62bsw.bkt.clouddn.com/Reader.png)
Reader 是所有的输入字符流的父类，它是一个抽象类，主要包含三个方法：

```
//读取一个字符并以整数的形式返回(0~255),如果返回-1已到输入流的末尾。 
int read() ； 
//读取一系列字符并存储到一个数组buffer，返回实际读取的字符数，如果读取前已到输入流的末尾返回-1。 
int read(char[] cbuf) ； 
//读取length个字符,并存储到一个数组buffer，从off位置开始存,最多读取len，返回实际读取的字符数，如果读取前以到输入流的末尾返回-1。 
int read(char[] cbuf, int off, int len)
```
对比InputStream和Reader所提供的方法，就不难发现两个基类的功能基本一样的，只不过读取的数据单元不同。

在执行完流操作后，要调用close()方法来关系输入流，因为程序里打开的IO资源不属于内存资源，垃圾回收机制无法回收该资源，所以应该显式关闭文件IO资源。

除此之外，InputStream和Reader还支持如下方法来移动流中的指针位置：

```
//在此输入流中标记当前的位置
//readlimit - 在标记位置失效前可以读取字节的最大限制。
void mark(int readlimit)
// 测试此输入流是否支持 mark 方法
boolean markSupported()
// 跳过和丢弃此输入流中数据的 n 个字节/字符
long skip(long n)
//将此流重新定位到最后一次对此输入流调用 mark 方法时的位置
void reset()
```
#### OutputStream
![OutputStream继承关系](http://orbm62bsw.bkt.clouddn.com/OutputStream.png)
OutputStream 是所有的输出字节流的父类，它是一个抽象类，主要包含如下四个方法：
```
//向输出流中写入一个字节数据,该字节数据为参数b的低8位。 
void write(int b) ; 
//将一个字节类型的数组中的数据写入输出流。 
void write(byte[] b); 
//将一个字节类型的数组中的从指定位置（off）开始的,len个字节写入到输出流。 
void write(byte[] b, int off, int len); 
//将输出流中缓冲的数据全部写出到目的地。 
void flush();
```

#### Writer
![Writer继承关系](http://orbm62bsw.bkt.clouddn.com/Writer.png)

Writer 是所有的输出字符流的父类，它是一个抽象类,主要包含如下六个方法：

```
//向输出流中写入一个字符数据,该字节数据为参数b的低16位。 
void write(int c); 
//将一个字符类型的数组中的数据写入输出流， 
void write(char[] cbuf) 
//将一个字符类型的数组中的从指定位置（offset）开始的,length个字符写入到输出流。 
void write(char[] cbuf, int offset, int length); 
//将一个字符串中的字符写入到输出流。 
void write(String string); 
//将一个字符串从offset开始的length个字符写入到输出流。 
void write(String string, int offset, int length); 
//将输出流中缓冲的数据全部写出到目的地。 
void flush()
```
可以看出，Writer比OutputStream多出两个方法，主要是支持写入字符和字符串类型的数据。

> 使用Java的IO流执行输出时，不要忘记关闭输出流，关闭输出流除了可以保证流的物理资源被回收之外，还能将输出流缓冲区的数据flush到物理节点里（因为在执行close()方法之前，自动执行输出流的flush()方法）

#### IO中的一股清流——RandomAccessFIle
![RandomAccessFIle继承关系](http://orbm62bsw.bkt.clouddn.com/RandomAccessFile.png)
> 我们发现RandomAccessFIle跟File类并没有联系，只是实现了DataOutput跟DataInput两个接口，完全自己重新定义了一遍File的读取操作

RandomAccessFile是Java中输入，输出流体系中功能最丰富的文件内容访问类，它提供很多方法来操作文件，包括读写支持，与普通的IO流相比，它最大的特别之处就是支持任意访问的方式，程序可以直接跳到任意地方来读写数据。

如果我们只希望访问文件的部分内容，而不是把文件从头读到尾，使用RandomAccessFile将会带来更简洁的代码以及更好的性能。

##### 方法

方法名 | 作用
:---:|:---:
getFilePointer() | 返回文件记录指针的当前位置
seek(long pos) | 将文件记录指针定位到pos的位置

##### 功能
- 1.读取任意位置的数据
- 2.追加数据
- 3.任意位置插入数据

### NIO
Java NIO是java 1.4之后新出的一套IO接口，这里的的新是相对于原有标准的Java IO和Java Networking接口。NIO提供了一种完全不同的操作方式。标准的IO编程接口是面向**字节流**和**字符流**的。而NIO是面向**Channel**(通道)和**Buffer**(缓冲区)的，数据总是从Channel中读到Buffer内，或者从Buffer写入到Channel中，Channel是需要注册到Selector(选择器)中去。我们知道不管是NIO还是BIO在读写数据的过程中都有两个操作：等待就绪和操作。举例来说，读函数，分为等待系统可读和真正的读；同理，写函数分为等待网卡可以写和真正的写。NIO跟BIO的操作都是一样的，区别在于等待就绪的这一过程，看看下面这张图：
![阻塞式IO](http://orbm62bsw.bkt.clouddn.com/%E9%98%BB%E5%A1%9E%E5%BC%8FIO.png)

![非阻塞式IO](http://orbm62bsw.bkt.clouddn.com/%E9%9D%9E%E9%98%BB%E5%A1%9E%E5%BC%8FIO.png)

> NIO跟BIO的区别在于不管现在有没有数据，都会给调用者一个返回值，在没有准备就绪之前，当前线程可以进行其他的操作，而不会阻塞。

#### 缓冲区
![Buffer的实现关系](http://orbm62bsw.bkt.clouddn.com/Buffer.png)
缓冲区实质上就是一个数组，但它不仅仅是一个数组，缓冲区还提供了对数据的结构化访问，而且还可以跟踪系统的读/写进程。

#### 通道
![Channel的实现关系](http://orbm62bsw.bkt.clouddn.com/Channel%E7%9A%84%E5%AE%9E%E7%8E%B0%E5%85%B3%E7%B3%BB.png)
通道用于在缓冲区和位于通道另一侧的实体（文件、套接字）之间有效的传输数据

#### 选择器
![Selector的实现关系](http://orbm62bsw.bkt.clouddn.com/Selector.png)
选择器类Selector并没有和通道有直接的关系，而是通过叫选择键的对象SelectionKey来联系的,而且Selector可以注册过个key，也就是说可以同时管理多个通道。选择键代表了通道与选择 器之间的一种注册关系，channel()和selector()方法分别返回注册的通道与选择器。

**工作原理**
NIO调用不会被阻塞，在IO开始的时候需要在分发器那里注册感兴趣的事件，并提供相应的处理者（event handler)，或者是回调函数；事件分发器在适当的时候，会将请求的事件分发给这些handler或者回调函数：如可读数据到达，新的套接字连接等等，在发生特定事件时，系统再通知我们。NIO中实现非阻塞I/O的核心对象就是Selector，Selector就是注册各种I/O事件地 方，而且当那些事件发生时，就是这个对象告诉我们所发生的事件，如下图所示：
![NIO工作原理](http://orbm62bsw.bkt.clouddn.com/NIO%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.png)
>从图中可以看出，当有读或写等任何注册的事件发生时，可以从Selector中获得相应的SelectionKey，同时从 SelectionKey中可以找到发生的事件和该事件所发生的具体的SelectableChannel，以获得客户端发送过来的数据。

#### 如何选择
NIO的优势在于**单线程管理多个连接**，可以在线程数较少的情况下实现快速地读取，当连接数<1000，并发程度不高并没有显著的性能优势。NIO可让您只使用一个（或几个）单线程管理多个通道（网络连接或文件），但付出的代价是解析数据可能会比从一个阻塞流中读取数据更复杂。如果需要管理同时打开的成千上万个连接，这些连接每次只是发送少量的数据，例如聊天服务器，实现NIO的服务器可能是一个优势。如果你有少量的连接使用非常高的带宽，一次发送大量的数据，也许典型的BIO服务器实现可能非常契合。


### IO与装饰者模式
其实装饰者模式在IO中的运用非常广泛，先看一下什么是装饰者。
**装饰者(Decorator)模式**：动态将职责附加到对象上，若要扩展功能，装饰者提供了比继承更具弹性的代替方案。
**设计原则**：开闭原则(一个软件实体如类、模块和函数应该对扩展开放，对修改关闭)。
下面用图简单描述一下：
![装饰者模式图解]](http://orbm62bsw.bkt.clouddn.com/%E8%A3%85%E9%A5%B0%E8%80%85%E6%A8%A1%E5%BC%8F%E6%96%B0.png)

下面解释一下这几个变量：
- Component：抽象组件
- ConcreteComponent：抽象组件的具体实现类
- Decorator: 装饰者的抽象类，继承自Component
- DecoratorA/B：具体的装饰实现类

通过装饰者对已有的对象进行包装，可以扩展已有类的方法跟属性，下面通过代码来说明：

Component
```
public abstract class Component {
    protected String description;

    protected abstract String getDescription();

    public abstract int getAge();

}
```
ConcreteComponent
```
public class ConcreteComponent extends Component {
    public ConcreteComponent() {
        description = "ConcreteComponent";
    }

    @Override
    protected String getDescription() {
        return description;
    }

    @Override
    public int getAge() {
        return 10;
    }
}
```
Decorator
```
public abstract class Decorator extends Component {
//空实现，具体的实现放在子类
}
```
DecoratorA/B
```
public class DecoratorA extends Decorator {
    private Component mComponent;

    public DecoratorA(Component component) {
        this.mComponent = component;
    }

    @Override
    protected String getDescription() {
        return mComponent.getDescription();
    }

    @Override
    public int getAge() {
        return mComponent.getAge() + 1;
    }

    public String getTime() {
        Date date = new Date();
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        return sdf.format(date);
    }

}
```
测试代码：
```
Component component = new ConcreteComponent();
        System.out.println("装饰前的参数：" + "描述：" + component.getDescription() + "   年龄：" + component.getAge());
        DecoratorA decoratorA = new DecoratorA(component);
        System.out.println("装饰后的参数：" + "描述：" + decoratorA.getDescription() + "   年龄
```
使用装饰者模式之后，不修改Description，将age+1，同时增加一个getTime方法来获取当前的时间

输出结果
```
装饰前的参数：描述：ConcreteComponent   年龄：10
装饰后的参数：描述：ConcreteComponent   年龄：11 时间：2017-11-04 15:11:03
```
跟我们预期的一样，不再赘述。下面看看IO中的设计模式，以OutpuStream为例：
![OutputStream中的装饰者模式](http://orbm62bsw.bkt.clouddn.com/OutputStream%E7%9A%84%E8%A3%85%E9%A5%B0%E8%80%85%E6%A8%A1%E5%BC%8F.png)

对照着上面的装饰者模式图应该很容易看出来，FileterOutputStream就是我们装饰者的抽象类，看一下他的构造方法,确实装饰了OutputStream ：
```
  public FilterOutputStream(OutputStream out) {
        this.out = out;
    }
```
### Android中的"path"
在开发Android的过程中，也会涉及到很多的IO操作，比如说网络请求，下载图片等，由于很多框架平时已经帮我们封装好了，所以平时容易忽略，下面简单分析一下Android下的存储目录：
![Android平台的存储目录](http://orbm62bsw.bkt.clouddn.com/Android%E5%AD%98%E5%82%A8%E7%9B%AE%E5%BD%95.png)

#### 内部存储
data文件夹就是我们常说的内部存储，对于没有root的手机来说，我们是没有权限打开这个文件夹的但是可以访问到，



#### 外部存储
外部存储才是我们平时操作最多的，外部存储一般就是我们上面看到的storage文件夹，当然也有可能是mnt文件夹，这个名称不影响我们操作数据。

#### 路径获取
两种存储方式都是通过Context类来进行获取的
**内部存储**：
   ```
      getFilesDir();//获取内部存储的File路径
      getCacheDir();//获取内部存储的Cache路径
      getDatabasePath("demo.db");//获取database路径
      getSharedPreferences("demo",MODE_PRIVATE);//获取SP
   ```
**外部存储**：
```
   getExternalCacheDir();//获取外部存储私有目录
   getExternalFilesDir(Environment.DIRECTORY_DCIM);//获取外部存储公有目录
      
```
#### 清除缓存/清除数据
**清除缓存**：缓存是程序运行时的临时存储空间，它可以存放从网络下载的临时图片，从用户的角度出发清除缓存对用户并没有太大的影响，但是清除缓存后用户再次使用该APP时，由于本地缓存已经被清理，所有的数据需要重新从网络上获取，注意：为了在清除缓存的时候能够正常清除与应用相关的缓存，请将缓存文件存放在getCacheDir()或者 getExternalCacheDir()路径下。
**清除数据**：清除用户配置，比如SharedPreferences、数据库等等，这些数据都是在程序运行过程中保存的用户配置信息，清除数据后，下次进入程序就和第一次进入程序时一样

#### 关于权限
Android6.0以后，谷歌加强了对用户权限的控制，但是这个权限只是针对于外部存储的公有目录，对于私有目录的，仍然可以正常访问。所以当遇到有些手机权限很难适配的时候可以把文件存储在外部存储的私有目录。


### 参考资料
[https://tech.meituan.com/nio.html](https://tech.meituan.com/nio.html)
