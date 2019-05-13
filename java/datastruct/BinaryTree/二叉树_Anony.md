# 二叉树

根据数据结构，我们可以简单理解二叉树是一个有两个 next 节点的链表，leftNext（左节点）和 rightNext（右节点），且只能有两个节点。当然，二叉树也可以用数组表示法，但是这里我们为了便于理解二叉树的概念，就只考虑链表的结构。
我们先来熟悉一下二叉树的相关术语

- 树的结点：包含一个数据元素及指向子树的分支
- 孩子结点：结点的子树的根称为该节点的孩子
- 双亲结点：B 结点是 A 结点的孩子，则 A 结点是 B 节点的双亲
- 兄弟结点：同一双亲的孩子结点
- 堂兄结点：度相同的所有节点
- 祖先结点：从根到该结点的所经分支上的所有结点
- 子孙结点：以某结点为根的子树中任一结点都是该结点的子孙
- 结点层：跟结点的层定义为1，根的孩子为第二层结点，类推
- 树的深度：树中最大的结点层
- 结点的度：结点子树的个数
- 树的度：树中的最大节点度
- 叶子结点：也叫终端结点，是度为0的结点
- 分枝结点：度不为0的结点
- 有序树：子树有序的树
- 无序树：不考虑子树的顺序

嗯呐，就这些，以上介绍摘抄自网络，大家理解一下每个名称的意思就行了，对着你的家族表去理解，不需要被，只要看到这个词知道意思就行。

二叉树有如下性质，同样理解一下就行了


- 在非空二叉树中，第 i 层的节点总数不超过 2^(i-1)
- 在深度为 h 的二叉树最多有2^(h)-1个节点
- 对于任意一颗二叉树，如果其叶节点为N0，而度数为2的结点总数为 N2，则 N0 = N2 +1（这条有点抽象，根据图理解）
- 具有 n 个结点的完全二叉树的深度为 log_2n(log以2为底的 n)+1

# 使用场景
最基本的数据结构，很多都是基于这个数据结构实现的，硬要说使用场景的话，一般是数据库索引、查找会使用到。Java 中，比较值得学习的有PriorityQueue（基于数组实现的属性优先队列，排序算法是二叉树实现），HashMap（数组+链表+链表转红黑树的实现，红黑树是一种特殊的二叉树）。

# Java 代码实现二叉树并实现添加节点、计算深度、遍历、清空树、计算结点个数功能


```Java

public class BinaryTree {

    public static void main(String[] args) {
        Node<Person> Tree = new Node<>();
    }


    public static class Person implements Comparable<Person> {

        private final int age;

        public Person(int age) {
            this.age = age;
        }

        @Override
        public int compareTo(@NonNull Person o) {
            return age > o.age ? 1 : age == o.age ? 0 : -1;
        }

        @Override
        public String toString() {
            return String.valueOf(age);
        }
    }

    public static class Node<T extends Comparable> {
        private T data;
        private Node<T> left;
        private Node<T> right;

        public T getData() {
            return data;
        }

        public void setData(T data) {
            this.data = data;
        }

        public Node<T> getLeft() {
            return left;
        }

        public Node<T> getRight() {
            return right;
        }

        public void setLeft(Node<T> node) {
            left = node;
        }

        public void setRight(Node<T> node) {
            right = node;
        }

        /**
         * 添加一个节点
         */
        public void addNode(@NonNull T data) {
            if (this.data == null) {
                this.data = data;
            } else if (data.compareTo(this.data) > 0) {
                addToRight(data);
            } else if (data.compareTo(this.data) < 0) {
                addToLeft(data);
            } else {
                this.data = data;
            }
        }

        /**
         * 查找节点
         */
        public Node<T> getNode(@NonNull T data) {
            int i = data.compareTo(this.data);
            if (i == 0) {
                return this;
            } else if (i < 0) {
                return getLeft().getNode(data);
            } else {
                return getRight().getNode(data);
            }
        }

        /**
         * 获取树的高度
         */
        public int getHeigt() {
            if (data == null) {
                return 0;
            }
            int leftHeight = left == null ? 0 : left.getHeigt();
            int rightHeight = right == null ? 0 : right.getHeigt();
            return 1 + (leftHeight > rightHeight ? leftHeight : rightHeight);
        }

        /**
         * 清空树
         */
        public void clear() {
            data = null;
            if (left != null) {
                left.clear();
                left = null;
            }
            if (right != null) {
                right.clear();
                right = null;
            }
        }

        /**
         * 判断树是否为 null
         */
        public boolean isEmpty() {
            return data == null;
        }

        /**
         * 先根遍历
         */
        public void preOrder(Node node) {
            if (node != null) {
                visted();
                preOrder(left);
                preOrder(right);
            }
        }

        /**
         * 中根遍历
         */
        public void inOrder(Node node) {
            if (node != null) {
                inOrder(left);
                visted();
                inOrder(right);
            }

        }

        /**
         * 后根遍历
         */
        public void postOrder(Node node) {
            if (node != null) {
                postOrder(left);
                postOrder(right);
                visted();
            }
        }


        /**
         * 获取节点数
         */
        public int getSize() {
            int size = 0;
            if (data != null) {
                size += 1;
            }
            if (left != null) {
                size += left.getSize();
            }
            if (right != null) {
                size += right.getSize();
            }
            return size;
        }


        private void addToRight(T data) {
            if (right == null) {
                right = new Node<>();
            }
            right.addNode(data);
        }

        private void addToLeft(T data) {
            if (left == null) {
                left = new Node<>();
            }
            left.addNode(data);
        }

        private void visted() {
            System.out.println(data.toString());
        }

    }
}

```


