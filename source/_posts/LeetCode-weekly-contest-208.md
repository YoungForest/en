---
title: LeetCode weekly contest 208
date: 2020-09-30 08:32:45
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/09/30/LeetCode-weekly-contest-208/
  en: https://youngforest.github.io/en/2020/09/30/LeetCode-weekly-contest-208/
---
It has been three and a half months since I last wrote a weekly contest summary. It is indeed a pity that the habit of writing weekly contest solutions, which I had kept for half a year, was interrupted. But at that time, it was unavoidable. In July I was busy with my short paper; in August came the proposal and midterm; in September the semester officially started; and since early July I had also been doing my summer internship at Amazon. There really were more tasks than before. Back then I felt overwhelmed and did not have enough energy to do everything well. Each weekly contest writeup usually takes half a day, and together with the contest itself, it basically costs a whole day. The energy drain from competing and writing solutions is obvious. Although the writeups stopped for these three months, I still kept participating in contests as usual. After all, I joined the "Cruel Problem Solving" group, and with more people doing weekly contests together, the feedback and enthusiasm from competing every week became stronger.

Over these three months, my ranking in the Cruel group also went up and down and down and down... At best I was ranked 15th, and at worst I had already fallen to 90th. Overall, I feel that I am better at conventional, not-too-hard problems. When I encounter something I have not seen before or have not practiced much, it is very easy for me to fail on the last problem.

Yesterday I confirmed the conversion process with my Amazon manager. After that, it is basically just process. With an ideal offer in hand, the tense string of full-time recruiting finally relaxed. I now feel like spending time on things I want to do: reading a few books I have always wanted to read, seriously doing several contests, and rebuilding the habit of writing solution posts. Of course, the internship and my graduation thesis are still the main tasks right now and need most of my time. But at last I have the energy to be happy.

Over these three months, because the Chinese LeetCode site has richer sponsor gifts, I switched from the US site to the Chinese site. The effect has been pretty good. Besides coins being easier to get, the physical rewards are also quite nice. I was lucky enough to enter the top 50 twice and got a canvas bag and a Xiaomi wireless mouse.

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 392 / 11499 | YoungForest | 	18 | 	1:04:46 |  0:28:38 | 0:40:28 | 1:04:46 | 0:55:59 |

In fact, because I had to handle an unexpected issue with the training plan, I was 25 minutes late. Otherwise, the result would have looked better.

This contest was criticized by everyone as a reading-comprehension contest: the problems were hard to read and the descriptions were confusing. That was indeed the case.

## 1598. Crawler Log Folder

A warm-up problem. We only need to care about going one level down and one level up. A single variable is enough to maintain the answer. During the contest, I used a stack, which was actually overkill.

```cpp
class Solution {
public:
    int minOperations(vector<string>& logs) {
        stack<string> st;
        for (auto& s : logs) {
            if (s == "../") {
                if (!st.empty()) {
                    st.pop();
                }
            } else if (s == "./") {
                continue;
            } else {
                s.pop_back();
                st.push(s);
            }
        }
        return st.size();
    }
};
```

Time complexity: O(N),
Space complexity: O(N).

## 1599. Maximum Profit of Operating a Centennial Wheel

Straightforward. Do not overthink it. Directly simulate each rotation and calculate the maximum profit. One detail to pay special attention to is that after you stop operating, later rotations no longer need to pay the cost.

```cpp
class Solution {
public:
    int minOperationsMaxProfit(vector<int>& customers, int boardingCost, int runningCost) {
        int waitCustomer = 0;
        int getBoard = 0;
        int maxProfit = 0;
        int ans = 0;
        for (int i = 0; i < customers.size() || waitCustomer > 0; ++i) {
            if (i < customers.size()) {
                waitCustomer += customers[i];
            }
            if (waitCustomer >= 4) {
                waitCustomer -= 4;
                getBoard += 4;
            } else {
                getBoard += waitCustomer;
                waitCustomer = 0;
            }
            const int profit = getBoard * boardingCost - runningCost * (i + 1);
            // cout << profit << endl;
            if (profit > maxProfit) {
                maxProfit = profit;
                ans = i + 1;
            }
        }
        if (maxProfit <= 0) return -1;
        else return ans;
    }
};
```

Time complexity: O(customers.size() + sum(customers) / 4),
Space complexity: O(1).

## 1600. Throne Inheritance

Although the description looks very complicated, it is actually just DFS / preorder traversal. The earlier problem description, especially the definition of the `Successor` function, is very confusing. In fact, it is just primogeniture. Looking directly at the example makes it much easier to understand. I only came back to read the third problem after finishing the fourth one, and the statement was indeed too hard to understand.

```cpp
class ThroneInheritance {
    unordered_set<string> dead;
    unordered_map<string, vector<string>> children;
    string root;
    // dfs
public:
    ThroneInheritance(string kingName) {
        root = kingName;
    }
    
    void birth(string parentName, string childName) {
        children[parentName].push_back(childName);
    }
    
    void death(string name) {
        dead.insert(name);
    }
    
    vector<string> getInheritanceOrder() {
        vector<string> ans;
        function<void(const string&)> dfs = [&](const string& r) -> void {
            if (dead.find(r) == dead.end()) {
                ans.push_back(r);
            }
            for (const string& child : children[r]) {
                dfs(child);
            }
        };
        dfs(root);
        return ans;
    }
};

/**
 * Your ThroneInheritance object will be instantiated and called as such:
 * ThroneInheritance* obj = new ThroneInheritance(kingName);
 * obj->birth(parentName,childName);
 * obj->death(name);
 * vector<string> param_3 = obj->getInheritanceOrder();
 */

```

## 1601. Maximum Number of Achievable Transfer Requests

Compared with the second and third problems, this one is much easier to understand. Since `request.length` itself is small, we can directly use brute force: enumerate all subsets of `requests`, check whether each subset is valid, meaning each building has equal in-degree and out-degree, and find the largest one.

```cpp
class Solution {
public:
    int maximumRequests(int n, vector<vector<int>>& requests) {
        // 2 ^ 16 * 16
        const int rl = requests.size();
        int ans = 0;
        auto verify  = [&](const int mask) -> bool {
            vector<int> in(n, 0);
            vector<int> out(n, 0);
            for (int i = 0; i < rl; ++i) {
                if ((mask & (1 << i)) != 0) {
                    ++in[requests[i][1]];
                    ++out[requests[i][0]];
                }
            }
            for (int i = 0; i < n; ++i) {
                if (in[i] != out[i]) return false;
            }
            return true;
        };
        for (int i = (1 << rl) - 1; i >= 0; --i) {
            if (verify(i)) {
                ans = max(ans, __builtin_popcount(i));
            }
        }
        return ans;
    }
};
```

m: `requests.length`,

Time complexity: O(2 ^ m * (m + n)),
Space complexity: O(n).

Of course, there is also a polynomial-time solution: [O((n+m)m)](https://leetcode-cn.com/problems/maximum-number-of-achievable-transfer-requests/solution/zui-xiao-fei-yong-zui-da-liu-onm2-c-by-heltion/).

It uses min-cost max-flow, which is already outside my knowledge scope. Interested classmates can learn about it.

## Afterword

After three years of preparation, my full-time recruiting is basically over. I have decided to stay at Amazon. In three more months I will become a corporate worker. This is a pretty ideal offer.

The most ideal dream company was of course Google. Unfortunately, after I finally passed the first internship interview, Google's internship program this year was canceled because of the pandemic. Full-time recruiting was also much later than in previous years, and only recently were there some pushed openings. HC itself is also very scarce, so opportunities are rare.

For other foreign companies I liked: Hulu passed my first interview, and I am waiting for the second one; I asked zenian for a Microsoft referral, but never received any written test or interview notice, probably because I applied to STCA in Beijing, where HC is limited and basically occupied by interns. I had also received a questionnaire asking whether I would accept being transferred to Suzhou. Difficult. Intel is basically stable, but the compensation there is much worse than Amazon, so I am no longer considering it. I also applied to the smaller company HotStar and got a verbal offer, but it was originally something I used to get a large package and compete with big companies, so I basically will not go there.

For domestic internet companies: I got offers from Ant Financial and Yuanfudao, both with pretty good ratings. Ant was even A+. At Ant, because I was lucky and got full marks on the written test, my senior and the lead really wanted me to join. During early recruiting in June, I applied to vivo just to test the waters. In the end, I was rejected for being overqualified. I clearly got all A's in the written test and chatted happily in the interview. If you do not want candidates who are too strong, just say it directly; do not waste everyone's time. In August I also applied to JD. The interview was very shallow, and the offer compensation was extremely insincere, basically a discouragement offer, so I rejected it directly. Baidu's early recruiting interview finished, and they said they would give me an offer, but I still have not heard anything. Tencent's internship interview hurt me, and there were no good positions in Beijing anyway, so I did not apply for full-time recruiting. Unexpectedly, I was still picked up for one interview, with no follow-up. ByteDance kept rejecting me, repeatedly fishing me up and repeatedly rejecting me. We really are incompatible.
