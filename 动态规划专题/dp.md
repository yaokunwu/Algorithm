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
坐标型动态规划
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

序列型动态规划
* [Example 2: Paint House](https://www.lintcode.com/problem/paint-house/description)<br>
//1. State: dp[i][j] represent the minimum cost of pre i houses with color j
//2. State transfer: dp[i][0] = min(dp[i - 1][1] + cost[i - 1][0], dp[i - 1][2] + cost[i - 1][0]);
//: dp[i][0] = min(dp[i - 1][1] + cost[i - 1][0], dp[i - 1][2] + cost[i - 1][0]);
//3. dp[0][0] = dp[0][1] = dp[0][2] = 0;
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
