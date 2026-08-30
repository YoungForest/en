---
title: LeetCode weekly contest 118
date: 2019-01-06 10:37:36
description: Progress after switching to C++ through power sums, pancake sorting, tree flips, rational equality and renewed language fundamentals.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/01/06/LeetCode-weekly-contest-118/
  en: https://youngforest.github.io/en/2019/01/06/LeetCode-weekly-contest-118/
---
The weekly LeetCode weekly contest has begun. This week I genuinely improved compared with before. First, I am more familiar with C++; previously I always used Python. The answering process was also smoother, and I almost solved three problems.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-118/en-hero.webp" alt="Forest sharpens new tools beside power beads, a flipped disc stack, a preorder tree and equal repeating-water vessels" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## 970. Powerful Integers

The first problem was only worth 3 points, and there was no algorithmic optimization to make. A brute approach was enough.
The corner case to note is when `x == 1` or `y == 1`, since `1^n = 1`.

```cpp
class Solution {
public:
    vector<int> powerfulIntegers(int x, int y, int bound) {
        unordered_set<int> res;
        
        for (int i = 0; ; ++i) {
            int ans = pow(x, i);
            if (ans > bound) break;
            for (int j = 0; ; ++j) {
                int ans_new = ans + pow(y, j);
                if (ans_new <= bound)
                    res.insert(ans_new);
                else
                    break;
                if (y == 1) break;
            }
            if (x == 1) break;
        }

        vector<int> res_vec;
        for (const auto a: res) {
            res_vec.push_back(a);
        }
        return res_vec;
    }
};
```

## 969. Pancake Sorting

The first idea was a brute-force method: sort the final element each time, similar to selection sort.
That is, first find the position of the final element, flip it to the first position, then flip once more to place it at the final position.
The invariant is that all elements after the final position are sorted.

```cpp
class Solution {
public:
    void flip(vector<int>& A, int begin,int end) {
        for (; begin < end; ++begin, --end) {
            int temp;
            temp = A[begin];
            A[begin] = A[end];
            A[end] = temp;
        }
    }
    
    vector<int> pancakeSort(vector<int>& A) {
        vector<int> res;
        for (int i = A.size(); i > 1; --i) {
            int index = 0;
            for (int j = 0; j < i; ++j) {
                if (A[j] == i) {
                    index = j;
                    break;
                }           
            }
            if (index + 1 != i) {
                res.push_back(index+1);
                flip(A, 0, index);
                res.push_back(i);
                flip(A, 0, i - 1);
            }
        }
        
        return res;
    }
};
```

## 971. Flip Binary Tree To Match Preorder Traversal

This problem can also be solved straightforwardly. Keep recursively checking whether each subtree can be flipped. Because I misunderstood the meaning of one parameter, even though the design was fine, there was an implementation oversight. `voyageIndex` was passed as the position of `root`, while `flip` returns the expected position of the root node of the next subtree in `voyage`.
This caused me to AC only at 12:19, 20 minutes late, and I also got 4 WAs.

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int flip(int voyageIndex, TreeNode* root, vector<int>& voyage, vector<int>& res) {
        if (root == nullptr) return voyageIndex;
        // cout << "root->val : " << root->val << endl;
        if (voyage[voyageIndex] != root->val) return -1;
        // cout << "root->val : " << root->val << endl;
        int voyageIndexEnd;
        bool flag = false;
        if (root->left != nullptr && voyage[voyageIndex + 1] == root->left->val) {
            ;
        } else if (root->right != nullptr && voyage[voyageIndex + 1] == root->right->val && root->left != nullptr) {
            TreeNode *temp;
            temp = root->left;
            root->left = root->right;
            root->right = temp;
            flag = true;
        } else {
            ;
        }
        // cout << "voyageIndex: " << voyageIndex << " "<< root->val << endl;
        voyageIndexEnd = flip(voyageIndex + 1, root->left, voyage, res);
        // cout << "voyageIndexEnd: " << voyageIndexEnd << endl; 
        if (voyageIndexEnd == -1) return -1;
        voyageIndexEnd = flip(voyageIndexEnd, root->right, voyage, res);
        // cout << "voyageIndexEnd: " << voyageIndexEnd << endl; 
        if (voyageIndexEnd == -1) return -1;
        if (flag) res.push_back(root->val);
        return voyageIndexEnd;
    }
    vector<int> flipMatchVoyage(TreeNode* root, vector<int>& voyage) {
        vector<int> error = {-1};
        vector<int> res;
        
        if (flip(0, root, voyage, res) == -1) {
            return error;
        } else {
            return res;
        }
    }
};
```

## 972. Equal Rational Numbers

This problem does not test any algorithm. It only tests data-structure abstraction and basic mathematical understanding of Rational Numbers.
I only understood how simple it was after reading the solution. It really is a problem that is hard if you do not know the trick and easy if you do.

```cpp
#include <iostream>
#include <string>
#include <cmath>

using namespace std;

class Fraction {
public:
    long long numerator, denominator;
    long long gcd(long long x, long long y) {
        return y > 0 ? gcd(y, x % y) : x;
    }
    Fraction(long long n, long long d) {
        long long g = this->gcd(n, d);
        this->numerator = n / g;
        this->denominator = d / g;
    }
    Fraction(string S) {
        // <IntegerPart><.><NonRepeatingPart><(><RepeatingPart><)>
        auto dotPosition = S.find(".");
        Fraction ans(0, 1);
        if (dotPosition != string::npos) {
            Fraction integerPart(stol(S.substr(0, dotPosition)), 1);
            ans.add(integerPart);
            
            auto leftParenthese = S.find("(");
            string nonRepeatingPartString;
            if (leftParenthese != string::npos) {
                nonRepeatingPartString = S.substr(dotPosition + 1, leftParenthese - dotPosition - 1);

                
                auto repeatingPartString = S.substr(leftParenthese + 1, S.size() - leftParenthese - 2);
                Fraction repeatingPart(stol(repeatingPartString), pow(10, nonRepeatingPartString.size()) * (pow(10, repeatingPartString.size()) - 1));
                ans.add(repeatingPart);
            } else {
                nonRepeatingPartString = S.substr(dotPosition + 1, S.size() - dotPosition - 1);
            }
            if (nonRepeatingPartString.size() > 0) {
                Fraction nonRepeatingPart(stol(nonRepeatingPartString), pow(10, nonRepeatingPartString.size()));
                ans.add(nonRepeatingPart);
            }
        } else {
            Fraction integerPart(stol(S.substr(0, S.size())), 1);
            ans.add(integerPart);
        }
        
        this->numerator = ans.numerator;
        this->denominator = ans.denominator;
    }
    void add(Fraction b) {
        Fraction n(this->numerator * b.denominator + this->denominator * b.numerator, this->denominator * b.denominator);
        this->numerator = n.numerator;
        this->denominator = n.denominator;
    }
    bool equal(Fraction b) {
        return this->numerator == b.numerator && this->denominator == b.denominator;
    }
};

class Solution {
public:
    bool isRationalEqual(string S, string T) {
        return Fraction(S).equal(Fraction(T));
    }
};

int main2(int argc, char const *argv[])
{
    Solution s;
    // auto res = s.isRationalEqual("0.(52)", "0.5(25)");
    Fraction a("0.(52)");
    cout << a.numerator << " / " << a.denominator << endl;

    Fraction b("0.5(25)");
    cout << b.numerator << " / " << b.denominator << endl;

    Fraction c("0.52(52)");
    cout << c.numerator << " / " << c.denominator << endl;

    return 0;
}
```

In fact, if you notice the constraints on rational numbers, there is an even more opportunistic solution. For details, see [Han's solution](https://leetcode.com/problems/equal-rational-numbers/discuss/214203/JavaC++Python-Easy-Cheat).
```
Each part consists only of digits.
The <IntegerPart> will not begin with 2 or more zeros.  (There is no other restriction on the digits of each part.)
1 <= <IntegerPart>.length <= 4
0 <= <NonRepeatingPart>.length <= 4
1 <= <RepeatingPart>.length <= 4
```

My code is very long. While writing it, I exposed how unfamiliar I am with implementing C++ classes. I originally wanted to use operator overloading, but later realized I did not know how.
It seems I still need to spend time reading the other parts of *C++ Primer*. After all:
> If a craftsman wants to do good work, he must first sharpen his tools.
-- *The Analects*, "Wei Ling Gong"

## Postscript

Finally, here is my review of *C++ Primer*:
> A masterpiece for getting started with C++. It has been recommended by countless people since freshman year, but I was always too lazy to read it and naively thought the C++ taught in school was enough. Later, as my programming studies continued to deepen, I gradually developed respect for C++. I realized that the C++ I had always been using was not real C++ at all, but just C with classes. Especially when studying CS107 Programming Paradigms, the teacher sharply pointed out that most C++ programmers are like this. After hearing that, I felt so ashamed that I even removed C++ from the list of languages I claimed to know on my resume. During the New Year's holiday, I finally had the courage to restart my C++ learning. I went directly to the English edition, using LeetCode in C++ as practice support, and first read chapters 1-3 and 9-12, meaning the basics, containers, and smart pointers. After reading them, I felt enlightened and regretted not "learning" C++ earlier. Since this book covers everything, and it is really too thick, I do not plan to finish the whole book in the short term. But I believe that every chapter I read will bring great benefit.
