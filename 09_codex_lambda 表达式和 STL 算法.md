你现在已经学完：

```text
STL 容器
vector
string
map
set
stack
queue
priority_queue
迭代器
基础 algorithm
```

下一步建议学习：

```text
lambda 表达式和 STL 算法
```

这是现代 C++ 非常常用的一部分。

---

**一、什么是 lambda 表达式**

lambda 表达式可以理解为：

```text
临时写出来的小函数
匿名函数
```

比如你之前见过：

```cpp
sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
    return a.score > b.score;
});
```

这里：

```cpp
[](const Student& a, const Student& b) {
    return a.score > b.score;
}
```

就是 lambda。

它没有名字，但可以像函数一样被调用。

---

**二、为什么需要 lambda**

假设你要给学生按成绩从高到低排序。

不用 lambda，你可能要写一个普通函数：

```cpp
bool compareStudent(const Student& a, const Student& b) {
    return a.score > b.score;
}
```

然后：

```cpp
sort(students.begin(), students.end(), compareStudent);
```

这当然可以。

但如果这个比较逻辑只用一次，单独写一个函数就有点麻烦。

于是可以直接写：

```cpp
sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
    return a.score > b.score;
});
```

lambda 的优点：

```text
就地定义
只在需要的地方使用
适合配合 STL 算法
代码更紧凑
```

---

**三、lambda 基本语法**

完整形式：

```cpp
[捕获列表](参数列表) -> 返回类型 {
    函数体
};
```

例如：

```cpp
[](int a, int b) -> int {
    return a + b;
}
```

返回类型通常可以省略：

```cpp
[](int a, int b) {
    return a + b;
}
```

最简单例子：

```cpp
#include <iostream>
using namespace std;

int main() {
    auto add = [](int a, int b) {
        return a + b;
    };

    cout << add(3, 4) << endl;

    return 0;
}
```

输出：

```text
7
```

这里：

```cpp
auto add
```

保存了这个 lambda。

---

**四、没有参数的 lambda**

```cpp
auto sayHello = []() {
    cout << "Hello" << endl;
};

sayHello();
```

如果没有参数，`()` 有时也可以省略：

```cpp
auto sayHello = [] {
    cout << "Hello" << endl;
};
```

---

**五、lambda 的捕获列表**

最前面的 `[]` 叫捕获列表。

它决定 lambda 能不能使用外部变量。

例如：

```cpp
int x = 10;

auto f = []() {
    cout << x << endl; // 错误
};
```

因为 `x` 是外部变量，lambda 默认不能访问。

如果想访问，需要捕获。

---

**六、值捕获**

```cpp
int x = 10;

auto f = [x]() {
    cout << x << endl;
};

f();
```

输出：

```text
10
```

`[x]` 表示把外部变量 `x` 的值复制一份到 lambda 内部。

注意是复制。

```cpp
int x = 10;

auto f = [x]() {
    cout << x << endl;
};

x = 20;

f();
```

输出：

```text
10
```

因为 lambda 捕获时复制的是当时的值。

---

**七、引用捕获**

如果希望 lambda 使用外部变量本身，可以用引用捕获：

```cpp
int x = 10;

auto f = [&x]() {
    cout << x << endl;
};

x = 20;

f();
```

输出：

```text
20
```

`[&x]` 表示捕获 `x` 的引用。

引用捕获可以修改外部变量：

```cpp
int x = 10;

auto f = [&x]() {
    x = 100;
};

f();

cout << x << endl;
```

输出：

```text
100
```

---

**八、捕获所有变量**

值捕获所有外部变量：

```cpp
[=]()
```

例如：

```cpp
int a = 10;
int b = 20;

auto f = [=]() {
    cout << a + b << endl;
};
```

引用捕获所有外部变量：

```cpp
[&]()
```

例如：

```cpp
int a = 10;
int b = 20;

auto f = [&]() {
    a++;
    b++;
};
```

也可以混合：

```cpp
[=, &x]
```

表示默认值捕获，但 `x` 用引用捕获。

```cpp
[&, x]
```

表示默认引用捕获，但 `x` 用值捕获。

初学建议：

```text
需要读取外部变量：优先 [=] 或 [x]
需要修改外部变量：用 [&x]
不要随便滥用 [&]
```

---

**九、mutable lambda**

值捕获默认不能修改捕获进来的副本。

```cpp
int x = 10;

auto f = [x]() {
    x++; // 错误
};
```

如果你只是想修改 lambda 内部的那份副本，可以加 `mutable`：

```cpp
int x = 10;

auto f = [x]() mutable {
    x++;
    cout << x << endl;
};

f();
cout << x << endl;
```

输出：

```text
11
10
```

注意外部 `x` 没变。

---

**十、lambda 和 sort**

lambda 最常见用途之一就是排序。

```cpp
vector<int> v = {3, 1, 5, 2, 4};

sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;
});
```

这表示降序排序。

比较函数的规则是：

```text
如果希望 a 排在 b 前面，就返回 true
```

所以：

```cpp
return a > b;
```

表示大的排前面。

---

**十一、按对象成员排序**

```cpp
class Student {
public:
    string name;
    int score;
};

vector<Student> students = {
    {"张三", 90},
    {"李四", 85},
    {"王五", 95}
};

sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
    return a.score > b.score;
});
```

如果成绩相同，再按姓名排序：

```cpp
sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
    if (a.score != b.score) {
        return a.score > b.score;
    }

    return a.name < b.name;
});
```

---

**十二、for_each**

`for_each` 可以对容器每个元素执行操作。

```cpp
#include <algorithm>
```

例子：

```cpp
vector<int> v = {1, 2, 3};

for_each(v.begin(), v.end(), [](int x) {
    cout << x << endl;
});
```

如果要修改元素：

```cpp
for_each(v.begin(), v.end(), [](int& x) {
    x *= 2;
});
```

不过实际开发中，简单遍历通常直接用范围 for 更清楚。

---

**十三、find_if**

`find_if` 用条件查找元素。

```cpp
vector<int> v = {1, 3, 5, 8, 9};

auto it = find_if(v.begin(), v.end(), [](int x) {
    return x % 2 == 0;
});

if (it != v.end()) {
    cout << "找到：" << *it << endl;
}
```

输出：

```text
找到：8
```

`find_if` 返回迭代器。

如果没找到，返回：

```cpp
v.end()
```

---

**十四、count_if**

`count_if` 用来统计满足条件的元素个数。

```cpp
vector<int> v = {1, 2, 3, 4, 5, 6};

int count = count_if(v.begin(), v.end(), [](int x) {
    return x % 2 == 0;
});

cout << count << endl;
```

输出：

```text
3
```

---

**十五、copy_if**

`copy_if` 可以把满足条件的元素复制到另一个容器。

```cpp
vector<int> v = {1, 2, 3, 4, 5};
vector<int> even;

copy_if(v.begin(), v.end(), back_inserter(even), [](int x) {
    return x % 2 == 0;
});
```

需要头文件：

```cpp
#include <iterator>
```

这里：

```cpp
back_inserter(even)
```

表示把元素通过 `push_back` 放进 `even`。

---

**十六、remove_if**

`remove_if` 用于“移除”满足条件的元素，但它本身不会真正缩短 vector。

通常要配合 `erase`，这叫 erase-remove idiom。

删除所有偶数：

```cpp
vector<int> v = {1, 2, 3, 4, 5, 6};

v.erase(
    remove_if(v.begin(), v.end(), [](int x) {
        return x % 2 == 0;
    }),
    v.end()
);
```

结果：

```text
1 3 5
```

这个写法很重要，建议记住。

---

**十七、transform**

`transform` 用于把一个容器转换成另一个容器。

例如把每个数平方：

```cpp
vector<int> v = {1, 2, 3};
vector<int> result;

transform(v.begin(), v.end(), back_inserter(result), [](int x) {
    return x * x;
});
```

结果：

```text
1 4 9
```

也可以原地修改：

```cpp
transform(v.begin(), v.end(), v.begin(), [](int x) {
    return x * 2;
});
```

---

**十八、all_of、any_of、none_of**

这三个算法用于判断整体条件。

```cpp
vector<int> v = {2, 4, 6};
```

是否全部是偶数：

```cpp
bool allEven = all_of(v.begin(), v.end(), [](int x) {
    return x % 2 == 0;
});
```

是否存在偶数：

```cpp
bool hasEven = any_of(v.begin(), v.end(), [](int x) {
    return x % 2 == 0;
});
```

是否没有负数：

```cpp
bool noNegative = none_of(v.begin(), v.end(), [](int x) {
    return x < 0;
});
```

---

**十九、accumulate**

`accumulate` 用于累加。

头文件：

```cpp
#include <numeric>
```

例子：

```cpp
vector<int> v = {1, 2, 3, 4};

int sum = accumulate(v.begin(), v.end(), 0);

cout << sum << endl;
```

输出：

```text
10
```

也可以自定义累加逻辑：

```cpp
int product = accumulate(v.begin(), v.end(), 1, [](int a, int b) {
    return a * b;
});
```

---

**二十、lambda 捕获外部条件**

比如你想找大于某个阈值的数：

```cpp
int threshold = 10;

auto it = find_if(v.begin(), v.end(), [threshold](int x) {
    return x > threshold;
});
```

如果阈值会改变，并希望 lambda 看到最新值：

```cpp
auto it = find_if(v.begin(), v.end(), [&threshold](int x) {
    return x > threshold;
});
```

---

**二十一、完整案例：学生筛选和排序**

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Student {
public:
    string name;
    int score;

    Student(string n, int s) : name(n), score(s) {
    }
};

int main() {
    vector<Student> students = {
        {"张三", 90},
        {"李四", 75},
        {"王五", 95},
        {"赵六", 60},
        {"钱七", 85}
    };

    vector<Student> passed;

    copy_if(students.begin(), students.end(), back_inserter(passed), [](const Student& s) {
        return s.score >= 80;
    });

    sort(passed.begin(), passed.end(), [](const Student& a, const Student& b) {
        return a.score > b.score;
    });

    for (const Student& s : passed) {
        cout << s.name << " " << s.score << endl;
    }

    return 0;
}
```

输出：

```text
王五 95
张三 90
钱七 85
```

这个例子用了：

```text
vector
lambda
copy_if
sort
back_inserter
范围 for
```

---

**二十二、常见错误**

### 1. 忘记捕获变量

错误：

```cpp
int limit = 10;

auto it = find_if(v.begin(), v.end(), [](int x) {
    return x > limit;
});
```

正确：

```cpp
auto it = find_if(v.begin(), v.end(), [limit](int x) {
    return x > limit;
});
```

---

### 2. sort 比较函数写错

比较函数必须表达严格弱序。

不要写：

```cpp
return a >= b;
```

应该写：

```cpp
return a > b;
```

如果写 `>=`，可能导致排序行为异常。

---

### 3. remove_if 后忘记 erase

错误：

```cpp
remove_if(v.begin(), v.end(), condition);
```

正确：

```cpp
v.erase(remove_if(v.begin(), v.end(), condition), v.end());
```

---

### 4. 修改元素却没用引用

```cpp
for_each(v.begin(), v.end(), [](int x) {
    x *= 2;
});
```

这样修改的是副本。

正确：

```cpp
for_each(v.begin(), v.end(), [](int& x) {
    x *= 2;
});
```

---

**二十三、你需要掌握的重点**

这一章重点掌握：

```text
1. lambda 基本语法
2. 捕获列表 []
3. 值捕获 [x]
4. 引用捕获 [&x]
5. sort + lambda
6. find_if
7. count_if
8. copy_if
9. remove_if + erase
10. transform
11. accumulate
```

最重要的模板：

```cpp
[](参数列表) {
    函数体
}
```

排序：

```cpp
sort(v.begin(), v.end(), [](const T& a, const T& b) {
    return a.xxx < b.xxx;
});
```

查找：

```cpp
auto it = find_if(v.begin(), v.end(), [](const T& x) {
    return 条件;
});
```

删除：

```cpp
v.erase(remove_if(v.begin(), v.end(), [](const T& x) {
    return 删除条件;
}), v.end());
```

---

**二十四、练习题**

### 练习 1：降序排序

给定：

```cpp
vector<int> v = {3, 1, 5, 2, 4};
```

使用 `sort + lambda` 从大到小排序。

---

### 练习 2：查找第一个大于 10 的数

```cpp
vector<int> v = {3, 7, 12, 5, 20};
```

使用：

```cpp
find_if
```

找第一个大于 10 的数。

---

### 练习 3：统计及格人数

定义：

```cpp
class Student {
public:
    string name;
    int score;
};
```

使用：

```cpp
count_if
```

统计分数大于等于 60 的人数。

---

### 练习 4：删除不及格学生

使用：

```cpp
remove_if + erase
```

删除分数小于 60 的学生。

---

### 练习 5：成绩整体加 5 分

使用：

```cpp
transform
```

把所有学生成绩加 5，但最高不超过 100。

---

你下一步建议学习：

```text
智能指针
```

也就是：

```text
unique_ptr
shared_ptr
weak_ptr
make_unique
make_shared
RAII
```

这会帮你逐渐摆脱手动 `new/delete`，写出更现代、更安全的 C++。