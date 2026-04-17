# string 头文件详解

`<string>`头文件定义了C++标准库中的`std::string`类，这是ACM竞赛中最常用的字符串处理工具。相比C风格字符串，`std::string`提供了自动内存管理、安全的边界检查和丰富的成员函数，是现代C++编程的首选。

## 1. string类概述

`std::string`是一个模板类`basic_string<char>`的特化，位于`std`命名空间中。它封装了字符序列的所有权管理，提供了高效的字符串操作接口。

### 1.1 头文件和包含方式

```cpp
#include <string>           // 标准方式
#include <bits/stdc++.h>    // 竞赛常用，包含了所有标准库
using namespace std;        // 使用std命名空间
```

### 1.2 构造函数的完整用法

string类提供了多种构造函数，适用于不同的初始化场景：

```cpp
// 默认构造函数 - 创建空字符串
string s1;                          // s1 = ""

// 拷贝构造函数 - 创建副本
string s2 = s1;                     // s2是s1的副本
string s3(s1);                      // 显式拷贝构造

// 移动构造函数 - 高效转移资源（C++11）
string s4 = std::move(s1);          // s1变为空字符串

// C风格字符串初始化
const char* cstr = "Hello";
string s5(cstr);                    // 从C字符串构造
string s6("World");                 // 从字符串字面量构造
string s7("Hello", 3);              // 前3个字符: "Hel"
string s8("Hello", 1, 2);           // 从索引1开始取2个字符: "el"

// 重复字符初始化
string s9(5, 'a');                  // "aaaaa"
string s10(10, '*');                // "**********"

// 范围初始化（迭代器）
vector<char> v = {'H', 'e', 'l', 'l', 'o'};
string s11(v.begin(), v.end());     // "Hello"

//  initializer_list初始化（C++11）
string s12{'H', 'e', 'l', 'l', 'o'}; // "Hello"

// 子串构造
string s13 = s5.substr(1, 3);       // 从索引1开始取3个字符
```

**使用场景分析**：
- 默认构造函数：需要先构建空字符串，后续可能动态拼接时使用
- 拷贝构造函数：需要独立副本时使用，注意深拷贝的性能开销
- 移动构造函数：函数返回或变量交换时使用，可避免不必要的拷贝
- C字符串构造：与C API交互或处理字符串字面量时使用
- 重复字符初始化：创建分隔符、占位符等场景

**复杂度**：构造函数的复杂度为O(n)，其中n为被复制或构造的字符数。

### 1.3 赋值操作

```cpp
string s;

// operator= 重载
s = "Hello";                        // C字符串赋值
s = 'A';                            // 单字符赋值（C++11）
s = s1;                             // 另一个string对象赋值
s = std::move(s1);                  // 移动赋值（C++11）

// assign成员函数
s.assign("World");                  // 完全替换
s.assign("Hello", 3);               // 只取前3个字符
s.assign("Hello", 1, 3);            // 从索引1开始取3个
s.assign(5, 'x');                   // 5个'x'
s.assign(v.begin(), v.end());       // 范围赋值

// 交换操作（高效）
s1.swap(s2);                        // 交换两个字符串的内容
swap(s1, s2);                       // 非成员函数版swap，更推荐
```

**性能提示**：赋值操作的时间复杂度为O(n)，其中n为新字符串的长度。移动赋值在可能的情况下会采用O(1)的复杂度。

## 2. 字符串元素访问

### 2.1 下标访问 operator[]

```cpp
string s = "Hello";

// 只读访问
char c1 = s[0];                     // 'H'
char c2 = s[1];                     // 'e'

// 可写访问
s[0] = 'h';                         // s变为 "hello"
s[4] = '!';                         // s变为 "hell!"

// 越界行为：未定义行为（可能崩溃或返回垃圾值）
char c3 = s[100];                   // 危险！未定义行为

// at()方法提供边界检查
char c4 = s.at(0);                  // 'H'
// char c5 = s.at(100);             // 抛出 out_of_range 异常
```

**重要区别**：
- `operator[]`：不进行边界检查，速度快但不安全
- `at()`：进行边界检查，速度稍慢但安全，抛出`std::out_of_range`异常

### 2.2 迭代器访问

```cpp
string s = "Hello";

// 正向迭代器
for (auto it = s.begin(); it != s.end(); ++it) {
    cout << *it << ' ';
}
// 输出: H e l l o

// 反向迭代器
for (auto rit = s.rbegin(); rit != s.rend(); ++rit) {
    cout << *rit << ' ';
}
// 输出: o l l e H

// const迭代器
for (auto cit = s.cbegin(); cit != s.cend(); ++cit) {
    // *cit = 'X';  // 错误：只读
}

// 范围for循环（C++11）
for (char c : s) {
    cout << c << ' ';
}

// 带引用的范围for循环（可修改）
for (char &c : s) {
    c = toupper(c);                 // 将所有字符转为大写
}
```

**迭代器失效**：当字符串进行重新分配（如`reserve`不足导致扩容）时，所有迭代器都会失效。

### 2.3 front() 和 back() 方法（C++11）

```cpp
string s = "Hello";

char first = s.front();             // 'H'，等价于 s[0]
char last = s.back();               // 'o'，等价于 s[s.size()-1]

// 可修改
s.front() = 'J';                    // s变为 "Jello"
s.back() = 'y';                     // s变为 "Jelly"
```

## 3. 容量操作

### 3.1 基础容量查询

```cpp
string s = "Hello";

// size() 和 length() - 完全等价
size_t len1 = s.size();             // 5
size_t len2 = s.length();           // 5

// empty() - 判断是否为空
bool is_empty = s.empty();          // false
string e;
bool is_empty2 = e.empty();         // true

// max_size() - 可容纳的最大长度
size_t max_len = s.max_size();      // 通常是 size_t 的最大值 / 2 左右

// capacity() - 当前分配的内存容量
size_t cap = s.capacity();          // 通常 >= 5，可能是15或更多
```

**容量管理策略**：标准库通常采用指数级增长策略（如 doubling 策略），当容量不足时重新分配更大的内存，通常为当前容量的1.5倍或2倍。

### 3.2 容量调整

```cpp
string s = "Hello";

// reserve() - 预分配内存（避免多次重新分配）
s.reserve(100);                     // 预分配至少能容纳100字符的内存
// 注意：如果请求的容量 <= 当前容量，什么都不做

// shrink_to_fit() - 释放多余内存（C++11）
s.shrink_to_fit();                  // 使capacity等于size

// resize() - 改变字符串长度
s.resize(10);                       // "Hello\0\0\0\0\0"（用空字符填充）
s.resize(10, '*');                  // "Hello*****"
s.resize(3);                        // 截断为 "Hel"
```

**使用场景**：
- `reserve()`：当预先知道最终字符串大小时，预先分配内存可以避免多次重新分配，提高性能
- `shrink_to_fit()`：在内存敏感的应用中释放多余内存
- `resize()`：需要固定长度字符串或填充特定字符时使用

### 3.3 清空和释放内存

```cpp
string s = "Hello World";

// clear() - 清空内容（不释放内存）
s.clear();                          // s变为空字符串，但capacity不变
// 复杂度: O(1) - 只是设置size为0

// 释放内存的两种方法
string().swap(s);                   // 方法1：创建空字符串并交换
s.shrink_to_fit();                  // 方法2：C++11的shrink_to_fit
```

**性能提示**：`clear()`只是将字符串设为空，不释放已分配的内存。如果需要释放内存，应使用上述方法之一。

## 4. 字符串拼接与追加

### 4.1 operator+= 操作符

```cpp
string s = "Hello";

// 追加字符串
s += " World";                      // "Hello World"

// 追加字符
s += '!';                           // "Hello World!"

// 追加另一个string对象
string suffix = "!!!";
s += suffix;                        // "Hello World!!!"
```

### 4.2 append() 成员函数

```cpp
string s = "Hello";

// append() 提供更多选项
s.append(" World");                 // "Hello World"
s.append("Hello", 2, 3);           // 从"Hello"索引2开始取3个字符: "llo"
s.append(5, '*');                  // 追加5个'*'
s.append(suffix);                  // 追加另一个string
s.append(suffix, 0, 3);            // 追加suffix的前3个字符
s.append(v.begin(), v.end());      // 从迭代器范围追加
```

### 4.3 push_back() 和 pop_back()

```cpp
string s = "Hello";

s.push_back('.');                  // "Hello." - 只能追加单个字符

// pop_back() - 删除最后一个字符（C++11）
s.pop_back();                      // 从"Hello."变回"Hello"
// 警告：对空字符串调用pop_back()是未定义行为
```

### 4.4 insert() 插入操作

```cpp
string s = "Hello World";

// 在指定位置插入
s.insert(5, " Beautiful");          // "Hello Beautiful World" - 在索引5前插入

// 插入另一个string的部分内容
string add = " Very Long String";
s.insert(5, add, 0, 5);             // "Hello Very World" - 插入add的前5个字符

// 插入重复字符
s.insert(0, 10, '*');               // "**********Hello World"

// 插入迭代器范围
vector<char> v = {'<', '>'};
s.insert(s.begin(), v.begin(), v.end());  // "<Hello World>"
```

### 4.5 operator+ 返回新字符串

```cpp
string a = "Hello";
string b = " World";

// operator+ 创建新字符串
string c = a + b;                   // "Hello World"
string d = a + " World";            // 与C字符串混用
string e = 'H' + a;                 // 字符在前也支持（C++11）
string f = a + 42;                  // 错误！不支持与整数直接相加
```

**性能注意**：每次使用`operator+`都会创建新的字符串对象并复制内容，可能导致多次内存分配。对于大量字符串拼接，应考虑使用`+=`或`stringstream`。

## 5. 字符串比较

### 5.1 operator==, !=, <, <=, >, >=

```cpp
string s1 = "Apple";
string s2 = "Banana";
string s3 = "Apple";
string s4 = "apple";

// 相等比较
bool eq = (s1 == s3);               // true
bool ne = (s1 == s2);               // false

// 不等比较
bool not_eq = (s1 != s2);           // true

// 字典序比较（基于ASCII值）
bool lt = (s1 < s2);                // true（'A' < 'B'）
bool gt = (s1 > s4);                // true（'A' < 'a'，小写字母ASCII值更大）
bool le = (s1 <= s3);               // true
bool ge = (s1 >= s3);               // true

// 比较运算符可以链式使用
bool result = (s1 < s2 && s2 < s3); // true
```

**比较规则**：字符串比较采用字典序（lexicographical order），逐字符比较直到出现差异或到达字符串末尾。

### 5.2 compare() 成员函数

```cpp
string s1 = "Apple";
string s2 = "Banana";
string s3 = "App";

// compare() 返回值: <0, =0, >0
int r1 = s1.compare(s2);            // <0（s1 < s2）
int r2 = s2.compare(s1);            // >0（s2 > s1）
int r3 = s1.compare(s3);            // >0（s1 > s3，s1更长）
int r4 = s3.compare(s1);            // <0（s3 < s1）

// 与子串比较
// compare(起始位置, 长度, 另一个字符串)
int r5 = s1.compare(0, 3, "App");   // =0（完全匹配）
int r6 = s1.compare(0, 3, "Api");   // <0（'p' < 'i'）

// 比较子串与子串
int r7 = s1.compare(0, 3, s2, 0, 3);  // 比较s1[0,3)与s2[0,3)
```

### 5.3 compare() 与 memcmp 对比

```cpp
// compare() 的返回值含义
// 返回值 < 0: 当前对象 < 参数字符串
// 返回值 = 0: 两个字符串相等
// 返回值 > 0: 当前对象 > 参数字符串

// 这种设计使其可以直接用作比较函数
vector<string> words = {"Apple", "Banana", "Cherry"};
sort(words.begin(), words.end());   // 使用operator<进行排序
sort(words.begin(), words.end(), 
     [](const string &a, const string &b) {
         return a.compare(b) > 0;   // 降序排序
     });
```

## 6. 字符串搜索

### 6.1 find() 和 rfind()

```cpp
string s = "Hello World, Hello";

// find() - 正向查找（返回第一次出现的位置）
size_t pos1 = s.find("Hello");              // 0
size_t pos2 = s.find("Hello", 1);           // 13（从索引1开始查找）
size_t pos3 = s.find('l');                  // 2
size_t pos4 = s.find('l', 3);               // 3
size_t pos5 = s.find("abc");                // string::npos（未找到）

// rfind() - 反向查找（返回最后一次出现的位置）
size_t rpos1 = s.rfind("Hello");            // 13
size_t rpos2 = s.rfind("Hello", 10);        // 0（在索引0-10范围内找最后一次）
size_t rpos3 = s.rfind('l');                // 10（最后一个'l'的位置）

// 查找失败处理
if (pos5 == string::npos) {
    cout << "Substring not found" << endl;
}
```

**使用场景**：
- `find()`：查找第一次出现的位置，适用于验证是否存在
- `rfind()`：查找最后一次出现的位置，适用于替换最后一个匹配

### 6.2 find_first_of() 和 find_last_of()

```cpp
string s = "Hello World";

// find_first_of() - 查找参数中任意字符第一次出现的位置
size_t p1 = s.find_first_of("aeiou");       // 1 ('e')
size_t p2 = s.find_first_of("aeiou", 5);    // 4 ('o'，从索引5开始)
size_t p3 = s.find_first_of("xyz");         // npos（都不存在）

// find_last_of() - 查找参数中任意字符最后一次出现的位置
size_t p4 = s.find_last_of("aeiou");        // 7 ('o'）
size_t p5 = s.find_last_of("aeiou", 10);    // 7（从索引0-10范围内找最后）

// find_first_not_of() - 查找不在参数中的第一个字符
size_t p6 = s.find_first_not_of("Helo Wrd"); // 3 ('l'，第一个不在集合中的字符)

// find_last_not_of() - 查找不在参数中的最后一个字符
size_t p7 = s.find_last_not_of("Hello World"); // npos（所有字符都在集合中）
```

**与find()的关键区别**：
- `find()`：查找**子串**"Hello"的完整匹配
- `find_first_of()`：查找**任意字符**'H'、'e'、'l'、'l'、'o'的第一次出现

### 6.3 搜索使用场景详解

```cpp
// 场景1：解析逗号分隔的值
string line = "apple,banana,cherry";
size_t start = 0;
while (true) {
    size_t pos = line.find(',', start);
    if (pos == string::npos) {
        cout << line.substr(start) << endl;  // 最后一个元素
        break;
    }
    cout << line.substr(start, pos - start) << endl;
    start = pos + 1;
}

// 场景2：提取文件名中的扩展名
string filename = "document.txt";
size_t dot_pos = filename.find_last_of('.');
if (dot_pos != string::npos) {
    string ext = filename.substr(dot_pos + 1);
    cout << "Extension: " << ext << endl;  // "txt"
}

// 场景3：移除字符串前后的空白符
string with_spaces = "   Hello World   ";
size_t first = with_spaces.find_first_not_of(" \t\n");
size_t last = with_spaces.find_last_not_of(" \t\n");
if (first != string::npos) {
    string trimmed = with_spaces.substr(first, last - first + 1);
}

// 场景4：检查字符串是否只包含字母
string test = "Hello123";
bool is_alpha = test.find_first_not_of(
    "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
) == string::npos;
```

### 6.4 find() 返回值详解

```cpp
// string::npos 是一个特殊的静态常量
// 通常定义为 -1 或 size_t 的最大值
cout << string::npos << endl;  // 输出 18446744073709551615 (64位系统)

// 检查返回值
size_t pos = s.find("pattern");
if (pos != string::npos) {
    // 找到处理
}

// 注意：npos 是 size_t 类型的最大值，比较时要注意
// 下面这样写是错误的：
if (pos == -1) {  // -1会被提升为size_t，变成很大的数
    // 错误！
}
```

## 7. 字符串截取与替换

### 7.1 substr() 子串提取

```cpp
string s = "Hello World";

// 基本用法
string sub1 = s.substr(6);                  // "World"（从索引6到末尾）
string sub2 = s.substr(0, 5);               // "Hello"（从索引0开始5个字符）
string sub3 = s.substr(6, 100);             // "World"（超出范围自动截断）

// 与find结合使用
size_t space_pos = s.find(' ');
string first_word = s.substr(0, space_pos); // "Hello"

// 边界情况
string empty = s.substr(s.size());          // ""（从末尾开始）
```

### 7.2 replace() 替换操作

```cpp
string s = "Hello World";

// replace(起始位置, 长度, 替换字符串)
s.replace(6, 5, "C++");                     // "Hello C++"
s.replace(0, 5, "Hi");                      // "Hi World"
s.replace(0, 5, "Hello World", 5, 3);       // 使用源字符串的子串

// replace(迭代器范围, 替换字符串)
s.replace(s.begin(), s.begin() + 5, "Hey"); // "Hey World"
s.replace(s.begin(), s.end(), "Replaced");  // "Replaced"

// 替换为重复字符
s.replace(0, 5, 10, '*');                   // "********* World"

// 从另一个字符串替换部分内容
string source = "Beautiful";
s.replace(6, 5, source, 0, 5);              // 从source取5个字符替换
```

### 7.3 erase() 删除操作

```cpp
string s = "Hello World";

// erase(起始位置, 长度) - 删除指定范围的字符
s.erase(5, 1);                              // 删除空格: "HelloWorld"
s.erase(0, 6);                              // 删除前6个字符: "World"

// erase(迭代器) - 删除单个位置
s.erase(s.begin() + 1);                     // 删除第二个字符

// erase(起始迭代器, 结束迭代器) - 删除范围
s.erase(s.begin(), s.begin() + 2);          // 删除前两个字符

// 清空整个字符串
s.erase();                                  // ""

s.clear();                                  // 等价于 s.erase()
```

### 7.4 字符串修复合并示例

```cpp
// 完整的字符串处理示例
string processString(const string &input) {
    string result = input;
    
    // 1. 移除所有空格
    result.erase(remove(result.begin(), result.end(), ' '), result.end());
    
    // 2. 转换为大写
    for (char &c : result) {
        c = toupper(c);
    }
    
    // 3. 替换特定模式
    size_t pos;
    while ((pos = result.find("XXX")) != string::npos) {
        result.replace(pos, 3, "YYY");
    }
    
    return result;
}
```

## 8. 字符串转换

### 8.1 数值转字符串

```cpp
// to_string() 函数模板（C++11）
int i = 42;
double d = 3.14159;
long l = 123456789L;
unsigned long ul = 123456789UL;

string si = to_string(i);           // "42"
string sd = to_string(d);           // "3.141590"
string sl = to_string(l);           // "123456789"
string sul = to_string(ul);         // "123456789"

// 控制精度
cout << to_string(d);               // 默认6位小数

// 设置精度（C++11，使用sprintf格式）
char buf[32];
snprintf(buf, sizeof(buf), "%.2f", d);
string precision_str = buf;         // "3.14"
```

### 8.2 字符串转数值（C++11）

```cpp
// 基本转换函数
string num_str = "42";

int i = stoi(num_str);              // 42
long l = stol(num_str);             // 42L
long long ll = stoll(num_str);      // 42LL
unsigned long ul = stoul(num_str);  // 42UL
unsigned long long ull = stoull(num_str); // 42ULL

// 浮点转换
string float_str = "3.14159";
float f = stof(float_str);          // 3.14159f
double d = stod(float_str);         // 3.14159
long double ld = stold(float_str);  // 3.14159L

// 进制转换（默认十进制）
string hex_str = "ff";
string bin_str = "1010";
int from_hex = stoi(hex_str, nullptr, 16);   // 255
int from_bin = stoi(bin_str, nullptr, 2);    // 10
int from_oct = stoi(bin_str, nullptr, 8);    // 513
```

### 8.3 指定解析起始位置

```cpp
string mixed = "123abc456";

int num1 = stoi(mixed);                    // 123（停在非数字处）
size_t pos = 0;
int num2 = stoi(mixed, &pos);              // 123，pos变为3
string remaining = mixed.substr(pos);      // "abc"

// 处理包含前缀的字符串
string with_prefix = "0xFF";
int hex_val = stoi(with_prefix, nullptr, 0);  // 0（0x不是十进制）
int hex_val2 = stoi(with_prefix.substr(2), nullptr, 16);  // 255
```

### 8.4 异常处理

```cpp
#include <stdexcept>

string invalid = "abc";

try {
    int val = stoi(invalid);               // 抛出 invalid_argument
} catch (const invalid_argument &e) {
    cerr << "Invalid argument: " << e.what() << endl;
} catch (const out_of_range &e) {
    cerr << "Out of range: " << e.what() << endl;
}

// 安全转换函数
template<typename T>
bool safeStoX(const string &s, T &result) {
    try {
        result = T(s);                     // 假设T有对应的构造函数
        return true;
    } catch (...) {
        return false;
    }
}
```

### 8.5 字符串流转换

```cpp
#include <sstream>

// 使用 stringstream 进行字符串与数值转换
string num_str = "123.456";

// 转数值
istringstream iss(num_str);
double val;
iss >> val;                                // val = 123.456

// 转字符串
ostringstream oss;
oss << val;
string result = oss.str();                 // "123.456"

// 多个值解析
string data = "10 20 30 40";
vector<int> numbers;
istringstream iss2(data);
int num;
while (iss2 >> num) {
    numbers.push_back(num);
}
```

## 9. C字符串转换

### 9.1 c_str() 和 data()

```cpp
string s = "Hello";

// c_str() - 返回const char*，以空字符结尾
const char* cptr = s.c_str();              // 与C API交互时使用

// data() - 返回const char*，C++17后可能不添加空结尾（C++11-14添加空结尾）
const char* dptr = s.data();

// 可写版本（C++17）
char* wptr = s.data();                     // 可修改字符，但不能扩展

// 示例：与C标准库函数交互
string filename = "data.txt";
FILE *f = fopen(s.c_str(), "r");           // 使用c_str()与C API交互

// 注意：c_str()返回的指针在字符串修改后可能失效
s += " World";
// cptr 现在可能指向无效内存！
```

### 9.2 copy() 成员函数

```cpp
string s = "Hello World";
char buffer[20];

// copy() - 将字符串复制到字符数组
size_t len = s.copy(buffer, 5, 0);         // 从索引0开始复制5个字符到buffer
buffer[len] = '\0';                        // 需要手动添加终止符
// buffer = "Hello"

// 参数：(目标缓冲区, 复制长度, 起始位置)
size_t len2 = s.copy(buffer, 3, 6);        // 从索引6开始复制3个字符
buffer[len2] = '\0';
// buffer = "Wor"
```

### 9.3 length() vs size() vs strlen()

```cpp
string s = "Hello";

// c_str()用于与C函数交互时
strlen(s.c_str());                         // 5

// string 成员
s.length();                                // 5
s.size();                                  // 5

// std::strlen 可以直接用于 string（C++有模板特化）
std::strlen(s.c_str());                    // 需要c_str()
```

## 10. 字符串迭代器高级用法

### 10.1 查找替换的迭代器版本

```cpp
string s = "Hello World, Hello";

// 使用迭代器查找
auto it = find(s.begin(), s.end(), 'l');   // 第一个'l'
if (it != s.end()) {
    cout << "Found at index: " << distance(s.begin(), it) << endl;
}

// 使用迭代器删除
auto new_end = remove(s.begin(), s.end(), 'l');  // 将'l'移到末尾
s.erase(new_end, s.end());                 // 删除移动后的'l'

// 使用迭代器替换
auto pos = find(s.begin(), s.end(), ' ');
if (pos != s.end()) {
    s.replace(pos, pos + 1, "_");          // 空格替换为下划线
}
```

### 10.2 transform 算法

```cpp
#include <algorithm>

string s = "Hello World";

// 转换为大写
transform(s.begin(), s.end(), s.begin(), 
          [](unsigned char c) { return toupper(c); });
// s = "HELLO WORLD"

// 转换为小写
transform(s.begin(), s.end(), s.begin(),
          [](unsigned char c) { return tolower(c); });

// 首字母大写
string title = "hello world";
transform(title.begin(), title.end(), title.begin(),
          [](unsigned char c, unsigned char prev) {
              return isspace(prev) ? toupper(c) : c;
          });
title[0] = toupper(title[0]);
```

### 10.3 使用迭代器的删除操作

```cpp
string s = "Hello World";

// erase-remove 惯用法
// 删除所有指定字符
s.erase(remove(s.begin(), s.end(), 'l'), s.end());

// 删除所有空白符
s.erase(remove_if(s.begin(), s.end(), 
                  [](char c) { return isspace(c); }), s.end());

// 删除连续重复字符（仅保留一个）
s.erase(unique(s.begin(), s.end()), s.end());

// 删除特定位置之后的所有字符
auto it = find(s.begin(), s.end(), ' ');
s.erase(it, s.end());
```

## 11. 实用函数汇总

### 11.1 空白符处理

```cpp
#include <cctype>

// 检查空白符
bool is_space(char c) {
    return isspace(static_cast<unsigned char>(c));
}

// 去除首尾空白符
string trim(const string &s) {
    size_t start = s.find_first_not_of(" \t\n\r\f\v");
    if (start == string::npos) return "";
    size_t end = s.find_last_not_of(" \t\n\r\f\v");
    return s.substr(start, end - start + 1);
}

// 去除所有空白符
string remove_spaces(const string &s) {
    string result;
    copy_if(s.begin(), s.end(), back_inserter(result),
            [](char c) { return !isspace(static_cast<unsigned char>(c)); });
    return result;
}

// 去除重复空白符
string normalize_spaces(const string &s) {
    string result;
    bool prev_space = false;
    for (char c : s) {
        bool cur_space = isspace(static_cast<unsigned char>(c));
        if (!prev_space || !cur_space) {
            result += c;
        }
        prev_space = cur_space;
    }
    return result;
}
```

### 11.2 大小写转换

```cpp
// 转大写
string to_upper(const string &s) {
    string result = s;
    for (char &c : result) {
        c = toupper(static_cast<unsigned char>(c));
    }
    return result;
}

// 转小写
string to_lower(const string &s) {
    string result = s;
    for (char &c : result) {
        c = tolower(static_cast<unsigned char>(c));
    }
    return result;
}

// 首字母大写
string capitalize(const string &s) {
    string result = s;
    if (!result.empty()) {
        result[0] = toupper(static_cast<unsigned char>(result[0]));
    }
    return result;
}

// 每个单词首字母大写
string title_case(const string &s) {
    string result = s;
    bool new_word = true;
    for (char &c : result) {
        if (isspace(static_cast<unsigned char>(c))) {
            new_word = true;
        } else if (new_word) {
            c = toupper(static_cast<unsigned char>(c));
            new_word = false;
        } else {
            c = tolower(static_cast<unsigned char>(c));
        }
    }
    return result;
}
```

### 11.3 回文检查

```cpp
bool is_palindrome(const string &s) {
    size_t left = 0, right = s.size() - 1;
    while (left < right) {
        // 跳过非字母字符（可选）
        if (!isalnum(static_cast<unsigned char>(s[left]))) {
            ++left;
            continue;
        }
        if (!isalnum(static_cast<unsigned char>(s[right]))) {
            --right;
            continue;
        }
        if (tolower(static_cast<unsigned char>(s[left])) != 
            tolower(static_cast<unsigned char>(s[right]))) {
            return false;
        }
        ++left;
        --right;
    }
    return true;
}
```

### 11.4 字符串分割

```cpp
#include <vector>
#include <sstream>

// 使用stringstream分割（适合简单分割）
vector<string> split(const string &s, char delim) {
    vector<string> result;
    istringstream iss(s);
    string token;
    while (getline(iss, token, delim)) {
        result.push_back(token);
    }
    return result;
}

// 使用find分割（更通用）
vector<string> split(const string &s, const string &delim) {
    vector<string> tokens;
    size_t start = 0, end = 0;
    while ((end = s.find(delim, start)) != string::npos) {
        tokens.push_back(s.substr(start, end - start));
        start = end + delim.length();
    }
    tokens.push_back(s.substr(start));
    return tokens;
}

// 分割为字符数组
vector<char> split_chars(const string &s) {
    return vector<char>(s.begin(), s.end());
}
```

### 11.5 字符串连接

```cpp
#include <numeric>

// 使用+运算符
string concat1 = "Hello" + " " + "World";

// 使用operator+=
string result;
result += "Hello";
result += " ";
result += "World";

// 使用stringstream
ostringstream oss;
oss << "Hello" << " " << "World";
string s = oss.str();

// 使用join函数（C++20有std::ranges::join_view，C++17手动实现）
template<typename Container>
string join(const Container &items, const string &separator) {
    if (items.empty()) return "";
    ostringstream oss;
    auto it = items.begin();
    oss << *it;
    ++it;
    for (; it != items.end(); ++it) {
        oss << separator << *it;
    }
    return oss.str();
}

vector<string> words = {"Hello", "World", "C++"};
string joined = join(words, ", ");           // "Hello, World, C++"
```

### 11.6 前缀后缀检查

```cpp
// 检查是否以指定前缀开头
bool starts_with(const string &s, const string &prefix) {
    if (s.length() < prefix.length()) return false;
    return s.compare(0, prefix.length(), prefix) == 0;
}

// 检查是否以指定后缀结尾
bool ends_with(const string &s, const string &suffix) {
    if (s.length() < suffix.length()) return false;
    return s.compare(s.length() - suffix.length(), 
                     suffix.length(), suffix) == 0;
}

// C++20有starts_with和ends_as成员函数（C++11-17需要自己实现）
bool starts_with_cxx17(const string &s, const string &prefix) {
    return s.size() >= prefix.size() && 
           s.compare(0, prefix.size(), prefix) == 0;
}

bool ends_with_cxx17(const string &s, const string &suffix) {
    return s.size() >= suffix.size() && 
           s.compare(s.size() - suffix.size(), suffix.size(), suffix) == 0;
}
```

## 12. 性能考虑

### 12.1 避免不必要的拷贝

```cpp
// 效率较低的写法
string s1 = "Hello";
string s2 = s1 + " World";  // 临时字符串 + 多次拷贝

// 更好的写法
string s3 = "Hello";
s3 += " World";             // 直接追加，避免临时对象

// 大量拼接时的最佳实践
string result;
result.reserve(1000);       // 预分配内存

for (int i = 0; i < 100; ++i) {
    result += to_string(i); // 追加
    result += ',';          // 追加
}
```

### 12.2 移动语义

```cpp
// 函数返回时使用移动语义
string create_string() {
    string s = "Hello World";
    return s;               // C++17保证不触发拷贝（RVO或移动）
}

// 显式移动
string s1 = "temporary";
string s2 = std::move(s1);  // s1变为空字符串，s2获得内容

// 交换操作
string a = "Hello";
string b = "World";
swap(a, b);                 // 交换指针，不拷贝内容
```

### 12.3 字符串视图（C++17）

```cpp
#include <string_view>

// string_view 是只读的字符串引用，不拥有所有权
string s = "Hello World";
string_view sv(s.c_str() + 6, 5);  // 指向 "World"

cout << sv << endl;                // "World"

// 避免不必要的字符串创建
void process(string_view sv) {     // 可以接受 const string&, const char*, 字符串字面量
    cout << sv << endl;
}

process("Hello");                  // 直接使用字面量
process(s);                        // 从string创建

// substring操作不拷贝
string_view sub = sv.substr(0, 3);  // 指向原字符串的视图，无拷贝
```

## 13. 常见陷阱与注意事项

### 13.1 字符串索引越界

```cpp
string s = "Hello";

// 危险写法
char c = s[10];                    // 未定义行为！
char c2 = s.at(10);                // 抛出 out_of_range 异常

// 正确做法：先检查
if (index < s.size()) {
    char c = s[index];
}
```

### 13.2 c_str() 生命周期

```cpp
string s = "Hello";
const char* p = s.c_str();

s += " World";                     // p 现在可能指向无效内存！

// 正确做法：使用时确保原字符串不变
const char* p = s.c_str();
useCString(p);                     // 在这里使用
```

### 13.3 比较函数返回值

```cpp
string a = "Apple";
string b = "Banana";

// 错误写法
if (a.compare(b) == true) { ... }  // compare返回int，不是bool

// 正确写法
if (a.compare(b) == 0) { ... }     // 检查是否相等
if (a.compare(b) < 0) { ... }      // a < b
```

### 13.4 find() 返回值判断

```cpp
string s = "Hello";
size_t pos = s.find("X");

// 错误写法
if (pos == -1) { ... }             // -1被转为size_t后是很大的数

// 正确写法
if (pos == string::npos) { ... }
```

### 13.5 处理二进制数据

```cpp
// string 适合存储文本，但也可以存储二进制数据
// 注意：构造函数和append会自动处理空字符

string binary_data;
binary_data.append(reinterpret_cast<const char*>(data), size);

// 访问时使用data()
const char* bytes = binary_data.data();
```

## 14. 复杂度汇总

| 操作 | 平均复杂度 | 最坏复杂度 |
|------|------------|------------|
| `size()`, `length()`, `empty()` | O(1) | O(1) |
| `operator[]` | O(1) | O(1) |
| `at()` | O(1) | O(1) |
| `push_back()`, `pop_back()` | O(1) amortized | O(n) |
| `insert()`, `erase()` | O(n) | O(n) |
| `find()`, `rfind()` | O(n) | O(n) |
| `substr()` | O(k) | O(k) |
| `compare()` | O(min(n,m)) | O(min(n,m)) |
| `operator+` | O(n+m) | O(n+m) |
| `operator+=` | O(m) amortized | O(n) |
| `reserve()` | O(1) | O(1) |
| `resize()` | O(k) | O(k) |

**amortized**：摊还复杂度，指多次操作的总复杂度平摊到每次操作

## 15. 总结

`std::string`是C++标准库中功能最完善的字符串类型，熟练掌握其各种成员函数和使用技巧对于ACM竞赛至关重要。关键要点包括：

1. **安全第一**：使用`at()`代替`operator[]`进行边界检查
2. **性能优化**：预先使用`reserve()`分配内存，减少重新分配
3. **资源管理**：注意`c_str()`返回指针的生命周期
4. **搜索技巧**：灵活运用`find()`系列函数处理各种搜索需求
5. **现代C++**：善用C++11/14/17的新特性，如`string_view`、移动语义等

通过本节的学习，您应该能够熟练处理竞赛中的各种字符串操作需求。