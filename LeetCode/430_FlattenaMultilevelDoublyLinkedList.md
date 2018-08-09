---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 430. Flatten a Multilevel Doubly Linked List

> <https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/description/>

## Description

You are given a doubly linked list which in addition to the next and previous pointers, it could have a child pointer, which may or may not point to a separate doubly linked list. These child lists may have one or more children of their own, and so on, to produce a multilevel data structure, as shown in the example below.

Flatten the list so that all the nodes appear in a single-level, doubly linked list. You are given the head of the first level of the list.

**Example**:
**Input**:
```
 1---2---3---4---5---6--NULL
         |
         7---8---9---10--NULL
             |
             11--12--NULL
```
**Output**: `1-2-3-7-8-11-12-9-10-4-5-6-NULL`

**Explanation for the above example**:

Given the following multilevel doubly linked list:
![](/LeetCode/MultilevelLinkedList.png)

We should return the following flattened doubly linked list:
![](/LeetCode/MultilevelLinkedListFlattened.png)

## Solution

```python{class=line-numbers}
"""
# Definition for a Node.
class Node(object):
    def __init__(self, val, prev, next, child):
        self.val = val
        self.prev = prev
        self.next = next
        self.child = child
"""
class Solution(object):
    def recursive_f(self, first):
        p = ret_tail = first
        while p:
            if p.child:
                head, tail = self.recursive_f(p.child)
                p.child = None
                tail.next = p.next
                if p.next:
                    p.next.prev = tail
                p.next = head
                head.prev = p
                ret_tail = tail
                p = tail.next
            else:
                ret_tail = p
                p = p.next
        return first, ret_tail
    def flatten(self, head):
        """
        :type head: Node
        :rtype: Node
        """
        ret, _ = self.recursive_f(head)
        return ret
```