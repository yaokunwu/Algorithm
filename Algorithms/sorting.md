# Sorting
## Most common sorting algorithms
### Quick sort 
**本例有助于理解recursion的实现思考方式，先判断原问题和子问题的关系确定要引入参数，再层与层转换（原问题与子问题的转换）过程中确定需当前层需要完成的内容， 并进入下一次)**  <br>
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
