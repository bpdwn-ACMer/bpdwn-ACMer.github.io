---
title: hdu-6621 K-th Closest Distance
date: 2019-08-02 20:00:49
categories: 2019hdu多校第四场
tags: 主席树
---
## 题目链接
[K-th Closest Distance](http://acm.hdu.edu.cn/showproblem.php?pid=6621)


## Problem Description

You have an array: a1, a2, , an and you must answer for some queries.  
For each query, you are given an interval [L, R] and two numbers p and K. Your goal is to find the Kth closest distance between p and aL, aL+1, ..., aR.  
The distance between p and ai is equal to |p - ai|.  
For example:  
A = {31, 2, 5, 45, 4 } and L = 2, R = 5, p = 3, K = 2.  
|p - a2| = 1, |p - a3| = 2, |p - a4| = 42, |p - a5| = 1.  
Sorted distance is {1, 1, 2, 42}. Thus, the 2nd closest distance is 1.

## Input

The first line of the input contains an integer T (1 <= T <= 3) denoting the number of test cases.  
For each test case:  
冘The first line contains two integers n and m (1 <= n, m <= 10^5) denoting the size of array and number of queries.  
The second line contains n space-separated integers a1, a2, ..., an (1 <= ai <= 10^6). Each value of array is unique.  
Each of the next m lines contains four integers L', R', p' and K'.  
From these 4 numbers, you must get a real query L, R, p, K like this:  
L = L' xor X, R = R' xor X, p = p' xor X, K = K' xor X, where X is just previous answer and at the beginning, X = 0.  
(1 <= L < R <= n, 1 <= p <= 10^6, 1 <= K <= 169, R - L + 1 >= K).

  

## Output

For each query print a single line containing the Kth closest distance between p and aL, aL+1, ..., aR.

 
## Sample Input
1
5 2
31 2 5 45 4
1 5 5 1
2 5 3 2


## Sample Output

0
1
## 题意
给出一个数组，m个询问l, r, p,ｋ区间$[l, r]$中$|p-a_i|$第ｋ大的值是多少，ai相同多次计算
## 题解
二分答案，那么$|p-a_i| \leq ans \Rightarrow p-ans \leq ai \leq p+ans$，利用主席树判断区间$[l,r]$内的$a_i$满足上面限制的数是否大于等于k个，少于k个则增大ans，多于则减小ans，复杂度$O(nlog^2n)$。

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
    if (k == 0) return 0;
    if (1 <= l && r <= k) {
        return T[y].sum - T[x].sum;
    }
    int mid = (l + r) / 2;
    int ans = 0;
    if (1 <= mid) ans += query(l, mid, T[x].l, T[y].l, k);
    if (mid < k && mid < r) ans += query(mid+1, r, T[x].r, T[y].r, k);
    return ans;
}

int main() {
    int T;
    scanf("%d", &T);

    while (T--) {
        v.clear();  cnt = 0;
        int n, m;
        scanf("%d%d", &n, &m);

        for (int i = 1; i <= n; i++) {
            scanf("%d", &a[i]);
            v.push_back(a[i]);
        }
        sort(v.begin(), v.end());
        v.erase(unique(v.begin(), v.end()), v.end());
        for (int i = 1; i <= n; i++) update(1, n, root[i], root[i-1], getid(a[i]));

        int l, r, p, k, ans = 0;
        while (m--) {
            scanf("%d%d%d%d", &l, &r, &p, &k);
            l = l^ans;
            r = r^ans;
            p = p^ans;
            k = k^ans;
            int pk = query(1, n, root[l-1], root[r], p);
            int L = 0, R = 0x3f3f3f3f;
            while (L < R) {
                int mid = (L + R) / 2;
                int Lid = getid(p-mid) - 1;
                int Rid = getid(p+mid);
                if (v[Rid-1] != p+mid) Rid--;
                int sum = query(1, n, root[l-1], root[r], Lid) + (r-l+1- query(1, n, root[l-1], root[r], Rid));
                sum = (r-l+1 - sum);
                if (sum >= k) R = mid;
                else L = mid + 1;
            }
            ans = L;
            printf("%d\n", ans);
        }
    }
    return 0;
}
```


