# queue 容器适配器详解

`<queue>`头文件实现了C++标准库中的队列容器适配器`std::queue`。队列是一种先进先出（FIFO, First In First Out）的数据结构，只允许在序列的一端插入元素，在另一端删除元素。`queue`不是独立的容器，而是基于其他容器实现的适配器，默认情况下使用`deque`作为底层容器。在ACM竞赛中，队列是解决广度优先搜索、层次遍历、生产者消费者等问题的基本工具。

## 1. queue类概述

`std::queue`是一个容器适配器，它提供了队列的标准接口，但将具体实现委托给底层的顺序容器。默认情况下，`queue`使用`deque`作为底层容器，但也可以通过模板参数指定使用`list`作为底层容器（不推荐使用`vector`，因为`vector`不支持`pop_front()`的高效实现）。队列的核心操作包括`push`（入队）、`pop`（出队）和`front`（访问队首元素）、`back`（访问队尾元素）。

队列的FIFO特性决定了其应用场景：先到达的元素先被处理。这使得队列成为模拟排队过程、实现广度优先搜索和处理按顺序到达的数据的理想工具。

### 1.1 头文件包含方式

```cpp
#include <queue>           // 标准方式
#include <bits/stdc++.h>   // 竞赛常用
using namespace std;       // 使用std命名空间
```

### 1.2 默认底层容器

```cpp
// 默认使用deque作为底层容器
queue<int> q1;             // deque<int>作为底层容器

// 指定list作为底层容器
queue<int, list<int>> q2;  // list<int>作为底层容器

// 不能使用vector作为queue的底层容器！
// queue<int, vector<int>> q3;  // 编译错误！
// vector不支持高效的pop_front()操作
```

**选择底层容器的考虑**：

- **deque（默认）**：在两端插入删除都高效，内存管理自动
- **list**：如果需要稳定的迭代器或双向遍历，但一般不需要

## 2. 基本操作

### 2.1 push() - 入队操作

```cpp
queue<int> q;

// push() - 将元素添加到队尾
q.push(1);                       // 队列: [1]（队首 ← 1 ← 队尾）
q.push(2);                       // 队列: [1, 2]
q.push(3);                       // 队列: [1, 2, 3]

// 复杂度: O(1)
```

### 2.2 pop() - 出队操作

```cpp
queue<int> q = {1, 2, 3};

// pop() - 移除队首元素（不返回）
q.pop();                         // 队列变为 [2, 3]
q.pop();                         // 队列变为 [3]

// 注意: pop()不返回被删除的元素
// 错误写法: int x = q.pop();
// 正确写法: int x = q.front(); q.pop();

// 对空队列调用pop()是未定义行为
if (!q.empty()) {
    q.pop();
}
```

### 2.3 front() - 访问队首元素

```cpp
queue<int> q = {1, 2, 3};

// front() - 返回队首元素的引用
int x = q.front();               // x = 1

// 可以修改队首元素
q.front() = 10;                  // 队列变为 [10, 2, 3]

// 对空队列调用front()是未定义行为
if (!q.empty()) {
    cout << q.front() << endl;
}
```

### 2.4 back() - 访问队尾元素

```cpp
queue<int> q = {1, 2, 3};

// back() - 返回队尾元素的引用
int x = q.back();                // x = 3

// 可以修改队尾元素
q.back() = 30;                   // 队列变为 [1, 2, 30]

// 对空队列调用back()是未定义行为
if (!q.empty()) {
    cout << q.back() << endl;
}
```

### 2.5 empty()和size()

```cpp
queue<int> q;

// empty() - 判断队列是否为空
bool is_empty = q.empty();       // true（空队列）

// size() - 返回队列中元素数量
size_t sz = q.size();            // 0

q.push(1);
q.push(2);

is_empty = q.empty();            // false
sz = q.size();                   // 2

// 推荐使用empty()而非size() == 0（更清晰，语义更明确）
```

## 3. 构造函数与初始化

### 3.1 默认构造

```cpp
// 创建空队列
queue<int> q1;                   // 空队列

queue<string> q2;
queue<vector<int>> q3;
```

### 3.2 拷贝构造与移动构造

```cpp
// 拷贝构造 - 创建队列的副本
queue<int> original;
original.push(1);
original.push(2);
original.push(3);

queue<int> copy1(original);      // 完整的深拷贝

// 移动构造 - 高效转移资源（C++11）
queue<int> moved = std::move(original);  // original变为空，moved获得资源
```

### 3.3 从容器构造（C++11）

```cpp
// 可以从支持front()和back()的容器构造queue
deque<int> d = {1, 2, 3, 4, 5};
queue<int> q1(d);                // 从deque构造

list<int> l = {1, 2, 3};
queue<int, list<int>> q2(l);     // 从list构造

// 注意: 构造时按容器顺序入队
// q1 队首是 1，队尾是 5
```

### 3.4 初始化列表构造

```cpp
// queue没有直接的initializer_list构造函数
// 需要逐个push
queue<int> q;
q.push(1);
q.push(2);
q.push(3);
// 或者使用容器作为中介
deque<int> init = {1, 2, 3};
queue<int> q2(init);
```

## 4. swap()操作

```cpp
queue<int> q1 = {1, 2, 3};
queue<int> q2 = {4, 5, 6};

// swap() - 交换两个队列的内容
q1.swap(q2);                     // q1 = {4, 5, 6}, q2 = {1, 2, 3}

// 非成员函数版
swap(q1, q2);                    // 同样交换内容

// 快速清空队列
queue<int> temp;
q1.swap(temp);                   // q1变为空
```

## 5. 竞赛中的常见应用

### 5.1 广度优先搜索（BFS）

```cpp
#include <queue>
#include <vector>
#include <utility>

struct Graph {
    int n;
    vector<vector<int>> adj;
    Graph(int n) : n(n), adj(n) {}
    
    void addEdge(int u, int v) {
        adj[u].push_back(v);
    }
};

// BFS单源最短路径
vector<int> bfsShortestPath(const Graph& g, int start, int end) {
    int n = g.n;
    vector<int> dist(n, -1);
    vector<int> parent(n, -1);
    queue<int> q;
    
    q.push(start);
    dist[start] = 0;
    
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        
        if (u == end) break;
        
        for (int v : g.adj[u]) {
            if (dist[v] == -1) {
                dist[v] = dist[u] + 1;
                parent[v] = u;
                q.push(v);
            }
        }
    }
    
    // 重建路径
    vector<int> path;
    if (dist[end] == -1) return path;  // 无法到达
    
    int current = end;
    while (current != -1) {
        path.push_back(current);
        current = parent[current];
    }
    reverse(path.begin(), path.end());
    
    return path;
}

// 使用示例
int main() {
    Graph g(6);
    g.addEdge(0, 1);
    g.addEdge(0, 2);
    g.addEdge(1, 3);
    g.addEdge(1, 4);
    g.addEdge(2, 5);
    
    auto path = bfsShortestPath(g, 0, 4);
    for (int v : path) {
        cout << v << " ";
    }
    cout << endl;
    return 0;
}
```

### 5.2 树的层次遍历

```cpp
#include <queue>
#include <vector>

struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

// 层次遍历
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> result;
    if (!root) return result;
    
    queue<TreeNode*> q;
    q.push(root);
    
    while (!q.empty()) {
        int level_size = q.size();
        vector<int> level;
        
        for (int i = 0; i < level_size; ++i) {
            TreeNode* node = q.front();
            q.pop();
            level.push_back(node->val);
            
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        
        result.push_back(level);
    }
    
    return result;
}

// 之字形层次遍历（锯齿形遍历）
vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
    vector<vector<int>> result;
    if (!root) return result;
    
    queue<TreeNode*> q;
    q.push(root);
    bool leftToRight = true;
    
    while (!q.empty()) {
        int level_size = q.size();
        vector<int> level(level_size);
        
        for (int i = 0; i < level_size; ++i) {
            TreeNode* node = q.front();
            q.pop();
            
            int index = leftToRight ? i : level_size - 1 - i;
            level[index] = node->val;
            
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        
        leftToRight = !leftToRight;
        result.push_back(level);
    }
    
    return result;
}

// 每一层的平均值
vector<double> averageOfLevels(TreeNode* root) {
    vector<double> result;
    if (!root) return result;
    
    queue<TreeNode*> q;
    q.push(root);
    
    while (!q.empty()) {
        int level_size = q.size();
        long long sum = 0;
        
        for (int i = 0; i < level_size; ++i) {
            TreeNode* node = q.front();
            q.pop();
            sum += node->val;
            
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        
        result.push_back(static_cast<double>(sum) / level_size);
    }
    
    return result;
}
```

### 5.3 网格BFS（最短路径）

```cpp
#include <queue>
#include <vector>
#include <tuple>

struct Pos {
    int x, y;
    Pos(int x, int y) : x(x), y(y) {}
};

int shortestPath(vector<vector<int>>& grid, Pos start, Pos end) {
    if (grid.empty() || grid[0].empty()) return -1;
    if (grid[start.x][start.y] == 1 || grid[end.x][end.y] == 1) return -1;
    
    int m = grid.size(), n = grid[0].size();
    vector<vector<int>> dist(m, vector<int>(n, -1));
    
    queue<Pos> q;
    q.push(start);
    dist[start.x][start.y] = 0;
    
    const int dx[] = {0, 0, 1, -1};
    const int dy[] = {1, -1, 0, 0};
    
    while (!q.empty()) {
        Pos cur = q.front();
        q.pop();
        
        if (cur.x == end.x && cur.y == end.y) {
            return dist[cur.x][cur.y];
        }
        
        for (int dir = 0; dir < 4; ++dir) {
            int nx = cur.x + dx[dir];
            int ny = cur.y + dy[dir];
            
            if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
            if (grid[nx][ny] == 1) continue;          // 障碍物
            if (dist[nx][ny] != -1) continue;         // 已访问
            
            dist[nx][ny] = dist[cur.x][cur.y] + 1;
            q.push(Pos(nx, ny));
        }
    }
    
    return -1;  // 无法到达
}

// 带有墙壁破坏的BFS（最多破坏k面墙）
int shortestPathWithKObstacles(vector<vector<int>>& grid, int k) {
    int m = grid.size(), n = grid[0].size();
    vector<vector<vector<int>>> visited(m, 
        vector<vector<int>>(n, vector<int>(k + 1, 0)));
    
    struct State {
        int x, y, remaining_k, dist;
    };
    
    queue<State> q;
    q.push({0, 0, k, 0});
    visited[0][0][k] = 1;
    
    const int dx[] = {0, 0, 1, -1};
    const int dy[] = {1, -1, 0, 0};
    
    while (!q.empty()) {
        auto [x, y, rem_k, dist] = q.front();
        q.pop();
        
        if (x == m - 1 && y == n - 1) return dist;
        
        for (int dir = 0; dir < 4; ++dir) {
            int nx = x + dx[dir];
            int ny = y + dy[dir];
            
            if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
            
            int new_k = rem_k - grid[nx][ny];
            if (new_k < 0) continue;
            if (visited[nx][ny][new_k]) continue;
            
            visited[nx][ny][new_k] = 1;
            q.push({nx, ny, new_k, dist + 1});
        }
    }
    
    return -1;
}
```

### 5.4 拓扑排序（Kahn算法）

```cpp
#include <queue>
#include <vector>

vector<int> topologicalSort(int n, const vector<vector<int>>& edges) {
    vector<vector<int>> adj(n);
    vector<int> in_degree(n, 0);
    
    for (const auto& edge : edges) {
        int u = edge[0], v = edge[1];
        adj[u].push_back(v);
        in_degree[v]++;
    }
    
    queue<int> q;
    for (int i = 0; i < n; ++i) {
        if (in_degree[i] == 0) {
            q.push(i);
        }
    }
    
    vector<int> result;
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        result.push_back(u);
        
        for (int v : adj[u]) {
            in_degree[v]--;
            if (in_degree[v] == 0) {
                q.push(v);
            }
        }
    }
    
    if (result.size() != n) {
        // 图中有环，无法进行拓扑排序
        return {};
    }
    
    return result;
}

// 检测图中是否有环
bool hasCycle(int n, const vector<vector<int>>& edges) {
    return topologicalSort(n, edges).empty();
}
```

### 5.5 BFS求图中两点最短路径（无权图）

```cpp
#include <queue>
#include <vector>
#include <limits>

vector<int> shortestPath(int n, const vector<vector<int>>& adj, 
                        int start, int end) {
    vector<int> dist(n, -1);
    vector<int> parent(n, -1);
    queue<int> q;
    
    q.push(start);
    dist[start] = 0;
    
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        
        if (u == end) break;
        
        for (int v : adj[u]) {
            if (dist[v] == -1) {
                dist[v] = dist[u] + 1;
                parent[v] = u;
                q.push(v);
            }
        }
    }
    
    // 重建路径
    vector<int> path;
    if (dist[end] == -1) return path;  // 无法到达
    
    int current = end;
    while (current != -1) {
        path.push_back(current);
        current = parent[current];
    }
    reverse(path.begin(), path.end());
    
    return path;
}
```

### 5.6 多源BFS

```cpp
#include <queue>
#include <vector>

// 从多个源点同时开始BFS
vector<vector<int>> multiSourceBFS(
    vector<vector<int>>& grid,
    const vector<Pos>& sources) {
    
    if (grid.empty()) return grid;
    
    int m = grid.size(), n = grid[0].size();
    queue<Pos> q;
    vector<vector<int>> dist(m, vector<int>(n, -1));
    
    for (const auto& src : sources) {
        if (grid[src.x][src.y] == 0) {
            q.push(src);
            dist[src.x][src.y] = 0;
        }
    }
    
    const int dx[] = {0, 0, 1, -1};
    const int dy[] = {1, -1, 0, 0};
    
    while (!q.empty()) {
        Pos cur = q.front();
        q.pop();
        
        for (int dir = 0; dir < 4; ++dir) {
            int nx = cur.x + dx[dir];
            int ny = cur.y + dy[dir];
            
            if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
            if (grid[nx][ny] == 1) continue;  // 障碍物
            if (dist[nx][ny] != -1) continue;  // 已访问
            
            dist[nx][ny] = dist[cur.x][cur.y] + 1;
            q.push(Pos(nx, ny));
        }
    }
    
    return dist;
}

// 腐烂橘子问题
int orangesRotting(vector<vector<int>>& grid) {
    if (grid.empty()) return -1;
    
    int m = grid.size(), n = grid[0].size();
    queue<Pos> q;
    int fresh = 0, minutes = 0;
    
    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < n; ++j) {
            if (grid[i][j] == 2) {
                q.push(Pos(i, j));
            } else if (grid[i][j] == 1) {
                fresh++;
            }
        }
    }
    
    const int dx[] = {0, 0, 1, -1};
    const int dy[] = {1, -1, 0, 0};
    
    while (!q.empty() && fresh > 0) {
        int level_size = q.size();
        
        for (int i = 0; i < level_size; ++i) {
            Pos cur = q.front();
            q.pop();
            
            for (int dir = 0; dir < 4; ++dir) {
                int nx = cur.x + dx[dir];
                int ny = cur.y + dy[dir];
                
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                if (grid[nx][ny] != 1) continue;
                
                grid[nx][ny] = 2;
                fresh--;
                q.push(Pos(nx, ny));
            }
        }
        
        if (!q.empty()) minutes++;
    }
    
    return fresh == 0 ? minutes : -1;
}
```

### 5.7 0-1 BFS（边权为0或1）

```cpp
#include <queue>
#include <vector>

// 0-1 BFS：用于边权只有0和1的最短路问题
vector<int> zeroOneBFS(int n, const vector<vector<pair<int,int>>>& adj, 
                       int start) {
    const int INF = 1e9;
    vector<int> dist(n, INF);
    deque<int> dq;
    
    dist[start] = 0;
    dq.push_back(start);
    
    while (!dq.empty()) {
        int u = dq.front();
        dq.pop_front();
        
        for (auto [v, w] : adj[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                if (w == 0) {
                    dq.push_front(v);
                } else {
                    dq.push_back(v);
                }
            }
        }
    }
    
    return dist;
}

// 使用示例：迷宫中每步可以移动（cost=1）或使用传送门（cost=0）
int shortestPath(vector<vector<int>>& grid) {
    int m = grid.size(), n = grid[0].size();
    const int INF = 1e9;
    vector<vector<int>> dist(m, vector<int>(n, INF));
    
    deque<tuple<int,int>> dq;
    dist[0][0] = 0;
    dq.push_back({0, 0});
    
    const int dx[] = {0, 0, 1, -1};
    const int dy[] = {1, -1, 0, 0};
    
    while (!dq.empty()) {
        auto [x, y] = dq.front();
        dq.pop_front();
        
        for (int dir = 0; dir < 4; ++dir) {
            int nx = x + dx[dir];
            int ny = y + dy[dir];
            
            if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
            if (grid[nx][ny] == 1) continue;
            
            // 普通移动cost=1
            if (dist[x][y] + 1 < dist[nx][ny]) {
                dist[nx][ny] = dist[x][y] + 1;
                dq.push_back({nx, ny});
            }
            
            // 假设某些格子是传送门（cost=0）
            // 这里用grid值为2表示传送门
            if (grid[nx][ny] == 2) {
                // 传送到某个特定位置
                // 实际应用中需要定义传送逻辑
            }
        }
    }
    
    return dist[m-1][n-1];
}
```

## 6. 双端队列模拟队列

```cpp
#include <deque>

// 使用deque实现队列（与std::queue相同，但更直观）
template<typename T>
class SimpleQueue {
private:
    deque<T> data;
public:
    void push(const T& x) {
        data.push_back(x);
    }
    
    void pop() {
        data.pop_front();
    }
    
    T& front() {
        return data.front();
    }
    
    const T& front() const {
        return data.front();
    }
    
    T& back() {
        return data.back();
    }
    
    const T& back() const {
        return data.back();
    }
    
    bool empty() const {
        return data.empty();
    }
    
    size_t size() const {
        return data.size();
    }
    
    void swap(SimpleQueue& other) {
        data.swap(other.data);
    }
};
```

## 7. 优先队列扩展（deque版）

```cpp
#include <deque>
#include <functional>

// 优先队列但支持按加入顺序处理相等优先级元素
template<typename T, typename Compare = less<T>>
class StablePriorityQueue {
private:
    vector<T> data;
    size_t index;  // 记录插入顺序
    
public:
    StablePriorityQueue() : index(0) {}
    
    void push(const T& x) {
        data.push_back({x, index++});
        push_heap(data.begin(), data.end(), 
                  [](const auto& a, const auto& b) {
                      return a.first > b.first;  // 最小堆
                  });
    }
    
    void pop() {
        pop_heap(data.begin(), data.end(),
                 [](const auto& a, const auto& b) {
                     return a.first > b.first;
                 });
        data.pop_back();
    }
    
    T& top() {
        return data.front().first;
    }
    
    const T& top() const {
        return data.front().first;
    }
    
    bool empty() const {
        return data.empty();
    }
    
    size_t size() const {
        return data.size();
    }
};
```

## 8. 常见陷阱与注意事项

### 8.1 pop()不返回值

```cpp
queue<int> q = {1, 2, 3};

// 错误写法
int x = q.pop();           // 编译错误！pop()返回void

// 正确写法
int x = q.front();
q.pop();
```

### 8.2 空队列操作

```cpp
queue<int> q;

// 以下操作都是未定义行为
// int x = q.front();       // 空队列访问队首
// int y = q.back();        // 空队列访问队尾
// q.pop();                 // 空队列弹出

// 正确做法
if (!q.empty()) {
    int x = q.front();
    q.pop();
}
```

### 8.3 迭代器问题

```cpp
queue<int> q = {1, 2, 3};

// queue不提供迭代器！
// 不能使用范围for循环遍历queue
// for (int x : q) { ... }  // 编译错误！

// 正确做法：不遍历queue，而是按顺序访问
while (!q.empty()) {
    cout << q.front() << " ";
    q.pop();
}
```

### 8.4 不能比较

```cpp
queue<int> q1 = {1, 2, 3};
queue<int> q2 = {1, 2, 3};

// queue不支持比较运算符
// if (q1 == q2) { ... }     // 编译错误！
```

### 8.5 使用deque实现queue的注意事项

```cpp
// queue的默认底层容器是deque
// deque同时支持push_front和push_back
// 但queue只使用push_back（队尾）和pop_front（队首）

// 这意味着：
// - 性能很好，两端操作都是O(1)
// - 不需要像vector那样担心重新分配问题

// 但如果你需要使用deque的其他特性
deque<int> dq;
dq.push_front(0);                   // deque特有，queue不支持
dq.push_back(5);
```

## 9. 复杂度分析

| 操作 | 时间复杂度 | 说明 |
|------|------------|------|
| `push()` | O(1) | 入队 |
| `pop()` | O(1) | 出队 |
| `front()` | O(1) | 访问队首 |
| `back()` | O(1) | 访问队尾 |
| `empty()` | O(1) | 判断空 |
| `size()` | O(1) | 获取大小 |
| `swap()` | O(1) | 交换内容 |

## 10. 完整竞赛模板

```cpp
#include <bits/stdc++.h>
using namespace std;

// BFS通用模板
class BFSTemplate {
public:
    // 1. 单源BFS
    template<typename Func>
    static vector<int> bfs(int n, int start, 
                          const vector<vector<int>>& graph,
                          Func process) {
        vector<int> dist(n, -1);
        queue<int> q;
        q.push(start);
        dist[start] = 0;
        
        while (!q.empty()) {
            int u = q.front();
            q.pop();
            
            if (!process(u, dist)) continue;
            
            for (int v : graph[u]) {
                if (dist[v] == -1) {
                    dist[v] = dist[u] + 1;
                    q.push(v);
                }
            }
        }
        
        return dist;
    }
    
    // 2. 带墙壁破坏的BFS
    static int bfsWithKObstacles(
        vector<vector<int>>& grid, int k) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<vector<char>>> visited(m, 
            vector<vector<char>>(n, vector<char>(k + 1, 0)));
        
        struct State { int x, y, k; };
        queue<State> q;
        q.push({0, 0, k});
        visited[0][0][k] = 1;
        
        const int dx[] = {0, 0, 1, -1};
        const int dy[] = {1, -1, 0, 0};
        int steps = 0;
        
        while (!q.empty()) {
            int sz = q.size();
            while (sz--) {
                auto [x, y, remaining_k] = q.front();
                q.pop();
                
                if (x == m - 1 && y == n - 1) return steps;
                
                for (int d = 0; d < 4; ++d) {
                    int nx = x + dx[d];
                    int ny = y + dy[d];
                    
                    if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                    
                    int new_k = remaining_k - grid[nx][ny];
                    if (new_k < 0) continue;
                    if (visited[nx][ny][new_k]) continue;
                    
                    visited[nx][ny][new_k] = 1;
                    q.push({nx, ny, new_k});
                }
            }
            steps++;
        }
        
        return -1;
    }
    
    // 3. 拓扑排序
    static vector<int> topologicalSort(
        int n, const vector<vector<int>>& edges) {
        vector<vector<int>> adj(n);
        vector<int> in_degree(n, 0);
        
        for (const auto& e : edges) {
            adj[e[0]].push_back(e[1]);
            in_degree[e[1]]++;
        }
        
        queue<int> q;
        for (int i = 0; i < n; ++i) {
            if (in_degree[i] == 0) q.push(i);
        }
        
        vector<int> result;
        while (!q.empty()) {
            int u = q.front();
            q.pop();
            result.push_back(u);
            
            for (int v : adj[u]) {
                if (--in_degree[v] == 0) {
                    q.push(v);
                }
            }
        }
        
        return result.size() == n ? result : vector<int>{};
    }
    
    // 4. 树/图的层次遍历
    template<typename Node>
    static vector<vector<Node>> levelOrder(
        Node* root,
        const vector<Node*>& children(Node*)) {
        vector<vector<Node>> result;
        if (!root) return result;
        
        queue<Node*> q;
        q.push(root);
        
        while (!q.empty()) {
            int sz = q.size();
            vector<Node*> level;
            
            for (int i = 0; i < sz; ++i) {
                Node* node = q.front();
                q.pop();
                level.push_back(node);
                
                for (Node* child : children(node)) {
                    if (child) q.push(child);
                }
            }
            
            result.push_back(level);
        }
        
        return result;
    }
};
```

## 11. 总结

`std::queue`是ACM竞赛中最常用的数据结构之一，其核心要点包括：

1. **FIFO特性**：先进先出，所有操作都发生在队首或队尾
2. **简洁接口**：`push()`、`pop()`、`front()`、`back()`四个核心操作
3. **底层实现**：默认使用`deque`，也可以指定`list`
4. **典型应用**：BFS搜索、层次遍历、拓扑排序、最短路径

掌握队列的使用对于解决各种图论问题和层次遍历问题至关重要。BFS是解决无权图最短路径的标准方法，而拓扑排序则是处理有向无环图（DAG）的基础工具。