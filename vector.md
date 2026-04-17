# vector 容器详解

`<vector>`是C++标准库中最重要的序列容器之一，提供了动态数组功能。在ACM竞赛中，`vector`是最常用的数据结构，因为它结合了数组的高效随机访问和动态内存管理的便利性。

## 1. vector类概述

`std::vector`是一个序列容器，封装了动态大小数组的功能。作为序列容器的特性，`vector`中的元素按照严格的线性顺序排列，可以通过迭代器进行顺序访问。`vector`在内存中采用连续存储的方式，这使得它支持与普通数组类似的随机访问特性，同时能够在末尾高效地插入和删除元素。

`vector`的核心特性包括：支持O(1)时间的随机访问、在末尾进行O(1) amortized时间的插入和删除、能够自动管理内存分配、以及提供与数组兼容的接口。这些特性使得`vector`成为处理大多数竞赛问题的首选数据结构。

### 1.1 头文件包含方式

```cpp
#include <vector>           // 标准方式
#include <bits/stdc++.h>    // 竞赛常用，包含所有标准库
using namespace std;        // 使用std命名空间
```

### 1.2 基本概念与特性

`vector`在内部维护三个关键指针：指向数据起始位置的指针、指向当前末尾的指针（等于数据起始位置加大小）、以及指向已分配内存末尾的指针（等于容量）。这种设计使得`vector`能够在保持连续内存的同时高效地管理动态增长。当插入元素导致空间不足时，`vector`会分配更大的内存（通常是当前容量的1.5倍或2倍），将现有元素复制或移动到新内存，然后释放旧内存。

**重要特性说明**：
- 连续内存：元素在内存中连续存储，支持指针算术运算
- 动态大小：可以根据需要自动增长或收缩
- 类型安全：模板参数指定元素类型，编译时检查类型一致性
- 内存自动管理：析构时自动释放内存，无需手动管理

## 2. 构造与初始化

### 2.1 默认构造函数

```cpp
// 创建空vector
vector<int> v1;                     // 空vector，size=0，capacity=0
vector<string> v2;                  // 字符串vector
vector<vector<int>> v3;             // 二维vector

// 使用explicit限制隐式转换
vector<int> v4(10);                 // 10个元素的vector，每个元素为0
vector<int> v5(5, -1);              // 5个元素的vector，每个元素为-1
```

### 2.2 拷贝构造与移动构造

```cpp
// 拷贝构造 - 创建完全独立的副本
vector<int> original = {1, 2, 3, 4, 5};
vector<int> copy1(original);        // 完整的深拷贝
vector<int> copy2 = original;       // 同样是拷贝构造

// 移动构造 - 高效转移资源（C++11）
vector<int> moved = std::move(original);  // original变为空，moved获得资源
// 注意：移动后original处于有效但未指定状态

// 赋值操作的拷贝与移动
vector<int> a = {1, 2, 3};
vector<int> b = {4, 5, 6};
a = b;                              // 拷贝赋值，a变为{4, 5, 6}
a = std::move(b);                   // 移动赋值，a变为{4, 5, 6}，b变为空
```

### 2.3 初始化列表构造（C++11）

```cpp
// 使用花括号初始化列表
vector<int> v1 = {1, 2, 3, 4, 5};   // 列表初始化
vector<int> v2{1, 2, 3, 4, 5};      // 直接初始化（不拷贝）

// 空vector初始化
vector<int> v3;                     // 默认空
vector<int> v4 = {};                // 空列表初始化
vector<int> v5{};                   // 同上

// 指定初始值
vector<int> v6(10, 42);             // 10个元素，每个都是42
vector<int> v7{10, 42};             // 2个元素：10和42（注意与上者的区别）

// 字符串vector
vector<string> words = {"hello", "world", "ACM"};
```

### 2.4 范围构造

```cpp
// 使用迭代器范围构造
int arr[] = {1, 2, 3, 4, 5};
vector<int> v1(arr, arr + 5);                   // 从数组构造
vector<int> v2(arr, arr + sizeof(arr)/sizeof(arr[0]));

vector<int> source = {1, 2, 3, 4, 5};
vector<int> v3(source.begin() + 1, source.end() - 1);  // 2, 3, 4

// 从另一个vector的部分构造
vector<int> v4(v1.begin(), v1.begin() + 3);    // 前3个元素

// 使用初始化列表（C++11）
vector<int> v5 = {1, 2, 3, 4, 5};
```

### 2.5 构造函数的参数详解

```cpp
// vector(size_type count, const T& value = T(), Allocator alloc = Allocator())
vector<int> v1(100);                // 100个默认构造的int（值为0）
vector<int> v2(100, -1);            // 100个值为-1的int

// vector(InputIt first, InputIt last, Allocator alloc = Allocator())
vector<int> v3(arr, arr + 5);       // 迭代器范围构造

// vector(const vector& other)
vector<int> v4(v3);                 // 拷贝构造

// vector(vector&& other) noexcept
vector<int> v5(std::move(v4));      // 移动构造

// vector(initializer_list<T> init, Allocator alloc = Allocator())
vector<int> v6 = {1, 2, 3, 4, 5};   // 列表构造
```

### 2.6 构造函数的复杂度分析

| 构造函数类型 | 时间复杂度 | 说明 |
|--------------|------------|------|
| 默认构造 | O(1) | 仅分配少量或无内存 |
| 拷贝构造 | O(n) | 复制所有元素 |
| 移动构造 | O(1) | 仅转移指针（如果实现支持） |
| 范围构造 | O(n) | 复制指定范围的元素 |
| 重复构造 | O(n) | 构造n个相同元素 |
| 列表构造 | O(n) | 构造列表中的n个元素 |

## 3. 元素访问

### 3.1 下标访问operator[]

```cpp
vector<int> v = {10, 20, 30, 40, 50};

// 只读访问
int a = v[0];                       // 10
int b = v[2];                       // 30

// 可写访问
v[0] = 100;                         // v[0]现在为100
v[4] = 500;                         // v[4]现在为500

// 越界访问：未定义行为
// v[10] = 100;                     // 危险！可能导致崩溃

// 负数索引：未定义行为
// v[-1] = 100;                     // 危险！
```

**关键说明**：`operator[]`不进行边界检查，这是它与`at()`的主要区别。在竞赛中，当能够确保索引有效时，使用`operator[]`可以获得更好的性能。但在处理用户输入或可能出错的索引时，应使用`at()`。

### 3.2 at()成员函数

```cpp
vector<int> v = {10, 20, 30, 40, 50};

// 安全的下标访问
int a = v.at(0);                    // 10
int b = v.at(2);                    // 30

// 边界检查，超出范围抛出异常
try {
    int c = v.at(10);               // 抛出 out_of_range 异常
} catch (const out_of_range& e) {
    cerr << "Index out of range: " << e.what() << endl;
}

// 修改元素
v.at(0) = 100;                      // 安全修改
```

**异常处理场景**：

```cpp
// 安全的边界访问函数
int safe_get(const vector<int>& v, size_t index, int default_value = 0) {
    try {
        return v.at(index);
    } catch (const out_of_range&) {
        return default_value;
    }
}
```

### 3.3 front()和back()成员函数

```cpp
vector<int> v = {10, 20, 30, 40, 50};

// front() - 访问第一个元素
int first = v.front();              // 10
// 对空vector调用front()是未定义行为
if (!v.empty()) {
    int f = v.front();
}

// back() - 访问最后一个元素
int last = v.back();                // 50
// 对空vector调用back()是未定义行为
if (!v.empty()) {
    int b = v.back();
}

// 可修改
v.front() = 100;                    // v[0] = 100
v.back() = 500;                     // v[4] = 500
```

### 3.4 data()成员函数（C++11）

```cpp
vector<int> v = {1, 2, 3, 4, 5};

// 获取指向底层数组的指针
int* ptr = v.data();                // ptr指向v[0]

// 通过指针访问元素
cout << *(ptr + 2) << endl;         // 3，等同于v[2]

// 通过指针修改元素
*(ptr + 1) = 20;                    // v[1] = 20

// const版本
const vector<int>& cv = v;
const int* cptr = cv.data();        // 只读指针
```

**使用场景**：与需要数组指针的C函数交互时特别有用。

```cpp
// 与C函数交互示例
void c_function(int* arr, size_t n) {
    for (size_t i = 0; i < n; ++i) {
        arr[i] *= 2;
    }
}

vector<int> v = {1, 2, 3, 4, 5};
c_function(v.data(), v.size());     // v变为{2, 4, 6, 8, 10}

// 计算数组总和（使用指针算术）
int sum = 0;
for (int* p = v.data(); p != v.data() + v.size(); ++p) {
    sum += *p;
}
```

### 3.5 迭代器访问

```cpp
vector<int> v = {10, 20, 30, 40, 50};

// 正向迭代器
for (auto it = v.begin(); it != v.end(); ++it) {
    cout << *it << ' ';             // 10 20 30 40 50
}

// 使用范围for循环（C++11）
for (int& x : v) {
    x *= 2;                         // 修改每个元素
}

// const迭代器（只读）
for (auto cit = v.cbegin(); cit != v.cend(); ++cit) {
    cout << *cit << ' ';
}

// 反向迭代器
for (auto rit = v.rbegin(); rit != v.rend(); ++rit) {
    cout << *rit << ' ';            // 50 40 30 20 10
}

// 使用迭代器计算索引
auto it = v.begin() + 3;            // 指向第4个元素
size_t index = distance(v.begin(), it);  // 3
```

**迭代器失效问题**：

```cpp
vector<int> v = {1, 2, 3, 4, 5};
auto it = v.begin() + 2;            // 指向元素3

v.push_back(6);                     // 可能导致重新分配

// 现在it可能失效！因为push_back可能导致vector重新分配内存
// cout << *it << endl;             // 未定义行为！

// 解决方案1：在修改vector后重新获取迭代器
it = v.begin() + 2;                 // 重新获取

// 解决方案2：使用reserve预留空间
vector<int> v2 = {1, 2, 3, 4, 5};
v2.reserve(100);                   // 预留足够空间
auto it2 = v2.begin() + 2;
v2.push_back(6);                   // 不会重新分配，it2仍然有效

// 不会使迭代器失效的操作：
// - push_back（除非重新分配）
// - pop_back
// - at()
// - operator[]
// - front()
// - back()

// 会使迭代器失效的操作：
// - insert
// - emplace
// - erase
// - clear
// - 任何可能导致重新分配的操作
```

## 4. 容量管理

### 4.1 size()和empty()成员函数

```cpp
vector<int> v = {1, 2, 3, 4, 5};

// size() - 返回元素数量
size_t sz = v.size();               // 5

// empty() - 判断是否为空（比size() == 0更高效）
bool is_empty = v.empty();          // false
vector<int> empty_v;
bool is_empty2 = empty_v.empty();   // true

// max_size() - 可容纳的最大元素数
size_t max_sz = v.max_size();       // 非常大的值（与实现相关）

// 容量与大小的区别
// size:   当前存储的元素数量
// capacity: 已分配的内存能容纳的元素数量
```

### 4.2 capacity()成员函数

```cpp
vector<int> v;
cout << v.size() << " " << v.capacity() << endl;  // 0 0

v.push_back(1);
cout << v.size() << " " << v.capacity() << endl;  // 1 1（或更大，取决于实现）

v.push_back(2);
cout << v.size() << " " << v.capacity() << endl;  // 2 2（或更大）

v.push_back(3);
cout << v.size() << " " << v.capacity() << endl;  // 3 4（通常翻倍）

v.push_back(4);
cout << v.size() << " " << v.capacity() << endl;  // 4 4

v.push_back(5);
cout << v.size() << " " << v.capacity() << endl;  // 5 8（翻倍）
```

**实现细节**：大多数标准库实现采用翻倍策略，当容量不足时，将容量翻倍后再分配。这样可以保证push_back的amortized时间复杂度为O(1)。

### 4.3 reserve()成员函数

```cpp
vector<int> v;

// reserve(n) - 预分配至少能容纳n个元素的内存
v.reserve(1000);                    // capacity至少为1000

// 此时size仍为0，但后续push_back不会频繁重新分配
for (int i = 0; i < 1000; ++i) {
    v.push_back(i);                 // 不会有重新分配
}

// reserve不会改变size
cout << v.size() << " " << v.capacity() << endl;  // 1000 >=1000

// 如果请求的容量小于等于当前容量，什么都不做
v.reserve(500);                     // 无操作，容量仍为1000
```

**使用场景**：

```cpp
// 场景1：已知最终大小时预先分配
int n;
cin >> n;
vector<int> fibonacci;
fibonacci.reserve(n);               // 预先分配空间
fibonacci.push_back(1);
fibonacci.push_back(1);
for (int i = 2; i < n; ++i) {
    fibonacci.push_back(fibonacci[i-1] + fibonacci[i-2]);
}

// 场景2：避免在循环中频繁重新分配
vector<int> results;
results.reserve(data_size);         // data_size在循环前已知
for (const auto& item : data) {
    if (condition(item)) {
        results.push_back(compute(item));
    }
}

// 场景3：与算法配合使用
vector<int> data;
data.reserve(100000);               // 预先分配
generate_n(back_inserter(data), 100000, rand);
```

### 4.4 shrink_to_fit()成员函数（C++11）

```cpp
vector<int> v = {1, 2, 3, 4, 5};

v.reserve(1000);                    // 预分配
cout << v.size() << " " << v.capacity() << endl;  // 5 1000

v.shrink_to_fit();                  // 释放多余内存
cout << v.size() << " " << v.capacity() << endl;  // 5 5（或接近5）
```

**注意**：`shrink_to_fit()`只是一个请求，不保证容量一定会缩小到size的大小。但大多数实现会满足这个请求。

### 4.5 resize()成员函数

```cpp
vector<int> v = {1, 2, 3};

// resize(n) - 调整大小为n
v.resize(5);                        // v = {1, 2, 3, 0, 0}
                                    // 新增元素使用默认初始化（数值为0）

// resize(n, value) - 用指定值填充新元素
v.resize(7, 99);                    // v = {1, 2, 3, 0, 0, 99, 99}

// 缩小大小
v.resize(3);                        // v = {1, 2, 3}
                                    // 末尾元素被删除

// resize到0
v.resize(0);                        // v变为空，但capacity不变
```

**resize vs reserve**：

```cpp
// reserve：只分配内存，不改变size
vector<int> v;
v.reserve(100);                     // size=0, capacity>=100

// resize：改变size，可能分配内存
vector<int> v;
v.resize(100);                      // size=100, capacity>=100
                                    // 100个元素都被初始化为0
```

### 4.6 clear()成员函数

```cpp
vector<int> v = {1, 2, 3, 4, 5};

v.clear();                          // size变为0，capacity不变
// v = {}，但内存仍保留
cout << v.size() << " " << v.capacity() << endl;  // 0 5

// 释放内存的方法
vector<int>().swap(v);              // 创建一个临时空vector并交换
// 或使用shrink_to_fit
v.shrink_to_fit();
```

### 4.7 容量管理的复杂度分析

| 操作 | 时间复杂度 | 说明 |
|------|------------|------|
| `size()` | O(1) | 直接返回成员变量 |
| `empty()` | O(1) | 比较size与0 |
| `capacity()` | O(1) | 直接返回成员变量 |
| `reserve(n)` | O(n) amortized | 可能重新分配内存 |
| `shrink_to_fit()` | O(n) | 可能收缩内存 |
| `resize(n)` | O(n) | 可能构造/析构元素 |
| `clear()` | O(k) | 析构所有元素（k为原size） |

## 5. 元素添加与删除

### 5.1 push_back()成员函数

```cpp
vector<int> v;

// 在末尾添加元素
v.push_back(1);                     // v = {1}
v.push_back(2);                     // v = {1, 2}
v.push_back(3);                     // v = {1, 2, 3}

// push_back的复杂度
// Amortized O(1)：大部分时候是O(1)，偶尔重新分配时O(n)
```

**Amortized分析**：假设初始容量为1，每次容量翻倍。插入n个元素的总代价为：1 + 2 + 4 + 8 + ... + n < 2n。因此每次插入的平均代价为O(1)。

### 5.2 emplace_back()成员函数（C++11）

```cpp
struct Point {
    int x, y;
    Point(int x, int y) : x(x), y(y) {}
};

vector<Point> points;

// push_back需要先构造临时对象
points.push_back(Point(1, 2));      // 临时对象构造 + 移动/拷贝

// emplace_back直接在容器内构造元素
points.emplace_back(1, 2);          // 直接在容器内存中构造
                                    // 避免了临时对象的构造和可能的移动

// 对于不可拷贝只可移动的类型特别重要
vector<unique_ptr<int>> ptrs;
ptrs.emplace_back(new int(42));     // 直接移动构造
```

**emplace vs push**：对于`push_back`，参数是要添加的元素的副本或要移动的对象；对于`emplace`，参数是构造元素所需的参数列表。

```cpp
// push_back 需要元素先存在
vector<string> v;
string s = "Hello";
v.push_back(s);                     // 拷贝构造
v.push_back(string("World"));       // 临时对象 + 可能移动
v.push_back("Hello World");         // 隐式转换 + 拷贝

// emplace 直接构造
v.emplace_back("Hello");            // 直接构造
v.emplace_back(string("World"));    // 仍然构造
v.emplace_back(5, 'x');             // string(5, 'x') = "xxxxx"
```

### 5.3 pop_back()成员函数

```cpp
vector<int> v = {1, 2, 3, 4, 5};

// 删除末尾元素
v.pop_back();                       // v = {1, 2, 3, 4}
v.pop_back();                       // v = {1, 2, 3}

// 复杂度 O(1)
// 注意：pop_back不会返回被删除的元素
int last = v.back();                // 先获取
v.pop_back();                       // 再删除

// 对空vector调用pop_back是未定义行为
if (!v.empty()) {
    v.pop_back();
}
```

### 5.4 insert()成员函数

```cpp
vector<int> v = {1, 2, 3, 4, 5};

// 在指定位置前插入元素
auto it = v.begin() + 2;            // 指向元素3
v.insert(it, 10);                   // v = {1, 2, 10, 3, 4, 5}

// 插入多个相同元素
v.insert(v.begin() + 1, 3, 99);     // 在位置1插入3个99
                                    // v = {1, 99, 99, 99, 2, 10, 3, 4, 5}

// 插入另一个容器的范围
vector<int> source = {100, 200};
v.insert(v.begin(), source.begin(), source.end());
                                    // v = {100, 200, 1, 99, 99, 99, 2, 10, 3, 4, 5}

// 插入初始化列表
v.insert(v.end(), {6, 7, 8});       // v末尾追加6, 7, 8

// 插入单个元素并返回迭代器
auto new_it = v.insert(v.begin() + 5, 42);
// new_it 指向新插入的元素
```

**insert的复杂度**：
- 插入位置越靠前，需要移动的元素越多
- 最坏情况O(n)：在开头插入需要移动所有元素
- 最好情况O(1)：在末尾插入（但可能触发重新分配）

### 5.5 emplace()成员函数（C++11）

```cpp
struct Data {
    int id;
    string name;
    Data(int id, const string& name) : id(id), name(name) {}
};

vector<Data> data;
data.reserve(10);

// emplace 在指定位置直接构造元素
auto it = data.begin() + 1;
data.emplace(it, 1, "Alice");       // 在位置1构造Data(1, "Alice")

// 等价于但可能更高效
it = data.begin();
data.insert(it, Data(2, "Bob"));    // 需要临时对象或移动
```

### 5.6 erase()成员函数

```cpp
vector<int> v = {1, 2, 3, 4, 5};

// 删除指定位置的元素
auto it = v.begin() + 2;            // 指向元素3
v.erase(it);                        // v = {1, 2, 4, 5}
                                    // 返回指向被删除元素后一个元素的迭代器

// 删除指定范围
it = v.begin() + 1;                 // 指向元素2
v.erase(it, it + 2);                // 删除2和4，v = {1, 5}
                                    // 返回删除区间的结束迭代器

// erase-remove 惯用法（删除所有匹配元素）
v = {1, 2, 3, 2, 4, 2, 5};
v.erase(remove(v.begin(), v.end(), 2), v.end());
                                    // v = {1, 3, 4, 5}
// remove 将所有不等于2的元素前移，返回指向新的末尾的迭代器
// erase 删除从该迭代器到原末尾的所有元素

// 删除满足条件的元素
v.erase(remove_if(v.begin(), v.end(),
                  [](int x) { return x % 2 == 0; }), v.end());
                                    // 删除所有偶数
```

**erase的复杂度**：O(n)，因为需要移动被删除元素之后的所有元素。

### 5.7 各种操作的复杂度总结

| 操作 | 时间复杂度 | 空间复杂度 |
|------|------------|------------|
| `push_back()` | O(1) amortized | - |
| `pop_back()` | O(1) | - |
| `insert()` | O(n) | - |
| `emplace()` | O(n) | - |
| `erase()` | O(n) | - |
| `emplace_back()` | O(1) amortized | - |
| `clear()` | O(n) | - |

### 5.8 使用场景示例

```cpp
// 场景1：动态数组 - 存储未知数量的输入
vector<int> input;
int x;
while (cin >> x) {
    input.push_back(x);
}

// 场景2：二维网格
int rows, cols;
cin >> rows >> cols;
vector<vector<int>> grid(rows, vector<int>(cols));
for (int i = 0; i < rows; ++i) {
    for (int j = 0; j < cols; ++j) {
        cin >> grid[i][j];
    }
}

// 场景3：邻接表（图的表示）
int n, m;
cin >> n >> m;
vector<vector<int>> adj(n);
for (int i = 0; i < m; ++i) {
    int u, v;
    cin >> u >> v;
    --u; --v;
    adj[u].push_back(v);
    adj[v].push_back(u);
}

// 场景4：滑动窗口
int k = 3;
vector<int> arr = {1, 3, -1, -3, 5, 3, 6, 7};
vector<int> results;
for (size_t i = 0; i + k <= arr.size(); ++i) {
    int max_val = arr[i];
    for (size_t j = 1; j < k; ++j) {
        max_val = max(max_val, arr[i + j]);
    }
    results.push_back(max_val);
}
```

## 6. 向量运算

### 6.1 比较运算

```cpp
vector<int> a = {1, 2, 3};
vector<int> b = {1, 2, 3};
vector<int> c = {1, 2, 4};

// 元素逐一比较（字典序）
bool eq = (a == b);                 // true
bool ne = (a != c);                 // true（3 != 4）
bool lt = (a < c);                  // true（3 < 4）
bool le = (a <= c);                 // true
bool gt = (a > c);                  // false
bool ge = (a >= c);                 // false
```

### 6.2 赋值运算

```cpp
vector<int> a = {1, 2, 3};
vector<int> b, c;

// operator=
b = a;                              // 拷贝赋值
b = {4, 5, 6};                      // 列表赋值（C++11）
b = vector<int>(10, -1);           // 临时vector赋值

// assign - 重新赋值
b.assign(5, 100);                   // b = {100, 100, 100, 100, 100}
b.assign(a.begin(), a.end());       // b = a
b.assign({1, 2, 3});                // b = {1, 2, 3}

// swap - 交换两个vector
a.swap(b);                          // a和b内容互换
swap(a, b);                         // 非成员函数版swap
```

### 6.3 swap操作

```cpp
vector<int> v1 = {1, 2, 3};
vector<int> v2 = {4, 5, 6};

v1.swap(v2);                        // v1 = {4, 5, 6}, v2 = {1, 2, 3}

// 快速清空vector并释放内存
vector<int> temp;
temp.swap(v1);                      // v1变为空，temp获得原v1的内容
```

## 7. 与数组的互操作

### 7.1 C风格数组转vector

```cpp
// 方法1：直接构造
int arr[] = {1, 2, 3, 4, 5};
vector<int> v(arr, arr + 5);        // 需要知道数组大小
vector<int> v2(arr, arr + sizeof(arr)/sizeof(arr[0]));

// 方法2：使用指针范围
int* begin_ptr = arr;
int* end_ptr = arr + 5;
vector<int> v3(begin_ptr, end_ptr);

// 方法3：初始化列表（C++11）
int arr2[] = {1, 2, 3};
vector<int> v4 = {arr2[0], arr2[1], arr2[2]};
```

### 7.2 vector转C风格数组

```cpp
vector<int> v = {1, 2, 3, 4, 5};

// 使用data()获取指针
int* arr = v.data();                // arr指向v[0]

// 但需要注意vector生命周期
process_array(v.data(), v.size());

// 不能直接获取数组引用，但可以模拟
// int (&arr_ref)[5] = reinterpret_cast<int(&)[5]>(*v.data());
// 这需要v.size()正好为5且未重新分配
```

### 7.3 多维数组

```cpp
// 二维vector
int rows = 3, cols = 4;

// 方法1：先指定外层大小
vector<vector<int>> matrix(rows, vector<int>(cols));
matrix[0][0] = 1;

// 方法2：使用初始化列表
vector<vector<int>> mat = {
    {1, 2, 3, 4},
    {5, 6, 7, 8},
    {9, 10, 11, 12}
};

// 方法3：三维vector
vector<vector<vector<int>>> cube(3, 
    vector<vector<int>>(3, vector<int>(3, 0)));

// 访问元素
int x = matrix[1][2];               // 第2行第3列
```

## 8. 算法配合使用

### 8.1 排序相关

```cpp
#include <algorithm>

vector<int> v = {5, 2, 8, 1, 9, 3};

// sort - 排序
sort(v.begin(), v.end());           // 升序：{1, 2, 3, 5, 8, 9}
sort(v.begin(), v.end(), greater<int>());  // 降序

// 自定义比较
sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;                   // 降序
});

// 部分排序 - 只排前n个元素
vector<int> partial = {5, 2, 8, 1, 9, 3};
partial_n(partial.begin(), partial.begin() + 3, partial.end());
// 前3个元素是最小的3个：{1, 2, 3}

// nth_element - 找到第n小的元素
vector<int> nth_test = {5, 2, 8, 1, 9, 3, 7, 4, 6};
nth_element(nth_test.begin(), nth_test.begin() + 4, nth_test.end());
// 现在第5个元素（索引4）是中位数，其他元素不保证顺序但比它小或大
```

### 8.2 查找相关

```cpp
vector<int> v = {1, 2, 3, 4, 5, 3, 6};

// find - 查找元素
auto it = find(v.begin(), v.end(), 3);
if (it != v.end()) {
    size_t pos = distance(v.begin(), it);  // 第一个3的位置
}

// find_if - 查找满足条件的元素
auto it2 = find_if(v.begin(), v.end(), [](int x) {
    return x > 4;
});                                      // 第一个大于4的元素

// find_if_not - 查找不满足条件的元素
auto it3 = find_if_not(v.begin(), v.end(), [](int x) {
    return x % 2 == 1;
});                                      // 第一个偶数

// search - 查找子序列
vector<int> pattern = {3, 4};
auto it4 = search(v.begin(), v.end(), pattern.begin(), pattern.end());

// binary_search - 二分查找（需先排序）
sort(v.begin(), v.end());
bool found = binary_search(v.begin(), v.end(), 4);

// lower_bound - 第一个不小于value的位置
auto lower = lower_bound(v.begin(), v.end(), 3);

// upper_bound - 第一个大于value的位置
auto upper = upper_bound(v.begin(), v.end(), 3);
```

### 8.3 变换相关

```cpp
vector<int> v = {1, 2, 3, 4, 5};

// transform - 转换元素
vector<int> result;
result.resize(v.size());
transform(v.begin(), v.end(), result.begin(),
          [](int x) { return x * 2; });   // {2, 4, 6, 8, 10}

// copy - 复制
vector<int> copy;
copy(v.begin(), v.end(), back_inserter(copy));

// remove - 移除元素（移动）
vector<int> v2 = {1, 2, 3, 2, 4, 2, 5};
v2.erase(remove(v2.begin(), v2.end(), 2), v2.end());  // {1, 3, 4, 5}

// reverse - 反转
reverse(v.begin(), v.end());              // {5, 4, 3, 2, 1}

// unique - 去重（需先排序）
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());

// accumulate - 求和（C++11前在numeric中）
#include <numeric>
int sum = accumulate(v.begin(), v.end(), 0);  // 初始值为0
```

### 8.4 堆操作

```cpp
vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6};

// make_heap - 创建堆
make_heap(v.begin(), v.end());
// v现在是最大堆：{9, 6, 4, 1, 5, 1, 2, 3}

// push_heap - 添加元素
v.push_back(8);
push_heap(v.begin(), v.end());          // 维护堆性质

// pop_heap - 移除堆顶
pop_heap(v.begin(), v.end());
int max_elem = v.back();                 // 最大元素
v.pop_back();

// sort_heap - 排序堆
sort_heap(v.begin(), v.end());           // 升序排序
```

## 9. 竞赛中的常见模式

### 9.1 滑动窗口最大值

```cpp
#include <deque>
#include <vector>

vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;                       // 存储索引
    vector<int> result;
    
    for (int i = 0; i < nums.size(); ++i) {
        // 移除窗口外的元素
        if (!dq.empty() && dq.front() <= i - k) {
            dq.pop_front();
        }
        
        // 移除小于当前元素的元素
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
```

### 9.2 并查集（Union-Find）

```cpp
class UnionFind {
private:
    vector<int> parent, rank;
public:
    UnionFind(int n) : parent(n), rank(n, 0) {
        iota(parent.begin(), parent.end(), 0);
    }
    
    int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]);  // 路径压缩
        }
        return parent[x];
    }
    
    void unite(int x, int y) {
        int px = find(x), py = find(y);
        if (px == py) return;
        
        if (rank[px] < rank[py]) swap(px, py);
        parent[py] = px;
        if (rank[px] == rank[py]) rank[px]++;
    }
    
    bool connected(int x, int y) {
        return find(x) == find(y);
    }
};
```

### 9.3 动态规划数组

```cpp
// 0-1背包问题
int knapsack(int W, const vector<int>& weights, const vector<int>& values) {
    int n = weights.size();
    vector<int> dp(W + 1, 0);
    
    for (int i = 0; i < n; ++i) {
        for (int w = W; w >= weights[i]; --w) {
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i]);
        }
    }
    
    return dp[W];
}

// 最长递增子序列
int lengthOfLIS(vector<int>& nums) {
    if (nums.empty()) return 0;
    
    vector<int> dp(nums.size(), 1);
    int max_len = 1;
    
    for (int i = 1; i < nums.size(); ++i) {
        for (int j = 0; j < i; ++j) {
            if (nums[j] < nums[i]) {
                dp[i] = max(dp[i], dp[j] + 1);
            }
        }
        max_len = max(max_len, dp[i]);
    }
    
    return max_len;
}
```

### 9.4 邻接表表示图

```cpp
class Graph {
private:
    int n;
    vector<vector<int>> adj;
public:
    Graph(int n) : n(n), adj(n) {}
    
    void addEdge(int u, int v) {
        adj[u].push_back(v);
    }
    
    void addUndirectedEdge(int u, int v) {
        adj[u].push_back(v);
        adj[v].push_back(u);
    }
    
    const vector<int>& neighbors(int u) const {
        return adj[u];
    }
    
    vector<int> neighbors(int u) {
        return adj[u];
    }
    
    int size() const { return n; }
};
```

## 10. 性能优化技巧

### 10.1 使用reserve()

```cpp
// 不好的写法
vector<int> v;
for (int i = 0; i < 1000000; ++i) {
    v.push_back(i * i);         // 可能触发多次重新分配
}

// 好的写法
vector<int> v;
v.reserve(1000000);             // 预先分配足够空间
for (int i = 0; i < 1000000; ++i) {
    v.push_back(i * i);         // 不会重新分配
}
```

### 10.2 使用emplace()

```cpp
struct Expensive {
    string name;
    int value;
    Expensive(const string& n, int v) : name(n), value(v) {}
};

// 不好的写法
vector<Expensive> v;
v.push_back(Expensive("test", 42));  // 临时对象 + 移动构造

// 好的写法
v.emplace_back("test", 42);          // 直接构造
```

### 10.3 使用迭代器范围

```cpp
// 不好的写法
vector<int> source = {1, 2, 3, 4, 5};
vector<int> dest;
for (int x : source) {
    dest.push_back(x * 2);
}

// 好的写法
vector<int> dest(source.size());
transform(source.begin(), source.end(), dest.begin(),
          [](int x) { return x * 2; });

// 更好的写法（直接操作原vector）
transform(v.begin(), v.end(), v.begin(),
          [](int x) { return x * 2; });
```

### 10.4 避免不必要的拷贝

```cpp
// 不好的写法
void process(vector<int> v) {        // 按值传递，拷贝整个vector
    // ...
}

// 好的写法（按引用传递）
void process(const vector<int>& v) { // 不拷贝，只读访问
    // ...
}

// 如果需要修改
void process(vector<int>& v) {       // 按引用传递，避免拷贝
    // ...
}

// 返回vector时使用移动语义
vector<int> createData() {
    vector<int> v;
    // ... 填充v
    return v;                        // C++17保证RVO或移动
}

// 或显式移动
vector<int> createData() {
    vector<int> v;
    // ...
    return std::move(v);             // 显式移动
}
```

## 11. 常见陷阱与注意事项

### 11.1 迭代器失效

```cpp
vector<int> v = {1, 2, 3, 4, 5};
auto it = v.begin() + 2;            // 指向3

v.insert(v.begin(), 0);             // 在开头插入
// it 现在可能失效！

v.push_back(6);                     // 可能导致重新分配，it失效
```

**解决方案**：在修改vector后重新获取迭代器，或确保使用reserve()预留空间。

### 11.2 空vector访问

```cpp
vector<int> v;

// 以下操作都是未定义行为
// int x = v[0];
// int x = v.front();
// int x = v.back();
// v.pop_back();
```

**解决方案**：始终在使用前检查v.empty()。

### 11.3 下标越界

```cpp
vector<int> v = {1, 2, 3};

// 以下是未定义行为
// int x = v[10];
// int x = v[-1];

// 安全做法：使用at()进行边界检查
try {
    int x = v.at(10);
} catch (const out_of_range&) {
    cout << "Index out of range" << endl;
}
```

### 11.4 在循环中修改vector

```cpp
vector<int> v = {1, 2, 3, 4, 5};

// 在范围for循环中修改vector
for (int& x : v) {
    if (x % 2 == 0) {
        v.erase(v.begin() + (x/2 - 1));  // 危险！迭代器失效
    }
}
```

**解决方案**：使用迭代器或索引，并在修改后重新计算位置。

### 11.5 capacity与size混淆

```cpp
vector<int> v(100);
cout << v.size() << endl;           // 100
cout << v.capacity() << endl;       // >= 100

v.push_back(1);                     // 不会重新分配
cout << v.size() << endl;           // 101
cout << v.capacity() << endl;       // 仍为100或更大

v.resize(50);                       // 改变size
cout << v.size() << endl;           // 50
cout << v.capacity() << endl;       // 仍为100或更大
```

## 12. vector与其他容器的比较

### 12.1 vs array

| 特性 | vector | array |
|------|--------|-------|
| 大小 | 动态 | 固定 |
| 内存分配 | 自动管理 | 栈上或静态 |
| 性能 | 有轻微开销 | 最高 |
| 初始化 | 灵活 | 需编译时常量大小 |

### 12.2 vs deque

| 特性 | vector | deque |
|------|--------|-------|
| 头部插入 | O(n) | O(1) amortized |
| 尾部插入 | O(1) amortized | O(1) amortized |
| 随机访问 | O(1) | O(1) |
| 内存连续 | 是 | 否 |

### 12.3 vs list

| 特性 | vector | list |
|------|--------|------|
| 随机访问 | O(1) | O(n) |
| 插入/删除（中间） | O(n) | O(1) |
| 内存开销 | 低 | 高 |
| 缓存友好 | 是 | 否 |

## 13. 复杂度汇总

| 操作 | 时间复杂度 | 空间复杂度 |
|------|------------|------------|
| 随机访问 | O(1) | - |
| push_back | O(1) amortized | - |
| pop_back | O(1) | - |
| insert | O(n) | - |
| erase | O(n) | - |
| clear | O(n) | - |
| resize | O(n) | - |
| find | O(n) | - |
| sort | O(n log n) | O(n) |

## 14. 总结

`vector`是ACM竞赛中最常用的容器，其优势包括：

1. **高效的随机访问**：O(1)时间复杂度的下标访问
2. **动态增长能力**：自动管理内存，避免数组固定大小的限制
3. **与数组兼容**：`data()`成员函数提供底层数组指针
4. **丰富的算法支持**：可与`<algorithm>`中的所有算法配合使用
5. **良好的缓存局部性**：连续存储提高CPU缓存命中率

掌握`vector`的使用技巧，合理使用`reserve()`预分配内存，避免不必要的拷贝操作，是在竞赛中高效使用`vector`的关键。