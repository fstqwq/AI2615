### 使用 clang

macOS 里很可能已经有安装好的 `clang`。新版的 macOS 中默认编译器是 `clang`，而不是 `gcc`；并且命令行中的 `g++` 也很有可能被设置为了 `clang++` 的符号链接。

!!! info "clang 与 gcc 的区别"
    `clang` 采用 `libc++` 而不是 `libstdc++` 作为标准库。最大的影响可能是：

    * 无法使用 `#include <bits/stdc++.h>`。

    关于这一点，网上存在一些可行的解决办法，你也可以使用常规的头文件。

### 如果你还需要安装一个 gcc

```bash
brew upgrade
brew update
brew install gcc
```

请注意，如果之前 `gcc` 已经被设置为 `clang` 的符号链接，这样安装可能并不会覆盖掉 `clang`，而会安装一个类似名为 `gcc-10` 的新版本。