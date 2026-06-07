---
title: LeetCode 2021 Spring Contest - Solo
date: 2021-04-05 19:27:19
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (2) |	Q2 (4) |	Q3 (6) |	Q4 (8)| Q5 (10) |
|--|--|--|--|--|--|--|--|--|
| 171 / 2750 | YoungForest | 12 | 0:56:51 | 0:06:21 | 0:49:14 | 0:56:55 | null | null |

[Contest link](https://leetcode-cn.com/contest/season/2021-spring/ranking/solo/)

## LCP 28. Purchase Plans

A warm-up problem. We can see that a total of 2750 contestants checked in.

Binary search.

```cpp
class Solution {
    const int MOD = 1e9 + 7;
public:
    int purchasePlans(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());
        // 1 2 4 5 6/
        // target = 5
        // 2
        int ans = 0;
        for (int i = 0; i < nums.size(); ++i) {
            const int x = nums[i];
            auto it = upper_bound(nums.begin(), nums.begin() + i, target - x);
            ans = (ans + distance(nums.begin(), it)) % MOD;
        }
        return ans;
    }
};
```

Time complexity: O(N log N),
space complexity: O(1).

## LCP 29. Orchestra Layout

At first, it is very easy to think of simulating and filling in numbers, with time complexity O(N^2). Since `n` can be as large as `10^9`, that obviously times out. We need to consider a better solution.

By observation, the number-filling pattern is very clear. We actually only need to calculate the position relative to the starting point. By calculating the target point's distance from the four sides, we can know how many layers exist outside it. Then using the arithmetic-series sum, we can quickly compute how many numbers are in the outer layers. After that, the problem is simplified to the case where the target point is definitely on the outermost ring. Split into four cases, top edge, right edge, bottom edge, and left edge, and calculate the target point's distance from the upper-left corner.

Although thinking of this O(1) solution is not difficult, the implementation is still somewhat challenging because it involves arithmetic-series sums, four edge cases, and other details. It is also quite easy to make mistakes accidentally. During the contest I wrote test cases covering all situations myself, fixed a few bugs, and then passed on the first submission.

```cpp
class Solution {
    using ll = long long;
public:
    int helper(int n, int x, int y) {
        // Sn=n*a1+n(n-1)d/2
        const ll left = y, right = n - 1 - y, up = x, down = n - 1 - x;
        const ll shell = min({left, right, up, down});
        const ll a1 = n - 1;
        const ll d = -2;
        ll shellAmount = 0;
        if (shell > 0) {
            shellAmount = 4 * (shell * a1 + shell * (shell - 1) * d / 2);
        }
        const ll startX = shell, startY = shell, len = n - 2 * shell;
        const ll start = (shellAmount) % 9;
        // cout << shell << " " << start << " " << startX << " " << startY << endl;
        if (shell == up) {
            return (y - startY + start) % 9;
        } else if (shell == right) {
            return (x - startX + start + len - 1) % 9;
        } else if (shell == down) {
            return (len - 1 - (y - startY) + start + 2 * (len - 1)) % 9;
        } else { // shell == left
            return (len - 1 - (x - startX) + start + 3 * (len - 1)) % 9;
        }
    }
    int orchestraLayout(int n, int x, int y) {
        return helper(n, x, y) + 1;
    }
};
```

Time complexity: O(1),
space complexity: O(1).

## LCP 30. Magic Tower Game

Greedy.

First traverse once to judge whether the sum is greater than or equal to 0, deciding whether final victory is possible.

When HP drops below 1, move the monster that previously caused the largest HP loss to the end.

Use a priority queue to maintain monsters' HP losses, so the largest can be found quickly.

```cpp
class Solution {
    using ll = long long;
public:
    int magicTower(vector<int>& nums) {
        ll total = 0;
        for (int x : nums) {
            total += x;
        }
        if (total < 0) return -1;
        ll current = 1;
        priority_queue<int> pq;
        int ans = 0;
        for (int x : nums) {
            current += x;
            if (x < 0)
                pq.push(-x);
            if (current <= 0) {
                current += pq.top();
                pq.pop();
                ++ans;
            }
        }
        return ans;
    }
};
```

Time complexity: O(N log N),
space complexity: O(N).

## LCP 31. Transforming Maze

During the contest, I only came up with a brute-force DFS + DP solution.

Time complexity: O(times * rows * cols * 2 * rows * cols * 5) = 100 * 50 * 50 * 2 * 50 * 50 * 5 = 6,250,000,000. Unsurprisingly, it TLE'd.

Even so, I still include my TLE version here, because I think this is a decent approach for similar path problems and can handle most medium problems.

```python
class Solution:
    def escapeMaze(self, maze: List[List[str]]) -> bool:
        maxT = len(maze)
        rows = len(maze[0])
        cols = len(maze[0][0])
        nextStep = [(1, 0), (0, 1), (-1, 0), (0, -1), (0, 0)]
        @lru_cache(None)
        def dfs(t, i, j, temp, forever):
            if t >= maxT: return False
            if i == rows - 1 and j == cols - 1: return True
            maxTry = rows - 1 - i + cols - 1 - j
            if maxTry > maxT - t - 1: return False
            for di, dj in nextStep:
                ni = i + di
                nj = j + dj
                if ni >= 0 and ni < rows and nj >= 0 and nj < cols and t + 1 < maxT:
                    if maze[t + 1][ni][nj] == '.':
                        if (dfs(t + 1, ni, nj, temp, forever)):
                            return True
                    else: # '#'
                        if forever == (ni, nj):
                            if (dfs(t + 1, ni, nj, temp, forever)):
                                return True
                        else:
                            if forever == (0, 0):
                                if (dfs(t + 1, ni, nj, temp, (ni, nj))):
                                    return True
                            if temp:
                                if (dfs(t + 1, ni, nj, False, forever)):
                                    return True
            return False
        
        ans = dfs(0, 0, 0, True, (0, 0))
        dfs.cache_clear()
        return ans;
```

After the contest, I still needed to learn from the stronger players' solutions. In fact, only 100 contestants passed during the contest.

[This solution](https://leetcode-cn.com/problems/Db3wC1/solution/ji-yi-hua-sou-suo-si-lu-xiang-jie-by-hal-mmnj/) was, after reading all the highly liked answers, actually all with single-digit likes, the most convincing and best solution I found. It can also be read together with [another solution](https://leetcode-cn.com/problems/Db3wC1/solution/dong-tai-gui-hua-he-xin-yao-dian-you-hua-gnas/); the ideas are similar.

The core of the problem is:

> If a trap at a position is eliminated "permanently", we can pass through this position multiple times. But this is equivalent to staying at this position for a period of time. For example, if the path is a -> b -> a -> c -> a, then it is equivalent to staying at a without moving.

Therefore, the factor used in the time complexity to enumerate the permanent-disappearance-spell position can be removed, eliminating `50*50` and reducing it to `5,000,000`.

## LCP 32. Process Tasks in Batches

A truly difficult problem. During the contest, only 31 people passed it. There were also far fewer solutions.

I read quite a few ACM-level solutions using advanced algorithms, and I could not even understand their code.

I found this [greedy-solution write-up](https://leetcode-cn.com/problems/t3fKg1/solution/c-tan-xin-by-zqy1018-03bd/) and share it with everyone.
