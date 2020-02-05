---
title: leetcode Maximum Product of Splitted Binary Tree
date: 2020-01-09 16:22:37
tags:
- Algorithm
- Tree
- DP
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/4FeMPNb.png
---



## [1339. Maximum Product of Splitted Binary Tree](https://leetcode.com/problems/maximum-product-of-splitted-binary-tree/)

Given a binary tree `root`. Split the binary tree into two subtrees by removing 1 edge such that the product of the sums of the subtrees are maximized.

Since the answer may be too large, return it modulo 10^9 + 7.

Example 1:

<img src="https://i.imgur.com/TmSXwF1.png" />

```
Input: root = [1,2,3,4,5,6]
Output: 110
Explanation: Remove the red edge and get 2 binary trees with sum 11 and 10. Their product is 110 (11*10)
```

<!-- more -->

## Solution

先求出整棵樹的和$sumT$
postorder traverse過程,計算切分後的左右乘積,更新最大值

有2種切法
case 1: $prodect = sumL * (sumT - sumL)$
case 2: $prodect = (sumT - sumR) * sumR$

<img src="https://i.imgur.com/IghJPCX.png" width="30%" />



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
    
    long ans;
    long sumT; // summation of whole tree
    
    int maxProduct(TreeNode* root) {
        // init
        const int modv = 1e9 + 7; 
        ans = 0;
        sumT = sum(root);
        
        // update maximum
        postorder(root);
        
        return ans % modv;
    }
    
    long sum(TreeNode* root) { // postorder
        if(!root) return 0;
        return root->val + sum(root->left) + sum(root->right);
    }
    
    int postorder(TreeNode* root) {
        if(!root) return 0;
        
        long sumL = postorder(root->left);
        long sumR = postorder(root->right);
        // 
        ans = max({ans,                     
                   sumL * (sumT - sumL),   // case 1
                   (sumT - sumR) * sumR}); // case 2

        
        return root->val + sumL + sumR;
    }
};
```