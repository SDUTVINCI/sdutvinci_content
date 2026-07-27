---
title: "std::optional"
---

## 本节解决什么问题

函数返回结果时，如何表示"没有结果"？

常见的做法各有缺点：

- 返回 `-1` 或 `nullptr`：语义不清晰，且 `-1` 可能是合法值。
- 返回 `bool` + 输出参数：代码冗长，需要先声明变量。
- 抛出异常：异常有开销，且"查不到"往往不算异常情况。

`std::optional<T>` 优雅地表达了"可能有值、也可能没有值"的语义。

## 这个特性是什么

`std::optional<T>` 是一个模板类，它要么包含一个 `T` 类型的值，要么为空（没有值）。它让"可能没有值"这个信息成为类型的一部分，编译器帮你检查。

## C++ 标准版本

C++17

## 需要的头文件

```cpp
#include <optional>
```

## 基本语法

```cpp
std::optional<T> opt;                    // 默认空
std::optional<T> opt = value;            // 有值
std::optional<T> opt = std::nullopt;     // 显式空

// 检查是否有值
if (opt.has_value()) { ... }
if (opt) { ... }                         // 隐式转 bool

// 获取值
T val = opt.value();                     // 若无值抛异常
T val = *opt;                            // 若无值是未定义行为
T val = opt.value_or(default);           // 若无值返回默认值
```

## 常用用法

| 操作 | 说明 |
|:---|:---|
| `opt.has_value()` | 是否有值 |
| `opt.value()` | 获取值（无值抛 `std::bad_optional_access`） |
| `*opt` | 获取值（无值行为未定义，先检查！） |
| `opt.value_or(default)` | 获取值，无值返回默认值 |
| `opt = std::nullopt` | 清空 |
| `opt.emplace(args...)` | 原地构造值 |

## 示例代码

### 示例 1：用 optional 表示"可能找不到"

```cpp
#include <iostream>
#include <optional>
#include <string>
#include <vector>

// 在 vector 中查找元素，返回位置（可能找不到）
std::optional<size_t> find_index(const std::vector<std::string>& v,
                                  const std::string& target)
{
    for (size_t i = 0; i < v.size(); ++i)
    {
        if (v[i] == target)
        {
            return i;  // 找到了
        }
    }
    return std::nullopt;  // 没找到
}

int main()
{
    std::vector<std::string> names = {"Alice", "Bob", "Charlie"};

    auto idx = find_index(names, "Bob");
    if (idx)
    {
        std::cout << "Found Bob at index " << *idx << "\n";
    }

    auto idx2 = find_index(names, "David");
    if (!idx2)
    {
        std::cout << "David not found\n";
    }

    // 使用 value_or 提供默认值
    std::cout << "Result: " << idx.value_or(999) << "\n";
    std::cout << "Result2: " << idx2.value_or(999) << "\n";

    return 0;
}
```

**运行结果**：

```
Found Bob at index 1
David not found
Result: 1
Result2: 999
```

### 示例 2：在示例 1 基础上，用 optional 表示"可能失败的转换"

```cpp
#include <iostream>
#include <optional>
#include <string>

// 字符串转整数，失败返回 nullopt
std::optional<int> to_int(const std::string& s)
{
    try
    {
        size_t pos;
        int val = std::stoi(s, &pos);
        if (pos == s.size())  // 整个字符串都被转换了
        {
            return val;
        }
    }
    catch (...) {}
    return std::nullopt;
}

int main()
{
    auto r1 = to_int("42");
    auto r2 = to_int("3.14");  // 带小数点
    auto r3 = to_int("hello"); // 不是数字

    std::cout << "\"42\" -> " << r1.value_or(-1) << "\n";
    std::cout << "\"3.14\" -> " << r2.value_or(-1) << "\n";
    std::cout << "\"hello\" -> " << r3.value_or(-1) << "\n";

    // 用 if 检查
    if (auto r = to_int("100"); r)
    {
        std::cout << "100 * 2 = " << (*r * 2) << "\n";
    }

    return 0;
}
```

**运行结果**：

```
"42" -> 42
"3.14" -> -1
"hello" -> -1
100 * 2 = 200
```

### 示例 3：在示例 2 基础上，用 optional 作为结构体成员（"可能没有"的字段）

```cpp
#include <iostream>
#include <optional>
#include <string>

struct Person
{
    std::string name;
    int age;
    std::optional<std::string> nickname;  // 可能没有昵称
    std::optional<int> score;              // 成绩可能尚未录入
};

void print_person(const Person& p)
{
    std::cout << "Name: " << p.name << "\n";
    std::cout << "Age: " << p.age << "\n";

    std::cout << "Nickname: " << p.nickname.value_or("(none)") << "\n";
    std::cout << "Score: " << p.score.value_or(-1) << "\n";

    std::cout << "---\n";
}

int main()
{
    Person p1{"Alice", 20, "Ali", 95};
    Person p2{"Bob", 22, std::nullopt, std::nullopt};  // 没有昵称和成绩

    print_person(p1);
    print_person(p2);

    return 0;
}
```

**运行结果**：

```
Name: Alice
Age: 20
Nickname: Ali
Score: 95
---
Name: Bob
Age: 22
Nickname: (none)
Score: -1
---
```

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | optional 返回查找结果 | `return i;` / `return std::nullopt;`、`if(opt)`、`*opt`、`value_or()` | optional 让"没有值"成为正常返回，语义清晰 | 解引用 `*opt` 前要确保有值 |
| 示例 2 | optional 表示转换失败 | `try/catch` 包装 + optional | 把异常转换成空 optional，调用者更易处理 | 也可以用 `std::from_chars`（C++17）更高效 |
| 示例 3 | optional 作为成员变量 | `std::optional<std::string>` 成员 | "可选字段"用 optional 比用 magic value（如 -1）更清晰 | optional 本身占用额外空间（T 的大小 + bool + padding） |

## optional 适合"可能没有"，不适合"错误详情很多"

`optional` 表达的是：这个值可能存在，也可能不存在。它不负责说明“为什么失败”。

| 场景 | 推荐 |
|:---|:---|
| 查找名字，可能找不到 | `std::optional<size_t>` |
| 配置项可能没填 | `std::optional<std::string>` |
| 字符串转数字，只关心成不成功 | `std::optional<int>` |
| 打开文件失败，需要知道权限/路径/格式错误 | 错误码、异常、或 `expected` 类工具 |
| 网络请求失败，需要错误类型和错误消息 | 错误对象，不要只用 optional |

### 示例 4：optional 和错误信息的区别

```cpp
#include <iostream>
#include <optional>
#include <string>

// optional 表示“可能有值，也可能没有值”的结果。
std::optional<int> parse_port_simple(const std::string& text)
{
    try
    {
        int port = std::stoi(text);
        if (port >= 0 && port <= 65535)
        {
            return port;
        }
    }
    catch (...) {}
    return std::nullopt;
}

struct ParseResult
{
    bool ok;
    int value;
    std::string error;
};

ParseResult parse_port_with_error(const std::string& text)
{
    try
    {
        int port = std::stoi(text);
        if (port < 0 || port > 65535)
        {
            return {false, 0, "port out of range"};
        }
        return {true, port, ""};
    }
    catch (...)
    {
        return {false, 0, "not a number"};
    }
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    auto simple = parse_port_simple("70000");
    std::cout << "simple ok? " << (simple ? "yes" : "no") << "\n";

    auto detailed = parse_port_with_error("70000");
    if (!detailed.ok)
    {
        std::cout << "error: " << detailed.error << "\n";
    }

    return 0;
}
```

**运行结果**：

```
simple ok? no
error: port out of range
```

如果调用者只关心“有没有值”，`optional` 很合适；如果调用者需要知道失败原因，单独的 `optional` 就不够了。

## 常见错误

**错误 1：不检查直接 `*opt`**

```cpp
std::optional<int> opt;
std::cout << *opt;  // ❌ 未定义行为！opt 为空
```

正确做法：先 `if (opt)` 检查，或用 `opt.value_or(default)`。

**错误 2：把 `std::nullopt` 和 `nullptr` 混淆**

```cpp
std::optional<int> opt = nullptr;  // ❌ 编译错误！应该用 std::nullopt
```

正确做法：`opt = std::nullopt;`

**错误 3：忘记 `value()` 会在无值时抛异常**

```cpp
std::optional<int> opt;
std::cout << opt.value();  // ❌ 抛出 std::bad_optional_access
```

正确做法：用 `value_or(default)` 或先检查。

## 使用建议

1. **函数可能"没有结果"时返回 `std::optional<T>`**：比返回 -1/nullptr 更安全。
2. **结构体中"可选字段"用 optional**：比 magic number 语义更清晰。
3. **默认用 `value_or()` 而不是 `*opt`**：`value_or()` 更安全。
4. **`std::optional` 有额外空间开销**：等于 `sizeof(T) + sizeof(bool) + padding`，小对象影响不大。
5. **C++23 引入了 `std::optional` 的 monadic 操作**：`.and_then()`, `.transform()`, `.or_else()` 链式调用更优雅。
6. **需要错误详情时别只用 optional**：optional 只表达有没有值，不表达为什么没有。

## 小结

- `std::optional<T>` 要么包含一个 `T`，要么为空。
- 解决了函数"可能没结果"的语义问题，替代 magic value。
- 用 `has_value()` 或 `if(opt)` 检查，用 `value_or(default)` 安全获取值。
- 可用于函数返回值、结构体成员、函数参数（但参数不推荐用 optional）。
