---
title: "auto"
---

## 本节解决什么问题

在 C++ 中，很多类型的名字很长、很难写，也容易写错，比如：

```cpp
std::map<std::string, std::vector<int>>::const_iterator it = m.begin();
std::shared_ptr<MyClass> ptr = std::make_shared<MyClass>();
```

`auto` 让编译器自动帮我们推导类型，减少冗长的类型声明，让代码更简洁、更易维护。

## 这个特性是什么

`auto` 关键字告诉编译器："请帮我根据初始化的值，自动推导出这个变量的类型"。它并不是弱化类型安全——推导出来的类型在编译时就已经确定，所以仍然是强类型的。

## C++ 标准版本

C++11（基础用法），C++14 增加了 `auto` 作为函数返回类型推导，C++17 增加了 `auto` 在结构化绑定等场景中的使用。

## 需要的头文件

不需要额外头文件。`auto` 是语言关键字。

## 基本语法

```cpp
auto 变量名 = 表达式;        // 编译器根据表达式推导类型
const auto 变量名 = 表达式;  // 推导后加 const
auto& 变量名 = 表达式;       // 推导为引用类型
const auto& 变量名 = 表达式; // 推导为只读引用类型
```

## 常用用法

| 用法 | 说明 | 示例 |
|:---|:---|:---|
| `auto` | 自动推导值类型（会丢失引用和 const） | `auto x = 42;` → int |
| `const auto` | 推导为只读类型 | `const auto s = "hello";` |
| `auto&` | 推导为引用（可修改原值） | `auto& x = vec[0];` |
| `const auto&` | 推导为只读引用（不拷贝、不可修改） | `const auto& x = get_value();` |
| `auto*` | 推导为指针 | `auto* p = &x;` |
| `decltype(auto)` | 保留引用和 cv 限定的完美转发（C++14） | `decltype(auto) x = get_ref();` |

## 什么时候用哪一种

`auto` 最容易被误解的地方是：它不是"自动变成你想要的类型"，而是按固定规则推导。尤其遇到引用、`const`、函数返回值时，要主动写清楚你的意图。

| 场景 | 推荐写法 | 原因 |
|:---|:---|:---|
| 局部变量类型很明显 | `auto x = expr;` | 少写重复类型 |
| 只读遍历大对象 | `const auto& x` | 避免拷贝，同时不允许修改 |
| 要修改原对象 | `auto& x` | 明确拿到引用 |
| 可能为空的指针 | `auto* p = get();` | 让读者一眼看出这是指针 |
| 函数返回引用且必须保留引用 | `decltype(auto)` | `auto` 返回值会丢失引用 |
| 类型本身是业务含义 | 显式类型 | 例如 `Meter distance = ...` 比 `auto distance` 更清楚 |

## 示例代码

### 示例 1：基本类型推导

```cpp
#include <iostream>
#include <string>

int main()
{
    auto n = 42;              // int
    auto d = 3.14;            // double
    auto c = 'A';             // char
    auto s = "hello";         // const char*
    auto str = std::string("world");  // std::string

    std::cout << "n = " << n << "\n";
    std::cout << "d = " << d << "\n";
    std::cout << "c = " << c << "\n";
    std::cout << "str = " << str << "\n";

    return 0;
}
```

**运行结果**：

```
n = 42
d = 3.14
c = A
str = world
```

### 示例 2：在示例 1 基础上，auto& 和 const auto& 的区别

```cpp
#include <iostream>

int main()
{
    int x = 10;

    auto a = x;         // a 是 int，拷贝了 x
    auto& b = x;        // b 是 int&，是 x 的引用
    const auto& c = x;  // c 是 const int&，只读引用

    a = 100;
    std::cout << "after a=100, x = " << x << "\n";  // x 不变

    b = 200;
    std::cout << "after b=200, x = " << x << "\n";  // x 变了

    // c = 300;  // ❌ 编译错误！c 是只读引用

    return 0;
}
```

**运行结果**：

```
after a=100, x = 10
after b=200, x = 200
```

### 示例 3：在示例 2 基础上，用 auto 简化迭代器和复杂类型

```cpp
#include <iostream>
#include <vector>
#include <map>
#include <memory>
#include <string>

int main()
{
    std::vector<std::string> names = {"Alice", "Bob", "Charlie"};

    // 不用 auto 的写法（类型名很长）
    for (std::vector<std::string>::const_iterator it = names.begin();
         it != names.end(); ++it)
    {
        std::cout << *it << " ";
    }
    std::cout << "\n";

    // 用 auto 简化（同样清晰）
    for (auto it = names.begin(); it != names.end(); ++it)
    {
        std::cout << *it << " ";
    }
    std::cout << "\n";

    // 智能指针用 auto 简化
    auto ptr = std::make_shared<int>(42);
    std::cout << "*ptr = " << *ptr << "\n";

    return 0;
}
```

**运行结果**：

```
Alice Bob Charlie 
Alice Bob Charlie 
*ptr = 42
```

### 示例 4：用 auto 遍历容器时注意拷贝 vs 引用

```cpp
#include <iostream>
#include <vector>
#include <string>

int main()
{
    std::vector<std::string> names = {"Alice", "Bob", "Charlie"};

    // ❌ 错误用法：auto 会拷贝每个元素（低效）
    std::cout << "auto (copies): ";
    for (auto s : names)
    {
        s = "X";  // 修改的是拷贝，不影响原容器
    }
    for (auto s : names)
    {
        std::cout << s << " ";
    }
    std::cout << "(unchanged)\n";

    // ✅ 正确用法：auto& 修改原容器中的元素
    for (auto& s : names)
    {
        s = "X";
    }
    std::cout << "auto& (references): ";
    for (auto s : names)
    {
        std::cout << s << " ";
    }
    std::cout << "(modified)\n";

    return 0;
}
```

**运行结果**：

```
auto (copies): Alice Bob Charlie (unchanged)
auto& (references): X X X (modified)
```

### 示例 5：在示例 4 基础上，auto 返回值和 decltype(auto) 的区别

```cpp
#include <iostream>

int global_score = 80;

int& score_ref()
{
    return global_score;
}

// auto 作为函数返回类型时，会按"值"返回，引用会被丢掉
auto read_score()
{
    return score_ref();
}

// decltype(auto) 会保留 score_ref() 的 int& 返回类型
decltype(auto) borrow_score()
{
    return score_ref();
}

int main()
{
    auto a = score_ref();       // a 是 int，拷贝
    a = 90;
    std::cout << "after auto a = 90, global_score = " << global_score << "\n";

    auto& b = score_ref();      // b 是 int&，引用
    b = 90;
    std::cout << "after auto& b = 90, global_score = " << global_score << "\n";

    auto c = read_score();      // c 是 int，read_score 本身也返回拷贝
    c = 100;
    std::cout << "after auto c = 100, global_score = " << global_score << "\n";

    decltype(auto) d = borrow_score(); // d 是 int&，仍然引用 global_score
    d = 100;
    std::cout << "after decltype(auto) d = 100, global_score = " << global_score << "\n";

    return 0;
}
```

**运行结果**：

```
after auto a = 90, global_score = 80
after auto& b = 90, global_score = 90
after auto c = 100, global_score = 90
after decltype(auto) d = 100, global_score = 100
```

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | 基本类型推导 | `auto x = 表达式` | 最简单的情况，类型由初始化值决定 | 字符串字面量推导为 `const char*` |
| 示例 2 | auto vs auto& vs const auto& | `auto&`、`const auto&` | `auto` 会丢失引用和顶层 const，要用 `auto&` 才能引用原变量 | 遍历容器元素时要注意 |
| 示例 3 | 简化复杂类型 | 迭代器、智能指针用 auto | 类型名太长时用 auto 保持可读性 | auto 不弱化类型安全，只是让编译器帮你写 |
| 示例 4 | auto 遍历时的拷贝陷阱 | 范围 for + auto/auto& | `auto` 遍历拷贝元素，`auto&` 引用元素 | 想修改原容器必须用 `auto&` |
| 示例 5 | auto 返回值 vs decltype(auto) | `auto` 函数返回、`decltype(auto)` | 函数返回引用时，普通 `auto` 会退化成值返回 | `decltype(auto)` 很强，但也要确认不会返回悬垂引用 |

## 常见错误

**错误 1：以为 `auto` 能自动推导为引用**

```cpp
int x = 10;
auto y = x;
y = 20;  // x 不会变！y 是拷贝不是引用
```

正确做法：需要修改原变量用 `auto&`。

**错误 2：用 `auto` 声明函数参数**

```cpp
void func(auto x) { ... }  // C++20 之前是错误！（除非是泛型 lambda）
```

正确做法：普通函数参数不能用 `auto`（C++20 允许，但那是模板语法）。

**错误 3：`auto` 不能用于多变量声明**

```cpp
auto x = 1, y = 3.14;  // ❌ 编译错误！推导类型不一致
```

正确做法：每个 auto 变量各自声明。

**错误 4：滥用 auto 让业务含义消失**

```cpp
auto timeout = 3000;  // 3000 是毫秒？秒？次数？
```

正确做法：类型或命名要能表达含义。例如 `std::chrono::milliseconds timeout{3000};`，或者至少写成 `auto timeout_ms = 3000;`。

## 使用建议

1. **能用 auto 的地方尽量用**：减少重复类型声明，让代码更简洁。
2. **遍历容器要用 `const auto&`**（不修改）或 `auto&`（需要修改），避免意外拷贝。
3. **明确需要引用的地方要写 `auto&`**：`auto` 不会自动推导出引用。
4. **`auto` 不能完全替代显式类型**：当类型不明显时（如从函数返回值推导），显式写类型可能更清晰。
5. **`decltype(auto)` 只在确实要保留引用时使用**：它更精确，也更容易把悬垂引用暴露出来。

## 小结

- `auto` 让编译器自动推导变量类型，减少冗长类型声明。
- `auto` 会丢失引用和顶层 const，遍历容器要用 `const auto&` 或 `auto&`。
- 特别适合简化迭代器、智能指针等复杂类型。
- `auto` 函数返回值默认按值返回；要保留引用语义时用 `decltype(auto)`。
- `auto` 仍然是强类型的，编译期类型就确定了。
