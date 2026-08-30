---
title: LeetCode Weekly Contest 185
date: 2020-04-20 11:42:21
description: A weekly review of alternating token types, restaurant-order aggregation, a frog state machine and exact-comparison array DP, with notes on state design.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/04/20/LeetCode-weekly-contest-185/
  en: https://youngforest.github.io/en/2020/04/20/LeetCode-weekly-contest-185/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-185/en-hero.webp" alt="Circles and squares alternate, café orders merge by shape, frogs cycle through lily pads and tiered trays build arrays with counted new highs" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (4) |	Q3 (6) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 703 / 9206 |	YoungForest | 12 | 	0:36:35 |  0:10:24 | 0:22:03 | 0:31:35  1 | null |

## 1417. Reformat The String

Count the number of digits and letters separately. If the difference in counts is no greater than 1, the string can be reformatted.
Choose the category with more characters first.

Time complexity: O(N),
space complexity: O(N). A two-pointer method can be used to save the space for storing the digit and letter strings.

```cpp
class Solution {
    string compose(const string& a, const string& b) {
        string ans;
        int ai = 0, bi = 0;
        if (a.size() > b.size())
            ans.push_back(a[ai++]);
        while (ai < a.size()) {
            ans.push_back(b[bi++]);
            ans.push_back(a[ai++]);
        }
        return ans;
    }
public:
    string reformat(string s) {
        string digit, alpha;
        for (char c : s) {
            if (c >= '0' && c <= '9') {
                digit.push_back(c);
            } else {
                alpha.push_back(c);
            }
        }
        if (digit.size() == alpha.size() || digit.size() + 1 == alpha.size() || alpha.size() + 1 == digit.size()) {
            if (digit.size() >= alpha.size()) {
                return compose(digit, alpha);
            } else {
                return compose(alpha, digit);
            }
        } else {
            return "";
        }
    }
};
```

## 1418. Display Table of Food Orders in a Restaurant

This tests the use of data structures. Traverse `orders` and convert it into a `dish -> table` HashMap, while using a `set` to store table numbers.

Time complexity: O(orders.length * orders[i].length),
space complexity: O(tables.length * dishes.length).

```cpp
class Solution {
public:
    vector<vector<string>> displayTable(vector<vector<string>>& orders) {
        set<int> tables;
        map<string, unordered_map<int, int>> count;
        for (const auto& p : orders) {
            const int table_number = stoi(p[1]);
            const auto& dish = p[2];
            ++count[dish][table_number];
            tables.insert(table_number);
        }
        vector<vector<string>> ans;
        vector<string> first_row = {"Table"};
        for (const auto& p : count) {
            first_row.push_back(p.first);
        }
        ans.push_back(move(first_row));
        for (int i : tables) {
            vector<string> row;
            row.push_back(to_string(i));
            for (auto& p : count) {
                row.push_back(to_string(p.second[i]));
            }
            ans.push_back(move(row));
        }
        return ans;
    }
};
```

## 1419. Minimum Number of Frogs Croaking

A finite state machine. Record the number of frogs at each different position, and simulate the entire croaking process.

Time complexity: O(croakOfFrogs.length),
space complexity: O(croak.length).

```cpp
class Solution {
public:
    int minNumberOfFrogs(string croakOfFrogs) {
        vector<int> state_count(5, 0);
        unordered_map<char, int> position = {
            {'c', 0},
            {'r', 1},
            {'o', 2},
            {'a', 3},
            {'k', 4}
        };
        for (char c : croakOfFrogs) {
            if (position[c] == 0) {
                if (state_count[4] > 0) {
                    --state_count[4];
                }
                ++state_count[position[c]];
            } else {
                ++state_count[position[c]];
                if (state_count[position[c]-1] <= 0)
                    return -1;
                --state_count[position[c]-1];
            }
        }
        if (all_of(state_count.begin(), state_count.begin() + 4, [](const auto& a) -> bool {
            return a == 0;
        }))
            return state_count[4];
        else
            return -1;
    }
};
```

## 1420. Build Array Where You Can Find The Maximum Exactly K Comparisons

Recently I have solved many DP problems, but when I encounter a new problem, I still cannot always solve it. The main difficulties are:
- Determining the definition of `dp`. Usually copying the target directly works, but not always.
- Determining boundary conditions, meaning the recursive exit conditions.
- The state transition equation, which is tightly connected to the definition of `dp`.

In this problem, `dp[i][currentMaxValue][cost]` represents the number of arrays of length `i` whose maximum value is `currentMaxValue` and whose increasing subsequence length is `cost`.

There are two types of state transitions:
The first is when the maximum value of the length `i-1` array is already `currentMaxValue`. In that case, we can add `[1, currentMaxValue]` at the `i`-th position, and `cost` stays unchanged.
The second is when the maximum value of the length `i-1` array is less than `currentMaxValue`. In this case, the `i`-th position must be `currentMaxValue`, and the previous `cost` is `cost-1`. Note that the minimum previous maximum value is `cost-1`, with the increasing sequence being `1, ..., cost-1`.

Time complexity: O(n * m * k * (m - k)),
space complexity: O(n * m * k).

```python
class Solution:
    def numOfArrays(self, n: int, m: int, k: int) -> int:
        mod = 10**9 + 7
        @lru_cache(None)
        def dp(i, currentMaxValue, cost):
            if cost == 1:
                return currentMaxValue**i
            ans = 0
            # arr[i] <= currentMaxValue
            if i + 1 > cost:
                ans += dp(i-1, currentMaxValue, cost) * currentMaxValue
            # arr[i] == currentMaxValue
            ans += sum(dp(i-1, x, cost - 1) for x in range(cost - 1, currentMaxValue))
            return ans
        return sum(dp(n - 1, x, k) for x in range(k, m+1)) % mod
        # return sum(dp(length, k, x) * (x ** (n - length)) for x in range(1, m + 1) for length in range(1, n+1))
```

## A DP Problem From Meituan's Written Test

I participated in Meituan's summer internship written test on Thursday. The last problem was also DP, but I did not solve it at the time. In fact, it was just that kind of problem.

> Given two strings `S` and `T`, find the number of substrings of `S` that are equal to subsequences of `T`. Note that even if substrings are the same, different positions count as different ways.
> The maximum length of `S` and `T` is 5000.

Time complexity: (len(S) * (len(T) ^ 2)),
space complexity: (len(S) * len(T)).

```python
import functools
S = input()
T = input()

@functools.lru_cache(None)
def dp(i, j):
    # S[x:i], 以s[i]结尾的子串，T[:j]中子串匹配到的数目
    if i == 0:
        return sum(1 if S[i] == T[x] else 0 for x in range(0, j + 1))
    if j == 0:
        return 1 if S[i] == T[0] else 0
    ans = 0
    if T[0] == S[i]:
        ans = 1
    for x in range(1, j+1):
        if T[x] == S[i]:
            ans += dp(i-1, x-1) + 1
    return ans

mod = 1000000000 + 7
ans = sum(dp(x, len(T)-1) for x in range(0, len(S))) % mod
print(ans)
```
