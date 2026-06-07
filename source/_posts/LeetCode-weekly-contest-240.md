---
title: LeetCode Weekly Contest 240
date: 2021-05-09 17:18:34
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 272 / 11577 | YoungForest | 18 | 1:15:29 | 0:04:46 | 0:14:19 | 0:47:06 | 1:10:29  1 |

This week had the May Day holiday plus thesis plagiarism checking. My mood was made extremely anxious by the thesis. Just hold on for two more weeks; after getting through the defense, everything will be fine.

After the defense is over, I will be willing to do anything.

In the previous few weeks, because my China-site rating was too high, I did not dare to compete there, afraid of losing rating. I moved to the US site, and now I have also pushed the US-site rating to 2350. That rating is also too high. Later I plan to move back to the China site again, because the new recruitment season is approaching, and China-site sponsor gifts are relatively generous. Although last year I only ranked high enough a few times and received prizes, at least there was a reward and a small expectation.

<!-- more -->

## 1854. Maximum Population Year

A warm-up problem. Brute-force enumerate every possible year.

```cpp
class Solution {
public:
    int maximumPopulation(vector<vector<int>>& logs) {
        map<int, int> cnt;
        for (const auto& v : logs) {
            for (int i = v[0]; i < v[1]; ++i) {
                ++cnt[i];
            }
        }
        int maxValue = 0;
        int maxYear = -1;
        for (const auto& p : cnt) {
            if (p.second > maxValue) {
                maxYear = p.first;
                maxValue = p.second;
            }
        }
        return maxYear;
    }
};
```

Time complexity: O(logs.length * MAX(death_i - birth_i)) = O(100 * 100),
space complexity: O(MAX(death_i - birth_i)).

## 1855. Maximum Distance Between a Pair of Values

```cpp
class Solution {
public:
    int maxDistance(vector<int>& nums1, vector<int>& nums2) {
        int ans = 0;
        for (int i = 0; i < nums1.size(); ++i) {
            auto it = upper_bound(nums2.begin() + i, nums2.end(), nums1[i], greater<>());
            ans = max(ans, static_cast<int>(distance(nums2.begin() + i, prev(it))));
        }
        return ans;
    }
};
```

Time complexity: O(nums1.length * log nums2.length),
space complexity: O(1).

This problem can also be solved with two pointers. `i` points to the position in `nums1`, and `j` points to the position in `nums2`.

Traverse `i` to the right and update `j`. The invariant is `j >= i && nums[j] < nums[i]`.

The time complexity is O(nums1.length + nums2.length).

Code below:

```cpp
class Solution {
public:
    int maxDistance(vector<int>& nums1, vector<int>& nums2) {
        int ans = 0;
        for (int i = 0, j = 0; i < nums1.size(); ++i) {
            while (j < nums2.size() && (j < i || nums2[j] >= nums1[i])) ++j;
            ans = max(ans, j - 1 - i);
        }
        return ans;
    }
};
```

## 1856. Maximum Subarray Min-Product

Similar to BFS, traverse elements from larger values downward and try to merge the left and right intervals.

The specific implementation needs to maintain interval left/right boundaries, minimum values, and interval sums. It can use an idea similar to Union-Find.

```cpp
class Solution {
    using ll = long long;
    const ll MOD = 1e9 + 7;
    const int INF = 0x3f3f3f3f;
public:
    int maxSumMinProduct(vector<int>& nums) {
        const int n = nums.size();
        // iterate from max to min
        vector<ll> s(n, 0);
        vector<int> right(n, 0);
        vector<int> left(n, 0);
        vector<int> minV(n, INF);
        multimap<int, int, greater<>> index;
        for (int i = 0; i < n; ++i) {
            index.insert({nums[i], i});
            minV[i] = nums[i];
            left[i] = i;
            right[i] = i;
            s[i] = nums[i];
        }
        ll ans = 0;
        for (const auto& p : index) {
            const int i = p.second;
            const ll minValue = p.first;
            if (i > 0 && minV[i-1] >= minValue) {
                left[i] = left[i-1];
                s[i] += s[i-1];
            }
            if (i + 1 < n && minV[i+1] > minValue) {
                right[i] = right[i+1];
                s[i] += s[i+1];
            }
            minV[right[i]] = minValue;
            minV[left[i]] = minValue;
            right[left[i]] = right[i];
            left[right[i]] = left[i];
            s[left[i]] = s[i];
            s[right[i]] = s[i];
            ans = max(ans, s[i] * minValue);
        }
        return ans % MOD;
    }
};
```

Time complexity: O(n * log n),
space complexity: O(n).

After the contest, after discussion with friends in the Cruel Coding group:

Similar problems include:

[LC 84. Largest Rectangle in Histogram](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/). The difference is that 84 is `minimum value * subarray length`, while this problem is `minimum value * subarray sum`.


## 1857. Largest Color Value in a Directed Graph

Topological sort. Meanwhile, when traversing to each node, update the maximum count of each color among paths that can reach it.

```python
class Solution:
    def largestPathValue(self, colors: str, edges: List[List[int]]) -> int:
        n = len(colors)
        m = len(edges)
        indegree = [0] * n
        graph = defaultdict(list)
        for e in edges:
            indegree[e[1]] += 1
            graph[e[0]].append(e[1])
            
        # topo sort
        def dfs(root):
            ans = [0] * 26
            ans[ord(colors[root]) - ord('a')] += 1
            for n in graph[root]:
                if n in path: return None
                x = dfs(n, path)
                if x is None: return None
                for i in range(26):
                    ans[i] += x[i]
            return ans
        ans = -1
        q = collections.deque()
        seen = 0
        cnt = [[0] * 26 for i in range (n)]
        for i in range(n):
            if indegree[i] == 0:
                cnt[i][ord(colors[i]) - ord('a')] += 1
                q.append(i)
                seen += 1
                ans = max(ans, max(cnt[i]))
                
        while q:
            front = q.popleft()
            for i in graph[front]:
                for x in range(26):
                    cnt[i][x] = max(cnt[i][x], cnt[front][x])
                indegree[i] -= 1
                if indegree[i] == 0:
                    q.append(i)
                    cnt[i][ord(colors[i]) - ord('a')] += 1
                    ans = max(ans, max(cnt[i]))
                    seen += 1
                
        if seen == n: return ans
        else: return -1
```

Time complexity: O(n + m),
space complexity: O(n + m).
