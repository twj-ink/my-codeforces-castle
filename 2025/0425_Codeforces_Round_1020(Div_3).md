# Codeforces Round 1020(Div.3)

https://codeforces.com/contest/2106

战绩：AC3

## A

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

对于数组a，可以列出不等式：$0 \leq b[i] = x - a[i] \leq k$ ，也就是说
 $a[i] \leq x \leq a[i] + k$ ，因此得到x的第一个范围：a数组的最大值到a数组的最小值+k（因为求的是交集）。

对于数组b，可以利用已有的值和$x = a[i]+b[i]$来得到x的第二个范围：`a[i]+b[i]`的最小值到其最大值（简单来说
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
## G
## H