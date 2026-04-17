# functional 头文件详解

`<functional>`头文件是C++标准库中用于函数对象（functor）和函数适配的核心头文件。它定义了各种可调用对象包装器、函数绑定器、内置函数对象等。在ACM竞赛中，这个头文件配合`<algorithm>`使用可以实现非常灵活的算法操作。

## 1. functional头文件概述

`<functional>`提供了以下主要功能：

- **std::function**：通用的可调用对象包装器
- **函数适配器**：`bind`、`placeholder`
- **内置函数对象**：算术、比较、逻辑运算
- **.mem_fn**：成员函数适配器

## 2. std::function - 函数包装器

`std::function`是一个通用的可调用对象包装器，可以存储、复制和调用任何可调用目标（函数指针、lambda表达式、函数对象、成员函数等）。

### 2.1 基本用法

```cpp
#include <functional>
#include <iostream>
using namespace std;

// 普通函数
int add(int a, int b) {
    return a + b;
}

// 带状态的结构体（函数对象）
struct Multiplier {
    int factor;
    Multiplier(int f) : factor(f) {}
    int operator()(int x) const {
        return x * factor;
    }
};

int main() {
    // 存储普通函数
    function<int(int, int)> f1 = add;
    cout << "f1(3, 4) = " << f1(3, 4) << endl;  // 7
    
    // 存储lambda表达式
    function<int(int, int)> f2 = [](int a, int b) { return a * b; };
    cout << "f2(3, 4) = " << f2(3, 4) << endl;  // 12
    
    // 存储函数对象
    function<int(int)> f3 = Multiplier(10);
    cout << "f3(5) = " << f3(5) << endl;  // 50
    
    // 存储成员函数
    struct Foo {
        int value;
        Foo(int v) : value(v) {}
        int get() const { return value; }
        int add(int x) const { return value + x; }
    };
    
    Foo obj(100);
    function<int()> f4 = bind(&Foo::get, &obj);
    cout << "f4() = " << f4() << endl;  // 100
    
    function<int(int)> f5 = bind(&Foo::add, &obj, placeholders::_1);
    cout << "f5(50) = " << f5(50) << endl;  // 150
    
    return 0;
}
```

### 2.2 空function检查

```cpp
#include <functional>
#include <iostream>
using namespace std;

void test() {
    function<void()> f;  // 空function
    
    // 检查是否为空
    if (f) {
        cout << "Not empty" << endl;
    } else {
        cout << "Empty" << endl;  // 输出Empty
    }
    
    // 使用bool运算符检查
    if (f == nullptr) {
        cout << "Is nullptr" << endl;
    }
    
    // 赋值后变为非空
    f = []() { cout << "Hello" << endl; };
    if (f) {
        cout << "Now not empty" << endl;  // 输出
        f();  // 调用lambda，输出Hello
    }
}

int main() {
    test();
    return 0;
}
```

### 2.3 实际应用

```cpp
#include <functional>
#include <map>
#include <iostream>
#include <vector>
using namespace std;

// 回调函数示例
using Callback = function<void(int)>;

void processNumbers(const vector<int>& nums, Callback callback) {
    for (int n : nums) {
        if (n > 0) {
            callback(n);
        }
    }
}

// 事件处理示例
class EventEmitter {
    map<string, vector<function<void()>>> listeners;
public:
    void on(const string& event, function<void()> handler) {
        listeners[event].push_back(handler);
    }
    void emit(const string& event) {
        if (listeners.count(event)) {
            for (auto& handler : listeners[event]) {
                handler();
            }
        }
    }
};

int main() {
    // 回调示例
    vector<int> data = {1, -2, 3, -4, 5};
    processNumbers(data, [](int n) {
        cout << "Positive: " << n << endl;
    });
    
    // 事件系统示例
    EventEmitter emitter;
    emitter.on("start", []() { cout << "Started!" << endl; });
    emitter.on("start", []() { cout << "Processing..." << endl; });
    emitter.on("end", []() { cout << "Finished!" << endl; });
    
    emitter.emit("start");
    emitter.emit("end");
    
    return 0;
}
```

## 3. 内置函数对象

`<functional>`提供了大量的内置函数对象（function objects），用于算法中的自定义操作。

### 3.1 算术运算函数对象

```cpp
#include <functional>
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

int main() {
    // plus<T> - 加法
    plus<int> add;
    cout << "5 + 3 = " << add(5, 3) << endl;  // 8
    
    // minus<T> - 减法
    minus<int> sub;
    cout << "10 - 3 = " << sub(10, 3) << endl;  // 7
    
    // multiplies<T> - 乘法
    multiplies<int> mul;
    cout << "4 * 5 = " << mul(4, 5) << endl;  // 20
    
    // divides<T> - 除法
    divides<int> div;
    cout << "20 / 4 = " << div(20, 4) << endl;  // 5
    
    // modulus<T> - 取模
    modulus<int> mod;
    cout << "17 % 5 = " << mod(17, 5) << endl;  // 2
    
    // negate<T> - 取反
    negate<int> neg;
    cout << "-5 = " << neg(5) << endl;  // -5
    
    return 0;
}
```

### 3.2 比较运算函数对象

```cpp
#include <functional>
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

struct Point {
    int x, y;
};

int main() {
    // equal_to<T> - 相等比较
    equal_to<int> eq;
    cout << "5 == 3: " << eq(5, 3) << endl;  // 0 (false)
    
    // not_equal_to<T> - 不等比较
    not_equal_to<int> neq;
    cout << "5 != 3: " << neq(5, 3) << endl;  // 1 (true)
    
    // greater<T> - 大于
    greater<int> gt;
    cout << "5 > 3: " << gt(5, 3) << endl;  // 1
    
    // less<T> - 小于
    less<int> lt;
    cout << "5 < 3: " << lt(5, 3) << endl;  // 0
    
    // greater_equal<T> - 大于等于
    // less_equal<T> - 小于等于
    
    // 在算法中使用
    vector<int> v = {5, 2, 8, 1, 9};
    sort(v.begin(), v.end(), greater<int>());  // 降序
    // v = {9, 8, 5, 2, 1}
    
    cout << "Sorted (desc): ";
    for (int x : v) cout << x << " ";
    cout << endl;
    
    return 0;
}
```

### 3.3 逻辑运算函数对象

```cpp
#include <functional>
#include <algorithm>
#include <iostream>
using namespace std;

int main() {
    // logical_and<T> - 逻辑与
    logical_and<int> land;
    cout << "true && false: " << land(1, 0) << endl;  // 0
    
    // logical_or<T> - 逻辑或
    logical_or<int> lor;
    cout << "true || false: " << lor(1, 0) << endl;  // 1
    
    // logical_not<T> - 逻辑非
    logical_not<int> lnot;
    cout << "!true: " << lnot(1) << endl;  // 0
    
    return 0;
}
```

### 3.4 位运算函数对象

```cpp
#include <functional>
#include <bitset>
#include <iostream>
using namespace std;

int main() {
    // bit_and<T> - 位与
    bit_and<int> band;
    cout << "12 & 10 = " << band(12, 10) << endl;  // 8 (1100 & 1010 = 1000)
    
    // bit_or<T> - 位或
    bit_or<int> bor;
    cout << "12 | 10 = " << bor(12, 10) << endl;  // 14
    
    // bit_xor<T> - 位异或
    bit_xor<int> bxor;
    cout << "12 ^ 10 = " << bxor(12, 10) << endl;  // 6
    
    // bit_not<T> - 位取反（C++11）
    bit_not<int> bnot;
    cout << "~5 = " << bnot(5) << endl;  // -6（取决于整数的表示）
    
    return 0;
}
```

## 4. std::bind - 函数绑定

`std::bind`用于创建函数对象的适配器，可以绑定函数参数、占位符等。

### 4.1 绑定参数

```cpp
#include <functional>
#include <iostream>
using namespace std;

void print(int a, int b, int c) {
    cout << a << " " << b << " " << c << endl;
}

int add(int a, int b) {
    return a + b;
}

int main() {
    // 绑定部分参数
    auto f1 = bind(print, 1, 2, 3);
    f1();  // 输出: 1 2 3
    
    // 使用占位符
    auto f2 = bind(print, placeholders::_1, 100, 200);
    f2(1);    // 输出: 1 100 200
    f2(99);   // 输出: 99 100 200
    
    // 多个占位符
    auto f3 = bind(print, placeholders::_2, placeholders::_1, 100);
    f3(1, 2);   // 输出: 2 1 100
    
    // 调整参数顺序
    auto f4 = bind(print, placeholders::_2, placeholders::_2, placeholders::_1);
    f4(1, 2);   // 输出: 2 2 1
    
    // 绑定返回值
    auto f5 = bind(add, placeholders::_1, 10);
    cout << "f5(5) = " << f5(5) << endl;  // 15
    
    return 0;
}
```

### 4.2 绑定成员函数

```cpp
#include <functional>
#include <iostream>
using namespace std;

class Calculator {
public:
    int add(int a, int b) { return a + b; }
    int multiply(int a, int b) { return a * b; }
    void print(const string& msg) { cout << msg << endl; }
    static int staticAdd(int a, int b) { return a + b; }
};

int main() {
    Calculator calc;
    
    // 绑定成员函数（需要对象指针或引用）
    auto f1 = bind(&Calculator::add, &calc, placeholders::_1, placeholders::_2);
    cout << "f1(3, 4) = " << f1(3, 4) << endl;  // 7
    
    // 绑定成员函数并预设参数
    auto f2 = bind(&Calculator::add, &calc, 100, placeholders::_1);
    cout << "f2(50) = " << f2(50) << endl;  // 150
    
    // 绑定const成员函数
    const Calculator constCalc;
    auto f3 = bind(&Calculator::multiply, &constCalc, placeholders::_1, 10);
    cout << "f3(7) = " << f3(7) << endl;  // 70
    
    // 绑定静态成员函数
    auto f4 = &Calculator::staticAdd;
    auto f5 = bind(&Calculator::staticAdd, placeholders::_1, placeholders::_2);
    cout << "f5(3, 4) = " << f5(3, 4) << endl;  // 7
    
    return 0;
}
```

### 4.3 在算法中使用bind

```cpp
#include <functional>
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

struct Person {
    string name;
    int age;
};

int main() {
    vector<int> v = {1, 2, 3, 4, 5};
    
    // 使用bind与transform
    vector<int> result(v.size());
    transform(v.begin(), v.end(), result.begin(),
        bind(multiplies<int>(), placeholders::_1, 2));
    // result = {2, 4, 6, 8, 10}
    
    cout << "Doubled: ";
    for (int x : result) cout << x << " ";
    cout << endl;
    
    // 与sort配合
    vector<Person> people = {
        {"Alice", 30},
        {"Bob", 25},
        {"Charlie", 35}
    };
    
    // 按年龄排序
    sort(people.begin(), people.end(),
        bind(&Person::age, placeholders::_1) < bind(&Person::age, placeholders::_2));
    
    cout << "Sorted by age: ";
    for (const auto& p : people) {
        cout << p.name << "(" << p.age << ") ";
    }
    cout << endl;
    
    // 与find_if配合
    vector<int> nums = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    auto it = find_if(nums.begin(), nums.end(),
        bind(greater<int>(), placeholders::_1, 5));
    if (it != nums.end()) {
        cout << "First > 5: " << *it << endl;  // 6
    }
    
    return 0;
}
```

## 5. std::ref 和 std::cref - 引用包装

```cpp
#include <functional>
#include <iostream>
#include <vector>
using namespace std;

void increment(int& x) {
    ++x;
}

int main() {
    int value = 10;
    
    // 通常函数包装会复制参数
    function<void()> f1 = bind(increment, value);  // 复制
    f1();
    cout << "value after f1: " << value << endl;  // 10（未改变）
    
    // 使用ref包装引用
    function<void()> f2 = bind(increment, ref(value));
    f2();
    cout << "value after f2: " << value << endl;  // 11
    
    // 使用cref包装const引用
    const int constValue = 100;
    function<const int&()> f3 = bind(cref(constValue));
    //cref主要用于保持const引用语义
    
    return 0;
}
```

## 6. std::mem_fn - 成员函数适配器

```cpp
#include <functional>
#include <vector>
#include <iostream>
using namespace std;

struct Person {
    string name;
    int age;
    void display() const { 
        cout << name << ", " << age << endl; 
    }
};

int main() {
    vector<Person> people = {
        {"Alice", 30},
        {"Bob", 25},
        {"Charlie", 35}
    };
    
    // 使用mem_fn调用所有对象的成员函数
    for_each(people.begin(), people.end(), mem_fn(&Person::display));
    
    // 与mem_fn配合使用bind
    auto getAge = mem_fn(&Person::age);
    for (const auto& p : people) {
        cout << p.name << "'s age: " << getAge(p) << endl;
    }
    
    return 0;
}
```

## 7. hash - 哈希函数对象

```cpp
#include <functional>
#include <string>
#include <unordered_map>
#include <iostream>
using namespace std;

// 自定义类型的哈希函数
struct Point {
    int x, y;
    bool operator==(const Point& other) const {
        return x == other.x && y == other.y;
    }
};

struct PointHash {
    size_t operator()(const Point& p) const {
        // 简单的哈希组合
        return hash<int>()(p.x) ^ (hash<int>()(p.y) << 1);
    }
};

int main() {
    // 使用内置hash
    hash<string> strHash;
    cout << "hash(\"hello\") = " << strHash("hello") << endl;
    
    hash<int> intHash;
    cout << "hash(42) = " << intHash(42) << endl;
    
    // 使用自定义哈希
    unordered_map<Point, string, PointHash> pointMap;
    pointMap[{1, 2}] = "Point A";
    pointMap[{3, 4}] = "Point B";
    
    cout << "Point (1,2): " << pointMap[{1, 2}] << endl;
    
    return 0;
}
```

## 8. 竞赛应用场景

### 8.1 自定义排序

```cpp
#include <functional>
#include <algorithm>
#include <vector>
#include <string>
#include <iostream>
using namespace std;

struct Student {
    string name;
    int score;
    int id;
};

int main() {
    vector<Student> students = {
        {"Alice", 90, 1},
        {"Bob", 85, 2},
        {"Charlie", 95, 3},
        {"David", 85, 4}
    };
    
    // 按分数降序，分数相同按姓名升序
    sort(students.begin(), students.end(),
        [](const Student& a, const Student& b) {
            if (a.score != b.score) return a.score > b.score;
            return a.name < b.name;
        });
    
    // 使用greater实现相同效果
    // sort(students.begin(), students.end(),
    //     [](const Student& a, const Student& b) {
    //         return tie(a.score, a.name) > tie(b.score, b.name);
    //     });
    
    for (const auto& s : students) {
        cout << s.name << ": " << s.score << endl;
    }
    
    return 0;
}
```

### 8.2 自定义查找条件

```cpp
#include <functional>
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

struct Product {
    string name;
    double price;
    int stock;
};

int main() {
    vector<Product> products = {
        {"Apple", 3.5, 100},
        {"Banana", 1.2, 50},
        {"Orange", 2.8, 0},
        {"Mango", 5.0, 20}
    };
    
    // 查找库存为0的产品
    auto outOfStock = find_if(products.begin(), products.end(),
        [](const Product& p) { return p.stock == 0; });
    
    if (outOfStock != products.end()) {
        cout << "Out of stock: " << outOfStock->name << endl;
    }
    
    // 查找价格低于3元的商品
    auto cheap = find_if(products.begin(), products.end(),
        [](const Product& p) { return p.price < 3.0; });
    
    if (cheap != products.end()) {
        cout << "Cheap: " << cheap->name << " - $" << cheap->price << endl;
    }
    
    return 0;
}
```

### 8.3 函数组合

```cpp
#include <functional>
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

// 函数组合：先f后g
template<typename F, typename G>
auto compose(F f, G g) {
    return [f, g](auto x) { return g(f(x)); };
}

// 求绝对值后再乘2
auto absThenDouble = compose(
    [](int x) { return abs(x); },
    [](int x) { return x * 2; }
);

int main() {
    cout << "absThenDouble(-5) = " << absThenDouble(-5) << endl;  // 10
    
    vector<int> v = {-3, 5, -2, 8, -1};
    
    // 对所有元素应用absThenDouble
    vector<int> result(v.size());
    transform(v.begin(), v.end(), result.begin(), absThenDouble);
    // result = {6, 10, 4, 16, 2}
    
    cout << "Transformed: ";
    for (int x : result) cout << x << " ";
    cout << endl;
    
    return 0;
}
```

### 8.4 回调和事件处理

```cpp
#include <functional>
#include <map>
#include <vector>
#include <iostream>
using namespace std;

class Button {
public:
    using ClickHandler = function<void()>;
    
    void setOnClick(ClickHandler handler) {
        onClick = handler;
    }
    
    void click() {
        cout << "Button clicked" << endl;
        if (onClick) {
            onClick();
        }
    }
    
private:
    ClickHandler onClick;
};

int main() {
    Button btn;
    
    // 使用lambda作为回调
    int clickCount = 0;
    btn.setOnClick([&clickCount]() {
        clickCount++;
        cout << "Click #" << clickCount << endl;
    });
    
    btn.click();  // Click #1
    btn.click();  // Click #2
    btn.click();  // Click #3
    
    return 0;
}
```

### 8.5 延迟计算

```cpp
#include <functional>
#include <iostream>
using namespace std;

// 延迟计算包装
template<typename T>
class Lazy {
    function<T()> evaluator;
    mutable optional<T> cached;
public:
    Lazy(function<T()> f) : evaluator(f) {}
    
    T get() const {
        if (!cached.has_value()) {
            cached = evaluator();
        }
        return cached.value();
    }
    
    // 显式转换为T
    explicit operator T() const { return get(); }
};

int expensiveCalculation(int n) {
    cout << "Calculating..." << n << " * " << n << endl;
    return n * n;
}

int main() {
    // 创建延迟计算对象
    Lazy<int> lazy([]() { return expensiveCalculation(100); });
    
    cout << "Before first get" << endl;
    int result1 = lazy.get();  // 第一次计算
    cout << "Result 1: " << result1 << endl;
    
    int result2 = lazy.get();  // 使用缓存
    cout << "Result 2: " << result2 << endl;
    
    return 0;
}
```

## 9. 注意事项

### 9.1 Lambda vs bind

```cpp
#include <functional>
#include <iostream>
using namespace std;

void print(int a, int b, int c) {
    cout << a << " " << b << " " << c << endl;
}

int add(int a, int b) { return a + b; }

int main() {
    // 推荐：Lambda通常更易读
    auto f1 = [](int x) { return x * 2; };
    
    // bind的等效写法
    auto f2 = bind([](int x) { return x * 2; }, placeholders::_1);
    
    // 对于简单操作，lambda更好
    // bind更适合复杂参数绑定
    
    return 0;
}
```

### 9.2 性能考虑

```cpp
#include <functional>
#include <chrono>
#include <iostream>
using namespace std;

void test() {
    // std::function有额外的间接调用开销
    // 对于性能敏感的代码，直接使用lambda或普通函数更好
    
    function<int(int)> f = [](int x) { return x * 2; };
    // 比直接调用lambda慢一点
}
```

### 9.3 线程安全

```cpp
#include <functional>
#include <mutex>
using namespace std;

// std::function本身不是线程安全的
// 如需多线程安全，需要自行加锁
class ThreadSafeCallback {
    mutex mtx;
    function<void()> callback;
public:
    void setCallback(function<void()> cb) {
        lock_guard<mutex> lock(mtx);
        callback = cb;
    }
    
    void execute() {
        lock_guard<mutex> lock(mtx);
        if (callback) callback();
    }
};
```

## 10. 复杂度汇总

| 功能 | 时间复杂度 | 说明 |
|------|------------|------|
| `std::function` 构造 | O(1) | 简单构造 |
| `std::function` 调用 | O(1) | 函数调用开销 |
| `std::bind` | O(1) | 创建绑定对象 |
| `std::ref`/`cref` | O(1) | 引用包装 |
| 内置函数对象 | O(1) | 简单调用 |

## 11. 总结

`<functional>`头文件提供了强大的函数对象支持：

1. **`std::function`**：通用可调用对象包装器，用于回调、事件处理
2. **内置函数对象**：`plus`、`minus`、`greater`、`less`等，用于算法自定义
3. **`std::bind`**：函数参数绑定，可以预设参数、重排参数顺序
4. **`std::ref`/`cref`**：引用包装，避免复制
5. **`mem_fn`**：成员函数适配器

在竞赛中，这些功能常用于：
- 自定义算法比较逻辑
- 回调和延迟计算
- 简化复杂函数调用
- 成员函数绑定

熟练使用这些工具可以让代码更简洁、更灵活。
