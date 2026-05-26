你现在已经学完：

```text
类和对象
封装
继承
```

下一步应该学习：

```text
多态
```

这是 C++ 面向对象最核心的一部分。可以说，**继承让类之间有关系，多态让这些关系真正有用起来**。

---

**一、什么是多态**

多态，字面意思是：

```text
多种形态
```

在 C++ 中，多态指的是：

```text
同一个函数调用，因为对象类型不同，产生不同的行为。
```

比如：

```text
动物会叫
狗会汪汪叫
猫会喵喵叫
鸟会叽叽叫
```

它们都有一个共同动作：

```cpp
speak()
```

但是不同动物的 `speak()` 行为不同。

这就是多态。

---

**二、没有多态时的问题**

先看普通继承：

```cpp
#include <iostream>
using namespace std;

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

int main() {
    Dog d;
    d.speak();

    return 0;
}
```

输出：

```text
狗在汪汪叫
```

这没问题。

但是如果这样写：

```cpp
Animal* p = &d;
p->speak();
```

完整代码：

```cpp
#include <iostream>
using namespace std;

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

int main() {
    Dog d;

    Animal* p = &d;
    p->speak();

    return 0;
}
```

输出是：

```text
动物在叫
```

为什么？

因为 `p` 的类型是：

```cpp
Animal*
```

编译器只看指针类型，于是调用了 `Animal` 的 `speak()`。

这还不是真正的多态。

---

**三、使用 virtual 实现多态**

想让 C++ 根据实际对象类型调用函数，需要在父类函数前加：

```cpp
virtual
```

例如：

```cpp
class Animal {
public:
    virtual void speak() {
        cout << "动物在叫" << endl;
    }
};
```

完整代码：

```cpp
#include <iostream>
using namespace std;

class Animal {
public:
    virtual void speak() {
        cout << "动物在叫" << endl;
    }
};

class Dog : public Animal {
public:
    void speak() {
        cout << "狗在汪汪叫" << endl;
    }
};

int main() {
    Dog d;

    Animal* p = &d;
    p->speak();

    return 0;
}
```

输出变成：

```text
狗在汪汪叫
```

这就是多态。

---

**四、多态成立的条件**

C++ 多态通常需要三个条件：

```text
1. 有继承关系
2. 父类中有虚函数 virtual
3. 使用父类指针或父类引用指向子类对象
```

例如：

```cpp
Animal* p = &dog;
p->speak();
```

或者：

```cpp
Animal& ref = dog;
ref.speak();
```

注意：如果你直接用子类对象调用，不算典型多态。

```cpp
Dog d;
d.speak();
```

这只是普通函数调用。

真正体现多态的是：

```cpp
Animal* p = &d;
p->speak();
```

---

**五、父类指针指向子类对象**

这是多态中非常重要的语法：

```cpp
Dog d;
Animal* p = &d;
```

为什么可以这样？

因为：

```text
Dog 是一种 Animal
```

所以父类指针可以指向子类对象。

再比如：

```cpp
Cat c;
Animal* p = &c;
```

因为：

```text
Cat 也是一种 Animal
```

这样我们就可以写统一代码：

```cpp
void makeAnimalSpeak(Animal* animal) {
    animal->speak();
}
```

然后传入不同对象：

```cpp
Dog dog;
Cat cat;

makeAnimalSpeak(&dog);
makeAnimalSpeak(&cat);
```

同一个函数：

```cpp
makeAnimalSpeak
```

会根据实际传入的对象，调用不同版本的 `speak()`。

---

**六、多态完整例子**

```cpp
#include <iostream>
using namespace std;

class Animal {
public:
    virtual void speak() {
        cout << "动物在叫" << endl;
    }
};

class Dog : public Animal {
public:
    void speak() {
        cout << "狗在汪汪叫" << endl;
    }
};

class Cat : public Animal {
public:
    void speak() {
        cout << "猫在喵喵叫" << endl;
    }
};

class Bird : public Animal {
public:
    void speak() {
        cout << "鸟在叽叽叫" << endl;
    }
};

void makeSpeak(Animal* animal) {
    animal->speak();
}

int main() {
    Dog dog;
    Cat cat;
    Bird bird;

    makeSpeak(&dog);
    makeSpeak(&cat);
    makeSpeak(&bird);

    return 0;
}
```

输出：

```text
狗在汪汪叫
猫在喵喵叫
鸟在叽叽叫
```

你看：

```cpp
void makeSpeak(Animal* animal)
```

这个函数只知道自己拿到的是一个 `Animal*`。

但实际传进去的是：

```text
Dog
Cat
Bird
```

所以调用结果不同。

这就是多态的力量。

---

**七、override 关键字**

在子类重写父类虚函数时，建议加上：

```cpp
override
```

例如：

```cpp
class Dog : public Animal {
public:
    void speak() override {
        cout << "狗在汪汪叫" << endl;
    }
};
```

`override` 的作用是告诉编译器：

```text
我这里是在重写父类的虚函数。
```

如果你写错了函数名或参数，编译器会报错。

例如：

```cpp
class Dog : public Animal {
public:
    void speek() override {
        cout << "狗在汪汪叫" << endl;
    }
};
```

这里 `speek` 拼错了，编译器会发现错误。

所以建议以后写虚函数重写时都加 `override`。

---

**八、虚析构函数**

这是 C++ 多态中非常重要的知识点。

如果父类中有虚函数，并且你可能通过父类指针删除子类对象，那么父类析构函数应该写成虚析构函数。

错误示例：

```cpp
class Animal {
public:
    ~Animal() {
        cout << "Animal 析构" << endl;
    }
};
```

如果这样：

```cpp
Animal* p = new Dog;
delete p;
```

可能只调用父类析构函数，不调用子类析构函数，造成资源释放不完整。

正确写法：

```cpp
class Animal {
public:
    virtual ~Animal() {
        cout << "Animal 析构" << endl;
    }
};
```

完整例子：

```cpp
#include <iostream>
using namespace std;

class Animal {
public:
    virtual void speak() {
        cout << "动物在叫" << endl;
    }

    virtual ~Animal() {
        cout << "Animal 析构" << endl;
    }
};

class Dog : public Animal {
public:
    void speak() override {
        cout << "狗在汪汪叫" << endl;
    }

    ~Dog() {
        cout << "Dog 析构" << endl;
    }
};

int main() {
    Animal* p = new Dog;

    p->speak();

    delete p;

    return 0;
}
```

输出：

```text
狗在汪汪叫
Dog 析构
Animal 析构
```

所以你可以记住一句话：

```text
如果一个类要作为父类使用，并且里面有 virtual 函数，析构函数通常也应该是 virtual。
```

---

**九、纯虚函数**

有些父类只是用来规定接口，本身不应该提供具体实现。

比如：

```text
图形 Shape
```

你不能直接说“图形的面积是多少”。

但是：

```text
圆 Circle 有面积
矩形 Rectangle 有面积
三角形 Triangle 有面积
```

所以 `Shape` 可以规定：

```cpp
getArea()
```

但不实现它。

这时候使用纯虚函数。

语法：

```cpp
virtual 返回类型 函数名(参数) = 0;
```

例如：

```cpp
class Shape {
public:
    virtual double getArea() = 0;
};
```

这个：

```cpp
= 0
```

表示纯虚函数。

---

**十、抽象类**

含有纯虚函数的类叫做抽象类。

抽象类不能创建对象。

例如：

```cpp
class Shape {
public:
    virtual double getArea() = 0;
};
```

下面这样错误：

```cpp
Shape s; // 错误，抽象类不能实例化
```

但是可以用父类指针：

```cpp
Shape* p;
```

然后指向子类对象。

---

**十一、子类必须实现纯虚函数**

如果子类继承抽象类，就必须实现纯虚函数，否则子类也会变成抽象类。

```cpp
class Shape {
public:
    virtual double getArea() = 0;
};

class Circle : public Shape {
private:
    double radius;

public:
    Circle(double r) : radius(r) {
    }

    double getArea() override {
        return 3.14 * radius * radius;
    }
};
```

这里 `Circle` 实现了 `getArea()`，所以可以创建对象。

---

**十二、抽象类完整例子**

```cpp
#include <iostream>
using namespace std;

class Shape {
public:
    virtual double getArea() = 0;

    virtual ~Shape() {
    }
};

class Circle : public Shape {
private:
    double radius;

public:
    Circle(double r) : radius(r) {
    }

    double getArea() override {
        return 3.14 * radius * radius;
    }
};

class Rectangle : public Shape {
private:
    double width;
    double height;

public:
    Rectangle(double w, double h) : width(w), height(h) {
    }

    double getArea() override {
        return width * height;
    }
};

void printArea(Shape* shape) {
    cout << "面积：" << shape->getArea() << endl;
}

int main() {
    Circle c(3);
    Rectangle r(4, 5);

    printArea(&c);
    printArea(&r);

    return 0;
}
```

输出：

```text
面积：28.26
面积：20
```

这里：

```cpp
Shape
```

是抽象父类。

```cpp
Circle
Rectangle
```

是具体子类。

```cpp
printArea(Shape* shape)
```

通过父类指针接收不同图形对象。

这就是典型多态设计。

---

**十三、多态的本质理解**

你可以这样理解：

没有多态时：

```cpp
Dog dog;
dog.speak();

Cat cat;
cat.speak();
```

每种类型分别处理。

有多态后：

```cpp
void makeSpeak(Animal* animal) {
    animal->speak();
}
```

一套代码处理所有动物。

以后新增一个类：

```cpp
class Sheep : public Animal {
public:
    void speak() override {
        cout << "羊在咩咩叫" << endl;
    }
};
```

原来的 `makeSpeak()` 不需要修改：

```cpp
Sheep sheep;
makeSpeak(&sheep);
```

这就是多态的巨大价值：

```text
代码更通用
扩展更方便
减少 if-else
```

---

**十四、没有多态时容易写出大量 if-else**

比如不用多态，你可能写：

```cpp
void speak(string type) {
    if (type == "dog") {
        cout << "狗叫" << endl;
    } else if (type == "cat") {
        cout << "猫叫" << endl;
    } else if (type == "bird") {
        cout << "鸟叫" << endl;
    }
}
```

以后新增动物，就要改这个函数。

用多态后：

```cpp
class Animal {
public:
    virtual void speak() = 0;
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
```

新增动物只需要新增类，不用修改原来的逻辑函数。

---

**十五、引用也可以实现多态**

除了指针，引用也可以实现多态。

```cpp
void makeSpeak(Animal& animal) {
    animal.speak();
}
```

使用：

```cpp
Dog dog;
Cat cat;

makeSpeak(dog);
makeSpeak(cat);
```

完整例子：

```cpp
#include <iostream>
using namespace std;

class Animal {
public:
    virtual void speak() {
        cout << "动物在叫" << endl;
    }

    virtual ~Animal() {
    }
};

class Dog : public Animal {
public:
    void speak() override {
        cout << "狗在汪汪叫" << endl;
    }
};

class Cat : public Animal {
public:
    void speak() override {
        cout << "猫在喵喵叫" << endl;
    }
};

void makeSpeak(Animal& animal) {
    animal.speak();
}

int main() {
    Dog dog;
    Cat cat;

    makeSpeak(dog);
    makeSpeak(cat);

    return 0;
}
```

---

**十六、静态多态和动态多态**

C++ 中多态大致可以分两类：

```text
静态多态
动态多态
```

### 静态多态

编译期间确定调用哪个函数。

例如函数重载：

```cpp
void print(int x) {
    cout << x << endl;
}

void print(string s) {
    cout << s << endl;
}
```

调用：

```cpp
print(10);
print("hello");
```

编译器在编译时就知道调用哪个版本。

这叫静态多态。

---

### 动态多态

运行期间才确定调用哪个函数。

例如：

```cpp
Animal* p = &dog;
p->speak();
```

只有程序运行时，才根据 `p` 实际指向的对象决定调用 `Dog::speak()`。

这叫动态多态。

你现在学习的 `virtual`，主要就是动态多态。

---

**十七、virtual 的简单原理**

初学阶段不用深入底层，但可以简单理解：

只要类里有虚函数，C++ 编译器会帮这个类维护一张“虚函数表”。

对象内部会有某种方式指向这张表。

当你调用：

```cpp
p->speak();
```

程序会在运行时查看实际对象是谁，然后调用对应版本的函数。

所以 `virtual` 会带来一点点额外开销，但换来的是更灵活的代码结构。

---

**十八、多态常见错误**

### 1. 父类函数忘记加 virtual

错误：

```cpp
class Animal {
public:
    void speak() {
        cout << "动物叫" << endl;
    }
};
```

这样：

```cpp
Animal* p = new Dog;
p->speak();
```

会调用父类版本。

正确：

```cpp
class Animal {
public:
    virtual void speak() {
        cout << "动物叫" << endl;
    }
};
```

---

### 2. 子类函数参数不一致

父类：

```cpp
virtual void speak() {
}
```

子类错误写法：

```cpp
void speak(int x) {
}
```

这不是重写，而是另一个函数。

推荐加：

```cpp
override
```

这样编译器能帮你检查。

---

### 3. 抽象类不能创建对象

错误：

```cpp
class Shape {
public:
    virtual double getArea() = 0;
};

Shape s;
```

正确：

```cpp
Shape* p;
Circle c(3);
p = &c;
```

---

### 4. 父类析构函数忘记 virtual

如果使用：

```cpp
Animal* p = new Dog;
delete p;
```

父类析构函数应该是虚函数：

```cpp
virtual ~Animal() {
}
```

---

**十九、学习多态时要记住的模板**

模板 1：普通虚函数

```cpp
class Animal {
public:
    virtual void speak() {
        cout << "动物叫" << endl;
    }

    virtual ~Animal() {
    }
};

class Dog : public Animal {
public:
    void speak() override {
        cout << "狗叫" << endl;
    }
};
```

模板 2：纯虚函数 / 抽象类

```cpp
class Shape {
public:
    virtual double getArea() = 0;

    virtual ~Shape() {
    }
};

class Circle : public Shape {
private:
    double radius;

public:
    Circle(double r) : radius(r) {
    }

    double getArea() override {
        return 3.14 * radius * radius;
    }
};
```

模板 3：用父类指针调用

```cpp
Shape* p = &circle;
cout << p->getArea() << endl;
```

模板 4：用父类引用调用

```cpp
void printArea(Shape& shape) {
    cout << shape.getArea() << endl;
}
```

---

**二十、练习题**

### 练习 1：动物叫声

写一个抽象类 `Animal`：

```text
纯虚函数：
speak()
```

写三个子类：

```text
Dog
Cat
Bird
```

分别输出：

```text
狗在汪汪叫
猫在喵喵叫
鸟在叽叽叫
```

写一个函数：

```cpp
void makeSpeak(Animal& animal)
```

让它接收不同动物并调用 `speak()`。

---

### 练习 2：图形面积

写一个抽象类 `Shape`：

```text
纯虚函数：
getArea()
```

写两个子类：

```text
Circle
Rectangle
```

要求：

```text
Circle 根据半径计算面积
Rectangle 根据宽和高计算面积
```

写一个函数：

```cpp
void printArea(Shape& shape)
```

传入不同图形，输出面积。

---

### 练习 3：员工工资系统

写一个抽象类 `Employee`：

```text
属性：
name

纯虚函数：
calculateSalary()
```

写两个子类：

```text
FullTimeEmployee
PartTimeEmployee
```

全职员工：

```text
固定月薪
```

兼职员工：

```text
小时工资 * 工作小时数
```

使用多态输出不同员工的工资。

这是一个非常经典的多态练习。

---

**二十一、你现在的阶段总结**

你已经学到了 C++ 面向对象的核心区：

```text
类：定义对象的结构
封装：保护数据
继承：复用和扩展父类
多态：同一接口，不同行为
```

这四个放在一起，就构成了 C++ OOP 的基础能力。

接下来建议学习顺序是：

```text
1. 多态练习
2. 运算符重载
3. 拷贝构造函数
4. 深拷贝和浅拷贝
5. 静态成员
6. 模板
7. STL
```

你下一步真正应该学的是：

```text
拷贝构造函数、赋值运算符、析构函数
```

也就是 C++ 中非常重要的“三/五法则”基础。它和对象生命周期、内存管理关系很大，是从“会写类”进入“写得安全”的关键。