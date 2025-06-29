从本题起，请独立完成。

### [1300. k-th Smallest Number](https://acm.sjtu.edu.cn/OnlineJudge/problem?problem_id=1299)

给定 $n$ 个正整数，请找出其中的第 $k$ 小的数。

限制：

* $1 \leq k \leq n \leq 4\times 10 ^ 7, 0 \leq a_i < 2^{31}$；
* $n$ 个数中前 $m \leq 10 ^ 5$ 个由输入给定，其余由给定生成器生成。 

#### 关于划分数组的提示

实现快速选择算法时，你需要实现一个划分方法：对于给定 pivot，将数组划分为和 pivot 大小关系不同的部分。然而，该部分非常容易实现错误：
!!! bug "Hack!"
    考虑你的代码能否通过这样的数据：
    ```in
    1 1 1 1 1 1 1 1 ...
    ```
    ```in
    1 2 3 4 5 6 7 8 ...
    ```

* 如果你选择将数组划分为 $<$ 和 $\geq$ 两部分，那么考虑能否通过 $n$ 个元素几乎完全一样的数据。
    * 许多经典的快速排序实现采用了这一方法，但是只有某些实现是正确的。如果你要实现为划分两部分，你需要注意每次是否一定在 $=\mathrm{pivot}$ 的最左边或者最右边，这样很容易退化为平方；有一种奇怪的解决方式是当左指针和右指针同时指向 $=$ 时，**同时**向中间移动一格，以实现均衡两侧的目的。
* 另一种解决方法是实现三路划分：$<$, $=$ 和 $>$。
    * 这种情况下，如果你需要使用临时数组，请注意控制内存开销：存储一遍 $n$ 个数需要花费 $\sim 150\mathrm{MB}$。你可能需要对 vector 传引用、释放无用数组等方法减少内存占用。
    * 你也可以通过扫两遍的方法（比如，先确定 $<$，再确定 $=$）来规避临时数组。
* 实现随机化快速选择时，不应当选择最左或者最右做 pivot。
    * 尽管任意非真随机都可以有确定性方法去攻击，但是考虑到通常能处理的数据分布，你的算法不应该被 $1, 2, \dots, n$ 攻击。

#### 查错参考

你可以参考 [Debug](../debug/make_data.md) 部分对数据生成器的介绍来造一个数据进行测试。