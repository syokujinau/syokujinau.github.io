---
title: Leetcode - Maximum Level Sum of a Binary Tree
date: 2019-12-14 00:33:21
tags:
- Algorithm
- Tree
- DFS
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/iJi4aVv.png
---

## [1161. Maximum Level Sum of a Binary Tree](https://leetcode.com/problems/maximum-level-sum-of-a-binary-tree/)

Given the root of a binary tree, the level of its root is 1, the level of its children is 2, and so on.

Return the **smallest** level X such that the sum of all the values of nodes at level X is **maximal**.

Example 1:

<img src="https://i.imgur.com/ndFAR2v.png" width="35%" />

```
Input: [1,7,0,7,-8,null,null]
Output: 2
Explanation: 
Level 1 sum = 1.
Level 2 sum = 7 + 0 = 7.
Level 3 sum = 7 + -8 = -1.
So we return the level with the maximum sum which is level 2.
```

<!-- more -->

## Solution

* 用hash以DFS紀錄每層的和，key為層數，value為該層之和
* $O(h)$找level sum最大值
* $O(h)$找擁有level sum最大值且level最小的 （可能有多個level之和都是max）

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
    
    unordered_map<int, int> um; // depth, sum
    
    int maxLevelSum(TreeNode* root) {
        
        dfs(root, 0); // O(n)
        
        // get the maximum sum 
        int max = INT_MIN;
        
        for(auto [key, val] : um) { // O(n)
            if(val > max) max = val;
        }
        
        // get the smallest level which has maximum sum
        int min_idx = INT_MAX;

        for(auto [key, val] : um) { // O(n)
            if(val == max && key < min_idx) min_idx = key;
        }
        
        return min_idx + 1; // level start from 1
    }
    
    void dfs(TreeNode* root, int d) {
        if(!root) return;
        um[d] += root->val;
        dfs(root->left, d + 1);
        dfs(root->right, d + 1);
    }
};
```

## Complexity

$O(n)$