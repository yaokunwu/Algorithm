# Binary Search Tree
## Defination
* 每个节点中的值必须大于（或等于）存储在其左侧子树中的任何值
* 每个节点中的值必须小于（或等于）存储在其右侧子树中的任何值
## Application
[Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/) <br>
思路：原问题为确认左子树所有值小于根节点以及右子树的所有值大于根节点，那么，子问题为根左右两棵子树都为BST并且当前左右node的值与根节点符合BST.
递归参数选择： treenode本身，lowerbound提供最小值，upperbound提供最大值.
```Java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValidBST(root, null, null);
    }
    
    private boolean isValidBST(TreeNode root, Integer lowerBound, Integer upperBound) {
        if (root == null) {return true;}
        if (lowerBound != null && root.val <= lowerBound) {return false;}
        if (upperBound != null && root.val >= upperBound) {return false;}
        
        boolean left = isValidBST(root.left, lowerBound, root.val);
        boolean right = isValidBST(root.right, root.val, upperBound);
        return left && right;
    }
}
```
中序遍历
```Java
class Solution {
    public boolean isValidBST(TreeNode root) {
        Integer prev = null;
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode curr = root;
        while (!stack.isEmpty() || curr != null) {
            if (curr != null) {
                stack.push(curr);
                curr = curr.left;
            } else {
                curr = stack.pop();
                if (prev != null && curr.val <= prev) {
                    return false;
                }
                prev = curr.val;
                curr = curr.right;
            }
        }
        return true;
    }
}
```

[Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/) <br>
思路：递归，参数不变，返回当前节点就行了
```Java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            return new TreeNode(val);
        }
        if (root.val > val) {
            root.left = insertIntoBST(root.left, val);
        } else {
            root.right = insertIntoBST(root.right, val);
        }
        return root;
    }
}
```

[Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/) <br>
思路： 找到要删除节点的左节点的最右节点或者右节点的最左节点然后改变left 和right即可 (也需要深入理解递归, 官方的图画的挺好)
这题有点东西。。
```Java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if (root == null) {
            return null;
        }
        if (root.val < key) {
            root.right = deleteNode(root.right, key);
        }
        if (root.val > key) {
            root.left = deleteNode(root.left, key);
        }
        if (root.val == key) {
            if (root.left == null && root.right == null) {
                return null;
            } else if (root.right != null) {
                TreeNode succ = successor(root);
                succ.right = deleteNode(root.right, successor(root).val);
                succ.left = root.left;
                return succ;
            } else {
                TreeNode prede = predecessor(root);
                prede.left = deleteNode(root.left, predecessor(root).val);
                return prede;
            }
        }
        return root;
    } 
    
    private TreeNode successor(TreeNode root) {
        if (root == null) {
            return null;
        }
        TreeNode right = root.right;
        while (right.left != null) {
            right = right.left;
        }
        return right;
    }
    
    private TreeNode predecessor(TreeNode root) {
        if (root == null) {
            return null;
        }
        TreeNode left = root.left;
        while (left.right != null) {
            left = left.right;
        }
        return left;
    }
}
```

