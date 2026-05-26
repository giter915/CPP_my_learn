好，我们继续第四阶段：

```text
输入校验与健壮性处理
```

前面版本已经能跑，但有一个明显问题：如果用户应该输入数字，却输入了字母，程序可能会进入错误状态。

比如菜单这里：

```cpp
int choice;
std::cin >> choice;
```

如果用户输入：

```text
abc
```

`std::cin` 会失败，后续输入也可能都读不进去。

这一节我们专门解决这个问题。

---

**一、为什么 cin 会出问题**

比如：

```cpp
int age;
std::cin >> age;
```

如果用户输入：

```text
18
```

没问题。

如果用户输入：

```text
abc
```

读取失败，`cin` 会进入失败状态。

这时：

```cpp
std::cin.fail()
```

会返回 `true`。

如果不清理这个状态，后面继续：

```cpp
std::cin >> score;
```

也会失败。

---

**二、处理 cin 失败的基本步骤**

当输入失败时，需要做两件事：

```cpp
std::cin.clear();
std::cin.ignore(...);
```

含义：

```cpp
std::cin.clear();
```

清除错误状态。

```cpp
std::cin.ignore(...);
```

丢弃输入缓冲区里的错误内容。

常用写法：

```cpp
std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
```

需要头文件：

```cpp
#include <limits>
```

---

**三、封装一个读取 int 的函数**

不要每次都写重复代码。

我们封装：

```cpp
int readInt(const std::string& prompt)
```

实现：

```cpp
int readInt(const std::string& prompt) {
    int value;

    while (true) {
        std::cout << prompt;

        if (std::cin >> value) {
            std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
            return value;
        }

        std::cout << "Invalid input. Please enter an integer.\n";

        std::cin.clear();
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
    }
}
```

这样用户输错会重新提示。

---

**四、封装读取 double**

```cpp
double readDouble(const std::string& prompt) {
    double value;

    while (true) {
        std::cout << prompt;

        if (std::cin >> value) {
            std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
            return value;
        }

        std::cout << "Invalid input. Please enter a number.\n";

        std::cin.clear();
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
    }
}
```

---

**五、封装读取 string**

如果你想支持姓名带空格，比如：

```text
Zhang San
```

就不能用：

```cpp
std::cin >> name;
```

因为它只读到空格前。

应该用：

```cpp
std::getline(std::cin, name);
```

封装：

```cpp
std::string readLine(const std::string& prompt) {
    std::string value;

    while (true) {
        std::cout << prompt;
        std::getline(std::cin, value);

        if (!value.empty()) {
            return value;
        }

        std::cout << "Input cannot be empty.\n";
    }
}
```

---

**六、为什么 readInt 后要 ignore**

如果你先读数字：

```cpp
std::cin >> age;
```

用户输入：

```text
18回车
```

数字 `18` 被读取了，但换行符 `\n` 还留在缓冲区。

如果后面马上调用：

```cpp
getline(std::cin, name);
```

它可能会直接读到这个空行。

所以在 `readInt` 和 `readDouble` 成功后也写：

```cpp
std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
```

把这一行剩余内容清掉。

---

**七、把输入函数放哪里**

现在可以先放在 `main.cpp` 顶部。

后面项目变大，可以拆成：

```text
include/InputUtils.h
src/InputUtils.cpp
```

但当前阶段先放在 `main.cpp`，方便学习。

---

**八、改造 addStudentUI**

原来：

```cpp
std::cin >> id;
std::cin >> name;
std::cin >> age;
std::cin >> score;
```

改成：

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

这样输入更稳。

---

**九、改造其他 UI 函数**

查找：

```cpp
void findStudentUI(const StudentManager& manager) {
    int id = readInt("Input id: ");

    const Student* student = manager.findById(id);

    if (student == nullptr) {
        std::cout << "Student not found.\n";
        return;
    }

    student->print();
}
```

删除：

```cpp
void removeStudentUI(StudentManager& manager) {
    int id = readInt("Input id: ");

    if (manager.removeById(id)) {
        std::cout << "Student removed.\n";
    } else {
        std::cout << "Student not found.\n";
    }
}
```

修改成绩：

```cpp
void updateScoreUI(StudentManager& manager) {
    int id = readInt("Input id: ");
    double score = readDouble("Input new score: ");

    manager.updateScore(id, score);

    std::cout << "Score updated.\n";
}
```

保存：

```cpp
void saveToFileUI(const StudentManager& manager) {
    std::string filename = readLine("Input filename: ");

    manager.saveToFile(filename);

    std::cout << "Saved.\n";
}
```

读取：

```cpp
void loadFromFileUI(StudentManager& manager) {
    std::string filename = readLine("Input filename: ");

    manager.loadFromFile(filename);

    std::cout << "Loaded.\n";
}
```

---

**十、改造 main 读取菜单**

原来：

```cpp
int choice;
std::cin >> choice;
```

改成：

```cpp
int choice = readInt("Choose: ");
```

所以 `printMenu()` 最后一行不要再输出 `"Choose: "`，或者保留也可以，但建议让 `readInt` 负责提示。

改成：

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
}
```

主循环：

```cpp
while (true) {
    printMenu();

    int choice = readInt("Choose: ");

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
```

---

**十一、完整 main.cpp 改造版**

关键是多 include 一个：

```cpp
#include <limits>
```

完整结构：

```cpp
#include "StudentManager.h"

#include <exception>
#include <iostream>
#include <limits>
#include <string>

int readInt(const std::string& prompt) {
    int value;

    while (true) {
        std::cout << prompt;

        if (std::cin >> value) {
            std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
            return value;
        }

        std::cout << "Invalid input. Please enter an integer.\n";

        std::cin.clear();
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
    }
}

double readDouble(const std::string& prompt) {
    double value;

    while (true) {
        std::cout << prompt;

        if (std::cin >> value) {
            std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
            return value;
        }

        std::cout << "Invalid input. Please enter a number.\n";

        std::cin.clear();
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
    }
}

std::string readLine(const std::string& prompt) {
    std::string value;

    while (true) {
        std::cout << prompt;
        std::getline(std::cin, value);

        if (!value.empty()) {
            return value;
        }

        std::cout << "Input cannot be empty.\n";
    }
}

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
}
```

后面的 UI 函数按前面改即可。

---

**十二、现在还有一个新问题**

如果姓名支持空格：

```text
Zhang San
```

保存文件时：

```text
1 Zhang San 18 90
```

读取时：

```cpp
file >> id >> name >> age >> score
```

会把：

```text
Zhang
```

当成 name，把：

```text
San
```

当成年龄，读取失败。

所以如果你支持姓名带空格，就不能再用空格分隔保存。

解决方式有几种：

```text
1. 第一版仍然规定姓名不能带空格
2. 使用 CSV 格式
3. 每个字段单独一行
4. 使用 JSON
```

为了学习简单，建议下一阶段改成 CSV：

```text
id,name,age,score
1,Zhang San,18,90
2,Li Si,19,85
```

这样姓名可以带空格。

---

**十三、临时选择**

目前你有两个选择：

简单版：

```text
name 仍然不允许空格
readLine 可以暂时不用在 name 上
```

增强版：

```text
支持姓名空格
下一步把文件保存改成 CSV
```

我建议走增强版，因为它能继续训练：

```text
字符串处理
文件格式设计
数据解析
健壮性
```

---

**十四、你现在要掌握的点**

这一阶段重点掌握：

```text
1. std::cin 失败后要 clear
2. ignore 用来清理输入缓冲区
3. getline 可以读取整行
4. 数字输入后接 getline 要处理换行
5. 输入逻辑应该封装成函数
6. UI 层负责输入校验
7. 业务层负责业务规则校验
```

这点很重要：

```text
输入是否合法，先由 UI 层保证格式。
数据是否合法，再由 Student / StudentManager 保证规则。
```

例如：

```text
abc 不是数字：UI 层处理
score = 120：Student 类处理
id 重复：StudentManager 处理
```

这就是分层思维。

---

下一节我们继续优化：

```text
把文件保存格式从空格分隔改成 CSV
让姓名可以包含空格
```