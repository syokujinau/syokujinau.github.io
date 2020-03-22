---
title: Leetcode - Construct Binary Tree from Preorder and Inorder Traversal
date: 2020-03-07 18:19:06
tags:
- Algorithm
- Tree
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/IwyHHN1.jpg
---

## [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)


Given preorder and inorder traversal of a tree, construct the binary tree.

Note:
You may assume that duplicates do not exist in the tree.

For example, given

```
preorder = [3,9,20,15,7]
inorder = [9,3,15,20,7]
```

Return the following binary tree:

```
    3
   / \
  9  20
    /  \
   15   7
```

<!-- more -->

## Solution

postorder的左邊界$pl$必定為root，用pos[preorder[pl]]可在$O(1)$知道root在inorder中的index，再用inorder的範圍$il$、$ir$來推算左右子樹的size

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
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        unordered_map<int, int> pos;
 
        for (int i = 0; i < inorder.size(); i++)
          pos[inorder[i]] = i;
        
        function<TreeNode*(int, int, int, int)> buildBT = [&](int pl, int pr, int il, int ir) {
            // 3 [9] [20 15 7]
            // [9] 3 [15 20 7]
            if(pl > pr) return (TreeNode*)NULL;
            //
            TreeNode* root = new TreeNode(preorder[pl]);
            
            //
            int im = pos[preorder[pl]]; // find the index of element in inorder array
            int pm = pl + (im - il); // the index of last element in postorder array  
            
            root->left  = buildBT(pl + 1, pm, il, im - 1);
            root->right = buildBT(pm + 1, pr, im + 1, ir);
            
            return root;
        };
        
        return buildBT(0, preorder.size() - 1, 0, inorder.size() - 1);
    }
};
```