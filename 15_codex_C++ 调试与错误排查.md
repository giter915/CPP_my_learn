现在继续学习：

```text
C++ 调试与错误排查
```

这一章非常重要。因为写程序时，真正花时间最多的往往不是“写第一版代码”，而是：

```text
看懂错误
定位问题
修复 bug
验证结果
```

你现在已经学了很多 C++ 语法，接下来要学会“代码出问题时怎么办”。

---

**一、错误大致分三类**

C++ 常见错误可以分成：

```text
编译错误
链接错误
运行时错误
逻辑错误
```

严格来说，逻辑错误也属于运行后的问题，但它很常见，所以单独讲。

---

**二、编译错误**

编译错误发生在编译阶段。

比如语法写错：

```cpp
int main() {
    cout << "hello" << endl
    return 0;
}
```

这里少了分号：

```cpp
cout << "hello" << endl;
```

编译器会报错。

常见编译错误：

```text
少分号
括号不匹配
变量未定义
类型不匹配
函数参数不匹配
头文件没 include
类成员访问权限错误
```

---

**三、看编译错误的方法**

编译器报错通常包含：

```text
文件名
行号
错误信息
```

比如：

```text
main.cpp:10:5: error: 'cout' was not declared in this scope
```

重点看：

```text
main.cpp
第 10 行
cout 没有声明
```

原因可能是忘了：

```cpp
#include <iostream>
```

或者忘了写：

```cpp
std::cout
```

---

**四、不要只看最后一行错误**

C++ 编译错误有时候会连带报很多行。

建议：

```text
先看第一个 error
先修第一个真正的错误
再重新编译
```

因为后面的很多错误可能是第一个错误引起的。

---

**五、常见编译错误示例**

### 1. 忘记分号

错误：

```cpp
class Student {
private:
    int age;
}
```

正确：

```cpp
class Student {
private:
    int age;
};
```

类定义结束后要有分号。

---

### 2. 忘记 include

错误：

```cpp
string name;
```

但没写：

```cpp
#include <string>
```

正确：

```cpp
#include <string>
```

---

### 3. 忘记 std::

错误：

```cpp
cout << "hello";
```

如果没有：

```cpp
using namespace std;
```

就要写：

```cpp
std::cout << "hello";
```

---

### 4. private 成员不能访问

```cpp
class Student {
private:
    int score;
};

int main() {
    Student s;
    s.score = 90; // 错误
}
```

解决：提供 public 函数。

```cpp
void setScore(int s) {
    score = s;
}
```

---

**六、链接错误**

链接错误发生在编译之后。

编译器知道函数存在，但链接器找不到函数实现。

常见报错：

```text
undefined reference to ...
unresolved external symbol ...
```

例如：

`Student.h`：

```cpp
class Student {
public:
    void show();
};
```

`main.cpp`：

```cpp
#include "Student.h"

int main() {
    Student s;
    s.show();
}
```

如果你声明了：

```cpp
void show();
```

但没有在 `Student.cpp` 实现它，就会链接错误。

---

**七、链接错误常见原因**

```text
函数只声明没实现
.cpp 文件没加入编译
函数签名声明和实现不一致
库没有链接
重复定义
```

比如你有：

```text
main.cpp
Student.cpp
```

但只编译：

```bash
g++ main.cpp -o app
```

会找不到 `Student.cpp` 里的实现。

正确：

```bash
g++ main.cpp Student.cpp -o app
```

如果用 CMake，要确保：

```cmake
add_executable(app
    main.cpp
    Student.cpp
)
```

---

**八、运行时错误**

运行时错误是程序编译成功，但运行时崩溃或异常。

常见：

```text
数组越界
空指针解引用
野指针
重复 delete
内存泄漏
除以 0
文件打开失败
递归爆栈
```

例如：

```cpp
int* p = nullptr;
cout << *p << endl; // 崩溃
```

---

**九、逻辑错误**

逻辑错误是程序能运行，但结果不对。

例如：

```cpp
int average = sum / count;
```

如果 `sum` 和 `count` 都是 `int`，结果会整数除法。

比如：

```cpp
int sum = 5;
int count = 2;

cout << sum / count << endl;
```

输出：

```text
2
```

不是：

```text
2.5
```

正确：

```cpp
double average = static_cast<double>(sum) / count;
```

---

**十、调试的基本思路**

遇到 bug，不要乱改。

建议流程：

```text
1. 复现问题
2. 看错误信息
3. 定位最小范围
4. 打印关键变量
5. 推断原因
6. 修改一处
7. 重新验证
```

不要同时改很多地方，否则不知道到底是哪一处修好的。

---

**十一、使用 cout 调试**

最简单的调试方法：

```cpp
cout << "x = " << x << endl;
```

例如：

```cpp
int sum = 0;

for (int i = 0; i <= n; i++) {
    sum += i;
    cout << "i = " << i << ", sum = " << sum << endl;
}
```

适合初学阶段快速观察变量。

---

**十二、使用 assert**

`assert` 用于检查你认为一定成立的条件。

头文件：

```cpp
#include <cassert>
```

例子：

```cpp
#include <cassert>

int divide(int a, int b) {
    assert(b != 0);
    return a / b;
}
```

如果 `b == 0`，程序会直接报错并停止。

适合检查程序内部逻辑。

---

**十三、assert 和异常的区别**

`assert` 用于程序员错误：

```text
这个条件按理说绝不该失败
失败说明代码有 bug
```

异常用于运行时可预期错误：

```text
文件打不开
用户输入不合法
网络失败
```

例如：

```cpp
assert(index >= 0 && index < size);
```

和：

```cpp
if (!file.is_open()) {
    throw runtime_error("文件打开失败");
}
```

---

**十四、断点调试**

断点调试比 `cout` 更强。

你可以：

```text
让程序停在某一行
一步一步执行
查看变量值
查看函数调用栈
观察程序走了哪个分支
```

常用操作：

```text
设置断点
Step Over：执行下一行，不进入函数
Step Into：进入函数
Step Out：跳出当前函数
Continue：继续运行到下一个断点
Watch：观察变量
Call Stack：调用栈
```

如果你用 Visual Studio、VS Code、CLion，都支持断点调试。

---

**十五、调用栈 Call Stack**

调用栈能告诉你程序是怎么走到当前函数的。

例如：

```text
main
 -> processStudents
 -> calculateAverage
 -> divide
```

如果 `divide` 出错，调用栈能帮你知道是谁传了错误参数。

---

**十六、gdb 简单入门**

如果你在命令行用 g++，可以用 `gdb`。

编译时加 `-g`：

```bash
g++ -g main.cpp -o app
```

启动：

```bash
gdb ./app
```

常用命令：

```text
break main       设置断点
run              运行
next             下一行，不进入函数
step             进入函数
continue         继续运行
print x          打印变量 x
backtrace        查看调用栈
quit             退出
```

---

**十七、编译选项**

建议开发时打开警告：

```bash
g++ -Wall -Wextra -g main.cpp -o app
```

含义：

```text
-Wall：打开常用警告
-Wextra：打开更多警告
-g：生成调试信息
```

很多 bug 编译器其实会提醒你。

例如：

```cpp
int x;
cout << x << endl;
```

未初始化变量，可能会有警告。

---

**十八、警告也要重视**

不要觉得 warning 可以忽略。

很多 warning 都是潜在 bug。

常见警告：

```text
变量未使用
变量未初始化
有符号和无符号比较
函数没有返回值
类型转换可能丢失数据
```

建议养成习惯：

```text
尽量让代码无 warning
```

---

**十九、内存错误排查**

C++ 最麻烦的问题之一是内存错误。

比如：

```text
越界访问
use after free
double delete
内存泄漏
```

Linux 上可以用：

```text
valgrind
asan
```

其中 AddressSanitizer 很常用。

编译：

```bash
g++ -fsanitize=address -g main.cpp -o app
```

运行后，如果有内存错误，它会给出详细位置。

---

**二十、AddressSanitizer 例子**

错误代码：

```cpp
int main() {
    int* arr = new int[3];

    arr[3] = 10;

    delete[] arr;

    return 0;
}
```

这是越界。

用：

```bash
g++ -fsanitize=address -g main.cpp -o app
```

运行时会报告：

```text
heap-buffer-overflow
```

并指出大概是哪一行。

---

**二十一、调试 STL 容器**

常见问题：

```cpp
vector<int> v;
cout << v[0] << endl;
```

这是错误，因为 vector 为空。

更安全：

```cpp
if (!v.empty()) {
    cout << v[0] << endl;
}
```

或者：

```cpp
v.at(0)
```

越界时会抛异常。

---

**二十二、迭代器失效**

STL 中一个常见坑是迭代器失效。

例如：

```cpp
vector<int> v = {1, 2, 3, 4};

for (auto it = v.begin(); it != v.end(); ++it) {
    if (*it == 2) {
        v.erase(it);
    }
}
```

这段代码有问题。

`erase` 后，原来的 `it` 可能失效。

正确：

```cpp
for (auto it = v.begin(); it != v.end(); ) {
    if (*it == 2) {
        it = v.erase(it);
    } else {
        ++it;
    }
}
```

---

**二十三、调试时常问自己**

遇到问题时，问：

```text
这个变量现在应该是多少？
它实际是多少？
它在哪里第一次变错？
这个函数输入是否正确？
这个对象是否还活着？
这个指针是否为空？
这个数组下标是否合法？
这个 .cpp 是否参与编译？
```

这些问题比盲改代码有用得多。

---

**二十四、单元测试入门**

调试是发现问题后修。

测试是提前发现问题。

比如你写了：

```cpp
int add(int a, int b) {
    return a + b;
}
```

可以写测试：

```cpp
#include <cassert>

int main() {
    assert(add(1, 2) == 3);
    assert(add(-1, 1) == 0);
    assert(add(0, 0) == 0);

    return 0;
}
```

如果某个条件不成立，程序会停止。

---

**二十五、给类写简单测试**

比如 `SafeArray`：

```cpp
SafeArray arr(3);

arr.at(0) = 10;
assert(arr.at(0) == 10);
assert(arr.getSize() == 3);
```

测试越界：

```cpp
try {
    arr.at(10);
    assert(false);
} catch (const out_of_range&) {
    assert(true);
}
```

---

**二十六、完整排查案例**

假设你写平均分：

```cpp
double averageScore(const vector<int>& scores) {
    int sum = 0;

    for (int score : scores) {
        sum += score;
    }

    return sum / scores.size();
}
```

问题：

```cpp
vector<int> scores = {90, 91};
cout << averageScore(scores) << endl;
```

输出：

```text
90
```

但期望：

```text
90.5
```

排查：

```text
sum 是 int
scores.size() 是整数
sum / scores.size() 做了整数除法
```

修复：

```cpp
return static_cast<double>(sum) / scores.size();
```

还要处理空数组：

```cpp
if (scores.empty()) {
    throw invalid_argument("scores 不能为空");
}
```

最终：

```cpp
double averageScore(const vector<int>& scores) {
    if (scores.empty()) {
        throw invalid_argument("scores 不能为空");
    }

    int sum = 0;

    for (int score : scores) {
        sum += score;
    }

    return static_cast<double>(sum) / scores.size();
}
```

---

**二十七、你需要掌握的重点**

这一章重点掌握：

```text
1. 编译错误看第一个 error
2. 链接错误常见原因是 .cpp 没参与编译
3. 运行时错误常见于指针、数组、资源管理
4. 逻辑错误要靠推理和测试发现
5. cout 可以快速观察变量
6. assert 检查内部不变量
7. 断点调试能逐行查看程序
8. 调用栈能定位错误路径
9. -Wall -Wextra -g 很有用
10. AddressSanitizer 能查内存错误
11. 迭代器 erase 后要小心失效
12. 单元测试可以提前发现问题
```

---

**二十八、练习题**

### 练习 1：修复编译错误

故意写一段少分号、忘 include、访问 private 成员的代码，观察编译器报错，并逐个修复。

---

### 练习 2：制造链接错误

写：

```text
Student.h
Student.cpp
main.cpp
```

然后故意只编译：

```bash
g++ main.cpp -o app
```

观察链接错误，再加入 `Student.cpp` 修复。

---

### 练习 3：assert 测试

写函数：

```cpp
int maxValue(int a, int b)
```

用 `assert` 测试：

```cpp
assert(maxValue(1, 2) == 2);
assert(maxValue(5, 3) == 5);
assert(maxValue(-1, -2) == -1);
```

---

### 练习 4：调试平均分

写一个平均分函数，先故意用整数除法，再用打印或断点找出问题并修复。

---

### 练习 5：迭代器删除

写程序从 `vector<int>` 删除所有偶数。

先尝试错误写法，再改成：

```cpp
it = v.erase(it);
```

---

下一步建议学习：

```text
C++ 设计原则和常见代码风格
```

比如：

```text
const 正确性
引用传参
值传递和移动
RAII 风格
类的职责划分
接口设计
避免全局变量
可读性和可维护性
```

这会让你从“语法会用”进入“代码写得漂亮、稳健”。