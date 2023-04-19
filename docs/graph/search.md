### DFS

对于一个无权图，你可以通过类似以下的方法深度优先遍历整个图：

```cpp
int n;
vector <int> g[N];
bool visited[N];
void dfs (int u) {
    visited[u] = true;
    for (auto v : g[u]) {
        if (! visited[v]) {
            dfs (v);
        }
    }
}
void traverse_the_graph () {
    // clear flag
    fill (visited + 1, visited + n + 1, false);
    // enumerated possible start
    // subject to change if there are extra requirements:
    // e.g., if every point is reachable by 1, dfs(1) is sufficient
    for (int i = 1; i <= n; i++) {
        if (! visited[i]) {
            dfs (i);
        }
    }
}
```

!!! question "本地运行一个稍大的图就 Segment Fault 了，如何解决？"
    请参考 [开栈](/debug/runtime/#开栈) 。

### BFS

对于一个无权图，你可以通过类似以下的方法广度优先遍历整个图：

```cpp
void bfs (int start) {
    // clear flag
    fill (visited + 1, visited + n + 1, false);
    queue <int> q;
    q.push(start);
    visited[start] = true;
    while (! q.empty()) {
        int u = q.front();
        q.pop();
        for (auto v : g[u]) {
            if (! visited[v]) {
                visited[v] = true;
                q.push(v);
            }
        }
    }
}
```

### DFS 序与 BFS 序

如果你关心 DFS 或者 BFS 中顺序的信息，你可以额外记录下来。

比如，如果你关心 DFS 序，那你可以在访问到一个顶点的时候记录 `order[step ++] = u;`。我们以 Kosaraju 算法中需要用到的 Finishing Time 为例：

```cpp
int order[N], step = 1;
void dfs (int u) {
    visited[u] = true;
    for (auto v : g[u]) {
        if (! visited[v]) {
            dfs (v);
        }
    }
    order[step ++] = u;
}
void traverse_the_graph () {
    fill (visited + 1, visited + n + 1, false);
    for (int i = 1; i <= n; i++) {
        if (! visited[i]) {
            dfs (i);
        }
    }
    // order[1...n] now contains the finishing time
}
```

如果你关心 BFS 序，一种简便方法是使用数组手写队列替换掉 `queue <int>`，这样在 BFS 结束后数组内就是 BFS 序。

```cpp
int q[N], head, tail;
void bfs () {
    head = 1, tail = 0;
    q[++ tail] = start;
    while (head <= tail) {
        int u = q[head ++];
        for (auto v : g[u]) {
            if (! visited[v]) {
                visited[v] = true;
                q[++ tail] = v;
            }
        }
    }
    // q[1...tail] now contains the bfs order
}
```