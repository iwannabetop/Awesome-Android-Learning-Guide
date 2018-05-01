# 队列的定义
> 队列(queue)是只允许在一端进行插入操作，在另一端进行删除操作的**线性表。**也称为先进先出(First In First Out)的线性表，允许插入的一端称为队尾，允许删除的一端称为对头。

队列和栈一样是一种抽象的概念，可以用链表实现，也可以用动态数组实现，这种抽象的概念可以用不同的数据结构来实现。

# 用链表来实现一个队列

```java
public class QueueNode {
    /**
     * 节点数据
     */
    Object data;
    /**
     * 指向下一个节点的指针
     */
    QueueNode next;

    public QueueNode() {
        this(null, null);
    }

    public QueueNode(Object data) {
        this(data, null);
    }

    public QueueNode(Object data, QueueNode next) {
        this.data = data;
        this.next = next;
    }
}


public class QueueTest {
    /**
     * 队头指针
     */
    QueueNode front;
    /**
     * 队尾指针
     */
    QueueNode rear;

    public QueueTest() {
        this.rear = null;
        this.front = null;

    }
    
    /**
     * 向队列插入数据
     * @param obj
     */
    public void enqueue(Object obj) {
        //队列为空时初始化，对头队尾都指向一个队列元素
        if (rear == null && front == null) {
            rear = new QueueNode(obj);
            front = rear;
        } else {
            QueueNode node = new QueueNode(obj);
            //将当前队尾指针的队列元素的指针,指向新加入的队列元素
            rear.next = node;
            //将当前队尾元素对象内存地址，指向新加入的队列元素 
            rear = rear.next;
        }
    }

    public Object outQueue() {
        if (front == null) {
            return null;
        }    
        //队列中只有一个队列元素时
        if (front == rear && rear != null) {
            QueueNode node = front;
            rear = null;
            front = null;
            return node.data;
        }
        //取出当前队头的元素
        Object obj = front.data;
        //将当前队头元素指针指向它下一个对了元素
        front = front.next;
        return obj;
    }

    public static void main(String[] args) {
        QueueTest queueTest = new QueueTest();
        queueTest.enqueue("1");
        queueTest.enqueue("2");
        queueTest.enqueue("3");
        queueTest.enqueue("4");

        System.out.println(queueTest.outQueue());
        System.out.println(queueTest.outQueue());
        System.out.println(queueTest.outQueue());
        System.out.println(queueTest.outQueue());
        System.out.println(queueTest.outQueue());

    }
}
```

# 使用场景
在Android中队列最典型的使用场景就是**MessageQueue**消息队列。
> 消息队列，用来存放Message对象的数据结构，按照“先进先出”的原则存放消息。存放并非实际意义的保存，而是将Message对象以链表的方式串联起来的。MessageQueue对象不需要我们自己创建，而是有Looper对象对其进行管理，一个线程最多只可以拥有一个MessageQueue。我们可以通过Looper.myQueue()获取当前线程中的MessageQueue。

# 两个队列实现一个栈

```java
public class QueueToStack {

	LinkedList<Integer> queue1 = new LinkedList<Integer>();
	LinkedList<Integer> queue2 = new LinkedList<Integer>();

	/**
	 * 入队
	 * 
	 * @return
	 */
	public void push(int value) {
		queue1.addLast(value);
	}
	/**
	 * 出队
	 * @return
	 */
	public int pop() {
		if (stackSize() != 0) {	
			if (!queue1.isEmpty()) {
				putDataToEmptyQueue();
				return queue1.removeFirst();
			}else{
				putDataToEmptyQueue();
				return queue2.removeFirst();
			}
		}else{
			return -1;
		}
	}
	/**
	 * 返回栈的总大小
	 * @return
	 */
	public int stackSize() {
		return queue1.size() + queue2.size();
	}
	/**
	 * 两个队列之间相互存入数据
	 */
	public void putDataToEmptyQueue() {
		if (!queue1.isEmpty()) {
			while (queue1.size() > 1) {
				System.out.println("queue2.addLast(queue1.removeFirst())");
				queue2.addLast(queue1.removeFirst());
			}
		} else if (!queue2.isEmpty()) {
			while (queue2.size() > 1) {
				System.out.println("queue1.addLast(queue2.removeFirst())");
				queue1.addLast(queue2.removeFirst());
			}
		}
	}
}

public class QueueToStackTest {
	
	public static void main(String[] args) {
		QueueToStack queueToStack  = new QueueToStack();
		queueToStack.push(1);
		queueToStack.push(2);
		queueToStack.push(3);
		queueToStack.push(4);
		
		System.out.println(queueToStack.pop());
		System.out.println(queueToStack.pop());
		System.out.println(queueToStack.pop());
		System.out.println(queueToStack.pop());

	}
}

```