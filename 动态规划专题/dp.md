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
#### 序列 + 状态动态规划
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
//1. State: dp[i] represent the maximum being stole by the first i houses<br>
//2. State transfer: dp[i] = max(dp[i - 1], dp[i - 2] + A[i - 1])<br>
//3. dp[0] = 0; dp[1] = A[i - 1];<br>
```Java
public long houseRobber(int[] A) {
    if (A == null || A.length == 0) {
        return 0;
    }
    long[] dp = new long[A.length + 1];
    dp[0] = 0;
    dp[1] = A[0];
    for (int i = 2; i <= A.length; i++) {
        dp[i] = Math.max(dp[i - 1], dp[i - 2] + A[i - 1]);    
    }
    return dp[A.length];
}
```

* [Example 3: House Robber II](https://www.lintcode.com/problem/house-robber-ii/description)<br>
//1. State: dp[i] represent the maximum being stole by the first i houses<br>
//2. State transfer: dp[i] = max(dp[i - 1], dp[i - 2] + A[i - 1])<br>
//3. dp[0] = 0; dp[1] = A[i - 1];<br>
//4. convert to 0 to N - 2 and 1 to N - 1<br>
```Java
public int houseRobber2(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    if (nums.length == 1) {
        return nums[0];
    }
    return Math.max(helper(Arrays.copyOfRange(nums, 0, nums.length - 1)), helper(Arrays.copyOfRange(nums, 1, nums.length)));
}

private int helper(int[] A) {
    if (A == null || A.length == 0) {
        return 0;
    }
    int[] dp = new int[A.length + 1];
    dp[0] = 0;
    dp[1] = A[0];
    for (int i = 2; i <= A.length; i++) {
        dp[i] = Math.max(dp[i - 1], dp[i - 2] + A[i - 1]);    
    }
    return dp[A.length];
}
```
* [Example 4: Best Time to Buy and Sell Stock II](https://www.lintcode.com/problem/best-time-to-buy-and-sell-stock-ii/description)<br>
//1. State dp[i] represent the max profit by the first i days (前i天)<br>
//2. State tranfer: dp[i] = dp[i - 1] if prices[i - 1] < prices[i - 2], dp[i] = dp[i - 1] + prices[i - 1] - prices[i - 2] if prices[i - 1] > prices[i - 2].<br>
//3. dp[0] = 0, dp[1] = 0<br>
```Java
public int maxProfit(int[] prices) {
    if (prices == null || prices.length == 0) {
        return 0;
    }
    int[] dp = new int[prices.length + 1];
    dp[0] = 0;
    dp[1] = 0;
    for (int i = 2; i <= prices.length; i++) {
        dp[i] = dp[i - 1];
        if (prices[i - 1] > prices[i - 2]) {
            dp[i] += prices[i - 1] - prices[i - 2];
        } 
    }
    return dp[prices.length];
}
```
* [Example 5: Best Time to Buy and Sell Stock III](https://www.lintcode.com/problem/best-time-to-buy-and-sell-stock-iii/description)<br>
// State: dp[i][j] represent the maximum profit by previous i days and state at j in day[i - 1], states from 0, 1, 2, 3, 4<br>
// State transfer: <br>
if j is even (not holding stock), dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - 1] + prices[i - 1] - prices[i - 2];<br>
if j is odd (holding stock), dp[i][j] = max(dp[i - 1][j] + prices[i - 1] - prices[i - 2], dp[i - 1][j - 1]<br>
// dp[0][0] = 0; dp[0][1] = dp[0][2] = dp[0][3] = dp[0][4] = Integer.MIN_VALUE because these values are unavailable to use<br>
***注意: 并不是买入扣钱卖出得钱，而是直接考虑的利润***
```Java
public static int maxProfit(int[] prices) {
    if (prices == null || prices.length == 0) {
        return 0;
    }
    int n = prices.length;
    int[][] dp = new int[prices.length + 1][5];
    dp[0][1] = dp[0][2] = dp[0][3] = dp[0][4] = Integer.MIN_VALUE;
    for (int i = 1; i <= prices.length; i++) {
        for (int j = 0; j < 5; j++) {
            if (j % 2 == 0) {
                dp[i][j] = dp[i - 1][j];
                // j == 0, 2, 4
                if (i - 2 >= 0 && j - 1 >= 0 && dp[i - 1][j - 1] != Integer.MIN_VALUE) {
                    dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - 1] + prices[i - 1] - prices[i - 2]);
                }
            } else {
                // j == 1, 3
                dp[i][j] = dp[i - 1][j - 1];
                if (i - 2 >= 0 && dp[i - 1][j] != Integer.MIN_VALUE) {
                    dp[i][j] = Math.max(dp[i][j], dp[i - 1][j] + prices[i - 1] - prices[i - 2]);
                }
            }
        }
    }
    return Math.max(dp[n][0], Math.max(dp[n][2], dp[n][4]));
}
```
* [Example 6: Best Time to Buy and Sell Stock IV](https://www.lintcode.com/problem/best-time-to-buy-and-sell-stock-iv/description)<br>
// State: dp[i][j] represent the maximum profit by previous i days and state at j in day[i - 1], states from 0, 1, 2, 3, 4 ... 2 * k + 1.<br>
// State transfer: <br>
if j is even (not holding stock), dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - 1] + prices[i - 1] - prices[i - 2];<br>
if j is odd (holding stock), dp[i][j] = max(dp[i - 1][j] + prices[i - 1] - prices[i - 2], dp[i - 1][j - 1]<br>
// dp[0][0] = 0; dp[0][1] = dp[0][2] = ... = dp[0][2 * k] = Integer.MIN_VALUE because these values are unavailable to use<br>
***注意: 并不是买入扣钱卖出得钱，而是直接考虑的利润***
```Java
public int maxProfit(int K, int[] prices) {
    if (prices == null || prices.length == 0) {
        return 0;
    }
    int n = prices.length;
    if (K > n) {
        int res = 0;
        for (int i = 1; i < n; i++) {
            if (prices[i] > prices[i - 1]) {
                res += prices[i] - prices[i - 1];
            }
        }
        return res;
    }
    int[][] dp = new int[prices.length + 1][2 * K + 1];
    dp[0][0] = 0;
    for (int k = 1; k < 2 * K + 1; k++) {
        dp[0][k] = Integer.MIN_VALUE;
    }
    for (int i = 1; i <= prices.length; i++) {
        for (int j = 0; j < 2 * K + 1; j++) {
            if (j % 2 == 0) {
                dp[i][j] = dp[i - 1][j];
                // j == 0, 2, 4
                if (i - 2 >= 0 && j - 1 >= 0 && dp[i - 1][j - 1] != Integer.MIN_VALUE) {
                    dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - 1] + prices[i - 1] - prices[i - 2]);
                }
            } else {
                // j == 1, 3
                dp[i][j] = dp[i - 1][j - 1];
                if (i - 2 >= 0 && dp[i - 1][j] != Integer.MIN_VALUE) {
                    dp[i][j] = Math.max(dp[i][j], dp[i - 1][j] + prices[i - 1] - prices[i - 2]);
                }
            }
        }
    }
    int res = dp[n][0];
    for (int k = 0; k < 2 * K + 1; k += 2) {
        res = Math.max(res, dp[n][k]);
    }
    return res;
}
```
#### 最长序列型动态规划
* [Example 7: Longest Increasing Subsequence](https://www.lintcode.com/problem/longest-increasing-subsequence/description)<br>
这是个坐标型 (nlogn算法看第七讲) <br>
//State: dp[i] represent the longest len at the end of num i.<br>
//State transfer: dp[i] = 1 or max(dp[j] + 1) | j < i and a[j] < a[i] <br>
// none<br>
```Java
public int longestIncreasingSubsequence(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int n = nums.length;
    int[] dp = new int[n];
    int res = 0;
    for (int i= 0; i < n; i++) {
        dp[i] = 1;
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                dp[i] = Math.max(dp[i], dp[j] + 1);    
            }
        }
       res = Math.max(res, dp[i]);
    }
    return res;
}
```

* [Example 8: Russian Doll Envelopes](https://www.lintcode.com/problem/russian-doll-envelopes/description)<br>
这是个坐标型 (**TODO**:nlogn算法看完第七讲后自己写)<br>
//State: dp[i] represent the maximum number of envelopes at envelop i<br>
//State transfer: dp[i] = 1 or max(dp[j] + 1) | j < i and width[j] < width[i])<br>
//None, need to sort first<br>
```Java
public int maxEnvelopes(int[][] envelopes) {
    if (envelopes == null || envelopes.length == 0 || envelopes[0].length == 0) {
        return 0;
    }
    Comparator<int[]> c = new Comparator<int[]>() {
        public int compare(int[] a, int[] b) {
            return a[0] - b[0];
        }
    };
    Arrays.sort(envelopes, c);
    int n = envelopes.length;
    int[] dp = new int[n];
    int res = 0;
    for (int i = 0; i < n; i++) {
        dp[i] = 1;
        for (int j = 0; j < i; j++) {
            if (envelopes[j][0] < envelopes[i][0] && envelopes[j][1] < envelopes[i][1]) {
                dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
        res = Math.max(res, dp[i]);
    }
    return res;
}
```
#### DP course#4
### 划分型动态规划(系统讲解）
* 要求将一个序列或字符串或数值划分成若干满足要求的片段 -> 最后一步 -> 最后一段<br>
* [Example 1: Perfect Squares](https://www.lintcode.com/problem/perfect-squares/description)<br>
//State: dp[i] represent the minimum number of perfect squares that sums up to i<br>
//State transfer: dp[i] = min(dp[i - j^2] + 1)<br>
//dp[0] = 0<br>
```Java
public int numSquares(int n) {
    int[] dp = new int[n + 1];
    dp[0] = 0;
    for (int i = 0; i < n; i++) {
        dp[i] = Integer.MAX_VALUE;
        for (int j = 1; j * j < i; j++) {
            dp[i] = Math.min(dp[i], dp[i - j * j] + 1);
        }
    }
    return dp[n];
}    
```

* [Example 2: Palindrome Partitioning II](https://www.lintcode.com/problem/palindrome-partitioning-ii/description)<br>
//State: dp[i] represent the minimum cut of previous string end at i - 1. <br>
//State transfer: dp[i]  = min(dp[j] + 1) | 0 <= j < i and isPalin(j, i - 1)<br>
//dp[0] = 0<br>
```Java
public int minCut(String s) {
    if (s == null || s.length() == 0) {
        return 0;
    }
    int n = s.length();
    //生成判断回文串 (这个代码可以复用，记住)
    boolean[][] isPalin = new boolean[n][n];
    //construct palin
    for (int i = 0; i < n; i++) {
        //odd
        int left, right;
        left = right = i;
        while (left >= 0 && right < n) {
            if (s.charAt(left) != s.charAt(right)) {
                break;
            }
            isPalin[left][right] = true;
            left--;
            right++;
        }
        //even
        left = i;
        right = i + 1;
        while (left >= 0 && right < n) {
            if (s.charAt(left) != s.charAt(right)) {
                break;
            }
            isPalin[left][right] = true;
            left--;
            right++;
        }
    }
    //dp求解
    int[] dp = new int[n + 1];
    dp[0] = 0;
    for (int i = 1; i <= n; i++) {
        dp[i] = Integer.MAX_VALUE;
        for (int j = 0; j < i; j++) {
            if (isPalin[j][i - 1]) {
                dp[i] = Math.min(dp[i], dp[j] + 1);
            }
        }
    }
    return dp[n] - 1;
}
```
* [Example 3: Copy Books](https://www.lintcode.com/problem/copy-books/description)<br>
//State: dp[i][j] represent the minimum time required for j number of persons to copy previous i number of books<br>
//State transfer: dp[i][k] = min(max(dp[j][k - 1], A[j]+...+ A[i])) | 0<j<=i<br>
//dp[0][k] = 0, dp[0][0] = 0, dp[i][0] = maxVal<br>
```Java
public int copyBooks(int[] pages, int K) {
    if (pages == null || pages.length == 0) {
        return 0;
    }
    int n = pages.length;
    int[] preSum = new int[n + 1];
    preSum[0] = 0;
    for (int i = 1; i <= n; i++) {
        preSum[i] = preSum[i - 1] + pages[i - 1];
    }
    int[][] dp = new int[K + 1][n + 1];
    dp[0][0] = 0;
    for (int k = 0; k <= K; k++) {
        dp[k][0] = 0;
    }
    for (int i = 1; i <= n; i++) {
        dp[0][i] = Integer.MAX_VALUE;
    } 
    for (int k = 1; k <= K; k++) {
        for (int i = 1; i <= n; i++) {
            dp[k][i] = Integer.MAX_VALUE;
            int t = Integer.MIN_VALUE;
            for (int j = 0; j < i; j++) {
                t = Math.max(dp[k - 1][j], preSum[i] - preSum[j]);
                dp[k][i] = Math.min(dp[k][i], t);
            }
        }
    }
    return dp[K][n];
} 
```

### 博弈型动态规划(系统讲解）
通常从第一步分析，而不是最后一步
* [Example 1: Coins in a Line](https://www.lintcode.com/problem/coins-in-a-line/description)<br>
//State: dp[i] represent whether it should win facing i number of coins<br>
//State transfer: dp[i] = !(dp[i - 1] && dp[i - 2]);<br>
//dp[0] = false; dp[1] = true;<br>
```Java
public boolean firstWillWin(int n) {
    if (n == 0) {
        return false;
    }
    boolean[] f = new boolean[n + 1];
    int i;
    f[0] = false;
    f[1] = true;
    for (i = 2; i <= n; i++) {
        f[i] = !(f[i - 1] && f[i - 2]);
    }
    return f[n];
}
```

### 背包问题(系统讲解）
* [Example 1: Backpack](https://www.lintcode.com/problem/backpack/description)<br>
//State: dp[i][j] represent whether previous i items can fill weight of j<br>
//State transfer: dp[i][j] = dp[i - 1][j] || dp[i - 1][j - weight[i - 1]]<br>
//dp[0][0] = true;<br>
```Java
public int backPack(int m, int[] A) {
    int n = A.length;
    boolean[][] f = new boolean[n + 1][m + 1];
    int i, j;
    for (i = 0; i <= n; i++) {
        f[i][0] = true;
    }
    for (i = 1; i <= n; i++) {
        for (j = 1; j <= m; j++) {
            f[i][j] = f[i - 1][j];
            if (j >= A[i - 1]) {
                f[i][j] = f[i][j] || f[i - 1][j - A[i - 1]];
            }
        }
    }
    for (i = m; i >= 0; i--) {
        if (f[n][i]) {
            break;
        }
    }
    return i;
}
```
* [Example 2: Backpack V](https://www.lintcode.com/problem/backpack-v/description)<br>
//State: dp[i][j] represent how many way to fill j with previous i item<br>
//State transfer: dp[i][j] = dp[i - 1][j] + dp[i - 1][j - A[i - 1]];<br>
//dp[0][0] = 1;<br>
```Java
public int backPackV(int[] nums, int target) {
    if (nums == null || nums.length == 0) {
        return 1;
    }
    int n = nums.length;
    int[] f = new int[target + 1];
    int i, j;
    f[0] = 1;
    for (i = 1; i <= n; i++) {
        for (j = target; j >= 0; j--) {
            if (j >= nums[i - 1]) {
                f[j] += f[j - nums[i - 1]];
            }
        }
    }
    return f[target];
}
```
* [Example 3: Backpack VI](https://www.lintcode.com/problem/combination-sum-iv/description)<br>
//State: dp[i] represent how many way to fill i<br>
//State transfer: dp[i] = forAll num : nums sum(dp[i - num])<br>
//dp[0] = 1<br>
```Java
public int backPackVI(int[] nums, int target) {
    int[] dp = new int[target + 1];
    dp[0] = 0;
    for (int i = 1; i <= target; i++) {
        for (int j = 0; j < nums.length; j++) {
            if (i >= nums[j]) {
                dp[i] += dp[i - nums[j]];
            }
        }
    }
    return dp[target];
}
```

#### DP course#5
* [Example 1: Backpack II](https://www.lintcode.com/problem/backpack-ii/description)<br>
//State: dp[i][j] represent the maximum profit at weight j for previous i items<br>
//State transfer: dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i - 1]] + profit[i - 1])<br>
//dp[0][0] = 0<br>
```Java
public int backPackII(int m, int[] A, int[] V) {
    int[][] dp = new int[A.length + 1][m + 1];
    dp[0][0] = 0;
    for (int i = 1; i <= A.length; i++) {
        for (int j = 0; j <= m; j++) {
            dp[i][j] = dp[i - 1][j];
            if (j >= A[i - 1]) {
                dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - A[i - 1]] + V[i - 1];
            }
        }
    }
    return dp[A.length][m];
}    
```

* [Example 2: Backpack III](https://www.lintcode.com/problem/backpack-iii/)<br>
//State: dp[i] represent the maximum profit at capacity i<br>
//State transfer: dp[i] = forAll item j max(dp[i - weight[j]] + profit[j])<br>
//dp[0] = 0<br>
```Java
public int backPackII(int m, int[] A, int[] V) {
    int[] dp = new int[m + 1];
    for (int i = 0; i <= m; i++) {
        for (int j = 0; j < A.length; j++) {
            if (i >= A[j]) {
                dp[i] = Math.max(dp[i], dp[i - A[j]] + V[j]);
            }
        }
    }
    return dp[m];
}    
```

### 区间型动态规划
* 给定一个序列/字符串，进行一些操作
* 最后一步会将序列/字符串去头/去尾
* 剩下的会是一个区间[i,j]
* 状态自然定义为f[i][j],表示面对子序列[i...j]时的最优性质
* [Example 1: Longest Palindromic Subsequence](https://www.lintcode.com/problem/longest-palindromic-subsequence/description)<br>
//State: dp[i][j] represent the longest subsequence from i to j<br>
//State transfer: dp[i][j] = max(dp[i + 1][j]| s[i] != s[j], dp[i][j - 1]| s[i] != s[j], dp[i + 1][j - 1] + 2 | s[i] == s[j])<br>
//dp[i][j] = 1 | i == j<br>
```Java
/** 递推　**/
public int longestPalindromeSubseq(String ss) {
    if (ss == null || ss.length() == 0) {
        return 0;
    }
    char[] s = ss.toCharArray();
    int n = s.length;
    int[][] f = new int[n][n];
    for (int i = 0; i < n; i++) {
        f[i][i] = 1;
    }
    //这个循环方式需要背过
    for (int len = 2; len <= n; len++) {
        for (int i = 0; i <= n - len; i++) {
            int j = i + len - 1;
            f[i][j] = Math.max(f[i + 1][j], f[i][j - 1]);
            if (s[i] == s[j]) {
                f[i][j] = Math.max(f[i][j], f[i + 1][j - 1] + 2);
            }
        }
    }
    return f[0][n - 1];
}

/** 递归　**/
private void Compute(int i, int j) {
    if (f[i][j] != null) {
        return;
    }
    if (i == j) {
        return 1;
    }
    
    //this base case is very important
    if (i + 1 == j) {
        f[i][j] = (s[i] == s[j]) ? 2 : 1;
        return;
    }
    
    Compute(i + 1, j);
    Compute(i, j - 1);
    Compute(i + 1, j - 1);
    f[i][j] = Math.max(f[i + 1][j], f[i][j - 1]);
    if (s[i] == s[j]) {
        f[i][j] = Math.max(f[i][j], f[i + 1][j - 1] + 2);
    }
}

Integer[][] f;
public int longestPalindromeSubseq(String ss) {
    if (ss == null || ss.length() == 0) {
        return 0;
    }
    char[] s = ss.toCharArray();
    int n = s.length;
    f = new Integer[n][n];
    Compute(0, n - 1);
    return f[0][n - 1];
}
```

* [Example 2: Coins in a line III](https://www.lintcode.com/problem/coins-in-a-line-iii/)<br>
//State: dp[i][j] represent the maximum sum difference between current player with the other<br>
//State transfer: dp[i][j] = max(m - dp[i + 1][j], m - dp[i][j - 1])<br>
//dp[i][j] = arr[i] | i == j<br>
```Java
public boolean coinsInALineIII(int[] A) {
    if (A.length == 0) {
        return true;
    }
    int n = A.length;
    int[][] f = new int[n][n];
    for (int i = 0; i < n; i++) {
        f[i][i] = A[i];
    }
    for (int len = 2; len <= n; len++) {
        for (int i = 0; i <= n - len; i++) {
            int j = i + len - 1;
            f[i][j] = Math.max(A[i] - f[i + 1][j], A[j] - f[i][j - 1]);
        }
    }
    return f[0][n - 1] >= 0;
}
```

* [Example 3: Scramble String](https://www.lintcode.com/problem/scramble-string/description)<br>
//State: dp[i][j][k] represent whether it is convertable from string1[i to i + k] to string2[j to j + l]<br>
//State transfer: dp[i][j][k] = forAll 0 < w < k ((f[i][j][w] && f[i + w][j + w][k - w])) || ((f[i][k - w + j][w] && f[i + w][j][k - w]))<br>
//dp[i][j][1] = s1[i] == s2[j] ? true : false<br>
```Java
public boolean isScramble(String ss1, String ss2) {
    char[] s1 = ss1.toCharArray();
    char[] s2 = ss2.toCharArray();
    int n = s1.length;
    boolean[][][] f = new boolean[n][n][n + 1];
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            f[i][j][1] = s1[i] == s2[j] ? true : false;
        }
    }
    for (int k = 2; k <= n; k++) {
        for (int i = 0; i <= n - k; i++) {
            for (int j = 0; j <= n - k; j++) {
                f[i][j][k] = false;
                for (int w = 1; w < k; w++) {
                    f[i][j][k] = f[i][j][k] || (f[i][j][w] && f[i + w][j + w][k - w]);
                    if (f[i][j][k]) {
                        break;
                    }
                    f[i][j][k] = f[i][j][k] || (f[i][k - w + j][w] && f[i + w][j][k - w]);
                    if (f[i][j][k]) {
                        break;
                    }   
                }
            }
        }
    }
    return f[0][0][n];
}
```
```Java
// 自顶向下动规。但是注意因为我不想操作两个串的i to j, k to h 我就用了substring 但这样做时map中的只能是两个string的连接，会造成大量时间消耗。 所以更好的方法是引入参数 i, j, k 表示从s1的i开始，s2的j开始长度为k的字符串能否互相转化，这样就可以利用数组进行记录，而不是直接用两个string用map记录。速度会加快很多。
Map<String, Boolean> map;
public boolean isScramble(String s1, String s2) {
    map = new HashMap<>();
    return dfs(s1, s2);
}

private boolean dfs(String s1, String s2) {
    if (map.containsKey(s1 + s2)) {
        return map.get(s1 + s2);
    }
    if (s1.equals(s2)) {
        return true;
    }

    boolean res = false;
    for (int i = 0; i < s1.length() - 1; i++) {
        String subS1left = s1.substring(0, i + 1);
        String subS1right = s1.substring(i + 1, s1.length());
        String subS2left1 = s2.substring(0, i + 1);
        String subS2right1 = s2.substring(i + 1, s1.length());
        String subS2left2 = s2.substring(s2.length() - (i + 1), s2.length());
        String subS2right2 = s2.substring(0, s2.length() - (i + 1));
        res = res || ((dfs(subS1left, subS2left1) && dfs(subS1right, subS2right1)) || (dfs(subS1left, subS2left2) && dfs(subS1right, subS2right2)));
        if (res) {
            break;
        }
    }
    map.put(s1 + s2, res);
    return res;
}
```

* [Example 4: Burst Balloons](https://www.lintcode.com/problem/burst-balloons/description)<br>
//State: dp[i][j] represent the maximum amount can be obtained from i + 1 to j - 1 balloons<br>
//State transfer: dp[i][j] = forAll k | 0 <= k <= j max(dp[i][k] + dp[k][j] + price[k]);<br>
//dp[0][1] = dp[1][2] = ... = dp[i][i + 1] = 0<br>
```Java
public int maxCoins(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int n = nums.length + 2;
    int[] A = new int[n];
    for (int i = 0; i < n; i++) {
        if (i == 0 || i == n - 1) {
            A[i] =  1;
        } else {
            A[i] = nums[i - 1];
        }
    }
    int[][] f = new int[n][n];
    for (int i = 0; i < n - 1; i++) {
        f[i][i + 1] = 0;
    }
    for (int len = 3; len <= n; len++) {
        for (int i = 0; i <= n - len; i++) {
            int j = i + len - 1;
            f[i][j] = 0;
            for (int k = i + 1; k < j; k++) {
                f[i][j] = Math.max(f[i][j], f[i][k] + f[k][j] + A[i] * A[k] * A[j]);
            }
        }
    }
    return f[0][n - 1];
}
```

#### DP course#6
### 双序列型动态规划
* 有两个序列/字符串，需要进行一些操作
* 每个序列本身是一维的
* 可以转化为二维动态规划

* [Example 1: Longest Common Subsequence](https://www.lintcode.com/problem/longest-common-subsequence/description)<br>
//State: dp[i][j] represent the longest length from previous i from str1 and previous j from str2.<br>
//State transfer: dp[i][j] = max(dp[i][j - 1], dp[i - 1][j], dp[i - 1][j - 1] + 1 | str1[i - 1] == str2[j - 1])<br>
// dp[0][n] = 0, dp[m][0] = 0<br>
//注意这题可以输出最终选择的字串结果，倒序打印<br>
```Java
public int longestCommonSubsequence(String A, String B) {
    if (A == null || B == null) {
        return 0;
    }
    if (A.length() == 0 || B.length() == 0) {
        return 0;
    }
    char[] s1 = A.toCharArray();
    char[] s2 = B.toCharArray();
    int m = s1.length;
    int n = s2.length;
    int[][] f = new int[m + 1][n + 1];
    for (int i = 0; i <= m; i++) {
        for (int j = 0; j <= n; j++) {
            if (i == 0 || j == 0) {
                f[i][j] = 0;
                continue;
            }
            f[i][j] = Math.max(f[i][j - 1], f[i - 1][j]);
            if (s1[i - 1] == s2[j - 1]) {
                f[i][j] = Math.max(f[i][j], f[i - 1][j - 1] + 1);
            }
        }
    }
    return f[m][n];
}
```

* [Example 2: Interleaving String](https://www.lintcode.com/problem/interleaving-string/description)<br>
//State: dp[s][i][j] represent the previous s characters can be obtained from previous i characters from A and previous j characters from B.<br>
//Important observation: 自由度其实为2 because i + j = s. So the state can be represented by dp[i][j], which represent whether the previous i + j characters in s3 can be obtained from previous i characters from A and previous j characters from B. 以后设置状态的时候都可以考虑一下状态本身有没有关系，从而可以简化状态表示。<br>
//State transfer: dp[i][j] = dp[i - 1][j] if (s3[i + j - 1] come from A) or dp[i][j - 1] if (s3[i + j - 1] come from B)<br>
//dp[0][0] = true;<br>
```Java
public boolean isInterleave(String ss1, String ss2, String ss3) {
    char[] s1 = ss1.toCharArray();
    char[] s2 = ss2.toCharArray();
    char[] s3 = ss3.toCharArray();
    int m = s1.length;
    int n = s2.length;
    int x = s3.length;
    if (m + n != x) {
        return false;
    }
    boolean[][] f = new boolean[m + 1][n + 1];
    f[0][0] = true;
    for (int i = 0; i <= m; i++) {
        for (int j = 0; j <= n; j++) {
            f[i][j] = false;
            if (i > 0 && s3[i + j - 1] == s1[i - 1]) {
                f[i][j] = f[i][j] || f[i - 1][j];
            }
            if (j > 0 && s3[i + j - 1] == s2[j - 1]) {
                f[i][j] = f[i][j] || f[i][j - 1];
            }
        }
    }
    return f[m][n];
}
```

* [Example 3: Edit Distance](https://www.lintcode.com/problem/edit-distance/description)<br>
//State: dp[i][j] represent the minimum edit distance from previous i characters of A to previous j characters from B<br>
//State transfer: dp[i][j] = min(dp[i][j - 1] + 1, dp[i - 1][j] + 1, dp[i - 1][j - 1] + 1, dp[i - 1][j - 1] | str1[i - 1] == str2[j - 1]);<br>
//dp[0][j] = j, dp[i][0] = i<br>

```Java
public int minDistance(String word1, String word2) {
    char[] s1 = word1.toCharArray();
    char[] s2 = word2.toCharArray();
    int m = s1.length;
    int n = s2.length;
    int[][] f = new int[m + 1][n + 1];
    int i, j;
    for (i = 0; i <= m; i++) {
        for (j = 0; i <= n; j++) {
            if (i == 0) {
                f[i][j] = j;
                continue;
            }
            if (j == 0) {
                f[i][j] = i;
                continue;
            }
            f[i][j] = Integer.MAX_VALUE;
            f[i][j] = Math.min(Math.min(f[i][j - 1] + 1, f[i - 1][j] + 1), f[i - 1][j - 1] + 1);
            if (s1[i - 1] == s2[j - 1]) {
                f[i][j] = Math.min(f[i][j], f[i - 1][j - 1]);
            }
        }
    }
    return f[m][n];
}
```

* [Example 4: Distinct Subsequences](https://www.lintcode.com/problem/distinct-subsequences/description)<br>
//State: dp[i][j] represent how many times previous j characters in B occurs in previous i characters in A<br>
//State transfer: dp[i][j] = dp[i - 1][j - 1] | A[i - 1] == B[j - 1]  + dp[i - 1][j]<br>
//dp[0][0] = 1, dp[0][j] = 0, dp[i][0] = 1 (这里dp[i][0] = 1是从使递推方程成立的角度考虑的)<br>
```Java
public int numDistinct(String S, String T) {
    char[] s1 = S.toCharArray();
    char[] s2 = T.toCharArray();
    int m = s1.length;
    int n = s2.length;
    int[][] f = new int[m + 1][n + 1];
    int i, j
    for (i = 0; i <= m; i++) {
        for (j = 0; j <= n; j++) {
            if (j == 0) {
                f[i][j] = 1;
                continue;
            }
            if (i == 0) {
                f[i][j] = 0;
                continue;
            }
            f[i][j] = f[i - 1][j];
            if (s1[i - 1] == s2[j - 1]) {
                f[i][j] += dp[i - 1][j - 1];
            }
        }
    }
    return f[m][n];
}   
```

* [Example 5: Regular Expression Matching](https://www.lintcode.com/problem/regular-expression-matching/description)<br>
//State: dp[i][j] represent whether previous j characters in regular expression string can match previous i characters in orginal string.<br>
//State transfer: dp[i][j] =
dp[i - 1][j - 1] | s1[i - 1] == s2[j - 1] and s2[j - 1] != * <br>
dp[i - 1][j] | s2[j - 1] == * and s2[j - 2] == s1[i - 1] or s[j - 2] == '.' <br>
dp[i][j - 2] | s2[j - 1] == * <br>
//dp[0][0] = true; dp[i][0] = false;<br>
```Java
public boolean isMatch(String s, String p) {
    char[] s1 = s.toCharArray();
    char[] s2 = p.toCharArray();
    int m = s1.length;
    int n = s2.length;
    boolean[][] f = new boolean[m + 1][n + 1];
    int i, j;
    for (i = 0; i <= m; i++) {
        for (j = 0; j <= n; j++) {
            if (i == 0 && j == 0) {
                f[i][j] = true;
                continue;
            }
            if (j == 0) {
                f[i][j] = false;
                continue;
            }
            f[i][j] = false;

            if (s2[j - 1] != '*') {
                if (i > 0 && (s2[j - 1] == '.' || s2[j - 1] == s1[i - 1])) {
                    f[i][j] = f[i - 1][j - 1];
                }
            } else {
                if (j > 1) {
                    f[i][j] |= f[i][j - 2];
                    if (i > 0 && (s2[j - 2] == '.' || s2[j - 2] == s1[i - 1])) {
                        f[i][j] |= f[i - 1][j];
                    }
                }
            }
        }
    }
    return f[m][n];
}
```

* [Example 6: Wildcard Matching](https://www.lintcode.com/problem/wildcard-matching/description)<br>
//State: dp[i][j] represent whether previous j characters in wildcard string can match previous i characters in orginal string.<br>
//State transfer: dp[i][j] = <br>
dp[i - 1][j - 1] | s2[j - 1] != * && (s1[i - 1] == s2[j - 1] or s2[j - 1] == '?')<br>
dp[i - 1][j] || dp[i][j - 1]<br>
//dp[0][0] = true; dp[i][0] = false;<br>
```Java
public boolean isMatch(String s, String p) {
    char[] s1 = s.toCharArray();
    char[] s2 = p.toCharArray();
    int m = s1.length;
    int n = s2.length;
    boolean[][] f = new boolean[m + 1][n + 1];
    int i, j;
    for (i = 0; i <= m; i++) {
        for (j = 0; j <= n; j++) {
            if (i == 0 && j == 0) {
                f[i][j] = true;
                continue;
            }
            if (j == 0) {
                f[i][j] = false;
                continue;
            }
            if (s2[j - 1] != '*') {
                if (i > 0 && (s2[j - 1] == '?' || s2[j - 1] == s1[i - 1])) {
                    f[i][j] |= f[i - 1][j - 1];
                }
            } else {
                if (i > 0) {
                    f[i][j] |= (f[i - 1][j] || f[i][j - 1]);
                }
            }
        }
    }
    return f[m][n];
}
```

随便讲的例子，跟背包问题类似
* [Example 7: Ones and Zeroes](https://www.lintcode.com/problem/ones-and-zeroes/description)<br>
//State: dp[i][j][k] represent the maximum number of string can be formed by previous i string with j 0s and k 1s<br>
//State transfer: dp[i][j][k] = max(dp[i - 1][j][k], dp[i - 1][j - zeros[i - 1]][k - ones[i - 1]] + 1<br>
//dp[0][m][n] = 0<br>
```Java
public int findMaxForm(String[] strs, int m, int n) {
    int len = strs.length;
    int[][] counts = new int[len][2];
    for (int i = 0; i < len; i++) {
        String str = strs[i];
        char[] s = str.toCharArray();
        for (int j = 0; j < s.length; j++) {
            if (s[j] == '0') {
                counts[i][0]++;
            } else {
                counts[i][1]++;
            }
        }
    }
    int[][][] f = new int[len + 1][m + 1][n + 1];
    for (int i = 0; i <= len; i++) {
        for (int j = 0; j <= m; j++) {
            for (int k = 0; k <= n; k++) {
                if (i == 0 || j == 0 || k == 0) {
                    f[i][j][k] = 0;
                    continue;
                }
                f[i][j][k] = f[i - 1][j][k];
                if (j - counts[i - 1][0] >= 0 && k - counts[i - 1][1] >= 0) {
                    f[i][j][k] = Math.max(f[i][j][k], f[i - 1][j - counts[i - 1][0]][k - counts[i - 1][1]] + 1);
                }
            }
        }
    }
    return f[len][m][n];
}
```

#### DP course#7
* 最难的动态规划题。。
* 综合型动态规划
* 需要辅助数据结构/算法
* [Example 1: Minimum Adjustment Cost](https://www.lintcode.com/problem/minimum-adjustment-cost/description)<br>
//State: dp[i][j] represent the minimum adjustment cost of previous i element with B[i - 1] changed to j<br>
//State transfer: dp[i][j] = forAll j - target <= k <= j + target min(dp[i - 1][k] + abs(A[i - 1] - j))<br>
//dp[0][j] = 0<br>
```Java
public int MinAdjustmentCost(List<Integer> A, int target) {
    if (A == null || A.size() == 0) {
        return 0;
    }
    int n = A.size();
    int[][] f = new int[n + 1][101];
    int i, j, k;
    for (i = 0; i <= n; i++) {
        for (j = 1; j <= 100; j++) {
            if (i == 0) {
                f[i][j] = 0;
                continue;
            }
            f[i][j] = Integer.MAX_VALUE;
            int left = Math.max(1, j - target);
            int right = Math.min(100, j + target);
            for (k = left; k <= right; k++) {
                f[i][j] = Math.min(f[i][j], f[i - 1][k] + Math.abs(A.get(i - 1) - j));
            }
        }
    }
    int res = Integer.MAX_VALUE;
    for (j = 1; j <= 100; j++) {
        res = Math.min(res, f[n][j]);
    }
    return res;
}
```

* [Example 2: k Sum](https://www.lintcode.com/problem/k-sum/description)<br>
//State: dp[i][j][k] represent how many solutions are there for previous i elements with sum of j and size of k<br>
//State transfer: dp[i][j][k] = dp[i - 1][j][k] + dp[i - 1][j - num[i - 1]][k - 1]<br>
//dp[0][0][0] = 1<br>
```Java
public int kSum(int[] A, int K, int target) {
    int n = A.length;
    int[][][] f = new int[n + 1][target + 1][K + 1];
    int i, j, k;
    f[0][0][0] = 1;
    for (i = 1; i <= n; i++) {
        for (j = 0; j <= target; j++) {
            for (k = 0; k <= K; k++) {
                f[i][j][k] = f[i - 1][j][k];
                if (k > 0 && j - A[i - 1] >= 0) {
                   f[i][j][k] += f[i - 1][j - A[i - 1]][k - 1];
                }
            }
        }
    }
    return f[n][target][K];
}
```

* [Example 3: Longest Increasing Subsequence O(nlogn)](https://www.lintcode.com/problem/longest-increasing-subsequence/description)<br>
```Java
public int longestIncreasingSubsequence(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int n = nums.length;
    //index represent the length of subsequence;
    //value represent the the smallest value in nums having that length;
    int[] A = new int[n + 1];
    A[0] = Integer.MIN_VALUE;
    int longest = 0;
    for (int i = 0; i < n; i++) {
        int left = 0, right = longest + 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (A[mid] < nums[i]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        A[right] = nums[i];
        if (right > longest) {
            longest = right;
        }
    }
    return longest;
}
```

* [Example 4: K Edit Distance](https://www.lintcode.com/problem/k-edit-distance/)<br>
//State: dp["prefix"][j] represent the minimum edit distance from prefix of A to previous j characters from B<br>
//State transfer: dp["prefix"][j] = min(dp[i][j - 1] + 1, dp["prefix - 1"][j] + 1, dp["prefix - 1"][j - 1] + 1, dp["prefix - 1"][j - 1] | str1[i - 1] == str2[j - 1]);<br>
//dp[""][j] = j<br>
```Java
class TrieNode {
    TrieNode[] c;
    boolean isWord;
    String word;

    TrieNode() {
        c = new TrieNode[26];
        for (int i = 0; i < 26; i++) {
            c[i] = null;
        }
        isWord = false;
        word = null;
    }

    public static void insert(TrieNode root, String str) {
        TrieNode curr = root;
        char[] s = str.toCharArray();
        int n = s.length;
        for (int i = 0; i < n; i++) {
            int idx = s[i] - 'a';
            if (curr.c[idx] == null) {
                curr.c[idx] = new TrieNode();
            }
            curr = curr.c[idx];
        }
        curr.isWord = true;
        curr.word = str;
    }
}
public class kEditDistance {

    List<String> res;
    int m;
    int k;
    char[] targets;
    public List<String> kDistance(String[] words, String target, int k) {
        res = new ArrayList<>();
        if (words == null || words.length == 0) {
            return res;
        }
        this.k = k;
        int n = words.length;
        targets = target.toCharArray();
        TrieNode root = new TrieNode();
        for (int i = 0; i < n; i++) {
            TrieNode.insert(root, words[i]);
        }

        this.m = target.length();
        int[] f = new int[m + 1];
        for (int i = 0; i <= m; i++) {
            f[i] = i;
        }

        dfs(root, f);
        return res;
    }

    private void dfs(TrieNode curr, int[] f) {
        int[] currf = new int[m + 1];
        if (curr.isWord && f[m] <= k) {
            res.add(curr.word);
        }

        for (int i = 0; i < 26; i++) {
            if (curr.c[i] == null) {
                continue;
            }
            currf[0] = f[0] + 1;
            for (int j = 1; j <= m; j++) {
                currf[j] = Math.min(Math.min(currf[j - 1] + 1, f[j] + 1), currf[j - 1] + 1);
                if (i == targets[j - 1] - 'a') {
                    currf[j] = Math.min(currf[j], f[j - 1]);
                }
            }
            dfs(curr.c[i], currf);
        }
    }
}
```

* [Example 5: Frog Jump](https://www.lintcode.com/problem/frog-jump/description)<br>
//State: dp[i][j] represent whether the frog can reach stone idx i with last step of j
//State transfer: dp[i][j] = dp[idx[a[i] - (j - 1)]][j - 1] || dp[idx[a[i] - j]][j] || dp[idx[a[i] - (j + 1)]][j + 1];
//可以直接用a[i]做key
```Java
public boolean canCross(int[] stones) {
    int n = stones.length;
    Map<Integer, Set<Integer>> stepSet = new HashMap<>();
    for (int i = 0; i < n; i++) {
        stepSet.put(stones[i], new HashSet<>());
    }
    stepSet.get(stones[0]).add(0);
    for (int i = 0; i < n ; i++) {
        Set<Integer> currSet = new HashSet<>(stepSet.get(stones[i]));
        for (int step : currSet) {
            for (int delta = -1; delta <= 1; delta++) {
                int nextStep = step + delta;
                int nextStone = stones[i] + nextStep;
                if (stepSet.containsKey(nextStone)) {
                    stepSet.get(nextStone).add(nextStep);
                }
            }
        }
    }
    return !stepSet.get(stones[n - 1]).isEmpty();
}  
```
