[1428. Minimum Spanning Tree](https://acm.sjtu.edu.cn/OnlineJudge/problem/1428)

给定一张 $n$ 个点，$m$ 条边的有权无向连通图，求最小生成树的边权和。

限制：$1 \leq n \leq 5000, 1 \leq m \leq 2\times 10 ^ 5, 1 \leq w_i \leq 10000$。

!!! question "数据范围能够支持多快的算法？"
    大约能支持 $O(n ^ 2)$，当然也可以支持 $m \log m$。
    
    也就是说，无优化的 Prim，采用并查集的 Kruskal 和 [Borůvka](https://en.wikipedia.org/wiki/Bor%C5%AFvka%27s_algorithm) 是可以通过的。


### 实现 Prim

!!! abstract "如何写一个 Prim？"
    以下是一个简要的教程。

    - 复制粘贴你的 Dijkstra 代码。
    - 将类似 `dis[v] > dis[u] + w` 的代码替换为 `dis[v] > w`。
    - 提交。

### 实现并查集优化的 Kruskal

Kruskal 查询连通性的基础组件是并查集，我们需要一个快速的实现来提升算法效率。

#### 路径压缩
```cpp
int f[N];
void init (int n) {
    for (int i = 1; i <= n; i++) f[i] = i;
}
int getf(int x) {
    return f[x] == x ? x : (f[x] = getf(f[x]));
}
bool merge (int x, int y) {
    x = getf(x), y = getf(y);
    if (x != y) {
        f[x] = y;
        return true;
    }
    return false;
}
```

通常来说，单纯路径压缩的并查集已经跑得足够快了，这是路径压缩的 $\log$ 难跑满；而 $\log$ 在实用的范围内与 $\alpha$ 差距只有至多 $5$ 倍，因此常数优化甚至能起到更好的效果。例如，在上面修改为 `f[x] = getf(f[f[x]])` 能在绝大多数情况下跑得比加了优化的并查集快。

### 路径压缩 + 按大小合并

!!! question "为什么不实现按秩合并？"
    - 两者在复杂度上是等价的；
    - 两者互相修改起来非常方便；
    - 大小信息通常比秩信息有用。

这里介绍一种不需要额外内存的方法。注意到，一棵子树的根指向自己其实只提供了一个 bit 的信息，因此我们可以用这块内存存储子树大小，方法是将根节点的 `f` 存储子树大小取负。请注意，此时一定不要用 0 标号。

```cpp
int f[N];
void init (int n) {
    for (int i = 1; i <= n; i++) f[i] = -1;
}
int getf(int x) {
    return f[x] < 0 ? x : (f[x] = getf(f[x]));
}
bool merge (int x, int y) {
    x = getf(x), y = getf(y);
    if (x != y) {
        if (f[x] > f[y]) swap(x, y); // size[x] < size[y]
        // merge : x <- y
        f[x] += f[y]; // size[x] += size[y]
        f[y] = x;
        return true;
    }
    return false;
}
```
