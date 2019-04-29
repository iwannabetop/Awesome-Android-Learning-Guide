# 数据结构与算法-栈

## 概念

**栈**是一种特殊的线性表。只能够在固定的一端进行插入、删除操作，即入栈、出栈，规则是LIFO （ last in first out），后进先出。最后入栈的元素位于栈顶，最先入栈的元素位于栈底。

## 常见方法

[参考:](https://developer.android.google.cn/reference/java/util/Stack.html#Stack())

|           | Public methods                           |
| --------- | ---------------------------------------- |
| `boolean` | `empty()`Tests if this stack is empty.   |
| `E`       | `peek()`Looks at the object at the top of this stack without removing it from the stack. |
| `E`       | `pop()`Removes the object at the top of this stack and returns that object as the value of this function. |
| `E`       | `push(E item)`Pushes an item onto the top of this stack. |
| `int`     | `search(Object o)`Returns the 1-based position where an object is on this stack. |



## 栈的分类

### [1）顺序栈](http://www.cnblogs.com/Dylansuns/p/6788919.html)

一种利用一组地址连续的存储单元，依次从栈底到栈顶保存数据的结构叫做顺序栈。

### [2）链式栈](http://www.cnblogs.com/Dylansuns/p/6788961.html)

采用单链表的方式保存栈中的元素，这种链式结构叫做链栈。

