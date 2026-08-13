---
title: LeetCode biweekly contest 1
date: 2019-06-03 20:03:05
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/06/03/LeetCode-biweekly-contest-1/
  en: https://youngforest.github.io/en/2019/06/03/LeetCode-biweekly-contest-1/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (6) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
| 241 / 983 |	YoungForest | 7 | 	0:18:23 | 0:09:56 | 0:18:23 | null  | null |

LeetCode opened its first biweekly contest, every Saturday night from 10:30 to 12:30. The goal may be to make it easier for students in Europe to participate. The normal weekly contest is usually in the early morning in Europe. With the duration extended to two hours, they can also set harder problems.

Since I had already taken ByteDance's summer camp written test from 19:00 to 21:30, and that written test was also very hard, with only 30% of the second of three programming questions passed, the later biweekly contest also went badly. My result was not ideal. I only solved two Easy problems.

## 1055. Fixed Point

Intuition:
Straight forward. One pass.

Time complexity: `O(N)`
Space complexity: `O(1)`

```cpp
class Solution {
public:
    int fixedPoint(vector<int>& A) {
        for (int i = 0; i < A.size(); ++i) {
            if (A[i] == i) {
                return i;
            }
        }
        return -1;
    }
};
```

Because array A is sorted in ascending order, we can also use binary search to implement `O(log N)`.

```cpp
class Solution {
public:
    int fixedPoint(vector<int>& A) {
        int lo = 0, hi = A.size();
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (A[mid] < mid) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        
        return A[lo] == lo ? lo : -1;
    }
};
```

## 1056. Index Pairs of a String

Intuition:
The problem is Easy and the data size is also small, so a brute-force solution is enough.

Time complexity: O(words.size() * text.size() * words[i].size()),
Space complexity: O(words.size() * text.size()).

```cpp
class Solution {
public:
    vector<vector<int>> indexPairs(string text, vector<string>& words) {
        vector<vector<int>> ret;
        for (const string& word : words) {
            int pos = 0;
            while ((pos = text.find(word, pos)) != string::npos) {
                ret.push_back({pos, pos + word.size() - 1});
                pos += 1;
            }
        }
        sort(ret.begin(), ret.end(), [](const vector<int>& lhs, const vector<int>& rhs) -> bool {
            if (lhs[0] != rhs[0])
                return lhs[0] < rhs[0];
            return lhs[1] < rhs[1];
        });
        return ret;
    }
};
```

## 1066. Campus Bikes II

Because the data size is very small, `1 <= workers.length <= bikes.length <= 10`, consider brute-force backtracking.

Time complexity: O(workers.length ^ bikes.length).
Space complexity: O(bikes.length).

Sadly, it timed out.

```cpp
class Solution {
    // 因为1 <= workers.length <= bikes.length <= 10，
    // 考虑阶乘算法 + 剪枝
    int global_minimum = numeric_limits<int>::max();
    void backtracking(const vector<vector<int>>& workers, const vector<vector<int>>& bikes, vector<bool>& visited, int local_distance, int step) {
        if (step == workers.size()) {
            global_minimum = min(global_minimum, local_distance);
            return;
        }
        if (local_distance >= global_minimum)
            return;
        for (int i = 0; i < visited.size(); ++i) {
            if (visited[i])
                continue;
            visited[i] = true;
            backtracking(workers, bikes, visited, local_distance + abs(workers[step][0] - bikes[i][0]) + abs(workers[step][1] - bikes[i][1]), step + 1);
            visited[i] = false;
        }
    }
public:
    int assignBikes(vector<vector<int>>& workers, vector<vector<int>>& bikes) {
        vector<bool> visited(bikes.size(), false);
        backtracking(workers, bikes, visited, 0, 0);
        return global_minimum;
    }
};
```

After reading Discuss, there are two solutions: priority queue or DP.

## 1067. Digit Count in Range

First, solve a simpler problem:
[Solution](https://leetcode.com/problems/number-of-digit-one/solution/)

Count the number of digit 1.

```cpp
int countDigitOne(int n)
{
    int countr = 0;
    for (long long i = 1; i <= n; i *= 10) {
        long long divider = i * 10;
        countr += (n / divider) * i + min(max(n % divider - i + 1, 0LL), i);
    }
    return countr;
}
```

Based on the solution above, it is easy to extend to any digit.

One thing to note is that digit `0` cannot be a leading digit, so it needs special handling.

```cpp
class Solution {
    // include n
    int helper(int d, int n) {
        int ret = 0;
        for (long long i = 1; i <= n; i *= 10) {
            long long divisor = i * 10;
            ret += (n / divisor) * i + min(i, max(0LL, n % divisor - d * i + 1)) - (d == 0 ? i : 0);
            // cout << ret << " ";
        }
        // cout << n << " " << ret << endl;
        return ret;
    }
public:
    int digitsCount(int d, int low, int high) {
        return helper(d, high) - helper(d, low - 1);
    }
};
```

Time complexity: O(log N)
Space complexity: O(1)
