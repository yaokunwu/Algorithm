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
**TO-DO 这题还有个按层模拟，有时间可以看一下**

#### String
知识点： <br>
1. String s1 = new String(s2); 拷贝s2给s1;
2. String s1 = "Something";  s1 == "Something" 为true
3. Java 无法改变已初始化好的string
4. s1.indexOf(char) : 返回第一个char的位置
5. s1.lastIndexOf(char) : 返回最后一个char的位置
6. s1.substring()  4，5，6这三个operation会take o（N）时间

[Add Binary](https://leetcode.com/problems/add-binary/) <br>
思路： 自己写的垃圾版本
```Java
class Solution {
    public String addBinary(String a, String b) {
        StringBuilder res = new StringBuilder();
        int carry = 0;
        int aLen = a.length();
        int bLen = b.length();
        int length = Math.max(aLen, bLen);
        if (bLen < aLen) {
            for (int i = 0; i < aLen - bLen; i++) {
                b = "0" + b;
            }
        } else if (aLen < bLen) {
            for (int i = 0; i < bLen - aLen; i++) {
                a = "0" + a;
            }
        }
        char[] charArray1 = a.toCharArray();
        char[] charArray2 = b.toCharArray();
        for (int i = length - 1; i >= 0; i--) {
            int value1 = Integer.parseInt(String.valueOf(charArray1[i]));
            int value2 = Integer.parseInt(String.valueOf(charArray2[i]));
            int value = value1 + value2 + carry;
            if (value > 1) {
                value = value % 2;
                carry = 1;
            } else {
                carry = 0;
            }
            res.append(String.valueOf(value));
        }
        if (carry == 1) {
            res.append("1");
        }
        res = res.reverse();
        return res.toString();
    }
}
```
优雅package版：只适用于长度较短的binary string
class Solution {
    public String addBinary(String a, String b) {
        return Integer.toBinaryString(Integer.parseInt(a, 2) + Integer.parseInt(b, 2));
    }
}

思路2： i可以正序遍历，string可以倒序
```Java
class Solution {
    public String addBinary(String a, String b) {
        StringBuilder res = new StringBuilder();
        int n = Math.max(a.length(), b.length());
        int carry = 0;
        for (int i = 0; i < n; i++) {
            int add1 = i >= a.length() ? 0 : a.charAt(a.length() - 1 - i) - '0';   // 注意这个减0的操作
            int add2 = i >= b.length() ? 0 : b.charAt(b.length() - 1 - i) - '0';
            int digit = add1 + add2 + carry;
            carry = digit / 2;
            digit = digit % 2;
            res.append(digit);
        }
        if (carry == 1) {
            res.append("1");
        }
        return res.reverse().toString();
    }
}
```
**TO-DO:这道题有个位运算解法，回头看**

[Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/) <br>
思路： 自己用双StringBuilder AC了， 主要看一下官方的代码
```Java
class Solution {
    public StringBuilder trimSpaces(String s) {
        int left = 0, right = s.length() - 1;
        // 去掉字符串开头的空白字符
        while (left <= right && s.charAt(left) == ' ') ++left;

        // 去掉字符串末尾的空白字符
        while (left <= right && s.charAt(right) == ' ') --right;

        // 将字符串间多余的空白字符去除
        StringBuilder sb = new StringBuilder();
        while (left <= right) {
            char c = s.charAt(left);

            if (c != ' ') sb.append(c);
            else if (sb.charAt(sb.length() - 1) != ' ') sb.append(c);

            ++left;
        }
        return sb;
    }

    public void reverse(StringBuilder sb, int left, int right) {
        while (left < right) {
            char tmp = sb.charAt(left);
            sb.setCharAt(left++, sb.charAt(right));
            sb.setCharAt(right--, tmp);
        }
    }

    public void reverseEachWord(StringBuilder sb) {
        int n = sb.length();
        int start = 0, end = 0;

        while (start < n) {
            // 循环至单词的末尾
            while (end < n && sb.charAt(end) != ' ') ++end;
            // 翻转单词
            reverse(sb, start, end - 1);
            // 更新start，去找下一个单词
            start = end + 1;
            ++end;
        }
    }

    public String reverseWords(String s) {
        StringBuilder sb = trimSpaces(s);

        // 翻转字符串
        reverse(sb, 0, sb.length() - 1);

        // 翻转每个单词
        reverseEachWord(sb);

        return sb.toString();
    }
}
```

思路2： 常用API
```Java
class Solution {
    public String reverseWords(String s) {
        s = s.trim();
        List<String> sArray = new ArrayList<>(Arrays.asList(s.split(" ")));
        Collections.reverse(sArray);
        return String.join(" ", sArray);
    }
}
```

注意 List<Integer> res = new ArrayList<>(Arrays.asList( Integer array )) ** 注意里面必须是integer array **
常用API： <br>
StringBuilder.setCharAt(idx, char);<br>
StringBuilder.setLength(0) : 为清空StringBuilder<br>
String.join(" ", list)<br>
s.trim(); 去除s的前后空格<br><br>
s.split(" "); 把s里面的字符按空格分开成String[] <br>

### Hash Table
[Design HashSet](https://leetcode.com/problems/design-hashset/) <br>
思路： 注意在设计的过程中，建立set 和建立bucket最后分离操作，降低设计复杂度。

常用API
LinkedList.indexOf(Object)用于元素的查找， -1 表示不存在 <br>
LinkedList.remove(Object) 直接移除元素
LinkedList.contains(Object)直接查找是否存在

**注意： 一定要注意在遍历list过程中的删除问题，一般不要用for(item : list)这种，一边遍历一边删除可能需要倒序。。** 

* hashmap.putIfAbsent();

[Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/) <br>
思路：一道简单题，有种解法是char array 和 char相关的，回头看，挺重要的。


[Minimum Index Sum of Two Lists](https://leetcode.com/explore/learn/card/hash-table/184/comparison-with-other-data-structures/1177/) <br>
思路： 自己用hashtable和优先级序列做的。。有点麻烦。
```Java
class Solution {
    private class Node {
        String key;
        int idx;
        private Node(String key, int idx) {
            this.key = key;
            this.idx = idx;
        }
    }
    public String[] findRestaurant(String[] list1, String[] list2) {
        Map<String, Integer> map = new HashMap<>();
        for (int i = 0; i < list1.length; i++) {
            map.put(list1[i], i);
        }
        
        PriorityQueue<Node> minHeap = new PriorityQueue<>((a, b) -> a.idx - b.idx);
        for (int i = 0; i < list2.length; i++) {
            if (map.containsKey(list2[i])) {
                int idx = map.get(list2[i]) + i;
                if (minHeap.isEmpty()) {
                    minHeap.offer(new Node(list2[i], idx));
                } else if (idx < minHeap.peek().idx) {
                    while (!minHeap.isEmpty() && idx < minHeap.peek().idx) {
                        minHeap.poll();
                    }
                    minHeap.offer(new Node(list2[i], idx));
                } else if (idx == minHeap.peek().idx) {
                    minHeap.offer(new Node(list2[i], idx));
                }
            }
        }
        String[] res = new String[minHeap.size()];
        for (int i = minHeap.size() - 1; i >= 0; i--) {
            res[i] = minHeap.poll().key;
        }
        return res;
    }
}
```
一定要注意一边遍历一边删除的操作

官方思路: 哈希表，key为下标和，value为list of string
```Java
class Solution {
    public String[] findRestaurant(String[] list1, String[] list2) {
        Map<String, Integer> map = new HashMap<>();
        for (int i = 0; i < list1.length; i++) {
            map.put(list1[i], i);
        }
        int minSum = Integer.MAX_VALUE;
        List<String> res = new ArrayList<>();
        for (int i = 0; i < list2.length; i++) {
            if (map.containsKey(list2[i])) {
                int currSum = map.get(list2[i]) + i;
                if (currSum < minSum) {
                    res.clear();
                    res.add(list2[i]);
                    minSum = currSum;
                } else if (currSum == minSum) {
                    res.add(list2[i]);
                }
            }
        }
        String[] sArray = new String[res.size()];
        return res.toArray(sArray);
    }
}
```
对哈希表的使用有所欠缺，需要继续加强训练 <br>
List.toArray(res); (to array 表示去哪个array) 这个array的type必须和List中的type一致。
Arrays.copyOfRange(int[], 0, index); 拷贝部分数组

[Valid Sudoku](https://leetcode.com/explore/learn/card/hash-table/185/hash_table_design_the_key/1126/) <br>
难点： 这里的box index 有点难想。
```Java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        //主要就是找个key
       Set<Character>[][] set = new HashSet[3][9];
        for (int i = 0; i < set.length; i++) {
            for (int j = 0; j < set[0].length; j++) {
                set[i][j] = new HashSet<>();
            }
        }
        
        for (int row = 0; row < board.length; row++) {
            for (int col = 0; col < board[0].length; col++) {
                char currChar = board[row][col];
                
                if (currChar == '.') {
                    continue;
                }
                
                int key3 = (row / 3) * 3 + col / 3;
                
                if (set[0][row].contains(currChar)) {
                    return false;
                } else {
                    set[0][row].add(currChar);
                }
                
                if (set[1][col].contains(currChar)) {
                    return false;
                } else {
                    set[1][col].add(currChar);
                }
                
                if (set[2][key3].contains(currChar)) {
                    return false;
                } else {
                    set[2][key3].add(currChar);
                }
            }
        }
        return true;
    }
}
```
注意 ** 创建array的set或者各种object的方法。**

[Find Duplicate Subtrees](https://leetcode.com/explore/learn/card/hash-table/185/hash_table_design_the_key/1127/) <br>
思路： 官方给的思路： 每棵子树的序列化结果不同，储存在map中就好。
我写的这个有点问题，最好是bottom up dfs，然后map建立的value应该是次数，而不是node
```Java
class Solution {
    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        List<TreeNode> res = new ArrayList<>();
        if (root == null) { return res; }
        Map<List<Integer>, TreeNode> map = new HashMap<>();
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode curr = stack.pop();
            List<Integer> currList = dfs(curr);
            if (map.containsKey(currList)) {
                res.add(map.get(currList));
                continue;
            }
            map.put(currList, curr);
            if (curr.right != null) {
                stack.push(curr.right);
            }
            if (curr.left != null) {
                stack.push(curr.left);
            }
        }
        return res;
    }
    
    private List<Integer> dfs(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode curr = stack.pop();
            if (curr != null) {
                res.add(curr.val);
                stack.push(curr.right);
                stack.push(curr.left);
            } else {
                res.add(null);
            }
        }
        return res;
    }
}
```
修改完map后：
```Java
class Solution {
    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        List<TreeNode> res = new ArrayList<>();
        if (root == null) { return res; }
        Map<List<Integer>, Integer> map = new HashMap<>();
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode curr = stack.pop();
            List<Integer> currList = dfs(curr);
            map.put(currList, map.getOrDefault(currList, 0) + 1);
            if (map.get(currList) == 2) {
                res.add(curr);
            }
            if (curr.right != null) {
                stack.push(curr.right);
            }
            if (curr.left != null) {
                stack.push(curr.left);
            }
        }
        return res;
    }
    
    private List<Integer> dfs(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode curr = stack.pop();
            if (curr != null) {
                res.add(curr.val);
                stack.push(curr.right);
                stack.push(curr.left);
            } else {
                res.add(null);
            }
        }
        return res;
    }
}
```
修改完traversal顺序后
```Java
class Solution {
    Map<List<Integer>, Integer> map;
    List<TreeNode> res;
    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        res = new ArrayList<>();
        map = new HashMap<>();
        dfs(root);
        return res;
    }
    
    private List<Integer> dfs(TreeNode root) {
        if (root == null) {
            List<Integer> curr = new ArrayList<>();
            curr.add(null);
            return curr;
        }
        
        List<Integer> left = dfs(root.left);
        List<Integer> right = dfs(root.right);
        left.addAll(right);
        left.add(0, root.val);
        List<Integer> curr = new ArrayList<>(left);  //***
        map.put(curr, map.getOrDefault(curr, 0) + 1);
        if (map.get(curr) == 2) {
            res.add(root);
        }
        return left;
    }
}
```
**注意：**
* 1. 在向map中加入数据时要确保数据不会变动
* 2. 连接两个list可以用 list1.addAll(list2);

[RandomizedSet](https://leetcode.com/problems/insert-delete-getrandom-o1/) <br>
```Java
class RandomizedSet {

    /** Initialize your data structure here. */
    List<Integer> list;
    Map<Integer, Integer> map;
    Random random;
    public RandomizedSet() {
        list = new ArrayList<>();
        map = new HashMap<>();
        random = new Random();
    }
    
    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
    public boolean insert(int val) {
        if (map.containsKey(val)) {
            return false;
        } else {
            map.put(val, list.size());
            list.add(val);
            return true;
        }
    }
    
    /** Removes a value from the set. Returns true if the set contained the specified element. */
    public boolean remove(int val) {
        if (!map.containsKey(val)) {
            return false;
        } else {
            int idx = map.get(val);
            int last = list.get(list.size() - 1);
            list.set(idx, last);
            map.put(last, idx);
            list.remove(list.size() - 1);
            map.remove(val);
            return true;
        }
        
    }
    
    /** Get a random element from the set. */
    public int getRandom() {
        return list.get(random.nextInt(list.size()));
    }
}
```
这个题的分析挺好， arraylist的random access 时间复杂度是o1， 都忘了。。 <br>
注意使用 random.nextInt(), 以及 list.set(index, value);

[4Sum II](https://leetcode.com/explore/learn/card/hash-table/187/conclusion-hash-table/1134/) <br>
思路： 我用了backtracking，o（n)4的复杂度，我感觉可以用动规。 n sum 算法. 这里用的是two sum
```Java
class Solution {
    public int fourSumCount(int[] A, int[] B, int[] C, int[] D) {
        Map<Integer, Integer> map = new HashMap<>();
        int n = A.length;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                int sum = A[i] + B[j];
                map.put(sum, map.getOrDefault(sum, 0) + 1);
            }
        }
        int res = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                int sum = C[i] + D[j];
                res += map.getOrDefault(-sum, 0);
            }
        }
        return res;
    }
}
```

### Binary Search
注意 ** 
```Java
        int a = 2147395599;
        a = a / 2;
        long b = (long) (a * a); // 先计算两个int 的乘积，再cast 为 long， 会造成越界
        long b = (long) a * a; // 先将首个a cast 为long 型 然后进行相乘计算，相当于 两个long相乘，不会越界
```
[Pow(x, n)](https://leetcode.com/problems/powx-n/) <br>
思路： 快速幂算法，注意： Math.abs(Integer.MIN_VALUE) = Integer.MIN_VALUE; 需要先把n转化为long,或者直接加负号
```Java
class Solution {
    public double myPow(double x, int n) {
        long count = n;
        return n >= 0 ? recur(x, count) : 1 / recur(x, -count);   
    }
    
    private double recur(double x, long n) {
        if (n == 0) {
            return 1;
        }
        double prev = recur(x, n / 2);
        return n % 2 == 0 ? prev * prev : prev * prev * x;
    }
}
```

Stack && Queue
[Number of Islands](https://leetcode.com/explore/learn/card/queue-stack/231/practical-application-queue/1374/) <br>
思路： 用了queue和visited一次性解决。
```Java
class Solution {
    int nRows, nCols;
    public int numIslands(char[][] grid) {
        //BFS
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }
        int count = 0;
        this.nRows = grid.length;
        this.nCols = grid[0].length;
        boolean[][] visited = new boolean[nRows][nCols];
        for (int row = 0; row < nRows; row++) {
            for (int col = 0; col < nCols; col++) {
                if (grid[row][col] == '1' && visited[row][col] == false) {
                    count++;
                    bfs(row, col, grid, visited);
                }
            }
        }
        return count;
    }
    
    private void bfs(int row, int col, char[][] grid, boolean[][] visited) {
        int idx = row * nCols + col;
        Queue<Integer> queue = new LinkedList<>();
        queue.add(idx);
        visited[row][col] = true;
        int[][] directions = new int[][] {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        while (!queue.isEmpty()) {
            int curr = queue.poll();
            int r = curr / nCols;
            int c = curr % nCols;
            for (int[] direction : directions) {
                int newR = r + direction[0];
                int newC = c + direction[1];
                if (newR >= 0 && newR < nRows && newC >= 0 && newC < nCols && grid[newR][newC] == '1' && visited[newR][newC] == false) {
                    queue.offer(newR * nCols + newC);
                    visited[newR][newC] = true;
                }
            }
        }
    }
}
```

[Open the Lock](https://leetcode.com/explore/learn/card/queue-stack/231/practical-application-queue/1375/) <br>
思路：经典BFS题，需要首先明确state是什么，state直接的转化关系， 因为是求最近距离，所以考虑BFS。 从画出的图上一个状态到另一个状态看可以发现是n ary tree的 level order traversal， 只需要标记visited。
```Java
class Solution {
    // 经典BFS
    // 从图上看可以发现是n ary tree的 level order traversal， 只需要标记visited, 找到目标后返回当前层数就行了。。
    public int openLock(String[] deadends, String target) {
        String start = "0000";
        Set<String> visited = new HashSet<>();
        for (String str : deadends) {
            visited.add(str);
        }
        if (visited.contains("0000")) {
            return -1;
        }
        Queue<String> queue = new LinkedList<>();
        queue.offer(start);
        visited.add(start);
        int step = 0;
        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            for (int i = 0; i < levelSize; i++) {
                String curr = queue.poll();
                if (curr.equals(target)) {
                    return step;
                }
                String[] nextAll = create(curr);
                for (String next : nextAll) {
                    if (!visited.contains(next)) {
                        queue.offer(next);
                        visited.add(next);
                    }
                }
            }
            step++;
        }
        return -1;
    }
    
    private String[] create(String str) {
        String[] res = new String[8];
        int start = 0;
        for (int i = 0; i < 4; i++) {
            char[] curr = str.toCharArray();
            char currChar = curr[i];
            curr[i] = convert(currChar, true);
            res[start] = String.valueOf(curr);
            start++;
            curr[i] = convert(currChar, false);
            res[start] = String.valueOf(curr);
            start++;
        }
        return res;
    }
    
    private char convert(char chr, boolean isNext) {
        if  (chr == '9' && isNext) {
            return '0';
        }
        if (chr == '0' && !isNext) {
            return '9';
        }
        if (isNext) {
            return (char) (chr + 1);
        } else {
            return (char) (chr - 1);
        }
    }
}
```

[Perfect Squares](https://leetcode.com/problems/perfect-squares/) <br>
思路： bfs和回溯我都写了一下，这题动规也能解。。, 还是挺难的
```Java
class Solution {
    public int numSquares(int n) {
        //BFS 从 开始state 0 到达 target n 的最短路径
        int len = (int) Math.sqrt(n);
        int[] list = new int[len];
        for (int i = 0; i < len; i++) {
            list[i] = (i + 1) * (i + 1);
        }
        Queue<Integer> queue = new LinkedList<>();
        Set<Integer> set = new HashSet<>();
        queue.offer(0);
        set.add(0);
        int step = 0;
        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            for (int i = 0; i < levelSize; i++) {
                int curr = queue.poll();
                if (curr == n) {
                    return step;
                }
                for (int j = 0; j < len; j++) {
                    int next = curr + list[j];
                    if (!set.contains(next)) {
                        queue.offer(next);
                        set.add(next);
                    }
                }
            }
            step++;
        }
        return -1;
    }
}
```
