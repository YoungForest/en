---
title: LeetCode Biweekly Contest 52
date: 2021-05-16 19:13:00
description: Three solutions sort sentence pieces, simulate growing memory allocations and rotate a box; the floor-pair attempt times out before a prefix-sum reference.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/05/16/LeetCode-biweekly-contest-52/
  en: https://youngforest.github.io/en/2021/05/16/LeetCode-biweekly-contest-52/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 2217 / 10364 | YoungForest | 12 | 0:51:47 | 0:05:11 | 0:51:47 | 0:29:51 | null |

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-biweekly-contest-52/en-hero.webp" alt="Notched blank word blocks sort into order, growing loads drain two memory reservoirs, stones settle after a box turns and a quotient-bucket machine remains jammed beside a later prefix rail" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## 1859. Sorting the Sentence

Use `python` for string problems. Although the problem is not hard, just splitting the string and sorting according to the specified content, the implementation is still more complex than expected. Fortunately, Python has many convenient APIs and data structures that can be used flexibly.

```python
class Solution:
    def sortSentence(self, s: str) -> str:
        words = s.split(' ')
        def f(word):
            return (int(word[-1:]), word[0:-1])
        tuples = list(map(f, words))
        tuples.sort()
        def f2(a, t):
            return a + ' ' + t[1] 
        return reduce(f2, tuples, '')[1:]
```

Time complexity: O(s.length),
space complexity: O(s.length).

## 1860. Incremental Memory Leak

Originally I wanted to solve it in O(1) using the arithmetic-series sum and solving equations. But in reality, implementation and equation solving were still too complicated. I did not finish it in the end. After finishing the third problem, I came back and calculated the brute-force time complexity: O(sqrt(2^32)) = 65536. Surprisingly low. Sure enough, the second problem should not be overcomplicated; direct brute force would have been much better.

Brute-force simulation of the memory allocation process described in the problem.

```cpp
class Solution {
public:
    vector<int> memLeak(int m1, int m2) {
        int t = 1;
        while (m1 >= t || m2 >= t) {
            if (m1 >= m2) {
                m1 -= t;
            } else {
                m2 -= t;
            }
            ++t;
        }
        return {t, m1, m2};
    }
};
```

Time complexity: O(sqrt(memory1 + memory2)),
space complexity: O(1).

## 1861. Rotating the Box

After understanding the essence of falling, you can see that it is only necessary to count how many stones exist between the left side of each obstacle/ground and the previous obstacle.

```cpp
class Solution {
public:
    vector<vector<char>> rotateTheBox(vector<vector<char>>& box) {
        const int rows = box.size();
        const int cols = box[0].size();
        vector<vector<char>> ans(cols, vector<char>(rows, '.'));
        for (int i = 0; i < rows; ++i) {
            int stoneCnt = 0;
            for (int j = 0; j < cols; ++j) {
                if (box[i][j] == '#') {
                    ++stoneCnt;
                } else if (box[i][j] == '*') {
                    // fall down
                    ans[j][rows - 1 - i] = '*';
                    for (int k = 1; k <= stoneCnt; ++k) {
                        ans[j - k][rows - 1 - i] = '#';
                    }
                    stoneCnt = 0;
                }
            }
            for (int k = 1; k <= stoneCnt; ++k) {
                ans[cols - k][rows - 1 - i] = '#';
            }
        }
        return ans;
    }
};
```

Time complexity: O(m * n),
space complexity: O(m * n).

## 1862. Sum of Floored Pairs

I did not have a good idea. First I tried the brute-force solution, enumerating all pairs:

```python
class Solution:
    def sumOfFlooredPairs(self, nums: List[int]) -> int:
        MOD = 10**9 + 7
        n = len(nums)
        ans = 0
        for i in range(n):
            for j in range(n):
                ans += nums[j] // nums[i]
        return ans % MOD
```

Time complexity: O(N^2), TLE,
space complexity: O(1).

Then I tried optimizing with binary search, finding the multiple ranges for each number.

```cpp
class Solution {
    const int MOD = 1e9 + 7;
public:
    int sumOfFlooredPairs(vector<int>& nums) {
        int ans = 0;
        sort(nums.begin(), nums.end());
        const int MAX = nums.back();
        const int n = nums.size();
        int last = 0;
        for (int i = 0; i < n; ++i) {
            if (i > 0 && nums[i] == nums[i-1]) {
                ans = (ans + last) % MOD;
                continue;
            }
            last = 0;
            for (int j = 1; nums[i] * j <= MAX; ++j) {
                auto l = lower_bound(nums.begin() + i, nums.end(), nums[i] * j);
                auto r = lower_bound(l, nums.end(), nums[i] * (j + 1));
                auto x = distance(l, r);
                last = (last + x * j) % MOD;
            }
            ans = (ans + last) % MOD;
        }
        return ans;
    }
};
```

Time complexity: O(N log N log N). Although there are two nested loops, the second is actually a harmonic series. Still, it TLE'd.

Space complexity: O(1).

I should just read LingShen's solution:

[Prefix-sum optimization](https://leetcode-cn.com/problems/sum-of-floored-pairs/solution/xiang-xia-qu-zheng-shu-dui-he-by-leetcod-u3eg/).
