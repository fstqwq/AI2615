Windows 下面我们推荐使用 MinGW 套件完成编译；或者使用 WSL 安装 Linux 子系统，并使用 Linux 的工具。


??? question "为什么不推荐使用 MSVC (Visual Studio)？"
    MSVC 编译器和 GCC 的编译器有一些不同，因此在使用 MSVC 编译器编译时，可能会出现一些意想不到的错误。同时，安装 Visual Studio 是一个非常复杂的过程。
    
    当然，你可以使用 Visual Studio 来完成题目，但是如下列例子的一些问题，请参考网络资料。
    
    * MSVC 无法编译带有 `#include <bits/stdc++.h>` 的文件。
    * MSVC 需要额外的设置来直接使用 `scanf`，`printf`。
    * MSVC 无法使用变量长度数组。


### MinGW

以下我们以 [WinLibs](https://winlibs.com/) 为参考，介绍安装方法。WinLibs 里面提供了一系列工具，包括 `gcc`, `clang`, `gdb`, `lldb` 等。


#### 测试 GCC 是否已经安装

使用 Win + R 打开运行窗口，输入 `cmd` 打开命令提示符，输入以下命令：
    
```bash
g++ --version
```

如果你的 g++ 版本低于 7，或者 `command not found`，那么你需要参考以下内容安装 g++。

??? question "我的电脑上有编译器，为什么仍然无法在命令行里运行 g++?"
    请参考后文将编译器的目录加入环境变量 PATH。


#### 下载与安装

访问 [WinLibs](https://winlibs.com/) 网站，下载 `Release versions` 中的 Win64 压缩文件。

将压缩文件中的 `mingw` 文件夹解压到一个目录，例如 `C:\mingw`。

#### 添加编译器到环境变量
为了能让系统找到编译器，你需要将 `C:\mingw64\bin` 加入 `PATH`。

你需要打开此电脑->属性->高级系统设置->环境变量->系统变量->`PATH`，然后点击编辑，将 `C:\mingw64\bin` 添加到最前面，可以参考 [OI Wiki](https://oi-wiki.org/tools/compiler/) 上提供的图示。

在此之后，重新打开命令行，你应当能够成功测试 g++ 版本。

### WSL

如果你安装了 WSL (Windows Subsystem for Linux)，你可以考虑使用 Linux 的方式编译运行程序。这是一种能让你在 Windows 上拥有几乎原生 Linux 的体验的方式。

!!! info "除非你理解 WSL2 的文件系统缺陷，推荐使用 WSL1。"

!!! TODO
    增加一个 WSL 教程。