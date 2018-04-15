# 数组

数组是最简单的一种数据结构，它占据一块连续的内存，而且是顺序存储结构。在创建数组时必须要指定数组的容量大小，在根据数组容量来分配内存。数组可以说是**线性表的顺序存储结构**。

## 数组优点

因为数组的内存是连续的，它的存取时间性能为O(1)，因此它的时间效率是很高的。

## 数组缺点

1. 需要预先知道数据规模
2. 插入效率低

## 数组使用场景

在下列三个场景使用数组很有优势

1. 数据量小
2. 数据规模已知
3. 对数据存取和修改操作较多，而插入和删除数据较少的情况。

# 链表

链表即链式储存结构，它的内存分配，可以是连续的也可以是不连续的，可以分配在内存未被占用的任意位置。它的内存分配不是在创建链表时一次性完成，而是每添加一次结点分配一次内存，因而没有闲置内存，所以空间效率比数组高。

在链式存储结构中除了要存储数据元素信息外，还要存储它的后继元素的存储地址。

## 链表优点
1. 插入删除效率高，时间复杂度为O(1)。
2. 不许要预先分配内存空间

## 链表缺点
存取效率低，时间复杂度为O(n)

## 链表使用场景
1. 不需要预先知道数据规模
2. 适用于插入删除操作较多，而存取操作较少的情况。



## 链表反转
```java
    class Node {  
        private int Data;// 数据  
        private Node Next;// 指针
      
        public Node(int Data) {  
            this.Data = Data;  
        }  
      
        public int getData() {  
            return Data;  
        }  
      
        public void setData(int Data) {  
            this.Data = Data;  
        }  
      
        public Node getNext() {  
            return Next;  
        }  
      
        public void setNext(Node Next) {  
            this.Next = Next;  
        }  
    }  
    

public class Test {  
    public static void main(String[] args) {  
        Node head = new Node(0);  
        Node node1 = new Node(1);  
        Node node2 = new Node(2);  
        Node node3 = new Node(3);  
  
        head.setNext(node1);  
        node1.setNext(node2);  
        node2.setNext(node3);  
  
        // 打印反转前的链表  
        Node h = head;  
        while (null != h) {  
            System.out.print(h.getData() + " ");  
            h = h.getNext();  
        }  
        // 调用反转方法  
        head = reverse2(head);  
  
        System.out.println("\n**************************");  
        // 打印反转后的结果  
        while (null != head) {  
            System.out.print(head.getData() + " ");  
            head = head.getNext();  
        }  
    }  
  
    /** 
     * 遍历，将当前节点的下一个节点缓存后更改当前节点指针 
     */  
    public static Node reverse2(Node head) {  
        if (head == null)  
            return head;  
        Node pre = head;// 上一结点  
        Node cur = head.getNext();// 当前结点  
        Node tmp;// 临时结点，用于保存当前结点的指针域（即下一结点）  
        while (cur != null) {// 当前结点为null，说明位于尾结点  
            tmp = cur.getNext();  
            cur.setNext(pre);// 反转指针域的指向  
  
            // 指针往下移动  
            pre = cur;  
            cur = tmp;  
        }  
        head.setNext(null);  
        return pre;  
    }  
}  
```
