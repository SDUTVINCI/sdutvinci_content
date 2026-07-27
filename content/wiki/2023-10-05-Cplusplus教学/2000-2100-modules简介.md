---
title: "modules 简介"
---

## 本节解决什么问题

传统的 `#include` 有三大问题：

1. **编译慢**：每个 `.cpp` 文件都要重新解析所有 include 的头文件（可能几万行）。
2. **宏污染**：`#define` 会影响所有 include 之后的代码。
3. **顺序依赖**：include 的顺序可能影响程序行为。

Modules 是 C++20 引入的新特性，从根本上解决了这些问题——更快编译、隔离性好、无宏泄漏。

## 这个特性是什么

Modules 是 C++20 引入的模块系统。和 `#include`（文本复制粘贴）不同，模块是预编译的接口声明，只导出你想暴露的部分，内部实现完全隐藏。

## C++ 标准版本

C++20（正式引入），C++23 增强了 `import std;` 标准库模块。

## 需要的头文件

Modules 不需要头文件。模块文件通常用 `.cppm` 扩展名（社区惯例）或 `.ixx`（MSVC）。

## 基本语法

```cpp
// math_module.cppm —— 模块接口文件
export module math;           // 声明模块名

export int add(int a, int b)  // export：对外可见
{
    return a + b;
}

int multiply(int a, int b)    // 没有 export：模块内部可见
{
    return a * b;
}

// main.cpp —— 使用模块
import math;                  // 导入模块（替代 #include）

int main()
{
    int x = add(3, 5);        // ✅ 可以访问
    // int y = multiply(3, 5); // ❌ 不可访问（没有 export）
    return 0;
}
```

**运行结果**：程序正常结束，终端没有额外输出。

## 核心概念

| 概念 | 说明 |
|:---|:---|
| `export module 模块名;` | 声明一个模块（放在模块接口文件中） |
| `export` | 标记对外可见的函数/类/变量 |
| `import 模块名;` | 导入一个模块 |
| `import <头文件>;` | 导入传统头文件（把它当作模块来用，编译器支持有限） |
| 模块接口单元 | `.cppm` 文件，包含 `export module` |
| 模块实现单元 | `.cpp` 文件，包含 `module 模块名;`（不带 export） |

## include 和 module 的核心区别

不要把 modules 理解成"把 `#include` 换成 `import`"这么简单。二者的编译模型不同，所以能解决的问题也不同。

| 对比点 | `#include` | `import module` |
|:---|:---|:---|
| 工作方式 | 文本复制，把头文件内容粘进当前文件 | 导入已经编译好的模块接口 |
| 可见性 | 头文件里写了什么，包含者基本都能看到 | 只有 `export` 的名字对外可见 |
| 宏 | 宏容易向后污染 | 模块接口不会像文本包含那样传播宏 |
| 编译依赖 | include 顺序可能影响结果 | 模块依赖更明确 |
| 构建要求 | 所有编译器和构建系统都成熟支持 | 需要较新的编译器和构建系统 |

所以现阶段学习 modules，重点是理解"接口和实现分离得更彻底"。真正项目中是否迁移，要看工具链是否稳定支持。

## 示例代码

### 示例 1：最简单的模块

**文件 1：`math_module.cppm`**（模块接口）

```cpp
export module math;

export int add(int a, int b)
{
    return a + b;
}

export int subtract(int a, int b)
{
    return a - b;
}

// 内部函数，不对外暴露
int internal_helper()
{
    return 0;
}
```

**文件 2：`main.cpp`**

```cpp
import math;
#include <iostream>

int main()
{
    std::cout << "add(3, 5) = " << add(3, 5) << "\n";
    std::cout << "subtract(10, 3) = " << subtract(10, 3) << "\n";
    // internal_helper();  // ❌ 编译错误！没有 export
    return 0;
}
```

**运行结果**：见下方“运行结果”；模块示例需要先按编译命令生成模块接口，再运行 `main.cpp`。

**编译命令（以 GCC 为例）**：

```bash
# 先编译模块接口
g++ -std=c++20 -fmodules-ts -c math_module.cppm -o math_module.o

# 再编译主程序
g++ -std=c++20 -fmodules-ts -c main.cpp -o main.o

# 链接
g++ math_module.o main.o -o program
```

**运行结果**：

```
add(3, 5) = 8
subtract(10, 3) = 7
```

### 示例 2：模块 + 类

**文件：`person_module.cppm`**

```cpp
export module person;

import <string>;
import <iostream>;

export class Person
{
    std::string name_;
    int age_;
public:
    Person(const std::string& name, int age)
        : name_(name), age_(age) {}

    void print() const
    {
        std::cout << name_ << ", age " << age_ << "\n";
    }
};
```

**文件：`main.cpp`**

```cpp
import person;

int main()
{
    Person p("Alice", 25);
    p.print();

    // p.name_ = "Bob";  // ❌ 编译错误！name_ 是 private

    return 0;
}
```

**运行结果**：

```
Alice, age 25
```

### 示例 3：在示例 2 基础上，把接口和实现拆开

示例 1 和示例 2 为了直观，把实现直接写在模块接口里。真实项目里更常见的写法是：接口单元只导出声明，实现单元放函数体。

**文件 1：`counter.cppm`**（模块接口单元）

```cpp
export module counter;

export class Counter
{
    int value_ = 0;

public:
    void add(int n);
    int value() const;
};
```

**文件 2：`counter.cpp`**（模块实现单元）

```cpp
module counter;

void Counter::add(int n)
{
    value_ += n;
}

int Counter::value() const
{
    return value_;
}
```

**文件 3：`main.cpp`**

```cpp
import counter;
#include <iostream>

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    Counter c;
    c.add(3);
    c.add(5);

    std::cout << "counter = " << c.value() << "\n";
    // 返回 0 表示程序正常结束。
    return 0;
}
```

**运行结果**：

```
counter = 8
```

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | 基础模块定义和导入 | `export module`、`import`、`export` | 模块只暴露 export 的内容 | 模块文件名没有硬性规定，`.cppm` 是社区惯例 |
| 示例 2 | 模块中导出类 | `export class`、`import <string>` | 类的 public/private 仍然有效 | `import <header>` 属于 header unit，工具链支持差异较大 |
| 示例 3 | 接口单元和实现单元分离 | `module counter;` | 接口只暴露声明，实现放到单独文件 | 构建系统要知道模块依赖关系 |

## 常见错误

**错误 1：编译器不支持或未启用**

```bash
g++ -std=c++17 main.cpp  # ❌ C++17 没有 modules
```

正确做法：用 `-std=c++20 -fmodules-ts`（GCC/Clang），MSVC 用 `/std:c++20`。

**错误 2：忘记了 `export`**

```cpp
// module.cppm
export module my_module;
int func() { return 42; }  // ❌ 没有 export，外部看不到！
```

正确做法：`export int func() { return 42; }`

**错误 3：编译顺序不对**

必须先编译模块接口单元（`.cppm`），再编译使用模块的文件。

正确做法：用 CMake 3.28+ 或构建系统管理模块依赖。

**错误 4：把 private 当成模块隐藏的全部**

```cpp
export module demo;

export class A
{
private:
    int value_;
};

int helper(); // 没有 export，外部看不到
```

`private` 控制的是类成员访问权限；`export` 控制的是模块级名字是否对外可见。二者解决的问题不同。

## 使用建议

1. **现阶段可以了解但不强制迁移**：C++20 modules 的编译器支持仍在完善中，传统 `#include` 在相当长时间内仍是主流。
2. **新项目可以尝试**：如果使用最新编译器（GCC 15+、Clang 17+、MSVC 2022+），modules 已比较稳定。
3. **CMake 3.28+ 支持 modules**：`target_sources(myapp PUBLIC FILE_SET CXX_MODULES FILES ...)`。
4. **标准库模块 `import std;` 是 C++23 的特性**：C++20 只能 import 自定义模块。
5. **理解 modules 的设计思想很重要**：它代表了 C++ 的未来方向。
6. **教程里先学概念，不强背命令**：不同编译器的模块命令差异很大，实际项目交给 CMake/构建系统管理更稳。

## 小结

- Modules 是 C++20 的新编译模型，替代 `#include`，更快、更安全。
- `export module` 声明模块名，`export` 标记对外可见的声明。
- `import` 导入模块（替代 `#include`）。
- 模块接口单元负责暴露 API，模块实现单元负责隐藏实现。
- 编译器支持在不断完善中，可以了解但现阶段项目中使用需要评估。
- 学习 modules 的设计有助于理解大型 C++ 项目的模块化思想。
