# 动态规划专题

## 九章算法

### 动态规划组成部分一： 确定状态
* f[i] 和 f[i][j]代表什么
* 最后一步 -> 子问题 -> 状态

### 动态规划组成部分二： 转移方程

### 动态规划组成部分三： 初始条件和边界情况

### 动态规划组成部分四： 计算顺序 （从小到大）

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
//1. State: dp[i] represent whether #i can be jumped to.
//2. State transfer: dp[i] = true if Exist j, where j < i that arr[j] + j >= i
//3. dp[0] = true
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
