---
title: LeetCode Weekly Contest 234
date: 2021-03-28 11:42:25
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/03/28/LeetCode-weekly-contest-234/
  en: https://youngforest.github.io/en/2021/03/28/LeetCode-weekly-contest-234/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 672 / 12421 | YoungForest | 19 | 1:19:08 | 0:12:04  2 | 0:23:51 | 0:29:26 | 0:54:08  3 |

It was check-in time again. I had been doing this brutal check-in for five consecutive weeks. And honestly, I did not feel the problems were that easy this time. I got 5 WAs and my mental state more or less exploded, but the ranking was still not ideal. It feels like LeetCode is getting more and more competitive.

<!-- more -->

## 1805. Number of Different Integers in a String

This problem is actually much nicer in Python and can be implemented much faster. Python's advantages with strings and big integers are hard to beat.
I still insisted on doing it in C++, running into all kinds of unfamiliar and inconvenient string operations. I got 2 WAs.

```cpp
class Solution {
public:
    int numDifferentIntegers(string word) {
        for (char& c : word) {
            if (!isdigit(c)) {
                c = ' ';
            }
        }
        while (!word.empty() && word.back() == ' ') {
            word.pop_back();
        }
        if (word.empty()) return 0;
        std::istringstream is(word);
        unordered_set<string> ans;
        while (is)
        {
            string s;
            is >> s;
            reverse(s.begin(), s.end());
            while (s.size() > 1 && s.back() == '0') {
                s.pop_back();
            }
            // cout << "xx : " << s << endl;
            if (s.empty()) continue;
            ans.insert(s);
        }
        return ans.size();
    }
};
```

Time complexity: O(N),
space complexity: O(N).

## 1806. Minimum Number of Operations to Reinitialize a Permutation

I thought about this problem backwards.
The inverse operation moves odd positions to the second half and even positions to the first half.
Also, by observing the transformation process, we only need to track one number. If it returns to its original position, all the remaining numbers will also return to their original positions.

```cpp
class Solution {
    // operation: odd to half second, even to half first
    // [0 1 2 3 4 5] 4
    // [0 2 4 1 3 5] 3
    // [0 4 3 2 1 5] 2
    // [0 3 1 4 2 5] 1
    // [0 1 2 3 4 5] 0
    
public:
    int reinitializePermutation(int n) {
        int ans = 0;
        int index1 = 1;
        do {
            if (index1 % 2 == 1) {
                index1 = n / 2 + index1 / 2;
            } else {
                index1 = index1 / 2;
            }
            ++ans;
        } while (index1 != 1);
        return ans;
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 1807. Evaluate the Bracket Pairs of a String

To be honest, as the third problem, this did not have the corresponding difficulty. It could totally have been a warm-up problem.
Just implement it directly.
Key ideas: string processing + dictionary/Map.

```cpp
class Solution {
public:
    string evaluate(string s, vector<vector<string>>& knowledge) {
        unordered_map<string, string> m;
        for (const auto& v : knowledge) {
            m[v[0]] = v[1];
        }
        string word;
        bool left = false;
        string ans;
        for (char c : s) {
            if (c == '(') {
                left = true;
            } else if (c == ')') {
                left = false;
                auto it = m.find(word);
                if (it == m.end()) {
                    ans.push_back('?');
                } else {
                    ans.append(it->second);
                }
                word.clear();
            } else {
                if (left) {
                    word.push_back(c);
                } else {
                    ans.push_back(c);
                }
            }
        }
        return ans;
    }
};
```

Time complexity: O(s.size() + knowledge.size() * (knowledge[i][0].size() + knowledge[i][1].size())),
space complexity: O(s.size() + knowledge.size() * (knowledge[i][0].size() + knowledge[i][1].size())).

## 1808. Maximize Number of Nice Divisors

The problem can be transformed into: with a fixed sum, maximize the product.

The fixed sum is `primeFactors`. Then the number of nice divisors is the product, which is exactly the objective to maximize in the problem.
If you look at nice divisors, they are essentially the product of the counts of each prime factor.

Searching `fix sum max multiplication` on Google, the third result was a similar GeeksforGeeks problem, [Breaking an Integer to get Maximum Product](https://www.geeksforgeeks.org/breaking-integer-to-get-maximum-product/). LeetCode also has the original problem, [343](https://leetcode-cn.com/problems/integer-break/).
I tried to copy its code, but there are two differences:

- GeeksforGeeks requires a break and does not allow using the number as a whole. This problem allows not breaking it. That leads to special cases: when `primeFactors = 2` or `3`, return `n`.
- The GeeksforGeeks code uses a `for` loop to multiply by 3. The time complexity is O(n / 3), which will TLE here. I wrote my own `pow` to multiply by 3 faster, with time complexity O(log n). This is basically [LeetCode 50 Pow(x, n)](https://leetcode-cn.com/problems/powx-n/).

```cpp
class Solution {
    using ll = long long;
    const ll MOD = 1e9 + 7;
    // https://www.geeksforgeeks.org/breaking-integer-to-get-maximum-product/
    /* The main function that returns the max possible product */
    ll mypow(ll a, ll b) {
        // a ^ b
        if (b == 0) {
            return 1;
        } else if (b % 2 == 1) {
            return (mypow(a, b - 1) * a) % MOD;
        } else {
            auto x = mypow(a, b/2);
            return (x * x) % MOD;
        }
    }
    int maxProd(ll n)
    {
       // n equals to 2 or 3 must be handled explicitly
       if (n == 2 || n == 3) return n;

       // Keep removing parts of size 3 while n is greater than 4
       ll res = 1;
       // while (n > 4)
       // {
       //     n -= 3;
       //     res = (res * 3) % MOD; // Keep multiplying 3 to res
       // }
        // 4 0
        // 5 1
        // 6 1
        // 7 1
        // 8 2
        if (n >= 5) {
            const int step = ((n - 5) / 3 + 1);
            res = mypow(3, step);
            n -= step * 3;
        }
       return (n * res) % MOD; // The last part multiplied by previous parts
    }
public:
    int maxNiceDivisors(int primeFactors) {
        return maxProd(primeFactors);
    }
};
```

Time complexity: O(log n),
space complexity: O(1).
