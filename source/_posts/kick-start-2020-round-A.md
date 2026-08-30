---
title: Kick Start 2020 Round A
date: 2020-03-22 14:43:50
description: "A four-problem contest recap covering greedy house allocation, plate-stack DP, binary search on workout gaps, and trie-based grouping."
tags:
- Competitive Programming
categories:
- KickStart
translations:
  zh-CN: https://youngforest.github.io/2020/03/22/kick-start-2020-round-A/
  en: https://youngforest.github.io/en/2020/03/22/kick-start-2020-round-A/
---
Kick Start has changed a little in the new year:
1. Whether all test results are correct is now returned immediately. Previously, large dataset results could only be seen after the contest. This is effectively a difficulty reduction and reduces the cost of mistakes for contestants. In the past, one small mistake meant losing the score for the large dataset. Now it is more like adding a time penalty.
2. The number of problems changed from 3 to 4, with the time unchanged, adding one easier problem for points.

Rank 570. Because everyone got 100 points, the final comparison was all about time. Since the contest was from 12:00 to 15:00, I spent half an hour in the middle eating lunch. Also, none of the problems were accepted on the first try; I debugged all of them with `printf`, which took quite a bit of time. The fastest experts finished in 20 minutes.

Let's do Round B next month! April 19, 7 AM.

[Round A problem link](https://codingcompetitions.withgoogle.com/kickstart/round/000000000019ffc7)

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/kick-start-2020-round-A/en-hero.webp" alt="A circular contest workshop packs small houses, selects stacked plates, splits a long track and bundles ribbons deep in a branching tree" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## Allocation

At first I thought it was a simple knapsack problem, but later realized it was an even simpler greedy problem. Because the goal is to maximize the number of houses bought, rather than maximize value, or in other words every house has value 1. So greedy is enough. In short, it is an easy warm-up problem.
Sort the houses by price from small to large, and buy the cheap ones first.

Time complexity: O(N log N),
space complexity: O(N).

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

vector<int> A(100005);

int main() {
    int T;
    cin >> T;

    for (int iCase = 1; iCase <= T; ++iCase) {
        int N, B;
        cin >> N >> B;
        for (int i = 0; i < N; ++i) {
            cin >> A[i];
        }
        sort(A.begin(), A.begin() + N);
        int ans = 0;
        int index = 0;
        while (index < N && B >= A[index]) {
            B -= A[index];
            ++ans;
            ++index;
        }
        cout << "Case #" << iCase << ": " << ans <<endl;
    }

    return 0;
}
```
Because I forgot to check `index < n` during the greedy process, I got one WA penalty.

## Plates

Dynamic Programming.
State transition:
For the `i`-th stack, after taking `j` plates, the maximum Beauty value obtained.
`dp[i][j] = max{dp[i-1][x] + top_sum[j - x] for x in [0, j]}`.

Time complexity: O(N * P * P) 50 * 1500 * 1500,
space complexity: O(N * P) 50 * 1500 -> O(P) 1500.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

vector<int> dp(1505, 0);
vector<int> line(35);

int main() {
    int T;
    cin >> T;

    for (int iCase = 1; iCase <= T; ++iCase) {
        int N, K, P;
        cin >> N >> K >> P;
        fill(dp.begin(), dp.end(), 0);
        for (int i = 0; i < N; ++i) {
            for (int j = 0; j < K; ++j) {
                cin >> line[j];
                if (j > 0)
                    line[j] += line[j - 1];
            }
            for (int j = P; j > 0; --j) {
                int max_dp = dp[j];
                for (int x = j - 1; x >= 0 && j - x - 1 < K; --x) {
                    max_dp = max(max_dp, dp[x] + line[j - x - 1]);
                }
                dp[j] = max_dp;
                // cout << dp[j] << ", ";
            }
            // cout << endl;
        }
        cout << "Case #" << iCase << ": " << dp[P] <<endl;
    }

    return 0;
}
```

## Workout

For the case `K == 1`, we can directly use a greedy strategy and split the largest interval evenly.

For the case `K > 1`, we can solve it by converting the optimization problem into a decision problem.
The optimization problem is: find the minimum difficulty.
The decision problem is: can `difficulty == x` be achieved? The time complexity is `O(N * K)`.
Then the distribution of answers to the decision problem is: `... F F F T T T ...`, and we need to find the first `T`.
Use binary search, with search interval `[1, max(M_i) = 1^9]`, and time complexity `O(log 1e9)`.
So overall:
time complexity: O(N * K * log 1e9),
space complexity: O(N).

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

vector<int> M(1e5 + 5);

int main() {
    int T;
    cin >> T;

    for (int iCase = 1; iCase <= T; ++iCase) {
        int N, K;
        cin >> N >> K;
        for (int i = 0; i < N; ++i) {
            cin >> M[i];
        }
        auto determine = [&](int x) -> bool {
            int used = 0;
            for (int i = 0; i < N - 1; ++i) {
                int difference = M[i + 1] - M[i];
                while (difference > x) {
                    ++used;
                    if (used > K)
                        return false;
                    difference -= x;
                }
            }
            return true;
        };
        int lo = 1, hi = 1e9;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            auto deter = determine(mid);
            // cout << mid << ": " << deter << endl;
            if (deter) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        cout << "Case #" << iCase << ": " << lo <<endl;
    }

    return 0;
}
```

## Bundling

Trie + DFS + Greedy.

Because common prefixes are needed, we can preprocess the strings with a Trie.
Use DFS to search the Trie and try to form groups as deep as possible, which gives a larger score.

Time complexity: O(characters.size()), 2 * 10 ^ 6.
Space complexity: O(characters.size()).

```cpp
#include <algorithm>
#include <iostream>
#include <memory>
#include <vector>
#include <functional>
#include <cassert>

using namespace std;

struct Trie {
    int passby = 0;
    int destination = 0;
    array<shared_ptr<Trie>, 26> data;
};

void build_tire(shared_ptr<Trie> root, const string &s, int index) {
    ++root->passby;
    if (index == s.size()) {
        ++root->destination;
        return;
    }
    if (root->data[s[index] - 'A'] == nullptr) {
        root->data[s[index] - 'A'] = make_shared<Trie>();
    }
    build_tire(root->data[s[index] - 'A'], s, index + 1);
}

int main() {
    int T;
    cin >> T;

    for (int iCase = 1; iCase <= T; ++iCase) {
        int N, K;
        cin >> N >> K;
        auto root = make_shared<Trie>();
        for (int i = 0; i < N; ++i) {
            string s;
            cin >> s;
            build_tire(root, s, 0);
        }
        int ans = 0;
        // return left number
        function<int(shared_ptr<Trie>, int)> dfs = [&](shared_ptr<Trie> r,
                                                       int depth) -> int {
            if (r == nullptr)
                return 0;
            int now = r->destination;
            for (int i = 0; i < 26; ++i) {
                now += dfs(r->data[i], depth + 1);
                // cout <<  depth << ": " << char('A' + i) << ": " << now << endl;
            }
            ans += depth * (now / K);
            return now % K;
        };
        int r = dfs(root, 0);
        // cout << "r: " << r << endl;
        cout << "Case #" << iCase << ": " << ans << endl;
    }

    return 0;
}
```
