# Codeforces Round 1022(Div.2)

url:

https://codeforces.com/contest/2107

## A

贪心策略，如果s中的所有元素都相等，输出no；否则把最大值置为2，其余位置都是1。

证明：考虑全为奇数、全为偶数、有奇有偶。

```python
for _ in range(int(input())):
    n=int(input())
    s=list(map(int,input().split()))
    if len(set(s))==1: print('no')
    else: 
        maxv=max(s)
        idx_max = s.index(maxv)
        ans=[1]*n
        ans[idx_max]=2
        print('yes')
        print(*ans)
```

## B

这个算是比较简单的博弈，只看代码应该就能理解了。

```python
for _ in range(int(input())):
    n,k=map(int,input().split())
    s=list(map(int,input().split()))
    all = sum(s)
    minv,maxv=min(s),max(s)
    cnt_min,cnt_max=s.count(minv),s.count(maxv)

    if maxv-minv>k+1:
        print('Jerry')
    else:
        if maxv-minv-k==1:
            if cnt_max==1:
                print(['Tom','Jerry'][(all-1)%2])
            else:
                print('Jerry')
        else:
            print(['Jerry','Tom'][all%2])

```

## C

