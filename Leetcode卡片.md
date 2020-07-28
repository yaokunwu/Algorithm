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
回溯：
```Java
class Solution {
    int sum = 0; 
    int count = 0;
    boolean found = false;
    int[] list;
    int len;
    public int numSquares(int n) {
        //构造选择列表
        len = (int) Math.sqrt(n);
        list = new int[len];
        for (int i = 0; i < len; i++) {
            list[i] = (i + 1) * (i + 1);
        }
        
        while (true) { // 从最小的组合个数开始循环
            backtracking(n, 0, null);
            if (found) { // 找到了就返回当前所需的组合的个数，因为从0开始所以能保证找到时组合的个数最小
                return count;
            }
            count++; //没找到就把组合的个数加1
        }
    }
    
    private void backtracking(int n, int size, Integer prev) {
        //递归出口
        if (size == count) {  //达到了设置的组合个数
            if (sum == n) {  //找到了， 返回
                found = true;
                return;
            } else { //没找到， 返回
                return;
            }
        }

        //从选择列表进行选择
        for (int i = 0; i < len; i++) { 
            // 剪枝，比如需要找组合个数为2. 第一次DFS找了1 ,4 那下一次循环到4的时候就不用找1了， prev记录上一层所选择的元素
            if (prev != null && list[i] < prev) { 
                continue;
            }
            //选择这个值
            sum += list[i];
            //进入下一层
            backtracking(n, size + 1, list[i]);
            //回溯
            sum -= list[i];
        }
    }
}
```
上面两道题总结一下，BFS的规律：
起始就是树和图的level order traversal，要注意state得如何定义的。
第一题的backtracking不好做因为没有固定的选择列表。 而且要注意，第一题不能用backtracking因为是不一定能得到target的，由于不清楚循环次数并及时终止，可能会陷入死循环。


dfs
[Target Sum](https://leetcode.com/explore/learn/card/queue-stack/232/practical-application-stack/1389/) <br>
思路： 1.回溯，
```Java
class Solution {
    // backtracking
    int res;  // 结果
    int remain; // 回溯需要一个全局状态。
    public int findTargetSumWays(int[] nums, int S) {
        res = 0;
        remain = S;
        backtracking(nums, 0);
        return res;
    }
    
    private void backtracking(int[] nums, int level) {
        if (level == nums.length) {  // 回溯终止条件
            if (remain == 0) {
                res++;
            } 
            return;
        } 
        //因为只有两个选择所以没写for循环，当选择很多时，改为for循环（也就是n-ary preorder traversal）
        remain -= nums[level]; // 选择当前值
        backtracking(nums, level + 1); // 进入下一层
        remain += nums[level]; //回溯， 因为引入了全局状态

        remain += nums[level]; //选择另一条路
        backtracking(nums, level + 1); // 进入下一层
        remain -= nums[level]; // 回溯
    }
}
```
思路2： preorder traversal
class Solution {
    // preorder traversal
    int res;  // 结果
    public int findTargetSumWays(int[] nums, int S) {
        res = 0;
        preOrder(nums, 0, S);
        return res;
    }
    
    private void preOrder(int[] nums, int level, int remain) {
        if (level == nums.length) {  // base case
            if (remain == 0) {
                res++;
            } 
            return;
        }
        
        // //简写为
//         preOrder(nums, level + 1, remain - nums[level]);  
//         //相当于先定义 int other1 = remain - nums[level], 然后call preOrder(nums, level + 1, other1);
        
//         preOrder(nums, level + 1, remain + nums[level]);
//         //同理相当于先定义 int other2 = remain - nums[level], 然后call preOrder(nums, level + 1, other2);
        
        //可以看下面代码做更深理解 （与上等价）
        remain -= nums[level];   // 做减法
        preOrder(nums, level + 1, remain); // 进入下一层，返回后不需要回溯
        
        remain += nums[level];  // 还原当前局部变量，不属于回溯
        
        remain += nums[level]; // 做加法
        preOrder(nums, level + 1, remain); // 进入下一层， 返回后不需要回溯
    }
}
思路3： postorder traversal
```Java
class Solution {
    // postorder traversal
    public int findTargetSumWays(int[] nums, int S) {
        return postOrder(nums, 0, S);
    }
    
    private int postOrder(int[] nums, int level, int remain) {
        if (level == nums.length) {  // base case
            if (remain == 0) {
                return 1;
            } 
            return 0;
        }
        
        int left = postOrder(nums, level + 1, remain + nums[level]);
        int right = postOrder(nums, level + 1, remain - nums[level]);
        
        return left + right;
    }
}
```
思路4： postorder traversal + 备忘录 （自顶向下动规）,考虑到每层和remain都有返回值，可以直接加备忘录
```Java
class Solution {
    // postorder traversal + memo
    Map<String, Integer> map;
    public int findTargetSumWays(int[] nums, int S) {
        map = new HashMap<>();
        return postOrder(nums, 0, S);
    }

    private int postOrder(int[] nums, int level, int remain) {
        if (level == nums.length) {  // base case
            if (remain == 0) {
                return 1;
            } 
            return 0;
        }
        String key = level + "," + remain;
        if (map.containsKey(key)) {
            return map.get(key);
        }
        
        int left = postOrder(nums, level + 1, remain + nums[level]);
        int right = postOrder(nums, level + 1, remain - nums[level]);
        map.put(key, left + right);
        
        return map.get(key);
    }
}
```
思路5： BFS
class Solution {
    //BFS
    public int findTargetSumWays(int[] nums, int S) {
        Queue<Integer> queue = new LinkedList<>();
        queue.add(S);
        int level = 0;
        int count = 0;
        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            for (int i = 0; i < levelSize; i++) {
                int remain = queue.poll();
                if (level == nums.length) {
                    if (remain == 0) {
                        count++;
                    }
                } else {
                    queue.offer(remain - nums[level]);
                    queue.offer(remain + nums[level]);
                }
            }
            level++;
        }
        return count;
    }
}
超时了
思路5： 动规 bottom up

backtracking也是dfs
**TO-DO 一会看一下两种递归树**
总结一下 dfs, backtracking, preorder traversal, postorder traversal, dp, 之间的关系。。。
首先 preorder traversal和postorder traversal 都属于 dfs （也有说postorder 属于分治，都无所谓)
backtracking的实质dfs中的preorder traversal， 是无法添加memorization的，也就无法变为自顶向下动态规划。
而有些时候（部分题中），preorder traversal可以转化为postorder traversal或者只能用postroder解决并且有重复子问题，这样递归中的参数可以添加到memorization中，所以可以将backtracking改为动规。
dp的自顶向下+memo和自底而上效果相同，可以相互转化。
还有状态和f（x）的问题


## backtracking
Backtracking实际上如果没有剪枝的话就跟bruteforce search一样了。所以只有有剪枝操作时才叫backtracking <br>
The backtracking way of solving this problem would stop going down a path when the path doesn't seem right. When we say the path doesn't seem right we mean we come across a node which will never lead to the right result. As we come across such node we back-track. That is go back to the previous node and take the next step.


[Sudoku Solver]() <br>
```Java
class Solution {
    Set<Integer>[][] sets; // char也行
    List<int[]> levels;
    char[][] board;
    boolean solved = false;
    public void solveSudoku(char[][] board) {
        this.board = board;
        sets = new HashSet[3][9];
        for (int row = 0; row < 3; row++) {
            for (int col = 0; col < 9; col++) {
                sets[row][col] = new HashSet<>();
            }
        }
        levels = new ArrayList<>();
        for (int row = 0; row < 9; row++) {
            for (int col = 0; col < 9; col++) {
                int diaIdx = (row / 3) * 3 + col / 3;
                if (board[row][col] != '.') {
                    int currValue = board[row][col] - '0';
                    placeCurr(currValue, row, col);
                } else {
                    levels.add(new int[] {row, col});
                }
            }
        }
        backtracking(0);
    }
    
    private void placeCurr(int value, int row, int col) {
        int diaIdx = (row / 3) * 3 + col / 3;
        sets[0][row].add(value);
        sets[1][col].add(value);
        sets[2][diaIdx].add(value);
        board[row][col] = (char) (value + '0');
    }
    
    private void removeCurr(int value, int row, int col) {
        int diaIdx = (row / 3) * 3 + col / 3;
        sets[0][row].remove(value);
        sets[1][col].remove(value);
        sets[2][diaIdx].remove(value);
        board[row][col] = '.';
    }
    
    private void backtracking(int level) {
        if (level == levels.size()) {
            solved = true;
            return;
        }
        for (int i = 0; i < 9; i++) {
            int[] idxex = levels.get(level);
            int row = idxex[0];
            int col = idxex[1];
            if (checking(i + 1, row, col)) {
                placeCurr(i + 1, row, col);
                backtracking(level + 1);
                if (solved == true) {
                    return;
                }
                removeCurr(i + 1, row, col);
            }
        }
    }
    
    private boolean checking(int value, int row, int col) {
        int diaIdx = (row / 3) * 3 + col / 3;
        if (sets[0][row].contains(value) || sets[1][col].contains(value) || sets[2][diaIdx].contains(value)) {
            return false;
        }
        return true;
    }
}
```
**注意** 
1. 从int 转 char 用 char chr = (char) (int + '0');
2. Character.getNumericValue(int)
3. 如果全局变量是需要返回的，那么在找到solution后需要checking and return。 否则会继续回溯把全局变量修改。

### Recursion 转化 iteration
[Same Tree](https://leetcode.com/problems/same-tree/) <br>
```Java
class Solution {
    
    private boolean check(TreeNode p, TreeNode q) {
        if (p == null && q == null) {
            return true;
        }
        if (p == null || q == null) {
            return false;
        }
        if (p.val != q.val) {
            return false;
        }
        return true;
    }
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (!check(p, q)) { return false; }
        Deque<TreeNode> stackP = new LinkedList<>();
        Deque<TreeNode> stackQ = new LinkedList<>();
        stackP.add(p);
        stackQ.add(q);
        while (!stackP.isEmpty() && !stackQ.isEmpty()) {
            TreeNode currP = stackP.poll();
            TreeNode currQ = stackQ.poll();
            if (!check(currP, currQ)) { return false; }
            if (currP != null) {
                stackP.add(currP.left);
                stackP.add(currP.right);
            }
            if (currQ != null) {
                stackQ.add(currQ.left);
                stackQ.add(currQ.right);
            }
        }
        if (stackP.isEmpty() && stackQ.isEmpty()) {
            return true;
        }
        return false;
    }
}
```
**注意： 因为有两棵树，所以需要两个stack来分别保存遍历顺序。 而且判断与遍历顺序无关，可以用(queue) BFS。**
附上正统后序递归解法
```Java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        // Base case
        if (p == null && q == null) {
            return true;
        }
        if (p == null || q == null) {
            return false;
        }
        // left
        boolean left = isSameTree(p.left, q.left);
        // right
        boolean right = isSameTree(p.right, q.right);
        // combine
        return left && right && p.val == q.val;
    }
}
```

我的分治思想的理解还是稍微薄弱了点，还没法达到自己看到题目就知道子问题怎么划分（类比为从二叉树的当前节点怎么分为去后面的节点），需要做分治类的抽象题目来提高分治思想。 比如说下面这道题最优解为o（n）的单调栈解法，但实际上当没有遇到过这问题的时候，分治法才是标准解答nlogn。<br>
问题的关键在于， 怎么把问题规模减小，怎么合并，关于规模的参数怎么设计，这些都要想的。
[Largest Rectangle in Histogram](https://leetcode.com/explore/learn/card/recursion-ii/507/beyond-recursion/2901/) <br>
```Java
//面试官难道想看你这解法？？ 想多了吧
class Solution {
    public int largestRectangleArea(int[] heights) {
        int[] newHeights = new int[heights.length + 2];
        System.arraycopy(heights, 0, newHeights, 1, heights.length);
        Deque<Integer> stack = new LinkedList<>();
        stack.push(0);
        int maxArea = 0;
        for (int i = 1; i < newHeights.length; i++) {
            while (newHeights[i] < newHeights[stack.peek()]) {
                int currIdx = stack.pop();
                int currHeight = newHeights[currIdx];
                maxArea = Math.max(maxArea, currHeight * (i - stack.peek() - 1));
            }
            stack.push(i);
        }
        return maxArea;
    }
}
```
常规解法(分治）<br>
在做题没有思路的时候（面试时），把问题规模缩小化，用分治思想才是应该首先考虑的，尽管对于这道题分治不是最优解，但对于在面试中展现自己思考解决问题的能力时，用分治把这题做对我相信一定没有问题。 而单调栈有可能在短时间内想不到或者说做的题不够多的话是没有办法想到的。
```Java
class Solution {
    public int largestRectangleArea(int[] heights) {
        return dfs(heights, 0, heights.length - 1);
    }
    //虽然是先处理了当前节点，但是还是bottom up postorder traversal。
    private int dfs(int[] heights, int left, int right) {
        if (left > right) {
            return 0;
        }
        int minIdx = left;
        for (int i = left + 1; i < right; i++) {
            if (heights[i] < heights[minIdx]) {
                minIdx = i;
            }
        }
        int leftMax = dfs(heights, left, minIdx - 1);
        int rightMax = dfs(heights, minIdx + 1, right);
        
        int maxArea = heights[minIdx] * (right - left + 1);
        maxArea = Math.max(maxArea, leftMax);
        maxArea = Math.max(maxArea, rightMax);
        return maxArea;
    }
}
```

然后再说一下，既然postorder 有返回值，分治也就是postorder，所以是有状态函数的，当状态有重复时，可以通过加备忘录来解决。或转化为动规下上。<br>
以下观点验证了我的逻辑。 考虑问题首先想分治，分治有重复子问题了加备忘录转化为动规上下。
1、  分治算法和动态规划算法都是通过对问题进行分解，通过对子问题的求解然后进行解重构，从而实现对原问题的求解。请指出这两种算法在对问题进行分解时各自所遵循的原则。 <br>

答：分治算法对问题进行分解时所遵循的原则是将待求解问题分解为若干个规模较小、相互独立且与原问题相同的子问题（不包含公共的子问题). <br>
动态规划对问题进行分解时所遵循的原则是将待求解问题分解为若干个规模较小、相互关联的与原问题类似的子问题（包含公共的子问题），采用记录表的方法来保存所有已解决问题的答案，而在需要的时候再找出已求得的答案，避免大量的重复计算。<br>
