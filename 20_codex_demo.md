好，那我们现在从“继续学语法”切换到：

```text
C++ 项目实战学习
```

我建议我们以一个经典项目开始：

```text
命令行学生管理系统
```

这个项目不大，但能把你前面学过的大部分 C++ 知识串起来。

---

**一、这个项目要训练什么**

这个项目会用到：

```text
类和对象
封装
STL vector
string
algorithm
optional
异常处理
文件读写
多文件组织
CMake
代码设计
```

也就是说，它不是单独学某个知识点，而是练习“怎么把知识点组合成一个完整程序”。

---

**二、项目功能目标**

我们先做第一版，功能如下：

```text
1. 添加学生
2. 显示所有学生
3. 按姓名查找学生
4. 删除学生
5. 修改学生成绩
6. 按成绩排序
7. 计算平均分
8. 保存到文件
9. 从文件读取
10. 命令行菜单
```

后面还可以扩展：

```text
按学号管理
成绩分段统计
导出排行榜
登录系统
课程管理
教师管理
```

但第一版先不要太贪心。

---

**三、项目结构设计**

建议结构：

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
        students.txt
```

每个文件职责：

```text
Student.h / Student.cpp
定义一个学生对象

StudentManager.h / StudentManager.cpp
管理多个学生

main.cpp
负责菜单和用户交互

students.txt
保存学生数据
```

这就是一个比较标准的小型 C++ 项目结构。

---

**四、第一步：设计 Student 类**

一个学生至少需要：

```text
姓名
年龄
成绩
```

也可以加：

```text
学号 id
```

我建议加学号，因为真实管理系统一般不能只靠姓名查找。

设计：

```cpp
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

注意几个点：

```text
成员变量 private
通过 getter 获取数据
通过 setter 修改数据
构造函数保证对象初始合法
print() 负责打印自己
```

---

**五、Student 类的合法性检查**

学生对象应该始终合法。

比如：

```text
id 必须大于 0
name 不能为空
age 应该在 0 到 150
score 应该在 0 到 100
```

如果非法，就抛异常：

```cpp
throw std::invalid_argument("score must be between 0 and 100");
```

这样可以防止创建出错误对象。

---

**六、StudentManager 类负责什么**

`Student` 只代表一个学生。

`StudentManager` 负责管理一组学生。

它内部可以用：

```cpp
std::vector<Student> students;
```

设计：

```cpp
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
};
```

这里有一个关键点：

```cpp
Student* findById(int id);
```

如果找到了，返回学生地址。

如果没找到，返回：

```cpp
nullptr
```

也可以用 `std::optional`，但因为要修改学生对象，指针版本更直接。

---

**七、为什么不用全局 vector**

不推荐：

```cpp
std::vector<Student> students;
```

作为全局变量。

更推荐：

```cpp
StudentManager manager;
```

因为这样数据和操作被封装在一起。

这就是你之前学过的封装思想。

---

**八、菜单设计**

`main.cpp` 负责用户交互。

菜单可以这样：

```text
========== 学生管理系统 ==========
1. 添加学生
2. 显示所有学生
3. 查找学生
4. 删除学生
5. 修改成绩
6. 按成绩排序
7. 显示平均分
8. 保存到文件
9. 从文件读取
0. 退出
请选择：
```

主循环：

```cpp
while (true) {
    printMenu();

    int choice;
    std::cin >> choice;

    if (choice == 0) {
        break;
    }

    switch (choice) {
        case 1:
            addStudentUI(manager);
            break;
        case 2:
            manager.printAll();
            break;
        // ...
    }
}
```

这里可以把每个菜单操作拆成函数，不要把所有逻辑都塞进 `main()`。

---

**九、文件保存格式**

先用简单文本格式：

```text
id name age score
```

例如：

```text
1 ZhangSan 18 90
2 LiSi 19 85.5
3 WangWu 20 95
```

保存时：

```cpp
file << student.getId() << " "
     << student.getName() << " "
     << student.getAge() << " "
     << student.getScore() << "\n";
```

读取时：

```cpp
int id;
std::string name;
int age;
double score;

while (file >> id >> name >> age >> score) {
    students.emplace_back(id, name, age, score);
}
```

初学阶段建议姓名先不要带空格。

---

**十、第一版先不要做太复杂**

第一版先避免：

```text
中文路径问题
姓名中带空格
复杂权限系统
数据库
图形界面
网络同步
```

我们先让程序稳定跑起来。

项目学习最怕一开始功能太大，最后哪里都没做完。

---

**十一、开发顺序**

建议按这个顺序写：

```text
1. 写 Student 类
2. 测试 Student 创建和打印
3. 写 StudentManager::addStudent
4. 写 printAll
5. 写 findById
6. 写 removeById
7. 写 updateScore
8. 写 sortByScoreDescending
9. 写 averageScore
10. 写 saveToFile/loadFromFile
11. 写菜单 main.cpp
12. 最后整理 CMake
```

这个顺序很稳。

不要一开始就写完整菜单。

---

**十二、核心代码示例：Student**

```cpp
class Student {
private:
    int id;
    std::string name;
    int age;
    double score;

public:
    Student(int id, const std::string& name, int age, double score)
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

    int getId() const {
        return id;
    }

    const std::string& getName() const {
        return name;
    }

    int getAge() const {
        return age;
    }

    double getScore() const {
        return score;
    }

    void setScore(double newScore) {
        if (newScore < 0 || newScore > 100) {
            throw std::invalid_argument("score out of range");
        }

        score = newScore;
    }

    void print() const {
        std::cout << id << " " << name << " " << age << " " << score << std::endl;
    }
};
```

---

**十三、核心代码示例：添加和查找**

```cpp
void StudentManager::addStudent(const Student& student) {
    if (findById(student.getId()) != nullptr) {
        throw std::runtime_error("student id already exists");
    }

    students.push_back(student);
}
```

查找：

```cpp
Student* StudentManager::findById(int id) {
    for (auto& student : students) {
        if (student.getId() == id) {
            return &student;
        }
    }

    return nullptr;
}
```

const 版本：

```cpp
const Student* StudentManager::findById(int id) const {
    for (const auto& student : students) {
        if (student.getId() == id) {
            return &student;
        }
    }

    return nullptr;
}
```

---

**十四、核心代码示例：删除**

```cpp
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
```

这里用到了你之前学过的：

```text
lambda
remove_if
erase
```

---

**十五、核心代码示例：排序**

```cpp
void StudentManager::sortByScoreDescending() {
    std::sort(students.begin(), students.end(),
        [](const Student& a, const Student& b) {
            return a.getScore() > b.getScore();
        }
    );
}
```

---

**十六、核心代码示例：平均分**

```cpp
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
```

---

**十七、核心代码示例：保存文件**

```cpp
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
```

---

**十八、核心代码示例：读取文件**

```cpp
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
```

---

**十九、这一阶段你要重点练什么**

现在你的目标不是“背代码”，而是理解项目拆分。

你要能回答：

```text
Student 类负责什么？
StudentManager 负责什么？
main.cpp 负责什么？
为什么 students vector 不放全局？
为什么查找函数返回指针？
为什么保存文件放在 StudentManager 里？
为什么菜单逻辑不应该塞进 Student 类？
```

这些问题比单纯写出代码更重要。

---

**二十、你的下一步任务**

建议你现在开始实现第一版。

先只做这 4 个功能：

```text
1. Student 类
2. StudentManager 添加学生
3. 显示所有学生
4. 按 id 查找学生
```

先别急着写文件保存和菜单。

最小可运行版本：

```cpp
int main() {
    StudentManager manager;

    manager.addStudent(Student(1, "ZhangSan", 18, 90));
    manager.addStudent(Student(2, "LiSi", 19, 85));

    manager.printAll();

    Student* student = manager.findById(1);
    if (student != nullptr) {
        student->print();
    }

    return 0;
}
```

等这个跑通，再继续扩展删除、修改、排序、保存文件。

---

下一节我们可以继续学这个项目的第二阶段：

```text
把 Student 和 StudentManager 拆成 .h / .cpp
并用 CMake 构建
```

这一步会让你真正从“写练习代码”进入“写项目代码”。