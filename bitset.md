# bitset 头文件详解

`<bitset>`是C++标准库中用于处理位集合的头文件，提供了固定大小的位数组操作。在ACM竞赛中，`bitset`常用于状态压缩、集合表示、位图操作等场景，特别适合需要高效位级操作的题目。

## 1. bitset概述

`std::bitset`是一个模板类，用于表示固定大小的位序列。它提供了一系列位操作函数，可以方便地进行位级别的设置、查询、计数等操作。与使用原始位运算相比，`bitset`提供了更直观、更安全的接口。

**特点**：
- 固定大小：在编译时确定大小
- 空间效率：每位只占1比特
- 便捷操作：提供丰富的成员函数
- 兼容C风格：可以与C风格的位运算互操作

## 2. 创建与初始化

### 2.1 基本构造方式

```cpp
#include <bitset>
#include <iostream>
using namespace std;

int main() {
    // 默认构造：所有位设为0
    bitset<8> b1;                // 00000000
    
    // 整数初始化：从低到高设置位
    bitset<8> b2(5);             // 00000101 (5的二进制)
    
    // 字符串初始化
    bitset<8> b3("11001010");    // 11001010
    
    // 字符串初始化（指定起始位置和长度）
    string s = "11001010";
    bitset<8> b4(s);             // 从字符串构造
    bitset<8> b5(s, 2, 4);      // 从字符串第2位开始取4位
    
    // 二进制字面量（C++14）
    // bitset<8> b6 = 0b11001010;
    
    cout << "b1: " << b1 << endl;
    cout << "b2: " << b2 << endl;
    cout << "b3: " << b3 << endl;
    
    return 0;
}
```

**注意**：字符串中的'1'和'0'从左到右对应高位到低位（即索引0对应最左边的位）。

### 2.2 bitset的大小

```cpp
// 大小必须是编译时常量
bitset<8> b8;    // 8位
bitset<16> b16;  // 16位
bitset<32> b32;  // 32位
bitset<64> b64;  // 64位
bitset<128> b128; // 128位

// 使用变量作为大小（C++11后支持动态bitset需要其他库）
// 注意：标准bitset的大小必须是编译时常量
constexpr size_t N = 100;
bitset<N> b100;  // 正确：N是常量表达式
```

## 3. 位访问操作

### 3.1 下标访问 operator[]

```cpp
bitset<8> b("10101010");

// 只读访问
bool bit0 = b[0];   // 读取第0位（最低位）= 0
bool bit7 = b[7];   // 读取第7位（最高位）= 1

// 可写访问
b[0] = 1;           // 设置第0位为1
b[3] = !b[3];       // 翻转第3位

// 越界访问会引发异常（但编译时无法检测）
// b[10] = 1;       // 危险：未定义行为！
```

### 3.2 测试位

```cpp
bitset<8> b("10101010");

// test() - 测试某位是否为1（会检查边界）
if (b.test(0)) {
    cout << "第0位是1" << endl;
}

// any() - 是否有任何位为1
if (b.any()) {
    cout << "至少有一位为1" << endl;
}

// none() - 是否所有位都为0
if (b.none()) {
    cout << "所有位都是0" << endl;
}

// all() - 是否所有位都为1（C++11）
if (b.all()) {
    cout << "所有位都是1" << endl;
}
```

### 3.3 count() 计数

```cpp
bitset<8> b("10101010");

// count() - 统计1的个数
size_t ones = b.count();    // 4
size_t zeros = b.size() - b.count();  // 0的个数

cout << "1的个数: " << ones << endl;
cout << "0的个数: " << zeros << endl;
```

## 4. 位修改操作

### 4.1 设置位

```cpp
bitset<8> b;

// set() - 所有位设为1
b.set();                   // 11111111

// set(pos, value) - 设置某位为指定值
b.set(0);                 // 将第0位设为1
b.set(3, 1);              // 将第3位设为1
b.set(5, 0);              // 将第5位设为0
```

### 4.2 复位位

```cpp
bitset<8> b("11111111");

// reset() - 所有位设为0
b.reset();                // 00000000

// reset(pos) - 将某位设为0
b.reset(2);               // 将第2位设为0
```

### 4.3 翻转位

```cpp
bitset<8> b("10101010");

// flip() - 翻转所有位
b.flip();                 // 01010101

// flip(pos) - 翻转某位
b.flip(0);                // 翻转第0位
```

## 5. 位逻辑运算

### 5.1 成员运算符

```cpp
bitset<8> a("11001010");
bitset<8> b("10101010");

// 按位与
bitset<8> c = a & b;      // 10001010

// 按位或
bitset<8> d = a | b;      // 11101010

// 按位异或
bitset<8> e = a ^ b;      // 01100000

// 按位取反
bitset<8> f = ~a;         // 00110101

// 赋值版本
a &= b;                   // a = a & b
a |= b;                   // a = a | b
a ^= b;                   // a = a ^ b
```

### 5.2 移位运算

```cpp
bitset<8> a("11001010");

// 左移
bitset<8> left = a << 2;  // 00101000
a <<= 2;                  // a = a << 2

// 右移
bitset<8> right = a >> 3; // 00011001
a >>= 3;                  // a = a >> 3
```

**注意**：移位超出范围时，超出的位会被丢弃，空出的位填充0。

## 6. 与整数的转换

### 6.1 to_ulong() 和 to_ullong()

```cpp
bitset<8> b("11001010");

// 转换为unsigned long（C++11起推荐使用to_ullong）
unsigned long ul = b.to_ulong();      // 202
unsigned long long ull = b.to_ullong(); // 202

// 如果位数超过ulong的范围，会抛出overflow_error
```

### 6.2 从整数构造

```cpp
// 从整数构造bitset
bitset<8> b1(202);         // 202的二进制：11001010
bitset<16> b2(202);       // 低8位为11001010，高8位为0

// 转换为整数
unsigned long val = b1.to_ulong();
```

## 7. 字符串转换

### 7.1 to_string()

```cpp
bitset<8> b("11001010");

// 转换为二进制字符串
string s = b.to_string();  // "11001010"

// 可以指定转换后的字符
string s_custom = b.to_string('X', 'O'); // "XXOXOXOO"
```

## 8. 竞赛应用场景

### 8.1 集合表示

```cpp
// 表示一个较小的集合（如{0, 2, 5}）
bitset<10> S;
S.set(0);
S.set(2);
S.set(5);
// S = 0b1000101 = "0010000100" (高位到低位)

// 判断元素是否在集合中
bool has3 = S.test(3);  // false

// 集合并集
bitset<10> A, B;
// A | B

// 集合交集
// A & B

// 集合差集
// A & (~B)
```

### 8.2 状态压缩DP

```cpp
// 假设有10个位置，每个位置可以是0或1
bitset<10> state;
state.set(0);
state.set(3);
state.set(7);

// 检查某个状态是否满足条件
bool valid = check(state);

// 枚举所有子集
for (bitset<10> sub = state; ; sub = (sub - 1) & state) {
    // 处理子集
    if (sub.none()) break;
}
```

### 8.2 素数筛法

```cpp
// 使用bitset实现埃拉托斯特尼筛法
const int N = 1000000;
bitset<N+1> is_prime;
is_prime.set();           // 假设所有数都是素数
is_prime.reset(0);
is_prime.reset(1);

for (int i = 2; i * i <= N; ++i) {
    if (is_prime.test(i)) {
        // 将i的倍数标记为非素数
        for (int j = i * i; j <= N; j += i) {
            is_prime.reset(j);
        }
    }
}
```

### 8.3 图的连通性

```cpp
// 使用bitset优化图的可达性判断
const int MAXN = 100;
bitset<MAXN> graph[MAXN];  // graph[i][j] = 1表示i到j有边

// Floyd-Warshall的bitset优化
for (int k = 0; k < MAXN; ++k) {
    for (int i = 0; i < MAXN; ++i) {
        if (graph[i].test(k)) {
            graph[i] |= graph[k];
        }
    }
}
```

### 8.4 字符串匹配（位运算）

```cpp
// Rabin-Karp的位运算优化版本
int patternMatch(const string& text, const string& pattern) {
    int n = text.length();
    int m = pattern.length();
    
    if (m > n) return -1;
    
    bitset<256> mask;  // 假设字符集为ASCII
    for (char c : pattern) {
        mask.set(c);
    }
    
    bitset<256> window;
    for (int i = 0; i < m; ++i) {
        if (mask.test(text[i])) {
            window.set(i);
        }
    }
    
    // 滑动窗口检查
    for (int i = m; i < n; ++i) {
        // 检查window是否包含pattern的所有字符
        // 这里需要更复杂的实现
    }
    
    return -1;
}
```

## 9. 完整示例

### 9.1 竞赛常用代码模板

```cpp
#include <bitset>
#include <iostream>
#include <string>
using namespace std;

// 检查一个数的二进制表示中1的个数
int countOnes(unsigned int x) {
    bitset<32> b(x);
    return b.count();
}

// 检查一个数是否为2的幂（只有一位为1）
bool isPowerOfTwo(unsigned int x) {
    return x != 0 && (x & (x - 1)) == 0;
}

// 最低位的1之后全部置1
int lowestBitToAllOnes(unsigned int x) {
    return x | (x - 1);
}

// 获取最低位的1
int lowestBit(unsigned int x) {
    return x & (-x);
}

// 交换两个数（不使用临时变量）
void swap(int& a, int& b) {
    a ^= b;
    b ^= a;
    a ^= b;
}

// 数组中出现奇数次的元素（其他元素都出现偶数次）
int findOddOccurring(int arr[], int n) {
    int result = 0;
    for (int i = 0; i < n; ++i) {
        result ^= arr[i];
    }
    return result;
}

int main() {
    // 基础操作
    bitset<8> b;
    b.set(0);
    b.set(2);
    b.set(5);
    cout << b << endl;  // 00100101
    
    // 计数
    cout << "1的个数: " << b.count() << endl;
    
    // 转换为整数
    unsigned long val = b.to_ulong();
    cout << "值: " << val << endl;
    
    return 0;
}
```

## 10. 注意事项

### 10.1 大小固定

```cpp
// 错误：大小必须是编译时常量
int n = 100;
bitset<n> b;  // 编译错误！

// 正确：使用常量表达式
constexpr int N = 100;
bitset<N> b;  // 正确
```

### 10.2 索引方向

```cpp
bitset<8> b("00000001");

// 索引0对应最低位（右起第1个字符）
bool bit0 = b[0];  // true (值为1)
bool bit7 = b[7];  // false (值为0)

// to_string()输出时，最左边的字符对应最高位
cout << b.to_string();  // "00000001"
```

### 10.3 与unsigned long的转换

```cpp
// 超过范围的转换会抛出异常
bitset<64> b;
// b.set() ... 设置很多位

try {
    unsigned long val = b.to_ulong();  // 可能抛出异常
} catch (const overflow_error& e) {
    cerr << "转换失败: " << e.what() << endl;
}
```

## 11. 复杂度汇总

| 操作 | 时间复杂度 | 说明 |
|------|------------|------|
| `operator[]` | O(1) | 常数时间访问 |
| `test()` | O(1) | 带边界检查的访问 |
| `count()` | O(n) | 统计1的个数 |
| `set()`/`reset()` | O(1) | 设置/复位单个位 |
| `flip()` | O(1) | 翻转单个位 |
| `to_ulong()` | O(n) | 转换为整数 |
| `to_string()` | O(n) | 转换为字符串 |
| `any()`/`none()`/`all()` | O(1) | 检查状态 |
| 位运算 `& \| ^ ~` | O(n) | 按位操作 |
| 移位 `<< >>` | O(n) | 移位操作 |

## 12. 总结

`bitset`是竞赛中处理位级操作的有力工具，特别适用于：

1. **集合表示**：当集合大小固定且较小时
2. **状态压缩**：DP中的状态表示
3. **素数筛法**：高效的内存使用
4. **图论算法**：可达性判断的优化
5. **位标志**：需要同时记录多个布尔状态

熟练掌握`bitset`的各种操作，可以在竞赛中更优雅地解决涉及位操作的问题。
