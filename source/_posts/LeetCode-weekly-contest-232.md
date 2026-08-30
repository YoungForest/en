---
title: LeetCode Weekly Contest 232
date: 2021-03-14 15:36:47
description: "Four solutions test one swap, find a star centre, greedily improve pass ratios, and expand from a fixed index for the best subarray score."
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/03/14/LeetCode-weekly-contest-232/
  en: https://youngforest.github.io/en/2021/03/14/LeetCode-weekly-contest-232/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 807 / 12541 | YoungForest | 17 | 	1:06:39 |  0:03:24 | 0:06:02 | 0:38:44 | 1:01:39  1 |

Yesterday I went out to repair my Mac because the screen kept flickering. Sure enough, for Apple products, AppleCare is necessary. Last time I repaired the keyboard; this time I repaired the screen, so all four sides have been replaced with new parts. I was out running around for a whole day and was especially tired. I woke up late this morning, and the contest started as soon as I got up. I had not had a sip of water or a bite of food.
After solving 3 problems for two consecutive weeks, I finally solved 4. At first I was quite pleased and thought I should not need to check in this week. Later I found the clown was actually me. Other contestants even thought this round was a speed round. I overcomplicated T3 and T4, so I was a bit slow and did not enter the top 500. My Cruel ranking also fell from 10+ to 40+. My rating will drop next week. I did not expect 3 problems to raise rating and 4 problems to drop it.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-232/en-hero.webp" alt="Two mismatched beads swap into alignment, spokes reveal a central hub, bonus marbles enter the best-gain vessel and two rails expand from an anchored column" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## 1790. Check if One String Swap Can Make Strings Equal

Warm-up problem. Special-case exact equality, then enumerate all swaps to check whether any one works.

```cpp
class Solution {
public:
    bool areAlmostEqual(string s1, string s2) {
        if (s1 == s2) return true;
        for (int i = 0; i < s1.size(); ++i) {
            for (int j = i + 1; j < s1.size(); ++j) {
                swap(s1[i], s1[j]);
                if (s1 == s2) return true;
                swap(s1[i], s1[j]);
            }
        }
        return false;
    }
};
```

Time complexity: O(N^2),
space complexity: O(1).

Of course, this problem also has an O(N) solution. For example, find the two positions where the two strings differ, then swap them.
Because the constraints were small and the contest emphasized speed, I chose a higher-complexity solution that was simpler to think of and write.

## 1791. Find Center of Star Graph

Compute the degree of every node. The node with degree `n-1` is the center node.

```cpp
class Solution {
public:
    int findCenter(vector<vector<int>>& edges) {
        const int n = edges.size() + 1;
        vector<int> degree(n + 1, 0);
        for (const auto& e : edges) {
            ++degree[e[0]];
            ++degree[e[1]];
        }
        for (int i = 0; i <= n; ++i) {
            if (degree[i] == n - 1) {
                return i;
            }
        }
        return -1;
    }
};
```

Time complexity: O(N),
space complexity: O(N).

Of course, this problem also has an O(1) time and space solution. For example, the common node of the first two edges is the center node.

## 1792. Maximum Average Pass Ratio

Greedy + priority queue.
Each smart student is always added first to the class where they can increase the pass ratio the most.
Use a priority queue to maintain this greedy idea. `pair<double, int>` represents the increase in pass ratio and the corresponding class index.

For a concrete proof of the greedy correctness, refer to [zerotrac's solution](https://leetcode-cn.com/problems/maximum-average-pass-ratio/solution/zui-da-ping-jun-tong-guo-lu-by-zerotrac2-84br/).

```cpp
class Solution {
    double div(const double a, const double b) {
        return a / b;
    }
public:
    double maxAverageRatio(vector<vector<int>>& classes, int extraStudents) {
        using pii = pair<double, int>;
        const int n = classes.size();
        priority_queue<pii> pq;
        auto change = [&](const int i, const int more) -> double {
            double next = div(classes[i][0] + more, classes[i][1] + more);
            double now = div(classes[i][0], classes[i][1]);
            return next - now;
        };
        double ans = 0;
        
        for (int i = 0; i < n; ++i) {
            pq.push({change(i, 1), i});
            ans += div(classes[i][0], classes[i][1]);
        }
        
        
        for (int i = 0; i < extraStudents; ++i) {
            auto [c, index] = pq.top();
            pq.pop();
            ans += c;
            classes[index][0] += 1;
            classes[index][1] += 1;
            pq.push({change(index, 1), index});
        }
        
        return div(ans, n);
    }
};
```

Time complexity: O(n * log n),
space complexity: O(n).

## 1793. Maximum Score of a Good Subarray

Based on the constraints, the solution's time complexity is likely either an O(N) two-pointer solution, or an O(N log N) binary search that turns an optimization problem into a decision problem.
Looking again at the trend of how `i` and `j` change, we can basically determine that a two-pointer solution should be used. Compare which side can become larger, then move that side. At the same time, we need to maintain the monotonically decreasing property, skipping numbers that would become larger.

In the contest, my implementation first used monotonic decrease to compute the change positions: `construct`.
Then I moved two pointers over those change positions.

```cpp
class Solution {
    using pii = pair<int, int>;
    template <typename T>
    vector<pii> construct(T begin, T end) {
        vector<pii> ans;
        int currentMin = *begin;
        for (auto it = next(begin); it != end; ++it) {
            if (*it < currentMin) {
                ans.push_back({currentMin, distance(begin, it)});
                currentMin = *it;
            }
        }
        ans.push_back({currentMin, distance(begin, end)});
        return ans;
    }
public:
    int maximumScore(vector<int>& nums, int k) {
        const int n = nums.size();
        auto right = construct(nums.begin() + k, nums.end());
        auto left = construct(nums.rbegin() + n - k - 1, nums.rend());
        int l = 0, r = 0;
        int ans = min(left[l].first, right[r].first) * (left[l].second + right[r].second - 1);
        while (l + 1 < left.size() || r + 1 < right.size()) {
            if (l + 1 < left.size() && r + 1 < right.size()) {
                if (left[l + 1].first > right[r + 1].first) {
                    ++l;
                } else {
                    ++r;
                }
            } else if (l + 1 < left.size()) {
                ++l;
            } else {
                ++r;
            }
            ans = max(ans, min(left[l].first, right[r].first) * (left[l].second + right[r].second - 1));
        }
        return ans;
    }
};
```

Time complexity: O(nums.size()),
space complexity: O(nums.size()) = O(2 * 10^4).

Actually, my implementation of the monotonic decreasing `construct` function is redundant. This monotonic decrease can be implemented directly while moving the two pointers, which can reduce the space complexity to `O(1)`.
