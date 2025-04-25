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
> that does not appear in it. For example, $ MEX(1,3,0,2)=4 $, and $ MEX(3,1,2)=0 $.

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



## D
## E
## F
## G
## H