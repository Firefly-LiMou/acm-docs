# iostream 头文件详解

`<iostream>`是C++标准库中最重要的输入输出头文件，提供了控制台输入输出的基本功能。在ACM竞赛中，熟练掌握`iostream`的各种特性对于高效编程至关重要。

## 1. 标准流对象

C++预定义了四个标准流对象，它们位于`std`命名空间中：

| 对象 | 描述 | 默认关联 |
|------|------|----------|
| `cin` | 标准输入流 | 键盘 |
| `cout` | 标准输出流 | 屏幕 |
| `cerr` | 标准错误流（无缓冲） | 屏幕 |
| `clog` | 标准日志流（有缓冲） | 屏幕 |

**重要说明**：`cerr`和`clog`主要用于错误信息输出。在竞赛中，通常使用`cout`进行所有输出，但了解它们的区别有助于调试程序。

## 2. 基本输入操作

### 2.1  operator>> 重载运算符

`operator>>`是C++中用于格式化输入的重载运算符，根据变量类型自动解析输入。

```cpp
int a;
double b;
char c;
string s;

// 基本类型输入
cin >> a;        // 读取整数
cin >> b;        // 读取浮点数
cin >> c;        // 读取单个字符
cin >> s;        // 读取字符串（以空白符分隔）

// 连续输入（链式调用）
cin >> a >> b >> c >> s;
```

**工作原理**：`operator>>`会自动跳过前导空白符（空格、换行、制表符等），然后读取字符直到遇到下一个空白符。这使得多个变量可以在同一行或不同行读取。

**注意事项**：
- 对于`char`类型，`>>`会跳过空白符。如果需要读取空白符，应使用`cin.get()`或`cin.get(char&)`。
- 读取字符串时，`>>`会在第一个空白符处停止，不会读取包含空格的字符串。

### 2.2 get() 函数家族

`get()`函数提供了更精细的字符读取控制，是处理特殊输入场景的关键工具。

```cpp
int cin.get();  // 无参数版本：读取单个字符，返回其ASCII值

char ch;
cin.get(ch);    // 引用版本：读取单个字符到ch

// 读取一行，保留分隔符
char buffer[100];
cin.get(buffer, 100);           // 读取最多99个字符到buffer，遇到换行符停止（不提取）
cin.get(buffer, 100, ':');      // 读取直到遇到':'或达到上限

// 提取并丢弃换行符
cin.get();                       // 从输入流中读取并丢弃一个字符
cin.ignore();                    // 忽略一个字符
cin.ignore(100);                 // 忽略最多100个字符
cin.ignore(100, '\n');           // 忽略最多100个字符或直到遇到换行符
```

**使用场景详解**：

```cpp
// 场景1：读取包含空格的整行字符串
string line;
getline(cin, line);              // 注意：这是getline函数，不是get

// 场景2：在读取数字后读取换行符
int n;
cin >> n;
cin.ignore();                    // 丢弃输入缓冲区中的换行符

// 场景3：读取直到特定分隔符
char data[100];
cin.get(data, 100, ',');         // 读取逗号前的所有字符
```

**注意事项**：
- `cin.get()`无参数版本返回`int`而非`char`，这是因为它需要返回EOF标记（通常是-1）。
- 当读取到文件末尾时，`get()`返回`EOF`。
- `ignore()`的默认参数是`1`，即忽略一个字符。

### 2.3 getline() 函数

`getline()`是读取整行文本的标准方法，在竞赛中处理字符串输入时非常重要。

```cpp
// C++风格string版本（推荐）
string s;
getline(cin, s);                 // 从标准输入读取一行到s

// 指定分隔符版本
getline(cin, s, ':');            // 读取直到遇到冒号

// C风格字符数组版本
char buffer[100];
cin.getline(buffer, 100);        // 读取一行到buffer（自动添加'\0'）
cin.getline(buffer, 100, ':');   // 读取直到冒号
```

**string版本的特殊用法**：

```cpp
// 循环读取多行
string line;
while (getline(cin, line)) {
    cout << line << endl;
}

// 使用getline和stringstream解析行内数据
string line, word;
getline(cin, line);
istringstream iss(line);
while (iss >> word) {
    cout << word << endl;
}
```

**与`operator>>`的重要区别**：
| 特性 | `operator>>` | `getline()` |
|------|--------------|-------------|
| 分隔符 | 空白符 | 换行符（或指定字符） |
| 跳过空白符 | 是 | 否 |
| 保留空白符 | 否 | 是 |
| 读取空行 | 否 | 是 |

### 2.4 read() 和 gcount() 函数

`read()`用于读取固定数量的原始字节，`gcount()`返回上一次格式化输入读取的字符数。

```cpp
char buffer[256];
cin.read(buffer, 100);           // 读取100个字符到buffer
cout << cin.gcount() << endl;    // 输出实际读取的字符数

// 读取二进制数据时很有用
struct Data {
    int id;
    char name[32];
};
Data d;
cin.read(reinterpret_cast<char*>(&d), sizeof(d));
```

**使用场景**：主要适用于二进制数据处理或需要精确控制读取字节数的场景。

### 2.5 peek() 和 putback() 函数

`peek()`用于查看下一个字符而不将其从流中移除，`putback()`将字符放回流中。

```cpp
char ch = cin.peek();            // 查看下一个字符，不提取
cout << ch << endl;              // 下一个字符仍然在流中

cin.putback(ch);                 // 将字符放回流中
```

**使用场景**：

```cpp
// 向前看型解析
while (cin.peek() != EOF) {
    if (isdigit(cin.peek())) {
        int num;
        cin >> num;
        cout << "Number: " << num << endl;
    } else {
        char ch = cin.get();
        cout << "Char: " << ch << endl;
    }
}
```

## 3. 基本输出操作

### 3.1 operator<< 重载运算符

`operator<<`是C++中用于格式化输出的重载运算符。

```cpp
int a = 100;
double b = 3.14159;
char c = 'A';
string s = "Hello";

// 基本类型输出
cout << a;           // 输出整数
cout << b;           // 输出浮点数
cout << c;           // 输出字符
cout << s;           // 输出字符串

// 连续输出（链式调用）
cout << a << " " << b << " " << c << " " << s << endl;

// 输出十六进制和八进制
cout << hex << 255 << endl;   // 输出 "ff"
cout << oct << 255 << endl;   // 输出 "377"
cout << dec << 255 << endl;   // 恢复十进制

// 设置基数前缀
cout << showbase << hex << 255 << endl;  // 输出 "0xff"
```

**注意**：基数修改（`hex`、`oct`、`dec`）是持久的，会影响后续所有整数输出。

### 3.2 put() 函数

`put()`用于输出单个字符。

```cpp
cout.put('A');       // 输出字符 'A'
cout.put('\n');      // 输出换行符

// 可以连续调用
cout.put('H').put('e').put('l').put('l').put('o');
```

### 3.3 write() 函数

`write()`用于输出指定数量的字符。

```cpp
const char* msg = "Hello, World!";
cout.write(msg, 5);              // 输出 "Hello"
cout << endl;

// 可以结合其他输出
cout.write(msg, 5) << "..." << endl;
```

## 4. 流状态管理

### 4.1 状态标志位

每个iostream对象都有四个状态标志位：

| 标志位 | 含义 |
|--------|------|
| `goodbit` | 一切正常（值为0） |
| `eofbit` | 到达文件末尾 |
| `failbit` | 操作失败（如类型不匹配） |
| `badbit` | 不可恢复的错误 |

```cpp
// 检查状态
if (cin.good()) {
    cout << "Stream is good" << endl;
}

if (cin.fail()) {
    cout << "Operation failed" << endl;
}

if (cin.eof()) {
    cout << "End of file reached" << endl;
}

if (cin.bad()) {
    cout << "Irrecoverable error" << endl;
}

// 综合检查（推荐方式）
if (!cin) {
    cout << "Stream has failed or is at EOF" << endl;
}
```

### 4.2 清除状态

```cpp
cin.clear();                     // 清除所有错误标志，重置为goodbit
cin.clear(ios::failbit);        // 设置特定标志

// 清除特定标志
cin.clear(cin.rdstate() & ~ios::failbit);  // 清除failbit
```

### 4.3 同步控制

```cpp
ios::sync_with_stdio(false);     // 关闭C++流与C标准I/O的同步（大幅提升I/O性能）
cin.tie(nullptr);               // 取消cin与cout的绑定（进一步提升性能）

// 竞赛中推荐的初始化方式
ios::sync_with_stdio(false);
cin.tie(nullptr);
```

**性能影响**：
- `ios::sync_with_stdio(false)`：`cin`/`cout`性能提升约5-10倍
- `cin.tie(nullptr)`：在混合使用`cin`和`cout`时避免不必要的flush

**重要警告**：关闭同步后，不能混用C和C++的I/O函数（如`scanf`/`printf`和`cin`/`cout`）。

## 5. 常用输出技巧

### 5.1 endl vs '\n'

```cpp
cout << endl;    // 输出换行符并刷新输出缓冲区
cout << '\n';    // 仅输出换行符，不刷新

// 性能建议
cout << "result: " << value << '\n';  // 推荐，效率更高
cout << "result: " << value << endl;  // 仅在需要立即刷新时使用
```

### 5.2 调试输出宏

```cpp
#define DEBUG
#ifdef DEBUG
#define debug(x) cerr << #x << " = " << x << endl
#else
#define debug(x)
#endif

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int x = 42;
    debug(x);  // 输出: x = 42
}
```

### 5.3 错误输出

```cpp
// 使用cerr输出错误信息（不会被输出重定向影响）
if (error_condition) {
    cerr << "Error: invalid input at line " << __LINE__ << endl;
}
```

## 6. 竞赛最佳实践

### 6.1 快速输入模板

```cpp
// 快速整数输入（适用于大数据量）
inline bool readInt(int &x) {
    int c = getchar();
    if (c == EOF) return false;
    while (c < '0' || c > '9') {
        if (c == '-') {
            int sign = 1;
            c = getchar();
            break;
        }
        c = getchar();
    }
    x = 0;
    while (c >= '0' && c <= '9') {
        x = x * 10 + (c - '0');
        c = getchar();
    }
    return true;
}
```

### 6.2 完整竞赛模板

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    // 输入输出代码
    int n;
    while (cin >> n) {
        // 处理
    }
    
    return 0;
}
```

### 6.3 处理大数据量输入

```cpp
// 方法1：使用scanf（如果不需要C++流特性）
#include <cstdio>

int main() {
    int n;
    scanf("%d", &n);
    // ...
}

// 方法2：关闭同步后使用cin
#include <iostream>
int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int n;
    cin >> n;
    // ...
}

// 方法3：自定义快速输入类
class FastInput {
    static const int BUFSIZE = 1 << 20;
    int idx, size;
    char buf[BUFSIZE];
public:
    FastInput(): idx(0), size(0) {}
    inline char read() {
        if (idx >= size) {
            size = fread(buf, 1, BUFSIZE, stdin);
            idx = 0;
            if (size == 0) return EOF;
        }
        return buf[idx++];
    }
    template<typename T>
    bool nextInt(T &out) {
        char c;
        T sign = 1;
        T num = 0;
        c = read();
        if (c == EOF) return false;
        while (c != '-' && (c < '0' || c > '9')) {
            c = read();
            if (c == EOF) return false;
        }
        if (c == '-') {
            sign = -1;
            c = read();
        }
        for (; c >= '0' && c <= '9'; c = read()) {
            num = num * 10 + (c - '0');
        }
        out = num * sign;
        return true;
    }
};
```

## 7. 常见问题与注意事项

### 7.1 输入缓冲区问题

```cpp
// 问题：混合使用getline和>>后，getline读取到空行
int n;
string s;
cin >> n;              // 读取数字
getline(cin, s);       // 读取到空行（残留换行符）

// 解决方案：在>>后调用ignore()
cin >> n;
cin.ignore();          // 忽略换行符
getline(cin, s);       // 正确读取下一行
```

### 7.2 char类型输入问题

```cpp
// 问题：使用>>读取char时会跳过空白符
char c1, c2;
cin >> c1 >> c2;   // 输入 "a b"，c1='a', c2='b'（跳过空格）

// 如果需要读取所有字符（包括空白符）
char c1, c2;
cin.get(c1).get(c2);   // 读取两个字符，包括空格
```

### 7.3 字符串截断问题

```cpp
// 问题：输入超出缓冲区大小会导致缓冲区溢出
char buf[10];
cin >> buf;   // 输入超过9个字符会溢出

// 解决方案：使用string或限制读取长度
string s;          // 推荐，无缓冲区溢出风险
cin >> s;

char buf[10];
cin.getline(buf, 10);  // 安全，自动添加终止符
```

### 7.4 状态位忽略问题

```cpp
// 问题：未检查流状态导致无限循环
int x;
while (cin >> x) {     // 当输入不是整数时，循环不会退出
    // ...
}

// 解决方案：正确处理输入错误
int x;
while (true) {
    if (!(cin >> x)) {
        if (cin.eof()) break;
        cin.clear();   // 清除错误状态
        string bad_input;
        cin >> bad_input;  // 跳过错误输入
        continue;
    }
    // 处理x
}
```

## 8. 性能优化总结

| 优化措施 | 性能提升 | 使用场景 |
|----------|----------|----------|
| `ios::sync_with_stdio(false)` | ~5-10倍 | 所有竞赛程序 |
| `cin.tie(nullptr)` | 额外提升 | 频繁交替输入输出 |
| 使用`'\n'`代替`endl` | 避免不必要的flush | 正常输出 |
| 使用`scanf`代替`cin` | ~2-3倍 | 超大数据量 |
| 自定义快速输入 | 最大性能 | 极大量数据输入 |

## 9. 总结

| 功能 | 函数/方法 | 复杂度 |
|------|-----------|--------|
| 格式化输入 | `cin >> var` | O(k) |
| 读取字符 | `cin.get()` | O(1) |
| 读取字符串 | `getline(cin, s)` | O(k) |
| 查看下一个字符 | `cin.peek()` | O(1) |
| 字符放回流 | `cin.putback(c)` | O(1) |
| 格式化输出 | `cout << var` | O(k) |
| 输出字符 | `cout.put(c)` | O(1) |
| 状态检查 | `cin.fail()`等 | O(1) |

掌握`iostream`的这些特性，将帮助您在ACM竞赛中更高效地处理各种输入输出需求。