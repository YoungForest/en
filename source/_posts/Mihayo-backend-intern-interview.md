---
title: miHoYo Server Development Summer Internship
date: 2020-03-20 16:29:48
tags:
- miHoYo
- Intern
categories:
- Interview
---

# First Round

time: 2020-03-20 16:29:48

Last Friday I took the written test on Nowcoder. The questions were not hard and were divided into computer science fundamentals, algorithms, and system design.
For computer science fundamentals, thanks to serious undergraduate study, there was no problem. The algorithm part was also around LeetCode Medium difficulty and I got AC quickly.
System design, however, stumped me. I am not good at it and had not prepared for it. The task was to design a MOBA game's matchmaking mechanism, including solo and team queue. I had never thought about it before and just wrote something randomly.
Yesterday I received a call saying I had passed the written test, and they scheduled a phone interview for 2:30 this afternoon.

I had a lovely expert roommate in undergraduate who eventually went to miHoYo. Although I am not particularly into games, with the attitude of doing more interviews and summarizing more, I also signed up for its spring referral recruiting.

The interview was expected to take 30 minutes, but actually took 40 minutes.

Self-introduction + project experience + computer science fundamentals.

Computer science fundamentals were further divided into:
- C++
- Operating System
- Database
- Computer Network
- Design Patterns

Things I did not know:
- TCP three-way termination, and the role of the final `TIME_WAIT`
- why a C++ base class destructor must be virtual
- MySQL
    - transactions and ACID
    - difference between Block and Tag
    - what BiLog is
    - difference between `timestamp` and `datetime`
- name common design patterns. I mentioned a few, but the interviewer did not seem satisfied

There was no live coding section, which was a little disappointing.

# Second Round

time: 2020-04-03 12:54:20

Previous [first-round post](https://www.nowcoder.com/discuss/387235).

Two full weeks passed between the first and second rounds. In between, HR called and hoped I could go to Shanghai for an onsite second round. I could only honestly say that the school currently did not allow cross-province movement. Your company is really bold. In the end, it still went according to plan as a video interview.

The whole interview lasted 50 minutes. It was not very difficult, but because it was a game company, many questions and projects were things I had not encountered or thought about before.

## Algorithm

Merge two sorted linked lists.

## Project

- Projects under Linux. I told him most of mine were all on Linux.
- A game I made in senior year (software engineering assignment), [GitHub](https://github.com/xxr5566833/Game).
- memory leak diagnosis
- protobuf

## Computer Science Fundamentals

- How to diagnose network problems?
- Consistent hashing, adding buckets and removing buckets.
- Singleton pattern, templated singleton, and singleton under multithreading. This was implemented locally in the IDE while sharing the screen.

# Third Round

time: 2020-04-08 12:58:36

[First round](https://www.nowcoder.com/discuss/387235)
[Second round](https://www.nowcoder.com/discuss/399642)

## Language Fundamentals

Implement the constructor and destructor of smart pointer `shared_ptr`.
Question: why must `count` use a pointer?

```cpp
typename<T>
class shared_ptr {
    T* data = nullptr;
    uint32_t* count = 0;
    shared_ptr(const shared_ptr& a) {
        data = a->data;
        count = a->count;
        ++(*count);
    }
    shared_ptr(T* t) {
        data = t;
        if (t) {
            count = new uint32_t();
            count = 0;
        }
    }
    shared_ptr operator = (const shared_ptr& a) {
        if (a == this) ;
        else {
            if (this != nullptr) {
                --(*count);
                if (*count == 0) {
                    delete data;
                    delete count;
                }
            }
            data = a->data;
            count = a->count;
            ++(*count);
        }
    }
    ~shared_ptr() {
        if (data) {
            if (*count > 0) {
                --(*count);
            } else {
                delete data;
                delete count;
            }
        }
    }
};

typename<T>
shared_ptr<T> make_shared() {
    return ret(new T());
}
```

## Algorithm

> Coins with denominations 1, 7, and 10.
Given an `n`, use the minimum number of coins to make this value.

At first I wanted to use greedy, but the interviewer quickly gave a counterexample.
> 15

Then I gave a DP solution with O(N). The interviewer then hinted: if `N` is very large, what optimization idea is there? This led to first taking modulo the least common multiple, then doing DP on the remainder, giving an O(1) solution.

```cpp
dp(n) = min(
    dp(n - 1) + 1,
    dp(n - 7) + 1,
    dp(n - 10) + 1,
); if n >= 10;
1 * 7 * 10 = 70
O (7 + 1) = O(1)
```

## Data Structure Design

Design a million-level leaderboard that supports insertion, lookup score by `uid`, lookup rank by `uid`, and lookup `uid` by rank.
Follow-up: when scores are the same, sort by time of entering the leaderboard.
```cpp
order statisc tree

int getSize(TreeNode* node) {}


set
multiset

order_statisc_tree<pair<分数, 时间>，uid>：按名次查uid log N
hashmap<uid, pair<分数,时间>>: 按uid查分数 O(1)
按uid查排名 O(log N)
insert: O(1 + log N)
```

## Computer Science Fundamentals

Familiar with Linux?
Troubleshoot an online process whose CPU usage is 100%.

## Others

What is special about game companies?
Have you played our company's games? (No.) Then what games do you usually play?

At the end, the interviewer asked whether I could start the internship early before graduation. I said no, because I could not go to Shanghai.

Question: your company's internal grouping for server development. Shared backend infrastructure across different products.
