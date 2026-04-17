# priority_queue 容器详解

`<queue>`头文件还实现了C++标准库中的优先队列容器适配器`std::priority_queue`。优先队列是一种特殊的队列，其中每个元素都有一个优先级，优先级最高的元素最先被移除。默认情况下，优先队列是一个最大堆（max-heap），即最大的元素具有最高的优先级。在ACM竞赛中，优先队列是解决Dijkstra最短路径、Huffman编码合并、K路归并等问题的核心工具。

## 1. priority_queue类概述

`std::priority_queue`是一个容器适配器，它提供了优先队列的标准接口。与普通队列的FIFO不同，优先队列根据元素的优先级来确定出队顺序——优先级最高的元素最先被移除。在内部实现上，`priority_queue`默认使用`vector`作为底层容器，并借助堆算法（通常是二叉堆）来维护元素的优先级顺序。

`priority_queue`的核心特性包括：默认情况下实现最大堆行为、可以通过比较函数改变优先级规则、提供O(log n)时间复杂度的插入和删除操作、以及O(1)时间复杂度的访问堆顶元素。

### 1.1 头文件包含方式

```cpp
#include <queue>           // 标准方式
#include <bits/stdc++.h>   // 竞赛常用
using namespace std;       // 使用std命名空间
```

### 1.2 默认底层容器

```cpp
// 默认使用vector作为底层容器，less<T>作为比较函数（最大堆）
priority_queue<int> pq1;   // 最大堆

// 指定自定义比较函数
priority_queue<int, vector<int>, greater<int>> pq2;  // 最小堆

// 使用list作为底层容器（不推荐，效率低）
priority_queue<int, list<int>> pq3;

// 使用deque作为底层容器
priority_queue<int, deque<int>> pq4;
```

## 2. 基本操作

### 2.1 push() - 入队操作

```cpp
priority_queue<int> pq;

// push() - 将元素插入优先队列
pq.push(3);                       // 堆: [3]
pq.push(1);                       // 堆: [3, 1]
pq.push(4);                       // 堆: [4, 1, 3]
pq.push(2);                       // 堆: [4, 2, 3, 1]
pq.push(5);                       // 堆: [5, 4, 3, 1, 2]

// 复杂度: O(log n)
```

### 2.2 pop() - 出队操作

```cpp
priority_queue<int> pq = {5, 4, 3, 2, 1};

// pop() - 移除堆顶元素（优先级最高的元素）
pq.pop();                         // 移除5，堆: [4, 2, 3, 1]
pq.pop();                         // 移除4，堆: [3, 2, 1]

// 注意: pop()不返回被删除的元素
// 错误写法: int x = pq.pop();
// 正确写法: int x = pq.top(); pq.pop();

// 对空优先队列调用pop()是未定义行为
if (!pq.empty()) {
    pq.pop();
}
```

### 2.3 top() - 访问堆顶元素

```cpp
priority_queue<int> pq = {5, 4, 3, 2, 1};

// top() - 返回堆顶元素（优先级最高的元素）
int x = pq.top();                 // x = 5（最大值）

// 不可以修改堆顶元素（top()返回const引用）
// pq.top() = 10;  // 编译错误！

// 对空优先队列调用top()是未定义行为
if (!pq.empty()) {
    cout << pq.top() << endl;
}
```

### 2.4 empty()和size()

```cpp
priority_queue<int> pq;

// empty() - 判断是否为空
bool is_empty = pq.empty();       // true

// size() - 返回元素数量
size_t sz = pq.size();            // 0

pq.push(1);
pq.push(2);

is_empty = pq.empty();            // false
sz = pq.size();                   // 2
```

## 3. 构造函数与初始化

### 3.1 默认构造

```cpp
// 创建空优先队列
priority_queue<int> pq1;          // 空优先队列（最大堆）
priority_queue<int, vector<int>, greater<int>> pq2;  // 最小堆
priority_queue<string> pq3;
priority_queue<vector<int>> pq4;
```

### 3.2 拷贝构造与移动构造

```cpp
// 拷贝构造 - 创建优先队列的副本
priority_queue<int> original;
original.push(1);
original.push(2);
original.push(3);

priority_queue<int> copy1(original);   // 完整的深拷贝

// 移动构造 - 高效转移资源（C++11）
priority_queue<int> moved = std::move(original);  // original变为空
```

### 3.3 从容器构造

```cpp
// 可以从已排序的容器构造优先队列
vector<int> v = {5, 4, 3, 2, 1};
priority_queue<int> pq(v.begin(), v.end());  // 从vector构造

deque<int> d = {1, 2, 3};
priority_queue<int> pq2(d.begin(), d.end()); // 从deque构造

// 注意：构造时不会自动排序
// 元素按照传入顺序插入堆中
```

### 3.4 使用比较函数

```cpp
// 最小堆（使用greater）
priority_queue<int, vector<int>, greater<int>> min_heap;

// 自定义比较函数
auto cmp = [](int a, int b) {
    return a > b;  // 最小堆
};
priority_queue<int, vector<int>, decltype(cmp)> custom_heap(cmp);

// 最大堆的默认比较函数是 less<T>
// priority_queue<T, Container, less<T>>
// top() 返回最大的元素

// 最小堆的比较函数是 greater<T>
// priority_queue<T, Container, greater<T>>
// top() 返回最小的元素
```

## 4. 自定义类型与比较

### 4.1 使用自定义结构体

```cpp
struct Task {
    int priority;
    string name;
    
    Task(int p, const string& n) : priority(p), name(n) {}
};

// 方法1：重载operator<（默认比较使用这个）
struct Task1 {
    int priority;
    string name;
    
    Task1(int p, const string& n) : priority(p), name(n) {}
    
    bool operator<(const Task1& other) const {
        // 反转比较，使priority大的优先级高（默认最大堆）
        return priority < other.priority;
    }
};

priority_queue<Task1> pq1;
pq1.push(Task1(3, "task3"));
pq1.push(Task1(1, "task1"));
pq1.push(Task1(2, "task2"));
cout << pq1.top().name << endl;   // 输出 "task3"

// 方法2：使用比较结构体
struct Task2 {
    int priority;
    string name;
};

struct CompareTask {
    bool operator()(const Task2& a, const Task2& b) {
        // 返回true表示a的优先级低于b（std::priority_queue使用max-heap时）
        // priority_queue使用std::less，比较函数返回true时将a放在b之后
        return a.priority < b.priority;  // priority越大，优先级越高
    }
};

priority_queue<Task2, vector<Task2>, CompareTask> pq2;
pq2.push(Task2{3, "task3"});
pq2.push(Task2{1, "task1"});
pq2.push(Task2{2, "task2"});
cout << pq2.top().name << endl;   // 输出 "task3"
```

### 4.2 Lambda比较函数（C++14+）

```cpp
struct Edge {
    int to;
    int weight;
};

auto cmp = [](const Edge& a, const Edge& b) {
    return a.weight > b.weight;  // 权重越小优先级越高
};

priority_queue<Edge, vector<Edge>, decltype(cmp)> pq(cmp);
pq.push({1, 5});
pq.push({2, 2});
pq.push({3, 8});
cout << pq.top().to << endl;      // 输出 2（权重最小的）
```

### 4.3 比较函数详解

```cpp
// priority_queue使用的比较函数规则：
// 如果使用 less<T>（默认）：
//   - top() 返回最大的元素
//   - 比较函数返回 a < b 时，a排在b后面

// 如果使用 greater<T>：
//   - top() 返回最小的元素
//   - 比较函数返回 a > b 时，a排在b后面

// 自定义比较函数的返回值含义：
// 返回true：a的优先级低于b，a应该在b之后被取出
// 返回false：a的优先级不低于b，a应该在b之前或同时被取出

// 常见的比较函数模式：

// 最大堆（默认）
struct MaxHeap {
    bool operator()(int a, int b) {
        return a < b;  // a < b 时返回true，表示a优先级低
    }
};

// 最小堆
struct MinHeap {
    bool operator()(int a, int b) {
        return a > b;  // a > b 时返回true，表示a优先级低
    }
};

// 自定义最大堆
struct MaxCompare {
    bool operator()(const Node& a, const Node& b) {
        return a.value < b.value;  // value越小，优先级越低
    }
};

// 自定义最小堆
struct MinCompare {
    bool operator()(const Node& a, const Node& b) {
        return a.value > b.value;  // value越大，优先级越低
    }
};
```

## 5. 竞赛中的常见应用

### 5.1 Dijkstra最短路径算法

```cpp
#include <queue>
#include <vector>
#include <limits>

struct Edge {
    int to;
    int weight;
};

vector<int> dijkstra(int n, int start, 
                    const vector<vector<Edge>>& graph) {
    const int INF = numeric_limits<int>::max();
    vector<int> dist(n, INF);
    priority_queue<pair<int,int>, vector<pair<int,int>>, 
                   greater<pair<int,int>>> pq;  // 最小堆：{dist, node}
    
    dist[start] = 0;
    pq.push({0, start});
    
    while (!pq.empty()) {
        auto [d, u] = pq.top();
        pq.pop();
        
        if (d != dist[u]) continue;  // 已有更短路径
        
        for (const auto& edge : graph[u]) {
            int v = edge.to;
            int new_dist = d + edge.weight;
            
            if (new_dist < dist[v]) {
                dist[v] = new_dist;
                pq.push({new_dist, v});
            }
        }
    }
    
    return dist;
}

// 多目标Dijkstra（带路径重建）
struct Result {
    vector<int> dist;
    vector<int> parent;
};

Result dijkstraWithPath(int n, int start,
                       const vector<vector<Edge>>& graph) {
    const int INF = numeric_limits<int>::max();
    vector<int> dist(n, INF);
    vector<int> parent(n, -1);
    
    using State = pair<int,int>;  // {dist, node}
    priority_queue<State, vector<State>, greater<State>> pq;
    
    dist[start] = 0;
    pq.push({0, start});
    
    while (!pq.empty()) {
        auto [d, u] = pq.top();
        pq.pop();
        
        if (d != dist[u]) continue;
        
        for (const auto& edge : graph[u]) {
            int v = edge.to;
            int new_dist = d + edge.weight;
            
            if (new_dist < dist[v]) {
                dist[v] = new_dist;
                parent[v] = u;
                pq.push({new_dist, v});
            }
        }
    }
    
    return {dist, parent};
}

// 重建路径
vector<int> reconstructPath(const vector<int>& parent, 
                           int start, int end) {
    vector<int> path;
    if (parent[end] == -1 && end != start) return path;
    
    int current = end;
    while (current != -1) {
        path.push_back(current);
        current = parent[current];
    }
    reverse(path.begin(), path.end());
    return path;
}
```

### 5.2 K路归并（合并K个有序数组）

```cpp
#include <queue>
#include <vector>
#include <tuple>

vector<int> mergeKArrays(const vector<vector<int>>& arrays) {
    using Node = tuple<int, int, int>;  // {value, array_index, element_index}
    
    // 最小堆：值最小的先出
    priority_queue<Node, vector<Node>, greater<Node>> pq;
    
    // 初始化：将每个数组的第一个元素放入堆
    for (int i = 0; i < arrays.size(); ++i) {
        if (!arrays[i].empty()) {
            pq.emplace(arrays[i][0], i, 0);
        }
    }
    
    vector<int> result;
    
    while (!pq.empty()) {
        auto [val, arr_idx, elem_idx] = pq.top();
        pq.pop();
        
        result.push_back(val);
        
        // 下一个元素
        if (elem_idx + 1 < arrays[arr_idx].size()) {
            pq.emplace(arrays[arr_idx][elem_idx + 1], arr_idx, elem_idx + 1);
        }
    }
    
    return result;
}

// 优化版：支持不等的数组大小
vector<int> mergeKLarger(const vector<vector<int>>& arrays) {
    using Node = pair<int, pair<int, int>>;  // {value, {array_idx, elem_idx}}
    
    struct Compare {
        bool operator()(const Node& a, const Node& b) {
            return a.first > b.first;  // 最小堆
        }
    };
    
    priority_queue<Node, vector<Node>, Compare> pq;
    
    for (int i = 0; i < arrays.size(); ++i) {
        if (!arrays[i].empty()) {
            pq.push({arrays[i][0], {i, 0}});
        }
    }
    
    vector<int> result;
    
    while (!pq.empty()) {
        auto [val, indices] = pq.top();
        pq.pop();
        auto [arr_idx, elem_idx] = indices;
        
        result.push_back(val);
        
        if (elem_idx + 1 < arrays[arr_idx].size()) {
            pq.push({arrays[arr_idx][elem_idx + 1], {arr_idx, elem_idx + 1}});
        }
    }
    
    return result;
}
```

### 5.3 Huffman编码（合并果子）

```cpp
#include <queue>
#include <vector>

// 经典问题：合并果子，每次合并的代价是两堆果子之和
long long mergeFruits(vector<int>& fruits) {
    if (fruits.size() <= 1) return 0;
    
    // 最小堆
    priority_queue<int, vector<int>, greater<int>> pq;
    for (int f : fruits) {
        pq.push(f);
    }
    
    long long total_cost = 0;
    
    while (pq.size() > 1) {
        int a = pq.top(); pq.pop();
        int b = pq.top(); pq.pop();
        int cost = a + b;
        total_cost += cost;
        pq.push(cost);
    }
    
    return total_cost;
}

// Huffman树构建
struct HuffmanNode {
    int weight;
    char symbol;
    HuffmanNode *left, *right;
    bool is_leaf;
    
    HuffmanNode(char c, int w) : symbol(c), weight(w), 
                                left(nullptr), right(nullptr), is_leaf(true) {}
    
    HuffmanNode(HuffmanNode* l, HuffmanNode* r) 
        : symbol(0), weight(l->weight + r->weight),
          left(l), right(r), is_leaf(false) {}
};

struct CompareHuffman {
    bool operator()(HuffmanNode* a, HuffmanNode* b) {
        return a->weight > b->weight;  // 最小堆：权重小的先出
    }
};

HuffmanNode* buildHuffmanTree(const vector<pair<char,int>>& symbols) {
    priority_queue<HuffmanNode*, vector<HuffmanNode*>, CompareHuffman> pq;
    
    for (const auto& [c, w] : symbols) {
        pq.push(new HuffmanNode(c, w));
    }
    
    while (pq.size() > 1) {
        HuffmanNode* left = pq.top(); pq.pop();
        HuffmanNode* right = pq.top(); pq.pop();
        pq.push(new HuffmanNode(left, right));
    }
    
    return pq.top();
}
```

### 5.4 滑动窗口最大值（高级版）

```cpp
#include <queue>
#include <deque>
#include <vector>

// 使用优先队列的版本（O(n log k)）
vector<int> maxSlidingWindowPQ(const vector<int>& nums, int k) {
    if (nums.empty() || k <= 0) return {};
    
    priority_queue<pair<int,int>> pq;  // {value, index}
    vector<int> result;
    
    for (int i = 0; i < nums.size(); ++i) {
        // 移除窗口外的元素
        while (!pq.empty() && pq.top().second <= i - k) {
            pq.pop();
        }
        
        pq.push({nums[i], i});
        
        if (i >= k - 1) {
            result.push_back(pq.top().first);
        }
    }
    
    return result;
}

// 使用单调双端队列的版本（O(n)）- 更高效
vector<int> maxSlidingWindow(const vector<int>& nums, int k) {
    if (nums.empty() || k <= 0) return {};
    
    deque<int> dq;  // 存储索引
    vector<int> result;
    
    for (int i = 0; i < nums.size(); ++i) {
        // 移除窗口外的元素
        while (!dq.empty() && dq.front() <= i - k) {
            dq.pop_front();
        }
        
        // 维护递减队列
        while (!dq.empty() && nums[dq.back()] <= nums[i]) {
            dq.pop_back();
        }
        
        dq.push_back(i);
        
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    
    return result;
}
```

### 5.5 Top-K问题

```cpp
#include <queue>
#include <vector>
#include <algorithm>

// 方法1：部分排序
vector<int> topK(const vector<int>& nums, int k) {
    partial_sort(nums.begin(), nums.begin() + k, nums.end(),
                 greater<int>());
    return vector<int>(nums.begin(), nums.begin() + k);
}

// 方法2：使用优先队列（大数据集）
vector<int> topKLarge(const vector<int>& nums, int k) {
    if (k >= nums.size()) {
        vector<int> result = nums;
        sort(result.begin(), result.end(), greater<int>());
        return result;
    }
    
    // 最小堆，保持k个最大的元素
    priority_queue<int, vector<int>, greater<int>> pq;
    
    for (int num : nums) {
        if (pq.size() < k) {
            pq.push(num);
        } else if (num > pq.top()) {
            pq.pop();
            pq.push(num);
        }
    }
    
    vector<int> result(pq.size());
    int i = pq.size() - 1;
    while (!pq.empty()) {
        result[i--] = pq.top();
        pq.pop();
    }
    
    return result;
}

// Top-K频繁元素
vector<int> topKFrequent(const vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    for (int num : nums) {
        freq[num]++;
    }
    
    // 最小堆：频率最小的先出
    using P = pair<int, int>;  // {frequency, element}
    priority_queue<P, vector<P>, greater<P>> pq;
    
    for (const auto& [num, count] : freq) {
        if (pq.size() < k) {
            pq.push({count, num});
        } else if (count > pq.top().first) {
            pq.pop();
            pq.push({count, num});
        }
    }
    
    vector<int> result;
    while (!pq.empty()) {
        result.push_back(pq.top().second);
        pq.pop();
    }
    
    return result;
}
```

### 5.6 A*搜索算法

```cpp
#include <queue>
#include <vector>
#include <cmath>

struct AStarNode {
    int x, y;
    int g;  // 从起点到当前点的代价
    int h;  // 从当前点到终点的启发式估计
    int f;  // g + h
    int parent_x, parent_y;
    
    AStarNode(int x, int y, int g, int h, int px, int py)
        : x(x), y(y), g(g), h(h), f(g + h), 
          parent_x(px), parent_y(py) {}
};

struct CompareAStar {
    bool operator()(const AStarNode& a, const AStarNode& b) {
        return a.f > b.f;  // f值小的优先级高（最小堆）
    }
};

int heuristic(int x1, int y1, int x2, int y2) {
    // 曼哈顿距离
    return abs(x1 - x2) + abs(y1 - y2);
}

vector<pair<int,int>> aStarSearch(
    const vector<vector<int>>& grid,
    pair<int,int> start,
    pair<int,int> goal) {
    
    int m = grid.size(), n = grid[0].size();
    vector<vector<int>> g_score(m, vector<int>(n, INT_MAX));
    priority_queue<AStarNode, vector<AStarNode>, CompareAStar> pq;
    
    g_score[start.first][start.second] = 0;
    pq.emplace(start.first, start.second, 0, 
               heuristic(start.first, start.second, goal.first, goal.second),
               -1, -1);
    
    const int dx[] = {0, 0, 1, -1};
    const int dy[] = {1, -1, 0, 0};
    
    while (!pq.empty()) {
        AStarNode current = pq.top();
        pq.pop();
        
        if (current.x == goal.first && current.y == goal.second) {
            // 重建路径
            vector<pair<int,int>> path;
            int x = current.x, y = current.y;
            while (x != -1) {
                path.push_back({x, y});
                int px = current.parent_x, py = current.parent_y;
                // 需要保存上一状态，这里简化处理
                x = px; y = py;
            }
            reverse(path.begin(), path.end());
            return path;
        }
        
        for (int dir = 0; dir < 4; ++dir) {
            int nx = current.x + dx[dir];
            int ny = current.y + dy[dir];
            
            if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
            if (grid[nx][ny] == 1) continue;  // 障碍物
            
            int tentative_g = current.g + 1;
            
            if (tentative_g < g_score[nx][ny]) {
                g_score[nx][ny] = tentative_g;
                int h = heuristic(nx, ny, goal.first, goal.second);
                pq.emplace(nx, ny, tentative_g, h, current.x, current.y);
            }
        }
    }
    
    return {};  // 无法到达
}
```

### 5.7 模拟服务器请求（带优先级）

```cpp
#include <queue>
#include <vector>
#include <chrono>

struct Request {
    int id;
    int priority;  // 数值越大优先级越高
    long long arrival_time;
    long long process_time;
    
    Request(int i, int p, long long arr, long long proc)
        : id(i), priority(p), arrival_time(arr), process_time(proc) {}
};

struct CompareRequest {
    bool operator()(const Request& a, const Request& b) {
        // 高优先级先处理
        return a.priority < b.priority;  // priority大的先出
    }
};

struct CompareRequestMin {
    bool operator()(const Request& a, const Request& b) {
        // 最小堆：优先级数值小的先处理（用于紧急任务）
        return a.priority > b.priority;
    }
};

// 模拟服务器调度
void simulateServer(const vector<Request>& requests) {
    priority_queue<Request, vector<Request>, CompareRequest> pq;
    
    long long current_time = 0;
    size_t idx = 0;
    
    while (!pq.empty() || idx < requests.size()) {
        // 添加新到达的请求
        while (idx < requests.size() && 
               requests[idx].arrival_time <= current_time) {
            pq.push(requests[idx]);
            ++idx;
        }
        
        if (!pq.empty()) {
            Request r = pq.top();
            pq.pop();
            cout << "时间 " << current_time 
                 << ": 处理请求 " << r.id 
                 << " (优先级=" << r.priority << ")" << endl;
            current_time += r.process_time;
        } else {
            // 空闲时间快进
            current_time = requests[idx].arrival_time;
        }
    }
}
```

### 5.8 最大利润调度（活动选择问题）

```cpp
#include <queue>
#include <vector>
#include <algorithm>

struct Activity {
    int start, end, profit;
    Activity(int s, int e, int p) : start(s), end(e), profit(p) {}
};

struct CompareEndTime {
    bool operator()(const Activity* a, const Activity* b) {
        return a->end > b->end;  // 最早结束时间先处理
    }
};

// 加权活动选择（动态规划 + 二分查找）
int weightedActivitySelection(vector<Activity>& activities) {
    sort(activities.begin(), activities.end(),
         [](const Activity& a, const Activity& b) {
             return a.end < b.end;
         });
    
    int n = activities.size();
    vector<int> dp(n);
    
    // 计算p(i)：不与i冲突的最后一个活动
    vector<int> p(n);
    for (int i = 0; i < n; ++i) {
        p[i] = -1;
        for (int j = i - 1; j >= 0; --j) {
            if (activities[j].end <= activities[i].start) {
                p[i] = j;
                break;
            }
        }
    }
    
    dp[0] = activities[0].profit;
    for (int i = 1; i < n; ++i) {
        dp[i] = max(activities[i].profit + 
                   (p[i] == -1 ? 0 : dp[p[i]]), 
                   dp[i - 1]);
    }
    
    return dp[n - 1];
}

// 最小堆选择k个不重叠区间
vector<Activity*> selectKActivities(
    vector<Activity*>& activities, int k) {
    
    priority_queue<Activity*, vector<Activity*>, 
                   CompareEndTime> pq;
    
    for (auto a : activities) {
        pq.push(a);
    }
    
    vector<Activity*> selected;
    int last_end = -1;
    
    while (!pq.empty() && selected.size() < k) {
        Activity* a = pq.top();
        pq.pop();
        
        if (a->start >= last_end) {
            selected.push_back(a);
            last_end = a->end;
        }
    }
    
    return selected;
}
```

## 6. 自定义优先队列实现

```cpp
template<typename T, typename Container = vector<T>,
         typename Compare = less<typename Container::value_type>>
class SimplePriorityQueue {
private:
    Container data;
    Compare comp;
    
    void heapify(int i) {
        int n = data.size();
        int largest = i;
        int left = 2 * i + 1;
        int right = 2 * i + 2;
        
        if (left < n && comp(data[largest], data[left])) {
            largest = left;
        }
        
        if (right < n && comp(data[largest], data[right])) {
            largest = right;
        }
        
        if (largest != i) {
            swap(data[i], data[largest]);
            heapify(largest);
        }
    }
    
public:
    SimplePriorityQueue() = default;
    
    explicit SimplePriorityQueue(const Compare& c) : comp(c) {}
    
    SimplePriorityQueue(const Container& c, const Compare& comp = Compare())
        : data(c), comp(comp) {
        make_heap(data.begin(), data.end(), comp);
    }
    
    bool empty() const {
        return data.empty();
    }
    
    size_t size() const {
        return data.size();
    }
    
    void push(const T& x) {
        data.push_back(x);
        push_heap(data.begin(), data.end(), comp);
    }
    
    void pop() {
        if (empty()) return;
        pop_heap(data.begin(), data.end(), comp);
        data.pop_back();
    }
    
    const T& top() const {
        return data.front();
    }
    
    void swap(SimplePriorityQueue& other) {
        swap(data, other.data);
        swap(comp, other.comp);
    }
};
```

## 7. 常见陷阱与注意事项

### 7.1 pop()不返回值

```cpp
priority_queue<int> pq = {1, 2, 3};

// 错误写法
int x = pq.pop();           // 编译错误！pop()返回void

// 正确写法
int x = pq.top();
pq.pop();
```

### 7.2 空队列操作

```cpp
priority_queue<int> pq;

// 以下操作都是未定义行为
// int x = pq.top();       // 空队列访问堆顶
// pq.pop();               // 空队列弹出

// 正确做法
if (!pq.empty()) {
    int x = pq.top();
    pq.pop();
}
```

### 7.3 迭代器问题

```cpp
priority_queue<int> pq = {1, 2, 3};

// priority_queue不提供迭代器！
// 不能使用范围for循环遍历priority_queue
// for (int x : pq) { ... }  // 编译错误！

// 正确做法：通过top()和pop()按顺序访问
while (!pq.empty()) {
    cout << pq.top() << " ";
    pq.pop();
}
```

### 7.4 不能比较

```cpp
priority_queue<int> pq1 = {1, 2, 3};
priority_queue<int> pq2 = {1, 2, 3};

// priority_queue不支持比较运算符
// if (pq1 == pq2) { ... }     // 编译错误！
```

### 7.5 修改内部元素

```cpp
struct Node {
    int priority;
    int id;
};

struct Compare {
    bool operator()(const Node& a, const Node& b) {
        return a.priority < b.priority;
    }
};

priority_queue<Node, vector<Node>, Compare> pq;

// 警告：可以修改top()返回的元素，但这可能破坏堆结构！
Node& top = pq.top();
top.priority = 100;  // 危险！堆性质可能被破坏

// 正确做法：先pop()，修改后再push()
Node n = pq.top();
pq.pop();
n.priority = 100;
pq.push(n);
```

### 7.6 比较函数选择

```cpp
// 默认是最大堆（less<T>）
priority_queue<int> pq_max;  // 最大元素在top
pq_max.push(3);
pq_max.push(1);
pq_max.push(2);
cout << pq_max.top() << endl;  // 输出 3

// 使用greater<T>变成最小堆
priority_queue<int, vector<int>, greater<int>> pq_min;
pq_min.push(3);
pq_min.push(1);
pq_min.push(2);
cout << pq_min.top() << endl;  // 输出 1

// 自定义比较函数
auto cmp = [](int a, int b) {
    return a % 10 > b % 10;  // 个位数小的优先级高
};
priority_queue<int, vector<int>, decltype(cmp)> pq_mod(cmp);
pq_mod.push(15);
pq_mod.push(23);
pq_mod.push(7);
cout << pq_mod.top() << endl;  // 输出 7（个位数最小）
```

## 8. 复杂度分析

| 操作 | 时间复杂度 | 说明 |
|------|------------|------|
| `push()` | O(log n) | 插入并维护堆 |
| `pop()` | O(log n) | 删除堆顶并维护堆 |
| `top()` | O(1) | 访问堆顶 |
| `empty()` | O(1) | 判断空 |
| `size()` | O(1) | 获取大小 |
| `swap()` | O(1) | 交换内容 |

## 9. 完整竞赛模板

```cpp
#include <bits/stdc++.h>
using namespace std;

// 优先队列通用模板
class PriorityQueueTemplate {
public:
    // 1. 最大堆（默认）
    template<typename T>
    static priority_queue<T> maxHeap() {
        return priority_queue<T>();
    }
    
    // 2. 最小堆
    template<typename T>
    static priority_queue<T, vector<T>, greater<T>> minHeap() {
        return priority_queue<T, vector<T>, greater<T>>();
    }
    
    // 3. 自定义比较的堆
    template<typename T, typename Compare>
    static priority_queue<T, vector<T>, Compare> customHeap(Compare comp) {
        return priority_queue<T, vector<T>, Compare>(comp);
    }
    
    // 4. Dijkstra算法
    template<typename T>
    static vector<T> dijkstra(
        int n, int start,
        const vector<vector<pair<int,T>>>& graph) {
        
        const T INF = numeric_limits<T>::max();
        vector<T> dist(n, INF);
        priority_queue<pair<T,int>, vector<pair<T,int>>, 
                       greater<pair<T,int>>> pq;
        
        dist[start] = 0;
        pq.push({0, start});
        
        while (!pq.empty()) {
            auto [d, u] = pq.top();
            pq.pop();
            
            if (d != dist[u]) continue;
            
            for (auto [v, w] : graph[u]) {
                if (dist[u] + w < dist[v]) {
                    dist[v] = dist[u] + w;
                    pq.push({dist[v], v});
                }
            }
        }
        
        return dist;
    }
    
    // 5. Top-K问题
    template<typename T>
    static vector<T> topK(const vector<T>& nums, int k) {
        if (k >= nums.size()) {
            vector<T> result = nums;
            sort(result.begin(), result.end(), greater<T>());
            return result;
        }
        
        priority_queue<T, vector<T>, greater<T>> pq;
        for (const auto& num : nums) {
            if (pq.size() < k) {
                pq.push(num);
            } else if (num > pq.top()) {
                pq.pop();
                pq.push(num);
            }
        }
        
        vector<T> result(pq.size());
        for (int i = pq.size() - 1; i >= 0; --i) {
            result[i] = pq.top();
            pq.pop();
        }
        return result;
    }
    
    // 6. 合并果子（哈夫曼树）
    template<typename T>
    static T mergeCost(vector<T> items) {
        priority_queue<T, vector<T>, greater<T>> pq;
        for (auto item : items) pq.push(item);
        
        T total = 0;
        while (pq.size() > 1) {
            T a = pq.top(); pq.pop();
            T b = pq.top(); pq.pop();
            T cost = a + b;
            total += cost;
            pq.push(cost);
        }
        return total;
    }
};
```

## 10. 总结

`std::priority_queue`是ACM竞赛中最重要的数据结构之一，其核心要点包括：

1. **堆特性**：默认实现最大堆，可以通过比较函数实现最小堆
2. **核心操作**：`push()`（O(log n)）、`pop()`（O(log n)）、`top()`（O(1)）
3. **典型应用**：Dijkstra最短路径、Top-K问题、K路归并、Huffman编码
4. **自定义类型**：通过重载`operator<`或提供比较函数结构体

掌握优先队列的使用对于解决各种优化问题和图论算法至关重要。Dijkstra算法是图论中最经典的单源最短路径算法，而Top-K问题则广泛应用于数据处理和机器学习场景。