对于部分同学来说，C++ 已经是非常久远的回忆，因此我们（针对做算法题）进行适当重温，以 OJ 题目为例讲解相关内容。

??? question "为什么要使用 C++，而不是 Python？"

    我们主要基于以下几点原因：

    - 在自动评测的机制下，
        - Python 无法区分出「复杂度正确而缓慢的实现」与「复杂度错误而高效的实现」。比如，在 Python 里手写的 $O(n)$ Median of Medians 是一定跑不过 $O(n \log n)$ 的用 C 实现的 `list.sort` 的。
        - Python 输入输出麻烦，而且非常容易触发异常。
    - 真正要熟练使用 Python，门槛并不比重新捡起 C++ 低。
        - Python 容易上手只是因为大多数情况下使用别人提供的库即可，不需要自己动手。
        - 在不熟练的情况下，debug Python 可能会非常麻烦。

[1294. A+B Problem](https://acm.sjtu.edu.cn/OnlineJudge/problem?problem_id=1294) 一个可能的实现是这样的：
```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int T;
    cin >> T;
    while (T--) {
        int a, b;
        cin >> a >> b;
        cout << a + b << endl;
    } 
}
```
#### 关于前两行
前两句表示的是使用（几乎）所有 C++ 头文件和命名空间 std。这两句话不符合很多工程规范，对此笔者做出一定的解释。你可以根据自己的喜好来做出自己的选择。
??? info "关于 bits/stdc++.h"
    使用这个 `libstdc++` 提供的“万能头文件”，你可以调用大量 STL 而不需要思考头文件的配置，减轻负担。

    例如，如果不使用该头文件，你可能需要考虑自己写下面的内容：
    ```cpp
    #include <iostream>
    #include <iomanip>
    #include <cstdio>
    #include <cstdlib>
    #include <ctime>
    #include <algorithm>
    #include <random>
    #include <vector>
    #include <queue>
    #include <map>
    #include <set>
    ```
    当然，`bits/stdc++.h` 有不少缺点：

    * 只有 GCC 和使用 `libstdc++` 的 clang 能够使用，MSVC 不支持。
    * 会导致编译速度降低。

    在上面两点并不致命的时候，这个「万能头文件」能带来许多便利。

??? info "关于 using namespace std"
    网上有大量关于这句话的讨论，比如 [Why “using namespace std” is considered bad practice](https://www.geeksforgeeks.org/using-namespace-std-considered-bad-practice/)。常见的缺点是：

    * 会导致导致某些变量名不可用，如 `map`, `y1`。
    * 在库函数与自己的函数命名冲突时，可能会导致令人困惑的编译错误和运行错误。

    使用这句话的原因主要是方便偷懒。在上下文无歧义时，更短的代码更方便编写和阅读。
    
    当然，还存在偷一半懒的方案。这样的做法在工程里也是规范的。

    * 只 `using` 自己用到的内容，如 `using std::cin, std::cout, std::ios, std::vector;`

#### cin/cout 运行缓慢的问题

提交这份代码，你可以发现这份代码会用到非常多的时间。这是因为：

1. `cin` / `cout` 与 stdio 同步导致其失去缓冲。
2. `endl` 会调用 `cout.flush()`，而这个操作非常耗时。

更多信息你可以在[这里](https://en.cppreference.com/w/cpp/io/manip/endl)获取到。我们尽量保证不需要解决这个问题就能通过对应题目。

??? info "如果你认为你的程序主要被这个原因拖慢了，并想解决"

    你可以用两种方法来解决这个问题：

    * 使用 C 的输入输出方式： `scanf`/`printf`。
    * 减少 `endl` 次数并禁用同步
        * 在输出行数较多时，使用 `'\n'` 而非 `endl`。
        * 在输入前添加
        ```cpp
        ios::sync_with_stdio(false);
        cin.tie(nullptr);
        ```
        来禁用同步。请注意，这种情况下你应当只使用 cin/(scanf, getchar) 中的一种，cout/(printf, puts, putchar) 中的一种。这是因为此时 `cin`/`cout` 会将内容输入输出到 buffer 里，导致与 stdio 失去同步。
    


#### 使用浮点数

有时，你可能需要使用浮点数（如 [1295. #include <random\>](https://acm.sjtu.edu.cn/OnlineJudge/problem?problem_id=1295)），并输出指定位数，默认的输出科学计数法的方式不能满足我们的要求。笔者更推荐使用 `printf` 来完成格式化输出，例如输出 3 位小数 `x` 可以这么做：

* `double`: `printf("%.3lf", x);`
* `long double`: `printf("%.3Lf", x);`
    
你也可以使用 `<iomanip>` 里提供的流操作来让 `cout` 输出固定小数：

* `cout << fixed << setprecision(3) << x;`

请注意，`float` 是 32 位浮点数，精度通常不能让人满意。因此，我们推荐任何时候都不要使用 `float`，大部分情况下使用 `double` 更好。

#### 使用 64 位整数
在数据规模达到 $2^{31} \approx 2.1 \times 10^9$ 时，`int` 会溢出，此时如果需要应当选择更大存储的整数。
通常情况下，使用 `unsigned long long` 和 `long long` 即可。尽管 C++ 标准中只要求 `long long` 是至少 64 位的，但是事实上所有编译器都会实现为 64 位。

```cpp
int main() {
    long long a, b;
    cin >> a >> b; // scanf("%lld %lld", &a, &b);
    cout << a + b << endl; // printf("%lld\n", a + b);
}
```