### [1855. Matching on a Tree](https://acm.sjtu.edu.cn/OnlineJudge/problem/1855)

给定一棵 $n$ 个点，$n−1$ 条边的无向树，求树上最大匹配的大小。

* 数据范围：$1 \leq n \leq 5 \times 10 ^ 5$

??? Solution

    树形 DP：设 $f[u][0]$ 为处理完 $u$ 子树且 $u$ 未与子节点匹配的最大匹配数，$f[u][1]$ 为 $u$ 已与一个子节点匹配的最大匹配数。对于每个子节点，枚举是否和 $u$ 匹配进行转移。

    贪心：反复选择任意叶子并与其父节点匹配并删除这两个点，直到图为空。实现上，可以在 DFS 的回溯时检查并贪心匹配。

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
    === "C++: 贪心"
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
    === "Python: 贪心"
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

    === "C++"
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
            vector<tuple<int, int, int>> edges;
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
    === "Python"
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

    目标等价于选择最多个互不重叠的工作区间。按结束时间排序，贪心选择每次选择结束时间最早且不与已选区间重叠的工作。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        struct Job {
            int l, r;
            bool operator<(const Job &other) const {
                return r < other.r;
            }
        };

        int main() {
            int n, m, x;
            cin >> n >> m >> x;
            vector<Job> jobs;
            jobs.reserve(m);
            for (int i = 0; i < m; i++) {
                int l, r, a;
                cin >> l >> r >> a;
                jobs.push_back({l, r});
            }
            sort(jobs.begin(), jobs.end());
            int last = -1;
            int ans = 0;
            for (auto job : jobs) {
                if (job.l > last) {
                    last = job.r;
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

    设 $f[i][j]$ 为第 $i$ 天结束后、玩了 $j$ 天时的最大剩余金钱，按天转移并在最后选择可行的最大 $j$。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        int main() {
            int n, m;
            long long x;
            cin >> n >> m >> x;
            vector<vector<pair<int, int>>> jobs(n + 1);
            for (int i = 0; i < m; i++) {
                int l, r, a;
                cin >> l >> r >> a;
                jobs[l].push_back({r, a});
            }

            const long long NEG = -(1LL << 60);
            vector<vector<long long>> f(n + 1, vector<long long>(n + 2, NEG));
            f[0][0] = 0;
            for (int i = 0; i < n; i++) {
                for (int j = 0; j <= i; j++) {
                    if (f[i][j] < 0) continue;
                    f[i + 1][j] = max(f[i + 1][j], f[i][j]);
                    f[i + 1][j + 1] = max(f[i + 1][j + 1], f[i][j] - x);
                    for (auto job : jobs[i + 1]) {
                        f[job.first][j] = max(f[job.first][j], f[i][j] + job.second);
                    }
                }
            }

            for (int j = 0; j <= n; j++) {
                if (j == n || f[n][j + 1] < 0) {
                    cout << f[n][j] << '\n';
                    break;
                }
            }
            return 0;
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
        f = [[neg] * (n + 2) for _ in range(n + 1)]
        f[0][0] = 0
        for i in range(n):
            for j in range(i + 1):
                if f[i][j] < 0:
                    continue
                if f[i + 1][j] < f[i][j]:
                    f[i + 1][j] = f[i][j]
                val = f[i][j] - x
                if f[i + 1][j + 1] < val:
                    f[i + 1][j + 1] = val
                for r, a in jobs[i + 1]:
                    val = f[i][j] + a
                    if f[r][j] < val:
                        f[r][j] = val

        for j in range(n + 1):
            if j == n or f[n][j + 1] < 0:
                print(f[n][j])
                break
        ```


### [2535. Array Game](https://acm.sjtu.edu.cn/OnlineJudge/problem/2535)

这是一道交互题。有一列 $n$ 个数 $a_1, a_2, \dots, a_n$。Alice 和 Bob 每次能从头或者尾取走一个数，Alice 先手。双方的最终收益为取走的数之和。你是 Bob，请计算游戏对方在最优策略下你能获得的最大收益。

* 数据范围：$2 \leq n \leq 100$, $0 \leq a_i \leq 100$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```

### [1431. Knapsack 3 in 1](https://acm.sjtu.edu.cn/OnlineJudge/problem/1431)

你有 $n$ 种物品，第 $i$ 种物品每个质量为 $w_i$，重要程度为 $v_i$，且最多携带 $c_i$ 个。你只能携带总质量不超过 $W$ 克的物品，求最大重要程度总和。

* 数据范围：$1 \leq n \leq 800$, $1 \leq W \leq 10000$, $1 \leq w_i, v_i \leq 10000$, $1 \leq c_i \leq 10^9$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1432. Colorful inversion](https://acm.sjtu.edu.cn/OnlineJudge/problem/1432)

请对于每个 $i$，输出以 $a_i$ 结尾的最长下降子序列长度。

* 数据范围：$1 \leq n \leq 10^5$, $1 \leq a_i \leq 10^6$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1433. Bo](https://acm.sjtu.edu.cn/OnlineJudge/problem/1433)

博先生知道了一只股票 $n$ 天内的价格走势，每天可以进行买入、卖出或不操作。初始和结束时持有 $0$ 股，求最多能赚多少钱。

* 数据范围：$1 \leq n \leq 10^5$, $1 \leq a_i \leq 10^6$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1434. Fair division](https://acm.sjtu.edu.cn/OnlineJudge/problem/1434)

你作为赏金猎人需要将一个重量为 $w$ 克的金块分给 $n$ 个扈从，第 $i$ 个扈从需要 $a_i$ 重量的金块。每次切割金块需要支付 $x \times p \%$ 枚银蛇币（$x$ 为切割前金块的重量），最终支付所有切割代价的和向上取整枚银蛇币。要求支付尽可能少的银蛇币。

* 数据范围：$1 \leq n \leq 10^5$, $0 \leq w \leq 10^7$, $0 < p < 100$, $a_i \geq 1$, $\sum_i a_i = w$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1451. Life on a tree](https://acm.sjtu.edu.cn/OnlineJudge/problem/1451)

在树结构的宿舍楼中选择一些医疗点，使得每幢宿舍楼都能到达一个距离不超过 $k$ 的医疗点，并要求医疗点数量尽可能少。输出一种可行的方案。

* 数据范围：$1 \leq n \leq 5 \times 10 ^ 5, 0 \leq k < n$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1454. Xingqiu at your service](https://acm.sjtu.edu.cn/OnlineJudge/problem/1454)

行秋想释放 $n$ 次大招，每次大招需要两次小技能充能。释放小技能时有 $p$ 的概率刷新小技能冷却时间（被刷新的小技能不能再次触发刷新）。求期望下需要主动释放多少次小技能。

* 数据范围：$1 \leq n \leq 10^9$, $0 \leq p \leq 1$（$p$ 至多两位小数），答案绝对误差需小于 $10^{-5}$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1765. Prehistoric Programs](https://acm.sjtu.edu.cn/OnlineJudge/problem/1765)

将 n 个由左右括号组成的字符串排列成一个序列，使得连接后的字符串是一个正确嵌套的括号结构。如果无法实现，输出 'impossible'。

* 数据范围：$1 \leq n \leq 10^6$，输入中最多包含 $10^7$ 个小括号。

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1856. Travelling Salesman Problem](https://acm.sjtu.edu.cn/OnlineJudge/problem/1856)

给一个 $n$ 个点的非负权有向图，求经过所有点的回路的最短长度。其中，每个点可以经过多次。

* 数据范围：$1 \leq n \leq 20$, $0 \leq w_{ij} \leq 10^5$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```
