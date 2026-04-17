# cstdio - C 风格输入输出

## 基本用法

```cpp
#include <cstdio>
using namespace std;
```

## 格式化输入

### scanf 格式控制符
```cpp
int a;           scanf("%d", &a);
long long b;     scanf("%lld", &b);
float c;         scanf("%f", &c);
double d;        scanf("%lf", &d);
char e;          scanf("%c", &e);
char s[100];     scanf("%s", s);
char *p;         scanf("%p", &p);
```

### 格式修饰符
```cpp
%5d       // 最小宽度 5
%05d      // 宽度 5，前导零填充
%-5d      // 左对齐，宽度 5
%.2f      // 保留 2 位小数
%10.2f    // 总宽 10，保留 2 位小数
%lx       // 十六进制 long
%llx      // 十六进制 long long
```

### 特殊输入
```cpp
scanf("%d%*d%d", &a, &b);  // 跳过中间的整数
scanf("%d%n", &a, &n);     // n 记录已读字符数
scanf("%[0-9]", s);        // 只读数字
scanf("%[^0-9]", s);       // 读非数字
scanf(" %c", &c);          // 跳过空白读字符
```

## 格式化输出

### printf 格式控制符
```cpp
printf("%d", a);        // 整数
printf("%lld", b);      // long long
printf("%f", c);        // float
printf("%.2f", d);      // 保留 2 位小数
printf("%e", d);        // 科学计数法
printf("%c", e);        // 字符
printf("%s", s);        // 字符串
printf("%p", p);        // 指针
printf("%x", a);        // 十六进制
printf("%o", a);        // 八进制
printf("%u", a);        // 无符号整数
```

### 输出修饰
```cpp
printf("%05d", 123);      // 00123
printf("%5d", 123);       // "  123"
printf("%-5d", 123);      // "123  "
printf("%10.2f", 3.14159);// "      3.14"
printf("%*d", 5, 123);    // 动态宽度
```

## 文件操作

### FILE 指针
```cpp
FILE *fin = fopen("input.txt", "r");
FILE *fout = fopen("output.txt", "w");
FILE *fapp = fopen("log.txt", "a");  // 追加模式

fscanf(fin, "%d", &a);
fprintf(fout, "%d\n", a);

fclose(fin);
fclose(fout);
```

### 文件重定向
```cpp
freopen("input.txt", "r", stdin);
freopen("output.txt", "w", stdout);
freopen("error.txt", "w", stderr);

// 程序结束后恢复（可选）
// fclose(stdin);
// fclose(stdout);
```

## 字符操作

### getchar / putchar
```cpp
char c = getchar();    // 读单个字符
putchar(c);            // 写单个字符

// 读整行（包括空格）
char s[1000];
int i = 0;
while ((c = getchar()) != '\n' && c != EOF) {
    s[i++] = c;
}
s[i] = '\0';
```

### gets / puts (已废弃，不推荐)
```cpp
// 使用 fgets 替代 gets
char s[1000];
fgets(s, sizeof(s), stdin);  // 安全版本
puts(s);                     // 输出字符串并换行
```

## 竞赛技巧

### 快速读入模板
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
```

### 快速输出模板
```cpp
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

### 输出优化（缓冲区）
```cpp
const int BUF_SIZE = 1 << 16;
char buf[BUF_SIZE], *p1 = buf, *p2 = buf;

#define getchar() (p1 == p2 && (p2 = (p1 = buf) + fread(buf, 1, BUF_SIZE, stdin), p1 == p2) ? EOF : *p1++)

// 使用时直接调用 getchar()
```

## 常用技巧

### 读入字符串直到特定字符
```cpp
char s[1000];
int len = 0;
char c;
while ((c = getchar()) != EOF && c != '\n') {
    s[len++] = c;
}
s[len] = '\0';
```

### 读入直到文件结束
```cpp
int a, b;
while (scanf("%d%d", &a, &b) != EOF) {
    // 处理
}

// 或者
while (~scanf("%d", &a)) {
    // 处理
}
```

## 注意事项

1. **安全性**: 避免使用 `gets()`，改用 `fgets()`
2. **格式匹配**: `scanf` 格式字符串必须与变量类型匹配
3. **取地址**: `scanf` 需要变量地址，`printf` 不需要
4. **缓冲区**: `scanf` 后可能需要清空缓冲区
5. **性能**: 大量数据时 `scanf/printf` 通常比 `cin/cout` 快（未加速时）
