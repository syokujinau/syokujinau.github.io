---
title: Leetcode - Construct Binary Search Tree from Preorder Traversal
date: 2020-02-27 15:15:16
tags:
- Algorithm
- Tree
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/CXwHeX7.png
---


## [1008. Construct Binary Search Tree from Preorder Traversal](https://leetcode.com/problems/construct-binary-search-tree-from-preorder-traversal/)

Return the root node of a binary search tree that matches the given preorder traversal.

(Recall that a binary search tree is a binary tree where for every node, any descendant of node.left has a value < node.val, and any descendant of node.right has a value > node.val.  Also recall that a preorder traversal displays the value of the node first, then traverses node.left, then traverses node.right.)

 

Example 1:

<img src="https://assets.leetcode.com/uploads/2019/03/06/1266.png" width="52%" />

```
Input: [8,5,1,7,10,12]
Output: [8,5,10,1,7,null,12]
```


<!-- more -->

## Solution


preorder順序為"DLR"，每個陣列範圍$l ~ r$中，第一個一定是root，第二個一定是左子樹的root，令$rRootIdx$指向下一個比root大的index，也就是右子樹root所在，遞迴將樹建立起來。

<img src="/myimages/20200227.png" width="35%" />

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
    TreeNode* bstFromPreorder(vector<int>& preorder) {
        const int n = preorder.size();
        return buildBST(preorder, 0, n - 1);
    }
    TreeNode* buildBST(vector<int>& preorder, int l, int r) {
        if(l > r) return NULL;
        
        TreeNode* root = new TreeNode(preorder[l]);
        // find the root index of right subtree
        int rRootIdx = l + 1; 
        while(rRootIdx <= r && preorder[rRootIdx] < root->val) rRootIdx++;
        // Left
        root->left = buildBST(preorder, l + 1, rRootIdx - 1);
        // Right
        root->right = buildBST(preorder, rRootIdx, r);
        
        return root;
    } 
};
```