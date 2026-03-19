### [1855. Matching on a Tree](https://acm.sjtu.edu.cn/OnlineJudge/problem/1855)

给定一棵 $n$ 个点，$n−1$ 条边的无向树，求树上最大匹配的大小。

* 数据范围：$1 \leq n \leq 5 \times 10 ^ 5$

??? Solution

    任意确定一个点作为根。

    * DP：设 $f[u][0]$ 为处理完 $u$ 子树后，$u$ 不与子节点匹配时的最大匹配数；设 $f[u][1]$ 为处理完 $u$ 子树后，$u$ 已经与某一个子节点匹配时的最大匹配数。

        则有

        $$
        f[u][\mathrm{matched}] =
        \begin{cases}
        \sum_{v \in son(u)} \max(f[v][0], f[v][1]), & \text{if not matched}\\\\
        \max_{v \in son(u)} \left( 1 + f[v][0] + \sum_{w \in son(u), w \neq v} \max(f[w][0], f[w][1]) \right), & \text{if matched}
        \end{cases}
        $$

        也就是说，若 $u$ 不和任何子节点匹配，那么每棵子树各自取最优；若 $u$ 要和某个子节点 $v$ 匹配，就必须让 $v$ 在自己的子树内保持未与子节点匹配的状态。

    * 贪心：反复删除掉一条叶子边和它的两个端点，直到树被删光为止。实现时不需要真的删点，只要在 DFS 回溯时，如果当前点和父节点都还没有被匹配，就立刻匹配。

    ??? info "为什么贪心是对的"
        设 $u$ 是一片叶子，$p$ 是它的父节点。考虑任意一个最大匹配：如果它不包含边 $(u, p)$，由于 $u$ 只有一个邻居，所以 $u$ 一定是未匹配的，那么把那条边换成 $(u, p)$，匹配大小不变。因此，总存在一个最大匹配使用任意一个叶子。

    === "C++: DP"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        const int N = 5e5 + 5;
        vector<int> E[N];
        int f[N][2];

        void dfs(int u, int p) {
            for (int v : E[u]) {
                if (v == p) continue;
                dfs(v, u);
                f[u][1] = max(f[u][1] + max(f[v][0], f[v][1]), f[u][0] + f[v][0] + 1);
                f[u][0] += max(f[v][0], f[v][1]);
            }
        }

        int main() {
            int n;
            cin >> n;
            for (int i = 0; i < n - 1; i++) {
                int u, v;
                cin >> u >> v;
                E[u].push_back(v);
                E[v].push_back(u);
            }
            dfs(1, 0);
            cout << max(f[1][0], f[1][1]) << '\n';
        }
        ```
    === "C++: Greedy"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        const int N = 5e5 + 5;
        vector<int> E[N];
        bool matched[N];
        int ans;

        void dfs(int u, int p) {
            for (int v : E[u]) {
                if (v == p) continue;
                dfs(v, u);
            }
            if (p != 0 && !matched[u] && !matched[p]) {
                matched[u] = true;
                matched[p] = true;
                ans++;
            }
        }

        int main() {
            int n;
            cin >> n;
            for (int i = 0; i < n - 1; i++) {
                int u, v;
                cin >> u >> v;
                E[u].push_back(v);
                E[v].push_back(u);
            }
            dfs(1, 0);
            cout << ans << '\n';
        }
        ```
    === "Python: DP"
        ```python
        n = int(input())
        E = [[] for _ in range(n + 1)]
        for _ in range(n - 1):
            u, v = map(int, input().split())
            E[u].append(v)
            E[v].append(u)

        parent = [0] * (n + 1)
        order = []
        stack = [1]
        parent[1] = -1
        while stack:
            u = stack.pop()
            order.append(u)
            for v in E[u]:
                if v == parent[u]:
                    continue
                parent[v] = u
                stack.append(v)

        f = [[0, 0] for _ in range(n + 1)]
        for u in reversed(order):
            cur0 = 0
            cur1 = 0
            for v in E[u]:
                if v == parent[u]:
                    continue
                cur1 = max(cur1 + max(f[v][0], f[v][1]), cur0 + f[v][0] + 1)
                cur0 = cur0 + max(f[v][0], f[v][1])
            f[u][0] = cur0
            f[u][1] = cur1

        print(max(f[1][0], f[1][1]))
        ```
    === "Python: Greedy"
        ```python
        n = int(input())
        E = [[] for _ in range(n + 1)]
        for _ in range(n - 1):
            u, v = map(int, input().split())
            E[u].append(v)
            E[v].append(u)

        parent = [0] * (n + 1)
        order = []
        stack = [1]
        parent[1] = -1
        while stack:
            u = stack.pop()
            order.append(u)
            for v in E[u]:
                if v == parent[u]:
                    continue
                parent[v] = u
                stack.append(v)

        matched = [False] * (n + 1)
        ans = 0
        for u in reversed(order):
            p = parent[u]
            if p > 0 and not matched[u] and not matched[p]:
                matched[u] = True
                matched[p] = True
                ans += 1

        print(ans)
        ```


### [1428. Minimum Spanning Tree](https://acm.sjtu.edu.cn/OnlineJudge/problem/1428)

给定一张 $n$ 个点，$m$ 条边的有权无向连通图，求最小生成树的边权和。

* 数据范围：$1 \leq n \leq 5000, 1 \leq m \leq 2\times 10 ^ 5$, $1 \leq w_i \leq 10000$

??? Solution

    请见 [Minimum Spanning Tree](mst.md) 章节。下面给出 Kruskal 和 Prim 的两份参考实现。

    === "C++ (Kruskal)"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        const int N = 5005;
        int parent[N];

        int find(int x) {
            if (parent[x] == x) return x;
            parent[x] = find(parent[x]);
            return parent[x];
        }

        bool merge(int a, int b) {
            a = find(a);
            b = find(b);
            if (a == b) return false;
            parent[b] = a;
            return true;
        }

        int main() {
            int n, m;
            cin >> n >> m;
            vector<array <int, 3>> edges;
            edges.reserve(m);
            for (int i = 0; i < m; i++) {
                int u, v, w;
                cin >> u >> v >> w;
                edges.emplace_back(w, u, v);
            }
            for (int i = 1; i <= n; i++) parent[i] = i;
            sort(edges.begin(), edges.end());
            long long ans = 0;
            int cnt = 0;
            for (auto [w, u, v] : edges) {
                if (merge(u, v)) {
                    ans += w;
                    cnt++;
                    if (cnt == n - 1) break;
                }
            }
            cout << ans << '\n';
        }
        ```
    === "C++ (Prim)"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;
        const int INF = 1e9;

        int main() {
            int n, m;
            cin >> n >> m;
            vector <vector <pair <int, int>>> E(n + 1);
            for (int i = 0; i < m; i++) {
                int u, v, w;
                cin >> u >> v >> w;
                E[u].push_back({v, w});
                E[v].push_back({u, w});
            }
            vector <int> key(n + 1, INF);
            vector <int> used(n + 1, 0);
            key[1] = 0;
            long long ans = 0;
            for (int i = 0; i < n; i++) {
                int u = 0;
                for (int j = 1; j <= n; j++)
                    if (!used[j] && key[j] < key[u])
                        u = j;
                if (key[u] == INF) break;
                used[u] = 1;
                ans += key[u];
                for (auto [v, w] : E[u]) {
                    if (!used[v] && w < key[v]) key[v] = w;
                }
            }
            cout << ans << endl;
        }
        ```
    === "Python (Kruskal)"
        ```python
        n, m = map(int, input().split())
        edges = []
        for _ in range(m):
            u, v, w = map(int, input().split())
            edges.append((w, u, v))
        edges.sort()

        parent = list(range(n + 1))

        def find(x):
            root = x
            while parent[root] != root:
                root = parent[root]
            while parent[x] != x:
                nxt = parent[x]
                parent[x] = root
                x = nxt
            return root

        def merge(a, b):
            a = find(a)
            b = find(b)
            if a == b:
                return False
            parent[b] = a
            return True

        ans = 0
        cnt = 0
        for w, u, v in edges:
            if merge(u, v):
                ans += w
                cnt += 1
                if cnt == n - 1:
                    break
        print(ans)
        ```
    === "Python (Prim)"
        ```python
        INF = 10 ** 18
        n, m = map(int, input().split())
        E = [[] for _ in range(n + 1)]
        for _ in range(m):
            u, v, w = map(int, input().split())
            E[u].append((v, w))
            E[v].append((u, w))

        key = [INF] * (n + 1)
        used = [0] * (n + 1)
        key[1] = 0
        ans = 0
        for _ in range(n):
            u = min((i for i in range(1, n + 1) if not used[i]), key=lambda x: key[x])
            if key[u] == INF:
                break
            used[u] = 1
            ans += key[u]
            for v, w in E[u]:
                if not used[v] and w < key[v]:
                    key[v] = w
        print(ans)
        ```


### [1429. Holiday scheduling: episode 1](https://acm.sjtu.edu.cn/OnlineJudge/problem/1429)

你有 $n$ 天的假期，每一天可以选择发呆、花费 $x$ 元出去玩或打工。打工有 $m$ 项任务可选，第 $i$ 项任务占用 $s_i$ 到 $t_i$ 天，完成可获得 $a_i$ 元。不能在某一天同时打两份工。初始有 $0$ 元，目标是在玩的天数尽可能多的情况下，赚尽量多的钱。输出假期结束后剩下的钱。

* 数据范围：$1 \leq n \leq 365, 1 \leq m \leq 10^5, x = 10^9, 1 \leq s_i \leq t_i \leq n, a_i = 1$

??? Solution


    无论如何都不可能真的出去玩。因此，题目就等价于：在所有工作区间里，选择尽可能多个互不重叠的区间。

    ??? info "为什么按结束时间排序是最优的"
        考虑某一步可选的所有工作。若一个最优解在这一步没有选结束最早的可行工作，而是选了一个结束更晚的工作，那么把它替换成结束更早的那一份之后，当前选择的工作数不会变少，且仍然可行。因此总存在一个最优解，其第一步就是选当前结束最早的可行区间。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        int main() {
            int n, m, x;
            cin >> n >> m >> x;
            vector<pair<int, int>> jobs;
            for (int i = 0; i < m; i++) {
                int l, r, a;
                cin >> l >> r >> a;
                jobs.push_back({r, l});
            }
            sort(jobs.begin(), jobs.end());
            int last = -1, ans = 0;
            for (auto [r, l] : jobs) {
                if (l > last)
                    last = r;
                    ans++;
                }
            }
            cout << ans << '\n';
        }
        ```
    === "Python"
        ```python
        n, m, x = map(int, input().split())
        jobs = []
        for _ in range(m):
            l, r, a = map(int, input().split())
            jobs.append((r, l))
        jobs.sort()

        last = -1
        ans = 0
        for r, l in jobs:
            if l > last:
                last = r
                ans += 1
        print(ans)
        ```


### [1430. Holiday scheduling: episode 2](https://acm.sjtu.edu.cn/OnlineJudge/problem/1430)

你有 $n$ 天的假期，每天可以选择发呆、花费 $x$ 元出去玩或打工。打工有 $m$ 项任务可选，第 $i$ 项任务占用 $s_i$ 到 $t_i$ 天，完成可获得 $a_i$ 元。目标是在玩的天数尽可能多的情况下，赚尽量多的钱。输出假期结束后剩下的钱。

* 数据范围：$1 \leq n \leq 365, 1\leq m \leq 10^5, 1 \leq x \leq 10^9, 1 \leq s_i \leq t_i \leq n, 1 \leq a_i \leq 10^9$

??? Solution

    与 episode 1 不同，这道题 $x$ 和 $a_i$ 都是一般值，所以真的需要在玩和打工之间做权衡。

    设 $f[i][j]$ 为第 $i$ 天结束后、玩了 $j$ 天时的最大剩余金钱。转移时枚举第 $i + 1$ 天的选择：

    * 发呆：$f[i + 1][j] \leftarrow f[i][j]$
    * 出去玩：$f[i + 1][j + 1] \leftarrow f[i][j] - x$
    * 接某份从第 $i + 1$ 天开始的工作：$f[r][j] \leftarrow f[i][j] + a$

    最后在所有 $f[n][j] \geq 0$ 中选择最大的 $j$，输出对应的 $f[n][j]$。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;
        typedef long long LL;

        int main() {
            int n, m, x;
            cin >> n >> m >> x;
            vector <vector <pair <int, int>>> jobs(n + 1);
            for (int i = 0; i < m; i++) {
                int l, r, a;
                cin >> l >> r >> a;
                jobs[l].push_back({r, a});
            }

            vector f(n + 1, vector (n + 1, (LL)-1e18));
            auto update = [&](auto &u, auto v) {
                u = max(u, v);
            };
            f[0][0] = 0;
            for (int i = 0; i < n; i++) {
                for (int j = 0; j <= i; j++) {
                    if (f[i][j] < 0) continue;
                    update(f[i + 1][j], f[i][j]);
                    update(f[i + 1][j + 1], f[i][j] - x);
                    for (auto [r, a] : jobs[i + 1]) {
                        update(f[r][j], f[i][j] + a);
                    }
                }
            }

            for (int j = 0; ; j++) {
                if (j == n || f[n][j + 1] < 0) {
                    cout << f[n][j] << '\n';
                    break;
                }
            }
        }
        ```
    === "Python"
        ```python
        n, m, x = map(int, input().split())
        jobs = [[] for _ in range(n + 1)]
        for _ in range(m):
            l, r, a = map(int, input().split())
            jobs[l].append((r, a))

        neg = -(1 << 60)
        f = [[neg] * (n + 1) for _ in range(n + 1)]
        f[0][0] = 0
        for i in range(n):
            for j in range(i + 1):
                if f[i][j] < 0:
                    continue
                f[i + 1][j] = max(f[i + 1][j], f[i][j])
                f[i + 1][j + 1] = max(f[i + 1][j + 1], f[i][j] - x)
                for r, a in jobs[i + 1]:
                    f[r][j] = max(f[r][j], f[i][j] + a)

        for j in range(n + 1):
            if j == n or f[n][j + 1] < 0:
                print(f[n][j])
                break
        ```


### [2535. Array Game](https://acm.sjtu.edu.cn/OnlineJudge/problem/2535)

这是一道交互题。有一列 $n$ 个数 $a_1, a_2, \dots, a_n$。Alice 和 Bob 每次能从头或者尾取走一个数，Alice 先手。双方的最终收益为取走的数之和。你是 Bob，请计算在 Alice 采取最优策略下你能获得的最大收益。

* 数据范围：$2 \leq n \leq 100$, $0 \leq a_i \leq 100$

??? Solution

    先忽略交互部分，只看从区间两端取数的博弈本身。设 $dp[l][r]$ 表示当前轮到操作的人面对区间 $[l, r]$ 时，自己最终总和减去对手最终总和的最大值。那么：

    * 如果取左端，得到的分差是 $a_l - dp[l + 1][r]$；
    * 如果取右端，得到的分差是 $a_r - dp[l][r - 1]$。

    所以有转移：

    $$dp[l][r] = \max \{a_l - dp[l + 1][r], a_r - dp[l][r - 1]\}$$

    边界是 $dp[i][i] = a_i$。设所有数的总和为 $S$，则整个序列上先手与后手的分差是 $dp[0][n - 1]$。由于 Alice 先手，所以 Bob 的最优得分就是 $(S - dp[0][n - 1]) / 2$。
    
    为了真正完成交互，记录每个区间下最优策略是取左还是取右即可。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        int main() {
            int n;
            cin >> n;
            vector <int> a(n);
            for (int i = 0; i < n; i++) cin >> a[i];
            
            int sum = 0;
            for (auto i : a) sum += i;
            
            vector dp(n, vector(n, 0));
            
            for (int i = 0; i < n; i++) dp[i][i] = a[i];
            for (int l = 1; l < n; l++) {
                for (int i = 0, j = l; j < n; i++, j++) {
                    dp[i][j] = max(a[i] - dp[i + 1][j], a[j] - dp[i][j - 1]);
                }
            }
            int ans = (sum - dp[0][n - 1]) / 2;
            cout << ans << endl;
            
            int l = 0, r = n - 1;
            for (int i = 0; i < n; i++) {
                if (i % 2 == 0) {
                    string action; cin >> action;
                    if (action == "L") {
                        l ++;
                    } else {
                        r --;
                    }
                } else {
                    if (l == r || a[l] - dp[l + 1][r] == dp[l][r]) {
                        cout << "L" << endl;
                        l++;
                    } else {
                        cout << "R" << endl;
                        r--;
                    }
                }
            }
        }
        ```
    === "Python"
        ```python
        n = int(input())
        a = list(map(int, input().split()))

        total = sum(a)
        dp = [[0] * n for _ in range(n)]

        for i in range(n):
            dp[i][i] = a[i]
        for length in range(1, n):
            for i in range(n - length):
                j = i + length
                dp[i][j] = max(a[i] - dp[i + 1][j], a[j] - dp[i][j - 1])

        ans = (total - dp[0][n - 1]) // 2
        print(ans, flush=True)

        l, r = 0, n - 1
        for i in range(n):
            if i % 2 == 0:
                action = input().strip()
                if action == "L":
                    l += 1
                else:
                    r -= 1
            else:
                if l == r or a[l] - dp[l + 1][r] == dp[l][r]:
                    print("L", flush=True)
                    l += 1
                else:
                    print("R", flush=True)
                    r -= 1
        ```

### [1431. Knapsack 3 in 1](https://acm.sjtu.edu.cn/OnlineJudge/problem/1431)

你有 $n$ 种物品，第 $i$ 种物品每个质量为 $w_i$，重要程度为 $v_i$，且最多携带 $c_i$ 个。你只能携带总质量不超过 $W$ 克的物品，求最大重要程度总和。

* 数据范围：$1 \leq n \leq 800$, $1 \leq W \leq 10000$, $1 \leq w_i, v_i \leq 10000$, $1 \leq c_i \leq 10^9$

??? Solution

    对于 $c_i = 1$ 和 $c_i = \infty$ 的情况，可以直接使用 $0/1$ 背包和完全背包来解决。但对于一般的 $c_i$，如果直接枚举数量，状态数就会变成 $O(nW\max c_i)$，无法接受。

    一个最容易理解的做法是进行二进制拆分：把 $c_i$ 个同种物品拆成若干组，数量分别为 $1, 2, 4, \dots$，直到剩余的数量不足以再拆成两倍为止。这样做的好处是：

    * 每一组都可以看成一个新的 $0/1$ 物品；
    * 体积变成 $w_i \times k$，价值变成 $v_i \times k$；
    * 时间复杂度上，额外的开销只有 $\log \min(c_i, W)$。

    !!! info "更高效地实现背包 DP"

        教科书和课堂的背包算法记录两维状态：$f[i][j]$ 表示只考虑前 $i$ 种物品、总重量为 $j$ 时的最大价值。转移为：

        $$f[i][j] = \max(f[i-1][j], f[i-1][j-w_i] + v_i)$$

        注意到 $f[i][j]$ 只依赖于 $f[i-1][\cdot]$，所以可以压缩成一维数组，在内存消耗和缓存命中上都更友好。关键是枚举顺序：

        * **0/1 背包**：从大到小枚举 $j$，保证 $f[j - w_i]$ 还是上一轮的值。
        * **完全背包**：从小到大枚举 $j$，让 $f[j - w_i]$ 是本轮的值，相当于允许重复选。

        下面的参考代码就采用了一维数组，`add` 函数里从大到小枚举，实现 0/1 背包的单次转移。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        using LL = long long;

        int n, W;
        int f[10005];

        void add(int u, int v, int c) {
            if ((LL)u * c > W) return;
            u *= c;
            v *= c;
            for (int i = W; i >= u; i--) {
                f[i] = max(f[i], f[i - u] + v);
            }
        }

        int main() {
            cin >> n >> W;
            for (int i = 1; i <= n; i++) {
                int u, v, c;
                cin >> u >> v >> c;
                for (int j = 1; j <= c; j *= 2) {
                    c -= j;
                    add(u, v, j);
                }
                if (c) add(u, v, c);
            }
            int ans = 0;
            for (int i = 1; i <= W; i++) {
                ans = max(ans, f[i]);
            }
            cout << ans << '\n';
        }
        ```
    === "Python"
        ```python
        n, W = map(int, input().split())
        f = [0] * (W + 1)

        def add(u, v, c):
            if u * c > W:
                return
            u *= c
            v *= c
            for i in range(W, u - 1, -1):
                f[i] = max(f[i], f[i - u] + v)

        for _ in range(n):
            u, v, c = map(int, input().split())
            j = 1
            while j <= c:
                c -= j
                add(u, v, j)
                j *= 2
            if c:
                add(u, v, c)

        print(max(f))
        ```


### [1432. Colorful inversion](https://acm.sjtu.edu.cn/OnlineJudge/problem/1432)

对于每个 $i$，请输出以 $a_i$ 结尾的最长下降子序列长度。

* 数据范围：$1 \leq n \leq 10^5$, $1 \leq a_i \leq 10^6$

??? Solution

    请见 [Colorful inversion](lis.md) 章节。下面这份代码直接实现其中的 $O(n \log n)$ 做法。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        int main() {
            int n;
            cin >> n;
            vector <int> s = {0};
            for (int i = 1; i <= n; i++) {
                int ai;
                cin >> ai;
                ai = INT_MAX - ai; // lis
                auto p = lower_bound(s.begin(), s.end(), ai) - s.begin();
                if (p == s.size()) s.push_back(ai);
                else s[p] = min(s[p], ai);
                cout << p << " \n"[i == n];
            }
        }
        ```
    === "Python"
        ```python
        import bisect

        n = int(input())
        INF = 2 ** 30
        s = [0]
        ans = []
        for x in map(int, input().split()):
            x = INF - x
            p = bisect.bisect_left(s, x)
            ans.append(p)
            if p == len(s):
                s.append(x)
            else:
                s[p] = min(s[p], x)

        print(*ans)
        ```


### [1433. Bo](https://acm.sjtu.edu.cn/OnlineJudge/problem/1433)

博先生知道了一只股票 $n$ 天内的价格走势，每天可以进行买入、卖出或不操作。初始和结束时持有 $0$ 股，求最多能赚多少钱。

* 数据范围：$1 \leq n \leq 10^5$, $1 \leq a_i \leq 10^6$

??? Solution

    本题来源是 [Codeforces 867E Buy Low Sell High](https://codeforces.com/problemset/problem/867/E)。

    一个直观的想法是：维护之前所有价格中的最小值，如果今天价格更高就立刻卖出。但这样有时不是最优的：例如 $a = [1, 2, 3]$，如果在价格 $1$ 买入、价格 $2$ 卖出只能赚 $1$ 元，而等到价格 $3$ 再卖可以赚 $2$ 元。

    关键观察是：买 $1$ 卖 $3$ 可以拆成买 $1$ 卖 $2$，再买 $2$ 卖 $3$。第二次买 $2$ 并不是真的买入新股票，而是撤销之前在 $2$ 卖出的决定，改成在 $3$ 卖出。这种套路称为反悔贪心。

    具体实现时，用小根堆维护所有可用的买入价格。处理到今天价格 $x$ 时：

    * 先把 $x$ 放进堆里，作为未来买入的候选；
    * 如果堆顶价格小于 $x$，就完成一次交易，获得利润 $x - \min$；
    * 交易完成后，**再把 $x$ 放进堆里一次**。这个 $x$ 代表的是：如果未来某天以更高价格卖出，可以撤销今天的卖出，改成在那天卖出。

    因此每次成功卖出后，堆里会多出两个 $x$：一个是正常的今天买入，一个是撤销今天卖出的反悔机会。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        using LL = long long;

        int main() {
            int n;
            cin >> n;
            LL ans = 0;
            priority_queue <LL, vector <LL>, greater <LL>> q;
            for (int i = 1; i <= n; i++) {
                int x;
                cin >> x;
                if (!q.empty() && q.top() < x) {
                    ans += x - q.top();
                    q.pop();
                    q.push(x);
                }
                q.push(x);
            }
            cout << ans << '\n';
        }
        ```
    === "Python"
        ```python
        import heapq

        n = int(input())
        a = list(map(int, input().split()))

        ans = 0
        q = []
        for x in a:
            if q and q[0] < x:
                ans += x - q[0]
                heapq.heappop(q)
                heapq.heappush(q, x)
            heapq.heappush(q, x)

        print(ans)
        ```


### [1434. Fair division](https://acm.sjtu.edu.cn/OnlineJudge/problem/1434)

你作为赏金猎人需要将一个重量为 $w$ 克的金块分给 $n$ 个扈从，第 $i$ 个扈从需要 $a_i$ 重量的金块。每次切割金块需要支付 $x \times p \%$ 枚银蛇币（$x$ 为切割前金块的重量），最终支付所有切割代价的和向上取整枚银蛇币。要求支付尽可能少的银蛇币。

* 数据范围：$1 \leq n \leq 10^5$, $0 \leq w \leq 10^7$, $0 < p < 100$, $a_i \geq 1$, $\sum_i a_i = w$

??? Solution

    每次切割一块金块的代价，只和被切开的那块当前有多重有关。因此问题等价于：把若干叶子权值两两合并成一棵二叉树，最小化所有内部节点权值之和。

    这正是经典的 optimal merge / Huffman 问题。每次合并当前最轻的两块金块一定最优，所以用一个小根堆不断取出两个最小值合并即可。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        using LL = long long;

        int main() {
            LL n, w, p;
            cin >> n >> w >> p;

            priority_queue <LL, vector <LL>, greater <LL>> q;
            for (int i = 0; i < n; i++) {
                LL x;
                cin >> x;
                q.push(x);
            }

            LL ans = 0;
            while (q.size() > 1) {
                LL a = q.top();
                q.pop();
                LL b = q.top();
                q.pop();
                LL c = a + b;
                ans += c;
                q.push(c);
            }

            cout << (ans * p + 99) / 100 << '\n';
        }
        ```
    === "Python"
        ```python
        import heapq

        n, w, p = map(int, input().split())
        a = list(map(int, input().split()))

        heapq.heapify(a)
        ans = 0
        while len(a) > 1:
            x = heapq.heappop(a)
            y = heapq.heappop(a)
            c = x + y
            ans += c
            heapq.heappush(a, c)

        print((ans * p + 99) // 100)
        ```


### [1451. Life on a tree](https://acm.sjtu.edu.cn/OnlineJudge/problem/1451)

给定一棵树，要求选择尽可能少的点，使得树上每个点到某个被选点的距离都不超过 $k$。输出一种可行方案。

* 数据范围：$1 \leq n \leq 5 \times 10 ^ 5, 0 \leq k < n$

??? Solution

    **贪心策略**：每次找到最深的未被覆盖点，在它向根方向第 $k$ 步的祖先处选点。

    * 无论如何都至少要选一个点来覆盖这个未被覆盖的点；
    * 而在所有能覆盖该点的位置中，第 $k$ 步祖先的（对于仍未覆盖点的）覆盖范围所有其他点的超集。

    ??? info "$O(kn)$ 做法"
        先说一个直观的暴力实现：每轮找到最深的未被覆盖点 $x$，走到它向根方向第 $k$ 步的祖先 $y$ 处选点，然后从 $y$ 出发做一次深度不超过 $k$ 的 BFS，把所有距离 $y$ 不超过 $k$ 的点标成已覆盖。

        上面这个做法是 $\Theta(n^2)$ 的：令 $k=2$，考虑一个蜘蛛形状，从一个中心发射 $n / 3$ 条长度为 $3$ 的链。此时，每个叶子的 $k$ 级祖先是中心点的邻居，都会触发中心点的更新。

        <center>
        ```mermaid
        graph TD
            center((o))
            a1((a1))
            b1((b1))
            leaf1((c1))
            a2((a2))
            b2((b2))
            leaf2((c2))
            a3((a3))
            b3((b3))
            leaf3((c3))
            a4((a4))
            b4((b4))
            leaf4((c4))

            center --- a1
            a1 --- b1
            b1 --- leaf1

            center --- a2
            a2 --- b2
            b2 --- leaf2

            center --- a3
            a3 --- b3
            b3 --- leaf3

            center --- a4
            a4 --- b4
            b4 --- leaf4

            style center fill:#e0e0e0,stroke:#333,stroke-width:2px
            style a1 fill:#90ee90,stroke:#333,stroke-width:2px
            style a2 fill:#90ee90,stroke:#333,stroke-width:2px
            style a3 fill:#90ee90,stroke:#333,stroke-width:2px
            style a4 fill:#90ee90,stroke:#333,stroke-width:2px
            style b1 fill:#e0e0e0,stroke:#333,stroke-width:2px
            style b2 fill:#e0e0e0,stroke:#333,stroke-width:2px
            style b3 fill:#e0e0e0,stroke:#333,stroke-width:2px
            style b4 fill:#e0e0e0,stroke:#333,stroke-width:2px
            style leaf1 fill:#e0e0e0,stroke:#333,stroke-width:2px
            style leaf2 fill:#e0e0e0,stroke:#333,stroke-width:2px
            style leaf3 fill:#e0e0e0,stroke:#333,stroke-width:2px
            style leaf4 fill:#e0e0e0,stroke:#333,stroke-width:2px
        ```
        </center>

        一个简单的优化是将覆盖标记改为记录从这个点出发最远可以覆盖多少范围的点，例如被标记的点是 $k$，没有被覆盖的点是 $-1$。在 BFS 的过程中，算法仅在标记被更新得更大的时候继续扫描邻居。此时，每个点（或者考虑更新的过程在边上，每条边）至多触发 $k$ 次更新，因此总复杂度是 $O(kn)$ 的。

    ??? info "$O(n ^ {1.5})$ 分析: 答案一定不会超过 $O(n / k)$"
        $O(kn)$ 的算法可以在 $k > \sqrt{n}$ 时使用另一个角度来分析。
        
        考虑每次我们从一个没有被个覆盖的点 $u_i$ 找一个 $k$ 级父亲 $a_i$ 的时候，$(u_i, a_i)$ 一定不会和之前的任何一条 $(u_j, a_j)$ 路径相交。否则，由于所有路径都是指向根的，我们可以从相交的点 $v$ 切换来构成 $u_i \rightarrow \dots \rightarrow v \rightarrow \dots \rightarrow a_i$ 的路径；因此，$u_i$ 在 $a_j$ 的子树里。而由于加入的时候 $a_j$ 子树内最深的未覆盖点是 $u_j$，因此 $a_j$ 一定能覆盖到 $u_i$，与算法假设矛盾。

        由于路径长度（除了选择覆盖根时的最后一条）至少是 $k$，总轮数是 $O(n / k)$ 的。
        
        哪怕每轮 BFS 算作 $O(n)$，总复杂度也是 $O(n ^ 2 / k) = O(n ^ {1.5})$ 的。
    
    ??? info "$O(n)$ 做法：懒标记"

        考虑基于答案一定不会超过 $O(n / k)$ 的分析中所用到性质的做法。首先我们提出一个新的 $O(kn)$ 做法：
        
        * 在标记一个点的时候，从标记点开始向上依次给予祖先 $k - 1, \dots, 1$ 的标记。标记仍然和之前一样表示从这个点出发最远可以覆盖多少范围的点，但此时标记不会给予子树内的点。
        * 在查询一个点是否被标记的时候，除了查看自己的标记以外，还要从这个点开始向上查询 $k$ 步，找是否存在 $i$ 级祖先标记不小于 $i$。

        这么做相当于把向下的标记去掉了，改为用向上的查询替代。由于我们知道答案不超过 $O(n / k)$，第一步标记的复杂度实际上是 $O(k) \cdot O(n / k) = O(n)$ 的，第二步的操作是可能慢的。现在我们考虑用我们算法枚举点是从深到浅的性质来优化第二步。考虑进行一次查询时，

        * 如果成功查询到一个祖先的标记满足要求，由于当前查询节点是最深的点，那么这个祖先的整个子树已经满足要求了。
        * 如果没有找到满足要求的祖先，那么这一步会触发一次标记。

        对于第一种情况，我们可以直接删除掉整棵子树；如果扫描了 $i$ 个点那么至少能删掉 $i$ 个点，一共只能删 $n$ 个点，因此是累计 $O(n)$。对于第二种情况，$O(k) \cdot O(n / k) = O(n)$ 仍然成立。

        因此，实现了删除操作的复杂度是 $O(n)$ 的。为了方便实现，删除操作甚至也可以直接通过打标记来懒实现。

        === "C++"
            ```cpp
            #include <bits/stdc++.h>
            using namespace std;

            int n, k;
            vector <vector <int>> E;
            vector <int> fa, r, ans;

            void check(int x) {
                int y = x;
                for (int i = 0; i <= k; i++, y = fa[y]) {
                    if (r[y] >= i) {
                        for (int j = 0; j < i; j++, x = fa[x]) r[x] = n; // delete
                        return;
                    }
                    if (i == k) break;
                }
                ans.push_back(y);
                for (int i = k; i >= 0; i--, y = fa[y]) {
                    r[y] = max(r[y], i);
                }
            }

            int main() {
                cin >> n >> k;
                E.resize(n + 1);
                fa.resize(n + 1);
                r.resize(n + 1, -1);
                for (int i = 1; i < n; i++) {
                    int u, v;
                    cin >> u >> v;
                    E[u].push_back(v);
                    E[v].push_back(u);
                }

                vector <int> q = {1};
                fa[1] = 1;
                for (size_t h = 0; h < q.size(); h++) {
                    int x = q[h];
                    for (auto v : E[x]) if (v != fa[x]) {
                        fa[v] = x;
                        q.push_back(v);
                    }
                }
                reverse(q.begin(), q.end());
                for (auto x : q) check (x);

                cout << ans.size() << '\n';
                for (auto v : ans) cout << v << " \n"[v == ans.back()];
            }
            ```

    ??? info "$O(n)$ 做法：子树信息传递"

        任取一个贪心算法得到的方案，观察任意一棵子树，其对外的影响可以由一个二元组决定：

        * **向外提供覆盖**：处理完 $x$ 的整棵子树后，当前子树内能覆盖到 $x$ 的最强剩余覆盖半径。这部分可能被传到祖先去解决其他子树的问题。
        * **向外提出需求**：子树里距离 $x$ 最远、但仍然没有被覆盖的点的距离。根据贪心规则，这个距离向上累加达到 $k$ 的时候，所在祖先位置会被标记。

        这个二元组只和子树本身形态有关，且是确定的。因此，我们只需要在搜索树的时候正确维护好这个二元组，就可以在一次 DFS/BFS 中解决标记的问题了。

        === "C++"
            ```cpp
            #include <bits/stdc++.h>
            using namespace std;

            int n, k;
            vector <vector <int>> E;
            vector <int> ans;

            pair <int, int> dfs(int x, int parent) {
                int far = 0, cover = -1;
                for (int v : E[x]) {
                    if (v == parent) continue;
                    auto [c, f] = dfs(v, x);
                    cover = max(cover, c - 1);
                    far = max(far, f + 1);
                }

                if (far > cover) {
                    if (x == 1 || far == k) {
                        ans.push_back(x);
                        cover = k;
                        far = -1;
                    }
                } else {
                    far = -1;
                }
                return {cover, far};
            }

            int main() {
                cin >> n >> k;
                E.resize(n + 1);
                for (int i = 1; i < n; i++) {
                    int u, v;
                    cin >> u >> v;
                    E[u].push_back(v);
                    E[v].push_back(u);
                }

                dfs(1, 0);
                cout << ans.size() << '\n';
                for (auto v : ans) cout << v << " \n"[v == ans.back()];
            }
            ```


### [1454. Xingqiu at your service](https://acm.sjtu.edu.cn/OnlineJudge/problem/1454)

行秋想释放 $n$ 次大招，每次大招需要两次小技能充能。释放小技能时有 $p$ 的概率刷新小技能冷却时间（被刷新的小技能不能再次触发刷新）。求期望下需要主动释放多少次小技能。

* 数据范围：$1 \leq n \leq 10^9$, $0 \leq p \leq 1$（$p$ 至多两位小数），答案绝对误差需小于 $10^{-5}$

??? Solution

    设 $f[i]$ 为还需要 $i$ 次小技能时，期望主动释放的次数。每次主动释放后：

    * 概率 $1 - p$：没有刷新，还需要 $i - 1$ 次
    * 概率 $p$：刷新了，相当于免费多一次，还需要 $i - 2$ 次

    于是有递推：

    $$f[i] = 1 + (1 - p) \cdot f[i - 1] + p \cdot f[i - 2]$$

    边界：$f[0] = 0$, $f[1] = 1$。答案是 $f[2n]$。

    由于 $n$ 可达 $10^9$，直接递推会超时。有三种加速方法：

    ??? info "矩阵快速幂"
        令状态向量为 $(f[i], f[i-1], 1)^\top$，转移矩阵为

        $$
        T = \begin{pmatrix}
        1 - p & p & 1 \\
        1 & 0 & 0 \\
        0 & 0 & 1
        \end{pmatrix},
        $$

        从初始向量 $v_1 = (f[1], f[0], 1)^\top = (1, 0, 1)^\top$ 出发，由 $v_i = T \cdot v_{i-1}$ 得 $v_{2n} = T^{2n-1} v_1$，其第一个分量即为 $f[2n]$。

        在这个题里，精度要求比较高，而精度舍入误差会被矩阵运算放大。C++ 需要（在计算 $1-p$ 前）使用 `__float128` 通过，而 Python 可以使用 `Decimal` 实现。

        === "C++"
            ```cpp
            #include <bits/stdc++.h>
            using namespace std;

            typedef __float128 f128;
            typedef array <array <f128, 3>, 3> Matrix;

            Matrix mul(const Matrix &P, const Matrix &Q) {
                Matrix C = {};
                for (int i = 0; i < 3; i++)
                    for (int k = 0; k < 3; k++)
                        for (int j = 0; j < 3; j++)
                            C[i][j] += P[i][k] * Q[k][j];
                return C;
            }

            int main() {
                int n; double p;
                cin >> n >> p;

                Matrix T = {{
                    {1 - f128(p), f128(p), 1},
                    {1, 0, 0},
                    {0, 0, 1}
                }},
                A = {{
                    {1, 0, 0},
                    {0, 1, 0},
                    {0, 0, 1}
                }};

                for (int k = 2 * n - 1; k; k >>= 1) {
                    if (k & 1) A = mul(A, T);
                    T = mul(T, T);
                }

                cout << fixed << setprecision(9) << (double)(A[0][0] + A[0][2]) << '\n';
            }
            ```
        === "Python"
            ```python
            from decimal import Decimal, getcontext
            getcontext().prec = 100

            n, p = input().split()
            n = int(n)
            p = Decimal(p)

            def mul(P, Q):
                C = [[0] * 3 for _ in range(3)]
                for i in range(3):
                    for k in range(3):
                        for j in range(3):
                            C[i][j] += P[i][k] * Q[k][j]
                return C

            T = [[1 - p, p, 1], [1, 0, 0], [0, 0, 1]]
            A = [[1, 0, 0], [0, 1, 0], [0, 0, 1]]

            k = 2 * n - 1
            while k:
                if k & 1:
                    A = mul(A, T)
                T = mul(T, T)
                k >>= 1

            print(f"{A[0][0] + A[0][2]:.9f}")
            ```

    ??? info "特征根法求通项"
        齐次部分的特征方程 $x^2 - (1-p)x - p = 0$ 有根 $x_1 = 1$, $x_2 = -p$。结合特解 $\frac{i}{1+p}$ 和初始条件，得通项公式：

        $$f[i] = \frac{i}{1+p} + \frac{p}{(1+p)^2}\left(1 - (-p)^i\right).$$

        因此答案为

        $$f[2n] = \frac{2n}{1+p} + \frac{p(1 - p^{2n})}{(1+p)^2}.$$

        === "C++"
            ```cpp
            #include <bits/stdc++.h>
            using namespace std;

            int main() {
                int n;
                double p;
                cin >> n >> p;

                double q = 1 + p;
                double pk = pow(p, 2 * n);
                double ans = 2.0 * n / q + p * (1 - pk) / (q * q);

                cout << fixed << setprecision(9) << ans << '\n';
            }
            ```
        === "Python"
            ```python
            n, p = map(float, input().split())

            q = 1 + p
            pk = p ** (2 * n)
            ans = 2 * n / q + p * (1 - pk) / (q * q)

            print(f"{ans:.9f}")
            ```

    ??? info "递推 + 线性外推"
        一列独立同分布的伯努利随机变量之和收敛于期望。
        或者，观察通项公式可知，当 $i$ 很大时且 $p < 1$ 时，$(-p)^i$ 趋近于 $0$，因此 $f[i] - f[i-1] \to \frac{1}{1+p}$，即递推趋于线性增长。

        利用这一性质，暴力递推前 $K$ 项，然后用线性外推：

        $$f[i] = f[K] + (i - K) \cdot \frac{1}{1+p} \quad (i > K),$$

        这样只需 $O(K)$ 时间。特殊情况 $p = 1$ 时上式对于奇偶分别成立；因此我们只需要关心最坏的 $p = 0.99$ 的情况。一个粗略的精度量级分析时 $\log_{p}({\varepsilon / n ^ 2}) \leq \log_{0.99}({10 ^ {-23}})  \approx 5269$，因此 $K$ 取略大于这个的就可以满足累计误差的要求了。 

        === "C++"
            ```cpp
            #include <bits/stdc++.h>
            using namespace std;

            int main() {
                int n;
                double p;
                cin >> n >> p;
                n *= 2;

                int m = min(n, 6000);
                vector<double> f(m + 1);
                f[1] = 1;
                for (int i = 2; i <= m; i++)
                    f[i] = 1 + (1 - p) * f[i - 1] + p * f[i - 2];

                cout << fixed << setprecision(9) << f[m] + (n - m) / (1.0 + p) << '\n';
            }
            ```
        === "Python"
            ```python
            n, p = map(float, input().split())
            n = int(n) * 2

            m = min(n, 6000)
            f = [0.0] * (m + 1)
            f[1] = 1
            for i in range(2, m + 1):
                f[i] = 1 + (1 - p) * f[i - 1] + p * f[i - 2]

            print(f"{f[m] + (n - m) / (1 + p):.9f}")
            ```


### [1765. Prehistoric Programs](https://acm.sjtu.edu.cn/OnlineJudge/problem/1765)

将 $n$ 个由左右括号组成的字符串排列成一个序列，使得连接后的字符串是一个正确嵌套的括号结构。如果无法实现，输出 `impossible`。

* 数据范围：$1 \leq n \leq 10^6$，输入中最多包含 $10^7$ 个小括号。

??? Solution

    > 题目来源：The 2021 ICPC World Finals Dhaka ([QOJ Link](https://qoj.ac/problem/5108))

    把左括号看成 $+1$，右括号看成 $-1$。一个括号串合法，当且仅当所有前缀和非负（也即每个前缀里，左括号数不少于右括号数），且总和为 $0$。

    对每个字符串，定义两个量：

    * $s$：总和（左括号数减右括号数）
    * $p$：扫描过程中前缀和的最小值（$\leq 0$）

    例如 `))((`：前缀和依次是 $-1, -2, -1, 0$，所以 $s = 0$, $p = -2$。

    把字符串分成两类：$s \geq 0$（贡献左括号）和 $s < 0$（贡献右括号）。$s \geq 0$ 的串应该放在前面，$s < 0$ 的串放在后面。

    **$s \geq 0$ 部分的排序**：按 $p$ 从大到小排序（即 $-p$ 从小到大）。直觉是：$p$ 越大，对前面余额的要求越低，应该越早放。

    ??? info "为什么这个排序是对的？"
        考虑两个 $s \geq 0$ 的字符串 $A$, $B$，若 $p_A \geq p_B$，则 $A$ 放在 $B$ 前面不会更差。

        把 $AB$ 接到当前余额 $c$ 后面，要求 $c + p_A \geq 0$ 且 $c + s_A + p_B \geq 0$，即 $c \geq \max(-p_A, -s_A - p_B)$。

        由于 $s_A \geq 0$，有 $-s_A - p_B \leq -p_B$；又 $p_A \geq p_B$ 意味着 $-p_A \leq -p_B$。所以 $AB$ 所需的最小余额不超过 $-p_B$。

        而 $BA$ 所需的最小余额至少是 $-p_B$。因此 $AB$ 不比 $BA$ 差。

    **$s < 0$ 部分的排序**：把字符串翻转并交换括号方向，问题就变成了对称的形式。也就是说，我们可以把这一类串变换后重新计算它们的 $s, p$，使用之前的贪心，最后把得到的顺序反过来接回原问题即可。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        struct Node {
            int s, p, id;
            bool operator < (const Node &other) const {
                return p > other.p;
            }
        };

        bool check(vector <Node> &a, vector <int> &order) {
            int cur = 0;
            sort(a.begin(), a.end());
            for (auto x : a) {
                if (cur + x.p < 0) return false;
                cur += x.s;
                order.push_back(x.id);
            }
            return true;
        }

        int main() {
            int n;
            cin >> n;

            vector <Node> left, right;
            int total = 0;
            for (int i = 1; i <= n; i++) {
                string s;
                cin >> s;

                int cur = 0, p = 0;
                for (char c : s) {
                    cur += (c == '(' ? 1 : -1);
                    p = min(p, cur);
                }
                total += cur;

                if (cur >= 0) {
                    left.push_back({cur, p, i});
                } else {
                    reverse(s.begin(), s.end());
                    cur = 0;
                    p = 0;
                    for (char c : s) {
                        cur -= (c == '(' ? 1 : -1);
                        p = min(p, cur);
                    }
                    right.push_back({cur, p, i});
                }
            }

            vector <int> l, r;
            if (total != 0 || !check(left, l) || !check(right, r)) {
                cout << "impossible" << '\n';
                return 0;
            }

            reverse(r.begin(), r.end());
            for (int x : l) cout << x << '\n';
            for (int x : r) cout << x << '\n';
        }
        ```


### [1856. Travelling Salesman Problem](https://acm.sjtu.edu.cn/OnlineJudge/problem/1856)

给定一个 $n$ 个点的非负权有向图，求经过所有点的回路的最短长度。其中，每个点可以经过多次。

* 数据范围：$1 \leq n \leq 20$, $0 \leq w_{ij} \leq 10^5$

??? Solution

    请见 [Travelling Salesman Problem](tsp.md) 章节。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        const int N = 20;
        const int INF = 0x3f3f3f3f;

        int f[N][1 << N];
        int d[N][N];

        int main() {
            int n;
            cin >> n;
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    cin >> d[i][j];
                }
            }

            for (int k = 0; k < n; k++) {
                for (int i = 0; i < n; i++) {
                    for (int j = 0; j < n; j++) {
                        d[i][j] = min(d[i][k] + d[k][j], d[i][j]);
                    }
                }
            }

            memset(f, 0x3f, sizeof f);
            f[0][1] = 0;
            for (int mask = 0; mask < (1 << n); mask++) {
                for (int i = 0; i < n; i++) {
                    if (!((mask >> i) & 1) || f[i][mask] >= INF) continue;
                    for (int j = 0; j < n; j++) {
                        if ((mask >> j) & 1) continue;
                        int nxt = mask | (1 << j);
                        f[j][nxt] = min(f[j][nxt], f[i][mask] + d[i][j]);
                    }
                }
            }

            int ans = INF;
            for (int i = 0; i < n; i++) {
                ans = min(ans, f[i][(1 << n) - 1] + d[i][0]);
            }
            cout << ans << '\n';
        }
        ```
