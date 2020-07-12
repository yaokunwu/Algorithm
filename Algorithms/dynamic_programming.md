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
    
动态规划，bottom up, 这里变成一维数组，因为层号实际上可以不用记录，每次记录上一层的值，到当前层就把以前的覆盖到，动态规划运用场景其中一条就是最优子结构，
往下走不用回头一定是最优的。<br>
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
