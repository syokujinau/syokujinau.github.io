---
title: Leetcode - Maximum Binary Tree
date: 2020-02-17 19:11:46
tags:
- Algorithm
- Tree
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/90Bep9s.png
---

## [654. Maximum Binary Tree](https://leetcode.com/problems/maximum-binary-tree/)

Given an integer array with **no duplicates**. A maximum tree building on this array is defined as follow:

The root is the maximum number in the array.
The left subtree is the maximum tree constructed from left part subarray divided by the maximum number.
The right subtree is the maximum tree constructed from right part subarray divided by the maximum number.
Construct the maximum tree by the given array and output the root node of this tree.

Example 1:

```
Input: [3,2,1,6,0,5]
Output: return the tree root node representing the following tree:

      6
    /   \
   3     5
    \    / 
     2  0   
       \
        1
```

<!-- more -->

## Solution

跟[108. Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)思路完全一樣，只差在108題是從左右子陣列中的mid為index，這題是找最大值所在的index。

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
    TreeNode* constructMaximumBinaryTree(vector<int>& nums) {        
        return trav(nums, 0, nums.size() - 1);
    }
    
    TreeNode* trav(vector<int>& nums, int l, int r) {
        if(l <= r) {
            int idx = idxMax(nums, l, r);
            TreeNode* root = new TreeNode(nums[idx]);
            root->left = trav(nums, l, idx - 1);
            root->right = trav(nums, idx + 1, r);
            return root;
        }
        
        return NULL;
    }
    
    int idxMax(vector<int>& nums, int l, int r) { 
        int Max = INT_MIN;
        for(int i = l; i <= r; i++) if (nums[i] > Max) Max = nums[i];
        for(int i = l; i <= r; i++) if (nums[i] == Max) return i;
        return -1;
    }
};
```