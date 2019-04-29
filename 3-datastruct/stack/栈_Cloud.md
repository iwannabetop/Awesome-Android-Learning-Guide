# 栈的定义
说起栈可以先想象一下手枪的弹夹，最后一个填入的子弹，第一个射出去，这就是一个典型的栈。
![image.png](https://upload-images.jianshu.io/upload_images/4951294-e68386b77a2b2747.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


栈是一种**后进先出**的数据结构，它是限定仅在**表尾**进行插入和删除操作的**线性表**。
我们把允许插入和删除的一端称为栈顶，另一端称为栈底，不含任何数据元素的栈称为空栈。
栈的插入操作，叫做进栈，也称压栈、入栈。就像子弹填入弹夹。
栈的删除操作，叫做出栈，也有叫做弹栈。就像弹夹中的子弹出夹。

# 栈的使用场景
在Android中一个众所周知的使用场景就是Activity任务栈，每当我们打一个Activity的时候就会往Activity栈中压入一个Activity，每当我们销毁一个Activity的时候会从Activity的任务栈中弹出一个Activity。具体见下图
![image.png](https://upload-images.jianshu.io/upload_images/4951294-ef912958087f754b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 两个栈实现一个队列

```java
public class StackTest {

	static Stack<Integer> s1 = new Stack<>();
	static Stack<Integer> s2 = new Stack<>();

	public void push(int node) {
		s1.push(node);
	}

	public int pop() throws Exception {
		if (s2.size() <= 0) {
			while (s1.size() > 0) {
				s2.push(s1.pop());
			}
		}
		if (s2.isEmpty()) {
			throw new Exception("stack is empty!");
		}
		return s2.pop();
	}
	
	public static void main(String[] args) {
		StackTest stackTest = new StackTest();
		stackTest.push(1);
		stackTest.push(2);
		stackTest.push(3);
		
		for (Integer integer : s1) {
			System.out.println(integer);
		}
		
		try {
			System.out.println(stackTest.pop()+"");
			stackTest.push(4);
			System.out.println(stackTest.pop()+"");
			stackTest.push(5);
			System.out.println(stackTest.pop()+"");
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

}
```