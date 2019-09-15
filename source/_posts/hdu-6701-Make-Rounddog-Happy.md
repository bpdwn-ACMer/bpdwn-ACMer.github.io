---
title: hdu-6701 Make Rounddog Happy
date: 2019-08-21 23:53:54
categories: 2019hdu多校第十场
tags: 启发式分治
---


## 题目链接
[Make Rounddog Happy](http://acm.hdu.edu.cn/showproblem.php?pid=6701)
## Problem Description

Rounddog always has an array  a1,a2,⋯,an  in his right pocket, satisfying  1≤ai≤n.  
  
A subarray is a non-empty subsegment of the original array. Rounddog defines a good subarray as a subsegment  al,al+1,⋯,ar  that all elements in it are different and  max(al,al+1,…,ar)−(r−l+1)≤k.  
  
Rounddog is not happy today. As his best friend, you want to find all good subarrays of  a  to make him happy. In this case, please calculate the total number of good subarrays of  a.  

  

## Input

The input contains several test cases, and the first line contains a single integer  T  (1≤T≤20), the number of test cases.  
  
The first line of each test case contains two integers  n  (1≤n≤300000)  and  k  (1≤k≤300000).  
  
The second line contains  n  integers, the  i-th of which is  ai  (1≤ai≤n).  
  
It is guaranteed that the sum of  n  over all test cases never exceeds  1000000.  

  

## Output

One integer for each test case, representing the number of subarrays Rounddog likes.  

  

## Sample Input

2
5 3
2 3 2 2 5
10 4
1 5 4 3 6 2 10 8 4 5

  

## Sample Output

7
31
## 题意
给出一个数组a和k，问有多少对$l,r$满足$max(al,al+1,…,ar)−(r−l+1)≤k$

## 题解
用启发式分治的方法遍历每个最大值掌控的区间，记区间为$[l,r]$，最大值在mid位置上，如果左区间更小就遍历左区间，计算以左区间每个点为左端点的方案数，否则就遍历右区间，预处理一个数组pre[i]表示以i向右最多延伸到哪里，使i到pre[i]数字不重复，suf[i]表示i向左最多延伸到哪里，使得suf[i]到i数字不重复，这样就能O(1)计算以每个点为左端点的方案数了。总体复杂度$O(n\log n)$

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;
const int mx = 3e5+5;
int a[mx], pre[mx], suf[mx];
int n, k;
bool vis[mx];

struct Node {
    int v, pos;
}tree[mx<<2];
 
void pushUp(int rt) {
    tree[rt].v = max(tree[rt<<1].v, tree[rt<<1|1].v);
    tree[rt].pos = (tree[rt<<1].v > tree[rt<<1|1].v ? tree[rt<<1].pos : tree[rt<<1|1].pos);
}
 
void build(int l, int r, int rt) {
    if (l >= r) {
        tree[rt].v = a[r];
        tree[rt].pos = r;
        return;
    }
    int mid = (l + r) / 2;
    build(l, mid, rt<<1);
    build(mid+1, r, rt<<1|1);
    pushUp(rt);
}
 
int query(int L, int R, int l, int r, int rt) {
    if (L <= l && r <= R) return tree[rt].pos;
    int mid = (l + r) / 2;
    int pos1 = -1, pos2 = -1;
    if (L <= mid) pos1 = query(L, R, l, mid, rt<<1);
    if (mid < R) pos2 = query(L, R, mid+1, r, rt<<1|1);
    if (pos1 == -1) return pos2;
    else if (pos2 == -1) return pos1;
    else return a[pos1] > a[pos2] ? pos1 : pos2;
}

void dfs(int l, int r, long long &ans) {
    if (l > r) return;
    int mid = query(l, r, 1, n, 1);
    int len = max(1, a[mid]-k);
    if (mid-l <= r-mid) {
        for (int i = l; i <= mid; i++) {
            int L = max(mid, i+len-1);
            int R = min(pre[i], r);
            ans += max(0, R-L+1);
        }
    } else {
        for (int i = mid; i <= r; i++) {
            int R = min(mid, i-len+1);
            int L = max(suf[i], l);
            ans += max(0, R-L+1);
        }
    }
    dfs(l, mid-1, ans);
    dfs(mid+1, r, ans);
}

int main() {
    int T;
    scanf("%d", &T);

    while (T--) {
        scanf("%d%d", &n, &k);
        for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
        build(1, n, 1);
        int pos = 0;
        for (int i = 1; i <= n; i++) {
            while (pos < n && !vis[a[pos+1]]) {
                pos++;
                vis[a[pos]] = true;
            }
            pre[i] = pos;
            vis[a[i]] = false;
        }
        pos = n+1;
        for (int i = n; i >= 1; i--) {
            while (pos > 1 && !vis[a[pos-1]]) {
                pos--;
                vis[a[pos]] = true;
            }
            suf[i] = pos;
            vis[a[i]] = false;
        }
        
        long long ans = 0;
        dfs(1, n, ans);
        printf("%lld\n", ans);
    }
    return 0;
}
```

