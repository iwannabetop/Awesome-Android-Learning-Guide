# 数组  

## 概念  
数组是存储同一种数据多个元素的集合，也可以看成是一个容器，数组既可以存储基本数据类型，也可以存储引用数据类型。数组初始化时会为其开辟连续的内存空间，并为数组每个元素赋值。  

## 数组的特点  
1. 数组的长度是固定的，一旦声明，便无法再改变其长度，在赋值是，如果数组元素未赋值，系统将为其指定默认初始值。  
2. 当需要访问某个数组时，只能从第一个元素开始按顺序访问，直到访问到指定元素；虽然我们可以在代码中直接通过下标访问指定位置的元素，但实际上计算机在处理时还是按顺序访问。
3. 对元素操作比较简单，可根据索引对数据直接进行操作。

## 使用场景  
在存储长度已知的数据时推荐使用数组。

# 链表 

## 概念  
链表是一种物理上非连续、非顺序的存储结构，数据元素之间的顺序是通过每个元素的指针关联的。链表由一些列节点组成，每个节点一般会包含两部分信息：一部分是元素数据本身，另一部分是指向下一个元素地址的指针。

## 链表类型 
链表分为两种类型：单向链表和双向链表。  

- 单向链表的每个节点除了存储元素数据本身外，还额外存储了一个指向下一个元素的指针。
- 双向链表的每个节点除了存储元素数据本身外，还额外存储了两个指针，分别指向上一个节点和下一个节点的地址。

## 链表的特点  
1. 存储空间不连续，可以利用系统自身的动态内存管理，但是需要更多的存储空间去存储指针信息。
2. 链表长度是不固定的，可向链表动态添加元素，而不需要考虑长度不足的问题。
3. 查找元素是需要逐个遍历，当链表很长时，所查找的元素位置越靠后，效率越低。
4. 对元素操作比较复杂，不但需要处理数据，还需要处理指针。

## 使用场景 
1. 在存储不确定长度的数据时推荐使用链表
2. 可直接使用单向链表实现栈

## 链表的代码实现


	public class Link<T> {
	    private int size;
	    private Node first;
	    private Node last;
	
	    /**
	     * 链表中只有一哥元素时，头尾都是同一个元素
	     *
	     * @param data
	     */
	    private void fillStart(T data) {
	        first = new Node();
	        first.setData(data);
	        last = first;
	    }
	
	    /**
	     * 头部插入
	     *
	     * @param data
	     */
	    public void addFirst(T data) {
	        if (size == 0) {
	            //链表为空时初始化头尾元素
	            fillStart(data);
	        } else {
	            Node<T> node = new Node<>();
	            node.setData(data);
	            //把元素下一个位置的指针指向头元素
	            node.setNext(first);
	            //把新插入的元素设置为链表头元素
	            first = node;
	        }
	
	        size++;
	    }
	
	    /**
	     * 尾部插入
	     *
	     * @param data
	     */
	    public void addLast(T data) {
	        if (size == 0) {
	            //链表为空时初始化头尾元素
	            fillStart(data);
	        } else {
	            Node<T> node = new Node<>();
	            node.setData(data);
	            //把尾元素的下一个位置指针指向尾元素
	            last.setNext(node);
	            //把新插入的元素设置为链表尾元素
	            last = node;
	        }
	
	        size++;
	    }
	
	    /**
	     * 在指定位置插入元素
	     *
	     * @param data
	     * @param index
	     */
	    public void add(T data, int index) {
	        if (size > index) {
	            if (size == 0) {
	                //链表为空时初始化头尾元素
	                fillStart(data);
	                size++;
	            } else if (index == 0) {
	                addFirst(data);
	            } else if (size == index + 1) {
	                addLast(data);
	            } else {
	                Node temp = get(index);
	                Node<T> node = new Node<>();
	                node.setData(data);
	                node.setNext(temp.getNext());
	                temp.setNext(node);
	                size++;
	            }
	        } else {
	            throw new IndexOutOfBoundsException("index error");
	        }
	    }
	
	    /**
	     * 删除链表头元素
	     */
	    public void removeFirst() {
	        if (size == 0) {
	            throw new IndexOutOfBoundsException("Link is Empty");
	        } else if (size == 1) {
	            clear();
	        } else {
	            Node temp = first;
	            first = temp.getNext();
	            temp.setNext(null);
	            size--;
	        }
	    }
	
	
	    /**
	     * 删除链表尾元素
	     */
	    public void removeLast() {
	        if (size == 0) {
	            throw new IndexOutOfBoundsException("Link is Empty");
	        } else if (size == 1) {
	            clear();
	        } else {
	            //尾元素的前一个节点
	            Node temp = get(size - 2);
	            temp.setNext(null);
	            last = temp;
	            size--;
	        }
	    }
	
	    /**
	     * 移除指定位置的元素
	     *
	     * @param index
	     */
	    public void remove(int index) {
	        if (size > index) {
	            if (size == 0) {
	                throw new IndexOutOfBoundsException("Link is Empty");
	            } else if (size == 1) {
	                clear();
	            } else {
	                if (index == 0) {
	                    removeFirst();
	                } else if (index == size - 1) {
	                    removeLast();
	                } else {
	                    //获取删除元素的前一个节点
	                    Node temp = get(index - 1);
	                    Node next = temp.getNext();
	                    temp.setNext(next.getNext());
	                    next.setNext(null);
	                    size--;
	                }
	            }
	        } else {
	            throw new IndexOutOfBoundsException("index error");
	        }
	
	    }
	
	    /**
	     * 只剩一个时清除头尾节点
	     */
	    private void clear() {
	        first = null;
	        last = null;
	        size = 0;
	    }
	
	    /**
	     * 获取指定下标元素
	     *
	     * @param index
	     * @return
	     */
	    public Node get(int index) {
	        Node temp = first;
	        for (int i = 0; i < index; i++) {
	            temp = temp.getNext();
	        }
	
	        return temp;
	    }
	
	
	    public int getSize() {
	        return size;
	    }
	
	}

## 反转链表 
- 思路：
	1. 将尾检点=头检点（last = first）
	2. 从第二个节点开始，将后一个节点反向指向前一个节点
	3. 将反转后的尾节点指针置空
	4. 设置头结点  

#
  	/**
     * 反转链表
     */
	public void reverse() {
        Node temp = first;
        last = first;
        Node next = first.getNext();
        for (int i = 0; i < size - 1; i++) {
            Node nextNext = next.getNext();
            next.setNext(temp);
            temp = next;
            next = nextNext;
        }
        
        last.setNext(null);
        first = temp;
    }