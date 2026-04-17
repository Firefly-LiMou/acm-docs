# C++有序集合容器完全指南

## set头文件概述

`<set>`头文件提供了C++标准库中的有序关联容器，它基于红黑树（一种自平衡二叉搜索树）实现。与`unordered_set`不同，`set`容器维护元素的有序性，所有元素按键的升序排列存储。在ACM竞赛中，当需要快速查找、同时保持元素有序或者需要使用二分查找相关操作时，`set`是一个非常重要的选择。

本章节将详细介绍`set`容器的各种操作、使用方法、适用场景以及注意事项。所有内容基于C++11及更高标准，确保在现代竞赛环境中能够正常使用。

### set的核心特性

`set`是一个关联容器，其中每个元素必须是唯一的，容器不允许存储重复的元素。元素的唯一性通过比较函数（默认为`std::less<T>`，即使用`<`运算符）来确定。由于底层使用红黑树实现，`set`能够保持元素的有序状态，并且提供对数时间复杂度的插入、删除和查找操作。

这个容器的主要特点在于其能够维护元素的有序性，支持使用`lower_bound`、`upper_bound`等函数进行二分查找，同时支持按顺序遍历元素。虽然查找操作的时间复杂度为O(log n)，不如`unordered_set`的O(1)平均复杂度，但`set`提供了`unordered_set`所不具备的有序操作能力。

## 基本用法与初始化

### 头文件与声明

使用`set`需要包含相应的头文件：

```cpp
#include <set>
using namespace std;
```

### 创建与初始化

`set`可以通过多种方式进行初始化：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    // 默认构造
    set<int> s1;
    
    // 使用初始化列表（C++11）
    set<int> s2 = {5, 3, 1, 4, 2};
    set<int> s3{5, 3, 1, 4, 2};
    
    // 拷贝构造
    set<int> s4(s2);
    
    // 移动构造
    set<int> s5(move(s2));
    
    // 使用迭代器范围构造
    vector<int> v = {10, 20, 30, 40, 50};
    set<int> s6(v.begin(), v.end());
    
    // 使用另一个set的一部分构造
    set<int> s7(s6.begin(), s6.end());
    
    // 指定比较函数（降序排列）
    set<int, greater<int>> s8 = {5, 3, 1, 4, 2};
    
    return 0;
}
```

### 指定自定义比较函数

`set`允许在构造时指定自定义的比较函数来改变元素的排序方式：

```cpp
#include <set>
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

// 按绝对值排序
struct AbsCompare {
    bool operator()(int a, int b) const {
        return abs(a) < abs(b);
    }
};

int main() {
    // 使用自定义比较函数
    set<string, StringLengthCompare> s1 = {
        "apple", "banana", "cat", "dog", "elephant"
    };
    
    cout << "按字符串长度排序:" << endl;
    for (const auto& str : s1) {
        cout << "  " << str << " (长度: " << str.length() << ")" << endl;
    }
    
    // 使用绝对值排序
    set<int, AbsCompare> s2 = {-5, 3, -1, 4, -2, 0};
    
    cout << "\n按绝对值排序:" << endl;
    for (int x : s2) {
        cout << x << " ";
    }
    cout << endl;
    
    // 使用greater比较函数（降序）
    set<int, greater<int>> s3 = {5, 3, 1, 4, 2};
    
    cout << "\n降序排列:" << endl;
    for (int x : s3) {
        cout << x << " ";
    }
    cout << endl;
    
    return 0;
}
```

## 元素操作

### insert操作

`insert`函数用于向容器中插入新元素。如果元素已经存在，则插入操作不会改变容器：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    set<int> s;
    
    // 单个元素插入
    pair<set<int>::iterator, bool> result;
    result = s.insert(10);
    if (result.second) {
        cout << "插入成功，值: " << *(result.first) << endl;
    } else {
        cout << "元素已存在，插入被拒绝" << endl;
    }
    
    // 插入重复元素
    result = s.insert(10);  // 插入失败
    cout << "重复插入10: " << (result.second ? "成功" : "失败") << endl;
    
    // 插入多个元素
    s.insert({20, 30, 40, 50});
    
    // 使用迭代器范围插入
    vector<int> v = {60, 70, 80, 90, 100};
    s.insert(v.begin(), v.end());
    
    // emplace（C++11）- 直接在容器内构造元素
    s.emplace(15);
    s.emplace(15);  // 不会重复插入
    
    // insert返回类型（C++17结构化绑定）
    auto [it, inserted] = s.insert(25);
    
    cout << "set中的元素: ";
    for (int x : s) {
        cout << x << " ";
    }
    cout << endl;
    
    return 0;
}
```

**重要特性**：`insert`函数返回`pair<iterator, bool>`，其中`bool`值表示元素是否新插入（`true`）还是已经存在（`false`）。这个返回值在竞赛中非常有用，可以用来统计不重复元素的数量或判断元素是否是第一次出现。

### find操作

`find`函数用于查找元素，返回指向元素的迭代器，如果元素不存在则返回`end()`：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    set<int> s = {10, 20, 30, 40, 50};
    
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

**效率分析**：`find`操作的时间复杂度为O(log n)，因为底层是红黑树结构，能够根据比较函数快速定位元素。这个复杂度虽然不如`unordered_set`的O(1)平均复杂度，但`set`支持`unordered_set`所不支持的有序操作。

### erase操作

`erase`函数用于从容器中删除元素：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    set<int> s = {10, 20, 30, 40, 50, 60, 70};
    
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
    s.erase(s.find(40), s.find(60));
    
    // 清空容器
    s.clear();
    
    return 0;
}
```

### count操作

`count`函数返回指定元素在容器中的数量，由于`set`中元素唯一，因此返回值只能是0或1：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    set<int> s = {10, 20, 30, 40, 50};
    
    cout << "20的个数: " << s.count(20) << endl;  // 输出: 1
    cout << "99的个数: " << s.count(99) << endl;  // 输出: 0
    
    // 常用作存在性检查
    if (s.count(30)) {
        cout << "30存在" << endl;
    }
    
    return 0;
}
```

## 二分查找操作

`set`容器支持多种二分查找操作，这些操作利用了元素有序的特性，是竞赛中处理范围查询问题的利器。

### lower_bound函数

`lower_bound`返回第一个不小于给定值的元素的迭代器：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    set<int> s = {10, 20, 30, 40, 50, 60, 70, 80, 90};
    
    // 查找第一个不小于30的元素
    auto it = s.lower_bound(30);
    if (it != s.end()) {
        cout << "第一个不小于30的元素: " << *it << endl;  // 输出: 30
    }
    
    // 查找第一个不小于35的元素
    it = s.lower_bound(35);
    if (it != s.end()) {
        cout << "第一个不小于35的元素: " << *it << endl;  // 输出: 40
    }
    
    // 查找第一个不小于95的元素
    it = s.lower_bound(95);
    if (it == s.end()) {
        cout << "不存在不小于95的元素" << endl;  // 输出此行
    }
    
    // 利用lower_bound进行范围查询：找出[30, 50)的元素
    cout << "范围[30, 50)的元素: ";
    for (auto iter = s.lower_bound(30); iter != s.lower_bound(50); ++iter) {
        cout << *iter << " ";
    }
    cout << endl;
    
    return 0;
}
```

### upper_bound函数

`upper_bound`返回第一个大于给定值的元素的迭代器：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    set<int> s = {10, 20, 30, 40, 50, 60, 70, 80, 90};
    
    // 查找第一个大于30的元素
    auto it = s.upper_bound(30);
    if (it != s.end()) {
        cout << "第一个大于30的元素: " << *it << endl;  // 输出: 40
    }
    
    // 查找第一个大于50的元素
    it = s.upper_bound(50);
    if (it != s.end()) {
        cout << "第一个大于50的元素: " << *it << endl;  // 输出: 60
    }
    
    // 利用upper_bound进行范围查询：找出(30, 50]的元素
    cout << "范围(30, 50]的元素: ";
    for (auto iter = s.upper_bound(30); iter != s.upper_bound(50); ++iter) {
        cout << *iter << " ";
    }
    cout << endl;
    
    return 0;
}
```

### equal_range函数

`equal_range`同时返回`lower_bound`和`upper_bound`，形成一个范围：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    set<int> s = {10, 20, 30, 30, 30, 40, 50};
    
    // 查找30的范围
    auto range = s.equal_range(30);
    
    cout << "30的范围: [" << *range.first << ", " << *range.second << ")" << endl;
    
    cout << "等于30的所有元素: ";
    for (auto iter = range.first; iter != range.second; ++iter) {
        cout << *iter << " ";
    }
    cout << endl;
    
    // 对于不存在的元素
    auto range2 = s.equal_range(35);
    if (range2.first == range2.second) {
        cout << "35不存在于set中" << endl;
    }
    
    return 0;
}
```

### 典型应用：排名查询

利用`set`的有序特性，可以实现高效的排名查询：

```cpp
#include <set>
#include <iostream>
using namespace std;

// 计算小于等于x的元素个数（排名）
int rankOf(set<int>& s, int x) {
    return distance(s.begin(), s.upper_bound(x));
}

// 计算小于x的元素个数
int rankOfLess(set<int>& s, int x) {
    return distance(s.begin(), s.lower_bound(x));
}

int main() {
    set<int> s = {10, 20, 30, 40, 50, 60, 70, 80, 90};
    
    cout << "元素排名（小于等于）:" << endl;
    for (int x : {10, 30, 50, 100}) {
        cout << "  " << x << "的排名是" << rankOf(s, x) << endl;
    }
    
    cout << "\n元素排名（小于）:" << endl;
    for (int x : {10, 30, 50, 100}) {
        cout << "  " << x << "的排名是" << rankOfLess(s, x) << endl;
    }
    
    return 0;
}
```

## 遍历与访问

### 范围for循环

遍历`set`的最简单方式是使用范围for循环，元素按排序顺序输出：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    set<int> s = {50, 20, 80, 10, 40, 60, 30, 70};
    
    // 基本遍历（元素有序）
    cout << "遍历元素（默认升序）: ";
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
    
    // C++17：使用结构化绑定
    cout << "结构化绑定: ";
    for (const auto& [elem] : s) {
        cout << elem << " ";
    }
    cout << endl;
    
    // 逆序遍历
    cout << "逆序遍历: ";
    for (auto rit = s.rbegin(); rit != s.rend(); ++rit) {
        cout << *rit << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 修改元素值

**重要提示**：不能直接修改`set`中的元素值，因为元素在树中的位置取决于其值。如果需要修改元素，必须先删除旧元素，再插入新元素：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    set<int> s = {10, 20, 30, 40, 50};
    
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
    
    // 也可以使用更简洁的方式
    s.erase(old_val);
    s.insert(new_val);
    
    cout << "修改后的set: ";
    for (int x : s) {
        cout << x << " ";
    }
    cout << endl;
    
    return 0;
}
```

## multiset容器

`multiset`是`set`的变体，允许存储重复的元素。所有`set`的操作都适用于`multiset`，但行为略有不同。

### multiset的基本用法

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    multiset<int> ms = {10, 20, 30, 20, 40, 20, 50};
    
    cout << "multiset中的元素: ";
    for (int x : ms) {
        cout << x << " ";
    }
    cout << endl;
    
    // insert总是成功
    ms.insert(20);
    ms.insert(60);
    
    cout << "插入后的multiset: ";
    for (int x : ms) {
        cout << x << " ";
    }
    cout << endl;
    
    // count返回元素的个数
    cout << "20的个数: " << ms.count(20) << endl;  // 输出: 4
    
    // find返回第一个等于该值的元素
    auto it = ms.find(20);
    cout << "第一个20: " << *it << endl;
    
    // erase删除所有等于该值的元素
    ms.erase(20);
    
    cout << "删除所有20后: ";
    for (int x : ms) {
        cout << x << " ";
    }
    cout << endl;
    
    return 0;
}
```

### multiset的边界查找

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    multiset<int> ms = {10, 20, 30, 30, 30, 40, 50};
    
    // lower_bound返回第一个不小于30的元素
    auto lb = ms.lower_bound(30);
    cout << "第一个不小于30: " << *lb << endl;
    
    // upper_bound返回第一个大于30的元素
    auto ub = ms.upper_bound(30);
    cout << "第一个大于30: " << *ub << endl;
    
    // 遍历所有等于30的元素
    cout << "所有等于30的元素: ";
    for (auto iter = ms.lower_bound(30); iter != ms.upper_bound(30); ++iter) {
        cout << *iter << " ";
    }
    cout << endl;
    
    // 使用equal_range
    auto range = ms.equal_range(30);
    cout << "equal_range范围: ";
    for (auto iter = range.first; iter != range.second; ++iter) {
        cout << *iter << " ";
    }
    cout << endl;
    
    return 0;
}
```

## 常用操作时间复杂度

| 操作 | set时间复杂度 | multiset时间复杂度 | 说明 |
|------|---------------|---------------------|------|
| insert | O(log n) | O(log n) | 插入元素 |
| erase | O(log n) | O(log n) | 删除元素 |
| find | O(log n) | O(log n) | 查找元素 |
| count | O(log n) | O(log n) | 统计元素个数 |
| lower_bound | O(log n) | O(log n) | 第一个不小于 |
| upper_bound | O(log n) | O(log n) | 第一个大于 |
| equal_range | O(log n) | O(log n) | 范围查询 |
| size | O(1) | O(1) | 获取元素数量 |
| begin/end | O(1) | O(1) | 获取迭代器 |

## 竞赛中的应用场景

### 场景一：自动排序与去重

```cpp
#include <set>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {5, 2, 8, 2, 1, 9, 5, 3, 8, 1};
    
    // 使用set自动排序并去重
    set<int> s(v.begin(), v.end());
    
    cout << "去重并排序后: ";
    for (int x : s) {
        cout << x << " ";
    }
    cout << endl;
    
    // 统计不重复元素个数
    cout << "不重复元素个数: " << s.size() << endl;
    
    return 0;
}
```

### 场景二：维护动态有序数据

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    // 模拟动态维护有序数据
    set<int> prices;
    
    // 插入一些价格
    prices.insert(100);
    prices.insert(50);
    prices.insert(75);
    prices.insert(200);
    prices.insert(150);
    
    cout << "当前价格列表（有序）: ";
    for (int p : prices) {
        cout << p << " ";
    }
    cout << endl;
    
    // 查找最便宜和最贵的商品
    cout << "最便宜: " << *prices.begin() << endl;
    cout << "最贵: " << *prices.rbegin() << endl;
    
    // 查找大于100的第一个价格
    auto it = prices.upper_bound(100);
    if (it != prices.end()) {
        cout << "第一个大于100的价格: " << *it << endl;
    }
    
    return 0;
}
```

### 场景三：查找第K大/小元素

```cpp
#include <set>
#include <iostream>
using namespace std;

// 查找第k小的元素（k从1开始）
int kthSmallest(set<int>& s, int k) {
    if (k < 1 || k > s.size()) {
        return -1;  // 无效
    }
    auto it = s.begin();
    advance(it, k - 1);
    return *it;
}

// 查找第k大的元素（k从1开始）
int kthLargest(set<int>& s, int k) {
    if (k < 1 || k > s.size()) {
        return -1;  // 无效
    }
    auto it = s.rbegin();
    advance(it, k - 1);
    return *it;
}

int main() {
    set<int> s = {10, 20, 30, 40, 50, 60, 70, 80, 90};
    
    cout << "第3小的元素: " << kthSmallest(s, 3) << endl;  // 输出: 30
    cout << "第3大的元素: " << kthLargest(s, 3) << endl;  // 输出: 70
    
    // 中位数
    int n = s.size();
    if (n % 2 == 1) {
        cout << "中位数: " << kthSmallest(s, (n + 1) / 2) << endl;
    } else {
        int a = kthSmallest(s, n / 2);
        int b = kthSmallest(s, n / 2 + 1);
        cout << "中位数: " << (a + b) / 2.0 << endl;
    }
    
    return 0;
}
```

### 场景四：范围查询统计

```cpp
#include <set>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    set<int> data = {1, 3, 5, 7, 9, 11, 13, 15, 17, 19, 21};
    
    // 统计[5, 15]范围内的元素个数
    int low = 5, high = 15;
    auto lb = data.lower_bound(low);
    auto ub = data.upper_bound(high);
    int count = distance(lb, ub);
    
    cout << "[" << low << ", " << high << "]范围内的元素个数: " << count << endl;
    
    // 列出范围内的所有元素
    cout << "范围内的元素: ";
    for (auto it = lb; it != ub; ++it) {
        cout << *it << " ";
    }
    cout << endl;
    
    // 找出第一个大于某个阈值的元素
    int threshold = 12;
    it = data.upper_bound(threshold);
    if (it != data.end()) {
        cout << "第一个大于" << threshold << "的元素: " << *it << endl;
    }
    
    return 0;
}
```

### 场景五：维护滑动窗口的中位数

```cpp
#include <set>
#include <vector>
#include <iostream>
#include <algorithm>
using namespace std;

class MedianFinder {
private:
    multiset<int> left;   // 最大堆（实际存储较小的半部分）
    multiset<int> right;  // 最小堆（实际存储较大的半部分）
    
public:
    void addNum(int num) {
        // 如果是第一个元素，放入right
        if (right.empty() || num >= *right.begin()) {
            right.insert(num);
        } else {
            left.insert(num);
        }
        
        // 平衡两个堆
        rebalance();
    }
    
    void removeNum(int num) {
        // 从right中删除（如果存在）
        auto it = right.find(num);
        if (it != right.end()) {
            right.erase(it);
        } else {
            it = left.find(num);
            if (it != left.end()) {
                left.erase(it);
            }
        }
        
        rebalance();
    }
    
    double findMedian() {
        // 保证left的大小等于right或比right小1
        if (left.size() > right.size()) {
            return *left.rbegin();  // 最大值
        } else if (right.size() > left.size() + 1) {
            return *right.begin();  // 最小值
        } else {
            // 两个堆大小相等或相差1
            if (right.empty()) return 0;
            return (*left.rbegin() + *right.begin()) / 2.0;
        }
    }
    
private:
    void rebalance() {
        // 保证left的大小 <= right的大小，且相差不超过1
        if (left.size() > right.size()) {
            // 将left中的最大值移到right
            auto it = prev(left.end());
            right.insert(*it);
            left.erase(it);
        } else if (right.size() > left.size() + 1) {
            // 将right中的最小值移到left
            auto it = right.begin();
            left.insert(*it);
            right.erase(it);
        }
    }
};

int main() {
    MedianFinder mf;
    vector<int> nums = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    
    cout << "添加元素并输出中位数: " << endl;
    for (int i = 0; i < nums.size(); i++) {
        mf.addNum(nums[i]);
        cout << "添加" << nums[i] << "后，中位数: " 
             << mf.findMedian() << endl;
    }
    
    return 0;
}
```

### 场景六：集合运算

```cpp
#include <set>
#include <vector>
#include <iostream>
using namespace std;

// 并集
set<int> setUnion(const set<int>& a, const set<int>& b) {
    set<int> result;
    // 直接合并并利用set去重
    result.insert(a.begin(), a.end());
    result.insert(b.begin(), b.end());
    return result;
}

// 交集
set<int> setIntersection(const set<int>& a, const set<int>& b) {
    set<int> result;
    for (const auto& elem : a) {
        if (b.count(elem)) {
            result.insert(elem);
        }
    }
    return result;
}

// 差集（在a中但不在b中）
set<int> setDifference(const set<int>& a, const set<int>& b) {
    set<int> result;
    for (const auto& elem : a) {
        if (!b.count(elem)) {
            result.insert(elem);
        }
    }
    return result;
}

int main() {
    set<int> A = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    set<int> B = {5, 6, 7, 8, 9, 10, 11, 12, 13, 14};
    
    auto U = setUnion(A, B);
    auto I = setIntersection(A, B);
    auto D = setDifference(A, B);
    
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

## set与unordered_set的比较

| 特性 | set | unordered_set |
|------|-----|---------------|
| 底层实现 | 红黑树 | 哈希表 |
| 元素顺序 | 有序（升序） | 无序 |
| 查找复杂度 | O(log n) | O(1)平均，O(n)最坏 |
| 插入复杂度 | O(log n) | O(1)平均，O(n)最坏 |
| 删除复杂度 | O(log n) | O(1)平均，O(n)最坏 |
| 迭代器遍历 | 按序遍历 | 无序遍历 |
| 内存开销 | O(n) | O(n)（更高常数因子） |
| 额外功能 | lower_bound等有序操作 | 无 |

### 选择建议

**使用set的场景：**
- 需要按元素顺序遍历
- 需要使用lower_bound/upper_bound等二分查找操作
- 需要维护有序的数据集合
- 数据量适中，查找操作不是绝对瓶颈

**使用unordered_set的场景：**
- 只需要快速查找，不需要有序遍历
- 数据量很大，查找操作非常频繁
- 不需要使用有序操作

## 性能优化技巧

### 避免频繁的小规模插入

```cpp
#include <set>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    // 错误做法：逐个插入
    set<int> s1;
    for (int i = 0; i < 10000; i++) {
        s1.insert(i);  // 每次插入O(log n)
    }
    
    // 正确做法：先收集到vector中，再一次性插入
    vector<int> v;
    for (int i = 0; i < 10000; i++) {
        v.push_back(i);
    }
    set<int> s2(v.begin(), v.end());  // 先构建vector，再批量插入
    
    // 更优：直接构造
    set<int> s3;
    s3.insert(v.begin(), v.end());
    
    return 0;
}
```

### 使用合适的比较函数

```cpp
#include <set>
#include <iostream>
using namespace std;

// 自定义比较函数应该有严格弱序的性质
struct Compare {
    bool operator()(int a, int b) const {
        return a < b;  // 正确的严格弱序
    }
};

// 错误示例：不是严格弱序
// struct BadCompare {
//     bool operator()(int a, int b) const {
//         return a <= b;  // 错误！应该是<
//     }
// };

int main() {
    set<int, Compare> s;
    s.insert(5);
    s.insert(3);
    s.insert(7);
    
    return 0;
}
```

## 常见错误与注意事项

### 1. 迭代器失效规则

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    set<int> s = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    
    // 插入不会使现有迭代器失效
    auto it = s.find(5);
    
    // 删除会使被删除元素的迭代器失效
    s.erase(it);  // it现在失效
    
    // 安全删除方式
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

### 2. 比较函数的一致性

```cpp
#include <set>
#include <iostream>
using namespace std;

// 错误示例：比较函数不一致
struct InconsistentCompare {
    bool operator()(int a, int b) const {
        return a < b;
    }
    // 同时需要operator==
    bool operator==(const InconsistentCompare&) const { return true; }
};

int main() {
    // 注意：set要求比较函数是严格弱序的
    // 并且==运算符应该反映比较函数的关系
    set<int, InconsistentCompare> s;
    
    return 0;
}
```

### 3. 不能修改元素

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    set<int> s = {10, 20, 30, 40, 50};
    
    // 错误做法：尝试修改元素
    // for (auto& elem : s) {
    //     elem *= 2;  // 编译错误！
    // }
    
    // 正确做法：删除后重新插入
    for (int old_val : {20, 40}) {
        auto it = s.find(old_val);
        if (it != s.end()) {
            int new_val = old_val * 10;
            s.erase(it);
            s.insert(new_val);
        }
    }
    
    return 0;
}
```

### 4. 自定义类型的比较函数

```cpp
#include <set>
#include <iostream>
#include <string>
using namespace std;

struct Person {
    string name;
    int age;
    
    // 定义比较函数（按年龄）
    bool operator<(const Person& other) const {
        return age < other.age;
    }
};

struct PersonCompare {
    bool operator()(const Person& a, const Person& b) const {
        return a.age < b.age;
    }
};

int main() {
    // 方式1：在类中定义operator<
    set<Person> s1;
    s1.insert({"Alice", 25});
    s1.insert({"Bob", 30});
    
    // 方式2：使用自定义比较函数
    set<Person, PersonCompare> s2;
    s2.insert({"Alice", 25});
    s2.insert({"Bob", 30});
    
    return 0;
}
```

## multiset的特殊用法

### 统计频率

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    multiset<int> freq;
    vector<int> data = {1, 2, 2, 3, 3, 3, 4, 4, 4, 4, 5};
    
    // 统计每个数出现的次数
    for (int x : data) {
        freq.insert(x);
    }
    
    // 利用multiset的性质来统计频率
    auto it = freq.begin();
    while (it != freq.end()) {
        int value = *it;
        auto range = freq.equal_range(value);
        int count = distance(range.first, range.second);
        cout << value << "出现了" << count << "次" << endl;
        it = range.second;
    }
    
    return 0;
}
```

## C++20新特性

### contains函数（C++20）

```cpp
#include <set>
// C++20
// int main() {
//     set<int> s = {1, 2, 3, 4, 5};
//     
//     if (s.contains(3)) {
//         cout << "3存在" << endl;
//     }
//     
//     return 0;
// }
```

### map中的contains（C++20）

与`set`类似，`map`也有`contains`成员函数。

## 总结与建议

`set`是ACM竞赛中处理有序数据集合问题的核心工具。以下是一些关键建议：

首先，理解`set`的底层实现是红黑树，所有操作的时间复杂度都是O(log n)。这个复杂度虽然不如`unordered_set`的O(1)，但`set`提供了`unordered_set`所不具备的有序操作能力。

其次，熟练掌握`lower_bound`、`upper_bound`和`equal_range`等二分查找操作，这些操作在处理范围查询、排名计算等问题时非常有用。

第三，在使用自定义类型作为`set`的元素时，必须确保类型定义了正确的比较运算符或提供自定义比较函数。比较函数必须是严格弱序的。

第四，注意`set`中元素不可修改的约束。如果需要修改元素，必须先删除再插入。

第五，根据具体需求在`set`和`unordered_set`之间权衡。如果需要有序操作，选择`set`；如果只需要快速查找且不需要有序，选择`unordered_set`。

第六，对于需要存储重复元素的场景，使用`multiset`代替`set`。