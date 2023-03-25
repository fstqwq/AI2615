### 目标：像写 Python 一样写 C++

不少同学会抱怨 Python 能非常方便处理的事情在 C++ 里全部需要自己写。事实上，你可以通过一些操作来让你的 C++ 在某些地方看起来像 Python 一样便利。

当然，C++ 和 Python 在很多地方仍然是本质不一样的，在 [最近点对](/divide_and_conqure/closest) 的翻译代码例子中我们可以体会到一部分，但这并不影响我们写出更「偷懒」而简短的代码。

熟悉这些内容后，你可以在 [1786. Ctrl + R](https://acm.sjtu.edu.cn/OnlineJudge/problem/1786) 一题里测试常用的 `string`, `vector`, `map` 的用法。这是一道关于字符串处理的题。不使用 STL 的话，C 语言相关的字符串处理及其相关的数据结构都较为繁琐；然而，使用 STL 可以在几行代码内轻松搞定。以下是一份参考实现：

```cpp
int main() {
	int n;
	cin >> n;
    vector <string> S;
	for (int i = 0; i < n; i++) {
		string s;
		cin >> s;
		S.push_back(s);
	}
    bool flag = true;
    map <string, string> f;
	for (auto s : S) {
        string t;
        cin >> t;
		if (f.count(s) && f[s] != t) {
            flag = false;
            break;
		}
		else f[s] = t;
	}
	cout << (flag ? "Yes\n" : "No\n");
}
```

### STL

STL 里提供了大量模板类与函数。你可以在 cppreference 上查询到更多关于 STL [容器](https://zh.cppreference.com/w/cpp/container) 与 [算法](https://zh.cppreference.com/w/cpp/algorithm) 的内容。

#### vector
`vector` 提供了类似 Python `list` 的功能。
```cpp
int main() {
    vector <int> a;
    int n;
    cin >> n;
    for (int i = 0; i < n; i++) {
        int x;
        cin >> x;
        a.push_back(x);
    }
    int sum = 0;
    for (auto x : a) 
        sum += x; // for (size_t i = 0; i < a.size(); i++) sum += a[i];
    cout << sum << endl;
}
```

* `vector.push_back(x)` = `list.append(x)`
* `vector.pop_back()` = `list.pop()`
* `vector.size()` = `len(list)`
* 你可以使用 `vector <int> a(n)` 申请一个大小为 $n$，初始化为默认（即 $0$）的 `vector<int>`。
    * 你可以传入第二个参数来指定初始值。
* 请注意，C++ 中默认按照**值传递**，而 Python 中默认**引用传递**。
    * 这意味着将 vector 作为参数时，应当使用类似 `vector<int>&` 的手段来实现类似 `int[]` 和 Python `list` 的浅拷贝效果。
* `vector` 仅能接受一种类型的元素。
* 笔者写代码时，一维临时数组更喜欢使用 `vector`，原因是代码看起来会干净一点。
* [不要使用 `vector <bool>`](https://isocpp.org/blog/2012/11/on-vectorbool)。
    * 要达到类似目的，考虑 `vector <char>`。

#### map & set
`map` 提供了类似有序的 Python `dict`：

```cpp
int main() {
    map <int, int> dict;
    dict[1] = 1;
    dict[123456789] = 2;
    cout << dict[1] << " " << dict[2] << " " << dict[123456789] << endl;
    // outputs: 1 0 2
    map <string, int> strdict;
    strdict["hello"] = 1;
}
```

你可以直接使用下标访问的方式访问元素内容。任何可以用小于号比较大小的内容都可以作为 key。对于自定义类，重载小于号即可。

* `map` 下标访问到不存在的 key 时，会返回默认构造的值。
    * 也就是说，访问不存在的 key 时，对于 value 类型为 `int` 的 `map` 会插入 $(\mathrm{key}, 0)$，并返回这个 value 的引用。
    * 相比之下，Python 的 `dict` 会抛出 `KeyError`。
* Python `dict` 其实是 C++ 中的 `unordered_map`，也即实现的是 hash table 的无序字典。但是 `unordered_map` 对于非整型与字符串的 key 支持比较麻烦，我们这里略过。
* 一个典型的用途是将 `map` 当做无限下标但是只占用 $O(\mathrm{size})$ 的数组。
    * 例如，对于 $n = m = 10^5$ 的图，使用邻接矩阵无法存储。但是，可以使用 `unordered_map <int, int> G[N];` 以额外常数作为开销替换掉 `G[N][N]`，同时提供遍历出边的功能。 

类似 `map`, `set` 提供了有序的 `Set()` 功能。
```cpp
int main() {
    set <int> s;
    s.insert (1); s.insert (1);
    cout << s.size() << " " << s.count(0) << " " << s.count(1) << endl;
    // outputs: 1 0 1
    s.erase(1);
    cout << s.size() << " " << s.count(0) << " " << s.count(1) << endl;
    // outputs: 0 0 0
}
```

* 事实上，`map` 和 `set` 的实现用的是同一个红黑树。在 `map` 中，你也可以使用 `map::insert(pair <key_type, value_type>)` 来插入内容。

#### priority_queue
`priority_queue` 提供了基于小于号比较的大根堆实现。

```cpp
int main() {
    vector <int> a = {1, 3, 2, 4, 5};
    priority_queue <int> q;
    for (auto x : a) q.push(x);
    while (!q.empty()) {
        cout << q.top() << endl; // 5 4 3 2 1
        q.pop();
    }
}
```

如果你想使用大于号（也即小根堆），请使用 `priority_queue <int, vector <int>, greater <int>>`.

#### 其他数据结构

* `string` 实现了支持简单操作的字符串，其中加号重载为字符串连接，小于号为字典序。
* 常用的 STL 类还有 `queue`, `stack`, `bitset` 等等。
* 不推荐使用 C++ 的 `list`，原因是不好用。
    * 多数算法里可能用到的动态数组功能都可以用 `vector` 代替。

#### sort

`sort` 可以帮你对可以随机访问的数据结构（数组，`vector`）实现排序算法，默认按照小于号从小到大排序。

```cpp
sort(a.begin(), a.end()); // for vector
sort(a, a + n); // for array, sort within [a, a + n)
```
如果你想对于自定义类排序，请重载小于号，或者使用自定义比较函数：
```cpp
bool cmp(int x, int y) {
    return x > y;
}
...
sort(a.begin(), a.end(), cmp);
```
或者使用 [Lambda 表达式](#lambda-表达式)：
```cpp
sort(a.begin(), a.end(), [](int x, int y) {return x > y;});
```
如果你想使用大于号排序，也可以使用 `sort(a.begin(), a.end(), greater<int>())`。

#### random

`<random>` 库中提供了比较现代的随机数生成器。相比起 `rand()` 函数，随机库中随机性质更好，并且跨平台稳定。
```cpp
mt19937 rng(SEED);
int my_rand(int l, int r) { // [l, r]
    return int(rng() % (r - l + 1)) + l;
}
```
你也可以使用 `shuffle(a.begin(), a.end(), rng);` 来完成随机打乱一个序列的功能。

### 现代 C++ 语法

许多现代 C++ 语法像是「魔法」一样，与其他特性一样构成了晦涩难懂的 C++ 标准。不过，我们可以使用其中比较方便的一些来为我们所用。

以下内容均需要 C++11 以上，部分需要 C++14/17。你可能需要添加 `-std=c++17` 编译选项。

#### [auto](https://zh.cppreference.com/w/cpp/language/auto)

无论到底是什么类型，Python 使用者都不需要知道他叫什么，只需要使用起提供的功能就行了；C++ 是充斥着模板的静态类型语言：对一个 `set <int> s`，你可能需要用 `set<int>::iterator` 来称呼 `s.begin()` 的类型。但是，从 C++11 开始，你可以使用 `auto` 来称呼它：
```cpp
auto it = s.find(x);
if (it != s.end()) {
    s.erase(it);    
}
```
这对于 lambda 函数等表达类型有困难的内容有奇效：
```cpp
auto cmp = [] (int x, int y) {return x > y;}
```
或者只是想偷懒，不打字了，也可以。
```cpp
vector <int> solve () {
    ...    
}
int main() {
    ...
    auto ret = solve();
}
```

#### [Range-based for](https://zh.cppreference.com/w/cpp/language/range-for)
正如之前我们访问 vector 的例子，从 C++11 开始，你可以使用以下方式遍历 STL 容器的元素：
```cpp
for (auto i : a) {
    ...    
}
```
类似于
```python
for i in a:
    ...
```
如果用这种方式访问 map，得到的 `i` 的类型是 `pair <key_type, value_type>`.
    
在 auto 后面添加 `&` 表示是对应值的引用，可以直接修改作用于原容器元素；不加则会复制一遍。

这种语法对于表达 for each 的情况非常好用。比如，如果用 `vector <int> G[N]` 来实现邻接表，访问一个顶点的出边可以：
```cpp
for (auto v : G[u]) {
    // do sth with u->v
}
```
#### [Structured binding](https://zh.cppreference.com/w/cpp/language/structured_binding)

采用 C++17 中的结构化绑定语法，我们可以通过下面的方式访问 `map`：

```cpp
for (auto [key, value] : a) {
    ...
} 
```

类似于

```python
for key, value in a.items():
    ...
```

类似的，对于任意 pair，你都可以通过 `auto [first, second] = pair` 进行解构，还可以使用 `auto&` 获取引用。

#### [Lambda 表达式](https://zh.cppreference.com/w/cpp/language/lambda)

Lambda 表达式可以让你在代码中生成一个局部的函数，通常来说，你可能会在自定义 `sort` 的比较函数时用到。这里我们不讲解具体他是如何工作的，请参考以下代码来使用。
```cpp
struct point {
    int x, y;
}
int main() {
    vector <point> a;
    ... // input n points
    // sort by x
    sort(a.begin(), a.end(), [](auto u, auto v) {
            return u.x < v.x;
        });
    // sort by y
    sort(a.begin(), a.end(), [](auto u, auto v) {
            return u.y < v.y;
        }); 
    vector <int> indices;
    for (int i = 0; i < n; i++) indices.push_back(i);
    // sort indices w.r.t. x coordinates of points
    // [&] for capture local variables
    sort(indices.begin(), indices.end(), [&](int i, int j) {
        return a[i].x < a[j].x;
    }); 
}
```