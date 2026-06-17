---
title: LeetCode weekly contest 257
date: 2021-09-06 23:15:10
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 222 / 12542 | YoungForest | 18 | 1:32:31 | 0:06:21  🐞1 | 0:12:18 |  0:24:28 | 1:17:31  🐞2 |

It has been a long time since I did not need to check in. I had checked in for three weeks before, and I really could not take it anymore. After starting work, I have spent much less energy and time on problem solving. Many check-ins were done by my girlfriend, or I simply copied one. You get what you put in. Learning is like rowing upstream: if you do not advance, you fall back. My weekly contest results have indeed declined because my ability has declined. Although I do not plan to change this and am slowly accepting the fact, I will still keep doing weekly contests and stay in the Cruel Problem Solving group, just to maintain a basic problem-solving feel. There is no need to set excessively high expectations for myself.

Recently, besides work, I have spent a lot of time on investing and personal finance.

On one hand, I have been learning related knowledge. For example, I followed "Xiao Yu Ge Who Likes Playing With Funds" on Bilibili and WeChat Official Accounts, as well as "Serious Tianma" on Bilibili and WeChat Official Accounts.

I have followed them for more than half a year. Xiao Yu Ge is absolutely a classic retail investor. I follow him for three reasons: he updates daily review videos and is very persistent and hardworking; the videos are fun, or more precisely, watching him lose money is fun, so they can be watched like entertainment-zone UP content; and he has made many of the mistakes and taboos that retail investors make, so we can learn lessons from his failures without failing ourselves.

Tianma is a proper knowledge-oriented and conservative UP主, which fits my needs and views better. His WeChat Official Account also updates daily, precisely at 8 a.m. every morning. At the end of each article there is a valuation table, which can be used to buy when undervalued and sell when overvalued. His articles and videos are also mainly practical knowledge, worth studying and watching repeatedly.

I also follow the YouTube channel "NaNa Talks US Stocks." It mainly talks about movements in the US stock market. I previously bought 200 RMB of NASDAQ 100 and S&P 500. After they rose a little, I sold them and missed the later rally. US stocks really are a long bull market and cannot be compared with A-shares. Although they are still rising now, they have already reached historical highs, so I do not dare to buy anymore.

I have also been reading investment books. I previously read *Ein Hund namens Money* (Chinese title: *小狗钱钱*), and recently finished *小乌龟的投资智慧* (*The Little Turtle's Investment Wisdom*) and *Reminiscences of a Stock Operator*. I wrote short reviews for all of them on [Douban](https://www.douban.com/people/141835199/).

Among them, *小乌龟的投资智慧* is the most useful book to me, especially for long-term investing of more than 10 years. I plan to follow its approach when preparing retirement money in the future. Right now, because I need to prepare for buying a house and going abroad, I temporarily do not have enough long-term capital. Almost all short-term money needs to be used.

On the other hand, I have been practicing. I divide my assets into four parts: flexible access, including cash and money market funds; conservative bond funds and fixed-income products, such as time deposits; and riskier equity funds, mainly broad-based index funds and sector index funds.

Half a year ago I wrote [my own investment story](https://youngforest.github.io/en/2021/03/22/investment/). Now, I can confidently say that my knowledge and experience have become richer and more professional. Although I have not earned much return, after starting work and increasing my principal, investing and personal finance have become more important.

My overall stock position is currently low, roughly less than one-third, and most of my money is in the other three conservative investment categories.

## 1995. Count Special Quadruplets

A warm-up problem. Brute-force enumeration is enough. However, because I was in a hurry and did not read the statement carefully, I ignored the requirement that indices must be increasing and added sorting on my own, causing one WA.

```python
class Solution:
    def countQuadruplets(self, nums: List[int]) -> int:
        seen = set()
        n = len(nums)
        # print(nums)
        for i in range(n):
            for j in range(i + 1, n):
                for k in range(j + 1, n):
                    for l in range(k + 1, n):
                        if nums[i] + nums[j] + nums[k] == nums[l]:
                            # print((i, j, k, l))
                            seen.add((i, j, k, l))
        return len(seen)
```

Time complexity: O(n^4),
Space complexity: O(n).

## 1996. The Number of Weak Characters in the Game

When doing this problem, I felt that it was somewhat like [354. Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/).

Sort first: one dimension descending, the other dimension ascending.

Then traverse. Since all previous elements are guaranteed to have the first dimension greater than or equal to the current one, we only need to check the maximum value of the second dimension among previous elements. This is also why the second dimension must be sorted ascending: when the first dimension is equal, the larger second dimension should appear later, so it will not affect the comparison during traversal.

```cpp
class Solution {
public:
    int numberOfWeakCharacters(vector<vector<int>>& p) {
        sort(p.begin(), p.end(), [](const auto& lhs, const auto& rhs) -> bool {
            if (lhs[0] != rhs[0]) {
                return lhs[0] > rhs[0];
            } else {
                return lhs[1] < rhs[1];
            }
        });
        int ans = 0;
        int maxDefense = 0;
        for (const auto& c : p) {
            if (maxDefense > c[1]) {
                ++ans;
            }
            maxDefense = max(maxDefense, c[1]);
        }
        return ans;
    }
};
```

Time complexity: O(nlogn),
Space complexity: O(log n).

## 1997. First Day Where You Have Been in All the Rooms

Luckily, I discovered the essence of this problem and solved it quickly with very few lines of code.

Otherwise, I might not have had time to debug the last problem.

Notice that `0 <= nextVisit[i] <= i`, so on odd-numbered visits, we must go backward or stay in place. On even-numbered visits, we can only move forward one step. This means that when we first, or more generally on an odd-numbered visit, reach position `i`, all previous rooms must have been visited an even number of times.

Therefore the state transition equation is:

`dp(i) = dp(i - 1) + 1 + (dp(i - 1) - dp(nextVisit[i-1])) + 1`,

meaning: first reach the previous position, take one step, go back to `nextVisit[i-1]`, reach the previous position again, and then take one more step to the current position.

```python
class Solution:
    def firstDayBeenInAllRooms(self, nextVisit: List[int]) -> int:
        MOD = 10**9 + 7
        @cache
        def dp(i):
            if i == 0: return 0
            return (dp(i-1) * 2 + 2 - dp(nextVisit[i-1])) % MOD
        n = len(nextVisit)
        return dp(n-1)
```

Time complexity: O(N),
Space complexity: O(N).

## 1998. GCD Sort of an Array

By observing the swap rule, we can see that the swappable positions are actually a union-find structure. Two elements can be swapped through a third element.

The problem is transformed into this: to move the original positions to the sorted positions, we need a series of swaps among elements, and those elements must be in the same component.

The necessary and sufficient condition for being in the same component is that gcd is greater than 1.

Therefore, a direct idea, though it obviously TLEs, is:

1. Calculate gcd pairwise and update union-find.
2. Sort the original array and find the corresponding sorted positions.
3. According to the DFS principle, determine which elements must be in the same component, then verify using the previously calculated union-find.

The time complexity is O(N^2 + N log N + N).

The bottleneck is the first step, calculating union-find. We try to optimize it.

Observe the data size: `2 <= nums[i] <= 10^5`, which means we can calculate the factors of each element, then unite elements with the same factor through the factor. There is a pitfall here: we need to connect not only the small factor, but also the corresponding large factor. We can enlarge the union-find array, and use positions after expansion to represent factors, with an offset of `+n`.

The time complexity is reduced to O(N * sqrt(num)), roughly `10^7`, which still may time out. This is also why I got seven penalty submissions on this problem, repeatedly testing on the edge of TLE.

A friend in the Cruel group reminded me: the fourth problem is very similar to 952. Largest Component Size by Common Factor. You need to optimize by factors, otherwise you will TLE to death. But I AC'd by moving local arrays to class variables, a constant-factor optimization.

Still, this factor-based optimization method is very worth learning. It can reduce the complexity of obtaining factors from O(sqrt(num)) to O(log num): https://leetcode.com/problems/gcd-sort-of-an-array/discuss/1445180/C%2B%2BPython-Union-Find-and-Sieve-and-Sorting-Clean-and-Concise

```cpp
class Solution {
    class UF {
public:
    vector<int> sz;
    int n;
    int comp_cnt;
    vector<int> fa;
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
    using pii = pair<int, int>;
    array<pii, 50000> index;
    array<int, 50000> reverseIndex;
    array<bool, 50000> visited;

public:
    bool gcdSort(vector<int>& nums) {
        // time: n log n + n ^ 2
        // (10**5)**0.5
        // = 316.22776601683796
        const int n = nums.size();
        for (int i = 0; i < n; ++i) {
            index[i] = {nums[i], i};
        }
        sort(index.begin(), index.begin() + n);
        for (int i = 0; i < n; ++i) {
            reverseIndex[index[i].second] = i;
        }
        const int maxX = *max_element(nums.begin(), nums.end());
        UF uf(n + maxX + 10);
        for (int a = 0; a < n; ++a) {
            uf.unite(a, n + nums[a]);
            for (int b = 2; b * b <= nums[a]; ++b) {
                if (nums[a] % b == 0) {
                    uf.unite(a, n + b);
                    // 有可能是倍数
                    if ((nums[a] / b) > 1) uf.unite(a, n + (nums[a] / b)); 
                }
            }
        }
        // N ^ 2, TLE
        // for (int a = 0; a < n; ++a) {
        //     for (int b = a + 1; b < n; ++b) {
        //         if (gcd(nums[a], nums[b]) > 1) {
        //             uf.unite(a, b);
        //         }
        //     }
        // }
        for (int i = 0; i < n; ++i) {
            visited[i] = false;
        }
        for (int i = 0; i < n; ++i) {
            if (!visited[i]) {
                vector<int> group;
                while (!visited[i]) {
                    visited[i] = true;
                    group.push_back(i);
                    i = reverseIndex[i];
                }
                const int root = uf.findset(group[0]);
                for (int i = 1; i < group.size(); ++i) {
                    if (root != uf.findset(group[i])) {
                        return false;
                    }
                }
            }
        }
        return true;
    }
};
```

Time complexity: O(N * sqrt(N) + N log N),
Space complexity: O(N + max(nums[i])).
