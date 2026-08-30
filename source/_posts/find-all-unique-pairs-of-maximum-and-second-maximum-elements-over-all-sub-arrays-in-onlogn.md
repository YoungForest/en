---
title: Find All Unique Pairs of Maximum and Second Maximum Elements in Subarrays
date: 2019-12-14 22:07:31
tags:
- Algorithm
categories:
- Programming
description: "Two linear approaches to maximum and second-maximum pairs in subarrays: a monotonic stack and expansion from the highest differing XOR bit."
translations:
  zh-CN: https://youngforest.github.io/2019/12/14/find-all-unique-pairs-of-maximum-and-second-maximum-elements-over-all-sub-arrays-in-onlogn/
  en: https://youngforest.github.io/en/2019/12/14/find-all-unique-pairs-of-maximum-and-second-maximum-elements-over-all-sub-arrays-in-onlogn/
---
The root of the problem is that a classmate asked about a lucky number problem: [Codeforces 280B](https://codeforces.com/problemset/problem/280/B). [Codeforces 281D](https://codeforces.com/problemset/problem/281/D) is the same problem.

[Find all unique pairs of maximum and second maximum elements over all sub-arrays in O(NlogN)](https://www.geeksforgeeks.org/find-all-unique-pairs-of-maximum-and-second-maximum-elements-over-all-sub-arrays-in-onlogn/)

A lucky number is defined as the XOR value of the maximum and second maximum of a subarray. The task is to find the largest lucky number among all lucky numbers.

The brute force solution enumerates all subarrays, with time complexity O(N ^ 2).
Is there a better method?
That is the problem to discuss today.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/find-all-unique-pairs-of-maximum-and-second-maximum-elements-over-all-sub-arrays-in-onlogn/en-hero.webp" alt="A mountain ridge is buried under overlapping survey frames while a descending rack selects only the nearest taller peak on each side" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## General Solution: Fast Algorithm for Finding Maximum and Second-Maximum Pairs

There is actually a fast algorithm for finding the maximum and second maximum in subarrays:
[Find all unique pairs of maximum and second maximum elements over all sub-arrays in O(NlogN)](https://www.geeksforgeeks.org/find-all-unique-pairs-of-maximum-and-second-maximum-elements-over-all-sub-arrays-in-onlogn/)

It is based on this observation: for each number in the array, if it wants to become the second maximum, it can only pair with the first number greater than it in front of it, or the first number greater than it after it.
We can maintain a monotonically decreasing stack. When adding a new number, to maintain monotonicity we pop all numbers smaller than it. At this time, the new number is the first number after the popped numbers that is greater than them; the largest number on the stack top is the first number before the new number that is greater than it.

Time complexity: O(N),
space complexity: O(N).

```cpp
#include <iostream>
#include <stack>
#include <vector>

using namespace std;
using ll = long long;

int solve() {
    ll N;
    cin >> N;
    vector<ll> nums(N);
    for (ll i = 0; i < N; ++i) {
        cin >> nums[i];
    }
    stack<ll> st;
    st.push(nums[0]);
    ll ans = 0;
    for (ll i = 1; i < N; ++i) {
        while (!st.empty() && nums[i] > st.top()) {
            ans = max(ans, nums[i] ^ st.top());
            st.pop();
        }
        if (!st.empty()) {
            ans = max(ans, nums[i] ^ st.top());
        }
        st.push(nums[i]);
    }
    return ans;
}

int main() {
    ll ans = solve();
    cout << ans << endl;
    return 0;
}
```

<figure class="editorial-illustration">
  <img src="/en/images/ai/find-all-unique-pairs-of-maximum-and-second-maximum-elements-over-all-sub-arrays-in-onlogn/en-highest-bit.webp" alt="Tall amber beacons along a layered rail line send search carts in both directions until the next beacon blocks the route" width="1536" height="864" loading="lazy" decoding="async">
</figure>

## The Solution I Thought of Independently

My solution uses the property of XOR. If we replaced XOR with another operation, it would not be general.

First traverse once to find the highest bit.
Then traverse again to find the numbers whose highest bit is 1. Let us call them the highest numbers.
Starting from these highest numbers, expand to both sides until encountering another highest number. During this process, find the second maximum and update the maximum lucky number. It can be proven that each number is visited at most twice.
Therefore, the total time complexity is O(N), and the space complexity is O(N).

```cpp
#include <iostream>
#include <vector>

using namespace std;
using ll = long long;

int solve() {
    int N;
    cin >> N;
    vector<ll> nums(N);
    for (ll i = 0; i < N; ++i) {
        cin >> nums[i];
    }
    const int MAX_BIT_BEGIN = 40;
    vector<vector<ll>> flag(MAX_BIT_BEGIN + 1, vector<ll>(2, 0));
    for (ll i : nums) {
        for (ll j = 0; j <= MAX_BIT_BEGIN; ++j) {
            ++flag[j][((i >> j) & 1)];
        }
    }
    ll max_bit = MAX_BIT_BEGIN;
    for (; max_bit >= 0; --max_bit) {
        if (flag[max_bit][0] > 0 && flag[max_bit][1] > 0) {
            break;
        }
    }
    if (max_bit == 0) {
        return 1;
    } else if (max_bit < 0) {
        return 0;
    } else {
        vector<ll> max_bit_is_1_indexs;
        for (ll i = 0; i < N; ++i) {
            if (((nums[i] >> max_bit) & 1) == 1) {
                max_bit_is_1_indexs.push_back(i);
            }
        }
        ll ans = 0;
        for (ll index : max_bit_is_1_indexs) {
            ll second_max = 0;
            for (ll i = index - 1; i >= 0 && ((nums[i] >> max_bit) & 1) == 0;
                 --i) {
                second_max = max(second_max, nums[i]);
                ans = max(ans, nums[index] ^ second_max);
            }
            second_max = 0;
            for (ll i = index + 1; i < N && ((nums[i] >> max_bit) & 1) == 0;
                 ++i) {
                second_max = max(second_max, nums[i]);
                ans = max(ans, nums[index] ^ second_max);
            }
        }
        return ans;
    }
}

int main() {
    ll ans = solve();
    cout << ans << endl;
    return 0;
}
```
