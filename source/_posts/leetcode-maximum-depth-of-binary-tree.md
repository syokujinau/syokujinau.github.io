---
title: LeetCode - Maximum Depth of Binary Tree
date: 2019-12-16 12:26:31
tags:
- Algorithm
- Tree
- DFS
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/tzJxftO.jpg
---

## 104. Maximum Depth of Binary Tree

### Solution 
 
![](https://i.imgur.com/0RQQEU5.gif)


### Code

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        int d = 1, max_d = 0;
        preorder(root, d, max_d);
        return max_d;             
    }

    void preorder(TreeNode* root, int d, int& max_d) {
        if(!root) 
            return;
        max_d = max(d, max_d);
        if(root->left)  preorder(root->left, d+1, max_d);
        if(root->right) preorder(root->right, d+1, max_d);
        return;
    }
};
```
<!-- more -->

### Time Complexity

DFS: $O(V+E)$, $E = V - 1$

$\Rightarrow O(V)$ 