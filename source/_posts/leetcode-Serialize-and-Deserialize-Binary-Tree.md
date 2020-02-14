---
title: Leetcode - Serialize and Deserialize Binary Tree
date: 2020-01-17 15:01:46
tags:
- Algorithm
- Tree
- String
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/U6acQmO.jpg
---


## [297. Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)

Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary tree can be serialized to a string and this string can be deserialized to the original tree structure.



<!-- more -->

## Solution

```
    1
   / \
  2   3
     / \
    4   5
```

用preorder(DLR)轉成序列會是"12XX34XX5XX", "X"代表`NULL`

* 這題沒有規定轉成的string要長怎樣，只要能decode回原樹即可
* 二元樹狀結構preorder的序列，如果沒有紀錄`NULL`就必須搭配inorder才能確定唯一的二元樹
* 最簡潔是用string stream，轉成序列時用空白分隔nodes，方便再decode的時候讀入node


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
class Codec {
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        ostringstream out;
        preorder(root, out);
        return out.str();
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        istringstream in(data);
        return decode(in);
    }

private:
    void preorder(TreeNode* root, ostringstream& out) {
        if(!root) {
            out << "X ";
            return;
        }
        
        out << root->val << " ";
        preorder(root->left, out);
        preorder(root->right, out);
    }
    
    TreeNode* decode(istringstream& in) {
        string node;
        in >> node;
        if(node == "X") return NULL;
        TreeNode* root = new TreeNode(stoi(node));
        root->left = decode(in);
        root->right = decode(in);
        return root;
    }
};

// Your Codec object will be instantiated and called as such:
// Codec codec;
// codec.deserialize(codec.serialize(root));
```