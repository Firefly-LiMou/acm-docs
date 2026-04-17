# C++无序映射容器完全指南

## unordered_map头文件概述

`<unordered_map>`头文件提供了C++标准库中的无序键值对容器，它基于哈希表实现，提供了平均时间复杂度为O(1)的插入、删除、查找和更新操作。在ACM竞赛中，当需要快速通过键查找值时，`unordered_map`是一个非常实用的选择。与`map`容器不同，`unordered_map`不维护键的排序顺序，而是根据哈希值组织键值对，因此查找操作更加高效。

本章节将详细介绍`unordered_map`的各种操作、使用方法、适用场景以及注意事项。所有内容基于C++11及更高标准，确保在现代竞赛环境中能够正常使用。

### unordered_map的核心特性

`unordered_map`是一个关联容器，其中每个元素是一个键值对（key-value pair），键必须是唯一的。容器通过键来组织元素，并提供通过键快速访问值的功能。元素的存储顺序由哈希函数决定，默认情况下使用`std::hash<Key>`作为哈希函数，使用`std::equal_to<Key>`作为键的相等性判断函数。

这个容器的主要优势在于其平均常数时间复杂度的查找操作。对于`map`容器，查找操作的时间复杂度为O(log n)，而`unordered_map`在理想情况下可以达到O(1)。这使得`unordered_map`成为处理大量数据键值映射问题的首选容器。

## 基本用法与初始化

### 头文件与声明

使用`unordered_map`需要包含相应的头文件：

```cpp
#include <unordered_map>
using namespace std;
```

### 创建与初始化

`unordered_map`可以通过多种方式进行初始化：

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    // 默认构造
    unordered_map<string, int> m1;
    
    // 使用初始化列表（C++11）
    unordered_map<string, int> m2 = {
        {"apple", 1},
        {"banana", 2},
        {"orange", 3}
    };
    unordered_map<string, int> m3{
        {"apple", 1},
        {"banana", 2},
        {"orange", 3}
    };
    
    // 拷贝构造
    unordered_map<string, int> m4(m2);
    
    // 移动构造
    unordered_map<string, int> m5(move(m2));
    
    // 使用迭代器范围构造
    vector<pair<string, int>> v = {
        {"dog", 4},
        {"cat", 5},
        {"bird", 6}
    };
    unordered_map<string, int> m6(v.begin(), v.end());
    
    // 使用另一个unordered_map的一部分构造
    unordered_map<string, int> m7(m6.begin(), m6.end());
    
    // 指定初始桶数量
    unordered_map<string, int> m8(100);  // 初始桶数量为100
    
    return 0;
}
```

### 指定自定义参数

`unordered_map`允许在构造时指定桶的数量、自定义的哈希函数和键比较函数：

```cpp
#include <unordered_map>
#include <functional>
#include <string>
using namespace std;

struct MyHash {
    size_t operator()(const string& s) const {
        // 自定义字符串哈希函数
        size_t hash = 0;
        for (char c : s) {
            hash = hash * 31 + c;
        }
        return hash;
    }
};

struct MyEqual {
    bool operator()(const string& a, const string& b) const {
        return a == b;
    }
};

int main() {
    // 使用自定义哈希函数
    unordered_map<string, int, MyHash> m1;
    m1["test"] = 100;
    
    // 使用自定义哈希和比较函数
    unordered_map<string, int, MyHash, MyEqual> m2;
    
    // 使用lambda定义哈希函数（C++14）
    auto hash_func = [](const string& s) -> size_t {
        return hash<string>{}(s) ^ (s.length() << 1);
    };
    unordered_map<string, int, decltype(hash_func)> m3(10, hash_func);
    
    return 0;
}
```

## 元素操作

### insert操作

`insert`函数用于向容器中插入新的键值对：

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    unordered_map<string, int> m;
    
    // 使用insert插入pair
    pair<unordered_map<string, int>::iterator, bool> result;
    result = m.insert({"apple", 1});
    if (result.second) {
        cout << "插入成功，键: " << result.first->first << ", 值: " 
             << result.first->second << endl;
    }
    
    // 插入重复键（不会覆盖）
    result = m.insert({"apple", 10});  // 插入失败
    if (!result.second) {
        cout << "键已存在，插入被拒绝" << endl;
    }
    
    // 使用make_pair
    m.insert(make_pair("banana", 2));
    
    // 使用初始化列表
    m.insert({{"cherry", 3}, {"date", 4}});
    
    // 使用迭代器范围插入
    vector<pair<string, int>> v = {{"elder", 5}, {"fig", 6}};
    m.insert(v.begin(), v.end());
    
    // emplace（C++11）- 直接在容器内构造键值对
    m.emplace("grape", 7);
    m.emplace("grape", 70);  // 不会重复插入
    
    // insert返回类型（C++17结构化绑定）
    auto [it, inserted] = m.insert({"honey", 8});
    
    return 0;
}
```

### operator[]访问和修改

`operator[]`是一个非常方便的访问方式，但如果键不存在，它会插入一个新元素：

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    unordered_map<string, int> m = {{"apple", 1}, {"banana", 2}};
    
    // 访问已存在的键
    cout << m["apple"] << endl;  // 输出: 1
    
    // 修改已存在的键的值
    m["apple"] = 10;
    cout << m["apple"] << endl;  // 输出: 10
    
    // 访问不存在的键（会插入新元素！）
    cout << m["orange"] << endl;  // 输出: 0（默认值），并插入{"orange", 0}
    
    // 现在m包含orange了
    for (const auto& [key, value] : m) {
        cout << key << ": " << value << endl;
    }
    
    return 0;
}
```

**重要警告**：如果键不存在，`operator[]`会使用值类型的默认构造函数创建一个新元素，并返回该元素的引用。在某些情况下，这可能导致意外的数据插入。如果只需要查询而不修改，应该使用`find`函数。

### at函数

`at`函数用于访问已存在的元素，如果键不存在会抛出`out_of_range`异常：

```cpp
#include <unordered_map>
#include <iostream>
#include <stdexcept>
using namespace std;

int main() {
    unordered_map<string, int> m = {{"apple", 1}, {"banana", 2}};
    
    try {
        cout << m.at("apple") << endl;  // 输出: 1
        cout << m.at("orange") << endl;  // 抛出异常！
    } catch (const out_of_range& e) {
        cout << "异常: " << e.what() << endl;
    }
    
    return 0;
}
```

### find操作

`find`函数用于查找键，返回指向键值对的迭代器，如果键不存在则返回`end()`：

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    unordered_map<string, int> m = {
        {"apple", 1},
        {"banana", 2},
        {"orange", 3}
    };
    
    // 查找存在的键
    auto it = m.find("banana");
    if (it != m.end()) {
        cout << "找到: " << it->first << " = " << it->second << endl;
    } else {
        cout << "键不存在" << endl;
    }
    
    // 查找不存在的键
    it = m.find("grape");
    if (it == m.end()) {
        cout << "grape不存在" << endl;
    }
    
    // 检查键是否存在（更安全的方式）
    if (m.count("apple")) {
        cout << "apple存在" << endl;
    }
    
    // 使用contains（C++20）
    // if (m.contains("apple")) { ... }
    
    return 0;
}
```

### erase操作

`erase`函数用于从容器中删除键值对：

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    unordered_map<string, int> m = {
        {"apple", 1},
        {"banana", 2},
        {"orange", 3},
        {"grape", 4}
    };
    
    // 使用键删除
    size_t removed = m.erase("orange");
    if (removed) {
        cout << "删除成功，删除了" << removed << "个元素" << endl;
    } else {
        cout << "键不存在" << endl;
    }
    
    // 使用迭代器删除
    auto it = m.find("banana");
    if (it != m.end()) {
        m.erase(it);
        cout << "通过迭代器删除banana成功" << endl;
    }
    
    // 删除范围内的元素
    m.erase(m.begin(), m.find("grape"));
    
    // 清空容器
    m.clear();
    
    return 0;
}
```

### count操作

`count`函数返回指定键在容器中的出现次数，对于`unordered_map`只能是0或1：

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    unordered_map<string, int> m = {
        {"apple", 1},
        {"banana", 2},
        {"orange", 3}
    };
    
    cout << "apple的个数: " << m.count("apple") << endl;  // 输出: 1
    cout << "grape的个数: " << m.count("grape") << endl;  // 输出: 0
    
    // 常用作键存在性检查
    if (m.count("banana")) {
        cout << "banana存在" << endl;
    }
    
    return 0;
}
```

## 遍历与访问

### 范围for循环

遍历`unordered_map`的最简单方式是使用范围for循环：

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    unordered_map<string, int> m = {
        {"apple", 1},
        {"banana", 2},
        {"orange", 3},
        {"grape", 4}
    };
    
    // 基本遍历（键值对无序）
    cout << "遍历键值对: " << endl;
    for (const auto& [key, value] : m) {  // C++17结构化绑定
        cout << "  " << key << ": " << value << endl;
    }
    
    // 使用迭代器遍历
    cout << "使用迭代器: " << endl;
    for (auto it = m.begin(); it != m.end(); ++it) {
        cout << "  " << it->first << ": " << it->second << endl;
    }
    
    // 只遍历键
    cout << "只遍历键: ";
    for (const auto& key : m) {
        cout << key.first << " ";
    }
    cout << endl;
    
    // 只遍历值
    cout << "只遍历值: ";
    for (const auto& kv : m) {
        cout << kv.second << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 修改值

可以通过迭代器或`operator[]`修改与键关联的值：

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    unordered_map<string, int> m = {
        {"apple", 1},
        {"banana", 2},
        {"orange", 3}
    };
    
    // 通过迭代器修改值
    auto it = m.find("apple");
    if (it != m.end()) {
        it->second = 10;
    }
    
    // 通过operator[]修改值
    m["banana"] = 20;
    
    // 使用at修改值（键必须存在）
    m.at("orange") = 30;
    
    // 不能修改键
    // it->first = "new_apple";  // 编译错误！
    
    for (const auto& [key, value] : m) {
        cout << key << ": " << value << endl;
    }
    
    return 0;
}
```

### bucket接口

与`unordered_set`类似，`unordered_map`也提供了桶接口：

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    unordered_map<string, int> m;
    
    // 插入一些元素
    for (int i = 0; i < 100; i++) {
        m["key" + to_string(i)] = i;
    }
    
    // 获取桶数量
    cout << "桶数量: " << m.bucket_count() << endl;
    
    // 获取元素数量
    cout << "元素数量: " << m.size() << endl;
    
    // 获取负载因子
    cout << "负载因子: " << m.load_factor() << endl;
    cout << "最大负载因子: " << m.max_load_factor() << endl;
    
    // 查看单个桶的元素数量
    cout << "桶0的元素数量: " << m.bucket_size(0) << endl;
    
    // 获取某键所在的桶
    auto it = m.find("key50");
    if (it != m.end()) {
        cout << "key50在桶" << m.bucket(it->first) << "中" << endl;
    }
    
    // 预分配空间
    m.reserve(200);  // 预留至少200个元素的空间
    
    // 强制重新哈希
    m.rehash(300);
    
    return 0;
}
```

## 竞赛中的应用场景

### 场景一：字符频率统计

```cpp
#include <unordered_map>
#include <iostream>
#include <string>
using namespace std;

int main() {
    string s = "hello world";
    unordered_map<char, int> freq;
    
    for (char c : s) {
        if (c != ' ') {  // 忽略空格
            freq[c]++;
        }
    }
    
    cout << "字符频率统计: " << endl;
    for (const auto& [char, count] : freq) {
        cout << "  '" << char << "': " << count << endl;
    }
    
    return 0;
}
```

### 场景二：单词频率统计

```cpp
#include <unordered_map>
#include <iostream>
#include <sstream>
#include <string>
using namespace std;

int main() {
    string text = "the quick brown fox jumps over the lazy dog the fox";
    unordered_map<string, int> word_freq;
    
    istringstream iss(text);
    string word;
    while (iss >> word) {
        word_freq[word]++;
    }
    
    // 找出出现频率最高的单词
    string most_common_word;
    int max_freq = 0;
    for (const auto& [w, freq] : word_freq) {
        if (freq > max_freq) {
            max_freq = freq;
            most_common_word = w;
        }
    }
    
    cout << "最常见的单词: " << most_common_word 
         << " (出现 " << max_freq << " 次)" << endl;
    
    return 0;
}
```

### 场景三：两数之和问题

```cpp
#include <unordered_map>
#include <vector>
#include <iostream>
using namespace std;

vector<int> twoSum(const vector<int>& nums, int target) {
    unordered_map<int, int> num_to_index;
    
    for (int i = 0; i < nums.size(); i++) {
        int complement = target - nums[i];
        auto it = num_to_index.find(complement);
        if (it != num_to_index.end()) {
            return {it->second, i};
        }
        num_to_index[nums[i]] = i;
    }
    
    return {};  // 没有找到解
}

int main() {
    vector<int> nums = {2, 7, 11, 15};
    int target = 9;
    auto result = twoSum(nums, target);
    
    if (!result.empty()) {
        cout << "解: nums[" << result[0] << "] + nums[" << result[1] << "] = " 
             << target << endl;
        cout << "即: " << nums[result[0]] << " + " << nums[result[1]] 
             << " = " << target << endl;
    }
    
    return 0;
}
```

### 场景四：图的邻接表表示

```cpp
#include <unordered_map>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    // 使用unordered_map表示图的邻接表
    // 键是顶点，值是该顶点的邻接顶点列表
    unordered_map<int, vector<int>> graph;
    
    // 添加无向图的边
    auto add_edge = [&](int u, int v) {
        graph[u].push_back(v);
        graph[v].push_back(u);
    };
    
    // 构建一个简单的图
    add_edge(1, 2);
    add_edge(1, 3);
    add_edge(2, 4);
    add_edge(3, 4);
    add_edge(4, 5);
    
    // 打印图的邻接表
    cout << "图的邻接表:" << endl;
    for (const auto& [vertex, neighbors] : graph) {
        cout << vertex << ": ";
        for (int neighbor : neighbors) {
            cout << neighbor << " ";
        }
        cout << endl;
    }
    
    // BFS示例
    unordered_map<int, bool> visited;
    queue<int> q;
    q.push(1);
    visited[1] = true;
    
    cout << "BFS遍历: ";
    while (!q.empty()) {
        int v = q.front();
        q.pop();
        cout << v << " ";
        for (int neighbor : graph[v]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
    cout << endl;
    
    return 0;
}
```

### 场景五：LRU缓存实现

```cpp
#include <unordered_map>
#include <list>
#include <iostream>
using namespace std;

template<typename Key, typename Value>
class LRUCache {
private:
    int capacity;
    list<pair<Key, Value>> cache_list;  // (key, value)列表
    unordered_map<Key, list<pair<Key, Value>>::iterator> cache_map;  // 键到迭代器的映射
    
public:
    LRUCache(int cap) : capacity(cap) {}
    
    Value get(Key key) {
        auto it = cache_map.find(key);
        if (it == cache_map.end()) {
            return Value();  // 返回默认值
        }
        
        // 将访问的项移到列表前端
        cache_list.splice(cache_list.begin(), cache_list, it->second);
        return it->second->second;
    }
    
    void put(Key key, Value value) {
        // 检查是否已存在
        auto it = cache_map.find(key);
        if (it != cache_map.end()) {
            // 更新值并移到前端
            it->second->second = value;
            cache_list.splice(cache_list.begin(), cache_list, it->second);
            return;
        }
        
        // 检查缓存是否已满
        if (cache_list.size() >= capacity) {
            // 删除最近最少使用的项（列表末尾）
            auto lru = cache_list.back();
            cache_map.erase(lru.first);
            cache_list.pop_back();
        }
        
        // 插入新项到列表前端
        cache_list.emplace_front(key, value);
        cache_map[key] = cache_list.begin();
    }
    
    void display() const {
        cout << "缓存内容（从新到旧）: ";
        for (const auto& [key, value] : cache_list) {
            cout << key << "->" << value << " ";
        }
        cout << endl;
    }
};

int main() {
    LRUCache<int, string> cache(3);
    
    cache.put(1, "one");
    cache.put(2, "two");
    cache.put(3, "three");
    
    cout << "初始缓存:" << endl;
    cache.display();
    
    // 访问key=1
    cout << "get(1) = " << cache.get(1) << endl;
    cache.display();
    
    // 添加新项，触发淘汰
    cache.put(4, "four");
    cache.display();
    
    return 0;
}
```

### 场景六：映射转换与查找表

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    // 创建罗马数字到整数的映射
    unordered_map<char, int> roman = {
        {'I', 1},
        {'V', 5},
        {'X', 10},
        {'L', 50},
        {'C', 100},
        {'D', 500},
        {'M', 1000}
    };
    
    // 使用映射进行转换
    string roman_num = "MCMXCIV";  // 1994
    int total = 0;
    
    for (int i = 0; i < roman_num.length(); i++) {
        int value = roman[roman_num[i]];
        if (i + 1 < roman_num.length() && value < roman[roman_num[i + 1]]) {
            total -= value;
        } else {
            total += value;
        }
    }
    
    cout << "罗马数字 " << roman_num << " = " << total << endl;
    
    return 0;
}
```

## 常用操作时间复杂度

| 操作 | 平均时间复杂度 | 最坏时间复杂度 | 说明 |
|------|----------------|----------------|------|
| insert | O(1) | O(n) | 插入键值对 |
| operator[] | O(1) | O(n) | 访问/插入键值对 |
| at | O(1) | O(n) | 访问键值对（需键存在） |
| erase | O(1) | O(n) | 删除键值对 |
| find | O(1) | O(n) | 查找键值对 |
| count | O(1) | O(n) | 统计键出现次数 |
| size | O(1) | O(1) | 获取元素数量 |
| bucket_count | O(1) | O(1) | 获取桶数量 |

## unordered_map与map的比较

| 特性 | unordered_map | map |
|------|---------------|-----|
| 底层实现 | 哈希表 | 红黑树 |
| 键顺序 | 无序 | 有序（升序） |
| 查找复杂度 | O(1)平均，O(n)最坏 | O(log n) |
| 插入复杂度 | O(1)平均，O(n)最坏 | O(log n) |
| 删除复杂度 | O(1)平均，O(n)最坏 | O(log n) |
| 迭代器遍历 | 无序 | 按键序遍历 |
| 内存开销 | 较高（哈希表） | 较低（节点） |
| 适用场景 | 快速查找，有序无关 | 需要有序遍历或范围查询 |

### 选择建议

**使用unordered_map的场景：**
- 只需要通过键快速查找值，不需要有序遍历
- 键类型是可哈希的
- 数据量较大，查找操作频繁

**使用map的场景：**
- 需要按键顺序遍历
- 需要使用lower_bound/upper_bound等有序操作
- 需要查找某个范围内的所有键
- 键类型不可哈希但可比较

## 性能优化技巧

### 预分配空间

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    // 预计有100000个键值对
    unordered_map<string, int> m;
    m.reserve(100000);  // 预留空间
    m.max_load_factor(0.7);  // 设置最大负载因子
    
    // 插入元素...
    for (int i = 0; i < 100000; i++) {
        m["key" + to_string(i)] = i;
    }
    
    return 0;
}
```

### 自定义哈希函数

```cpp
#include <unordered_map>
#include <functional>
#include <string>
#include <chrono>
using namespace std;

// 高质量的哈希函数（splitmix64）
struct CustomHash {
    static uint64_t splitmix64(uint64_t x) {
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }
    
    size_t operator()(uint64_t x) const {
        static const uint64_t FIXED_RANDOM = 
            chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
    
    size_t operator()(const string& s) const {
        return (*this)(hash<string>{}(s));
    }
    
    template<typename T>
    size_t operator()(const pair<T, T>& p) const {
        size_t h1 = (*this)(p.first);
        size_t h2 = (*this)(p.second);
        return h1 ^ (h2 << 1);
    }
};

int main() {
    unordered_map<int, int, CustomHash> m1;
    unordered_map<string, int, CustomHash> m2;
    
    return 0;
}
```

### 处理大量数据

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    // 对于非常大的数据集：
    
    // 1. 预估大小，预留空间
    const size_t EXPECTED_SIZE = 1000000;
    unordered_map<int, int> m;
    m.reserve(EXPECTED_SIZE);
    
    // 2. 考虑内存使用
    // 每个键值对需要额外的哈希表开销
    
    // 3. 对于键密集的情况，考虑使用vector instead
    // 如果键是连续的整数，可以直接使用vector
    
    // 4. 对于需要持久化的场景，考虑序列化方法
    
    return 0;
}
```

## 常见错误与注意事项

### 1. operator[]的副作用

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    unordered_map<string, int> m;
    
    // 检查键是否存在时使用operator[]会插入新元素！
    if (m["unknown_key"] > 0) {  // 这会插入{"unknown_key", 0}
        // ...
    }
    
    cout << "容器大小: " << m.size() << endl;  // 输出: 1
    
    // 正确做法：使用find
    auto it = m.find("unknown_key2");
    if (it != m.end()) {
        cout << it->second << endl;
    } else {
        cout << "键不存在" << endl;
    }
    
    return 0;
}
```

### 2. 键必须是可拷贝/可移动的

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

// 错误示例：尝试使用不能拷贝的类作为键
// class NonCopyable {
// public:
//     NonCopyable() = default;
//     NonCopyable(const NonCopyable&) = delete;
//     NonCopyable& operator=(const NonCopyable&) = delete;
// };
// unordered_set<NonCopyable> s;  // 编译错误！

// 正确做法：确保类型可以拷贝
struct Key {
    int value;
    bool operator==(const Key& other) const {
        return value == other.value;
    }
};

struct KeyHash {
    size_t operator()(const Key& k) const {
        return hash<int>{}(k.value);
    }
};

int main() {
    unordered_map<Key, int, KeyHash> m;
    m[{1}] = 100;
    
    return 0;
}
```

### 3. 迭代器失效规则

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

int main() {
    unordered_map<string, int> m = {
        {"a", 1},
        {"b", 2},
        {"c", 3},
        {"d", 4},
        {"e", 5}
    };
    
    // 插入不会使现有迭代器失效（除非rehash）
    auto it = m.find("c");
    
    // rehash会使所有迭代器失效
    m.reserve(10000);
    
    // it现在可能失效
    // cout << it->second << endl;  // 未定义行为！
    
    // 重新获取
    it = m.find("c");
    
    // 删除会使被删除元素的迭代器失效
    m.erase(it);  // it现在失效
    
    // 安全删除方式
    for (auto iter = m.begin(); iter != m.end(); ) {
        if (iter->second % 2 == 0) {
            iter = m.erase(iter);  // erase返回下一个有效迭代器
        } else {
            ++iter;
        }
    }
    
    return 0;
}
```

### 4. 哈希冲突问题

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

struct BadHash {
    size_t operator()(int x) const {
        return 0;  // 所有整数都哈希到同一个桶
    }
};

int main() {
    unordered_map<int, string, BadHash> m;
    
    // 插入大量元素会导致性能极差
    for (int i = 0; i < 10000; i++) {
        m[i] = "value" + to_string(i);
    }
    
    // 查找也会很慢
    auto start = chrono::steady_clock::now();
    m.find(9999);
    auto end = chrono::steady_clock::now();
    // 时间会很长
    
    return 0;
}
```

## 与其他容器的配合使用

### vector与unordered_map的转换

```cpp
#include <unordered_map>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    // vector转unordered_map（键必须是唯一的）
    vector<pair<string, int>> v = {
        {"a", 1}, {"b", 2}, {"c", 3}
    };
    unordered_map<string, int> m(v.begin(), v.end());
    
    // unordered_map转vector
    vector<pair<string, int>> v2(m.begin(), m.end());
    
    // 只提取键
    vector<string> keys;
    keys.reserve(m.size());
    for (const auto& kv : m) {
        keys.push_back(kv.first);
    }
    
    // 只提取值
    vector<int> values;
    values.reserve(m.size());
    for (const auto& kv : m) {
        values.push_back(kv.second);
    }
    
    return 0;
}
```

### 与algorithm配合使用

```cpp
#include <unordered_map>
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    unordered_map<string, int> m = {
        {"apple", 5},
        {"banana", 3},
        {"orange", 8},
        {"grape", 2},
        {"kiwi", 6}
    };
    
    // 找出值最大的键值对
    auto max_it = max_element(m.begin(), m.end(),
                              [](const auto& a, const auto& b) {
                                  return a.second < b.second;
                              });
    cout << "最大值: " << max_it->first << " = " << max_it->second << endl;
    
    // 统计值大于5的元素个数
    int count = count_if(m.begin(), m.end(),
                         [](const auto& kv) { return kv.second > 5; });
    cout << "值大于5的元素个数: " << count << endl;
    
    // 找出所有值大于4的键
    vector<string> keys;
    for_each(m.begin(), m.end(),
             [&keys](const auto& kv) {
                 if (kv.second > 4) {
                     keys.push_back(kv.first);
                 }
             });
    
    cout << "值大于4的键: ";
    for (const auto& k : keys) {
        cout << k << " ";
    }
    cout << endl;
    
    return 0;
}
```

## C++20新特性

### contains函数（C++20）

```cpp
#include <unordered_map>
// C++20
// int main() {
//     unordered_map<string, int> m = {{"a", 1}, {"b", 2}};
//     
//     if (m.contains("a")) {
//         cout << "a存在" << endl;
//     }
//     
//     return 0;
// }
```

### extract函数（C++17）

C++17引入了`extract`函数，允许在不拷贝节点的情况下移动节点：

```cpp
#include <unordered_map>
#include <iostream>
using namespace std;

// 注意：这些函数在C++17中可用
// int main() {
//     unordered_map<string, int> m1 = {{"a", 1}, {"b", 2}};
//     unordered_map<string, int> m2 = {{"c", 3}};
//     
//     // 从m1中提取节点并插入m2
//     auto node = m1.extract("a");
//     if (node) {
//         m2.insert(move(node));
//     }
//     
//     return 0;
// }
```

## 总结与建议

`unordered_map`是ACM竞赛中处理键值映射和快速查找问题的核心工具。以下是一些关键建议：

首先，理解`unordered_map`的工作原理是基于哈希表，平均情况下查找、插入和删除操作都是O(1)的。但要意识到在最坏情况下性能可能退化到O(n)。

其次，对于不需要按顺序遍历键的场景，`unordered_map`通常是比`map`更好的选择，因为它提供更快的查找操作。

第三，在使用`operator[]`时要小心，因为它会在键不存在时插入新元素。如果只需要查询而不修改，使用`find`或`count`更安全。

第四，在创建`unordered_map`时，如果已知元素数量，使用`reserve`预分配空间可以显著提高性能。同时考虑使用高质量的自定义哈希函数来减少哈希冲突。

第五，对于自定义类型作为键，必须提供哈希函数和相等比较函数，并确保它们基于相同的属性。

第六，根据具体需求在`unordered_map`和`map`之间权衡。如果不需要有序操作且查找频繁，`unordered_map`是更好的选择；否则考虑使用`map`。