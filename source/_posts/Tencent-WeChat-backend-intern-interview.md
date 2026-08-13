---
title: Tencent WeChat Business Group Summer Intern Interview
description: "A detailed review of a Tencent WeChat backend internship interview, including process and technical questions."
date: 2020-03-11 10:28:43
tags:
- Tencent
- Intern
categories:
- Interview
translations:
  zh-CN: https://youngforest.github.io/2020/03/11/Tencent-WeChat-backend-intern-interview/
  en: https://youngforest.github.io/en/2020/03/11/Tencent-WeChat-backend-intern-interview/
---
## First Round

time: 2020-03-11 10:28:43

Last week HR contacted me to ask about my preferred work city, but did not schedule a specific interview time.

Last night at 8:30, I suddenly received a call from Shenzhen, Guangdong, asking whether it was convenient, and the interview started directly. Surprise, excitement, all at once. The official interview website was also not very good and had quite a few issues. For example, during live coding, the content on the other side's website could not refresh in time.

## Computer Science Fundamentals

### Distributed Systems, Deep Learning

BN layer, dropout. How are they computed?
BN: mean, variance.

Difference between single-machine training and multi-machine training.

During multi-machine training, how to reduce the loss obtained by each single machine.

Data parallel training and model parallel training.

Training with tens of billions of features. What does "tens of billions" refer to?

### Language Fundamentals: C++

HashTable implementation

Differences among `shared_ptr`, `unique_ptr`, and `weak_ptr`

move semantics

## Algorithm Problems

Depth of a binary tree.

Given a 2D array where rows and columns are both non-decreasing, find a target value in `O(m + n)`. [LeetCode 240](https://leetcode.com/problems/search-a-2d-matrix-ii/description/)

## Reverse Questions

Question: what does your group work on, and why ask so many deep learning and distributed systems questions?

Answer: large-scale distributed training framework. Tech stack: C++, Python.

## Second Round

time: 2020-03-17 21:59:18

After waiting for a week, I finally got my first follow-up interview. The interviewer was in the same group as the last interviewer. It should be because the tech stack matched fairly well, so the distributed training framework group picked up my resume.

First, he asked many questions related to projects and internship experience. For example: the most challenging task, what difficulties I encountered, and how I solved them. I answered using the legendary STAR method, but the interviewer did not seem very satisfied. I am usually worst at talking about these things, though after doing more interviews I have learned a little. Live coding is still more straightforward and direct, represented by Google, which comes up and just starts coding. It is like standardized interview questions and is easier to prepare for.

Fundamentals:
- difference between `shared_ptr` and `unique_ptr`
- necessary conditions for deadlock and how to solve deadlock
- difference between TCP and UDP
- how TCP guarantees reliability

Distributed systems:
- PyTorch architecture, classes, C++ interface wrapping, calls between languages
- how to update parameters during multi-machine multi-GPU training

Algorithm problem:

Given a very long sorted array and another unsorted array, insert the unsorted array into the sorted array while ensuring the result is still sorted.

Given sorted vector sorted_a and unsorted vector b;   size of a is about 1G
vector<int> sorted_a;
vector<int> b;
insert b to sorted_a as fast as possible, result sorted_a should be sorted, too.

```cpp
void insertSortedVector(vector<int>& sorted_a, vector<int>& b) {
    if (sorted_a.emtpy() || b.empty()) return;
        sort(b.begin(), b.end());
        int a_tail = sorted_a.size() - 1;
        int b_tail = b.size() - 1;
        sorted_a.resize(sorted_a.size() + b.size()); // 可能有 sorted_a.size()
        int after_tail = sorted_a.size() - 1;
        while (a_tail >= 0 && b_tail >= 0) {
            if (sorted_a[a_tail] > b[b_tail]) {
            sorted_a[after_tail] = sorted_a[a_tail];
            --a_tail;
        } else {
            sorted_a[after_tail] = b[b_tail];
            --b_tail;
        }
        --after_tail;
    }
    while (b_tail >= 0) {
        sorted_a[after_tail] = b[b_tail];
        --b_tail;
        --after_tail
    }
}
```

Time: O(a.size() + b.size() * log b.size())
Space: O(1)

After I finished writing the code, he kept asking me to optimize it, with a hint that it was not an optimization at the time-complexity level. I found several places, and the code above is my final version. Only then was the interviewer satisfied.

## Third Round

time: 2020-03-19 09:06:13

I had just finished the second round yesterday afternoon, and this afternoon I received an HR call scheduling an interview for 8 PM. It was through Nowcoder's platform: video interview + live coding. In total it lasted 1 hour and 20 minutes.

After a brief self-introduction, we started live coding. I like that. There were four problems in total, none of them hard, around LeetCode Easy/Medium level. The only uncomfortable part was that the first two problems had to be written in C.

After 50 minutes of live coding, we moved to fundamentals Q&A, including operating systems, computer networks, databases, data structures, and so on. The interviewer probably had a list of questions, and asked very quickly and asked a lot. After I answered, he wrote something down. I had not prepared computer science fundamentals for interviews, relying entirely on what remained from serious undergraduate study. Most of it was enough. The only memorable parts were the ones I could not answer:
- difference and implementation of TCP congestion control and flow control
- difference between clustered index and ordinary index in databases
- differences among asynchronous IO, blocking IO, and IO multiplexing

Then he asked about previous internship and project experience. I wrote five items and he asked about three of them, in quite a lot of detail.

After asking everything, the interviewer ended the interview very decisively. It took me a long time to recover from the high-intensity interview state.

Two out of three interviews were held at night, so you can see the overtime state. Only the third-round interviewer turned on video. Seeing the expert's hairline, I felt that my own ability was really too weak.

## Fourth Round

time: 2020-03-19 23:53:31

I had just finished the third round last night, and this morning I received the fourth-round call, scheduling the interview for the evening.

It started directly with three algorithm problems. I like that.

- find the minimum element in an increasing cyclic integer array
- find the 3rd largest node in a binary search tree
- print a spiral matrix with side length `n`

Then he asked about projects and internship experience: two Kuaishou projects and the HAWQ bug-fix experience.

I asked the interviewer why the status on join.qq.com showed only one interview.
Answer: the same candidate can be picked by different groups and interviewed. No wonder rounds 1 and 2, and rounds 3 and 4, differed in interview style, questions, and platform.

Rounds 1 and 2 were clearly from the distributed framework group. Rounds 3 and 4 probably were not, but I forgot to ask.

I also participated in a Microsoft SWE intern interview in the afternoon. At the time I already felt I was doomed, and sure enough, I received a Thank You Letter that evening. A sad song for myself. Keep going for fall recruiting, try again!

## Fifth Round

time: 2020-03-20 17:40:42

After finishing the miHoYo interview today, I was doing Kuaishou's assessment when I suddenly received a phone interview invitation for 5 PM, half an hour later.

To be honest, by the fifth round I was already exhausted and wanted to complain wildly. But the fifth round was very simple and lasted only 15 minutes.

Self-introduction + projects + computer science fundamentals.

For fundamentals, he asked the questions I had failed to answer in rounds three and four. It was probably intentional. Fortunately, I summarize and review the knowledge I failed to answer after every interview.

What do you think are your strengths and weaknesses?
- Strengths: solid computer science fundamentals, no problem with algorithms
- Weakness: not suitable for research; insufficient innovation and creativity

Reasons for switching companies in previous internships.
Available internship time and format?

Next, wait for the HR interview.

He was from the WeChat Search Service group. Rounds three and four were also from that group. The first two rounds were from the distributed training framework group, and both the interview format and content were different. He said I probably did not pass the distributed training framework group, so my resume was released to their group. He could only see the evaluations from rounds three and four; cross-group information is not transparent. I really do lack fundamentals and experience in distributed training.
I can also understand why five interviews were needed.

## Seventh Round

time: 2020-03-31 19:22:14

Last Wednesday, I scheduled an HR interview. We chatted casually for quite a while, and it was very different from technical interviews. Because I said I wanted to intern in Beijing, they scheduled another technical interview with a Beijing colleague this Monday afternoon. On the Beijing side, there should be only one technical interview plus an HR interview.

The video interview used Nowcoder's platform and was divided into projects, algorithms, data structures, and computer science fundamentals.

## Algorithm Problem
> Shopping
On the weekend, Xiao Q and his friends came to a big city to go shopping. There are many tall buildings on a pedestrian street, with `n` buildings arranged in a row.
Xiao Q walked from the first building to the last building. Xiao Q had never seen so many buildings before, so he wanted to know how many buildings he could see from the position of each building. When a building in front is taller than or equal to a building behind it, the building behind will be blocked.
Input description
The first input line contains a number `n`, representing the number of buildings. The next line contains `n` numbers `wi(1<=i<=n)`, representing the height of each building.
1<=n<=100000;
1<=wi<=100000;
Output description
Output one line containing `n` numbers separated by spaces, where `vi` represents the number of buildings Xiao Q can see at the `i`-th building.
Example 1
Input
6
5 3 8 3 2 5
Output
3 3 5 4 4 4
Explanation
When Xiao Q is at position 3, he can see buildings at positions 2 and 1 in front, and positions 4 and 6 behind. Including the 3rd building, he can see 5 buildings in total. When Xiao Q is at position 4, he can see position 3 in front, and positions 5 and 6 behind. Including the 4th building, he can see 4 buildings in total.

LeetCode Medium difficulty. I killed it instantly: use a monotonically decreasing stack twice, once forward and once backward. One thing to note is that the visible buildings include the current building, so the current building is counted twice, and we need to subtract 1 at the end.

```cpp
#include <vector>
#include <stack>
#include <iostream>

using namespace  std;

int main() {
    int n;
    cin >> n;
    vector<int> height(n);
    for (int i = 0; i < n; ++i) {
        cin >> height[i];
    }
    vector<int> ans(n, 0);
    stack<int> s;
    auto process = [&](int i) -> void {
        while (!s.empty() && height[s.top()] <= height[i])  {
            int t = s.top();
            s.pop();
            ++ans[i];
        }
        s.push(i);
        ans[i] += s.size();
    };
    for (int i = 0; i < n; ++i) {
        process(i);
    }
    while (!s.empty()) {
        s.pop();
    }
    
    for (int i = n - 1; i >= 0; --i) {
        process(i);
    }
    for (int i = 0; i < n; ++i) {
        -- ans[i]; // delete repeated self(count twice)
        cout << ans[i] << " ";
    }
    cout << endl;
    
    return 0;
}
```

## Data Structure

Design a HashMap that supports serialization and deserialization. I had not encountered a similar problem before, though I knew a little about serialization. So I designed a linear probing HashMap, because in that way all data can be stored in one array, which makes serialization convenient.

For ease of implementation, I did not consider generalization or resizing, although I communicated this with the interviewer in advance. The interviewer still criticized linear probing for poor space utilization, saying there would be problems when a single bucket had too many elements. I did not agree with this, and we discussed it for quite a while. In the end, he still criticized me, saying it was not as good as others' implementations and that I did not consider resizing during `insert`. But I had already communicated with him in advance that resizing and generalization were excluded to simplify the problem. I could not help feeling that the interviewer was arguing just to argue, and I was not convinced. I asked how others implemented it and what the mainstream approach was. He only said there was no standard answer.

```cpp
struct HashMap {
    const int capity = 1 << 7;
    array<int, capity> data;
    const int NOT_EXIST = -1;
    HashMap() {
        memset(data.data(), capity * sizeof(int), -1);
    }
    void searilize(const string& file_name) {
        // 把data内容写到文件中
        std::ofstream fout (file_name, std::fstream::binary);
        auto& d = static_cast<array<char, capity*sizeof(int)>>(data)
        std::copy(d.begin(), d.end(), std::istreambuf_iterator<char>(fout));
    }
    void load(const string& file_name) {
        // 把文件内容读到data中
        std::ifstream input(file_name, std::ios::binary );
        std::copy( 
            std::istreambuf_iterator<char>(input), 
            std::istreambuf_iterator<char>( ),
            static_cast<array<char, capity*sizeof(int)>>(data).begin());
    }
    int get(int key) {
        int hashcode = hash(key);
        int bucket = hashcode & 6;
        for (int i = bucket; i < 1 << 6; ++i) {
            if (data[i] == key) {
                return data[i + (1 << 6)];
            } else if (data[i] == NOT_EXIST) {
                return NOT_EXIST;
            }
        }
        return NOT_EXIST;
    }
    void insert (int key, int value) {
        int hashcode = hash(key);
        int bucket = hashcode & 6;
        for (int i = bucket; i < 1 << 6; ++i) {
            if (data[i] == NOT_EXIST || data[i] == key) {
                data[i] = key;
                data[i + (1 << 6)] = value;
            }
        }
    }
}
```

## Computer Science Fundamentals

- Polymorphism. Constructors cannot be virtual functions; destructors can be virtual functions.
- Some understanding of concurrency.

## Others

My strengths:
I talked about solid fundamentals and good algorithms, since I solved many problems.
He talked about his view on grinding algorithm problems. Although he did not reject it, he talked a lot about issues with ACM contestants, such as not considering engineering implementation carefully enough. It felt like he had a lot of complaints.

He also asked why my undergraduate grades were good but my graduate grades were not as good.
I answered honestly: graduate grades are not important.

The interviewer had also studied at Beihang in earlier years, and at the end we chatted a bit about the current state of my lab.

## Eighth Round: Another Mysterious Interview, Bad Review

time: 2020-04-13 12:59:03

Today (4.13) at 11:30 AM, I received a call and immediately started a 40-minute interview. They did not schedule the time with me in advance at all, interrupting many things I had been doing. He also said that colleagues from their group should already have interviewed me once before. But the previous seven Tencent interviews were all with other groups.

## Content

Self-introduction, project introduction.

Questions about APIs and time complexity of various C++ containers.

He even asked me to design a recommendation system. Internally I was... I only briefly described the implementation of some common recommendation algorithms.

## Two Algorithm Problems

>1. Implement `strcpy` to copy a string.
```cpp
void strcpy(const char* source, const char* destination) {
    if (source == NULL || destination == NULL) return;
    int i = 0;
    while (source[i] != '\0' && source + i != destination) {
        destination[i] = source[i];
        ++i;
    }
}
```

>2. Given 1 billion integers, find the numbers that do not repeat.

```cpp
vector<int> findUnique(const vector<int>& v) {    // 4G
    bitset<1 << 32> seen;    // 4G
    for (int i : v) {
        seen.set(static_cast<unsigned int>(i));
    }
    vector<int> ans; // 4 G = 10亿 * 4
    for (size_t i = 0; i < seen.size(); ++i) {
        if (!seen[i]) {
            ans.insert(static_cast<int>(i);
        }
    }
    return ans;
}
```

The interviewer was from the WeChat Kitcup Recommendation System group. I directly gave feedback that next time they should schedule the interview in advance; this time was very rushed and my state was not good.
This interviewer's attitude was also among the worst I have encountered. Several times when communicating with him, he said, "don't ask me." Many questions were also unclear. Bad review. I really wanted to complain about him.

## Ninth Round

This interview only lasted about 10 minutes. He asked one algorithm problem: determine whether a linked list is a palindrome. Required time complexity O(N), space complexity O(1). Around LeetCode Medium difficulty.

Then he asked me: I see you have interviewed with Tencent many times before and even reached the HR round. Why did you not sign? I said it was because of a work location conflict. I expected to be in Beijing, while their group was all in Shenzhen. The interviewer said: I am also interviewing for a Shenzhen role. You selected "accept reassignment" in your application preferences. Then there was no need to continue the interview. The fastest interview in history!!

Afterward I checked the official website, and it was really true, so I changed it to not accept reassignment. I hope the Beijing groups can pick me up. I wonder whether engineers in a group can directly pick specific candidates. Otherwise, I could contact classmates and senior schoolmates working at Tencent.

## 4.26 Written Test

TX's written test was quite difficult, especially problems 2 and 3. But TX does not produce algorithm problems; it is only a porter of algorithm problems.

## 2. Find the Closest Pair Between Two Point Sets

[Original problem link](https://www.acwing.com/problem/content/121/)
Da Xuecai provides a [video explanation](https://www.acwing.com/video/96/). In short, mark the points from the two point sets separately, then use the closest pair algorithm for one point set, treating points with the same marker as infinitely far apart. [Closest pair within one point set](https://www.geeksforgeeks.org/closest-pair-of-points-using-divide-and-conquer-algorithm/) is a very classic problem and can be solved with divide and conquer.

Time complexity: O(N log N), space complexity: O(N).

## 2. Card Flip

[Original problem: Swap and Flip](https://atcoder.jp/contests/keyence2020/tasks/keyence2020_d)

Dynamic Programming. `DP[mask][i]` means the cards in `mask` are on the leftmost side, and the `i`-th card is the last among those cards, while maintaining the minimum number of operations for non-decreasing order.
![illustration](https://uploadfiles.nowcoder.com/images/20200428/407266647_1588073288778_01811641BAED0EC338B3EE5B829175F4 "image title") 
Time complexity: O(n^2 * 2^n),
space complexity: O(n * 2 ^ n)

```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;

const int INF = 0x3f3f3f3f;

int main() {
    int N;
    cin >> N;
    vector<int> A(N);
    vector<int> B(N);

    for (int i = 0; i < N; ++i) {
        cin >> A[i];
    }
    for (int i = 0; i < N; ++i) {
        cin >> B[i];
    }
    vector<vector<int>> dp((1 << N), vector<int>(N, INF));
    // dp[mask][i]: the minimum operation when cards in mask are in leftmost and the ith card is in the end
    for (int i = 0; i < N; ++i) {
        dp[1 << i][i] = 0;  // there is no card in leftmost whose id larger than i
    }
    for (int s = 0; s < (1 << N); ++s) {
        for (int i = 0; i < N; ++i) {
            if (((s >> i) & 1) == 0)
                continue;                  // i is not in state
            int c = __builtin_popcount(s); // card number in s
            int value_i = (c % 2) == (i % 2) ? B[i] : A[i];
            int cost = c; // the number of card in s, whose id is larger than j
            for (int j = 0; j < N; ++j) {
                if (((s >> j) & 1) == 1) {
                    --cost;
                    continue; // j is in state already
                }
                int value_j = (j % 2) == (c % 2) ? A[j] : B[j];
                if (value_j >= value_i) {
                    dp[s | (1 << j)][j] =
                        min(dp[s | (1 << j)][j], dp[s][i] + cost);
                }
            }
        }
    }
    int ans = INF;
    for (int i = 0; i < N; ++i) {
        ans = min(ans, dp[(1 << N) - 1][i]);
    }
    ans = ans >= INF ? -1 : ans;
    cout << ans << endl;
    return 0;
}
```

