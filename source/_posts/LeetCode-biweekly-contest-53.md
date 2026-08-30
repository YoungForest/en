---
title: LeetCode biweekly contest 53
date: 2021-05-30 11:44:44
description: Four solutions test three distinct characters, pair array extremes, rank rhombus sums, and use bitmask DP for minimum XOR matching.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/05/30/LeetCode-biweekly-contest-53/
  en: https://youngforest.github.io/en/2021/05/30/LeetCode-biweekly-contest-53/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 219 / 12291 | YoungForest | 18 | 0:57:45 | 0:19:12 | 0:22:21 |  0:46:08 |  0:57:45 |

Because I went with my partner to a Spartan Race today, I got up before 6 a.m. and spent the whole day outside.
Before the contest, I thought I would rest a little and planned to sleep for 20 minutes. I did not expect to be that tired; when the alarm rang, I turned it off myself. So I was more than 10 minutes late. Otherwise my ranking could have been higher. The problems were neither too easy nor too hard; overall this was a well-designed contest.

Ling Shen's rating data:
1876,Substrings of Size Three with Distinct Characters,substrings-of-size-three-with-distinct-characters,1248.7224675206
1877,Minimize Maximum Pair Sum in Array,minimize-maximum-pair-sum-in-array,1301.3817574010
1878,Get Biggest Three Rhombus Sums in a Grid,get-biggest-three-rhombus-sums-in-a-grid,1897.5516652727
1879,Minimum XOR Sum of Two Arrays,minimum-xor-sum-of-two-arrays,2145.1839952670

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-biweekly-contest-53/en-hero.webp" alt="A three-slot window tests distinct beads, heavy and light weights pair across balances, diagonal rails rank three rhombus sums and a masked matching table minimizes prism light" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## 1876. Substrings of Size Three with Distinct Characters

A warm-up problem. Because the substring length is fixed at 3, we can use a sliding window to enumerate every substring of length 3 and check whether each character appears only once.

```cpp
class Solution {
public:
    int countGoodSubstrings(string s) {
        const int n = s.size();
        vector<int> cnt(26, 0);
        auto ok = [&]() -> bool {
            for (int i : cnt) {
                if (i > 1) return false;
            }
            return true;
        };
        if (n < 3) return 0;
        int ans = 0;
        ++cnt[s[0] - 'a'];
        ++cnt[s[1] - 'a'];
        ++cnt[s[2] - 'a'];
        if (ok()) ++ans;
        for (int i = 3; i < n; ++i) {
            ++cnt[s[i] - 'a'];
            --cnt[s[i - 3] - 'a'];
            if (ok()) ++ans;
        }
        return ans;
    }
};
```

Time complexity: O(n),
Space complexity: O(1).

## 1877. Minimize Maximum Pair Sum in Array

Greedy: pair large numbers with small numbers.
Pair the maximum with the minimum, and the minimum with the maximum.

```cpp
class Solution {
public:
    int minPairSum(vector<int>& nums) {
        // greedy: match largest and smallest
        sort(nums.begin(), nums.end());
        const int n = nums.size();
        int l = 0, r = n - 1;
        int ans = 0;
        while (l < r) {
            ans = max(ans, nums[l++] + nums[r--]);
        }
        return ans;
    }
};
```

Time complexity: O(N log N),
Space complexity: O(1).


## 1878. Get Biggest Three Rhombus Sums in a Grid

Enumerate all rhombuses, then maintain the largest three rhombus sums.
My enumeration method is to enumerate the center of the rhombus and the distance from the four vertices to the center.
The rhombus sum can be calculated in O(1) with prefix sums `presum`. Therefore the overall time complexity is O(N^3). The maximum N is 100, just enough to satisfy the constraints.

Later I found that LeetCode changed the data range from 100 to 50. I had heard that during the contest some people passed with an O(N^4) brute-force solution. I thought there would be a rejudge after the contest, but instead they simply changed the constraints in the statement and let everyone pass. Maybe they were too lazy to rejudge and add large test cases. Changing the data range is so much easier.

```cpp
class Solution {
public:
    vector<int> getBiggestThree(vector<vector<int>>& grid) {
        // x := Rhombus number: m * n * m
        // time: x log x
        const int m = grid.size();
        const int n = grid[0].size();
        
        vector<vector<int>> presum(m + 1, vector<int>(n + 1, 0));
        vector<vector<int>> presum2(m + 1, vector<int>(n + 1, 0));
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                presum[i+1][j+1] = presum[i][j] + grid[i][j];
            }
        }
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                presum2[i+1][j] = presum2[i][j+1] + grid[i][j];
            }
        }
        
        
        set<int> s;
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                s.insert(grid[i][j]);
                for (int len = 1; i - len >= 0 && j - len >= 0 && i + len < m && j + len < n; ++len) {
                    const int rhombus = presum[i+1][j+len+1] - presum[i-len][j]
                        + presum[i+len+1][j+1] - presum[i][j-len]
                        + presum2[i+len+1][j] - presum2[i][j+len+1]
                        + presum2[i+1][j-len] - presum2[i-len][j+1]
                        - grid[i-len][j] - grid[i][j+len] - grid[i][j-len] - grid[i+len][j];
                    s.insert(rhombus);
                }
            }
        }
        vector<int> ans;
        int i = 0;
        for (auto it = s.rbegin(); it != s.rend() && i < 3; ++it, ++i) {
            ans.push_back(*it);
        }
        return ans;
    }
};
```

Number of rhombuses: `x = m * n * min(m,n)`,
Time complexity: O(x log x),
Space complexity: O(x + m * n). Since we only need to maintain the largest three rhombus sums, this can be optimized to O(m*n).

## 1879. Minimum XOR Sum of Two Arrays

Classic DP + bitmask.

At first I thought of brute-forcing all permutations. The time complexity is the number of permutations, n! = 14!, which would definitely time out.
From the data scale `n = 14`, we can infer that bitmask is needed.

Define `dp(i, mask)` as the minimum XOR sum for `nums1[i:]` and subset `mask` of `nums2`.
The transition is: brute-force each available value in `mask` to pair with `nums[i]`, and take the minimum.

```python
class Solution:
    def minimumXORSum(self, nums1: List[int], nums2: List[int]) -> int:
        # time: n * n * 2 ^ n
        n = len(nums2)
        @cache
        def dp(i, mask):
            if i >= len(nums1): return 0
            ans = float('inf')
            for j in range(n):
                if ((1 << j) & mask) == 0: continue
                ans = min(ans, (nums1[i] ^ nums2[j]) + dp(i+1, mask ^ (1 << j)))
            return ans
            
        return dp(0, (1 << n) - 1)
```

Time complexity: O(n * n * 2 ^ n)  = 14 * 14 * 2^14 = 3211264,
Space complexity: O(n * 2^ n).
