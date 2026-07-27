---
title: "std::span"
---

## 本节解决什么问题

你要写一个函数，处理一段连续数据——可以是 vector、array、C 数组。在 C++20 之前，你有两种选择：

1. **传 `const std::vector<T>&`**：只能接收 vector，不能接收 array 或 C 数组。
2. **传 `const T* data, size_t size`**：能接收所有，但丢失了大小信息，容易写错。

`std::span` 提供了**统一的只读（或可写）视图**，不拥有数据，只是一个"窗口"，可以统一处理所有连续内存容器。

## 这个特性是什么

`std::span<T>` 是 C++20 引入的**视图类型**，它不拥有数据，只是指向一段连续内存的指针 + 大小的轻量包装。你可以把它理解为"**对数组的引用**"。

## C++ 标准版本

C++20

## 需要的头文件

```cpp
#include <span>
```

## 基本语法

```cpp
// span 不拥有数据，只是观察
std::span<T> sp;             // 空 span
std::span<T> sp(ptr, size);  // 从指针 + 大小构造
std::span<T> sp(vector);     // 从 vector 构造
std::span<T> sp(array);      // 从 std::array 构造
std::span<T> sp(c_array);    // 从 C 数组构造

// 常用操作
sp.size();      // 元素个数
sp.empty();     // 是否为空
sp[i];          // 下标访问
sp.front();     // 第一个元素
sp.back();      // 最后一个元素
sp.data();      // 底层指针
sp.subspan(pos, count);  // 子视图
sp.first(n);    // 前 n 个元素的子视图
sp.last(n);     // 后 n 个元素的子视图
```

## 常用用法

| 用法 | 说明 |
|:---|:---|
| `void f(std::span<const int> data)` | 接收任何连续 int 容器 |
| `std::span<int>(vec)` | 从 vector 创建 |
| `std::span<int>(arr)` | 从 C 数组创建 |
| `sp.subspan(offset, count)` | 创建子视图（无拷贝） |

## 示例代码

### 示例 1：用 span 统一接收不同容器类型

```cpp
#include <iostream>
#include <span>
#include <vector>
#include <array>

// 一个函数处理所有类型的连续 int 数据
void print_data(std::span<const int> data)
{
    std::cout << "size = " << data.size() << ": ";
    for (int n : data)
    {
        std::cout << n << " ";
    }
    std::cout << "\n";
}

int main()
{
    int c_arr[] = {1, 2, 3, 4, 5};              // C 数组
    std::vector<int> vec = {10, 20, 30};         // vector
    std::array<int, 4> arr = {100, 200, 300, 400};  // std::array

    print_data(c_arr);   // ✅ C 数组
    print_data(vec);      // ✅ vector
    print_data(arr);      // ✅ std::array

    return 0;
}
```

**运行结果**：

```
size = 5: 1 2 3 4 5 
size = 3: 10 20 30 
size = 4: 100 200 300 400 
```

### 示例 2：在示例 1 基础上，span 的子视图（subspan）

```cpp
#include <iostream>
#include <span>
#include <vector>

int main()
{
    std::vector<int> vec = {10, 20, 30, 40, 50, 60};

    std::span<int> sp(vec);

    // 取中间 3 个元素（从索引 1 开始，取 3 个）
    auto mid = sp.subspan(1, 3);
    std::cout << "mid: ";
    for (int n : mid) std::cout << n << " ";
    std::cout << "\n";

    // 取前 2 个
    auto first2 = sp.first(2);
    std::cout << "first 2: ";
    for (int n : first2) std::cout << n << " ";
    std::cout << "\n";

    // 取后 3 个
    auto last3 = sp.last(3);
    std::cout << "last 3: ";
    for (int n : last3) std::cout << n << " ";
    std::cout << "\n";

    // 子视图修改会影响原数据！
    mid[0] = 999;
    std::cout << "after modifying mid[0] = 999\n";
    std::cout << "original vec[1] = " << vec[1] << "\n";

    return 0;
}
```

**运行结果**：

```
mid: 20 30 40 
first 2: 10 20 
last 3: 40 50 60 
after modifying mid[0] = 999
original vec[1] = 999
```

### 示例 3：在示例 2 基础上，写一个求和的函数

```cpp
#include <iostream>
#include <span>
#include <vector>

// 计算 span 中所有元素的和
double sum(std::span<const double> data)
{
    double total = 0.0;
    for (double x : data)
    {
        total += x;
    }
    return total;
}

// 计算 span 中所有元素的平均值
double average(std::span<const double> data)
{
    if (data.empty()) return 0.0;
    return sum(data) / data.size();
}

int main()
{
    std::vector<double> scores = {85.5, 92.0, 78.5, 90.0, 88.0};
    double c_arr[] = {1.1, 2.2, 3.3};

    std::cout << "sum(scores) = " << sum(scores) << "\n";
    std::cout << "avg(scores) = " << average(scores) << "\n";
    std::cout << "sum(c_arr) = " << sum(c_arr) << "\n";
    std::cout << "avg(c_arr) = " << average(c_arr) << "\n";

    return 0;
}
```

**运行结果**：

```
sum(scores) = 434
avg(scores) = 86.8
sum(c_arr) = 6.6
avg(c_arr) = 2.2
```

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | 统一接收不同容器 | `std::span<const int>` 作为参数 | 一个函数处理 vector/array/C 数组 | span 只是视图，不拥有数据 |
| 示例 2 | 子视图操作 | `subspan()`、`first()`、`last()` | 零拷贝切片，可以局部处理大数组 | 子视图修改影响原数据 |
| 示例 3 | 用 span 写通用函数 | `sum(std::span<const double>)` | 通用算法 + 任意连续容器 | 函数内部用 range-for 和 .size() 即可 |

## `span<const T>` 和 `span<T>` 的区别

| 写法 | 能否修改底层数据 | 常见用途 |
|:---|:---|:---|
| `std::span<const T>` | 不能修改 | 只读处理、统计、打印、校验 |
| `std::span<T>` | 可以修改 | 填充缓冲区、归一化数据、原地滤波 |

`span` 不拥有数据，所以“是否 const”非常重要。函数参数默认优先写 `std::span<const T>`，只有明确要修改原数据时才写 `std::span<T>`。

### 示例 4：只读 span 和可写 span

```cpp
#include <iostream>
#include <span>
#include <vector>

void print(std::span<const int> data)
{
    for (int n : data)
    {
        std::cout << n << " ";
    }
    std::cout << "\n";
}

void add_offset(std::span<int> data, int offset)
{
    for (int& n : data)
    {
        n += offset;
    }
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // vector 是动态数组，元素数量可以在运行时变化。
    std::vector<int> values = {10, 20, 30};

    print(values);
    add_offset(values, 5);
    print(values);

    return 0;
}
```

**运行结果**：

```
10 20 30
15 25 35
```

`print` 只读，所以用 `span<const int>`；`add_offset` 要原地修改，所以用 `span<int>`。这比传 `vector<int>&` 更通用，因为它也可以接收 `array` 和 C 数组。

## 常见错误

**错误 1：span 比它引用的数据活得久**

```cpp
std::span<int> make_span()
{
    std::vector<int> v = {1, 2, 3};
    return std::span<int>(v);  // ❌ v 是局部变量，span 成为悬挂视图！
}
```

正确做法：span 只是视图，必须确保底层数据比 span 活得久。

**错误 2：用 span 接收临时对象**

```cpp
std::span<const int> sp = std::vector<int>{1, 2, 3};  // ❌ 临时对象已销毁！
```

正确做法：先创建具名的容器，再传给 span。

**错误 3：认为 span 可以用于不连续容器**

```cpp
std::list<int> lst = {1, 2, 3};
std::span<int> sp(lst);  // ❌ list 不连续！
```

正确做法：span 只适用于连续内存容器（vector、array、C 数组、deque 的部分场景）。

## 使用建议

1. **函数参数优先用 `std::span<const T>`**：代替 `const vector<T>&`，更通用。
2. **span 只是视图**：不拥有数据，必须确保底层数据存活。
3. **子视图零拷贝**：`subspan` 等操作非常高效。
4. **C++20 才有 span**：如果你的项目用 C++17，可以用 `std::string_view`（字符串版的 span）或第三方库。
5. **只读参数优先 `span<const T>`**：这样 vector、array、C 数组都能传，而且不会被函数修改。

## 小结

- `std::span<T>` 是不拥有数据的连续内存视图。
- 可以统一接收 vector、array、C 数组——一个参数类型覆盖所有。
- 支持子视图切片（`subspan`、`first`、`last`），零拷贝。
- span 的生命周期不能超过底层数据。
- 只适用于连续内存容器。
