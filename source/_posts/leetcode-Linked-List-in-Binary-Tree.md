---
title: Leetcode - Linked List in Binary Tree
date: 2020-02-29 12:36:47
tags:
- Algorithm
- Tree
- Linked-list
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/KGSCzsL.png
---

## [1367. Linked List in Binary Tree](https://leetcode.com/problems/linked-list-in-binary-tree/)

Given a binary tree root and a linked list with head as the first node. 

Return True if all the elements in the linked list starting from the head correspond to some downward path connected in the binary tree otherwise return False.

In this context downward path means a path that starts at some node and goes downwards.

Example 1:

<img src="https://assets.leetcode.com/uploads/2020/02/12/sample_1_1720.png" width="45%" />

```
Input: head = [4,2,8], root = [1,4,4,null,2,2,null,1,null,6,8,null,null,null,null,1,3]
Output: true
Explanation: Nodes in blue form a subpath in the binary Tree.  
```

Example 2:

<img src="https://assets.leetcode.com/uploads/2020/02/12/sample_2_1720.png" width="45%" />

```
Input: head = [1,4,2,6], root = [1,4,4,null,2,2,null,1,null,6,8,null,null,null,null,1,3]
Output: true
```

<!-- more -->

Example 3:

```
Input: head = [1,4,2,6,8], root = [1,4,4,null,2,2,null,1,null,6,8,null,null,null,null,1,3]
Output: false
Explanation: There is no path in the binary tree that contains all the elements of the linked list from head.
```


## Solution

match函式檢查鏈結串列是否配對以root為首的二元樹，遞迴檢查。

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isSubPath(ListNode* head, TreeNode* root) {
        if(!root) return false;
        return match(head, root) || isSubPath(head, root->left) || isSubPath(head, root->right);
    }
    
    bool match(ListNode* head, TreeNode* root) {
        if(!head) return true;
        if(!root) return false; 
        return head->val == root->val && (match(head->next, root->left) || match(head->next, root->right)); 
    }
};
```