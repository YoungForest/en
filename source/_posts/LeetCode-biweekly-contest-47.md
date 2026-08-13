---
title: LeetCode Biweekly Contest 47
date: 2021-03-07 16:06:47
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/03/07/LeetCode-biweekly-contest-47/
  en: https://youngforest.github.io/en/2021/03/07/LeetCode-biweekly-contest-47/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 64 / 9933 | YoungForest | 18 | 0:55:55 |  0:03:37 |  0:07:16 |  0:13:28 | 0:55:55 |

A crazy rating-gain round. I solved 3 problems in 13 minutes. The idea for the last problem was also relatively smooth. I hit two blockers: first, at the beginning I forgot to consider point pairs with no edge between them; second, I solved the complementary problem, but when returning the answer, I carelessly thought the total count was `n^2`, while in fact it is `C(n, 2) = n * (n - 1) / 2`. Debugging wasted quite a bit of time. If it had gone more smoothly, maybe the result could have broken through the sky and won the generous top-20 prize.

<!-- more -->

## 1779. Find Nearest Point That Has the Same X or Y Coordinate

Warm-up problem. Traverse once, checking whether a point is in the same row/column and whether the distance is closer.

```cpp
class Solution {
    int distance(const vector<int>& a, const vector<int>& b) {
        return abs(a[0] - b[0]) + abs(a[1] - b[1]);
    }
public:
    int nearestValidPoint(int x, int y, vector<vector<int>>& points) {
        const vector<int> me = {x, y};
        int ans = -1;
        int ansDistance = numeric_limits<int>::max();
        for (int i = 0; i < points.size(); ++i) {
            const auto& v = points[i];
            if (v[0] == me[0] || v[1] == me[1]) {
                const int newDistance = distance(me, v);
                if (newDistance < ansDistance) {
                    ansDistance = newDistance;
                    ans = i;
                }
            }
        }
        return ans;
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 1780. Check if Number is a Sum of Powers of Three

Ternary encoding. Each digit can only be 0 or 1, not 2.

```cpp
class Solution {
public:
    bool checkPowersOfThree(int n) {
        // x = (x - 1) / 3 or x / 3
        // time: log_3 (n)
        int x = n;
        while (x > 0) {
            if ((x - 1) % 3 == 0) {
                x = (x - 1) / 3;
            } else if (x % 3 == 0) {
                x = x / 3;
            } else {
                return false;
            }
        }
        return true;
    }
};
```

Time complexity: O(log_3 N),
space complexity: O(1).

## 1781. Sum of Beauty of All Substrings

Observing that the constraint is not large, `500`, we can brute-force enumerate all substrings and update `beauty` in O(1).

```cpp
class Solution {
public:
    int beautySum(string s) {
        // brute-force: n ^ 2 * log 26
        int ans = 0;
        for (int i = 0; i < s.size(); ++i) {
            vector<int> count(26, 0);
            multiset<int> cnt;
            for (int j = i; j < s.size(); ++j) {
                // s[i:j]
                auto it = cnt.find(count[s[j] - 'a']);
                if (it != cnt.end()) cnt.erase(it);
                ++count[s[j] - 'a'];
                cnt.insert(count[s[j] - 'a']);
                ans += (*cnt.rbegin() - *cnt.begin());
            }
        }
        return ans;
    }
};
```

Time complexity: O(N^2 * log 26),
space complexity: O(26).

## 1782. Count Pairs Of Nodes

Observe that the number of vertices `V` is still large: `2*10^4`, so we cannot enumerate all point pairs.
But the number of edges `E` is not large, `10^5`, so this is not a dense graph.
Therefore, we can brute-force over edges.
Also, `queries.size()` is small, only 20.

The overall idea is to first compute every node's degree. If the sum of two nodes' degrees is less than or equal to `query`, then the `cnt` of the two nodes must be less than or equal to `query`. This is because `cnt(a, b) <= degree(a) + degree(b)`, where equality holds if and only if `a` and `b` are not directly connected.
We can first sort the degrees, then use a `two sum` style binary search to find all point pairs satisfying `degree(a) + degree(b) <= query`.
Although the problem asks for `cnt` strictly greater than `query`, we first compute `cnt <= query`, because the two are complements. In addition to the point pairs above, some point pairs with edges may also satisfy the requirement. These pairs are:
`degree(a) + degree(b) > query and degree(a) + degree(b) - edges(a, b) <= query`. We only need to traverse all edges once.

```cpp
class Solution {
    using pii = pair<int, int>;
public:
    vector<int> countPairs(int n, vector<vector<int>>& edges, vector<int>& queries) {
        // time: E + 20 * (E + V log V)
        map<pii, int> edgesCount;
        vector<int> degree(n, 0);
        for (auto& e : edges) {
            if (e[0] > e[1]) swap(e[0], e[1]);
            ++degree[e[0] - 1];
            ++degree[e[1] - 1];
            ++edgesCount[{e[0] - 1, e[1] - 1}];
        }
        
        vector<int> degreeV = degree;
        sort(degreeV.begin(), degreeV.end());
        
        vector<int> answers;
        answers.reserve(queries.size());
        for (int i : queries) {
            int ret = 0;
            for (auto ait = degreeV.begin(); ait != degreeV.end(); ++ait) {
                const int j = *ait;
                auto bit = upper_bound(degreeV.begin(), ait, i - j);
                ret += distance(degreeV.begin(), bit);
            }
            for (auto p : edgesCount) {
                if (degree[p.first.first] + degree[p.first.second] > i && degree[p.first.first] + degree[p.first.second] - p.second <= i) ++ret;
            }
            answers.push_back((n * (n - 1)) / 2 - ret);
        }
        return answers;
    }
};
```

Time complexity: O(E + queries.size() * (E + V log V)),
space complexity: O(V + E).
