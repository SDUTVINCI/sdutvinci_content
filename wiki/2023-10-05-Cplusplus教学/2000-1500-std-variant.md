---
vinciId: 253e20b2-f635-5ae3-8aa1-7302f7003577
title: std::variant
description: 本节解决什么问题
authors:
  - dongjiahui
publishedAt: 2023-10-05T00:00:00.000Z
updatedAt: 2026-08-12T11:38:56.804Z
---

## 本节解决什么问题

有时候一个变量需要存储"可能是 int，也可能是 string，也可能是 double"的值。传统的做法是 `union`（C 语言），但它不类型安全——你不知道当前存的是哪种类型，访问错了就崩溃。

`std::variant` 是类型安全的联合体，能存储多种类型之一，并且**知道当前存的是哪种类型**。

## 这个特性是什么

`std::variant<T1, T2, ...>` 是 C++17 引入的类型安全的联合体。在同一时刻，它只存储其中一种类型的值。访问时编译器会帮你检查，不会出现"访问了错误类型"的问题。

## C++ 标准版本

C++17

## 需要的头文件

```cpp
#include <variant>
```

## 基本语法

```cpp
std::variant<int, double, std::string> v;

v = 42;                      // 存 int
v = 3.14;                    // 存 double
v = std::string("hello");   // 存 string

// 访问方式 1：std::get<T>(v) —— 类型不对抛异常
int n = std::get<int>(v);

// 访问方式 2：std::get_if<T>(&v) —— 类型不对返回 nullptr
if (auto* p = std::get_if<int>(&v)) { ... }

// 访问方式 3：std::visit —— 用 visitor 模式处理所有可能的类型
std::visit([](auto&& val) { ... }, v);

// 查询当前存储的类型的索引
size_t idx = v.index();  // 0-based
```

## 常用用法

| 操作 | 说明 |
|:---|:---|
| `v = value;` | 赋值（自动切换类型） |
| `v.emplace<T>(args...)` | 原地构造 |
| `std::get<T>(v)` | 获取值（类型不对抛 `std::bad_variant_access`） |
| `std::get_if<T>(&v)` | 安全获取（类型不对返回 nullptr） |
| `v.index()` | 返回当前类型的索引（0-based） |
| `std::visit(visitor, v)` | 用 visitor 模式处理 |
| `std::holds_alternative<T>(v)` | 判断是否持有 T 类型 |

## 示例代码

### 示例 1：variant 基本用法——存不同类型的值

```cpp
#include <iostream>
#include <variant>
#include <string>
#include <type_traits>

int main()
{
    // v 可以存 int、double 或 string
    std::variant<int, double, std::string> v;

    v = 42;
    std::cout << "int: " << std::get<int>(v) << "\n";

    v = 3.14;
    std::cout << "double: " << std::get<double>(v) << "\n";

    v = std::string("hello");
    std::cout << "string: " << std::get<std::string>(v) << "\n";

    // 查看当前类型索引
    std::cout << "current index: " << v.index() << "\n";  // 2 (string)

    return 0;
}
```

**运行结果**：

```
int: 42
double: 3.14
string: hello
current index: 2
```

### 示例 2：在示例 1 基础上，用 get_if 安全访问

```cpp
#include <iostream>
#include <variant>
#include <string>

void print_value(const std::variant<int, double, std::string>& v)
{
    // 安全方式：逐个尝试，get_if 返回指针
    if (auto* p = std::get_if<int>(&v))
    {
        std::cout << "int: " << *p << "\n";
    }
    else if (auto* p = std::get_if<double>(&v))
    {
        std::cout << "double: " << *p << "\n";
    }
    else if (auto* p = std::get_if<std::string>(&v))
    {
        std::cout << "string: " << *p << "\n";
    }
}

int main()
{
    std::variant<int, double, std::string> v;

    v = 42;
    print_value(v);

    v = 3.14159;
    print_value(v);

    v = std::string("C++17");
    print_value(v);

    return 0;
}
```

**运行结果**：

```
int: 42
double: 3.14159
string: C++17
```

### 示例 3：在示例 2 基础上，用 std::visit 处理所有类型

```cpp
#include <iostream>
#include <variant>
#include <string>

int main()
{
    std::variant<int, double, std::string> v;

    // std::visit 配合泛型 lambda 优雅处理所有类型
    auto printer = [](const auto& val) {
        std::cout << "value: " << val << "\n";
    };

    v = 42;
    std::visit(printer, v);

    v = 2.718;
    std::visit(printer, v);

    v = std::string("hello variant");
    std::visit(printer, v);

    // 也可以返回不同类型的值
    auto to_double = [](const auto& val) -> double {
        if constexpr (std::is_same_v<std::decay_t<decltype(val)>, std::string>)
        {
            return 0.0;  // string 不能转 double
        }
        else
        {
            return static_cast<double>(val);
        }
    };

    v = 10;
    std::cout << "to_double: " << std::visit(to_double, v) << "\n";

    return 0;
}
```

**运行结果**：

```
value: 42
value: 2.718
value: hello variant
to_double: 10
```

### 示例 4：在示例 3 基础上，用 variant 表示消息类型

```cpp
#include <iostream>
#include <variant>
#include <string>

// 定义消息类型
struct TextMessage { std::string text; };
struct NumberMessage { int number; };
struct QuitMessage {};

using Message = std::variant<TextMessage, NumberMessage, QuitMessage>;

// 处理消息的 visitor
struct MessageHandler
{
    void operator()(const TextMessage& msg) const
    {
        std::cout << "Text: " << msg.text << "\n";
    }
    void operator()(const NumberMessage& msg) const
    {
        std::cout << "Number: " << msg.number << "\n";
    }
    void operator()(const QuitMessage&) const
    {
        std::cout << "Quit!\n";
    }
};

int main()
{
    Message msg;

    msg = TextMessage{"Hello World"};
    std::visit(MessageHandler{}, msg);

    msg = NumberMessage{42};
    std::visit(MessageHandler{}, msg);

    msg = QuitMessage{};
    std::visit(MessageHandler{}, msg);

    return 0;
}
```

**运行结果**：

```
Text: Hello World
Number: 42
Quit!
```

### `std::visit` 与 Visitor 机制

上面的代码中：

```cpp
std::visit(MessageHandler{}, msg);
```

这一句刚开始看起来可能比较奇怪。

先记住 `std::visit` 最基本的形式：

```cpp
std::visit(visitor, variant对象);
```

其中：

- 第二个参数是 `std::variant`
- 第一个参数是一个**可调用对象（Callable）**
- `std::visit` 会根据 `variant` 当前保存的数据类型，调用对应的处理函数

例如：

```cpp
Message msg;

msg = TextMessage{"Hello World"};
std::visit(MessageHandler{}, msg);
```

此时 `msg` 内部保存的是：

```cpp
TextMessage
```

因此 `std::visit` 会把这个 `TextMessage` 取出来，并交给 `MessageHandler` 处理。

---

#### `MessageHandler{}` 是什么？

这里：

```cpp
MessageHandler{}
```

不是函数，也不是特殊语法。

它就是创建了一个临时的 `MessageHandler` 对象。

例如：

```cpp
MessageHandler handler;
```

和：

```cpp
MessageHandler{}
```

创建的对象类型是一样的，只不过后者是临时对象。

因此：

```cpp
std::visit(MessageHandler{}, msg);
```

也可以写成：

```cpp
MessageHandler handler;
std::visit(handler, msg);
```

---

#### 为什么一个结构体可以像函数一样调用？

因为 `MessageHandler` 重载了：

```cpp
operator()
```

例如：

```cpp
struct MessageHandler
{
    void operator()(const TextMessage& msg) const
    {
        std::cout << "Text: " << msg.text << "\n";
    }
};
```

创建对象以后：

```cpp
MessageHandler handler;
```

就可以直接这样调用：

```cpp
handler(TextMessage{"Hello"});
```

看起来像是在调用一个函数。

实际上等价于：

```cpp
handler.operator()(TextMessage{"Hello"});
```

所以这种重载了：

```cpp
operator()
```

的对象也叫：

**函数对象（Function Object / Functor）**。

---

#### 为什么这里有三个 `operator()`？

因为：

```cpp
using Message =
    std::variant<TextMessage, NumberMessage, QuitMessage>;
```

`Message` 可能保存三种不同的数据：

```cpp
TextMessage
NumberMessage
QuitMessage
```

因此 `MessageHandler` 分别准备了三个处理函数：

```cpp
void operator()(const TextMessage& msg) const
{
    std::cout << "Text: " << msg.text << "\n";
}
```

处理：

```cpp
TextMessage
```

---

```cpp
void operator()(const NumberMessage& msg) const
{
    std::cout << "Number: " << msg.number << "\n";
}
```

处理：

```cpp
NumberMessage
```

---

```cpp
void operator()(const QuitMessage&) const
{
    std::cout << "Quit!\n";
}
```

处理：

```cpp
QuitMessage
```

它们虽然函数名都是：

```cpp
operator()
```

但是参数类型不同，因此属于**函数重载**。

---

#### `std::visit` 到底做了什么？

例如：

```cpp
msg = TextMessage{"Hello World"};

std::visit(MessageHandler{}, msg);
```

此时 `msg` 保存的是：

```cpp
TextMessage
```

可以粗略理解为 `std::visit` 做了：

```cpp
MessageHandler{}(TextMessage{"Hello World"});
```

于是编译器找到：

```cpp
void operator()(const TextMessage& msg) const
```

最终输出：

```text
Text: Hello World
```

---

再例如：

```cpp
msg = NumberMessage{42};

std::visit(MessageHandler{}, msg);
```

可以粗略理解成：

```cpp
MessageHandler{}(NumberMessage{42});
```

于是调用：

```cpp
void operator()(const NumberMessage& msg) const
```

输出：

```text
Number: 42
```

---

最后：

```cpp
msg = QuitMessage{};

std::visit(MessageHandler{}, msg);
```

则会选择：

```cpp
void operator()(const QuitMessage&) const
```

输出：

```text
Quit!
```

整个过程可以理解为：

```text
                         std::variant
                              │
              ┌───────────────┼───────────────┐
              │               │               │
       TextMessage      NumberMessage     QuitMessage
              │               │               │
              ▼               ▼               ▼
 operator(TextMessage) operator(NumberMessage) operator(QuitMessage)
```

`std::visit` 的作用就是：

> 查看 `variant` 当前保存的是哪一种类型，然后自动调用 visitor 中能够处理这个类型的函数。

---

#### `std::visit` 第一个参数必须是结构体吗？

不是。

`std::visit` 第一个参数本质上只要求是一个：

**可调用对象（Callable）**。

也就是说，只要一个东西能够像下面这样调用：

```cpp
对象(参数);
```

就可以作为 visitor。

常见的可调用对象包括：

```text
普通函数
Lambda
函数对象（重载 operator() 的 class / struct）
std::function
```

上面的例子使用的是：

```cpp
MessageHandler{}
```

它属于：

```text
结构体对象
    ↓
重载 operator()
    ↓
函数对象 Functor
    ↓
可以作为 std::visit 的 Visitor
```

---

#### Lambda 为什么也能作为 Visitor？

例如：

```cpp
std::visit(
    [](const auto& msg)
    {
        std::cout << "收到一条消息\n";
    },
    msg
);
```

这里第一个参数就是一个 Lambda。

Lambda 本身也是一种可调用对象。

例如：

```cpp
auto f = [](int x)
{
    std::cout << x << "\n";
};

f(10);
```

能够像函数一样调用：

```cpp
f(10);
```

因此它也可以传给：

```cpp
std::visit
```

实际上，可以把 Lambda 粗略理解成编译器自动生成了一个匿名的函数对象：

```cpp
struct 某个匿名类型
{
    void operator()(int x) const
    {
        std::cout << x << "\n";
    }
};
```

所以从思想上来说：

```cpp
Lambda
```

和：

```cpp
重载了 operator() 的 struct/class
```

非常相似。

---

#### 为什么这个例子更适合用结构体 Visitor？

如果只有一种简单操作，Lambda 很方便：

```cpp
[](const auto& value)
{
    // ...
}
```

但是这里需要针对不同类型执行完全不同的逻辑：

```cpp
TextMessage
NumberMessage
QuitMessage
```

使用多个 `operator()` 重载会非常直观：

```cpp
struct MessageHandler
{
    void operator()(const TextMessage& msg) const
    {
        // 处理文本消息
    }

    void operator()(const NumberMessage& msg) const
    {
        // 处理数字消息
    }

    void operator()(const QuitMessage&) const
    {
        // 处理退出消息
    }
};
```

这样每种消息类型都有自己独立的处理逻辑。

---

#### 核心理解

可以把：

```cpp
std::visit(MessageHandler{}, msg);
```

拆成两个部分理解。

首先：

```cpp
MessageHandler{}
```

表示：

```text
创建一个 MessageHandler 临时对象
```

由于它重载了：

```cpp
operator()
```

所以它是一个可调用对象。

然后：

```cpp
std::visit(..., msg);
```

负责：

```text
查看 msg 当前保存的类型
        ↓
取出对应的数据
        ↓
调用 MessageHandler 对应的 operator()
```

因此：

```cpp
std::visit(MessageHandler{}, msg);
```

可以概括成一句话：

> 根据 `msg` 当前保存的数据类型，让 `MessageHandler` 自动选择对应的 `operator()` 进行处理。

也可以记成：

```cpp
std::visit(visitor, variant);
```

即：

```text
visit
 │
 ├── 看 variant 当前是什么类型
 │
 ├── 把里面的数据取出来
 │
 └── 用这个数据调用 visitor
```

其中 visitor 并不是某一种固定语法，而是任何能够被调用的对象。

在本例中：

```cpp
MessageHandler{}
```

就是一个通过重载 `operator()` 实现的函数对象。


## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | 基本赋值和 get | `std::variant<int, double, string>`、`std::get<T>(v)` | variant 类型安全，赋值时自动切换类型 | `get<T>` 类型不对会抛异常 |
| 示例 2 | get_if 安全访问 | `std::get_if<T>(&v)` | 返回指针，类型不对返回 nullptr | 比 get 更安全，推荐使用 |
| 示例 3 | visit 模式 | `std::visit(lambda, v)` | visit 强制覆盖所有类型，是 variant 的最佳访问方式 | 泛型 lambda + visit 是最简洁的组合 |
| 示例 4 | 消息分发模式 | struct visitor + variant | 用 variant + visitor 实现类型安全的消息处理 | visitor 必须为每种类型都提供 operator() |

## variant 适合"有限几种类型之一"

`variant` 不是为了替代所有继承和多态。它最适合这种情况：类型种类有限，而且你希望编译器提醒你把每种情况都处理掉。

| 场景 | 推荐 |
|:---|:---|
| 消息只有 Text / Number / Quit 三类 | `std::variant` |
| 状态只有 Idle / Running / Error 几类 | `std::variant` |
| 解析结果可能是 int / double / string | `std::variant` |
| 类型种类很多且需要运行时扩展插件 | 继承 + 虚函数 |
| 所有对象共享一套接口 | 多态接口更自然 |

### 示例 5：用 variant 表示状态机

```cpp
#include <iostream>
#include <string>
#include <type_traits>
#include <variant>

struct Idle {};
struct Running
{
    int task_id;
};
struct Error
{
    std::string message;
};

// variant 表示一个变量可以在多个候选类型中保存其中一种。
using State = std::variant<Idle, Running, Error>;

void print_state(const State& state)
{
    // visit 会根据 variant 当前保存的类型调用对应处理逻辑。
    std::visit([](const auto& s) {
        using T = std::decay_t<decltype(s)>;

        if constexpr (std::is_same_v<T, Idle>)
        {
            std::cout << "state: idle\n";
        }
        else if constexpr (std::is_same_v<T, Running>)
        {
            std::cout << "state: running task " << s.task_id << "\n";
        }
        else if constexpr (std::is_same_v<T, Error>)
        {
            std::cout << "state: error " << s.message << "\n";
        }
    }, state);
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    State state = Idle{};
    print_state(state);

    state = Running{42};
    print_state(state);

    state = Error{"motor timeout"};
    print_state(state);

    return 0;
}
```

**运行结果**：

```
state: idle
state: running task 42
state: error motor timeout
```

这里的状态永远只能是三种之一。相比用 `int state_code` 加一堆额外字段，`variant` 能把每种状态需要的数据放在对应类型里，减少“错误状态却还读 running 字段”这类问题。

## 常见错误

**错误 1：get 用错类型抛异常**

```cpp
std::variant<int, double> v = 42;
std::cout << std::get<double>(v);  // ❌ 抛出 std::bad_variant_access！
```

正确做法：先用 `std::holds_alternative<double>(v)` 检查，或用 `std::get_if`。

**错误 2：variant 中没有默认类型时默认构造**

```cpp
std::variant<int, std::string> v;  // 默认构造第一个类型的默认值（int = 0）
```

这种情况是合法的，但如果第一种类型没有默认构造函数，则编译失败。

**错误 3：visit 的 visitor 没有覆盖所有类型**

```cpp
struct Visitor {
    void operator()(int) {}
    // 缺少 double 和 string 的 operator()
};
std::variant<int, double, std::string> v;
std::visit(Visitor{}, v);  // ❌ 编译错误！
```

正确做法：visit 的 visitor 必须为 variant 中所有类型提供 `operator()`，或者用泛型 lambda。

## 使用建议

1. **替代 `union`**：variant 类型安全，知道当前存的是什么。
2. **用 `std::visit` + 泛型 lambda 是最简洁的访问方式**。
3. **需要"知道当前是哪种类型"时用 `std::get_if`**：返回指针，安全高效。
4. **用 variant + visit 实现消息/事件分发**：模式匹配的雏形。
5. **variant 的大小是所有类型中最大的 + 索引字段**：不要存太多大类型。
6. **类型种类有限时用 variant 更清晰**：如果类型需要随插件扩展，继承和虚函数通常更合适。

## 小结

- `std::variant<T1, T2, ...>` 是类型安全的联合体。
- `std::get<T>(v)` 直接获取（不安全），`std::get_if<T>(&v)` 返回指针（安全）。
- `std::visit(visitor, v)` 是最推荐的方式，强制覆盖所有类型。
- 适用于消息分发、可选配置、状态机等场景。
