# 快速输入输出

## 基础加速

### cin/cout 加速
```cpp
// 竞赛必备
ios::sync_with_stdio(false);
cin.tie(nullptr);
cout.tie(nullptr);  // 可选

// 放在 main 函数开头
int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    // 你的代码
    return 0;
}
```

### 注意事项
- 加速后不要混用 `scanf/printf` 和 `cin/cout`
- `cout.tie(nullptr)` 通常不需要
- `endl` 会刷新缓冲区，大量输出用 `\n`

## 快速读入模板

### 整数快速读入
```cpp
inline int read() {
    int x = 0, f = 1;
    char c = getchar();
    while (c < '0' || c > '9') {
        if (c == '-') f = -1;
        c = getchar();
    }
    while (c >= '0' && c <= '9') {
        x = x * 10 + c - '0';
        c = getchar();
    }
    return x * f;
}

inline void write(int x) {
    if (x < 0) {
        putchar('-');
        x = -x;
    }
    if (x > 9) write(x / 10);
    putchar(x % 10 + '0');
}

inline void writeln(int x) {
    write(x);
    putchar('\n');
}
```

### 长整数快速读入
```cpp
typedef long long ll;

inline ll readll() {
    ll x = 0, f = 1;
    char c = getchar();
    while (c < '0' || c > '9') {
        if (c == '-') f = -1;
        c = getchar();
    }
    while (c >= '0' && c <= '9') {
        x = x * 10 + c - '0';
        c = getchar();
    }
    return x * f;
}

inline void writell(ll x) {
    if (x < 0) {
        putchar('-');
        x = -x;
    }
    if (x > 9) writell(x / 10);
    putchar(x % 10 + '0');
}
```

### 浮点数快速读入
```cpp
inline double readd() {
    double x = 0, f = 1;
    char c = getchar();
    while (c < '0' || c > '9') {
        if (c == '-') f = -1;
        c = getchar();
    }
    while (c >= '0' && c <= '9') {
        x = x * 10 + c - '0';
        c = getchar();
    }
    if (c == '.') {
        double t = 0.1;
        c = getchar();
        while (c >= '0' && c <= '9') {
            x += (c - '0') * t;
            t *= 0.1;
            c = getchar();
        }
    }
    return x * f;
}
```

### 字符串快速读入
```cpp
inline void reads(char* s) {
    char c = getchar();
    while (c < '!' || c > '~') c = getchar();
    while (c >= '!' && c <= '~') {
        *s++ = c;
        c = getchar();
    }
    *s = '\0';
}
```

## 缓冲区优化

### fread 优化
```cpp
const int BUF_SIZE = 1 << 18;
char buf[BUF_SIZE], *p1 = buf, *p2 = buf;

inline char getchar() {
    return p1 == p2 && (p2 = (p1 = buf) + fread(buf, 1, BUF_SIZE, stdin), 
                        p1 == p2) ? EOF : *p1++;
}

// 使用上面的 read() 函数
```

### fwrite 优化
```cpp
const int BUF_SIZE = 1 << 18;
char buf[BUF_SIZE], *p = buf;

inline void putchar(char c) {
    if (p == buf + BUF_SIZE) {
        fwrite(buf, 1, BUF_SIZE, stdout);
        p = buf;
    }
    *p++ = c;
}

inline void flush() {
    if (p != buf) {
        fwrite(buf, 1, p - buf, stdout);
        p = buf;
    }
}

// 程序结束时调用
// flush();
```

## 完整模板

### 模板 1：基础版
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int t;
    cin >> t;
    while (t--) {
        // 处理每个测试用例
    }
    
    return 0;
}
```

### 模板 2：快速 IO 版
```cpp
#include <bits/stdc++.h>
using namespace std;

inline int read() {
    int x = 0, f = 1;
    char c = getchar();
    while (c < '0' || c > '9') {
        if (c == '-') f = -1;
        c = getchar();
    }
    while (c >= '0' && c <= '9') {
        x = x * 10 + c - '0';
        c = getchar();
    }
    return x * f;
}

inline void write(int x) {
    if (x < 0) {
        putchar('-');
        x = -x;
    }
    if (x > 9) write(x / 10);
    putchar(x % 10 + '0');
}

inline void writeln(int x) {
    write(x);
    putchar('\n');
}

int main() {
    int t = read();
    while (t--) {
        // 处理每个测试用例
    }
    return 0;
}
```

### 模板 3：fread 优化版
```cpp
#include <bits/stdc++.h>
using namespace std;

const int BUF_SIZE = 1 << 18;
char buf[BUF_SIZE], *p1 = buf, *p2 = buf;

inline char gc() {
    return p1 == p2 && (p2 = (p1 = buf) + fread(buf, 1, BUF_SIZE, stdin), 
                        p1 == p2) ? EOF : *p1++;
}

inline int read() {
    int x = 0, f = 1;
    char c = gc();
    while (c < '0' || c > '9') {
        if (c == '-') f = -1;
        c = gc();
    }
    while (c >= '0' && c <= '9') {
        x = x * 10 + c - '0';
        c = gc();
    }
    return x * f;
}

char obuf[BUF_SIZE], *op = obuf;

inline void pc(char c) {
    if (op == obuf + BUF_SIZE) {
        fwrite(obuf, 1, BUF_SIZE, stdout);
        op = obuf;
    }
    *op++ = c;
}

inline void write(int x) {
    if (x < 0) {
        pc('-');
        x = -x;
    }
    if (x > 9) write(x / 10);
    pc(x % 10 + '0');
}

inline void writeln(int x) {
    write(x);
    pc('\n');
}

inline void flush() {
    if (op != obuf) {
        fwrite(obuf, 1, op - obuf, stdout);
        op = obuf;
    }
}

int main() {
    int t = read();
    while (t--) {
        // 处理
    }
    flush();
    return 0;
}
```

## 使用场景

### 何时使用快速 IO
1. **输入输出量大**: 超过 10^5 个整数
2. **时间限制严格**: 1 秒以内的大数据
3. **多测试用例**: T 很大的情况

### 何时不需要
1. **数据量小**: 少于 10^4 个整数
2. **时间充裕**: 2 秒以上
3. **交互题**: 需要即时刷新输出

## 注意事项

1. **混用问题**: 加速后不要混用不同 IO 方式
2. **刷新缓冲区**: 交互题需要及时刷新
3. **调试**: 调试时可暂时关闭快速 IO
4. **文件操作**: 本地测试可用文件重定向
