---
title: Leetcode - Binary Search Tree to Greater Sum Tree
date: 2020-02-07 20:15:16
tags:
- Algorithm
- Tree
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/Mtmfvds.png
---

## [1038. Binary Search Tree to Greater Sum Tree](https://leetcode.com/problems/binary-search-tree-to-greater-sum-tree/)


Given the root of a binary search tree with distinct values, modify it so that every node has a new value equal to the sum of the values of the original tree that are greater than or equal to node.val.

As a reminder, a binary search tree is a tree that satisfies these constraints:

The left subtree of a node contains only nodes with keys less than the node's key.
The right subtree of a node contains only nodes with keys greater than the node's key.
Both the left and right subtrees must also be binary search trees.
 

Example 1:

<img src="https://assets.leetcode.com/uploads/2019/05/02/tree.png" width="62%">

```
Input: [4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
Output: [30,36,21,36,35,26,15,null,null,null,33,null,null,null,8]
```

Note:

1. The number of nodes in the tree is between 1 and 100.
2. Each node will have value between 0 and 100.
3. The given tree is a binary search tree.

<!-- more -->

## Solution

以RDL順序traverse二元樹，用accu紀錄累加值，並更新node的值。

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
    TreeNode* bstToGst(TreeNode* root) {
        int accu = 0;
        
        function<void(TreeNode*)> traverse = [&](TreeNode* root) { // RDL
            if(!root) return;
            traverse(root->right);
            accu += root->val;
            root->val = accu;
            traverse(root->left);            
        };
        
        traverse(root);
        return root; 
    }  
};
```

## Time complexity

$O(n)$