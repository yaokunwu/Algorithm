# Recursion
## Introduction
将大问题转化为小问题，通过递归一次解决各个小问题<br>
## Examples
[Reverse String](https://leetcode.com/problems/reverse-string/) <br>
递归专题还是考虑递归的解法吧， 不要用swap<br>
思路: 错误思路：原问题为reverse这个string [0 : len]，子问题为reverse [0 : len - 1], 中间差的为当前层操作步骤<br>
正确思路： 原问题为reverse这个string [0 : len], 子问题为 reverse[1, len - 1]...  中间差的为当前层操作步骤，很明显，只需要swap 当前层的start 与end index<br>
```Java
class Solution {
    public void reverseString(char[] s) {
        helper(0, s.length - 1, s);
    }
    
    private void helper(int start, int end, char[] s) {
        if (start >= end) {
            return;
        }
        char temp = s[start];
        s[start] = s[end];
        s[end] = temp;
        helper(start + 1, end - 1, s);
    }
}
```

[Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/) <br>
思路：原问题为swap一个整的linkedlist 子问题为每一段linkedlist swap一次, 太强了，这也能自己做出来，还是按照当前层和后面层的练习来做。<br>
其实就是postOrder traversal<br>
```Java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode nextHead = head.next.next;
        ListNode next = head.next;
        next.next = head;
        head.next = swapPairs(nextHead);
        return next;
    }
}
```
改为更明显一点的postOrder<br>
```Java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode nextHead = head.next.next; // 下一层的入口，相当于level + 1;
        ListNode returned = swapPairs(nextHead);
        ListNode next = head.next;
        next.next = head;
        head.next = returned;
        return next;
    }
}
```
非递归的写法又忘了。。。到时候再看一下。。。<br>
[Unique Binary Search Trees II](https://leetcode.com/problems/unique-binary-search-trees-ii/) <br>
思路: naive的错误思路：从建立val1开始，要么给左边加新node要么右边，然后进入recursion就行了<br>
正确思路: 原问题为建立1到n的BST，子问题为建立更小的BST 比如 2 到 n-1， n - 5 到 n 等等， 所以需要引入的迭代参数为start 和 end. <br>
(这道题需要对递归理解很深, 可以暂时停一下)；<br>
在理解一下： 重复性子问题： 选中1 - n 中任意数字， 0 - 任意数字 和 任意数字到n的二叉树数量的乘积。<br>
```
class Solution {
    List<TreeNode> res;
    public List<TreeNode> generateTrees(int n) {
        if (n < 1) {
            return new ArrayList<TreeNode>();
        }
        return postOrder(1, n);
    }
    
    private List<TreeNode> postOrder(int start, int end) {
        List<TreeNode> currList = new ArrayList<>();
        if (start > end) {
            currList.add(null);
            return currList;
        }
        for (int i = start; i <= end; i++) {
            List<TreeNode> leftList = postOrder(start, i - 1);
            List<TreeNode> rightList = postOrder(i + 1, end);
            for (TreeNode leftRoot : leftList) {
                for (TreeNode rightRoot : rightList) {
                    TreeNode currNode = new TreeNode(i);
                    currNode.right = rightRoot;
                    currNode.left = leftRoot;
                    currList.add(currNode);
                }
            }
        }
        return currList;
    }
}
```
**注意，currList.add(null)是必要的因为for 循环 list如果为kong不会执行for循环中内容。 (null也算list中的元素）<br>


[Unique Binary Search Trees](https://leetcode.com/problems/unique-binary-search-trees/) <br>
这两道题都很有意思，都是分治。
```Java
class Solution {
    Integer[][] dp;
    public int numTrees(int n) {
        dp = new Integer[n + 1][n + 1];
        return dfs(1, n);
    }
    
    private int dfs(int start, int end) {
        if (start >= end) {
            return 1;
        }
        if (dp[start][end] != null) {
            return dp[start][end];
        }
        int count = 0;
        for (int i = start; i <= end; i++) {
            int left = dfs(start, i - 1);
            int right = dfs(i + 1, end);
            count += left * right;
        }
        dp[start][end] = count;
        return dp[start][end];
    }
}
```

[Fibonacci Number](https://leetcode.com/problems/fibonacci-number/) <br>
思路： 动规的单参数<br>
top - down
```Java
class Solution {
    // top down
    Integer[] dp;
    public int fib(int N) {
        dp = new Integer[N + 1];
        return recur(N);
    }
    
    private int recur(int N) {
        if (N <= 1) {
            return N;
        }
        if (dp[N] != null) {
            return dp[N];
        }
        int left = recur(N - 1);
        int right = recur(N - 2);
        dp[N] = left + right;
        return dp[N];
    }
}
```
Bottom - up <br>
两个值可解 <br>
```Java
class Solution {
    public int fib(int N) {
        if (N <= 1) {
            return N;
        }
        int first = 0;
        int second = 1;
        int temp = 0;
        for (int i = 2; i <= N; i++) {
            temp = first + second;
            first = second;
            second = temp;
        }
        return temp;
    }
}

常规bottom up <br>
```
class Solution {
    public int fib(int N) {
        int[] dp = new int[N + 1];
        dp[0] = 0;
        dp[1] = 1;
        for (int i = 2; i <= N; i++) {
           dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[N];
    }
}
```
