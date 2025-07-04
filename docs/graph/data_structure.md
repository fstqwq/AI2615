通常，我们可以用邻接矩阵或者邻接表来表示一个图。

### 邻接矩阵

对于邻接矩阵 $g$，$g[i][j]$ 表示顶点 $i$ 和顶点 $j$ 之间边的关系：

在无权图中，如果有 $i \rightarrow j$ 这条边，那么 $a[i][j]$ 的值为 $1$，否则为 $0$。

在有权图中，如果有 $i \rightarrow j$ 这条边，那么 $a[i][j]$ 的值为边的权值，否则通常可记录为 $+\infty$。


!!! example "$+\infty$ 的取值"

    $+\infty$ 的取值应当是一个足够大的数来不影响计算答案，但是通常应该比数据类型的最大值小一半，原因是我们可能会用比如 $a[i][k] + a[k][j]$ 去更新 $a[i][j]$ 的值，我们需要确保这个加法不会导致整型溢出。

    例如，当边权总和不超过 $10 ^ 9$ 时，你可以使用 `0x3f3f3f3f`$\approx 1.06 \times 10 ^ 9$ 这个值作为 `INF`。
    
    这么做的好处是，你可以使用 `memset(a, 0x3f, sizeof(a))` 来初始化这个数组。

!!! bug "整型溢出"
    当边权值域为 $10 ^ 9$ 时，最短路可能是 $n \cdot 10 ^ 9$ 的级别。此时应当使用 64 位整型。    

    
邻接矩阵无法记录有权重边，通常你可以记录重边中最小值或者最大值。

对于无向图，你应当保证 $a[i][j] = a[j][i]$。

邻接矩阵存储空间是 $O(|V| ^ 2)$ 的，遍历一个点的出边是 $O(|V|)$ 的，但是查询任意两点之间的边的关系的时间复杂度是 $O(1)$ 的。同时，由于其内存连续，访问速度非常快。

#### 使用 unordered_map 减小空间占用

如果你的图的顶点数很大，但是边数并没有达到平方级别，那么你可以使用哈希表 `unordered_map` 来减小空间占用，仅需把 `int g[N][N]` 替换为 `unordered_map<int, int> g[N]` 即可。

与此同时，遍历 `unordered_map` 可以实现类似 `vector` 邻接表的遍历功能。

!!! warning "下标访问特性"
    `unordered_map<int, int>` 使用下标访问时，如果没有这个键，会自动插入一个值为 $0$ 的键值对。
    
    因此，如果你需要判断 $(u, v)$ 边是否存在，你应当使用 `g[u].count(v)` 来判断，而不是进行下标访问。

!!! info "尽量避免使用 `unordered_map`"

    在大多数情况下，`unordered_map` 可以被看做一个无限下标的数组，但并不代表其能替代数组或者 `vector`：

    - **性能较差**：尽管 `unordered_map` 访问与增删均是期望 $O(1)$ 的，但是有额外操作，并且内存不连续，导致其访问较慢；在嵌套时，这个差距还会被放大。
    - **容易误用**：`unordered_map` 的下标访问特性导致其在看起来只读的操作中进行写入，除了上述提到的插入 $0$，还可能会导致更严重的后果，如 [unordered_map: 记一次有趣的 debug](../ref/debug_unordered_map.md)。
    
    在能够使用数组实现或者邻接表的情况下，尽量不要使用这种存边方法；更一般地，能用数组和 `vector` 时不要使用 `unordered_map`。


### 邻接表

邻接表是对于每个顶点，用一个表记录其的出边的数据结构。 

对于无向图，应当插入正反两条边。

!!! bug "无向图中的数组越界"
    
    如果你使用了边数相关的数组，请记得开两倍。


邻接矩阵存储空间是 $O(|V| + |E|)$ 的，遍历一个点的出边是 $O(\mathrm{deg})$ 的，但是查询任意两点之间的边的关系的时间复杂度是 $O(\mathrm{deg})$ 的。

邻接表的实现方式有很多种，这里我们介绍两种常见的实现方式。

#### vector

使用 `vector`，我们可以非常简短地实现邻接表。

```cpp
struct edge {
    int v, w;
};

vector <edge> g[N];

void add_edge (int u, int v, int w) {
    g[u].push_back({v, w});
}

void traverse (int u) {
    for (auto [v, w] : g[u]) {
        // do something
    }
}
```

这里，`g[i]` 是一个 `vector`，表示顶点 $i$ 的出边。`g[i][j]` 表示顶点 $i$ 的第 $j$ 条出边，它是一个 `edge` 类型的变量，其中 `v` 表示这条边的终点，`w` 表示这条边的权值。

在不需要边权信息的时候，我们可以使用 `vector <int>` 来表示 `g`。

#### 链表

在没有 `vector` 的时候，我们可以使用链表来实现邻接表，以实现动态往数组里添加值。这里实现了一种常见的单向链表，因其特性我们在网络流部分可能会再次使用到。

```cpp
struct edge {
    int v, w, next;
} e[M];

int head[N], cnt;

void add_edge (int u, int v, int w) {
    e[++cnt] = {v, w, head[u]};
    head[u] = cnt;
}

void traverse (int u) {
    for (int i = head[u]; i; i = e[i].next) {
        int v = e[i].v, w = e[i].w;
        // do something
    }
}
```