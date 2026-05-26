当然。我们来系统学习 C++ 里的“类”。你可以把“类”理解成 C++ 面向对象编程的核心工具。它用来描述一类事物的共同特征和行为。

比如：

```cpp
class Student {
public:
    string name;
    int age;

    void introduce() {
        cout << "我叫 " << name << "，今年 " << age << " 岁。" << endl;
    }
};
```

这里 `Student` 就是一个类，它描述了“学生”这种对象应该有什么数据、能做什么事情。

---

## 一、什么是类

在 C++ 中，类是一种自定义数据类型。

普通变量只能保存单个数据，比如：

```cpp
int age = 18;
string name = "Tom";
```

但现实中的对象通常不止一个属性。比如一个学生有：

```cpp
姓名
年龄
学号
成绩
```

还可能有行为：

```cpp
自我介绍
学习
考试
修改成绩
```

于是我们可以用类把这些内容封装到一起。

```cpp
class Student {
public:
    string name;
    int age;
    int score;

    void study() {
        cout << name << " 正在学习。" << endl;
    }

    void showInfo() {
        cout << "姓名：" << name << endl;
        cout << "年龄：" << age << endl;
        cout << "成绩：" << score << endl;
    }
};
```

类里面的变量叫做**成员变量**，类里面的函数叫做**成员函数**。

---

## 二、对象是什么

类只是一个“模板”或者“设计图”，对象才是真正创建出来的东西。

比如：

```cpp
Student s1;
```

这行代码的意思是：根据 `Student` 这个类，创建一个具体的学生对象 `s1`。

完整例子：

```cpp
#include <iostream>
#include <string>
using namespace std;

class Student {
public:
    string name;
    int age;
    int score;

    void showInfo() {
        cout << "姓名：" << name << endl;
        cout << "年龄：" << age << endl;
        cout << "成绩：" << score << endl;
    }
};

int main() {
    Student s1;

    s1.name = "张三";
    s1.age = 18;
    s1.score = 95;

    s1.showInfo();

    return 0;
}
```

输出：

```text
姓名：张三
年龄：18
成绩：95
```

这里：

```cpp
Student s1;
```

创建对象。

```cpp
s1.name = "张三";
```

访问对象的成员变量。

```cpp
s1.showInfo();
```

调用对象的成员函数。

---

## 三、class 的基本语法

类的基本结构是：

```cpp
class 类名 {
访问权限:
    成员变量;
    成员函数;
};
```

注意最后有一个分号：

```cpp
};
```

这个分号不能忘。

比如：

```cpp
class Person {
public:
    string name;
    int age;

    void sayHello() {
        cout << "Hello" << endl;
    }
};
```

---

## 四、访问权限：public、private、protected

C++ 类里面有三种常见访问权限：

```cpp
public
private
protected
```

初学阶段最重要的是 `public` 和 `private`。

### 1. public：公开权限

`public` 下面的内容可以在类外直接访问。

```cpp
class Person {
public:
    string name;
    int age;
};

int main() {
    Person p;
    p.name = "李四";
    p.age = 20;
}
```

这是允许的。

---

### 2. private：私有权限

`private` 下面的内容只能在类内部访问，类外不能直接访问。

```cpp
class Person {
private:
    int age;

public:
    void setAge(int a) {
        age = a;
    }

    int getAge() {
        return age;
    }
};
```

使用：

```cpp
int main() {
    Person p;

    p.setAge(18);
    cout << p.getAge() << endl;

    return 0;
}
```

但是下面这样不行：

```cpp
p.age = 18; // 错误，age 是 private
```

---

## 五、为什么要用 private

这是类里面非常重要的思想：**封装**。

封装就是把数据保护起来，不让外部随便修改，而是通过函数控制访问。

比如年龄不能是负数：

```cpp
class Person {
private:
    int age;

public:
    void setAge(int a) {
        if (a >= 0 && a <= 150) {
            age = a;
        } else {
            cout << "年龄不合法" << endl;
        }
    }

    int getAge() {
        return age;
    }
};
```

这样外部就不能乱写：

```cpp
p.age = -100; // 不允许
```

只能通过：

```cpp
p.setAge(-100);
```

而 `setAge` 函数可以进行判断。

这就是封装的意义。

---

## 六、class 和 struct 的区别

C++ 里 `class` 和 `struct` 很像，主要区别是：

```cpp
class 默认是 private
struct 默认是 public
```

例如：

```cpp
class A {
    int x;
};
```

等价于：

```cpp
class A {
private:
    int x;
};
```

而：

```cpp
struct B {
    int x;
};
```

等价于：

```cpp
struct B {
public:
    int x;
};
```

所以在 C++ 中：

- `struct` 常用于简单数据组合
- `class` 常用于有封装、有行为的对象

---

## 七、构造函数

构造函数是在创建对象时自动调用的特殊函数。

比如：

```cpp
class Student {
public:
    string name;
    int age;

    Student() {
        cout << "Student 对象被创建了" << endl;
    }
};
```

使用：

```cpp
Student s;
```

创建对象时，会自动执行：

```cpp
Student()
```

---

## 八、带参数的构造函数

构造函数经常用来初始化对象。

```cpp
class Student {
public:
    string name;
    int age;

    Student(string n, int a) {
        name = n;
        age = a;
    }

    void showInfo() {
        cout << name << " " << age << endl;
    }
};
```

使用：

```cpp
int main() {
    Student s("王五", 19);
    s.showInfo();

    return 0;
}
```

输出：

```text
王五 19
```

这比下面这样更方便：

```cpp
Student s;
s.name = "王五";
s.age = 19;
```

---

## 九、构造函数初始化列表

更推荐的初始化方式是初始化列表。

```cpp
class Student {
private:
    string name;
    int age;

public:
    Student(string n, int a) : name(n), age(a) {
    }

    void showInfo() {
        cout << name << " " << age << endl;
    }
};
```

这句：

```cpp
Student(string n, int a) : name(n), age(a) {
}
```

表示创建对象时直接把 `name` 初始化为 `n`，把 `age` 初始化为 `a`。

它比在函数体里面赋值更规范。

---

## 十、析构函数

析构函数是在对象销毁时自动调用的函数。

语法：

```cpp
~类名() {
}
```

例如：

```cpp
class Student {
public:
    Student() {
        cout << "构造函数调用" << endl;
    }

    ~Student() {
        cout << "析构函数调用" << endl;
    }
};
```

使用：

```cpp
int main() {
    Student s;
    return 0;
}
```

输出大致是：

```text
构造函数调用
析构函数调用
```

构造函数在对象创建时调用，析构函数在对象生命周期结束时调用。

---

## 十一、成员函数写在类外

成员函数可以写在类里面，也可以只在类里面声明，然后在类外定义。

```cpp
class Student {
private:
    string name;
    int age;

public:
    Student(string n, int a);
    void showInfo();
};

Student::Student(string n, int a) : name(n), age(a) {
}

void Student::showInfo() {
    cout << name << " " << age << endl;
}
```

这里的：

```cpp
Student::showInfo
```

意思是：`showInfo` 是 `Student` 类的成员函数。

`::` 叫做**作用域解析运算符**。

---

## 十二、this 指针

在类的成员函数里面，`this` 表示当前对象的地址。

例如：

```cpp
class Student {
private:
    string name;
    int age;

public:
    Student(string name, int age) {
        this->name = name;
        this->age = age;
    }

    void showInfo() {
        cout << this->name << " " << this->age << endl;
    }
};
```

为什么要用 `this`？

因为参数名和成员变量名一样：

```cpp
Student(string name, int age)
```

这时候：

```cpp
this->name
```

表示成员变量 `name`。

右边的：

```cpp
name
```

表示参数 `name`。

所以：

```cpp
this->name = name;
```

意思是：把参数 `name` 的值赋给当前对象的成员变量 `name`。

---

## 十三、const 成员函数

如果一个成员函数不会修改对象的数据，可以写成 `const` 成员函数。

```cpp
class Student {
private:
    string name;
    int age;

public:
    Student(string n, int a) : name(n), age(a) {
    }

    void showInfo() const {
        cout << name << " " << age << endl;
    }
};
```

这里：

```cpp
void showInfo() const
```

表示这个函数不会修改成员变量。

下面这样是不允许的：

```cpp
void showInfo() const {
    age = 20; // 错误，const 成员函数不能修改普通成员变量
}
```

这是一种很好的编程习惯。

---

## 十四、对象数组

类也可以创建数组。

```cpp
class Student {
public:
    string name;
    int age;

    void showInfo() {
        cout << name << " " << age << endl;
    }
};

int main() {
    Student arr[3];

    arr[0].name = "张三";
    arr[0].age = 18;

    arr[1].name = "李四";
    arr[1].age = 19;

    arr[2].name = "王五";
    arr[2].age = 20;

    for (int i = 0; i < 3; i++) {
        arr[i].showInfo();
    }

    return 0;
}
```

如果类有带参数构造函数，可以这样初始化：

```cpp
class Student {
public:
    string name;
    int age;

    Student(string n, int a) : name(n), age(a) {
    }

    void showInfo() {
        cout << name << " " << age << endl;
    }
};

int main() {
    Student arr[3] = {
        Student("张三", 18),
        Student("李四", 19),
        Student("王五", 20)
    };

    for (int i = 0; i < 3; i++) {
        arr[i].showInfo();
    }

    return 0;
}
```

---

## 十五、对象指针

可以用指针指向对象。

```cpp
Student s("赵六", 21);

Student* p = &s;

p->showInfo();
```

这里：

```cpp
p->showInfo();
```

等价于：

```cpp
(*p).showInfo();
```

对象用点：

```cpp
s.showInfo();
```

对象指针用箭头：

```cpp
p->showInfo();
```

---

## 十六、new 创建对象

可以用 `new` 在堆区创建对象。

```cpp
Student* p = new Student("小明", 18);

p->showInfo();

delete p;
```

注意：

```cpp
new
```

创建对象后，要用：

```cpp
delete
```

释放对象。

不过现代 C++ 更推荐使用智能指针，比如 `unique_ptr`，初学可以先知道 `new/delete` 的基本形式。

---

## 十七、静态成员变量

`static` 成员变量属于类本身，不属于某一个对象。

比如统计学生数量：

```cpp
class Student {
public:
    static int count;

    Student() {
        count++;
    }
};

int Student::count = 0;
```

使用：

```cpp
int main() {
    Student s1;
    Student s2;
    Student s3;

    cout << Student::count << endl;

    return 0;
}
```

输出：

```text
3
```

注意静态成员变量需要在类外定义：

```cpp
int Student::count = 0;
```

---

## 十八、静态成员函数

静态成员函数也属于类本身，可以不用对象调用。

```cpp
class Student {
private:
    static int count;

public:
    Student() {
        count++;
    }

    static int getCount() {
        return count;
    }
};

int Student::count = 0;
```

使用：

```cpp
int main() {
    Student s1;
    Student s2;

    cout << Student::getCount() << endl;

    return 0;
}
```

静态成员函数只能直接访问静态成员，不能直接访问普通成员变量。

---

## 十九、友元函数

有时候类外的函数需要访问类的私有成员，可以使用 `friend`。

```cpp
class Student {
private:
    string name;
    int age;

public:
    Student(string n, int a) : name(n), age(a) {
    }

    friend void showStudent(Student s);
};

void showStudent(Student s) {
    cout << s.name << " " << s.age << endl;
}
```

`showStudent` 不是类的成员函数，但因为它是 `friend`，所以可以访问 `private` 成员。

不过友元会破坏封装，不能滥用。

---

## 二十、类的三大特性

C++ 面向对象有三大核心特性：

```text
封装
继承
多态
```

这一节主要讲的是类的基础，重点是封装。

### 1. 封装

把数据和操作数据的函数放到一起，并隐藏内部细节。

例如：

```cpp
class BankAccount {
private:
    double balance;

public:
    BankAccount(double b) : balance(b) {
    }

    void deposit(double money) {
        if (money > 0) {
            balance += money;
        }
    }

    bool withdraw(double money) {
        if (money > 0 && money <= balance) {
            balance -= money;
            return true;
        }
        return false;
    }

    double getBalance() const {
        return balance;
    }
};
```

这里 `balance` 是私有的，外部不能直接改余额，只能通过 `deposit` 和 `withdraw` 操作。

这就防止了：

```cpp
account.balance = -999999;
```

这种危险操作。

---

## 二十一、完整小案例：学生管理类

下面这个例子比较完整，建议你认真看。

```cpp
#include <iostream>
#include <string>
using namespace std;

class Student {
private:
    string name;
    int age;
    double score;

public:
    Student(string n, int a, double s) : name(n), age(a), score(s) {
    }

    void setAge(int a) {
        if (a >= 0 && a <= 150) {
            age = a;
        } else {
            cout << "年龄不合法" << endl;
        }
    }

    void setScore(double s) {
        if (s >= 0 && s <= 100) {
            score = s;
        } else {
            cout << "成绩不合法" << endl;
        }
    }

    string getName() const {
        return name;
    }

    int getAge() const {
        return age;
    }

    double getScore() const {
        return score;
    }

    void showInfo() const {
        cout << "姓名：" << name << endl;
        cout << "年龄：" << age << endl;
        cout << "成绩：" << score << endl;
    }
};

int main() {
    Student s("张三", 18, 92.5);

    s.showInfo();

    s.setAge(19);
    s.setScore(96);

    cout << "修改后：" << endl;
    s.showInfo();

    return 0;
}
```

这个例子里面用到了：

```text
类
对象
private
public
构造函数
初始化列表
setter
getter
const 成员函数
封装
```

这是学习 C++ 类的一个很标准的入门模型。

---

## 二十二、常见错误

### 1. 忘记类后面的分号

错误：

```cpp
class Student {
public:
    int age;
}
```

正确：

```cpp
class Student {
public:
    int age;
};
```

---

### 2. private 成员在类外直接访问

错误：

```cpp
class Student {
private:
    int age;
};

int main() {
    Student s;
    s.age = 18;
}
```

正确：

```cpp
class Student {
private:
    int age;

public:
    void setAge(int a) {
        age = a;
    }
};
```

---

### 3. 构造函数写了返回值

错误：

```cpp
class Student {
public:
    void Student() {
    }
};
```

正确：

```cpp
class Student {
public:
    Student() {
    }
};
```

构造函数没有返回值，连 `void` 也不能写。

---

### 4. 类名和构造函数名不一致

错误：

```cpp
class Student {
public:
    Person() {
    }
};
```

正确：

```cpp
class Student {
public:
    Student() {
    }
};
```

---

### 5. 对象和对象指针访问方式混淆

对象用：

```cpp
s.showInfo();
```

指针用：

```cpp
p->showInfo();
```

---

## 二十三、学习路线建议

你可以按这个顺序学：

```text
1. 类和对象的基本概念
2. 成员变量和成员函数
3. public/private
4. 构造函数和析构函数
5. this 指针
6. const 成员函数
7. 静态成员
8. 友元
9. 继承
10. 多态
```

其中前 7 个是类的基础，后面的继承和多态是进阶重点。

---

## 二十四、练习题

你可以自己尝试写这几个类。

### 练习 1：Person 类

要求：

```text
属性：
name
age

函数：
setName
setAge
getName
getAge
showInfo
```

要求 `age` 不能小于 0。

---

### 练习 2：Rectangle 类

要求：

```text
属性：
width
height

函数：
setWidth
setHeight
getArea
getPerimeter
```

面积：

```cpp
width * height
```

周长：

```cpp
2 * (width + height)
```

---

### 练习 3：BankAccount 类

要求：

```text
属性：
owner
balance

函数：
deposit
withdraw
getBalance
showInfo
```

要求：

```text
存款金额必须大于 0
取款金额必须大于 0
取款金额不能超过余额
balance 不能被外部直接修改
```

---

## 二十五、你需要记住的核心句子

C++ 中的类就是一种自定义类型，用来把数据和函数封装在一起。

对象是类创建出来的具体实例。

`public` 允许类外访问，`private` 禁止类外直接访问。

构造函数在对象创建时自动调用，析构函数在对象销毁时自动调用。

封装的核心思想是：数据尽量私有，操作通过公开函数完成。

最常见的类写法大概是：

```cpp
class 类名 {
private:
    成员变量;

public:
    构造函数;
    setter函数;
    getter函数;
    其他成员函数;
};
```

如果你刚开始学，先把下面这个模板背熟：

```cpp
class Student {
private:
    string name;
    int age;

public:
    Student(string n, int a) : name(n), age(a) {
    }

    void setAge(int a) {
        if (a >= 0) {
            age = a;
        }
    }

    int getAge() const {
        return age;
    }

    void showInfo() const {
        cout << name << " " << age << endl;
    }
};
```

掌握这个模板之后，C++ 类的基础部分就会顺很多。