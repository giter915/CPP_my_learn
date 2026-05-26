现在继续学习：

```text
C++ 代码风格与设计原则
```

你前面已经学了很多“语法能力”，现在要开始学习“怎么写得更好”。

这部分不会介绍太多新语法，而是教你如何写出：

```text
更安全
更清晰
更容易维护
更不容易出 bug
```

的 C++ 代码。

---

**一、为什么要学代码风格和设计原则**

两段代码都能运行，但质量可能完全不同。

差的代码：

```cpp
int f(vector<int> a) {
    int x = 0;
    for (int i = 0; i < a.size(); i++) {
        x += a[i];
    }
    return x / a.size();
}
```

问题很多：

```text
函数名不清楚
参数复制了整个 vector
空 vector 会除以 0
整数除法可能丢精度
变量名没意义
```

更好的写法：

```cpp
double averageScore(const std::vector<int>& scores) {
    if (scores.empty()) {
        throw std::invalid_argument("scores cannot be empty");
    }

    int sum = 0;

    for (int score : scores) {
        sum += score;
    }

    return static_cast<double>(sum) / scores.size();
}
```

功能差不多，但清楚、安全很多。

---

**二、原则 1：让名字表达意图**

变量名、函数名、类名要让人一眼知道它干什么。

不推荐：

```cpp
int x;
int y;
void f();
class A;
```

推荐：

```cpp
int studentCount;
int totalScore;
void calculateAverageScore();
class StudentManager;
```

名字长一点没关系，清楚更重要。

---

**三、常见命名习惯**

C++ 命名风格没有唯一标准，但要保持统一。

常见风格：

```cpp
class StudentManager;       // 类型名：大驼峰
void addStudent();          // 函数名：小驼峰
int totalScore;             // 变量名：小驼峰
const int MaxStudentCount;  // 常量：可用大驼峰
```

也有项目喜欢：

```cpp
student_manager
add_student
total_score
```

重点不是哪种绝对正确，而是：

```text
一个项目里保持一致
```

---

**四、原则 2：函数只做一件事**

不推荐：

```cpp
void processStudents() {
    // 读取文件
    // 解析学生
    // 排序
    // 打印
    // 保存结果
}
```

太多职责混在一起。

更好：

```cpp
std::vector<Student> loadStudents(const std::string& filename);

void sortStudentsByScore(std::vector<Student>& students);

void printStudents(const std::vector<Student>& students);

void saveStudents(const std::vector<Student>& students, const std::string& filename);
```

每个函数职责清楚。

---

**五、原则 3：参数尽量用 const 引用**

如果参数是大对象，比如：

```cpp
std::vector<int>
std::string
Student
```

不需要复制时，用：

```cpp
const T&
```

例如：

```cpp
void printStudent(const Student& student);
void saveText(const std::string& text);
void processScores(const std::vector<int>& scores);
```

这样：

```text
避免复制
保证函数不会修改参数
```

---

**六、什么时候按值传递**

小类型可以按值传递：

```cpp
int
double
char
bool
```

例如：

```cpp
int add(int a, int b);
double area(double radius);
```

这些复制成本很低。

如果函数需要保存一份参数，也可以按值再移动：

```cpp
class Student {
private:
    std::string name;

public:
    Student(std::string name) : name(std::move(name)) {
    }
};
```

这是现代 C++ 常见写法，但初学可以先优先掌握 `const std::string&`。

---

**七、原则 4：能 const 就 const**

如果一个变量不应该被修改，加 `const`。

```cpp
const int maxScore = 100;
```

如果成员函数不修改对象，加 `const`。

```cpp
int getScore() const {
    return score;
}
```

如果函数参数不修改，加 `const` 引用。

```cpp
void printStudent(const Student& student);
```

`const` 的好处：

```text
表达意图
防止误修改
帮助编译器检查错误
```

---

**八、const 成员函数**

例如：

```cpp
class Student {
private:
    std::string name;
    int score;

public:
    int getScore() const {
        return score;
    }

    void setScore(int newScore) {
        score = newScore;
    }
};
```

`getScore()` 不修改对象，所以应该加 `const`。

这样你可以对 `const Student` 调用：

```cpp
void printScore(const Student& student) {
    std::cout << student.getScore() << std::endl;
}
```

如果 `getScore()` 没有 `const`，这里可能调用不了。

---

**九、原则 5：避免裸 new/delete**

现代 C++ 尽量避免：

```cpp
Student* p = new Student();
delete p;
```

优先：

```cpp
Student s;
```

或者：

```cpp
auto p = std::make_unique<Student>();
```

或者容器：

```cpp
std::vector<Student> students;
```

记住：

```text
优先栈对象
其次 STL 容器
需要动态所有权时用智能指针
尽量少手动 new/delete
```

---

**十、原则 6：类要有清晰职责**

一个类不要什么都管。

不推荐：

```cpp
class StudentManager {
public:
    void addStudent();
    void sortStudents();
    void saveToFile();
    void connectDatabase();
    void drawWindow();
    void sendEmail();
};
```

职责太多。

可以拆成：

```cpp
class StudentManager;
class StudentRepository;
class StudentPrinter;
class EmailService;
```

初学时不必拆得太碎，但要知道：

```text
一个类应该围绕一个明确概念工作
```

---

**十一、原则 7：封装数据**

不推荐：

```cpp
class Student {
public:
    std::string name;
    int score;
};
```

如果只是简单数据结构可以这样，但如果要控制合法性，应该封装：

```cpp
class Student {
private:
    std::string name;
    int score;

public:
    Student(const std::string& name, int score)
        : name(name), score(score) {
        if (score < 0 || score > 100) {
            throw std::invalid_argument("score out of range");
        }
    }

    int getScore() const {
        return score;
    }

    void setScore(int newScore) {
        if (newScore < 0 || newScore > 100) {
            throw std::invalid_argument("score out of range");
        }

        score = newScore;
    }
};
```

封装不是为了麻烦，而是为了保护对象始终合法。

---

**十二、原则 8：让对象始终处于有效状态**

不要允许对象创建后就是坏的。

不推荐：

```cpp
Student s;
s.setName("张三");
s.setScore(90);
```

如果忘了设置 score，对象状态不完整。

更好：

```cpp
Student s("张三", 90);
```

构造函数保证对象一创建就是有效的。

---

**十三、原则 9：少用全局变量**

不推荐：

```cpp
std::vector<Student> students;
```

全局变量的问题：

```text
任何地方都能改
难以追踪状态变化
测试困难
容易产生隐式依赖
```

更好：

```cpp
class StudentManager {
private:
    std::vector<Student> students;
};
```

把状态放到明确的对象里。

---

**十四、原则 10：错误处理要明确**

不要悄悄失败。

不推荐：

```cpp
int getScore(int index) {
    if (index < 0 || index >= scores.size()) {
        return -1;
    }

    return scores[index];
}
```

如果 `-1` 也可能是合法值，就混乱。

更明确：

```cpp
int getScore(int index) const {
    if (index < 0 || index >= scores.size()) {
        throw std::out_of_range("index out of range");
    }

    return scores[index];
}
```

或者返回 `std::optional`，这个可以以后学。

---

**十五、原则 11：优先使用标准库**

不要自己重复造基础工具。

比如排序：

```cpp
std::sort(v.begin(), v.end());
```

不要自己手写排序，除非你是在学习算法。

动态数组：

```cpp
std::vector<int>
```

字符串：

```cpp
std::string
```

键值表：

```cpp
std::map
std::unordered_map
```

资源管理：

```cpp
std::unique_ptr
std::shared_ptr
```

标准库通常更可靠。

---

**十六、原则 12：接口简单，内部复杂**

一个类对外暴露的 public 函数越多，别人越容易用错。

不推荐：

```cpp
class StudentManager {
public:
    std::vector<Student> students;

    void sortInternalData();
    void recalculateCache();
    void validateIndex();
};
```

更好：

```cpp
class StudentManager {
public:
    void addStudent(const Student& student);
    void removeStudent(const std::string& name);
    std::vector<Student> getStudentsSortedByScore() const;

private:
    std::vector<Student> students;
};
```

对外只暴露真正需要的操作。

---

**十七、原则 13：避免魔法数字**

不推荐：

```cpp
if (score >= 60) {
    cout << "pass";
}
```

`60` 是什么意思？

更好：

```cpp
const int PassingScore = 60;

if (score >= PassingScore) {
    std::cout << "pass";
}
```

这样更清楚。

---

**十八、原则 14：提前返回减少嵌套**

不推荐：

```cpp
void process(const std::vector<int>& nums) {
    if (!nums.empty()) {
        if (nums.size() > 3) {
            // do something
        }
    }
}
```

更好：

```cpp
void process(const std::vector<int>& nums) {
    if (nums.empty()) {
        return;
    }

    if (nums.size() <= 3) {
        return;
    }

    // do something
}
```

这样代码更平。

---

**十九、原则 15：头文件保持干净**

头文件里：

```text
少 include
不 using namespace std
只暴露必要声明
加 #pragma once
```

比如：

```cpp
#pragma once

#include <string>

class Student {
public:
    Student(const std::string& name, int score);

private:
    std::string name;
    int score;
};
```

---

**二十、一个较好的 Student 类示例**

```cpp
#pragma once

#include <string>
#include <stdexcept>

class Student {
private:
    std::string name;
    int score;

public:
    Student(const std::string& name, int score)
        : name(name), score(score) {
        if (name.empty()) {
            throw std::invalid_argument("name cannot be empty");
        }

        if (score < 0 || score > 100) {
            throw std::invalid_argument("score out of range");
        }
    }

    const std::string& getName() const {
        return name;
    }

    int getScore() const {
        return score;
    }

    void setScore(int newScore) {
        if (newScore < 0 || newScore > 100) {
            throw std::invalid_argument("score out of range");
        }

        score = newScore;
    }
};
```

这个类体现了：

```text
封装
构造时保证有效
const 正确性
参数检查
避免无意义 setName
返回 const 引用避免复制
```

---

**二十一、一个较好的 StudentManager 示例**

```cpp
#pragma once

#include "Student.h"
#include <vector>
#include <string>
#include <algorithm>
#include <stdexcept>

class StudentManager {
private:
    std::vector<Student> students;

public:
    void addStudent(const Student& student) {
        students.push_back(student);
    }

    const Student& findByName(const std::string& name) const {
        auto it = std::find_if(students.begin(), students.end(),
            [&name](const Student& student) {
                return student.getName() == name;
            }
        );

        if (it == students.end()) {
            throw std::runtime_error("student not found");
        }

        return *it;
    }

    std::vector<Student> getSortedByScore() const {
        std::vector<Student> result = students;

        std::sort(result.begin(), result.end(),
            [](const Student& a, const Student& b) {
                return a.getScore() > b.getScore();
            }
        );

        return result;
    }
};
```

注意：

```cpp
getSortedByScore()
```

没有修改内部 `students`，而是返回一个排序后的副本。

这是一种比较安全的设计。

---

**二十二、什么时候返回引用，什么时候返回值**

返回引用：

```cpp
const std::string& getName() const;
```

适合返回内部较大对象，避免复制。

但要小心：不要返回局部变量引用。

错误：

```cpp
const std::string& getName() {
    std::string temp = "abc";
    return temp; // 错误，temp 函数结束就销毁
}
```

返回值：

```cpp
std::vector<Student> getSortedByScore() const;
```

适合返回新生成的数据。

现代 C++ 有返回值优化和移动语义，返回对象通常不必过度担心性能。

---

**二十三、代码审查时检查什么**

你写完代码后，可以自己检查：

```text
名字是否清楚
函数是否太长
类职责是否太多
参数是否不必要复制
该 const 的地方是否 const
是否有裸 new/delete
是否有全局变量
错误处理是否明确
头文件是否污染命名空间
是否能用 STL 简化
```

---

**二十四、你需要掌握的重点**

这一章重点掌握：

```text
1. 名字表达意图
2. 函数只做一件事
3. 大对象参数用 const 引用
4. 能 const 就 const
5. 少用裸 new/delete
6. 类职责清晰
7. 封装数据，保护对象合法性
8. 构造函数让对象一创建就有效
9. 少用全局变量
10. 优先使用标准库
11. public 接口要简洁
12. 头文件不要 using namespace std
```

---

**二十五、练习题**

### 练习 1：重构函数名和变量名

把下面这种风格：

```cpp
int f(vector<int> a)
```

改成更清晰的：

```cpp
double calculateAverageScore(const std::vector<int>& scores)
```

并处理空数组。

---

### 练习 2：完善 Student 类

写一个 `Student` 类：

```text
name 不能为空
score 必须在 0 到 100
getName() const
getScore() const
setScore()
```

要求所有非法输入都抛异常。

---

### 练习 3：StudentManager

写一个 `StudentManager`：

```text
addStudent
findByName
getSortedByScore
getAverageScore
```

要求：

```text
参数用 const 引用
不暴露内部 vector
该 const 的函数加 const
```

---

### 练习 4：消除全局变量

写一个使用全局 `vector<Student>` 的程序，然后重构成 `StudentManager` 类。

---

### 练习 5：代码审查

拿你之前写过的一个练习程序，检查：

```text
是否有不清楚的名字
是否有不必要复制
是否有裸 new/delete
是否有函数太长
是否有 public 数据成员
```

并尝试改进。

---

下一步建议学习：

```text
C++ 常用现代特性
```

比如：

```text
auto
范围 for
nullptr
enum class
std::optional
std::variant
结构化绑定
constexpr
```

这些语法会让你的 C++ 代码更现代、更简洁。