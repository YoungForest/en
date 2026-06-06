---
title: LeetCode weekly contest 149
date: 2019-08-11 12:34:32
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (6) |	Q4 (9)|
|--|--|--|--|--|--|--|--|
| 476 / 5091 |	YoungForest | 15	 | 	1:00:14 | 0:10:21  | 0:42:14 | 1:00:14 | null |

Ashamedly, my ranking fell back to 400+. I had half an hour to solve the last problem and kept trying to use a segment tree. Just like Kick Start Round D, I got obsessed with segment trees and crashed. The lesson learned is: do not insist that interval problems must be solved with segment trees. There are often simpler approaches.

## 1154. Day of the Year

Determine whether it is a leap year, then accumulate the days in previous months.

Time complexity: O(1),
Space complexity: O(1).

```cpp
class Solution {
    bool isLeap(int yy) {
        return (yy % 4 == 0 && yy % 100 != 0) || yy % 400 == 0;
    }
public:
    int dayOfYear(string date) {
        auto yy = stoi(date.substr(0, 4));
        auto mm = stoi(date.substr(5, 2));
        auto dd = stoi(date.substr(8, 2));
        vector<int> days = {0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
        if (isLeap(yy)) {
            days[2] = 29;
        }
        int ans = 0;
        ans += accumulate(days.cbegin(), days.cbegin() + mm, 0);
        ans += dd;
        return ans;
    }
};
```

## 1155. Number of Dice Rolls With Target Sum

Stars and bars, a classic combinatorics problem. Since the interval between two bars cannot be greater than the maximum die face value, it cannot be solved directly with a mathematical formula. I used backtracking with memoization, so it can also be considered DP.

Time complexity: O(N ^ 2),
Space complexity: O(d * bar).

```cpp
class Solution {
    const int mod = 1e9 + 7;
    int f;
    map<pair<int, int>, int> memo;
    // [begin, end), the index of insert bar
    int backtracking(int begin, int end, int bar) {
        if (memo.find({begin, bar}) != memo.end()) {
            return memo[{begin, bar}];
        }
        if (bar == 0) {
            if (end - begin + 1 <= f)
                return 1;
            else
                return 0;
        }
        int ans = 0;
        for (int i = begin; i < end && i - begin + 1 <= f; ++i) {
            // insert in i
            ans += backtracking(i + 1, end, bar - 1);
            ans %= mod;
        }
        memo[{begin, bar}] = ans;
        return ans;
    }
public:
    int numRollsToTarget(int d, int f, int target) {
        this->f = f;
        int ans = backtracking(0, target - 1, d - 1);
        return ans;
    }
};
```

## 1156. Swap For Longest Repeated Character Substring

One pass, updating the longest length that can be obtained.

We need to observe that the longest length obtained has nothing to do with the character in the middle gap, and only depends on whether there are needed characters before and after it.

Time complexity: O(N),
Space complexity: O(N).

```cpp
class Solution {
public:
    int maxRepOpt1(string text) {
        vector<vector<pair<int, int>>> v(26);
        char last = ' ';
        for (int i = 0; i < text.size(); ++i) {
            if (text[i] == last) {
                v[text[i] - 'a'].back().second = i + 1;
            } else {
                v[text[i] - 'a'].push_back({i, i + 1});
            }
            last = text[i];
        }
        int ans = 0;
        for (const auto& row : v) {
            for (int i = 0; i < row.size(); ++i) {
                if (i + 1 == row.size()) {
                    if (row.size() == 1)
                        ans = max(ans, row[i].second - row[i].first);
                    else
                        ans = max(ans, row[i].second - row[i].first + 1);
                } else {
                    const auto& a = row[i];
                    const auto& b = row[i + 1];
                    if (a.second + 1 == b.first) {
                        if (row.size() > 2) {
                            ans = max(ans, a.second - a.first + b.second - b.first + 1);
                        } else {
                            ans = max(ans, a.second - a.first + b.second - b.first);
                        }
                    } else {
                        ans = max(ans, a.second - a.first + 1);
                    }
                }
            }
        }
        return ans;
    }
};
```

## 1157. Online Majority Element In Subarray

Random pick plus binary search.

Time complexity: O(log N).
Space complexity: O(N).

```cpp
class MajorityChecker {
    unordered_map<int, vector<int>> appear_index;
    vector<int> arr;
public:
    MajorityChecker(vector<int>& arr) {
        for (int i = 0; i < arr.size(); ++i) {
            appear_index[arr[i]].push_back(i);
        }
        this->arr = arr;
    }
    
    int query(int left, int right, int threshold) {
        std::random_device dev;
        std::mt19937 rng(dev());
        std::uniform_int_distribution<std::mt19937::result_type> dist6(left,right); // distribution in range [left, right]
        for (int i = 0; i < 20; ++i) {
            int a = arr[dist6(rng)];
            const auto& index = appear_index[a];
            auto l = lower_bound(index.cbegin(), index.cend(), left);
            auto r = upper_bound(index.cbegin(), index.cend(), right);
            if (r - l >= threshold) {
                return a;
            }
        }
        return -1;
    }
};

/**
 * Your MajorityChecker object will be instantiated and called as such:
 * MajorityChecker* obj = new MajorityChecker(arr);
 * int param_1 = obj->query(left,right,threshold);
 */
```
