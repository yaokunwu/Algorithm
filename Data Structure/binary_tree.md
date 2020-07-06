# Binary Tree
## 知识点
### Binary Tree Traversal

**Pre-order Traversal**：先访问根节点，再前序遍历左子树，再前序遍历右子树<br>
**In-order Traversal**：先中序遍历左子树，再访问根节点，再中序遍历右子树<br>
**Post-order Traversal**：先后序遍历左子树，再后序遍历右子树，再访问根节点<br>

***注意点*** <br>
* 以根访问顺序决定是什么遍历<br>
* 左子树都是优先右子树<br>

**Tree Structure**
```Java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int val) {
      this.val = val;
    }
} 
```
* * *
#### Sample code (example):  Adding values to a list from every single node in a tree

**Pre-order Traversal** **- (Recursive)**
```Java
public class PreOrderRecursive {
    List<Integer> res = new ArrayList<>();
    public void preOrder(TreeNode root) {
        if (root == null) {
            return;
        }
        // 在递归之前写操作
        res.add(root.val);
        preOrder(root.left);
        preOrder(root.right);
    }
}
```

**In-order Traversal** **- (Recursive)**
```Java
public class InOrderRecursive {
    List<Integer> res = new ArrayList<>();
    public void inOrder(TreeNode root) {
        if (root == null) {
            return;
        }
        inOrder(root.left);
        // 在递归中间写操作
        res.add(root.val);
        inOrder(root.right);
    }
}
```

**Post-order Traversal** **- (Recursive)**
```
public class PostOrderRecursive {
    List<Integer> res = new ArrayList<>();
    public void postOrder(TreeNode root) {
        if (root == null) {
            return;
        }
        postOrder(root.left);
        postOrder(root.right);
        // 在递归之后写操作
        res.add(root.val);
    }
}
```

**Pre-order Traversal** **- (Iterative)**
```Java
public class PreOrderIterative {
     public List<Integer> preOrder(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }
        List<Integer> res = new ArrayList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        stack.addFirst(root);
        while (!stack.isEmpty()) {
            TreeNode curr = stack.removeFirst();
            res.add(curr.val);
            if (curr.right != null) {
                stack.addFirst(curr.right);
            }
            if (curr.left != null) {
                stack.addFirst(curr.left);
            }
        }
        return res;
    }
}
```

**In-order Traversal** **- (Iterative)**
```Java
public class InOrderIterative {
     public List<Integer> inOrder(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }
        List<Integer> res = new ArrayList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode curr = root;
        while (!stack.isEmpty() || curr != null) {
            while (curr != null) {
                stack.addFirst(curr);
                curr = curr.left;
            }
            curr = stack.removeFirst();
            res.add(curr.val);
            curr = curr.right;
        }
        return res;
    }
}
```

**Post-order Traversal** **- (Iterative)**
```Java
public class PostOrderIterative {
     public List<Integer> postOrder(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }
       // List<Integer> res = new LinkedList<>()；// List接口没有addFirst, LinkedList有
        LinkedList<Integer> res = new LinkedList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        stack.addFirst(root);
        while (!stack.isEmpty()) {
            TreeNode curr = stack.removeFirst();
            res.addFirst(curr.val);  // List接口没有addFirst, LinkedList有
            if (curr.left != null) {
                stack.addFirst(curr.left);
            }
            if (curr.right != null) {
                stack.addFirst(curr.right);
            }
        }
        return res;
    }
}
```

**Depth First Search**  **- (Top - down) Recursion part similar to pre-order traversal**
```Java
class Solution {
    private void dfs(TreeNode node, List<Integer> res) {
        if (node == null) {
            return;
        }
        res.add(node.val);
        dfs(node.left, res);
        dfs(node.right, res);
    }
    public List<Integer> dfsTopDown(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        dfs(root, res);
        return res;
    }
}
```

**Depth First Search** **- (Bottom - up) Recursion part similar to post-order traversal (Divide and conquer)**
```Java
class Solution {  
    private List<Integer> divideAndConquer(TreeNode node) {
        if (node == null) {
            return new ArrayList<>();
        }
        List<Integer> left = divideAndConquer(node.left);
        List<Integer> right = divideAndConquer(node.right);
        List<Integer> curr = new ArrayList<>();
        //curr.add(node.val); /*是post order traversal 但是返回的结果与pre-order traversal相同*/
        curr.addAll(left);
        curr.addAll(right);
        curr.add(node.val); /*是post order traversal 返回的结果与post-order traversal相同*/
        return curr;
    }
    public List<Integer> dfsBottomUp(TreeNode root) {
            return divideAndConquer(root);
    }
}
```

**Breadth First search** **- (Level order traversal)**
```Java
class Solution {  
    public List<Integer> bfs(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            for (int i = 0; i < levelSize; i++) {
                TreeNode curr = queue.poll();
                res.add(curr.val);
                if (curr.left != null) {
                    queue.add(curr.left);
                }
                if (curr.right != null) {
                    queue.add(curr.right);
                }
            }
        }
        return res;
    }
}
```
* * *
#### **习题及答案

[Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree) <br>
思路1：bottom up (Post order, Divide and Conquer)

```Java
class Solution {
    private int maxDepthBottomUp(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int leftDepth = maxDepthBottomUp(root.left);
        int rightDepth = maxDepthBottomUp(root.right);
        int currDepth = Math.max(leftDepth, rightDepth) + 1;
        return currDepth;
    }
    
    public int maxDepth(TreeNode root) {
        return maxDepthBottomUp(root);
    }
}
```

思路2：Top Down (Pre order + backtracking)

```Java
class Solution {
    int maxDepth = 0;
    private void maxDepthTopDown(TreeNode root, int currDepth) {
        if (root == null) {
            return;
        }
        currDepth = currDepth + 1;
        if (root.left == null && root.right == null && currDepth > maxDepth) {
                maxDepth = currDepth;
        }
        maxDepthTopDown(root.left, currDepth);
        maxDepthTopDown(root.right, currDepth);
        currDepth -= 1;
    }
    
    public int maxDepth(TreeNode root) {
        maxDepthTopDown(root, 0);
        return maxDepth;
    }
}
```

[Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree) <br>
思路：bottom up (Post order, Divide and Conquer) //自己的思路

```Java
class Solution {
    private int getHeight(TreeNode node) {
        if (node == null) {
            return 0;
        }
        int left = getHeight(node.left);
        int right = getHeight(node.right);
        return Math.max(left, right) + 1;
    }

    public boolean isBalanced(TreeNode root) {
        if(root == null) {
            return true;
        }
        boolean left = isBalanced(root.left);
        boolean right = isBalanced(root.right);
        
        if (left && right && Math.abs(getHeight(root.left) - getHeight(root.right)) <= 1) {
            return true;
        }
        return false;
    }
}
```
优化后(本来是否balance就可以自下而上求depth的过程中得知)
```Java
class Solution {
    private boolean balanced = true;
    private int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int leftDepth = maxDepth(root.left);
        int rightDepth = maxDepth(root.right);
        if (Math.abs(rightDepth - leftDepth) > 1) {
            balanced = false;
        }
        return Math.max(leftDepth, rightDepth) + 1;
    }
    
    public boolean isBalanced(TreeNode root) {
        maxDepth(root);
        return balanced;
    }
}
```

[binary-tree-maximum-path-sum](https://leetcode.com/problems/binary-tree-maximum-path-sum)
思路： bottom up (Post order, Divide and Conquer) 路径和等于当前节点的值与正数子节点的最大贡献值之和。 当前节点最大贡献值等于当前节点值+子节点最大贡献值。 思考的时候只按照某一节点开始思考。
```Java
class Solution {
    int maxSum = Integer.MIN_VALUE;
    private int maxContribute(TreeNode root) {
        int maxCon = 0;  // 当前节点最大贡献值
        int sum = root.val; // 当前节点最大路径和
        if (root == null) {
            return maxCon;
        }
        int leftMax = maxContribute(root.left);
        int rightMax = maxContribute(root.right);
        maxCon = Math.max(Math.max(leftMax, rightMax) + root.val, root.val);
        if (leftMax > 0) {
            sum += leftMax;
        }
        if (rightMax > 0) {
            sum += rightMax;
        }
        if (sum > maxSum) {
            maxSum = sum;
        }
        return maxCon;
    }
    public int maxPathSum(TreeNode root) {
        maxContribute(root);
        return maxSum;
    }
}
```
优化后(基本一致，别人代码更简洁,我的代码更好理解)
```Java
class Solution {
    private int maxSum = Integer.MIN_VALUE;
    
    public int maxPathSum(TreeNode root) {
        maxGain(root);
        return maxSum;
    }
    
    private int maxGain(TreeNode node) {
        if (node == null) {
            return 0;
        }
        // 递归计算左右子节点的最大贡献值
        // 只有在最大贡献值大于 0 时，才会选取对应子节点
        int leftGain = Math.max(maxGain(node.left), 0);
        int rightGain = Math.max(maxGain(node.right), 0);
        // 节点的最大路径和取决于该节点的值与该节点的左右子节点的最大贡献值
        int curMaxSum = node.val + leftGain + rightGain;
        // 更新最大路径和
        maxSum = Math.max(maxSum, curMaxSum);
        // 返回节点的最大贡献值
        return node.val + Math.max(leftGain, rightGain);
    }
}
```

[Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree)
思路: bottom up (Post order, Divide and Conquer) 返回不为null的treenode。 判断当前节点是否等于要找节点，如果是，返回当前节点，不是，返回null或者以前找到的节点。 若左右同时不会null， 返回当前节点。
```Java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) {
            return root;
        }
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left != null && right != null) {
            return root;
        } else if (left == null) {
            return right;
        } else {
            return left;
        }
    }
}
```

[Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal)
思路：模板level order traversal， 每个level新建一个list保存当前level的结果
```Java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            List<Integer> curr = new ArrayList<>();
            for (int i = 0; i < levelSize; i++) {
                TreeNode currNode = queue.poll();
                curr.add(currNode.val);
                if (currNode.left != null) {
                    queue.offer(currNode.left);
                }
                if (currNode.right != null) {
                    queue.offer(currNode.right);
                }
            }
            res.add(curr);
            
        }
        return res;
    }
}
```
[Binary Tree Level Order Traversal II](https://leetcode.com/problems/binary-tree-level-order-traversal-ii)
思路： 把结果翻过来加就行了
```Java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            List<Integer> curr = new ArrayList<>();
            for (int i = 0; i < levelSize; i++) {
                TreeNode currNode = queue.poll();
                curr.add(currNode.val);
                if (currNode.left != null) {
                    queue.offer(currNode.left);
                }
                if (currNode.right != null) {
                    queue.offer(currNode.right);
                }
            }
            res.add(0, curr);  //反过来加
            
        }
        return res;
    }
}
```

[Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal)
思路： 定义一个boolean变量来控制左右。
```Java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> res = new LinkedList<>();
        if (root == null) {
            return res;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        boolean leftToRight = true;
        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            List<Integer> curr = new LinkedList<>();
            for (int i = 0; i < levelSize; i++) {
                TreeNode currNode = queue.poll();
                if (leftToRight) {
                    curr.add(currNode.val);
                } else {
                    curr.add(0, currNode.val);
                }
                if (currNode.left != null) {
                    queue.offer(currNode.left);
                }
                if (currNode.right != null) {
                    queue.offer(currNode.right);
                }
            }
            res.add(curr);
            leftToRight = !leftToRight;
        }
        return res;
    }
}
```

#### Application for Binary Search Tree <br>
[Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree)
思路： bottom up (Post order, Divide and Conquer) 
```Java
///一个有问题的解 ： 必须满足左边所有值小于当前node， 右边所有值大于当前node
class Solution {
    public boolean isValidBST(TreeNode root) {
        if (root == null) {
            return true;
        }
        
        boolean left = isValidBST(root.left);
        boolean right = isValidBST(root.right);
        if ((root.left != null && root.left.val >= root.val) || (root.right != null && root.right.val <= root.val) || !left || !right) {
            return false;      
        }
            
        return true;
    }
}
```
思路： 当前node大于左子树的最大值， 小于右子树的最小值 (自己想的，写法很难受)。 思路有问题， 因为这是BST，不需要用从底向上postorder traversal
```Java
///修改后
class Solution {
    boolean isvalid = true;
    private int[] minMax(TreeNode root) {
        if (root == null) {
            return null;
        }
        int min = root.val;
        int max = root.val;
        int[] left = minMax(root.left);
        int[] right = minMax(root.right);
        if (left != null && left[1] >= root.val) {
            isvalid = false;
        }
        if (right != null && right[0]<= root.val) {
            isvalid = false;
        }
        
        if (left != null) {
            min = Math.min(left[0], min);
            max = Math.max(left[1], max);
        }
        if (right != null) {
            min = Math.min(right[0], min);
            max = Math.max(right[1], max);
        }
        return new int[] {min, max};   
    }
    
    public boolean isValidBST(TreeNode root) {
        minMax(root);
        return isvalid;
    }
}
```
思路2：中序遍历，如果中序遍历得到的节点的值小于等于前一个 preVal，说明不是二叉搜索树 (课改写为中序非递归)
```Java
class Solution {
    boolean isValid = true;
    public boolean isValidBST(TreeNode root) {
        inOrder(root);
        return isValid;
    }
    
    Integer pre;
    private void inOrder(TreeNode root) {
        if (root == null) {
            return;
        }
        inOrder(root.left);
        if (pre != null && pre >= root.val) {
            isValid = false;
        }
        pre = root.val;
        inOrder(root.right);
    }
}
```

[Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree)
思路1： 找到最后一个叶子节点满足插入条件即可  Recursion (BST不需要遍历，所以跟前面那些traversal没有关系，遍历是所有元素过一遍)
```
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            return new TreeNode(val);
        }
        if (root.val < val) {
            root.right = insertIntoBST(root.right, val);
        }
        if (root.val > val) {
            root.left = inserIntoBST(root.left, val);
        }
        return root;
    }
}
```
思路2：改写为iterative方法
```Java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            return new TreeNode(val);
        }
        TreeNode prev = null;
        TreeNode curr = root;
        while(curr != null) {
            prev = curr;
            if (curr.val < val) {
                curr = curr.right;
            } else {
                curr = curr.left;
            }
        }
        if (prev.val > val) {
            prev.left = new TreeNode(val);
        } else {
            prev.right = new TreeNode(val);
        }
        return root;
    }
}
```

## 总结
* 掌握二叉树递归与非递归遍历
* 理解DFS前序遍历与分治法
* 理解BFS层次遍历








        
