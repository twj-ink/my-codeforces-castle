# Codeforces Round 1022(Div.2)

https://codeforces.com/contest/2108

战绩：AC0 (难绷

A题瞪眼瞪了半天居然没想出来怎么做，然后就洗洗睡了。。。以下思路均是tutorial的中文+个人理解版。

## A

For a permutation p of length n∗, we define the function:

$$ f(p)=\sum_{i=1}^{n}|p_i−i| $$

You are given a number n. You need to compute how many distinct values the function f(p) can take when considering all possible permutations of the numbers from 1 to n.

直觉告诉我，**当p为从n递减到1的逆序排列时，这个绝对值的和最大**，从偏移量的角度可能行得通（？但是我不知道怎么解释）。

然后考虑如何得到这个逆序排列：一开始是从1到n的排列，将n不断地与其前一位的数字互换位置（与n-1、n-2、...），直到变为第一位，在这个过程中
每互换一次就会导致绝对值的和式增大2；接着将最后的一位的n-1不断地与其前一位的数字互换位置，直到互换到第二位（此时为`[n,n-1,1,2,...,n-3]`）；
不断重复直到得到完全逆序排列。

首先计算一下这个逆序排序对应的f(p)，然后再说明一下为什么这个值是绝对值和所能取得到的最大值（直观理解）。

首先，$ p_i = n-i+1 $，则和式即为：

$$ f(p_{rev})=\sum_{i=1}^{n}|n-2i+1| $$

一，n=2k，此时i取值为[1,2k]，i取1时项为2k-1，i取k时项为1，i取k+1到2k的部分与前方对称，则和为：

$$ f(p_{rev})=2 \cdot \sum_{j=1}^{k}(2j-1)=2 \cdot (1+3+5+...+(2k-1))=2 \cdot k^2 $$

带入n，和即为 

$$ \dfrac{n^2}{2} $$ 。

二，n=2k+1，此时i取值为[1,2k+1]，但是当i=k时项为0，所以和式的值与一中一致（2乘k^2），
带入n，和即为 

$$ 2 \cdot \lfloor \dfrac{n}{2} \rfloor^2 = \lfloor \dfrac{n^2}{2} \rfloor $$

故和即为 

$$ \lfloor \dfrac{n^2}{2} \rfloor $$

然后考虑到形成这个排列的过程中是采取元素两两相邻互换的方式，则能够取到0到这个最大值之间所有
的偶数值（包含0），因此答案就是 

$$ ans = \lfloor \dfrac{\lfloor \dfrac{n^2}{2} \rfloor}{2} \rfloor +1=\lfloor \dfrac{n^2}{4} \rfloor+1 $$

下面给出个人的直观理解，**为什么能取到所有区间中的偶数值**？

在每次将最后一个元素前移的过程中，剩下的元素都在整体右移，也就是整体偏移其原先的索引位置，导致绝对值的和是在不断+2的；而
对于那个正在前移的元素，有两种情况：一是在向其对应的索引位置靠拢（如对n-1前移的第一次操作，从索引n移到了n-1，是在靠拢），这样使得绝对值的和保持不变（一个+1一个-1），
二是也在向对应的索引位置远离，这样每移一次也在+2。所以总体来看，整个移动的过程只会使和式+2，或者不变，因此找到了最大值，答案所求的个数就是从0到最大值之间的偶数数目。

![https://github.com/twj-ink/my-codeforces-castle/blob/main/img/CR1022A.jpg](https://github.com/twj-ink/my-codeforces-castle/blob/main/img/CR1022A.jpg)

下面给出代码：

```python
for _ in range(int(input())):
    n = int(input())
    print((n ** n) // 4 + 1)
```

## B

考察异或的位运算，发现cf上好多这种题目，这道题让我联想到了我上大学以来第一次打cf上竞赛的一道题：

https://codeforces.com/contest/2020/problem/C

同样也是位运算的题目。

经验：既然是位运算，转为二进制之后运算只会在当前位进行，**不要考虑什么进位退位之类的了**（当时因为没想明白这个没敢做）。

关于异或的一些经验：

1. 异或自己置为0；与1异或是开关
2. 若干个01异或，结果取决于1的个数：奇数个1结果就是1；偶数个1结果就是0

这道题就要用到2。根据题解，要对n和**x二进制中1的个数**的相对大小分类：

![https://github.com/twj-ink/my-codeforces-castle/blob/main/img/CR1022B.jpg](https://github.com/twj-ink/my-codeforces-castle/blob/main/img/CR1022B.jpg)

```python
for _ in range(int(input())):
    n,x=map(int,input().split())
    c=sum(1 for i in bin(x)[2:] if i=='1')
    if x==0:
        if n==1:
            print(-1)
        elif n%2==0:
            print(n)
        else:
            print(n+3)
    elif x==1:
        if n%2==0:
            print(n+3)
        else:
            print(n)
    else:
        print(x + (max(0, n - c) + 1) // 2 * 2)
```