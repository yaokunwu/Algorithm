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
[Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/) <br>
思路： 这几道construct 二叉树的题思路差不多，都是先选择根节点，然后拆分向下。 (BST)
```Java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        if (nums == null || nums.length < 1) {
            return null;
        }
        int left = 0, right = nums.length - 1;
        return build(nums, left, right);
    }
    
    private TreeNode build(int[] nums, int left, int right) {
        if (left > right) {
            return null;
        }
        int middle = left + (right - left) / 2;
        TreeNode currNode = new TreeNode(nums[middle]);
        currNode.right = build(nums, middle + 1, right);
        currNode.left = build(nums, left, middle - 1);
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

### Binary Search Tree

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
**这个题目的总结就是： 滑动窗口使条件2满足，维护所有元素查看条件1是否满足**

### Arrays and Strings
#### 知识点
Cast an array to a List
Integer[] a = {0, 1, 2, 3, 4};  // **注意这里是Integer[]; 下次看pattern复习时看看是不是这么用的**
v1 = new ArrayList<>(Arrays.asList(a));

[Diagonal Traverse](https://leetcode.com/problems/diagonal-traverse/)<br>
思路1: 二维矩阵的遍历问题, 自己写的好鸡儿麻烦。。， 重点看官方写的，右matrix的matching问题，把第一行和最后一列mapping成一维后就可以直接遍历了。
```Java
class Solution {
    public int[] findDiagonalOrder(int[][] matrix) {
        if (matrix == null || matrix.length < 1) {
            return new int[0];
        }
        int m = matrix.length;
        int n = matrix[0].length;
        if (m == 1) {
            return matrix[0];
        }
        int[] res = new int[m * n];
        int end = 0;
        boolean isnormal = true;
        for (int col = 0; col < n; col++) {
            Deque<Integer> levelList = new LinkedList<>();
            int currRow = 0, currCol = col;
            while (currRow < m && currCol >= 0) {
                if (isnormal) {
                    levelList.addFirst(matrix[currRow][currCol]);
                } else {
                    levelList.addLast(matrix[currRow][currCol]);
                }
                currRow++;
                currCol--;
            }
            isnormal = !isnormal;
            while (!levelList.isEmpty()) {
                res[end] = levelList.removeFirst();
                end++;
            }
        }
        for (int row = 1; row < m; row++) {
            Deque<Integer> levelList = new LinkedList<>();
            int currRow = row, currCol = n - 1;
            while (currRow < m && currCol >= 0) {
                if (isnormal) {
                    levelList.addFirst(matrix[currRow][currCol]);
                } else {
                    levelList.addLast(matrix[currRow][currCol]);
                }
                currRow++;
                currCol--;
            }
            isnormal = !isnormal;
            while (!levelList.isEmpty()) {
                res[end] = levelList.removeFirst();
                end++;
            }
        }
        return res;
    }
}
```
与上述思路相同： 但简化了很多, 其实本题的实质和level order traversal一样。。
```Java
class Solution {
    public int[] findDiagonalOrder(int[][] matrix) {
        if (matrix == null || matrix.length < 1) {
            return new int[0];
        }
        int m = matrix.length;
        int n = matrix[0].length;
        int[] res = new int[m * n];
        int end = 0;
        Deque<Integer> levelList = new LinkedList<>();
        for (int idx = 0; idx < m + n - 1; idx++) {     // 把需要遍历的idx转换为一维进行遍历， 然后下面从一维转化为二维进行操作。
            int currRow = idx < n ? 0 : idx - n + 1;  // 注意这两行
            int currCol = idx < n ? idx : n - 1;      // 注意这两行
            while (currRow < m && currCol >= 0) {
                if (idx % 2 == 0) {
                    levelList.addFirst(matrix[currRow][currCol]);
                } else {
                    levelList.addLast(matrix[currRow][currCol]);
                }
                currRow++;
                currCol--;
            }
            while (!levelList.isEmpty()) {
                res[end] = levelList.removeFirst();
                end++;
            }
        }
        return res;
    }
}
```
**注意此题还可以直接reverse一个list，需要记住，Collections.reverse(List), list.clear()**

[Spiral Matrix](https://leetcode.com/problems/spiral-matrix/) <br>
思路:模拟问题，注意 **需要掌握方向的改变技巧以及标记矩阵的使用**。
```Java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return new ArrayList<>();
        }
        List<Integer> res = new ArrayList<>();
        int m = matrix.length;
        int n = matrix[0].length;
        int[][] directions = {{0, 1}, {1, 0}, {-0, -1}, {-1, 0}};  //需要掌握
        int row = 0, col = 0;
        boolean[][] visited = new boolean[m][n]; //需要掌握
        int total = m * n;
        int dIdx = 0;
        for (int i = 0; i < total; i++) {
            res.add(matrix[row][col]);
            visited[row][col] = true;
            int nextRow = row + directions[dIdx][0], nextCol = col + directions[dIdx][1];
            if (nextRow >= m || nextRow < 0 || nextCol >= n || nextCol < 0 || visited[nextRow][nextCol]) {
                dIdx = (dIdx + 1) % 4;
            }
            row += directions[dIdx][0];
            col += directions[dIdx][1];
        }
        return res;
    }
}
```

