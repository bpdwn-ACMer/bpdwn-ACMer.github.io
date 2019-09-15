---
title: hdu-6681 Rikka with Cake
date: 2019-08-20 01:32:11
categories: 2019hdu多校第九场
tags:
---


## 题目链接
[hdu-6681](http://acm.hdu.edu.cn/showproblem.php?pid=6681)
## Problem Description

Rikka's birthday is on June 12th. The story of this problem happens on that day.  
  
Today is Rikka's birthday. Yuta prepares a big cake for her: the shape of this cake is a rectangular of  n  centimeters times  m  centimeters. With the guidance of a grimoire, Rikka is going to cut the cake.  
  
For simplicity, Rikka firstly builds a Cartesian coordinate system on the cake: the coordinate of the left bottom corner is  (0,0)  while that of the right top corner is  (n,m). There are  K  instructions on the grimoire: The  ith cut is a ray starting from  (xi,yi)  while the direction is  Di. There are four possible directions: L, passes  (xi−1,yi); R, passes  (xi+1,yi); U, passes  (xi,yi+1); D, passes  (xi,yi−1).  
  
Take advantage of the infinite power of Tyrant's Eye, Rikka finishes all the instructions quickly. Now she wants to count the number of pieces of the cake. However, since a huge number of cuts have been done, the number of pieces can be very large. Therefore, Rikka wants you to finish this task.

  

## Input

The first line of the input contains a single integer  T(1≤T≤100), the number of the test cases.  
  
For each test case, the first line contains three positive integers  n,m,K(1≤n,m≤109,1≤K≤105), which represents the shape of the cake and the number of instructions on the grimoire.  
  
Then  K  lines follow, the  ith line contains two integers  xi,yi(1≤xi<n,1≤yi<m)  and a char  Di∈{'L','R','U','D'}, which describes the  ith cut.  
  
The input guarantees that there are no more than  5  test cases with  K>1000, and no two cuts share the same  x  coordinate or  y  coordinate, i.e.,  ∀1≤i<j≤K,  xi≠xj  and  yi≠yj.

  

## Output

For each test case, output a single line with a single integer, the number of pieces of the cake.  
  

_Hint_

  
The left image and the right image show the results of the first and the second test case in the sample input respectively. Clearly, the answer to the first test case is  3while the second one is  5.  

![](http://acm.hdu.edu.cn/data/images/C586-1002-1.png)

  

  

## Sample Input

2
4 4 3
1 1 U
2 2 L
3 3 L
5 5 4
1 2 R
3 1 U
4 3 L
2 4 D

  

## Sample Output

3
5

## 题意
矩形平面上有一些水平或垂直的射线，问这些射线把这个矩形分成了几块

## 题解
有一个神仙公式叫欧拉公式：$V-E+F=2$，V是点数，E是边数，F是平面数
V = 矩形四个点 + 射线端点n + 射线到矩形交点n + 射线间焦点c = 2n + 4 + c
E = 矩形四条边 + 矩形四条边被射线分割的边n + 射线n条 + 射线与射线分割的边(每个交点都会产生2条新边) 2c
所以F = 2 + c，再扣掉矩形外的平面答案就是1 + c
求交点个数只要从左往右遍历垂直射线，遍历过程中每遇到水平射线左端点则在树状数组上+1，遇到水平射线右端点就在树状数组上-1

## 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
const int mx = 1e5+5;
int C[mx];
int lowbit(int x) {
    return x & -x;
}
vector <int> vy;
struct Seg {
    int x, y1, y2;
    bool operator < (Seg other) const {
        return x < other.x;
    }
}seg[mx];

struct Point {
    int x, y, val;
    bool operator < (Point other) const {
        return x < other.x;
    }
}point[mx];

int getid(int y) {
    return lower_bound(vy.begin(), vy.end(), y) - vy.begin() + 1;
}

void update(int x, int val) {
    for (int i = x; i < mx; i+=lowbit(i))
        C[i] += val;
}

int query(int l, int r) {
    int sumr = 0, suml = 0;
    for (int i = r; i > 0; i-=lowbit(i)) sumr += C[i];
    for (int i = l-1; i > 0; i-=lowbit(i)) suml += C[i];
    return sumr-suml;
}

int main() {
    int T;
    scanf("%d", &T);

    while (T--) {
        memset(C, 0, sizeof(C));
        vy.clear();
        
        int n, m, k, x, y;
        int cnts = 0, cntp = 0;
        char dir[2];
        scanf("%d%d%d", &n, &m, &k);
        vy.push_back(0); vy.push_back(m);
        for (int i = 1; i <= k; i++) {
            scanf("%d%d%s", &x, &y, dir);
            vy.push_back(y);
            if (dir[0] == 'L') {
                point[cntp++] = Point{0, y, 1};
                point[cntp++] = Point{x, y, -1};
            } else if (dir[0] == 'R') {
                point[cntp++] = Point{x, y, 1};
                point[cntp++] = Point{n, y, -1};
            } else if (dir[0] == 'U') {
                seg[cnts++] = Seg{x, y, m};
            } else {
                seg[cnts++] = Seg{x, 0, y};
            }
        }
        sort(vy.begin(), vy.end());
        sort(seg, seg+cnts);
        sort(point, point+cntp);
        for (int i = 0; i < cnts; i++) {
            seg[i].y1 = getid(seg[i].y1);
            seg[i].y2 = getid(seg[i].y2);
        }
        for (int i = 0; i < cntp; i++) point[i].y = getid(point[i].y);
        int pos = 0;
        int sum = 0;
        for (int i = 0; i < cnts; i++) {
            while (point[pos].x < seg[i].x) {
                update(point[pos].y, point[pos].val);
                pos++;
            }
            sum += query(seg[i].y1, seg[i].y2);
        }
        printf("%d\n", sum+1);
    }
    return 0;
}
```
