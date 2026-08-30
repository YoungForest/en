---
title: LeetCode Biweekly Contest 38
date: 2020-11-01 08:56:43
description: A biweekly review of frequency sorting, the widest empty vertical strip, brute-force substring pairs differing once, and memoized column-frequency DP.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/11/01/LeetCode-biweekly-contest-38/
  en: https://youngforest.github.io/en/2020/11/01/LeetCode-biweekly-contest-38/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-biweekly-contest-38/en-hero.webp" alt="Coloured stones reorder by pile height, the widest empty lane opens between point towers, paired bead windows differ once and a column cabinet feeds a remembered target chain" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 318 / 7446 | YoungForest | 18 | 1:07:29 | 0:12:38 | 0:16:42 |  0:57:29  2 |  0:41:26 |

## 5539. Sort Array by Increasing Frequency

Warm-up problem. First count frequencies according to the problem statement, then sort by frequency.

```cpp
class Solution {
public:
    vector<int> frequencySort(vector<int>& nums) {
        unordered_map<int, int> cnt;
        for (int i : nums) {
            ++cnt[i];
        }
        using pii = pair<int,int>;
        vector<pii> frequency;
        for (const auto& p : cnt) {
            frequency.emplace_back(p.second, p.first);
        }
        sort(frequency.begin(), frequency.end(), [&](const auto& a, const auto& b) -> bool {
            if (a.first == b.first) {
                return a.second > b.second;
            } else {
                return a.first < b.first;
            }
        });
        vector<int> ans;
        for (const auto& p : frequency) {
            for (int i = 0; i < p.first; ++i) {
                ans.push_back(p.second);
            }
        }
        return ans;
    }
};
```

Time complexity: O(N log N),
space complexity: O(N).

## 5540. Widest Vertical Area Between Two Points Containing No Points

Although it looks complicated, it is actually just sorting by x and finding the largest gap. The problem statement deliberately made it sound harder and led everyone around in a circle.

```cpp
class Solution {
public:
    int maxWidthOfVerticalArea(vector<vector<int>>& points) {
        const int n = points.size();
        vector<int> x;
        x.reserve(n);
        for (const auto& v : points) {
            x.push_back(v[0]);
        }
        sort(x.begin(), x.end());
        int ans = 0;
        int lastx = x[0];
        for (int i = 1; i < n; ++i) {
            ans = max(ans, x[i] - lastx);
            lastx = x[i];
        }
        return ans;
    }
};
```

Time complexity: O(N log N),
space complexity: O(N).

## 5541. Count Substrings That Differ by One Character

This problem is also brute force. But at first I overcomplicated it and thought brute force was N^4, while actually it is N^3. So I skipped it and did the fourth problem first. After coming back, I still tried to use a so-called 25*N^3 algorithm and TLEed twice. After my roommate reminded me, I suddenly realized that it really was brute force.

```cpp
class Solution {
public:
    int countSubstrings(string s, string t) {
        int ans = 0;
        for (int si = 0; si < s.size(); ++si) {
            for (int sj = 1; si + sj <= s.size(); ++sj) {
                for (int ti = 0; ti < t.size(); ++ti) {
                    if (ti + sj > t.size()) break;
                    int diff = 0;
                    for (int x = 0; x < sj; ++x) {
                        if (s[si + x] != t[ti + x]) {
                            ++diff;
                            if (diff > 1) goto end;
                        }
                    }
                    if (diff == 1) ++ans;
                    end:;
                }
            }
        }
        return ans;
    }
};
```

Time complexity: O(N^3),
space complexity: O(1).

## 5542. Number of Ways to Form a Target String Given a Dictionary

Classic DP. `dp(begin, i)` represents the number of ways to form `target[i:]` starting from the `begin`-th character of `words`.
The state transition equation is:
`dp(begin, i) = dp(begin + 1, i + 1) * (the number of times target[i] appears in words[j][begin]) + dp(begin + 1, i)`

```python
class Solution:
    def numWays(self, words: List[str], target: str) -> int:
        MOD = int(10**9 + 7)
        n = len(words[0])
        dictionary = defaultdict(lambda : defaultdict(int))
        for w in words:
            for i in range(n):
                dictionary[i][w[i]] += 1
            
        @lru_cache(None)
        def dp(begin, i):
            if i == len(target):
                # print ('return ', begin, i)
                return 1
            if begin >= n:
                return 0
            appear = dictionary[begin][target[i]]
            # print (begin, i)
            return (dp(begin + 1, i + 1) * appear + dp(begin + 1, i)) % MOD
        return dp(0, 0)
```

Time complexity: O(words.size() * words[0].size() + words[0].size() * target.size()),
space complexity: O(words[0].size() * target.size()).
