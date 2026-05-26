现在我们继续学习：

```text
智能指针
RAII
资源管理
```

这是现代 C++ 非常重要的一部分。学完它之后，你会更少手动写：

```cpp
new
delete
```

而是使用更安全的方式管理内存。

---

**一、为什么需要智能指针**

以前我们这样创建对象：

```cpp
Student* p = new Student("张三");
delete p;
```

问题是：你必须记得 `delete`。

如果忘记：

```cpp
Student* p = new Student("张三");

// 忘了 delete
```

就会造成内存泄漏。

如果重复 delete：

```cpp
delete p;
delete p;
```

可能导致程序崩溃。

如果中间发生异常：

```cpp
Student* p = new Student("张三");

someFunction(); // 如果这里抛异常

delete p;       // 可能执行不到
```

也会泄漏。

所以现代 C++ 推荐使用智能指针。

---

**二、什么是智能指针**

智能指针本质上是一个类对象。

它像普通指针一样指向资源，但会在自己生命周期结束时自动释放资源。

也就是说：

```text
创建时接管资源
销毁时自动释放资源
```

常用智能指针有三个：

```cpp
unique_ptr
shared_ptr
weak_ptr
```

头文件：

```cpp
#include <memory>
```

---

**三、RAII 思想**

RAII 是 C++ 非常核心的资源管理思想。

全称：

```text
Resource Acquisition Is Initialization
资源获取即初始化
```

简单理解：

```text
对象创建时获取资源
对象销毁时释放资源
```

比如智能指针：

```cpp
{
    unique_ptr<Student> p = make_unique<Student>("张三");
} // 离开作用域时自动释放
```

当 `p` 离开作用域，它的析构函数自动调用，然后释放里面管理的对象。

你不用手动写：

```cpp
delete
```

这就是 RAII。

---

**四、unique_ptr**

`unique_ptr` 表示：

```text
独占所有权
```

也就是说，同一块资源只能有一个 `unique_ptr` 管理。

基本用法：

```cpp
#include <iostream>
#include <memory>
using namespace std;

class Student {
private:
    string name;

public:
    Student(string n) : name(n) {
        cout << "Student 构造：" << name << endl;
    }

    ~Student() {
        cout << "Student 析构：" << name << endl;
    }

    void show() const {
        cout << "姓名：" << name << endl;
    }
};

int main() {
    unique_ptr<Student> p = make_unique<Student>("张三");

    p->show();

    return 0;
}
```

输出：

```text
Student 构造：张三
姓名：张三
Student 析构：张三
```

注意：我们没有写 `delete`，但对象自动释放了。

---

**五、make_unique**

推荐这样创建 `unique_ptr`：

```cpp
auto p = make_unique<Student>("张三");
```

而不是：

```cpp
unique_ptr<Student> p(new Student("张三"));
```

`make_unique` 更安全、更简洁。

---

**六、unique_ptr 不能拷贝**

因为它是独占所有权。

```cpp
auto p1 = make_unique<Student>("张三");

auto p2 = p1; // 错误
```

如果允许这样做，就会变成两个智能指针管理同一个对象，最后可能释放两次。

---

**七、unique_ptr 可以移动**

虽然不能拷贝，但可以移动。

```cpp
auto p1 = make_unique<Student>("张三");

auto p2 = std::move(p1);
```

移动后：

```text
p2 拥有对象
p1 变为空
```

可以检查：

```cpp
if (p1 == nullptr) {
    cout << "p1 已经为空" << endl;
}
```

完整例子：

```cpp
#include <iostream>
#include <memory>
using namespace std;

class Student {
public:
    Student() {
        cout << "构造" << endl;
    }

    ~Student() {
        cout << "析构" << endl;
    }

    void hello() {
        cout << "hello" << endl;
    }
};

int main() {
    unique_ptr<Student> p1 = make_unique<Student>();

    unique_ptr<Student> p2 = std::move(p1);

    if (p1 == nullptr) {
        cout << "p1 为空" << endl;
    }

    p2->hello();

    return 0;
}
```

---

**八、unique_ptr 作为函数参数**

如果函数只是使用对象，不接管所有权，传引用：

```cpp
void printStudent(const Student& s) {
    s.show();
}
```

调用：

```cpp
auto p = make_unique<Student>("张三");
printStudent(*p);
```

如果函数要接管所有权，传 `unique_ptr`：

```cpp
void takeOwnership(unique_ptr<Student> p) {
    p->show();
}
```

调用：

```cpp
takeOwnership(std::move(p));
```

这表示把所有权交给函数。

---

**九、unique_ptr 作为返回值**

函数可以返回 `unique_ptr`：

```cpp
unique_ptr<Student> createStudent() {
    return make_unique<Student>("张三");
}
```

使用：

```cpp
auto p = createStudent();
p->show();
```

这很常见，表示函数创建对象并把所有权交给调用者。

---

**十、shared_ptr**

`shared_ptr` 表示：

```text
共享所有权
```

多个 `shared_ptr` 可以共同管理同一个对象。

只有最后一个 `shared_ptr` 被销毁时，对象才会释放。

基本用法：

```cpp
#include <iostream>
#include <memory>
using namespace std;

class Student {
public:
    Student() {
        cout << "构造" << endl;
    }

    ~Student() {
        cout << "析构" << endl;
    }
};

int main() {
    shared_ptr<Student> p1 = make_shared<Student>();

    {
        shared_ptr<Student> p2 = p1;

        cout << p1.use_count() << endl;
        cout << p2.use_count() << endl;
    }

    cout << p1.use_count() << endl;

    return 0;
}
```

输出大致：

```text
构造
2
2
1
析构
```

---

**十一、make_shared**

推荐用：

```cpp
auto p = make_shared<Student>();
```

而不是：

```cpp
shared_ptr<Student> p(new Student());
```

`make_shared` 更高效，也更安全。

---

**十二、use_count**

`use_count()` 返回当前有多少个 `shared_ptr` 共享这个对象。

```cpp
cout << p.use_count() << endl;
```

但是实际开发中，不要过度依赖它做复杂逻辑。它更多用于调试和理解。

---

**十三、shared_ptr 适合什么时候用**

适合：

```text
资源确实需要被多个对象共享
不知道哪个对象最后释放资源
需要共同拥有一个对象
```

例如：

```cpp
shared_ptr<Resource> r = make_shared<Resource>();
```

多个模块都要持有它，并且资源应该在最后一个使用者结束后释放。

但如果只有一个所有者，优先用：

```cpp
unique_ptr
```

---

**十四、weak_ptr**

`weak_ptr` 是配合 `shared_ptr` 使用的。

它表示：

```text
弱引用
不增加引用计数
不拥有对象
```

为什么需要它？

因为 `shared_ptr` 可能产生循环引用。

---

**十五、shared_ptr 循环引用问题**

看例子：

```cpp
class B;

class A {
public:
    shared_ptr<B> b;

    ~A() {
        cout << "A 析构" << endl;
    }
};

class B {
public:
    shared_ptr<A> a;

    ~B() {
        cout << "B 析构" << endl;
    }
};
```

使用：

```cpp
int main() {
    auto pa = make_shared<A>();
    auto pb = make_shared<B>();

    pa->b = pb;
    pb->a = pa;

    return 0;
}
```

问题：

```text
pa 指向 A
pb 指向 B
A 里面 shared_ptr 指向 B
B 里面 shared_ptr 指向 A
```

即使 `main` 结束，外部 `pa` 和 `pb` 销毁了，但 A 和 B 互相引用，引用计数都不是 0，所以不会析构。

这就是循环引用。

---

**十六、用 weak_ptr 打破循环引用**

把其中一方改成 `weak_ptr`：

```cpp
class B;

class A {
public:
    shared_ptr<B> b;

    ~A() {
        cout << "A 析构" << endl;
    }
};

class B {
public:
    weak_ptr<A> a;

    ~B() {
        cout << "B 析构" << endl;
    }
};
```

完整例子：

```cpp
#include <iostream>
#include <memory>
using namespace std;

class B;

class A {
public:
    shared_ptr<B> b;

    ~A() {
        cout << "A 析构" << endl;
    }
};

class B {
public:
    weak_ptr<A> a;

    ~B() {
        cout << "B 析构" << endl;
    }
};

int main() {
    auto pa = make_shared<A>();
    auto pb = make_shared<B>();

    pa->b = pb;
    pb->a = pa;

    return 0;
}
```

现在可以正常析构。

---

**十七、weak_ptr 怎么使用对象**

`weak_ptr` 不能直接使用对象。

不能这样：

```cpp
weak_ptr<Student> wp;
wp->show(); // 错误
```

要先通过：

```cpp
lock()
```

转换成 `shared_ptr`。

```cpp
shared_ptr<Student> sp = wp.lock();

if (sp) {
    sp->show();
}
```

完整例子：

```cpp
auto sp = make_shared<Student>();
weak_ptr<Student> wp = sp;

if (auto locked = wp.lock()) {
    locked->show();
}
```

如果对象已经被释放，`lock()` 会返回空的 `shared_ptr`。

---

**十八、智能指针怎么选**

你可以这样记：

```text
默认优先 unique_ptr
确实需要共享所有权，用 shared_ptr
需要观察 shared_ptr 管理的对象，但不拥有它，用 weak_ptr
```

简单决策：

```text
只有一个所有者？
用 unique_ptr

多个地方共同拥有？
用 shared_ptr

只是引用一下，不想延长生命周期？
用 weak_ptr
```

---

**十九、智能指针和普通指针**

普通指针仍然有用，但更多时候它表达：

```text
我只是观察，不负责释放
```

比如：

```cpp
void print(Student* p) {
    if (p) {
        p->show();
    }
}
```

但如果涉及所有权，尽量用智能指针表达清楚。

---

**二十、不要这样用智能指针**

### 1. 不要用同一个裸指针创建多个智能指针

错误：

```cpp
Student* raw = new Student();

shared_ptr<Student> p1(raw);
shared_ptr<Student> p2(raw);
```

这会导致两个 `shared_ptr` 以为自己分别拥有对象，最后释放两次。

正确：

```cpp
auto p1 = make_shared<Student>();
auto p2 = p1;
```

---

### 2. 不要随便 get 后 delete

```cpp
auto p = make_unique<Student>();

Student* raw = p.get();

delete raw; // 错误
```

智能指针还会再释放一次，危险。

---

### 3. 不要滥用 shared_ptr

如果资源不需要共享，不要为了方便全部用 `shared_ptr`。

优先：

```cpp
unique_ptr
```

因为它语义更清楚，开销也更小。

---

**二十一、智能指针和多态**

智能指针经常和多态一起使用。

```cpp
class Animal {
public:
    virtual void speak() = 0;

    virtual ~Animal() {
    }
};

class Dog : public Animal {
public:
    void speak() override {
        cout << "狗叫" << endl;
    }
};

int main() {
    unique_ptr<Animal> animal = make_unique<Dog>();

    animal->speak();

    return 0;
}
```

输出：

```text
狗叫
```

注意父类析构函数要是虚函数：

```cpp
virtual ~Animal() {}
```

---

**二十二、vector 里存智能指针**

如果你想存多态对象，通常这样：

```cpp
vector<unique_ptr<Animal>> animals;

animals.push_back(make_unique<Dog>());
animals.push_back(make_unique<Cat>());
```

遍历：

```cpp
for (const auto& animal : animals) {
    animal->speak();
}
```

完整例子：

```cpp
#include <iostream>
#include <memory>
#include <vector>
using namespace std;

class Animal {
public:
    virtual void speak() = 0;

    virtual ~Animal() {
    }
};

class Dog : public Animal {
public:
    void speak() override {
        cout << "狗叫" << endl;
    }
};

class Cat : public Animal {
public:
    void speak() override {
        cout << "猫叫" << endl;
    }
};

int main() {
    vector<unique_ptr<Animal>> animals;

    animals.push_back(make_unique<Dog>());
    animals.push_back(make_unique<Cat>());

    for (const auto& animal : animals) {
        animal->speak();
    }

    return 0;
}
```

---

**二十三、unique_ptr 和 vector**

因为 `unique_ptr` 不能拷贝，只能移动，所以放进 vector 时通常这样：

```cpp
vector<unique_ptr<Student>> students;

students.push_back(make_unique<Student>("张三"));
```

如果已有一个 `unique_ptr`：

```cpp
auto p = make_unique<Student>("李四");

students.push_back(std::move(p));
```

移动后：

```cpp
p == nullptr
```

---

**二十四、智能指针核心总结**

你需要记住：

```text
智能指针用于自动管理动态资源
RAII 是 C++ 资源管理核心思想
unique_ptr 表示独占所有权
shared_ptr 表示共享所有权
weak_ptr 表示弱引用，不增加引用计数
优先使用 make_unique / make_shared
少写 new/delete
多用对象、容器和智能指针
```

最常用模板：

```cpp
auto p = make_unique<T>(参数);
```

```cpp
auto p = make_shared<T>(参数);
```

```cpp
unique_ptr<Base> p = make_unique<Derived>();
```

```cpp
vector<unique_ptr<Base>> v;
v.push_back(make_unique<Derived>());
```

---

**二十五、练习题**

### 练习 1：unique_ptr 管理 Student

写一个 `Student` 类：

```text
成员：
name

函数：
show()
```

用：

```cpp
unique_ptr<Student>
make_unique
```

创建对象并调用 `show()`。

---

### 练习 2：unique_ptr 所有权转移

创建：

```cpp
auto p1 = make_unique<Student>("张三");
auto p2 = std::move(p1);
```

判断：

```cpp
p1 是否为空
p2 是否能正常使用
```

---

### 练习 3：shared_ptr 引用计数

创建一个 `shared_ptr<Student>`，复制给另一个 `shared_ptr`，观察：

```cpp
use_count()
```

在不同作用域中的变化。

---

### 练习 4：weak_ptr 打破循环引用

写两个类：

```text
Teacher
Student
```

让 Teacher 拥有多个学生：

```cpp
vector<shared_ptr<Student>>
```

让 Student 弱引用自己的老师：

```cpp
weak_ptr<Teacher>
```

测试程序结束时析构是否正常调用。

---

### 练习 5：多态 + 智能指针

写抽象类：

```cpp
Animal
```

子类：

```cpp
Dog
Cat
Bird
```

使用：

```cpp
vector<unique_ptr<Animal>>
```

保存不同动物，并调用各自的：

```cpp
speak()
```

---

下一步建议学习：

```text
异常处理
```

也就是：

```text
try
catch
throw
exception
自定义异常
异常安全
```

这和 RAII、智能指针关系很深：当程序出错时，C++ 怎么安全地释放资源、报告错误。