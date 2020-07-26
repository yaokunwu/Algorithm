# Stack and Queue
## Short Description <br>
The characteristic of stack is Last In, First Out (LIFO)
![image](https://camo.githubusercontent.com/d7eecb884f217ed62e817b91c6cb9014aff9f863/68747470733a2f2f696d672e667569626f6f6d2e636f6d2f696d672f737461636b2e706e67)
根据这个特点可以临时保存一些数据，之后用到依次再弹出来，常用于 DFS 深度搜索<br>

队列一般常用于 BFS 广度搜索，类似一层一层的搜索

## Stack
[Min Stack](https://leetcode.com/problems/min-stack) <br>

思路： 用俩个stack来实现，其中一个是正常stack，另一个专门为了找min而用,始终保证最小值在顶部 <br>

```Java
class MinStack {
    private Deque<Integer> stack;
    private Deque<Integer> minStack;
    private int min;
    /** initialize your data structure here. */
    public MinStack() {
        stack = new LinkedList<>();
        minStack = new LinkedList<>();
        min = Integer.MAX_VALUE;   
    }
    
    public void push(int x) {
        stack.addFirst(x);
        min = Math.min(min, x);
        minStack.addFirst(min);
    }
    
    public void pop() {
        stack.removeFirst();
        int mini = minStack.removeFirst();
        if (!minStack.isEmpty()) {
            min = minStack.peek();
        } else {
            min = Integer.MAX_VALUE;
        }
   
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
}
```

[Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation) <br>

思路：逆波兰表达式，遇数字进栈，遇字符出栈两次做运算并进栈 <br>
这题太傻逼了。。 记着一定用.equals 在判定string相等时，有的傻逼编译器不会通过 ==
```Java
class Solution {
    public int evalRPN(String[] tokens) {
        Deque<Integer> stack = new LinkedList<>();
        int first, second, res;
        for (String str : tokens) {
            if (str.equals("+")) {
                first= stack.removeFirst();
                second = stack.removeFirst();
                res = second + first;
                stack.addFirst(res);
            } else if (str.equals("-")) {
                first= stack.removeFirst();
                second = stack.removeFirst();
                res = second - first;
                stack.addFirst(res);
            } else if (str.equals("*")) {
                first= stack.removeFirst();
                second = stack.removeFirst();
                res = second * first;
                stack.addFirst(res);
            } else if (str.equals("/")) {
                first= stack.removeFirst();
                second = stack.removeFirst();
                res = second / first;
                stack.addFirst(res);
            } else {
                stack.addFirst(Integer.parseInt(str));
            }
        }
        return stack.peek();
    }
}
```
**注意事项**
* string的比较要用.equals, 记住常用表达式： 比如Integer.parseInt(), Integer.valueOf(), Character.isDigit()
* Integer.parseInt()里面是string 不能是character
* array 的excption条件有 1. null 2. array.length == 0 这两个条件要同时判断

[Decode String](https://leetcode.com/problems/decode-string) <br>
思路: 两个栈， 一个存数字，一个存字符串，栈用来保存当前结果
```Java
class Solution {
    public String decodeString(String s) {
        if (s == null || s.length() <= 1) {
            return s;
        }
        Deque<Integer> stackMulti = new LinkedList<>();
        Deque<StringBuilder> stackStr = new LinkedList<>();
        int multi = 0;
        StringBuilder res = new StringBuilder();
        for (char chr : s.toCharArray()) {
            if (chr >= '0' && chr <= '9') {
                multi = multi * 10 + Integer.parseInt(String.valueOf(chr));
            } else if (chr == '[') {
                stackMulti.addFirst(multi);
                stackStr.addFirst(res);
                res = new StringBuilder();
                multi = 0;
            } else if (chr == ']') {
                int currMulti = stackMulti.removeFirst();
                StringBuilder currBuilder = stackStr.removeFirst();
                for (int i = 0; i < currMulti; i++) {
                    currBuilder.append(res);
                }
                res = currBuilder;
            } else {
                res.append(chr);
            }
        }
        return res.toString();
    }
}
```
**注意事项** <br>
* 在计算倍数时不要忘了之前的倍数位数
* 把char转化为string用string.valueOf()比较好

[Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal) <br>
思路： 就是简单的非递归中序遍历， 用栈解
```Java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        if (root == null) {
            return new ArrayList<Integer>();
        }
        List<Integer> res = new ArrayList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode curr = root;
        while (curr != null || !stack.isEmpty()) {
            while (curr != null) {
                stack.addFirst(curr);
                curr = curr.left;
            }
            curr = stack.poll();
            res.add(curr.val);
            curr = curr.right;
        }
        return res;
    }
}
```

[Clone Graph](https://leetcode.com/problems/clone-graph) <br>
思路：**bfs**非递归
```Java
class Solution {
    public Node cloneGraph(Node node) {
        if (node == null) {
            return node;
        }
        Map<Node, Node> checkMap = new HashMap<>();
        Deque<Node> stack = new LinkedList<>();
        stack.push(node);
        checkMap.put(node, new Node(node.val));
        while (!stack.isEmpty()) {
            Node currNode = stack.pop();
            for (Node neighbor : currNode.neighbors) {
                if (!checkMap.containsKey(neighbor)) {
                    checkMap.put(neighbor, new Node(neighbor.val));
                    stack.push(neighbor);
                }
            }
            Node currCopy = checkMap.get(currNode);
            for (Node neighbor : currNode.neighbors) {
                currCopy.neighbors.add(checkMap.get(neighbor));
            }
        }
        return checkMap.get(node);
    }
}
```
dfs递归解 <br>
```
Java
class Solution {
    Map<Node, Node> checkMap = new HashMap<>();
    public Node cloneGraph(Node node) {
        //preorder
        if (node == null) {
            return null;
        }
        if (checkMap.containsKey(node)) {
            return checkMap.get(node);
        }
        checkMap.put(node, new Node(node.val));
        
        Node currCopy = checkMap.get(node);
        for (Node neighbor : node.neighbors) {
            currCopy.neighbors.add(cloneGraph(neighbor));
        }
        return checkMap.get(node);
    }
}
```
**暂时没想到如何用stack来转换为dfs非递归解**

[Number of Islands](https://leetcode.com/problems/number-of-island) <br>
思路： 深度优先遍历， 遇到1的使它变为0, 计算搜索次数， 就是岛屿数量
```Java
class Solution {
    private void dfs(char[][] grid, int row, int col) {
        int nRows = grid.length;
        int nCols = grid[0].length;
        if (row < 0 || col < 0 || row >= nRows || col >= nCols || grid[row][col] == '0') {
            return;
        }
        grid[row][col] = '0';
        dfs(grid, row - 1, col);
        dfs(grid, row + 1, col);
        dfs(grid, row, col + 1);
        dfs(grid, row, col - 1);
    }
    
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) {
            return 0;
        }
        int nRows = grid.length;
        int nCols = grid[0].length;
        int nIsland = 0;
        for (int i = 0; i < nRows; i++) {
            for (int j = 0; j < nCols; j++) {
                if (grid[i][j] == '1') {
                    nIsland += 1;
                    dfs(grid, i, j);
                }
            }
        }
        return nIsland;
    }
}
```
BFS解，每扫描一个陆地node，先判断其上下左右的node, 所以是BFS <br>
```Java
class Solution {  
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) {
            return 0;
        }
        int nRows = grid.length;
        int nCols = grid[0].length;
        int nIsland = 0;

        for (int i = 0; i < nRows; i++) {
            for (int j = 0; j < nCols; j++) {
                
                if (grid[i][j] == '1') {
                    grid[i][j] = '0';
                    nIsland += 1;
                    Queue<Integer> queue = new LinkedList<>();
                    int id = i * nCols + j;
                    queue.offer(id);
                    while (!queue.isEmpty()) {
                        int id = queue.poll();
                        int c = id % nCols;
                        int r = id / nCols;
                        if (r - 1 >= 0 && grid[r - 1][c] == '1') {
                            grid[r - 1][c] = '0';
                            queue.offer((r - 1) * nCols + c);
                        }
                        if (r + 1 < nRows && grid[r + 1][c] == '1') {
                            grid[r + 1][c] = '0';
                            queue.offer((r + 1) * nCols + c);
                        }
                        if (c - 1 >= 0 && grid[r][c - 1] == '1') {
                            grid[r][c - 1] = '0';
                            queue.offer(r * nCols + c - 1);
                        }
                        if (c + 1 < nCols && grid[r][c + 1] == '1') {
                            grid[r][c + 1] = '0';
                            queue.offer(r * nCols + c + 1);
                        }
                    }
                }
            }
        }
        return nIsland;
    }
}
```
**注意网格DFS与二叉树DFS的区别， 二叉树只需要node作为起始点， 而网格需要grid和当前row， col， 三个变量作为起始点**
[网格DFS模型详解](https://leetcode-cn.com/problems/number-of-islands/solution/dao-yu-lei-wen-ti-de-tong-yong-jie-fa-dfs-bian-li-) <br>
**后面再看看dfs， bfs**

[Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram) <br>
思路: 从暴力解法到运用单调栈 (和哨兵节点)。 单调栈为后继元素单调递增或递减。
```Java
class Solution {
    public int largestRectangleArea(int[] heights) {
        if (heights == null || heights.length == 0) {
            return 0;
        }
        
        int maxArea = 0;
        
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(0);
        for (int i = 1; i < heights.length; i++) {
            while (!stack.isEmpty() && heights[i] < heights[stack.peek()]) {
                int currHeight = heights[stack.pop()];
                int currWidth = stack.isEmpty()? i : i - stack.peek() - 1;
                maxArea = Math.max(maxArea, currWidth * currHeight);
            }
            stack.push(i);
        }
        while (!stack.isEmpty()) {
            int currHeight = heights[stack.pop()];
            int currWidth = stack.isEmpty()? heights.length : heights.length - stack.peek() - 1;
            maxArea = Math.max(maxArea, currWidth * currHeight);
        }
        return maxArea;
    }
}
```
**注意**
* System.arraycopy(sourceArray, sourceStart, targetArray, targetStart, copyLength);

## Queue
常用于BFS宽度优先搜索<br>

[Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks) <br>
思路1：要两个栈，其中一个用来反转顺序
```Java
class MyQueue {
    private Deque<Integer> in;
    private Deque<Integer> revert;
    /** Initialize your data structure here. */
    public MyQueue() {
        in = new LinkedList<>();
        revert = new LinkedList<>();
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        while (!in.isEmpty()) {
            revert.push(in.pop());
        }
        in.push(x);
        while (!revert.isEmpty()) {
            in.push(revert.pop());
        }
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        return in.pop();
    }
    
    /** Get the front element. */
    public int peek() {
        return in.peek();
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return in.isEmpty();
    }
}
```
思路2： 两个栈，一个负责输入， 一个负责输出
```Java
class MyQueue {
    private int front; // peek时不需要执行倒转stack的操作；
    // 一栈进，一栈出
    private Deque<Integer> in;
    private Deque<Integer> out;
    /** Initialize your data structure here. */
    public MyQueue() {
        in = new LinkedList<>();
        out = new LinkedList<>();
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        if (in.isEmpty()) {
            front = x;
        }
        in.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if (out.isEmpty()) {
            while (!in.isEmpty()) {
                out.push(in.pop());
            }
        }
        return out.pop();
    }
    
    /** Get the front element. */
    public int peek() {
        if (out.isEmpty()) {
            return front;
        }
        return out.peek();
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return out.isEmpty() && in.isEmpty();
    }
}
```

[Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal) <br>
思路： 常规BFS解法，用queue实现
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) {
            return new ArrayList<>();
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            List<Integer> currLevel = new ArrayList<>();
            for (int i = 0; i < levelSize; i++) {
                TreeNode currNode = queue.poll();
                currLevel.add(currNode.val);
                if (currNode.left != null) {
                    queue.offer(currNode.left);
                }
                if (currNode.right != null) {
                    queue.offer(currNode.right);
                }
            }
            res.add(currLevel);
        }
        return res;
    }
}
```

[01 Matrix](https://leetcode.com/problems/01-matrix) <br>
思路：用BFS做，每次判断当前node是否是0，然后进入bfs，求解最短距离 <br>
自己的解： 不是很优雅。。 但是是自己想的，用queue进行BFS， 注意 row = id / nCols; col = id % nCols 这种与网格相关的技巧。
```Java
class Solution {
    public int[][] updateMatrix(int[][] matrix) {
        int nRows = matrix.length;
        int nCols = matrix[0].length;
        int[][] distance = new int[nRows][nCols];
        
        for (int i = 0; i < nRows; i++) {
            for (int j = 0; j < nCols; j++) {
                int dist = 0;
                if (matrix[i][j] != 0) {
                    Queue<Integer> queue = new LinkedList<>();
                    int id = i * nCols + j;
                    queue.add(id);
                    while (!queue.isEmpty()) {
                        int levelSize = queue.size();
                        
                        for (int k = 0; k < levelSize; k++) {
                            id = queue.poll();
                            int r = id / nCols;
                            int c = id % nCols;
                            if (matrix[r][c] == 0) {
                                queue.clear();
                                // dist -= 1;
                                break;
                            }
                            if (r - 1 >= 0) {
                                queue.add((r - 1) * nCols + c);
                            }
                            if (r + 1 < nRows) {
                                queue.add((r + 1) * nCols + c);
                            }
                            if (c - 1 >= 0) {
                                queue.add(r * nCols + c - 1);
                            }
                            if (c + 1 < nCols) {
                                queue.add(r * nCols + c + 1);
                            }
                        }
                        if (!queue.isEmpty()) {
                            dist += 1;
                        }
                    }
                }
            distance[i][j] = dist;
            }
        }
        return distance;
    }
}
```
思路2： 优化后的BFS， 别人的code <br>
思路3： 动态规划 <br>

## Summary
* 熟悉栈的使用场景
    * 后进先出，保存临时值
    * 利用栈进行DFS深度搜索
* 熟悉队列的使用场景
    * 利用队列BFS广度搜索
    
## TO-DO
[Number of Islands](https://leetcode.com/problems/number-of-island) <br> 类似题型练习，掌握网格基本处理方法 <br>
[01 Matrix](https://leetcode.com/problems/01-matrix) <br> 查看BFS优化解以及动态规划解 <br>
