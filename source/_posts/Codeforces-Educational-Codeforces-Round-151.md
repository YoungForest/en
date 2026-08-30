---
title: Codeforces Educational Codeforces Round 151
date: 2023-07-01 16:33:20
description: "Three solutions use case analysis to compose a target, measure the longest shared route, and greedily seek a password that is not a subsequence."
tags:
- codeforces
- contest
categories:
- Programming
translations:
  zh-CN: https://youngforest.github.io/2023/07/01/Codeforces-Educational-Codeforces-Round-151/
  en: https://youngforest.github.io/en/2023/07/01/Codeforces-Educational-Codeforces-Round-151/
---
[Contest link](https://codeforces.com/contest/1845)
[Official editorial](https://codeforces.com/blog/entry/117791)

I solved the first two problems and got stuck on the third.
Rating change: 1407 -> 1378

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/Codeforces-Educational-Codeforces-Round-151/en-hero.webp" alt="Allowed blocks bypass a forbidden bin, two routes share an origin before splitting, and a greedy arm chooses the farthest eligible bead" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## [A. Forbidden Integer](https://codeforces.com/contest/1845/problem/A)

Case analysis. If 1 can be chosen, then any number can definitely be formed.
If not, but 2 and 3 can be chosen, then any number except 1 can also be formed.
If only 2 can be chosen, then only even numbers can be formed.

Time complexity: O(t * n),

```python
t = int(input())

for _ in range(t):
    n, k, x = map(int, input().split())

    if x == 1:
        if k == 1:
            print('NO')
            continue
        elif k == 2:
            if n % 2 == 0:
                print('YES')
                print(n//2)
                for _ in range(n//2):
                    print(2, end=' ')
                print()
            else:
                print('NO')
                continue
        else:
            print('YES')
            print(n//2)
            while n > 3:
                print(2, end=' ')
                n -= 2
            print(n)
    else:
        print('YES')
        print(n)
        for _ in range(n):
            print(1, end=' ')
        print()
```

## [B. Come Together](https://codeforces.com/contest/1845/problem/B)

Case analysis. B and C are in different quadrants, so the longest shared route differs.

Time complexity: O(t),
Space complexity: O(1).

```python
t = int(input())

for _ in range(t):
    A = list(map(int, input().split()))
    B = list(map(int, input().split()))
    C = list(map(int, input().split()))
    B[0] = B[0] - A[0]
    B[1] = B[1] - A[1]
    C[0] = C[0] - A[0]
    C[1] = C[1] - A[1]

    # if B and C are in the same quadrant
    if (B[0] * C[0] > 0 and B[1] * C[1] > 0) \
        or (B[0] * C[0] == 0 and B[1] * C[1] > 0) \
        or (B[0] * C[0] > 0 and B[1] * C[1] == 0):
        print(min(abs(B[0]), abs(C[0])) + 1 + min(abs(B[1]), abs(C[1])) + 1 - 1)
    elif B[0] * C[0] <= 0 and B[1] * C[1] > 0: # the same side but different quadrant
        print(min(abs(B[1]), abs(C[1])) + 1)
    elif B[0] * C[0] > 0 and B[1] * C[1] <= 0: # the same side but different quadrant
        print(min(abs(B[0]), abs(C[0])) + 1)
    else: # opposite quadrant
        print(1)
```

## [C. Strong Password](https://codeforces.com/contest/1845/problem/C#)

The brute-force method is simple: backtrack to enumerate all passwords satisfying the l and r constraints, then check whether each is a subsequence of s.
However, the time complexity of the backtracking step is already exponential.

The correct solution is greedy. For each position, choose the digit that appears as far to the right as possible in s.
Time complexity: O(m*D + n), n = s.length, D = r_i - l_i = 10
