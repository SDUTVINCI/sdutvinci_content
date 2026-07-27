---
title: "std::format / std::print"
---

## 本节解决什么问题

C++ 中输出格式化的字符串一直比较麻烦：

- `printf`：快但类型不安全，格式字符串错误会导致崩溃。
- `std::cout`：类型安全但写起来冗长，格式控制不方便。
- `std::stringstream`：功能全但非常啰嗦。

C++20/23 引入了 `std::format`（格式化字符串）和 `std::print`（直接输出），结合了 Python 风格的简洁和 C++ 的类型安全 + 高性能。

## 这个特性是什么

- `std::format`（C++20）：类似 Python 的 `f"{name}: {score}"`，返回格式化后的 `std::string`。
- `std::print`（C++23）：类似 Python 的 `print()`，直接把格式化结果输出到 stdout。

底层基于 `{fmt}` 库，性能极高——接近甚至超过 `printf`。

## C++ 标准版本

- `std::format`：C++20
- `std::print`：C++23

## 需要的头文件

```cpp
#include <format>    // for std::format (C++20)
#include <print>     // for std::print, std::println (C++23)
```

## 基本语法

```cpp
// std::format：返回 string
std::string s = std::format("Hello, {}!", name);
std::string s2 = std::format("{0} + {1} = {2}", a, b, a + b);

// std::print：直接输出（不自动换行）
std::print("x = {}, y = {}", x, y);

// std::println：输出并换行
std::println("Hello, {}!", name);

// 指定输出目标：stdout / stderr 是 FILE*
std::println(stdout, "normal message");
std::println(stderr, "error message");

// 格式控制
std::format("{:.2f}", 3.14159);     // "3.14" —— 保留 2 位小数
std::format("{:>10}", 42);          // "        42" —— 右对齐，宽度 10
std::format("{:<10}", 42);          // "42        " —— 左对齐
std::format("{:^10}", 42);          // "    42    " —— 居中
std::format("{:#x}", 255);          // "0xff" —— 十六进制带前缀
std::format("{:04d}", 7);           // "0007" —— 前导零填充
```

## 输出方式对比

| 方式 | 性能 | 类型安全 | 简洁 | 出现版本 |
|:---|:---|:---|:---|:---|
| `printf` | 快 | ❌ | ✅ | C |
| `std::cout` | 慢 | ✅ | ❌ | C++98 |
| `std::format` | 很快 | ✅ | ✅ | C++20 |
| `std::print` | 很快 | ✅ | ✅ | C++23 |

## std::print 输出到哪里

C++23 标准库里的 `std::print` / `std::println` 常用两类写法：

| 写法 | 输出目标 |
|:---|:---|
| `std::println("x = {}", x)` | 默认输出到标准输出，也就是 `stdout` |
| `std::println(stdout, "x = {}", x)` | 明确输出到 `stdout` |
| `std::println(stderr, "error = {}", code)` | 输出到标准错误 `stderr` |

这里的 `stdout` 和 `stderr` 是 C 标准库里的 `FILE*`，需要包含 `<cstdio>`。

注意：标准 C++23 的 `std::println` 第一个参数不是 `std::cout`。`std::cout` 是 C++ 的 `std::ostream` 对象，属于 `<iostream>`。你可能在 `{fmt}` 库或某些扩展里见过类似 `fmt::print(std::cout, "...")` 的写法，那和标准库 `std::println` 不是同一个接口。

## 示例代码

### 示例 1：std::format 基本用法

```cpp
#include <iostream>
#include <format>
#include <string>

int main()
{
    std::string name = "Alice";
    int age = 25;
    double score = 92.5;

    // 基本占位符
    std::string s1 = std::format("Name: {}, Age: {}, Score: {}", name, age, score);
    std::cout << s1 << "\n";

    // 指定顺序
    std::string s2 = std::format("{1} is {0} years old", age, name);
    std::cout << s2 << "\n";

    // 保留 2 位小数
    std::string s3 = std::format("Score: {:.2f}", score);
    std::cout << s3 << "\n";

    return 0;
}
```

**运行结果**：

```
Name: Alice, Age: 25, Score: 92.5
Alice is 25 years old
Score: 92.50
```

### 示例 2：在示例 1 基础上，格式控制和对齐

```cpp
#include <iostream>
#include <format>

int main()
{
    // 右对齐，宽度 10
    std::cout << std::format("[{:>10}]\n", 42);

    // 左对齐，宽度 10
    std::cout << std::format("[{:<10}]\n", 42);

    // 居中，宽度 10
    std::cout << std::format("[{:^10}]\n", 42);

    // 前导零
    std::cout << std::format("{:05d}\n", 7);

    // 十六进制
    std::cout << std::format("hex = {:#x}, oct = {:#o}\n", 255, 255);

    // 打印一个简单的表格
    std::cout << std::format("{:<10} {:>5} {:>7}\n", "Name", "Age", "Score");
    std::cout << std::format("{:<10} {:>5} {:>7.1f}\n", "Alice", 25, 92.5);
    std::cout << std::format("{:<10} {:>5} {:>7.1f}\n", "Bob", 22, 88.0);
    std::cout << std::format("{:<10} {:>5} {:>7.1f}\n", "Charlie", 24, 78.5);

    return 0;
}
```

**运行结果**：

```
[        42]
[42        ]
[    42    ]
00007
hex = 0xff, oct = 0377
Name         Age   Score
Alice         25    92.5
Bob           22    88.0
Charlie       24    78.5
```

### 示例 3：std::print 直接输出（C++23）

```cpp
#include <cstdio>
#include <print>
#include <string>

int main()
{
    std::string name = "World";
    int value = 42;

    // 直接输出，不换行
    std::print("Hello, ");
    std::print("{}", name);
    std::print("!\n");

    // 输出到 stdout 并换行
    std::println("The answer is {}", value);

    // 第一个参数也可以显式写 stdout
    std::println(stdout, "stdout: {}", name);

    // 带格式输出
    std::println("pi = {:.3f}", 3.1415926);

    // 输出到 stderr
    std::println(stderr, "Error: something went wrong!");

    return 0;
}
```

**运行结果**：

```
Hello, World!
The answer is 42
stdout: World
pi = 3.142
Error: something went wrong!
```

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | format 基本用法 | `std::format("...", arg1, arg2)`、`{}` 占位符 | Python 风格 + C++ 类型安全 | 参数个数要和 {} 个数匹配 |
| 示例 2 | 格式控制和表格 | `{:>10}`、`{:.2f}`、`{:#x}` | 内置格式说明符比 printf 更丰富 | 格式控制符在 `{}` 内，`:` 后面 |
| 示例 3 | print/println 直接输出 | `std::println()`、`std::print()`、`stdout`、`stderr` | 不需要 `std::cout`，直接格式化输出 | 标准接口接收的是 `FILE*`，不是 `std::cout` |

## 常见错误

**错误 1：格式说明符 `:` 前忘了写索引**

```cpp
std::format("{.2f}", 3.14);       // ❌ 缺少 : 前的占位符
```

正确做法：`std::format("{:.2f}", 3.14);` 或 `std::format("{}", 3.14);`

**错误 2：参数个数不匹配**

```cpp
std::format("{}, {}", 1);  // ❌ 有 2 个占位符，只有 1 个参数
```

编译时报错（这是 format 比 printf 安全的地方）。

**错误 3：用 format 输出的字符串中包含 `{` 或 `}`**

```cpp
std::format("Set = {1, 2, 3}");  // ❌ {1, 2, 3} 会被误解为格式说明
```

正确做法：用 `{{` 表示字面量 `{`，`}}` 表示字面量 `}`：
```cpp
std::format("Set = {{1, 2, 3}}");  // 输出：Set = {1, 2, 3}
```

## 使用建议

1. **C++20 项目用 `std::format` 代替 `stringstream` 和 `sprintf`**：代码简洁、类型安全、性能高。
2. **C++23 项目用 `std::print`/`std::println` 简化格式化输出**：默认输出到 `stdout`，也可以传 `stderr`。
3. **格式说明符和 Python 很像**：如果你熟悉 Python 的格式化语法，上手很快。
4. **如果你的编译器还不支持 C++20/23**，可以使用 `{fmt}` 库（https://github.com/fmtlib/fmt），它是标准化的基础。

## 小结

- `std::format("{} + {} = {}", a, b, c)` 返回格式化字符串。
- `std::print("...")` / `std::println("...")` 直接输出到 `stdout`。
- `std::println(stderr, "...")` 可以输出到标准错误。
- 类型安全 + 格式强大 + 性能高：结合了 printf 的速度和 cout 的安全性。
- 格式控制：`{:.2f}` 小数位数、`{:>10}` 对齐、`{:#x}` 进制、`{:04d}` 补零。
