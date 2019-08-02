---
title: H-Magic Line
date: 2019-07-27 10:54:22
categories: 2019牛客暑期多校训练营（第三场）
tags: 几何
---

![problem](problem.png)

## 题意

二维平面上有n个整数坐标的点，求出一条直线将平面上的点分为数量相等的两部分，且线上不能有点，输出线上两个点确定该直线

## 题解：
先在左下角无穷远处取一质数坐标点(x,y)  对该点和n个点进行极角排序，设排序后中点坐标为(a,b)则这两点连线会将点分为数量相等的两部分，接着取左下角关于中点的对称点(a+a-x, b+b-y),再将该点左移动一格变成(2a-x-1, 2b-y)
则(x,y) (2a-x-1, 2b-y)两点确定的直线就可以分割点为两部分，且线上不会有点
## 代码
```cpp
#include <cstdio>
#include <cmath>
#include <cstring>
#include <algorithm>
using namespace std;
const int MAX=100005;
const int INF=999999;
typedef long long ll;

int n,top;
struct Node
{
       ll x,y;
}p[MAX],S[MAX];
ll Cross(Node a,Node b,Node c)
{
       return (b.x-a.x)*(c.y-a.y)-(b.y-a.y)*(c.x-a.x);
}
  
ll dis(Node a,Node b)
{
    return (a.x-b.x)*(a.x-b.x)+(a.y-b.y)*(a.y-b.y);
}
 bool cmp(Node a,Node b)
{  
   ll flag = Cross(p[1],a,b);
   if(flag != 0) return flag > 0;
   return dis(p[1],a) < dis(p[1],b); 
}
  
int main()
{
    int T;
    scanf("%d", &T);
    while(T--)
    {
        int n;
        scanf("%d", &n);
        p[1].x = -400000009; p[1].y = -2e3;
 
        for(int i=1;i<=n;i++)
            scanf("%lld%lld",&p[i+1].x,&p[i+1].y);
        n++;
        sort(p+2, p+1+n, cmp);
         
        int pos = n/2 + 1;
        ll a = p[pos].x - p[1].x + p[pos].x-1;
        ll b = p[pos].y - p[1].y + p[pos].y;
 
        printf("%lld %lld %lld %lld\n", p[1].x, p[1].y, a, b);
    }
}
```

