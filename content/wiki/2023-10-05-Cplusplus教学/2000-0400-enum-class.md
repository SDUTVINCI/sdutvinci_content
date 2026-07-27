---
title: "enum class"
---

## 本节解决什么问题

C 语言的 `enum` 有几个问题：

1. **名字污染**：枚举值会泄漏到其所在的作用域，例如 `enum Color { red, green };` 写完后，`red` 就成了全局名字，不能再用于别的枚举。
2. **隐式转整数**：枚举值会悄悄变成整数参与运算（`red + 1` 合法但通常没有意义）。
3. **底层类型不确定**：编译器自行决定用多大空间存储枚举值。

`enum class` 解决了上述所有问题。

## 这个特性是什么

`enum class`（强类型枚举 / 限定作用域枚举）是 C++11 引入的改进版枚举：

- 枚举值有**作用域限定**，需要用 `Color::red` 访问。
- **不会**隐式转换为整数。
- 可以**指定底层类型**（如 `enum class Color : uint8_t`）。

## C++ 标准版本

C++11

## 需要的头文件

不需要额外头文件。`enum class` 是语言关键字。如果要使用 `uint8_t` 这类固定宽度整数作为底层类型，需要 `#include <cstdint>`。

## 基本语法

```cpp
enum class 枚举名 : 底层类型
{
    值1,
    值2,
    值3
};

// 访问
枚举名 变量 = 枚举名::值1;
```

## 常用用法

| 用法 | 说明 |
|:---|:---|
| `enum class E { A, B, C };` | 基本枚举类 |
| `enum class E : uint8_t { A, B, C };` | 指定底层类型 |
| `E::A` | 访问枚举值（带作用域） |
| `static_cast<int>(E::A)` | 显式转换（不会隐式转换） |
| `switch (val) { case E::A: ... }` | switch 匹配 |

## 示例代码

### 示例 1：旧 enum vs enum class

```cpp
#include <iostream>

// 旧式 enum（不推荐）
enum OldColor { red, green, blue };

// enum class（推荐）
enum class Color { red, green, blue };

int main()
{
    // 旧式 enum：名字污染，red 直接可见
    OldColor oc = red;  // 直接写 red
    int n = red;         // 隐式转为 0

    // enum class：需要带作用域
    Color c = Color::red;

    // int m = Color::red;  // ❌ 编译错误！不会隐式转换为 int
    int m = static_cast<int>(Color::red);  // ✅ 显式转换

    std::cout << "OldColor red = " << n << "\n";
    std::cout << "Color::red = " << m << "\n";

    return 0;
}
```

**运行结果**：

```
OldColor red = 0
Color::red = 0
```

### 示例 2：在示例 1 基础上，指定底层类型和自定义值

```cpp
#include <cstdint>
#include <iostream>

// 指定底层类型为 uint8_t（只占一个字节）
enum class Status : uint8_t
{
    Idle = 0,
    Running = 10,
    Paused = 20,
    Stopped = 30
};

void print_status(Status s)
{
    switch (s)
    {
        case Status::Idle:
            std::cout << "Idle\n";
            break;
        case Status::Running:
            std::cout << "Running\n";
            break;
        case Status::Paused:
            std::cout << "Paused\n";
            break;
        case Status::Stopped:
            std::cout << "Stopped\n";
            break;
    }
}

int main()
{
    Status s1 = Status::Running;
    print_status(s1);
    print_status(Status::Stopped);

    // 查看底层整数值
    std::cout << "sizeof(Status) = " << sizeof(Status) << " byte(s)\n";
    std::cout << "Running = " << static_cast<int>(Status::Running) << "\n";

    return 0;
}
```

**运行结果**：

```
Running
Stopped
sizeof(Status) = 1 byte(s)
Running = 10
```

### 示例 3：在示例 2 基础上，enum class 在结构体中的应用

```cpp
#include <iostream>
#include <string>

enum class Grade
{
    A = 90,
    B = 80,
    C = 70,
    D = 60,
    F = 0
};

struct Student
{
    std::string name;
    Grade grade;
};

std::string grade_to_string(Grade g)
{
    switch (g)
    {
        case Grade::A: return "A (优秀)";
        case Grade::B: return "B (良好)";
        case Grade::C: return "C (中等)";
        case Grade::D: return "D (及格)";
        case Grade::F: return "F (不及格)";
        default:       return "未知";
    }
}

int main()
{
    Student s1{"Alice", Grade::A};
    Student s2{"Bob", Grade::C};

    std::cout << s1.name << " grade: " << grade_to_string(s1.grade) << "\n";
    std::cout << s2.name << " grade: " << grade_to_string(s2.grade) << "\n";

    // 比较（enum class 支持 ==, <, > 等）
    if (s1.grade > s2.grade)
    {
        std::cout << s1.name << " has higher grade\n";
    }

    return 0;
}
```

**运行结果**：

```
Alice grade: A (优秀)
Bob grade: C (中等)
Alice has higher grade
```

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | 旧 enum 问题 vs enum class | `enum class`、`static_cast<int>()` | 旧 enum 名字污染、隐式转 int；enum class 解决 | 需要整数值时必须显式转换 |
| 示例 2 | 指定底层类型和 switch 用法 | `enum class : uint8_t`、switch + enum class | 指定底层类型能节省内存（如嵌入式场景） | `sizeof(enum class)` 不等于成员个数 |
| 示例 3 | 在 struct 中使用 enum class | enum class 做 struct 成员、比较操作 | 表示状态、等级等有限取值集合 | enum class 支持 `==`, `<`, `>` 等比较 |

## enum class 适合表示状态和选项

`enum class` 最适合表示“有限个离散取值”，比如机器人状态、任务阶段、通信协议命令、UI 模式。

### 示例 4：用 enum class 表示机器人状态

```cpp
#include <iostream>

enum class RobotState
{
    Idle,
    Running,
    Error
};

void handle_state(RobotState state)
{
    switch (state)
    {
        case RobotState::Idle:
            std::cout << "wait for command\n";
            break;
        case RobotState::Running:
            std::cout << "execute task\n";
            break;
        case RobotState::Error:
            std::cout << "stop and report error\n";
            break;
    }
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    RobotState state = RobotState::Running;
    handle_state(state);

    // 返回 0 表示程序正常结束。
    return 0;
}
```

**运行结果**：

```
execute task
```

如果用 `int state = 1`，读代码的人不知道 `1` 是什么；如果用 `enum class RobotState::Running`，语义直接写在类型里，而且不会和其他枚举的 `Running` 混用。

## 常见错误

**错误 1：直接拿 enum class 值做数组下标**

```cpp
enum class Color { red, green, blue };
int counts[3];
counts[Color::red];  // ❌ 编译错误！不会隐式转换
```

正确做法：`counts[static_cast<int>(Color::red)]`，或者在这种场景考虑用 map。

**错误 2：忘记写作用域前缀**

```cpp
enum class Status { Running, Stopped };
Status s = Running;  // ❌ 编译错误！缺少 Status::
```

正确做法：`Status s = Status::Running;`

**错误 3：switch 中混用不同 enum class 的值**

```cpp
switch (status)
{
    case Status::Running:  // ✅ 正确
    case Machine::Running: // ❌ 编译错误！不同枚举类型
        break;
}
```

正确做法：switch 中 case 必须和 switch 表达式是同一枚举类型。

## 使用建议

1. **永远用 `enum class` 而不是旧的 `enum`**：类型安全、作用域清晰。
2. **可以指定底层类型**：嵌入式场景用 `enum class : uint8_t` 节省空间。
3. **需要整数值时显式用 `static_cast`**：不要指望隐式转换。
4. **enum class 仍然是整数，可以 switch**：配合 `default` 分支处理未知值。

## 小结

- `enum class` 避免名字污染，枚举值通过 `枚举名::值` 访问。
- 不会隐式转换为整数，需要 `static_cast` 显式转换。
- 可以指定底层类型（`enum class E : uint8_t`）。
- 适用于表示状态、等级、选项等有限的离散取值集合。
