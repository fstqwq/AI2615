本题我们提供了代码模板和参考 Python 实现，你可以使用模板和参考代码完成该题。

### [1299. Closest pair](https://acm.sjtu.edu.cn/OnlineJudge/problem?problem_id=1299)

给定 $n$ 个二维欧几里得平面上的点 $p_1, p_2, \dots, p_n$，请输出距离最近的两个点的距离。

限制：$2 \leq n \leq 4\times 10^5, -10^7 \leq x_i, y_i \leq 10 ^ 7$。


#### Python 参考实现
这道题参考 Python 实现分治算法如下。
```python
class point:
    def __init__(self, x:int, y:int):
        self.x = x
        self.y = y
    def distance_2(self, other) -> int:
        return (self.x - other.x) ** 2 + (self.y - other.y) ** 2

def solve(a, l, r) -> int: # the square of closest distance between index range [l, r)
    if l + 1 >= r:
        return 2 ** 64 # -> INFINITY
    m = (l + r) // 2
    ret = min(solve(a, l, m), solve(a, m, r))
    strip = []
    for i in range(l, r):
        if (a[i].x - a[m].x) ** 2 < ret:
            strip.append(a[i])
    strip.sort(key=lambda p : p.y)
    for i in range(len(strip)):
        for j in range(i + 1, len(strip)):
            if (strip[i].y - strip[j].y) ** 2 >= ret:
                break
            ret = min(ret, strip[i].distance_2(strip[j]))
    return ret

n = int(input())
a = []
for i in range(n):
    x, y = map(int, input().split())
    a.append(point(x, y))
a.sort(key=lambda p : p.x)
print(solve(a, 0, n))
```
#### 转译为 C++
现在，考虑使用 C++ 的 `vector` 来代替 Python 的 `list` 来实现这个算法。

!!! warning "整数类型区别"
    对于 Python 来说，整数是自带高精度扩展的，但是对于 C++ 来说并不是。这道题的数据范围要求 C++ 实现时需要 64 位整数，并且其中无穷大的值也应当小于 $2^{64}$，因为有符号 64 位整数最大能表达 $2 ^ {63} - 1$。
    
    * `INF` 取值要充分大，但我们推荐通常略小于类型上限的一半，因为在类似最短路、动态规划算法里存在 `INF + INF` 的可能性。在这份代码里，我们取到 `LLONG_MAX` 也是合适的，这是因为我们只用 `INF` 来比大小。
    * 如果你觉得 `long long` 打起来非常长，中间有一个空格不好看，你可以考虑使用 `typedef long long LL` 并在之后用 `LL` 指代这个类型。

!!! info "更好（？）的实现"
    对于一般的分治算法题来说，使用一个全局数组 `a` 即可，不需要 `vector` 传为参数。

    但请注意，不要在函数开始传了一个数组参数，但实际上用的全局数组的内容。这样即使能通过本题，但函数本身并没有实现期望的功能，也会对读者带来困扰。

这里给出一个代码框架，请补全代码再提交。

!!! tip "你当然也可以实现一个更好的代码！" 

    你的实现不必和 Python 代码的思路一致。
    
    你可以修改代码框架为你想要的形状，也可以从头重写。

    例如，Python 代码的实现复杂度是 $O(n \log ^ 2 n)$。你可以优化为 $O(n \log n)$ 的实现。

```cpp
#include <bits/stdc++.h>
using namespace std;

const long long INF = 1e18;

/* 
   NOTE:
   std::pow is for floating point numbers.
   For integers, it's better to implement one.
*/

// FIXME: reimplement with long long int
int pow_2 (int x) {
    return x * x;
}

struct point {
    int x, y;
    point (int _x, int _y) : x(_x), y(_y) {}
    long long distance_2 (const point & other) const {
        // TODO: implement distance_2
    }
};

long long solve (vector <point> &a, int l, int r) {
    // TODO: implement solve    
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