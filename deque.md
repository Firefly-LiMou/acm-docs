# deque 容器详解

`<deque>`头文件实现了C++标准库中的双端队列容器`std::deque`。`deque`（double-ended queue，发音为"deck"）是一种具有队列和栈特性的容器，支持在两端进行高效的插入和删除操作。与`vector`类似，`deque`支持随机访问，但与`vector`不同的是，它在头部插入删除时不需要移动所有元素。在ACM竞赛中，`deque`是实现滑动窗口、BFS搜索和需要双端操作的算法的理想选择。

## 1. deque类概述

`std::deque`是一个双端队列容器，支持在序列的两端进行高效的插入和删除操作。从内部实现来看，`deque`采用分块的连续内存管理方式，它将多个固定大小的内存块通过指针链接在一起，形成一个虚拟的连续空间。这种设计使得`deque`既支持`vector`的随机访问特性，又具备在两端进行O(1)时间复杂度操作的能力。

`deque`的核心特性包括：支持O(1)时间的头尾插入删除、支持O(1)时间的随机访问、内部存储不是完全连续但支持指针运算、不需要在中间插入时移动所有元素、以及迭代器在重新分配时可能失效但插入操作本身不会使迭代器失效。

### 1.1 头文件包含方式

```cpp
#include <deque>           // 标准方式
#include <bits/stdc++.h>   // 竞赛常用
using namespace std;       // 使用std命名空间
```

### 1.2 与vector和list的对比

| 特性 | vector | deque | list |
|------|--------|-------|------|
| 随机访问 | O(1) | O(1) | O(n) |
| 头部插入/删除 | O(n) | O(1) amortized | O(1) |
| 尾部插入/删除 | O(1) amortized | O(1) amortized | O(1) |
| 中间插入/删除 | O(n) | O(n) | O(1) |
| 内存连续 | 是 | 否 | 否 |
| 缓存友好 | 高 | 中等 | 低 |

**deque的定位**：在随机访问性能和双端操作效率之间取得平衡，是需要同时支持这两种操作的场景的最佳选择。

## 2. 构造与初始化

### 2.1 默认构造函数

```cpp
// 创建空deque
deque<int> d1;                      // 空deque，size=0

deque<string> d2;                   // 字符串deque

deque<deque<int>> d3;               // 二维deque（矩阵常用）
```

### 2.2 拷贝构造与移动构造

```cpp
// 拷贝构造 - 创建完全独立的副本
deque<int> original = {1, 2, 3, 4, 5};
deque<int> copy1(original);         // 完整的深拷贝
deque<int> copy2 = original;        // 同样是拷贝构造

// 移动构造 - 高效转移资源（C++11）
deque<int> moved = std::move(original);  // original变为空，moved获得资源
```

### 2.3 初始化列表构造（C++11）

```cpp
// 使用花括号初始化列表
deque<int> d1 = {1, 2, 3, 4, 5};    // 列表初始化
deque<int> d2{1, 2, 3, 4, 5};       // 直接初始化

// 空deque初始化
deque<int> d3;                      // 默认空
deque<int> d4 = {};                 // 空列表初始化
deque<int> d5{};                    // 同上
```

### 2.4 重复元素初始化

```cpp
// 指定数量的默认初始化
deque<int> d1(10);                  // 10个元素的deque，每个元素为0

// 指定数量的值初始化
deque<int> d2(5, -1);               // 5个元素的deque，每个元素为-1
deque<string> d3(3, "hello");       // 3个元素的deque，每个都是"hello"

cout << d1.size() << endl;          // 10
cout << d2.size() << endl;          // 5
cout << d3.size() << endl;          // 3
```

### 2.5 范围构造

```cpp
// 使用迭代器范围构造
int arr[] = {1, 2, 3, 4, 5};
deque<int> d1(arr, arr + 5);                    // 从数组构造

vector<int> v = {1, 2, 3, 4, 5};
deque<int> d2(v.begin(), v.end());              // 从vector构造

deque<int> source = {1, 2, 3, 4, 5};
deque<int> d3(source.begin() + 1, source.end() - 1);  // 2, 3, 4
```

### 2.6 构造函数的复杂度分析

| 构造函数类型 | 时间复杂度 | 说明 |
|--------------|------------|------|
| 默认构造 | O(1) | 仅初始化空deque |
| 拷贝构造 | O(n) | 复制所有元素 |
| 移动构造 | O(1) | 仅转移指针（如果实现支持） |
| 范围构造 | O(n) | 复制指定范围的元素 |
| 重复构造 | O(n) | 构造n个相同元素 |
| 列表构造 | O(n) | 构造列表中的n个元素 |

## 3. 元素访问

### 3.1 下标访问operator[]

```cpp
deque<int> d = {10, 20, 30, 40, 50};

// 只读访问
int a = d[0];                       // 10
int b = d[2];                       // 30

// 可写访问
d[0] = 100;                         // d[0]现在为100
d[4] = 500;                         // d[4]现在为500

// 越界访问：未定义行为
// d[10] = 100;                     // 危险！可能导致崩溃
```

**重要说明**：`deque`的`operator[]`与`vector`一样不进行边界检查，性能高效但不安全。当需要边界检查时，应使用`at()`方法。

### 3.2 at()成员函数

```cpp
deque<int> d = {10, 20, 30, 40, 50};

// 安全的下标访问
int a = d.at(0);                    // 10
int b = d.at(2);                    // 30

// 边界检查，超出范围抛出异常
try {
    int c = d.at(10);               // 抛出 out_of_range 异常
} catch (const out_of_range& e) {
    cerr << "Index out of range: " << e.what() << endl;
}

// 修改元素
d.at(0) = 100;                      // 安全修改
```

### 3.3 front()和back()成员函数

```cpp
deque<int> d = {10, 20, 30, 40, 50};

// front() - 访问第一个元素
int first = d.front();              // 10
// 对空deque调用front()是未定义行为

// back() - 访问最后一个元素
int last = d.back();                // 50
// 对空deque调用back()是未定义行为

// 可修改
d.front() = 100;                    // d[0] = 100
d.back() = 500;                     // d[4] = 500

// 安全访问模式
if (!d.empty()) {
    int f = d.front();
    int b = d.back();
}
```

### 3.4 迭代器访问

```cpp
deque<int> d = {10, 20, 30, 40, 50};

// 正向迭代器
for (auto it = d.begin(); it != d.end(); ++it) {
    cout << *it << ' ';             // 10 20 30 40 50
}

// 使用范围for循环（C++11）
for (int& x : d) {
    x *= 2;                         // 修改每个元素
}

// const迭代器（只读）
for (auto cit = d.cbegin(); cit != d.cend(); ++cit) {
    cout << *cit << ' ';
}

// 反向迭代器
for (auto rit = d.rbegin(); rit != d.rend(); ++rit) {
    cout << *rit << ' ';            // 50 40 30 20 10
}
```

### 3.5 迭代器失效问题

```cpp
deque<int> d = {1, 2, 3, 4, 5};
auto it = d.begin() + 2;            // 指向元素3

// 哪些操作会使迭代器失效？
d.push_back(6);                     // 可能使迭代器失效（重新分配）
d.push_front(0);                    // 可能使迭代器失效
d.insert(d.begin(), 0);             // 可能使迭代器失效
d.pop_back();                       // 不影响其他迭代器
d.pop_front();                      // 可能使指向被删除元素的迭代器失效
d.erase(d.begin());                 // 可能使迭代器失效

// 结论：deque的迭代器可能在任何插入删除操作后失效
// 使用时需要在操作后重新获取迭代器
```

## 4. 容量管理

### 4.1 size()和empty()成员函数

```cpp
deque<int> d = {1, 2, 3, 4, 5};

// size() - 返回元素数量
size_t sz = d.size();               // 5

// empty() - 判断是否为空（比size() == 0更高效）
bool is_empty = d.empty();          // false
deque<int> empty_d;
bool is_empty2 = empty_d.empty();   // true
```

### 4.2 max_size()成员函数

```cpp
deque<int> d;
size_t max_sz = d.max_size();       // 非常大的值
// max_size取决于系统可用内存和实现
```

### 4.3 deque没有reserve()

```cpp
// 与vector不同，deque没有reserve()函数！
// deque不需要也不能预先分配固定大小的内存
// 它采用分块管理，自动按需分配

deque<int> d;
// d.reserve(100);                  // 编译错误！没有这个函数

// deque的内存管理对用户透明
// 它会自动管理多个内存块
```

**关键区别**：与`vector`不同，`deque`不需要也不能通过`reserve()`预分配内存。它采用分块连续内存的设计，在插入元素时自动在需要的地方分配新的内存块。

### 4.4 shrink_to_fit()成员函数（C++11）

```cpp
deque<int> d = {1, 2, 3, 4, 5};

d.shrink_to_fit();                  // 尝试释放多余内存
// 注意：这是请求，不保证一定释放
```

## 5. 元素添加与删除

### 5.1 push_back()和push_front()

```cpp
deque<int> d;

// 在末尾添加元素
d.push_back(1);                     // {1}
d.push_back(2);                     // {1, 2}
d.push_back(3);                     // {1, 2, 3}

// 在头部添加元素
d.push_front(0);                    // {0, 1, 2, 3}
d.push_front(-1);                   // {-1, 0, 1, 2, 3}

// 复杂度：都是O(1) amortized
```

### 5.2 emplace_back()和emplace_front()（C++11）

```cpp
struct Point {
    int x, y;
    Point(int x, int y) : x(x), y(y) {}
};

deque<Point> points;

// emplace_back - 直接在末尾构造元素
points.emplace_back(1, 2);          // 直接构造Point(1, 2)

// emplace_front - 直接在头部构造元素
points.emplace_front(0, 0);         // 直接构造Point(0, 0)

// 比push版本更高效
points.push_back(Point(3, 4));      // 临时对象 + 移动构造
points.emplace_back(3, 4);          // 直接构造
```

### 5.3 pop_back()和pop_front()

```cpp
deque<int> d = {1, 2, 3, 4, 5};

// 删除末尾元素
d.pop_back();                       // {1, 2, 3, 4}

// 删除头部元素
d.pop_front();                      // {2, 3, 4}

// 复杂度：都是O(1)

// 注意：这些函数不返回被删除的元素
int last = d.back();                // 先获取
d.pop_back();

int first = d.front();              // 先获取
d.pop_front();
```

### 5.4 insert()成员函数

```cpp
deque<int> d = {1, 2, 3, 4, 5};

// 在指定位置前插入元素
auto it = d.begin() + 2;            // 指向元素3
d.insert(it, 10);                   // {1, 2, 10, 3, 4, 5}

// 插入多个相同元素
d.insert(d.begin() + 1, 3, 99);     // 在位置1插入3个99
                                    // {1, 99, 99, 99, 2, 10, 3, 4, 5}

// 插入另一个容器的范围
deque<int> source = {100, 200};
d.insert(d.begin(), source.begin(), source.end());
                                    // {100, 200, 1, 99, 99, 99, 2, 10, 3, 4, 5}

// 插入初始化列表
d.insert(d.end(), {6, 7, 8});       // 末尾追加6, 7, 8

// insert的复杂度：O(n)（需要移动元素）
// 但不需要像vector那样复制整个内存块
```

### 5.5 emplace()成员函数（C++11）

```cpp
struct Data {
    int id;
    string name;
    Data(int id, const string& name) : id(id), name(name) {}
};

deque<Data> data;
data.reserve(10);                   // deque没有reserve，这是错误写法

auto it = data.begin();

// emplace 在指定位置直接构造元素
data.emplace(it, 1, "Alice");       // 在位置1构造Data(1, "Alice")
```

### 5.6 erase()成员函数

```cpp
deque<int> d = {1, 2, 3, 4, 5};

// 删除指定位置的元素
auto it = d.begin() + 2;            // 指向元素3
d.erase(it);                        // {1, 2, 4, 5}
// 返回指向被删除元素后一个元素的迭代器

// 删除范围
it = d.begin() + 1;
d.erase(it, it + 2);                // 删除第2和第3个元素: {1, 4, 5}

// erase-remove 惯用法
d = {1, 2, 3, 2, 4, 2, 5};
d.erase(remove(d.begin(), d.end(), 2), d.end());
                                    // {1, 3, 4, 5}

// erase的复杂度：O(n)
```

### 5.7 clear()成员函数

```cpp
deque<int> d = {1, 2, 3, 4, 5};

d.clear();                          // size变为0，内存可能保留
// d = {}
```

### 5.8 各种操作的复杂度

| 操作 | 时间复杂度 | 说明 |
|------|------------|------|
| `push_back()` | O(1) amortized | 尾部添加 |
| `push_front()` | O(1) amortized | 头部添加 |
| `pop_back()` | O(1) | 尾部删除 |
| `pop_front()` | O(1) | 头部删除 |
| `insert()` | O(n) | 指定位置插入 |
| `erase()` | O(n) | 指定位置删除 |
| `operator[]` | O(1) | 随机访问 |
| `at()` | O(1) | 边界检查访问 |

## 6. deque特有操作

### 6.1 与vector和list的比较操作

```cpp
deque<int> d1 = {1, 2, 3};
deque<int> d2 = {1, 2, 3};
deque<int> d3 = {1, 2, 4};

// 比较运算（字典序）
bool eq = (d1 == d2);               // true
bool ne = (d1 != d3);               // true
bool lt = (d1 < d3);                // true（3 < 4）
bool le = (d1 <= d3);               // true
bool gt = (d1 > d3);                // false
bool ge = (d1 >= d3);               // false
```

### 6.2 赋值操作

```cpp
deque<int> d1 = {1, 2, 3};
deque<int> d2;

// operator=
d2 = d1;                            // 拷贝赋值
d2 = {4, 5, 6};                     // 列表赋值

// assign - 重新赋值
d1.assign(5, 100);                  // d1 = {100, 100, 100, 100, 100}
d1.assign(d2.begin(), d2.end());    // d1 = d2
d1.assign({1, 2, 3});               // d1 = {1, 2, 3}

// swap - 交换两个deque
d1.swap(d2);
swap(d1, d2);
```

### 6.3 快速清空和释放内存

```cpp
deque<int> d = {1, 2, 3, 4, 5};

d.clear();                          // 清空内容，内存可能保留

// 释放内存的方法
deque<int>().swap(d);               // 创建空deque并交换
```

## 7. 竞赛中使用deque的实用模式

### 7.1 滑动窗口最大值

```cpp
#include <deque>
#include <vector>

vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;                  // 存储索引
    vector<int> result;
    
    for (int i = 0; i < nums.size(); ++i) {
        // 移除窗口外的元素索引
        if (!dq.empty() && dq.front() <= i - k) {
            dq.pop_front();
        }
        
        // 移除小于当前元素的元素索引
        while (!dq.empty() && nums[dq.back()] <= nums[i]) {
            dq.pop_back();
        }
        
        dq.push_back(i);
        
        // 从第k个元素开始输出
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    
    return result;
}

// 使用示例
int main() {
    vector<int> nums = {1, 3, -1, -3, 5, 3, 6, 7};
    int k = 3;
    vector<int> result = maxSlidingWindow(nums, k);
    // 输出: [3, 3, 5, 5, 6, 7]
    return 0;
}
```

### 7.2 BFS搜索（层次遍历）

```cpp
#include <deque>
#include <vector>

struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> result;
    if (!root) return result;
    
    deque<TreeNode*> q;
    q.push_back(root);
    
    while (!q.empty()) {
        int level_size = q.size();
        vector<int> level;
        
        for (int i = 0; i < level_size; ++i) {
            TreeNode* node = q.front();
            q.pop_front();
            level.push_back(node->val);
            
            if (node->left) q.push_back(node->left);
            if (node->right) q.push_back(node->right);
        }
        
        result.push_back(level);
    }
    
    return result;
}
```

### 7.3 BFS处理网格问题

```cpp
#include <deque>
#include <vector>
#include <queue>

struct Pos {
    int x, y;
    Pos(int x, int y) : x(x), y(y) {}
};

int shortestPath(vector<vector<int>>& grid, int k) {
    if (grid.empty() || grid[0].empty()) return -1;
    
    int m = grid.size(), n = grid[0].size();
    if (k >= m + n - 2) return m + n - 2;  // 可以直线到达
    
    vector<vector<vector<int>>> visited(m, 
        vector<vector<int>>(n, vector<int>(k + 1, 0)));
    
    deque<tuple<int, int, int, int>> q;  // x, y, remaining_k, steps
    q.emplace_back(0, 0, k, 0);
    visited[0][0][k] = 1;
    
    const int dx[] = {0, 0, 1, -1};
    const int dy[] = {1, -1, 0, 0};
    
    while (!q.empty()) {
        auto [x, y, rem_k, steps] = q.front();
        q.pop_front();
        
        if (x == m - 1 && y == n - 1) return steps;
        
        for (int dir = 0; dir < 4; ++dir) {
            int nx = x + dx[dir];
            int ny = y + dy[dir];
            
            if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
            if (grid[nx][ny] == 1 && rem_k == 0) continue;
            
            int new_k = rem_k - grid[nx][ny];
            if (visited[nx][ny][new_k]) continue;
            
            visited[nx][ny][new_k] = 1;
            q.emplace_back(nx, ny, new_k, steps + 1);
        }
    }
    
    return -1;
}
```

### 7.4 双端队列实现栈

```cpp
template<typename T>
class DequeStack {
private:
    deque<T> data;
public:
    void push(const T& x) {
        data.push_back(x);          // 在尾部插入
    }
    
    void pop() {
        data.pop_back();
    }
    
    T& top() {
        return data.back();
    }
    
    const T& top() const {
        return data.back();
    }
    
    bool empty() const {
        return data.empty();
    }
    
    size_t size() const {
        return data.size();
    }
};
```

### 7.5 双端队列实现队列

```cpp
template<typename T>
class DequeQueue {
private:
    deque<T> data;
public:
    void push(const T& x) {
        data.push_back(x);          // 从尾部插入
    }
    
    void pop() {
        data.pop_front();           // 从头部删除
    }
    
    T& front() {
        return data.front();
    }
    
    const T& front() const {
        return data.front();
    }
    
    T& back() {
        return data.back();
    }
    
    const T& back() const {
        return data.back();
    }
    
    bool empty() const {
        return data.empty();
    }
    
    size_t size() const {
        return data.size();
    }
};
```

### 7.6 循环缓冲区（Ring Buffer）

```cpp
template<typename T, size_t Capacity>
class CircularBuffer {
private:
    deque<T> buffer;
    size_t capacity_;
public:
    CircularBuffer(size_t cap = Capacity) : capacity_(cap) {}
    
    void push(const T& x) {
        if (buffer.size() == capacity_) {
            buffer.pop_front();     // 移除最旧的元素
        }
        buffer.push_back(x);
    }
    
    const T& oldest() const {
        return buffer.front();
    }
    
    const T& newest() const {
        return buffer.back();
    }
    
    bool full() const {
        return buffer.size() == capacity_;
    }
    
    bool empty() const {
        return buffer.empty();
    }
    
    size_t size() const {
        return buffer.size();
    }
    
    void clear() {
        buffer.clear();
    }
};
```

## 8. 与其他容器的性能比较

### 8.1 插入操作性能对比

```cpp
// 测试头部插入性能
vector<int> v;
deque<int> d;
list<int> l;

const int N = 100000;

// vector头部插入最慢
for (int i = 0; i < N; ++i) {
    v.insert(v.begin(), i);         // O(n²) 总体
}

// deque和list头部插入都很快
for (int i = 0; i < N; ++i) {
    d.push_front(i);                // O(n) 总体，amortized O(1) per operation
    l.push_front(i);                // O(n) 总体，O(1) per operation
}

// 但deque支持随机访问，list不支持
int x = d[50000];                   // O(1)
auto it = l.begin();
advance(it, 50000);                 // O(n)
```

### 8.2 内存使用比较

```cpp
// 内存使用量（大约）
// 假设存储100万个int（4字节）

// vector:
// 总内存 ≈ 4 * 1000000 + 少量开销 ≈ 4MB
// 连续内存，缓存友好

// deque:
// 总内存 ≈ 4 * 1000000 + 指针开销（取决于块大小）
// 例如块大小为512，每块需要1个指针：
// 指针开销 ≈ 8 * 1000000 / 512 ≈ 16KB
// 非连续内存，缓存局部性较差

// list:
// 每个节点：数据(4) + 前驱指针(8) + 后继指针(8) = 20字节（可能对齐到24）
// 总内存 ≈ 24 * 1000000 = 24MB
// 高度分散的内存
```

### 8.3 选择容器的决策树

```
需要随机访问？
├─ 是 → 需要头部插入删除？
│    ├─ 是 → 使用 deque
│    └─ 否 → 使用 vector
└─ 否 → 需要在中间频繁插入删除？
     ├─ 是 → 使用 list
     └─ 否 → 考虑 deque 或 list
```

## 9. 常见陷阱与注意事项

### 9.1 没有reserve()

```cpp
deque<int> d;

// 错误：deque没有reserve()函数
// d.reserve(1000);               // 编译错误！

// 正确做法：接受deque的自动内存管理
// 或者使用vector如果需要预分配
vector<int> v;
v.reserve(1000);                    // vector有这个函数
```

### 9.2 迭代器失效问题

```cpp
deque<int> d = {1, 2, 3, 4, 5};
auto it = d.begin() + 2;

// 在deque中，以下操作可能使迭代器失效：
d.push_back(6);                     // 可能失效
d.push_front(0);                    // 可能失效
d.insert(d.begin(), 0);             // 可能失效
d.erase(d.begin());                 // 可能失效

// 不会使迭代器失效的操作：
d.pop_back();                       // 不影响其他迭代器
d.pop_front();                      // 只影响指向被删除元素的迭代器
d[0] = 100;                         // 不影响迭代器
d.front() = 100;                    // 不影响迭代器

// 最佳实践：操作deque后重新获取迭代器
it = d.begin() + 2;
```

### 9.3 与vector的混淆

```cpp
deque<int> d = {1, 2, 3, 4, 5};

// vector的某些操作deque也有
d.size();                           // O(1)
d.empty();                          // O(1)
d[0];                               // O(1)
d.front();                          // O(1)

// deque没有vector的这些操作
// d.reserve(1000);                // 错误
// d.capacity();                   // 错误（deque没有public的capacity）
```

### 9.4 空容器操作

```cpp
deque<int> d;

// 以下操作都是未定义行为
// d.front()
// d.back()
// d.pop_front()
// d.pop_back()

// 正确做法
if (!d.empty()) {
    int x = d.front();
    d.pop_front();
}
```

### 9.5 边界检查

```cpp
deque<int> d = {1, 2, 3};

// 安全访问
int x = d.at(0);                    // 没问题
// int y = d.at(10);               // 抛出异常

// 不安全但高效
int z = d[10];                      // 未定义行为（可能崩溃或返回垃圾值）

// 竞赛建议：在能保证索引有效时使用[]，否则使用at()
```

## 10. 复杂度总结

| 操作 | 时间复杂度 | 空间复杂度 |
|------|------------|------------|
| 随机访问 `[]` | O(1) | - |
| 随机访问 `at()` | O(1) | - |
| `push_back()` | O(1) amortized | - |
| `push_front()` | O(1) amortized | - |
| `pop_back()` | O(1) | - |
| `pop_front()` | O(1) | - |
| `insert()` | O(n) | - |
| `erase()` | O(n) | - |
| `clear()` | O(n) | - |
| `find()` | O(n) | - |

## 11. 总结

`deque`是C++标准库中功能最灵活的双端容器，其核心优势包括：

1. **双端操作高效**：O(1)时间复杂度的头尾插入删除
2. **随机访问支持**：O(1)时间复杂度的下标访问
3. **无需预分配**：自动管理分块内存
4. **综合性能良好**：在多种操作类型中都有较好的表现

在ACM竞赛中，`deque`是以下场景的最佳选择：

- **滑动窗口问题**：如最大值/最小值滑动窗口
- **BFS搜索**：层次遍历图或树
- **双端队列操作**：需要同时支持头尾操作的算法
- **栈和队列的混合需求**：需要在两端都能操作的场景

虽然`deque`的缓存局部性不如`vector`，但其双端操作的高效性使其成为许多竞赛问题的首选容器。