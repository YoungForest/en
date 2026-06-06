---
title: Kick Start 2020 Round B
date: 2020-04-19 20:39:28
tags:
- Competitive Programming
categories:
- KickStart
---

| ID | score | rank | Bike Tour | Bus Routes | Robot Path Coding | Wandering Robot | Time |
|--|--|--|--|--|--|--|--|
| YoungForest | 74 | 524 | 5 + 7 | 10 + 13 | 11 + 16 | 14 + 0 | 1:35:18 |

Last year I participated in six Kick Start rounds in total and successfully got Google's internship offer this year. Unfortunately, because of the pandemic, all Google China summer internship programs were canceled. This year, for full-time recruiting, I still need to continue participating in Kick Start. Although today's Round B was at 7 a.m., many classmates still joined. Unfortunately, my time complexity for the last problem was too high, and the large test set TLE'd.

## Bike Tour

A warm-up problem. Traverse `mountains` once and find positions higher than both neighbors.

Time complexity: O(N),
Space complexity: O(N).

```cpp
#include <algorithm>
#include <iostream>
#include <vector>

using namespace std;

int main() {
    int T;
    cin >> T;

    for (int iCase = 1; iCase <= T; ++iCase) {
        int N;
        cin >> N;
        vector<int> mountains(N);
        for (int i = 0; i < N; ++i) {
            cin >> mountains[i];
        }
        int ans = 0;
        for (int i = 1; i < N - 1; ++i) {
            if (mountains[i] > mountains[i - 1] &&
                mountains[i] > mountains[i + 1]) {
                ++ans;
            }
        }
        cout << "Case #" << iCase << ": " << ans << endl;
    }

    return 0;
}
```

## Bus Routes

Greedy. Go backward and choose the latest possible bus for each route.

Time complexity: O(N). I used tail recursion here, which is the same as iteration and does not need extra space.
Space complexity: O(N).

```cpp
#include <algorithm>
#include <iostream>
#include <vector>
#include <functional>

using namespace std;

using ll = long long;

vector<ll> schedule(1005);

int main() {
    int T;
    cin >> T;

    for (int iCase = 1; iCase <= T; ++iCase) {
        int N;
        ll D;
        cin >> N >> D;
        for (int i = 0; i < N; ++i) {
            cin >> schedule[i];
        }
        function<ll(int, ll)> f = [&](int index, ll d) -> ll {
            if (index == 0) {
                return (d / schedule[index]) * schedule[index];
            } else {
                return f(index - 1, (d / schedule[index]) * schedule[index]);
            }
        };
        
        cout << "Case #" << iCase << ": " << f(N-1, D) << endl;
    }

    return 0;
}
```

## Robot Path Decoding

Similar to a compiler supporting function calls, we can use a stack to store the state of the parent program.

Time complexity: O(N),
Space complexity: O(N).

```cpp
#include <algorithm>
#include <functional>
#include <iostream>
#include <stack>
#include <vector>

using namespace std;

using ll = long long;

const ll mod = 1e9;

int main() {
    int T;
    cin >> T;

    for (int iCase = 1; iCase <= T; ++iCase) {
        string instructions;
        cin >> instructions;
        pair<ll, ll> shift = {0, 0};
        stack<ll> st;
        stack<pair<ll, ll>> st_shift;
        char digit = '1';
        for (char c : instructions) {
            switch (c) {
            case '(':
                st.push(digit - '0');
                st_shift.push(shift);
                shift = {0, 0};
                break;
            case ')': {
                ll X = st.top();
                auto before_shift = st_shift.top();
                st.pop();
                st_shift.pop();
                before_shift.first =
                    (before_shift.first + X * shift.first + 12 * mod) % mod;
                before_shift.second =
                    (before_shift.second + X * shift.second + 12 * mod) % mod;
                shift = before_shift;
            } break;
            case 'N':
                shift.second = (shift.second - 1 + mod) % mod;
                break;
            case 'S':
                shift.second = (shift.second + 1) % mod;
                break;
            case 'E':
                shift.first = (shift.first + 1) % mod;
                break;
            case 'W':
                shift.first = (shift.first - 1 + mod) % mod;
                break;
            default:
                break;
            }
            digit = c;
        }

        cout << "Case #" << iCase << ": " << shift.first + 1 << " " << shift.second + 1
             << endl;
    }

    return 0;
}
```

## Wandering Robot

During the contest, I only solved the small case. The large data set TLE'd.

The idea is simple: use DP to calculate the probability of reaching each grid cell.

`dp[i][j] = 0.5 * dp[i-1][j] + 0.5 * dp[i][j-1]`.

Then calculate the sum of probabilities of reaching the top and left sides of the hole. Multiplying by 0.5 gives the probability of falling into the hole. To avoid handling the special cases at the bottom and right boundaries, when the hole touches the bottom, we can expand the hole to the left and intercept the probability that must fall into the hole earlier. The case where the hole touches the right boundary is handled similarly.

Time complexity: O(W * T),
Space complexity: O(W).

```cpp
#include <algorithm>
#include <functional>
#include <iostream>
#include <stack>
#include <vector>

using namespace std;

using ll = long long;

const ll mod = 1e9;
const int MAX_WIDTH = 1e5 + 5;

int main() {
    int T;
    cin >> T;
    vector<double> dp(MAX_WIDTH);

    for (int iCase = 1; iCase <= T; ++iCase) {
        int W, H, L, U, R, D;
        cin >> W >> H >> L >> U >> R >> D;
        --L;
        --U;
        double ans = 0;
        if (D == H && R == W) {
            ans = 1;
        } else {
            if (D == H) {
                // 触底
                L = 0;
            }
            if (R == W) {
                // 触右
                U = 0;
            }
            if (U == 0 && L == 0) {
                ans = 1;
            } else {
                for (int i = 0; i < D; ++i) {
                    for (int j = 0; j < MAX_WIDTH; ++j) {
                        if (i > 0 && j > 0) {
                            dp[j] = 0.5 * dp[j] + 0.5 * dp[j - 1];
                        } else if (j > 0) {
                            dp[j] = 0.5 * dp[j - 1];
                        } else if (i > 0) {
                            dp[j] = 0.5 * dp[j];
                        } else {
                            dp[j] = 1;
                        }
                    }
                    if (L > 0 && i >= U && i < D) {
                        ans += 0.5 * dp[L - 1];
                    }
                    if (U > 0 && i == U - 1) {
                        for (int j = L; j < R; ++j) {
                            ans += 0.5 * dp[j];
                        }
                    }
                }
            }
        }
        cout << "Case #" << iCase << ": " << (1 - ans) << endl;
    }

    return 0;
}
```

One important observation is that we can use combinations to calculate `prop(i, j)`: the probability of moving from the origin to `(i, j)`, 0-indexed.

`prop(i, j) = 0.5 ^ (the length path to (i, j)) * number of possible paths`

`= 0.5 ^ (i + j) * Combination(i + j, j)`

To quickly calculate combinations, we can precompute `log(x!)`, then use the formula:

$$ 2^{log_2(n! / (k! \times (n-k)!) / 2^n)} = 2^{log_2 n! - log_2 k! - log_2 (n-k)! - n} $$

Time complexity: O(W + H),
Space complexity: O(W).

```cpp
#include <algorithm>
#include <cmath>
#include <functional>
#include <iostream>
#include <stack>
#include <vector>

using namespace std;

using ll = long long;

const ll mod = 1e9;
const int MAX_WIDTH = 1e5 + 5;

double logfactor[2 * MAX_WIDTH];

double CombinationDivide2N(ll n, ll m) {
    return powl(2, logfactor[n] - logfactor[m] - logfactor[n-m] - n);
}

int main() {
    int T;
    cin >> T;
    // preprocess log(x!)
    logfactor[0] = 0;
    for (int i = 1; i < 2 * MAX_WIDTH; ++i) {
        logfactor[i] = logfactor[i-1] + log2l(i);
    }

    auto dp = [&](int i, int j) -> double {
        return CombinationDivide2N(i + j, j);
    };
    for (int iCase = 1; iCase <= T; ++iCase) {
        int W, H, L, U, R, D;
        cin >> W >> H >> L >> U >> R >> D;
        --L;
        --U;
        double ans = 0;
        if (W > MAX_WIDTH || H > MAX_WIDTH) {
            ans = 1;
        } else if (D == H && R == W) {
            ans = 1;
        } else {
            if (D == H) {
                // 触底
                L = 0;
            }
            if (R == W) {
                // 触右
                U = 0;
            }
            if (U == 0 && L == 0) {
                ans = 1;
            } else {
                if (U > 0) {
                    for (int i = L; i < R; ++i) {
                        ans += 0.5 * dp(i, U - 1);
                    }
                }
                if (L > 0) {
                    for (int i = U; i < D; ++i) {
                        ans += 0.5 * dp(L - 1, i);
                    }
                }
            }
        }
        cout << "Case #" << iCase << ": " << (1 - ans) << endl;
    }

    return 0;
}
```
