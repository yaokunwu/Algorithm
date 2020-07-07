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
思路： (Iterative) 遍历一遍列表，把当前值与之前值比较，重复的删除
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
思路： (Recursive) 遍历一遍列表，把当前值与之前值比较，重复的删除 <br>
这个recursion写的有问题 <br>
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
思路： 链表头节点可能被删除， 考虑增加dummy node 辅助删除 <br>

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

思路： 和上面差不多, 第m次循环找到起始反转点，然后相应调整就行 <br>

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

[Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists) <br>

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
[Partition List](https://leetcode.com/problems/partition-list) <br>

思路：直接创建两个新的链表，遍历原链表，小于给定值的放在一个链表，大于的放在另一个链表 <br>

```Java
class Solution {
    public ListNode partition(ListNode head, int x) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode first = new ListNode(-1);
        ListNode second = new ListNode(-1);
        ListNode firstHead = first;
        ListNode secondHead = second;
        while (head != null) {
            if (head.val < x) {
                first.next = new ListNode(head.val);
                first = first.next;
            } else {
                second.next = new ListNode(head.val);
                second = second.next;
            }
            head = head.next;
        }
        first.next = secondHead.next;
        return firstHead.next;
    }
}
```

[Sort List](https://leetcode.com/problems/sort-list) <br>

思路： 归并排序 (拆分为两个问题，1. 快慢指针，2. 合并两个排好序的linked list) <br>

```Java
class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode fast = head.next;
        ListNode slow = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        ListNode secondHalf = slow.next;
        slow.next = null;
        ListNode firstHalf = head;
        ListNode left = sortList(firstHalf);
        ListNode right = sortList(secondHalf);
        return merge(left, right);
    }
    
    private ListNode merge(ListNode first, ListNode second) { 
        if (first == null) { 
            return second;
        }
        if (second == null) {
            return first;
        }
        if (first.val < second.val) {
            first.next = merge(first.next, second);
            return first;
        } else {
            second.next = merge(first, second.next);
            return second;
        }
    }
}
```
注意点<br>
* 快慢指针的起始指向问题， 需要偶数中间项前一个，需要把fast指向head.next， 如果需要偶数中间项后一个，fast指向head <br>
* 递归merge需要断开中间点

[Reorder List](https://leetcode.com/problems/reorder-list) <br>

思路： 快慢指针的题 (拆分为reverse list， merge list), fast = head.next表明前半段list个数大于等于后半段list个数 <br>

```Java
class Solution {
    private ListNode reverse(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
    
    public void reorderList(ListNode head) {
        if (head == null || head.next == null) {
          return;
        }
        ListNode fast = head.next;
        ListNode slow = head;
        while (fast != null && fast.next != null) {
          slow = slow.next;
          fast = fast.next.next;
        }
        ListNode secondHalf = slow.next;
        slow.next = null;
        ListNode reversed = reverse(secondHalf);
        ListNode curr = head;
        while (reversed != null) {
            ListNode next = reversed.next;
            reversed.next = curr.next;
            curr.next = reversed;
            curr = reversed.next;
            reversed = next;     
        }
    }
}
```
[Linked List Cycle](https://leetcode.com/problems/linked-list-cycle) <br>

思路：快慢指针直接循环 <br>

```Java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) {
            return false;
        }
        ListNode fast = head.next;
        ListNode slow = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) {
                return true;
            }
        }
        return false;
    }
}
```
[Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii) <br>

思路： 先确定cycle长度， 再找起始node. (注意后面双指针同速) <br>

```Java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null || head.next == null) {
            return null;
        }
        ListNode fast = head;
        ListNode slow = head;
        int cycleLen = 0;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) {
                do {
                    slow = slow.next;
                    cycleLen++;
                } while (fast != slow);
                break;
            }
        }
        if (cycleLen == 0) {
            return null;
        }
        
        ListNode pointer1 = head;
        ListNode pointer2 = head;
        for(int i = 0; i < cycleLen; i++) {
            pointer2 = pointer2.next;
        }
        
        while (pointer1 != pointer2) {
            pointer1 = pointer1.next;
            pointer2 = pointer2.next;
        }
        return pointer2;
    }
}
```

更简单的思路（原理不清楚， 要记住才行）： 第一次相遇后把slow指针指向head， 改变两指针速度同为1，下一次相遇的地方即为循环起始点 <br>

```Java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null || head.next == null) {
            return null;
        }
        ListNode fast = head.next;
        ListNode slow = head;
    
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) {
                slow = head;
                fast = fast.next;
                while (fast != slow) {
                    fast = fast.next;
                    slow = slow.next;
                }
                return slow;
            }
           
        }
        return null;
    }
}
```
Note: 一般用fast == head.next 比较多， 因为这样可以知道中点的上一个节点，可以用来删除等操作 <br>

[Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list) <br>

思路： reverse半个list， 然后比较就行 <br>

```Java
class Solution {
    private ListNode reverse(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            ListNode next = curr.next;
            curr.next = prev; 
            prev = curr;
            curr = next;
        }
        return prev;
    }
    private boolean compare(ListNode first, ListNode second) {
        while (second != null) {
            if (first.val != second.val) {
                return false;
            }
            first = first.next;
            second = second.next;
        }
        return true;
    }
    public boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) {
            return true;
        }
        ListNode fast = head.next;
        ListNode slow = head;
        
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        ListNode second = slow.next;
        slow.next = null;
        ListNode reversed = reverse(second);
        
        return compare(head, reversed);
    }
}
```
[Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer) <br>

思路： 新建nodes， 交错，拷贝random pointer， 反交错 <br>

```Java
class Solution {
    public Node copyRandomList(Node head) {
        // null condition check
        if (head == null) {
            return head;
        }
        
        // weave nodes creation
        Node curr = head;
        while (curr != null) {
            Node next = curr.next;
            Node newNode = new Node(curr.val);
            newNode.next = next;
            curr.next = newNode;
            curr = next;
        }
        
        // weave nodes completion
        curr = head;
        while (curr != null) {
            Node copyNode = curr.next;
            if (curr.random != null) {
                copyNode.random = curr.random.next;
            }
            curr = curr.next.next;
        }
        // deweave
        curr = head;
        Node newNode = head.next;
        while (curr != null) {
            Node newN = curr.next;
            curr.next = newN.next;
            if (curr.next != null) {
                newN.next = curr.next.next;
            } 
            curr = curr.next;
        }
        return newNode;
    }
}
```

## Conclusion
链表必须要掌握的一些点：<br>

* null/nil exception handling
* dummy node use case
* fast slow pointers
* insert a node to a sorted list
* remove a node from a sorted list
* reverse a list
* merge two list
* find the middle node of a list
