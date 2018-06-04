# 堆 
堆是一种特殊的数据结构，一般我们常说的堆是二叉堆，它是一种特殊形式的完全二叉树。堆分为两种：  
1. 大顶堆：每个节点的值都不大于其父节点的值；  
2. 小顶堆：每个节点的值都不小于其父节点的值。

# 堆的代码实现
堆本身就是完全二叉树，所以可以直接用完全二叉树的方式去存储，也就是使用一个一维数组。0位置用来存储元素的个数，1~n来存储元素。    
对于任意一个元素i来说，（1 <= i <= n）,如果它有左孩子，那么它的左孩子一定是2i；如果有右孩子，那么右孩子一定是2i+1。

	public class Heap {
	    private int[] elements;
	
	    public Heap(int maxSize) {
	        elements = new int[maxSize + 1];
	    }
	
	    /**
	     * 判断是否为空堆
	     *
	     * @return
	     */
	    public boolean isEmpty() {
	        return elements[0] == 0;
	    }
	
	    /**
	     * 判断堆是否已满
	     *
	     * @return
	     */
	    public boolean isFull() {
	        return elements[0] == (elements.length - 1);
	    }
	
	    /**
	     * 大顶堆添加元素
	     *
	     * @param value
	     */
	    public void insert(int value) {
	        if (isFull()) {
	            throw new IndexOutOfBoundsException("Heap is full");
	        }
	
	
	        //确认新增元素的下标
	        int i = elements[0] + 1;
	        while (i != 1 && value > elements[i / 2]) {
	            //如果比父节点的值大，则父节点下移
	            elements[i] = elements[i / 2];
	            i /= 2;
	        }
	
	        elements[i] = value;
	
	        elements[0]++;
	    }
	
	    /**
	     * 删除大顶堆的根节点元素
	     */
	    public int delete() {
	        if (isEmpty()) {
	            throw new IndexOutOfBoundsException("Heap is Empty");
	        }
	
	        //删除元素，先赋值为最后一个有效元素
	        int delteElement = elements[1];
	        elements[1] = elements[elements[0]];
	        elements[0]--;
	        int temp = elements[1];
	        //重建堆
	        int parent = 1;
	        int child = 2;
	        //循环至叶子节点
	        while (child <= elements[0]) {
	            if (child < elements[0] && elements[child] < elements[child + 1]) {
	                child++;
	            }
	
	            if (temp > elements[child]) {
	                break;
	            }
	
	            elements[parent] = elements[child];
	            parent = child;
	            child *= 2;
	        }
	
	        //把最后一个有效元素放到该位置
	        elements[parent] = temp;
	        return delteElement;
	    }
	}

# 插入删除的时间复杂度
- 插入：插入的需要向上比较各个节点，对于有n个节点的堆来说，树的高度为O(logn),所以插入的时间复杂度为O(logn)。
- 删除：删除的操作时一个向下比较的过程，所以删除的时间复杂度也为O(logn)。

# 堆排序
堆排序就是对数组进行堆初始化，再插入元素再删除元素完成排序的操作，其时间复杂度为O(nlogn)。  
过程如下：首先需要初始化一个堆，然后把无序待排数组中的每个值依次插入到堆中，然后依次执行删除操作，并把被删除的元素放到数组的最后一个有效元素之后的一个位置，最后得到期望的有序数组。  

	    public void sort() {
	        if (isEmpty()) {
	            throw new RuntimeException("Heap is Empty");
	        }
	
	        //依次删除元素，放入数组的最后
	        int size = elements[0];
	        for (int i = 0; i < size; i++) {
	            int deleteElement = delete();
	            elements[elements[0] + 1] = deleteElement;
	        }
	
	        for (int i = 0; i < size; i++) {
	            System.out.print(elements[i]);
	        }
	    }