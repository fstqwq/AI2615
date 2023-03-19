参考 Linux，或者使用 Xcode。

### 关于编译器

macOS 默认使用 clang 作为编译器（你在命令行中的 `g++` 大约已经是 `alias g++=clang++`），并且采用 `libc++` 而不是 `libstdc++` 作为标准库。

* 最大的影响可能是：无法使用 `#include <bits/stdc++.h>`。

关于这一点，网上存在一些可行的解决办法，你也可以使用常规的头文件。