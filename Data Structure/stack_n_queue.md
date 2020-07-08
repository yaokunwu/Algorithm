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










