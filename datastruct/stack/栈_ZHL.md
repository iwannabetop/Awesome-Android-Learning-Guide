# 栈
# 概念
栈是一个非常常见的数据结构，一般使用一段连续的空间进行存储，通常预先分配一个长度，可以简单的使用数组去实现。栈一般是不考虑数据的排序的，需要O(n)才能找到其中最大或者最小元素。
# 特点
栈的特点是先进后出（FILO）,即最先被压入(push)栈的元素会最后一个被弹出(pop)。
# 适用场景
根据栈先进后出的特点，一般有如下几个应用：  

 1. 逆序输出
 2. 语法检查，通过匹配，检查符号是否成对出现
 3. 数制转换，通过求余法，将十进制转换为其它进制

# 使用两个栈实现队列
栈的特点是先进后出，而队列的特点是先进先出。  
**思路：** 一个栈作为压入栈，在压数据时只往这个栈中压入，记为stackPush;另一个栈只作为弹出栈，在弹出数据时只从这个栈弹出，记为stackPop。  
同时，两个栈必须遵循以下两个原则  
1. 如果stackPush要往stackPop中压入数据，必须一次性把stackPush中的数据全部压入。  
2. 如果stakPop不为空,stackPush绝对不能向stackPop中压入数据。 

	public class TwoStackQueue<E> {
	
	    private final Stack<E> stackPush;
	    private final Stack<E> stackPop;
	
	    public TwoStackQueue() {
	        stackPush = new Stack<>();
	        stackPop = new Stack<>();
	    }
	
	    public void add(E e) {
	        stackPush.push(e);
	    }
	
	    public E poll(E e) {
	        if (stackPop.empty() && stackPush.empty()) {
	            throw new RuntimeException("Queue is Empty!!!");
	        }
	
	        if (stackPop.empty()) {
	            while (!stackPush.empty()) {
	                stackPop.push(stackPush.pop());
	            }
	        }
	
	        return stackPop.pop();
	    }
	    
	    public E peek() {
	        if (stackPop.empty() && stackPush.empty()) {
	            throw new RuntimeException("Queue is Empty!!!");
	        }
	
	        if (stackPop.empty()) {
	            while (!stackPush.empty()) {
	                stackPop.push(stackPush.pop());
	            }
	        }
	        
	        return stackPop.peek();
	    }
	}