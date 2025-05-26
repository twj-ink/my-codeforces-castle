# Codeforces Round 1026(Div.2)

url:

https://codeforces.com/contest/2110

## A: Fashionable Array

问题即：求至少需要删除多少元素才能使

$$ (min(a) + max(a)) % 2 = 0 $$

也就是最大最小值奇偶性相同。首先排序检查，如果不相等，就分别从前往后找第一个与最大值奇偶性相同元素的idx；和从后往前找第一个与最小值奇偶性相同元素的idx，比较较小值。

```cpp
#include <bits/stdc++.h>

using namespace std;

void solve() {
    int n;
    cin >> n;
    vector<int> s(n);
    for (int i = 0; i < n; i++) {
        cin >> s[i];
    }
    sort(s.begin(), s.end());
    if (s[0] % 2 == s[n-1] % 2) {
        cout << 0 << endl;
        return;
    }

    int left = n, right = n;
    for (int i = 1; i < n; i++) {
        if (s[i] % 2 != s[0] % 2) {
            left = i;
            break;
        }
    }

    for (int i = 1; i < n; i++) {
        if (s[n - i - 1] % 2 != s[n - 1] % 2) {
            right = i;
            break;
        }
    }

    cout << min(left, right) << endl;
    return;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);

    int t;
    cin >> t;
    while (t--) {
        solve();
    }

    return 0;
}
```

## B: Down with Brackets

考虑题目说的：

> 2. If the bracket sequence A is balanced, then (A) is also balanced.
> 
> 3. If the bracket sequences A and B are balanced, then the concatenated sequence AB is also balanced.

对于整体的s，如果是2类型的，不管移除哪一对括号，剩下的总能形成正确的序列；而如果是3类型的，只需要移除最左边和右边的一对即可，因为它们对于的另一个括号必定是背靠背的。

```cpp
#include <bits/stdc++.h>

using namespace std;

void solve() {
    string s;
    cin >> s;
    int cnt = 0;
    for (int i = 0; i < s.size(); i++) {
        if (i != 0 && cnt == 0) {
            cout << "YES" << endl;
            return;
        }
        if (s[i] == '(') {
            cnt ++;
        } else {
            cnt --;
        }
    }
    cout << "NO" << endl;
}


int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);

    int t;
    cin >> t;
    while (t--) {
        solve();
    }

    return 0;
}

```

## C: Racing

Constructive algorithm

题目要求能否构建出这样的一个序列使得满足条件。首先对于题目给的每个位置的高度限制和部分高度差序列，可以利用dp的思维构建出 **每一个位置的可能高度范围** ，并保证每个位置的最高点的的确确不低于最低点。

如果真的能构建出这样的dp序列，就说明对于最后那个位置的各个可能最终高度，都会存在一条路径到达。那么便可以随意取最后一个位置的一个可能高度，反向构建出答案序列。

也即：

***如果能构建出每个位置的可能高度区间，那么从最后一个位置任取一个合法高度，便可以反向构造出一条合法路径。***

```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n;
    cin >> n;
    vector<int> d(n + 1);
    for (int i = 1; i <= n; i++) {
        cin >> d[i];
    }

    vector<int> L(n + 1), R(n + 1);
    bool ok = true;
    for (int i = 1; i <= n; i++) {
        int l, r;
        cin >> l >> r;
        if (d[i] == 0) {
            L[i] = L[i - 1];
            R[i] = R[i - 1];
        } else if (d[i] == 1) {
            L[i] = L[i - 1] + 1;
            R[i] = R[i - 1] + 1;
        } else {
            L[i] = L[i - 1];
            R[i] = R[i - 1] + 1;
        }

        L[i] = max(L[i], l);
        R[i] = min(R[i], r);

        if (L[i] > R[i]) {
            ok = false;
        }
    }

    if (!ok) {
        cout << -1 << endl;
        return;
    }

    // 反向构建
    vector<int> h(n + 1, 0);
    h[n] = L[n];
    vector<int> ans(n, 0);
    bool valid = true;

    for (int i = n; i > 0; i--) {
        if (d[i] == 0) {
            h[i - 1] = h[i];
        } else if (d[i] == 1) {
            h[i - 1] = h[i] - 1;
        } else {
            // 先尝试不升高
            if (L[i-1] <= h[i] && h[i] <= R[i - 1]) {
                h[i - 1] = h[i];
            } else {
                h[i - 1] = h[i] - 1;
            }
        }
        // 检查h[i-1]位置是否越界
        if (L[i - 1] > h[i - 1] || h[i - 1] > R[i - 1]) {
            valid = false;
            break;
        }
        ans[i - 1] = h[i] - h[i - 1];
    }

    if (!valid) {
        cout << -1 << endl;
    } else {
        for (int i : ans) {
            cout << i << " ";
        }
        cout << endl;
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);

    int t;
    cin >> t;
    while (t--) {
        solve();
    }
    return 0;
}

```

## D: Fewer Batteries

Binary search

题目要求最终的最小电池数目，电池最大为1e9，考虑直接对最终电池数目进行二分。而能否取得的条件是：在该电池数目的情况下能否从1位置走到n位置。

在走的过程中利用dp，dp[i]表示到达i位置时的最大电池数目，一方面如果该位置有电池尽量拿，另一方面保证每一个dp值都小于等于每次二分的尝试电池数目。这样的目的是使得在走的过程中尽快达到当前尝试的电池数目。

优化：题目说了m条路保证 

$$ u < v $$，

即只会有从小到大的单向边，那么在遍历图的时候就不需要进行n次松弛（bellman）或者利用双端队列（spfa）进行了，只需要松弛一次，从小到大遍历一次节点即可。（否则会爆内存）

```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n, m;
    cin >> n >> m;
    vector<int> b(n + 1);
    for (int i = 1; i <= n; i++) {
        cin >> b[i];
    }
    vector<vector<pair<int, int>>> g(n + 1);

    for (int i = 0; i < m; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        g[u].push_back(make_pair(v, w));
    }

    auto can_reach = [&](int mid) {
        vector<int> dp(n + 1, -1);
        dp[1] = min(b[1], mid);
        // queue<int> q;
        // q.push(1);

        for (int u = 1; u <= n; u++) {
            // int u = q.front(); q.pop();
            // if (u == n) {
            //     return true;
            // }
            for (auto [v, w] : g[u]) {
                if (dp[u] >= w && mid >= w) {
                    int neww = dp[u] + b[v];
                    if (neww >= mid) {
                        neww = mid;
                    }
                    if (neww >= dp[v]) {
                        dp[v] = neww;
                        // q.push(v);
                    }
                }
            }
        }
        return (dp[n] != -1);
    };

    int lo = 0, hi = 1e9;
    int ans = -1;

    while (lo <= hi) {
        int mid = (lo + hi) / 2;
        if (can_reach(mid)) {
            ans = mid;
            hi = mid - 1;
        } else {
            lo = mid + 1;
        }
    }

    cout << ans << endl;
    
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);

    int t;
    cin >> t;
    while (t--) {
        solve();
    }

    return 0;
}
```