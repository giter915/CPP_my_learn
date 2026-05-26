好，我们继续学习：

```text
现代 C++ 常用特性
```

这部分会让你的代码更简洁、更安全、更像现代 C++，而不是老式 C++。

重点学习：

```text
auto
范围 for
nullptr
enum class
结构化绑定
std::optional
constexpr
using
```

---

**一、auto**

`auto` 让编译器自动推导变量类型。

以前写：

```cpp
std::vector<int>::iterator it = v.begin();
```

现在可以写：

```cpp
auto it = v.begin();
```

例子：

```cpp
int x = 10;
auto y = x;
```

编译器会推导：

```cpp
y 是 int
```

---

**二、auto 适合什么时候用**

适合类型很长、很明显的时候：

```cpp
auto it = students.begin();
auto result = findStudentByName("张三");
```

不太建议滥用到让人看不出类型：

```cpp
auto x = getData();
```

如果 `getData()` 不清楚返回什么，读代码的人会困惑。

一个经验：

```text
右边类型明显，或者类型很长时，用 auto
否则显式写类型
```

---

**三、范围 for**

以前遍历 vector：

```cpp
for (int i = 0; i < v.size(); i++) {
    cout << v[i] << endl;
}
```

现在可以写：

```cpp
for (int x : v) {
    cout << x << endl;
}
```

如果要修改元素：

```cpp
for (int& x : v) {
    x *= 2;
}
```

如果元素是大对象，推荐 const 引用：

```cpp
for (const Student& student : students) {
    student.show();
}
```

也可以用 auto：

```cpp
for (const auto& student : students) {
    student.show();
}
```

---

**四、nullptr**

老式 C++ 常用：

```cpp
int* p = NULL;
```

现代 C++ 推荐：

```cpp
int* p = nullptr;
```

`nullptr` 是真正的空指针常量，类型更安全。

例如：

```cpp
void f(int);
void f(int*);

f(NULL);     // 可能匹配 int 版本
f(nullptr);  // 明确匹配指针版本
```

以后写空指针，优先用：

```cpp
nullptr
```

---

**五、using 类型别名**

以前：

```cpp
typedef std::vector<std::string> StringList;
```

现代 C++ 推荐：

```cpp
using StringList = std::vector<std::string>;
```

例子：

```cpp
using ScoreMap = std::map<std::string, int>;

ScoreMap scores;
```

这让复杂类型更好读。

---

**六、enum class**

老式枚举：

```cpp
enum Color {
    Red,
    Green,
    Blue
};
```

问题是枚举值会进入外部作用域。

现代推荐：

```cpp
enum class Color {
    Red,
    Green,
    Blue
};
```

使用：

```cpp
Color c = Color::Red;
```

更安全、更清晰。

例子：

```cpp
enum class Role {
    Student,
    Teacher,
    Admin
};

void printRole(Role role) {
    if (role == Role::Student) {
        std::cout << "Student" << std::endl;
    }
}
```

---

**七、结构化绑定**

C++17 引入。

它可以方便地拆解 pair、tuple、map 元素。

以前遍历 map：

```cpp
for (const auto& pair : scores) {
    cout << pair.first << " " << pair.second << endl;
}
```

现在可以写：

```cpp
for (const auto& [name, score] : scores) {
    cout << name << " " << score << endl;
}
```

例子：

```cpp
std::map<std::string, int> scores = {
    {"张三", 90},
    {"李四", 85}
};

for (const auto& [name, score] : scores) {
    std::cout << name << " " << score << std::endl;
}
```

---

**八、std::optional**

`std::optional` 表示：

```text
可能有值，也可能没有值
```

头文件：

```cpp
#include <optional>
```

场景：查找学生。

以前可能这样：

```cpp
Student* findStudent(const std::string& name);
```

找不到返回：

```cpp
nullptr
```

现代也可以用：

```cpp
std::optional<Student> findStudent(const std::string& name);
```

例子：

```cpp
#include <iostream>
#include <optional>
#include <string>
#include <vector>

class Student {
public:
    std::string name;
    int score;
};

std::optional<Student> findStudent(
    const std::vector<Student>& students,
    const std::string& name
) {
    for (const auto& student : students) {
        if (student.name == name) {
            return student;
        }
    }

    return std::nullopt;
}

int main() {
    std::vector<Student> students = {
        {"张三", 90},
        {"李四", 85}
    };

    auto result = findStudent(students, "张三");

    if (result.has_value()) {
        std::cout << result->name << " " << result->score << std::endl;
    } else {
        std::cout << "没找到" << std::endl;
    }

    return 0;
}
```

---

**九、optional 的常用操作**

```cpp
std::optional<int> value;
```

没有值：

```cpp
std::nullopt
```

判断是否有值：

```cpp
if (value.has_value()) {
}
```

也可以：

```cpp
if (value) {
}
```

取值：

```cpp
value.value()
```

或者：

```cpp
*value
```

对于对象：

```cpp
result->name
```

默认值：

```cpp
int x = value.value_or(0);
```

---

**十、optional 适合什么时候用**

适合表达：

```text
查找可能失败
解析可能失败
计算结果可能不存在
```

例如：

```cpp
std::optional<Student> findByName(...);
std::optional<int> parseInt(...);
std::optional<double> calculateAverage(...);
```

比返回特殊值更清楚。

不推荐：

```cpp
return -1;
```

因为 `-1` 可能和正常值混淆。

---

**十一、constexpr**

`constexpr` 表示编译期常量或可在编译期计算。

例子：

```cpp
constexpr int MaxScore = 100;
```

比普通 `const` 更强调：

```text
这个值可以在编译期确定
```

函数也可以是 constexpr：

```cpp
constexpr int square(int x) {
    return x * x;
}
```

使用：

```cpp
constexpr int result = square(5);
```

结果在编译期就能算出来。

---

**十二、const 和 constexpr 简单区别**

```cpp
const int x = 10;
```

表示 x 不能修改。

```cpp
constexpr int y = 10;
```

表示 y 是编译期常量。

初学可以这样理解：

```text
const：只读
constexpr：编译期就能确定的只读
```

---

**十三、统一初始化 {}**

现代 C++ 推荐使用 `{}` 初始化。

```cpp
int x{10};
std::vector<int> v{1, 2, 3};
Student s{"张三", 90};
```

它可以减少一些隐式窄化转换。

例如：

```cpp
int x = 3.14;  // 允许，但丢失小数
int y{3.14};  // 编译错误，更安全
```

---

**十四、默认成员初始化**

以前：

```cpp
class Student {
private:
    int score;

public:
    Student() : score(0) {
    }
};
```

现在可以：

```cpp
class Student {
private:
    int score = 0;
};
```

如果成员有默认值，可以直接写在类里：

```cpp
class Config {
private:
    int timeout = 30;
    bool debug = false;
};
```

---

**十五、= default 和 = delete**

如果你想明确使用默认构造函数：

```cpp
class Student {
public:
    Student() = default;
};
```

如果你想禁止拷贝：

```cpp
class Resource {
public:
    Resource() = default;

    Resource(const Resource&) = delete;
    Resource& operator=(const Resource&) = delete;
};
```

`unique_ptr` 就是不能拷贝，只能移动。

---

**十六、override 和 final**

你之前学多态时见过 `override`。

```cpp
class Dog : public Animal {
public:
    void speak() override {
        std::cout << "dog" << std::endl;
    }
};
```

建议重写虚函数时都写 `override`。

`final` 表示不能再被继承或重写。

```cpp
class Dog final : public Animal {
};
```

或者：

```cpp
void speak() override final {
}
```

---

**十七、现代 C++ 综合例子**

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <optional>
#include <map>

enum class Role {
    Student,
    Teacher
};

class Person {
private:
    std::string name;
    Role role;

public:
    Person(std::string name, Role role)
        : name(std::move(name)), role(role) {
    }

    const std::string& getName() const {
        return name;
    }

    Role getRole() const {
        return role;
    }
};

std::optional<Person> findPerson(
    const std::vector<Person>& people,
    const std::string& name
) {
    for (const auto& person : people) {
        if (person.getName() == name) {
            return person;
        }
    }

    return std::nullopt;
}

int main() {
    std::vector<Person> people{
        {"张三", Role::Student},
        {"李老师", Role::Teacher}
    };

    auto result = findPerson(people, "张三");

    if (result) {
        std::cout << result->getName() << std::endl;
    }

    std::map<std::string, int> scores{
        {"张三", 90},
        {"李四", 85}
    };

    for (const auto& [name, score] : scores) {
        std::cout << name << " " << score << std::endl;
    }

    return 0;
}
```

这个例子用到了：

```text
auto
范围 for
enum class
optional
结构化绑定
std::move
统一初始化
const 引用
```

---

**十八、你需要掌握的重点**

这一章重点掌握：

```text
1. auto 用于类型明显或很长的地方
2. 范围 for 简化遍历
3. nullptr 替代 NULL
4. using 替代 typedef
5. enum class 更安全
6. 结构化绑定适合 pair/map
7. optional 表示可能没有值
8. constexpr 表示编译期常量
9. {} 初始化更安全
10. override 帮你检查虚函数重写
```

---

**十九、练习题**

### 练习 1：auto 和范围 for

用：

```cpp
std::vector<int>
```

存一组整数，使用范围 for 输出，并用 `auto&` 把所有数字乘 2。

---

### 练习 2：enum class

定义：

```cpp
enum class UserRole {
    Guest,
    Member,
    Admin
};
```

写函数：

```cpp
std::string roleToString(UserRole role);
```

---

### 练习 3：optional 查找学生

写：

```cpp
std::optional<Student> findByName(...)
```

找到了返回学生，没找到返回：

```cpp
std::nullopt
```

---

### 练习 4：结构化绑定统计单词

用：

```cpp
std::map<std::string, int>
```

统计单词出现次数，然后用：

```cpp
for (const auto& [word, count] : wordCounts)
```

输出。

---

### 练习 5：constexpr

写：

```cpp
constexpr int square(int x)
```

并用它初始化一个：

```cpp
constexpr int result
```

---

下一步建议学习：

```text
数据结构与算法基础
```

比如：

```text
时间复杂度
数组
链表
栈
队列
哈希表
树
排序
查找
递归
```

你已经掌握了不少 C++ 语法，接下来学习算法和数据结构，会让你真正具备解决问题的能力。