---
title: Graph Game
date: 2019-07-30 01:27:56
categories: 2019牛客暑期多校训练营（第三场）
tags: 分块
---

![](problem.png)
## 题意
给出一张无向图，定义S[x]表示与点x直接相连的点集，有两个操作
1 x y表示将第x到第y条边状态变化(若存在则删除，不存在则建立)
2 x y询问S[x]与S[y]是否相等

## 题解
有一个技巧可以压缩的表示点集：给每个点随机一个key，S[x]就可以表示为
与x相连的点的key亦或起来。
考虑如何维护S[x], 因为修改操作是对输入的顺序的区间修改，我们就按边输入的
顺序进行分块，用sum[i][j]记录第i块对点j的贡献值，也就是如果第i块有一条边u-v
那么$sum[i][u] \bigoplus= key[v], sum[i][v] \bigoplus= key[u]$
查询一个点的点集就变成求$sum[1][x] \bigoplus sum[2][x] \bigoplus sum[3][x] \cdots \bigoplus sum[num][x]$
修改的时候如果修改区间落在不同的块上，对夹在中间的块打个lazy标记，表示查询的时候
不用亦或上这个块的贡献，对与两边块内的修改操作可以再用一个数组S记录暴力修改的状态，
比如要修改区间$[l,r]$是块内的,那么就修改$S[u[i]] \bigoplus= key[v[i]], S[v[i]] \bigoplus= key[u[i]]  (i\in[l,r])$
查询x的点集时再xor上S[x]就行，总的来说就是块间修改只需要对sum打标记，块内修改就
暴力更改S,最后复杂度$O(q\sqrt m)$，分块的时候块数要开成$1.5\sqrt m$

## 代码
```cpp
#include <bits/stdc++.h>

using namespace std;
const int mx = 2e5+10;
typedef long long ll;

int belong[mx], block, num, l[mx], r[mx], id[mx];
int n, m, q, u[mx], v[mx];
int lazy[mx];
ll  sum[450][mx], S[mx];

void build() {
    block = 1.5*sqrt(m);
    num = m / block;
    if (m % block) num++;
    for (int i = 1; i <= num; i++) {
        l[i] = (i-1) * block + 1;
        r[i] = i * block;
        lazy[i] = 1;
        for (int j = 1; j <= n; j++)
            sum[i][j] = 0;
    }
    r[num] = m;
    for (int i = 1; i <= m; i++)
        belong[i] = (i-1) / block + 1;
    for (int i = 1; i <= n; i++) S[i] = 0;
         
}

void update(int x, int y) {
    if (belong[x] == belong[y]) {
        for (int i = x; i <= y; i++) {
            S[u[i]] ^= id[v[i]];
            S[v[i]] ^= id[u[i]];
        }
        return;
    }
    int L = belong[x], R = belong[y];
    for (register int i = x; i <= r[L]; i++) {
        S[u[i]] ^= id[v[i]];
        S[v[i]] ^= id[u[i]];
    }
    for (register int i = L+1; i < R; i++) lazy[i] ^= 1;
    for (register int i = l[R]; i <= y; i++) {
        S[u[i]] ^= id[v[i]];
        S[v[i]] ^= id[u[i]];
    }
}


int main() {
    srand(time(NULL));
    for (int i = 1; i < 100005; i++) id[i] = rand() + 1;
    int T;
    scanf("%d", &T);

    while (T--) {
        scanf("%d%d", &n, &m);
        build();
        for (int i = 1; i <= m; i++) {
            scanf("%d%d", &u[i], &v[i]);
            sum[belong[i]][u[i]] ^= id[v[i]];
            sum[belong[i]][v[i]] ^= id[u[i]];
        }
        scanf("%d", &q);
        while (q--) {
            int op, x, y;
            scanf("%d%d%d", &op, &x, &y);
            if (op == 1) {
                update(x, y);
            } else {
                ll ansx = S[x], ansy = S[y];
                for (int i = 1; i <= num; i++) {
                    if (lazy[i]) {
                        ansx ^= sum[i][x];
                        ansy ^= sum[i][y];
                    }
                }
                putchar(ansx==ansy?'1':'0');
            }
        }
        putchar('\n');
    }
    return 0;
}
```
