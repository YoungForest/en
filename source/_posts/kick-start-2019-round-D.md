---
title: kick start 2019 round D
date: 2019-07-28 16:19:32
tags:
- Competitive Programming
categories:
- KickStart
translations:
  zh-CN: https://youngforest.github.io/2019/07/28/kick-start-2019-round-D/
  en: https://youngforest.github.io/en/2019/07/28/kick-start-2019-round-D/
---
Rank: 765 / 1866.

## X or What

This problem is about finding patterns and tests one's familiarity with xor. In fact, I had once been very close to the correct solution. But I was fixated on my past experience solving interval problems with segment trees, trying to figure out what information each node should record. As a result, I went further and further off track.

To summarize, the pattern is:
The problem gives the definition of xor-even.
From the properties of xor, we have:
- odd xor odd -> even
- odd xor even -> odd
- even xor even -> even

To make the final xor-even, the number of odd elements in the interval must be even. A very direct idea follows. Count the number of odd elements. If it is even, the longest interval is the length of the whole array. If it is odd, remove either the head or the tail and find the longer one.

```cpp
#include <set>
#include <cstdio>

using namespace std;

const int N = 1e5 + 5;
int n, q;
int a[N], v[N];
int p[N];

int even(int i) {
  int count = 0;
  while (i > 0) {
    count += i % 2;
    i /= 2;
  }
  return count;
}

void read_input() {
  scanf("%d %d", &n, &q);
  for (int i = 0; i < n; ++i) {
    int x;
    scanf("%d", &x);
    a[i] = even(x) & 1;
  }
  for (int i = 0; i < q; ++i) {
      int x;
    scanf("%d %d", p+i, &x);
    v[i] = even(x) & 1;
  }
}

void solve() {
  set<int> idx[2];
  for (int i = 0; i < n; ++i) {
    idx[a[i]].insert(i);
  }
  for (int i = 0; i < q; ++i) {
    if (a[p[i]] != v[i]) {
      idx[a[p[i]]].erase(p[i]);
      a[p[i]] = v[i];
      idx[a[p[i]]].insert(p[i]);
    }
    if (i)
      printf(" ");
    if (idx[1].size() % 2 == 0) {
      printf("%d", n);
    }
    else {
      int ans = max(*idx[1].rbegin(), n-*idx[1].begin()-1);
      printf("%d", ans);
    }
  }
  printf("\n");
}

int main() {
#ifdef LOCAL
  time_t starttime = clock();
#endif
  int tt;
  scanf("%d", &tt);
  for (int tc = 1; tc <= tt; tc++) {
    printf("Case #%d: ", tc);
    read_input();
    solve();
#ifdef LOCAL
    cerr << "~ TC#" << tc << " done! execution time: " <<
      (double)(clock()-starttime) / CLOCKS_PER_SEC << " s" << endl;
#endif
  }
  return 0;
}
```
