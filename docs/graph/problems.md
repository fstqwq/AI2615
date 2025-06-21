
### [1985. A Way Out](https://acm.sjtu.edu.cn/OnlineJudge/problem/1985)

给定一张标号为 $1$ 到 $n$ 的有向图，请找到一条从 $s$ 到 $t$ 的简单路。简单路定义：不存在重复的点与边。

* 数据范围：$n \leq 1000, m \leq 10^4$

??? Solution

    === "C++ (BFS)"
        ```cpp
        vector <int> find_path(int n, int s, int t, vector < pair <int, int> >& edges) {
            vector <vector <int>> E(n + 1);
            for (auto [u, v] : edges) {
                E[u].push_back(v);
            }
            queue <int> q;
            vector <int> parent(n + 1, -1);
            q.push(s);
            parent[s] = 0; // root
            while (!q.empty()) {
                int u = q.front();
                q.pop();
                if (u == t) {
                    vector <int> path;
                    for (int i = t; i; i = parent[i]) path.push_back(i);
                    reverse(path.begin(), path.end());
                    return path;
                }
                for (int v : E[u]) {
                    if (parent[v] == -1) {
                        parent[v] = u;
                        q.push(v);
                    }
                }
            }
            return {};
        }
        ```
    === "C++ (DFS)"
        ```cpp
        vector <vector <int>> E;
        vector <int> path;
        vector <int> visited;

        bool dfs (int u, int t) {
            path.push_back(u);
            visited[u] = 1;
            if (u == t) return true;
            for (auto v : E[u]) {
                if (!visited[v]) {
                    if (dfs(v, t)) return true;
                }
            }
            path.pop_back();
            return false;
        }

        vector <int> find_path(int n, int s, int t, vector < pair <int, int> >& edges) {
            path.clear();
            E.assign(n + 1, {});
            visited.assign(n + 1, 0);
            for (auto [u, v] : edges) {
                E[u].push_back(v);
            }
            if (dfs (s, t)) return path;
            return {};
        }
        ```
    === "C++ (Bellman-Ford)"
        ```cpp
        vector <int> find_path(int n, int s, int t, vector < pair <int, int> >& edges) {
            vector <int> parent(n + 1, -1);
            parent[s] = 0;
            for (int _ = 0; _ < n - 1 && parent[t] == -1; _++)
                for (auto& [u, v] : edges)
                    if (parent[v] == -1 && parent[u] != -1)
                        parent[v] = u;
            if (parent[t] == -1) return {};
            vector <int> path;
            for (int i = t; i; i = parent[i]) path.push_back(i);
            reverse(path.begin(), path.end());
            return path;
        }
        ```

    === "Python (BFS)"
        ```python
        from collections import deque
        def find_path(n, s, t, edges):
            E = [[] for _ in range(n + 1)]
            for u, v in edges:
                E[u].append(v)
            q = deque([s])
            parent = [-1] * (n + 1)
            parent[s] = 0  # root
            while q:
                u = q.popleft()
                if u == t:
                    path = []
                    while u:
                        path.append(u)
                        u = parent[u]
                    return path[::-1]
                for v in E[u]:
                    if parent[v] == -1:
                        parent[v] = u
                        q.append(v)
            return []
        ```

    === "Python (DFS)"
        ```python
        def find_path(n, s, t, edges):
            E = [[] for _ in range(n + 1)]
            for u, v in edges:
                E[u].append(v)
            path = []
            visited = [0] * (n + 1)

            def dfs(u):
                path.append(u)
                visited[u] = 1
                if u == t:
                    return True
                for v in E[u]:
                    if not visited[v]:
                        if dfs(v):
                            return True
                path.pop()
                return False

            if dfs(s):
                return path
            return []
        ```
    === "Python (Bellman-Ford)"
        ```python
        def find_path(n, s, t, edges):
            parent = [-1] * (n + 1)
            parent[s] = 0
            for _ in range(n - 1):
                for u, v in edges:
                    if parent[v] == -1 and parent[u] != -1:
                        parent[v] = u
            if parent[t] == -1:
                return []
            path = []
            while t:
                path.append(t)
                t = parent[t]
            return path[::-1]
        ```


### [2466. A Way Out, Interactively](https://acm.sjtu.edu.cn/OnlineJudge/problem/2466)

**这是一道交互题。** 有一张由 $n$ 个点构成的无向图。最开始你在 $1$ 号点，你的目标是走到 $n$ 号点。在每一步，你可以走到一个邻居点并获取这个点的邻居信息。你可以在最多 $2n$ 次操作内走到 $n$ 号点，或者判断不可能走到。

* 数据范围：$2 \leq n \leq 100$

??? Solution

    与非交互版本不同之处在于，你需要维护当前的位置，且不能跳到之前访问过的任意点。因此你不能使用 BFS，而是需要使用 DFS。由于一个点最多出入一次，模拟 DFS 的移动过程即可满足要求。

    !!! warning "我在哪？"
        
        由于需要明确地返回当前节点，因此你需要在每次 DFS 访问完一个点后，输出当前点编号并输入（其实已经知道的） 01 串。
    
    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        int n;
        vector <int> vis;
        void dfs (int x) {
            vis[x] = 1;
            if (x == n) {
                cout << "yes" << endl;
                exit(0);
            }
            string s;
            cin >> s;
            for (int i = 1; i <= n; i++) {
                if (s[i - 1] == '1' && !vis[i]) {
                    cout << i << endl; // x -> i
                    dfs(i);
                    cout << x << endl; // i -> x
                    cin >> s; // to consume the input
                }
            }
        }

        int main() {
            cin >> n;
            vis.assign(n + 1, 0);
            dfs (1);
            cout << "no" << endl;
        }
        ```
    === "Python"
        ```python
        n = int(input())
        vis = [0] * n

        def dfs(x):
            global vis
            vis[x] = 1
            if x == n - 1:
                print("yes", flush=True)
                exit(0)
            s = input()
            for i in range(n):
                if s[i] == '1' and not vis[i]:
                    print(i + 1, flush=True)
                    dfs(i)
                    print(x + 1, flush=True)
                    input()

        dfs(0)
        print("no", flush=True)
        ```


### [1342. Shortest Path](https://acm.sjtu.edu.cn/OnlineJudge/problem/1342)

给定一个 $n$ 个点 $m$ 条边的无向图，求 $s$ 到 $t$ 的最短路的长度。

* 数据范围：$1 \leq n \leq 2500$, $1 \leq m \leq 6200$, $0 \leq w_i \leq 10^9$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1343. 0-1 Shortest path](https://acm.sjtu.edu.cn/OnlineJudge/problem/1343)

给定一个 $n$ 个点 $m$ 条边的无向图，求 $s$ 到 $t$ 的最短路的长度。

* 数据范围：$1 \leq n, m \leq 10^6$, $0 \leq w_i \leq 1$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1340. Strongly Connected](https://acm.sjtu.edu.cn/OnlineJudge/problem/1340)

给定一张 $n$ 个点，$m$ 条边的有向图，询问有多少对点 $(u, v)$ 满足：

1. 存在一条从 $u$ 到 $v$ 的路径；
2. 存在一条从 $v$ 到 $u$ 的路径；
3. $u < v$。

* 数据范围：$1 \leq n, m \leq 10^5$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1341. 2-SAT](https://acm.sjtu.edu.cn/OnlineJudge/problem/1341)

判断是否存在一组变量满足一系列形如 $(x_i=a) \lor (x_j=b)$ 的约束，其中 $a,b\in \{0,1\}$，并输出一组解（如果存在）。

* 数据范围：$1 \leq n, m \leq 10^6$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1344. Negative Cycle](https://acm.sjtu.edu.cn/OnlineJudge/problem/1344)

给定一个有向图，判断图中是否存在负环。如果存在，输出任意一个负环。

* 数据范围：$1 \leq n \leq 2500$, $1 \leq m \leq 6200$, $-10^9 \leq w_i \leq 10^9$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1345. Strict k-th shortest path](https://acm.sjtu.edu.cn/OnlineJudge/problem/1345)

给定一个有向图，求 $s$ 到 $t$ 的严格 $1, 2, \dots, k$ 短路的长度。

* 数据范围：$1 \leq n \leq 10^5$, $1 \leq m \leq 2 \times 10^5$, $0 \leq w_i \leq 10^9$, $k = 10$, Time Limit: 1s, Memory Limit: 128MB

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1739. Bracket Query](https://acm.sjtu.edu.cn/OnlineJudge/problem/1739)

给定一个合法的括号序列的长度 $n$ 和 $q$ 个查询，每个查询给出一个区间 $[l_i, r_i]$ 和该区间内左括号数减去右括号数的差 $c_i$。要求根据这些查询判断是否存在一个合法的括号序列满足所有查询，如果存在则输出一个可能的序列，否则输出 `?`。

* 数据范围：$2 \leq n \leq 3000$, $0 \leq q \leq \min({n + 1 \choose 2}, 5 \times 10^5)$, $1 \leq l_i \leq r_i \leq n$, $-n \leq c_i \leq n$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```

