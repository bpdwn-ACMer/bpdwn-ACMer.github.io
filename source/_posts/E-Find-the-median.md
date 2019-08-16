---
title: E-Find the median
date: 2019-08-13 19:27:48
categories: 2019牛客多校第七场
tags: 
---
![problem](problem.png)
# 题意
N次操作,每次塞入区间$[L,R]$的每个数，并输出此时的中位数。
# 题解
如果题目不是每次塞入一整个区间，而是只塞入一个数，可以简单的建权值线段树查询区间第K大，由于每次都是查询整个区间就不用主席树了。
现在题目是塞一个区间，那么就要想办法把原来的权值线段树的单点更新变为区间更新，如果L,R的范围较小，可以很容易的把单点修改换成区间修改，题目范围是1e9不可能对整个1e9的区间建树，我们就需要对区间端点进行离散化，原来权值线段树的每一个点的含义由点变成了区间，例如1-5，6-10，离散化为为1-2，3-4，点1就表示1-5这个区间，点3表示6-10这个区间，
但是这样定义点的含义会有一点问题，比如1-5,5-10，离散化为1-2,2-3，此时点1表示1-5，点2表示5-10，点1和点2表示的区间重叠了，那如果我把点含义定义成左闭右开呢？点1表示1-4，点2表示5-9，点10表示10-10，此时如果我想更新1-5这个区间，会发现没法更新，这样定义也不行。
一种做法是把输入的区间右端点+1，比如上面的例子1-5，5-10，变成1-6,5-11，离散化后变成1-3,2-4，点1表示1-4，点2表示5-5，点3表示6-9，点4表示10-10，我要更新1-5只要更新点1和2就行了，问题得到解决。
# 代码
```cpp
#include <bits/stdc++.h>
typedef long long ll;
using namespace std;
const int mx = 8e5+5;
int x[mx], y[mx];
vector <int> vv;

int getid(int x) {
    return lower_bound(vv.begin(), vv.end(), x) - vv.begin() + 1;
}

struct Tree {
    int lazy, len;
    ll sum;
}tree[mx<<2];

void pushUp(int rt) {
    tree[rt].sum = tree[rt<<1].sum + tree[rt<<1|1].sum;
}

void pushDown(int rt) {
    tree[rt<<1].lazy += tree[rt].lazy;
    tree[rt<<1|1].lazy += tree[rt].lazy;
    tree[rt<<1].sum += 1LL * tree[rt<<1].len * tree[rt].lazy;
    tree[rt<<1|1].sum += 1LL * tree[rt<<1|1].len * tree[rt].lazy;
    tree[rt].lazy = 0;
}

void build(int l, int r, int rt) {
    if (l == r) {
        tree[rt].len = vv[r+1-1] - vv[l-1];
        tree[rt].sum = tree[rt].lazy = 0;
        return;
    }
    int mid = (l + r) / 2;
    build(l, mid, rt<<1);
    build(mid+1, r, rt<<1|1);
    tree[rt].len = tree[rt<<1].len + tree[rt<<1|1].len;
}

void update(int L, int R, int l, int r, int rt) { 
    if (L <= l && r <= R) {
        tree[rt].sum += tree[rt].len;
        tree[rt].lazy += 1;
        return;
    }
    int mid = (l + r) / 2;
    pushDown(rt);
    if (L <= mid) update(L, R, l, mid, rt<<1);
    if (mid < R) update(L, R, mid+1, r, rt<<1|1);
    pushUp(rt);
}

int query(int l, int r, ll k, int rt) {
    
    if (l == r) {
        ll t = tree[rt].sum / tree[rt].len;

        return vv[l-1] + (k-1) / t;
    }
    pushDown(rt);
    int mid = (l + r) / 2;
    if (tree[rt<<1].sum >= k) return query(l, mid, k, rt<<1);
    else return query(mid+1, r, k-tree[rt<<1].sum, rt<<1|1);
}

int main() {
    ll n, A1, B1, C1, M1, A2, B2, C2, M2;
    scanf("%lld", &n);
    scanf("%d%d%lld%lld%lld%lld", &x[1], &x[2], &A1, &B1, &C1, &M1);
    scanf("%d%d%lld%lld%lld%lld", &y[1], &y[2], &A2, &B2, &C2, &M2);
    for (int i = 3; i <= n; i++) {
        x[i] = (A1*x[i-1] + B1*x[i-2] + C1) % M1;
        y[i] = (A2*y[i-1] + B2*y[i-2] + C2) % M2;
    }
    for (int i = 1; i <= n; i++) {
        if (x[i] > y[i]) swap(x[i], y[i]);
        x[i]++; y[i]+=2;
        vv.push_back(x[i]);
        vv.push_back(y[i]);
    }
    sort(vv.begin(), vv.end());
    vv.erase(unique(vv.begin(), vv.end()), vv.end());
    vv.push_back(vv[vv.size()-1]+1);
    build(1, vv.size()-1, 1);

    ll sum = 0;
    for (int i = 1; i <= n; i++) {
        update(getid(x[i]), getid(y[i])-1, 1, vv.size()-1, 1);
        sum += y[i]-x[i];
        printf("%d\n", query(1, vv.size()-1, (sum-1)/2+1, 1));
    }
    return 0;
}
```
