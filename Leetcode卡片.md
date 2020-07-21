# Leetcode卡片习题分析记录

## 记录一些不太熟练，无法下手的题


### LinkedList
[Flatten a Multilevel Doubly Linked List](https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/) <br>
思路： 首先通过题目的要求，发现flatten后的list排列和二叉树preorder traversal相同，所以确定出解题方向为二叉树preorder traversal，又考虑到
linkedlist node的连接方式，确定出需要返回系列化好的尾结点。 并且考虑到尾结点与下一个节点连接过程与当前节点和下一个节点连接过程的异同确定出需要另一个prev节点， 然后
递归写出recursion。 （这题要思考好多。。） <br>
**二叉树还是不熟练**
```Java
class Solution {
    public Node flatten(Node head) {
        if (head == null) {
            return null;
        }
        Node sentinel = new Node(2020, null, head, null);
        dfs(sentinel, head);
        sentinel.next.prev = null;
        return sentinel.next;
    }
    
    private Node dfs(Node prev, Node curr) {
        if (curr == null) {
            return prev;
        }
        curr.prev = prev;
        prev.next = curr;
        Node tempNext = curr.next;
        Node tail = dfs(curr, curr.child);
        curr.child = null;
        return dfs(tail, tempNext);
    }
}
```

### Binary Tree
二叉树还是不熟练， 调用递归时可以同时引入多个参数，不需要局限于二叉树的traversal方法
[Symmetric Tree]()<br>
思路：二叉树是否镜像对称转化为，根节点的左右子树的根节点是否相等以及左子树的右和右子树的左，而且左子树的左和右子树的右是否相等
```Java
//Iterative
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) {
            return true;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        queue.offer(root);
        while (!queue.isEmpty()) {
            TreeNode left = queue.poll();
            TreeNode right = queue.poll();
            if (left == null && right == null) {
                continue;
            }
            if ((left == null || right == null) || (left.val != right.val)) {
                return false;
            }
            
            queue.offer(left.left);
            queue.offer(right.right);
            queue.offer(left.right);
            queue.offer(right.left);
        }
        return true;
    }
}
```
**注意： 在使用queue的时候，null可以被加入, 之所以在level order traversal的时候要判断是否为null是因为要取node.val，不需要null node**

```Java
//Recursive
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return check(root, root);
    }
    
    private boolean check(TreeNode left, TreeNode right) {
        if (left == null && right == null) {
            return true;
        }
        if (left == null || right == null) {
            return false;
        }
        
        boolean curr = left.val == right.val && check(left.left,right.right) && check(left.right, right.left);
        return curr;
    }
}
```

[Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/) <br>
思路：题很经典，注意根节点的全局变量以及建立顺序。
```Java
class Solution {
    Map<Integer, Integer> map;
    int post_idx; // 此处必须要用全局变量维持同一状态
    int[] postorder;
    int[] inorder;
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        map = new HashMap<>();
        this.postorder = postorder;
        this.inorder = inorder;
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        int inorderStart = 0, inorderEnd = inorder.length - 1;
        post_idx = postorder.length - 1;
        return build(inorderStart, inorderEnd);
    }
    
    private TreeNode build(int inorderStart, int inorderEnd) {
        if (inorderStart > inorderEnd) {
            return null;
        }
        int rootVal = postorder[post_idx];
        int rootIdx = map.get(rootVal);
        TreeNode curr = new TreeNode(rootVal);
        
        post_idx--;
        curr.right = build(rootIdx + 1, inorderEnd); //先右后左
        curr.left = build(inorderStart, rootIdx - 1); //先右后左
        return curr;
    }
}
```

[Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) <br>
思路：和上题一样，preorder正序construct即可。
```Java
class Solution {
    int preIdx = 0;
    Map<Integer, Integer> map;
    int[] preorder, inorder;
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        this.preorder = preorder;
        this.inorder = inorder;
        this.map = new HashMap<>();
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return build(0, inorder.length - 1);
    }
    
    private TreeNode build(int start, int end) {
        if (start > end) {
            return null;
        }
        
        int rootVal = preorder[preIdx];
        TreeNode currNode = new TreeNode(rootVal);
        
        int idx = map.get(rootVal);
        preIdx++;
        
        currNode.left = build(start, idx - 1);
        currNode.right = build(idx + 1, end);
        return currNode;
    }
}
```

[Serialize and Deserialize Binary Tree]
思路： BFS序列化，反序列化时需要用到字符串转数组  **注意 String[] strArray = data.substring(1, data.length()-1).split(",");**
```Java
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder res =  new StringBuilder();
        res.append("[");
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        int validNum = 1;
        while (!queue.isEmpty() || validNum == 0) {
            TreeNode curr = queue.poll();
            if (curr == null) {
                res.append("null,");
            } else {
                validNum--;
                res.append(String.valueOf(curr.val));
                res.append(",");
                if (curr.left != null) {
                    validNum++;
                }
                queue.offer(curr.left);
                if (curr.right != null) {
                    validNum++;
                }
                queue.offer(curr.right);
            }
        }
        res.deleteCharAt(res.length() - 1);
        res.append("]");
        return res.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        String[] strArray = data.substring(1, data.length() - 1).split(",");
        if (strArray[0].equals("null")) {
            return null;
        }
        TreeNode root = new TreeNode(Integer.valueOf(strArray[0]));
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        int i = 1;
        while (!queue.isEmpty()) {
            TreeNode curr = queue.poll();
            if (strArray[i].equals("null")) {
                curr.left = null;
                i++;
            } else {
                TreeNode leftNode = new TreeNode(Integer.valueOf(strArray[i]));
                curr.left = leftNode;
                queue.offer(leftNode);
                i++;
            }

            if (strArray[i].equals("null")) {
                curr.right = null;
                // i++;
            } else {
                TreeNode rightNode = new TreeNode(Integer.valueOf(strArray[i]));
                curr.right = rightNode;
                queue.offer(rightNode);
            }
            i++;
        }
        return root;
    }
}
```

[Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/) <br>
思路： 滑动窗口的思路，窗口中的元素需要储存在二叉树中方便提取最接近当前元素的值 用来判断条件是否成立。 <br>
**新增知识点： treeSet的operation
```Java
class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        TreeSet<Long> set = new TreeSet<>();
        int start = 0;
        for (int end = 0; end < nums.length; end++) {
            Long currVal = (long) nums[end];
            Long s = set.ceiling(currVal);
            if (s != null && s - currVal <= t) {
                return true;
            }
            s = set.floor(currVal);
            if (s != null && currVal - s <= t) {
                return true;
            }
            set.add(currVal);
            if (set.size() > k) {
                set.remove((long) nums[start]);
                start++;
            }
        }
        return false;
    }
}
```
**注意treeSet (自平衡二叉树) 中 treeSet.ceiling(), treeSet.floor(), treeSet.remove(), treeSet.add() 的使用，相当于二叉树的搜索，删除和添加操作， 以及 long 和 int的转换**<br>
思路2： 模板还是滑动窗口，不过储存元素换为了桶储存，Map<桶id, 值> <br>
```Java
class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        if (nums == null || nums.length <= 1 || t < 0) {
            return false;
        }
        Map<Long, Long> map = new HashMap<>();
        int start = 0;
        Long width = (long) t + 1; // 因为计算id时， width要当除数，不能为0;
        for (int end = 0; end < nums.length; end++) {
            Long currVal = (long) nums[end];
            Long currId = getId(currVal, width);
            if (map.containsKey(currId)) {
                return true;
            }
            if (map.containsKey(currId - 1) && currVal - map.get(currId - 1) <= t) {
                return true;
            }
            if (map.containsKey(currId + 1) && map.get(currId + 1) - currVal <= t) {
                return true;
            }
            map.put(currId, currVal);
            if (map.size() > k) {
                Long removedId = getId((long) nums[start], width);
                map.remove(removedId);
                start++;
            }
        }
        return false;
    }
    
    private Long getId(Long num, Long width) {
        return num < 0 ? (num + 1) / width - 1 : num / width;
    } 
}
```
