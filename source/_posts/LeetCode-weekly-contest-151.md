---
title: LeetCode weekly contest 151
date: 2019-08-31 20:25:38
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/08/31/LeetCode-weekly-contest-151/
  en: https://youngforest.github.io/en/2019/08/31/LeetCode-weekly-contest-151/
---
This week I went to ByteDance to attend the summer camp, and still had classes on Sunday, so I skipped the weekly contest. Then how could I participate in Kick Start? After all, this month's Round E was the so-called golden round, important for getting interview slots, so I chose to skip the summer camp.

After the summer camp ended, I made up the problems as promised. I have to say, LeetCode is still much less difficult than Kick Start. It feels like Kick Start's warm-up problems are Medium, and the last two problems are Hard.

## 1169. Invalid Transactions

This tests string processing. Since `transactions.lenght < 1000`, even a brute-force method is acceptable. In fact, the optimized method I implemented does not become better in the worst case.

Time complexity: O(N^2).
Space complexity: O(N).

```cpp
class Solution {
public:
    vector<string> invalidTransactions(vector<string>& transactions) {
        unordered_map<string, vector<tuple<int, int, string>>> name_trasactions;
        for (const auto& s : transactions) {
            stringstream ss(s);
            string tmp;
            vector<string> v;
            while (getline(ss, tmp, ',')) {
                v.push_back(tmp);
            }
            name_trasactions[v[0]].push_back({stoi(v[1]), stoi(v[2]), v[3]});
        }
        unordered_set<string> ans;
        for (auto& p : name_trasactions) {
            sort(p.second.begin(), p.second.end());
            int l = 0, r = 0;
            for (; r < p.second.size(); ++r) {
                if (get<1>(p.second[r]) > 1000) {
                    ans.insert(p.first + "," + to_string(get<0>(p.second[r])) + "," + to_string(get<1>(p.second[r]))+ "," + get<2>(p.second[r]));
                }
                while (get<0>(p.second[r]) - get<0>(p.second[l]) > 60) {
                    ++l;
                }
                for (int i = l; i < r; ++i) {
                    if (get<2>(p.second[r]) != get<2>(p.second[i])) {
                        ans.insert(p.first + "," + to_string(get<0>(p.second[r])) + "," + to_string(get<1>(p.second[r]))+ "," + get<2>(p.second[r]));
                        ans.insert(p.first + "," + to_string(get<0>(p.second[i])) + "," + to_string(get<1>(p.second[i]))+ "," + get<2>(p.second[i]));
                    }
                }
            }
        }
        return vector<string>(ans.begin(), ans.end());
    }
};
```

## 1170. Compare Strings by Frequency of the Smallest Character

A simple binary search.

Time complexity: O(queries.length * queries[i].length * log words.length),
space complexity: O(queries.length + words.length).

```cpp
class Solution {
    int f(const string& s) {
        char mi = 'z' + 1;
        int count = 0;
        for (char c : s) {
            if (c == mi) {
                ++count;
            } else if (c < mi) {
                mi = c;
                count = 1;
            }
        }
        return count;
    }
public:
    vector<int> numSmallerByFrequency(vector<string>& queries, vector<string>& words) {
        vector<int> a;
        for (const auto& w : words) {
            a.push_back(f(w));
        }
        sort(a.begin(), a.end());
        vector<int> ans;
        for (const auto& q : queries) {
            int c = f(q);
            auto it = upper_bound(a.begin(), a.end(), c);
            ans.push_back(distance(it, a.end()));
        }
        return ans;
    }
};
```

## 1171. Remove Zero Sum Consecutive Nodes from Linked List

This tests linked list operations. To quickly find the corresponding elements and delete elements, we need to use a hash table to store the mapping from prefix sum to node.

Time complexity: O(N),
space complexity: O(N).

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* removeZeroSumSublists(ListNode* head) {
        ListNode dummy(0);
        dummy.next = head;
        ListNode* current = head;
        unordered_map<int, ListNode*> m;
        int sum = 0;
        m[0] = &dummy;
        while (current) {
            sum += current->val;
            if (m.find(sum) != m.end()) {
                auto delete_head = m[sum]->next;
                m[sum]->next = current->next;
                current->next = nullptr;
                int delete_sum = sum;
                while (delete_head) {
                    auto tmp = delete_head;
                    delete_sum += tmp->val;
                    if (delete_sum != sum)
                        m.erase(delete_sum);
                    delete_head = delete_head->next;
                    delete tmp;
                }
                current = m[sum]->next;
            } else {
                m[sum] = current;
                current = current->next;
            }
            
        }
        return dummy.next;
    }
};
```

## 1172. Dinner Plate Stacks

Use multiple stacks to simulate one stack.
The implementation is not complicated, but many boundary conditions need to be considered: how each function changes the state of the stacks.

Time complexity: O(1).
Space complexity: O(N).

```cpp
class DinnerPlates {
    vector<stack<int>> stacks;
    set<int> condidates;
    int capacity;
public:
    DinnerPlates(int capacity_) : capacity(capacity_) {
    }
    
    void push(int val) {
        if (condidates.empty()) {
            condidates.insert(stacks.size());
            stacks.push_back(stack<int>());
        }
        int target = *condidates.begin();
        stacks[target].push(val);
        if (stacks[target].size() == capacity) {
            condidates.erase(target);
        }
    }
    
    int pop() {
        if (stacks.empty())
            return -1;
        int ret = stacks.back().top();
        stacks.back().pop();
        while (!stacks.empty() && stacks.back().empty()) {
            stacks.pop_back();
            condidates.erase(stacks.size());
        }
        if (!stacks.empty() && stacks.back().size() < capacity) {
            condidates.insert(stacks.size() - 1);
        }
        return ret;
    }
    
    int popAtStack(int index) {
        if (index >= stacks.size())
            return -1;
        if (stacks[index].empty())
            return -1;
        if (index == stacks.size() - 1)
            return pop();
        int ret = stacks[index].top();
        stacks[index].pop();
        condidates.insert(index);
        return ret;
    }
};

/**
 * Your DinnerPlates object will be instantiated and called as such:
 * DinnerPlates* obj = new DinnerPlates(capacity);
 * obj->push(val);
 * int param_2 = obj->pop();
 * int param_3 = obj->popAtStack(index);
 */
```
