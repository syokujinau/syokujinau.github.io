---
title: LeetCode - Reverse Linked List
date: 2019-12-13 22:26:31
tags:
- Algorithm
- Linked-list
- Backtracking
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/wlOQ9yw.jpg
---

有兩種方法，iteration & recursion

## 206. Reverse Linked List

```
Example:

Input: 1->2->3->4->5->NULL
Output: 5->4->3->2->1->NULL
```



### Method 1 - recursion

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
    ListNode* reverseList(ListNode* head) {
        if(head == NULL || head->next == NULL)
            return head;
        
        ListNode* ptr = head->next;
        head->next = NULL;
        
        ListNode* head2 = reverseList(ptr);
        
        ptr->next = head;
        
        return head2;
    }
};
```
<!-- more -->

### Method 2 - iteration

```cpp
class Solution {
public:
    
    ListNode* reverseList(ListNode* head) {

        ListNode* head2 = NULL;

        for(ListNode* itr = head; itr != NULL; itr = itr->next) // O(n)
            insert(head2, itr->val); // add to head

        return head2;
        
    }


    void insert(ListNode*& head, int val) { // add to head
        ListNode* newNode = new ListNode(val);
        newNode->next = head;
        head = newNode;
    }

};
```

## Time complexity

$O(n)$

