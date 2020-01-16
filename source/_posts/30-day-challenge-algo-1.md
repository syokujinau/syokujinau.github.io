---
title: 30-Day Challenge 1 - Generate Parentheses
date: 2020-01-16 11:36:47
tags:
- Algorithm
- Backtracking
- DFS
categories:
- [Algorithm, 30-Day Challenge]
thumbnail: https://i.imgur.com/IpgtwV7.png
---


## LeetCode - 22. Generate Parentheses

Given $n$ pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

For example, given $n = 3$, a solution set is:

```
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]
```
<!-- more -->

## Solution

DFS(s, open, close)

$s$: current string
$open$: number of left parentheses can be appended to current string 
$close$: number of right parentheses can be appended to current string 
An answer is generated when $open = close = 0$ (base case)

## Code

```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        
        dfs("", n, 0);
        
        return ans;
    }
    
    vector<string> ans;
    
    void dfs(string s, int open, int close) {
        if(open == 0 && close == 0) {
            ans.push_back(s);
        }
        
        if(open)  dfs(s + "(", open - 1, close + 1);
        if(close) dfs(s + ")", open    , close - 1);
    }
};
```

## Time complexity

Huh...