---
title: LeetCode Weekly Contest 244
date: 2021-06-06 15:37:26
description: "Four solutions rotate a matrix, count sorted reductions, minimize flips in a circular alternating string, and reduce packaging waste with prefix sums."
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/06/06/LeetCode-weekly-contest-244/
  en: https://youngforest.github.io/en/2021/06/06/LeetCode-weekly-contest-244/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 142 / 14467 | YoungForest | 18 | 0:51:13 |  0:05:21 | 0:09:54 |  0:30:19 | 0:46:13 🐞1 |

This afternoon I arranged to have dinner with [Cruel Coding East God](http://leetcode.com/ddoudle), a strong player with a 2700+ rating. He did his undergraduate studies at Zhejiang University and is doing graduate school in Canada. This summer he came to Beijing for an internship at Megvii, so we had a chance to meet offline.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-244/en-hero.webp" alt="A tiled tray rotates toward a template, stepped blocks reduce by level, a two-colour belt alternates with few flips and nested boxes capture spare space" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## 1886. Determine Whether Matrix Can Be Obtained By Rotation

A warm-up problem. Rotate three times plus the original state, for four states in total, and compare them one by one. Rotation itself is another classic LeetCode problem. In-place rotation is still somewhat complex to implement. But because `n` is small and this is a warm-up problem, I directly used an auxiliary array. Although the time complexity went up, the implementation became much simpler.

```cpp
class Solution {
    void rotate(vector<vector<int>>& mat) {
        vector<vector<int>> cp = mat;
        const int n = mat.size();
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                cp[j][n-1-i] = mat[i][j];
            }
        }
        mat = move(cp);
    }
public:
    bool findRotation(vector<vector<int>>& mat, vector<vector<int>>& target) {
        if (mat == target) return true;
        for (int i = 0; i < 3; ++i) {
            rotate(mat);
            if (mat == target) return true;
        }
        return false;
    }
};
```

Time complexity: O(4 * n * n),
space complexity: O(n * n).

## 1887. Reduction Operations to Make the Array Elements Equal

After understanding the whole reduction process, you can see that the number of operations needed for each number to reduce to the minimum is actually equal to the number of distinct elements smaller than it.

Therefore, sort first, then use one pass to sum the count of "elements smaller than it".

```cpp
class Solution {
public:
    int reductionOperations(vector<int>& nums) {
        const int n = nums.size();
        sort(nums.begin(), nums.end());
        int ans = 0;
        int add = 0;
        for (int i = 1; i < n; ++i) {
            if (nums[i] > nums[i-1]) {
                ++add;
            }
            ans += add;
        }
        return ans;
    }
};
```

Time complexity: O(N log N),
space complexity: O(1).

## 1888. Minimum Number of Flips to Make the Binary String Alternating

Observation:

Operation 1 can be performed any number of times. That means we can enumerate every position, assume it is the beginning, enumerate the two cases where the beginning is 0 or 1, and then count how many positions after and before it do not match the expectation.

The operation of counting "how many positions before and after do not match the expectation" can be implemented in O(1) by maintaining the counts of 0/1 at odd/even positions before and after.

Because of the parity issue, the "number before it that does not match expectation" needs separate discussion.

It turns out that when the length is even, the starting position does not matter. In that case the problem can be further simplified.

When the length is odd, every starting position still needs to be enumerated.

```cpp
class Solution {
public:
    int minFlips(string s) {
        const int n = s.size();
        if (n % 2 == 0) {
            vector<int> cnt(2, 0);
            for (int i = 0; i < n; ++i) {
                cnt[i % 2] += (s[i] - '0');
            }
            const int half = n / 2;
            return min(half - cnt[0] + cnt[1], cnt[0] + half - cnt[1]);
        } else {
            vector<int> after(2, 0);
            for (int i = 0; i < n; ++i) {
                after[i % 2] += (s[i] - '0');
            }
            vector<int> before(2, 0);
            int ans = numeric_limits<int>::max();
            const int half = n / 2;
            for (int i = 0; i < n; ++i) {
                const int now = i % 2;
                ans = min(ans, half + 1 - after[now] - before[1-now] + after[1-now] + before[now]); // 1 begin
                ans = min(ans, after[now] + before[1-now] + half - after[1-now] - before[now]); // 0 begin
                after[i % 2] -= (s[i] - '0');
                before[i % 2] += (s[i] - '0');
            }
            return ans;
        }
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 1889. Minimum Space Wasted From Packaging

The brute-force solution for this problem is easy to think of:

Iterate through all `boxes`; for each supplier, iterate through all packages; for each package, find the smallest box that is only larger than it for packaging. The time complexity is O(m * n * log m), which will obviously TLE.

The problem gives data constraints, and one of them is especially worth noticing: `sum(boxes[j].length) <= 10^5`.

That means we can fully enumerate every box and find the packages it uses. This can be done with binary search: find the position of the largest package the box can fit. Excluding what smaller boxes have already packed, the rest are packed by this box. Then use prefix sums to quickly compute wasted space.

The time complexity is `O(sum(boxes[j].length) * log n + n log n)`, which exactly satisfies the requirement.

Because the maximum wasted space is `max(boxes[i][j]) * packges.length = 10 ^ 10`, `int` will overflow, so `long long` is needed.

Finally, never forget MOD. I had one WA because of this.

```cpp
class Solution {
    using ll = long long;
    const ll MOD = 1e9 + 7;
public:
    int minWastedSpace(vector<int>& packages, vector<vector<int>>& boxes) {
        sort(packages.begin(), packages.end());
        const int n = packages.size();
        vector<ll> presum(n+1);
        presum[0] = 0;
        for (int i = 0; i < n; ++i) {
            presum[i+1] = presum[i] + packages[i];
        }
        ll ans = numeric_limits<ll>::max();
        const int m = boxes.size();
        for (auto& b : boxes) {
            sort(b.begin(), b.end());
            int lastIdx = 0;
            ll current = 0;
            for (int j : b) {
                auto it = upper_bound(packages.begin(), packages.end(), j);
                const ll d = distance(packages.begin(), it);
                const ll width = d - lastIdx;
                current += width * j - (presum[d] - presum[lastIdx]);
                lastIdx = d;
            }
            if (b.back() < packages.back()) { // can not fit
                
            } else {
                ans = min(ans, current);
            }
        }
        if (ans == numeric_limits<ll>::max()) return -1;
        else return ans % MOD;
    }
};
```

Time complexity: O(sum(boxes[j].length) * log n + n log n),
space complexity: O(n).
