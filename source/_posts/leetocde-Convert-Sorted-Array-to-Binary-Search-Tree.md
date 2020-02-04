---
title: Leetcode - Convert Sorted Array to Binary Search Tree
date: 2019-12-20 18:23:31
tags:
- Algorithm
- BFS
- Tree
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/UfACFnW.png
---

## [108. Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)

Given an array where elements are sorted in ascending order, convert it to a height balanced BST.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

Example:

```
Given the sorted array: [-10,-3,0,5,9],

One possible answer is: [0,-3,9,-10,null,5], which represents the following height balanced BST:

      0
     / \
   -3   9
   /   /
 -10  5
 ```

 <!-- more -->

 ## Solution

因為nums是排序好 要balanced就拿中間當root 
會切分出左右子陣列
遞迴處理成左右子樹

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
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return bfs(nums, 0, nums.size() - 1);
    }
    
    TreeNode* bfs(vector<int>& nums, int left, int right) {
        if(left > right) return NULL;
        
        int mid = left + (right - left) / 2;
        TreeNode* pNode = new TreeNode(nums[mid]);
        
        
        pNode->left  = bfs(nums, left, mid - 1);
        pNode->right = bfs(nums, mid + 1, right);
        
        return pNode;
    }
};
```

