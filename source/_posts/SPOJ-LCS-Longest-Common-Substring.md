---
title: SPOJ-LCS Longest Common Substring
date: 2019-07-20 01:33:15
categories: 后缀自动机
tags: [ACM,字符串]
---

## 题目连接：
https://vjudge.net/problem/SPOJ-LCS

## Description

A string is finite sequence of characters over a non-empty finite set Σ.

In this problem, Σ is the set of lowercase letters.

Substring, also called factor, is a consecutive sequence of characters occurrences at least once in a string.

Now your task is simple, for two given strings, find the length of the longest common substring of them.

Here common substring means a substring of two or more strings.

## Input
The input contains exactly two lines, each line consists of no more than 250000 lowercase letters, representing a string.
 
## Output
The length of the longest common substring. If such string doesn't exist, print "0" instead.
## Sample Input
alsdfkjfjkdsal
fdjskalajfkdsla



## Sample Output
3



## Hint

## 题意

求两个串的最长公共子串长度

## 题解：
对a串建后缀自动机，b串在建好的自动机上跑，跑的过程和建树是一样的，如果Next[p][c]存在则nowlen++，否则p回退到fa[p]直到Next[p][c]存在，并更新nowlen为len[p]+1,对整个过程的nowlen取max就是答案
## 代码
```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;
const int mx = 1e6+5;

struct SAM_automaton {
    int Next[mx][26], len[mx], fa[mx];
    int last, tot;
    int newnode() {
        tot++;
        for (int i = 0; i < 26; i++) Next[tot][i] = 0;
        return tot;
    }

    void init() {
        tot = 0;
        last = newnode();
    }

    void add(int c) {
        int p = last;
        int np = last = newnode();
        len[np] = len[p] + 1;
        while (p && !Next[p][c]) {
            Next[p][c] = np;
            p = fa[p];
        }
        if (!p) fa[np] = 1;
        else {
            int q = Next[p][c];
            if (len[q] == len[p] + 1) fa[np] = q;
            else {
                int nq = newnode();
                len[nq] = len[p] + 1;
                fa[nq] = fa[q];
                for (int i = 0; i < 26; i++) Next[nq][i] = Next[q][i];
                fa[q] = fa[np] = nq;
                while (p && Next[p][c] == q) {
                    Next[p][c] = nq;
                    p = fa[p];
                }
            }
        }
    }
}SAM;

char a[mx], b[mx], str[mx];

int main() {
    SAM.init();
    
    scanf("%s%s", a, b);
    int lena = strlen(a);
    int lenb = strlen(b);
    for (int i = 0; i < lena; i++) SAM.add(a[i]-'a');
    int ans = 0, len = 0;
    for (int i = 0, p = 1; i < lenb; i++) {
        int c = b[i] - 'a';
        if (SAM.Next[p][c]) {
            len++;
            p = SAM.Next[p][c];
        } else {
            while (p && !SAM.Next[p][c]) p = SAM.fa[p];
            if (!p) {
                p = 1;
                len =0;
            } else {
                len = SAM.len[p] + 1;
                p = SAM.Next[p][c];
            }
        }
        ans = max(ans, len);
    }

    printf("%d\n", ans);    
    return 0;
}
```
