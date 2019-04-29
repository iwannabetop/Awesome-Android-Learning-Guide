# 队列
# 概念
队列是只允许在一段进行插入操作，而在另一端进行删除操作的线性表。
# 特点
先进先出（FIFO）。

# 应用场景
队列一般可作为缓冲器或者解耦使用。
1. 在线秒杀功能
2. 生产者消费者和消费者模式

# 队列代码实现
	public class ArrayQueue {
	    private final Object[] items;
	    private int head;
	    private int tail;
	
	    /**
	     * 初始化队列
	     *
	     * @param capacity 队列长度
	     */
	    public ArrayQueue(int capacity) {
	        items = new Object[capacity];
	    }
	
	    /**
	     * 入队
	     * @param item
	     * @return
	     */
	    public boolean put(Object item) {
	        if (head == (tail + 1) % items.length) {
	            //队列已满
	            return false;
	        }
	        
	        items[tail] = item;
	        tail = (tail + 1) % items.length;
	        return true;
	    }
	
	    /**
	     * 获取队列头元素，不出队
	     * @return
	     */
	    public Object peek(){
	        if (head == tail) {
	            return null;
	        }
	        
	        return items[head];
	    }
	
	    /**
	     * 出队
	     * @return
	     */
	    public Object poll() {
	        if (head == tail) {
	            return null;
	        }
	        
	        Object item = items[head];
	        items[head] = null;
	        head = (head + 1) % items.length;
	        return item;
	    }
	
	    /**
	     * 队长
	     * @return
	     */
	    public int Size(){
	        if (tail >= head) {
	            return  tail - head;
	        } else {
	            return tail + items.length - head；
	        }
	    }

		public boolean isEmpty() {
			return tail == head;
		}
	}

# 使用两个队列实现栈
队列的特点是先进先出，而栈的特点是先进后出  
**思路：**  将两个队列定义为queue1,queue2:
- 入栈：那个队列不为空，就把队列入队到哪个队列中；如果都为空，则任选一个队列入队；
- 出栈：把不为空的队列中除最后一个元素外的所有元素移动到另一个队列中，然后出队最后一个元素。

	public class TwoQueueStack {
	    private ArrayQueue queue1;
	    private ArrayQueue queue2;
	    private int maxLength;
	    
	    public TwoQueueStack(int capacity) {
	        maxLength = capacity;
	        queue1 = new ArrayQueue(capacity);
	        queue2 = new ArrayQueue(capacity);
	    }
	
	    /**
	     * 入栈
	     * @param item
	     * @return
	     */
	    public boolean push (int item) {
	        if (size() == maxLength) {
	            return false;
	        }
	
	        if (queue2.isEmpty()) {
	            queue1.put(item);
	        } else {
	            queue2.put(item);
	        }
	        
	        return true;
	    }
	
	    /**
	     * 出栈
	     * @return
	     */
	    public Object pop() {
	        if (size() == 0) {
	            throw new IndexOutOfBoundsException("Stack is empty");
	        }
	
	        if (queue2.isEmpty()) {
	            while (queue1.Size() > 1) {
	                queue2.put(queue1.poll());
	            }
	            return queue1.poll();
	        } else {
	            while (queue2.Size() > 1) {
	                queue1.put(queue2.poll());
	            }
	            return queue1.poll();
	        }
	        
	    }
	    
	    public int size() {
	        return queue1.Size() + queue2.Size();
	    }
	}

