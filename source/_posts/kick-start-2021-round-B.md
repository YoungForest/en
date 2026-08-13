---
title: Kick Start 2021 Round B
date: 2021-04-20 19:31:25
tags:
- Competitive Programming
categories:
- KickStart
translations:
  zh-CN: https://youngforest.github.io/2021/04/20/kick-start-2021-round-B/
  en: https://youngforest.github.io/en/2021/04/20/kick-start-2021-round-B/
---
The contest time was 7 a.m. to 10 a.m. Beijing time on the 19th, and it happened that I had to submit the first draft of my graduation thesis at 2 p.m. Recently I had been busy writing the thesis, so I did not participate in Round B. Now I have already passed the age of campus recruitment, so competing in Kick Start is purely for fun.

Post-contest upsolving.

[Contest problem link](https://codingcompetitions.withgoogle.com/kickstart/round/0000000000435a5b)



## [Increasing Substring](https://codingcompetitions.withgoogle.com/kickstart/round/0000000000435a5b/000000000077a882)

A warm-up problem. DP. You only need to compare with the previous character.

```cpp
#include <vector>
#include <string>
#include <iostream>

using namespace std;

void solve() {
    int N ;
    cin >> N;
    string s;
    cin >> s;
    vector<int> dp(N);
    for (int i = 0; i < N; ++i) {
        if (i > 0 && s[i] > s[i-1]) {
            dp[i] = dp[i-1] + 1;
        } else {
            dp[i] = 1;
        }
        cout << dp[i] << " ";
    }
}

int main() {
    int T;
    cin >> T;
    for (int i = 0; i < T; ++i) {
        cout << "Case #" << i + 1 << ": ";
        solve();
        cout << endl;
    }
    return 0;
}
```

Time complexity: O(N),
space complexity: O(N) -> O(1). Although my implementation is O(N), the `dp` array is actually unnecessary; only the previous value matters.

## [Longest Progression](https://codingcompetitions.withgoogle.com/kickstart/round/0000000000435a5b/000000000077a3a5)

I hit WA because of C++ type issues. I list it here as a warning.

Because of type conversion, when comparing `size()` with `-1`, `-1` is always larger.

```cpp
#include <vector>
#include <string>
#include <iostream>
#include <unordered_map>
#include <unordered_set>
#include <functional>
#include <map>

using namespace std;

using ll = int;


template <typename T, class Cmp>
ostream& operator <<(ostream& out, const unordered_set<T, Cmp>& a) {
  out << "{"; bool first = true;
  for (auto& v : a) { out << (first ? "" : ", "); out << v; first = 0;} out << "}";
  return out;
}


template <typename U, typename T, class Cmp>
ostream& operator <<(ostream& out, const unordered_map<U, T, Cmp>& a) {
  out << "{"; bool first = true;
  for (auto& p : a) { out << (first ? "" : ", "); out << p.first << ":" << p.second; first = 0;} out << "}";
  return out;
}

int solve() {
    int N ;
    cin >> N;
    vector<int> nums(N);
    for (int i = 0; i < N; ++i) {
        cin >> nums[i];
    }
    if (N <= 3) return N;
    vector<int> diff(N);
    diff[0] = 0;
    for (int i = 1; i < N; ++i) {
        diff[i] = nums[i] - nums[i-1];
    }
    int hi = N + 1;
    int lo = 4;
    auto binary = [&](ll lo, ll hi, function<bool(const ll)> predicate) -> int {
        // return first true
        while (lo < hi) {
            ll mid = lo + (hi - lo) / 2;
            if (predicate(mid)) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        return lo;
    };
    auto pred = [&](const ll x) -> bool {
        unordered_map<int, unordered_set<int>> cnt;
        map<int, unordered_set<int>, greater<int>> reverseCnt; // cnt->value, cnt->count
        auto addCnt = [&](const int i) -> void {
            cnt[diff[i]].insert(i);
            reverseCnt[cnt[diff[i]].size()].insert(diff[i]);
            reverseCnt[cnt[diff[i]].size() - 1].erase(diff[i]);
            if (reverseCnt[cnt[diff[i]].size() - 1].empty()) {
                reverseCnt.erase(cnt[diff[i]].size() - 1);
            }
        };
        auto eraseCnt = [&](const int i) -> void {
            auto& first = cnt[diff[i]];
            first.erase(first.find(i));
            reverseCnt[cnt[diff[i]].size() + 1].erase(diff[i]);
            if (reverseCnt[cnt[diff[i]].size() + 1].empty()) {
                reverseCnt.erase(cnt[diff[i]].size() + 1);
            }
            if (first.empty()) {
                cnt.erase(diff[i]);
            }
            reverseCnt[cnt[diff[i]].size()].insert(diff[i]);
        };

        for (int i = 1; i < x; ++i) {
            addCnt(i);
        }
        auto check = [&](const int lastIdx) -> bool {
            // cout << lastIdx << " ... " << cnt << " " << maxDiffIndex << " " << maxDiffValue << endl;
            auto it = reverseCnt.begin();
            const int maxDiffValue = it->first;
            const int maxDiffIndex = *(it->second.begin());
            if (maxDiffValue == x - 1) return true;
            else if (maxDiffValue == x - 2 || maxDiffValue == x - 3) {
                vector<int> index;
                index.reserve(2);
                for (const auto& p : cnt) {
                    if (p.first != maxDiffIndex) {
                        for (const auto& idx : p.second) {
                            index.push_back(idx);
                        }
                    }
                }
                if (maxDiffValue == x - 2) {
                    if (index[0] == lastIdx || index[0] == lastIdx - x + 2) return true;
                    else return false;
                } else if (maxDiffValue == x - 3) {
                    if (index[1] < index[0]) {
                        swap(index[0], index[1]);
                    }
                    if (index[0] + 1 != index[1]) return false;
                    if (nums[index[1]] - nums[index[0] - 1] == maxDiffIndex * 2) return true;
                    else return false;
                } else return false;
            } else return false;
        };
        // cout << "Debug: " << maxDiffValue << endl;
        if (check(x-1)) return false;
        // if check
        for (int i = x; i < N; ++i) {
            // cout << "before: " << cnt ;
            eraseCnt(i - x + 1);
            addCnt(i);
            // cout << "after: " << cnt << " !!! ";
            if (check(i)) return false;
        }
        return true;
    };
    // for (int i = 4; i < hi; ++i) {
    //     cout << i << ":" << pred(i) << " ";
    // }
    return binary(lo, hi, pred) - 1;
}

int main() {
    int T;
    cin >> T;
    for (int i = 0; i < T; ++i) {
        cout << "Case #" << i + 1 << ": ";
        cout << solve();
        cout << endl;
    }
    return 0;
}
```

Time complexity: O(N log N log N),
space complexity: O(N).

By rights, `N = 10 ^ 5` should not time out, but the large case still TLE'd. It was probably killed by constant factors.

After reading the official solution, the time complexity is O(N). Two extra logs still do not work.



## [Consecutive Primes](https://codingcompetitions.withgoogle.com/kickstart/round/0000000000435a5b/000000000077a8e6)

## [Truck Delivery](https://codingcompetitions.withgoogle.com/kickstart/round/0000000000435a5b/000000000077a885)
