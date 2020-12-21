# Sorting
## Most common sorting algorithms
### Quick Sort 
**本例有助于理解recursion的实现思考方式，先判断原问题和子问题的关系确定要引入参数，再层与层转换（原问题与子问题的转换）过程中确定需当前层需要完成的内容， 并进入下一层)**  <br>
```Java
public class Solution {
    public int[] sortArray(int[] nums) {
        if (nums == null || nums.length <= 1) {
            return nums;
        }
        int start = 0, end = nums.length - 1;
        quickSort(nums, start, end); // 因为在把问题减小的过程中，需要quicksort的部分在减小，对应了两个parameter， start和end
        return nums;
    }


    
    private void quickSort(int[] nums, int start, int end) {
        if (start >= end) {  // base case
            return;
        } 
        int pivot = findPivot(nums, start, end);  //在本层做的事情
        quickSort(nums, start, pivot - 1); //进入下一层
        quickSort(nums, pivot + 1, end);    //进入下一层
    }
    //实现在每一层干的事
    private int findPivot(int[] nums, int start, int end) {
        int target = nums[end];
        int i = start;
        for (; start < end; start++) {
            if (nums[start] < target) {
                swap(nums, i, start);
                i++;
            }
        }
        //完成后， i所在位置大于等于target, 然后将pivot放在中间
        swap(nums, i, end);
        return i;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```
### Merge Sort
思路： 先分再和，bottom up，也是recursion，需引入参数， start和end， 分完后，考虑合并的逻辑
```Java 
class Solution {
    public int[] sortArray(int[] nums) {
        if (nums == null || nums.length <= 1) {
            return nums;
        }
        int left = 0, right = nums.length - 1;
        mergeSort(nums, left, right);  // 需要考虑是否要return value；
        return nums;
    }
    
    private void mergeSort(int[] nums, int left, int right) {
        if(left >= right) {
            return;
        }
        int mid = left + (right - left) / 2;
        mergeSort(nums, left, mid);
        mergeSort(nums, mid + 1, right); 
        merge(nums, left, right);
    }
    
    private void merge(int[] nums, int left, int right) {
        int mid = left + (right - left) / 2;
        int i = left, j = mid + 1, k = 0;
        int[] tmp = new int[right - left + 1];
        while (i <= mid && j <= right) {
            if (nums[i] > nums[j]) {
                tmp[k++] = nums[j++];
            } else {
                tmp[k++] = nums[i++];
            }
        }
        while (i <= mid) {
            tmp[k++] = nums[i++];
        }
        while (j <= right) {
            tmp[k++] = nums[j++];
        }
        for (int idx = 0; idx < right - left + 1; idx++) {
            nums[left + idx] = tmp[idx];
        }
    }
}
```
带返回值的mergesort： 模仿二叉树的后序递归，只帮助理解，不建议使用 (暂时没找到一个好的方法从List直接转换成Array)
```Java
class Solution {
    public int[] sortArray(int[] nums) {
        List<Integer> res = new ArrayList<>();
        res = mergeSort(nums, 0, nums.length - 1);  // 需要考虑是否要return value；
        int[] array = res.stream().mapToInt(i->i).toArray();
        return array;
    }
    
    private List<Integer> mergeSort(int[] nums, int start, int end) {
        if( start > end) {
            return new ArrayList<>();
        }
        if( start == end) {
            return Arrays.asList(nums[start]);
        }
        int middle = start + (end - start) / 2;
        List<Integer> left = mergeSort(nums, start, middle);
        List<Integer> right = mergeSort(nums, middle + 1, end); 
        // 当前层逻辑： 因为是bottom up， 底层俩个问题已被解决，当前层需要归并这两层。
        List<Integer> res = new ArrayList<>();
        int i = 0, j = 0;
        while (i < left.size() && j < right.size()) {
            if (left.get(i) < right.get(j)) {
                res.add(left.get(i));
                i++;
            } else {
                res.add(right.get(j));
                j++;
            }
        }
        
        while (i < left.size()) {
            res.add(left.get(i));
            i++;
        }
        while (j < right.size()) {
            res.add(right.get(j));
            j++;
        }
        return res;
    }
}
```

### Heap sort
用数组表示的完美二叉树 complete binary tree <br>
完美二叉树 vs 其他二叉树
![image](https://camo.githubusercontent.com/48c8803d6989e367fe9188215392243d5049e35f/687474703a2f2f7761726473657074656d6265722e636c75622f466c6b444b66646c46686b74432d41756d394432546357572d557864)
[动画展示](https://github.com/wardseptember/notes/blob/master/docs/LeetCode/basic_algorithm/sort.md) <br>
**注意**：(可以新建一个maxHeap然后sort， 也可以heapification inplace，然后再sort)
详情参考[cs61B Basic sorts](https://www.youtube.com/watch?v=AEAmgbls8TM&feature=youtube) <br>
* 一个 数组给定后，相当于给了一个complete binary tree 这样相当于heap就已建立好，需要做的就是遍历并sink每个元素直到maxHeap建立完成 （**Import!!!**: 建立maxHeap时需要（bottom up level order traversal），也就是从array的最后开始sink down,这样才能保证遍历所有元素后我们建立了正确的maxheap） <br>
* 针对bottom up level order sinking, 可以直接添加条件从一半开始up, 因为后一半都是leaf,没有child，不需要sink。
* maxHeap建立完成后就可以进行inplace sorting(常规heap remove 操作， 只不过把remove的值放在了array最后， 然后进行sink)。 <br>
这个代码自己写的，(inplace heapSort)自顶向下<br>
```Java
class Solution {
    //Heap sort
    public int[] sortArray(int[] nums) {
        if (nums.length <= 1) {
            return nums;
        }
        // Heapification
        heapification(nums);
        // inplace sorting
        sorting(nums);
        return nums;
    }
    
    //Heapification
    private void heapification(int[] nums) {
        for (int i = (nums.length - 1) / 2; i >= 0; i--) {
            sink(nums, i, nums.length);
        }
    }
    
    //inplace sorting
    private void sorting(int[] nums) {
        for(int i = nums.length - 1; i > 0 ; i--) {
            swap(nums, 0, i);
            sink(nums, 0, i);
        }
    }
    
    //其实就是写个sink代码, 这个比较好记，因为把比较顺序换了
    private void sink(int[] nums, int i, int upperBound) {
        int largest = i;
        int leftChild = i * 2 + 1;
        int rightChild = leftChild + 1;
        if (leftChild < upperBound && nums[leftChild] > nums[largest]) {
            largest = leftChild;
        }
        if (rightChild < upperBound && nums[rightChild] > nums[largest]) {
            largest = rightChild;
        }
        if (largest != i) {
            swap(nums, i, largest);
            sink(nums, largest, upperBound);
        }
    }
    
    private void swap(int[] nums, int i, int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```
## 参考
[十大经典排序](https://www.cnblogs.com/onepixel/p/7674659.html) <br>
[二叉堆](https://labuladong.gitbook.io/algo/shu-ju-jie-gou-xi-lie/er-cha-dui-xiang-jie-shi-xian-you-xian-ji-dui-lie)<br>
