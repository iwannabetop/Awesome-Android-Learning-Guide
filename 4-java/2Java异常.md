
# 深入了解 Java 异常


读完本文你将了解：

* 什么是异常，异常的分类有哪些？
* ``Error`` 和 ``Exception`` 的区别? 
* 常见运行时异常(``RuntimeException``)和检查型异常(``CheckedException``) 有哪些？
* 异常的机制(异常处理的流程)？
* 异常的使用方法(如何抛出异常，如何捕获异常，再次抛出异常)?
* ``throws``, ``throw``, ``try``, ``catch``, ``finally`` 分别代表什么意义？
* 什么是“异常链”?
* 怎么自定义异常？
* Java 异常类有哪些的重要方法？

## 文章出处

>文章出自：[安卓进阶学习指南](https://github.com/iwannabetop/Awesome-Android-Learning-Guide)
>
>作者：[justdoitJX](https://github.com/justdoitJX), [Milo] (https://github.com/hzmeibi)
>
>审核者:[YangZhe](https://github.com/StudyLifeTime)


# 背景介绍

![这里写图片描述](http://img.blog.csdn.net/20171031224622133?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTI0MDg3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

大家好，这篇文章是 [《安卓进阶技能树计划》](https://github.com/iwannabetop/Awesome-Android-Learning-Guide) 的第一部分 [《Java 基础系列》](https://github.com/iwannabetop/Awesome-Android-Learning-Guide/blob/master/java/README.md) 的第二篇。

距离上一篇预告 [《Java 基础夯实1：细谈抽象类和接口》](https://mp.weixin.qq.com/s?__biz=MzI1ODQ3NDA2Mg==&tempkey=OTMxX3BLdVE3eURhMXh6T09LUERRdzBKcUw1d3JPRDUxLXAteWVKZl94WjNJamVjd3EwZmg0cnRkYU55dy1VMHlHbXRfMUhMNGdUOE5iTUVtbEtZMUFpTTJhak5lNlJTUXB5NzlHaDVtekVGOWFnbDRpTDlNQmFaVHlQdGR1aUxlTnczQmp6ZlkxUzdVM2lzVDFPM1lPemdYOE1CczA3aE1yd0FuMjJjbUF%2Bfg%3D%3D&chksm=6a06ea2a5d71633ca0ab719797250935409b70a04a317473031fff3a5ae129ce5b96abf69dcd#rd) 过去了很久，之所以这么慢，是因为我们做这个活动，除了要保证知识点的全面、完整，还想要让每一篇文章都有自己的思考，尽可能的将知识点与实践结合，努力让读者读了有所收获。每位小伙伴都有工作在身，每个知识点都需要经过**思考、学习、写作、提交、审核、修改、编辑、发布等多个过程**，所以整体下来时间就会慢一些，这里先向各位道歉。

《Java 基础系列》初步整理大概有 12 篇，主要内容为：

1. 抽象类和接口
2. 内部类
3. 修饰符
4. 装箱拆箱
5. 注解
6. 反射
7. 泛型
8. 异常
9. 集合
10. IO
11. 字符串
12. 其他

由于最近 996，我约好的文章跳票了，实在不好意思 （°ο°）。好在有小伙伴挺身而出，完成了这篇很棒的文章，这让我在寒冷的冬天感受到爱的温暖。有小伙伴一起学习的路，比自己独自前行要轻松多了！

这篇我们来聊聊 **Java 异常**。

“Java 异常” 听起来是非常普遍的东西，有些朋友会觉得：这个太基础了吧，有啥好说的，你又来糊弄我。

![这里写图片描述](http://img.blog.csdn.net/20171031231349683?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTI0MDg3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

哈哈，和上一篇类似的对白，不是我懒，而是在整理这系列文章时，我们不仅一次地发现以前不以为然的基础知识，比自己想的要复杂的多，深刻认识到自己基础的不扎实。

**如果你很自信，可以不看内容，留言回答下文章首部的那些问题，看看你水平如何** (ง •̀_•́)ง 。

言归正传。

# 什么是异常
**异常是发生在程序执行过程中阻碍程序正常执行的错误事件；**

比如打开的文件不存在、网络连接中断、操作数组越界等都会导致出现异常。


## 异常的分类
![](http://upload-images.jianshu.io/upload_images/8805388-83b6ddb35000a43a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

异常的基类为 ``Throwable``，``Error`` 和 ``Exception`` 都继承自 ``Throwable``。

``Error``: 程序无法处理的错误。

这些错误表示故障发生于虚拟机自身、或者发生在虚拟机试图执行应用时，一般不需要程序处理。 

``Exception``: 代码异常

这种异常分两大类 ``RuntimeException``(运行时异常)和 ``CheckedException``(编译时异常);

常见的 ``RuntimeException`` 有：

	ArrayIndexOutOfBoundsException(数组索引越界异常)
	NullPointerException(空指针异常)
	ClassNotFoundException(找不到类异常)
	IllegalArgumentException(非法参数异常)
	SecurityException(安全性异常)
	
常见的 ``CheckedException`` 有：

	NoSuchMethodException(方法未找到抛出的异常)
	ClassCastException(类型转换异常类)
	NumberFormatException(字符串转换为数字抛出的异常)
	IOException(操作输入流和输出流时可能出现的异常)

## Java 异常核心

>关键字：try、catch 、finally 、throw 、throws 详细介绍

我们通过下面的例子分析异常代码的执行、处理机制：

```
public class ExceptionTest {
    public static void doWork() {
        try {
            int i = 10 / 0;    //会抛出异常
            System.out.println("i=" + i);
        } catch (ArithmeticException e) {  //捕获异常
            //捕获 ArithmeticException
            System.out.println("ArithmeticException: " + e);
        } catch (Exception e) {
            //捕获 Exception
            System.out.println("Exception: " + e + "；又抛出了");
            throw e;  //再次抛出
        } finally {
            System.out.println("finally 执行到这了");
        }
    }

    public static void mayHaveTroube() throws Exception { //throws
        //do something
    }

    public static void main(String[] args) {
        doWork();
    }
}
```

运行结果：

```
ArithmeticException: java.lang.ArithmeticException: / by zero
finally 执行到这了

Process finished with exit code 0
```

#### 1）异常处理机制

我们知道除数不能为 0，上述程序执行时会出现运行时异常。

异常处理机制大致如下：

- 异常出现时，会像 Java 中其他对象的创建一样，使用 new 在堆上创建异常对象
- 然后终止当前的执行路径，并且从当前环境中弹出异常对象的引用
- 此时，异常机制接管程序，并开始**寻找一个恰当的地方继续执行程序**
- 这个恰当的地方就是异常处理程序，它的任务是将程序从错误状态中恢复，以使程序能要么换一种方式运行，要么继续下去。

#### 2）抛出异常的方式

**使用 ``throw`` 抛出异常**：

``throw`` 总是出现在**函数体**中，用来抛出一个 ``Throwable`` 类型的异常，例如抛出一个 ``IOException`` 类的异常对象：

``` throw new IOException``` 

程序会在 ``throw`` 语句后立即终止，它后面的语句执行不到，然后在包含它的所有 try 块中（可能在上层调用函数中）从里向外寻找含有与其匹配的 catch 子句的 try 块。

**使用 ``throws`` 抛出异常**:

如果一个方法可能会出现异常，但没有能力处理这种异常，可以在**方法声明处**用 ``throws`` 子句来声明抛出异常，比如：

```
    public static void mayHaveTroube() throws Exception { //throws
        //do something
    }
```

``throws`` 语句用在方法定义时声明该方法要抛出的异常类型，如果抛出的是 ``Exception`` 异常类型，则该方法被声明为抛出所有的异常。

多个异常可使用逗号分割，语法格式为：

``` 
methodname throws Exception1,Exception2,..,ExceptionN {
	//...
}
``` 

当方法抛出异常列表中的异常时，方法将不对这些类型及其子类类型的异常作处理，而抛向调用该方法的方法去处理。

#### 3）重新抛出异常

有时我们会把捕获的异常重新抛出，尤其是在使用 ``Exception`` 捕获所有异常的时候，可能我们只能处理其中的一部分，另外一部分还是教给调用方来解决吧。

既然已经得到了对当前异常的引用，可以这样把它重新抛出：

``` 
catch(Exception e){
	System.out,println("An exception was thrown");
	throw e;
}
```
 
**重抛异常会把异常抛给上一级环境中的异常处理程序，同一个 try 块的后续 catch 子句将被忽略，但 finally 语句会被执行。**


> *注意*：

1. 如果是 ``Error``、``RuntimeException`` 或它们的子类，那么不可使用 ``throws`` 关键字来声明要抛出的异常，虽然编译可以通过，但是在运行时会被系统抛出。

2. 如果一个方法出现编译时异常，要么用 ``try-catch`` 语句捕获，要么用 ``throws`` 子句声明将它抛出，否则会导致编译错误。

3. 仅当抛出了异常，该方法的调用者才必须处理或者重新抛出该异常。

4. 调用方法必须遵循任何可查异常的处理和声明规则。若覆盖一个方法，则不能声明与覆盖方法不同的异常。声明的任何异常必须是被覆盖方法所声明异常的同类或子类。

#### 4）捕获异常

在 Java 中，异常通过 ``try-catch  语句捕获，其语法形式为：

``` 
try {  
    // 可能会发生异常的程序代码  
} catch (Type1 id1){  
    // 捕获并处置try抛出的异常类型Type1  
}  
catch (Type2 id2){  
     //捕获并处置try抛出的异常类型Type2  
}  
``` 

异常处理程序必须紧跟在 try 块之后。

当异常被抛出时，异常处理机制将负责搜寻参数与异常类型想匹配的**第一个处理程序**。

然后进入 ``catch`` 子句执行，此时认为异常的得到了处理。

一旦 ``catch`` 子句结束，则处理程序的查找过程结束。只有匹配的 ``catch`` 子句才能得到执行。

可以写一个 ``Exception`` 异常处理程序来捕获所有类型的异常。

``` 
catch(Exception e){
	System.out.println("Caught an exception");
}
``` 

这将捕获所有异常，所以最好把它放在程序列表的末尾，以防它抢在其他处理程序之前先把异常捕获了。

因为 ``Exception`` 是与编程有关的所有异常类的基类，所以他不会含有太多具体的信息，不过我们可以调用它从其基类 ``Throwable`` 继承的方法：

```
String getMessage()
 
String getLocalizedMessage()
```

用来获取详细信息，或用本地语言表示的详细信息。


```
String toString()
```  

返回对Throwable的简单描述，要是有详细信息的话，也会把它包含在内。

```
void printStackTrace()
 
void printStackTrace(PrintStream)
 
void printStackTrace(java.io.PrintWriter) 
```

打印 ``Throwable`` 和 ``Throwable`` 的调用栈轨迹。其中第一个版本输出到标准错误，后两个版本允许选择输出的流。

```
Throwable fillInStackTrace()
```

用于在 ``Throwable`` 对象的内部记录栈帧的当前状态。这在程序重新抛出错误或异常时很有用。

此外，也可以使用 ``Throwable`` 从基类 ``Object`` 继承的方法。


#### 5）异常链

我们常常会想要在捕获一个异常后抛出另一个异常，并且希望把原始异常的信息保存下来，这被称为异常链。

现在所有 ``Throwable`` 的子类在构造器中都可以接受一个 ``cause``(异常因由)对象作为参数。

这个 ``cause`` 就用来表示原始异常，这样通过把原始异常传递给新的异常，使得即使当前位置创建并抛出了新的异常，也能通过这个异常链追踪到异常最初发生的位置。

#### 6）finally

>敲黑板，这一部分是常考重点。
>

无论是否捕获或处理异常，``finally`` 块里的语句都会被执行，甚至在异常没有被当前的异常处理程序捕获的情况下，异常处理机制也会在跳到更高一层的异常处理程序**之前**，执行 ``finally`` 子句，当涉及 ``break`` 及 ``continue`` 语句的时候，``finally`` 子句也会得到执行。

当要把除内存之外的资源恢复到它们的初始状态时，就要用到 ``finally`` 子句，如已经打开的文件或网络连接，在屏幕上画的图形，甚至可以是外部世界的某个开关。

当在 ``try`` 块或 ``catch`` 块中遇到 ``return`` 语句时，``finally`` 语句块将在方法返回之前被执行。

**在以下 4 种特殊情况下，``finally`` 块不会被执行：**

1. 在 ``finally`` 语句块中发生了异常。
2. 在前面的代码中用了 ``System.exit()`` 退出程序。
3. 程序所在的线程死亡。
3. 关闭 CPU。

## 如何自定义异常

自定义异常通常是定义一个继承自 ``Exception`` 类的子类。

一般情况下我们为了统一捕获处理当前工程项目中的异常，都会直接继承自 ``Exception`` 类，在其中保存要记录的异常信息，而不会继承某个运行时的异常类。

比如这样：

```
public class MyException extends Exception{

    String deviceInfo;  //记录一些核心信息
    
    public MyException() {
    }

    public MyException(String message) {
        super(message);
    }

    public MyException(String message, Throwable cause) {
        super(message, cause);
    }

    public MyException(Throwable cause) {
        super(cause);
    }

}

    public void test2() throws MyException {
        //do something
        throw new MyException("");
    }

    public void test3() {
        //do something
        try {
            throw new MyException("");
        } catch (MyException e) {
            e.printStackTrace();
        }
    }

```

# 总结

Java 异常是 Java 提供的一种识别及响应错误的一致性机制。

Java 异常机制可以使程序中异常处理代码和正常业务代码分离，降低耦合度。保证程序代码更加优雅，并提高程序健壮性。

在有效使用异常的情况下，它能清晰地回答异常 what, where, why 这3个问题： 

1. 异常类型回答了“什么”被抛出
2. 异常堆栈跟踪回答了“在哪“抛出
3. 异常信息回答了“为什么“会抛出

本文向读者介绍如何编写正确的异常处理程序，并将展示当方法出问题的时候，如何产生自定义异常。如有不对欢迎拍砖，谢谢。


# Thanks
 《Java 编程思想》
 http://blog.csdn.net/you_off3/article/details/7572471
 https://www.cnblogs.com/skywang12345/p/3544168.html#a2
 http://www.importnew.com/1701.html

