
### [1341. 2-SAT](https://acm.sjtu.edu.cn/OnlineJudge/problem/1341)

对于一系列形如 $(x_i=a)  \lor (x_j=b)$ 的约束，其中 $a,b\in \{0,1\}$，你需要判断是否存在一组 $\{x_i\}$ 使其同时满足。如果有，请输出一组解。

限制：$1 \leq n, m \leq 10 ^ 6$。

??? question "如何建图？"
    我们考虑两个命题 $p, q$。

    * 如果有 $p\rightarrow q$, 那么有 $\neg q \rightarrow \neg p$。
    * 如果有 $p\leftrightarrow \neg p$，那么无解。
    * 我们考虑把每个变量表示为图上的两个点，其中一个点代表这个变量取 0，另一个点代表这个变量不取 0（也即取 1），把命题表示为变量之间的有向边。
    * 存在 $p\leftrightarrow \neg p$ 时，命题无解，此时图两个点之间存在一定关系。否则，我们声称一定有解。

??? question "应该如何存储这个图？"
    应当使用邻接表，邻接矩阵存不下。
    
    如果你实在想使用邻接矩阵，也应当使用 `unordered_map` 优化空间。

    !!! warning "数组开两倍"
        一个点会被拆成真和假两个点；两个命题之前构成的关系也需要需要添加其逆否。

        同时，注意使用的标号范围，特别是 0 号点和 1 号点。

??? question "如何构造一组解？"

	* 考虑如果 $p$ 与 $q$ 本身是矛盾的，比如 $p : x=0$, $q : x = 1$。如果有 $p\rightarrow q$，也即 $p$ 必为假，$q$ 必为真。
    
        也就是说，$(x=0)\rightarrow (x = 1)$ 表示此刻 $x$ 必然不可能为 $0$。
	
    * 如何在解中体现 $p \rightarrow \neg p$ 时取到 $\neg p$？