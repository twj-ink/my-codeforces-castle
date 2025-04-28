# Educational Codeforces Round 178(Div.2)

https://codeforces.com/contest/2104

战绩：AC4 (A,B,C,D)

## A: Three Decks

简单数学题，首先用c把a和b之间的差值补齐，然后判断一下c比b的多余值模3是否为0，如果为0说明c多余的部分可以均分，否则答案就是NO。

```python
for _ in range(int(input())):
    a,b,c=map(int,input().split())
    dif=b-a
    c-=dif
    dif2=c-b
    if dif2>=0 and dif2%3==0:
        print('YES')
    else:
        print('NO')
```

## B: Move to the End

数据结构题，考察最小堆heap。对k倒着从n减为1考虑比较简单：

当k取n时答案就是sum(s)，因为所有的n个元素都是必须取的；

随着k减小，每次答案的必须取的元素会逐个从开头部分减少，比如k=n-1时，后n-2个元素必须取，前两个元素可以调整位置（显然要把小的放第一位，即把前两位的最大值放到第二位）；
k=n-2时，后n-3个元素必须取，然后把前三位的元素的最大值放到第二位；以此类推。

这样，只需要一开始计算sum和，然后每次k减小时，把开头的元素放入堆中，并且每次都将sum减去堆中的最小元素并弹出，即可倒序模拟整个过程。

因为堆结构可以保证每次取heap[0]都是当前最小值，随着后缀必须取的数量减少，为了保证每个答案最大，最好的策略就是在每次更新的时候减去前方灵活元素的最小值（其实也就是一直保留前方n-k个元素的最大值，然后加到ans中）


```python
from heapq import heappop,heappush

for _ in range(int(input())):
    n=int(input())
    s=list(map(int,input().split()))
    heap=[s[0]]
    ans=[]
    total=sum(s)
    ans.append(total)
    for i in range(n-1):
        heappush(heap,s[i+1])
        total-=heap[0]
        heappop(heap)
        ans.append(total)
    print(*ans[::-1])
```


## C: Card Game

噩梦博弈题，不过这个博弈还算比较简单和好想的，同时这个博弈规则的1>n有点而两拨千金的感觉哈哈。。

我的思路是这样的：由于题目说到了1>n这个特殊比较规则，那就把1和n这两个特殊位置拿出来分一下类，由于每个序列都有A和B的出现，简单起见考虑A和B的出现是连续的。

**情况一**：只有最后一个字符是B，前方都是A（AAA...AAB）。这样Alice只需要出第一张即可获胜（Alice WIN）；

**情况二**：除了最后一个字符是B，倒数若干个字符都是B，前方都是A（AAA...AABB..B）。这样A取哪一个都会被B压制（Bob WIN）；

**情况三**：只有最后一个字符是A，前方都是B（BBB...BBA）。这样Alice只能出最后一张，Bob出第一张即可（Bob WIN）；

**情况四**：除了最后一个字符是A，倒数若干个字符都是A，前方都是B（BBB...BBAA..A）。A随便取都能赢，除了最后一张（Alice WIN）；

接着继续对四种情况深入考虑：前方都是A的子串中如果穿插了B会怎么样？在情况一和二中，会发现不管A出那张牌，总有B比它大（Bob WIN）；

前方都是B的子串中如果穿插了A会怎么样？在情况三和四中，同理得知，（Bob WIN）

综上，Alice获胜只有两种情况：要么是B只有一张且在最后的位置；要么是A有多张且连续排在最后的位置。那么只需要判断这两个条件是否成立即可。

```python
for _ in range(int(input())):
    n=int(input())
    s=input()
    if s.count('B')==1 and s[-1]=='B':
        print('Alice')
    elif s.count('A')>1:
        if s[-2:]=='AA' or s[0]==s[-1]=='A':
            print('Alice')
        else:
            print('Bob')
    else:
        print('Bob')
```


## D: Array and GCD

**欧拉筛宣传片**，直接现场敲一个欧拉筛，嘎嘎自信（，代码中加了注释方便查阅。

首先考虑一个长度为n的数组能否变为ideal。由于硬币个数必须大于等于0且初始为0，那么可以无限制地对数组的元素进行减1操作，且无需保证加1次数与减1一致（可以减，可以不加）。

那么在若干次加减操作后，数组的和的范围实际上为：

$$ 2 \cdot n \leq \sum_{new} s \leq \sum_{old} s $$

那为了保证数组的两两元素之间gcd均为0，再考虑到数组的和可以随意缩小，可以得出判断：

只要 **原数组的和** 大于等于 **长度为n的、从2开始的质数数组的和** ，就可以将其调整为这样的质数数组。

如果小于，说明无法满足条件，只能删除一个值来缩小n的大小，使得质数前缀和的界限bound降低，同时为了保证能尽快满足不等式，每次删除的都是s中的最小值。

因此需要进行两个操作：一是预处理出足够数量的质数数组，并计算出前缀和；二是将原数组进行倒序排序，每次删除从尾端弹出的值。

```python
def euler_sieve(n):
    primes = []
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False  # 0和1不是质数

    for i in range(2, n + 1):
        if is_prime[i]:
            primes.append(i)  # i 是质数
        for prime in primes:
            if i * prime > n:
                break
            is_prime[i * prime] = False
            # 如果 prime 是 i 的最小质因数，停止继续筛选
            if i % prime == 0:
                break

    return primes

MAX=10**7
primes=euler_sieve(MAX+1)
l=len(primes)
pre=[0]*(l+1)
for i in range(1,l):
    pre[i]=primes[i-1]+pre[i-1]
for _ in range(int(input())):
    n=int(input())
    s=list(map(int,input().split()))
    curr=sum(s)
    ss=sorted(s,reverse=True)
    bound=pre[n]
    ans=0
    while bound>curr and n>=1 and ss:
        # print((upper,curr))
        n-=1
        ans+=1
        curr-=ss.pop()
        bound=pre[n]
    print(ans)
```

## E
## F
## G

Github链接：