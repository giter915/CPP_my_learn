好，那我们做最后一次优化，把这个项目收尾成一个“完成版”。

这次优化目标：

```text
1. 支持姓名包含空格
2. 文件保存改成 CSV
3. loadFromFile 更安全
4. 菜单输入更稳
5. 项目达到可提交状态
```

---

**一、最终项目结构**

保持这个结构：

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
    data/
        students.csv
```

最终功能：

```text
添加学生
显示所有学生
按 id 查找
按 id 删除
修改成绩
按成绩排序
显示平均分
保存 CSV
读取 CSV
健壮输入
异常处理
```

---

**二、CSV 文件格式**

把文件格式改成：

```csv
id,name,age,score
1,Zhang San,18,90
2,Li Si,19,85
3,Wang Wu,20,95
```

第一行是表头。

这样姓名可以包含空格。

简单版 CSV 暂时不处理名字里带英文逗号的情况。也就是说姓名可以有空格，但不要输入：

```text
Zhang, San
```

---

**三、StudentManager.h 最终版**

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

---

**四、StudentManager.cpp 的 CSV 保存**

重点改 `saveToFile`：

```cpp
void StudentManager::saveToFile(const std::string& filename) const {
    std::ofstream file(filename);

    if (!file.is_open()) {
        throw std::runtime_error("cannot open file for writing");
    }

    file << "id,name,age,score\n";

    for (const auto& student : students) {
        file << student.getId() << ","
             << student.getName() << ","
             << student.getAge() << ","
             << student.getScore() << "\n";
    }
}
```

---

**五、StudentManager.cpp 的 CSV 读取**

需要引入：

```cpp
#include <sstream>
```

然后实现：

```cpp
void StudentManager::loadFromFile(const std::string& filename) {
    std::ifstream file(filename);

    if (!file.is_open()) {
        throw std::runtime_error("cannot open file for reading");
    }

    std::vector<Student> loadedStudents;
    std::string line;

    std::getline(file, line);

    while (std::getline(file, line)) {
        if (line.empty()) {
            continue;
        }

        std::stringstream ss(line);

        std::string idText;
        std::string name;
        std::string ageText;
        std::string scoreText;

        if (!std::getline(ss, idText, ',') ||
            !std::getline(ss, name, ',') ||
            !std::getline(ss, ageText, ',') ||
            !std::getline(ss, scoreText)) {
            throw std::runtime_error("invalid file format");
        }

        int id = std::stoi(idText);
        int age = std::stoi(ageText);
        double score = std::stod(scoreText);

        for (const auto& student : loadedStudents) {
            if (student.getId() == id) {
                throw std::runtime_error("duplicate student id in file");
            }
        }

        loadedStudents.emplace_back(id, name, age, score);
    }

    students = std::move(loadedStudents);
}
```

这个版本有一个重要改进：

```cpp
std::vector<Student> loadedStudents;
```

先读到临时 vector 里，全部成功后才赋值给 `students`。

这样如果文件中途有错误，不会破坏原来的数据。

---

**六、main.cpp 最终输入策略**

最终建议：

```text
数字：readInt / readDouble
文本：readLine
```

添加学生时：

```cpp
void addStudentUI(StudentManager& manager) {
    int id = readInt("Input id: ");
    std::string name = readLine("Input name: ");
    int age = readInt("Input age: ");
    double score = readDouble("Input score: ");

    manager.addStudent(Student(id, name, age, score));

    std::cout << "Student added.\n";
}
```

这样姓名可以输入：

```text
Zhang San
Li Si
Wang Wu
```

---

**七、最终验证流程**

项目完成前，按这个顺序手动测试：

```text
1. 添加 3 个学生
2. 显示所有学生
3. 查找存在 id
4. 查找不存在 id
5. 修改某个学生成绩
6. 按成绩排序
7. 显示平均分
8. 删除一个学生
9. 保存到 data/students.csv
10. 退出程序
11. 重新运行
12. 从 data/students.csv 读取
13. 显示所有学生，确认数据还在
```

再测试错误输入：

```text
菜单输入 abc
年龄输入 abc
成绩输入 120
id 输入重复值
读取不存在的文件
读取格式错误的 CSV
```

---

**八、最终 CMakeLists.txt**

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

构建：

```bash
cmake -S . -B build
cmake --build build
```

---

**九、项目完成标准**

这个项目做到下面这样，就可以算完成：

```text
核心功能全部可用
输入错误不会直接把程序搞坏
学生数据能保存和读取
代码分成 .h / .cpp
CMake 能正常构建
没有全局 vector
Student 负责单个学生
StudentManager 负责业务逻辑
main.cpp 负责菜单交互
```

这就是一个合格的 C++ 入门综合项目。

---

**十、你从这个项目真正学到了什么**

这个项目串起了：

```text
类和封装
STL vector
algorithm
lambda
异常处理
文件操作
字符串解析
多文件组织
CMake
输入校验
分层设计
```

这比单独刷语法题更接近真实开发。

下一步你可以把这个项目保存下来，作为你的第一个 C++ 作品。之后再做第二个项目时，就不要再做学生管理系统了，可以换成：

```text
Todo List
图书管理系统
命令行记账本
通讯录
简易日志系统
```

其中我最推荐下一个做：

```text
命令行 Todo List
```

因为它可以继续练习：

```text
任务状态
日期
文件持久化
排序筛选
简单搜索
项目结构
```