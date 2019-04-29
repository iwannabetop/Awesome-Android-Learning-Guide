### 栈

#### 概念
后进先出。按照生活中常见的场景形容一下栈，就是堆盘子，你第一个放下的盘子一定是在底部（在栈中的就叫push（压入）），最后一个盘子在顶部，当你想用盘子的时候，一定是从顶部拿起（在栈中就叫做pop（弹出））


#### 简单实现

```
public class Stack {
	private int mTop = -1; 
	private int mMaxSize;
	private char[] mStack;
	
	public Stack(int max) {
		mMaxSize = max;
		mStack = new char[mMaxSize];
	}
	
	public void push(char ch) {
		mStack[++mTop] = ch;
	}
	
	public char pop() {
		return mStack[mTop--];
	}
	
	public boolean isEmpty() {
		if(mTop == -1) {
			return true;
		} else {
			return false;
		}
	}
}

```
#### 面试题2个栈实现一个队列


```
package appdemo.struggle.com.lib;

import java.util.Stack;

/**
 * Created by struggle on 2018/4/22.
 */

public class StackManager {

    Stack<Integer> stack1 = new Stack<Integer>();
    Stack<Integer> stack2 = new Stack<Integer>();

    public void push(int node) {
        stack1.push(node);
    }

    public int pop() {

        if(stack1.isEmpty()){
            try {
                throw new Exception("stack1 is empty.");
            } catch (Exception e) {
            }
        }
        if(stack2.size()<=0){
            if (stack1.size()>0){
                stack2.push(stack1.pop());
            }
        }

        if(stack2.isEmpty()){
            try {
                throw new Exception("stack2 is empty.");
            } catch (Exception e) {
            }
        }
        int head = stack2.pop();
        return head;

    }
}

