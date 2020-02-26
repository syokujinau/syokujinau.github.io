---
title: Leetcode - Is Graph Bipartite?
date: 2019-12-16 07:31:16
tags:
- Algorithm
- Graph
- DFS
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/f5re2OQ.jpg
---

## [785. Is Graph Bipartite?](https://leetcode.com/problems/is-graph-bipartite/)

Given an **undirected graph**, return true if and only if it is bipartite.

Recall that a graph is bipartite if we can split it's set of nodes into two independent subsets A and B such that every edge in the graph has one node in A and another node in B.

The graph is given in the following form: graph[i] is a list of indexes j for which the edge between nodes i and j exists.  Each node is an integer between 0 and graph.length - 1.  There are no self edges or parallel edges: graph[i] does not contain i, and it doesn't contain any element twice.

Example 1:

```
Input: [[1,3], [0,2], [1,3], [0,2]]
Output: true
Explanation: 
The graph looks like this:
0----1
|    |
|    |
3----2
We can divide the vertices into two groups: {0, 2} and {1, 3}.
```

<!-- more -->

## Solution - DFS

graph是以adjacency list表示無向圖，以一個flag(`isBip`)代表其為bipartite(二分圖)，其中visit紀錄是否拜訪過，每個node相鄰node必須是不同顏色。
* 0: 未拜訪
* 1: 紅色
* 2: 藍色

<img src="https://syokujinau.github.io/myimages/20191216.png" width="80%" />

```cpp
class Solution {
public:
    bool isBipartite(vector<vector<int>>& graph) {
    
        vector<int> visit(graph.size(), 0); // 0-unknown, mark 1, 2
        
        int isBip = true;

        function<void(int, int)> dfs = [&](int prevMark, int node) {

            if(visit[node] != 0) {
                if(prevMark == visit[node]) isBip = false;
                return;
            }
            // mark           
            if(prevMark == 0) visit[node] = 1;
            else              visit[node] = (prevMark == 1)? 2 : 1;
            
            for(int u : graph[node]) {
                dfs(visit[node], u);
            }
        };

        for(int v = 0; v < graph.size(); ++v) {
            if(visit[v] == 0) dfs(0, v);
        }
        
        return isBip;

    }
};
```

不用flag的簡化寫法，在發現此圖非二分圖時就會中止執行，執行效率較好。

```cpp
class Solution {
public:
    bool isBipartite(vector<vector<int>>& graph) {
    
        vector<int> visit(graph.size(), 0); // 0-unknown, mark 1, 2
        

        function<bool(int, int)> isBipartite = [&](int prevMark, int node) {

            if(visit[node] != 0) { // node has visited
                // if the color of this node is the same as previous node, then it's NOT bipartite
                return !(visit[node] == prevMark); 
            }
            // mark           
            if(prevMark == 0) visit[node] = 1;
            else              visit[node] = (prevMark == 1)? 2 : 1;
            
            for(int u : graph[node]) {
                if(!isBipartite(visit[node], u)) return false;
            }
            
            return true;
        
        };

        for(int v = 0; v < graph.size(); ++v) {
            if(visit[v] == 0 && !isBipartite(0, v)) return false;
        }
        
        return true;

    }
};
```


## Solution - BFS

```cpp
// TBD
```


## Note

DFS, BFS pseudo code 整理，用相鄰串列表示graph

DFS: 
*GeneralDFS*可以處理非連通的狀況

```
visited[n] = {0}

dfs(v) {
    visited[v] = 1
    for u in adj[v]
        if(!visited[u])
            dfs(u)
}

GeneralDFS() {
    for v = 1 to n 
        if(!visited[v])
            dfs(v)
}
```

BFS:

```
visited[n] = {0}
queue Q

bfs(v) {
    visited[v] = 1
    Q.enqueue(v)
    while(!Q.empty()) {
        u = Q.dequeue()
        for k in adj[u]
            if(!visited[k])
                visited[k] = 1
                Q.enqueue(k)
    }
}
```