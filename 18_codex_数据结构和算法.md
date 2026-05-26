现在继续学习：

```text
数据结构与算法基础
```

你前面学的是 C++ 语言能力，现在开始进入“用 C++ 解决问题”的核心能力。

数据结构与算法主要回答两个问题：

```text
数据怎么存？
问题怎么解？
```

比如：

```text
学生成绩用 vector 存还是 map 存？
查找一个人用线性查找还是哈希表？
排序用什么算法？
括号匹配为什么用栈？
最短路径为什么用图？
```

---

**一、什么是数据结构**

数据结构就是：

```text
组织和存储数据的方式
```

常见数据结构：

```text
数组
链表
栈
队列
哈希表
树
堆
图
```

在 C++ STL 中，对应大概是：

```text
数组/动态数组：vector
链表：list
栈：stack
队列：queue
哈希表：unordered_map / unordered_set
树结构集合：map / set
堆：priority_queue
```

---

**二、什么是算法**

算法就是：

```text
解决问题的步骤
```

比如：

```text
查找最大值
排序
二分查找
递归
深度优先搜索
广度优先搜索
动态规划
```

同一个问题可以有很多算法，但效率不同。

---

**三、时间复杂度**

时间复杂度用来描述：

```text
数据量变大时，算法耗时增长得有多快
```

常见复杂度：

```text
O(1)        常数时间
O(log n)    对数时间
O(n)        线性时间
O(n log n)  常见高效排序
O(n^2)      双重循环
O(2^n)      指数级
```

---

**四、O(1)：常数时间**

不管数据量多大，操作时间基本不变。

例如访问 vector 某个下标：

```cpp
v[3]
```

不管 `v` 有 10 个元素还是 100 万个元素，访问下标 3 都很快。

这是：

```text
O(1)
```

---

**五、O(n)：线性时间**

需要看一遍所有元素。

例如找最大值：

```cpp
int maxValue(const std::vector<int>& nums) {
    int maxVal = nums[0];

    for (int x : nums) {
        if (x > maxVal) {
            maxVal = x;
        }
    }

    return maxVal;
}
```

如果有 `n` 个元素，最多要看 `n` 次。

所以是：

```text
O(n)
```

---

**六、O(n^2)：平方时间**

常见于双重循环。

```cpp
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
        // ...
    }
}
```

如果 `n = 1000`，大约执行：

```text
1000000 次
```

这就是：

```text
O(n^2)
```

---

**七、O(log n)：对数时间**

典型例子是二分查找。

每次把范围缩小一半：

```text
1000
500
250
125
...
```

数据量很大时也很快。

---

**八、数组 / vector**

`vector` 是最常用的数据结构之一。

特点：

```text
支持下标快速访问 O(1)
尾部插入较快
中间插入删除较慢
内存连续
```

适合：

```text
需要频繁按下标访问
大多数普通列表数据
排序
遍历
```

例子：

```cpp
std::vector<int> nums{1, 2, 3, 4};
nums.push_back(5);
std::cout << nums[0] << std::endl;
```

---

**九、链表 / list**

链表不连续存储，每个节点保存数据和指针。

STL 对应：

```cpp
std::list
```

特点：

```text
中间插入删除快
不支持下标访问
查找慢
```

适合：

```text
频繁在中间插入删除
不需要随机访问
```

但真实开发里，`vector` 用得更多。因为 `vector` 内存连续，缓存友好，实际性能经常很好。

---

**十、栈 stack**

栈的特点：

```text
后进先出
Last In First Out
```

就像一摞盘子，最后放上去的最先拿下来。

常用操作：

```cpp
push
pop
top
empty
```

例子：

```cpp
std::stack<int> s;

s.push(1);
s.push(2);
s.push(3);

while (!s.empty()) {
    std::cout << s.top() << std::endl;
    s.pop();
}
```

输出：

```text
3
2
1
```

常见应用：

```text
括号匹配
函数调用栈
表达式求值
撤销操作
深度优先搜索
```

---

**十一、括号匹配**

判断字符串括号是否匹配：

```cpp
bool isValid(const std::string& text) {
    std::stack<char> s;

    for (char ch : text) {
        if (ch == '(') {
            s.push(ch);
        } else if (ch == ')') {
            if (s.empty()) {
                return false;
            }

            s.pop();
        }
    }

    return s.empty();
}
```

测试：

```cpp
isValid("(()())") // true
isValid("(()")    // false
isValid(")(")     // false
```

---

**十二、队列 queue**

队列特点：

```text
先进先出
First In First Out
```

就像排队买票，先来先服务。

常用操作：

```cpp
push
pop
front
back
empty
```

例子：

```cpp
std::queue<int> q;

q.push(1);
q.push(2);
q.push(3);

while (!q.empty()) {
    std::cout << q.front() << std::endl;
    q.pop();
}
```

输出：

```text
1
2
3
```

常见应用：

```text
任务队列
消息队列
广度优先搜索 BFS
缓存处理
```

---

**十三、哈希表 unordered_map**

如果你想快速根据 key 查 value，用：

```cpp
std::unordered_map
```

比如统计单词次数：

```cpp
std::unordered_map<std::string, int> count;

for (const auto& word : words) {
    count[word]++;
}
```

特点：

```text
平均查找 O(1)
不排序
适合快速查询
```

---

**十四、map 和 unordered_map 的区别**

```text
map：
按 key 排序
底层通常是红黑树
查找 O(log n)

unordered_map：
不排序
底层哈希表
平均查找 O(1)
```

怎么选：

```text
需要按 key 有序：map
只想快速查找：unordered_map
```

---

**十五、set 和 unordered_set**

`set` 用来去重并排序：

```cpp
std::set<int> s;
```

`unordered_set` 用来去重，不排序，但通常更快：

```cpp
std::unordered_set<int> s;
```

判断元素是否存在：

```cpp
if (s.find(x) != s.end()) {
    // 找到了
}
```

---

**十六、二分查找**

二分查找用于：

```text
有序数组
```

每次看中间元素。

```cpp
int binarySearch(const std::vector<int>& nums, int target) {
    int left = 0;
    int right = static_cast<int>(nums.size()) - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (nums[mid] == target) {
            return mid;
        }

        if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return -1;
}
```

复杂度：

```text
O(log n)
```

注意前提：

```text
数组必须有序
```

---

**十七、排序**

最常用排序：

```cpp
std::sort(nums.begin(), nums.end());
```

复杂度通常：

```text
O(n log n)
```

自定义排序：

```cpp
std::sort(students.begin(), students.end(),
    [](const Student& a, const Student& b) {
        return a.score > b.score;
    }
);
```

实际开发中，除非学习算法原理，否则优先用 `std::sort`。

---

**十八、递归**

递归就是函数调用自己。

经典例子：阶乘。

```cpp
int factorial(int n) {
    if (n == 0) {
        return 1;
    }

    return n * factorial(n - 1);
}
```

关键：

```text
递归终止条件
问题规模变小
```

没有终止条件会无限递归，导致栈溢出。

---

**十九、递归例子：斐波那契**

```cpp
int fib(int n) {
    if (n <= 1) {
        return n;
    }

    return fib(n - 1) + fib(n - 2);
}
```

这个写法简单，但效率很差，因为重复计算很多。

后面学习动态规划会优化它。

---

**二十、树**

树是一种层级结构。

常见概念：

```text
根节点
子节点
父节点
叶子节点
深度
高度
```

二叉树每个节点最多两个孩子：

```cpp
struct TreeNode {
    int value;
    TreeNode* left;
    TreeNode* right;
};
```

树常用于：

```text
文件目录
组织架构
表达式解析
搜索结构
数据库索引
```

---

**二十一、二叉树遍历**

三种常见遍历：

```text
前序：根 左 右
中序：左 根 右
后序：左 右 根
```

代码：

```cpp
void preorder(TreeNode* root) {
    if (root == nullptr) {
        return;
    }

    std::cout << root->value << std::endl;
    preorder(root->left);
    preorder(root->right);
}
```

---

**二十二、堆 priority_queue**

堆常用来快速获取最大值或最小值。

C++ 中：

```cpp
std::priority_queue<int>
```

默认最大堆。

```cpp
std::priority_queue<int> pq;

pq.push(3);
pq.push(1);
pq.push(5);

std::cout << pq.top() << std::endl; // 5
```

小顶堆：

```cpp
std::priority_queue<int, std::vector<int>, std::greater<int>> pq;
```

常见应用：

```text
Top K 问题
任务优先级
最短路径算法
合并多个有序序列
```

---

**二十三、图**

图由：

```text
点
边
```

组成。

比如：

```text
城市和道路
用户和好友关系
网页和链接
地图导航
任务依赖
```

图的表示方式：

邻接表：

```cpp
std::vector<std::vector<int>> graph;
```

例如：

```cpp
graph[0] = {1, 2};
```

表示 0 号点连接 1 和 2。

---

**二十四、BFS 广度优先搜索**

BFS 常用队列。

适合：

```text
最短步数
层序遍历
从一个点向外一层层扩散
```

模板：

```cpp
void bfs(const std::vector<std::vector<int>>& graph, int start) {
    std::vector<bool> visited(graph.size(), false);
    std::queue<int> q;

    visited[start] = true;
    q.push(start);

    while (!q.empty()) {
        int current = q.front();
        q.pop();

        std::cout << current << std::endl;

        for (int next : graph[current]) {
            if (!visited[next]) {
                visited[next] = true;
                q.push(next);
            }
        }
    }
}
```

---

**二十五、DFS 深度优先搜索**

DFS 可以用递归。

适合：

```text
连通性
路径搜索
树/图遍历
回溯问题
```

模板：

```cpp
void dfs(
    const std::vector<std::vector<int>>& graph,
    int current,
    std::vector<bool>& visited
) {
    visited[current] = true;
    std::cout << current << std::endl;

    for (int next : graph[current]) {
        if (!visited[next]) {
            dfs(graph, next, visited);
        }
    }
}
```

---

**二十六、算法学习路线**

建议按这个顺序学：

```text
1. 时间复杂度
2. vector / stack / queue / map / set
3. 排序和查找
4. 双指针
5. 滑动窗口
6. 递归
7. 二叉树
8. BFS / DFS
9. 堆和 Top K
10. 动态规划
```

不要急着一口吃完。算法需要大量练习。

---

**二十七、你现在重点掌握**

这一章先掌握：

```text
1. 时间复杂度是什么
2. vector 的特点
3. stack 的后进先出
4. queue 的先进先出
5. unordered_map 快速查找
6. set 去重
7. 二分查找要求有序
8. sort 的使用
9. 递归必须有终止条件
10. BFS 用 queue
11. DFS 常用递归
```

---

**二十八、练习题**

### 练习 1：最大值

写函数：

```cpp
int maxValue(const std::vector<int>& nums)
```

返回数组最大值。

---

### 练习 2：二分查找

写函数：

```cpp
int binarySearch(const std::vector<int>& nums, int target)
```

找到了返回下标，找不到返回 `-1`。

---

### 练习 3：括号匹配

使用：

```cpp
std::stack<char>
```

判断字符串括号是否匹配。

扩展：支持 `()[]{}` 三种括号。

---

### 练习 4：单词统计

使用：

```cpp
std::unordered_map<std::string, int>
```

统计一组单词出现次数。

---

### 练习 5：Top K

给定一组整数，找最大的 3 个数。

可以先用：

```cpp
sort
```

实现，再尝试用：

```cpp
priority_queue
```

实现。

---

下一步建议继续学习：

```text
常见算法技巧
```

比如：

```text
双指针
滑动窗口
前缀和
哈希查找
递归与回溯
```

这些是算法题和实际数据处理里最常用的套路。