当「一键编译运行」不能满足你的需求的时候，你可以直接通过命令自己控制编译运行的行为。

如果你需要安装编译器，请参考本章编译器配置中的关于你的系统的内容。


### 编译运行
假设有一个程序 `a.cpp`，内容如下。

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int a, b;
    cin >> a >> b;
    cout << a + b << endl;
}
```
你可以使用以下命令，来编译名为 `a.cpp` 的程序到二进制程序 `a`，并运行：
=== "Linux / macOS"
    ```bash
    g++ a.cpp -o a -g -Wall -std=c++17
    ./a
    ```
=== "Windows"
    ```cmd
    g++ a.cpp -o a -g -Wall -std=c++17
    .\a.exe
    ```


笔者解释一下这里的编译命令：

* `-g` 表示开启调试。这意味着你可以使用 `gdb` 等工具来调试他。
* `-Wall` 表示开启大多数警告。警告可以有效避免低级错误，我们举例来说：
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    int factorials(int n) {
        if (n <= 1) return 1;
        else factorials(n - 1) * n; // oops, i forgot return
    }

    int main() {
        cout << factorials(5) << endl;
    }
    ```
    这种代码在老版本的 `g++` 下是没有警告的；甚至由于编译器特性，计算结果与返回值默认放在 `eax` 寄存器上，导致本地调试不会出现问题。
    
    然而，由于代码本身是存在 ub (undefined behavior，未定义行为) 的，优化器会默认有 ub 的代码是不会被运行的，因此在 OJ 上开启优化开关后运行出错。如果打开警告，编译器可以对这种情况进行提示。
    
    ![](https://notes.sjtu.edu.cn/uploads/upload_732fa452f3351ae3e4b190a6d8bf54b5.png)

    笔者通常使用的更多警告包括 `-Wextra -Wconversion -Wshadow`。
	
    * 警告并不一定代表问题，例如开启 `-Wconversion` 后，比较 `int` 和 `unsigned int` 会导致一个警告。如果你不喜欢某些警告，可以仅开启需要的。
    * 但是，编译器能起到有效 Warning 是编译型语言的优越性。强烈建议至少打开 `-Wall`。
	
* `-std=c++17` 表示采用 C++17 语言标准。这句话通常可以省略，除非你遇到了相关的 Compile Error。

### 文件输入输出
有时，我们会希望从一个文件里面读取输入文件，并输出到文件中。在不改动代码文件的情况下，你可以这么做：

=== "Linux / macOS"
    ```bash
    ./a < input.txt > output.txt
    ```
=== "Windows"
    ```cmd
    .\a.exe < input.txt > output.txt
    ```
    
    !!! info "对于使用 PowerShell 的 Windows 用户"
        PowerShell 的管道输入输出语法不一样，因此没法在 PowerShell 里运行以上命令。

        要直接使用上面的命令，你可以在打开的 PowerShell 窗口里输入 cmd 来让 PowerShell 成为 cmd 的套皮。




### Liunx / macOS 下测时

有时，我们会想测试代码运行效率。这种情况下，结合以上方法使用 `time` 命令将会非常方便。请注意，OJ 默认开启编译优化 O2，因此测时时应当也开启 O2。

```bash
g++ a.cpp -o a -O2 -std=c++17
time ./a < input.txt
```

如果使用 `/usr/bin/time` 甚至能粗略测出内存占用。
```bash
/usr/bin/time ‐v ./a
```

!!! TODO
    更换该页图片为代码块。
    
    类似 `/usr/bin/time -v` 的用法在 macOS 下需要 `brew install gnu-time`，需要补充。