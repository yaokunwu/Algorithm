废话不多说，直接上题


[剑指 Offer 04. 二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/) <br>
思路： 分治. 注意**二维矩阵一定要把图画出来看边界条件，不要想当然**, 一定要理清搜索空间，base case有哪些的问题
```Java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }
        return dfs(matrix, target, 0, matrix.length - 1, 0, matrix[0].length - 1);
    }
    
    private boolean dfs(int[][] matrix, int target, int top, int down, int left, int right) {
        if (top > down || left > right) {
            return false;
        } else if (target > matrix[down][right] || target < matrix[top][left]) {
            return false;
        }
        
        int middle = left + (right - left) / 2;
        int row = top;
        for (; row <= down; row++) {
            if (matrix[row][middle] == target) {
                return true;
            } else if (matrix[row][middle] > target) {
                break;
            }
        }
        return dfs(matrix, target, row, down, left, middle - 1) || dfs(matrix, target, top, row - 1, middle + 1, right);
    }
}
```

[剑指 Offer 07. 重建二叉树](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/) <br>
这题不熟练到爆炸


[剑指 Offer 12. 矩阵中的路径](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/) <br>
思路: 好经典的一道dfs 回溯(所以以后需要判断是否需要回溯)

[剑指 Offer 14- I. 剪绳子](https://leetcode-cn.com/problems/jian-sheng-zi-lcof/) <br>
动规，不太好想。这道题dp可以一个参数，为啥dynamic pattern里面那个unbounded knapsack不能用一个参数。
//判断重复子问题倒是很好判断。

** 注意 == 的运算高于位运算符， if (n & 1 == 1) {} 要写成 if ((n & 1) == 1) {}


[剑指 Offer 26. 树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/) <br>
思路: 刚开始不会做，但是看了答案以后发现了和常规dfs的共同点。就是遍历+搜索。 和矩阵中找匹配字符串其实是同一种思想。
在这讲一下共通点：
1. char矩阵中匹配字符串时，首先需要遍历char矩阵，并对以每一个char为起的位置来进行对字符串的匹配。
类比到这题就是 首先需要遍历整个二叉树， 并对以每个node为起点的位置来进行对子树的匹配。
2.不同点在于， 遍历char矩阵只需要for循环即可，而遍历二叉树需要recursion， 所以才会出现双递归。
3. 遍历顺序和结果无关，dfs，bfs都可以，并且，树没有相同指向问题，不需要标记矩阵。
```Java
class Solution {
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        if (A == null || B == null) {  // 相当于for循环结束了都没找到
            return false;
        }

        if (dfs(A, B)) {  // 相当于遍历到当前节点，需要检查是否从该节点出发可以匹配到。
            return true;
        }

        return isSubStructure(A.left, B) || isSubStructure(A.right, B); // 当前的没找到，那么继续遍历下一个节点，相当于for循环到下一个char
    }

    private boolean dfs(TreeNode A, TreeNode B) {
        //检查从当前节点开始是否匹配

        // 3个边界检查，相当于检查矩阵的搜索范围是否超标。
        if (A == null && B == null) {
            return true;
        }
        if (B == null) {
            return true;
        }
        if (A == null) {
            return false;
        }
        // 数字不相等，相当于字符串不相等。
        if (A.val != B.val) {
            return false;
        }
        // 相等了，再进行下一个字符串的判断。
        return dfs(A.left, B.left) && dfs(A.right, B.right);
    }
}
```

[剑指 Offer 27. 二叉树的镜像](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/) <br>
这题我都不会， 我感觉我完了。。。 继续练，我没想到是直接用原来的树，我还在想怎么新建树
注意虽然可以先交换两个节点，但是其实如果在真实问题上必须先考虑加入stack的先后顺序问题。
```Java
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if(root == null) return null;
        Stack<TreeNode> stack = new Stack<>() {{ add(root); }};
        while(!stack.isEmpty()) {
            TreeNode node = stack.pop();
            if(node.left != null) stack.add(node.left);
            if(node.right != null) stack.add(node.right);
            TreeNode tmp = node.left;
            node.left = node.right;
            node.right = tmp;
        }
        return root;
    }
}
```

[剑指 Offer 31. 栈的压入、弹出序列](https://leetcode-cn.com/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/) <br>
模拟法的使用, 官方的代码好简洁，我的代码好垃圾。。
```Java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Deque<Integer> stack = new LinkedList<>();
        int j = 0;
        for (int i = 0; i < pushed.length; i++) {
            if (pushed[i] != popped[j]) {
                stack.push(pushed[i]);
            } else {
                j++;
                while (!stack.isEmpty() && stack.peek() == popped[j]) {
                    stack.pop();
                    j++;
                }
            }
        }

        for (; j < popped.length; j++) {
            if (popped[j] != stack.pop()) {
                return false;
            }
        }
        return true;
    }
}
```
官方的供参考
```Java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Deque<Integer> stack = new LinkedList<>();
        int i = 0;
        for (int num : pushed) {
            stack.push(num);
            while (!stack.isEmpty() && popped[i] == stack.peek()) {
                stack.pop();
                i++;
            }
        }
        return stack.isEmpty();
    }
}
```

[剑指 Offer 33. 二叉搜索树的后序遍历序列](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/) <br>
思路： 其实这道题说post order的意思就是让你从后往前寻找根节点。 我没想到的是怎么对问题进行分解, 不过还是看了思路后自己写出来的。 官方的解答更巧妙
```Java
class Solution {
    public boolean verifyPostorder(int[] postorder) { 
        if (postorder == null || postorder.length == 0) {
            return true;
        }
        return verify(postorder, 0, postorder.length - 1, null, null);
    }

    private boolean verify(int[] postorder, int left, int right, Integer lowerBound, Integer upperBound) {
        if (left > right) {
            return true;
        }
        int currVal = postorder[right];
        if (lowerBound != null && currVal < lowerBound) {
            return false;
        }
        if (upperBound != null && currVal > upperBound) {
            return false;
        }
        int cut = right - 1;
        while (cut >= 0 && postorder[cut] > currVal) {
            cut--;
        }
        boolean leftTree = verify(postorder, left, cut, lowerBound, currVal);
        boolean rightTree = verify(postorder, cut + 1, right - 1, currVal, upperBound);
        return leftTree && rightTree;
    }
}
···


[剑指 Offer 34. 二叉树中和为某一值的路径](https://leetcode-cn.com/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/) <br>
思路都会，就是回溯，讲个细节问题, 先上错误代码： 可以得知，最好在backtracking做选择的时候，就把不满足条件的先排除了，不要等进下一层循环了再排除，有可能回溯的时候有问题。
```Java
class Solution {
    List<List<Integer>> res;
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        backtracking(root, new ArrayList<>(), sum);
        return res;
    }

    private void backtracking(TreeNode curr, List<Integer> single, int remain) {
        if (curr == null) {
            return;
        }
        single.add(curr.val);
        // 终止条件只用做终止条件。
        if (curr.left == null && curr.right == null) {
            if (remain == curr.val) {
                res.add(new ArrayList<>(single));
            }
            return;
        }
        ❌//回溯下一层之前先判断是否可以，提前剪枝。
        backtracking(curr.left, single, remain - curr.val);
        single.remove(single.size() - 1);
        
        backtracking(curr.right, single, remain - curr.val);
        single.remove(single.size() - 1);
        
    }
}
```
修改后,回溯的标准写法
```Java
class Solution {
    List<List<Integer>> res;
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        backtracking(root, new ArrayList<>(), sum);
        return res;
    }

    private void backtracking(TreeNode curr, List<Integer> single, int remain) {
        single.add(curr.val);
        if (curr.left == null && curr.right == null) {
            if (remain == curr.val) {
                res.add(new ArrayList<>(single));
            }
            return;
        }
        if (curr.left != null) {
            backtracking(curr.left, single, remain - curr.val);
            single.remove(single.size() - 1);
        }
        if (curr.right != null) {
            backtracking(curr.right, single, remain - curr.val);
            single.remove(single.size() - 1);
        }
    }
}
```
下一个版本，注意比较这两种写法。
```Java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    List<List<Integer>> res;
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        res = new ArrayList<>();
        backtracking(root, new ArrayList<>(), sum);
        return res;
    }

    private void backtracking(TreeNode curr, List<Integer> single, int remain) {
        if (curr == null) {
            return;
        }
        single.add(curr.val);
        if (curr.left == null && curr.right == null && remain == curr.val) {
            res.add(new ArrayList<>(single));
            //这里不能return，所以并不是回溯的标准写法。
        }
        backtracking(curr.left, single, remain - curr.val);
        backtracking(curr.right, single, remain - curr.val);
        single.remove(single.size() - 1);
    }
}
```

[剑指 Offer 35. 复杂链表的复制](https://leetcode-cn.com/problems/fu-za-lian-biao-de-fu-zhi-lcof/) <br>
思路：其实和图的拷贝题完全一样，需要记录访问过的节点避免死循环，用map记录，而如果这个题是二叉树的拷贝就不需要map了。。
```Java
class Solution {
    Map<Node, Node> map;
    public Node copyRandomList(Node head) {
        map = new HashMap<>();
        return dfs(head);
    }

    private Node dfs(Node currNode) {
        if (currNode == null) {
            return null;
        }
        if (map.containsKey(currNode)) {
            return map.get(currNode);
        }

        Node copyedNode = new Node(currNode.val);
        map.put(currNode, copyedNode);
        copyedNode.next = dfs(currNode.next);
        copyedNode.random = dfs(currNode.random);
        return copyedNode;
    }
}
```

[剑指 Offer 36. 二叉搜索树与双向链表](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/) <br>
这题有点难度，如果prev node当作遍历中参数的话会不容易做，最好定义全局变量
要注意的点：二叉树返回时的路径问题。
```Java
class Solution {
    Node head;
    Node tail;
    public Node treeToDoublyList(Node root) {
        if (root == null) {
            return null;
        }
        inOrder(root, null);
        head.left = tail;
        tail.right = head;
        return head;
    }

    private Node inOrder(Node curr, Node prev) {
        if (curr == null) {
            return null;
        }
        if (curr.left != null) {
            prev = inOrder(curr.left, prev);
        }
        tail = curr;
        if (head == null) {
            head = curr;
        }
        curr.left = prev;
        if (prev != null) {
            prev.right = curr;
        }
        prev = inOrder(curr.right, curr);
        return prev == null ? curr : prev;
    }
}
```

注意哈希表的一些api使用
map.entrySet();
Map.Entry<> 
entry.getKey();
entry.getValue();

[剑指 Offer 45. 把数组排成最小的数](https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/) <br>
很经典的一道考察对排序算法理解的应用题。<br>
注意对arrays.copyOfRange()的掌握。
String a.compareTo()的掌握
两个int类的string可以通过直接compareTo判断

[剑指 Offer 46. 把数字翻译成字符串](https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/) <br>
动规，头一次见到还是不知道怎么下手。

[剑指 Offer 56 - II. 数组中数字出现的次数 II](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/) <br>
```Java
class Solution {
    public int singleNumber(int[] nums) {
        int[] count = getCount(nums);
        count = modCount(count);
        int num = restore(count);
        return num;
    }

    private int[] getCount(int[] nums) {
        int[] count = new int[32];
        for (int i = 0; i < nums.length; i++) {
            for (int j = 31; j >= 0; j--) {
                count[j] += nums[i] & 1;
                nums[i] = nums[i] >>> 1;
            }
        }
        return count;
    }

    private int[] modCount(int[] count) {
        for (int i = 0; i < count.length; i++) {
            count[i] = count[i] % 3;
        }
        return count;
    }

    private int restore(int[] count) {
        int res = 0;
        for (int i = 0; i < count.length; i++) {
            res = res << 1;
            res = res | count[i];
        }
        return res;
    }
}
```
注意一个基础操作： 给定32位的位表示，返回对应的整数。
```Java
private int restore(int[] count) {
    int res = 0;
    for (int i = 0; i < count.length; i++) {
        res = res << 1;
        res = res | count[i];
    }
    return res;
}
```

[剑指 Offer 59 - I. 滑动窗口的最大值](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/) <br>
单调队列可还行
```Java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int start = 0, write = 0;
        int[] res = new int[nums.length - k + 1];
        Deque<Integer> deque = new LinkedList<>();
        for (int end = 0; end < nums.length; end++) {
            int currNum = nums[end];
            while (!deque.isEmpty() && nums[deque.getLast()] < currNum) {
                deque.removeLast();
            }
            deque.addLast(end);
            if (end >= k - 1) {
                res[write++] = nums[deque.getFirst()];
                if (start == deque.getFirst()) {  // 这里最好用 nums[start] == nums[deque.getFirst()];与下一题的单调队列设计保持一致
                    deque.removeFirst();
                }
                start++;
            }
        }
        return res;
    }
}
```

[剑指 Offer 59 - II. 队列的最大值](https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof/) <br>
1. 一定要注意pop的条件是单调队列头部值与正常队列头部值相等。
2. Integer 元素也算地址，要用.equals才行
3. 为什么pop的时候不能用两个queue的size相等？ 反例 ： 1 2 4 2 1 3.
这题牛逼。
```Java
class MaxQueue {
    Deque<Integer> maxDeque = new LinkedList<>();
    Queue<Integer> queue = new LinkedList<>();
    public MaxQueue() {
        maxDeque = new LinkedList<>();
        queue = new LinkedList<>();
    }
    
    public int max_value() {
        if (queue.isEmpty()) {
            return -1;
        }
        return maxDeque.getFirst();
    }
    
    public void push_back(int value) {
        while (!maxDeque.isEmpty() && maxDeque.getLast() < value) {
            maxDeque.removeLast();
        }
        maxDeque.addLast(value);
        queue.offer(value);
    }
    
    public int pop_front() {
        if (queue.isEmpty()) {
            return -1;
        }
        if (queue.peek().equals(maxDeque.getFirst()) {  //一定要注意这里
            maxDeque.removeFirst();
        }
        return queue.poll();
    }
}
```
