---
title: "constexpr"
---

## 本节解决什么问题

有些计算在编译时就已经知道结果，但如果用普通函数，却要等到运行时才计算——浪费了 CPU 时间。此外，某些场景（数组大小、模板参数、static_assert）必须使用编译期常量，普通变量做不到。

`constexpr` 让函数和变量在**编译期就能求值**，把运行时开销转移到编译期，还能用在"必须编译期常量"的地方。

## 这个特性是什么

`constexpr` 声明一个变量或函数可以在编译期求值。从 C++11 开始引入，后续标准不断增强。

| C++ 版本 | 支持 |
|:---|:---|
| C++11 | 简单 constexpr 函数（一条 return 语句）、constexpr 变量 |
| C++14 | constexpr 函数支持多条语句、循环 |
| C++17 | constexpr lambda |
| C++20 | `consteval`（必须在编译期执行）、更强的 constexpr |
| C++23 | constexpr 支持更多标准库 |

## C++ 标准版本

C++11（随后的每个版本都有增强）

## 需要的头文件

不需要额外头文件。`constexpr` 是语言关键字。

## 基本语法

```cpp
// constexpr 变量：值在编译期确定
constexpr int size = 100;

// constexpr 函数：可以在编译期执行
constexpr int square(int n)
{
    return n * n;
}

// constexpr 变量可以用编译期函数初始化
constexpr int val = square(5);  // 编译期计算

// C++20 consteval：必须在编译期执行（不能运行时调用）
consteval int cube(int n)
{
    return n * n * n;
}
```

## const vs constexpr

| 方面 | const | constexpr |
|:---|:---|:---|
| 含义 | 变量不可修改 | 变量或函数可在编译期求值 |
| 初始化 | 可在运行时初始化 | 必须在编译期初始化 |
| 函数 | 只能修饰成员函数 | 函数可在编译期执行 |
| 用途 | 防止变量被修改 | 编译期计算，提高效率 |
| 示例 | `const int x = get_value();` | `constexpr int x = 5 * 3;` |

## constexpr、consteval、constinit 的区别

这三个词看起来都和"常量"有关，但解决的问题不同。初学时先掌握 `constexpr`，再理解 C++20 的 `consteval` 和 `constinit`。

| 关键字 | 含义 | 典型场景 |
|:---|:---|:---|
| `constexpr` | 可以在编译期求值，也可以在运行期调用 | 通用编译期函数、常量表达式 |
| `consteval` | 必须在编译期求值 | 生成编译期 ID、编译期校验 |
| `constinit` | 保证静态对象在编译期初始化，但对象不一定是 const | 避免全局变量动态初始化顺序问题 |

一句话区分：`constexpr` 偏"能不能算"，`consteval` 偏"必须现在算"，`constinit` 偏"初始化时机必须早"。

## 示例代码

### 示例 1：constexpr 变量——必须编译期确定

```cpp
#include <iostream>

int main()
{
    constexpr int size = 10;        // 编译期常量
    int arr[size];                  // ✅ 可以用作数组大小

    const int runtime_size = size;  // const 可以用编译期常量初始化
    int arr2[runtime_size];         // ✅ 也可以用作数组大小（因为初始化值是 constexpr）

    int n = 5;
    // constexpr int s2 = n;        // ❌ n 是运行时变量，不能初始化 constexpr
    const int s3 = n;               // ✅ const 可以在运行时初始化
    // int arr3[s3];                // ❌ s3 不是编译期常量，不能用作数组大小

    std::cout << "size = " << size << "\n";

    return 0;
}
```

**运行结果**：

```
size = 10
```

### 示例 2：在示例 1 基础上，constexpr 函数

```cpp
#include <iostream>

// constexpr 函数：编译期可执行
constexpr int factorial(int n)
{
    int result = 1;
    for (int i = 1; i <= n; ++i)  // C++14 起支持循环
    {
        result *= i;
    }
    return result;
}

int main()
{
    // 编译期计算：factorial(5) 在编译时就得到 120
    constexpr int f5 = factorial(5);
    std::cout << "factorial(5) = " << f5 << "\n";

    // 也可运行时调用
    int n;
    std::cout << "Enter a number: ";
    std::cin >> n;
    std::cout << "factorial(" << n << ") = " << factorial(n) << "\n";

    return 0;
}
```

**运行结果**：

```
factorial(5) = 120
Enter a number: 4
factorial(4) = 24
```

### 示例 3：在示例 2 基础上，constexpr 用于模板和编译期检查

```cpp
#include <iostream>

// constexpr 函数：编译期判断是否为质数
constexpr bool is_prime(int n)
{
    if (n <= 1) return false;
    for (int i = 2; i * i <= n; ++i)
    {
        if (n % i == 0) return false;
    }
    return true;
}

// 编译期静态断言
static_assert(is_prime(2), "2 is prime");
static_assert(is_prime(7), "7 is prime");
static_assert(!is_prime(9), "9 is NOT prime");

// 编译期生成质数数组
constexpr int get_nth_prime(int n)
{
    int count = 0;
    int num = 1;
    while (count < n)
    {
        ++num;
        if (is_prime(num))
        {
            ++count;
        }
    }
    return num;
}

int main()
{
    constexpr int primes[] = {
        get_nth_prime(1),  // 2
        get_nth_prime(2),  // 3
        get_nth_prime(3),  // 5
        get_nth_prime(4),  // 7
        get_nth_prime(5)   // 11
    };

    std::cout << "First 5 primes: ";
    for (int p : primes)
    {
        std::cout << p << " ";
    }
    std::cout << "\n";

    return 0;
}
```

**运行结果**：

```
First 5 primes: 2 3 5 7 11 
```

### 示例 4：C++17 constexpr lambda

```cpp
#include <iostream>

int main()
{
    // constexpr lambda：编译期可调用
    constexpr auto square = [](int x) constexpr {
        return x * x;
    };

    constexpr int result = square(5);  // 编译期计算
    std::cout << "square(5) = " << result << "\n";

    // constexpr lambda 也可运行时调用
    int n = 7;
    std::cout << "square(" << n << ") = " << square(n) << "\n";

    return 0;
}
```

**运行结果**：

```
square(5) = 25
square(7) = 49
```

### 示例 5：在示例 4 基础上，constexpr 可以运行期调用，consteval 不行

```cpp
#include <iostream>

constexpr int square(int x)
{
    return x * x;
}

consteval int compile_time_square(int x)
{
    return x * x;
}

int main()
{
    constexpr int a = square(5);              // 编译期计算
    int n = 7;
    int b = square(n);                        // 运行期调用，也允许

    constexpr int c = compile_time_square(6); // 必须编译期计算
    // int d = compile_time_square(n);        // ❌ 编译错误：n 不是编译期常量

    std::cout << "a = " << a << "\n";
    std::cout << "b = " << b << "\n";
    std::cout << "c = " << c << "\n";

    return 0;
}
```

**运行结果**：

```
a = 25
b = 49
c = 36
```

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | constexpr vs const | `constexpr int size = 10;`、数组大小 | constexpr 必须编译期初始化，可以用作数组大小 | const 可以在运行时初始化，不保证编译期 |
| 示例 2 | constexpr 函数 | 循环在 constexpr 函数中 | 同一个函数可编译期调用也可运行时调用 | 编译期调用时所有参数也必须是编译期常量 |
| 示例 3 | constexpr 用于 static_assert | `static_assert()` | 编译期检查，不通过直接编译失败 | static_assert 的参数必须是编译期常量 |
| 示例 4 | constexpr lambda | `constexpr auto f = [](int x) constexpr {...};` | lambda 也能编译期求值 | C++17 起支持，两个 constexpr 都要写 |
| 示例 5 | constexpr vs consteval | `consteval` | consteval 函数只能编译期调用 | C++20 才有 consteval |

## 常见错误

**错误 1：把运行时变量赋给 constexpr**

```cpp
int n = 5;
constexpr int x = n;  // ❌ n 不是编译期常量
```

正确做法：`const int x = n;`（用 const 而不是 constexpr）。

**错误 2：constexpr 函数假设所有情况都能编译期求值**

```cpp
constexpr int divide(int a, int b)
{
    return a / b;  // 编译期 b=0 会导致编译错误
}
constexpr int x = divide(10, 0);  // ❌ 编译错误！
```

正确做法：编译期调用时注意参数合法性。

**错误 3：constexpr 函数内部有未定义行为**

```cpp
constexpr int get(int* p)
{
    return *p;  // 编译期不能解引用空指针
}
constexpr int x = get(nullptr);  // ❌ 编译错误！
```

正确做法：constexpr 函数中避免未定义行为。

**错误 4：以为 constexpr 函数一定在编译期执行**

```cpp
constexpr int square(int x) { return x * x; }

int n = 5;
int y = square(n);  // ✅ 这是运行期调用，不是编译期调用
```

正确做法：需要强制编译期时，把结果放进 `constexpr` 变量、`static_assert`、模板参数中；C++20 起也可以使用 `consteval` 函数。

## 使用建议

1. **能用 constexpr 表达的就用 constexpr**：把运行时计算移到编译期，程序性能更好。
2. **constexpr 函数既编译期又运行期**：不必写两份代码。
3. **用 `static_assert` 做编译期检查**：配合 constexpr 函数非常强大。
4. **constexpr 不是"更快"的魔法**：对于小函数，编译器本来就会优化。但对于常量表、预计算等场景很有用。
5. **需要"必须编译期"才考虑 consteval**：普通工具函数优先写 constexpr，限制更少。

## 小结

- `constexpr` 变量必须在编译期确定，可以用作数组大小、模板参数等。
- `constexpr` 函数可以在编译期执行（参数也都是 constexpr 时）。
- `constexpr` 函数也可以运行期调用；`consteval` 才是必须编译期调用。
- `const` 强调"不可修改"，`constexpr` 强调"编译期求值"。
- C++17 起 lambda 可以用 `constexpr`。
- `static_assert` + constexpr 函数 = 编译期安全网。
