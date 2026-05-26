既然你已经学会了 C++ 的“类”，下一步最自然、最重要的内容是：

**继承**

然后是：

**多态**

这两部分和“类”放在一起，就是 C++ 面向对象编程的核心：

```text
类和对象
封装
继承
多态
```

你现在已经学了类和封装，所以下一步就应该学习**继承**。我建议学习顺序是：

```text
1. 继承
2. 派生类和基类
3. 继承方式：public / protected / private
4. 子类对象的构造和析构顺序
5. 子类重写父类函数
6. 多态
7. 虚函数 virtual
8. 纯虚函数和抽象类
```

我们今天先重点学：**继承**。

---

**一、为什么需要继承**

假设你要写几个类：

```text
学生 Student
老师 Teacher
管理员 Manager
```

它们都有一些共同属性：

```text
姓名 name
年龄 age
性别 gender
```

也都有一些共同函数：

```text
showInfo()
```

如果不用继承，你可能会这样写：

```cpp
class Student {
private:
    string name;
    int age;

public:
    void showInfo() {
        cout << name << " " << age << endl;
    }
};

class Teacher {
private:
    string name;
    int age;

public:
    void showInfo() {
        cout << name << " " << age << endl;
    }
};
```

你会发现很多代码重复了。

继承就是为了解决这种问题。

我们可以把共同内容提取到一个父类中：

```cpp
class Person {
protected:
    string name;
    int age;

public:
    Person(string n, int a) : name(n), age(a) {
    }

    void showInfo() {
        cout << "姓名：" << name << endl;
        cout << "年龄：" << age << endl;
    }
};
```

然后让 `Student` 和 `Teacher` 继承它：

```cpp
class Student : public Person {
public:
    Student(string n, int a) : Person(n, a) {
    }
};

class Teacher : public Person {
public:
    Teacher(string n, int a) : Person(n, a) {
    }
};
```

这样 `Student` 和 `Teacher` 就自动拥有了 `Person` 的成员。

---

**二、什么是继承**

继承的意思是：一个类可以获得另一个类的成员变量和成员函数。

语法：

```cpp
class 子类 : 继承方式 父类 {
};
```

例如：

```cpp
class Student : public Person {
};
```

这里：

```text
Person 是父类，也叫基类
Student 是子类，也叫派生类
```

也就是说：

```text
Student 继承了 Person
Student 是一种 Person
```

这句话很重要。

如果一个学生类继承人类，那么可以理解为：

```text
学生是人
```

所以继承适合表达 “is-a” 关系。

例如：

```text
学生 是 人
老师 是 人
猫 是 动物
汽车 是 交通工具
圆 是 图形
```

这些都适合用继承。

但下面这种不适合：

```text
汽车 是 发动机
学生 是 书包
电脑 是 键盘
```

这不是继承关系，而是“拥有”关系。

---

**三、最简单的继承例子**

```cpp
#include <iostream>
#include <string>
using namespace std;

class Person {
public:
    string name;
    int age;

    void showInfo() {
        cout << "姓名：" << name << endl;
        cout << "年龄：" << age << endl;
    }
};

class Student : public Person {
public:
    int score;

    void showScore() {
        cout << "成绩：" << score << endl;
    }
};

int main() {
    Student s;

    s.name = "张三";
    s.age = 18;
    s.score = 95;

    s.showInfo();
    s.showScore();

    return 0;
}
```

输出：

```text
姓名：张三
年龄：18
成绩：95
```

这里 `Student` 类里面没有写 `name`、`age`、`showInfo()`，但是它可以使用。

因为它继承了 `Person`。

---

**四、继承中的成员访问**

你之前学过类的访问权限：

```text
public
private
protected
```

现在继承里面要重点理解 `protected`。

### public

`public` 成员：

```text
类内可以访问
子类可以访问
类外可以访问
```

### private

`private` 成员：

```text
类内可以访问
子类不能直接访问
类外不能访问
```

### protected

`protected` 成员：

```text
类内可以访问
子类可以访问
类外不能访问
```

`protected` 就是专门给继承准备的。

看例子：

```cpp
class Person {
protected:
    string name;
    int age;
};

class Student : public Person {
public:
    void setInfo(string n, int a) {
        name = n;
        age = a;
    }
};
```

这里 `Student` 可以访问 `Person` 里面的 `protected` 成员。

但是类外不能这样访问：

```cpp
Student s;
s.name = "张三"; // 错误
```

因为 `name` 是 `protected`，不是 `public`。

---

**五、private 成员不能被子类直接访问**

很多初学者容易误解：以为继承之后，子类就可以访问父类所有内容。

不是。

例如：

```cpp
class Person {
private:
    string name;
};

class Student : public Person {
public:
    void test() {
        name = "张三"; // 错误
    }
};
```

虽然 `Student` 继承了 `Person`，但 `name` 是 `private`，子类不能直接访问。

正确做法是通过父类提供的 `public` 或 `protected` 函数访问。

```cpp
class Person {
private:
    string name;

public:
    void setName(string n) {
        name = n;
    }

    string getName() {
        return name;
    }
};

class Student : public Person {
public:
    void test() {
        setName("张三");
    }
};
```

---

**六、继承方式**

C++ 有三种继承方式：

```cpp
public 继承
protected 继承
private 继承
```

语法：

```cpp
class Student : public Person {
};
```

或者：

```cpp
class Student : protected Person {
};
```

或者：

```cpp
class Student : private Person {
};
```

初学阶段，最常用的是：

```cpp
public 继承
```

你可以先重点掌握 public 继承。

### public 继承

父类成员权限在子类中基本保持不变：

```text
父类 public    -> 子类 public
父类 protected -> 子类 protected
父类 private   -> 子类不可直接访问
```

例如：

```cpp
class Person {
public:
    string name;

protected:
    int age;

private:
    double money;
};

class Student : public Person {
public:
    void test() {
        name = "张三"; // 可以
        age = 18;      // 可以
        // money = 100; // 错误
    }
};
```

---

**七、子类可以添加自己的成员**

继承不是简单复制父类。

子类可以拥有父类内容，也可以添加自己的新内容。

```cpp
class Person {
protected:
    string name;
    int age;

public:
    Person(string n, int a) : name(n), age(a) {
    }

    void showPerson() {
        cout << "姓名：" << name << endl;
        cout << "年龄：" << age << endl;
    }
};

class Student : public Person {
private:
    int score;

public:
    Student(string n, int a, int s) : Person(n, a), score(s) {
    }

    void showStudent() {
        showPerson();
        cout << "成绩：" << score << endl;
    }
};
```

使用：

```cpp
int main() {
    Student s("张三", 18, 95);
    s.showStudent();

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
Person(n, a)
```

表示调用父类构造函数。

```cpp
score(s)
```

表示初始化子类自己的成员。

---

**八、父类构造函数和子类构造函数**

继承中一个很重要的问题是：

创建子类对象时，父类部分也要先被创建。

例如：

```cpp
class Person {
public:
    Person() {
        cout << "Person 构造函数" << endl;
    }
};

class Student : public Person {
public:
    Student() {
        cout << "Student 构造函数" << endl;
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

输出：

```text
Person 构造函数
Student 构造函数
```

也就是说：

```text
先调用父类构造函数
再调用子类构造函数
```

因为学生首先也是一个人，得先把“人”的部分构造好，再构造“学生”自己的部分。

---

**九、析构函数调用顺序**

析构顺序和构造顺序相反。

```cpp
class Person {
public:
    Person() {
        cout << "Person 构造函数" << endl;
    }

    ~Person() {
        cout << "Person 析构函数" << endl;
    }
};

class Student : public Person {
public:
    Student() {
        cout << "Student 构造函数" << endl;
    }

    ~Student() {
        cout << "Student 析构函数" << endl;
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

输出：

```text
Person 构造函数
Student 构造函数
Student 析构函数
Person 析构函数
```

顺序是：

```text
构造：先父类，后子类
析构：先子类，后父类
```

这个顺序要记住。

---

**十、子类调用父类构造函数**

如果父类没有默认构造函数，子类必须显式调用父类构造函数。

例如：

```cpp
class Person {
protected:
    string name;
    int age;

public:
    Person(string n, int a) : name(n), age(a) {
    }
};
```

这个 `Person` 没有无参构造函数。

那么子类必须这样写：

```cpp
class Student : public Person {
private:
    int score;

public:
    Student(string n, int a, int s) : Person(n, a), score(s) {
    }
};
```

不能只写：

```cpp
Student(string n, int a, int s) {
    score = s;
}
```

因为父类的 `name` 和 `age` 不知道怎么初始化。

---

**十一、函数同名时怎么办**

如果父类和子类有同名函数，子类会隐藏父类的同名函数。

```cpp
class Person {
public:
    void showInfo() {
        cout << "Person 的 showInfo" << endl;
    }
};

class Student : public Person {
public:
    void showInfo() {
        cout << "Student 的 showInfo" << endl;
    }
};
```

使用：

```cpp
int main() {
    Student s;
    s.showInfo();

    return 0;
}
```

输出：

```text
Student 的 showInfo
```

因为子类有自己的 `showInfo()`。

如果你想调用父类的版本，可以写：

```cpp
s.Person::showInfo();
```

完整例子：

```cpp
int main() {
    Student s;

    s.showInfo();
    s.Person::showInfo();

    return 0;
}
```

输出：

```text
Student 的 showInfo
Person 的 showInfo
```

---

**十二、函数重写**

当子类重新定义父类中的函数时，这通常叫“重写”。

例如：

```cpp
class Animal {
public:
    void speak() {
        cout << "动物在叫" << endl;
    }
};

class Dog : public Animal {
public:
    void speak() {
        cout << "狗在汪汪叫" << endl;
    }
};
```

使用：

```cpp
Dog d;
d.speak();
```

输出：

```text
狗在汪汪叫
```

不过严格来说，在 C++ 中真正和多态相关的重写，通常要配合 `virtual` 虚函数。

这个就是你学完继承之后下一步要学的内容：**多态**。

---

**十三、继承的完整案例**

我们写一个稍微完整的例子：学校成员管理。

```cpp
#include <iostream>
#include <string>
using namespace std;

class Person {
protected:
    string name;
    int age;

public:
    Person(string n, int a) : name(n), age(a) {
    }

    void showBasicInfo() const {
        cout << "姓名：" << name << endl;
        cout << "年龄：" << age << endl;
    }
};

class Student : public Person {
private:
    int score;

public:
    Student(string n, int a, int s) : Person(n, a), score(s) {
    }

    void showInfo() const {
        cout << "学生信息：" << endl;
        showBasicInfo();
        cout << "成绩：" << score << endl;
    }
};

class Teacher : public Person {
private:
    string subject;

public:
    Teacher(string n, int a, string sub) : Person(n, a), subject(sub) {
    }

    void showInfo() const {
        cout << "老师信息：" << endl;
        showBasicInfo();
        cout << "科目：" << subject << endl;
    }
};

int main() {
    Student s("张三", 18, 95);
    Teacher t("李老师", 35, "C++");

    s.showInfo();
    cout << endl;
    t.showInfo();

    return 0;
}
```

这个程序中：

```text
Person 是父类
Student 是子类
Teacher 是子类
Student 和 Teacher 复用了 Person 的 name、age、showBasicInfo()
Student 添加了 score
Teacher 添加了 subject
```

这就是继承的价值：复用共同代码，同时保留各自特点。

---

**十四、继承适合什么时候用**

适合使用继承的情况：

```text
多个类有明显的共同特征
它们之间存在“是一个”的关系
子类确实可以看成父类的一种
```

例如：

```text
Dog 是 Animal
Cat 是 Animal
Student 是 Person
Teacher 是 Person
Circle 是 Shape
Rectangle 是 Shape
```

不适合继承的情况：

```text
Car 继承 Engine
Student 继承 Book
Computer 继承 Keyboard
```

因为汽车不是发动机，学生不是书，电脑不是键盘。

这种关系应该用成员变量表达：

```cpp
class Engine {
};

class Car {
private:
    Engine engine;
};
```

这叫组合。

继承表达：

```text
is-a
```

组合表达：

```text
has-a
```

这两个概念很重要。

---

**十五、继承和封装的关系**

你刚学完类，应该知道封装强调：

```text
数据私有，函数公开
```

继承中也一样，不建议把所有成员都写成 `public`。

更推荐：

```cpp
class Person {
protected:
    string name;
    int age;

public:
    Person(string n, int a) : name(n), age(a) {
    }

    string getName() const {
        return name;
    }

    int getAge() const {
        return age;
    }
};
```

如果父类成员只希望子类访问，就用 `protected`。

如果希望所有外部代码都能访问，就用 `public`。

如果只希望父类内部访问，就用 `private`。

---

**十六、继承的常见错误**

### 1. 忘记写 public 继承

如果你写：

```cpp
class Student : Person {
};
```

对于 `class` 来说，默认是 `private` 继承。

初学时建议明确写：

```cpp
class Student : public Person {
};
```

---

### 2. 子类直接访问父类 private 成员

错误：

```cpp
class Person {
private:
    string name;
};

class Student : public Person {
public:
    void test() {
        name = "张三";
    }
};
```

正确做法：

```cpp
class Person {
private:
    string name;

public:
    void setName(string n) {
        name = n;
    }
};

class Student : public Person {
public:
    void test() {
        setName("张三");
    }
};
```

---

### 3. 父类没有默认构造函数，子类忘记调用父类构造函数

错误：

```cpp
class Person {
public:
    Person(string n) {
    }
};

class Student : public Person {
public:
    Student() {
    }
};
```

正确：

```cpp
class Student : public Person {
public:
    Student() : Person("张三") {
    }
};
```

---

### 4. 以为继承就是复制代码

继承不是简单复制。

子类对象内部确实包含父类部分，但继承更重要的是表达类型关系。

```text
Student 是一种 Person
Dog 是一种 Animal
Circle 是一种 Shape
```

---

**十七、你现在应该重点掌握什么**

学完类之后，你下一步不用急着马上冲到复杂语法。

先把这几个点搞懂：

```text
1. 什么是父类、子类
2. 子类如何继承父类
3. public 继承怎么写
4. protected 的作用
5. 子类如何调用父类构造函数
6. 构造顺序：先父后子
7. 析构顺序：先子后父
8. 子类如何扩展父类
9. 子类如何重写父类函数
```

这些掌握以后，再学多态会非常顺。

---

**十八、练习题**

### 练习 1：Person 和 Student

写一个 `Person` 类：

```text
属性：
name
age

函数：
showInfo()
```

再写一个 `Student` 类继承 `Person`：

```text
新增属性：
score

函数：
showStudentInfo()
```

要求输出：

```text
姓名：张三
年龄：18
成绩：95
```

---

### 练习 2：Animal 和 Dog

写一个 `Animal` 类：

```text
函数：
speak()
```

再写一个 `Dog` 类继承 `Animal`：

```text
重写 speak()
```

让 `Dog` 的 `speak()` 输出：

```text
狗在汪汪叫
```

---

### 练习 3：Shape、Circle、Rectangle

写一个 `Shape` 类：

```text
属性：
name
```

写两个子类：

```text
Circle
Rectangle
```

`Circle` 有：

```text
radius
getArea()
```

`Rectangle` 有：

```text
width
height
getArea()
```

这是以后学习多态的经典铺垫。

---

**十九、下一阶段预告：多态**

等你学完继承之后，就要学多态。

多态解决的问题是：

```text
同一个函数调用，根据对象类型不同，表现出不同的行为。
```

例如：

```cpp
Animal* a;

Dog d;
Cat c;

a = &d;
a->speak(); // 狗叫

a = &c;
a->speak(); // 猫叫
```

要实现这种效果，就需要：

```cpp
virtual
```

也就是虚函数。

所以学习路线应该是：

```text
类
封装
继承
虚函数
多态
抽象类
接口思想
```

你现在的位置很好，正好进入 C++ 面向对象最核心的一段。下一步建议你先把“继承”练熟，然后我们再继续学“多态”。