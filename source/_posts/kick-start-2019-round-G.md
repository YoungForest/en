---
title: Kick Start 2019 Round G
date: 2019-10-20 18:53:36
tags:
- Competitive Programming
categories:
- KickStart
---

This round was the second-to-last round of the year, and also a relatively easy one.

I solved the third problem and the small data sets for problems 1 and 2. My algorithm for the second problem was correct in itself, but I did not correctly estimate the maximum number of bits in `k` or prevent overflow, so I got WA on the large data set. The first problem was not hard either, but I was not sensitive enough to divisors, so I missed the better solution. Overall, this was the closest I got to AC in a round. My luck was relatively good, and I finished the contest one hour early. Later, because I really could not think of more solutions or details to watch out for, I gave up.

## Book Reading

Brute force plus memoization can pass directly. But I wrote the memoization wrong and forgot to actually memoize. That caused TLE and lost quite a few points. What a pity.

Also, if you can use `long long`, do not use `int`; otherwise the final `ans` will overflow.

Time complexity: O(N log N).

Because memoization exists, the number of pages calculated is at most `1 + 1 / 2 + 1 / 3 + ... + 1 / N = log N`, the harmonic series.

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>

using namespace std;
using ll = long long;

int main() {
    ll T;
    cin >> T;
    for (ll iCase = 1; iCase <= T; ++iCase) {
        ll ans = 0;
        ll N, M, Q;
        cin >> N >> M >> Q;
        vector<bool> pages(N + 1, true);
        pages[0] = false;
        for (ll i = 0; i < M; ++i) {
            ll P;
            cin >> P;
            pages[P] = false;
        }
        unordered_map<ll, ll> memo;
        for (ll i = 0; i < Q; ++i) {
            ll R;
            cin >> R;
            ll count = 0;
            if (memo.find(R) != memo.end()) {
                count = memo[R];
            } else {
                for (ll j = 1; j * R <= N; ++j) {
                    if (pages[j * R]) {
                        ++count;
                    }
                }
                memo[R] = count;
            }
            ans += count;
        }
        cout << "Case #" << iCase << ": " << ans << endl;
    }
}
```

## The Equation

There are quite a few details to pay attention to in this problem, though the algorithm itself is not difficult.

1. The `long long` constant should be `1L`; otherwise `1` defaults to `int`, which creates overflow risk.
2. We cannot start searching from the highest bit in A, because `k` may be a larger number. Instead, we should consider the maximum value of `k` from the perspective of M. Here M has 15 decimal digits, corresponding to around 50 binary bits. However, we also cannot start from too high a bit, otherwise `N * (1 << i)` will overflow 64 bits. Since the maximum N is 1000, around 10 binary bits, we can start searching from bits 50 to 53.

Since we want to maximize `k`, we can use a greedy idea: from high bits to low bits, try to place 1 whenever possible; otherwise place 0 and backtrack when the requirement is exceeded.

Because there is a lot of backtracking and pruning, the time complexity is not easy to analyze. But it can pass the large data set.

```cpp
#include <iostream>
#include <vector>

using namespace std;
using ll = long long;

ll backtracking(const vector<ll> &bits, const ll N, const ll M, ll position,
                ll accu, ll k) {
    ll one = bits[position];
    ll zero = N - one;
    // k always wants to be 1 in position i
    ll i = position;
    if (position < 0) {
        return k;
    }

    ll ret = -1;
    if (accu + zero * (1L << i) <= M) { // if one
        ret = backtracking(bits, N, M, position - 1,
                           accu + zero * (1L << i),
                           k + (1L << i));
    }
    if (ret == -1) { // zero
        if (accu + one * (1L << i) <= M) {
            ret = backtracking(bits, N, M, position - 1,
                               accu + one * (1L << i), k);
        }
    }
    return ret;
}

int main() {
    ll T;
    cin >> T;
    for (ll iCase = 1; iCase <= T; ++iCase) {
        ll ans = 0;
        ll N, M;
        cin >> N >> M;
        vector<ll> bits(64, 0);
        ll max_position = 0;
        for (ll i = 0; i < N; ++i) {
            ll A;
            cin >> A;
            ll position = 0;
            while (A > 0) {
                ll last_bit = A % 2;
                if (last_bit > 0) {
                    ++bits[position];
                    max_position = max(max_position, position);
                }
                A = A / 2;
                ++position;
            }
        }
        ans = backtracking(bits, N, M, 53, 0, 0);

        cout << "Case #" << iCase << ": " << ans << endl;
    }
}
```

## Shifts

Same as the second problem, I used backtracking with a lot of pruning and memoization to enumerate all combinations. I did not expect it to pass.

The official solution uses divide and conquer: enumerate the combinations of two smaller sets separately, then iterate over all combinations and find the number of combinations that satisfy the requirements after merging. This is the meet-in-the-middle approach.

```cpp
#include <cmath>
#include <iostream>
#include <map>
#include <vector>

using namespace std;
using ll = long long;

ll backtracking(const vector<ll> &A, const vector<ll> &B,
                const vector<ll> &suffixA, const vector<ll> &suffixB, ll needA,
                ll needB, ll position, map<tuple<ll, ll, ll>, ll> &memo,
                map<tuple<ll, ll>, ll> &memoA, map<tuple<ll, ll>, ll> &memoB) {
    const ll n = A.size();
    if (position >= n) {
        return needA <= 0 && needB <= 0 ? 1 : 0;
    }
    auto it = memo.find({needA, needB, position});
    if (it != memo.end()) {
        return it->second;
    }
    if (needA <= 0 && needB <= 0) {
        return memo[{needA, needB, position}] = pow(3, n - position);
    }
    if (needA > suffixA[position] || needB > suffixB[position]) {
        return memo[{needA, needB, position}] = 0;
    }
    if (needA <= 0) {
        auto it = memoA.find({needB, position});
        if (it != memoA.end()) {
            return it->second;
        }
    }
    if (needA <= 0) {
        auto it = memoA.find({needB, position});
        if (it != memoA.end()) {
            return it->second;
        }
    }
    if (needB <= 0) {
        auto it = memoB.find({needA, position});
        if (it != memoB.end()) {
            return it->second;
        }
    }
    ll ret = 0;
    ret += backtracking(A, B, suffixA, suffixB, needA - A[position],
                        needB - B[position], position + 1, memo, memoA, memoB);
    ret += backtracking(A, B, suffixA, suffixB, needA, needB - B[position],
                        position + 1, memo, memoA, memoB);
    ret += backtracking(A, B, suffixA, suffixB, needA - A[position], needB,
                        position + 1, memo, memoA, memoB);
    if (needA <= 0) {
        memoA[{needB, position}] = ret;
    }
    if (needB <= 0) {
        memoB[{needA, position}] = ret;
    }
    return memo[{needA, needB, position}] = ret;
}

int main() {
    ll T;
    cin >> T;
    for (ll iCase = 1; iCase <= T; ++iCase) {
        ll N, H;
        cin >> N >> H;
        vector<ll> A(N), B(N), suffixA(N), suffixB(N);
        for (int i = 0; i < N; ++i) {
            cin >> A[i];
        }
        for (int i = 0; i < N; ++i) {
            cin >> B[i];
        }
        if (N > 0) {
            suffixA[N - 1] = A[N - 1];
            suffixB[N - 1] = B[N - 1];
        }
        for (int i = N - 2; i >= 0; --i) {
            suffixA[i] = A[i] + suffixA[i + 1];
            suffixB[i] = B[i] + suffixB[i + 1];
        }
        map<tuple<ll, ll, ll>, ll> memo;
        map<tuple<ll, ll>, ll> memoA;
        map<tuple<ll, ll>, ll> memoB;
        auto ans =
            backtracking(A, B, suffixA, suffixB, H, H, 0, memo, memoA, memoB);

        cout << "Case #" << iCase << ": " << ans << endl;
    }
}
```
