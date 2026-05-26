你现在已经学完：

```text
类和对象
封装
继承
多态
拷贝构造
赋值运算符
析构函数
深拷贝和浅拷贝
三法则
```

下一步建议学习：

```text
移动语义
```

也就是：

```text
右值引用
移动构造函数
移动赋值运算符
std::move
五法则
```

这部分是现代 C++ 非常重要的内容。

---

**一、为什么需要移动语义**

先看一个类：

```cpp
class MyArray {
private:
    int* data;
    int size;

public:
    MyArray(int s) : size(s) {
        data = new int[size];
    }

    MyArray(const MyArray& other) : size(other.size) {
        data = new int[size];

        for (int i = 0; i < size; i++) {
            data[i] = other.data[i];
        }
    }

    ~MyArray() {
        delete[] data;
    }
};
```

如果我们写：

```cpp
MyArray a1(1000000);
MyArray a2 = a1;
```

这会发生深拷贝：

```text
重新申请一块大内存
把 1000000 个元素复制过去
```

这很安全，但可能很慢。

但是有些情况下，对象只是临时对象，马上就不用了。

比如：

```cpp
MyArray createArray() {
    MyArray temp(1000000);
    return temp;
}

MyArray a = createArray();
```

`temp` 是临时对象，函数返回后它就没用了。

既然它马上就要销毁，那为什么还要复制它的数据？

我们能不能直接把它内部的那块内存“交给”新对象？

这就是移动语义。

---

**二、拷贝和移动的区别**

拷贝：

```text
复制资源
两个对象各自拥有一份资源
```

移动：

```text
转移资源
新对象接管旧对象的资源
旧对象不再拥有资源
```

举个直观例子。

拷贝像是：

```text
复印一份文件
原文件还在
新文件也有
```

移动像是：

```text
把文件夹搬到另一个地方
原位置没有了
新位置拥有它
```

在 C++ 中，移动通常用于：

```text
临时对象
即将销毁的对象
不再需要的对象
```

---

**三、左值和右值**

移动语义前，需要先理解左值和右值。

### 左值

左值通常是：

```text
有名字
可以取地址
可以多次使用
```

例如：

```cpp
int x = 10;
```

这里 `x` 是左值。

```cpp
cout << &x << endl;
```

可以取地址。

对象也是：

```cpp
MyArray a(10);
```

`a` 是左值。

---

### 右值

右值通常是：

```text
临时的
没有名字
用完就没了
```

例如：

```cpp
10
x + 1
MyArray(10)
createArray()
```

这些通常是右值。

例如：

```cpp
int x = 10 + 20;
```

`10 + 20` 的结果是临时值，是右值。

---

**四、左值引用**

你之前应该见过普通引用：

```cpp
int x = 10;
int& ref = x;
```

这里：

```cpp
int& ref
```

是左值引用。

它只能绑定到左值：

```cpp
int& ref = x;  // 正确
int& ref = 10; // 错误
```

但是 `const` 左值引用可以绑定右值：

```cpp
const int& ref = 10;
```

这常用于函数参数：

```cpp
void print(const string& s) {
    cout << s << endl;
}
```

---

**五、右值引用**

右值引用是 C++11 引入的语法。

写法：

```cpp
类型&&
```

例如：

```cpp
int&& r = 10;
```

这里 `r` 是一个右值引用。

右值引用主要用于接收临时对象：

```cpp
void test(MyArray&& arr) {
}
```

它表示：

```text
arr 可以绑定到一个即将被销毁的临时 MyArray 对象
```

---

**六、移动构造函数**

移动构造函数用于：

```text
用一个即将被销毁的对象创建新对象
```

语法：

```cpp
类名(类名&& other)
```

例如：

```cpp
MyArray(MyArray&& other)
```

完整例子：

```cpp
#include <iostream>
using namespace std;

class MyArray {
private:
    int* data;
    int size;

public:
    MyArray(int s) : size(s) {
        data = new int[size];
        cout << "普通构造函数" << endl;
    }

    MyArray(const MyArray& other) : size(other.size) {
        data = new int[size];

        for (int i = 0; i < size; i++) {
            data[i] = other.data[i];
        }

        cout << "拷贝构造函数" << endl;
    }

    MyArray(MyArray&& other) : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;

        cout << "移动构造函数" << endl;
    }

    ~MyArray() {
        delete[] data;
        cout << "析构函数" << endl;
    }
};
```

重点是：

```cpp
MyArray(MyArray&& other) : data(other.data), size(other.size) {
    other.data = nullptr;
    other.size = 0;
}
```

这不是重新申请内存，而是直接接管 `other` 的内存。

然后把 `other.data` 设为 `nullptr`，防止它析构时释放这块内存。

---

**七、移动构造的内存变化**

假设：

```cpp
MyArray a2 = MyArray(100);
```

移动前：

```text
临时对象.data ---> 堆内存
a2.data 还未初始化
```

移动后：

```text
a2.data ---> 堆内存
临时对象.data ---> nullptr
```

临时对象析构时：

```cpp
delete[] nullptr;
```

这是安全的。

---

**八、std::move**

有时候一个对象是左值，但你确定它以后不用了，希望把它的资源移动给别人。

这时候使用：

```cpp
std::move
```

例如：

```cpp
MyArray a1(100);
MyArray a2 = std::move(a1);
```

`a1` 本来是左值，因为它有名字。

但 `std::move(a1)` 会把它转换成右值引用，让移动构造函数可以被调用。

注意：

```cpp
std::move
```

本身不移动任何东西。

它只是告诉编译器：

```text
我允许你把这个对象当成即将失效的对象处理。
```

真正移动资源的是移动构造函数或移动赋值运算符。

使用 `std::move` 后，原对象仍然存在，但它的状态通常不再适合继续正常使用，只能保证可以析构或重新赋值。

---

**九、移动赋值运算符**

和拷贝赋值类似，移动赋值用于：

```text
一个已经存在的对象，接管另一个即将失效对象的资源
```

语法：

```cpp
类名& operator=(类名&& other)
```

例如：

```cpp
MyArray& operator=(MyArray&& other)
```

完整写法：

```cpp
MyArray& operator=(MyArray&& other) {
    cout << "移动赋值运算符" << endl;

    if (this != &other) {
        delete[] data;

        data = other.data;
        size = other.size;

        other.data = nullptr;
        other.size = 0;
    }

    return *this;
}
```

流程是：

```text
1. 释放自己原来的资源
2. 接管 other 的资源
3. 把 other 置空
4. 返回当前对象
```

---

**十、完整 MyArray：五法则版本**

```cpp
#include <iostream>
#include <utility>
using namespace std;

class MyArray {
private:
    int* data;
    int size;

public:
    MyArray(int s) : size(s) {
        data = new int[size];

        for (int i = 0; i < size; i++) {
            data[i] = 0;
        }

        cout << "普通构造函数" << endl;
    }

    MyArray(const MyArray& other) : size(other.size) {
        data = new int[size];

        for (int i = 0; i < size; i++) {
            data[i] = other.data[i];
        }

        cout << "拷贝构造函数" << endl;
    }

    MyArray& operator=(const MyArray& other) {
        cout << "拷贝赋值运算符" << endl;

        if (this != &other) {
            delete[] data;

            size = other.size;
            data = new int[size];

            for (int i = 0; i < size; i++) {
                data[i] = other.data[i];
            }
        }

        return *this;
    }

    MyArray(MyArray&& other) noexcept
        : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;

        cout << "移动构造函数" << endl;
    }

    MyArray& operator=(MyArray&& other) noexcept {
        cout << "移动赋值运算符" << endl;

        if (this != &other) {
            delete[] data;

            data = other.data;
            size = other.size;

            other.data = nullptr;
            other.size = 0;
        }

        return *this;
    }

    ~MyArray() {
        delete[] data;
        cout << "析构函数" << endl;
    }

    void set(int index, int value) {
        if (index >= 0 && index < size) {
            data[index] = value;
        }
    }

    int get(int index) const {
        if (index >= 0 && index < size) {
            return data[index];
        }

        return -1;
    }

    int getSize() const {
        return size;
    }
};
```

测试：

```cpp
MyArray createArray() {
    MyArray temp(5);
    temp.set(0, 100);
    return temp;
}

int main() {
    MyArray a1(3);
    MyArray a2 = a1;

    MyArray a3 = createArray();

    MyArray a4(10);
    a4 = std::move(a1);

    return 0;
}
```

---

**十一、noexcept 的作用**

你会看到移动构造函数常写：

```cpp
MyArray(MyArray&& other) noexcept
```

移动赋值也常写：

```cpp
MyArray& operator=(MyArray&& other) noexcept
```

`noexcept` 表示这个函数不会抛异常。

为什么重要？

因为 STL 容器，比如 `vector`，在扩容时，如果你的移动构造函数是 `noexcept`，它更愿意使用移动而不是拷贝。

所以移动构造和移动赋值一般建议加：

```cpp
noexcept
```

---

**十二、std::move 后还能用原对象吗**

例如：

```cpp
MyArray a1(10);
MyArray a2 = std::move(a1);
```

移动后，`a1` 仍然存在，但是它内部资源已经被转移了。

你可以：

```cpp
a1 = MyArray(5);
```

重新赋值。

你也可以让它正常析构。

但是不要假设它还保留原来的数据。

可以理解为：

```text
被 move 之后的对象处于“有效但不确定”的状态。
```

有效：可以析构、可以重新赋值。

不确定：里面原来的值不应该依赖。

---

**十三、什么时候会调用移动构造**

常见情况：

### 1. 用临时对象创建对象

```cpp
MyArray a = MyArray(10);
```

可能调用移动构造，也可能被编译器优化掉。

---

### 2. 函数返回临时对象

```cpp
MyArray create() {
    return MyArray(10);
}

MyArray a = create();
```

现代编译器可能直接省略拷贝/移动，这叫返回值优化。

---

### 3. 使用 std::move

```cpp
MyArray a1(10);
MyArray a2 = std::move(a1);
```

这会明确倾向于调用移动构造。

---

**十四、五法则**

你之前学过三法则：

```text
析构函数
拷贝构造函数
拷贝赋值运算符
```

现在加上：

```text
移动构造函数
移动赋值运算符
```

就是五法则：

```text
如果一个类需要自己管理资源，通常需要考虑这五个函数。
```

五法则完整列表：

```cpp
~ClassName();

ClassName(const ClassName& other);

ClassName& operator=(const ClassName& other);

ClassName(ClassName&& other) noexcept;

ClassName& operator=(ClassName&& other) noexcept;
```

---

**十五、现代 C++ 更推荐零法则**

虽然五法则很重要，但在真实项目里，更推荐尽量避免自己写它们。

比如使用：

```cpp
std::vector<int>
std::string
std::unique_ptr<int[]>
```

而不是手动：

```cpp
int* data;
new
delete
```

例如：

```cpp
#include <vector>
using namespace std;

class MyArray {
private:
    vector<int> data;

public:
    MyArray(int size) : data(size, 0) {
    }

    void set(int index, int value) {
        if (index >= 0 && index < data.size()) {
            data[index] = value;
        }
    }

    int get(int index) const {
        if (index >= 0 && index < data.size()) {
            return data[index];
        }

        return -1;
    }
};
```

这个版本不需要自己写：

```text
析构函数
拷贝构造
拷贝赋值
移动构造
移动赋值
```

因为 `vector` 已经帮你处理好了。

这就是零法则。

---

**十六、移动语义和性能**

移动语义最大的价值是提升性能。

特别是这些类型：

```text
string
vector
map
unique_ptr
自定义资源管理类
```

如果拷贝它们，可能很贵。

如果移动它们，只需要转移内部资源指针，速度很快。

例如：

```cpp
vector<int> v1(1000000);
vector<int> v2 = std::move(v1);
```

这通常不会复制一百万个整数，而是把 `v1` 的内部数组交给 `v2`。

---

**十七、unique_ptr 为什么只能移动不能拷贝**

之后你会学智能指针。

这里先提前感受一下：

```cpp
unique_ptr<int> p1 = make_unique<int>(10);
unique_ptr<int> p2 = p1; // 错误
```

为什么？

因为 `unique_ptr` 表示独占所有权。

一块内存只能有一个 `unique_ptr` 管理。

但是可以移动：

```cpp
unique_ptr<int> p2 = std::move(p1);
```

移动后：

```text
p2 拥有资源
p1 变为空
```

这就是移动语义在真实标准库中的典型应用。

---

**十八、常见错误**

### 1. 以为 std::move 会移动

```cpp
std::move(x);
```

单独写这一句几乎没意义。

`std::move` 只是类型转换。

真正移动发生在：

```cpp
MyArray a2 = std::move(a1);
```

或者：

```cpp
a2 = std::move(a1);
```

---

### 2. move 后继续使用原值

```cpp
string s = "hello";
string t = std::move(s);

cout << s << endl; // 不建议依赖 s 的内容
```

`s` 仍然可以使用，但内容不确定。

---

### 3. 忘记把 other 指针置空

错误：

```cpp
MyArray(MyArray&& other) {
    data = other.data;
}
```

这样两个对象还是指向同一块内存。

正确：

```cpp
MyArray(MyArray&& other) {
    data = other.data;
    other.data = nullptr;
}
```

---

### 4. 移动赋值前忘记释放旧资源

错误：

```cpp
data = other.data;
```

如果当前对象原本已经有资源，这会造成内存泄漏。

正确：

```cpp
delete[] data;
data = other.data;
```

---

**十九、你需要记住的核心**

移动语义的核心是：

```text
能转移资源，就不要昂贵地复制资源。
```

右值引用：

```cpp
T&&
```

用于接收可移动对象。

移动构造：

```cpp
T(T&& other) noexcept
```

用于用临时对象创建新对象。

移动赋值：

```cpp
T& operator=(T&& other) noexcept
```

用于把一个临时对象的资源转移给已有对象。

`std::move`：

```cpp
std::move(x)
```

表示我允许把 `x` 当成右值处理。

---

**二十、练习题**

### 练习 1：给 MyArray 添加移动语义

在你之前写的 `MyArray` 基础上添加：

```text
移动构造函数
移动赋值运算符
```

要求输出：

```text
普通构造
拷贝构造
拷贝赋值
移动构造
移动赋值
析构
```

观察不同情况下调用哪个函数。

---

### 练习 2：测试 std::move

写一个 `Person` 类，成员是：

```cpp
string name;
```

打印构造、拷贝、移动过程。

测试：

```cpp
Person p1("张三");
Person p2 = p1;
Person p3 = std::move(p1);
```

观察 `p1` 被 move 后的状态。

---

### 练习 3：理解 vector 的移动

写代码测试：

```cpp
vector<int> v1(1000000, 1);
vector<int> v2 = std::move(v1);
```

输出：

```cpp
v1.size()
v2.size()
```

观察移动前后变化。

---

你现在已经从传统 C++ 类，进入现代 C++ 的核心区域了。下一步建议学习：

```text
运算符重载
```

因为你已经学过 `operator=`，接下来可以系统学习 `operator+`、`operator<<`、`operator[]`、`operator()` 等等。