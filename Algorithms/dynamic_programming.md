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
        boolean isob = false;
        for (int row = 0; row < nRows; row++) {
            if (obstacleGrid[row][0] == 1) {
                isob = true;
            }
            dp[row][0] = isob? 0 : 1;
        }
        
        isob = false;
        for (int col = 0; col < nCols; col++) {
            if (obstacleGrid[0][col] == 1) {
                isob = true;
            }
            dp[0][col] = isob? 0 : 1;
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








