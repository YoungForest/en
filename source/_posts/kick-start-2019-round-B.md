---
title: kick start 2019 round B
date: 2019-07-24 11:04:30
tags:
- Competitive Programming
categories:
- KickStart
translations:
  zh-CN: https://youngforest.github.io/2019/07/24/kick-start-2019-round-B/
  en: https://youngforest.github.io/en/2019/07/24/kick-start-2019-round-B/
---
A post-contest write-up.
[Problem link](https://codingcompetitions.withgoogle.com/kickstart/round/0000000000050eda)

## Building Palindromes

Given a string of length N and Q queries. Each query is a range, from which we can get a substring. Determine whether the substring can be rearranged into a palindrome. Since arbitrary rearrangement is allowed, the order of characters in the substring does not matter; what matters is the frequency of each character. If the number of characters with odd frequency is 0 or 1, the substring can be rearranged into a palindrome.
Because N and Q are large, `10^5`, a quadratic algorithm will time out. Here we borrow the prefix sum idea to quickly compute character frequencies in a substring.
The time complexity is linear.

```cpp
#include <vector>
#include <iostream>
#include <unordered_map>
#include <algorithm>

using namespace std;

int main(int argc, char const *argv[])
{
    int T;
    cin >> T;
    for (int i = 1; i <= T; ++i) {
        int N, Q;
        cin >> N >> Q;
        string s;
        cin >> s;
        vector<vector<int>> prefix(N+1);
        prefix[0] = vector<int>(26, 0);
        for (int j = 1; j <= N; ++j) {
            prefix[j] = prefix[j-1];
            ++prefix[j][s[j-1] - 'A'];
        }
        int ans = 0;
        for (int j = 0; j < Q; ++j) {
            int L, R;
            cin >> L >> R;
            const auto& l = prefix[L-1], r = prefix[R];
            int odd = 0;
            for (int k = 0; k < 26; ++k) {
                if ((r[k] - l[k]) % 2 == 1)
                    ++odd;
            }
            if (odd <= 1)
                ++ans;
        }
        cout << "Case #" << i << ": " << ans << endl;
    }
    return 0;
}
```

## Energy Stones

The idea of this problem is greedy + dynamic programming (a kind of [0-1 knapsack](https://en.wikipedia.org/wiki/Knapsack_problem)). For more knapsack problems, see [Pack PDF](https://comzyh.com/upload/PDF/Pack-PDF-Comzyh.pdf).
The hard part is that the traversal order for the knapsack needs to be sorted with a greedy idea.

For the small test set where S is the same, traverse stones with larger L first. This minimizes the lost energy.
For the large test set where S differs, the order of two stones `i` and `j` is determined by `S_i * L_j`. If `S_i * L_j < S_j * L_i`, eating `i` first loses less energy.

The correctness of the greedy approach is also easy to prove. If we have a sequence for eating stones, it must be in the order that loses less energy. Otherwise, swapping the order can obtain more energy.

Time complexity: O(N * N * S_i).
Space complexity: O(N * N * S_i), which can be further optimized to O(N * S_i)

```cpp
#include <algorithm>
#include <iostream>
#include <tuple>
#include <vector>

using namespace std;

struct Stone {
  int s, e, l;
  bool operator<(const Stone &p) const { return s * p.l < l * p.s; }
};

int main(int argc, char const *argv[]) {
  int T;
  cin >> T;
  for (int iCase = 1; iCase <= T; ++iCase) {
    int N;
    cin >> N;
    vector<Stone> data(N + 1);
    int S_sum = 0;
    for (int i = 1; i <= N; ++i) {
      cin >> data[i].s >> data[i].e >> data[i].l;
      S_sum += data[i].s;
    }
    sort(data.begin() + 1, data.end());
    vector<vector<int>> dp(N + 1, vector<int>(S_sum + 1));
    for (int i = 1; i <= N; ++i) {
      for (int time = 0; time <= S_sum; ++time) {
        if (time < data[i].s) {
          dp[i][time] = dp[i - 1][time];
        } else {
          dp[i][time] =
              max(dp[i - 1][time],
                  dp[i - 1][time - data[i].s] +
                      max(0, data[i].e - data[i].l * (time - data[i].s)));
        }
      }
    }
    int ans = 0;
    for (int time = 0; time <= S_sum; ++time) {
      ans = max(ans, dp[N][time]);
    }
    cout << "Case #" << iCase << ": " << ans << endl;
  }
  return 0;
}
```

## Diverse Subarray

Like the first problem, this problem also needs prefix sums, but this transformation is not very obvious.
First convert the types sequence into an increase/decrease event sequence. Then it becomes clear that the prefix sum of the event sequence is the final number of gifts we need to optimize.
Then, to quickly obtain the maximum prefix sum, we can use a [Segment Tree](// https://www.geeksforgeeks.org/maximum-prefix-sum-given-range/). It supports querying the maximum prefix sum in a range in `O(log N)` and updating the segment tree in `O(log N)`.

Overall time complexity: O(N log N)
- Initialize the segment tree in O(N log N)
- For N starting points, update and query the segment tree in O(log N)

```cpp
#include <algorithm>
#include <iostream>
#include <limits>
#include <unordered_map>
#include <vector>

using namespace std;

struct SegmentTree {
  struct Node {
    int sum = numeric_limits<int>::min();
    int prefix = numeric_limits<int>::min();
    bool isValid() const {
      return sum != numeric_limits<int>::min() &&
             prefix != numeric_limits<int>::min();
    }
    Node &operator=(const Node &rhs) {
      this->sum = rhs.sum;
      this->prefix = rhs.prefix;
      return *this;
    }
  };
  const int MAX_N = 1 << 17;

  // 存储线段树的全局数组
  vector<Node> dat;
  int n;
  // 初始化
  SegmentTree(int n_, int a[]) {
    // 为简单起见，把元素个数扩大到2的幂
    n = 1;
    while (n < n_)
      n *= 2;

    // 把所有的值都设为INT_MAX
    dat.resize(2 * n);
    for (int i = 0; i < n_; ++i) {
      update(i, a[i]);
    }
  }

  void print() const {
    for (const auto &d : dat) {
      cout << "{" << d.sum << ", " << d.prefix << "}, ";
    }
    cout << endl;
  }

  // 把第k个值(0-indexed)更新为a
  void update(int k, int a) {
    // 叶子节点
    k += n - 1;
    dat[k].sum = a;
    dat[k].prefix = a;
    // 向上更新
    while (k > 0) {
      k = (k - 1) / 2;
      if (!dat[2 * k + 2].isValid())
        dat[k] = dat[2 * k + 1];
      else {
        dat[k].sum = dat[2 * k + 1].sum + dat[2 * k + 2].sum;
        dat[k].prefix = max(dat[2 * k + 1].prefix,
                            dat[2 * k + 1].sum + dat[2 * k + 2].prefix);
      }
    }
  }

  // 求[beg, end)的最小值
  // 后面的参数是为了计算起来方便而转入的。
  // k 是节点的编号, l, r表示这个节点对应的是[l, r)区间。
  // 在外部调用时，用query(index, beg, end, 0, n)
  Node query(int index, const int beg, const int end, int l, int r) const {
    // cout << "(" << index << ", " << l << ", " << r << ");" << endl;
    Node ret;
    // 如果[beg, end) 和 [l, r)不相交，则返回INT_MAX
    if (r <= beg || end <= l)
      return ret;
    // 如果[beg, end)完全包含[l, r), 则返回当前节点的值
    if (beg <= l && r <= end)
      return dat[index];
    int mid = l + (r - l) / 2;
    auto vl = query(index * 2 + 1, beg, end, l, mid);
    auto vr = query(index * 2 + 2, beg, end, mid, r);
    if (!vr.isValid()) {
      ret = vl;
    } else if (!vl.isValid()) {
      ret = vr;
    } else {
      ret.sum = vl.sum + vr.sum;
      ret.prefix = max(vl.prefix, vl.sum + vr.prefix);
    }
    return ret;
  }

  Node queryMin(const int a, const int b) const { return query(0, a, b, 0, n); }
};

// https://www.geeksforgeeks.org/maximum-prefix-sum-given-range/
int main(int argc, char const *argv[]) {
  int T;
  cin >> T;
  for (int iCase = 1; iCase <= T; ++iCase) {
    int N, S;
    cin >> N >> S;
    vector<int> A(N), events(N);
    unordered_map<int, vector<int>> types; // type, <index>
    for (int i = 0; i < N; ++i) {
      cin >> A[i];
      types[A[i]].push_back(i);
      if (types[A[i]].size() <= S) {
        events[i] = 1;
      } else if (types[A[i]].size() == S + 1) {
        events[i] = -S;
      } else {
        events[i] = 0;
      }
    }
    SegmentTree st(N, events.data());
    int ans = numeric_limits<int>::min();
    for (int i = 0; i < N; ++i) {
      auto r = st.queryMin(i, N);
      ans = max(ans, r.prefix);
      auto &v = types[A[i]];
      auto it = lower_bound(v.begin(), v.end(), i);
      if (it + S < v.end()) {
        st.update(*(it + S), 1);
        if (it + S + 1 < v.end())
          st.update(*(it + S + 1), -S);
      }
    }
    cout << "Case #" << iCase << ": " << ans << endl;
  }
  return 0;
}
```

## Afterword

After practicing two Kick Start rounds and actually participating in one, I realized that my level is still far from Google's requirements. Google's algorithm requirement is close to ACM level, so competitive programmers have a big advantage. My ability is limited, but there is still time to improve.
There are six years of past contest problems, eight rounds per year. That is enough for two months of practice.

There will be a 2019 Round D this weekend, and I will participate as practice. I will try to get full score on the first problem and the small-case scores for the last two problems.
Go, Forest!
