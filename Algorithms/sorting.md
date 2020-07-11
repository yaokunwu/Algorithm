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
