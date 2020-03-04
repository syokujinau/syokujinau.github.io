---
title: Leetcode - Binary Watch
date: 2019-12-22 19:30:20
tags:
- Algorithm
- Backtracking
- DFS
categories:
- [Algorithm, LeetCode]
thumbnail: https://upload.wikimedia.org/wikipedia/commons/8/8b/Binary_clock_samui_moon.jpg
---


## [401. Binary Watch](https://leetcode.com/problems/binary-watch/)


A binary watch has 4 LEDs on the top which represent the hours (0-11), and the 6 LEDs on the bottom represent the minutes (0-59).

Each LED represents a zero or one, with the least significant bit on the right.


For example, the above binary watch reads "3:25".

Given a non-negative integer n which represents the number of LEDs that are currently on, return all possible times the watch could represent.

Example:

<img src="https://upload.wikimedia.org/wikipedia/commons/8/8b/Binary_clock_samui_moon.jpg" width="80%">

```
Input: n = 1
Return: ["1:00", "2:00", "4:00", "8:00", "0:01", "0:02", "0:04", "0:08", "0:16", "0:32"]
```

<!-- more -->

## Solution

用dfs產生10位數的binary number字串，第一與第二個參數是可用的"1"與"0"的數量，decode將字串轉成整數(decode("101") = 5)，isVaild檢查時間是否正確。

```cpp
class Solution {
public:
    vector<string> readBinaryWatch(int num) {
            
        dfs(num, 10 - num, "");
        
        for(string s : permutations) {
            string hour = s.substr(0, 4);
            string minute = s.substr(4);
            if(!isVaild(hour, minute)) continue;
            int h = decode(hour);
            int m = decode(minute);
            string time = to_string(h) + ":";
            if (m < 10) time += "0"; 
            time += to_string(m);
            ans.push_back(time);
        }
        
        return ans;
    }
    
    vector<string> ans;
    vector<string> permutations;
    
    void dfs(int n1, int n0, string cur) {
        if(n1 == 0 && n0 == 0) {
            permutations.push_back(cur);
            return;
        }

        if(n1) {
            dfs(n1 - 1, n0    , cur + "1");
        } 
        if(n0) {
            dfs(n1    , n0 - 1, cur + "0");
        }
    };
    
    bool isVaild(string hour, string minute) {
        if(decode(hour) > 11 || decode(minute) > 59) return false;
        return true;
    }
    
    int decode(string s) {
        int len = s.length();
        if(len == 0) return 0;
        return (s[0] - '0') * pow(2, len - 1) + decode(s.substr(1));
    }
};
```