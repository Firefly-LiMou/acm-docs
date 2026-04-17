# C++无序关联容器完全指南

## unordered_set头文件概述

`<unordered_set>`头文件提供了C++标准库中的无序集合容器，它基于哈希表实现，提供了平均时间复杂度为O(1)的插入、删除和查找操作。在ACM竞赛中，当需要快速判断元素是否存在或者需要存储不重复的元素时，`unordered_set`是一个非常实用的选择。与`set`容器不同，`unordered_set`不维护元素的排序顺序，而是根据哈希值组织元素，因此查找操作更加高效。

本章节将详细介绍`unordered_set`的各种操作、使用方法、适用场景以及注意事项。所有内容基于C++11及更高标准，确保在现代竞赛环境中能够正常使用。

### unordered_set的核心特性

`unordered_set`是一个关联容器，其中每个元素必须是唯一的，容器不允许存储重复的元素。元素的唯一性通过`key_equal`比较函数和哈希函数来确定。默认情况下，`unordered_set`使用`std::hash<Key>`作为哈希函数，使用`std::equal_to<Key>`作为相等性判断函数。

这个容器的主要优势在于其平均常数时间复杂度的查找操作。对于`set`容器，查找操作的时间复杂度为O(log n)，而`unordered_set`在理想情况下可以达到O(1)。这使得`unordered_set`成为处理大量数据查找问题的首选容器。

## 基本用法与初始化

### 头文件与声明

使用`unordered_set`需要包含相应的头文件：

```cpp
#include <unordered_set>
using namespace std;
```

### 创建与初始化

`unordered_set`可以通过多种方式进行初始化：

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

int main() {
    // 默认构造
    unordered_set<int> s1;
    
    // 使用初始化列表（C++11）
    unordered_set<int> s2 = {1, 2, 3, 4, 5};
    unordered_set<int> s3{1, 2, 3, 4, 5};
    
    // 拷贝构造
    unordered_set<int> s4(s2);
    
    // 移动构造
    unordered_set<int> s5(move(s2));
    
    // 使用迭代器范围构造
    vector<int> v = {10, 20, 30, 40, 50};
    unordered_set<int> s6(v.begin(), v.end());
    
    // 使用另一个unordered_set的一部分构造
    unordered_set<int> s7(s6.begin(), s6.end());
    
    return 0;
}
```

### 指定桶数量和参数

`unordered_set`允许在构造时指定桶的数量和自定义的哈希函数和比较函数：

```cpp
#include <unordered_set>
#include <functional>
using namespace std;

struct MyHash {
    size_t operator()(int x) const {
        // 自定义哈希函数
        return hash<int>()(x * 2654435761);
    }
};

struct MyEqual {
    bool operator()(int a, int b) const {
        return a == b;
    }
};

int main() {
    // 指定初始桶数量
    unordered_set<int> s1(100);  // 初始桶数量为100
    
    // 使用自定义哈希函数
    unordered_set<int, MyHash> s2;
    s2.insert(10);
    s2.insert(20);
    
    // 使用自定义哈希和比较函数
    unordered_set<int, MyHash, MyEqual> s3;
    
    // 使用lambda定义哈希函数（C++14）
    auto hash_func = [](int x) -> size_t {
        return hash<int>()(x) ^ (x << 1);
    };
    unordered_set<int, decltype(hash_func)> s4(10, hash_func);
    
    return 0;
}
```

## 元素操作

### insert操作

`insert`函数用于向容器中插入新元素。如果元素已经存在，则插入操作不会改变容器：

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

int main() {
    unordered_set<int> s;
    
    // 单个元素插入
    pair<unordered_set<int>::iterator, bool> result;
    result = s.insert(10);
    if (result.second) {
        cout << "插入成功，新元素: " << *(result.first) << endl;
    } else {
        cout << "元素已存在" << endl;
    }
    
    // 插入多个相同元素（只有一个会成功）
    s.insert(10);  // 不会改变容器
    s.insert(20);
    s.insert(30);
    
    // 使用初始化列表插入（C++11）
    s.insert({40, 50, 60});
    
    // 使用迭代器范围插入
    vector<int> v = {70, 80, 90};
    s.insert(v.begin(), v.end());
    
    // emplace（C++11）- 直接在容器内构造元素
    s.emplace(100);
    s.emplace(100);  // 不会重复插入
    
    // 插入返回类型
    auto [it, inserted] = s.insert(200);  // C++17结构化绑定
    
    for (int x : s) {
        cout << x << " ";
    }
    cout << endl;
    
    return 0;
}
```

**注意事项**：`insert`函数返回一个`pair<iterator, bool>`，其中`bool`值表示元素是否新插入（`true`）还是已经存在（`false`）。这个返回值在竞赛中非常有用，可以用来判断元素是否是第一次出现。

### find操作

`find`函数用于查找元素，返回指向元素的迭代器，如果元素不存在则返回`end()`：

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

int main() {
    unordered_set<int> s = {10, 20, 30, 40, 50};
    
    // 查找存在的元素
    auto it = s.find(30);
    if (it != s.end()) {
        cout << "找到元素: " << *it << endl;
    } else {
        cout << "元素不存在" << endl;
    }
    
    // 查找不存在的元素
    it = s.find(99);
    if (it == s.end()) {
        cout << "99不存在" << endl;
    }
    
    // 检查元素是否存在（更简洁的方式）
    if (s.count(30)) {
        cout << "30存在" << endl;
    }
    
    // 使用contains（C++20）
    // if (s.contains(30)) { ... }
    
    return 0;
}
```

**效率分析**：`find`操作的时间复杂度平均为O(1)，最坏情况下为O(n)（当发生大量哈希冲突时）。在实际应用中，哈希冲突的概率通常很低，因此`find`操作非常高效。

### erase操作

`erase`函数用于从容器中删除元素：

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

int main() {
    unordered_set<int> s = {10, 20, 30, 40, 50};
    
    // 使用值删除
    size_t removed = s.erase(30);
    if (removed) {
        cout << "删除成功，删除了" << removed << "个元素" << endl;
    } else {
        cout << "元素不存在" << endl;
    }
    
    // 使用迭代器删除
    auto it = s.find(20);
    if (it != s.end()) {
        s.erase(it);
        cout << "通过迭代器删除20成功" << endl;
    }
    
    // 删除范围内的元素
    s.erase(s.begin(), s.find(40));
    
    // 清空容器
    s.clear();
    
    return 0;
}
```

### count操作

`count`函数返回指定元素在容器中的数量，由于`unordered_set`中元素唯一，因此返回值只能是0或1：

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

int main() {
    unordered_set<int> s = {10, 20, 30, 40, 50};
    
    cout << "20的个数: " << s.count(20) << endl;  // 输出: 1
    cout << "99的个数: " << s.count(99) << endl;  // 输出: 0
    
    // 常用作存在性检查
    if (s.count(30)) {
        cout << "30存在" << endl;
    }
    
    return 0;
}
```

## 遍历与访问

### 范围for循环

遍历`unordered_set`的最简单方式是使用范围for循环：

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

int main() {
    unordered_set<int> s = {10, 20, 30, 40, 50};
    
    // 基本遍历（元素无序）
    cout << "遍历元素: ";
    for (const auto& elem : s) {
        cout << elem << " ";
    }
    cout << endl;
    
    // 使用迭代器遍历
    cout << "使用迭代器: ";
    for (auto it = s.begin(); it != s.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;
    
    // C++17：使用结构化绑定遍历
    for (const auto& [elem] : s) {  // 注意：unordered_set的value_type就是key_type
        cout << elem << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 修改元素值

**重要提示**：不能直接修改`unordered_set`中的元素值，因为元素的哈希值是基于元素值计算的，如果修改了值，哈希桶的位置可能需要改变，这是不允许的：

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

int main() {
    unordered_set<int> s = {10, 20, 30, 40, 50};
    
    // 错误做法：不能通过迭代器修改元素
    // for (auto& elem : s) {
    //     elem *= 2;  // 编译错误！
    // }
    
    // 正确做法：先删除，再插入
    int old_val = 20;
    int new_val = 200;
    
    auto it = s.find(old_val);
    if (it != s.end()) {
        s.erase(it);
        s.insert(new_val);
    }
    
    return 0;
}
```

### bucket接口

`unordered_set`的底层实现是哈希表，了解桶接口有助于调试和性能调优：

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

int main() {
    unordered_set<int> s = {10, 20, 30, 40, 50, 60, 70, 80, 90, 100};
    
    // 获取桶数量
    cout << "桶数量: " << s.bucket_count() << endl;
    
    // 获取元素数量
    cout << "元素数量: " << s.size() << endl;
    
    // 获取负载因子（每个桶的平均元素数）
    cout << "负载因子: " << s.load_factor() << endl;
    
    // 最大负载因子
    cout << "最大负载因子: " << s.max_load_factor() << endl;
    
    // 查看单个桶的元素数量
    cout << "桶0的元素数量: " << s.bucket_size(0) << endl;
    cout << "桶1的元素数量: " << s.bucket_size(1) << endl;
    
    // 获取某元素所在的桶
    auto it = s.find(30);
    if (it != s.end()) {
        cout << "30在桶" << s.bucket(*it) << "中" << endl;
    }
    
    // 重新哈希以预留更多桶
    s.reserve(100);  // 预留至少100个元素的空间
    cout << "reserve后桶数量: " << s.bucket_count() << endl;
    
    // 强制重新哈希
    s.rehash(200);
    cout << "rehash后桶数量: " << s.bucket_count() << endl;
    
    return 0;
}
```

## 常用操作时间复杂度

| 操作 | 平均时间复杂度 | 最坏时间复杂度 | 说明 |
|------|----------------|----------------|------|
| insert | O(1) | O(n) | 插入新元素 |
| erase | O(1) | O(n) | 删除元素 |
| find | O(1) | O(n) | 查找元素 |
| count | O(1) | O(n) | 统计元素个数 |
| size | O(1) | O(1) | 获取元素数量 |
| bucket_count | O(1) | O(1) | 获取桶数量 |

## 竞赛中的应用场景

### 场景一：元素去重

```cpp
#include <unordered_set>
#include <vector>
#include <iostream>
using namespace std;

vector<int> removeDuplicates(vector<int>& nums) {
    unordered_set<int> seen;
    vector<int> result;
    
    for (int num : nums) {
        if (seen.find(num) == seen.end()) {
            seen.insert(num);
            result.push_back(num);
        }
    }
    
    return result;
}

int main() {
    vector<int> nums = {1, 2, 3, 2, 1, 4, 5, 4, 3, 2};
    auto result = removeDuplicates(nums);
    
    for (int x : result) {
        cout << x << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 场景二：判断元素是否出现过

```cpp
#include <unordered_set>
#include <vector>
#include <iostream>
using namespace std;

bool hasDuplicate(const vector<int>& nums) {
    unordered_set<int> seen;
    
    for (int num : nums) {
        if (seen.find(num) != seen.end()) {
            return true;  // 找到重复
        }
        seen.insert(num);
    }
    
    return false;
}

int main() {
    vector<int> nums1 = {1, 2, 3, 4, 5};
    vector<int> nums2 = {1, 2, 3, 2, 4, 5};
    
    cout << "nums1有重复? " << (hasDuplicate(nums1) ? "是" : "否") << endl;
    cout << "nums2有重复? " << (hasDuplicate(nums2) ? "是" : "否") << endl;
    
    return 0;
}
```

### 场景三：找只出现一次的元素

```cpp
#include <unordered_set>
#include <vector>
#include <iostream>
using namespace std;

int findSingleElement(const vector<int>& nums) {
    unordered_set<int> seen_once;
    unordered_set<int> seen_multiple;
    
    for (int num : nums) {
        if (seen_multiple.find(num) != seen_multiple.end()) {
            // 已经在multiple中，跳过
        } else if (seen_once.find(num) != seen_once.end()) {
            // 第二次出现，移到multiple
            seen_once.erase(num);
            seen_multiple.insert(num);
        } else {
            // 第一次出现
            seen_once.insert(num);
        }
    }
    
    return *seen_once.begin();
}

int main() {
    vector<int> nums = {4, 1, 2, 1, 2};
    cout << "只出现一次的元素: " << findSingleElement(nums) << endl;
    
    return 0;
}
```

### 场景四：两数之和问题

```cpp
#include <unordered_set>
#include <vector>
#include <iostream>
using namespace std;

vector<int> twoSum(const vector<int>& nums, int target) {
    unordered_set<int> seen;
    
    for (int i = 0; i < nums.size(); i++) {
        int complement = target - nums[i];
        if (seen.find(complement) != seen.end()) {
            // 找到配对
            // 需要返回索引，这里简化处理
            return {i, static_cast<int>(
                find(nums.begin(), nums.end(), complement) - nums.begin())};
        }
        seen.insert(nums[i]);
    }
    
    return {};
}

// 更高效的实现
pair<int, int> twoSumEfficient(const vector<int>& nums, int target) {
    unordered_map<int, int> num_to_index;  // 需要索引，unordered_map更合适
    
    for (int i = 0; i < nums.size(); i++) {
        int complement = target - nums[i];
        auto it = num_to_index.find(complement);
        if (it != num_to_index.end()) {
            return {it->second, i};
        }
        num_to_index[nums[i]] = i;
    }
    
    return {-1, -1};
}

int main() {
    vector<int> nums = {2, 7, 11, 15};
    int target = 9;
    auto result = twoSumEfficient(nums, target);
    cout << "两数之和: " << nums[result.first] << " + " << nums[result.second] 
         << " = " << target << endl;
    
    return 0;
}
```

### 场景五：集合操作

```cpp
#include <unordered_set>
#include <vector>
#include <iostream>
using namespace std;

// 并集
unordered_set<int> unionSet(const unordered_set<int>& a, 
                           const unordered_set<int>& b) {
    unordered_set<int> result = a;
    result.insert(b.begin(), b.end());
    return result;
}

// 交集
unordered_set<int> intersectionSet(const unordered_set<int>& a,
                                   const unordered_set<int>& b) {
    unordered_set<int> result;
    for (const auto& elem : a) {
        if (b.find(elem) != b.end()) {
            result.insert(elem);
        }
    }
    return result;
}

// 差集（在a中但不在b中）
unordered_set<int> differenceSet(const unordered_set<int>& a,
                                 const unordered_set<int>& b) {
    unordered_set<int> result;
    for (const auto& elem : a) {
        if (b.find(elem) == b.end()) {
            result.insert(elem);
        }
    }
    return result;
}

int main() {
    unordered_set<int> A = {1, 2, 3, 4, 5};
    unordered_set<int> B = {3, 4, 5, 6, 7};
    
    auto U = unionSet(A, B);
    auto I = intersectionSet(A, B);
    auto D = differenceSet(A, B);
    
    cout << "A ∪ B: ";
    for (int x : U) cout << x << " ";
    cout << endl;
    
    cout << "A ∩ B: ";
    for (int x : I) cout << x << " ";
    cout << endl;
    
    cout << "A - B: ";
    for (int x : D) cout << x << " ";
    cout << endl;
    
    return 0;
}
```

## 性能优化技巧

### 预分配空间

在已知元素数量的情况下，预分配空间可以避免频繁的重新哈希：

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

int main() {
    // 预计有1000个元素
    unordered_set<int> s;
    s.reserve(1000);  // 预留至少1000个元素的空间
    s.max_load_factor(0.7);  // 设置最大负载因子
    
    // 插入元素...
    for (int i = 0; i < 1000; i++) {
        s.insert(i);
    }
    
    return 0;
}
```

### 自定义哈希函数

对于某些类型，特别是自定义类型或特殊范围的数据，默认哈希函数可能不够高效：

```cpp
#include <unordered_set>
#include <functional>
#include <string>
using namespace std;

// 字符串的简单哈希（实际应使用更好的哈希算法）
struct StringHash {
    size_t operator()(const string& s) const {
        size_t hash = 0;
        for (char c : s) {
            hash = hash * 31 + c;
        }
        return hash;
    }
};

// 处理哈希冲突的策略
struct CustomHash {
    // 使用splitmix64哈希算法（C++中常用的高质量哈希）
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
    
    // 字符串版本
    size_t operator()(const string& s) const {
        return (*this)(hash<string>{}(s));
    }
};

int main() {
    // 使用自定义哈希的unordered_set
    unordered_set<string, StringHash> s1;
    unordered_set<int, CustomHash> s2;
    
    return 0;
}
```

### 处理大量数据

当处理大量数据时，需要注意内存使用和性能：

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

int main() {
    // 对于非常大的数据集，使用unordered_set时需要注意：
    
    // 1. 预估大小，预留空间
    const size_t EXPECTED_SIZE = 1000000;
    unordered_set<int> s;
    s.reserve(EXPECTED_SIZE);
    
    // 2. 使用更紧凑的类型（如果可能）
    // 使用int32_t而不是long long可以节省内存
    
    // 3. 考虑使用robin_hood哈希（第三方库）
    // 在某些场景下比标准unordered_set更高效
    
    return 0;
}
```

## unordered_set与set的比较

| 特性 | unordered_set | set |
|------|---------------|-----|
| 底层实现 | 哈希表 | 红黑树 |
| 元素顺序 | 无序 | 有序（升序） |
| 查找复杂度 | O(1)平均，O(n)最坏 | O(log n) |
| 插入复杂度 | O(1)平均，O(n)最坏 | O(log n) |
| 删除复杂度 | O(1)平均，O(n)最坏 | O(log n) |
| 迭代器遍历 | 无序 | 按序遍历 |
| 内存开销 | 较高（哈希表） | 较低（节点） |
| 适用场景 | 快速查找，有序无关 | 需要有序遍历 |

### 选择建议

**使用unordered_set的场景：**
- 只需要快速查找，不需要有序遍历
- 数据量较大，查找操作频繁
- 元素是可哈希的类型

**使用set的场景：**
- 需要按元素顺序遍历
- 需要使用lower_bound/upper_bound等有序操作
- 需要维持元素的排序关系
- 元素类型不支持哈希但支持比较

## 常见错误与注意事项

### 1. 元素必须可哈希

```cpp
#include <unordered_set>
// 错误示例：自定义类型需要提供哈希函数
// struct Point {
//     int x, y;
// };
// unordered_set<Point> s;  // 编译错误！

// 正确做法：提供哈希函数
struct Point {
    int x, y;
    bool operator==(const Point& other) const {
        return x == other.x && y == other.y;
    }
};

struct PointHash {
    size_t operator()(const Point& p) const {
        return hash<int>{}(p.x) ^ (hash<int>{}(p.y) << 1);
    }
};

int main() {
    unordered_set<Point, PointHash> s;
    s.insert({1, 2});
    s.insert({1, 2});  // 重复，不会插入
    
    return 0;
}
```

### 2. 哈希冲突导致性能下降

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

// 糟糕的哈希函数：所有元素都映射到同一个桶
struct BadHash {
    size_t operator()(int x) const {
        return 0;  // 所有元素的哈希值都是0
    }
};

int main() {
    unordered_set<int, BadHash> s;
    
    for (int i = 0; i < 10000; i++) {
        s.insert(i);
    }
    
    // 查找性能会非常差，因为所有元素都在同一个桶中
    auto start = chrono::steady_clock::now();
    cout << s.find(9999) != s.end() << endl;
    auto end = chrono::steady_clock::now();
    
    // 使用time_point和duration计算时间...
    
    return 0;
}
```

### 3. 迭代器失效规则

```cpp
#include <unordered_set>
#include <iostream>
using namespace std;

int main() {
    unordered_set<int> s = {1, 2, 3, 4, 5};
    
    // 插入元素不会使迭代器失效（除非发生rehash）
    auto it = s.find(3);
    
    // rehash会使得所有迭代器失效
    s.reserve(10000);  // 可能触发rehash
    
    // it现在可能失效，不能再使用
    // cout << *it << endl;  // 未定义行为！
    
    // 重新获取迭代器
    it = s.find(3);
    
    // 删除元素会使被删除元素的迭代器失效
    s.erase(it);  // it现在失效
    
    // 安全做法：在修改容器后重新获取所有需要的迭代器
    for (auto iter = s.begin(); iter != s.end(); ) {
        if (*iter % 2 == 0) {
            iter = s.erase(iter);  // erase返回下一个有效迭代器
        } else {
            ++iter;
        }
    }
    
    return 0;
}
```

### 4. 比较函数与哈希函数的匹配

```cpp
#include <unordered_set>
#include <functional>
using namespace std;

struct MyType {
    int value;
};

// 错误示例：比较函数和哈希函数使用不同的相等性定义
struct BadHash {
    size_t operator()(const MyType& a) const {
        return hash<int>{}(a.value);
    }
};

struct BadEqual {
    bool operator()(const MyType& a, const MyType& b) const {
        return a.value == b.value;  // 这个相等定义
    }
};

struct GoodEqual {
    bool operator()(const MyType& a, const MyType& b) const {
        return a.value == b.value;  // 必须与哈希函数基于相同属性
    }
};

int main() {
    // 必须确保哈希函数和比较函数基于相同的属性
    unordered_set<MyType, BadHash, GoodEqual> s;
    
    return 0;
}
```

## 与其他容器的配合使用

### vector与unordered_set的转换

```cpp
#include <unordered_set>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    // vector转unordered_set（去重）
    vector<int> v = {1, 2, 2, 3, 3, 3, 4, 4, 4, 4};
    unordered_set<int> s(v.begin(), v.end());
    
    // unordered_set转vector
    vector<int> v2(s.begin(), s.end());
    
    // 使用unordered_set快速去重，然后转vector保持顺序
    // 但需要注意：unordered_set不保证顺序
    // 如果需要保持原顺序，需要其他方法
    
    return 0;
}
```

### 与algorithm配合使用

```cpp
#include <unordered_set>
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    unordered_set<int> s = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5};
    
    // 统计大于5的元素个数
    int count = count_if(s.begin(), s.end(), [](int x) { return x > 5; });
    cout << "大于5的元素个数: " << count << endl;
    
    // 查找最大元素
    auto max_it = max_element(s.begin(), s.end());
    cout << "最大元素: " << *max_it << endl;
    
    // 对unordered_set排序后使用其他算法
    vector<int> v(s.begin(), s.end());
    sort(v.begin(), v.end());
    
    return 0;
}
```

## C++20新特性

### contains函数（C++20）

C++20为关联容器添加了`contains`成员函数，提供更简洁的存在性检查：

```cpp
#include <unordered_set>
// C++20
// int main() {
//     unordered_set<int> s = {1, 2, 3, 4, 5};
//     
//     if (s.contains(3)) {
//         cout << "3存在" << endl;
//     }
//     
//     return 0;
// }
```

### find_or_range（C++23）

C++23为关联容器添加了`find_and_erase`等更方便的操作。

## 总结与建议

`unordered_set`是ACM竞赛中处理元素去重和快速查找问题的重要工具。以下是一些关键建议：

首先，理解`unordered_set`的工作原理是基于哈希表，平均情况下查找、插入和删除操作都是O(1)的。但在最坏情况下（大量哈希冲突），性能可能退化到O(n)。

其次，在创建`unordered_set`时，如果已知元素数量，使用`reserve`预分配空间可以提高性能。同时，考虑使用合适的负载因子来平衡内存使用和性能。

第三，对于自定义类型，必须提供哈希函数和相等比较函数。确保这两个函数基于相同的属性进行计算。

第四，注意迭代器失效规则，特别是在修改容器后。安全做法是在遍历过程中需要删除时使用返回迭代器版本的erase。

第五，根据具体需求在`unordered_set`和`set`之间选择。如果不需要有序遍历且查找频繁，`unordered_set`是更好的选择；否则考虑使用`set`。