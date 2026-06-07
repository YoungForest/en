---
title: LeetCode Biweekly Contest 39
date: 2020-11-15 09:56:39
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 688 / 6047 | YoungForest | 7 | 0:16:02 | 0:03:44 | 0:11:02  1 |  null |  0:41:26 |

This week's biweekly contest was still quite difficult. Although the point values were 3/4/5/6, I think the third and fourth problems were worth more than 5/6 points. Of course, I only solved the first two. For the third and fourth problems, I only tried brute-force solutions, but facts proved that although they were very close to the correct answers, they all TLEed.

## 5550. Defuse the Bomb

Warm-up problem. Use an auxiliary array and fill it according to the problem statement.

```cpp
class Solution {
public:
    vector<int> decrypt(vector<int>& code, int k) {
        const int n = code.size();
        vector<int> ans(n, 0);
        if (k == 0) {
            return ans;
        } else if (k > 0) {
            for (int i = 0; i < n; ++i) {
                for (int j = 1; j <= k; ++j) {
                    ans[i] += code[(i + j) % n];
                }
            }
            return ans;
        } else {
            k = -k;
            for (int i = 0; i < n; ++i) {
                for (int j = 1; j <= k; ++j) {
                    ans[i] += code[(i - j + n) % n];
                }
            }
            return ans;
        }
    }
};
```

Time complexity: O(N),
space complexity: O(N).

## 5551. Minimum Deletions to Make String Balanced

Enumerate every split point. Delete all `b`s before the split point and all `a`s after the split point. Find the minimum number of deletions.

```cpp
class Solution {
public:
    int minimumDeletions(string s) {
        const int n = s.size();
        int ans = n;
        const int totalA = count_if(s.begin(), s.end(), [](char c) -> bool {
            return c == 'a';
        });
        int a = 0, b = 0;
        for (int i = 0; i < n; ++i) {
            ans = min(ans, b + totalA - a);
            if (s[i] == 'a') ++a;
            else ++b;
        }
        ans = min(ans, b + totalA - a);
        return ans;
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 1654. Minimum Jumps to Reach Home

To find the minimum number of steps, use BFS. However, the key to this problem is how to control the state space, namely avoiding walking to the right forever. A natural idea is to have a threshold. After exceeding this threshold, you can only walk left. For how to determine the threshold, refer to [the Cruel group owner's explanation](https://github.com/wisdompeak/LeetCode/tree/master/BFS/1654.Minimum-Jumps-to-Reach-Home).

```cpp
class Solution {
public:
    int minimumJumps(vector<int>& forbidden, int a, int b, int x) {
        using pii = pair<int, bool>; // location, backward
        unordered_set<int> blocks;
        for (int i : forbidden) blocks.insert(i);
        queue<pii> q;
        set<pii> seen;
        q.push({0, false});
        seen.insert({0, false});
        const int limits = max(x, *max_element(forbidden.begin(), forbidden.end())) + b;
        int level = 0;
        while (!q.empty()) {
            const int s = q.size();
            for (int i = 0; i < s; ++i) {
                auto [location, back] = q.front();
                // cout << location << " " << back << endl;
                q.pop();
                if (location == x) return level;
                if (location <= limits) {
                    // forward
                    const int nextLocation = location + a;
                    if (blocks.find(nextLocation) == blocks.end() && seen.find({nextLocation, false}) == seen.end()) {
                        q.push({nextLocation, false});
                        seen.insert({nextLocation, false});
                    }
                }
                if (!back){
                    // backward
                    const int nextLocation = location - b;
                    if (nextLocation > 0 && blocks.find(nextLocation) == blocks.end() && seen.find({nextLocation, true}) == seen.end()) {
                        q.push({nextLocation, true});
                        seen.insert({nextLocation, true});
                    }
                }
            }
            ++level;
        }
        return -1;
    }
};
```

Time complexity: O(max(x, max_forbidden) + b),
space complexity: O(max(x, max_forbidden) + b).

## 5553. Distribute Repeating Integers

During the contest, I only thought of brute-force backtracking and tried to optimize it with pruning, but it still timed out.
The time complexity is O(50^10), so theoretically it just cannot pass.

After reading [a solution in the discussion section](https://leetcode-cn.com/problems/distribute-repeating-integers/solution/zi-ji-mei-ju-jing-dian-tao-lu-zhuang-ya-dp-by-arse/), I suddenly realized this is a state-compression DP problem.

[Huahua's video explanation]() is also especially clear. Here I implemented his Python top-down version, which is easier to write and understand than bottom-up. Note that there is a special trick when enumerating subsets of a set.

```python
class Solution:
    def canDistribute(self, nums: List[int], quantity: List[int]) -> bool:
        cnts = list(Counter(nums).values())
        m = len(quantity)
        n = len(cnts)
        sums = [0] * (1 << m)
        for mask in range(1 << m):
            for i in range(m):
                if mask & (1 << i):
                    sums[mask] += quantity[i]
        @lru_cache(None)
        def dp(mask: int, i: int) -> bool:
            if mask == 0: return True
            if i >= n: return False
            cur = mask
            while cur:
                if sums[cur] <= cnts[i] and dp(mask ^ cur, i + 1):
                    return True
                cur = (cur - 1) & mask
            return dp(mask, i + 1)

        return dp((1 << m) - 1, 0)
```

Time complexity: O(3^m * n),
space complexity: O(2^m * n).
