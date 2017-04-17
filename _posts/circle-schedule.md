---
title: 循环赛日程安排
---
# 题目描述
有n名选手进行比赛，每名选手每天只进行一场比赛。求出一种有效的安排算法，使得当n为奇数时，能在n天比完，当n为偶数时，在n-1天比完。
# 思路分析
* 当n为2的指数幂时，该问题利用分治法可以非常简单的解决
每次将n的规模除以2，直到n=2时，日程安排如上图所示。然后将该安排平移到其对角线位置即可。至于右边和下边的安排也很简单，只需要将现在的安排方式都加上n即可。
* 当n不为2的指数幂时，意味着在进行n/2的分治过程中，必然会遇到n为奇数的情况。这时，像之前一样简单的分治方法无法继续发挥作用，这意味着我们必须要找到能在分治过程中对n为奇数的情况进行处理的算法。

<!--more-->

# 解决思路

# 代码
```C++
#include <cstdio>
#include <cstring>

int sche[1100][1100];

void devide(int n)
{
    if (n == 1)
    {
        sche[1][1] = 1;
        return;
    }
    //当n为奇数时，将其+1转变成偶数
    if (n & 1)
        n++;
    int t = n / 2;
    //将[1:t][1:t]的矩阵排列好
    devide(t);

    //利用排列好的[1:t][1:t]对更大的范围进行合并
    for (int i = 1; i <= t; i++)
        for (int j = 1; j <= t + 1; j++)
        {
            //当前位置为第i位选手对阵第(n/2)+1位选手时-->将其位置用其他选手填充
            //此时n为奇数
            if (sche[i][j] > t)
            {
                //根据实验报告的讨论，i和i+t选手当天都没有比赛
                //所以让他们两个进行比赛
                sche[i + t][j] = i;
                sche[i][j] = i + t;

                /**
                 * 以下代码是实验报告中当n/2为奇数时，将安排完成的[1:n][t]的前
                 * [1:t]列交叉复制到后[n-t+1:n]列中的代码
                 */
                int c = i + t + 1;
                //当t是奇数时，进入该循环，安排[t+2:n]列的比赛日程
                for (int k = t + 2; (t & 1) && k <= n; k++, c++)
                {
                    //此时该选手已经被安排过，c++
                    if (c == i + t)
                        c++;
                    //若c大于n，从头开始循环，保证每一名选手都被安排
                    if (c > n)
                        c -= n / 2;
                    //开始复制
                    sche[i][k] = c;
                    sche[c][k] = i;
                }
            }
            //当前比赛的对手在1~t之间
            else
            {
                //将安排结果+t复制到[t:n][1:t]
                sche[i + t][j] = sche[i][j] + t;

                //当n是偶数或者t是一时，将结果复制到
                //[1:t][t:n]和[t:n][t:n]
                if (n % 2 == 0 || t == 1)
                {
                    sche[i + t][j + t] = sche[i][j];
                    sche[i][j + t] = sche[i + t][j];
                }
            }
        }
}

int main()
{
    int n;
    puts("please input the player nums:");
    while (~scanf("%d", &n)) //一直读取直到文件尾
    {
        memset(sche, 0, sizeof(sche)); //清零
        if (n & 1)                     //n是奇数
            devide(n + 1);
        else
            devide(n); //n是偶数
        puts("the schedule are as follow:");
        for (int i = 1; i <= n; i++)
        {
            for (int j = 1; j <= n + (n & 1); j++)
            {
                printf("%2d ", (sche[i][j] > n ? 0 : sche[i][j]));
            }
            putchar('\n');
        }
        puts("please input the player nums:");
    }
}
```
# 参考
[参考资料](http://www.itdadao.com/articles/c15a62194p0.html)


