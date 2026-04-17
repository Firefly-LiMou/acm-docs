# C++有序映射容器完全指南

## map头文件概述

`<map>`头文件提供了C++标准库中的有序键值对容器，它基于红黑树（一种自平衡二叉搜索树）实现。与`unordered_map`不同，`map`容器维护键值对的有序性，所有元素按键的升序排列存储。在ACM竞赛中，当需要快速通过键查找值，同时保持元素有序或者需要使用二分查找相关操作时，`map`是一个非常重要的选择。

本章节将详细介绍`map`容器的各种操作、使用方法、适用场景以及注意事项。所有内容基于C++11及更高标准，确保在现代竞赛环境中能够正常使用。

### map的核心特性

`map`是一个关联容器，其中每个元素是一个键值对（key-value pair），键必须是唯一的。容器通过比较函数（默认为`std::less<T>`，即使用`<`运算符）来组织元素，确保元素按键的有序存储。由于底层使用红黑树实现，`map`能够保持元素的有序状态，并且提供对数时间复杂度的插入、删除和查找操作。

这个容器的主要特点在于其能够维护键值对的有序性，支持使用`lower_bound`、`upper_bound`等函数进行二分查找，同时支持按键顺序遍历元素。虽然查找操作的时间复杂度为O(log n)，不如`unordered_map`的O(1)平均复杂度，但`map`提供了`unordered_map`所不具备的有序操作能力。

## 基本用法与初始化

### 头文件与声明

使用`map`需要包含相应的头文件：

```cpp
#include <map>
using namespace std;
```

### 创建与初始化

`map`可以通过多种方式进行初始化：

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    // 默认构造
    map<string, int> m1;
    
    // 使用初始化列表（C++11）
    map<string, int> m2 = {
        {"apple", 1},
        {"banana", 2},
        {"orange", 3}
    };
    map<string, int> m3{
        {"apple", 1},
        {"banana", 2},
        {"orange", 3}
    };
    
    // 拷贝构造
    map<string, int> m4(m2);
    
    // 移动构造
    map<string, int> m5(move(m2));
    
    // 使用迭代器范围构造
    vector<pair<string, int>> v = {
        {"dog", 4},
        {"cat", 5},
        {"bird", 6}
    };
    map<string, int> m6(v.begin(), v.end());
    
    // 使用另一个map的一部分构造
    map<string, int> m7(m6.begin(), m6.end());
    
    // 指定比较函数（按字符串长度排序）
    struct StringLengthCompare {
        bool operator()(const string& a, const string& b) const {
            if (a.length() != b.length()) {
                return a.length() < b.length();
            }
            return a < b;
        }
    };
    map<string, int, StringLengthCompare> m8 = {
        {"cat", 1},
        {"dog", 2},
        {"elephant", 3},
        {"hi", 4}
    };
    
    return 0;
}
```

### 指定自定义比较函数

`map`允许在构造时指定自定义的比较函数来改变键的排序方式：

```cpp
#include <map>
#include <iostream>
#include <string>
using namespace std;

// 按字符串长度升序排列
struct StringLengthCompare {
    bool operator()(const string& a, const string& b) const {
        if (a.length() != b.length()) {
            return a.length() < b.length();
        }
        return a < b;  // 长度相同按字典序
    }
};

// 按值排序（对于map的value_type不太适用，但可供参考）
struct ValueCompare {
    bool operator()(const pair<string, int>& a, 
                   const pair<string, int>& b) const {
        return a.second < b.second;
    }
};

int main() {
    // 使用自定义比较函数（按字符串长度排序）
    map<string, int, StringLengthCompare> m = {
        {"cat", 1},
        {"dog", 2},
        {"elephant", 3},
        {"hi", 4},
        {"apple", 5}
    };
    
    cout << "按字符串长度排序:" << endl;
    for (const auto& [key, value] : m) {
        cout << "  " << key << " (长度: " << key.length() 
             << ") = " << value << endl;
    }
    
    // 使用greater比较函数（按键降序排列）
    map<string, int, greater<string>> m2 = {
        {"zebra", 1},
        {"apple", 2},
        {"monkey", 3}
    };
    
    cout << "\n按键降序排列:" << endl;
    for (const auto& [key, value] : m2) {
        cout << "  " << key << " = " << value << endl;
    }
    
    return 0;
}
```

## 元素操作

### insert操作

`insert`函数用于向容器中插入新的键值对：

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m;
    
    // 使用insert插入pair
    pair<map<string, int>::iterator, bool> result;
    result = m.insert({"apple", 1});
    if (result.second) {
        cout << "插入成功，键: " << result.first->first 
             << ", 值: " << result.first->second << endl;
    }
    
    // 插入重复键（不会覆盖）
    result = m.insert({"apple", 10});
    if (!result.second) {
        cout << "键已存在，插入被拒绝" << endl;
        cout << "原有值: " << result.first->second << endl;
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
    
    cout << "map中的元素: " << endl;
    for (const auto& [key, value] : m) {
        cout << "  " << key << ": " << value << endl;
    }
    
    return 0;
}
```

**重要特性**：`insert`函数返回`pair<iterator, bool>`，其中`bool`值表示键值对是否新插入（`true`）还是键已存在（`false`）。如果键已存在，`insert`不会更新值。

### operator[]访问和修改

`operator[]`是一个非常方便的访问和修改方式，但如果键不存在，它会插入一个新元素：

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m = {{"apple", 1}, {"banana", 2}};
    
    // 访问已存在的键
    cout << m["apple"] << endl;  // 输出: 1
    
    // 修改已存在的键的值
    m["apple"] = 10;
    cout << m["apple"] << endl;  // 输出: 10
    
    // 访问不存在的键（会插入新元素！）
    cout << m["orange"] << endl;  // 输出: 0（默认值），并插入{"orange", 0}
    
    // 使用operator[]赋值
    m["grape"] = 5;
    m["banana"] = 20;
    
    cout << "\n所有元素:" << endl;
    for (const auto& [key, value] : m) {
        cout << "  " << key << ": " << value << endl;
    }
    
    return 0;
}
```

**重要警告**：如果键不存在，`operator[]`会使用值类型的默认构造函数创建一个新元素，并返回该元素的引用。在某些情况下，这可能导致意外的数据插入。如果只需要查询而不修改，应该使用`find`函数。

### at函数

`at`函数用于访问已存在的元素，如果键不存在会抛出`out_of_range`异常：

```cpp
#include <map>
#include <iostream>
#include <stdexcept>
using namespace std;

int main() {
    map<string, int> m = {{"apple", 1}, {"banana", 2}};
    
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
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m = {
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

**效率分析**：`find`操作的时间复杂度为O(log n)，因为底层是红黑树结构，能够根据比较函数快速定位元素。

### erase操作

`erase`函数用于从容器中删除键值对：

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m = {
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
    auto start = m.lower_bound("a");
    auto end = m.lower_bound("g");
    m.erase(start, end);
    
    // 清空容器
    m.clear();
    
    return 0;
}
```

### count操作

`count`函数返回指定键在容器中的出现次数，对于`map`只能是0或1：

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m = {
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

## 二分查找操作

`map`容器支持多种二分查找操作，这些操作利用了键有序的特性，是竞赛中处理范围查询问题的利器。

### lower_bound函数

`lower_bound`返回第一个键不小于给定键的元素的迭代器：

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m = {
        {"apple", 1},
        {"banana", 2},
        {"cherry", 3},
        {"date", 4},
        {"elderberry", 5},
        {"fig", 6}
    };
    
    // 查找第一个不小于"cherry"的键
    auto it = m.lower_bound("cherry");
    if (it != m.end()) {
        cout << "第一个不小于cherry: " << it->first << " = " << it->second << endl;
    }
    
    // 查找第一个不小于"cloud"的键
    it = m.lower_bound("cloud");
    if (it != m.end()) {
        cout << "第一个不小于cloud: " << it->first << " = " << it->second << endl;
    } else {
        cout << "不存在不小于cloud的键" << endl;
    }
    
    // 利用lower_bound进行范围查询：找出以'c'到'd'开头的键
    cout << "\n范围[c, d)的元素: " << endl;
    for (auto iter = m.lower_bound("c"); 
         iter != m.lower_bound("d"); ++iter) {
        cout << "  " << iter->first << " = " << iter->second << endl;
    }
    
    return 0;
}
```

### upper_bound函数

`upper_bound`返回第一个键大于给定键的元素的迭代器：

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m = {
        {"apple", 1},
        {"banana", 2},
        {"cherry", 3},
        {"date", 4},
        {"elderberry", 5},
        {"fig", 6}
    };
    
    // 查找第一个大于"cherry"的键
    auto it = m.upper_bound("cherry");
    if (it != m.end()) {
        cout << "第一个大于cherry: " << it->first << " = " << it->second << endl;
    }
    
    // 查找第一个大于"fig"的键
    it = m.upper_bound("fig");
    if (it != m.end()) {
        cout << "第一个大于fig: " << it->first << " = " << it->second << endl;
    } else {
        cout << "不存在大于fig的键" << endl;
    }
    
    // 利用upper_bound进行范围查询
    cout << "\n范围(c, d]的元素: " << endl;
    for (auto iter = m.upper_bound("cherry"); 
         iter != m.upper_bound("elderberry"); ++iter) {
        cout << "  " << iter->first << " = " << iter->second << endl;
    }
    
    return 0;
}
```

### equal_range函数

`equal_range`同时返回`lower_bound`和`upper_bound`，形成一个范围：

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m = {
        {"apple", 1},
        {"banana", 2},
        {"cherry", 3},
        {"date", 4}
    };
    
    // 查找"cherry"的范围
    auto range = m.equal_range("cherry");
    
    if (range.first != range.second) {
        cout << "找到cherry: " << range.first->first << " = " 
             << range.first->second << endl;
    } else {
        cout << "cherry不存在" << endl;
    }
    
    // 对于不存在的键
    auto range2 = m.equal_range("zzz");
    if (range2.first == range2.second) {
        cout << "zzz不存在于map中" << endl;
    }
    
    return 0;
}
```

### 典型应用：前缀查找

利用`map`的有序特性，可以实现高效的前缀查询：

```cpp
#include <map>
#include <iostream>
#include <string>
using namespace std;

// 查找所有以prefix开头的键值对
vector<pair<string, int>> prefixSearch(map<string, int>& m, 
                                       const string& prefix) {
    vector<pair<string, int>> result;
    
    auto it = m.lower_bound(prefix);
    while (it != m.end()) {
        // 检查是否以prefix开头
        if (it->first.compare(0, prefix.length(), prefix) == 0) {
            result.push_back(*it);
            ++it;
        } else {
            break;  // 已经超出前缀范围
        }
    }
    
    return result;
}

int main() {
    map<string, int> dict = {
        {"apple", 1},
        {"app", 2},
        {"application", 3},
        {"apply", 4},
        {"banana", 5},
        {"band", 6}
    };
    
    cout << "以'app'开头的单词: " << endl;
    auto result = prefixSearch(dict, "app");
    for (const auto& [key, value] : result) {
        cout << "  " << key << " = " << value << endl;
    }
    
    return 0;
}
```

## 遍历与访问

### 范围for循环

遍历`map`的最简单方式是使用范围for循环，元素按键的排序顺序输出：

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m = {
        {"zebra", 1},
        {"apple", 2},
        {"monkey", 3},
        {"banana", 4}
    };
    
    // 基本遍历（按键升序）
    cout << "遍历键值对（按键升序）: " << endl;
    for (const auto& [key, value] : m) {
        cout << "  " << key << ": " << value << endl;
    }
    
    // 使用迭代器遍历
    cout << "\n使用迭代器: " << endl;
    for (auto it = m.begin(); it != m.end(); ++it) {
        cout << "  " << it->first << ": " << it->second << endl;
    }
    
    // 只遍历键
    cout << "\n只遍历键: ";
    for (const auto& [key, _] : m) {
        cout << key << " ";
    }
    cout << endl;
    
    // 只遍历值
    cout << "只遍历值: ";
    for (const auto& [_, value] : m) {
        cout << value << " ";
    }
    cout << endl;
    
    // 逆序遍历
    cout << "\n逆序遍历: " << endl;
    for (auto rit = m.rbegin(); rit != m.rend(); ++rit) {
        cout << "  " << rit->first << ": " << rit->second << endl;
    }
    
    return 0;
}
```

### 修改值

可以通过迭代器、`operator[]`或`at`修改与键关联的值：

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m = {
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
    
    cout << "修改后的map: " << endl;
    for (const auto& [key, value] : m) {
        cout << "  " << key << ": " << value << endl;
    }
    
    return 0;
}
```

## multimap容器

`multimap`是`map`的变体，允许存储重复的键。所有`map`的操作都适用于`multimap`，但行为略有不同。

### multimap的基本用法

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    multimap<string, int> mm = {
        {"apple", 1},
        {"banana", 2},
        {"apple", 3},
        {"cherry", 4},
        {"apple", 5}
    };
    
    cout << "multimap中的元素（按键排序）: " << endl;
    for (const auto& [key, value] : mm) {
        cout << "  " << key << ": " << value << endl;
    }
    
    // insert总是成功（可以插入重复键）
    mm.insert({"banana", 20});
    mm.insert({"date", 6});
    
    cout << "\n插入后的multimap: " << endl;
    for (const auto& [key, value] : mm) {
        cout << "  " << key << ": " << value << endl;
    }
    
    // count返回键的个数
    cout << "\napple的个数: " << mm.count("apple") << endl;  // 输出: 3
    
    // find返回第一个等于该键的元素
    auto it = mm.find("banana");
    cout << "第一个banana: " << it->first << " = " << it->second << endl;
    
    // erase删除所有等于该键的元素
    mm.erase("apple");
    
    cout << "\n删除所有apple后: " << endl;
    for (const auto& [key, value] : mm) {
        cout << "  " << key << ": " << value << endl;
    }
    
    return 0;
}
```

### multimap的边界查找

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    multimap<string, int> mm = {
        {"apple", 1},
        {"apple", 2},
        {"apple", 3},
        {"banana", 4},
        {"cherry", 5}
    };
    
    // lower_bound返回第一个不小于"apple"的元素
    auto lb = mm.lower_bound("apple");
    cout << "第一个不小于apple: " << lb->first << " = " << lb->second << endl;
    
    // upper_bound返回第一个大于"apple"的元素
    auto ub = mm.upper_bound("apple");
    cout << "第一个大于apple: " << ub->first << " = " << ub->second << endl;
    
    // 遍历所有等于"apple"的元素
    cout << "所有等于apple的元素: " << endl;
    for (auto iter = mm.lower_bound("apple"); 
         iter != mm.upper_bound("apple"); ++iter) {
        cout << "  " << iter->first << " = " << iter->second << endl;
    }
    
    return 0;
}
```

### multimap的应用：一对多映射

```cpp
#include <map>
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 使用multimap表示一对多关系
    // 学科到学生的映射
    multimap<string, string> subject_students;
    
    subject_students.insert({"Math", "Alice"});
    subject_students.insert({"Math", "Bob"});
    subject_students.insert({"Science", "Charlie"});
    subject_students.insert({"Math", "David"});
    subject_students.insert({"Science", "Eve"});
    subject_students.insert({"Science", "Frank"});
    
    // 找出所有选Math的学生
    string subject = "Math";
    cout << "选" << subject << "的学生: ";
    auto range = subject_students.equal_range(subject);
    for (auto it = range.first; it != range.second; ++it) {
        cout << it->second << " ";
    }
    cout << endl;
    
    // 找出所有选Science的学生
    subject = "Science";
    cout << "选" << subject << "的学生: ";
    range = subject_students.equal_range(subject);
    for (auto it = range.first; it != range.second; ++it) {
        cout << it->second << " ";
    }
    cout << endl;
    
    return 0;
}
```

## 常用操作时间复杂度

| 操作 | map时间复杂度 | multimap时间复杂度 | 说明 |
|------|---------------|---------------------|------|
| insert | O(log n) | O(log n) | 插入键值对 |
| operator[] | O(log n) | 不适用 | 访问/插入键值对 |
| at | O(log n) | O(log n) | 访问键值对（需键存在） |
| erase | O(log n) | O(log n) | 删除键值对 |
| find | O(log n) | O(log n) | 查找键值对 |
| count | O(log n) | O(log n) | 统计键出现次数 |
| lower_bound | O(log n) | O(log n) | 第一个不小于 |
| upper_bound | O(log n) | O(log n) | 第一个大于 |
| equal_range | O(log n) | O(log n) | 范围查询 |
| size | O(1) | O(1) | 获取元素数量 |
| begin/end | O(1) | O(1) | 获取迭代器 |

## 竞赛中的应用场景

### 场景一：统计频率并按键序输出

```cpp
#include <map>
#include <vector>
#include <iostream>
#include <string>
using namespace std;

int main() {
    string text = "the quick brown fox jumps over the lazy dog the fox";
    map<string, int> word_freq;
    
    // 统计单词频率
    istringstream iss(text);
    string word;
    while (iss >> word) {
        word_freq[word]++;
    }
    
    // 按字母顺序输出
    cout << "按字母顺序统计: " << endl;
    for (const auto& [w, freq] : word_freq) {
        cout << "  " << w << ": " << freq << endl;
    }
    
    // 找出出现频率最高的单词
    string most_common;
    int max_freq = 0;
    for (const auto& [w, freq] : word_freq) {
        if (freq > max_freq) {
            max_freq = freq;
            most_common = w;
        }
    }
    
    cout << "\n最常见的单词: " << most_common 
         << " (出现 " << max_freq << " 次)" << endl;
    
    return 0;
}
```

### 场景二：区间查询统计

```cpp
#include <map>
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 维护一个成绩表
    map<string, int> scores = {
        {"Alice", 95},
        {"Bob", 87},
        {"Charlie", 92},
        {"David", 78},
        {"Eve", 88},
        {"Frank", 91},
        {"Grace", 85}
    };
    
    // 查询分数在[85, 90)区间的学生
    int low = 85, high = 90;
    cout << "分数在[" << low << ", " << high << ")区间的学生: " << endl;
    
    // 找出所有分数 >= 85的学生
    for (auto it = scores.lower_bound(""); 
         it != scores.end(); ++it) {
        if (it->second < high) {
            cout << "  " << it->first << ": " << it->second << endl;
        } else {
            break;
        }
    }
    
    // 更高效的方式：先排序再过滤
    cout << "\n分数>=85的学生: " << endl;
    for (const auto& [name, score] : scores) {
        if (score >= 85) {
            cout << "  " << name << ": " << score << endl;
        }
    }
    
    return 0;
}
```

### 场景三：字典查询

```cpp
#include <map>
#include <iostream>
#include <string>
using namespace std;

class Dictionary {
private:
    map<string, string> dict;
    
public:
    void addWord(const string& word, const string& definition) {
        dict[word] = definition;
    }
    
    bool hasWord(const string& word) const {
        return dict.count(word);
    }
    
    string getDefinition(const string& word) const {
        auto it = dict.find(word);
        if (it != dict.end()) {
            return it->second;
        }
        return "";
    }
    
    vector<string> getWordsStartingWith(const string& prefix) const {
        vector<string> result;
        
        auto it = dict.lower_bound(prefix);
        while (it != dict.end()) {
            if (it->first.compare(0, prefix.length(), prefix) == 0) {
                result.push_back(it->first);
                ++it;
            } else {
                break;
            }
        }
        
        return result;
    }
    
    void printAll() const {
        for (const auto& [word, definition] : dict) {
            cout << word << ": " << definition << endl;
        }
    }
};

int main() {
    Dictionary dict;
    dict.addWord("apple", "a round red or green fruit");
    dict.addWord("banana", "a long yellow fruit");
    dict.addWord("application", "a formal request to be considered for a position");
    dictWord("apply", "to make a formal request");
    dict.addWord("appreciate", "to recognize the full worth of");
    
    cout << "apple的定义: " << dict.getDefinition("apple") << endl;
    cout << "\n以'app'开头的单词: ";
    auto words = dict.getWordsStartingWith("app");
    for (const auto& w : words) {
        cout << w << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 场景四：区间合并问题

```cpp
#include <map>
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 合并区间
    vector<pair<int, int>> intervals = {{1, 3}, {2, 6}, {8, 10}, {15, 18}};
    
    if (intervals.empty()) {
        cout << "没有区间" << endl;
        return 0;
    }
    
    // 使用map合并：键为起始点，值为结束点
    map<int, int> merged;
    for (const auto& interval : intervals) {
        merged[interval.first] = max(merged[interval.first], interval.second);
    }
    
    // 合并重叠区间
    vector<pair<int, int>> result;
    int current_start = merged.begin()->first;
    int current_end = merged.begin()->second;
    
    for (auto it = next(merged.begin()); it != merged.end(); ++it) {
        if (it->first <= current_end) {
            // 重叠，合并
            current_end = max(current_end, it->second);
        } else {
            // 不重叠，保存当前区间
            result.push_back({current_start, current_end});
            current_start = it->first;
            current_end = it->second;
        }
    }
    result.push_back({current_start, current_end});
    
    cout << "合并后的区间: " << endl;
    for (const auto& [start, end] : result) {
        cout << "  [" << start << ", " << end << "]" << endl;
    }
    
    return 0;
}
```

### 场景五：模拟考试排名系统

```cpp
#include <map>
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class RankingSystem {
private:
    // 分数到姓名的映射（允许多个相同分数）
    multimap<int, string, greater<int>> ranking;
    
public:
    void addScore(const string& name, int score) {
        ranking.insert({score, name});
    }
    
    // 获取第k名的分数和姓名
    pair<int, string> getKth(int k) {
        if (k < 1 || k > ranking.size()) {
            return {-1, ""};
        }
        auto it = ranking.begin();
        advance(it, k - 1);
        return {it->first, it->second};
    }
    
    // 获取某人的排名
    int getRank(const string& name) {
        int rank = 1;
        for (const auto& [score, n] : ranking) {
            if (n == name) {
                return rank;
            }
            rank++;
        }
        return -1;
    }
    
    // 获取同分数的所有人
    vector<string> getSameScore(int score) {
        vector<string> result;
        auto range = ranking.equal_range(score);
        for (auto it = range.first; it != range.second; ++it) {
            result.push_back(it->second);
        }
        return result;
    }
    
    void printAll() const {
        int rank = 1;
        for (const auto& [score, name] : ranking) {
            cout << "第" << rank << "名: " << name << " (" << score << "分)" << endl;
            rank++;
        }
    }
};

int main() {
    RankingSystem system;
    system.addScore("Alice", 95);
    system.addScore("Bob", 87);
    system.addScore("Charlie", 92);
    system.addScore("David", 78);
    system.addScore("Eve", 88);
    system.addScore("Frank", 92);
    
    cout << "排名榜: " << endl;
    system.printAll();
    
    cout << "\n第3名: ";
    auto kth = system.getKth(3);
    cout << kth.second << " (" << kth.first << "分)" << endl;
    
    cout << "\nCharlie的排名: " << system.getRank("Charlie") << endl;
    
    cout << "\n92分的学生: ";
    auto same = system.getSameScore(92);
    for (const auto& name : same) {
        cout << name << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 场景六：前缀和查询

```cpp
#include <map>
#include <iostream>
#include <vector>
using namespace std;

class PrefixSum {
private:
    map<int, long long> prefix;  // 位置到前缀和的映射
    
public:
    void build(const vector<int>& arr) {
        prefix.clear();
        long long sum = 0;
        prefix[0] = 0;  // 前0个元素的和为0
        
        for (int i = 0; i < arr.size(); i++) {
            sum += arr[i];
            prefix[i + 1] = sum;
        }
    }
    
    // 查询区间[l, r)的和（0-indexed）
    long long query(int l, int r) const {
        if (l > r) return 0;
        auto it_r = prefix.upper_bound(r);
        auto it_l = prefix.lower_bound(l);
        // prefix[k]表示前k个元素的和
        return prefix.at(r) - prefix.at(l);
    }
    
    // 打印前缀和表
    void print() const {
        cout << "前缀和表: " << endl;
        for (const auto& [pos, sum] : prefix) {
            cout << "  prefix[" << pos << "] = " << sum << endl;
        }
    }
};

int main() {
    vector<int> arr = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    PrefixSum ps;
    ps.build(arr);
    
    ps.print();
    
    cout << "\n区间[2, 7)的和: " << ps.query(2, 7) << endl;
    cout << "区间[0, 10)的和: " << ps.query(0, 10) << endl;
    cout << "区间[3, 5)的和: " << ps.query(3, 5) << endl;
    
    return 0;
}
```

## map与unordered_map的比较

| 特性 | map | unordered_map |
|------|-----|---------------|
| 底层实现 | 红黑树 | 哈希表 |
| 键顺序 | 有序（升序） | 无序 |
| 查找复杂度 | O(log n) | O(1)平均，O(n)最坏 |
| 插入复杂度 | O(log n) | O(1)平均，O(n)最坏 |
| 删除复杂度 | O(log n) | O(1)平均，O(n)最坏 |
| 迭代器遍历 | 按键序遍历 | 无序遍历 |
| 内存开销 | O(n) | O(n)（更高常数因子） |
| 额外功能 | lower_bound等有序操作 | 无 |

### 选择建议

**使用map的场景：**
- 需要按键顺序遍历
- 需要使用lower_bound/upper_bound等有序操作
- 需要按范围查询键
- 键类型不可哈希但可比较

**使用unordered_map的场景：**
- 只需要通过键快速查找值，不需要有序遍历
- 键类型是可哈希的
- 数据量较大，查找操作非常频繁

## 性能优化技巧

### 避免频繁的小规模插入

```cpp
#include <map>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    // 错误做法：逐个插入
    map<int, string> m1;
    for (int i = 0; i < 10000; i++) {
        m1[i] = "value" + to_string(i);  // 每次插入O(log n)
    }
    
    // 正确做法：先收集到vector中，再一次性插入
    vector<pair<int, string>> v;
    for (int i = 0; i < 10000; i++) {
        v.push_back({i, "value" + to_string(i)});
    }
    map<int, string> m2(v.begin(), v.end());  // 批量插入更高效
    
    return 0;
}
```

### 使用emplace减少临时对象

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, vector<int>> m;
    
    // 使用insert需要构造临时pair
    m.insert({"key", vector<int>{1, 2, 3}});
    
    // 使用emplace直接在map中构造
    m.emplace("key2", vector<int>{4, 5, 6});
    
    // 更高效：使用initializer_list（C++11）
    m["key3"] = {7, 8, 9};
    
    return 0;
}
```

## 常见错误与注意事项

### 1. operator[]的副作用

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m;
    
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

### 2. 迭代器失效规则

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m = {
        {"a", 1},
        {"b", 2},
        {"c", 3},
        {"d", 4},
        {"e", 5}
    };
    
    // 插入不会使现有迭代器失效
    auto it = m.find("c");
    
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

### 3. 不能修改键

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m = {
        {"apple", 1},
        {"banana", 2}
    };
    
    // 错误做法：尝试修改键
    // auto it = m.find("apple");
    // it->first = "new_apple";  // 编译错误！
    
    // 正确做法：删除后重新插入
    auto it = m.find("apple");
    if (it != m.end()) {
        int value = it->second;
        m.erase(it);
        m["new_apple"] = value;
    }
    
    return 0;
}
```

### 4. multimap不支持operator[]

```cpp
#include <map>
#include <iostream>
using namespace std;

int main() {
    multimap<string, int> mm;
    
    // 错误：multimap不支持operator[]
    // mm["key"] = 10;  // 编译错误！
    
    // 正确做法：使用insert或emplace
    mm.insert({"key", 10});
    mm.insert({"key", 20});
    mm.emplace("another_key", 30);
    
    for (const auto& [key, value] : mm) {
        cout << key << ": " << value << endl;
    }
    
    return 0;
}
```

## 与其他容器的配合使用

### vector与map的转换

```cpp
#include <map>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    // map转vector（键值对）
    map<string, int> m = {
        {"a", 1},
        {"b", 2},
        {"c", 3}
    };
    vector<pair<string, int>> v(m.begin(), m.end());
    
    // 只提取键
    vector<string> keys;
    for (const auto& [key, _] : m) {
        keys.push_back(key);
    }
    
    // 只提取值
    vector<int> values;
    for (const auto& [_, value] : m) {
        values.push_back(value);
    }
    
    // vector转map
    map<string, int> m2(v.begin(), v.end());
    
    return 0;
}
```

### 与algorithm配合使用

```cpp
#include <map>
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    map<string, int> m = {
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
    
    // 找出所有值大于4的键（按键序）
    vector<string> keys;
    for (const auto& [key, value] : m) {
        if (value > 4) {
            keys.push_back(key);
        }
    }
    
    cout << "值大于4的键（按键序）: ";
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
#include <map>
// C++20
// int main() {
//     map<string, int> m = {{"a", 1}, {"b", 2}};
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
#include <map>
#include <iostream>
using namespace std;

// 注意：这些函数在C++17中可用
// int main() {
//     map<string, int> m1 = {{"a", 1}, {"b", 2}};
//     map<string, int> m2 = {{"c", 3}};
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

### merge函数（C++17）

C++17引入了`merge`函数，用于合并两个map：

```cpp
#include <map>
#include <iostream>
using namespace std;

// int main() {
//     map<string, int> m1 = {{"a", 1}, {"b", 2}};
//     map<string, int> m2 = {{"b", 20}, {"c", 3}};
//     
//     // 合并m2到m1（m2中的元素会被移动或忽略）
//     m1.merge(m2);
//     
//     // m1: {"a": 1, "b": 2, "c": 3}
//     // m2: {"b": 20}
//     
//     return 0;
// }
```

## 总结与建议

`map`是ACM竞赛中处理有序键值映射和范围查询问题的核心工具。以下是一些关键建议：

首先，理解`map`的底层实现是红黑树，所有操作的时间复杂度都是O(log n)。这个复杂度虽然不如`unordered_map`的O(1)，但`map`提供了`unordered_map`所不具备的有序操作能力。

其次，熟练掌握`lower_bound`、`upper_bound`和`equal_range`等二分查找操作，这些操作在处理范围查询、前缀搜索等问题时非常有用。

第三，在使用`operator[]`时要小心，因为它会在键不存在时插入新元素。如果只需要查询而不修改，使用`find`或`count`更安全。

第四，在使用自定义类型作为`map`的键时，必须确保类型定义了正确的比较运算符或提供自定义比较函数。比较函数必须是严格弱序的。

第五，注意`map`中键不可修改的约束。如果需要修改键，必须先删除再插入。

第六，根据具体需求在`map`和`unordered_map`之间权衡。如果需要有序操作，选择`map`；如果只需要快速查找且不需要有序，选择`unordered_map`。

第七，对于需要存储重复键的场景，使用`multimap`代替`map`。