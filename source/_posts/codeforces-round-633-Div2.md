---
title: Codeforces Round 633 Div2
date: 2020-04-12 22:06:55
description: "Three solutions simplify diamond-belt states, reorder sorted values from the middle outward, and use doubling power to repair a nondecreasing array."
tags:
- codeforces
- contest
categories:
- Programming
translations:
  zh-CN: https://youngforest.github.io/2020/04/12/codeforces-round-633-Div2/
  en: https://youngforest.github.io/en/2020/04/12/codeforces-round-633-Div2/
---
[Official editorial](https://codeforces.com/blog/entry/75913)

Problems on Codeforces are generally harder than ordinary interview problems. If your goal is only interviews, practicing LeetCode is enough. But if you are interested in algorithms and competitive programming, I strongly encourage you to give it a try. The quantity and quality of the problems far exceed LeetCode. It also provides different tracks for students at different levels, with different problem difficulties. For high-level players, the contest experience is much better.

So far I have participated in two Div.2 contests, and my rating is 1480. Yes, the initial rating is 1500, and I actually dropped.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/codeforces-round-633-Div2/en-hero.webp" alt="A jewel workshop links two diamond states, alternates sorted pillars from the centre and raises gaps with doubling lifts" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## A. Filling Diamonds

We can think about this problem with dynamic programming. For a belt of length n, there are two states:

0.
\
/

and

1.
/
\

The state transition equations are:

`dp[n][0] = dp[n-1][1] + dp[n-1][0]`,

`dp[n][1] = dp[n-1][1]`.

Initial values:

`dp[0][1] = 1`,

`dp[0][0] = 0`.

The answer is `dp[n][0]`.

From this equation, we can quickly derive `dp[n][0] = n`.

Time complexity: O(1),
Space complexity: O(1).

```cpp
#include <iostream>
#include <vector>

using namespace std;

using ll = long long;

int main() {
    int T;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;
        cout << n << endl;
    }
    return 0;
}
```

The problem setter also proudly said that this was the simplest Div.2 A so far. The code is very simple, but the idea is still quite clever.

## B. Sorted Adjacent Differences

Greedy. Sort first, then start selecting from the middle: jump right once, then jump left once.

Time complexity: O(n log n),
Space complexity: O(n).

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

const int SIZE = 1e5 + 5;

using ll = long long;

vector<int> a(SIZE);

int main() {
    int T;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;
        for (int i = 0; i < n; ++i){
            cin >> a[i];
        }
        sort(a.begin(), a.begin() + n);
        int current = (n - 1) / 2;
        int left = current, right = current;
        bool direction = true;
        // [left, right]
        while (current >= 0 && current < n) {
            cout << a[current] << " ";
            if (direction) {
                ++right;
                current = right;
            } else {
                --left;
                current = left;
            }
            direction = !direction;
        }
        cout << endl;
    }
    return 0;
}
```

## C. Powered Addition

The problem can be transformed into this: we can add at most `2^k - 1` to any number in the array to make the whole array non-decreasing. Find the minimum `k`.

Time complexity: O(N),
Space complexity: O(N).

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

using ll = long long;

int main() {
    int T;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;
        ll max_value_in_array = numeric_limits<ll>::min();
        ll max_demand = 0;
        for (int i = 0; i < n; ++i){
            ll current;
            cin >> current;
            if (current > max_value_in_array) {
                max_value_in_array = current;
            } else {
                max_demand = max(max_demand, max_value_in_array - current);
            }
        }
        int ans = 0;
        ll could_present = 1;
        while (could_present <= max_demand) {
            ++ans;
            could_present *= 2;
        }
        cout << ans << endl;
    }
    return 0;
}
```
