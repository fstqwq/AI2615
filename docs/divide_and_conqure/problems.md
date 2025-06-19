
### [1953. Partition](https://acm.sjtu.edu.cn/OnlineJudge/problem/1953)

实现一个划分函数，将数组根据给定的 pivot 划分为三部分：小于等于 pivot 的部分、等于 pivot 的部分和大于等于 pivot 的部分。返回划分后的边界位置 l 和 r。

* 数据范围：$1 \leq n \leq 10 ^ 5$, $a_i \in [0, 2 ^ {31})$, 保证数组中至少有一个元素等于 pivot。

??? Solution

    作为快速选择和快速排序算法的一部分，这个看上去简单的过程实践中很容易犯错：
    !!! bug "Hack!"
        考虑你的代码能否在这样的数据上表现良好：
        ```in
        100000 1
        1 1 1 1 1 1 1 1 ...
        ```
        ```in
        100000 1
        1 2 3 4 5 6 7 8 ...
        ```

    一种选择是将数组划分为 $\leq$ 和 $\geq$ 两部分。许多经典的快速排序实现采用了这一方法，性能也相对较好，但是这种方法是容易写错的。
    
    * 维护双指针时很容易在边界上出错。一种防止犯错的方法是，明确当前维护的区间代表的含义。在下面的参考代码里，代码维护的不变量是：

        * 在 $[0, l)$ 的部分一定小于等于 pivot。
        * 在 $(r, n)$ 的部分一定大于等于 pivot。
        * 在 $[l, r]$ 的部分没有任何保证，仍需要进一步处理。

        因此，当最终 $l = r + 1$ 时，$(l, l)$ 就是一个正确的划分点。

    * 即使划分点没有问题，还需要考虑函数是否会返回较差的划分点，从而导致递归超时。
        * 例如，一些实现会导致返回切分位置永远在 $=\mathrm{pivot}$ 的最左边或者最右边，在面对 $n$ 个元素完全一样的情况时会导致快速选择退化为平方。在下面的参考代码里，循环中途会让指针停在 $=\mathrm{pivot}$ 的位置，容易证明这么做划分点任意一侧中不会有超过 $n/2$ 个 $=\mathrm{pivot}$ 的元素，从而构成了均衡的条件。
        * 某些实现在 $\mathrm{pivot}$ 是最小值或者最大值时切分点在数组外侧，依据这样的切分点会递归处理整个数组。

    另一种解决方法是实现三路划分：$<$, $=$ 和 $>$。

    * 你可以通过扫两遍的方法（比如，先确定 $<$，再确定 $=$）来规避临时数组。如果仔细实现的话，也可以只扫一遍而不用临时数组。
    * 这种方法结果比较直观，且在实现快速选择时更方便。
    
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
                if (l < r) {
                    // It can be done at most n/2 times,
                    // so there are at most n/2 pivots on either side.
                    swap(a[l++], a[r--]); 
                } else if (l == r) {
                    // a[l] == pivot
                    return {l, l + 1};
                }
            }
            assert (l == r + 1);
            return {l, l};
        }
        ```

    === "Python"
        以下这份代码实现了三路划分（扫两遍）：
        
        ```python
        def partition(a, pivot):
            l, n = 0, len(a)
            for i in range(n):
                if a[i] < pivot:
                    a[l], a[i] = a[i], a[l]
                    l += 1
            r = l
            for i in range(n):
                if a[i] == pivot:
                    a[r], a[i] = a[i], a[r]
                    r += 1
            return (l, r)
        ```
        
        以下这份代码更接近实际中使用的快速排序（扫一遍）：
        
        ```python
        def partition(a, pivot):
            l, r = 0, len(a) - 1
            while l <= r:
                while l <= r and a[l] < pivot: l += 1
                while l <= r and a[r] > pivot: r -= 1
                if l < r:
                    # It can be done at most n/2 times,
                    # so there are at most n/2 pivots on either side.
                    a[l], a[r] = a[r], a[l]
                    l, r = l + 1, r - 1
                elif l == r:
                    # a[l] == pivot
                    return (l, l + 1)
            assert l == r + 1
            return (l, l)
        ```


### [1300. k-th Smallest Number](https://acm.sjtu.edu.cn/OnlineJudge/problem/1300)

给定 $n$ 个正整数，请找出其中的第 $k$ 小的数。

* 数据范围：$1 \leq k \leq n \leq 4\times 10^7, 0 \leq a_i < 2^{31}$

??? Solution

    实现随机化快速选择时，不应当选择最左或者最右做 pivot。

    * 尽管任意固定种子的伪随机都可以有确定性方法去攻击，但是在本题中你面对的测试数据是确定的，你几乎只需要考虑一些攻击者能想到的数据分布：例如，你的算法不应该被 $1, 2, \dots, n$ 攻击。
    * 在调试时，固定一个随机种子可以方便你复现问题。

    课堂上讲的 Median of Medians 实践中比随机快速选择慢很多。实现 Median of Medians 时，需要注意实现的时间、空间复杂度中常常被忽略掉的常数。

    * 如果你需要使用临时数组，请注意控制内存开销：存储一遍 $n$ 个数需要花费 $\sim 150\mathrm{MB}$。你可能需要对 vector 传引用、释放无用数组等方法减少内存占用。

    本题是 [调试技巧中的对拍章节](../debug/make_data.md) 的例题，你可以参考里面的内容调试你的程序。

    === "C++: 随机化"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        pair<int, int> partition(vector <int> &a, int pivot) {
            int l = 0, n = int(a.size());
            for (int i = 0; i < n; i++)
                if (a[i] < pivot) swap(a[i], a[l++]);
            int r = l;
            for (int i = 0; i < n; i++)
                if (a[i] == pivot) swap(a[i], a[r++]);
            return {l, r};
        }

        // use precise time as random seed
        const int RANDOM_SEED = chrono::system_clock::now().time_since_epoch().count();
        mt19937 rng(RANDOM_SEED); // random number generator
        
        int randint(int l, int r) { // rand in [l, r]
            return int(rng() % (r - l + 1) + l);
        }

        int kth(vector <int> a, int k) {
            int n = a.size();
            auto [l, r] = partition(a, a[randint(0, n - 1)]);
            if (k <  l) return kth(vector<int>(a.begin(), a.begin() + l), k);
            if (k >= r) return kth(vector<int>(a.begin() + r, a.end()), k - r);
            return a[k];
        }

        int n, k;
        vector <int> a;
        void read_input_data_vector() {
            int m;
            cin >> n >> k >> m; a.resize(n);
            for (int i = 0; i < m; i++){
                cin >> a[i];
            }
            unsigned int z = a[m - 1];
            for (int i = m; i < n; i++) {
                z ^= z << 13;
                z ^= z >> 17;
                z ^= z << 5;
                a[i] = z & 0x7fffffff;
            }
        }

        int main() {
            read_input_data_vector();
            cout << kth(a, k - 1) << endl; 
        }
        ```
    === "C++: 随机化（性能优化）"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        pair<int, int> partition(vector <int> &a, int L, int R, int pivot) {
            int l = L, r = R - 1;
            while (l <= r) {
                while (l <= r && a[l] < pivot) l++;
                while (l <= r && a[r] > pivot) r--;
                if (l < r) {
                    // It can be done at most n/2 times,
                    // so there are at most n/2 pivots on either side.
                    swap(a[l++], a[r--]); 
                } else if (l == r) {
                    // a[l] == pivot
                    return {l, l + 1};
                }
            }
            assert (l == r + 1);
            return {l, l};
        }

        // use precise time as random seed
        const int RANDOM_SEED = chrono::system_clock::now().time_since_epoch().count();
        mt19937 rng(RANDOM_SEED); // random number generator
        
        int randint(int l, int r) { // rand in [l, r]
            return int(rng() % (r - l + 1) + l);
        }

        int kth(vector <int> &a, int L, int R, int k) {
            auto [l, r] = partition(a, L, R, a[randint(L, R - 1)]);
            if (k <  l) return kth(a, L, l, k);
            if (k >= r) return kth(a, r, R, k);
            return a[k];
        }

        int n, k;
        vector <int> a;
        void read_input_data_vector() {
            int m;
            cin >> n >> k >> m; a.resize(n);
            for (int i = 0; i < m; i++){
                cin >> a[i];
            }
            unsigned int z = a[m - 1];
            for (int i = m; i < n; i++) {
                z ^= z << 13;
                z ^= z >> 17;
                z ^= z << 5;
                a[i] = z & 0x7fffffff;
            }
        }

        int main() {
            read_input_data_vector();
            cout << kth(a, 0, n, k - 1) << endl; 
        }
        ```

    === "C++: Median of Medians"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        pair<int, int> partition(vector <int> &a, int L, int R, int pivot) {
            int l = L, r = R - 1;
            while (l <= r) {
                while (l <= r && a[l] < pivot) l++;
                while (l <= r && a[r] > pivot) r--;
                if (l < r) {
                    // It can be done at most n/2 times,
                    // so there are at most n/2 pivots on either side.
                    swap(a[l++], a[r--]); 
                } else if (l == r) {
                    // a[l] == pivot
                    return {l, l + 1};
                }
            }
            assert (l == r + 1);
            return {l, l};
        }

        int kth(vector <int> &a, int L, int R, int k) {
            if (R - L <= 10) {
                sort(a.begin() + L, a.begin() + R);
                return a[k];
            }
            int p = L;
            for (int i = L; i < R; i += 5) {
                int j = min(i + 5, R);
                sort(a.begin() + i, a.begin() + j);
                swap(a[p++], a[(i + j) / 2]);
            }
            int pivot = kth(a, L, p, (L + p) / 2);
            auto [l, r] = partition(a, L, R, pivot);
            if (k <  l) return kth(a, L, l, k);
            if (k >= r) return kth(a, r, R, k);
            return a[k];
        }

        int n, k;
        vector <int> a;
        void read_input_data_vector() {
            int m;
            cin >> n >> k >> m; a.resize(n);
            for (int i = 0; i < m; i++){
                cin >> a[i];
            }
            unsigned int z = a[m - 1];
            for (int i = m; i < n; i++) {
                z ^= z << 13;
                z ^= z >> 17;
                z ^= z << 5;
                a[i] = z & 0x7fffffff;
            }
        }

        int main() {
            read_input_data_vector();
            cout << kth(a, 0, n, k - 1) << endl; 
        }
        ```

    === "C++: STL"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        int n, k;
        vector <int> a;
        void read_input_data_vector() {
            int m;
            cin >> n >> k >> m; a.resize(n);
            for (int i = 0; i < m; i++){
                cin >> a[i];
            }
            unsigned int z = a[m - 1];
            for (int i = m; i < n; i++) {
                z ^= z << 13;
                z ^= z >> 17;
                z ^= z << 5;
                a[i] = z & 0x7fffffff;
            }
        }

        int main() {
            read_input_data_vector();
            nth_element(a.begin(), a.begin() + k - 1, a.end());
            cout << a[k - 1] << endl; 
        }
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

