你可以参考 Linux 下的方法，使用使用命令行进行编译运行。不过，大多数同学会选择使用 IDE 并配置一键编译脚本。对于已经有合适环境配置的同学，只需要可以参考 Linux 下的编译命令配置警告等等即可。

### 配置编辑器 / IDE

通常大家会选择 IDE 编辑代码，主要有以下几种选择。

#### VSCode
VSCode 通常是一个中庸的选择：你可能会更喜欢使用这个普及率高，插件多，相对轻量级的编辑器。

* 有同学说 [VSCode 相比 CLion 省电一半](https://shuiyuan.sjtu.edu.cn/t/topic/127129/62)。

VSCode 的 C++ 插件需要一些配置，请参考网络资料。

#### DevC++

曾有的主流学习 C++ 的 IDE 是 DevC++。但是 DevC++ 官方已经不再维护，网上流传版本多是老版本。

对于老版本 DevC++，请考虑开启自动保存并更换编译器；或者使用一个较新的维护分支，[比如](https://github.com/Embarcadero/Dev-Cpp/releases) 。

类似 DevC++ 的 IDE 还有 Code::Blocks。

#### CLion

有同学[强烈推荐](https://shuiyuan.sjtu.edu.cn/t/topic/127129/13) CLion：一款提供了大量工具的现代化商业软件，并且交大目前提供了正版。

如果你想拥有开箱即用的体验，或者未来考虑进行更大规模的 C++ 开发项目，CLion 是一个不错的选择。

#### Visual Studio
你可以使用 Visual Studio 来完成题目，但是如下列例子的一些问题，请参考网络资料。

* MSVC 无法编译带有 `#include <bits/stdc++.h>` 的文件。
* MSVC 需要额外的设置来直接使用 `scanf`，`printf`。
* MSVC 无法使用变量长度数组。

#### 其他

其他常见的选择有 vim, emacs, notepad++, gedit 等。

选一个你喜欢的、习惯的就好。但是，助教可能不会擅长你的工具，请考虑自己处理相关情况。

更多关于工具选择的信息可以参考 [OI-Wiki](https://oi-wiki.org/tools/)。

### 配置编译器

如果你的 IDE 没有提供编译器，那么你可以考虑下载安装 MinGW-w64 项目提供的 GCC 编译器。实践上来说，笔者更喜欢 [winlibs](https://winlibs.com/) 套件，该套件提供了基于 `libstdc++` 的 `GCC` 和 `clang`。

下载文件解压到一个你喜欢的目录后，将 bin 目录加到系统 PATH 里即可。具体安装过程可以参考 [OI Wiki](https://oi-wiki.org/tools/compiler/)。

安装完成后，你应当可以在 PowerShell 或者 cmd 里使用 `g++ --version` 或者 `clang++ --version` 查看编译器版本。

* 为了保证不会因为编译器过旧产生各种历史遗留问题，你的编译器版本不应该低于 7，这通常意味着他是 2017 年之后发布的。

### 关于 WSL

如果你安装了 WSL (Windows Subsystem for Linux)，你可以考虑使用 Linux 的方式编译运行程序。这是一种能让你在 Windows 上拥有几乎原生 Linux 的体验的方式。

* 除非你理解 WSL2 的文件系统缺陷，推荐使用 WSL1。

### 关于 PowerShell

PowerShell 的 pipe 输入输出语法不一样，因此没法 `a.exe < input.txt > output.txt`。

笔者不擅长 PowerShell，因此推荐使用 `cmd`，可以直接使用上面的命令。你可以在打开的 PowerShell 窗口里输入 cmd 来让 PowerShell 成为 cmd 的套皮人。
