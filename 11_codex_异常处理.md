现在继续学习：

```text
异常处理
```

也就是 C++ 里的：

```cpp
try
catch
throw
exception
```

这部分很重要，因为真实程序里一定会遇到错误：文件打不开、输入非法、数组越界、内存申请失败、网络失败等等。

---

**一、为什么需要异常处理**

以前我们处理错误，可能这样写：

```cpp
int divide(int a, int b) {
    if (b == 0) {
        return -1;
    }

    return a / b;
}
```

问题是：

```text
-1 可能本身就是合法结果
错误信息不清楚
调用者容易忘记检查
复杂程序里错误层层传递很麻烦
```

异常处理可以把“正常逻辑”和“错误处理”分开。

---

**二、throw 抛出异常**

如果发现错误，可以使用：

```cpp
throw
```

例如：

```cpp
int divide(int a, int b) {
    if (b == 0) {
        throw "除数不能为 0";
    }

    return a / b;
}
```

这里：

```cpp
throw "除数不能为 0";
```

表示抛出一个异常。

---

**三、try 和 catch 捕获异常**

可能抛异常的代码放在 `try` 中：

```cpp
try {
    int result = divide(10, 0);
}
```

异常处理放在 `catch` 中：

```cpp
catch (const char* msg) {
    cout << msg << endl;
}
```

完整例子：

```cpp
#include <iostream>
using namespace std;

int divide(int a, int b) {
    if (b == 0) {
        throw "除数不能为 0";
    }

    return a / b;
}

int main() {
    try {
        int result = divide(10, 0);
        cout << result << endl;
    } catch (const char* msg) {
        cout << "发生错误：" << msg << endl;
    }

    return 0;
}
```

输出：

```text
发生错误：除数不能为 0
```

---

**四、异常处理流程**

程序执行到：

```cpp
throw
```

后，会立刻跳出当前正常流程，寻找匹配的 `catch`。

```cpp
try {
    cout << "开始" << endl;
    throw "错误";
    cout << "结束" << endl;
} catch (const char* msg) {
    cout << msg << endl;
}
```

输出：

```text
开始
错误
```

这句不会执行：

```cpp
cout << "结束" << endl;
```

---

**五、可以抛出不同类型异常**

C++ 可以抛出任意类型：

```cpp
throw 1;
throw 3.14;
throw "error";
throw string("error");
```

对应捕获：

```cpp
catch (int e) {
}

catch (double e) {
}

catch (const char* e) {
}

catch (const string& e) {
}
```

例子：

```cpp
try {
    throw string("文件打不开");
} catch (const string& e) {
    cout << e << endl;
}
```

---

**六、catch 的匹配顺序**

如果有多个 `catch`，会从上到下匹配。

```cpp
try {
    throw 10;
} catch (double e) {
    cout << "double" << endl;
} catch (int e) {
    cout << "int" << endl;
}
```

输出：

```text
int
```

---

**七、catch(...) 捕获所有异常**

```cpp
catch (...) {
    cout << "捕获未知异常" << endl;
}
```

它可以捕获所有类型异常。

通常放在最后：

```cpp
try {
    // code
} catch (const exception& e) {
    cout << e.what() << endl;
} catch (...) {
    cout << "未知异常" << endl;
}
```

---

**八、标准异常类 exception**

C++ 标准库提供了异常基类：

```cpp
std::exception
```

头文件：

```cpp
#include <exception>
```

很多标准异常都继承自它。

常见标准异常：

```text
std::exception
std::runtime_error
std::logic_error
std::invalid_argument
std::out_of_range
std::bad_alloc
```

更推荐抛标准异常，而不是字符串。

例如：

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

int divide(int a, int b) {
    if (b == 0) {
        throw runtime_error("除数不能为 0");
    }

    return a / b;
}

int main() {
    try {
        cout << divide(10, 0) << endl;
    } catch (const exception& e) {
        cout << "发生异常：" << e.what() << endl;
    }

    return 0;
}
```

输出：

```text
发生异常：除数不能为 0
```

---

**九、常用标准异常**

### invalid_argument

参数非法：

```cpp
throw invalid_argument("年龄不能为负数");
```

### out_of_range

越界：

```cpp
throw out_of_range("下标越界");
```

### runtime_error

运行时错误：

```cpp
throw runtime_error("文件打开失败");
```

### bad_alloc

内存申请失败时可能抛出：

```cpp
bad_alloc
```

---

**十、vector::at 会抛异常**

你之前学过：

```cpp
v[i]
```

和：

```cpp
v.at(i)
```

区别：

```cpp
v[i]
```

不检查越界。

```cpp
v.at(i)
```

越界会抛出异常。

例子：

```cpp
#include <iostream>
#include <vector>
#include <exception>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3};

    try {
        cout << v.at(10) << endl;
    } catch (const exception& e) {
        cout << "异常：" << e.what() << endl;
    }

    return 0;
}
```

---

**十一、异常和函数调用链**

异常可以跨函数传播。

```cpp
void level3() {
    throw runtime_error("level3 出错");
}

void level2() {
    level3();
}

void level1() {
    level2();
}
```

捕获：

```cpp
int main() {
    try {
        level1();
    } catch (const exception& e) {
        cout << e.what() << endl;
    }
}
```

异常会从 `level3` 一路传到 `main` 的 `catch`。

---

**十二、栈展开**

当异常从函数中抛出时，C++ 会离开当前函数，并自动销毁已经创建的局部对象。

这叫：

```text
栈展开
stack unwinding
```

例子：

```cpp
class Resource {
public:
    Resource() {
        cout << "获取资源" << endl;
    }

    ~Resource() {
        cout << "释放资源" << endl;
    }
};

void test() {
    Resource r;
    throw runtime_error("出错了");
}
```

调用：

```cpp
try {
    test();
} catch (const exception& e) {
    cout << e.what() << endl;
}
```

输出：

```text
获取资源
释放资源
出错了
```

即使发生异常，局部对象 `r` 也会析构。

这就是 RAII 为什么重要。

---

**十三、异常和智能指针**

看这个例子：

```cpp
void test() {
    unique_ptr<int> p = make_unique<int>(10);

    throw runtime_error("出错");

    // 不需要 delete
}
```

即使发生异常，`unique_ptr` 也会自动释放资源。

这就是：

```text
RAII + 异常安全
```

所以现代 C++ 推荐：

```text
用对象管理资源
用智能指针管理动态内存
不要裸 new/delete
```

---

**十四、自定义异常类**

可以自己定义异常类，继承 `std::exception`。

```cpp
#include <iostream>
#include <exception>
using namespace std;

class MyException : public exception {
public:
    const char* what() const noexcept override {
        return "这是自定义异常";
    }
};

int main() {
    try {
        throw MyException();
    } catch (const exception& e) {
        cout << e.what() << endl;
    }

    return 0;
}
```

输出：

```text
这是自定义异常
```

注意：

```cpp
what() const noexcept override
```

表示：

```text
不会修改对象
不会抛出异常
重写父类虚函数
```

---

**十五、带信息的自定义异常**

更常见做法是继承 `runtime_error`：

```cpp
class AgeException : public runtime_error {
public:
    AgeException(const string& msg) : runtime_error(msg) {
    }
};
```

使用：

```cpp
void setAge(int age) {
    if (age < 0) {
        throw AgeException("年龄不能为负数");
    }
}
```

完整例子：

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

class AgeException : public runtime_error {
public:
    AgeException(const string& msg) : runtime_error(msg) {
    }
};

void setAge(int age) {
    if (age < 0) {
        throw AgeException("年龄不能为负数");
    }
}

int main() {
    try {
        setAge(-1);
    } catch (const exception& e) {
        cout << e.what() << endl;
    }

    return 0;
}
```

---

**十六、不要在析构函数中抛异常**

析构函数里不要抛异常。

因为如果程序正在处理另一个异常时，析构函数又抛异常，可能导致程序直接终止。

错误倾向：

```cpp
~Resource() {
    throw runtime_error("析构失败"); // 非常危险
}
```

析构函数应该尽量：

```text
不抛异常
安全释放资源
```

---

**十七、什么时候用异常**

适合用异常：

```text
构造失败
文件打开失败
参数明显非法
程序无法继续完成当前操作
资源申请失败
```

不适合频繁用异常处理普通流程：

```text
循环中的普通条件判断
能用 if 简单处理的情况
非常高频的正常分支
```

例如：

```cpp
if (score >= 60) {
    cout << "及格";
}
```

这种不应该用异常。

异常适合处理“不正常情况”。

---

**十八、异常安全等级**

你先了解三个概念即可：

```text
基本保证
强保证
不抛保证
```

### 基本保证

出错后程序不崩，资源不泄漏，但对象状态可能变化。

### 强保证

出错后像没发生过一样，对象状态不变。

### 不抛保证

函数保证不抛异常。

例如移动构造常写：

```cpp
noexcept
```

就是不抛保证。

---

**十九、noexcept**

`noexcept` 表示函数不会抛异常。

```cpp
void f() noexcept {
}
```

移动构造和移动赋值经常写：

```cpp
MyClass(MyClass&& other) noexcept;
MyClass& operator=(MyClass&& other) noexcept;
```

这有助于 STL 容器优化。

比如 `vector` 扩容时，如果移动构造是 `noexcept`，更愿意使用移动。

---

**二十、完整案例：安全数组类**

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

class SafeArray {
private:
    int* data;
    int size;

public:
    SafeArray(int s) : size(s) {
        if (s <= 0) {
            throw invalid_argument("数组大小必须大于 0");
        }

        data = new int[size];

        for (int i = 0; i < size; i++) {
            data[i] = 0;
        }
    }

    ~SafeArray() {
        delete[] data;
    }

    int& at(int index) {
        if (index < 0 || index >= size) {
            throw out_of_range("数组下标越界");
        }

        return data[index];
    }

    int getSize() const {
        return size;
    }
};

int main() {
    try {
        SafeArray arr(3);

        arr.at(0) = 10;
        arr.at(1) = 20;

        cout << arr.at(0) << endl;
        cout << arr.at(5) << endl;
    } catch (const exception& e) {
        cout << "错误：" << e.what() << endl;
    }

    return 0;
}
```

输出：

```text
10
错误：数组下标越界
```

---

**二十一、异常处理核心模板**

```cpp
try {
    // 可能出错的代码
} catch (const exception& e) {
    cout << e.what() << endl;
}
```

抛异常：

```cpp
throw runtime_error("错误信息");
```

参数非法：

```cpp
throw invalid_argument("参数非法");
```

越界：

```cpp
throw out_of_range("越界");
```

自定义异常：

```cpp
class MyException : public runtime_error {
public:
    MyException(const string& msg) : runtime_error(msg) {
    }
};
```

---

**二十二、你需要掌握的重点**

这一章重点掌握：

```text
1. throw 抛出异常
2. try/catch 捕获异常
3. catch 的匹配顺序
4. catch(...) 捕获所有异常
5. std::exception 和 e.what()
6. runtime_error / invalid_argument / out_of_range
7. 异常会触发栈展开
8. RAII 可以保证异常时资源释放
9. 析构函数不要抛异常
10. noexcept 的基本作用
```

最重要的是这一句：

```text
异常处理和 RAII 是一对组合拳。
```

如果资源都交给对象、容器、智能指针管理，那么发生异常时，C++ 会自动帮你清理局部对象，程序更安全。

---

**二十三、练习题**

### 练习 1：安全除法

写函数：

```cpp
double divide(double a, double b)
```

如果 `b == 0`，抛出：

```cpp
invalid_argument("除数不能为 0")
```

在 `main` 中用 `try/catch` 捕获。

---

### 练习 2：检查年龄

写函数：

```cpp
void setAge(int age)
```

如果年龄小于 0 或大于 150，抛出：

```cpp
out_of_range
```

---

### 练习 3：SafeArray

写一个 `SafeArray` 类：

```text
构造函数接收 size
如果 size <= 0，抛 invalid_argument
at(index) 越界时抛 out_of_range
```

---

### 练习 4：智能指针和异常

写一个函数：

```cpp
void test()
```

里面创建：

```cpp
unique_ptr<int>
```

然后抛出异常，观察对象是否能安全释放。

---

### 练习 5：自定义异常

定义：

```cpp
class LoginException : public runtime_error
```

当用户名或密码为空时抛出这个异常。

---

下一步建议学习：

```text
文件操作
```

也就是：

```cpp
ifstream
ofstream
fstream
文本文件读写
二进制文件读写
文件异常处理
```

这会让你的程序可以保存数据、读取配置、处理文本。