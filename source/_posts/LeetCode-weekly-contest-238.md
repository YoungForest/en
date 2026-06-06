---
title: LeetCode weekly contest 238
date: 2021-04-25 17:58:13
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 383 / 11635 | YoungForest | 18 | 1:53:50 | 0:01:05 | 0:13:02 |  0:20:59 | 1:28:50 5 |

<!-- more -->

## 1837. Sum of Digits in Base K

A warm-up problem. Convert from decimal to base `k`.

```python
class Solution:
    def sumBase(self, n: int, k: int) -> int:
        ans = 0
        while n > 0:
            ans += n % k
            n //= k
        return ans
```

Time complexity: O(log_k n),
Space complexity: O(1).

## 1838. Frequency of the Most Frequent Element

Q2 and Q3 in this week's contest were both sliding window problems. In fact, judging from the data, Q2 was still harder than Q3.

First, think of the brute-force solution: try every element and attempt to increase all smaller elements up to it, then see the maximum possible count.

Time complexity: O(N ^ 2), likely TLE.

Usually Q2 can still be solved by brute force, although not this problem.

Based on the brute-force solution, try to optimize it. Observe that the operation "increase all smaller elements up to it" may be done in O(1), without brute-force trying every smaller element, because those elements have already been increased to the previous tried value.

Sort first. Then, from small to large, try to increase all values to `nums[r]`.

The window `[l:r]` maintains the elements that have been increased to the target value `nums[r]`.

When `r` moves right, update all elements in the window from the previous value to `nums[r]`.

If too many increment operations are used, increase `l` to release some increment operations.

Take the widest window as the answer.

```python
class Solution:
    def maxFrequency(self, nums: List[int], k: int) -> int:
        # brute force: N ^ 2
        # for each element, try to make it most frequency
        # sliding window
        nums.sort()
        ans = 0
        l = 0
        for r in range(len(nums)):
            if r > 0:
                # make all increment to nums[r]
                k -= (nums[r] - nums[r-1]) * (r - l)
            while k < 0:
                k += nums[r] - nums[l]
                l += 1
            ans = max(r - l + 1, ans)
            
        return ans
```

Time complexity: O(n log n + n),
Space complexity: O(1).

## 1839. Longest Substring Of All Vowels in Order

Compared with the previous problem, this one is even more obviously a sliding window problem.

The invariant of the window `[l:r]` is that the substring is increasing.

```python
class Solution:
    def longestBeautifulSubstring(self, word: str) -> int:
        # brute force: N^3, enumerate all substring * check each substring
        # sliding window: N
        # at least once, incresing
        l = 0
        r = 0
        n = len(word)
        cnt = [0] * 26
        def check():
            for i in 'aeiou':
                if cnt[ord(i) - ord('a')] <= 0:
                    return False
            return True
        ans = 0
        while r < n:
            cnt[ord(word[r]) - ord('a')] += 1
            if r > 0 and word[r] < word[r-1]:
                while l < r:
                    cnt[ord(word[l]) - ord('a')] -= 1
                    l += 1
            if check():
                ans = max(ans, r - l + 1)
            r += 1
        return ans
```

Time complexity: O(word.length),
Space complexity: O(1).

## 1840. Maximum Building Height

This was still a pretty difficult Hard problem.

The height difference between adjacent buildings is 1. At first, I thought of using BFS: start from the smaller height restrictions and traverse outward to update nearby heights. That is also a classic algorithm.

After TLE, I finally noticed the constraint `n <= 10^9`. An O(N) algorithm would definitely time out.

Then notice `restrictions.length <= 10^5`; most likely we need to start from there.

Therefore, during BFS, only update the restricted blocks, and then use binary search to find the highest point between blocks. In fact, this can be solved with an O(1) mathematical formula, but during the contest I did not have a good idea, so I used brute-force binary search.

```cpp
class Solution {
    const int INF = 0x3f3f3f3f;
public:
    int maxBuilding(int n, vector<vector<int>>& restrictions) {
        map<int, int> rtxMap;
        unordered_map<int, bool> seen;
        seen.reserve(restrictions.size() + 2);
        rtxMap[0] = 0;
        rtxMap[n-1] = INF;
        seen[0] = false;
        seen[n-1] = false;
        // bfs: O(N), TLE
        using pii = pair<int, int>;
        priority_queue<pii, vector<pii>, greater<>> pq;
        pq.push({0, 0});
        for (const auto& v : restrictions) {
            pq.push({v[1], v[0] - 1});
            rtxMap[v[0] - 1] = v[1];
            seen[v[0] - 1] = false;
        }
        int ans = 0;
        while (!pq.empty()) {
            auto [height, idx] = pq.top();
            // cout << height << ", " << idx << endl;
            pq.pop();
            if (seen[idx]) continue;
            ans = max(ans, height);
            seen[idx] = true;
            for (const int j : {-1, 1}) {
                const int next = idx + j;
                if (next >= n || next < 0 || seen[next]) continue;
                if (j == 1) {
                    auto it = rtxMap.lower_bound(next);
                    if (it != rtxMap.end()) {
                        const int x = it->first;
                        it->second = min(it->second, height + x - idx);
                        pq.push({it->second, x});
                    }
                } else {
                    auto it = rtxMap.lower_bound(idx);
                    // 2 5
                    // next = 4
                    if (it != rtxMap.begin()) {
                        --it;
                        const int x = it->first;
                        it->second = min(it->second, height + idx - x);
                        pq.push({it->second, x});
                    }
                }
            }
        }
        
        for (auto it = rtxMap.begin(); next(it) != rtxMap.end(); ++it) {
            const int leftIdx = it->first, leftHeight = it->second;
            const int rightIdx = next(it)->first, rightHeight = next(it)->second;
            if (leftIdx + 1 == rightIdx) continue;
            // cout << leftIdx << ", " << leftHeight << ", " << rightIdx << ", " << rightHeight << endl;
            int lo = min(leftHeight, rightHeight), hi = min(leftHeight + rightIdx - leftIdx, rightHeight + rightIdx - leftIdx);
            while (lo < hi) {
                const int mid = lo + (hi - lo) / 2;
                bool deter = false;
                if (mid - leftHeight < rightIdx - leftIdx and mid - rightHeight < rightIdx - leftIdx and mid - leftHeight + mid - rightHeight - 1 < rightIdx - leftIdx) deter = true;
                if (deter) {
                    lo = mid + 1;
                } else {
                    hi = mid;
                }
                // t, t, t, f, f
            }
            ans = max(ans, lo - 1);
        }
        return ans;
    }
};
```

`m = restrictions.length`

Time complexity: O(m log m),
Space complexity: O(m).

[LingShen's solution](https://leetcode-cn.com/problems/maximum-building-height/solution/zui-gao-jian-zhu-gao-du-by-leetcode-solu-axbb/910208) has a two-pass algorithm. Although the time complexity is the same, the implementation is simpler and the constant factor should be better.
