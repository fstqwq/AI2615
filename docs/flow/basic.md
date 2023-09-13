在网络流算法里，我们会关心一条边和其反向边：在正边被使用后，反边需要更新其容量。

因此，我们存储图的时候需要额外维护正反边关系。

### 使用邻接表实现网络流算法

使用指针的单向邻接链表可能实现如下：

```cpp
struct edge {
    edge *next, *reverse;
    int to, flow;
};
edge* list_head[N];
void add_edge (int u, int v, int flow) {
    edge* uv = new edge (list_head[u], NULL, v, flow);
    edge* vu = new edge (list_head[v], NULL, u, 0);
    uv->reverse = vu;
    vu->reverse = uv;
    list_head[u] = uv;
    list_head[v] = vu;
}
void traverse(int u) {
    for (edge* e = list_head[u]; e != NULL; e = e.next) {
        // do something with e
    }
}
```

其中，我们省略掉了对一个链表的抽象，而是使用表头来直接表示一个链表。

在算法题这种语境下，我们还可以规避掉指针，使用一个全局数组来提前申请空间，并在之后使用数组下标来访问。这么做有点像是内存池，但还有额外的好处：我们可以通过相邻标号来标记一对正反边。

!!! info "使用异或操作得到反边"

    对于 $k \geq 0$，我们有 $2k \oplus 1 = 2k + 1$, $2k + 1 \oplus 1 = 2k$，其中 $\oplus$ 为异或操作。

    因此，我们可以将正边存储在偶数标号上，反边存储在配对的奇数标号上，并定义 $\mathrm{reverse}(x) = x \oplus 1$。

以下代码片段使用了 $0$ 作为空指针，因此边的标号需要从 $2$ 开始。你也可以将 $-1$ 设为空，这样空间能从 $0$ 开始用。

```cpp
struct edge {
    int next, to, flow;
};
edge e[(M + 1) * 2]; // double space for back edge, reserve 0 for null
int edge_cnt = 2; // reverse(e[x]) = e[x ^ 1]
int list_head[N]; // init as 0
void add_edge (int u, int v, int flow) {
    e[edge_cnt] = {list_head[u], v, flow};
    list_head[u] = edge_cnt++;
    e[edge_cnt] = {list_head[v], u, 0};
    list_head[v] = edge_cnt++;
}
void traverse(int u) {
    for (int cur = list_head[u]; cur != 0; cur = e[cur].next) {
        // do something with e[cur]
        // e[cur ^ 1] is the reversed
    }
}
```

### 使用 vector 存图

!!! warning "vector 在扩容后，迭代器可能失效。"

    因此你不应当像链表的方式，在 `push_back` 的同时存储反边的指针或者迭代器。

你可以通过记录反边所在数组的下标，来规避掉上述问题。

```cpp
struct edge {
    int to, flow, rid;
};

vector <edge> G[N];

void add_edge (int u, int v, int flow) {
    int uid = (int) G[u].size(),
        vid = (int) G[v].size();
    G[u].push_back({v, flow, vid}};
    G[v].push_back({u, 0,  , uid});
}

void traverse(int u) {
    for (auto &[to, flow, rid] : G[u]) {
        // do something with e
        // G[to][rid] is the reversed
    }
}
```
