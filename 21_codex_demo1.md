好，我们继续做这个项目的第二阶段：

```text
把 Student 和 StudentManager 拆成 .h / .cpp
并用 CMake 构建
```

这一节的目标是让你真正掌握 C++ 项目组织。

---

**一、当前阶段目标**

我们要把学生管理系统整理成这个结构：

```text
StudentManager/
    CMakeLists.txt
    include/
        Student.h
        StudentManager.h
    src/
        Student.cpp
        StudentManager.cpp
        main.cpp
```

职责：

```text
Student.h
声明 Student 类

Student.cpp
实现 Student 类

StudentManager.h
声明 StudentManager 类

StudentManager.cpp
实现 StudentManager 类

main.cpp
程序入口，测试功能

CMakeLists.txt
告诉 CMake 怎么编译项目
```

---

**二、Student.h**

路径：

```text
include/Student.h
```

内容：

```cpp
#pragma once

#include <string>

class Student {
private:
    int id;
    std::string name;
    int age;
    double score;

public:
    Student(int id, const std::string& name, int age, double score);

    int getId() const;
    const std::string& getName() const;
    int getAge() const;
    double getScore() const;

    void setName(const std::string& newName);
    void setAge(int newAge);
    void setScore(double newScore);

    void print() const;
};
```

注意：

```cpp
#pragma once
```

防止头文件重复包含。

头文件里不要写：

```cpp
using namespace std;
```

所以我们写：

```cpp
std::string
```

---

**三、Student.cpp**

路径：

```text
src/Student.cpp
```

内容：

```cpp
#include "Student.h"

#include <iostream>
#include <stdexcept>

Student::Student(int id, const std::string& name, int age, double score)
    : id(id), name(name), age(age), score(score) {
    if (id <= 0) {
        throw std::invalid_argument("id must be positive");
    }

    if (name.empty()) {
        throw std::invalid_argument("name cannot be empty");
    }

    if (age < 0 || age > 150) {
        throw std::invalid_argument("age out of range");
    }

    if (score < 0 || score > 100) {
        throw std::invalid_argument("score out of range");
    }
}

int Student::getId() const {
    return id;
}

const std::string& Student::getName() const {
    return name;
}

int Student::getAge() const {
    return age;
}

double Student::getScore() const {
    return score;
}

void Student::setName(const std::string& newName) {
    if (newName.empty()) {
        throw std::invalid_argument("name cannot be empty");
    }

    name = newName;
}

void Student::setAge(int newAge) {
    if (newAge < 0 || newAge > 150) {
        throw std::invalid_argument("age out of range");
    }

    age = newAge;
}

void Student::setScore(double newScore) {
    if (newScore < 0 || newScore > 100) {
        throw std::invalid_argument("score out of range");
    }

    score = newScore;
}

void Student::print() const {
    std::cout << "ID: " << id
              << ", Name: " << name
              << ", Age: " << age
              << ", Score: " << score
              << std::endl;
}
```

---

**四、StudentManager.h**

路径：

```text
include/StudentManager.h
```

内容：

```cpp
#pragma once

#include "Student.h"

#include <string>
#include <vector>

class StudentManager {
private:
    std::vector<Student> students;

public:
    void addStudent(const Student& student);
    bool removeById(int id);

    Student* findById(int id);
    const Student* findById(int id) const;

    void updateScore(int id, double newScore);
    void printAll() const;

    double averageScore() const;
    void sortByScoreDescending();

    void saveToFile(const std::string& filename) const;
    void loadFromFile(const std::string& filename);

    int getStudentCount() const;
};
```

这里声明了完整功能，但我们可以先实现一部分，再逐步补全。

---

**五、StudentManager.cpp**

路径：

```text
src/StudentManager.cpp
```

内容：

```cpp
#include "StudentManager.h"

#include <algorithm>
#include <fstream>
#include <iostream>
#include <stdexcept>

void StudentManager::addStudent(const Student& student) {
    if (findById(student.getId()) != nullptr) {
        throw std::runtime_error("student id already exists");
    }

    students.push_back(student);
}

bool StudentManager::removeById(int id) {
    auto it = std::remove_if(students.begin(), students.end(),
        [id](const Student& student) {
            return student.getId() == id;
        }
    );

    if (it == students.end()) {
        return false;
    }

    students.erase(it, students.end());
    return true;
}

Student* StudentManager::findById(int id) {
    for (auto& student : students) {
        if (student.getId() == id) {
            return &student;
        }
    }

    return nullptr;
}

const Student* StudentManager::findById(int id) const {
    for (const auto& student : students) {
        if (student.getId() == id) {
            return &student;
        }
    }

    return nullptr;
}

void StudentManager::updateScore(int id, double newScore) {
    Student* student = findById(id);

    if (student == nullptr) {
        throw std::runtime_error("student not found");
    }

    student->setScore(newScore);
}

void StudentManager::printAll() const {
    if (students.empty()) {
        std::cout << "No students." << std::endl;
        return;
    }

    for (const auto& student : students) {
        student.print();
    }
}

double StudentManager::averageScore() const {
    if (students.empty()) {
        throw std::runtime_error("no students");
    }

    double sum = 0;

    for (const auto& student : students) {
        sum += student.getScore();
    }

    return sum / students.size();
}

void StudentManager::sortByScoreDescending() {
    std::sort(students.begin(), students.end(),
        [](const Student& a, const Student& b) {
            return a.getScore() > b.getScore();
        }
    );
}

void StudentManager::saveToFile(const std::string& filename) const {
    std::ofstream file(filename);

    if (!file.is_open()) {
        throw std::runtime_error("cannot open file for writing");
    }

    for (const auto& student : students) {
        file << student.getId() << " "
             << student.getName() << " "
             << student.getAge() << " "
             << student.getScore() << "\n";
    }
}

void StudentManager::loadFromFile(const std::string& filename) {
    std::ifstream file(filename);

    if (!file.is_open()) {
        throw std::runtime_error("cannot open file for reading");
    }

    students.clear();

    int id;
    std::string name;
    int age;
    double score;

    while (file >> id >> name >> age >> score) {
        students.emplace_back(id, name, age, score);
    }
}

int StudentManager::getStudentCount() const {
    return static_cast<int>(students.size());
}
```

---

**六、main.cpp 第一版**

路径：

```text
src/main.cpp
```

先写一个测试版，不急着做菜单：

```cpp
#include "StudentManager.h"

#include <iostream>
#include <exception>

int main() {
    try {
        StudentManager manager;

        manager.addStudent(Student(1, "ZhangSan", 18, 90));
        manager.addStudent(Student(2, "LiSi", 19, 85));
        manager.addStudent(Student(3, "WangWu", 20, 95));

        std::cout << "All students:" << std::endl;
        manager.printAll();

        std::cout << "\nAverage score: "
                  << manager.averageScore()
                  << std::endl;

        std::cout << "\nSorted by score:" << std::endl;
        manager.sortByScoreDescending();
        manager.printAll();

        std::cout << "\nUpdate score:" << std::endl;
        manager.updateScore(2, 99);
        manager.printAll();

        std::cout << "\nFind student id 1:" << std::endl;
        Student* student = manager.findById(1);
        if (student != nullptr) {
            student->print();
        }

        std::cout << "\nSave to file." << std::endl;
        manager.saveToFile("students.txt");

        StudentManager loadedManager;
        loadedManager.loadFromFile("students.txt");

        std::cout << "\nLoaded students:" << std::endl;
        loadedManager.printAll();
    } catch (const std::exception& e) {
        std::cout << "Error: " << e.what() << std::endl;
    }

    return 0;
}
```

这版会测试：

```text
添加
显示
平均分
排序
修改
查找
保存
读取
异常捕获
```

---

**七、CMakeLists.txt**

路径：

```text
CMakeLists.txt
```

内容：

```cmake
cmake_minimum_required(VERSION 3.10)

project(StudentManager)

add_library(student_manager_lib
    src/Student.cpp
    src/StudentManager.cpp
)

target_include_directories(student_manager_lib PUBLIC include)

target_compile_features(student_manager_lib PUBLIC cxx_std_17)

add_executable(student_manager_app
    src/main.cpp
)

target_link_libraries(student_manager_app PRIVATE student_manager_lib)
```

这个 CMake 设计比较好：

```text
Student 和 StudentManager 做成库
main.cpp 做成可执行程序
可执行程序链接这个库
```

后面如果你要写测试，也可以复用这个库。

---

**八、构建和运行**

在项目根目录执行：

```bash
cmake -S . -B build
cmake --build build
```

生成的可执行文件位置和系统有关。

Linux/macOS 通常可能在：

```text
build/student_manager_app
```

Windows Visual Studio 生成器可能在：

```text
build/Debug/student_manager_app.exe
```

或：

```text
build/Release/student_manager_app.exe
```

---

**九、这一版可能遇到的问题**

### 1. 找不到 Student.h

错误类似：

```text
Student.h: No such file or directory
```

检查 CMake 是否有：

```cmake
target_include_directories(student_manager_lib PUBLIC include)
```

---

### 2. undefined reference

如果报：

```text
undefined reference to Student::...
```

检查是否把 `.cpp` 加进了库：

```cmake
add_library(student_manager_lib
    src/Student.cpp
    src/StudentManager.cpp
)
```

---

### 3. C++ 标准不够

检查：

```cmake
target_compile_features(student_manager_lib PUBLIC cxx_std_17)
```

---

### 4. students.txt 位置不确定

程序运行时生成的：

```text
students.txt
```

会出现在运行目录中。

如果从 build 目录运行，可能出现在：

```text
build/students.txt
```

这是正常的。

---

**十、为什么先写测试版 main**

你可能想马上做菜单，但我建议先写测试版 `main.cpp`。

原因：

```text
菜单会增加输入处理复杂度
测试版 main 更容易验证核心功能
核心类稳定后，再做菜单更轻松
```

开发项目时，先把核心逻辑跑通，再做交互层，这是很好的习惯。

---

**十一、下一阶段做什么**

当这一版能正常运行后，下一步做：

```text
命令行菜单 UI
```

也就是把 main.cpp 改成：

```text
循环显示菜单
读取用户选择
调用 StudentManager 对应函数
处理输入错误
捕获异常
```

这会训练：

```text
输入处理
switch
函数拆分
异常处理
用户交互设计
```

---

**十二、你现在的任务**

你现在可以照这个结构创建文件：

```text
include/Student.h
src/Student.cpp
include/StudentManager.h
src/StudentManager.cpp
src/main.cpp
CMakeLists.txt
```

然后构建运行。

如果能看到类似输出：

```text
All students:
ID: 1, Name: ZhangSan, Age: 18, Score: 90
ID: 2, Name: LiSi, Age: 19, Score: 85
ID: 3, Name: WangWu, Age: 20, Score: 95

Average score: 90

Sorted by score:
ID: 3, Name: WangWu, Age: 20, Score: 95
...
```

说明第二阶段成功。

下一节我们继续做第三阶段：

```text
把测试版 main.cpp 改成真正的命令行菜单程序
```