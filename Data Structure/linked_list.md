# Linked List <br>
## Basic skill <br>
### Key points related to linked list <br>
* null/nil Exception handling <br>
* dummy node <br>
* Fast slow pointers
* Insert a node to a sorted linked list
* Remove a node from a linked list
* Reverse linked list
* Merge linked list
* Middle node of a linked list

## Common type of problems related to linked list
[Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list) <br>
思路： (Iterative) 遍历一遍列表，把当前值与之前值比较，重复的删除。
```Java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            if (prev != null && curr.val == prev.val) {
                prev.next = curr.next;
                curr = curr.next;
                continue;
            }
            prev = curr;
            curr = curr.next;
        }
        return head;
    }
}
```
思路： (Recursive) 遍历一遍列表，把当前值与之前值比较，重复的删除。
这个recursion写的有问题
```Java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode prev = head;
        ListNode curr = head == null? null: head.next;
        if (prev == null || curr == null) {
            return prev;
        }
        prev.next = deleteDuplicates(head.next);
        if (prev.val == curr.val) {
            return curr;
        }
        return prev;
    }
}
```

[Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii) <br>
思路： 链表头节点可能被删除， 考虑增加dummy node 辅助删除
```Java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) {  // 空List或者只有一个元素，啥都不用干
            return head;
        }
        ListNode dummyNode = new ListNode(0);
        dummyNode.next = head;
        ListNode curr = head.next;
        ListNode first = dummyNode;
        while (curr != null) {
            if (first.next.val != curr.val) {
                curr = curr.next;
                first = first.next;
            } else {
                while (curr != null && curr.val == first.next.val) {
                    curr = curr.next;
                }
                
                first.next = curr;
                if (curr != null) {
                    curr = curr.next;
                }
            }
                
        }
        return dummyNode.next;
    }
}
```
[Reverse Linked List](https://leetcode.com/problems/reverse-linked-list) <br>
思路： iteration
```Java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode prev = null;
        ListNode curr = head;
        while (curr!= null) {
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;  // 这里别忘了改
        }
        return prev;  //return的值是prev
    }
}
```

[Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii) <br>
思路： 和上面差不多, 第m次循环找到起始反转点，然后相应调整就行
```Java
class Solution {
    public ListNode reverseBetween(ListNode head, int m, int n) {
        if (head.next == null || m == n) {
            return head;
        }
        ListNode prev = null;
        ListNode curr = head;
        for (int i = 0; curr != null && i < m - 1; i++) {  // 别忘了curr != null这个条件
            prev = curr;
            curr = curr.next;
        }
        
        ListNode currLastNode = curr;
        ListNode prevLastNode = prev;
        
        for (int i = m; i < n + 1; i++) {  //注意第一次循环相当于只初始化了头节点的next为null， 还没有开始reverse， 所以要n + 1.  
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        
        if (prevLastNode != null) {
            prevLastNode.next = prev;
        } else {
            head = prev;
        }
        currLastNode.next = curr;
    }
    return head;
}
```

[Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists)
思路： using recursion 或者 heap都行
```Java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        } 
        if (l2 == null) {
            return l1;
        }
        
        if (l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
}
```
[Partition List](https://leetcode.com/problems/partition-list)
思路： 














