---
title: hdu-6601 Keen On Everything But Triangle
date: 2019-07-25 00:56:13
categories: 2019hdu多校第二场
tags: 主席树
---

## 题目连接：
http://acm.hdu.edu.cn/showproblem.php?pid=6601

## Description

N sticks are arranged in a row, and their lengths are a1,a2,...,aN.

There are Q querys. For i-th of them, you can only use sticks between li-th to ri-th. Please output the maximum circumference of all the triangles that you can make with these sticks, or print −1 denoting no triangles you can make.


## Input
There are multiple test cases.

Each case starts with a line containing two positive integers N,Q(N,Q≤10^5).

The second line contains N integers, the i-th integer ai(1≤ai≤10^9) of them showing the length of the i-th stick.

Then follow Q lines. i-th of them contains two integers li,ri(1≤li≤ri≤N), meaning that you can only use sticks between li-th to ri-th.

It is guaranteed that the sum of Ns and the sum of Qs in all test cases are both no larger than 4×10^5.

## Output
For each test case, output Q lines, each containing an integer denoting the maximum circumference.

## Sample Input
5 3
2 5 6 5 2
1 3
2 4
2 5


## Sample Output
13
16
16

## Hint

## 题意
给一个长度为N的数组，Q个询问，(l, r)区间内任三个数能构成的三角形的最大周长

## 题解：
对于排序好的数组，若想要构成三角形周长最大，肯定从最大的边开始取，且三条边是连续的，也就是先取第一大第二大第三大，若不能构成三角形则取第二大第三大第四大，依次取下去。
未排序的数组可以用主席数查询第K大，对于每次询问最多只要查询四十多次，因为若要构造出不能构成三角形的数组，最优构造策略是斐波那契数列，1,2,3,5,8,11,19，到四十多项就超过1e9了。

## 代码
```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
using namespace std;
const int mx = 1e5+5;
typedef long long ll;

int a[mx], root[mx], cnt;
vector <int> v;
struct node {
   int l, r, sum;
}T[mx*40];

int getid(int x) {
   return lower_bound(v.begin(), v.end(), x) - v.begin() + 1;
}

void update(int l, int r, int &x, int y, int pos) {
   T[++cnt] = T[y]; T[cnt].sum++; x = cnt;
   if (l == r) return;
   int mid = (l+r) / 2;
   if (mid >= pos) update(l, mid, T[x].l, T[y].l, pos);
   else update(mid+1, r, T[x].r, T[y].r, pos);
}

int query(int l, int r, int x, int y, int k) {
   if (l == r) return l;
   int mid = (l+r) / 2;
   int sum = T[T[y].l].sum - T[T[x].l].sum;
   if (sum >= k) return query(l, mid, T[x].l, T[y].l, k);
   else return query(mid+1, r, T[x].r, T[y].r, k-sum);
}

int main() {
    int n, m;
    while (scanf("%d%d", &n, &m) != EOF) {
        v.clear();  cnt = 0;
        for (int i = 1; i <= n; i++) {
            scanf("%d", &a[i]);
            v.push_back(a[i]);
        }
        sort(v.begin(), v.end());
        v.erase(unique(v.begin(), v.end()), v.end());
        for (int i = 1; i <= n; i++) update(1, n, root[i], root[i-1], getid(a[i]));
        for (int i = 1; i <= m; i++) {
            int x, y;
            scanf("%d%d", &x, &y);
            bool flag = false;
            int len = y - x + 1;
            for (int j = 1; j <= y-x-1; j++) {
                ll a = v[query(1, n, root[x-1], root[y], len-j+1)-1];
                ll b = v[query(1, n, root[x-1], root[y], len-(j+1)+1)-1];
                ll c = v[query(1, n, root[x-1], root[y], len-(j+2)+1)-1];
                if (b+c > a) {
                    flag = true;
                    printf("%lld\n", a+b+c);
                    break;
                }
            }
            if (!flag) puts("-1");
        }
    }
    return 0;
}
```
