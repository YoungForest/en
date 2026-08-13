---
title: LeetCode Weekly Contest 217
date: 2020-11-29 20:28:52
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/11/29/LeetCode-weekly-contest-217/
  en: https://youngforest.github.io/en/2020/11/29/LeetCode-weekly-contest-217/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (6) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 356 / 9462 | YoungForest | 13 | 	1:24:07 |  0:03:55	 | 0:20:14 |  1:09:07  3 | null |


## 1672. Richest Customer Wealth

Warm-up problem. Sum an array; C++ `accumulate` handles it in one line.

```cpp
class Solution {
public:
    int maximumWealth(vector<vector<int>>& accounts) {
        int ans = 0;
        for (const auto & v : accounts) {
            ans = max(ans, accumulate(v.begin(), v.end(), 0));
        }
        return ans;
    }
};
```

## 1673. Find the Most Competitive Subsequence

Similar to [402. Remove K Digits](https://leetcode-cn.com/problems/remove-k-digits/), use a greedy idea and try to delete larger numbers. The standard approach is to use a monotonic stack.

During the contest, I used a priority queue to maintain the maximum number to delete.

```cpp
class Solution {
public:
    vector<int> mostCompetitive(vector<int>& nums, int k) {
        using pii = pair<int, int>;
        auto cmp = [](pii a, pii b) -> bool {
            if (a.first != b.first) {
                return a.first > b.first;
            } else {
                return a.second > b.second;
            }
        };
        priority_queue<pii, vector<pii>, decltype(cmp)> m(cmp);
        
        for (int i = 0; i + k - 1 < nums.size(); ++i) {
            m.push({nums[i], i});
        }
        int begin = 0;
        vector<int> ans;
        while (k > 0) {
            auto p = m.top();
            m.pop();
            while (p.second < begin && !m.empty()) {
                p = m.top();
                m.pop();
            }
            begin = p.second;
            ans.push_back(p.first);
            --k;
            if (k == 0) break;
            m.push({nums[nums.size() - k], nums.size() - k});
        }
        return ans;
    }
};
```

Time complexity: O(N log N),
space complexity: O(N).


## 1674. Minimum Moves to Make Array Complementary

I recorded my contest-time thinking in the comments. Finally, inspiration struck: determine the range where each pair needs one change, then enumerate every possible target sum and quickly compute the number of changes needed in O(log N).


```cpp
class Solution {
    static const int MAX = 1e5 + 5;
    int open[MAX], close[MAX], dot[MAX];
public:
    int minMoves(vector<int>& nums, int limit) {
        // 统计和的最大众数，变其他。但是有的需要大于limit (x)
        // 二分，快速求判定问题？
        // 暴力，枚举互补数，遍历一遍再。O(n * n)
        // a + b = 互补数，0
        // a + b 变 互补数： 1， 2
        // 0次变 是一个数
        // 一次变最大，一次变最小 是一个范围
        // 出了范围就需要2次变
        // 确定互补数，可以遍历一遍确定变数
        // 确定变数呢？
        // ｜  .  ｜
        // open - close: in range
        // close + total - open: out range
        // dtop: 0
        const int n = nums.size();
        for (int i = 0; i < n - 1 - i; ++i) {
            int a = nums[i];
            int b = nums[n - 1 - i];
            if (a > b) swap(a, b);
            // a <= b
            int lower = a + 1;
            int upper = b + limit;
            dot[i] = a + b;
            open[i] = lower;
            close[i] = upper;
        }
        const int size = n / 2;
        auto openEnd = begin(open) + size;
        auto closeEnd = begin(close) + size;
        auto dotEnd = begin(dot) + size;
        sort(begin(open), openEnd);
        sort(begin(close), closeEnd);
        sort(begin(dot), dotEnd);
        
        auto need = [&](const int line) -> int {
            auto itOpen = upper_bound(begin(open), openEnd, line);
            auto itClose = lower_bound(begin(close), closeEnd, line);
            auto itDot = lower_bound(begin(dot), dotEnd, line);
            auto itDot2 = upper_bound(itDot, dotEnd, line);
            int dotNumber = distance(itDot, itDot2);
            int outRange = 0;
            outRange += distance(begin(close), itClose);
            outRange += distance(itOpen, openEnd);
            int inRange = n / 2 - dotNumber - outRange;
            return inRange + 2 * outRange;
        };
        int ans = n;
        for (int line = 2; line <= limit * 2; ++line) {
            const int condidate = need(line);
            // cout << line << ": " << condidate << endl;
            ans = min(ans, condidate);
        }
        return ans;
    }
};
```

Time complexity: O(limit * log N),
space complexity: O(N).

During the contest, C++ was hit by constant-factor limits. I had to change arrays to global arrays to AC.
I am usually more accustomed to using local `vector`, which is more readable and maintainable.

Using a difference array can avoid binary search. See [zerotrac's solution](https://leetcode-cn.com/problems/minimum-moves-to-make-array-complementary/solution/shi-shu-zu-hu-bu-de-zui-shao-cao-zuo-ci-shu-by-zer/) for details. This was also my first time encountering the concept of a difference array.

## 1675. Minimize Deviation in Array

Based on the two operations, the problem can be easily transformed into a previous LC problem:
[632. Smallest Range Covering Elements from K Lists](https://leetcode-cn.com/problems/smallest-range-covering-elements-from-k-lists/).
Then use a priority queue or Tree to solve it.

```cpp
class Solution {
public:
    int minimumDeviation(vector<int>& nums) {
        multiset<int> ms;
        for (int i : nums) {
            if (i % 2 == 0) ms.insert(i);
            else ms.insert(i * 2);
        }
        int ret = numeric_limits<int>::max();
        while (true) {
            ret = min(ret, *ms.rbegin() - *ms.begin());
            const int maxValue = *ms.rbegin();
            ms.erase(prev(ms.end()));
            if (maxValue % 2 != 0) break;
            ms.insert(maxValue / 2);
        }
        return ret;
    }
};
```

Time complexity: O(N log N),
space complexity: O(N).
