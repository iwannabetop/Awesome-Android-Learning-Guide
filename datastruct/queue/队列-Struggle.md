### 队列

#### 概念
队列是一种特殊的线性表，特殊之处在于它只允许在表的前端（front）进行删除操作，而在表的后端（rear）进行插入操作，和栈一样，队列是一种操作受限制的线性表。进行插入操作的端称为队尾，进行删除操作的端称为队头。

#### 应用场景

典型的应用
生产者与消费者下载、任务、Handler内部实现等等。
以下代码是Okhttp源码用队列实现的任务处理

```
   synchronized void enqueue(AsyncCall call) {
        if(this.runningAsyncCalls.size() < this.maxRequests && this.runningCallsForHost(call) < this.maxRequestsPerHost) {
            this.runningAsyncCalls.add(call);
            this.executorService().execute(call);
        } else {
            this.readyAsyncCalls.add(call);
        }

    }
```

#### 手写队列


```
/**
 * Created by StruggleLiping on 2018/4/25.
 */
public class Queue {

    private Stack<Integer> stack1 = new Stack<>();//执行入队操作的栈
    private Stack<Integer> stack2 = new Stack<>();//执行出队操作的栈


    //方法：给队列增加一个入队的操作
    public void push(int data) {
        stack1.push(data);

    }

    //方法：给队列正价一个出队的操作
    public int pop() throws Exception {
        if (stack2.empty()) {//stack1中的数据放到stack2之前，先要保证stack2里面是空的（要么一开始就是空的，要么是stack2中的数据出完了），不然出队的顺序会乱的，这一点很容易忘
            while (!stack1.empty()) {
                stack2.push(stack1.pop());//把stack1中的数据出栈，放到stack2中【核心代码】
            }
        }
        if (stack2.empty()) { //stack2为空时，有两种可能：1、一开始，两个栈的数据都是空的；2、stack2中的数据出完了
            throw new Exception("队列为空");
        }
        return stack2.pop();
    }

}
```


#### 面试题二个队列实现一个栈

```
/**
 * Created by StruggleLiping on 2018/4/25.
 */
public class Stack {
    private ArrayDeque<Integer> queue1;
    private ArrayDeque<Integer> queue2;
    
    Stack() {
        this.queue1 = new ArrayDeque<>();
        this.queue2 = new ArrayDeque<>();
    }
    
    // queue1始终负责实现栈的push操作
    public void push(int element) {
        queue1.offer(element);
    }
    
    // 将from队列元素出队，并依次入队到to队列，直到from队列中只有一个元素
    public void move(ArrayDeque<Integer> from,
                     ArrayDeque<Integer> to) {
        while (from.size() > 1) {
            to.offer(from.poll());
        }
    }
    
    public Integer pop() {
        if (!isEmpty()) {
            if (!queue1.isEmpty()) {
                move(queue1, queue2);
                // queue1队列pop后为空
              return   queue1.poll();
            } else if (!queue2.isEmpty()) {
                move(queue2, queue1);
                // queue2队列pop后为空
                return   queue2.poll();
            }
        }
        return 0;
    }

    
    public boolean isEmpty() {
        return queue1.isEmpty() && queue2.isEmpty();
    }

    public static void main(String[] args) {
        Stack stack = new Stack();
        for(int i=0;i<10;i++){
            stack.push(i);
        }
        for(int i=0;i<10;i++){
            System.out.println(stack.pop());
        }
    }

}
```
