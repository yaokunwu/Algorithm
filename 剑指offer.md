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
修改后
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
