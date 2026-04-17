# stack 容器适配器详解

`<stack>`头文件实现了C++标准库中的栈容器适配器`std::stack`。栈是一种后进先出（LIFO, Last In First Out）的数据结构，只允许在序列的一端进行插入和删除操作。`stack`不是独立的容器，而是基于其他容器实现的适配器，默认情况下使用`deque`作为底层容器。在ACM竞赛中，栈是解决括号匹配、表达式求值、深度优先搜索等问题的基本工具。

## 1. stack类概述

`std::stack`是一个容器适配器，它提供了栈的标准接口，但将具体实现委托给底层的顺序容器。默认情况下，`stack`使用`deque`作为底层容器，但也可以通过模板参数指定使用`vector`或`list`作为底层容器。栈的核心操作包括`push`（入栈）、`pop`（出栈）和`top`（访问栈顶元素）。

`stack`适配器的设计遵循LIFO原则：最后插入的元素最先被移除。这意味着栈的所有操作都发生在同一端——栈顶。这种设计使得栈成为处理嵌套结构、递归问题转换和回溯算法的理想工具。

### 1.1 头文件包含方式

```cpp
#include <stack>           // 标准方式
#include <bits/stdc++.h>   // 竞赛常用
using namespace std;       // 使用std命名空间
```

### 1.2 默认底层容器

```cpp
// 默认使用deque作为底层容器
stack<int> s1;             // deque<int>作为底层容器

// 指定vector作为底层容器
stack<int, vector<int>> s2; // vector<int>作为底层容器

// 指定list作为底层容器
stack<int, list<int>> s3;   // list<int>作为底层容器
```

**选择底层容器的考虑**：

- **deque（默认）**：在两端插入删除都高效，不需要预分配
- **vector**：如果主要在尾部操作，且需要随机访问栈内元素
- **list**：如果需要稳定的迭代器，但一般不需要

## 2. 基本操作

### 2.1 push() - 入栈操作

```cpp
stack<int> s;

// push() - 将元素压入栈顶
s.push(1);                       // 栈: [1]
s.push(2);                       // 栈: [1, 2]（栈顶在右侧）
s.push(3);                       // 栈: [1, 2, 3]

// 复杂度: O(1)（取决于底层容器）
```

### 2.2 pop() - 出栈操作

```cpp
stack<int> s = {1, 2, 3};

// pop() - 移除栈顶元素（不返回）
s.pop();                         // 栈变为 [1, 2]
s.pop();                         // 栈变为 [1]

// 注意: pop()不返回被删除的元素
// 错误写法: int x = s.pop();
// 正确写法: int x = s.top(); s.pop();

// 对空栈调用pop()是未定义行为
if (!s.empty()) {
    s.pop();
}
```

### 2.3 top() - 访问栈顶元素

```cpp
stack<int> s = {1, 2, 3};

// top() - 返回栈顶元素的引用
int x = s.top();                 // x = 3（栈顶元素）

// 可以修改栈顶元素
s.top() = 10;                    // 栈变为 [1, 2, 10]

// 对空栈调用top()是未定义行为
if (!s.empty()) {
    cout << s.top() << endl;
}
```

### 2.4 empty()和size()

```cpp
stack<int> s;

// empty() - 判断栈是否为空
bool is_empty = s.empty();       // true（空栈）

// size() - 返回栈中元素数量
size_t sz = s.size();            // 0

s.push(1);
s.push(2);

is_empty = s.empty();            // false
sz = s.size();                   // 2

// 推荐使用empty()而非size() == 0（更清晰，语义更明确）
```

## 3. 构造函数与初始化

### 3.1 默认构造

```cpp
// 创建空栈
stack<int> s1;                   // 空栈

stack<string> s2;
stack<vector<int>> s3;
```

### 3.2 拷贝构造与移动构造

```cpp
// 拷贝构造 - 创建栈的副本
stack<int> original;
original.push(1);
original.push(2);
original.push(3);

stack<int> copy1(original);      // 完整的深拷贝

// 移动构造 - 高效转移资源（C++11）
stack<int> moved = std::move(original);  // original变为空，moved获得资源
```

### 3.3 从容器构造（C++11）

```cpp
// 可以从支持push_back的容器构造stack
vector<int> v = {1, 2, 3, 4, 5};
stack<int> s1(v);                // 从vector构造，v的元素被逆序压入

deque<int> d = {1, 2, 3};
stack<int> s2(d);                // 从deque构造

list<int> l = {1, 2, 3};
stack<int, list<int>> s3(l);     // 从list构造

// 注意: 构造时元素被按顺序压入，所以栈顶是容器的最后一个元素
// s1 栈顶是 5，s2 栈顶是 3，s3 栈顶是 3
```

### 3.4 初始化列表构造

```cpp
// C++11支持初始化列表，但stack没有直接的initializer_list构造函数
// 需要逐个push
stack<int> s;
s.push(1);
s.push(2);
s.push(3);
// 或者使用下面的方式
vector<int> init = {1, 2, 3};
stack<int> s2(init);
```

## 4. swap()操作

```cpp
stack<int> s1 = {1, 2, 3};
stack<int> s2 = {4, 5, 6};

// swap() - 交换两个栈的内容
s1.swap(s2);                     // s1 = {4, 5, 6}, s2 = {1, 2, 3}

// 非成员函数版
swap(s1, s2);                    // 同样交换内容

// 快速清空栈
stack<int> temp;
s1.swap(temp);                   // s1变为空
```

## 5. 竞赛中的常见应用

### 5.1 括号匹配

```cpp
#include <stack>
#include <string>

bool isValid(const string& s) {
    stack<char> st;
    
    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else if (c == ')' || c == ']' || c == '}') {
            if (st.empty()) return false;
            char top = st.top();
            st.pop();
            
            if ((c == ')' && top != '(') ||
                (c == ']' && top != '[') ||
                (c == '}' && top != '{')) {
                return false;
            }
        }
    }
    
    return st.empty();
}

// 使用示例
int main() {
    cout << isValid("()[]{}") << endl;    // 1 (true)
    cout << isValid("([)]") << endl;      // 0 (false)
    cout << isValid("{[]()}") << endl;    // 1 (true)
    return 0;
}
```

### 5.2 表达式求值（中缀表达式）

```cpp
#include <stack>
#include <string>
#include <cctype>
#include <unordered_map>

// 运算符优先级
int precedence(char op) {
    if (op == '+' || op == '-') return 1;
    if (op == '*' || op == '/') return 2;
    return 0;
}

// 应用运算符
int applyOp(int a, int b, char op) {
    switch (op) {
        case '+': return a + b;
        case '-': return a - b;
        case '*': return a * b;
        case '/': return a / b;  // 注意除零情况
    }
    return 0;
}

// 判断是否是运算符
bool isOperator(char c) {
    return c == '+' || c == '-' || c == '*' || c == '/';
}

// 中缀表达式求值
int evaluate(const string& expression) {
    stack<int> values;           // 操作数栈
    stack<char> ops;             // 运算符栈
    
    for (size_t i = 0; i < expression.size(); ++i) {
        char c = expression[i];
        
        if (isspace(c)) continue;
        
        if (isdigit(c)) {
            int num = 0;
            while (i < expression.size() && isdigit(expression[i])) {
                num = num * 10 + (expression[i] - '0');
                ++i;
            }
            --i;                  // 因为for循环会++i
            values.push(num);
        }
        else if (c == '(') {
            ops.push(c);
        }
        else if (c == ')') {
            while (!ops.empty() && ops.top() != '(') {
                int val2 = values.top(); values.pop();
                int val1 = values.top(); values.pop();
                char op = ops.top(); ops.pop();
                values.push(applyOp(val1, val2, op));
            }
            if (!ops.empty()) ops.pop();  // 弹出'('
        }
        else if (isOperator(c)) {
            while (!ops.empty() && 
                   ops.top() != '(' && 
                   precedence(ops.top()) >= precedence(c)) {
                int val2 = values.top(); values.pop();
                int val1 = values.top(); values.pop();
                char op = ops.top(); ops.pop();
                values.push(applyOp(val1, val2, op));
            }
            ops.push(c);
        }
    }
    
    while (!ops.empty()) {
        int val2 = values.top(); values.pop();
        int val1 = values.top(); values.pop();
        char op = ops.top(); ops.pop();
        values.push(applyOp(val1, val2, op));
    }
    
    return values.top();
}
```

### 5.3 后缀表达式求值

```cpp
#include <stack>
#include <string>
#include <cctype>
#include <sstream>

bool isOperator(const string& token) {
    return token == "+" || token == "-" || 
           token == "*" || token == "/";
}

int applyOp(int a, int b, const string& op) {
    if (op == "+") return a + b;
    if (op == "-") return a - b;
    if (op == "*") return a * b;
    if (op == "/") return a / b;
    return 0;
}

int evaluatePostfix(const string& expr) {
    stack<int> st;
    istringstream iss(expr);
    string token;
    
    while (iss >> token) {
        if (isOperator(token)) {
            int b = st.top(); st.pop();
            int a = st.top(); st.pop();
            st.push(applyOp(a, b, token));
        } else {
            st.push(stoi(token));
        }
    }
    
    return st.top();
}

// 使用示例
int main() {
    cout << evaluatePostfix("5 3 +") << endl;           // 8
    cout << evaluatePostfix("5 3 2 * + 1 -") << endl;   // 10
    return 0;
}
```

### 5.4 深度优先搜索（DFS）

```cpp
#include <stack>
#include <vector>

struct Graph {
    int n;
    vector<vector<int>> adj;
    Graph(int n) : n(n), adj(n) {}
    
    void addEdge(int u, int v) {
        adj[u].push_back(v);
    }
};

// 递归版DFS
void dfsRecursive(const Graph& g, int start, vector<int>& visited) {
    visited[start] = 1;
    cout << start << " ";
    for (int neighbor : g.adj[start]) {
        if (!visited[neighbor]) {
            dfsRecursive(g, neighbor, visited);
        }
    }
}

// 迭代版DFS（使用stack）
void dfsIterative(const Graph& g, int start, vector<int>& visited) {
    stack<int> st;
    st.push(start);
    
    while (!st.empty()) {
        int node = st.top();
        st.pop();
        
        if (visited[node]) continue;
        visited[node] = 1;
        cout << node << " ";
        
        // 反向遍历使输出顺序与递归版一致
        for (auto it = g.adj[node].rbegin(); 
             it != g.adj[node].rend(); ++it) {
            if (!visited[*it]) {
                st.push(*it);
            }
        }
    }
}

// 使用示例
int main() {
    Graph g(7);
    g.addEdge(0, 1);
    g.addEdge(0, 2);
    g.addEdge(1, 3);
    g.addEdge(1, 4);
    g.addEdge(2, 5);
    g.addEdge(2, 6);
    
    vector<int> visited(7, 0);
    cout << "DFS: ";
    dfsIterative(g, 0, visited);      // 输出: 0 2 6 5 1 4 3（或类似顺序）
    cout << endl;
    
    return 0;
}
```

### 5.5 单调栈问题

```cpp
#include <stack>
#include <vector>

// 下一个更大元素
vector<int> nextGreaterElement(const vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> st;  // 存储索引
    
    for (int i = 0; i < n; ++i) {
        while (!st.empty() && nums[i] > nums[st.top()]) {
            result[st.top()] = i;  // 下一个更大元素是i
            st.pop();
        }
        st.push(i);
    }
    
    return result;
}

// 每日温度（下一个更高温度的天数）
vector<int> dailyTemperatures(const vector<int>& temperatures) {
    int n = temperatures.size();
    vector<int> result(n, 0);
    stack<int> st;  // 存储索引
    
    for (int i = 0; i < n; ++i) {
        while (!st.empty() && temperatures[i] > temperatures[st.top()]) {
            int prev = st.top();
            st.pop();
            result[prev] = i - prev;  // 等待天数
        }
        st.push(i);
    }
    
    return result;
}

// 柱状图中的最大矩形
int largestRectangleArea(const vector<int>& heights) {
    int n = heights.size();
    stack<int> st;
    int maxArea = 0;
    
    for (int i = 0; i <= n; ++i) {
        int h = (i == n) ? 0 : heights[i];
        
        while (!st.empty() && h < heights[st.top()]) {
            int height = heights[st.top()];
            st.pop();
            int width = st.empty() ? i : i - st.top() - 1;
            maxArea = max(maxArea, height * width);
        }
        st.push(i);
    }
    
    return maxArea;
}
```

### 5.6 迷宫问题求解

```cpp
#include <stack>
#include <vector>
#include <utility>

struct Pos {
    int x, y;
    Pos(int x, int y) : x(x), y(y) {}
};

bool inBounds(int x, int y, int m, int n) {
    return x >= 0 && x < m && y >= 0 && y < n;
}

vector<Pos> solveMaze(const vector<vector<int>>& maze, 
                      Pos start, Pos end) {
    int m = maze.size(), n = maze[0].size();
    vector<vector<bool>> visited(m, vector<bool>(n, false));
    stack<pair<Pos, vector<Pos>>> st;  // {当前位置, 路径}
    
    st.push({start, {start}});
    visited[start.x][start.y] = true;
    
    const int dx[] = {0, 0, 1, -1};
    const int dy[] = {1, -1, 0, 0};
    
    while (!st.empty()) {
        auto [current, path] = st.top();
        st.pop();
        
        if (current.x == end.x && current.y == end.y) {
            return path;  // 找到路径
        }
        
        for (int dir = 0; dir < 4; ++dir) {
            int nx = current.x + dx[dir];
            int ny = current.y + dy[dir];
            
            if (inBounds(nx, ny, m, n) && 
                maze[nx][ny] == 0 && 
                !visited[nx][ny]) {
                visited[nx][ny] = true;
                vector<Pos> newPath = path;
                newPath.push_back(Pos(nx, ny));
                st.push({Pos(nx, ny), newPath});
            }
        }
    }
    
    return {};  // 无解
}
```

### 5.7 消除游戏

```cpp
#include <stack>
#include <string>
#include <unordered_map>

// 消除连续重复字符（如LeetCode 1209）
string removeDuplicates(const string& s, int k) {
    if (s.empty()) return "";
    
    // 存储{字符, 连续计数}
    struct Node {
        char c;
        int count;
    };
    
    stack<Node> st;
    
    for (char c : s) {
        if (!st.empty() && st.top().c == c) {
            st.top().count++;
            if (st.top().count == k) {
                st.pop();
            }
        } else {
            st.push({c, 1});
        }
    }
    
    // 构建结果
    string result;
    while (!st.empty()) {
        Node node = st.top();
        st.pop();
        result.append(node.count, node.c);
    }
    
    reverse(result.begin(), result.end());
    return result;
}
```

## 6. 与其他语言栈实现的比较

### 6.1 C++ stack vs Python list

```python
# Python list作为栈
stack = []
stack.append(1)     # push
stack.append(2)
stack.append(3)
print(stack.pop())  # 3，pop并返回
print(stack[-1])    # 2，访问栈顶
```

```cpp
// C++ stack
stack<int> s;
s.push(1);
s.push(2);
s.push(3);
cout << s.top() << endl;  // 访问栈顶
s.pop();                   // 移除栈顶（不返回）
cout << s.top() << endl;  // 2
```

**关键区别**：C++的`pop()`不返回值，需要先调用`top()`获取值，再调用`pop()`移除。

### 6.2 使用vector模拟栈（某些场景更高效）

```cpp
// 当需要随机访问或已知大小时，使用vector可能更高效
vector<int> stack;
stack.push_back(1);    // push
stack.push_back(2);
stack.back() = 10;     // 修改栈顶
int x = stack.back();  // 访问栈顶
stack.pop_back();      // pop

// 优点：可以随机访问，可以预先reserve
// 缺点：需要自己保证使用正确（push_back/pop_back）
```

## 7. 自定义栈实现（对比学习）

```cpp
template<typename T>
class SimpleStack {
private:
    vector<T> data;
public:
    // push
    void push(const T& x) {
        data.push_back(x);
    }
    
    // pop
    void pop() {
        if (data.empty()) {
            throw runtime_error("Stack is empty");
        }
        data.pop_back();
    }
    
    // top
    T& top() {
        if (data.empty()) {
            throw runtime_error("Stack is empty");
        }
        return data.back();
    }
    
    const T& top() const {
        if (data.empty()) {
            throw runtime_error("Stack is empty");
        }
        return data.back();
    }
    
    // empty
    bool empty() const {
        return data.empty();
    }
    
    // size
    size_t size() const {
        return data.size();
    }
    
    // swap
    void swap(SimpleStack& other) {
        data.swap(other.data);
    }
};
```

## 8. 常见陷阱与注意事项

### 8.1 pop()不返回值

```cpp
stack<int> s = {1, 2, 3};

// 错误写法
int x = s.pop();           // 编译错误！pop()返回void

// 正确写法
int x = s.top();
s.pop();

// 或者使用emplace的新方法（C++11）
s.emplace(4);              // 直接构造，push_back等价操作
```

### 8.2 空栈操作

```cpp
stack<int> s;

// 以下操作都是未定义行为
// int x = s.top();        // 空栈访问栈顶
// s.pop();                // 空栈弹出

// 正确做法
if (!s.empty()) {
    int x = s.top();
    s.pop();
}

// C++17没有try_pop()或top_pop()，必须手动检查
```

### 8.3 迭代器问题

```cpp
stack<int> s = {1, 2, 3};

// stack不提供迭代器！
// 不能使用范围for循环遍历stack
// for (int x : s) { ... }  // 编译错误！

// 正确做法：不遍历stack，而是通过top()和pop()访问元素
while (!s.empty()) {
    cout << s.top() << " ";
    s.pop();
}
```

### 8.4 不能复制或比较

```cpp
stack<int> s1 = {1, 2, 3};
stack<int> s2 = {1, 2, 3};

// stack不支持比较运算符
// if (s1 == s2) { ... }     // 编译错误！

// stack不支持直接遍历
// for (auto it = s1.begin(); ...)  // 编译错误！
```

## 9. 复杂度分析

| 操作 | 时间复杂度 | 说明 |
|------|------------|------|
| `push()` | O(1) amortized | 入栈 |
| `pop()` | O(1) | 出栈 |
| `top()` | O(1) | 访问栈顶 |
| `empty()` | O(1) | 判断空 |
| `size()` | O(1) | 获取大小 |
| `swap()` | O(1) | 交换内容 |

**Amortized说明**：`push()`的amortized时间复杂度取决于底层容器。使用`deque`或`vector`时，`push_back()`的amortized复杂度为O(1)。

## 10. 完整竞赛模板

```cpp
#include <bits/stdc++.h>
using namespace std;

// 栈应用的通用模板
class StackTemplate {
public:
    // 1. 括号匹配
    static bool isValid(const string& s) {
        stack<char> st;
        unordered_map<char, char> pairs = {
            {')', '('},
            {']', '['},
            {'}', '{'}
        };
        
        for (char c : s) {
            if (pairs.count(c)) {
                if (st.empty() || st.top() != pairs[c]) {
                    return false;
                }
                st.pop();
            } else {
                st.push(c);
            }
        }
        
        return st.empty();
    }
    
    // 2. 单调栈模板
    template<typename Func>
    static vector<int> monotonicStack(const vector<int>& nums, 
                                      Func compare) {
        int n = nums.size();
        vector<int> result(n, -1);
        stack<int> st;
        
        for (int i = 0; i < n; ++i) {
            while (!st.empty() && compare(nums[i], nums[st.top()])) {
                result[st.top()] = i;
                st.pop();
            }
            st.push(i);
        }
        
        return result;
    }
    
    // 3. DFS遍历
    static void dfs(const vector<vector<int>>& graph, int start) {
        int n = graph.size();
        vector<char> visited(n, 0);
        stack<int> st;
        st.push(start);
        
        while (!st.empty()) {
            int u = st.top();
            st.pop();
            
            if (visited[u]) continue;
            visited[u] = 1;
            cout << u << " ";
            
            for (int i = graph[u].size() - 1; i >= 0; --i) {
                int v = graph[u][i];
                if (!visited[v]) {
                    st.push(v);
                }
            }
        }
    }
};
```

## 11. 总结

`std::stack`是ACM竞赛中最基础也是最重要的数据结构之一，其核心要点包括：

1. **LIFO特性**：后进先出，所有操作都在栈顶进行
2. **简洁接口**：`push()`、`pop()`、`top()`三个核心操作
3. **底层实现**：默认使用`deque`，也可以指定`vector`或`list`
4. **典型应用**：括号匹配、表达式求值、DFS搜索、单调栈问题

掌握栈的使用对于解决许多经典算法问题至关重要。单调栈是解决"下一个更大/更小元素"问题的利器，DFS可以解决几乎所有可以建模为状态空间搜索的问题。