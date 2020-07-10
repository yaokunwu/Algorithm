# Binary Search
## Template for Binary Search
给一个**有序数组**和目标值，找第一次/最后一次/任何一次出现的索引，如果没有出现返回-1 <br>
模板四点要素<br>
* 1. 初始化：start = 0, end = len - 1;
* 2. 循环条件： start <= end
* 3. 比较中点和目标值： arr[middle] ==, <, > target
* 4. 判断最后两个元素是否符合： A[start], A[end] ? target <br>
时间复杂度O(logn), 使用场景一般是有序数组的查找 <br>
### Examples
[Binary Search](https://leetcode.com/problems/binary-search) <br>
```Java
class Solution {
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return -1;
        }
        int start = 0, end = nums.length - 1;  // start， end 都是index
        while (start <= end) {
            int middle = start + (end - start) / 2;
            if (nums[middle] == target) {
                return middle;
            } else if (nums[middle] < target) {
                start = middle + 1;
            } else {
                end = middle - 1;
            }
        }
        return -1;
    }
}
```
大部分二分查找类的题目都可以用这个模板，然后做一点特殊逻辑即可<br>
另外二分查找还有一些其他模板如下图，大部分场景模板#3都能解决问题，而且还能找第一次/最后一次出现的位置，应用更加广泛 <br>
![image](https://camo.githubusercontent.com/12b8c344966691f1dc81b59f69a9cdd0393356c5/687474703a2f2f7761726473657074656d6265722e636c75622f466c47475039315f72344d3963466d6378444c78524c454575614345) <br>

[Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array) <br>
思路：要求一个起始点和末尾点，所以遇到相等暂时不返回，继续向左找或者向右找。
```Java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int[] res = new int[] {-1, -1};
        if (nums == null || nums.length == 0) {
            return res;
        }
        res[0] = search(nums, target, false);
        if (res[0] == -1) {
            return res;
        }
        res[1] = search(nums, target, true);
        return res;
    }
    
    private int search(int[] nums, int target, boolean toRight) {
        int start = 0, end = nums.length - 1;
        int res = -1;
        while (start <= end) {
            int middle = start + (end - start) / 2;
            if (nums[middle] < target) {
                start = middle + 1;
            } else if (nums[middle] > target) {
                end = middle - 1;
            } else {
                res = middle;
                if (toRight) {
                    start = middle + 1;
                } else {
                    end = middle - 1;
                }
            }
        }
        return res;
    }
}
```
[Search Insert Position](https://leetcode.com/problems/search-insert-position/) <br>
思路：有几个corner case需要事先考虑进去， 然后正常写就行了<br>
```Java
class Solution {
    public int searchInsert(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int start = 0, end = nums.length - 1;
        if (target < nums[start]) {
            return 0;
        }
        if (target > nums[end]) {
            return nums.length;
        }
        
        while (start <= end) {
            int middle = start + (end - start) / 2;
            if (nums[middle] == target) {
                return middle;
            } else if (nums[middle] < target) {
                start = start + 1;
            } else {
                end = end - 1;
            }
        }
        return start;
    }
}
```
[Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix) <br>
思路： 网格的就用id就行了
```Java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0) {
            return false;
        }
        //use the id for each position in the matrix, and perform the same operation as the 1D sorted array
        int nRows = matrix.length;
        int nCols = matrix[0].length;
        int startId = 0, endId = nRows * nCols - 1;
        while (startId <= endId) {
            int middleId = startId + (endId - startId) / 2;
            int row = middleId / nCols;
            int col = middleId % nCols;
            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] < target) {
                startId = middleId + 1;
            } else {
                endId = middleId - 1;
            }
        }
        return false;
    }
}
```

[First Bad Version](https://leetcode.com/problems/first-bad-version/) <br>
思路：二分，如果是bad version那么向前查找，不是bad，向后查找 (用模板3解的)
```Java
public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int start = 1;
        int end = n;
        while (start < end - 1) {
            int middle = start + (end - start) / 2;
            if (isBadVersion(middle)) {
                end = middle;
            } else {
                start = middle;
            }
        }
        if (isBadVersion(start)) {
            return start;
        }
        return end;
    }
}
```
[Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/) <br>
思路：二分，判断左边还是右边递增来解决
```Java
class Solution {
    public int findMin(int[] nums) {
        int res = nums[0];
        int start = 0, end = nums.length - 1;
        while (start <= end) {
            int middle = start + (end - start) / 2;
            // right side is increasing
            if (nums[start] > nums[middle]) {
                res = Math.min(nums[middle], res);
                end = middle - 1;
            } else {
                res = Math.min(nums[start], res);
                start = middle + 1;
            }
        }
        return res;
    }
}
```

[Find Minimum in Rotated Sorted Array II](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/) <br>
思路： 当左中右三个点同时相等时，需要左++ 右--
```Java
class Solution {
    public int findMin(int[] nums) {
        int start = 0, end = nums.length - 1;
        int res = nums[0];
        while (start < end) {
            int middle = start + (end - start) / 2;
            if (nums[middle] == nums[start] && nums[middle] == nums[end]) {
                    start++;
                    end--;
            } else {
                if (nums[middle] < nums[start]) {
                    res = Math.min(res, nums[middle]);
                    end = middle - 1;
                } else{
                    res = Math.min(res, nums[start]);
                    start = middle + 1;
                }
            }
        }
        return Math.min(res, nums[start]);
    }
}
```










