---
title: SPOJ-LCS2 Longest Common Substring II
date: 2019-07-20 01:54:09
categories: 后缀自动机
tags: [ACM]
---

## 题目连接：
https://vjudge.net/problem/SPOJ-LCS2

## Description

A string is finite sequence of characters over a non-empty finite set Σ.

In this problem, Σ is the set of lowercase letters.

Substring, also called factor, is a consecutive sequence of characters occurrences at least once in a string.

Now your task is a bit harder, for some given strings, find the length of the longest common substring of them.

Here common substring means a substring of two or more strings.

## Input
The input contains at most 10 lines, each line consists of no more than 100000 lowercase letters, representing a string.
 
## Output
The length of the longest common substring. If such string doesn't exist, print "0" instead.
## Sample Input
alsdfkjfjkdsal
fdjskalajfkdsla
aaaajfaaaa


## Sample Output
2



## Hint

## 题意

求n个串的最长公共子串长度 (2<=n<=10)

## 题解：
对a串建后缀自动机，其余串在建好的自动机上跑，记录len[i][j]为第i个串与a串在状态j时的最长公共长度，
与上一题只有两个串不同的是，在每个串跑完后要从底向上的对每个状态j的fa更新len，原因是如果b串和a串有公共子串abcde
那么一定也有公共子串{bcde,cde...} 这时如果不更新len[bcde], len[cde]..当下一个串开始匹配时如果c串和a串公共子串最长只有bcde，那么bcde这个答案就漏掉了，因为此时len[2][endpos(bcde)] = 0，而上一题只有两个串时却不用更新，因为既然b串和a串有公共长度abcde，那么bcde一定长于{bcde,cde..}不更新他们是不会影响到答案的，也就是说c串可能没有abcde但是有bcde所以必须更新fa[abcde]

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

char a[mx];
int id[mx], len[12][mx];
int main() {
    SAM.init();
    
    scanf("%s", a);
    int lena = strlen(a);
    
    for (int i = 0; i < lena; i++) SAM.add(a[i]-'a');
    int tot = 0;
    for (int i = 1; i <= SAM.tot; i++) len[tot][i] = SAM.len[i];
    
    while (scanf("%s", a) != EOF) {
        lena = strlen(a);
        tot++;
        int nowlen = 0, p = 1;
        for (int i = 0; i < lena; i++) {
            int c = a[i] - 'a';
            if (SAM.Next[p][c]) {
                nowlen++;
                p = SAM.Next[p][c];
            } else {
                while (p && !SAM.Next[p][c]) p = SAM.fa[p];
                if (p == 0) {nowlen = 0, p = 1;}
                else {
                    nowlen = SAM.len[p] + 1;
                    p = SAM.Next[p][c];
                }
            }
            len[tot][p] = max(len[tot][p], nowlen);
        }
        for (int i = SAM.tot; i >= 1; i--) {
            if (len[tot][i]) {
                for (int j = SAM.fa[i]; j && len[tot][j] != SAM.len[j]; j = SAM.fa[j])//如果len[tot][j] == SAM.len[j]说明fa[j]已经更新过
                    len[tot][j] = SAM.len[j];
            }
        }
    }
    int ans = 0;
    for (int i = 1; i <= SAM.tot; i++) {
        int tmp = len[0][i];
        for (int j = 0; j <= tot; j++)
            tmp = min(tmp, len[j][i]);
        ans = max(ans, tmp);
    }
    printf("%d\n", ans);    
    return 0;
}
```

