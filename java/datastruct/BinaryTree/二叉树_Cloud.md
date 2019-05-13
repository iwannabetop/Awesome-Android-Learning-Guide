在介绍二叉树之前首先简单说一下树的相关概念已做铺垫
# 树的定义
树的定义：树是N个结点（N>=0）个结点的有限集，N=0称为空树。在任意一棵非空树中有以下2个特点：1.只有一个能称为根（Root）的结点；2.当N>1时，其余结点可以分为M(M>0)个互不相交的有限集，其中每个集合本身又一棵树，这些树称为根的子树(SubTree)。
# 结点分类
1. 根节点
2. 叶结点：度为0的结点称为叶结点 (Leaf)，也叫终端结点
3. 分支结点：度不为0的结点称为分支结点或非终端结点
4. 结点的度：即结点拥有的子树数量
5. 树的度：树内部各结点的度的最大值

# 结点的关系
一个结点它的根称为它的**双亲(parent)**，它的子树称为它的**孩子(child)**，拥有同一个双亲的结点互称**兄弟（Sibling）**,结点的**祖先**是从根到该结点的所经分支上的所有结点。反之以某一结点为根的子树中任一结点都称为该结点的**子孙**。

---
树的相关概念介绍完了，下面可以正式讲解二叉树了

# 二叉树的定义
概念：二叉树是n(n>=0)个结点的有限集合，该集合或为空集，或者由一个根结点和两棵互不相交的、分别称为根的左子树和右子树的二叉树组成。

# 二叉树特点
1. 每个结点最多有两棵子树，所以二叉树中不存在度大于2的结点
2. 左子树和右子树是有顺序的，次序不能颠倒
3. 即使树中的某个结点只有一颗子树，也要区分是左子树还是右子树

# 二叉树的存储结构
1. 二叉树顺序存储结构
2. 二叉链表

# 二叉树的创建（二叉链表形式）
```
public class TreeBean {
    
    public String data;
    public TreeBean leftChild;
    public TreeBean rightChild;
    
    public TreeBean(String data,TreeBean lTreeBean,TreeBean rTreeBean) {
        // TODO Auto-generated constructor stub
        this.data = data;
        this.leftChild = lTreeBean;
        this.rightChild = rTreeBean;
    }
    
    
    
}

public class TreeTest {
    
    public static void main(String[] args) {
        TreeBean treeBeanF = new TreeBean("F", null, null);
        TreeBean treeBeanE = new TreeBean("E", null, null);
        TreeBean treeBeanD = new TreeBean("D", treeBeanF, null);
        TreeBean treeBeanC = new TreeBean("C", null, null);
        TreeBean treeBeanB = new TreeBean("B", treeBeanD, treeBeanE);
        TreeBean treeBeanA = new TreeBean("A", treeBeanB, treeBeanC);
        
    }
    
}
```

#  二叉树的遍历（前序、中序、后序）

```
/**
     * 前序遍历
     * @param treeBean
     */
    public static void frontOrderTree(TreeBean treeBean){
        if (treeBean == null) {
            return;
        }
        System.out.println(treeBean.data);
        frontOrderTree(treeBean.leftChild);
        frontOrderTree(treeBean.rightChild);
    }
    /**
     * 中序遍历
     * @param treeBean
     */
    public static void middleOrderTree(TreeBean treeBean){
        if (treeBean == null) {
            return;
        }
        middleOrderTree(treeBean.leftChild);
        System.out.println(treeBean.data);
        middleOrderTree(treeBean.rightChild);
    }
    /**
     * 后序遍历
     * @param treeBean
     */
    public static void backOrderTree(TreeBean treeBean){
        if (treeBean == null) {
            return;
        }
        backOrderTree(treeBean.leftChild);
        backOrderTree(treeBean.rightChild);
        System.out.println(treeBean.data);
    }
```

