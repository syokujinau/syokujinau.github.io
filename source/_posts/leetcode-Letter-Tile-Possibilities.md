---
title: Leetcode - Letter Tile Possibilities
date: 2019-12-17 08:33:42
tags:
- Algorithm
- Backtracking
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/Cw3uETT.png
---

## [1079. Letter Tile Possibilities](https://leetcode.com/problems/letter-tile-possibilities/)

You have a set of `tiles`, where each tile has one letter `tiles[i]` printed on it.  Return the number of possible non-empty sequences of letters you can make.


Example 1:

```
Input: "AAB"
Output: 8
Explanation: The possible sequences are "A", "B", "AA", "AB", "BA", "AAB", "ABA", "BAA".
```


## Solution

<!-- more -->


permutation dfs搜尋所有可能，用一個hash table紀錄搜尋過程看過的排列

<img src="https://i.imgur.com/tKOHTeB.png" />

```cpp
class Solution {
public:
    int numTilePossibilities(string tiles) {
        
        unordered_set<string> us;
        string cur = "";
        vector<int> used(tiles.size());
        
        function<void(int)> dfs = [&] (int d) {
            // cout << cur << endl;
            us.insert(cur);
            
            for(int i = 0; i < tiles.size(); i++) {
                if(used[i]) continue;
                used[i] = 1;
                cur.push_back(tiles[i]);
                dfs(d + 1);
                cur.pop_back();
                used[i] = 0;
            }
        };
        
        dfs(0);
        
        return us.size() - 1; // exclude initial empty string
        
    }
};
```

已經紀錄過的排列就不用繼續遞迴搜尋了，如下圖的"A"與"BA"，可降低時間複雜度

<img src="https://i.imgur.com/dGYbCTf.png" />

```cpp
class Solution {
public:
    int numTilePossibilities(string tiles) {
        
        unordered_set<string> us;
        string cur = "";
        vector<int> used(tiles.size());
        
        function<void(int)> dfs = [&] (int d) {
            if(us.find(cur) != us.end()) return; // cut
            us.insert(cur);
            
            for(int i = 0; i < tiles.size(); i++) {
                if(used[i]) continue;
                used[i] = 1;
                cur.push_back(tiles[i]);
                dfs(d + 1);
                cur.pop_back();
                used[i] = 0;
            }
        };
        
        dfs(0);
        
        return us.size() - 1; // exclude initial empty string
        
    }
};
```


