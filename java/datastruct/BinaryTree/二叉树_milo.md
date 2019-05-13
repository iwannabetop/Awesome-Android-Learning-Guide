# 数据结构与算法-二叉树
## 概念
**二叉树**是一种每个节点最多只有两个字数的树结构。通常子树被称为”左子树“和“右子树”，常被用于二分查找和二叉堆。

**基本形态：**
![](media/15257039158821/15257059114067.jpg)

(1)空二叉树——如图(a)；
(2)只有一个根结点的二叉树——如图(b)；
(3)只有左子树——如图(c)；
(4)只有右子树——如图(d)；
(5)完全二叉树——如图(e)。
## 分类
### 1）满二叉树
左右叶子节点都不为空的二叉树称为满二叉树。
### 2）完全二叉树
叶节点只能出现在最下层和次下层，并且最下一层的节点都集中在该层的最左边的若干二叉树称为完全二叉树。
### 3）平衡二叉树

## 二叉树的实现
* 数组

```
/**
 * Created by milo on 2018/5/10.
 * 数组实现顺序二叉树
 */

public class ArrayBinTree {
    private Object[] arrayBinTree;
    private int DEFAULT_DEEP = 4;//默认深度
    private int deep;
    private int arraySize;

    /**
     * 初始化二叉树
     *
     * @param data 根节点
     */
    public ArrayBinTree(Object data) {
        this.deep = DEFAULT_DEEP;
        this.arraySize = (int) (Math.pow(2, deep) - 1);
        arrayBinTree = new Object[arraySize];
        arrayBinTree[0] = data;
    }


    /**
     * 向指定位置添加子节点
     *
     * @param index
     * @param data
     */
    public void add(int index, Object data, boolean isLeft) {
        if (arrayBinTree[index] == null) {
            throw new RuntimeException("找不到根节点");
        }
        if (index * 2 + 1 >= arraySize) {
            throw new RuntimeException("数组越界");
        }

        if (isLeft) {
            arrayBinTree[index * 2 + 1] = data;
        } else {
            arrayBinTree[index * 2 + 2] = data;
        }

    }

    @Override
    public String toString() {
        return Arrays.toString(arrayBinTree);
    }

    public static void main(String[] arg) {
        ArrayBinTree binTree = new ArrayBinTree("根节点");
        binTree.add(0, "0-left", true);
        binTree.add(1, "1-right", false);

        System.out.println(binTree.toString());
    }

}
```
![](media/15257039158821/15258840793545.jpg)



* 链表


```
/**
 * Created by milo on 2018/5/10.
 * 链表实现二叉树
 */

public class LinkedBinTree {
    private int DEFAULT_DEEP = 4;
    private TreeNode rootTreeNode;
    private int treeSize;

    /**
     * 初始化根节点
     *
     * @param parent
     */
    public LinkedBinTree(String parent) {
        rootTreeNode = new TreeNode(null, null, null, parent);
    }

    public TreeNode add(TreeNode parent, String data, boolean isLeft) {
        if (treeSize > (int) (Math.pow(2, DEFAULT_DEEP) - 1)) {
            throw new RuntimeException("二叉树已满");
        }
        TreeNode treeNode = new TreeNode(null, null, null, data);
        if (isLeft) {
            parent.setLeft(treeNode);
        } else {
            treeNode.setRight(treeNode);
        }

        return treeNode;
    }


    public class TreeNode {
        private TreeNode parent;
        private TreeNode left;
        private TreeNode right;
        private String data;

        public TreeNode(TreeNode parent, TreeNode left, TreeNode right, String data) {
            this.parent = parent;
            this.left = left;
            this.right = right;
            this.data = data;
        }

        public String getData() {
            return data;
        }

        public void setData(String data) {
            this.data = data;
        }

        public TreeNode getParent() {
            return parent;
        }

        public void setParent(TreeNode parent) {
            this.parent = parent;
        }

        public TreeNode getLeft() {
            return left;
        }

        public void setLeft(TreeNode left) {
            this.left = left;
        }

        public TreeNode getRight() {
            return right;
        }

        public void setRight(TreeNode right) {
            this.right = right;
        }
    }

    public static void main(String[] arg) {

        LinkedBinTree linkedBinTree = new LinkedBinTree("根节点");
        LinkedBinTree.TreeNode treeNode1 = linkedBinTree.add(linkedBinTree.rootTreeNode, "根节点 - left", true);
        LinkedBinTree.TreeNode treeNode2 = linkedBinTree.add(treeNode1, "treeNode1 - right", false);

        System.out.println("rootTreeNode: " + linkedBinTree.rootTreeNode.data);
        System.out.println("rootTreeNode left: " + linkedBinTree.rootTreeNode.getLeft().data);
        System.out.println("treeNode1 right: " + treeNode2.data);

    }


}
```

![](media/15257039158821/15258867076072.jpg)


### 遍历
* 中序
* 先序
* 后序


## 应用场景

## 注意点

## 参考
http://www.cnblogs.com/Dylansuns/p/6791852.html


