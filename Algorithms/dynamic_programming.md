# Dynamic programming
## Background
先从一道题目开始~<br>
[Triangle](https://leetcode.com/problems/triangle/)<br>
思路：两种递归 到 加记忆化搜索 到bottom up 动态规划 <br>
**Preorder traversal
* 考虑到从上层到下层需要层数作为参数因为层数会作为判断base case的条件
* 从某一层到另外一层需要知道那个值被选择，所以当前层所选index要作为参数传入
* 因为是求路径和，返回时路径和的结果需作为参数传递。
所以recursion的function有3个参数。
```Java
class Solution {
    int res;
    public int minimumTotal(List<List<Integer>> triangle) {
        res = Integer.MAX_VALUE;
        nextStep(triangle, 0, 0, 0);
        return res;
    }
    
    private void nextStep(List<List<Integer>> triangle, int level, int sum, int idx) {
        if (level >= triangle.size()) {
            res = Math.min(res, sum);
            return;
        }
        
        int sum1 = sum + triangle.get(level).get(idx);
        nextStep(triangle, level + 1, sum1, idx);
        if (triangle.get(level).size() > 1) {
            int sum2 = sum + triangle.get(level).get(idx + 1);
            nextStep(triangle, level + 1, sum2, idx + 1);
        }
    }
}
```
**Postorder traveral (分治)
```Java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int res = postOrder(triangle, 0, 0);
        return res;
    }
    
    private int postOrder(List<List<Integer>> triangle, int level, int idx) {
        if (level >= triangle.size() - 1) {
            return triangle.get(level).get(idx);
        }
        int left = postOrder(triangle, level + 1, idx);
        int right = postOrder(triangle, level + 1, idx + 1);
        return Math.min(left, right) + triangle.get(level).get(idx);
    }
}
```
**以上两种方法均未通过（超时）
给分治加个memorization试一下<br>
class Solution {
    private Integer[][] cache;
    public int minimumTotal(List<List<Integer>> triangle) {
        cache = new Integer[triangle.size()][triangle.get(triangle.size() - 1).size()];
        int res = postOrder(triangle, 0, 0);
        return res;
    }
    
    private int postOrder(List<List<Integer>> triangle, int level, int idx) {
        if (level >= triangle.size() - 1) {
            return triangle.get(level).get(idx);
        }
        int left, right;
        if (cache[level + 1][idx] == null) {
            left = postOrder(triangle, level + 1, idx);
            cache[level + 1][idx] = left;
        } else {
            left = cache[level + 1][idx];
        }
        
        if (cache[level + 1][idx + 1] == null) {
            right = postOrder(triangle, level + 1, idx + 1);
            cache[level + 1][idx + 1] = right;
        } else {
            right = cache[level + 1][idx + 1];
        }
        return Math.min(left, right) + triangle.get(level).get(idx);
    }
}
```
可以简化成下面```
```Java
public class Solution {
    int row;
    Integer[][] memo;
    
    public int minimumTotal(List<List<Integer>> triangle) {
        row = triangle.size();
        memo = new Integer[row][row];
        return helper(0,0, triangle);
    }
    
    private int helper(int level, int c, List<List<Integer>> triangle){
        if (memo[level][c]!=null)
            return memo[level][c];
        if (level==row-1){
            return memo[level][c] = triangle.get(level).get(c);
        }
        int left = helper(level+1, c, triangle);
        int right = helper(level+1, c+1, triangle);
        return memo[level][c] = Math.min(left, right) + triangle.get(level).get(c);
    }
}
```
**Question #1 : 给preorder recursion加一下memo？？ 咋加啊。。好像加不了？？**
**Question #2 : 怎么直接判断有无重复子问题？  通过画出state space tree 我们可以发现节点5被计算了两次（例子），所以有重复子问题**
动态规划就是把大问题变成小问题，并解决了小问题重复计算的方法 <br>
动态规划和DFS的区别：<br>
* 二叉树 子问题是没有交集的，所以大部分二叉树都用递归和分治法，即DFS，就可以解决 <br>
* 像triangle这种是有重复走的情况，子问题是有交集，所以可以用动态规划来解决<br>

动态规划三个特性：
* 最优子结构<br>
    最优子结构指的是，问题的最优包含子问题的最优解，反过来说就是，我们可以通过子问题的最优解，推导出问题的最优解。如果我们把最优子结构，对应到我们前面
    定义的动态规划问题模型上，那我们也可以理解为， 后面阶段的状态可以通过前面的状态推导出来。
* 无后效性
    无后效性有两层含义，第一层含义是，在推到后面阶段的状态时候，我们只关心前面阶段的状态值，不关心这个状态是怎么一步一步推导出来的。 第二层含义是，
    某阶段状态一旦确定，就不受之后阶段的决策影响。 无后效性是一个非常宽松的要求，只要满足前面提到的动态规划问题模型， 其实基本上都会满足无后效性。
* 重复子问题
    如果用一句话概括一下， 那就是，不同的决策序列，到达某个相同阶段时，可能会产生重复的状态。所以才会用一个数组记录中间结果，避免重复计算。<br>
    
动态规划，bottom up, 这里变成一维数组，因为层号实际上可以不用记录，每次记录上一层的值，到当前层就把以前的覆盖到，动态规划运用场景其中一条就是最优子结构, 往下走不用回头一定是最优的。<br>
```Java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int size = triangle.size();
        // 建立dp矩阵
        int[][] dp = new int[size + 1][size + 1];
        
        // 初始化初值 (对于这道题可省略)
        // for (int i = 0; i < triangle.get(size - 1).size(); i++) {
        //     dp[size - 1][i] = triangle.get(size - 1).get(i);
        // }
        
        // 构建表达式
        for (int level = size - 1; level >= 0; level--) {
            for (int idx = 0; idx <= level ; idx++) {
                dp[level][idx] = Math.min(dp[level + 1][idx], dp[level + 1][idx + 1]) + triangle.get(level).get(idx);
            }
        }
        return dp[0][0];
    }
}
```
这里空间我就不优化了。

## 递归和动态规划的关系 <br>
递归是一种程序的实现方式，函数的自我调用。（注意recursion function的设计，需要的参数等等）<br>
```Java
public Type recursion(Type level, Type param1, Type param2) {
    //达到最大梦境层数，do something退出
    if (level >= maxLevel) {
        return something;
    }
    //当前层需要干的事情
    param1.doSomeThing();
    
    //进入下一层梦境
    recursion(level + 1, param1, param2);
    
    //从下一层梦境回来时，do something。
    清理当前层参数等
}
```
动态规划，是一种解决问题的思想，大规模问题的结果，是由小规模问题的结果运算来的。 动态规划可用递归来实现（Memorization Search, Top-down)<br>
## 使用场景<br>
满足三个条件：最优子结构，无后效性， 重复子问题<br>
简单来说就是：<br>
* 满足以下条件之一
    * 求最大/最小值 (Maximum/Minimum)
    * 求是否可行(Yes/No)
    * 求可行个数(Count(*))
* 满足不能排序或者交换（Can not sort / swap)
如题：[Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/) 位置可以交换，所以不用动态规划<br>
**TO-DO： 上述结论待理解**

## 四点要素
1. 状态 State
    * 灵感，创造力，存储小规模问题的结果
2. 方程 Function
    * 状态之间的联系，怎么通过小的状态，来算大的状态
3. 初始化 Initialization
    * 最极限的小状态是什么，起点在哪
4. 答案 Answer
    * 最大的那个状态是什么，终点在哪
## 常见四种类型
1. Matrix DP (10%)
2. Sequence (40%)
3. Two Sequences DP (40%)
4. Backpack (10%) <br>
注意点
    * 贪心算法大多数题目靠背答案，所以如果能用动态规划就尽量用动态规划，不用贪心算法 **待理解**<br>

### 1. 矩阵类型 （10%）
[Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/) <br>
思路: 先递归 + memo 再到 bottom up
自顶向下
```Java
class Solution {
    private Integer[][] dp;
    public int minPathSum(int[][] grid) {
        // recursion, level
        // base case: reach the last position
        // param: row, col
        // So: define a recursion helper method
        dp = new Integer[grid.length][grid[0].length];
        int res = recur(grid, grid.length - 1, grid[0].length - 1);
        return res;
    }
    
    private int recur(int[][] grid, int row, int col) {
        //base case 2
        if (row < 0 || col < 0) {
            return Integer.MAX_VALUE;
        }
        if (dp[row][col] != null) {
            return dp[row][col];
        }
        //base case 1
        if (row == 0 && col == 0) {
            return grid[0][0];
        }
        
        int up = recur(grid, row - 1, col);
        int left = recur(grid, row, col - 1);
        dp[row][col] = Math.min(up, left) + grid[row][col];
        return dp[row][col];
    }
}
```
Bottom up
```Java
class Solution {
    public int minPathSum(int[][] grid) {
    // 注意加quality checking
        int nRows = grid.length;
        int nCols = grid[0].length;
        if (grid == null || nRows == 0 || nCols == 0) {
            return 0;
        }
        // dp construction
        int[][] dp = new  int[nRows][nCols];
        
        //Initilization
        dp[0][0] = grid[0][0];
        
        int sum = grid[0][0];
        for (int row = 1; row < nRows; row++) {
            sum += grid[row][0];
            dp[row][0] = sum;
        }
        
        sum = grid[0][0];
        for (int col = 1; col < nCols; col++) {
            sum += grid[0][col];
            dp[0][col] = sum;
        }
        
        // Formulation
        for (int row = 1; row < nRows; row++) {
            for (int col = 1; col < nCols; col++) {
                dp[row][col] = Math.min(dp[row][col - 1], dp[row - 1][col])
                    + grid[row][col];
            }
        }
        return dp[nRows - 1][nCols - 1];
    }
}
```
[Unique Paths](https://leetcode.com/problems/unique-paths/) <br>
```Java
class Solution {
    int nRows, nCols;
    Integer[][] dp;
    public int uniquePaths(int m, int n) {
        nRows = m;
        nCols = n;
        dp = new Integer[m][n];
        int res = helper(m - 1, n - 1);
        return res;
    }
    
    private int helper(int row, int col) {
        if (row == 0 || col == 0) {
            return 1;
        }
        
        if (dp[row][col] != null) {
            return dp[row][col];
        }
        
        int up = helper(row - 1, col);
        int left = helper(row, col - 1);
        dp[row][col] = up + left;
        return dp[row][col];
    }
}
```
Bottom up
```Java
class Solution {
    public int uniquePaths(int m, int n) {
        // dp construction
        int[][] dp = new int[m][n];
        
        //Initialization
        for (int row = 0; row < m; row++) {
            dp[row][0] = 1;
        }
        for (int col = 0; col < n; col++) {
            dp[0][col] = 1;
        }
        
        // formulation
        for (int row = 1; row < m; row++) {
            for (int col = 1; col < n; col++) {
                dp[row][col] = dp[row - 1][col] + dp[row][col - 1];
            }
        }
        
        return dp[m - 1][n - 1];
    }
}
```
**TO-DO 回顾dp pattern里面是怎么讲解空间压缩的，有点忘了**

[Unique Paths II](https://leetcode.com/problems/unique-paths-ii/) <br>
思路：除了初始化的时候不一样，其他地方差不多。
```Java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int nRows = obstacleGrid.length;
        int nCols = obstacleGrid[0].length;
        //dp construction
        int[][] dp = new int[nRows][nCols];
        
        //initilization
        for (int row = 0; row < nRows; row++) {
            if (obstacleGrid[row][0] == 1) {
                break;
            }
            dp[row][0] = 1;
        }
        
        for (int col = 0; col < nCols; col++) {
            if (obstacleGrid[0][col] == 1) {
                break;
            }
            dp[0][col] = 1;
        }
        // formulation
        for (int row = 1; row < nRows; row++) {
            for (int col = 1; col < nCols; col++) {
                if (obstacleGrid[row][col] == 1) {
                    dp[row][col] = 0;
                } else {
                    dp[row][col] = dp[row - 1][col] +dp[row][col - 1];
                }
            }
        }
        return dp[nRows - 1][nCols - 1];
    }
}
```
Recursion<br>
循环终止条件比较难写（意味着不用memo的话我没找到base case怎么写）, 注意，base case 不好在recursion中找的话就可以直接提前写进memo。
```Java
class Solution {
    Integer[][] dp;
    int nRows, nCols;
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        nRows = obstacleGrid.length;
        nCols = obstacleGrid[0].length;
        dp = new Integer[nRows][nCols];
        boolean isobs = false;
        for (int row = 0; row < nRows; row++) {
            if (obstacleGrid[row][0] == 1) {
                isobs = true;
            }
            dp[row][0] = isobs? 0 : 1;
        }
        isobs = false;
        for (int col = 0; col < nCols; col++) {
            if (obstacleGrid[0][col] == 1) {
                isobs = true;
            }
            dp[0][col] = isobs? 0 : 1;
        }
        
        int res = dfs(obstacleGrid, nRows - 1, nCols - 1);
        return res;
    }
    
    private int dfs(int[][] obstacleGrid, int row, int col) {
        if (dp[row][col] != null) {
            return dp[row][col];
        }
        
        int up = dfs(obstacleGrid, row - 1, col);
        int left = dfs(obstacleGrid, row, col - 1);
        dp[row][col] = obstacleGrid[row][col] == 1? 0 : (up + left);
        
        return dp[row][col];
    }
}
```
**TO-DO 一道题体会 动规，递归，分治，回溯** <br>

### 2. 序列类型（40%）
[Climbing Stairs](https://leetcode.com/problems/climbing-stairs/) <br>
思路: 要么从倒数第二节台阶上来要么从倒数第一阶上来
```Java
class Solution {
    Integer[] dp;
    public int climbStairs(int n) {
        dp = new Integer[n + 1];
        return climb(n);
    }
    
    private int climb(int n) {
        if (n == 1) {
            return 1;
        }
        if (n == 2) {
            return 2;
        }
        if (dp[n] != null) {
            return dp[n];
        }
        int back1 = climb(n - 1);
        int back2 = climb(n - 2);
        dp[n] = back1 + back2;
        return dp[n];
    }
}
```
Bottom up
```Java
class Solution {
    public int climbStairs(int n) {
        int[] dp = new int[n + 1];
        dp[1] = 1;
        dp[2] = 2;
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
}
```

[Jump Game](https://leetcode.com/problems/jump-game/) <br>
思路： 原问题为： 能否从第一个元素跳到最后一个元素，重复子问题为，能否从该元素能跳到的元素跳到最后一个元素。 可以通过recur + memo写 <br>
1491ms。。。 才通过。 大概读个思路吧，我看看别人写的在更新。
```Java
class Solution {
    Boolean[] dp;
    public boolean canJump(int[] nums) {
        dp = new Boolean[nums.length];
        return canJump(nums, 0);
    }
    
    private boolean canJump(int[] nums, int currIdx) {
        if (dp[currIdx] != null) {
            return dp[currIdx];
        }
        
        if (nums[currIdx] >= (nums.length - 1 - currIdx)) {
            return true;
        }
        int num = nums[currIdx];
        boolean curr = false;
        boolean canJ = false;
        for (int i = currIdx; i < currIdx + num; i++) {
            if (canJ == true) {
                break;
            }
            canJ = canJump(nums, i + 1);
        }
        dp[currIdx] = canJ;
        return dp[currIdx];
    }
}
```
**To-DO ： Top-Down 重写**
Bottom - up 
```Java
class Solution {
    public boolean canJump(int[] nums) {
        int[] dp = new int[nums.length];
        dp[0] = nums[0];
        
        for (int i = 1; i < nums.length; i++) {
            if ((dp[i - 1] - 1) < 0) {
                return false;
            }
            dp[i] = Math.max(dp[i - 1] - 1, nums[i]);
        }
        return true;
    }
}
```
[Jump Game II](https://leetcode.com/problems/jump-game-ii/) <br>
思路： 原问题为： 跳到最后一个index的最小次数，子问题为，跳到之前格子的最小次数。 所以，dp[]存储跳到当前格子的最小次数。<br>
DP代码：
```Java
class Solution {
    public int jump(int[] nums) {
        if (nums == null || nums.length <= 1) {
            return 0;
        }
        // construction
        int[] dp = new int[nums.length];
        
        // initialization
        dp[0] = 0;
        
        // formulation
        int idx = 0;
        int steps = nums[0];
        int maxIdx = 0;
        int maxSteps = 0;
        for (int i = 1; i < nums.length; i++) {
            if (i > idx + steps) {
                idx = maxIdx;
                steps = maxSteps;
                maxIdx = 0;
                maxSteps = 0;
            }
            if (nums[i] + i >= maxSteps + maxIdx) {
                maxIdx = i;
                maxSteps = nums[i];
            }
            
            dp[i] = dp[idx] + 1;
        }
        return dp[nums.length - 1];
    }
}
```
思路2：
贪心算法
```Java
class Solution {
    public int jump(int[] nums) {
        int maxPosition = 0;
        int end = 0;
        int steps = 0;
        for (int i = 0; i < nums.length - 1; i++) {
            maxPosition = Math.max(maxPosition, nums[i] + i);
            if (i == end) {
                end = maxPosition;
                steps++;
            }
        }
        return steps;
    }
}
```

[Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/) <br>
思路： 暂时不想做回文串的题目， 稍后回来,(暂时没啥思路)
* 注意Array.fill的使用


[Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) <br>
思路:一个有问题的思路：dp表示前i个数的最长子序列长度， dp[0] = 1, dp[i] = dp[i - 1] or dp[i] 取决于当前数字大小与之前nums在序列中的最大值的关系。 <br>
修改后的思路: dp表示前i个数并选择当前数后的最长子序列长度， dp[i] = max(dp[j]) for j < i if nums[j] < nums[i] , 那么前i项的最长len为max(dp[0 : i])<br>
```Java
class Solution {
    public int lengthOfLIS(int[] nums) {
        if (nums == null || nums.length < 1) {
            return 0;
        }
        int res = 1;
        
        // construction
        int[] dp = new int[nums.length];
        
        // init
        dp[0] = 1;
        
        // formulation
        for (int i = 0; i < nums.length; i++) {
            int maxLen = 0;
            int j = 0;
            while (j < i) {
                if (nums[j] < nums[i]) {
                    maxLen = Math.max(maxLen, dp[j]);
                }
                j++;
            }
            dp[i] = maxLen + 1;
            res = Math.max(dp[i], res);
        }
        return res;
    }
}
```
思路2： 贪心加二分
class Solution {
    //贪心 + 二分
    public int lengthOfLIS(int[] nums) {
        if (nums == null || nums.length < 1) {
            return 0;
        }
        List<Integer> subs = new ArrayList<>();
        subs.add(nums[0]);
        int len = subs.size();
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] > subs.get(len - 1)) {
                subs.add(nums[i]);
                len = subs.size();
            } else {
                int insertIdx = binarySearch(subs, nums[i]);
                subs.remove(insertIdx);
                subs.add(insertIdx, nums[i]);
            }
        }
        return len;
    }
    
    private int binarySearch(List<Integer> subs, int target) {
        if (subs == null || subs.size() < 1) {
            return 0;
        }
        int start = 0, end = subs.size() - 1;
        while (start < end - 1) {
            int middle = start + (end - start) / 2;
            if (subs.get(middle) == target) {
                return middle;
            } else if (subs.get(middle) > target) {
                end = middle;
            } else {
                start = middle;
            }
        }
        
        if (subs.get(start) < target) {
            return end;
        }
        return start;
    }
}
```
**思路二中可以不使用list而使用一个长度为n的数组

[Word Break](https://leetcode.com/problems/word-break/) <br>
思路：dp[i]表示前i个子串能否被字典表示。然后讨论怎么从n推到 n+1就行了
```Java
class Solution {
    //用的是自顶向下格式(格式不是方法)
    public boolean wordBreak(String s, List<String> wordDict) {
        boolean[] dp = new boolean[s.length()];
        
        for (int i = 0; i < s.length(); i++) {
            dp[i] = search(s, i, wordDict, dp) ? true : false;
        }
        return dp[s.length() - 1];
    }
    
    private boolean search(String s, int idx, List<String> wordDict, boolean[] dp){
        for (int i = 0; i < idx; i++) {            
            if (dp[i] == true && isIn(s.substring(i + 1, idx + 1), wordDict)) {
                return true;
            }
        }
        if (isIn(s.substring(0, idx + 1), wordDict)) {
            return true;
        }
    return false;
}
    
    private boolean isIn(String subs, List<String> wordDict) {
        for (String item : wordDict) {
            if(subs.equals(item)) {
                return true;
            }
        }
        return false;
    }
}
```
注意： 查找可以直接用一个set 代替，不用再遍历了。。, 初始化set时候可以直接用 HashSet<>(a list);
```Java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> set = new HashSet<>(wordDict);
        boolean[] dp = new boolean[s.length()];
        
        for (int i = 0; i < s.length(); i++) {
            dp[i] = search(s, i, set, dp) ? true : false;
        }
        return dp[s.length() - 1];
    }
    
    private boolean search(String s, int idx, Set<String> wordDict, boolean[] dp){
        for (int i = 0; i < idx; i++) {            
            if (dp[i] == true && wordDict.contains(s.substring(i + 1, idx + 1))) {
                return true;
            }
        }
        if (isIn(s.substring(0, idx + 1), wordDict)) {
            return true;
        }
    return false;
}
```
小结 <br>
常见处理方式是给0位置占位，这样处理问题时一视同仁，初始化则在原来基础上length + 1; 返回结果 f(n)
* 状态可以为前i个
* 初始化length + 1
* 取值 index = i - 1;
* 返回值： f[n]或者f[m][n] <br>

### Two Sequences DP (40%)
[Longest Common Subsequence]<https://leetcode.com/problems/longest-common-subsequence/> <br>
思路：
