# Codeforces Round 1034(Div.3)

战绩：AC3（ABC）

url:

https://codeforces.com/contest/2123

## A - Blackboard Game

Math, Complementary

```python
def solve():
    n = int(input())
    if (n%4==0):
        print('Bob')
    else:
        print('Alice')
 
t = int(input())
for _ in range(t):
    solve()
```

## B - Tournament

Greedy, Complementary

如果是力量最大者，剩几个人都有可能存活；

否则，如果条件是只剩下一人，一定是力量最大者，也即k>1时是可以存活的。

```python
def solve():
    n,j,k=map(int,input().split())
    a=list(map(int,input().split()))
    max_v=max(a)
    target=a[j-1]
    if max_v==target: print('yes')
    else:
        if k==1: print('no')
        else:print('yes')
t = int(input())
for _ in range(t):
    solve()
```

## C - Prefix Min and Suffix Max

Brute force, Complementary

> In one operation, you may either:
> choose a nonempty prefix∗ of a and replace it with its minimum value, or
> choose a nonempty suffix† of a and replace it with its maximum value.

考虑：

如果该值是最小值，直接取pre[n-1]替代所有元素即可；最大值取suf[0]；因此这俩答案对应的都是1.

如果是卡在中间的值，假设其索引为i，数列中的最小值在其左侧且最大值在其右侧，那么在对0..i-1的元素取前缀、对i+1..n-1的元素取后缀后，形成的这样的状态：

$$ a_{min}, a_i, a_{max} $$

此时不管如何取前后缀，a_i一定会被淹没，答案就是0。更进一步，即使左右并不是极值，只要是能够包夹该值的俩个数字，该数字最后也无法存活。

那如何知道其左右两侧有没有能够包夹它的俩数字呢？只需要查看`pre[i - 1]`和`suf[i + 1]`与当前值`a[i]`的大小关系即可.如果恰好在两者之间，答案就是0.

```python
'''
1 3 5 4 7 2
1 1 1 1 1 1
7 7 7 7 7 2
 
13 10 12 20
13 10 10 10
20 20 20 20
'''
def solve():
    n=int(input())
    a=list(map(int,input().split()))
    pre=[a[0]]+[float('inf')]*(n-1)
    suf=[float('inf')]*(n-1)+[a[-1]]
    for i in range(1,n):
        pre[i]=min(pre[i-1],a[i])
    for i in range(n-2,-1,-1):
        suf[i]=max(suf[i+1],a[i])
    ans=[0]*n
    ans[0]=ans[-1]=1
    for i in range(1,n-1):
        ans[i]=1 if (a[i]>=suf[i+1] or a[i]<=pre[i-1]) else 0
    print(''.join(map(str,ans)))
 
t = int(input())
for _ in range(t):
    solve()
```

---

## D - Binary String Battle

Constructive algorithms, **Games**, Greedy

(Attention is all you need)

给定一个长度为 \(n\) 的二进制串 `s`（只包含 `0`/`1`）和一个整数 `k`（\(1 \le k < n\)）。Alice 与 Bob 轮流操作（Alice 先手）：

- **Alice**：任选一个 **长度恰好为** `k` 的子序列（位置可不连续），将其上的所有字符置为 `0`。  
- **Bob**：任选一个 **长度恰好为** `k` 的子串（必须连续），将其上的所有字符置为 `1`。  

只要在任何时刻（包括 Alice 刚操作后、Bob 操作前）字符串变为全 `0`，Alice 立即获胜；否则如果 Alice 永远无法在有限步内全零，则 Bob 获胜。

Solution：

记 `cnt` 为字符串中 `1` 的总个数：

1. **若 `cnt ≤ k`**  
   Alice 第一回合可一次性选出所有 `1`（最多 `k` 个）置零 ⇒ **Alice 赢**。

2. **否则若 `n < 2·k`**  
   任意长度为 `k` 的连续子串都必包含位置 `k`（1-indexed）。  
   - Alice 固守该位的 `1` 不动，其余恰好 `k` 个 `1` 每回合被她置零；  
   - Bob 的子串无论如何都含该位，只能“补回”至多 `k-1` 个 `1`。  
   故每回合净减少 ≥1 个 `1`，最终被清空 ⇒ **Alice 赢**。

3. **否则（`cnt > k` 且 `n ≥ 2·k`）**  
   Bob 可在 Alice 清掉的 `k` 个 `1` 之外，选一个额外的 `1`，然后在剩余区间内选一个不含此额外 `1` 的长度-`k` 子串全部置 `1`，始终保证“>k 个 `1` 且它们跨度 >k” ⇒ **Bob 赢**。


```python
def solve():
    n,k=map(int,input().split())
    s=input()
    cnt=s.count('1')
    if cnt <= k or n < 2 * k:
        print('Alice')
    else:
        print('Bob')

for _ in range(int(input())):
    solve()
```

## E - MEX Count

Prefix sum & difference array, Greedy

You are given an array $ a $ of size n of non-negative integers.

For all k (0≤k≤n), count the number of possible values of MEX(a) after removing exactly k values from $ a $.

第一步，找出数组a的初始mex，之后删除k个元素后，新的mex值最多只会是初始的值。

第二步，要找出删除k个元素后的可能mex值的个数，可以反过来思考：对于每个可能会产生的mex值，有多少种删除方法来实现？

比如，对于某个新的mex值，通过计算发现，当k取区间l，r中的值时都能实现，那么就可以对长度为(n+1)的数组ans的区间l，r中都加1了，这个ans数组对应的就是要输出的结果。

考虑两个细节：

1. 如何计算对于某个特定的mex对应的k取值区间？
   1. 首先一定要把数组中mex这个数字都删除掉，即$$ k \ge cnt[mex] $$。
   2. 其次不能删除掉太多的元素，至少要保证从0到mex-1这mex个数字都还要存在，也即$$ n - k \ge mex  \rightarrow k \le n - mex$$。
   3. 这样就得到了k的取值范围，只要删除的元素个数在这个范围内，那么当前的mex就有办法得到。
2. 对ans的区间l，r都加1，时间复杂度较高，对于**区间修改**问题可以使用**差分数组**完成，最后的原数组就是差分的前缀和。

```python
from collections import Counter
def solve():
    n=int(input())
    s=list(map(int,input().split()))
    # 1. Find the original MEX
    cnt = Counter(s)
    MEX = 0
    while MEX in cnt:
        MEX += 1
    # 2. Iterate all possible new mex from 0 to MEX
    # and create a difference array with length n + 2.
    # because for k, use 1-based and possible number is n + 1.
    diff = [0] * (n + 2)
    for mex in range(MEX + 1):
        l = cnt[mex]
        r = n - mex
        if l <= r:
            diff[l] += 1
            diff[r + 1] -= 1
    # 3. Get the ans array according to the prefix sum of diff
    ans = [0] * (n + 1)
    for i in range(n + 1):
        ans[i] = (ans[i - 1] if i >= 1 else 0) + diff[i]
    print(*ans)

for _ in range(int(input())):
    solve()
```

> 从 MEX 的可能取值去遍历，而不是从删的个数 k 去遍历，这种反向思考非常巧妙。


## F - Minimize Fixed Points

Constructive algorithms, Number theory

> Call a permutation∗ p of length n good if gcd(pi,i)† >1 for all 2≤i≤n. Find a good permutation with the minimum number of fixed points‡ across all good permutations of length n. If there are multiple such permutations, print any of them.
> > ∗ A permutation of length n is an array that contains every integer from 1 to n exactly once, in any order.
> 
> > †gcd(x,y) denotes the greatest common divisor (GCD) of x and y.
> 
> > ‡A fixed point of a permutation p is an index j (1≤j≤n) such that pj=j.


首先第一位idx=1的时候不做要求，直接把1与其配对；对于剩下的，可以构建一个这样的字典：

$$ {smallest.prime.factor : [a_1, a_2, ...]} $$.

比如：2:[2,4,6]，这样的好处是，可以直接将idx为2，4，6对应的排列数字与这个列表**旋转一次后的自身**一一映射即可，即2-4；4-6；6-2，这样保证了gcd>1。
如果这个质数对应的元素只有一个（自己），那就只能自己与自己映射，也就是不可避免的*fixed point*了。

可以借助线性筛的写法，提前构建出排列各个元素的最小质因数，然后后续构建字典并得到最终排列。

```python
from collections import defaultdict
def solve():
    n = int(input())
    sieve = [0] * (n + 1)
    for i in range(2, n + 1):
        if sieve[i] == 0: # i is a prime
            for j in range(i, n + 1, i):
                sieve[j] = i
    stuff = defaultdict(list)
    for i in range(2, n + 1):
        stuff[sieve[i]].append(i)
    
    perm = [0] * (n + 1)
    perm[1] = 1
    for p, nums in stuff.items():
        L = len(nums)
        for i in range(L):
            perm[nums[i]] = nums[(i + 1) % L]
    print(*perm[1:])
for _ in range(int(input())):
    solve()

```

Github链接：
https://github.com/twj-ink/my-codeforces-castle/blob/main/2025/0701_Codeforces_Round_1034(Div_3).md