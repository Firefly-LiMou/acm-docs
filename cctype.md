# cctype 头文件详解

`<cctype>`头文件是C++标准库中用于字符分类和转换的头文件，提供了判断字符类型和进行字符大小写转换的函数。这些函数在ACM竞赛中非常常用，特别是在处理字符串输入、字符分类、文本处理等场景中。

## 1. cctype头文件概述

`<cctype>`（以及其C语言版本`<ctype.h>`）提供了一系列用于字符分类和转换的函数。这些函数接受一个`int`参数（通常是`char`或`unsigned char`），返回一个整数结果（真或假）或转换后的字符。

**包含的函数类型**：
- 字符分类函数：判断字符属于哪一类
- 字符转换函数：转换字符的大小写

**重要提示**：C++标准要求这些函数接受`int`参数并返回`int`。使用时应该将`char`类型显式转换为`unsigned char`（对于C++11及更高版本），以避免未定义行为。

## 2. 字符分类函数

这些函数用于判断字符属于哪一类，返回非零表示真，返回零表示假。

### 2.1 字母数字判断

```cpp
#include <cctype>
#include <iostream>
using namespace std;

int main() {
    char c1 = 'A';
    char c2 = '5';
    char c3 = '!';
    
    // isalnum - 是否为字母或数字
    cout << "isalnum('A'): " << isalnum(c1) << endl;  // 真（非零）
    cout << "isalnum('5'): " << isalnum(c2) << endl;  // 真
    cout << "isalnum('!'): " << isalnum(c3) << endl;  // 假（0）
    
    return 0;
}
```

### 2.2 字母判断

```cpp
#include <cctype>
#include <iostream>
using namespace std;

int main() {
    // isalpha - 是否为字母（a-z, A-Z）
    cout << "isalpha('A'): " << isalpha('A') << endl;  // 真
    cout << "isalpha('z'): " << isalpha('z') << endl;  // 真
    cout << "isalpha('5'): " << isalpha('5') << endl;  // 假
    
    // isupper - 是否为大写字母
    cout << "isupper('A'): " << isupper('A') << endl;  // 真
    cout << "isupper('a'): " << isupper('a') << endl;  // 假
    
    // islower - 是否为小写字母
    cout << "islower('a'): " << islower('a') << endl;  // 真
    cout << "islower('A'): " << islower('A') << endl;  // 假
    
    return 0;
}
```

### 2.3 数字判断

```cpp
#include <cctype>
#include <iostream>
using namespace std;

int main() {
    // isdigit - 是否为十进制数字（0-9）
    cout << "isdigit('5'): " << isdigit('5') << endl;  // 真
    cout << "isdigit('A'): " << isdigit('A') << endl;  // 假
    
    // isxdigit - 是否为十六进制数字（0-9, a-f, A-F）
    cout << "isxdigit('F'): " << isxdigit('F') << endl;  // 真
    cout << "isxdigit('G'): " << isxdigit('G') << endl;  // 假
    cout << "isxdigit('a'): " << isxdigit('a') << endl;  // 真
    
    // iscntrl - 是否为控制字符
    cout << "iscntrl('\\n'): " << iscntrl('\n') << endl;  // 真
    cout << "iscntrl('A'): " << iscntrl('A') << endl;      // 假
    
    return 0;
}
```

### 2.4 空白字符判断

```cpp
#include <cctype>
#include <iostream>
using namespace std;

int main() {
    // isspace - 是否为空白字符
    // 包括: 空格 ' ', 制表符 '\t', 换行符 '\n', 
    //       回车符 '\r', 换页符 '\f', 垂直制表符 '\v'
    cout << "isspace(' '): " << isspace(' ') << endl;   // 真
    cout << "isspace('\\t'): " << isspace('\t') << endl; // 真
    cout << "isspace('\\n'): " << isspace('\n') << endl; // 真
    cout << "isspace('A'): " << isspace('A') << endl;    // 假
    
    // isblank - 是否为空白字符（C++11）
    // 仅包括: 空格 ' ' 和制表符 '\t'
    cout << "isblank(' '): " << isblank(' ') << endl;    // 真
    cout << "isblank('\\t'): " << isblank('\t') << endl; // 真
    cout << "isblank('\\n'): " << isblank('\n') << endl; // 假
    
    return 0;
}
```

### 2.5 可打印字符判断

```cpp
#include <cctype>
#include <iostream>
using namespace std;

int main() {
    // isprint - 是否为可打印字符（包括空格）
    // 范围: 0x20 - 0x7E (32-126)
    cout << "isprint('A'): " << isprint('A') << endl;  // 真
    cout << "isprint(' '): " << isprint(' ') << endl;  // 真
    cout << "isprint('\\n'): " << isprint('\n') << endl; // 假
    
    // isgraph - 是否为可打印的非空白字符
    cout << "isgraph('A'): " << isgraph('A') << endl;  // 真
    cout << "isgraph(' '): " << isgraph(' ') << endl;  // 假
    cout << "isgraph('!'): " << isgraph('!') << endl;  // 真
    
    // ispunct - 是否为标点符号（可打印但非字母数字）
    cout << "ispunct('!'): " << ispunct('!') << endl;  // 真
    cout << "ispunct('A'): " << ispunct('A') << endl;  // 假
    cout << "ispunct('5'): " << ispunct('5') << endl;  // 假
    
    return 0;
}
```

## 3. 字符转换函数

### 3.1 大小写转换

```cpp
#include <cctype>
#include <iostream>
using namespace std;

int main() {
    // tolower - 转换为小写
    cout << "tolower('A'): " << (char)tolower('A') << endl;  // 'a'
    cout << "tolower('a'): " << (char)tolower('a') << endl;  // 'a'
    cout << "tolower('5'): " << (char)tolower('5') << endl;  // '5'（不变）
    
    // toupper - 转换为大写
    cout << "toupper('a'): " << (char)toupper('a') << endl;  // 'A'
    cout << "toupper('A'): " << (char)toupper('A') << endl;  // 'A'
    cout << "toupper('5'): " << (char)toupper('5') << endl;  // '5'（不变）
    
    return 0;
}
```

### 3.2 转换的注意事项

```cpp
#include <cctype>
#include <iostream>
using namespace std;

int main() {
    // 重要：使用unsigned char避免未定义行为
    // char可能是带符号的，传递负值给cctype函数可能导致未定义行为
    
    char c = -1;  // 这可能是无效值
    
    // 错误做法：可能未定义
    // int result = isalpha(c);
    
    // 正确做法：显式转换为unsigned char
    int result = isalpha(static_cast<unsigned char>(c));
    
    // C++11更好的做法：使用新函数
    // C++11引入了接受char参数的模板重载，更安全
    // 但为了兼容性，建议仍使用显式转换
    
    return 0;
}
```

## 4. 竞赛应用场景

### 4.1 字符串验证

```cpp
#include <cctype>
#include <string>
#include <iostream>
using namespace std;

// 检查字符串是否只包含字母
bool isAlpha(const string& s) {
    for (char c : s) {
        if (!isalpha(static_cast<unsigned char>(c))) {
            return false;
        }
    }
    return !s.empty();
}

// 检查字符串是否只包含数字
bool isDigit(const string& s) {
    for (char c : s) {
        if (!isdigit(static_cast<unsigned char>(c))) {
            return false;
        }
    }
    return !s.empty();
}

// 检查字符串是否只包含字母和数字
bool isAlnum(const string& s) {
    for (char c : s) {
        if (!isalnum(static_cast<unsigned char>(c))) {
            return false;
        }
    }
    return !s.empty();
}

// 检查字符串是否为整数（可以带可选的正负号）
bool isInteger(const string& s) {
    if (s.empty()) return false;
    size_t start = 0;
    if (s[0] == '+' || s[0] == '-') {
        if (s.length() == 1) return false;
        start = 1;
    }
    for (size_t i = start; i < s.length(); ++i) {
        if (!isdigit(static_cast<unsigned char>(s[i]))) {
            return false;
        }
    }
    return true;
}

// 检查字符串是否为浮点数
bool isFloat(const string& s) {
    if (s.empty()) return false;
    size_t start = 0;
    if (s[0] == '+' || s[0] == '-') {
        if (s.length() == 1) return false;
        start = 1;
    }
    bool hasDot = false;
    bool hasDigit = false;
    for (size_t i = start; i < s.length(); ++i) {
        if (s[i] == '.') {
            if (hasDot) return false;
            hasDot = true;
        } else if (isdigit(static_cast<unsigned char>(s[i]))) {
            hasDigit = true;
        } else {
            return false;
        }
    }
    return hasDigit;
}

int main() {
    cout << "isAlpha(\"Hello\"): " << isAlpha("Hello") << endl;    // 1
    cout << "isAlpha(\"Hello123\"): " << isAlpha("Hello123") << endl; // 0
    cout << "isDigit(\"12345\"): " << isDigit("12345") << endl;    // 1
    cout << "isInteger(\"-123\"): " << isInteger("-123") << endl;  // 1
    cout << "isFloat(\"3.14\"): " << isFloat("3.14") << endl;      // 1
    
    return 0;
}
```

### 4.2 字符串转换

```cpp
#include <cctype>
#include <string>
#include <iostream>
using namespace std;

// 字符串转大写
string toUpper(const string& s) {
    string result = s;
    for (char& c : result) {
        c = static_cast<char>(toupper(static_cast<unsigned char>(c)));
    }
    return result;
}

// 字符串转小写
string toLower(const string& s) {
    string result = s;
    for (char& c : result) {
        c = static_cast<char>(tolower(static_cast<unsigned char>(c)));
    }
    return result;
}

// 标题格式（每个单词首字母大写）
string toTitle(const string& s) {
    string result = s;
    bool newWord = true;
    for (char& c : result) {
        if (isspace(static_cast<unsigned char>(c))) {
            newWord = true;
        } else if (newWord) {
            c = static_cast<char>(toupper(static_cast<unsigned char>(c)));
            newWord = false;
        }
    }
    return result;
}

// 去除字符串中的所有非字母数字字符
string removeNonAlnum(const string& s) {
    string result;
    for (char c : s) {
        if (isalnum(static_cast<unsigned char>(c))) {
            result += c;
        }
    }
    return result;
}

// 只保留字母
string keepAlpha(const string& s) {
    string result;
    for (char c : s) {
        if (isalpha(static_cast<unsigned char>(c))) {
            result += c;
        }
    }
    return result;
}

// 只保留数字
string keepDigit(const string& s) {
    string result;
    for (char c : s) {
        if (isdigit(static_cast<unsigned char>(c))) {
            result += c;
        }
    }
    return result;
}

int main() {
    cout << toUpper("Hello World") << endl;    // HELLO WORLD
    cout << toLower("Hello World") << endl;    // hello world
    cout << toTitle("hello world") << endl;    // Hello World
    cout << keepDigit("abc123def456") << endl; // 123456
    
    return 0;
}
```

### 4.3 单词统计

```cpp
#include <cctype>
#include <string>
#include <vector>
using namespace std;

// 统计字符串中的单词数
int countWords(const string& s) {
    int count = 0;
    bool inWord = false;
    for (char c : s) {
        if (isalpha(static_cast<unsigned char>(c))) {
            if (!inWord) {
                ++count;
                inWord = true;
            }
        } else {
            inWord = false;
        }
    }
    return count;
}

// 统计各类字符的数量
struct CharCount {
    int letters = 0;
    int digits = 0;
    int spaces = 0;
    int punct = 0;
    int other = 0;
};

CharCount analyzeString(const string& s) {
    CharCount result;
    for (char c : s) {
        unsigned char uc = static_cast<unsigned char>(c);
        if (isalpha(uc)) ++result.letters;
        else if (isdigit(uc)) ++result.digits;
        else if (isspace(uc)) ++result.spaces;
        else if (ispunct(uc)) ++result.punct;
        else ++result.other;
    }
    return result;
}

// 判断字符是否为单词分隔符
bool isWordSeparator(char c) {
    return isspace(static_cast<unsigned char>(c)) || 
           ispunct(static_cast<unsigned char>(c));
}

// 分割字符串为单词
vector<string> splitWords(const string& s) {
    vector<string> words;
    string current;
    for (char c : s) {
        if (isWordSeparator(c)) {
            if (!current.empty()) {
                words.push_back(current);
                current.clear();
            }
        } else {
            current += c;
        }
    }
    if (!current.empty()) {
        words.push_back(current);
    }
    return words;
}

int main() {
    string text = "Hello, world! This is a test.";
    cout << "Word count: " << countWords(text) << endl;
    
    auto counts = analyzeString(text);
    cout << "Letters: " << counts.letters << endl;
    cout << "Digits: " << counts.digits << endl;
    cout << "Spaces: " << counts.spaces << endl;
    cout << "Punct: " << counts.punct << endl;
    
    auto words = splitWords(text);
    cout << "Words: ";
    for (const auto& w : words) {
        cout << w << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 4.4 字符串解析

```cpp
#include <cctype>
#include <string>
#include <vector>
#include <iostream>
using namespace std;

// 提取字符串中的所有整数
vector<int> extractIntegers(const string& s) {
    vector<int> result;
    string current;
    for (size_t i = 0; i < s.length(); ++i) {
        char c = s[i];
        if (isdigit(static_cast<unsigned char>(c))) {
            // 处理可能的负数
            if (c == '-' && i + 1 < s.length() && 
                isdigit(static_cast<unsigned char>(s[i + 1]))) {
                current += c;
            } else if (!current.empty() || isdigit(static_cast<unsigned char>(c))) {
                current += c;
            }
        } else if (!current.empty()) {
            result.push_back(stoi(current));
            current.clear();
        }
    }
    if (!current.empty()) {
        result.push_back(stoi(current));
    }
    return result;
}

// 判断字符是否为十六进制字符
bool isHexChar(char c) {
    return isxdigit(static_cast<unsigned char>(c));
}

// 十六进制字符转数值
int hexCharToInt(char c) {
    if (c >= '0' && c <= '9') return c - '0';
    if (c >= 'a' && c <= 'f') return c - 'a' + 10;
    if (c >= 'A' && c <= 'F') return c - 'A' + 10;
    return 0;
}

// 判断字符是否为八进制字符
bool isOctalChar(char c) {
    return c >= '0' && c <= '7';
}

// 判断字符是否为二进制字符
bool isBinaryChar(char c) {
    return c == '0' || c == '1';
}

// 简化空白字符（多个空格合并为一个）
string normalizeSpaces(const string& s) {
    string result;
    bool prevSpace = false;
    for (char c : s) {
        bool curSpace = isspace(static_cast<unsigned char>(c));
        if (!curSpace) {
            result += c;
            prevSpace = false;
        } else if (!prevSpace) {
            result += ' ';
            prevSpace = true;
        }
    }
    // 去除首尾空格
    size_t start = result.find_first_not_of(' ');
    size_t end = result.find_last_not_of(' ');
    if (start == string::npos) return "";
    return result.substr(start, end - start + 1);
}

int main() {
    string s = "abc123def456ghi";
    auto nums = extractIntegers(s);
    cout << "Integers: ";
    for (int n : nums) cout << n << " ";
    cout << endl;
    
    cout << "isHexChar('F'): " << isHexChar('F') << endl;
    cout << "hexCharToInt('F'): " << hexCharToInt('F') << endl;
    
    return 0;
}
```

### 4.5 回文和相似问题

```cpp
#include <cctype>
#include <string>
#include <iostream>
using namespace std;

// 判断是否为回文（忽略大小写和非字母数字）
bool isPalindrome(const string& s) {
    size_t left = 0;
    size_t right = s.length();
    while (left < right) {
        // 跳过非字母数字
        while (left < right && !isalnum(static_cast<unsigned char>(s[left]))) {
            ++left;
        }
        while (left < right && !isalnum(static_cast<unsigned char>(s[right - 1]))) {
            --right;
        }
        if (left >= right) break;
        // 比较（忽略大小写）
        if (tolower(static_cast<unsigned char>(s[left])) != 
            tolower(static_cast<unsigned char>(s[right - 1]))) {
            return false;
        }
        ++left;
        --right;
    }
    return true;
}

// 找到第一个单词的起始位置
size_t findFirstWordStart(const string& s) {
    for (size_t i = 0; i < s.length(); ++i) {
        if (isalpha(static_cast<unsigned char>(s[i]))) {
            return i;
        }
    }
    return string::npos;
}

// 找到最后一个单词的结束位置
size_t findLastWordEnd(const string& s) {
    for (size_t i = s.length(); i > 0; --i) {
        if (isalpha(static_cast<unsigned char>(s[i - 1]))) {
            return i;
        }
    }
    return string::npos;
}

// 提取第一个单词
string getFirstWord(const string& s) {
    size_t start = findFirstWordStart(s);
    if (start == string::npos) return "";
    size_t end = start;
    while (end < s.length() && 
           isalpha(static_cast<unsigned char>(s[end]))) {
        ++end;
    }
    return s.substr(start, end - start);
}

// 提取最后一个单词
string getLastWord(const string& s) {
    size_t end = findLastWordEnd(s);
    if (end == 0) return "";
    size_t start = end;
    while (start > 0 && 
           isalpha(static_cast<unsigned char>(s[start - 1]))) {
        --start;
    }
    return s.substr(start, end - start);
}

int main() {
    cout << "isPalindrome(\"A man, a plan, a canal: Panama\"): " 
         << isPalindrome("A man, a plan, a canal: Panama") << endl;  // 1
    cout << "isPalindrome(\"not a palindrome\"): " 
         << isPalindrome("not a palindrome") << endl;  // 0
    
    string text = "  Hello World  ";
    cout << "First word: " << getFirstWord(text) << endl;  // Hello
    cout << "Last word: " << getLastWord(text) << endl;    // World
    
    return 0;
}
```

### 4.6 字符编码转换

```cpp
#include <cctype>
#include <string>
#include <iostream>
using namespace std;

// 判断字符是否为可打印ASCII（32-126）
bool isPrintableAscii(char c) {
    return isprint(static_cast<unsigned char>(c));
}

// 判断字符是否为小写英文字母
bool isLowerEnglish(char c) {
    return c >= 'a' && c <= 'z';
}

// 判断字符是否为大写英文字母
bool isUpperEnglish(char c) {
    return c >= 'A' && c <= 'Z';
}

// 判断字符是否为英文字母
bool isEnglishLetter(char c) {
    return isLowerEnglish(c) || isUpperEnglish(c);
}

// 判断字符是否为数字字符
bool isDigitChar(char c) {
    return c >= '0' && c <= '9';
}

// 将数字字符转换为整数值
int digitToInt(char c) {
    if (isDigitChar(c)) {
        return c - '0';
    }
    return 0;
}

// 判断字符是否为字母或下划线（用于标识符）
bool isIdentifierChar(char c, bool firstChar = false) {
    return isEnglishLetter(c) || c == '_' || 
           (!firstChar && isDigitChar(c));
}

int main() {
    cout << "isPrintableAscii('A'): " << isPrintableAscii('A') << endl;
    cout << "isIdentifierChar('_'): " << isIdentifierChar('_', true) << endl;
    cout << "digitToInt('7'): " << digitToInt('7') << endl;
    
    return 0;
}
```

## 5. 注意事项

### 5.1 类型转换

```cpp
#include <cctype>
#include <iostream>
using namespace std;

// 重要：始终将char转换为unsigned char
void correctUsage() {
    char c = 'A';
    
    // 正确
    int result = isalpha(static_cast<unsigned char>(c));
    
    // 错误：可能导致未定义行为
    // int result2 = isalpha(c);  // 如果char是有符号的，可能传递负值
}

// C++11可以使用新的安全版本
void cpp11Usage() {
    char c = 'A';
    // C++11起，某些实现提供了更安全的重载
    // 但为兼容性，建议仍然显式转换
}
```

### 5.2 locale设置

```cpp
#include <cctype>
#include <locale>
using namespace std;

// cctype函数的行为可能受locale影响
// 默认情况下使用"C"locale，即ASCII字符集
void localeExample() {
    locale loc = locale::classic();  // C locale
    // 可以设置其他locale来支持其他字符集
    // 但在竞赛中，通常只使用ASCII字符
}
```

### 5.3 与字符串函数配合

```cpp
#include <cctype>
#include <algorithm>
#include <string>
using namespace std;

// 使用remove_if和cctype函数
string removeSpaces(const string& s) {
    string result;
    copy_if(s.begin(), s.end(), back_inserter(result),
        [](char c) { 
            return !isspace(static_cast<unsigned char>(c)); 
        });
    return result;
}

// 使用transform和cctype函数
string toUpperTransform(const string& s) {
    string result = s;
    transform(result.begin(), result.end(), result.begin(),
        [](unsigned char c) { return toupper(c); });
    return result;
}
```

## 6. 复杂度汇总

| 函数 | 时间复杂度 | 说明 |
|------|------------|------|
| 所有分类函数 | O(1) | 常数时间 |
| 所有转换函数 | O(1) | 常数时间 |

所有`<cctype>`中的函数都是常数时间操作，没有循环或复杂计算。

## 7. 总结

`<cctype>`头文件提供了高效的字符分类和转换功能：

1. **字符分类**：`isalpha`、`isdigit`、`isalnum`等用于判断字符类型
2. **大小写转换**：`tolower`、`toupper`用于字符大小写转换
3. **空白处理**：`isspace`、`isblank`用于空白字符判断
4. **实际应用**：字符串验证、转换、解析、单词统计、回文判断等

在竞赛中，这些函数常用于：
- 输入字符串的合法性验证
- 字符串大小写转换
- 文本解析和处理
- 字符统计和分析

熟练掌握这些函数可以更简洁、更高效地处理字符串相关问题。
