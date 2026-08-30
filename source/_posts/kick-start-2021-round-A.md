---
title: Kick Start 2021 Round A
date: 2021-03-22 19:19:06
tags:
- Competitive Programming
categories:
- KickStart
description: "Returning to algorithm competition for interest, four problems restore the joy of reasoning through symmetry, geometry, terrain and graph structure."
translations:
  zh-CN: https://youngforest.github.io/2021/03/22/kick-start-2021-round-A/
  en: https://youngforest.github.io/en/2021/03/22/kick-start-2021-round-A/
---
| ID | score | rank | K-Goodness String | L Shaped Plots | Rabbit House | Checksum | Penalty Time |
|--|--|--|--|--|--|--|--|
| YoungForest | 56 | 1295 | 5 + 7 | 8 + 12 | 9 + 15 | 0 | 1:40:55 |


I participated in Kick Start after a long absence. For the previous two years, because I wanted to go to Google, I had kept participating. But man proposes, God disposes: because of the pandemic, both internships and autumn recruitment basically fell through. I had participated in 10+ contests over more than two years, and they were "wasted" in that sense.
This year I joined purely out of interest. The result was indeed a 1000+ rank. Before, I was usually in the several hundreds.
I have to say that since last year Kick Start has become much easier and friendlier. Previously I often could only solve one and a half problems. Later, to broaden participation, three problems became four, and the overall difficulty also came down.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/kick-start-2021-round-A/en-hero.webp" alt="A calm tabletop of four tactile puzzles shows mirrored beads, L-shaped tiles, rising rabbit terrain and a network ring opening into a tree" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## K-Goodness String

Warm-up problem. Count the string's goodness score. Whether increasing or decreasing it, the minimum operation count changes by 1 each time.

```cpp
#include <bits/stdc++.h>

using namespace std;

int solve() {
    int N, K;
    cin >> N >> K;
    string s;
    cin >> s;
    int k = 0;
    for (int i = 1; i <= N / 2; ++i) {
        if (s[i - 1] != s[N - i + 1 - 1]) {
            ++k;
        }
    }
    return abs(K - k);
}

int main() {
    int T;
    cin >> T;
    for (int i = 0; i < T; ++i) {
        auto ans = solve();
        cout << "Case #" << i + 1 << ": " << ans << endl;
    }
    return 0;
}
```

Time complexity: O(N),
space complexity: O(N).

## L Shaped Plots

Count L shapes in one direction. L shapes in the other directions can be obtained by up/down flip, left/right flip, and rotation, for a total of 8 cases.
Use dynamic programming to update the length to the left of the current position and the length above it, then compute the number of L shapes with the current position as the corner.

```cpp
#include <bits/stdc++.h>

using namespace std;

int solve() {
    int R, C;
    cin >> R >> C;
    vector<vector<bool>> matrix(R, vector<bool> (C));
    int x;
    for (int i = 0; i < R; ++i) {
        for (int j = 0; j < C; ++j) {
            cin >> x;
            matrix[i][j] = (x == 1);
        }
    }
    
    auto reverseLeftRight = [&]() -> void {
        for (auto& m : matrix) {
            reverse(m.begin(), m.end());
        }
    };
    auto reverseUpDown = [&]() -> void {
        for (int j = 0; j < C; ++j) {
            for (int i = 0; i < R - 1 - i; ++i) {
                swap(matrix[i][j], matrix[R - 1 - i][j]);
            }
        }
    };
    auto flip = [&]() -> void {
        vector<vector<bool>> newMatrix(C, vector<bool>(R));
        for (int i = 0; i < R; ++i) {
            for (int j = 0; j < C; ++j) {
                newMatrix[j][i] = matrix[i][j];
            }
        }
        matrix = move(newMatrix);
        swap(R, C);
    };
    auto one = [&]() -> int {
        vector<vector<int>> dp(R, vector<int> (C));
        int ans = 0;
        for (int j = 0; j < C; ++j) {
            dp[0][j] = matrix[0][j] ? 1 : 0;
        }
        for (int i = 1; i < R; ++i) {
            int left = 0;
            for (int j = 0; j < C; ++j) {
                if (matrix[i][j]) {
                    ++left;
                    dp[i][j] = dp[i-1][j] + 1;
                    const int up = dp[i][j] / 2;
                    const int could = min(up, left);
                    if (could > 1) ans += could - 1;
                } else {
                    left = 0;
                    dp[i][j] = 0;
                }
            }
        }
        return ans;
    };
    auto two = [&]() -> int {
        int ans = 0;
        ans += one();
        reverseLeftRight();
        ans += one();
        return ans;
    };
    auto four = [&]() -> int {
        int ans = 0;
        ans += two();
        reverseUpDown();
        ans += two();
        return ans;
    };
    int ans = 0;
    ans += four();
    flip();
    ans += four();
    return ans;
}

int main() {
    int T;
    cin >> T;
    for (int i = 0; i < T; ++i) {
        auto ans = solve();
        cout << "Case #" << i + 1 << ": " << ans << endl;
    }
    return 0;
}
```

Time complexity: O(8 * R * C)
space complexity: O(R*C).


## Rabbit House

BFS. The difference between adjacent positions is at most 1, so search starting from the highest cells.

```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;
using pii = pair<int, int>;

const vector<pii> directions = {
    {1, 0}, {0, 1}, {-1, 0}, {0, -1}
};

ll solve() {
    int R, C;
    cin >> R >> C;
    vector<vector<ll>> matrix(R, vector<ll> (C));
    unordered_map<int, vector<pii>> cells;
    ll x;
    ll maxHeight = 0;
    for (int i = 0; i < R; ++i) {
        for (int j = 0; j < C; ++j) {
            cin >> matrix[i][j];
            cells[matrix[i][j]].emplace_back(i, j);
            maxHeight = max(maxHeight, matrix[i][j]);
        }
    }

    queue<pii> q;
    for (const auto& p : cells[maxHeight]) {
        q.push(p);
    }

    ll ans = 0;
    ll height = maxHeight;
    while (!q.empty() && height > 0) {
        const int s = q.size();
        for (int x = 0; x < s; ++x) {
            auto [i, j] = q.front();
            q.pop();
            for (pii d : directions) {
                const int ni = i + d.first;
                const int nj = j + d.second;
                if (ni >= 0 && ni < R && nj >= 0 && nj < C) {
                    if (matrix[ni][nj] >= height) continue;
                    q.emplace(ni, nj);
                    ans += height - 1 - matrix[ni][nj];
                    matrix[ni][nj] = height;
                }
            }
        }
        for (const auto& p : cells[height-1]) {
            q.push(p);
        }
        --height;
    }
    
    return ans;
}

int main() {
    int T;
    cin >> T;
    for (int i = 0; i < T; ++i) {
        auto ans = solve();
        cout << "Case #" << i + 1 << ": " << ans << endl;
    }
    return 0;
}
```

Time complexity: O(R * C),
space complexity: O(R * C).

Two points to watch:

- During BFS, each time we also need to add cells with the same height into the queue. These are also starting points for the next traversal.
- Also, the final result can exceed 32 bits, so `long long` is needed to avoid an overflow WA.

Because of this, I also got 3 more WAs worth of penalty time.

## Checksum

By the time I reached the last problem, I was already very tired. LeetCode Weekly Contest for 1 hour plus Kick Start for 3 hours, such high-intensity mental activity is a huge drain on a person. So by the last problem, my brain was no longer as clear as at the beginning. After thinking for a while, I gave up. In fact, at my usual level, I should still have been able to brute-force the small test set for some points.

I upsolved it after the contest, with the solution referencing the official Analysis.

### Brute Force, Test Set 1

Enumerate every set of costs and check whether the whole board can be inferred.
To determine whether all elements can be inferred, use BFS starting from rows or columns that have a unique element, then keep adding elements. DFS also works.

Time complexity: O(2^(N^2) * N ^ 2).

### Cycle-Breaking Method, Test Set 2

Use row and column numbers as nodes, and missing elements as edges. This converts the original matrix into a bipartite graph. Rows are on one side, columns on the other.
An isolated node means all elements in that row/column are known and can be ignored directly.
A node with degree 1 means that row/column has only one unknown element, which can be inferred from the checksum, so it can be deleted directly.
The remaining graph forms an undirected weighted graph with cycles.

The original problem becomes: delete some edges at minimum cost so that the original graph has no cycles.
A greedy algorithm can be used. Traverse edges from small to large; if an edge is inside a cycle, delete it. Whether it is inside a cycle can be checked by brute-force DFS: ignore this edge and see whether another path exists from one endpoint to the other.

Time complexity: traverse all remaining edges * cycle check = O(N ^ 2 * N ^ 2) = O(N^4).

For the correctness proof of the greedy algorithm, refer to [Minimum spanning tree Kruskal algorithm](https://en.wikipedia.org/wiki/Kruskal%27s_algorithm).

### Maximum Spanning Tree, Test Set 3

Based on solution 2, in fact, we can use a maximum spanning tree algorithm to quickly obtain the final state.

Time complexity: Prim O(N^2), Kruskal O(N^2 * log N).

```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = int;
using pii = pair<int, int>;
using tii = tuple<int, int, int>;

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

ll solve()
{
    int N;
    cin >> N;
    // construct the 2-partical graph
    vector<vector<pii>> graph(2 * N);
    vector<tii> edges;
    ll x;
    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            cin >> x;
        }
    }
    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            cin >> x;
            if (x > 0)
            {
                graph[i].push_back({j + N, x});
                graph[j + N].push_back({i, x});
                edges.emplace_back(x, i, j + N);
            }
        }
    }
    for (int i = 0; i < N; ++i)
    {
        cin >> x;
    }
    for (int i = 0; i < N; ++i)
    {
        cin >> x;
    }

    sort(edges.begin(), edges.end(), greater<>());
    UF uf(2*N);
    ll cost = 0;
    for (const auto& e : edges) {
        if (uf.connected(get<1>(e), get<2>(e))) {
            cost += get<0>(e);
        } else {
            uf.unite(get<1>(e), get<2>(e));
        }
    }

    return cost;
}

int main()
{
    int T;
    cin >> T;
    for (int i = 0; i < T; ++i)
    {
        auto ans = solve();
        cout << "Case #" << i + 1 << ": " << ans << endl;
    }
    return 0;
}
```

I used Kruskal because it is easier to implement.

Time complexity: O(N^2 * log N^2),
space complexity: O(N^2).
