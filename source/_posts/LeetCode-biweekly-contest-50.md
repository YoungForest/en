---
title: LeetCode Biweekly Contest 50
date: 2021-04-18 11:57:36
description: Three solutions raise an increasing array, count points in circles and maintain XOR in reverse; the fourth has only a link and no completed solution.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/04/18/LeetCode-biweekly-contest-50/
  en: https://youngforest.github.io/en/2021/04/18/LeetCode-biweekly-contest-50/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 1032 / 10097 | YoungForest | 12 | 0:20:28 | 0:09:10 |  0:12:51 |  0:20:28 | null |

I was eight minutes late because of showering; otherwise I should have been able to enter the top 500.

This biweekly contest was a disguised hand-speed contest, with 141 people solving all four problems. The rest was a competition of speed on the first three problems.

I smoothly finished the first three problems in 10 minutes, but thought about Q4 for an hour without making a major breakthrough.

Although I had a few clues and felt it was a DP problem, afterward I found that the problem had already gone beyond the syllabus, so not solving it was normal.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-biweekly-contest-50/en-hero.webp" alt="A lifting rig minimally raises an increasing row, hoops count scattered pegs, a reverse bead chain feeds a complement prism and the final permutation cabinet remains closed and unresolved" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## 1827. Minimum Operations to Make the Array Increasing

Greedy. While maintaining increasing order, keep the numbers as small as possible.

```cpp
class Solution {
public:
    int minOperations(vector<int>& nums) {
        // greedy
        // time: O(N)
        int last = 0;
        int ans = 0;
        for (int i : nums) {
            if (i > last) {
                last = i;
            } else {
                ans += last + 1 - i;
                ++last;
            }
        }
        return ans;
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 1828. Queries on Number of Points Inside a Circle

Because the number of points and circles are both small (<= 500), direct brute force is enough.

```cpp
class Solution {
    bool inside(const vector<int>& p, const vector<int>& c) {
        return (p[0] - c[0]) * (p[0] - c[0]) + (p[1] - c[1]) * (p[1] - c[1]) <= c[2] * c[2];
    }
public:
    vector<int> countPoints(vector<vector<int>>& points, vector<vector<int>>& queries) {
        vector<int> ans;
        ans.reserve(queries.size());
        for (const auto& v : queries) {
            int count = 0;
            for (const auto& p : points) {
                if (inside(p, v)) ++count;
            }
            ans.push_back(count);
        }
        return ans;
    }
};
```

Time complexity: O(points.length * queries.length),
space complexity: O(queries.length).

## 1829. Maximum XOR for Each Query

Use prefix sum, actually prefix XOR, to quickly perform the `Remove` operation, then construct `k` by complementing bits.

```cpp
class Solution {
public:
    vector<int> getMaximumXor(vector<int>& nums, int maximumBit) {
        int currentXOR = accumulate(nums.begin(), nums.end(), 0, [](int a, int b) -> int {
            return a ^ b;
        });
        auto findMax = [&](const int a) -> int {
            // argmax a ^ k
            int k = 0;
            for (int i = maximumBit - 1; i >= 0; --i) {
                if (!(a & (1 << i))) {
                    k |= (1 << i);
                }
            }
            return k;
        };
        vector<int> ans;
        ans.reserve(nums.size());
        for (auto it = nums.rbegin(); it != nums.rend(); ++it) {
            const int x = *it;
            ans.push_back(findMax(currentXOR));
            currentXOR ^= x;
        }
        return ans;
    }
};
```

Time complexity: O(nums.length * maximumBit),
space complexity: O(nums.length).

## 1830. Minimum Number of Operations to Make String Sorted

[Problem link](https://leetcode.com/problems/minimum-number-of-operations-to-make-string-sorted/).
