
### [1342. Shortest Path](https://acm.sjtu.edu.cn/OnlineJudge/problem/1342)

给一个 $n$ 个点 $m$ 条边的无向图，求 $s$ 到 $t$ 的最短路的长度。

限制：$n \leq 2500, m \leq 6200, 0 \leq w_i \leq 10 ^ 9$。

!!! question "数据范围能够支持多快的算法？"
    大约能支持 $O(nm)$, $O(n ^ 3)$ 是难以通过的。
    
    也就是说，无优化的 Dijkstra 和 Bellman-Ford 是可以通过的，但是 Floyd 可能比较困难。

### 使用 std::priority_queue 优化 Dijkstra

实现 $O(n \log n)$ 的 Dijkstra 需要堆支持 decrease-key 操作，这导致库函数接口设计起来较不便。

!!! question "需要学习实现 Fibnoacci 堆吗？"
    不推荐实现 Fibnocci 堆。
    
    实践中即使需要 $o(\log n)$ 的 decrease-key，通常会采用结构简单、运行效率高的配对堆。

与此同时，任何一个支持 push 和 pop-min 的堆都可以实现 $O(m \log m)$ 的 Dijkstra。

* decrease-key: 直接 push 一个新的结点进去。
* pop-min: 如果 pop 出的距离信息不是最新的，那么跳过。

??? question "为什么是 $O(m \log m)$？"
    Hint: 对每条边进行考虑。


因此，我们在这里介绍使用 `priority_queue` 来实现 $O(m \log n)$ Dijkstra。`priority_queue` 默认提供了一个基于小于号比较的**大**根堆，这意味着我们需要对操作进行一点修改来让其每次返回最小距离。以下是几种可能的方法：

* 修改 `priority_queue` 的比较函数: `priority_queue < pair<LL, int>, vector<pair<LL, int>>, greater<pair<LL, int>> >`
* 修改信息结点的小于号:
    ```cpp
    struct node {
        LL dis;
        int x;
        bool operator < (const node & other) const {
            return dis > other.dis;
        }
    };
    ```
* 向 `priority_queue` 里 push 取负的距离：`pq.push({-dis[v], v})`
