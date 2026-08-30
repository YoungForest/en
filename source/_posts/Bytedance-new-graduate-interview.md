---
title: ByteDance 2020 Fall Recruiting Interview
date: 2020-06-23 18:56:43
description: "Notes from a ByteDance backend interview on algorithms, operating systems, networking, databases, Python, and lessons from rushed preparation."
tags:
- ByteDance
- New Graduate
categories:
- Interview
translations:
  zh-CN: https://youngforest.github.io/2020/06/23/Bytedance-new-graduate-interview/
  en: https://youngforest.github.io/en/2020/06/23/Bytedance-new-graduate-interview/
---
I applied for ByteDance's early batch, for a backend development role in the Technology Middle Platform.
I did not review computer science fundamentals well enough, so my answers were poor.
Wishing for an offer.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/Bytedance-new-graduate-interview/en-hero.webp" alt="Forest hurries toward an interview chair with one polished algorithm tool while the computer-fundamentals toolbox behind him still has several empty compartments" width="1536" height="864" decoding="async">
</figure>

<!-- more -->

## First Round

Self-introduction.

### Algorithm Problem

Give the brute-force solution first, then optimize it.

> Problem: An array represents the stock price for each day. Each day you may buy once, sell once, or do nothing. You must buy before you sell. Given that only one transaction is allowed, meaning buying once and selling once, find the maximum profit.
Input: [2,1,4,1,5,6,1]
Output: 5 

```cpp
#include <iostream>
#include <vector>
using namespace std;

int solution(const vector<int>& prices) {
    // time: N ^ 2
    // space: 1
    int ans = 0;
    for (int buy = 0; buy < prices.size(); ++buy) {
        for (int sell = buy + 1; sell < prices.size(); ++sell) {
            int profit = prices[sell] - prices[buy];
            ans = max(ans, profit);
        }
    }
    return ans;
}

int solution2(const vector<int>& prices) {
    // time: O(N)
    // space: 1
    int ans = 0;
    if (prices.empty()) return 0;
    int minPrices = prices[0];
    for (int sell = 1; sell < prices.size(); ++sell) {
        int profit = prices[sell] - minPrices;
        ans = max(ans, profit);
        minPrices = min(minPrices, prices[sell]);
    }
    return ans;
}

int main() {
    //int a;
    //cin >> a;
    //cout << a << endl;
    vector<int> prices = {2,1,4,1,5,6,1};
    cout << solution2(prices) << endl;
}
```

### Computer Science Fundamentals

#### Operating System

Types of IPC
semaphore

process and thread
how many states each has, and the state transition diagram.

virtual memory

[Some common Linux commands](https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/ldd.html), which I had just reviewed today.
For example, checking ports, memory, and process status.
[The current working directory of a process](https://unix.stackexchange.com/questions/94357/find-out-current-working-directory-of-a-running-process): `lsof -p <PID> | grep cwd`

#### Computer Networking

The process and necessity of the three-way handshake and four-way termination. I answered terribly.

#### Database

Several normal forms
Do I understand them deeply? No.

#### Language

python
- GIL
- reference or value

This team is most likely writing python.


<figure class="editorial-illustration">
  <img src="/en/images/ai/Bytedance-new-graduate-interview/en-review-plan.webp" alt="Forest arranges scattered systems, networking, database, and language cards into a three-stage study bridge leading toward the next interview" width="1536" height="864" loading="lazy" decoding="async">
</figure>

### Summary

The interview was scheduled yesterday for today. That was a mistake. It was too rushed. The early batch for fall recruiting is important, and the computer science fundamentals are exactly the questions in those question banks. Spending three days memorizing them is still necessary. Otherwise, relying only on what I learned during undergraduate studies will not lead to good answers and will hurt a lot.

Many classmates asked where the question bank is. I also found it online: [https://github.com/CyC2018/CS-Notes](https://github.com/CyC2018/CS-Notes)
It feels quite complete. I am already memorizing it.
