
# 堆
堆是一种特殊的数据结构，可以理解成是一种特殊的完全二叉树。

堆分为两种，一种是大顶堆（数据最大的元素放在顶部），一种是小顶堆（元素最小的元素放在顶部）。 其实大顶堆和小顶堆是一样的。

大顶堆的定义：堆一般用数组来表示，给定一个 n 个元素的堆，则数组的大小为 n+1，角标表示为0-n，其中角标为0的元素保存了堆的个数。满足以下条件的二叉树，就叫堆。

- 必须是完全二叉树，即叶子节点的层级差不大于1，且最后一层的结点从左至右依次排列
- 任意取第 i 个元素，满足i>=2i和i>=2i+1（n>=2i+1）。
- 对于任意元素 i，如果 i 有左右孩子，那么 i 的左右孩子依次是2i 和2i+1。

好了，就酱紫。

# 堆的基本操作
前面我们说了堆就是一个特殊的二叉树，而我们的操作无非就是增删改查，需要注意的是在增删的过程中，我们要调整结构，使二叉树满足上面的条件。其中，曾加操作永远只添加在堆的末尾，删除操作一般只从顶部开始删除，在执行完增加/删除操作之后，为了保持堆的特效，需要对增加/删除的元素进行升序降序判断。
想象一下，如果添加一个元素在尾部n，因为 n 是新加的元素，为了保持 n/2>n，则必须判断，如果不成立，则替换两个元素的结点，使其保持堆的特效，删除也是同理。

堆一般用于排序，排序的时间复杂度是 n*Log n。且辅助空间为O（1），即堆排序不会损失过多的内存消耗。

# 大顶堆的代码实现及堆排序

```Java
public class MaxHeapTest {

    public static void main(String[] args) {

        int[] array = new int[]{6, 1, 12, 32, 17, 42, 100, 101, 28, 34, 56, 54, 33, 93, 8, 8, 13, 14, 53, 27, 8};
        MaxHeap maxHeap = new MaxHeap(array.length);
        for (int data : array) {
            maxHeap.put(data);
        }
        int size = maxHeap.getSize();
        for (int i = 0; i < size; i++) {
            System.out.println(maxHeap.removeMaxElement());
        }

    }

    public static class MaxHeap {

        int[] elements;

        public MaxHeap(int size) {
            elements = new int[size + 1];
        }

        public void put(int data) {
            if (elements[0] >= elements.length) {
                throw new IndexOutOfBoundsException("堆已经满了");
            }
            int index = elements[0];
            elements[++index] = data;
            upElement(index);
            elements[0]++;
        }

        public int getSize() {
            return elements[0];
        }

        public int removeMaxElement() {
            if (elements[0] <= 0) {
                throw new IndexOutOfBoundsException("堆已经空了");
            }
            int size = elements[0];
            int max = elements[1];
            elements[1] = elements[size];
            downElement(1);
            elements[0]--;
            return max;

        }

        private void downElement(int i) {
            while (i * 2 <= elements[0]) {
                int index = i * 2;
                if (index + 1 <= elements[0] && elements[index] < elements[index + 1]) {
                    index++;
                }
                if (elements[i] > elements[index]) {
                    break;
                }
                swap(i, index);
                i = index;
            }
        }


        private void upElement(int size) {
            while (size > 1 && elements[size] > elements[size / 2]) {
                swap(size, size / 2);
                size /= 2;
            }
        }

        private void swap(int i, int j) {
            int swap = elements[i];
            elements[i] = elements[j];
            elements[j] = swap;
        }

    }

}

```


