---
title: hdu-6638-Snowy Smile
date: 2019-08-07 19:26:21
categories: 2019hdu多校第六场
tags:
---

## 题目链接
[Snowy Smile](http://acm.hdu.edu.cn/showproblem.php?pid=6638)


## Problem Description

There are  n  pirate chests buried in Byteland, labeled by  1,2,…,n. The  i-th chest's location is  (xi,yi), and its value is  wi,  wi  can be negative since the pirate can add some poisonous gases into the chest. When you open the  i-th pirate chest, you will get  wi  value.  
  
You want to make money from these pirate chests. You can select a rectangle, the sides of which are all paralleled to the axes, and then all the chests inside it or on its border will be opened. Note that you must open all the chests within that range regardless of their values are positive or negative. But you can choose a rectangle with nothing in it to get a zero sum.  
  
Please write a program to find the best rectangle with maximum total value.

## Input

The first line of the input contains an integer T(1≤T≤100), denoting the number of test cases.  
  
In each test case, there is one integer n(1≤n≤2000) in the first line, denoting the number of pirate chests.  
  
For the next n lines, each line contains three integers xi,yi,wi(−109≤xi,yi,wi≤109), denoting each pirate chest.  
  
It is guaranteed that ∑n≤10000.

  

## Output

For each test case, print a single line containing an integer, denoting the maximum total value.

 
## Sample Input
2 
4 
1 1 50 
2 1 50 
1 2 50
2 2 -500
2
-1 1 5 
-1 1 1


## Sample Output
100
6

## 题意
平面上有n个点，每个点有价值$w_i$，可以任意选一个矩形，获取矩形内所有点的值，求最大的价值和为多少

## 题解
先对所有点坐标离散化，枚举矩形上界，对于上界及以下的点，以y坐标相等的点为一组，按y从大到小，一组一组的插入线段树，每插入完一组点，用线段树求出当前的最大子段和，整个过程相当于在枚举矩形上下界，利用线段树维护最大子段和。

线段树每个节点维护：区间和，左端点向右最大子段和，右端点向左最大子段和，区间最大子段和，用类似区间合并的方式合并

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;
const int mx = 2005;
const ll INF = 1e18;

bool vis[mx][mx];

struct Node {
    int x, y, w;
    int p, q;
}node[mx];

vector <int> vx, vy;
vector <Node> mp[mx];

int getidx(int x) {
    return lower_bound(vx.begin(), vx.end(), x) - vx.begin() + 1;
}

int getidy(int y) {
    return lower_bound(vy.begin(), vy.end(), y) - vy.begin() + 1;
}

struct Tree {
    ll sum;
    ll Lans, Rans, ans;
}tree[mx<<2];

void pushUp(int rt) {
    tree[rt].ans = max(max(tree[rt<<1].ans, tree[rt<<1|1].ans), tree[rt<<1].Rans+tree[rt<<1|1].Lans);
    tree[rt].Lans = max(tree[rt<<1].Lans, tree[rt<<1].sum+tree[rt<<1|1].Lans);
    tree[rt].Rans = max(tree[rt<<1|1].Rans, tree[rt<<1|1].sum+tree[rt<<1].Rans);
    tree[rt].sum = tree[rt<<1].sum + tree[rt<<1|1].sum;
}

void build(int l, int r, int rt) {
    if (l == r) {
        tree[rt].sum = tree[rt].Lans = tree[rt].Rans = tree[rt].ans = 0;
        return;
    }
    int mid = (l + r) / 2;
    build(l, mid, rt<<1);
    build(mid+1, r, rt<<1|1);
    pushUp(rt);
}

void update(int pos, int val, int l, int r, int rt) {
    if (l == r) {
        tree[rt].sum += val;
        tree[rt].Lans = tree[rt].Rans = tree[rt].ans = tree[rt].sum;
        return;
    }
    int mid = (l + r) / 2;
    if (pos <= mid) update(pos, val, l, mid, rt<<1);
    else update(pos, val, mid+1, r, rt<<1|1);
    pushUp(rt);
}

int main() {
    int T;
    scanf("%d", &T);

    while (T--) {
        vx.clear(); vy.clear();

        int n;
        scanf("%d", &n);
        for (int i = 1; i <= n; i++) {
            scanf("%d%d%d", &node[i].x, &node[i].y, &node[i].w);
            vx.push_back(node[i].x);
            vy.push_back(node[i].y);
        }
        sort(vx.begin(), vx.end()); sort(vy.begin(), vy.end());
        vx.erase(unique(vx.begin(), vx.end()), vx.end());
        vy.erase(unique(vy.begin(), vy.end()), vy.end());

        for (int i = 1; i <= n; i++) {
            node[i].p = getidx(node[i].x);
            node[i].q = getidy(node[i].y);
        }
        for (int i = 1; i <= vy.size(); i++) mp[i].clear();
        for (int i = 1; i <= n; i++) mp[node[i].q].push_back(node[i]);
        ll ans = 0;
        for (int i = 1; i <= vy.size(); i++) {
            build(1, vx.size(), 1);
            for (int j = i; j <= vy.size(); j++) {
                for (int k = 0; k < mp[j].size(); k++) {
                    Node tmp = mp[j][k];
                    update(tmp.p, tmp.w, 1, vx.size(), 1);
                }
                ans = max(ans, tree[1].ans);
            }
        }
        printf("%lld\n", ans);
    }
    return 0;
}
```


