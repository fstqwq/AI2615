
你需要在一个编辑器中编写 C++ 代码，使用编译器编译为可执行文件，然后运行；运行中为了找到错误，你可能还需要调试器。

许多 IDE (Integrated Development Environment，集成开发环境) 可以帮助一次搞定这些工作，你也可以使用一些工具合作完成这些工作。

无论你是否拥有一个编写 C++ 的环境，我们都推荐你先阅读 [使用命令行编译运行](commandline.md) 的方法。

如果你没有一个可以进行 C++ 编程的环境，这一章将也会介绍一些常用的 C++ 编程环境。以下是我们推荐的环境：


=== "Linux"
    * 编辑器: [VSCode](./editor/vscode.md) / gedit / [Vim](./editor/vim.md)，编译器: GCC
    * IDE: [CLion](./editor/clion.md)

=== "Windows"
    * 编辑器: [VSCode](./editor/vscode.md)，编译器: GCC-MinGW, 或 WSL:GCC
    * IDE: [Dev-C++ / Code::Blocks](./editor/devcpp.md)
    * IDE: [CLion](./editor/clion.md)

    你也可以参考 [ACM 班程序设计 - 环境配置教程（2021 年）](https://acm.sjtu.edu.cn/wiki/%E6%96%87%E4%BB%B6:%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE%E6%95%99%E7%A8%8B.pdf) 来安装 WSL，CLion。

    !!! question "为什么不推荐使用 MSVC (Visual Studio)？"
        MSVC 编译器和 GCC 的编译器有一些不同。同时，从头开始安装 Visual Studio 是一个非常复杂的过程。
        
        当然，你可以使用 Visual Studio 来完成题目，但是如下列例子的一些问题，请参考网络资料。
        
        * MSVC 无法编译带有 `#include <bits/stdc++.h>` 的文件。
        * MSVC 需要额外的设置来直接使用 `scanf`，`printf`。
        * MSVC 无法使用变量长度数组。

=== "macOS"
    * 编辑器: [VSCode](./editor/vscode.md) / [Vim](./editor/vim.md)，编译器: GCC
    * IDE: XCode
    * IDE: [CLion](./editor/clion.md) 