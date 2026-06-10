---
title: LeetCode biweekly contest 20
date: 2020-02-23 11:34:19
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 233 / 4347 |	YoungForest | 18 | 	0:41:39 | 0:03:32 |  0:13:58  1 |  0:24:13 |  0:31:39  1 |

The problems in this contest were relatively simple. It was another contest of speed and bug-free coding.
After missing contests for half a year, I cannot even get into the top 200 in a smaller biweekly contest anymore. Scary.

## 1356. Sort Integers by The Number of 1 Bits

Use the C++ standard library sorting function and a lambda expression.

Time complexity: O(n log n),
space complexity: O(1).

```cpp
class Solution {
public:
    vector<int> sortByBits(vector<int>& arr) {
        sort(arr.begin(), arr.end(), [](const auto& a, const auto& b) -> bool {
            auto aa = __builtin_popcount(a);
            auto bb = __builtin_popcount(b);
            if (aa == bb) {
                return a < b;
            } else {
                return aa < bb;
            }
        });
        return arr;
    }
};
```

## 1357. Apply Discount Every n Orders

This tests familiarity with data structures. Just implement it directly according to the statement.
Because I did not read carefully, I misunderstood the definition of `discount` - it means how much to discount, not how much remains - which caused one wrong submission.

Time complexity: O(product.length),
space complexity: O(getBill.length).

```cpp
class Cashier {
    unordered_map<int, int> m;
    int d;
    int nn;
    int index = 0;
public:
    Cashier(int n, int discount, vector<int>& products, vector<int>& prices) {
        d = discount;
        nn = n;
        for (int i = 0; i < products.size(); ++i) {
            m[products[i]] = prices[i];
        }
        index = 0;
    }
    
    double getBill(vector<int> product, vector<int> amount) {
        ++index;
        bool discount = false;
        if (index == nn) {
            index = 0;
            discount = true;
        }
        double ans = 0;
        for (int i = 0; i < product.size(); ++i) {
            ans += amount[i] * m[product[i]];
        }
        if (discount)
            ans *= 1 - d / 100.0;
        return ans;
    }
};

/**
 * Your Cashier object will be instantiated and called as such:
 * Cashier* obj = new Cashier(n, discount, products, prices);
 * double param_1 = obj->getBill(product,amount);
 */
```

## 1358. Number of Substrings Containing All Three Characters

Sliding window. While moving the right side, keep the window minimal. At that point, every substring starting to the left of the window's left boundary is valid.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
public:
    int numberOfSubstrings(string s) {
        int right = 0, left = 0;
        vector<int> count(3, 0);
        int ans = 0;
        while (right < s.size()) {
            ++count[s[right] - 'a'];
            while (left <= right && count[s[left] - 'a'] > 1) {
                --count[s[left] - 'a'];
                ++left;
            }
            if (count[0] >= 1 && count[1] >= 1 && count[2] >= 1) {
                ans += (left + 1) * 1;
            }
            ++right;
        }
        return ans;
    }
};
```

## 1359. Count All Valid Pickup and Delivery Options

A typical recursive problem.
For n items, choose any one of them as the first pickup, then solve the ordering problem for the remaining n - 1 items. The delivery can be inserted at any position (`2 * (n - 1) + 1` possibilities).

A modulo issue caused one Wrong Answer. This is not the first time. I need to be more careful next time.

Time complexity: O(N),
space complexity: O(N) -> O(1), if using an iterative solution or tail recursion. I will not show it here.

```cpp
class Solution {
    using ll = long long;
    ll mod = 1e9 + 7;
public:
    ll countOrders(int n) {
        if (n == 1) return 1;
        return (n * (countOrders(n - 1) * (2 * (n - 1) + 1)) % mod) % mod;
    }
};
```
