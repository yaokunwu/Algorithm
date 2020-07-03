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
* * *
          

















        
