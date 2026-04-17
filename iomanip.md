# C++输入输出格式化完全指南

## iomanip头文件概述

`<iomanip>`头文件提供了丰富的格式化输出和输入控制功能，这些功能在ACM竞赛中处理输出格式要求时非常有用。虽然竞赛中的输出通常要求简洁明了，但在某些特定场景下（如调试输出、浮点数精度控制、进制转换等），熟练掌握这些格式化工具可以大大提高代码的可读性和效率。

本章节详细介绍各种流控制操纵符的用法，包括精度控制、进制转换、对齐方式、填充字符等。所有格式化操作都基于C++11及更高标准，涵盖了竞赛中可能用到的各种场景。

### 格式化输出基础概念

C++的输入输出流使用一套对象化的格式化系统，通过插入操纵符（manipulator）到流中来实现各种格式化效果。这些操纵符可以分为两大类：一类是直接作用于流的操纵符（如`endl`），另一类是返回流引用以支持链式调用的操纵符（如`setprecision`）。

理解流格式状态的概念对于正确使用格式化功能至关重要。每个流对象都维护着一组格式标志（flags）和格式参数（parameters），这些状态决定了数据如何被格式化。操纵符通过修改这些状态来实现各种格式化效果。

流对象还维护着三个重要的格式域：宽度域（width）、精度域（precision）和填充域（fill）。这三个域分别控制输出的最小宽度、浮点数的显示精度以及用于填充空白区域的字符。

## 精度与小数位数控制

### setprecision函数

`setprecision`是控制浮点数输出精度的核心操纵符。它有两种工作模式：当与`fixed`或`scientific`一起使用时，控制小数点后的位数；当单独使用时，控制有效数字的总数。其函数原型为：

```cpp
#include <iomanip>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double pi = 3.141592653589793;
    
    // 默认精度（6位有效数字）
    cout << "默认精度: " << pi << endl;  // 输出: 3.14159
    
    // 设置精度为10位有效数字
    cout << setprecision(10) << pi << endl;  // 输出: 3.141592654
    
    // 设置精度为3位有效数字
    cout << setprecision(3) << pi << endl;  // 输出: 3.14
    
    // 使用fixed模式，控制小数位数
    cout << fixed << setprecision(4) << pi << endl;  // 输出: 3.1416
    
    // 使用scientific模式，科学计数法
    cout << scientific << setprecision(2) << pi << endl;  // 输出: 3.14e+00
    
    return 0;
}
```

**重要提示**：`setprecision`设置的是精度，而不是小数位数。精度指的是有效数字的总数。例如，数字123.456在精度为4时的输出是"123.5"（四舍五入到4位有效数字），而在精度为2时输出"1.2e+02"。

在竞赛场景中，如果题目要求输出保留N位小数，必须使用`fixed`配合`setprecision(N)`，否则可能会出现科学计数法输出或有效数字数量不对的问题：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    // 题目要求：保留3位小数
    double answer = 2.0 / 3.0;
    
    // 错误做法：可能输出科学计数法或有效数字不对
    // cout << setprecision(3) << answer << endl;
    
    // 正确做法
    cout << fixed << setprecision(3) << answer << endl;  // 输出: 0.667
    
    // 注意：fixed不会自动重置，每次流的状态会保持
    // 如果需要恢复默认行为，使用unsetf或重新设置
    
    return 0;
}
```

### showpoint与noshowpoint

`showpoint`操纵符强制显示小数点，即使数字是整数。这在需要对齐小数点位置的场景中非常有用：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    cout << showpoint;
    
    cout << setprecision(3);
    cout << 42 << endl;    // 输出: 42.0
    cout << 3.14 << endl;  // 输出: 3.14
    cout << 0.5 << endl;   // 输出: 0.500
    
    cout << noshowpoint;
    cout << 42 << endl;    // 输出: 42
    
    return 0;
}
```

## 进制与数值格式

### dec、oct、hex操纵符

这三个操纵符分别设置整数输出的进制为十进制、八进制和十六进制：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    int n = 255;
    
    cout << "十进制: " << dec << n << endl;  // 输出: 255
    cout << "八进制: " << oct << n << endl;  // 输出: 377
    cout << "十六进制: " << hex << n << endl;  // 输出: ff
    
    // 十六进制使用大写字母
    cout << uppercase << hex << n << endl;  // 输出: FF
    cout << nouppercase << endl;  // 恢复小写
    
    return 0;
}
```

**注意**：这些进制设置会影响所有后续的整数输出，包括`cout << hex << 42`这样的输出。竞赛中有时需要临时改变进制然后恢复，避免影响其他输出。

### setbase函数

`setbase`函数可以同时设置进制（参数可以是8、10或16），但实际上它只对8、10、16进制有效：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    int n = 255;
    
    cout << setbase(10) << n << endl;  // 输出: 255
    cout << setbase(16) << n << endl;  // 输出: ff
    cout << setbase(8) << n << endl;   // 输出: 377
    
    return 0;
}
```

### hexfloat和scientific（C++11）

`hexfloat`操纵符以十六进制浮点数格式输出，`scientific`以科学计数法输出：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    double x = 123.456;
    
    cout << "默认: " << x << endl;  // 输出: 123.456
    cout << scientific << x << endl;  // 输出: 1.234560e+02
    cout << hexfloat << x << endl;  // 输出: 0x1.edd2f2a52d220p+6
    
    // 恢复默认格式
    cout << defaultfloat;
    
    return 0;
}
```

## 宽度与对齐控制

### setw函数

`setw`设置下一个输出项的最小宽度。如果输出内容宽度大于设置值，则按实际宽度输出，不会截断：

```cpp
#include <iomanip>
#include <iostream>
#include <string>
using namespace std;

int main() {
    cout << setw(10) << 42 << endl;  // 输出: "        42"（10个字符宽度）
    cout << setw(10) << "hello" << endl;  // 输出: "     hello"
    cout << setw(3) << "hello" << endl;  // 输出: "hello"（实际宽度超过设置值，不截断）
    
    // 连续使用
    cout << setw(5) << 1 << setw(5) << 2 << setw(5) << 3 << endl;
    // 输出: "    1    2    3"
    
    return 0;
}
```

**重要提示**：`setw`只影响下一个输出项，而不是持续有效。这与`setprecision`等其他操纵符不同。如果需要持续设置宽度，需要每次都调用`setw`。

### left、right和internal对齐

`left`设置左对齐，`right`设置右对齐，`internal`设置符号在左，数值在右（用于负数的格式化）：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    int n = -42;
    
    cout << right << setw(10) << n << endl;  // 输出: "       -42"
    cout << left << setw(10) << n << endl;   // 输出: "-42       "
    
    // internal: 符号左对齐，数值右对齐
    cout << internal << setw(10) << n << endl;  // 输出: "-       42"
    
    // 对比正数
    cout << internal << setw(10) << 42 << endl;  // 输出: "        42"
    
    return 0;
}
```

### setfill函数

`setfill`设置用于填充空白区域的字符，默认是空格：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    cout << setfill('*');
    
    cout << right << setw(10) << 42 << endl;   // 输出: "********42"
    cout << left << setw(10) << 42 << endl;    // 输出: "42********"
    cout << internal << setw(10) << -42 << endl;  // 输出: "-********42"
    
    cout << setfill(' ');  // 恢复空格填充
    
    return 0;
}
```

## 字符串格式化

### setiosflags操纵符

`setiosflags`是一个功能强大的操纵符，用于设置多个流状态标志。它接受一个`ios_base::fmtflags`类型的参数，可以同时设置多个标志：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    double pi = 3.14159;
    
    // 设置科学计数法格式
    cout << setiosflags(ios::scientific) << pi << endl;
    
    // 设置固定小数点格式
    cout << setiosflags(ios::fixed) << setprecision(2) << pi << endl;
    
    // 组合多个标志
    cout << setiosflags(ios::scientific | ios::uppercase) << pi << endl;
    
    // 取消设置的标志
    cout << resetiosflags(ios::scientific) << pi << endl;
    
    return 0;
}
```

**常用的格式标志**包括：

| 标志 | 说明 |
|------|------|
| ios::left | 左对齐 |
| ios::right | 右对齐 |
| ios::internal | 内部对齐 |
| ios::dec | 十进制（默认） |
| ios::oct | 八进制 |
| ios::hex | 十六进制 |
| ios::fixed | 定点浮点格式 |
| ios::scientific | 科学计数法 |
| ios::showbase | 显示进制前缀 |
| ios::showpoint | 显示小数点 |
| ios::showpos | 显示正号 |
| ios::uppercase | 大写字母 |
| ios::boolalpha | 布尔值为true/false |
| ios::skipws | 跳过空白字符（输入） |
| ios::unitbuf | 每次输出后刷新缓冲区 |

### showpos操纵符

`showpos`在非负数前显示加号：

```cpp
#include <iostream>
using namespace std;

int main() {
    cout << showpos;
    cout << 42 << endl;  // 输出: +42
    cout << -42 << endl;  // 输出: -42
    
    cout << noshowpos;
    cout << 42 << endl;  // 输出: 42
    
    return 0;
}
```

### boolalpha操纵符

`boolalpha`将布尔值输出为"true"或"false"，而不是1或0：

```cpp
#include <iostream>
using namespace std;

int main() {
    bool flag = true;
    
    cout << flag << endl;  // 输出: 1
    cout << boolalpha << flag << endl;  // 输出: true
    cout << noboolalpha;  // 恢复数字形式
    cout << flag << endl;  // 输出: 1
    
    return 0;
}
```

## 输入格式化控制

### ws操纵符

`ws`操纵符跳过输入流中的空白字符，在读取非空白内容前清除空格：

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string s1, s2;
    
    // 输入: "  hello   world  "
    cin >> s1 >> ws >> s2;
    
    // s1 = "hello"
    // s2 = "world"（ws跳过了hello和world之间的空格）
    
    return 0;
}
```

### skipws和noskipws

`skipws`（默认）在读取时跳过空白字符，`noskipws`则不跳过：

```cpp
#include <iostream>
using namespace std;

int main() {
    char c1, c2, c3;
    
    // 输入: "  a b"
    cin >> noskipws;  // 不跳过空白字符
    cin >> c1 >> c2 >> c3;
    // c1 = ' ', c2 = 'a', c3 = ' '
    
    cin >> skipws;  // 恢复默认行为
    
    return 0;
}
```

### setbase和输入

`setbase`同样影响输入时的进制解释：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    int n;
    
    cin >> setbase(16) >> n;  // 输入: ff
    cout << n << endl;  // 输出: 255
    
    cin >> setbase(10) >> n;  // 输入: 100
    cout << n << endl;  // 输出: 100
    
    return 0;
}
```

## 综合示例与竞赛应用

### 竞赛输出格式处理

在ACM竞赛中，有时题目会要求特定的输出格式。以下是一些常见场景的处理方法：

```cpp
#include <iomanip>
#include <iostream>
#include <iomanip>
#include <vector>
using namespace std;

int main() {
    // 场景1：表格输出，对齐列
    vector<vector<string>> table = {
        {"Name", "Age", "Score"},
        {"Alice", "18", "95.5"},
        {"Bob", "19", "87.3"},
        {"Charlie", "20", "92.0"}
    };
    
    cout << left;
    cout << setw(10) << "Name" << setw(6) << "Age" << "Score" << endl;
    cout << string(22, '-') << endl;
    
    for (const auto& row : table) {
        cout << setw(10) << row[0] 
             << setw(6) << row[1] 
             << row[2] << endl;
    }
    
    // 场景2：浮点数输出，保留指定小数位
    double values[] = {3.14159, 2.71828, 1.41421, 1.73205};
    cout << fixed << setprecision(4);
    for (double v : values) {
        cout << v << endl;
    }
    
    // 场景3：数值对齐，右对齐，加前导零
    cout << right << setfill('0');
    for (int i = 1; i <= 5; i++) {
        cout << "Case " << setw(2) << i << ": " << i * i << endl;
    }
    cout << setfill(' ');  // 恢复默认填充字符
    
    return 0;
}
```

### 大数输出处理

```cpp
#include <iomanip>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    // 处理极大或极小的浮点数
    double large = 1.23e15;
    double small = 1.23e-15;
    
    cout << "默认格式:" << endl;
    cout << "large = " << large << endl;   // 可能显示为科学计数法
    cout << "small = " << small << endl;   // 可能显示为科学计数法
    
    cout << "\n固定格式，保留2位小数:" << endl;
    cout << fixed << setprecision(2);
    cout << "large = " << large << endl;   // 1230000000000000.00
    cout << "small = " << small << endl;   // 0.00（太小了）
    
    cout << "\n科学计数法格式:" << endl;
    cout << scientific << setprecision(3);
    cout << "large = " << large << endl;   // 1.230e+15
    cout << "small = " << small << endl;   // 1.230e-15
    
    return 0;
}
```

### 货币格式输出

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    double amount = 1234.5678;
    
    cout << "金额: " << fixed << setprecision(2) << amount << endl;
    
    // 添加货币符号（需要手动添加）
    cout << "¥" << fixed << setprecision(2) << amount << endl;
    cout << "$" << fixed << setprecision(2) << amount << endl;
    
    // 使用showpos显示正号
    cout << showpos << fixed << setprecision(2) << amount << endl;
    
    return 0;
}
```

### 进度条输出

```cpp
#include <iomanip>
#include <iostream>
#include <thread>
#include <chrono>
using namespace std;

int main() {
    const int total = 100;
    const int width = 50;
    
    cout << "下载进度: [";
    cout.flush();
    
    for (int i = 0; i <= total; i++) {
        // 回退光标，覆盖之前的输出
        cout << "\r下载进度: [";
        int pos = i * width / total;
        for (int j = 0; j < width; j++) {
            if (j < pos) cout << "=";
            else if (j == pos) cout << ">";
            else cout << " ";
        }
        cout << "] " << setw(3) << i << "%" << flush;
        
        // 模拟耗时操作
        this_thread::sleep_for(chrono::milliseconds(50));
    }
    
    cout << endl;
    
    return 0;
}
```

## 流状态与格式恢复

### 保存和恢复流状态

在复杂的输出场景中，可能需要临时改变格式设置，然后恢复原来的状态。可以使用`ios::fmtflags`来保存和恢复：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    // 保存当前格式状态
    ios::fmtflags old_flags = cout.flags();
    streamsize old_prec = cout.precision();
    char old_fill = cout.fill();
    
    // 临时改变格式
    cout << fixed << setprecision(4) << setfill('*');
    cout << "临时格式: " << 3.14159 << endl;
    
    // 恢复原来的格式
    cout.flags(old_flags);
    cout.precision(old_prec);
    cout.fill(old_fill);
    
    cout << "恢复后: " << 3.14159 << endl;
    
    return 0;
}
```

### 使用RAII模式管理流状态

可以创建一个辅助类来自动管理流状态的保存和恢复：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

class FormatGuard {
public:
    FormatGuard(ostream& os) : os_(os), flags_(os.flags()), 
                               precision_(os.precision()), 
                               fill_(os.fill()) {}
    
    ~FormatGuard() {
        os_.flags(flags_);
        os_.precision(precision_);
        os_.fill(fill_);
    }
    
    // 禁止拷贝
    FormatGuard(const FormatGuard&) = delete;
    FormatGuard& operator=(const FormatGuard&) = delete;
    
private:
    ostream& os_;
    ios::fmtflags flags_;
    streamsize precision_;
    char fill_;
};

int main() {
    {
        FormatGuard guard(cout);
        
        // 在这个作用域内修改格式
        cout << fixed << setprecision(4) << setfill('#');
        cout << "临时格式: " << 3.14159 << endl;
    }
    
    // guard被销毁，格式自动恢复
    cout << "恢复后: " << 3.14159 << endl;
    
    return 0;
}
```

## 注意事项与常见错误

### 精度陷阱

使用浮点数格式化时的一个常见陷阱是四舍五入行为：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    double x = 2.5;
    
    // 注意：C++的四舍五入遵循"银行家舍入法"（round to even）
    // 2.5 -> 2, 3.5 -> 4
    cout << fixed << setprecision(0) << x << endl;   // 输出: 2
    cout << fixed << setprecision(0) << 3.5 << endl; // 输出: 4
    
    // 使用half_up舍入（C++没有内置，需要自己实现）
    auto round_half_up = [](double v, int digits) {
        double factor = pow(10.0, digits);
        if (v >= 0) {
            return floor(v * factor + 0.5) / factor;
        } else {
            return -floor(-v * factor + 0.5) / factor;
        }
    };
    
    double y = 2.5;
    cout << fixed << setprecision(0) << round_half_up(y, 0) << endl;  // 输出: 3
    
    return 0;
}
```

### 宽度设置的单次有效问题

`setw`只影响下一次输出，这个特性有时会导致意外结果：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    // 错误示例：以为setw会持续有效
    cout << setw(10) << 1 << 2 << 3 << endl;
    // 输出: "        1" 然后是 "2" 然后是 "3"
    // 实际上只有第一个数字被设置了宽度
    
    // 正确做法：每次都设置
    cout << setw(10) << 1 << setw(10) << 2 << setw(10) << 3 << endl;
    // 输出: "        1        2        3"
    
    return 0;
}
```

### 进制标志的持续影响

进制设置会影响所有后续的整数输出，这可能导致问题：

```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    // 改变进制后忘记恢复
    cout << hex << 42 << endl;  // 输出: 2a
    
    cout << "索引: " << 1 << endl;  // 输出: 索引: 1（这里是十进制，因为是字符串连接）
    // 但如果直接输出数字：
    cout << 100 << endl;  // 输出: 64（十六进制）
    
    return 0;
}
```

### 缓冲区与flush

格式化输出可能会被缓冲，使用`endl`或`flush`可以强制刷新缓冲区：

```cpp
#include <iostream>
using namespace std;

int main() {
    cout << "正在计算...";
    // 计算过程...
    cout << endl;  // 刷新缓冲区并换行
    
    // 或者使用flush单独刷新
    cout << flush;
    
    return 0;
}
```

### 与printf的互操作性

虽然C++的iostream系统更加类型安全，但在某些场景下可能需要与printf混用：

```cpp
#include <cstdio>
#include <iostream>
using namespace std;

int main() {
    double pi = 3.14159;
    
    // 使用cout
    cout << fixed << setprecision(2) << pi << endl;
    
    // 使用printf（注意格式化字符串）
    printf("%.2f\n", pi);
    
    // 混用时可能需要同步
    ios::sync_with_stdio(false);  // 取消同步以提高性能
    // 但取消后不要与printf混用，可能导致输出顺序问题
    
    return 0;
}
```

## C++20新增功能

### std::format（C++20）

C++20引入了`std::format`，提供了一种更现代、更灵活的格式化方式：

```cpp
#include <format>  // C++20
#include <iostream>
using namespace std;

int main() {
    // 基本用法
    cout << format("The answer is {}", 42) << endl;
    
    // 指定位置
    cout << format("{0} + {1} = {2}", 1, 2, 3) << endl;
    
    // 命名参数
    cout << format("{name} is {age} years old", 
                   fmt::arg("name", "Alice"), 
                   fmt::arg("age", 25)) << endl;
    
    // 格式化数字
    cout << format("{:.2f}", 3.14159) << endl;  // 3.14
    cout << format("{:>10}", "hello") << endl;  // "     hello"
    cout << format("{:06d}", 42) << endl;       // "000042"
    
    return 0;
}
```

虽然`std::format`非常强大，但目前大多数在线评测系统可能还不支持C++20，所以在竞赛代码中暂时应该使用`iomanip`的格式化功能。

## 总结与建议

`<iomanip>`头文件提供了强大的输出格式化功能，在ACM竞赛中主要用于：

**浮点数精度控制**：使用`fixed`或`scientific`配合`setprecision`控制输出格式。记住`setprecision`控制的是有效数字而非小数位数。

**进制转换**：使用`dec`、`oct`、`hex`或`setbase`进行进制转换。注意进制设置会持续有效。

**对齐与填充**：使用`setw`、`setfill`、`left`、`right`、`internal`控制输出对齐方式和填充字符。

**特殊格式**：使用`showpoint`、`showpos`、`boolalpha`等操纵符控制特殊格式的输出。

**输入控制**：使用`ws`、`skipws`等操纵符控制输入行为。

在实际编程中，建议：

1. 在处理浮点数输出时，始终明确指定格式（fixed或scientific）和精度。
2. 使用`setw`时记得每次都需要调用。
3. 注意进制设置的持续影响，必要时保存和恢复格式状态。
4. 在需要精确控制输出格式的场景（如表格、对齐输出），充分利用`iomanip`提供的各种功能。