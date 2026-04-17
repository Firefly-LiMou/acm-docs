# list 容器详解

`<list>`头文件实现了C++标准库中的双向链表容器`std::list`。与`vector`不同，`list`采用节点式存储，每个元素都存储在独立的节点中，节点之间通过指针链接。这种非连续存储的特性使得`list`在任意位置的插入和删除操作都具有常数时间复杂度，但在随机访问方面表现不佳。在ACM竞赛中，`list`适用于需要频繁在容器中间进行插入删除操作、且不需要随机访问的场景。

## 1. list类概述

`std::list`是一个双向链表容器，提供双向迭代器，支持在常数时间内于任意位置插入和删除元素。链表的节点结构使得每个元素都包含指向前驱和后继节点的指针，这种设计牺牲了内存连续性和随机访问能力，换来了插入删除操作的高效性。`list`还提供了特殊的成员函数`splice`，可以在O(1)时间内将一个list中的元素移动到另一个list中的任意位置，这是其他容器无法提供的独特功能。

### 1.1 头文件包含方式

```cpp
#include <list>            // 标准方式
#include <bits/stdc++.h>   // 竞赛常用
using namespace std;       // 使用std命名空间
```

### 1.2 核心特性

`list`的核心特性体现在以下几个方面：首先是常量时间的插入删除操作，无论在链表的头部、尾部还是中间位置，插入或删除一个元素都只需要调整相邻节点的指针，时间复杂度为O(1)；其次是不发生迭代器失效的问题，除了被删除的迭代器外，其他迭代器在插入删除操作中都不会失效，这与`vector`形成鲜明对比；此外，`list`还支持双向遍历，可以使用`rbegin()`/`rend()`进行反向遍历；最后是`list`支持常数时间的合并和排序操作（针对链表优化的`merge`和`sort`算法）。

## 2. 构造与初始化

### 2.1 默认构造函数

```cpp
// 创建空链表
list<int> l1;                      // 空list，size=0

list<string> l2;                   // 字符串链表

list<list<int>> l3;                // 嵌套list（邻接表常用）
```

### 2.2 拷贝构造与移动构造

```cpp
// 拷贝构造 - 创建完全独立的副本
list<int> original = {1, 2, 3, 4, 5};
list<int> copy1(original);         // 完整的深拷贝
list<int> copy2 = original;        // 同样是拷贝构造

// 移动构造 - 高效转移资源（C++11）
list<int> moved = std::move(original);  // original变为空，moved获得资源
```

### 2.3 初始化列表构造（C++11）

```cpp
// 使用花括号初始化列表
list<int> l1 = {1, 2, 3, 4, 5};    // 列表初始化
list<int> l2{1, 2, 3, 4, 5};       // 直接初始化

// 空list初始化
list<int> l3;                      // 默认空
list<int> l4 = {};                 // 空列表初始化
```

### 2.4 范围构造

```cpp
// 使用迭代器范围构造
int arr[] = {1, 2, 3, 4, 5};
list<int> l1(arr, arr + 5);                    // 从数组构造

vector<int> v = {1, 2, 3, 4, 5};
list<int> l2(v.begin(), v.end());              // 从vector构造

list<int> source = {1, 2, 3, 4, 5};
list<int> l3(source.begin() + 1, source.end() - 1);  // 2, 3, 4
```

### 2.5 构造函数的复杂度分析

| 构造函数类型 | 时间复杂度 | 说明 |
|--------------|------------|------|
| 默认构造 | O(1) | 仅初始化空链表 |
| 拷贝构造 | O(n) | 复制所有节点 |
| 移动构造 | O(1) | 仅转移指针 |
| 范围构造 | O(n) | 复制指定范围的元素 |
| 列表构造 | O(n) | 构造列表中的n个元素 |

## 3. 元素访问

### 3.1 front()和back()成员函数

```cpp
list<int> l = {10, 20, 30, 40, 50};

// front() - 访问第一个元素
int first = l.front();             // 10
// 对空list调用front()是未定义行为

// back() - 访问最后一个元素
int last = l.back();               // 50
// 对空list调用back()是未定义行为

// 可修改
l.front() = 100;                   // l[0] = 100
l.back() = 500;                    // l[4] = 500

// 安全访问模式
if (!l.empty()) {
    int f = l.front();
    int b = l.back();
}
```

### 3.2 迭代器访问

```cpp
list<int> l = {10, 20, 30, 40, 50};

// 正向迭代器
for (auto it = l.begin(); it != l.end(); ++it) {
    cout << *it << ' ';            // 10 20 30 40 50
}

// 使用范围for循环（C++11）
for (int& x : l) {
    x *= 2;                        // 修改每个元素
}

// const迭代器（只读）
for (auto cit = l.cbegin(); cit != l.cend(); ++cit) {
    cout << *cit << ' ';
}

// 反向迭代器
for (auto rit = l.rbegin(); rit != l.rend(); ++rit) {
    cout << *rit << ' ';           // 50 40 30 20 10
}

// 使用迭代器时不能使用+或-运算，只能使用++和--
// 错误：auto it = l.begin() + 3;  // 编译错误！
// 正确：
auto it = l.begin();
advance(it, 3);                    // 或使用next(it, 3)
```

### 3.3 迭代器导航函数

```cpp
#include <iterator>

list<int> l = {1, 2, 3, 4, 5};

// advance - 迭代器前进n步
auto it = l.begin();
advance(it, 3);                    // 指向元素4

// next和prev - 返回前进/后退n步后的迭代器（C++11）
it = l.begin();
auto next_it = next(it, 2);        // 指向元素3
auto prev_it = prev(it, 1);        // 指向元素1

// distance - 计算两个迭代器之间的距离
auto it1 = l.begin();
auto it2 = l.end();
ptrdiff_t dist = distance(it1, it2);  // 5
```

**重要说明**：`list`的迭代器是双向迭代器，不是随机访问迭代器，因此不支持`+`、`-`、`[]`等操作。对于`advance`和`distance`，`list`迭代器的实现可能是O(n)的。

### 3.4 为什么list不支持随机访问

```cpp
list<int> l = {10, 20, 30, 40, 50};

// 以下操作对list无效
// l[2];                  // 错误：没有operator[]
// *(l.begin() + 2);      // 错误：没有+运算符

// 必须使用迭代器遍历
auto it = l.begin();
for (int i = 0; i < 2; ++i) ++it;  // 移动两步
cout << *it << endl;               // 30
```

## 4. 容量管理

### 4.1 size()和empty()成员函数

```cpp
list<int> l = {1, 2, 3, 4, 5};

// size() - 返回元素数量
size_t sz = l.size();              // 5

// empty() - 判断是否为空（比size() == 0更高效）
bool is_empty = l.empty();         // false
list<int> empty_l;
bool is_empty2 = empty_l.empty();  // true
```

### 4.2 max_size()成员函数

```cpp
list<int> l;
size_t max_sz = l.max_size();      // 非常大的值
// max_size取决于系统可用内存和实现
```

### 4.3 容量特点说明

```cpp
// list没有capacity()函数！
// list在插入元素时自动分配新节点
// 不存在"预分配"的概念

list<int> l;
l.push_back(1);                    // 分配第一个节点
l.push_back(2);                    // 分配第二个节点
// 每个push_back都可能导致内存分配
// 但不会像vector那样复制已有元素
```

**关键区别**：与`vector`不同，`list`不需要连续的内存空间，不需要预先分配固定大小的内存块。每个元素都是独立分配的节点，通过指针链接在一起。

## 5. 元素添加与删除

### 5.1 push_back()和push_front()

```cpp
list<int> l;

// 在末尾添加元素
l.push_back(1);                    // {1}
l.push_back(2);                    // {1, 2}
l.push_back(3);                    // {1, 2, 3}

// 在头部添加元素
l.push_front(0);                   // {0, 1, 2, 3}
l.push_front(-1);                  // {-1, 0, 1, 2, 3}

// 复杂度：都是O(1)
```

### 5.2 emplace_back()和emplace_front()（C++11）

```cpp
struct Point {
    int x, y;
    Point(int x, int y) : x(x), y(y) {}
};

list<Point> points;

// emplace_back - 直接在末尾构造元素
points.emplace_back(1, 2);         // 直接构造Point(1, 2)

// emplace_front - 直接在头部构造元素
points.emplace_front(0, 0);        // 直接构造Point(0, 0)

// 比push版本更高效，避免临时对象
points.push_back(Point(3, 4));     // 临时对象 + 移动构造
points.emplace_back(3, 4);         // 直接构造
```

### 5.3 pop_back()和pop_front()

```cpp
list<int> l = {1, 2, 3, 4, 5};

// 删除末尾元素
l.pop_back();                      // {1, 2, 3, 4}

// 删除头部元素
l.pop_front();                     // {2, 3, 4}

// 复杂度：都是O(1)

// 注意：这些函数不返回被删除的元素
int last = l.back();               // 先获取
l.pop_back();

int first = l.front();             // 先获取
l.pop_front();
```

### 5.4 insert()成员函数

```cpp
list<int> l = {1, 2, 5};

// 在指定位置前插入元素
auto it = l.begin();
++it;                              // 指向元素2
l.insert(it, 3);                   // {1, 3, 2, 5}

// 插入多个相同元素
l.insert(l.begin(), 3, 0);         // 在开头插入3个0: {0, 0, 0, 1, 3, 2, 5}

// 插入另一个容器的范围
list<int> source = {100, 200};
l.insert(l.begin(), source.begin(), source.end());
                                    // {100, 200, 0, 0, 0, 1, 3, 2, 5}

// 插入初始化列表
l.insert(l.end(), {6, 7, 8});      // {100, 200, 0, 0, 0, 1, 3, 2, 5, 6, 7, 8}

// insert的复杂度：O(1)（在迭代器指向的位置插入）
// 因为不需要移动其他元素
```

### 5.5 emplace()成员函数（C++11）

```cpp
struct Data {
    int id;
    string name;
    Data(int id, const string& name) : id(id), name(name) {}
};

list<Data> data;
auto it = data.begin();

// emplace 在指定位置直接构造元素
data.emplace(it, 1, "Alice");      // 在位置1构造Data(1, "Alice")
```

### 5.6 erase()成员函数

```cpp
list<int> l = {1, 2, 3, 4, 5};

// 删除指定位置的元素
auto it = l.begin();
++it;                              // 指向元素2
l.erase(it);                       // {1, 3, 4, 5}
// 返回指向被删除元素后一个元素的迭代器

// 删除多个元素
it = l.begin();
l.erase(it, it + 2);               // 删除前两个元素: {4, 5}
// list迭代器不支持+运算符，需要这样写：
it = l.begin();
auto it2 = it;
++it2; ++it2;                     // it2指向第3个元素
l.erase(it, it2);                  // {4, 5}

// erase-remove 惯用法
l = {1, 2, 3, 2, 4, 2, 5};
l.erase(remove(l.begin(), l.end(), 2), l.end());
                                    // {1, 3, 4, 5}

// erase的复杂度：O(1)
```

### 5.7 各种操作的复杂度

| 操作 | 时间复杂度 | 说明 |
|------|------------|------|
| `push_back()` | O(1) | 尾部添加 |
| `push_front()` | O(1) | 头部添加 |
| `pop_back()` | O(1) | 尾部删除 |
| `pop_front()` | O(1) | 头部删除 |
| `insert()` | O(1) | 指定位置插入 |
| `erase()` | O(1) | 指定位置删除 |
| `emplace()` | O(1) | 直接构造插入 |

## 6. list特有操作

### 6.1 splice() - 链表拼接操作

`splice`是`list`独有的强大功能，可以在O(1)时间内将元素从一个list移动到另一个list。

```cpp
list<int> l1 = {1, 2, 3};
list<int> l2 = {4, 5, 6};

// 移动l2的全部元素到l1的末尾
l1.splice(l1.end(), l2);           // l1 = {1, 2, 3, 4, 5, 6}, l2 = {}

// 移动l2的部分元素
l1 = {1, 2, 3};
l2 = {4, 5, 6};
auto it = l2.begin();
++it;                              // 指向4
l1.splice(l1.begin(), l2, it);     // 移动l2的第一个元素到l1头部
                                    // l1 = {5, 1, 2, 3}, l2 = {4, 6}

// 移动l2的一个范围
l1 = {1, 2, 3};
l2 = {4, 5, 6};
auto first = l2.begin();
auto last = l2.begin();
++last; ++last;                    // 指向6
l1.splice(l1.end(), l2, first, last);  // 移动4, 5到l1末尾
                                    // l1 = {1, 2, 3, 4, 5}, l2 = {6}
```

**splice使用场景**：

```cpp
// 场景1：实现LRU缓存（最近最少使用）
class LRUCache {
private:
    size_t capacity;
    list<pair<int, int>> lru;      // {key, value}
    unordered_map<int, list<pair<int, int>>::iterator> pos;
    
public:
    LRUCache(size_t cap) : capacity(cap) {}
    
    int get(int key) {
        auto it = pos.find(key);
        if (it == pos.end()) return -1;
        
        // 将访问的元素移到链表头部
        lru.splice(lru.begin(), lru, it->second);
        return it->second->second;
    }
    
    void put(int key, int value) {
        auto it = pos.find(key);
        if (it != pos.end()) {
            // 更新并移到头部
            it->second->second = value;
            lru.splice(lru.begin(), lru, it->second);
            return;
        }
        
        if (pos.size() == capacity) {
            // 删除尾部元素
            auto last = lru.back();
            pos.erase(last.first);
            lru.pop_back();
        }
        
        lru.emplace_front(key, value);
        pos[key] = lru.begin();
    }
};

// 场景2：按条件分区
list<int> positives, negatives;
list<int> numbers = {-3, 1, -2, 4, -1, 2, -5, 3};

for (int x : numbers) {
    if (x > 0) {
        positives.push_back(x);
    } else {
        negatives.push_back(x);
    }
    // 也可以使用splice从原list移动
}

// 场景3：归并两个已排序链表
list<int> merge_sorted(list<int>& a, list<int>& b) {
    list<int> result;
    auto ait = a.begin(), bit = b.begin();
    
    while (ait != a.end() && bit != b.end()) {
        if (*ait < *bit) {
            result.splice(result.end(), a, ait++);
        } else {
            result.splice(result.end(), b, bit++);
        }
    }
    
    if (ait != a.end()) {
        result.splice(result.end(), a, ait, a.end());
    }
    if (bit != b.end()) {
        result.splice(result.end(), b, bit, b.end());
    }
    
    return result;
}
```

### 6.2 remove()和remove_if()

```cpp
list<int> l = {1, 2, 3, 2, 4, 2, 5};

// remove - 删除所有等于指定值的元素
l.remove(2);                       // {1, 3, 4, 5}

// remove_if - 删除所有满足条件的元素
l = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
l.remove_if([](int x) { return x % 2 == 0; });  // 删除所有偶数: {1, 3, 5, 7, 9}

// 复杂度：O(n)
```

### 6.3 unique()

```cpp
list<int> l = {1, 1, 2, 2, 2, 3, 3, 4, 4, 4, 5};

// unique - 删除连续重复元素
l.unique();                        // {1, 2, 3, 4, 5}

// 带比较函数的unique
l = {1, 2, 2, 3, 3, 3, 2, 2, 1};
l.unique([](int a, int b) { return abs(a - b) <= 1; });
                                    // {1, 2, 3, 2, 1}
// 解释：1和2相差1，保留第一个2；2和2相同，删除；3和3相同，删除；3和2相差1，保留第一个2；2和1相差1，保留

// 通常先排序再unique
l.sort();
l.unique();                        // 完全去重
```

### 6.4 merge()

```cpp
list<int> l1 = {1, 3, 5, 7, 9};
list<int> l2 = {2, 4, 6, 8, 10};

// merge - 合并两个已排序链表（l2被清空）
l1.merge(l2);                      // l1 = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}, l2 = {}

// 带比较函数的merge
l1 = {9, 7, 5, 3, 1};
l2 = {10, 8, 6, 4, 2};
l1.merge(l2, greater<int>());      // l1 = {10, 9, 8, 7, 6, 5, 4, 3, 2, 1}

// 注意：merge要求两个链表都已排序（或按比较函数排序）
```

### 6.5 sort()

```cpp
list<int> l = {5, 3, 1, 4, 2};

// sort - 链表排序
l.sort();                          // {1, 2, 3, 4, 5}

// 降序排序
l.sort(greater<int>());            // {5, 4, 3, 2, 1}

// 自定义比较
l.sort([](int a, int b) {          // 绝对值排序
    return abs(a) < abs(b);
});
```

**为什么list需要自己的sort**：标准库的`std::sort`算法要求随机访问迭代器，而`list`的迭代器是双向迭代器。C++标准库为`list`提供了成员函数`sort`，该实现针对链表进行了优化，可以实现原地排序（不需要额外内存），时间复杂度为O(n log n)。

### 6.6 reverse()

```cpp
list<int> l = {1, 2, 3, 4, 5};

// reverse - 反转链表
l.reverse();                       // {5, 4, 3, 2, 1}

// 复杂度：O(n)
```

## 7. list特有操作复杂度汇总

| 操作 | 时间复杂度 | 说明 |
|------|------------|------|
| `splice()` | O(1) | 任意位置拼接 |
| `remove()` | O(n) | 删除所有匹配元素 |
| `remove_if()` | O(n) | 删除满足条件的元素 |
| `unique()` | O(n) | 删除连续重复 |
| `merge()` | O(n+m) | 合并两个链表 |
| `sort()` | O(n log n) | 链表排序 |
| `reverse()` | O(n) | 反转链表 |

## 8. 与vector的比较

### 8.1 复杂度对比

| 操作 | vector | list |
|------|--------|------|
| 随机访问 | O(1) | O(n) |
| 头部插入/删除 | O(n) | O(1) |
| 尾部插入/删除 | O(1) amortized | O(1) |
| 中间插入/删除 | O(n) | O(1) |
| 迭代器失效 | 可能失效 | 不失效 |
| 内存连续 | 是 | 否 |
| 缓存友好 | 是 | 否 |

### 8.2 何时使用list

**适合使用list的场景**：

```cpp
// 场景1：频繁在中间位置插入删除
list<int> l;
auto it = l.begin();
for (int i = 1; i <= 1000000; ++i) {
    l.insert(it, i);               // O(1) per insertion
    ++it;
}

// 场景2：需要迭代器在操作中保持有效
vector<int> v = {1, 2, 3, 4, 5};
auto it = v.begin() + 2;           // 指向3
v.insert(v.begin(), 0);            // it可能失效！
cout << *it << endl;               // 未定义行为

list<int> l = {1, 2, 3, 4, 5};
auto lit = l.begin();
++lit; ++lit;                      // 指向3
l.insert(l.begin(), 0);            // lit仍然有效
cout << *lit << endl;              // 3

// 场景3：LRU缓存等需要频繁移动元素的应用
// 场景4：需要O(1)时间复杂度的删除操作（已知迭代器位置）
```

**不适合使用list的场景**：

```cpp
// 场景1：需要随机访问
vector<int> v(100000);
int x = v[50000];                  // O(1)

list<int> l(100000);
auto it = l.begin();
advance(it, 50000);                // O(50000)

// 场景2：需要二分查找
sort(v.begin(), v.end());
bool found = binary_search(v.begin(), v.end(), 50000);  // O(log n)

// 场景3：需要良好的缓存局部性
// vector连续存储，CPU缓存命中率更高
```

### 8.3 内存使用对比

```cpp
// vector内存布局
// [1][2][3][4][5]
// 连续内存，开销：3个指针（begin, end, end_of_storage）

// list内存布局
// [1]<->[2]<->[3]<<->[4]<->[5]
// 每个节点：数据 + 前驱指针 + 后继指针
// 假设int占4字节，指针占8字节（64位系统）：
// vector：每元素4字节 + 少量整体开销
// list：每元素4 + 8 + 8 = 20字节（可能对齐到24字节）
```

## 9. 竞赛中使用list的实用模式

### 9.1 实现双端队列（deque的简化版）

```cpp
template<typename T>
class Deque {
private:
    list<T> data;
public:
    void push_front(const T& x) { data.push_front(x); }
    void push_back(const T& x) { data.push_back(x); }
    void pop_front() { data.pop_front(); }
    void pop_back() { data.pop_back(); }
    T& front() { return data.front(); }
    T& back() { return data.back(); }
    bool empty() const { return data.empty(); }
    size_t size() const { return data.size(); }
};
```

### 9.2 实现有序链表

```cpp
template<typename T>
class SortedList {
private:
    list<T> data;
public:
    void insert(const T& x) {
        auto it = data.begin();
        while (it != data.end() && *it < x) {
            ++it;
        }
        data.insert(it, x);
    }
    
    bool contains(const T& x) const {
        return find(data.begin(), data.end(), x) != data.end();
    }
    
    void remove(const T& x) {
        data.remove(x);
    }
    
    // 其他委托方法...
};
```

### 9.3 实现约瑟夫环问题

```cpp
// 约瑟夫环问题
int josephus(int n, int k) {
    if (n == 1) return 0;
    
    list<int> people;
    for (int i = 0; i < n; ++i) {
        people.push_back(i);
    }
    
    auto it = people.begin();
    while (people.size() > 1) {
        // 移动k-1步
        for (int i = 1; i < k; ++i) {
            ++it;
            if (it == people.end()) {
                it = people.begin();
            }
        }
        // 删除当前元素（下一个开始位置）
        it = people.erase(it);
        if (it == people.end()) {
            it = people.begin();
        }
    }
    
    return people.front();
}
```

### 9.4 使用splice实现高效的移动操作

```cpp
// 在O(1)时间内将元素从一个位置移动到另一个位置
list<int> l = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

// 将第5个元素移动到开头
auto it = l.begin();
advance(it, 4);                    // 指向元素5
auto to_move = it;
++it;                              // it指向元素6
l.splice(l.begin(), l, to_move);   // 将5移动到开头
// 结果：{5, 1, 2, 3, 4, 6, 7, 8, 9, 10}

// 将偶数移动到末尾
l = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
list<int> evens;
for (auto it = l.begin(); it != l.end(); ) {
    if (*it % 2 == 0) {
        evens.splice(evens.end(), l, it++);
    } else {
        ++it;
    }
}
// l: {1, 3, 5, 7, 9}
// evens: {2, 4, 6, 8, 10}
```

## 10. 常见陷阱与注意事项

### 10.1 迭代器不支持随机访问

```cpp
list<int> l = {1, 2, 3, 4, 5};

// 错误写法（编译错误）
// auto it = l.begin() + 2;
// if (l[0] < l[1]) ...

// 正确写法
auto it = l.begin();
advance(it, 2);                    // 移动两步
// 或
it = next(l.begin(), 2);
```

### 10.2 空容器操作

```cpp
list<int> l;

// 以下操作都是未定义行为
// l.front()
// l.back()
// l.pop_front()
// l.pop_back()
```

**解决方案**：始终在操作前检查`l.empty()`。

### 10.3 splice后迭代器有效性

```cpp
list<int> l1 = {1, 2, 3};
list<int> l2 = {4, 5, 6};
auto it = l1.begin();              // 指向1

l1.splice(l1.end(), l2);           // 将l2拼接到l1末尾
// it 仍然有效，指向1
```

**重要**：`splice`操作不会使任何迭代器失效，包括指向被移动元素的迭代器。被移动的元素在新位置仍然有效。

### 10.4 erase返回值的处理

```cpp
list<int> l = {1, 2, 3, 4, 5};

auto it = l.begin();
++it;                              // 指向2
it = l.erase(it);                  // 删除2，返回指向3的迭代器
cout << *it << endl;               // 3

// 不使用返回值，迭代器会失效
it = l.begin();
++it;
l.erase(it);                       // 删除2，但it没有更新
// cout << *it << endl;           // 未定义行为！
```

### 10.5 remove和erase的区别

```cpp
list<int> l = {1, 2, 3, 2, 4, 2, 5};

// remove删除所有等于指定值的元素，返回新末尾迭代器
auto new_end = l.remove(2);        // l中所有2被删除
l.erase(new_end, l.end());         // 清除

// 或者更简单（erase-remove惯用法）
l = {1, 2, 3, 2, 4, 2, 5};
l.remove(2);                       // 直接删除
```

## 11. 复杂度总结

| 操作类型 | 操作 | 时间复杂度 |
|----------|------|------------|
| 访问 | `front()`, `back()` | O(1) |
| 插入 | `push_front()`, `push_back()` | O(1) |
| 插入 | `insert()`, `emplace()` | O(1) |
| 删除 | `pop_front()`, `pop_back()` | O(1) |
| 删除 | `erase()` | O(1) |
| 搜索 | `find()` | O(n) |
| 排序 | `sort()` | O(n log n) |
| 拼接 | `splice()` | O(1) |
| 其他 | `remove()`, `unique()`, `reverse()` | O(n) |

## 12. 总结

`list`是C++标准库中唯一的双向链表容器，其核心优势在于：

1. **O(1)时间的任意位置插入删除**：不需要移动其他元素
2. **迭代器稳定性**：插入删除不会使其他迭代器失效
3. **特有的splice操作**：O(1)时间复杂度的元素移动
4. **链表优化的sort**：成员函数sort使用原地排序算法

在ACM竞赛中，`list`适用于以下场景：

- 需要频繁在中间位置插入删除元素
- 需要保持迭代器长期有效
- 实现LRU缓存等需要频繁移动元素的数据结构
- 约瑟夫环等需要高效删除的游戏模拟问题

然而，如果需要随机访问、二分查找或良好的缓存局部性，应优先选择`vector`或`deque`。