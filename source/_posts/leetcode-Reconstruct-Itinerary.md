---
title: Leetcode - Reconstruct Itinerary
date: 2020-02-16 17:31:16
tags:
- Algorithm
- Graph
- DFS
- Multiset
- Hash Table
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/kPirER2.png
---


## [332. Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary/)

Given a list of airline tickets represented by pairs of departure and arrival airports [from, to], reconstruct the itinerary in order. All of the tickets belong to a man who departs from JFK. Thus, the itinerary must begin with JFK.

Note:

* If there are multiple valid itineraries, you should return the itinerary that has the smallest lexical order when read as a single string. For example, the itinerary ["JFK", "LGA"] has a smaller lexical order than ["JFK", "LGB"].
* All airports are represented by three capital letters (IATA code).
* You may assume all tickets form at least one valid itinerary.

Example 1:

```
Input: [["MUC", "LHR"], ["JFK", "MUC"], ["SFO", "SJC"], ["LHR", "SFO"]]
Output: ["JFK", "MUC", "LHR", "SFO", "SJC"]
```

Example 2:

```
Input: [["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
Output: ["JFK","ATL","JFK","SFO","ATL","SFO"]
Explanation: Another possible reconstruction is ["JFK","SFO","ATL","JFK","ATL","SFO"].
             But it is larger in lexical order.
```

<!-- more -->

## Solution

視為有向圖(directed graph)，因為可能有重複的機票，在圖上形成multiple edges，又必須照字母順序(lexical order)安排行程，所以adjacency list以hash table與multiset(二元樹，插入後即排序完成)等資料結構儲存graph，題目表明一定有解，畫出一個解可以發現以postorder的DFS訪問每個機場再反序就是答案。 複雜度為$O(V+E)$

```cpp
class Solution {
public:
  vector<string> findItinerary(vector<vector<string>>& tickets) {
      
      for(auto ticket : tickets) {
          adj[ticket[0]].insert(ticket[1]);
      }
      
      // for(auto& [key, val] : adj) {
      //   cout << key << " - ";
      //   for(auto el : val) cout << el << " "; cout << endl;
      // }
      
      dfs("JFK");

      reverse(ans.begin(), ans.end());
      
      return ans;
  }

private:
    
  unordered_map<string, multiset<string>> adj;
  vector<string> ans;

  void dfs(string v) {
    while(!adj[v].empty()) {
      string u = *(adj[v].begin()); // choose the first in lexocal order
      adj[v].erase(adj[v].begin()); // delete the edge
      dfs(u);
    }
    // postorder
    ans.push_back(v);
  };
    
};

```