---
title: Leetcode - Binary Tree Level Order Traversal
date: 2019-10-17 15:01:46
tags:
- Algorithm
- Tree
- BFS
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/cfyFNNu.png
---


Tree traversal基本題


## 102. Binary Tree Level Order Traversal

Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).

For example:
Given binary tree `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

<!-- more -->
return its level order traversal as:

```
[
  [3],
  [9,20],
  [15,7]
]
```

## Solution 

就BFS

## Code

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        
        vector<vector<int>> ans;
        
        bfs(ans, root, 0);
        
        
        return ans;
    }
    
private:
    // unordered_map<int, vector<int>> um;
    
    void bfs(vector<vector<int>>& ans, TreeNode* root, int depth) {
        if(!root) return;
        

        // record
        // 檢查是否超過存取範圍
        if(ans.size() >  depth) { // 沒超過
            ans[depth].push_back(root->val);
        } else {                  // 超過範圍，新增一層
            vector<int> vec{root->val};
            ans.push_back(vec);
        }
      
        
        bfs(ans, root->left, depth + 1);
        bfs(ans, root->right, depth + 1);
    }
};
```




再補一題N-ary的版本

## 429. N-ary Tree Level Order Traversal

![](https://i.imgur.com/cfyFNNu.png)

```
Input: root = [1,null,3,2,4,null,5,6]
Output: [[1],[3,2,4],[5,6]]
```

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}

    Node(int _val) {
        val = _val;
    }

    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/
class Solution {
public:
    vector<vector<int>> levelOrder(Node* root) {
        
        vector<vector<int>> ans;
        
        bfs(ans, root, 0);
        
        return ans;
    }
    
    
    
    
    
    void bfs(vector<vector<int>>& ans, Node* root, int depth) {
        if(!root) return;
        
        
        if(ans.size() >  depth) {
            ans[depth].push_back(root->val);
        } else {
            vector<int> vec{root->val};
            ans.push_back(vec);
        }
        
        if(root->children.size() != 0) {
            for(auto child : root->children) {
                bfs(ans, child, depth + 1);
            }
        }
    }
};
```

## Time complexity

$O(n)$