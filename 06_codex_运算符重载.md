你现在已经学完：

```text
类和对象
封装
继承
多态
拷贝构造
赋值运算符
析构函数
移动语义
五法则
```

下一步非常适合学习：

```text
运算符重载
```

你之前已经见过：

```cpp
operator=
```

这就是运算符重载的一种。

现在我们系统学习它。

---

**一、什么是运算符重载**

C++ 中很多运算符默认只能用于内置类型：

```cpp
int a = 10;
int b = 20;
int c = a + b;
```

这里 `+` 可以用于 `int`。

但如果是自定义类：

```cpp
class Point {
public:
    int x;
    int y;
};
```

你不能直接写：

```cpp
Point p3 = p1 + p2;
```

因为编译器不知道两个 `Point` 相加是什么意思。

但是你可以自己定义：

```cpp
Point operator+(const Point& other)
```

让 `+` 支持你的类。

这就叫运算符重载。

---

**二、为什么需要运算符重载**

比如一个二维点：

```cpp
Point p1(1, 2);
Point p2(3, 4);
```

我们希望：

```cpp
Point p3 = p1 + p2;
```

效果等价于：

```cpp
Point p3(p1.x + p2.x, p1.y + p2.y);
```

如果不用运算符重载，你可能只能写：

```cpp
Point p3 = p1.add(p2);
```

也可以，但不够自然。

重载后代码更接近数学表达。

---

**三、基本语法**

运算符重载函数的名字格式是：

```cpp
operator运算符
```

例如：

```cpp
operator+
operator-
operator==
operator=
operator[]
operator()
operator<<
```

可以写成成员函数：

```cpp
class Point {
public:
    Point operator+(const Point& other) {
    }
};
```

也可以写成普通函数：

```cpp
Point operator+(const Point& a, const Point& b) {
}
```

初学阶段，先掌握成员函数写法，再学习友元函数写法。

---

**四、重载 + 运算符**

例子：二维点相加。

```cpp
#include <iostream>
using namespace std;

class Point {
private:
    int x;
    int y;

public:
    Point(int x, int y) : x(x), y(y) {
    }

    Point operator+(const Point& other) const {
        return Point(x + other.x, y + other.y);
    }

    void show() const {
        cout << "(" << x << ", " << y << ")" << endl;
    }
};

int main() {
    Point p1(1, 2);
    Point p2(3, 4);

    Point p3 = p1 + p2;

    p3.show();

    return 0;
}
```

输出：

```text
(4, 6)
```

这里：

```cpp
p1 + p2
```

本质上等价于：

```cpp
p1.operator+(p2)
```

也就是说：

```cpp
p1
```

是调用者。

```cpp
p2
```

是参数 `other`。

---

**五、为什么 operator+ 后面加 const**

```cpp
Point operator+(const Point& other) const
```

第一个 `const`：

```cpp
const Point& other
```

表示不会修改右边的对象 `other`。

第二个 `const`：

```cpp
... const
```

表示不会修改左边的对象 `p1`。

因为：

```cpp
p1 + p2
```

理论上不应该改变 `p1` 和 `p2`，而应该返回一个新对象。

---

**六、重载 - 运算符**

```cpp
Point operator-(const Point& other) const {
    return Point(x - other.x, y - other.y);
}
```

完整：

```cpp
class Point {
private:
    int x;
    int y;

public:
    Point(int x, int y) : x(x), y(y) {
    }

    Point operator+(const Point& other) const {
        return Point(x + other.x, y + other.y);
    }

    Point operator-(const Point& other) const {
        return Point(x - other.x, y - other.y);
    }

    void show() const {
        cout << "(" << x << ", " << y << ")" << endl;
    }
};
```

使用：

```cpp
Point p1(5, 6);
Point p2(2, 3);

Point p3 = p1 - p2;
p3.show();
```

输出：

```text
(3, 3)
```

---

**七、重载 == 运算符**

比较两个对象是否相等。

```cpp
bool operator==(const Point& other) const {
    return x == other.x && y == other.y;
}
```

使用：

```cpp
Point p1(1, 2);
Point p2(1, 2);

if (p1 == p2) {
    cout << "相等" << endl;
}
```

完整例子：

```cpp
#include <iostream>
using namespace std;

class Point {
private:
    int x;
    int y;

public:
    Point(int x, int y) : x(x), y(y) {
    }

    bool operator==(const Point& other) const {
        return x == other.x && y == other.y;
    }
};

int main() {
    Point p1(1, 2);
    Point p2(1, 2);
    Point p3(3, 4);

    cout << (p1 == p2) << endl;
    cout << (p1 == p3) << endl;

    return 0;
}
```

输出：

```text
1
0
```

---

**八、重载 != 运算符**

通常可以基于 `==` 实现：

```cpp
bool operator!=(const Point& other) const {
    return !(*this == other);
}
```

这里：

```cpp
*this
```

表示当前对象本身。

完整：

```cpp
bool operator==(const Point& other) const {
    return x == other.x && y == other.y;
}

bool operator!=(const Point& other) const {
    return !(*this == other);
}
```

---

**九、重载 < 运算符**

`<` 常用于排序。

例如我们写一个学生类，按成绩排序：

```cpp
class Student {
private:
    string name;
    int score;

public:
    Student(string name, int score) : name(name), score(score) {
    }

    bool operator<(const Student& other) const {
        return score < other.score;
    }

    void show() const {
        cout << name << " " << score << endl;
    }
};
```

这样可以配合 `sort` 使用：

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class Student {
private:
    string name;
    int score;

public:
    Student(string name, int score) : name(name), score(score) {
    }

    bool operator<(const Student& other) const {
        return score < other.score;
    }

    void show() const {
        cout << name << " " << score << endl;
    }
};

int main() {
    vector<Student> students = {
        Student("张三", 90),
        Student("李四", 80),
        Student("王五", 95)
    };

    sort(students.begin(), students.end());

    for (const Student& s : students) {
        s.show();
    }

    return 0;
}
```

输出：

```text
李四 80
张三 90
王五 95
```

因为 `operator<` 定义了“谁更小”。

---

**十、重载 << 输出运算符**

我们希望直接这样输出对象：

```cpp
cout << p;
```

而不是：

```cpp
p.show();
```

这就需要重载：

```cpp
operator<<
```

通常它写成友元函数。

```cpp
#include <iostream>
using namespace std;

class Point {
private:
    int x;
    int y;

public:
    Point(int x, int y) : x(x), y(y) {
    }

    friend ostream& operator<<(ostream& out, const Point& p);
};

ostream& operator<<(ostream& out, const Point& p) {
    out << "(" << p.x << ", " << p.y << ")";
    return out;
}
```

使用：

```cpp
int main() {
    Point p(1, 2);

    cout << p << endl;

    return 0;
}
```

输出：

```text
(1, 2)
```

---

**十一、为什么 operator<< 要返回 ostream&**

因为这样可以支持连续输出：

```cpp
cout << p << endl;
```

它的执行过程类似：

```cpp
(cout << p) << endl;
```

所以：

```cpp
operator<<(cout, p)
```

必须返回 `cout` 本身，才能继续输出 `endl`。

因此写：

```cpp
return out;
```

---

**十二、为什么 operator<< 常写成 friend**

如果写成成员函数：

```cpp
p.operator<<(cout);
```

那调用形式会变成：

```cpp
p << cout;
```

这不符合我们习惯。

我们想要的是：

```cpp
cout << p;
```

左边是 `cout`，它不是我们的 `Point` 类对象。

所以一般写成普通函数：

```cpp
ostream& operator<<(ostream& out, const Point& p)
```

但它需要访问 `Point` 的私有成员 `x`、`y`，所以声明为 `friend`。

---

**十三、重载 >> 输入运算符**

同理，可以让对象支持：

```cpp
cin >> p;
```

例子：

```cpp
#include <iostream>
using namespace std;

class Point {
private:
    int x;
    int y;

public:
    Point(int x = 0, int y = 0) : x(x), y(y) {
    }

    friend istream& operator>>(istream& in, Point& p);
    friend ostream& operator<<(ostream& out, const Point& p);
};

istream& operator>>(istream& in, Point& p) {
    in >> p.x >> p.y;
    return in;
}

ostream& operator<<(ostream& out, const Point& p) {
    out << "(" << p.x << ", " << p.y << ")";
    return out;
}

int main() {
    Point p;

    cin >> p;
    cout << p << endl;

    return 0;
}
```

输入：

```text
3 4
```

输出：

```text
(3, 4)
```

---

**十四、重载 [] 运算符**

`[]` 常用于让对象像数组一样访问数据。

例如自定义数组类：

```cpp
class MyArray {
private:
    int data[5];

public:
    int& operator[](int index) {
        return data[index];
    }
};
```

使用：

```cpp
MyArray arr;
arr[0] = 10;
cout << arr[0] << endl;
```

完整例子：

```cpp
#include <iostream>
using namespace std;

class MyArray {
private:
    int data[5];

public:
    int& operator[](int index) {
        return data[index];
    }

    const int& operator[](int index) const {
        return data[index];
    }
};

int main() {
    MyArray arr;

    arr[0] = 10;
    arr[1] = 20;

    cout << arr[0] << endl;
    cout << arr[1] << endl;

    return 0;
}
```

为什么返回引用？

因为我们希望支持：

```cpp
arr[0] = 10;
```

如果返回 `int`，返回的是副本，不能修改原数据。

---

**十五、重载 () 运算符**

`()` 叫函数调用运算符。

重载它之后，对象可以像函数一样使用。

```cpp
class Add {
public:
    int operator()(int a, int b) const {
        return a + b;
    }
};
```

使用：

```cpp
Add add;
cout << add(3, 4) << endl;
```

输出：

```text
7
```

这种对象叫：

```text
函数对象
仿函数
functor
```

完整例子：

```cpp
#include <iostream>
using namespace std;

class Multiply {
public:
    int operator()(int a, int b) const {
        return a * b;
    }
};

int main() {
    Multiply mul;

    cout << mul(3, 4) << endl;

    return 0;
}
```

---

**十六、前置 ++ 和后置 ++**

这部分稍微特殊。

前置：

```cpp
++x
```

后置：

```cpp
x++
```

对类来说可以分别重载。

例子：计数器类。

```cpp
class Counter {
private:
    int value;

public:
    Counter(int v = 0) : value(v) {
    }

    Counter& operator++() {
        value++;
        return *this;
    }

    Counter operator++(int) {
        Counter temp = *this;
        value++;
        return temp;
    }

    int getValue() const {
        return value;
    }
};
```

区别：

```cpp
Counter& operator++()
```

表示前置 `++`。

```cpp
Counter operator++(int)
```

表示后置 `++`。

这里的 `int` 只是一个占位参数，用来区分前置和后置。

使用：

```cpp
Counter c(10);

++c;
cout << c.getValue() << endl; // 11

c++;
cout << c.getValue() << endl; // 12
```

---

**十七、哪些运算符不能重载**

大多数运算符都可以重载，但有几个不能重载：

```cpp
.
::
?:
sizeof
typeid
```

初学阶段知道即可。

---

**十八、运算符重载的原则**

运算符重载虽然强大，但不能乱用。

### 1. 含义要自然

比如：

```cpp
Point p3 = p1 + p2;
```

表示坐标相加，比较自然。

但如果你让：

```cpp
p1 + p2
```

表示删除文件、连接数据库，这就很奇怪。

---

### 2. 不要改变运算符习惯

例如 `+` 一般不应该修改左右操作数。

```cpp
Point p3 = p1 + p2;
```

不应该改变 `p1` 或 `p2`。

如果要修改自己，应该用：

```cpp
+=
```

---

### 3. 成员函数还是友元函数

一般建议：

```text
=、[]、()、-> 必须或通常写成成员函数
<<、>> 通常写成友元函数
+、-、== 可以写成成员函数或普通函数
```

---

**十九、完整案例：Complex 复数类**

这是运算符重载的经典案例。

```cpp
#include <iostream>
using namespace std;

class Complex {
private:
    double real;
    double imag;

public:
    Complex(double r = 0, double i = 0) : real(r), imag(i) {
    }

    Complex operator+(const Complex& other) const {
        return Complex(real + other.real, imag + other.imag);
    }

    Complex operator-(const Complex& other) const {
        return Complex(real - other.real, imag - other.imag);
    }

    bool operator==(const Complex& other) const {
        return real == other.real && imag == other.imag;
    }

    bool operator!=(const Complex& other) const {
        return !(*this == other);
    }

    friend ostream& operator<<(ostream& out, const Complex& c);
};

ostream& operator<<(ostream& out, const Complex& c) {
    out << c.real;

    if (c.imag >= 0) {
        out << " + " << c.imag << "i";
    } else {
        out << " - " << -c.imag << "i";
    }

    return out;
}

int main() {
    Complex c1(3, 4);
    Complex c2(1, -2);

    Complex c3 = c1 + c2;
    Complex c4 = c1 - c2;

    cout << c3 << endl;
    cout << c4 << endl;

    cout << (c1 == c2) << endl;

    return 0;
}
```

输出：

```text
4 + 2i
2 + 6i
0
```

---

**二十、你需要掌握的重点**

这一章重点掌握：

```text
1. operator+ 怎么写
2. operator== 怎么写
3. operator<< 为什么常用 friend
4. operator[] 为什么返回引用
5. operator() 可以让对象像函数
6. 前置++和后置++的区别
7. 运算符重载不要乱用，要符合直觉
```

最重要的模板：

```cpp
类名 operator+(const 类名& other) const {
    return 类名(...);
}
```

```cpp
bool operator==(const 类名& other) const {
    return ...;
}
```

```cpp
friend ostream& operator<<(ostream& out, const 类名& obj);
```

```cpp
类型& operator[](int index) {
    return data[index];
}
```

---

**二十一、练习题**

### 练习 1：Point 类

写一个 `Point` 类，包含：

```text
x
y
```

重载：

```text
+
-
==
!=
<<
```

测试：

```cpp
Point p1(1, 2);
Point p2(3, 4);

cout << p1 + p2 << endl;
cout << p1 - p2 << endl;
cout << (p1 == p2) << endl;
```

---

### 练习 2：Complex 复数类

写一个复数类，包含：

```text
real
imag
```

重载：

```text
+
-
*
==
<<
```

复数乘法公式：

```text
(a + bi)(c + di) = (ac - bd) + (ad + bc)i
```

---

### 练习 3：MyString 简化字符串类

写一个简化版 `MyString`：

```text
成员：
char* data
```

要求实现：

```text
构造函数
析构函数
拷贝构造
赋值运算符
operator+
operator==
operator[]
operator<<
```

这个练习会把你前面学的深拷贝、五法则、运算符重载串起来，很有价值。

---

你下一步建议学习：

```text
模板
```

也就是：

```text
函数模板
类模板
泛型编程
```

学完模板之后，你就能真正理解 STL 里面的 `vector<int>`、`vector<string>`、`map<string, int>` 为什么可以这样写。