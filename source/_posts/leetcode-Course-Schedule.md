---
title: Leetcode - Course Schedule
date: 2020-01-26 17:01:16
tags:
- Algorithm
- Graph
- DFS
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/tmLhNJE.png
---

## [207. Course Schedule](https://leetcode.com/problems/course-schedule/)


There are a total of n courses you have to take, labeled from 0 to n-1.

Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: [0,1]

Given the total number of courses and a list of prerequisite pairs, is it possible for you to finish all courses?

Example 1:

```
Input: 2, [[1,0]] 
Output: true
Explanation: There are a total of 2 courses to take. 
             To take course 1 you should have finished course 0. So it is possible.
```

<!-- more -->

## Solution

$n$課程，每堂視為node $v_i$, $i = 0$ to $n-1$
我將題目當作有向圖的**cycle detection**問題，拓樸排序也是一樣架構
* $visited[i]$代表$v_i$被拜訪的狀態
	* 0: 未拜訪
	* 1: 拜訪中
	* 2: 已拜訪
* 設一個flag紀錄cycle是否被發現
	* 發現條件：拜訪visiting (`visited[i] == 1`)的node
* DFS搜尋$v_0 ~ v_1$, 如下*Line 35*，每個node都要做dfs才能處理非連通的graph
* *Line 21* 反註解就是拓樸排序


![](https://i.imgur.com/9iF4Y1n.png)

```cpp
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        
        unordered_map<int, vector<int>> adj;
        
        // O(E)
        for(auto e : prerequisites) // adjacency list
            adj[e[1]].push_back(e[0]);
        
        ////
        // for(auto [key, val] : adj) {
        //     cout << key << " -> [";
        //     for(int i : val) cout << i << " "; cout << "]" << endl;
        // }

        vector<int> visited(numCourses); // 0-unknown, 1-visiting, 2-visited
        bool cycleDetected = false;
        
        function<void(int)> dfs = [&](int node) {
            // cout << node << endl;
            if(cycleDetected) return;
            if(visited[node] == 1) {
                cycleDetected = true;
            }
            if(visited[node] == 2) return;
            visited[node] = 1;
            for(int v : adj[node]) {
                dfs(v);
            }
            visited[node] = 2;
        };
        
        // O(V+E)        
        for(auto [node, list] : adj) {
           if(visited[node] != 2 && !cycleDetected) dfs(node);
        }

        return !cycleDetected;
    }
};
```

## Complexity

有向圖的dfs，且graph用adjacency list表示，複雜度$O(V + E)$
