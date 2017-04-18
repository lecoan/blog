---
title: 最长公共子序列
tags: 动态规划
categories: algorithm
---
# 题目描述
已知两个序列，求两个序列中最长的公共子序列
# 思路
动态规划的特点是具有**最优子结构**和**重叠子问题**<br/>
对于这个问题，我们定义z[k]是x[m] y[n]的最长子序列
* 若x[m]==y[n], z[k-1]是x[m-1] y[n-1]的最长子序列
* 若x[m]!=y[n], z[k]是 {x[m],y[n-1]} {x[m-1],y[n-1]} 中最长的公共子序列

<!--more-->
# 代码实现
先定义一下数据名称
```C++
int x[] = {1, 2, 3, 2, 4, 1, 2};
int y[] = {2, 4, 3, 1, 2, 1};
int temp[100][100] = {0};
```
## 递归实现
```C++
int algo(int m, int n)
{
    if (temp[m][n] != -1)
        return temp[m][n];
    if (m == -1 || n == -1)
        return 0;
    if (x[m] == y[n])
        return temp[m][n] = algo(m - 1, n - 1) + 1;
    else
        return temp[m][n] = max(algo(m - 1, n), algo(m, n - 1));
}
```
## 迭代实现
```C++
int algo(int m, int n)
{
    temp[0][0] = 0;
    for(int i =1;i<=m,++i) temp[i][0]=0;
    for(int i =1;i<=n,++i) temp[0][i]=0;
    for(int i =1;i<=m;++i)
        for(int j =1;j<=n;++j)
            if(x[i]==y[j])
                temp[i][j]=temp[i-1][j-1]+1;
            else
                temp[i][j]=max(temp[i-1][j],temp[i][j-1])
    return temp[m][n];
}
```