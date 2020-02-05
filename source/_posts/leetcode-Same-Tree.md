---
title: Leetcode - Same Tree
date: 2019-12-25 12:22:31
tags:
- Algorithm
- Tree
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/iW3HqoU.png
---

基本的tree結構操作
另外還有[101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree/), [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/), [450. Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/), [872. Leaf-Similar Trees](https://leetcode.com/problems/leaf-similar-trees/), etc.


## [100. Same Tree](https://leetcode.com/problems/same-tree/)

Given two binary trees, write a function to check if they are the same or not.

Two binary trees are considered the same if they are structurally identical and the nodes have the same value.

Example 1:

```
Input:     1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]
Output: true
```



<!-- more -->

Example 2:

```
Input:     1         1
          /           \
         2             2

        [1,2],     [1,null,2]
Output: false
```


Example 3:

```
Input:     1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

Output: false
```



## Code

```cpp
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
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if(p == NULL && q == NULL) 
            return true;
        
        else if(p != NULL && q != NULL) {
            return p->val == q->val && 
                   isSameTree(p->left, q->left) && 
                   isSameTree(p->right, q->right);
        }
        
        else 
            return false;    
        
    }
};
```