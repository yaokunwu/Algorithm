# Sorting
## Most common sorting algorithms
### Quick Sort 
**本例有助于理解recursion的实现思考方式，先判断原问题和子问题的关系确定要引入参数，再层与层转换（原问题与子问题的转换）过程中确定需当前层需要完成的内容， 并进入下一层)**  <br>
```Java
public class Solution {
    // inplace sorting algorithm
    public int[] quickSort(int[] nums) {
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
    private int findPivot(int[] nums, start, end) {
        int target = nums[end];
        int i = start;
        for (; start < end; start++) {
            if (nums[start] < target) {
                swap(i, start);
                i++;
            }
        }
        //完成后， i所在位置大于等于target, 然后将pivot放在中间
        swap(i, end);
    }
    
    private void swap(int[] nums, i, j) {
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
        int start = 0, end = nums.length - 1;
        mergeSort(nums, start, end);  // 需要考虑是否要return value；
        return nums;
    }
    
    private void mergeSort(int[] nums, int start, int end) {
        if( start >= end) {
            return;
        }
        int middle = start + (end - start) / 2;
        mergeSort(nums, start, middle);
        mergeSort(nums, middle + 1, end); 
        // 当前层逻辑： 因为是bottom up， 底层俩个问题已被解决，当前层需要归并这两层。
        merge(nums, start, end);
    }
    
    private void merge(int[] nums, int start, int end) {
        int middle = start + (end - start) / 2;
        int i = start, j = middle + 1;
        int[] tmp = new int[nums.length];
        int idx = start;
        while (i <= middle && j <= end) {
            if (nums[i] > nums[j]) {
                tmp[idx] = nums[j];
                idx++;
                j++;
            } else {
                tmp[idx] = nums[i];
                i++;
                idx++;
            }
        }
        while (i <= middle) {
            tmp[idx] = nums[i];
            idx++;
            i++;
        }
        while (j <= end) {
            tmp[idx] = nums[j];
            idx++;
            j++;
        }
        System.arraycopy(tmp, start, nums, start, end - start + 1);
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
详情参考[cs61B Basic sorts](https://www.youtube.com/watch?v=AEAmgbls8TM&feature=youtu.be) <br>
* 一个 数组给定后，相当于给了一个complete binary tree 这样相当于heap就已建立好，需要做的就是遍历并sink每个元素直到maxHeap建立完成 （**Import!!!**: 建立maxHeap时需要（bottom up level order traversal），也就是从array的最后开始sink down,这样才能保证遍历所有元素后我们建立了正确的maxheap）<br>
* 
