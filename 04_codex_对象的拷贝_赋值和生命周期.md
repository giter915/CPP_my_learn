你现在已经学完：

```text
类和对象
封装
继承
多态
```

下一步建议学习：

```text
对象的拷贝、赋值和生命周期
```

也就是：

```text
拷贝构造函数
赋值运算符重载
析构函数
浅拷贝和深拷贝
三法则
五法则
```

这部分非常重要。因为 C++ 和很多语言不一样：对象什么时候创建、什么时候复制、什么时候销毁，很多时候都由你来控制。

---

**一、为什么要学拷贝构造**

先看这个例子：

```cpp
class Student {
public:
    string name;
    int age;
};
```

我们可以这样写：

```cpp
Student s1;
s1.name = "张三";
s1.age = 18;

Student s2 = s1;
```

这里：

```cpp
Student s2 = s1;
```

表示用 `s1` 创建一个新的对象 `s2`。

这时候会发生一次“对象拷贝”。

C++ 会把 `s1` 的成员变量复制给 `s2`：

```text
s2.name = s1.name
s2.age = s1.age
```

这看起来很自然，但底层其实调用了一个特殊函数：

```cpp
拷贝构造函数
```

---

**二、什么是拷贝构造函数**

拷贝构造函数是一种特殊构造函数，用来“用已有对象创建新对象”。

语法：

```cpp
类名(const 类名& other)
```

例如：

```cpp
class Student {
public:
    string name;
    int age;

    Student(string n, int a) : name(n), age(a) {
        cout << "普通构造函数" << endl;
    }

    Student(const Student& other) {
        cout << "拷贝构造函数" << endl;
        name = other.name;
        age = other.age;
    }
};
```

使用：

```cpp
int main() {
    Student s1("张三", 18);

    Student s2 = s1;

    return 0;
}
```

输出：

```text
普通构造函数
拷贝构造函数
```

---

**三、拷贝构造函数什么时候调用**

常见情况有三种。

### 1. 用一个对象创建另一个对象

```cpp
Student s2 = s1;
```

或者：

```cpp
Student s2(s1);
```

都会调用拷贝构造函数。

---

### 2. 对象作为函数参数，按值传递

```cpp
void printStudent(Student s) {
    cout << s.name << endl;
}
```

调用：

```cpp
printStudent(s1);
```

这里会复制一份 `s1` 给参数 `s`，所以调用拷贝构造函数。

为了避免不必要的复制，通常写成引用：

```cpp
void printStudent(const Student& s) {
    cout << s.name << endl;
}
```

这不会复制对象。

---

### 3. 函数按值返回对象

```cpp
Student createStudent() {
    Student s("张三", 18);
    return s;
}
```

理论上可能发生拷贝，不过现代 C++ 编译器通常会做返回值优化。

你初学时可以先知道：按值返回对象可能涉及拷贝或移动。

---

**四、默认拷贝构造函数**

如果你不写拷贝构造函数，C++ 会自动生成一个默认的。

它会逐个成员复制。

比如：

```cpp
class Student {
public:
    string name;
    int age;
};
```

默认拷贝效果类似：

```cpp
Student(const Student& other) {
    name = other.name;
    age = other.age;
}
```

对于普通成员变量，这样通常没问题。

但是如果类里面有指针，就可能出事。

---

**五、浅拷贝**

看这个例子：

```cpp
#include <iostream>
using namespace std;

class Person {
public:
    int* age;

    Person(int a) {
        age = new int(a);
    }

    ~Person() {
        delete age;
    }
};
```

使用：

```cpp
int main() {
    Person p1(18);
    Person p2 = p1;

    return 0;
}
```

表面上看没问题，但实际上很危险。

默认拷贝会这样复制：

```cpp
p2.age = p1.age;
```

也就是说，两个对象的指针指向同一块内存。

```text
p1.age ----+
           |
           +---- 堆内存：18
           |
p2.age ----+
```

程序结束时：

```text
p2 析构，delete age
p1 析构，又 delete age
```

同一块内存被释放两次。

这就是浅拷贝的问题。

浅拷贝的意思是：

```text
只复制指针地址，不复制指针指向的资源
```

---

**六、深拷贝**

深拷贝的意思是：

```text
不只复制指针地址，而是重新申请一块新内存，并复制里面的内容
```

正确写法：

```cpp
#include <iostream>
using namespace std;

class Person {
public:
    int* age;

    Person(int a) {
        age = new int(a);
    }

    Person(const Person& other) {
        age = new int(*other.age);
    }

    ~Person() {
        delete age;
    }
};
```

这样：

```cpp
Person p1(18);
Person p2 = p1;
```

内存关系是：

```text
p1.age ---> 堆内存：18

p2.age ---> 另一块堆内存：18
```

两个对象互不影响。

完整例子：

```cpp
#include <iostream>
using namespace std;

class Person {
public:
    int* age;

    Person(int a) {
        age = new int(a);
        cout << "构造函数" << endl;
    }

    Person(const Person& other) {
        age = new int(*other.age);
        cout << "拷贝构造函数：深拷贝" << endl;
    }

    ~Person() {
        delete age;
        cout << "析构函数" << endl;
    }

    void showAge() const {
        cout << "年龄：" << *age << endl;
    }
};

int main() {
    Person p1(18);
    Person p2 = p1;

    p1.showAge();
    p2.showAge();

    return 0;
}
```

---

**七、赋值运算符**

看这两句：

```cpp
Person p2 = p1;
```

和：

```cpp
p2 = p1;
```

它们不一样。

第一种：

```cpp
Person p2 = p1;
```

是在创建新对象时复制，所以调用：

```text
拷贝构造函数
```

第二种：

```cpp
p2 = p1;
```

两个对象都已经存在了，是赋值操作，所以调用：

```text
赋值运算符 operator=
```

---

**八、赋值运算符重载**

如果类里有指针资源，也需要自己写赋值运算符。

错误风险：

```cpp
Person p1(18);
Person p2(20);

p2 = p1;
```

如果用默认赋值，也是浅拷贝：

```cpp
p2.age = p1.age;
```

这又会导致两个对象指向同一块内存。

正确写法：

```cpp
class Person {
private:
    int* age;

public:
    Person(int a) {
        age = new int(a);
    }

    Person(const Person& other) {
        age = new int(*other.age);
    }

    Person& operator=(const Person& other) {
        if (this != &other) {
            delete age;
            age = new int(*other.age);
        }

        return *this;
    }

    ~Person() {
        delete age;
    }
};
```

重点看这里：

```cpp
Person& operator=(const Person& other)
```

它表示重载赋值运算符。

---

**九、为什么要判断 this != &other**

赋值可能出现自己给自己赋值：

```cpp
p1 = p1;
```

如果不判断，代码可能这样执行：

```cpp
delete age;
age = new int(*other.age);
```

但 `other` 就是自己，`other.age` 已经被 delete 了，再读取就危险。

所以要写：

```cpp
if (this != &other) {
    delete age;
    age = new int(*other.age);
}
```

---

**十、为什么返回 Person&**

赋值运算符通常返回当前对象引用：

```cpp
return *this;
```

这样可以支持连续赋值：

```cpp
p3 = p2 = p1;
```

如果 `operator=` 不返回引用，这种写法就不方便了。

---

**十一、完整代码：拷贝构造 + 赋值运算符 + 析构函数**

```cpp
#include <iostream>
using namespace std;

class Person {
private:
    int* age;

public:
    Person(int a) {
        age = new int(a);
        cout << "构造函数" << endl;
    }

    Person(const Person& other) {
        age = new int(*other.age);
        cout << "拷贝构造函数" << endl;
    }

    Person& operator=(const Person& other) {
        cout << "赋值运算符" << endl;

        if (this != &other) {
            delete age;
            age = new int(*other.age);
        }

        return *this;
    }

    ~Person() {
        delete age;
        cout << "析构函数" << endl;
    }

    void setAge(int a) {
        *age = a;
    }

    int getAge() const {
        return *age;
    }
};

int main() {
    Person p1(18);
    Person p2 = p1;

    Person p3(20);
    p3 = p1;

    p1.setAge(30);

    cout << p1.getAge() << endl;
    cout << p2.getAge() << endl;
    cout << p3.getAge() << endl;

    return 0;
}
```

输出大致是：

```text
构造函数
拷贝构造函数
构造函数
赋值运算符
30
18
18
析构函数
析构函数
析构函数
```

注意：

```text
p1 改成 30
p2 和 p3 仍然是 18
```

说明它们是深拷贝，各自有自己的内存。

---

**十二、三法则**

C++ 中有一个非常重要的经验规则：

```text
如果一个类需要自己写析构函数，
通常也需要自己写拷贝构造函数和赋值运算符。
```

这三个函数是：

```text
析构函数
拷贝构造函数
拷贝赋值运算符
```

这叫：

```text
三法则
Rule of Three
```

为什么？

因为如果你需要自己写析构函数，通常说明你的类管理了资源，比如：

```text
new 出来的内存
文件句柄
网络连接
锁
系统资源
```

既然管理资源，就必须考虑：

```text
对象销毁时怎么释放
对象拷贝时怎么复制
对象赋值时怎么处理
```

---

**十三、五法则**

现代 C++ 里还有移动语义，所以三法则扩展成五法则。

五个函数是：

```text
析构函数
拷贝构造函数
拷贝赋值运算符
移动构造函数
移动赋值运算符
```

也叫：

```text
Rule of Five
```

移动语义你可以稍后再学。现在先把前三个掌握好。

---

**十四、零法则**

现代 C++ 更推荐：

```text
能不手动管理资源，就不手动管理资源。
```

比如尽量使用：

```cpp
std::string
std::vector
std::unique_ptr
std::shared_ptr
```

这样很多时候不需要自己写析构、拷贝、赋值。

这叫：

```text
零法则
Rule of Zero
```

例如：

```cpp
class Student {
private:
    string name;
    vector<int> scores;
};
```

这种类通常不需要自己写析构函数、拷贝构造函数、赋值运算符。

因为 `string` 和 `vector` 自己已经管理好了资源。

---

**十五、什么时候必须自己写这些函数**

如果类里有裸指针，并且负责 `new/delete`，就要警惕。

例如：

```cpp
class MyArray {
private:
    int* data;
    int size;
};
```

如果这个类负责：

```cpp
data = new int[size];
```

那么你通常就要自己写：

```text
析构函数
拷贝构造函数
赋值运算符
```

否则默认拷贝会复制指针地址，导致浅拷贝问题。

---

**十六、一个更完整的 MyArray 案例**

这个例子很经典。

```cpp
#include <iostream>
using namespace std;

class MyArray {
private:
    int* data;
    int size;

public:
    MyArray(int s) : size(s) {
        data = new int[size];

        for (int i = 0; i < size; i++) {
            data[i] = 0;
        }
    }

    MyArray(const MyArray& other) : size(other.size) {
        data = new int[size];

        for (int i = 0; i < size; i++) {
            data[i] = other.data[i];
        }
    }

    MyArray& operator=(const MyArray& other) {
        if (this != &other) {
            delete[] data;

            size = other.size;
            data = new int[size];

            for (int i = 0; i < size; i++) {
                data[i] = other.data[i];
            }
        }

        return *this;
    }

    ~MyArray() {
        delete[] data;
    }

    void set(int index, int value) {
        if (index >= 0 && index < size) {
            data[index] = value;
        }
    }

    int get(int index) const {
        if (index >= 0 && index < size) {
            return data[index];
        }

        return -1;
    }

    int getSize() const {
        return size;
    }
};
```

使用：

```cpp
int main() {
    MyArray a1(3);
    a1.set(0, 10);
    a1.set(1, 20);
    a1.set(2, 30);

    MyArray a2 = a1;

    a1.set(0, 100);

    cout << a1.get(0) << endl;
    cout << a2.get(0) << endl;

    return 0;
}
```

输出：

```text
100
10
```

说明 `a2` 有自己的数组，不受 `a1` 修改影响。

---

**十七、new 和 delete 的匹配**

这部分特别容易错。

如果你用：

```cpp
new int
```

释放时用：

```cpp
delete
```

例如：

```cpp
int* p = new int(10);
delete p;
```

如果你用：

```cpp
new int[10]
```

释放时必须用：

```cpp
delete[]
```

例如：

```cpp
int* arr = new int[10];
delete[] arr;
```

一定要匹配：

```text
new      -> delete
new[]    -> delete[]
```

---

**十八、拷贝构造和赋值运算符的区别**

这点非常重要。

```cpp
Person p2 = p1;
```

这是：

```text
创建对象时拷贝
调用拷贝构造函数
```

而：

```cpp
Person p2(20);
p2 = p1;
```

这是：

```text
对象已经存在
调用赋值运算符
```

记忆方式：

```text
新对象：拷贝构造
旧对象：赋值运算符
```

---

**十九、const 引用参数**

拷贝构造函数为什么这样写？

```cpp
Person(const Person& other)
```

不是：

```cpp
Person(Person other)
```

原因是：

```cpp
Person(Person other)
```

参数按值传递，会再次调用拷贝构造函数，于是无限递归。

所以必须用引用：

```cpp
Person(const Person& other)
```

加 `const` 是为了表示不会修改被拷贝的对象。

---

**二十、学习建议**

这一章你重点掌握：

```text
1. 什么是拷贝构造函数
2. 什么时候调用拷贝构造函数
3. 什么是浅拷贝
4. 什么是深拷贝
5. 什么是赋值运算符重载
6. 为什么要判断自赋值
7. 什么是三法则
8. new/delete 和 new[]/delete[] 的匹配
```

这部分比前面的继承、多态更偏底层，但非常重要。

---

**二十一、练习题**

### 练习 1：Person 深拷贝

写一个 `Person` 类：

```text
成员：
int* age
```

要求：

```text
构造函数中 new int
析构函数中 delete
自己写拷贝构造函数
自己写赋值运算符
```

测试：

```cpp
Person p1(18);
Person p2 = p1;
Person p3(20);

p3 = p1;

p1.setAge(30);

cout << p1.getAge() << endl; // 30
cout << p2.getAge() << endl; // 18
cout << p3.getAge() << endl; // 18
```

---

### 练习 2：MyArray

写一个数组类 `MyArray`：

```text
成员：
int* data
int size
```

要求：

```text
构造函数申请数组
析构函数释放数组
拷贝构造函数实现深拷贝
赋值运算符实现深拷贝
set(index, value)
get(index)
getSize()
```

---

### 练习 3：Book 类

写一个 `Book` 类：

```text
成员：
char* title
```

要求：

```text
构造函数接收字符串
析构函数释放 title
拷贝构造函数深拷贝 title
赋值运算符深拷贝 title
showTitle()
```

这个练习可以很好地训练你处理 C 风格字符串，不过实际项目中更推荐 `std::string`。

---

你现在已经从“怎么定义类”进入到“对象在内存里到底怎么活着”。这一章如果掌握牢，后面学 STL、智能指针、移动语义都会轻松很多。