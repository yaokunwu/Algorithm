# Leetcode卡片习题分析记录

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
