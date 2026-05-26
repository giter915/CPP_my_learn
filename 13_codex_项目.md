现在继续学习：

```text
C++ 项目组织和头文件
```

也就是：

```text
.h / .cpp 分离
头文件保护
#pragma once
#include
命名空间
多文件编译
```

这部分非常重要。它决定你能不能从“写小练习”进入“写真正项目”。

---

**一、为什么要分文件**

刚开始学 C++ 时，我们通常写一个文件：

```cpp
main.cpp
```

所有代码都放里面：

```cpp
class Student {
    ...
};

int main() {
    ...
}
```

小程序可以这样，但程序一大就会混乱。

比如你有：

```text
Student 类
Teacher 类
Course 类
FileManager 类
工具函数
main 函数
```

全部放一个文件里会非常难维护。

所以真实项目通常分成：

```text
.h 文件：声明
.cpp 文件：实现
main.cpp：程序入口
```

---

**二、声明和实现**

C++ 里经常把“声明”和“实现”分开。

声明告诉编译器：

```text
有什么类
有什么函数
函数长什么样
```

实现告诉编译器：

```text
函数具体怎么做
```

例如：

```cpp
void hello();
```

这是声明。

```cpp
void hello() {
    cout << "hello" << endl;
}
```

这是实现。

---

**三、最简单的多文件结构**

假设我们写一个学生类。

项目结构：

```text
project/
    main.cpp
    Student.h
    Student.cpp
```

---

**四、Student.h**

头文件一般放类声明。

```cpp
#pragma once

#include <string>
using namespace std;

class Student {
private:
    string name;
    int score;

public:
    Student(string name, int score);

    void show() const;

    string getName() const;

    int getScore() const;
};
```

这里没有写函数具体内容，只写函数声明。

---

**五、Student.cpp**

源文件放函数实现。

```cpp
#include "Student.h"
#include <iostream>
using namespace std;

Student::Student(string name, int score)
    : name(name), score(score) {
}

void Student::show() const {
    cout << name << " " << score << endl;
}

string Student::getName() const {
    return name;
}

int Student::getScore() const {
    return score;
}
```

注意：

```cpp
Student::show
```

表示 `show` 是 `Student` 类的成员函数。

---

**六、main.cpp**

主文件使用这个类。

```cpp
#include "Student.h"

int main() {
    Student s("张三", 90);

    s.show();

    return 0;
}
```

这样结构就清楚了：

```text
Student.h：告诉别人 Student 怎么用
Student.cpp：实现 Student 怎么工作
main.cpp：使用 Student
```

---

**七、#include 是什么**

`#include` 的作用是把另一个文件的内容包含进来。

```cpp
#include <iostream>
```

包含标准库头文件。

```cpp
#include "Student.h"
```

包含你自己写的头文件。

区别：

```cpp
#include <...>
```

通常用于标准库或系统库。

```cpp
#include "..."
```

通常用于自己项目里的文件。

---

**八、为什么 .cpp 要 include 自己的 .h**

比如：

```cpp
#include "Student.h"
```

`Student.cpp` 需要知道 `Student` 类的声明，才能实现：

```cpp
Student::show()
```

否则编译器不知道 `Student` 是什么。

---

**九、为什么 main.cpp include .h 而不是 .cpp**

正确：

```cpp
#include "Student.h"
```

错误倾向：

```cpp
#include "Student.cpp"
```

一般不要 include `.cpp` 文件。

原因是 `.cpp` 文件应该单独参与编译，然后由链接器合并。

如果 include `.cpp`，容易造成重复定义。

---

**十、头文件保护**

如果一个头文件被重复包含，可能会导致重复定义。

例如：

```cpp
#include "Student.h"
#include "Student.h"
```

所以头文件需要保护。

现代常用：

```cpp
#pragma once
```

放在头文件第一行：

```cpp
#pragma once
```

意思是：

```text
这个头文件在一次编译中只包含一次
```

---

**十一、传统 include guard**

除了 `#pragma once`，也可以写传统保护：

```cpp
#ifndef STUDENT_H
#define STUDENT_H

class Student {
};

#endif
```

意思是：

```text
如果 STUDENT_H 没定义，就定义它，并包含下面内容
如果已经定义过，就跳过
```

现代项目中 `#pragma once` 很常见，更简单。

---

**十二、头文件里不要随便 using namespace std**

刚才为了简单写了：

```cpp
using namespace std;
```

但在真实项目中，不建议在头文件里写：

```cpp
using namespace std;
```

因为头文件会被很多文件 include。

如果头文件里写了 `using namespace std;`，会污染所有包含它的文件。

更推荐：

```cpp
#pragma once

#include <string>

class Student {
private:
    std::string name;
    int score;

public:
    Student(const std::string& name, int score);

    void show() const;

    std::string getName() const;

    int getScore() const;
};
```

然后 `.cpp` 里可以选择写：

```cpp
using namespace std;
```

或者也写 `std::`。

---

**十三、更规范的 Student.h**

```cpp
#pragma once

#include <string>

class Student {
private:
    std::string name;
    int score;

public:
    Student(const std::string& name, int score);

    void show() const;

    const std::string& getName() const;

    int getScore() const;
};
```

注意：

```cpp
const std::string& getName() const;
```

返回引用，避免复制字符串。

---

**十四、更规范的 Student.cpp**

```cpp
#include "Student.h"
#include <iostream>

Student::Student(const std::string& name, int score)
    : name(name), score(score) {
}

void Student::show() const {
    std::cout << name << " " << score << std::endl;
}

const std::string& Student::getName() const {
    return name;
}

int Student::getScore() const {
    return score;
}
```

---

**十五、更规范的 main.cpp**

```cpp
#include "Student.h"

int main() {
    Student s("张三", 90);

    s.show();

    return 0;
}
```

---

**十六、多文件编译**

如果你用命令行编译：

```bash
g++ main.cpp Student.cpp -o app
```

注意要把所有 `.cpp` 文件都交给编译器：

```text
main.cpp
Student.cpp
```

不要只编译：

```bash
g++ main.cpp -o app
```

否则会链接错误，因为 `Student` 的函数实现找不到。

---

**十七、常见链接错误**

如果你只编译 `main.cpp`：

```bash
g++ main.cpp -o app
```

可能出现类似：

```text
undefined reference to Student::show()
```

意思是：

```text
我知道有 Student::show 这个函数声明，
但找不到它的具体实现。
```

解决：

```bash
g++ main.cpp Student.cpp -o app
```

---

**十八、头文件里可以写什么**

头文件通常写：

```text
类声明
函数声明
常量声明
模板定义
inline 函数
结构体声明
枚举
```

例如：

```cpp
class Student;
void printHello();
```

---

**十九、头文件里不要写什么**

头文件里一般不要写：

```text
普通函数定义
全局变量定义
非 inline 的函数实现
using namespace std
```

例如，不推荐在 `.h` 里写：

```cpp
int globalValue = 10;
```

如果多个 `.cpp` include 这个头文件，就可能重复定义。

---

**二十、模板为什么常写在头文件**

你之前学过模板。

模板比较特殊，通常要把定义也写在头文件里。

例如：

```cpp
template <typename T>
T myMax(T a, T b) {
    return a > b ? a : b;
}
```

因为编译器需要看到模板完整定义，才能根据类型生成代码。

所以模板类经常写在：

```text
.hpp
```

文件里。

---

**二十一、命名空间 namespace**

项目大了以后，可能有名字冲突。

比如两个库都定义了：

```cpp
class Student
```

这就麻烦。

命名空间可以把名字包起来。

```cpp
namespace school {
    class Student {
    };
}
```

使用：

```cpp
school::Student s;
```

---

**二十二、命名空间例子**

`Student.h`：

```cpp
#pragma once

#include <string>

namespace school {

class Student {
private:
    std::string name;
    int score;

public:
    Student(const std::string& name, int score);

    void show() const;
};

}
```

`Student.cpp`：

```cpp
#include "Student.h"
#include <iostream>

namespace school {

Student::Student(const std::string& name, int score)
    : name(name), score(score) {
}

void Student::show() const {
    std::cout << name << " " << score << std::endl;
}

}
```

`main.cpp`：

```cpp
#include "Student.h"

int main() {
    school::Student s("张三", 90);

    s.show();

    return 0;
}
```

---

**二十三、using namespace 的使用建议**

在 `.cpp` 文件里可以适当写：

```cpp
using namespace std;
```

但在头文件里不建议。

对于自己的命名空间，可以局部使用：

```cpp
using school::Student;

Student s("张三", 90);
```

这比：

```cpp
using namespace school;
```

更精确。

---

**二十四、前向声明**

有时候你只需要告诉编译器“有这个类”，不需要包含完整头文件。

```cpp
class Teacher;
```

这叫前向声明。

适用于指针或引用成员：

```cpp
class Student {
private:
    Teacher* teacher;
};
```

因为指针大小固定，编译器不需要知道 `Teacher` 的完整结构。

但如果是对象成员：

```cpp
Teacher teacher;
```

就必须 include `Teacher.h`，因为编译器需要知道 `Teacher` 的大小。

---

**二十五、前向声明的例子**

`Student.h`：

```cpp
#pragma once

class Teacher;

class Student {
private:
    Teacher* teacher;

public:
    void setTeacher(Teacher* t);
};
```

`Student.cpp`：

```cpp
#include "Student.h"
#include "Teacher.h"

void Student::setTeacher(Teacher* t) {
    teacher = t;
}
```

头文件用前向声明，可以减少 include 依赖，加快编译。

---

**二十六、include 谁的原则**

简单规则：

```text
头文件里需要完整类型，就 include 对应头文件
头文件里只用指针/引用，可以考虑前向声明
cpp 文件里用到了具体成员函数，就 include 对应头文件
```

例子：

```cpp
class Teacher;
```

只知道有这个类。

```cpp
#include "Teacher.h"
```

知道完整定义。

---

**二十七、一个小项目结构示例**

```text
StudentManager/
    include/
        Student.h
        StudentManager.h
    src/
        Student.cpp
        StudentManager.cpp
        main.cpp
```

`Student.h`：

```cpp
#pragma once

#include <string>

class Student {
private:
    std::string name;
    int score;

public:
    Student(const std::string& name, int score);

    const std::string& getName() const;
    int getScore() const;
    void show() const;
};
```

`StudentManager.h`：

```cpp
#pragma once

#include "Student.h"
#include <vector>

class StudentManager {
private:
    std::vector<Student> students;

public:
    void addStudent(const Student& student);
    void showAll() const;
};
```

`StudentManager.cpp`：

```cpp
#include "StudentManager.h"

void StudentManager::addStudent(const Student& student) {
    students.push_back(student);
}

void StudentManager::showAll() const {
    for (const Student& s : students) {
        s.show();
    }
}
```

`main.cpp`：

```cpp
#include "StudentManager.h"

int main() {
    StudentManager manager;

    manager.addStudent(Student("张三", 90));
    manager.addStudent(Student("李四", 85));

    manager.showAll();

    return 0;
}
```

编译：

```bash
g++ src/main.cpp src/Student.cpp src/StudentManager.cpp -Iinclude -o app
```

这里：

```bash
-Iinclude
```

表示告诉编译器去 `include/` 目录找头文件。

---

**二十八、你需要掌握的重点**

这一章重点掌握：

```text
1. .h 放声明
2. .cpp 放实现
3. main.cpp 放程序入口
4. #include "xxx.h" 引入自己的头文件
5. 不要 include .cpp
6. 头文件要加 #pragma once
7. 头文件里不要 using namespace std
8. 多文件编译时要编译所有 .cpp
9. undefined reference 通常是实现没参与编译
10. namespace 用来避免命名冲突
11. 前向声明可以减少头文件依赖
```

---

**二十九、练习题**

### 练习 1：拆分 Student 类

把下面内容拆成三个文件：

```text
Student.h
Student.cpp
main.cpp
```

`Student` 包含：

```text
name
score
show()
```

---

### 练习 2：StudentManager

写：

```text
Student.h
Student.cpp
StudentManager.h
StudentManager.cpp
main.cpp
```

`StudentManager` 内部用：

```cpp
vector<Student>
```

支持：

```text
addStudent
showAll
getAverageScore
```

---

### 练习 3：命名空间

把 `Student` 和 `StudentManager` 放进：

```cpp
namespace school
```

使用时写：

```cpp
school::Student
school::StudentManager
```

---

### 练习 4：文件保存

在 `StudentManager` 中添加：

```text
saveToFile
loadFromFile
```

把你之前学过的文件操作结合进来。

---

下一步建议学习：

```text
CMake 或基本构建系统
```

因为当 `.cpp` 文件变多时，每次手写：

```bash
g++ main.cpp Student.cpp StudentManager.cpp -o app
```

会很麻烦。

CMake 可以帮你管理多文件项目、头文件路径、编译选项和可执行文件。