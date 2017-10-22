# 文章出处

> 文章出自：[安卓进阶学习指南](https://github.com/iwannabetop/Awesome-Android-Learning-Guide)
>
> 作者：[Cloud9527](http://www.jianshu.com/u/69fc55c71311)
>
> 审核者: shixinzhang Struggle Alex_赵
>
> 完稿日期：

##深入浅出Java内部类

大家每天都要和Java中的类打交道，大家对于普通的类想必不用多说什么都已经用的滚瓜烂熟，但是提起Java内部类（Inner Class）可能很多就只是听过内部类而已，下面就介绍一下Java中的内部类。

### 内部类的概念

定义在一个类中或者方法中的类称作为内部类。

内部类又可以具体细分为**成员内部类**、**局部内部类**、**匿名内部类**、**静态内部类**。

#### 成员内部类

成员内部类就是最普通的内部类，它定义在一个类的内部中，就如同一个成员变量一样。如下面的形式

```java
class OutClass { // 外部类
	private int i = 1;
	public static String str = "outclass";

	class InnerClass { // 成员内部类

		public void innerMethod() {
			System.out.println("i=" + i);
			System.out.println("str=" + str);

		}
	}
}

public class TestClass {

	public static void main(String[] args) {
		//先创建外部类对象
		OutClass outClass = new OutClass(); 
		//创建内部类对象
		OutClass.InnerClass in = outClass.new InnerClass();
		//内部类对象调用自己的方法
		in.innerMethod();
	}
} 
```

因为内部类依附于外部类存在，所以需要外部类的实例来创建内部类；成员内部类可以无条件的访问外部类的成员属性和成员方法（包括private成员和static成员)，但是外部类想要调用内部类的方法或者属性必须先要创建内部类的实例才可以。

> 注：如果内部类的属性和方法和外部类的属性和方法同名，如果直接调用这个属性或者方法则是默认调用内部类的。如果想调用外部类的属性和方法则需要如下写法 ：
>
> 外部类.this.属性
>
> 外部类.this.方法

成员内部类就如同外部类的成员一样，同样可以被public、protected、private、缺省（default）这些修饰符来修饰，下面是这些修饰符的权限范围：

| 修饰符       | 当前类  | 同一包内 | 子孙类  | 其他包  |
| --------- | ---- | ---- | ---- | ---- |
| public    | Y    | Y    | Y    | Y    |
| protected | Y    | Y    | Y    | N    |
| default   | Y    | Y    | N    | N    |
| private   | Y    | N    | N    | N    |

#### 局部内部类

它和成员内部类的区别就是：局部内部类的作用域只能在其所在的代码块或者方法内，在其它地方是无法创建该类的对象。

```java
public class OutClass {

	{
		class PartClassOne { // 局部内部类
			private void method() {
				System.out.println("PartClassone");
			}
		}
		new PartClassOne().method();
	}

	public void testMethod() {
		class PartClassTwo { // 局部类内部类
			private void method() {
				System.out.println("PartClassTwo");
			}
		}
		new PartClassTwo().method();
	}
}
```

#### 匿名内部类

先让我们来看一段最常见的代码

`Car jeep=new Car();`

在Java中**“操纵的标识符实际是指向一个对象的引用”**也就是说`jeep`是一个指向`Car`类对象的引用，而右面的 `new Car()`才是真正创建对象的语句。这可以将`jeep`抽象的理解为`Car`类对象的“名字”，而匿名内部类顾名思义可以抽象的理解为没有“名字”内部类.

```java
button.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View v) {
    // TODO Auto-generated method stub
    }
});
```

上面代码是Android中最常见的设置button的点击事件，其中new OnClickListener() {…} 就是一个匿名内部类，在这里没有创建类对象的引用，而是直接创建的类对象。这个就是最典型的匿名内部类。大部分匿名类用于接口回调。

#### 静态内部类

使用**static**关键字修饰的内部类就是静态内部类，静态内部类和外部类没有任何关系，可以看作是和外部类平级的类。静态内部类只能访问外部类的**静态属性和静态方法**，外部类只能访问内部类的**静态属性和静态方法**。

```java
public class OutClassThree {
	private String str = "out";
	private static String str2 = "static_str";

	public void print() {
		System.out.println("outclass:" + str);
		System.out.println("outclass:" + str2);
      	//外部类可以直接调用静态内部类的静态属性
		System.out.println("outclass:" + StaticInnerClass.instr2); 

		StaticInnerClass innerClass = new StaticInnerClass();
		innerClass.print();
	}

	public static class StaticInnerClass {
		private String instr = "out";
		private static String instr2 = "static_str";

		public void print() {
			// 静态内部类不能访问外部类的非静态属性和方法
			// System.out.println("InnerClass:" + OutClassThree.this.str);
			// 静态内部类可以直接访问外部类的静态属性 外部类.静态属性
			System.out.println("InnerClass:" + OutClassThree.str2);
			System.out.println("InnerClass:" + instr2);
		}
	}
}

```

#### 上面这些就是对不同内部类概念的介绍。但是Java中为什么会提供内部类呢？

1. 每个内部类都能独立的继承一个接口的实现，所以无论外部类是否已经继承了某个(接口的)实现，对于内部类都没有影响。内部类使得多继承的解决方案变得完整，
2. 匿名内部类可以方便的实现闭包。
3. 静态内部类可以带来更好的代码聚合，提高代码可维护性



#### 参考资料

《Java编程思想》

http://blog.csdn.net/qq7342272/article/details/6671433

http://www.cnblogs.com/latter/p/5665015.html