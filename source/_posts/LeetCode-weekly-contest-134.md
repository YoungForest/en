---
title: LeetCode weekly contest 134
date: 2019-04-28 20:32:34
description: Corner-case contest notes on moving stones, coloring a border, longest common subsequences and escaping a maze by bounding blockers.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/04/28/LeetCode-weekly-contest-134/
  en: https://youngforest.github.io/en/2019/04/28/LeetCode-weekly-contest-134/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-134/en-hero.webp" alt="Forest repairs corner-case gears beside consecutive stones, a colored border, uncrossed pairings and a bounded maze escape" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (5) |	Q4 (5)|
|--|--|--|--|--|--|--|--|
|220 / 4136	|	YoungForest |	17 | 		1:45:10 | 0:14:52 (1) | 0:33:50(1) |	null | 1:25:10 (2) |

The quality of this contest continued to improve on top of last week. The corner cases in particular caused me to have 4 incorrect attempts, which means 20 minutes of penalty. But from the leaderboard, everyone seemed to be in a similar state, with many wrong attempts.
The focus of this contest was solving all the problems. It happened to require all 4 problems to enter the top 200. Because my idea for the third problem was wrong, even if I had another half hour, I would still have had difficulty solving it. So I lost convincingly.

## 1033. Moving Stones Until Consecutive

Intuition:
There are only 3 cases for the minimum number of moves:
0: the 3 coordinates are consecutive,
1: 2 are consecutive, or there is exactly one empty position between 2 of them,
2: both intervals between x, y and y, z are greater than 1.
The maximum number of moves must be z - x - 2, where each move only moves one step toward the middle.

Time complexity: O(1)
Space complexity: O(1)

```cpp
class Solution {
public:
    vector<int> numMovesStones(int a, int b, int c) {
        // greedy
        int minimum_moves = 0, maximum_moves;
        int x = min({a, b, c});
        int z = max({a, b, c});
        int y = a + b + c - x - z;

        if (y - x == 1 && z - y == 1)
            minimum_moves = 0;
        else if (y - x == 1 || z - y == 1 || y - x == 2 || z - y == 2)
            minimum_moves = 1;
        else
            minimum_moves = 2;
        maximum_moves = z - y - 1 + y - x - 1;
        return {minimum_moves, maximum_moves};
    }
};
```

## 1034. Coloring A Border

Intuition:
DFS coloring. Pay attention to how to mark visited nodes; here I use negative numbers to represent them.
Time complexity: O(n*m),
Space complexity: O(1), in place.

```cpp
class Solution {
    bool border(vector<vector<int>>& grid, int r, int c) {
        vector<int> di = {1, 0, -1, 0};
        vector<int> dj = {0, 1, 0, -1};
        for (int k = 0; k < 4; ++k) {
            int ni = r + di[k];
            int nj = c + dj[k];
            if (ni < 0 || ni >= grid.size() || nj < 0 || nj >= grid[0].size() || abs(grid[ni][nj]) != abs(grid[r][c]))
                return true;
        }
        return false;
    }
    void dfs(vector<vector<int>> &grid, int r, int c) {
        grid[r][c] = -grid[r][c];
        vector<int> di = {1, 0, -1, 0};
        vector<int> dj = {0, 1, 0, -1};
        for (int k = 0; k < 4; ++k) {
            int ni = r + di[k];
            int nj = c + dj[k];
            if (ni >= 0 && ni < grid.size() && nj >= 0 && nj < grid[0].size() && grid[ni][nj] == abs(grid[r][c]))
                dfs(grid, ni, nj);
        }
        if (!border(grid, r, c))
            grid[r][c] = -grid[r][c];
    }
public:
    vector<vector<int>> colorBorder(vector<vector<int>>& grid, int r0, int c0, int color) {
        // dfs
        if (grid[r0][c0] == color) return grid;
        int origin_color = grid[r0][c0];
        dfs(grid, r0, c0);
        for (auto& r : grid) {
            for (auto & item : r) {
                if (item < 0)
                    item = color;
            }
        }
        return grid;
    }
};
```

## 1035. Uncrossed Lines

Intuition:
My first instinct was to transform the problem into finding a maximum independent set in a bipartite graph. Treat all lines as nodes, and line intersections as edges between nodes. I searched online for algorithms for solving bipartite graphs, but none were simple enough to write in a short time. So I gave up on this problem and solved the fourth problem first. In fact, the strategy of doing the fourth problem first was correct. It proved that my idea for this problem had gone off track. It should be transformed into LCS (Longest Common Subsequence) and solved with DP. Also, the fourth problem had a higher score, so my contest rank ended up being better.

The DP implementation for LCS is also simple.
Time complexity: O(N*M).
Space complexity: O(N*M).

```cpp
class Solution {
public:
    int maxUncrossedLines(vector<int>& A, vector<int>& B) {
        const int m = A.size(), n = B.size();
        vector<vector<int>> dp(m + 1, vector<int> (n + 1, 0));
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (A[i - 1] == B[j - 1]) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);                    
                }
            }
        }
        return dp[m][n];
    }
};
```

The space complexity can be optimized to N, because the DP subproblems only use the previous row and the current row.

```cpp
class Solution {
public:
    int maxUncrossedLines(vector<int>& A, vector<int>& B) {
        const int m = A.size(), n = B.size();
        vector<int> dp(n + 1, 0);
        for (int i = 1; i <= m; ++i) {
            for (int j = n; j >= 1; --j) {
                if (A[i - 1] == B[j - 1]) {
                    dp[j] = 1 + dp[j - 1];
                }
            }
            for (int j = 1; j <= n; ++j) {
                dp[j] = max(dp[j], dp[j - 1]);
            }
        }
        return dp[n];
    }
};
```

## 1036. Escape a Large Maze

Intuition:
DFS: O(n ^ 2), 10 ^ 12, TLE.
`blocked.length` is small, so we can try to use that.
`blocked` can divide the entire area into several regions. We only need to determine whether `source` and `target` are in the same region.
Since `blocked.length <= 200`, the maximum search space can be calculated as 19900, when the blocked cells and the border form an isosceles right triangle.
However, when `bound = 10000`, DFS causes stack overflow. With 5000, it can pass all test points.
A better implementation is to use iteration rather than recursion. Implementing DFS with a stack, or BFS with a queue, are both good choices.

Time complexity: O(blocked.length ^ 2).
Space complexity: O(blocked.length ^ 2).

```cpp
class Solution {
    const int bound = 5000;
    const int border = 1e6;
    int source_step = 0;
    set<pair<int, int>> source_seen, target_seen, block_set;
    bool find = false;
    int target_step = 0;
    void dfs(set<pair<int, int>>& seen, int& step, vector<int>& target, int i, int j) {
        seen.insert({i, j});
        ++step;
        if (i == target[0] && j == target[1]) {
            find = true;
            return;
        }
        if (step > bound || find == true)
            return;
        vector<int> di = {1, 0, -1, 0};
        vector<int> dj = {0, 1, 0, -1};
        for (int k = 0; k < 4; ++k) {
            int ni = i + di[k];
            int nj = j + dj[k];
            if (ni >= 0 && ni < border && nj >= 0 && nj < border && seen.find({ni, nj}) == seen.end() && block_set.find({ni, nj}) == block_set.end()) {
                dfs(seen, step, target, ni, nj);
                if (step > bound || find == true)
                    return;
            }
        }
        if (step > bound || find == true)
            return;
    }
public:
    bool isEscapePossible(vector<vector<int>>& blocked, vector<int>& source, vector<int>& target) {
        for (const auto & cell : blocked) {
            block_set.insert({cell[0], cell[1]});
        }
        find = false;
        dfs(source_seen, source_step, target, source[0], source[1]);
        if (find) return true;
        find = false;
        dfs(target_seen, target_step, source, target[0], target[1]);
        if (find) return true;
        if (target_step > bound && source_step > bound)
            return true;
        return false;
    }
};
```
