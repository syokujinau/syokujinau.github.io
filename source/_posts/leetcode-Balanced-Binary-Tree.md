---
title: Leetcode - Balanced Binary Tree
date: 2019-11-17 15:01:46
tags:
- Algorithm
- Tree
- DFS
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/iFDgSvF.jpg
---

## [110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)

Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as:

> a binary tree in which the left and right subtrees of every node differ in height by no more than 1.



Example 1:

```
Given the following tree [3,9,20,null,null,15,7]:

    3
   / \
  9  20
    /  \
   15   7
Return true.
```

<!-- more -->

## Solution

平衡二元樹定義：左右子樹高度差小於等於1且左右子樹皆為平衡二元樹

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
    bool isBalanced(TreeNode* root) {
        if(!root) return true;
        return abs(hight(root->left) - hight(root->right)) <= 1 &&
    		   isBalanced(root->left) &&
    		   isBalanced(root->right);
    }
    
    int hight(TreeNode* root) {
        if(!root) return 0;
        return max(hight(root->left), hight(root->right)) + 1;
    }
};
```
 