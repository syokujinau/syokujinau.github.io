---
title: Leetcode - Sum of Nodes with Even-Valued Grandparent
date: 2020-02-03 16:51:06
tags:
- Algorithm
- Tree
- DFS
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/36bECB4.png
---


## [1315. Sum of Nodes with Even-Valued Grandparent](https://leetcode.com/problems/sum-of-nodes-with-even-valued-grandparent/)

Given a binary tree, return the sum of values of nodes with even-valued grandparent.  (A grandparent of a node is the parent of its parent, if it exists.)

If there are no nodes with an even-valued grandparent, return 0.

 

Example 1:


<img src="https://i.imgur.com/i8Sc406.png" width="66%">

```
Input: root = [6,7,8,2,7,1,3,9,null,1,4,null,null,null,5]
Output: 18
Explanation: The red nodes are the nodes with even-value grandparent while the blue nodes are the even-value grandparents.
```

<!-- more -->

Constraints:

* The number of nodes in the tree is between 1 and 10^4.
* The value of nodes is between 1 and 100.


## Solution


典型的樹狀結構traversal，過程中若root是偶數就求sum of grandchild，遞迴求解。

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
    int sumEvenGrandparent(TreeNode* root) {
        if(!root) return 0;
        if(root->val % 2 == 0) return addGrandchild(root) + sumEvenGrandparent(root->left) + sumEvenGrandparent(root->right);
        else                   return sumEvenGrandparent(root->left) + sumEvenGrandparent(root->right);
        
    }
    
    int addGrandchild(TreeNode* root) {
        int sum = 0;
        if(root->left) {
            if(root->left->left)  sum += root->left->left->val;
            if(root->left->right) sum += root->left->right->val;
        } 
        if(root->right) {
            if(root->right->left)  sum += root->right->left->val;
            if(root->right->right) sum += root->right->right->val;
        }
        return sum;
    }
};
```