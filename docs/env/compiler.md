如果你使用 IDE，可能编译器已经伴随 IDE 安装好；部分操作系统也会默认安装编译器。



根据你的操作系统，编译器安装方式可能有所不同。在 macOS 里有额外注意事项，也请参考。

### 测试 GCC 是否已经安装

=== "Linux"
    按下 `CTRL` + `T` 打开一个终端。

=== "Windows"
    按下 `WIN` + `R`，输入 `cmd`，打开一个终端。

=== "macOS"
    !!! quote "🤖 New Bing"
        您好，这是必应。😊

        在 macOS 上打开终端的方法有多种，以下是一些常用的方法:

        - 在 Dock 上点击 Launchpad 图标，输入 Terminal，在搜索结果中点击 Terminal。
        - 在 Finder 中打开 /Applications/Utilities 文件夹，双击 Terminal。
        - 在 Spotlight 搜索中输入 Terminal，并按回车键。
        - 在 Finder 中选择前往 > 实用工具，在实用工具文件夹中双击 Terminal。
        
        希望这些信息对您有帮助。👍

在终端中，输入以下命令：

```bash
g++ --version
```

如果版本低于 7，或者提示命令未找到，那么你需要参考以下内容安装或者升级 GCC。


### 安装或升级编译器

=== "Linux"
    你可以使用 Linux 发行版中提供的包管理器安装编译器。

    我们将以 Ubuntu 为例，介绍如何安装编译器。

    ### Ubuntu: 安装 g++


    #### 更换为 SJTU 源

    境外的源可能会导致下载速度过慢，因此我们建议更换源为 SJTU 源，方法是将 `/etc/apt/sources.list` 中的 `archive.ubuntu.com` 全部替换为 `mirror.sjtu.edu.cn`。
    
    你可以使用以下命令完成这一操作。

    ```bash
    # 备份原有的 source.list
    sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    # 将 archive.ubuntu.com 替换为 mirror.sjtu.edu.cn
    sudo sed -i 's/archive.ubuntu.com/mirror.sjtu.edu.cn/g' /etc/apt/sources.list
    ```

    请注意，如果你安装的是中文系统，上述命令中的 `archive.ubuntu.com` 可能需要替换为 `cn.archive.ubuntu.com`。你可以使用以下命令查看你的系统中的源是什么。

    ```bash
    cat /etc/apt/sources.list | grep -v '^#'
    ```

    #### 安装 g++

    ```bash
    sudo apt update
    sudo apt install g++ -y
    ```

    安装完成后，你应当可以测试出 g++ 已经安装好了。

    #### 升级

    如果你的 GCC 版本比较老，以下命令可以升级所有已安装的软件。

    ```bash
    sudo apt update
    sudo apt upgrade -y
    ```

=== "Windows"

    ??? question "我的电脑上有编译器，为什么仍然无法在命令行里运行 g++?"
        请参考下面的 [添加编译器到环境变量](#添加编译器到环境变量)。

    Windows 下面我们推荐使用 MinGW 套件完成编译；或者使用 WSL 安装 Linux 子系统，并使用 Linux 的工具。

    ### Windows: MinGW

    以下我们以 [WinLibs](https://winlibs.com/) 为参考，介绍安装方法。WinLibs 里面提供了一系列工具，包括 `gcc`, `clang`, `gdb`, `lldb` 等。

    #### 下载与安装

    访问 [WinLibs](https://winlibs.com/) 网站，下载 `Release versions` 中的 Win64 压缩文件。

    将压缩文件中的 `mingw` 文件夹解压到一个目录，例如 `C:\mingw`。

    #### 添加编译器到环境变量
    为了能让系统找到编译器，你需要将编译器所在的目录加入 `PATH`，例如 `C:\mingw64\bin` 。

    你需要打开此电脑->属性->高级系统设置->环境变量->系统变量->`PATH`，然后点击编辑，将 `C:\mingw64\bin` 添加到最前面，可以参考 [OI Wiki](https://oi-wiki.org/tools/compiler/) 上提供的图示。

    在此之后，**重新**打开命令行，你应当能够成功测试 g++ 版本。

    ??? question "我的电脑上编译很慢，要花五秒以上。"
        请检查自己的电脑是否打开省电模式。如果问题还没有解决，可以参考 [这里](https://fstqwq.pw/speedup-cpp-compilation/)。

    ### Windows: WSL

    如果你安装了 WSL (Windows Subsystem for Linux)，你可以考虑使用 Linux 的方式编译运行程序。这是一种能让你在 Windows 上拥有几乎原生 Linux 的体验的方式。

    !!! TODO
        增加一个 WSL 教程。

=== "macOS"
    !!! warning "你的 g++ 可能是 clang++"
        新版 macOS 中默认编译器是 `clang`，而不是 `gcc`。
        
        甚至，命令行中的 `g++` 也被设置为了 `clang++` 的符号链接。

    ### macOS: 使用 clang

    macOS 里可能已经有安装好的 `clang`。如果没有，你可以通过以下方式安装。

    ```bash
    xcode-select --install
    ```
    !!! info "clang 与 gcc 的区别"
        `clang` 采用 `libc++` 而不是 `libstdc++` 作为标准库。最大的影响可能是：

        * 无法使用 `#include <bits/stdc++.h>`。

        关于这一点，网上存在一些可行的解决办法，你也可以使用常规的头文件。


    

    ### macOS: 如果你还需要安装一个 gcc

    ```bash
    brew upgrade
    brew update
    brew install gcc
    ```

    请注意，如果之前 `g++` 已经被设置为 `clang++` 的符号链接，这样安装可能并不会覆盖掉，而会安装一个类似名为 `g++-10` 的新版本。