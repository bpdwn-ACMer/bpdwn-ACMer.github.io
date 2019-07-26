---
title: SPOJ - NSUBSTR Substrings
date: 2019-07-20 01:55:34
categories: 后缀自动机
tags: [ACM]
---

## 题目连接：
https://vjudge.net/problem/SPOJ-NSUBSTR

## Description

You are given a string S which consists of 250000 lowercase latin letters at most. We define F(x) as the maximal number of times that some string with length x appears in S. For example for string 'ababa' F(3) will be 2 because there is a string 'aba' that occurs twice. Your task is to output F(i) for every i so that 1<=i<=|S|.

## Input
String S consists of at most 250000 lowercase latin letters.
 
## Output
Output |S| lines. On the i-th line output F(i).
## Sample Input
ababa

## Sample Output
3
2
2
1
1

## Hint

## 题意

求一个关于串的函数F，F[i]表示串中长度为i的子串出现的最多次数

## 题解：
这题就是求后缀自动机每个点的Right大小，因为每个endpos类表示的是出现次数及位置一样的一类子串，设一个endpos类中的字符串最长
的长度为len[i]，最短为minlen[i], 所以更新时应更新$ F[j] = max(F[j], |Right[i]|) j \in [minlen[i], len[i]] $,但其实不必每个都更新，因为如果长度为n的字符串出现了m次，那么长度为n-1的字符串一定至少出现了m次，及$ F[i] >= F[j] (i < j) $，所以对于每个点只需要更新$ F[len[i]] = max(F[i],|Right[i]|) $，最后从大到小更新一下F就好了。
然后就是求解Right大小，先对所有前缀节点赋1,然后自底向上更新right($ Right[i] = \sum_{edge[i]} Right[j] $),为了保证Right正确需要以拓扑序更新Rgiht，也就是<font color=#FF0000 >len[i]大的节点先更新</font>，因为len[i] > len[fa[i]]，len越大的节点越靠近parent tree底部

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

char str[mx];
int r[mx], b[mx], id[mx], f[mx];

int main() {
    SAM.init();
    scanf("%s", str);
    int len = strlen(str);
    for (int i = 0; i < len; i++) SAM.add(str[i] - 'a');
    for (int i = 0, p = 1; i < len; i++) {
        int c = str[i] - 'a';
        r[SAM.Next[p][c]]++;
        p = SAM.Next[p][c];
    }
    for (int i = 1; i <= SAM.tot; i++) b[SAM.len[i]]++;
    for (int i = 1; i <= len; i++) b[i] += b[i-1];
    for (int i = 1; i <= SAM.tot; i++) id[b[SAM.len[i]]--] = i;
    for (int i = SAM.tot; i >= 1; i--) r[SAM.fa[id[i]]] += r[id[i]];

    for (int i = 1; i <= SAM.tot; i++) f[SAM.len[i]] = max(f[SAM.len[i]], r[i]);
    for (int i = len-1; i >= 1; i--) f[i] = max(f[i], f[i+1]);
    for (int i = 1; i <= len; i++) printf("%d\n", f[i]);
    return 0;
}
```

