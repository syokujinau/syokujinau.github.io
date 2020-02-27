---
title: Leetcode - Cells with Odd Values in a Matrix
date: 2020-02-07 20:25:40
tags:
- Algorithm
- Matrix
- Array
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/7N29eaf.png
---



## [1252. Cells with Odd Values in a Matrix](https://leetcode.com/problems/cells-with-odd-values-in-a-matrix/)


Given n and m which are the dimensions of a matrix initialized by zeros and given an array indices where indices[i] = [ri, ci]. For each pair of [ri, ci] you have to increment all cells in row ri and column ci by 1.

Return the number of cells with odd values in the matrix after applying the increment to all indices.

 

Example 1:

<img src="https://assets.leetcode.com/uploads/2019/10/30/e1.png" width="70%" />

```
Input: n = 2, m = 3, indices = [[0,1],[1,1]]
Output: 6
Explanation: Initial matrix = [[0,0,0],[0,0,0]].
After applying first increment it becomes [[1,2,1],[0,1,0]].
The final matrix will be [[1,3,1],[1,3,1]] which contains 6 odd numbers.
```

<!-- more -->

## Solution

按照題目要求將指定的row、column加1，有個小技巧是計算奇數時，假設某數是x，若(x & 0x1)為0代表x是偶數，反之為奇數。

```cpp
class Solution {
public:
    int oddCells(int n, int m, vector<vector<int>>& indices) {

        vector<vector<int>> mat(n, vector<int>(m));
        
        for(auto el : indices) {
            addRow(el[0], mat);
            addCol(el[1], mat);
        }        
               
        // for(auto row : mat) {
        //     for(int col : row) cout << col << " "; cout << endl;
        // }
        
        int countOdd = 0;
        
        for(auto row : mat) {
            for(int col : row) 
            	if(col & 0x1) countOdd++;
        }
        
        return countOdd;

    }
    
    void addRow(int row, vector<vector<int>>& mat) {
        for(int& el : mat[row]) el++;
    }
    
    void addCol(int col, vector<vector<int>>& mat) {
        for(auto& row : mat) row[col]++;
    }
};
```