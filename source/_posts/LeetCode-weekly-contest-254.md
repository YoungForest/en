---
title: LeetCode Weekly Contest 254
date: 2021-08-15 11:40:36
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (6) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 616 / 13755 | YoungForest | 18 | 1:28:25 | 0:03:14 | 0:08:43 | 0:37:17  🐞4 | 1:03:25  🐞1 |

Without noticing, I had skipped the weekly contest blog for another three weeks. Although I kept participating in weekly contests, after contests I often forgot to summarize and review them. On ordinary days, I also relaxed my problem-solving practice. For daily check-ins, either I let my girlfriend solve them or copied previous submissions. After all, I have already solved 1300+ problems myself, and the problems that appear are often ones I have done before.

The main reason is still that life has become much richer after starting work, and the priority of problem solving and weekly contests has dropped a lot. I skipped multiple biweekly contests too. The temptation of having fun is too strong.

## 1967. Number of Strings That Appear as Substrings in Word

A warm-up problem. String problems are so easy with `Python`. Unfortunately, I did not stick with `Python` for Q3, otherwise skipping check-in this week would have been very possible. I was only 10 minutes away from top 500.

```python
class Solution:
    def numOfStrings(self, patterns: List[str], word: str) -> int:
        ans = 0
        for s in patterns:
            if s in word:
                ans += 1
        return ans
```

Time complexity: O(sum(patterns[i].length * word.length)),
space complexity: O(1).

## 1968. Array With Elements Not Equal to Average of Neighbors

Greedy. The answer to this problem is not unique, but most people's idea seemed to be the same as mine.

That is, insert large and small numbers alternately, ensuring that the numbers on both sides are both greater than or both smaller than the middle number. Naturally, the average is also guaranteed to be greater than or smaller than the middle number.

```cpp
class Solution {
public:
    vector<int> rearrangeArray(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        const int n = nums.size();
        int l = 0, r = n - 1;
        vector<int> ans(n);
        for (int i = 0; l <= r; i += 2) {
            ans[i] = nums[l++];
            if (l <= r)
                ans[i+1] = nums[r--];
        }
        return ans;
    }
};
```

Time complexity: O(n log n),
space complexity: O(n).

## 1969. Minimum Non-Zero Product of the Array Elements

Greedy. Split the numbers into large and small ones as much as possible, namely `1 * (2^p - 2) * 1 * (2^p - 2) * ... * (2^p - 1)`.

There are `(2^p - 2) / 2` groups in front.

For the proof, see [link](https://leetcode-cn.com/problems/minimum-non-zero-product-of-the-array-elements/solution/tan-xin-ji-qi-shu-xue-zheng-ming-by-endl-uumv/).

WA 3 times + Runtime Error 1 time.

WA 1: when calculating `2^p`, MOD should not be applied; it should be calculated directly.

WA 2: the non-MOD `pow` was written incorrectly. Because I copied the MOD version directly, it was still calling the original function, and I forgot to update it to the new function.

Runtime Error: multiplication overflow. It exceeded `long long`, which happened when `base` was too large. In fact, taking `base` modulo first could overcome this, but unfortunately my contest mentality had already collapsed. I switched directly to Python and did not dig further into the cause or solution.

WA 3: I reluctantly switched to Python, but because I again copied the original code and modified it, I forgot to change one place involving integer division and got another WA. Also, Python has built-in fast exponentiation with modulo, while other languages need to implement it themselves.

Lesson: be careful when copying and modifying code during a contest. It is easy to miss places that need changes. If convenient, it may be better to rewrite it once.

```python
class Solution:
    def minNonZeroProduct(self, p: int) -> int:
        MOD = 10**9 + 7;
        x = (2**p)
        return ((x - 1) * pow(x - 2, ((x - 2) // 2), MOD)) % MOD
```

## 1970. Last Day Where You Can Still Cross

Union-Find. Penetration, the Union-Find exercise from Princeton CS226.

The difference is that if filling water forward, the `unite` operation needs to try eight directions.

Of course, it is also possible to remove water backward; in that case, land is connected in four directions.

Note that dummy nodes are needed to connect the boundary layers.

Another common approach is BFS + Binary search, which is somewhat brute-force. Although it also passes, the time complexity is much worse:

O(log(rows * cols) * rows * cols).

Runtime Error 1 time.

I actually wrote the conversion from row/column coordinates to one-dimensional coordinates incorrectly, causing an array out-of-bounds error and a long debugging session.

It should have been `r*col+c`, but I wrote `r*row+c`.

```cpp
class Solution {
    class UF {
    public:
        vector<int> fa;
        vector<int> sz;
        int n;
        int comp_cnt;

    public:
        UF(int _n): n(_n), comp_cnt(_n), fa(_n), sz(_n, 1) {
            iota(fa.begin(), fa.end(), 0);
        }

        int findset(int x) {
            return fa[x] == x ? x : fa[x] = findset(fa[x]);
        }

        void unite(int x, int y) {
            x = findset(x);
            y = findset(y);
            if (x != y) {
                if (sz[x] < sz[y]) {
                    swap(x, y);
                }
                fa[y] = x;
                sz[x] += sz[y];
                --comp_cnt;
            }
        }

        bool connected(int x, int y) {
            x = findset(x);
            y = findset(y);
            return x == y;
        }
    };
public:
    int latestDayToCross(const int row, const int col, vector<vector<int>>& cells) {
        vector<vector<int>> nums(row, vector<int>(col, 0));
        UF uf(row * col + 2);
        vector<vector<int>> direcionts = {
            {0, 1}, 
            {1, 0},
            {-1, 0},
            {0, -1},
            {1, 1},
            {1, -1},
            {-1, 1},
            {-1, -1}
        };
        const int L = row * col;
        const int R = row * col + 1;
        for (int i = 0; i < cells.size(); ++i) {
            const int r = cells[i][0] - 1;
            const int c = cells[i][1] - 1;
            // cout << r << ", " << c << endl;
            nums[r][c] = 1;
            // cout << r << ", " << c << endl;
            if (c == 0) {
                uf.unite(r * col + c, L);
            }
            // cout << r << ", " << c << endl;
            if (c == col - 1) {
                uf.unite(r * col + c, R);
            }
            // cout << r << ", " << c << endl;
            
            for (const auto& d : direcionts) {
                const int dr = d[0];
                const int dc = d[1];
                const int nr = dr + r;
                const int nc = dc + c;
                if (nr >= 0 && nr < row && nc >= 0 && nc < col) {
                    // cout << "xx: " << nr << ", " << nc << endl;
                    if (nums[nr][nc] == 1) {
                        uf.unite(r * col + c, nr * col + nc);
                    }
                }
            }
            if (uf.connected(L, R)) return i;
        }
        return -1;
    }
};
```

Time complexity: O(rows * cols),
space complexity: O(rows * cols).
