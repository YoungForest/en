---
title: Kick Start 2019 Round E
date: 2019-08-29 19:12:03
tags:
- Competitive Programming
categories:
- KickStart
---

## Cherries Mesh

Minimum spanning tree.
Pay special attention to the Union-Find implementation: `find` must use path compression to achieve O(1).
Otherwise it will time out.


```cpp
#include <iostream>
#include <vector>

using namespace std;

struct UF {
	vector<int> parents;
	UF(int n) {
		parents.resize(n);
		for (int i = 0; i < n; ++i) {
			parents[i] = i;
		}
	}

	int find(int x) {
		return parents[x] == x ? x : (parents[x] = find(parents[x]));
	}

	void unio(int x, int y) {
		int px = find(x);
		int py = find(y);
		parents[px] = py;
	}
};

int main() {
	int T;
	cin >> T;
	for (int i = 0; i < T; ++i) {
		int N, M;
		cin >> N >> M;
		UF uf(N);
		int ans = 0;
		for (int j = 0; j < M; ++j) {
			int l, r;
			cin >> l >> r;
			--l;
			--r;
			if (uf.find(l) == uf.find(r)) {
				continue;
			}
			uf.unio(l, r);
			ans += 1;
		}
		ans += (N - 1 - ans) * 2;
		cout << "Case #" << i + 1 << ": " << ans << endl;
	}
	return 0;
}
```

## Code-Eat Switcher

### Test set 1:

S = 1 or 2. We can solve it by writing inequalities.
When S = 2, there are two variables, and the process of solving inequalities is similar to linear programming.

During the contest, my idea was correct, but because I wrote the wrong return value when S = 1, I could not get AC for a long time. At that time I kept debugging the logic for S = 2 and completely failed to think that S = 1 could be wrong.

```cpp
#include <vector>
#include <iostream>
#include <unordered_map>
#include <map>
#include <cmath>

using namespace std;

bool solve(const vector<pair<int, int>>& energy, const int S, double code, double eat) {
    if (S == 1) {
        double Ci = energy[0].first;
        double Ei = energy[0].second;
        return code / Ci + eat / Ei <= 1;
    } else if (S == 2) {
        double C1 = energy[0].first, C2 = energy[1].first;
        double E1 = energy[0].second, E2 = energy[1].second;
        if (C1 / C2 == E1 / E2) {
            vector<pair<int, int>> tmp = {make_pair(energy[0].first + energy[1].first, energy[0].second + energy[1].second)};
            return solve(tmp, 1, code, eat);
        }
        double C = code, E = eat;
        double x = (C / C2 - 1 + E / E2 - E1 / E2) / (C1 / C2 - E1 / E2);
        double y = C / C2 - x * C1 / C2;
        // cout << "result: " << x << ", " << y << "|" << x * C1 + y * C2 << ", " << (1 - x) * E1 + (1 - y) * E2 << " | " << 
        // 1 - E / E2 + E1 / E2 - x * E1 / E2
        // << endl;
        if (x > 1) {
            return 1 - E / E2 + E1 / E2 - 1 * E1 / E2 >= C / C2 - 1 * C1 / C2;
        }
        if (y > 1) {
            return (1 - E / E2 + E1 / E2 - 1) * (E2 / E1) >= (C / C2 - 1) * (C2 / C1);
        }
        if (x < 0) {
            return 1 - E / E2 + E1 / E2 >= C / C2;
        }
        if (y < 0) {
            return (1 - E / E2 + E1 / E2) * (E2 / E1) >= (C / C2) * (C2 / C1);
        }
        return true;
        // return x >= 0 && y >= 0 && x <= 1 && y <= 1;
    } else {
        return false;
    }
}

int main()
{
	int T;
	cin >> T;
	for (int i = 0; i < T; ++i)
	{
		int D, S;
        cin >> D >> S;
        string ans;
        vector<pair<int, int>> energy(S);
        for (int i = 0; i < S; ++i) {
            cin >> energy[i].first >> energy[i].second;
        }
        for (int i = 0; i < D; ++i) {
            int A, B;
            cin >> A >> B;
            bool yes = solve(energy, S, A, B);
            if (yes) {
                ans.push_back('Y');
            } else {
                ans.push_back('N');
            }
        }
		cout << "Case #" << i + 1 << ": " << ans << endl;
	}
	return 0;
}
```

### Test Set 2

Observation: for each slot, for every one unit of eating energy obtained, we lose $C_i / E_i$ units of coding energy. So if $C_i / E_i < C_j / E_j$, the better choice is always i.
Based on this observation, we sort the slots by $C_i / E_i$, compute the prefix sum of E and the suffix sum of C. For each day, use binary search to find the boundary point that satisfies eating, then judge whether coding can also be satisfied.

```cpp
#include <cmath>
#include <iostream>
#include <map>
#include <unordered_map>
#include <vector>
#include <algorithm>

using namespace std;

bool solve(const vector<pair<int, int>> &energy, const int S,
           const vector<int> &prefix, const vector<int> &suffix, int code,
           int eat) {
    auto it = lower_bound(prefix.begin(), prefix.end(), eat);
    int index = distance(prefix.begin(), it);
    if (index == S) {
        return false;
    }
    double need;
    if (index == 0) {
        need = eat;
    } else {
        need = eat - prefix[index - 1];
    }
    double f = need / energy[index].second;
    double code_have;
    if (index == S - 1) {
        code_have = 0;
    } else {
        code_have = suffix[index + 1];
    }
    double code_need = code - code_have;
    return (1 - f) * energy[index].first >= code_need;
}

int main() {
    int T;
    cin >> T;
    for (int i = 0; i < T; ++i) {
        int D, S;
        cin >> D >> S;
        string ans;
        vector<pair<int, int>> energy(S);
        for (int i = 0; i < S; ++i) {
            cin >> energy[i].first >> energy[i].second;
        }
        sort(energy.begin(), energy.end(),
             [](const auto &lhs, const auto &rhs) -> bool {
                 return lhs.first / static_cast<double>(lhs.second) <
                        rhs.first / static_cast<double>(rhs.second);
             });
        // build prefix cumulative for eating
        vector<int> prefix(S);
        int current = 0;
        for (int i = 0; i < S; ++i) {
            current += energy[i].second;
            prefix[i] = current;
        }
        // build suffix cumulative for coding
        vector<int> suffix(S);
        current = 0;
        for (int i = S - 1; i >= 0; --i) {
            current += energy[i].first;
            suffix[i] = current;
        }
        // binary search for each day
        for (int i = 0; i < D; ++i) {
            int A, B;
            cin >> A >> B;
            bool yes = solve(energy, S, prefix, suffix, A, B);
            if (yes) {
                ans.push_back('Y');
            } else {
                ans.push_back('N');
            }
        }
        cout << "Case #" << i + 1 << ": " << ans << endl;
    }
    return 0;
}
```

## Street Checkers

Whether a number X is interesting can be transformed into |(# of odd divisors) - (# of even divisors)| <= 2.
For the dataset where R < 10^6, we can preprocess all numbers less than 10^6 to obtain the number of divisors for each, with time complexity O(sqrt(X)), then build the prefix sum of interesting numbers in [1, X]. For a query [L, R], we can then get the answer in O(1). The total time complexity is $O(R_{max} \sqrt{R_{max}} + T)$: preprocessing plus T test cases.

For the large dataset, we need to continue observing.
Every divisor of an odd number is still odd. Every integer X can be written as $X = A \times 2 ^ p$, where A is an odd number, and X is a non-negative integer.
Therefore, we can divide all divisors of X into groups, where $d_1$,$d_2$,...,$d_k$ are the divisors of A.

$\{d_1, d_1 * 2, d_1 * 2^2, \dots, d_1 * 2^p\}$
$\{d_2, d_2 * 2, d_2 * 2^2, \dots, d_2 * 2^p\}$
$\dots$
$\{d_k, d_k * 2, d_k * 2^2, \dots, d_k * 2^p\}$

We can see that the number of odd divisors is k, (k >= 1, at least the odd divisor 1 exists).
The number of even divisors is k * X.
The odd-even difference is $|k(X-1)| \le 2$.
The solutions are:
- Case 1: X = 0, k = 1 or 2
- Case 2: X = 1, k can be any value
- Case 3: X = 2, k = 1 or 2
- Case 4: X = 3, k = 1

- Case 1 means 1 or an odd prime.
- Case 2 means A can be any odd number, so the original number has the form $ 2*(2n + 1) = 4 * n + 2$, which can be counted in O(1).
- Case 3 means A is 1 or any odd prime. That is, the number of odd primes in [L / 4, R / 4].
- Case 4 only represents the number 8.
The number of primes can be computed using the [Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes).

Time complexity: $O(T * (R - L + 1) * log(\sqrt{R}))$

```cpp

```
