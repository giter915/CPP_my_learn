现在你已经学完：

```text
类和对象
封装
继承
多态
拷贝/赋值/析构
移动语义
运算符重载
```

下一步非常适合学习：

```text
模板
```

模板是 C++ 里非常重要的一部分。它是 STL 的基础。

比如你经常会看到：

```cpp
vector<int>
vector<string>
map<string, int>
```

这些背后都和模板有关。

---

**一、为什么需要模板**

假设你想写一个函数，求两个数中的较大值。

对于 `int`：

```cpp
int myMax(int a, int b) {
    return a > b ? a : b;
}
```

对于 `double`：

```cpp
double myMax(double a, double b) {
    return a > b ? a : b;
}
```

对于 `char`：

```cpp
char myMax(char a, char b) {
    return a > b ? a : b;
}
```

你会发现逻辑完全一样，只是类型不同。

模板就是为了解决这种重复。

---

**二、函数模板**

函数模板语法：

```cpp
template <typename T>
T 函数名(T 参数1, T 参数2) {
    函数体;
}
```

例子：

```cpp
#include <iostream>
using namespace std;

template <typename T>
T myMax(T a, T b) {
    return a > b ? a : b;
}

int main() {
    cout << myMax(10, 20) << endl;
    cout << myMax(3.14, 2.71) << endl;
    cout << myMax('a', 'z') << endl;

    return 0;
}
```

输出：

```text
20
3.14
z
```

这里 `T` 是一个类型占位符。

调用：

```cpp
myMax(10, 20)
```

编译器会推导出：

```cpp
T = int
```

调用：

```cpp
myMax(3.14, 2.71)
```

编译器会推导出：

```cpp
T = double
```

---

**三、typename 和 class**

模板里可以写：

```cpp
template <typename T>
```

也可以写：

```cpp
template <class T>
```

在这里它们基本等价。

例如：

```cpp
template <class T>
T myMax(T a, T b) {
    return a > b ? a : b;
}
```

更推荐初学时写：

```cpp
typename
```

因为它更直观：`T` 是一个类型。

---

**四、显式指定模板类型**

有时候编译器推导不出来，或者你想明确指定类型，可以这样写：

```cpp
myMax<int>(10, 20);
```

例子：

```cpp
cout << myMax<int>(10, 20) << endl;
```

如果参数类型不同：

```cpp
myMax(10, 20.5);
```

可能报错，因为一个是 `int`，一个是 `double`。

可以显式指定：

```cpp
myMax<double>(10, 20.5);
```

这时 `10` 会转换成 `double`。

---

**五、多个模板参数**

模板可以有多个类型参数。

```cpp
template <typename T1, typename T2>
void printPair(T1 a, T2 b) {
    cout << a << " " << b << endl;
}
```

使用：

```cpp
printPair(10, "hello");
printPair(3.14, 'A');
```

完整例子：

```cpp
#include <iostream>
using namespace std;

template <typename T1, typename T2>
void printPair(T1 a, T2 b) {
    cout << a << " " << b << endl;
}

int main() {
    printPair(10, "hello");
    printPair(3.14, 'A');

    return 0;
}
```

---

**六、函数模板和普通函数**

如果普通函数和模板函数都能匹配，C++ 通常优先调用普通函数。

```cpp
#include <iostream>
using namespace std;

void print(int x) {
    cout << "普通函数：" << x << endl;
}

template <typename T>
void print(T x) {
    cout << "模板函数：" << x << endl;
}

int main() {
    print(10);
    print(3.14);

    return 0;
}
```

输出：

```text
普通函数：10
模板函数：3.14
```

因为 `print(10)` 可以完全匹配普通函数。

---

**七、类模板**

函数可以模板化，类也可以模板化。

例如我们想写一个 `Box` 类，能保存任意类型的数据。

如果不用模板：

```cpp
class IntBox {
private:
    int value;
};

class StringBox {
private:
    string value;
};
```

很重复。

使用类模板：

```cpp
template <typename T>
class Box {
private:
    T value;

public:
    Box(T v) : value(v) {
    }

    T getValue() const {
        return value;
    }

    void setValue(T v) {
        value = v;
    }
};
```

使用：

```cpp
Box<int> b1(10);
Box<string> b2("hello");

cout << b1.getValue() << endl;
cout << b2.getValue() << endl;
```

完整：

```cpp
#include <iostream>
#include <string>
using namespace std;

template <typename T>
class Box {
private:
    T value;

public:
    Box(T v) : value(v) {
    }

    T getValue() const {
        return value;
    }

    void setValue(T v) {
        value = v;
    }
};

int main() {
    Box<int> b1(10);
    Box<string> b2("hello");

    cout << b1.getValue() << endl;
    cout << b2.getValue() << endl;

    return 0;
}
```

---

**八、类模板必须指定类型**

函数模板通常可以自动推导：

```cpp
myMax(10, 20);
```

但类模板一般要写明类型：

```cpp
Box<int> b1(10);
Box<string> b2("hello");
```

C++17 以后某些情况可以自动推导，但初学时建议明确写：

```cpp
Box<int>
```

---

**九、类模板多个参数**

```cpp
template <typename K, typename V>
class Pair {
private:
    K key;
    V value;

public:
    Pair(K k, V v) : key(k), value(v) {
    }

    K getKey() const {
        return key;
    }

    V getValue() const {
        return value;
    }
};
```

使用：

```cpp
Pair<string, int> p("age", 18);

cout << p.getKey() << endl;
cout << p.getValue() << endl;
```

这和 STL 里的：

```cpp
map<string, int>
```

感觉就很像了。

---

**十、模板类成员函数写在类外**

如果类模板的成员函数写在类外，要这样写：

```cpp
template <typename T>
class Box {
private:
    T value;

public:
    Box(T v);
    T getValue() const;
};
```

类外定义：

```cpp
template <typename T>
Box<T>::Box(T v) : value(v) {
}

template <typename T>
T Box<T>::getValue() const {
    return value;
}
```

注意：

```cpp
Box<T>::getValue
```

不能只写：

```cpp
Box::getValue
```

因为这是一个类模板，不是普通类。

---

**十一、模板和运算符**

模板函数内部使用某个运算符时，传入的类型必须支持这个运算符。

例如：

```cpp
template <typename T>
T myMax(T a, T b) {
    return a > b ? a : b;
}
```

这里用了：

```cpp
>
```

所以 `T` 类型必须支持 `>`。

如果你传入自定义类型：

```cpp
Student s1;
Student s2;

myMax(s1, s2);
```

那么 `Student` 必须重载：

```cpp
operator>
```

否则会编译失败。

---

**十二、模板特化**

有时候大多数类型用通用模板就行，但某个类型需要特殊处理。

这叫模板特化。

例如：

```cpp
template <typename T>
void print(T value) {
    cout << "普通类型：" << value << endl;
}
```

对 `string` 特殊处理：

```cpp
template <>
void print<string>(string value) {
    cout << "字符串：" << value << endl;
}
```

完整：

```cpp
#include <iostream>
#include <string>
using namespace std;

template <typename T>
void print(T value) {
    cout << "普通类型：" << value << endl;
}

template <>
void print<string>(string value) {
    cout << "字符串：" << value << endl;
}

int main() {
    print(10);
    print(3.14);
    print(string("hello"));

    return 0;
}
```

输出：

```text
普通类型：10
普通类型：3.14
字符串：hello
```

---

**十三、非类型模板参数**

模板参数不一定只能是类型，也可以是普通值。

比如数组大小：

```cpp
template <typename T, int Size>
class Array {
private:
    T data[Size];

public:
    int getSize() const {
        return Size;
    }

    T& operator[](int index) {
        return data[index];
    }
};
```

使用：

```cpp
Array<int, 5> arr;
Array<double, 10> arr2;
```

这里：

```cpp
int
```

是类型参数。

```cpp
5
```

是非类型参数。

完整例子：

```cpp
#include <iostream>
using namespace std;

template <typename T, int Size>
class Array {
private:
    T data[Size];

public:
    int getSize() const {
        return Size;
    }

    T& operator[](int index) {
        return data[index];
    }
};

int main() {
    Array<int, 5> arr;

    arr[0] = 10;
    arr[1] = 20;

    cout << arr[0] << endl;
    cout << arr.getSize() << endl;

    return 0;
}
```

---

**十四、模板的本质**

模板不是一个真正的函数或类。

它更像是一个“代码生成器”。

比如：

```cpp
template <typename T>
T myMax(T a, T b) {
    return a > b ? a : b;
}
```

当你调用：

```cpp
myMax(10, 20);
```

编译器生成：

```cpp
int myMax(int a, int b) {
    return a > b ? a : b;
}
```

当你调用：

```cpp
myMax(3.14, 2.71);
```

编译器生成：

```cpp
double myMax(double a, double b) {
    return a > b ? a : b;
}
```

所以模板是在编译期工作的。

---

**十五、模板为什么通常写在头文件**

普通函数可以：

```text
.h 声明
.cpp 定义
```

但模板通常要把定义也写在头文件里。

原因是：

```text
编译器在使用模板时，需要看到完整定义，才能根据类型生成代码。
```

所以你经常会看到模板类全部写在 `.h` 或 `.hpp` 文件中。

例如：

```cpp
// MyArray.hpp
template <typename T>
class MyArray {
    ...
};
```

---

**十六、模板和 STL 的关系**

STL 里大量使用模板。

比如：

```cpp
vector<int> v1;
vector<string> v2;
```

可以粗略理解成：

```cpp
template <typename T>
class vector {
    ...
};
```

当你写：

```cpp
vector<int>
```

编译器生成一个能存 `int` 的 vector。

当你写：

```cpp
vector<string>
```

编译器生成一个能存 `string` 的 vector。

再比如：

```cpp
map<string, int>
```

可以理解成：

```cpp
template <typename Key, typename Value>
class map {
    ...
};
```

---

**十七、模板的优点**

模板的优点：

```text
代码复用
类型安全
编译期生成，效率高
STL 基础
支持泛型编程
```

例如：

```cpp
template <typename T>
void swapValue(T& a, T& b) {
    T temp = a;
    a = b;
    b = temp;
}
```

这个函数可以交换：

```cpp
int
double
string
自定义类
```

只要这个类型支持赋值。

---

**十八、模板的缺点**

模板也有一些缺点：

```text
错误信息可能很长
编译时间可能增加
代码写法更复杂
模板定义通常要放头文件
```

初学阶段不用害怕，先掌握最常用的写法。

---

**十九、完整案例：模板数组类**

```cpp
#include <iostream>
using namespace std;

template <typename T>
class MyArray {
private:
    T* data;
    int size;

public:
    MyArray(int s) : size(s) {
        data = new T[size];
    }

    MyArray(const MyArray& other) : size(other.size) {
        data = new T[size];

        for (int i = 0; i < size; i++) {
            data[i] = other.data[i];
        }
    }

    MyArray& operator=(const MyArray& other) {
        if (this != &other) {
            delete[] data;

            size = other.size;
            data = new T[size];

            for (int i = 0; i < size; i++) {
                data[i] = other.data[i];
            }
        }

        return *this;
    }

    ~MyArray() {
        delete[] data;
    }

    T& operator[](int index) {
        return data[index];
    }

    const T& operator[](int index) const {
        return data[index];
    }

    int getSize() const {
        return size;
    }
};
```

使用：

```cpp
int main() {
    MyArray<int> arr(3);

    arr[0] = 10;
    arr[1] = 20;
    arr[2] = 30;

    for (int i = 0; i < arr.getSize(); i++) {
        cout << arr[i] << endl;
    }

    MyArray<string> names(2);
    names[0] = "张三";
    names[1] = "李四";

    for (int i = 0; i < names.getSize(); i++) {
        cout << names[i] << endl;
    }

    return 0;
}
```

这个例子把你之前学的内容串起来了：

```text
类模板
动态内存
析构函数
拷贝构造
赋值运算符
operator[]
```

---

**二十、你需要掌握的重点**

模板这一章先掌握：

```text
1. 函数模板怎么写
2. 类模板怎么写
3. typename T 是类型占位符
4. 模板可以有多个参数
5. 类模板使用时要写 Box<int>
6. 模板函数里用到的操作，类型必须支持
7. 模板通常写在头文件
8. STL 大量基于模板
```

最常用模板：

```cpp
template <typename T>
T myMax(T a, T b) {
    return a > b ? a : b;
}
```

```cpp
template <typename T>
class Box {
private:
    T value;

public:
    Box(T v) : value(v) {
    }

    T getValue() const {
        return value;
    }
};
```

---

**二十一、练习题**

### 练习 1：函数模板

写一个函数模板：

```cpp
mySwap
```

可以交换两个同类型变量。

测试：

```cpp
int
double
string
```

---

### 练习 2：Box 类模板

写一个 `Box<T>` 类：

```text
成员：
T value

函数：
setValue
getValue
```

测试：

```cpp
Box<int>
Box<string>
Box<double>
```

---

### 练习 3：Pair 类模板

写一个 `Pair<K, V>` 类：

```text
成员：
key
value

函数：
getKey
getValue
```

测试：

```cpp
Pair<string, int> p("age", 18);
```

---

### 练习 4：Array 类模板

写一个：

```cpp
template <typename T, int Size>
class Array
```

支持：

```cpp
operator[]
getSize()
```

测试：

```cpp
Array<int, 5>
Array<string, 3>
```

---

你下一步应该学习：

```text
STL 标准模板库
```

也就是：

```text
vector
string
deque
list
map
set
stack
queue
algorithm
迭代器
```

这会让你的 C++ 编程效率大幅提升，因为真实开发中不会总是自己写数组、链表、排序，很多都直接用 STL。