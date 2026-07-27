---
title: "nullptr"
---

## 本节解决什么问题

在传统 C/C++ 中，用 `NULL` 或 `0` 表示空指针。但这有问题：

- `NULL` 实际上就是 `0`（在 C++ 中定义为 `#define NULL 0`），它会被当作**整数**来处理，导致类型不匹配。
- 在某些重载函数中，`NULL` 可能匹配到整数版本的函数而非指针版本。

`nullptr` 是真正的"空指针类型"，解决了这些类型安全问题。

## 这个特性是什么

`nullptr` 是 C++11 引入的关键字，类型是 `std::nullptr_t`，可以隐式转换为任何类型的指针，但**不能**隐式转换为整数类型。这让编译器能区分"空指针"和"整数 0"。

## C++ 标准版本

C++11

## 需要的头文件

不需要额外头文件。`nullptr` 是语言关键字。如果需要使用 `std::nullptr_t` 类型，需要 `#include <cstddef>`。

## 基本语法

```cpp
int* p = nullptr;           // 空 int 指针
double* d = nullptr;        // 空 double 指针
std::shared_ptr<int> sp = nullptr;  // 空智能指针

// 判断空指针
if (p == nullptr) { ... }
if (!p) { ... }             // 等价写法
if (p) { ... }              // p 非空
```

## 常用用法

| 用法 | 说明 |
|:---|:---|
| `int* p = nullptr;` | 初始化空指针 |
| `p == nullptr` | 判断是否为 nullptr |
| `if (ptr)` | 判断非空 |
| `func(nullptr)` | 传递空指针参数 |
| `return nullptr;` | 返回空指针 |

## nullptr 和其他"空"的区别

`nullptr` 只表示"没有指向任何对象的指针"。它不是整数 0，也不是空字符串，更不是容器为空。

| 表达含义 | 推荐写法 | 不要混用 |
|:---|:---|:---|
| 空指针 | `T* p = nullptr;` | 不要写 `NULL` 或 `0` |
| 空智能指针 | `std::shared_ptr<T> p = nullptr;` | 不要手动 new/delete |
| 空字符串 | `std::string s;` 或 `""` | 不要用 `nullptr` |
| 可能没有返回值 | `std::optional<T>` | 不要硬塞一个"特殊值" |
| 容器为空 | `v.empty()` | 不要和指针是否为空混淆 |

## 示例代码

### 示例 1：nullptr 基本使用

```cpp
#include <iostream>
#include <cstddef>

int main()
{
    int* p1 = nullptr;    // 空指针初始化
    int* p2 = 0;           // 旧写法（用 0 赋给指针）
    int* p3 = NULL;        // 旧写法（NULL 实际上还是 0）

    // 三种写法在简单的场景下等价
    std::cout << "p1 = " << p1 << "\n";
    std::cout << "p2 = " << p2 << "\n";
    std::cout << "p3 = " << p3 << "\n";

    // 判断空指针
    if (p1 == nullptr)
    {
        std::cout << "p1 is null\n";
    }

    return 0;
}
```

**运行结果**：

```
p1 = 0
p2 = 0
p3 = 0
p1 is null
```

### 示例 2：在示例 1 基础上，nullptr 解决重载歧义

```cpp
#include <iostream>

// 两个重载函数
void process(int n)
{
    std::cout << "process(int): " << n << "\n";
}

void process(int* p)
{
    if (p == nullptr)
    {
        std::cout << "process(int*): nullptr\n";
    }
    else
    {
        std::cout << "process(int*): " << *p << "\n";
    }
}

int main()
{
    process(42);        // 调用 process(int)
    process(nullptr);   // 调用 process(int*)，明确是指针版本

    // process(NULL);   // ❌ 有歧义！编译器不知道调哪个
    // process(0);      // ❌ 同样的问题！0 匹配 process(int)

    int* p = nullptr;
    process(p);         // 调用 process(int*)

    return 0;
}
```

**运行结果**：

```
process(int): 42
process(int*): nullptr
process(int*): nullptr
```

### 示例 3：在示例 2 基础上，nullptr 在模板和智能指针中的应用

```cpp
#include <iostream>
#include <memory>

template <typename T>
void check_ptr(T* ptr)
{
    if (ptr == nullptr)
    {
        std::cout << "pointer is null\n";
    }
    else
    {
        std::cout << "pointer value = " << *ptr << "\n";
    }
}

int main()
{
    // 原始指针初始化为空
    int* rp = nullptr;
    check_ptr(rp);

    // 智能指针初始化为空
    std::shared_ptr<int> sp = nullptr;
    // 智能指针也可直接用 bool 判断
    if (!sp)
    {
        std::cout << "shared_ptr is null\n";
    }

    // 创建后赋值
    sp = std::make_shared<int>(100);
    if (sp)
    {
        std::cout << "shared_ptr value = " << *sp << "\n";
    }

    return 0;
}
```

**运行结果**：

```
pointer is null
shared_ptr is null
shared_ptr value = 100
```

### 示例 4：nullptr 不是魔法——多个指针重载仍需指定类型

```cpp
#include <iostream>

void reset(int* p)
{
    std::cout << "reset int pointer\n";
    if (p != nullptr)
    {
        *p = 0;
    }
}

void reset(double* p)
{
    std::cout << "reset double pointer\n";
    if (p != nullptr)
    {
        *p = 0.0;
    }
}

int main()
{
    int count = 42;
    double ratio = 3.14;

    reset(&count);   // 明确调用 int* 版本
    reset(&ratio);   // 明确调用 double* 版本

    int* count_ptr = nullptr;
    reset(count_ptr); // 仍然明确是 int* 版本

    // reset(nullptr); // ❌ 编译错误：nullptr 可以转成 int*，也可以转成 double*

    std::cout << "count = " << count << "\n";
    std::cout << "ratio = " << ratio << "\n";

    return 0;
}
```

**运行结果**：

```
reset int pointer
reset double pointer
reset int pointer
count = 0
ratio = 0
```

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | nullptr 基本用法 | `nullptr`、`p == nullptr` | 初始化指针为"空"，类型安全 | nullptr 的类型是 `std::nullptr_t` |
| 示例 2 | 解决重载歧义 | 重载函数中 nullptr 匹配指针版本 | NULL 不能区分整数和指针重载，nullptr 可以 | 这就是 nullptr 替代 NULL 的核心原因 |
| 示例 3 | 模板和智能指针 | `shared_ptr = nullptr`、`if(sp)` | 智能指针也能用 nullptr 初始化 | 智能指针的 bool 转换等价于 `!= nullptr` |
| 示例 4 | 多个指针重载 | `int*`、`double*`、`nullptr` | nullptr 能排除整数重载，但不能替你选择具体指针类型 | 多个指针重载时先把 nullptr 放进明确类型的指针变量 |

## 常见错误

**错误 1：以为 nullptr 可以匹配任意一个指针重载**

```cpp
void f(int*);
void f(double*);

f(nullptr);  // ❌ 编译错误：两个指针版本都能匹配
```

正确做法：先明确类型：`int* p = nullptr; f(p);`

**错误 2：把 `nullptr` 赋给整数**

```cpp
int n = nullptr;  // ❌ 编译错误！nullptr 不能隐式转换为整数
```

正确做法：整型用 0，指针用 nullptr。

**错误 3：用 `NULL` 做指针判断**

```cpp
if (p == NULL) { ... }  // 能工作，但不是最佳实践
```

正确做法：用 `if (p == nullptr)` 或直接 `if (!p)`。

**错误 4：把 nullptr 当布尔值用在非预期的地方**

```cpp
std::string s = nullptr;  // ❌ C++23 起直接禁止；旧标准中也是未定义行为
```

正确做法：空字符串用 `std::string s;` 或 `std::string s = "";`。

## 使用建议

1. **永远用 `nullptr` 而不是 `NULL` 或 `0`**：这是现代 C++ 的规则，避免类型歧义。
2. **智能指针也可用 `nullptr`**：如 `std::shared_ptr<T> p = nullptr;`。
3. **用 `if (ptr)` 检查非空**：等价于 `if (ptr != nullptr)`，更简洁。
4. **不要给非指针类型赋 nullptr**：整数、字符串等不要用 nullptr 初始化。

## 小结

- `nullptr` 是类型安全的空指针常量，替代 `NULL` 和 `0`。
- `nullptr` 的类型是 `std::nullptr_t`，只能隐式转为指针类型。
- 在重载函数中，`nullptr` 可以正确匹配到指针版本，`NULL` 不行。
- 现代 C++ 中永远使用 `nullptr`。
