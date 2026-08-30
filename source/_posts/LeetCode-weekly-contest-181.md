---
title: LeetCode Weekly Contest 181
date: 2020-03-22 18:57:47
description: A weekly review of indexed insertion, four-divisor filtering, street-grid connectivity and the longest matching prefix and suffix.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/03/22/LeetCode-weekly-contest-181/
  en: https://youngforest.github.io/en/2020/03/22/LeetCode-weekly-contest-181/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-181/en-hero.webp" alt="Forest compares road connectors among an insertion rail, four-support cubes, a glowing street grid and a ribbon whose ends overlap" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

Since LeetCode updated its rating algorithm, my rating reached its peak and has been dropping ever since. But that is also because I am not good enough; every contest I rank several hundred places down, and occasionally over a thousand.

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 839 / 10930 |	YoungForest | 	18	 | 	1:31:13 | 0:04:53 | 0:14:43  1 | 0:45:27  1 | 1:16:13  1 |


## 1389. Create Target Array in the Given Order

A warm-up problem. Use `vector`'s `insert` interface. The downside is efficiency, but it is enough for a warm-up problem.

Time complexity: O(n ^ 2), in the worst case, every insertion is at the front.
Space complexity: O(n).

```cpp
class Solution {
public:
    vector<int> createTargetArray(vector<int>& nums, vector<int>& index) {
        vector<int> ans;
        const int n = nums.size();
        for (int i = 0; i < n ; ++ i) {
            if (index[i] < ans.size()) 
                ans.insert(ans.begin() + index[i], nums[i]);
            else
                ans.push_back(nums[i]);
        }
        return ans;
    }
};
```

## 1390. Four Divisors

Brute force. Calculate the divisors of each number.

Time complexity: O(sqrt(nums[i]) * nums.length),
space complexity: O(nums.length) -> O(1) without memo.

```cpp
class Solution {
    unordered_map<int, int> memo;
    unordered_map<int, int> sumDivisors;
    int divisors(int x) {
        if (x == 1)
            return 1;
        else {
            if (memo.find(x) == memo.end()) {
                int ans = 2;
                int sum = 1 + x;
                for (int i = 2; i * i <= x; ++i) {
                    if (x % i == 0) {
                        ans += (i * i == x) ? 1 : 2;
                        sum += (i * i == x) ? i : i + x / i;
                    }
                    if (ans > 4)
                        break;
                }
                if (ans == 4) {
                    sumDivisors[x] = sum;
                }
                return memo[x] = ans;
            } else {
                return memo[x];
            }
        }
    }
public:
    int sumFourDivisors(vector<int>& nums) {
        int ans = 0;
        for (int i : nums) {
            if (divisors(i) == 4)
                ans += sumDivisors[i];
        }
        return ans;
    }
};
```

## 1391. Check if There is a Valid Path in a Grid

This problem is not very difficult algorithmically; one search is enough. The difficulty lies in implementation and abstracting a complex problem. Here I used multiple HashMaps to solve the abstraction of directions.

Time complexity: O(row * col),
space complexity: O(1).

This problem can also be solved with [Union-Find](https://leetcode.com/problems/check-if-there-is-a-valid-path-in-a-grid/discuss/547229/Python-Union-Find), which is a bit simpler to implement.

```cpp
class Solution {
public:
    bool hasValidPath(vector<vector<int>>& grid) {
        unordered_map<string, pair<int, int>> directions = {
            {"up", {-1, 0}},
            {"down", {1, 0}},
            {"left", {0, -1}},
            {"right", {0, 1}}
        };
        unordered_map<int, unordered_map<string, string>> mm = {
            {1, {{"left", "right"}, {"right", "left"}}},
            {2, {{"up", "down"}, {"down", "up"}}},
            {3, {{"left", "down"}, {"down", "left"}}},
            {4, {{"down", "right"}, {"right", "down"}}},
            {5, {{"left", "up"}, {"up", "left"}}},
            {6, {{"up", "right"}, {"right", "up"}}}
        };
        unordered_map<string, string> reversed = {
            {"left", "right"},
            {"right", "left"},
            {"up", "down"},
            {"down", "up"}
        };
        unordered_map<int, vector<string>> start_direction = {
            {1, {"left"}},
            {2, {"up"}},
            {3, {"left"}},
            {4, {"down", "right"}},
            {5, {}},
            {6, {"up"}}
        };
        const int m = grid.size();
        const int n = grid[0].size();
        if (m == 1 && n == 1)
            return true;
        for (string d : start_direction[grid[0][0]]) {
            int row = 0, col = 0;
            while (true) {
                d = mm[grid[row][col]][d];
                row += directions[d].first;
                col += directions[d].second;
                d = reversed[d];
                if (row < 0 || row >= m || col < 0 || col >= n || mm[grid[row][col]].find(d) == mm[grid[row][col]].end()) {
                    break;
                } else if (row == m - 1 && col == n - 1)
                    return true;
            }
        }
        return false;
    }
};
```

## 1392. Longest Happy Prefix

I referred to the code on [GeeksforGeeks](https://www.geeksforgeeks.org/longest-prefix-also-suffix/). It is not hard to find; Google "longest prefix suffix" and it comes up.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    string longestPrefix(string s) {
        int n = s.size(); 
  
        vector<int> lps(n); 
        lps[0] = 0; // lps[0] is always 0 

        // length of the previous 
        // longest prefix suffix 
        int len = 0; 

        // the loop calculates lps[i] 
        // for i = 1 to n-1 
        int i = 1; 
        while (i < n) 
        { 
            if (s[i] == s[len]) 
            { 
                len++; 
                lps[i] = len; 
                i++; 
            } 
            else // (pat[i] != pat[len]) 
            { 
                if (len != 0) 
                { 
                    len = lps[len-1]; 
                } 
                else // if (len == 0) 
                { 
                    lps[i] = 0; 
                    i++; 
                } 
            } 
        } 
        int res = lps[n-1]; 

        return s.substr(0, res); 
    }
};
```
