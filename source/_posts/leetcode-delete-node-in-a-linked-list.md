---
title: LeetCode - Delete Node in a Linked List
date: 2019-12-13 16:26:11
tags:
- Algorithm
- Linked-list
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/QXgmni1.png
---


據說是微軟面試題...算是腦筋急轉吧，事實上沒有真的delete node而是直接skip該node。

## 237. Delete Node in a Linked List

Write a function to delete a node (except the tail) in a singly linked list, given only access to that node.

Given linked list -- head = [4,5,1,9], which looks like following:

<img src="https://i.imgur.com/97WJDXP.png" width="45%" />

Example 1:

```
Input: head = [4,5,1,9], node = 5
Output: [4,1,9]
Explanation: You are given the second node with value 5, the linked list should become 4 -> 1 -> 9 after calling your function.
```

Example 2:

```
Input: head = [4,5,1,9], node = 1
Output: [4,5,9]
Explanation: You are given the third node with value 1, the linked list should become 4 -> 5 -> 9 after calling your function.
```

Note:

* The linked list will have at least two elements.
* All of the nodes' values will be unique.
* The given node will not be the tail and it will always be a valid node of the linked list.
* Do not return anything from your function.

<!-- more -->

## Code 

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
    void deleteNode(ListNode* node) {
        node->val = node->next->val;
        node->next = node->next->next;
    }
};
```

## Time complexity

$O(1)$