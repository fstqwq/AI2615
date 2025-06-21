
### [1953. Partition](https://acm.sjtu.edu.cn/OnlineJudge/problem/1953)

实现一个划分函数，将数组根据给定的 pivot 划分为三部分：小于等于 pivot 的部分、等于 pivot 的部分和大于等于 pivot 的部分。返回划分后的边界位置 l 和 r。要求：

* $0 \leq l \leq r \leq n$;
* 当 $l=r$ 时，$0 < l = r < n$;
	* 也即，依据你的划分进行递归不会导致死递归。
* 对于划分左侧的 `= pivot` 数量 $x$ 和右侧的数量 $y$，需要满足 $\max (x, y) \leq \max (10, 0.5 n)$。
	* 也即，依据你的划分进行递归不会导致较严重的复杂度退化。

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

    !!! warning "实现随机化快速选择时，不应当选择最左或者最右做 pivot。"

        尽管任意固定种子的伪随机都可以有确定性方法去攻击，但是在本题中你面对的测试数据是确定的，你几乎只需要考虑一些攻击者能想到的数据分布：例如，你的算法不应该被 $1, 2, \dots, n$ 攻击。

        提示里提供了一种用当前时间作为随机种子的方法。将其替换为固定随机种子，可以方便你调试时复现问题。

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

### [1302. Optimal Sort](https://acm.sjtu.edu.cn/OnlineJudge/problem/1302)

本题为实现 C++ 函数的交互形式。有一个 $1 \dots n$ 的排列，你需要通过比较两个元素来实现排序，且最多进行 $n \left\lceil \log_2 (n) \right\rceil - (n - 1)$ 次询问。

* 数据范围：共 $10$ 组数据，每组 $n$ 分别为 $2, 4, 8, \dots, 1024$。每组数据调用程序至多 $100$ 次。

??? Solution

    ??? info "仔细实现的归并排序是满足要求的。"

        考虑归并的过程：对于长度为 $x, y$ 的两个数组，归并过程至多需要 $x + y - 1$ 次比较。
        
        把这个比较数量在归并树上进行计算，可以发现：

        * 每个非叶子节点代表一次归并操作，代价至多为元素数量减一；
        * 每个叶子所代表的元素参与归并的次数就是其深度。
        
        因此，总比较次数可以用叶子深度求和 (不超过 $n \lceil \log_2 n \rceil$) 减去非叶子数量 (恰好为 $n - 1$) 来表示，也即题目所求。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;
        int query(int x, int y);

        void solve(vector <int> &a) {
            if (a.size() <= 1) return;
            vector <int> left(a.begin(), a.begin() + a.size() / 2);
            vector <int> right(a.begin() + a.size() / 2, a.end());
            solve(left); solve(right);
            merge(left.begin(), left.end(),
                  right.begin(), right.end(),
                  a.begin(), [](int x, int y) { return query(x, y) == -1; });
        }

        void optimal_sort(int n) {
            vector <int> a(n);
            for (int i = 0; i < n; i++) a[i] = i + 1;
            solve(a);
            for (int i = 0; i < n; i++) cout << a[i] << " \n"[i == n - 1];
        }
        ```
    === "C++ (手写 merge)"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;
        int query(int x, int y);

        void solve(vector <int> &a) {
            if (a.size() <= 1) return;
            vector <int> left(a.begin(), a.begin() + a.size() / 2);
            vector <int> right(a.begin() + a.size() / 2, a.end());
            solve(left); solve(right);
            auto cmp = [&] (auto i, auto j) {
                if (i == left.size()) return false;
                if (j == right.size()) return true;
                return query(left[i], right[j]) == -1; // x < y
            };
            for (size_t i = 0, j = 0, k = 0; k < a.size(); k++) {
                if (cmp(i, j)) {
                    a[k] = left[i++];
                } else {
                    a[k] = right[j++];
                }
            }
        }

        void optimal_sort(int n) {
            vector <int> a(n);
            for (int i = 0; i < n; i++) a[i] = i + 1;
            solve(a);
            for (int i = 0; i < n; i++) cout << a[i] << " \n"[i == n - 1];
        }
        ```

### [1301. Bubbling Bubbles](https://acm.sjtu.edu.cn/OnlineJudge/problem/1301)

给定一个长度为 $n$ 的排列，元素标号为 $1 \dots n$。如果对这个排列进行冒泡排序，请输出每个元素在进行冒泡排序时，参与了多少次交换。

* 数据范围：$1 \leq n \leq 10^6$

??? Solution

    直接运行冒泡排序是 $O(n ^ 2)$ 的，且可以发现对于 $n,n-1,n-2,\\dots,1$ 这样的序列总能达到上界。因此，需要用别的方法完成这个计数过程。

    ??? info "答案每个元素参与的 $(i, j): i < j, a_i > a_j$ 的数量。"

        冒泡排序最终一定会排好整个序列，且只有在相邻为逆序时才会交换改变两个元素的相对位置。

    一个求上述答案的分治思路是：考虑把原数组从 $m = n / 2$ 分为两半 $[1, m], [m + 1, n]$，所有的 $(i, j)$ 被分为三类：

    * $1 \leq i < j \leq m$;
    * $m < i < j \leq n$;
    * $i \leq m < j$.

    前两类可以递归求解，我们只需要知道最后一种的两个情况：
    
    * 每个后半部分的数，在前半部分有多少个数比他大，和；
    * 每个前半部分的数，在后半部分有多少个数比他小。
    
    这个过程可以在归并排序的时候计数完成。例如，考虑在每次从后半部分取出一个数时，此时所有已经被归并的部分都比他小，所有仍未归并的部分都比他大。因此，这次归并中这个数需要计算的答案，其实就是前半还没有归并的数的数量，也即此时的 $m - i + 1$。

    当然，擅长数据结构的同学可能会想到使用能够单点修改，区间求和的树状数组或者线段树来实现这个题目。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        void solve(vector <int> &a, vector <int> &ans) {
            if (a.size() <= 1) return;
            vector <int> left(a.begin(), a.begin() + a.size() / 2);
            vector <int> right(a.begin() + a.size() / 2, a.end());
            solve(left, ans); solve(right, ans);
            auto cmp = [&] (auto i, auto j) {
                if (i == left.size()) return false;
                if (j == right.size()) return true;
                return left[i] < right[j];
            };
            for (size_t i = 0, j = 0, k = 0; k < a.size(); k++) {
                if (cmp(i, j)) {
                    a[k] = left[i++];
                    ans[a[k]] += j;
                } else {
                    a[k] = right[j++];
                    ans[a[k]] += left.size() - i;
                }
            }
        }

        int main() {
            int n;
            cin >> n;
            vector <int> a(n), ans(n);
            for (int i = 0; i < n; i++) cin >> a[i], a[i]--;
            solve(a, ans);
            for (int i = 0; i < n; i++) cout << ans[i] << (i == n - 1 ? '\n' : ' ');
        }
        ```
    === "Python"
        ```python
        def solve(a, ans):
            if len(a) <= 1: return
            mid = len(a) // 2
            left = a[:mid]
            right = a[mid:]
            solve(left, ans); solve(right, ans)
            i, j, k = 0, 0, 0
            while i < len(left) and j < len(right):
                if left[i] < right[j]:
                    ans[left[i]] += j
                    a[k] = left[i]
                    i += 1
                else:
                    ans[right[j]] += len(left) - i
                    a[k] = right[j]
                    j += 1
                k += 1
            while i < len(left):
                ans[left[i]] += j
                a[k] = left[i]
                i += 1; k += 1
            while j < len(right):
                ans[right[j]] += len(left) - i
                a[k] = right[j]
                j += 1; k += 1
        n = int(input())
        a = list(map(lambda x: int(x) - 1, input().split()))
        ans = [0] * n
        solve(a, ans)
        print(*ans)
        ```
    === "C++ (树状数组)"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        struct FenwickTree {
            vector<int> t;
            FenwickTree(int n) : t(n + 1, 0) {}
            void add(int i, int x) {
                for ( ; i < t.size(); i += i & -i) t[i] += x;
            }
            int sum(int i) {
                int ret = 0;
                for (; i > 0; i -= i & -i) ret += t[i];
                return ret;
            }
        };

        int main() {
            int n;
            cin >> n;
            vector<int> a(n + 1), ans(n + 1);
            for (int i = 1; i <= n; i++) cin >> a[i];
            FenwickTree t(n);
            for (int i = n; i >= 1; i--) {
                ans[a[i]] += i - a[i] + t.sum(a[i]) * 2;
                t.add(a[i], 1);
            }
            for (int i = 1; i <= n; i++) cout << ans[i] << (i == n ? '\n' : ' ');
        }
        ```

### [1299. Closest Pair](https://acm.sjtu.edu.cn/OnlineJudge/problem/1299)

给定 $n$ 个二维欧几里得平面上的点 $p_1, p_2, \dots, p_n$，请输出距离最近的两个点的距离的平方。

* 数据范围：$2 \leq n \leq 4\times 10^5$, $-10^7 \leq x_i, y_i \leq 10^7$

??? Solution

    使用归并排序可以在 $O(n \log n)$ 的时间内求解，但是懒一点的话多一个 $\log$ 实际运行也不会慢很多。
    === "C++ ($n \log ^ 2 n$)"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        const int64_t INF = 1e18;

        int64_t pow_2 (int64_t x) {
            return x * x;
        }

        struct point {
            int x, y;
            point (int _x, int _y) : x(_x), y(_y) {}
            int64_t distance_2 (const point & other) const {
                return pow_2(x - other.x) + pow_2(y - other.y);
            }
        };
        
        int64_t solve (const vector <point> &a, int l, int r) {
            if (r - l <= 1) return INF;
            int mid = (l + r) / 2;
            int64_t d = min(solve(a, l, mid), solve(a, mid, r));

            vector <point> strip;
            for (int i = l; i < r; i++) {
                if (pow_2(a[i].x - a[mid].x) < d) {
                    strip.push_back(a[i]);
                }
            }
            sort(strip.begin(), strip.end(), [](auto &u, auto &v) {
                return u.y < v.y;
            });

            for (int i = 0; i < strip.size(); i++) {
                for (int j = i + 1; j < strip.size(); j++) {
                    if (pow_2(strip[j].y - strip[i].y) >= d) break;
                    d = min(d, strip[i].distance_2(strip[j]));
                }
            }
            return d;
        }

        int main() {
            int n;
            cin >> n;
            vector <point> a;
            for (int i = 0; i < n; i++) {
                int x, y;
                cin >> x >> y;
                a.push_back(point(x, y));
            }
            sort(a.begin(), a.end(), [](auto &u, auto &v) {
                return u.x < v.x;
            });
            cout << solve (a, 0, n) << endl;
        }
        ```
    === "C++ ($n \log n$)"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        const int64_t INF = 1e18;

        int64_t pow_2 (int64_t x) {
            return x * x;
        }

        struct point {
            int x, y;
            point (int _x, int _y) : x(_x), y(_y) {}
            int64_t distance_2 (const point & other) const {
                return pow_2(x - other.x) + pow_2(y - other.y);
            }
        };

        // Inputs: 	A vector of points sorted by x-coordinate.
        // Return: 	The minimum squared distance between any two points,
        // 			and points are (merge-)sorted by y-coordinate on the way.
        int64_t solve (vector <point> &a) {
            if (a.size() < 2) return INF;
            vector <point> 	left(a.begin(), a.begin() + a.size() / 2),
                            right(a.begin() + a.size() / 2, a.end());
            int mid_x = a[a.size() / 2].x; // Important: a[] will be modified
            int64_t d = min(solve(left), solve(right));
            
            merge(	left.begin(), left.end(),
                    right.begin(), right.end(),
                    a.begin(), [](auto u, auto &v) {
                        return u.y < v.y;
                    } );

            vector <point> strip;
            for (auto &p : a) {
                if (pow_2(p.x - mid_x) < d) {
                    strip.push_back(p);
                }
            }

            for (int i = 0; i < strip.size(); i++) {
                for (int j = i + 1; j < strip.size(); j++) {
                    if (pow_2(strip[j].y - strip[i].y) >= d) break;
                    d = min(d, strip[i].distance_2(strip[j]));
                }
            }
            return d;
        }

        int main() {
            int n;
            cin >> n;
            vector <point> a;
            for (int i = 0; i < n; i++) {
                int x, y;
                cin >> x >> y;
                a.push_back(point(x, y));
            }
            sort(a.begin(), a.end(), [](auto &u, auto &v) {
                return u.x < v.x;
            });
            cout << solve (a) << endl;
        }
        ```


### [1982. Minimum Perimeter Triangle](https://acm.sjtu.edu.cn/OnlineJudge/problem/1982)

给定 $n$ 个二维欧几里得平面上的点 $p_1, p_2, \dots, p_n$，请选出三个不相同的点，构成周长最小的三角形。

* 数据范围：$3 \leq n \leq 4 \times 10^5$, $-10^7 \leq x_i, y_i \leq 10^7$, 答案绝对误差不超过 $10^{-5}$

??? Solution

    三角形边长的最大值超过了周长的一半，因此有和最近点对问题类似的复杂度分析和解法。

    !!! warning "注意递归边界"
        
        在下面使用了归并排序的代码中，将递归边界直接设置为 `if (a.size() < 3)` 是错误的，因为这么做函数返回时并没有保证 `a` 按照 y 轴排序，会导致后续计算错误。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        const double INF = 1e18;

        double pow_2 (double x) {
            return x * x;
        }

        struct point {
            int x, y;
            point (int _x, int _y) : x(_x), y(_y) {}
            double distance (const point & other) const {
                return sqrt(pow_2(x - other.x) + pow_2(y - other.y));
            }
        };

        double solve (vector <point> &a) {
            if (a.size() < 2) {
                return INF;
            }
            vector <point> 	left(a.begin(), a.begin() + a.size() / 2),
                            right(a.begin() + a.size() / 2, a.end());
            int mid_x = a[a.size() / 2].x;
            double d = min(solve(left), solve(right));
            merge(	left.begin(), left.end(),
                    right.begin(), right.end(),
                    a.begin(), [](auto u, auto &v) {
                        return u.y < v.y;
                    } );
            vector <point> strip;
            for (int i = 0; i < a.size(); i++) {
                if (abs(a[i].x - mid_x) < d / 2) {
                    strip.push_back(a[i]);
                }
            }
            for (int i = 0; i < strip.size(); i++) {
                for (int j = i + 1; j < strip.size(); j++) {
                    assert (strip[i].y <= strip[j].y);
                    double ijy = (strip[j].y - strip[i].y);
                    if (ijy >= d) break;
                    double ij = strip[i].distance(strip[j]);
                    if (ij >= d) continue;
                    for (int k = j + 1; k < strip.size(); k++) {
                        assert (strip[j].y <= strip[k].y);
                        double iky = (strip[k].y - strip[i].y);
                        if (iky * 2 >= d) break;
                        double ik = strip[i].distance(strip[k]);
                        double jk = strip[j].distance(strip[k]);
                        d = min(d, ij + ik + jk);
                    }
                }
            }
            return d;
        }

        int main() {
            int n;
            cin >> n;
            vector <point> a;
            for (int i = 0; i < n; i++) {
                int x, y;
                cin >> x >> y;
                a.push_back(point(x, y));
            }
            sort(a.begin(), a.end(), [](auto &u, auto &v) {
                return u.x < v.x;
            });
            cout << fixed << setprecision(9) << solve (a) << endl;
        }
        ```


### [1303. Subset Sum](https://acm.sjtu.edu.cn/OnlineJudge/problem/1303)

从 $n$ 个可能有重复的数字中取出一个非空子集，有 $2^n - 1$ 种取法。其中，每个数在 $2^{n-1}$ 个子集中出现。现在给你 $2^n-1$ 个数，请构造 $n$ 个整数，使得他们的非空子集的和与这 $2^n - 1$ 个数对应相同。

* 数据范围：$1 \leq n \leq 15$, $-10^6 \leq s_i \leq 10^6$

??? Solution

    在以下的所有讨论中，我们假设空集也被考虑在内，也即给输入的数组补一个 $0$。
    
    首先，我们给出一个对于有解时构造方案的大体思路：有解时能够构造出解。

    1. 确定 $n$ 个元素中其中一个元素的值。
    2. 将子集和分为有这个元素和没有这个元素的两部分，对后者进行递归。

    ??? question "如果所有元素都是正数，如何确定一个元素的值？"

        此时，子集和中最小正值就是一个元素。

    ??? question "如果所有元素都是正数，给定一个元素的值 $x$，如何将子集和分为两部分？"

        注意到所有集合之间存在一个配对关系：有 $x$ 和没有 $x$ 的集合配对。因此，我们尝试维护这个配对关系，并把集合分成有和没有的两部分。每次贪心选择出子集和中的最小值 $\min$，此时 $\min$ 一定代表一个不包含 $x$ 的子集（否则去掉 $x$ 可以更小），然后删掉这两个集合继续处理。由归纳关系，这样贪心匹配是正确的。

    ??? question "那么对于存在负数的情况……"

        可以用两种方法来解决这个问题。

        !!! example "转化为正数的情况"

            考虑子集和里的最大值和最小值：它们是所有正数的和和所有负数的和。我们可以给所有子集和减去最小的子集和，此时对于一个负元素 $-x$，我们可以视作元素 $x$：

            * 在 $-x$ 存在的集合里，不存在 $x$；
            * 在 $-x$ 不存在的集合里，存在 $x$。

            这样，问题就转化为了所有元素都是正数的情况。

        !!! example "考虑元素的绝对值"

            考虑类比正数的做法，我们可以发现子集和里最小值和次小值对应与正数做法里的 $0$ 和最小正值。此时，最小值和次小值的差值为有最小绝对值的元素的绝对值，但我们暂时还不知道正负。

            无论正负，我们都可以首先当做正数来划分集合。划分完成后，我们可以通过考虑哪边子集和存在 $0$——将其视为空集——来确定往哪个方向递归。如果两侧集合都有 $0$，则说明存在一个集合包含当前元素，且子集和为 $0$：在这种情况下，当前元素是正是负均是有解的，因为可以利用取反这个 $0$ 和的子集元素构造出两种方案。
    
    ??? question "如果存在元素是 $0$……"
        
        只需要在需要考虑最小正值的做法里，统一改为次小值减最小值，此时 $0$ 就可以被当成正数处理了。

    时间复杂度 $O(n \cdot 2 ^ n)$。

    === "C++"
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;

        optional<vector<int>> solve (vector <int> a) {
            if (a.size() <= 1) {
                if (a.size() == 1 && a[0] == 0)
                    return vector<int>();
                else
                    return nullopt;
            }
            int x = a[1] - a[0];
            vector <int> left, right;
            size_t j = 0;
            for (size_t i = 0; i < a.size(); i++) {
                if (j < right.size() && a[i] == right[j]) {
                    j++;
                } else {
                    left.push_back(a[i]);
                    right.push_back(a[i] + x);
                }
            }
            if (j == right.size()) { // all elements matched
                if (count(left.begin(), left.end(), 0)) {
                    if (auto ret = solve(left)) {
                        ret->push_back(x);
                        return ret;
                    }
                } else if (count(right.begin(), right.end(), 0)) {
                    if (auto ret = solve(right)) {
                        ret->push_back(-x);
                        return ret;
                    }
                }
            }
            return nullopt;
        }

        int main() {
            int n;
            cin >> n;
            vector <int> a(1 << n);
            for (int i = 1; i < (1 << n); i++) cin >> a[i];
            sort(a.begin(), a.end());
            if (auto ret = solve(a)) {
                cout << "YES\n";
                for (int i = 0; i < n; i++) cout << (*ret)[i] << " \n"[i == n - 1];
            } else {
                cout << "NO\n";
            }
        }
        ```
    === "Python"
        ```python
        def solve(a):
            if len(a) <= 1:
                if len(a) == 1 and a[0] == 0:
                    return []
                else:
                    return None
            x = a[1] - a[0]
            left, right = [], []
            j = 0
            for i in range(len(a)):
                if j < len(right) and a[i] == right[j]:
                    j += 1
                else:
                    left.append(a[i])
                    right.append(a[i] + x)
            if j == len(right): # all elements matched
                if left.count(0):
                    ret = solve(left)
                    if ret is not None:
                        ret.append(x)
                        return ret
                elif right.count(0):
                    ret = solve(right)
                    if ret is not None:
                        ret.append(-x)
                        return ret
            return None
        n = int(input())
        a = [0] + list(map(int, input().split()))
        a.sort()
        if ret := solve(a):
            print("YES")
            print(*ret)
        else:
            print("NO")
        ```

