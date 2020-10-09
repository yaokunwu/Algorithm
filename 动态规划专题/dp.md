# 动态规划专题

## 九章算法

### 动态规划组成部分一： 确定状态
* f[i] 和 f[i][j]代表什么
* 最后一步 -> 子问题 -> 状态

### 动态规划组成部分二： 转移方程

### 动态规划组成部分三： 初始条件和边界情况

### 动态规划组成部分四： 计算顺序 （从小到大）

#### DP course#1

* [Example 1: Coin Change](https://www.lintcode.com/problem/coin-change/description)<br>
//1. State: dp[x] represent the minimum number of coins to fill the amount <br>
//2. State transfer: dp[x] = min{dp[x - k] | for all k} + 1<br>
//3. dp[0] = 0<br>
```Java
public int coinChange(int[] coins, int amount) {
    int[] dp = new int[amount + 1];
    dp[0] = 0;
    for (int i = 1; i < amount + 1; i++) {
        dp[i] = Integer.MAX_VALUE;
        for (int j = 0; j < coins.length; j++) {
            if (i >= coins[j] && dp[i - coins[j]] != Integer.MAX_VALUE) {
                dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1);
            }
        }
    }
    return dp[amount] == Integer.MAX_VALUE ? -1 : dp[amount];
}
```

* [Example 2: Unique Path](https://www.lintcode.com/problem/unique-paths/description)<br>
//1. State: dp[i][j] represent how many way from source to grid[i][j] <br>
//2. State transfer: dp[i][j] = dp[i - 1][j] + dp[i][j - 1] <br>
//3. dp[0][0] = 1, dp[row][0] = 1, dp[0][col] = 1 <br>
```Java
public int uniquePaths(int m, int n) {
    int[][] dp = new int[m][n];
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (i == 0 || j == 0) {
                dp[i][j] = 1;
            } else {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
    }
    return dp[m - 1][n - 1];
}
```

* [Example 3: Jump Game](https://www.lintcode.com/problem/jump-game/description)<br>
//1. State: dp[i] represent whether #i can be jumped to.<br>
//2. State transfer: dp[i] = true if Exist j, where j < i that arr[j] + j >= i<br>
//3. dp[0] = true<br>
```Java
public boolean canJump(int[] A) {
    boolean[] dp = new boolean[A.length];
    dp[0] = true;
    for (int i = 1; i < A.length; i++) {
        for (int j = 0; j < i; j++) {
                dp[i] = dp[i] || (dp[j] && A[j] + j >= i);
            }
        }
    }
    return dp[A.length - 1];
}
```

#### DP course#2
坐标型动态规划(例题）
* [Example 1: Unique Path II](https://www.lintcode.com/problem/unique-paths-ii/description)<br>
//1. State: dp[i][j] represent how many way from source to grid[i][j] <br>
//2. State transfer: dp[i][j] = dp[i - 1][j] + dp[i][j - 1] if obstacleGrid[i][j] != 1 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;: dp[i][j] = 0 if obstacleGrid[i][j] == 1 <br>
//3. dp[0][0] = 1, dp[row][0] = 1, dp[0][col] = 1 <br>
```Java
public int uniquePathsWithObstacles(int[][] obstacleGrid) {
    if (obstacleGrid == null || obstacleGrid.length == 0 || obstacleGrid[0].length == 0
        || obstacleGrid[0][0] == 1) {
        return 0;
    }
    
    int rows = obstacleGrid.length;
    int cols = obstacleGrid[0].length;
    int[][] dp = new int[rows][cols];
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (obstacleGrid[i][j] == 1) {
                dp[i][j] = 0;
            } else {
                if (i == 0 && j == 0) {
                    dp[i][j] = 1;
                } else {
                    if (i - 1 >= 0) {
                        dp[i][j] += dp[i - 1][j];
                    }
                    if (j - 1 >= 0) {
                        dp[i][j] += dp[i][j - 1];
                    }
                }
            }
        }
    }
    return dp[rows - 1][cols - 1];
}
```

序列型动态规划(例题）
* [Example 2: Paint House](https://www.lintcode.com/problem/paint-house/description)<br>
//1. State: dp[i][j] represent the minimum cost of pre i houses with color j<br>
//2. State transfer: dp[i][0] = min(dp[i - 1][1] + cost[i - 1][0], dp[i - 1][2] + cost[i - 1][0]);<br>
: dp[i][0] = min(dp[i - 1][1] + cost[i - 1][0], dp[i - 1][2] + cost[i - 1][0]);<br>
//3. dp[0][0] = dp[0][1] = dp[0][2] = 0;<br>
```Java
public int minCost(int[][] costs) {
    if (costs == null) {
        return 0;
    }
    int nHouses = costs.length;
    int[][] dp = new int[nHouses + 1][3];
    dp[0][0] = 0;
    dp[0][1] = 0;
    dp[0][2] = 0;
    for (int i = 1; i <= nHouses; i++) {
        for (int j = 0; j < 3; j++) {
            dp[i][j] = Integer.MAX_VALUE;
            for (int k = 0; k < 3; k++) {
                if (j == k) {
                    continue;
                }
                dp[i][j] = Math.min(dp[i][j], dp[i - 1][k] + costs[i - 1][j]);
            }
        }
    }
    return Math.min(Math.min(dp[nHouses][0],dp[nHouses][1]), dp[nHouses][2]);
}
```

划分型动态规划(例题）
* [Example 3: Decode Ways](https://www.lintcode.com/problem/decode-ways/description)<br>
//1. State: dp[i] represent how many way to decode the string from previous i<br>
//2. State transfer: dp[i] = dp[i - 1] | string[i - 1] is a valid number + dp[i - 2] | string[i - 1 to i - 2] is a valid number<br>
//3. dp[0] = 1<br>
```Java
public int numDecodings(String s) {
    if (s == null || s.length() == 0) {
        return 0;
    }
    char[] nums = s.toCharArray();
    int[] dp = new int[nums.length + 1];
    dp[0] = 1;
    for (int i = 1; i <= nums.length; i++) {
        dp[i] = 0;
        if (nums[i - 1] > '0') {
            dp[i] += dp[i - 1];
        }
        if (i > 1) {
            int twoDigit = (nums[i - 2] - '0') * 10 + (nums[i - 1] - '0');
            if (twoDigit >= 10 && twoDigit <= 26) {
                dp[i] += dp[i - 2];
            }
        }
    }
    return dp[nums.length];
}
```

### 坐标型动态规划(系统讲解）
* f[i] 中的下标i表示以ai为结尾的满足条件的子序列的性质 (注意坐标型动规的最后一步指的是以ai为结尾的最后一步） 
* [Example 4: Longest increasing continuous subsequence](https://www.lintcode.com/problem/longest-continuous-increasing-subsequence/description)<br>
//1. State: dp[i] represent the longest length that ends with i.<br>
//2. State transfer: dp[i] = max(1, dp[i - 1] + 1 if i >= 1 && arr[i - 1] < arr[i])<br>
//3. dp[0] = 1<br>
```Java
public int longestIncreasingContinuousSubsequence(int[] A) {
    int[] A2 = new int[A.length];
    for (int i = 0; i < A.length; i++) {
        A2[i] = A[A.length - i - 1];
    }
    int res = Math.max(helper(A),helper(A2));
    return res;
}

private int helper(int[] A) {
    if (A == null || A.length == 0) {
        return 0;
    }
    int res = 0;
    int[] dp = new int[A.length];
    for (int i = 0; i < A.length; i++) {
        dp[i] = 1;
        if (i > 0 && A[i - 1] < A[i]) {
            dp[i] = dp[i - 1] + 1;
        }
        res = Math.max(res, dp[i]);
    }
    return res;
}
```

* [Example 5: Minumum Path Sum](https://www.lintcode.com/problem/minimum-path-sum/description)<br>
//1. State: dp[i][j] represent the minimum path sum ends at matrix[i][j]<br>
//2. State transfer: dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]) + matrix[i][j]<br>
//3. dp[0][0] = matrix[0][0], first row and first col<br>
```Java
public int minPathSum(int[][] grid) {
    if (grid == null || grid.length == 0 || grid[0].length == 0) {
        return 0;
    }
    int m = grid.length;
    int n = grid[0].length;
    int[][] dp = new int[m][n];
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (i == 0 && j == 0) {
                dp[0][0] = grid[0][0];
                continue;
            }
            int t = Integer.MAX_VALUE;
            if (i - 1 >= 0) {
                t = Math.min(t, dp[i - 1][j]);
            }
            if (j - 1 >= 0) {
                t = Math.min(t, dp[i][j - 1]);
            }
            dp[i][j] = t + grid[i][j];
        }
    }
    return dp[m - 1][n - 1];
}
```
* [Example 6: Bomb Enemy](https://www.lintcode.com/problem/bomb-enemy/description)<br>
//1. State: dp[i][j] represent the maximum number of enemy being bombed at grid[i][j] in direction k.<br>
//2. State transfer: dp[i][j] = dp[i ? 1][j ? 1] + (1)<br>
//3. first row, first col; first row, last col; last row, first col; last row, last col;<br>
```Java
public int maxKilledEnemies(char[][] grid) {
    if (grid == null || grid.length == 0 || grid[0].length == 0) {
        return 0;
    }
    int m = grid.length;
    int n = grid[0].length;
    int[][] dp = new int[m][n];
    int[][] res = new int[m][n];
    // k = 0;
    for (int j = n - 1; j >= 0; j--) {
        for (int i = 0; i < m; i++) {
            dp[i][j] = 0;
            if (grid[i][j] == 'E') {
                dp[i][j] = 1;
            } 
            if (grid[i][j] != 'W' && j + 1 < n) {
                dp[i][j] += dp[i][j + 1];
            }
            res[i][j] += dp[i][j];
        }
    }
    //k = 1
    for (int i = m - 1; i >= 0; i--) {
        for (int j = 0; j < n; j++) {
            dp[i][j] = 0;
            if (grid[i][j] == 'E') {
                dp[i][j] = 1;
            } 
            if (grid[i][j] != 'W' && i + 1 < m) {
                dp[i][j] += dp[i + 1][j];
            }
            res[i][j] += dp[i][j];
        }
    }
    //k = 2
    for (int j = 0; j < n; j++) {
        for (int i = 0; i < m; i++) {
            dp[i][j] = 0;
            if (grid[i][j] == 'E') {
                dp[i][j] = 1;
            } 
            if (grid[i][j] != 'W' && j - 1 >= 0) {
                dp[i][j] += dp[i][j - 1];
            }
            res[i][j] += dp[i][j];
        }
    }

    //k = 3
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            dp[i][j] = 0;
            if (grid[i][j] == 'E') {
                dp[i][j] = 1;
            } 
            if (grid[i][j] != 'W' && i - 1 >= 0) {
                dp[i][j] += dp[i - 1][j];
            }
            res[i][j] += dp[i][j];
        }
    }
    int result = 0;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == '0') {
                result = Math.max(result, res[i][j]);
            }
        }
    }
    return result;
}
```

* 附带例题 （位运算型动态规划）
* [Example 7: Counting Bits](https://www.lintcode.com/problem/counting-bits/description)<br>
// State: dp[i] represent the number of 1 in the bit representation of num i<br>
// State transfer: dp[i] = dp[i>>1] + i mod 2;<br>
// dp[0] = 0<br>
```Java
public int[] countBits(int num) {
    int[] dp = new int[num + 1];
    for (int i = 0; i <= num; i++) {
        dp[i] = dp[i >>> 1] + i % 2;
    }
    return dp;
}
```
#### DP course#3
### 序列型动态规划(系统讲解）
* f[i] 中的下标i表示以前i个元素a[0], a[1], ..., a[n - 1]的性质
* [Example 1: Paint House II](https://www.lintcode.com/problem/paint-house-ii/description)<br>
//1. dp[i][k] represent the minimum cost to paint the first i house with houst[i - 1] painted as color k.<br>
//2. dp[i][k] = min(dp[i - 1][j not k]) + costs[i - 1][k];<br>
//3. dp[0][k] = 0;<br>
```Java
public int minCostII(int[][] costs) {
    if (costs == null || costs.length == 0 || costs[0].length == 0) {
        return 0;
    }
    int[][] dp = new int[costs.length + 1][costs[0].length];
    //注意求最小值和次小值需要把下标和值分开
    int j1, j2;
    for (int i = 1; i <= costs.length; i++) {
        int min1 = Integer.MAX_VALUE;
        int min2 = Integer.MAX_VALUE;
        j1 = j2 = -1;
        for (int k = 0; k < costs[0].length; k++) {
            if (dp[i - 1][k] < min1) {
                min2 = min1;
                j2 = j1;
                min1 = dp[i - 1][k];
                j1 = k;
            } else if (dp[i - 1][k] < min2) {
                min2 = dp[i - 1][k];
                j2 = k;
            }
        }
        for (int k = 0; k < costs[0].length; k++) {
            if (k != j1) {
                dp[i][k] = min1;
            } else {
                dp[i][k] = min2;
            }
            dp[i][k] += costs[i - 1][k];
        }
    }
    int res = Integer.MAX_VALUE;
    for (int k = 0; k < costs[0].length; k++) {
        res = Math.min(res, dp[costs.length][k]);
    }
    return res;
}
```
* [Example 2: House Robber](https://www.lintcode.com/problem/house-robber/description)<br>













