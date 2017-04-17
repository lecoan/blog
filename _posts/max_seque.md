---
tiltle 最长公共子序列
---
## 题目描述
已知两个序列，求两个序列中最长的公共子序列
<!--more-->
## 代码实现
```C++
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

int count_ = 0;

int x[] = {1, 2, 3, 2, 4, 1, 2};
int y[] = {2, 4, 3, 1, 2, 1};
int temp[100][100] = {0};
int algo(int m, int n)
{
    if (temp[m][n] != -1)
    {
        return temp[m][n];
    }
    if (m == -1 || n == -1)
    {
        count_++;
        return 0;
    }
    if (x[m] == y[n])
    {
        count_++;
        return temp[m][n] = algo(m - 1, n - 1) + 1;
    }
    else
    {
        count_++;
        return temp[m][n] = max(algo(m - 1, n), algo(m, n - 1));
    }
}

int main()
{
    memset(temp, -1, sizeof(temp));
    printf("%d\n", algo(6, 5));
    printf("%d", count_);
    getchar();
}
```