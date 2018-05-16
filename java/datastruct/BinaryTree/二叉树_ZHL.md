# 二叉树
# 概念
二叉树是有限个节点的集合，这个集合可以是空集，也可以是一个根节点和两棵不相交的子二叉树组成的集合，其中一棵树作为根的左子数，另一棵树叫做根的右子树。
# 二叉树的实现


	public class BinaryTreeNode<T> {
	
	    /**
	     * 数据
	     */
	    private T data;
	
	    /**
	     * 左孩子
	     */
	    private BinaryTreeNode<T> leftChild;
	
	    /**
	     * 右孩子
	     */
	    private BinaryTreeNode<T> rightChild;
	
	    public T getData() {
	        return data;
	    }
	
	    public void setData(T data) {
	        this.data = data;
	    }
	
	    public BinaryTreeNode<T> getLeftChild() {
	        return leftChild;
	    }
	
	    public void setLeftChild(BinaryTreeNode<T> leftChild) {
	        this.leftChild = leftChild;
	    }
	
	    public BinaryTreeNode<T> getRightChild() {
	        return rightChild;
	    }
	
	    public void setRightChild(BinaryTreeNode<T> rightChild) {
	        this.rightChild = rightChild;
	    }
	}  


	public class BinaryTree<T> {
	
	    private BinaryTreeNode<T> root;
	
	    public BinaryTree() {
	    }
	
	    public BinaryTree(BinaryTreeNode root) {
	
	    }
	
	    public BinaryTreeNode<T> getRoot() {
	        return root;
	    }
	
	    public void setRoot(BinaryTreeNode<T> root) {
	        this.root = root;
	    }
	
	    /**
	     * 清空树
	     */
	    public void clear() {
	        clear(root);
	    }
	
	
	    /**
	     * 清空某个子树的节点
	     *
	     * @param node
	     */
	    public void clear(BinaryTreeNode node) {
	        if (node == null) {
	            return;
	        }
	        //递归删除左子节点
	        clear(node.getLeftChild());
	        //递归删除右子节点
	        clear(node.getRightChild());
	        //删除节点
	        node = null;
	    }
	
	
	    /**
	     * 获取二叉树高度
	     *
	     * @return
	     */
	    public int height() {
	        return height(root);
	    }
	
	    /**
	     * 获取某节点为子树的高度
	     *
	     * @param node
	     * @return
	     */
	    public int height(BinaryTreeNode node) {
	        if (node == null) {
	            //递归结束，空子树高度为0
	            return 0;
	        } else {
	            //递归获取左、右子树高度
	            int l = height(node.getLeftChild());
	            int r = height(node.getRightChild());
	            //取高的一边
	            return l > r ? l + 1 : r + 1;
	        }
	    }
	
	    /**
	     * 获取二叉树节点数
	     *
	     * @return
	     */
	    public int size() {
	        return size(root);
	    }
	
	    /**
	     * 获取某节点为子树的节点数
	     *
	     * @param node
	     * @return
	     */
	    public int size(BinaryTreeNode node) {
	        if (node == null) {
	            //节点为空，节点数为零
	            return 0;
	        } else {
	            //递归获取左、右子树节点数
	            int l = size(node.getLeftChild());
	            int r = size(node.getRightChild());
	            return l + r + 1;
	        }
	    }
	
	    /**
	     * 查找node节点在二叉树中的父节点
	     *
	     * @param node
	     * @return
	     */
	    public BinaryTreeNode getParent(BinaryTreeNode node) {
	        return getParent(root, node);
	    }
	
	
	    /**
	     * 查找node节点在subTree子树中的父节点
	     *
	     * @param subTree
	     * @param node
	     * @return
	     */
	    public BinaryTreeNode getParent(BinaryTreeNode subTree, BinaryTreeNode node) {
	        if (subTree == null || subTree == node) {
	            return null;
	        }
	
	        if (node == subTree.getLeftChild() || node == subTree.getRightChild()) {
	            //子树树根的左、右孩子节点是所找节点，这个子树树根为所在父节点
	            return subTree;
	        }
	
	
	        //先递归左子树查找
	        BinaryTreeNode parent = getParent(subTree.getLeftChild(), node);
	        if (parent != null) {
	            return parent;
	        }
	
	        //递归右子树查找
	        return getParent(subTree.getRightChild(), node);
	
	    }
	
	    /**
	     * 获取某个节点的左子树
	     * @param node
	     * @return
	     */
	    public BinaryTreeNode getLeftTree(BinaryTreeNode node) {
	        return node.getLeftChild();
	    }
	
	    /**
	     * 获取某个节点的右子树
	     * @param node
	     * @return
	     */
	    public BinaryTreeNode getRightTree(BinaryTreeNode node) {
	        return node.getRightChild();
	    }
	
	    /**
	     * 给某节点插入左子节点
	     * @param parent
	     * @param newNode
	     */
	    public void insertLeft(BinaryTreeNode<T> parent, BinaryTreeNode<T> newNode){
	        parent.setLeftChild(newNode);
	    }
	
	    /**
	     * 给某节点插入右子节点
	     * @param parent
	     * @param newNode
	     */
	    public void insertRight(BinaryTreeNode<T> parent, BinaryTreeNode<T> newNode){
	        parent.setRightChild(newNode);
	    }
	    
	    
	    /**
	     * 判断二叉树是否为空
	     *
	     * @return
	     */
	    public boolean isEmpty() {
	        return root == null;
	    }
	}

# 递归方式遍历二叉树  

 		/**
	     * 先序遍历
	     * 1.访问根节点
	     * 2.先序遍历左子树
	     * 3.先序遍历右子树
	     * @param node
	     */
	    public void preOrder(BinaryTreeNode node){
	        if (node == null) {
	            return;
	        }
	        
	        System.out.print(node.getData());
	        preOrder(node.getLeftChild());
	        preOrder(node.getRightChild());
	        
	    }
	
	    /**
	     * 中序遍历
	     * 1.中序遍历左子树
	     * 2.访问根节点
	     * 3.中序遍历右子树
	     * @param node
	     */
	    public void inOrder(BinaryTreeNode node){
	        if (node == null) {
	            return;
	        }
	
	        inOrder(node.getLeftChild());
	        System.out.print(node.getData());
	        inOrder(node.getRightChild());
	    }
	
	    /**
	     * 后序遍历
	     * 1.后序遍历左子树
	     * 2.后序遍历右子树
	     * 3.访问根节点
	     * @param node
	     */
	    public void posOrder(BinaryTreeNode node){
	        if (node == null) {
	            return;
	        }
	
	        posOrder(node.getLeftChild());
	        posOrder(node.getRightChild());
	        System.out.print(node.getData());
	    }

