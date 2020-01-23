---
title: Algorithm Daily - Generate Parentheses
date: 2019-12-16 11:36:47
tags:
- Algorithm
- Backtracking
- DFS
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/hivsaxA.png
---

> 排列組合問題常用的DFS解法
> 加一個"("，未來就要用一個")"來構成一組合法括號
> 紀錄可用的"("與")"數量
> 試著畫出這棵二元樹就清楚了

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