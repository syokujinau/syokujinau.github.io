---
title: Leetcode - Count Number of Teams
date: 2020-03-30 11:21:16
tags:
- Algorithm
- Backtracking
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/EJBV6OV.jpg
---

## [1395. Count Number of Teams](https://leetcode.com/problems/count-number-of-teams/)

There are n soldiers standing in a line. Each soldier is assigned a unique rating value.

You have to form a team of 3 soldiers amongst them under the following rules:

* Choose 3 soldiers with index (i, j, k) with rating (rating[i], rating[j], rating[k]).
* A team is valid if:  (rating[i] < rating[j] < rating[k]) or (rating[i] > rating[j] > rating[k]) where (0 <= i < j < k < n).

Return the number of teams you can form given the conditions. (soldiers can be part of multiple teams).

 

Example 1:

```
Input: rating = [2,5,3,4,1]
Output: 3
Explanation: We can form three teams given the conditions. (2,3,4), (5,4,1), (5,3,1). 
```
<!-- more -->
Example 2:

```
Input: rating = [2,1,3]
Output: 0
Explanation: We can't form any team given the conditions.
```

Example 3:

```
Input: rating = [1,2,3,4]
Output: 4
```

Constraints:

* n == rating.length
* 1 <= n <= 200
* 1 <= rating[i] <= 10^5

## Solution 

$O(n^3)$

```cpp
class Solution {
public:
    int numTeams(vector<int>& rating) {
        
        int cnt = 0;
        
        for(int i = 0; i < rating.size(); ++i) {
            for(int j = i + 1; j < rating.size(); ++j) {
                for(int k = j + 1; k < rating.size(); ++k) {
                    if(rating[i] < rating[j] && rating [j] < rating[k] || rating[i] > rating[j] && rating [j] > rating[k])
                        cnt++;
                }
            }
        }
        
        return cnt;
    }
};
```

## Solution - Backtracking

$O(2^n)$

```cpp
class Solution {
public:
    int numTeams(vector<int>& rating) {
        
        vector<int> cur;
        vector<bool> used(rating.size(), false);
        int cnt = 0;
        
        function<void(int, int)> dfs = [&](int d, int s) {
            if(d == 3) {
                // for(int el : cur) cout << el << " "; cout << endl;
                if(cur[0] < cur[1] && cur[1] < cur[2]) cnt++;
                if(cur[0] > cur[1] && cur[1] > cur[2]) cnt++;
                return;
            }
            
            for(int i = s; i < rating.size(); ++i) {
                if(used[i]) continue;
                used[i] = true;
                cur.push_back(rating[i]);
                dfs(d + 1, i + 1);
                cur.pop_back();
                used[i] = false;
            }
        };
        
        dfs(0, 0);
        
        return cnt;
    }
};
```