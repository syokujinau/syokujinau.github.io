---
title: Leetcode - Validate Binary Tree Nodes
date: 2020-01-20 11:31:16
tags:
- Algorithm
- Graph
- DFS
- Binary Tree
categories:
- [Algorithm, LeetCode]
thumbnail: https://syokujinau.github.io/myimages/lc1361-1.png
---

## [1361. Validate Binary Tree Nodes](https://leetcode.com/problems/validate-binary-tree-nodes/)


You have n binary tree nodes numbered from 0 to n - 1 where node i has two children leftChild[i] and rightChild[i], return true if and only if all the given nodes form exactly one valid binary tree.

If node i has no left child then leftChild[i] will equal -1, similarly for the right child.

Note that the nodes have no values and that we only use the node numbers in this problem.

 

Example 1:

<img src="https://assets.leetcode.com/uploads/2019/08/23/1503_ex1.png" height="80%">

```
Input: n = 4, leftChild = [1,-1,3,-1], rightChild = [2,-1,-1,-1]
Output: true
```

<!-- more -->

Example 2:

<img src="https://assets.leetcode.com/uploads/2019/08/23/1503_ex2.png" height="80%">

```
Input: n = 4, leftChild = [1,-1,3,-1], rightChild = [2,3,-1,-1]
Output: false
```

Example 3:

<img src="https://assets.leetcode.com/uploads/2019/08/23/1503_ex3.png" height="80%">

```
Input: n = 2, leftChild = [1,0], rightChild = [-1,-1]
Output: false
```


Example 4:

<img src="https://assets.leetcode.com/uploads/2019/08/23/1503_ex4.png" height="80%">

```
Input: n = 6, leftChild = [1,-1,-1,4,-1,-1], rightChild = [2,-1,-1,5,-1,-1]
Output: false
```

Constraints:

* 1 <= n <= 10^4
* leftChild.length == rightChild.length == n
* -1 <= leftChild[i], rightChild[i] <= n - 1





## Solution 

題目有n個node，編號0 ~ n-1，$v_0$的左右子點紀錄在leftChild[0]與rightChild[0]，題目傳入的參數相當於adjacency list，所以我將樹當作graph來看，用visit陣列紀錄拜訪次數，一個合法的二元樹(binary tree)每個node只能拜訪一次，若大於1次則返回false(驗證失敗)，如上面example 3，會讓dfs拜訪node無限次，大於1次即確認失敗沒有必要再搜尋下去了，最後一個for迴圈用來檢查非連通圖(disconnected graph)，這種情況也不是合法的二元樹。




```cpp
class Solution {
public:
    bool validateBinaryTreeNodes(int n, vector<int>& leftChild, vector<int>& rightChild) {
        vector<int> visit(n, 0);
        
        function<bool(int)> dfs = [&](int v) {
            if(v == -1) return true;
            
            visit[v]++;
            if(visit[v] > 1) return false;
            dfs(leftChild[v]);
            dfs(rightChild[v]);            
   
            return true;
        };
        
        if(!dfs(0)) return false;
        
        // for(int el:visit) cout << el << " "; cout << endl;
        
        for(int el : visit){
            if(el != 1) return false;
        }
        
        return true;
    }
};
```

## Complexity

$O(V+E)$