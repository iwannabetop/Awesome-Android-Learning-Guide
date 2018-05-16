
### 二叉树

#### 二叉树的应用场景

二叉树支持动态的插入和查找，保证操作在O(height)时间，这就是完成了哈希表不便完成的工作，动态性。但是二叉树有可能出现worst-case，如果输入序列已经排序，则时间复杂度为O(N)

平衡二叉树/红黑树就是为了将查找的时间复杂度保证在O(logN)范围内。
所以如果输入结合确定，所需要的就是查询，则可以考虑使用哈希表，如果输入集合不确定，则考虑使用平衡二叉树/红黑树，保证达到最大效率

#### 二叉树的前序遍历

```
ArrayList<Integer> preOrder(TreeNode root){
        Stack<TreeNode> stack = new Stack<TreeNode>();
        ArrayList<Integer> list = new ArrayList<Integer>();
        if(root == null){
            return list;
        }
        stack.push(root);
        while(!stack.empty()){
            TreeNode node = stack.pop();
            list.add(node.val);
            if(node.right!=null){
                stack.push(node.right);
            }
            if(node.left != null){
                stack.push(node.left);
            }
            
        }
        return list;
    }

```
#### 二叉树的遗弃遍历

```
ArrayList<Integer> inOrder(TreeNode root){
        ArrayList<Integer> list = new ArrayList<<Integer>();
        Stack<TreeNode> stack = new Stack<TreeNode>();
        TreeNode current = root;
        while(current != null|| !stack.empty()){
            while(current != null){
                stack.add(current);
                current = current.left;
            }
            current = stack.peek();
            stack.pop();
            list.add(current.val);
            current = current.right;
            
        }
        return list;
        
    }
```



#### 二叉树的后序遍历

```
ArrayList<Integer> postOrder(TreeNode root){
        ArrayList<Integer> list = new ArrayList<Integer>();
        if(root == null){
            return list;
        }
        list.addAll(postOrder(root.left));
        list.addAll(postOrder(root.right));
        list.add(root.val);
        return list;
    }

```
