---
title: Leetcode - Two Sum IV - Input is a BST
date: 2020-01-15 12:01:46
tags:
- Algorithm
- Tree
- Binary Search
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/GFPryLX.jpg
---

## [653. Two Sum IV - Input is a BST](https://leetcode.com/problems/two-sum-iv-input-is-a-bst/)

Given a Binary Search Tree and a target number, return true if there exist two elements in the BST such that their sum is equal to the given target.

Example 1:

```
Input: 
    5
   / \
  3   6
 / \   \
2   4   7

Target = 9

Output: True
```

<!-- more -->

## Solution

* 注意因為一個和為k的pair可能同時在tree的左或右子樹，所以不能直接在tree結構搜尋，需要轉成陣列(原Two Sum Problem) 
* 想用binary search來尋找pair，將BST轉成sorted序列，因此使用inorder traversal         

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
    bool findTarget(TreeNode* root, int k) {
        vector<int> nums;
        
        function<void(TreeNode*)> inorder = [&nums, &inorder](TreeNode* root) {
            if(!root) return;
            inorder(root->left);
            nums.push_back(root->val);
            inorder(root->right);
        };
        
        inorder(root); // O(n)
        
        function<bool(int, int, int)> binarySearch = [&](int l, int r, int t) {
            if(l <= r) {
                int mid = (l + r) / 2;
                if(nums[mid] == t) return true;
                if(nums[mid] > t)  return binarySearch(l, mid - 1, t);
                else               return binarySearch(mid + 1, r, t);
            }
            return false;
        };
        
        for(int i = 0; i < nums.size(); ++i) { // O(nlogn)
            if(binarySearch(i + 1, nums.size() - 1, k - nums[i])) return true;
        }
        
        
        return false;
    }
};
```

### Time complexity

$O(nlogn)$