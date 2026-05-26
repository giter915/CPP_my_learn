现在继续学习：

```text
CMake 基础
```

CMake 是 C++ 项目里非常常见的构建工具。学会它之后，你就可以更方便地管理多文件项目。

---

**一、为什么需要 CMake**

之前多文件编译可能要写：

```bash
g++ main.cpp Student.cpp StudentManager.cpp -o app
```

文件少还好。

如果项目变成：

```text
main.cpp
Student.cpp
Teacher.cpp
Course.cpp
FileManager.cpp
StudentManager.cpp
```

命令就会很长。

如果还要加：

```text
头文件目录
C++ 标准版本
第三方库
编译选项
Debug/Release
```

手写命令就很麻烦。

CMake 可以帮你管理这些内容。

---

**二、CMake 是什么**

CMake 不是编译器。

真正编译 C++ 的还是：

```text
g++
clang++
MSVC
```

CMake 的作用是：

```text
根据 CMakeLists.txt 生成构建系统
```

例如生成：

```text
Makefile
Ninja 构建文件
Visual Studio 工程
```

然后再由这些工具调用编译器。

你可以理解为：

```text
CMakeLists.txt 是项目说明书
CMake 根据说明书组织编译
```

---

**三、最小 CMake 项目**

项目结构：

```text
HelloCMake/
    CMakeLists.txt
    main.cpp
```

`main.cpp`：

```cpp
#include <iostream>

int main() {
    std::cout << "Hello CMake" << std::endl;
    return 0;
}
```

`CMakeLists.txt`：

```cmake
cmake_minimum_required(VERSION 3.10)

project(HelloCMake)

set(CMAKE_CXX_STANDARD 17)

add_executable(hello main.cpp)
```

解释：

```cmake
cmake_minimum_required(VERSION 3.10)
```

要求 CMake 最低版本。

```cmake
project(HelloCMake)
```

项目名字。

```cmake
set(CMAKE_CXX_STANDARD 17)
```

使用 C++17。

```cmake
add_executable(hello main.cpp)
```

生成一个可执行程序，名字叫 `hello`，源文件是 `main.cpp`。

---

**四、推荐的构建方式**

通常不要在源码目录直接构建，而是新建一个 `build` 目录。

```bash
mkdir build
cd build
cmake ..
cmake --build .
```

含义：

```bash
cmake ..
```

读取上一级目录的 `CMakeLists.txt`，生成构建文件。

```bash
cmake --build .
```

执行构建。

---

**五、为什么要 build 目录**

如果直接在源码目录构建，会生成很多文件：

```text
CMakeCache.txt
CMakeFiles/
Makefile
```

会弄乱源码目录。

使用：

```text
build/
```

可以把构建产物集中放进去。

项目变成：

```text
HelloCMake/
    CMakeLists.txt
    main.cpp
    build/
        CMakeCache.txt
        CMakeFiles/
        ...
```

---

**六、多文件项目 CMake**

项目结构：

```text
StudentProject/
    CMakeLists.txt
    main.cpp
    Student.h
    Student.cpp
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
    void show() const;
};
```

`Student.cpp`：

```cpp
#include "Student.h"
#include <iostream>

Student::Student(const std::string& name, int score)
    : name(name), score(score) {
}

void Student::show() const {
    std::cout << name << " " << score << std::endl;
}
```

`main.cpp`：

```cpp
#include "Student.h"

int main() {
    Student s("张三", 90);
    s.show();
    return 0;
}
```

`CMakeLists.txt`：

```cmake
cmake_minimum_required(VERSION 3.10)

project(StudentProject)

set(CMAKE_CXX_STANDARD 17)

add_executable(student_app
    main.cpp
    Student.cpp
)
```

注意：

```cmake
Student.h
```

通常不用写进 `add_executable` 也能编译，因为真正被编译的是 `.cpp`。

不过有些项目会把 `.h` 也列进去，方便 IDE 显示。

---

**七、include 目录结构**

真实项目常这样组织：

```text
StudentProject/
    CMakeLists.txt
    include/
        Student.h
    src/
        Student.cpp
        main.cpp
```

这时候 `main.cpp` 里：

```cpp
#include "Student.h"
```

但头文件在 `include/` 目录，编译器不一定找得到。

需要在 CMake 里添加头文件搜索路径：

```cmake
target_include_directories(student_app PRIVATE include)
```

完整：

```cmake
cmake_minimum_required(VERSION 3.10)

project(StudentProject)

set(CMAKE_CXX_STANDARD 17)

add_executable(student_app
    src/main.cpp
    src/Student.cpp
)

target_include_directories(student_app PRIVATE include)
```

---

**八、target 是什么**

在 CMake 中，`target` 可以理解为构建目标。

比如：

```cmake
add_executable(student_app ...)
```

这里 `student_app` 就是一个 target。

后面可以给它设置属性：

```cmake
target_include_directories(student_app PRIVATE include)
target_compile_features(student_app PRIVATE cxx_std_17)
target_compile_options(student_app PRIVATE -Wall)
```

现代 CMake 推荐围绕 target 写配置。

---

**九、PRIVATE / PUBLIC / INTERFACE**

你经常会看到：

```cmake
PRIVATE
PUBLIC
INTERFACE
```

它们表示属性是否传递给依赖你的其他 target。

初学先这样理解：

```text
PRIVATE：只自己用
PUBLIC：自己用，别人依赖我时也要用
INTERFACE：自己不用，别人依赖我时用
```

对于可执行程序，通常写：

```cmake
PRIVATE
```

例如：

```cmake
target_include_directories(student_app PRIVATE include)
```

---

**十、用变量收集源文件**

可以写：

```cmake
set(SOURCES
    src/main.cpp
    src/Student.cpp
    src/StudentManager.cpp
)

add_executable(student_app ${SOURCES})
```

这样文件多时更清楚。

完整：

```cmake
cmake_minimum_required(VERSION 3.10)

project(StudentProject)

set(CMAKE_CXX_STANDARD 17)

set(SOURCES
    src/main.cpp
    src/Student.cpp
    src/StudentManager.cpp
)

add_executable(student_app ${SOURCES})

target_include_directories(student_app PRIVATE include)
```

---

**十一、生成库 add_library**

如果你想把一些代码做成库，可以用：

```cmake
add_library
```

例如：

```text
include/
    Student.h
    StudentManager.h
src/
    Student.cpp
    StudentManager.cpp
    main.cpp
```

可以把学生管理相关代码做成库：

```cmake
add_library(school_lib
    src/Student.cpp
    src/StudentManager.cpp
)
```

然后可执行程序链接它：

```cmake
add_executable(student_app src/main.cpp)

target_link_libraries(student_app PRIVATE school_lib)
```

完整：

```cmake
cmake_minimum_required(VERSION 3.10)

project(StudentProject)

set(CMAKE_CXX_STANDARD 17)

add_library(school_lib
    src/Student.cpp
    src/StudentManager.cpp
)

target_include_directories(school_lib PUBLIC include)

add_executable(student_app src/main.cpp)

target_link_libraries(student_app PRIVATE school_lib)
```

这里：

```cmake
target_include_directories(school_lib PUBLIC include)
```

写 `PUBLIC` 是因为：

```text
school_lib 自己需要 include
student_app 使用 school_lib 时也需要 include
```

---

**十二、target_compile_features**

比全局设置：

```cmake
set(CMAKE_CXX_STANDARD 17)
```

更现代的写法是：

```cmake
target_compile_features(student_app PRIVATE cxx_std_17)
```

如果是库：

```cmake
target_compile_features(school_lib PUBLIC cxx_std_17)
```

表示使用这个库的人也需要 C++17。

---

**十三、Debug 和 Release**

CMake 可以指定构建类型。

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug
cmake --build build
```

或者 Release：

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build
```

区别：

```text
Debug：带调试信息，方便调试
Release：优化性能，适合发布
```

注意：Visual Studio 这类多配置生成器可能在 build 时指定：

```bash
cmake --build build --config Debug
```

---

**十四、推荐命令形式**

更推荐这样写：

```bash
cmake -S . -B build
cmake --build build
```

含义：

```text
-S .       源码目录是当前目录
-B build   构建目录是 build
```

比：

```bash
mkdir build
cd build
cmake ..
```

更清晰。

---

**十五、清理构建目录**

如果 CMake 配置乱了，最简单方式是删除 `build` 目录重新生成。

```bash
rm -rf build
```

Windows PowerShell：

```powershell
Remove-Item -Recurse -Force build
```

然后重新：

```bash
cmake -S . -B build
cmake --build build
```

---

**十六、常见错误**

### 1. 忘记添加某个 .cpp

比如：

```cmake
add_executable(app main.cpp)
```

但 `Student.cpp` 没加进去。

可能报：

```text
undefined reference to Student::show()
```

解决：

```cmake
add_executable(app
    main.cpp
    Student.cpp
)
```

---

### 2. 头文件找不到

错误：

```text
fatal error: Student.h: No such file or directory
```

解决：

```cmake
target_include_directories(app PRIVATE include)
```

---

### 3. include 了 .cpp

不要这样：

```cpp
#include "Student.cpp"
```

应该 include：

```cpp
#include "Student.h"
```

然后在 CMake 里编译 `Student.cpp`。

---

### 4. C++ 标准不够

如果你用了：

```cpp
make_unique
```

至少需要 C++14。

如果用了更多现代特性，推荐 C++17 或 C++20。

CMake 中：

```cmake
target_compile_features(app PRIVATE cxx_std_17)
```

---

**十七、完整案例：学生管理项目**

项目结构：

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

`CMakeLists.txt`：

```cmake
cmake_minimum_required(VERSION 3.10)

project(StudentManager)

add_library(school_lib
    src/Student.cpp
    src/StudentManager.cpp
)

target_include_directories(school_lib PUBLIC include)

target_compile_features(school_lib PUBLIC cxx_std_17)

add_executable(student_app
    src/main.cpp
)

target_link_libraries(student_app PRIVATE school_lib)
```

这个结构已经比较像真实小项目了。

---

**十八、什么时候用库**

如果项目里有很多业务代码，比如：

```text
Student
Teacher
Course
StudentManager
FileManager
```

可以放进库：

```cmake
add_library(school_lib ...)
```

然后：

```cmake
add_executable(app src/main.cpp)
target_link_libraries(app PRIVATE school_lib)
```

这样结构更清晰。

以后你写测试时，也可以让测试程序链接同一个库。

---

**十九、CMakeLists.txt 常用模板**

单文件项目：

```cmake
cmake_minimum_required(VERSION 3.10)

project(MyApp)

add_executable(my_app main.cpp)

target_compile_features(my_app PRIVATE cxx_std_17)
```

多文件项目：

```cmake
cmake_minimum_required(VERSION 3.10)

project(MyApp)

add_executable(my_app
    src/main.cpp
    src/A.cpp
    src/B.cpp
)

target_include_directories(my_app PRIVATE include)

target_compile_features(my_app PRIVATE cxx_std_17)
```

库 + 可执行程序：

```cmake
cmake_minimum_required(VERSION 3.10)

project(MyApp)

add_library(my_lib
    src/A.cpp
    src/B.cpp
)

target_include_directories(my_lib PUBLIC include)

target_compile_features(my_lib PUBLIC cxx_std_17)

add_executable(my_app src/main.cpp)

target_link_libraries(my_app PRIVATE my_lib)
```

---

**二十、你需要掌握的重点**

这一章重点掌握：

```text
1. CMake 用 CMakeLists.txt 描述项目
2. add_executable 生成可执行程序
3. add_library 生成库
4. target_include_directories 添加头文件路径
5. target_link_libraries 链接库
6. target_compile_features 设置 C++ 标准
7. 推荐使用 build 目录
8. cmake -S . -B build
9. cmake --build build
10. undefined reference 常常是 .cpp 没加入构建
```

---

**二十一、练习题**

### 练习 1：单文件 CMake

创建：

```text
main.cpp
CMakeLists.txt
```

使用 CMake 编译运行 Hello CMake。

---

### 练习 2：多文件 CMake

创建：

```text
main.cpp
Student.h
Student.cpp
CMakeLists.txt
```

用 CMake 构建。

---

### 练习 3：include/src 结构

改成：

```text
include/Student.h
src/Student.cpp
src/main.cpp
```

使用：

```cmake
target_include_directories
```

解决头文件路径。

---

### 练习 4：库 + 主程序

创建：

```text
school_lib
student_app
```

用：

```cmake
add_library
add_executable
target_link_libraries
```

组织项目。

---

下一步建议学习：

```text
调试与错误排查
```

也就是：

```text
编译错误
链接错误
运行时错误
断点调试
gdb / lldb / Visual Studio 调试器
日志输出
assert
单元测试入门
```

这会让你从“会写代码”变成“能修代码”。