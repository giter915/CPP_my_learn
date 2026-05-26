你现在已经学完：

```text
类和对象
继承
多态
拷贝和移动
运算符重载
模板
```

下一步非常适合学习：

```text
STL 标准模板库
```

STL 是 C++ 里最常用、最实用的一大块内容。学完它，你写 C++ 的效率会明显提高。

---

**一、什么是 STL**

STL 全称：

```text
Standard Template Library
标准模板库
```

它主要包含三大部分：

```text
容器：用来存数据
迭代器：用来访问数据
算法：用来处理数据
```

比如：

```cpp
vector<int> nums;
sort(nums.begin(), nums.end());
```

这里：

```text
vector 是容器
begin() 和 end() 返回迭代器
sort 是算法
```

---

**二、为什么要学 STL**

以前你如果想存一组数据，可能要写数组：

```cpp
int arr[100];
```

但数组有很多限制：

```text
大小固定
插入删除麻烦
容易越界
功能少
```

STL 提供了很多更好用的数据结构：

```cpp
vector<int> v;
list<int> lst;
map<string, int> mp;
set<int> st;
queue<int> q;
stack<int> s;
```

它们都是现成的，安全、强大、效率高。

---

**三、STL 常见容器**

常用容器大致分为几类：

```text
顺序容器：
vector
deque
list

关联容器：
set
map
multiset
multimap

无序关联容器：
unordered_set
unordered_map

容器适配器：
stack
queue
priority_queue

字符串：
string
```

初学建议重点掌握：

```text
string
vector
map
set
stack
queue
algorithm
```

---

**四、vector**

`vector` 是最常用的 STL 容器。

你可以把它理解成：

```text
可以自动扩容的数组
```

头文件：

```cpp
#include <vector>
```

基本用法：

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> v;

    v.push_back(10);
    v.push_back(20);
    v.push_back(30);

    for (int i = 0; i < v.size(); i++) {
        cout << v[i] << endl;
    }

    return 0;
}
```

输出：

```text
10
20
30
```

常用操作：

```cpp
v.push_back(x);    // 尾部添加
v.pop_back();      // 删除最后一个
v.size();          // 元素个数
v.empty();         // 是否为空
v.clear();         // 清空
v[i];              // 下标访问
v.at(i);           // 带越界检查的访问
v.front();         // 第一个元素
v.back();          // 最后一个元素
```

---

**五、vector 的遍历方式**

方式 1：下标遍历

```cpp
for (int i = 0; i < v.size(); i++) {
    cout << v[i] << endl;
}
```

方式 2：范围 for

```cpp
for (int x : v) {
    cout << x << endl;
}
```

方式 3：引用遍历，可以修改元素

```cpp
for (int& x : v) {
    x *= 2;
}
```

方式 4：const 引用，适合复杂对象

```cpp
for (const string& s : names) {
    cout << s << endl;
}
```

---

**六、vector 和对象**

你可以存自定义类对象。

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class Student {
private:
    string name;
    int score;

public:
    Student(string n, int s) : name(n), score(s) {
    }

    void show() const {
        cout << name << " " << score << endl;
    }
};

int main() {
    vector<Student> students;

    students.push_back(Student("张三", 90));
    students.push_back(Student("李四", 85));

    for (const Student& s : students) {
        s.show();
    }

    return 0;
}
```

---

**七、string**

`string` 也是 STL 里非常常用的类型。

头文件：

```cpp
#include <string>
```

基本用法：

```cpp
string s = "hello";

cout << s.size() << endl;
cout << s[0] << endl;
```

常用操作：

```cpp
s.size();              // 长度
s.empty();             // 是否为空
s += "world";          // 拼接
s.substr(pos, len);    // 截取子串
s.find("abc");         // 查找
s.insert(pos, str);    // 插入
s.erase(pos, len);     // 删除
```

例子：

```cpp
string s = "hello world";

cout << s.substr(0, 5) << endl;

int pos = s.find("world");
if (pos != string::npos) {
    cout << "找到了" << endl;
}
```

注意：

```cpp
string::npos
```

表示没找到。

---

**八、迭代器**

迭代器可以理解成：

```text
像指针一样的东西，用来访问容器元素
```

例如：

```cpp
vector<int> v = {10, 20, 30};

vector<int>::iterator it = v.begin();

cout << *it << endl;
```

输出：

```text
10
```

`begin()` 指向第一个元素。

`end()` 指向最后一个元素的后一个位置。

遍历：

```cpp
for (vector<int>::iterator it = v.begin(); it != v.end(); ++it) {
    cout << *it << endl;
}
```

现代 C++ 可以用 `auto`：

```cpp
for (auto it = v.begin(); it != v.end(); ++it) {
    cout << *it << endl;
}
```

---

**九、algorithm 算法库**

头文件：

```cpp
#include <algorithm>
```

常用算法：

```cpp
sort
reverse
find
count
max_element
min_element
```

例子：

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> v = {3, 1, 5, 2, 4};

    sort(v.begin(), v.end());

    for (int x : v) {
        cout << x << " ";
    }

    return 0;
}
```

输出：

```text
1 2 3 4 5
```

---

**十、自定义排序**

从大到小排序：

```cpp
sort(v.begin(), v.end(), greater<int>());
```

或者写 lambda：

```cpp
sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;
});
```

对学生按成绩排序：

```cpp
class Student {
public:
    string name;
    int score;
};

vector<Student> students = {
    {"张三", 90},
    {"李四", 85},
    {"王五", 95}
};

sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
    return a.score > b.score;
});
```

---

**十一、map**

`map` 用来存键值对。

你可以理解成：

```text
根据 key 找 value
```

比如：

```text
姓名 -> 分数
单词 -> 出现次数
商品名 -> 价格
```

头文件：

```cpp
#include <map>
```

基本用法：

```cpp
#include <iostream>
#include <map>
#include <string>
using namespace std;

int main() {
    map<string, int> scores;

    scores["张三"] = 90;
    scores["李四"] = 85;

    cout << scores["张三"] << endl;

    return 0;
}
```

遍历：

```cpp
for (const auto& pair : scores) {
    cout << pair.first << " " << pair.second << endl;
}
```

其中：

```cpp
pair.first
```

是 key。

```cpp
pair.second
```

是 value。

---

**十二、map 的特点**

`map` 的特点：

```text
key 唯一
按 key 自动排序
查找效率较高
```

例如：

```cpp
map<string, int> mp;
```

key 是 `string`，value 是 `int`。

如果你写：

```cpp
mp["apple"]++;
```

可以用来统计单词出现次数。

```cpp
vector<string> words = {"apple", "banana", "apple"};

map<string, int> count;

for (const string& word : words) {
    count[word]++;
}
```

---

**十三、unordered_map**

`unordered_map` 和 `map` 类似，也是键值对。

区别：

```text
map：按 key 排序
unordered_map：不排序，通常更快
```

头文件：

```cpp
#include <unordered_map>
```

用法：

```cpp
unordered_map<string, int> count;
count["apple"]++;
```

如果不需要排序，经常可以用 `unordered_map`。

---

**十四、set**

`set` 用来存不重复元素。

头文件：

```cpp
#include <set>
```

例子：

```cpp
#include <iostream>
#include <set>
using namespace std;

int main() {
    set<int> s;

    s.insert(3);
    s.insert(1);
    s.insert(3);
    s.insert(2);

    for (int x : s) {
        cout << x << " ";
    }

    return 0;
}
```

输出：

```text
1 2 3
```

特点：

```text
自动去重
自动排序
```

---

**十五、unordered_set**

`unordered_set`：

```text
自动去重
不排序
通常更快
```

头文件：

```cpp
#include <unordered_set>
```

用法：

```cpp
unordered_set<int> s;
s.insert(10);
s.insert(20);
```

---

**十六、stack**

`stack` 是栈。

特点：

```text
后进先出
Last In First Out
```

头文件：

```cpp
#include <stack>
```

常用操作：

```cpp
s.push(x);
s.pop();
s.top();
s.empty();
s.size();
```

例子：

```cpp
#include <iostream>
#include <stack>
using namespace std;

int main() {
    stack<int> s;

    s.push(10);
    s.push(20);
    s.push(30);

    while (!s.empty()) {
        cout << s.top() << endl;
        s.pop();
    }

    return 0;
}
```

输出：

```text
30
20
10
```

---

**十七、queue**

`queue` 是队列。

特点：

```text
先进先出
First In First Out
```

头文件：

```cpp
#include <queue>
```

常用操作：

```cpp
q.push(x);
q.pop();
q.front();
q.back();
q.empty();
q.size();
```

例子：

```cpp
queue<int> q;

q.push(10);
q.push(20);
q.push(30);

while (!q.empty()) {
    cout << q.front() << endl;
    q.pop();
}
```

输出：

```text
10
20
30
```

---

**十八、priority_queue**

`priority_queue` 是优先队列，默认是大根堆。

也就是说，每次取出来的是最大值。

```cpp
#include <iostream>
#include <queue>
using namespace std;

int main() {
    priority_queue<int> pq;

    pq.push(3);
    pq.push(1);
    pq.push(5);

    while (!pq.empty()) {
        cout << pq.top() << endl;
        pq.pop();
    }

    return 0;
}
```

输出：

```text
5
3
1
```

小根堆：

```cpp
priority_queue<int, vector<int>, greater<int>> pq;
```

---

**十九、deque 和 list**

### deque

`deque` 是双端队列。

可以在头尾快速插入删除。

```cpp
deque<int> d;

d.push_back(10);
d.push_front(20);
d.pop_back();
d.pop_front();
```

头文件：

```cpp
#include <deque>
```

---

### list

`list` 是双向链表。

适合频繁在中间插入删除。

```cpp
list<int> lst;

lst.push_back(10);
lst.push_front(20);
```

头文件：

```cpp
#include <list>
```

但 `list` 不支持下标访问：

```cpp
lst[0]; // 错误
```

---

**二十、容器怎么选**

初学可以这样记：

```text
大多数情况：vector
需要键值对：map / unordered_map
需要去重：set / unordered_set
后进先出：stack
先进先出：queue
总取最大/最小：priority_queue
频繁头尾操作：deque
频繁中间插入删除：list
```

真实开发里，`vector` 的使用频率非常高。

---

**二十一、完整案例：学生成绩管理**

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Student {
public:
    string name;
    int score;

    Student(string n, int s) : name(n), score(s) {
    }
};

int main() {
    vector<Student> students;

    students.push_back(Student("张三", 90));
    students.push_back(Student("李四", 85));
    students.push_back(Student("王五", 95));

    sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
        return a.score > b.score;
    });

    for (const Student& s : students) {
        cout << s.name << " " << s.score << endl;
    }

    return 0;
}
```

输出：

```text
王五 95
张三 90
李四 85
```

这个例子用到了：

```text
vector
对象
sort
lambda
范围 for
const 引用
```

---

**二十二、完整案例：统计单词出现次数**

```cpp
#include <iostream>
#include <map>
#include <string>
#include <vector>
using namespace std;

int main() {
    vector<string> words = {
        "apple", "banana", "apple", "orange", "banana", "apple"
    };

    map<string, int> count;

    for (const string& word : words) {
        count[word]++;
    }

    for (const auto& pair : count) {
        cout << pair.first << " : " << pair.second << endl;
    }

    return 0;
}
```

输出：

```text
apple : 3
banana : 2
orange : 1
```

---

**二十三、STL 学习重点**

这一章你重点掌握：

```text
1. vector 的使用
2. string 的常用操作
3. 迭代器 begin/end
4. sort 的使用
5. map 统计键值对
6. set 去重
7. stack 和 queue
8. priority_queue
9. 容器选择
```

最重要的是：

```text
vector + sort + map
```

它们在刷题和项目中都特别常用。

---

**二十四、练习题**

### 练习 1：vector 排序

输入 5 个整数，存入 `vector<int>`，然后升序输出。

要求使用：

```cpp
sort
```

---

### 练习 2：学生成绩排序

定义 `Student`：

```text
name
score
```

用 `vector<Student>` 存多个学生，按成绩从高到低排序。

---

### 练习 3：统计单词次数

给定：

```cpp
vector<string> words = {
    "cpp", "java", "cpp", "python", "cpp", "java"
};
```

使用 `map<string, int>` 统计每个单词出现次数。

---

### 练习 4：去重排序

输入一组整数：

```cpp
3 1 2 3 2 5
```

使用 `set<int>` 输出：

```cpp
1 2 3 5
```

---

### 练习 5：括号匹配

使用 `stack<char>` 判断字符串括号是否匹配。

例如：

```text
"(()())"  匹配
"(()"     不匹配
")("      不匹配
```

---

你下一步建议学习：

```text
lambda 表达式和 STL 算法
```

因为你现在已经接触了：

```cpp
sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
    return a.score > b.score;
});
```

这个 `[](...) { ... }` 就是 lambda。学会它之后，你使用 STL 算法会顺很多。