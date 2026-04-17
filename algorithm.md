# C++标准库算法完全指南

## algorithm头文件概述

`<algorithm>`头文件是C++标准库中最重要的头文件之一，包含了大量的通用算法函数。这些算法可以应用于各种容器（主要是序列容器），提供了排序、查找、变换、删除、比较等核心操作。在ACM竞赛中，熟练掌握这些算法对于提高编码效率和程序性能至关重要。

本章节将详细介绍竞赛中最常用的算法，包括它们的函数原型、使用方法、时间复杂度分析、适用场景以及注意事项。所有算法都支持C++11及更高标准，部分高级特性基于C++17或C++20。

### 算法分类概览

本头文件中的算法可以分为以下几个主要类别：

**不修改序列的算法**：这类算法只读取容器中的元素，不会修改容器的结构或元素值。包括`for_each`、`find`、`find_if`、`count`、`adjacent_find`、`mismatch`、`equal`、`search`等函数。这些算法的时间复杂度通常是线性的O(n)，适用于只需要读取数据而不改变数据的场景。

**修改序列的算法**：这类算法会修改容器中的元素值或容器结构。包括`copy`、`move`、`swap`、`transform`、`replace`、`fill`、`generate`、`remove`、`unique`、`reverse`、`rotate`、`shuffle`等函数。使用这些算法时需要注意容器是否支持写入操作，以及迭代器是否有效。

**划分算法**：这类算法根据某种条件将序列划分为两部分。包括`partition`、`stable_partition`、`partition_copy`、`partition_point`等函数。这些算法在需要对数据进行分类处理时非常有用。

**排序相关算法**：这是竞赛中使用最频繁的算法类别。包括`sort`、`stable_sort`、`partial_sort`、`nth_element`、`is_sorted`等函数。排序算法的时间复杂度从O(n log n)到O(n²)不等，选择合适的算法对性能影响很大。

**二分查找算法**：在已排序序列中进行高效查找的算法。包括`lower_bound`、`upper_bound`、`binary_search`、`equal_range`等函数。这些算法的时间复杂度为O(log n)，是竞赛中处理查找问题的利器。

**集合算法**：针对两个有序序列进行集合运算的算法。包括`set_union`、`set_intersection`、`set_difference`、`set_symmetric_difference`等函数。这些算法的时间复杂度为O(n + m)，适用于集合运算场景。

**堆相关算法**：利用堆数据结构的算法。包括`make_heap`、`push_heap`、`pop_heap`、`sort_heap`、`is_heap`等函数。这些算法与`priority_queue`容器配合使用效果极佳。

**比较算法**：对序列元素进行比较的算法。包括`lexicographical_compare`、`lexicographical_compare_three_way`、`min`、`max`、`minmax`等函数。这些算法简化了元素比较的操作。

**排列算法**：生成序列各种排列的算法。包括`next_permutation`、`prev_permutation`、`is_permutation`等函数。这些算法在解决排列相关问题时非常有用。

## 查找算法详解

### find和find_if函数

`find`函数用于在指定范围内查找第一个等于给定值的元素。其函数原型为：

```cpp
template<class InputIt, class T>
InputIt find(InputIt first, InputIt last, const T& value);
```

函数返回一个迭代器，指向找到的元素；如果未找到，则返回`last`迭代器。该函数使用`operator==`进行元素比较。对于自定义类型的元素，需要确保该类型定义了相等比较运算符。

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 3, 5, 7, 9, 2, 4, 6, 8};
    auto it = find(v.begin(), v.end(), 5);
    if (it != v.end()) {
        cout << "找到元素: " << *it << "，位置: " << (it - v.begin()) << endl;
    } else {
        cout << "未找到元素" << endl;
    }
    // 输出: 找到元素: 5，位置: 2
    return 0;
}
```

`find_if`函数是`find`的高级版本，它接受一个谓词（函数或lambda表达式），查找第一个满足谓词条件的元素。其函数原型为：

```cpp
template<class InputIt, class UnaryPredicate>
InputIt find_if(InputIt first, InputIt last, UnaryPredicate p);
```

这个函数在竞赛中非常常用，特别是需要根据复杂条件查找元素时：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 3, 5, 7, 9, 2, 4, 6, 8};
    
    // 查找第一个大于5的元素
    auto it1 = find_if(v.begin(), v.end(), [](int x) { return x > 5; });
    cout << "第一个大于5的元素: " << *it1 << endl;  // 输出: 7
    
    // 查找第一个负数（假设存在）
    auto it2 = find_if(v.begin(), v.end(), [](int x) { return x < 0; });
    if (it2 == v.end()) {
        cout << "未找到负数" << endl;
    }
    
    // 查找第一个偶数
    auto it3 = find_if(v.begin(), v.end(), [](int x) { return x % 2 == 0; });
    cout << "第一个偶数: " << *it3 << endl;  // 输出: 2
    
    return 0;
}
```

**注意事项**：这两个函数的时间复杂度为O(n)，在最坏情况下需要遍历整个序列。对于需要频繁查找的场景，应该考虑使用关联容器（如`unordered_map`、`unordered_set`）来将查找复杂度降低到O(1)。

### find_if_not函数（C++11）

`find_if_not`是`find_if`的补充版本，它查找第一个不满足谓词条件的元素。这在需要找到第一个"例外"元素时非常有用：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {2, 4, 6, 8, 10, 7, 12, 14};
    
    // 查找第一个非偶数（即第一个奇数）
    auto it = find_if_not(v.begin(), v.end(), [](int x) { return x % 2 == 0; });
    cout << "第一个奇数: " << *it << endl;  // 输出: 7
    
    return 0;
}
```

### count和count_if函数

`count`函数用于统计指定范围内等于给定值的元素个数：

```cpp
template<class InputIt, class T>
typename iterator_traits<InputIt>::difference_type
count(InputIt first, InputIt last, const T& value);
```

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 2, 3, 2, 4, 2, 5, 2};
    int cnt = count(v.begin(), v.end(), 2);
    cout << "数字2出现了 " << cnt << " 次" << endl;  // 输出: 5
    return 0;
}
```

`count_if`函数统计满足谓词条件的元素个数：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    int cnt = count_if(v.begin(), v.end(), [](int x) { return x % 2 == 0; });
    cout << "偶数个数: " << cnt << endl;  // 输出: 4
    return 0;
}
```

### adjacent_find函数

`adjacent_find`用于查找两个相邻且相等的元素，返回第一个相等相邻元素的迭代器：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 3, 4, 5, 5, 5, 6};
    auto it = adjacent_find(v.begin(), v.end());
    if (it != v.end()) {
        cout << "找到相邻相等的元素: " << *it << " 和 " << *(it + 1) << endl;
        cout << "位置: " << (it - v.begin()) << endl;
    }
    // 输出: 找到相邻相等的元素: 3 和 3，位置: 2
    return 0;
}
```

如果需要查找相邻满足任意条件的元素，可以使用重载版本：

```cpp
adjacent_find(first, last, BinaryPredicate p);
```

### search函数

`search`函数在一个序列中查找子序列第一次出现的位置：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> text = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    vector<int> pattern = {4, 5, 6};
    
    auto it = search(text.begin(), text.end(), pattern.begin(), pattern.end());
    if (it != text.end()) {
        cout << "子序列从位置 " << (it - text.begin()) << " 开始" << endl;
    }
    // 输出: 子序列从位置 3 开始
    
    return 0;
}
```

`search`还支持自定义相等比较谓词，这在处理复杂比较逻辑时非常有用。

### find_end函数

`find_end`与`search`类似，但它查找的是子序列**最后一次**出现的位置：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> text = {1, 2, 3, 2, 3, 2, 3, 4};
    vector<int> pattern = {2, 3};
    
    auto it = find_end(text.begin(), text.end(), pattern.begin(), pattern.end());
    if (it != text.end()) {
        cout << "子序列最后一次从位置 " << (it - text.begin()) << " 开始" << endl;
    }
    // 输出: 子序列最后一次从位置 5 开始
    
    return 0;
}
```

## 二分查找算法详解

二分查找是竞赛中使用最频繁的算法之一，C++标准库提供了完整的二分查找函数族。这些函数都要求序列是**已排序**的，并且默认使用`<`运算符进行升序排序。

### lower_bound函数

`lower_bound`返回第一个**不小于**（即大于等于）给定值的元素的位置。函数原型为：

```cpp
template<class ForwardIt, class T>
ForwardIt lower_bound(ForwardIt first, ForwardIt last, const T& value);
```

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 2, 3, 3, 3, 4, 5, 5};
    int x = 3;
    
    auto it = lower_bound(v.begin(), v.end(), x);
    cout << "第一个大于等于 " << x << " 的元素位置: " << (it - v.begin()) << endl;
    cout << "元素值: " << *it << endl;
    // 输出: 第一个大于等于 3 的元素位置: 3，元素值: 3
    
    // 查找不存在的元素
    x = 3.5;
    it = lower_bound(v.begin(), v.end(), x);
    cout << "第一个大于等于 " << x << " 的元素位置: " << (it - v.begin()) << endl;
    // 输出: 第一个大于等于 3.5 的元素位置: 6（值为4）
    
    return 0;
}
```

**重要应用**：使用`lower_bound`判断元素是否存在：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

bool binary_search(vector<int>& v, int target) {
    auto it = lower_bound(v.begin(), v.end(), target);
    return (it != v.end() && *it == target);
}

int main() {
    vector<int> v = {1, 2, 2, 3, 3, 3, 4, 5, 5};
    cout << binary_search(v, 3) << endl;  // 输出: 1 (true)
    cout << binary_search(v, 6) << endl;  // 输出: 0 (false)
    return 0;
}
```

### upper_bound函数

`upper_bound`返回第一个**大于**给定值的元素的位置：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 2, 3, 3, 3, 4, 5, 5};
    int x = 3;
    
    auto it = upper_bound(v.begin(), v.end(), x);
    cout << "第一个大于 " << x << " 的元素位置: " << (it - v.begin()) << endl;
    cout << "元素值: " << *it << endl;
    // 输出: 第一个大于 3 的元素位置: 6，元素值: 4
    
    // 计算等于x的元素个数（upper_bound - lower_bound）
    auto range = equal_range(v.begin(), v.end(), x);
    cout << "3出现的次数: " << (range.second - range.first) << endl;
    // 输出: 3出现的次数: 3
    
    return 0;
}
```

### equal_range函数

`equal_range`返回等于给定值的元素范围的上下界（pair形式）：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 2, 3, 3, 3, 4, 5, 5};
    
    // 等于3的范围
    auto range3 = equal_range(v.begin(), v.end(), 3);
    cout << "3的范围: [" << (range3.first - v.begin()) << ", " 
         << (range3.second - v.begin()) << ")" << endl;
    cout << "3的个数: " << (range3.second - range3.first) << endl;
    // 输出: 3的范围: [3, 6)
    // 输出: 3的个数: 3
    
    // 等于2的范围
    auto range2 = equal_range(v.begin(), v.end(), 2);
    cout << "2的范围: [" << (range2.first - v.begin()) << ", " 
         << (range2.second - v.begin()) << ")" << endl;
    // 输出: 2的范围: [1, 3)
    
    return 0;
}
```

### binary_search函数

`binary_search`简单判断元素是否存在，返回bool值：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    cout << binary_search(v.begin(), v.end(), 5) << endl;  // true
    cout << binary_search(v.begin(), v.end(), 10) << endl; // false
    return 0;
}
```

**重要注意事项**：这些二分查找函数默认使用`<`运算符进行升序排序。如果需要降序排序或使用自定义比较，需要提供比较函数对象：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    // 降序排序
    vector<int> v = {9, 8, 7, 6, 5, 4, 3, 2, 1};
    
    // 使用greater比较器进行二分查找
    auto it = lower_bound(v.begin(), v.end(), 5, greater<int>());
    cout << "第一个小于等于5的元素: " << *it << endl;
    
    // 使用greater时，lower_bound的行为是找第一个不大于（即小于等于）
    // 对于[9,8,7,6,5,4,3,2,1]，lower_bound(v, 5, greater) 返回4的位置
    
    return 0;
}
```

### 自定义比较器的二分查找

对于自定义类型的二分查找，必须确保比较器与排序时使用的比较器一致：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

struct Person {
    string name;
    int age;
    bool operator<(const Person& other) const {
        return age < other.age;  // 按年龄排序
    }
};

int main() {
    vector<Person> people = {
        {"Alice", 25},
        {"Bob", 30},
        {"Charlie", 35}
    };
    
    Person target{"Unknown", 30};
    auto it = lower_bound(people.begin(), people.end(), target);
    cout << "找到: " << it->name << ", 年龄: " << it->age << endl;
    
    return 0;
}
```

## 排序算法详解

### sort函数

`sort`是竞赛中使用最频繁的排序函数，它实现了快速排序的平均情况，时间复杂度为O(n log n)。其函数原型为：

```cpp
template<class RandomIt>
void sort(RandomIt first, RandomIt last);

template<class RandomIt, class Compare>
void sort(RandomIt first, RandomIt last, Compare comp);
```

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5};
    sort(v.begin(), v.end());
    
    for (int x : v) {
        cout << x << " ";
    }
    cout << endl;
    // 输出: 1 1 2 3 3 4 5 5 5 6 9
    
    return 0;
}
```

**自定义排序**：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5};
    
    // 降序排序
    sort(v.begin(), v.end(), greater<int>());
    
    // 或者使用lambda表达式
    sort(v.begin(), v.end(), [](int a, int b) { return a > b; });
    
    return 0;
}
```

**结构体排序**：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
#include <string>
using namespace std;

struct Student {
    string name;
    int score;
    int id;
};

// 按分数降序，分数相同按学号升序
bool cmp(const Student& a, const Student& b) {
    if (a.score != b.score) return a.score > b.score;
    return a.id < b.id;
}

int main() {
    vector<Student> students = {
        {"Alice", 90, 1001},
        {"Bob", 85, 1002},
        {"Charlie", 90, 1003},
        {"David", 80, 1004}
    };
    
    sort(students.begin(), students.end(), cmp);
    
    for (const auto& s : students) {
        cout << s.name << ": " << s.score << endl;
    }
    
    return 0;
}
```

**stable_sort函数**：保持相等元素的相对顺序：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

struct Item {
    int key;
    string value;
    int original_index;
};

int main() {
    vector<Item> items = {
        {1, "A", 0},
        {2, "B", 1},
        {1, "C", 2},
        {2, "D", 3}
    };
    
    sort(items.begin(), items.end(), [](const Item& a, const Item& b) {
        return a.key < b.key;
    });
    
    // 排序后1和2的相对顺序可能改变
    for (const auto& item : items) {
        cout << "key=" << item.key << ", value=" << item.value 
             << ", original=" << item.original_index << endl;
    }
    
    return 0;
}
```

### nth_element函数

`nth_element`用于部分排序，它将第n个元素放置在排序后应该出现的位置，且左侧所有元素不大于它，右侧所有元素不小于它。时间复杂度为O(n)平均情况：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5};
    
    // 找到第3小元素（索引2，0-based）
    nth_element(v.begin(), v.begin() + 2, v.end());
    
    cout << "第3小元素: " << v[2] << endl;
    
    // 现在v中前3个元素是所有元素中最小的3个，但不保证顺序
    cout << "最小的3个元素: ";
    for (int i = 0; i < 3; i++) {
        cout << v[i] << " ";
    }
    cout << endl;
    
    return 0;
}
```

**典型应用场景**：查找第k大/小的元素，求中位数等。

### partial_sort函数

`partial_sort`对序列的前m个元素进行排序，使它们是整个序列中最小的m个元素（按升序）：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5};
    
    // 对前5个元素排序（这5个是原序列中最小的5个）
    partial_sort(v.begin(), v.begin() + 5, v.end());
    
    cout << "最小的5个元素（已排序）: ";
    for (int i = 0; i < 5; i++) {
        cout << v[i] << " ";
    }
    cout << endl;
    
    return 0;
}
```

### is_sorted系列函数

`is_sorted`检查序列是否已排序：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v1 = {1, 2, 3, 4, 5};
    vector<int> v2 = {1, 3, 2, 4, 5};
    
    cout << is_sorted(v1.begin(), v1.end()) << endl;  // true
    cout << is_sorted(v2.begin(), v2.end()) << endl;  // false
    
    // 查找第一个未排序的位置
    auto it = is_sorted_until(v1.begin(), v1.end());
    if (it == v1.end()) {
        cout << "v1已完全排序" << endl;
    }
    
    return 0;
}
```

## 变换与修改算法

### copy函数

`copy`将一个范围内的元素复制到另一个位置：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> src = {1, 2, 3, 4, 5};
    vector<int> dst(5);
    
    copy(src.begin(), src.end(), dst.begin());
    
    // dst现在为{1, 2, 3, 4, 5}
    
    // 复制到cout（输出元素）
    copy(src.begin(), src.end(), ostream_iterator<int>(cout, " "));
    // 输出: 1 2 3 4 5
    
    return 0;
}
```

`copy_if`（C++11）只复制满足条件的元素：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> src = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    vector<int> dst;
    
    copy_if(src.begin(), src.end(), back_inserter(dst), 
            [](int x) { return x % 2 == 0; });
    
    for (int x : dst) {
        cout << x << " ";
    }
    // 输出: 2 4 6 8
    
    return 0;
}
```

`copy_backward`从后向前复制：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5, 0, 0, 0, 0, 0};
    
    // 将{1,2,3,4,5}复制到从位置5开始的位置
    copy_backward(v.begin(), v.begin() + 5, v.end());
    
    for (int x : v) {
        cout << x << " ";
    }
    // 输出: 1 2 3 4 5 1 2 3 4 5
    
    return 0;
}
```

### move函数（C++11）

`move`将元素移动到新位置（源元素变为有效但未指定的状态）：

```cpp
#include <algorithm>
#include <vector>
#include <string>
#include <iostream>
using namespace std;

int main() {
    vector<string> src = {"a", "b", "c", "d"};
    vector<string> dst(4);
    
    move(src.begin(), src.end(), dst.begin());
    
    // src中的字符串现在处于有效但未指定的状态
    // dst中的字符串包含了原来的值
    
    return 0;
}
```

### transform函数

`transform`对范围内的每个元素应用一个函数，并将结果存储到另一位置：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
#include <cctype>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5};
    vector<int> result(5);
    
    // 一元变换：每个元素加倍
    transform(v.begin(), v.end(), result.begin(), 
              [](int x) { return x * 2; });
    
    for (int x : result) {
        cout << x << " ";
    }
    cout << endl;
    // 输出: 2 4 6 8 10
    
    // 二元变换：两个向量对应元素相加
    vector<int> a = {1, 2, 3};
    vector<int> b = {4, 5, 6};
    vector<int> c(3);
    
    transform(a.begin(), a.end(), b.begin(), c.begin(),
              [](int x, int y) { return x + y; });
    
    for (int x : c) {
        cout << x << " ";
    }
    // 输出: 5 7 9
    
    return 0;
}
```

### replace系列函数

`replace`将所有等于old_value的元素替换为new_value：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 2, 4, 2, 5};
    
    replace(v.begin(), v.end(), 2, 20);
    
    for (int x : v) {
        cout << x << " ";
    }
    // 输出: 1 20 3 20 4 20 5
    
    return 0;
}
```

`replace_if`替换满足条件的元素：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    
    replace_if(v.begin(), v.end(),
               [](int x) { return x % 2 == 0; }, 0);
    
    for (int x : v) {
        cout << x << " ";
    }
    // 输出: 1 0 3 0 5 0 7 0 9
    
    return 0;
}
```

### fill和generate函数

`fill`将范围内的所有元素设置为指定值：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v(10);
    fill(v.begin(), v.end(), 42);
    
    for (int x : v) {
        cout << x << " ";
    }
    // 输出: 42 42 42 42 42 42 42 42 42 42
    
    return 0;
}
```

`generate`使用生成函数为每个元素赋值：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
#include <cstdlib>
using namespace std;

int main() {
    vector<int> v(10);
    
    // 生成随机数
    generate(v.begin(), v.end(), []() { return rand() % 100; });
    
    for (int x : v) {
        cout << x << " ";
    }
    
    return 0;
}
```

### remove系列函数

`remove`将所有等于value的元素"移除"（实际上是将其余元素前移）：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 2, 4, 2, 5};
    
    // remove返回新的终点迭代器
    auto new_end = remove(v.begin(), v.end(), 2);
    
    // erase掉不再需要的元素
    v.erase(new_end, v.end());
    
    for (int x : v) {
        cout << x << " ";
    }
    // 输出: 1 3 4 5
    
    return 0;
}
```

**经典用法：Erase-Remove Idiom**：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    
    // 删除所有偶数
    v.erase(remove_if(v.begin(), v.end(),
                      [](int x) { return x % 2 == 0; }),
            v.end());
    
    for (int x : v) {
        cout << x << " ";
    }
    // 输出: 1 3 5 7 9
    
    return 0;
}
```

### unique函数

`unique`移除相邻的重复元素：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 1, 2, 2, 2, 3, 3, 1, 1};
    
    v.erase(unique(v.begin(), v.end()), v.end());
    
    for (int x : v) {
        cout << x << " ";
    }
    // 输出: 1 2 3 1
    
    return 0;
}
```

## 序列操作算法

### reverse函数

`reverse`反转序列中元素的顺序：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5};
    reverse(v.begin(), v.end());
    
    for (int x : v) {
        cout << x << " ";
    }
    // 输出: 5 4 3 2 1
    
    return 0;
}
```

### rotate函数

`rotate`将序列旋转，使middle元素成为新的首元素：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    
    // 将位置3的元素旋转到前面
    rotate(v.begin(), v.begin() + 3, v.end());
    
    for (int x : v) {
        cout << x << " ";
    }
    // 输出: 4 5 6 7 8 9 1 2 3
    
    return 0;
}
```

**旋转算法的典型应用**：在循环数组中处理连续子数组：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    
    // 找到最大元素的位置
    auto max_it = max_element(v.begin(), v.end());
    rotate(v.begin(), max_it, v.end());
    
    // 现在最大元素在首位
    cout << "首位元素: " << v[0] << endl;
    
    return 0;
}
```

### shuffle函数（C++17）

`shuffle`随机打乱序列中的元素：

```cpp
#include <algorithm>
#include <vector>
#include <random>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    
    // 使用随机数引擎
    random_device rd;
    mt19937 g(rd());
    
    shuffle(v.begin(), v.end(), g);
    
    for (int x : v) {
        cout << x << " ";
    }
    
    return 0;
}
```

### partition系列函数

`partition`根据谓词将序列划分为两部分，满足条件的在前，不满足的在后：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    
    // 所有偶数在前，奇数在后
    auto pivot = partition(v.begin(), v.end(), 
                           [](int x) { return x % 2 == 0; });
    
    cout << "偶数: ";
    for (auto it = v.begin(); it != pivot; ++it) {
        cout << *it << " ";
    }
    cout << endl;
    
    cout << "奇数: ";
    for (auto it = pivot; it != v.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;
    
    return 0;
}
```

`stable_partition`保持相等元素的相对顺序：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

struct Item {
    int key;
    string value;
    int original_pos;
};

int main() {
    vector<Item> items = {
        {1, "A", 0}, {2, "B", 1}, {1, "C", 2}, {3, "D", 3}
    };
    
    stable_partition(items.begin(), items.end(),
                     [](const Item& item) { return item.key == 1; });
    
    for (const auto& item : items) {
        cout << "key=" << item.key << ", value=" << item.value 
             << ", original=" << item.original_pos << endl;
    }
    // 注意：partition后两个1的相对位置可能改变，
    // 而stable_partition会保持原来的相对位置
    
    return 0;
}
```

## 堆算法

C++标准库提供了一系列操作堆的算法，这些算法与`priority_queue`容器配合使用效果极佳。

### make_heap函数

`make_heap`将一个序列创建为堆：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6, 5};
    
    // 创建最大堆
    make_heap(v.begin(), v.end());
    
    // 堆顶元素
    cout << "堆顶元素: " << v.front() << endl;
    
    return 0;
}
```

### push_heap函数

`push_heap`将新元素插入堆中：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6, 5};
    make_heap(v.begin(), v.end());
    
    // 添加新元素
    v.push_back(10);
    push_heap(v.begin(), v.end());
    
    cout << "堆顶元素: " << v.front() << endl;
    
    return 0;
}
```

### pop_heap函数

`pop_heap`将堆顶元素移到序列末尾，并重新调整堆：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6, 5};
    make_heap(v.begin(), v.end());
    
    // 弹出堆顶元素
    pop_heap(v.begin(), v.end());
    int max_elem = v.back();
    v.pop_back();
    
    cout << "最大元素: " << max_elem << endl;
    cout << "新的堆顶: " << v.front() << endl;
    
    return 0;
}
```

### sort_heap函数

`sort_heap`将堆转换为排序序列：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6, 5};
    make_heap(v.begin(), v.end());
    
    // 堆排序
    sort_heap(v.begin(), v.end());
    
    for (int x : v) {
        cout << x << " ";
    }
    // 输出: 1 1 2 3 4 5 5 6 9
    
    return 0;
}
```

### is_heap系列函数

`is_heap`检查序列是否是堆：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {9, 6, 5, 3, 1};
    cout << is_heap(v.begin(), v.end()) << endl;  // true
    
    vector<int> v2 = {9, 6, 7, 3, 1};
    cout << is_heap(v2.begin(), v2.end()) << endl;  // false
    
    // 找到第一个不是堆的位置
    auto it = is_heap_until(v.begin(), v.end());
    
    return 0;
}
```

## 集合算法

### set_union函数

`set_union`计算两个有序序列的并集：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> a = {1, 2, 3, 4, 5};
    vector<int> b = {3, 4, 5, 6, 7};
    vector<int> result;
    
    set_union(a.begin(), a.end(), b.begin(), b.end(),
              back_inserter(result));
    
    for (int x : result) {
        cout << x << " ";
    }
    // 输出: 1 2 3 4 5 6 7
    
    return 0;
}
```

### set_intersection函数

`set_intersection`计算两个有序序列的交集：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> a = {1, 2, 3, 4, 5};
    vector<int> b = {3, 4, 5, 6, 7};
    vector<int> result;
    
    set_intersection(a.begin(), a.end(), b.begin(), b.end(),
                     back_inserter(result));
    
    for (int x : result) {
        cout << x << " ";
    }
    // 输出: 3 4 5
    
    return 0;
}
```

### set_difference函数

`set_difference`计算两个有序序列的差集（在A中但不在B中）：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> a = {1, 2, 3, 4, 5};
    vector<int> b = {3, 4, 5, 6, 7};
    vector<int> result;
    
    set_difference(a.begin(), a.end(), b.begin(), b.end(),
                   back_inserter(result));
    
    for (int x : result) {
        cout << x << " ";
    }
    // 输出: 1 2
    
    return 0;
}
```

### set_symmetric_difference函数

`set_symmetric_difference`计算对称差集（在A或B中但不同时在两者中）：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> a = {1, 2, 3, 4, 5};
    vector<int> b = {3, 4, 5, 6, 7};
    vector<int> result;
    
    set_symmetric_difference(a.begin(), a.end(), b.begin(), b.end(),
                             back_inserter(result));
    
    for (int x : result) {
        cout << x << " ";
    }
    // 输出: 1 2 6 7
    
    return 0;
}
```

## 排列算法

### next_permutation函数

`next_permutation`生成序列的下一个排列（按字典序）：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3};
    
    do {
        for (int x : v) {
            cout << x << " ";
        }
        cout << endl;
    } while (next_permutation(v.begin(), v.end()));
    
    // 输出:
    // 1 2 3
    // 1 3 2
    // 2 1 3
    // 2 3 1
    // 3 1 2
    // 3 2 1
    
    return 0;
}
```

### prev_permutation函数

`prev_permutation`生成序列的上一个排列：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {3, 2, 1};
    
    do {
        for (int x : v) {
            cout << x << " ";
        }
        cout << endl;
    } while (prev_permutation(v.begin(), v.end()));
    
    // 输出:
    // 3 2 1
    // 3 1 2
    // 2 3 1
    // 2 1 3
    // 1 3 2
    // 1 2 3
    
    return 0;
}
```

### is_permutation函数（C++11）

`is_permutation`检查两个序列是否是彼此的排列：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> a = {1, 2, 3, 4, 5};
    vector<int> b = {5, 4, 3, 2, 1};
    vector<int> c = {1, 2, 3, 4, 6};
    
    cout << is_permutation(a.begin(), a.end(), b.begin()) << endl;  // true
    cout << is_permutation(a.begin(), a.end(), c.begin()) << endl;  // false
    
    return 0;
}
```

## 比较算法

### min和max函数

```cpp
#include <algorithm>
#include <iostream>
using namespace std;

int main() {
    cout << max(1, 2) << endl;  // 2
    cout << min(1, 2) << endl;  // 1
    
    // initializer_list版本（C++11）
    cout << max({1, 2, 3, 4, 5}) << endl;  // 5
    cout << min({1, 2, 3, 4, 5}) << endl;  // 1
    
    // 同时获取min和max
    auto [mn, mx] = minmax({1, 2, 3, 4, 5});
    cout << mn << " " << mx << endl;  // 1 5
    
    return 0;
}
```

### lexicographical_compare函数

`lexicographical_compare`按字典序比较两个序列：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> a = {1, 2, 3};
    vector<int> b = {1, 2, 4};
    
    cout << lexicographical_compare(a.begin(), a.end(), 
                                    b.begin(), b.end()) << endl;
    // true，因为3 < 4
    
    vector<int> c = {1, 2};
    vector<int> d = {1, 2, 3};
    
    cout << lexicographical_compare(c.begin(), c.end(),
                                    d.begin(), d.end()) << endl;
    // true，因为c是d的前缀
    
    return 0;
}
```

## for_each函数

`for_each`对范围内的每个元素应用一个函数：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5};
    
    // 打印所有元素
    for_each(v.begin(), v.end(), [](int x) { cout << x << " "; });
    cout << endl;
    
    // 修改元素
    for_each(v.begin(), v.end(), [](int& x) { x *= 2; });
    
    // 累加
    int sum = 0;
    for_each(v.begin(), v.end(), [&sum](int x) { sum += x; });
    cout << "sum = " << sum << endl;
    
    return 0;
}
```

## 算法性能注意事项

### 迭代器类型要求

不同的算法对迭代器类型有不同的要求，了解这些要求对于选择正确的算法和避免编译错误非常重要：

| 算法 | 要求的最低迭代器类型 |
|------|----------------------|
| find, count, for_each | InputIterator |
| find_if, count_if | InputIterator |
| lower_bound, upper_bound | ForwardIterator |
| sort, nth_element | RandomAccessIterator |
| copy, transform | OutputIterator |

### 容器选择对算法性能的影响

选择合适的容器可以显著提高算法效率：

- 对于需要随机访问的算法（如sort），优先使用`vector`或`deque`，因为它们支持随机访问迭代器。`list`不支持随机访问迭代器，因此不能直接使用`sort`，但可以使用`list::sort`成员函数。

- 对于需要频繁在序列中间插入/删除元素的场景，考虑使用`list`配合只能双向迭代器移动的算法。

- 对于大量数据的查找操作，使用关联容器（如`unordered_set`、`unordered_map`）通常比在序列中使用`find`更高效。

### 算法复杂度速查表

| 算法 | 平均时间复杂度 | 最坏时间复杂度 | 空间复杂度 |
|------|----------------|----------------|------------|
| find | O(n) | O(n) | O(1) |
| sort | O(n log n) | O(n log n) | O(log n) |
| stable_sort | O(n log n) | O(n² log n) | O(n) |
| nth_element | O(n) | O(n²) | O(1) |
| lower_bound | O(log n) | O(log n) | O(1) |
| partition | O(n) | O(n) | O(1) |
| stable_partition | O(n) | O(n) | O(n) |

### 避免不必要的拷贝

对于大型数据结构，使用移动语义可以避免不必要的拷贝：

```cpp
#include <algorithm>
#include <vector>
#include <string>
using namespace std;

int main() {
    vector<string> src = {"hello", "world", "cpp", "algorithm"};
    vector<string> dst;
    
    // 使用move避免字符串拷贝
    move(src.begin(), src.end(), back_inserter(dst));
    
    // src中的字符串现在处于有效但未指定的状态
    // dst包含了原来的值
    
    return 0;
}
```

## 常见错误与调试技巧

### 迭代器失效问题

在使用算法后，必须注意迭代器可能失效的问题：

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    
    auto it = find(v.begin(), v.end(), 5);
    
    // 在find后插入元素不会使it失效（除非发生重分配）
    v.insert(it + 1, 50);  // 安全，因为是在it之后插入
    
    // erase会使it之后的迭代器失效
    it = find(v.begin(), v.end(), 6);
    v.erase(it);  // it现在失效
    
    // 重新获取有效迭代器
    it = find(v.begin(), v.end(), 7);
    
    return 0;
}
```

### 使用reserve避免不必要的重分配

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> result;
    
    // 预分配足够的空间
    result.reserve(1000);
    
    // 现在大量push_back不会频繁重分配
    for (int i = 0; i < 1000; i++) {
        result.push_back(i);
    }
    
    return 0;
}
```

### 避免使用已删除元素的迭代器

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {1, 2, 3, 4, 5};
    
    // 错误做法：remove后继续使用原来的迭代器
    // auto it = remove(v.begin(), v.end(), 3);
    // cout << *it << endl;  // 未定义行为！
    
    // 正确做法：erase移除多余元素
    v.erase(remove(v.begin(), v.end(), 3), v.end());
    
    // 或者直接使用erase-remove idiom
    return 0;
}
```

## 竞赛中的高级技巧

### 使用lambda表达式捕获外部变量

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    int threshold = 50;
    vector<int> v = {10, 20, 30, 40, 50, 60, 70, 80, 90, 100};
    
    // 捕获threshold用于条件判断
    auto it = find_if(v.begin(), v.end(), 
                      [threshold](int x) { return x > threshold; });
    
    cout << "第一个大于" << threshold << "的元素: " << *it << endl;
    
    // 捕获引用以修改外部变量
    int count = 0;
    for_each(v.begin(), v.end(), 
             [&count](int x) { if (x > threshold) count++; });
    
    cout << "大于" << threshold << "的元素个数: " << count << endl;
    
    return 0;
}
```

### 使用bind创建复杂谓词

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
#include <functional>
using namespace std;
using namespace std::placeholders;

int main() {
    vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    
    // 查找大于5的元素
    auto it = find_if(v.begin(), v.end(), 
                      bind(greater<int>(), _1, 5));
    
    cout << *it << endl;  // 6
    
    // 查找小于threshold的元素
    int threshold = 7;
    it = find_if(v.begin(), v.end(),
                 bind(less<int>(), _1, threshold));
    
    cout << *it << endl;  // 1
    
    return 0;
}
```

### 算法组合使用

```cpp
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5};
    
    // 先排序，再去重
    sort(v.begin(), v.end());
    v.erase(unique(v.begin(), v.end()), v.end());
    
    // 查找元素
    auto it = lower_bound(v.begin(), v.end(), 5);
    
    // 反转
    reverse(v.begin(), it);
    
    return 0;
}
```

## 总结与建议

`<algorithm>`头文件是C++标准库中最强大和最常用的部分之一。熟练掌握这些算法对于ACM竞赛至关重要。以下是一些关键建议：

首先，理解每个算法的时间复杂度和空间复杂度，根据问题规模选择合适的算法。对于大数据集，O(n log n)或更快的算法通常是首选。

其次，熟悉各类容器的特性，选择合适的容器与算法配合使用。`vector`通常是默认选择，但对于特殊需求可以考虑其他容器。

第三，注意迭代器失效问题，特别是在修改容器结构后。使用算法返回的迭代器，并及时更新保存的迭代器。

第四，合理使用lambda表达式，它可以大大提高代码的可读性和灵活性。同时注意捕获方式的选择，避免不必要的拷贝。

最后，多练习实际题目，通过实践加深对算法的理解。算法库虽然强大，但正确使用它们需要对算法原理和容器特性的深入理解。