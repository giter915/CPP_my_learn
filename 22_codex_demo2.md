好，我们继续第三阶段：

```text
把测试版 main.cpp 改成真正的命令行菜单程序
```

这一阶段的目标是：让学生管理系统可以像一个小软件一样被用户操作，而不是只在 `main()` 里写死测试数据。

---

**一、本阶段目标**

我们要实现这个菜单：

```text
========== Student Manager ==========
1. Add student
2. Show all students
3. Find student by id
4. Remove student by id
5. Update score
6. Sort by score
7. Show average score
8. Save to file
9. Load from file
0. Exit
Choose:
```

用户输入数字，程序执行对应功能。

---

**二、main.cpp 的职责**

`main.cpp` 现在负责：

```text
显示菜单
读取用户输入
调用 StudentManager
处理异常
控制程序循环
```

但它不应该负责：

```text
直接管理 vector
直接修改 Student 内部成员
直接实现排序逻辑
直接实现文件读写细节
```

这些仍然交给 `StudentManager`。

---

**三、主循环结构**

核心结构是：

```cpp
int main() {
    StudentManager manager;

    while (true) {
        printMenu();

        int choice;
        std::cin >> choice;

        if (choice == 0) {
            break;
        }

        try {
            handleChoice(choice, manager);
        } catch (const std::exception& e) {
            std::cout << "Error: " << e.what() << std::endl;
        }
    }

    return 0;
}
```

我们会把操作拆成多个函数，让 `main()` 保持干净。

---

**四、菜单函数**

```cpp
void printMenu() {
    std::cout << "\n========== Student Manager ==========\n";
    std::cout << "1. Add student\n";
    std::cout << "2. Show all students\n";
    std::cout << "3. Find student by id\n";
    std::cout << "4. Remove student by id\n";
    std::cout << "5. Update score\n";
    std::cout << "6. Sort by score\n";
    std::cout << "7. Show average score\n";
    std::cout << "8. Save to file\n";
    std::cout << "9. Load from file\n";
    std::cout << "0. Exit\n";
    std::cout << "Choose: ";
}
```

这个函数只负责显示，不处理逻辑。

---

**五、添加学生函数**

```cpp
void addStudentUI(StudentManager& manager) {
    int id;
    std::string name;
    int age;
    double score;

    std::cout << "Input id: ";
    std::cin >> id;

    std::cout << "Input name: ";
    std::cin >> name;

    std::cout << "Input age: ";
    std::cin >> age;

    std::cout << "Input score: ";
    std::cin >> score;

    manager.addStudent(Student(id, name, age, score));

    std::cout << "Student added.\n";
}
```

注意：

```cpp
manager.addStudent(...)
```

可能抛异常，比如：

```text
id 已存在
成绩不合法
姓名为空
```

异常由外层统一捕获。

---

**六、查找学生函数**

```cpp
void findStudentUI(const StudentManager& manager) {
    int id;

    std::cout << "Input id: ";
    std::cin >> id;

    const Student* student = manager.findById(id);

    if (student == nullptr) {
        std::cout << "Student not found.\n";
        return;
    }

    student->print();
}
```

这里用 `const StudentManager&`，因为查找不修改 manager。

---

**七、删除学生函数**

```cpp
void removeStudentUI(StudentManager& manager) {
    int id;

    std::cout << "Input id: ";
    std::cin >> id;

    if (manager.removeById(id)) {
        std::cout << "Student removed.\n";
    } else {
        std::cout << "Student not found.\n";
    }
}
```

`removeById` 返回 `bool`，表示是否删除成功。

---

**八、修改成绩函数**

```cpp
void updateScoreUI(StudentManager& manager) {
    int id;
    double score;

    std::cout << "Input id: ";
    std::cin >> id;

    std::cout << "Input new score: ";
    std::cin >> score;

    manager.updateScore(id, score);

    std::cout << "Score updated.\n";
}
```

如果学生不存在，`updateScore` 会抛异常。

---

**九、保存文件函数**

```cpp
void saveToFileUI(const StudentManager& manager) {
    std::string filename;

    std::cout << "Input filename: ";
    std::cin >> filename;

    manager.saveToFile(filename);

    std::cout << "Saved.\n";
}
```

比如输入：

```text
students.txt
```

---

**十、读取文件函数**

```cpp
void loadFromFileUI(StudentManager& manager) {
    std::string filename;

    std::cout << "Input filename: ";
    std::cin >> filename;

    manager.loadFromFile(filename);

    std::cout << "Loaded.\n";
}
```

---

**十一、处理菜单选择**

```cpp
void handleChoice(int choice, StudentManager& manager) {
    switch (choice) {
        case 1:
            addStudentUI(manager);
            break;
        case 2:
            manager.printAll();
            break;
        case 3:
            findStudentUI(manager);
            break;
        case 4:
            removeStudentUI(manager);
            break;
        case 5:
            updateScoreUI(manager);
            break;
        case 6:
            manager.sortByScoreDescending();
            std::cout << "Sorted.\n";
            break;
        case 7:
            std::cout << "Average score: "
                      << manager.averageScore()
                      << "\n";
            break;
        case 8:
            saveToFileUI(manager);
            break;
        case 9:
            loadFromFileUI(manager);
            break;
        default:
            std::cout << "Invalid choice.\n";
            break;
    }
}
```

---

**十二、完整 main.cpp**

你可以把 `src/main.cpp` 改成：

```cpp
#include "StudentManager.h"

#include <exception>
#include <iostream>
#include <string>

void printMenu() {
    std::cout << "\n========== Student Manager ==========\n";
    std::cout << "1. Add student\n";
    std::cout << "2. Show all students\n";
    std::cout << "3. Find student by id\n";
    std::cout << "4. Remove student by id\n";
    std::cout << "5. Update score\n";
    std::cout << "6. Sort by score\n";
    std::cout << "7. Show average score\n";
    std::cout << "8. Save to file\n";
    std::cout << "9. Load from file\n";
    std::cout << "0. Exit\n";
    std::cout << "Choose: ";
}

void addStudentUI(StudentManager& manager) {
    int id;
    std::string name;
    int age;
    double score;

    std::cout << "Input id: ";
    std::cin >> id;

    std::cout << "Input name: ";
    std::cin >> name;

    std::cout << "Input age: ";
    std::cin >> age;

    std::cout << "Input score: ";
    std::cin >> score;

    manager.addStudent(Student(id, name, age, score));

    std::cout << "Student added.\n";
}

void findStudentUI(const StudentManager& manager) {
    int id;

    std::cout << "Input id: ";
    std::cin >> id;

    const Student* student = manager.findById(id);

    if (student == nullptr) {
        std::cout << "Student not found.\n";
        return;
    }

    student->print();
}

void removeStudentUI(StudentManager& manager) {
    int id;

    std::cout << "Input id: ";
    std::cin >> id;

    if (manager.removeById(id)) {
        std::cout << "Student removed.\n";
    } else {
        std::cout << "Student not found.\n";
    }
}

void updateScoreUI(StudentManager& manager) {
    int id;
    double score;

    std::cout << "Input id: ";
    std::cin >> id;

    std::cout << "Input new score: ";
    std::cin >> score;

    manager.updateScore(id, score);

    std::cout << "Score updated.\n";
}

void saveToFileUI(const StudentManager& manager) {
    std::string filename;

    std::cout << "Input filename: ";
    std::cin >> filename;

    manager.saveToFile(filename);

    std::cout << "Saved.\n";
}

void loadFromFileUI(StudentManager& manager) {
    std::string filename;

    std::cout << "Input filename: ";
    std::cin >> filename;

    manager.loadFromFile(filename);

    std::cout << "Loaded.\n";
}

void handleChoice(int choice, StudentManager& manager) {
    switch (choice) {
        case 1:
            addStudentUI(manager);
            break;
        case 2:
            manager.printAll();
            break;
        case 3:
            findStudentUI(manager);
            break;
        case 4:
            removeStudentUI(manager);
            break;
        case 5:
            updateScoreUI(manager);
            break;
        case 6:
            manager.sortByScoreDescending();
            std::cout << "Sorted.\n";
            break;
        case 7:
            std::cout << "Average score: "
                      << manager.averageScore()
                      << "\n";
            break;
        case 8:
            saveToFileUI(manager);
            break;
        case 9:
            loadFromFileUI(manager);
            break;
        default:
            std::cout << "Invalid choice.\n";
            break;
    }
}

int main() {
    StudentManager manager;

    while (true) {
        printMenu();

        int choice;
        std::cin >> choice;

        if (choice == 0) {
            std::cout << "Goodbye.\n";
            break;
        }

        try {
            handleChoice(choice, manager);
        } catch (const std::exception& e) {
            std::cout << "Error: " << e.what() << "\n";
        }
    }

    return 0;
}
```

---

**十三、现在的程序有什么不足**

这一版已经能用，但还有几个问题：

```text
如果用户输入字母，std::cin 会进入失败状态
姓名不能带空格
文件读取时没有处理重复 id
菜单函数都放在 main.cpp，后面可能变长
退出前没有自动保存
```

这很正常。项目开发通常就是：

```text
先做能跑版本
再逐步增强健壮性
再重构结构
```

---

**十四、下一阶段优化方向**

下一阶段可以做：

```text
1. 输入校验
2. 防止 cin 输入失败
3. 姓名支持空格
4. loadFromFile 检查重复 id
5. 添加自动保存
6. 把菜单 UI 拆成单独类
7. 添加简单测试
```

最推荐下一步学：

```text
输入校验与健壮性处理
```

因为命令行程序最容易被错误输入搞崩。

---

**十五、你现在要练习的点**

你现在重点理解：

```text
main.cpp 只负责用户交互
StudentManager 负责业务逻辑
Student 负责单个学生数据
异常在 UI 层捕获并显示
每个菜单操作拆成独立函数
```

这就是小项目分层设计的雏形：

```text
数据层：Student
业务层：StudentManager
交互层：main.cpp 菜单函数
```

下一节我们继续优化这个项目：

```text
让输入更健壮，处理用户输错内容的情况
```