---
title: Leetcode - Univalued Binary Tree
date: 2019-11-07 07:30:17
tags:
- Algorithm
- Tree
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/PvHKKX1.png
---


## [965. Univalued Binary Tree](https://leetcode.com/problems/univalued-binary-tree/)


A binary tree is univalued if every node in the tree has the same value.

Return true if and only if the given tree is univalued.

Example 1:

<img src="https://i.imgur.com/DQ5vmXo.png" width="35%" />

```
Input: [1,1,1,1,1,null,1]
Output: true
```

<!-- more -->

Example 2:

<img src="https://i.imgur.com/Jdxc3YK.png" width="30%" />

```
Input: [2,2,2,5,2]
Output: false
```


## Solution

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
    bool isUnivalTree(TreeNode* root) {
        if(!root) return true;
        
        if(root->left && root->right) {
            return root->val == root->left->val 
                   && root->val == root->right->val 
                   && isUnivalTree(root->left) 
                   && isUnivalTree(root->right);
        }
        if(!root->left && root->right) return root->val == root->right->val 
                                              && isUnivalTree(root->right);
        if(root->left && !root->right) return root->val == root->left->val 
                                              && isUnivalTree(root->left);
        
        return true;
    }
};
```

## Complexity

$O(n)$