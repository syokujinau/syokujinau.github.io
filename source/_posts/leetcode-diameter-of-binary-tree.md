---
title: Leetcode - Diameter of Binary Tree
date: 2019-11-24 11:36:47
tags:
- Algorithm
- Tree
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/vYQljRI.png
---

Tree資料結構的操作與postorder traversal

## 543. Diameter of Binary Tree

Given a binary tree, you need to compute the length of the diameter of the tree. The diameter of a binary tree is the length of the longest path between any two nodes in a tree. This path may or may not pass through the root.

Example:
```
Given a binary tree
          1
         / \
        2   3
       / \     
      4   5    
```

## Solution

postorder traversal會訪問所有tree node，所以一定會經過左右子樹高度最長的root(如下圖node 2)，構成題目定義的直徑，在traversal過程一定會經過此點，若有更長的直徑則更新diameter的值


<img src="https://i.imgur.com/RkN7cMD.png" width="50%" />

<!-- more -->

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
    int diameterOfBinaryTree(TreeNode* root) {
        
        int diameter = 0;
        postorder(root, diameter);
        
        return diameter;
    }
    
    int postorder(TreeNode* root, int& diameter) { // LRD: postorder traversal
        if(!root) return 0;
        
        int lh = postorder(root->left, diameter);
        int rh = postorder(root->right, diameter);
        
        diameter = max(diameter, lh + rh); // update max 
        
        return max(lh, rh) + 1;
    }
};
```

## Time complexity

$O(n)$