### 对比分析

|            |   数组   |   链表   |
| :--------: | :------: | :------: |
|  内存分布  |   连续   |   散乱   |
|  随机访问  |    快    |    慢    |
|  元素查找  | 依次遍历 | 依次遍历 |
|  元素增删  |    慢    |    快    |
| 空间利用率 |    低    |    高    |

### 链表逆序

```java
 public static Node reverse(Node head) {  
        if (head == null || head.next() == null) {  
            return head;  
        }  
        Node reHead = reverse(head.next);
        head.next.next=head;  
        head.next=null; 
        return reHead;
    }  
```

