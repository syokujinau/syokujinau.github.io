---
title: Leetcode - Merge Two Sorted Lists
date: 2019-09-24 22:06:21
tags:
- Algorithm
- Linked-list
- Backtracking
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/Yyv9Kk5.png
---

## [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

Example:

```
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4
```



<!-- more -->

## Method 1 - Recursive

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if(l1 == NULL && l2 == NULL) return NULL;
        if(l1 == NULL) return l2;
        if(l2 == NULL) return l1;
        
        ListNode* l3 = NULL;
        
        if(l1->val <= l2->val) {
            l3 = new ListNode(l1->val);
            l3->next = mergeTwoLists(l1->next, l2);
        } else {
            l3 = new ListNode(l2->val);
            l3->next = mergeTwoLists(l1, l2->next);
        }
        
        return l3;
    }
};
```


## Method 2 - Iterative

雖然題目說要產生new list，但是這個方法還是可以過

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {        
        ListNode l3(0);
        ListNode* ptr3 = &l3;
        
        
        while(l1 && l2) {
            if(l1->val < l2->val) {
                ptr3->next = l1;
                l1 = l1->next;
            } else {
                ptr3->next = l2;
                l2 = l2->next;
            }
            ptr3 = ptr3->next;
        }
        
        ptr3->next = (l1) ? l1 : l2;
        
        return l3.next;
    }
};
```