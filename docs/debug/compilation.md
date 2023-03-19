### 模板相关
C++ 还有一大特色：编译错误非常长，且晦涩难懂。
得益于模板特性，下面一段简单的代码可能触发一长串来自 GCC 的连招：
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    cout << max(0, 0.0) << endl;
}
```
```log
fstqwq@DESKTOP-fstqwq:/mnt/c/code$ g++ a.cpp
a.cpp: In function ‘int main()’:
a.cpp:5:20: error: no matching function for call to ‘max(int, double)’
  cout << max(0, 0.0) << endl;
                    ^
In file included from /usr/include/c++/7/bits/char_traits.h:39:0,
                 from /usr/include/c++/7/ios:40,
                 from /usr/include/c++/7/istream:38,
                 from /usr/include/c++/7/sstream:38,
                 from /usr/include/c++/7/complex:45,
                 from /usr/include/c++/7/ccomplex:39,
                 from /usr/include/x86_64-linux-gnu/c++/7/bits/stdc++.h:52,
                 from a.cpp:1:
/usr/include/c++/7/bits/stl_algobase.h:219:5: note: candidate: template<class _Tp> constexpr const _Tp& std::max(const _Tp&, const _Tp&)
     max(const _Tp& __a, const _Tp& __b)
     ^~~
/usr/include/c++/7/bits/stl_algobase.h:219:5: note:   template argument deduction/substitution failed:
a.cpp:5:20: note:   deduced conflicting types for parameter ‘const _Tp’ (‘int’ and ‘double’)
  cout << max(0, 0.0) << endl;
                    ^
In file included from /usr/include/c++/7/bits/char_traits.h:39:0,
                 from /usr/include/c++/7/ios:40,
                 from /usr/include/c++/7/istream:38,
                 from /usr/include/c++/7/sstream:38,
                 from /usr/include/c++/7/complex:45,
                 from /usr/include/c++/7/ccomplex:39,
                 from /usr/include/x86_64-linux-gnu/c++/7/bits/stdc++.h:52,
                 from a.cpp:1:
/usr/include/c++/7/bits/stl_algobase.h:265:5: note: candidate: template<class _Tp, class _Compare> constexpr const _Tp& std::max(const _Tp&, const _Tp&, _Compare)
     max(const _Tp& __a, const _Tp& __b, _Compare __comp)
     ^~~
/usr/include/c++/7/bits/stl_algobase.h:265:5: note:   template argument deduction/substitution failed:
a.cpp:5:20: note:   deduced conflicting types for parameter ‘const _Tp’ (‘int’ and ‘double’)
  cout << max(0, 0.0) << endl;
                    ^
In file included from /usr/include/c++/7/algorithm:62:0,
                 from /usr/include/x86_64-linux-gnu/c++/7/bits/stdc++.h:65,
                 from a.cpp:1:
/usr/include/c++/7/bits/stl_algo.h:3462:5: note: candidate: template<class _Tp> constexpr _Tp std::max(std::initializer_list<_Tp>)
     max(initializer_list<_Tp> __l)
     ^~~
/usr/include/c++/7/bits/stl_algo.h:3462:5: note:   template argument deduction/substitution failed:
a.cpp:5:20: note:   mismatched types ‘std::initializer_list<_Tp>’ and ‘int’
  cout << max(0, 0.0) << endl;
                    ^
In file included from /usr/include/c++/7/algorithm:62:0,
                 from /usr/include/x86_64-linux-gnu/c++/7/bits/stdc++.h:65,
                 from a.cpp:1:
/usr/include/c++/7/bits/stl_algo.h:3468:5: note: candidate: template<class _Tp, class _Compare> constexpr _Tp std::max(std::initializer_list<_Tp>, _Compare)
     max(initializer_list<_Tp> __l, _Compare __comp)
     ^~~
/usr/include/c++/7/bits/stl_algo.h:3468:5: note:   template argument deduction/substitution failed:
a.cpp:5:20: note:   mismatched types ‘std::initializer_list<_Tp>’ and ‘int’
  cout << max(0, 0.0) << endl;
                    ^
fstqwq@DESKTOP-fstqwq:/mnt/c/code$
```
当你觉得编译错误不知所云的时候，请关注在你的代码里 error 的地方：比如上面的编译错误信息里，有模板库里的代码，也有你的代码。事实上，只需要关注这条信息就足够了：
```log
a.cpp:5:20: error: no matching function for call to ‘max(int, double)’
```
因此你需要做的就是进行一次类型强转，保证 `max` 两边内容类型相同。

### ld returned 1 exit status
在 Windows 下，你可能会遇到类似这样的错误信息：
```log
c:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/12.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: cannot open output file a.exe: Permission denied
collect2.exe: error: ld returned 1 exit status
```
这通常表明输出文件被占用了，通常是因为程序还在运行中。杀死运行中的程序重新编译即可。