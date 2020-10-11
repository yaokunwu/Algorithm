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











