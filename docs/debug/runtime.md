调试程序错误总是令人苦恼的。这里我们主要介绍几种不依赖于 IDE 的调试方法，以期起到一定普适的介绍。

我们将以这份代码为例，尝试找到其中出现的问题。
```cpp
#include <bits/stdc++.h>
using namespace std;
int a[10];
void test (int x) {
    if (x > 1e6) return;
    a[x] = x;
    test (x + 1);
}
int main() {
    test (1);
}
```
以上代码将会递归调用 `test` 函数 $10^6$ 次，但是从第 10 次开始访问 `a` 数组就越界了。

### 输出调试与断言

你可以增加输出与断言并重新运行的方法来尝试找到程序错误，有时这种方法也叫打桩测试。

尽管类似的功能可以通过调试器设置断点并输出的方法完成，但是这种方法在编译足够快时效率更高，更方便离线地（对于一段静态内容进行分析）而不是在线地（和程序进行大量交互）查错。

对于上述代码，我们可以在可能出错的代码前后插入输出语句，来看对应运行中途过程是否是想要的。以上面代码为例：
```cpp
#include <bits/stdc++.h>
using namespace std;
int a[10];
void test (int x) {
    cerr << "test " << x << endl;
    if (x > 1e6) return;
    a[x] = x;
    test (x + 1);
}
int main() {
    test (1);
}
```
此时，你的程序会在若干行 `test` 后被杀死，你可以直接针对输出 $x$ 的信息寻找问题。

你也可以对一些条件进行断言，来判断程序是否按照预期进行。例如，我们可以在 `test` 函数中加入断言，来判断 `x` 是否会超过数组的范围：
```cpp
#include <bits/stdc++.h>
using namespace std;
int a[10];
void test (int x) {
    assert (x < 10);
    if (x > 1e6) return;
    a[x] = x;
    test (x + 1);
}
int main() {
    test (1);
}
```
此时，你的程序会在断言失败时输出以下内容：
```bash
Assertion failed: x < 10, file a.cpp, line 5
```
断言对于寻找一些简单的错误非常有用，同时由于其会在 OJ 上面显示 Runtime Error，因此也可以通过增删测试来从 OJ 结果里获得更多信息。


### GDB 调试

调试器在运行时错误方面（特别是递归）拥有优势。除了 IDE 提供了可视化调试工具，你还可以使用 `gdb` 进行命令行调试。如果需要 `gdb` 输出信息，你可能需要打开 `-g` 编译开关。

```cpp
#include <bits/stdc++.h>
using namespace std;
int a[10];
void test (int x) {
    if (x > 1e6) return;
    a[x] = x;
    test (x + 1);
}
int main() {
    test (1);
}
```
以上代码将会递归调用 `test` 函数 $10^6$ 次，但是从第 10 次开始访问 `a` 数组就越界了。一个参考的调试过程如下：
```bash
~$ g++ a.cpp -o a -g
~$ gdb a
...
Reading symbols from a...done.
(gdb) r
Starting program: ~/a

Program received signal SIGSEGV, Segmentation fault.
0x000000000800076b in test (x=1008) at a.cpp:6
6               a[x] = x;
(gdb) p x
$1 = 1008
(gdb) back
#0  0x000000000800076b in test (x=1008) at a.cpp:6
#1  0x000000000800077b in test (x=1007) at a.cpp:7
#2  0x000000000800077b in test (x=1006) at a.cpp:7
#3  0x000000000800077b in test (x=1005) at a.cpp:7
#4  0x000000000800077b in test (x=1004) at a.cpp:7
#5  0x000000000800077b in test (x=1003) at a.cpp:7
#6  0x000000000800077b in test (x=1002) at a.cpp:7
#7  0x000000000800077b in test (x=1001) at a.cpp:7
#8  0x000000000800077b in test (x=1000) at a.cpp:7
#9  0x000000000800077b in test (x=999) at a.cpp:7
#10 0x000000000800077b in test (x=998) at a.cpp:7
#11 0x000000000800077b in test (x=997) at a.cpp:7
#12 0x000000000800077b in test (x=996) at a.cpp:7
#13 0x000000000800077b in test (x=995) at a.cpp:7
#14 0x000000000800077b in test (x=994) at a.cpp:7
#15 0x000000000800077b in test (x=993) at a.cpp:7
#16 0x000000000800077b in test (x=992) at a.cpp:7
#17 0x000000000800077b in test (x=991) at a.cpp:7
#18 0x000000000800077b in test (x=990) at a.cpp:7
#19 0x000000000800077b in test (x=989) at a.cpp:7
#20 0x000000000800077b in test (x=988) at a.cpp:7
#21 0x000000000800077b in test (x=987) at a.cpp:7
#22 0x000000000800077b in test (x=986) at a.cpp:7
#23 0x000000000800077b in test (x=985) at a.cpp:7
#24 0x000000000800077b in test (x=984) at a.cpp:7
#25 0x000000000800077b in test (x=983) at a.cpp:7
#26 0x000000000800077b in test (x=982) at a.cpp:7
#27 0x000000000800077b in test (x=981) at a.cpp:7
#28 0x000000000800077b in test (x=980) at a.cpp:7
---Type <return> to continue, or q <return> to quit---q
Quit
(gdb) quit
A debugging session is active.

        Inferior 1 [process 15145] will be killed.

Quit anyway? (y or n) y
~$
```

常用的指令有以下几个：

* `b x`：在第 `x` 行设置断点。
* `r`：运行程序。你可以使用 `r < input.txt` 来指定输入文件。
* `p x`：输出当前语句附近 `x` 这个表达式的值。`x` 可以是一个变量名，也可以是一句计算语句、一个函数调用。
* `back`：输出当前的递归栈帧情况。
* `up`：递归栈往上一层。
* `down`：递归栈往下一层。
* `c`：continue，继续运行。通常对断点使用，如果是程序错误，continue 会导致程序被杀死。

GDB 功能很多，但是只需学会很小的子集便能开始发挥作用了。你可以亲自调试一下上面的代码，或者参考网络资料进行学习。

### Sanitizer 调试

有时，我们使用 GDB 也看不出具体到底哪里出现问题了，这是因为 GDB 只有在程序出现很严重的问题的时候才能捕获到信号，导致真正的犯罪现场已经被破坏掉了。例如，上述程序中 `x = 10` 时就已经越界了，但是程序直到 `x` 更大的时候才会触发 `SIGSEGV`。

部分编译器[^com]为类似整数溢出、数组越界的情况提供了 sanitizer 调试工具。我们仍然以上面的代码为例，使用以下编译命令编译：

[^com]: Linux 下应该都是支持的，其他的得看情况。

```bash
g++ a.cpp -o a -g -fsanitize=address -fsanitize=undefined
```
运行，可以得到类似以下的 log：
```bash
~$ ./a
test 1
test 2
test 3
test 4
test 5
test 6
test 7
test 8
test 9
test 10
a.cpp:7:5: runtime error: index 10 out of bounds for type 'int [10]'
a.cpp:7:7: runtime error: store to address 0x7f0e0b006cc8 with insufficient space for an object of type 'int'
0x7f0e0b006cc8: note: pointer points here
 09 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00
              ^
=================================================================
==15175==ERROR: AddressSanitizer: global-buffer-overflow on address 0x7f0e0b006cc8 at pc 0x7f0e0ae02e75 bp 0x7fffd0fd7800 sp 0x7fffd0fd77f0
WRITE of size 4 at 0x7f0e0b006cc8 thread T0
    #0 0x7f0e0ae02e74 in test(int) ~/a.cpp:7
    #1 0x7f0e0ae02eb8 in test(int) ~/a.cpp:8
    #2 0x7f0e0ae02eb8 in test(int) ~/a.cpp:8
    #3 0x7f0e0ae02eb8 in test(int) ~/a.cpp:8
    #4 0x7f0e0ae02eb8 in test(int) ~/a.cpp:8
    #5 0x7f0e0ae02eb8 in test(int) ~/a.cpp:8
    #6 0x7f0e0ae02eb8 in test(int) ~/a.cpp:8
    #7 0x7f0e0ae02eb8 in test(int) ~/a.cpp:8
    #8 0x7f0e0ae02eb8 in test(int) ~/a.cpp:8
    #9 0x7f0e0ae02eb8 in test(int) ~/a.cpp:8
    #10 0x7f0e0ae02ed0 in main ~/a.cpp:11
    #11 0x7f0e085a1c86 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21c86)
    #12 0x7f0e0ae02a99 in _start (~/a+0x2a99)

0x7f0e0b006cc8 is located 0 bytes to the right of global variable 'a' defined in 'a.cpp:3:5' (0x7f0e0b006ca0) of size 40
SUMMARY: AddressSanitizer: global-buffer-overflow ~/a.cpp:7 in test(int)
Shadow bytes around the buggy address:
  0x0fe2415f8d40: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0fe2415f8d50: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0fe2415f8d60: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0fe2415f8d70: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0fe2415f8d80: 00 00 00 00 00 00 00 00 00 00 00 00 01 f9 f9 f9
=>0x0fe2415f8d90: f9 f9 f9 f9 00 00 00 00 00[f9]f9 f9 f9 f9 f9 f9
  0x0fe2415f8da0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0fe2415f8db0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0fe2415f8dc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0fe2415f8dd0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0fe2415f8de0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
==15175==ABORTING
```

尽管后面有一些看起来昏昏沉沉的内容，但是没关系！我们可以看到第一句：
```bash
a.cpp:7:5: runtime error: index 10 out of bounds for type 'int [10]'
```
说得很对，马上改代码。

### STL 容器调试：_GLIBCXX_DEBUG
    
调试时，如果遇到访问 STL 容器越界但无法准确定位错误的问题，还可以在你的代码头文件前添加 `#define _GLIBCXX_DEBUG` 来将容器替换成容易调试（但速度可能会显著变慢）的版本。这个版本中的一些容器会积极主动地检查一些操作的前置条件是否满足，从而让错误尽早暴露。比如，对于以下的代码
```cpp
vector <int> a(n);
for (int i = 1; i <= n; i++) {
	a[i] = i;
}
```
大多数时候，写入越界一个 int 的 `a[n]` 并不会报错。但是，如果使用 debug 容器，会收到以下运行时报错：
    
```
.../debug/vector:442:
In function:
    std::debug::vector<_Tp, _Allocator>::reference std::debug::vector<_Tp,
    _Allocator>::operator[](size_type) [with _Tp = int; _Allocator =
    std::allocator<int>; reference = int&; size_type = long long unsigned
    int]

Error: attempt to subscript container with out-of-bounds index 5, but
container only holds 5 elements.

Objects involved in the operation:
    sequence "this" @ 00000031069FF690 {
      type = std::debug::vector<int, std::allocator<int> >;
    }
```
如果你使用的是 libc++ 的编译器，可以使用 `#define _LIBCPP_ENABLE_ASSERTIONS 1` 来达到类似效果。详见[提供该项内容的同学在水源的介绍](https://shuiyuan.sjtu.edu.cn/t/topic/127129/106)。

### 开栈

尽管在 OJ 上配置了无限栈，但是在本地调试时，你可能会遇到爆栈的问题。

在 Linux 下，默认运行环境下栈是只有若干 MB 的，这在比如 dfs 深度大时会导致栈溢出。大多数 OJ 采用了无限栈，在 Linux 下要做到这一点，你可以使用以下命令（可能需要 `sudo`）
```bash
ulimit -s unlimited
```
请注意，如果你是实体 Linux 机，这么做可能导致死递归的时候占用非常大的内存，并导致系统由于不停交换内存死机。通常，将 `unlimited` 替换为 `104857600` （100MB）可能是合适的。 

Windows 程序栈大小在编译时确定，你可以向 GCC 的编译选项添加`-Wl,-stack=104857600` 达到目的。
    
MSVC 开栈指令不同；macOS 开栈方式也不同。请参考网络资料。