
### [1953. Partition](https://acm.sjtu.edu.cn/OnlineJudge/problem/1953)

实现一个划分函数，将数组根据给定的 pivot 划分为三部分：小于等于 pivot 的部分、等于 pivot 的部分和大于等于 pivot 的部分。返回划分后的边界位置 l 和 r。

* 数据范围：$1 \leq n \leq 10 ^ 5$, $a_i \in [0, 2 ^ {31})$, 保证数组中至少有一个元素等于 pivot。

??? Solution

    作为快速选择算法的一部分，这个看上去简单的过程实践中很容易犯错：
    !!! bug "Hack!"
        考虑你的代码能否在这样的数据上表现良好：
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

    通常来说，有两种实现方法：

    - 扫两遍，第一遍将小于 pivot 的元素移到前面，第二遍将等于 pivot 的元素移到小于部分的后面。
    - 扫一遍，
    === "C++"
        以下这份代码实现了三路划分（扫两遍）：

        ```cpp
        pair<int, int> partition(vector <int> &a, int pivot) {
            int l = 0, n = int(a.size());
            for (int i = 0; i < n; i++)
                if (a[i] < pivot) swap(a[i], a[l++]);
            int r = l;
            for (int i = 0; i < n; i++)
                if (a[i] == pivot) swap(a[i], a[r++]);
            return {l, r};
        }
        ```

        以下这份代码更接近实际中使用的快速排序（扫一遍）：
        
        ```cpp
        pair<int, int> partition(vector <int> &a, int pivot) {
            int l = 0, r = int(a.size()) - 1;
            while (l <= r) {
                while (l <= r && a[l] < pivot) l++;
                while (l <= r && a[r] > pivot) r--;
                if (l <= r) swap(a[l++], a[r--]);
            }
            return {l, r + 1};
        }
        ```

    === "Python"
        ```python
        def partition(a, pivot):
            l = 0
            r = len(a) - 1
            while l <= r:
                while l <= r and a[l] < pivot: l += 1
                while l <= r and a[r] > pivot: r -= 1
                if l <= r: a[l], a[r] = a[r], a[l]; l += 1; r -= 1
            return (l, r + 1)
        ```


### [1300. k-th Smallest Number](https://acm.sjtu.edu.cn/OnlineJudge/problem/1300)

给定 $n$ 个正整数，请找出其中的第 $k$ 小的数。

* 数据范围：$1 \leq k \leq n \leq 4\times 10^7, 0 \leq a_i < 2^{31}$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1301. Bubbling Bubbles](https://acm.sjtu.edu.cn/OnlineJudge/problem/1301)

给定一个长度为 $n$ 的排列，元素标号为 $1 \dots n$。如果对这个排列进行冒泡排序，请输出每个元素在进行冒泡排序时，参与了多少次交换。

* 数据范围：$1 \leq n \leq 10^6$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1299. Closest Pair](https://acm.sjtu.edu.cn/OnlineJudge/problem/1299)

给定 $n$ 个二维欧几里得平面上的点 $p_1, p_2, \dots, p_n$，请输出距离最近的两个点的距离的平方。

* 数据范围：$2 \leq n \leq 4\times 10^5$, $-10^7 \leq x_i, y_i \leq 10^7$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1982. Minimum Perimeter Triangle](https://acm.sjtu.edu.cn/OnlineJudge/problem/1982)

给定 $n$ 个二维欧几里得平面上的点 $p_1, p_2, \dots, p_n$，请选出三个不相同的点，构成周长最小的三角形。

* 数据范围：$3 \leq n \leq 4 \times 10^5$, $-10^7 \leq x_i, y_i \leq 10^7$, 答案绝对误差不超过 $10^{-5}$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```


### [1302. Optimal Sort](https://acm.sjtu.edu.cn/OnlineJudge/problem/1302)

有一个 $1 \dots n$ 的排列，你需要通过比较排序，且最多进行 $n \left\lceil \log_2 (n) \right\rceil - (n - 1)$ 次询问。

* 数据范围：共 $10$ 组数据，每组 $n$ 分别为 $2, 4, 8, \dots, 1024$。每组数据调用程序至多 $100$ 次。

??? Solution

    TODO

    === "C++"
        ```cpp

        ```


### [1303. Subset Sum](https://acm.sjtu.edu.cn/OnlineJudge/problem/1303)

从 $n$ 个可能有重复的数字中取出一个非空子集，有 $2^n - 1$ 种取法。其中，每个数在 $2^{n-1}$ 个子集中出现。现在给你 $2^n-1$ 个数，请构造 $n$ 个整数，使得他们的非空子集的和与这 $2^n - 1$ 个数对应相同。

* 数据范围：$1 \leq n \leq 15$, $-10^6 \leq s_i \leq 10^6$

??? Solution

    TODO

    === "C++"
        ```cpp

        ```
    === "Python"
        ```python

        ```

