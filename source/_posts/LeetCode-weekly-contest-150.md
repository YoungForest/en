---
title: LeetCode weekly contest 150
date: 2019-08-19 09:45:53
description: Weekly notes on character counts, tree-level sums, multi-source distance search and greatest suffixes after a relaxed break.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/08/19/LeetCode-weekly-contest-150/
  en: https://youngforest.github.io/en/2019/08/19/LeetCode-weekly-contest-150/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-150/en-hero.webp" alt="Forest winds a practice mechanism beside colour-token blocks, tree-level baskets, expanding island ripples and racing patterned suffix ribbons" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 476 / 5091 |	YoungForest | 	19	 | 	1:31:13 | 0:03:52  | 0:09:23 | 1:16:13  2 |  0:50:16  1 |

The result of this contest was poor again, with a rank in the 400s. The direct reason was that I spent too much time on search pruning for the third problem and did not get it right in one shot. The root cause was that I finally had a two-week holiday at home recently, relaxed my practice, and basically stopped grinding problems; naturally, my hands got rusty. It really matches the saying, "Excellence comes from diligence and is ruined by idleness." Last week's rank was also very bad.

## 1160. Find Words That Can Be Formed by Characters

A standard letter-counting problem. Store counts with a hashmap. One detail to note is that when checking each word, you need to get a copy of the counts.

Time complexity: O(chars.size() + words.size() * word.size()),
Space complexity: O(26)

```cpp
class Solution {
public:
    int countCharacters(vector<string>& words, string chars) {
        int ans = 0;
        vector<int> count(26);
        for (char c : chars) {
            ++count[c - 'a'];
        }
        for (const auto& s : words) {
            auto count_copy = count;
            for (char c : s) {
                --count_copy[c - 'a'];
                if (count_copy[c - 'a'] < 0) {
                    goto next;
                }
            }
            ans += s.size();
            next:
                ;
        }
        return ans;
    }
};
```

## 1161. Maximum Level Sum of a Binary Tree

First get the sum of each level, then take the maximum.

Time complexity: O(N),
Space complexity: O(N).

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
    vector<int> sum_of_level;
    void dfs(TreeNode* root, int depth) {
        if (root == nullptr)
            return;
        if (depth > sum_of_level.size()) {
            sum_of_level.push_back(0);
        }
        sum_of_level[depth-1] += root->val;
        dfs(root->left, depth + 1);
        dfs(root->right, depth + 1);
    }
public:
    int maxLevelSum(TreeNode* root) {
        dfs(root, 1);
        int ans = 0;
        for (int i = 1; i < sum_of_level.size(); ++i) {
            if (sum_of_level[i] > sum_of_level[ans]) {
                ans = i;
            }
        }
        return ans + 1;
    }
};
```

## 1162. As Far from Land as Possible

Start from land and run DFS.
The pruning condition is quite complicated. Pay attention to:
`((src_i == i && src_j == j) || (direct_i == di[k]) || (direct_j == dj[k]) || (direct_i == 0 && dj[k] == 0) || (direct_j == 0 && di[k] == 0)) && grid[ni][nj] != 1`.
These mean:
- The search direction is consistent with the previous direction
    - The current starting point is the origin
    - The row direction is consistent
    - The column direction is consistent
    - Previously we did not move in the row direction, and now we move in the row direction
    - Previously we did not move in the column direction, and now we move in the column direction
- It is not land

Time complexity: O((row*column) ^ 2),
Space complexity: O(row*column).

In fact, the better algorithm is BFS. Initialize the queue with all land cells. BFS guarantees that each cell is visited only once.

```cpp
class Solution {
    void dfs(const vector<vector<int>>& grid, vector<vector<int>>& distance, int i, int j, int depth, int src_i, int src_j) {
        if (distance[i][j] != -1 && depth >= distance[i][j]) {
            return;
        }
        distance[i][j] = depth;
        vector<int> di = {1, 0, -1, 0};
        vector<int> dj = {0, 1, 0, -1};
        for (int k = 0; k < 4; ++k) {
            int ni = di[k] + i;
            int nj = dj[k] + j;
            int direct_i = (i - src_i) == 0 ? 0 : (i - src_i) / abs(i - src_i);
            int direct_j = (j - src_j) == 0 ? 0 : (j - src_j) / abs(j - src_j);
            if (ni >= 0 && ni < grid.size() && nj >= 0 && nj < grid[0].size() && ((src_i == i && src_j == j) || (direct_i == di[k]) || (direct_j == dj[k]) || (direct_i == 0 && dj[k] == 0) || (direct_j == 0 && di[k] == 0)) && grid[ni][nj] != 1) {
                dfs(grid, distance, ni, nj, depth + 1, src_i, src_j);
            }
        }
    }
public:
    int maxDistance(vector<vector<int>>& grid) {
        auto distance = grid;
        for (int i = 0; i < distance.size(); ++i) {
            std::fill(distance[i].begin(), distance[i].end(), -1);
        }
        for (int i = 0; i < grid.size(); ++i) {
            for (int j = 0; j < grid[0].size(); ++j) {
                if (grid[i][j] == 1) {
                    dfs(grid, distance, i, j, 0, i, j);
                }
            }
        }
        int ans = -1;
        for (int i = 0; i < distance.size(); ++i) {
            for (int j = 0; j < distance[0].size(); ++j) {
                if (grid[i][j] == 0) {
                    ans = max(ans, distance[i][j]);
                }
            }
        }
        return ans;
    }
};
```

## 1163. Last Substring in Lexicographical Order

The answer may start from some character and continue to the end of the string.
The first character must be the largest character.
Each time, select the largest character, then select the second character, and keep moving backward until only one candidate remains.
One pruning case is something like `'zzzz'`. Everyone has the largest character, so the only candidate should be the first largest character.

Time complexity: O(N). Because of the pruning condition, the number of candidates is `N + N / 2 + N / 4 + ... + 1 = 2 * N`.
Space complexity: O(N).

```cpp
class Solution {
    /**
    find the first maximum char, then compare next char
    */
public:
    string lastSubstring(string s) {
        vector<pair<int, int>> q; // begin index, compare length
        char maximum_char = 'a';
        for(int i = 0; i < s.size(); ++i) {
            if (s[i] > maximum_char) {
                maximum_char = s[i];
            }
        }
        for(int i = 0; i < s.size(); ++i) {
            if (s[i] == maximum_char) {
                q.push_back({i, 1});
            }
        }
        while (q.size() > 1) {
            char mc = 'a';
            vector<pair<int, int>> nq;
            for(const auto& p : q) {
                if (p.first + p.second < s.size() && s[p.first + p.second] > mc) {
                    mc = s[p.first + p.second];
                }
            }
            for(const auto& p : q) {
                if (p.first + p.second < s.size() && s[p.first + p.second] == mc) {
                    if (mc == maximum_char && p.first > 0 && s[p.first  - 1] == mc) {
                        continue;
                    }
                    nq.push_back({p.first, p.second + 1});
                }
            }
            std::swap(q, nq);
        }
        return s.substr(q.back().first);
    }
};
```

## Afterword

I once wrote a very careful answer on Zhihu: [How does everyone grind LeetCode?](https://www.zhihu.com/question/280279208/answer/704774024). Over the past few months, students have kept following me, and some even commented that they would keep watching what I do next. I should not let them down. Time to clean up this lazy mood and face the new semester in full grind-mode!
