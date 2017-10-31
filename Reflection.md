# 文章出处

> 文章出自：[安卓进阶学习指南](https://github.com/iwannabetop/Awesome-Android-Learning-Guide)
>
> 作者：[HongleiZhang](https://github.com/RayCourant)
>
> 审核者:[审核者的名字](链接地址 Github 或者 博客)
>
> 完稿日期：2017.10.31
#1.什么是反射
- 首先我们来看看Oracle官方对反射的阐述:
> Reflection is commonly used by programs which require the ability to examine or modify the runtime behavior of applications running in the Java virtual machine. This is a relatively advanced feature and should be used only by developers who have a strong grasp of the fundamentals of the language. With that caveat in mind, reflection is a powerful technique and can enable applications to perform operations which would otherwise be impossible.

  - **简而言之，反射可以在运行时加载和使用在编译期完全未知的类。**通过反射，我们可以在运行时获得程序或程序集中每一个类型的成员和成员的信息。程序中一般的对象的类型都是在编译期就确定下来的，而Java反射机制可以动态地创建对象并调用其属性，这样的对象的类型在编译期是未知的。所以我们可以通过反射机制直接创建对象，即使这个对象的类型在编译期是未知的。**反射的核心是JVM在运行时才动态加载类或调用方法/访问属性,它不需要事先知道运行对象是谁。**

#2.我们为什么需要反射
- Java反射框架提供了以下几个功能:
1.在运行时判断一个对象所属的类；
2.在运行时构造任意一个类的对象；
3.在运行时判断任意一个类所具有的成员变量和方法；
4.在运行时调用任意一个对象的方法；
5.修改构造函数、方法、属性的可见性；
#3.反射的基本使用
下面我们通过几个简单的例子学习如何使用反射：
###（1）通过反射获取Class对象
 - (i)直接获取某个对象的class(最安全/性能最好)
如果在编译期已经知道了一个类的名字，可以直接通过类名.class获取到该Class对象.
```
Class<?> targetClass = TargetClass.class;
```
 - (ii)调用某个对象的getClass()方法
若已经持有某对象的引用，此时想获取该对象的Class对象，可通过`getClass()`方法获取.
```
TargetClass target = new TargetClass();
Class<?> targetClass = target.getClass();
```
 - (iii)使用Class类的forName静态方法
对于在编译期获取不到但是知道其完整路径的目标类型，可以通过调用`Class.forName("全类名")`动态获取到该Class对象,ClassforName()有两个重载方法.
```
//a.加载指定的Class 对象, 参数1 为要加载的类的完整路径
Class<?> targetClass = Class.forName("com.zhl.demo.TargetClass");
//b.加载指定的 Class 对象，参数 1 为要加载的类的完整路径,参数2为是否要初始化该Class对象,参数3为指定加载该类的ClassLoader
Class<?> targetClass = Class.forName("com.zhl.reflectiontest.TargetClass", true, classLoader);
```
*注意：如果在调用`Class.forName()`方法时，没有在该路径下找到对应的类，那么其将抛出`ClassNotFoundException`.*
##3.通过反射生成对象
- 在获取Class后,可以使用Class对象的`newInstance()`方法来创建Class对象对应类的实例(前提是该Class对象的对应类有默认Constructor).
- 先使用Class对象获取指定的Constructor对象,再调用Constructor对象的`newInstance()`方法创建Class对象对应类的实例(通过这种方式可以选择指定的够着起来创建实例).
##4.通过反射获取成员方法并使用
  - 通过Class对象的`getDeclaredMethod(String name, Class...<?> parameterTypes)`方法获得指定方法名和参数的该类下的**方法**.而如果想获得该类**所有方法**,可以调用`getDeclaredMethods() `.
```
// 获取 Class 对象中指定方法名和参数的方法，参数1为方法名，参数 2 为参数类型列表
public Method getDeclaredMethod (String name, Class...<?> parameterTypes)

// 获取该 Class 对象中的所有方法( 不包含从父类继承的方法 )
public Method[] getDeclaredMethods ()
```
  - 而如果仅仅需要获得Class对象的**公有方法**,可以通过Class对象的`getMethod(String name, Class...<?> parameterTypes)`方法获得指定方法名和参数的该类下的**公有方法**.而如果想获得该类**所有公有方法**,可以调用`getMethods() `
```
// 获取指定的 Class 对象中的公有方法，参数1为方法名，参数 2 为参数类型列表
public Method getMethod (String name, Class...<?> parameterTypes)

// 获取该 Class 对象中的所有公有方法 ( 包含从父类和接口类继承下来的方法 )
public Method[] getMethods ()
```

##4.通过反射获取成员变量并使用 
通过Class对象的`getField()`方法可以获得该类所包含的指定的**公有成员变量**Field,而`getDeclaredField ()`可获得获得该类所包含的指定的**成员变量**Field,其使用方法与上述*`通过反射获取成员方法并使用`*相差无几,在此不再赘述.另外,Filed提供了如下两种方法读取和设置成员变量.
  - `getXxx(Object obj)`:获取obj对象的改成员变量的值,Xxx对应8种基本类型,如果该变量是引用类型,则取消则直接调用`get(Object obj)`即可.
 -  `setXxx(Object obj, Xxx val)`:将obj对象的对应成员变量置为val值,同上,Xxx对应8种基本类型,如果该变量是引用类型,则取消则直接调用`set(Object obj, Xxx val)`.
```
// 获取指定的 Class 对象中的公有成员变量，参数1为变量名
public Method getField (String name)

// 获取该 Class 对象中的所有公有成员变量 ( 包含从父类和接口类集成下来的公有成员变量 )
public Method[] getFields ()

// 获取 Class 对象中指定成员变量，参数1为变量名
public Method getDeclaredField (String name)

// 获取该 Class 对象中的所有成员变量( 不包含从父类继承的成员变量 )
public Method[] getDeclaredFields ()

```

# Thanks

* [公共技术点之 Java 反射 Reflection](http://a.codekk.com/detail/Android/Mr.Simple/%E5%85%AC%E5%85%B1%E6%8A%80%E6%9C%AF%E7%82%B9%E4%B9%8B%20Java%20%E5%8F%8D%E5%B0%84%20Reflection)
* [Reflection Tutorials](https://docs.oracle.com/javase/tutorial/reflect/)
* [Java 反射](http://www.importnew.com/17616.html)
