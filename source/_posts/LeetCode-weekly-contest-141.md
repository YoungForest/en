---
title: LeetCode weekly contest 141
date: 2019-06-16 11:44:47
description: Exam-week notes on in-place zero duplication, label-limited selection, binary-grid paths and shortest common supersequences.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/06/16/LeetCode-weekly-contest-141/
  en: https://youngforest.github.io/en/2019/06/16/LeetCode-weekly-contest-141/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-141/en-hero.webp" alt="Forest studies beside blank exam binders while duplicating hollow beads, limiting labelled weights, crossing a grid and weaving two sequences" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (6) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
| 234 / 4126 |	YoungForest | 22 | 1:18:45 | 0:25:23  1 | 0:36:29 | 0:51:47 | 1:13:45 |

I had a Natural Dialectics exam on Monday and a Matrix exam on Tuesday, but still forced myself to make time for the contest. My review itself was not sufficient, and my usual study was not very solid either. I was really taking things lightly.
To enter the top 200, the finish time had to be at least within 1:14:23.

## 1089. Duplicate Zeros

Intuition:
Because the requirement is in-place, a natural idea is to update values from back to front.
Two passes: the first forward pass obtains the original coordinate of the last element in the result array.
The second reverse pass updates the result array.

Time complexity: O(N),
Space complexity: O(1).

```cpp
class Solution {
public:
    void duplicateZeros(vector<int>& arr) {
        int zeros = 0;
        int i = 0;
        for (i = 0; i < arr.size() && i + zeros < arr.size(); ++i) {
            if (arr[i] == 0) {
                ++zeros;
            }
        }
        --i;
        for (int j = arr.size() - 1; j >= 0; --j, --i) {
            arr[j] = arr[i];
            if (arr[i] == 0 && (i + zeros < arr.size())) {
                --j;    // 多减一j
                if (j >= 0)
                    arr[j] = 0;
            }
        }
    }
};
```

I got one penalty because I ignored the case where the final zero is not expanded if there is not enough space.

Test cases:
```
[0]
[0,0]
[0,0,0]
[0,0,1]
[8,4,5,0,0,0,0,7]
[8,4,5,0,0,0,0,0,7]
```

## 1090. Largest Values From Labels

Intuition:
Greedy idea. Always try to add the item with the largest value.
The concrete implementation is to sort first, and use a hashtable to ensure the `use_limit` constraint is not violated.

Time complexity: O(N log N),
Space complexity: O(N)

```cpp
class Solution {
public:
    int largestValsFromLabels(vector<int>& values, vector<int>& labels, int num_wanted, int use_limit) {
        vector<pair<int, int>> items;
        for (int i = 0; i < values.size(); ++i) {
            items.push_back({values.at(i), labels.at(i)});
        }
        sort(items.begin(), items.end(), std::greater<pair<int, int>>());
        unordered_map<int, int> used;
        int num = 0;
        int index = 0;
        int ret = 0;
        while (num < num_wanted && index < items.size()) {
            const auto& p = items.at(index);
            if (used[p.second] < use_limit) {
                ++used[p.second];
                ++num;
                ret += p.first;
            }
            ++index;
        }
        return ret;
    }
};
```

Test cases:
```
[5,4,3,2,1]
[1,1,2,2,3]
3
1
[5,4,3,2,1]
[1,3,3,3,2]
3
2
[9,8,8,7,6]
[0,0,0,1,1]
3
1
[9,8,8,7,6]
[0,0,0,1,1]
3
2
```

## 1091. Shortest Path in Binary Matrix

Intuition:
For shortest path, use DFS.

Time complexity: O(N^2),
Space complexity: O(N^2).

```cpp
class Solution {
public:
    int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
        // bfs
        int n = grid.size();
        set<pair<int, int>> visited;
        queue<pair<int, int>> q;
        if (grid[0][0] == 0)
            q.push({0, 0});
        int level = 1;
        while (!q.empty()) {
            int s = q.size();
            for (int i = 0; i < s; ++i) {
                auto current = q.front();
                q.pop();
                if (current.first == n - 1 && current.second == n - 1) {
                    return level;
                }
                for (auto di : {-1, 0, 1}) {
                    for (auto dj : {-1, 0, 1}) {
                        int ni = current.first + di, nj = current.second + dj;
                        if (ni == current.first && nj == current.second)
                            continue;
                        if (visited.find({ni, nj}) != visited.end())
                            continue;
                        if (ni < 0 || ni >= n || nj < 0 || nj >= n)
                            continue;
                        if (grid[ni][nj] == 1)
                            continue;
                        visited.insert({ni, nj});
                        q.push({ni, nj});
                    }
                }
            }
            ++level;
        }
        return -1;
    }
};
```

My test cases:
```
[[0,1],[1,0]]
[[0,0,0],[1,1,0],[1,1,0]]
[[0,0,0],[0,1,0],[0,1,0]]
[[0,0,0],[0,1,1],[0,1,0]]
[[0]]
[[1]]
[[0,1,0,0,0],[0,1,0,1,0],[0,1,0,1,0],[0,1,0,1,0],[0,0,0,1,0]]
[[0,1,0,0,0],[0,1,0,1,0],[0,1,1,1,0],[0,1,0,1,0],[0,0,0,1,0]]
```

Expected results:
```
2
4
4
-1
1
-1
13
-1
```

## 1092. Shortest Common Supersequence

Intuition:
First compute the Longest Common Subsequence.
Then fill in the extra characters.
The template for Longest Common Subsequence is important and needs to be implemented quickly.

Time Complexity: O(str1.size() * str2.size()),
Space Complexity: O(str1.size() * str2.size()).

```cpp
class Solution {
    enum class Direction {
        left,
        up,
        left_up
    };
public:
    string shortestCommonSupersequence(string str1, string str2) {
        vector<vector<pair<int, Direction>>> v(str1.size() + 1, vector<pair<int, Direction>>(str2.size() + 1));
        v[0][0] = {0, Direction::left_up};
        for (int i = 1; i < str1.size() + 1; ++i) {
            v[i][0] = {0, Direction::up};
        }
        for (int i = 1; i < str2.size() + 1; ++i) {
            v[0][i] = {0, Direction::left};
        }
        for (int i = 1; i < str1.size() + 1; ++i) {
            for (int j = 1; j < str2.size() + 1; ++j) {
                if (str1.at(i - 1) == str2.at(j - 1)) {
                    v[i][j] = {v[i-1][j-1].first + 1, Direction::left_up};
                } else if (v[i][j-1].first > v[i-1][j].first) {
                    v[i][j] = {v[i][j-1].first, Direction::left};
                } else {
                    v[i][j] = {v[i-1][j].first, Direction::up};
                }
            }
        }
        string ret;
        int i = str1.size(), j = str2.size();
        while (i > 0 || j > 0) {
            switch (v[i][j].second) {
                case Direction::left_up:
                    ret.push_back(str1.at(i - 1));
                    --i;
                    --j;
                    break;
                case Direction::up:
                    ret.push_back(str1.at(i - 1));
                    --i;
                    break;
                case Direction::left:
                    ret.push_back(str2.at(j - 1));
                    --j;
                    break;
            }
        }
        reverse(ret.begin(), ret.end());
        return ret;
    }
};
```

My test cases:
```
"abac"
"cab"
"geek"
"eke"
"AGGTAB"
"GXTXAYB"
```

Expected results:
```
"cabac"
"gekek"
"AGXGTXAYB"
```
