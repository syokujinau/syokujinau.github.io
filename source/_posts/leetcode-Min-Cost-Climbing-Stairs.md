---
title: Leetcode - Min Cost Climbing Stairs
date: 2020-03-11 07:30:20
tags:
- Algorithm
- Dynamic Programming
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/6cJL2Tu.jpg
---


## [746. Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/)

On a staircase, the i-th step has some non-negative cost cost[i] assigned (0 indexed).

Once you pay the cost, you can either climb one or two steps. You need to find minimum cost to reach the top of the floor, and you can either start from the step with index 0, or the step with index 1.

Example 1:

```
Input: cost = [10, 15, 20]
Output: 15
Explanation: Cheapest is start on cost[1], pay that cost and go to the top.
```

Example 2:

```
Input: cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]
Output: 6
Explanation: Cheapest is start on cost[0], and only step on 1s, skipping cost[3].
```

<!-- more -->

## Solution 1

### <span style="color:red;">[TLE]</span> Recursion 

$recur(i)$ := min cost to reach i-th stair. The answer is $recur(n)$. 

Time complexity: $O(2^n)$

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        const int n = cost.size();
        
        // recur(i) := min cost to reach i-th stair.
        function<int(int)> recur = [&](int n) {
            if(n < 2) return 0;
            return min(recur(n - 1) + cost[n - 1], recur(n - 2) + cost[n - 2]);
        };
        
        return recur(n);
    }
};
```

### DP (Memoization)

Time complexity: $O(n)$

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        const int n = cost.size();
        
        vector<int> memo(n + 1, -1); // -1 stands for the answer of subproblem does not exist.
        
        function<int(int)> recur = [&](int n) {
            if(n < 2) return 0;
            if(memo[n] != -1) return memo[n];
            else {
                // update
                return memo[n] = min(recur(n - 1) + cost[n - 1], recur(n - 2) + cost[n - 2]);
            }
        };
        
        return recur(n);
    }
};
```

### DP (Tabulation) 

Time complexity: $O(n)$

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        const int n = cost.size();
        
        // dp[i] := min cost to reach i-th stair.
        vector<int> dp(n + 1, INT_MAX);
        
        dp[0] = 0;
        dp[1] = 0;
        
        for(int i = 2; i <= n; ++i) {
            dp[i] = min(dp[i - 1] + cost[i - 1], dp[i - 2] + cost[i - 2]);
        }
            
        return dp.back();
    }
};
```


## Solution 2

$recur(i)$ := min cost to leave i-th stair. The answer is $recur(n)$. 

### <span style="color:red;">[TLE]</span> Recursion

Time complexity: $O(2^n)$

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        const int n = cost.size();
        
        // recur(i) := min cost to leave i-th stair. 
        function<int(int)> recur = [&](int n) {
            if(n < 2) return cost[n];
            return min(recur(n - 1), recur(n - 2)) + cost[n];
        };
        
        return min(recur(n - 1), recur(n - 2));
    }
};
```

### DP (Memoization)

Time complexity: $O(n)$

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        const int n = cost.size();
        
        vector<int> memo(n, -1);
        
        // recur(i) := min cost to leave i-th stair. 
        function<int(int)> recur = [&](int n) {
            if(n < 2) return cost[n];
            if(memo[n] != -1) return memo[n];
            else {
                // update
                return memo[n] = min(recur(n - 1), recur(n - 2)) + cost[n];
            }            
        };
        
        return min(recur(n - 1), recur(n - 2)); // O(2n)
    }
};
```

### DP (Tabulation) 

Time complexity: $O(n)$

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        const int n = cost.size();
        
        // dp[i] := min cost to leave i-th stair.
        vector<int> dp(n, INT_MAX);
        
        dp[0] = cost[0];
        dp[1] = cost[1];
        
        for(int i = 2; i < n; ++i) {
            dp[i] = min(dp[i - 1], dp[i - 2]) + cost[i];
        }
            
        return min(dp[n - 1], dp[n - 2]);
    }
};
```