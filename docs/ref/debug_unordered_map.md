
### bug 描述

在 [1340. Strongly Connected](https://acm.sjtu.edu.cn/OnlineJudge/problem/1340) 一题中，有一份代码：

- 通过了所有小数据，
- WA 了大多数大数据，
- 并且没有整型溢出和数组越界的问题。

这份代码采用了 `unordered_map < int, vector<int> >` 来实现邻接表，部分代码类似如下：

```cpp
unordered_map < int, vector<int> > G;
void insert_edge (int x, int y) {
    G[x].push_back(y);
}
void dfs (int x) {
    for (int adj : G[x]) {
        if (! visited [adj]) {
            visited [adj] = true;
            dfs (adj);
        }
    }
    // ...
}
void work () {
    for (auto p : G) {
        int u = p.first;
        if (! visited [u]) {
            visited [u] = true;
            dfs (u);
        }
    }
}
```

### debug

这份代码初看没有任何问题。

??? info "直到……"

    用 `map` 替换 `unordered_map` 得到了 AC。

??? info "那么……"
    重新寻找上面看起来非常正确的代码里面可能出现的问题：`G[x]` 与常见的 `vector<int>[]` 行为是不一样的，后者是只读的，而前者会在不存在时插入键值对。


??? bug "bug 原因"
    
    `dfs` 中往 `unordered_map` 插入不影响 `dfs` 的键值对为什么会导致 WA 呢？我们尝试展开一下 `work` 这个 `for` 里面隐藏的东西：

    ```cpp
    void work() {
        for (auto it = GR.begin(); it != GR.end(); it++) {
            auto p = *it;
            // ...
        }
    }
    ```

    `map` 和 `unordered_map` 在下标访问时都会进行插入，但区别在于迭代器的*有效性*：
    
    - `map` 的实现是红黑树，因此其迭代器只有在删除时失效；
    - 而 `unordered_map` 的实现是动态扩容的哈希表，在扩容时，所有现有迭代器全部失效。

    而 `dfs` 函数有没有可能触发扩容呢？是的，比如只有一条边 $1 \rightarrow 2$ 的图里，`dfs(1)` 会调用 `dfs(2)`，而因为 2 没有出边，map 会插入一个 $(2, \{\})$。

    这个问题看似完美解决了，不过遗留一个疑问：为什么小数据没有出现问题？

??? question "小数据为什么不会挂？"
    考虑 bug 的触发条件：对于每个点 $x$，如果
    
    - $x$ 没有出边，且
    - $x$ 有入边，

    那么就会触发一次插入。当插入触发扩容的时候，`work` 就可能返回错误的结果。

    这个题还有一个隐藏的特性，大多数数据是随机的：

    ```python
    m = n
    print(n, m)
    for i in range(n):
        u, v = random.randint(1, n), random.randint(1, n)
        print(u, v)
    ```

    我们尝试对 $n$ 足够大的时候，分析每个点 $x$ 满足上述的概率。

    - $x$ 没有出边：$P(\forall u_i. u_i \neq x) = (1 - \frac {1} {n}) ^ n \rightarrow 1/e.$
    - $x$ 有入边：$P(\exists v_i. v_i = x) = 1 - P(\forall v_i. v_i \neq x) = 1 - (1 - \frac {1} {n}) ^ n \rightarrow 1 - 1/e.$

    由于 $u, v$ 独立，因此 $P(x \text{ leads to insert}) \rightarrow (e - 1)/e^2$.

    现在带入本题小数据的范围 $n = 500$。

    - 在 `work` 前，`unordered_map` 里存在期望 $500 \cdot (1 - 1/e) \approx 316$ 个点；
    - 接下来，会被插入 $500 \cdot (e - 1)/e^2 \approx 116$ 个点。

    `unordered_map` 默认 load factor 是 $1$，也即满后扩容；默认扩容方法是，将当前桶大小乘以 growth factor（默认为 $2$），并在一个质数表里寻找下一个大于等于这个值的质数。
    
    如果从头开始插入所有内容，得到的结果是：

    * 13, 29, 59, 127, **257**, **541**, ...

    我们可以发现，

    * 默认大小至少是 $13$，因此样例不会挂。
    * $257 << 316 \leq 316 + ({\rm insert}) \leq 500 < 541$，因此 $n = 500$ 附近挂的概率极低。