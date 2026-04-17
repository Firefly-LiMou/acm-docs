# ACM竞赛C++标准库总结

本项目旨在为ACM/ICPC编程竞赛提供一份全面、规范、易于查阅的C++标准库参考手册。文档基于C++17标准编写，涵盖了竞赛中最常用的STL组件和标准库函数。

## 文档结构

| 文件 | 头文件 | 主要内容 |
|------|--------|----------|
| [fast_io.md](./fast_io.md) | `<iostream>` | 各种快读模板 |
| [cstdio.md](./cstdio.md) | `<cstdio>` | C风格输入输出 |
| [iostream.md](./iostream.md) | `<iostream>` | 输入输出流、getline、cin方法 |
| [iomanip.md](./iomanip.md) | `<iomanip>` | 格式化参数设置 |
| [string.md](./string.md) | `<string>` | 字符串操作、字符处理 |
| [vector.md](./vector.md) | `<vector>` | 动态数组容器 |
| [list.md](./list.md) | `<list>` | 双向链表容器 |
| [deque.md](./deque.md) | `<deque>` | 双端队列容器 |
| [stack.md](./stack.md) | `<stack>` | 栈容器适配器 |
| [queue.md](./queue.md) | `<queue>` | 队列容器适配器 |
| [priority_queue.md](./priority_queue.md) | `<priority_queue>` | 优先队列（堆） |
| [set.md](./set.md) | `<set>` | 有序集合（红黑树） |
| [map.md](./map.md) | `<map>` | 有序键值对映射 |
| [unordered_set.md](./unordered_set.md) | `<unordered_set>` | 无序哈希集合 |
| [unordered_map.md](./unordered_map.md) | `<unordered_map>` | 无序哈希映射 |
| [algorithm.md](./algorithm.md) | `<algorithm>` | 算法（排序、查找、变换） |
| [cmath.md](./cmath.md) | `<cmath>` | 数学函数 |
| [bitset.md](./bitset.md) | `<bitset>` | 位集合、位操作 |
| [numeric.md](./numeric.md) | `<numeric>` | 数值算法（累加、内积、GCD） |
| [cctype.md](./cctype.md) | `<cctype>` | 字符分类与转换 |
| [functional.md](./functional.md) | `<functional>` | 函数对象、bind、lambda |

## 使用说明

### 1. 复杂度标注
每个函数和操作都标注了时间复杂度，便于竞赛中评估算法性能：
- **O(1)** - 常数时间
- **O(log n)** - 对数时间
- **O(n)** - 线性时间
- **O(n log n)** - 线性对数时间
- **O(n²)** - 平方时间

### 2. 代码示例
所有主要功能都配有简洁的代码示例，演示正确用法。

### 3. 注意事项
每个部分都包含常见陷阱和最佳实践，帮助避免竞赛中的常见错误。

## 命名空间

除非另有说明，本文档中的示例默认包含以下命名空间：
```cpp
using namespace std;
```

## 编译要求

文档中的代码示例基于C++17标准，编译时请使用：
```bash
g++ -std=c++17 -O2 -Wall -Wextra program.cpp
```

## 贡献指南

如果您发现错误或有改进建议，欢迎提交Issue或Pull Request。

## 许可证

本项目仅供学习和竞赛准备使用。
