由于判定两份代码是否等价是无法做到的（也是没有必要的），OJ 采用的是在一组数据集上进行测试的方法来确定代码是否正确。样例可能仅仅展示了一个非常小的例子，无法涵盖所有情况：很可能你样例全部通过了，但是 OJ 返回了五彩斑斓的结果。

然而，这并不代表你对于隐藏的数据无可奈何：你可以通过自己生成数据的方法测试程序。
    
我们以 [1300. k-th Smallest Number](https://acm.sjtu.edu.cn/OnlineJudge/problem?problem_id=1300) 为例，给出一系列生成数据与对拍的办法。

这道题的输入自带了一个随机数据生成器，因此我们可以用下列输入直接构造出随机极限数据：
```
40000000 19260817 1
1919810
```
不过，通常题目并不会提供一个随机数生成器；同时纯随机大数据并不能完全满足我们的要求：它只能测出部分错误，并且难以用其他程序检查正确性。

接下来，笔者假定 $n = m$，也即数据完全由输入给定的时候，应该如何生成数据。

#### 生成特殊数据
对于部分题目来说，看起来结构简单的特殊数据具有比较强的针对性，可以使得某些错误程序挂掉；同时，这些数据可以很方便手算一个答案出来，来判断答案正确性。

* 对于序列来说，$1, 2, \dots, n$ 可能是一个非常有用的数据：比如对于错误实现的快速排序和快速选择算法，如果取第一个或者最后一个元素作为 pivot，那么会立刻超时。
    ```python
    n = int(1e5)
    print(n, 23333, n)
    for i in range(n):
        print(i + 1, end=' ')
    ```
    ```sh
    python gen.py > input.txt
    ```
* 对于树来说，可以考虑一条链、一个菊花图、一颗完全二叉树。
* 对于图来说，除了类似树的情况，还可以考虑完全图和一个大环。
    
#### 生成随机数据与对拍
如果程序有 WA 但查不出来，生成小的随机数据并与参考程序「对拍」可能是比较好的选择。参考程序不一定需要很快，只要在小数据上保证正确即可。找出错误数据后，可以针对其进行查错。

对于这道题来说，一个缓慢但是有效的做法是，直接排序输出第 $k$ 个数：
```cpp
int main() {
    read_input_data();
    sort(a + 1, a + n + 1);
    cout << a[k] << endl;
}
```
假设上面的程序命名为 `std.cpp`，并编译为 `std` 的二进制文件；你的程序是 `a.cpp`，并编译为 `a` 的二进制文件。你可以使用以下 Python 脚本来进行对拍：
```python
import random, os
from random import randint

STD = 'std'
CMP = 'a'
INPUT = 'input.txt'
STD_OUTPUT = 'std.txt'
CMP_OUTPUT = 'cmp.txt'

if os.name == 'nt': # Windows uses a different prefix
    STD = '.\\' + STD
    CMP = '.\\' + CMP
    DIFF = "fc"
else:
    STD = './' + STD
    CMP = './' + CMP
    DIFF = "diff"
def gen(test):
    random.seed(test) # fixed seed to have reproducible results 
    f = open(INPUT, 'w')
    n = randint(1, 10)
    k = randint(1, n)
    print(n, k, n, file=f)
    for i in range(n):
        print(randint(1, 10), end=' ', file=f)

def run():
    if os.system(f'{STD} < {INPUT} > {STD_OUTPUT}'):
        print("STD Runtime Error")
        exit(1)
    if os.system(f'{CMP} < {INPUT} > {CMP_OUTPUT}'):
        print("Runtime Error")
        exit(1)

def check():
    if os.system(f'{DIFF} {STD_OUTPUT} {CMP_OUTPUT}'):
        print("Wrong Answer")
        exit(1)

test = 0
while True:
    print('Test', test)
    gen(test)
    run()
    check()
    test += 1
```
我们现在假设 `a.cpp` 的实现与 `std.cpp` 一致，但是 `sort` 的边界错了：写成了 `sort(a + 1, a + n)`。运行以上脚本，我们能得到：
```
fstqwq@DESKTOP-fstqwq:/mnt/c/code$ python3 check.py
Test 0
1c1
< 9
---
> 5
Wrong Answer
fstqwq@DESKTOP-fstqwq:/mnt/c/code$
```
立刻就找出问题了。

!!! bug "答案错误还是参考答案错误？"
    请注意 Wrong Answer 也有可能是参考程序写挂了。