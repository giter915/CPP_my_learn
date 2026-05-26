现在继续学习：

```text
C++ 文件操作
```

也就是：

```cpp
ifstream
ofstream
fstream
```

文件操作很实用。学会之后，你的程序就可以：

```text
读取配置
保存数据
读取文本
写日志
处理文件
保存学生信息
```

---

**一、文件操作需要的头文件**

C++ 文件操作主要使用：

```cpp
#include <fstream>
```

常用三个类：

```cpp
ifstream   // input file stream，读文件
ofstream   // output file stream，写文件
fstream    // file stream，既能读也能写
```

记法：

```text
i = input  = 输入 = 读
o = output = 输出 = 写
```

---

**二、写文本文件 ofstream**

最简单写文件：

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main() {
    ofstream file("data.txt");

    file << "Hello C++" << endl;
    file << "文件写入测试" << endl;

    file.close();

    return 0;
}
```

运行后，会生成：

```text
data.txt
```

内容：

```text
Hello C++
文件写入测试
```

---

**三、打开文件是否成功**

写文件时应该检查是否打开成功：

```cpp
ofstream file("data.txt");

if (!file.is_open()) {
    cout << "文件打开失败" << endl;
    return 1;
}
```

完整：

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main() {
    ofstream file("data.txt");

    if (!file.is_open()) {
        cout << "文件打开失败" << endl;
        return 1;
    }

    file << "Hello" << endl;
    file.close();

    return 0;
}
```

---

**四、ofstream 默认会覆盖文件**

如果文件原来有内容：

```text
old content
```

你这样写：

```cpp
ofstream file("data.txt");
file << "new content";
```

文件内容会变成：

```text
new content
```

原来的内容被覆盖。

---

**五、追加写入**

如果你想在文件末尾追加内容，用：

```cpp
ios::app
```

例子：

```cpp
ofstream file("log.txt", ios::app);

file << "新的日志" << endl;
```

完整：

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main() {
    ofstream file("log.txt", ios::app);

    if (!file.is_open()) {
        cout << "文件打开失败" << endl;
        return 1;
    }

    file << "程序启动" << endl;

    file.close();

    return 0;
}
```

---

**六、读文本文件 ifstream**

读文件用：

```cpp
ifstream
```

例子：

```cpp
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

int main() {
    ifstream file("data.txt");

    if (!file.is_open()) {
        cout << "文件打开失败" << endl;
        return 1;
    }

    string line;

    while (getline(file, line)) {
        cout << line << endl;
    }

    file.close();

    return 0;
}
```

这里：

```cpp
getline(file, line)
```

表示从文件读取一整行。

---

**七、按单词读取**

如果文件内容是：

```text
hello cpp world
```

可以这样读取：

```cpp
string word;

while (file >> word) {
    cout << word << endl;
}
```

完整：

```cpp
ifstream file("data.txt");

string word;

while (file >> word) {
    cout << word << endl;
}
```

它会按空格、换行分隔。

---

**八、按数字读取**

文件内容：

```text
10 20 30 40
```

读取：

```cpp
ifstream file("nums.txt");

int x;

while (file >> x) {
    cout << x << endl;
}
```

---

**九、读取学生信息**

假设文件 `students.txt` 内容是：

```text
张三 90
李四 85
王五 95
```

读取：

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <vector>
using namespace std;

class Student {
public:
    string name;
    int score;

    Student(string n, int s) : name(n), score(s) {
    }
};

int main() {
    ifstream file("students.txt");

    if (!file.is_open()) {
        cout << "文件打开失败" << endl;
        return 1;
    }

    vector<Student> students;

    string name;
    int score;

    while (file >> name >> score) {
        students.push_back(Student(name, score));
    }

    for (const Student& s : students) {
        cout << s.name << " " << s.score << endl;
    }

    file.close();

    return 0;
}
```

---

**十、保存学生信息**

```cpp
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
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
        {"李四", 85},
        {"王五", 95}
    };

    ofstream file("students.txt");

    if (!file.is_open()) {
        cout << "文件打开失败" << endl;
        return 1;
    }

    for (const Student& s : students) {
        file << s.name << " " << s.score << endl;
    }

    file.close();

    return 0;
}
```

---

**十一、fstream 读写文件**

`fstream` 可以同时读写。

```cpp
fstream file("data.txt", ios::in | ios::out);
```

不过初学阶段，建议：

```text
只读用 ifstream
只写用 ofstream
读写都需要再用 fstream
```

这样更清楚。

---

**十二、常见打开模式**

```cpp
ios::in       // 读
ios::out      // 写
ios::app      // 追加
ios::trunc    // 清空原内容
ios::binary   // 二进制模式
ios::ate      // 打开后定位到文件末尾
```

组合使用：

```cpp
ofstream file("data.txt", ios::out | ios::app);
```

---

**十三、文件会自动关闭吗**

如果文件流对象离开作用域，会自动关闭文件。

```cpp
{
    ofstream file("data.txt");
    file << "hello";
} // 这里自动 close
```

所以很多时候可以不手动写：

```cpp
file.close();
```

但初学时写上也没问题。

这也是 RAII：

```text
文件流对象创建时打开文件
文件流对象析构时关闭文件
```

---

**十四、用异常处理文件错误**

可以结合异常：

```cpp
#include <iostream>
#include <fstream>
#include <stdexcept>
using namespace std;

void saveText(const string& filename, const string& text) {
    ofstream file(filename);

    if (!file.is_open()) {
        throw runtime_error("文件打开失败：" + filename);
    }

    file << text;
}

int main() {
    try {
        saveText("data.txt", "Hello");
    } catch (const exception& e) {
        cout << e.what() << endl;
    }

    return 0;
}
```

---

**十五、二进制文件写入**

文本文件适合人看，二进制文件适合程序保存原始数据。

写二进制文件：

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main() {
    int x = 12345;

    ofstream file("data.bin", ios::binary);

    file.write(reinterpret_cast<const char*>(&x), sizeof(x));

    file.close();

    return 0;
}
```

这里：

```cpp
reinterpret_cast<const char*>(&x)
```

把 `int*` 转成 `char*`，因为 `write` 按字节写入。

---

**十六、二进制文件读取**

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main() {
    int x;

    ifstream file("data.bin", ios::binary);

    file.read(reinterpret_cast<char*>(&x), sizeof(x));

    cout << x << endl;

    file.close();

    return 0;
}
```

---

**十七、保存结构体到二进制文件**

如果结构体很简单，比如：

```cpp
struct Student {
    char name[20];
    int score;
};
```

可以直接写：

```cpp
Student s = {"ZhangSan", 90};

ofstream file("student.bin", ios::binary);

file.write(reinterpret_cast<const char*>(&s), sizeof(s));
```

读取：

```cpp
Student s;

ifstream file("student.bin", ios::binary);

file.read(reinterpret_cast<char*>(&s), sizeof(s));
```

但是注意：不要直接二进制保存包含 `string`、`vector`、指针的对象。

比如：

```cpp
struct Student {
    string name;
    int score;
};
```

不适合直接 `write` 整个对象，因为 `string` 内部有复杂结构和动态内存。

---

**十八、文件位置 seekg / seekp**

读文件位置：

```cpp
seekg
```

写文件位置：

```cpp
seekp
```

查看当前位置：

```cpp
tellg
tellp
```

例子：获取文件大小。

```cpp
ifstream file("data.txt", ios::binary | ios::ate);

streamsize size = file.tellg();

cout << "文件大小：" << size << endl;
```

`ios::ate` 表示打开后定位到文件末尾。

---

**十九、完整案例：学生成绩保存和读取**

```cpp
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
using namespace std;

class Student {
public:
    string name;
    int score;

    Student(string n, int s) : name(n), score(s) {
    }
};

void saveStudents(const vector<Student>& students, const string& filename) {
    ofstream file(filename);

    if (!file.is_open()) {
        throw runtime_error("无法打开文件：" + filename);
    }

    for (const Student& s : students) {
        file << s.name << " " << s.score << endl;
    }
}

vector<Student> loadStudents(const string& filename) {
    ifstream file(filename);

    if (!file.is_open()) {
        throw runtime_error("无法打开文件：" + filename);
    }

    vector<Student> students;
    string name;
    int score;

    while (file >> name >> score) {
        students.push_back(Student(name, score));
    }

    return students;
}

int main() {
    try {
        vector<Student> students = {
            {"张三", 90},
            {"李四", 85},
            {"王五", 95}
        };

        saveStudents(students, "students.txt");

        vector<Student> loaded = loadStudents("students.txt");

        for (const Student& s : loaded) {
            cout << s.name << " " << s.score << endl;
        }
    } catch (const exception& e) {
        cout << "错误：" << e.what() << endl;
    }

    return 0;
}
```

这个例子结合了：

```text
vector
class
文件读写
异常处理
const 引用
返回对象
```

---

**二十、文件操作常见错误**

### 1. 忘记包含头文件

```cpp
#include <fstream>
```

### 2. 没检查文件是否打开成功

```cpp
if (!file.is_open()) {
    cout << "打开失败";
}
```

### 3. 读文件时死循环

推荐：

```cpp
while (file >> x) {
}
```

或者：

```cpp
while (getline(file, line)) {
}
```

不要写很复杂的 eof 判断。

### 4. 二进制保存 string 对象

不要直接：

```cpp
file.write(reinterpret_cast<char*>(&student), sizeof(student));
```

如果 `student` 里有 `string`，这样不可靠。

### 5. 路径问题

```cpp
ofstream file("data.txt");
```

文件会出现在程序运行时的当前工作目录，不一定是源码所在目录。

---

**二十一、你需要掌握的重点**

这一章重点掌握：

```text
1. ofstream 写文件
2. ifstream 读文件
3. getline 按行读取
4. file >> x 按格式读取
5. ios::app 追加
6. ios::binary 二进制模式
7. is_open 检查打开成功
8. 文件流也是 RAII，会自动关闭
9. 文件操作可以配合异常
10. 不要直接二进制保存含 string/vector 的对象
```

常用模板：

```cpp
ofstream file("data.txt");
file << "hello" << endl;
```

```cpp
ifstream file("data.txt");
string line;
while (getline(file, line)) {
    cout << line << endl;
}
```

```cpp
ifstream file("nums.txt");
int x;
while (file >> x) {
    cout << x << endl;
}
```

---

**二十二、练习题**

### 练习 1：写入文本

写程序创建 `hello.txt`，写入：

```text
Hello C++
I am learning file IO
```

---

### 练习 2：读取文本

读取 `hello.txt`，逐行输出。

---

### 练习 3：保存学生成绩

用 `vector<Student>` 保存学生信息，然后写入 `students.txt`。

格式：

```text
name score
```

---

### 练习 4：读取学生成绩并排序

从 `students.txt` 读取学生信息，按成绩从高到低排序后输出。

---

### 练习 5：统计文件单词数

读取一个文本文件，统计每个单词出现次数。

提示：

```cpp
map<string, int>
ifstream
while (file >> word)
```

---

下一步建议学习：

```text
C++ 项目组织和头文件
```

也就是：

```text
.h / .cpp 分离
include guard
#pragma once
命名空间
多文件编译
类声明和实现分离
```

这是从“写单个 cpp 文件”进入“写真正项目”的关键。