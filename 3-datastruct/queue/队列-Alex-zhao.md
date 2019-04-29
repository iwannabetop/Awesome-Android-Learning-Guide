##数据结构--队列
@(数据结构，算法)

队列（queue）是一种采用先进先出(FIFO)策略的抽象数据结构，它的想法来自于生活中排队的策略。顾客在付款结账的时候，按照到来的先后顺序排队结账，先来的顾客先结账，后来的顾客后结账。


####实现一个MyQueue类，该类用两个栈来实现一个队列。

思想：
定义两个栈，stackNewest和stackOldest，stackNewest顶端为最新元素，stackOldest顶端为最旧元素。在将一个元素出列时，我们希望先移除最旧元素，因此先将元素从stackOldest出列。若stackOldest为空，则将stackNewest中的所有元素以相反的顺序转移到stackOldest中。如要插入元素，就将其压入stackNewest，因为最新元素位于它的顶端。

```
import java.util.*;  
class Queue{  
    public static void main(String args[]){  
        MyQueue<Object> queue =new MyQueue<Object>();  
        queue.add(1);  
        queue.add(2);  
        queue.add(3);  
        queue.add(4);  
        queue.add(5);  
        System.out.println(queue.remove());  
        System.out.println(queue.remove());  
        System.out.println(queue.remove());  
        System.out.println(queue.remove());  
        System.out.println(queue.remove());  
    }  
}  
  
class MyQueue<T>{  
    Stack<T> stackNewest,stackOldest;  
      
    public MyQueue(){  
        stackNewest = new Stack<T>();  
        stackOldest = new Stack<T>();  
    }  
      
    public int size(){  
        return stackNewest.size()+stackOldest.size();  
    }  
      
    public void add(T value){  
        /*压入stackNewest，最新元素始终位于它的顶端*/  
        stackNewest.push(value);  
    }  
      
    /*将元素从stackNewest移至stackOldest，这么做通常是为了要在stackOldest上执行操作*/  
    private void shiftStack(){  
        if(stackOldest.isEmpty()){  
            while(!stackNewest.isEmpty()){  
                stackOldest.push(stackNewest.pop());  
            }  
        }  
    }  
      
    public T peek(){  
        shiftStack();//确保stackOldest含有量当前元素  
        return stackOldest.peek();//取回最新元素  
    }  
      
    public T remove(){  
        shiftStack();//确保stackNewest含有量当前元素  
        return stackOldest.pop();//弹出最旧元素  
    }  
}  
```

