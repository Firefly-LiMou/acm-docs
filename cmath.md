# C++数学函数完全指南

## cmath头文件概述

`<cmath>`头文件是C++标准库中最重要的数学函数头文件之一，包含了大量用于数学计算的函数。在ACM竞赛中，这个头文件被广泛使用，涵盖了三角函数、指数对数函数、幂函数、舍入函数、双曲函数、误差函数等多个类别。熟练掌握这些函数对于解决涉及数值计算的题目至关重要。

本章节将详细介绍竞赛中常用的数学函数，包括它们的函数原型、使用方法、适用场景以及注意事项。所有内容基于C++11及更高标准，确保在现代竞赛环境中能够正常使用。

### 数学函数分类概览

`<cmath>`头文件中的函数可以分为以下几个主要类别：

**基本三角函数**：包括`sincostan`及其反函数`asinacosatan`，用于计算角度的正弦、余弦和正切值及其反函数。这些函数接受弧度作为参数，返回值也是弧度。

**双曲函数**：包括`sinhcoshtanh`及其反函数`asinhacoshatanh`，用于计算双曲正弦、余弦和正切值。这些函数在某些特定数学问题中有应用。

**指数与对数函数**：包括`expexp2loglog10log2`等函数，用于计算指数和自然对数、以10为底的对数等。这些函数在处理大规模数据或概率计算时非常有用。

**幂函数与开方**：包括`powsqrtcbrt`等函数，用于计算幂次方、平方根和立方根。

**舍入与取整函数**：包括`floorceilroundtrunc`等函数，用于对浮点数进行各种方式的舍入处理。

**绝对值函数**：包括`fabsfabsf`等函数，用于计算浮点数的绝对值。

**误差函数**：包括`erferrfc`等函数，用于正态分布相关的计算。

## 三角函数详解

### 基本三角函数（sin, cos, tan）

三角函数是竞赛中常用的数学函数，主要用于处理角度计算、几何问题和物理模拟。函数原型如下：

```cpp
#include <cmath>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double angle_rad = M_PI / 4;  // 45度转为弧度
    
    // sin函数：计算正弦值
    double sin_val = sin(angle_rad);
    cout << "sin(π/4) = " << fixed << setprecision(6) << sin_val << endl;
    // 输出: sin(π/4) = 0.707107
    
    // cos函数：计算余弦值
    double cos_val = cos(angle_rad);
    cout << "cos(π/4) = " << cos_val << endl;
    // 输出: cos(π/4) = 0.707107
    
    // tan函数：计算正切值
    double tan_val = tan(angle_rad);
    cout << "tan(π/4) = " << tan_val << endl;
    // 输出: tan(π/4) = 1.000000
    
    // 验证恒等式
    cout << "sin² + cos² = " << sin_val * sin_val + cos_val * cos_val << endl;
    // 输出: sin² + cos² = 1.000000
    
    return 0;
}
```

**重要说明**：C++的三角函数接受**弧度**作为参数，而不是角度。如果题目给出的是角度，需要先转换为弧度：

```cpp
#include <cmath>
#include <iostream>
using namespace std;

// 角度转弧度
double deg2rad(double degrees) {
    return degrees * M_PI / 180.0;
}

// 弧度转角度
double rad2deg(double radians) {
    return radians * 180.0 / M_PI;
}

int main() {
    double angle_deg = 30;  // 30度
    
    // 将角度转换为弧度
    double angle_rad = deg2rad(angle_deg);
    cout << angle_deg << "度 = " << angle_rad << "弧度" << endl;
    
    // 计算正弦值
    double sin_val = sin(angle_rad);
    cout << "sin(" << angle_deg << "°) = " << sin_val << endl;
    // 输出: sin(30°) = 0.5
    
    return 0;
}
```

### 反三角函数（asin, acos, atan）

反三角函数用于根据三角函数值计算角度，返回值是弧度：

```cpp
#include <cmath>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    // asin函数：反正弦，返回[-π/2, π/2]范围内的弧度
    double sin_val = 0.5;
    double asin_result = asin(sin_val);
    cout << "asin(0.5) = " << fixed << setprecision(6) << asin_result 
         << " 弧度" << endl;
    cout << "转换为角度: " << rad2deg(asin_result) << " 度" << endl;
    // 输出: asin(0.5) = 0.523599 弧度 = 30.000000 度
    
    // acos函数：反余弦，返回[0, π]范围内的弧度
    double cos_val = 0.5;
    double acos_result = acos(cos_val);
    cout << "acos(0.5) = " << acos_result << " 弧度" << endl;
    cout << "转换为角度: " << rad2deg(acos_result) << " 度" << endl;
    // 输出: acos(0.5) = 1.047198 弧度 = 60.000000 度
    
    // atan函数：反正切，返回(-π/2, π/2)范围内的弧度
    double tan_val = 1.0;
    double atan_result = atan(tan_val);
    cout << "atan(1.0) = " << atan_result << " 弧度" << endl;
    cout << "转换为角度: " << rad2deg(atan_result) << " 度" << endl;
    // 输出: atan(1.0) = 0.785398 弧度 = 45.000000 度
    
    // atan2函数：考虑象限的反正切（推荐使用）
    double x = -1.0, y = 1.0;
    double atan2_result = atan2(y, x);  // 注意参数顺序是(y, x)
    cout << "atan2(1, -1) = " << atan2_result << " 弧度" << endl;
    cout << "转换为角度: " << rad2deg(atan2_result) << " 度" << endl;
    // 输出: atan2(1, -1) = 2.356194 弧度 = 135.000000 度
    
    return 0;
}
```

**atan2的重要用途**：`atan2(y, x)`函数是竞赛中处理角度计算的首选函数，因为它：
- 正确处理所有四个象限
- 能够区分(1, 1)和(-1, -1)等情况
- 当x和y都为0时返回0（而不是未定义）

### 双曲函数（sinh, cosh, tanh）

双曲函数在某些特定数学问题中有应用，例如：

```cpp
#include <cmath>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double x = 1.0;
    
    // 双曲正弦：sinh(x) = (e^x - e^(-x)) / 2
    double sinh_val = sinh(x);
    cout << "sinh(1.0) = " << fixed << setprecision(6) << sinh_val << endl;
    
    // 双曲余弦：cosh(x) = (e^x + e^(-x)) / 2
    double cosh_val = cosh(x);
    cout << "cosh(1.0) = " << cosh_val << endl;
    
    // 双曲正切：tanh(x) = sinh(x) / cosh(x)
    double tanh_val = tanh(x);
    cout << "tanh(1.0) = " << tanh_val << endl;
    
    // 验证恒等式
    cout << "cosh² - sinh² = " << cosh_val * cosh_val - sinh_val * sinh_val << endl;
    // 输出: 1.000000
    
    // 反双曲函数
    double asinh_val = asinh(x);
    double acosh_val = acosh(x);
    double atanh_val = atanh(0.5);  // |x| < 1
    
    cout << "asinh(1.0) = " << asinh_val << endl;
    cout << "acosh(1.0) = " << acosh_val << endl;
    cout << "atanh(0.5) = " << atanh_val << endl;
    
    return 0;
}
```

## 指数与对数函数

### 指数函数（exp, exp2, expm1）

指数函数用于计算e的幂次：

```cpp
#include <cmath>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double x = 2.0;
    
    // exp: 计算e^x
    double exp_val = exp(x);
    cout << "exp(2.0) = " << fixed << setprecision(6) << exp_val << endl;
    // 输出: exp(2.0) = 7.389056
    
    // exp2: 计算2^x（C++11）
    double exp2_val = exp2(x);
    cout << "exp2(2.0) = " << exp2_val << endl;
    // 输出: exp2(2.0) = 4.000000
    
    // expm1: 计算e^x - 1，对于小的x值更精确
    double expm1_val = expm1(x);
    cout << "expm1(2.0) = " << expm1_val << endl;
    // 输出: expm1(2.0) = 6.389056
    
    // 当x很小时，expm1更精确
    double small_x = 0.0001;
    cout << "\n对于小值x = " << small_x << ":" << endl;
    cout << "exp(x) - 1 = " << exp(small_x) - 1 << endl;
    cout << "expm1(x) = " << expm1(small_x) << endl;
    // exp(x) - 1可能会损失精度，而expm1更精确
    
    return 0;
}
```

**重要提示**：`expm1`函数在处理非常小的x值时比`exp(x) - 1`更精确，因为当x接近0时，两者相减会导致有效数字的损失。

### 对数函数（log, log10, log2, log1p）

对数函数用于计算各种底数的对数：

```cpp
#include <cmath>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double x = 100.0;
    
    // log: 自然对数（以e为底）
    double log_val = log(x);
    cout << "log(100) = " << fixed << setprecision(6) << log_val << endl;
    // 输出: log(100) = 4.605170
    
    // log10: 以10为底的对数
    double log10_val = log10(x);
    cout << "log10(100) = " << log10_val << endl;
    // 输出: log10(100) = 2.000000
    
    // log2: 以2为底的对数（C++11）
    double log2_val = log2(x);
    cout << "log2(100) = " << log2_val << endl;
    // 输出: log2(100) = 6.643856
    
    // log1p: 计算log(1 + x)，对于小的x值更精确
    double log1p_val = log1p(x);
    cout << "log1p(100) = " << log1p_val << endl;
    // 输出: log1p(100) = 4.615121
    
    // 对于小的x值，log1p更精确
    double small_x = 0.0001;
    cout << "\n对于小值x = " << small_x << ":" << endl;
    cout << "log(1 + x) = " << log(1 + small_x) << endl;
    cout << "log1p(x) = " << log1p(small_x) << endl;
    
    // 换底公式
    cout << "\n验证换底公式: log2(x) = log(x) / log(2)" << endl;
    cout << "log2(100) = " << log(x) / log(2) << endl;
    cout << "直接计算: log2(100) = " << log2(x) << endl;
    
    return 0;
}
```

**换底公式**：如果需要计算任意底数的对数，可以使用换底公式：

```cpp
double log_base(double x, double base) {
    return log(x) / log(base);
}

// 例如计算以3为底的对数
double log3_100 = log(100) / log(3);
```

## 幂函数与开方

### pow函数

`pow`函数用于计算幂次方，是竞赛中使用最频繁的数学函数之一：

```cpp
#include <cmath>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    // 基本用法
    cout << "pow(2, 3) = " << pow(2, 3) << endl;        // 8
    cout << "pow(2, -2) = " << pow(2, -2) << endl;     // 0.25
    cout << "pow(2, 0.5) = " << pow(2, 0.5) << endl;   // 1.414214 (√2)
    cout << "pow(-2, 3) = " << pow(-2, 3) << endl;     // -8
    cout << "pow(-2, 2) = " << pow(-2, 2) << endl;     // 4
    
    // 整数幂的快速计算（使用位运算）
    auto power_int = [](int base, int exp) -> long long {
        long long result = 1;
        long long b = base;
        int e = exp;
        while (e > 0) {
            if (e & 1) result *= b;
            b *= b;
            e >>= 1;
        }
        return result;
    };
    
    cout << "\n使用快速幂计算:" << endl;
    cout << "power_int(2, 10) = " << power_int(2, 10) << endl;  // 1024
    cout << "power_int(3, 5) = " << power_int(3, 5) << endl;    // 243
    
    // 计算组合数（阶乘相关）
    auto factorial = [](int n) -> long long {
        long long result = 1;
        for (int i = 2; i <= n; i++) {
            result *= i;
        }
        return result;
    };
    
    cout << "\n计算组合数 C(10, 3):" << endl;
    int n = 10, k = 3;
    long long C = factorial(n) / (factorial(k) * factorial(n - k));
    cout << "C(" << n << ", " << k << ") = " << C << endl;  // 120
    
    return 0;
}
```

**注意事项**：
- `pow`函数返回`double`类型，对于整数幂可能会产生精度问题
- 对于整数幂的精确计算，建议使用整数运算或高精度库
- `pow(-base, exp)`当`exp`不是整数时会产生复数，C++中会产生NaN

### sqrt函数

`sqrt`函数用于计算平方根：

```cpp
#include <cmath>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double x = 16.0;
    
    // sqrt: 平方根
    double sqrt_val = sqrt(x);
    cout << "sqrt(16) = " << fixed << setprecision(6) << sqrt_val << endl;
    // 输出: sqrt(16) = 4.000000
    
    // 负数的平方根会产生NaN
    double negative = -16.0;
    double sqrt_negative = sqrt(negative);
    cout << "sqrt(-16) = " << sqrt_negative << endl;
    // 输出: nan
    
    // 使用cbrt计算立方根（C++11）
    double cbrt_val = cbrt(8.0);
    cout << "cbrt(8) = " << cbrt_val << endl;
    // 输出: cbrt(8) = 2.000000
    
    // cbrt可以处理负数
    double cbrt_negative = cbrt(-8.0);
    cout << "cbrt(-8) = " << cbrt_negative << endl;
    // 输出: cbrt(-8) = -2.000000
    
    // hypot函数：计算直角三角形的斜边（C++11）
    double a = 3.0, b = 4.0;
    double hypotenuse = hypot(a, b);
    cout << "hypot(3, 4) = " << hypotenuse << endl;
    // 输出: hypot(3, 4) = 5.000000
    
    // hypot可以处理多个参数
    double hypotenuse_3d = hypot(3.0, 4.0, 5.0);
    cout << "hypot(3, 4, 5) = " << hypotenuse_3d << endl;
    
    return 0;
}
```

## 舍入与取整函数

### floor与ceil

`floor`返回不大于参数的最大整数（向下取整），`ceil`返回不小于参数的最小整数（向上取整）：

```cpp
#include <cmath>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double values[] = {2.3, 2.7, -2.3, -2.7, 3.0};
    
    cout << "值      floor    ceil" << endl;
    for (double v : values) {
        cout << fixed << setprecision(1) << v << "      " 
             << floor(v) << "      " << ceil(v) << endl;
    }
    
    // 输出:
    // 2.3      2      3
    // 2.7      2      3
    // -2.3     -3     -2
    // -2.7     -3     -2
    // 3.0      3      3
    
    // 应用场景：向上取整计算页数
    int total_items = 100;
    int items_per_page = 7;
    int total_pages = (total_items + items_per_page - 1) / items_per_page;
    cout << "\n需要 " << total_items << " 个项目，每页 " << items_per_page 
         << " 个，共需 " << total_pages << " 页" << endl;
    // 或者使用ceil
    cout << "使用ceil计算: " << (int)ceil((double)total_items / items_per_page) << endl;
    
    return 0;
}
```

### round函数

`round`函数进行四舍五入，使用"银行家舍入法"（round to even）：

```cpp
#include <cmath>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double values[] = {2.3, 2.5, 2.7, 3.5, 4.5};
    
    cout << "值      round" << endl;
    for (double v : values) {
        cout << fixed << setprecision(1) << v << "      " << round(v) << endl;
    }
    
    // 输出:
    // 2.3      2
    // 2.5      2  (四舍五入到最近的偶数)
    // 2.7      3
    // 3.5      4  (四舍五入到最近的偶数)
    // 4.5      4  (四舍五入到最近的偶数)
    
    // 负数的四舍五入
    double neg_values[] = {-2.3, -2.5, -2.7};
    cout << "\n负数四舍五入:" << endl;
    for (double v : neg_values) {
        cout << fixed << setprecision(1) << v << "      " << round(v) << endl;
    }
    // 输出:
    // -2.3     -2
    // -2.5     -2  (四舍五入到最近的偶数)
    // -2.7     -3
    
    // 固定小数位数输出
    double pi = 3.1415926535;
    cout << "\n圆周率的各种舍入:" << endl;
    cout << "floor: " << (int)floor(pi) << endl;        // 3
    cout << "ceil: " << (int)ceil(pi) << endl;          // 4
    cout << "round: " << (int)round(pi) << endl;        // 3
    cout << "保留2位: " << fixed << setprecision(2) << pi << endl;  // 3.14
    
    return 0;
}
```

### trunc函数

`trunc`函数直接截断小数部分，不进行四舍五入：

```cpp
#include <cmath>
#include <iostream>
using namespace std;

int main() {
    double values[] = {2.3, 2.7, -2.3, -2.7};
    
    cout << "值      trunc" << endl;
    for (double v : values) {
        cout << fixed << setprecision(1) << v << "      " << trunc(v) << endl;
    }
    
    // 输出:
    // 2.3      2
    // 2.7      2
    // -2.3     -2
    // -2.7     -2
    
    // trunc和floor/ceil的区别
    cout << "\n比较:" << endl;
    double x = -2.7;
    cout << "x = " << x << endl;
    cout << "trunc(x) = " << trunc(x) << endl;  // -2
    cout << "floor(x) = " << floor(x) << endl;  // -3
    cout << "ceil(x) = " << ceil(x) << endl;    // -2
    
    return 0;
}
```

## 绝对值与取模

### fabs函数

`fabs`计算浮点数的绝对值：

```cpp
#include <cmath>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double values[] = {3.14, -3.14, 0.0, -0.0, INFINITY, -INFINITY};
    
    cout << "值           fabs" << endl;
    for (double v : values) {
        cout << fixed << setprecision(4) << v << "      " << fabs(v) << endl;
    }
    
    // 输出:
    // 3.1400      3.1400
    // -3.1400     3.1400
    // 0.0000      0.0000
    // -0.0000     0.0000
    // inf         inf
    // -inf        inf
    
    // 对于整数，使用abs
    int i = -42;
    cout << "\n整数绝对值: abs(-42) = " << abs(i) << endl;
    
    // long long版本
    long long ll_val = -1234567890123;
    cout << "llabs(-1234567890123) = " << llabs(ll_val) << endl;
    
    return 0;
}
```

### fmod函数

`fmod`计算浮点数的模（余数）：

```cpp
#include <cmath>
#include <iostream>
using namespace std;

int main() {
    // fmod: 计算x/y的余数，结果的符号与x相同
    double x = 10.5, y = 3.0;
    double remainder = fmod(x, y);
    cout << "fmod(" << x << ", " << y << ") = " << remainder << endl;
    // 输出: fmod(10.5, 3.0) = 1.5
    
    // 负数的情况
    double x2 = -10.5, y2 = 3.0;
    double remainder2 = fmod(x2, y2);
    cout << "fmod(" << x2 << ", " << y2 << ") = " << remainder2 << endl;
    // 输出: fmod(-10.5, 3.0) = -1.5
    
    // remainder: 使用IEEE定义的舍入余数
    double remainder3 = remainder(10.5, 3.0);
    cout << "remainder(10.5, 3.0) = " << remainder3 << endl;
    // 输出可能不同，取决于舍入规则
    
    // modf: 分解整数和小数部分
    double num = 123.456;
    double int_part, frac_part;
    frac_part = modf(num, &int_part);
    cout << "modf(" << num << ") = 整数部分: " << int_part 
         << ", 小数部分: " << frac_part << endl;
    // 输出: modf(123.456) = 整数部分: 123, 小数部分: 0.456
    
    return 0;
}
```

## 特殊值函数

### isfinite, isinf, isnan

这些函数用于检查浮点数的特殊值：

```cpp
#include <cmath>
#include <iostream>
using namespace std;

int main() {
    double values[] = {1.0, 0.0, INFINITY, -INFINITY, NAN, 1e300, 1e-310};
    
    cout << "值               isfinite  isinf    isnan" << endl;
    for (double v : values) {
        cout << v << "       " 
             << (isfinite(v) ? "true " : "false") << "   "
             << (isinf(v) ? "true " : "false") << "   "
             << (isnan(v) ? "true " : "false") << endl;
    }
    
    // 使用isnan检查计算结果
    double x = 0.0;
    double result = 1.0 / x;  // 无穷大
    if (isinf(result)) {
        cout << "\n1.0 / 0.0 = 无穷大" << endl;
    }
    
    double y = 0.0 / 0.0;  // NaN
    if (isnan(y)) {
        cout << "0.0 / 0.0 = NaN" << endl;
    }
    
    // isnormal检查是否为正规数（不是subnormal）
    cout << "\n检查数字类型:" << endl;
    double normal = 1.0;
    double subnormal = 1e-310;
    double zero = 0.0;
    
    cout << "1.0: isnormal = " << isnormal(normal) << endl;
    cout << "1e-310: isnormal = " << isnormal(subnormal) 
         << ", isnfinite = " << isfinite(subnormal) << endl;
    cout << "0.0: isnormal = " << isnormal(zero) << endl;
    
    return 0;
}
```

### fpclassify（C++20）

C++20引入了`fpclassify`函数，可以一次检查所有特殊情况：

```cpp
#include <cmath>
// C++20
// int main() {
//     double x = 1.0;
//     
//     switch (fpclassify(x)) {
//         case FP_INFINITE: cout << "无穷大"; break;
//         case FP_NAN: cout << "NaN"; break;
//         case FP_ZERO: cout << "零"; break;
//         case FP_SUBNORMAL: cout << "非正规数"; break;
//         case FP_NORMAL: cout << "正规数"; break;
//     }
//     
//     return 0;
// }
```

## 误差函数（erf, erfc）

误差函数在统计学和概率论中有重要应用，特别是在涉及正态分布的计算中：

```cpp
#include <cmath>
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    // 误差函数 erf(x) = (2/√π) ∫₀ˣ e^(-t²) dt
    double x = 1.0;
    double erf_val = erf(x);
    cout << "erf(1.0) = " << fixed << setprecision(6) << erf_val << endl;
    // 输出: erferf(1.0) = 0.842701
    
    // 互补误差函数 erfc(x) = 1 - erf(x)
    double erfc_val = erfc(x);
    cout << "erfc(1.0) = " << erfc_val << endl;
    // 输出: erfc(1.0) = 0.157299
    
    // 验证 erfc(x) = 1 - erf(x)
    cout << "1 - erf(x) = " << 1 - erf(x) << endl;
    
    // 使用误差函数计算正态分布的累积分布函数
    // Φ(x) = 0.5 * [1 + erf(x/√2)]
    auto normal_cdf = [](double x, double mean, double stddev) {
        return 0.5 * (1.0 + erf((x - mean) / (stddev * sqrt(2.0))));
    };
    
    double mean = 100, stddev = 15;
    cout << "\n正态分布CDF (mean=100, stddev=15):" << endl;
    cout << "P(X ≤ 100) = " << normal_cdf(100, mean, stddev) << endl;  // 0.5
    cout << "P(X ≤ 130) = " << normal_cdf(130, mean, stddev) << endl;  // ~0.977
    cout << "P(X ≤ 85) = " << normal_cdf(85, mean, stddev) << endl;    // ~0.159
    
    return 0;
}
```

## 常用数学常量

C++标准库提供了几个常用的数学常量：

```cpp
#include <cmath>
#include <iostream>
using namespace std;

int main() {
    cout << "M_PI = " << M_PI << endl;                    // π
    cout << "M_PI_2 = " << M_PI_2 << endl;               // π/2
    cout << "M_PI_4 = " << M_PI_4 << endl;               // π/4
    cout << "M_1_PI = " << M_1_PI << endl;               // 1/π
    cout << "M_2_PI = " << M_2_PI << endl;               // 2/π
    cout << "M_E = " << M_E << endl;                     // e
    cout << "M_LOG2E = " << M_LOG2E << endl;             // log2(e)
    cout << "M_LN2 = " << M_LN2 << endl;                 // ln(2)
    cout << "M_SQRT2 = " << M_SQRT2 << endl;             // √2
    cout << "M_SQRT1_2 = " << M_SQRT1_2 << endl;         // 1/√2
    
    // 注意：这些常量可能需要定义_GNU_SOURCE或使用更好的方法
    // C++20起可以使用 std::numbers 相关常量
    
    return 0;
}
```

**重要提示**：某些数学常量可能不是标准定义的，如果编译时出现未定义错误，可以使用以下方式：

```cpp
// 方法1：使用M_PI前定义宏
#define _USE_MATH_DEFINES
#include <cmath>

// 方法2：C++20使用std::numbers
// #include <numbers>
// std::numbers::pi, std::numbers::e, etc.
```

## 竞赛应用实例

### 实例一：计算两点距离

```cpp
#include <cmath>
#include <iostream>
#include <utility>
using namespace std;

struct Point {
    double x, y;
};

double distance(Point a, Point b) {
    return sqrt(pow(a.x - b.x, 2) + pow(a.y - b.y, 2));
    // 或者使用hypot
    // return hypot(a.x - b.x, a.y - b.y);
}

double distance3D(Point a, Point b) {
    return sqrt(pow(a.x - b.x, 2) + pow(a.y - b.y, 2) + 
                pow(a.z - b.z, 2));
    // 或者使用hypot
    // return hypot(a.x - b.x, a.y - b.y, a.z - b.z);
}

int main() {
    Point p1 = {0, 0};
    Point p2 = {3, 4};
    cout << "距离 = " << distance(p1, p2) << endl;  // 5
    
    return 0;
}
```

### 实例二：角度计算

```cpp
#include <cmath>
#include <iostream>
using namespace std;

struct Vector2D {
    double x, y;
};

// 计算向量的长度
double length(Vector2D v) {
    return sqrt(v.x * v.x + v.y * v.y);
}

// 计算两个向量的点积
double dot(Vector2D a, Vector2D b) {
    return a.x * b.x + a.y * b.y;
}

// 计算两个向量的夹角（弧度）
double angle(Vector2D a, Vector2D b) {
    double dot_product = dot(a, b);
    double len_a = length(a);
    double len_b = length(b);
    // cos(θ) = (a·b) / (|a||b|)
    return acos(dot_product / (len_a * len_b));
}

int main() {
    Vector2D v1 = {1, 0};
    Vector2D v2 = {0, 1};
    Vector2D v3 = {1, 1};
    
    cout << "v1与v2的夹角: " << rad2deg(angle(v1, v2)) << "度" << endl;
    cout << "v1与v3的夹角: " << rad2deg(angle(v1, v3)) << "度" << endl;
    // 输出:
    // v1与v2的夹角: 90度
    // v1与v3的夹角: 45度
    
    return 0;
}
```

### 实例三：解二次方程

```cpp
#include <cmath>
#include <iostream>
#include <vector>
using namespace std;

vector<double> solveQuadratic(double a, double b, double c) {
    vector<double> roots;
    
    if (a == 0) {
        // 退化为一元一次方程
        if (b != 0) {
            roots.push_back(-c / b);
        }
        return roots;
    }
    
    double discriminant = b * b - 4 * a * c;
    
    if (discriminant > 0) {
        // 两个不同的实数根
        roots.push_back((-b + sqrt(discriminant)) / (2 * a));
        roots.push_back((-b - sqrt(discriminant)) / (2 * a));
    } else if (discriminant == 0) {
        // 一个重根
        roots.push_back(-b / (2 * a));
    } else {
        // 共轭复数根
        // 如果需要复数解，需要使用complex类型
        // 这里只返回实数部分（NaN）
        roots.push_back(NAN);
        roots.push_back(NAN);
    }
    
    return roots;
}

int main() {
    // x² - 3x + 2 = 0 的解为 x = 1, x = 2
    auto roots1 = solveQuadratic(1, -3, 2);
    cout << "x² - 3x + 2 = 0 的解: ";
    for (double r : roots1) {
        cout << r << " ";
    }
    cout << endl;
    
    // x² - 2x + 1 = 0 的解为 x = 1
    auto roots2 = solveQuadratic(1, -2, 1);
    cout << "x² - 2x + 1 = 0 的解: ";
    for (double r : roots2) {
        cout << r << " ";
    }
    cout << endl;
    
    // x² + 1 = 0 的解为复数
    auto roots3 = solveQuadratic(1, 0, 1);
    cout << "x² + 1 = 0 的解: ";
    for (double r : roots3) {
        cout << r << " ";
    }
    cout << endl;
    
    return 0;
}
```

### 实例四：计算几何面积

```cpp
#include <cmath>
#include <iostream>
using namespace std;

struct Point {
    double x, y;
};

// 使用海伦公式计算三角形面积
double triangleArea(Point a, Point b, Point c) {
    // 计算三边长度
    double side1 = sqrt(pow(a.x - b.x, 2) + pow(a.y - b.y, 2));
    double side2 = sqrt(pow(b.x - c.x, 2) + pow(b.y - c.y, 2));
    double side3 = sqrt(pow(c.x - a.x, 2) + pow(c.y - a.y, 2));
    
    // 半周长
    double s = (side1 + side2 + side3) / 2;
    
    // 海伦公式
    return sqrt(s * (s - side1) * (s - side2) * (s - side3));
}

// 使用叉积计算多边形面积
double polygonArea(const vector<Point>& vertices) {
    double area = 0;
    int n = vertices.size();
    
    for (int i = 0; i < n; i++) {
        const Point& current = vertices[i];
        const Point& next = vertices[(i + 1) % n];
        area += current.x * next.y - next.x * current.y;
    }
    
    return abs(area) / 2.0;
}

int main() {
    // 计算三角形面积
    Point p1 = {0, 0}, p2 = {4, 0}, p3 = {0, 3};
    cout << "三角形面积: " << triangleArea(p1, p2, p3) << endl;  // 6
    
    // 计算四边形面积
    vector<Point> quad = {{0, 0}, {4, 0}, {4, 3}, {0, 3}};
    cout << "四边形面积: " << polygonArea(quad) << endl;  // 12
    
    return 0;
}
```

## 注意事项与常见错误

### 1. 浮点精度问题

```cpp
#include <cmath>
#include <iostream>
using namespace std;

int main() {
    // 浮点数相等性判断
    double a = 0.1 + 0.2;
    double b = 0.3;
    
    cout << "a = " << a << endl;
    cout << "b = " << b << endl;
    cout << "a == b: " << (a == b) << endl;  // false！
    
    // 正确做法：使用容差比较
    const double EPSILON = 1e-9;
    cout << "a ≈ b: " << (fabs(a - b) < EPSILON) << endl;  // true
    
    // 判断浮点数是否为0
    double x = 1e-15;
    cout << "x == 0: " << (x == 0) << endl;  // false
    cout << "x ≈ 0: " << (fabs(x) < EPSILON) << endl;  // true
    
    return 0;
}
```

### 2. 弧度与角度混淆

```cpp
#include <cmath>
#include <iostream>
using namespace std;

int main() {
    double angle_deg = 90;
    
    // 错误：直接将角度当作弧度使用
    // double sin_wrong = sin(angle_deg);
    // cout << "sin(90°)错误计算: " << sin_wrong << endl;  // ~0.894 而不是 1
    
    // 正确：先转换为弧度
    double angle_rad = angle_deg * M_PI / 180.0;
    double sin_correct = sin(angle_rad);
    cout << "sin(90°)正确计算: " << sin_correct << endl;  // 1
    
    return 0;
}
```

### 3. 大数溢出问题

```cpp
#include <cmath>
#include <iostream>
#include <limits>
using namespace std;

int main() {
    // 浮点数的范围
    cout << "double最大正数: " << numeric_limits<double>::max() << endl;
    cout << "double最小正数: " << numeric_limits<double>::min() << endl;
    cout << "double精度: " << numeric_limits<double>::digits << " 位" << endl;
    
    // pow可能导致溢出
    double large = pow(10, 308);
    cout << "10^308 = " << large << endl;  // 接近最大double
    
    double overflow = pow(10, 309);
    cout << "10^309 = " << overflow << endl;  // inf
    
    // 解决：使用对数
    // 要计算 log(10^309) = 309 * log(10)
    double log_value = 309 * log(10);
    cout << "log(10^309) = " << log_value << endl;  // 不会溢出
    
    return 0;
}
```

### 4. 特殊值传播

```cpp
#include <cmath>
#include <iostream>
using namespace std;

int main() {
    // NaN的传播性
    double nan_val = NAN;
    double result = nan_val + 1;
    cout << "NAN + 1 = " << result << endl;  // nan
    
    // 无穷大的传播
    double inf_val = INFINITY;
    double result2 = inf_val - inf_val;
    cout << "inf - inf = " << result2 << endl;  // nan
    
    // 0/0 = nan
    double result3 = 0.0 / 0.0;
    cout << "0/0 = " << result3 << endl;  // nan
    
    // 检查并处理特殊值
    double x = 1.0;
    double y = 0.0;
    double z = x / y;
    
    if (isnan(z)) {
        cout << "计算结果为NaN" << endl;
    } else if (isinf(z)) {
        cout << "计算结果为无穷大" << endl;
    } else {
        cout << "计算结果: " << z << endl;
    }
    
    return 0;
}
```

## C++20新特性

### std::numbers（C++20）

C++20引入了`std::numbers`命名空间，提供了更标准化的数学常量：

```cpp
#include <numbers>
#include <iostream>
using namespace std;

// C++20
// int main() {
//     cout << "π = " << numbers::pi << endl;
//     cout << "e = " << numbers::e << endl;
//     cout << "√2 = " << numbers::sqrt2 << endl;
//     cout << "ln(2) = " << numbers::ln2 << endl;
//     cout << "log2(e) = " << numbers::log2e << endl;
//     
//     return 0;
// }
```

### std::lerp（C++20）

线性插值函数：

```cpp
#include <numbers>
// C++20
// int main() {
//     // 线性插值: lerp(a, b, t) = a + t * (b - a)
//     double a = 0.0, b = 10.0;
//     cout << "lerp(0, 10, 0.5) = " << lerp(a, b, 0.5) << endl;  // 5.0
//     
//     return 0;
// }
```

### std::midpoint（C++20）

计算两个数的中点：

```cpp
#include <numbers>
// C++20
// int main() {
//     int a = 1, b = 9;
//     cout << "midpoint(1, 9) = " << midpoint(a, b) << endl;  // 5
//     
//     return 0;
// }
```

## 总结与建议

`<cmath>`头文件是ACM竞赛中处理数值计算的核心工具。以下是一些关键建议：

首先，理解三角函数使用弧度而非角度，在使用时需要进行转换。记住`π弧度 = 180度`。

其次，`atan2(y, x)`是处理角度计算的首选函数，它能正确处理所有四个象限。

第三，注意浮点数精度问题，使用`fabs(a - b) < EPSILON`来比较浮点数，而不是直接使用`==`。

第四，对于非常小的数值，使用`expm1`和`log1p`来避免精度损失。

第五，对于涉及极大或极小数的计算，考虑使用对数来避免溢出。

第六，在处理特殊值（NaN、无穷大）时，使用`isnan`、`isinf`、`isfinite`等函数进行检查。

最后，熟悉各种数学函数的应用场景，选择合适的函数解决问题。