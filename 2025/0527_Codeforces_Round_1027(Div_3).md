# Codeforces Round 1027(Div.3)

战绩：AC5（ABCDE），开心

url:

https://codeforces.com/contest/2114

## A - Square Year

Complementary

```python
def doit():
    n = int(input())
    if int(n**0.5)!=n**0.5:
        print(-1)
        return
    print(0,int(n**0.5))
for _ in range(int(input())):
    doit()

```

## B - Not Quite a Palindromic String

Complementary

找1和0的个数并保留二者个数的maxv和minv。能形成的最大匹配数目肯定是各自向下整除2；最小匹配数目则是：把minv先用maxv全部匹配掉，剩下的maxv自己向下整除2。同时，求得最大最小匹配数目后，中间的可取的数值**奇偶性一定与最值相同**，因为如果互换某两个值的位置，造成的后果肯定要么是匹配数不变，要么同时变。

```python
from collections import Counter

def doit():
    n,k=map(int,input().split())
    s=input()
    c=Counter(s)
    c1,c0=c['1'],c['0']
    maxv,minv=max(c1,c0),min(c1,c0)
    minvv = (maxv-minv)//2
    if minvv>k:
        print('no')
        return
    maxvv = (maxv//2+minv//2)
    if maxvv<k:
        print('no')
        return
    if k%2!=maxvv%2:
        print('no')
        return
    print('yes')

for _ in range(int(input())):
    doit()

```

## C - Need More Arrays

Dynamix programming, Binary search

模板是最长不降子序列，这个题要求是最长相差大于1的子序列。如果直接两层循环更新dp会超时，考虑对于每个位置，二分查找恰好比其小2的值的位置。如果真的找到了这个值，或者没有这个值导致j=i，那么都是可以对先前的dp值加1的；否则只能乖乖的取先前的dp值。

```python
from collections import Counter
from bisect import bisect_left,bisect_right

def doit():
    n=int(input())
    s=list(map(int,input().split()))

    dp=[0]*n
    dp[0]=1
    for i in range(1,n):
        j=bisect_left(s,s[i]-2)
            # if s[i]-s[j]>1:
        if s[i]-s[j]>1 or i==j:
            dp[i]=max(dp[i],dp[j]+1,dp[j-1]+1)
        else:
            dp[i]=max(dp[i],dp[j])
        # print(f'dp{i}={dp[i]}')
    print(dp[-1])

for _ in range(int(input())):
    doit()
```

## D - Come a Little Closer

Complementary

纯模拟屎山代码。

四次移动，分别是x最小、x最大、y最小、y最大。以第一个举例，在代码的开始得到两个分别以x坐标和y坐标升序的数组sx和sy，先假设把x最小的点删掉，那么此时矩形的length_of_x(lx)就是固定的：

$$ lx = sx[-1][0] - sx[1][0] $$，

此时只需要考虑这个删掉的点的y值是不是边界的点即可。

1. `miny<dy(deleted_y)<maxy`

此时y的上下边界不受影响，直接做差更新面积。

2. `dy==miny`

此时只需要取第二小的y值即可，即sy[1][1].

3. `dy==maxy`

与2中类似。

在更新完之后，考虑到有可能删掉的这个点没法移到新矩形区域内，额外判断一下面积是不是恰好是n-1，如果是说明没法把删掉的点填进去，那就只能把面积多加一条长或者宽；如果不是说明可以放进去，就和直接删掉一样了。

```python
from collections import Counter,defaultdict
from bisect import bisect_left,bisect_right

def doit():
    n=int(input())
    s=[]
    for _ in range(n):
        x,y=map(int,input().split())
        s.append((x,y))
    if n==1:
        print(1)
        return

    sx=sorted(s,key=lambda x:x[0])
    sy=sorted(s,key=lambda x:x[1])
    maxx,minx,maxy,miny = sx[-1][0],sx[0][0],sy[-1][0],sy[0][0]
    anss=ans=float('inf')
    ans=min(ans,(sx[-1][0]-sx[0][0]+1)*(maxy-miny+1))
    anss=min(ans,anss)

    #sx[0]
    lx=sx[-1][0]-sx[1][0]+1
    dy=sx[0][1]
    if miny<dy<maxy:
        ans = min(ans, curr:=(lx * (maxy - miny+1)))
    elif dy==miny:
            ans = min(ans, curr:=(lx * (maxy - sy[1][1]+1)))
    elif dy==maxy:
            ans = min(ans, curr:=(lx * (sy[-2][1] - miny+1)))
    currly=curr//lx
    if curr==n-1:
        ans+=min(lx,currly)
    anss=min(anss,ans)

    #sx[-1]
    lx=sx[-2][0]-sx[0][0]+1
    dy=sx[-1][1]
    if miny<dy<maxy:
        ans = min(ans, curr := (lx * (maxy - miny + 1)))
    elif dy == miny:
        ans = min(ans, curr := (lx * (maxy - sy[1][1] + 1)))
    elif dy == maxy:
        ans = min(ans, curr := (lx * (sy[-2][1] - miny + 1)))
    currly=curr//lx
    if curr==n-1:
        ans+=min(lx,currly)
    anss=min(anss,ans)

    #sy[0]
    ly=sy[-1][1]-sy[1][1]+1
    dx=sy[0][0]
    if minx<dx<maxx:
        ans = min(ans, curr:=(ly * (maxx - minx+1)))
    elif dx==minx:
            ans = min(ans, curr:=(ly * (maxx - sx[1][0]+1)))
    elif dx==maxx:
            ans = min(ans, curr:=(ly * (sx[-2][0] - minx+1)))
    currlx=curr//ly
    if curr==n-1:
        ans+=min(ly,currlx)
    anss=min(anss,ans)

    #sy[-1]
    ly=sy[-2][1]-sy[0][1]+1
    dx=sy[-1][0]
    if minx<dx<maxx:
        ans = min(ans, curr:=(ly * (maxx - minx+1)))
    elif dx==minx:
            ans = min(ans, curr:=(ly * (maxx - sx[1][0]+1)))
    elif dx==maxx:
            ans = min(ans, curr:=(ly * (sx[-2][0] - minx+1)))
    currlx=curr//ly
    if curr==n-1:
        ans+=min(ly,currlx)
    anss=min(anss,ans)

    print(anss)

for _ in range(int(input())):
    doit()


```

## E - Kirei Attacks the Estate

Dynamic programming

根节点选自己；第一次子节点也选自己；剩下的：

$$ dp[v] = max(s[v], s[v] - s[u] + dp[father[u]]) $$，

即要么选自己，要么把父节点选上同时把父节点的父节点的最大值dp[father[u]]也选上。重点维护树的结构和构建father数组。

```python
from collections import deque

for _ in range(int(input())):
    n=int(input())
    s=[0]+list(map(int,input().split()))
    g=[[] for _ in range(n+1)]
    for _ in range(n-1):
        u,v=map(int,input().split())
        g[u].append(v)
        g[v].append(u)
    vis=[False]*(n+1)
    fat=[0]*(n+1)
    dp=[0]*(n+1)
    dp[1]=s[1]
    q=deque([1])
    while q:
        u=q.popleft()
        if vis[u]:
            continue
        vis[u]=True
        for v in g[u]:
            if vis[v]:
                continue
            fat[v]=u
            if u==1:
                dp[v]=s[v]
            else:
                dp[v]=max(s[v],s[v]-s[u]+dp[fat[u]])
            q.append(v)
    print(*dp[1:])
```