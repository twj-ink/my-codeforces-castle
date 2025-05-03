# Codeforces Round 1022(Div.2)

https://codeforces.com/contest/2108

战绩：AC0 (难绷

A题瞪眼瞪了半天居然没想出来怎么做，然后就洗洗睡了。。。以下思路均是tutorial的中文+个人理解版。

## A

For a permutation p of length n∗, we define the function:

$$ f(p)=\sum_{i=1}^{n}|p_i−i| $$

You are given a number n. You need to compute how many distinct values the function f(p) can take when considering all possible permutations of the numbers from 1 to n.

直觉告诉我，当p为从n递减到1的逆序排列时，这个绝对值的和最大，从偏移量的角度可能行得通（？但是我不知道怎么解释）。

然后考虑如何得到这个逆序排列：一开始是从1到n的排列，将n不断地与其前一位的数字互换位置（与n-1、n-2、...），直到变为第一位，在这个过程中
每互换一次就会导致绝对值的和式增大2；接着将最后的一位的n-1不断地与其前一位的数字互换位置，直到互换到第二位（此时为`[n,n-1,1,2,...,n-3]`）；
不断重复直到得到完全逆序排列。

首先计算一下这个逆序排序对应的f(p)，然后再说明一下为什么这个值是绝对值和所能取得到的最大值（直观理解）。

首先，$ p_i = n-i+1 $，则和式即为：

$$ f(p_{rev})=\sum_{i=1}^{n}|n-2i+1| $$

一，n=2k，此时i取值为[1,2k]，i取1时项为2k-1，i取k时项为1，i取k+1到2k的部分与前方对称，则和为：

$$ f(p_{rev})=2 \cdot \sum_{j=1}^{k}(2j-1)=2 \cdot (1+3+5+...+(2k-1))=2 \cdot k^2 $$

带入n，和即为 $ \dfrac{n^2}{2} $ 。

二，n=2k+1，此时i取值为[1,2k+1]，但是当i=k时项为0，所以和式的值与一中一致（2乘k^2），
带入n，和即为 $ 2 \cdot \lfloor \dfrac{n}{2} \rfloor^2 = \lfloor \dfrac{n^2}{2} \rfloor $ 。

故和即为 $ \lfloor \dfrac{n^2}{2} \rfloor $ ，然后考虑到形成这个排列的过程中是采取元素两两相邻互换的方式，则能够取到0到这个最大值之间所有
的偶数值（包含0），因此答案就是 

$$ ans = \lfloor \dfrac{\lfloor \dfrac{n^2}{2} \rfloor}{2} \rfloor +1=\lfloor \dfrac{n^2}{4} \rfloor+1 $$

下面给出个人的直观理解，为什么能取到所有区间中的偶数值？

在每次将最后一个元素前移的过程中，剩下的元素都在整体右移，也就是整体偏移其原先的索引位置，导致绝对值的和是在不断+2的；而
对于那个正在前移的元素，有两种情况：一是在向其对应的索引位置靠拢（如对n-1前移的第一次操作，从索引n移到了n-1，是在靠拢），这样使得绝对值的和保持不变（一个+1一个-1），
二是也在向对应的索引位置远离，这样每移一次也在+2。所以总体来看，整个移动的过程只会使和式+2，或者不变，因此找到了最大值，答案所求的个数就是从0到最大值之间的偶数数目。

![https://github.com/twj-ink/my-codeforces-castle/blob/main/img/CR1022A.jpg](https://github.com/twj-ink/my-codeforces-castle/blob/main/img/CR1022A.jpg)



```python
for _ in range(int(input())):
    n=int(input())
    s=input()
    ans=0
    for i in range(n):
        if s[i]=='0':
            ans+=1
        else:
            ans+=n-1
    print(ans)
```

## B

> MEX: The **MEX** of a sequence is defined as the first non-negative integer
> that does not appear in it. For example, MEX(1,3,0,2)=4, and MEX(3,1,2)=0.

为了能够让x出现的次数最多，首先应该让x尽快出现，将0-(x-1)的数字都垫在前面；然后将x的位置空出来留给后面的
元素填，把x放到最后。这样，整体就是先填充0-n，然后如果x<n就把x和n互换。

cpp的`vector<int> p(n); iota(p.begin(), p.end(), 0);` 等价于py的`p=[i for i in range(n)]`。

```python
for _ in range(int(input())):
    n,x=map(int,input().split())
    p=[i for i in range(n)]
    if x<n:
        p[x],p[-1]=p[-1],p[x]
    print(*p)
```

```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n, x;
    cin >> n >> x;
    vector<int> p(n);
    iota(p.begin(), p.end(), 0);
    if (x < n) {
        swap(p[x], p.back());
    }
    for (int i = 0; i < n; i++) {
        cout << p[i] << " \n"[i == n - 1];
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    while (t--) {
        solve();
    }
    return 0;
}
```

## C

可以理解为求给定的两个序列的元素和的可能个数，令为x。

对于数组a，可以列出不等式：
$0 \leq b[i] = x - a[i] \leq k$
，也就是说
 $a[i] \leq x \leq a[i] + k$ ，因此得到x的第一个范围：a数组的最大值到a数组的最小值+k（因为求的是交集）。

对于数组b，可以利用已有的值和
$x = a[i]+b[i]$
来得到x的第二个范围：`a[i]+b[i]`的最小值到其最大值（简单来说
如果存在多个这样的值会导致l>r而使得结果为0）

而x的初始范围为0到2*k，可以两次遍历得到最终的l和r，r-l+1就是答案。

```python
def solve():
    n, k = map(int, input().split())
    a = list(map(int, input().split()))
    b = list(map(int, input().split()))

    l = 0
    r = 2 * k

    for i in range(n):
        if b[i] == -1:
            l = max(l, a[i])
            r = min(r, a[i] + k)
        else:
            x_val = a[i] + b[i]
            l = max(l, x_val)
            r = min(r, x_val)

    print(max(0, r - l + 1))

# 处理多组测试数据
t = int(input())
for _ in range(t):
    solve()
```

```cpp
// Copyed from jiangly, url: https://codeforces.com/contest/2106/submission/316991544

#include <bits/stdc++.h>

using i64 = long long;
using u64 = unsigned long long;
using u32 = unsigned;

using i128 = __int128;
using u128 = unsigned __int128;

void solve() {
    int n, k;
    std::cin >> n >> k;
    
    std::vector<int> a(n), b(n);
    
    int l = 0, r = 2 * k;
    for (int i = 0; i < n; i++) {
        std::cin >> a[i];
        l = std::max(l, a[i]);
        r = std::min(r, a[i] + k);
    }
    
    for (int i = 0; i < n; i++) {
        std::cin >> b[i];
        if (b[i] != -1) {
            l = std::max(l, a[i] + b[i]);
            r = std::min(r, a[i] + b[i]);
        }
    }
    
    std::cout << std::max(0, r - l + 1) << "\n";
}

int main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
    
    int t;
    std::cin >> t;
    
    while (t--) {
        solve();
    }
    
    return 0;
}
```

## D

这题的关键不是暴力尝试插，而是理解**插一朵花=删除一个需求**！

所以思路分两步：

* 首先试一遍不插花，能不能收集成功？

    * 可以的话直接输出 0。

* 否则考虑删掉 b 中的某一朵花，看能不能成功。

    * 怎么快速判定删哪一朵可以？

为了快速判断：

* 定义 forwards_match[i]：收集到 b[0..i] 需要扫描到 a 的哪个位置。

* 定义 backwards_match[i]：从后往前收集 b[i..m-1] 需要从 a 的哪个位置开始。

用 两个指针分别做：

* 一个正着跑，搞出 forwards_match。

* 一个反着跑，搞出 backwards_match。

这样，要删除 b[i] 的条件是：

* 删除 b[i]，只要前面收集到 b[0..i-1] (forwards_match[i-1])的位置在后面收集 b[i+1..m-1] (backwards_match[i+1])的位置之前即可！

即：

`forwards_match[i-1] < backwards_match[i+1]`

才能删掉 b[i]。 （注意边界处理，比如 i=0 或 i=m-1）

```python
def solve(n,m,a,b):
    pre=[0]*m
    i=0
    for j in range(m):
        while i<n and a[i]<b[j]:
            i+=1
        pre[j]=i
        i+=1
    if pre[-1]<n:
        return 0

    suf=[n-1]*m
    i=n-1
    for j in range(m-1,-1,-1):
        while i>=0 and a[i]<b[j]:
            i-=1
        suf[j]=i
        i-=1

    ans=float('inf')
    for pos in range(m):
        match_pre=pre[pos-1] if pos>0 else -1
        match_suf=suf[pos+1] if pos<m-1 else n
        if match_pre<match_suf:
            ans=min(ans,b[pos])
    return ans if ans!=float('inf') else -1

for _ in range(int(input())):
    n,m=map(int,input().split())
    a=list(map(int,input().split()))
    b=list(map(int,input().split()))
    print(solve(n,m,a,b))
```

## E
## F

考虑连通性，如果是0，只会在i位置为1，出现上下两部分的0；如果是1，down接着加1，然后把up变为down，down变为0。

```python
for _ in range(int(input())):
    n=int(input())
    s=input()
    up=0
    down=0
    ans=0
    for i in range(n):
        if s[i]=='0':
            up+=i
            down+=n-i-1
        else:
            ans=max(ans,up,down)
            up=down+1
            down=0
            ans=max(ans,up)
    ans=max(up,down,ans)
    print(ans)
```

## G
## H