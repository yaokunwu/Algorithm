# Grokking Dynamic Programming Patterns for Coding Interviews

结果树（state space tree） 和参数树 （参数树可以用来判断有无重复子问题）

注意这两者之间的区别(提供一个错误解答）
这个解加了dp就是错的。。 不加就对了 (最好提前判断，不能让remain为负)
```Java
class RodCutting {

  int len;
  int[] lengths;
  int[] prices;
  Integer[][] dp;
  public int solveRodCutting(int[] lengths, int[] prices, int n) {
    this.len = lengths.length;
    this.lengths = lengths;
    this.prices = prices; 
    dp = new Integer[len][n + 1];
    return dfs(0, n, 0);
  }

  private int dfs(int level, int remain, int profit) {
    if (remain < 0 || level == len) {  // 
      return 0;
    }
    if (dp[level][remain] != null) {
      return dp[level][remain];
    }
    int left = dfs(level, remain - lengths[level], profit + prices[level]);
    int right = dfs(level + 1, remain, profit);
    dp[level][remain] = Math.max(profit, Math.max(left, right));
    return dp[level][remain];
  }

  public static void main(String[] args) {
    RodCutting rc = new RodCutting();
    int[] lengths = {1, 2};
    int[] prices = {2, 6};
    int maxProfit = rc.solveRodCutting(lengths, prices, 2);
    System.out.println(maxProfit);
  }
}

```
[Rod Cutting](educative.io/courses/grokking-dynamic-programming-patterns-for-coding-interviews/RM5E56PGnVY) <br>
```Java
class RodCutting {

  int len;
  int[] lengths;
  int[] prices;
  Integer[][] dp;
  public int solveRodCutting(int[] lengths, int[] prices, int n) {
    this.len = lengths.length;
    this.lengths = lengths;
    this.prices = prices; 
    dp = new Integer[len][n + 1];
    return dfs(0, n);
  }

  private int dfs(int level, int remain) {
    if (remain <= 0 || level == len) {
      return 0;
    }
    if (dp[level][remain] != null) {
      return dp[level][remain];
    }
    int left = 0;
    if (lengths[level] <= remain) {
      left = prices[level] + dfs(level, remain - lengths[level]); // prices写到外面的前提是你能选这个
    }
    int right = dfs(level + 1, remain);
    dp[level][remain] = Math.max(left, right);
    return dp[level][remain];
  }

  public static void main(String[] args) {
    RodCutting rc = new RodCutting();
    int[] lengths = {1, 2, 3, 4, 5};
    int[] prices = {2, 6, 7, 10, 13};
    int maxProfit = rc.solveRodCutting(lengths, prices, 5);
    System.out.println(maxProfit);
  }
}
```

[Unbounded Knapsack](https://www.educative.io/courses/grokking-dynamic-programming-patterns-for-coding-interviews/qV6RXWME4D3) <br>
注意这两种逻辑，算法是不同的
第一种逻辑： 东西随便选，选择能使profit最大的item。
```Java
class Knapsack {
  int[] profits;
  int[] weights;
  public int solveKnapsack(int[] profits, int[] weights, int capacity) {
    this.profits = profits;
    this.weights = weights;
    //algorithm 1 : only use capacity as the parameter
    return dfs(capacity, 0);
  }

  private int dfs(int capacity, int profit) {
    if (capacity == 0) {
      return 0;
    }

    int maxProfit = 0;
    for (int i = 0; i < profits.length; i++) {
      if (weights[i] <= capacity) {
        maxProfit = Math.max(maxProfit, dfs(capacity - weights[i], profit + profits[i]));
      }
    }

    return Math.max(maxProfit, profit);

  }

  public static void main(String[] args) {
    Knapsack ks = new Knapsack();
    int[] profits = {  50, 60 };
    int[] weights = {  3, 5 };
    int maxProfit = ks.solveKnapsack(profits, weights, 7);
    System.out.println(maxProfit);
  }
}
```
另一种： 选择第一个item的最大profit 和 不选它的最大profit的最大值。
```Java
class Knapsack {
  int[] profits;
  int[] weights;
  public int solveKnapsack(int[] profits, int[] weights, int capacity) {
    this.profits = profits;
    this.weights = weights;
    //algorithm 1 : only use capacity as the paramete
    return dfs(0, capacity, 0);
  }

  private int dfs(int level, int capacity, int profit) {
    if (capacity == 0 || level == profits.length) {
      return 0;
    }

    int profit1 = 0;
    if (weights[level] <= capacity) {
      profit1 = dfs(level, capacity - weights[level], profit + profits[level]);
    }
    int profit2 = dfs(level + 1, capacity, profit);

    return Math.max(profit, Math.max(profit1, profit2));

  }

  public static void main(String[] args) {
    Knapsack ks = new Knapsack();
    int[] profits = {  50, 60 };
    int[] weights = {  3, 5 };
    int maxProfit = ks.solveKnapsack(profits, weights, 7);
    System.out.println(maxProfit);
  }
}
```
这种还有个变形。。
```Java
class Knapsack {
  int[] profits;
  int[] weights;
  public int solveKnapsack(int[] profits, int[] weights, int capacity) {
    this.profits = profits;
    this.weights = weights;
    //algorithm 1 : only use capacity as the paramete
    return dfs(0, capacity);
  }

  private int dfs(int level, int capacity) {
    if (capacity == 0 || level == profits.length) {
      return 0;
    }

    int profit1 = 0;
    if (weights[level] <= capacity) {
      profit1 = profits[level] + dfs(level, capacity - weights[level]);
    }
    int profit2 = dfs(level + 1, capacity);

    return Math.max(profit1, profit2);

  }

  public static void main(String[] args) {
    Knapsack ks = new Knapsack();
    int[] profits = {  50, 60 };
    int[] weights = {  3, 5 };
    int maxProfit = ks.solveKnapsack(profits, weights, 10);
    System.out.println(maxProfit);
  }
}
```
int[] nums
public int CountWays(int n) {
  this.nums = {1,3,4};
  dfs(0, n);
}

private int dfs(int level, int remain) {
  if (remain == 0) {
    return 1;
  } 
  if (level == 3) {
    return 0;
  }
  
  int left = 0;
  if (nums[level] <= remain) {
    left = dfs(level, remain);
  }
  int right = dfs(level + 1, remain);
  return left + right;
}

[Number factors](https://www.educative.io/courses/grokking-dynamic-programming-patterns-for-coding-interviews/NE52PnMY376) <br>
注意比较这个题和coin change的题，你就会发现为什么之前一个用level参数，一个用for循环。
（**和排列顺序有关系**） 这道题需要对比coin change
```Java
class ExpressNumber {
    int[] nums;
    public int CountWays(int n) {
       this.nums = new int[] {1,3,4};
       return dfs(n);
    }

    private int dfs(int remain) {
    if (remain == 0) {
      return 1;
    } 
    int count = 0;
    for (int i = 0; i < nums.length; i++) {
      if (nums[i] <= remain) {
        count += dfs(remain - nums[i]);
      }
    }
    return count;
  }
}
```

[Minimum jumps to reach the end](https://www.educative.io/courses/grokking-dynamic-programming-patterns-for-coding-interviews/7nAKN0Qz67r) <br>
思路： 在每一层都会有相应的可以选择的状态，只需要对每个状态process


[Combination Sum](https://leetcode.com/problems/combination-sum/) <br>
这个题我就借用了unbounded 背包中的思路，没有用for循环，而是一种不选回头路的回溯
