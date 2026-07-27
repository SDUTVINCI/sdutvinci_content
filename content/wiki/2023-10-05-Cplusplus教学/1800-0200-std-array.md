---
title: "std::array"
---

## 本节解决什么问题

C 风格数组（`int arr[5]`）有很多缺点：不能直接赋值、传给函数时会退化成指针丢失大小信息、没有边界检查。而 `std::vector` 虽然功能强大，但它是动态分配内存的，有微小的额外开销。

`std::array` 解决的是：当你需要一个**编译期确定大小、栈上分配**的数组时，提供一个和 C 数组一样快、但拥有 STL 容器接口（有 `size()`、有迭代器、可赋值）的安全数组。

## 这个特性是什么

`std::array<T, N>` 是 STL 中封装了固定大小数组的容器模板。它大小在编译时确定，不动态分配内存（在栈上），包装了 C 数组同时提供 STL 容器的标准接口。

## C++ 标准版本

C++11

## 需要的头文件

```cpp
#include <array>
```

**运行/观察结果：** 这段是语法或接口示例，重点观察写法；放入完整程序后再运行验证。

## 基本语法

```cpp
std::array<元素类型, 大小> 变量名;            // 默认初始化（值未定义）
std::array<元素类型, 大小> 变量名 = {};        // 全部初始化为零
std::array<元素类型, 大小> 变量名 = {v1, v2, ...}; // 列表初始化
```

**运行/观察结果：** 这段是语法片段，重点看写法；补全上下文后再运行。

## 常用用法

| 操作 | 说明 |
|:---|:---|
| `a[i]` | 随机访问（不检查越界） |
| `a.at(i)` | 随机访问（检查越界） |
| `a.size()` | 返回元素个数（编译期常量） |
| `a.empty()` | 是否为空（永远为 false） |
| `a.front()` | 返回第一个元素 |
| `a.back()` | 返回最后一个元素 |
| `a.fill(val)` | 用 val 填充所有元素 |
| `a.data()` | 返回底层 C 数组指针 |

## 示例代码

### 示例 1：创建 array 并访问元素

```cpp
#include <iostream>
#include <array>

int main()
{
    // 创建一个包含 5 个 int 的 array，并初始化
    std::array<int, 5> arr = {10, 20, 30, 40, 50};

    // 用下标访问
    std::cout << "arr[0] = " << arr[0] << "\n";
    std::cout << "arr[4] = " << arr[4] << "\n";

    // 用 size() 获取大小
    std::cout << "size = " << arr.size() << "\n";

    return 0;
}
```

**运行结果**：

```
arr[0] = 10
arr[4] = 50
size = 5
```

### 示例 2：在示例 1 基础上，用 at() 安全访问和 fill() 填充

```cpp
#include <iostream>
#include <array>

int main()
{
    std::array<int, 5> arr = {10, 20, 30, 40, 50};

    // 用 at() 安全访问
    std::cout << "arr.at(2) = " << arr.at(2) << "\n";

    // 用 fill() 将所有元素设为同一个值
    arr.fill(99);

    std::cout << "after fill: ";
    for (int n : arr)
    {
        std::cout << n << " ";
    }
    std::cout << "\n";

    return 0;
}
```

**运行结果**：

```
arr.at(2) = 30
after fill: 99 99 99 99 99 
```

### 示例 3：在示例 2 基础上，对比 C 数组和 std::array 传递参数

```cpp
#include <iostream>
#include <array>

// C 风格：数组退化为指针，丢失大小信息
void print_c_array(int* arr, int size)
{
    std::cout << "C array: ";
    for (int i = 0; i < size; ++i)
    {
        std::cout << arr[i] << " ";
    }
    std::cout << "\n";
}

// std::array：大小信息不丢失
void print_std_array(const std::array<int, 5>& arr)
{
    std::cout << "std::array: ";
    for (int n : arr)
    {
        std::cout << n << " ";
    }
    std::cout << "\n";
    std::cout << "size from inside function = " << arr.size() << "\n";
}

int main()
{
    std::array<int, 5> arr = {1, 2, 3, 4, 5};

    // C 风格：需要额外传大小
    print_c_array(arr.data(), arr.size());

    // std::array：自带大小信息
    print_std_array(arr);

    return 0;
}
```

**运行结果**：

```
C array: 1 2 3 4 5 
std::array: 1 2 3 4 5 
size from inside function = 5
```

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | 创建 array 和基本访问 | `std::array<T, N>`、`size()` | array 大小是编译期常量，`size()` 返回固定值 | 大小必须是编译期常量，不能传变量 |
| 示例 2 | at() 安全访问和 fill() 填充 | `at()`、`fill()` | `at()` 越界会抛异常；`fill()` 方便批量赋值 | `fill()` 对所有元素赋相同值 |
| 示例 3 | 对比 C 数组和 array 传参 | `data()`、`const std::array<int,5>&` | array 传参时大小信息不丢失 | array 大小是类型的一部分，不同大小是不同的类型 |

## array 和 vector 的区别什么时候明显

如果只是存 5 个整数，`array` 和 `vector` 看起来都能用。但放到工程里，差异主要体现在“大小是否固定”和“是否需要动态扩容”：

| 场景 | 推荐 | 原因 |
|:---|:---|:---|
| 三轴 IMU 数据 `{x, y, z}` | `std::array<double, 3>` | 大小永远是 3，不需要扩容 |
| 固定长度 PID 参数 `{kp, ki, kd}` | `std::array<double, 3>` | 编译期就知道大小，语义明确 |
| 运行时读取一批传感器点 | `std::vector<Point>` | 点的数量运行时才知道 |
| 用户输入任意数量数据 | `std::vector<T>` | 需要 `push_back` 动态增长 |

`array` 更像“有 STL 接口的固定数组”，`vector` 更像“会变长的数组”。不要因为 `array` 更轻量就到处用它：只要元素个数运行时才知道，就应该用 `vector`。

### 示例 4：固定三轴数据更适合 array

```cpp
#include <array>
#include <iostream>

// std::array 是固定长度数组，长度在编译期就确定。
double norm3(const std::array<double, 3>& v)
{
    return v[0] * v[0] + v[1] * v[1] + v[2] * v[2];
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    std::array<double, 3> accel = {0.1, 0.2, 9.8};

    std::cout << "accel size = " << accel.size() << "\n";
    std::cout << "squared norm = " << norm3(accel) << "\n";

    return 0;
}
```

**运行结果**：

```
accel size = 3
squared norm = 96.09
```

这个例子中，三轴数据必须正好是 3 个值。用 `std::array<double, 3>` 可以把这个约束写进类型里；如果误传 `std::array<double, 2>`，编译期就会报错。

## 常见错误

**错误 1：用变量指定 array 大小**

```cpp
int n = 5;
std::array<int, n> arr;  // ❌ 编译错误！n 必须是编译期常量
```

**运行/观察结果：** 这段是语法或接口示例，重点观察写法；放入完整程序后再运行验证。

正确做法：用 `constexpr` 常量，或者改用 `std::vector`。

**错误 2：忘记初始化就访问**

```cpp
std::array<int, 5> arr;   // 值未定义（栈上的垃圾值）
std::cout << arr[0];      // ❌ 未定义行为
```

**运行/观察结果：** 运行后会按输出语句打印对应内容，变量值可结合初始化、赋值和函数调用顺序推导。

正确做法：用 `{}` 初始化或 `fill()`。

**错误 3：不同大小的 array 互相赋值**

```cpp
std::array<int, 3> a = {1, 2, 3};
std::array<int, 5> b = a;  // ❌ 编译错误！类型不同
```

**运行/观察结果：** 这段是语法或接口示例，重点观察写法；放入完整程序后再运行验证。

正确做法：`std::array<int,3>` 和 `std::array<int,5>` 是不同的类型，不能互相赋值。

## 使用建议

1. **编译期确定大小用 array**：如果大小在编译时就已知，用 `std::array` 比 `std::vector` 更轻量（无堆分配）。
2. **代替 C 数组**：能用 `std::array` 的地方就不要用 `int arr[N]`。
3. **传参注意大小**：`std::array<T, N>` 的大小是类型的一部分，函数签名要指定 N。
4. **可赋值**：和 C 数组不同，`std::array` 能用 `=` 整体赋值（相同类型）。

## 小结

- `std::array<T, N>` 封装了固定大小的数组，大小在编译期确定。
- 拥有 STL 容器的标准接口（`size()`、`at()`、迭代器等）。
- 可以整体赋值，传给函数时不会退化为指针，大小信息不丢失。
- 适用于编译期已知大小的场景，比 `vector` 更轻量。
