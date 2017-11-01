## 概述

流是一组有顺序的，有起点和终点的字节集合，是对数据传输的总称或抽象。即数据在两设备间的传输称为流，流的本质是数据传输，根据数据传输特性将流抽象为各种类，方便更直观的进行数据操作。

## 正文

### 基础知识

#### 字符跟字节
在Java中有输入、输出两种IO流，每种输入、输出流又分为字节流和字符流两大类。关于字节，在基本数据类型中，每个字节(byte)有8bit组成，每种数据类型又几个字节组成等。字符是指计算机中使用的文字和符号，通常会把String称之为字符串，字符跟字节之间实际上是一个对应关系。在Java中，一个字符表示2个字节，不管是中文还是英文字符，因为java采用的是Unicode编码。但如果采用其他编码方式，一个字符占用的字节数则各不相同。

#### File类
File类是java.io包下代表与平台无关的文件和目录，也就是说，如果希望在程序中操作文件和目录，都可以通过File类来完成。
##### 构造函数
```
File(String pathname)
File(String parent,String child)
File(File parent,String child)
```
**路径**：
- 相对路径：./表示当前路径../表示上一级路径
- 绝对路径：绝对路径名是完整的路径名，不需要任何其他信息就可以定位自身表示的文件

##### 路径分隔符：
- windows： "/" "\" 都可以
- linux/unix： "/"
> 注意:如果windows选择用"\"做分割符的话,那么请记得替换成"\\",因为Java中"\"代表转义字符所以推荐都使用"/"，也可以直接使用代码File.separator，表示跨平台分隔符。

##### 创建与删除方法
```
//如果文件存在返回false，否则返回true并且创建文件 
boolean createNewFile();
//创建一个File对象所对应的目录，成功返回true，否则false。且File对象必须为路径而不是文件。只会创建最后一级目录，如果上级目录不存在就抛异常。
boolean mkdir();
//创建一个File对象所对应的目录，成功返回true，否则false。且File对象必须为路径而不是文件。创建多级目录，创建路径中所有不存在的目录
boolean mkdirs()    ;
//如果文件存在返回true并且删除文件，否则返回false
boolean delete();
//在虚拟机终止时，删除File对象所表示的文件或目录。
void deleteOnExit();
```
##### 判断方法
```
boolean canExecute()    ;//判断文件是否可执行
boolean canRead();//判断文件是否可读
boolean canWrite();//判断文件是否可写
boolean exists();//判断文件是否存在
boolean isDirectory();//判断是否是目录
boolean isFile();//判断是否是文件
boolean isHidden();//判断是否是隐藏文件或隐藏目录
boolean isAbsolute();//判断是否是绝对路径 文件不存在也能判断
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


##### 文件过滤

```
File[] listFiles(FilenameFilter filter);//返回满足过滤器要求的一个文件对象数组
其中包含了一个重要的接口FileNameFilter，该接口是个文件过滤器，包含了一个accept(File dir,String name)方法，该方法依次对指定File的所有子目录或者文件进行迭代，按照指定条件，进行过滤，过滤出满足条件的所有文件。
```

 // 文件过滤
 File[] files = file.listFiles(new FilenameFilter() {
    @Override
  public boolean accept(File file, String filename) {
           return filename.endsWith(".mp3");
            }
        });
```
file目录下的所有子文件如果满足后缀是.mp3的条件的文件都会被过滤出来。

#### RandomAccessFIle
RandomAccessFile是Java中输入，输出流体系中功能最丰富的文件内容访问类，它提供很多方法来操作文件，包括读写支持，与普通的IO流相比，它最大的特别之处就是支持任意访问的方式，程序可以直接跳到任意地方来读写数据。

如果我们只希望访问文件的部分内容，而不是把文件从头读到尾，使用RandomAccessFile将会带来更简洁的代码以及更好的性能。

##### 方法


方法名 | 作用
:---:|:---:
getFilePointer()  | 返回文件记录指针的当前位置
seek(long pos) | 将文件记录指针定位到pos的位置

##### 功能
- 1.读取任意位置的数据
- 2.追加数据
- 3.任意位置插入数据


### 分类

![IO流分类](http://orbm62bsw.bkt.clouddn.com/IO%E6%B5%81.png)

**按类型分**：

- 字节流：字节流主要是操作byte类型数据
- 字符流： 因为数据编码的不同，而有了对字符进行高效操作的流对象。本质其实就是基于字节流读取时，去查了指定的码表。
**区别**：
- 读写单位不同：字节流以字节（8bit）为单位，字符流以字符为单位，根据码表映射字符，一次可能读16位字节。
- 处理对象不同：字节流能处理所有类型的数据（如图片、avi等），而字符流只能处理字符类型的数据。

> 设备上的数据无论是图片或者视频，文字，它们都以二进制存储的。二进制的最终都是以一个8位为数据单元进行体现，所以计算机中的最小数据单元就是字节。意味着，字节流可以处理设备上的所有数据，所以字节流一样可以处理字符数据。

**结论**：只要是处理纯文本数据，就优先考虑使用字符流。 除此之外都使用字节流。

**按照数据流向分**：

- 输入流：InputStream或者Reader：从文件中读到程序中；
- 输出流：OutputStream或者Writer：从程序中输出到文件中；
> 这里的输入和输出都是以程序为参照物

**按照流的角色**
- 节点流：直接与数据源相连，读入或读出，可以从/向一个特定的IO设备（如磁盘、网络）读/写数据的流，称为节点流，节点流也被成为低级流。
- 处理流：处理流是对一个已存在的流进行连接或封装，通过封装后的流来实现数据读/写功能，处理流也被称为高级流。

>当使用处理流进行输入/输出时，程序并不会直接连接到实际的数据源，没有和实际的输入/输出节点连接。使用处理流的一个明显好处是，只要使用相同的处理流，程序就可以采用完全相同的输入/输出代码来访问不同的数据源，随着处理流所包装节点流的变化，程序实际所访问的数据源也相应地发生变化。

**常用的节点流**
父　类 ：InputStream 、OutputStream、 Reader、 Writer
文　件 ：FileInputStream 、 FileOutputStrean 、FileReader 、FileWriter 文件进行处理的节点流
数　组 ：ByteArrayInputStream、 ByteArrayOutputStream、 CharArrayReader 、CharArrayWriter 对数组进行处理的节点流（对应的不再是文件，而是内存中的一个数组）
字符串 ：StringReader、 StringWriter 对字符串进行处理的节点流
管　道 ：PipedInputStream 、PipedOutputStream 、PipedReader 、PipedWriter 对管道进行处理的节点流
**常用的处理流**
缓冲流：BufferedInputStrean 、BufferedOutputStream、 BufferedReader、 BufferedWriter 增加缓冲功能，避免频繁读写硬盘。
转换流：InputStreamReader 、OutputStreamReader实现字节流和字符流之间的转换。
数据流： DataInputStream 、DataOutputStream 等-提供将基础数据类型写入到文件中，或者读取出来。
转换流

InputStreamReader 、OutputStreamWriter 要InputStream或OutputStream作为参数，实现从字节流到字符流的转换。

### 父类介绍

#### InputStream

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
输出字节流 OutputStream
OutputStream 是所有的输出字节流的父类，它是一个抽象类。
ByteArrayOutputStream、FileOutputStream 是两种基本的介质流，它们分别向Byte 数组、和本地文件中写入数据。
PipedOutputStream 是向与其它线程共用的管道中写入数据。
ObjectOutputStream 和所有FilterOutputStream 的子类都是装饰流。

#### Writer

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



### IO流与装饰者模式

实际上，Java使用处理流来包装节点流是一种典型的装饰器设计模式，通过使用处理流来包装不同的节点流，既可以消除不同节点流的实现差异，也可以提供更方便的方法来完成输入/输出功能。
